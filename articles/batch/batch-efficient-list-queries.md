<properties
    pageTitle="在 Azure Batch 中的有效清單查詢 |Microsoft Azure"
    description="查詢 Azure Batch 實體 (例如集區、工作、作業和計算節點) 時，減少傳回的資料量以提高效能。"
    services="batch"
    documentationCenter=".net"
    authors="mmacy"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="batch"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="big-compute"
    ms.date="10/12/2015"
    ms.author="v-marsma"/>

# 有效率地查詢 Azure Batch 服務

在這篇文章中，您將學習如何減少項目數目，以及使用時傳回的資料量 [批次.NET][api_net] API 來查詢批次服務的工作，工作清單的計算節點，等等。

Azure Batch 是大型運算，在實際執行環境中，工作、作業和運算節點等實體可能有數千個。 因此，取得這些項目的相關資訊可能產生必須在每個查詢上傳送的大量資料。 限制項目數目及各項目傳回的資訊類型將可加速查詢，因而提高應用程式的效能。

列出工作、作業、計算節點 -- 以下是每個使用 Azure Batch 的應用程式幾乎都必須執行的作業範例，而且通常是經常執行： 監視是常見的使用案例。 例如，判斷集區的容量和狀態需要查詢集區中的所有計算節點。 另一個例子是查詢工作的作業，判斷是否有任何作業仍在佇列中。

這 [批次.NET][api_net] API 程式碼片段會擷取所有工作，以及這些工作的屬性為完整組件相關聯的工作:

```
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks = batchClient.JobOperations.ListTasks("job-001");
```

更有效率的清單查詢可以執行，不過，藉由提供 [ODATADetailLevel][odata] 至 [JobOperations.ListTasks][net_list_tasks] 方法。 此程式碼片段只是傳回已完成之作業的識別碼、命令列和計算節點資訊屬性：

```
// Configure an ODATADetailLevel specifying a subset of tasks and their properties to return
ODATADetailLevel detailLevel = new ODATADetailLevel();
detailLevel.FilterClause = "state eq 'completed'";
detailLevel.SelectClause = "id,commandLine,nodeInfo";

// Supply the ODATADetailLevel to the ListTasks method
IPagedEnumerable<CloudTask> completedTasks = batchClient.JobOperations.ListTasks("job-001", detailLevel);
```

在上述範例案例中，如果工作中有數千個作業，則第二次查詢傳回結果的速度，通常會比第一次快很多。 關於在 Batch .NET API 中使用 ODATADetailLevel 列出項目，以下提供相關資訊。

> [AZURE.IMPORTANT]
> 強烈建議您 **一律** 提供 ODATADetailLevel 您的.NET API 清單呼叫，以確保最大效率和應用程式的效能。 指定詳細層級有助於縮短 Batch 服務回應時間、提高網路使用率，以及讓用戶端應用程式的記憶體使用量降到最低。

## 提高查詢效率的工具

 [批次.NET][api_net] 和 [批次 REST][api_rest] Api 可讓您減少傳回項目的數目為每個藉由指定傳回的清單，以及資訊數量 *篩選*, ，*選取*, ，和 *展開* 執行時的字串清單的查詢。

- **篩選** - *篩選條件字串* 是運算式，可減少傳回項目的數目。 例如，只列出工作正在執行的作業，或只列出可執行作業的計算節點。
  - 篩選字串包含一個或多個運算式，而運算式由屬性名稱、運算子和值構成。 可指定的屬性及每個屬性支援的運算子，取決於每個 API 呼叫類型。
  - 多個運算式可以透過邏輯運算子 `and` 和 `or` 結合。
  - 只列出執行中呈現作業的篩選字串範例：`startswith(id, 'renderTask') and (state eq 'running')`
- **選取** - *選取字串* 限制的屬性值所傳回的每個項目。 選取字串中可以指定項目的屬性清單，清單查詢結果的每個項目就只會傳回那些屬性值。
  - 選取字串由屬性名稱的逗號分隔清單組成。 可指定清單作業傳回的項目的任何屬性。
  - 指定每個作業只傳回三個屬性的選取字串範例：`id, state, stateTransitionTime`
