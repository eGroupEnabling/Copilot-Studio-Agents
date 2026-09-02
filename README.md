## Overview

This repository contains two complementary agents:

1. **Work Improvement Idea Intake Agent** – Collects work improvement ideas from users in a conversational manner and records them in SharePoint.
2. **AI Use Case Evaluator Agent** – Automatically evaluates new AI use case submissions, enriches missing data, and notifies stakeholders.

Both agents interact with the **AI Use Case Inventory** SharePoint list, serving as a central repository for capturing and evaluating AI initiatives.

## Repository Structure

```
├── README.md                                    # This file
├── LICENSE                                      # MIT License
├── SECURITY.md                                  # Security guidelines
└── instructions/
    ├── work-improvement-idea-intake.md          # Agent 1 prompt instructions
    └── ai-use-case-evaluator.md                 # Agent 2 prompt instructions
```

## Scope

- **Agent prompt instructions** are version-controlled in the `instructions/` folder
- **Copilot Studio configuration** (environment IDs, connection references, secrets) is managed separately in your hosted Copilot Studio environment
- **SharePoint list schema** reference is provided below
- No CI/CD pipelines or Azure components are included in this solution

## License

This project is licensed under the MIT License. See [LICENSE](LICENSE).

---

## Deployment Guide

This guide walks you through deploying both agents in Copilot Studio and configuring connections to SharePoint and email services.

### Prerequisites

- **Copilot Studio** environment access
- **Microsoft 365 tenant** with:
  - SharePoint site (example: "AI Hub")
  - SharePoint list: "AI Use Case Inventory"
  - Exchange Online (for email notifications)
- **Permissions**:
  - SharePoint list read/write access
  - Outlook mailbox send access
  - Copilot Studio agent creation and editing

### Step 1: Create the SharePoint List

Before building the agents, create the **AI Use Case Inventory** list in your SharePoint site ("AI Hub").

#### List Schema

| Column Name | Type | Required | Notes |
|---|---|---|---|
| Use Case Name | Text | Yes | List title field |
| Description | Multi-line Text | Yes | Detailed problem/opportunity |
| Owner/Point of Contact | Text | Yes | Submitter name |
| Priority Class | Choice | No | 1 - Top Priority, 2 - Next Steps, 3 - Longer Term |
| Priority # | Number | No | Numeric ranking |
| Persona(s) | Multi-line Text | No | Affected users/roles |
| Replacing an existing system? | Choice | No | Yes, No, Maybe |
| Documented Process or Tribal Knowledge? | Choice | No | Both, Documented, Tribal Knowledge |
| Expected Value | Choice | No | High, Medium, Low |
| Expected Value Details | Multi-line Text | No | Business impact description |
| Scope of Impact | Choice | No | Low, Medium, High |
| Impact Details | Multi-line Text | No | Who/what is affected |
| User Readiness for Change | Choice | No | Ready, Partially Ready, Not Ready |
| Data Readiness | Choice | No | Data is Ready, Data is Partially Ready, Data is Not Ready |
| Data Source Details | Multi-line Text | No | System sources and APIs |
| Complexity Rank | Choice | No | 1 - Low, 2 - Medium, 3 - High |
| Feasibility Rank | Choice | No | 1 - High, 2 - Medium, 3 - Low |
| Buy or Build | Choice | No | Buy, Build, Hybrid |
| Buy/Build Justification | Multi-line Text | No | Rationale |
| Use M365 Copilot (Chat/Basic) for Solution? | Choice | No | Yes, No, Maybe |
| Use M365 Copilot (Licensed/Premium) for Solution? | Choice | No | Yes, No, Maybe |
| Use Copilot Studio for Solution? | Choice | No | Yes, No, Maybe |
| Use Foundry for Solution? | Choice | No | Yes, No, Maybe |
| Use Azure AI Services for Solution? | Choice | No | Yes, No, Maybe |
| Use Power Platform for Solution? | Choice | No | Yes, No, Maybe |
| Additional Solution Considerations | Multi-line Text | No | Other technical notes |
| Follow Up Questions | Multi-line Text | No | Clarification questions |
| Additional Use Case Notes | Multi-line Text | No | Internal team notes |

### Step 2: Build Agent 1 – Work Improvement Idea Intake

This agent collects new use case ideas conversationally and writes them to SharePoint.

#### Create the Agent

1. **In Copilot Studio**, create a new agent
2. **Name**: "Work Improvement Idea Intake"
3. **Description**: "Collects work improvement ideas from users and records them in the AI Use Case Inventory SharePoint list"

#### Configure Prompt Instructions

