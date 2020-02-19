---
title: Műveletek és tevékenységek figyelése
titleSuffix: Azure Cognitive Search
description: Naplózás engedélyezése, lekérdezési tevékenység metrikáinak beolvasása, erőforrás-használat és egyéb rendszeradatok Azure Cognitive Search szolgáltatásból.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/15/2020
ms.openlocfilehash: 5846e9516548032595c1ce072d1dae8dcce9d39e
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443601"
---
# <a name="monitor-operations-and-activity-of-azure-cognitive-search"></a>Az Azure Cognitive Search működésének és tevékenységének figyelése

Ez a cikk a szolgáltatás (erőforrás) szintjén, a munkaterhelés szintjén (lekérdezések és indexelés), valamint a felhasználói hozzáférés figyelésére szolgáló keretrendszert mutat be.

A spektrumon a beépített infrastruktúra és az alapszintű szolgáltatások, például a Azure Monitor, valamint a statisztikát, darabszámot és állapotot visszaadó szolgáltatás API-k kombinációját fogja használni. A funkciók körének megismerése segíthet egy visszajelzési hurok összeállításában, így problémák merülhetnek fel, ahogy azok megjelentek.

## <a name="use-azure-monitor"></a>Az Azure Monitor használata

Számos szolgáltatás, többek között az Azure Cognitive Search, a riasztások, a metrikák és a naplózási diagnosztikai adatok kihasználása [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/) . Az Azure Cognitive Search esetében a beépített figyelési infrastruktúrát elsősorban az erőforrás-szintű monitorozás (a szolgáltatás állapota) és a [lekérdezések figyelése](search-monitor-queries.md)használja.

A következő képernyőfelvétel segít megkeresni Azure Monitor szolgáltatásokat a portálon.

+ A **figyelés** lap a fő Áttekintés lapon a legfontosabb metrikákat jeleníti meg egy pillantással.
+ **Műveletnapló**, csak az alábbi áttekintés, erőforrás-szintű műveletek jelentései: szolgáltatás állapota és API-kulcs kérése értesítések.
+ A **figyelés**, a lista lejjebb, konfigurálható riasztások, metrikák és diagnosztikai naplók. Ezeket akkor hozza létre, amikor szüksége van rájuk. Az adatok gyűjtése és tárolása után lekérdezheti vagy megjelenítheti az elemzések adatait.

![Azure Monitor integráció egy keresési szolgáltatásban](./media/search-monitor-usage/azure-monitor-search.png
 "Azure Monitor integráció egy keresési szolgáltatásban")

### <a name="precision-of-reported-numbers"></a>Jelentett számok pontossága

A portál oldalai néhány percenként frissülnek. Így a portálon jelentett számok hozzávetőleges értékkel bírnak, így általános értelemben láthatja, hogy a rendszer milyen jól szolgálja ki a karbantartási kérelmeket. A tényleges mérőszámok, például a másodpercenkénti lekérdezések (QPS) lehetnek magasabbak vagy alacsonyabbak, mint az oldalon megjelenített szám.

## <a name="activity-logs-and-service-health"></a>Tevékenységek naplói és szolgáltatás állapota

A [**tevékenység naplója**](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view) adatokat gyűjt a Azure Resource Managerről, és jelentéseket készít a szolgáltatás állapotát érintő változásokról. A szolgáltatási állapottal kapcsolatos kritikus, hibával és figyelmeztetési feltételekkel nyomon követheti a tevékenység naplóját.

A szolgáltatáson belüli feladatokhoz – mint például a lekérdezések, az indexelés vagy az objektumok létrehozása – általános tájékoztató értesítéseket fog látni, például a *rendszergazdai kulcs beolvasása* és a *lekérdezési kulcsok beolvasása* az egyes kérelmekhez, de nem maga a konkrét művelet. A gabonával kapcsolatos információkért be kell állítania a diagnosztikai naplózást.

A **tevékenység naplóját** a bal oldali navigációs ablaktáblán, vagy a felső ablak parancssáv vagy a **problémák diagnosztizálása és megoldása** oldalon található értesítések közül lehet elérni.

