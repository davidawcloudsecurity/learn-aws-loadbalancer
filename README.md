# learn-aws-loadbalancer
how to change rules in load balancer
### How to view list of load balancers' name and arn
```bash
aws elbv2 describe-load-balancers --query 'LoadBalancers[*].[LoadBalancerName, LoadBalancerArn]' --output text
```
