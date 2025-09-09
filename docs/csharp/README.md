# Get Started
The link to the NuGet package and some examples . For additional information about Azure OpenAI SDK, see full documentation  and samples .

## 1. Authentication using API Key
For OpenAI API Endpoints, deploy the Model to generate the endpoint URL and an API key to authenticate against the service. In this sample endpoint and key are strings holding the endpoint URL and the API Key.

The API endpoint URL and API key can be found on the Deployments + Endpoint page once the model is deployed.

To create a client with the OpenAI SDK using an API key, initialize the client by passing your API key to the SDK's configuration. This allows you to authenticate and interact with OpenAI's services seamlessly:

```csharp
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
```

## 3. Run a basic code sample
This sample demonstrates a basic call to the chat completion API. The call is synchronous.

```csharp
using OpenAI.Chat;
using Azure;
using Azure.AI.OpenAI;

var endpoint = new Uri("https://crysta-openai-hackathon.cognitiveservices.azure.com/");
var deploymentName = "gpt-4o-mini-hackaton-test";
var apiKey = "<your-api-key>";

AzureOpenAIClient azureClient = new(
    endpoint,
    new AzureKeyCredential(apiKey));
ChatClient chatClient = azureClient.GetChatClient(deploymentName);

var requestOptions = new ChatCompletionOptions()
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

var response = chatClient.CompleteChat(messages, requestOptions);
System.Console.WriteLine(response.Value.Content[0].Text);
```

## 4. Explore more samples
### Run a multi-turn conversation
This sample demonstrates a multi-turn conversation with the chat completion API. When using the model for a chat application, you'll need to manage the history of that conversation and send the latest messages to the model.

```csharp
using OpenAI.Chat;
using Azure;
using Azure.AI.OpenAI;

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

var response = chatClient.CompleteChat(messages);
System.Console.WriteLine(response.Value.Content[0].Text);
// Append the model response to the chat history.
messages.Add(new AssistantChatMessage(response.Value.Content[0].Text));
// Append new user question.
messages.Add(new UserChatMessage("What is so great about #1?"));

response = chatClient.CompleteChat(messages);
System.Console.WriteLine(response.Value.Content[0].Text);
```

### Stream the output
For a better user experience, you will want to stream the response of the model so that the first token shows up early and you avoid waiting for long responses.

```csharp
using OpenAI.Chat;
using Azure;
using Azure.AI.OpenAI;

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

var response = chatClient.CompleteChatStreaming(messages);

foreach (StreamingChatCompletionUpdate update in response)
{
    foreach (ChatMessageContentPart updatePart in update.ContentUpdate)
    {
        System.Console.Write(updatePart.Text);
    }
}
System.Console.WriteLine("");
```
