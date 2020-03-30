---
title: Hatékony listalekérdezések tervezése - Azure Batch | Microsoft dokumentumok
description: Növelje a teljesítményt a lekérdezések szűrésével, amikor adatokat kér a Batch-erőforrásokról, például készletekről, feladatokról, feladatokról és számítási csomópontokról.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 031fefeb-248e-4d5a-9bc2-f07e46ddd30d
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 12/07/2018
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: df923ac479ce5f5a3668c18c616b11348dc6c0b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022239"
---
# <a name="create-queries-to-list-batch-resources-efficiently"></a>Lekérdezések létrehozása a Batch-erőforrások hatékony listázásához

Itt megtudhatja, hogyan növelheti az Azure Batch-alkalmazás teljesítményét azáltal, hogy csökkenti a szolgáltatás által a feladatok, feladatok, számítási csomópontok és egyéb erőforrások lekérdezéseke kor a [szolgáltatás][api_net] által visszaadott adatok mennyiségét.

Szinte minden Batch-alkalmazásnak valamilyen figyelést vagy más műveletet kell végrehajtania, amely lekérdezi a Batch szolgáltatást, gyakran rendszeres időközönként. Ha például meg szeretné állapítani, hogy vannak-e várólistán lévő feladatok a feladatban, a feladat minden feladatára vonatkozóan adatokat kell beszereznie. A készletben lévő csomópontok állapotának meghatározásához a készlet minden csomópontjára adatokat kell beszereznie. Ez a cikk bemutatja, hogyan hajthatja végre az ilyen lekérdezéseket a leghatékonyabb módon.

> [!NOTE]
> A Batch szolgáltatás speciális API-támogatást nyújt a feladatok számlálása a feladatban, és a számítási csomópontok számlálása a batch készletben gyakori forgatókönyvekhez. Ahelyett, hogy listalekérdezést használna ezekhez, meghívhatja a [Feladatszámlálók és][rest_get_task_counts] a [Listacsomók száma][rest_get_node_counts] műveleteket. Ezek a műveletek hatékonyabbak, mint a listalekérdezések, de korlátozottabb információkat adnak vissza. Lásd: [Feladatok és számítási csomópontok száma államonként.](batch-get-resource-counts.md) 


## <a name="meet-the-detaillevel"></a>Ismerje meg a DetailLevel
Egy éles batch alkalmazás entitások, például a feladatok, feladatok és számítási csomópontok száma ezres. Amikor adatokat kér ezekről az erőforrásokról, egy potenciálisan nagy mennyiségű adatnak kell "átlépnie a vezetéket" a Batch szolgáltatásból az alkalmazásba minden egyes lekérdezésnél. A lekérdezés által visszaadott elemek számának és típusának korlátozásával növelheti a lekérdezések sebességét, és ezáltal az alkalmazás teljesítményét.

Ez [a Batch .NET][api_net] API-kódkódrészlet *felsorolja a* feladathoz társított összes feladatot, valamint az egyes feladatok *összes* tulajdonságát:

```csharp
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks =
    batchClient.JobOperations.ListTasks("job-001");
```

Sokkal hatékonyabb listalekérdezést azonban "részletszint" alkalmazásával hajthat végre a lekérdezésre. Ehhez adja meg az [ODATADetailLevel][odata] objektumot a [JobOperations.ListTasks][net_list_tasks] metódusnak. Ez a kódrészlet csak a befejezett feladatok azonosítóját, parancssori és számítási csomópontinformáció-tulajdonságait adja vissza:

```csharp
// Configure an ODATADetailLevel specifying a subset of tasks and
// their properties to return
ODATADetailLevel detailLevel = new ODATADetailLevel();
detailLevel.FilterClause = "state eq 'completed'";
detailLevel.SelectClause = "id,commandLine,nodeInfo";

// Supply the ODATADetailLevel to the ListTasks method
IPagedEnumerable<CloudTask> completedTasks =
    batchClient.JobOperations.ListTasks("job-001", detailLevel);
```

