# learn-aws-loadbalancer
how to change rules in load balancer
```bash
#!/bin/bash

# Prompt the user for the Rule ARN
read -p "Enter the number to redirect [1] to maintain [2] to revert back to norm: " RULE_ARN

RULE_ARN_server=
TARGET_GROUP_backend=
RULE_ARN_platform=
TARGET_GROUP_frontend=
TARGET_GROUP_redirect=

TARGET_GROUP_STRING='Type=fixed-response,FixedResponseConfig={StatusCode=503,ContentType="text/plain",MessageBody="Service Unavailable"}'

 Check if RUN_ARN is not equal to 2
if [ "$RUN_ARN" -ne 2 ]; then
  # Modify the load balancer rule for the server to respond with a fixed 503 Service Unavailable response
  aws elbv2 modify-rule \
    --rule-arn "$RULE_ARN_server" \
    --actions "$TARGET_GROUP_STRING"

  # Modify the load balancer rule for the platform to respond with a fixed 503 Service Unavailable response
  aws elbv2 modify-rule \
    --rule-arn "$RULE_ARN_platform" \
    --actions Type=forward,TargetGroupArn="$TARGET_GROUP_redirect"
else
  # Modify the load balancer rule for the server to respond with a fixed 503 Service Unavailable response
  aws elbv2 modify-rule \
    --rule-arn "$RULE_ARN_server" \
    --actions Type=forward,TargetGroupArn="$TARGET_GROUP_backend"

  # Modify the load balancer rule for the platform to respond with a fixed 503 Service Unavailable response
  aws elbv2 modify-rule \
    --rule-arn "$RULE_ARN_platform" \
    --actions Type=forward,TargetGroupArn="$TARGET_GROUP_frontend"
fi
```
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
  echo "Usage: $0 <RULE_ARN of /platform> <TARGET_GROUP_ARN of redirect-maintenance>"
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
  echo "Usage: $0 <RULE_ARN of /server>"
  exit 1
fi

RULE_ARN=$1
TARGET_GROUP_STRING='Type=fixed-response,FixedResponseConfig={StatusCode=503,ContentType="text/plain",MessageBody="Service Unavailable"}'

# Modify the load balancer rule to forward traffic to the specified target group
aws elbv2 modify-rule \
  --rule-arn "$RULE_ARN" \
  --actions $TARGET_GROUP_STRING
```
