# kops

### Documentation Reference
- https://github.com/kubernetes/kops/blob/master/docs/aws.md

### Getting Started

Step 1: Complete "Getting Started" document @ https://github.com/kubernetes/kops/blob/master/docs/aws.md

This tutorial will install kops, kubectl and setup your environment, specifically AWS. 

My environment

- Public DNS (Scenario 1a): dev.oyarsa.net
```(base) private@ubuntu:~/devops/project0$ aws --profile kops route53 list-hosted-zones 
{
    "HostedZones": [
        {
            "Id": "/hostedzone/Z19KYSK4809JXK",
            "Name": "oyarsa.net.",
            "CallerReference": "RISWorkflow-RD:762ac2dc-82ab-4555-a0ce-8cec78844722",
            "Config": {
                "Comment": "HostedZone created by Route53 Registrar",
                "PrivateZone": false
            },
            "ResourceRecordSetCount": 3
        }
    ]
}
```

Step 2: 
