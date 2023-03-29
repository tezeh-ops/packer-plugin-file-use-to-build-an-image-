# packer-plugin-file-use-to-build-an-image-
# for installation on your local   https://developer.hashicorp.com/packer/tutorials/aws-get-started/get-started-install-cli

#https://developer.hashicorp.com/packer/tutorials/aws-get-started/aws-get-started-build-image


# Write Packer template
# A Packer template is a configuration file that defines the image you want to build and how to build it. Packer templates use the Hashicorp Configuration 3 #Language (HCL).

#Create a new directory named packer_tutorial. This directory will contain your Packer template for this tutorial.

 mkdir packer_tutorial
 
#Copy
#Navigate into the directory.

 cd packer_tutorial
 
#Copy
#Create a file aws-ubuntu.pkr.hcl, add the following HCL block to it, and save the file.

packer {
  required_plugins {
    amazon = {
      version = ">= 0.0.2"
      source  = "github.com/hashicorp/amazon"
    }
  }
}

source "amazon-ebs" "ubuntu" {
  ami_name      = "learn-packer-linux-aws"
  instance_type = "t2.micro"
  region        = "us-west-2"
  source_ami_filter {
    filters = {
      name                = "ubuntu/images/*ubuntu-xenial-16.04-amd64-server-*"
      root-device-type    = "ebs"
      virtualization-type = "hvm"
    }
    most_recent = true
    owners      = ["099720109477"]
  }
  ssh_username = "ubuntu"
}

build {
  name    = "learn-packer"
  sources = [
    "source.amazon-ebs.ubuntu"
  ]
}

/*
#This is a complete Packer template that you will use to build an AWS Ubuntu AMI in the us-west-2 region. In the following sections, you will review each #block of this template in more detail.



#Packer Block
#The packer {} block contains Packer settings, including specifying a required Packer version.


In addition, you will find required_plugins block in the Packer block, which specifies all the plugins required by the template to build your image. Even though Packer is packaged into a single binary, it depends on plugins for much of its functionality. Some of these plugins, like the Amazon AMI Builder (AMI builder) which you will to use, are built, maintained, and distributed by HashiCorp — but anyone can write and use plugins.


Each plugin block contains a version and source attribute. Packer will use these attributes to download the appropriate plugin(s).

The source attribute is only necessary when requiring a plugin outside the HashiCorp domain. You can find the full list of HashiCorp and community maintained builders plugins in the Packer Builders documentation page.
The version attribute is optional, but we recommend using it to constrain the plugin version so that Packer does not install a version of the plugin that does not work with your template. If you do not specify a plugin version, Packer will automatically download the most recent version during initialization.
In the example template above, Packer will use the Packer Amazon AMI builder plugin that is greater than version 0.0.2.



Source block
The source block configures a specific builder plugin, which is then invoked by a build block. Source blocks use builders and communicators to define what kind of virtualization to use, how to launch the image you want to provision, and how to connect to it. Builders and communicators are bundled together and configured side-by-side in a source block. A source can be reused across multiple builds, and you can use multiple sources in a single build. A builder plugin is a component of Packer that is responsible for creating a machine and turning that machine into an image.


A source block has two important labels: a builder type and a name. These two labels together will allow us to uniquely reference sources later on when we define build runs.


In the example template, the builder type is amazon-ebs and the name is ubuntu.
*/

source "amazon-ebs" "ubuntu" {
  ami_name      = "learn-packer-linux-aws"
  instance_type = "t2.micro"
  region        = "us-west-2"
  source_ami_filter {
    filters = {
      name                = "ubuntu/images/*ubuntu-xenial-16.04-amd64-server-*"
      root-device-type    = "ebs"
      virtualization-type = "hvm"
    }
    most_recent = true
    owners      = ["099720109477"]
  }
  ssh_username = "ubuntu"
}


/*
Each builder has its own unique set of configuration attributes. The amazon-ebs builder launches the source AMI, runs provisioners within this instance, then repackages it into an EBS-backed AMI.

In the example template, the amazon-ebs builder configuration launches a t2.micro AMI in the us-west-2 region using an ubuntu:xenial AMI as the base image, then creates an image named learn-packer-linux-aws from that instance. The builder will create all the temporary resources necessary (for example, keypairs, security group rules, etc..) to temporarily access the instance while the image is being created.

This example template also uses the SSH communicator. By specifying the ssh_username attribute, Packer is able to SSH into EC2 instance using a temporary keypair and security group to provision your instances. The next tutorial walks you through modify your AMI using provisioners.

The Build Block
The build block defines what Packer should do with the EC2 instance after it launches.

In the example template, the build block references the AMI defined by the source block above (source.amazon-ebs.ubuntu).
*/

build {
  sources = [
    "source.amazon-ebs.ubuntu"
  ]
}


/*
Tip

In later tutorials, you will add the provision block (define additional provisioning steps) and post-process block (define what do to with the build artifacts) to the build block.

Authenticate to AWS
Before you can build the AMI, you need to provide your AWS credentials to Packer. These credentials have permissions to create, modify and delete EC2 instances. Refer to the documentation to find the full list IAM permissions required to run the amazon-ebs builder.

To alow Packer to access your IAM user credentials, set your AWS access key ID as an environment variable.
*/

 export AWS_ACCESS_KEY_ID="<YOUR_AWS_ACCESS_KEY_ID>"
Copy
Now set your secret key.

 export AWS_SECRET_ACCESS_KEY="<YOUR_AWS_SECRET_ACCESS_KEY>"
