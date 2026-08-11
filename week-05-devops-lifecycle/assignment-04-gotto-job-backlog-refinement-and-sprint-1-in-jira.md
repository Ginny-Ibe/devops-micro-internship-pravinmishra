# Assignment 4 — Gotto Job: Backlog Refinement & Sprint 1 in Jira

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

In this 90-minute, time-boxed exercise, you will act as a Scrum team — or run in Solo Mode, playing every role yourself — to turn the Gotto Job template into a value-ordered backlog, estimate the work in story points, plan Sprint 1, open the burndown chart, and ship one small UI-only increment (text, color, spacing, a label, or a CTA — no backend changes).

---

# Task 1 — Roles & Mode Setup (Team vs Solo)

## Goal

Choose Team Mode or Solo Mode, and document how each Scrum role (Product Owner, Scrum Master, Dev Lead, DevOps Lead) was handled.

### Evidence

#### Screenshot 1 — Jira "Create project" screen, or the project sidebar after creation

![ouput](./screenshots/wk5a4t1-ss1.png)

**Notes**
Mode: Solo Mode — I will performed all four roles as part of the one-person exercise.

Product Owner(PO): Prioritized the backlog by product value, placing discoverability and the search experience ahead of secondary trust signals and form labelling.

Scrum Master(SM): Kept the sprint time-boxed and the committed scope fixed, limiting the sprint to three Stories totaling four Story Points.

Dev Lead: Built the selected increment by updating the hero heading and primary search button, with no backend or data changes.

DevOps Lead: Deployed the Gotto Job template to the existing EC2 instance using a dedicated Nginx location and separate directory to prevent interference with the portfolio deployment.

---

### Notes

Write one line for each role: PO (what you prioritized), SM (how you ensured process), Dev Lead (what you built), DevOps Lead (how you shipped).

**Mode: Solo Mode — I played all four roles myself as part of the one-person exercise.**

Product Owner: I prioritized the backlog by value, putting discoverability and the search experience ahead of trust signals and form labelling.

Scrum Master: I kept the sprint time-boxed and the scope fixed, limiting the work to three Stories worth four points rather than expanding the sprint.

Dev Lead: I built the smallest useful increment: updating the hero heading and primary search button without changing the backend or data.

DevOps Lead: I deployed the Gotto Job template to my existing EC2 instance using a separate Nginx location and directory so the portfolio deployment would not overwrite it.

---

# Task 2 — Create the Jira Project (Team-managed → Scrum)

## Goal

Create a Team-managed Scrum project named `Gotto Job – Team <#>` (Team Mode) or `Gotto Job – <YourName>` (Solo Mode).

### Evidence

#### Screenshot 2 — Project created page showing the project name and key

![ouput](./screenshots/wk5a4t2-ss2.png)

**Page showing key**
![ouput](./screenshots/wk5a4t2-ss2a.png)

---

# Task 3 — Create the Epic

## Goal

Create the Epic `Improve Gotto Job UI discoverability & trust` to group the UI improvement initiative.

### Evidence

#### Screenshot 3 — Backlog showing the Epic panel with the Epic visible

![ouput](./screenshots/wk5a4t3-ss3.png)

---

# Task 4 — Seed the Product Backlog (6–8 Stories + Fibonacci Points + Ranking)

## Goal

Create at least six Stories under the Epic, estimate each with 1, 2, or 3 story points, and rank them by value.

### Evidence

#### Screenshot 4 — Backlog showing the Epic and at least six Stories under it

![ouput](./screenshots/wk5a4t4-ss4.png)

---

#### Screenshot 5 — One Story opened showing its Story Points and acceptance criteria filled in

Add your screenshot here.![ouput](./screenshots/wk5a4t4-ss5.png)

---

# Task 5 — Planning Poker (Estimate + Debate Notes)

## Goal

Confirm the Story Points (1, 2, or 3) for each Story and record brief reasoning for each estimate.

### Evidence

