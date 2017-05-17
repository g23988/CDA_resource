# CDA_resource
for AWS Certified Developer – Associate test prepare

這裡是以筆記形式紀錄我在CDA考試所準備的東西

如果需要更詳細的操作

請至[我的網誌](http://g23988.blogspot.tw/)觀看


## 重點節錄

### IAM
- IAM consists of the following
- users
- Groups (A way to group our users and apply polices to them collectively)
- Roles
- Policy Documents
- IAM is universal. It does not aplly to regions at this time.
- The "root account" is simply the account created when first setup your AWS account. It has complete Admin access.
- New Users have NO permissions when first created.
- New Users are assigned Access Key ID & Secert Access Keys when first created.
- These are not the same as a password, and you cannot use the Access key ID & Secret Access Key to Login in to the console. You can use this to access AWS via the APIs and Command Line howerver.
- You only get to view these once. If you lose them, you have to regenerate them. So save them in a secure location.
- Always setip Multifactor Authentication on your root account.
- You can create and customise your own password rotation policies.

### VPC Summary
- Think of a VPC as a logical datacenter in AWS.
- Consists of IGW'S (Or Virtual Private Gateways),Route Tables , Network Access Control Lists, Subnets ,Security Groups.
- 1 Subnets = 1 Availability Zone
- Security Groups are Stateful, Network Access Control Lists are Stateless.
- Can Peer VPCs both in the same account and with other AWS accounts
- No such thing that transitive peering

### NAT instances
- When creating a NAT instances, Disable Source/Destination Check on the instances
- NAT Gateway is a very new feature that might be not in exam.
- NAT instance must be in a public Subnet
- Must have an elactic IP address to work
- There must be a route out of the private subnet to the NAT instance, in order for this to work
- The amount of traffic that NAT instances supports, depends on the instance size. If you are bottlenecking, increase the instance size
- You can create high availability using Autoscaling Groups, multiple subnets in different Availability Zone's and a script to automate failover
- Always behind a security group

### NET Gateways
- Very new, may not be in the exam yet
- Prefer by the enterprise
- Scales automatically up to 10Gnps
- No need to patch
- Not associated with secuity groups
- Automatically assigned a public ip address
- Remember to update your route tables
- No need to disable Source/Destination Checks

### Network ACL's
- Your VPC automatically comes a default Network ACL and by default it allows all inbound and outbound traffic
- You can create a custom Network ACL.By default, each custom network ACL denies all inbound and outbound traffic until you add rules
- Each subnet in your VPC must be associated with a network ACL.If you don't explicitly associated a subnet with a network ACL, the subnet is automatically associated with the default network ACL.
- You can associated a network ACL with multiple subnets; however, a subnet can be associated with only one network ACL at a time. When you associate a network ACL with a subnet, the previous association is removed.
- A network ACL comtains a numbered list of rules that is evaluated in order, starting with the lowest numbered rule
- A network ACL has separate inbound and outbound rules, and each rules can either allow or deny traffic
- Networks ACLs are stateless; responses to allowed inbound traffic are subject to the rules for outbound traffic(and vice versa)
- Block IP Addresses using network ACL's not security group.

### MAT & Bastion
- A NAT is used to provide internet traffic to EC2 instance in private subnets
- A Bastion is used to securely administer EC2 instance (using SSH or RDP) in private subnets. In other word, Bastion is Jump Boxes.

### Resilient Architecture
- If you want resiliency, always have 2 public subnets and 2 private subnets. Make sure each subnet is in different availability zones.
- With ELB's make sure they are in 2 public subnets in 2 different availability zones.
- With Bastion hosts, put them behind an autoscaling group with a minimum size of 2. Use Route53 (either round robin or using a health check) to automatically fail over
- NAT instances are tricky to make resilient. You need one in each public subnet, each with their own public IP address, and you need to write a script to fail between the two. Instrad where possible, use NAT gateways

### VPC Flow logs
- You can monitor network traffic within your custom VPC's using VPC Flow Logs

### DynamoDB

### Route53
- ELB's do not have predefined ipv4 addresses, you resolve them using a DNS name.
- Understand the different between an Alias Record and a CNAME.
- 使用 CNAME 會被收錢，Alias Record 則不會
- Given a choice, always choose an Alias Record over a CNAME.
- Remember the different routing policies and their use cases.
  - Simple
  - Weighted
  - Latency
  - Failover
  - Geolocation



### S3
#### Bucket Restrictions and Limitations
- A bucket is owned by the AWS account that created it. By default, you can create up to 100 buckets in each of your AWS accounts. If you need additional buckets, you can increase your bucket limit by submitting a service limit increase.
> S3 bucket 在預設情況下每個帳號可以開到100個。 當然也可以請求aws放大這個值。

### SQS
#### VisibilityTimeout
- Each queue starts with a default setting of 30 seconds for the visibility timeout.
- The visibility timeout for the queue. Valid values: an integer from 0 to 43,200 (12 hours). The default is 30.
> 依照預設值，SQS被消費者A取得後30秒內，其他消費者無法看見。這個值最高可以調整到12小時。

## 有用的範例問題
```
You are inserting 1000 new items every second in a DynamoDB table. Once an hour these
items are analyzed and then are no longer needed. You need to minimize provisioned
throughput, storage, and API calls. Given these requirements, what is the most efficient way
to manage these Items after the analysis?
A.Retain the items in a single table
B.Delete items individually over a 24 hour period
C.Delete the table and create a new table per hour
D.Create a new table per hour
```
> D

刪掉再建最快

---------

```
Which code snippet below returns the URL of a load balanced web site created in
CloudFormation with an AWS::ElasticLoadBalancing::LoadBalancer resource name
“ElasticLoad Balancer”?
A.“Fn::Join” : [“”. [ “http://”, {“Fn::GetAtr” : [ “ElasticLoadBalancer”,”DNSName”]}]]
B.“Fn::Join” : [“.”, [ “http://”, {“Ref” : “ElasticLoadBalancerDNSName”}]]
C.“Fn::Join” : [“”. [ “http://”, {“Ref” : “ElasticLoadBalancerUrl”}]]
D.“Fn::Join” : [“”. [ “http://”, {“Fn::GetAtr” : [ “ElasticLoadBalancer”,”Url”]}]]
```
> A

- [http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/example-templates-autoscaling.html](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/example-templates-autoscaling.html)
- [http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-ec2-elb.html#aws-properties-ec2-elb-ref](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-ec2-elb.html#aws-properties-ec2-elb-ref)

```
Correct answer is A, and should be GetAtt not GetAtr
exact code snippet:

“Outputs” : {
“URL” : {
“Description” : “The URL of the website”,
“Value” : { “Fn::Join” : [ “”, [ “http://”, { “Fn::GetAtt” : [ “ElasticLoadBalancer”, “DNSName” ]}]]}
}
```

---------
```
Which DynamoDB limits can be raised by contacting AWS support? Choose 2 answers
A.The number of hash keys per account
B.The maximum storage used per account
C.The number of tables per account
D.The number of local secondary indexes per account
E.The number of provisioned throughput units per account
```

> C E

[http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Limits.html](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Limits.html)
```
or any AWS account, there is an initial limit of 256 tables per region.

You can request an increase on this limit. For more information, go to http://aws.amazon.com/support.

An AWS account places some initial maximum limits on the throughput you can provision:

US East (N. Virginia) Region:
Per table – 40,000 read capacity units and 40,000 write capacity units
Per account – 80,000 read capacity units and 80,000 write capacity units
All Other Regions:
Per table – 10,000 read capacity units and 10,000 write capacity units
Per account – 20,000 read capacity units and 20,000 write capacity units
The provisioned throughput limit includes the sum of the capacity of the table together with the capacity of all of its global secondary indexes.
```

---------


## 官方範例
聽說必出，所以自己先拿來回答了

有找到正確答案再修正
```
Which of the following statements about SQS is true?
A. Messages will be delivered exactly once and messages will be delivered in First in, First out order
B. Messages will be delivered exactly once and message delivery order is indeterminate
C. Messages will be delivered one or more times and messages will be delivered in First in, First out order
D. Messages will be delivered one or more times and message delivery order is indeterminate
```
> D

SQS可以提供多次讀取，雖說目前已經有FIFO (First in, First out)，但是還不會反映在考試上

---------

```
EC2 instances are launched from Amazon Machine Images (AMIs). A given public AMI:
A. can be used to launch EC2 instances in any AWS region
B. can only be used to launch EC2 instances in the same country as the AMI is stored
C. can only be used to launch EC2 instances in the same AWS region as the AMI is stored
D. can only be used to launch EC2 instances in the same AWS availability zone as the AMI is stored
```
> C

AMI僅會儲存在當下的region，如果想要讓AMI能夠在別的region使用，可以使用 Copy AMI 功能複製到別的region

---------

```
Company B provides an online image recognition service and utilizes SQS to decouple system
components for scalability. The SQS consumers poll the imaging queue as often as possible to keep endto-end
throughput as high as possible. However, Company B is realizing that polling in tight loops is
burning CPU cycles and increasing costs with empty responses. How can Company B reduce the number
of empty responses?
A. Set the imaging queue VisibilityTimeout attribute to 20 seconds
B. Set the imaging queue ReceiveMessageWaitTimeSeconds attribute to 20 seconds
C. Set the imaging queue MessageRetentionPeriod attribute to 20 seconds
D. Set the DelaySeconds parameter of a message to 20 seconds
```
> B

因為向SQS要Queue，SQS會馬上回應。

如果SQS是空的那就慘了，他會一直一直回應直到把你CPU吃滿

所以AWS有設計一個long pool，最長可以設定到20秒

那個選項在aws console 上的label是 Receive Message Wait Time

---------

```
You attempt to store an object in the US-STANDARD region in Amazon S3, and receive a confirmation
that it has been successfully stored. You then immediately make another API call and attempt to read
this object. S3 tells you that the object does not exist. What could explain this behavior?
A. US-STANDARD uses eventual consistency and it can take time for an object to be readable in a bucket.
B. Objects in Amazon S3 do not become visible until they are replicated to a second region.
C. US-STANDARD imposes a 1 second delay before new objects are readable
D. You exceeded the bucket object limit, and once this limit is raised the object will be visible.
```
> A

S3有設計最終一致性的功能(eventual consistency)，所以最新的PUT object並不會馬上反映在讀取上

直到所有的準備都完成，才能取得object

順帶一提，美國標準(US-STANDARD) 已經改名為 北維吉尼亞 (N.Virginia us-east-1)

---------

```
You have reached your account limit for the number of CloudFormation stacks in a region. How do you
increase your limit?
A. Make an API call
B. Contact AWS
C. Use the console
D. You cannot increase your limit
```
> B

找飯店？trivago！

---------

```
Which statements about DynamoDB are true? (Pick 2 correct answers)
A. DynamoDB uses a pessimistic locking model
B. DynamoDB uses optimistic concurrency control
C. DynamoDB uses conditional writes for consistency
D. DynamoDB restricts item access during reads
E. DynamoDB restricts item access during writes
```
> BC

這題答案我也不確定，不過這兩項看起來是DynamoDB能夠辦到的

---------

```
What is one key difference between an Amazon EBS-backed and an instance-store backed instance?
A. Instance-store backed instances can be stopped and restarted
B. Auto scaling requires using Amazon EBS-backed instances
C. Amazon EBS-backed instances can be stopped and restarted
D. Virtual Private Cloud requires EBS backed instances
```
> C

Instance-store 是設計在各台虛擬主機上的暫存空間，而EBS則是在AWS Storage設備上的iSCSI空間

所以Instance-store 會隨著stop，或是terminate而消失

但是兩者reboot都是沒問題的

---------

```
A corporate web application is deployed within an Amazon VPC, and is connected to the corporate data
center via IPSec VPN. The application must authenticate against the on-premise LDAP server. Once
authenticated, logged-in users can only access an S3 keyspace specific to the user.
Which two approaches can satisfy the objectives?
A. The application authenticates against LDAP. The application then calls the IAM Security Service to login
to IAM using the LDAP credentials. The application can use the IAM temporary credentials to access the
appropriate S3 bucket.
B. The application authenticates against LDAP, and retrieves the name of an IAM role associated with the
user. The application then calls the IAM Security Token Service to assume that IAM Role. The application
can use the temporary credentials to access the appropriate S3 bucket.
C. The application authenticates against IAM Security Token Service using the LDAP credentials. The
application uses those temporary AWS security credentials to access the appropriate S3 bucket.
D. Develop an identity broker which authenticates against LDAP, and then calls IAM Security Token Service
to get IAM federated user credentials. The application calls the identity broker to get IAM federated user
credentials with access to the appropriate S3 bucket.
E. Develop an identity broker which authenticates against IAM Security Token Service to assume an IAM
Role to get temporary AWS security credentials. The application calls the identity broker to get AWS
temporary security credentials with access to the appropriate S3 bucket.
```
> BD

在LDAP做auth 使用情景下

應用程式拿role，再用role給予的temporary credentials 做存取 (預設一小時後過期 3600s)

使用者則拿federated user credential 做存取

---------

```
You run an ad-supported photo sharing website using S3 to serve photos to visitors of your site. At some
point you find out that other sites have been linking to the photos on your site, causing loss to your
business. What is an effective method to mitigate this?
A. Use CloudFront distributions for static content.
B. Remove public read access and use signed URLs with expiry dates.
C. Block the IPs of the offending websites in Security Groups.
D. Store photos on an EBS volume of the web server.
```
> B

先把暴露在外面的URL先拿掉，避免盜連

然後使用signed urls去設定連結的過期時間


---------
```
Your application is trying to upload a 6 GB file to Simple Storage Service and receive a "Your proposed
upload exceeds the maximum allowed object size." error message. What is a possible solution for this?
A. None, Simple Storage Service objects are limited to 5 GB
B. Use the multi-part upload API for this object
C. Use the large object upload API for this object
D. Contact support to increase your object size limit
E. Upload to a different region
```
> B

AWS建議超過100MB就使用 Multi-part upload 了

當然不做也可以，但是會被限制在 5 GB

單個object最大可以存 5 TB

而bucket能存放的object則是無限。


## 問題集
 - [範例問題](https://github.com/g23988/ACD_resource/tree/master/Sample_question/sample_question_200)
 - [範例問題解答](https://github.com/g23988/ACD_resource/blob/master/Sample_question/Pages%20from%20Amazon_ACDA.pdf)
 - [網路搜尋範例問題](https://blog.cloudthat.com/sample-questions-for-amazon-web-services-certified-developer-associate-certification/)
 - [網路搜尋範例問題2](http://devcertguru.com/certifications/amazon/exam-questions/aws-cda-v1)
 - [網路搜尋範例問題3](http://www.aiotestking.com/amazon/category/exam-aws-cda-aws-certified-developer-associate-update-oct-27th-2016/)

## Resource
 - [ACD sample question blog](http://m8010-241-dumps-pdf.blogspot.tw/2016/03/amazon-aws-certified-developer.html?m=1)
 - [ACD offical sample question & answer](http://www.certificationking.com/download/Amazon-AWS.htm)
 - [官方考試首頁](https://aws.amazon.com/tw/certification/certified-developer-associate/)
 - [認證資源 - 白皮書](https://aws.amazon.com/tw/certification/certification-prep/)
