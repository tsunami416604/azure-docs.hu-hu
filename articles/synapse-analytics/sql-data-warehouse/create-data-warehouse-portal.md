---
title: Szinapszis SQL-készlet létrehozása és lekérdezése (Azure Portal)
description: Synapse SQL-készlet létrehozása és lekérdezése az Azure Portal használatával
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 05/28/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 685d6970b0e88032fe503bf97a139c0b8c0f8a73
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631351"
---
# <a name="quickstart-create-and-query-a-synapse-sql-pool-using-the-azure-portal"></a>Rövid útmutató: Szinapszid SQL-készlet létrehozása és lekérdezése az Azure Portal használatával

Gyorsan létrehozhat és lekérdezheti a Synapse SQL-készletet (adatraktárt) az Azure Synapse Analytics (korábbi sql DW) rendszerben az Azure Portalon.

## <a name="prerequisites"></a>Előfeltételek

1. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes](https://azure.microsoft.com/free/) fiókot, mielőtt elkezdené.

   > [!NOTE]
   > Sql-készlet létrehozása az Azure Synapse eredményezhet egy új számlázható szolgáltatás. További információ: [Azure Synapse Analytics pricing](https://azure.microsoft.com/pricing/details/synapse-analytics/).

2. Töltse le és telepítse az [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS) legújabb verzióját.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

## <a name="create-a-sql-pool"></a>SQL-készlet létrehozása

Az adatraktárak az Azure Synapse Analytics SQL-készletével jönnek létre. Az SQL-készlet a [számítási erőforrások](memory-concurrency-limits.md)meghatározott készletével jön létre. Az adatbázis egy [Azure-erőforráscsoporton](../../azure-resource-manager/management/overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) belül egy [Azure SQL logikai kiszolgálón](../../sql-database/sql-database-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) jön létre.

Az alábbi lépésekkel hozzon létre egy SQL-készletet, amely tartalmazza az **AdventureWorksDW** mintaadatait.

1. Válassza az **Erőforrás létrehozása** lehetőséget az Azure Portal bal felső sarkában.

   ![erőforrás létrehozása az Azure Portalon](./media/create-data-warehouse-portal/create-a-resource.png)

2. Válassza **ki** az adatbázisok az **új** lapon, és válassza az **Azure Synapse Analytics (korábban SQL DW)** a **Kiemelt** listában.

   ![üres adattárház létrehozása](./media/create-data-warehouse-portal/create-a-data-warehouse.png)

3. Az **Alapok szolgáltatás**ban adja meg az előfizetést, az erőforráscsoportot, az SQL-készlet nevét és a kiszolgáló nevét:

   | Beállítás | Ajánlott érték | Leírás |
   | :------ | :-------------- | :---------- |
   | **Előfizetés** | Az Ön előfizetése | Az előfizetései részleteivel kapcsolatban lásd az [előfizetéseket](https://account.windowsazure.com/Subscriptions) ismertető cikket. |
   | **Erőforráscsoport** | myResourceGroup | Az érvényes erőforráscsoport-nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) ismertető cikket. |
   | **SQL-készlet neve** | Bármely globálisan egyedi név (erre példa a *mySampleDataWarehouse*) | Az érvényes adatbázisnevekkel kapcsolatban lásd az [adatbázis-azonosítókat](/sql/relational-databases/databases/database-identifiers?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) ismertető cikket. Ne feledje, hogy az SQL-készlet az egyik típusú adatbázis. |
   | **Kiszolgáló** | Bármely globálisan egyedi név | Jelölje ki a meglévő kiszolgálót, vagy hozzon létre egy új kiszolgálónevet, és válassza **az Új létrehozása lehetőséget.** Az érvényes kiszolgálónevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) ismertető cikket. |

   ![adattárház alapvető részleteinek létrehozása](./media/create-data-warehouse-portal/create-sql-pool-basics.png)

4. A **Teljesítmény szint**csoportban válassza a **Teljesítményszint kiválasztása lehetőséget** a beállítás csúszkával való beállításhoz.

   ![adattárház teljesítményszintjének módosítása](./media/create-data-warehouse-portal/create-sql-pool-performance-level.png)  

   A teljesítményszintekről a [Számítási feladatok kezelése az Azure SQL Data Warehouseban című témakörben](sql-data-warehouse-manage-compute-overview.md)talál további információt.

5. Most, hogy befejezte az Azure Synapse Analytics űrlap Alapjai lapját, válassza a **Véleményezés + Létrehozás** lehetőséget, majd a **Létrehozás** lehetőséget az SQL-készlet létrehozásához. Az üzembe helyezés eltarthat néhány percig.

   ![válassza a Véleményezés + Létrehozás lehetőséget](./media/create-data-warehouse-portal/create-sql-pool-review-create.png)

   ![létrehozás kiválasztása](./media/create-data-warehouse-portal/create-sql-pool-create.png)

6. Az eszköztáron válassza az **Értesítések** lehetőséget a telepítési folyamat figyeléséhez.

   ![értesítés](./media/create-data-warehouse-portal/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Kiszolgálószintű tűzfalszabály létrehozása

Az Azure Synapse szolgáltatás tűzfalat hoz létre a kiszolgáló szintjén. Ez a tűzfal megakadályozza, hogy külső alkalmazások és eszközök csatlakozzanak a kiszolgálóhoz vagy a kiszolgáló bármely adatbázisához. A csatlakozás engedélyezéséhez hozzáadhat tűzfalszabályokat, amelyek adott IP-címekkel engedélyezik a kapcsolódást. A következő lépéseket követve hozzon létre egy [kiszolgálószintű tűzfalszabályt](../../sql-database/sql-database-firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) az ügyfél IP-címéhez.

> [!NOTE]
> Az Azure Synapse az 1433-as porton keresztül kommunikál. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat az 1433-as porton keresztül. Ebben az esetben nem tud csatlakozni az Azure SQL-adatbáziskiszolgálóhoz, ha az informatikai részleg nem nyitja meg az 1433-as portot.

1. A telepítés befejezése után válassza a bal oldali menü **Minden szolgáltatás** lehetőséget. Válassza **az Adatbázisok**lehetőséget, és válassza ki az Azure **Synapse Analytics** melletti csillagot, és adja hozzá az Azure Synapse Analytics-et a kedvencekhez.

2. Válassza az **Azure Synapse Analytics** a bal oldali menüben, majd válassza **a mySampleDataWarehouse** az **Azure Synapse Analytics** lapon. Megnyílik az adatbázis áttekintő lapja, amely a teljesen minősített kiszolgáló nevét (például **sqlpoolservername.database.windows.net**) jeleníti meg, és további konfigurációs lehetőségeket biztosít.

3. Másolja a teljesen minősített kiszolgálónevet a kiszolgálóhoz és annak adatbázisaihoz való csatlakozáshoz ebben és más gyorsindításokban. A kiszolgálóbeállítások megnyitásához jelölje ki a kiszolgáló nevét.

   ![kiszolgálónév keresése](./media/create-data-warehouse-portal/find-server-name.png)

4. Válassza **a Tűzfalbeállítások megjelenítése**lehetőséget.

   ![kiszolgáló beállításai](./media/create-data-warehouse-portal/server-settings.png)

5. Megnyílik az SQL-adatbáziskiszolgálóhoz tartozó **Tűzfalbeállítások** oldal.

   ![kiszolgálói tűzfalszabály](./media/create-data-warehouse-portal/server-firewall-rule.png)

6. Ha az aktuális IP-címet új tűzfalszabályhoz szeretné hozzáadni, válassza az **eszköztár Ügyfél IP hozzáadása parancsát.** A tűzfalszabály az 1433-as portot egy egyedi IP-cím vagy egy IP-címtartomány számára nyithatja meg.

7. válassza a **Mentés gombot.** A rendszer létrehoz egy kiszolgálószintű tűzfalszabályt az aktuális IP-címhez, és megnyitja az 1433-as portot a logikai kiszolgálón.

8. válassza **az OK gombot,** majd zárja be a **Tűzfal beállításai** lapot.

Ezzel az IP-címmel most már csatlakozhat az SQL-kiszolgálóhoz és annak SQL-készleteihez. A csatlakozás az SQL Server Management Studio vagy más, választott eszköz használatával lehetséges. A csatlakozáskor használja a korábban létrehozott ServerAdmin-fiókot.

> [!IMPORTANT]
> Alapértelmezés szerint az összes Azure-szolgáltatás számára engedélyezett a hozzáférés az SQL Database tűzfalán keresztül. válassza **a KI** lehetőséget ezen a lapon, majd a **Mentés** lehetőséget az összes Azure-szolgáltatás tűzfalának letiltásához.

## <a name="get-the-fully-qualified-server-name"></a>A teljes kiszolgálónév lekérése

Kérje le az SQL-kiszolgáló teljes kiszolgálónevét az Azure Portalon. Később ezt a teljes nevet fogja majd használni a kiszolgálóhoz való kapcsolódás során.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

2. Válassza ki az **Azure Synapse Analytics** a bal oldali menüben, és válassza ki az **Azure Synapse Analytics** oldalon.

3. Az Azure Portalon az adatbázishoz tartozó lap **Alapvető erőforrások** ablaktábláján keresse meg, majd másolja ki a **Kiszolgáló nevét**. Ebben a példában a teljesen minősített név sqlpoolservername.database.windows.net.

    ![kapcsolatadatok](./media/create-data-warehouse-portal/find-server-name-copy.png)

## <a name="connect-to-the-server-as-server-admin"></a>Csatlakozás a kiszolgálóhoz kiszolgáló-rendszergazdaként

Ebben a részben az [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) használatával építjük fel a kapcsolatot az Azure SQL-kiszolgálóval.

1. Nyissa meg az SQL Server Management Studiót.

2. A **Connect to Server** (Kapcsolódás a kiszolgálóhoz) párbeszédpanelen adja meg a következő adatokat:

   | Beállítás | Ajánlott érték | Leírás |
   | :------ | :-------------- | :---------- |
   | Kiszolgáló típusa | Adatbázismotor | Kötelezően megadandó érték |
   | Kiszolgálónév | A teljes kiszolgálónév | Íme egy példa: **sqlpoolservername.database.windows.net**. |
   | Hitelesítés | SQL Server-hitelesítés | Ebben az oktatóanyagban az SQL-hitelesítésen kívül más hitelesítéstípus nincs konfigurálva. |
   | Bejelentkezés | A kiszolgálói rendszergazdafiók | A kiszolgáló létrehozásakor megadott fiók. |
   | Jelszó | A kiszolgálói rendszergazdai fiók jelszava | A kiszolgáló létrehozásakor megadott jelszó. |
   ||||

   ![kapcsolódás a kiszolgálóhoz](./media/create-data-warehouse-portal/connect-to-server-ssms.png)

3. válassza a **Csatlakozás**lehetőséget. Megnyílik az Object Explorer ablak az SSMS-ben.

4. Az Object Explorerben bontsa ki a **Databases** (Adatbázisok) elemet. Ezután bontsa ki a **mySampleDatabase** csomópontot az új adatbázisban található objektumok megtekintéséhez.

   ![adatbázis-objektumok](./media/create-data-warehouse-portal/connected-ssms.png)

## <a name="run-some-queries"></a>Néhány lekérdezés futtatása

Az SQL Data Warehouse a T-SQL nyelvet használja lekérdezési nyelvként. A lekérdezésablakok megnyitásához és a T-SQL-lekérdezések futtatásához végezze el az alábbi lépéseket:

1. Válassza ki a jobb oldali parancsot **a mySampleDataWarehouse elemre,** majd az **Új lekérdezés parancsra.** Megnyílik egy új lekérdezési ablak.

2. A lekérdezésablakban írja be a következő parancsot az adatbázisok listájának megjelenítéséhez.

    ```sql
    SELECT * FROM sys.databases
    ```

3. válassza **a Végrehajtás lehetőséget.** A lekérdezés eredménye két adatbázist mutat: a **master** és a **mySampleDataWarehouse** adatbázist.

   ![Adatbázisok lekérdezése](./media/create-data-warehouse-portal/query-databases.png)

4. Hogy lássunk néhány adatot is, a következő parancs használatával tekintsük meg, hány Adams vezetéknevű, három gyermeket nevelő ügyfél létezik. A lekérdezés hat ügyfelet listáz ki.

    ```sql
    SELECT LastName, FirstName FROM dbo.dimCustomer
    WHERE LastName = 'Adams' AND NumberChildrenAtHome = 3;
    ```

   ![A dbo.dimCustomer lekérdezés](./media/create-data-warehouse-portal/query-customer.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az adattárház-egységekért és az SQL-készletben tárolt adatokért díjat számítunk fel. Ezek a számítási és tárolási erőforrások elkülönítve lesznek kiszámlázva.

- Ha meg szeretné tartani az adatokat a tárolóban, szüneteltetheti a számítást, ha nem használja az SQL-készletet. A számítás szüneteltetésével csak az adattárolásért kell fizetnie. Bármikor folytathatja a számítást, amikor készen áll az adatokkal való munkára.

- Ha el szeretné távolítani a jövőbeli díjakat, törölheti az SQL-készletet.

Az alábbi lépésekkel megtisztíthatja a már nem szükséges erőforrásokat.

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com)válassza ki az SQL-készletet.

   ![Az erőforrások eltávolítása](./media/create-data-warehouse-portal/clean-up-resources.png)

2. A számítás szüneteltetéséhez kattintson a **Szünet** gombra. Az SQL-készlet szüneteltetésekor megjelenik a **Folytatás** gomb. A számítás folytatásához válassza a **Folytatás lehetőséget.**

3. Ha el szeretné távolítani az SQL-készletet, hogy ne kelljen fizetnie a számításért vagy a tárolásért, válassza a **Törlés lehetőséget.**

4. A létrehozott SQL-kiszolgáló eltávolításához jelölje **ki sqlpoolservername.database.windows.net** az előző képen, majd kattintson a **Törlés gombra.** A törléssel bánjon óvatosan, mivel a kiszolgálóval együtt a hozzá rendelt összes adatbázis is törölve lesz.

5. Az erőforráscsoport eltávolításához válassza a **MyResourceGroup**lehetőséget, majd az **Erőforráscsoport törlése**lehetőséget.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az adatok sql-készletbe való betöltéséről, folytassa az [Adatok betöltése az SQL-készletbe](load-data-from-azure-blob-storage-using-polybase.md) cikkhez.
