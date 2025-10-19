# Google Calendar MCP Setup Guide

**Status**: Installed, Pending OAuth Configuration
**Server**: guinacio/mcp-google-calendar
**Purpose**: Integrate Google Calendar with Claude Code for schedule analysis and management

---

## Installation Complete ✅

**Installed on**: 2025-10-07
**Location**: `/home/kxdev/dev/mcp-servers/google-calendar-mcp`
**Virtual Environment**: `.venv/` (Python 3.12)

### Dependencies Installed:
- google-api-python-client >= 2.170.0
- google-auth >= 2.40.0
- google-auth-oauthlib >= 1.2.0
- mcp >= 1.16.0
- pydantic >= 2.12.0
- All dependencies successfully installed

---

## Next Steps: OAuth Configuration

### Step 1: Google Cloud Console Setup

1. **Go to Google Cloud Console**: https://console.cloud.google.com
2. **Create or Select Project**:
   - Create new project: "Tyler Personal Productivity"
   - Or use existing project

3. **Enable Google Calendar API**:
   - Navigate to "APIs & Services" > "Library"
   - Search for "Google Calendar API"
   - Click "Enable"

4. **Configure OAuth Consent Screen**:
   - Go to "APIs & Services" > "OAuth consent screen"
   - User Type: External (for personal use)
   - App name: "1000x Dashboard"
   - User support email: Tyler's email
   - Add scopes: `https://www.googleapis.com/auth/calendar`

5. **Create OAuth 2.0 Credentials**:
   - Go to "APIs & Services" > "Credentials"
   - Click "Create Credentials" > "OAuth client ID"
   - Application type: **Desktop Application**
   - Name: "1000x Dashboard CLI"
   - Click "Create"
   - Download `credentials.json`

### Step 2: Place Credentials File

Move the downloaded `credentials.json` to:
```bash
/home/kxdev/dev/mcp-servers/google-calendar-mcp/mcp_server_google_calendar/credentials.json
```

**Command**:
```bash
# After downloading credentials.json from Google Cloud Console
mv ~/Downloads/credentials.json /home/kxdev/dev/mcp-servers/google-calendar-mcp/mcp_server_google_calendar/
```

### Step 3: Initial Authentication

Run the server to trigger OAuth flow:
```bash
cd /home/kxdev/dev/mcp-servers/google-calendar-mcp
.venv/bin/python -m mcp_server_google_calendar
```

This will:
1. Open your browser
2. Ask you to sign in to Google
3. Request calendar permissions
4. Save `token.json` for future use

---

## Claude Code Configuration

### For Terminal Mode (Full MCP Access)

Claude Code terminal has full MCP support. Configure in Claude Code settings UI:

**MCP Server Settings**:
- **Name**: `google-calendar`
- **Command**: `/home/kxdev/dev/mcp-servers/google-calendar-mcp/.venv/bin/python`
- **Args**: `-m mcp_server_google_calendar`

### For Cursor IDE Mode (Limited Beta)

**Status**: MCP configuration not fully supported in IDE beta yet
**Workaround**: Use terminal mode for Google Calendar integration

---

## Available Tools

Once configured, the following tools will be available:

### Calendar Management
- `list-calendars` - View all available calendars
- `get-timezone-info` - Get user's timezone
- `get-current-date` - Get current date/time in user's timezone

### Event Operations
- `get-events` - List events from calendar (with filters)
- `create-event` - Schedule new events
- `update-event` - Modify existing events
- `delete-event` - Remove events

### Availability
- `check-availability` - Check free/busy status

---

## Integration with 1000xdashboard

### Subagent Updates

Once Google Calendar MCP is configured, update the **schedule-optimizer** subagent to use calendar tools:

**File**: `/home/kxdev/dev/1000xdashboard/.claude/agents/schedule-optimizer.md`

**Update tools line**:
```yaml
tools: Read, Grep, Glob, list-calendars, get-events, create-event, check-availability
```

