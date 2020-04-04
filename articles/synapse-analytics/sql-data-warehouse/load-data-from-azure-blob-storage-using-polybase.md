---
title: 'Oktatóanyag: A New York-i taxiadatok betöltése'
description: Az oktatóanyag az Azure Portal és az SQL Server Management Studio segítségével tölti be a New York-i Taxicab-adatokat egy globális Azure-blobból a Synapse SQL számára.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 741779e8328c38e544b1ad297e59155dab4e8c0d
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633909"
---
# <a name="tutorial-load-the-new-york-taxicab-dataset"></a>Oktatóanyag: Töltse be a New York-i taxiadatkészletet

Ez az oktatóanyag a PolyBase használatával tölti be a New York-i Taxitaxi-adatokat egy globális Azure blobtár-fiókból. Az oktatóanyag az [Azure Portalt](https://portal.azure.com) és az [SQL Server Management Studiót](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS) használja a következőkhöz:

> [!div class="checklist"]
>
> * SQL-készlet létrehozása az Azure Portalon
> * Kiszolgálószintű tűzfalszabály létrehozása az Azure Portalon
> * Csatlakozás az adattárházhoz az SSMS használatával
> * Adatok betöltésére kijelölt felhasználó létrehozása
> * Külső táblák létrehozása az Azure Blob Storage-ban található adatokhoz
> * Adatok betöltése az adattárházba a CTAS T-SQL-utasítás használatával
> * Az adatok állapotának megtekintése betöltés közben
> * Statisztikák készítése az újonnan betöltött adatokról

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="before-you-begin"></a>Előkészületek

Az oktatóanyag megkezdése előtt töltse le és telepítse az [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS) legújabb verzióját.

## <a name="log-in-to-the-azure-portal"></a>Bejelentkezés az Azure Portalra

Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

## <a name="create-a-blank-database"></a>Hozzon létre egy üres adatbázist

Az SQL-készlet a [számítási erőforrások](memory-concurrency-limits.md)meghatározott készletével jön létre. Az adatbázis egy [Azure-erőforráscsoporton](../../azure-resource-manager/management/overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) belül egy [Azure SQL logikai kiszolgálón](../../sql-database/sql-database-features.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) jön létre.

Üres adatbázis létrehozásához kövesse az alábbi lépéseket.

1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.

2. Válassza ki az **adatbázisok** az **új** lapon, és válassza az **Azure Synapse Analytics** a **Kiemelt** az **új** lapon.

    ![adattárház létrehozása](./media/load-data-from-azure-blob-storage-using-polybase/create-empty-data-warehouse.png)

3. Adja meg az alábbi adatokat az űrlapon:

   | Beállítás            | Ajánlott érték       | Leírás                                                  |
   | ------------------ | --------------------- | ------------------------------------------------------------ |
   | *név**            | mySampleDataWarehouse | Az érvényes adatbázisnevekkel kapcsolatban lásd az [adatbázis-azonosítókat](/sql/relational-databases/databases/database-identifiers?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) ismertető cikket. |
   | **Előfizetés**   | Az Ön előfizetése     | Az előfizetései részleteivel kapcsolatban lásd az [előfizetéseket](https://account.windowsazure.com/Subscriptions) ismertető cikket. |
   | **Erőforráscsoport** | myResourceGroup       | Az érvényes erőforráscsoport-nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) ismertető cikket. |
   | **Forrás kijelölése**  | Üres adatbázis        | Megköveteli egy üres adatbázis létrehozását. Megjegyzés: Az adattárház az adatbázisok egy típusa. |

    ![adattárház létrehozása](./media/load-data-from-azure-blob-storage-using-polybase/create-data-warehouse.png)

4. Válassza a **Kiszolgáló** lehetőséget új kiszolgáló létrehozásához és konfigurálásához az új adatbázis számára. Adja meg az alábbi adatokat az **Új kiszolgálóűrlapon**:

    | Beállítás                | Ajánlott érték          | Leírás                                                  |
    | ---------------------- | ------------------------ | ------------------------------------------------------------ |
    | **Kiszolgáló neve**        | Bármely globálisan egyedi név | Az érvényes kiszolgálónevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) ismertető cikket. |
    | **Kiszolgálórendszergazdai bejelentkezés** | Bármely érvényes név           | Az érvényes bejelentkezési nevekkel kapcsolatban lásd az [adatbázis-azonosítókat](/sql/relational-databases/databases/database-identifiers?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) ismertető cikket. |
    | **Jelszó**           | Bármely érvényes jelszó       | A jelszónak legalább nyolc karakter hosszúságúnak kell lennie, és tartalmaznia kell karaktereket a következő kategóriák közül legalább háromból: nagybetűs karakterek, kisbetűs karakterek, számjegyek és nem alfanumerikus karakterek. |
    | **Helyen**           | Bármely érvényes hely       | A régiókkal kapcsolatos információkért lásd [az Azure régióit](https://azure.microsoft.com/regions/) ismertető cikket. |

    ![adatbázis-kiszolgáló létrehozása](./media/load-data-from-azure-blob-storage-using-polybase/create-database-server.png)

5. Válassza a **Kiválasztás** lehetőséget.

6. Válassza a **Teljesítményszint lehetőséget,** ha meg szeretné adni, hogy az adattárház Gen1 vagy Gen2, és az adatraktáregységek számát.

7. Ehhez az oktatóanyaghoz válassza az SQL-készlet **Gen2**lehetőséget. A csúszka alapértelmezés szerint **DW1000c** értékre van állítva.  Csúsztassa fel és le, hogy kipróbálja a működését a gyakorlatban.

    ![teljesítmény konfigurálása](./media/load-data-from-azure-blob-storage-using-polybase/configure-performance.png)

8. Kattintson az **Alkalmaz** gombra.
9. A kiépítési panelen válassza ki az üres adatbázis **illesztését.** A jelen oktatóanyag esetében használja az alapértelmezett értéket. A rendezésekkel kapcsolatos további információkért lásd: [Rendezések](/sql/t-sql/statements/collations?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

10. Most, hogy kitöltötte az űrlapot, válassza a **Létrehozás** lehetőséget az adatbázis kiépítéséhez. Az üzembe helyezés eltarthat néhány percig.

11. Az eszköztáron válassza az **Értesítések** lehetőséget a telepítési folyamat figyeléséhez.
  
     ![értesítés](./media/load-data-from-azure-blob-storage-using-polybase/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Kiszolgálószintű tűzfalszabály létrehozása

Kiszolgálószintű tűzfal, amely megakadályozza, hogy külső alkalmazások és eszközök csatlakozzanak a kiszolgálóhoz vagy a kiszolgáló bármely adatbázisához. A csatlakozás engedélyezéséhez hozzáadhat tűzfalszabályokat, amelyek adott IP-címekkel engedélyezik a kapcsolódást.  A következő lépéseket követve hozzon létre egy [kiszolgálószintű tűzfalszabályt](../../sql-database/sql-database-firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) az ügyfél IP-címéhez.

> [!NOTE]
> Az SQL Data Warehouse az 1433-as portot használja a kommunikációhoz. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat az 1433-as porton keresztül. Ebben az esetben nem tud csatlakozni az Azure SQL-adatbáziskiszolgálóhoz, ha az informatikai részleg nem nyitja meg az 1433-as portot.

1. A telepítés befejezése után válassza az **SQL-adatbázisok lehetőséget** a bal oldali menüből, majd válassza a **mySampleDatabase** lehetőséget az **SQL-adatbázisok** lapon. Megnyílik az adatbázis áttekintő lapja, amely a teljesen minősített kiszolgálónevét (például **mynewserver-20180430.database.windows.net)** jeleníti meg, és további konfigurációs lehetőségeket biztosít.

2. Másolja le ezt a teljes kiszolgálónevet, mert a későbbi rövid útmutatók során szüksége lesz rá a kiszolgálóhoz és az adatbázisokhoz való csatlakozáshoz. Ezután válassza ki a kiszolgáló nevét a kiszolgáló beállításainak megnyitásához.

    ![kiszolgálónév keresése](././media/load-data-from-azure-blob-storage-using-polybase/find-server-name.png)

3. A kiszolgálóbeállítások megnyitásához válassza ki a kiszolgáló nevét.

    ![kiszolgáló beállításai](./media/load-data-from-azure-blob-storage-using-polybase/server-settings.png)

4. Válassza **a Tűzfalbeállítások megjelenítése**lehetőséget. Megnyílik az SQL-adatbáziskiszolgálóhoz tartozó **Tűzfalbeállítások** oldal.

    ![kiszolgálói tűzfalszabály](./media/load-data-from-azure-blob-storage-using-polybase/server-firewall-rule.png)

5. Válassza az **eszköztár Ügyfél IP hozzáadása** lehetőséget, ha az aktuális IP-címet új tűzfalszabályhoz szeretné hozzáadni. A tűzfalszabály az 1433-as portot egy egyedi IP-cím vagy egy IP-címtartomány számára nyithatja meg.

6. Kattintson a **Mentés** gombra. A rendszer létrehoz egy kiszolgálószintű tűzfalszabályt az aktuális IP-címhez, és megnyitja az 1433-as portot a logikai kiszolgálón.

7. Válassza **az OK gombot,** majd zárja be a **Tűzfal beállításai** lapot.

Mostantól csatlakozhat az SQL-kiszolgálóhoz és annak adattárházaihoz erről az IP-címről. A csatlakozás az SQL Server Management Studio vagy más, választott eszköz használatával lehetséges. A csatlakozáskor használja a korábban létrehozott ServerAdmin-fiókot.  

> [!IMPORTANT]
> Alapértelmezés szerint az összes Azure-szolgáltatás számára engedélyezett a hozzáférés az SQL Database tűzfalán keresztül. Ezen a lapon válassza **a KI** lehetőséget, majd a **Mentés** lehetőséget az összes Azure-szolgáltatás tűzfalának letiltásához.

## <a name="get-the-fully-qualified-server-name"></a>A teljes kiszolgálónév lekérése

Kérje le az SQL-kiszolgáló teljes kiszolgálónevét az Azure Portalon. Később ezt a teljes nevet fogja majd használni a kiszolgálóhoz való kapcsolódás során.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. Válassza ki az **Azure Synapse Analytics** a bal oldali menüben, és válassza ki az adatbázist az **Azure Synapse Analytics** oldalon.
3. Az Azure Portalon az adatbázishoz tartozó lap **Alapvető erőforrások** ablaktábláján keresse meg, majd másolja ki a **Kiszolgáló nevét**. Ebben a példában a teljesen minősített név mynewserver-20180430.database.windows.net.

    ![kapcsolatadatok](././media/load-data-from-azure-blob-storage-using-polybase/find-server-name.png)  

## <a name="connect-to-the-server-as-server-admin"></a>Csatlakozás a kiszolgálóhoz kiszolgáló-rendszergazdaként

Ebben a részben az [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) használatával építjük fel a kapcsolatot az Azure SQL-kiszolgálóval.

1. Nyissa meg az SQL Server Management Studiót.

2. A **Connect to Server** (Kapcsolódás a kiszolgálóhoz) párbeszédpanelen adja meg a következő adatokat:

    | Beállítás        | Ajánlott érték                            | Leírás                                                  |
    | -------------- | ------------------------------------------ | ------------------------------------------------------------ |
    | Kiszolgáló típusa    | Adatbázismotor                            | Kötelezően megadandó érték                                       |
    | Kiszolgálónév    | A teljes kiszolgálónév            | A név kell valami ilyesmi: **mynewserver-20180430.database.windows.net**. |
    | Hitelesítés | SQL Server-hitelesítés                  | Ebben az oktatóanyagban az SQL-hitelesítésen kívül más hitelesítéstípus nincs konfigurálva. |
    | Bejelentkezés          | A kiszolgálói rendszergazdafiók                   | Ezt a fiókot adta meg a kiszolgáló létrehozásakor. |
    | Jelszó       | A kiszolgálói rendszergazdai fiók jelszava | Ez az a jelszó, amely a kiszolgáló létrehozásakor lett megadva. |

    ![kapcsolódás a kiszolgálóhoz](./media/load-data-from-azure-blob-storage-using-polybase/connect-to-server.png)

3. Kattintson a **Csatlakozás** gombra. Megnyílik az Object Explorer ablak az SSMS-ben.

4. Az Object Explorerben bontsa ki a **Databases** (Adatbázisok) elemet. Ezután bontsa ki a **System databases** (Rendszeradatbázisok) és a **master** elemeket az objektumok megtekintéséhez a master adatbázisban.  Bontsa ki a **mySampleDatabase** csomópontot az új adatbázisban található objektumok megtekintéséhez.

    ![adatbázis-objektumok](./media/load-data-from-azure-blob-storage-using-polybase/connected.png)

## <a name="create-a-user-for-loading-data"></a>Felhasználó létrehozása az adatok betöltéséhez

A kiszolgáló rendszergazdai fiókjának célja, hogy felügyeleti műveleteket végezzenek vele, és nem alkalmas a felhasználói adatok lekérdezésére. Az adatok betöltése memóriaigényes művelet. A memóriamaximumok meghatározása a konfigurált [adattárházegységek](what-is-a-data-warehouse-unit-dwu-cdwu.md) és [erőforrásosztály](resource-classes-for-workload-management.md) szerint történik.

Érdemes létrehozni egy adatok betöltésére kijelölt felhasználót és fiókot. Ezután adja hozzá a betöltést végző felhasználót egy olyan [erőforrásosztályhoz](resource-classes-for-workload-management.md), amely lehetővé teszi a megfelelő mértékű maximális memórialefoglalást.

Mivel jelenleg a kiszolgálói rendszergazdaként csatlakozik, létrehozhat bejelentkezéseket és felhasználókat. Kövesse ezeket a lépéseket egy **LoaderRC20** nevű fiók és felhasználó létrehozásához. Ezután rendelje hozzá a felhasználót a **staticrc20** erőforrásosztályhoz.

1. Az SSMS jobb oldali válassza ki a **főkiszolgálót** a legördülő menü megjelenítéséhez, és válassza az **Új lekérdezés**parancsot. Megnyílik egy új lekérdezési ablak.

    ![Új lekérdezés a master adatbázisban](./media/load-data-from-azure-blob-storage-using-polybase/create-loader-login.png)

2. A lekérdezési ablakban adja meg ezeket a T-SQL-parancsokat egy LoaderRC20 nevű fiók és felhasználó létrehozásához, az „a123STRONGpassword!” helyett pedig adjon meg egy saját jelszót.

    ```sql
    CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    ```

3. Válassza a **Végrehajtás** lehetőséget.

4. Kattintson jobb gombbal a **mySampleDataWarehouse** elemre, majd válassza a **New Query** (Új lekérdezés) elemet. Megnyílik egy új lekérdezési ablak.  

    ![Új lekérdezés futtatása a minta-adattárházon](./media/load-data-from-azure-blob-storage-using-polybase/create-loading-user.png)

5. A következő T-SQL-parancsok begépelésével hozzon létre egy LoaderRC20 nevű felhasználót a LoaderRC20-fiókhoz. A második sor az új adattárházra vonatkozó CONTROL (vezérlési) engedélyeket ad az új felhasználónak.  Ezen engedélyek megadása ahhoz hasonló, mintha az adatbázis tulajdonosává tenné a felhasználót. A harmadik sor a staticrc20 [erőforrásosztály](resource-classes-for-workload-management.md) tagjaként veszi fel az új felhasználót.

    ```sql
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
    EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
    ```

6. Válassza a **Végrehajtás** lehetőséget.

## <a name="connect-to-the-server-as-the-loading-user"></a>Csatlakozás a kiszolgálóhoz a betöltést végző felhasználóként

Az adatok betöltésének első lépése a LoaderRC20-ként való bejelentkezés.  

1. Az Objektumkezelőben válassza a **Csatlakozás** legördülő menüt, és válassza az **Adatbázis-motor lehetőséget**. A **Connect to Server** (Kapcsolódás a kiszolgálóhoz) párbeszédpanel jelenik meg.

    ![Csatlakozás az új fiókkal](./media/load-data-from-azure-blob-storage-using-polybase/connect-as-loading-user.png)

2. Gépelje be a teljes kiszolgálónevet, és adja meg a **LoaderRC20** felhasználónévként.  Adja meg a LoaderRC20-hoz tartozó jelszót.

3. Kattintson a **Csatlakozás** gombra.

4. Ha a kapcsolat készen áll, az Object Explorerben két kiszolgálói kapcsolat lesz látható. Az egyik kapcsolat ServerAdmin-ként, a másik pedig MedRCLogin-ként jelenik meg.

    ![Sikeres csatlakozás](./media/load-data-from-azure-blob-storage-using-polybase/connected-as-new-login.png)

## <a name="create-external-tables-for-the-sample-data"></a>Külső táblák létrehozása a mintaadatokhoz

Készen áll megkezdeni az adatok az új adattárházba való betöltésének folyamatát. Ez az oktatóanyag bemutatja, hogyan használhatja a külső táblákat a New York-i taxiadatok betöltéséhez egy Azure Storage-blobból. A későbbi, a [betöltési áttekintésben](design-elt-data-loading.md)megtudhatja, hogyan juthat el az Azure blobstorage-ba, illetve hogyan töltheti be őket közvetlenül a forrásból.

Futtassa a következő SQL-parancsfájlokat, és adja meg a betölteni kívánt adatokkal kapcsolatos információkat. Ezen információk közé tartozik az adatok helye, az adatok tartalmának formátuma és az adatok tábladefiníciója.

1. Az előző szakaszban LoaderRC20-ként jelentkezett be az adattárházba. Az SSMS-ben kattintson jobb gombbal a LoaderRC20-kapcsolatra, és válassza a **New Query** (Új lekérdezés) elemet.  Megnyílik egy új lekérdezési ablak.

    ![Új betöltési lekérdezési ablak](./media/load-data-from-azure-blob-storage-using-polybase/new-loading-query.png)

2. Hasonlítsa össze a lekérdezési ablakot az előző képpel.  Győződjön meg arról, hogy az új lekérdezési ablak LoaderRC20-ként fut, és a MySampleDataWarehouse adatbázison hajt végre lekérdezéseket. A betöltés összes lépését ebben a lekérdezési ablakban végezze el.

3. Hozzon létre egy főkulcsot a MySampleDataWarehouse adatbázishoz. Adatbázisonként csak egyszer kell főkulcsot létrehoznia.

    ```sql
    CREATE MASTER KEY;
    ```

4. Futtassa a következő [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) utasítást az Azure blob helyének meghatározásához. Ez a külső taxiadatok helye.  A lekérdezési ablakhoz csatolt parancs futtatásához jelölje ki a futtatni kívánt parancsokat, és válassza a **Végrehajtás lehetőséget.**

    ```sql
    CREATE EXTERNAL DATA SOURCE NYTPublic
    WITH
    (
        TYPE = Hadoop,
        LOCATION = 'wasbs://2013@nytaxiblob.blob.core.windows.net/'
    );
    ```

5. Futtassa a következő [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) T-SQL-utasítást a külső adatfájl formázási jellemzőinek és beállításainak megadásához. Ez az utasítás adja meg, hogy a külső adatok szövegként legyenek tárolva, továbbá azt is, hogy az értékeket függőleges vonal („|”) karakter válassza el egymástól. A külső fájl tömörítése a Gzip használatával történik.

    ```sql
    CREATE EXTERNAL FILE FORMAT uncompressedcsv
    WITH (
        FORMAT_TYPE = DELIMITEDTEXT,
        FORMAT_OPTIONS (
            FIELD_TERMINATOR = ',',
            STRING_DELIMITER = '',
            DATE_FORMAT = '',
            USE_TYPE_DEFAULT = False
        )
    );
    CREATE EXTERNAL FILE FORMAT compressedcsv
    WITH (
        FORMAT_TYPE = DELIMITEDTEXT,
        FORMAT_OPTIONS ( FIELD_TERMINATOR = '|',
            STRING_DELIMITER = '',
        DATE_FORMAT = '',
            USE_TYPE_DEFAULT = False
        ),
        DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
    );
    ```

6. Futtassa a következő [CREATE SCHEMA](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) utasítást egy séma létrehozásához a külső fájlformátum számára. A séma lehetővé teszi a létrehozni kívánt külső táblák rendszerezését.

    ```sql
    CREATE SCHEMA ext;
    ```

7. Hozza létre a külső táblákat. A tábladefiníciók az adatraktárban tárolódnak, de a táblák az Azure blob storage-ban tárolt adatokra hivatkoznak. A következő T-SQL parancsok futtatásával hozzon létre több külső táblát, amelyek mind a külső adatforrásban korábban meghatározott Azure-blobra mutatnak.

    ```sql
    CREATE EXTERNAL TABLE [ext].[Date]
    (
        [DateID] int NOT NULL,
        [Date] datetime NULL,
        [DateBKey] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DaySuffix] varchar(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeek] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeekInMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeekInYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfQuarter] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfYear] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfMonth] varchar(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Month] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Quarter] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [QuarterName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Year] char(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [YearName] char(7) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthYear] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MMYYYY] char(6) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [FirstDayOfMonth] date NULL,
        [LastDayOfMonth] date NULL,
        [FirstDayOfQuarter] date NULL,
        [LastDayOfQuarter] date NULL,
        [FirstDayOfYear] date NULL,
        [LastDayOfYear] date NULL,
        [IsHolidayUSA] bit NULL,
        [IsWeekday] bit NULL,
        [HolidayUSA] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        LOCATION = 'Date',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    );
    CREATE EXTERNAL TABLE [ext].[Geography]
    (
        [GeographyID] int NOT NULL,
        [ZipCodeBKey] varchar(10) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [County] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [City] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [State] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Country] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [ZipCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        LOCATION = 'Geography',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    );
    CREATE EXTERNAL TABLE [ext].[HackneyLicense]
    (
        [HackneyLicenseID] int NOT NULL,
        [HackneyLicenseBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [HackneyLicenseCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        LOCATION = 'HackneyLicense',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    );
    CREATE EXTERNAL TABLE [ext].[Medallion]
    (
        [MedallionID] int NOT NULL,
        [MedallionBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [MedallionCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        LOCATION = 'Medallion',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    )
    ;  
    CREATE EXTERNAL TABLE [ext].[Time]
    (
        [TimeID] int NOT NULL,
        [TimeBKey] varchar(8) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [HourNumber] tinyint NOT NULL,
        [MinuteNumber] tinyint NOT NULL,
        [SecondNumber] tinyint NOT NULL,
        [TimeInSecond] int NOT NULL,
        [HourlyBucket] varchar(15) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [DayTimeBucketGroupKey] int NOT NULL,
        [DayTimeBucket] varchar(100) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL
    )
    WITH
    (
        LOCATION = 'Time',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    );
    CREATE EXTERNAL TABLE [ext].[Trip]
    (
        [DateID] int NOT NULL,
        [MedallionID] int NOT NULL,
        [HackneyLicenseID] int NOT NULL,
        [PickupTimeID] int NOT NULL,
        [DropoffTimeID] int NOT NULL,
        [PickupGeographyID] int NULL,
        [DropoffGeographyID] int NULL,
        [PickupLatitude] float NULL,
        [PickupLongitude] float NULL,
        [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DropoffLatitude] float NULL,
        [DropoffLongitude] float NULL,
        [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [PassengerCount] int NULL,
        [TripDurationSeconds] int NULL,
        [TripDistanceMiles] float NULL,
        [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [FareAmount] money NULL,
        [SurchargeAmount] money NULL,
        [TaxAmount] money NULL,
        [TipAmount] money NULL,
        [TollsAmount] money NULL,
        [TotalAmount] money NULL
    )
    WITH
    (
        LOCATION = 'Trip2013',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = compressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    );
    CREATE EXTERNAL TABLE [ext].[Weather]
    (
        [DateID] int NOT NULL,
        [GeographyID] int NOT NULL,
        [PrecipitationInches] float NOT NULL,
        [AvgTemperatureFahrenheit] float NOT NULL
    )
    WITH
    (
        LOCATION = 'Weather',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    )
    ;
    ```

8. Az Object Explorerben bontsa ki a mySampleDataWarehouse elemet az imént létrehozott külső táblák listájának megtekintéséhez.

    ![Külső táblák megtekintése](./media/load-data-from-azure-blob-storage-using-polybase/view-external-tables.png)

## <a name="load-the-data-into-your-data-warehouse"></a>Az adatok betöltése az adattárházba

Ez a szakasz az imént definiált külső táblákat használja a mintaadatok azure storage blobból való betöltéséhez.  

> [!NOTE]
> Ez az oktatóanyag az adatokat közvetlenül a végső táblázatba tölti be. Éles környezetben általában a CREATE TABLE AS SELECT utasítás használatával végez betöltést egy előkészítési táblába. Amíg az adatok az előkészítési táblában vannak, bármilyen szükséges átalakítás elvégezhető rajtuk. Az előkészítési táblában lévő adatok éles táblához való hozzáfűzéséhez használhatja az INSERT...SELECT utasítást. További információkért lásd: [Adatok beszúrása egy éles táblába](guidance-for-loading-data.md#inserting-data-into-a-production-table).

A szkript a [CREATE TABLE AS SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) T-SQL-utasítást használja az adatok betöltéséhez az Azure Storage-blobból az adattárházban található új táblákba. A CTAS egy új táblát hoz létre egy kiválasztási utasítás eredményei alapján. Az új tábla oszlopai és adattípusai megegyeznek a kiválasztási utasítás eredményeivel. Amikor a select utasítás külső táblából választ, az adatok importálása az adatraktár ban lévő relációs táblába történik.

1. Futtassa a következő szkriptet az adatok betöltéséhez az adattárházban található új táblákba.

    ```sql
    CREATE TABLE [dbo].[Date]
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Date]
    OPTION (LABEL = 'CTAS : Load [dbo].[Date]')
    ;
    CREATE TABLE [dbo].[Geography]
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT * FROM [ext].[Geography]
    OPTION (LABEL = 'CTAS : Load [dbo].[Geography]')
    ;
    CREATE TABLE [dbo].[HackneyLicense]
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[HackneyLicense]
    OPTION (LABEL = 'CTAS : Load [dbo].[HackneyLicense]')
    ;
    CREATE TABLE [dbo].[Medallion]
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Medallion]
    OPTION (LABEL = 'CTAS : Load [dbo].[Medallion]')
    ;
    CREATE TABLE [dbo].[Time]
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Time]
    OPTION (LABEL = 'CTAS : Load [dbo].[Time]')
    ;
    CREATE TABLE [dbo].[Weather]
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Weather]
    OPTION (LABEL = 'CTAS : Load [dbo].[Weather]')
    ;
    CREATE TABLE [dbo].[Trip]
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Trip]
    OPTION (LABEL = 'CTAS : Load [dbo].[Trip]')
    ;
    ```

2. A betöltés közben megtekintheti az adatokat. Több GB-nyi adatot tölt be, és nagy teljesítményt igénylő fürtözött oszlopcentrikus indexekbe tömöríti azokat. Futtassa az alábbi lekérdezést, amely dinamikus felügyeleti nézetekkel (DMV-k) jeleníti meg a töltés állapotát.

    ```sql
    SELECT
        r.command,
        s.request_id,
        r.status,
        count(distinct input_name) as nbr_files,
        sum(s.bytes_processed)/1024/1024/1024.0 as gb_processed
    FROM
        sys.dm_pdw_exec_requests r
        INNER JOIN sys.dm_pdw_dms_external_work s
        ON r.request_id = s.request_id
    WHERE
        r.[label] = 'CTAS : Load [dbo].[Date]' OR
        r.[label] = 'CTAS : Load [dbo].[Geography]' OR
        r.[label] = 'CTAS : Load [dbo].[HackneyLicense]' OR
        r.[label] = 'CTAS : Load [dbo].[Medallion]' OR
        r.[label] = 'CTAS : Load [dbo].[Time]' OR
        r.[label] = 'CTAS : Load [dbo].[Weather]' OR
        r.[label] = 'CTAS : Load [dbo].[Trip]'
    GROUP BY
        r.command,
        s.request_id,
        r.status
    ORDER BY
        nbr_files desc,
        gb_processed desc;
    ```

3. Tekintse meg az összes rendszerlekérdezést.

    ```sql
    SELECT * FROM sys.dm_pdw_exec_requests;
    ```

4. Láthatja, ahogy adatai szépen betöltődnek az adattárházba.

    ![A betöltött táblák megtekintése](./media/load-data-from-azure-blob-storage-using-polybase/view-loaded-tables.png)

## <a name="authenticate-using-managed-identities-to-load-optional"></a>Hitelesítés felügyelt identitások használatával a betöltéshez (nem kötelező)

A PolyBase használatával történő betöltés és a felügyelt identitások hitelesítése a legbiztonságosabb mechanizmus, amely lehetővé teszi a virtuális hálózati szolgáltatás végpontjainak kihasználását az Azure Storage-szal.

### <a name="prerequisites"></a>Előfeltételek

1. Telepítse az Azure PowerShellt ezzel az [útmutatóval.](/powershell/azure/install-az-ps?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
2. Ha általános célú v1- vagy blobtár-fiókkal rendelkezik, először frissítenie kell az általános célú v2-re ezzel az [útmutatóval.](../../storage/common/storage-account-upgrade.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
3. Az Azure Storage-fiók **tűzfalai és a virtuális hálózatok** beállításai menüben be van kapcsolva a megbízható **Microsoft-szolgáltatások hozzáférésének engedélyezése a tárfiókhoz.** További információt ebben az [útmutatóban](../../storage/common/storage-network-security.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#exceptions) talál.

#### <a name="steps"></a>Lépések

1. A PowerShellben **regisztrálja az SQL-kiszolgálót az** Azure Active Directoryval (AAD):

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId your-subscriptionId
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-database-servername -AssignIdentity
   ```

2. Általános **célú v2 tárfiók** létrehozása ezzel az [útmutatóval.](../../storage/common/storage-account-create.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

   > [!NOTE]
   > Ha általános célú v1- vagy blobtár-fiókkal rendelkezik, **először frissítenie** kell a v2-re ezzel az [útmutatóval.](../../storage/common/storage-account-upgrade.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

3. A tárfiók alatt keresse meg a **Hozzáférés-vezérlés (IAM)** elemet, és válassza **a Szerepkör-hozzárendelés hozzáadása**lehetőséget. Rendelje hozzá **a Storage Blob Data Contributor** RBAC szerepkört az SQL Database-kiszolgálóhoz.

   > [!NOTE]
   > Ezt a lépést csak tulajdonosi jogosultsággal rendelkező tagok hajthatják végre. Az Azure-erőforrások különböző beépített szerepköreiről ezt az [útmutatót](../../role-based-access-control/built-in-roles.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)ismerteti.
  
**Polibázis-kapcsolat az Azure Storage-fiókkal:**

1. Hozza létre az adatbázis hatóköre hitelesítő adatait **az IDENTITY = "Felügyelt szolgáltatás identitása"** használatával:

   ```SQL
   CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
   ```

   > [!NOTE]
   >
   > * Nincs szükség secret megadása az Azure Storage hozzáférési kulcs, mert ez a mechanizmus [felügyelt identitást](../../active-directory/managed-identities-azure-resources/overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) használ a borítók alatt.
   > * Identity név kell **"felügyelt szolgáltatás identitása"** a PolyBase-kapcsolat az Azure Storage-fiókkal való együttműködésre.

2. Hozza létre a külső adatforrást, amely megadja az adatbázis hatókörrel rendelkező hitelesítő adatait a felügyelt szolgáltatásidentitással.

3. Lekérdezés a szokásos módon [külső táblák](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)használatával .

Tekintse meg a következő [dokumentációt,](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) ha virtuális hálózati szolgáltatás végpontokat szeretne beállítani az Azure Synapse Analytics számára.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az adattárházába betöltött számítási erőforrások és adatok díjkötelesek. Ezeket külön-külön számlázzuk.

* Ha szeretné az adatokat megtartani a tárolóban, a számítási erőforrásokat szüneteltetheti, amíg nem használja az adattárházat. A számítás felfüggesztése esetén csak az adattárolásért kell fizetni, és folytathatja a számítást, amikor már készen áll az adatokkal való munkára.
* Ha szeretné megelőzni a jövőbeli kiadásokat, az adattárházat törölheti is.

Kövesse az alábbi lépéseket a fölöslegessé vált erőforrások eltávolítására.

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com)válassza ki az adatraktárt.

    ![Az erőforrások eltávolítása](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. A számítás szüneteltetéséhez kattintson a **Szünet** gombra. Ha az adattárház szüneteltetve van, az **Indítás** gomb látható.  A számítás folytatásához válassza a **Start gombot.**

3. Ha el szeretné távolítani az adatraktárt, hogy ne kelljen fizetnie a számításért vagy a tárolásért, válassza a **Törlés lehetőséget.**

4. A létrehozott SQL-kiszolgáló eltávolításához jelölje **be mynewserver-20180430.database.windows.net** az előző képen, majd kattintson a **Törlés gombra.**  Ezzel kapcsolatban legyen körültekintő, mert a kiszolgáló törlésével a kiszolgálóhoz rendelt összes adatbázis is törölve lesz.

5. Az erőforráscsoport eltávolításához válassza a **MyResourceGroup**lehetőséget, majd az **Erőforráscsoport törlése**lehetőséget.

## <a name="next-steps"></a>További lépések

Ennek az oktatóanyagnak a segítségével megtanulta, hogyan hozhat létre egy adattárházat, illetve egy felhasználót az adatok betöltéséhez. Külső táblákat hozott létre, hogy definiálhassa az Azure Storage-blobban tárolt adatok struktúráját, majd a PolyBase CREATE TABLE AS SELECT utasításával adatokat töltött be az adattárházába.

A következőket hajtotta végre:
> [!div class="checklist"]
>
> * Egy adattárház létrehozása az Azure Portalon
> * Kiszolgálószintű tűzfalszabály létrehozása az Azure Portalon
> * Csatlakozás az adattárházhoz az SSMS használatával
> * Adatok betöltésére kijelölt felhasználó létrehozása
> * Külső táblák létrehozása az Azure Storage-blobban található adatokhoz
> * Adatok betöltése az adattárházba a CTAS T-SQL-utasítás használatával
> * Az adatok állapotának megtekintése betöltés közben
> * Statisztikák készítése az újonnan betöltött adatokról

A fejlesztési áttekintéshez továbbtekinthet, és megtudhatja, hogyan telepíthet át egy meglévő adatbázist az Azure Synapse Analytics szolgáltatásba.

> [!div class="nextstepaction"]
> [Meglévő adatbázis Azure Synapse Analytics szolgáltatásba való áttelepítésének tervezési döntései](sql-data-warehouse-overview-develop.md)
