### udawsdevass
12.
IAM
- Customize(change name)
- Users->user action->manage password->
14.
- IAM->(right sidebar) web identity federation playground
15.
- SAML- secure assertive markup lang
- Authenticate againest Identity Provider(Fb/AD)
- Obtain temp security credentials
- (AssumeRoleWithWebIdentity)
17.
aws_access_key=AKIAAA
aws_secret_access_key=
region=
18.
- Must assign a role when creating EC2.Roles cannot be changed after creating
19.
```
<?php
require '/var/www/html/vendor/autoload.php';
use Aws\S3\S3Client;
$client = S3Client::factory();
?>
```
21.
After logging in EC2,
```
curl http://169.254.169.254/latest/meta-data/
```
Or add folder name
```
curl http://169.254.169.254/latest/meta-data/public-ipv4
```
22.
- Multiple SSL Cert can be terminated on an ELB
- ELB's are not free.(includes CFormation, EL Beanstalk, Autoscaling)
- Ports: 25(smtp) 80 443 1024-65535
23.
- Default region:us-east-1
- some have default region(java)
- some do not(nodejs)



25.
- upload data to S3 via SSL Encrycpted End Ponits
- AWS Key Management service(KMS)
- Encryption
  - Server side (S3 Managed keys, KMS-Managed keys,Customer Provided keys)
  - Client side
25
- bucket name should be unique per region
- To make properties public, action->public
27
- website address structure: bucketname.s3-website-eu-west-1.amazonaws.com
- non-website: s3-website-eu-west-1.amazonaws.com/bucketname
- Add cors if blocked
- Step: add the following.Note add the file to child bucket
```
<CORSConfiguration xmlns="http://s3.amazonaws.com/doc/2006-03-01/">
<CORSRule>
<AllowedOrigin><parent link that will load this asset></AllowedOrigin>
<AllowedMethod>GET</AllowedMethod>
<MaxAgeSecond>3000</MaxAgeSecond>
<AllowedHeader>*</AllowedHeader>
</CORSRule>
</CORSConfiguration>
```
permissions->add CORS config,paste
add this file to child(asset) bucket.

31.
CloudFront
- Origin: origin of all the files that the CDN will distribute, this can be either an S3 bucket an EC2 instance,an Elastic LB or Route53.
- Distribution: consists of a colletion of edge locations
- One dist with multiple origins
- Web distribution, RTMP
32.


34.
- You can use embedded data structures in mongodb,but not in dynamodb
- OLAP OLTP(data mining)
36.
- ECR: consistency across all aopies of data is usually reached within a second,repeating a read after a shoer time should return the updated data.(best)
- SCR: Returns a result that reflects all writes the received as sucessful response prior to the read

37.
- Local secondary index (5 max) has the same hash key, different range key
  - can only be created when creating a table. They can not be removed or modified later.

- Global secondary index (5 max)
  -has diff hash key and diff range key.can be created later

38.
- Query: finds item in a table using only primary key attribute values. Must provide a hash key attribute name and a distinct value to search for.
- Optionally provide a range key attribute and value, and use a comparison operator to refine the searvch results.
- set the ScanIndexForward to false if want to descending.(by default is evetually consistent but can be changed to be strongly consistent)
- Use projectExpression param so that the Scan only returns some of the attributes, rather than all of then.

39.
- Unit of read provisioned throughput
  - All reads are 4kb
  - Eventually consistent reads(default) consist 2 reads per second
  - Strongly consistent reads consist 1 reads per second
- Unit of write provisioned throughput
  - All writes are 1kb
  - All writes consist of 1 write per second
- (Read calculation problems)
- Ex: 7kb, 8items, stronglyconsistent. Read throughput= ceil(7/4)*8/1=16.
- Ex: 10kb,5items, write consistent=5*10=50
- API Error code: 400: ProvisionedThroughtExceededException( you exceeded your max allowed pro thou for a table or for one or more global sec indexes)

40
- AssumeRoleWithWebIdentity API
- copy ARN from any table, IAM->select role type->role for identity provider access->Grant access to web identity access->select facebook, type in applicationID->next step->policy generator->allow dynamodb(all action),paste ARN. ->Add statement

41
- Idempotent
- Atomic counter(not idempotent), This means that the counter will increment each time you call updateItem 
- BatchGetItem API, retrieve 1MB of data as many as 100 items
43
SQS
- Does not offer FIFO
- 30second min, 12 hours max visibility time out(when you receive a message from a queue and begin processing it, you may find the visibility timeout for the queue is insufficient to fully process and delete that message. You can change hte itmeout by using ChangeMessageViibility action
- long polling max =20sec
- 256kb message size
- billed at 64kb chunks

46
- Create new topic>

48
- The decider is a program that controls the coordination of tasks. their ordering, concurrency, and scheduling according to applicaion logic
- Your workflow and activity flows and age workflow execution itself are all scoped to a domain
- Maximum workflow can be 1 year and the value is always measured in seconds
- SWF task is assigned and is never duplicated.

50
- environment tier(webserver)->create an RDS, Create this a VPC->
52
- create a subnet(CIDR:10.0.1.0/24)
- create second subnet(CIDR:10.0.2.0/24)
- create third subnet(CIDR:10.0.3.0/24)
- Edit route table, by default, the route table can access to vpc itself
- create a new igw, attach to vpc, create a new route table, add target=igw, destination=0.0.0.0/0.
- Attach subnet to route table.
- one subnet can only assoc to one route table.
53
- default ACL-> allow all
- new ACL->deny all
- A subnet can only assoc to one ACL.
- A ACL can assoc to multiple subnet.
- If you disasso your subnet from your ACL, it will go to default subnet.
54
create NAT instance
- security group is bound to vpc
- create sg, add inbound rules, (HTTP,HTTPS,, source=private subnet(10.0.2.0/24)
- outbound rules.(HTTP,HTTPS), source = (0.0.0.0/0)
- Then create new instance(EX, amzi-ami-vpc-nat-hvm), subnet should be under public subnet.(10.0.1.0/24),no public IP
- To make a subnet public 1.public ip 2.elastic ip 3.elastic lb
- attach se g.
- And asso a elas ip.
- action->networking->change source dest check->disable
- Then edit default route table, add target:new instance destination:0.0.0.0/0
