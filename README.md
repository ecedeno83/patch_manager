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
 [logo]: https://github.com/ecedeno83/patch_manager/images/image.png  "Launch Instance"
