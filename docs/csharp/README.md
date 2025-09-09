# Get Started
The link to the NuGet package and some examples . For additional information about Azure OpenAI SDK, see full documentation  and samples .

## 1. Authentication using API Key
For OpenAI API Endpoints, deploy the Model to generate the endpoint URL and an API key to authenticate against the service. In this sample endpoint and key are strings holding the endpoint URL and the API Key.

The API endpoint URL and API key can be found on the Deployments + Endpoint page once the model is deployed.

To create a client using the Azure OpenAI SDK with async patterns, initialize the client by passing your API key to the SDK's configuration. This approach uses modern async patterns and is ready for Microsoft.Extensions.AI integration:

```csharp
using Azure;
using Azure.AI.OpenAI;
using OpenAI.Chat;

var endpoint = new Uri("https://crysta-openai-hackathon.cognitiveservices.azure.com/");
var model = "gpt-4o-mini";
var deploymentName = "gpt-4o-mini-hackaton-test";
var apiKey = "<your-api-key>";

AzureOpenAIClient azureClient = new(
    endpoint,
    new AzureKeyCredential(apiKey));
ChatClient chatClient = azureClient.GetChatClient(deploymentName);
```

## 2. Install dependencies
### Install dotnet runtime 
Open PowerShell at the desired project folder, restore the environment, and run the program.

```bash
dotnet restore
dotnet run
dotnet add package Azure.AI.OpenAI
dotnet add package Azure.Core
dotnet add package Microsoft.Extensions.AI
```

> **Migration Note**: These examples use async patterns (CompleteChatAsync, CompleteChatStreamingAsync) which are the recommended approach for modern .NET applications. Microsoft.Extensions.AI provides additional abstractions for dependency injection and testing scenarios. As the Microsoft.Extensions.AI ecosystem evolves, these patterns will provide a smooth migration path.

## 3. Run a basic code sample
This sample demonstrates a basic call to the chat completion API using modern async patterns. This approach is compatible with Microsoft.Extensions.AI abstractions and uses the latest recommended patterns.

```csharp
using Azure;
using Azure.AI.OpenAI;
using OpenAI.Chat;

var endpoint = new Uri("https://crysta-openai-hackathon.cognitiveservices.azure.com/");
var deploymentName = "gpt-4o-mini-hackaton-test";
var apiKey = "<your-api-key>";

AzureOpenAIClient azureClient = new(
    endpoint,
    new AzureKeyCredential(apiKey));
ChatClient chatClient = azureClient.GetChatClient(deploymentName);

var chatOptions = new ChatCompletionOptions()
{
    MaxOutputTokenCount = 4096,
    Temperature = 1.0f,
    TopP = 1.0f,
};

List<ChatMessage> messages = new List<ChatMessage>()
{
    new SystemChatMessage("You are a helpful assistant."),
    new UserChatMessage("I am going to Paris, what should I see?"),
};

// Use async pattern for better performance
var response = await chatClient.CompleteChatAsync(messages, chatOptions);
Console.WriteLine(response.Value.Content[0].Text);
```

## 4. Explore more samples
### Run a multi-turn conversation
This sample demonstrates a multi-turn conversation with the chat completion API using async patterns. When using the model for a chat application, you'll need to manage the history of that conversation and send the latest messages to the model.

```csharp
using Azure;
using Azure.AI.OpenAI;
using OpenAI.Chat;

var endpoint = new Uri("https://crysta-openai-hackathon.cognitiveservices.azure.com/");
var deploymentName = "gpt-4o-mini-hackaton-test";
var apiKey = "<your-api-key>";

AzureOpenAIClient azureClient = new(
    endpoint,
    new AzureKeyCredential(apiKey));
ChatClient chatClient = azureClient.GetChatClient(deploymentName);

List<ChatMessage> messages = new List<ChatMessage>()
{
    new SystemChatMessage("You are a helpful assistant."),
    new UserChatMessage("I am going to Paris, what should I see?"),
};

var response = await chatClient.CompleteChatAsync(messages);
Console.WriteLine(response.Value.Content[0].Text);
// Append the model response to the chat history.
messages.Add(new AssistantChatMessage(response.Value.Content[0].Text));
// Append new user question.
messages.Add(new UserChatMessage("What is so great about #1?"));

response = await chatClient.CompleteChatAsync(messages);
Console.WriteLine(response.Value.Content[0].Text);
```

### Stream the output
For a better user experience, you will want to stream the response of the model so that the first token shows up early and you avoid waiting for long responses.

```csharp
using Azure;
using Azure.AI.OpenAI;
using OpenAI.Chat;

var endpoint = new Uri("https://crysta-openai-hackathon.cognitiveservices.azure.com/");
var deploymentName = "gpt-4o-mini-hackaton-test";
var apiKey = "<your-api-key>";

AzureOpenAIClient azureClient = new(
    endpoint,
    new AzureKeyCredential(apiKey));
ChatClient chatClient = azureClient.GetChatClient(deploymentName);

List<ChatMessage> messages = new List<ChatMessage>()
{
    new SystemChatMessage("You are a helpful assistant."),
    new UserChatMessage("I am going to Paris, what should I see?"),
};

await foreach (StreamingChatCompletionUpdate update in chatClient.CompleteChatStreamingAsync(messages))
{
    foreach (ChatMessageContentPart updatePart in update.ContentUpdate)
    {
        Console.Write(updatePart.Text);
    }
}
Console.WriteLine("");
```

## 5. Migration from Older Azure OpenAI SDK

If you're migrating from older Azure OpenAI SDK approaches, here are the key changes:

### Key Changes Summary
- **Async Patterns**: Use `CompleteChatAsync()` instead of `CompleteChat()` for better performance
- **Streaming**: Use `CompleteChatStreamingAsync()` with `await foreach` for streaming responses
- **Future-ready**: Adding `Microsoft.Extensions.AI` package prepares your code for upcoming abstractions
- **Modern C#**: Examples use modern async/await patterns throughout

### Migration Example
**Before (Synchronous):**
```csharp
using OpenAI.Chat;
var response = chatClient.CompleteChat(messages, requestOptions);
Console.WriteLine(response.Value.Content[0].Text);
```

**After (Asynchronous):**
```csharp
using OpenAI.Chat;
var response = await chatClient.CompleteChatAsync(messages, requestOptions);
Console.WriteLine(response.Value.Content[0].Text);
```

### Benefits of the New Approach
- **Performance**: Async operations don't block threads while waiting for responses
- **Scalability**: Better resource utilization in web applications and services
- **Future-proof**: Ready for Microsoft.Extensions.AI abstractions as they become available
- **Modern .NET**: Follows current .NET best practices for async programming
