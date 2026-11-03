# n8n Automation Setup: Google Docs & AI Validation

This guide outlines the complete process for connecting a local n8n instance to the Google Docs API to automate research paper validation using AI.

## Phase 1: Google Cloud Project Setup

Before n8n can read your documents, you need to create a project in Google Cloud and enable the necessary APIs.

1. **Access Google Cloud Console**: Go to [console.cloud.google.com](https://console.cloud.google.com).
2. **Handle Security Requirements**: Ensure 2-Step Verification (MFA) is enabled on your Google account, or Google Cloud will block your access.
3. **Create a New Project**: 
   * Click **Select a project** (top left) -> **New Project**.
   * *Note:* If you hit a project limit, you must either request a quota increase or use a fresh Google account.
   * Enter a project name (must be between 4 and 30 characters, e.g., `n8n-research-auto`). 
   * Leave location as "No organization" and click **Create**.
4. **Enable the Google Docs API**:
   * Use the top search bar to search for **Google Docs API**.
   * Click on the API in the results and click the blue **Enable** button.

## Phase 2: OAuth Consent Screen Configuration

You must configure how Google asks for your permission to access your files.

1. Navigate to **APIs & Services** > **OAuth consent screen** (via the left-hand hamburger menu).
2. Under "User Type", select **External** and click **Create**.
3. **App Information**:
   * **App name**: e.g., `Automation` or `n8n-docs`.
   * **User support email**: Select your own Gmail address from the dropdown.
4. **Developer Contact Information**:
   * Enter your Gmail address again at the very bottom and click **Save and Continue**.
5. **Scopes**: Skip this step by clicking **Save and Continue**.
6. **Test Users (CRITICAL STEP)**: 
   * Because your app is in "Testing" mode, it will block anyone not explicitly authorized.
   * Click **+ Add Users**.
   * Type your exact Gmail address (e.g., `your.email@gmail.com`) and click **Save**.
   * Click **Save and Continue** to finish.

## Phase 3: Generating Credentials

Now, you generate the specific keys n8n needs to connect.

1. Navigate to **APIs & Services** > **Credentials**.
2. Click **+ CREATE CREDENTIALS** (top of the screen) and select **OAuth client ID**.
3. **Application Type**: Select **Web application**.
4. **Name**: Leave as default or name it (e.g., `n8n Web Client`).
5. **Authorized Redirect URIs**:
   * Scroll to the bottom and click **+ Add URI**.
   * Paste the exact n8n callback URL: `http://localhost:5678/rest/oauth2-credential/callback`
   * Click **Create**.
6. **Copy your Keys**: A pop-up will appear. Copy the **Client ID** and **Client Secret**. Keep this window open or paste them temporarily into a notepad.

## Phase 4: Connecting to n8n

1. **Add the Google Docs Node**: Open your n8n workflow canvas, click the `+` button, and add a **Google Docs** node. Action should be set to "Get a document".
2. **Setup Credential**:
   * Under "Credential", select **Create New Credential**.
   * Paste the **Client ID** and **Client Secret** you got from Google Cloud.
   * Ensure the OAuth Redirect URL matches the one you put in Google Cloud.
   * Click **Save** in the top right corner.
3. **Authenticate**:
   * Click the **Sign in with Google** button in n8n.
   * *Security Bypass*: Google will say "Google hasn't verified this app." Click **Advanced**, then click **Go to [App Name] (unsafe)**.
   * Check the boxes to grant access to your Google Docs and click **Continue**.
4. **Execute**:
   * Close the credential window. 
   * In the Google Docs node settings, paste the URL of your research paper into the **Doc ID or URL** field.
   * Click **Execute step** to verify n8n can successfully read your document's text.

## Phase 5: Adding AI and Output (Next Steps)

Once the document text is successfully pulled into n8n:
1. Connect an **AI Agent** (or basic LLM node like Google Gemini/OpenAI) after the Google Docs node.
2. In the AI prompt, instruct the AI to act as a reviewer, paste your grading rubric, and drag the text output from the Google Docs node into the prompt.
3. Connect an output node (like **Discord**, **Gmail**, or **Google Docs - Append**) to send the AI's critique back to you and your team.