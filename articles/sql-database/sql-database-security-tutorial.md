---
title: "Az Azure SQL-adatbázis védelme |} Microsoft Docs"
description: "További tudnivalók a módszerek és szolgáltatások az Azure SQL-adatbázis védelme."
services: sql-database
documentationcenter: 
author: DRediske
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,security
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 06/28/2017
ms.author: daredis
ms.openlocfilehash: 90c03f1538197e1cd1c90165417a4ec74c9c5961
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2017
---
# <a name="secure-your-azure-sql-database"></a>Az Azure SQL-adatbázis védelme

Az SQL Database úgy biztosítja az adatai védelmét, hogy korlátozza az adatbázishoz való hozzáférést tűzfalszabályokkal, a felhasználókat az azonosságuk igazolására kényszerítő hitelesítési mechanizmusokkal, engedélyezéssel az adatokhoz szerepalapú tagságok és engedélyek útján, valamint sorszintű biztonsággal és dinamikus adatmaszkolással.

Az adatbázis rosszindulatú felhasználók vagy néhány egyszerű lépésben jogosulatlan hozzáférés elleni védelme is javítható. Ebben az oktatóanyagban elsajátíthatja, hogy: 

> [!div class="checklist"]
> * A kiszolgáló Azure-portálon kiszolgálószintű tűzfal-szabályok beállítása
> * Az adatbázisban az SSMS használatával vonatkozó adatbázis-szintű tűzfalszabályok beállítása
> * Kapcsolódás saját adatbázishoz, a biztonságos kapcsolati karakterláncok használata
> * Felhasználói hozzáférés kezelése
> * A titkosított adatok védelme
> * SQL-adatbázis naplózásának engedélyezése
> * SQL-adatbázis fenyegetésészlelés engedélyezéséhez

