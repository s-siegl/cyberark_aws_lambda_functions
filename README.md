# AWS-Lambda-For-CyberArk
Implementation of AWS Lambda functions in order to provision/deprovision Windows and Linux Admin account dynamically in/from CyberArk.
The provisionning function creates a safe and position a Vault user or group as owner according to AWS machine configured tags. The account is onboarded with a 'default' key retrieved from the Vault. The 'default' key is used as SSH access key for linux machines and as password encryption key for windows machines. An immediate rotation is scheduled once the new account is onboarded. 
The deprovisonng function deletes all entries in CyberArk that contains the InstanceID of the terminated machine. 

Requirements
------------

- Execute lambda function with AWS role that have access to instances information and credentials.
- Having access to Vault REST API and Central Credential Provider
- Declare an environment variable named 'PrivateKey' that contains private key string in pem format (example: PrivateKey: -----BEGIN RSA PRIVATE KEY-----\nMIIEogIBAAKCAQEAszEY23vmmzhu6fK3BC3LoDBRKoGx+mq4eb7TM46IOnzksdsM\nxuOW .....FOgWMEN3/1yRufKhD7l/yTEy83vtoo=\n-----END RSA PRIVATE KEY-----\n) replacing backspace with '\n'. This Key is used to authenticate to CyberArk Central Credential provider in order to retrieve Vault REST API Credentials and should be encrypted using AWS KMS Key. 
- Update the global variables secion in the code according to your environment parameters (edit the .py files in the zip files or choose 'edit code inline' in AWS).
- Add a 'Instance' file category to the windows and linux platforms that will contains the AWS VM instance ID.
- Create two CloudWatch events: 
     ° One for machine 'pending' state and trigger the AWS provisionning lambda function with it
     ° One for machine 'terminated' state and trigger the AWS deprovisionning lambda function with it

HowTo
------------
To test the deprovisionning function for example follow the steps: 
- Create a new lambda function in AWS and upload the zip file DeprovCYBR
- Ensure that the lambda function is executed with the appropriate role
- Check the requirements above and update code consequently
- Test using below test data example by updating the "instance-id" tag with an existing VM in AWS:
```
{
  "version": "0",
  "id": "b6d7c674-6576-4d38-9b6d-1d04e4755f80",
  "detail-type": "EC2 Instance State-change Notification",
  "source": "aws.ec2",
  "account": "475713226446",
  "time": "2017-03-01T10:14:18Z",
  "region": "eu-central-1",
  "resources": [
    "arn:aws:ec2:eu-central-1:475713226446:instance/i-000986486613337bf"
  ],
  "detail": {
    "instance-id": "i-088a3542c9b5ecd1a",
    "state": "terminated"
  }
}
```

License
-------

MIT

Author Information
------------------

- Djamel Bourokba (djamel.bourokba@cyberark.com)
