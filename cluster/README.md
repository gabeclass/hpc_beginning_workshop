# What's an HPC cluster?

[Table of Contents](/hpc_beginning_workshop/)


[![Beowulf Cluster](beowulf2.png)](beowulf.png)
[*By Mukarramahmad (Own work) [Public domain], via Wikimedia Commons*](https://commons.wikimedia.org/wiki/File:Beowulf.png)

## Terminology

* Head Node
  * The server that connects a cluster to the an outside network.
* Compute Node
  * A server that is used for running computations, sometimes managed by a scheduler.
* Cores
  * A shorthand way to refer to the number of
  processor cores (usually physical) of a CPU
  in a node.
  
**IMPORTANT**: *You may run test jobs on the head nodes that run for up to 10 minutes and use up to 10% of the CPU cores and memory. You will likely disrupt the work of others if you exceed these limits.*

## Some Clusters At Princeton
For more detail, see:
[Computational Hardware @ Princeton](https://researchcomputing.princeton.edu/systems-and-services/available-systems)

### [Nobel](https://researchcomputing.princeton.edu/systems-and-services/available-systems/nobel)
  This cluster is most appropriate for interactive work. Any member of the
  university community can request access for their work. Two nodes (Davisson and Compton), no distinction between login/compute.

### [Adroit](https://www.princeton.edu/researchcomputing/computational-hardware/adroit/)
  Also available generally to members of the university community. 9 Skylake nodes with a mix of
  Tesla k20m, k40c and v100 GPUs available.

### Other Clusters
  These other clusters such as Perseus, Tiger, Della and Traverse are research
  clusters with many more nodes and GPFS networked storage between clusters.

  See [https://www.princeton.edu/researchcomputing/access/](https://www.princeton.edu/researchcomputing/access/)
  for details:

  > Proposals for the large  cluster systems should be submitted as PDF or MS Word documents not to exceed 3 pages. The proposal, which can be submitted through an online form . . . etc.

### Quick Start

Connect to Adroit and make a new directory:

```
ssh <YourNetID>@adroit.princeton.edu
mkdir python_test
cd python_test
pwd
```

On your local machine, in a new shell, run the following scp (secure copy) command in the directory containing your script:

```
scp matrix_inverse.py <YourNetID>@adroit.princeton.edu:/home/<YourNetID>/python_test
```

Return to your original session on Adroit:

```
wget https://tigress-web.princeton.edu/~jdh4/job.slurm
# use a text editor to modify job.slurm as follows

#!/bin/bash
#SBATCH --job-name=py-test       # create a short name for your job
#SBATCH --nodes=1                # node count
#SBATCH --ntasks=1               # total number of tasks across all nodes
#SBATCH --cpus-per-task=1        # cpu-cores per task (>1 if multithread tasks)
#SBATCH --mem-per-cpu=4G         # memory per cpu-core (4G is default)
#SBATCH --time=00:01:00          # total run time limit (HH:MM:SS)
#SBATCH --mail-type=begin        # send mail when process begins
#SBATCH --mail-type=end          # send email when job ends
#SBATCH --mail-user=<YourNetID>@princeton.edu
#SBATCH -p class                 # DELETE THIS LINE AFTER WORKSHOP

module load anaconda3
srun python matrix_inverse.py 
```

Submit the job with `sbatch job.slurm`. After the job runs you can view the output with `cat slurm-<XXXXXX>.out`. You will receive an email when the job is finished. You can also run use this command: `squeue -u <YourNetID>`. If this command doesn't list the job then it has finished.

A similar procedure can be used to run an R script. To do this, first make a directory on Adroit for the job:

```
cd ~
mkdir r_test
cd r_test
pwd
```

Use a second shell to transfer the files from your local machine to Adroit:

```
scp data_analysis.R <YourNetID>@adroit.princeton.edu:/home/<YourNetID>/r_test
scp cdc.csv <YourNetID>@adroit.princeton.edu:/home/<YourNetID>/r_test
```

Return to your session on Adroit:

```
wget https://tigress-web.princeton.edu/~jdh4/job.slurm
# use a text editor to modify job.slurm as follows

#!/bin/bash
#SBATCH --job-name=r-test        # create a short name for your job
#SBATCH --nodes=1                # node count
#SBATCH --ntasks=1               # total number of tasks across all nodes
#SBATCH --cpus-per-task=1        # cpu-cores per task (>1 if multithread tasks)
#SBATCH --mem-per-cpu=4G         # memory per cpu-core (4G is default)
#SBATCH --time=00:01:00          # total run time limit (HH:MM:SS)
#SBATCH --mail-type=begin        # send mail when process begins
#SBATCH --mail-type=end          # send email when job ends
#SBATCH --mail-user=jdh4@princeton.edu
#SBATCH -p class                 # DELETE THIS LINE AFTER WORKSHOP

srun Rscript data_analysis.R
```

Submit the job with `sbatch job.slurm`. You will receive an email when the job is finished. After the job runs you can view the output with `cat slurm-<XXXXXX>.out`.

### Learn More About Adroit by Running Commands

Type each command below and examine the output:

```
hostname                  # get the name of the machine you are on
whoami                    # get username of the account
date                      # get the current date and time
pwd                       # print working directory
cat /etc/os-release       # info about operating system
lscpu                     # info about the CPUs on head node
snodes                    # info about the compute nodes
squeue                    # which jobs are running or waiting to run
who                       # list users on the head node
```

Here is example output from the commands above:

```
[jdh4@adroit4 ~]$ hostname
adroit4



[jdh4@adroit4 ~]$ whoami
jdh4



[jdh4@adroit4 ~]$ date
Sat Sep 14 15:15:11 EDT 2019


[jdh4@adroit4 ~]$ pwd
/home/jdh4



[jdh4@adroit4 ~]$ cat /etc/os-release 
NAME="Springdale Linux"
VERSION="7.7 (Verona)"
ID="rhel"
ID_LIKE="fedora"
VERSION_ID="7.7"
PRETTY_NAME="Springdale Linux 7.7 (Verona)"
ANSI_COLOR="0;32"
CPE_NAME="cpe:/o:springdale:linux:7.7:GA"
HOME_URL="http://springdale.princeton.edu/"
BUG_REPORT_URL="https://springdale.math.ias.edu/"

REDHAT_BUGZILLA_PRODUCT="Springdale Linux 7"
REDHAT_BUGZILLA_PRODUCT_VERSION=7.7
REDHAT_SUPPORT_PRODUCT="Springdale Linux"
REDHAT_SUPPORT_PRODUCT_VERSION=7.7



[jdh4@adroit4 ~]$ lscpu
Architecture:          x86_64
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                32
On-line CPU(s) list:   0-31
Thread(s) per core:    1
Core(s) per socket:    16
Socket(s):             2
NUMA node(s):          2
Vendor ID:             GenuineIntel
CPU family:            6
Model:                 85
Model name:            Intel(R) Xeon(R) Gold 6142 CPU @ 2.60GHz
Stepping:              4
CPU MHz:               1021.179
CPU max MHz:           3700.0000
CPU min MHz:           1000.0000
BogoMIPS:              5200.00
Virtualization:        VT-x
L1d cache:             32K
L1i cache:             32K
L2 cache:              1024K
L3 cache:              22528K
NUMA node0 CPU(s):     0,2,4,6,8,10,12,14,16,18,20,22,24,26,28,30
NUMA node1 CPU(s):     1,3,5,7,9,11,13,15,17,19,21,23,25,27,29,31
Flags:                 fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush dts acpi mmx fxsr sse sse2 ss ht tm pbe syscall nx pdpe1gb rdtscp lm constant_tsc art arch_perfmon pebs bts rep_good nopl xtopology nonstop_tsc aperfmperf eagerfpu pni pclmulqdq dtes64 monitor ds_cpl vmx smx est tm2 ssse3 sdbg fma cx16 xtpr pdcm pcid dca sse4_1 sse4_2 x2apic movbe popcnt tsc_deadline_timer aes xsave avx f16c rdrand lahf_lm abm 3dnowprefetch epb cat_l3 cdp_l3 intel_ppin intel_pt ssbd mba ibrs ibpb stibp tpr_shadow vnmi flexpriority ept vpid fsgsbase tsc_adjust bmi1 hle avx2 smep bmi2 erms invpcid rtm cqm mpx rdt_a avx512f avx512dq rdseed adx smap clflushopt clwb avx512cd avx512bw avx512vl xsaveopt xsavec xgetbv1 cqm_llc cqm_occup_llc cqm_mbm_total cqm_mbm_local dtherm ida arat pln pts pku ospke md_clear spec_ctrl intel_stibp flush_l1d



[jdh4@adroit4 ~]$ snodes
HOSTNAMES     STATE    CPUS S:C:T    CPUS(A/I/O/T)   CPU_LOAD MEMORY   GRES     PARTITION          AVAIL_FEATURES
adroit-01     idle     20   2:10:1   0/20/0/20       0.01     128000   (null)   class              ivy
adroit-02     idle     20   2:10:1   0/20/0/20       0.01     64000    (null)   class              ivy
adroit-03     idle     20   2:10:1   0/20/0/20       0.01     64000    (null)   class              ivy
adroit-04     idle     20   2:10:1   0/20/0/20       0.01     64000    (null)   class              ivy
adroit-05     idle     20   2:10:1   0/20/0/20       0.01     64000    (null)   class              ivy
adroit-06     idle     20   2:10:1   0/20/0/20       0.01     64000    (null)   class              ivy
adroit-07     idle     20   2:10:1   0/20/0/20       0.01     64000    (null)   class              ivy
adroit-08     mix      32   2:16:1   31/1/0/32       19.93    384000   (null)   all*               skylake
adroit-09     idle     32   2:16:1   0/32/0/32       0.01     384000   (null)   all*               skylake
adroit-10     mix      32   2:16:1   25/7/0/32       16.58    384000   (null)   all*               skylake
adroit-11     mix      32   2:16:1   30/2/0/32       11.01    384000   (null)   all*               skylake
adroit-12     mix      32   2:16:1   30/2/0/32       10.98    384000   (null)   all*               skylake
adroit-13     mix      32   2:16:1   24/8/0/32       4.20     384000   (null)   all*               skylake
adroit-14     mix      32   2:16:1   20/12/0/32      20.01    384000   (null)   all*               skylake
adroit-15     idle     32   2:16:1   0/32/0/32       0.01     384000   (null)   all*               skylake
adroit-16     mix      32   2:16:1   5/27/0/32       3.01     384000   (null)   all*               skylake
adroit-h11g1  idle     40   2:20:1   0/40/0/40       0.04     770000   gpu:tesl gpu                (null)
adroit-h11g4  idle     16   2:8:1    0/16/0/16       0.61     64000    gpu:tesl gpu                (null)



[jdh4@adroit4 ~]$ squeue
             JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
            660090       all run_05_0    fanid PD       0:00      1 (QOSMaxJobsPerUserLimit)
            661432       all       KL   mlenel PD       0:00      1 (Dependency)
      661431_[6-7]       all       KL   mlenel PD       0:00      1 (Dependency)
            661439       all sys/dash lclingan  R      37:28      1 adroit-10
          661429_6       all       KL   mlenel  R    1:13:28      1 adroit-08
          661429_7       all       KL   mlenel  R    1:13:28      1 adroit-08
            660093       all run_15_0    fanid  R 3-20:26:13      1 adroit-16
            660092       all run_15_0    fanid  R 3-20:27:14      1 adroit-08
            660091       all run_15_0    fanid  R 3-20:28:42      1 adroit-08
            660087       all run_15_1    fanid  R 9-12:05:41      1 adroit-08
            660999       all BBR_m06-    kyras  R 2-21:31:33      1 adroit-12
            660996       all BMR_m06-    kyras  R 2-21:40:50      1 adroit-11
            660085       all run_1_1_    fanid  R 10-00:38:49      1 adroit-08
            660086       all run_05_1    fanid  R 10-00:38:49      1 adroit-08
            660088       all run_05_0    fanid  R 10-00:38:49      1 adroit-16
            660089       all run_05_0    fanid  R 10-00:38:49      1 adroit-16
            660116       all tBu3PPd_     leit  R 8-05:22:17      1 adroit-11
            660114       all Phdiimin     leit  R 8-14:31:04      1 adroit-14
            660115       all tBu3PPd_     leit  R 8-14:31:04      1 adroit-14
            660113       all Phdiimin     leit  R 9-12:05:41      1 adroit-12
            661298       all job.scri vincenzi  R    6:37:12      1 adroit-13
            661391       all run_cali  hherman  R    3:59:23      1 adroit-10
 
 
 
[jdh4@adroit4 ~]$ who
mlenel   pts/0        2019-09-14 10:15 (vpn10-client-128-112-69-121.princeton.edu)
root     pts/1        2019-09-03 12:45 (delta.princeton.edu)
mlenel   pts/2        2019-09-14 10:16 (adroit4.princeton.edu)
mlenel   pts/8        2019-09-14 13:55 (vpn10-client-128-112-71-230.princeton.edu)
hherman  pts/10       2019-09-14 14:06 (vpn10-client-128-112-69-8.princeton.edu)
lclingan pts/11       2019-09-14 14:33 (myadroit)
lclingan pts/12       2019-09-14 14:34 (myadroit)
jdh4     pts/14       2019-09-14 15:02 (vpn10-client-128-112-70-165.princeton.edu)
tcyr     pts/15       2019-09-14 15:05 (nat-oitwireless-inside-vapornet100-10-9-77-247.princeton.edu)
```
