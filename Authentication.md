* Register User Accounts
  > First Add class User in Models folder
  ```cs
  using System;
  using System.Collections.Generic;
  using System.Linq;
  using System.Threading.Tasks;

  namespace DotNet.Models
  {
      public class User
      {
          public int Id { get; set; }
          public string Username { get; set; }
          public byte[] PasswordHash { get; set; } = new byte[0];
          public byte[] PasswordSalt { get; set; } = new byte[0];
          public List<Character> characters {get;set;}
      }
  }
  ```
  >>> And Alter Model Character
  ```cs
  using System;
  using System.Collections.Generic;
  using System.Linq;
  using System.Threading.Tasks;

  namespace DotNet.Models
  {
      public class Character
      {
          public int Id { get; set; }
          public  string Name { get; set; } = "Frodo";
          public int HitPoints { get; set; } = 100;
          public int Strength { get; set; } = 10;
          public int Defense { get; set; } = 10 ;
          public int Intelligence { get; set; } = 10;
          public RpgClass TypeClass { get; set; } = RpgClass.Knight;
          public User Users { get; set; }
      }
  }
  ```

  > Second add DBset User 
  ```cs
  using System;
  using System.Collections.Generic;
  using System.Linq;
  using System.Threading.Tasks;

  namespace DotNet.Data
  {
      public class DataContext : DbContext
      {
          public DataContext(DbContextOptions options) : base(options){ }
          public DbSet<Character> Characters => Set<Character>(); 
          public DbSet<User> Users => Set<User>();
      }
  }
  ```
  > Fird Migration and Update Database
  ```cs
  dotnet ef migrations add CreateUser
  ```
 ```cs
  dotnet ef database update
  ```
* User Character relations
  >Authentication Theory
  
  ![image](https://github.com/nguyenduong-lifetechvn/CShape/assets/130037593/ca39a151-0d7b-4bfd-b6ed-3f2ce22a736f)

  >Authentication Repository

  ![image](https://github.com/nguyenduong-lifetechvn/CShape/assets/130037593/173ac1bb-560e-4e58-9ce5-b2019bd63c8b)
  
  ![image](https://github.com/nguyenduong-lifetechvn/CShape/assets/130037593/f7b02334-ee38-41da-b295-b04da28cf262)

* JSON Web Tokens
  > First in ``appsetting.json`` file add AppSettings
  ```cs
  {
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "AppSettings": {
    "Token": "my top secret key"
  },
  "AllowedHosts": "*",
  "ConnectionStrings": {
    "DefaultConnetionString": "Server=localhost;Database=CharacterDb;integrated security=True;MultipleActiveResultSets=True;TrustServerCertificate=True"
  }
  }

  ```
  >Second Create method CreateToken and change Login mothed return ``` response.Data = CreateTokent(user); ``` and add _configuration

  ```cs
  using System;
  using System.Collections.Generic;
  using System.Linq;
  using System.Security.Claims;
  using System.Threading.Tasks;

  namespace DotNet.Data.Repository.Impl
  {
      public class AuthRepositoryImpl : IAuthRepository
      {
          private readonly DataContext _context;
          private readonly IConfiguration _configuration;

        public AuthRepositoryImpl(DataContext context, IConfiguration configuration)
        {
            _context = context;
            _configuration = configuration;
        }
        public async Task<ServiceResponse<string>> Login(string username, string password)
        {
            var response = new ServiceResponse<string>();
            var user = await _context.Users.FirstOrDefaultAsync(u => u.Username.ToLower().Equals(username.ToLower()));

            if(user == null)
            {
                response.Success = false;
                response.Messages = "User not found !!";
            }else if(!VerifyPasswordHash(password, user.PasswordSalt, user.PasswordHash)){
                response.Success = false;
                response.Messages = "Password was wrong !!";
            }else{
                response.Data = CreateToken(user);
            }
            return response;
        }

        ...
        private string CreateToken(User user){
            var claims = new List<Claim>
            {
                new Claim(ClaimTypes.NameIdentifier, user.Id.ToString()),
                new Claim(ClaimTypes.Name, user.Username),
            };
            var appSettingToken = _configuration.GetSection("AppSettings:Token").Value;
            if(appSettingToken == null)
                throw new Exception("AppSettings Token is null");

            SymmetricSecurityKey key = new SymmetricSecurityKey(System.Text.Encoding.UTF8.GetBytes(appSettingToken));

            SigningCredentials creds = new SigningCredentials(key, SecurityAlgorithms.HmacSha256Signature);

            var tokenDescriptor = new SecurityTokenDescriptor
            {
                Subject = new ClaimsIdentity(claims),
                Expires = DateTime.Now.AddDays(1),
                SigningCredentials = creds
            };

            JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler();
            SecurityToken token = tokenHandler.CreateToken(tokenDescriptor);

            return tokenHandler.WriteToken(token);

        }
    }
  }
  ```
* Secure CRUD Operation
