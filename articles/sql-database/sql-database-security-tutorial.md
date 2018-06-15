---
title: Az Azure SQL Database-adatbázis védelme | Microsoft Docs
description: Megismerheti az Azure SQL Database-adatbázis védelmét biztosító módszereket és funkciókat.
services: sql-database
author: DRediske
manager: craigg
ms.service: sql-database
ms.custom: mvc,security
ms.topic: tutorial
ms.date: 04/24/2018
ms.author: daredis
ms.openlocfilehash: 54ec3c1386d6ce2023106367a6af1915e754948f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32193461"
---
# <a name="secure-your-azure-sql-database"></a>Az Azure SQL Database-adatbázis védelme

Az SQL Database a következőképpen védi az adatait: 
- Az adatbázishoz való hozzáférés korlátozása tűzfalszabályok használatával 
- Identitást igénylő hitelesítési mechanizmusok használata
- Az adathozzáférés engedélyezése szerepköralapú tagságok és engedélyek segítségével 
- Sorszintű biztonság
- Dinamikus adatmaszkolás

Az SQL Database ezenfelül kifinomult monitorozást, naplózást és fenyegetésészlelést biztosít. 

Mindössze néhány lépés végrehajtásával fokozhatja az adatbázis védelmét a rosszindulatú felhasználókkal és a jogosulatlan hozzáféréssel szemben. Ebben az oktatóanyagban az alábbiakkal fog megismerkedni: 

> [!div class="checklist"]
> * Kiszolgálószintű tűzfalszabályok beállítása a kiszolgáló számára az Azure Portalon.
> * Adatbázisszintű tűzfalszabályok beállítása az adatbázis számára SSMS használatával.
> * Csatlakozás az adatbázishoz biztonságos kapcsolati karakterlánc használatával.
> * Felhasználói hozzáférés kezelése.
> * Adatok védelme titkosítással.
> * Az SQL Database naplózási funkciójának engedélyezése.
> * Az SQL Database fenyegetésészlelési funkciójának engedélyezése.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez győződjön meg arról, hogy rendelkezik az alábbiakkal:

- Telepítette az [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) legújabb verzióját. 
- Telepítette a Microsoft Excelt.
- Létrehozott egy Azure SQL Server-kiszolgálót és Database-adatbázist – erről az [Azure SQL Database létrehozása az Azure Portalon](sql-database-get-started-portal.md), az [Önálló Azure SQL-adatbázis létrehozása az Azure CLI használatával](sql-database-get-started-cli.md) és az [Önálló Azure SQL-adatbázis létrehozása a PowerShell használatával](sql-database-get-started-powershell.md) című cikkben talál további információt. 

## <a name="log-in-to-the-azure-portal"></a>Bejelentkezés az Azure Portalra

Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Kiszolgálószintű tűzfalszabály létrehozása az Azure Portalon

Az SQL Database-adatbázisokat tűzfal védi az Azure-ban. A rendszer alapértelmezés szerint elutasítja a kiszolgálóra és a kiszolgálón lévő adatbázisokra irányuló összes kapcsolatot, a más Azure-szolgáltatások irányából érkező kapcsolatokat kivéve. További információkért lásd: [Kiszolgáló- és adatbázisszintű Azure SQL Database-tűzfalszabályok](sql-database-firewall-configure.md).

A legbiztonságosabb konfigurációt az „Azure-szolgáltatásokhoz való hozzáférés engedélyezése” beállítás kikapcsolása biztosítja. Ha az adatbázishoz Azure-beli virtuális gépről vagy felhőszolgáltatásból kíván csatlakozni, létre kell hoznia egy [fenntartott IP-címet](../virtual-network/virtual-networks-reserved-public-ip.md), és a tűzfalon keresztüli hozzáférést csak ezen fenntartott IP-cím számára tegye lehetővé. 

