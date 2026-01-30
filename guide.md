# Hyak Quick Start Guide for mlopt Group

## 1. Login to Hyak

Open your terminal and connect to the cluster:

    ssh your_netid@klone.hyak.uw.edu

- **Password:** Your UW NetID password
- **2FA:** Press `1` for Duo push notification

---

## 2. Understanding the File System

### Storage Locations

**Home Directory** (`/mmfs1/home/your_netid`)
- 10 GB quota (personal storage)
- Backed up daily
- Use for: configuration files, scripts, small datasets

**Group Scratch** (`/gscratch/mlopt`)
- Shared storage for the mlopt group
- TBs of space
- Use for: datasets, model checkpoints, conda environments

### Quick Navigation

    # Go to your home directory
    cd ~

    # Go to mlopt group scratch
    cd /gscratch/mlopt

    # Check disk usage
    du -sh /mmfs1/home/your_netid
    du -sh /gscratch/mlopt/your_netid

---

## 3. Check Available Resources

    hyakalloc

**Expected output for mlopt:**
- **Account:** mlopt
- **Partition:** gpu-l40s
- **CPUs:** 128 per node
- **Memory:** 1498G per node
- **GPUs:** 8 per node (48 GB each)


#### References
https://hyak.uw.edu/docs/compute/start-here 
---


## 4. Setting Up Conda

**Important note: be sure to replace 'your_netid' in the commands below with your actual UW NetID.**

### Install Miniconda

    # Navigate to group scratch
    cd /gscratch/mlopt

    # Create your directory if needed
    mkdir -p your_netid
    cd your_netid

    # Download and install Miniconda
    wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
    bash Miniconda3-latest-Linux-x86_64.sh -b -p /mmfs1/gscratch/mlopt/your_netid/miniconda3

    # Initialize conda
    /mmfs1/gscratch/mlopt/your_netid/miniconda3/bin/conda init bash

    # Reload your shell
    source ~/.bashrc

#### Reference

https://hyak.uw.edu/docs/tools/python/

### Configure Conda (Recommended)

Create `~/.condarc` to store environments and packages in gscratch:

    cat > ~/.condarc << 'EOF'
    envs_dirs:
      - /mmfs1/gscratch/mlopt/your_netid/conda_envs
    pkgs_dirs:
      - /mmfs1/gscratch/mlopt/your_netid/conda_pkgs
    always_copy: true
    EOF

#### Reference

https://hyak.uw.edu/blog/conda-disk-storage/#configuring-your-package-cache-and-envs-directories 

### Create an Environment

    # Create environment with Python and PyTorch
    conda create -n myenv python=3.11

    # Activate environment
    conda activate myenv

    # Install PyTorch for L40S GPUs (CUDA 12.x)
    not sure yet. 

---

## 5. Running GPU Jobs

### Interactive Session (for testing)

    salloc --account=mlopt --partition=gpu-l40s --nodes=1 --ntasks-per-node=1 \
      --cpus-per-task=8 --mem=64G --gres=gpu:1 --time=02:00:00

Once allocated, load CUDA and test:

    module load cuda/12.2
    nvidia-smi
    python your_script.py

Exit when done:

    exit

### Batch Job Script

Create `gpu_job.slurm`:

    #!/bin/bash
    #SBATCH --job-name=mlopt_job
    #SBATCH --account=mlopt
    #SBATCH --partition=gpu-l40s
    #SBATCH --nodes=1
    #SBATCH --ntasks-per-node=1
    #SBATCH --cpus-per-task=16
    #SBATCH --mem=128G
    #SBATCH --gres=gpu:1
    #SBATCH --time=04:00:00
    #SBATCH --output=slurm-%j.out
    #SBATCH --error=slurm-%j.err

    # Load CUDA module
    module load cuda/12.2

    # Activate conda environment
    source /mmfs1/gscratch/mlopt/your_netid/miniconda3/bin/activate myenv

    # Run your script
    python my_script.py

**Submit the job:**

    sbatch gpu_job.slurm


#### References
https://hyak.uw.edu/docs/compute/scheduling-jobs

---

## 6. Monitoring Jobs

    # Check your jobs
    squeue -u your_netid

    # Check all mlopt jobs
    squeue -A mlopt

    # View job details
    scontrol show job job_id

    # Cancel a job
    scancel job_id

    # View completed job info
    sacct -j job_id --format=JobID,JobName,Elapsed,State,MaxRSS

#### References
https://hyak.uw.edu/docs/compute/resource-monitoring

---

## 7. Resource Limits

**Per L40S Node:**
- CPUs: 128 maximum
- Memory: 1498G maximum
- GPUs: 8 maximum (48 GB VRAM each)

**Typical allocations:**
- Small job: 8-16 CPUs, 64-128G RAM, 1 GPU
- Medium job: 32 CPUs, 256G RAM, 2-4 GPUs
- Large job: 64-128 CPUs, 512G-1T RAM, 4-8 GPUs

---

## 8. Best Practices

1. **Store everything in gscratch:** Your 10GB home quota fills quickly
2. **Use descriptive job names:** Helps when monitoring multiple jobs
3. **Test interactively first:** Use `salloc` before submitting batch jobs
4. **Check job output:** Look at `slurm-*.out` and `slurm-*.err` files
5. **Request appropriate resources:** Don't request more than you need

---

## Quick Reference

    # Login
    ssh your_netid@klone.hyak.uw.edu

    # Check resources
    hyakalloc

    # Interactive GPU session
    salloc --account=mlopt --partition=gpu-l40s --gres=gpu:1 --time=02:00:00

    # Submit batch job
    sbatch gpu_job.slurm

    # Monitor jobs
    squeue -u your_netid

    # Cancel job
    scancel job_id

---

## Need Help?

- Documentation: https://hyak.uw.edu/docs
- Email: help@uw.edu (subject: "Hyak")
- Slack: https://uw-hpcc.slack.com
