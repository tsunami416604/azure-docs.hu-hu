---
title: Hatékony lista lekérdezések - Azure Batch kialakítása |} Microsoft Docs
description: Teljesítmény növeléséhez a lekérdezések szűrést, ha a kért információ kötegelt erőforrásokhoz, mint a gyűjtők, feladatok, feladatok, és a számítási csomópontok.
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
ms.date: 08/02/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 330350d6ac6838ea5b09763fe1f73fab1934710c
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="create-queries-to-list-batch-resources-efficiently"></a>Létrehozhat olyan lekérdezéseket, a lista kötegelt erőforrásokhoz hatékonyan

Itt megtudhatja, hogyan és számítási csomópont-lekérdezés feladatok, feladatok, a szolgáltatás által visszaadott adatokat csökkentésével az Azure Batch-alkalmazások teljesítményének növelése a [Batch .NET] [ api_net] könyvtárban.

Szinte minden kötegelt alkalmazásokat kell bizonyos típusú figyelési vagy más műveletet, amely a Batch szolgáltatás gyakran rendszeres időközönként hajthat végre. Például annak megállapításához, hogy vannak-e minden fennmaradó feladatokban aszinkron feladatot, ha előbb telepítik azokra adatokat a feladat minden tevékenység. Nem sikerült meghatározni az állapotát a csomópontok a készlet, kell beolvasni az adatokat a készlet minden egyes csomópontjára. Ez a cikk azt ismerteti, hogyan a leghatékonyabb módon az ilyen lekérdezések végrehajtásához.

> [!NOTE]
> A Batch szolgáltatás különleges API-támogatást biztosít a számítási feladatok feladatokban közös forgatókönyv esetében. Ezek a lista lekérdezés helyett hívása a [beolvasása feladat száma] [ rest_get_task_counts] műveletet. Get feladat száma azt jelzi, hány feladatok várakoznak, fut, vagy végezze el, és hány feladatok sikeres vagy sikertelen volt. Get-feladat száma hatékonyabb, mint egy listájának lekérdezéséhez. További információkért lásd: [(előzetes verzió) állapota alapján feladat feladatok száma](batch-get-task-counts.md). 
>
> Az beszerzése feladat száma művelet nem érhető el a Batch szolgáltatás verziókban rendszernél korábbi 2017-06-01.5.1. Ha a szolgáltatás egy régebbi verzióját használja, majd lekérdezéssel lista egy feladatot a feladatok száma helyette.
>
> 

## <a name="meet-the-detaillevel"></a>A detaillevel paraméternek felel meg
Éles kötegelt alkalmazás entitások, például a feladatok, a feladatok és a számítási csomópontok több ezer száma is. Amikor ezeket az erőforrásokat információkat kér le, potenciálisan nagy mennyiségű adatot kell "át a hálózaton" a Batch szolgáltatás az alkalmazás minden lekérdezésben. Az elemek száma és típusa, amely egy lekérdezés által visszaadott korlátozásával növelheti a sebesség, a lekérdezések, ezért az alkalmazás teljesítményét.

Ez [Batch .NET] [ api_net] API kód részlet listák *minden* , amelyhez társítva van egy feladatot, valamint a feladat *összes* az egyes tevékenységek tulajdonságok:

```csharp
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks =
    batchClient.JobOperations.ListTasks("job-001");
```

Sokkal hatékonyabb listájának lekérdezéséhez, azonban hajthatják végre a lekérdezést a "részletességi szintje" alkalmazásával. Ehhez a parancskimenetnél egy [ODATADetailLevel] [ odata] az objektum a [JobOperations.ListTasks] [ net_list_tasks] metódust. Ezt a kódrészletet adja vissza, csak az Azonosítót, a parancssor és a számítási csomópont információk tulajdonságainak befejezett feladatok:

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