Ha nem rendelkezik Azure-előfizetéssel, [ingyenes fiók létrehozását](https://azure.microsoft.com/free/) megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez, győződjön meg arról, hogy a következő:

- A legújabb verziójának telepítése [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS). 
- Telepített Microsoft Excel
- Létrehozott egy Azure SQL server és adatbázis - lásd [Azure SQL-adatbázis létrehozása az Azure portálon](sql-database-get-started-portal.md), [az Azure parancssori felület használatával egyetlen Azure SQL-adatbázis létrehozása](sql-database-get-started-cli.md), és [PowerShell használatával egyetlen Azure SQL-adatbázis létrehozása](sql-database-get-started-powershell.md). 

## <a name="log-in-to-the-azure-portal"></a>Jelentkezzen be az Azure portálra.

Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Kiszolgálószintű tűzfalszabály létrehozása az Azure Portalon

SQL-adatbázisok védelmének tűzfal az Azure-ban. Alapértelmezés szerint az összes kapcsolat a kiszolgáló és az adatbázisok belül a kiszolgáló visszautasítja kivételével az egyéb Azure-szolgáltatásokhoz érkező kapcsolatokat. További információkért lásd: [Kiszolgáló- és adatbázisszintű Azure SQL Database-tűzfalszabályok](sql-database-firewall-configure.md).

A legbiztonságosabb konfiguráció is, hogy "Azure-szolgáltatásokhoz való hozzáférés engedélyezése" off. Ha szeretne kapcsolódni az adatbázishoz egy Azure virtuális vagy felhőalapú szolgáltatásból, akkor hozzon létre egy [foglalt IP-cím](../virtual-network/virtual-networks-reserved-public-ip.md) és csak a fenntartott IP cím hozzáférés engedélyezése a tűzfalon keresztül. 

Hajtsa végre az alábbi lépéseket követve létrehozhat egy [SQL-adatbázis kiszolgálószintű tűzfalszabály](sql-database-firewall-configure.md) a kiszolgáló engedélyezi a kapcsolódást egy adott IP-címről. 

> [!NOTE]
> Ha hozott létre a mintaadatbázis az Azure-ban egy előző oktatóanyagok vagy quickstarts, és ebben az oktatóanyagban végzik a azonos IP-cím, mint amikor, telefonon keresztül ezen oktatóprogram egy olyan számítógépre, ezt a lépést kihagyhatja, a rendszer már létrehozott egy kiszolgálószintű tűzfalszabályt.
>

1. Kattintson a **SQL-adatbázisok** a bal oldali menüből és szeretné beállítani a tűzfalat az adatbázis-szabályok a kattintson a **SQL-adatbázisok** lap. Áttekintő lapjára jut az adatbázis nyit meg, hogy bemutatja a kiszolgáló teljesen minősített nevét (például **mynewserver-20170313.database.windows.net**) és további konfigurációs lehetőségeket.

      ![kiszolgálói tűzfalszabály](./media/sql-database-security-tutorial/server-firewall-rule.png) 

2. Kattintson a **Kiszolgálótűzfal beállítása** lehetőségre az eszköztáron az előző képen látható módon. Megnyílik az SQL Database kiszolgálóhoz tartozó **Tűzfalbeállítások** oldal. 

3. Kattintson a **ügyfél IP-cím hozzáadása** adja hozzá a nyilvános IP-cím, a számítógép csatlakozik a portál vagy manuálisan adja meg a tűzfalszabályt, és kattintson az eszköztáron **mentése**.

      ![kiszolgálótűzfal-szabály beállítása](./media/sql-database-security-tutorial/server-firewall-rule-set.png) 

4. Kattintson az **OK** gombra, majd az **X**-re a **Tűzfalbeállítások** oldal bezárásához.

Minden adatbázis a kiszolgálón, a megadott IP-címmel vagy IP-címtartományt is csatlakozhat.

> [!NOTE]
> Az SQL Database az 1433-as porton kommunikál. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat az 1433-as porton keresztül. Ebben az esetben nem tud csatlakozni az Azure SQL Database-kiszolgálóhoz, ha az informatikai részleg nem nyitja meg az 1433-as portot.
>

## <a name="create-a-database-level-firewall-rule-using-ssms"></a>Hozzon létre egy adatbázis-szintű tűzfalszabályt SSMS használatával

Adatbázis-szintű tűzfalszabályok lehetővé teszik a különböző tűzfal beállításainak a különböző adatbázisokhoz belül az azonos logikai kiszolgáló létrehozásához, és létre tűzfalszabályokat, amelyek hordozható – ami azt jelenti, hogy azok kövesse az adatbázis során egy [feladatátvételi](sql-database-geo-replication-overview.md) ahelyett, hogy az SQL-kiszolgálón tárolja. Adatbázis-szintű tűzfalszabályok csak akkor lehet Transact-SQL-utasítások segítségével konfigurált csak az első kiszolgálószintű tűzfalszabály konfigurálása után. További információkért lásd: [Kiszolgáló- és adatbázisszintű Azure SQL Database-tűzfalszabályok](sql-database-firewall-configure.md).

A következő lépéseket egy adatbázis-specifikus tűzfalszabály létrehozásához.

1. Kapcsolódás saját adatbázishoz, például a használatával [SQL Server Management Studio](./sql-database-connect-query-ssms.md).

2. Az Object Explorerben kattintson a jobb gombbal az adatbázishoz, vegye fel egy tűzfalszabályt, és kattintson a kívánt **új lekérdezés**. Megnyílik egy, az adatbázishoz csatlakoztatott üres lekérdezési ablak.

3. A lekérdezési ablakban módosítani a nyilvános IP-címet az IP-címet, és majd végre a következő lekérdezés:

    ```sql
    EXECUTE sp_set_database_firewall_rule N'Example DB Rule','0.0.0.4','0.0.0.4';
    ```

4. Kattintson az eszköztár **Execute** tűzfalszabály létrehozásához.

## <a name="view-how-to-connect-an-application-to-your-database-using-a-secure-connection-string"></a>Kapcsolódás az adatbázishoz használja a biztonságos kapcsolati karakterláncok alkalmazás megtekintése

Ahhoz, hogy egy ügyfél alkalmazás és az SQL-adatbázis közötti biztonságos, titkosított kapcsolatot, úgy kell konfigurálni, hogy rendelkezik a kapcsolati karakterlánc:

- Egy titkosított kapcsolati kérelmek és
- A kiszolgálói tanúsítvány nem megbízható. 

Ez a használata a Transport Layer Security (TLS) kapcsolatot létesít, és csökkenti-átjárójának kockázatát. Ezt úgy szerezheti be megfelelően konfigurált kapcsolati karakterláncokat az SQL-adatbázis, a támogatott ügyfél illesztőprogramok Azure-portálról látható az ADO.net ezen a képernyőfelvételen látható módon.

1. Válassza ki **SQL-adatbázisok** a bal oldali menüből, majd kattintson az adatbázis a a **SQL-adatbázisok** lap.

2. Az a **áttekintése** az adatbázis lapján kattintson **adatbázis-kapcsolati karakterláncok megjelenítése**.

3. Tekintse át az **ADO.NET** teljes kapcsolati karakterláncát.

    ![ADO.NET kapcsolati karakterlánc](./media/sql-database-security-tutorial/adonet-connection-string.png)

## <a name="creating-database-users"></a>Adatbázis-felhasználók létrehozásával

Mielőtt létrehozna azokat a felhasználókat, először közül kell választania az Azure SQL Database által támogatott két hitelesítési típusok egyikét: 

**SQL-hitelesítés**, felhasználónevet és jelszót használó bejelentkezéseket és a felhasználók, akik csak logikai kiszolgáló az adott adatbázis környezetében érvényes. 

**Az Azure Active Directory-hitelesítéssel**, amely Azure Active Directory által kezelt identitások használja. 

Ha a használni kívánt [Azure Active Directory](./sql-database-aad-authentication.md) hitelesítése SQL-adatbázis, a feltöltött Azure Active Directory léteznie kell a folytatás előtt.

Kövesse az alábbi lépéseket egy SQL-hitelesítést használó felhasználó létrehozásához:

1. Kapcsolódás saját adatbázishoz, például a használatával [SQL Server Management Studio](./sql-database-connect-query-ssms.md) server rendszergazdai hitelesítő adataival.

2. Az Object Explorerben kattintson a jobb gombbal az új felhasználó hozzáadása, és kattintson a kívánt adatbázis **új lekérdezés**. Megjelenik egy üres lekérdezési ablak, amely a kijelölt adatbázishoz kapcsolódik.

3. A lekérdezési ablakban írja be a következő lekérdezést:

    ```sql
    CREATE USER ApplicationUser WITH PASSWORD = 'YourStrongPassword1';
    ```

4. Kattintson az eszköztár **Execute** a felhasználó létrehozásához.

5. Alapértelmezés szerint a felhasználó képes kapcsolódni az adatbázishoz, de nem jogosult az olvasni vagy írni az adatok. Ezek a hozzáférési jogot az újonnan létrehozott felhasználó, egy új lekérdezési ablak az alábbi két parancs hajtható végre

    ```sql
    ALTER ROLE db_datareader ADD MEMBER ApplicationUser;
    ALTER ROLE db_datawriter ADD MEMBER ApplicationUser;
    ```

Akkor ajánlott ezeket a nem rendszergazdai fiókokat létrehozni az adatbázis szintjén nem hajtható végre felügyeleti feladatokat, például új felhasználók létrehozása kívánja az adatbázishoz való kapcsolódáshoz. Tekintse át a [Azure Active Directory-oktatóanyag](./sql-database-aad-authentication-configure.md) történő hitelesítéséhez az Azure Active Directory használatával.


## <a name="protect-your-data-with-encryption"></a>A titkosított adatok védelme

Az Azure SQL Database átlátható adattitkosítás (TDE) az adatok aktívan, automatikusan titkosítja az alkalmazáshoz való hozzáférés a titkosított adatbázis módosítása nélkül. Az újonnan létrehozott adatbázisok esetén TDE alapértelmezés szerint van bekapcsolva. Az adatbázis TDE engedélyezéséhez, vagy győződjön meg arról, hogy TDE, kövesse az alábbi lépéseket:

1. Válassza ki **SQL-adatbázisok** a bal oldali menüből, majd kattintson az adatbázis a a **SQL-adatbázisok** lap. 

2. Kattintson a **átlátható adattitkosítás** TDE a lap megnyitásához.

    ![Transzparens adattitkosítás](./media/sql-database-security-tutorial/transparent-data-encryption-enabled.png)

3. Ha szükséges, állítsa be **adattitkosítás** ON értékre állítása kattintson **mentése**.

A titkosítási folyamat elindul a háttérben. SQL-adatbázishoz való kapcsolódás által kísérheti [SQL Server Management Studio](./sql-database-connect-query-ssms.md) encryption_state oszlopa lekérdezésével a `sys.dm_database_encryption_keys` nézet.

## <a name="enable-sql-database-auditing-if-necessary"></a>SQL-adatbázis naplózásának engedélyezése, ha szükséges

Adatbázis-események, mind az írás őket naplózási jelentkezzen be az Azure Storage-fiókot az Azure SQL Database Auditing követi nyomon. Naplózás segít törvényi megfelelőség fenntartásában, ismerje meg adatbázis-tevékenység, és betekintést az eltérések és rendellenességek feltárását, jelezheti a potenciális biztonsági problémát. Hozzon létre egy naplórendet az SQL-adatbázis alapértelmezett lépések végrehajtásával:

1. Válassza ki **SQL-adatbázisok** a bal oldali menüből, majd kattintson az adatbázis a a **SQL-adatbázisok** lap. 

2. A beállítások panelen válassza ki a **naplózás és Fenyegetésészlelés**. Figyelje meg, hogy kiszolgálószintű naplózás letiltott állapotában és, hogy egy **beállításainak megtekintéséhez** hivatkozás, amely lehetővé teszi megtekintheti vagy módosíthatja a kiszolgáló naplózási beállításainak az ebben a környezetben.

    ![Naplózás panel](./media/sql-database-security-tutorial/auditing-get-started-settings.png)

3. Ha egy naplózási típus (vagy a hely?) engedélyezni szeretné a kiszolgáló szintjén egyezik az megadott, kapcsolja **ON** naplózás, és válassza ki a **Blob** naplózás típusát. Ha a kiszolgáló Blobnaplózási funkció engedélyezve van, a konfigurált adatbázis-ellenőrzéshez és a kiszolgáló Blob naplózási jelen.

    ![A naplózás bekapcsolása](./media/sql-database-security-tutorial/auditing-get-started-turn-on.png)

4. Válassza ki **tárolási részletek** naplózási naplók tárolási panel megnyitásához. Válassza ki az Azure storage-fiók, ahol a naplókat a rendszer menti, és a megőrzési időtartam, amely után a régi naplókat törlődni fog, majd kattintson a **OK** alján. 

   > [!TIP]
   > Ugyanazt a tárfiókot az összes naplózott adatbázis segítségével a legtöbbet hozhatja ki a naplózási jelentések sablonok.
   > 

5. Kattintson a **Save** (Mentés) gombra.

> [!IMPORTANT]
> Ha szeretné testre szabni a naplózott eseményeket, ehhez PowerShell vagy a REST API - lásd [SQL-adatbázis naplózásának](sql-database-auditing.md) további részleteket.
>

## <a name="enable-sql-database-threat-detection"></a>SQL-adatbázis fenyegetésészlelés engedélyezéséhez

A Fenyegetésészlelés biztonsági, amelyek segítségével a felhasználók észlelése és azok bekövetkezésekor, adja meg a biztonsági riasztások a rendellenes tevékenységek reagáljon a lehetséges veszélyforrásokra egy új réteget biztosít. Felhasználók felfedezheti az SQL Database Auditing segítségével határozza meg, ha azok hozzáférést, megsértik vagy az adatbázis adatai kihasználásához kísérlet eredménye gyanús eseményeket. A Fenyegetésészlelés egyszerűen potenciális fenyegetések ellen az adatbázisba történő szakértői biztonsági vagy speciális biztonsági rendszerek figyelése kezelése nélkül.
A Fenyegetésészlelés például bizonyos adatbázist érintő rendellenes tevékenységeket utaló esetleges SQL injektálási kísérletek begyűjtésére észleli. SQL-injektálás az egyik a közös webes alkalmazás biztonsági problémák elleni támadásra adatvezérelt alkalmazások, az interneten. A támadók rosszindulatú SQL-utasítások mezőkbe alkalmazás bejegyzést, megsértése vagy az adatbázis adatai módosításához, szúrjon alkalmazás biztonsági előnyeit.

1. Nyissa meg a figyelni kívánt SQL-adatbázis konfigurációs panelt. A beállítások panelen válassza ki a **naplózás és Fenyegetésészlelés**.

    ![Navigációs ablaktábla](./media/sql-database-security-tutorial/auditing-get-started-settings.png)
2. Az a **naplózás és Fenyegetésészlelés** konfigurációs panelen kapcsolja **ON** naplózás, amely megjeleníti a fenyegetés észlelési beállítások.

3. Kapcsolja be **ON** veszélyforrások detektálása.

4. Konfigurálhatja az e-mailek, amelyek megkapják a adatbázist érintő rendellenes tevékenységeket észlelésekor biztonsági riasztások listáját.

5. Kattintson a **mentése** a a **naplózási & Threat detection** panelt, és mentse az új vagy frissített naplózás és a fenyegetés szabályzat.

    ![Navigációs ablaktábla](./media/sql-database-security-tutorial/td-turn-on-threat-detection.png)

    Adatbázist érintő rendellenes tevékenységeket a rendszer észleli, ha adatbázist érintő rendellenes tevékenységeket a észlelésekor e-mailben értesítést fog kapni. Az e-mailt a gyanús biztonsági esemény, például a rendellenes tevékenységek, a adatbázis neve, a kiszolgáló nevét és az esemény időpontja jellege tájékoztatást fogunk adni. Emellett a lehetséges okok tájékoztatást fogunk adni, és javasolt műveletek vizsgálatához és az adatbázis következő potenciális fenyegetések csökkentésében. A következő lépések végigvezetik a keresztül mi a teendő, hogy mailt kell kapnia ilyen:

    ![Fenyegetések észlelése e-mail](./media/sql-database-threat-detection-get-started/4_td_email.png)

6. Az e-mailt, kattintson a a **Azure SQL-naplózás napló** hivatkozás, amely az Azure-portálon elindul, és a megfelelő naplózási bejegyzések a gyanús esemény környékén megjelenítése.

    ![Auditálási rekordok](./media/sql-database-threat-detection-get-started/5_td_audit_records.png)

7. Kattintson az auditálási rekordok további részleteket az adatbázis gyanús tevékenységek, például SQL-utasításban, a hiba okát, és az ügyfél IP.

    ![Rekord részletei](./media/sql-database-security-tutorial/6_td_audit_record_details.png)

8. A naplózási bejegyzések paneljén kattintson **az Excelben** nyissa meg az előre konfigurált excel-sablon importálása és futtatása a napló a gyanús esemény környékén mélyebb elemzése.

    > [!NOTE]
    > Az Excel 2010 vagy újabb, a Power Query és a **gyors összevonás** beállításra akkor szükség.

    ![Nyitott rekordokat az Excel programban](./media/sql-database-threat-detection-get-started/7_td_audit_records_open_excel.png)

9. Konfigurálhatja a **gyors összevonás** beállítás - a a **POWER QUERY** menüszalag lapon jelölje be **beállítások** a beállítások párbeszédpanel megjelenítéséhez. Válassza ki az adatvédelmi szakaszt, és válassza a második lehetőség - "És a teljesítmény lehetséges javítása az adatvédelmi szintek figyelmen kívül":

    ![Gyors Excel egyesítése](./media/sql-database-threat-detection-get-started/8_td_excel_fast_combine.png)

10. A betöltés SQL naplókat, ügyeljen arra, hogy a beállítások lapon helyesen van beállítva, és válassza ki a "Data" szalagon, és kattintson a "Az összes frissítés" gombra a paraméterek.

    ![Excel-paraméterek](./media/sql-database-threat-detection-get-started/9_td_excel_parameters.png)

11. Az eredmények jelennek meg a **SQL naplók** lap, amely lehetővé teszi a rendellenes tevékenységek talált mélyebb elemzésének futtatja, és csökkenteni a hatását a biztonsági események az alkalmazásban.


## <a name="next-steps"></a>Következő lépések
Ebben az oktatóprogramban megismerte az adatbázis rosszindulatú felhasználók vagy csak néhány egyszerű lépésben jogosulatlan hozzáférés elleni védelméhez.  Megismerte, hogyan végezheti el az alábbi műveleteket: 

> [!div class="checklist"]
> * A kiszolgálói és/vagy adatbázis vonatkozó tűzfalszabályok beállítása
> * Kapcsolódás saját adatbázishoz, a biztonságos kapcsolati karakterláncok használata
> * Felhasználói hozzáférés kezelése
> * A titkosított adatok védelme
> * SQL-adatbázis naplózásának engedélyezése
> * SQL-adatbázis fenyegetésészlelés engedélyezéséhez

A következő oktatóanyag áttekintésével megismerheti, hogyan egy földrajzilag elosztott adatbázist végrehajtásához továbblépés.

> [!div class="nextstepaction"]
>[Földrajzilag elosztott adatbázis implementálása](sql-database-implement-geo-distributed-database.md)