- **展開** - *展開字串* 可減少取得某些資訊所需的 API 呼叫數目。 只要使用單一 API 呼叫，就可以取得每一個清單項目的詳細資訊，而不需要先取得清單，再對清單中的每一個項目執行呼叫。
  - 與選取字串相似，展開字串可以控制清單查詢結果中是否包含特定資料。
  - 只有列出工作、工作排程、作業和集區時，才支援展開字串，目前僅支援統計資料資訊。
  - 指定每一個項目應該傳回統計資料資訊的展開字串範例：`stats`
  - 當所有屬性都是必要且指定了任何選取的字串，在擴充字串 *必須* 用來取得統計資料資訊。 如果使用選取字串來取得屬性子集，則可以在選取字串中指定 `stats`，不需要指定展開字串。

> [AZURE.NOTE] 當建構的任何三個查詢字串類型 (篩選條件，[選取]，展開)，您必須確定屬性名稱和大小寫相符的 REST API 項目與其。 例如，當使用.NET [CloudTask](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask), ，您必須指定 **狀態** 而不是 **狀態** 即使.NET 屬性是 [CloudTask.State](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.state)。 關於 .NET 和 REST API 之間的屬性對應，請參閱下表。

### 篩選、選取和展開字串規格

- 屬性指定在篩選中，選取及展開字串中出現的屬性名稱等同於 [批次 REST][api_rest] API-這是即使的情況使用 [批次.NET][api_net] 程式庫。
- 所有屬性名稱都會區分大小寫，但屬性值不會區分大小寫
- 日期/時間字串有兩種格式，開頭必須加上 `DateTime`
  - W3CDTF 格式範例：`creationTime gt DateTime'2011-05-08T08:49:37Z'`
  - RFC1123 格式範例：`creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`
- 布林值字串為 `true` 或 `false`
- 如果指定無效的屬性或運算子，將會導致 `400 (Bad Request)` 錯誤

## 在 Batch .NET 中有效率地查詢

內 [批次.NET][api_net] API， [ODATADetailLevel][odata] 是用來提供篩選器，選取及展開清單作業的字串。 ODataDetailLevel 物件有三個可能直接設定或建構函式中指定的公用字串屬性，並將此物件會傳遞做為參數的各種清單作業例如 [ListPools][net_list_pools], ，[ListJobs][net_list_jobs], ，和 [ListTasks][net_list_tasks]。

- [ODATADetailLevel.FilterClause][odata_filter] -限制傳回的項目數目
- [ODATADetailLevel.SelectClause][odata_select] – 指定屬性值的子集傳回每個項目
- [ODATADetailLevel.ExpandClause][odata_expand] – 擷取項目資料，在單一 API 呼叫，而不是每個發出呼叫

下列程式碼片段使用 Batch .NET API，有效率地向 Batch 服務查詢一組特定集區的統計資料。 在此案例中，Batch 使用者具有測試與實際執行的集區，其測試集區識別碼前面加上 "test"，而實際執行的集區識別碼前面加上 "prod"。 在程式碼片段， *myBatchClient* 已妥善初始化的執行個體 [BatchClient](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient)。

    // First we need an ODATADetailLevel instance on which to set the expand, filter, and select
    // clause strings
    ODATADetailLevel detailLevel = new ODATADetailLevel();

    // We want to pull only the "test" pools, so we limit the number of items returned by using a
    // FilterClause and specifying that the pool IDs must start with "test"
    detailLevel.FilterClause = "startswith(id, 'test')";

    // To further limit the data that crosses the wire, configure the SelectClause to limit the
    // properties returned on each CloudPool object to only CloudPool.Id and CloudPool.Statistics
    detailLevel.SelectClause = "id, stats";

    // Specify the ExpandClause so that the .NET API pulls the statistics for the CloudPools in a single
    // underlying REST API call. Note that we use the pool's REST API element name "stats" here as opposed
    // to "Statistics" as it appears in the .NET API (CloudPool.Statistics)
    detailLevel.ExpandClause = "stats";

    // Now get our collection of pools, minimizing the amount of data returned by specifying the
    // detail level we configured above
    List<CloudPool> testPools = await myBatchClient.PoolOperations.ListPools(detailLevel).ToListAsync();

> [AZURE.TIP] 執行個體 [ODATADetailLevel][odata] 設定搭配 Select 和 Expand 子句也可以傳遞至適當的 Get 方法，例如 [PoolOperations.GetPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getpool.aspx) 來限制傳回的資料量。

