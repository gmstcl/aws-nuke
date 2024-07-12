# aws-nuke  
<div align="left">
   <img src="https://img.shields.io/badge/AmazonWebServices-232F3E?style=flat&logo=amazonwebservices&logoColor=white"/> <!-- AWS -->
</div>
AWS Nuke searches for resources that can be deleted from your AWS account, which is a tool that deletes all resources that you create yourself, except for the basic resources that are captured by AWS administration.  
  
> Therefore, there are some things to be aware of when using AWS nuke.

# precautions

⚠️  
1. By default, AWS nuke deletes only the user-generated resources mentioned above, adding the command "--no-dry-run" is essential to actually delete the resources.
   ex) “aws-nuke -c config.yml --no-dry-run”

2. AWS nuke makes two deletion confirmation requests by entering the alias of the account.
   First: After the start
   Second: once after you query the deleteable resource
3. You need to create an alias in your account to prevent people from only showing accounts that can be ignored.
4. Account alias will not run if a string containing "prod" is entered.
5. The setup file has a block field. AWS nuke is suspended if the account ID you are trying to nuke is part of this block list. By default, it is recommended that all production accounts be added to the block list.
6. The settings file contains account-specific settings.
7. You must specify a configuration file so that you do not accidentally delete any account. It is recommended that you keep only a single configuration file and add it to the central repository.   

--- 

## Amazon Web Service IAM alias

AWS iam Page :
   ```sh
   https://us-east-1.console.aws.amazon.com/iam
   ```
It is located in the Dashboard Account ID section.  
Create alias .. ex ) hrdkorea  
IAM aliases are used to create IAM aliases because AWS-NUKE works.

## Amazon Web Service ecurity_credentials
   ```sh
   https://us-east-1.console.aws.amazon.com/iam/home#/security_credentials
   ```
Access keys Create : AWS Access Key ID && AWS Secret Access Key 

## PowerShell

You must download the aws-nuke (.exe) above. The tasks below are performed locally on Windows.

C:\Users\admin\Downloads\aws-nuke-v2.19.0-windows-amd64 -> C:\Users\admin\Downloads\aws-nuke 
  
mv C:\Users\admin\Downloads\aws-nuke C:\Windows\System32\aws-nuke 

windows + x + i  //open PowerShell

aws cli install 
  
  ```sh
  msiexec.exe /i https://awscli.amazonaws.com/AWSCLIV2.msi
  ```

aws configure

  ```sh
  aws configure --profile aws-nuke
  AWS Access Key ID [None] : <aws nuke Access key>
  AWS Secret Access Key [None] : <aws nuke Secret Access key>
  Default region name [None] : ap-northeast-2
  Default output format [None] : json
  ```

  ```sh
  notepad config.yml
  ```

  ```sh
  regions:
  - us-east-1
  - us-east-2
  - us-west-1
  - us-west-2
  - ap-northeast-1
  - ap-northeast-2
  - ap-northeast-3
  - ap-southeast-1
  - ap-southeast-2
  - global

  account-blocklist:
  - "999999999999"

  resource-types:
    excludes:
    - IAMUser
    - IAMUserPolicyAttachment
    - IAMUserAccessKey
    - Route53HostedZone

  accounts:
    000000000000: # AWS ID
       filters:
        IAMUser:
        - "aws-nuke-user"
        IAMUserPolicyAttachment:
        - property: RoleName
          value: "aws-nuke-user"
        IAMUserAccessKey:
        - property: UserName
  ```

## Account Check

Make sure it's the same as your account.
  ```sh
  aws sts get-caller-identity --query 'Account' --output text --profile aws-nuke
  ```

Running aws-nuke
  ```sh
  aws-nuke -c config.yml --no-dry-run --profile aws-nuke
  ```


