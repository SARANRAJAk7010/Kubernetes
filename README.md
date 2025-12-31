# Kubernetes

1. Launch a Kubernetes Cluster
Launch EC2 instances and connenct to instances via EC2 instance cconnect

--- Launch an EKS Cluster
eksctl create cluster \
--name nextwork-eks-cluster \
--nodegroup-name nextwork-nodegroup \
--node-type t3.micro \
--nodes 3 \
--nodes-min 1 \
--nodes-max 3 \
--version 1.33
--region us-east-1

-> eksctl not found
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv -v /tmp/eksctl /usr/local/bin

-> Again try creating cluster Again Failed, Permissionneeded
-> Need IAM role to let ec2 instances connect to resources

--Creating IAM role
IAM -> Roles -> Create role -> Trsuted entity type, select AWS service -> Under use case, select EC2 -> Under Permission Policies, AdministratorAccess.

--Attach IAM role to EC2 Instance
EC2 -> Actions dropdown -> Security, Modify IAM role.

-> Create ClusterAgain

AWS -> eksctl -> CloudFormation will create required resources

-> Cant able to access Kubernetes cluster even after allowing AdministratorAccess, Need to have Allow Access

Create access entry -> Under IAM Principal, select yours -> Under Policy name, select "AmazonEKSClusterAdminPolicy" -> select Add Policy 
