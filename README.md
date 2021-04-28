# Brainlife & Compute Canada

These are instructions to supplement the Brainlife.io instructions on setting up a new resource. 

## Instructions for getting set-up on the compute canada login node

1. Install abcd-spec as per Brainlife instructions

2. Edit ~/.bashrc on compute canada (graham) login node to add:

```
export PATH=~/abcd-spec/hooks/slurm:$PATH
module load nodejs singularity
export SINGULARITY_CACHEDIR=/project/6050199/akhanf/singularity-cache
```
Note: Have `SINGULARITY_CACHEDIR` point to a singularity cache folder that is updated remotely (see details on singularity below)

## Configuration for the new resource on brainlife.io


#### Username
Enter your compute canada username
```
akhanf
```

#### Hostname
```
graham.computecanada.ca
```

#### I/O Hostname
Set this to the data transfer host, e.g. for graham: 
```
gra-dtn1.computecanada.ca
```

#### Workdir
`/scratch` is an appropriate location for the work dir, replace with your own folder:
```
/scratch/akhanf/brainlife
```

#### ENV
If you have multiple allocations, you will need to define the account to use as below (replace `ctb-akhanf-ab` with your allocation name):
```
{
    "SLURM_ACCOUNT": "ctb-akhanf-ab",
    "SALLOC_ACCOUNT": "ctb-akhanf-ab",
    "SBATCH_ACCOUNT": "ctb-akhanf-ab"
}
```


## Note on Singularity & Compute Canada:

Pulling/building containers on Compute Canada hosts is sadly not straightforward, for a number of reasons, including limits on memory on login nodes, limited size on /tmp, no internet access on compute nodes, etc.. Some workarounds are described here: https://docs.computecanada.ca/wiki/Singularity, however, those workarounds require pre-downloading singularity images as sif files first, and Brainlife uses singularity exec with the docker uris directly. 

So, a workaround that I have been using so far is to use a local cache folder, but update that cache folder (i.e. pull/build the containers) on a remote system. This can be done by setting SINGULARITY_CACHEDIR on compute canada to a specific path, e.g. `/project/6050199/akhanf/singularity-cache`, then populating the cache located at this path from a remote system. You could use rsync to do this, but what I have done is used a Graham Cloud instance where the remote cache folder is mapped via an ssfhs mount, and simply use `singularity exec docker://app/name:here  bash` on that instance to pull the container if it is not already cached. This could be automated with a list of URIs and a cron job if desired. Then that same command when executed on the login or compute node will find the cached singularity container.