Az ebben a példaforgatókönyvben a feladat több ezer esetén a második lekérdezés eredményeként előálló általában visszaadott sokkal gyorsabb, mint az első. Amikor Ön listaelemet Batch .NET API-val ODATADetailLevel használatáról további információkat is megtalálható [alatt](#efficient-querying-in-batch-net).

> [!IMPORTANT]
> Erősen ajánlott, hogy Ön *mindig* adjon meg egy ODATADetailLevel objektumot a .NET API-lista hívások maximális hatékonyság és az alkalmazás teljesítményének biztosítása érdekében. A részletességi szint Megadja, segítségével csökkentheti a Batch szolgáltatás válaszidejét, hálózathasználat javítása, és minimális memóriahasználat ügyfélalkalmazások.
> 
> 

## <a name="filter-select-and-expand"></a>Szűrés, válassza ki, és bontsa ki a
A [Batch .NET] [ api_net] és [Batch REST] [ api_rest] API-k lehetővé teszi mindkét visszaadott egy listán szereplő elemek száma, valamint az egyes visszaküldött adatmennyiség csökkentése érdekében. Megadásával ehhez **szűrő**, **válasszon**, és **bontsa ki a karakterláncok** lista lekérdezések végrehajtása során.

### <a name="filter"></a>Szűrés
A szűrési karakterláncot egy kifejezés, amely csökkenti az eredmények számát. Például egy feladat csak a futó feladatok listában, és csak a feladatok futtatásához készen áll a számítási csomópontok sorolja fel.

* A szűrési karakterláncot tartalmaz egy vagy több kifejezést, egy kifejezés, amely egy tulajdonság neve, a kezelő és a érték áll. A tulajdonságok adhatók meg vonatkoznak minden lekérdezni, entity Type típusként, amelyek az operátorok mindegyik tulajdonság támogatott.
* A logikai operátorok használatával több kifejezések egyesíthetők `and` és `or`.
* Ez a példa szűrőlista karakterlánc csak a futó "leképezési" feladatok: `(state eq 'running') and startswith(id, 'renderTask')`.

### <a name="select"></a>Válassza ezt:
A select karakterlánc, amely minden elemhez visszaadja a tulajdonságértéket korlátozza. A tulajdonságnevek listáját adja meg, és csak azokat a tulajdonságértékek a lekérdezés eredményében elemeket adja vissza.

* A select karakterlánc áll tulajdonságnevek vesszővel tagolt listája. Az entitástípus kérdez le tulajdonságok adhatja meg.
* A példában válassza karakterlánc határozza meg, hogy a csak három tulajdonságértékek vissza kell-e az egyes feladatok: `id, state, stateTransitionTime`.

### <a name="expand"></a>Kibontás
A kibontott karakterlánc csökkenti a szükséges bizonyos adatok beszerzése API-hívások száma. Egy bővített karakterlánc használata esetén további információt az egyes elemek érhető el egyetlen API-hívással. Ahelyett, hogy az entitások, majd az a lista minden eleme az információt kérő listájának lekérése a kibontás karakterláncra ugyanazokat az információkat egyetlen API-hívással az beszerzése használhatja. Kevesebb az API-hívásokban azt jelenti, hogy a jobb teljesítmény érdekében.

* A select karakterlánc hasonló, a kibontás karakterláncot határozza meg, hogy a lista lekérdezés eredményei között szerepel-e bizonyos adatokat.
* A kibontott karakterlánc csak akkor támogatott, ha a feladatokat, a feladatütemezéseket, a feladatok és a készletek listázása használatban van. Jelenleg csak a támogatott statisztikai adatok.
* Ha az összes tulajdonság szükséges, és válassza karakterlánc nélkül van megadva, a kibontás karakterlánc *kell* statisztikai adatok eléréséhez használható. Ha egy select karakterlánc egy részét tulajdonságok, majd beszerzésére szolgál `stats` a select karakterlánc adható meg, és a kibontás karakterlánc nem kell megadni.
* Ez a példa bontsa ki a karakterláncot határozza meg, hogy a statisztikai adatok vissza kell-e az a lista minden eleme: `stats`.

> [!NOTE]
> A három lekérdezés-karakterlánc típusú térített (szűrés, válassza ki, és bontsa ki a), meg kell győződnie arról, hogy a tulajdonságnevek és esetben egyezik, mint a REST API elem. Például, ha a .NET használata [CloudTask](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask) osztály, meg kell adnia **állapot** helyett **állapot**, annak ellenére, hogy a .NET tulajdonság [CloudTask.State](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.state). Tekintse meg az alábbi táblázatokban tulajdonságleképezései a .NET és REST API-k között.
> 
> 

### <a name="rules-for-filter-select-and-expand-strings"></a>Szabályok szűrő, válassza ki, és bontsa ki a karakterláncok
* Tulajdonságok nevek szűrő, válassza ki, és bontsa ki a karakterláncok meg kell jelennie, mint a [Batch REST] [ api_rest] API – még akkor is, ha használ [Batch .NET] [ api_net]vagy az egyéb kötegelt SDK-k közül.
* Minden tulajdonságnevek megkülönböztetik a kis-és nagybetűket, de a tulajdonságértékek-és nagybetűket.
* Dátum és idő karakterláncok lehet két formátumok egyikét, és utasítás előtt szerepelnie kell a `DateTime`.
  
  * W3C-DTF formátum példa: `creationTime gt DateTime'2011-05-08T08:49:37Z'`
  * RFC 1123 formátum példa: `creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`
* Logikai értékek a következők: vagy `true` vagy `false`.
* Ha egy tulajdonság érvénytelen vagy operátor van megadva, a `400 (Bad Request)` hibát fog okozni.

## <a name="efficient-querying-in-batch-net"></a>Hatékony, a Batch .NET lekérdezése
Belül a [Batch .NET] [ api_net] API-t a [ODATADetailLevel] [ odata] osztály szűrő ellátására szolgál, válassza ki, és bontsa ki a listában karakterláncok műveletek. A ODataDetailLevel osztály tulajdonságai három nyilvános karakterlánc, amely meg a konstruktorban, vagy közvetlenül az objektumra beállítva. Akkor továbbítja a ODataDetailLevel objektum paraméterként a különböző műveletek, mint [ListPools][net_list_pools], [ListJobs][net_list_jobs], és [ListTasks][net_list_tasks].

* [ODATADetailLevel][odata].[ FilterClause][odata_filter]: a visszaadott elemek számának korlátozásához.
* [ODATADetailLevel][odata].[ SelectClause][odata_select]: Adja meg a tulajdonságértékek egyes elemek küld vissza a rendszer.
* [ODATADetailLevel][odata].[ ExpandClause][odata_expand]: olvashatók be adatokat az összes egy API hívása helyett külön hívások minden elemhez.

A következő kódrészletet a Batch .NET API használatával kérdezi le hatékonyan az egy adott készletét készletek statisztikáit a következőképpen a Batch szolgáltatás. Ebben a forgatókönyvben a kötegelt felhasználó teszt- és éles címkészletekkel rendelkezik. A teszt készletre azonosítók fűzve előtagként a "test" és a termelési gyűjtő azonosítók fűzve előtagként a "termék". A kódrészletet a *myBatchClient* megfelelően inicializálva példánya a [BatchClient](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient) osztály.

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
> Példányának [ODATADetailLevel] [ odata] válassza ki a konfigurált és kibontott záradék is átadhatók megfelelő Get módszerek, például a [PoolOperations.GetPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getpool.aspx), a visszaadott adatok korlátozásához.
> 
> 

## <a name="batch-rest-to-net-api-mappings"></a>A Batch REST .NET API rendelése
Tulajdonságnevek szűrő, válassza ki, és bontsa ki a karakterláncok *kell* REST API mint a, mind a név és esetben tükrözi. Az alábbi táblázatok a .NET és REST API megfelelők esetében közötti hozzárendelések adja meg.

### <a name="mappings-for-filter-strings"></a>Azon szűrőkarakterláncokban
* **.NET-lista módszerek**: a .NET API-módszer ebben az oszlopban fogad el egy [ODATADetailLevel] [ odata] objektum paraméterként.
* **REST kérelmek száma**: Ebben az oszlopban kapcsolódó REST API-t minden lap tartalmaz a táblázat, amelyben a tulajdonságok és az engedélyezett műveletek *szűrő* karakterláncok. Használhatja a tulajdonság nevét és a műveletek hoz egy [ODATADetailLevel.FilterClause] [ odata_filter] karakterlánc.

| .NET-lista módszerek | REST kérelmek száma |
| --- | --- |
| [CertificateOperations.ListCertificates][net_list_certs] |[A tanúsítványokat egy fiókot a listáról][rest_list_certs] |
| [CloudTask.ListNodeFiles][net_list_task_files] |[A feladathoz társított fájlok listázása][rest_list_task_files] |
| [JobOperations.ListJobPreparationAndReleaseTaskStatus][net_list_jobprep_status] |[A feladat előkészítése és a feladat kiadási tevékenységek a feladat állapotának felsorolása][rest_list_jobprep_status] |
| [JobOperations.ListJobs][net_list_jobs] |[A feladatok egy fiókot a listáról][rest_list_jobs] |
| [JobOperations.ListNodeFiles][net_list_nodefiles] |[A csomópont fájlok listázása][rest_list_nodefiles] |
| [JobOperations.ListTasks][net_list_tasks] |[A feladathoz társított feladatok felsorolása][rest_list_tasks] |
| [JobScheduleOperations.ListJobSchedules][net_list_job_schedules] |[A feladatütemezéseket egy fiókot a listáról][rest_list_job_schedules] |
| [JobScheduleOperations.ListJobs][net_list_schedule_jobs] |[A feladat ütemezés szerint társított feladatok listázásához][rest_list_schedule_jobs] |
| [PoolOperations.ListComputeNodes][net_list_compute_nodes] |[A készlet számítási csomópontjai felsorolása][rest_list_compute_nodes] |
| [PoolOperations.ListPools][net_list_pools] |[A készletek egy fiókot a listáról][rest_list_pools] |

### <a name="mappings-for-select-strings"></a>Jelölje be karakterláncok leképezéseit
* **A Batch .NET típusok**: Batch .NET API-típusok.
* **REST API entitások**: Ebben az oszlopban minden lap tartalmaz egy vagy több táblában, amely a REST API tulajdonságnevek a típus listából. A tulajdonságnevek hoz használt *válasszon* karakterláncok. Ezek azonos tulajdonságnevek fogja használni, amikor, hozhat létre egy [ODATADetailLevel.SelectClause] [ odata_select] karakterlánc.

| Batch .NET típusok | REST API entitások |
| --- | --- |
| [tanúsítvány][net_cert] |[A tanúsítvány adatainak beolvasása][rest_get_cert] |
| [CloudJob][net_job] |[A feladat adatainak beolvasása][rest_get_job] |
| [CloudJobSchedule][net_schedule] |[A feladatütemezés adatainak beolvasása][rest_get_schedule] |
| [Átjárócsomópontján][net_node] |[A csomópont adatainak beolvasása][rest_get_node] |
| [CloudPool][net_pool] |[Egy készlet adatainak beolvasása][rest_get_pool] |
| [CloudTask][net_task] |[A feladat adatainak beolvasása][rest_get_task] |

## <a name="example-construct-a-filter-string"></a>Példa: egy szűrési karakterláncot létrehozni
Ha, hozhat létre egy szűrési karakterláncot a [ODATADetailLevel.FilterClause][odata_filter], tekintse meg a fenti táblázatban "Szűrőkarakterláncokban leképezéseit" alatt található a REST API dokumentációjában oldalra, amely megfelel a lista elvégezni kívánt műveletet. Az első többsoros tábla adott oldalon megtalálja a szűrhető tulajdonságok és azok támogatott operátort. Ha szeretné beolvasni az összes olyan feladatot, amelynek kilépési kód: nem nulla, például a sor: a [egy feladatot a feladatok lista] [ rest_list_tasks] határozza meg a megfelelő tulajdonság karakterláncot és a processzoridő operátorok:

| Tulajdonság | Engedélyezett műveletek | Típus |
|:--- |:--- |:--- |
| `executionInfo/exitCode` |`eq, ge, gt, le , lt` |`Int` |

Ebből kifolyólag a szűrési karakterláncot egy nem nulla kilépési kód minden feladat felsoroló lenne:

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

## <a name="example-construct-a-select-string"></a>Például: select karakterlánc összeállításához
Összeállításához [ODATADetailLevel.SelectClause][odata_select], tekintse át a "Select karakterláncok leképezéseit" a fenti táblázatban, és nyissa meg a REST API-t a lapot, amely megfelel a listázásakor típusát. Az első többsoros tábla adott oldalon megtalálja a választható tulajdonságok és azok támogatott operátort. Ha csak a azonosítója és a parancssor az egyes feladatok listájában, például megtalálja a sorokra alkalmazandó tábla a [feladat adatainak beolvasása][rest_get_task]:

| Tulajdonság | Típus | Megjegyzések |
|:--- |:--- |:--- |
| `id` |`String` |`The ID of the task.` |
| `commandLine` |`String` |`The command line of the task.` |

A select karakterláncot, amely a azonosítója és minden felsorolt feladat-parancssorból lesz:

`id, commandLine`

## <a name="code-samples"></a>Kódminták
### <a name="efficient-list-queries-code-sample"></a>Hatékony lista lekérdezések kódminta
Tekintse meg a [EfficientListQueries] [ efficient_query_sample] mintaprojektet a Githubon, hogy hogyan hatékony lista lekérdezése befolyásolhatja a teljesítményt az alkalmazásban. A C# Konzolalkalmazás hoz létre, és számos feladatot ad hozzá egy feladatot. Több hívásainak teszi majd, a [JobOperations.ListTasks] [ net_list_tasks] metódus és fázisok [ODATADetailLevel] [ odata] -objektumok a visszaadandó adatok mennyisége eltérő másik tulajdonságot értékekre állították be. Ez a következőhöz hasonló kimenetet hoz létre:

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

Ahogy az az eltelt idő, nagy mértékben csökkentheti lekérdezési válaszidőt, ha a tulajdonságok és a visszaadott elemek száma korlátozza. Ez, és az egyéb mintaprojektjeit megtalálhatja a [azure-köteg-minták] [ github_samples] GitHub tárházából.

### <a name="batchmetrics-library-and-code-sample"></a>BatchMetrics és a kód a minta
A EfficientListQueries kódmintában fenti mellett található a [BatchMetrics] [ batch_metrics] a projektre a [azure-köteg-minták] [ github_samples]GitHub-tárházban. A BatchMetrics mintaprojektet bemutatja, hatékonyan figyelése az Azure Batch feladat előrehaladását a kötegelt API használatával.

A [BatchMetrics] [ batch_metrics] minta tartalmazza a .NET hordozhatóosztálytár-projektjének amely beépítheti saját projektek és egy egyszerű parancssori program a megadásával, és mutassa be a könyvtár használatát.

A mintaalkalmazás a projekt mutatja be a következő műveleteket:

1. Ahhoz, hogy töltse le a tulajdonság csak adott attribútumok kiválasztása
2. Szűrés állapot átmenet alkalommal ahhoz, hogy csak a változások letöltése a legutóbbi lekérdezés óta

Például a következő metódus jelenik meg, a BatchMetrics könyvtárban. Azt adja vissza, amely megadja, hogy csak egy ODATADetailLevel a `id` és `state` tulajdonságait a rendszer megkérdezi a entitások beszerzése. Azt is, hogy csak olyan entitások, amelynek állapota megváltozott, mert a megadott `DateTime` vissza kell adni a paraméter.

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
### <a name="parallel-node-tasks"></a>Párhuzamos csomópont feladatok
[Azure Batch számítási erőforrás-használat csomópont egyidejű feladatok maximális](batch-parallel-node-tasks.md) egy másik cikkben kapcsolódó Batch-alkalmazások teljesítményének. Bizonyos típusú munkaterheléseket is kihasználhatja a párhuzamos tevékenységek feldolgozás alatt álló nagyobb – de--kevesebb számítási csomópontot. Tekintse meg a [példa](batch-parallel-node-tasks.md#example-scenario) a cikkben talál részletes információt ilyen esetben.

### <a name="batch-forum"></a>Batch fórum
A [Azure Batch fórum] [ forum] az MSDN webhelyen van remek kötegelt tárgyalja, és kérdése van a szolgáltatás. Központi a keresztül a hasznos "kapcsolódó" bejegyzések, és a kötegelt megoldások létrehozása során felmerülő kérdéseit.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_metrics]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchMetrics
[efficient_query_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries
[forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
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

[rest_get_task_counts]: https://docs.microsoft.com/rest/api/batchservice/get-the-task-counts-for-a-job