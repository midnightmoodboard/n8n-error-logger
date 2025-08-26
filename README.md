# N8N Error Logger

Automated error logging workflow that captures all N8N workflow failures and logs detailed error information to a Notion database for centralized monitoring.

## 🚨 What It Does

This N8N error workflow automatically captures:

- **Workflow Failures**: Any workflow that encounters an error
- **Node Failures**: Specific node that caused the failure
- **Error Details**: Complete error messages, stack traces, and metadata
- **Execution Context**: Timestamp, execution ID, and dashboard links
- **Centralized Logging**: All errors stored in Notion Error Database

## ⚡ Workflow Architecture

### Nodes
1. **Error Trigger**: Automatically triggers when any workflow fails
2. **Format Error Data**: Extracts and structures error information
3. **Log to Notion**: Creates detailed error record in database

### Error Handling
- Captures all workflow errors automatically
- Processes error context with fallback values
- Logs to Notion with `continueOnFail: true` to prevent error loops

## 📊 Error Data Captured

### Error Database (23d4ea7cdefa80ccb756dc7794f7c972)
Detailed error records:
```json
{
  "workflow_name": "Server Health Monitor",
  "workflow_id": "1kZkMI3Nf4sbkKF0", 
  "error_message": "Connection timeout after 10000ms",
  "error_type": "ConnectionError",
  "error_stack": "Full stack trace...",
  "timestamp": "2025-08-26T17:45:00Z",
  "execution_id": "uuid-execution-id",
  "failed_node": "Check Website",
  "node_type": "n8n-nodes-base.httpRequest",
  "dashboard_url": "https://n8n.getfoundstocks.com/workflow/1kZkMI3Nf4sbkKF0",
  "execution_url": "https://n8n.getfoundstocks.com/execution/uuid",
  "severity": "Error",
  "status": "Failed"
}
```

### Error Properties in Notion
- **Error Message** (Title): Primary error description
- **Workflow** (Rich Text): Name of failed workflow
- **Node** (Rich Text): Specific node that failed
- **Timestamp** (Date): When error occurred
- **Severity** (Select): Error severity level
- **Status** (Select): Current status (Failed)
- **Execution URL** (URL): Direct link to failed execution

## 🔧 Installation

### Prerequisites
- N8N instance with error workflow support
- Notion API credential configured (ID: `SRYmkeEiOj8uO1Vp`)
- Error Database created in Notion

### Deploy Error Workflow
1. Import `error-logger.json` to your N8N instance
2. Verify Notion credential is configured
3. **Do NOT activate manually** - Error trigger activates automatically
4. Set as error workflow for other workflows in their settings

### Link to Other Workflows
Add this error workflow to any workflow's settings:
```json
{
  "settings": {
    "errorWorkflow": "NvNkRyZqqBY8PXqt"
  }
}
```

## 🔍 Error Monitoring

### Check Error Workflow Status
```bash
curl -X GET "https://n8n.getfoundstocks.com/api/v1/workflows/NvNkRyZqqBY8PXqt" \
  -H "X-N8N-API-KEY: your-key"
```

### View Error Executions
```bash
curl -X GET "https://n8n.getfoundstocks.com/api/v1/executions?workflowId=NvNkRyZqqBY8PXqt&limit=5" \
  -H "X-N8N-API-KEY: your-key"
```

### Test Error Handling
Create a test workflow that deliberately fails:
```json
{
  "nodes": [
    {
      "name": "Test Error",
      "type": "n8n-nodes-base.httpRequest",
      "parameters": {
        "url": "https://nonexistent-domain-12345.com"
      }
    }
  ],
  "settings": {
    "errorWorkflow": "NvNkRyZqqBY8PXqt"
  }
}
```

## 📈 Error Categories

### Common Error Types
- **ConnectionError**: Network timeouts, DNS failures
- **ValidationError**: Invalid data, missing parameters  
- **AuthenticationError**: API key issues, permission denied
- **ServerError**: 5xx HTTP responses
- **NodeError**: N8N node configuration issues

### Error Severity Levels
- **Critical**: System-wide failures, authentication issues
- **Error**: Workflow failures, data processing errors (default)
- **Warning**: Recoverable issues, timeouts with retries

## ⚙️ Configuration

### Error Workflow Settings
```json
{
  "settings": {
    "executionOrder": "v1"
  }
}
```

### Notion Database
- **Database ID**: `23d4ea7cdefa80ccb756dc7794f7c972`
- **Required Properties**: Error Message (title), Workflow, Node, Timestamp, Severity, Status, Execution URL

### Credential Requirements
- **Notion API**: Credential ID `SRYmkeEiOj8uO1Vp`

## 🎯 Success Metrics

- ✅ All workflow errors automatically captured
- ✅ Detailed error context preserved
- ✅ Direct links to failed executions
- ✅ Centralized error database in Notion
- ✅ No error loops or infinite recursion

## 🛠️ Troubleshooting

### Error Workflow Not Triggering
1. Check if workflow is linked in other workflow settings
2. Verify error workflow ID is correct: `NvNkRyZqqBY8PXqt`
3. Ensure error trigger node is properly configured

### Missing Error Data in Notion
- Verify credential `SRYmkeEiOj8uO1Vp` is valid
- Check database ID: `23d4ea7cdefa80ccb756dc7794f7c972`
- Review Notion database properties match expected schema

### Error Workflow Itself Failing
- Check N8N logs: `docker logs getfoundstocks-n8n-prod`
- Verify Notion API connectivity
- Test credential manually

### Debug Commands
```bash
# Check error workflow status
curl "https://n8n.getfoundstocks.com/api/v1/workflows/NvNkRyZqqBY8PXqt" | jq '.data.active'

# View recent error executions
curl "https://n8n.getfoundstocks.com/api/v1/executions?workflowId=NvNkRyZqqBY8PXqt&limit=5"

# Test Notion credential
curl -X GET "https://api.notion.com/v1/databases/23d4ea7cdefa80ccb756dc7794f7c972" \
  -H "Authorization: Bearer your-notion-token" \
  -H "Notion-Version: 2022-06-28"
```

## 🔄 Integration with Other Workflows

### Server Health Monitor
The Server Health Monitor workflow uses this error handler:
```json
{
  "settings": {
    "errorWorkflow": "NvNkRyZqqBY8PXqt"
  }
}
```

### Expected Error Scenarios
- Network connectivity issues (website down)
- API rate limits (Notion, external services)  
- Docker container access failures
- Invalid credentials or permissions

## 📊 Error Dashboard

### Notion Database Views
1. **Recent Errors**: Last 24 hours, sorted by timestamp
2. **By Workflow**: Grouped by workflow name
3. **Critical Only**: High severity errors requiring immediate attention
4. **Unresolved**: Status = "Failed" for follow-up

### Error Analytics
- Track error frequency by workflow
- Identify recurring failure patterns
- Monitor system stability trends
- Alert on critical error spikes

---

## 📝 File Contents

- `error-logger.json` - Complete N8N error workflow definition
- `README.md` - This documentation
- `test-error-workflow.json` - Test workflow for validation

**Repository**: https://github.com/midnightmoodboard/n8n-error-logger
**N8N Workflow ID**: `NvNkRyZqqBY8PXqt`
**Error Database**: `23d4ea7cdefa80ccb756dc7794f7c972`
**Status**: ✅ Deployed and Ready

*Last Updated: August 26, 2025 by Claude Code*