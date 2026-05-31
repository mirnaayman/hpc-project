# Hybrid HPC and Big Data Cluster for Bioinformatics Disease Classification

## Video Drive Link

https://drive.google.com/drive/folders/1dfZl9y4T8_89ctfxxlE18_TXr_CrEi37

## Project Overview

This project implements a hybrid computing infrastructure combining traditional High-Performance Computing (HPC) using MPI and a Big Data framework using Dockerized Apache Spark. It is designed to analyze bioinformatics datasets containing gene and protein expression levels for disease classification. The goal is to compare the effectiveness of MPI-based parallel processing and PySpark-based distributed machine learning workflows in a virtualized cluster environment.

## Project Structure

```text
project_hpc_hybrid_cluster/
├── bio_classifier.py # PySpark ML pipeline script for disease classification
├── cleaned_bio_dataset.csv # Bioinformatics dataset
├── process_dataset_mpi.py # Python MPI script for analyzing gene expression data
├── hostfile.unknown # Hostfile with node IPs and slots for MPI nodes and slot configuration
├── spark-swarm.yml # Docker stack config for Spark cluster
├── HPC_Report_Mirna.pdf # Full documentation of the project
├── HPC_Script_Mirna.txt # Full setup commands and documentation
├── HPC_PPT_Mirna.pdf # Final presentation
├── screenshots/ # Images for results and verification
│ ├── ssh_success.png # Successful SSH setup
│ ├── mpi_output.png # MPI execution output
│ └── spark_ui.png # Spark UI screenshot
├── expression_comparison_rank0.csv # MPI output: gene expression stats (rank 0)
├── expression_comparison_rank1.csv # MPI output: gene expression stats (rank 1)
├── ml_results_rank0.txt # MPI output: ML results (rank 0) from Task 1
├── ml_results_rank1.txt # MPI output: ML results (rank 1) from Task 1
├── result.txt # PySpark output: final accuracy from Task 2
├── spark-swarm.yml # Docker stack file to deploy Spark master and workers
└── README.md # This file
```

## Technologies Used

* **VirtualBox + Ubuntu** (for creating VMs)
* **MPI + mpi4py** (Task 1 – Mini-HPC Cluster)
* **Docker + Docker Swarm**
* **Apache Spark + PySpark** (Task 2 – Big Data Cluster)
* **Python libraries**: `pandas`, `scikit-learn`, `mpi4py`

## How to Run

### Task 1 – MPI Cluster (Mini-HPC)

1. **Set up VMs**:

   * Create 3 Ubuntu Server 20.04 VMs using VirtualBox (1 master, 2 workers).
   * Configure NAT or Bridged networking and assign static IPs.

2. **Install Dependencies**:

   ```bash
   sudo apt update
   sudo apt install -y python3 python3-pip openmpi-bin libopenmpi-dev
   pip3 install mpi4py scikit-learn pandas
   ```

3. **Enable Passwordless SSH**:

   * Generate SSH keys with `ssh-keygen`
   * Share keys using `ssh-copy-id` to all nodes

4. **Transfer Files**:

   * Copy `process_dataset_mpi.py` and `cleaned_bio_dataset.csv` to all nodes
   * Prepare a `hostfile` with all node IPs and slots

5. **Run MPI Script**:

   ```bash
   mpirun -np 6 --hostfile hostfile python3 process_dataset_mpi.py
   ```

### Task 2 – Spark Cluster (Hybrid Big Data)

1. **Install Docker** on all nodes:

   ```bash
   sudo apt install -y docker.io
   sudo systemctl start docker && sudo systemctl enable docker
   sudo usermod -aG docker $USER
   ```

2. **Initialize Docker Swarm** (on master):

   ```bash
   docker swarm init --advertise-addr <master_ip>
   ```

   * Join workers with `docker swarm join --token ...`

3. **Deploy Spark**:

   ```bash
   docker stack deploy -c spark-swarm.yml sparkcluster
   docker service ls
   ```

4. **Submit Spark Job**:

   ```bash
   docker cp bio_classifier.py <container_id>:/opt/bitnami/spark/
   docker cp cleaned_bio_dataset.csv <container_id>:/opt/bitnami/spark/
   docker exec -it <container_id> bash
   spark-submit --master spark://spark-master:7077 bio_classifier.py
   ```

## Results

* **MPI Task**: Parallel logistic regression achieved consistent results across all nodes; expression comparison saved as CSV.
* **Spark Task**: Logistic regression in PySpark returned accuracy metrics; evaluated on the same dataset in a scalable manner.
* Output files: `expression_comparison_rankX.csv`, `ml_results_rankX.txt`, `result.txt`

## Screenshots

### SSH Setup

![SSH1](https://github.com/user-attachments/assets/13408a81-6b97-4a3d-b5d7-d9f5d0806a33)

![SSH2](https://github.com/user-attachments/assets/95348edb-87db-472e-8730-35f6ad70fc96)

![SSH3](https://github.com/user-attachments/assets/b820bb43-583a-4a90-8d94-249566a6cd27)

### MPI Output

![MPI1](https://github.com/user-attachments/assets/c634e8a9-d56c-4fed-b10b-412ae508e131)

![MPI2](https://github.com/user-attachments/assets/9eee7894-ee47-49c5-92e0-5fb4312d5f8d)

### Spark UI

![SparkUI](https://github.com/user-attachments/assets/e1a97632-5cb9-4bce-8a0d-b881ee9dc086)

## Author

* **Mirna Ayman 221001980**
* **Malak Reda 231001933**
* **Abdallah Mohamed 231002006**
* **Sama saeed 221002117**
* **Haneen Mohamed 221000582**
## License

This project is developed for educational purposes as part of the CBIO312: High Performance Computing course under Dr. Mohamed El-Sayeh, Spring 2025.
