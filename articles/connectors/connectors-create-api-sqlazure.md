---
title: Csatlakozás SQL Server vagy Azure SQL Database alkalmazáshoz
description: Az Azure Logic Apps használatával automatizálhatja a helyszíni vagy felhőbeli SQL-adatbázisok feladatait
services: logic-apps
ms.suite: integration
ms.reviewer: klam; logicappspm
ms.topic: conceptual
ms.date: 11/08/2019
tags: connectors
ms.openlocfilehash: 93b63d332f00c31a352c11e483fc3ce5cb45a922
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789195"
---
# <a name="automate-workflows-for-sql-server-or-azure-sql-database-by-using-azure-logic-apps"></a>Az SQL Server vagy az Azure SQL Database munkafolyamatainak automatizálása az Azure Logic Apps használatával

Ez a cikk bemutatja, hogyan érheti el az SQL-adatbázis adatait egy logikai alkalmazáson belülről az SQL Server-összekötővel. Így automatizálhatja az SQL-adatokat és -erőforrásokat kezelő feladatokat, folyamatokat és munkafolyamatokat logikai alkalmazások létrehozásával. Az SQL Server-összekötő a [helyszíni SQL Server](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation) és a [felhőalapú Azure SQL Database esetén](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)is működik.

Létrehozhat logikai alkalmazásokat, amelyek akkor futnak, ha az SQL-adatbázisban vagy más rendszerekben, például a Dynamics CRM Online-ban események aktiválják őket. A logikai alkalmazások is lekérik, beszúrják és törlik az adatokat az SQL-lekérdezések és a tárolt eljárások futtatásával együtt. Létrehozhat például egy logikai alkalmazást, amely automatikusan megkeresi az új rekordokat a Dynamics CRM Online rendszerben, elemeket ad hozzá az SQL-adatbázishoz az új rekordokhoz, majd e-mailben értesítéseket küld a hozzáadott elemekről.

