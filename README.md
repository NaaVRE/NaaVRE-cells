# NaaVRE-cells

Template for repositories that build notebook cells into workflow components. These repositories are intended to be used by the [NaaVRE-containerizer-service](https://github.com/NaaVRE/NaaVRE-containerizer-service/).

## Setting up a new repository

### Step 1: create the repository

Create a new repository using this template.

- For testing or development, a single public repository can be used for all virtual labs. 
- For deployments, each virtual lab should have its own private repository. The naming convention is `NaaVRE/cells-vl-{virtual lab slug}` or `NaaVRE-cells-vl-{virtual lab slug}`.

### Step 2: configure access to the repository

- Create a fine grained personal access token granting the following permission to the repository:
  - **Read** access to metadata
  - **Read** and **Write** access to actions and code
- If the repo is owned by an organization, make sure that the user who owns the token has `write` permissions to the repo (repo settings > “Collaborators and teams”).
- Enable workflow write permissions (repo settings > “Actions” > “General” > “Workflow permissions” > check “Read and write permissions” and save).

### Step 3: configure the NaaVRE to use the repository

When deploying with [NaaVRE-helm](https://github.com/NaaVRE/NaaVRE-helm), set the following values for the virtual lab:

```yaml
jupyterhub:
  vlabs:
    {virtual lab slug}:  # e.g. `openlab`
      configuration:
        cell_github_url: https://github.com/{owner}/{repo}
        cell_github_token: github_pat_...  # use the token created at step 2
        registry_url: ghcr.io/{owner}/{repo}
```

When running [NaaVRE-containerizer-service](https://github.com/NaaVRE/NaaVRE-containerizer-service/), set the following values in your `configuration.json` for the virtual lab (see [Test on GitHub](https://github.com/NaaVRE/NaaVRE-containerizer-service/#test-on-github) section of the README):

```jsonl
{
  "vl_configurations": [
    {
      "name": "{virtual lab slug}",  # e.g. `openlab`
      "cell_github_url": "https://github.com/{owner}/{repo}",
      "cell_github_token": "github_pat_...",  # use the token created at step 2
      "registry_url": "ghcr.io/{owner}/{repo}",
      ...
      }
    }
  ]
}
```

### Optional: customize the docker registry

Repositories created from this template use the Github image registry (ghcr.io) by default, with built-in authentication. This works without additional configuration.

To use another registry, set the following actions secrets and variables in the repository settings:

- variable `REGISTRY_NAME` (eg. `https://index.docker.io/v1/` for Docker Hub)
- secret `REGISTRY_PASSWORD`
- secret `REGISTRY_USERNAME`

and make sure to update the registry URL is set on the NaaVRE deployment:

```yaml
jupyterhub:
  vlabs:
    {virtual lab slug}:
      configuration:
        ...
        registry_url: ghcr.io/{owner}/{repo}
```
