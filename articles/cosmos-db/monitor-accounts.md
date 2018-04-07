---
title: Azure Cosmos DB kérések és a tárolási figyelése |} Microsoft Docs
description: Útmutató a teljesítménymutatók, például a kérelmek és a kiszolgáló hibát, és a szoftverhasználati mérési adatok, például a tároló fogyasztása Azure Cosmos DB fiókja.
services: cosmos-db
documentationcenter: ''
author: SnehaGunda
manager: kfile
ms.assetid: 4c6a2e6f-6e78-48e3-8dc6-f4498b235a9e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: sngun
ms.openlocfilehash: 008c47bbceeba1345c56b5171f5fbc1f8676b02c
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="monitor-azure-cosmos-db"></a>Az Azure Cosmos DB figyelése
A Azure Cosmos DB-fiókok a figyelheti a [Azure-portálon](https://portal.azure.com/). Minden Azure Cosmos DB fiók metrikák tartalmazó teljes csomag érhető el átviteli, a tároló, a rendelkezésre állási, a késés és a konzisztencia figyelése.

Metrikák áttekintheti a fiók lapon, az új mérőszámok lap vagy az Azure-figyelő.

## <a name="view-performance-metrics-on-the-metrics-page"></a>A metrikák oldalon nézet metrikák
1. Az a [Azure-portálon](https://portal.azure.com/), kattintson a **minden szolgáltatás**, görgessen **adatbázisok**, kattintson a **Azure Cosmos DB**, és kattintson a nevére, az Azure-beli Cosmos DB-fiók, amelynek szeretné teljesítménymutatók megtekintése.
2. Ha az új értékkel, erőforrás menüben a **figyelés**, kattintson a **metrikák**.
3. A metrikák lap megnyitásakor, válassza ki a gyűjteményt, hogy tekintse át a a **következő gyűjtemény(ek) készleteit szinkronizálja** legördülő listán.

   Az Azure-portálon a gyűjtemény voltak elérhetők metrikák programcsomag jeleníti meg. Vegye figyelembe, hogy átviteli, a tároló, a rendelkezésre állási, a késés és a konzisztencia metrikák külön lapon találhatók. Megszerezni a további részletek a metrikákra vonatkozóan megadott, kattintson a felső két mutató nyílra, minden egyes metrikák panel jobb.

   ![A figyelés fókuszt a metrikák suite megjelenítő képernyőfelvétel](./media/monitor-accounts/metrics-suite.png)

## <a name="view-performance-metrics-by-using-azure-monitoring"></a>Nézet teljesítménymutatók figyelése Azure használatával
1. Az a [Azure-portálon](https://portal.azure.com/), kattintson a **figyelő** a bal oldali sávon.
2. Az erőforrás menüjében kattintson **metrikák**.
3. Az a **figyelő - metrikák** ablakban, a a **erőforráscsoport** legördülő menüben válassza ki az erőforráscsoportot, hogy a figyelni kívánt Azure Cosmos DB fiókhoz tartozó. 
4. Az a **erőforrás** legördülő menüben válassza az adatbázis fiókját a figyelheti.
5. A közül **elérhető**, kiválaszthatja a megjelenítendő metrikákat. Használja a CTRL gombra, jelölje ki. 

## <a name="view-performance-metrics-on-the-account-page"></a>A fiók lapon nézet metrikák
1. Az a [Azure-portálon](https://portal.azure.com/), kattintson a **minden szolgáltatás**, görgessen **adatbázisok**, kattintson a **Azure Cosmos DB**, és kattintson a nevére, az Azure-beli Cosmos DB-fiók, amelynek szeretné teljesítménymutatók megtekintése.
2. A **figyelés** fókuszban alapértelmezés szerint megjeleníti a következő csempék találhatók:
   
   * Az aktuális napra kérelmek teljes száma.
   * A tárolási használt.
   
   ![A figyelés fókuszt a kérelmeket, és a tárhelyhasználatot megjelenítő képernyőfelvétel](./media/monitor-accounts/documentdb-total-requests-and-usage.png)
3. A jobb felső részén a dupla mutató nyílra kattintva a **kérelmek** csempe megnyílik egy részletes **metrika** lap.
4. A **metrika** lap részleteit jeleníti meg, hogy az összes kérelmet. 

## <a name="set-up-alerts-in-the-portal"></a>A portál értesítések beállítása
1. Az a [Azure-portálon](https://portal.azure.com/), kattintson a **minden szolgáltatás**, kattintson a **Azure Cosmos DB**, és kattintson a nevére, amelynek szeretné beállítani a teljesítmény Azure Cosmos DB fiók metrika riasztásokat.
2. Az erőforrás menüjében kattintson **riasztási szabályok** riasztási szabályok lapjának megnyitásához.  
   ![Képernyőfelvétel a riasztás szabályok kijelölve](./media/monitor-accounts/madocdb10.5.png)
3. Az a **riasztási szabályok** kattintson **riasztás hozzáadása**.  
   ![Képernyőfelvétel a riasztási szabályok lapon, a riasztás hozzáadása gomb](./media/monitor-accounts/madocdb11.png)
4. Az a **riasztási szabály felvétele** csoportjában adja meg:
   
   * A riasztási szabályt hoz létre a neve.
   * Az Új riasztási szabály leírását.
   * A riasztási szabály a metrikát.
   * A feltétel küszöbérték és időszak, amelyek meghatározzák, ha a riasztás akkor aktiválódik. Például egy kiszolgáló hibák száma nagyobb, mint 5 az elmúlt 15 perc.
   * Hogy a szolgáltatás-rendszergazda és coadministrators rendszer e-mailben a riasztás aktiválódásakor.
   * További e-mail címeket a riasztási értesítésekhez.  
     ![Képernyőfelvétel a hozzáadása egy riasztási szabály lap](./media/monitor-accounts/madocdb12.png)

## <a name="monitor-azure-cosmos-db-programmatically"></a>Azure Cosmos DB programozott módon figyelése
A fiók szintű metrikák elérhető a portálon, például a fiók tárolási használati és a végösszeg kérelmeket, az SQL API-k segítségével nem érhetők el. Használati adatok, a gyűjtemény szintjén azonban az SQL API-k használatával kérheti le. Gyűjtemény szolgáltatásiszint-adatok beolvasása, tegye a következőket:

* A REST API-t használandó [végezze el a gyűjtemény egy GET](https://msdn.microsoft.com/library/mt489073.aspx). A kvóta- és használati adatokat a gyűjtemény eredmény abban az esetben az x-ms-erőforráskvótát és az x-ms-erőforrás-használat fejlécek, a válaszban.
* A .NET SDK használatához a [DocumentClient.ReadDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx) metódus, amely adja vissza egy [ResourceResponse](https://msdn.microsoft.com/library/dn799209.aspx) például tartalmazó használati tulajdonság  **CollectionSizeUsage**, **DatabaseUsage**, **DocumentUsage**, stb.

További metrikák elérésére, a [Azure figyelő SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights). Elérhető metrikai meghatározásainak hívásával kérhető:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metricDefinitions?api-version=2015-04-08

Egyéni metrikák beolvasása lekérdezések használja a következő formátumot:

    https://management.azure.com/subscriptions/{SubecriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metrics?api-version=2015-04-08&$filter=%28name.value%20eq%20%27Total%20Requests%27%29%20and%20timeGrain%20eq%20duration%27PT5M%27%20and%20startTime%20eq%202016-06-03T03%3A26%3A00.0000000Z%20and%20endTime%20eq%202016-06-10T03%3A26%3A00.0000000Z

További információkért lásd: [erőforrás metrikák beolvasása az Azure figyelő REST API-n keresztül](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/02/23/retrieving-resource-metrics-via-the-azure-insights-api/). Vegye figyelembe, hogy a "Azure Insights" névre lett átnevezve "Azure figyelés".  Ezt a blogbejegyzést régebbi nevére hivatkozik.

## <a name="next-steps"></a>További lépések
Azure Cosmos DB kapacitás tervezésével kapcsolatos további tudnivalókért tekintse meg a [Azure Cosmos DB kapacitás planner Számológép](https://www.documentdb.com/capacityplanner).

