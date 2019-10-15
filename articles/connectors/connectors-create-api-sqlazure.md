---
title: Kapcsolódás SQL Server vagy Azure SQL Database-Azure Logic Apps
description: A helyszínen vagy a felhőben található SQL-adatbázisok feladatainak automatizálása Azure Logic Apps használatával
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam; LADocs
manager: carmonm
ms.topic: conceptual
tags: connectors
ms.date: 10/14/2019
ms.openlocfilehash: 6c86ef26bbf7bd9dbce8aa77aef2213b14b57f5f
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/14/2019
ms.locfileid: "72311951"
---
# <a name="automate-workflows-for-sql-server-or-azure-sql-database-by-using-azure-logic-apps"></a>SQL Server vagy Azure SQL Database munkafolyamatainak automatizálása Azure Logic Apps használatával

Ez a cikk bemutatja, hogyan érheti el az SQL Database-ben tárolt adatok egy logikai alkalmazásban az SQL Server-összekötő használatával. Így automatizálhatja az SQL-adatok és-erőforrások kezelésére szolgáló feladatokat, folyamatokat és munkafolyamatokat logikai alkalmazások létrehozásával. Az SQL Server-összekötő a helyszíni [SQL Server](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation) és a [felhőalapú Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)esetében egyaránt működik.

Létrehozhat olyan logikai alkalmazásokat, amelyek az SQL-adatbázisban vagy más rendszerekben, például a Dynamics CRM Online-ban indított események indításakor futnak. A logikai alkalmazások az SQL-lekérdezések és a tárolt eljárások futtatásával is lekérhetik, behelyezhetik és törölhetik az adatforrásokat. Létrehozhat például egy olyan logikai alkalmazást, amely automatikusan ellenőrzi az új rekordokat a Dynamics CRM Online-ban, elemeket ad hozzá az SQL-adatbázishoz bármely új rekordhoz, majd e-mail-riasztásokat küld a hozzáadott elemekről.

