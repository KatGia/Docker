# 🐳 Quick Guide to Docker Installation and Basic Usage

This guide covers the essential steps to install Docker and begin running your first containerized bioinformatics tool.

## 1\. Installing Docker

Docker is available as **Docker Desktop** for desktop operating systems (Windows, macOS) and as a CLI (Command Line Interface) engine for Linux.

### For Windows and macOS

1.  **Download Docker Desktop:** Navigate to the official [Docker Desktop website](https://www.docker.com/products/docker-desktop).

2.  **Install:** Follow the standard installation process. On Windows, ensure that the **WSL 2** (Windows Subsystem for Linux) feature is enabled, as Docker Desktop relies on it.

3.  **Start and Verify:** Launch Docker Desktop. Once running, open your terminal (or Command Prompt) and verify the installation:

    ```bash
    docker --version
    ```

### For Linux (Ubuntu/Debian Example)

On Linux, the Docker engine is installed directly. This method is common for use on HPC or cloud servers.

1.  **Update Package Indexes:**
    ```bash
    sudo apt update
    ```
2.  **Install Prerequisites:**
    ```bash
    sudo apt install apt-transport-https ca-certificates curl gnupg lsb-release
    ```
3.  **Install Docker Engine:**
    ```bash
    sudo apt install docker-ce docker-ce-cli containerd.io
    ```
4.  **Configure User (Optional but Recommended):** Add your user to the `docker` group to run commands without `sudo`:
    ```bash
    sudo usermod -aG docker $USER
    # Log out and log back in for the changes to take effect.
    ```

-----

## 2\. Basic Commands and Key Concepts

Once installed, you can start working with images (the templates) and containers (the running instances).

### 2.1. Running a Bioinformatics Tool Example (FastQC)

We will use a common image from BioContainers for **FastQC** (a quality control tool for sequencing data). This is the key example for demonstrating reproducibility and data mapping.

#### Step 1: Prepare Your Data

  * Create a directory on your host machine to hold your raw data (e.g., `~/bio_data`).
  * Place your input file (e.g., `sample.fastq.gz`) inside this directory.

#### Step 2: Pull the Image

Download the specific version of the FastQC image from Docker Hub (or BioContainers registry):

```bash
docker pull biocontainers/fastqc:v0.11.9_cv1
```

#### Step 3: Run the Container and Map Data (The Crucial Step\!)

To analyze data, you must link your local data folder to a folder *inside* the container using the **Volume** option (`-v`).

```bash
# Syntax: docker run -v /path/on/host:/path/in/container image_name command
docker run \
    -v ~/bio_data:/data \
    biocontainers/fastqc:v0.11.9_cv1 \
    fastqc /data/sample.fastq.gz -o /data
```

| Component | Description |
| :--- | :--- |
| `docker run` | Creates and starts a new container from the specified image. |
| `-v ~/bio_data:/data` | **Volume Mapping:** Maps your local folder `~/bio_data` to the folder `/data` **inside** the container. This grants the container temporary access to your data. |
| `biocontainers/fastqc...` | The name and tag of the Docker image to use. |
| `fastqc /data/sample.fastq.gz -o /data` | The actual command executed *inside* the isolated container. It reads the input from `/data/sample.fastq.gz` and writes the output report back to `/data`. |

**Result:** Once the command finishes, you will find the FastQC HTML report in your local `~/bio_data` directory.

### 2.2. Image and Container Management

| Command | Description |
| :--- | :--- |
| `docker images` | Lists all locally downloaded images. |
| `docker ps` | Lists currently **running** containers. |
| `docker ps -a` | Lists **all** containers (running and terminated). |
| `docker stop [ID]` | Stops a running container using its ID. |
| `docker rm [ID]` | Removes a stopped container (frees up space). |
| `docker rmi [IMAGE_NAME]` | Removes a downloaded image. |


-----

### 💡 Beyond FastQC: Containerizing the Bioinformatics Landscape
It is important to understand that FastQC is merely an introductory example.

The true power of containerization lies in its ability to manage the vast ecosystem of bioinformatics tools, which often have complex and conflicting dependencies. Docker allows you to:

Standardize complex software: Run tools like BWA (alignment), Samtools (BAM manipulation), GATK (variant calling), or entire R/Bioconductor environments without worrying about installing prerequisites on your host system.

Integrate multi-step pipelines: Use different tools, each in its own containerized environment, connected via workflow managers (like Snakemake or Nextflow), ensuring consistency from raw data to final results.

This approach transforms the management of your analytical environment from a dependency nightmare into a robust, repeatable process.

### 🍀🍀🍀 Good luck! 🍀🍀🍀
