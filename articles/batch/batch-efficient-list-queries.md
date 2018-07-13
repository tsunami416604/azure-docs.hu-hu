---
title: Hatékony listázó lekérdezések – Azure Batch tervezése |} A Microsoft Docs
description: Teljesítmény növeléséhez a szűrést a lekérdezéseket, ha a Batch-erőforrásokat, például a készletek, feladatok, tevékenységek információkat kér, és a számítási csomópontokon.
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 031fefeb-248e-4d5a-9bc2-f07e46ddd30d
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 06/26/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6bc31e8541797930583e41fb6efbb6473cd4b894
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39004455"
---
# <a name="create-queries-to-list-batch-resources-efficiently"></a>Hozzon létre hatékony lekérdezések Batch-erőforrások listája

Itt megtudhatja, hogyan feladatok, tevékenységek, számítási csomópontok és más erőforrások lekérdezheti, ha a szolgáltatás által visszaadott adatmennyiség csökkentésével az Azure Batch-alkalmazás teljesítményének növelése érdekében a [Batch .NET] [ api_net] könyvtár.

Gyakorlatilag az összes Batch-alkalmazások kell valamilyen típusú, hogy milyen gyakran kérdezi le a Batch szolgáltatás rendszeres időközönként, figyelési vagy más művelet végrehajtásához. Például annak megállapításához, hogy vannak-e van hátra a feladat aszinkron tevékenységeket, be kell szereznie adatok minden tevékenység a feladat. Annak megállapításához, a készletben lévő csomópontok állapota, adatokat kell szereznie a készlet minden csomópontján. Ez a cikk bemutatja, hogyan hajthat végre ilyen-lekérdezéseket a lehető leghatékonyabb módon.

> [!NOTE]
> A Batch szolgáltatás speciális API-támogatást biztosít a gyakran használt esetekben számbavételi egy feladat tevékenységeit, és a Batch-készletben lévő számítási csomópontok számolását. Listalekérdezés ezek helyett meghívhatja a [beolvasása feladat száma] [ rest_get_task_counts] és [lista készlet csomópont Counts] [ rest_get_node_counts] műveletek. Ezek a műveletek még hatékonyabbak, mint listalekérdezés, de a visszaadandó több korlátozott információkat. Lásd: [feladatok száma, és a számítási csomópontok állapot szerint](batch-get-resource-counts.md). 


## <a name="meet-the-detaillevel"></a>A detaillevel paraméternek felel meg
Batch-alkalmazás egy éles környezetben entitások, például a feladatok, tevékenységek és csomópontok a több ezer száma is. Ezeket az erőforrásokat információt kér, amikor egy nagy mennyiségű adatot kell "adatbázisközi az átviteli" a Batch szolgáltatás az alkalmazás minden egyes lekérdezés. Azon elemek száma és a egy lekérdezés által visszaadott információk típusáról korlátozásával megnövelheti a lekérdezések sebességét, ezért az alkalmazás teljesítményét.

Ez [Batch .NET] [ api_net] API kódját kódrészlet listák *minden* , amelyhez társítva van egy feladatot, valamint a feladat *összes* mindegyik tevékenység tulajdonságai:

```csharp
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks =
    batchClient.JobOperations.ListTasks("job-001");
```

Sokkal hatékonyabb listalekérdezés, azonban hajthat végre egy "részletességi szint" alkalmazására a lekérdezést. Ezt az értéket egy [ODATADetailLevel] [ odata] az objektum a [JobOperations.ListTasks] [ net_list_tasks] metódust. Ez a kódrészlet csak az Azonosítót, a parancssori és a számítási csomópont információk befejezett feladatok tulajdonságait adja vissza:

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

