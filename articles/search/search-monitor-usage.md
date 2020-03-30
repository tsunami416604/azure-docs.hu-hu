---
title: Műveletek és tevékenységek figyelése
titleSuffix: Azure Cognitive Search
description: Engedélyezze a naplózást, a lekérdezési tevékenység metrikáit, az erőforrás-használatot és más rendszeradatokat az Azure Cognitive Search szolgáltatásból.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/15/2020
ms.openlocfilehash: 353e00f902a7314e5e5b7c8ee03e8b925a510b26
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462326"
---
# <a name="monitor-operations-and-activity-of-azure-cognitive-search"></a>Az Azure Cognitive Search műveleteinek és tevékenységének figyelése

Ez a cikk bemutatja a szolgáltatás (erőforrás) szintjén, a munkaterhelés szintjén (lekérdezések és indexelés) figyelése, és keretrendszert javasol a felhasználói hozzáférés figyeléséhez.

A teljes spektrumon a beépített infrastruktúra és az alapvető szolgáltatások, például az Azure Monitor kombinációját, valamint a statisztikákat, a számlálókat és az állapotot visszaadó szolgáltatásAPI-k kombinációját fogja használni. A képességek tartományának megértése segíthet egy visszacsatolási hurok létrehozásában, hogy a felmerülő problémákat megoldhassa.

## <a name="use-azure-monitor"></a>Az Azure Monitor használata

Számos szolgáltatás, beleértve az Azure Cognitive Search, használja [az Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/) riasztások, metrikák és naplózási diagnosztikai adatok. Az Azure Cognitive Search esetében a beépített figyelési infrastruktúra elsősorban erőforrásszintű figyelésre (szolgáltatásállapot) és [lekérdezésfigyelésre](search-monitor-queries.md)szolgál.

A következő képernyőkép segítségével megkeresheti az Azure Monitor funkcióit a portálon.

+ **A figyelés** lap, amely a fő áttekintő oldalon található, egy pillantással megjeleníti a legfontosabb mutatókat.
+ **Tevékenységnapló**, közvetlenül az áttekintés alatt, jelentések erőforrásszintű műveletek: szolgáltatás állapota és API-kulcs kérelem értesítések.
+ **Figyelés**, lejjebb a listán, konfigurálható riasztásokat, metrikákat és diagnosztikai naplókat biztosít. Hozza létre ezeket, amikor szüksége van rájuk. Az adatok gyűjtése és tárolása után lekérdezheti vagy megjelenítheti az információkat az elemzésekhez.

![Az Azure Monitor integrációja egy keresési szolgáltatásban](./media/search-monitor-usage/azure-monitor-search.png
 "Az Azure Monitor integrációja egy keresési szolgáltatásban")

### <a name="precision-of-reported-numbers"></a>A jelentett számok pontossága

A portáloldalak néhány percenként frissülnek. Mint ilyen, a portálon jelentett számok hozzávetőlegesek, amelyek célja, hogy általános képet adjanak arról, hogy a rendszer mennyire jól szolgálja a kérelmeket. A tényleges metrikák, például a másodpercenkénti lekérdezések (QPS) magasabbak vagy alacsonyabbak lehetnek, mint az oldalon látható szám.

## <a name="activity-logs-and-service-health"></a>Tevékenységnaplók és szolgáltatásállapot

A [**tevékenységnapló**](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view) adatokat gyűjt az Azure Resource Managertől, és jelentéseket tesz a szolgáltatás állapotának változásairól. Figyelheti a tevékenységnaplót a szolgáltatás állapotával kapcsolatos kritikus, hiba- és figyelmeztetési feltételekre.

A szolgáltatáson belül végzett feladatokhoz – például lekérdezésekhez, indexeléshez vagy objektumok létrehozásához – általános tájékoztató értesítések jelennek meg, például a *Felügyeleti kulcs bekérése* és a *Lekérdezéskulcsok bekérése* minden egyes kéréshez, de maga az adott művelet nem. A szemcsézettel kapcsolatos információkért konfigurálnia kell a diagnosztikai naplózást.

