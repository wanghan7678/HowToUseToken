# HowToUseToken
This project is showing how to create a Token auth api based on .net 8.0

Step 1. Install the required package, using NuGet to install:
1. Microsoft.AspNetCore.Identity.EntityFramework
2. Microsoft.AspNetCore.Authentication.JwtBearer
3. Microsoft.EntityFrameworkCore
4. Microsoft.EntityFrameworkCore.Tools
5. MySql.Data.EntityFramework
6. Pomela.EntityFrameworkCore.MySql

Next we need to add the middleware into the service 
Step 2. Add the database connection:
        var conString = configuration.GetConnectionString("Default");
        service.AddDbContext<DbContext>(options =>
            options.UseMySql(conString, ServerVersion.AutoDetect(conString)));

Step 3. Add the Cors:
        service.AddCors(options =>
        {
            options.AddPolicy("AnyPolicy", builder =>
                builder.AllowAnyOrigin()
                    .AllowAnyMethod()
                    .AllowAnyHeader());
        });

Step 4. Add Identity:
        service.AddIdentity<User, IdentityRole>(options =>
        {
        ...
        })
        .AddEntityFrameworkStores<PatientContext>();

Step 5. Add the Authentication: Remember: AddIdentity must before AddAuthentication:
        service.AddAuthentication( options => {
                options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
                options.DefaultScheme = JwtBearerDefaults.AuthenticationScheme;})
            .AddJwtBearer(options =>
                {
                    options.SaveToken = true;
                    options.TokenValidationParameters = new TokenValidationParameters()
                    {
                        ValidateIssuer = true,
                        ValidateAudience = true,
                        ValidateLifetime = false,
                        ValidateIssuerSigningKey = true,
                        ValidIssuer = tokenOptions.Issuer,
                        ValidAudience = tokenOptions.Audience,
                        IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(tokenOptions.SecurityKey))
                    };
                }
        });

Step 6. Add the policy:
        service.AddAuthorizationBuilder()
            .AddPolicy("Guest", policy => policy.RequireRole("Guest"))
            .AddPolicy("Administrator", policy => policy.RequireRole("Guest", "Administrator"));

Step 7. Immigrate the changes into the database:
    dotnet ef migration add userauth;
    dotnet ef database update;

Next step we need to add handler and controller to handle the 

Step 8. Create a handler to generate the JwtToken

Step 9. Create a controller to login/register

Step 10. Configure the swagger to add bearer.
