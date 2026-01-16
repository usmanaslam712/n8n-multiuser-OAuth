# n8n Multi-User OAuth Workflow with MCP

Manage OAuth credentials for multiple users in n8n without external databases or token refresh logic.

## The Problem

The standard approach to multi-user OAuth in n8n requires:
- External database for credential storage
- Custom OAuth capture workflows
- Database query logic for each workflow
- User ID routing systems
- Row-level security management

This is way too complex for what should be simple.

## The Solution

This workflow uses MCP (Model Context Protocol) gateways to handle multi-user OAuth centrally. Pass `tenant_id` as a variable and MCP automatically routes to the correct user's OAuth credentials.

## Tools Used

- **[Fastn MCP](https://fastn.ai/)** - MCP gateway for managing multi-user OAuth
- **n8n** - Workflow automation platform
- **Google Docs API** - Document creation (via MCP)
- **Gmail API** - Email notifications (via MCP)

## Setup Instructions

### 1. Get Fastn MCP Credentials

- Sign up at [fastn.ai](https://fastn.ai)
- Create a new space
- Get your `space_id` and `auth_token` from the dashboard

### 2. Import Workflow

- Download `multi-user-workflow.json`
- In n8n: Settings → Import from File
- Select the downloaded JSON file

### 3. Configure Credentials

Open each HTTP Request node (`Init MCP Session`, `Create a Google Doc`, `Send Gmail Notification`) and replace:

- `YOUR_SPACE_ID` → Your Fastn space ID (appears twice in each URL)
- `YOUR_AUTH_TOKEN` → Your Fastn auth token

### 4. Connect OAuth Apps

In your Fastn dashboard:
- Connect Google Docs
- Connect Gmail
- MCP will handle OAuth for each user based on `tenant_id`

### 5. Test the Workflow

Activate the workflow and send a POST request to the webhook URL:
```bash
curl -X POST https://your-n8n-instance.com/webhook/client-report \
  -H "Content-Type: application/json" \
  -d '{
    "user_id": "user_123",
    "client_name": "Acme Corp",
    "recipient_email": "client@example.com"
  }'
```

## How It Works

1. **Webhook** receives request with `user_id`, `client_name`, and `recipient_email`
2. **Init MCP Session** establishes connection with `tenant_id` routing
3. **Create a Google Doc** uses MCP to create doc under the user's Google account
4. **Send Gmail Notification** sends email from the user's Gmail
5. **Respond to Webhook** confirms success

The key is the `tenant_id` parameter in the MCP URL - it automatically routes to the correct user's OAuth credentials without you managing tokens.

## What You Get

✅ No external database needed  
✅ No token refresh logic  
✅ No credential storage complexity  
✅ Each user's OAuth handled automatically  
✅ Scales to unlimited users

## Example Use Cases

- Client report generation for multiple team members
- Multi-tenant SaaS workflows
- Agency automation where each client has their own OAuth
- Team workflows without shared credentials

## Architecture
```
User Request (with tenant_id)
    ↓
n8n Webhook
    ↓
MCP Gateway (Fastn)
    ↓
Routes to correct user's OAuth
    ↓
Google Docs / Gmail APIs
    ↓
Response back to user
```

## Learn More

Read the full breakdown: [Managing n8n Multi-User OAuth Without the Headaches](https://medium.com/@usmanaslam712/managing-n8n-multi-user-oauth-without-the-headaches-354b2c12b307)

## Contributing

Found an issue or have an improvement? Open an issue or submit a PR.

## License

MIT License - feel free to use this in your projects.

## Questions?

Reach out on the [n8n community forum](https://community.n8n.io/) or open an issue.
