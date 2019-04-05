# A set of useful scripts.  

To build, use 'build', then make a github release and update the [homebrew-tools](http://github.com/aug24/homebrew-tools/blob/master/Formula/tools.rb) entry for this repo.

# To install:

`brew install aug24/tools/tools`

# Stuff that isn't scripts yet...!


## To find all your account aliases

grep "\[" ~/.aws/credentials | tr -d '[]' | while read i; do printf "${i}: "; aws --profile ${i} iam list-account-aliases | jq '.AccountAliases' | xargs; done

## Create a VPC with a specific CIDR

aws --profile profile ec2 create-vpc --cidr-block CIDR

## List VPCs

aws --profile profile ec2 describe-vpcs

## Find a VPC by name (assuming standard tagging - works for Honeycomb anyway)

aws --profile profile ec2 describe-vpcs --query 'Vpcs[?Tags[?Value==`NAME`]]|[0]|VpcId' --output text 

## List subnets of a VPC

aws --profile profile ec2 describe-subnets --query 'Subnets[?VpcId==`vpc-XXXXXX`].SubnetId' --output text

## List CIDRs of all VPCs

aws --profile profile ec2 describe-vpcs --query 'Vpcs[*].[VpcId, CidrBlock]' --output text

## Request a peering connection

aws --profile profile ec2 create-vpc-peering-connection --vpc-id vpc-XXXXXX --peer-vpc-id vpc-YYYYYY --peer-owner-id 9999999999

## Accept a peering connection

aws --profile profile ec2 accept-vpc-peering-connection --vpc-peering-connection-id pcx-ZZZZZZ

## Describe the routing tables for a specific VPC

aws --profile profile ec2 describe-route-tables --query 'RouteTables[?VpcId==`vpc-XXXXXX`]'

## List routing table ids for a specific VPC

aws --profile profile ec2 describe-route-tables --query 'RouteTables[?VpcId==`vpc-XXXXXX`]|[*].RouteTableId' --output text

## Add a route to a specific peering connection to a routing table 

aws --profile profile ec2 create-route --route-table-id rtb-aaaaaa --destination-cidr-block qqq.rrr.sss.ttt/uu --vpc-peering-connection-id pcx-ZZZZZZ

## List dns of all instances with a particular role:

aws --profile hcam-dev ec2 describe-instances | jq -r '.Reservations|.[]|.Instances|.[]|{Tags: (.Tags|from_entries), InstanceId, PublicDnsName}|select(.Tags.role == "ROLE")|.PublicDnsName'
