https://tekanaid.com/posts/terraform-for-beginners-course-and-training

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

This destroy command is used to destroy the resources created by Terraform. It deletes the resources defined in the configuration files and also removes these resources from the state file. It’s important to be careful when using this command as it can delete important resources and cause data loss. It’s always recommended to review the plan generated by the `terraform destroy` command before proceeding.
