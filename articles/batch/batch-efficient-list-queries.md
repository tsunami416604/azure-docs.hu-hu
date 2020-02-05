---
title: Hatékony lista-lekérdezések tervezése – Azure Batch | Microsoft Docs
description: Növelje a teljesítményt a lekérdezések szűrésével, amikor adatokat kér a Batch-erőforrásokról, például a készletekről, a feladatokról, a tevékenységekről és a számítási csomópontokról.
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
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022239"
---
# <a name="create-queries-to-list-batch-resources-efficiently"></a>Lekérdezések létrehozása a Batch-erőforrások hatékony listázásához

Itt megtudhatja, hogyan növelheti a Azure Batch alkalmazása teljesítményét azáltal, hogy csökkenti a szolgáltatás által visszaadott adatok mennyiségét, amikor a [Batch .net][api_net] -kódtár segítségével lekérdezi a feladatokat, a feladatokat, a számítási csomópontokat és más erőforrásokat.

Szinte minden batch-alkalmazásnak valamilyen típusú figyelést vagy más műveletet kell végrehajtania, amely gyakran rendszeres időközönként lekérdezi a Batch szolgáltatást. Ha például meg szeretné állapítani, hogy vannak-e a feladatokban egy várólistára helyezett feladat, akkor a feladat minden feladatához le kell kérnie az összes feladatot. A készletben lévő csomópontok állapotának meghatározásához a készlet minden csomópontján be kell szereznie az adatait. Ez a cikk bemutatja, hogyan hajthatja végre az ilyen lekérdezéseket a leghatékonyabb módon.

> [!NOTE]
> A Batch szolgáltatás speciális API-támogatást biztosít a feladatok feladatainak számlálására, valamint a számítási csomópontok számlálására a Batch-készletben. Ezeknek a listáknak a lekérése helyett a [feladatok beolvasása][rest_get_task_counts] és a [lista készlet csomópontok számlálása][rest_get_node_counts] műveletekkel hívható meg. Ezek a műveletek hatékonyabbak, mint a lista lekérdezése, de csak korlátozott mennyiségű adatot adnak vissza. Lásd: [a feladatok és a számítási csomópontok állapot szerinti számlálása](batch-get-resource-counts.md). 


## <a name="meet-the-detaillevel"></a>A DetailLevel teljesítve
Az éles batch-alkalmazásokban a feladatok, feladatok és számítási csomópontok több ezren is megadhatók. Amikor információt kér ezekről az erőforrásokról, egy potenciálisan nagy mennyiségű adatnak "át kell haladnia a drótot" a Batch szolgáltatásból az alkalmazásba az egyes lekérdezéseken. A lekérdezés által visszaadott elemek számának és típusának korlátozásával növelheti a lekérdezések sebességét, és így az alkalmazás teljesítménye is.

Ez a [Batch .net][api_net] API-kódrészlet *felsorolja a feladatokhoz* társított összes feladatot, valamint az egyes feladatok *összes* tulajdonságát:

```csharp
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks =
    batchClient.JobOperations.ListTasks("job-001");
```

Ha azonban a lekérdezéshez egy "részletességi szintet" alkalmaz, egy sokkal hatékonyabb lista-lekérdezést is végrehajthat. Ezt úgy teheti meg, hogy egy [ODATADetailLevel][odata] objektumot biztosít a [JobOperations. ListTasks][net_list_tasks] metódusnak. Ez a kódrészlet csak a Befejezett feladatok AZONOSÍTÓját, parancssorát és számítási csomópontjának információit adja vissza:

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

