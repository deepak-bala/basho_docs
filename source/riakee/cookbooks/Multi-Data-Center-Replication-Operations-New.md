---
title: "Multi Data Center Replication: Operations (New)"
project: riakee
version: 1.3.0+
document: cookbook
toc: true
audience: intermediate
keywords: [mdc, repl, operator, bnw]
---

## The riak-repl Command

Replication is controlled by the `riak-repl` command. Usage:

### Cluster Connectivity

**clustername**

Set the `clustername` for all nodes in a Riak cluster. *This only needs to be run once on a single node of a cluster for the changes to propagate to all other nodes.*

To **set** the clustername:

  * *Syntax:* `riak-repl clustername <clustername>`
  * *Example:* `riak-repl clustername Boston`

To **get** the clustername:

  * *Syntax:* `riak-repl clustername`
  * *Example:* `riak-repl clustername`


**connect**

The `connect` command establishes communications from a source cluster to a sink cluster.  The host:port of the sink cluster is used for this.  Host can be either an IP address or a hostname that will resolve to one. The IP and port to connect to can be found in the app.config of the remote cluster, under `riak_core » cluster_mgr`. Once a connection is established, one can continue to configure real time or fullsync replication.

**NOTE** 

The cluster_mgr configuration variable in app.config must be an IP; it may not be a hostname.

* *Syntax* `riak-repl connect <ip>:<port>`
* *Syntax*	`riak-repl connect <host>:<port>`

* *Example* `riak-repl connect 192.168.2.1:8085`
* *Example*	 `riak-repl connect Austin:8085`


**disconnect**

Disconnecting a source cluster from a sink cluser can be done either by host:port or by using the name of cluster.

* *Syntax* `riak-repl disconnect <host>:<port>`
* *Example* `riak-repl disconnect 192.168.2.1:8085`

* *Syntax* `riak-repl disconnect <sink_clustername>`
* *Example* `riak-repl disconnect Austin`



###Realtime Replication Configuration

**realtime enable**

**realtime disable**

Enable or disable realtime replication from a source cluster to sink clusters.

* *Syntax* `riak-repl realtime enable <sink_clustername>`
* *Syntax* `riak-repl realtime disable <sink_clustername>`

* *Example* `riak-repl realtime enable Austin`
* *Example* `riak-repl realtime disable Austin`


**realtime start**

**realtime stop**

Start or stop realtime replication from a source cluster to sink clusters.

* *Syntax* `riak-repl realtime start <sink_clustername>`
* *Syntax* `riak-repl realtime stop <sink_clustername>`

* *Syntax* `riak-repl realtime start <sink_clustername>`
* *Syntax* `riak-repl realtime stop <sink_clustername>`


To start or stop replication for all configured sink clusters:


* *Example* `riak-repl realtime start`
* *Example* `riak-repl realtime stop`


###Fullsync Replication Configuration

**fullsync enable**

**fullsync disable**

By default, a fullsync will start as soon as a connection is established to the remote cluster.  This behavior can be altered by using the app.config `fullsync_on_connect`.

* *Syntax* `riak-repl fullsync enable <sink_clustername>`
* *Syntax* `riak-repl fullsync disable <sink_clustername>`

* *Example* `riak-repl fullsync enable Austin`
* *Example* `riak-repl fullsync disable Austin`


**fullsync start**

**fullsync stop**

Starts or stops a fullsync. 

If the application configuration `fullsync_on_connect` is set to false, a fullsync needs to be started manually.  This is also used to periodically fullsync using a cron job.  While a fullsync is in progress, a start command is ignored; a message is printed.

* *Syntax* `riak-repl fullsync start <sink_clustername>`
* *Syntax* `riak-repl fullsync stop <sink_clustername>`

* *Enable* `riak-repl fullsync start Austin`
* *Enable* `riak-repl fullsync stop Austin`


### Tuning

**fullsync max_fssource_node**

The `max_fssource_node` limits the number of fullsync workers that will be running on each individual node in a source cluster.  This is a hard limit for all fullsyncs enabled; additional fullsync configurations will not increase the number of fullsync workers allowed to run on any node. This value defaults to 2. This only affects nodes on the source cluster on which this parameter is defined via the configuration file or command line.

