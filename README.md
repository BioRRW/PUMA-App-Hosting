# PUMA Services Modernization & Migration Project

## Project Overview

PUMA (Process Automation, Uncertainty Quantification, Modeling, and Analytics) Services is a critical platform providing quantitative analysts at NWRC (National Wildlife Research Center) with tools for code sharing, workflow automation, and hosting R Shiny applications and PostgreSQL databases.

This project involved a significant modernization and migration effort for an existing, inherited PUMA services deployment.

## Credit where credit is due

Josh Hewitt was the original developer of the PUMA service and deserves all credit for its inital development.

## System Description

The PUMA services platform leverages Docker Compose to orchestrate a suite of integrated services designed to support quantitative analysts at the NWRC. This system facilitates enhanced collaboration, streamlined workflows, and centralized data management.

Key capabilities provided by the platform include:

* **Code Sharing & Version Control:** Powered by a dockerized **GitLab** instance, enabling secure and centralized code collaboration, versioning, and project management for analytical scripts and small datasets. Access is facilitated via HTTP for work computers.
* **Workflow Automation:** Supported through GitLab's CI/CD pipelines with dockerized GitLab Runners, capable of watching pipelines and processing data. Additionally, traditional shell tools accessed via SSH allow users to manage locally shared files and automate tasks like data downloads, pre-processing, and automated reporting.
* **Shiny Application Hosting:** Dockerized **Shiny Server** instances host interactive R Shiny applications, making analytical tools accessible via web URLs. Deployments are managed using GitLab's CI/CD tools, with each project potentially having its own Shiny Server instance and read access to dedicated public project files.
* **Database Hosting:** A dockerized **PostgreSQL** server provides robust database hosting for various projects. It includes administrative interfaces (like pgAdmin) and is backed up via a scheduled GitLab CI/CD pipeline configured to access network storage for secure data retention.

Many settings for the repository and services are historically tailored to the NWRC environment, with services accessed via internal DNS mappings (e.g., `http://<server>.usda.net/gitlab`).

## My Role & Contributions

As a [Your Role Here - e.g., DevOps Engineer, System Administrator, Infrastructure Lead], I was responsible for the end-to-end management, enhancement, and migration of the PUMA services.

### Phase 1: Pre-Migration Enhancements & Troubleshooting (Inherited System)

* **System Stabilization:** Inherited a local workstation deployment experiencing intermittent performance and operational issues. Actively managed, updated, and troubleshooted existing Dockerized services to ensure stability.
* **Dependency Resolution:** Investigated and resolved complex Docker installation and dependency conflicts, specifically:
    * Diagnosed and remediated `snapd` sandboxing conflicts that hindered Docker Compose operations and file system access.
    * Rectified intricate Docker permission and dependency conflicts, addressing `docker.sock` access issues.
    * Resolved `shinyWidgets` R package installation failures due to R version incompatibilities and library path misconfigurations within containers.
* **Data Persistence & Logging:** Implemented consistent logging and application cache persistence for R Shiny applications by meticulously configuring Docker bind mounts and host file system permissions, and by debugging `shiny` user (UID 999) write access across container/host boundaries.
* **Configuration Standardization:** Updated and refined `docker-compose.yml`, `Dockerfile.shiny`, and `shiny-startup.sh` for improved maintainability and reliability.
* Performed initial updates and maintenance on inherited GitLab and Shiny Server instances.

### Phase 2: High-Resource Server Migration & Scaling

* **New Server Foundation Setup:**
    * Provisioned and configured a new, high-resource Linux server (Ubuntu) with **700GB RAM and 196 CPU cores**.
    * Established foundational system settings, including networking, firewall rules, and DNS resolution for the new environment.
    * **Created dedicated system users and groups:** Set up the `docker_runner` user and added it to the `docker` group. Established project-specific Linux groups (`nwrc-mudd`, `nwrc-rabies`, `nwrc-cwd`) for data access control.
    * **Installed core Docker components:** Successfully installed the official Docker Engine, Docker Buildx, and Docker Compose plugins via `apt` repositories, ensuring robust container orchestration.
    * Installed critical host-level system libraries required by containerized services (e.g., Oracle Instant Client dependencies like `alien`, `libaio1`, `libgssapi-krb5-2`).
* **Comprehensive Data Migration:**
    * Executed full backup and restore of **GitLab repositories and configurations**.
    * Performed comprehensive **PostgreSQL database dumps**, migrated data, and **renamed key databases** (`cwd-dev` to `cwd2_dev`, `puma_authorization` to `puma2_authorization`) on the new instance, ensuring data integrity.
    * Transferred and verified permissions for all `/web`, `/srv/shiny-server`, and `/work` directories containing application code, configurations, and user data.
* **Service Deployment & Configuration:**
    * Deployed all Dockerized services (GitLab, NGINX, multiple R Shiny Server instances, PostgreSQL, pgAdmin, and GitLab Runners) on the new server.
    * Configured service-specific files (`gitlab.rb`, `nginx.conf`, `.env`) to reflect the new server's hostnames and environment.
* **CI/CD Reconfiguration:** Re-registered and reconfigured all GitLab CI/CD runners (including analytic workflow runners) on the new GitLab instance, updating their host volume mounts and environment variables to ensure uninterrupted automation.
* **Performance Scaling:** Configured Docker Compose scaling parameters for R Shiny Server instances to fully leverage the new server's extensive resources, improving application responsiveness and concurrency.
* **Post-Migration Validation:** Conducted comprehensive testing to verify service functionality, data integrity, and network accessibility across all PUMA components in the new environment.

## Key Technologies Demonstrated

* **Containerization:** Docker, Docker Compose
* **Version Control & CI/CD:** GitLab (Community Edition / Enterprise Edition), GitLab Runners
* **Web Servers/Proxies:** NGINX
* **Application Hosting:** R Shiny Server, R Shiny Applications
* **Database Management:** PostgreSQL, pgAdmin
* **Operating System:** Linux (Ubuntu Server)
* **Scripting:** Bash, R
* **Security Tools:** `sudo`, `chown`, `chmod`, `systemctl` for host-level permissions and service management.

## Impact & Achievements

* **Massive Scalability Boost:** Transformed a resource-constrained local workstation into a robust, high-performance platform capable of handling significantly increased computational demands from quantitative analysts.
* **Enhanced Reliability:** Standardized and resilient Docker deployments improved overall service uptime and reduced manual intervention.
* **Improved Maintainability:** Streamlined configuration, dependency management, and troubleshooting processes for future updates.
* **Critical Service Continuity:** Ensured a seamless transition and uninterrupted access to essential tools and data for NWRC analysts during the migration.
* **Complex Problem Solving:** Demonstrated deep technical expertise in diagnosing and resolving intricate Docker-specific challenges related to permissions, volume mounts, Snap confinement, and inter-container communication.

## Getting Started (High-Level Overview)

This repository contains the necessary Docker Compose configurations, Dockerfiles, and scripts to deploy the PUMA services.

* **Prerequisites:** A Linux server with Docker Engine and Docker Compose installed, along with appropriate host directory structures and permissions configured.
* **Deployment:** Services are launched via a `startserver.sh` bash script utilizing `docker compose`.
* **Access:** Services are accessible via NGINX reverse proxy through configured domain names (e.g., `http://<server2>.usda.net/gitlab`, `http://<server2>.usda.net/[project]/[app]`).

*(Note: For security and proprietary reasons, detailed internal deployment guides, specific application code, and sensitive configuration values are omitted from this public repository.)*
