**If you're a meren lab member, visit here for instructions on how to use our default parameters: https://github.com/merenlab/lab-fiesta/wiki/Working-on-Midway#clusterize.**


# clusterize


<img src="clusterize_demo.gif" width="450" />

The most basic python SLURM wrapper imaginable. The intent is to avoid the use of creating sbatch files by instead prefixing your single-line command with `clusterize`.

## Requirements

This is written in python3.

## Installation

Installation will depend upon your permissions. First, try

```
git clone https://github.com/ekiefl/clusterize.git
cd clusterize
python setup.py install
clusterize -h
```

Did that bring up the help menu? If not, try:

```
cd ..
clusterize -h
cd -
```

(For some reason it be like that) Did that bring up the help menu? If not, try:

```
python setup.py install --user
clusterize -h
```

If that also didn't bring up the help menu, try and find the line:

```
Installing clusterize script to <SOME_PATH>
```

and try `<SOME_PATH>/clusterize -h`. if that brought the help menu, you simply need to add `<SOME_PATH>` to your `$PATH` variable.

Please contact me if none of these worked, especially if you found a solution to your problem.


## Usage

### Submitting a job

The command to be submitted should be enclosed double-quotes. If the command itself contains double-quotes, they should be preceded by `\`.

```
clusterize "echo hello"
clusterize "./my_script.sh"
clusterize "echo \"$HOME\""
```

### Providing sbatch parameters

You can provide one-time sbatch parameters to your command to adjust the partition, number of cores, time limits, etc:

```
clusterize "echo hello" --partition my_partition \
                        --job-name my_job \
                        --num-nodes 1 \
                        --num-tasks-per-node 10
```

Since this README was last updated, here are the options available:

```
-p PARTITION, --partition PARTITION
                        Which partition of the cluster are you using?
  -o JOB_NAME, --job-name JOB_NAME
                        Give a useful name to your job. It doesn't have to be
                        unique, as a unique ID is appended to whatever you
                        pick. This name will show up in the SLURM queue, and
                        will be the prefix for the .out and .err files of the
                        job. as an example, if the job name is `job`, it may
                        show in the queue as `job_hcwknUCbSr`, and the outputs
                        will be `job_hcwknUCbSra.out` and
                        `job_hcwknUCbSra.err`. The default is simply
                        clusterize
  -N NUM_NODES, --num-nodes NUM_NODES
                        How many nodes you want to use? default is 1
  -n NUM_TASKS_PER_NODE, --num-tasks-per-node NUM_TASKS_PER_NODE
                        How many nodes you want to use? default 1
  -t ALLOTTED_TIME, --allotted-time ALLOTTED_TIME
                        After this amount of time, the process will be killed
                        :( The default is 4-0:00:00, which could be higher
                        than your cluster allows. One acceptable time format
                        is HH:MM:SS, i.e. 15 hours would be: `15:00:00`
  -M MEM_PER_CPU, --mem-per-cpu MEM_PER_CPU
                        How much memory in MB should be allotted per cpu?
                        Default is 10000
```

## Default Configuration

More conveniently, you can create a default configuration file that stores default parameters for you. For example, rather than providing `--partion your_partition` each time you run `clusterize`, you can instead add a default partition value to the configuration file.

### Editing Your Configuration File

The very first time `clusterize` is first ran, it creates a default configuration file at `~/clusterize_config` that looks like this:

```
[CLUSTERIZE_DEFAULTS]
job_name = clusterize
partition = None
num_nodes = 1
num_tasks_per_node = 1
allotted_time = 4-0:00:00
mem_per_cpu = 10000

```

Each time after clusterize is ran, it grabs defaults from this configuration file. So probably the first thing you want to do is change partition from `None` to whatever your partition is. Note that if you provide explicit parameters, e.g. `--partition`, they will take priority over the defaults found in this file.

### Sharing A Configuration File

If your team already has a configuration file and you want to adopt it, you can ensure clusterize reads that configuration file instead of `~/.clusterize_config`. To do so, open up `~/.clusterize_pointer` and change its default contents from

```
$HOME/.clusterize_config
```

To your new configuration file:

```
<path-to-new-config-file>
```

From now on, clusterize will read defaults from this config file.

### Creating A Template

Finally, if you want to generate a new configuration file, you can do so with:

```
clusterize "asdf" --gen-new-config-file <path-to-new-config-file>
```

Remember to point `clusterize` to this new file once you are happy with it by modifying `~/.clusterize_pointer` as described above