Ebben a példában, ha több ezer feladat van a feladatban, a második lekérdezés eredményei általában sokkal gyorsabban lesznek visszaadva, mint az első. Az [ALÁBBIAKBAN](#efficient-querying-in-batch-net)további információt talál az ODATADetailLevel használatáról a Batch .NET API-val való listaelemek listázásakor.

> [!IMPORTANT]
> Javasoljuk, hogy *mindig* adja meg az ODATADetailLevel objektumot a .NET API-listahívásaihoz az alkalmazás maximális hatékonyságának és teljesítményének biztosítása érdekében. A részletességi szint megadásával csökkentheti a Batch szolgáltatás válaszidejét, javíthatja a hálózati kihasználtságot, és minimalizálhatja az ügyfélalkalmazások memóriahasználatát.
> 
> 

## <a name="filter-select-and-expand"></a>Szűrés, kijelölés és kibontás
A [Batch .NET][api_net] és [a Batch REST][api_rest] API-k lehetővé teszik a listában visszaadott elemek számának, valamint az egyes cikkekhez visszaadott információk mennyiségének csökkentését. Ezt a listalekérdezések végrehajtásakor **szűrő,** **kijelölés**és **karakterláncok kibontásával hajthatja** végre.

### <a name="filter"></a>Szűrés
A szűrőkarakterlánc olyan kifejezés, amely csökkenti a visszaadott elemek számát. Például csak a feladat futó feladatait sorolja fel, vagy csak azokat a számítási csomópontokat, amelyek készen állnak a feladatok futtatására.

* A szűrőkarakterlánc egy vagy több kifejezésből áll, egy tulajdonságnévből, operátorból és értékből álló kifejezéssel. A megadható tulajdonságok az egyes lekért entitástípusokra, valamint az egyes tulajdonságokhoz támogatott operátorokra vonatkoznak.
* Több kifejezés kombinálható a logikai `and` operátorok és `or`használatával.
* Ez a példa szűrőkarakterlánc csak a `(state eq 'running') and startswith(id, 'renderTask')`futó "renderelési" feladatokat sorolja fel: .

### <a name="select"></a>Válassza ezt:
A select string korlátozza az egyes elemekhez visszaadott tulajdonságértékeket. Megad egy tulajdonságlistát, és csak ezeket a tulajdonságértékeket adja vissza a lekérdezés eredményében szereplő elemekhez.

* A select string a tulajdonságnevek vesszővel tagolt listájából áll. A lekérdező entitástípus bármely tulajdonságát megadhatja.
* Ez a példa választókarakterlánc azt adja meg, hogy `id, state, stateTransitionTime`minden feladathoz csak három tulajdonságértéket kell visszaadni: .

### <a name="expand"></a>Kibontás
A kibontási karakterlánc csökkenti az egyes információk megszerzéséhez szükséges API-hívások számát. Kibontási karakterlánc használata esetén az egyes elemekről további információk szerezhetők be egyetlen API-hívással. Ahelyett, hogy először beszerezné az entitások listáját, majd információt kérne a lista minden eleméről, egy kibontási karakterláncot használ, hogy ugyanazokat az információkat egyetlen API-hívásban szerezze be. A kevesebb API-hívás jobb teljesítményt jelent.

* A select karakterlánchoz hasonlóan a kibontó karakterlánc is azt szabályozza, hogy bizonyos adatok szerepeljenek-e a listalekérdezés eredményében.
* A kibontási karakterlánc csak akkor támogatott, ha feladatok, munkatervek, feladatok és készletek listázására használják. Jelenleg csak statisztikai információkat támogat.
* Ha az összes tulajdonság szükséges, és nincs megadva kijelölési karakterlánc, a kibontási karakterláncot *kell* használni a statisztikai adatok leéséhez. Ha egy select karakterláncot használ a tulajdonságok `stats` egy részhalmazának megszerzéséhez, akkor megadható a select karakterláncban, és a kibontási karakterláncot nem kell megadni.
* Ez a példa kibontó karakterlánc azt határozza meg, hogy `stats`a lista minden elemére statisztikai adatokat kell visszaadni: .

> [!NOTE]
> A három lekérdezési karakterlánc-típus (szűrő, kijelölés és kibontás) bármelyikének összeállításakor gondoskodnia kell arról, hogy a tulajdonságnevek és az eset megegyezzen a REST API-elem megfelelőivel. Ha például a .NET [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask) osztállyal dolgozik, akkor az **állapot**helyett **az állapotot** kell megadnia, még akkor is, ha a .NET tulajdonság [a CloudTask.State](/dotnet/api/microsoft.azure.batch.cloudtask.state#Microsoft_Azure_Batch_CloudTask_State). Az alábbi táblázatokban a .NET és a REST API-k közötti tulajdonságleképezéseket talál.
> 
> 

### <a name="rules-for-filter-select-and-expand-strings"></a>Karakterláncok szűrési, kijelölési és kibontási szabályai
* A szűrőben szereplő tulajdonságneveknek ugyanúgy kell megjelenniük a [Batch REST][api_rest] API-ban – még akkor is, ha a [Batch .NET][api_net] vagy a többi Batch SDK-t használja.
* Minden tulajdonságnév ben a kis- és nagybetűk et nem lehet figyelembe vevő, de a tulajdonságértékek nem különböznek a kis- és nagybetűktől.
* A dátum-/időkarakterláncok két formátum egyike lehetnek, `DateTime`és a program előtt meg kell előzni.
  
  * Példa A W3C-DTF formátumra:`creationTime gt DateTime'2011-05-08T08:49:37Z'`
  * Példa az RFC 1123 formátumára:`creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`
* A logikai karakterláncok vagy `true` vagy `false`.
* Ha érvénytelen tulajdonságot vagy operátort `400 (Bad Request)` adott meg, hiba lép fel.

## <a name="efficient-querying-in-batch-net"></a>Hatékony lekérdezés a Batch .NET-ben
A [Batch .NET][api_net] API-n belül az [ODATADetailLevel][odata] osztály szűrőt biztosít, kiválasztja és kibontja a karakterláncokat a műveletek listázásához. Az ODataDetailLevel osztály három nyilvános karakterlánc-tulajdonsággal rendelkezik, amelyek megadhatók a konstruktorban, vagy közvetlenül az objektumon állíthatók be. Ezután az ODataDetailLevel objektumot paraméterként adja át a különböző listaműveleteknek, például a [ListPools][net_list_pools], [a ListJobs][net_list_jobs]és a [ListTasks műveleteknek.][net_list_tasks]

* [ODATADetailLevel][odata]. [FilterClause][odata_filter]: A visszaadott elemek számának korlátozása.
* [ODATADetailLevel][odata]. [SelectClause][odata_select]: Adja meg, hogy az egyes cikkekhez mely tulajdonságértékeket adja vissza a rendszer.
* [ODATADetailLevel][odata]. [ExpandClause][odata_expand]: Az egyes elemekre vonatkozó külön hívások helyett egyetlen API-hívás összes elemének adatainak lekérése.

A következő kódrészlet a Batch .NET API-t használja a Batch szolgáltatás hatékony lekérdezéséhez egy adott készlet statisztikáihoz. Ebben a forgatókönyvben a Batch-felhasználó teszt- és éleskészletekkel is rendelkezik. A tesztkészlet-azonosítók előtaggal vannak ellátva a "teszt"- vel, a termelési készlet azonosítói pedig "prod" előtaggal. A kódrészletben a *myBatchClient* a [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient) osztály megfelelően inicializált példánya.

```csharp
// First we need an ODATADetailLevel instance on which to set the filter, select,
// and expand clause strings
ODATADetailLevel detailLevel = new ODATADetailLevel();

// We want to pull only the "test" pools, so we limit the number of items returned
// by using a FilterClause and specifying that the pool IDs must start with "test"
detailLevel.FilterClause = "startswith(id, 'test')";

// To further limit the data that crosses the wire, configure the SelectClause to
// limit the properties that are returned on each CloudPool object to only
// CloudPool.Id and CloudPool.Statistics
detailLevel.SelectClause = "id, stats";

// Specify the ExpandClause so that the .NET API pulls the statistics for the
// CloudPools in a single underlying REST API call. Note that we use the pool's
// REST API element name "stats" here as opposed to "Statistics" as it appears in
// the .NET API (CloudPool.Statistics)
detailLevel.ExpandClause = "stats";

// Now get our collection of pools, minimizing the amount of data that is returned
// by specifying the detail level that we configured above
List<CloudPool> testPools =
    await myBatchClient.PoolOperations.ListPools(detailLevel).ToListAsync();
```

> [!TIP]
> Az [ODATADetailLevel][odata] select és expand záradékokkal konfigurált példánya is átadható a megfelelő Get metódusnak, például [a PoolOperations.GetPool](/dotnet/api/microsoft.azure.batch.pooloperations.getpool#Microsoft_Azure_Batch_PoolOperations_GetPool_System_String_Microsoft_Azure_Batch_DetailLevel_System_Collections_Generic_IEnumerable_Microsoft_Azure_Batch_BatchClientBehavior__)metódusnak a visszaadott adatok mennyiségének korlátozása érdekében.
> 
> 

## <a name="batch-rest-to-net-api-mappings"></a>A .NET API-hozzárendelések batch REST-hez rendelése
A szűrőben, a kijelölésben és a kibontásban lévő *tulajdonságneveknek* névben és esetben is tükrözniük kell a REST API megfelelőit. Az alábbi táblázatok a .NET és a REST API megfelelői közötti hozzárendeléseket biztosítják.

### <a name="mappings-for-filter-strings"></a>Szűrőkarakterláncok hozzárendelései
* **.NET listametódusok**: Ebben az oszlopban a .NET API-metódusok mindegyike egy [ODATADetailLevel][odata] objektumot fogad el paraméterként.
* **REST-listakérelmek:** Az ebben az oszlopban hivatkozott MINDEN REST API-lap tartalmaz egy táblázatot, amely meghatározza a *szűrőkarakterláncokban* engedélyezett tulajdonságokat és műveleteket. Ezeket a tulajdonságneveket és műveleteket [az ODATADetailLevel.FilterClause karakterlánc összeállításakor][odata_filter] fogja használni.

| .NET listametódusok | REST-listakérések |
| --- | --- |
| [CertificateOperations.ListCertificates][net_list_certs] |[A tanúsítványok listázása egy fiókban][rest_list_certs] |
| [CloudTask.ListNodeFiles][net_list_task_files] |[Feladathoz társított fájlok listázása][rest_list_task_files] |
| [JobOperations.ListJobPreparation/ReleaseTaskStatus][net_list_jobprep_status] |[Feladat-előkészítési és feladatkiadási feladatok állapotának listázása egy feladathoz][rest_list_jobprep_status] |
| [JobOperations.ListJobs][net_list_jobs] |[A feladatok listázása egy fiókban][rest_list_jobs] |
| [JobOperations.ListNodeFiles][net_list_nodefiles] |[A fájlok listázása egy csomóponton][rest_list_nodefiles] |
| [JobOperations.ListTasks][net_list_tasks] |[Feladathoz társított feladatok felsorolása][rest_list_tasks] |
| [JobScheduleOperations.ListJobSchedules][net_list_job_schedules] |[A feladatütemezések listázása egy partnerben][rest_list_job_schedules] |
| [JobScheduleOperations.ListJobs][net_list_schedule_jobs] |[A feladatütemezéshez társított feladatok listázása][rest_list_schedule_jobs] |
| [PoolOperations.ListComputeNodes][net_list_compute_nodes] |[A készlet számítási csomópontjainak listázása][rest_list_compute_nodes] |
| [PoolOperations.ListPools][net_list_pools] |[A fiók készleteinek listázása][rest_list_pools] |

### <a name="mappings-for-select-strings"></a>Leképezések kiválasztott karakterláncokhoz
* **Batch .NET típusok**: Batch .NET API-típusok.
* **REST API-entitások:** Ebben az oszlopban minden lap tartalmaz egy vagy több táblát, amely felsorolja a REST API-tulajdonság neveket a típushoz. Ezek a tulajdonságnevek *a kiválasztott* karakterláncok összeállításakor használatosak. Ugyanezeket a tulajdonságneveket fogja használni [az ODATADetailLevel.SelectClause][odata_select] karakterlánc összeállításakor.

| Köteg .NET típusok | REST API-entitások |
| --- | --- |
| [Tanúsítvány][net_cert] |[Információ a tanúsítványról][rest_get_cert] |
| [CloudJob (Felhőfeladat)][net_job] |[Információ a munkáról][rest_get_job] |
| [CloudJobÜtemezés][net_schedule] |[Információ a munkaütemezésről][rest_get_schedule] |
| [Számítási csomópont][net_node] |[Információ a csomópontról][rest_get_node] |
| [CloudPool][net_pool] |[Információ a készletről][rest_get_pool] |
| [CloudTask][net_task] |[Információ beszerezése egy feladatról][rest_get_task] |

## <a name="example-construct-a-filter-string"></a>Példa: szűrőkarakterlánc összeállítása
Ha az [ODATADetailLevel.FilterClause][odata_filter]szűrőkarakterláncot hoz létre, a fenti táblázatban a "Szűrőkarakterláncok hozzárendelése" területen találja meg a REST API dokumentációs lapját, amely megfelel a végrehajtani kívánt listaműveletnek. A szűrhető tulajdonságokat és azok támogatott operátorait az oldal első többsoros táblázatában találja. Ha például minden olyan feladatot be szeretne olvasni, amelynek kilépési kódja nem nulla, akkor [a Feladathoz társított feladatok listázása című][rest_list_tasks] sor ban a megfelelő tulajdonságkarakterláncot és a megengedett operátorokat adja meg:

| Tulajdonság | Engedélyezett műveletek | Típus |
|:--- |:--- |:--- |
| `executionInfo/exitCode` |`eq, ge, gt, le , lt` |`Int` |

Így a nem nulla kilépési kóddal rendelkező összes feladat listázásához a szűrőkarakterlánc a következő lenne:

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

## <a name="example-construct-a-select-string"></a>Példa: select string összeállítása
Az [ODATADetailLevel.SelectClause][odata_select]létrehozásához olvassa el a fenti táblázatot a "Leképezések a kiválasztott karakterláncokhoz" alatt, és keresse meg a REST API-lapot, amely megfelel a felsorolt entitás típusának. A választható tulajdonságokat és azok támogatott operátorait az oldal első többsoros táblázatában találja. Ha például csak az azonosítót és a parancssort szeretné beolvasni egy lista minden feladatához, akkor ezeket a sorokat a [Feladattal kapcsolatos információk kérése][rest_get_task]című táblázatban találja:

| Tulajdonság | Típus | Megjegyzések |
|:--- |:--- |:--- |
| `id` |`String` |`The ID of the task.` |
| `commandLine` |`String` |`The command line of the task.` |

A kiválasztott karakterlánc, amely csak az azonosítót és a parancssort tartalmazza az egyes felsorolt feladatokhoz, a következő lesz:

`id, commandLine`

## <a name="code-samples"></a>Kódminták
### <a name="efficient-list-queries-code-sample"></a>Hatékony listalekérdezési kódminta
Tekintse meg a [EfficientListQueries][efficient_query_sample] mintaprojektet a GitHubon, hogy megtudja, a hatékony listalekérdezés milyen hatással lehet az alkalmazások teljesítményére. Ez a C# konzolalkalmazás nagyszámú feladatot hoz létre és ad hozzá egy feladathoz. Ezután több hívást kezdeményez a [JobOperations.ListTasks][net_list_tasks] metódushoz, és átadja a különböző tulajdonságértékekkel konfigurált [ODATADetailLevel][odata] objektumokat a visszaadandó adatok mennyiségének módosítására. A következőhöz hasonló kimenetet állít elő:

```
Adding 5000 tasks to job jobEffQuery...
5000 tasks added in 00:00:47.3467587, hit ENTER to query tasks...

4943 tasks retrieved in 00:00:04.3408081 (ExpandClause:  | FilterClause: state eq 'active' | SelectClause: id,state)
0 tasks retrieved in 00:00:00.2662920 (ExpandClause:  | FilterClause: state eq 'running' | SelectClause: id,state)
59 tasks retrieved in 00:00:00.3337760 (ExpandClause:  | FilterClause: state eq 'completed' | SelectClause: id,state)
5000 tasks retrieved in 00:00:04.1429881 (ExpandClause:  | FilterClause:  | SelectClause: id,state)
5000 tasks retrieved in 00:00:15.1016127 (ExpandClause:  | FilterClause:  | SelectClause: id,state,environmentSettings)
5000 tasks retrieved in 00:00:17.0548145 (ExpandClause: stats | FilterClause:  | SelectClause: )

Sample complete, hit ENTER to continue...
```

Ahogy az eltelt időkben látható, jelentősen csökkentheti a lekérdezésválaszi időket a tulajdonságok és a visszaadott elemek számának korlátozásával. Ezt és más mintaprojekteket a GitHub [azure-batch-samples][github_samples] tárházában talál.

### <a name="batchmetrics-library-and-code-sample"></a>BatchMetrics-tár és kódminta
A fenti EfficientListQueries-kódminta mellett a [BatchMetrics][batch_metrics] projekt et az [azure-batch-samples][github_samples] GitHub-tárházban is megtalálhatja. A BatchMetrics mintaprojekt bemutatja, hogyan lehet hatékonyan figyelni az Azure Batch feladat előrehaladását a Batch API használatával.

A [BatchMetrics][batch_metrics] minta tartalmaz egy .NET osztálykönyvtár-projektet, amelyet beépíthet a saját projektjeibe, valamint egy egyszerű parancssori programot a tár használatának gyakorlására és bemutatására.

A projekten belüli mintaalkalmazás a következő műveleteket mutatja be:

1. Adott attribútumok kiválasztása, hogy csak a szükséges tulajdonságokat töltse le
2. Szűrés az állapotáttűnési időken, hogy csak az utolsó lekérdezés óta végrehajtott módosításokat töltse le

Például a következő módszer jelenik meg a BatchMetrics könyvtárban. Egy ODATADetailLevel értéket ad vissza, amely azt adja meg, hogy csak a `id` lekérdezett entitásokhoz és `state` tulajdonságokat kell beszerezni. Azt is előírja, hogy csak azokat az `DateTime` entitásokat kell visszaadni, amelyek állapota a megadott paraméter óta megváltozott.

```csharp
internal static ODATADetailLevel OnlyChangedAfter(DateTime time)
{
    return new ODATADetailLevel(
        selectClause: "id, state",
        filterClause: string.Format("stateTransitionTime gt DateTime'{0:o}'", time)
    );
}
```

## <a name="next-steps"></a>További lépések
### <a name="parallel-node-tasks"></a>Párhuzamos csomópontfeladatok
[Az Azure Batch számítási erőforrás-használatának maximalizálása egyidejű csomópontfeladatokkal](batch-parallel-node-tasks.md) egy másik, a Batch-alkalmazás teljesítményével kapcsolatos cikk. Bizonyos típusú számítási feladatok hasznoslehet párhuzamos feladatok végrehajtása nagyobb – de kevesebb számítási csomópontokon. Tekintse meg a [példa forgatókönyv](batch-parallel-node-tasks.md#example-scenario) a cikkben az ilyen forgatókönyv részleteit.


[api_net]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: https://docs.microsoft.com/rest/api/batchservice/
[batch_metrics]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchMetrics
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

[rest_get_task_counts]: /rest/api/batchservice/get-the-task-counts-for-a-job
[rest_get_node_counts]: /rest/api/batchservice/account/listpoolnodecounts