A **tevékenységnaplót** a bal oldali navigációs ablaktáblából, vagy a felső ablak parancssávján lévő Értesítések ablakból, vagy a **Problémák diagnosztizálása és megoldása** lapról érheti el.

## <a name="monitor-storage"></a>Monitor tároló

Az Áttekintés lapba beépített többlapos lapok jelentést tesznek az erőforrás-használatról. Ez az információ akkor válik elérhetővé, amikor elkezdi használni a szolgáltatást, és nincs szükség konfigurációra, és a lap néhány percenként frissül. 

Ha véglegesíti a döntéseket arról, [hogy melyik réteget használja az éles számítási feladatokhoz,](search-sku-tier.md)vagy hogy [állítsa-e be az aktív replikák és partíciók számát,](search-capacity-planning.md)ezek a metrikák segíthetnek ezekkel a döntésekkel, megmutatva, hogy milyen gyorsan használják fel az erőforrásokat, és milyen jól kezeli az aktuális konfiguráció a meglévő terhelést.

A tárolással kapcsolatos riasztások jelenleg nem érhetők el; a tárolási felhasználás nincs összesítve vagy nem jelentkezik be az **AzureMetrics** táblába az Azure Monitorban. Létre kell [hoznunk egy egyéni megoldást,](https://docs.microsoft.com/azure/azure-monitor/insights/solutions-creating) amely erőforrással kapcsolatos értesítéseket bocsát ki, ahol a kód ellenőrzi a tárolási méretet, és kezeli a választ. A tárolási mutatókról a [Szolgáltatásstatisztikák begyűjtése](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics#response)című témakörben talál további információt.

A portálon vizuális figyelése a **portálon,** a Használat lapon mutatja az erőforrás elérhetőségét képest a szolgáltatási szint által előírt aktuális [korlátokat.](search-limits-quotas-capacity.md) 

A következő ábra az ingyenes szolgáltatás, amely korlátozza a 3 objektumok minden típusú és 50 MB tároló. Az alapszintű vagy standard szolgáltatás magasabb korlátokkal rendelkezik, és ha növeli a partíciók számát, a maximális tárhely arányosan emelkedik.

![Használati állapot a rétegkorlátokhoz viszonyítva](./media/search-monitor-usage/usage-tab.png
 "Használati állapot a rétegkorlátokhoz viszonyítva")

## <a name="monitor-workloads"></a>Munkaterhelések figyelése

A naplózott események közé tartoznak az indexeléshez és a lekérdezésekhez kapcsolódó események. Az **AzureDiagnostics** tábla a Log Analytics gyűjti a lekérdezésekkel és az indexeléssel kapcsolatos működési adatokat.

A naplózott adatok többsége csak olvasható műveletekhez szükséges. A naplóban nem rögzített egyéb létrehozás-frissítés-törlés műveletek esetén lekérdezheti a keresési szolgáltatást a rendszeradatokért.

| OperationName | Leírás |
|---------------|-------------|
| ServiceStats szolgáltatás | Ez a művelet egy rutin hívás [a Szolgáltatás statisztika betöltése](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics)vagy frissítése esetén közvetlenül vagy implicit módon a portál áttekintő lapjának feltöltésére. |
| Lekérdezés.Keresés |  Kérelmek lekérdezése egy indexhez, lásd: [Lekérdezésfigyelés](search-monitor-queries.md) a naplózott lekérdezésekkel kapcsolatos információkért.|
| Indexelés.Index  | Ez a művelet [dokumentumok hozzáadása, frissítése vagy törlése.](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) |
| Indexek. Prototípus | Ez az Adatok importálása varázsló által létrehozott index. |
| Indexelők.Létrehozás | Hozzon létre egy indexelőt explicit módon vagy implicit módon az Adatok importálása varázslón keresztül. |
| Indexelők.Get | Az indexelő futtatásakor egy indexelő nevét adja eredményül. |
| Indexelők.Állapot | Az indexelő állapotát adja eredményül az indexelő futtatásakor. |
| Adatforrások.Bek | Az adatforrás nevét adja eredményül, amikor egy indexelőt futtat.|
| Indexek.Get | Indexindex nevét adja eredményül, amikor egy indexelőt futtat. |

### <a name="kusto-queries-about-workloads"></a>Kusto lekérdezések a számítási feladatokról

Ha engedélyezte a naplózást, lekérdezheti az **AzureDiagnostics-t** a szolgáltatáson futó műveletek listájához, és mikor. A teljesítményváltozásainak vizsgálatához a tevékenységkoris korrelálhat.

#### <a name="example-list-operations"></a>Példa: Műveletek listázása 

Visszaad egy listát a műveletek és a száma minden egyes.

```
AzureDiagnostics
| summarize count() by OperationName
```

#### <a name="example-correlate-operations"></a>Példa: Műveletek korrelációja

A lekérdezési kérelem és az indexelési műveletek korrelálása, valamint az adatpontok idődiagramon keresztüli megjelenítése a műveletek egybeesésének megtekintéséhez.

```
AzureDiagnostics
| summarize OperationName, Count=count()
| where OperationName in ('Query.Search', 'Indexing.Index')
| summarize Count=count(), AvgLatency=avg(DurationMs) by bin(TimeGenerated, 1h), OperationName
| render timechart
```

### <a name="use-search-apis"></a>Keresési API-k használata

Az Azure Cognitive Search REST API és a .NET SDK egyaránt programozott hozzáférést biztosít a szolgáltatás metrikáihoz, az index- és indexelő adatokhoz, valamint a dokumentumok számához.

+ [Szervizstatisztika get-je](/rest/api/searchservice/get-service-statistics)
+ [Get index statisztika](/rest/api/searchservice/get-index-statistics)
+ [GET dokumentumszámai](/rest/api/searchservice/count-documents)
+ [Get indexelő állapota](/rest/api/searchservice/get-indexer-status)

## <a name="monitor-user-access"></a>Felhasználói hozzáférés figyelése

Mivel a keresési indexek egy nagyobb ügyfélalkalmazás összetevői, nincs beépített módszer az indexfelhasználónkénti hozzáférés szabályozására vagy figyelésére. A kérelmek et a rendszer ügyfélalkalmazásból, rendszergazdai vagy lekérdezési kérelmekhez érkezik. A rendszergazdai olvasási és írási műveletek közé tartozik az objektumok létrehozása, frissítése, törlése a teljes szolgáltatásban. Az írásvédett műveletek a dokumentumgyűjtemény lekérdezései, amelyek egyetlen indexre vannak kitéve. 

Így a tevékenységnaplókban a rendszergazdai vagy lekérdezési kulcsokat használó hívásokra mutató hivatkozások jelennek meg. A megfelelő kulcs szerepel az ügyfélkódból származó kérelmekben. A szolgáltatás nincs felszerelve identitásjogkivonatok vagy megszemélyesítés kezelésére.

Ha a felhasználónkénti engedélyezéshez léteznek üzleti követelmények, a javaslat az Azure Active Directoryval való integráció. A $filter és a felhasználói identitások segítségével levághatja a felhasználó által nem látható dokumentumok [keresési eredményeit.](search-security-trimming-for-azure-search-with-aad.md) 

Ezt az információt nem lehet a $filter paramétert tartalmazó lekérdezési karakterlánctól elkülönítve naplózni. A lekérdezési karakterláncok jelentésével kapcsolatos részletekért tekintse meg a [Lekérdezések](search-monitor-queries.md) figyelése.

## <a name="next-steps"></a>További lépések

Az Azure Monitor szolgáltatással való folyékonyság elengedhetetlen bármely Azure-szolgáltatás felügyeletéhez, beleértve az olyan erőforrásokat is, mint például az Azure Cognitive Search. Ha nem ismeri az Azure Monitort, szánjon időt az erőforrásokkal kapcsolatos cikkek áttekintésére. Amellett, hogy oktatóanyagok, a következő cikk egy jó kiindulópont lehet.

> [!div class="nextstepaction"]
> [Az Azure-erőforrások figyelése az Azure Monitorsegítségével](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource)
