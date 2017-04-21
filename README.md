# CDA_resource
for AWS Certified Developer – Associate test prepare

這裡是以筆記形式紀錄我在CDA考試所準備的東西

如果需要更詳細的操作

請至[我的網誌](http://g23988.blogspot.tw/)觀看


## 重點節錄

### S3
#### Bucket Restrictions and Limitations
- A bucket is owned by the AWS account that created it. By default, you can create up to 100 buckets in each of your AWS accounts. If you need additional buckets, you can increase your bucket limit by submitting a service limit increase.
> S3 bucket 在預設情況下可以開到100個。 當然也可以請求aws放大這個值。

### SQS
#### VisibilityTimeout
- Each queue starts with a default setting of 30 seconds for the visibility timeout.
- The visibility timeout for the queue. Valid values: an integer from 0 to 43,200 (12 hours). The default is 30.
> 依照預設值，SQS被消費者A取得後30秒內，其他消費者無法看見。這個值最高可以調整到12小時。

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

## Resource
 - [ACD sample question blog](http://m8010-241-dumps-pdf.blogspot.tw/2016/03/amazon-aws-certified-developer.html?m=1)
 - [ACD offical sample question & answer](http://www.certificationking.com/download/Amazon-AWS.htm)
 - [官方考試首頁](https://aws.amazon.com/tw/certification/certified-developer-associate/)
 - [認證資源 - 白皮書](https://aws.amazon.com/tw/certification/certification-prep/)
