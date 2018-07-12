---
title: Kapcsolódás az SQL Server vagy az Azure SQL Database – Azure Logic Apps |} A Microsoft Docs
description: Elérése és kezelése a helyszíni vagy felhőalapú SQL Database-adatbázisok az Azure Logic Apps a munkafolyamatok automatizálásával
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 05/15/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: dccb91c782408a5fed5c3ef1b68f9918823ce402
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38544023"
---
# <a name="connect-to-sql-server-or-azure-sql-database-from-azure-logic-apps"></a>Csatlakozás az SQL Server vagy az Azure SQL Database az Azure Logic Apps

Ez a cikk bemutatja, hogyan férhet hozzá adataihoz a logikai alkalmazást az SQL Server-összekötő belül az SQL-adatbázisban. Ezzel a módszerrel automatizálhatja feladatok, folyamatok és munkafolyamatok, amelyek a logikai alkalmazások létrehozásával az SQL-adatok és erőforrások kezelése. Az összekötő működését is [a helyszíni SQL Server](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation) és [Azure SQL Database felhőbeli](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview). 

Futtassa, amikor az SQL database-ben vagy más rendszerekben, például a Dynamics CRM Online események által kiváltott logic Apps alkalmazásokat hozhat létre. A logic apps is beolvasása, helyezze be, és törli az adatokat SQL-lekérdezések és tárolt eljárások végrehajtása mellett. Ha például hozhat létre egy logikai alkalmazást, amely automatikusan ellenőrzi az új rekordok a Dynamics CRM Online-ban, elemeket ad hozzá az SQL database-új rekordokat, és ezután elküldi az e-mailes riasztásokhoz.

Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>. Ha most ismerkedik a logic apps, tekintse át [Mi az Azure Logic Apps](../logic-apps/logic-apps-overview.md) és [a rövid útmutató: az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md). Összekötő-specifikus technikai tudnivalókért tekintse meg a <a href="https://docs.microsoft.com/connectors/sql/" target="blank">SQL Server-összekötő-referencia</a>.

## <a name="prerequisites"></a>Előfeltételek