### Example Usage

**Tyler says:**
```
"schedule-optimizer, what are my priorities this week?"
```

**schedule-optimizer will:**
1. Read GMU course schedules from archive/gmu-schedule/
2. Use `get-current-date` to determine today
3. Use `get-events` to check existing calendar events
4. Cross-reference academic deadlines with calendar
5. Provide priority-ranked analysis

**Tyler says:**
```
"Add my CS-471 midterm to my calendar"
```

**schedule-optimizer will:**
1. Read cs-471-schedule.md for midterm date
2. Use `create-event` to add to Google Calendar
3. Set reminder notifications
4. Confirm event created

---

## Features

### Automatic Capabilities
- **Timezone detection**: Uses Google Calendar settings
- **Conflict checking**: Warns about overlapping events
- **Recurring events**: Full iCalendar RRULE support
- **Attendee management**: Email validation and invitations

### Advanced Features
- Multi-calendar support
- Conference link integration
- Custom reminders
- Color coding
- Visibility settings (public/private)

---

## Authentication Files

### credentials.json (You Provide)
- **Location**: `mcp_server_google_calendar/credentials.json`
- **Source**: Download from Google Cloud Console
- **Contains**: OAuth client ID and secret
- **Security**: ⚠️ Keep private, do not commit to git

### token.json (Auto-generated)
- **Location**: `google-calendar-mcp/token.json`
- **Created**: After first OAuth authentication
- **Contains**: Access and refresh tokens
- **Security**: ⚠️ Keep private, do not commit to git
- **Refresh**: Auto-refreshes when expired

**Git Ignore**:
```gitignore
# Already in .gitignore
credentials.json
token.json
*.json
```

---

## Troubleshooting

### "credentials.json not found"
**Solution**: Download from Google Cloud Console and place in correct location:
```bash
/home/kxdev/dev/mcp-servers/google-calendar-mcp/mcp_server_google_calendar/credentials.json
```

### "Calendar API not enabled"
**Solution**: Enable in Google Cloud Console > APIs & Services > Library

### "OAuth consent screen not configured"
**Solution**: Configure in Google Cloud Console > APIs & Services > OAuth consent screen

### "Token expired or invalid"
**Solution**: Delete `token.json` and re-authenticate:
```bash
rm /home/kxdev/dev/mcp-servers/google-calendar-mcp/token.json
.venv/bin/python -m mcp_server_google_calendar
```

### "Permission denied"
**Solution**: Check calendar permissions in Google Calendar settings

---

## Testing

### Test in Terminal Mode

1. **Start Claude Code in terminal**:
   ```bash
   cd /home/kxdev/dev/1000xdashboard
   claude
   ```

2. **Check available tools**:
   ```
   > List all available MCP tools
   ```

3. **Test calendar listing**:
   ```
   > Use the list-calendars tool to show my calendars
   ```

4. **Test event retrieval**:
   ```
   > Get my events for this week
   ```

---

## Security Best Practices

1. **Never commit credentials**:
   - credentials.json is in `.gitignore`
   - token.json is in `.gitignore`
   - Verify before each commit

2. **Scope limitation**:
   - Only request calendar scope (not full Google account)
   - Review permissions during OAuth flow

3. **Token storage**:
   - Tokens stored locally in project directory
   - Auto-refresh mechanism prevents frequent re-auth
   - Delete token to revoke access

4. **OAuth consent**:
   - Use "External" type for personal use
   - No need for verification for personal projects
   - Can limit to specific Google account

---

## Integration Timeline

### Phase 1: Setup (Current)
- ✅ MCP server installed
- ⏳ OAuth credentials needed
- ⏳ Initial authentication pending

### Phase 2: Basic Integration
- ⏳ Configure in Claude Code terminal
- ⏳ Test basic calendar operations
- ⏳ Update schedule-optimizer subagent

