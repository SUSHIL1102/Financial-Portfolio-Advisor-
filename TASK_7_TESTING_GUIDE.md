# Task 7: Testing Guide - EWM MCP Server via Bob Client

## Overview
This guide provides a systematic approach to test all 8 read-only tools through Bob's MCP interface and document their behavior.

## Prerequisites

Before testing, ensure:
1. ✅ Tasks 2-4 are completed (server renamed, tools renamed, write tools disabled)
2. ✅ EWM server is accessible at the URL in your `.env` file
3. ✅ Valid credentials are configured in `.env`
4. ✅ MCP server is running and connected to Bob

## Testing Approach

### Test Each Tool Using This Format:

For each tool, document:
1. **Tool Name**: The renamed tool name
2. **Test Input**: Parameters you provided
3. **Expected Behavior**: What should happen
4. **Actual Result**: What actually happened
5. **Status**: ✅ Pass / ❌ Fail / ⚠️ Partial
6. **Notes**: Any observations, errors, or issues

## 8 Read-Only Tools to Test

### 1. get_workitem

**Purpose**: Retrieve a single work item by ID

**Test Cases**:

#### Test 1.1: Valid Work Item ID
```
Tool: get_workitem
Input: 
  - workitem_id: "123" (use a valid ID from your EWM)

Expected: Returns work item details including:
  - ID
  - Name/Title
  - URL
  - Basic metadata

Actual Result:
[Document what Bob returns]

Status: [ ] Pass [ ] Fail [ ] Partial

Notes:
- 
```

#### Test 1.2: Invalid Work Item ID
```
Tool: get_workitem
Input:
  - workitem_id: "999999" (non-existent ID)

Expected: Error message indicating work item not found

Actual Result:
[Document the error message]

Status: [ ] Pass [ ] Fail [ ] Partial

Notes:
-
```

---

### 2. search_workitem

**Purpose**: Search for work items using OSLC query syntax

**Test Cases**:

#### Test 2.1: Search All Work Items
```
Tool: search_workitem
Input:
  - project_area: "JKE Banking" (or your project area)
  - filters: null (empty to get all)

Expected: Returns list of work items (max 100)

Actual Result:
[Document number of items returned and sample data]

Status: [ ] Pass [ ] Fail [ ] Partial

Notes:
-
```

#### Test 2.2: Search with Filter
```
Tool: search_workitem
Input:
  - project_area: "JKE Banking"
  - filters: 'dcterms:title="Bug"'

Expected: Returns work items with "Bug" in title

Actual Result:
[Document results]

Status: [ ] Pass [ ] Fail [ ] Partial

Notes:
-
```

#### Test 2.3: Search with Attributes
```
Tool: search_workitem
Input:
  - project_area: "JKE Banking"
  - filters: null
  - attributes: "dcterms:title,rtc_cm:state"

Expected: Returns work items with only title and state fields

Actual Result:
[Document results]

Status: [ ] Pass [ ] Fail [ ] Partial

Notes:
-
```

---

### 3. get_workitem_by_oid

**Purpose**: Retrieve work item by Object ID (UUID)

**Test Cases**:

#### Test 3.1: Valid OID
```
Tool: get_workitem_by_oid
Input:
  - oid: "_54A40A49EfGDsqN6FGdFpA" (use valid OID from your EWM)

Expected: Returns work item details

Actual Result:
[Document what Bob returns]

Status: [ ] Pass [ ] Fail [ ] Partial

Notes:
-
```

---

### 4. get_workitem_comments

**Purpose**: Get all comments for a work item

**Test Cases**:

#### Test 4.1: Work Item with Comments
```
Tool: get_workitem_comments
Input:
  - workitem_id: "123" (use ID with comments)

Expected: Returns list of comments with:
  - Comment text
  - Author
  - Timestamp

Actual Result:
[Document comments returned]

Status: [ ] Pass [ ] Fail [ ] Partial

Notes:
-
```