Kövesse az alábbi lépéseket, és hozzon létre egy [SQL Database kiszolgálószintű tűzfalszabályt](sql-database-firewall-configure.md) a kiszolgáló számára az adott IP-címről történő kapcsolódás engedélyezéséhez. 

> [!NOTE]
> Ha az előző oktatóanyagok vagy gyors útmutatók elvégzése során már létrehozott egy mintaadatbázist, és ezt az oktatóanyagot ugyanazzal IP-címmel rendelkező számítógépen hajtja végre, mint a korábbi oktatóanyagokat, akkor kihagyhatja ezt a lépést, hiszen minden bizonnyal létrehozott már egy kiszolgálószintű tűzfalszabályt.
>

1. Kattintson az **SQL-adatbázisok** elemre a bal oldali menüben, majd kattintson arra az adatbázisra az **SQL-adatbázisok** oldalon, amely számára a tűzfalszabályt konfigurálni kívánja. Megnyílik az adatbázis áttekintőoldala, amelyen látható a teljes kiszolgálónév (például: **mynewserver-20170313.database.windows.net**), valamint a további konfigurálható beállítások.

      ![kiszolgálói tűzfalszabály](./media/sql-database-security-tutorial/server-firewall-rule.png) 

2. Kattintson a **Kiszolgálótűzfal beállítása** lehetőségre az eszköztáron az előző képen látható módon. Megnyílik az SQL Database kiszolgálóhoz tartozó **Tűzfalbeállítások** oldal. 

3. Kattintson az **Ügyfél IP-címének hozzáadása** elemre az eszköztárban a portálhoz csatlakoztatott számítógép IP-címének hozzáadásához, vagy adja meg manuálisan a tűzfalszabályt, majd kattintson a **Mentés** gombra.

      ![kiszolgálótűzfal-szabály beállítása](./media/sql-database-security-tutorial/server-firewall-rule-set.png) 

4. Kattintson az **OK** gombra, majd az **X**-re a **Tűzfalbeállítások** oldal bezárásához.

Mostantól a kiszolgáló bármelyik adatbázisához csatlakozhat a megadott IP-címmel vagy IP-címtartománnyal.

> [!NOTE]
> Az SQL Database az 1433-as porton kommunikál. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat az 1433-as porton keresztül. Ebben az esetben nem tud csatlakozni az Azure SQL Database-kiszolgálóhoz, ha az informatikai részleg nem nyitja meg az 1433-as portot.
>

## <a name="create-a-database-level-firewall-rule-using-ssms"></a>Adatbázisszintű tűzfalszabály létrehozása az SSMS használatával

Az adatbázisszintű tűzfalszabályok segítségével különböző tűzfalbeállításokat hozhat létre ugyanazon logikai kiszolgáló különböző adatbázisai számára, továbbá létrehozhat hordozható, vagyis olyan tűzfalszabályokat is, amelyek a [feladatátvétel](sql-database-geo-replication-overview.md) során követik az adatbázist, nem pedig az SQL Server-kiszolgálón történik a tárolásuk. Az adatbázisszintű tűzfalszabályok kizárólag Transact-SQL-utasításokkal konfigurálhatók, az első kiszolgálószintű tűzfalszabály konfigurálását követően. További információkért lásd: [Kiszolgáló- és adatbázisszintű Azure SQL Database-tűzfalszabályok](sql-database-firewall-configure.md).

Kövesse az alábbi lépéseket egy adatbázis-specifikus tűzfalszabály létrehozásához.

1. Csatlakozzon az adatbázishoz, például az [SQL Server Management Studióval](./sql-database-connect-query-ssms.md).

2. Az Object Explorerben kattintson a jobb gombbal arra az adatbázisra, amely számára tűzfalszabályt kíván hozzáadni, majd kattintson a **New Query** (Új lekérdezés) elemre. Megnyílik egy, az adatbázishoz csatlakoztatott üres lekérdezési ablak.

