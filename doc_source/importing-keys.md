# Importing Key Material in AWS Key Management Service \(AWS KMS\)<a name="importing-keys"></a>

A customer master key \(CMK\) is a logical representation of a master key in AWS KMS\. In addition to the master key's identifiers and other metadata including its creation date, description, and key state, a CMK contains the *key material* used to encrypt and decrypt data\. When you create a CMK, by default AWS KMS generates the key material for that CMK\. But you can choose to create a CMK without key material and then import your own key material into that CMK\.

When you use imported key material, you remain responsible for the key material while allowing AWS KMS to use a copy of it\. You might choose to do this for one or more of the following reasons:

+ To prove that you generated the key material using a source of randomness that meets your requirements\.

   

+ To use key material from your own infrastructure with AWS services, and to use AWS KMS to manage the lifecycle of that key material within AWS\.

   

+ To gain the ability to set an expiration time for the key material in AWS and to manually delete it, but to also make it available again in the future\. In contrast, scheduling key deletion requires a waiting period of 7 to 30 days, after which you cannot recover the deleted CMK\.

   

+ To own the original copy of the key material, and to keep it outside of AWS for additional durability and disaster recovery during the complete lifecycle of the key material\.

For information about important differences between CMKs with imported key material and those with key material generated by AWS KMS, see [Considerations for Imported Key Material](#importing-keys-considerations)\.

The key material you import must be a 256\-bit symmetric encryption key\.

**Topics**

+ [How To Import Key Material](#importing-keys-overview)

+ [Considerations for Imported Key Material](#importing-keys-considerations)

## How To Import Key Material<a name="importing-keys-overview"></a>

The following overview describes the process to import your key material into AWS KMS\. For more details about each step in the process, see the corresponding topic\.

1. Create a CMK with no key material – To get started with importing key material, first create a CMK whose *origin* is `EXTERNAL`\. This indicates that the key material was generated outside of AWS KMS and prevents AWS KMS from generating key material for the CMK\. In a later step you will import your own key material into this CMK\.

    

1. Download the public key and import token – After completing step 1, download a public key and an import token\. These items protect the import of your key material to AWS KMS\.

    

1. Encrypt the key material – Use the public key that you downloaded in step 2 to encrypt the key material that you created on your own system\.

    

1. Import the key material – Upload the encrypted key material that you created in step 3 and the import token that you downloaded in step 2\.

## Considerations for Imported Key Material<a name="importing-keys-considerations"></a>

Before you decide to import key material into AWS KMS, you should understand the following characteristics of imported key material\.

**Availability and durability**  
You remain responsible for the key material's overall availability and durability\. AWS KMS is designed to keep imported key material highly available, but the service does not maintain the durability of imported key material at the same level as key material generated on your behalf\. This difference is meaningful in the following cases:

+ When you set an expiration time for your imported key material, AWS KMS deletes the key material after it expires\. AWS KMS does not delete the CMK or its metadata\. You cannot set an expiration time for key material generated by AWS KMS\.

+ When you manually delete imported key material, AWS KMS deletes the key material but does not delete the CMK or its metadata\. In contrast, scheduling key deletion requires a waiting period of 7 to 30 days, after which AWS KMS deletes the key material and all of the CMK's metadata\.

+ In the unlikely event of certain regionwide failures that affect the service \(such as a total loss of power\), AWS KMS cannot automatically restore your imported key material\. However, AWS KMS can restore the CMK and its metadata\.

To make your key material available again after any of these events, you must retain a copy of the key material in a system that you control so that you can reimport it into the original CMK\.

**Secure key generation**  
You are responsible for generating the key material using a source of randomness that meets your security requirements\.

**One key per CMK**  
When you import key material into a CMK, the CMK is permanently associated with that key material\. You cannot import different key material into that CMK, and you cannot enable automatic key rotation for a CMK with imported key material\. However, you can manually rotate a CMK with imported key material\. To do so, create a new CMK and then import the new key material into that CMK\. Then change the CMK identifier in your applications or AWS service configurations to the key ID for the new CMK\. Also, if you have a KMS alias that points to the old CMK, you can update it to point to the new one to complete the rotation process\.

**Ciphertexts are not portable between CMKs**  
When you encrypt data under a KMS CMK, the ciphertext cannot be decrypted with any other CMK\. This applies to all KMS CMKs, and remains true even when you import the same key material into a different CMK\.