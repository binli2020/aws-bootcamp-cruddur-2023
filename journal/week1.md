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

* Watching week 1 live YT video
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

    > The Docker Compose file specifies the configuration of the services that make up an application, such as the Docker image to use, environment variables, ports to expose, and volumes to mount. It can also define dependencies between services, such as one service depending on another service being started first.

    > Using a Docker Compose file, developers can quickly define and deploy an entire application stack, including all necessary dependencies, in a repeatable and consistent way across different environments. This can greatly simplify the development, testing, and deployment of complex applications.