#### Screenshot 6 — Backlog showing Story Points visible, or two or three Stories opened showing their points

![ouput](./screenshots/wk5a4t5-ss6.png)

![ouput](./screenshots/wk5a4t5-ss6a.png)

---

### Notes

For each story, explain in one or two lines why it is a 1, 2, or 3 (mention any debate, even in Solo Mode).

# Story Point Estimates and Rationale

| Story  | Story                             | Points | Estimation Rationale                                                                                                                                                                                                               |
| ------ | --------------------------------- | -----: | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **S1** | **Hero Tagline**                  |  **1** | This is a single-content change with minimal implementation effort and no functional impact. I considered 2 points, but the limited scope and low uncertainty supported a **1-point estimate**.                                    |
| **S2** | **Button Colour**                 |  **1** | This requires a simple CSS update across the affected buttons. I considered 2 points because multiple buttons are involved, but the change is straightforward and low-risk, so **1 point** was appropriate.                        |
| **S3** | **Job Card Typography**           |  **2** | This requires changes to font size and weight plus responsive validation to ensure the layout remains consistent. I considered 1 point, but the additional visual testing and potential layout adjustments justified **2 points**. |
| **S4** | **REMOTE Badge**                  |  **2** | This requires adding a new UI element and applying conditional display logic for remote jobs. I considered 1 point, but the additional logic and validation make **2 points** more appropriate.                                    |
| **S5** | **Posted-On Date**                |  **1** | This is a straightforward content update with no new logic or significant testing requirements. The low complexity and low uncertainty support a **1-point estimate**.                                                             |
| **S6** | **Search Labels**                 |  **2** | Multiple labels and placeholders need to be updated and verified across the search interface. I considered 1 point, but the number of affected elements and required testing justified **2 points**.                               |
| **S7** | **Job Detail – Apply Now Button** |  **1** | This adds a single button with a simple email or placeholder link and does not introduce application logic. I considered 2 points, but the implementation and testing effort are small, so **1 point** was appropriate.            |
| **S8** | **Footer Trust Links**            |  **1** | This requires adding two simple links to the footer with no complex functionality or dependencies. The small scope and low uncertainty support a **1-point estimate**.                                                             |

**Total Estimate: 11 Story Points**

**Solo Mode Estimation Note:**
Because this was completed in Solo Mode, I performed the estimation challenge myself rather than through a team discussion. For each Story, I considered whether the initial estimate adequately reflected **effort, complexity, uncertainty, and testing**, and adjusted or retained the estimate accordingly.

The above avoids making the mistake of treating 'Story Points' as just a measurement of coding time.


---

# Task 6 — Sprint Planning: Create Sprint 1 + Sprint Goal + Scope

## Goal

Create Sprint 1, move three or four Stories into it (approximately 3–6 points), set the Sprint Goal, and break each selected Story into Build, Verify, Deploy, and Screenshot Sub-tasks.

### Evidence

#### Screenshot 7 — Sprint 1 with the selected Stories inside it

![ouput](./screenshots/wk5a4t6-ss7.png)

---

#### Screenshot 8 — One Story showing the Sub-tasks created

![ouput](./screenshots/wk5a4t6-ss8.png)

---

# Task 7 — Reports: Open Burndown Chart

## Goal

Open the Burndown Chart and confirm it exists for Sprint 1. It is acceptable if the chart is not yet populated.

### Evidence

#### Screenshot 9 — Burndown Chart page opened, even if empty

![ouput](./screenshots/wk5a4t7-ss9.png)

---

# Task 8 — Ship One Small Increment (Build + Deploy + Proof)

## Goal

Implement one small UI-only Story from Sprint 1, commit it, deploy it live, and move the Story and its Sub-tasks to Done in Jira.

### Evidence

#### Screenshot 10 — Jira board showing the Story moved to Done

![ouput](./screenshots/wk5a4t8-ss10.png)

![ouput](./screenshots/wk5a4t8-ss10a.png)

---

#### Screenshot 11 — Git commit output

