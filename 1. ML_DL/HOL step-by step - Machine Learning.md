# Machine Learning hands-on lab step-by-step

## Solution architecture

The following diagram summarizes the key components and processing steps in the lab.

![Vehicle battery telemetry is ingested by an IoT Hub or Event Hub. This data is stored in long term storage, Azure Storage. This data is used by Azure Databricks to train the model that is managed and registered via an Azure Machine Learning workspace. AutoML is also another option that can be used to register a machine learning model. These models are then used for stream data processing and batch data processing in Azure Databricks.](images/lab-architecture.png 'Solution Architecture')



## Exercise 1: Data exploration and preprocessing

### Task 1: Load, explore and prepare the dataset using an Azure Databricks notebook

1. Browse to your Azure Databricks Workspace and navigate to `AI with Databricks and AML \ 1.0 Data Preparation`. This is the notebook you will step through executing in this exercise.

2. When you execute a notebook, you will need to attach it to a cluster. You can specify that the **lab** cluster should be used in the upper left-hand corner of the notebook. 

    ![Attaching the lab cluster to the notebook for execution.](images/attach-cluster-to-notebook.png "Attaching lab cluster to notebook")

3. Follow the instructions within the notebook to complete the exercise. Press Shift+Enter to execute a cell.



## Exercise 2: Creating a forecast model using automated machine learning

### Task 1: Create an automated machine learning experiment