## Batch REST 與 .NET API 的對應

屬性名稱，在篩選中，選取及展開字串 *必須* 反映成 REST API 的對應，在名稱和大小寫。 下表提供 .NET 和 REST API 對應項目之間的對應。

### 篩選字串的對應

- **.NET 清單方法** -每個.NET API 方法，此資料行中接受 [ODATADetailLevel][odata] 物件做為參數。
- **其餘列出要求** -此資料行中的每個 REST API 頁面包含指定屬性的表格，並允許作業 *篩選* 字串。 您建構時將使用這些屬性名稱和作業 [ODATADetailLevel.FilterClause][odata_filter] 字串。

| .NET 清單方法 | REST 清單要求 |
|---|---|
| [CertificateOperations.ListCertificates][net_list_certs] | [列出帳戶中的憑證][rest_list_certs]
| [CloudTask.ListNodeFiles][net_list_task_files] | [列出與作業相關聯的檔案][rest_list_task_files]
| [JobOperations.ListJobPreparationAndReleaseTaskStatus][net_list_jobprep_status] | [列出工作的工作準備和工作釋放作業的狀態][rest_list_jobprep_status]
| [JobOperations.ListJobs][net_list_jobs] | [列出帳戶中的工作][rest_list_jobs]
| [JobOperations.ListNodeFiles][net_list_nodefiles] | [列出節點上的檔案][rest_list_nodefiles]
| [JobOperations.ListTasks][net_list_tasks] | [列出與工作相關聯的作業][rest_list_tasks]
| [JobScheduleOperations.ListJobSchedules][net_list_job_schedules] | [列出帳戶中的工作排程][rest_list_job_schedules]
| [JobScheduleOperations.ListJobs][net_list_schedule_jobs] | [列出與作業排程相關聯的工作][rest_list_schedule_jobs]
| [PoolOperations.ListComputeNodes][net_list_compute_nodes] | [列出集區中的運算節點][rest_list_compute_nodes]
| [PoolOperations.ListPools][net_list_pools] | [列出帳戶中的集區][rest_list_pools]

### 選取字串的對應

- **批次.NET 型別** -批次.NET API 型別
- **REST API 實體** -此資料行中的每個頁面包含一個或多個資料表列出 REST API 的屬性名稱的類型。 建構時使用這些屬性名稱 *選取* 字串。 您會在建構時使用這些相同的屬性名稱 [ODATADetailLevel.SelectClause][odata_select] 字串。

| Batch .NET 類型 | REST API 實體 |
|---|---|
| [憑證][net_cert] | [取得憑證的相關資訊][rest_get_cert] |
| [CloudJob][net_job] | [取得工作的相關資訊][rest_get_job] |
| [CloudJobSchedule][net_schedule] | [取得工作排程的相關資訊][rest_get_schedule] |
| [ComputeNode][net_node] | [取得節點的相關資訊][rest_get_node] |
| [CloudPool][net_pool] | [取得集區的相關資訊][rest_get_pool] |
| [CloudTask][net_task] | [取得作業的相關資訊][rest_get_task] |

### 範例：建構篩選字串

建構篩選字串時 [ODATADetailLevel.FilterClause][odata_filter], ，請參閱下的表 *對應的篩選字串* 來尋找對應至您想要執行的清單作業的 REST API 文件頁面。 在該頁面的第一個含有多資料列的資料表中，您可以找到可篩選的屬性及其支援的運算子。 如果您想要擷取其結束代碼為非零的所有工作，例如，此資料列上 [列出與作業相關聯的工作][rest_list_tasks] 指定適用的屬性字串，可允許的運算子:

| 屬性 | 允許的作業 | 類型 |
| :--- | :--- | :--- |
| `executionInfo/exitCode` | `eq, ge, gt, le , lt` | `Int` |

因此，為了列出具有非零結束碼的所有作業，篩選字串如下：

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

### 範例：建構選取字串

建構 [ODATADetailLevel.SelectClause][odata_select], ，請參閱下的表 *對應選取字串* 並瀏覽至您要列出的實體類型對應的 REST API 畫面。 在該頁面的第一個含有多資料列的資料表中，您可以找到可選取的屬性及其支援的運算子。 如果您想要擷取只識別碼和命令列在清單中的每項工作，比方說，您會發現這些資料列中適用的資料表上 [取得工作的相關資訊][rest_get_task]:

