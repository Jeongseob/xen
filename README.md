 # Accelerating Critical OS Services in Virtualized Systems with Flexible Micro-sliced Cores (EuroSys'18)

 We will release the Xen hypervisor code used for the EuroSys paper. (Comming soon)

## Test machine
Processor: Intel(R) Xeon(R) CPU E5645  @ 2.40GHz x 2

Memory: 32GB per socket

~~~
# xl cpupool-list -c
Name               CPU list
Pool-0             0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23
~~~

## 1. Preparing the micro-sliced pool

We launch two virtual machines which has 12 vcpus on a NUMA node (`pool-1`) and create a micro-sliced-pool (`Hidden-node`) as a child pool of the pool-1.

~~~
# xl cpupool-numa-split
# xl cpupool-list -c
Name               CPU list
Pool-node0         0,1,2,3,4,5,6,7,8,9,10,11
Pool-node1         12,13,14,15,16,17,18,19,20,21,22,23

# xl create /root/guestVMs/avm-01/config.12vcpus pool=\'Pool-node1\'
# xl create /root/guestVMs/avm-02/config.12vcpus pool=\'Pool-node1\'
# xl cpupool-list Pool-node1 -c
Name               CPUs   Sched     Active   Domain count
Pool-node1          12    credit       y          2

# xl cpupool-create -t 1 -s name=\'Hidden-node\'
Using config file "command line"
cpupool name:   Hidden-node
scheduler:      credit
number of cpus: 0
hidden pool: ture
parent pool id: 1
~~~

If you would like to test with static micro-sliced cores (by the static-usliced-cores branch), then you need to manually configure the number of processors in the `Hidden-node` via the cpupool commands such as `xl cpupool-cpu-add`.

With the dyn-usliced-cores branch, you do not need to configure the number of cores in the `Hidden-node` pool. 

## 2. Modifying (or updating) the critical symbols

Our proposed technique takes advantage of the kernel symbol of guest virtual machines to interpret the instruction pointer of preempted or yielded virtual CPUs. So, you may want to change the `is_urgent_kernel()` in the xen/common/schedule.c file. At this moment, our implementation does not have a systematic mechanism to extract the critical OS services from the kernel symbols. We just hard code the part. (NEED to further update) 

 # Authors
 * Jeongseob Ahn
 * Chang Hyun Park
 * Taekyung Heo
 * Jaehyuk Huh