Example: ***TODO*** move to configuration section
{max_fssource_node, 2}


* *Syntax* `riak-repl fullsync max_fssource_node <value>`
* *Default* 2

* *Example* `riak-repl fullsync max_fssource_node 2`

**fullsync max_fssource_cluster**

The ‘max_fssource_cluster’ is the hard limit of fullsync workers that will be running on the source side of a cluster across all nodes on that cluster for a fullsync to a sink cluster.  This means if one has configured fullsync for two different clusters, both with a max_fssource_cluster of 5, 10 fullsync workers can be in progress.  This value defaults to 5. Only affects nodes on the source cluster on which this parameter is defined via the configuration file or command line.


Example: Example: ***TODO*** move to configuration section
{max_fssource_cluster, 5}


* *Syntax* `riak-repl fullsync max_fssource_cluster <value>`
* *Default* 5

* *Example* `riak-repl fullsync max_fssource_cluster 5`


**fullsync max_fssink_node**

The ‘max_fssink_node’ limits the number of fullsync workers allowed to run on each individual node in a sink cluster.  This is a hard limit for all fullsync sources interacting with the sink cluster. Thus, multiple simultaneous source connections to the sink cluster will have to share the sink node’s number of maximum connections. This value defaults to 2. Only affects nodes on the sink cluster on which this parameter is defined via the configuration file or command line.

* *Syntax* `riak-repl fullsync max_fssink_cluster <value>`
* *Default* 2

* *Example* `riak-repl fullsync max_fssink_cluster 5`



## riak-repl status output

The following definitions describe the output of `riak-repl status`. Please note that many of these statistics will only appear on the current leader node.

**All counts will be reset to 0 upon restarting Riak EE unless otherwise noted.**



###Realtime Replication Statistics

How to access number of objects sent, number of objects pending, number of objects dropped via JSON stats:

- *source*
	- *source_stats*
		- *rt_source_connected_to* 
			
			The name of the sink cluster that the source cluster is connected. *Soon to be renamed.*
			
		- *connected*
		
			<true/false> If true, then the source is connected to a sink.
		
		- *objects*
			
			<number> The number of realtime replication objects that have been successfully transmitted to the sink cluster.
			
		- *sent_seq*
    	
    		<number> The last realtime queue sequence number that has been transmitted.



- realtime_queue_stats:

	- *bytes* 
	
	The size in bytes of all objects currently in the realtime queue.
	
	- *consumers* 
	
	A list of source consumers of the realtime queue.
	
	- *sinkclustername*
	
	A consumer of the realtime queue.
	
	- *drops* 
	
	The number of objects dropped from the realtime queue as the result of the queue being full or other errors.
	
	- *errs* 
	
	The number of errors while pushing/popping from the realtime queue.
	
	- *pending*
	
	The number of objects waiting to be sent to the sink cluster.	
	- *unacked*
	
	The number of objects waiting to be acknowledged by a queue consumer.


- *rt_dirty*

	The number of rt_source_errors and/ort rt_sink errors have been detected. ***This value will persist across restarts until a fullsync is complete.***

- *rt_sink_errors*
	A sink error has been detected on the source node. This value will be reset to 0 after a node restarts.

- *rt_source_errors*
	A source error has been detected on the source node. This value will be reset to 0 after a node restarts.




###Fullsync Replication Statistics

The fullsync_coordinator will only be populated on clusters that act as a source.



