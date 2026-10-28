Unread Email Summariser (n8n Workflow)

An automated n8n workflow that fetches unread emails from your Gmail inbox, uses an AI Agent to generate concise 20-word summaries for each, formats them into a clean HTML digest with distinct line spacing, and sends the compiled summary directly to your inbox.

📌 Features

On-Demand Execution: Triggered manually whenever you want an inbox catch-up.

Automated Summarisation: Processes up to 5 unread emails at a time using an OpenAI-powered AI Agent node.

Strict Formatting Constraints: Limits each summary to 20 words maximum, removing greetings, subject headers, and polite filler.

Clean HTML Formatting: Combines all summaries into a readable email digest separated by double-spaced paragraph breaks.

Secure Credentials: Keeps your OpenAI and Google credentials safely configured within n8n.

🛠 Workflow Architecture

The workflow follows a 5-step linear pipeline:

[ Manual Trigger ] ➔ [ Gmail (Get Many) ] ➔ [ AI Agent + OpenAI Model ] ➔ [ Code (JavaScript) ] ➔ [ Gmail (Send) ]


Manual Trigger (When clicking 'Execute workflow'): Initiates execution on demand.

Gmail (Get many messages): Queries Gmail for 5 unread messages.

AI Agent Node (AI Agent + OpenAI Chat Model): Processes each email individually to create a short summary.

Code Node (Code in JavaScript): Aggregates individual outputs into an HTML-formatted single string with clear paragraph spacing.

Gmail (Send a message): Sends the compiled summary email to your target email address.

🚀 Setup & Configuration Guide

Step 1: Add Credentials in n8n

Gmail OAuth2 API Credential:

Go to Credentials in n8n > Create New > Select Gmail OAuth2 API.

Authenticate your Google account with the required permissions to read and send emails.

OpenAI API Credential:

Go to Credentials > Create New > Select OpenAI API.

Enter your OpenAI API Key (stored securely inside n8n).

Step 2: Node Setup & Configuration

1. Manual Trigger Node

Node Type: Manual Trigger

Name: When clicking 'Execute workflow'

Configuration: No extra settings needed.

2. First Gmail Node (Fetch Emails)

Node Type: Gmail

Name: Get many messages

Resource: Message

Operation: Get Many

Parameters:

Read Status: Unread

Limit: 5

3. AI Agent Node & OpenAI Chat Model

Node Type: AI Agent

Connected Sub-Node: Connect an OpenAI Chat Model node to the Chat Model input port. Select your OpenAI credential and choose model gpt-4o-mini (or gpt-3.5-turbo).

Prompt / Instructions:

Summarize the provided email snippet into a single sentence of 20 words maximum. Focus strictly on the core action or topic. Do NOT include any greeting, subject line, headings, bullet points, or polite closing.

Email Content: {{$json.snippet}}


4. Code Node (JavaScript Aggregator)

Node Type: Code

Name: Code in JavaScript

Mode: Run Once for All Items

Language: JavaScript

Code snippet:

// Map through each summary item and wrap in HTML paragraph tags for explicit line spacing
const listItems = items
  .map(item => {
    let text = item.json.output || item.json.text || item.json.message || '';
    text = text.trim().replace(/^[-*•]\s*/, '');
    return `<p style="margin-bottom: 14px; margin-top: 0;">${text}</p>`;
  })
  .filter(Boolean)
  .join('');

return [{ json: { htmlSummary: listItems } }];


5. Second Gmail Node (Send Summary Email)

Node Type: Gmail

Name: Send a message

Resource: Message

Operation: Send

Parameters:

To: your-email@gmail.com

Subject: Your 5 unread emails.

Email Type / Body Type: HTML

HTML Body:

{{ $json.htmlSummary }}


📧 Example Output

When triggered, the email delivered to your inbox will be formatted as follows:

Subject: Your 5 unread emails.

Today's Medium daily digest features highlights from Titu Durais and Saquib Aftab.

The latest data shows Anitha R enrolled in the ML course with an undefined status.

The latest data indicates that Ravi Kumar is enrolled in the Python course with an undefined status.

The latest data shows Priya S enrolled in the Machine Learning course with an undefined status.

The latest data reveals that Manoj K has passed the Python course.

📝 Workflow Details

Workflow Name: Unread Email Summariser

Environment: Compatible with n8n v1.0+
