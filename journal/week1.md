# Week 1 — App Containerization

### Finished the following tasks (1/Mar)
* Watched the YT video about how to ask a technical question
  * Basic idea: provide as much as info about what you've **_tried and observed, and related codes_**.
  * Include the following information in your question
    * What I'm trying to do. What error I'm facing
    * Screenshot or show error message
    * What you've done to try to fix it? i.e. what's actions, Googled, learned
    * Paste and format code snippet (use \`\`\`) or copy the whole file to gist.github.com and share it.
    * Inspect the network in your browser, take a screenshot covering all info, i.e. URL, the Console, the request Headers/Response etc.
    * Use `print` to find where it breaks
    * Use Debugger (i.e use `pdb` in Python)

* Watched week 1 live YT video
  * `linuxserver.io`
    * A bunch of docker images
    * A reference for learning to write Dockerfiles
  * Layers in Docker images
    * Docker image is created based on other existing images
    * This construct a layers structure.
    * The final image is a merge and union of all these layers, like you view the layers from the top. 
  * Difference between Virtual machine and containers
    * Virtual machines run on a hypervisor layer on a host OS, it has its own guest OS.
    * Container, instead, runs on a container daemon, and share the OS with the host OS.
![image](https://user-images.githubusercontent.com/71969513/222669927-6e4322ff-417b-4d1d-b544-d60174e52e1b.png)
  * Commands to build docker image. Inside the backend-flash directory, run this command
  ```bash
  docker build -t backend-flask .
  ```
  * Run docker container
  ```bash
  docker run --rm -p 4567:4567 -it -e FRONEND_URL='*' -e BACKEND_URL='*' backend-flask
  ```
  * Learn Docker Compose
    > A Docker Compose file is a YAML file that defines how Docker containers should behave in relation to each other. It is used to define a multi-container Docker application, where each container represents a separate component or service of the application.

    > The functionality of a Docker Compose file is to define and configure the various components of a Docker application, including the containers, networks, and volumes. It allows developers to easily spin up and configure multiple containers with a single command, making it much easier to manage and deploy complex applications.

    > The Docker Compose file specifies the configuration of the services that make up an application, such as the **Docker image** to use, **environment variables**, **ports** to expose, and **volumes** to mount. It can also define **dependencies between services**, such as one service depending on another service being started first.

    > Using a Docker Compose file, developers can quickly define and deploy an entire application stack, including all necessary dependencies, in a repeatable and consistent way across different environments. This can greatly simplify the development, testing, and deployment of complex applications.
  * Run docker compose
    * Run `npm install` under `frontend-react-js` folder. This is for installing React.
    * Run `docker compose up`. This is for running the two containers of frontend and backend.
* Watched YT video about Gitpod, Github CodeSpace, Cloud9 and CloudTrial
  * > Cloud9 uses Amazon Elastic Compute Cloud (EC2) instances to provide the computing power needed to run the IDE. If you leave your Cloud9 instance running even when you're not actively using it, this can result in idle instances, which can lead to increased costs on your AWS account.
  * When uses CloudTrial to record API request towards your AWS account and AWS resources, a few options need to be disabled to avoid charge.
* Watched YT video about Docker container security
  * Top 10 security best practices for Docer container
    ![image](https://user-images.githubusercontent.com/71969513/222949211-c6e7c84f-8b85-42ba-abad-34921aabe920.png)
  * Snyk Open Source Github Walkthrough
    * Connected my repo to Snyk, scanned vulnerabilities, found 6 critical and 26 high issues.
    * Fixed the vulnerabilities by a PR from Snyk. Now it is much better. ![image](https://user-images.githubusercontent.com/71969513/223003202-b06caf16-0af2-4d0a-a440-76cdf4295e59.png)
  * AWS **Secrets Manager** walk through
    * > AWS Secrets Manager is a fully managed AWS service that enables you to easily rotate, manage, and retrieve secrets such as database credentials, API keys, OAuth tokens, and other sensitive data. Secrets Manager can help you protect access to applications, services, and IT resources without having to build or maintain custom code or scripts.
    
      > With AWS Secrets Manager, you can securely store and retrieve secrets using a simple API call, without needing to manage your own encryption keys. Secrets Manager provides encryption for secrets at rest and in transit, and can also automatically rotate secrets on a schedule that you define.
     
      > In addition, Secrets Manager integrates with other AWS services such as AWS Lambda, AWS CloudFormation, and AWS Elastic Beanstalk, making it easy to securely manage secrets across your entire AWS environment.
      
      > Overall, AWS Secrets Manager can help you improve security and compliance by enabling you to easily manage secrets and access controls, without the need for custom code or manual processes.
  * AWS Inspector go through
    * Amazon Inspector is an automated vulnerability management service that continually scans workloads for software vulnerabilities and unintended network exposure.

* Learn more about **Docker Architecture**
  * https://docs.docker.com/get-started/overview/#docker-architecture ![image](https://user-images.githubusercontent.com/71969513/222997662-541e6319-e05c-4a6c-89c6-434bb04643ef.png)
  * > The **Docker daemon**
    > The Docker daemon (dockerd) listens for Docker API requests and manages Docker objects such as images, containers, networks, and volumes. A daemon can also communicate with other daemons to manage Docker services.

  * > **The Docker client**
    > The Docker client (docker) is the primary way that many Docker users interact with Docker. When you use commands such as docker run, the client sends these commands to dockerd, which carries them out. The docker command uses the Docker API. The Docker client can communicate with more than one daemon.

  * > **Docker Desktop**
    > Docker Desktop is an easy-to-install application for your Mac, Windows or Linux environment that enables you to build and share containerized applications and microservices. Docker Desktop includes the Docker daemon (dockerd), the Docker client (docker), Docker Compose, Docker Content Trust, Kubernetes, and Credential Helper. For more information, see Docker Desktop.

  * > **Docker registries**
    > A Docker registry stores Docker images. Docker Hub is a public registry that anyone can use, and Docker is configured to look for images on Docker Hub by default. You can even run your own private registry.

    > When you use the docker pull or docker run commands, the required images are pulled from your configured registry. When you use the docker push command, your image is pushed to your configured registry.

  * 
