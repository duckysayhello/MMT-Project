# Project: Intra-Domain Routing Algorithms

## Objective

* Implement distance-vector

## Introduction

The Internet is composed of many independent networks (called autonomous systems) that must cooperate in order for packets to reach their destinations. This necessitates different protocols and algorithms for routing packet within autonomous systems, where all routers are operated by the same entity, and between autonomous systems, where business agreements and other policy considerations affect routing decisions.

This project focuses on intra-domain routing algorithms used by routers within a single autonomous system (AS). The goal of intra-domain routing is typically to forward packets along the shortest or lowest cost path through the network.

The need to rapidly handle unexpected router or link failures, changing link costs (usually depending on traffic volume), and connections from new routers and clients, motivates the use of distributed algorithms for intra-domain routing. In these distributed algorithms, routers start with only their local state and must communicate with each other to learn lowest cost paths.

Nearly all intra-domain routing algorithms used in real-world networks fall into one of two categories, distance-vector or link-state. In this project, you will implement distributed distance-vector and link-state routing algorithms in Python and test them with a provided network simulator.

## Background

At a high level, they work as follows. Your goal in this project is to turn this high-level description to actual working code. You might find it helpful to review the details of the algorithms in textbooks (see course syllabus for textbook recommendations).

### Distance-Vector Routing

* Each router keeps its own distance vector, which contains its distance to all destinations.
* When a router receives a distance vector from a neighbor, it updates its own distance vector and the forwarding table.
* Each router broadcasts its own distance vector to all neighbors when the distance vector changes. The broadcast is also done periodically if no detected change has occurred.
* Each router **does not** broadcast the received distance vector to its neighbors. It **only** broadcasts its own distance vector to its neighbors.

You will notice that the `DVrouter` classes contain several unfinished methods marked with `TODO`. They are:

- `__init__`
- `handle_packet`
- `handle_new_link`
- `handle_remove_link`
- `handle_time`
- `__repr__` (optional, for your own debugging)

## Running and Testing

You should test your `DVrouter` using the provided network simulator. There are multiple JSON files defining different network architectures and link failures and additions. The JSON files without `_events` in their file name do not have link failures or additions and are good for initial testing.

To run the simulation with a graphical interface:

```
usage: visualize_network.py [-h] net_json_path [{DV,LS}]

Visualize a network simulation.

positional arguments:
  net_json_path  Path to the network simulation configuration file (JSON).
  {DV,LS}        DV for DVrouter and LS for LSrouter. If not provided, Router is used.

options:
  -h, --help     show this help message and exit
```

The second argument can be `DV` or `LS` which indicates whether to run `DVrouter` or `LSrouter`, respectively.

To run the simulation without the graphical interface:

```
usage: network.py [-h] net_json_path [{DV,LS}]

Run a network simulation.

positional arguments:
  net_json_path  Path to the network simulation configuration file (JSON).
  {DV,LS}        DV for DVrouter and LS for LSrouter. If not provided, Router is used.

options:
  -h, --help     show this help message and exit
```

The routes to and from each client at the end of the simulation will print, along with whether they match the reference lowest-cost routes. If the routes match, your implementation has passed for that simulation. If they do not, continue debugging (using print statements and the `__repr__` method in your router classes).

The bash script `test_scripts/test_dv_ls.sh` will run all the supplied networks with your router implementations. You can also pass `LS` or `DV` as an argument to `test_scripts/test_dv_ls.sh` (e.g. `./test_scripts/test_dv_ls.sh DV`) to test only one of the two implementations.

Don't worry if you get the following error. It sometimes occurs when the threads are stopped at the end of the simulation without warning:

```
Unhandled exception in thread started by
sys.excepthook is missing
lost sys.stderr
```

## Acknowledgements

This programming project is based on Princeton University's Project 2 from COS 461: Computer Networks, and Johns Hopkins University's Assignment 3 from EN.601.414/614: Computer Networks.