Ebben a példában a forgatókönyvben van-e több ezer, a feladat, a második lekérdezés eredményeinek általában visszaad sokkal gyorsabb, mint az első. A Batch .NET API-val elemek listázásakor ODATADetailLevel használatáról további információt megtalálható [alább](#efficient-querying-in-batch-net).

> [!IMPORTANT]
> Kifejezetten ajánljuk, hogy Ön *mindig* adja meg a maximális hatékonyság és az alkalmazás teljesítményének biztosítása a .NET API-lista hívások ODATADetailLevel objektum. A részletességi szint megadásával segítségével csökkentheti a Batch szolgáltatás válaszidőt, a hálózathasználat, és minimalizálja a memóriahasználat az ügyfélalkalmazások által.
> 
> 

## <a name="filter-select-and-expand"></a>Szűrés, válassza ki, és bontsa ki a
A [Batch .NET] [ api_net] és [Batch REST] [ api_rest] API-k lehetővé teszi, hogy mindkét listáját, a visszaadott elemek számának csökkentése, valamint az egyes visszaadott információk mennyisége. Ezt úgy teszi **szűrő**, **kiválasztása**, és **bontsa ki a karakterláncok** listázó lekérdezések végrehajtásakor.

### <a name="filter"></a>Szűrés
A szűrési karakterláncot a rendszer egy kifejezés, amely csökkenti a visszaadott elemek száma. Például egy feladat csak a futó feladatok listája, vagy a listában csak az olyan számítási csomópontok, feladatok futtatása készen áll.

* A szűrési karakterláncot tartalmaz egy vagy több kifejezést, és a egy kifejezés, amely egy tulajdonság nevét, egy operátort és egy értékből áll. Adható meg a tulajdonságait jellemzőek minden entitás típusa, amely a lekérdezés, mivel minden egyes tulajdonság támogatott az operátorok.
* A logikai operátorok használatával több kifejezésének kombinálható is `and` és `or`.
* Ebben a példában szűrőlista karakterlánc csak a futó "renderelési" feladatok: `(state eq 'running') and startswith(id, 'renderTask')`.

### <a name="select"></a>Válassza ezt:
A select karakterlánc értékekre korlátozza a tulajdonságot, amely az egyes elemeket adja vissza. A tulajdonságnevek listájának megadását, és csak azokat a tulajdonságértékek adja vissza a lekérdezés eredményeit az elemek.

* Jelölje be a karakterlánc áll a tulajdonságnevek vesszővel tagolt listája. Megadhatja, a tulajdonságoknál a entitástípus kérdez le.
* Ebben a példában válassza karakterlánc Megadja, hogy csak három tulajdonságértékek vissza kell minden egyes feladathoz: `id, state, stateTransitionTime`.

### <a name="expand"></a>Kibontás
Expand karakterlánc csökkenti az egyes információk beszerzéséhez szükséges API-hívások számát. További információ az egyes elemek szerezhető be egy expand karakterlánc használata esetén egyetlen API hívással. Ahelyett, hogy az entitások, majd az a lista minden eleme az információt kérő listájának beszerzése a kibontás karakterláncra beszerzése egyetlen API hívással ugyanazokat az információkat használhatja. Kevesebb API-hívások azt jelenti, hogy a jobb teljesítmény érdekében.

* Hasonló jelölje be a karakterláncot, expand karakterlánc azt szabályozza, hogy bizonyos adatok szerepel-e a lista a lekérdezés eredményében.
* A kibontás karakterlánc csak akkor támogatott, listázás, feladatok, a feladatok ütemezését, a feladatok és a készletek használatban van. Jelenleg csak a támogatott statisztikai adatok.
* Ha az összes tulajdonság megadása kötelező, és nincs select karakterlánc megadva, az expand-karakterlánc *kell* statisztikai információkat kaphat. Ha a select karakterláncnak, tulajdonságok egy részének majd beszerzésére használatos `stats` a select karakterlánc adható meg, és az expand-karakterlánc nem kell megadni.
* Ebben a példában bontsa ki a karakterláncot adja meg, hogy statisztikai adatok a rendszer visszalépteti az a lista minden eleme: `stats`.

> [!NOTE]
> A három lekérdezési karakterlánc típusok létesítő (szűrés, válassza ki, és bontsa ki), győződjön meg arról, hogy a tulajdonságnevek és esetben egyezik, mint a REST API-val elem. Például, amikor a .NET használata [CloudTask](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask) osztály, meg kell adnia **állapot** helyett **állapot**, annak ellenére, hogy a .NET-tulajdonság [ CloudTask.State](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.state). Tekintse meg az alábbi táblázatokban tulajdonságleképezései a .NET és REST API-k között.
> 
> 

### <a name="rules-for-filter-select-and-expand-strings"></a>Szabályok szűrőt, válassza ki, és bontsa ki a karakterláncok
* Tulajdonságok nevei a szűrőt, válassza ki, és bontsa ki a karakterláncok meg kell jelennie, mint az a [Batch REST] [ api_rest] API – még akkor is, ha használ [Batch .NET] [ api_net]vagy valamelyik Batch SDK útján.
* Minden tulajdonságnevek megkülönböztetik a kis-és nagybetűket, de a tulajdonságértékek megkülönbözteti a kis-és nagybetű nincs megkülönböztetve.
* Dátum/idő karakterlánc lehet két formátumot, és meg kell előznie a `DateTime`.
  
  * W3C-DTF formátum. példa: `creationTime gt DateTime'2011-05-08T08:49:37Z'`
  * RFC 1123 formátum. példa: `creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`
* Logikai karakterláncok kiadásokhoz `true` vagy `false`.
* Ha egy érvénytelen vlastnost nebo operátor van megadva, a `400 (Bad Request)` hibát fog eredményezni.

## <a name="efficient-querying-in-batch-net"></a>Hatékony lekérdezése a Batch .NET-ben
Belül a [Batch .NET] [ api_net] API-t, a [ODATADetailLevel] [ odata] osztály szűrő ellátására szolgál, válassza ki, és bontsa ki a karakterláncok listázása műveletek. A ODataDetailLevel osztály konstruktorában megadott, vagy közvetlenül a objektu nastavit három nyilvános karakterlánc-tulajdonságok rendelkezik. Ezután adja át a ODataDetailLevel objektum paraméterként a különféle műveletek például [ListPools][net_list_pools], [ListJobs][net_list_jobs], és [ListTasks][net_list_tasks].

* [ODATADetailLevel][odata].[ FilterClause][odata_filter]: a visszaadott elemek száma.
* [ODATADetailLevel][odata].[ SelectClause][odata_select]: Adja meg az egyes elemek visszaadott tulajdonságértékek.
* [ODATADetailLevel][odata].[ ExpandClause][odata_expand]: kérhető le adat minden eleme egy API hívása helyett különálló hívásokat az egyes elemekhez.

A következő kódrészletet a Batch .NET API használatával kérdezi le hatékonyan az egy adott készletét készletek statisztikáit a Batch szolgáltatást. Ebben a forgatókönyvben a Batch-felhasználók számára a készletek tesztelési és éles környezetben is. A teszt címkészlet azonosítókat a "test" előtaggal van, és az éles készletet azonosítók "éles" előtagot kapnak. A kódtöredék *myBatchClient* megfelelően inicializálva példánya a [BatchClient](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient) osztály.

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
> Egy példányát [ODATADetailLevel] [ odata] konfigurált egyes és kibontás záradékok is átadható megfelelő Get módszerek, például [PoolOperations.GetPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getpool.aspx), a visszaadott adatok mennyisége korlátozza.
> 
> 

## <a name="batch-rest-to-net-api-mappings"></a>A Batch .NET API leképezések a REST
A tulajdonságnevek a szűrőt, válassza ki, és bontsa ki a karakterláncok *kell* REST API-val megfelelőik, mind a nevét és eset tükrözik. Az alábbi táblázatokban a .NET és REST API-val igényló közötti leképezéseket.

### <a name="mappings-for-filter-strings"></a>Hozzárendelések szűrő-karakterlánc
* **.NET-lista metódusokat**: a .NET API-módszer ebben az oszlopban levő fogad egy [ODATADetailLevel] [ odata] paraméterként objektum.
* **REST-listára vonatkozó kérelmek**: Ebben az oszlopban levő kapcsolódó REST API-t minden lap tartalmaz egy táblát, amely megadja a tulajdonságok és műveletek engedélyezett *szűrő* karakterláncokat. Fogja használni, ezeket a tulajdonságnevek és műveletek amikor hozhat létre egy [ODATADetailLevel.FilterClause] [ odata_filter] karakterlánc.

| .NET-metódusokat listája | REST-listára vonatkozó kérelmek |
| --- | --- |
| [CertificateOperations.ListCertificates][net_list_certs] |[Egy fiók a tanúsítványok felsorolása][rest_list_certs] |
| [CloudTask.ListNodeFiles][net_list_task_files] |[A feladathoz társított fájlok listázása][rest_list_task_files] |
| [JobOperations.ListJobPreparationAndReleaseTaskStatus][net_list_jobprep_status] |[A feladat-előkészítési és a feladatkiadási tevékenységeket-feladat állapotának listázása][rest_list_jobprep_status] |
| [JobOperations.ListJobs][net_list_jobs] |[A feladatok egy fiókot a listában][rest_list_jobs] |
| [JobOperations.ListNodeFiles][net_list_nodefiles] |[A csomóponton lévő fájlok listázása][rest_list_nodefiles] |
| [JobOperations.ListTasks][net_list_tasks] |[A feladathoz hozzárendelt feladatok felsorolása][rest_list_tasks] |
| [JobScheduleOperations.ListJobSchedules][net_list_job_schedules] |[A feladatütemezések egy fiók listázása][rest_list_job_schedules] |
| [JobScheduleOperations.ListJobs][net_list_schedule_jobs] |[A feladatütemezésbe tartozó feladatok listázása][rest_list_schedule_jobs] |
| [PoolOperations.ListComputeNodes][net_list_compute_nodes] |[Egy készletben lévő számítási csomópontok listája][rest_list_compute_nodes] |
| [PoolOperations.ListPools][net_list_pools] |[A készletekben található listában.][rest_list_pools] |

### <a name="mappings-for-select-strings"></a>Válassza ki a karakterláncok leképezéseit
* **A Batch .NET-típusok**: a Batch .NET API-típusok.
* **REST API-entitások**: Ebben az oszlopban minden oldal tartalmaz egy vagy több olyan táblát, amely a REST API-t a tulajdonságnevek a típus listából. Ezek a tulajdonságnevek hoz használt *kiválasztása* karakterláncokat. Ezek azonos tulajdonság nevét fogja használni, amikor hozhat létre egy [ODATADetailLevel.SelectClause] [ odata_select] karakterlánc.

| A Batch .NET-típusok | REST API-entitás |
| --- | --- |
| [Tanúsítvány][net_cert] |[Tanúsítvány adatainak lekérése][rest_get_cert] |
| [CloudJob][net_job] |[Egy feladat adatainak lekérése][rest_get_job] |
| [CloudJobSchedule][net_schedule] |[A feladatütemezés adatainak lekérése][rest_get_schedule] |
| [Átjárócsomópontján][net_node] |[Egy csomópont adatainak lekérése][rest_get_node] |
| [CloudPool][net_pool] |[Egy készlet adatainak lekérése][rest_get_pool] |
| [CloudTask][net_task] |[Feladat adatainak lekérése][rest_get_task] |

## <a name="example-construct-a-filter-string"></a>Példa: egy szűrési karakterláncot hozhatnak létre.
Amikor egy szűrési karakterláncot a hozhat létre [ODATADetailLevel.FilterClause][odata_filter], tekintse meg a fenti táblázat szerinti "Hozzárendelések szűrő-karakterlánc" Keresés a REST API dokumentáció lap, amely megfelel a lista a végrehajtani kívánt művelet. Az első többsoros tábla az adott oldalon megtalálja a szűrhető tulajdonságok és azok támogatott operátorok. Ha szeretné, amelynek kilépési kód: nem nulla összes feladat olvasható be, például a sor a [a feladathoz hozzárendelt feladatok felsorolása] [ rest_list_tasks] alkalmazható tulajdonság karakterlánc és engedélyezett operátorok adja meg:

| Tulajdonság | Engedélyezett műveletek | Típus |
|:--- |:--- |:--- |
| `executionInfo/exitCode` |`eq, ge, gt, le , lt` |`Int` |

Így a szűrési karakterláncot egy nullától eltérő kilépési kódot tartalmazó összes feladat listázása a következő lesz:

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

## <a name="example-construct-a-select-string"></a>Példa: select karakterlánc hozhatnak létre
Összeállításához [ODATADetailLevel.SelectClause][odata_select], tekintse meg a fenti táblázat "Válassza karakterláncok leképezéseit" alatt, és keresse meg a REST API-val oldalt, amely megfelel az entitás, amely listázásakor típusát. Az első többsoros tábla az adott oldalon megtalálja a választható tulajdonságok és azok támogatott operátorok. Ha szeretné lekérni, csak az Azonosítót és a egy lista minden egyes tevékenység parancssora, például megtalálja ezeket a sorokat a alkalmazni táblában a [feladat információinak lekérése][rest_get_task]:

| Tulajdonság | Típus | Megjegyzések |
|:--- |:--- |:--- |
| `id` |`String` |`The ID of the task.` |
| `commandLine` |`String` |`The command line of the task.` |

Válassza ki, amely a azonosítója és a parancssor az egyes felsorolt tevékenységek karakterlánca lesz:

`id, commandLine`

## <a name="code-samples"></a>Kódminták
### <a name="efficient-list-queries-code-sample"></a>Hatékony listázó lekérdezések kódminta
Tekintse meg a [EfficientListQueries] [ efficient_query_sample] mintaprojektet a Githubon tekintheti meg, hogyan hatékony lista lekérdezése hatással lehetnek az alkalmazások teljesítményét. A C#-konzolalkalmazást hoz létre, és a feladatok nagy számú ad egy feladathoz. Ezután lehetővé teszi több alkalommal hívnia a [JobOperations.ListTasks] [ net_list_tasks] metódust, és pass [ODATADetailLevel] [ odata] objektumok konfigurálva, és a különböző tulajdonságértékek eltérő visszaadandó adatok mennyisége. A következőhöz hasonló kimenetet küld:

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

Ahogyan az eltelt idő, nagy mértékben csökkentheti lekérdezések válaszidejét, korlátozza a tulajdonságok és a visszaadott elemek száma. Ez és az egyéb mintaprojektekkel találja a [azure-batch-samples] [ github_samples] tárházban a Githubon.

### <a name="batchmetrics-library-and-code-sample"></a>BatchMetrics erőforrástár és a kód a minta
EfficientListQueries fenti kódmintában, valamint annak a [BatchMetrics] [ batch_metrics] projektre a [azure-batch-samples] [ github_samples]GitHub-adattárban. A BatchMetrics mintaprojektet bemutatja, hogyan hatékony figyelése az Azure Batch-feladat állapotát a Batch API-val.

A [BatchMetrics] [ batch_metrics] minta egy .NET-osztálytár projektet beépítheti a saját projektek és a kódtár használatát mutatja be, és a egy egyszerű parancssori program, amely magában foglalja.

A projekten belül a mintaalkalmazást mutat be a következő műveleteket:

1. Annak érdekében, hogy csak a szükséges tulajdonságokat letöltése adott attribútumok kiválasztása
2. Szűrés állapot átmeneti alkalommal annak érdekében, hogy töltse le változások csak a legutóbbi lekérdezés óta

Például a következő metódust a BatchMetrics könyvtárban jelenik meg. Azt adja vissza, amely megadja, hogy csak egy ODATADetailLevel a `id` és `state` tulajdonságok beszerzése az entitásokat a rendszer megkérdezi. Azt is megadja, hogy csak olyan entitások, amelynek állapota megváltozott, mert a megadott `DateTime` vissza kell adni a paraméter.

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
### <a name="parallel-node-tasks"></a>Párhuzamos csomóponti feladatok
[Maximalizálhatja az Azure Batch számítási erőforrás-használat a párhuzamosan futó csomóponti feladatok](batch-parallel-node-tasks.md) egy másik cikk a Batch-alkalmazások teljesítményével kapcsolatos. Bizonyos típusú számítási feladatok párhuzamos végrehajtása profitálhatnának nagyobb –, de kevesebb – a számítási csomópontokon. Tekintse meg a [példaforgatókönyv](batch-parallel-node-tasks.md#example-scenario) ilyen esetben a részleteket a cikkben.


[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
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