![ouput](./screenshots/wk5a4t8-ss11.png)

---

#### Screenshot 12 — Live URL in the browser showing the UI change, with the URL visible

![ouput](./screenshots/wk5a4t8-ss12.png)

---

# Task 9 — Retro Notes (Scrum Pillar + Value)

## Goal

Add a retro comment covering what went well, what to improve, one Scrum pillar observed (Transparency, Inspection, or Adaptation), and one Scrum value (Openness, Focus, Commitment, Courage, or Respect).

### Evidence

#### Screenshot 13 — Jira retro comment visible

![ouput](./screenshots/wk5a4t9-ss13.png)

---

# Task 10 — LinkedIn Post (Mandatory)

## Goal

Publish a LinkedIn post about what you delivered, including your live URL, three to five lines on what you did and learned, and one screenshot (Burndown Chart, Sprint board, or the live UI change).

## Evidence

#### LinkedIn Post URL

Paste your LinkedIn post URL here:

https://www.linkedin.com/posts/dr-ginny-ibe_dmibypravinmishra-devops-agenticai-activity-7492318943592976385-VkeL?utm_source=share&utm_medium=member_desktop&rcm=ACoAAGTqulMBvpSBQMnxbzFBrJkA0C9nlWM_uqM

---

#### Screenshot 14 — Published LinkedIn post

![ouput](./screenshots/wk5a4link-ss.png)

---

# Submission Instructions

- Add all 14 required screenshots
- Full name must be visible in required screenshots
- Do not expose sensitive information (keys, passwords, account IDs)

---

# Completion Checklist

- [x] Task 1: Team Mode or Solo Mode selected and all four roles documented (Screenshot 1 & Notes)
- [x] Task 2: Team-managed Scrum project created with the required name (Screenshot 2)
- [x] Task 3: UI improvement Epic created (Screenshot 3)
- [x] Task 4: 6–8 Stories added under the Epic and ranked by value (Screenshots 4 & 5)
- [x] Task 5: Story Points set (1, 2, or 3) with reasoning recorded (Screenshot 6 & Notes)
- [x] Task 6: Sprint 1 created with Sprint Goal, 3–4 Stories, and Sub-tasks (Screenshots 7 & 8)
- [x] Task 7: Burndown Chart opened (Screenshot 9)
- [x] Task 8: One UI-only increment implemented, committed, deployed, and verified (Screenshots 10–12)
- [x] Task 9: Retro comment with one Scrum pillar and one Scrum value (Screenshot 13)
- [x] Task 10: Mandatory LinkedIn post published with the live URL, backlog refinement, Sprint planning, one shipped increment, proof, and Screenshot 14
- [x] Full Name visible in required screenshots
- [x] No sensitive data exposed

---

## 📌 About DMI & CloudAdvisory

DevOps Micro Internship (DMI) is a project-based DevOps program run by Pravin Mishra (The CloudAdvisory) focused on real-world execution, systems thinking, and career readiness.

It helps learners build strong DevOps foundations with hands-on experience.

---

## 📌 Resources

- 🌐 DMI Official Website: https://dmi.pravinmishra.com?utm_source=github&utm_medium=readme  
- 🎓 University: https://university.pravinmishra.com?utm_source=github&utm_medium=readme  
- 💬 Discord Community: https://discord.pravinmishra.com?utm_source=github&utm_medium=readme  
- 📝 Blog: https://dmi.pravinmishra.com/blog?utm_source=github&utm_medium=readme  
- ▶️ YouTube Playlist: https://www.youtube.com/playlist?list=PLFeSNDtI4Cho  
- 🔗 Pravin Mishra (LinkedIn): https://www.linkedin.com/in/pravin-mishra-aws-trainer/  
- 🏢 CloudAdvisory (LinkedIn): https://www.linkedin.com/company/thecloudadvisory/

---

*This submission is part of DevOps Micro Internship (DMI) Cohort 3 — Agentic AI Track.*
