# Complete Task Guide - EWM MCP Server Refactoring

## Table of Contents
1. [Task Overview](#task-overview)
2. [Task 1: Access Naming Convention](#task-1-access-naming-convention)
3. [Task 2: Rename MCP Server](#task-2-rename-mcp-server)
4. [Task 3: Expose Only Read-Only Tools](#task-3-expose-only-read-only-tools)
5. [Task 4: Rename Tools Based on Convention](#task-4-rename-tools-based-on-convention)
6. [Task 5: Clean Up Markdown Files](#task-5-clean-up-markdown-files)
7. [Task 6: Refactor Loggers](#task-6-refactor-loggers)
8. [Task 7: Test via Bob Client](#task-7-test-via-bob-client)
9. [Task 8: Update Box Documentation](#task-8-update-box-documentation)
10. [Task 9: Move Code to Parent Level](#task-9-move-code-to-parent-level)
11. [Task 10: Merge Server Files](#task-10-merge-server-files)

---

## Task Overview

This guide provides step-by-step instructions for refactoring the EWM MCP Server. Each task is explained in detail with:
- **What needs to be done** - Clear description
- **Why it matters** - Context and reasoning
- **Files to modify** - Specific file paths
- **Step-by-step instructions** - Detailed actions
- **Verification steps** - How to confirm success
- **Potential issues** - What could go wrong

**Important:** Follow the tasks in order, as some depend on previous tasks being completed.

---

## Task 1: Access Naming Convention

### What Needs to Be Done
Access the naming convention document at: https://ibm.box.com/s/dl7709528y87lzt8uactniclhwyujoq2

### Why It Matters
The naming convention defines:
- How the MCP server should be named
- How tools should be named
- Consistency across IBM's MCP servers
- Documentation standards

### Action Required
**Please provide the naming convention details from the Box document.**

I need you to share:
1. **Server naming format** (e.g., `ibm-ewm-workitem-server`, `ewm-mcp-server`, etc.)
2. **Tool naming format** (e.g., `get_work_item` vs `getWorkItem` vs `ewm_get_work_item`)
3. **Any prefixes or suffixes required**
4. **Capitalization rules**
5. **Any other naming standards**

### Questions to Clarify
- What should the server be named?
- Should tools have a prefix (e.g., `ewm_` or `workitem_`)?
- Are there examples of other IBM MCP servers following this convention?
- Is there a specific format for tool descriptions?

---

## Task 2: Rename MCP Server

### Current State
- **Current name:** `ewm-workitems-python`
- **Defined in 3 locations:**
  1. `~/.bob/settings/mcp_settings.json` - Bob's configuration
  2. `src/ewm_workitem_server/server.py` - FastMCP instance
  3. `pyproject.toml` - Python package name

### What Needs to Be Done
Rename the server to match the agreed-upon naming convention (from Task 1).

### Files to Modify

#### File 1: `~/.bob/settings/mcp_settings.json`
**Location:** `~/.bob/settings/mcp_settings.json`

**Current content:**
```json
{
  "mcpServers": {
    "ewm-workitems-python": {
      "command": "/path/to/venv/bin/python",
      "args": ["-m", "ewm_workitem_server.server"],
      "env": {
        "EWM_SERVER_URL": "https://...",
        "EWM_USERNAME": "...",
        "EWM_PASSWORD": "..."
      }
    }
  }
}
```

**What to change:**
- Change the key `"ewm-workitems-python"` to the new name
- Example: `"ibm-ewm-workitem-server"` (replace with actual agreed name)

**After change:**
```json
{
  "mcpServers": {
    "NEW-SERVER-NAME-HERE": {
      "command": "/path/to/venv/bin/python",
      "args": ["-m", "ewm_workitem_server.server"],
      "env": {
        "EWM_SERVER_URL": "https://...",
        "EWM_USERNAME": "...",
        "EWM_PASSWORD": "..."
      }
    }
  }
}
```

#### File 2: `src/ewm_workitem_server/server.py`
**Location:** `src/ewm_workitem_server/server.py`

**Current line (around line 30-40):**
```python
mcp = FastMCP("ewm-workitem-server")
```

**What to change:**
- Change the string `"ewm-workitem-server"` to the new name
- This is the name Bob sees when listing available servers

**After change:**
```python
mcp = FastMCP("NEW-SERVER-NAME-HERE")
```

#### File 3: `pyproject.toml`
**Location:** `pyproject.toml`

**Current content (line 2):**
```toml
[project]
name = "ewm-workitem-server"
```

**What to change:**
- Change `name = "ewm-workitem-server"` to the new name
- This affects the Python package name

**After change:**
```toml
[project]
name = "NEW-SERVER-NAME-HERE"
```

### Step-by-Step Instructions

1. **Decide on the new name** (from Task 1 naming convention)

2. **Update mcp_settings.json:**
   ```bash
   # Open the file
   nano ~/.bob/settings/mcp_settings.json
   
   # Change the server key name
   # Save: Ctrl+O, Enter, Ctrl+X
   ```

3. **Update server.py:**
   ```bash
   # Open the file
   nano src/ewm_workitem_server/server.py
   
   # Find: mcp = FastMCP("ewm-workitem-server")
   # Change to: mcp = FastMCP("NEW-NAME")
   # Save: Ctrl+O, Enter, Ctrl+X
   ```

4. **Update pyproject.toml:**
   ```bash
   # Open the file
   nano pyproject.toml
   
   # Find: name = "ewm-workitem-server"
   # Change to: name = "NEW-NAME"
   # Save: Ctrl+O, Enter, Ctrl+X
   ```

5. **Reinstall the package** (to update the package name):
   ```bash
   cd /path/to/ewm-workitem-server-python
   source venv/bin/activate
   pip install -e .
   ```

### Verification Steps

1. **Check server.py:**
   ```bash
   grep "FastMCP" src/ewm_workitem_server/server.py
   # Should show: mcp = FastMCP("NEW-NAME")
   ```

2. **Check pyproject.toml:**
   ```bash
   grep "^name" pyproject.toml
   # Should show: name = "NEW-NAME"
   ```

3. **Check mcp_settings.json:**
   ```bash
   cat ~/.bob/settings/mcp_settings.json | grep -A 10 "mcpServers"
   # Should show the new server name as a key
   ```

4. **Test with Bob:**
   - Restart Bob
   - Ask Bob: "List available MCP servers"
   - Should see the new server name

### Potential Issues

**Issue 1: Bob doesn't recognize the server**
- **Cause:** mcp_settings.json not updated correctly
- **Fix:** Verify JSON syntax is valid (no trailing commas, proper quotes)

**Issue 2: Import errors after renaming**
- **Cause:** Package name changed but not reinstalled
- **Fix:** Run `pip install -e .` again

**Issue 3: Old name still appears**
- **Cause:** Bob cache not cleared
- **Fix:** Restart Bob completely (quit and reopen)

---

## Task 3: Expose Only Read-Only Tools

### Current State
**All 12 tools are currently available:**

**Read-Only Tools (8 - KEEP THESE):**
1. ✅ `get_work_item` - Retrieve work item by ID
2. ✅ `query_work_items` - Search work items
3. ✅ `get_work_item_comments` - Read comments
4. ✅ `get_project_areas` - List project areas
5. ✅ `get_work_item_types` - List work item types
6. ✅ `get_work_item_by_oid` - Get by Object ID
7. ✅ `get_workflows` - Get workflow info
8. ✅ `get_work_item_shape` - Get OSLC shape

**Write Tools (4 - DISABLE THESE):**
9. ❌ `create_work_item` - Creates new work items
10. ❌ `update_work_item` - Modifies work items
11. ❌ `add_work_item_comment` - Adds comments
12. ❌ `change_work_item_state` - Changes state

### Why It Matters
- **Security:** Prevents accidental modifications to EWM
- **Safety:** Read-only access reduces risk
- **Compliance:** May be required by your organization

### Implementation Options

#### Option 1: Disable in Bob Configuration (RECOMMENDED)
**Pros:**
- No code changes required
- Easy to enable/disable
- Can be different per Bob installation

**Cons:**
- Must update on each machine
- Not enforced in code

#### Option 2: Comment Out Tool Registration
**Pros:**
- Enforced in code
- Clear what's disabled
- Easy to re-enable

**Cons:**
- Requires code changes
- Must reinstall package

#### Option 3: Remove Tool Decorators
**Pros:**
- Tools completely removed
- Cleanest approach

**Cons:**
- Harder to re-enable
- More invasive changes

### Recommended Approach: Option 1

#### File to Modify: `~/.bob/settings/mcp_settings.json`

**Current content:**
```json
{
  "mcpServers": {
    "ewm-workitems-python": {
      "command": "/path/to/venv/bin/python",
      "args": ["-m", "ewm_workitem_server.server"],
      "env": {
        "EWM_SERVER_URL": "https://...",
        "EWM_USERNAME": "...",
        "EWM_PASSWORD": "..."
      }
    }
  }
}
```

**After adding disabledTools:**
```json
{
  "mcpServers": {
    "ewm-workitems-python": {
      "command": "/path/to/venv/bin/python",
      "args": ["-m", "ewm_workitem_server.server"],
      "env": {
        "EWM_SERVER_URL": "https://...",
        "EWM_USERNAME": "...",
        "EWM_PASSWORD": "..."
      },
      "disabledTools": [
        "create_work_item",
        "update_work_item",
        "add_work_item_comment",
        "change_work_item_state"
      ]
    }
  }
}
```

### Step-by-Step Instructions

1. **Open Bob's MCP settings:**
   ```bash
   nano ~/.bob/settings/mcp_settings.json
   ```

2. **Add the `disabledTools` array** after the `env` section:
   - Add a comma after the closing `}` of `env`
   - Add the `disabledTools` array as shown above

3. **Verify JSON syntax:**
   - Use a JSON validator: https://jsonlint.com/
   - Or use: `python -m json.tool ~/.bob/settings/mcp_settings.json`

4. **Save the file:**
   - Ctrl+O, Enter, Ctrl+X (in nano)

5. **Restart Bob** to apply changes

### Verification Steps

1. **Check the configuration:**
   ```bash
   cat ~/.bob/settings/mcp_settings.json | grep -A 5 "disabledTools"
   ```

2. **Test with Bob:**
   ```
   You: "What tools are available in the EWM server?"
   Bob: Should list only 8 read-only tools
   ```

3. **Try a disabled tool:**
   ```
   You: "Create a new work item in EWM"
   Bob: Should say the tool is not available
   ```

### Potential Issues

**Issue 1: JSON syntax error**
- **Cause:** Missing comma or quote
- **Fix:** Validate JSON syntax

**Issue 2: Tools still available**
- **Cause:** Bob not restarted
- **Fix:** Completely quit and restart Bob

**Issue 3: Wrong tool names**
- **Cause:** Tool names don't match actual names
- **Fix:** Verify tool names match those in server.py

---

## Task 4: Rename Tools Based on Convention

### What Needs to Be Done
Rename all tools to match the naming convention from the Box document.

### Current Tool Names
1. `get_work_item`
2. `query_work_items`
3. `create_work_item`
4. `update_work_item`
5. `get_work_item_comments`
6. `add_work_item_comment`
7. `get_project_areas`
8. `get_work_item_types`
9. `get_work_item_by_oid`
10. `get_workflows`
11. `change_work_item_state`
12. `get_work_item_shape`

### Waiting for Naming Convention
**I need the naming convention from the Box document to proceed.**

Possible formats:
- **Option A:** Add prefix: `ewm_get_work_item`, `ewm_query_work_items`
- **Option B:** CamelCase: `getWorkItem`, `queryWorkItems`
- **Option C:** Namespace: `ewm.workitem.get`, `ewm.workitem.query`
- **Option D:** Keep as-is but document differently

### Files That Will Need Changes

Once we know the convention, we'll need to update:

1. **Tool definition files:**
   - `src/ewm_workitem_server/tools/workitem_tools.py`
   - `src/ewm_workitem_server/tools/comment_tools.py`
   - `src/ewm_workitem_server/tools/project_tools.py`
   - `src/ewm_workitem_server/tools/workflow_tools.py`

2. **Tool registration:**
   - `src/ewm_workitem_server/server.py`

3. **Documentation:**
   - `README.md`
   - `WorkitemOSLCAPIs.md`

4. **Bob configuration:**
   - `~/.bob/settings/mcp_settings.json` (disabledTools array)

### Example: If Convention is to Add `ewm_` Prefix

**Before (in workitem_tools.py):**
```python
@mcp.tool()
async def get_work_item(work_item_id: str) -> str:
    """Retrieve a work item by ID from EWM."""
    ...
```

**After:**
```python
@mcp.tool()
async def ewm_get_work_item(work_item_id: str) -> str:
    """Retrieve a work item by ID from EWM."""
    ...
```

### Step-by-Step Instructions (Once Convention is Known)

1. **Create a mapping** of old names to new names

2. **Update tool definitions** in each file:
   ```bash
   # For each tool file
   nano src/ewm_workitem_server/tools/workitem_tools.py
   # Change function names
   ```

3. **Update disabledTools** in mcp_settings.json:
   ```json
   "disabledTools": [
     "NEW_NAME_create_work_item",
     "NEW_NAME_update_work_item",
     "NEW_NAME_add_work_item_comment",
     "NEW_NAME_change_work_item_state"
   ]
   ```

4. **Update documentation:**
   ```bash
   # Update README.md with new tool names
   nano README.md
   ```

5. **Reinstall package:**
   ```bash
   pip install -e .
   ```

6. **Restart Bob**

7. **Test each renamed tool**

### Verification Steps (Once Renamed)

1. **Check tool definitions:**
   ```bash
   grep "@mcp.tool()" src/ewm_workitem_server/tools/*.py -A 1
   ```

2. **Test with Bob:**
   ```
   You: "List all available tools in the EWM server"
   Bob: Should show new tool names
   ```

3. **Test a renamed tool:**
   ```
   You: "Use [NEW_TOOL_NAME] to get work item 123"
   Bob: Should execute successfully
   ```

---

## Task 5: Clean Up Markdown Files

### Current State
**Too many markdown files (11 total):**
1. `README.md` ✅ KEEP
2. `WorkitemOSLCAPIs.md` ✅ KEEP
3. `FINAL_NOTES.md` ❌ DELETE
4. `IDE_SETUP.md` ❌ DELETE
5. `IDE_TROUBLESHOOTING.md` ❌ DELETE
6. `PROJECT_SUMMARY.md` ❌ DELETE
7. `QUICK_START.md` ❌ DELETE
8. `SETUP.md` ❌ DELETE
9. `TEST_RESULTS.md` ❌ DELETE
10. `MCP_EXPLAINED.md` ❓ EVALUATE
11. `TASK_GUIDE.md` (this file) ❓ EVALUATE

### Goal
- Keep only `README.md` and `WorkitemOSLCAPIs.md`
- Consolidate useful information into README.md
- Remove redundant documentation

### Why It Matters
- **Clarity:** Too many docs confuse users
- **Maintenance:** Fewer files to keep updated
- **Standards:** Most projects have one main README

### Files to DELETE (Redundant)

**1. FINAL_NOTES.md (194 lines)**
- **Content:** Project completion status
- **Why delete:** Historical notes, not needed for users
- **Action:** Delete entirely

**2. IDE_SETUP.md (215 lines)**
- **Content:** VSCode configuration
- **Why delete:** IDE-specific, not essential
- **Action:** Delete entirely

**3. IDE_TROUBLESHOOTING.md (254 lines)**
- **Content:** Detailed IDE troubleshooting
- **Why delete:** Too detailed, IDE-specific
- **Action:** Delete entirely

**4. PROJECT_SUMMARY.md (320 lines)**
- **Content:** Technical overview
- **Why delete:** Redundant with README
- **Action:** Merge key parts into README, then delete

**5. QUICK_START.md (134 lines)**
- **Content:** Quick start guide
- **Why delete:** Should be in README
- **Action:** Merge into README, then delete

**6. SETUP.md (289 lines)**
- **Content:** Python installation
- **Why delete:** Should be in README
- **Action:** Merge essential parts into README, then delete

**7. TEST_RESULTS.md (298 lines)**
- **Content:** Test results
- **Why delete:** Historical, not needed
- **Action:** Delete entirely

### Files to EVALUATE

**1. MCP_EXPLAINED.md (682 lines)**
- **Content:** Comprehensive MCP explanation
- **Options:**
  - Keep it (useful for MCP newcomers)
  - Move to `docs/` folder
  - Delete (assume users know MCP)
- **Recommendation:** Keep it OR move to `docs/MCP_EXPLAINED.md`

**2. TASK_GUIDE.md (this file)**
- **Content:** Refactoring task instructions
- **Options:**
  - Keep temporarily during refactoring
  - Delete after tasks complete
  - Move to `docs/` folder
- **Recommendation:** Delete after all tasks are complete

### Step-by-Step Instructions

#### Step 1: Backup Current Files
```bash
cd /path/to/ewm-workitem-server-python
mkdir backup_docs
cp *.md backup_docs/
```

#### Step 2: Enhance README.md

Create an enhanced README.md with these sections:

```markdown
# EWM Work Item MCP Server

## Overview
[Brief description of the server]

## Quick Start
1. Install Python 3.10+
2. Create virtual environment
3. Install dependencies
4. Configure credentials
5. Test the server

## Installation

### Prerequisites
- Python 3.10 or later
- Access to EWM server

### Setup Steps
```bash
# Create virtual environment
python3.11 -m venv venv
source venv/bin/activate

# Install package
pip install -e .

# Configure
cp .env.example .env
# Edit .env with your credentials
```

## Configuration

### Bob MCP Settings
[Configuration example]

### Environment Variables
[List of variables]

## Available Tools

### Read-Only Tools (8)
1. get_work_item - [Description]
2. query_work_items - [Description]
...

### Write Tools (Disabled)
[List of disabled tools]

## Usage Examples

### Example 1: Get Work Item
```
You: "Get details for work item 123"
Bob: [Response]
```

### Example 2: Query Work Items
```
You: "Query all open defects in JKE Banking"
Bob: [Response]
```

## Architecture

[High-level architecture diagram]

## Troubleshooting

### Common Issues
1. Authentication failed
2. Connection timeout
3. Tool not found

## API Reference
See [WorkitemOSLCAPIs.md](WorkitemOSLCAPIs.md) for detailed API documentation.

## License
MIT
```

#### Step 3: Delete Redundant Files
```bash
# Delete files
rm FINAL_NOTES.md
rm IDE_SETUP.md
rm IDE_TROUBLESHOOTING.md
rm PROJECT_SUMMARY.md
rm QUICK_START.md
rm SETUP.md
rm TEST_RESULTS.md

# Optional: Move MCP_EXPLAINED.md to docs/
mkdir -p docs
mv MCP_EXPLAINED.md docs/

# Delete TASK_GUIDE.md after all tasks complete
# rm TASK_GUIDE.md
```

#### Step 4: Update References

**Check for references to deleted files:**
```bash
grep -r "FINAL_NOTES\|IDE_SETUP\|PROJECT_SUMMARY\|QUICK_START\|SETUP\|TEST_RESULTS" *.md
```

**Update any links in README.md**

### Verification Steps

1. **Check remaining files:**
   ```bash
   ls *.md
   # Should show: README.md, WorkitemOSLCAPIs.md, (optionally MCP_EXPLAINED.md in docs/)
   ```

2. **Verify README.md is comprehensive:**
   ```bash
   wc -l README.md
   # Should be substantial (500+ lines)
   ```

3. **Check for broken links:**
   ```bash
   grep -E "\[.*\]\(.*\.md\)" README.md
   ```

4. **Test documentation:**
   - Read through README.md
   - Ensure all essential information is present

---

## Task 6: Refactor Loggers

### Current State
**Multiple logging approaches:**
- `src/ewm_workitem_server/core/logging.py` - Custom logging setup
- Various files use `logging.getLogger(__name__)`

### Goal
Refactor all logging to use `elmai.logger`

### What is elmai.logger?
**I need clarification on this:**
- Is `elmai` an IBM internal logging library?
- Where is it documented?
- How should it be imported?
- What's the API?

### Questions to Answer

1. **Import statement:**
   ```python
   # Is it one of these?
   from elmai import logger
   from elmai.logger import Logger
   import elmai.logger as logger
   ```

2. **Usage:**
   ```python
   # How do we use it?
   logger.info("Message")
   logger.error("Error message")
   logger.debug("Debug info")
   ```

3. **Configuration:**
   - Does it need initialization?
   - Are there configuration options?
   - Does it replace Python's standard logging?

4. **Dependencies:**
   - Do we need to add `elmai` to `pyproject.toml`?
   - Is it already available in IBM's environment?

### Files That Currently Use Logging

1. `src/ewm_workitem_server/core/logging.py`
2. `src/ewm_workitem_server/ewm/base.py`
3. `src/ewm_workitem_server/ewm/workitems.py`
4. `src/ewm_workitem_server/ewm/comments.py`
5. `src/ewm_workitem_server/ewm/projects.py`
6. `src/ewm_workitem_server/ewm/workflows.py`
7. `src/ewm_workitem_server/server.py`

### Example Refactoring (Once elmai.logger is Clarified)

**Before:**
```python
import logging
logger = logging.getLogger(__name__)

async def get_work_item(self, work_item_id: str):
    logger.info(f"Fetching work item {work_item_id}")
```

**After:**
```python
from elmai import logger

async def get_work_item(self, work_item_id: str):
    logger.info(f"Fetching work item {work_item_id}")
```

### Step-by-Step Instructions (Once Clarified)

1. **Add elmai dependency** (if needed):
   ```toml
   dependencies = [
       "fastmcp>=0.1.0",
       "httpx>=0.27.0",
       "python-dotenv>=1.0.0",
       "elmai>=X.X.X"
   ]
   ```

2. **Update each file:**
   - Replace `import logging` with `from elmai import logger`
   - Replace `logging.getLogger(__name__)` with `logger`

3. **Remove custom logging setup:**
   - Delete or refactor `src/ewm_workitem_server/core/logging.py`

4. **Test logging:**
   ```bash
   python -m ewm_workitem_server.server
   ```

### Verification Steps (Once Implemented)

1. **Check imports:**
   ```bash
   grep -r "import logging" src/
   # Should return no results
   ```

2. **Test logging output:**
   - Run the server
   - Verify logs appear correctly

---

## Task 7: Test via Bob Client

### Goal
Test all read-only operations using Bob.

### Read-Only Tools to Test (8 total)

1. ✅ `get_work_item`
2. ✅ `query_work_items`
3. ✅ `get_work_item_comments`
4. ✅ `get_project_areas`
5. ✅ `get_work_item_types`
6. ✅ `get_work_item_by_oid`
7. ✅ `get_workflows`
8. ✅ `get_work_item_shape`

### Test Plan

#### Test 1: Get Work Item
**Command:** "Get details for work item 123 from EWM"
**Expected:** Work item details returned
**Verify:** ✅ Tool executes, ✅ Data returned, ✅ All fields present

#### Test 2: Query Work Items
**Command:** "Query all open defects in project area 'JKE Banking'"
**Expected:** List of open defects
**Verify:** ✅ Query works, ✅ Filtering works, ✅ Multiple results

#### Test 3: Get Comments
**Command:** "Get all comments for work item 123"
**Expected:** List of comments
**Verify:** ✅ Comments retrieved, ✅ Authors present, ✅ Timestamps correct

#### Test 4: Get Project Areas
**Command:** "List all project areas in EWM"
**Expected:** List of project areas
**Verify:** ✅ All areas listed, ✅ Names present, ✅ URIs included

#### Test 5: Get Work Item Types
**Command:** "What work item types are available in 'JKE Banking'?"
**Expected:** List of types
**Verify:** ✅ All types listed, ✅ IDs present, ✅ Descriptions included

#### Test 6: Get Work Item by OID
**Command:** "Get work item with OID '_54A40A49EfGDsqN6FGdFpA'"
**Expected:** Work item details
**Verify:** ✅ Retrieved, ✅ Data matches, ✅ No errors

#### Test 7: Get Workflows
**Command:** "Get workflow information for 'JKE Banking'"
**Expected:** Workflow states and transitions
**Verify:** ✅ Workflows retrieved, ✅ States listed, ✅ Transitions shown

#### Test 8: Get Work Item Shape
**Command:** "Get the OSLC shape for work item 123"
**Expected:** Shape definition
**Verify:** ✅ Shape retrieved, ✅ Properties listed, ✅ Constraints shown

### Test Execution Checklist

```
[ ] Test 1: get_work_item - PASSED / FAILED
[ ] Test 2: query_work_items - PASSED / FAILED
[ ] Test 3: get_work_item_comments - PASSED / FAILED
[ ] Test 4: get_project_areas - PASSED / FAILED
[ ] Test 5: get_work_item_types - PASSED / FAILED
[ ] Test 6: get_work_item_by_oid - PASSED / FAILED
[ ] Test 7: get_workflows - PASSED / FAILED
[ ] Test 8: get_work_item_shape - PASSED / FAILED
```

### Verification Steps

1. **Before testing:** Restart Bob
2. **During testing:** Note any errors
3. **After testing:** Create test report

---

## Task 8: Update Box Documentation

### Goal
Add missing tools information to: https://ibm.box.com/s/dl7709528y87lzt8uactniclhwyujoq2

### What to Document

1. **Server Information**
2. **Available Tools** (8 read-only)
3. **Configuration**
4. **Installation Instructions**
5. **Troubleshooting**

**Note:** I cannot directly update Box. You'll need to copy the information I provide.

---

## Task 9: Move Code to Parent Level

### Current Structure
```
ccm-tap-ai-mcp/
└── ewm-workitem-server-python/
    ├── src/
    └── ...
```

### Target Structure
```
ccm-tap-ai-mcp/
├── src/
└── ...
```

### Prerequisites
- Complete Tasks 1-8 first
- All tests passed

### Step-by-Step Instructions

1. **Backup:**
   ```bash
   tar -czf backup.tar.gz ewm-workitem-server-python/
   ```

2. **Move files:**
   ```bash
   mv ewm-workitem-server-python/* .
   rmdir ewm-workitem-server-python
   ```

3. **Update paths in mcp_settings.json**

4. **Reinstall:**
   ```bash
   pip install -e .
   ```

5. **Test with Bob**

---

## Task 10: Merge Server Files

### Goal
Merge `src/server.py` and `src/ewm_workitem_server/server.py`

### Step-by-Step Instructions

1. **Locate both files:**
   ```bash
   find . -name "server.py"
   ```

2. **Compare:**
   ```bash
   diff src/server.py src/ewm_workitem_server/server.py
   ```

3. **Merge or delete:**
   - If identical: delete one
   - If different: merge unique parts

4. **Test:**
   ```bash
   python -m ewm_workitem_server.server
   ```

---

## Summary

This guide provides detailed instructions for all 10 tasks. Follow them in order, and ask for clarification on:
- Task 1: Naming convention details
- Task 4: Tool naming format
- Task 6: elmai.logger usage

Good luck with the refactoring!