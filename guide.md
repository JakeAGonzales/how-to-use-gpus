
# Instructions for using L40 GPUs on Hyak

### 1. Login
Open your terminal (Mac/Linux) or PowerShell (Windows).
```bash
ssh your_netid@klone.hyak.uw.edu
```
* **Password:** Enter your NetID password.
* **2FA:** Type `1` for Duo push.

### 2. Check Available GPUs
Find which **account** and **partition** you have access to.
```bash
hyakalloc
```
* Confirm the **Account** name is **mlopt** and the partition is `gpu-l40s`. 

### 3. Create Job Script (`gpu_job.slurm`)
Create a file named `gpu_job.slurm` and paste the following. 

```bash
#!/bin/bash
#SBATCH --job-name=mlopt_gpu_job
#SBATCH --account=mlopt             # Hardcoded for mlopt
#SBATCH --partition=gpu-l40s        # Hardcoded for L40S partition
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=1
#SBATCH --cpus-per-task=4           # Max available: 128
#SBATCH --mem=32G                   # Max available: 1498G
#SBATCH --gres=gpu:1                # Request 1 GPU (Max: 8)
#SBATCH --time=01:00:00             # Max runtime (HH:MM:SS)

# Load Modules (L40S requires CUDA 11.8+)
module load cuda/12.2

# Run Script
python my_script.py
```

### 4. Submit and Monitor
**Submit the job:**
```bash
sbatch gpu_job.slurm
```

**Check status:**
```bash
squeue -u your_netid
```

**Cancel a job:**
```bash
scancel job_id
```

**Check mlopt jobs:**
```bash
squeue -A mlopt
```