3. A lekérdezési ablakban módosítsa az IP-címet az Ön nyilvános IP-címére, majd hajtsa végre az alábbi lekérdezést:

    ```sql
    EXECUTE sp_set_database_firewall_rule N'Example DB Rule','0.0.0.4','0.0.0.4';
    ```

4. Az eszköztáron kattintson az **Execute** (Végrehajtás) elemre a tűzfalszabály létrehozásához.

## <a name="view-how-to-connect-an-application-to-your-database-using-a-secure-connection-string"></a>Alkalmazás csatlakoztatása az adatbázishoz biztonságos kapcsolati karakterlánc használatával

Ahhoz, hogy biztonságos, titkosított kapcsolatot létesíthessen az ügyfélalkalmazás és az SQL Database között, a kapcsolati karakterláncot a következőképpen kell konfigurálni:

- titkosított kapcsolatot kérjen; és
- ne bízzon meg a kiszolgálói tanúsítványban. 

A kapcsolat így a Transport Layer Security (TLS) protokoll használatával jön létre, és védettebb lesz a közbeékelődéses támadásokkal szemben. Az SQL Database-adatbázis megfelelően konfigurált kapcsolati karakterláncát az alábbi képernyőképen, az ADO.net esetén bemutatott módon szerezheti be a támogatott ügyfélillesztőkhöz az Azure Portalról. További információ a TLS-ről és a kapcsolatokról: [A TLS megfontolandó szempontjai](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity).

1. Válassza az **SQL-adatbázisok** elemet a bal oldali menüben, majd kattintson az adatbázisra az **SQL-adatbázisok** oldalon.

2. Az adatbázis **Áttekintés** oldalán kattintson az **Adatbázis kapcsolati karakterláncainak megjelenítése** elemre.

3. Tekintse át az **ADO.NET** teljes kapcsolati karakterláncát.

    ![ADO.NET kapcsolati karakterlánc](./media/sql-database-security-tutorial/adonet-connection-string.png)

## <a name="creating-database-users"></a>Adatbázis-felhasználók létrehozása

A felhasználók létrehozása előtt ki kell választania az Azure SQL Database által támogatott kétféle hitelesítési típus egyikét: 

Az **SQL-hitelesítést**, amely olyan bejelentkezések és felhasználók felhasználónevét és jelszavát használja, amelyek egy adott adatbázissal összefüggésben érvényesek egy logikai kiszolgálón. 

Az **Azure Active Directory-alapú hitelesítést**, amely az Azure Active Directory által felügyelt identitásokat használ. 

Ha az SQL Database felé történő hitelesítést az [Azure Active Directoryval](./sql-database-aad-authentication.md) kívánja végezni, akkor a folytatás előtt fel kell tölteni adatokkal az Azure Active Directoryt.

A felhasználók SQL-hitelesítéssel történő létrehozásához kövesse az alábbi lépéseket:

1. Csatlakozzon az adatbázishoz, például az [SQL Server Management Studióval](./sql-database-connect-query-ssms.md) és a kiszolgálói rendszergazdai hitelesítő adatokkal.

2. Az Object Explorerben kattintson a jobb gombbal arra az adatbázisra, amelyhez új felhasználót kíván hozzáadni, majd kattintson a **New Query** (Új lekérdezés) elemre. Megnyílik egy, a kiválasztott adatbázishoz csatlakoztatott üres lekérdezési ablak.

3. A lekérdezési ablakban írja be a következő lekérdezést:

    ```sql
    CREATE USER ApplicationUser WITH PASSWORD = 'YourStrongPassword1';
    ```

4. Az eszköztáron kattintson az **Execute** (Végrehajtás) elemre a felhasználó létrehozásához.

