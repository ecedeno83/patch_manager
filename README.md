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
* Systems Manager requires an IAM role for EC2 instances that will process commands. Follow Task 2 in Configuring Access Using Systems Manager Managed Policies.
* Create an IAM role for Maintenance Windows
* For Maintenance Windows to send commands to EC2 instances, you must configure an IAM role. Follow the steps in Configuring Access to Maintenance Windows.

### Step 1: Launch Windows Instance
![alt text](https://github.com/ecedeno83/patch_manager/blob/master/images/image.png  "Launch Instance")

### Step 2: Add Tags to EC2 instance
<b>Add Tags</b> - define EC2 tags as follows: 
* First, create a tag called Patch Group (required) and a value, such as Windows Server 2016 Base. A patch group is an optional means of organizing instances for patching. It defines which patch baseline should be used. If this tag is not defined, and you don’t specify your own default patch baseline, an AWS predefined patch baseline is used instead. For more information, see Working with Patch Manager.

* Next, create a tag called Name, which is used later as a filter in the maintenance window configuration. Enter a value such as Patch Manager Demo.
![alt text](https://github.com/ecedeno83/patch_manager/blob/master/images/addtags.png  "Add Tags")