Ebben a példában, ha több ezer feladat van a feladatban, a második lekérdezés eredményei általában sokkal gyorsabban lesznek visszaadva, mint az első. További információ a ODATADetailLevel használatáról: a Batch .NET API-val rendelkező elemek listázása [alább](#efficient-querying-in-batch-net)található.

> [!IMPORTANT]
> Javasoljuk, hogy az alkalmazás maximális hatékonyságának és teljesítményének biztosítása érdekében *mindig* adjon meg egy ODATADetailLevel-objektumot a .NET API-hívások listájához. A Részletezési szint megadásával csökkentheti a Batch szolgáltatás válaszideje, javíthatja a hálózat kihasználtságát, és csökkentheti az ügyfélalkalmazások által felhasznált memóriát.
> 
> 

## <a name="filter-select-and-expand"></a>Szűrés, kijelölés és Kibontás
A [Batch .net][api_net] és a [Batch Rest][api_rest] API-k lehetővé teszik a listában visszaadott elemek számának, valamint a visszaadott adatok mennyiségének csökkentését. Ehhez adja meg a **szűrés**, a **kiválasztás**és a **karakterláncok kibontása** a listák lekérdezésének végrehajtásakor lehetőséget.

### <a name="filter"></a>Szűrő
A szűrő sztring egy kifejezés, amely csökkenti a visszaadott elemek számát. Például csak a feladat futási feladatait listázhatja, vagy csak olyan számítási csomópontokat listázhat, amelyek készen állnak a feladatok futtatására.

* A szűrő sztring egy vagy több kifejezésből áll, és egy olyan kifejezéssel, amely egy tulajdonság nevét, operátorát és értékét tartalmazza. A megadható tulajdonságok a lekérdezett entitások egyes típusaira jellemzőek, csakúgy, mint az egyes tulajdonságok által támogatott operátorok.
* Több kifejezés is egyesíthető a logikai operátorok `and` és `or`használatával.
* Ez a példa szűrő sztring csak a futó "render" feladatokat sorolja fel: `(state eq 'running') and startswith(id, 'renderTask')`.

### <a name="select"></a>Válassza ezt:
A Select sztring korlátozza az egyes elemekhez visszaadott tulajdonságértékek értékét. Megadhatja a tulajdonságlapok listáját, és csak ezek a tulajdonságértékek lesznek visszaadva a lekérdezés eredményeiben lévő elemekhez.

* A Select sztring a tulajdonságok neveinek vesszővel tagolt listáját tartalmazza. Megadhatja a lekérdezni kívánt entitás típusának bármelyik tulajdonságát.
* Ez a példa a sztring kiválasztásával meghatározza, hogy az egyes feladatokhoz csak három tulajdonságérték legyen visszaadva: `id, state, stateTransitionTime`.

### <a name="expand"></a>Kibontás
A kibontási karakterlánc csökkenti az egyes információk beszerzéséhez szükséges API-hívások számát. Ha kibontott karakterláncot használ, az egyes elemekre vonatkozó további információkat egyetlen API-hívással lehet beszerezni. Az entitások listájának első beszerzése helyett, majd a lista egyes elemeire vonatkozó információkat kér le, a kibontási karakterlánc használatával egyetlen API-hívásban szerezheti be ugyanezeket az adatokat. A kevésbé API-hívások jobb teljesítményt jelentenek.

* A Select sztringhez hasonlóan a kibontási karakterlánc azt is meghatározza, hogy bizonyos adatmennyiség szerepel-e a lista lekérdezési eredményei között.
* A kibontási karakterlánc csak akkor támogatott, ha a feladatok, a feladatütemezés, a feladatok és a készletek listájában használatos. Jelenleg csak a statisztikai adatokat támogatja.
* Ha az összes tulajdonság megadása kötelező, és nincs megadva a Select sztring, a kibontási *karakterláncot kell használni* a statisztikai adatok lekérdezéséhez. Ha egy Select sztringet használ a tulajdonságok egy részhalmazának beszerzésére, akkor `stats` megadható a Select karakterláncban, és a kibontási karakterláncot nem kell megadni.
* Ez a példa a karakterlánc kibontásával meghatározza, hogy a statisztikai adatokat vissza kell-e adni a lista egyes elemeihez: `stats`.

> [!NOTE]
> A három lekérdezési karakterlánc típusának (Filter, Select és Expand) összeállításakor győződjön meg arról, hogy a tulajdonságok nevei és az eset megegyezik a REST API elemével. Ha például a .NET [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask) osztállyal dolgozik, **az állapot helyett**az **állapotot** kell megadnia, annak ellenére, hogy a .net-tulajdonság [CloudTask. State](/dotnet/api/microsoft.azure.batch.cloudtask.state#Microsoft_Azure_Batch_CloudTask_State). Tekintse meg az alábbi táblázatokat a .NET és a REST API-k közötti tulajdonság-hozzárendelésekhez.
> 
> 

### <a name="rules-for-filter-select-and-expand-strings"></a>A szűrésre, kiválasztásra és a karakterláncok kibontására vonatkozó szabályok
* A Filter, a Select és a Expand sztring tulajdonságok nevei a [Batch Rest][api_rest] API-ban is megjelennek, még akkor is, ha a Batch [.net][api_net] vagy egy másik batch SDK-t használ.
* Az összes tulajdonságnév megkülönbözteti a kis-és nagybetűket, de a tulajdonságértékek a kis-és nagybetűk megkülönböztetése.
* A dátum/idő karakterláncok két formátum egyike lehet, és a `DateTime`előtt szerepelnie kell.
  
  * W3C-DTF Format példa: `creationTime gt DateTime'2011-05-08T08:49:37Z'`
  * RFC 1123 formátumú példa: `creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`
* A logikai karakterláncok `true` vagy `false`.
* Ha érvénytelen tulajdonság vagy operátor van megadva, a rendszer `400 (Bad Request)` hibát eredményez.

## <a name="efficient-querying-in-batch-net"></a>Hatékony lekérdezés a Batch .NET-ben
A [Batch .net][api_net] API-n belül a [ODATADetailLevel][odata] osztály a szűrők megadására, a kiválasztására és a karakterláncok kibontására szolgál a műveletek listázásához. A ODataDetailLevel osztály három nyilvános karakterlánc-tulajdonságot tartalmaz, amelyek megadhatók a konstruktorban, vagy közvetlenül az objektumon is megadhatók. Ezután a ODataDetailLevel objektumot paraméterként adja át a különböző lista-műveletekhez, például a [ListPools][net_list_pools], a [ListJobs][net_list_jobs]és a [ListTasks][net_list_tasks].

* [ODATADetailLevel][odata]. [FilterClause][odata_filter]: korlátozza a visszaadott elemek számát.
* [ODATADetailLevel][odata]. [SelectClause][odata_select]: adja meg, hogy mely tulajdonságértékek legyenek visszaadva az egyes elemeknél.
* [ODATADetailLevel][odata]. [ExpandClause][odata_expand]: egyetlen API-hívásban lévő összes elemre vonatkozó adatok lekérése az egyes elemek külön hívása helyett.

A következő kódrészlet a Batch .NET API használatával hatékonyan kérdezi le a Batch szolgáltatást egy adott készlet statisztikájában. Ebben az esetben a Batch-felhasználónak tesztelési és üzemi készletek is vannak. A test Pool-azonosítók "test" előtaggal vannak ellátva, és a termelési készlet azonosítói a "Prod" előtaggal vannak ellátva. A kódrészletben a *myBatchClient* a [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient) osztály megfelelően inicializált példánya.

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
> A Select és Expand záradékokkal konfigurált [ODATADetailLevel][odata] -példányok is átadhatók a megfelelő Get metódusoknak (például [PoolOperations. GetPool)](/dotnet/api/microsoft.azure.batch.pooloperations.getpool#Microsoft_Azure_Batch_PoolOperations_GetPool_System_String_Microsoft_Azure_Batch_DetailLevel_System_Collections_Generic_IEnumerable_Microsoft_Azure_Batch_BatchClientBehavior__)a visszaadott adatmennyiség korlátozása érdekében.
> 
> 

## <a name="batch-rest-to-net-api-mappings"></a>Batch – REST – .NET API-hozzárendelések
A szűrőben, a Select és a Expand karakterláncban szereplő tulajdonságok neveinek a neve és a Case kifejezésnek *kell megegyezniük* a REST API társaival. Az alábbi táblázatok a .NET-és REST API-partnerek közötti leképezéseket biztosítanak.

### <a name="mappings-for-filter-strings"></a>Szűrési karakterláncok megfeleltetése
* **.Net-lista módszerei**: ebben az oszlopban található összes .NET API-metódus egy [ODATADetailLevel][odata] objektumot fogad el paraméterként.
* **Rest-listákra vonatkozó kérelmek**: az ebben az oszlopban található összes REST API-oldal tartalmaz egy táblázatot, amely meghatározza a *szűrő* sztringekben engedélyezett tulajdonságokat és műveleteket. Ezeket a tulajdonságokat és műveleteket fogja használni, amikor [ODATADetailLevel. FilterClause][odata_filter] karakterláncot hoz létre.

| .NET-lista módszerei | REST-lista kérései |
| --- | --- |
| [CertificateOperations. ListCertificates][net_list_certs] |[Fiókban lévő tanúsítványok listázása][rest_list_certs] |
| [CloudTask. ListNodeFiles][net_list_task_files] |[Feladathoz társított fájlok listázása][rest_list_task_files] |
| [JobOperations. ListJobPreparationAndReleaseTaskStatus][net_list_jobprep_status] |[Feladathoz tartozó feladat-előkészítési és feladat-kiadási feladatok állapotának listázása][rest_list_jobprep_status] |
| [JobOperations. ListJobs][net_list_jobs] |[A fiókban lévő feladatok listázása][rest_list_jobs] |
| [JobOperations. ListNodeFiles][net_list_nodefiles] |[Csomóponton található fájlok listázása][rest_list_nodefiles] |
| [JobOperations. ListTasks][net_list_tasks] |[Feladathoz társított feladatok listázása][rest_list_tasks] |
| [JobScheduleOperations.ListJobSchedules][net_list_job_schedules] |[A fiókban lévő feladatok ütemtervének listázása][rest_list_job_schedules] |
| [JobScheduleOperations.ListJobs][net_list_schedule_jobs] |[A feladatütemezés által hozzárendelt feladatok listázása][rest_list_schedule_jobs] |
| [PoolOperations. ListComputeNodes][net_list_compute_nodes] |[A készletben lévő számítási csomópontok listázása][rest_list_compute_nodes] |
| [PoolOperations. ListPools][net_list_pools] |[A fiókban lévő készletek listázása][rest_list_pools] |

### <a name="mappings-for-select-strings"></a>A kiválasztott karakterláncok megfeleltetése
* **Batch .net-típusok**: Batch .NET API-típusok.
* **REST API entitások**: ebben az oszlopban minden oldal egy vagy több olyan táblát tartalmaz, amely a típus REST API tulajdonságának nevét listázza. Ezeket a tulajdonságokat a *Select* sztringek összeállításakor használja a rendszer. Ugyanezeket a tulajdonságokat fogja használni, amikor [ODATADetailLevel. SelectClause][odata_select] karakterláncot hoz létre.

| Batch .NET-típusok | Entitások REST API |
| --- | --- |
| [Tanúsítvány][net_cert] |[Tanúsítvány adatainak beolvasása][rest_get_cert] |
| [CloudJob][net_job] |[Feladattal kapcsolatos információk lekérése][rest_get_job] |
| [CloudJobSchedule][net_schedule] |[Feladatütemezés adatainak beolvasása][rest_get_schedule] |
| [ComputeNode][net_node] |[Csomópont adatainak beolvasása][rest_get_node] |
| [CloudPool][net_pool] |[Készlet adatainak beolvasása][rest_get_pool] |
| [CloudTask][net_task] |[Feladat adatainak beolvasása][rest_get_task] |

## <a name="example-construct-a-filter-string"></a>Példa: szűrő sztring létrehozása
Amikor [ODATADetailLevel. FilterClause][odata_filter]-hoz hoz létre egy szűrő sztringet, tekintse meg a fenti táblázatot a "szűrési karakterláncok leképezése" alatt, és keresse meg az REST API dokumentációs oldalát, amely megfelel a végrehajtani kívánt lista műveletnek. A szűrhető tulajdonságokat és a támogatott operátorokat a lap első MultiRow táblázatában találja. Ha szeretné lekérdezni az összes olyan feladatot, amelynek kilépési kódja nem nulla, például egy adott feladathoz [társított feladat][rest_list_tasks] meghatározza a megfelelő tulajdonság-karakterláncot és a megengedett operátorokat:

| Tulajdonság | Engedélyezett műveletek | Type (Típus) |
|:--- |:--- |:--- |
| `executionInfo/exitCode` |`eq, ge, gt, le , lt` |`Int` |

Így a nullától eltérő kilépési kóddal rendelkező összes tevékenység listázására szolgáló szűrő sztring a következő lesz:

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

## <a name="example-construct-a-select-string"></a>Példa: válasszon ki egy Select sztringet
A [ODATADetailLevel. SelectClause][odata_select]létrehozásához tekintse meg a fenti táblázatot a "Select Strings (hozzárendelések a kiválasztott karakterláncok számára)" szakaszban, és navigáljon a REST API oldalra, amely megfelel a listán szereplő entitás típusának. A kiválasztható tulajdonságokat és a támogatott operátorokat a lap első MultiRow táblázatában találja. Ha például csak az azonosítót és a parancssort szeretné lekérdezni a listában szereplő egyes feladatokhoz, a következő sorok szerepelnek a megfelelő táblázatban a [feladattal kapcsolatos információk lekéréséhez][rest_get_task]:

| Tulajdonság | Type (Típus) | Megjegyzések |
|:--- |:--- |:--- |
| `id` |`String` |`The ID of the task.` |
| `commandLine` |`String` |`The command line of the task.` |

A Select sztring, amely csak az azonosítót és a parancssort tartalmazza, és mindegyik felsorolt feladathoz a következő lesz:

`id, commandLine`

## <a name="code-samples"></a>Kódminták
### <a name="efficient-list-queries-code-sample"></a>Hatékony lista lekérdezési kódjának mintája
Tekintse meg a [EfficientListQueries][efficient_query_sample] -minta projektet a githubon, hogy megtudja, mennyire befolyásolhatja az alkalmazások teljesítményét az alkalmazásokban. Ez C# a konzolszoftver nagy mennyiségű feladatot hoz létre és tesz hozzá egy feladathoz. Ezután több hívást kezdeményez a [JobOperations. ListTasks][net_list_tasks] metódushoz, és átadja a különböző tulajdonság-értékekkel konfigurált [ODATADetailLevel][odata] -objektumokat a visszaadott adatok mennyiségének a megváltoztatásához. A következőhöz hasonló kimenetet hoz létre:

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

Ahogy az eltelt időkben is látható, jelentősen csökkentheti a lekérdezési válaszidőt a tulajdonságok és a visszaadott elemek számának korlátozásával. Ezt és más mintákat az [Azure-batch-Samples][github_samples] repositoryban találja a githubon.

### <a name="batchmetrics-library-and-code-sample"></a>BatchMetrics könyvtár és mintakód
A fenti EfficientListQueries-mintakód mellett az [Azure-batch-Samples GitHub-][github_samples] tárházban találhatja meg a [BatchMetrics][batch_metrics] -projektet is. A BatchMetrics minta projekt bemutatja, hogyan lehet hatékonyan figyelni Azure Batch feladatok előrehaladását a Batch API használatával.

A [BatchMetrics][batch_metrics] minta tartalmaz egy .net-osztályhoz tartozó függvénytár-projektet, amelyet saját projektjeibe építhet be, valamint egy egyszerű parancssori programot, amely a könyvtár használatát mutatja be és ismerteti.

A projekten belüli minta alkalmazás a következő műveleteket mutatja be:

1. Adott attribútumok kiválasztása csak a szükséges tulajdonságok letöltéséhez
2. Állapot-átváltási időpontok szűrése csak a legutóbbi lekérdezés óta végrehajtott módosítások letöltéséhez

A következő metódus például a BatchMetrics-könyvtárban jelenik meg. Egy olyan ODATADetailLevel ad vissza, amely megadja, hogy csak a `id` és `state` tulajdonságokat kell beolvasni a lekérdezett entitásokhoz. Azt is meghatározza, hogy csak azok az entitások legyenek módosítva, amelyek állapota megváltozott, mert a megadott `DateTime` paramétert vissza kell adni.

```csharp
internal static ODATADetailLevel OnlyChangedAfter(DateTime time)
{
    return new ODATADetailLevel(
        selectClause: "id, state",
        filterClause: string.Format("stateTransitionTime gt DateTime'{0:o}'", time)
    );
}
```

## <a name="next-steps"></a>Következő lépések
### <a name="parallel-node-tasks"></a>Párhuzamos csomóponti feladatok
Az [egyidejű csomópont-feladatokkal rendelkező számítási erőforrások maximális Azure Batchának maximalizálása](batch-parallel-node-tasks.md) a Batch-alkalmazások teljesítményével kapcsolatos további cikk. A számítási feladatok bizonyos típusai kihasználhatják a párhuzamos feladatok végrehajtását nagyobb, de kevesebb-számítási csomóponton. Tekintse meg a [példában szereplő példát](batch-parallel-node-tasks.md#example-scenario) az adott forgatókönyv részleteinek megtekintéséhez.


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