5. Alapértelmezés szerint a felhasználó csatlakozhat az adatbázishoz, de nem jogosult adatok olvasására vagy írására. Ha az újonnan létrehozott felhasználónak ilyen engedélyeket kíván adni, futtassa az alábbi két parancsot egy új lekérdezési ablakban.

    ```sql
    ALTER ROLE db_datareader ADD MEMBER ApplicationUser;
    ALTER ROLE db_datawriter ADD MEMBER ApplicationUser;
    ```

Az ajánlott eljárás az, hogy ezeket a nem rendszergazdai fiókokat adatbázisszinten hozza létre az adatbázishoz történő csatlakozáshoz, kivéve, ha olyan rendszergazdai feladatokat kell végrehajtania, mint az új felhasználók létrehozása. Az Azure Active Directoryval történő hitelesítésről az [Azure Active Directory oktatóanyagában](./sql-database-aad-authentication-configure.md) tájékozódhat.


## <a name="protect-your-data-with-encryption"></a>Adatok védelme titkosítással.

Az Azure SQL Database transzparens adattitkosítási (TDE) funkciója automatikusan titkosítja az inaktív adatokat anélkül, hogy módosítani kellene a titkosított adatbázist elérő alkalmazást. Az újonnan létrehozott adatbázisok esetében a TDE alapértelmezés szerint be van kapcsolva. Ha engedélyezni szeretné a TDE-t az adatbázis számára, illetve ha ellenőrizni kívánja a TDE bekapcsolt állapotát, kövesse az alábbi lépéseket:

1. Válassza az **SQL-adatbázisok** elemet a bal oldali menüben, majd kattintson az adatbázisra az **SQL-adatbázisok** oldalon. 

2. Kattintson a **Transzparens adattitkosítás** elemre a TDE konfigurációs lapjának megnyitásához.

    ![Transzparens adattitkosítás](./media/sql-database-security-tutorial/transparent-data-encryption-enabled.png)

3. Ha szükséges, állítsa BE értékűre az **Adattitkosítás** beállítást, majd kattintson a **Mentés** gombra.

A háttérben elindul a titkosítási folyamat. Az állapot monitorozásához csatlakozzon az SQL Database-adatbázishoz az [SQL Server Management Studióval](./sql-database-connect-query-ssms.md), és kérdezze le a [sys.dm_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql?view=sql-server-2017) nézet encryption_state oszlopát. Ha az állapot 3, akkor az adatbázis titkosított. 

## <a name="enable-sql-database-auditing-if-necessary"></a>Az SQL Database naplózási funkciójának engedélyezése szükség esetén

Az Azure SQL Database naplózási szolgáltatása nyomon követi az adatbázisok eseményeit, és felvezeti ezeket egy naplófájlba, amely a felhasználó Azure Storage-fiókjában található. A naplózás segíthet a jogszabályi megfelelőség fenntartásában és az adatbázison végzett tevékenység megértésében, valamint az esetleges biztonsági problémákat jelző rendellenességek feltárásában. Kövesse az alábbi lépéseket az SQL Database-adatbázis alapértelmezett naplózási szabályzatának létrehozásához:

1. Válassza az **SQL-adatbázisok** elemet a bal oldali menüben, majd kattintson az adatbázisra az **SQL-adatbázisok** oldalon. 

2. A Beállítások panelen válassza a **Naplózás és fenyegetésészlelés** elemet. Vegye figyelembe, hogy a kiszolgálószintű naplózás le van tiltva, és a **View server settings** (Kiszolgálóbeállítások megtekintése) hivatkozás teszi lehetővé a kiszolgálónaplózási beállítások megtekintését vagy módosítását ebből a környezetből.

    ![Auditing (Naplózás) panel](./media/sql-database-security-tutorial/auditing-get-started-settings.png)

3. Ha a kiszolgálószinten megadotthoz képest más naplózási típust (vagy helyet) kíván engedélyezni, állítsa **ON** (Be) értékűre az Auditing (Naplózás) funkciót, az Auditing Type (Naplózási típus) számára pedig a **Blob** lehetőséget válassza. Ha a kiszolgálószintű blobnaplózás engedélyezve van, az adatbázisszinten konfigurált naplózás és a kiszolgálószintű blobnaplózás egyszerre történik.

    ![A naplózás bekapcsolása](./media/sql-database-security-tutorial/auditing-get-started-turn-on.png)

