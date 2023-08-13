# GitHub Action CI/CD workflow Setup Guide #
## Continuous deployment to a Docker container, protected by Tailscale, and powered by GitHub ##
![Let your development flow!](assets/flow.jpg "=400x300" "riverflow")
Introduction: The content of this guide is purely for educational purposes and comes with no warranty whatsoever.  
The steps below outline the process of setting up a development workflow that deploys changes to a simple app running on docker.  

The docker architecture is a very simple two container setup.   
First, a simple Python Flask app is created as an example - then an NGINX container is used to proxy requests.  

This architecture allows the user to quickly integrate HTTPS, load balancing, and other features provided by NGINX.  

The hope is to push changes to the app quickly, without having to expose the host (hypervisor) to the public internet.  


### section 1: Pre-Requisites and assumptions ###
- A GitHub account with GitHub Actions enabled
- A GitHub repository - to store app source code, workflow files, and authentication credentials
- A GitHub API token with workflows permission
- Development server running ssh server, and connected to tailscale.  
- required packages/services:  
	- openssh-server  
	- docker-compose  
	- git  

#### tailscale setup ####
1. Download and install tailscale on the development server / hypervisor
2. Authenticate into your tailnet
3. Optional: Disable expiry for development server/hypervisor
4. Generate and store a tailscale API token

### Section 2: Dev server setup ###
- generate SSH key for developer user  
1. Create a development user, and add them to the appropriate groups.  
2. Generate a pub/private keypair for the development user.  
- copy SSH key to GitHub as repository secret  
1. 
### Section 3: Python App setup ###
- Required python packages
- virtual environment considerations
- gunicorn server VS uWSGI

### Section 4: Docker Compose setup and configuration ###
- example docker compose file  
- example dockerfile  

### Section 5: Github Action: workflow file setup, and testing ###
- defining a github action workflow.yml file  
- referencing github secrets in workflow file  

### Section 6: Additional Resources and follow up reading ###
- where do we go from here?  