Ha most kezdi meg a logikai alkalmazásokat, tekintse át [az Azure Logic Apps](../logic-apps/logic-apps-overview.md) és a Rövid útmutató: Az első logikai alkalmazás létrehozása című ismertetése című ismertetése című ismertetése. [Quickstart: Create your first logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md) Az összekötő-specifikus műszaki információkat, korlátozásokat és ismert problémákat az [SQL Server összekötő referencialapján](https://docs.microsoft.com/connectors/sql/)találja.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* [SQL Server-](https://docs.microsoft.com/sql/relational-databases/databases/create-a-database) vagy [Azure SQL-adatbázis](../sql-database/sql-database-get-started-portal.md)

  A tábláknak adatokkal kell rendelkezniük, hogy a logikai alkalmazás eredményeket adhasson vissza a műveletek hívásakor. Ha létrehoz egy Azure SQL-adatbázist, használhatja a minta adatbázisok, amelyek szerepelnek.

* Az SQL-kiszolgáló neve, az adatbázis neve, a felhasználóneve és a jelszava. Ezekre a hitelesítő adatokra van szüksége, hogy engedélyezze a logika iSQL-kiszolgáló elérését.

  * Az SQL Server esetében ezeket az adatokat a kapcsolati karakterláncban találja:

    `Server={your-server-address};Database={your-database-name};User Id={your-user-name};Password={your-password};`

  * Az Azure SQL Database esetében ezeket az adatokat a kapcsolati karakterláncban vagy az Azure Portalon találja az SQL Database tulajdonságai alatt:

    `Server=tcp:{your-server-name}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`

* A helyi számítógépre telepített [helyszíni adatátjáró](../logic-apps/logic-apps-gateway-install.md) és az Azure [Portalon létrehozott Azure-adatátjáró-erőforrás a](../logic-apps/logic-apps-gateway-connection.md) következő esetekben:

  * A logikai alkalmazások nem futnak [integrációs szolgáltatási környezetben (ISE).](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)

  * A logikai alkalmazások integrációs szolgáltatási környezetben *futnak,* de az SQL Server-kapcsolathoz Windows-hitelesítést kell használnia. Ebben az esetben használja az SQL Server-összekötő nem ISE-verzióját az adatátjáróval együtt, mert az ISE-verzió nem támogatja a Windows-hitelesítést.

* A logikai alkalmazás, ahol az SQL-adatbázishoz való hozzáférésre van szüksége. A logikai alkalmazás SQL-eseményindítóval való elindításához [egy üres logikai alkalmazásra](../logic-apps/quickstart-create-first-logic-app-workflow.md)van szükség.

<a name="add-sql-trigger"></a>

## <a name="add-a-sql-trigger"></a>SQL-eseményindító hozzáadása

Az Azure Logic Apps minden logikai alkalmazás kell kezdeni egy [eseményindító,](../logic-apps/logic-apps-overview.md#logic-app-concepts)amely egy adott esemény bekövetkezésekor, vagy ha egy adott feltétel teljesül. Minden alkalommal, amikor az eseményindító aktiválódik, a Logic Apps motor létrehoz egy logikai alkalmazáspéldányt, és megkezdi a logikai alkalmazás munkafolyamatának futtatását.

1. Az Azure Portalon vagy a Visual Studio,hozzon létre egy üres logikai alkalmazás, amely megnyitja a Logic Apps Designer. Ez a példa az Azure Portalt használja.

1. A tervezőkeresőmezőjében írja be szűrőként az "sql server" kifejezést. Az eseményindítók listájában válassza ki a kívánt SQL-eseményindítót.

   Ez a példa a **Cikk létrehozásakor eseményindítót** használja.

   ![Válassza az "Elem létrehozásakor" eseményindító lehetőséget.](./media/connectors-create-api-sqlazure/select-sql-server-trigger.png)

1. Ha a rendszer kéri, hogy hozzon létre egy kapcsolatot, [hozza létre most az SQL-kapcsolatot.](#create-connection) Ha a kapcsolat létezik, válasszon **egy táblanevet.**

   ![A kívánt tábla kijelölése](./media/connectors-create-api-sqlazure/azure-sql-database-table.png)

1. Állítsa be az **Intervallum** és **gyakoriság** tulajdonságokat, amelyek meghatározzák, hogy a logikai alkalmazás milyen gyakran ellenőrizze a táblát.

   Ez az eseményindító csak egy sort ad vissza a kijelölt táblából, semmi mást. Más feladatok végrehajtásához adjon hozzá egyéb műveleteket, amelyek a kívánt feladatokat hajtják végre. Ha például meg szeretné tekinteni a sor adatait, hozzáadhat más műveleteket is, amelyek olyan fájlt hoznak létre, amely tartalmazza a visszaadott sor mezőit, majd e-mailes értesítéseket küldhet. Az összekötő höz elérhető egyéb műveletekről az [összekötő referencialapján](https://docs.microsoft.com/connectors/sql/)olvashat.

1. Ha elkészült, a tervező eszköztárán válassza a **Mentés gombot.**

   Ez a lépés automatikusan engedélyezi és közzéteszi a logikai alkalmazást élőben az Azure-ban.

<a name="add-sql-action"></a>

## <a name="add-a-sql-action"></a>SQL-művelet hozzáadása

Az Azure Logic Apps egy [művelet](../logic-apps/logic-apps-overview.md#logic-app-concepts) egy lépés a munkafolyamatban, amely követi az eseményindító vagy egy másik művelet. Ebben a példában a logikai alkalmazás az [Ismétlődés eseményindítóval](../connectors/connectors-native-recurrence.md)kezdődik, és meghív egy műveletet, amely egy sql-adatbázisból kap egy sort.

1. Az Azure Portalon vagy a Visual Studio, nyissa meg a logikai alkalmazást a Logic Apps Designer. Ez a példa az Azure Portalt használja.

1. Az eseményindító vagy művelet alatt, ahol hozzá szeretné adni az SQL-műveletet, válassza az **Új lépés**lehetőséget.

   ![Új lépés hozzáadása a logikai alkalmazáshoz](./media/connectors-create-api-sqlazure/select-new-step-logic-app.png)

   Ha műveletet szeretne hozzáadni a meglévő lépések közé, vigye az egeret a csatlakozó nyíl fölé. Jelölje ki a**+** megjelenő pluszjelet ( ), majd kattintson **a Művelet hozzáadása gombra.**

1. A **Művelet kiválasztása csoportkereső**mezőjében írja be szűrőként az "sql server" kifejezést. A műveletek listájában válassza ki a kívánt SQL-műveletet.

   Ez a példa a **Sor legete** műveletet használja, amely egyetlen rekordot kap.

   ![Sql "Get row" művelet ének keresése és kijelölése](./media/connectors-create-api-sqlazure/find-select-sql-get-row-action.png)

   Ez a művelet csak egy sort ad vissza a kijelölt táblából, semmi mást. Az ebben a sorban lévő adatok megtekintéséhez hozzáadhat más műveleteket, amelyek olyan fájlt hoznak létre, amely tartalmazza a visszaadott sor mezőit, és a fájlt egy felhőalapú tárfiókban tárolja. Az összekötő höz elérhető egyéb műveletekről az [összekötő referencialapján](https://docs.microsoft.com/connectors/sql/)olvashat.

1. Ha a rendszer kéri, hogy hozzon létre egy kapcsolatot, [hozza létre most az SQL-kapcsolatot.](#create-connection) Ha a kapcsolat létezik, jelöljön ki egy **táblanevet**, és adja meg a kívánt rekord **sorazonosítóját.**

   ![Adja meg a tábla nevét és a sorazonosítót](./media/connectors-create-api-sqlazure/specify-table-row-id-property-value.png)

1. Ha elkészült, a tervező eszköztárán válassza a **Mentés gombot.**

   Ez a lépés automatikusan engedélyezi és közzéteszi a logikai alkalmazást élőben az Azure-ban.

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Csatlakozás az adatbázishoz

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to SQL Server or Azure SQL Database](../../includes/connectors-create-api-sqlazure.md)]

## <a name="handle-bulk-data"></a>Tömeges adatok kezelése

Néha olyan nagy eredményhalmazokkal kell dolgoznia, hogy az összekötő nem adja vissza az összes eredményt egyszerre, vagy jobban szeretné szabályozni az eredményhalmazok méretét és szerkezetét. Íme néhány módszer, amelyekkel ilyen nagy eredményhalmazokat kezelhet:

* Ha kisebb halmazként szeretné kezelni az eredményeket, kapcsolja be a *tördelést.* További információt a [Tömeges adatok, rekordok és elemek tördeléssel című](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md)témakörben talál.

* Hozzon létre egy tárolt eljárást, amely a kívánt módon rendezi az eredményeket.

  Több sor beszerzésekor vagy beszúrásakor a logikai alkalmazás végighaladhat ezeken a sorokon egy [*until ciklus*](../logic-apps/logic-apps-control-flow-loops.md#until-loop) használatával ezeken a [korlátokon](../logic-apps/logic-apps-limits-and-config.md)belül. Ha azonban a logikai alkalmazásnak olyan nagy rekordkészletekkel kell dolgoznia, például több ezer vagy több millió sor, hogy minimalizálni szeretné az adatbázis-hívásokból eredő költségeket.

  Ha az eredményeket a kívánt módon szeretné rendszerezni, létrehozhat egy [*tárolt eljárást,*](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) amely az SQL-példányban fut, és a **SELECT - ORDER BY** utasítást használja. Ez a megoldás nagyobb ellenőrzést biztosít az eredmények mérete és szerkezete felett. A logikai alkalmazás meghívja a tárolt eljárást az SQL Server-összekötő **Tárolt eljárás végrehajtása** művelet használatával.

  További részletek értendők a következő cikkekben:

  * [SQL-töredezés tömeges adatátvitelhez a Logic Apps alkalmazásokkal](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx)

  * [SELECT - ORDER BY záradék](https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql)

## <a name="connector-specific-details"></a>Összekötő-specifikus részletek

Az összekötő eseményindítóival, műveleteivel és korlátaival kapcsolatos technikai információkért tekintse meg az [összekötő referencialapját.](https://docs.microsoft.com/connectors/sql/)

## <a name="next-steps"></a>További lépések

* További információ az Azure Logic Apps egyéb [összekötőiről](../connectors/apis-list.md)
