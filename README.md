# Flask App Deployment using Helm and ArgoCD

This repository contains the configurations to deploy a Flask application to Kubernetes using Helm and ArgoCD, following GitOps principles.

## Overview

This project uses an ArgoCD `ApplicationSet` to manage the deployment of a Helm-based Flask application to multiple environments (development and production). Git is used as the single source of truth for the desired state of the application.

## Repository Structure

*   `argocd/applicationset.yaml`: Defines the ArgoCD `ApplicationSet` for multi-environment deployment.
*   `helm/flask/`: Contains the Helm chart for the Flask application.
    *   `Chart.yaml`: The chart definition file.
    *   `values.yaml`: The default values for the Helm chart.
    *   `templates/`: Directory for Kubernetes manifest templates.

## How it Works

The `ApplicationSet` controller in ArgoCD will detect the `applicationset.yaml` file and create ArgoCD `Application` resources for each environment defined in the generator. In this case, it will create two applications: `flask-dev` and `flask-prod`.

Each application will be synced to its respective cluster and namespace, using the environment-specific values file (`values-dev.yaml` or `values-prod.yaml`).

## Prerequisites

*   A Kubernetes cluster (or separate clusters for dev and prod).
*   `kubectl` access to the cluster(s).
*   ArgoCD installed on the cluster.
*   The repository URL and paths configured correctly in `argocd/applicationset.yaml`.

## Deployment

1.  **Create environment-specific values files.**
    In the `helm/flask/` directory, create `values-dev.yaml` and `values-prod.yaml` with the appropriate settings for each environment.

    Example `values-dev.yaml`:
    ```yaml
    replicaCount: 1
    service:
      type: ClusterIP
      port: 80
    ```

    Example `values-prod.yaml`:
    ```yaml
    replicaCount: 2
    service:
      type: LoadBalancer
      port: 80
    ```
2.  **Apply the ApplicationSet to your cluster.**
    ```bash
    kubectl apply -f argocd/applicationset.yaml
    ```
3.  **Verify in ArgoCD.**
    Open your ArgoCD UI and you should see `flask-dev` and `flask-prod` applications being created and synced.
