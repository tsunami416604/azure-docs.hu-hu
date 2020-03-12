---
title: Az adatok kezelése az Azure Cosmos DB explorer használatával
description: Az Azure Cosmos DB explorer egy önálló webes felület, amely lehetővé teszi, hogy az Azure Cosmos DB-ben tárolt adatok megtekintése és kezelése.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dech
ms.openlocfilehash: 57ba647ade45928f03cd7fb2b037642d5e4d52cc
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096817"
---
# <a name="work-with-data-using-azure-cosmos-explorer"></a>Adatok kezelése az Azure Cosmos Explorerrel 

Az Azure Cosmos DB explorer egy önálló webes felület, amely lehetővé teszi, hogy az Azure Cosmos DB-ben tárolt adatok megtekintése és kezelése. Azure Cosmos DB-fiók létrehozásakor a Azure Cosmos DB Explorer az Azure Portal meglévő **adatkezelő** lapjának felel meg. A meglévő adatkezelő keresztül Azure Cosmos DB explorer főbb előnyei a következők:

* A teljes képernyős érdekében az adatok, a lekérdezések futtatását, a tárolt eljárások, eseményindítók megtekintéséhez rendelkezik, és megtekintheti az eredményeiket.  

* Ideiglenes vagy állandó olvasási vagy írási-olvasási hozzáférést biztosíthat a fiókot, és annak gyűjteményeiről más felhasználóknak, akik nem rendelkeznek hozzáféréssel az Azure portal vagy az előfizetés számára.  

* A lekérdezési eredményeket megoszthatja más felhasználókkal, akik nem rendelkeznek hozzáféréssel az Azure portal vagy előfizetésbe.  

## <a name="access-azure-cosmos-db-explorer"></a>Hozzáférés az Azure Cosmos DB explorer

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/). 

2. Az **összes erőforrás**területen keresse meg és navigáljon a Azure Cosmos db-fiókjához, válassza a kulcsok lehetőséget, és másolja ki az **elsődleges kapcsolódási karakterláncot**.  

3. Nyissa meg a https://cosmos.azure.com/, illessze be a kapcsolati karakterláncot, és válassza a **Kapcsolódás**lehetőséget. A kapcsolati karakterlánc használatával is elérheti az Azure Cosmos DB explorer minden olyan alkalommal korlátok nélkül.  

   Ha azt szeretné, hogy az Azure Cosmos DB-fiók ideiglenes hozzáférést biztosíthat más felhasználók, az olvasási és írási és olvasási hozzáférés URL-címek használatával megteheti. 

4. Nyissa meg a **adatkezelő** panelt, és válassza a **teljes képernyő megnyitása**lehetőséget. Az előugró párbeszédpanelen két hozzáférési URL-címet tekinthet meg – **írható** és **olvasható**. Az URL-címek lehetővé teszik az Azure Cosmos DB-fiókja átmenetileg megosztása más felhasználókkal. A fiókhoz való hozzáférés lejár, 24 órában, ami után újra egy új hozzáférési URL-cím vagy a kapcsolati karakterlánc használatával. 

   **Írás és olvasás – ha** az írási-olvasási URL-címet más felhasználókkal osztja meg, megtekintheti és módosíthatja az adott fiókhoz tartozó adatbázisokat, gyűjteményeket, lekérdezéseket és egyéb erőforrásokat.

   **Olvasás** – ha a csak olvasási URL-címet más felhasználókkal osztja meg, megtekintheti az adott fiókhoz társított adatbázisokat, gyűjteményeket, lekérdezéseket és egyéb erőforrásokat. Például ha meg szeretné osztani az eredményeket a lekérdezés a csapattársai, akiknek nincs hozzáférésük az Azure portal vagy az Azure Cosmos DB-fiókot, biztosíthat azokat az URL-címet.

   Válassza ki azt a hozzáférési típust, amelyhez meg szeretné nyitni a fiókot, és kattintson a **Megnyitás**gombra. Az megnyitása után az a felhasználói élményt, azonos, mivel a az Azure portal adatkezelő lap kellett.   

   ![Nyissa meg az Azure Cosmos DB-adatkezelőt](./media/data-explorer/open-data-explorer-with-access-url.png)

## <a name="known-issues"></a>Ismert problémák

Jelenleg a **nyílt teljes képernyős** élmény lehetővé teszi, hogy az ideiglenes írási-olvasási vagy olvasási hozzáférést még nem támogatja Azure Cosmos db Gremlin és Table API fiókokhoz. A kapcsolati karakterláncot adja át az Azure Cosmos DB Explorer továbbra is megtekintheti a Gremlin- és Table API-fiók. 

Az UUID-t tartalmazó dokumentumok jelenleg nem támogatottak Adatkezelőban. Ez nem befolyásolja a gyűjtemények betöltését, csak az ezeket a dokumentumokat tartalmazó egyes dokumentumokat vagy lekérdezéseket tekinti meg. A dokumentumok megtekintéséhez és kezeléséhez a felhasználóknak továbbra is az eredetileg a dokumentumok létrehozásához használt eszközt kell használniuk.

## <a name="next-steps"></a>Következő lépések
Most, hogy megtanulhatta, hogyan kezelheti az adatokat, akkor is tovább az Azure Cosmos DB explorer használatának első lépései:

* Megkezdheti a [lekérdezések](sql-api-query-reference.md) definiálását az SQL-szintaxis használatával, és [kiszolgálóoldali programozást](stored-procedures-triggers-udfs.md) hajthat végre tárolt eljárások, UDF, eseményindítók használatával. 
