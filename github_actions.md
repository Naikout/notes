# Github actions

## Yaml file for actions

```
name : Hello World
on : [push]
jobs : 
  say-hello :
    runs-on : ubuntu-latest
    steps :
    - name : Checkout
      uses : actions/checkout@v2.0.0
      run : echo Hello, world!
```

### Jobs

- Workflows must have at the least one job
- Each job must have an identifier
- Must start with a letter or an underscore
- Jobs run in parallel by default

### Runs-on

- The type of machine needed to run the job

### Steps

- List of actions or commands
- Access to the file system
- Each step runs in its own process

### Uses

- Identifies an action to use
- Defines the location of that action

### Run

- runs commands in the virtual environments shell

### Name

- An optional identifier for the step

### Workflows

- Workflows define the event that triggers actions
- Workflows define which actions to run
- Repositories can contain multiple workflows
- mkdir -p .github/workflows

Events
|---|
Push
Pull request
Release
https://help.github.com/en/actions/automating-your-workflow-with-github-actions/events-that-trigger-workflows

- vim first.yml

```
name: first
on: push

jobs:
  job1:
    name: First job
    runs-on: windows-latest
  job2:
    name: Second job
    runs-on: ubuntu-latest
```
