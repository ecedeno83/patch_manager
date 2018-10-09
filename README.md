# AWS Systems Manager Patch Manager

### Demo Ovewrview
EC2 Systems Manager Patch Manager helps you automatically apply OS patches within customized maintenance windows, collect software inventory, and configure Windows and Linux operating systems. These capabilities enable automated configuration and ongoing management of systems at scale and help maintain software compliance for instances running in Amazon EC2 or on-premises.

#### In this Demo you will:
1. Launch new Windows instance.
2. Create a custom patch baseline.
3. Set the patch group for the custom patch baseline.
4. Create a maintenance window.
5. Register targets for the maintenance window.
6. Register a task for the maintenance window.
7. Verify the patch compliance report.

#### Prerequisites
Make sure you have the following roles created before starting the walkthrough:
* Create an IAM role for EC2 instances
* Systems Manager requires an IAM role for EC2 instances that will process commands. Follow Task 2 in <a href='http://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-configuring-access-policies.html#sysman-configuring-access-role'>Configuring Access Using Systems Manager Managed Policies.</a>
* Create an IAM role for Maintenance Windows
* For Maintenance Windows to send commands to EC2 instances, you must configure an IAM role. Follow the steps in <a href='http://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-maintenance-permissions.html'>Configuring Access to Maintenance Windows.</a>

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

Select a schedule to run the tasks. The format of the schedule can be specified in <a href='http://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-maintenance-cron.html'>CRON expression</a> or you can use the prepared schedule builder to construct a schedule. In this example, I set up a fast feedback loop for demonstration. For <b>CRON/Rate expression</b>, enter rate(15 minutes). For <b>Duration</b>, enter 1 For <b>Stop initiating tasks</b>, enter 0 for the period before the end of the maintenance window when the system should stop scheduling new tasks to run.

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
7. For Parameters, choose Install to instruct the task to install the patch onto the instances. Or, you can choose Scan to instruct the task to perform read-only operations.
8. Click on Register Run command task
