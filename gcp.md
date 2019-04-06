---
layout: page
title: Google Cloud Platform Setup
description: Guides to setup Google Cloud Platform (GCP) for course homework and project.
---

## Before You Start
---

Use a non-Stanford Google account.  
Redeem your GCP credits provided by the course.  
Google and Piazza are always helpful.

## Prepare your Google Cloud Platform account
---

### Create a new project named cme-213 (or any name you prefer)

You can create and manage your GCP projects on the [Resource Management Page](https://console.cloud.google.com/cloud-resource-manager).

### [hw3 and after] Request GPU quotas
Google Cloud Platform requires you to request GPU quotas before you can create virtual machines with GPUs. Quota requests have to be done
through the [Quotas Page](https://console.cloud.google.com/iam-admin/quotas).

You need two types of quotas throughout the course:
* `GPUs (all regions)`
* `NVIDIA K80 GPUs` at location `us-west1`
and you need at least 4 for both of them.

To request GPU quotas:
![quota](quota.png)
1. Go to [Quotas Page](https://console.cloud.google.com/iam-admin/quotas) (picture above).
2. Click on the drop-down box under `Metric` and type "GPU" in the search bar. Check <br> `GPUs (all regions)` and `NVIDIA K80 GPUs`.
3. Click on the drop-down box under `Location` and check `Global` and `us-west1`.
4. You will find the two quotas needed for the course appearing on the page. Check both of them.
5. Click `EDIT QUOTAS` button on top of the page.
6. Fill the quota request form to request at least 4 quotas for both items.

You will not be charged for requesting quotas.
It can take up to 48 hours to process your request, so we suggest requesting your quotas as early as possible.

## Install Google Cloud SDK on your local machine
---
Google Cloud SDK (`gcloud`) is a software package that allows you to manage your GCP projects from your local machine through terminals.
We are going to use `gcloud` to run scripts that will create the correct virtual machines for you.

`gcloud` can be installed on a variety of operating systems including macOS and Windows.  
Installation procedures can be found [here](https://cloud.google.com/sdk/docs/downloads-interactive).

## Manage virtual machine
---

You should use separate virtual machines for different homework and project. Environment is setup for each virtual machine by the script we provide.

### Create your virtual machine
1. Start a terminal
2. `cd` to your starter code directory 
3. Run `./create_vm.sh`

You will see something like this if success
```
Updated property [compute/zone].
Created [https://www.googleapis.com/compute/v1/projects/cme-213/zones/us-west1-b/instances/hw2].
NAME  ZONE        MACHINE_TYPE  PREEMPTIBLE  INTERNAL_IP  EXTERNAL_IP    STATUS
hw2   us-west1-b  n1-highcpu-8               10.138.0.24  35.197.115.51  RUNNING
Installing necessary libraries. You will be able to log into the VM after several minutes with:
gcloud compute ssh hw2
```

You should remember the name of your VM (`hw2` in this case) and use it for `NAME` in the commands below.

**Billing starts now!!!**

### Start your virtual machine
Your virtual machine should start automatically after you created it. However you will need to manually start your machine if you stopped it.  

To start a virtual machine:
1. Start a terminal
2. Run `gcloud compute instances start NAME`

or through [Compute Page](https://console.cloud.google.com/compute/).

**Billing starts now!!!**

### Log into your virtual machine
You can only log into your VM after it is started.

To log into your virtual machine
1. Start a terminal
2. Run `gcloud compute ssh NAME`

or through [Compute Page](https://console.cloud.google.com/compute/).

You might need to wait for a while if you see the following message. It's likely we are installing necessary packages for the homework/project and disabled ssh.
```
ssh: connect to host 35.197.115.51 port 22: Connection refused
ERROR: (gcloud.compute.ssh) [/usr/bin/ssh] exited with return code [255].
```

### Stop your virtual machine
We manage to setup an auto-shutdown service on your VMs through `create_vm.sh`. It will automatically stop your VM after 30 minutes of disconnection. However
it won't work if you use tools that keep you logged in, like `tmux` or `screen`. So
**Stop your VM when you are not using it!!!**

To stop your virtual machine
1. Start a terminal
2. Run `gcloud compute instances stop NAME`

or through [Compute Page](https://console.cloud.google.com/compute/).

### Delete your virtual machine
Your don't usually need to delete virtual machines --- you only need to stop them. However if you mess things up you can always start over by deleting and recreating your virtual machine.

To delete your virtual machine
1. Start a terminal
2. Run `gcloud compute instances delete NAME`

or through [Compute Page](https://console.cloud.google.com/compute/).

### Transfer files between local machine and VMs
It is recommended to use `scp` to transfer files to/from your virtual machine:

To transfer to your VM:
1. Start a terminal
2. Run `gcloud compute scp LOCAL_PATH NAME:VM_PATH`, where `LOCAL_PATH` is the path to your local file and `VM_PATH` is the VM destination your file will appear

To transfer from your VM:
1. Start a terminal
2. Run `gcloud compute scp NAME:VM_PATH LOCAL_PATH`, where `VM_PATH` is the path of file on your VM and  `LOCAL_PATH` is the local destination

You can use `--recurse` flag to transfer a directory. For example:  
`gcloud compute scp --recurse ./starter_code hw2:~/`  
will transfer the local `starter_code` folder to your hw2 virtual machine.


### Other helpful commands
`gcloud` is powerful and allows you to do about anything about your cloud platform. Here are some useful commands:
* `gcloud compute instances list` will list all of your VMs
* `gcloud compute instances reset NAME` will reset your VM (should be equivalent as delete then create)
* `gcloud compute --help` will show help for `gcloud compute`

Remember you can always manage your VMs from the [Compute Page](https://console.cloud.google.com/compute/) if you are not comfortable with command line tools.

### Keep a mind on your credits
You can see how you are doing with your credits on the [Billing Page](https://console.cloud.google.com/billing/).

Here we provide a table of specs for each type of virtual machine used for homework and project:

|           | CPU cores | Memory     | Disk        | GPU type    | GPU number | Hourly cost |
| --------- | :-------: | :--------: | :---------: | :---------: | :--------: | :---------: |
| HW2       | 8         | 7.2GB      | 10GB        | -           | -          | $0.199      |


## Remote Editor
---
It is cumbersome to edit locally and run remotely, if you are not comfortable with command-line editors like `vim` or `emacs`. Typically you would have to manually sync between your local copy and the remote one.

We manage to find this VS Code extension that can help you edit your remote files locally on VS Code. Here we provide a brief tutorial on how to setup your remote editor. It is **not required** to use VS Code for course homework or project.

### Download VS Code and install Remote VSCode extension

VS Code can be downloaded from it's [Official Website](https://code.visualstudio.com/), and Remote VSCode extension is [here](https://marketplace.visualstudio.com/items?itemName=rafaelmaiolla.remote-vscode). Installation should be straightforward.

### Start VS Code server

1. Open VS Code.
2. Start the server in the command palette - Press `F1` and type `Remote: Start server`, and press `ENTER` to start the server. You may see a Starting server at the status bar in the bottom.

### Open a remote file

1. Make sure your VS Code server is running.
2. Start a terminal.
3. Connect to your VM with: `gcloud compute ssh NAME --ssh-flag="-R 52698:localhost:52698"`
4. Run `rmate FILENAME` on your VM, where `FILENAME` is path to the file you want to edit.
5. Find your remote file opened in VS Code.

Note:
* You can repeat step 4 to open multiple files concurrently.
* Make sure to save and close your file before disconnecting your VM.