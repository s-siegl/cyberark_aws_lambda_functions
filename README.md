# AWS-Lambda-For-CyberArk
Implementation of AWS Lambda functions in order to provision/deprovision Windows and Linux Admin account dinamycally in/from CyberArk.
The provisionning function creates a safe and position a Vault user or group as owner according to AWS machine configured tags. The account is onboarded with a 'default' key retrieved from the Vault. The 'default' key is used as SSH access key for linux machines and as password encryption key for windows machines. An immediate rotation is scheduled once the new account is onboarded. 
The deprovisonng function deletes all entries in CyberArk that contains the InstanceID of the terminated machine. 

Requirements
------------

- Execute lambda function with AWS role that have access to instances information and credentials.
- Having access to Vault REST API and Central Credential Provider
- Declare an environment variable named 'PrivateKey' that contains private key string in pem format (example: PrivateKey: -----BEGIN RSA PRIVATE KEY-----\nMIIEogIBAAKCAQEAszEY23vmmzhu6fK3BC3LoDBRKoGx+mq4eb7TM46IOnzksdsM\nxuOW .....FOgWMEN3/1yRufKhD7l/yTEy83vtoo=\n-----END RSA PRIVATE KEY-----\n) replacing backspace with '\n'.
- Update the global variables secion in the code according to your environment parameters (edit the .py files in the zip files or choose 'edit code inline' in AWS).
- Add a 'Instance' file category to the windows and linux platforms that will contains the AWS VM instance ID.
- Create two CloudWatch events: 
     ° One for machine 'pending' state and trigger the AWS provisionning lambda function with it
     ° One for machine 'terminated' state and trigger the AWS deprovisionning lambda function with it

License
-------

MIT

Author Information
------------------

- Djamel Bourokba (djamel.bourokba@cyberark.com)
