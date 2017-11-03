---
title: "SQL Server adatbázis áttelepítése az Azure SQL Database |} Microsoft Docs"
description: "Ismerje meg, hogy az SQL Server-adatbázis áttelepítése az Azure SQL Database."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,migrate
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: Active
ms.date: 09/01/2017
ms.author: carlrab
ms.openlocfilehash: 526222944974c08f92aec2a8418e9b42401bc4d3
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="migrate-your-sql-server-database-to-azure-sql-database"></a>Az SQL Server-adatbázis áttelepítése az Azure SQL Database

Helyezze át az SQL Server az Azure SQL Database-adatbázis más dolga, mint egy üres SQL-adatbázis létrehozása az Azure-ban, majd ezzel a [adatok áttelepítési Segéd](https://www.microsoft.com/download/details.aspx?id=53595) (DMA-) az adatbázis importálására az Azure. Ebben az oktatóanyagban elsajátíthatja, hogy:

> [!div class="checklist"]
> * Üres Azure SQL-adatbázis létrehozása az Azure portálon (egy új vagy meglévő Azure SQL adatbázis-kiszolgáló)
> * Az Azure-portálon hozzon létre egy kiszolgálószintű tűzfal (Ha nem a korábban létrehozott)
> * Használja a [adatok áttelepítési Segéd](https://www.microsoft.com/download/details.aspx?id=53595) (DMA-) az SQL Server adatbázis importálása az üres Azure SQL-adatbázis 
> * Használjon [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) adatbázis tulajdonságainak módosításához.

Ha nem rendelkezik Azure-előfizetéssel, [ingyenes fiók létrehozását](https://azure.microsoft.com/free/) megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

- A legújabb verziójának telepítése [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).  
- A legújabb verzióját a [adatok áttelepítési Segéd](https://www.microsoft.com/download/details.aspx?id=53595) (DMA-).
- Azonosította, és egy adatbázis áttelepítéséhez hozzáféréssel rendelkeznek. Ez az oktatóanyag használja a [SQL Server 2008R2 AdventureWorks OLTP adatbázis](https://msftdbprodsamples.codeplex.com/releases/view/59211) egy példány SQL Server 2008R2 vagy újabb, de használhatja az Ön által választott bármely adatbázis. Kompatibilitási problémák megoldásához használjon [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)

## <a name="log-in-to-the-azure-portal"></a>Jelentkezzen be az Azure portálra.

Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

## <a name="create-a-blank-sql-database"></a>Üres SQL-adatbázis létrehozása

Az Azure SQL-adatbázis [számítási és tárolási erőforrások](sql-database-service-tiers.md) egy meghatározott készletével együtt jön létre. Az adatbázis egy [Azure-erőforráscsoporton](../azure-resource-manager/resource-group-overview.md) belül egy [Azure SQL Database logikai kiszolgálón](sql-database-features.md) jön létre. 

Kövesse az alábbi lépéseket egy üres SQL-adatbázis létrehozásához. 

1. Kattintson az Azure Portal bal felső sarkában található **Új** gombra.

2. Az **Új** oldalon válassza az **Adatbázisok** lehetőséget, majd az **Új** oldal **SQL Database** területén válassza a **Létrehozás** lehetőséget.

   ![Üres-adatbázis létrehozása](./media/sql-database-design-first-database/create-empty-database.png)

3. Töltse ki az SQL Database űrlapját a következő információkkal az előző képen látható módon:   

   | Beállítás       | Ajánlott érték | Leírás | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Adatbázis neve** | mySampleDatabase | Az érvényes adatbázisnevekkel kapcsolatban lásd az [adatbázis-azonosítókat](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers) ismertető cikket. | 
   | **Előfizetés** | Az Ön előfizetése  | Az előfizetései részleteivel kapcsolatban lásd az [előfizetéseket](https://account.windowsazure.com/Subscriptions) ismertető cikket. |
   | **Erőforráscsoport** | myResourceGroup | Az érvényes erőforráscsoport-nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) ismertető cikket. |
   | **Forrás kiválasztása** | Az üres adatbázis | Meghatározza, hogy egy üres adatbázist kell létrehozni. |

4. Kattintson a **Kiszolgáló** lehetőségre új kiszolgáló létrehozásához és konfigurálásához az új adatbázis számára. Töltse ki a **új kiszolgáló űrlap** a következő információkat: 

   | Beállítás       | Ajánlott érték | Leírás | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Kiszolgálónév** | Bármely globálisan egyedi név | Az érvényes kiszolgálónevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) ismertető cikket. | 
   | **Kiszolgálói rendszergazdai bejelentkezés** | Bármely érvényes név | Az érvényes bejelentkezési nevekkel kapcsolatban lásd az [adatbázis-azonosítókat](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers) ismertető cikket.|
   | **Jelszó** | Bármely érvényes jelszó | A jelszó legalább 8 karakterből kell állnia, és az alábbiak közül hármat tartalmaznia kell: nagybetűk, kisbetűk, számok és nem alfanumerikus karakterek száma. |
   | **Hely** | Bármely érvényes hely | A régiókkal kapcsolatos információkért lásd [az Azure régióit](https://azure.microsoft.com/regions/) ismertető cikket. |

   ![adatbázis-kiszolgáló létrehozása](./media/sql-database-design-first-database/create-database-server.png)

5. Kattintson a **Kiválasztás** gombra.

6. Kattintson a **Tarifacsomag** parancsra a szolgáltatásszint, a DTU-szám és a tárterületméret megadásához. Megismerkedhet a dtu-inak száma és az Ön számára elérhető egyes száma beállításait. 

7. A jelen oktatóanyag esetében válassza ki a **szabványos** szolgáltatásréteget, és a csúszka segítségével válassza ki a **100 dtu-i (S3)** és **400** GB tárhelyet.

   ![adatbázis létrehozása-s1](./media/sql-database-design-first-database/create-empty-database-pricing-tier.png)

8. A **Kiegészítő tárterület** beállítás használatához el kell fogadnia az előzetes verziójú szolgáltatás feltételeit. 

   > [!IMPORTANT]
   > \* A szolgáltatási keretbe foglaltnál nagyobb tárterületek előzetes verzióban érhetők el, és extra költségek vonatkoznak rájuk. Részletes információ: [SQL Database – Díjszabás](https://azure.microsoft.com/pricing/details/sql-database/). 
   >
   >\* Az 1 TB tárhelyméretet meghaladó prémium szintű készletek jelenleg a következő régiókban érhetők el: USA 2. keleti régiója, USA nyugati régiója, USA-beli államigazgatás – Virginia, Nyugat-Európa, Közép-Németország, Délkelet-Ázsia, Kelet-Japán, Kelet-Ausztrália, Közép-Kanada és Kelet-Kanada. Lásd: [P11–P15 – Aktuális korlátozások](sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
   > 

9. A kiszolgálószint, a DTU-szám és a tárterületméret kiválasztása után kattintson az **Alkalmaz** gombra.  

10. Válassza ki a **rendezés** az üres adatbázis (a jelen oktatóanyag esetében használja az alapértelmezett érték). Rendezések kapcsolatos további információkért lásd: [rendezések](https://docs.microsoft.com/sql/t-sql/statements/collations)

11. Most, hogy kitöltötte az SQL Database űrlapját, kattintson a **Létrehozás** gombra az adatbázis létrehozásához. Az üzembe helyezés eltarthat néhány percig. 

12. Az eszköztáron kattintson az **Értesítések** parancsra az üzembe helyezési folyamat megfigyeléséhez.
    
     ![értesítés](./media/sql-database-get-started-portal/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Kiszolgálószintű tűzfalszabály létrehozása

Az SQL Database szolgáltatás egy tűzfalat hoz létre a kiszolgáló szintjén, amely megakadályozza, hogy a külső alkalmazások és eszközök csatlakozzanak a kiszolgálóhoz vagy a kiszolgálón lévő adatbázisokhoz, kivéve, ha létrehoz tűzfalszabályt, hogy adott IP-címek számára megnyissa a tűzfalat. A következő lépésekkel hozzon létre egy [kiszolgálószintű SQL Database-tűzfalszabályt](sql-database-firewall-configure.md) az ügyfél IP-címéhez, és engedélyezze a külső kapcsolatokat csak az Ön IP-címéhez az SQL Database-tűzfalon keresztül. 

> [!NOTE]
> Az SQL Database az 1433-as porton kommunikál. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat az 1433-as porton keresztül. Ebben az esetben nem tud csatlakozni az Azure SQL Database-kiszolgálóhoz, ha az informatikai részleg nem nyitja meg az 1433-as portot.
>

1. Az üzembe helyezés befejezése után kattintson az **SQL-adatbázisok** elemre a bal oldali menüben, majd kattintson a **mySampleDatabase** adatbázisra az **SQL-adatbázisok** lapon. Megnyílik az adatbázis áttekintő oldala, amelyen látható a teljes kiszolgálónév (például: **mynewserver-20170824.database.windows.net**), valamint a további konfigurálható beállítások. 

2. Másolja le ezt a teljes kiszolgálónevet, mert a későbbi rövid útmutatók során szüksége lesz rá a kiszolgálóhoz és az adatbázisokhoz való csatlakozáshoz. 

   ![kiszolgáló neve](./media/sql-database-get-started-portal/server-name.png) 

3. Kattintson a **kiszolgáló tűzfalának beállítása** az eszköztáron. Megnyílik az SQL Database kiszolgálóhoz tartozó **Tűzfalbeállítások** oldal. 

   ![kiszolgálói tűzfalszabály](./media/sql-database-get-started-portal/server-firewall-rule.png) 

4. Az eszköztár **Ügyfél IP-címének hozzáadása** elemére kattintva vegye fel aktuális IP-címét egy új tűzfalszabályba. A tűzfalszabály az 1433-as portot egy egyedi IP-cím vagy egy IP-címtartomány számára nyithatja meg.

5. Kattintson a **Save** (Mentés) gombra. A rendszer létrehoz egy kiszolgálószintű tűzfalszabályt az aktuális IP-címhez, és megnyitja az 1433-as portot a logikai kiszolgálón.

6. Kattintson az **OK** gombra, majd zárja be a **Tűzfalbeállítások** lapot.

Most csatlakozhat az SQL adatbázis-kiszolgáló és az adatbázisok, SQL Server Management Studio eszközt, adatok áttelepítési Segéd vagy az Ön által választott, ez a kiszolgáló rendszergazdai fiókjának az előző eljárásban létrehozott használatával IP-címről egy másik eszköz használatával.

> [!IMPORTANT]
> Alapértelmezés szerint az összes Azure-szolgáltatás számára engedélyezett a hozzáférés az SQL Database tűzfalán keresztül. Kattintson a **KI** gombra ezen az oldalon az összes Azure-szolgáltatás hozzáférésének letiltásához.

## <a name="sql-server-connection-information"></a>Az SQL-kiszolgáló kapcsolatadatai

Kérje le az Azure SQL Database kiszolgáló teljes kiszolgálónevét az Azure Portalon. A teljes kiszolgálónév használhatja az ügyféleszközök elől, beleértve az adatok áttelepítési segítséget és az SQL Server Management Studio használatával Azure SQL-kiszolgálóhoz való csatlakozáshoz.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. Válassza az **SQL-adatbázisok** elemet a bal oldali menüben, majd kattintson az új adatbázisra az **SQL-adatbázisok** oldalon. 
3. Az Azure Portalon az adatbázishoz tartozó lap **Alapvető erőforrások** ablaktábláján keresse meg, majd másolja ki a **Kiszolgáló nevét**.

   ![kapcsolatadatok](./media/sql-database-get-started-portal/server-name.png)

## <a name="migrate-your-database"></a>Adatbázis migrálása

Következő lépések segítségével használja a  **[adatok áttelepítési Segéd](https://www.microsoft.com/download/details.aspx?id=53595)**  az áttelepítés az Azure SQL Database adatbázisba vizsgálata, és az áttelepítés befejezéséhez.

1. Nyissa meg a **adatok áttelepítési Segéd**. Futtathatja DMA minden olyan számítógépen, amely az áttelepíteni kívánt adatbázist tartalmazó SQL Server-példányhoz való kapcsolódás és képes kapcsolódni az internetre. Nem kell telepíteni az SQL Server-példány áttelepíteni kívánt futtató számítógépen. A tűzfalszabály, amely egy előző eljárásban létrehozott kell lennie a számítógépen, amelyen az adatok áttelepítési Segéd futtatja.

     ![Nyissa meg az adatok áttelepítési Segéd](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-open.png)

2. Kattintson a bal oldali menüből **+ új** létrehozásához egy **Assessment** projekt. Adja meg a kért értékeket, és kattintson a **létrehozása**:

   | Beállítás      | Ajánlott érték | Leírás | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | Projekt típusa | Migrálás | Az adatbázis az áttelepítéshez értékeléséhez, vagy válassza ki annak ellenőrzéséhez, és ugyanabban a munkafolyamatban részeként áttelepítése |
   |Projekt neve|Áttelepítési útmutató| Egy leíró nevet |
   |Adatforrás kiszolgáló típusa| SQL Server | Ez az, hogy a jelenleg támogatott egyedüli információforrásként |
   |Cél kiszolgáló típusa| Azure SQL Database| Választási lehetőségek: Azure SQL-adatbázis, az SQL Server, az SQL Server Azure virtuális gépeken |
   |Áttelepítési hatókör| Séma- és adatok| Választási lehetőségek: csak a séma- és adatokat, csak, séma adatok |
   
   ![új adatok áttelepítési Segéd projekt](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-new-project.png)

3.  Az a **forrás kiválasztása** lapon, töltse ki a kért értékek, majd kattintson **Connect**:

    | Beállítás      | Ajánlott érték | Leírás | 
    | ------------ | ------------------ | ------------------------------------------------- | 
    | Kiszolgálónév | A kiszolgáló neve vagy IP-cím | A kiszolgáló neve vagy IP-cím |
    | Hitelesítés típusa | Az elsődleges hitelesítés típusa| Választási lehetőségek: A Windows-hitelesítés, az SQL Server-hitelesítés Active Directory beépített hitelesítés, az Active Directory jelszavas hitelesítést |
    | Felhasználónév | A bejelentkezési név | Rendelkeznie kell a bejelentkezési **CONTROL SERVER** engedélyek |
    | Jelszó| a jelszó | a jelszó |
    | Kapcsolat tulajdonságai| Válassza ki **Kapcsolattitkosítása** és **megbízható kiszolgálói tanúsítvány** a környezetnek megfelelően. | A csatlakozás a kiszolgálóhoz megfelelő tulajdonságok kiválasztása |

    ![új adatok áttelepítési forrás kiválasztása](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-source.png)

5. Válasszon egy önálló adatbázis áttelepítése az Azure SQL Database, és kattintson a forráskiszolgálóról történő **következő**. Ebben az oktatóanyagban nincs csak egyetlen adatbázist.

6. Az a **válassza célként** lapon, töltse ki a kért értékek, majd kattintson **Connect**:

    | Beállítás      | Ajánlott érték | Leírás | 
    | ------------ | ------------------ | ------------------------------------------------- | 
    | Kiszolgálónév | A teljes Azure-adatbázis-kiszolgáló neve | A teljesen minősített Azure adatbázis-kiszolgáló nevét az előző eljárás |
    | Hitelesítés típusa | SQL Server-hitelesítés | SQL Server-hitelesítést a lehetőség csak ebben az oktatóanyagban írása, de az Active Directory integrált hitelesítést és az Active Directory jelszavas hitelesítést is támogat az Azure SQL adatbázis |
    | Felhasználónév | A bejelentkezési név | Rendelkeznie kell a bejelentkezési **FELADATVEZÉRLŐ ADATBÁZISHOZ** engedéllyel kell rendelkeznie a forrás-adatbázishoz |
    | Jelszó| a jelszó | a jelszó |
    | Kapcsolat tulajdonságai| Válassza ki **Kapcsolattitkosítása** és **megbízható kiszolgálói tanúsítvány** a környezetnek megfelelően. | A csatlakozás a kiszolgálóhoz megfelelő tulajdonságok kiválasztása |

    ![új adatok áttelepítési válassza cél](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-target.png)

7. Válassza ki az adatbázist a célkiszolgálóról az előző eljárásban létrehozott, és kattintson a **tovább** a source adatbázis séma értékelési folyamat elindításához. Ebben az oktatóanyagban nincs csak egyetlen adatbázist. Figyelje meg, hogy az adatbázis kompatibilitási szintjének beállításai 140, amely az összes új adatbázisokat az Azure SQL-adatbázis alapértelmezett kompatibilitási szintje.

   > [!IMPORTANT] 
   > Az adatbázis az Azure SQL Database az áttelepítés után dönthet úgy fog működni az adatbázis előző verziókkal való kompatibilitás célokra megadott kompatibilitási szinten. A implications és a beállításokat egy adatbázis kompatibilitási szintű működő további információkért lásd: [adatbázis kompatibilitási szintje ALTER](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level). Lásd még: [adatbázis HATÓKÖRŰ konfiguráció ALTER](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) kompatibilitási szintre vonatkozó további adatbázis-szintű beállítással kapcsolatos információkat.
   >

8. Az a **objektumok kijelölése** lap, miután a source adatbázis-séma értékelési folyamat befejeződik, tekintse át az áttelepítési a kijelölt objektumokat, és tekintse át az objektumokat tartalmazó problémákat. Tekintse át például a **dbo.uspSearchCandidateResumes** objektum **SERVERPROPERTY('LCID')** viselkedésváltozások és a **HumanResourcesJobCandidate** -objektuma A teljes szöveges keresés módosításait. 

   > [!IMPORTANT] 
   > Attól függően, az adatbázis tervezés és az alkalmazás, a forrás-adatbázis áttelepítésekor, előfordulhat, hogy módosítania vagy vagy mind az adatbázis vagy az alkalmazás az áttelepítés után (és egyes esetekben az áttelepítés előtt). Az áttelepítés hatással lehet a Transact-SQL közötti különbségeket kapcsolatos információkért lásd: [feloldása Transact-SQL különbségek SQL-adatbázishoz való áttelepítéskor](sql-database-transact-sql-information.md).

     ![új adatok áttelepítési értékelési és objektum kijelölése](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-assessment-results.png)

9. Kattintson a **létrehozni SQL-parancsfájl** az adatbázisséma objektumaiban a forrásadatbázis parancsfájllal történő. 
10. Nézze át a létrehozott parancsfájlt, és kattintson a **ezután ki** áttekintheti az azonosított assessment problémák és javaslatokat igény szerint. Például teljes szöveges keresést, az ajánlás frissítésekor, hogy tesztelje az alkalmazásokat, a teljes szöveges szolgáltatásokat kihasználva. Mentse, vagy másolja a parancsfájlt, ha.

     ![új adatok létrehozott áttelepítési parancsfájl](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-generated-script.png)

11. Kattintson a **telepítés séma** és a séma áttelepítési folyamat megtekintése.

     ![új áttelepítési séma adatáttelepítés](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-schema-migration.png)

12. A séma áttelepítés befejezése után tekintse át az eredményeket a hibákat, és ha nincsenek nincs, kattintson a **adatok áttelepítése**.
13. Az a **táblák kiválasztása** lapon ellenőrizze az áttelepítéshez kijelölt táblák és kattintson a **indítsa el az adatok áttelepítése**.

     ![új adatok áttelepítési adatok áttelepítése](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-data-migration.png)

14. Tekintse meg az áttelepítési folyamat.

     ![új az áttelepítési adatok áttelepítési folyamata](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-data-migration-process.png)

## <a name="connect-to-the-database-with-ssms"></a>Kapcsolódni az adatbázishoz ssms alkalmazásával

Használjon [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) az Azure SQL Database-kiszolgálóhoz csatlakozni.

1. Nyissa meg az SQL Server Management Studiót.

2. A **Connect to Server** (Kapcsolódás a kiszolgálóhoz) párbeszédpanelen adja meg a következő adatokat:

   | Beállítás       | Ajánlott érték | Leírás | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | Kiszolgáló típusa | Adatbázismotor | Ez értékének megadása kötelező. |
   | Kiszolgálónév | A teljes kiszolgálónév | A névnek kell lennie, például ehhez hasonló: **mynewserver20170824.database.windows.net**. |
   | Authentication | SQL Server-hitelesítés | Az SQL-hitelesítés az egyetlen hitelesítési típus, amelyet ebben az oktatóanyagban konfiguráltunk. |
   | Bejelentkezés | A kiszolgálói rendszergazdai fiók | Ez az a fiók, amely a kiszolgáló létrehozásakor lett megadva. |
   | Jelszó | A kiszolgálói rendszergazdai fiók jelszava | Ezt a jelszót adta meg a kiszolgáló létrehozásakor. |

   ![kapcsolódás a kiszolgálóhoz](./media/sql-database-connect-query-ssms/connect.png)

3. A **Connect to server** (Kapcsolódás a kiszolgálóhoz) párbeszédpanelen kattintson az **Options** (Beállítások) elemre. A **Connect to database** (Csatlakozás az adatbázishoz) szakaszban adja meg a következőt: **mySampleDatabase** az adatbázishoz való csatlakozáshoz.

   ![csatlakozás kiszolgálón található adatbázishoz](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. Kattintson a **Connect** (Csatlakozás) gombra. Megnyílik az Object Explorer ablak az SSMS-ben. 

5. Az Object Explorerben bontsa ki a **Database** (Adatbázisok), majd a **mySampleDatabese** csomópontot a mintaadatbázisban található objektumok megtekintéséhez.

   ![Adatbázis-objektumok](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="change-database-properties"></a>Adatbázis tulajdonságainak módosítása

A szolgáltatási rétegben, teljesítményszintet és SQL Server Management Studio használatával kompatibilitási szint módosítása Az importálás fázisban azt javasoljuk, hogy a legjobb teljesítmény érdekében magasabb teljesítmény réteg adatbázis importálja, de méretezni kevesebbet költeni, amíg az aktívan használja az importált adatbázis készen áll az importálás után. A kompatibilitási szint módosítása eredményezhet a jobb teljesítmény és az elérhető legújabb képességeket az Azure SQL Database szolgáltatás elérését. Egy régebbi adatbázis áttelepítésekor az adatbázis kompatibilitási szintje, amely kompatibilis az adatbázis a támogatott legalacsonyabb szintű költséget. További információkért lásd: [növeli a lekérdezési teljesítményt kompatibilitási szint 130 az Azure SQL Database](sql-database-compatibility-level-query-performance-130.md).

1. Az Object Explorerben kattintson a jobb gombbal **mySampleDatabase** majd **új lekérdezés**. A lekérdezés ablak csatlakozik-e az adatbázis.

2. Hajtsa végre a következő parancsot történő beállítása a szolgáltatási rétegben **szabványos** és a teljesítményszintet **S1**.

    ```sql
    ALTER DATABASE mySampleDatabase 
    MODIFY 
        (
        EDITION = 'Standard'
        , MAXSIZE = 250 GB
        , SERVICE_OBJECTIVE = 'S1'
    );
    ```

## <a name="next-steps"></a>Következő lépések 
Ez az oktatóanyag megtanulta, hogy:

> * Üres Azure SQL-adatbázis létrehozása az Azure portálon 
> * Egy kiszolgálószintű tűzfal létrehozása az Azure portálon 
> * Használja a [adatok áttelepítési Segéd](https://www.microsoft.com/download/details.aspx?id=53595) (DMA-) az SQL Server adatbázis importálása az üres Azure SQL-adatbázis 
> * Használjon [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) adatbázis tulajdonságainak módosításához.

A következő oktatóanyag áttekintésével megismerheti, hogyan az adatbázis védelme továbblépés.

> [!div class="nextstepaction"]
> [Az Azure SQL-adatbázis védelme](sql-database-security-tutorial.md).


