# aws_list: quickly and easily list all of your ec2 instances
Managing multiple AWS accounts can sometimes be tough, even when doing something as simple as matching a private IP address with a hostname.

In the past I used the aws cli tools, but I constantly switched both the accounts and the regions when making requests:

```
# Make it's in the prod-5 account in us-west-1
aws ec2 --profile=prod-5 --region=us-west-1 | grep my_instance

# Okay not that account or region, let's try eu-west-1
aws ec2 --profile=prod-5 --region=eu-west-2 | grep my_instance
```

Repeat 1x for each account and region. As you can imagine this is extremely time consuming, even when using the CLI tools.

aws_list will find every single instance you can view using every account available to you (according to your ~/.aws/config). The aggregate results can then be searched.

I choose to use the existing ~/.aws/config file so that it works along side your normal aws cli tools. 

This is a very good use-case for Golang, which has nice concurrency primitives. I have five accounts and this script (once compiled) will display all of the results in under 1.5 seconds. Not bad.

The result is a space-separated list with some additional values added. It should be easy to find what you're looking for:

```
$ ./aws_list
i-71930187 prod-manage005 10.0.0.180 t2.micro 207.171.166.22 prod-account
i-71930187 stage-controller7 10.0.0.164 t2.medium 72.21.206.80 staging-account
i-71930187 prod-vpn01 10.0.0.239 m4.large None prod-account
i-71930187 stephen-test-host 10.0.0.216 m3.large 216.58.216.174 test-account
...
```

The output is a space-separated file and only "running" and "pending" instances are displayed. For a list of filters or instance attributes consult the official [documentation](http://godoc.org/github.com/awslabs/aws-sdk-go/service/ec2#Instance).
