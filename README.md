using System.Security.Cryptography.X509Certificates;

var builder = WebApplication.CreateBuilder(args);

// Get base64-encoded secrets from environment variables
var certBase64 = Environment.GetEnvironmentVariable("CERT_BASE64");
var keyBase64 = Environment.GetEnvironmentVariable("KEY_BASE64");

// Decode the base64 strings
var certBytes = Convert.FromBase64String(certBase64);
var keyBytes = Convert.FromBase64String(keyBase64);

// Create the certificate from the cert and key bytes
var certificate = X509Certificate2.CreateFromPem(new ReadOnlySpan<byte>(certBytes), new ReadOnlySpan<byte>(keyBytes));

// Configure Kestrel
builder.WebHost.ConfigureKestrel(serverOptions =>
{
    serverOptions.ListenAnyIP(5000); // HTTP
    serverOptions.ListenAnyIP(5001, listenOptions =>
    {
        listenOptions.UseHttps(certificate); // HTTPS with retrieved certificate
    });
});

var app = builder.Build();

app.UseHttpsRedirection();
app.UseRouting();
app.UseAuthorization();

app.MapControllers();

app.Run();