| 屬性 | 類型 | 注意事項 |
| :--- | :--- | :--- |
| `id` | `String` | `The id of the task.` |
| `commandLine` | `String` | `The command line of the task.` |

為了讓每個列出的作業只包含識別碼和命令列，選取字串如下列：

`id, commandLine`

## 後續步驟

簽出 [EfficientListQueries][efficient_query_sample] 上以查看如何進行有效率的清單查詢的 GitHub 範例專案會影響應用程式的效能。 這個 C# 主控台應用程式會建立並將大量的工作加入至工作，然後使用不同的批次服務會查詢 [ODATADetailLevel][odata] 規格，顯示類似下面的輸出:

        Adding 5000 tasks to job jobEffQuery...
        5000 tasks added in 00:00:47.3467587, hit ENTER to query tasks...

        4943 tasks retrieved in 00:00:04.3408081 (ExpandClause:  | FilterClause: state eq 'active' | SelectClause: id,state)
        0 tasks retrieved in 00:00:00.2662920 (ExpandClause:  | FilterClause: state eq 'running' | SelectClause: id,state)
        59 tasks retrieved in 00:00:00.3337760 (ExpandClause:  | FilterClause: state eq 'completed' | SelectClause: id,state)
        5000 tasks retrieved in 00:00:04.1429881 (ExpandClause:  | FilterClause:  | SelectClause: id,state)
        5000 tasks retrieved in 00:00:15.1016127 (ExpandClause:  | FilterClause:  | SelectClause: id,state,environmentSettings)
        5000 tasks retrieved in 00:00:17.0548145 (ExpandClause: stats | FilterClause:  | SelectClause: )

        Sample complete, hit ENTER to continue...

如同經過時間的資訊中所顯示的，限制傳回的屬性和項目數目可以大幅降低查詢回應時間。 這和其他範例專案中的，您可以找到 [azure 批次範例][github_samples] GitHub 上的儲存機制。

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[efficient_query_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries
[github_samples]: https://github.com/Azure/azure-batch-samples
[odata]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx
[odata_ctor]: https://msdn.microsoft.com/library/azure/dn866178.aspx
[odata_expand]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.expandclause.aspx
[odata_filter]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.filterclause.aspx
[odata_select]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.selectclause.aspx

[net_list_certs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.listcertificates.aspx
[net_list_compute_nodes]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx
[net_list_job_schedules]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobschedules.aspx
[net_list_jobprep_status]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobpreparationandreleasetaskstatus.aspx
[net_list_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[net_list_nodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listnodefiles.aspx
[net_list_pools]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx
[net_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobs.aspx
[net_list_task_files]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[net_list_tasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx

[rest_list_certs]: https://msdn.microsoft.com/library/azure/dn820154.aspx
[rest_list_compute_nodes]: https://msdn.microsoft.com/library/azure/dn820159.aspx
[rest_list_job_schedules]: https://msdn.microsoft.com/library/azure/mt282174.aspx
[rest_list_jobprep_status]: https://msdn.microsoft.com/library/azure/mt282170.aspx
[rest_list_jobs]: https://msdn.microsoft.com/library/azure/dn820117.aspx
[rest_list_nodefiles]: https://msdn.microsoft.com/library/azure/dn820151.aspx
[rest_list_pools]: https://msdn.microsoft.com/library/azure/dn820101.aspx
[rest_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/mt282169.aspx
[rest_list_task_files]: https://msdn.microsoft.com/library/azure/dn820142.aspx
[rest_list_tasks]: https://msdn.microsoft.com/library/azure/dn820187.aspx

[rest_get_cert]: https://msdn.microsoft.com/library/azure/dn820176.aspx
[rest_get_job]: https://msdn.microsoft.com/library/azure/dn820106.aspx
[rest_get_node]: https://msdn.microsoft.com/library/azure/dn820168.aspx
[rest_get_pool]: https://msdn.microsoft.com/library/azure/dn820165.aspx
[rest_get_schedule]: https://msdn.microsoft.com/library/azure/mt282171.aspx
[rest_get_task]: https://msdn.microsoft.com/library/azure/dn820133.aspx

[net_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificate.aspx
[net_job]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_schedule]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjobschedule.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx

