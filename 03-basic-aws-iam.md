# Basic AWS IAM

Identity and access management is an important element of any compute infrastructure. All commands you've ran up to this point have worked because your user was allowed to run them. So let's have a look at the way AWS handles IAM. 

## AWS IAM vs AWS STS
__IAM__, identity and access management, is the service where users, groups and roles are defined. \
__STS__, security token service, is the service that issues temporary, limited-priviledge security credentials for users authenticated through IAM or an organization's own identity provider (IDP).

Every call made to the AWS API and the services offered through it, is validated against the security token sent along with it. 
If your request does not have a valid security token, or your token does not have the authorization required to perform the action, the request will be denied. 

The security token sent along with your request is generated based on your current authorisations. You get these authorisations through the IAM policies that are granted to you either through a role you have assumed through federation, or roles that have been linked to your personal user in IAM. 

So remember: this is a two step process. First your identity and authorisations are established, then a token is issued to actually perform the action. 

<mark style="background: #00ced1!important">*__Exercise 1:__ why/how am I allowed to do any of this? - Finding the account number and your current identity in the AWS console*</mark>
>In AWS console
1. Click on the down-arrow in the top right
2. Notice the Account ID \
The twelve character string, presented in three groupings of four numbers, is the ID of the account you're logged into.
3. Notice the entry below the account ID \
If you're logged in through a federated identity, you'll see ```Federated user:``` and details of their federated identity. \
If you're logged in through an IAM user, you'll see: ```IAM user:``` followed by the username. 

<mark style="background: #00ced1!important">*__Exercise 2:__ why/how am I allowed to do any of this? - asking aws cli*</mark>

>In cloudshell
1. Run ```aws sts help``` \
Notice the options that aws sts offers to interact with the sts service. \
Since we're interested in finding out our current authorizations, ```get-caller-identity``` looks interesting.
2. Run ```aws sts get-caller-identity help``` \
Our assumption seems to have been correct - the ```get-caller-identity``` switch will return details about the IAM user or role whose credentials are used to call the operation. Quit with ```q```
3. Run ```aws sts get-caller-identity``` \

If you logged into AWS through a federated IDP, your output will look like: 
```shell
{
    "UserId": "AROERGFWGSFSEERGREGRGOTENOOSJEJL:AL12345@alloyander.com",
    "Account": "012345678910",
    "Arn": "arn:aws:sts::012345678910:assumed-role/AWSReservedSSO_PowerUserAccess-eu-central-1_e72ab3366b95a257/AL12345@alloyander.com"
}
```
If you're logged on through an IAM user you created yourself, your output will look like: 

```shell
{
    "UserId": "AIDAREGEGERHEFQF4O4CV7",
    "Account": "012345678910",
    "Arn": "arn:aws:iam::012345678910:user/firstname.lastname"
}
```

<mark style="background: #00ced1!important">*__Question 1:__ interpreting the aws sts output*</mark>

<details>
<summary><mark>What's the "UserID"?</mark></summary>
<blockquote>
For federated users: <br>
<ul>
<li>The string of characters before the ':' is your federated user.

- This user is  temporary, and only valid for the duration of your IDP session. It will change when you log out, and back in. </li>
<li>The string of characters after the ':' is the identity with which you logged into the federated IDP, and is usually the same as your work domain user. </li>
</ul>
<br>
For IAM users: <br>
This is the temporary user that was issued based on your IAM credentials. 
</blockquote>
</details>
<details>
<summary><mark>What's the "Account"?</mark></summary>
<blockquote>
The account reflects the AWS account you are currently logged into. Every AWS account has an account name consisting of twelve numbers. 
</blockquote>
</details>
<details>
<summary><mark>What's the "Arn"?</mark></summary>
<blockquote>
The arn reflects the entity that has been evaluated to produce the temporary credentials. We'll explore how to read arn's below.  
</blockquote>
</details>

<br>
<br>




### Arn's
An ARN (Amazon Resource Name) is a unique identifier used to refer to resources in AWS. ARNs allow you to specify and reference AWS resources in IAM policies, API calls, and other AWS services. Every resource within AWS (such as EC2 instances, S3 buckets, Lambda functions, and more) has an ARN, which provides a standardized way to refer to these resources. </p>

Arn's have a standardized format, which looks like ```"arn:partition:service:region:account-id:resource" ```

- arn: indicates that it is indeed an arn
- partition: the AWS partition the resource is in - which is in most cases: aws. 
- service: the AWS service the resource belongs to
- region: the AWS region where the resource is located
- resource: the specific resource identifier - which could be a name, an ID or other resource-specific details. 

<mark style="background: #00ced1!important">*__Question 2:__ interpreting the differnt arn's*</mark>

<details>
<summary><mark>What is the AWS service mentioned in the arn's?</summary></mark>
<blockquote>
For federated users the service is <strong>sts</strong> - which means the AWS STS service has issued the token we used. <br>
For IAM users the service is <strong>iam</strong> - which means the IAM service has called the STS service to issue the token we used.
</blockquote>
</details>
<details>
<summary><mark>What is the resource mentioned in the arn's?</mark></summary>
<blockquote>For federated users, the resource is <strong>assumed-role/AWSReservedSSO_PowerUserAccess-eu-central-1_e72ab3366b95a257/AL12345@alloyander.com</strong> - which tells us we have assumed a role called <strong>AWSReservedSSO_PowerUserAccess-eu-central-1_e72ab3366b95a257</strong>, and have used it to get STS to issue a token.<br>
For IAM users, the resource is <strong>user/firstname.lastname</strong> - which points at the iam user we used to authenticate.
</blockquote>
</details>


## Users, User groups, Roles and Policies

AWS IAM allows you to create several security entities that you can use to tailor who gets to do what within the AWS account. 

__A user__ is an entity that has long-term credentials, and is the closest thing to a regular username and password. A user does not have any inherent authorisations.

__A user group__ is a way to combine users that have similar authorisation needs, and allow you to administer access on group-level for all members, instead of having to do it for every user individually. 

__A role__ is an identity in AWS that you can assume to gain temporary access to certain permissions and resources. Unlike an IAM user, which is permanently associated with a specific person or service, an IAM role is designed to be assumed temporarily by trusted entities such as users, applications, or AWS services.

__A policy__ is a document that defines permissions, specifying what actions are allowed or denied for a particular AWS resource. Policies are written in [JSON](./12-json.md) format and are attached to IAM users, groups, roles, or resources to define access controls.

### IAM Roles vs IAM Policies

|Feature |	IAM Roles |	IAM Policies|
|--|--|---|
|Purpose	|An IAM role is assumed to grant temporary permissions|	An IAM policy defines permissions (allow/deny actions)|
|Temporary vs Permanent	|Roles provide temporary credentials	|Policies provide permanent permissions (as long as attached)|
|How They Work|	Roles are assumed by users, services, or applications	|Policies are attached to IAM users, groups, or roles|
|Usage|	Used for temporary access, AWS services, cross-account access	|Defines who can do what (permission management)|
|Credentials	|No long-term credentials (temporary STS credentials)|	Can be attached to users, groups, or roles, providing long-term credentials|
|Attachment	|Roles are assumed temporarily by trusted entities|	Policies are attached to roles, users, or groups


### IAM Policies - inline vs customer managed vs AWS managed

Not all IAM policies are created equal. The main difference is inline vs managed. 

An __inline__ IAM policy is a policy that is embedded directly into a specific IAM entity (user, group or role). They are not reusable, and only apply to the entity they are attached to. You are responsible for any updates or changes required to the policy. 

There are two flavors of managed IAM policy. Both of them function in much the same way, with a difference in maintenance responsibility. 

A __customer managed__ IAM policy is a policy created by you, that can be assigned to multiple IAM entities. These policies have to be created before they can be attached to an entity - they cannot be created inline. You are responsible for any updates or changes required to the policy.

A __AWS managed__ IAM policy is a policy that is created and maintained by AWS. You can assign it to multiple IAM entities. If the policy required updates, AWS will perform them. 

<mark style="background: #00ced1!important">*__Exercise 3:__ Finding and interpreting credentials via the console*</mark>

>In AWS console

