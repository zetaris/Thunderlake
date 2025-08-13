# Lightning Catalog

The **Lightning Catalog** is an open-source data catalog designed for preparing data at scale for **ad-hoc analytics**, **data warehousing**, **lakehouses**, and **machine learning projects**.  

It enables enterprises to **manage, discover, and unify diverse data assets** without requiring a centralized repository. Built on top of **Apache Spark**, it leverages GPU acceleration via **RAPIDS** to handle high-performance workloads.

---

## Key Capabilities

- **Unified Data Access**  
  Manage endpoints for diverse enterprise data assets with unified access via SQL and Apache Spark API.

- **Federated Query Processing**  
  Run SQL queries across multiple source systems without physically moving data.

- **Data Engineering Simplification**  
  Streamline pipeline lifecycles — build, test, and deploy — using **Data Flow Tables** for incremental loads and transformations.

- **Machine Learning Data Prep**  
  Reduce ML engineering effort by enabling direct access to structured and unstructured data.

- **Unstructured Data Support**  
  Ingest and query metadata and contents of files in parallel using Spark.

- **Unified Semantic Layer**  
  Upload DDL definitions and map them to underlying data sources for consistent enterprise-wide semantics.

- **Data Quality Checks**  
  Apply business rules and constraints (PK, Unique, FK) to non-RDBMS formats like Parquet, Iceberg, and Delta.

---

## Deployment Options

Lightning Catalog can run:
1. **On Google Cloud Platform (GCP)** — inside a GPU-enabled Virtual Machine (VM)
2. **On Local Machine** — with Docker or native setup

This guide covers:
- Provisioning a **GPU-accelerated VM** for Spark + RAPIDS
- Installing prerequisites (Conda, CUDA, NVIDIA Driver)
- Running Lightning Catalog via **Docker Compose**

---

## GCP VM Prerequisites

| Component         | Recommended Configuration |
|-------------------|---------------------------|
| **GPU Type**      | NVIDIA V100 (16GB) — Best RAPIDS compatibility and proven Spark + cuDF support |
| **Machine Type**  | Minimum: `n1-standard-8` (8 vCPUs, 30 GB RAM)<br>Recommended: Higher for larger workloads |
| **OS**            | Ubuntu 22.04 LTS |
| **Disk**          | Boot Disk: 100 GB SSD |

---

## Environment Setup

Once the VM is provisioned, install **Conda**, **RAPIDS**, **CUDA**, and the **NVIDIA Driver**.

### **A. Install Conda, RAPIDS, and CUDA**
#### Download and install Miniconda
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh

#### Add Conda to PATH
echo 'export PATH="$HOME/miniconda3/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc

#### Create RAPIDS environment
conda create -n rapids-24.02 -c rapidsai -c nvidia -c conda-forge     rapids=24.02 python=3.10 cudatoolkit=11.8
conda activate rapids-24.02

#### Verify Conda installation
conda --version

### **B. Install NVIDIA Driver**

sudo apt update
sudo apt install -y nvidia-driver-535
sudo reboot

Verify GPU access:
nvidia-smi

**Validation:**  
- \`conda --version\` → Conda installed and accessible  
- \`nvidia-smi\` → GPU recognized and driver working  

---

## Run Lightning Catalog on GPU VM

Your VM should now have:
- Ubuntu 22.04 LTS
- NVIDIA Driver, CUDA, and RAPIDS installed

We will now:
1. Install **Git** + **Docker**
2. Clone the Lightning Catalog repo
3. Run it via Docker Compose

---

### **1. Install Git, Docker, and Docker Compose**
#### Update system packages
sudo apt update && sudo apt -y upgrade

#### Install Git & dependencies
sudo apt install -y git ca-certificates curl gnupg lsb-release

#### Add Docker’s GPG key & repo
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg |   sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo   "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg]   https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" |   sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

#### Install Docker Engine and Compose plugin
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

#### (Optional) Run Docker without sudo
sudo usermod -aG docker $USER
newgrp docker

#### Verify installation
docker --version
docker compose version

---

### **2. Clone the Repository**
git clone https://github.com/zetaris/Thunderlake.git
cd Thunderlake

---

### **3. Pull the Docker Image**
#### Using Compose v2
docker compose pull

#### Legacy
docker-compose pull

#### Specific image example:
docker pull zetaris/lightning-catalog-nvidia:latest

---

### **4. Start the Application**

#### Using Compose v2
docker compose up -d

#### Legacy
docker-compose up -d

---

### **5. Verify & Access**

#### See running containers
docker ps

#### View logs
docker compose logs -f
docker-compose logs -f (legacy)

**Access the UI:**
- Direct: \`http://<VM_PUBLIC_IP>:8081\`  
- GCP SSH Tunnel:

gcloud compute ssh <INSTANCE_NAME> --zone <ZONE>   -- -L 8081:localhost:8081 -L 8080:localhost:8080

Then browse: [http://localhost:8081](http://localhost:8081)

> Ensure firewall rules/security groups allow inbound traffic on the exposed ports.

---

## Online Documentation
- [GitHub Documentation](https://github.com/zetaris/Thunderlake)

---

## Contributing
We welcome contributions!  
- Report issues via [GitHub Issues](https://github.com/zetaris/Thunderlake/issues)  
- Submit improvements via [Pull Requests](https://github.com/zetaris/Thunderlake/pulls)

---

## License
This project is licensed under the [Apache 2.0 License](LICENSE).
