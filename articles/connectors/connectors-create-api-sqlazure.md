---
title: Csatlakozás az SQL Server vagy az Azure SQL adatbázis - Azure Logic Apps |} Microsoft Docs
description: Kapcsolatok létrehozása a helyszíni SQL Server és a felhőben az Azure SQL Database az Azure Logic Apps
services: logic-apps
documentationcenter: ''
author: ecfan
manager: cfowler
editor: ''
tags: connectors
ms.assetid: d8a319d0-e4df-40cf-88f0-29a6158c898c
ms.service: logic-apps
ms.workload: logic-apps
ms.devlang: ''
ms.tgt_pltfrm: ''
ms.topic: article
ms.date: 05/15/2018
ms.author: estfan
ms.openlocfilehash: 4917f784c07919155e006711026899ce7712fecb
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
---
# <a name="connect-to-sql-server-or-azure-sql-database-from-azure-logic-apps"></a>Csatlakozás SQL Server vagy az Azure SQL Database a Azure Logic Apps alkalmazásokból

Ez a cikk bemutatja, hogyan lehet elérni az adatokat az SQL Server-összekötő használatával logikai alkalmazás belül az SQL-adatbázisban. Ily módon a logic apps, feladatok és az adatok kezelésére szolgáló munkafolyamatok automatizáló is létrehozhat. A connector működik, ha mindkét [a helyszíni SQL Server](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation) és [Azure SQL Database felhőbeli](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview). 

A logic apps, amikor az SQL-adatbázis vagy más rendszerekre, például a Dynamics CRM Online események által kiváltott futtató hozhat létre. A logic Apps alkalmazásokat is, helyezze, vagy törli az adatokat, és is az SQL-lekérdezések, illetve tárolt eljárások végrehajtása. Például hozhat létre egy logikai alkalmazás automatikusan ellenőrzi az új bejegyzések a Dynamics CRM Online, elemek hozzáadása új bejegyzésekhez az SQL-adatbázis, amely ezután elküldi az e-mailes riasztásokhoz.

Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>. Ha most ismerkedik a logic apps, tekintse át a [Mi az Azure Logic Apps](../logic-apps/logic-apps-overview.md) és [gyors üzembe helyezés: az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md). Összekötő-specifikus műszaki információkért lásd: a <a href="https://docs.microsoft.com/connectors/sql/" target="blank">SQL Server-összekötő hivatkozás</a>.

## <a name="prerequisites"></a>Előfeltételek

