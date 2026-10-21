
# Assignment 5 — AI-Assisted Azure DevOps Dual-Pipeline Failure Triage

Part of the DevOps Micro Internship (DMI) — Agentic AI Track

---

## Student Information

**Full Name:** Ginny Ibe

**GitHub Repository or Fork URL:** https://github.com/Ginny-Ibe/devops-micro-internship-pravinmishra

**Public LinkedIn Post URL:**  https://www.linkedin.com/feed/update/urn:li:activity:7518485274989969408/

---

## Purpose

In this assignment, I configured an AI-assisted, read-only failure-triage workflow for the EpicBook Infrastructure and Application Pipelines. The workflow uses Bash to gather Azure DevOps pipeline evidence and Claude Code to analyze the evidence and recommend a recovery action while keeping all changes under human control.

---

# Task 0 — Verify Tools, Authentication, and Pipeline Details

## Goal

Verify the required tools, Azure DevOps authentication, organization and project details, and numeric pipeline IDs.

No screenshot is required for this task.

---

# Task 1 — Capture the Healthy Baseline and Prepare the Supplied Files

## Goal

Confirm that both EpicBook pipelines are healthy and place the supplied assignment files in the correct repository locations.

## Evidence

### Screenshot 1 — Healthy Baseline for Both Pipelines

Terminal output showing the latest completed Infrastructure and Application Pipeline runs with successful results.

![ouput](./screenshots/wk10a5t1-ss1.png)

## Notes

### 1. What proves that both pipelines were healthy before the drill?

- I queried the latest run of each pipeline directly from Azure DevOps with az pipelines runs list --pipeline-ids <id> --top 1. The Infrastructure Pipeline (ID 6, Ginny-Ibe.Epicbook-Infra-Pipeline) returned run 15 (20261020.8) on the main branch with status completed and result succeeded. The Application Pipeline (ID 8, Ginny-Ibe.Epicbook-App-Pipeline) returned run 16 (20261020.1) on main, also completed and succeeded. Because these results came from the Azure DevOps API rather than from looking at the web dashboard, the check is repeatable and is based on the actual pipeline records.


### 2. Why is a healthy baseline necessary before introducing a controlled failure?

- A healthy baseline gives me a known-good starting point to compare against. If either pipeline were already failing before the drill, I would not be able to tell whether the triage tool had detected the failure I introduced or an older, unrelated problem. Starting with both pipelines green means any failure reported after the drill can only have come from my deliberate change. It also lets me prove the recovery properly, because after the fix the pipelines should return to the same healthy state recorded in the baseline.

---

# Task 2 — Configure and Review the Supplied CLAUDE.md

## Goal

Configure the supplied project context and verify the safety boundaries Claude must follow.

## Evidence

### Screenshot 2 — CLAUDE.md Context and Safety Rules

`CLAUDE.md` open in the editor with the Project Overview, Incident Workflow, Safety Rules, and Output Rules visible.

![ouput](./screenshots/wk10a5t2-ss2.png)
![ouput](./screenshots/wk10a5t2-ss2a.png)

## Notes

### 1. Why does Claude need project-specific operational context?

- Without context, Claude would only see a report and some logs, with no idea what the system looks like. CLAUDE.md tells it there are two pipelines, what each one does (Terraform for AWS infrastructure and Ansible for the application), their IDs, and which script gathers evidence. That lets it attribute a failure to the right pipeline and layer instead of guessing, and it loads automatically every session, so the rules don't have to be repeated.


### 2. Which rules keep the human responsible for the recovery action?

- The Incident Workflow puts a "Human Act" step between Analyze and Verify. The Safety Rules say "Recommend a fix, but do not apply it" and "Require human approval and action for every recovery change", and they forbid editing project files, running Terraform or Ansible, and triggering, retrying, cancelling or approving pipeline runs. Claude can only diagnose and recommend. The engineer makes and applies the change.

### 3. Which rules protect pipeline credentials and application secrets?

- Claude must never read, print, store, expose or change a token, password, private key, authorization header, Service Connection credential or database credential, and must never ask for a secret to be pasted into the session. It is also forbidden from changing Service Connections, Secure Files, Variable Groups, agent pools or pipeline permissions, and its output must not expose sensitive values. The triage script also redacts secret-looking lines before anything is written to the report.

---

# Task 3 — Configure and Validate the Supplied Pipeline Triage Script

