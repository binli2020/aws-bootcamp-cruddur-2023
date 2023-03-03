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