4. Az Audit Logs Storage (Naplótárolás) panel megnyitásához válassza a **Storage Details** (Tároló részletei) lehetőséget. Válassza ki a naplók tárolására szolgáló Azure Storage-fiókot, valamint azt a megőrzési időtartamot, amelynek leteltével a rendszer törli a régi naplókat, majd kattintson alul az **OK** gombra. 

   > [!TIP]
   > A naplózásijelentés-sablonok minél hatékonyabb használatához, használja ugyanazt a tárfiókot az összes naplózott adatbázishoz.
   > 

5. Kattintson a **Save** (Mentés) gombra.

> [!IMPORTANT]
> A naplózott események testreszabásához használhatja a PowerShellt vagy a REST API-t. További információért tekintse meg [az SQL Database naplózási funkcióját](sql-database-auditing.md) ismertető cikket.
>

## <a name="enable-sql-database-threat-detection"></a>Az SQL Database fenyegetésészlelési funkciójának engedélyezése.

A fenyegetésészlelés új biztonsági réteget jelent, amely a rendellenes tevékenységekre adott riasztásokkal teszi lehetővé, hogy az ügyfelek bekövetkezésük pillanatában észlelhessék a vélhető fenyegetéseket, és reagálhassanak azokra. A felhasználók az SQL Database naplózási funkciójának használatával elemezhetik a gyanús eseményeket annak megállapításához, hogy azok hozzáférési kísérlet, illetéktelen behatolás vagy biztonsági rés kihasználása következtében történtek-e meg az adatbázisban. A fenyegetésészlelés biztonsági szakértelem vagy fejlett biztonsági figyelőrendszerek üzemeltetése nélkül is egyszerűvé teszi az adatbázis elleni lehetséges fenyegetések elhárítását.
A fenyegetésészlelés például egyes, az adatbázist érintő rendellenes tevékenységeket észlel, amelyek lehetséges SQL-injektálási kísérleteket jelezhetnek. Az SQL-injektálás az egyik leggyakoribb webalkalmazás-biztonsági probléma az interneten, a használatával adatvezérelt alkalmazásokat támadnak meg. Az alkalmazások biztonsági réseinek kihasználásával a támadók rosszindulatú SQL-utasításokat injektálhatnak az alkalmazás beviteli mezőibe az adatbázisban található adatokkal való visszaéléshez vagy azok módosításához.

1. Lépjen a monitorozni kívánt SQL Database-adatbázis konfigurációs paneljére. A Beállítások panelen válassza a **Naplózás és fenyegetésészlelés** elemet.

    ![Navigációs ablaktábla](./media/sql-database-security-tutorial/auditing-get-started-settings.png)
2. A **Naplózás és fenyegetésészlelés** konfigurációs panelen kapcsolja **BE** a naplózási szolgáltatást, amely megjeleníti a fenyegetésészlelési beállításokat.

3. Kapcsolja **BE** a fenyegetésészlelési szolgáltatást.

4. Adja meg azon e-mail-címek listáját, amelyekre a rendszer rendellenes adatbázis-tevékenységek észlelésekor biztonsági riasztást küld.

5. Az új vagy a frissített naplózási és fenyegetésészlelési szabályzat mentéséhez kattintson a **Mentés** gombra a **Naplózás és fenyegetésészlelés** panelen.

    ![Navigációs ablaktábla](./media/sql-database-security-tutorial/td-turn-on-threat-detection.png)

    Rendellenes adatbázis-tevékenységek észlelésekor a rendszer értesítést küld e-mailben. Az e-mail információkat tartalmaz a gyanús biztonsági eseményről, beleértve a rendellenes tevékenységek jellegét, az adatbázis és a kiszolgáló nevét, valamint az esemény időpontját. Az e-mail ezen kívül ismerteti a lehetséges okokat, illetve a lehetséges adatbázis-fenyegetések kivizsgálására és elhárítására javasolt műveleteket is. A következő lépések azt mutatják be, hogy milyen teendői vannak, ha ilyen e-mailt kap:

    ![Fenyegetésészlelési e-mail](./media/sql-database-threat-detection-get-started/4_td_email.png)

