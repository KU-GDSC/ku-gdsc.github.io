# Detailed Guides 

### SSH Configuration

Normally, accessing the cluster via `ssh` and `scp` requires you to enter
your username and password each time. However, it is possible to create a
configuration profile on your local computer that will make this process
more efficient. Using a text editor, you can create or edit the file 
`~/.ssh/config` to include the following lines:

!!! note "Editing ~/.ssh/config"

    ```bash
    Host cluster
        HostName hpc.crc.ku.edu
        User USERNAME
    ```

Where `USERNAME` is your own username. The host `cluster` is arbitrary, you
any name you like. After editing or creating this file, you can now use
less complicated `ssh` and `scp` commands to log in or copy files.

!!! note "Log in with an SSH config file"

    ```bash
    ssh cluster
    ```

!!! note "Copy a file from your present working directory on your computer to your home directory on the cluster"

    ```bash
    scp myfile.txt cluster:/home/USERNAME
    ```

## Working interactively on the cluster

When you initially log in to the cluster you are on a "login node," which
is designed for handling logins and basic tasks such as submitting 
job scripts to the resource scheduler, but is not designed to
handle resource-intensive analysis work. When you are on a login node, 
the command prompt will look like this:

!!! note ""
    ```bash
    [USERNAME@submit1 ~]$
    ```

If you want to work interactively
on the cluster you must first request a "compute node," which lets you
specify the number of CPUs, the amount of memory, etc. To request a
compute node, use a command such as the following:

!!! note ""

    ```
    srun --nodes=1 --mem=128G --time=01:00:00 --partition=sixhour --pty /bin/bash -l
    ```

This command will allocate the resources you've requested, which in this case
is a single node, with 128GB of memory, for 1 hour, using the "sixhour"
community cluster. When you enter this command, you should see something
like this:


!!! note ""

    ```
    srun: job 55741230 queued and waiting for resources
    srun: job 55741230 has been allocated resources
    ```

And you should notice that the command prompt has changed, and reflects
that you are working on a compute node:

!!! note ""

    ```bash
    [USERNAME@r11r12n03 ~]$
    ```

The program `crctool` allows you to see what resources you have available to request, either for interactive sessions or in [job submission scripts](#setting-up-long-running-jobs-on-the-cluster)

## Running Nextflow pipelines on the cluster

We have prepared some software modules to help make it more convenient to run pipelines on the cluster.
In order to use these modules, you need to run the following commands:

!!! note ""

    ```bash
    module use --append /panfs/pfs.local/work/sjmac/observer/modules
    module load nextflow
    ```

The first line adds the set of modules the GDSC has prepared to your profile. The second line loads the module `nextflow`, which also load a few required programs such as `java` and `singularity`.

**Note**: these commands need to be run every time you log in, or in the body of any script that you run, in order for the modules to be loaded properly. In order to have this happen automatically, you can add those lines to the file `~/.bashrc`, which is used to load various programs every time you log into the cluster.

To test that everything is working correctly, you can run a simple test program with the command:

  `nextflow run hello`
  
If everything is loaded properly, you should see the following output

!!! note ""

    ```
    N E X T F L O W  ~  version 23.04.3
    Launching `https://github.com/nextflow-io/hello` [confident_picasso] DSL2 - revision: 1d71f857bb [master]
    executor >  local (4)
    [5a/e0c8dc] process > sayHello (2) [100%] 4 of 4 ✔
    Hello world!
  
    Bonjour world!
  
    Hola world!
  
    Ciao world!
    ```
  
## Setting up long-running jobs on the cluster

Rather than running programs directly from the command line, as we've been doing so far, it often makes sense to prepare scripts that can be submitted to the CRC job scheduler to run in the background. This has several benefits:

1. Jobs run this way aren't tied to your login session, so if your computer goes to sleep or you get disconnected, they will continue running until they're completed.

1. You can request much larger amounts of computing resources this way, and so it can make your job run more efficiently.

1. The job scheduler can notify you via e-mail when your job completes (successfully or otherwise) so you don't need to keep a constant eye on things.

An example job script to run the simple Nextflow test job above looks like this:

!!! note ""

    ```bash
    #!/bin/bash
    #SBATCH --job-name=nextflow_test
    #SBATCH --mail-type=END,FAIL
    #SBATCH --mail-user=<YOUR_EMAIL_ADDRESS>
    #SBATCH --ntasks=1
    #SBATCH --cpus-per-task 1
    #SBATCH --time=24:00:00
    #SBATCH --partition=sjmac
    #SBATCH --mem=10G
    #SBATCH --output=%x_%A.out

    module use --append /panfs/pfs.local/work/sjmac/observer/modules
    module load nextflow

    NXF_ANSI_SUMMARY=true
    NXF_ANSI_LOG=true

    nextflow run hello
    ```

The lines beginning with `#SBATCH` are all directions to the job submission system to specify how much memory you want (`#SBATCH --mem=`), how much time you want to allocate for the job (`#SBATCH --time`), and may other. More detail about job submission scripts is available on on the [CRC documentation website](https://docs.crc.ku.edu/how-to/submitting-jobs/).

Try using the above example to make a script called `run_script.sh` on the cluster. Modify the line 
`#SBATCH --mail-user=<YOUR_EMAIL_ADDRESS>` to replace `<YOUR_EMAIL_ADDRESS>` with your actual email address (e.g. `#SBATCH --mail-user=gdsc@ku.edu`).

**sbatch** Batch job submission to the Slurm job scheduler on the cluster.

!!! note "Submit a job script to run an analysis"

    ```
    sbatch run_script.sh
    ```

**squeue** Provide the status of running jobs on the cluster

!!! note "Check the status of jobs that you have submitted"

    ```
    squeue -u USERNAME
    ```

The output should look like this:

!!! note ""

    ```
    JOBID     PARTITION     NAME     USER     ST     TIME     NODES     NODELIST(REASON) 
    55741582_1     sjmac     treemix     observer     R     20:27:52     1     r11r12n02
    55741582_2     sjmac     treemix     observer     R     20:27:52     1     r06r28n03
    55741582_3     sjmac     treemix     observer     R     20:27:52     1     r15r18n02
    55741582_4     sjmac     treemix     observer     R     20:27:52     1     r15r18n02
    55741582_5     sjmac     treemix     observer     R     17:31:00     1     r11r12n02   
    ```

The column "ST" refers to the state of the job. The most common states are:

-   PD: Pending

-   R: Running

-   CG: Completing

**scancel** Cancel a job that is running on the cluster

!!! note "Cancel a specific job with the JobID (the first column of the **squeue** output)"

    ```
    scancel 0000001
    ```

!!! note "Cancel all jobs that you have submitted that are running or pending"

    ```
    scancel -u USERNAME
    ```

This is useful if you make a mistake with a big batch job that submits many subjobs


