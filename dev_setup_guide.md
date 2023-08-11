# GitHub Action CI/CD workflow Setup Guide #
SubTitle: Continuous deployment to a Docker container, protected by Tailscale, and powered by GitHub

Introduction: The content of this guide is purely for educational purposes and comes with no warranty whatsoever. 

The steps below outline the process of setting up a development workflow that deploys changes to a simple app running on docker.

The docker architecture is a very simple two container setup. 
First, a simple Python Flask app is created as an example - then an NGINX container is used to proxy requests.

This architecture allows the user to quickly integrate HTTPS, load balancing, and other features provided by NGINX.

The hope is to push changes to the app quickly, without having to expose the host (hypervisor) to the public internet.



section 1: Pre-Requisites and assumptions
- Development server running ssh server, and connected to tailscale.
- required packages/services:
	- openssh-server
	- docker-compose
	- git
- tailscale setup
	- development server must have 'no exipiry' option selected in Tailscale

Section 2: Dev server setup
- generate SSH key for developer user
- copy SSH key to GitHub as repo secret

Section 3: Docker Compose setup and configuration
- example docker compose file
- example dockerfile
-

Section 4: Github Action: workflow file setup, and testing
- defining a github action workflow.yml file
- referencing github secrets in workflow file

Section 5: Additional Resources and follow up reading