## <a name="monitor-storage"></a>Tároló figyelése

Az áttekintő lapokra épülő, Többlapos lapok az erőforrás-használattal kapcsolatos jelentést jelentenek. Ezek az adatok a szolgáltatás használatának megkezdése után azonnal elérhetővé válnak, és az oldal néhány percenként frissül. 

Ha az éles számítási feladatokhoz [használt szintet](search-sku-tier.md)véglegesíti, vagy ha [módosítani szeretné az aktív replikák és partíciók számát](search-capacity-planning.md), akkor ezek a mérőszámok segítséget nyújthatnak ezekhez a döntésekhez azáltal, hogy megmutatják, hogy az erőforrások milyen gyorsan legyenek felhasználva, és hogy a jelenlegi konfiguráció milyen mértékben kezeli a meglévő terhelést.

A tárterülettel kapcsolatos riasztások jelenleg nem érhetők el; a tárolási felhasználás nincs összesítve, vagy be van jelentkezve a Azure Monitor **AzureMetrics** táblába. Létre kell hoznia egy egyéni megoldást, amely az erőforrásokkal kapcsolatos értesítéseket bocsát ki, ahol a kód ellenőrzi a tárolási méretet, és kezeli a választ. További információ a tárolási metrikákkal kapcsolatban: [szolgáltatás statisztikáinak beolvasása](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics#response).

A portálon a vizualizáció figyeléséhez a **használat** lapon az erőforrás rendelkezésre állása látható a szolgáltatási szinten kiszabott jelenlegi [korlátokhoz](search-limits-quotas-capacity.md) képest. 

Az alábbi ábra az ingyenes szolgáltatás, amely az egyes típusok 3 objektumára, illetve 50 MB tárterületre van korlátozva. Egy alapszintű vagy standard szolgáltatás magasabb korláttal rendelkezik, és ha megnöveli a partíciók számát, a maximális tárterület arányosan növekszik.

![A használati állapot a rétegek korlátaihoz képest](./media/search-monitor-usage/usage-tab.png
 "A használati állapot a rétegek korlátaihoz képest")

## <a name="monitor-workloads"></a>Munkaterhelések figyelése

A naplózott események tartalmazzák az indexeléssel és a lekérdezésekkel kapcsolatos eseményeket. A Log Analytics **AzureDiagnostics** táblázata a lekérdezésekhez és az indexeléshez kapcsolódó operatív adatokat gyűjti.

A naplózott adatok többsége csak olvasási műveletekhez használható. A naplóban nem rögzített további frissítési-törlési műveletekhez a keresési szolgáltatást a rendszerinformációk lekérdezése céljából kérdezheti le.

| OperationName | Leírás |
|---------------|-------------|
| ServiceStats | Ez a művelet egy rutinos hívás a [szolgáltatás statisztikáinak beszerzésére](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics), vagy közvetlenül vagy implicit módon, a portál áttekintő oldalának feltöltésekor, amikor betöltődik vagy frissül. |
| Lekérdezés. keresés |  Lekérdezési kérelmek egy indextel kapcsolatban: a naplózott lekérdezésekkel kapcsolatos információk [figyelése](search-monitor-queries.md) .|
| Indexelés. index  | Ez a művelet a [dokumentumok hozzáadását, frissítését és törlését](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)hívja meg. |
| indexek. Prototípus | Ez az adatbázis importálása varázsló által létrehozott index. |
| Indexelő. létrehozás | Hozzon létre egy indexelő explicit módon vagy implicit módon az adatimportálás varázsló segítségével. |
| Indexelő. Get | Egy indexelő nevét adja vissza, amikor az indexelő fut. |
| Indexelő. status | Egy indexelő állapotát adja vissza, amikor az indexelő fut. |
| Adatforrások. Get | Az adatforrás nevét adja vissza, amikor egy indexelő fut.|
| Indexek. Get | Egy index nevét adja vissza, amikor egy indexelő fut. |

### <a name="kusto-queries-about-workloads"></a>Kusto lekérdezések a számítási feladatokról

Ha engedélyezte a naplózást, a **AzureDiagnostics** lekérdezheti a szolgáltatásban futtatott műveletek listáját, valamint a következőt:. A tevékenységek a teljesítmény változásainak vizsgálatára is felhasználhatók.

#### <a name="example-list-operations"></a>Példa: műveletek listázása 

Visszaküldi a műveletek listáját és az egyes elemek számát.

```
AzureDiagnostics
| summarize count() by OperationName
```

#### <a name="example-correlate-operations"></a>Példa: korrelációs műveletek

A lekérdezési kérelmek korrelációja az indexelési műveletekkel, valamint az adatpontok megjelenítése az idődiagramokon keresztül, hogy a műveletek egybeessenek.

```
AzureDiagnostics
| summarize OperationName, Count=count()
| where OperationName in ('Query.Search', 'Indexing.Index')
| summarize Count=count(), AvgLatency=avg(DurationMs) by bin(TimeGenerated, 1h), OperationName
| render timechart
```

### <a name="use-search-apis"></a>Keresési API-k használata

Az Azure Cognitive Search REST API és a .NET SDK egyaránt biztosít programozott hozzáférést a szolgáltatási metrikák, az index és az indexelő adataihoz, valamint a dokumentumok számát.

+ [Szolgáltatás statisztikáinak beolvasása](/rest/api/searchservice/get-service-statistics)
+ [Index statisztikáinak beolvasása](/rest/api/searchservice/get-index-statistics)
+ [Dokumentumok számának beolvasása](/rest/api/searchservice/count-documents)
+ [Indexelő állapotának beolvasása](/rest/api/searchservice/get-indexer-status)

## <a name="monitor-user-access"></a>Felhasználói hozzáférés figyelése

Mivel a keresési indexek egy nagyobb ügyfélalkalmazás összetevői, a felhasználónkénti hozzáférés-vezérléshez és-figyeléshez nem áll rendelkezésre beépített módszertan az indexekhez. A rendszer a kérelmeket egy ügyfélalkalmazás, rendszergazdai vagy lekérdezési kérelmek esetében feltételezi. A rendszergazdai írási és olvasási műveletek közé tartozik a teljes szolgáltatáson belüli objektumok létrehozása, frissítése és törlése. A csak olvasási műveletek a dokumentumok gyűjteményére vonatkozó lekérdezések, amelyek hatóköre egyetlen indexre van korlátozva. 

Így a tevékenység naplóiban láthatók a rendszergazdai kulcsokat vagy a lekérdezési kulcsokat használó hívásokra mutató hivatkozások. A megfelelő kulcsot az ügyfél kódjából származó kérelmek tartalmazzák. A szolgáltatás nem alkalmas az identitás-tokenek vagy a megszemélyesítés kezelésére.

Ha a felhasználónkénti hitelesítéshez üzleti követelmények léteznek, a javaslat a Azure Active Directory integrációja. $Filter és felhasználói identitások használatával levágja a dokumentumok azon [keresési eredményeit](search-security-trimming-for-azure-search-with-aad.md) , amelyeket a felhasználó nem láthat. 

Ezt az információt nem lehet külön naplózni a lekérdezési karakterláncból, amely tartalmazza a $filter paramétert. A jelentéskészítési lekérdezési karakterláncokkal kapcsolatos részletekért lásd: [lekérdezések figyelése](search-monitor-queries.md) .

## <a name="next-steps"></a>Következő lépések

A Azure Monitorekkel való gördülékenység elengedhetetlen az Azure-szolgáltatások, például az Azure-Cognitive Searchek felügyeletéért. Ha nem ismeri a Azure Monitort, szánjon időt az erőforrásokkal kapcsolatos cikkek áttekintésére. Az oktatóanyagokon kívül a következő cikk is jó kiindulópont.

> [!div class="nextstepaction"]
> [Azure-erőforrások monitorozása Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource)