#### Test 4.2: Work Item without Comments
```
Tool: get_workitem_comments
Input:
  - workitem_id: "456" (use ID without comments)

Expected: Returns empty list or message indicating no comments

Actual Result:
[Document result]

Status: [ ] Pass [ ] Fail [ ] Partial

Notes:
-
```

---

### 5. list_ccm_project_areas

**Purpose**: List all available project areas

**Test Cases**:

#### Test 5.1: List All Project Areas
```
Tool: list_ccm_project_areas
Input: (no parameters)

Expected: Returns list of project areas with:
  - Project area names
  - Aliases
  - URIs

Actual Result:
[Document project areas returned]

Status: [ ] Pass [ ] Fail [ ] Partial

Notes:
-
```

---

### 6. get_workitem_type

**Purpose**: Get all work item types in a project area

**Test Cases**:

#### Test 6.1: Valid Project Area
```
Tool: get_workitem_type
Input:
  - project_area: "JKE Banking" (or your project area)

Expected: Returns list of work item types like:
  - defect
  - task
  - story
  - epic

Actual Result:
[Document types returned]

Status: [ ] Pass [ ] Fail [ ] Partial

Notes:
-
```

---

### 7. get_workitem_workflows

**Purpose**: Get workflow information for a project area

**Test Cases**:

#### Test 7.1: Valid Project Area Item ID
```
Tool: get_workitem_workflows
Input:
  - project_area_item_id: "_eEFRIfXOEfCTzqugoLaRmw" (use valid ID)
  - request_icon_uri: true

Expected: Returns workflows with:
  - States
  - Actions
  - Transitions
  - Icon URIs

Actual Result:
[Document workflow data returned]

Status: [ ] Pass [ ] Fail [ ] Partial

Notes:
-
```

---

### 8. get_workitem_shape

**Purpose**: Get OSLC Resource Shape for a work item

**Test Cases**:

#### Test 8.1: Valid Shape URL
```
Tool: get_workitem_shape
Input:
  - shape_url: "https://server/ccm/oslc/shapes/workitems/_54A40A49EfGDsqN6FGdFpA"

Expected: Returns shape definition with:
  - Properties
  - Constraints
  - Valid values
  - Workflow states

Actual Result:
[Document shape data returned]

Status: [ ] Pass [ ] Fail [ ] Partial

Notes:
-
```

---

## Testing Workflow

### Step 1: Prepare Test Data
Before testing, gather:
- [ ] Valid work item IDs from your EWM server
- [ ] Valid project area names/aliases
- [ ] Valid OIDs (Object IDs)
- [ ] Valid project area item IDs
- [ ] Valid shape URLs

**How to get test data**:
1. Use `list_ccm_project_areas` first to get project areas
2. Use `search_workitem` to get work item IDs
3. Use `get_workitem` to get OIDs and shape URLs from responses

### Step 2: Test in Bob

For each tool:
1. Open Bob chat interface
2. Ask Bob to use the specific MCP tool
3. Provide the test parameters
4. Observe and document the response

**Example Bob prompts**:
```
"Use the get_workitem tool to retrieve work item 123"

"Use the search_workitem tool to search for all work items in JKE Banking project"

"Use the list_ccm_project_areas tool to show all project areas"
```

### Step 3: Document Results

Create a test results document with this structure:

```markdown
# EWM MCP Server Test Results

**Test Date**: [Date]
**Tester**: [Your Name]
**Server**: [EWM Server URL]
**MCP Server Version**: ewm-workitems (after renaming)

## Summary
- Total Tools Tested: 8
- Passed: X
- Failed: Y
- Partial: Z

## Detailed Results

### 1. get_workitem
[Copy your test results here]

### 2. search_workitem
[Copy your test results here]

... (continue for all 8 tools)

## Issues Found
1. [List any issues]
2. [List any issues]

## Recommendations
1. [Any improvements needed]
2. [Any improvements needed]
```

