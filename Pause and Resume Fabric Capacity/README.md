# 🤖 Automate Pausing and Resuming Microsoft Fabric Capacity ⚡️

This PowerShell script is designed to be used as an Azure Automation Runbook to programmatically pause or resume a Microsoft Fabric capacity. By automating this process, you can effectively manage costs by ensuring capacity is only active when needed, for instance, during business hours.

The script leverages a Managed Identity for secure, credential-free authentication to your Azure resources.

## 🔑 Key Features

- **Pause & Resume:** Start or stop your Fabric capacity on a schedule.
- **Cost-Effective:** Helps reduce Azure costs by running capacity only when necessary.
- **Secure Authentication:** Uses Azure Managed Identity, eliminating the need to store credentials in your code.
- **Flexible Inputs:** Accepts various synonyms for "suspend" and "resume" operations (e.g., `stop`, `pause`, `start`).
- **Azure Automation Ready:** Designed to be dropped directly into an Azure Automation PowerShell Runbook.

## ⚙️ How It Works

The script performs the following actions:

1. Accepts two mandatory parameters: the full **Resource ID** of the Fabric capacity and the **operation** to perform (`suspend` or `resume`).
2. Connects to Azure using the Managed Identity associated with the Automation Account.
3. Acquires an authentication token for the Azure Resource Manager API.
4. Constructs the appropriate REST API endpoint for the specified operation.
5. Sends a POST request to the API to either suspend or resume the capacity.
6. Outputs the results and status of the operation.

## ✅ Prerequisites

Before using this script, ensure you have the following in place:

1. An **Azure Automation Account**.
2. A **System-Assigned Managed Identity** enabled for the Automation Account.
3. The Managed Identity must be granted the **Fabric Capacity Administrator** or **Contributor** role on the target Microsoft Fabric Capacity resource.
4. The `Az.Accounts` PowerShell module must be available in your Automation Account's modules.

## 🚀 Setup and Usage

Follow these steps to deploy the script as an Azure Automation Runbook:

1. **Create a Runbook:**
   - Navigate to your Azure Automation Account.
   - Under "Process Automation," select **Runbooks** and click **Create a runbook**.
   - Give it a name (e.g., `Manage-Fabric-Capacity`), select **PowerShell** as the runbook type, and choose a runtime version (e.g., 7.2).

2. **Add the Script:**
   - Copy the contents of the `fabric_pause_and_resume.ps1` script and paste it into the editor.
   - Save and **Publish** the runbook.

3. **Schedule the Runbook:**
   - You can run the script manually or link it to a schedule for full automation.
   - To automate, go to **Schedules** within your Automation Account and create two schedules:
     - One for suspending the capacity (e.g., "Stop-Fabric-Nightly").
     - One for resuming the capacity (e.g., "Start-Fabric-Morning").
   - Link each schedule to your runbook by selecting the runbook and clicking **Link to schedule**.

4. **Configure Parameters for Schedules:**
   - When linking a schedule, you must provide the runbook parameters.
   - **For the "Stop" schedule:**
     - `ResourceID`: `/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.Fabric/capacities/{your-capacity-name}`
     - `operation`: `suspend`
   - **For the "Start" schedule:**
     - `ResourceID`: `/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.Fabric/capacities/{your-capacity-name}`
     - `operation`: `resume`

## 🎛️ Parameters

The script accepts the following parameters:

| Parameter    | Type   | Description                                                                                                                              |
| :----------- | :----- | :--------------------------------------------------------------------------------------------------------------------------------------- |
| `ResourceID` | String | **(Required)** The full Azure Resource ID for the Fabric capacity. Format: `/subscriptions/{sub-id}/resourceGroups/{rg}/providers/Microsoft.Fabric/capacities/{capacity-name}` |
| `operation`  | String | **(Required)** The operation to perform. See the valid options below.                                                                    |

### ✔️ Valid Operations

The `operation` parameter is flexible and accepts the following case-insensitive values:

- **To Suspend Capacity:** `suspend`, `stop`, `pause`, `0`
- **To Resume Capacity:** `resume`, `start`, `run`, `1`

## ⚠️ Error Handling

The script includes `try`/`catch` blocks to handle errors during execution. If the API call fails or another exception occurs, it will write a detailed error message to the output stream and exit with a non-zero status code, which will mark the Automation Job as "Failed".

---

*This script is provided as-is. Always test automation scripts in a non-production environment first.*
