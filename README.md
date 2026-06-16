# Assignment 3 Robustness.
The folder contains the code and the steps to achieve the best score in assignment 3 - Robustness.

## Files
- README.md - explains how to recreate the best result
- train_apex.py - contains the training script for ResNet-50
- submission.py - submission script
- run_apex.sh and run_apex.sub - shell and HTCondor files used to run the trainig job on cluster GPU



How to Setup and Run :

We ran the code in a python virtual environment with PyTorch and torchvision installed. The training was done on university cluster environment.

1. Create working directory using `mkdir -p ~/tml26_task3_robustness` in cluster, also create checkpoint directory inside it using `mkdir -p checkpoints`.
2. Then place the assignment files into this repository,
    Dataset file - train.npz
    Training script - train_apex.py
    Submission script - submission.py
3. Create and activate python virtual environment, using
   `python3 -m venv .venv`
   `source .venv/bin/activate`
4. Install dependencies,
   `pip install --upgrade pip`
   `pip install torch torchvision numpy tqdm requests`
5. Create the cluster run script using,
      ```     
      cat > run_apex.sh <<'EOF'
      #!/bin/bash
      set -eux
      cd ~/tml26_task3_robustness
      source .venv/bin/activate
      echo "The APEX Model"
      python train_apex.py
      EOF
      chmod +x run_apex.sh```
7. Create HTcondor file using,
      cat > run_apex.sub <<'EOF'
      universe = vanilla
      container_image = /scratch/common/images/code-server-default-env.sif
      executable = run_apex.sh
      output = logs/run_apex.$(Cluster).out
      error  = logs/run_apex.$(Cluster).err
      log    = logs/run_apex.$(Cluster).log
      request_cpus = 7
      request_gpus = 1
      request_memory = 32 GB
      queue
      EOF
8. Submit the training job to cluster using, `condor_submit run_apex.sub` and monitor the job using `condor_q`
9. The training script creates a checkpoint inside `checkpoints` directory as `resnet50_apex.pt`.
10. For clarity, we have preserved the final submission checkpoint along with their local score as `BEST_APEX_R50_0544.pt`using `cp checkpoints/resnet50_apex.pt checkpoints/BEST_APEX_R50_0544.pt`.
11. To submit the model, edit the `submission.py` file with `MODEL_PATH = "checkpoints/BEST_APEX_R50_0544.pt" and MODEL_NAME = "resnet50"` (also include the API key).
12. Run the submission script, `python submission.py`
13. Check the leaderboard after successfull submission for score.


