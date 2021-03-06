# AWS Systems Manager Patch Manager

### Demo Ovewrview
EC2 <a href='https://docs.aws.amazon.com/systems-manager/latest/userguide/what-is-systems-manager.html'>Systems Manager</a> Patch Manager helps you automatically apply OS patches within customized maintenance windows, collect software inventory, and configure Windows and Linux operating systems. These capabilities enable automated configuration and ongoing management of systems at scale and help maintain software compliance for instances running in Amazon EC2 or on-premises.

#### In this Demo you will:
1. Launch new Windows instance.
2. Create a custom patch baseline.
3. Set the patch group for the custom patch baseline.
4. Create a maintenance window.
5. Register targets for the maintenance window.
6. Register a task for the maintenance window.
7. Verify the patch compliance report.

![alt text](https://github.com/ecedeno83/patch_manager/blob/master/images/diagram1.PNG  "Patch Manager Architecture")

#### Prerequisites
* Create an IAM role for EC2 instances
* Systems Manager requires an IAM role for EC2 instances that will process commands. Follow Task 2 in <a href='http://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-configuring-access-policies.html#sysman-configuring-access-role'>Configuring Access Using Systems Manager Managed Policies.</a>
* Create an IAM role for Maintenance Windows
* For Maintenance Windows to send commands to EC2 instances, you must configure an IAM role. Follow the steps in <a href='http://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-maintenance-permissions.html'>Configuring Access to Maintenance Windows.</a>
* Install SSM Agent on the EC2 instance(s) to be patched. See the <a href='https://docs.aws.amazon.com/systems-manager/latest/userguide/ssm-agent.html'> how to install and configure SSM agent</a> guide for additional information.
  - SSM Agent is installed, by default, on Windows Server 2016 instances and instances created from Windows Server 2003-2012 R2 AMIs published in November 2016 or later. <a href='https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-win.html'>View Details here.</a>
  - SSM Agent is installed, by default, on Amazon Linux base AMIs dated 2017.09 and later. SSM Agent is also installed, by default, on Amazon Linux 2, Ubuntu Server 16.04, and Ubuntu Server 18.04 LTS AMIs. <a href='https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-agent.html'>View details here</a>

### Step 1: Launch Windows Instance
![alt text](https://github.com/ecedeno83/patch_manager/blob/master/images/image.png  "Launch Instance")

### Step 2: Add Tags to EC2 instance
<b>Add Tags</b> - define EC2 tags as follows: 
* First, create a tag called Patch Group (required) and a value, such as Windows Server 2016 Base. A patch group is an optional means of organizing instances for patching. It defines which patch baseline should be used. If this tag is not defined, and you don’t specify your own default patch baseline, an AWS predefined patch baseline is used instead. For more information, see <a href='http://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-patch-working.html'>Working with Patch Manager.</a>

* Next, create a tag called Name, which is used later as a filter in the maintenance window configuration. Enter a value such as Patch Manager Demo.

![alt text](https://github.com/ecedeno83/patch_manager/blob/master/images/addtags.png  "Add Tags")

### Step 3: Create a custom patch baseline
A patch baseline defines which patches should and shouldn’t be installed on your instances. Systems Manager provides a predefined default patch baseline. You can use the predefined baseline or create your own default to meet your patch compliance requirements. For more information, see <a href='http://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-patch-working.html'>Working with Patch Manager.</a>

#### To create a custom baseline for Windows:
1. In the Systems Manager console, under Actions, choose Patch Manager, then click on Create Patch Baseline button in the upper right.

![alt text](https://github.com/ecedeno83/patch_manager/blob/master/images/patchbaseline.png  "Custom Patch Baseline")

2. In the Create Patch Baseline page, enter values for the patch filters as follows: 
* For Product, enter WindowsServer2016.
* For Classification, enter SecurityUpdates.
* For Severity, enter both Critical and Important.
* For Auto Approval Delay, enter 3 days.
3. Choose Create Patch Baseline.
![alt text](https://github.com/ecedeno83/patch_manager/blob/master/images/editbaseline.png  "Edit Baseline")

#### To create a custom baseline for Linux:

1. Select your Operating System and Product from the drop down menus, along with Approval Rules just as you did for the Windows patch baseline.
![alt text](https://github.com/ecedeno83/patch_manager/blob/master/images/lx_patchbaseline.png  "Patch Baseline")

2. Add comma separated list of approved or rejected patches, to ensure path manager either only installs patches certified by your organization, or explicitly blocks patches that you do not want installed.  For this demo we will leave these values blank.

Choose your Patch sources based on the OS and Product selected in prior steps.  Then click on Create patch baseline.
![alt text](https://github.com/ecedeno83/patch_manager/blob/master/images/patchsources.png  "Patch Source")

### Step 4: Set the patch group for custom patch baseline
<b>To modify the patch groups for this custom baseline policy</b>
Click on the radio button for the Custom Patch Baseline created in the prior step, then click on Actions menu and select “Modify patch groups”.

On the Modify patch groups page add the previously defined tag <b>Windows Server 2016 Base</b>, then click close.
![alt text](https://github.com/ecedeno83/patch_manager/blob/master/images/modifypatchgroups.png  "Modify Patch Group")

### Step 5: Create a Maintenance Window
The Maintenance Windows feature defines schedules when you are performing potentially disruptive actions, for example Operation System patching, updating drivers, or installing software. Inside the maintenance window, you register targets, which are EC2 instances to be patched, and register a patching tasks to be run.

To create a maintenance window

In the Systems Manager console, under <b>Actions</b>, choose <b>Maintenance Windows</b>, then click <b>Create maintenance window.</b>

On the <b>Create maintenance window</b> page, fill in the name of the maintenance window and uncheck the box for allow unregistered target.

Select a schedule to run the tasks. The format of the schedule can be specified in <a href='http://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-maintenance-cron.html'>CRON expression</a> or you can use the prepared schedule builder to construct a schedule. In this example, I set up a fast feedback loop for demonstration. For <b>CRON/Rate expression</b>, enter rate(5 minutes). For <b>Duration</b>, enter 1 For <b>Stop initiating tasks</b>, enter 0 for the period before the end of the maintenance window when the system should stop scheduling new tasks to run.

After filling in the required fields, choose <b>Create maintenance window</b> and return to the maintenance window list.
![alt text](https://github.com/ecedeno83/patch_manager/blob/master/images/maintwindow.png  "Maintenance Window")

### Step 6: Register targets for Maintenance Window
After creating the maintenance window, associate the EC2 instances that will be affected inside the schedule.

<b>To register a target for the maintenance window</b>
1. Choose <b>Actions</b>, <b>Register targets</b> for the maintenance window created in the previous step.
2. On the Register targets page, for <b>Tag Filters</b>, use the <b>Patch Group</b> tag and <b>Windows Server 2016 Base</b> value created earlier for the EC2 instance.
3. Choose Register targets.
![alt text](https://github.com/ecedeno83/patch_manager/blob/master/images/targets.png  "Register Targets")

### Step 7: Register a task for the Maintenance Window
After the target is registered, register a patch task for the maintenance window.

<b>To register a task</b>
1. Choose <b>Actions</b>, <b>Register Run command task</b> for the maintenance window created earlier.
2. In the <b>Register Task</b> page, for <b>Document</b>, select <b>AWS-RunPatchBaseline</b>
3. For <b>Task Priority</b>, specify a priority for this task. Digit 1 is the highest priority. Tasks in a maintenance window are scheduled in priority order. Tasks that have the same priority are scheduled in parallel.
4. For <b>Targets</b>, select the target created in the previous step.
5. For <b>Rate Control</b>, you can specify how many instances ca be patched in parallel, either by percentage or number of targets and how many errors are allowed before the patching operation is stopped.  For this demo lets choose 100 for percentage and enter 1 for Error threshold field, which will stop the task from proceeding on other instances if 1 error is encountered.
6. Select the <b>IAM service role</b> that allows Maintenance Windows to interact with other AWS services on your behalf.  Either use the <b>AWSServiceRoleForAmazonSSM</b> or use a custom you have created that includes the appropriate policies.
7. For <b>Parameters</b>, choose <b>Install</b> to instruct the task to install the patch onto the instances. Or, you can choose Scan to instruct the task to perform read-only operations.
8. Click on <b?Register Run command task</b>
![alt text](https://github.com/ecedeno83/patch_manager/blob/master/images/mainttask.png  "Maintenance Tasks")

![alt text](https://github.com/ecedeno83/patch_manager/blob/master/images/taskdocument.png  "Task Document")

![alt text](https://github.com/ecedeno83/patch_manager/blob/master/images/iamrole.png  "IAM Role")
![alt text](https://github.com/ecedeno83/patch_manager/blob/master/images/taskparams.png  "Task Parameters")

### Step 8: Verify the patch compliance report
After completing Step 7, the Patch Manager is all set.  The patches are applied to the instances during the next scheduled maintenance window (every 5 minutes for this demo).

After the initial maintenance task is completed, you should see the result of the tasks in the Maintenance Window History
![alt text](https://github.com/ecedeno83/patch_manager/blob/master/images/compreport.png)

<b>To see the patch compliance report</b>

1. In the Systems Manager console, click on <b>Compliance</b>, under Insights section on the left-hand side.
2. Review the Compliance resources summary for a dashboard view of patching across instances
![alt text](https://github.com/ecedeno83/patch_manager/blob/master/images/compreport2.png)

You can also click on the Resource ID radio button to display Compliance rules and patch status for individual instances
![alt text](https://github.com/ecedeno83/patch_manager/blob/master/images/compreport3.png)

By clicking on an individual instance ID, you can drill down to see specific patches installed. 
![alt text](https://github.com/ecedeno83/patch_manager/blob/master/images/compreport4.png)
![alt text](https://github.com/ecedeno83/patch_manager/blob/master/images/compreport5.png)

---

# Common Questions for this Demo
#### <a href='https://aws.amazon.com/systems-manager/faq/#Patch_Manager'>Click here to see the full AWS Patch Manager FAQ's</a>

<b>Q: Do EC2 instances need public internet access to communicate with Systems Manager service?</b><br>
A: No. VPC interface endpoints can be created to keep communication private within your VPC between EC2 instances and Systems Manager service. Communication between EC2 instances and Systems Manager does not need to traverse the public internet.
VPC endpoints need to be created for the following services:<br>
<b>com.amazonaws.region.ssm</b> - The endpoint for the Systems Manager service.<br>
<b>com.amazonaws.region.ec2messages</b> -  Systems Manager uses this endpoint to make calls from SSM Agent to SSM service.<br>
<b>com.amazonaws.region.ec2</b> – Use this endpoint if you are using Systems Manager to create VSS-enabled snapshots.<br>
You will also need to create a gateway endpoint for Amazon S3. Systems Manager uses this endpoint to upload Amazon S3 output logs, and to update SSM Agent.

<b>Q: Can I configure and use my own private patch repository for patch manager?</b><br>
A: Yes. You can modify the EC2 instance repo configuration files to point to your own private repo URL's. For example,  on a RHEL instance you can modify the /etc/yum.repos.d/repository.repo file and change the baseurl to your private repo endpoint.  Many customers use WSUS (Windows) and Red Hat Satellite (Linux) to manage their private repositories.

<b>Q: Are there any differences between Windows and Linux Patching?</b><br>
A: The following table describes important differences between Linux and Windows patching.

| Difference | Details |
| --- | --- |
| Patch evaluation | For Linux patching, Systems Manager evaluates patch baseline rules and the list of approved and rejected patches on each managed instance. Systems Manager must evaluate patching on each instance because the service retrieves the list of known patches and updates from the repositories that are configured on the instance.  Patch Manager uses a different process to evaluate which patches should be present on Windows managed instances versus Linux managed instance. For Windows patching, Systems Manager evaluates patch baseline rules and the list of approved and rejected patches directly in the service. It can do this because Windows patches are pulled from a single repository (Windows Update).
| Not Applicable patches | Due to the large number of available packages for Linux operating systems, Systems Manager does not report details about patches in the Not Applicable state. A Not Applicable patch is, for example, a patch for Apache software when the instance does not have Apache installed. Systems Manager does report the number of Not Applicable patches in the summary, but if you call the DescribeInstancePatches API for an instance, the returned data does not include patches with a state of Not Applicable. This behavior is different from Windows. |

<b>Q: What happens if a patch installation fails?</b><br>
A: When registering a task for the patch manager maintenance window, you specify the number of errors allowed before stopping the task. If you specify 1, the task will stop after encountering the first error, allowing you to troubleshoot and remediate any issues prior to continuing with upgrades on your fleet of instances.

<b>Q: How much does Systems Manager cost?</b><br>
A: Systems Manager features and shared components are offered at <b>no additional cost</b>. You pay only for the AWS resources that you use.