* A logikai alkalmazást hozzáférést kell az SQL-adatbázis. A Logic Apps alkalmazást az SQL eseményindító elindításához szükséges egy [üres logikai alkalmazás](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

* Egy [Azure SQL adatbázis](../sql-database/sql-database-get-started-portal.md) vagy egy [SQL Server-adatbázis](https://docs.microsoft.com/sql/relational-databases/databases/create-a-database) 

  A táblák adataival kell rendelkeznie, hogy a Logic Apps alkalmazást is adja vissza az eredményeket műveletek hívásakor. Ha egy Azure SQL Database adatbázist hoz létre, használhatja mintaadatbázisokat, amely tartalmazza. 

* Az SQL-kiszolgáló neve, az adatbázisnév, a felhasználónevét és jelszavát. Ezeket a hitelesítő adatokat kell, hogy engedélyezheti a logika az SQL-kiszolgáló elérésére. 

  * Az Azure SQL Database ezeket az adatokat a kapcsolati karakterlánc, vagy az SQL adatbázis-tulajdonságok az Azure portál találja meg:

    "Server = tcp: <*yourServerName*>. database.windows.net,1433;Initial katalógus = <*yourDatabaseName*>; Biztonsági információ megőrzése = False; Felhasználói azonosító = <*felhasználónév*>; Jelszó = <*yourPassword*>; MultipleActiveResultSets eredménykészleteket = False; Titkosítani = True; TrustServerCertificate = False; Kapcsolódási időtúllépés = 30; "

  * Az SQL Server a kapcsolati karakterláncban találja meg ezeket az adatokat: 

    "Server = <*yourServerAddress*>; adatbázis = <*yourDatabaseName*>; Felhasználói azonosító = <*felhasználónév*>; Jelszó = <*yourPassword*>; "

* A logic apps csatlakozhat a helyszíni rendszerekre, például az SQL Server előtt [állítson be egy helyszíni adatátjárót](../logic-apps/logic-apps-gateway-install.md). Ily módon kiválaszthatja az átjáró amikor az SQL-kapcsolatot hoz létre a Logic Apps alkalmazást.

<a name="add-sql-trigger"></a>

## <a name="add-sql-trigger"></a>SQL eseményindító hozzáadása

Az Azure Logic Apps, minden logikai alkalmazást be kell kezdődnie egy [eseményindító](../logic-apps/logic-apps-overview.md#logic-app-concepts), amely akkor indul, amikor egy adott esemény történik, vagy ha egy adott feltétel teljesül. Minden alkalommal, amikor az eseményindító következik be, a Logic Apps motor hoz létre egy logic app-példány, az alkalmazás a munkafolyamat futásának indításakor.

1. Az Azure portálon vagy a Visual Studio hozzon létre egy üres logikai alkalmazás, amely megnyitja a Logic Apps-tervezőben. A példa az Azure-portálon.

2. A keresési mezőbe írja be az "sql server" szűrőként. Az eseményindítók listájából válassza ki a használni kívánt SQL eseményindító. 

   Ehhez a példához válassza ki az ehhez az eseményindítóhoz: **SQL Server - elem létrehozásakor**

   ![Válassza ki az "SQL Server - elem létrehozásakor" eseményindító](./media/connectors-create-api-sqlazure/sql-server-trigger.png)

3. Ha a kapcsolat részletes számítógép [most az SQL-kapcsolat létrehozása](#create-connection). 
   Vagy, ha a kapcsolat már létezik, válassza ki a **táblanév** , amelyet a listából.

   ![Tábla kiválasztása](./media/connectors-create-api-sqlazure/azure-sql-database-table.png)

4. Állítsa be a **időköz** és **gyakoriság** tulajdonságait, adja meg a Logic Apps alkalmazást milyen gyakran kérdezze le a táblázatban.

   Ebben a példában csak a kijelölt táblához, semmi mást ellenőrzi. 
   Valami ennél is érdekesebb megoldást, amelyek a kívánt feladatot, adja hozzá. 
   
   Például szeretné megtekinteni az új elem a táblázatban, akkor előfordulhat, hogy más műveletek, például hozzon létre a tábla mezői fájlt és küld e-mail riasztást, majd. 
   Egyéb műveleteket ezzel az összekötővel vagy a többi összekötőt, lásd: [Logic Apps-összekötők](../connectors/apis-list.md).

5. Amikor elkészült, a designer eszköztáron válassza **mentése**. 

   Ebben a lépésben automatikusan lehetővé teszi, hogy és közzéteszi a Logic Apps alkalmazást az élő Azure-ban. 

<a name="add-sql-action"></a>

## <a name="add-sql-action"></a>SQL-művelet hozzáadása

Az Azure Logic Apps egy [művelet](../logic-apps/logic-apps-overview.md#logic-app-concepts) a munkafolyamatban, amely egy eseményindító vagy egy másik művelet a következő lépés. Ebben a példában a logikai alkalmazás kezdődik-e a [ismétlődési eseményindító](../connectors/connectors-native-recurrence.md), és egy műveletet, amely egy sort lekérdezi egy SQL-adatbázisból.

1. Az Azure portálon vagy a Visual Studio nyissa meg a Logic Apps alkalmazást Logic Apps-tervezőben. A példa az Azure-portálon.

2. Válassza ki az eseményindító vagy a művelet, a Logic App Designer **új lépés** > **művelet hozzáadása**.

   !["Új lépés", "Művelet hozzáadása" Válasszon](./media/connectors-create-api-sqlazure/add-action.png)
   
   Meglévő lépései közötti művelet hozzáadása az egérmutatót a kapcsolódó nyílra. 
   Válassza ki a plusz jelre (**+**), amely akkor jelenik meg, és válassza a **művelet hozzáadása**.

2. A keresési mezőbe írja be az "sql server" szűrőként. A műveletek listáról válassza ki a használni kívánt SQL beavatkozást. 

   Ebben a példában válassza Ez a művelet lekérdezi egy rekord: **SQL Server - Get sor**

   ![Adja meg az "sql server", "SQL Server - Get sor" kiválasztása](./media/connectors-create-api-sqlazure/select-sql-get-row.png) 

3. Ha a kapcsolat részletes számítógép [most az SQL-kapcsolat létrehozása](#create-connection). 
   Vagy ha a kapcsolat létezik, egy **táblanév**, és írja be a **Sorazonosító** , amelyet a rekord.

   ![Adja meg a táblanevet és sorazonosító:](./media/connectors-create-api-sqlazure/table-row-id.png)
   
   Ebben a példában a kijelölt táblából, semmi mást csak egy sort adja vissza. 
   Ezt a sort a adatainak megtekintéséhez, előfordulhat, hogy hozzáadja más műveletek, hozzon létre egy fájl újabb tekintse át a sort a mezőket, és tárolja a fájlt a felhőalapú társzolgáltatás fiókja. Ezt az összekötőt, vagy más összekötők lévő egyéb műveletek kapcsolatos további tudnivalókért lásd: [Logic Apps-összekötők](../connectors/apis-list.md).

4. Amikor elkészült, a designer eszköztáron válassza **mentése**. 

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Csatlakozás az adatbázishoz

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to SQL Server or Azure SQL Database](../../includes/connectors-create-api-sqlazure.md)]

## <a name="process-data-in-bulk"></a>A tömeges adatfeldolgozásra

Használatakor, hogy az összekötő nem ad vissza az eredményeket egy időben, vagy több méretétől és felépítésétől használni szeretne az eredménykészlet nagy eredményhalmazt, használhat *tördelési*, amelyen kezelheti azokat kisebb beállítása eredményeket. 

[!INCLUDE [Set up pagination for results exceeding default page size](../../includes/connectors-pagination-bulk-data-transfer.md)]

### <a name="create-a-stored-procedure"></a>Hozzon létre egy tárolt eljárás

Első, vagy több sort beszúrni, ha a Logic Apps alkalmazást is iterációt ezeket az elemeket a egy [ *amíg hurok* ](../logic-apps/logic-apps-control-flow-loops.md#until-loop) belül ezek [korlátok](../logic-apps/logic-apps-limits-and-config.md). De, néha a Logic Apps alkalmazást annyira nagy, például a több ezer vagy több millió sort, az adatbázis-hívásokban költségek minimalizálása kívánt rekordhalmazok együttműködni. 

Ehelyett, létrehozhat egy <a href="https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine" target="blank"> *tárolt eljárás* </a> , amely az SQL-példány fut, és használja a **kiválasztása - ORDER BY** nyilatkozatot, így az eredményeket a kívánt módon rendezheti. Ez a megoldás lehetővé teszi az méretétől és felépítésétől az eredmények teljesebb körű vezérlése. A Logic Apps alkalmazást hívja a következő tárolt eljárást az SQL Server-összekötő használatával **tárolt eljárás végrehajtása** művelet. 

Megoldás további információkért lásd: ezek a cikkek:

* <a href="https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx" target="_blank">A Logic Apps tömeges adatátvitel SQL tördelési</a>

* <a href="https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql" target="_blank">Válassza ki,-ORDER BY záradék</a>

## <a name="connector-specific-details"></a>Összekötő-specifikus részletei

Az összekötő eseményindítókat, műveletek és korlátok kapcsolatos műszaki információkért lásd: a [összekötő hivatkozási adatokat](/connectors/sql/). 

## <a name="next-steps"></a>További lépések

* Tudnivalók más [Logic Apps-összekötők](../connectors/apis-list.md)

