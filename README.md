### **GitHub Repository: WhatsApp Google Drive Assistant (n8n)**

#### **`README.md`**

```markdown
# WhatsApp-driven Google Drive Assistant (n8n)

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

```

-----

#### **`troubleshooting.md`**

```markdown
# Troubleshooting Summary

This document outlines the significant challenges and solutions discovered during the development of the WhatsApp Google Drive Assistant workflow. The process was defined by a series of platform-specific bugs and version incompatibilities.

### 1. n8n Version & UI Inconsistencies (v1.114.3)

The user's n8n instance presented a different UI and node set than modern versions, requiring several workarounds:

-   **Problem:** The dedicated `WhatsApp Business Cloud` trigger node was bugged, incorrectly opening an older `WhatsApp Trigger` node that required the wrong authentication.
-   **Solution:** We bypassed this entirely by using the generic **`Webhook`** trigger and a **`Respond to Webhook`** node to manually handle Meta's verification challenge.

-   **Problem:** The `Set` node was not available.
-   **Solution:** Replaced with the more flexible **`Code`** node to parse inputs and set variables using JavaScript.

-   **Problem:** The Google Drive node's simple filters (`Name`, `Mime Type`) were not available.
-   **Solution:** Switched to using the **`Custom Query`** search method, writing raw Google Drive API query strings to find files and folders.

-   **Problem:** The `File: Update` operation for moving files did not have a simple "Move" option.
-   **Solution:** Discovered the correct method for this version was to use the **`Properties`** option and set the `addParents` and `removeParents` keys directly.

### 2. Meta API Connection and Testing

The final and most persistent challenge was initiating a test conversation.

-   **Problem:** After all n8n setup was complete, API calls to send the first message failed with a `(#133010) Account not registered` error, despite the recipient number being verified.

-   **Troubleshooting Steps:**
    1.  Corrected `curl` command syntax for the user's PowerShell environment.
    2.  Confirmed recipient number was verified by deleting and re-adding it.
    3.  Generated a new Permanent Access Token with the correct `whatsapp_business_messaging` and `whatsapp_business_management` permissions.

-   **Final Diagnosis:** After eliminating all user-side configuration errors, we concluded the issue was a persistent, unrecoverable bug in the **Meta Test Number environment** itself.

-   **Final Solution:** The problem was resolved by creating a **brand new Meta App**. This provisioned a new, clean test environment with a new test number, which worked immediately. This confirmed the issue was platform-side with the original test account.

```
