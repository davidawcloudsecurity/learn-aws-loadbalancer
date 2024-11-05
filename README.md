# learn-aws-loadbalancer
how to change rules in load balancer
### How to view list of load balancers' name and arn
```bash
aws elbv2 describe-load-balancers --query 'LoadBalancers[*].[LoadBalancerName, LoadBalancerArn]' --output text
```
### How to pull specific target load balancer
```bash
aws elbv2 describe-load-balancers --query "LoadBalancers[?contains(LoadBalancerName, 'k8s-platform-ingress')].LoadBalancerArn"     --output text
```
### How to pull specific target group
```bash
aws elbv2 describe-target-groups --query "TargetGroups[?contains(TargetGroupName, 'maintenance')].TargetGroupArn" --output text
```
### how to modify a rule to a target group
```bash
#!/bin/bash

# Check if the required inputs are provided
if [ "$#" -ne 2 ]; then
  echo "Usage: $0 <RULE_ARN> <TARGET_GROUP_ARN>"
  exit 1
fi

RULE_ARN=$1
TARGET_GROUP_ARN=$2

# Modify the load balancer rule to forward traffic to the specified target group
aws elbv2 modify-rule \
  --rule-arn "$RULE_ARN" \
  --actions Type=forward,TargetGroupArn="$TARGET_GROUP_ARN"
```
### how to modify a rule to a string
```bash
#!/bin/bash

# Check if the required inputs are provided
if [ "$#" -ne 2 ]; then
  echo "Usage: $0 <RULE_ARN> <Type=fixed-response,FixedResponseConfig={StatusCode=503,ContentType="text/plain",MessageBody="Service Unavailable"}>"
  exit 1
fi

RULE_ARN=$1
TARGET_GROUP_STRING='Type=fixed-response,FixedResponseConfig={StatusCode=503,ContentType="text/plain",MessageBody="Service Unavailable"}'

# Modify the load balancer rule to forward traffic to the specified target group
aws elbv2 modify-rule \
  --rule-arn "$RULE_ARN" \
  --actions $TARGET_GROUP_STRING
```
