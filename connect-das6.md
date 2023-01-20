## Remotely access Jupyter Notebook on a DAS-6 cluster node using SLURM
The local machine used for the SSH connection runs <i>Ubuntu 20.04</i>.

The connection was made to the TU Delft DAS-6 cluster ```fs3.das6.tudelft.nl``` (https://www.cs.vu.nl/das6/).
<hr>

Firstly, if the local machine is not on the TU Delft campus, connect with EduVPN using the netid.
Go to https://tudelft.eduvpn.nl/ for more details.
## <b>Terminal 1</b>

```ssh <username>@fs3.das6.tudeflt.nl``` to login to the DAS-6 TUDelft cluster.

<b>Initial Setup</b>
- Install miniconda3 in the ```/home/<username> ``` directory
- Create conda environment: ```$ conda create <env-name> ```
- Install jupyter notebook: ```$ conda install jupyter ```
- Create password for jupyter: ```$ jupyter notebook password ```

<b>Each Session</b>
- ```conda activate <env-name>```
- Run script to request SLURM job and open the jupyter notebook:  ```$ sbatch drifts.sbatch``` which is


```bash
#!/bin/bash
#SBATCH --time=00:15:00
#SBATCH -N 2
#SBATCH --job-name=drift-detection
#SBATCH --output=drift-detection-%j.log

date;hostname;pwd

## Get Tunneling Info ##
port=8889
node=$(hostname -s)
user=$(whoami)
cluster=$(hostname -f | awk -F"." '{print $2}')
echo -e "
#MacOS or linux terminal command to create your ssh tunnel
ssh -N -f -R ${port}:localhost:${port} ${user}@fs3.das6.tudelft.nl
Remote server: ${node}
Remote port: ${port}
SSH cluster: ${cluster}
SSH login: $user
SSH port: 22
Use a browser on your local machine to access:
localhost:${port}  (prefix with https:// if using password)
"

ssh -N -f -R $port:localhost:$port fs3
jupyter notebook --no-browser --port=${port}
```

- Check whether the job was successfully started with ```squeue```. To cancel a job: ```scancel <jobId>```.
-  Frequently run ```cat 'slurm-output-<jobId>.log``` to view console updates and check whether jupyter runs correctly

## <b> Terminal 2</b>

 Run ```ssh -L 8889:localhost:8889 <username>@fs3.das6.tudelft.nl``` for the remote SSH port forwarding.
 <hr>
 
 Now you should be able to locally connect to the jupyter notebook that is ran on the node by typing ```https://localhost:8889``` in a browser.
