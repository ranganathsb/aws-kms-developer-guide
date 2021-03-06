# Keeping Key Policies Up to Date<a name="key-policy-upgrading"></a>

When you use the AWS Management Console to create a customer master key \(CMK\), you can choose the IAM users, IAM roles, and AWS accounts that you want to have access to the CMK\. These users, roles, and accounts are added to a default key policy that controls access to the CMK\. Occasionally, the default key policy for new CMKs is updated\. Typically, these updates correspond to new AWS KMS features\.

When you create a new CMK, the latest version of the default key policy is added to the CMK\. However, existing CMKs continue to use their existing key policy—that is, new versions of the default key policy are *not* automatically applied to existing CMKs\. Instead, the console alerts you that a newer version is available and prompts you to upgrade it\.

**Note**  
The console alerts you only when you are using the default key policy that was applied when you created the CMK\. If you manually modified the key policy document using the console's *policy view* or the [PutKeyPolicy](http://docs.aws.amazon.com/kms/latest/APIReference/API_PutKeyPolicy.html) API operation, the console does not alert you when new permissions are available\.

For information about the permissions that are added to a key policy when you upgrade it, see [Changes to the Default Key Policy](#key-policy-changes)\. Upgrading to the latest version of the key policy should not cause problems because it only adds permissions; it doesn't remove any\. We recommend keeping your key policies up to date unless you have a specific reason not to\.

**To determine if a newer version of the default key policy is available**

1. Sign in to the AWS Management Console and open the AWS Identity and Access Management \(IAM\) console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the left navigation pane, choose **Encryption keys**\.

1. For **Region**, choose the appropriate AWS Region\. Do not use the region selector in the navigation bar \(top right corner\)\.

1. Choose the alias of the CMK whose key policy you want to see\. When a newer version of the default key policy is available, the console displays the following alert\.  
![\[Key policy upgrade alert on the console's key details page\]](http://docs.aws.amazon.com/kms/latest/developerguide/images/console-key-policy-upgrade.png)

**To upgrade to the latest version of the default key policy**

1. When you see the preceding alert, choose **Preview and upgrade to the new key policy\.**

1. Review the key policy document for the latest version of the default key policy\. For more information about the difference between the latest version and previous versions, see [Changes to the Default Key Policy](#key-policy-changes)\. After reviewing the key policy, choose **Upgrade key policy**\.

## Changes to the Default Key Policy<a name="key-policy-changes"></a>

In the current version of the default key policy, the *key administrators statement* contains more permissions than those in previous versions\. These additional permissions correspond to new AWS KMS features\.

CMKs that are using an older version of the default key policy might be missing the following permissions\. When you upgrade to the latest version of the default key policy, they're added to the key administrators statement\.

**kms:TagResource and kms:UntagResource**  
These permissions allow key administrators to add, update, and remove tags from the CMK\. They were added to the default key policy when AWS KMS released the tagging feature\.

**kms:ScheduleKeyDeletion and kms:CancelKeyDeletion**  
These permissions allow key administrators to schedule and cancel deletion for the CMK\. They were added to the default key policy when AWS KMS released the CMK deletion feature\.  
The `kms:ScheduleKeyDeletion` and `kms:CancelKeyDeletion` permissions are included by default when you create a CMK and when you upgrade to the latest version of the default key policy\. However, you can optionally remove them from the default key policy when you create a CMK by clearing the box for **Allow key administrators to delete this key**\. In the same way, you can use the key details page to remove them from the default key policy for existing CMKs\. That includes CMKs whose key policy you upgraded to the latest version\.