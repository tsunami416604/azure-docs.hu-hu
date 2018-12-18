---
title: Egy Azure SQL Database-adatbázis védelme |} A Microsoft Docs
description: Ismerje meg a módszerek és szolgáltatások egy Azure SQL Database-adatbázis védelmét.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
manager: craigg
ms.date: 12/17/2018
ms.openlocfilehash: aea95c245b86905b7bef0a35ffaa6c5e00567111
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2018
ms.locfileid: "53558634"
---
# <a name="tutorial-secure-a-single-database-in-azure-sql-database"></a>Oktatóanyag: Egy Azure SQL Database-adatbázis védelme

Az SQL Database védi az adatokat egy egyetlen Azure SQL Database által:

- Az adatbázishoz való hozzáférés korlátozása tűzfalszabályok használatával
- Identitást igénylő hitelesítési mechanizmusok használata
- Az adathozzáférés engedélyezése szerepköralapú tagságok és engedélyek segítségével
- Sorszintű biztonság
- Dinamikus adatmaszkolás

Az SQL Database ezenfelül kifinomult monitorozást, naplózást és fenyegetésészlelést biztosít.

> [!IMPORTANT]
> Az Azure SQL Database felügyelt példány az adatbázis hálózati biztonsági szabályok és a privát végpontok segítségével védi. További információkért lásd: [Azure SQL Database felügyelt példányába](sql-database-managed-instance-index.yml) és [Azure SQL Database felügyelt példány kapcsolati architektúra](sql-database-managed-instance-connectivity-architecture.md).

Mindössze néhány lépés végrehajtásával fokozhatja az adatbázis védelmét a rosszindulatú felhasználókkal és a jogosulatlan hozzáféréssel szemben. Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> - Kiszolgálószintű tűzfalszabályok beállítása a kiszolgáló számára az Azure Portalon.
> - Adatbázisszintű tűzfalszabályok beállítása az adatbázis számára SSMS használatával.
> - Csatlakozás az adatbázishoz biztonságos kapcsolati sztring használatával.
> - Az Azure SQL Azure Active Directory-rendszergazda konfigurálása
> - Felhasználói hozzáférés kezelése.
> - Adatok védelme titkosítással.
> - Az SQL Database naplózási funkciójának engedélyezése.
> - Az SQL Database fenyegetésészlelési funkciójának engedélyezése.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez győződjön meg arról, hogy rendelkezik az alábbiakkal:

- Telepítette az [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) legújabb verzióját.
- Telepítette a Microsoft Excelt.
- Létrehozott egy Azure SQL Server-kiszolgálót és Database-adatbázist – erről az [Azure SQL Database létrehozása az Azure Portalon](sql-database-get-started-portal.md), az [Önálló Azure SQL-adatbázis létrehozása az Azure CLI használatával](sql-database-cli-samples.md) és az [Önálló Azure SQL-adatbázis létrehozása a PowerShell használatával](sql-database-powershell-samples.md) című cikkben talál további információt.

