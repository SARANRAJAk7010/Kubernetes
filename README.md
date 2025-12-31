# Kubernetes

1. Launch a Kubernetes Cluster
Launch EC2 instances and connenct to instances via EC2 instance connect

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

-> Cant able to access Kubernetes cluster even after allowing AdministratorAccess, Need to have Allow Access. Coz k8s follow RBAC  

Create access entry -> Under IAM Principal, select yours -> Under Policy name, select "AmazonEKSClusterAdminPolicy" -> select Add Policy 

2. Pull the code for your Backend
$git clone <github URL>

-> Install git
$ sudo dnf update
$ sudo dnf install git -y

3. Build a container Image for your Backend
$ docker build -t nextwork-flask-backend .

-> Install docker
$ sudo yum install -y docker

#error, Docker needs root level user to build docker.
$ whoami
op : ec2user

-->Add ec2-user to the Docker group:
$ sudo usermod -a -G docker ec2-user

$ docker build -t nextwork-flask-backend .

4. Push Your Container Image to Amazon ECR
$ aws ecr create-repository \
  --repository-name nextwork-flask-backend \
  --image-scanning-configuration scanOnPush=true \

5. Push your container image to ECR
ECR console -> created new repo -> Select, "view push commands"
RUn the 4 commands in your EC2 instance connect

- write deployment.yaml
- write service.yaml

-> Install Kubectl 
$ sudo curl -o /usr/local/bin/kubectl \
https://s3.us-west-2.amazonaws.com/amazon-eks/1.31.0/2024-09-12/bin/linux/amd64/kubectl


$ kubectl apply -f flask-deployment.yaml
$ kubectl apply -f flask-service.yaml

$ sudo chmod +x /usr/local/bin/kubectl

If find an error :
$ aws eks update-kubeconfig --name cluster-name --region your-region-code