1. **In the agent authoring interface**, locate the **System Prompt** or **Instructions** field
2. **Copy the full content** from [instructions/work-improvement-idea-intake.md](instructions/work-improvement-idea-intake.md)
3. **Paste it** into the agent's system prompt
4. **Update tenant-specific references** in the prompt:
   - Replace `AI Hub` with your actual SharePoint site name
   - Replace `ai.hub@yourtenant.com` or similar with your actual SharePoint URL path

#### Configure Agent Tools and Knowledge

This is a generative agent. Configure it without adding topics:

1. In the **Model Context Protocol** section, add the **SharePoint (Preview)** tool.
2. In the **Knowledge** section, add the **AI Use Case Inventory** SharePoint list.
3. Disable **Web Search** for this agent.

#### Test Locally

1. **Start a test conversation** with the agent
2. **Walk through the flow** and verify SharePoint item is created
3. **Check the AI Use Case Inventory list** to confirm the new item appears

### Step 3: Build Agent 2 – AI Use Case Evaluator

This agent automatically evaluates new submissions and sends notifications.

#### Create the Agent

1. **In Copilot Studio**, create a new agent
2. **Name**: "AI Use Case Evaluator"
3. **Description**: "Evaluates new AI use case submissions, enriches missing fields, and notifies stakeholders"

#### Configure Prompt Instructions

1. **Locate the System Prompt or Instructions field**
2. **Copy the full content** from [instructions/ai-use-case-evaluator.md](instructions/ai-use-case-evaluator.md)
3. **Paste it** into the agent's system prompt
4. **Update references**:
   - Replace `AI Hub` with your actual SharePoint site name
   - Replace `kai.andrews@egroup-us.com` with your IT review contact email

#### Configure Agent Tools, Knowledge, and Trigger

This is a generative agent. Configure it without adding topics:

1. In the **Knowledge** section, add the **AI Use Case Inventory** SharePoint list.
2. Enable **Web Search**.
3. In the **Model Context Protocol** section, add the **Mail (Preview)** and **SharePoint (Preview)** tools.
4. Configure each tool to run under the **Maker credentials**.
5. In **Triggers**, add the SharePoint **When an item is created** trigger and select the **AI Use Case Inventory** list.

#### Test Trigger

1. **Manually add a new item** to the AI Use Case Inventory list
2. **Monitor the agent run** in Copilot Studio (check Analytics/Run history)
3. **Verify**:
   - SharePoint item was updated with enriched fields
   - Confirmation email sent to submitter
   - IT notification email sent

### Step 4: Deploy & Publish

1. **In Copilot Studio**, publish each agent:
   - Click **Publish** button
   - Wait for confirmation
   - Note the deployment ID

2. **Configure availability**:
   - Share agent with intended users or teams
   - Add to Teams, web chat, or other channels as needed

3. **Monitor & validate**:
   - Track agent analytics in Copilot Studio
   - Review SharePoint list for new submissions
   - Verify emails are being sent successfully

### Step 5: Optional – Integrate with Teams

1. **Share the agent** with your Microsoft Teams environment:
   - Copilot Studio → Agent → **Share**
   - Select your Teams workspace
   - Choose channels for availability

2. **Users can invoke** the agent directly in Teams:
   - `@Work Improvement Idea Intake` to submit ideas
   - `@AI Use Case Evaluator` (if used interactively)

---

## Agent Instructions Reference

- [Work Improvement Idea Intake Instructions](instructions/work-improvement-idea-intake.md)
- [AI Use Case Evaluator Instructions](instructions/ai-use-case-evaluator.md)

Both agents contain detailed guidelines, skills, step-by-step workflows, and interaction examples.

---

## Troubleshooting

### SharePoint Tool Issues

- **Verify permissions**: Ensure your account has read/write access to the AI Use Case Inventory list
- **Test the tool**: Confirm the **SharePoint (Preview)** tool is attached to the agent and configured to run under the Maker credentials
- **Check list name**: Confirm the exact name matches (case-sensitive in some cases)

### Mail Not Sending

- **Verify the tool**: Confirm the **Mail (Preview)** tool is attached to the evaluator and configured to run under the Maker credentials
- **Check recipient email**: Confirm email addresses are correctly mapped from SharePoint
- **Monitor runs**: Check Copilot Studio Analytics for error details in failed agent runs

### Agent Not Triggering

- **SharePoint trigger**: Confirm the "When an item is created" trigger is active
- **Verify the list**: Ensure the trigger points to the AI Use Case Inventory list
- **Check agent status**: Ensure the agent is published and active

---

## Support & Contributing

For questions or improvements to these agent examples, please refer to [SECURITY.md](SECURITY.md) for guidelines.
