# Ansible EC2 Termination Playbook

This repository contains an Ansible playbook that queries and terminates EC2 instances based on a filter.

## Prerequisites

Before running the playbook, decide if you want to run the playbook via ansible or from within a container via podman.

### 1. Ansible Standalone

1. **ansible**: Ensure you have ansible installed by running:

```bash
ansible --version
```

If ansible is not installed, check the installation guide [here] (https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#pipx-install) and follow the instructions.

2. **boto3 and botocore**:
   These Python libraries allow us to interact with AWS Services.

```bash
pip3 install boto3 botocore
```

3. **AWS Credentials**:

   1. If you need to create credentials got to [AWS IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html) and follow the instructions.
   2. Once you valid AWS credentials to interact with your EC2 instances. Ensure the following environment variables are set:

   - `AWS_ACCESS_KEY_ID`
   - `AWS_SECRET_ACCESS_KEY`
   - `AWS_REGION`
   - `AWS_USERNAME`

   You can set these environment variables using the following commands (replace with your actual values):

   ```bash
   export AWS_ACCESS_KEY_ID=your_access_key_id
   export AWS_SECRET_ACCESS_KEY=your_secret_access_key
   export AWS_REGION=your_aws_region
   export AWS_USERNAME=your_aws_username
   ```

## Usage

`Cd` to directory where the playbook lives on your machine.

Run the playbook using the following command:

> [!WARNING]
> Be careful when running this playbook. It will terminate all EC2 instances that match the filter.

```bash
ansible-playbook terminate_ec2_instances.yml -e "region=$AWS_REGION filter=$AWS_USERNAME AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY"
```

### 2. Podman

1. **Podman**: Install Podman on your system. Follow the [Podman installation guide](https://podman.io/getting-started/installation) if it's not already installed.

2. **AWS Credentials**:

   1. If you need to create credentials got to [AWS IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html) and follow the instructions.
   2. Once you valid AWS credentials to interact with your EC2 instances. Ensure the following environment variables are set:

   - `AWS_ACCESS_KEY_ID`
   - `AWS_SECRET_ACCESS_KEY`
   - `AWS_REGION`

   You can set these environment variables using the following commands (replace with your actual values):

   ```bash
   export AWS_ACCESS_KEY_ID=your_access_key_id
   export AWS_SECRET_ACCESS_KEY=your_secret_access_key
   export AWS_REGION=your_aws_region
   ```

3. Build the Podman container image using the following command:

   1. Assuming the Dockerfile is in the current directory, run the following command to build the container image:

   ```bash
   podman build -t terminate_ec2_instances:local .

   ```

4. Verify the container image is built successfully by running the following command:

```bash
podman images
```

## Usage

Run the playbook using the following command:

Add `your_user_name` to filter the instances you want to terminate.

> [!WARNING]
> Be careful when running this playbook. It will terminate all EC2 instances that match the filter.

```bash
podman run --rm -it \
    -e AWS_ACCESS_KEY_ID="$AWS_ACCESS_KEY_ID" \
    -e AWS_SECRET_ACCESS_KEY="$AWS_SECRET_ACCESS_KEY" \
    -e AWS_REGION="$AWS_REGION" \
    -v "$(pwd)":/playbook \
    terminate_ec2_instances:local \
    ansible-playbook /playbook/terminate_ec2_instances.yml \
    -e "filter=your_use_name region=$AWS_REGION"
```