6. Ha az e-mailben szereplő **Azure SQL-napló** hivatkozásra kattint, megnyílik az Azure Portal, ahol megjelennek a gyanús esemény bekövetkezésének időpontja környékén rögzített kapcsolódó naplórekordok.

    ![Naplórekordok](./media/sql-database-threat-detection-get-started/5_td_audit_records.png)

7. A naplórekordokra kattintva további információkat tekinthet meg a gyanús adatbázis-tevékenységekről, így például az SQL-utasításokról, a meghibásodás okáról és az ügyfél IP-címéről.

    ![Rekordok részletei](./media/sql-database-security-tutorial/6_td_audit_record_details.png)

8. Az Auditing Records (Naplózási rekordok) panelen kattintson a **Megnyitás Excelben** lehetőségre egy előre konfigurált Excel-sablon megnyitásához a gyanús esemény bekövetkezésének időpontja környékén rögzített naplók importálásához és részletesebb elemzéséhez.

    > [!NOTE]
    > Az Excel 2010 vagy újabb verziója esetében meg kell adni a Power Query és a **Gyors összevonás** beállítását.

    ![Rekordok megnyitása az Excelben](./media/sql-database-threat-detection-get-started/7_td_audit_records_open_excel.png)

9. A **Gyors összevonás** beállítás konfigurálása – a **POWER QUERY** menüszalagján válassza a **Beállítások** elemet a Beállítások párbeszédpanel megjelenítéséhez. Válassza az Adatvédelem szakaszt, majd válassza a második lehetőséget – „A teljesítmény lehetséges javítása az adatvédelmi szintek figyelmen kívül hagyásával”:

    ![Az Excel Gyors összevonás szolgáltatása](./media/sql-database-threat-detection-get-started/8_td_excel_fast_combine.png)

10. Az SQL-naplók betöltéséhez ellenőrizze, hogy megfelelően állította-e be a Beállítások lap paramétereit, majd válassza ki az „Adatok” menüszalagot, és kattintson „Az összes frissítése” elemre.

    ![Excel-paraméterek](./media/sql-database-threat-detection-get-started/9_td_excel_parameters.png)

11. Az eredmények az **SQL Audit Logs** (SQL-naplók) lapon jelennek meg, ahol részletesebb elemzésnek vetheti alá az észlelt rendellenes tevékenységeket, és csökkentheti a biztonsági események hatását az alkalmazásban.


## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban megtanulta, hogy hogyan fokozhatja mindössze néhány lépés végrehajtásával az adatbázis védelmét a rosszindulatú felhasználókkal és a jogosulatlan hozzáféréssel szemben.  Megismerte, hogyan végezheti el az alábbi műveleteket: 

> [!div class="checklist"]
> * Tűzfalszabályok beállítása a kiszolgáló és/vagy az adatbázis számára.
> * Csatlakozás az adatbázishoz biztonságos kapcsolati karakterlánc használatával.
> * Felhasználói hozzáférés kezelése.
> * Adatok védelme titkosítással.
> * Az SQL Database naplózási funkciójának engedélyezése.
> * Az SQL Database fenyegetésészlelési funkciójának engedélyezése.

A következő oktatóanyag egy földrajzilag elosztott adatbázis implementálását mutatja be.

> [!div class="nextstepaction"]
>[Földrajzilag elosztott adatbázis implementálása](sql-database-implement-geo-distributed-database.md)

