
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
* Look for partitions with `gpu` in the name (e.g., `gpu-l40`).
* Note the **Account** name associated with that partition should be **mlopt**.

### 3. Create Job Script (`gpu_job.slurm`)
Create a file named `gpu_job.slurm` and paste the following. **Update the account and partition fields.**

```bash
#!/bin/bash
#SBATCH --job-name=my_gpu_job
#SBATCH --account=your_account_name   # <--- UPDATE THIS (from hyakalloc)
#SBATCH --partition=gpu-a40           # <--- UPDATE THIS (from hyakalloc)
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=1
#SBATCH --cpus-per-task=4             # CPU cores per GPU
#SBATCH --mem=20G                     # Memory limit
#SBATCH --gres=gpu:1                  # Request 1 GPU
#SBATCH --time=01:00:00               # Max runtime (HH:MM:SS)

# Load Modules (example)
module load cuda/11.8

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