## Goal

Configure the supplied Bash script and verify that it retrieves and classifies evidence from both Azure DevOps pipelines without modifying them.

## Evidence

### Screenshot 3 — Pipeline Triage Script Configuration

Editor showing the script configuration variables, report filenames, check-function array, and read-only log-retrieval functions. Ensure that no token is visible.

![ouput](./screenshots/wk10a5t3-ss3.png)
![ouput](./screenshots/wk10a5t3-ss3a.png)
![ouput](./screenshots/wk10a5t3-ss3b.png)

---

### Screenshot 4 — Script Validation

Terminal showing successful Bash syntax validation and executable file permission.

![ouput](./screenshots/wk10a5t3-ss4.png)

## Notes

### 1. Why are pipeline metadata and step console logs handled separately?

- The metadata (run ID, branch, status, result, completion time from az pipelines runs list) is small and tells the script whether a run passed, failed, is still running or was cancelled. The console logs are large and are only needed to explain why a run failed, so the script fetches them only when the latest run's result is failed. Keeping them separate avoids downloading logs for healthy runs and means the run's state comes from Azure DevOps' own record, not from text patterns. The categories found in the logs are treated as supporting evidence, not as the source of truth.

### 2. How does the script obtain the actual console logs?

- For a failed run, fetch_build_logs() gets a short-lived Microsoft Entra access token with az account get-access-token for the Azure DevOps resource. It then calls the Build Logs REST API with read-only GET requests: first /_apis/build/builds/<runId>/logs to list the log IDs, then each /logs/<logId> to download its text. The token is sent to curl on stdin rather than on the command line, so it never appears in process listings. The JSON and HTTP status are validated, and every line is sanitized before the logs are saved to reports/infra-last-run.log or reports/app-last-run.log.

### 3. How does the check-function array control the classification loop?

- The categories array holds one entry per failure category in the form "Category Name|pattern1|pattern2|…". run_category_checks() loops over every entry, splits the name from the patterns, and runs a case-insensitive grep -iE against the pipeline's log file. Each category that matches is recorded as a [FAIL] with its first matching line as sanitized evidence, so several categories can match one run. Adding or changing a category only requires editing the array, not the loop.

### 4. What prevents a failed but unmatched run from being reported as healthy?

-  The pipeline's result decides health, not the pattern matching. If Azure DevOps reports failed and none of the patterns match, the script records an "Unclassified Pipeline Failure" ([FAIL], root cause not confirmed). If the logs cannot be retrieved, it still records a failure. HEALTHY is only possible when there are zero errors, failures and warnings, and only a completed / succeeded run counts as a pass. Queued, running or cancelled runs are warnings, and API or authentication problems are errors (exit 3), never health.

### 5. Why are different exit codes useful to another automation tool?

- A calling tool can react without parsing the report text: 0 means healthy, 1 means a warning (unfinished, cancelled or partial run), 2 means a pipeline failure and 3 means the triage tool itself could not do its job (configuration, login, API or log-retrieval error). Separating 3 from 2 is especially important, because "I couldn't check" must never be mistaken for "the pipeline is fine" or for "the pipeline is broken". The /pipeline-triage skill relies on these codes too.

---

# Task 4 — Run and Understand the Healthy-State Report

## Goal

Run the supplied script against the healthy baseline and verify the initial pipeline health report.

## Evidence

### Screenshot 5 — Healthy Pipeline Report

Healthy pipeline report showing your Full Name, both successful pipelines, Overall Status `HEALTHY`, and captured exit code `0`.

![ouput](./screenshots/wk10a5t4-ss5.png)

## Notes

### 1. What evidence proves that both pipelines are healthy?

- The report generated by pipeline-triage.sh (reports/pipeline-health-report.txt) shows my name, the timestamp, and a separate section for each pipeline. The Infrastructure Pipeline (ID 6) latest run 15 has status completed and result succeeded, marked [PASS]. The Application Pipeline (ID 8) latest run 16 also has status completed and result succeeded, marked [PASS]. The summary shows PASS: 2, WARN: 0, FAIL: 0, ERROR: 0 and Overall Status: HEALTHY, and the exit code I captured with echo $? straight after running the script was 0. The script only gives this result when both latest runs are completed and succeeded and no warnings or errors occurred, so a queued, cancelled, or unreachable pipeline could not have produced it.

