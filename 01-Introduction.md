## Jupyter environment
The Jupyter is an open-source web application that allows you to create and share documents that contain live code, equations, visualizations and explanatory text. Uses include: data cleaning and transformation, numerical simulation, statistical modeling, machine learning and much more.

We will be using a Jupyter server as the primary web interface for this workshop.

The Agave image has several customizations to facilitate use of the platform and ease much of the heavy lifting done behind the scenes in this tutorial.

## Custom Kernels
Your Jupyter server has multiple kernels available for use right away. We have preconfigured them with several useful libraries and tools to help users get up and running with common tasks easier. Additionally, we have bundled in Agave CLI and Python SDK into the Bash, Python 2, and Python 3 kernels respectively. Both kernels are pre-authenticated with valid Agave auth tokens that you can use to begin interacting with the Agave Platform right away.

## Web console
Jupyter contains a web terminal that can be used to access your sandbox environment or interact with the Jupyter container itself. To login to your sandbox from the Jupyter web terminal, simply run the following command:

ssh  workshop@VM_IPADDRESS

## Sandbox environment
The tutorial sandbox is a full Ubuntu 16.04 server running on the NSF Jetstream cloud. It includes Docker and Singularity already installed for your use in this tutorial.

## VM runtimes
The images used in this tutorial are available from the public Jetstream image repository.

## Accessibility
To login to the sandbox from outside the Jupyter server, use the host IP address. You have been given the public IP address of your sandbox and the password for the workshop user.
## Persistence
Your VM will remain available during the workshop. During that time, your data will remain available. After that, the VM and any data saved with it will be destroyed. If you need to persist your data, it is recommended that you move it to another host, or create your own account in the Agave public tenant and save your data in the free cloud storage provided to you by default there.

## Logging In
We have already configured resources for you to use in this tutorial.

## Training Account
A training account on the University of Hawaii Agave Platform's public tenant has also been allocated to you.

## Login
The Jupyter HUB server is available at <a href="https://uh-jupyter.its.hawaii.edu">https://uh-jupyter.its.hawaii.edu</a>

Usernames will be your UH credentials - this uses the UH authentication system and is secure. You will need to "Approve" Agave OAuth access to your profile to complete the login process.

When you first login, you will need to start your server. You will find your space empty. You can start a new notebook via the "new" button and selecting the notebook type you desire.
