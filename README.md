# AWS Cost Categories for Organizations Automation


The **AWS Cost Categories for Organizations Automation** tool is designed to automate the process of creating AWS Cost Categories based on tags defined at accounts level in your AWS Organizations. It leverages AWS EventBridge to periodically check your AWS Organization account structure and dynamically create and update Cost Categories definitions for better cost allocation and management.


## Table of Contents
- [AWS Cost Categories for Organizations Automation](#aws-cost-categories-for-organizations-automation)
  - [Table of Contents](#table-of-contents)
  - [Introduction](#introduction)
  - [Prerequisites](#prerequisites)
  - [Installation and Configuration](#installation-and-configuration)
  - [Usage](#usage)
  - [Notes](#notes)
  - [Roadmap](#roadmap)
  - [License](#license)

## Introduction

Managing costs within an AWS Organization can be challenging, especially when dealing with a large number of accounts and services. The AWS Cost Categories for Organization Automation tool simplifies this process by automatically generating cost categories based on the tags associated with each account in your AWS Organization.

With this tool, you can define cost categories that align with your organization's cost allocation strategy and automatically update them as accounts and tags change within your AWS Organization. This automation helps you save time and ensure accurate cost attribution across your organization and allows you to visualize categories directly from the AWS Cost Explorer.

## Prerequisites

Before using the AWS Cost Categories for Organizations Automation tool, make sure you have the following prerequisites:

- An AWS Organization Management account with consolidated billing enabled.
- An AWS Organization structure where the account is the most granular billable entity.
- At the AWS Organization level, tags associated directly with AWS Accounts that would be used for Cost Categories. 
  - Note: Tags associated with Organization OUs are not currently used by this solution to build Cost Categories.
- IAM credentials or Console access with appropriate permissions to create/update CloudFormation stacks that includes IAM Role creation.
- No SCPs that prevents this solution IAM Role from using : 
    - AWS Organizations.
    - Cost Explorer categories.
    - Lambda functions.
    - CloudWatch Log Groups.
    - Systems Manager Parameter Store. 
    - See CloudFormation template `OrgCostCategoriesAutoRole` for the IAM Policies in details.
  
- Basic knowledge of AWS CloudFormation and its usage.
- Basic knowledge of AWS Cost Explorer queries and filters.

## Installation and Configuration

To install and configure the AWS Cost Categories for Organizations Automation tool, follow these steps:

1. Download the CloudFormation template provided in this GitHub repository, save it to your local machine. ( `AWS-Organization-Cost-Categories-automation-installer.yml` )
2. Go to the CloudFormation Console
3. Create a CloudFormation Stack from new resources, upload the CloudFormation template from your local machine.
4. Choose a Stack name  
   - eg: `OrgCostCategoriesAutomation`
5. Provide the tag keys that should be considered for building the Cost Categories, be sure to use coma separated list 
    -   eg: `CostCenter, ProjectEnv, ProjectID`
    -   Keep in mind that AWS Tags are case sensitive.
6. Choose the starting date by entering year and month for building categories, fields : `OrgCostCategoriesAutoStartMonth` and `OrgCostCategoriesAutoStartYear`.
   - Important : AWS Cost Categories are built on Cost Explorer data, so you can go back in time up to 12 months.
7. Leave `OrgCostCategoriesAutoSSMPathOrgAccountsDigest` and `OrgCostCategoriesAutoSSMPathOrgUnitsDigest` default values.
8. Acknowledge that CloudFormation will create IAM resources, then Create.


## Usage

1. Following the installation steps, the CloudFormation stack should be in `CREATE_COMPLETE` state.
2. A primer invoke is made from CloudFormation to build the Cost Categories.
3. Once a day, the lambda runs and detect potential changes you made
   - ie: add or remove tags and accounts in your AWS Organization.
4. When adding or removing tags, you must :
      -    First, add the new tags to the relevant AWS accounts that must be considered under new Cost Categories.
      -    Update the CloudFormation stack parameters with the new tags or remove tags that are not in use anymore.
      -    Note : This solution wont't delete any AWS Cost Categories, wether you update or delete the CloudFormation stack.

## Notes
Currently, this solution assumes that an AWS Account is the most granular element for you to do costs allocation, we plan to support other mechanisms for a more granular approach within multi tenants AWS accounts based on Cost Allocation Tags.

There's no cost associated with the usage of AWS Cost Categories, however other services fees apply with this solution such as EventBridge, Lambda invocation and SSM Parameter Store, see :
- https://aws.amazon.com/eventbridge/pricing/
- https://aws.amazon.com/lambda/pricing/
- https://aws.amazon.com/systems-manager/pricing/


## Roadmap
-   Support for AWS Cost Allocation Tags.
-   Support for AWS Cost Categories by Organizational Units.
-   Support for AWS Cost Categories deletion when tags removed.
   
## License
This project is licensed under the MIT-0 License.

