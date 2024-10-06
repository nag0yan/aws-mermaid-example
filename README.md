``` mermaid
architecture-beta
    service dns(logos:aws-route53)[Route53]
    service cdn(logos:aws-cloudfront)[CloudFront]
    service cert(logos:aws-certificate-manager)[ACM]

    service storage(logos:aws-s3)[S3]

    group vpc(logos:aws-vpc)[VPC]

    service loadbalancer(logos:aws-elb)[ALB] in vpc

    group private_subnet1[Private Subnet 1] in vpc
    service appserver1(logos:aws-ec2)[EC2 Instance 1] in private_subnet1
    service db_primary(logos:aws-aurora)[Aurora Primary] in private_subnet1

    group private_subnet2[Private Subnet 2] in vpc
    service appserver2(logos:aws-ec2)[EC2 Instance 2] in private_subnet2
    service db_replica(logos:aws-aurora)[Aurora Replica] in private_subnet2

    junction asg_junction in vpc
    junction aurora_junction in vpc

    dns:R -- L:cdn
    cdn:R -- L:loadbalancer
    loadbalancer:R -- L:asg_junction
    asg_junction:T -- B:appserver1
    asg_junction:B -- T:appserver2
    appserver1:R -- L:db_primary
    appserver2:R -- L:db_replica
    cdn:T -- B:storage

    db_replica:T -- B:aurora_junction
    aurora_junction:T --> B:db_primary

    cert:B -- T:dns
```
