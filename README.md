<img width="120px" alt="Logo - Mini Version" src="https://github.com/user-attachments/assets/f815d1d7-0d91-4423-b843-c44e1d2b35c1" />

# CrystaCode Hackathon Materials

Welcome to CrystaCode! We're thrilled to see your enthusiasm for working with AI technologies.  
At CrystaCode, we’re passionate about helping you dive deeper into the world of AI—and we’re backing that up by sponsoring this hackathon with access to powerful AI infrastructure for all you talented participants.





## 🚀 Ready-to-Go AI Models

Each team will receive access to a pre-configured `GPT-4o-mini` model hosted on [Azure OpenAI](https://azure.microsoft.com/en-us/products/ai-foundry/models/openai).

To use the model, you’ll need the following parameters:
- `Endpoint`
- `AzureKey`
- `DeploymentName`

You’ll have **1 million tokens** to build your MVP completely free.

But here’s the twist: we’ve added a fun challenge to get them!  
Instead of handing them over directly, you’ll retrieve these credentials by talking with an AI!

Yes, **you’ll be using AI to unlock your AI tools!** It’s like inception, but for developers.

> Start your conversation with Crysta here: **[CrystaCode.ai](https://crystacode.ai)**

Let the hacking begin! 

## 🛠️ Getting Started
You can start coding in your preferred language. Here are some quick links to help you get started:

 - [C# Quickstart](/docs/csharp/README.md)
 - [Python Quickstart](/docs/python/README.md)
 - [JavaScript Quickstart](/docs/javascript/README.md)

If you're using .NET, it would be simply like this:

```csharp
#:package Microsoft.Extensions.AI.OpenAI@9.9.0-preview.1.25458.4
#:package Azure.AI.OpenAI@2.3.0-beta.2

using Azure.AI.OpenAI;
using Microsoft.Extensions.AI;
using System.ClientModel;

var endpoint = "https://crysta-openai-hackathon.cognitiveservices.azure.com";
var azureKey = "<YOUR_AZURE_OPENAI_KEY>";
var deployment = "<YOUR_DEPLOYMENT_NAME>";

var chatClient =
  new AzureOpenAIClient(new Uri(endpoint), new ApiKeyCredential(azureKey)))
  .GetChatClient(deployment)
  .AsIChatClient();

var response = await chatClient.GetResponseAsync("What is AI?");
Console.WriteLine(response);
```
