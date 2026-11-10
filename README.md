# Support Reply AI

A small Gemini-assisted customer service workflow. It receives a message, categorizes it and returns a **draft** response in German. It does **not** send an email.

**Flow:** Webhook -> Validate -> IF -> Build Prompt -> Gemini API -> Format Reply -> Respond

## Setup

1. Import `workflow.json` into n8n.
2. Open the **Ask Gemini** HTTP Request node.
3. Under **Authentication**, keep **Generic Credential Type > Header Auth**.
4. Create a **Header Auth** credential with:
   - **Name:** `x-goog-api-key`
   - **Value:** your Gemini API key (from Google AI Studio)
5. Select the credential in the node and save it.
6. If `gemini-2.5-flash` is not available in your account, choose an available Gemini model and update the request URL.
7. In **Support Webhook**, click **Listen for test event**.

## Test

```powershell
$body = Get-Content .\03-support-reply-ai\example_request.json -Raw
Invoke-RestMethod -Uri "http://localhost:5678/webhook-test/support-reply" -Method Post -ContentType "application/json" -Body ([Text.Encoding]::UTF8.GetBytes($body))
```

## Output

`category`, `priority`, `summary`, `suggested_reply`, `needs_review`.

The input `message` must be 10–5000 characters. Invalid inputs receive HTTP 400. Gemini or network failures will return an n8n error unless you add an error-handling workflow.

## Notes

- The draft must be reviewed before it is sent.
- Do not send real personal or confidential customer data to an external model without a lawful basis and suitable privacy arrangements.
- Never commit a Google API key to GitHub.
