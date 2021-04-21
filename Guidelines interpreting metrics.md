# Interpreting monitoring metrics

*This post is Part 3 of a 3-part series about setting up proper monitoring on your SafeCoin Validator.*

* [Part 1.](https://github.com/safegw/SafeCoin-Monitoring/blob/main/README.md) SafeCoin Validator Monitoring Tool
* [Part 2.](https://github.com/safegw/SafeCoin-Monitoring/blob/main/How%20to%20Install%20TIG%20Stack.md) How to Install Telegraf, InfluxDB, and Grafana
* [Part 3.](https://github.com/safegw/SafeCoin-Monitoring/blob/main/Guidelines%20interpreting%20metrics.md) Interpreting monitoring metrics

## Interpreting monitoring metrics

### Telegraf | A Metrics Collector For InfluxDB

Telegraf can collect metrics from a wide array of inputs and write them to a wide array of outputs. It is plugin-driven for both collection and output of data so it is easily extendable. It is written in Go, which means that it is compiled and standalone binary that can be executed on any system with no need for external dependencies, or package management tools required.

![Architecture](https://i.imgur.com/xmbND94.png)

### The Telegraf agent runs on the Validator node and sends metrics data to your InfluxDB database. 

### Metrics Explained

#### Server performance metrics:
- Server uptime
- Server Load Average
- Server memory utilization - Used, cached, free
- CPU utilization
- Number of CPU cores and each cpu utilization
- Processes - stopped, sleeping, running e.t.c
- Disk Utilization - Free and used space for / and all othe system partitions
- Disk Inodes - / and all othe partitions in the system
- Open Files
- Swap - usage and IO
- Disk IO - requests, bytes and time per disk
- Disk Usage, ramdisk usage if used.

#### SafeCoin Validator Application performance metrics:
- Validator Status. Is your validator health ok and validating
- Epoch progress
- Active Stake
- Leaderslots, missed slots and last voted slot
- Skiprate and Cluster skiprate measured from your local validator RPC.
- SafeCoin version
- Validator fee
- Balance of your identity and vote accounts

### Things you should be looking for in your grafana dashboard:
To have a good performing server and validator, all the different metrics in the dashboard should be in it's best state. When one of the components in the table below if in a red state. the rest of the server would suffer from it and will probably result in high skiprate or a very short NVMe disk life. depending on what's going on.

I have put most metrics in a detailed table, the normal and alarm table states what normal and alarm values are + some details on what to do when numbers look bad.


| metric  | normal | alarm | details|
|---------|--------|-------|--------|
|Load (LA)| 1-15 | >15 | Server load is important. When server load is extremely high it's a good indicator something is wrong. I have seen scenario's with too little CPU cores, or too slow NVMe disks causing very high server load |
|Memory usage| 1-25%  | >25%  | Memory usage is split between total, cache, used and free.|
|IOWait |0-3%|>3%|IOWait is pretty important measurement. SafeCoin validators need fast NVMe disks and having much IOWait time basically means your disk is too slow to catch up.|
|Disk Usage| 0-70% | >70% | Make sure you have enough free disk space available, depending on the options used in validator startup file.|
|Swap Usage| 0% | >1% | You basically want your server not to use swap. Sometimes this cannot be prevented but having a server use the swapspace means it's out of memory|
|Ramdisk Usage| 0-20%| >20% | When you use a ramdisk you want to make sure it can expand to at least:  memorysize - 20GB + swapfile. for example: when your server has 128GB memory - 20GB for the validator processes + 128GB Swapfile = Your ramdisk needs to be 236GB.
|Status| Validatating | Delinquent | Metric shows if your validator is online, delinquent or in error.|
|Active Stake| your stake | 0 | This metric should show your active stake.|
|Last slot voted| | | Metric should show the last slot your validator has voted on. This value should progress every 15-30 seconds.|
|Skiprate|0-25%|>25%|Skiprate is pretty important measurement of how your server is performing. Having more than 25% skiprate normally implies something is wrong. Most of the time it's diskspeed, lack of processor cores, high latency or throughput.|


![Metrics-Explained](https://i.imgur.com/oTD0Uc4.png)


