# Deploy Cluster

This directory contains the script and [guide](https://github.com/MincYu/pheromone/blob/main/deploy/cluster/docs/getting-started-aws.md) for creating a Pheromone cluster, which is in part from [Hydro cluster](https://github.com/hydro-project/cluster).

# 版本问题集合

1. aws ec2 更改为 ubuntu Ubuntu Server 22.04 LTS (HVM), SSD Volume Type

# 操作集合

ssh ec2之前要在安全组上新增入站规则ssh 22 0.0.0.0/0

sudo apt update
sudo apt install git
sudo apt install unzip
git version

sudo yum install python3-pip
pip3 install awscli boto3 kubernetes

## kubectl
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"

sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl

## kops
curl -Lo kops https://github.com/kubernetes/kops/releases/download/$(curl -s https://api.github.com/repos/kubernetes/kops/releases/latest | grep tag_name | cut -d '"' -f 4)/kops-linux-amd64
chmod +x ./kops
sudo mv ./kops /usr/local/bin/

## awscli
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install

## starter主机的
[ec2-user@ip-172-31-82-212 ~]$ aws iam create-access-key --user-name kops
{
    "AccessKey": {
        "UserName": "kops",
        "AccessKeyId": "创建的密钥ID",
        "Status": "Active",
        "SecretAccessKey": "创建的密钥",
        "CreateDate": "2023-08-14T03:07:06+00:00"
    }

将如下环境变量放入/etc/profile并source /etc/profile    

export PHERO_HOME=/home/ubuntu/pheromone
export KOPS_STATE_STORE=s3://starter-example-com-state-store
export PHERO_CLUSTER_NAME=raids-buaa.net

export AWS_ACCESS_KEY_ID=创建的密钥ID
export AWS_SECRET_ACCESS_KEY=创建的密钥

## 修改pheromone权限+x

cd ~/pheromone/deploy/cluster/
// 1 memory tier node, 1 routing node, 2 coordinator, 3 function
python3 -m deploy.cluster.create_cluster -m 1 -r 1 -c 2 -f 3

## 输出结果

ubuntu@ip-172-31-41-103:~/pheromone/deploy/cluster$

ubuntu@ip-172-31-41-103:~/pheromone/deploy/cluster$ python3 -m deploy.cluster.create_cluster -m 1 -r 1 -c 2 -f 3
Creating cluster object...
Adding general instance group
Creating cluster on AWS...
Using cluster from kubectl context: raids-buaa.net

kOps has set your kubectl context to raids-buaa.net
Validating cluster...
Creating management pods...
Creating management service...
Unable to connect to the server: dial tcp: lookup domainName on 127.0.0.53:53: no such host
error: /home/ubuntu/.ssh/id_rsa doesn't exist in local filesystem
error: /home/ubuntu/.ssh/id_rsa.pub doesn't exist in local filesystem
Unable to connect to the server: dial tcp: lookup domainName on 127.0.0.53:53: no such host
Unable to connect to the server: dial tcp: lookup domainName on 127.0.0.53:53: no such host
Creating 1 routing nodes...
Adding 1 routing server node(s) to cluster...
Validating cluster...

Creating 1 memory, 0 ebs node(s)...
Adding 1 memory server node(s) to cluster...
Adding 0 ebs server node(s) to cluster...
Validating cluster...
Creating routing service...
Adding 2 coordinator nodes...
Adding 2 coordinator server node(s) to cluster...
Validating cluster...
Coordinator ips: ['xxx.xxx.xxx.xxx', 'xxx.xxx.xxx.xxx']
Adding 3 function nodes...
Adding 3 function server node(s) to cluster...
Validating cluster...
Adding 0 sender/client nodes...
Adding 0 sender server node(s) to cluster...
Validating cluster...
Finished creating all pods...
Authorizing ports for routing service...
Anna the kvs service can be accessed here:
        xxx.us-east-1.elb.amazonaws.com
Pheromone can be accessed here:
        xxx.us-east-1.elb.amazonaws.com