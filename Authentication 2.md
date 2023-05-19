> Authentication Middleware and Authorize Attribute 013

* First Add package

```cs
dotnet add package Microsoft.AspNetCore.Authentication.JwtBearer
```
* Second Add Authentication and app.UserAuthentication in ``Program.cs`` fie

```cs
global using DotNet.Models;
global using DotNet.Models.Responses;
global using DotNet.Services.CharacterInterface;
global using DotNet.Services.CharacterService;
global using DotNet.Dtos.Character;
global using AutoMapper;
global using Microsoft.EntityFrameworkCore;
global using DotNet.Data;
using DotNet.Data.Repository;
using DotNet.Data.Repository.Impl;

using Microsoft.AspNetCore.Authentication.JwtBearer;
using Microsoft.IdentityModel.Tokens;

var builder = WebApplication.CreateBuilder(args);

// Add services to the container.
builder.Services.AddDbContext<DataContext>(options => options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnetionString")));

builder.Services.AddControllers();
// Learn more about configuring Swagger/OpenAPI at https://aka.ms/aspnetcore/swashbuckle
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();

builder.Services.AddAutoMapper(typeof(Program).Assembly);

builder.Services.AddScoped<ICharacterService,CharacterService>();
builder.Services.AddScoped<IAuthRepository,AuthRepositoryImpl>();
//here
builder.Services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(options => {
        options.TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuerSigningKey = true,
            IssuerSigningKey = new SymmetricSecurityKey(System.Text.Encoding.UTF8.GetBytes(builder.Configuration.GetSection("AppSettings:Token").Value)),
            ValidateIssuer = false,
            ValidateAudience = false,

        };
    });
 var app = builder.Build();

// Configure the HTTP request pipeline.
if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI();
}

app.UseHttpsRedirection();

//here
app.UseAuthentication();

app.UseAuthorization();

app.MapControllers();

app.Run();
```

* Fird In CharacterController add ``[Authorize]`` and now use controller you must login if not It throw new Exception Unauthorize

```cs
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;

using Microsoft.AspNetCore.Mvc;
//here
using Microsoft.AspNetCore.Authorization;
namespace DotNet.Controllers
{
    //here
    [Authorize]
    [ApiController]
    [Route("api/[controller]")]
    public class CharacterController : ControllerBase
    {
    ...
```
* Continute 014 Testing Secured Methods with Swagger

> First Add package
```cs
dotnet add package Swashbuckle.AspNetCore.Filters 
```
> Second configs in file ``program.cs``
```cs
using DotNet.Data.Repository;
using DotNet.Data.Repository.Impl;

using Microsoft.AspNetCore.Authentication.JwtBearer;
using Microsoft.IdentityModel.Tokens;
//here
using Swashbuckle.AspNetCore.Filters;
using Microsoft.OpenApi.Models;

var builder = WebApplication.CreateBuilder(args);

// Add services to the container.
builder.Services.AddDbContext<DataContext>(options => options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnetionString")));

builder.Services.AddControllers();
// Learn more about configuring Swagger/OpenAPI at https://aka.ms/aspnetcore/swashbuckle
builder.Services.AddEndpointsApiExplorer();
// Changer here add 
builder.Services.AddSwaggerGen(c => {
    c.AddSecurityDefinition("oauth2", new OpenApiSecurityScheme{
        Description = """Standard Authorization header using the Bearer scheme. Example: "bearer {token}" """,
        In = ParameterLocation.Header,
        Name = "Authorization",
        Type = SecuritySchemeType.ApiKey
    });

    c.OperationFilter<SecurityRequirementsOperationFilter>();
});
```
