# SampleCICDProject

![CI/CD](../images/projectdeployment.jpg) 

## Overview

SampleCICDProject demonstrates how to set up Continuous Integration and Continuous Deployment (CI/CD) for containerized projects using Docker. The project includes a GitHub Actions workflow that automates testing and deployment processes.

## Workflow Configuration

The CI/CD workflow is configured in `.github/workflows/main.yml`. The workflow:

- Triggers on pushes to any branch except 'main', and on releases published on the 'main' branch.
- Consists of two jobs: 'test' and 'deploy'.

## Job: test

This job runs when a push event occurs. It performs the following steps:

1. Checks out the code from the repository.
2. Runs unit tests using pytest.
3. Extracts commit data using the `rlespinasse/git-commit-data-action` action.
4. Creates a pull request with the extracted commit data using the GitHub CLI (`gh`).

## Job: deploy

This job runs when a release event occurs on the 'main' branch. It performs the following steps:

1. Checks out the code from the repository.
2. Copies the project source code to the `/app` directory.
3. Runs unit tests using pytest on the copied source code.
4. Builds and deploys the Docker containers using `docker compose up`.

## Getting Started

1. Clone this repository: `git clone https://github.com/karrabi/SampleCICDProject.git`
2. Set up your Docker environment.
3. Configure the GitHub repository secrets:
   - `GH_TOKEN`: Your GitHub personal access token.
4. Modify the workflow configuration as needed.
5. Push to branches or create releases to trigger the workflow.

## Prerequisites

- Docker
- GitHub account
- GitHub CLI (`gh`)


## Preparing the project

### installing Docker on Server
  https://docs.docker.com/engine/install/ubuntu
  ```
  # Docker Installation
  curl -fsSL https://get.docker.com/ -o get-docker.sh
  sh get-docker.sh
  ```

  Install following Packages:

  ```
  sudo apt update && sudo apt install python3 git tmux python-is-python3 python3-pip
  sudo python3 -m pip install pytest==7.4.0
  ```

  Install these packages also

  https://github.com/cli/cli/blob/trunk/docs/install_linux.md

  ```
   type -p curl >/dev/null || (sudo apt update && sudo apt install curl -y)
   curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | sudo dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg \
   && sudo chmod go+r /usr/share/keyrings/githubcli-archive-keyring.gpg \
   && echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list    > /dev/null \
   && sudo apt update \
   && sudo apt install gh -y
  ```
  
  Now configure the github action as follow: 
  
  https://docs.github.com/en/actions/hosting-your-own-runners/managing-self-hosted-runners/adding-self-hosted-runners

  ```
   Repo -> Settings -> Action -> Runners -> New self hosted runner -> Linux x64 -> run all commands on server
  ```
  
  you can use tmux for continuse running 


  If you face this Error:

   Must not run interactively with sudo
   Exiting runner

  Run this Command 
   ‍‍‍```export RUNNER_ALLOW_RUNASROOT="1" ```


## Repository Configuration

your repo -> settings -> Actions -> general

```
Required permissions
Actions permissions -> Allow all actions and reusable workflows
Workflow permissions -> Read and write permissions , Allow GitHub Actions to create and approve pull requests
disable Run workflows from fork pull requests if you see this option.
```


## Step before start:

1- Copy .github folder with its contents to your repository. (README.md file is not necessary)


## How it works on New Release 
1- The Repository will cloned in server
2- All tests will be run on the Project
3- If all tests done successfully then the containers will update and run again


## How it works on New Push
1- The Repository will cloned in server
2- All tests will be run on the Project
3- If all tests done successfully then one pull request will be generated automatically

## Workflow Customization

Feel free to modify the workflow to suit your project's needs. You can add more steps, include additional tests, or customize deployment settings.

## License

This project is licensed under the [MIT License](LICENSE).

---

**Note:** This project is for demonstration purposes and may contain simplified configurations or concepts.
