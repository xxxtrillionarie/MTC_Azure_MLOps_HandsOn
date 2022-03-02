## Exercise 1: Setup New Project in Azure DevOps

### Task 1: Sign in to Azure DevOps Project

1. Sign in to [Azure DevOps](http://dev.azure.com).

2. Select the project **mlops-quickstart**.

### Task 2: Import Quickstart code from a GitHub Repo

1. In the project `mlops-quickstart`:

   - Select **Repos** from left navigation bar.

   - Select **Import** from the content section.

    ![In Azure DevOps, Repos is selected from the left menu. In the mlops-quickstart screen the Import button is selected in the Import a repository section.](media/devops-project-03.png 'Azure DevOps Repos')

2. Provide the following GitHub URL: `https://github.com/solliancenet/mcw-mlops-starter-v3` and select **Import**. This should import the code required for the quickstart.

    ![In the Import a Git repository dialog, the Clone URL is populated with the value indicated above and the Import button is selected.](media/devops-project-04.png 'Import a Git repository dialog')

### Task 3: Update the build YAML file

1. Select and open the **azure-pipelines.yml** file.

   >**Note**: If you are using Microsoft-hosted agents, rename `azure-pipelines.yml` to `azure-pipelines-self-hosted.yml` and then rename `azure-pipelines-hosted.yml` to `azure-pipelines.yml`. Continue below with `azure-pipelines.yml`.

2. Select **Edit** and update the following variables: **resourcegroup**, and **workspace**.

    ![In the left menu, beneath Repos, the Files item is selected. In the files list, azure-pipelines.yml is selected. In the editor pane, the contents of the file are displayed with the resource group and workspace variables highlighted. The Edit button is selected in the top toolbar.](media/devops-build-pipeline-01.png 'Edit Build YAML file')

3. Select **Commit** to save your changes and select **Commit** again in the commit properties dialog.

    ![The content of azure-pipelines.yml is updated and the Commit button is selected in the top taskbar.](media/devops-build-pipeline-02.png 'Commit Build YAML file')

### Task 4: Create new Service Connection

1. From the left navigation, select **Project settings** and then select **Service connections**.

    ![In the left menu, Project settings is selected. In the Project Settings list, Service connections is selected. In the Create your first service connection screen, the Create service connection button is selected.](media/devops-build-pipeline-03.png 'Service Connections')

2. Select **Create service connection**, select **Azure Resource Manager**, and then select **Next**.

    ![In the New service connection dialog, Azure Resource Manager is selected from the list. The Next button is selected.](media/devops-build-pipeline-04.png 'Azure Resource Manager')

3. Select **Service principal (automatic)** and then select **Next**.

    ![In the New service connection dialog, under Authentication method, Service principal (automatic) is selected. The Next button is selected.](media/devops-build-pipeline-05.png 'Service principal authentication')

4. Provide the following information in the **New Azure service connection** dialog box and then select **Save**:

    - **Scope level**: **Machine Learning Workspace**

    - **Subscription**: Select the Azure subscription to use.

      > **Note**: You may be asked to log into Azure Portal to access the list of available subscriptions. It might take up to 30 seconds for the **Subscription** dropdown to be populated with available subscriptions, depending on the number of different subscriptions your account has access to.

    - **Resource group**: This value should match the value you provided in the **azure-pipelines.yml** file.

    - **Machine Learning Workspace**: This value should match the value you provided in the **azure-pipelines.yml** file.

    - **Service connection name**: `quick-starts-sc`

    - **Security**: Grant access permission to all pipelines is checked.

    ![The New Azure service connection form is populated with the values outlined above. The Save button is selected.](media/devops-build-pipeline-06.png 'Add an Azure Resource Manager service connection dialog')

    >**Note**: If you are unable to select your **Machine Learning Workspace**, do the following steps:

    - Quit the `New Azure service connection` dialog.
    
    - Refresh or reload the web browser.
    
    - Repeat steps 1-3 above.
    
    - In step 4, change the `Scope level` to **Subscription** and then select your **Resource group**.
    
    - Please remember to name your service connection as `quick-starts-sc`.
    
    - Grant access permission to all pipelines.

## Exercise 2: Setup and Run the Build Pipeline

### Task 1: Setup Build Pipeline

1. From left navigation select **Pipelines, Pipelines** and then select **Create pipeline**.

    ![In the left menu, the Pipelines item is expanded with the Pipelines item selected. In the content pane, the Create pipeline button is highlighted.](media/devops-build-pipeline-07.png 'Create Build Pipeline')

2. Select **Azure Repos Git** as your code repository.

    ![On the Connect tab of your pipeline screen, Azure Repos Git is selected beneath the Where is your code? prompt.](media/devops-build-pipeline-08.png 'Repository Source')

3. Select **mlops-quickstart** as your repository.

    ![On the Select tab of your pipeline screen, beneath the Select a repository section, the mlops-quickstart repository is selected.](media/devops-build-pipeline-09.png 'Select Repository')

4. Review the YAML file.

    The build pipeline has four key steps:

    - Attach folder to workspace and experiment. This command creates the **.azureml** subdirectory that contains a **config.json** file that is used to communicate with your Azure Machine Learning workspace. All subsequent steps rely on the **config.json** file to instantiate the workspace object.

    - Create the AML Compute target to run your main pipeline for model training and model evaluation.

    - Run the main pipeline. The main pipeline has two steps: (1) Train the machine learning model, and (2) Evaluate the trained machine learning model. The evaluation step evaluates if the new model performance is better than the currently deployed model. If the new model performance is improved, the evaluate step will register the new model with Azure Machine Learning workspace. You can review the code for the main pipeline and its steps in **aml_service**, **aml_service/scripts/train.py**, and **aml_service/scripts/evaluate.py**.

    - Publish the build artifacts. The **snapshot of the repository** is published as build artifacts.

    ![On the Review tab of your pipeline screen, the contents of azure-pipelines.yml is displayed.](media/devops-build-pipeline-10.png 'Build pipeline YAML')

### Task 2: Run the Build Pipeline

1. Select **Run** to start running your build pipeline.

    ![On the Review tab of your pipeline screen, the contents of azure-pipelines.yml is displayed. The Run button is selected from the top taskbar.](media/devops-build-pipeline-11.png 'Run Build Pipeline')

2. Monitor the build run. The build pipeline, for the first run, will take around 30-35 minutes to run.

    ![A build pipeline run summary screen is displayed indicating it was manually triggered. A single job is selected in the Jobs section with a status of Success.](media/devops-build-pipeline-12.png 'Monitor Build Pipeline')

3. Select **Job** to monitor the detailed status of the build pipeline execution.

    ![In the Jobs screen, the Job that was selected in the previous step is expanded displaying multiple steps. To the right a summary of the run is displayed.](media/devops-build-pipeline-13.png 'Monitor Build Pipeline Details')

### Task 3: Review Build Outputs

1. Log in to [Azure Machine Learning studio](https://ml.azure.com) either directly or via the [Azure Portal](https://portal.azure.com). Make sure you select the Azure Machine Learning workspace that you created from the notebook earlier. Open your **Models** section and observe the versions of the registered model: **compliance-classifier**. The latest version is the one registered by the build pipeline you ran in the previous task.

    ![In Azure Machine Learning Studio, models is selected in the left menu. A list of multiple versions of the compliance-classifier model is displayed in the Model list table with the largest version of the model highlighted in the table.](media/devops-build-outputs-01.png 'Registered Models in Azure Machine Learning studio')

2. Select the latest version of your model to review its properties. The **build_number** tag links the registered model to the Azure DevOps build that generated it.

    ![The model screen for the latest version of the compliance-classifier is displayed with the Build_Number tag highlighted.](!media/../media/devops-build-outputs-02.png 'Registered model details and Build_Number tag')

3. Open your **Datasets** section and observe the versions of the registered dataset: **connected_car_components**. The latest version is the one registered by the build pipeline you have run in the previous task.

    ![In Azure Machine Learning Studio, Datasets is selected from the left menu. The Datasets screen displays a list of datasets on the Registered datasets tab. The connected_car_components dataset is selected in the table with its tag indicating the build number matching the value from the previous step.](media/devops-build-outputs-03.png 'Registered Datasets in Azure Machine Learning studio')

4. Select the latest version of your dataset to review its properties. Notice the **build_number** tag that links the dataset version to the Azure DevOps build that generated it.

    ![The registered dataset version properties screen is displayed with a tag that indicates the build_number.](medial/../media/devops-build-outputs-04.png 'Registered dataset details in Azure Machine Learning studio')

5. Select **Models** to view a list of registered models that reference the dataset.

    ![A list of registered models that reference the selected dataset is displayed.](media/devops-build-outputs-05.png 'Registered dataset model references in Azure Machine Learning studio')

## Exercise 3: Setup the Release Pipeline

### Task 1: Create an Empty Job for the Release Pipeline

1. Return to Azure DevOps and navigate to **Pipelines, Releases** and select **New pipeline**.

    ![In Azure DevOps, the Pipelines item is expanded in the left menu with the Releases item selected. In the content pane, a message indicates No release pipelines found and the New pipeline button is selected.](media/devops-release-pipeline-01.png 'New Release Pipeline')

2. Select **Empty job**.

    ![In the Select a template dialog, the Empty job item is selected above the list featured templates.](media/devops-release-pipeline-022.png 'Select a template: Empty job')

3. Provide Stage name: `Test Deployment` and close the dialog.

    ![On the Stage dialog, the Stage name textbox is populated with Test Deployment. The close button at the top of the dialog is selected.](media/devops-release-pipeline-03b.png 'Deploy and Test Stage')

### Task 2: Add Build Artifacts for the Release Pipeline

1. Select **+ Add an artifact**.

    ![In the New release pipeline screen, on the Pipeline tab, the + Add a new artifact item is selected within the Artifacts tile.](media/devops-release-pipeline-04b.png 'Add an artifact')

2. Select **Source type**: **AzureML Model Artifact** and provide the values below and then select **Add**.

    - **Service Endpoint:** `quick-starts-sc`
    - **Model Names:** `compliance-classifier`
    - **Default version:** `Latest version for the specified models`
    - **Source alias:** `_compliance-classifier`

    ![The Add an artifact dialog form is populated with the aforementioned values. The Add button is selected.](media/devops-release-pipeline-05b.png 'Add a build artifact')

    > **Note:** Please ensure you have installed the Microsoft DevLabs Machine Learning plugin for Azure DevOps as described in the `Before the HOL – MLOps.md`.

3. Select **+ Add an artifact**, then select **Source type**: **Azure Repos Git** and provide the values below and then select **Add**.

    - **Project:** `mlops-quickstart`
    - **Source (repository):** `mlops-quickstart`
    - **Default branch:** `Select the branch for your repo`
    - **Default version:** `Latest from the default branch`
    - **Source alias:** `_mlops-quickstart`

    ![The Add an artifact dialog form is populated with the aforementioned values. The Add button is selected.](media/devops-release-pipeline-05c.png 'Add a build artifact')

### Task 3: Setup Agent Pool for Test Deployment stage

1. Open **View stage tasks** link.

    ![On the New release pipeline screen, within the Test Deployment tile, the View stage tasks link is selected.](media/devops-release-pipeline-06b.png 'View Stage Tasks')

2. Select **Agent job** and then select **MCW Agent Pool** as the Agent pool.

    ![On the New release pipeline screen, Tasks tab, the Agent job is selected. The Agent job details form is populated with the aforementioned values.](media/devops-release-pipeline-10b.png 'Agent Job Setup')

    >**Note**: If you are using Microsoft-hosted agents, select **Azure Pipelines** as the Agent pool, and **ubuntu-18.04** as the Agent Specification.

### Task 4: Add Install the AML CLI task to Test Deployment stage

1. Select **Add a task to Agent job** (the **+** button), search for `Azure CLI`, and select **Add**.

    ![On the New release pipeline screen, the Tasks tab is selected. Agent job is displayed in the list of tasks. The + button is selected in the Agent job tile. The Add tasks pane is displayed with Azure CLI entered in the search box, and the Azure CLI search result is highlighted with its Add button selected.](media/devops-release-pipeline-77a.png 'Add Azure CLI Task')

2. Provide the following information:

    - **Task version:** `2.*`
    - **Display name:** `Install the AML CLI`
    - **Azure Resource manager connection:** `quick-starts-sc`
    - **Script Type:** `Shell`
    - **Script Location:** `Inline script`
    - **Inline Script:** `az extension add -n azure-cli-ml`

    >**Note**: If the **Azure Resource manager connection**: `quick-starts-sc` does not show up automatically. We need to first authorize the AML workspace.

    >**Note**: If you are using Microsoft-hosted agents, select `1.*` as the **Task version**.

    ![The Azure CLI Task form is displayed populated with the preceding values.](media/devops-release-pipeline-77b.png 'Azure CLI Task Dialog')

### Task 5: Add AzureML Model Deploy task to Test Deployment stage

1. Select **Add a task to Agent job** (the **+** button), search for `AzureML Model Deploy`, and select **Add**.

    ![On the New release pipeline screen, the Tasks tab is selected. Agent job is displayed in the list of tasks. The + button is selected in the Agent job tile. The Add tasks pane is displayed with AzureML Model Deploy entered in the search box, and the AzureML Model Deploy search result is highlighted with its Add button selected.](media/devops-release-pipeline-11b.png 'Add AzureML Model Deploy Task')

2. Provide the following information:

    - **Display name:** `Test Deployment`
    - **Azure ML Workspace:** `quick-starts-sc`
    - **Model Source:** `Model Artifact`
    - **Inference config Path:** `$(System.DefaultWorkingDirectory)/_mlops-quickstart/aml_service/image_files/inferenceConfig.yml`
    - **Model Deployment Target:** `Azure Container Instance`
    - **Deployment Name:** `test-service`
    - **Deployment configuration file:** `$(System.DefaultWorkingDirectory)/_mlops-quickstart/aml_service/image_files/aciDeploymentConfig.yml`
    - **Overwrite existing deployment:** `checked`

    ![The AzureML Model Deploy Task form is displayed populated with the preceding values.](media/devops-release-pipeline-12b.png 'AzureML Model Deploy Task Dialog')

### Task 6: Clone the Test Deployment stage

1. Select **Pipeline, Clone**.

    ![On the New release pipeline screen, the Pipeline tab is selected and then the Clone button is selected on the Test Deployment stage.](media/devops-release-pipeline-13b.png 'Clone Test Deployment Stage')

### Task 7: Configure the Production Deployment stage

1. Open **View stage tasks** link.

    ![On the New release pipeline screen, within the Copy of Test Deployment tile, the View stage tasks link is selected.](media/devops-release-pipeline-13c.png 'View Stage Tasks')

2. Provide new stage name: **Production Deployment**

   ![On the New release pipeline screen, the Tasks tab is selected and a new stage name is provided.](media/devops-release-pipeline-13d.png 'Production Deployment stage')

3. Select **AzureML Model Deploy task** and change the following information:

    - **Display name:** `Production Deployment`
    - **Model Deployment Target:** `Azure Kubernetes Service`
    - **Select AKS Cluster for Deployment:** `aks-cluster01`
    - **Deployment Name:** `compliance-classifier-service`
    - **Deployment configuration file:** `$(System.DefaultWorkingDirectory)/_mlops-quickstart/aml_service/image_files/aksDeploymentConfig.yml`

    ![The AzureML Model Deploy Task form is displayed populated with the preceding values.](media/devops-release-pipeline-13e.png 'AzureML Model Deploy Task Dialog')

### Task 8: Enable Pre-deployment Approvals

1. Navigate to **Pipeline** tab and select **Pre-deployment conditions** for the **Production Deployment** stage.

2. Select **Pre-deployment approvals, enabled** and provide the name of approvers.

    ![In the New release pipeline screen, the Pipeline tab is selected. Within the Stages tile, the Pre-deployment Condition item is selected in the Production Deployment stage tile. The Pre-deployment conditions form is displayed with the Pre-deployment approvals section expanded.](media/devops-release-pipeline-21-b.png 'Pre-deployment Conditions Dialog')

3. Close the dialog.

### Task 9: Save the Release Pipeline

1. Provide name: `Production Release Pipeline`.

2. Select **Save** (use the default values in the **Save** dialog).

    ![In the header of the New pipeline screen, the pipeline name is set to Production Release Pipeline, and the Save button is selected from the top taskbar.](media/devops-release-pipeline-23b.png 'Save')

## Exercise 4: Create Release for the Production Release Pipeline

### Task 1: Create new release

1. Select **Releases, Production Release Pipeline, Create release**.

    ![In Azure DevOps, the Pipelines item is expanded in the left menu with the Releases item selected. The Production Release Pipeline is selected followed by Create release.](media/devops-release-pipeline-01b.png 'Create New Release')

2. On the `Create a new release` page review the `Artifacts` section and select **Create**.

    ![In the Create a new release dialog, the Artifacts section is highlighted and the Create button is selected.](media/devops-release-pipeline-02.png 'Create New Release')

### Task 2: Monitor the Test Deployment stage

1. Reload the `Production Release Pipeline`. Observe that the Production Release Pipeline's `Test Deployment` stage is running. Select as shown in the figure to view the pipeline logs.

   ![In Azure DevOps, on the left menu, Pipelines is expanded and the Releases item is selected. The Production Release Pipeline screen is displayed. A button in the Stages column selected that is used to view the pipeline logs.](media/devops-test-pipelines-05.png 'Pipelines - Releases')

2. The `Test Deployment` stage will run for about 10 minutes. Wait for the stage to complete.

3. From the pipeline logs view, select **Test Deployment** task to view details.

   ![A list of tasks related to Agent job is displayed. The Test Deployment task is selected from the list.](media/devops-test-pipelines-06.png 'Pipeline Logs')

4. Observe the **Scoring URI** for the deployed ACI webservice. Copy the `Scoring URI` for the next exercise.

    ![The Test Deployment task logs are displayed. Within the logs the Scoring URI is highlighted.](media/devops-test-pipelines-07.png 'Test Deployment Task Logs')

## Exercise 5: Testing the deployed solution and review deployed model datasheet

Documenting the right information in the machine learning process is key to making responsible decisions at each stage. Datasheets are a way to document machine learning assets that are used and created as part of the machine learning lifecycle. [Annotation and Benchmarking on Understanding and Transparency of Machine Learning Lifecycles (ABOUT ML), an initiative from the Partnership in AI (PAI)](https://www.partnershiponai.org/about-ml), provides a set of guidelines for machine learning system developers to develop, test, and implement machine learning system documentation. Developers can use Azure Machine Learning SDK to implement datasheets for models as part of the model registration processes in Azure Machine Learning workspace. During model registration, the tags parameter in the register method of the Model class can be leveraged to document the necessary datasheet information recommended in the established guidelines.

In this exercise, you verify that the release of the application works. You will also review the deployed model datasheet.

> **Note:** The new [Azure Machine Learning studio](https://ml.azure.com) provides a new immersive experience for managing the end-to-end machine learning lifecycle. You can use it either by logging in directly to it or by selecting the **Try the new Azure Machine Learning studio, Launch now** option in the **Overview** section of your Azure Machine Learning workspace.

### Task 1: Setup the notebooks environment

1. Download the [**Test Deployment.ipynb**](./notebooks/Test&#32;Deployment.ipynb) notebook to your computer, by selecting the **Raw** view in GitHub, and then **right-click + Save as**. Please ensure that the extension of the saved file is `.ipynb`. This is the notebook you will step through executing in this lab.

2. Sign in to [Azure Machine Learning studio](https://ml.azure.com).

3. Select your subscription and the workspace you have available.

4. Select **Notebooks, Go directly to my notebooks** on the left navigation pane.

    ![In Azure Machine Learning Studio, Notebooks is selected from the left navigation pane.](media/notebook-00.png 'Open notebooks in Azure Machine Learning Studio')

5. Select the folder under the **User files** section. It should be named as the currently logged username. Select the option to **Create new folder** in the top menu.

    ![On the Notebooks screen, the current user is selected beneath the User Files section, and the Create New Folder icon is highlighted in the top toolbar.](media/notebook-01.png 'Create new notebooks folder')

6. Fill in the folder name: `MCW-MLOps`.

7. Select the **Upload files** option in the side menu.

    ![On the Notebooks screen, beneath user files, the folder of the current user is expanded displaying the folder that was created in the previous step. The Upload files icon is highlighted in the top toolbar.](media/notebook-02.png 'Upload notebook to the workspace file share')

8. Browse for the downloaded notebook, **Test Deployment.ipynb** and then select **Upload**.

9. Select the notebook. Select **+ New Compute** to create the compute instance VM.

    ![On the Notebooks screen, beneath user files, the folder of the current user is expanded along with the folder that was created in step 6. Inside this folder the Notebook that we uploaded in the previous step is displayed and is selected. On the Compute screen to the right, the + New Compute button is highlighted in the top taskbar.](media/notebook-03.png 'Create new compute instance')

10. Provide the necessary data for creating a new compute instance to run on your notebooks.

    - Compute name: `mlops-compute-<insert unique identifier>`. When you create a VM, provide a name. The name must be between 2 to 16 characters. Valid characters are letters, digits, and the – character. The compute name must not end with '-' or contain '-' followed by numbers. '-' needs to be followed by at least one letter. Finally, the compute name must also be unique across the entire Azure region.

    - Virtual machine type: CPU (Central Processing Unit)

    - Virtual machine size: **Standard_D3_v2**.

    - Then select **Create**. It can take approximately 5 minutes to set up your VM.

    ![The New Compute Instance form is displayed populated with the preceding values.](media/notebook-04.png 'Configure the new compute instance')

    > **Note**: Once the VM is available it will be displayed in the top toolbar.

### Task 2: Test the deployment and review model datasheet

1. Select the uploaded **Test Deployment.ipynb** notebook, and then select the **Menu** drop down, and then select **Edit in Jupyter**. In the pop dialog, select **Continue** and then a new browser tab will be opened.

    ![On the Notebooks screen, the Deep Learning with Text Notebook is selected. On the Compute screen to the right, a drop down list shows the compute currently running for the notebook, and in the taskbar the Edit option is expanded with the options Edit in Jupyter and Edit in JupyterLab.](media/notebook-05.png 'Edit the notebook in Jupyter')

2. Select **Python 3.6 - Azure ML** if you are asked to select a Kernel.

    ![A Kernel not found dialog is displayed with Python 3.6 - Azure ML selected from a drop down list. A Set Kernel button is available to confirm the kernel selection.](media/notebook-06.png 'Select Kernel version')

3. Note that you will have to provide values for **Scoring URI** for the deployed ACI webservice in the notebook.

4. Follow the instructions within the notebook to complete the task.

## Exercise 6: Deploy the Production Deployment stage

### Task 1: Approve the Production Deployment

1. From the email that was sent to the approvers email address, select **View approval**.

    ![The email sent to approver is shown with the View approval button highlighted.](media/devops-test-pipelines-09.png 'View Approval')

2. Select **Approve, Approve** from the `Production Deployment` stage.

    ![The pipeline release page showing the steps to approve the Production Deployment stage.](media/devops-test-pipelines-10.png 'Approve Production Deployment Stage')

### Task 2: Monitor the Production Deployment stage

1. The `Production Deployment` stage will run for about 3-5 minutes. Wait for the stage to complete.

2. From the pipeline logs view, select **Production Deployment** task to view details.

   ![A list of tasks related to Agent job is displayed. The Production Deployment task is selected from the list.](media/devops-test-pipelines-07b.png 'Pipeline Logs')

3. Navigate to Pipelines, Releases and select **Production Release Pipeline, Release-1** to review the pipeline artifacts and stages.

   ![The image shows the various artifacts and stages for the Release-1 of the Production Release Pipeline.](media/devops-test-pipelines-07d.png 'Production Release Pipeline')

4. Navigate to Azure Machine Learning Studio and select **Endpoints, compliance-classifier-service, Consume**. Copy the **Scoring URI** and **API Key** for the next exercise.

    ![The Production Deployment task logs are displayed. Within the logs the Scoring URI and API Key is highlighted.](media/devops-test-pipelines-07c.png 'Consumption Info')

## Exercise 7 (Optional): Examining deployed model performance

In this exercise, you learn how to monitor the performance of a deployed model.

### Task 1: Activate App Insights and data collection on the deployed model

1. Download the [**Model Telemetry.ipynb**](./notebooks/Model&#32;Telemetry.ipynb) notebook to your computer, by selecting the **Raw** view in GitHub, and then **right-click + Save as**. Please ensure that the extension of the saved file is `.ipynb`. This is the notebook you will step through executing in this exercise.

2. In the Azure Machine Learning Studio, navigate to **Notebooks**, and select **Upload files** option in the top menu.

3. Browse your local computer for the downloaded notebook, **Model Telemetry.ipynb** and then select **MCW-MLOps** folder as the target folder. Select **Upload**.

4. On the top bar, select the **mlops-compute** compute instance to use to run the notebook. Select the **Edit** drop down, and then select **Edit in Jupyter**. The new browser window will be opened.

5. Follow the instructions within the notebook to complete the task. When finished, your deployed model has now both [Application Insights](https://docs.microsoft.com/en-us/azure/azure-monitor/app/app-insights-overview) integration and data collection activated.

6. Note that if there are errors (for example, **Too many requests for service compliance-classifier-service (overloaded)** or **No ready replicas for service compliance-classifier-service.**) when you make calls against the deployed web service after your enable app insights (last cell in the **Model Telemetry** notebook), you should wait for 5 minutes and rerun the cell to make the calls.

### Task 2: Check Application Insights telemetry

1. Navigate to the [Azure portal](https://portal.azure.com) and locate the resource group you created for this lab (the one where the Azure Machine Learning service workspace was created in).

2. Locate the Application Insights instance for your AKS deployment in the resource group and select it.

    ![The Application Insights resource is selected in the resource group.](media/model-telemetry-01.png 'Resource Group Overview')

3. Go to **Overview**.

4. From the top row of the right section, select **Logs**. This will open the Application Insights query editor with an empty new query. Dismiss popups if displayed.

    ![On the Application Insights resource screen, the Overview item is selected in the left menu, and the Logs item is selected from the top toolbar.](media/model-telemetry-02.png 'Application Insights - Dashboard')

5. In the left pane, make sure the **Tables** tab is selected.

6. Provide the following query `requests | where timestamp > ago(24h) | limit 10` and then select **Run**.

    ![On the Application Insights Logs screen, a New Query tab is shown with the Tables tab selected. The icon next to the requests table is selected.](media/model-telemetry-03.png 'Create Requests Query')

7. Look at the results displayed. Application Insights is tracing all requests made to your model. Sometimes, a couple of minutes are needed for the telemetry information to propagate. If there are no results displayed, wait a minute. Call your model wait a minute and select **Run** to re-execute the Application Insights query.

   ![On the Application Insights Logs screen, a query is run against the requests table and a list of results is displayed.](media/model-telemetry-04.png 'Requests Query Results')

    > **Note**: If you do not see telemetry information after selecting **Run** to re-execute the Application insights query. Please rerun the last cell in the **Model Telemetry** notebook a few more times to generate more data. Then select **Run** on this page to re-execute the Application insights query.

    > **Note**: If you continue to see a message `No results found from the specified time range` you are most likely in the application insights for your ACI deployment. You need to select the other application insights resource.

### Task 3: Check the data collected

1. Navigate to the [Azure portal](https://portal.azure.com) and locate the resource group you created for this lab (the one where the Azure Machine Learning service workspace was created in).

2. Locate the Storage Account instance in the resource group and select it.

    ![The Storage account resource is selected in the resource group.](media/model-telemetry-05.png 'Resource Group Overview')

3. Go to **Storage browser (preview)**.

4. Expand the **BLOB CONTAINERS** section and identify the **modeldata** container. Select **More->Refresh** if you do not see **modeldata** container.

    ![In the Storage Explorer screen, the BLOB CONTAINERS item is expanded with the modeldata item selected.](media/model-telemetry-06.png 'Storage Explorer')

5. Identify the CSV files containing the collected data. The path to the output blobs is based on the following structure:

    **modeldata -> subscriptionid -> resourcegroup -> workspace -> webservice -> model -> version -> ... -> inputs -> year -> month -> day -> inputs.csv**

    ![In the Storage Explorer, the modeldata container is selected beneath BLOB containers. The inputs.csv file is selected in the list of files at the path specified above.](media/model-telemetry-07.png 'Storage Explorer - inputs.csv')



## After the hands-on lab

To avoid unexpected charges, it is recommended that you clean up all of your lab resources when you complete the lab.

### Task 1: Clean up lab resources

1. Navigate to the Azure Portal and locate the resource group you created for this lab (the one where the Azure Machine Learning service workspace was created in).

2. Select **Delete resource group** from the command bar.

    ![Screenshot of the Delete resource group button.](media/cleanup-01.png 'Delete resource group button')

3. In the confirmation dialog that appears, enter the name of the resource group and select **Delete**.

4. Wait for the confirmation that the Resource Group has been successfully deleted. If you don't wait, and the delete fails for some reason, you may be left with resources running that were not expected. You can monitor using the Notifications dialog, which is accessible from the Alarm icon.

    ![The Notifications dialog box has a message stating that the resource group is being deleted.](media/cleanup-02.png 'Delete Resource Group Notification Dialog')

5. When the Notification indicates success, the cleanup is complete.

    ![The Notifications dialog box has a message stating that the resource group has been deleted.](media/cleanup-03.png 'Delete Resource Group Notification Dialog')

You should follow all steps provided _after_ attending the Hands-on lab.
