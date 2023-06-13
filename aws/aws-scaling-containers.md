# AWS: Scaling containers

Source: https://www.vladionescu.me/posts/scaling-containers-on-aws-in-2022/

Tl;dr:

- Fargate is now faster than EC2
- ECS on Fargate improved so much and is the perfect example for why offloading engineering effort to AWS is a good idea
- ECS on Fargate using Windows containers is surprisingly fast
- App Runner is on the way to becoming a fantastic service
- Up to a point, EKS on Fargate is faster than EKS on EC2
- EKS on EC2 scales faster when using karpenter rather than cluster-autoscaler, even in the worst possible scenario
- EKS on EC2 is a tiny bit faster when using IPv6
- Lambda with increased limits scales ridiculously fast

