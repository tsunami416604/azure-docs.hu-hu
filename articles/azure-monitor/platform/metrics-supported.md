---
title: Azure Monitor supported metrics by resource type
description: List of metrics available for each resource type with Azure Monitor.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 05/20/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 30e2b880f32f896098778942deb67d7ced9f5c2d
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484215"
---
# <a name="supported-metrics-with-azure-monitor"></a>Supported metrics with Azure Monitor

Azure Monitor provides several ways to interact with metrics, including charting them in the portal, accessing them through the REST API, or querying them using PowerShell or CLI. Below is a complete list of all metrics currently available with Azure Monitor's metric pipeline. Other metrics may be available in the portal or using legacy APIs. This list below only includes metrics available using the consolidated Azure Monitor metric pipeline. To query for and access these metrics please use the [2018-01-01 api-version](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)

> [!NOTE]
> A többdimenziós metrikák diagnosztikai beállításokon keresztül történő küldése jelenleg nem támogatott. A dimenziókkal rendelkező metrikák egybesimított, egydimenziós metrikákként vannak exportálva, összesített dimenzióértékekkel.
>
> *Például*: Egy eseményközpont „Bejövő üzenetek” metrikája üzenetsoronként deríthető fel és ábrázolható. Ha azonban diagnosztikai beállításokon keresztül van exportálva, a metrika az eseményközpontban lévő összes üzenetsor összes bejövő üzeneteként lesz ábrázolva.
>
>

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|qpu_metric|QPU|Mennyiség|Átlag|QPU. Range 0-100 for S1, 0-200 for S2 and 0-400 for S4|ServerResourceType|
|memory_metric|Memória|Bytes|Átlag|Memory. Range 0-25 GB for S1, 0-50 GB for S2 and 0-100 GB for S4|ServerResourceType|
|private_bytes_metric|Private Bytes |Bytes|Átlag|The total amount of memory the Analysis Services engine process and Mashup container processes have allocated, not including memory shared with other processes.|ServerResourceType|
|virtual_bytes_metric|Virtual Bytes |Bytes|Átlag|The current size of the virtual address space that Analysis Services engine process and Mashup container processes are using.|ServerResourceType|
|TotalConnectionRequests|Total Connection Requests|Mennyiség|Átlag|Total connection requests. These are arrivals.|ServerResourceType|
|SuccessfullConnectionsPerSec|Successful Connections Per Sec|CountPerSecond|Átlag|Rate of successful connection completions.|ServerResourceType|
|TotalConnectionFailures|Total Connection Failures|Mennyiség|Átlag|Total failed connection attempts.|ServerResourceType|
|CurrentUserSessions|Current User Sessions|Mennyiség|Átlag|Current number of user sessions established.|ServerResourceType|
|QueryPoolBusyThreads|Query Pool Busy Threads|Mennyiség|Átlag|Number of busy threads in the query thread pool.|ServerResourceType|
|CommandPoolJobQueueLength|Command Pool Job Queue Length|Mennyiség|Átlag|Number of jobs in the queue of the command thread pool.|ServerResourceType|
|ProcessingPoolJobQueueLength|Processing Pool Job Queue Length|Mennyiség|Átlag|Number of non-I/O jobs in the queue of the processing thread pool.|ServerResourceType|
|CurrentConnections|Connection: Current connections|Mennyiség|Átlag|Current number of client connections established.|ServerResourceType|
|CleanerCurrentPrice|Memory: Cleaner Current Price|Mennyiség|Átlag|Current price of memory, $/byte/time, normalized to 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Memory: Cleaner Memory shrinkable|Bytes|Átlag|Amount of memory, in bytes, subject to purging by the background cleaner.|ServerResourceType|
|CleanerMemoryNonshrinkable|Memory: Cleaner Memory nonshrinkable|Bytes|Átlag|Amount of memory, in bytes, not subject to purging by the background cleaner.|ServerResourceType|
|MemoryUsage|Memory: Memory Usage|Bytes|Átlag|Memory usage of the server process as used in calculating cleaner memory price. Equal to counter Process\PrivateBytes plus the size of memory-mapped data, ignoring any memory which was mapped or allocated by the xVelocity in-memory analytics engine (VertiPaq) in excess of the xVelocity engine Memory Limit.|ServerResourceType|
|MemoryLimitHard|Memory: Memory Limit Hard|Bytes|Átlag|Hard memory limit, from configuration file.|ServerResourceType|
|MemoryLimitHigh|Memory: Memory Limit High|Bytes|Átlag|High memory limit, from configuration file.|ServerResourceType|
|MemoryLimitLow|Memory: Memory Limit Low|Bytes|Átlag|Low memory limit, from configuration file.|ServerResourceType|
|MemoryLimitVertiPaq|Memory: Memory Limit VertiPaq|Bytes|Átlag|In-memory limit, from configuration file.|ServerResourceType|
|Kvóta|Memory: Quota|Bytes|Átlag|Current memory quota, in bytes. Memory quota is also known as a memory grant or memory reservation.|ServerResourceType|
|QuotaBlocked|Memory: Quota Blocked|Mennyiség|Átlag|Current number of quota requests that are blocked until other memory quotas are freed.|ServerResourceType|
|VertiPaqNonpaged|Memory: VertiPaq Nonpaged|Bytes|Átlag|Bytes of memory locked in the working set for use by the in-memory engine.|ServerResourceType|
|VertiPaqPaged|Memory: VertiPaq Paged|Bytes|Átlag|Bytes of paged memory in use for in-memory data.|ServerResourceType|
|RowsReadPerSec|Processing: Rows read per sec|CountPerSecond|Átlag|Rate of rows read from all relational databases.|ServerResourceType|
|RowsConvertedPerSec|Processing: Rows converted per sec|CountPerSecond|Átlag|Rate of rows converted during processing.|ServerResourceType|
|RowsWrittenPerSec|Processing: Rows written per sec|CountPerSecond|Átlag|Rate of rows written during processing.|ServerResourceType|
|CommandPoolBusyThreads|Threads: Command pool busy threads|Mennyiség|Átlag|Number of busy threads in the command thread pool.|ServerResourceType|
|CommandPoolIdleThreads|Threads: Command pool idle threads|Mennyiség|Átlag|Number of idle threads in the command thread pool.|ServerResourceType|
|LongParsingBusyThreads|Threads: Long parsing busy threads|Mennyiség|Átlag|Number of busy threads in the long parsing thread pool.|ServerResourceType|
|LongParsingIdleThreads|Threads: Long parsing idle threads|Mennyiség|Átlag|Number of idle threads in the long parsing thread pool.|ServerResourceType|
|LongParsingJobQueueLength|Threads: Long parsing job queue length|Mennyiség|Átlag|Number of jobs in the queue of the long parsing thread pool.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Threads: Processing pool busy I/O job threads|Mennyiség|Átlag|Number of threads running I/O jobs in the processing thread pool.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Threads: Processing pool busy non-I/O threads|Mennyiség|Átlag|Number of threads running non-I/O jobs in the processing thread pool.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Threads: Processing pool I/O job queue length|Mennyiség|Átlag|Number of I/O jobs in the queue of the processing thread pool.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Threads: Processing pool idle I/O job threads|Mennyiség|Átlag|Number of idle threads for I/O jobs in the processing thread pool.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Threads: Processing pool idle non-I/O threads|Mennyiség|Átlag|Number of idle threads in the processing thread pool dedicated to non-I/O jobs.|ServerResourceType|
|QueryPoolIdleThreads|Threads: Query pool idle threads|Mennyiség|Átlag|Number of idle threads for I/O jobs in the processing thread pool.|ServerResourceType|
|QueryPoolJobQueueLength|Threads: Query pool job queue length|Mennyiség|Átlag|Number of jobs in the queue of the query thread pool.|ServerResourceType|
|ShortParsingBusyThreads|Threads: Short parsing busy threads|Mennyiség|Átlag|Number of busy threads in the short parsing thread pool.|ServerResourceType|
|ShortParsingIdleThreads|Threads: Short parsing idle threads|Mennyiség|Átlag|Number of idle threads in the short parsing thread pool.|ServerResourceType|
|ShortParsingJobQueueLength|Threads: Short parsing job queue length|Mennyiség|Átlag|Number of jobs in the queue of the short parsing thread pool.|ServerResourceType|
|memory_thrashing_metric|Memory Thrashing|Százalék|Átlag|Average memory thrashing.|ServerResourceType|
|mashup_engine_qpu_metric|M Engine QPU|Mennyiség|Átlag|QPU usage by mashup engine processes|ServerResourceType|
|mashup_engine_memory_metric|M Engine Memory|Bytes|Átlag|Memory usage by mashup engine processes|ServerResourceType|
|mashup_engine_private_bytes_metric|M Engine Private Bytes |Bytes|Átlag|The total amount of memory Mashup container processes have allocated, not including memory shared with other processes.|ServerResourceType|
|mashup_engine_virtual_bytes_metric|M Engine Virtual Bytes |Bytes|Átlag|The current size of the virtual address space Mashup container processes are using.|ServerResourceType|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Kérelmek|Kérelmek|Mennyiség|Összes|The total number of gateway requests in a given period. It can be sliced by various dimensions to help you diagnose issues. |Location, BackendResponseCode, LastErrorReason, GatewayResponseCode|
|TotalRequests|Total Gateway Requests|Mennyiség|Összes|The total number of gateway requests in a given period. This metric has been deprecated, we recommend using the new `Requests` metric. |Location, Hostname|
|SuccessfulRequests|Successful Gateway Requests|Mennyiség|Összes|The total number of successful gateway requests in a given period. This metric has been deprecated, we recommend using the new `Requests` metric.|Location, Hostname|
|UnauthorizedRequests|Unauthorized Gateway Requests|Mennyiség|Összes| The total number of unauthorized gateway requests in a given period. This metric has been deprecated, we recommend using the new `Requests` metric.|Location, Hostname|
|FailedRequests|Failed Gateway Requests|Mennyiség|Összes|The total number of failed gateway requests in a given period. This metric has been deprecated, we recommend using the new `Requests` metric.|Location, Hostname|
|OtherRequests|Other Gateway Requests|Mennyiség|Összes|The total number of gateway requests in a given period that do not fall into the successful, unauthorized, or failed categories. This metric has been deprecated, we recommend using the new `Requests` metric. |Location, Hostname|
|Időtartam|Overall Duration of Gateway Requests|Milliseconds|Átlag|The time between when API Management receives a request from a client and when it returns a response to the client.|Location, Hostname|
|Kapacitás|Kapacitás|Százalék|Átlag|Indicator of load on an API Management instance for making informed decisions whether to scale the instance to accommodate more load.|Földrajzi egység|
|EventHubTotalEvents|Total EventHub Events|Mennyiség|Összes|The total number of events sent to EventHub from API Management in a given period.|Földrajzi egység|
|EventHubSuccessfulEvents|Successful EventHub Events|Mennyiség|Összes|The total number of successful EventHub events in a given period.|Földrajzi egység|
|EventHubTotalFailedEvents|Failed EventHub Events|Mennyiség|Összes|The total number of failed EventHub events in a given period.|Földrajzi egység|
|EventHubRejectedEvents|Rejected EventHub Events|Mennyiség|Összes|The total number of rejected EventHub events (wrong configuration or unauthorized) in a given period.|Földrajzi egység|
|EventHubThrottledEvents|Throttled EventHub Events|Mennyiség|Összes|The total number of throttled EventHub events in a given period.|Földrajzi egység|
|EventHubTimedoutEvents|Timed Out EventHub Events|Mennyiség|Összes|The total number of timed out EventHub events in a given period.|Földrajzi egység|
|EventHubDroppedEvents|Dropped EventHub Events|Mennyiség|Összes|The total number of events skipped because of queue size limit reached in a given period.|Földrajzi egység|
|EventHubTotalBytesSent|Size of EventHub Events|Bytes|Összes|The total size of EventHub events in bytes in a given period.|Földrajzi egység|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TotalJob|Total Jobs|Mennyiség|Összes|The total number of jobs|Runbook, Status|
|TotalUpdateDeploymentRuns|Total Update Deployment Runs|Mennyiség|Összes|Total software update deployment runs|SoftwareUpdateConfigurationName, Status|
|TotalUpdateDeploymentMachineRuns|Total Update Deployment Machine Runs|Mennyiség|Összes|Total software update deployment machine runs in a software update deployment run|SoftwareUpdateConfigurationName, Status, TargetComputer, SoftwareUpdateConfigurationRunId|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CoreCount|Dedicated Core Count|Mennyiség|Összes|Total number of dedicated cores in the batch account|No Dimensions|
|TotalNodeCount|Dedicated Node Count|Mennyiség|Összes|Total number of dedicated nodes in the batch account|No Dimensions|
|LowPriorityCoreCount|LowPriority Core Count|Mennyiség|Összes|Total number of low-priority cores in the batch account|No Dimensions|
|TotalLowPriorityNodeCount|Low-Priority Node Count|Mennyiség|Összes|Total number of low-priority nodes in the batch account|No Dimensions|
|CreatingNodeCount|Creating Node Count|Mennyiség|Összes|Number of nodes being created|No Dimensions|
|StartingNodeCount|Starting Node Count|Mennyiség|Összes|Number of nodes starting|No Dimensions|
|WaitingForStartTaskNodeCount|Waiting For Start Task Node Count|Mennyiség|Összes|Number of nodes waiting for the Start Task to complete|No Dimensions|
|StartTaskFailedNodeCount|Start Task Failed Node Count|Mennyiség|Összes|Number of nodes where the Start Task has failed|No Dimensions|
|IdleNodeCount|Idle Node Count|Mennyiség|Összes|Number of idle nodes|No Dimensions|
|OfflineNodeCount|Offline Node Count|Mennyiség|Összes|Number of offline nodes|No Dimensions|
|RebootingNodeCount|Rebooting Node Count|Mennyiség|Összes|Number of rebooting nodes|No Dimensions|
|ReimagingNodeCount|Reimaging Node Count|Mennyiség|Összes|Number of reimaging nodes|No Dimensions|
|RunningNodeCount|Running Node Count|Mennyiség|Összes|Number of running nodes|No Dimensions|
|LeavingPoolNodeCount|Leaving Pool Node Count|Mennyiség|Összes|Number of nodes leaving the Pool|No Dimensions|
|UnusableNodeCount|Unusable Node Count|Mennyiség|Összes|Number of unusable nodes|No Dimensions|
|PreemptedNodeCount|Preempted Node Count|Mennyiség|Összes|Number of preempted nodes|No Dimensions|
|TaskStartEvent|Task Start Events|Mennyiség|Összes|Total number of tasks that have started|No Dimensions|
|TaskCompleteEvent|Task Complete Events|Mennyiség|Összes|Total number of tasks that have completed|No Dimensions|
|TaskFailEvent|Task Fail Events|Mennyiség|Összes|Total number of tasks that have completed in a failed state|No Dimensions|
|PoolCreateEvent|Pool Create Events|Mennyiség|Összes|Total number of pools that have been created|No Dimensions|
|PoolResizeStartEvent|Pool Resize Start Events|Mennyiség|Összes|Total number of pool resizes that have started|No Dimensions|
|PoolResizeCompleteEvent|Pool Resize Complete Events|Mennyiség|Összes|Total number of pool resizes that have completed|No Dimensions|
|PoolDeleteStartEvent|Pool Delete Start Events|Mennyiség|Összes|Total number of pool deletes that have started|No Dimensions|
|PoolDeleteCompleteEvent|Pool Delete Complete Events|Mennyiség|Összes|Total number of pool deletes that have completed|No Dimensions|
|JobDeleteCompleteEvent|Job Delete Complete Events|Mennyiség|Összes|Total number of jobs that have been successfully deleted.|No Dimensions|
|JobDeleteStartEvent|Job Delete Start Events|Mennyiség|Összes|Total number of jobs that have been requested to be deleted.|No Dimensions|
|JobDisableCompleteEvent|Job Disable Complete Events|Mennyiség|Összes|Total number of jobs that have been successfully disabled.|No Dimensions|
|JobDisableStartEvent|Job Disable Start Events|Mennyiség|Összes|Total number of jobs that have been requested to be disabled.|No Dimensions|
|JobStartEvent|Job Start Events|Mennyiség|Összes|Total number of jobs that have been successfully started.|No Dimensions|
|JobTerminateCompleteEvent|Job Terminate Complete Events|Mennyiség|Összes|Total number of jobs that have been successfully terminated.|No Dimensions|
|JobTerminateStartEvent|Job Terminate Start Events|Mennyiség|Összes|Total number of jobs that have been requested to be terminated.|No Dimensions|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|connectedclients|Connected Clients|Mennyiség|Maximum||ShardId|
|totalcommandsprocessed|Total Operations|Mennyiség|Összes||ShardId|
|cachehits|Cache Hits|Mennyiség|Összes||ShardId|
|cachemisses|Cache Misses|Mennyiség|Összes||ShardId|
|getcommands|Gets|Mennyiség|Összes||ShardId|
|setcommands|Sets|Mennyiség|Összes||ShardId|
|operationsPerSecond|Operations Per Second|Mennyiség|Maximum||ShardId|
|evictedkeys|Evicted Keys|Mennyiség|Összes||ShardId|
|totalkeys|Total Keys|Mennyiség|Maximum||ShardId|
|expiredkeys|Expired Keys|Mennyiség|Összes||ShardId|
|usedmemory|Used Memory|Bytes|Maximum||ShardId|
|usedmemorypercentage|Used Memory Percentage|Százalék|Maximum||ShardId|
|usedmemoryRss|Used Memory RSS|Bytes|Maximum||ShardId|
|serverLoad|Server Load|Százalék|Maximum||ShardId|
|cacheWrite|Cache Write|BytesPerSecond|Maximum||ShardId|
|cacheRead|Cache Read|BytesPerSecond|Maximum||ShardId|
|percentProcessorTime|CPU|Százalék|Maximum||ShardId|
|cacheLatency|Cache Latency Microseconds (Preview)|Mennyiség|Átlag||ShardId, SampleType|
|Hibák|Hibák|Mennyiség|Maximum||ShardId, ErrorType|
|connectedclients0|Connected Clients (Shard 0)|Mennyiség|Maximum||No Dimensions|
|totalcommandsprocessed0|Total Operations (Shard 0)|Mennyiség|Összes||No Dimensions|
|cachehits0|Cache Hits (Shard 0)|Mennyiség|Összes||No Dimensions|
|cachemisses0|Cache Misses (Shard 0)|Mennyiség|Összes||No Dimensions|
|getcommands0|Gets (Shard 0)|Mennyiség|Összes||No Dimensions|
|setcommands0|Sets (Shard 0)|Mennyiség|Összes||No Dimensions|
|operationsPerSecond0|Operations Per Second (Shard 0)|Mennyiség|Maximum||No Dimensions|
|evictedkeys0|Evicted Keys (Shard 0)|Mennyiség|Összes||No Dimensions|
|totalkeys0|Total Keys (Shard 0)|Mennyiség|Maximum||No Dimensions|
|expiredkeys0|Expired Keys (Shard 0)|Mennyiség|Összes||No Dimensions|
|usedmemory0|Used Memory (Shard 0)|Bytes|Maximum||No Dimensions|
|usedmemoryRss0|Used Memory RSS (Shard 0)|Bytes|Maximum||No Dimensions|
|serverLoad0|Server Load (Shard 0)|Százalék|Maximum||No Dimensions|
|cacheWrite0|Cache Write (Shard 0)|BytesPerSecond|Maximum||No Dimensions|
|cacheRead0|Cache Read (Shard 0)|BytesPerSecond|Maximum||No Dimensions|
|percentProcessorTime0|CPU (Shard 0)|Százalék|Maximum||No Dimensions|
|connectedclients1|Connected Clients (Shard 1)|Mennyiség|Maximum||No Dimensions|
|totalcommandsprocessed1|Total Operations (Shard 1)|Mennyiség|Összes||No Dimensions|
|cachehits1|Cache Hits (Shard 1)|Mennyiség|Összes||No Dimensions|
|cachemisses1|Cache Misses (Shard 1)|Mennyiség|Összes||No Dimensions|
|getcommands1|Gets (Shard 1)|Mennyiség|Összes||No Dimensions|
|setcommands1|Sets (Shard 1)|Mennyiség|Összes||No Dimensions|
|operationsPerSecond1|Operations Per Second (Shard 1)|Mennyiség|Maximum||No Dimensions|
|evictedkeys1|Evicted Keys (Shard 1)|Mennyiség|Összes||No Dimensions|
|totalkeys1|Total Keys (Shard 1)|Mennyiség|Maximum||No Dimensions|
|expiredkeys1|Expired Keys (Shard 1)|Mennyiség|Összes||No Dimensions|
|usedmemory1|Used Memory (Shard 1)|Bytes|Maximum||No Dimensions|
|usedmemoryRss1|Used Memory RSS (Shard 1)|Bytes|Maximum||No Dimensions|
|serverLoad1|Server Load (Shard 1)|Százalék|Maximum||No Dimensions|
|cacheWrite1|Cache Write (Shard 1)|BytesPerSecond|Maximum||No Dimensions|
|cacheRead1|Cache Read (Shard 1)|BytesPerSecond|Maximum||No Dimensions|
|percentProcessorTime1|CPU (Shard 1)|Százalék|Maximum||No Dimensions|
|connectedclients2|Connected Clients (Shard 2)|Mennyiség|Maximum||No Dimensions|
|totalcommandsprocessed2|Total Operations (Shard 2)|Mennyiség|Összes||No Dimensions|
|cachehits2|Cache Hits (Shard 2)|Mennyiség|Összes||No Dimensions|
|cachemisses2|Cache Misses (Shard 2)|Mennyiség|Összes||No Dimensions|
|getcommands2|Gets (Shard 2)|Mennyiség|Összes||No Dimensions|
|setcommands2|Sets (Shard 2)|Mennyiség|Összes||No Dimensions|
|operationsPerSecond2|Operations Per Second (Shard 2)|Mennyiség|Maximum||No Dimensions|
|evictedkeys2|Evicted Keys (Shard 2)|Mennyiség|Összes||No Dimensions|
|totalkeys2|Total Keys (Shard 2)|Mennyiség|Maximum||No Dimensions|
|expiredkeys2|Expired Keys (Shard 2)|Mennyiség|Összes||No Dimensions|
|usedmemory2|Used Memory (Shard 2)|Bytes|Maximum||No Dimensions|
|usedmemoryRss2|Used Memory RSS (Shard 2)|Bytes|Maximum||No Dimensions|
|serverLoad2|Server Load (Shard 2)|Százalék|Maximum||No Dimensions|
|cacheWrite2|Cache Write (Shard 2)|BytesPerSecond|Maximum||No Dimensions|
|cacheRead2|Cache Read (Shard 2)|BytesPerSecond|Maximum||No Dimensions|
|percentProcessorTime2|CPU (Shard 2)|Százalék|Maximum||No Dimensions|
|connectedclients3|Connected Clients (Shard 3)|Mennyiség|Maximum||No Dimensions|
|totalcommandsprocessed3|Total Operations (Shard 3)|Mennyiség|Összes||No Dimensions|
|cachehits3|Cache Hits (Shard 3)|Mennyiség|Összes||No Dimensions|
|cachemisses3|Cache Misses (Shard 3)|Mennyiség|Összes||No Dimensions|
|getcommands3|Gets (Shard 3)|Mennyiség|Összes||No Dimensions|
|setcommands3|Sets (Shard 3)|Mennyiség|Összes||No Dimensions|
|operationsPerSecond3|Operations Per Second (Shard 3)|Mennyiség|Maximum||No Dimensions|
|evictedkeys3|Evicted Keys (Shard 3)|Mennyiség|Összes||No Dimensions|
|totalkeys3|Total Keys (Shard 3)|Mennyiség|Maximum||No Dimensions|
|expiredkeys3|Expired Keys (Shard 3)|Mennyiség|Összes||No Dimensions|
|usedmemory3|Used Memory (Shard 3)|Bytes|Maximum||No Dimensions|
|usedmemoryRss3|Used Memory RSS (Shard 3)|Bytes|Maximum||No Dimensions|
|serverLoad3|Server Load (Shard 3)|Százalék|Maximum||No Dimensions|
|cacheWrite3|Cache Write (Shard 3)|BytesPerSecond|Maximum||No Dimensions|
|cacheRead3|Cache Read (Shard 3)|BytesPerSecond|Maximum||No Dimensions|
|percentProcessorTime3|CPU (Shard 3)|Százalék|Maximum||No Dimensions|
|connectedclients4|Connected Clients (Shard 4)|Mennyiség|Maximum||No Dimensions|
|totalcommandsprocessed4|Total Operations (Shard 4)|Mennyiség|Összes||No Dimensions|
|cachehits4|Cache Hits (Shard 4)|Mennyiség|Összes||No Dimensions|
|cachemisses4|Cache Misses (Shard 4)|Mennyiség|Összes||No Dimensions|
|getcommands4|Gets (Shard 4)|Mennyiség|Összes||No Dimensions|
|setcommands4|Sets (Shard 4)|Mennyiség|Összes||No Dimensions|
|operationsPerSecond4|Operations Per Second (Shard 4)|Mennyiség|Maximum||No Dimensions|
|evictedkeys4|Evicted Keys (Shard 4)|Mennyiség|Összes||No Dimensions|
|totalkeys4|Total Keys (Shard 4)|Mennyiség|Maximum||No Dimensions|
|expiredkeys4|Expired Keys (Shard 4)|Mennyiség|Összes||No Dimensions|
|usedmemory4|Used Memory (Shard 4)|Bytes|Maximum||No Dimensions|
|usedmemoryRss4|Used Memory RSS (Shard 4)|Bytes|Maximum||No Dimensions|
|serverLoad4|Server Load (Shard 4)|Százalék|Maximum||No Dimensions|
|cacheWrite4|Cache Write (Shard 4)|BytesPerSecond|Maximum||No Dimensions|
|cacheRead4|Cache Read (Shard 4)|BytesPerSecond|Maximum||No Dimensions|
|percentProcessorTime4|CPU (Shard 4)|Százalék|Maximum||No Dimensions|
|connectedclients5|Connected Clients (Shard 5)|Mennyiség|Maximum||No Dimensions|
|totalcommandsprocessed5|Total Operations (Shard 5)|Mennyiség|Összes||No Dimensions|
|cachehits5|Cache Hits (Shard 5)|Mennyiség|Összes||No Dimensions|
|cachemisses5|Cache Misses (Shard 5)|Mennyiség|Összes||No Dimensions|
|getcommands5|Gets (Shard 5)|Mennyiség|Összes||No Dimensions|
|setcommands5|Sets (Shard 5)|Mennyiség|Összes||No Dimensions|
|operationsPerSecond5|Operations Per Second (Shard 5)|Mennyiség|Maximum||No Dimensions|
|evictedkeys5|Evicted Keys (Shard 5)|Mennyiség|Összes||No Dimensions|
|totalkeys5|Total Keys (Shard 5)|Mennyiség|Maximum||No Dimensions|
|expiredkeys5|Expired Keys (Shard 5)|Mennyiség|Összes||No Dimensions|
|usedmemory5|Used Memory (Shard 5)|Bytes|Maximum||No Dimensions|
|usedmemoryRss5|Used Memory RSS (Shard 5)|Bytes|Maximum||No Dimensions|
|serverLoad5|Server Load (Shard 5)|Százalék|Maximum||No Dimensions|
|cacheWrite5|Cache Write (Shard 5)|BytesPerSecond|Maximum||No Dimensions|
|cacheRead5|Cache Read (Shard 5)|BytesPerSecond|Maximum||No Dimensions|
|percentProcessorTime5|CPU (Shard 5)|Százalék|Maximum||No Dimensions|
|connectedclients6|Connected Clients (Shard 6)|Mennyiség|Maximum||No Dimensions|
|totalcommandsprocessed6|Total Operations (Shard 6)|Mennyiség|Összes||No Dimensions|
|cachehits6|Cache Hits (Shard 6)|Mennyiség|Összes||No Dimensions|
|cachemisses6|Cache Misses (Shard 6)|Mennyiség|Összes||No Dimensions|
|getcommands6|Gets (Shard 6)|Mennyiség|Összes||No Dimensions|
|setcommands6|Sets (Shard 6)|Mennyiség|Összes||No Dimensions|
|operationsPerSecond6|Operations Per Second (Shard 6)|Mennyiség|Maximum||No Dimensions|
|evictedkeys6|Evicted Keys (Shard 6)|Mennyiség|Összes||No Dimensions|
|totalkeys6|Total Keys (Shard 6)|Mennyiség|Maximum||No Dimensions|
|expiredkeys6|Expired Keys (Shard 6)|Mennyiség|Összes||No Dimensions|
|usedmemory6|Used Memory (Shard 6)|Bytes|Maximum||No Dimensions|
|usedmemoryRss6|Used Memory RSS (Shard 6)|Bytes|Maximum||No Dimensions|
|serverLoad6|Server Load (Shard 6)|Százalék|Maximum||No Dimensions|
|cacheWrite6|Cache Write (Shard 6)|BytesPerSecond|Maximum||No Dimensions|
|cacheRead6|Cache Read (Shard 6)|BytesPerSecond|Maximum||No Dimensions|
|percentProcessorTime6|CPU (Shard 6)|Százalék|Maximum||No Dimensions|
|connectedclients7|Connected Clients (Shard 7)|Mennyiség|Maximum||No Dimensions|
|totalcommandsprocessed7|Total Operations (Shard 7)|Mennyiség|Összes||No Dimensions|
|cachehits7|Cache Hits (Shard 7)|Mennyiség|Összes||No Dimensions|
|cachemisses7|Cache Misses (Shard 7)|Mennyiség|Összes||No Dimensions|
|getcommands7|Gets (Shard 7)|Mennyiség|Összes||No Dimensions|
|setcommands7|Sets (Shard 7)|Mennyiség|Összes||No Dimensions|
|operationsPerSecond7|Operations Per Second (Shard 7)|Mennyiség|Maximum||No Dimensions|
|evictedkeys7|Evicted Keys (Shard 7)|Mennyiség|Összes||No Dimensions|
|totalkeys7|Total Keys (Shard 7)|Mennyiség|Maximum||No Dimensions|
|expiredkeys7|Expired Keys (Shard 7)|Mennyiség|Összes||No Dimensions|
|usedmemory7|Used Memory (Shard 7)|Bytes|Maximum||No Dimensions|
|usedmemoryRss7|Used Memory RSS (Shard 7)|Bytes|Maximum||No Dimensions|
|serverLoad7|Server Load (Shard 7)|Százalék|Maximum||No Dimensions|
|cacheWrite7|Cache Write (Shard 7)|BytesPerSecond|Maximum||No Dimensions|
|cacheRead7|Cache Read (Shard 7)|BytesPerSecond|Maximum||No Dimensions|
|percentProcessorTime7|CPU (Shard 7)|Százalék|Maximum||No Dimensions|
|connectedclients8|Connected Clients (Shard 8)|Mennyiség|Maximum||No Dimensions|
|totalcommandsprocessed8|Total Operations (Shard 8)|Mennyiség|Összes||No Dimensions|
|cachehits8|Cache Hits (Shard 8)|Mennyiség|Összes||No Dimensions|
|cachemisses8|Cache Misses (Shard 8)|Mennyiség|Összes||No Dimensions|
|getcommands8|Gets (Shard 8)|Mennyiség|Összes||No Dimensions|
|setcommands8|Sets (Shard 8)|Mennyiség|Összes||No Dimensions|
|operationsPerSecond8|Operations Per Second (Shard 8)|Mennyiség|Maximum||No Dimensions|
|evictedkeys8|Evicted Keys (Shard 8)|Mennyiség|Összes||No Dimensions|
|totalkeys8|Total Keys (Shard 8)|Mennyiség|Maximum||No Dimensions|
|expiredkeys8|Expired Keys (Shard 8)|Mennyiség|Összes||No Dimensions|
|usedmemory8|Used Memory (Shard 8)|Bytes|Maximum||No Dimensions|
|usedmemoryRss8|Used Memory RSS (Shard 8)|Bytes|Maximum||No Dimensions|
|serverLoad8|Server Load (Shard 8)|Százalék|Maximum||No Dimensions|
|cacheWrite8|Cache Write (Shard 8)|BytesPerSecond|Maximum||No Dimensions|
|cacheRead8|Cache Read (Shard 8)|BytesPerSecond|Maximum||No Dimensions|
|percentProcessorTime8|CPU (Shard 8)|Százalék|Maximum||No Dimensions|
|connectedclients9|Connected Clients (Shard 9)|Mennyiség|Maximum||No Dimensions|
|totalcommandsprocessed9|Total Operations (Shard 9)|Mennyiség|Összes||No Dimensions|
|cachehits9|Cache Hits (Shard 9)|Mennyiség|Összes||No Dimensions|
|cachemisses9|Cache Misses (Shard 9)|Mennyiség|Összes||No Dimensions|
|getcommands9|Gets (Shard 9)|Mennyiség|Összes||No Dimensions|
|setcommands9|Sets (Shard 9)|Mennyiség|Összes||No Dimensions|
|operationsPerSecond9|Operations Per Second (Shard 9)|Mennyiség|Maximum||No Dimensions|
|evictedkeys9|Evicted Keys (Shard 9)|Mennyiség|Összes||No Dimensions|
|totalkeys9|Total Keys (Shard 9)|Mennyiség|Maximum||No Dimensions|
|expiredkeys9|Expired Keys (Shard 9)|Mennyiség|Összes||No Dimensions|
|usedmemory9|Used Memory (Shard 9)|Bytes|Maximum||No Dimensions|
|usedmemoryRss9|Used Memory RSS (Shard 9)|Bytes|Maximum||No Dimensions|
|serverLoad9|Server Load (Shard 9)|Százalék|Maximum||No Dimensions|
|cacheWrite9|Cache Write (Shard 9)|BytesPerSecond|Maximum||No Dimensions|
|cacheRead9|Cache Read (Shard 9)|BytesPerSecond|Maximum||No Dimensions|
|percentProcessorTime9|CPU (Shard 9)|Százalék|Maximum||No Dimensions|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Százalékos processzorhasználat|Százalékos processzorhasználat|Százalék|Átlag|The percentage of allocated compute units that are currently in use by the Virtual Machine(s).|No Dimensions|
|Bejövő hálózat|Bejövő hálózat|Bytes|Összes|The number of bytes received on all network interfaces by the Virtual Machine(s) (Incoming Traffic).|No Dimensions|
|Kimenő hálózat|Kimenő hálózat|Bytes|Összes|The number of bytes out on all network interfaces by the Virtual Machine(s) (Outgoing Traffic).|No Dimensions|
|Disk Read Bytes/Sec|Disk Read|BytesPerSecond|Átlag|Average bytes read from disk during monitoring period.|No Dimensions|
|Disk Write Bytes/Sec|Disk Write|BytesPerSecond|Átlag|Average bytes written to disk during monitoring period.|No Dimensions|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Átlag|Disk Read IOPS.|No Dimensions|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Átlag|Disk Write IOPS.|No Dimensions|

## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Százalékos processzorhasználat|Százalékos processzorhasználat|Százalék|Átlag|The percentage of allocated compute units that are currently in use by the Virtual Machine(s).|RoleInstanceId|
|Bejövő hálózat|Bejövő hálózat|Bytes|Összes|The number of bytes received on all network interfaces by the Virtual Machine(s) (Incoming Traffic).|RoleInstanceId|
|Kimenő hálózat|Kimenő hálózat|Bytes|Összes|The number of bytes out on all network interfaces by the Virtual Machine(s) (Outgoing Traffic).|RoleInstanceId|
|Disk Read Bytes/Sec|Disk Read|BytesPerSecond|Átlag|Average bytes read from disk during monitoring period.|RoleInstanceId|
|Disk Write Bytes/Sec|Disk Write|BytesPerSecond|Átlag|Average bytes written to disk during monitoring period.|RoleInstanceId|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Átlag|Disk Read IOPS.|RoleInstanceId|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Átlag|Disk Write IOPS.|RoleInstanceId|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TotalCalls|Total Calls|Mennyiség|Összes|Total number of calls.|ApiName, OperationName, Region|
|SuccessfulCalls|Successful Calls|Mennyiség|Összes|Number of successful calls.|ApiName, OperationName, Region|
|TotalErrors|Total Errors|Mennyiség|Összes|Total number of calls with error response (HTTP response code 4xx or 5xx).|ApiName, OperationName, Region|
|BlockedCalls|Blocked Calls|Mennyiség|Összes|Number of calls that exceeded rate or quota limit.|ApiName, OperationName, Region|
|ServerErrors|Server Errors|Mennyiség|Összes|Number of calls with service internal error (HTTP response code 5xx).|ApiName, OperationName, Region|
|ClientErrors|Client Errors|Mennyiség|Összes|Number of calls with client side error (HTTP response code 4xx).|ApiName, OperationName, Region|
|DataIn|Data In|Bytes|Összes|Size of incoming data in bytes.|ApiName, OperationName, Region|
|DataOut|Data Out|Bytes|Összes|Size of outgoing data in bytes.|ApiName, OperationName, Region|
|Késés|Késés|MilliSeconds|Átlag|Latency in milliseconds.|ApiName, OperationName, Region|
|CharactersTranslated|Characters Translated|Mennyiség|Összes|Total number of characters in incoming text request.|ApiName, OperationName, Region|
|CharactersTrained|Characters Trained|Mennyiség|Összes|Total number of characters trained.|ApiName, OperationName, Region|
|SpeechSessionDuration|Speech Session Duration|másodperc|Összes|Total duration of speech session in seconds.|ApiName, OperationName, Region|
|TotalTransactions|Total Transactions|Mennyiség|Összes|Total number of transactions.|No Dimensions|
|TotalTokenCalls|Total Token Calls|Mennyiség|Összes|Total number of token calls.|ApiName, OperationName, Region|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Százalékos processzorhasználat|Százalékos processzorhasználat|Százalék|Átlag|The percentage of allocated compute units that are currently in use by the Virtual Machine(s)|No Dimensions|
|Bejövő hálózat|Network In Billable|Bytes|Összes|The number of billable bytes received on all network interfaces by the Virtual Machine(s) (Incoming Traffic)|No Dimensions|
|Kimenő hálózat|Network Out Billable|Bytes|Összes|The number of billable bytes out on all network interfaces by the Virtual Machine(s) (Outgoing Traffic)|No Dimensions|
|Disk Read Bytes|Disk Read Bytes|Bytes|Összes|Bytes read from disk during monitoring period|No Dimensions|
|Disk Write Bytes|Disk Write Bytes|Bytes|Összes|Bytes written to disk during monitoring period|No Dimensions|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Átlag|Disk Read IOPS|No Dimensions|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Átlag|Disk Write IOPS|No Dimensions|
|CPU Credits Remaining|CPU Credits Remaining|Mennyiség|Átlag|Total number of credits available to burst|No Dimensions|
|CPU Credits Consumed|CPU Credits Consumed|Mennyiség|Átlag|Total number of credits consumed by the Virtual Machine|No Dimensions|
|Per Disk Read Bytes/sec|Data Disk Read Bytes/Sec (Deprecated)|CountPerSecond|Átlag|Bytes/Sec read from a single disk during monitoring period|SlotId|
|Per Disk Write Bytes/sec|Data Disk Write Bytes/Sec (Deprecated)|CountPerSecond|Átlag|Bytes/Sec written to a single disk during monitoring period|SlotId|
|Per Disk Read Operations/Sec|Data Disk Read Operations/Sec (Deprecated)|CountPerSecond|Átlag|Read IOPS from a single disk during monitoring period|SlotId|
|Per Disk Write Operations/Sec|Data Disk Write Operations/Sec (Deprecated)|CountPerSecond|Átlag|Write IOPS from a single disk during monitoring period|SlotId|
|Per Disk QD|Data Disk QD (Deprecated)|Mennyiség|Átlag|Data Disk Queue Depth(or Queue Length)|SlotId|
|OS Per Disk Read Bytes/sec|OS Disk Read Bytes/Sec (Deprecated)|CountPerSecond|Átlag|Bytes/Sec read from a single disk during monitoring period for OS disk|No Dimensions|
|OS Per Disk Write Bytes/sec|OS Disk Write Bytes/Sec (Deprecated)|CountPerSecond|Átlag|Bytes/Sec written to a single disk during monitoring period for OS disk|No Dimensions|
|OS Per Disk Read Operations/Sec|OS Disk Read Operations/Sec (Deprecated)|CountPerSecond|Átlag|Read IOPS from a single disk during monitoring period for OS disk|No Dimensions|
|OS Per Disk Write Operations/Sec|OS Disk Write Operations/Sec (Deprecated)|CountPerSecond|Átlag|Write IOPS from a single disk during monitoring period for OS disk|No Dimensions|
|OS Per Disk QD|OS Disk QD (Deprecated)|Mennyiség|Átlag|OS Disk Queue Depth(or Queue Length)|No Dimensions|
|Data Disk Read Bytes/sec|Data Disk Read Bytes/Sec (Preview)|CountPerSecond|Átlag|Bytes/Sec read from a single disk during monitoring period|LUN|
|Data Disk Write Bytes/sec|Data Disk Write Bytes/Sec (Preview)|CountPerSecond|Átlag|Bytes/Sec written to a single disk during monitoring period|LUN|
|Data Disk Read Operations/Sec|Data Disk Read Operations/Sec (Preview)|CountPerSecond|Átlag|Read IOPS from a single disk during monitoring period|LUN|
|Data Disk Write Operations/Sec|Data Disk Write Operations/Sec (Preview)|CountPerSecond|Átlag|Write IOPS from a single disk during monitoring period|LUN|
|Data Disk Queue Depth|Data Disk Queue Depth (Preview)|Mennyiség|Átlag|Data Disk Queue Depth(or Queue Length)|LUN|
|OS Disk Read Bytes/sec|OS Disk Read Bytes/Sec (Preview)|CountPerSecond|Átlag|Bytes/Sec read from a single disk during monitoring period for OS disk|No Dimensions|
|OS Disk Write Bytes/sec|OS Disk Write Bytes/Sec (Preview)|CountPerSecond|Átlag|Bytes/Sec written to a single disk during monitoring period for OS disk|No Dimensions|
|OS Disk Read Operations/Sec|OS Disk Read Operations/Sec (Preview)|CountPerSecond|Átlag|Read IOPS from a single disk during monitoring period for OS disk|No Dimensions|
|OS Disk Write Operations/Sec|OS Disk Write Operations/Sec (Preview)|CountPerSecond|Átlag|Write IOPS from a single disk during monitoring period for OS disk|No Dimensions|
|OS Disk Queue Depth|OS Disk Queue Depth (Preview)|Mennyiség|Átlag|OS Disk Queue Depth(or Queue Length)|No Dimensions|
|Inbound Flows|Inbound Flows (Preview)|Mennyiség|Átlag|Inbound Flows are number of current flows in the inbound direction (traffic going into the VM)|No Dimensions|
|Outbound Flows|Outbound Flows (Preview)|Mennyiség|Átlag|Outbound Flows are number of current flows in the outbound direction (traffic going out of the VM)|No Dimensions|
|Inbound Flows Maximum Creation Rate|Inbound Flows Maximum Creation Rate (Preview)|CountPerSecond|Átlag|The maximum creation rate of inbound flows (traffic going into the VM)|No Dimensions|
|Outbound Flows Maximum Creation Rate|Outbound Flows Maximum Creation Rate (Preview)|CountPerSecond|Átlag|The maximum creation rate of outbound flows (traffic going out of the VM)|No Dimensions|
|Premium Data Disk Cache Read Hit|Premium Data Disk Cache Read Hit (Preview)|Százalék|Átlag|Premium Data Disk Cache Read Hit|LUN|
|Premium Data Disk Cache Read Miss|Premium Data Disk Cache Read Miss (Preview)|Százalék|Átlag|Premium Data Disk Cache Read Miss|LUN|
|Premium OS Disk Cache Read Hit|Premium OS Disk Cache Read Hit (Preview)|Százalék|Átlag|Premium OS Disk Cache Read Hit|No Dimensions|
|Premium OS Disk Cache Read Miss|Premium OS Disk Cache Read Miss (Preview)|Százalék|Átlag|Premium OS Disk Cache Read Miss|No Dimensions|
|Network In Total|Network In Total|Bytes|Összes|The number of bytes received on all network interfaces by the Virtual Machine(s) (Incoming Traffic)|No Dimensions|
|Network Out Total|Network Out Total|Bytes|Összes|The number of bytes out on all network interfaces by the Virtual Machine(s) (Outgoing Traffic)|No Dimensions|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Százalékos processzorhasználat|Százalékos processzorhasználat|Százalék|Átlag|The percentage of allocated compute units that are currently in use by the Virtual Machine(s)|VMName|
|Bejövő hálózat|Network In Billable|Bytes|Összes|The number of billable bytes received on all network interfaces by the Virtual Machine(s) (Incoming Traffic)|VMName|
|Kimenő hálózat|Network Out Billable|Bytes|Összes|The number of billable bytes out on all network interfaces by the Virtual Machine(s) (Outgoing Traffic)|VMName|
|Disk Read Bytes|Disk Read Bytes|Bytes|Összes|Bytes read from disk during monitoring period|VMName|
|Disk Write Bytes|Disk Write Bytes|Bytes|Összes|Bytes written to disk during monitoring period|VMName|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Átlag|Disk Read IOPS|VMName|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Átlag|Disk Write IOPS|VMName|
|CPU Credits Remaining|CPU Credits Remaining|Mennyiség|Átlag|Total number of credits available to burst|No Dimensions|
|CPU Credits Consumed|CPU Credits Consumed|Mennyiség|Átlag|Total number of credits consumed by the Virtual Machine|No Dimensions|
|Per Disk Read Bytes/sec|Data Disk Read Bytes/Sec (Deprecated)|CountPerSecond|Átlag|Bytes/Sec read from a single disk during monitoring period|SlotId|
|Per Disk Write Bytes/sec|Data Disk Write Bytes/Sec (Deprecated)|CountPerSecond|Átlag|Bytes/Sec written to a single disk during monitoring period|SlotId|
|Per Disk Read Operations/Sec|Data Disk Read Operations/Sec (Deprecated)|CountPerSecond|Átlag|Read IOPS from a single disk during monitoring period|SlotId|
|Per Disk Write Operations/Sec|Data Disk Write Operations/Sec (Deprecated)|CountPerSecond|Átlag|Write IOPS from a single disk during monitoring period|SlotId|
|Per Disk QD|Data Disk QD (Deprecated)|Mennyiség|Átlag|Data Disk Queue Depth(or Queue Length)|SlotId|
|OS Per Disk Read Bytes/sec|OS Disk Read Bytes/Sec (Deprecated)|CountPerSecond|Átlag|Bytes/Sec read from a single disk during monitoring period for OS disk|No Dimensions|
|OS Per Disk Write Bytes/sec|OS Disk Write Bytes/Sec (Deprecated)|CountPerSecond|Átlag|Bytes/Sec written to a single disk during monitoring period for OS disk|No Dimensions|
|OS Per Disk Read Operations/Sec|OS Disk Read Operations/Sec (Deprecated)|CountPerSecond|Átlag|Read IOPS from a single disk during monitoring period for OS disk|No Dimensions|
|OS Per Disk Write Operations/Sec|OS Disk Write Operations/Sec (Deprecated)|CountPerSecond|Átlag|Write IOPS from a single disk during monitoring period for OS disk|No Dimensions|
|OS Per Disk QD|OS Disk QD (Deprecated)|Mennyiség|Átlag|OS Disk Queue Depth(or Queue Length)|No Dimensions|
|Data Disk Read Bytes/sec|Data Disk Read Bytes/Sec (Preview)|CountPerSecond|Átlag|Bytes/Sec read from a single disk during monitoring period|LUN, VMName|
|Data Disk Write Bytes/sec|Data Disk Write Bytes/Sec (Preview)|CountPerSecond|Átlag|Bytes/Sec written to a single disk during monitoring period|LUN, VMName|
|Data Disk Read Operations/Sec|Data Disk Read Operations/Sec (Preview)|CountPerSecond|Átlag|Read IOPS from a single disk during monitoring period|LUN, VMName|
|Data Disk Write Operations/Sec|Data Disk Write Operations/Sec (Preview)|CountPerSecond|Átlag|Write IOPS from a single disk during monitoring period|LUN, VMName|
|Data Disk Queue Depth|Data Disk Queue Depth (Preview)|Mennyiség|Átlag|Data Disk Queue Depth(or Queue Length)|LUN, VMName|
|OS Disk Read Bytes/sec|OS Disk Read Bytes/Sec (Preview)|CountPerSecond|Átlag|Bytes/Sec read from a single disk during monitoring period for OS disk|VMName|
|OS Disk Write Bytes/sec|OS Disk Write Bytes/Sec (Preview)|CountPerSecond|Átlag|Bytes/Sec written to a single disk during monitoring period for OS disk|VMName|
|OS Disk Read Operations/Sec|OS Disk Read Operations/Sec (Preview)|CountPerSecond|Átlag|Read IOPS from a single disk during monitoring period for OS disk|VMName|
|OS Disk Write Operations/Sec|OS Disk Write Operations/Sec (Preview)|CountPerSecond|Átlag|Write IOPS from a single disk during monitoring period for OS disk|VMName|
|OS Disk Queue Depth|OS Disk Queue Depth (Preview)|Mennyiség|Átlag|OS Disk Queue Depth(or Queue Length)|VMName|
|Inbound Flows|Inbound Flows (Preview)|Mennyiség|Átlag|Inbound Flows are number of current flows in the inbound direction (traffic going into the VM)|VMName|
|Outbound Flows|Outbound Flows (Preview)|Mennyiség|Átlag|Outbound Flows are number of current flows in the outbound direction (traffic going out of the VM)|VMName|
|Inbound Flows Maximum Creation Rate|Inbound Flows Maximum Creation Rate (Preview)|CountPerSecond|Átlag|The maximum creation rate of inbound flows (traffic going into the VM)|VMName|
|Outbound Flows Maximum Creation Rate|Outbound Flows Maximum Creation Rate (Preview)|CountPerSecond|Átlag|The maximum creation rate of outbound flows (traffic going out of the VM)|VMName|
|Premium Data Disk Cache Read Hit|Premium Data Disk Cache Read Hit (Preview)|Százalék|Átlag|Premium Data Disk Cache Read Hit|LUN, VMName|
|Premium Data Disk Cache Read Miss|Premium Data Disk Cache Read Miss (Preview)|Százalék|Átlag|Premium Data Disk Cache Read Miss|LUN, VMName|
|Premium OS Disk Cache Read Hit|Premium OS Disk Cache Read Hit (Preview)|Százalék|Átlag|Premium OS Disk Cache Read Hit|VMName|
|Premium OS Disk Cache Read Miss|Premium OS Disk Cache Read Miss (Preview)|Százalék|Átlag|Premium OS Disk Cache Read Miss|VMName|
|Network In Total|Network In Total|Bytes|Összes|The number of bytes received on all network interfaces by the Virtual Machine(s) (Incoming Traffic)|VMName|
|Network Out Total|Network Out Total|Bytes|Összes|The number of bytes out on all network interfaces by the Virtual Machine(s) (Outgoing Traffic)|VMName|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CpuUsage|CPU Usage|Mennyiség|Átlag|CPU usage on all cores in millicores.|containerName|
|MemoryUsage|Memory Usage|Bytes|Átlag|Total memory usage in byte.|containerName|
|NetworkBytesReceivedPerSecond|Network Bytes Received Per Second|Bytes|Átlag|The network bytes received per second.|No Dimensions|
|NetworkBytesTransmittedPerSecond|Network Bytes Transmitted Per Second|Bytes|Átlag|The network bytes transmitted per second.|No Dimensions|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TotalPullCount|Total Pull Count|Mennyiség|Átlag|Number of image pulls in total|No Dimensions|
|SuccessfulPullCount|Successful Pull Count|Mennyiség|Átlag|Number of successful image pulls|No Dimensions|
|TotalPushCount|Total Push Count|Mennyiség|Átlag|Number of image pushes in total|No Dimensions|
|SuccessfulPushCount|Successful Push Count|Mennyiség|Átlag|Number of successful image pushes|No Dimensions|
|RunDuration|Run Duration|Milliseconds|Összes|Run Duration in milliseconds|No Dimensions|

## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Total number of available cpu cores in a managed cluster|Mennyiség|Összes|Total number of available cpu cores in a managed cluster|No Dimensions|
|kube_node_status_allocatable_memory_bytes|Total amount of available memory in a managed cluster|Bytes|Összes|Total amount of available memory in a managed cluster|No Dimensions|
|kube_pod_status_ready|Number of pods in Ready state|Mennyiség|Összes|Number of pods in Ready state|namespace, pod|
|kube_node_status_condition|Statuses for various node conditions|Mennyiség|Összes|Statuses for various node conditions|condition, status, status2, node|
|kube_pod_status_phase|Number of pods by phase|Mennyiség|Összes|Number of pods by phase|phase, namespace, pod|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft.CustomerInsights/hubs

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|DCIApiCalls|Customer Insights API Calls|Mennyiség|Összes||No Dimensions|
|DCIMappingImportOperationSuccessfulLines|Mapping Import Operation Successful Lines|Mennyiség|Összes||No Dimensions|
|DCIMappingImportOperationFailedLines|Mapping Import Operation Failed Lines|Mennyiség|Összes||No Dimensions|
|DCIMappingImportOperationTotalLines|Mapping Import Operation Total Lines|Mennyiség|Összes||No Dimensions|
|DCIMappingImportOperationRuntimeInSeconds|Mapping Import Operation Runtime In Seconds|másodperc|Összes||No Dimensions|
|DCIOutboundProfileExportSucceeded|Outbound Profile Export Succeeded|Mennyiség|Összes||No Dimensions|
|DCIOutboundProfileExportFailed|Outbound Profile Export Failed|Mennyiség|Összes||No Dimensions|
|DCIOutboundProfileExportDuration|Outbound Profile Export Duration|másodperc|Összes||No Dimensions|
|DCIOutboundKpiExportSucceeded|Outbound Kpi Export Succeeded|Mennyiség|Összes||No Dimensions|
|DCIOutboundKpiExportFailed|Outbound Kpi Export Failed|Mennyiség|Összes||No Dimensions|
|DCIOutboundKpiExportDuration|Outbound Kpi Export Duration|másodperc|Összes||No Dimensions|
|DCIOutboundKpiExportStarted|Outbound Kpi Export Started|másodperc|Összes||No Dimensions|
|DCIOutboundKpiRecordCount|Outbound Kpi Record Count|másodperc|Összes||No Dimensions|
|DCIOutboundProfileExportCount|Outbound Profile Export Count|másodperc|Összes||No Dimensions|
|DCIOutboundInitialProfileExportFailed|Outbound Initial Profile Export Failed|másodperc|Összes||No Dimensions|
|DCIOutboundInitialProfileExportSucceeded|Outbound Initial Profile Export Succeeded|másodperc|Összes||No Dimensions|
|DCIOutboundInitialKpiExportFailed|Outbound Initial Kpi Export Failed|másodperc|Összes||No Dimensions|
|DCIOutboundInitialKpiExportSucceeded|Outbound Initial Kpi Export Succeeded|másodperc|Összes||No Dimensions|
|DCIOutboundInitialProfileExportDurationInSeconds|Outbound Initial Profile Export Duration In Seconds|másodperc|Összes||No Dimensions|
|AdlaJobForStandardKpiFailed|Adla Job For Standard Kpi Failed In Seconds|másodperc|Összes||No Dimensions|
|AdlaJobForStandardKpiTimeOut|Adla Job For Standard Kpi TimeOut In Seconds|másodperc|Összes||No Dimensions|
|AdlaJobForStandardKpiCompleted|Adla Job For Standard Kpi Completed In Seconds|másodperc|Összes||No Dimensions|
|ImportASAValuesFailed|Import ASA Values Failed Count|Mennyiség|Összes||No Dimensions|
|ImportASAValuesSucceeded|Import ASA Values Succeeded Count|Mennyiség|Összes||No Dimensions|
|DCIProfilesCount|Profile Instance Count|Mennyiség|Last||No Dimensions|
|DCIInteractionsPerMonthCount|Interactions per Month Count|Mennyiség|Last||No Dimensions|
|DCIKpisCount|KPI Count|Mennyiség|Last||No Dimensions|
|DCISegmentsCount|Segment Count|Mennyiség|Last||No Dimensions|
|DCIPredictiveMatchPoliciesCount|Predictive Match Count|Mennyiség|Last||No Dimensions|
|DCIPredictionsCount|Prediction Count|Mennyiség|Last||No Dimensions|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|NICReadThroughput|Read Throughput (Network)|BytesPerSecond|Átlag|The read throughput of the network interface on the device in the reporting period for all volumes in the gateway.|InstanceName|
|NICWriteThroughput|Write Throughput (Network)|BytesPerSecond|Átlag|The write throughput of the network interface on the device in the reporting period for all volumes in the gateway.|InstanceName|
|CloudReadThroughputPerShare|Cloud Download Throughput (Share)|BytesPerSecond|Átlag|The download throughput to Azure from a share during the reporting period.|Megosztás|
|CloudUploadThroughputPerShare|Cloud Upload Throughput (Share)|BytesPerSecond|Átlag|The upload throughput to Azure from a share during the reporting period.|Megosztás|
|BytesUploadedToCloudPerShare|Cloud Bytes Uploaded (Share)|Bytes|Átlag|The total number of bytes that is uploaded to Azure from a share during the reporting period.|Megosztás|
|TotalCapacity|Total Capacity|Bytes|Átlag|Total Capacity|No Dimensions|
|AvailableCapacity|Available Capacity|Bytes|Átlag|The available capacity in bytes during the reporting period.|No Dimensions|
|CloudUploadThroughput|Cloud Upload Throughput|BytesPerSecond|Átlag|The cloud upload throughput  to Azure during the reporting period.|No Dimensions|
|CloudReadThroughput|Cloud Download Throughput|BytesPerSecond|Átlag|The cloud download throughput to Azure during the reporting period.|No Dimensions|
|BytesUploadedToCloud|Cloud Bytes Uploaded (Device)|Bytes|Átlag|The total number of bytes that is uploaded to Azure from a device during the reporting period.|No Dimensions|
|HyperVVirtualProcessorUtilization|Edge Compute - Percentage CPU|Százalék|Átlag|Percent CPU Usage|InstanceName|
|HyperVMemoryUtilization|Edge Compute - Memory Usage|Százalék|Átlag|Amount of RAM in Use|InstanceName|

## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|FailedRuns|Failed Runs|Mennyiség|Összes||pipelineName, activityName|
|SuccessfulRuns|Successful Runs|Mennyiség|Összes||pipelineName, activityName|

## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|PipelineFailedRuns|Failed pipeline runs metrics|Mennyiség|Összes||FailureType, Name|
|PipelineSucceededRuns|Succeeded pipeline runs metrics|Mennyiség|Összes||FailureType, Name|
|ActivityFailedRuns|Failed activity runs metrics|Mennyiség|Összes||ActivityType, PipelineName, FailureType, Name|
|ActivitySucceededRuns|Succeeded activity runs metrics|Mennyiség|Összes||ActivityType, PipelineName, FailureType, Name|
|TriggerFailedRuns|Failed trigger runs metrics|Mennyiség|Összes||Name, FailureType|
|TriggerSucceededRuns|Succeeded trigger runs metrics|Mennyiség|Összes||Name, FailureType|
|IntegrationRuntimeCpuPercentage|Integration runtime CPU utilization|Százalék|Átlag||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeAvailableMemory|Integration runtime available memory|Bytes|Átlag||IntegrationRuntimeName, NodeName|
|MaxAllowedResourceCount|Maximum allowed entities count|Mennyiség|Maximum||No Dimensions|
|MaxAllowedFactorySizeInGbUnits|Maximum allowed factory size (GB unit)|Mennyiség|Maximum||No Dimensions|
|ResourceCount|Total entities count|Mennyiség|Maximum||No Dimensions|
|FactorySizeInGbUnits|Total factory size (GB unit)|Mennyiség|Maximum||No Dimensions|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|JobEndedSuccess|Successful Jobs|Mennyiség|Összes|Count of successful jobs.|No Dimensions|
|JobEndedFailure|Failed Jobs|Mennyiség|Összes|Count of failed jobs.|No Dimensions|
|JobEndedCancelled|Canceled Jobs|Mennyiség|Összes|Count of canceled jobs.|No Dimensions|
|JobAUEndedSuccess|Successful AU Time|másodperc|Összes|Total AU time for successful jobs.|No Dimensions|
|JobAUEndedFailure|Failed AU Time|másodperc|Összes|Total AU time for failed jobs.|No Dimensions|
|JobAUEndedCancelled|Canceled AU Time|másodperc|Összes|Total AU time for canceled jobs.|No Dimensions|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TotalStorage|Összes tárhely|Bytes|Maximum|Total amount of data stored in the account.|No Dimensions|
|DataWritten|Data Written|Bytes|Összes|Total amount of data written to the account.|No Dimensions|
|DataRead|Data Read|Bytes|Összes|Total amount of data read from the account.|No Dimensions|
|WriteRequests|Write Requests|Mennyiség|Összes|Count of data write requests to the account.|No Dimensions|
|ReadRequests|Read Requests|Mennyiség|Összes|Count of data read requests to the account.|No Dimensions|

## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|cpu_percent|CPU percent|Százalék|Átlag|CPU percent|No Dimensions|
|memory_percent|Memory percent|Százalék|Átlag|Memory percent|No Dimensions|
|io_consumption_percent|IO percent|Százalék|Átlag|IO percent|No Dimensions|
|storage_percent|Storage percent|Százalék|Átlag|Storage percent|No Dimensions|
|storage_used|Felhasznált tárterület|Bytes|Átlag|Felhasznált tárterület|No Dimensions|
|storage_limit|Storage limit|Bytes|Átlag|Storage limit|No Dimensions|
|serverlog_storage_percent|Server Log storage percent|Százalék|Átlag|Server Log storage percent|No Dimensions|
|serverlog_storage_usage|Server Log storage used|Bytes|Átlag|Server Log storage used|No Dimensions|
|serverlog_storage_limit|Server Log storage limit|Bytes|Átlag|Server Log storage limit|No Dimensions|
|active_connections|Active Connections|Mennyiség|Átlag|Active Connections|No Dimensions|
|connections_failed|Sikertelen kapcsolatok|Mennyiség|Összes|Sikertelen kapcsolatok|No Dimensions|
|seconds_behind_master|Replication lag in seconds|Mennyiség|Átlag|Replication lag in seconds|No Dimensions|
|backup_storage_used|Backup Storage used|Bytes|Átlag|Backup Storage used|No Dimensions|
|network_bytes_egress|Kimenő hálózat|Bytes|Összes|Network Out across active connections|No Dimensions|
|network_bytes_ingress|Bejövő hálózat|Bytes|Összes|Network In across active connections|No Dimensions|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|cpu_percent|CPU percent|Százalék|Átlag|CPU percent|No Dimensions|
|memory_percent|Memory percent|Százalék|Átlag|Memory percent|No Dimensions|
|io_consumption_percent|IO percent|Százalék|Átlag|IO percent|No Dimensions|
|storage_percent|Storage percent|Százalék|Átlag|Storage percent|No Dimensions|
|storage_used|Felhasznált tárterület|Bytes|Átlag|Felhasznált tárterület|No Dimensions|
|storage_limit|Storage limit|Bytes|Átlag|Storage limit|No Dimensions|
|serverlog_storage_percent|Server Log storage percent|Százalék|Átlag|Server Log storage percent|No Dimensions|
|serverlog_storage_usage|Server Log storage used|Bytes|Átlag|Server Log storage used|No Dimensions|
|serverlog_storage_limit|Server Log storage limit|Bytes|Átlag|Server Log storage limit|No Dimensions|
|active_connections|Active Connections|Mennyiség|Átlag|Active Connections|No Dimensions|
|connections_failed|Sikertelen kapcsolatok|Mennyiség|Összes|Sikertelen kapcsolatok|No Dimensions|
|seconds_behind_master|Replication lag in seconds|Mennyiség|Átlag|Replication lag in seconds|No Dimensions|
|backup_storage_used|Backup Storage used|Bytes|Átlag|Backup Storage used|No Dimensions|
|network_bytes_egress|Kimenő hálózat|Bytes|Összes|Network Out across active connections|No Dimensions|
|network_bytes_ingress|Bejövő hálózat|Bytes|Összes|Network In across active connections|No Dimensions|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|cpu_percent|CPU percent|Százalék|Átlag|CPU percent|No Dimensions|
|memory_percent|Memory percent|Százalék|Átlag|Memory percent|No Dimensions|
|io_consumption_percent|IO percent|Százalék|Átlag|IO percent|No Dimensions|
|storage_percent|Storage percent|Százalék|Átlag|Storage percent|No Dimensions|
|storage_used|Felhasznált tárterület|Bytes|Átlag|Felhasznált tárterület|No Dimensions|
|storage_limit|Storage limit|Bytes|Átlag|Storage limit|No Dimensions|
|serverlog_storage_percent|Server Log storage percent|Százalék|Átlag|Server Log storage percent|No Dimensions|
|serverlog_storage_usage|Server Log storage used|Bytes|Átlag|Server Log storage used|No Dimensions|
|serverlog_storage_limit|Server Log storage limit|Bytes|Átlag|Server Log storage limit|No Dimensions|
|active_connections|Active Connections|Mennyiség|Átlag|Active Connections|No Dimensions|
|connections_failed|Sikertelen kapcsolatok|Mennyiség|Összes|Sikertelen kapcsolatok|No Dimensions|
|backup_storage_used|Backup Storage used|Bytes|Átlag|Backup Storage used|No Dimensions|
|network_bytes_egress|Kimenő hálózat|Bytes|Összes|Network Out across active connections|No Dimensions|
|network_bytes_ingress|Bejövő hálózat|Bytes|Összes|Network In across active connections|No Dimensions|
|pg_replica_log_delay_in_seconds|Replica Lag|másodperc|Maximum|Replica lag in seconds|No Dimensions|
|pg_replica_log_delay_in_bytes|Max Lag Across Replicas|Bytes|Maximum|Lag in bytes of the most lagging replica|No Dimensions|

## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|cpu_percent|CPU percent|Százalék|Átlag|CPU percent|No Dimensions|
|memory_percent|Memory percent|Százalék|Átlag|Memory percent|No Dimensions|
|iops|IO|Mennyiség|Átlag|IO Operations per second|No Dimensions|
|storage_percent|Storage percent|Százalék|Átlag|Storage percent|No Dimensions|
|storage_used|Felhasznált tárterület|Bytes|Átlag|Felhasznált tárterület|No Dimensions|
|active_connections|Active Connections|Mennyiség|Átlag|Active Connections|No Dimensions|
|network_bytes_egress|Kimenő hálózat|Bytes|Összes|Network Out across active connections|No Dimensions|
|network_bytes_ingress|Bejövő hálózat|Bytes|Összes|Network In across active connections|No Dimensions|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Telemetry message send attempts|Mennyiség|Összes|Number of device-to-cloud telemetry messages attempted to be sent to your IoT hub|No Dimensions|
|d2c.telemetry.ingress.success|Telemetry messages sent|Mennyiség|Összes|Number of device-to-cloud telemetry messages sent successfully to your IoT hub|No Dimensions|
|c2d.commands.egress.complete.success|Commands completed|Mennyiség|Összes|Number of cloud-to-device commands completed successfully by the device|No Dimensions|
|c2d.commands.egress.abandon.success|Commands abandoned|Mennyiség|Összes|Number of cloud-to-device commands abandoned by the device|No Dimensions|
|c2d.commands.egress.reject.success|Commands rejected|Mennyiség|Összes|Number of cloud-to-device commands rejected by the device|No Dimensions|
|devices.totalDevices|Total devices (deprecated)|Mennyiség|Összes|Number of devices registered to your IoT hub|No Dimensions|
|devices.connectedDevices.allProtocol|Connected devices (deprecated) |Mennyiség|Összes|Number of devices connected to your IoT hub|No Dimensions|
|d2c.telemetry.egress.success|Routing: telemetry messages delivered|Mennyiség|Összes|The number of times messages were successfully delivered to all endpoints using IoT Hub routing. If a message is routed to multiple endpoints, this value increases by one for each successful delivery. If a message is delivered to the same endpoint multiple times, this value increases by one for each successful delivery.|No Dimensions|
|d2c.telemetry.egress.dropped|Routing: telemetry messages dropped |Mennyiség|Összes|The number of times messages were dropped by IoT Hub routing due to dead endpoints. This value does not count messages delivered to fallback route as dropped messages are not delivered there.|No Dimensions|
|d2c.telemetry.egress.orphaned|Routing: telemetry messages orphaned |Mennyiség|Összes|The number of times messages were orphaned by IoT Hub routing because they didn't match any routing rules (including the fallback rule). |No Dimensions|
|d2c.telemetry.egress.invalid|Routing: telemetry messages incompatible|Mennyiség|Összes|The number of times IoT Hub routing failed to deliver messages due to an incompatibility with the endpoint. This value does not include retries.|No Dimensions|
|d2c.telemetry.egress.fallback|Routing: messages delivered to fallback|Mennyiség|Összes|The number of times IoT Hub routing delivered messages to the endpoint associated with the fallback route.|No Dimensions|
|d2c.endpoints.egress.eventHubs|Routing: messages delivered to Event Hub|Mennyiség|Összes|The number of times IoT Hub routing successfully delivered messages to Event Hub endpoints.|No Dimensions|
|d2c.endpoints.latency.eventHubs|Routing: message latency for Event Hub|Milliseconds|Átlag|The average latency (milliseconds) between message ingress to IoT Hub and message ingress into an Event Hub endpoint.|No Dimensions|
|d2c.endpoints.egress.serviceBusQueues|Routing: messages delivered to Service Bus Queue|Mennyiség|Összes|The number of times IoT Hub routing successfully delivered messages to Service Bus queue endpoints.|No Dimensions|
|d2c.endpoints.latency.serviceBusQueues|Routing: message latency for Service Bus Queue|Milliseconds|Átlag|The average latency (milliseconds) between message ingress to IoT Hub and telemetry message ingress into a Service Bus queue endpoint.|No Dimensions|
|d2c.endpoints.egress.serviceBusTopics|Routing: messages delivered to Service Bus Topic|Mennyiség|Összes|The number of times IoT Hub routing successfully delivered messages to Service Bus topic endpoints.|No Dimensions|
|d2c.endpoints.latency.serviceBusTopics|Routing: message latency for Service Bus Topic|Milliseconds|Átlag|The average latency (milliseconds) between message ingress to IoT Hub and telemetry message ingress into a Service Bus topic endpoint.|No Dimensions|
|d2c.endpoints.egress.builtIn.events|Routing: messages delivered to messages/events|Mennyiség|Összes|The number of times IoT Hub routing successfully delivered messages to the built-in endpoint (messages/events). This metric only starts working when routing is enabled (https://aka.ms/iotrouting) for the IoT hub.|No Dimensions|
|d2c.endpoints.latency.builtIn.events|Routing: message latency for messages/events|Milliseconds|Átlag|The average latency (milliseconds) between message ingress to IoT Hub and telemetry message ingress into the built-in endpoint (messages/events). This metric only starts working when routing is enabled (https://aka.ms/iotrouting) for the IoT hub.|No Dimensions|
|d2c.endpoints.egress.storage|Routing: messages delivered to storage|Mennyiség|Összes|The number of times IoT Hub routing successfully delivered messages to storage endpoints.|No Dimensions|
|d2c.endpoints.latency.storage|Routing: message latency for storage|Milliseconds|Átlag|The average latency (milliseconds) between message ingress to IoT Hub and telemetry message ingress into a storage endpoint.|No Dimensions|
|d2c.endpoints.egress.storage.bytes|Routing: data delivered to storage|Bytes|Összes|The amount of data (bytes) IoT Hub routing delivered to storage endpoints.|No Dimensions|
|d2c.endpoints.egress.storage.blobs|Routing: blobs delivered to storage|Mennyiség|Összes|The number of times IoT Hub routing delivered blobs to storage endpoints.|No Dimensions|
|EventGridDeliveries|Event Grid deliveries (preview)|Mennyiség|Összes|The number of IoT Hub events published to Event Grid. Use the Result dimension for the number of successful and failed requests. EventType dimension shows the type of event (https://aka.ms/ioteventgrid).|Result, EventType|
|EventGridLatency|The average latency (milliseconds) from when the Iot Hub event was generated to when the event was published to Event Grid. This number is an average between all event types. Use the EventType dimension to see latency of a specific type of event.|EventType|
|d2c.twin.read.success|Successful twin reads from devices|Mennyiség|Összes|The count of all successful device-initiated twin reads.|No Dimensions|
|d2c.twin.read.failure|Failed twin reads from devices|Mennyiség|Összes|The count of all failed device-initiated twin reads.|No Dimensions|
|d2c.twin.read.size|Response size of twin reads from devices|Bytes|Átlag|The average, min, and max of all successful device-initiated twin reads.|No Dimensions|
|d2c.twin.update.success|Successful twin updates from devices|Mennyiség|Összes|The count of all successful device-initiated twin updates.|No Dimensions|
|d2c.twin.update.failure|Failed twin updates from devices|Mennyiség|Összes|The count of all failed device-initiated twin updates.|No Dimensions|
|d2c.twin.update.size|Size of twin updates from devices|Bytes|Átlag|The average, min, and max size of all successful device-initiated twin updates.|No Dimensions|
|c2d.methods.success|Successful direct method invocations|Mennyiség|Összes|The count of all successful direct method calls.|No Dimensions|
|c2d.methods.failure|Failed direct method invocations|Mennyiség|Összes|The count of all failed direct method calls.|No Dimensions|
|c2d.methods.requestSize|Request size of direct method invocations|Bytes|Átlag|The average, min, and max of all successful direct method requests.|No Dimensions|
|c2d.methods.responseSize|Response size of direct method invocations|Bytes|Átlag|The average, min, and max of all successful direct method responses.|No Dimensions|
|c2d.twin.read.success|Successful twin reads from back end|Mennyiség|Összes|The count of all successful back-end-initiated twin reads.|No Dimensions|
|c2d.twin.read.failure|Failed twin reads from back end|Mennyiség|Összes|The count of all failed back-end-initiated twin reads.|No Dimensions|
|c2d.twin.read.size|Response size of twin reads from back end|Bytes|Átlag|The average, min, and max of all successful back-end-initiated twin reads.|No Dimensions|
|c2d.twin.update.success|Successful twin updates from back end|Mennyiség|Összes|The count of all successful back-end-initiated twin updates.|No Dimensions|
|c2d.twin.update.failure|Failed twin updates from back end|Mennyiség|Összes|The count of all failed back-end-initiated twin updates.|No Dimensions|
|c2d.twin.update.size|Size of twin updates from back end|Bytes|Átlag|The average, min, and max size of all successful back-end-initiated twin updates.|No Dimensions|
|twinQueries.success|Successful twin queries|Mennyiség|Összes|The count of all successful twin queries.|No Dimensions|
|twinQueries.failure|Failed twin queries|Mennyiség|Összes|The count of all failed twin queries.|No Dimensions|
|twinQueries.resultSize|Twin queries result size|Bytes|Átlag|The average, min, and max of the result size of all successful twin queries.|No Dimensions|
|jobs.createTwinUpdateJob.success|Successful creations of twin update jobs|Mennyiség|Összes|The count of all successful creation of twin update jobs.|No Dimensions|
|jobs.createTwinUpdateJob.failure|Failed creations of twin update jobs|Mennyiség|Összes|The count of all failed creation of twin update jobs.|No Dimensions|
|jobs.createDirectMethodJob.success|Successful creations of method invocation jobs|Mennyiség|Összes|The count of all successful creation of direct method invocation jobs.|No Dimensions|
|jobs.createDirectMethodJob.failure|Failed creations of method invocation jobs|Mennyiség|Összes|The count of all failed creation of direct method invocation jobs.|No Dimensions|
|jobs.listJobs.success|Successful calls to list jobs|Mennyiség|Összes|The count of all successful calls to list jobs.|No Dimensions|
|jobs.listJobs.failure|Failed calls to list jobs|Mennyiség|Összes|The count of all failed calls to list jobs.|No Dimensions|
|jobs.cancelJob.success|Successful job cancellations|Mennyiség|Összes|The count of all successful calls to cancel a job.|No Dimensions|
|jobs.cancelJob.failure|Failed job cancellations|Mennyiség|Összes|The count of all failed calls to cancel a job.|No Dimensions|
|jobs.queryJobs.success|Successful job queries|Mennyiség|Összes|The count of all successful calls to query jobs.|No Dimensions|
|jobs.queryJobs.failure|Failed job queries|Mennyiség|Összes|The count of all failed calls to query jobs.|No Dimensions|
|jobs.completed|Completed jobs|Mennyiség|Összes|The count of all completed jobs.|No Dimensions|
|jobs.failed|Failed jobs|Mennyiség|Összes|The count of all failed jobs.|No Dimensions|
|d2c.telemetry.ingress.sendThrottle|Number of throttling errors|Mennyiség|Összes|Number of throttling errors due to device throughput throttles|No Dimensions|
|dailyMessageQuotaUsed|Total number of messages used|Mennyiség|Átlag|Number of total messages used today. This is a cumulative value that is reset to zero at 00:00 UTC every day.|No Dimensions|
|deviceDataUsage|Total device data usage|Bytes|Összes|Bytes transferred to and from any devices connected to IotHub|No Dimensions|
|totalDeviceCount|Total devices (preview)|Mennyiség|Átlag|Number of devices registered to your IoT hub|No Dimensions|
|connectedDeviceCount|Connected devices (preview)|Mennyiség|Átlag|Number of devices connected to your IoT hub|No Dimensions|
|configurations|Configuration Metrics|Mennyiség|Összes|Metrics for Configuration Operations|No Dimensions|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|RegistrationAttempts|Registration attempts|Mennyiség|Összes|Number of device registrations attempted|ProvisioningServiceName, IotHubName, Status|
|DeviceAssignments|Devices assigned|Mennyiség|Összes|Number of devices assigned to an IoT hub|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Attestation attempts|Mennyiség|Összes|Number of device attestations attempted|ProvisioningServiceName, Status, Protocol|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|AvailableStorage|Available Storage|Bytes|Összes|Total available storage reported at 5 minutes granularity|CollectionName, DatabaseName, Region|
|CassandraConnectionClosures|Cassandra Connection Closures|Mennyiség|Összes|Number of Cassandra connections that were closed, reported at a 1 minute granularity|Region, ClosureReason|
|CassandraRequestCharges|Cassandra Request Charges|Mennyiség|Összes|RUs consumed for Cassandra requests made|DatabaseName, CollectionName, Region, OperationType, ResourceType|
|CassandraRequests|Cassandra Requests|Mennyiség|Mennyiség|Number of Cassandra requests made|DatabaseName, CollectionName, Region, OperationType, ResourceType, ErrorCode|
|DataUsage|Adathasználat|Bytes|Összes|Total data usage reported at 5 minutes granularity|CollectionName, DatabaseName, Region|
|DocumentCount|Document Count|Mennyiség|Összes|Total document count reported at 5 minutes granularity|CollectionName, DatabaseName, Region|
|DocumentQuota|Document Quota|Bytes|Összes|Total storage quota reported at 5 minutes granularity|CollectionName, DatabaseName, Region|
|IndexUsage|Index Usage|Bytes|Összes|Total index usage reported at 5 minutes granularity|CollectionName, DatabaseName, Region|
|MetadataRequests|Metadata Requests|Mennyiség|Mennyiség|Count of metadata requests. Cosmos DB maintains system metadata collection for each account, that allows you to enumerate collections, databases, etc., and their configurations, free of charge.|DatabaseName, CollectionName, Region, StatusCode, |
|MongoRequestCharge|Mongo Request Charge|Mennyiség|Összes|Mongo Request Units Consumed|DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|MongoRequests|Mongo Requests|Mennyiség|Mennyiség|Number of Mongo Requests Made|DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|ProvisionedThroughput|Kiosztott átviteli kapacitás|Mennyiség|Maximum|Kiosztott átviteli kapacitás|DatabaseName, CollectionName|
|ReplicationLatency|P99 Replication Latency|MilliSeconds|Átlag|P99 Replication Latency across source and target regions for geo-enabled account|SourceRegion, TargetRegion|
|ServiceAvailability|Service Availability|Százalék|Átlag|Account requests availability at one hour, day or month granularity|No Dimensions|
|TotalRequestUnits|Total Request Units|Mennyiség|Összes|Request Units consumed|DatabaseName, CollectionName, Region, StatusCode, OperationType|
|TotalRequests|Összes kérelem|Mennyiség|Mennyiség|Number of requests made|DatabaseName, CollectionName, Region, StatusCode, OperationType|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|PublishSuccessCount|Published Events|Mennyiség|Összes|Total events published to this topic|No Dimensions|
|PublishFailCount|Publish Failed Events|Mennyiség|Összes|Total events failed to publish to this topic|ErrorType, Error|
|UnmatchedEventCount|Unmatched Events|Mennyiség|Összes|Total events not matching any of the event subscriptions for this topic|No Dimensions|
|PublishSuccessLatencyInMs|Publish Success Latency|Mennyiség|Összes|Publish success latency in milliseconds|No Dimensions|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|MatchedEventCount|Matched Events|Mennyiség|Összes|Total events matched to this event subscription|No Dimensions|
|DeliveryAttemptFailCount|Delivery Failed Events|Mennyiség|Összes|Total events failed to deliver to this event subscription|Error, ErrorType|
|DeliverySuccessCount|Delivered Events|Mennyiség|Összes|Total events delivered to this event subscription|No Dimensions|
|DestinationProcessingDurationInMs|Destination Processing Duration|Milliseconds|Átlag|Destination processing duration in milliseconds|No Dimensions|
|DroppedEventCount|Dropped Events|Mennyiség|Összes|Total dropped events matching to this event subscription|DropReason|
|DeadLetteredCount|Dead Lettered Events|Mennyiség|Összes|Total dead lettered events matching to this event subscription|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|PublishSuccessCount|Published Events|Mennyiség|Összes|Total events published to this topic|No Dimensions|
|PublishFailCount|Failed Events|Mennyiség|Összes|Total events failed to publish to this topic|ErrorType, Error|
|UnmatchedEventCount|Unmatched Events|Mennyiség|Összes|Total events not matching any of the event subscriptions for this topic|No Dimensions|
|PublishSuccessLatencyInMs|Publish Success Latency|Mennyiség|Összes|Publish success latency in milliseconds|No Dimensions|

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|SuccessfulRequests|Successful Requests|Mennyiség|Összes|Successful Requests for Microsoft.EventHub.|EntityName, |
|ServerErrors|Server Errors.|Mennyiség|Összes|Server Errors for Microsoft.EventHub.|EntityName, |
|UserErrors|User Errors.|Mennyiség|Összes|User Errors for Microsoft.EventHub.|EntityName, |
|QuotaExceededErrors|Quota Exceeded Errors.|Mennyiség|Összes|Quota Exceeded Errors for Microsoft.EventHub.|EntityName, |
|ThrottledRequests|Throttled Requests.|Mennyiség|Összes|Throttled Requests for Microsoft.EventHub.|EntityName, |
|IncomingRequests|Incoming Requests|Mennyiség|Összes|Incoming Requests for Microsoft.EventHub.|EntityName|
|IncomingMessages|Incoming Messages|Mennyiség|Összes|Incoming Messages for Microsoft.EventHub.|EntityName|
|OutgoingMessages|Outgoing Messages|Mennyiség|Összes|Outgoing Messages for Microsoft.EventHub.|EntityName|
|IncomingBytes|Incoming Bytes.|Bytes|Összes|Incoming Bytes for Microsoft.EventHub.|EntityName|
|OutgoingBytes|Outgoing Bytes.|Bytes|Összes|Outgoing Bytes for Microsoft.EventHub.|EntityName|
|ActiveConnections|ActiveConnections|Mennyiség|Átlag|Total Active Connections for Microsoft.EventHub.|No Dimensions|
|ConnectionsOpened|Connections Opened.|Mennyiség|Átlag|Connections Opened for Microsoft.EventHub.|EntityName|
|ConnectionsClosed|Connections Closed.|Mennyiség|Átlag|Connections Closed for Microsoft.EventHub.|EntityName|
|CaptureBacklog|Capture Backlog.|Mennyiség|Összes|Capture Backlog for Microsoft.EventHub.|EntityName|
|CapturedMessages|Captured Messages.|Mennyiség|Összes|Captured Messages for Microsoft.EventHub.|EntityName|
|CapturedBytes|Captured Bytes.|Bytes|Összes|Captured Bytes for Microsoft.EventHub.|EntityName|
|Méret|Méret|Bytes|Átlag|Size of an EventHub in Bytes.|EntityName|
|INREQS|Incoming Requests (Deprecated)|Mennyiség|Összes|Total incoming send requests for a namespace (Deprecated)|No Dimensions|
|SUCCREQ|Successful Requests (Deprecated)|Mennyiség|Összes|Total successful requests for a namespace (Deprecated)|No Dimensions|
|FAILREQ|Failed Requests (Deprecated)|Mennyiség|Összes|Total failed requests for a namespace (Deprecated)|No Dimensions|
|SVRBSY|Server Busy Errors (Deprecated)|Mennyiség|Összes|Total server busy errors for a namespace (Deprecated)|No Dimensions|
|INTERR|Internal Server Errors (Deprecated)|Mennyiség|Összes|Total internal server errors for a namespace (Deprecated)|No Dimensions|
|MISCERR|Other Errors (Deprecated)|Mennyiség|Összes|Total failed requests for a namespace (Deprecated)|No Dimensions|
|INMSGS|Incoming Messages (Deprecated) (Deprecated)|Mennyiség|Összes|Total incoming messages for a namespace. This metric is deprecated. Please use Incoming Messages metric instead (Deprecated)|No Dimensions|
|EHINMSGS|Incoming Messages (Deprecated)|Mennyiség|Összes|Total incoming messages for a namespace (Deprecated)|No Dimensions|
|OUTMSGS|Outgoing Messages (Deprecated) (Deprecated)|Mennyiség|Összes|Total outgoing messages for a namespace. This metric is deprecated. Please use Outgoing Messages metric instead (Deprecated)|No Dimensions|
|EHOUTMSGS|Outgoing Messages (Deprecated)|Mennyiség|Összes|Total outgoing messages for a namespace (Deprecated)|No Dimensions|
|EHINMBS|Incoming bytes (Deprecated) (Deprecated)|Bytes|Összes|Event Hub incoming message throughput for a namespace. This metric is deprecated. Please use Incoming bytes metric instead (Deprecated)|No Dimensions|
|EHINBYTES|Incoming bytes (Deprecated)|Bytes|Összes|Event Hub incoming message throughput for a namespace (Deprecated)|No Dimensions|
|EHOUTMBS|Outgoing bytes (Deprecated) (Deprecated)|Bytes|Összes|Event Hub outgoing message throughput for a namespace. This metric is deprecated. Please use Outgoing bytes metric instead (Deprecated)|No Dimensions|
|EHOUTBYTES|Outgoing bytes (Deprecated)|Bytes|Összes|Event Hub outgoing message throughput for a namespace (Deprecated)|No Dimensions|
|EHABL|Archive backlog messages (Deprecated)|Mennyiség|Összes|Event Hub archive messages in backlog for a namespace (Deprecated)|No Dimensions|
|EHAMSGS|Archive messages (Deprecated)|Mennyiség|Összes|Event Hub archived messages in a namespace (Deprecated)|No Dimensions|
|EHAMBS|Archive message throughput (Deprecated)|Bytes|Összes|Event Hub archived message throughput in a namespace (Deprecated)|No Dimensions|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|SuccessfulRequests|Successful Requests (Preview)|Mennyiség|Összes|Successful Requests for Microsoft.EventHub. (Előzetes verzió)|No Dimensions|
|ServerErrors|Server Errors. (Előzetes verzió)|Mennyiség|Összes|Server Errors for Microsoft.EventHub. (Előzetes verzió)|No Dimensions|
|UserErrors|User Errors. (Előzetes verzió)|Mennyiség|Összes|User Errors for Microsoft.EventHub. (Előzetes verzió)|No Dimensions|
|QuotaExceededErrors|Quota Exceeded Errors. (Előzetes verzió)|Mennyiség|Összes|Quota Exceeded Errors for Microsoft.EventHub. (Előzetes verzió)|No Dimensions|
|ThrottledRequests|Throttled Requests. (Előzetes verzió)|Mennyiség|Összes|Throttled Requests for Microsoft.EventHub. (Előzetes verzió)|No Dimensions|
|IncomingRequests|Incoming Requests (Preview)|Mennyiség|Összes|Incoming Requests for Microsoft.EventHub. (Előzetes verzió)|No Dimensions|
|IncomingMessages|Incoming Messages (Preview)|Mennyiség|Összes|Incoming Messages for Microsoft.EventHub. (Előzetes verzió)|No Dimensions|
|OutgoingMessages|Outgoing Messages (Preview)|Mennyiség|Összes|Outgoing Messages for Microsoft.EventHub. (Előzetes verzió)|No Dimensions|
|IncomingBytes|Incoming Bytes. (Előzetes verzió)|Bytes|Összes|Incoming Bytes for Microsoft.EventHub. (Előzetes verzió)|No Dimensions|
|OutgoingBytes|Outgoing Bytes. (Előzetes verzió)|Bytes|Összes|Outgoing Bytes for Microsoft.EventHub. (Előzetes verzió)|No Dimensions|
|ActiveConnections|ActiveConnections (Preview)|Mennyiség|Átlag|Total Active Connections for Microsoft.EventHub. (Előzetes verzió)|No Dimensions|
|ConnectionsOpened|Connections Opened. (Előzetes verzió)|Mennyiség|Átlag|Connections Opened for Microsoft.EventHub. (Előzetes verzió)|No Dimensions|
|ConnectionsClosed|Connections Closed. (Előzetes verzió)|Mennyiség|Átlag|Connections Closed for Microsoft.EventHub. (Előzetes verzió)|No Dimensions|
|CaptureBacklog|Capture Backlog. (Előzetes verzió)|Mennyiség|Összes|Capture Backlog for Microsoft.EventHub. (Előzetes verzió)|No Dimensions|
|CapturedMessages|Captured Messages. (Előzetes verzió)|Mennyiség|Összes|Captured Messages for Microsoft.EventHub. (Előzetes verzió)|No Dimensions|
|CapturedBytes|Captured Bytes. (Előzetes verzió)|Bytes|Összes|Captured Bytes for Microsoft.EventHub. (Előzetes verzió)|No Dimensions|
|CPU|CPU (Preview)|Százalék|Maximum|CPU utilization for the Event Hub Cluster as a percentage|Szerepkör|
|AvailableMemory|Available Memory (Preview)|Mennyiség|Maximum|Available memory for the Event Hub Cluster in bytes|Szerepkör|

## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|GatewayRequests|Gateway Requests|Mennyiség|Összes|Number of gateway requests|ClusterDnsName, HttpStatus|
|CategorizedGatewayRequests|Categorized Gateway Requests|Mennyiség|Összes|Number of gateway requests by categories (1xx/2xx/3xx/4xx/5xx)|ClusterDnsName, HttpStatus|
|NumActiveWorkers|Number of Active Workers|Mennyiség|Maximum|Number of Active Workers|ClusterDnsName, MetricName|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ObservedMetricValue|Observed Metric Value|Mennyiség|Átlag|The value computed by autoscale when executed|MetricTriggerSource|
|MetricThreshold|Metric Threshold|Mennyiség|Átlag|The configured autoscale threshold when autoscale ran.|MetricTriggerRule|
|ObservedCapacity|Observed Capacity|Mennyiség|Átlag|The capacity reported to autoscale when it executed.|No Dimensions|
|ScaleActionsInitiated|Scale Actions Initiated|Mennyiség|Összes|The direction of the scale operation.|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

(Public Preview)

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Elérhetőség|Százalék|Átlag|Percentage of successfully completed availability tests|availabilityResult/name, availabilityResult/location|
|availabilityResults/count|Rendelkezésre állási tesztek|Mennyiség|Mennyiség|Count of availability tests|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|availabilityResults/duration|Availability test duration|MilliSeconds|Átlag|Availability test duration|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|browserTimings/networkDuration|Page load network connect time|MilliSeconds|Átlag|Time between user request and network connection. Includes DNS lookup and transport connection.|No Dimensions|
|browserTimings/processingDuration|Client processing time|MilliSeconds|Átlag|Time between receiving the last byte of a document until the DOM is loaded. Async requests may still be processing.|No Dimensions|
|browserTimings/receiveDuration|Receiving response time|MilliSeconds|Átlag|Time between the first and last bytes, or until disconnection.|No Dimensions|
|browserTimings/sendDuration|Send request time|MilliSeconds|Átlag|Time between network connection and receiving the first byte.|No Dimensions|
|browserTimings/totalDuration|Browser page load time|MilliSeconds|Átlag|Time from user request until DOM, stylesheets, scripts and images are loaded.|No Dimensions|
|dependencies/count|Dependency calls|Mennyiség|Mennyiség|Count of calls made by the application to external resources.|dependency/type, dependency/performanceBucket, dependency/success, operation/synthetic, cloud/roleInstance, cloud/roleName|
|dependencies/duration|Dependency duration|MilliSeconds|Átlag|Duration of calls made by the application to external resources.|dependency/type, dependency/performanceBucket, dependency/success, operation/synthetic, cloud/roleInstance, cloud/roleName|
|dependencies/failed|Dependency call failures|Mennyiség|Mennyiség|Count of failed dependency calls made by the application to external resources.|dependency/type, dependency/performanceBucket, operation/synthetic, cloud/roleInstance, cloud/roleName|
|pageViews/count|Page views|Mennyiség|Mennyiség|Count of page views.|operation/synthetic|
|pageViews/duration|Page view load time|MilliSeconds|Átlag|Page view load time|operation/synthetic|
|performanceCounters/requestExecutionTime|HTTP request execution time|MilliSeconds|Átlag|Execution time of the most recent request.|cloud/roleInstance|
|performanceCounters/requestsInQueue|HTTP requests in application queue|Mennyiség|Átlag|Length of the application request queue.|cloud/roleInstance|
|performanceCounters/requestsPerSecond|HTTP request rate|CountPerSecond|Átlag|Rate of all requests to the application per second from ASP.NET.|cloud/roleInstance|
|performanceCounters/exceptionsPerSecond|Exception rate|CountPerSecond|Átlag|Count of handled and unhandled exceptions reported to windows, including .NET exceptions and unmanaged exceptions that are converted into .NET exceptions.|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|Process IO rate|BytesPerSecond|Átlag|Total bytes per second read and written to files, network and devices.|cloud/roleInstance|
|performanceCounters/processCpuPercentage|Process CPU|Százalék|Átlag|The percentage of elapsed time that all process threads used the processor to execute instructions. This can vary between 0 to 100. This metric indicates the performance of w3wp process alone.|cloud/roleInstance|
|performanceCounters/processorCpuPercentage|Processor time|Százalék|Átlag|The percentage of time that the processor spends in non-idle threads.|cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|Available memory|Bytes|Átlag|Physical memory immediately available for allocation to a process or for system use.|cloud/roleInstance|
|performanceCounters/processPrivateBytes|Process private bytes|Bytes|Átlag|Memory exclusively assigned to the monitored application's processes.|cloud/roleInstance|
|requests/duration|Server response time|MilliSeconds|Átlag|Time between receiving an HTTP request and finishing sending the response.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|requests/count|Server requests|Mennyiség|Mennyiség|Count of HTTP requests completed.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|requests/failed|Sikertelen kérelmek|Mennyiség|Mennyiség|Count of HTTP requests marked as failed. In most cases these are requests with a response code >= 400 and not equal to 401.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, cloud/roleName|
|requests/rate|Server request rate|CountPerSecond|Átlag|Rate of server requests per second|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|exceptions/count|Kivételek|Mennyiség|Mennyiség|Combined count of all uncaught exceptions.|cloud/roleName, cloud/roleInstance, client/type|
|exceptions/browser|Böngészőkivételek|Mennyiség|Mennyiség|Count of uncaught exceptions thrown in the browser.|No Dimensions|
|exceptions/server|Server exceptions|Mennyiség|Mennyiség|Count of uncaught exceptions thrown in the server application.|cloud/roleName, cloud/roleInstance|
|traces/count|Traces|Mennyiség|Mennyiség|Trace document count|trace/severityLevel, operation/synthetic, cloud/roleName, cloud/roleInstance|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ServiceApiHit|Total Service Api Hits|Mennyiség|Mennyiség|Number of total service api hits|ActivityType, ActivityName|
|ServiceApiLatency|Overall Service Api Latency|Milliseconds|Átlag|Overall latency of service api requests|ActivityType, ActivityName, StatusCode|
|ServiceApiResult|Total Service Api Results|Mennyiség|Mennyiség|Number of total service api results|ActivityType, ActivityName, StatusCode|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CacheUtilization|Cache Utilization|Százalék|Átlag|Utilization level in the cluster scope|None|
|QueryDuration|Query Duration|Milliseconds|Átlag|Queries’ duration in seconds|Query Status|
|IngestionUtilization|Ingestion Utilization|Százalék|Átlag|Ratio of used ingestion slots in the cluster|None|
|KeepAlive|Keep Alive|Mennyiség|Átlag|Sanity check indicates the cluster responds to queries|None|
|IngestionVolumeInMB|Ingestion Volume (In MB)|Mennyiség|Összes|Overall volume of ingested data to the cluster (in MB)|Adatbázis|
|IngestionLatencyInSeconds|Ingestion Latency (In seconds)|másodperc|Átlag|Ingestion time from the source (e.g. message is in EventHub) to the cluster in seconds|None|
|EventProcessedForEventHubs|Events Processed (for Event Hubs)|Mennyiség|Összes|Number of events processed by the cluster when ingesting from Event Hub|None|
|IngestionResult|Ingestion Result|Mennyiség|Mennyiség|Number of ingestion operations|Állapot|
|CPU|CPU|Százalék|Átlag|CPU utilization level|None|
| ContinuousExportNumOfRecordsExported | Number of records exported in continuous export | Mennyiség | Összes | Number of records exported for every storage artifact written during the export operation  | None |
| ExportUtilization | Export Utilization | Százalék | Maximum | Export utilization | None |
| ContinuousExportPendingCount | Continuous Export Pending Count | Mennyiség | Maximum | The number of pending continuous export jobs ready for execution | None |
| ContinuousExportMaxLatenessMinutes | Continuous Export Max Lateness Minutes | Mennyiség | Maximum | The max time in minutes of all continuous exports which are pending and ready for execution | None |

## <a name="microsoftlocationbasedservicesaccounts"></a>Microsoft.LocationBasedServices/accounts

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Használat|Használat|Mennyiség|Mennyiség|Count of API calls|ApiCategory, ApiName|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|RunsStarted|Runs Started|Mennyiség|Összes|Number of workflow runs started.|No Dimensions|
|RunsCompleted|Runs Completed|Mennyiség|Összes|Number of workflow runs completed.|No Dimensions|
|RunsSucceeded|Runs Succeeded|Mennyiség|Összes|Number of workflow runs succeeded.|No Dimensions|
|RunsFailed|Runs Failed|Mennyiség|Összes|Number of workflow runs failed.|No Dimensions|
|RunsCancelled|Runs Canceled|Mennyiség|Összes|Number of workflow runs canceled.|No Dimensions|
|RunLatency|Run Latency|másodperc|Átlag|Latency of completed workflow runs.|No Dimensions|
|RunSuccessLatency|Run Success Latency|másodperc|Átlag|Latency of succeeded workflow runs.|No Dimensions|
|RunThrottledEvents|Run Throttled Events|Mennyiség|Összes|Number of workflow action or trigger throttled events.|No Dimensions|
|RunFailurePercentage|Run Failure Percentage|Százalék|Összes|Percentage of workflow runs failed.|No Dimensions|
|ActionsStarted|Actions Started |Mennyiség|Összes|Number of workflow actions started.|No Dimensions|
|ActionsCompleted|Actions Completed |Mennyiség|Összes|Number of workflow actions completed.|No Dimensions|
|ActionsSucceeded|Actions Succeeded |Mennyiség|Összes|Number of workflow actions succeeded.|No Dimensions|
|ActionsFailed|Actions Failed|Mennyiség|Összes|Number of workflow actions failed.|No Dimensions|
|ActionsSkipped|Actions Skipped |Mennyiség|Összes|Number of workflow actions skipped.|No Dimensions|
|ActionLatency|Action Latency |másodperc|Átlag|Latency of completed workflow actions.|No Dimensions|
|ActionSuccessLatency|Action Success Latency |másodperc|Átlag|Latency of succeeded workflow actions.|No Dimensions|
|ActionThrottledEvents|Action Throttled Events|Mennyiség|Összes|Number of workflow action throttled events..|No Dimensions|
|TriggersStarted|Triggers Started |Mennyiség|Összes|Number of workflow triggers started.|No Dimensions|
|TriggersCompleted|Triggers Completed |Mennyiség|Összes|Number of workflow triggers completed.|No Dimensions|
|TriggersSucceeded|Triggers Succeeded |Mennyiség|Összes|Number of workflow triggers succeeded.|No Dimensions|
|TriggersFailed|Triggers Failed |Mennyiség|Összes|Number of workflow triggers failed.|No Dimensions|
|TriggersSkipped|Triggers Skipped|Mennyiség|Összes|Number of workflow triggers skipped.|No Dimensions|
|TriggersFired|Triggers Fired |Mennyiség|Összes|Number of workflow triggers fired.|No Dimensions|
|TriggerLatency|Trigger Latency |másodperc|Átlag|Latency of completed workflow triggers.|No Dimensions|
|TriggerFireLatency|Trigger Fire Latency |másodperc|Átlag|Latency of fired workflow triggers.|No Dimensions|
|TriggerSuccessLatency|Trigger Success Latency |másodperc|Átlag|Latency of succeeded workflow triggers.|No Dimensions|
|TriggerThrottledEvents|Trigger Throttled Events|Mennyiség|Összes|Number of workflow trigger throttled events.|No Dimensions|
|BillableActionExecutions|Billable Action Executions|Mennyiség|Összes|Number of workflow action executions getting billed.|No Dimensions|
|BillableTriggerExecutions|Billable Trigger Executions|Mennyiség|Összes|Number of workflow trigger executions getting billed.|No Dimensions|
|TotalBillableExecutions|Total Billable Executions|Mennyiség|Összes|Number of workflow executions getting billed.|No Dimensions|
|BillingUsageNativeOperation|Billing Usage for Native Operation Executions|Mennyiség|Összes|Number of native operation executions getting billed.|No Dimensions|
|BillingUsageStandardConnector|Billing Usage for Standard Connector Executions|Mennyiség|Összes|Number of standard connector executions getting billed.|No Dimensions|
|BillingUsageStorageConsumption|Billing Usage for Storage Consumption Executions|Mennyiség|Összes|Number of storage consumption executions getting billed.|No Dimensions|
|BillingUsageNativeOperation|Billing Usage for Native Operation Executions|Mennyiség|Összes|Number of native operation executions getting billed.|No Dimensions|
|BillingUsageStandardConnector|Billing Usage for Standard Connector Executions|Mennyiség|Összes|Number of standard connector executions getting billed.|No Dimensions|
|BillingUsageStorageConsumption|Billing Usage for Storage Consumption Executions|Mennyiség|Összes|Number of storage consumption executions getting billed.|No Dimensions|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|RunsStarted|Runs Started|Mennyiség|Összes|Number of workflow runs started.|No Dimensions|
|RunsCompleted|Runs Completed|Mennyiség|Összes|Number of workflow runs completed.|No Dimensions|
|RunsSucceeded|Runs Succeeded|Mennyiség|Összes|Number of workflow runs succeeded.|No Dimensions|
|RunsFailed|Runs Failed|Mennyiség|Összes|Number of workflow runs failed.|No Dimensions|
|RunsCancelled|Runs Canceled|Mennyiség|Összes|Number of workflow runs canceled.|No Dimensions|
|RunLatency|Run Latency|másodperc|Átlag|Latency of completed workflow runs.|No Dimensions|
|RunSuccessLatency|Run Success Latency|másodperc|Átlag|Latency of succeeded workflow runs.|No Dimensions|
|RunThrottledEvents|Run Throttled Events|Mennyiség|Összes|Number of workflow action or trigger throttled events.|No Dimensions|
|RunStartThrottledEvents|Run Start Throttled Events|Mennyiség|Összes|Number of workflow run start throttled events.|No Dimensions|
|RunFailurePercentage|Run Failure Percentage|Százalék|Összes|Percentage of workflow runs failed.|No Dimensions|
|ActionsStarted|Actions Started |Mennyiség|Összes|Number of workflow actions started.|No Dimensions|
|ActionsCompleted|Actions Completed |Mennyiség|Összes|Number of workflow actions completed.|No Dimensions|
|ActionsSucceeded|Actions Succeeded |Mennyiség|Összes|Number of workflow actions succeeded.|No Dimensions|
|ActionsFailed|Actions Failed |Mennyiség|Összes|Number of workflow actions failed.|No Dimensions|
|ActionsSkipped|Actions Skipped |Mennyiség|Összes|Number of workflow actions skipped.|No Dimensions|
|ActionLatency|Action Latency |másodperc|Átlag|Latency of completed workflow actions.|No Dimensions|
|ActionSuccessLatency|Action Success Latency |másodperc|Átlag|Latency of succeeded workflow actions.|No Dimensions|
|ActionThrottledEvents|Action Throttled Events|Mennyiség|Összes|Number of workflow action throttled events..|No Dimensions|
|TriggersStarted|Triggers Started |Mennyiség|Összes|Number of workflow triggers started.|No Dimensions|
|TriggersCompleted|Triggers Completed |Mennyiség|Összes|Number of workflow triggers completed.|No Dimensions|
|TriggersSucceeded|Triggers Succeeded |Mennyiség|Összes|Number of workflow triggers succeeded.|No Dimensions|
|TriggersFailed|Triggers Failed |Mennyiség|Összes|Number of workflow triggers failed.|No Dimensions|
|TriggersSkipped|Triggers Skipped|Mennyiség|Összes|Number of workflow triggers skipped.|No Dimensions|
|TriggersFired|Triggers Fired |Mennyiség|Összes|Number of workflow triggers fired.|No Dimensions|
|TriggerLatency|Trigger Latency |másodperc|Átlag|Latency of completed workflow triggers.|No Dimensions|
|TriggerFireLatency|Trigger Fire Latency |másodperc|Átlag|Latency of fired workflow triggers.|No Dimensions|
|TriggerSuccessLatency|Trigger Success Latency |másodperc|Átlag|Latency of succeeded workflow triggers.|No Dimensions|
|TriggerThrottledEvents|Trigger Throttled Events|Mennyiség|Összes|Number of workflow trigger throttled events.|No Dimensions|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Workflow Processor Usage for Integration Service Environment|Százalék|Átlag|Workflow processor usage for integration service environment.|No Dimensions|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Workflow Memory Usage for Integration Service Environment|Százalék|Átlag|Workflow memory usage for integration service environment.|No Dimensions|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Connector Processor Usage for Integration Service Environment|Százalék|Átlag|Connector processor usage for integration service environment.|No Dimensions|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Connector Memory Usage for Integration Service Environment|Százalék|Átlag|Connector memory usage for integration service environment.|No Dimensions|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Completed Runs|Completed Runs|Mennyiség|Összes|Number of runs completed successfully for this workspace|Alkalmazási helyzet|
|Started Runs|Started Runs|Mennyiség|Összes|Number of runs started for this workspace|Alkalmazási helyzet|
|Failed Runs|Failed Runs|Mennyiség|Összes|Number of runs failed for this workspace|Alkalmazási helyzet|

## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Használat|Használat|Mennyiség|Mennyiség|Count of API calls|ApiCategory, ApiName, ResultType, ResponseCode|
|Elérhetőség|Elérhetőség|Százalék|Átlag|Availability of the APIs|ApiCategory, ApiName|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/Volumes

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|AverageOtherLatency|Average other latency|ms/op|Átlag|Average other latency (that is not read or write) in milliseconds per operation|No Dimensions|
|AverageReadLatency|Average read latency|ms/op|Átlag|Average read latency in milliseconds per operation|No Dimensions|
|AverageTotalLatency|Average total latency|ms/op|Átlag|Average total latency in milliseconds per operation|No Dimensions|
|AverageWriteLatency|Average write latency|ms/op|Átlag|Average write latency in milliseconds per operation|No Dimensions|
|FilesystemOtherOps|Filesystem other ops|ops|Átlag|Number of filesystem other operations (that is not read or write)|No Dimensions|
|FilesystemReadOps|Filesystem read ops|ops|Átlag|Number of filesystem read operations|No Dimensions|
|FilesystemTotalOps|Filesystem total ops|ops|Átlag|Sum of all filesystem operations|No Dimensions|
|FilesystemWriteOps|Filesystem write ops|ops|Átlag|Number of filesystem write operations|No Dimensions|
|IoBytesPerOtherOps|Io bytes per other ops|bytes/op|Átlag|Number of In/out bytes per other operations (that is not read or write)|No Dimensions|
|IoBytesPerReadOps|Io bytes per read ops|bytes/op|Átlag|Number of In/out bytes per read operation|No Dimensions|
|IoBytesPerTotalOps|Io bytes per op across all operations|bytes/op|Átlag|Sum of all In/out bytes operation|No Dimensions|
|IoBytesPerWriteOps|Io bytes per write ops|bytes/op|Átlag|Number of In/out bytes per write operation|No Dimensions|
|OtherIops|Other iops|operations/second|Átlag|Other In/out operation per second|No Dimensions|
|OtherThroughput|Other throughput|MBps|Átlag|Other throughput (that is not read or write) in megabytes per second|No Dimensions|
|ReadIops|Read iops|operations/second|Átlag|Read In/out operations per second|No Dimensions|
|ReadThroughput|Read throughput|MBps|Átlag|Read throughput in megabytes per second|No Dimensions|
|TotalIops|Total iops|operations/second|Átlag|Sum of all In/out operations per second|No Dimensions|
|TotalThroughput|Total throughput|MBps|Átlag|Sum of all throughput in megabytes per second|No Dimensions|
|VolumeAllocatedSize|Volume allocated size|bytes|Átlag|Allocated size of the volume (Not the actual used bytes)|No Dimensions|
|VolumeLogicalSize|Volume logical size|bytes|Átlag|Logical size of the volume (used bytes)|No Dimensions|
|VolumeSnapshotSize|Volume snapshot size|bytes|Átlag|Size of all snapshots in volume|No Dimensions|
|WriteIops|Write iops|operations/second|Átlag|Write In/out operations per second|No Dimensions|
|WriteThroughput|Write throughput|MBps|Átlag|Write throughput in megabytes per second|No Dimensions|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Volume pool allocated size|bytes|Átlag|Allocated size of the pool (Not the actual used bytes)|No Dimensions|
|VolumePoolAllocatedUsed|Volume pool allocated used|bytes|Átlag|Allocated used size of the pool|No Dimensions|
|VolumePoolTotalLogicalSize|Volume pool total logical size|bytes|Átlag|Sum of the logical size of all the volumes belonging to the pool|No Dimensions|
|VolumePoolTotalSnapshotSize|Volume pool total snapshot size|bytes|Átlag|Sum of all snapshots in pool|No Dimensions|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BytesSentRate|Bytes Sent|Mennyiség|Összes|Number of bytes the Network Interface sent|No Dimensions|
|BytesReceivedRate|Bytes Received|Mennyiség|Összes|Number of bytes the Network Interface received|No Dimensions|
|PacketsSentRate|Packets Sent|Mennyiség|Összes|Number of packets the Network Interface sent|No Dimensions|
|PacketsReceivedRate|Packets Received|Mennyiség|Összes|Number of packets the Network Interface received|No Dimensions|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|VipAvailability|Data Path Availability|Mennyiség|Átlag|Average Load Balancer data path availability per time duration|FrontendIPAddress, FrontendPort|
|DipAvailability|Health Probe Status|Mennyiség|Átlag|Average Load Balancer health probe status per time duration|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount|Byte Count|Mennyiség|Összes|Total number of Bytes transmitted within time period|FrontendIPAddress, FrontendPort, Direction|
|PacketCount|Packet Count|Mennyiség|Összes|Total number of Packets transmitted within time period|FrontendIPAddress, FrontendPort, Direction|
|SYNCount|SYN Count|Mennyiség|Összes|Total number of SYN Packets transmitted within time period|FrontendIPAddress, FrontendPort, Direction|
|SnatConnectionCount|SNAT Connection Count|Mennyiség|Összes|Total number of new SNAT connections created within time period|FrontendIPAddress, BackendIPAddress, ConnectionState|
|AllocatedSnatPorts|Allocated SNAT Ports (Preview)|Mennyiség|Összes|Total number of SNAT ports allocated within time period|FrontendIPAddress, BackendIPAddress, ProtocolType|
|UsedSnatPorts|Used SNAT Ports (Preview)|Mennyiség|Összes|Total number of SNAT ports used within time period|FrontendIPAddress, BackendIPAddress, ProtocolType|

## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|QueryVolume|Query Volume|Mennyiség|Összes|Number of queries served for a DNS zone|No Dimensions|
|RecordSetCount|Record Set Count|Mennyiség|Maximum|Number of Record Sets in a DNS zone|No Dimensions|
|RecordSetCapacityUtilization|Record Set Capacity Utilization|Százalék|Maximum|Percent of Record Set capacity utilized by a DNS zone|No Dimensions|

## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|PacketsInDDoS|Inbound packets DDoS|CountPerSecond|Maximum|Inbound packets DDoS|No Dimensions|
|PacketsDroppedDDoS|Inbound packets dropped DDoS|CountPerSecond|Maximum|Inbound packets dropped DDoS|No Dimensions|
|PacketsForwardedDDoS|Inbound packets forwarded DDoS|CountPerSecond|Maximum|Inbound packets forwarded DDoS|No Dimensions|
|TCPPacketsInDDoS|Inbound TCP packets DDoS|CountPerSecond|Maximum|Inbound TCP packets DDoS|No Dimensions|
|TCPPacketsDroppedDDoS|Inbound TCP packets dropped DDoS|CountPerSecond|Maximum|Inbound TCP packets dropped DDoS|No Dimensions|
|TCPPacketsForwardedDDoS|Inbound TCP packets forwarded DDoS|CountPerSecond|Maximum|Inbound TCP packets forwarded DDoS|No Dimensions|
|UDPPacketsInDDoS|Inbound UDP packets DDoS|CountPerSecond|Maximum|Inbound UDP packets DDoS|No Dimensions|
|UDPPacketsDroppedDDoS|Inbound UDP packets dropped DDoS|CountPerSecond|Maximum|Inbound UDP packets dropped DDoS|No Dimensions|
|UDPPacketsForwardedDDoS|Inbound UDP packets forwarded DDoS|CountPerSecond|Maximum|Inbound UDP packets forwarded DDoS|No Dimensions|
|BytesInDDoS|Inbound bytes DDoS|BytesPerSecond|Maximum|Inbound bytes DDoS|No Dimensions|
|BytesDroppedDDoS|Inbound bytes dropped DDoS|BytesPerSecond|Maximum|Inbound bytes dropped DDoS|No Dimensions|
|BytesForwardedDDoS|Inbound bytes forwarded DDoS|BytesPerSecond|Maximum|Inbound bytes forwarded DDoS|No Dimensions|
|TCPBytesInDDoS|Inbound TCP bytes DDoS|BytesPerSecond|Maximum|Inbound TCP bytes DDoS|No Dimensions|
|TCPBytesDroppedDDoS|Inbound TCP bytes dropped DDoS|BytesPerSecond|Maximum|Inbound TCP bytes dropped DDoS|No Dimensions|
|TCPBytesForwardedDDoS|Inbound TCP bytes forwarded DDoS|BytesPerSecond|Maximum|Inbound TCP bytes forwarded DDoS|No Dimensions|
|UDPBytesInDDoS|Inbound UDP bytes DDoS|BytesPerSecond|Maximum|Inbound UDP bytes DDoS|No Dimensions|
|UDPBytesDroppedDDoS|Inbound UDP bytes dropped DDoS|BytesPerSecond|Maximum|Inbound UDP bytes dropped DDoS|No Dimensions|
|UDPBytesForwardedDDoS|Inbound UDP bytes forwarded DDoS|BytesPerSecond|Maximum|Inbound UDP bytes forwarded DDoS|No Dimensions|
|IfUnderDDoSAttack|Under DDoS attack or not|Mennyiség|Maximum|Under DDoS attack or not|No Dimensions|
|DDoSTriggerTCPPackets|Inbound TCP packets to trigger DDoS mitigation|CountPerSecond|Maximum|Inbound TCP packets to trigger DDoS mitigation|No Dimensions|
|DDoSTriggerUDPPackets|Inbound UDP packets to trigger DDoS mitigation|CountPerSecond|Maximum|Inbound UDP packets to trigger DDoS mitigation|No Dimensions|
|DDoSTriggerSYNPackets|Inbound SYN packets to trigger DDoS mitigation|CountPerSecond|Maximum|Inbound SYN packets to trigger DDoS mitigation|No Dimensions|
|VipAvailability|Data Path Availability|Mennyiség|Átlag|Average IP Address availability per time duration|Port|
|ByteCount|Byte Count|Mennyiség|Összes|Total number of Bytes transmitted within time period|Port, Direction|
|PacketCount|Packet Count|Mennyiség|Összes|Total number of Packets transmitted within time period|Port, Direction|
|SynCount|SYN Count|Mennyiség|Összes|Total number of SYN Packets transmitted within time period|Port, Direction|

## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ApplicationRuleHit|Application rules hit count|Mennyiség|Összes|Number of times Application rules were hit|Status, Reason, Protocol|
|NetworkRuleHit|Network rules hit count|Mennyiség|Összes|Number of times Network rules were hit|Status, Reason, Protocol|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Adatforgalom|Adatforgalom|BytesPerSecond|Összes|Number of bytes per second the Application Gateway has served|No Dimensions|
|UnhealthyHostCount|Unhealthy Host Count|Mennyiség|Átlag|Number of unhealthy backend hosts|BackendSettingsPool|
|HealthyHostCount|Healthy Host Count|Mennyiség|Átlag|Number of healthy backend hosts|BackendSettingsPool|
|TotalRequests|Összes kérelem|Mennyiség|Összes|Count of successful requests that Application Gateway has served|BackendSettingsPool|
|FailedRequests|Sikertelen kérelmek|Mennyiség|Összes|Count of failed requests that Application Gateway has served|BackendSettingsPool|
|ResponseStatus|Response Status|Mennyiség|Összes|Http response status returned by Application Gateway|HttpStatusGroup|
|CurrentConnections|Current Connections|Mennyiség|Összes|Count of current connections established with Application Gateway|No Dimensions|
|CapacityUnits|Current Capacity Units|Mennyiség|Átlag|Capacity Units consumed|No Dimensions|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|AverageBandwidth|Gateway S2S Bandwidth|BytesPerSecond|Átlag|Average site-to-site bandwidth of a gateway in bytes per second|No Dimensions|
|P2SBandwidth|Gateway P2S Bandwidth|BytesPerSecond|Átlag|Average point-to-site bandwidth of a gateway in bytes per second|No Dimensions|
|P2SConnectionCount|P2S Connection Count|Mennyiség|Maximum|Point-to-site connection count of a gateway|Protocol (Protokoll)|
|TunnelAverageBandwidth|Tunnel Bandwidth|BytesPerSecond|Átlag|Average bandwidth of a tunnel in bytes per second|ConnectionName, RemoteIP|
|TunnelEgressBytes|Tunnel Egress Bytes|Bytes|Összes|Outgoing bytes of a tunnel|ConnectionName, RemoteIP|
|TunnelIngressBytes|Tunnel Ingress Bytes|Bytes|Összes|Incoming bytes of a tunnel|ConnectionName, RemoteIP|
|TunnelEgressPackets|Tunnel Egress Packets|Mennyiség|Összes|Outgoing packet count of a tunnel|ConnectionName, RemoteIP|
|TunnelIngressPackets|Tunnel Ingress Packets|Mennyiség|Összes|Incoming packet count of a tunnel|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Tunnel Egress TS Mismatch Packet Drop|Mennyiség|Összes|Outgoing packet drop count from traffic selector mismatch of a tunnel|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Tunnel Ingress TS Mismatch Packet Drop|Mennyiség|Összes|Incoming packet drop count from traffic selector mismatch of a tunnel|ConnectionName, RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Átlag|Bits ingressing Azure per second|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Átlag|Bits egressing Azure per second|PeeringType|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Átlag|Bits ingressing Azure per second|No Dimensions|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Átlag|Bits egressing Azure per second|No Dimensions|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Átlag|Bits ingressing Azure per second|No Dimensions|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Átlag|Bits egressing Azure per second|No Dimensions|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|QpsByEndpoint|Queries by Endpoint Returned|Mennyiség|Összes|Number of times a Traffic Manager endpoint was returned in the given time frame|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Endpoint Status by Endpoint|Mennyiség|Maximum|1 if an endpoint's probe status is "Enabled", 0 otherwise.|EndpointName|

## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ProbesFailedPercent|% Probes Failed|Százalék|Átlag|% of connectivity monitoring probes failed|No Dimensions|
|AverageRoundtripMs|Avg. Round-trip Time (ms)|MilliSeconds|Átlag|Average network round-trip time (ms) for connectivity monitoring probes sent between source and destination|No Dimensions|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|RequestCount|Kérelmek száma|Mennyiség|Összes|The number of client requests served by the HTTP/S proxy|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Request Size|Bytes|Összes|The number of bytes sent as requests from clients to the HTTP/S proxy|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Response Size|Bytes|Összes|The number of bytes sent as responses from HTTP/S proxy to clients|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendRequestCount|Backend Request Count|Mennyiség|Összes|The number of requests sent from the HTTP/S proxy to backends|HttpStatus, HttpStatusGroup, Backend|
|BackendRequestLatency|Backend Request Latency|MilliSeconds|Átlag|The time calculated from when the request was sent by the HTTP/S proxy to the backend until the HTTP/S proxy received the last response byte from the backend|Backend|
|TotalLatency|Total Latency|MilliSeconds|Átlag|The time calculated from when the client request was received by the HTTP/S proxy until the client acknowledged the last response byte from the HTTP/S proxy|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendHealthPercentage|Backend Health Percentage|Százalék|Átlag|The percentage of successful health probes from the HTTP/S proxy to backends|Backend, BackendPool|
|WebApplicationFirewallRequestCount|Web Application Firewall Request Count|Mennyiség|Összes|The number of client requests processed by the Web Application Firewall|PolicyName, RuleName, Action|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|registration.all|Registration Operations|Mennyiség|Összes|The count of all successful registration operations (creations updates queries and deletions). |No Dimensions|
|registration.create|Registration Create Operations|Mennyiség|Összes|The count of all successful registration creations.|No Dimensions|
|registration.update|Registration Update Operations|Mennyiség|Összes|The count of all successful registration updates.|No Dimensions|
|registration.get|Registration Read Operations|Mennyiség|Összes|The count of all successful registration queries.|No Dimensions|
|registration.delete|Registration Delete Operations|Mennyiség|Összes|The count of all successful registration deletions.|No Dimensions|
|incoming|Incoming Messages|Mennyiség|Összes|The count of all successful send API calls. |No Dimensions|
|incoming.scheduled|Scheduled Push Notifications Sent|Mennyiség|Összes|Scheduled Push Notifications Canceled|No Dimensions|
|incoming.scheduled.cancel|Scheduled Push Notifications Canceled|Mennyiség|Összes|Scheduled Push Notifications Canceled|No Dimensions|
|scheduled.pending|Pending Scheduled Notifications|Mennyiség|Összes|Pending Scheduled Notifications|No Dimensions|
|installation.all|Installation Management Operations|Mennyiség|Összes|Installation Management Operations|No Dimensions|
|installation.get|Get Installation Operations|Mennyiség|Összes|Get Installation Operations|No Dimensions|
|installation.upsert|Create or Update Installation Operations|Mennyiség|Összes|Create or Update Installation Operations|No Dimensions|
|installation.patch|Patch Installation Operations|Mennyiség|Összes|Patch Installation Operations|No Dimensions|
|installation.delete|Delete Installation Operations|Mennyiség|Összes|Delete Installation Operations|No Dimensions|
|outgoing.allpns.success|Successful notifications|Mennyiség|Összes|The count of all successful notifications.|No Dimensions|
|outgoing.allpns.invalidpayload|Payload Errors|Mennyiség|Összes|The count of pushes that failed because the PNS returned a bad payload error.|No Dimensions|
|outgoing.allpns.pnserror|External Notification System Errors|Mennyiség|Összes|The count of pushes that failed because there was a problem communicating with the PNS (excludes authentication problems).|No Dimensions|
|outgoing.allpns.channelerror|Channel Errors|Mennyiség|Összes|The count of pushes that failed because the channel was invalid not associated with the correct app throttled or expired.|No Dimensions|
|outgoing.allpns.badorexpiredchannel|Bad or Expired Channel Errors|Mennyiség|Összes|The count of pushes that failed because the channel/token/registrationId in the registration was expired or invalid.|No Dimensions|
|outgoing.wns.success|WNS Successful Notifications|Mennyiség|Összes|The count of all successful notifications.|No Dimensions|
|outgoing.wns.invalidcredentials|WNS Authorization Errors (Invalid Credentials)|Mennyiség|Összes|The count of pushes that failed because the PNS did not accept the provided credentials or the credentials are blocked. (Windows Live does not recognize the credentials).|No Dimensions|
|outgoing.wns.badchannel|WNS Bad Channel Error|Mennyiség|Összes|The count of pushes that failed because the ChannelURI in the registration was not recognized (WNS status: 404 not found).|No Dimensions|
|outgoing.wns.expiredchannel|WNS Expired Channel Error|Mennyiség|Összes|The count of pushes that failed because the ChannelURI is expired (WNS status: 410 Gone).|No Dimensions|
|outgoing.wns.throttled|WNS Throttled Notifications|Mennyiség|Összes|The count of pushes that failed because WNS is throttling this app (WNS status: 406 Not Acceptable).|No Dimensions|
|outgoing.wns.tokenproviderunreachable|WNS Authorization Errors (Unreachable)|Mennyiség|Összes|Windows Live is not reachable.|No Dimensions|
|outgoing.wns.invalidtoken|WNS Authorization Errors (Invalid Token)|Mennyiség|Összes|The token provided to WNS is not valid (WNS status: 401 Unauthorized).|No Dimensions|
|outgoing.wns.wrongtoken|WNS Authorization Errors (Wrong Token)|Mennyiség|Összes|The token provided to WNS is valid but for another application (WNS status: 403 Forbidden). This can happen if the ChannelURI in the registration is associated with another app. Check that the client app is associated with the same app whose credentials are in the notification hub.|No Dimensions|
|outgoing.wns.invalidnotificationformat|WNS Invalid Notification Format|Mennyiség|Összes|The format of the notification is invalid (WNS status: 400). Note that WNS does not reject all invalid payloads.|No Dimensions|
|outgoing.wns.invalidnotificationsize|WNS Invalid Notification Size Error|Mennyiség|Összes|The notification payload is too large (WNS status: 413).|No Dimensions|
|outgoing.wns.channelthrottled|WNS Channel Throttled|Mennyiség|Összes|The notification was dropped because the ChannelURI in the registration is throttled (WNS response header: X-WNS-NotificationStatus:channelThrottled).|No Dimensions|
|outgoing.wns.channeldisconnected|WNS Channel Disconnected|Mennyiség|Összes|The notification was dropped because the ChannelURI in the registration is throttled (WNS response header: X-WNS-DeviceConnectionStatus: disconnected).|No Dimensions|
|outgoing.wns.dropped|WNS Dropped Notifications|Mennyiség|Összes|The notification was dropped because the ChannelURI in the registration is throttled (X-WNS-NotificationStatus: dropped but not X-WNS-DeviceConnectionStatus: disconnected).|No Dimensions|
|outgoing.wns.pnserror|WNS Errors|Mennyiség|Összes|Notification not delivered because of errors communicating with WNS.|No Dimensions|
|outgoing.wns.authenticationerror|WNS Authentication Errors|Mennyiség|Összes|Notification not delivered because of errors communicating with Windows Live invalid credentials or wrong token.|No Dimensions|
|outgoing.apns.success|APNS Successful Notifications|Mennyiség|Összes|The count of all successful notifications.|No Dimensions|
|outgoing.apns.invalidcredentials|APNS Authorization Errors|Mennyiség|Összes|The count of pushes that failed because the PNS did not accept the provided credentials or the credentials are blocked.|No Dimensions|
|outgoing.apns.badchannel|APNS Bad Channel Error|Mennyiség|Összes|The count of pushes that failed because the token is invalid (APNS binary protocol status code: 8. APNS HTTP protocol status code: 400 with "BadDeviceToken").|No Dimensions|
|outgoing.apns.expiredchannel|APNS Expired Channel Error|Mennyiség|Összes|The count of token that were invalidated by the APNS feedback channel.|No Dimensions|
|outgoing.apns.invalidnotificationsize|APNS Invalid Notification Size Error|Mennyiség|Összes|The count of pushes that failed because the payload was too large (APNS binary protocol status code: 7).|No Dimensions|
|outgoing.apns.pnserror|APNS Errors|Mennyiség|Összes|The count of pushes that failed because of errors communicating with APNS.|No Dimensions|
|outgoing.gcm.success|GCM Successful Notifications|Mennyiség|Összes|The count of all successful notifications.|No Dimensions|
|outgoing.gcm.invalidcredentials|GCM Authorization Errors (Invalid Credentials)|Mennyiség|Összes|The count of pushes that failed because the PNS did not accept the provided credentials or the credentials are blocked.|No Dimensions|
|outgoing.gcm.badchannel|GCM Bad Channel Error|Mennyiség|Összes|The count of pushes that failed because the registrationId in the registration was not recognized (GCM result: Invalid Registration).|No Dimensions|
|outgoing.gcm.expiredchannel|GCM Expired Channel Error|Mennyiség|Összes|The count of pushes that failed because the registrationId in the registration was expired (GCM result: NotRegistered).|No Dimensions|
|outgoing.gcm.throttled|GCM Throttled Notifications|Mennyiség|Összes|The count of pushes that failed because GCM throttled this app (GCM status code: 501-599 or result:Unavailable).|No Dimensions|
|outgoing.gcm.invalidnotificationformat|GCM Invalid Notification Format|Mennyiség|Összes|The count of pushes that failed because the payload was not formatted correctly (GCM result: InvalidDataKey or InvalidTtl).|No Dimensions|
|outgoing.gcm.invalidnotificationsize|GCM Invalid Notification Size Error|Mennyiség|Összes|The count of pushes that failed because the payload was too large (GCM result: MessageTooBig).|No Dimensions|
|outgoing.gcm.wrongchannel|GCM Wrong Channel Error|Mennyiség|Összes|The count of pushes that failed because the registrationId in the registration is not associated to the current app (GCM result: InvalidPackageName).|No Dimensions|
|outgoing.gcm.pnserror|GCM Errors|Mennyiség|Összes|The count of pushes that failed because of errors communicating with GCM.|No Dimensions|
|outgoing.gcm.authenticationerror|GCM Authentication Errors|Mennyiség|Összes|The count of pushes that failed because the PNS did not accept the provided credentials the credentials are blocked or the SenderId is not correctly configured in the app (GCM result: MismatchedSenderId).|No Dimensions|
|outgoing.mpns.success|MPNS Successful Notifications|Mennyiség|Összes|The count of all successful notifications.|No Dimensions|
|outgoing.mpns.invalidcredentials|MPNS Invalid Credentials|Mennyiség|Összes|The count of pushes that failed because the PNS did not accept the provided credentials or the credentials are blocked.|No Dimensions|
|outgoing.mpns.badchannel|MPNS Bad Channel Error|Mennyiség|Összes|The count of pushes that failed because the ChannelURI in the registration was not recognized (MPNS status: 404 not found).|No Dimensions|
|outgoing.mpns.throttled|MPNS Throttled Notifications|Mennyiség|Összes|The count of pushes that failed because MPNS is throttling this app (WNS MPNS: 406 Not Acceptable).|No Dimensions|
|outgoing.mpns.invalidnotificationformat|MPNS Invalid Notification Format|Mennyiség|Összes|The count of pushes that failed because the payload of the notification was too large.|No Dimensions|
|outgoing.mpns.channeldisconnected|MPNS Channel Disconnected|Mennyiség|Összes|The count of pushes that failed because the ChannelURI in the registration was disconnected (MPNS status: 412 not found).|No Dimensions|
|outgoing.mpns.dropped|MPNS Dropped Notifications|Mennyiség|Összes|The count of pushes that were dropped by MPNS (MPNS response header: X-NotificationStatus: QueueFull or Suppressed).|No Dimensions|
|outgoing.mpns.pnserror|MPNS Errors|Mennyiség|Összes|The count of pushes that failed because of errors communicating with MPNS.|No Dimensions|
|outgoing.mpns.authenticationerror|MPNS Authentication Errors|Mennyiség|Összes|The count of pushes that failed because the PNS did not accept the provided credentials or the credentials are blocked.|No Dimensions|
|notificationhub.pushes|All Outgoing Notifications|Mennyiség|Összes|All outgoing notifications of the notification hub|No Dimensions|
|incoming.all.requests|All Incoming Requests|Mennyiség|Összes|Total incoming requests for a notification hub|No Dimensions|
|incoming.all.failedrequests|All Incoming Failed Requests|Mennyiség|Összes|Total incoming failed requests for a notification hub|No Dimensions|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Average_% Free Inodes|% Free Inodes|Mennyiség|Átlag|Average_% Free Inodes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Free Space|% Free Space|Mennyiség|Átlag|Average_% Free Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Inodes|% Used Inodes|Mennyiség|Átlag|Average_% Used Inodes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Space|% Used Space|Mennyiség|Átlag|Average_% Used Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Read Bytes/sec|Disk Read Bytes/sec|Mennyiség|Átlag|Average_Disk Read Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Reads/sec|Disk Reads/sec|Mennyiség|Átlag|Average_Disk Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Transfers/sec|Disk Transfers/sec|Mennyiség|Átlag|Average_Disk Transfers/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Write Bytes/sec|Disk Write Bytes/sec|Mennyiség|Átlag|Average_Disk Write Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Writes/sec|Disk Writes/sec|Mennyiség|Átlag|Average_Disk Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Megabytes|Free Megabytes|Mennyiség|Átlag|Average_Free Megabytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Logical Disk Bytes/sec|Logical Disk Bytes/sec|Mennyiség|Átlag|Average_Logical Disk Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Available Memory|% Available Memory|Mennyiség|Átlag|Average_% Available Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Available Swap Space|% Available Swap Space|Mennyiség|Átlag|Average_% Available Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Memory|% Used Memory|Mennyiség|Átlag|Average_% Used Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Swap Space|% Used Swap Space|Mennyiség|Átlag|Average_% Used Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes Memory|Available MBytes Memory|Mennyiség|Átlag|Average_Available MBytes Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes Swap|Available MBytes Swap|Mennyiség|Átlag|Average_Available MBytes Swap|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page Reads/sec|Page Reads/sec|Mennyiség|Átlag|Average_Page Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page Writes/sec|Page Writes/sec|Mennyiség|Átlag|Average_Page Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages/sec|Pages/sec|Mennyiség|Átlag|Average_Pages/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used MBytes Swap Space|Used MBytes Swap Space|Mennyiség|Átlag|Average_Used MBytes Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used Memory MBytes|Used Memory MBytes|Mennyiség|Átlag|Average_Used Memory MBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes Transmitted|Total Bytes Transmitted|Mennyiség|Átlag|Average_Total Bytes Transmitted|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes Received|Total Bytes Received|Mennyiség|Átlag|Average_Total Bytes Received|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes|Total Bytes|Mennyiség|Átlag|Average_Total Bytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Packets Transmitted|Total Packets Transmitted|Mennyiség|Átlag|Average_Total Packets Transmitted|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Packets Received|Total Packets Received|Mennyiség|Átlag|Average_Total Packets Received|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Rx Errors|Total Rx Errors|Mennyiség|Átlag|Average_Total Rx Errors|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Tx Errors|Total Tx Errors|Mennyiség|Átlag|Average_Total Tx Errors|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Collisions|Total Collisions|Mennyiség|Átlag|Average_Total Collisions|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Read|Avg. Disk sec/Read|Mennyiség|Átlag|Average_Avg. Disk sec/Read|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Transfer|Avg. Disk sec/Transfer|Mennyiség|Átlag|Average_Avg. Disk sec/Transfer|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Write|Avg. Disk sec/Write|Mennyiség|Átlag|Average_Avg. Disk sec/Write|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Physical Disk Bytes/sec|Physical Disk Bytes/sec|Mennyiség|Átlag|Average_Physical Disk Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct Privileged Time|Pct Privileged Time|Mennyiség|Átlag|Average_Pct Privileged Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct User Time|Pct User Time|Mennyiség|Átlag|Average_Pct User Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used Memory kBytes|Used Memory kBytes|Mennyiség|Átlag|Average_Used Memory kBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Virtual Shared Memory|Virtual Shared Memory|Mennyiség|Átlag|Average_Virtual Shared Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% DPC Time|% DPC Time|Mennyiség|Átlag|Average_% DPC Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Idle Time|% Idle Time|Mennyiség|Átlag|Average_% Idle Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Interrupt Time|% Interrupt Time|Mennyiség|Átlag|Average_% Interrupt Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% IO Wait Time|% IO Wait Time|Mennyiség|Átlag|Average_% IO Wait Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Nice Time|% Nice Time|Mennyiség|Átlag|Average_% Nice Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Privileged Time|% Privileged Time|Mennyiség|Átlag|Average_% Privileged Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Processor Time|% Processor Time|Mennyiség|Átlag|Average_% Processor Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% User Time|% User Time|Mennyiség|Átlag|Average_% User Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Physical Memory|Free Physical Memory|Mennyiség|Átlag|Average_Free Physical Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Space in Paging Files|Free Space in Paging Files|Mennyiség|Átlag|Average_Free Space in Paging Files|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Virtual Memory|Free Virtual Memory|Mennyiség|Átlag|Average_Free Virtual Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|Folyamatok|Mennyiség|Átlag|Average_Processes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Size Stored In Paging Files|Size Stored In Paging Files|Mennyiség|Átlag|Average_Size Stored In Paging Files|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|Uptime|Mennyiség|Átlag|Average_Uptime|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|Felhasználók|Mennyiség|Átlag|Average_Users|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Read|Avg. Disk sec/Read|Mennyiség|Átlag|Average_Avg. Disk sec/Read|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Write|Avg. Disk sec/Write|Mennyiség|Átlag|Average_Avg. Disk sec/Write|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Current Disk Queue Length|Current Disk Queue Length|Mennyiség|Átlag|Average_Current Disk Queue Length|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Reads/sec|Disk Reads/sec|Mennyiség|Átlag|Average_Disk Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Transfers/sec|Disk Transfers/sec|Mennyiség|Átlag|Average_Disk Transfers/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Writes/sec|Disk Writes/sec|Mennyiség|Átlag|Average_Disk Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Megabytes|Free Megabytes|Mennyiség|Átlag|Average_Free Megabytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Free Space|% Free Space|Mennyiség|Átlag|Average_% Free Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes|Available MBytes|Mennyiség|Átlag|Average_Available MBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Committed Bytes In Use|% Committed Bytes In Use|Mennyiség|Átlag|Average_% Committed Bytes In Use|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Received/sec|Bytes Received/sec|Mennyiség|Átlag|Average_Bytes Received/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Sent/sec|Bytes Sent/sec|Mennyiség|Átlag|Average_Bytes Sent/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Total/sec|Bytes Total/sec|Mennyiség|Átlag|Average_Bytes Total/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Processor Time|% Processor Time|Mennyiség|Átlag|Average_% Processor Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processor Queue Length|Processor Queue Length|Mennyiség|Átlag|Average_Processor Queue Length|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Heartbeat|Heartbeat|Mennyiség|Összes|Heartbeat|Computer, OSType, Version, SourceComputerId|
|Frissítés|Frissítés|Mennyiség|Átlag|Frissítés|Computer, Product, Classification, UpdateState, Optional, Approved|
|Esemény|Esemény|Mennyiség|Átlag|Esemény|Source, EventLog, Computer, EventCategory, EventLevel, EventLevelName, EventID|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|QueryDuration|Query Duration|Milliseconds|Átlag|DAX Query duration in last interval|No Dimensions|
|QueryPoolJobQueueLength|Threads: Query pool job queue length|Mennyiség|Átlag|Number of jobs in the queue of the query thread pool.|No Dimensions|
|qpu_high_utilization_metric|QPU High Utilization|Mennyiség|Összes|QPU High Utilization In Last Minute, 1 For High QPU Utilization, Otherwise 0|No Dimensions|
|memory_metric|Memória|Bytes|Átlag|Memory. Range 0-3 GB for A1, 0-5 GB for A2, 0-10 GB for A3, 0-25 GB for A4, 0-50 GB for A5 and 0-100 GB for A6|No Dimensions|
|memory_thrashing_metric|Memory Thrashing|Százalék|Átlag|Average memory thrashing.|No Dimensions|

## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ListenerConnections-Success|ListenerConnections-Success|Mennyiség|Összes|Successful ListenerConnections for Microsoft.Relay.|EntityName|
|ListenerConnections-ClientError|ListenerConnections-ClientError|Mennyiség|Összes|ClientError on ListenerConnections for Microsoft.Relay.|EntityName|
|ListenerConnections-ServerError|ListenerConnections-ServerError|Mennyiség|Összes|ServerError on ListenerConnections for Microsoft.Relay.|EntityName|
|SenderConnections-Success|SenderConnections-Success|Mennyiség|Összes|Successful SenderConnections for Microsoft.Relay.|EntityName|
|SenderConnections-ClientError|SenderConnections-ClientError|Mennyiség|Összes|ClientError on SenderConnections for Microsoft.Relay.|EntityName|
|SenderConnections-ServerError|SenderConnections-ServerError|Mennyiség|Összes|ServerError on SenderConnections for Microsoft.Relay.|EntityName|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|Mennyiség|Összes|Total ListenerConnections for Microsoft.Relay.|EntityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|Mennyiség|Összes|Total SenderConnections requests for Microsoft.Relay.|EntityName|
|ActiveConnections|ActiveConnections|Mennyiség|Összes|Total ActiveConnections for Microsoft.Relay.|EntityName|
|ActiveListeners|ActiveListeners|Mennyiség|Összes|Total ActiveListeners for Microsoft.Relay.|EntityName|
|BytesTransferred|BytesTransferred|Mennyiség|Összes|Total BytesTransferred for Microsoft.Relay.|EntityName|
|ListenerDisconnects|ListenerDisconnects|Mennyiség|Összes|Total ListenerDisconnects for Microsoft.Relay.|EntityName|
|SenderDisconnects|SenderDisconnects|Mennyiség|Összes|Total SenderDisconnects for Microsoft.Relay.|EntityName|

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|SearchLatency|Search Latency|másodperc|Átlag|Average search latency for the search service|No Dimensions|
|SearchQueriesPerSecond|Search queries per second|CountPerSecond|Átlag|Search queries per second for the search service|No Dimensions|
|ThrottledSearchQueriesPercentage|Throttled search queries percentage|Százalék|Átlag|Percentage of search queries that were throttled for the search service|No Dimensions|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|SuccessfulRequests|Successful Requests (Preview)|Mennyiség|Összes|Total successful requests for a namespace (Preview)|EntityName|
|ServerErrors|Server Errors. (Előzetes verzió)|Mennyiség|Összes|Server Errors for Microsoft.ServiceBus. (Előzetes verzió)|EntityName|
|UserErrors|User Errors. (Előzetes verzió)|Mennyiség|Összes|User Errors for Microsoft.ServiceBus. (Előzetes verzió)|EntityName|
|ThrottledRequests|Throttled Requests. (Előzetes verzió)|Mennyiség|Összes|Throttled Requests for Microsoft.ServiceBus. (Előzetes verzió)|EntityName|
|IncomingRequests|Incoming Requests (Preview)|Mennyiség|Összes|Incoming Requests for Microsoft.ServiceBus. (Előzetes verzió)|EntityName|
|IncomingMessages|Incoming Messages (Preview)|Mennyiség|Összes|Incoming Messages for Microsoft.ServiceBus. (Előzetes verzió)|EntityName|
|OutgoingMessages|Outgoing Messages (Preview)|Mennyiség|Összes|Outgoing Messages for Microsoft.ServiceBus. (Előzetes verzió)|EntityName|
|ActiveConnections|ActiveConnections (Preview)|Mennyiség|Összes|Total Active Connections for Microsoft.ServiceBus. (Előzetes verzió)|No Dimensions|
|Méret|Size (Preview)|Bytes|Átlag|Size of an Queue/Topic in Bytes. (Előzetes verzió)|EntityName|
|Üzenetek|Count of messages in a Queue/Topic. (Előzetes verzió)|Mennyiség|Átlag|Count of messages in a Queue/Topic. (Előzetes verzió)|EntityName|
|ActiveMessages|Count of active messages in a Queue/Topic. (Előzetes verzió)|Mennyiség|Átlag|Count of active messages in a Queue/Topic. (Előzetes verzió)|EntityName|
|DeadletteredMessages|Count of dead-lettered messages in a Queue/Topic. (Előzetes verzió)|Mennyiség|Átlag|Count of dead-lettered messages in a Queue/Topic. (Előzetes verzió)|EntityName|
|ScheduledMessages|Count of scheduled messages in a Queue/Topic. (Előzetes verzió)|Mennyiség|Átlag|Count of scheduled messages in a Queue/Topic. (Előzetes verzió)|EntityName|
|CPUXNS|CPU usage per namespace|Százalék|Maximum|Service bus premium namespace CPU usage metric|No Dimensions|
|WSXNS|Memory size usage per namespace|Százalék|Maximum|Service bus premium namespace memory usage metric|No Dimensions|

## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|Mennyiség|Átlag|Cpu allocated to this container in millicores|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|AllocatedMemory|AllocatedMemory|Bytes|Átlag|Memory allocated to this container in MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualCpu|ActualCpu|Mennyiség|Átlag|Actual CPU usage in millicores|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualMemory|ActualMemory|Bytes|Átlag|Actual memory usage in MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|CpuUtilization|CpuUtilization|Százalék|Átlag|Utilization of CPU for this container as percentage of AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|MemoryUtilization|MemoryUtilization|Százalék|Átlag|Utilization of CPU for this container as percentage of AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ApplicationStatus|ApplicationStatus|Mennyiség|Átlag|Status of Service Fabric Mesh application|ApplicationName, Status|
|ServiceStatus|ServiceStatus|Mennyiség|Átlag|Health Status of a service in Service Fabric Mesh application|ApplicationName, Status, ServiceName|
|ServiceReplicaStatus|ServiceReplicaStatus|Mennyiség|Átlag|Health Status of a service replica in Service Fabric Mesh application|ApplicationName, Status, ServiceName, ServiceReplicaName|
|ContainerStatus|ContainerStatus|Mennyiség|Átlag|Status of the container in Service Fabric Mesh application|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName, Status|
|RestartCount|RestartCount|Mennyiség|Átlag|Restart count of a container in Service Fabric Mesh application|ApplicationName, Status, ServiceName, ServiceReplicaName, CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ConnectionCount|Connection Count|Mennyiség|Maximum|The amount of user connection.|Végpont|
|MessageCount|Message Count|Mennyiség|Összes|The total amount of messages.|No Dimensions|
|InboundTraffic|Bejövő forgalom|Bytes|Összes|The inbound traffic of service|No Dimensions|
|OutboundTraffic|Kimenő forgalom|Bytes|Összes|The outbound traffic of service|No Dimensions|
|UserErrors|User Errors|Százalék|Maximum|The percentage of user errors|No Dimensions|
|SystemErrors|System Errors|Százalék|Maximum|The percentage of system errors|No Dimensions|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|allocated_data_storage|Data space allocated|Bytes|Átlag|Data space allocated. Not applicable to data warehouses.|No Dimensions|
|app_cpu_billed|App CPU billed|Mennyiség|Összes|App CPU billed. Applies to serverless databases.|No Dimensions|
|app_cpu_percent|App CPU percentage|Százalék|Átlag|App CPU percentage. Applies to serverless databases.|No Dimensions|
|app_memory_percent|App memory used percentage|Százalék|Átlag|App memory used percentage. Applies to serverless databases.|No Dimensions|
|blocked_by_firewall|Blocked by Firewall|Mennyiség|Összes|Blocked by Firewall|No Dimensions|
|cache_hit_percent|Cache hit percentage|Százalék|Maximum|Cache hit percentage. Applies only to data warehouses.|No Dimensions|
|cache_used_percent|Cache used percentage|Százalék|Maximum|Cache used percentage. Applies only to data warehouses.|No Dimensions|
|connection_failed|Sikertelen kapcsolatok|Mennyiség|Összes|Sikertelen kapcsolatok|No Dimensions|
|connection_successful|Successful Connections|Mennyiség|Összes|Successful Connections|No Dimensions|
|cpu_percent|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|No Dimensions|
|cpu_limit|CPU limit|Mennyiség|Átlag|CPU limit. Applies to vCore-based databases.|No Dimensions|
|cpu_used|CPU used|Mennyiség|Átlag|CPU used. Applies to vCore-based databases.|No Dimensions|
|deadlock|Deadlocks|Mennyiség|Összes|Deadlocks. Not applicable to data warehouses.|No Dimensions|
|diff_backup_size_bytes|Differential backup storage size|Bytes|Maximum|Cumulative differential backup storage size. Applies to General Purpose and Business Critical databases. Does not currently apply to Manage instance databases.|No Dimensions|
|dtu_limit|DTU Limit|Mennyiség|Átlag|DTU Limit. Applies to DTU-based databases.|No Dimensions|
|dtu_consumption_percent|DTU-kihasználtság (%)|Százalék|Átlag|DTU percentage. Applies to DTU-based databases.|No Dimensions|
|dtu_used|DTU used|Mennyiség|Átlag|DTU used. Applies to DTU-based databases.|No Dimensions|
|dw_cpu_percent|DW node level CPU percentage|Százalék|Átlag|DW node level CPU percentage|DwLogicalNodeId|
|dw_physical_data_read_percent|DW node level Data IO percentage|Százalék|Átlag|DW node level Data IO percentage|DwLogicalNodeId|
|dwu_consumption_percent|DWU percentage|Százalék|Maximum|DWU percentage. Applies only to data warehouses.|No Dimensions|
|dwu_limit|DWU limit|Mennyiség|Maximum|DWU limit. Applies only to data warehouses.|No Dimensions|
|dwu_used|DWU used|Mennyiség|Maximum|DWU used. Applies only to data warehouses.|No Dimensions|
|full_backup_size_bytes|Full backup storage size|Bytes|Maximum|Cumulative full backup storage size. Applies to General Purpose and Business Critical databases. Does not currently apply to Manage instance databases.|No Dimensions|
|local_tempdb_usage_percent|Local tempdb percentage|Százalék|Átlag|Local tempdb percentage. Applies only to data warehouses.|No Dimensions|
|log_backup_size_bytes|Log backup storage size|Bytes|Maximum|Cumulative log backup storage size. Applies to General Purpose and Business Critical databases. Does not currently apply to Manage instance databases.|No Dimensions|
|log_write_percent|Log IO percentage|Százalék|Átlag|Log IO percentage. Not applicable to data warehouses.|No Dimensions|
|physical_data_read_percent|Adat IO kihasználtsága (%)|Százalék|Átlag|Adat IO kihasználtsága (%)|No Dimensions|
|sessions_percent|Sessions percentage|Százalék|Átlag|Sessions percentage. Not applicable to data warehouses.|No Dimensions|
|sqlserver_process_core_percent|SQL Server process core percent|Százalék|Maximum|This metric is a placeholder and not populated at this time.|No Dimensions|
|sqlserver_process_memory_percent|SQL Server process memory percent|Százalék|Maximum|This metric is a placeholder and not populated at this time.|No Dimensions|
|tárterület|Data space used|Bytes|Maximum|Total database size. Not applicable to data warehouses.|No Dimensions|
|storage_percent|Data space used percent|Százalék|Maximum|Database size percentage. Not applicable to data warehouses or hyperscale databases.|No Dimensions|
|tempdb_data_size|Tempdb Data File Size Kilobytes|Mennyiség|Maximum|Tempdb Data File Size Kilobytes. Not applicable to data warehouses. This metric will be available for databases using the vCore purchasing model or 100 DTU and higher for DTU-based purchasing models.|No Dimensions|
|tempdb_log_size|Tempdb Log File Size Kilobytes|Mennyiség|Maximum|Tempdb Log File Size Kilobytes. Not applicable to data warehouses. This metric will be available for databases using the vCore purchasing model or 100 DTU and higher for DTU-based purchasing models.|No Dimensions|
|tempdb_log_used_percent|Tempdb Percent Log Used|Százalék|Maximum|Tempdb Percent Log Used. Not applicable to data warehouses. This metric will be available for databases using the vCore purchasing model or 100 DTU and higher for DTU-based purchasing models.|No Dimensions|
|workers_percent|Workers percentage|Százalék|Átlag|Workers percentage. Not applicable to data warehouses.|No Dimensions|
|xtp_storage_percent|In-Memory OLTP storage percent|Százalék|Átlag|In-Memory OLTP storage percent. Not applicable to data warehouses.|No Dimensions|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|allocated_data_storage|Data space allocated|Bytes|Átlag|Data space allocated|No Dimensions|
|allocated_data_storage_percent|Data space allocated percent|Százalék|Maximum|Data space allocated percent|No Dimensions|
|cpu_limit|CPU limit|Mennyiség|Átlag|CPU limit. Applies to vCore-based elastic pools.|No Dimensions|
|cpu_percent|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|No Dimensions|
|cpu_used|CPU used|Mennyiség|Átlag|CPU used. Applies to vCore-based elastic pools.|No Dimensions|
|dtu_consumption_percent|DTU-kihasználtság (%)|Százalék|Átlag|DTU percentage. Applies to DTU-based elastic pools.|No Dimensions|
|eDTU_limit|eDTU limit|Mennyiség|Átlag|eDTU limit. Applies to DTU-based elastic pools.|No Dimensions|
|eDTU_used|eDTU used|Mennyiség|Átlag|eDTU used. Applies to DTU-based elastic pools.|No Dimensions|
|log_write_percent|Log IO percentage|Százalék|Átlag|Log IO percentage|No Dimensions|
|physical_data_read_percent|Adat IO kihasználtsága (%)|Százalék|Átlag|Adat IO kihasználtsága (%)|No Dimensions|
|sessions_percent|Sessions percentage|Százalék|Átlag|Sessions percentage|No Dimensions|
|storage_limit|Data max size|Bytes|Átlag|Storage limit|No Dimensions|
|storage_percent|Data space used percent||Százalék|Átlag|Storage percentage|No Dimensions|
|storage_used|Data space used|Bytes|Átlag|Felhasznált tárterület|No Dimensions|
|sqlserver_process_core_percent|SQL Server process core percent|Százalék|Maximum|This metric is a placeholder and not populated at this time.|No Dimensions|
|sqlserver_process_memory_percent|SQL Server process memory percent|Százalék|Maximum|This metric is a placeholder and not populated at this time.|No Dimensions|
|tempdb_data_size|Tempdb Data File Size Kilobytes|Mennyiség|Maximum|Tempdb Data File Size Kilobytes. Not applicable to data warehouses. This metric will be available for databases using the vCore purchasing model or 100 DTU and higher for DTU-based purchasing models.|No Dimensions|
|tempdb_log_size|Tempdb Log File Size Kilobytes|Mennyiség|Maximum|Tempdb Log File Size Kilobytes. Not applicable to data warehouses. This metric will be available for databases using the vCore purchasing model or 100 DTU and higher for DTU-based purchasing models.|No Dimensions|
|tempdb_log_used_percent|Tempdb Percent Log Used|Százalék|Maximum|Tempdb Percent Log Used. Not applicable to data warehouses. This metric will be available for databases using the vCore purchasing model or 100 DTU and higher for DTU-based purchasing models.|No Dimensions|
|workers_percent|Workers percentage|Százalék|Átlag|Workers percentage|No Dimensions|
|xtp_storage_percent|In-Memory OLTP storage percent|Százalék|Átlag|In-Memory OLTP storage percent|No Dimensions|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|avg_cpu_percent|Average CPU percentage|Százalék|Átlag|Average CPU percentage|No Dimensions|
|io_bytes_read|IO bytes read|Bytes|Átlag|IO bytes read|No Dimensions|
|io_requests|IO requests count|Mennyiség|Átlag|IO requests count|No Dimensions|
|io_bytes_written|IO bytes written|Bytes|Átlag|IO bytes written|No Dimensions|
|reserved_storage_mb|Storage space reserved|Mennyiség|Átlag|Storage space reserved|No Dimensions|
|storage_space_used_mb|Storage space used|Mennyiség|Átlag|Storage space used|No Dimensions|
|virtual_core_count|Virtual core count|Mennyiség|Átlag|Virtual core count|No Dimensions|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|UsedCapacity|Used capacity|Bytes|Átlag|Account used capacity|No Dimensions|
|Tranzakciók|Tranzakciók|Mennyiség|Összes|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Use ResponseType dimension for the number of different type of response.|ResponseType, GeoType, ApiName, Authentication|
|Belépő|Belépő|Bytes|Összes|The amount of ingress data, in bytes. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, Authentication|
|Kimenő forgalom|Kimenő forgalom|Bytes|Összes|The amount of egress data, in bytes. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Success Server Latency|Milliseconds|Átlag|The average latency used by Azure Storage to process a successful request, in milliseconds. Ez az érték nem tartalmazza az AverageE2ELatency paraméterben megadott hálózati késést.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Success E2E Latency|Milliseconds|Átlag|The average end-to-end latency of successful requests made to a storage service or the specified API operation, in milliseconds. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, Authentication|
|Elérhetőség|Elérhetőség|Százalék|Átlag|The percentage of availability for the storage service or the specified API operation. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BlobCapacity|Blob Capacity|Bytes|Átlag|The amount of storage used by the storage account’s Blob service in bytes.|BlobType, Tier|
|BlobCount|Blob Count|Mennyiség|Összes|The number of Blob in the storage account’s Blob service.|BlobType|       |BlobCount|Blob Count|Mennyiség|Átlag|The number of Blob in the storage account’s Blob service.|BlobType, Tier|
|ContainerCount|Blob Container Count|Mennyiség|Átlag|The number of containers in the storage account’s Blob service.|No Dimensions|
|IndexCapacity|Index Capacity|Bytes|Átlag|The amount of storage used by ADLS Gen2 (Hierarchical) Index in bytes.|No Dimensions|
|Tranzakciók|Tranzakciók|Mennyiség|Összes|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Use ResponseType dimension for the number of different type of response.|ResponseType, GeoType, ApiName, Authentication|
|Belépő|Belépő|Bytes|Összes|The amount of ingress data, in bytes. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, Authentication|
|Kimenő forgalom|Kimenő forgalom|Bytes|Összes|The amount of egress data, in bytes. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Success Server Latency|Milliseconds|Átlag|The average latency used by Azure Storage to process a successful request, in milliseconds. Ez az érték nem tartalmazza az AverageE2ELatency paraméterben megadott hálózati késést.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Success E2E Latency|Milliseconds|Átlag|The average end-to-end latency of successful requests made to a storage service or the specified API operation, in milliseconds. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, Authentication|
|Elérhetőség|Elérhetőség|Százalék|Átlag|The percentage of availability for the storage service or the specified API operation. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|FileCapacity|File Capacity|Bytes|Átlag|The amount of storage used by the storage account’s File service in bytes.|No Dimensions|
|FileCount|File Count|Mennyiség|Átlag|The number of file in the storage account’s File service.|No Dimensions|
|FileShareCount|File Share Count|Mennyiség|Átlag|The number of file shares in the storage account’s File service.|No Dimensions|
|Tranzakciók|Tranzakciók|Mennyiség|Összes|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Use ResponseType dimension for the number of different type of response.|ResponseType, GeoType, ApiName, Authentication|
|Belépő|Belépő|Bytes|Összes|The amount of ingress data, in bytes. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, Authentication|
|Kimenő forgalom|Kimenő forgalom|Bytes|Összes|The amount of egress data, in bytes. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Success Server Latency|Milliseconds|Átlag|The average latency used by Azure Storage to process a successful request, in milliseconds. Ez az érték nem tartalmazza az AverageE2ELatency paraméterben megadott hálózati késést.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Success E2E Latency|Milliseconds|Átlag|The average end-to-end latency of successful requests made to a storage service or the specified API operation, in milliseconds. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, Authentication|
|Elérhetőség|Elérhetőség|Százalék|Átlag|The percentage of availability for the storage service or the specified API operation. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|QueueCapacity|Queue Capacity|Bytes|Átlag|The amount of storage used by the storage account’s Queue service in bytes.|No Dimensions|
|QueueCount|Queue Count|Mennyiség|Átlag|The number of queue in the storage account’s Queue service.|No Dimensions|
|QueueMessageCount|Queue Message Count|Mennyiség|Átlag|The approximate number of queue messages in the storage account’s Queue service.|No Dimensions|
|Tranzakciók|Tranzakciók|Mennyiség|Összes|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Use ResponseType dimension for the number of different type of response.|ResponseType, GeoType, ApiName, Authentication|
|Belépő|Belépő|Bytes|Összes|The amount of ingress data, in bytes. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, Authentication|
|Kimenő forgalom|Kimenő forgalom|Bytes|Összes|The amount of egress data, in bytes. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Success Server Latency|Milliseconds|Átlag|The average latency used by Azure Storage to process a successful request, in milliseconds. Ez az érték nem tartalmazza az AverageE2ELatency paraméterben megadott hálózati késést.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Success E2E Latency|Milliseconds|Átlag|The average end-to-end latency of successful requests made to a storage service or the specified API operation, in milliseconds. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, Authentication|
|Elérhetőség|Elérhetőség|Százalék|Átlag|The percentage of availability for the storage service or the specified API operation. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TableCapacity|Table Capacity|Bytes|Átlag|The amount of storage used by the storage account’s Table service in bytes.|No Dimensions|
|TableCount|Table Count|Mennyiség|Átlag|The number of table in the storage account’s Table service.|No Dimensions|
|TableEntityCount|Table Entity Count|Mennyiség|Átlag|The number of table entities in the storage account’s Table service.|No Dimensions|
|Tranzakciók|Tranzakciók|Mennyiség|Összes|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Use ResponseType dimension for the number of different type of response.|ResponseType, GeoType, ApiName, Authentication|
|Belépő|Belépő|Bytes|Összes|The amount of ingress data, in bytes. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, Authentication|
|Kimenő forgalom|Kimenő forgalom|Bytes|Összes|The amount of egress data, in bytes. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Success Server Latency|Milliseconds|Átlag|The average latency used by Azure Storage to process a successful request, in milliseconds. Ez az érték nem tartalmazza az AverageE2ELatency paraméterben megadott hálózati késést.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Success E2E Latency|Milliseconds|Átlag|The average end-to-end latency of successful requests made to a storage service or the specified API operation, in milliseconds. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, Authentication|
|Elérhetőség|Elérhetőség|Százalék|Átlag|The percentage of availability for the storage service or the specified API operation. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ServerSyncSessionResult|Sync Session Result|Mennyiség|Átlag|Metric that logs a value of 1 each time the Server Endpoint successfully completes a Sync Session with the Cloud Endpoint|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|Files Synced|Mennyiség|Összes|Count of Files synced|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Files not syncing|Mennyiség|Összes|Count of files failed to sync|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|Bytes synced|Bytes|Összes|Total file size transferred for Sync Sessions|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncServerHeartbeat|Server Online Status|Mennyiség|Maximum|Metric that logs a value of 1 each time the registered server successfully records a heartbeat with the Cloud Endpoint|ServerName|
|StorageSyncRecallIOTotalSizeBytes|Cloud tiering recall|Bytes|Összes|Total size of data recalled by the server|ServerName|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ResourceUtilization|SU % Utilization|Százalék|Maximum|SU % Utilization|LogicalName, PartitionId|
|InputEvents|Input Events|Mennyiség|Összes|Input Events|LogicalName, PartitionId|
|InputEventBytes|Input Event Bytes|Bytes|Összes|Input Event Bytes|LogicalName, PartitionId|
|LateInputEvents|Late Input Events|Mennyiség|Összes|Late Input Events|LogicalName, PartitionId|
|OutputEvents|Output Events|Mennyiség|Összes|Output Events|LogicalName, PartitionId|
|ConversionErrors|Data Conversion Errors|Mennyiség|Összes|Data Conversion Errors|LogicalName, PartitionId|
|Hibák|Runtime Errors|Mennyiség|Összes|Runtime Errors|LogicalName, PartitionId|
|DroppedOrAdjustedEvents|Out of order Events|Mennyiség|Összes|Out of order Events|LogicalName, PartitionId|
|AMLCalloutRequests|Function Requests|Mennyiség|Összes|Function Requests|LogicalName, PartitionId|
|AMLCalloutFailedRequests|Failed Function Requests|Mennyiség|Összes|Failed Function Requests|LogicalName, PartitionId|
|AMLCalloutInputEvents|Function Events|Mennyiség|Összes|Function Events|LogicalName, PartitionId|
|DeserializationError|Input Deserialization Errors|Mennyiség|Összes|Input Deserialization Errors|LogicalName, PartitionId|
|EarlyInputEvents|Early Input Events|Mennyiség|Összes|Early Input Events|LogicalName, PartitionId|
|OutputWatermarkDelaySeconds|Watermark Delay|másodperc|Maximum|Watermark Delay|LogicalName, PartitionId|
|InputEventsSourcesBacklogged|Backlogged Input Events|Mennyiség|Maximum|Backlogged Input Events|LogicalName, PartitionId|
|InputEventsSourcesPerSecond|Input Sources Received|Mennyiség|Összes|Input Sources Received|LogicalName, PartitionId|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ingress Received Messages|Mennyiség|Összes|Count of messages read from all Event hub or IoT hub event sources|No Dimensions|
|IngressReceivedInvalidMessages|Ingress Received Invalid Messages|Mennyiség|Összes|Count of invalid messages read from all Event hub or IoT hub event sources|No Dimensions|
|IngressReceivedBytes|Ingress Received Bytes|Bytes|Összes|Count of bytes read from all event sources|No Dimensions|
|IngressStoredBytes|Ingress Stored Bytes|Bytes|Összes|Total size of events successfully processed and available for query|No Dimensions|
|IngressStoredEvents|Ingress Stored Events|Mennyiség|Összes|Count of flattened events successfully processed and available for query|No Dimensions|
|IngressReceivedMessagesTimeLag|Ingress Received Messages Time Lag|másodperc|Maximum|Difference between the time that the message is enqueued in the event source and the time it is processed in Ingress|No Dimensions|
|IngressReceivedMessagesCountLag|Ingress Received Messages Count Lag|Mennyiség|Átlag|Difference between the sequence number of last enqueued message in the event source partition and sequence number of message being processed in Ingress|No Dimensions|
|WarmStorageMaxProperties|Warm Storage Max Properties|Mennyiség|Maximum|Maximum number of properties used allowed by the environment for S1/S2 SKU and maximum number of properties allowed by Warm Store for PAYG SKU|No Dimensions|
|WarmStorageUsedProperties|Warm Storage Used Properties |Mennyiség|Maximum|Number of properties used by the environment for S1/S2 SKU and number of properties used by Warm Store for PAYG SKU|No Dimensions|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ingress Received Messages|Mennyiség|Összes|Count of messages read from the event source|No Dimensions|
|IngressReceivedInvalidMessages|Ingress Received Invalid Messages|Mennyiség|Összes|Count of invalid messages read from the event source|No Dimensions|
|IngressReceivedBytes|Ingress Received Bytes|Bytes|Összes|Count of bytes read from the event source|No Dimensions|
|IngressStoredBytes|Ingress Stored Bytes|Bytes|Összes|Total size of events successfully processed and available for query|No Dimensions|
|IngressStoredEvents|Ingress Stored Events|Mennyiség|Összes|Count of flattened events successfully processed and available for query|No Dimensions|
|IngressReceivedMessagesTimeLag|Ingress Received Messages Time Lag|másodperc|Maximum|Difference between the time that the message is enqueued in the event source and the time it is processed in Ingress|No Dimensions|
|IngressReceivedMessagesCountLag|Ingress Received Messages Count Lag|Mennyiség|Átlag|Difference between the sequence number of last enqueued message in the event source partition and sequence number of message being processed in Ingress|No Dimensions|
|WarmStorageMaxProperties|Warm Storage Max Properties|Mennyiség|Maximum|Maximum number of properties used allowed by the environment for S1/S2 SKU and maximum number of properties allowed by Warm Store for PAYG SKU|No Dimensions|
|WarmStorageUsedProperties|Warm Storage Used Properties |Mennyiség|Maximum|Number of properties used by the environment for S1/S2 SKU and number of properties used by Warm Store for PAYG SKU|No Dimensions|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Disk Read Bytes/Sec|BytesPerSecond|Átlag|Average disk throughput due to read operations over the sample period.|No Dimensions|
|DiskWriteBytesPerSecond|Disk Write Bytes/Sec|BytesPerSecond|Átlag|Average disk throughput due to write operations over the sample period.|No Dimensions|
|Disk Read Bytes|Disk Read Bytes|Bytes|Összes|Total disk throughput due to read operations over the sample period.|No Dimensions|
|Disk Write Bytes|Disk Write Bytes|Bytes|Összes|Total disk throughput due to write operations over the sample period.|No Dimensions|
|DiskReadOperations|Disk Read Operations|Mennyiség|Összes|The number of IO read operations in the previous sample period. Note that these operations may be variable sized.|No Dimensions|
|DiskWriteOperations|Disk Write Operations|Mennyiség|Összes|The number of IO write operations in the previous sample period. Note that these operations may be variable sized.|No Dimensions|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Átlag|The average number of IO read operations in the previous sample period. Note that these operations may be variable sized.|No Dimensions|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Átlag|The average number of IO write operations in the previous sample period. Note that these operations may be variable sized.|No Dimensions|
|DiskReadLatency|Disk Read Latency|Milliseconds|Átlag|Total read latency. The sum of the device and kernel read latencies.|No Dimensions|
|DiskWriteLatency|Disk Write Latency|Milliseconds|Átlag|Total write latency. The sum of the device and kernel write latencies.|No Dimensions|
|NetworkInBytesPerSecond|Network In Bytes/Sec|BytesPerSecond|Átlag|Average network throughput for received traffic.|No Dimensions|
|NetworkOutBytesPerSecond|Network Out Bytes/Sec|BytesPerSecond|Átlag|Average network throughput for transmitted traffic.|No Dimensions|
|Bejövő hálózat|Bejövő hálózat|Bytes|Összes|Total network throughput for received traffic.|No Dimensions|
|Kimenő hálózat|Kimenő hálózat|Bytes|Összes|Total network throughput for transmitted traffic.|No Dimensions|
|MemoryUsed|Memory Used|Bytes|Átlag|The amount of machine memory that is in use by the VM.|No Dimensions|
|MemoryGranted|Memory Granted|Bytes|Átlag|The amount of memory that was granted to the VM by the host. Memory is not granted to the host until it is touched one time and granted memory may be swapped out or ballooned away if the VMkernel needs the memory.|No Dimensions|
|MemoryActive|Memory Active|Bytes|Átlag|The amount of memory used by the VM in the past small window of time. This is the "true" number of how much memory the VM currently has need of. Additional, unused memory may be swapped out or ballooned with no impact to the guest's performance.|No Dimensions|
|Százalékos processzorhasználat|Százalékos processzorhasználat|Százalék|Átlag|The CPU utilization. This value is reported with 100% representing all processor cores on the system. As an example, a 2-way VM using 50% of a four-core system is completely using two cores.|No Dimensions|
|PercentageCpuReady|Percentage CPU Ready|Milliseconds|Összes|Ready time is the time spend waiting for CPU(s) to become available in the past update interval.|No Dimensions|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CpuPercentage|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|Példány|
|MemoryPercentage|Memory Percentage|Százalék|Átlag|Memory Percentage|Példány|
|DiskQueueLength|Disk Queue Length|Mennyiség|Átlag|Disk Queue Length|Példány|
|HttpQueueLength|Http Queue Length|Mennyiség|Átlag|Http Queue Length|Példány|
|BytesReceived|Data In|Bytes|Összes|Data In|Példány|
|BytesSent|Data Out|Bytes|Összes|Data Out|Példány|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (excluding functions)

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CpuTime|CPU Time|másodperc|Összes|CPU Time|Példány|
|Kérelmek|Kérelmek|Mennyiség|Összes|Kérelmek|Példány|
|BytesReceived|Data In|Bytes|Összes|Data In|Példány|
|BytesSent|Data Out|Bytes|Összes|Data Out|Példány|
|Http101|Http 101|Mennyiség|Összes|Http 101|Példány|
|Http2xx|Http 2xx|Mennyiség|Összes|Http 2xx|Példány|
|Http3xx|Http 3xx|Mennyiség|Összes|Http 3xx|Példány|
|Http401|Http 401|Mennyiség|Összes|Http 401|Példány|
|Http403|Http 403|Mennyiség|Összes|Http 403|Példány|
|Http404|Http 404|Mennyiség|Összes|Http 404|Példány|
|Http406|Http 406|Mennyiség|Összes|Http 406|Példány|
|Http4xx|Http 4xx|Mennyiség|Összes|Http 4xx|Példány|
|Http5xx|Http Server Errors|Mennyiség|Összes|Http Server Errors|Példány|
|MemoryWorkingSet|Memory working set|Bytes|Átlag|Memory working set|Példány|
|AverageMemoryWorkingSet|Average memory working set|Bytes|Átlag|Average memory working set|Példány|
|AverageResponseTime|Average Response Time|másodperc|Átlag|Average Response Time|Példány|
|AppConnections|Connections (Kapcsolatok)|Mennyiség|Átlag|Connections (Kapcsolatok)|Példány|
|Handles|Handle Count|Mennyiség|Átlag|Handle Count|Példány|
|Threads|Thread Count|Mennyiség|Átlag|Thread Count|Példány|
|PrivateBytes|Private Bytes|Bytes|Átlag|Private Bytes|Példány|
|IoReadBytesPerSecond|IO Read Bytes Per Second|BytesPerSecond|Összes|IO Read Bytes Per Second|Példány|
|IoWriteBytesPerSecond|IO Write Bytes Per Second|BytesPerSecond|Összes|IO Write Bytes Per Second|Példány|
|IoOtherBytesPerSecond|IO Other Bytes Per Second|BytesPerSecond|Összes|IO Other Bytes Per Second|Példány|
|IoReadOperationsPerSecond|IO Read Operations Per Second|BytesPerSecond|Összes|IO Read Operations Per Second|Példány|
|IoWriteOperationsPerSecond|IO Write Operations Per Second|BytesPerSecond|Összes|IO Write Operations Per Second|Példány|
|IoOtherOperationsPerSecond|IO Other Operations Per Second|BytesPerSecond|Összes|IO Other Operations Per Second|Példány|
|RequestsInApplicationQueue|Requests In Application Queue|Mennyiség|Átlag|Requests In Application Queue|Példány|
|CurrentAssemblies|Current Assemblies|Mennyiség|Átlag|Current Assemblies|Példány|
|TotalAppDomains|Total App Domains|Mennyiség|Átlag|Total App Domains|Példány|
|TotalAppDomainsUnloaded|Total App Domains Unloaded|Mennyiség|Átlag|Total App Domains Unloaded|Példány|
|Gen0Collections|Gen 0 Garbage Collections|Mennyiség|Összes|Gen 0 Garbage Collections|Példány|
|Gen1Collections|Gen 1 Garbage Collections|Mennyiség|Összes|Gen 1 Garbage Collections|Példány|
|Gen2Collections|Gen 2 Garbage Collections|Mennyiség|Összes|Gen 2 Garbage Collections|Példány|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (functions)

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BytesReceived|Data In|Bytes|Összes|Data In|Példány|
|BytesSent|Data Out|Bytes|Összes|Data Out|Példány|
|Http5xx|Http Server Errors|Mennyiség|Összes|Http Server Errors|Példány|
|MemoryWorkingSet|Memory working set|Bytes|Átlag|Memory working set|Példány|
|AverageMemoryWorkingSet|Average memory working set|Bytes|Átlag|Average memory working set|Példány|
|FunctionExecutionUnits|Function Execution Units|MB / Milliseconds|Összes|[Function Execution Units](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Példány|
|FunctionExecutionCount|Function Execution Count|Mennyiség|Összes|Function Execution Count|Példány|
|PrivateBytes|Private Bytes|Bytes|Átlag|Private Bytes|Példány|
|IoReadBytesPerSecond|IO Read Bytes Per Second|BytesPerSecond|Összes|IO Read Bytes Per Second|Példány|
|IoWriteBytesPerSecond|IO Write Bytes Per Second|BytesPerSecond|Összes|IO Write Bytes Per Second|Példány|
|IoOtherBytesPerSecond|IO Other Bytes Per Second|BytesPerSecond|Összes|IO Other Bytes Per Second|Példány|
|IoReadOperationsPerSecond|IO Read Operations Per Second|BytesPerSecond|Összes|IO Read Operations Per Second|Példány|
|IoWriteOperationsPerSecond|IO Write Operations Per Second|BytesPerSecond|Összes|IO Write Operations Per Second|Példány|
|IoOtherOperationsPerSecond|IO Other Operations Per Second|BytesPerSecond|Összes|IO Other Operations Per Second|Példány|
|RequestsInApplicationQueue|Requests In Application Queue|Mennyiség|Átlag|Requests In Application Queue|Példány|
|CurrentAssemblies|Current Assemblies|Mennyiség|Átlag|Current Assemblies|Példány|
|TotalAppDomains|Total App Domains|Mennyiség|Átlag|Total App Domains|Példány|
|TotalAppDomainsUnloaded|Total App Domains Unloaded|Mennyiség|Átlag|Total App Domains Unloaded|Példány|
|Gen0Collections|Gen 0 Garbage Collections|Mennyiség|Összes|Gen 0 Garbage Collections|Példány|
|Gen1Collections|Gen 1 Garbage Collections|Mennyiség|Összes|Gen 1 Garbage Collections|Példány|
|Gen2Collections|Gen 2 Garbage Collections|Mennyiség|Összes|Gen 2 Garbage Collections|Példány|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CpuTime|CPU Time|másodperc|Összes|CPU Time|Példány|
|Kérelmek|Kérelmek|Mennyiség|Összes|Kérelmek|Példány|
|BytesReceived|Data In|Bytes|Összes|Data In|Példány|
|BytesSent|Data Out|Bytes|Összes|Data Out|Példány|
|Http101|Http 101|Mennyiség|Összes|Http 101|Példány|
|Http2xx|Http 2xx|Mennyiség|Összes|Http 2xx|Példány|
|Http3xx|Http 3xx|Mennyiség|Összes|Http 3xx|Példány|
|Http401|Http 401|Mennyiség|Összes|Http 401|Példány|
|Http403|Http 403|Mennyiség|Összes|Http 403|Példány|
|Http404|Http 404|Mennyiség|Összes|Http 404|Példány|
|Http406|Http 406|Mennyiség|Összes|Http 406|Példány|
|Http4xx|Http 4xx|Mennyiség|Összes|Http 4xx|Példány|
|Http5xx|Http Server Errors|Mennyiség|Összes|Http Server Errors|Példány|
|MemoryWorkingSet|Memory working set|Bytes|Átlag|Memory working set|Példány|
|AverageMemoryWorkingSet|Average memory working set|Bytes|Átlag|Average memory working set|Példány|
|AverageResponseTime|Average Response Time|másodperc|Átlag|Average Response Time|Példány|
|FunctionExecutionUnits|Function Execution Units|Mennyiség|Összes|Function Execution Units|Példány|
|FunctionExecutionCount|Function Execution Count|Mennyiség|Összes|Function Execution Count|Példány|
|AppConnections|Connections (Kapcsolatok)|Mennyiség|Átlag|Connections (Kapcsolatok)|Példány|
|Handles|Handle Count|Mennyiség|Átlag|Handle Count|Példány|
|Threads|Thread Count|Mennyiség|Átlag|Thread Count|Példány|
|PrivateBytes|Private Bytes|Bytes|Átlag|Private Bytes|Példány|
|IoReadBytesPerSecond|IO Read Bytes Per Second|BytesPerSecond|Összes|IO Read Bytes Per Second|Példány|
|IoWriteBytesPerSecond|IO Write Bytes Per Second|BytesPerSecond|Összes|IO Write Bytes Per Second|Példány|
|IoOtherBytesPerSecond|IO Other Bytes Per Second|BytesPerSecond|Összes|IO Other Bytes Per Second|Példány|
|IoReadOperationsPerSecond|IO Read Operations Per Second|BytesPerSecond|Összes|IO Read Operations Per Second|Példány|
|IoWriteOperationsPerSecond|IO Write Operations Per Second|BytesPerSecond|Összes|IO Write Operations Per Second|Példány|
|IoOtherOperationsPerSecond|IO Other Operations Per Second|BytesPerSecond|Összes|IO Other Operations Per Second|Példány|
|RequestsInApplicationQueue|Requests In Application Queue|Mennyiség|Átlag|Requests In Application Queue|Példány|
|CurrentAssemblies|Current Assemblies|Mennyiség|Átlag|Current Assemblies|Példány|
|TotalAppDomains|Total App Domains|Mennyiség|Átlag|Total App Domains|Példány|
|TotalAppDomainsUnloaded|Total App Domains Unloaded|Mennyiség|Átlag|Total App Domains Unloaded|Példány|
|Gen0Collections|Gen 0 Garbage Collections|Mennyiség|Összes|Gen 0 Garbage Collections|Példány|
|Gen1Collections|Gen 1 Garbage Collections|Mennyiség|Összes|Gen 1 Garbage Collections|Példány|
|Gen2Collections|Gen 2 Garbage Collections|Mennyiség|Összes|Gen 2 Garbage Collections|Példány|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Kérelmek|Kérelmek|Mennyiség|Összes|Kérelmek|Példány|
|BytesReceived|Data In|Bytes|Összes|Data In|Példány|
|BytesSent|Data Out|Bytes|Összes|Data Out|Példány|
|Http101|Http 101|Mennyiség|Összes|Http 101|Példány|
|Http2xx|Http 2xx|Mennyiség|Összes|Http 2xx|Példány|
|Http3xx|Http 3xx|Mennyiség|Összes|Http 3xx|Példány|
|Http401|Http 401|Mennyiség|Összes|Http 401|Példány|
|Http403|Http 403|Mennyiség|Összes|Http 403|Példány|
|Http404|Http 404|Mennyiség|Összes|Http 404|Példány|
|Http406|Http 406|Mennyiség|Összes|Http 406|Példány|
|Http4xx|Http 4xx|Mennyiség|Összes|Http 4xx|Példány|
|Http5xx|Http Server Errors|Mennyiség|Összes|Http Server Errors|Példány|
|AverageResponseTime|Average Response Time|másodperc|Átlag|Average Response Time|Példány|
|CpuPercentage|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|Példány|
|MemoryPercentage|Memory Percentage|Százalék|Átlag|Memory Percentage|Példány|
|DiskQueueLength|Disk Queue Length|Mennyiség|Átlag|Disk Queue Length|Példány|
|HttpQueueLength|Http Queue Length|Mennyiség|Átlag|Http Queue Length|Példány|
|ActiveRequests|Active Requests|Mennyiség|Összes|Active Requests|Példány|
|TotalFrontEnds|Total Front Ends|Mennyiség|Átlag|Total Front Ends|No Dimensions|
|SmallAppServicePlanInstances|Small App Service Plan Workers|Mennyiség|Átlag|Small App Service Plan Workers|No Dimensions|
|MediumAppServicePlanInstances|Medium App Service Plan Workers|Mennyiség|Átlag|Medium App Service Plan Workers|No Dimensions|
|LargeAppServicePlanInstances|Large App Service Plan Workers|Mennyiség|Átlag|Large App Service Plan Workers|No Dimensions|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Metrika|Metric Display Name|Unit (Egység)|Aggregation Type|Leírás|Dimenziók|
|---|---|---|---|---|---|
|WorkersTotal|Total Workers|Mennyiség|Átlag|Total Workers|No Dimensions|
|WorkersAvailable|Available Workers|Mennyiség|Átlag|Available Workers|No Dimensions|
|WorkersUsed|Used Workers|Mennyiség|Átlag|Used Workers|No Dimensions|
|CpuPercentage|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|Példány|
|MemoryPercentage|Memory Percentage|Százalék|Átlag|Memory Percentage|Példány|

## <a name="next-steps"></a>Következő lépések
* [Read about metrics in Azure Monitor](data-platform.md)
* [Create alerts on metrics](alerts-overview.md)
* [Export metrics to storage, Event Hub, or Log Analytics](resource-logs-overview.md)
