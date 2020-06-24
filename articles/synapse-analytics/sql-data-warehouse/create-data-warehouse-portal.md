---
title: Szinapszis SQL-készlet létrehozása és lekérdezése (Azure Portal)
description: Szinapszis SQL-készlet létrehozása és lekérdezése a Azure Portal használatával
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 05/28/2019
ms.author: Kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: c7efccf35ddb47bf349d9a21628e9445e37b7699
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85211306"
---
# <a name="quickstart-create-and-query-a-synapse-sql-pool-using-the-azure-portal"></a>Rövid útmutató: szinapszis SQL-készlet létrehozása és lekérdezése a Azure Portal használatával

Gyorsan létrehozhat és lekérdezheti a szinapszis SQL-készletet (adattárházat) az Azure szinapszis Analyticsben (korábban SQL DW) a Azure Portal használatával.

## <a name="prerequisites"></a>Előfeltételek

1. Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

   > [!NOTE]
   > Ha SQL-készletet hoz létre az Azure Szinapszisban, egy új számlázható szolgáltatást eredményezhet. További információ: az [Azure szinapszis Analytics díjszabása](https://azure.microsoft.com/pricing/details/synapse-analytics/).

2. Töltse le és telepítse az [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS) legújabb verzióját.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="create-a-sql-pool"></a>SQL-készlet létrehozása

Az adattárházak létrehozása az SQL-készlet használatával történik az Azure szinapszis Analyticsben. A rendszer létrehoz egy SQL-készletet a [számítási erőforrások](memory-concurrency-limits.md)meghatározott készletével. Az adatbázis egy [Azure-erőforráscsoport](../../azure-resource-manager/management/overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) és egy [logikai SQL Server-kiszolgáló](../../azure-sql/database/logical-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)között jön létre.

Az alábbi lépéseket követve létrehozhat egy SQL-készletet, amely tartalmazza a **AdventureWorksDW** .

1. Válassza az **erőforrás létrehozása** lehetőséget a Azure Portal bal felső sarkában.

   ![erőforrás létrehozása Azure Portal](./media/create-data-warehouse-portal/create-a-resource.png)

2. Az **új** oldalon válassza az **adatbázisok** lehetőséget, majd válassza ki az **Azure szinapszis Analytics (korábbi nevén SQL DW)** elemet a **Kiemelt** listában.

   ![üres adattárház létrehozása](./media/create-data-warehouse-portal/create-a-data-warehouse.png)

3. Az **alapismeretek**területen adja meg az előfizetését, az erőforráscsoportot, az SQL-készlet nevét és a kiszolgáló nevét:

   | Beállítás | Ajánlott érték | Leírás |
   | :------ | :-------------- | :---------- |
   | **Előfizetés** | Az Ön előfizetése | Az előfizetései részleteivel kapcsolatban lásd az [előfizetéseket](https://account.windowsazure.com/Subscriptions) ismertető cikket. |
   | **Erőforráscsoport** | myResourceGroup | Az érvényes erőforráscsoport-nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) ismertető cikket. |
   | **SQL-készlet neve** | Bármely globálisan egyedi név (például *mySampleDataWarehouse*) | Az érvényes adatbázisnevekkel kapcsolatban lásd az [adatbázis-azonosítókat](/sql/relational-databases/databases/database-identifiers?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) ismertető cikket. Vegye figyelembe, hogy egy SQL-készlet egy adatbázis-típus. |
   | **Kiszolgáló** | Bármely globálisan egyedi név | Válasszon ki egy meglévő kiszolgálót, vagy hozzon létre egy új kiszolgálónevet, és válassza az **új létrehozása**lehetőséget. Az érvényes kiszolgálónevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) ismertető cikket. |

   ![adatraktár alapszintű adatainak létrehozása](./media/create-data-warehouse-portal/create-sql-pool-basics.png)

4. A **teljesítmény szintje**területen válassza a **teljesítmény kiválasztása** lehetőséget, ha szeretné, hogy a konfigurációt egy csúszka használatával módosítsa.

   ![az adatraktár teljesítményi szintjének módosítása](./media/create-data-warehouse-portal/create-sql-pool-performance-level.png)  

   A teljesítményszint részletes ismertetését lásd: [a számítások kezelése az Azure szinapszis Analyticsben](sql-data-warehouse-manage-compute-overview.md).

5. Válassza a **További beállítások**lehetőséget, majd a **meglévő adat használata**területen válassza a **minta** lehetőséget, hogy a rendszer létrehozza a AdventureWorksDW.

    ![meglévő adathasználat kiválasztása](./media/create-data-warehouse-portal/create-sql-pool-additional-1.png) 

6. Most, hogy végrehajtotta az Azure szinapszis Analytics űrlapjának alapismeretek lapját, válassza a **felülvizsgálat + létrehozás** , majd a **Létrehozás** lehetőséget az SQL-készlet létrehozásához. Az üzembe helyezés eltarthat néhány percig.

   ![Válassza a felülvizsgálat + létrehozás lehetőséget](./media/create-data-warehouse-portal/create-sql-pool-review-create.png)

   ![létrehozás kiválasztása](./media/create-data-warehouse-portal/create-sql-pool-create.png)

