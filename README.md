# kops + kubernetes + AWS Tutorial

### Documentation Reference
- https://github.com/kubernetes/kops/blob/master/docs/aws.md

### Getting Started

Complete "Getting Started" document @ https://github.com/kubernetes/kops/blob/master/docs/aws.md

This tutorial will ask you to install kops, kubectl and setup your AWS environment. The tuturial will require an AWS account where you can create various resources. I've used a personal AWS account in this tutorial and a public domain hosted by route53 (Scenario 1b). There are other domain options including optional DNS using a .local domain. 

My environment

- Public DNS (Scenario 1b): dev.oyarsa.net
- Account: private AWS
- Region: us-west-2

### Step by Step Runbook

In this runbook I've ommited the installation of kops, kubernetes and setting up your AWS environment. They are covered in the "Getting Started" document and many web sites describe how to handle any issues. The main purpose here is to show how I used kops to deploy my kubernetes cluster.

**Step 1:** 
```
(base) private@ubuntu:~/devops$ mkdir project4; cd project4
(base) private@ubuntu:~/devops/project4$ 
```
**Step 2:**
```
(base) private@ubuntu:~/devops/project4$ cat project4.rc; source project4.rc 
export EDITOR=/usr/bin/vim
export AWS_DEFAULT_PROFILE=kops
export AWS_DEFAULT_REGION=us-west-2
export NAME=project4.dev.oyarsa.net
export KOPS_STATE_STORE=s3://project4-dev-oyarsa-net-state-store
```
**Step 3:** 
```
(base) private@ubuntu:~/devops/project4$ ID=$(uuidgen) && aws route53 create-hosted-zone --name project4.dev.oyarsa.net --caller-reference $ID | \
>     jq .DelegationSet.NameServers
[
  "ns-49.awsdns-06.com",
  "ns-1343.awsdns-39.org",
  "ns-934.awsdns-52.net",
  "ns-1771.awsdns-29.co.uk"
]
(base) private@ubuntu:~/devops/project4$ aws route53 list-hosted-zones | jq '.HostedZones[] | select(.Name=="oyarsa.net.") | .Id'
"/hostedzone/Z19KYSK4809JXK"
(base) private@ubuntu:~/devops/project4$ cat subdomain.json 
{
  "Comment": "Create a subdomain NS record in the parent domain",
  "Changes": [
    {
      "Action": "CREATE",
      "ResourceRecordSet": {
        "Name": "project4.dev.oyarsa.net",
        "Type": "NS",
        "TTL": 300,
        "ResourceRecords": [
          {
            "Value": "ns-49.awsdns-06.com"
          },
          {
            "Value": "ns-1343.awsdns-39.org"
          },
          {
            "Value": "ns-934.awsdns-52.net"
          },
          {
            "Value": "ns-1771.awsdns-29.co.uk"
          }
        ]
      }
    }
  ]
}
(base) private@ubuntu:~/devops/project4$ aws route53 change-resource-record-sets \
>  --hosted-zone-id Z19KYSK4809JXK \
>  --change-batch file://subdomain.json
{
    "ChangeInfo": {
        "Id": "/change/C3KI0E77CVLQX4",
        "Status": "PENDING",
        "SubmittedAt": "2019-06-18T02:32:36.187Z",
        "Comment": "Create a subdomain NS record in the parent domain"
    }
}
(base) private@ubuntu:~/devops/project4$ aws route53 get-hosted-zone --id Z2RE54LR5QLE2R
{
    "HostedZone": {
        "Id": "/hostedzone/Z2RE54LR5QLE2R",
        "Name": "project4.dev.oyarsa.net.",
        "CallerReference": "cd127e4e-15cb-49ac-8639-31f046abfbb4",
        "Config": {
            "PrivateZone": false
        },
        "ResourceRecordSetCount": 2
    },
    "DelegationSet": {
        "NameServers": [
            "ns-49.awsdns-06.com",
            "ns-1343.awsdns-39.org",
            "ns-934.awsdns-52.net",
            "ns-1771.awsdns-29.co.uk"
        ]
    }
}
(base) private@ubuntu:~/devops/project4$ dig NS project4.dev.oyarsa.net +short
ns-1343.awsdns-39.org.
ns-1771.awsdns-29.co.uk.
ns-49.awsdns-06.com.
ns-934.awsdns-52.net.
```
**Step 5:**
