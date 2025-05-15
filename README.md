# DevOps CI Scripts for Jenkins + C++ Build

This repository contains Jenkins pipeline scripts that automate building and running a C++ project hosted in a separate GitHub repository. The pipeline also integrates with Discord to send real-time build status notifications.

## Overview

The Jenkins pipeline performs the following steps:

1. Sends a Discord notification when the build starts.
2. Checks out the C++ source code from the specified GitHub repository.
3. Compiles all `.cpp` files using `g++`.
4. Runs the compiled executable.
5. Saves the compile and run logs locally with timestamps under `~/Downloads/Jenkins-Builds/`.
6. Sends a Discord notification on success or failure, including links to the log files.

## Repository Structure

- This repo (`devops-ci-scripts`) contains:
  - `Jenkinsfile` – The pipeline definition.

- The C++ source code is maintained in a separate repository:
  - [https://github.com/MayankSaxena03/jenkins-test.git](https://github.com/MayankSaxena03/jenkins-test.git)

## Prerequisites

- Jenkins installed with:
  - Git plugin
  - GitHub integration plugin
- Jenkins credentials configured:
  - GitHub access (if the repo is private)
  - Discord webhook URL stored as credential with ID `discord-webhook`
- The Jenkins job is configured to use this repo's `Jenkinsfile` as the pipeline source.
- A GitHub webhook configured to trigger the Jenkins job on new commits, or SCM polling enabled.

## Log Storage

Build logs are saved locally on the Jenkins agent machine under:
~/Downloads/Jenkins-Builds/

Log files are named with timestamps, for example:

- `compile_log_20250516_112230.txt`
- `run_log_20250516_112235.txt`

## Discord Notifications

Notifications are sent via Discord webhook:

- When the build starts
- On build success with links to logs
- On build failure with links to logs

## How to Use

1. Clone this repository to your Jenkins server or connect your Jenkins pipeline to this repo.
2. Configure Jenkins credentials as described above.
3. Set up the Jenkins job with this pipeline.
4. Configure GitHub webhook or polling to trigger builds.
5. Push code changes to your C++ repo to trigger builds.

---

## Author

Mayank Saxena  
[https://github.com/MayankSaxena03](https://github.com/MayankSaxena03)
