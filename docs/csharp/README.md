# Get Started
The link to the NuGet package and some examples . For additional information about Azure OpenAI SDK, see full documentation  and samples .

## 1. Authentication using API Key
For OpenAI API Endpoints, deploy the Model to generate the endpoint URL and an API key to authenticate against the service. In this sample endpoint and key are strings holding the endpoint URL and the API Key.

The API endpoint URL and API key can be found on the Deployments + Endpoint page once the model is deployed.

To create a client with the Microsoft.Extensions.AI abstractions using an API key, you have a few options. The most future-proof approach uses the Microsoft.Extensions.AI abstractions:

```csharp
using Microsoft.Extensions.AI;
using Azure;
using Azure.AI.OpenAI;

var endpoint = new Uri("https://crysta-openai-hackathon.cognitiveservices.azure.com/");
var model = "gpt-4o-mini";
var deploymentName = "gpt-4o-mini-hackaton-test";
var apiKey = "<your-api-key>";

// Create Azure OpenAI client
AzureOpenAIClient azureClient = new(
    endpoint,
    new AzureKeyCredential(apiKey));

// Option 1: Direct IChatClient approach (when available)
// IChatClient chatClient = azureClient.GetChatClient(deploymentName).AsChatClient();

// Option 2: Current working approach with Azure OpenAI SDK
var chatClient = azureClient.GetChatClient(deploymentName);
```

## 2. Install dependencies
### Install dotnet runtime 
Open PowerShell at the desired project folder, restore the environment, and run the program.

```bash
dotnet restore
dotnet run
dotnet add package Microsoft.Extensions.AI
dotnet add package Azure.AI.OpenAI
dotnet add package Azure.Core
```

> **Migration Note**: We now recommend using Microsoft.Extensions.AI abstractions for better testability, dependency injection support, and consistent APIs across different AI providers. The examples below show the updated approach using `IChatClient` interface and async patterns. Note that the Microsoft.Extensions.AI integration with Azure OpenAI is actively being developed, so exact APIs may evolve.

## 3. Run a basic code sample
This sample demonstrates a basic call to the chat completion API using modern .NET patterns. The approach shown here uses async/await patterns and is compatible with Microsoft.Extensions.AI abstractions.

```csharp
using Microsoft.Extensions.AI;
using Azure;
using Azure.AI.OpenAI;

var endpoint = new Uri("https://crysta-openai-hackathon.cognitiveservices.azure.com/");
var deploymentName = "gpt-4o-mini-hackaton-test";
var apiKey = "<your-api-key>";

AzureOpenAIClient azureClient = new(
    endpoint,
    new AzureKeyCredential(apiKey));
var chatClient = azureClient.GetChatClient(deploymentName);

// Use modern ChatMessage constructor pattern
List<ChatMessage> messages = new List<ChatMessage>()
{
    new(ChatRole.System, "You are a helpful assistant."),
    new(ChatRole.User, "I am going to Paris, what should I see?"),
};

// Use async pattern for better performance
var response = await chatClient.CompleteAsync(messages);
Console.WriteLine(response.Message.Text);
```

## 4. Explore more samples
### Run a multi-turn conversation
This sample demonstrates a multi-turn conversation with the chat completion API using Microsoft.Extensions.AI. When using the model for a chat application, you'll need to manage the history of that conversation and send the latest messages to the model.

```csharp
using Microsoft.Extensions.AI;
using Azure;
using Azure.AI.OpenAI;

var endpoint = new Uri("https://crysta-openai-hackathon.cognitiveservices.azure.com/");
var deploymentName = "gpt-4o-mini-hackaton-test";
var apiKey = "<your-api-key>";

AzureOpenAIClient azureClient = new(
    endpoint,
    new AzureKeyCredential(apiKey));
IChatClient chatClient = azureClient.GetChatClient(deploymentName).AsChatClient();

List<ChatMessage> messages = new List<ChatMessage>()
{
    new(ChatRole.System, "You are a helpful assistant."),
    new(ChatRole.User, "I am going to Paris, what should I see?"),
};

var response = await chatClient.CompleteAsync(messages);
Console.WriteLine(response.Message.Text);
// Append the model response to the chat history.
messages.Add(new(ChatRole.Assistant, response.Message.Text));
// Append new user question.
messages.Add(new(ChatRole.User, "What is so great about #1?"));

response = await chatClient.CompleteAsync(messages);
Console.WriteLine(response.Message.Text);
```

### Stream the output
For a better user experience, you will want to stream the response of the model so that the first token shows up early and you avoid waiting for long responses.

```csharp
using Microsoft.Extensions.AI;
using Azure;
using Azure.AI.OpenAI;

var endpoint = new Uri("https://crysta-openai-hackathon.cognitiveservices.azure.com/");
var deploymentName = "gpt-4o-mini-hackaton-test";
var apiKey = "<your-api-key>";

AzureOpenAIClient azureClient = new(
    endpoint,
    new AzureKeyCredential(apiKey));
IChatClient chatClient = azureClient.GetChatClient(deploymentName).AsChatClient();

List<ChatMessage> messages = new List<ChatMessage>()
{
    new(ChatRole.System, "You are a helpful assistant."),
    new(ChatRole.User, "I am going to Paris, what should I see?"),
};

await foreach (var update in chatClient.CompleteStreamingAsync(messages))
{
    Console.Write(update.Text);
}
Console.WriteLine("");
```

## 5. Migration from Older Azure OpenAI SDK

If you're migrating from older Azure OpenAI SDK approaches, here are the key changes:

### Key Changes Summary
- **Package**: Added `Microsoft.Extensions.AI` and `Microsoft.Extensions.AI.AzureAIInference` 
- **Interface**: Use `IChatClient` instead of `ChatClient` directly
- **Methods**: Use `CompleteAsync()` instead of `CompleteChat()`
- **Streaming**: Use `CompleteStreamingAsync()` instead of `CompleteChatStreaming()`
- **Message Types**: Use `new ChatMessage(ChatRole.X, "text")` instead of specific message types like `SystemChatMessage`
- **Options**: Use `ChatOptions` instead of `ChatCompletionOptions`
- **Async Pattern**: All operations are now async by default

### Migration Example
**Before (Old SDK):**
```csharp
using OpenAI.Chat;
ChatClient chatClient = azureClient.GetChatClient(deploymentName);
var response = chatClient.CompleteChat(messages, requestOptions);
Console.WriteLine(response.Value.Content[0].Text);
```

**After (Microsoft.Extensions.AI):**
```csharp
using Microsoft.Extensions.AI;
IChatClient chatClient = azureClient.GetChatClient(deploymentName).AsChatClient();
var response = await chatClient.CompleteAsync(messages, chatOptions);
Console.WriteLine(response.Message.Text);
```

### Benefits of the New Approach
- **Testability**: IChatClient interface makes it easier to mock and test your code
- **Dependency Injection**: Better integration with .NET's built-in DI container
- **Consistency**: Unified API across different AI providers
- **Future-proof**: Prepared for upcoming .NET AI ecosystem developments