### 2. Why must the baseline exit code be verified before the incident drill?

- The exit code is what the /pipeline-triage skill and any other automation use to decide the pipeline state, so I needed to prove the script returns 0 when everything is healthy before relying on it to return 2 for a failure. Checking it also proves the tool itself works end to end: authentication, Azure DevOps access, and configuration are all correct. My first attempt actually returned exit code 3 (ERROR) because my access token was not set correctly in the terminal. If I had skipped this check, that tool error could have been confused with the drill's failure later. With a verified 0 baseline, a later exit code 2 can only mean the deliberate pipeline failure, and the recovery can be proven by getting 0 again.


---

# Task 5 — Configure and Test the Supplied /pipeline-triage Skill

## Goal

Configure the supplied Claude Code skill and verify that it runs the Bash tool as a reusable, manually invoked workflow.

## Evidence

### Screenshot 6 — Pipeline-Triage Skill Definition

`SKILL.md` showing the frontmatter, manual-invocation setting, narrowly scoped tools, safety rules, and required output structure.

![ouput](./screenshots/wk10a5t5-ss6.png)
![ouput](./screenshots/wk10a5t5-ss6a.png)

---

### Screenshot 7 — Healthy Skill Result

Healthy `/pipeline-triage` result showing that both pipelines are healthy and no fix is required.

![ouput](./screenshots/wk10a5t5-ss7.png)

## Notes

### 1. Why is `disable-model-invocation: true` appropriate for this skill?

- This setting means the skill only runs when I deliberately type /pipeline-triage. Claude cannot decide on its own to start it in the middle of another conversation. Pipeline triage queries Azure DevOps with my credentials and overwrites the report files in reports/, so it should only happen when an engineer chooses to start an investigation. Manual invocation also keeps the evidence trail clear: every report corresponds to a run I started on purpose, which matters when I save the incident report before the fix and the recovery report after it.


### 2. Why should the skill avoid broad Bash approval?

- Broad approval such as Bash or Bash(*) would pre-approve any shell command while the skill runs, including commands that could trigger, retry, or cancel pipelines (az pipelines run), push code, run Terraform or Ansible, delete files, or print secrets from environment variables. The supplied skill uses allowed-tools: Bash(bash pipeline-triage.sh), Read, Grep, so the only command it pre-approves is the reviewed, read-only triage script. Reading the report and logs is done with Read and Grep. Anything else would need my explicit approval, which keeps the skill to Gather and Analyze and leaves every change to a human.

### 3. What work is performed by Bash, and what work is performed by Claude?

- Bash (pipeline-triage.sh) does the deterministic evidence gathering. It validates the configuration, queries Azure DevOps for each pipeline's latest run, downloads the console logs of failed runs through the read-only Build Logs API, redacts sensitive lines, matches the logs against the failure-category patterns, writes the report and log files, and returns an exit code (0, 1, 2 or 3). Claude does the analysis. It reads CLAUDE.md for the rules, runs the approved script, reads the report and sanitized logs, interprets the exit code, explains the evidence, names the most likely failure category, and recommends one fix and one verification step for the engineer to perform. Claude does not query Azure DevOps directly or change anything.

### 4. Why are permission rules required in addition to written safety instructions?

- The safety rules in CLAUDE.md and SKILL.md are instructions that Claude is expected to follow, but they are guidance, not enforcement. A misunderstanding, an ambiguous request, or instructions hidden inside pipeline log text (prompt injection) could still lead to an unsafe action. Permission rules are enforced by Claude Code itself: a tool or command that isn't allowed is blocked or requires human approval, whatever the model decides. The SKILL.md also notes that its allowed-tools entry does not override broader permissions in Claude Code settings, so project permission rules should deny dangerous commands such as pipeline runs, pushes, Terraform, Ansible, and file deletion. Written rules explain intent, and permission rules guarantee the boundary.

---

# Task 6 — Introduce a Safe Failure in the Application Pipeline

## Goal

Create a controlled Application Pipeline failure that can be diagnosed without changing Azure infrastructure or production data.

## Evidence

### Screenshot 8 — Controlled Application Pipeline Failure

Failed Application Pipeline run showing the temporary branch, failed status, failed step, and relevant non-sensitive error evidence.

