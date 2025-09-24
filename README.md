# Evidently Monitoring Workspace

This repository packages a ready-to-run Evidently monitoring service with sample projects and dashboards. It is meant as a starting point for experimenting with the Evidently self-hosted service, exploring example workspaces, and extending them with your own reports.

Docker is required to run the included Evidently service; install it before starting the quickstart steps.

## Features
- Preconfigured Evidently workspace with two demo monitoring projects (Iris classification and German credit risk)
- Docker script for spinning up the Evidently service pinned to this workspace
- Python project declaration (`pyproject.toml`) for generating additional reports via notebooks or scripts
- Example Jupyter notebook (`generate-reports.ipynb`) showing how to produce and upload snapshots

## Prerequisites
- Docker (required to run the Evidently service)
- Python 3.12+ with [`uv`](https://docs.astral.sh/uv/) or `pip` (optional, only needed to run notebooks or generate new reports)

## Quickstart
1. Clone the repository and move into the project directory.
2. Start the Evidently service:
   ```bash
   ./run_service.sh
   ```
   The script runs the official `evidently/evidently-service:latest` Docker image and mounts the local `workspace/` directory so your dashboards and snapshots persist.
3. Open http://localhost:8000 in your browser to explore the sample dashboards.
4. Stop the service when you finish:
   ```bash
   docker stop evidently-service && docker rm evidently-service
   ```

To start the container without the helper script, run the command inside `run_service.sh` manually (useful if you need to change ports or add environment variables).

## Working With the Workspace
- All Evidently artifacts (projects, dashboards, snapshots) live inside `workspace/`.
- Each subfolder corresponds to an Evidently project. For example, `workspace/{project.id}/` contains a project and its saved snapshots.
- Any updates you make through the web UI or API are written back to these folders because the container mounts the directory as a volume.
- To reset the workspace, remove or replace the relevant folders directly, or via the evidently dashboard sdk.

## Generating New Reports
1. Install dependencies:
   ```bash
   uv sync
   ```
2. Launch JupyterLab to run `generate-reports.ipynb`:
   ```bash
   uv run jupyter lab
   ```
3. Run `generate_reports.ipynb` to generate Evidently monitoring reports. Any artifacts created from the notebook runs will be saved into `workspace/`.

> Important: When running notebooks, ensure the Docker container is up so that API calls to the Evidently service succeed.

## Repository Structure
- `README.md` – This guide
- `run_service.sh` – Helper script that starts the Evidently Docker service and mounts the local workspace
- `workspace/` – Persistent Evidently workspace with sample projects and snapshots
- `generate-reports.ipynb` – Notebook demonstrating how to create reports programmatically
- `pyproject.toml` / `uv.lock` – Python project configuration and dependency lockfile for reproducible environments

## Troubleshooting
- **Port already in use**: Modify the `-p` flag in `run_service.sh` to expose the service on a different port, e.g. `-p 8080:8000`.
- **Container already running**: Stop or remove any previous `evidently-service` container before launching a new one.
- **Workspace permissions**: Ensure your user can write to the `workspace/` directory so that the container can persist dashboards and snapshots.

## Next Steps
- Contextualize the Evidently workspace to your own data systems and monitoring needs.
- Schedule batch jobs that transform new data segments into Evidently reports and upload the artifacts to S3 (or another object store).
- Deploy the Evidently OSS service against that object store so dashboards stay updated with fresh reports.
