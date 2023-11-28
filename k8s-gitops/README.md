# Kubernetes GitOps for Edge Platform

This repository hosts the Kubernetes GitOps (FluxCD) configurations and bootstrap files for the Edge platform, inspired by the [LifeChamps Project](https://lifechamps.eu). It serves as the backbone for orchestrating and provisioning resources on Kubernetes for a Raspberry Pi-based platform, employing MicroK8s as the container management system.

## Overview

The Raspberry Pi hosts [MicroK8s](https://microk8s.io/) as a container management system. The setup includes deploying MySphera LOCS gateway and Edge Analytics Engine as separate containers, sourced from LifeChamps' private Docker registry on GitLab. The control plane, implemented via FluxCD, automates the deployment and management of resources on Kubernetes through Git.

### Key Features

- **Configuration Management**: Kubernetes resource configurations are stored in Git, ensuring version control and history tracking.
- **GitOps Workflow**: Modifications to Kubernetes resources are made solely via pull requests to the Git repository.
- **Automatic Synchronization**: Changes in Git trigger immediate and automated updates to the cluster.
- **State Management**: The system corrects or alerts any state drifts from the desired configuration.

### Services

- **Configuration Synchronizer**: Pulls individual Raspberry Pi configurations (YAML files) from the Configuration Repository, storing application-specific configurations like MAC addresses of MySphera motion sensors in Kubernetes secrets.
- **Docker Orchestrator**: Monitors Docker images for updates, replacing old containers with new versions seamlessly.

## Data Handling

Telemetry and log data are transmitted over the data plane to the central LifeChamps data store (ECS), ensuring centralized and consistent data management.

## Repository and FluxCD Configuration
This repository, hosted on [GitHub](https://github.com/LifeChamps/edge), contains Kubernetes GitOps (FluxCD) configurations and bootstrap files for the Edge platform, inspired by the [LifeChamps Project](https://lifechamps.eu). It is designed to orchestrate and provision resources on Kubernetes for Raspberry Pi-based platforms using MicroK8s as the container management system.

## Setup and Usage

Detailed instructions on setting up and using this repository for your Raspberry Pi-based Kubernetes setup can be found in the [Setup Guide](./SETUP.md).

## Contributing

Contributions to improve the configurations or the deployment process are welcome. Please follow the standard Git workflow - fork the repository, make changes, and submit a pull request.

## License

This work is licensed under the Creative Commons Attribution-NonCommercial (CC BY-NC) license. This license allows others to remix, tweak, and build upon this work non-commercially, as long as they credit the original creation and license their new creations under identical terms. For more details, see [CC BY-NC License](https://creativecommons.org/licenses/by-nc/4.0/).

## Acknowledgments

This work is inspired by and dedicated to the advancements made in the [LifeChamps Project](https://lifechamps.eu), aiming to improve healthcare services through technology.
