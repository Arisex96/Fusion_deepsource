# SE Assignment #2: Code Review & Quality Analysis Steps

## Assignment Overview
Review the backend code (views.py) using **5 LLMs** and **3 Static Analysis Tools**, then report issues in a specific Excel format.

---

## STEP 1: PREPARE YOUR CODE FILES

### 1.1 Identify Your Module
- **Module Name**: Your Faculty/Staff Management Module
- **Code File**: `api/views.py` (721 lines)
- **Helper File**: `api/helpers.py` (may have connections)

### 1.2 Create Problem Statement Document
Create a file named `Problem Statement.txt` containing:
- Overview of your module's functionality
- Key features implemented
- Database models used
- API endpoints exposed

---

## STEP 2: USE 5 LLMs FOR CODE REVIEW

### 2.1 Prepare for LLM Analysis
Download the Prompt Template from the assignment folder: `Prompt Template.txt`

The prompt includes analysis categories:
1. **Error Handling** - Exception handling, error logging
2. **Security** - Auth, CSRF, SQL injection, sensitive data exposure
3. **Input Validation** - Missing/incomplete validation, model validation
4. **Performance** - Database queries, caching, bulk operations
5. **Maintainability** - Code duplication, dead code, magic numbers
6. **Correctness** - Data comparison, business logic, data consistency
7. **Code Style & Structure** - Organization, naming, formatting
8. **API Design** - HTTP methods, status codes, response format
9. **Testing** - Unit tests, test coverage
10. **Documentation** - Docstrings, inline comments
11. **Concurrency** - Race conditions, concurrency handling

### 2.2 Review Using 5 LLMs
Use these LLM platforms with the prompt:

**A. Perplexity Pro - Sonar**
- Login to Perplexity Pro
- Select "Sonar" model
- Paste prompt template
- Attach files: Problem Statement.txt + views.py
- Request CSV format output (semicolon separated)

**B. Perplexity Pro - Gemini 3 Pro**
- Select "Gemini 3 Pro" model
- Paste same prompt
- Attach same files
- Get CSV output

**C. Perplexity Pro - GPT 5.2**
- Select "GPT 5.2" model
- Paste same prompt
- Attach same files
- Get CSV output

**D. Perplexity Pro - Claude Sonnet 4.5**
- Select "Claude Sonnet 4.5" model
- Paste same prompt
- Attach same files
- Get CSV output

**E. DeepSeek (Not on Perplexity)**
- Visit: https://www.deepseek.com/
- Login/create account
- Use chat interface
- Paste prompt template
- Attach files
- Get CSV output

### 2.3 Expected Output Format from LLMs
CSV format with semicolon separators:
```
Issue Type;Issue Subtype;Issue Description;Code Snippet;API Name;Line No;Severity;Suggested Fix
Error Handling;Exception Handling;Missing error handling in password reset;try: user = AuthUser.objects.get(...);reset_password;172;High;Add specific exception handling
Security;Hardcoded Secrets;Plain text password displayed in response;return Response({"password": new_password};reset_password;185;Critical;Hash password before returning
```

---

## STEP 3: USE 3 STATIC ANALYSIS TOOLS

### 3.1 DeepSource Analysis

**Steps:**
1. Upload backend code to GitHub (if not already done)
2. Create `.deepsource.toml` file in repo root:
   ```toml
   version = 1

   [[analyzers]]
   name = "python"
   enabled = true

   [analyzers.meta]
   runtime_version = "3.x.x"
   ```
3. Go to: https://deepsource.io/
4. Login and connect your GitHub repository
5. Wait for analysis to complete
6. Download report in CSV format
7. Extract relevant issues for views.py

### 3.2 Sonarqube Analysis

**Prerequisites:**
- Download SonarQube Community Edition
- Download Sonar Scanner

**Steps:**
1. Start SonarQube server:
   ```
   cd "D:\SonarQube\sonarqube-25.6.0.109173\bin\windows-x86-64"
   .\StartSonar.bat
   ```
   - Wait for server to start
   - Login: admin / admin

2. Navigate to scanner directory:
   ```
   cd "D:\SonarQube\sonar-scanner-7.1.0.4889-windows-x64\bin"
   ```

3. Run scanner on your backend folder:
   ```
   sonar-scanner.bat -Dsonar.projectKey=your-module-name -Dsonar.sources=. -Dsonar.host.url=http://localhost:9000 -Dsonar.login=your-token
   ```

4. Extract issues to JSON:
   ```
   curl -u admin:admin "http://localhost:9000/api/issues/search?componentKeys=your-module-name&ps=500" -o output.json
   ```

5. Convert JSON to CSV format

### 3.3 Prospector Analysis

**Steps:**
1. Create virtual environment:
   ```
   python -m venv venv
   .\venv\Scripts\activate
   ```

2. Install prospector:
   ```
   pip install prospector
   ```

3. Run prospector on views.py:
   ```
   prospector api/views.py --output-format json > views_prospector.json
   ```

4. Convert JSON output to CSV:
   ```python
   import json
   import csv
   
   with open('views_prospector.json') as f:
       data = json.load(f)
   
   with open('prospector_output.csv', 'w', newline='') as csvfile:
       writer = csv.writer(csvfile, delimiter=';')
       writer.writerow(['Issue Type', 'Issue Code', 'Message', 'Line', 'Source'])
       
       for issue in data['messages']:
           writer.writerow([
               issue.get('type', ''),
               issue.get('code', ''),
               issue.get('message', ''),
               issue.get('linenumber', ''),
               issue.get('source', '')
           ])
   ```

