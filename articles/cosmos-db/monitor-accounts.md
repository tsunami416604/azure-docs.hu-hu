---
title: Az Azure Cosmos DB teljesítmény- és tárolási metrikák figyelése
description: Ismerje meg az Azure Cosmos DB-fiókot, a teljesítmény-mérőszámokat, például kérelmeket, és a kiszolgáló hibát, és a használati metrikák, például a tárhelyhasználat figyelése.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: a12e0f567747b67c67f7bc825df1079d852e2f16
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57448238"
---
# <a name="monitor-performance-and-storage-metrics-in-azure-cosmos-db"></a>Az Azure Cosmos DB teljesítmény- és tárolási metrikák figyelése

Nyomon követheti az Azure Cosmos DB-fiókok a [az Azure portal](https://portal.azure.com/). Az egyes Azure Cosmos DB-fiók teljes körű metrikák figyelése az átviteli sebesség, a storage, a rendelkezésre állási, a késés és a konzisztencia érhető el.

Metrikák tekinthető meg a fiók lapon, az új mérőszámok lap vagy az Azure monitorban.

## <a name="view-performance-metrics-on-the-metrics-page"></a>A metrikák oldalon teljesítménymetrikák megjelenítése
1. Az a [az Azure portal](https://portal.azure.com/), kattintson a **minden szolgáltatás**, görgessen a **adatbázisok**, kattintson a **Azure Cosmos DB**, majd kattintson a nevére, az Azure Cosmos DB-fiók, amelynek szeretné teljesítmény-mérőszámok megtekintéséhez.
2. Ha az új oldal megfelelően töltődik be, az erőforrások menüjének alatt **figyelés**, kattintson a **metrikák**.
3. A metrikák lap megnyitásakor, válassza ki a gyűjteményt, amelyben tekintse át a a **próbaidőszakában** listából.

   Az Azure portal megjeleníti a gyűjtemény metrikák elérhető csomag. Vegye figyelembe, hogy átviteli sebesség, a storage, a rendelkezésre állási, a késés és a konzisztencia metrikákat külön lapon. Kellhet további részleteket a metrikák, dupla nyilat tetején kattintson az egyes metrika panel.

   ![A figyelés fókuszban a metrikák suite megjelenítő képernyőkép](./media/monitor-accounts/metrics-suite.png)

## <a name="view-performance-metrics-by-using-azure-monitoring"></a>A teljesítménymetrikák megjelenítése az Azure Monitoring segítségével
1. Az a [az Azure portal](https://portal.azure.com/), kattintson a **figyelő** a bal oldali sávon.
2. Az erőforrás menüben kattintson **metrikák**.
3. Az a **megfigyelés – mérőszámok** ablakban, a a **erőforráscsoport** legördülő menüben válassza az erőforráscsoport az Azure Cosmos DB-fiókot, amely a figyelni kívánt társított. 
4. Az a **erőforrás** legördülő menüben válassza az adatbázis-fiók figyelése.
5. Listájában **rendelkezésre álló metrikák**, válassza ki a megjelenítendő mérőszámokat. Csoportos kijelöléséhez a CTRL gomb használatával. 

## <a name="view-performance-metrics-on-the-account-page"></a>A fiók lapon teljesítménymetrikák megjelenítése
1. Az a [az Azure portal](https://portal.azure.com/), kattintson a **minden szolgáltatás**, görgessen a **adatbázisok**, kattintson a **Azure Cosmos DB**, majd kattintson a nevére, az Azure Cosmos DB-fiók, amelynek szeretné teljesítmény-mérőszámok megtekintéséhez.
2. A **figyelés** lens alapértelmezés szerint megjeleníti a következő csempék találhatók:
   
   * Az aktuális napra kérelmek teljes száma.
   * Felhasznált tárterület.
   
   ![A figyelés fókuszban a kérelmek és a tárolási felhasználásának megjelenítő képernyőkép](./media/monitor-accounts/documentdb-total-requests-and-usage.png)
3. Kattintson a dupla nyilat a jobb felső részén található a **kérelmek** csempe megnyílik egy részletes **metrika** lap.
4. A **metrika** az oldal megjeleníti az összes kérelem részleteit. 

## <a name="set-up-alerts-in-the-portal"></a>A portál beállítása
1. Az a [az Azure portal](https://portal.azure.com/), kattintson a **minden szolgáltatás**, kattintson a **Azure Cosmos DB**, majd kattintson a nevére, amelyhez szeretné beállítani a teljesítmény az Azure Cosmos DB-fiók metrikákhoz kapcsolódó riasztások.
2. Az erőforrás menüben kattintson **riasztási szabályok** a riasztási szabályok lap megnyitásához.  
   ![Képernyőfelvétel a riasztási szabályok rész kiválasztva](./media/monitor-accounts/madocdb10.5.png)
3. Az a **riasztási szabályok** kattintson **riasztás hozzáadása**.  
   ![Képernyőfelvétel a riasztási szabályok lapján, a riasztás hozzáadása gomb kiemelésével](./media/monitor-accounts/madocdb11.png)
4. Az a **riasztási szabály hozzáadása** csoportjában adja meg:
   
   * A riasztási szabály nevét állítja be.
   * Az Új riasztási szabály leírását.
   * A metrika a riasztási szabály.
   * A feltétel küszöbértékét és időszak, amelyek meghatározzák, ha a riasztás aktiválódik. Ha például egy kiszolgáló hibák száma az elmúlt 15 percben 5-nél nagyobb.
   * Hogy a szolgáltatás-rendszergazda és társrendszergazdák rendszer e-mailben a riasztás aktiválódásakor.
   * Riasztási értesítések további e-mail-címét.  
     ![Képernyőfelvétel a hozzáadása egy riasztási szabályt lap](./media/monitor-accounts/madocdb12.png)

## <a name="monitor-azure-cosmos-db-programmatically"></a>Azure Cosmos DB programozott figyelése
A fiók metrikáit, például a fiók tárolási használat és a teljes kérelmek, a portálon elérhető nem érhetők el az SQL API-kon keresztül. Használati adatok, a gyűjtemény szintjén azonban lekérheti az SQL API-k használatával. Adatgyűjtési szint adatok lekéréséhez tegye a következőket:

* A REST API [hajtsa végre a gyűjtemény egy GET](https://msdn.microsoft.com/library/mt489073.aspx). A kvóta- és használati adatok gyűjtésére az x-ms-resource-kvóta és az x-ms-erőforrás-használat fejlécek, a válaszban adja vissza.
* A .NET SDK használatához használja a [DocumentClient.ReadDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx) metódussal, amely adja vissza egy [ResourceResponse](https://msdn.microsoft.com/library/dn799209.aspx) , amely tartalmaz egy használati tulajdonságok száma például  **CollectionSizeUsage**, **DatabaseUsage**, **DocumentUsage**, stb.

További metrikákhoz való hozzáféréshez, használja a [Azure Monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights). Elérhető metrikai meghatározások meghívásával lekérhető:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metricDefinitions?api-version=2015-04-08

Lekérdezések egyéni metrikákat lekéréséhez használja a következő formátumot:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metrics?api-version=2015-04-08&$filter=%28name.value%20eq%20%27Total%20Requests%27%29%20and%20timeGrain%20eq%20duration%27PT5M%27%20and%20startTime%20eq%202016-06-03T03%3A26%3A00.0000000Z%20and%20endTime%20eq%202016-06-10T03%3A26%3A00.0000000Z

További információkért lásd: [erőforrás-metrikák beolvasása az Azure Monitor REST API-val](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/02/23/retrieving-resource-metrics-via-the-azure-insights-api/). Vegye figyelembe, hogy "Azure Insights" nevet kapott: "Az Azure Monitor".  Ez a blogbejegyzés régebbi nevére hivatkozik.

## <a name="next-steps"></a>További lépések
Azure Cosmos DB-kapacitás tervezésével kapcsolatos további tudnivalókért tekintse meg a [Azure Cosmos DB kapacitás planner Számológép](https://www.documentdb.com/capacityplanner).

