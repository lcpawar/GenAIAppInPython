# 🧐 Create a Generative AI Chat App with Azure AI Foundry

This exercise guides you through building a generative AI chat app using the **Azure AI Foundry SDK**. You'll deploy a GPT-4o model and develop a simple client app using Python or C#.

> **Estimated time**: \~40 minutes
> **Note**: This exercise uses *pre-release SDKs*. You may encounter warnings or version conflicts.

---

## 🚀 Deploy a Model in Azure AI Foundry

1. Navigate to [https://ai.azure.com](https://ai.azure.com) and sign in with Azure credentials.
2. Close any onboarding tips and return to the **Home** page.
3. In **Explore models and capabilities**, search for **`gpt-4o`**.
4. Select **Use this model**.
5. Create a new project:

   * **Azure AI Foundry resource**: Valid resource name
   * **Subscription**: Your Azure subscription
   * **Resource group**: Create/select a group
   * **Region**: Choose any AI-supported location
6. Click **Create** and wait for the deployment.
7. Once complete, the **Chat Playground** opens automatically.
8. Note the **model deployment name** (e.g., `gpt-4o`) in the **Setup** pane or under **Models and endpoints**.
9. Go to **Overview** for a summary of your project.

---

## 💻 Create a Client Application

You may choose to use **Python** or **C#**.

### 📚 Prepare Configuration

1. In the Azure AI Foundry portal, navigate to your project **Overview**.
2. Note your **Project Endpoint**.
3. Open [https://portal.azure.com](https://portal.azure.com) in a new tab.
4. Launch **Cloud Shell** (choose **PowerShell** environment).
5. In Cloud Shell settings, select **Go to Classic version** (for editor support).
6. Clone the exercise repository:

```powershell
rm -r mslearn-ai-foundry -f
git clone https://github.com/microsoftlearning/mslearn-ai-studio mslearn-ai-foundry
```

7. Navigate to your language folder:

#### Python:

```powershell
cd mslearn-ai-foundry/labfiles/chat-app/python
ls -a -l
```

#### C#:

```powershell
cd mslearn-ai-foundry/labfiles/chat-app/c-sharp
ls -a -l
```

8. Install dependencies:

#### Python:

```powershell
python -m venv labenv
./labenv/bin/Activate.ps1
pip install -r requirements.txt azure-identity azure-ai-projects openai
```

#### C#:

```powershell
dotnet add package Azure.Identity --prerelease
dotnet add package Azure.AI.Projects --prerelease
dotnet add package Azure.AI.OpenAI --prerelease
```

9. Edit your config file:

#### Python:

```powershell
code .env
```

#### C#:

```powershell
code appsettings.json
```

Replace:

* `your_project_endpoint` ➔ Azure AI Foundry endpoint
* `your_model_deployment` ➔ `gpt-4o`

Save changes and close the editor.

---

## 📄 Write Code to Connect and Chat

Edit your main application code:

#### Python:

```powershell
code chat-app.py
```

#### C#:

```powershell
code Program.cs
```

### ➕ Add References

#### Python:

```python
from azure.identity import DefaultAzureCredential
from azure.ai.projects import AIProjectClient
from openai import AzureOpenAI
```

#### C#:

```csharp
using Azure.Identity;
using Azure.AI.Projects;
using Azure.AI.OpenAI;
using OpenAI.Chat;
```

### 🚡 Initialize the Project Client

#### Python:

```python
project_client = AIProjectClient(
    credential=DefaultAzureCredential(
        exclude_environment_credential=True,
        exclude_managed_identity_credential=True
    ),
    endpoint=project_endpoint,
)
```

#### C#:

```csharp
DefaultAzureCredentialOptions options = new()
{
    ExcludeEnvironmentCredential = true,
    ExcludeManagedIdentityCredential = true
};
var projectClient = new AIProjectClient(
    new Uri(project_connection),
    new DefaultAzureCredential(options));
```

### 🤖 Get a Chat Client

#### Python:

```python
openai_client = project_client.inference.get_azure_openai_client(api_version="2024-10-21")
```

#### C#:

```csharp
ChatClient openaiClient = projectClient.GetAzureOpenAIChatClient(
    deploymentName: model_deployment,
    connectionName: null,
    apiVersion: "2024-10-21"
);
```

### 🦬 Add System Prompt

#### Python:

```python
prompt = [
    {"role": "system", "content": "You are a helpful AI assistant that answers questions."}
]
```

#### C#:

```csharp
var prompt = new List<ChatMessage>() {
    new SystemChatMessage("You are a helpful AI assistant that answers questions.")
};
```

### ♻️ Chat Loop

#### Python:

```python
prompt.append({"role": "user", "content": input_text})
response = openai_client.chat.completions.create(
    model=model_deployment,
    messages=prompt
)
completion = response.choices[0].message.content
print(completion)
prompt.append({"role": "assistant", "content": completion})
```

#### C#:

```csharp
prompt.Add(new UserChatMessage(input_text));
ChatCompletion completion = openaiClient.CompleteChat(prompt);
var completionText = completion.Content[0].Text;
Console.WriteLine(completionText);
prompt.Add(new AssistantChatMessage(completionText));
```

---

## 🔐 Sign In and Run the App

Authenticate and run:

```powershell
az login
```

### Run the App:

#### Python:

```powershell
python chat-app.py
```

#### C#:

```powershell
dotnet run
```

> If you see a .NET version error, use `dotnet --version` and update the `.csproj` file accordingly.

---

## 📉 Test It Out!

Try:

* "What is the fastest animal on Earth?"
* Follow-ups like "Where can I see one?" or "Are they endangered?"

Type `quit` to end the chat.

> If you see a rate limit or quota error, wait a few seconds and try again.

---

## 📅 Summary

You:

* Deployed a GPT-4o model using Azure AI Foundry
* Built a Python or C# client using Azure AI SDKs
* Interacted with a generative chat model

---

## 🛢️ Clean Up Resources

To avoid charges:

1. Go to [Azure Portal](https://portal.azure.com)
2. Locate your **Resource Group**
3. Select **Delete Resource Group**
4. Confirm the name to delete all associated resources

---

Happy building with Azure AI Foundry! 🚀
