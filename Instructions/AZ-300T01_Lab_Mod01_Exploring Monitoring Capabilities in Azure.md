# Managing Azure Subscriptions and Resources
# Lab: Exploring Monitoring Capabilities in Azure
  
### Scenario
  
Adatum Corporation wants to explore monitoring capabilities in Azure


### Objectives
  
After completing this lab, you will be able to:

-  Deploy Azure VM scale sets

-  Implement monitoring and alerting by using Azure Monitor

### Lab Setup
  
Estimated Time: 45 minutes

User Name: **Student**

Password: **Pa55w.rd**

# Exercise 1: Deploy Azure VM scale sets
  
The main tasks for this exercise are as follows:

1. Deploy an Azure VM scale set by using an Azure QuickStart template

1. Review autoscaling settings of the Azure VM scale set


#### Task 1: Deploy an Azure VM scale set by using an Azure QuickStart template

1. From the lab virtual machine, start Microsoft Edge and browse to the Azure portal at http://portal.azure.com and sign in by using the Microsoft account that has the Owner role in the target Azure subscription.

1. In the Azure portal, in the Microsoft Edge window, start a PowerShell session within the Cloud Shell.

1. If you are presented with the **You have no storage mounted** message, configure storage using the following settings:

    - Subsciption: the name of the target Azure subscription

    - Cloud Shell region: the name of the Azure region that is available in your subscription and which is closest to the lab location

    - Resource group: the name of a new resource group **az3000100-LabRG**

    - Storage account: a name of a new storage account

    - File share: a name of a new file share

1. From the Cloud Shell pane, run the following to identify a unique DNS domain name (substitute the placeholder `<custom-label>` with any alphanumeric string starting with a letter and no longer than 9 characters, which is likely to be unique and the placeholder `<location>` with the name of the Azure region into which you intend to deploy resources in this lab):

    ```
    Test-AzDnsAvailability -DomainNameLabel <custom-label> -Location '<location'>
    ```
    
1. Verify that the command returned **True**. If not, rerun the same command with a different value of the <custom-label> until the command returns **True**. 
  
1. Note the value of the <custom-label> that resulted in the successful outcome. You will need it in the next task.
  
1. From the lab virtual machine, start Microsoft Edge and browse to the Azure QuickStart template that deploys autoscale demo app on Ubuntu 16.04 at [**https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale**](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale).

1. Click **Deploy to Azure** and, when prompted, sign in by using the Microsoft account that has the Owner role in the target Azure subscription.
  
1. In the Azure Portal, on the **Deploy VM Scale Set with Python Bottle server & AutoScale** blade, specify the following settings and initiate the deployment:

    - Subscription: the name of the target Azure subscription

    - Resource group: the name of a new resource group **az3000101-LabRG**

    - Location: the name of the Azure region that you referenced when running `Test-AzDnsAvailability` earlier in this task

    - Vm Sku: **Standard_D1_v2**

    - Vmss Name: the custom label you identified when running `Test-AzDnsAvailability` earlier in this task

    - Instance count: **1**

    - Admin Username: **student**

    - Admin Password: **Pa55w.rd1234**
    
1. Wait for the deployment to complete. This will take about 5 minutes.


#### Task 2: Review autoscaling settings of the Azure VM scale set
  
1. In Azure Portal, navigate to the blade representing the newly deployed Azure VM scale set. 

1. From the VM scale set blade, navigate to the its **Scaling** blade.

1. Note that the Azure VM scale set is configured to scale dynamically based on a metric using the following criteria:

    - Scale out: increase instance count by 1 when average percentage of CPU > 60

    - Scale in: decrease instance count by 1 when average percentage of CPU < 30

    - Minimum number of instances: 1

    - Maximum number of instances: 10

1. Modify the maximum number of instances to 3.

> **Result**: After you completed this exercise, you have deployed an Azure VM scale set and reviewed its autoscaling settings.


## Exercise 2: Implementing monitoring and alerting by using Azure Monitor
  
The main tasks for this exercise are as follows:

1. Create Azure VM scale set metrics-based alerts

1. Configure Azure VM scale set autoscaling-based notifications