* A logikai alkalmazás, ahol hozzáférésre van szüksége az SQL Database-adatbázishoz. Egy SQL-trigger indít el a logikai alkalmazást, szüksége egy [üres logikai alkalmazás](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

* Egy [Azure SQL database](../sql-database/sql-database-get-started-portal.md) vagy egy [SQL Server-adatbázis](https://docs.microsoft.com/sql/relational-databases/databases/create-a-database) 

  A táblák adatait rendelkeznie kell, hogy a logikai alkalmazás adhat vissza eredményt, műveletek hívásakor. Ha létrehoz egy Azure SQL Database, használhatja a minta adatbázisokat, amelyek részét képezik. 

* Az SQL-kiszolgáló neve, adatbázis nevét, felhasználónevét és jelszavát. Ezeket a hitelesítő adatokat kell, hogy engedélyezheti a logikát, az SQL server elérése. 

  * Az Azure SQL Database ezeket az adatokat a kapcsolati karakterláncot, vagy az SQL Database-tulajdonságok mellett az Azure Portalon találhatja meg:

    "Server = tcp: <*yourServerName*>. database.windows.net,1433;Initial katalógus = <*yourDatabaseName*>; Biztonsági információ megőrzése = False; Felhasználói azonosító = <*felhasználónév*>; Jelszó = <*yourPassword*>; MultipleActiveResultSets eredménykészleteket = False; Titkosítása = True; TrustServerCertificate = False; Kapcsolat időtúllépése = 30. "

  * Az SQL Server esetében keresse meg ezeket az adatokat a kapcsolati karakterlánc: 

    "Server = <*yourServerAddress*>; adatbázis = <*yourDatabaseName*>; Felhasználói azonosító = <*felhasználónév*>; Jelszó = <*yourPassword*>; "

* A logic apps csatlakozhat a helyszíni rendszerek, például az SQL Server, előtt [állítsa be egy helyszíni adatátjárót](../logic-apps/logic-apps-gateway-install.md). Ezzel a módszerrel választhat az átjáró a logikai alkalmazás az SQL-kapcsolat létrehozásakor.

<a name="add-sql-trigger"></a>

## <a name="add-sql-trigger"></a>SQL-eseményindító hozzáadása

Az Azure Logic Appsben, mindegyik logikai alkalmazásnak kell kezdődnie, egy [eseményindító](../logic-apps/logic-apps-overview.md#logic-app-concepts), amely akkor aktiválódik, ha egy adott esemény történik, vagy ha egy adott feltétel teljesül. Minden alkalommal, amikor akkor aktiválódik, a Logic Apps-motor létrehoz egy logikaialkalmazás-példányt, és megkezdi az alkalmazás munkafolyamatában.

1. Az Azure Portalon vagy a Visual Studióban hozzon létre egy üres logikai alkalmazást, amely megnyílik a Logic Apps Designerben. Ebben a példában az Azure Portalt használja.

2. A keresőmezőbe írja be az "sql server" szűrőként. Az eseményindítók listáról válassza ki a kívánt SQL-eseményindítóhoz. 

   Ebben a példában válassza ki a következő eseményindítót: **SQL Server - elem létrehozásakor**

   ![Válassza ki az "SQL Server - elem létrehozásakor" eseményindító](./media/connectors-create-api-sqlazure/sql-server-trigger.png)

3. Ha a kapcsolat részleteivel, kér [most már az SQL-kapcsolat létrehozása](#create-connection). 
   Vagy, ha a kapcsolat már létezik, válassza ki a **táblanév** , amelyeket szeretne a listából.

   ![Tábla kiválasztása](./media/connectors-create-api-sqlazure/azure-sql-database-table.png)

4. Állítsa be a **időköz** és **gyakorisága** tulajdonságok, amelyek meghatározzák, hogy milyen gyakran az a logikai alkalmazás ellenőrzi a tábla.

   Ebben a példában csak a kijelölt táblához, semmi más ellenőrzi. 
   Valami érdekeset további műveleteket, amelyeket szeretne-e a feladatokat, adja hozzá. 
   
   Például a táblázatban az új elem megtekintéséhez, előfordulhat, hogy más műveletek hozzáadása, például az olyan fájlt, amelyben a táblában a mezők létrehozása és küld e-mail riasztást, majd. 
   Ez az összekötő vagy egyéb összekötőkhöz egyéb műveleteivel kapcsolatos tudnivalókért lásd: [Logic Apps-összekötők](../connectors/apis-list.md).

5. Ha elkészült, a Tervező eszköztárán válassza a **mentése**. 

   Ebben a lépésben automatikusan lehetővé teszi, és közzéteszi a logikai alkalmazás élő Azure-ban. 

<a name="add-sql-action"></a>

## <a name="add-sql-action"></a>SQL-művelet hozzáadása

Az Azure Logic Apps- [művelet](../logic-apps/logic-apps-overview.md#logic-app-concepts) a munkafolyamat egy eseményindító vagy egy másik műveletet a következő lépés. Ebben a példában a logikai alkalmazás kezdődik a [ismétlődési trigger](../connectors/connectors-native-recurrence.md), és meghív egy műveletet, amely beolvas egy sort egy SQL database-ből.

1. Az Azure Portalon vagy a Visual Studióban nyissa meg a logikai alkalmazás a Logic Apps Designerben. Ebben a példában az Azure Portalt használja.

2. A Logic App Designerben az eseményindítót vagy műveletet, válassza **új lépés** > **művelet hozzáadása**.

   ![Válassza az "Új lépés", "Művelet hozzáadása"](./media/connectors-create-api-sqlazure/add-action.png)
   
   Meglévő lépések közötti művelet hozzáadása, vigye az egérmutatót a csatlakozó nyílra. 
   Válassza a plusz jelre (**+**), amely akkor jelenik meg, és válassza **művelet hozzáadása**.

2. A keresőmezőbe írja be az "sql server" szűrőként. A műveletek listáról válassza ki a használni kívánt SQL semmit. 

   Ebben a példában válassza ezt a műveletet, mely egyetlen rekord: **SQL Server - sor beolvasása**

   ![Adja meg az "sql server", "SQL Server - sor beolvasása" kiválasztása](./media/connectors-create-api-sqlazure/select-sql-get-row.png) 

3. Ha a kapcsolat részleteivel, kér [most már az SQL-kapcsolat létrehozása](#create-connection). 
   Vagy, ha a kapcsolat létezik, válassza a **táblanév**, és adja meg a **Sorazonosító** a kívánt rekord.

   ![Adja meg a táblázat nevét, és a sorazonosító:](./media/connectors-create-api-sqlazure/table-row-id.png)
   
   Ebben a példában csak egy sort adja vissza a kiválasztott táblából, semmi más. 
   A sor az adatok megtekintéséhez, előfordulhat, hogy hozzáadja más műveletek, így később ellenőrizheti őket a sor származó mezőkkel hozzon létre egy fájlt, és ezt a fájlt tárolja egy felhős társzolgáltatás fiókjába. Ez az összekötő vagy egyéb összekötőkhöz lévő egyéb műveletek kapcsolatos további információkért lásd: [Logic Apps-összekötők](../connectors/apis-list.md).

4. Ha elkészült, a Tervező eszköztárán válassza a **mentése**. 

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Csatlakozás az adatbázishoz

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to SQL Server or Azure SQL Database](../../includes/connectors-create-api-sqlazure.md)]

## <a name="process-data-in-bulk"></a>Tömeges dolgozza fel az adatokat

Eredményhalmazt rendkívül nagy méretűek, hogy az összekötő nem ad vissza az összes eredmény egyszerre, vagy az eredménykészlet méretétől és felépítésétől jobban kézben szeretne dolgozni, ha *tördelés*, amelyek segítségével kezelheti azokat kisebb mint eredményeket. 

[!INCLUDE [Set up pagination for results exceeding default page size](../../includes/connectors-pagination-bulk-data-transfer.md)]

### <a name="create-a-stored-procedure"></a>Tárolt eljárás létrehozása

Első, vagy több sort szúr be, a logikai alkalmazás is végigvenni ezeket az elemeket használatával egy [ *until ciklus* ](../logic-apps/logic-apps-control-flow-loops.md#until-loop) belül ezek [korlátok](../logic-apps/logic-apps-limits-and-config.md). Azonban, egyes esetekben a logikai alkalmazás rendkívül nagy méretűek, például a több ezer vagy millió sort, az adatbázis-hívások kapcsolódó költségek minimalizálása érdekében kívánt rekordhalmazok dolgozhat. 

Ehelyett létrehozhat egy <a href="https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine" target="blank"> *tárolt eljárás* </a> , amely az SQL-példány fut, és használja a **kiválasztása – ORDER BY** utasítással rendezheti az eredményeket a kívánt módon. Ez a megoldás révén, méretétől és felépítésétől az eredmények további szabályozhatja. A logikai alkalmazás az SQL Server-összekötő használatával meghívja a tárolt eljárás **tárolt eljárás végrehajtása** művelet. 

A megoldás részletei tanulmányozza a következő cikkeket:

* <a href="https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx" target="_blank">SQL tördelés a tömeges adatátviteli Logic Apps szolgáltatással</a>

* <a href="https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql" target="_blank">Válassza ki – az ORDER BY záradék</a>

## <a name="connector-specific-details"></a>Összekötő-specifikus részletei

Ez az összekötő eseményindítókat, műveleteket és korlátokat kapcsolatos műszaki információkért lásd: a [összekötő hivatkozás részletei](/connectors/sql/). 

## <a name="next-steps"></a>További lépések

* További információk egyéb [Logic Apps-összekötők](../connectors/apis-list.md)

