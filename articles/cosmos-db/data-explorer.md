---
title: Az adatkezelés Azure Cosmos DB Explorer használatával
description: A Azure Cosmos DB Explorer egy önálló, webalapú felület, amellyel megtekintheti és kezelheti a Azure Cosmos DB tárolt adatforrásokat.
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/23/2020
ms.author: dech
ms.openlocfilehash: ebfb175de67d7bb8ea011ac340b57f5d62d9e223
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91318806"
---
# <a name="work-with-data-using-azure-cosmos-explorer"></a>Adatok kezelése az Azure Cosmos Explorerrel 

A Azure Cosmos DB Explorer egy önálló, webalapú felület, amellyel megtekintheti és kezelheti a Azure Cosmos DB tárolt adatforrásokat. Azure Cosmos DB-fiók létrehozásakor a Azure Cosmos DB Explorer az Azure Portal meglévő **adatkezelő** lapjának felel meg. A Azure Cosmos DB Explorer fő előnyei a meglévő adatkezelőben:

* A teljes képernyős Real-Estate használatával megtekintheti az adatait, futtathat lekérdezéseket, tárolt eljárásokat, eseményindítókat és megtekintheti az eredményeket.  

* Ideiglenes vagy állandó olvasási vagy írási és olvasási hozzáférést biztosíthat az adatbázis-fiókjához és annak gyűjteményéhez más felhasználók számára, akik nem rendelkeznek hozzáféréssel a Azure Portalhoz vagy előfizetéshez.  

* A lekérdezés eredményeit megoszthatja más felhasználókkal, akik nem férnek hozzá Azure Portalhoz vagy előfizetéshez.  

## <a name="access-azure-cosmos-db-explorer"></a>Hozzáférés Azure Cosmos DB Explorerrel

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/). 

2. Az **összes erőforrás**területen keresse meg és navigáljon a Azure Cosmos db-fiókjához, válassza a kulcsok lehetőséget, és másolja ki az **elsődleges kapcsolódási karakterláncot**.  

3. Lépjen https://cosmos.azure.com/ a mezőbe, illessze be a kapcsolati karakterláncot, majd válassza a **Kapcsolódás**lehetőséget. A kapcsolati karakterlánc használatával a Azure Cosmos DB Explorer időbeli korlátok nélkül is elérhető.  

   Ha más felhasználóknak is ideiglenes hozzáférést szeretne biztosítani a Azure Cosmos DB-fiókjához, ezt az írási-olvasási és olvasási hozzáférési URL-címek használatával teheti meg. 

4. Nyissa meg a **adatkezelő** panelt, és válassza a **teljes képernyő megnyitása**lehetőséget. Az előugró párbeszédpanelen két hozzáférési URL-címet tekinthet meg – **írható** és **olvasható**. Ezek az URL-címek lehetővé teszik a Azure Cosmos DB fiók ideiglenes megosztását más felhasználókkal. A fiókhoz való hozzáférés 24 órán belül lejár, amely után új hozzáférési URL-címmel vagy a kapcsolati karakterlánccal csatlakozhat. 

   **Írás és olvasás – ha** az írási-olvasási URL-címet más felhasználókkal osztja meg, megtekintheti és módosíthatja az adott fiókhoz tartozó adatbázisokat, gyűjteményeket, lekérdezéseket és egyéb erőforrásokat.

   **Olvasás** – ha a csak olvasási URL-címet más felhasználókkal osztja meg, megtekintheti az adott fiókhoz társított adatbázisokat, gyűjteményeket, lekérdezéseket és egyéb erőforrásokat. Ha például egy lekérdezés eredményét szeretné megosztani a csapattársakkal, akik nem férnek hozzá a Azure Portalhoz vagy a Azure Cosmos DB-fiókjához, akkor megadhatja az URL-címet.

   Válassza ki azt a hozzáférési típust, amelyhez meg szeretné nyitni a fiókot, és kattintson a **Megnyitás**gombra. Az Explorer megnyitása után a felhasználói élmény ugyanaz, mint a Azure Portal Adatkezelő lapján.

   :::image type="content" source="./media/data-explorer/open-data-explorer-with-access-url.png" alt-text="Azure Cosmos DB Explorer megnyitása":::

## <a name="known-issues"></a>Ismert problémák

Jelenleg a **nyílt teljes képernyős** élmény lehetővé teszi, hogy az ideiglenes írási-olvasási vagy olvasási hozzáférést még nem támogatja Azure Cosmos db Gremlin és Table API fiókokhoz. A Gremlin és a Table API fiókokat továbbra is megtekintheti, ha átadja a kapcsolódási karakterláncot a Azure Cosmos DB Explorerben. 

Az UUID-t tartalmazó dokumentumok jelenleg nem támogatottak Adatkezelőban. Ez nem befolyásolja a gyűjtemények betöltését, csak az ezeket a dokumentumokat tartalmazó egyes dokumentumokat vagy lekérdezéseket tekinti meg. A dokumentumok megtekintéséhez és kezeléséhez a felhasználóknak továbbra is az eredetileg a dokumentumok létrehozásához használt eszközt kell használniuk.

Előfordulhat, hogy a HTTP-401 hibákat fogadó ügyfelek nem rendelkeznek elegendő RBAC engedéllyel az ügyfél Azure-fiókjához, különösen akkor, ha a fiók egyéni RBAC szerepkörrel rendelkezik. Minden egyéni szerepkörnek rendelkeznie kell a `Microsoft.DocumentDB/databaseAccounts/listKeys/*` adatkezelő használatára, ha Azure Active Directory hitelesítő adataival jelentkezik be.

## <a name="next-steps"></a>Következő lépések

Most, hogy megtanulta, hogyan használhatja a Azure Cosmos DB Explorer alkalmazást az adatai kezeléséhez, a következő lépésként:

* Megkezdheti a [lekérdezések](sql-api-query-reference.md) definiálását az SQL-szintaxis használatával, és [kiszolgálóoldali programozást](stored-procedures-triggers-udfs.md) hajthat végre tárolt eljárások, UDF, eseményindítók használatával. 