fullsync_coordinator: [ {<RemoteClusterName>: <fullsync_stats>}, ... ]
	fullsync_stats:  {
		cluser:  <RemoteClusterName>,
		queued:  <PartitionsWaiting>, // Number of partitions that are waiting for an available process
		in_progress: <PartitionsWaiting>, // Number of partitions what are being synced
		starting:  <PartitionsIniting>, // Number of partitions getting a connection to remote cluster
		successful_exits:  <PartitionsDone>, // Number of partitions successfully synced; when completed this will be the same number as total number of partitions in the ring
		error_exits:  <PartitionsRequeued>, // If a sync failed or aborted, the partition will be queued again and try again later.
		running_stats:  [ {<ProcessId>, <running_stats>}, …] // Any running sync processes are listed here.
		socket:  {peername: <RemoteIP:Port>, sockname: <LocalIP:Port>},
{fullsync_suggested,[ <NodeID>]},	// Realtime replication errors occurred on these nodes, a fullsync is suggested
                         {fullsync_suggested_during_fs,[<NodeID>]}]}] // Realtime replication errors occurred on these nodes while a fullsync is already in progress, a fullsync is suggested after the current fullsync completes. These value will be moved to the fullsync_suggested value when the current fullsync complete.

	}
	running_stats:  {
                      node:  <NodeInLocalCluster>,
		site: <RemoteClusterName>,
		strategy: fullsync,
		fullsync_worker: <ProcessId>
		socket: {peername:  <RemoteIP:Port>,sockname: <LocalIP:Port>},
		state, <statename>,
		fullsync, <PartitionKey>,
		partition_start, <SecondsSincePartitionStart>
		start_state, <SecondsSinceStateStart>
		get_pool_size, <Number>
	}

When acting as a sink:
fullsync_coordinator_srv: [ {<LocalIP:Port>: <fullsync_coordinator_srv_stats>}, …]
	fullsync_coordinator_srv_stats: {socket: <socket_stats>}

Socket Stats

Many sections of the status output include a “socket” section. A reading is taken once every 10 seconds, and the last 7 readings are stored.

"socket": {
   "peername": "127.0.0.1:9086", The address and port for the other end of a connection.
   "recv_avg": "[32, 32]", 	Average size of packets in bytes received to the socket
   "recv_cnt": "[0]", 		Number of packets received to the socket.
   "recv_dvi": "[0, 0]", 		Average packet size deviation in bytes received to the socket.
   "recv_kbps": "[0]", 		Socket kilobits/second received.
   "recv_max": "[33, 33]", 	The size of the largest packet in bytes received to the socket.
   "send_cnt": "[0]", 		Number of packets sent from the socket.
   "send_kbps": "[0]", 		Socket kilobits/second sent.
   "send_pend": "[0, 0]", 	The number of bytes in the Erlang VM to be sent over the socket.
   "sockname": "127.0.0.1:57559" The address and port for “this end” of the connection.
}









# Stats below need to be weeded out!


* **listener_[nodeid]**: "ip:port"

    Defines a replication listener that is running on node [nodeid].

* **[sitename]_ips**

    Defines a replication site.

* **client_bytes_recv**

    The total number of bytes the client has received since the server has been started. 

* **client_bytes_sent**

    The total number of bytes sent to all connected sites. 

* **client_connect_errors**
    
    The number of TCP/IP connection errors. 

* **client_connects**
    
    A count of the number of site connections made to this node. 

* **client_redirect**

    If a client connects to a non-leader node, it will be redirected to a leader node. 

* **client_rx_kbps**

    A snapshot of the client (site) received kilobits/second taken once a minute. The past 8 snapshots are stored in this list. Newest snapshots appear on the left side of the list.

* **client_tx_kbps**

    A snapshot of the client (site) sent kilobits/second taken once a minute. The past 8 snapshots are stored in this list. Newest snapshots appear on the left side of the list.


* **elections_elected**

    If the replication leader node becomes unresponsive or unavailable, a new leader node in the cluster will be elected.  

* **elections_leader_changed**
  
  The number of times a Riak node has surrendered leadership.
    
* **objects_dropped_no_clients**
    
    If the real-time replication work queue is full and there aren't any clients to receive objects, then objects will be dropped from the queue. These objects will be synchronized during a fullsync.

* **objects_dropped_no_leader**
    
    If a client (site) cannot connect to a leader, objects will be dropped during real-time replication.
    
* **objects_forwarded**

   The number of Riak objects forwarded to the leader the participate in replication. *Please note that this value will only be accurate on a non-leader node.*

* **objects_sent**

    The number of objects sent via real-time replication.

* **server_bytes_recv**

    The total number of bytes the server (listener) has received. 

* **server_bytes_sent** 

    The total number of bytes the server (listener) has sent. 

* **server_connect_errors**

    The number of listener to site connection errors.

* **server_connects**

    The number of times the listener connects to the client site.

* **server_fullsyncs**
    
    The number of full-synchronizations that have occurred since the server was started. 

* **server_rx_kbps**

   A snapshot of the server (listener) received kilobits/second taken once a minute. The past 8 snapshots are stored in this list. Newest snapshots appear on the left side of the list. 