![ouput](./screenshots/wk10a5t6-ss8.png)
![ouput](./screenshots/wk10a5t6-ss8a.png)
![ouput](./screenshots/wk10a5t6-ss8b.png)
![ouput](./screenshots/wk10a5t6-ss8c.png)

## Notes

### 1. What exact failure did you introduce?

- On a temporary branch called drill/pipeline-failure, I added a dependency that does not exist, "not-a-real-package-xyz": "1.0.0", to the dependencies section of package.json. I committed it with a message that clearly marks it as a drill (do not merge) and ran the Application Pipeline (ID 8) against that branch as run 17. The entry was also missing a comma, so package.json became invalid JSON. The pipeline failed in the "Configure and Deploy" stage at the Ansible task "Install Node.js dependencies", where npm ci --omit=dev stopped with npm error code EJSONPARSE … Expected ',' or '}' after property value. Ansible reported fatal: [backend_server]: FAILED!, and the Verify stage then failed because the play recap was never produced.

### 2. Which category should detect it?

- The intended category was Dependency Installation Failure, because the problem is in the application's dependency file and the failing step is the npm install. However, the triage script's dependency pattern looks for npm ERR!, and newer npm versions print npm error instead. The line that does match is Ansible's FAILED! => output, so the script should classify it as an Ansible, Nginx, or Application Deployment Failure, with the npm error as supporting evidence. This shows that pattern-based categories are heuristics, and why Claude must explain the evidence rather than just repeat the category name.

### 3. Why is the failure safe and easily reversible?

- It is a single-line change to one file on a temporary branch. No infrastructure, Terraform code, credentials, Service Connections, Variable Groups, or AWS resources were touched, and the Infrastructure Pipeline was not run, so its latest run stayed at run 15, succeeded. Reversing it only requires restoring package.json to its original contents on the branch and running the pipeline again, which also overwrites the copy on the server with the correct file. The database tasks were all idempotent and made no changes (ok/skipped), so no data was affected.

### 4. How did you prevent the deliberate failure from reaching `main` or changing the deployed application?

- The change was made only on the drill/pipeline-failure branch and was never merged into main. The Application Pipeline's trigger only watches main, so I ran the drill branch manually, and the branch will be deleted after recovery. The deployed application kept running: Ansible stopped at the failed npm ci task, so the restart handler never ran, and the site at http://98.81.52.20 still returned HTTP 200 afterwards. However, the drill did not fully stop changes from reaching the server. The "Deploy EpicBook application code" task had already copied the modified package.json to the backend server before npm ci failed. A safer drill would fail in the Validate stage, before any code is synced (for example, an unreachable inventory host). 

---

# Task 7 — Diagnose and Save the Incident Evidence

## Goal

Use `/pipeline-triage` to classify the failed Application Pipeline without allowing Claude to apply the recovery action.

## Evidence

### Screenshot 9 — Failed-State Diagnosis and Incident Report

`/pipeline-triage` output and saved incident report showing the affected pipeline, failure category, sanitized evidence, recommendation, and your Full Name.

![ouput](./screenshots/wk10a5t7-ss9.png)
![ouput](./screenshots/wk10a5t7-ss9a.png)
![ouput](./screenshots/wk10a5t7-ss9b.png)

## Notes

### 1. Which failure category was identified?

- Ansible, Nginx, or Application Deployment Failure, in the Application Pipeline (ID 8), run 17. The failing step was npm ci inside the Ansible backend role's "Install Node.js dependencies" task, so Dependency Installation Failure is a reasonable second category. Category matching is a heuristic. The Infrastructure Pipeline (ID 6, run 15) succeeded.

### 2. What exact evidence supported the diagnosis?

