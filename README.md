# Terraform Notes

* personal notes for terraform

## Intro to Terraform

* infrastructure as code
    * you want to spin up some machine, you can either go to console and start up instances
    * automates your infrastructure
    * keeps infrastructure in a certain state
        * for example you want to always run 5 instances of something even if you try to manually change it 
        * eg. 2 web instances with 2 volumnes, and load balancers, terraform willl ensure that this state is consistent
    * make your infrastructure auditable
        * you can keep your infrastructure history in git, so there is a paper trail
    * **keep machines in compliance** in a certain state is what terraform does
    * you can use ansible, chef, puppet, saltstack to configure and install
    * you can use aws to run terraform and when hardware is available, we then use ansible to install and configure

## installing terraform

* https://developer.hashicorp.com/terraform/downloads

## Terraform basics

* Understanding Terraform HCL

* terraform files ends with .tf

* main.tf

```hcl
variable "myvar" {
    type = "string"
    default = "hello terraform"
}
```

* running terraform console

```
~$terraform console
> var.myvar
hello terraform
>"${var.myvar}"
hello terraform
>exit
```

* modifying main.tf with map

```hcl
variable "myvar" {
    type = "string"
    default = "hello terraform"
}
variable "mymap" {
    type = map(string)
    default = {
        mykey = "my value"
    }
}
```

```
~$terraform console
> var.myvar
hello terraform
>var.mymap
{
    "mykey" = "my value"
}
> var.mymap["mykey"]
my value
>exit
```
* adding list to main.tf

```hcl
variable "myvar" {
    type = "string"
    default = "hello terraform"
}
variable "mymap" {
    type = map(string)
    default = {
        mykey = "my value"
    }
}

variable "mylist" {
    type = list
    default = [1,2,3]
}
```

```
~$terraform console
> var.mylist
[
    1,
    2,
    3,
]
>var.mylist[0]
1
>element(var.mylist,1)
2
>slice(var.mylist,0,2)
[
    1,
    2,
]
>exit
```

* resource.tf
* when you are creating a resource you need to specify a provider

```hcl
provider "aws" {

}

variable "AWS_REGION" {
    type = string
}

resource "aws_instance" "example" {
    ami = var.AMIS[var.AWS_REGION]
    instance_Type = "t2.micro"
}
```

* ami: The AMI resource allows the creation and management of a completely-custom Amazon Machine Image (AMI)

* t2,micro is a amazon ec2 t2 type of instance

* you can make a terrafor.tfvars file

```hcl
AWS_REGION="eu-west-1"
```

* run terraform init
    * need to run init to download provider

* then run terraform console

```
>var.AWS_REGION
eu-west-1
>exit
```

* will also create a terraform state called: terraform.tfstate

* adding AMIS variable to resource.tf

```hcl
provider "aws" {

}

variable "AWS_REGION" {
    type = string
}

variable "AMIS" {
    type = map(string)
    default = {
        eu-west-1 = "my ami"
    }
}

resource "aws_instance" "example" {
    ami = var.AMIS[var.AWS_REGION]
    instance_Type = "t2.micro"
}
```

```
>var.AMIS[var.AWS_REGION]
my ami
```

* adding more instances

```hcl
provider "aws" {

}

variable "AWS_REGION" {
    type = string
}

variable "AMIS" {
    type = map(string)
    default = {
        eu-west-1 = "my ami"
    }
}

resource "aws_instance" "example1" {
    ami = var.AMIS[var.AWS_REGION]
    instance_Type = "t2.micro"
}

resource "aws_instance" "example2" {
    ami = var.AMIS[var.AWS_REGION]
    instance_Type = "t2.small"
}

resource "aws_instance" "example3" {
    ami = var.AMIS[var.AWS_REGION]
    instance_Type = "t2.micro"
}
```

* make a vars.tf to keep variables
* terraform.tfvars will have the values

* everytime you change provider, or you're using a module or plugin you will have to run terraform init again

