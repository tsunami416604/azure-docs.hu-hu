---
title: Az Azure Cosmos DB explorer használata az adatok kezeléséhez
description: Az Azure Cosmos DB explorer egy önálló webalapú felület, amely lehetővé teszi az Azure Cosmos DB-ben tárolt adatok megtekintését és kezelését.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dech
ms.openlocfilehash: 57ba647ade45928f03cd7fb2b037642d5e4d52cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096817"
---
# <a name="work-with-data-using-azure-cosmos-explorer"></a>Adatok kezelése az Azure Cosmos Explorerrel 

Az Azure Cosmos DB explorer egy önálló webalapú felület, amely lehetővé teszi az Azure Cosmos DB-ben tárolt adatok megtekintését és kezelését. Az Azure Cosmos DB explorer egyenértékű a meglévő **Data Explorer** lapon, amely elérhető az Azure Portalon, amikor létrehoz egy Azure Cosmos DB-fiókot. Az Azure Cosmos DB explorer fő előnyei a meglévő Adatkezelővel szemben a következők:

* Teljes képernyős ingatlannal rendelkezik az adatok megtekintéséhez, lekérdezések futtatásához, tárolt eljárásokhoz, eseményindítókhoz és az eredmények megtekintéséhez.  

* Ideiglenes vagy állandó olvasási vagy írási vagy írási hozzáférést biztosíthat az adatbázis-fiókjához és gyűjteményéhez más felhasználók számára, akik nem férnek hozzá az Azure Portalhoz vagy az előfizetéshez.  

* Megoszthatja a lekérdezés eredményeit más felhasználókkal, akik nem férnek hozzá az Azure Portalhoz vagy előfizetéshez.  

## <a name="access-azure-cosmos-db-explorer"></a>Az Azure Cosmos DB-kezelőjének elérése

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/) 

2. Az **összes erőforrásból**keresse meg az Azure Cosmos DB-fiókját, és keresse meg azt, válassza a Kulcsok lehetőséget, és másolja az **elsődleges kapcsolati karakterláncot.**  

3. Nyissa https://cosmos.azure.com/meg a lehetőséget, illessze be a kapcsolati karakterláncot, és válassza a **Csatlakozás lehetőséget.** A kapcsolati karakterlánc használatával időkorlátok nélkül elérheti az Azure Cosmos DB explorert.  

   Ha azt szeretné, hogy más felhasználók ideiglenes hozzáférést az Azure Cosmos DB-fiók, ezt megteheti az írási és olvasási hozzáférési URL-címek használatával. 

4. Nyissa meg az **Adatkezelő** panelt, és válassza **a Teljes képernyő megnyitása**lehetőséget. Az előugró párbeszédpanelen két hozzáférési URL-t tekinthet meg – **olvasási-olvasási** és **olvasási**. Ezek az URL-címek lehetővé teszik, hogy ideiglenesen megossza az Azure Cosmos DB-fiókját más felhasználókkal. A fiókhoz való hozzáférés 24 órán belül lejár, amely után újra csatlakozhat egy új hozzáférési URL-cím vagy a kapcsolati karakterlánc használatával. 

   **Olvasási-olvasás** – Amikor megosztja az írási és olvasási URL-címet más felhasználókkal, megtekinthetik és módosíthatják az adott fiókhoz társított adatbázisokat, gyűjteményeket, lekérdezéseket és egyéb erőforrásokat.

   **Olvasás** – Amikor megosztja az írásvédett URL-t más felhasználókkal, megtekinthetik az adott fiókhoz társított adatbázisokat, gyűjteményeket, lekérdezéseket és egyéb erőforrásokat. Ha például meg szeretné osztani egy lekérdezés eredményeit az Azure Portalhoz vagy az Azure Cosmos DB-fiókjához nem hozzáférő csapattársaival, megadhatja nekik ezt az URL-címet.

   Válassza ki, hogy milyen típusú hozzáféréssel szeretné megnyitni a fiókot, és kattintson a **Megnyitás gombra.** Miután megnyitotta a felfedezőt, a felhasználói élmény megegyezik az Azure Portal Adatkezelő lapjával.   

   ![Az Azure Cosmos DB explorer megnyitása](./media/data-explorer/open-data-explorer-with-access-url.png)

## <a name="known-issues"></a>Ismert problémák

Jelenleg az **Open Full Screen** szolgáltatás, amely lehetővé teszi az ideiglenes írási és olvasási hozzáférés megosztását, még nem támogatott az Azure Cosmos DB Gremlin és table API-fiókok számára. A Gremlin- és table API-fiókok továbbra is megtekintheti a kapcsolati karakterlánc ot az Azure Cosmos DB Explorer. 

Az Adatkezelő jelenleg nem támogatja az UUID azonosítót tartalmazó dokumentumok megtekintését. Ez nincs hatással a gyűjtemények betöltésére, csak az ezeket a dokumentumokat tartalmazó egyes dokumentumok vagy lekérdezések megtekintésére. A dokumentumok megtekintéséhez és kezeléséhez a felhasználóknak továbbra is a dokumentumok létrehozásához eredetileg használt eszközt kell használniuk.

## <a name="next-steps"></a>További lépések
Most, hogy megtanulta, hogyan kezdheti el az Azure Cosmos DB explorer t az adatok kezeléséhez, a következő lépés:

* Kezdje el definiálni a [lekérdezéseket](sql-api-query-reference.md) SQL szintaxissal, és hajtsa végre [a kiszolgálóoldali programozást](stored-procedures-triggers-udfs.md) tárolt eljárások, UDF-ek és eseményindítók használatával. 
