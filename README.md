# AI-Powered Job Search Automation (n8n)

An intelligent, automated workflow designed to streamline job hunting. This n8n workflow uses Large Language Models (LLMs) and search APIs to find, extract, and organize job opportunities into a structured Google Sheet based on your personal preferences.

![n8n Workflow Screenshot](Screenshot%202026-03-13%20162842.png)

## Features

- **Personalized Search**: Captures job title, location, work type, and seniority through a simple n8n form.
- **AI Query Generation**: Uses Groq (Llama 3.3 70B) to generate optimized, boolean-powered search queries.
- **Deep Web Search**: Leverages the Serper API (Google Search) to find the most relevant and recent job listings.
- **Automated Data Extraction**: Employs AI to parse websites and snippets, extracting company names, websites, locations, and direct job URLs.
- **Structured Storage**: Automatically appends job opportunities to a Google Sheet with timestamps for easy tracking.

## Workflow Architecture

The workflow consists of several interconnected nodes, each handling a specific part of the automation pipeline:

1.  **n8n Form Trigger**: 
    - Provides a user-friendly interface to input criteria.
    - Fields: `Job Title`, `Location`, `Work Type` (Hybrid, Remote, Onsite), and `Seniority Level`.
2.  **Groq: Generate Search Query**: 
    - **Model**: `llama-3.3-70b-versatile`
    - **Prompt**: Specifically engineered to output a Google-ready search string (e.g., `site:lever.co | site:greenhouse.io "Software Engineer" New York`).
3.  **Search API (Serper)**: 
    - Executes the generated query via Serper.dev.
    - Configured to return the top 20 organic results.
4.  **Groq: Extract Job Data**: 
    - **Model**: `llama-3.3-70b-versatile`
    - **Logic**: Iterates through search results snippets and extracts structured data.
    - **Output**: Raw JSON array for robust downstream processing.
5.  **Parse & Format Data (Code Node)**: 
    - JavaScript logic to sanitize LLM output.
    - Removes markdown artifacts (e.g., ```json blocks).
    - Injects an ISO timestamp for every entry.
6.  **Google Sheets**: 
    - Appends results to the bottom of your designated sheet.

## Prerequisites

To use this workflow, you will need the following:

- **n8n**: A self-hosted or cloud instance of n8n.
- **Groq API Key**: [Get it here](https://console.groq.com/).
- **Serper API Key**: [Get it here](https://serper.dev/).
- **Google Cloud Console Project**: Enable Google Sheets API and create OAuth2 credentials.
- **Google Sheet**: A spreadsheet with headers: `companyName`, `website`, `location`, `employeeCount`, `jobUrl`, `Timestamp`.

## Setup Instructions

1.  **Import the Workflow**:
    - Download the `AI-Powered Job Research Automation.json` file.
    - In n8n, go to **Workflows > Import from File** and select the JSON.

2.  **Configure API Credentials**:
    - **Groq**: In both Groq nodes, update the Authorization header: `Bearer YOUR_GROQ_API_KEY`.
    - **Serper**: In the Search API node, update the `X-API-KEY` header.
    - **Google Sheets**: Select your OAuth2 credential and Spreadsheet ID in the Google Sheets node.

3.  **Security Note**: 
    > [!IMPORTANT]
    > It is highly recommended to use **n8n Credentials** or **Environment Variables** for your API keys instead of hardcoding them directly into the HTTP Request nodes to keep your keys secure if you share your workflow.

## Usage

1.  Activate the workflow and open the **n8n Form Trigger** URL.
2.  Fill in your target Job Title, Location, Work Type, and Seniority Level.
3.  Submit the form.
4.  Check your Google Sheet after a few seconds for the new listings!

## Troubleshooting

- **Parsing Errors**: If the LLM output isn't a valid JSON array, check the `Parse & Format Data` node's execution log.
- **Rate Limits**: Groq and Serper have free-tier limits. Ensure you aren't exceeding them during testing.
- **Schema Mismatch**: Ensure your Google Sheet column names exactly match the keys in the JSON output (`companyName`, etc.).

