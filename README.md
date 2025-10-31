## **WhatsApp Google Drive Assistant (n8n)**

This repository contains the n8n workflow and documentation for a WhatsApp-based assistant that allows users to manage their Google Drive files through simple chat commands.

---

## Objective

The goal of this project is to create an n8n workflow where a user can interact with their Google Drive via WhatsApp messages. This allows for quick file management tasks like listing, deleting, moving, and summarizing files directly from a mobile device.

---

## Features

* **WhatsApp Commands:** The assistant responds to the following commands sent via WhatsApp:
    * `LIST /FolderName` → Lists all files in the specified folder.
    * `DELETE /FolderName/file.pdf` → Deletes a specific file.
    * `MOVE /SourceFolder/file.pdf /DestinationFolder` → Moves a file between folders.
    * `RENAME /FolderName/old_name.pdf new_name.pdf` → Renames a file.
    * `SUMMARY /FolderName` → Provides AI-powered summaries of all text-based files in a folder.
    * `UPLOAD /FolderName new_name.pdf` → Attach a file with this command to upload it to the specified folder with a new name.

* **Google Drive Integration:** Securely connects to a user's Google Drive using OAuth2. It has permissions to read, write, update, and delete files and folders.

* **AI-Powered Summaries:** Utilizes the OpenAI API (GPT-4o) to summarize the content of `.pdf`, `.txt`, and Google Docs files.

---

## Project Files

* **`workflow.json`**: The complete n8n workflow that can be imported directly into an n8n instance.
* **`troubleshooting.md`**: A summary of the extensive troubleshooting process required to overcome platform-specific bugs and version incompatibilities.
* **`README.md`**: This file.

---

## Screenshot

![n8n_flow.png](/relative/path/to/img.jpg?raw=true "n8n_flow.png")

---

## Setup Instructions

1.  **Prerequisites:**
    * An active n8n instance (tested on version `1.114.3`).
    * A Meta for Developers account with a configured WhatsApp Business App.
    * A Google Cloud Platform project with the Google Drive API enabled.
    * An OpenAI account with API key.

2.  **n8n Credentials:**
    * **Google Drive OAuth2:** Create and configure OAuth2 credentials for the Google Drive API in your n8n instance.
    * **OpenAI API Key:** Add your OpenAI API key as a credential.
    * **Meta Permanent Token:** Create a `Header Auth` credential named `Meta Permanent Token` with the value `Bearer YOUR_PERMANENT_TOKEN`.

3.  **Workflow Import:**
    * Import the included `workflow.json` file into your n8n instance.

4.  **Configuration:**
    * **Webhook Trigger:** Open the `Webhook` node, copy the **Test URL**, and use it to configure the webhook in your Meta App dashboard. Remember to set the **HTTP Method** to `GET` for the initial verification, then change it to `POST` for receiving messages.
    * **HTTP Request Nodes:** In all `HTTP Request` nodes used for sending replies, update the URL with your **Phone Number ID** from the Meta dashboard.
    * **Credentials:** Ensure all Google Drive and OpenAI nodes are linked to the correct credentials you created.

---

## Project Limitations and Known Issues

* **n8n Version Dependency:** The workflow is specifically tailored for n8n version `1.114.3` due to UI and node availability issues. The use of the generic `Webhook` trigger and `Custom Query` for Google Drive searches are workarounds for bugs present in that version.
* **Hardcoded AI Model:** The `SUMMARY` branch uses a hardcoded model (`gpt-4o`). This could be parameterized for more flexibility.
* **Limited Error Handling:** While `IF` nodes check for the existence of files and folders, the workflow does not handle all potential errors gracefully (e.g., API rate limits, invalid commands, AI summary failures). Error messages for the user are basic.
* **No Multi-User Support:** The entire workflow is designed for a single user's Google Drive, authenticated via a single credential. It does not support multiple users authenticating with their own accounts.
* **Manual Setup for First Message:** To initiate a test conversation, the first message must be sent programmatically via an API call (like `curl` or `Invoke-WebRequest`) or by using the Meta dashboard, as direct messaging to a test number is not possible.
* **Text-Only Summaries:** The `SUMMARY` function only works for text-extractable files (PDF, TXT, GDocs) and will fail on other file types like images or spreadsheets.