> [!NOTE]
> Ez az oktatóanyag feltételezi, hogy rendelkezik már konfigurált Azure Active Directoryban, vagy felügyelt, hogy használ-e a kezdeti Azure Active Directory tartományhoz. Számos célra az Azure Active Directory konfigurálásával kapcsolatos további információkért lásd: [a helyszíni identitások integrálása az Azure Active Directory](../active-directory/hybrid/whatis-hybrid-identity.md), [saját tartománynév hozzáadása az Azure ad-ben](../active-directory/active-directory-domains-add-azure-portal.md), [Microsoft Azure mostantól támogatja a Windows Server Active Directoryval való összevonás](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [az Azure AD-címtár felügyelete](../active-directory/fundamentals/active-directory-administer.md), [Windows PowerShell-lelazAzureADkezelése](/powershell/azure/overview?view=azureadps-2.0), és [hibrid identitás – szükséges portok és protokollok](../active-directory/hybrid/reference-connect-ports.md).

## <a name="log-in-to-the-azure-portal"></a>Bejelentkezés az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Kiszolgálószintű tűzfalszabály létrehozása az Azure Portalon

Az SQL Database-adatbázisokat tűzfal védi az Azure-ban. A rendszer alapértelmezés szerint elutasítja a kiszolgálóra és a kiszolgálón lévő adatbázisokra irányuló összes kapcsolatot, a más Azure-szolgáltatások irányából érkező kapcsolatokat kivéve. További információkért lásd: [Kiszolgáló- és adatbázisszintű Azure SQL Database-tűzfalszabályok](sql-database-firewall-configure.md).

A legbiztonságosabb konfigurációt az „Azure-szolgáltatásokhoz való hozzáférés engedélyezése” beállítás kikapcsolása biztosítja. Ha az adatbázishoz Azure-beli virtuális gépről vagy felhőszolgáltatásból kíván csatlakozni, létre kell hoznia egy [fenntartott IP-címet (klasszikus üzembe helyezés)](../virtual-network/virtual-networks-reserved-public-ip.md), és a tűzfalon keresztüli hozzáférést csak ezen fenntartott IP-cím számára tegye lehetővé. Ha a [Resource Manager](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)-alapú üzemi modellt használja, a rendszer egy dedikált nyilvános IP-címet rendel az erőforráshoz, és ezt az IP-címet engedélyeznie kell a tűzfalon.

Kövesse az alábbi lépéseket, és hozzon létre egy [SQL Database kiszolgálószintű tűzfalszabályt](sql-database-firewall-configure.md) a kiszolgáló számára az adott IP-címről történő kapcsolódás engedélyezéséhez.

> [!NOTE]
> Ha az előző oktatóanyagok vagy gyors útmutatók elvégzése során már létrehozott egy mintaadatbázist, és ezt az oktatóanyagot ugyanazzal IP-címmel rendelkező számítógépen hajtja végre, mint a korábbi oktatóanyagokat, akkor kihagyhatja ezt a lépést, hiszen minden bizonnyal létrehozott már egy kiszolgálószintű tűzfalszabályt.

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

Adatbázisszintű tűzfalszabályok lehetővé teszik a különböző tűzfalbeállításokat az egyazon logikai kiszolgálón található különböző adatbázisok létrehozása és a tűzfalszabályokat, amelyek a hordozható – ami azt jelenti, hogy egy feladatátvétel során követik az adatbázist ahelyett, hogy a tárolásuk az SQL-kiszolgálón. Az adatbázisszintű tűzfalszabályok kizárólag Transact-SQL-utasításokkal konfigurálhatók, az első kiszolgálószintű tűzfalszabály konfigurálását követően. További információkért lásd: [Kiszolgáló- és adatbázisszintű Azure SQL Database-tűzfalszabályok](sql-database-firewall-configure.md).

Kövesse az alábbi lépéseket egy adatbázis-specifikus tűzfalszabály létrehozásához.

1. Csatlakozzon az adatbázishoz, például az [SQL Server Management Studióval](./sql-database-connect-query-ssms.md).

2. Az Object Explorerben kattintson a jobb gombbal arra az adatbázisra, amely számára tűzfalszabályt kíván hozzáadni, majd kattintson a **New Query** (Új lekérdezés) elemre. Megnyílik egy, az adatbázishoz csatlakoztatott üres lekérdezési ablak.

3. A lekérdezési ablakban módosítsa az IP-címet az Ön nyilvános IP-címére, majd hajtsa végre az alábbi lekérdezést:

    ```sql
    EXECUTE sp_set_database_firewall_rule N'Example DB Rule','0.0.0.4','0.0.0.4';
    ```

4. Az eszköztáron kattintson az **Execute** (Végrehajtás) elemre a tűzfalszabály létrehozásához.

## <a name="view-how-to-connect-an-application-to-your-database-using-a-secure-connection-string"></a>Alkalmazás csatlakoztatása az adatbázishoz biztonságos kapcsolati sztring használatával

Ahhoz, hogy biztonságos, titkosított kapcsolatot létesíthessen az ügyfélalkalmazás és az SQL Database között, a kapcsolati sztringet a következőképpen kell konfigurálni:

- titkosított kapcsolatot kérjen; és
- ne bízzon meg a kiszolgálói tanúsítványban.

A kapcsolat így a Transport Layer Security (TLS) protokoll használatával jön létre, és védettebb lesz a közbeékelődéses támadásokkal szemben. Az SQL Database-adatbázis megfelelően konfigurált kapcsolati sztringjét az alábbi képernyőképen, az ADO.net esetén bemutatott módon szerezheti be a támogatott ügyfélillesztőkhöz az Azure Portalról. További információ a TLS-ről és a kapcsolatokról: [A TLS megfontolandó szempontjai](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity).

1. Válassza az **SQL-adatbázisok** elemet a bal oldali menüben, majd kattintson az adatbázisra az **SQL-adatbázisok** oldalon.

2. Az adatbázis **Áttekintés** oldalán kattintson az **Adatbázis kapcsolati sztringjeinek megjelenítése** elemre.

3. Tekintse át az **ADO.NET** teljes kapcsolati sztringjét.

    ![ADO.NET kapcsolati sztring](./media/sql-database-security-tutorial/adonet-connection-string.png)

## <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server"></a>Az Azure Active Directory-rendszergazda, az Azure SQL Database-kiszolgáló üzembe helyezése

Üzembe helyezhető az Azure Active Directory-rendszergazda, az Azure SQL Serverhez az Azure Portalon.

1. Az a [az Azure portal](https://portal.azure.com/), a jobb felső sarokban válassza a legördülő lista lehetővé aktív címtárak listájának létesített kapcsolatot. Válassza ki a megfelelő Active Directory, az alapértelmezett Azure ad-ben. Ebben a lépésben az Azure SQL server gondoskodik róla, hogy, hogy ugyanahhoz az előfizetéshez is szolgál az előfizetéshez tartozó Active Directory hivatkozásokat tartalmaz az Azure AD és az SQL Server. (Az Azure SQL-kiszolgáló is lehet futtató, Azure SQL Database vagy Azure SQL Data Warehouse.)

    ![Válasszon ad](./media/sql-database-aad-authentication/8choose-ad.png)

2. A **SQL Server** lapon jelölje be **Active Directory-rendszergazda**, és az a **Active Directory-rendszergazda** lapon jelölje be **rendszergazda beállítása**.  ![az active directory kiválasztása](./media/sql-database-aad-authentication/select-active-directory.png)  

   > [!IMPORTANT]
   > Meg kell lennie a "Vállalati rendszergazda" vagy "Globális rendszergazda" Ez a feladat végrehajtásához.

3. Az a **rendszergazda hozzáadása** lapon, keresse meg a felhasználó, válassza ki a felhasználó vagy csoport rendszergazdája lesz, és válassza **kiválasztása**. (Az Active Directory-rendszergazda lap megjeleníti az összes tag és Active Directory-csoportokat. Felhasználók vagy csoportok szürkén jelennek meg, nem választható, mert ez nem támogatott az Azure AD-rendszergazdaként. (A támogatott rendszergazdák listájának megtekintéséhez a **az Azure AD-funkciók és korlátozások** szakaszában [használata az Azure Active Directory-hitelesítés az SQL Database vagy az SQL Data Warehouse-hitelesítéshez](sql-database-aad-authentication.md).) Szerepköralapú hozzáférés-vezérlés (RBAC) csak azokra a portálon, és az SQL Server nem propagálja.
    ![Válassza ki a rendszergazda](./media/sql-database-aad-authentication/select-admin.png)  

4. Felső részén a **Active Directory-rendszergazda** lapon jelölje be **mentése**.
    ![rendszergazdai mentése](./media/sql-database-aad-authentication/save-admin.png)

A változó a rendszergazda a folyamat eltarthat néhány percig. Az új rendszergazda megjelenik a **Active Directory-rendszergazda** mezőbe.

   > [!NOTE]
   > Az Azure AD-rendszergazda beállításakor az új felügyeleti name (felhasználó vagy csoport) nem már megtalálható a virtuális master adatbázishoz az SQL Server hitelesítési felhasználóként. Ha van ilyen, az Azure AD felügyeleti telepítés sikertelen lesz; visszaállítása a létrehozása, és amely azt jelzi, hogy az ilyen rendszergazdai (név) már létezik. Például egy SQL Server authentication felhasználóhoz nem része az Azure ad-ben, mivel minden annak érdekében, hogy csatlakozzon a kiszolgálóhoz az Azure AD-hitelesítés használatával sikertelen lesz.

## <a name="creating-database-users"></a>Adatbázis-felhasználók létrehozása

A felhasználók létrehozása előtt ki kell választania az Azure SQL Database által támogatott kétféle hitelesítési típus egyikét:

Az **SQL-hitelesítést**, amely olyan bejelentkezések és felhasználók felhasználónevét és jelszavát használja, amelyek egy adott adatbázissal összefüggésben érvényesek egy logikai kiszolgálón.

Az **Azure Active Directory-alapú hitelesítést**, amely az Azure Active Directory által felügyelt identitásokat használ.

### <a name="create-a-user-using-sql-authentication"></a>Hozzon létre egy SQL-hitelesítést használó felhasználó

Kövesse az alábbi lépéseket követve létrehozhat egy SQL-hitelesítést használó felhasználó:

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

### <a name="create-a-user-using-azure-active-directory-authentication"></a>Hozzon létre egy Azure Active Directory-hitelesítést használó felhasználó

Az Azure Active Directory-hitelesítés szükséges, tartalmazottadatbázis-felhasználókat létrehozni az adatbázis-felhasználók. Egy Azure AD identity alapuló tartalmazottadatbázis-felhasználó egy adatbázis-felhasználót, amely nem rendelkezik bejelentkezési adatokat a master adatbázisban, és amely leképezi a az adatbázishoz társított Azure AD-címtárat az identitást. Az Azure AD identity lehet egyedi felhasználói fiók vagy csoport. Tartalmazottadatbázis-felhasználókkal kapcsolatos további információkért lásd: [tartalmazott adatbázis-felhasználók – így az adatbázis hordozható](https://msdn.microsoft.com/library/ff929188.aspx).

> [!NOTE]
> Adatbázis-felhasználók (a rendszergazdák) kivételével nem hozhatók létre az Azure portal használatával. RBAC-szerepkörök nem vonatkoznak az SQL Server, SQL Database vagy az SQL Data warehouse-bA. Azure RBAC-szerepkörök kezeléséhez az Azure-erőforrások használatban vannak, és adatbázis-engedélyek nem vonatkoznak. Ha például a **SQL Server Közreműködője** szerepkör nem biztosít hozzáférést az SQL Database vagy az SQL Data warehouse-ba való csatlakozáshoz. A hozzáférési engedélyt közvetlenül az adatbázist a Transact-SQL-utasítások használatával.
> [!WARNING]
> Különleges karaktereket, például kettőspont `:` vagy és szimbólumot `&` való használata nem támogatott a T-SQL-CREATE LOGIN és a CREATE USER utasítást felhasználóneveket belefoglalva.

1. Az Azure SQL Serverhez az Azure Active Directory-fiókhoz való csatlakozás legalább a **bármely felhasználó ALTER** engedéllyel.
2. Az Object Explorerben kattintson a jobb gombbal arra az adatbázisra, amelyhez új felhasználót kíván hozzáadni, majd kattintson a **New Query** (Új lekérdezés) elemre. Megnyílik egy, a kiválasztott adatbázishoz csatlakoztatott üres lekérdezési ablak.

3. A lekérdezési ablakban írja be a következő lekérdezést, és módosítsa `<Azure_AD_principal_name>` kívánt felhasználói egyszerű neve lesz az Azure AD-felhasználó vagy egy Azure AD-csoport megjelenített neve:

   ```sql
   CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
   ```

   > [!NOTE]
   > Az Azure AD-felhasználók lesznek megjelölve az adatbázis metaadatai típusú E (EXTERNAL_USER) és a csoportok típusú X (EXTERNAL_GROUPS). További információkért lásd: [sys.database_principals](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql).

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

2. A Beállítások panelen válassza a **Naplózás és fenyegetésészlelés** elemet. Vegye figyelembe, hogy a kiszolgálószintű naplózás le van tiltva, és a **Kiszolgálóbeállítások megtekintése** hivatkozás teszi lehetővé a kiszolgálónaplózási beállítások megtekintését vagy módosítását ebből a környezetből.

    ![Auditing (Naplózás) panel](./media/sql-database-security-tutorial/auditing-get-started-settings.png)

3. Ha a kiszolgálószinten megadotthoz képest más naplózási típust (vagy helyet) kíván engedélyezni, állítsa **ON** (Be) értékűre az Auditing (Naplózás) funkciót, az Auditing Type (Naplózási típus) számára pedig a **Blob** lehetőséget válassza. Ha a kiszolgálószintű blobnaplózás engedélyezve van, az adatbázisszinten konfigurált naplózás és a kiszolgálószintű blobnaplózás egyszerre történik.

    ![A naplózás bekapcsolása](./media/sql-database-security-tutorial/auditing-get-started-turn-on.png)

4. Az Audit Logs Storage (Naplótárolás) panel megnyitásához válassza a **Storage Details** (Tároló részletei) lehetőséget. Válassza ki a naplók tárolására szolgáló Azure Storage-fiókot, valamint azt a megőrzési időtartamot, amelynek leteltével a rendszer törli a régi naplókat, majd kattintson alul az **OK** gombra.

   > [!TIP]
   > A naplózásijelentés-sablonok minél hatékonyabb használatához, használja ugyanazt a tárfiókot az összes naplózott adatbázishoz.

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
> - Tűzfalszabályok beállítása a kiszolgáló és/vagy az adatbázis számára.
> - Csatlakozás az adatbázishoz biztonságos kapcsolati sztring használatával.
> - Az Azure SQL Azure Active Directory-rendszergazda konfigurálása
> - Felhasználói hozzáférés kezelése.
> - Adatok védelme titkosítással.
> - Az SQL Database naplózási funkciójának engedélyezése.
> - Az SQL Database fenyegetésészlelési funkciójának engedélyezése.

A következő oktatóanyag egy földrajzilag elosztott adatbázis implementálását mutatja be.

> [!div class="nextstepaction"]
>[Földrajzilag elosztott adatbázis implementálása](sql-database-implement-geo-distributed-database.md)
