# MLOps before the hands-on lab setup guide

## Before the hands-on lab

### Task 1: Create New Project in Azure DevOps

1. Sign in to [Azure DevOps](http://dev.azure.com).

2. Select **+ New project**.

    ![In the Azure DevOps screen, the + New project button is selected.](media/devops-project-01.png 'Create new project')

3. Provide Project Name: `mlops-quickstart` and select **Create**.

    ![The Create new project dialog is shown populated with the value above. Visibility is set to Private, and the Create button is highlighted.](media/devops-project-02.png 'Create New Project Dialog')

### Task 2: Install the Microsoft DevLabs Machine Learning plugin for Azure DevOps

1. Navigate to the VisualStudio Marketplace: https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml and select **Get it free**.

2. Select **Azure DevOps organization** from the dropdown list and then select **Install**.

    ![The Microsoft DevLabs Machine Learning plugin for Azure DevOps page is shown with the Azure DevOps organization selected and the Install button highlighted.](media/bhol-04.png 'Install Plug-In')

### Task 3: Create an Azure Machine Learning workspace

1. Sign in to [Azure portal](https://portal.azure.com) by using the credentials for your Azure subscription.

2. In the upper-left corner of Azure portal, select **+ Create a resource**.

3. Use the search bar to find the **Machine Learning**.

4. Select **Machine Learning**.

5. In the **Machine Learning** pane, select **Create** to begin.

   ![The Machine Learning page displays with the Create button selected.](media/bhol-01.png 'Open Create Azure Machine Learning Workspace')

6. Provide the following information to configure your new workspace:

   - **Subscription**: Select the Azure subscription that you want to use.

   - **Resource group**: Use an existing resource group in your subscription or enter a name to create a new resource group. A resource group holds related resources for an Azure solution. In this example, we use **MCW-MLOps**.

   - **Workspace name**: Enter a unique name that identifies your workspace. In this example, we use **quick-start-ws**. Names must be unique across the resource group. Use a name that's easy to recall and to differentiate from workspaces created by others.

   - **Location**: Select the location closest to your users and the data resources to create your workspace.

   - **Container registry**: Use an existing container registry in your subscription or enter a name to create a new container registry. For example, you can use **quickstartwsregistry**.

   ![The Machine Learning Create form is displayed populated with the aforementioned values. The Review + Create button is highlighted.](media/bhol-02a.png 'Create Azure Machine Learning Workspace page')

7. After you are finished configuring the workspace, select **Review + Create**. Select **Create** after you review the fields you just entered.

    > **Note**: It can take several minutes to create your workspace in the cloud.

    When the process is finished, a deployment success message appears.

8. To view the new workspace, select **Go to resource**.

9. Navigate to the [Azure Machine Learning Studio](https://ml.azure.com) and select the workspace that you created or select **Launch now** under **Try the new Azure Machine Learning studio** in the **Overview** section of your Azure Machine Learning workspace.

   ![The Machine Learning resource page is shown with Overview selected from the left menu, and the Launch now button highlighted in the Overview screen.](media/bhol-03a.png 'Launch the Azure Machine Learning studio')

   > **Note**: If you use an existing workspace, please ensure that the default datastore for the workspace is the workspace blob store and not the file store. If you create a new workspace, as instructed above, the default datastore is already set to the workspace blob store.

### Task 4: Setup AKS Cluster for Production Deployment

1. From within the Azure Machine Learning Studio, navigate to **Compute, Inference Clusters** and select **+ New**.

  ![The Azure Machine Learning Studio Compute, Inference Clusters section is shown with the + New button highlighted.](media/setup-aks-01.png 'Create New Inference Cluster')

2. In the `Select virtual machine` dialog, select a location closest to your AML Workspace, select a VM size **Standard_D3_v2** or equivalent and then select **Next**.

  ![The Select virtual machine dialog is shown with the selected VM location, selected VM size, and the Next button highlighted.](media/setup-aks-02.png 'Select Virtual Machine')

3. In the `Configure Settings` dialog, provide the following values and then select **Create**:

   - **Compute name**: `aks-cluster01`
   - **Cluster purpose**: `Dev-test`
   - **Number of nodes**: `1`
   - **Network configuration**: `Basic`

 ![The Configure Settings dialog is shown populated with the values above, and the Create button highlighted.](media/setup-aks-03.png 'Configure Settings')

### Task 5: Setup Azure DevOps Agent

In order to complete the lab, you need an DevOps agent to run your build and release pipeline jobs. You have two options for the types of agents you can use to run your pipeline jobs:

1. Microsoft-hosted agents
2. Self-hosted agents

**Option 1: Configure Microsoft-hosted agents**

Microsoft has temporarily disabled the free grant of parallel jobs running on Microsoft-hosted agents for public projects and for certain private projects. If you do not already have an approved free grant for parallel jobs, you can request the grant by submitting a request [here](https://aka.ms/azpipelines-parallelism-request). **Please note that it takes about 2-3 business days to respond to your free grant requests**. The other option is to configure and pay for parallel jobs as per instructions [here](https://docs.microsoft.com/en-us/azure/devops/pipelines/licensing/concurrent-jobs?view=azure-devops&tabs=ms-hosted).

**Option 2: Setup and configure self-hosted agents**

For this option, we will look at steps to setup and configure a self-hosted agent that can be used to run your pipeline jobs.

1. Sign in to [Azure DevOps](http://dev.azure.com).

2. Select **User setting, Personal Access Tokens**.

    ![Azure DevOps home page is shown with the user setting menu open and the Personal Access Tokens option selected.](media/setup-pat1.png 'Personal Access Tokens')

3. Select **+ New Token** and provide the following information and then select **Create**:

    - **Name**: `mlops-agent`
    - **Organization**: `Select your organization`
    - **Full access**: `Selected`

    ![Create new personal access token dialog is shown populated with the values above, and the Create button is highlighted.](media/setup-pat2.png 'Create New Personal Access Token')

4. Save the token somewhere safe and accessible for later use during the setup and close the window.

   ![Success dialog is shown, and the Copy and Close buttons are highlighted.](media/setup-pat3.png 'Personal Access Token')

5. From within Azure DevOps, navigate to **Organization Settings, Agent pools** and then select **Add pool**.

   ![Agent pools section under organization settings is shown, and the Add pool button is highlighted.](media/setup-agent-pool1.png 'Add Pool')

6. In the `Add agent pool` dialog provide the following information and then select **Create**:

    - **Pool type**: `Self-hosted`
    - **Name**: `MCW Agent Pool`
    - **Grant access permission to all pipelines**: `Selected`
    - **Auto-provision this agent pool in all projects**: `Selected`

    ![Add gent pool token dialog is shown populated with the values above, and the Create button is highlighted.](media/setup-agent-pool2.png 'Add Agent Pool')

7. Navigate to **MCW Agent Pool, Agents** and then select **New agent**.

    ![The Agents section of the MCW Agent Pool page is shown, and the New agent button is highlighted.](media/setup-agent-pool3.png 'Add New Agent')

8. From the `Get the agent` dialog, select **Linux, x64**, copy `Download the agent URL` and save it for later use during the setup, and then close the dialog.

    ![The Linux, x64 section of the Get the agent dialog is shown, and the Copy button is highlighted.](media/setup-agent-pool4.png 'Get the Agent')


9. From within Azure Portal, navigate to **Virtual machines** and then select **+ Create, + Virtual machine**.

10. In the `Create a virtual machine` dialog, provide the following values and then select **Review + create**:

    - **Subscription**: Select the Azure subscription that you want to use.
    - **Resource group**: Use an existing resource group in your subscription or enter a name to create a new resource group.
    - **Virtual machine name**: **mlops-agent**
    - **Region**: Select the region closest to your users and the data resources.
    - **Image**: **Ubuntu Server 18.04 LTS - Gen2**
    - **Size**: **Standard_D2s_v3 - 2 vcpus, 8 GiB memory**
    - **Authentication type**: **password**
    - **Username**: **mlopsuser**
    - **Password**: Provide a password and save it for later use.
    - **Public inbound ports**: **Allow selected ports**
    - **Select inbound ports**: **SSH (22)**

    ![The Create a virtual machine dialog is shown populated with the values above.](media/setup-vm1.png 'Create Virtual Machine')

11. On the `Review + create` section, select **Create**. It will take few minutes for the VM to be deployed. Continue below once the VM deployed and ready.

12. From the `Overview` section of the virtual machine, copy the **Public IP address** and save it for later use.

   ![Virtual Machine Overview page is shown with copy Public IP address button is highlighted.](media/setup-vm2.png 'Virtual Machine Overview')

13. From an `Azure cloud shell` or `terminal` or `command prompt`, run the following commands:

    - `ssh mlopsuser@xx.xxx.xxx.xxx` (replace the IP address with your VMs public IP address)
       - If you are prompted: `Are you sure you want to continue connecting (yes/no/[fingerprint])?`, type `yes`
    - Provide password for `mlopsuser`

14. Once you are logged into the VM, run the following commands:

   - `curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash`
   - `az extension add --name azure-cli-ml`
   - `curl -O [Download the agent URL copied above]`
     - For example, `curl -O https://vstsagentpackage.azureedge.net/agent/2.193.0/vsts-agent-linux-x64-2.193.0.tar.gz`
   - `mkdir myagent && cd myagent`
   - `tar zxvf ../vsts-agent-linux-x64-2.193.0.tar.gz`
       - Ensure that the `tar.gz` file is the one downloaded above
   - `./config.sh`
       - Accept the Team Explorer Everywhere license agreement now? `Y`
       - Enter server URL > [Provide your Azure DevOps organization URL]
           - For example, `https://dev.azure.com/organization-name`
       - Enter authentication type (press enter for PAT) > press enter
       - Enter personal access token > [Provide the PAT saved above]
       - Enter agent pool (press enter for default) > `MCW Agent Pool`
       - Enter agent name (press enter for mlops-agent) > press enter
       - Enter work folder (press enter for _work) > press enter
   - `sudo ./svc.sh install`
   - `sudo ./svc.sh start`
      
      > **Note**: to stop the agent run: `sudo ./svc.sh stop`. If required, you can find more details on setting up and configuring Self-hosted Linux agents [here](https://docs.microsoft.com/en-us/azure/devops/pipelines/agents/v2-linux?view=azure-devops).

15. From within Azure DevOps, navigate to **Organization Settings, Agent Pools, MCW Agent Pool** and then select the **Agents** tab. Confirm that the `mlops-agent` is `online`.

  ![The Agents tab of MCW Agent Pool showing the status of the mlops-agent as online.](media/check-agent-status.png 'MCW Agent Pool Status')

You should follow all steps provided *before* performing the Hands-on lab.
