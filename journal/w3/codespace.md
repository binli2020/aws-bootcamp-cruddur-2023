Configure and Use GitHub Codespace

# [What is GitHub Codespace](https://docs.github.com/en/codespaces/overview)
> A codespace is a development environment that's hosted in the cloud. You can customize your project for GitHub Codespaces by committing [configuration files](https://docs.github.com/en/codespaces/setting-up-your-project-for-codespaces/adding-a-dev-container-configuration/introduction-to-dev-containers) to your repository (often known as Configuration-as-Code), which creates a repeatable codespace configuration for all users of your project.
> 
> Each codespace you create is hosted by GitHub in a Docker container, running on a virtual machine. You can choose from a selection of virtual machine types, from 2 cores, 8 GB RAM, and 32 GB storage, up to 32 cores, 64 GB RAM, and 128 GB storage.

# Create a Codespace
From you repo in GitHub, click `< > Code` button, then create the `+` button to create a Codespace.

![image](https://user-images.githubusercontent.com/71969513/228438846-defd36f2-3332-4e22-bd44-2aeacbd7a561.png)

The codespace is being created ...

![image](https://user-images.githubusercontent.com/71969513/228441067-27d21877-30da-4230-a790-ce2d438601e5.png)

The codespace is created

![image](https://user-images.githubusercontent.com/71969513/228444090-8bc8fda5-4e1f-4fcb-ab45-c45a50e9bef9.png)

# Configure Codespace

> When you work in a codespace, the environment you are working in is created using a development container, or dev container, hosted on a virtual machine.
> 
> Who can use this feature: People with write permissions to a repository can create or edit the codespace configuration.
> 
> About dev containers
> 
> Development containers, or dev containers, are Docker containers that are specifically configured to provide a fully featured development environment. Whenever you work in a codespace, you are using a dev container on a virtual machine.

> You can configure the dev container for a repository so that codespaces created for that repository give you a tailored development environment, complete with all the tools and runtimes you need to work on a specific project. If you don't define a configuration in the repository then GitHub Codespaces uses a default configuration, which contains many of the common tools that your team might need for development with your project. For more information, see ["Using the default dev container configuration."](https://docs.github.com/en/codespaces/setting-up-your-project-for-codespaces/adding-a-dev-container-configuration/introduction-to-dev-containers#using-the-default-dev-container-configuration)

> The configuration files for a dev container are contained in a .devcontainer directory in your repository. You can use Visual Studio Code to add configuration files for you. You can choose from a selection of predefined configurations for various project types. You can use these without further configuration, or you can edit the configurations to refine the development environment they produce. For more information, see ["Using a predefined dev container configuration."](https://docs.github.com/en/codespaces/setting-up-your-project-for-codespaces/adding-a-dev-container-configuration/introduction-to-dev-containers#using-a-predefined-dev-container-configuration)
> 
> Alternatively, you can add your own custom configuration files. For more information, see ["Creating a custom dev container configuration."](https://docs.github.com/en/codespaces/setting-up-your-project-for-codespaces/adding-a-dev-container-configuration/introduction-to-dev-containers#creating-a-custom-dev-container-configuration)
