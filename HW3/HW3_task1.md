# Task 1: Lecture 4 Environment and Superset Startup Review

## Command Explanations
*   **`make init`**: Initializes the local environment. It creates the `.env` file from `.env.example` (if missing), generates a secure `SUPERSET_SECRET_KEY`, sets the `AIRFLOW_UID`, and creates necessary directories. This ensures all configuration variables required by Docker Compose are present.
*   **`make up-superset`**: Starts the Superset stack in the background using Docker Compose. It specifically targets the `superset` profile to spin up only the containers needed for data visualization and the data warehouse, skipping other services like Airflow.
*   **`make devcontainer-join-course-network`**: Connects the currently running VS Code devcontainer to the Docker Compose network (`course_default`). This is necessary so that the devcontainer can resolve and communicate with the other containers.
*   **`make ps`**: Shows the status, health, and port mappings of all running containers in the current Docker Compose project. It's used to verify that the environment started successfully.

**Personally, I like to go to the Makefile help: section and look for commands and explaination from there. Here is the copied versions from Makefile:**
* @echo "  make init           Copy .env.example to .env (if missing), set secret key, set AIRFLOW_UID"
* @echo "  make up-superset    Start Superset stack (profile: superset)"
* @echo "  make devcontainer-join-course-network  Attach devcontainer to compose network"
* @echo "  make ps             Show container status"

## Expected Services for Lecture 4
*   **`postgres`**: Acts as the data warehouse storing the ETL data, as well as the metadata database for Superset.
*   **`superset-redis`**: The caching layer used by Superset to improve performance.
*   **`superset`**: The main web application for data exploration and dashboarding.
*   **`superset-init`**: A short-lived container that runs initialization scripts (setting up the admin user, upgrading the database, and initializing roles) and then exits.

## Why `make devcontainer-join-course-network` is needed
By default, the devcontainer and the Docker Compose services run in separate, isolated virtual networks. Because the ETL scripts are executed from *inside* the devcontainer, the devcontainer must join the Docker Compose network (`course_default`). Without this, the ETL script wouldn't be able to connect to the `postgres` container to load the data.

## How `make` and Docker Compose work together
Docker Compose handles the orchestration of the containers, networks, and volumes based on the `docker-compose.yml` file. `make` acts as a task runner/wrapper (via the `Makefile`) that simplifies long and complex Docker Compose commands into easy-to-remember shortcuts. `make` also orchestrates dependencies (e.g., ensuring `init` runs before `up-superset`) and runs complex shell scripts (like resolving Windows/Linux paths for volume mounts) before passing the final arguments to Docker Compose.

## How to start working again in specific cases

*   **The Superset stack already exists but is stopped:**
    Simply run `make up-superset` to start the existing containers, followed by `make devcontainer-join-course-network` to reattach the devcontainer to the network.

*   **The containers have been removed:**
    Run `make up-superset`. Docker Compose will recreate the containers from the images. Since the data is stored in persistent Docker volumes, your previous work and warehouse data will still be intact. Then, run `make devcontainer-join-course-network`.

*   **You changed `.env` values or reopened the repository from a different host folder:**
    Run `make down` to cleanly remove the old containers and network attachments. Then, run `make up-superset` to recreate the containers so they pick up the new environment variables and new volume bind mounts. Finally, run `make devcontainer-join-course-network`.