---

## STEP 4: COMPILE RESULTS INTO EXCEL FILES

### 4.1 Create `<Module-Name>-Total.xlsx`

**Structure: 8 Sheets**

**Sheet 1: Sonar (LLM)**
- Headers: Issue Type | Issue Subtype | Description | Code Snippet | API Name | Line No | Severity | Suggested Fix | Review Comments
- Copy all issues from Sonar LLM output
- Add your review comments in last column

**Sheet 2: Gemini 3 Pro**
- Same structure as Sheet 1
- Copy all issues from Gemini output
- Add review comments

**Sheet 3: GPT 5.2**
- Same structure
- Copy all issues from GPT output
- Add review comments

**Sheet 4: Claude Sonnet 4.5**
- Same structure
- Copy all issues from Claude output
- Add review comments

**Sheet 5: DeepSeek**
- Same structure
- Copy all issues from DeepSeek output
- Add review comments

**Sheet 6: DeepSource**
- Headers: Issue Code | Message | Severity | File | Line | Suggested Fix | Review Comments
- Copy all issues from DeepSource report
- Add review comments

**Sheet 7: SonarQube**
- Headers: Issue Key | Rule | Message | Severity | Status | File | Line | Suggested Fix | Review Comments
- Copy all issues from SonarQube report
- Add review comments

**Sheet 8: Prospector**
- Headers: Issue Type | Code | Message | Line | Severity | Suggested Fix | Review Comments
- Copy all issues from Prospector report
- Add review comments

### 4.2 Create `<Module-Name>-Master.xlsx`

**Structure: Single Master Sheet**

**Column Headers:**
| Issue Type | Issue Subtype | Description | Code Snippet | Line No | Severity | Sonar | Gemini 3 Pro | GPT 5.2 | Claude 4.5 | DeepSeek | DeepSource | SonarQube | Prospector | Review Comments |

**How to fill:**
1. Create consolidated list of all unique issues found across all tools
2. For each issue, mark which tools detected it (yes/no or checkmark)
3. Use Line No as reference to match issues across tools
4. Severity: Use highest severity if multiple tools report it
5. Review Comments: Add your analysis and recommended actions

**Example Row:**
```
Error Handling | Exception Handling | Missing error handling in add_individual_student | try: user = auth_serializer.save() | 280 | High | ✓ | ✓ | ✓ | | | ✓ | ✓ | | Need to handle specific exceptions, not generic Exception
```

---

## STEP 5: KEY ISSUES TO LOOK FOR (Based on views.py Analysis)

### Common Issues Likely to be Found:

**Critical Security Issues:**
1. Line 185: Plaintext password in response (`return Response({"password": new_password}`)
2. Line 150-196: Generic exception handling exposing error details
3. Hardcoded department default (Line 290: `GlobalsDepartmentinfo.objects.get(name='CSE')`)

**High Priority Maintainability:**
1. Code duplication in `add_individual_student`, `add_individual_staff`, `add_individual_faculty` (300+ lines repeated)
2. Magic numbers and hardcoded values throughout
3. Missing input validation in multiple endpoints

**Performance Issues:**
1. Multiple separate queries instead of using select_related/prefetch_related
2. N+1 query problems in loops

**Input Validation:**
1. Missing email format validation
2. No phone number format validation
3. No date of birth validation

**Documentation:**
1. Missing docstrings for all views and functions
2. No API documentation

---

## STEP 6: SUBMISSION

### Deliverables:
1. **`<Your-Module-Name>-Total.xlsx`** - All 8 sheets with individual tool outputs + comments
2. **`<Your-Module-Name>-Master.xlsx`** - Aggregated master report with cross-tool mapping

### File Format:
- Submit as XLSX format
- Replace `<Your-Module-Name>` with your actual module name (e.g., "Faculty Management")

### Deadline:
- Check your assignment portal for due date

---

## STEP 7: SAMPLE ANALYSIS FORMAT

### Example Issue Entry:

**Tool: SonarQube (Static Analysis)**
```
Issue Type: Security
Issue Subtype: Sensitive Data Exposure
Issue Description: Password returned in plaintext in API response
Code Snippet: return Response({"password": new_password,"message": "Password reset successfully."}, status=status.HTTP_200_OK)
API Name: reset_password
Line No: 185
Severity: Critical
Suggested Fix: Remove password from response. Send password via secure email only.
Review Comments: Critical security risk. Passwords must never be exposed in API responses. Implement server-side password sending mechanism.
```

---

## NOTES:

1. **Authentication**: Some tools require authentication - plan ahead
2. **Time Required**: ~8-10 hours total (2-3 hours per tool setup + 2-3 hours analysis)
3. **File Access**: Keep all original files for reference
4. **Duplicates**: Same issue found by multiple tools should be consolidated in Master sheet
5. **Comments**: Add meaningful review comments - don't leave them blank

---

## QUICK CHECKLIST:

- [ ] Problem Statement created
- [ ] Views.py reviewed and understood
- [ ] 5 LLM analyses completed (Sonar, Gemini, GPT, Claude, DeepSeek)
- [ ] DeepSource setup and analysis completed
- [ ] SonarQube installed and analysis completed
- [ ] Prospector setup and analysis completed
- [ ] Total.xlsx created with all 8 sheets
- [ ] Master.xlsx created with consolidated issues
- [ ] Review comments added to all issues
- [ ] Files submitted before deadline