### Phase 3: Advanced Features
- ⏳ Automatic deadline sync from GMU schedules
- ⏳ Study block scheduling
- ⏳ Exam reminder creation
- ⏳ Conflict detection with Kratos work

### Phase 4: Full Automation
- ⏳ Proactive calendar management
- ⏳ Weekly schedule optimization
- ⏳ Integration with job search tracking
- ⏳ Multi-calendar orchestration

---

## Example Workflows

### Workflow 1: Sync Academic Deadlines

**Tyler**: "Sync all my CS-471 deadlines to Google Calendar"

**schedule-optimizer**:
1. Reads `/home/kxdev/dev/1000xdashboard/archive/gmu-schedule/cs-471-schedule.md`
2. Extracts all assignment due dates
3. Uses `create-event` for each deadline
4. Sets 24-hour and 1-week reminders
5. Color-codes as "School" (blue)
6. Confirms all events created

### Workflow 2: Check Availability

**Tyler**: "Am I free tomorrow afternoon for Kratos work?"

**schedule-optimizer**:
1. Uses `get-current-date` to determine "tomorrow"
2. Uses `check-availability` for afternoon time slot
3. Cross-references with GMU schedule
4. Checks upcoming deadlines
5. Provides recommendation

### Workflow 3: Optimize Study Time

**Tyler**: "Block study time for CS-471 midterm"

**schedule-optimizer**:
1. Reads cs-471-schedule.md for midterm date (Oct 9)
2. Calculates optimal study schedule (week before)
3. Uses `check-availability` to find free blocks
4. Creates multiple 2-hour study sessions
5. Sets prep reminders
6. Avoids conflicts with Kratos work

---

## File Structure

```
google-calendar-mcp/
├── .venv/                      # Virtual environment (Python 3.12)
├── mcp_server_google_calendar/
│   ├── __init__.py
│   ├── __main__.py            # STDIO entry point
│   ├── credentials.json        # YOU PROVIDE - OAuth credentials
│   ├── server.py              # MCP server implementation
│   ├── schemas.py             # Pydantic schemas
│   ├── auth/                  # Authentication module
│   ├── tools/                 # Tool definitions
│   └── utils/                 # Utility functions
├── token.json                 # AUTO-GENERATED - Auth token
├── pyproject.toml             # Project config
├── README.md                  # Official documentation
├── SETUP.md                   # This file
└── .gitignore                 # Excludes credentials and tokens
```

---

## Support Resources

- **Official Docs**: README.md in this folder
- **GitHub**: https://github.com/guinacio/mcp-google-calendar
- **Google Calendar API**: https://developers.google.com/calendar/api
- **MCP Documentation**: https://modelcontextprotocol.io

---

## Status Summary

**Installation**: ✅ Complete
**OAuth Setup**: ✅ Complete (credentials.json configured on 2025-10-07)
**Authentication**: ✅ Complete (OAuth flow successful, token.json created)
**Claude Code Config**: ⏳ Pending (needs terminal MCP settings configuration)
**Testing**: ⏳ Pending (after Claude Code MCP configuration)
**Integration**: ⏳ Pending (schedule-optimizer update)

**Authentication Details**:
- ✅ OAuth 2.0 consent screen configured (External, Testing mode)
- ✅ Google Calendar API enabled
- ✅ Test user added (Tyler's email)
- ✅ Desktop app credentials created
- ✅ credentials.json placed in mcp_server_google_calendar/
- ✅ OAuth flow completed in Chromium browser
- ✅ token.json saved at /home/kxdev/dev/mcp-servers/token.json

**Next Action**: Configure MCP server in Claude Code terminal settings:
- Name: `google-calendar`
- Command: `/home/kxdev/dev/mcp-servers/google-calendar-mcp/.venv/bin/python`
- Args: `-m mcp_server_google_calendar`

---

*Setup guide created by 1000xagent on 2025-10-07*
*OAuth authentication completed on 2025-10-07 13:11 UTC*