### Step 4: Verify Write Tools Are Disabled

Also verify that the 4 write tools are NOT available:
- [ ] `create_workitem` - Should NOT appear in Bob's tool list
- [ ] `update_workitem` - Should NOT appear in Bob's tool list
- [ ] `add_comment_to_workitem` - Should NOT appear in Bob's tool list
- [ ] `change_workitem_state` - Should NOT appear in Bob's tool list

**How to verify**:
1. Ask Bob: "What MCP tools are available from the ewm-workitems server?"
2. Confirm only the 8 read-only tools are listed
3. Try to use a write tool (should fail with "tool not found")

---

## Common Issues and Troubleshooting

### Issue 1: Authentication Errors
**Symptom**: 401 Unauthorized errors
**Solution**: 
- Check `.env` file has correct credentials
- Verify EWM_USERNAME and EWM_PASSWORD are set
- Test credentials with `test_auth.py`

### Issue 2: Connection Errors
**Symptom**: Cannot connect to EWM server
**Solution**:
- Verify EWM_BASE_URL in `.env`
- Check network connectivity
- Verify EWM server is running

### Issue 3: Tool Not Found
**Symptom**: Bob says tool doesn't exist
**Solution**:
- Verify MCP server is running
- Check Bob's MCP settings point to correct server
- Restart MCP server and Bob

### Issue 4: Invalid Parameters
**Symptom**: Tool returns parameter validation errors
**Solution**:
- Check parameter names match tool schema
- Verify parameter types (string, boolean, etc.)
- Review tool documentation in code

---

## Success Criteria

Task 7 is complete when:
- ✅ All 8 read-only tools tested successfully
- ✅ Each tool returns expected data format
- ✅ Error handling works correctly (invalid inputs)
- ✅ Write tools are confirmed disabled
- ✅ Test results documented
- ✅ Any issues identified and documented

---

## Sample Test Results Template

Save your results in a file called `TEST_RESULTS_TASK7.md`:

```markdown
# EWM MCP Server - Task 7 Test Results

**Date**: 2026-04-24
**Tester**: [Your Name]
**Environment**: Development
**EWM Server**: https://your-ewm-server.com/ccm

## Test Summary
| Tool | Status | Notes |
|------|--------|-------|
| get_workitem | ✅ Pass | Works correctly |
| search_workitem | ✅ Pass | Returns max 100 items |
| get_workitem_by_oid | ✅ Pass | Works with valid OID |
| get_workitem_comments | ✅ Pass | Returns comments list |
| list_ccm_project_areas | ✅ Pass | Lists all areas |
| get_workitem_type | ✅ Pass | Returns types |
| get_workitem_workflows | ✅ Pass | Returns workflow data |
| get_workitem_shape | ✅ Pass | Returns shape definition |

## Detailed Test Results

### 1. get_workitem
**Test 1.1: Valid Work Item**
- Input: workitem_id="123"
- Result: ✅ Success
- Response: [paste actual response]
- Notes: Returned all expected fields

**Test 1.2: Invalid Work Item**
- Input: workitem_id="999999"
- Result: ✅ Success (error handled correctly)
- Response: [paste error message]
- Notes: Proper error handling

[Continue for all tools...]

## Write Tools Verification
- ✅ create_workitem: Not available (as expected)
- ✅ update_workitem: Not available (as expected)
- ✅ add_comment_to_workitem: Not available (as expected)
- ✅ change_workitem_state: Not available (as expected)

## Issues Found
None / [List any issues]

## Recommendations
[Any suggestions for improvements]
```

---

## Next Steps After Testing

1. **If all tests pass**:
   - Mark Task 7 as complete
   - Proceed to Task 8 (Update Box documentation)

2. **If tests fail**:
   - Document the failures
   - Debug the issues
   - Fix the code
   - Re-test

3. **Share results**:
   - Share test results with your mentor
   - Get feedback on any issues
   - Discuss any improvements needed