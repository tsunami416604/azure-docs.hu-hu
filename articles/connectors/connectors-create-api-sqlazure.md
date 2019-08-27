---
title: Kapcsolódás SQL Server vagy Azure SQL Database-Azure Logic Appshoz | Microsoft Docs
description: A helyszíni vagy a felhőben található SQL-adatbázisok elérése és kezelése a munkafolyamatok automatizálásával Azure Logic Apps
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
ms.openlocfilehash: 804a913d17c3151d07a1ecf229e2db148dc45558
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050765"
---
# <a name="connect-to-sql-server-or-azure-sql-database-from-azure-logic-apps"></a>Kapcsolódás SQL Server vagy Azure SQL Database a Azure Logic Apps

Ez a cikk bemutatja, hogyan érheti el az SQL Database-ben tárolt adatok egy logikai alkalmazásban az SQL Server-összekötő használatával. Így automatizálhatja az SQL-adatok és-erőforrások kezelésére szolgáló feladatokat, folyamatokat és munkafolyamatokat a logikai alkalmazások létrehozásával. Az összekötő a helyszíni és a [felhőbeli Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview) [SQL Server](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation) is működik. 

Létrehozhat olyan logikai alkalmazásokat, amelyek az SQL-adatbázisban vagy más rendszerekben, például a Dynamics CRM Online-ban indított események indításakor futnak. A logikai alkalmazások az SQL-lekérdezések és a tárolt eljárások végrehajtásával is lekérhetik, behelyezhetik és törölhetik az adatforrásokat. Létrehozhat például egy olyan logikai alkalmazást, amely automatikusan ellenőrzi az új rekordokat a Dynamics CRM Online-ban, minden új rekordhoz hozzáadja az elemeket az SQL-adatbázishoz, majd e-mail-riasztásokat küld.

Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). Ha most ismerkedik a Logic apps szolgáltatással, tekintse át [a mi az Azure Logic apps](../logic-apps/logic-apps-overview.md) és [a gyors útmutató: Hozza létre az első logikai](../logic-apps/quickstart-create-first-logic-app-workflow.md)alkalmazását. Az összekötő-specifikus technikai információk az SQL Server- [összekötő referenciájában](https://docs.microsoft.com/connectors/sql/)olvashatók.

## <a name="prerequisites"></a>Előfeltételek

* Az a logikai alkalmazás, amelyben hozzá kell férnie az SQL-adatbázishoz. A logikai alkalmazás SQL-triggerrel való elindításához [üres logikai alkalmazásra](../logic-apps/quickstart-create-first-logic-app-workflow.md)van szükség. 

* Egy [Azure SQL Database-adatbázis](../sql-database/sql-database-get-started-portal.md) vagy egy [SQL Server-adatbázis](https://docs.microsoft.com/sql/relational-databases/databases/create-a-database) 

  A tábláknak adatokkal kell rendelkezniük, hogy a logikai alkalmazás a műveletek meghívásakor eredményeket lehessen visszaadni. Ha Azure SQL Database hoz létre, használhat példákat tartalmazó adatbázisokat is. 

* Az SQL-kiszolgáló nevét, az adatbázis nevét, a felhasználónevét és a jelszavát. Ezekre a hitelesítő adatokra akkor van szükség, ha engedélyezni szeretné a logikáját az SQL Serverhez való hozzáféréshez. 

  * Azure SQL Database ezeket a részleteket a kapcsolati karakterláncban találja, vagy a Azure Portal a SQL Database tulajdonságok területen:

    "Server = TCP: <*yourServerName*>. database. Windows. net, 1433; kezdeti katalógus = <*yourDatabaseName*>; Biztonsági adatok megőrzése = hamis; Felhasználói azonosító = <*felhasználónév*>; Password = <*yourPassword*>; Eredménykészleteket = false; Titkosítás = igaz; TrustServerCertificate = false; Kapcsolat időkorlátja = 30; "

  * SQL Server a következő adatokat találja meg a kapcsolati karakterláncban: 

    "Server = <*yourServerAddress*>;D atabase = <*yourDatabaseName*>; Felhasználói azonosító = <*felhasználónév*>; Password = <*yourPassword*>; "

* Ahhoz, hogy a logikai alkalmazásokat a helyszíni rendszerekhez, például a SQL Serverhoz lehessen kapcsolni, [be kell állítania egy](../logic-apps/logic-apps-gateway-install.md)helyszíni adatátjárót. Így kiválaszthatja az átjárót a logikai alkalmazáshoz tartozó SQL-kapcsolatok létrehozásakor.

<a name="add-sql-trigger"></a>

## <a name="add-sql-trigger"></a>SQL-trigger hozzáadása

Azure Logic Apps minden logikai alkalmazásnak egy eseményindítóval kell kezdődnie [](../logic-apps/logic-apps-overview.md#logic-app-concepts), amely akkor következik be, amikor egy adott esemény történik, vagy ha egy adott feltétel teljesül. A Logic Apps motor létrehoz egy Logic app-példányt, és elindítja az alkalmazás munkafolyamatát.

1. A Azure Portal vagy a Visual Studióban hozzon létre egy üres logikai alkalmazást, amely megnyitja Logic Apps designert. Ez a példa a Azure Portal használja.

2. A keresőmezőbe írja be szűrőként az "SQL Server" kifejezést. Az eseményindítók listából válassza ki a kívánt SQL-eseményindítót. 

   Ebben a példában válassza ezt az triggert: **SQL Server – egy tétel létrehozásakor**

   ![Válassza a "SQL Server – elem létrehozásakor" triggert](./media/connectors-create-api-sqlazure/sql-server-trigger.png)

3. Ha a rendszer megkérdezi a kapcsolat részleteit, [hozza létre most az SQL](#create-connection)-kapcsolatát. 
   Ha már létezik a kapcsolatai, válassza ki a listából a kívánt **Táblanév nevét** .

   ![Tábla kiválasztása](./media/connectors-create-api-sqlazure/azure-sql-database-table.png)

4. Adja meg az **intervallum** és a **gyakoriság** tulajdonságait, amely meghatározza, hogy a logikai alkalmazás milyen gyakran ellenőrizze a táblát.

   Ez a példa csak a kijelölt táblázatot ellenőrzi, semmi más. 
   Ha valami érdekeset szeretne tenni, adja hozzá a kívánt feladatokat végrehajtó műveleteket. 
   
   Ha például meg szeretné tekinteni a tábla új elemét, további műveleteket is hozzáadhat, például létrehozhat egy olyan fájlt, amely mezőket tartalmaz a táblából, majd elküldheti az e-mailes riasztásokat. 
   Az összekötőhöz vagy más összekötőhöz tartozó egyéb műveletekkel kapcsolatos további tudnivalókért lásd: [Logic apps](../connectors/apis-list.md)-összekötők.

5. Ha elkészült, a tervező eszköztárán válassza a **Mentés**lehetőséget. 

   Ez a lépés automatikusan engedélyezi és közzéteszi a logikai alkalmazást az Azure-ban. 

<a name="add-sql-action"></a>

## <a name="add-sql-action"></a>SQL-művelet hozzáadása

Azure Logic Apps a [művelet](../logic-apps/logic-apps-overview.md#logic-app-concepts) egy olyan lépés a munkafolyamatban, amely egy triggert vagy egy másik műveletet követ. Ebben a példában a logikai alkalmazás az [ismétlődési eseményindítóval](../connectors/connectors-native-recurrence.md)kezdődik, és olyan műveletet hív meg, amely egy SQL-adatbázisból származó sort kap.

1. A Azure Portal vagy a Visual Studióban nyissa meg a logikai alkalmazást Logic Apps Designerben. Ez a példa a Azure Portal használja.

2. A Logic app Designerben az trigger vagy a művelet alatt válassza az **új lépés** > **művelet hozzáadása**lehetőséget.

   ![Válassza az "új lépés", "művelet hozzáadása" lehetőséget.](./media/connectors-create-api-sqlazure/add-action.png)
   
   A meglévő lépések közötti művelet hozzáadásához vigye az egeret a csatlakozás nyíl fölé. 
   Válassza ki a megjelenő pluszjelet ( **+** ), majd válassza a **művelet hozzáadása**lehetőséget.

2. A keresőmezőbe írja be szűrőként az "SQL Server" kifejezést. A műveletek listából válassza ki a kívánt SQL-műveletet. 

   Ebben a példában válassza ezt a műveletet, amely egyetlen rekordot kap: **SQL Server – sor beolvasása**

   ![Adja meg az "SQL Server" értéket, válassza a "SQL Server-sor beolvasása" lehetőséget.](./media/connectors-create-api-sqlazure/select-sql-get-row.png) 

3. Ha a rendszer megkérdezi a kapcsolat részleteit, [hozza létre most az SQL](#create-connection)-kapcsolatát. 
   Ha a hálózat már létezik, válassza ki a **Táblanév nevet**, és adja meg a kívánt rekordhoz tartozó **sort** .

   ![Adja meg a tábla nevét és a sor AZONOSÍTÓját](./media/connectors-create-api-sqlazure/table-row-id.png)
   
   Ez a példa csak egy sort ad vissza a kijelölt táblából, semmi más. 
   Az ebben a sorban található adatok megtekintéséhez további műveleteket is hozzáadhat, amelyek a sorokból származó mezőket hoznak létre a későbbi áttekintés céljából, és a fájlt egy felhőalapú Storage-fiókban tárolják. Az összekötőn vagy más összekötőn található egyéb műveletekkel kapcsolatos további tudnivalókért lásd: [Logic apps](../connectors/apis-list.md)-összekötők.

4. Ha elkészült, a tervező eszköztárán válassza a **Mentés**lehetőséget. 

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Csatlakozás az adatbázishoz

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to SQL Server or Azure SQL Database](../../includes/connectors-create-api-sqlazure.md)]

## <a name="handle-bulk-data"></a>Tömeges adatkezelés

Időnként előfordulhat, hogy az eredményhalmazban együtt kell működnie, hogy az összekötő ne adja vissza az összes eredményt egyszerre, vagy az eredményhalmaz méretének és szerkezetének jobb szabályozását szeretné. A következő módszerekkel kezelheti az ilyen nagy eredményeket:

* Az eredmények kisebb készletekként való kezeléséhez kapcsolja be a *tördelést*. További információ: [tömeges adatok, rekordok és elemek beolvasása a tördelés használatával](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md).

* Hozzon létre egy tárolt eljárást, amely a kívánt módon rendezi az eredményeket.

  Több sor beolvasása vagy beillesztése esetén a logikai alkalmazás ezen sorokon keresztül is megismételheti [](../logic-apps/logic-apps-control-flow-loops.md#until-loop) ezeket a sorokat. [](../logic-apps/logic-apps-limits-and-config.md) 
  Ha azonban a logikai alkalmazásnak a rekordhalmazokkal együtt kell működnie, például több ezer vagy több millió sorból, akkor az adatbázis felé irányuló hívások költségeinek minimalizálására van szükség.

  Az eredmények a kívánt módon történő rendezéséhez létrehozhat egy [*tárolt eljárást*](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) , amely az SQL-példányban fut, és a **Select-Order by** utasítást használja. 
  Ez a megoldás az eredmények méretének és szerkezetének nagyobb mértékű szabályozását teszi lehetővé. 
  A logikai alkalmazás meghívja a tárolt eljárást az SQL Server-összekötő által **tárolt eljárás végrehajtása** művelettel.

  A megoldás részleteiért tekintse meg a következő cikkeket:

  * [SQL-tördelés a tömeges adatátvitel Logic Apps](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx)

  * [SELECT-ORDER BY záradék](https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql)

## <a name="connector-specific-details"></a>Összekötő-specifikus részletek

Az összekötő eseményindítókkal, műveletekkel és korlátozásokkal kapcsolatos technikai információkért tekintse [meg az összekötő hivatkozási adatait](/connectors/sql/). 

## <a name="next-steps"></a>További lépések

* További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése

