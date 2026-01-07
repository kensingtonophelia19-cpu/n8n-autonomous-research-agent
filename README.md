🕵️‍♂️ Autonomous AI Research Agent (n8n)
An automated intelligence pipeline built in n8n that autonomously researches topics, scrapes web content, analyzes it using GPT-4, and builds a strategic knowledge base in Airtable.

![Workflow View](images/Autonomous%20Research%20Agent%201.png)

🚀 Features
Autonomous Daily Trigger: Wakes up on a schedule to perform fresh research.

Smart Deduplication: Checks your database before scraping to avoid processing the same link twice.

Anti-Bot Scraping: Uses header rotation to bypass basic bot protection and scrape real article text.

Executive AI Analysis: Uses a custom "Executive Intelligence" prompt to turn raw text into bulleted strategic insights (no fluff).

Junk Filtering: Automatically discards blocked pages, cookies notices, or empty sites before they clutter your database.

Sentiment & Action Logic: Automatically tags content sentiment and suggests the "Next Action" (e.g., Share with Team, Archive, Investigate).

🛠️ Tech Stack
Workflow Engine: n8n

Search: Google Custom Search JSON API

Database: Airtable

AI Analysis: OpenAI (GPT-4o-mini or GPT-4)

Scraping: Native n8n HTTP Request (with header masquerading)

📋 Prerequisites
Before importing this workflow, ensure you have the following accounts and keys:

n8n: Self-hosted or Cloud version.

Google Cloud Platform:

Enable Custom Search API.

Create a Programmable Search Engine (CSE) to get your cx ID.

Generate an API Key.

OpenAI API Key: With access to gpt-4o or gpt-4o-mini.

Airtable Base: Create a table with the specific columns listed below.

⚙️ Setup Instructions
1. Database Setup (Airtable)
Create a table named Research Logs with the following columns:

Topic (Single Line Text)

Summary (Long Text)

Source Link (URL)

Date Researched (Date)

Status (Single Select: New, Done, Reviewing)

Summary Sentiment (Single Line Text or Single Select)

Next Action (Single Line Text)

(Optional) Source Domain (Formula: =LEFT(URL, ...))

2. Import Workflow
Download the JSON file from this repository.

Open your n8n editor.

Click Menu (top right) > Import from File.

3. Configure Credentials
Update the nodes with your specific credentials:

Google Search Node:

q: Your search topic (e.g., "SpaceX Starship updates 2025").

cx: Your Search Engine ID.

key: Your Google Cloud API Key.

Airtable Nodes: Connect your account and select your Base/Table.

OpenAI Node: Connect your API Key.

🧠 Workflow Logic Breakdown
1. Discovery Phase
Daily Schedule: Triggers the workflow.

Google Search: Fetches the top 10 results for the defined query.

Deduplication Filter: Compares new URLs against existing URLs in Airtable. If a link exists, it is skipped.

2. Extraction Phase
Loop: Iterates through new unique links one by one.

Scrape HTML: Masquerades as a Chrome browser (User-Agent headers) to fetch page HTML.

Clean HTML: Code node strips HTML tags and truncates text to ~6,000 characters to prevent token overflow.

3. Analysis Phase
AI Analyst: Sends cleaned text to OpenAI with a strict "System Prompt" to generate a JSON object containing:

3-bullet executive summary.

Sentiment analysis.

Relevance score.

Junk Filter (Code Node): Checks if the AI output contains phrases like "Please provide text" or "Access Denied". If detected, the item is dropped to keep the database clean.

4. Storage Phase
Save to Airtable: Maps the valid JSON data into the database columns (Summary, Link, Date, etc.).

Rate Limit: A Wait node pauses for 10-20 seconds between items to respect API rate limits.

📝 Customization
Changing the "Executive Prompt"
To adjust the tone of the summaries, edit the System Message in the AI Analyst node.

Current Mode: Strategic/Briefing style.

Edit: You can change this to "Academic", "Simple", or "Twitter Thread" style depending on your needs.

Modifying the Search Topic
To change what the agent researches, simply update the Value of the q parameter in the Google Search node.
