# ElasticSearch AWS Cluster

This project is the special configuration for AWS Elastic Beanstalk service. It
uses empty java-8 service and installs proper ElasticSearch version during
deployment stage.

## Prerequisites

Make sure you have installed

 - AWS Beanstalk [CLI Tools](http://docs.aws.amazon.com/elasticbeanstalk/latest/dg/eb-cli3.html)

## Deployment

### Init the EB application

```bash
$ eb init
```

**Please remember that our default application name is `elasticsearch-cluster`**

### Configuration

Few environment variables have to be specified before deployment

 - `CLUSTER_NAME`: This is a name of your new shiny ElasticSearch cluster, please avoid use of `elasticsearch` name
 - `AWS_KEY_ID`: AWS Key ID
 - `AWS_KEY`: AWS Secret Key
 - `AWS_REGION`: Region in which ES cluster will be created
 - `EC2_TAG_NAME`: This value should be equal to the AWS Name tag (same as environment name)
 - `MASTER_NODES`: Amount of master nodes. The rule is simple, this number should equal to total number of nodes (N) divided by 2 plus 1. `N / 2 + 1`.
 - `PORT`: Should always be set to 9200, unless you changed ES http port

### Usage of `.env.` file

See example in `.env.example` file

### Create new cluster

In order to create new cluster you need to execute following bash commands

```bash
$ ENV_VARS=$(cat .env | xargs | sed 's/ /,/g')
$ eb create -c 2pventures-elasticsearch-staging --envvars ${ENV_VARS} --platform=java-8 -i m3.large --scale 4 elasticsearch-staging
```

Where `2pventures-elasticsearch-staging` is a CNAME; `elasticsearch-staging` is the environment name; `m3.large` is a instance type and `--scale 4` is how many nodes to create

### Configure AWS Security Groups

After environment is created you need to change AWS Security Group rules. You have to allow all 9300-9400 TCP and ICMP traffic within SG group.

### Deploy changes

```bash
$ eb deploy elasticsearch-staging
```

Where `elasticsearch-staging` is a environment name

## Important Notes

 - Do not scale down the cluster during peak times, this will cause cluster move shards around also some pending requests may fail
 - When adding new nodes to cluster some requests may fail
 - When added new nodes to cluster remember to change `MASTER_NODES` var