>For federated user: 
1. Navigate to the ```iam``` service
2. Click on ```Roles```
3. Copy your federated role from the down arrow at the top right. \
It should look like ```your-role-name/your-federated-identity@yourdomain```
4. In the search bar on the ```Roles``` page, type ```your-role-name```. (remove the ```/your-federated-identity@yourdomain```) \
The role your're looking for should show up. \
5. Click the role and look at the ```Permissions``` tab, to see the ```Permission policies``` that are attached to the role.
6. Click the [+] next to the attached policies to view their contents. \
One policy attached to this example role is called AdministratorAccess. The policy is displayed in [JSON](./12-json.md) format and looks like: 
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "*",
            "Resource": "*"
        }
    ]
}
```
This policy example above gives administrator access - which means it is ```"Effect": "Allow",``` allowed to do ```"Action": "*"``` - anything - to ```"Resource": "*"``` - anything. 

<br>

>In AWS console

>For IAM user: 
1. Navigate to the ```iam``` service
2. Click on ```Users```
3. Select a user and view its ```Permission policies``` \
This is where you can see the IAM policies associated with the user. 
4. Click the [+] in front of a policy name \
You can now see the contents of the policy in [JSON](./12-json.md), like the example above. 
<br>
<br>

<mark style="background: #00ced1!important">*__Exercise 4:__ Finding and interpreting credentials via aws cli*</mark>
>In CloudShell 

>Federated user: 

To see what we are allowed to do with our assumed role, we need to find the contents of the role. \
1. Identify your role you have assumed (see above)
2. Find the aws cli command that can describe the role for you. Run ```aws iam help``` \
```get-role``` looks promising! Exit with ```q```
3. Verify whether ```aws iam get-role``` will indeed give us the insight we need by running ```aws iam get-role help``` \
Yep looks like the right option. \
Scroll down to the ```SYNOPSIS``` and notice the formatting of the command. aws cli expects ```aws iam get-role --role-name value```, and exit with ```q```
4. Run ```aws iam get-role --role-name name-of-your-federated-role``` \
Notice that you're indeed getting information about the specified role, including its path, guid, arn and trust policy - but not the actual policies that are attached. 
5. Run ```aws iam help``` again, and look for options that can tell you something about policies attached to roles. \
Ah ```list-role-policies``` sounds good. Exit with ```q``` 
6. Run ```aws iam list-role-policies help``` \
Aha - this is going to tell us the inline policies attached to the role - but not the managed attached policies. Scroll down to the ```SYNOPSIS``` and note the formatting the command expects. Let's see what we find.  \
7. Run ```aws iam list-role-policies --role-name name-of-your-federated-role``` \
Hmm .. bit underwhelming. \
```json 
{
    "PolicyNames": []
}
```
Seems the role does not have any inline policies attached to it. But it definitely lets us do things. So it must have managed policies attached to it. \
7. Run ```aws iam help``` and look for an option that can tell us more about attached policies. \
There we go ```list-attached-role-policies``` seems promising. \
8. Run ```aws iam list-attached-role-policies help``` to verify this is indeed what we're looking for.\
Still looking good! Scroll down to the ```SYNOPSIS``` and make sure you understand the formatting of the command.\  
9. Run ```aws iam list-attached-role-policies --role-name name-of-your-federated-role``` \
The output is presented in [JSON](./12-json.md), and looks like: 
```json
{
    "AttachedPolicies": [
        {
            "PolicyName": "AdministratorAccess",
            "PolicyArn": "arn:aws:iam::aws:policy/AdministratorAccess"
        }
    ]
}
```
Nifty - yes - but the actual policy name and arn do not tell us what the policy allows us to do. \ 

8. To see the contents of the iam policies, first you have to grab their current default version. You can grab this by running ```aws iam get-policy --policy-arn arn-of-the-policy``` \
This command will output JSON metadata of the policy requested, which should look like: 
```json 
{
    "Policy": {
        "PolicyName": "AdministratorAccess",
        "PolicyId": "ANPAIWMBCKSKIEE64ZLYK",
        "Arn": "arn:aws:iam::aws:policy/AdministratorAccess",
        "Path": "/",
        "DefaultVersionId": "v1",
        "AttachmentCount": 7,
        "PermissionsBoundaryUsageCount": 0,
        "IsAttachable": true,
        "Description": "Provides full access to AWS services and resources.",
        "CreateDate": "2015-02-06T18:39:46+00:00",
        "UpdateDate": "2015-02-06T18:39:46+00:00",
        "Tags": []
    }
}
``` 
Notice the line ```"DefaultVersionId": "v1"``` - that's the last snippet of information we need to see the contents of the policy. 

9. Run ```aws iam get-policy-version --policy-arn arn-of-the-policy --version-id default-version-of-the-policy```\
You now see the actual policy document in JSON format. Example for the default AdministratorAccess policy would look like: 
```json
{
    "PolicyVersion": {
        "Document": {
            "Version": "2012-10-17",
            "Statement": [
                {
                    "Effect": "Allow",
                    "Action": "*",
                    "Resource": "*"
                }
            ]
        },
        "VersionId": "v1",
        "IsDefaultVersion": true,
        "CreateDate": "2015-02-06T18:39:46+00:00"
    }
}
```
This example gives administrator access - which means it is ```"Effect": "Allow",```  to do ```"Action": "*"``` - anything - to ```"Resource": "*"``` -anything. \
Compare the output with the policy document you saw through the AWS console. 

>In CloudShell

>For IAM user:
1. Identify which IAM user you are logged in as (see above)
2. Since we're interested in grabbing the policies attached to an IAM user now, and not a role, let's look for the appropriate aws cli options. Run ```aws iam help``` and look for an option to list policies attached to a user. \
Ah - ```list-user-policies``` looks promising. Let's see if it does what we want it to do. 
3. Run ```aws iam list-user-policies help``` \ 
So that gives us the inline policies attached to the user. Scroll down to the ```SYNOPSIS``` and notice the formatting of the command. \
Now, let's see if there are any. 
4. Run ```aws iam list-user-policies --user-name your-iam-user-name``` \
The output is presented in [JSON](./12-json.md), and looks like: 
```json 
{
    "PolicyNames": []
}
```
So no inline policies for this particular user. Fair enough. Then the user must have managed iam policies attached to it. \

5. Run ```aws iam help``` and look for an option that might tell us more about the policies attached to a user. \
Hey ```list-attached-user-policies``` sounds promising. Time to verify through the help documentation. 
6. Run ```aws iam list-attached-user-policies help``` \
Yep that's definitely it. Scroll down to the ```SYNOPSIS``` and notice the formatting of the command.
7. Run ```aws iam list-attached-user-policies --user-name your-iam-user-name``` \

From this point, you can retrieve the contents of the IAM policies as described above, though use of ```aws iam get-policy --policy-arn arn-of-the-policy``` and ```aws iam get-policy-version --policy-arn arn-of-the-policy --version-id default-version-of-the-policy```

