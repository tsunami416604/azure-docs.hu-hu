---
title: Az Azure Cosmos DB figyelése az Azure Monitor for Cosmos DB szolgáltatással (előzetes verzió)| Microsoft dokumentumok
description: Ez a cikk ismerteti az Azure Monitor for Cosmos DB szolgáltatás, amely a Cosmos DB-tulajdonosok a teljesítmény és a kihasználtsági problémák gyors megértését a CosmosDB-fiókok.
ms.subservice: ''
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/27/2019
ms.openlocfilehash: 9a900a2f2e950fe9b9846ebcc047d7c344284948
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250682"
---
# <a name="explore-azure-monitor-for-azure-cosmos-db-preview"></a>Az Azure Cosmos DB Azure Monitor jának felfedezése (előzetes verzió)

Az Azure Cosmos DB Azure Monitor szolgáltatása (előzetes verzió) az Azure Cosmos DB összes erőforrásának általános teljesítményét, hibáit, kapacitását és működési állapotát egy egységes interaktív környezetben jeleníti meg. Ez a cikk segít megérteni az új figyelési élmény előnyeit, és azt, hogy hogyan módosíthatja és igazíthatja az élményt a szervezet egyedi igényeihez.   

## <a name="introduction"></a>Bevezetés

Mielőtt belemerülne az élménybe, meg kell értenie, hogyan mutatja be és vizualizálja az információkat. 

Ez biztosítja:

* Az Azure Cosmos DB-erőforrásainak **skálázási perspektívájában** az összes előfizetésében egyetlen helyen, azzal a képességgel, hogy csak azokat az előfizetéseket és erőforrásokat tudja szelektíven kiértékelni.

* Egy adott Azure CosmosDB-erőforrás **elemzését** a problémák diagnosztizálása vagy részletes elemzés kategória – kihasználtság, hibák, kapacitás és műveletek – alapján végezheti el. A beállítások bármelyikének kiválasztása részletes áttekintést nyújt a megfelelő Azure Cosmos DB-metrikákról.  

* **Testreszabható** – Ez a felület az Azure Monitor munkafüzetsablonjaira épül, amelyek lehetővé teszik a jelenlévő metrikák módosítását, a korlátokhoz igazodó küszöbértékek módosítását, módosítását vagy beállítását, majd az egyéni munkafüzetbe való mentést. A munkafüzetekben lévő diagramok ezután rögzíthetők az Azure-irányítópultokon.  

Ez a szolgáltatás nem igényel semmit engedélyezni vagy konfigurálni, ezek az Azure Cosmos DB-metrikák alapértelmezés szerint gyűjtik.

>[!NOTE]
>A szolgáltatás eléréséért nem számítunk fel díjat, és csak az Azure Monitor által konfigurált vagy engedélyezett alapvető funkciókért kell fizetnie, az [Azure Monitor díjszabási részletei](https://azure.microsoft.com/pricing/details/monitor/) lapján leírtak szerint.

## <a name="view-utilization-and-performance-metrics-for-azure-cosmos-db"></a>Az Azure Cosmos DB kihasználtsági és teljesítménymutatóinak megtekintése

A tárfiókok kihasználtságának és teljesítményének megtekintéséhez az összes előfizetésben hajtsa végre a következő lépéseket.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

2. Keressen a **Figyelő** mezőbe, és válassza a **Figyelő**lehetőséget.

    ![Keresőmező a "Monitor" szóval és egy legördülő menüvel, amely en a Szolgáltatások "Figyelő" szövege látható sebességmérő stílusú képpel](./media/cosmosdb-insights-overview/search-monitor.png)

3. Válassza a **Cosmos DB (előzetes verzió)** lehetőséget.

    ![Képernyőkép a Cosmos DB áttekintéséről](./media/cosmosdb-insights-overview/cosmos-db.png)

### <a name="overview"></a>Áttekintés

**Az áttekintést**a táblázat interaktív Azure Cosmos DB metrikákat jelenít meg. Az eredményeket az alábbi legördülő listákból kiválasztott beállítások alapján szűrheti:

* **Előfizetések** – csak az Azure Cosmos DB-erőforrással rendelkező előfizetések vannak felsorolva.  

* **Cosmos DB** – Kiválaszthatja az összes, egy részhalmazt, vagy egyetlen Azure Cosmos DB erőforrás.

* **Időtartomány** – alapértelmezés szerint az utolsó 4 óra információt jeleníti meg a megfelelő beállítások alapján.

A legördülő listák alatti számlálócsempe összegző az Azure Cosmos DB-erőforrások teljes száma a kiválasztott előfizetésekben. Feltételes színkódolás vagy hőtérkép van a munkafüzet azon oszlopaihoz, amelyek tranzakciós mutatókat jelentenek. A legmélyebb szín értéke a legmagasabb, a világosabb szín pedig a legalacsonyabb értékeken alapul. 

Ha az Azure Cosmos DB egyik erőforrása melletti legördülő nyilat választ, az egyes adatbázis-tárolók szintjén a teljesítménymutatók bontása jelenik meg:

![Bővített legördülő menü, amely az egyes adatbázis-tárolókat és a kapcsolódó teljesítménylebontást mutatja be](./media/cosmosdb-insights-overview/container-view.png)

Ha az Azure Cosmos DB erőforrás nevét kék színnel kiemelve választja ki, akkor a társított Azure Cosmos DB-fiók alapértelmezett **áttekintése** lesz. 

### <a name="failures"></a>Hibák

Válassza a lap tetején a **Hibák** lehetőséget, és megnyílik a munkafüzetsablon **Hibák** része. A kérelmeket a válaszok elosztásával jeleníti meg:

![Képernyőkép a HTTP-kérelemtípus szerinti bontásban észlelt hibákról](./media/cosmosdb-insights-overview/failures.png)

| Kód      |  Leírás       | 
|-----------|:--------------------|
| `200 OK`  | Az alábbi REST-műveletek egyike sikeres volt: </br>- Egy erőforrásra. </br> - Egy erőforrásra. </br> - POST egy erőforrás. </br> - POST egy tárolt eljárás erőforrás végrehajtásához a tárolt eljárást.|
| `201 Created` | Az erőforrás létrehozásához szükséges POST-művelet sikeres. |
| `404 Not Found` | A művelet olyan erőforráson próbál meg hatni, amely már nem létezik. Előfordulhat például, hogy az erőforrást már törölték. |

Az állapotkódok teljes listáját az [Azure Cosmos DB HTTP állapotkód cikkében](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb)tekintheti meg.

### <a name="capacity"></a>Kapacitás

Válassza a **Kapacitás** lehetőséget a lap tetején, és megnyílik a munkafüzetsablon **Kapacitás** része. Megmutatja, hogy hány dokumentummal rendelkezik, a dokumentumok idővel növekednek, az adathasználat és a rendelkezésre álló tárterület teljes mennyisége.  Ez segítségével azonosíthatja a potenciális tárolási és adatkihasználtsági problémákat.

![Kapacitásmunkafüzet](./media/cosmosdb-insights-overview/capacity.png) 

Az áttekintő munkafüzethez, az **Előfizetés** oszlopban az Azure Cosmos DB-erőforrás melletti legördülő menü kiválasztásával az adatbázist képező egyes tárolók lebontása jelenik meg.

### <a name="operations"></a>Műveletek 

Válassza a lap tetején a **Műveletek lehetőséget,** és megnyílik a munkafüzetsablon **Műveletek** része. Ez lehetővé teszi, hogy a kérelmek et a kérelmek típusa szerinti bontásban láthassa. 

Tehát az alábbi példában `eastus-billingint` látható, hogy elsősorban olvasási kérelmek fogadása, de egy kis számú upsert és kérelmek létrehozása. `westeurope-billingint` Míg kérésszempontjából csak olvasható, legalább az elmúlt négy órában, amelyre a munkafüzet hatóköre jelenleg az időtartomány-paraméterén keresztül történik.

![Műveleti munkafüzet](./media/cosmosdb-insights-overview/operation.png) 

## <a name="pin-export-and-expand"></a>Rögzítés, exportálás és kibontás

A metrikaszakaszok bármelyikét rögzítheti egy [Azure-irányítópulton](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) a szakasz jobb felső részén lévő pushpin ikon kiválasztásával.

![Példa az irányítópult metrikus szakaszpinje](./media/cosmosdb-insights-overview/pin.png)

Ha excel formátumba szeretné exportálni az adatokat, jelölje ki a mutatógomb ikonjának bal oldalán található lefelé mutató nyílikont.

![Munkafüzet exportálása ikon](./media/cosmosdb-insights-overview/export.png)

A munkafüzet összes legördülő nézetének kibontásához vagy összecsukásához jelölje ki az exportálásikontól balra lévő kibontás ikont:

![Munkafüzet kibontása ikon](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cosmos-db-preview"></a>Az Azure Cosmos DB Azure Monitor szolgáltatásának testreszabása (előzetes verzió)

Mivel ez a felhasználói felület az Azure Monitor munkafüzetsablonjaira épül, testreszabhatja a módosított verzió egy példányának **testreszabását** > **Edit** és **mentése** egyéni munkafüzetbe. 

![Sáv testreszabása](./media/cosmosdb-insights-overview/customize.png)

A munkafüzetek egy erőforráscsoporton belül kerülnek mentésre, vagy a **Saját jelentések** szakaszban, amely magánjellegű, vagy a **Megosztott jelentések** szakaszban, amely mindenki számára elérhető, aki hozzáfér az erőforráscsoporthoz. Az egyéni munkafüzet mentése után a bemutató elindításához a munkafüzettárba kell lépnie.

![Munkafüzettár indítása a parancssávról](./media/cosmosdb-insights-overview/gallery.png)

## <a name="next-steps"></a>További lépések

* [Metrikariasztások](../platform/alerts-metric.md) és [szolgáltatásállapot-értesítések](../../service-health/alerts-activity-log-service-notifications.md) konfigurálásával automatikus riasztást állíthat be a problémák észleléséhez.

* Ismerje meg a munkafüzetek támogatását, új és testre szabható forgatókönyveket, valamint az Interaktív jelentések létrehozása az Azure Monitor munkafüzetekkel című [áttekintést.](../app/usage-workbooks.md)
