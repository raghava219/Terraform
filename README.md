# Terraform Overview

https://www.youtube.com/watch?v=HbG3zVBVYvM

https://tekanaid.com/posts/terraform-for-beginners-course-and-training

## Agenda:

What is Infarastructure as code(IAC) ?

What is Terraform

Hands-on Lab - Docker Example, AWS Example

Infastructure As Code (IAC) :

IAC is the process of managing and provisioning infrastructure with machine-readbale definition files. 

Think of it as executable documentation.

Advantages of IaC

> It provides **configuration consistency** and **standardiation** for deployments.
> It is **simple to learn** and **write** as it is more like **configuration** than development code.
> It allows you to turn **manual** tasks into simple **automated** deployments.
> You can create **reusable modules** for easy **sharing** and **collaboration**.
> You can **modify** existing **infrastructure** safely by using a **"dry run"** before applying changes.
> You can **integrate** with current application **CI/CD workflow pipelines** for more mature automated deployments.
> It creates a **blueprint** for your **environment**
> It is **consistent, repetable, idempotent** and **predictable**. 
> Allows you to recreate an applcation's infrastructure for **disaster recovery**.

Terraform Overview

Terraform is a **stateful** application. State is a **necessary** requirement for **Terraform to function**.
**State** is kind of a **database** that allows Terraform to **map** its **configuration** to the **real-world**.
Terraform is a **cloud-agnostic tool (public or private)** and therefore isn't limited to a single cloud provider, such as AWS **CloudFormation** or **Azure Resource Manager**.
Terrafor allows IT organizations to **focus** on learning a **single tool** for deploying its infrastucture, regardless of what platform it's being deployed on. (**Multi-cloud deployment**)


Other Tools

These tools work well form configuration the operating system and applcation. 
They are not purpose-built for provisioning cloud infrastructure and platform services. 

**CHEF, puppet, ANSIBLE, PowerShell, BASH**

Native Cloud Provisioning Tools

Each **cloud** has its own **YAML or JSON** based **provisioning** tool.
**Terraform** can be used **across all** major **cloud** providers and **VM hypervisors.** 

## Basic Commands:

Below are some of the most important commands to get started with Terraform.

### **terraform init**

This command is used to initialize a Terraform working directory. It is the first command that should be run after writing a new configuration or cloning an existing one. The command downloads the necessary plugins and verifies that the configuration is syntactically correct.

### **terraform fmt**

This command is used to format the configuration files. It automatically updates the indentation and whitespace of the configuration files to make them more readable and consistent.

### **terraform validate**

This command is used to validate the syntax of the configuration files. It checks for any errors or issues in the configuration files that would prevent Terraform from running properly.

### **terraform plan**

This command is used to create an execution plan for the configuration. It shows the resources that will be created, updated, or deleted and the changes that will be made. It also allows you to review the plan before making any changes to the infrastructure.

### **terraform apply**

This command is used to apply the changes described in the configuration files. It creates, updates, or deletes the resources defined in the configuration files to match the desired state.

### **terraform destroy**

This destroy command is used to destroy the resources created by Terraform. It deletes the resources defined in the configuration files and also removes these resources from the state file. It???s important to be careful when using this command as it can delete important resources and cause data loss. It???s always recommended to review the plan generated by the??`terraform destroy`??command before proceeding.


## Deploy Cloud Functions on GCP with Terraform

https://towardsdatascience.com/deploy-cloud-functions-on-gcp-with-terraform-111a1c4a9a88