- The sanitized log reports/app-last-run.log (line 2373) and the health report both contain:
TASK [backend : Install Node.js dependencies]
fatal: [backend_server]: FAILED! => {"cmd": ["npm", "ci", "--omit=dev"], "rc": 1, ...
npm error code EJSONPARSE
npm error path /opt/epicbook/package.json
npm error JSON.parse Expected ',' or '}' after property value in JSON at position 871 while parsing near "...lize\": \"^6.3.0\"\n    \"not-a-real-package-..."
The PLAY RECAP backs this up: backend_server : ... failed=1. Next comes ##[error]Bash exited with code '2'. The later play-recap artifact errors were caused by the first failure. The evidence shows that package.json on the drill/pipeline-failure branch is invalid JSON: a comma is missing, and a "not-a-real-package-..." dependency was injected.

### 3. Did Claude apply the fix or rerun the pipeline? Why is that important?

- No. Claude ran only the read-only triage script and read the report and logs. It didn't edit package.json or any other file. It didn't trigger, retry or cancel a pipeline run, and it didn't touch AWS or Azure resources. It recommended a fix and a verification step and left both for the engineer to carry out. (Claude also ran one extra read-only wc -l command to count log lines, which it disclosed.)

- This matters because a human with full context stays accountable for production changes. Pipeline logs are untrusted data, and an assistant acting on them automatically could make things worse or follow injected instructions. Keeping humans in charge also protects secrets and service connections, keeps an audit trail of who changed what, and stops a wrong diagnosis from being applied before someone checks it.

### 4. Which part represents Gather, and which part represents Analyze?

- Gather: running bash pipeline-triage.sh. It fetched the latest run status for both pipelines from Azure DevOps and downloaded and sanitized the logs into reports/app-last-run.log and reports/infra-last-run.log. It also classified the failures and wrote reports/pipeline-health-report.txt with exit code 2.
- Analyze: Claude then read the fresh report and the relevant log sections. It matched exit code 2 to the FAIL status and found the npm ci EJSONPARSE error as the main failure. It separated that from the later play-recap errors it caused, explained the likely root cause (malformed package.json), and recommended a fix and a verification step for the engineer.

---

# Task 8 — Apply the Human-Reviewed Fix and Verify Recovery

## Goal

Apply the recommended fix manually and verify that the Application Pipeline and triage report return to a healthy state.

## Evidence

### Screenshot 10 — Corrected Application Pipeline Run

Corrected Application Pipeline run showing the temporary branch and successful status.

![ouput](./screenshots/wk10a5t8-ss10.png)
![ouput](./screenshots/wk10a5t8-ss10a.png)

---

### Screenshot 11 — Recovery Triage Result

Recovery `/pipeline-triage` output showing Overall Status `HEALTHY`, exit code `0`, your Full Name, and both saved report filenames.

![ouput](./screenshots/wk10a5t8-ss11.png)
![ouput](./screenshots/wk10a5t8-ss11a.png)

## Notes

### 1. What exact fix did you apply?

- On the drill/pipeline-failure branch, I restored package.json to its original version from main with git checkout main -- package.json. That removed the drill dependency "not-a-real-package-xyz": "1.0.0" and the missing comma that had made the file invalid JSON. I committed it as 3dc56f1 ("fix: restore package.json (remove drill dependency) per pipeline-triage recommendation") and pushed the branch. I deliberately did not use git revert HEAD, because the drill commit also contained my assignment workspace files, and reverting the whole commit would have deleted them. The fix commit changes only package.json, and afterwards the file was identical to main. I then manually ran the Application Pipeline on the corrected branch from the Azure DevOps portal.

### 2. Did the fix match Claude’s recommendation? Explain briefly.

- Yes. The incident triage identified Application Pipeline run 17 as failed, quoting the evidence fatal: [backend_server]: FAILED! => {"cmd": ["npm", "ci", "--omit=dev"] …} with npm error code EJSONPARSE and npm error path /opt/epicbook/package.json. It recommended correcting package.json and removing the invalid dependency entry. That is the change I reviewed against the log evidence and applied myself. Claude only recommended the fix: it did not edit the file, commit, push, or run the pipeline.

### 3. What evidence proves that the pipeline recovered?

- Application Pipeline run 19 on refs/heads/drill/pipeline-failure completed with result succeeded at 2026-10-21T00:12:23Z, with every stage green. The recovery /pipeline-triage run then produced a fresh report (timestamp 2026-10-21T00:16:27Z) showing Full Name: Ginny Ibe, [PASS] for the Infrastructure Pipeline (ID 6, run 15) and [PASS] for the Application Pipeline (ID 8, run 19), and PASS: 2, WARN: 0, FAIL: 0, ERROR: 0, Overall Status: HEALTHY, Script Exit Code: 0. Comparing it with the saved incident report, the status changed from FAIL / exit code 2 to HEALTHY / exit code 0. Both reports/incident-failure-report.txt and reports/recovery-report.txt are saved in the repository.

### 4. Why is a second triage run required after the pipeline becomes green?

- A green run in the portal only shows that one run passed. The second triage run applies the same independent, repeatable check used for the baseline and the incident, across both pipelines, and confirms through the exit code that the overall state is back to HEALTHY. It confirms that the latest Application Pipeline run is the corrected run 19 rather than an older run, that the Infrastructure Pipeline was not affected by the drill, and that the tool returns 0 again. This is the "Verify" step of the Gather → Analyze → Human Act → Verify workflow. It also produces the recovery report, which proves the incident is closed. As Claude noted, it shows the health of the latest pipeline runs, not proof that the deployed application works, so I also checked the site separately.

### 5. What risk would be created if Claude could automatically edit, push, approve, and rerun the pipeline?

- An AI acting on its own diagnosis could make changes that no human has reviewed. If the diagnosis were wrong, for example because category matching is only heuristic (my npm failure was classified as an Ansible deployment failure), it could push an incorrect fix straight to the pipeline and deploy it to production servers. Approving its own runs would bypass the human approval gates that protect infrastructure changes, and automatic reruns could loop through failing deployments or hide the real problem. Pipeline logs are untrusted input, so text inside a log could manipulate Claude into running harmful commands, exposing secrets, or changing infrastructure. There would also be no clear accountability for who made each change. Keeping Claude read-only, with a human reviewing and applying every fix, prevents all of this while still getting fast, evidence-based diagnosis.

---

# LinkedIn Post — Mandatory

## LinkedIn Post URL

https://www.linkedin.com/feed/update/urn:li:activity:7518485274989969408/

## Evidence

### Screenshot 12 — Published LinkedIn Post

Published LinkedIn post showing its text and at least one image or link.

![ouput](./screenshots/wk10a5tlink-ss0.png)

---

# Required Repository Files

Confirm that the following files are available in your repository:

* [x] `CLAUDE.md`
* [x] `pipeline-triage.sh`
* [x] `.claude/skills/pipeline-triage/SKILL.md`
* [x] `reports/incident-failure-report.txt`
* [x] `reports/recovery-report.txt`

---

# Submission Instructions

* Complete all tasks in sequence.
* Include all 12 required screenshots.
* Answer every Notes question in your own words.
* Include your GitHub repository or fork URL.
* Include your public LinkedIn post URL.
* Ensure your Full Name appears in the required reports.
* Do not include raw logs containing sensitive information.
* Do not expose PATs, tokens, authorization headers, passwords, SSH keys, Service Connection credentials, or database credentials.

---

# Completion Checklist

* [x] Both Azure DevOps pipelines were healthy before the drill.
* [x] The supplied files were copied to the correct repository locations.
* [x] Only the required student-specific placeholders were updated.
* [x] `CLAUDE.md` contains the required context and safety rules.
* [x] `pipeline-triage.sh` passed Bash syntax validation.
* [x] The script has executable permission.
* [x] The script uses read-only Azure DevOps operations.
* [x] The script retrieves pipeline metadata and console logs.
* [x] No token or password is stored in the script.
* [x] The healthy baseline reported `HEALTHY` with exit code `0`.
* [x] `/pipeline-triage` was invoked manually.
* [x] The skill does not have broad Bash approval.
* [x] The controlled failure affected only the Application Pipeline.
* [x] The failure occurred before deployment changes were applied.
* [x] The deliberate failure was not merged into `main`.
* [x] The failed-state report was saved before applying the fix.
* [x] Claude diagnosed the failure but did not apply the fix.
* [x] The fix was reviewed and applied manually.
* [x] The corrected Application Pipeline completed successfully.
* [x] The recovery triage reported `HEALTHY` with exit code `0`.
* [x] `incident-failure-report.txt` exists.
* [x] `recovery-report.txt` exists.
* [x] All Notes questions have been answered.
* [x] All 12 screenshots have been added.
* [x] The GitHub repository or fork URL has been included.
* [x] The LinkedIn post is public.
* [x] The LinkedIn post URL has been included.
* [x] No sensitive information is exposed.

---

# Final Submission

**Full Name:** Ginny Ibe

**GitHub Repository or Fork URL:**  https://github.com/Ginny-Ibe/devops-micro-internship-pravinmishra


**LinkedIn Post URL:** https://www.linkedin.com/feed/update/urn:li:activity:7518485274989969408/

---

*This submission is part of the DevOps Micro Internship (DMI) — Agentic AI Track.*
