# AWS Systems Manager Patch Manager
Demo on how to configure systems manager patch manager for Windows and Linux hosts

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
![alt text](https://github.com/ecedeno83/patch_manager/blob/master/images/modifypatchgroups.png  "MJodify Patch Group")

### Step 5: Create a Maintenance Window
The Maintenance Windows feature defines schedules when you are performing potentially disruptive actions, for example Operation System patching, updating drivers, or installing software. Inside the maintenance window, you register targets, which are EC2 instances to be patched, and register a patching tasks to be run.

To create a maintenance window

In the Systems Manager console, under <b>Actions</b>, choose <b>Maintenance Windows</b>, then click <b>Create maintenance window.</b>

On the <b>Create maintenance window</b> page, fill in the name of the maintenance window and uncheck the box for allow unregistered target.

Select a schedule to run the tasks. The format of the schedule can be specified in <a href='http://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-maintenance-cron.html'>CRON expression</a> or you can use the prepared schedule builder to construct a schedule. In this example, I set up a fast feedback loop for demonstration. For <b>CRON/Rate expression</b>, enter rate(15 minutes). For <b>Duration</b>, enter 1 For <b>Stop initiating tasks</b>, enter 0 for the period before the end of the maintenance window when the system should stop scheduling new tasks to run.

After filling in the required fields, choose <b>Create maintenance window</b> and return to the maintenance window list.
