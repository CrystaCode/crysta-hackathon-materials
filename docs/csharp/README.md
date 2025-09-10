# Get Started

## Install .NET 10
Follow the documentation and install [the latest version of .NET 10](https://dotnet.microsoft.com/en-us/download/dotnet/10.0)

## Write your first code
Write your very first C# file: `app.cs`:

```csharp
#:package Microsoft.Extensions.AI.OpenAI@9.9.0-preview.1.25458.4
#:package Azure.AI.OpenAI@2.3.0-beta.2

using Azure.AI.OpenAI;
using Microsoft.Extensions.AI;
using System.ClientModel;

var endpoint = "https://crysta-openai-hackathon.cognitiveservices.azure.com";
var azureKey = "<YOUR_AZURE_OPENAI_KEY>";
var deployment = "<YOUR_DEPLOYMENT_NAME>";

AzureOpenAIClient azureClient = new(
    new Uri(endpoint),
    new ApiKeyCredential(azureKey));

var chatClient = azureClient.GetChatClient(deployment).AsIChatClient();
var response = await chatClient.GetResponseAsync("What is AI?");
Console.WriteLine(response);
```

## Run your code
```bash
dotnet run app.cs
```