1. Test Azure VM scale set monitoring and alerting.


#### Task 1: Create Azure VM scale set metrics-based alerts
  
1. In the Azure portal, navigate to the **Monitor** blade and, from there, switch to the **Monitor - Metrics** blade.

1. On the **Monitor - Metrics** blade, use the filter to display **Avg Percentage CPU** metric of the VM scale set resource you provisioned in the previous exercise of this lab.

1. Review the resulting chart and note the average percentage CPU within the last few minutes.

1. Navigate to the **Monitor - Alerts** blade. 

1. From the **Monitor - Alerts** blade, navigate to the **Create rule** blade. 

1. From the **Create rule** blade, navigate to the **Select a resource** blade and select the  VM scale set you provisioned in the previous exercise of this lab.

1. From the **Create rule** blade, navigate to the **Configure signal logic** blade, select the **Percentage CPU** metric, leave the dimension settings and condition type with their default values, set the condition to **Greater than**, set the time aggregation to **Average**, set the threshold to **60**, set the period (grain) to **Over the last 1 minutes**, and set the frequency to **Every 1 minute**.

1. From the **Create rule** blade, navigate to the **Add action group** blade, set the action group name to **az30001 action group**, set short name: **az30001**, select the Azure subscription you used in the previous exercise, accept the default name of the resource group of **Default-ActivityLogAlerts (to be created)**, set the action name: **az30001-email**, and set the action type to **Email/SMS/Push/Voice**. 

1. On the **Email/SMS/Push/Voice** blade, set an email address, a mobile phone number, or a phone number that you want to use to receive alerts generated by this rule. 

1. Back on the **Create rule** blade, set the alert rule name to **Percentage CPU of the VM scale set is greater than 60 percent**, its description to **Percentage CPU of the VM scale set is greater than 60 percent**, its severity to **Sev 3**, and set enable rule upon creation to **Yes**.
    
    > **Note**: It can take up to 10 minutes for a metric alert rule to become active 

#### Task 2: Configure Azure VM scale set autoscaling-based notifications
  
1. In the Azure portal, navigate to the **Monitor - Autoscale** blade. 

1. In the list of resources capable of autoscaling, click the VM scale set you provisioned in the previous exerrcise of this lab. 

1. On the **Autoscale setting** blade, click the **Notify** tab heading, configure the following settings, and save your changes:

    - Email administrators: enabled

    - Email co-administrators: disabled

    - Additional administrator emails(s): add an email address that you want to use to receive notifications about autoscaling events


#### Task 3: Test Azure VM scale set monitoring and alerting.
  
1. In the Azure portal, navigate to the blade representing the Azure VM scale set you deployed in the previous exercise of this lab.

1. From the VM scale set blade, identify the value of the **Public IP address** assigned to the front end of the load balancer associated with the VM scale set.

1. From the lab computer, start Microsoft Edge and browse to **http://*Public IP address*:9000** (where ***Public IP address*** is the IP address you identified in the previous step)

1. On the **Worker interface** page, click the **Start work** link.

1. Use the **CPU (average)** chart on the VM scale set blade to monitor changes to the CPU utilization.

    > **Note**: Alternatively, you can navigate back to the **Monitor - Metrics** blade and use the filter to display **Avg Percentage CPU** metric of the VM scale set resource.

    > **Note**: You should receive an alert regarding increased CPU utilization within a couple of minutes

1. Switch to the **Instances** blade of the VM scale set in order to identify the number of its instances.

    > **Note**: Alternatively, you can navigate back to the **Monitor - Autoscale** blade, in the list of resources capable of autoscaling, click the name of the VM scale set, on the **Autoscale settings** blade, click **Run history**, and then review the list of autoscale events.

    > **Note**: Autoscaling should be triggered within a couple of minutes. 

1.  Switch to the Microsoft Edge window displaying worker instances page and click the **Stop work** link.

1.  Monitor decrease in CPU utilization and scaling in events using the same methods that you used when scaling out the VM scale set.

> **Result**: After you completed this exercise, you have implemented and tested monitoring and alerting by using Azure Monitor.