* **server_tx_kbps** 

   A snapshot of the server (listener) sent kilobits/second taken once a minute. The past 8 snapshots are stored in this list. Newest snapshots appear on the left side of the list. 


* **local_leader_message_queue_len**
    
    The length of the object queue on the leader.
    
* **local_leader_heap_size**

  The amount of memory the leader is using.

 * **client_stats**
 
 * **server_stats**
 
## Client Statistics

   
* **node** 
    A unique ID for the Riak node that the client (site) in running on.
    
* **site**
   
  The connected site name configured with `riak-repl add-site
  
* **strategy** 
  
  A replication strategy defines an implementation of the Riak Replication protocol.
  
    Valid values are:
  * **keylist**
    * This replication strategy appeared in Riak EE 1.1. This is the default and preferred replication strategy.
  * **syncv1**
    * *syncv1 is a legacy replication strategy and shouldn't be used.*

* **fullsync_worker**
     
    The Erlang process ID of the full synchronization worker.
    
* **waiting_to_retry**

    The listeners currently waiting to retry replication after a failure.

* **connected**

  A list of connected clients

  - **connected** 
    
    * The IP address and port of a connected client (site).
  
  - **cluster_name**

    * The name of the connected client (site).
    
* **connecting**
  
  The PID, IP address and port of a client currently establishing a connection.
   
   
* **state**
  * State shows what the current replication strategy is currently processing. 
  
  * The following definitions appear in the status output if keylist strategy is being used. They can be used by Basho support to identify replication issues.
      
    * **request_partition**     
    * **wait_for_fullsync**
      * **send_keylist**
      * **wait_ack**
      
        
## Server Statistics

* **node**
* **site**
* **strategy**
* **fullsync_worker**

Please see the definitions in the **Client Statistics** section.

* **bounded_queue** 

    Please see the Bounded Queue section below.

* **state**
  * State shows what the current replication strategy is currently processing. 
  
  * The following definitions appear in the status output if keylist strategy is being used. They can be used by Basho support to identify replication issues.    * **wait_for_partition**
    * **build_keylist**
    * **wait_keylist**    
    * **diff_bloom**
    * **diff_keylist**
    

* **message_queue_len** 

  The number of Erlang messages that are waiting to be process by the server.
  

## Keylist Strategy


* **keylist_server** and **keylist_client**

  * **fullsync**
    On the client, the number of partitions that remain to be processed.
    On the server, the partition currently being processed by full-synchronization replication.
    
  * **partition_start**
  
    The number of elapsed seconds since replication has started on a given partition.
    
  * **stage_start**

    The number of elapsed seconds since replication has started on a given stage.
    
  * **get_pool_size**
  
    The number of Riak GET finite state workers available to process requests.
    

## Bounded Queue

   * The bounded queue is responsible for holding objects that are waiting to participate in real-time replication. Please see the [[Riak EE MDC Replication Configuration|Multi-Data-Center Replication Configuration]] guide for more information. 
   * Please note that these values are only available in Riak EE 1.2.1+.
   
  - **queue_pid** 
  
    The Erlang process ID of the bounded queue.
  
  - **dropped_count** 
  
    The number of objects that failed to be enqueued in the bounded_queue due to the queue being full. *These objects will be replicated during the next full synchronization.*

  - **queue_length** 
  
    The number of Riak objects currently in the bounded queue.

  - **queue_byte_size**
    
    The size of all objects currently in the queue.
  
  - **queue_max_size**
  
    The number of bytes the queue can hold before objects are dropped.  *These objects will be replicated during the next full synchronization.*

  - **queue_percentage**

  The percentage of the queue that is full.
    
  - **queue_pending** 
  
     The current count of 'in flight' objects we've sent that the client has not acknowledged.
            
  - **queue_max_pending**
    The maximum number of objects that can be 'in flight' before we refuse to send any more.


### Accessing Replication web-based statistics

These stats can be accessed via the command line with the following command:

	curl -q http://127.0.0.1:8091/riak-repl/stats

A simple way to view formatted statistics is to use a command such as:
	
	curl -q http://127.0.0.1:8091/riak-repl/stats | python -mjson.tool