1. Navigate to your Azure Machine Learning workspace in the Azure Portal. Select **Try the new Azure Machine Learning studio, Launch now**.

    ![The Azure Machine Learning workspace is displayed. The Launch now button is selected on the Overview screen.](images/automl-open-studio.png 'Open Azure Machine Learning studio')

    > **Note**: Alternatively, you can sign-in directly to the [Azure Machine Learning studio portal](https://ml.azure.com).

2. Select **Automated ML icon** in the left navigation bar.

    ![The Automated ML menu item is highlighted in the left menu in the Azure Machine Learning studio.](images/automl-open.png 'Open Automated ML section')

3. Select **+ New automated ML run**.

    ![In the Automated machine learning section in Azure Machine Learning studio. The "New automated ML run" button is selected.](./images/automl-new-run.png 'Create new automated ML run')

4. Select the `daily-battery-time-series` dataset from the list of registered datasets and then select **Next**. (This dataset was registered as a final step of the previous exercise, from the Azure Databricks notebook.)

     ![In the Create a new Automated ML run dialog, select the daily-battery-time-series dataset from the dataset list. The Next button is highlighted.](images/automl-create-dataset-01.png 'Select registered dataset')

5. Review the dataset details in the `Configure run` section, by selecting the **View dataset** link next to the dataset name.

    ![The Configure run screen shows the option to review the selected dataset structure. Select the view dataset link next to the dataset name.](images/automl-create-dataset-02.png 'Confirm and create the dataset')

6. In the `Dataset preview` dialog select **Close** and return to the `Configure run` dialog.

    ![Dataset preview dialog showing the preview of the training dataset.](images/automl-data-preview.png 'Dataset preview dialog')

7. Provide the experiment name: `Battery-Cycles-Forecast` and select **Daily_Cycles_Used** as target column. Select **Create a new compute**.

    ![In the Configure run form is populated with the above values. The Create a new compute button is highlighted.](images/automl-create-experiment.png 'Create New Experiment details')

8. For the new compute, provide the following values and then select **Next**:

    - **Virtual machine priority**: `Dedicated`

    - **Virtual machine type**: `CPU`

    - **Virtual machine size**: `Select from recommended options` --> `Standard_DS3_v2`

    ![The Create compute cluster form is populated with the above values. The Next button is selected at the bottom of the form.](images/create-compute-1.png "Setting preliminary information for a new compute cluster")

9. On the next page, provide the following parameters for your compute cluster. Then, select **Create**.

    - **Compute name**: `auto-ml-compute`

    - **Minimum number of nodes**: `1`

    - **Maximum number of nodes**: `1`

    - **Idle seconds before scale down**: `120`

    ![Setting compute name, minimum number of nodes, maximum number of nodes, and idle seconds before scale down.](images/create-compute-2.png "Finalizing compute parameters")

    > **Note**: The creation of the new compute may take several minutes. Once the process is completed, select **Next** in the `Configure run` section.

10.  Select the `Time series forecasting` task type and provide the following values and then select **View additional configuration settings**:

    - **Time column**: `Date`

    - **Time series identifier(s)**: `Battery_ID`

    - **Forecast horizon**: `30`

    ![The Select task type form is populated with the values outlined above. The View additional configuration settings link is highlighted.](images/automl-configure-task-01.png 'Configure time series forecasting task')

11. For the automated machine learning run additional configurations, provide the following values and then select **Save**:

    - **Primary metric**: `Normalized root mean squared error`

    - **Explain best model**: Selected

    - **Training job time (hours)** (in the `Exit criterion` section): enter `1` as this is the lowest value currently accepted.

    - **Metric score threshold**: enter `0.1355`. When this threshold value will be reached for an iteration metric the training job will terminate.

    ![The Additional configurations form is populated with the values defined above. The Save button is highlighted at the bottom of the form.](images/automl-configure-task-02.png 'Configure automated machine learning run additional configurations')

    > **Note**: We are setting a metric score threshold to limit the training time. In practice, for initial experiments, you will typically only set the training job time to allow AutoML to discover the best algorithm to use for your specific data.

12. Select **Finish** to start the new automated machine learning run.

    > **Note**: The experiment should run for up to 10 minutes. If the run time exceeds 15 minutes, cancel the run and start a new one (steps 3, 9, 10). Make sure you provide a higher value for `Metric score threshold` in step 10.

### Task 2: Review the experiment run results

1. Once the experiment completes, select `Details` to examine the details of the run containing information about the best model and the run summary.

   ![The Run Detail screen of Run 1 indicates it has completed. The Details tab is selected where the the best model, ProphetModel, is indicated along with the run summary.](images/automl-review-run-01.png 'Run details - best model and summary')

2. Select `Models` to see a table view of different iterations and the `Normalized root mean squared error` score for each iteration. Note that the normalized root mean square error measures the error between the predicted value and actual value. In this case, the model with the lowest normalized root mean square error is the best model. Note that Azure Machine Learning Python SDK updates over time and gives you the best performing model at the time you run the experiment. Thus, it is possible that the best model you observe can be different than the one shown below.

    ![The Run Detail screen of Run 1 is displayed with the Models tab selected. A table of algorithms is displayed with the values for Normalized root mean squared error highlighted.](images/automl-review-run-02.png 'Run Details - Models with their associated primary metric values')

3. Return to the details of your experiment run and select the best model **Algorithm name**.

    ![The Run Detail screen of Run 1 is displayed with the Details tab selected. The best model algorithm name is selected.](images/automl-review-run-03.png 'Run details - recommended model and summary')

4. From the `Model` tab, select **View all other metrics** to review the various `Run Metrics` to evaluate the model performance.

    ![The model details page displays run metrics associated with the Run.](images/automl-review-run-04.png 'Model details - Run Metrics')

5. Next, select **Metrics, predicted_true** to review the model performance curve: `Predicted vs True`.

    ![The model run page is shown with the Metrics tab selected. A chart is displayed showing the Predicted vs True curve.](images/automl-review-run-05.png 'Predicted vs True curve')

    > **Note**: You may need to deselect the other metrics.

### Task 3: Deploy the Best Model

1. From the top toolbar select **Deploy**.

    ![From the toolbar the Deploy button is selected.](images/automl-deploy-best-model-01.png 'Deploy best model')

2. Provide the `Name`, `Description` and `Compute type`, and then select **Deploy**:

    - **Name**: **battery-cycles**

    - **Description**: **The best AutoML model to predict battery cycles.**

    - **Compute type**: Select `ACI`.

    ![The Deploy a model dialog is populated with the values listed above. The Deploy button is selected at the bottom of the form.](images/automl-deploy-best-model-02.png 'Deploy the best model')

3. The model deployment process will register the model, create the deployment image, and deploy it as a scoring webservice in an Azure Container Instance (ACI). To view the deployed model, from Azure Machine Learning studio select **Endpoints icon, Real-time endpoints**.

   ![In the left menu, the Endpoints icon is selected. On the Endpoints screen, the Rea-time endpoints tab is selected and a table is displayed showing the battery-cycles endpoint highlighted.](images/automl-deploy-best-model-03.png 'Deployed model endpoints')

   > **Note**: The `battery-cycles` endpoint will show up in a matter of seconds, but the actual deployment takes several minutes. You can check the deployment state of the endpoint by selecting it and then selecting the `Details` tab. A successful deployment will have a state of `Healthy`.

4. If you see your model deployed in the above list, you are now ready to continue on to the next exercise.
   
### Task 4: Perform batch scoring in Azure DataBricks

1. Browse to your Azure Databricks Workspace and navigate to `AI with Databricks and AML \ 2.0 Batch Scoring for Timeseries`. This is the notebook you will step through executing in this exercise. Again, remember that you may need to reconnect to the **lab** cluster.

2. Follow the instructions within the notebook to complete the exercise.



## Exercise 3: Creating a deep learning model (RNN) for time series data

### Task 1: Create the deep learning model and start a streaming job using a notebook

1. Browse to your Azure Databricks Workspace and navigate to `AI with Databricks and AML \ 3.0 Deep Learning with Time Series`. This is the notebook you will step through executing in this exercise.

2. Follow the instructions within the notebook to complete the exercise.



## After the hands-on lab

### Task 1: Clean up lab resources

1. Navigate to the Azure Portal and locate the `MCW-Machine-Learning` Resource Group you created for this lab.

2. Select **Delete resource group** from the command bar.

    ![The Delete resource group button.](images/cleanup-delete-resource-group.png 'Delete resource group button')

3. In the confirmation dialog that appears, enter the name of the resource group and select **Delete**.

4. Wait for the confirmation that the Resource Group has been successfully deleted. If you don't wait, and the delete fails for some reason, you may be left with resources running that were not expected. You can monitor using the Notifications dialog, which is accessible from the Alarm icon.

    ![The Notifications dialog box has a message stating that the resource group is being deleted.](images/cleanup-delete-resource-group-notification-01.png 'Notifications dialog box')

5. When the Notification indicates success, the cleanup is complete.

    ![The Notifications dialog box has a message stating that the resource group has been deleted.](images/cleanup-delete-resource-group-notification-02.png 'Notifications dialog box')

