

**Flow**

![[Pasted image 20250315221146.png]]

**The Terraform GitHub Repo** :

![[Pasted image 20250315222313.png]]



**Creating The IAM User**

![[Pasted image 20250315222826.png]]



**Adding the secret credentials to Jenkins**


![[Pasted image 20250315230345.png]]






**Creating/Configuring the automation Jenkins pipeline**


![[Pasted image 20250315230304.png]]

**Explanation of the Jenkinsfile written in groovy scripting**




![[Pasted image 20250316185407.png]]


This `Jenkinsfile` defines a Jenkins pipeline script for automating the deployment of Terraform configurations. Here's a detailed explanation of each part of the file:

### Pipeline Definition

- **pipeline**: This block defines the entire pipeline structure.

### Parameters

- **parameters**: This block defines parameters that can be used to control the pipeline's behavior.
    - **booleanParam**: Defines a boolean parameter named `autoApprove` with a default value of `false`. It is used to determine if the `apply` stage should run automatically after generating the plan.

### Environment

- **environment**: This block sets up environment variables.
    - **AWS_ACCESS_KEY_ID** and **AWS_SECRET_ACCESS_KEY**: These are set using Jenkins credentials, and they will be used for AWS authentication within the pipeline.

### Agent

- **agent any**: Specifies that the pipeline can run on any available Jenkins agent.

### Stages

The pipeline consists of several stages:

1. **Checkout**
    
    - **stage('Checkout')**: This stage checks out the code from the Git repository.
        - **steps**: Contains the steps to be executed in this stage.
            - **script**: Executes the steps within a Groovy script.
                - **git**: Checks out the code from the specified branch (`main`) of the repository.
                - **sh**: Runs shell commands to create a directory named `terraform` and copy all files into this directory.
2. **Plan**
    
    - **stage('Plan')**: This stage initializes Terraform and generates a plan.
        - **steps**: Contains the steps to be executed in this stage.
            - **sh**: Runs shell commands to:
                - Initialize Terraform (`terraform init`).
                - Generate a Terraform plan and save it as `tfplan` (`terraform plan -out tfplan`).
                - Show the plan and save the output to `tfplan.txt` (`terraform show -no-color tfplan > tfplan.txt`).
3. **Approval**
    
    - **stage('Approval')**: This stage waits for manual approval before applying the plan.
        - **when**: Specifies the condition under which this stage will run.
            - **not**: Inverts the condition.
                - **equals**: Checks if the `autoApprove` parameter is not equal to `true`.
        - **steps**: Contains the steps to be executed in this stage.
            - **script**: Executes the steps within a Groovy script.
                - **readFile**: Reads the content of `tfplan.txt`.
                - **input**: Prompts for manual input, displaying the plan and asking if the user wants to apply it.
4. **Apply**
    
    - **stage('Apply')**: This stage applies the Terraform plan.
        - **steps**: Contains the steps to be executed in this stage.
            - **sh**: Runs a shell command to apply the Terraform plan without asking for input (`terraform apply -input=false tfplan`).

This pipeline automates the process of checking out code, generating a Terraform plan, and optionally applying it after manual approval. The use of parameters and environment variables allows for flexibility and secure handling of sensitive information like AWS credentials.