Ha most ismerkedik a Logic apps szolgáltatással, tekintse át a [Mi az Azure Logic apps](../logic-apps/logic-apps-overview.md) és a gyors útmutató [: az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)lehetőséget. Az összekötő-specifikus technikai információkkal, korlátozásokkal és ismert problémákkal kapcsolatban tekintse meg az [SQL Server-összekötő hivatkozását ismertető oldalt](https://docs.microsoft.com/connectors/sql/).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Egy [SQL Server adatbázis](https://docs.microsoft.com/sql/relational-databases/databases/create-a-database) vagy [Azure SQL Database](../sql-database/sql-database-get-started-portal.md)

  A tábláknak adatokkal kell rendelkezniük, hogy a logikai alkalmazás a műveletek meghívásakor eredményeket lehessen visszaadni. Ha Azure SQL Database hoz létre, használhat példákat tartalmazó adatbázisokat is.

* Az SQL-kiszolgáló nevét, az adatbázis nevét, a felhasználónevét és a jelszavát. Ezekre a hitelesítő adatokra akkor van szükség, ha engedélyezni szeretné a logikáját az SQL Serverhez való hozzáféréshez.

  * SQL Server a következő adatokat találja meg a kapcsolati karakterláncban:

    `Server={your-server-address};Database={your-database-name};User Id={your-user-name};Password={your-password};`

  * Azure SQL Database ezeket a részleteket a kapcsolati karakterláncban találja, vagy a Azure Portal a SQL Database tulajdonságok területen:

    `Server=tcp:{your-server-name}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`

* Ahhoz, hogy a logikai alkalmazásokat a helyszíni rendszerekhez, például a SQL Serverhoz lehessen kapcsolni, [be kell állítania egy helyszíni adatátjárót](../logic-apps/logic-apps-gateway-install.md). Így kiválaszthatja az átjárót a logikai alkalmazáshoz tartozó SQL-kapcsolatok létrehozásakor.

* Az a logikai alkalmazás, amelyben hozzá kell férnie az SQL-adatbázishoz. A logikai alkalmazás SQL-triggerrel való elindításához [üres logikai alkalmazásra](../logic-apps/quickstart-create-first-logic-app-workflow.md)van szükség.

<a name="add-sql-trigger"></a>

## <a name="add-a-sql-trigger"></a>SQL-trigger hozzáadása

Azure Logic Apps minden logikai alkalmazásnak egy [eseményindítóval](../logic-apps/logic-apps-overview.md#logic-app-concepts)kell kezdődnie, amely akkor következik be, amikor egy adott esemény történik, vagy ha egy adott feltétel teljesül. A Logic Apps motor minden alkalommal elindít egy Logic app-példányt, és elindítja a logikai alkalmazás munkafolyamatának futtatását.

1. A Azure Portal vagy a Visual Studióban hozzon létre egy üres logikai alkalmazást, amely megnyitja Logic Apps designert. Ez a példa a Azure Portal használja.

1. A tervezőben a keresőmezőbe írja be szűrőként az "SQL Server" kifejezést. Az eseményindítók listából válassza ki a kívánt SQL-eseményindítót.

   Ez a példa **azt használja, hogy mikor hozzon létre triggert egy elemmel** .

   !["Az elem létrehozásakor" trigger kiválasztása](./media/connectors-create-api-sqlazure/select-sql-server-trigger.png)

1. Ha a rendszer kéri, hogy hozzon létre egy-egy kapcsolódást, [hozzon létre most egy SQL-kapcsolódást](#create-connection). Ha a kapcsolatok már léteznek, válassza ki a **Táblanév nevet**.

   ![Válassza ki a kívánt táblázatot](./media/connectors-create-api-sqlazure/azure-sql-database-table.png)

1. Adja meg az **intervallum** és a **gyakoriság** tulajdonságait, amely meghatározza, hogy a logikai alkalmazás milyen gyakran ellenőrizze a táblát.

   Ez az trigger csak egy sort ad vissza a kijelölt táblából, semmi más. Egyéb feladatok elvégzéséhez vegyen fel további műveleteket, amelyek végrehajtják a kívánt feladatokat. Ha például meg szeretné tekinteni az ebben a sorban lévő adatok megjelenítését, hozzáadhat más műveleteket is, amelyek a visszaadott sorból származó mezőket tartalmazzák, majd e-mail-riasztásokat küldenek. Ha többet szeretne megtudni az összekötőhöz elérhető egyéb műveletekről, tekintse [meg az összekötő hivatkozási oldalát](/connectors/sql/).

1. Ha elkészült, a tervező eszköztárán válassza a **Mentés**lehetőséget.

   Ez a lépés automatikusan engedélyezi és közzéteszi a logikai alkalmazást az Azure-ban.

<a name="add-sql-action"></a>

## <a name="add-a-sql-action"></a>SQL-művelet hozzáadása

Azure Logic Apps a [művelet](../logic-apps/logic-apps-overview.md#logic-app-concepts) egy olyan lépés a munkafolyamatban, amely egy triggert vagy egy másik műveletet követ. Ebben a példában a logikai alkalmazás az [ismétlődési eseményindítóval](../connectors/connectors-native-recurrence.md)kezdődik, és olyan műveletet hív meg, amely egy SQL-adatbázisból származó sort kap.

1. A Azure Portal vagy a Visual Studióban nyissa meg a logikai alkalmazást Logic Apps Designerben. Ez a példa a Azure Portal használja.

1. Az aktiválás vagy művelet alatt, ahol az SQL-műveletet hozzá szeretné adni, válassza az **új lépés**lehetőséget.

   ![Válassza az "új lépés" lehetőséget](./media/connectors-create-api-sqlazure/select-new-step-logic-app.png)

   A meglévő lépések közötti művelet hozzáadásához vigye az egeret a csatlakozás nyíl fölé. Válassza ki a megjelenő pluszjelet ( **+** ), majd válassza a **művelet hozzáadása**lehetőséget.

1. A **válasszon műveletet**területen a keresőmezőbe írja be szűrőként az "SQL Server" kifejezést. A műveletek listából válassza ki a kívánt SQL-műveletet.

   Ez a példa a **sor beolvasása** műveletet használja, amely egyetlen rekordot kap.

   ![Az SQL "sor beolvasása" művelet megkeresése és kiválasztása](./media/connectors-create-api-sqlazure/select-sql-get-row.png)

   Ez a művelet csak egy sort ad vissza a kijelölt táblából, semmi más. Az ebben a sorban lévő adatok megtekintéséhez hozzáadhat más műveleteket is, amelyek a visszaadott sorból származó mezőket tartalmazzák, és a fájlt egy felhőalapú Storage-fiókban tárolják. Ha többet szeretne megtudni az összekötőhöz elérhető egyéb műveletekről, tekintse [meg az összekötő hivatkozási oldalát](/connectors/sql/).

1. Ha a rendszer kéri, hogy hozzon létre egy-egy kapcsolódást, [hozzon létre most egy SQL-kapcsolódást](#create-connection). Ha a hálózat már létezik, válassza ki a **Táblanév nevet**, és adja meg a kívánt rekordhoz tartozó **sort** .

   ![Adja meg a tábla nevét és a sor AZONOSÍTÓját](./media/connectors-create-api-sqlazure/table-row-id.png)

1. Ha elkészült, a tervező eszköztárán válassza a **Mentés**lehetőséget.

   Ez a lépés automatikusan engedélyezi és közzéteszi a logikai alkalmazást az Azure-ban.

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Csatlakozás az adatbázishoz

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to SQL Server or Azure SQL Database](../../includes/connectors-create-api-sqlazure.md)]

## <a name="handle-bulk-data"></a>Tömeges adatkezelés

Időnként úgy kell dolgoznia az eredményhalmazt, hogy az összekötő ne adja vissza az összes eredményt egyszerre, vagy az eredményhalmaz méretének és szerkezetének jobb szabályozását szeretné. A következő módszerekkel kezelheti az ilyen nagy eredményeket:

* Az eredmények kisebb készletekként való kezeléséhez kapcsolja be a *tördelést*. További információ: [tömeges adatok, rekordok és elemek beolvasása a tördelés használatával](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md).

* Hozzon létre egy tárolt eljárást, amely a kívánt módon rendezi az eredményeket.

  Több sor beolvasása vagy beillesztése esetén a logikai [alkalmazás ezen](../logic-apps/logic-apps-limits-and-config.md)sorokon keresztül is megismételheti [*ezeket a sorokat*](../logic-apps/logic-apps-control-flow-loops.md#until-loop) . Ha azonban a logikai alkalmazásnak a rekordhalmazokkal együtt kell működnie, például több ezer vagy több millió sorból, akkor az adatbázis felé irányuló hívások költségeinek minimalizálására van szükség.

  Az eredmények a kívánt módon történő rendezéséhez létrehozhat egy [*tárolt eljárást*](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) , amely az SQL-példányban fut, és a **Select-Order by** utasítást használja. Ez a megoldás az eredmények méretének és szerkezetének nagyobb mértékű szabályozását teszi lehetővé. A logikai alkalmazás meghívja a tárolt eljárást az SQL Server-összekötő által **tárolt eljárás végrehajtása** művelettel.

  További megoldási részletekért tekintse meg a következő cikkeket:

  * [SQL-tördelés a tömeges adatátvitel Logic Apps](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx)

  * [SELECT-ORDER BY záradék](https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql)

## <a name="connector-specific-details"></a>Összekötő-specifikus részletek

Az összekötő eseményindítókkal, műveletekkel és korlátozásokkal kapcsolatos technikai információkért tekintse [meg az összekötő hivatkozási oldalát](/connectors/sql/).

## <a name="next-steps"></a>Következő lépések

* További tudnivalók [a Azure Logic apps-összekötők](../connectors/apis-list.md) használatáról