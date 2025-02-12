 gcp-load-balancer-challenge

Challenge scenario

You have started a new role as a Junior Cloud Engineer for Jooli, Inc. You are expected to help manage the infrastructure at Jooli. Common tasks include provisioning resources for projects.

You are expected to have the skills and knowledge for these tasks, so step-by-step guides are not provided.

Some Jooli, Inc. standards you should follow:

1. Create all resources in the default region or zone, unless otherwise directed. The default region is `$REGION`, and the default zone is `$ZONE`.

2. Naming normally uses the format team-resource; for example, an instance could be named **nucleus-webserver1**.

3. Make sure to create an instance template in global location.

4. Allocate cost-effective resource sizes. Projects are monitored, and excessive resource use will result in the containing project's termination (and possibly yours), so plan carefully. This is the guidance the monitoring team is willing to share: unless directed, use **e2-micro** for small Linux VMs, and use **e2-medium** for Windows or other applications, such as Kubernetes nodes.

## Task 1. Create a project jumphost instance

You will use this instance to perform maintenance for the project.

Requirements:

- Name the instance `$JUMPHOST`.
- Create the instance in the `$ZONE` zone.
- Use an **e2-micro** machine type.
- Use the default image type (Debian Linux).

`ON THE GOOGLE CLOUD SHELL:`
```shell
# VARS
export REGION=___
export ZONE=___
export JUMPHOST=___

# SETUP DEFAULTS
gcloud config set compute/region $REGION
gcloud config set compute/zone $ZONE

# TASK 1 - JUMPHOST VM
gcloud compute instances create nucleus-jumphost-835 \
    --zone=$ZONE \
    --machine-type=e2-micro
```

## Task 2. Set up an HTTP load balancer

You will serve the site via nginx web servers, but you want to ensure that the environment is fault-tolerant. Create an HTTP load balancer with a managed instance group of 2 nginx web servers. Use the following code to configure the web servers; the team will replace this with their own configuration later.

```shell
cat << EOF > startup.sh
#! /bin/bash
apt-get update
apt-get install -y nginx
service nginx start
sed -i -- 's/nginx/Google Cloud Platform - '"\$HOSTNAME"'/' /var/www/html/index.nginx-debian.html
EOF
```

> Note: There is a limit to the resources you are allowed to create in your project, so do not create more than 2 instances in your managed instance group. If you do, the lab might end and you might be banned.

You need to:

1. Create an `instance template`. Don't use the default machine type. Make sure you specify **e2-medium** as the machine type and create the **Global** template.
2. Create a `managed instance group` based on the template.
3. Create a `firewall rule` named as `$FWRULE` to allow traffic (80/tcp).
4. Create a `health check`.
5. Create a `backend service`, and add your instance group as the backend to the backend service group with named port (http:80).
6. Create a `URL map`, and target the HTTP proxy to route the incoming requests to the default backend service.
7. Create a `target HTTP proxy` to route requests to your URL map
8. Create a `forwarding rule`.

> Note: You may need to wait for 5 to 7 minutes to get the score for this task.
