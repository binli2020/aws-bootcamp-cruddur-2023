# Configure and Use GitHub Codespaces

# [What is GitHub Codespaces](https://docs.github.com/en/codespaces/overview)
> A codespace is a development environment that's hosted in the cloud. You can customize your project for GitHub Codespaces by committing [configuration files](https://docs.github.com/en/codespaces/setting-up-your-project-for-codespaces/adding-a-dev-container-configuration/introduction-to-dev-containers) to your repository (often known as Configuration-as-Code), which creates a repeatable codespace configuration for all users of your project.
> 
> Each codespace you create is hosted by GitHub in a Docker container, running on a virtual machine. You can choose from a selection of virtual machine types, from 2 cores, 8 GB RAM, and 32 GB storage, up to 32 cores, 64 GB RAM, and 128 GB storage.

# Create a Codespaces
From you repo in GitHub, click `< > Code` button, then create the `+` button to create a Codespaces.

![image](https://user-images.githubusercontent.com/71969513/228438846-defd36f2-3332-4e22-bd44-2aeacbd7a561.png)

The codespace is being created ...

![image](https://user-images.githubusercontent.com/71969513/228441067-27d21877-30da-4230-a790-ce2d438601e5.png)

The codespace is created

![image](https://user-images.githubusercontent.com/71969513/228444090-8bc8fda5-4e1f-4fcb-ab45-c45a50e9bef9.png)

# Configure Codespaces

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

## My configuration file

I end up using the following `devcontainer.json`

```json
{
	"name": "Cruddur Configuration",
	"workspaceFolder": "/workspaces/${localWorkspaceFolderBasename}",
	// Features to add to the dev container. More info: https://containers.dev/features.
	"features": {
		"ghcr.io/devcontainers/features/aws-cli:1": {}
	},
	"remoteEnv": {
		"AWS_CLI_AUTO_PROMPT": "on-partial"
	},
	"postCreateCommand": "cd frontend-react-js; npm install",
	"customizations": {
		"vscode": {
			"extensions": [
				"ms-azuretools.vscode-docker",
				"ms-python.python",
				"cweijan.vscode-postgresql-client2",
				"zaaack.markdown-editor"
				// "vscodevim.vim"
			],
			"settings": {
				"terminal.integrated.fontSize": 18,
				"editor.fontSize": 18,
				"workbench.colorTheme": "Default Dark+ Experimental"
			}
		}
	}
	// Use 'forwardPorts' to make a list of ports inside the container available locally.
	// "forwardPorts": [],

	// Configure tool-specific properties.
	// "customizations": {},

	// Uncomment to connect as an existing user other than the container default. More info: https://aka.ms/dev-containers-non-root.
	// "remoteUser": "devcontainer"
}
```

### Some issues with the configuration

* For the VIM VSCode Extension, it has to be installed locally in my browser. So I remove it from `devcontainer.json`

    If it is already installed in a Codespaces container, the way to make vim working is: 

    1. Uninstall it
    2. Reload the codespace
    3. Install the VIM extension (so it is installed in the local browser)

* AWS credentials

    I haven't set AWS credentials so the following command is failed.

    ```sh
    $ aws sts get-caller-identity

    Unable to locate credentials. You can configure credentials by running "aws configure".
    ```

	The following environment variables need be set
	```sh
	export AWS_ACCESS_KEY_ID=...
	export AWS_SECRET_ACCESS_KEY=...
	export AWS_DEFAULT_REGION=ap-southeast-2
	```
	
    * Use `Codespaces Manage User Secrets` to set up the AWS credentials
    
	1. Use the following steps to set up for each of the environment variables.

		![image](https://user-images.githubusercontent.com/71969513/228472721-22c31566-3e5c-4b6e-8b23-13f8d583b038.png)

    	![image](https://user-images.githubusercontent.com/71969513/228473155-94989c82-3314-4704-9446-cd91d0883c2b.png)

		![image](https://user-images.githubusercontent.com/71969513/228475121-5016b146-0602-44a6-9760-7121db20753d.png)

		![image](https://user-images.githubusercontent.com/71969513/228475343-8c203d9b-a802-44cf-b030-f57e2fa84302.png)

	2. Then rebuild the codespace container of the codespace
    
		![image](https://user-images.githubusercontent.com/71969513/228476457-0620ecb7-5f9a-4e9b-b35f-33c592d85bd4.png)

	3. Verify the env vars are set after the codespace rebuild
    
		![image](https://user-images.githubusercontent.com/71969513/228483534-367dad05-fc9a-4fee-b4f3-cafef9bd8de8.png)
	
	4. It can also be seen in the [github codespace settings](https://github.com/settings/codespaces)

		![image](https://user-images.githubusercontent.com/71969513/228484159-4689d739-f7d8-4d9f-95b1-36864ebc005e.png)

* Set other environment variables

	Use `remoteEnv` to set up other environment variables
	```json
		"remoteEnv": {
			"AWS_CLI_AUTO_PROMPT": "on-partial"
		},
	```

* Use `postCreateCommand` to run commands when a codespace container is up, i.e. `npm install`

	```json
	"postCreateCommand": "cd frontend-react-js; npm install",
	```
* Update the environment variables names in URLs in `docker-compose.yml`

	```sh
	FRONTEND_URL: "https://${CODESPACE_NAME}-3000.${GITHUB_CODESPACES_PORT_FORWARDING_DOMAIN}"
	BACKEND_URL: "https://${CODESPACE_NAME}-4567.${GITHUB_CODESPACES_PORT_FORWARDING_DOMAIN}"
	```

	```sh
	$ echo $CODESPACE_NAME
	binli2020-orange-space-palm-tree-9wr6pjrg6vgfx4rq
	$ echo $GITHUB_CODESPACES_PORT_FORWARDING_DOMAIN
	preview.app.github.dev
	```

This `devcontainer.json` was verified to be able to setup the GitHub Codespaces well.