7. Az eszköztáron válassza az **értesítések** lehetőséget a telepítési folyamat figyeléséhez.

   ![értesítés](./media/create-data-warehouse-portal/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Kiszolgálószintű tűzfalszabály létrehozása

Az Azure szinapszis szolgáltatás egy tűzfalat hoz létre a kiszolgáló szintjén. Ez a tűzfal megakadályozza, hogy a külső alkalmazások és eszközök csatlakozzanak a kiszolgálóhoz vagy a kiszolgálón lévő adatbázisokhoz. A csatlakozás engedélyezéséhez hozzáadhat tűzfalszabályokat, amelyek adott IP-címekkel engedélyezik a kapcsolódást. A következő lépéseket követve hozzon létre egy [kiszolgálószintű tűzfalszabályt](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) az ügyfél IP-címéhez.

> [!NOTE]
> Az Azure szinapszis a 1433-as porton keresztül kommunikál. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat az 1433-as porton keresztül. Ha igen, nem tud csatlakozni a kiszolgálóhoz, kivéve, ha az informatikai részleg megnyitja a 1433-es portot.

1. Az üzembe helyezés befejezése után válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben. Válassza az **adatbázisok**lehetőséget, válassza ki az **Azure szinapszis Analytics** melletti csillagot az Azure szinapszis Analytics kedvencekhez való hozzáadásához.

2. Válassza ki az **Azure szinapszis Analytics** elemet a bal oldali menüben, majd válassza a **MySampleDataWarehouse** lehetőséget az **Azure szinapszis Analytics** oldalán. Megnyílik az adatbázis áttekintő lapja, amely megjeleníti a teljes kiszolgálónevet (például **sqlpoolservername.database.Windows.net**), és további konfigurálási lehetőségeket biztosít.

3. Másolja ezt a teljes kiszolgálónevet a kiszolgálóhoz és az adatbázisaihoz való kapcsolódáshoz, és az egyéb gyors indításokat. A kiszolgáló beállításainak megnyitásához válassza ki a kiszolgáló nevét.

   ![kiszolgálónév keresése](./media/create-data-warehouse-portal/find-server-name.png)

4. Válassza a **tűzfalbeállítások megjelenítése**lehetőséget.

   ![kiszolgáló beállításai](./media/create-data-warehouse-portal/server-settings.png)

5. Ekkor megnyílik a kiszolgáló **tűzfalbeállítások** lapja.

   ![kiszolgálói tűzfalszabály](./media/create-data-warehouse-portal/server-firewall-rule.png)

6. Az aktuális IP-cím új tűzfalszabály hozzáadásához válassza az **ügyfél IP-** címének hozzáadása lehetőséget az eszköztáron. A tűzfalszabály az 1433-as portot egy egyedi IP-cím vagy egy IP-címtartomány számára nyithatja meg.

7. Válassza a **Mentés**lehetőséget. A rendszer létrehoz egy kiszolgálói szintű tűzfalszabály-szabályt az aktuális IP-címhez, amely megnyitja az 1433-es portot a kiszolgálón.

8. kattintson **az OK gombra** , majd a **tűzfalbeállítások** oldal bezárásához.

Most már csatlakozhat a kiszolgálóhoz és az SQL-készletekhez ezzel az IP-címmel. A csatlakozás az SQL Server Management Studio vagy más, választott eszköz használatával lehetséges. A csatlakozáskor használja a korábban létrehozott ServerAdmin-fiókot.

> [!IMPORTANT]
> Alapértelmezés szerint az összes Azure-szolgáltatás számára engedélyezett a hozzáférés az SQL Database tűzfalán keresztül. válassza **ki** ezt a lapot, majd a **Mentés** gombra kattintva tiltsa le a tűzfalat az összes Azure-szolgáltatáshoz.

## <a name="get-the-fully-qualified-server-name"></a>A teljes kiszolgálónév lekérése

Szerezze be a kiszolgáló teljes nevét a Azure Portalban. Később ezt a teljes nevet fogja majd használni a kiszolgálóhoz való kapcsolódás során.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Válassza ki az **Azure szinapszis Analytics** elemet a bal oldali menüben, és válassza ki az **Azure szinapszis Analytics** -oldalát.

3. Az Azure Portalon az adatbázishoz tartozó lap **Alapvető erőforrások** ablaktábláján keresse meg, majd másolja ki a **Kiszolgáló nevét**. Ebben a példában a teljes név sqlpoolservername.database.windows.net.

    ![kapcsolatadatok](./media/create-data-warehouse-portal/find-server-name-copy.png)

## <a name="connect-to-the-server-as-server-admin"></a>Csatlakozás a kiszolgálóhoz kiszolgáló-rendszergazdaként

Ez a szakasz [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS) használatával létesít kapcsolatot a kiszolgálóval.

1. Nyissa meg az SQL Server Management Studiót.

2. A **Connect to Server** (Kapcsolódás a kiszolgálóhoz) párbeszédpanelen adja meg a következő adatokat:

   | Beállítás | Ajánlott érték | Leírás |
   | :------ | :-------------- | :---------- |
   | Kiszolgáló típusa | Adatbázismotor | Kötelezően megadandó érték |
   | Kiszolgáló neve | A teljes kiszolgálónév | Íme egy példa: **sqlpoolservername.database.Windows.net**. |
   | Hitelesítés | SQL Server-hitelesítés | Ebben az oktatóanyagban az SQL-hitelesítésen kívül más hitelesítéstípus nincs konfigurálva. |
   | Bejelentkezés | A kiszolgálói rendszergazdafiók | A kiszolgáló létrehozásakor megadott fiók. |
   | Jelszó | A kiszolgálói rendszergazdai fiók jelszava | A kiszolgáló létrehozásakor megadott jelszó. |
   ||||

   ![kapcsolódás a kiszolgálóhoz](./media/create-data-warehouse-portal/connect-to-server-ssms.png)

3. Válassza a **kapcsolat**lehetőséget. Megnyílik az Object Explorer ablak az SSMS-ben.

4. Az Object Explorerben bontsa ki a **Databases** (Adatbázisok) elemet. Ezután bontsa ki a **mySampleDatabase** csomópontot az új adatbázisban található objektumok megtekintéséhez.

   ![adatbázis-objektumok](./media/create-data-warehouse-portal/connected-ssms.png)

## <a name="run-some-queries"></a>Néhány lekérdezés futtatása

Nem ajánlott nagyméretű lekérdezéseket futtatni a kiszolgálói rendszergazdaként való naplózás során, mivel ez egy [korlátozott erőforrás-osztályt](resource-classes-for-workload-management.md)használ. Ehelyett konfigurálja a [munkaterhelés elkülönítését](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-configure-workload-isolation-tsql) [az oktatóanyagokban bemutatott](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/load-data-wideworldimportersdw#create-a-user-for-loading-data)módon.

Az SQL Data Warehouse a T-SQL nyelvet használja lekérdezési nyelvként. A lekérdezésablakok megnyitásához és a T-SQL-lekérdezések futtatásához végezze el az alábbi lépéseket:

1. Kattintson a jobb gombbal a **mySampleDataWarehouse** elemre, és válassza az **Új lekérdezés**elemet. Megnyílik egy új lekérdezési ablak.

2. A lekérdezésablakban írja be a következő parancsot az adatbázisok listájának megjelenítéséhez.

    ```sql
    SELECT * FROM sys.databases
    ```

3. Válassza a **végrehajtás**lehetőséget. A lekérdezés eredménye két adatbázist mutat: a **master** és a **mySampleDataWarehouse** adatbázist.

   ![Adatbázisok lekérdezése](./media/create-data-warehouse-portal/query-databases.png)

4. Hogy lássunk néhány adatot is, a következő parancs használatával tekintsük meg, hány Adams vezetéknevű, három gyermeket nevelő ügyfél létezik. A lekérdezés hat ügyfelet listáz ki.

    ```sql
    SELECT LastName, FirstName FROM dbo.dimCustomer
    WHERE LastName = 'Adams' AND NumberChildrenAtHome = 3;
    ```

   ![A dbo.dimCustomer lekérdezés](./media/create-data-warehouse-portal/query-customer.png)

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Az adatraktár-egységek és az SQL-készletben tárolt adatmennyiség után kell fizetnie. Ezek a számítási és tárolási erőforrások elkülönítve lesznek kiszámlázva.

- Ha meg szeretné őrizni az adatok tárolását, szüneteltetheti a számítást, ha nem használja az SQL-készletet. A számítás felfüggesztésével csak az adattárolás díját számítjuk fel. Bármikor folytathatja a számítást, amikor készen áll az adatokkal való együttműködésre.

- Ha el szeretné távolítani a jövőbeli díjakat, törölheti az SQL-készletet.

Kövesse az alábbi lépéseket a már nem szükséges erőforrások törléséhez.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és válassza ki az SQL-készletet.

   ![Erőforrások felszabadítása](./media/create-data-warehouse-portal/clean-up-resources.png)

2. A számítás szüneteltetéséhez kattintson a **szüneteltetés** gombra. Ha az SQL-készlet fel van függesztve, a **Folytatás** gomb jelenik meg. A számítás folytatásához válassza a **Folytatás**lehetőséget.

3. Ha el szeretné távolítani az SQL-készletet, hogy a számítás vagy a tárolás ne legyen felszámítva, válassza a **Törlés**lehetőséget.

4. A létrehozott kiszolgáló eltávolításához válassza a **sqlpoolservername.database.Windows.net** lehetőséget az előző képen, majd válassza a **Törlés**lehetőséget. A törléssel bánjon óvatosan, mivel a kiszolgálóval együtt a hozzá rendelt összes adatbázis is törölve lesz.

5. Az erőforráscsoport eltávolításához válassza a **myResourceGroup**lehetőséget, majd válassza az **erőforráscsoport törlése**lehetőséget.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az SQL-készletbe való betöltésről, folytassa az [adatgyűjtés az SQL-készletbe](load-data-from-azure-blob-storage-using-polybase.md) című cikket.
