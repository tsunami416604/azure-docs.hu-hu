---
title: Az Azure SQL Database önálló vagy készletezett adatbázis védelme |} A Microsoft Docs
description: Ismerje meg a módszerek és szolgáltatások biztonságossá egy önálló vagy készletezett adatbázis az Azure SQL Database-ben.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: d6f14a7cdcb77c1ca47d0f79f587e0bf3606b5d5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57893271"
---
# <a name="tutorial-secure-a-single-or-pooled-database"></a>Oktatóanyag: Egyetlen vagy készletezett adatbázis védelme

Az Azure SQL Database egy önálló vagy készletezett adatbázis adatait azáltal, hogy védelemmel látja el:

- Hozzáférés korlátozása tűzfalszabályok használatával
- Identitást igénylő hitelesítési mechanizmusok használata
- Engedélyezési használata szerepköralapú tagságok és engedélyek
- Biztonsági szolgáltatások engedélyezése

> [!NOTE]
> Az Azure SQL database felügyelt példány használatával lett biztonságossá téve hálózati biztonsági szabályok és a privát végpontok leírtak szerint [Azure SQL database felügyelt példány](sql-database-managed-instance-index.yml) és [kapcsolati architektúra](sql-database-managed-instance-connectivity-architecture.md).

Mindössze néhány lépés végrehajtásával az adatbázis-biztonság javítása érdekében. Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> - Kiszolgálószintű és adatbázisszintű tűzfalszabályok létrehozása
> - Az Azure Active Directory (AD) rendszergazda konfigurálása
> - Felhasználói hozzáférés felügyelete az SQL-hitelesítés, az Azure AD-hitelesítés és a biztonságos kapcsolati karakterláncok
> - Biztonsági funkciók, például a speciális biztonsági, naplózási adatmaszkolási, és a titkosítás engedélyezése

További tudnivalókért tekintse meg a [Azure SQL Database biztonsági áttekintése](/azure/sql-database/sql-database-security-index) és [képességek](sql-database-security-overview.md) cikkeket.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez, ellenőrizze, hogy a következő előfeltételek vonatkoznak:

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)
- Egy Azure SQL server és adatbázis
  - Hozza létre azokat az [az Azure portal](sql-database-single-database-get-started.md), [CLI](sql-database-cli-samples.md), vagy [PowerShell](sql-database-powershell-samples.md)

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Az oktatóanyag lépéseit minden, jelentkezzen be a [Azure Portalon](https://portal.azure.com/)

## <a name="create-firewall-rules"></a>Tűzfalszabályok létrehozása

Az Azure-ban tűzfallal védett SQL-adatbázisok. Alapértelmezés szerint a kiszolgáló és az adatbázis felé irányuló összes kapcsolatot a rendszer elutasítja, más Azure-szolgáltatásokhoz érkező kapcsolatokat kivéve. További tudnivalókért lásd: [Azure SQL Database kiszolgálószintű és adatbázisszintű tűzfalszabályok](sql-database-firewall-configure.md).

Állítsa be **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** való **OFF** a legbiztonságosabb konfiguráció. Ezután hozzon létre egy [fenntartott IP (klasszikus üzembe helyezés)](../virtual-network/virtual-networks-reserved-public-ip.md) az erőforrás által csatlakoznak, például egy Azure virtuális gépek és felhőszolgáltatások szolgáltatással, és kizárólag az adott IP-cím hozzáféréssel a tűzfalon keresztül. Ha használja a [erőforrás-kezelő](/azure/virtual-network/virtual-network-ip-addresses-overview-arm) -alapú üzemi modellben egy dedikált nyilvános IP-cím szükség az egyes erőforrások.

> [!NOTE]
> Az SQL Database az 1433-as porton kommunikál. Csatlakozás a vállalati hálózaton belülről próbál, ha a hálózati tűzfal előfordulhat, hogy nem engedélyezett a kimenő forgalmat az 1433-as porton keresztül. Ha igen, nem lehet csatlakoztatni az Azure SQL Database-kiszolgálóhoz, ha a rendszergazda megnyitja az 1433-as porton.

### <a name="set-up-sql-database-server-firewall-rules"></a>Állítsa be az SQL Database-kiszolgáló tűzfalszabályait

Kiszolgálószintű IP-tűzfalszabályainak alkalmazása az egyazon SQL Database-kiszolgálón található összes adatbázishoz.

A kiszolgálószintű tűzfalszabályok beállítása:

1. Az Azure Portalon, válassza ki a **SQL-adatbázisok** elemet a bal oldali menüben válassza ki az adatbázist a a **SQL-adatbázisok** lap.

    ![kiszolgálói tűzfalszabály](./media/sql-database-security-tutorial/server-name.png)

    > [!NOTE]
    > Ellenőrizze, hogy a teljes kiszolgálónév (például *yourserver.database.windows.net*) használható az oktatóanyag későbbi részében.

1. Az a **áttekintése** lapon jelölje be **kiszolgálótűzfal beállítása**. A **tűzfalbeállítások** megnyílik az adatbázis-kiszolgáló.

   1. Válassza ki **ügyfél IP-cím hozzáadása** az aktuális IP-cím hozzáadása egy új tűzfalszabályt az eszköztáron. A szabály a 1433-as porton az egyetlen IP-címet vagy egy IP-címtartományt is megnyithatja. Kattintson a **Mentés** gombra.

      ![kiszolgálótűzfal-szabály beállítása](./media/sql-database-security-tutorial/server-firewall-rule2.png)

   1. Válassza ki **OK** és zárja be a **tűzfalbeállítások** lapot.

Mostantól a kiszolgáló bármelyik adatbázisához csatlakozhat a megadott IP-címmel vagy IP-címtartománnyal.

> [!IMPORTANT]
> Alapértelmezés szerint az SQL Database-tűzfalon keresztül érhető el az Azure-szolgáltatásokhoz, a **Azure-szolgáltatásokhoz való hozzáférés engedélyezése**. Válasszon **OFF** letiltja a hozzáférést az Azure-szolgáltatásokhoz.

### <a name="setup-database-firewall-rules"></a>Tűzfalszabályok beállítása

Adatbázisszintű tűzfalszabályok kizárólag az egyes adatbázisok érvényesek. Az adatbázis megőrzi ezeket a szabályokat kiszolgáló a feladatátvétel alatt. Adatbázisszintű tűzfalszabályok a Transact-SQL (T-SQL-) utasításokkal csak konfigurálható, és csak azután konfigurálta egy kiszolgálószintű tűzfalszabályt.

Adatbázisszintű tűzfalszabály beállításához:

1. Csatlakozás az adatbázishoz, például az [SQL Server Management Studio](./sql-database-connect-query-ssms.md).

1. A **Object Explorer**jobb gombbal az adatbázisra, és válassza ki **új lekérdezés**.

1. A lekérdezési ablakban adja hozzá ezt az utasítást, és a nyilvános IP-címet az IP-címének módosítása:

    ```sql
    EXECUTE sp_set_database_firewall_rule N'Example DB Rule','0.0.0.4','0.0.0.4';
    ```

1. Válassza az eszköztár **Execute** a tűzfalszabály létrehozásához.

> [!NOTE]
> Ön is létrehozhat egy kiszolgálószintű tűzfalszabályt az SSMS használatával a [sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database?view=azuresqldb-current) parancsot, bár Ön kapcsolódnia kell a *fő* adatbázis.

## <a name="create-an-azure-ad-admin"></a>Hozzon létre egy Azure AD-rendszergazda

Győződjön meg arról, hogy a megfelelő Azure Active Directory (AD) a felügyelt tartomány használja. Válassza ki az AD-tartományhoz, használja az Azure portal jobb felső sarkában. Ez a folyamat megerősíti, hogy ugyanahhoz az előfizetéshez is szolgál az Azure AD és az SQL Server kiszolgálót az Azure SQL database, sem az adattárházra.

   ![Válasszon ad](./media/sql-database-security-tutorial/8choose-ad.png)

Az Azure AD-rendszergazda beállítása:

1. Az Azure Portalon az a **az SQL server** lapon jelölje be **Active Directory-rendszergazda**. Ezután válassza ki **rendszergazda beállítása**.

    ![az Active Directory kiválasztása](./media/sql-database-security-tutorial/admin-settings.png)  

    > [!IMPORTANT]
    > Meg kell lennie a "Vállalati rendszergazda" vagy "Globális rendszergazda" Ez a feladat végrehajtásához.

1. Az a **rendszergazda hozzáadása** lapon, a Keresés és a az AD-felhasználó vagy csoport kijelölése és a válassza **kiválasztása**. A tagok és az Active Directory-csoportokat, és a bejegyzés szürkén jelenik meg az Azure AD-rendszergazdák nem támogatottak. Lásd: [Azure AD-funkciók és korlátozások](sql-database-aad-authentication.md#azure-ad-features-and-limitations).

    ![Válassza ki a rendszergazda](./media/sql-database-security-tutorial/admin-select.png)

    > [!IMPORTANT]
    > Szerepköralapú hozzáférés-vezérlés (RBAC) csak a portál vonatkozik, és nem vonatkoznak az SQL Server.

1. Felső részén a **Active Directory-rendszergazda** lapon jelölje be **mentése**.

    – Egy rendszergazda megváltoztathatja a folyamat eltarthat néhány percig. Az új rendszergazda fog megjelenni a **Active Directory-rendszergazda** mezőbe.

> [!NOTE]
> Azure AD-rendszergazdát beállításakor az új felügyeleti name (felhasználó vagy csoport) az SQL Server hitelesítési felhasználóként nem létezik a *fő* adatbázis. Ha van ilyen, a telepítés meghiúsul, és visszafordítani a változásokat, amely azt jelzi, hogy az ilyen felügyeleti neve már létezik. Mivel az SQL Server-hitelesítés felhasználói lehetőség nem része az Azure ad-ben, bármely annak érdekében, hogy a felhasználót az Azure AD-hitelesítés használatával csatlakozás sikertelen lesz.

Konfigurálása az Azure AD kapcsolatos információkért lásd:

- [A helyszíni identitások integrálása az Azure ad-ben](../active-directory/hybrid/whatis-hybrid-identity.md)
- [A saját tartománynév hozzáadása az Azure ad-ben](../active-directory/active-directory-domains-add-azure-portal.md)
- [A Microsoft Azure mostantól támogatja a Windows Server AD összevonási](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)
- [Az Azure AD-címtár felügyelete](../active-directory/fundamentals/active-directory-administer.md)
- [Azure AD kezelése PowerShell használatával](/powershell/azure/overview?view=azureadps-2.0)
- [Hibrid identitás – szükséges portok és protokollok](../active-directory/hybrid/reference-connect-ports.md)

## <a name="manage-database-access"></a>Adatbázis-hozzáférés kezelése

Adatbázis-hozzáférés kezelése a felhasználók hozzáadása az adatbázishoz, vagy a biztonságos kapcsolati karakterláncok felhasználói hozzáférést. Kapcsolati karakterláncok hasznosak a külső alkalmazások számára. További tudnivalókért lásd: [hozzáférés-vezérlés az Azure SQL](sql-database-control-access.md) és [AD-hitelesítés](sql-database-aad-authentication.md).

Felhasználók hozzáadásához válassza ki az adatbázis-hitelesítés típusa:

- **SQL-hitelesítés**felhasználónevet és jelszót használja bejelentkezéseket és a esetén csak a kiszolgáló egy adott adatbázissal összefüggésben érvényesek

- **Az Azure AD-hitelesítés**, az Azure AD által felügyelt identitásokat használ

### <a name="sql-authentication"></a>SQL-hitelesítés

SQL-hitelesítés a felhasználó hozzáadása:

1. Csatlakozás az adatbázishoz, például az [SQL Server Management Studio](./sql-database-connect-query-ssms.md).

1. A **Object Explorer**, kattintson a jobb gombbal az adatbázist, és válassza a **új lekérdezés**.

1. A lekérdezési ablakban adja meg a következő parancsot:

    ```sql
    CREATE USER ApplicationUser WITH PASSWORD = 'YourStrongPassword1';
    ```

1. Válassza az eszköztár **Execute** a felhasználó létrehozásához.

1. Alapértelmezés szerint a felhasználó csatlakozhat az adatbázishoz, de nem jogosult adatok olvasására vagy írására. Az engedélyek megadása egy új lekérdezési ablakban hajtsa végre a következő parancsokat:

    ```sql
    ALTER ROLE db_datareader ADD MEMBER ApplicationUser;
    ALTER ROLE db_datawriter ADD MEMBER ApplicationUser;
    ```

> [!NOTE]
> Hozzon létre a nem rendszergazdai fiókoknak az adatbázis szintjén, kivéve, ha új felhasználók létrehozása, például a rendszergazdai feladatok végrehajtásához szükséges.

### <a name="azure-ad-authentication"></a>Azure AD-hitelesítés

Az Azure Active Directory-hitelesítés szükséges, hogy adatbázis-felhasználók jönnek létre, tartalmazott. Egy tartalmazottadatbázis-felhasználó az adatbázishoz társított Azure AD-címtárat az identitás vannak leképezve, és nincs bejelentkezési szerepel a *fő* adatbázis. Az Azure AD identity lehet egy adott felhasználó vagy csoport. További információkért lásd: [tartalmazottadatbázis-felhasználók, ellenőrizze az adatbázis hordozható](https://msdn.microsoft.com/library/ff929188.aspx) , és tekintse át a [az oktatóanyag az Azure AD](./sql-database-aad-authentication-configure.md) való hitelesítés az Azure AD használatával.

> [!NOTE]
> Adatbázis-felhasználók (kivéve a rendszergazdák) nem hozható létre az Azure portal használatával. Az Azure RBAC-szerepkörök nem SQL kiszolgáló, adatbázisok vagy data warehouse-adattárházak való propagálása. Ezek csak segítségével felügyelhető az Azure-erőforrásokat, és nem adatbázis-engedélyek vonatkoznak.
>
> Ha például a *SQL Server Közreműködője* szerepkör nem biztosít hozzáférést szeretne csatlakozni egy adatbázist, sem az adattárházra. Ezt az engedélyt kell biztosítani a T-SQL-utasítások használatával adatbázison belül.

> [!IMPORTANT]
> Különleges karaktereket, például kettőspont `:` vagy és szimbólumot `&` nem támogatottak a T-SQL-felhasználóneveket `CREATE LOGIN` és `CREATE USER` utasításokat.

Az Azure AD-hitelesítés a felhasználó hozzáadása:

1. Csatlakozás az Azure SQL server, az Azure AD-fiókkal legalább a *bármely felhasználó ALTER* engedéllyel.

1. A **Object Explorer**jobb gombbal az adatbázisra, és válassza ki **új lekérdezés**.

1. A lekérdezési ablakban adja meg a következő parancsot, és módosítsa `<Azure_AD_principal_name>` egyszerű neve lesz az Azure AD-felhasználó vagy az Azure AD-csoport megjelenítendő nevét:

   ```sql
   CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
   ```

> [!NOTE]
> Az Azure AD-felhasználók lesznek megjelölve az adatbázis metaadatai típus `E (EXTERNAL_USER)` , és írja be `X (EXTERNAL_GROUPS)` csoportok számára. További információkért lásd: [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql).

### <a name="secure-connection-strings"></a>Biztonságos kapcsolati karakterláncok

Ahhoz, hogy az ügyfélalkalmazás és az SQL database közötti biztonságos, titkosított kapcsolatot, egy kapcsolati karakterláncot kell konfigurálni:

- Titkosított kapcsolatot kérjen
- Nem megbízható kiszolgálótanúsítvány

A kapcsolat jött létre a Transport Layer Security (TLS), és csökkenti a man-in-the-middle támadások kockázatát. Kapcsolati karakterláncok adatbázisonként érhető el, és például az ADO.NET, JDBC, ODBC és PHP-ügyfél illesztőprogramokhoz konfigurált. További információ a TLS-ről és a kapcsolatokról: [A TLS megfontolandó szempontjai](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity).

Biztonságos kapcsolati karakterlánc másolása:

1. Az Azure Portalon, válassza ki a **SQL-adatbázisok** elemet a bal oldali menüben válassza ki az adatbázist a a **SQL-adatbázisok** lap.

1. Az a **áttekintése** lapon jelölje be **adatbázis kapcsolati karakterláncainak megjelenítése**.

1. Válasszon ki egy illesztőprogram-lapot, és másolja a kapcsolati karakterláncot.

    ![ADO.NET kapcsolati sztring](./media/sql-database-security-tutorial/connection.png)

## <a name="enable-security-features"></a>Biztonsági szolgáltatások engedélyezése

Az Azure SQL Database biztonsági szolgáltatásaival, amelyekhez hozzáférnek az Azure portal használatával. Ezek a funkciók érhetők el az adatbázis és a kiszolgáló, kivéve az adatmaszkolás, amely csak érhető el az adatbázisban. További tudnivalókért lásd: [adatbiztonság speciális](sql-database-advanced-data-security.md), [naplózási](sql-database-auditing.md), [dinamikus adatmaszkolás](sql-database-dynamic-data-masking-get-started.md), és [transzparens adattitkosítás](transparent-data-encryption-azure-sql.md).

### <a name="advanced-data-security"></a>Advanced Data Security

A speciális biztonsági szolgáltatás fordulhat elő, és a biztonsági riasztásokat biztosít a rendellenes tevékenységekre adott észlelte a potenciális fenyegetéseket. A felhasználók a gyanús eseményeket, a naplózási szolgáltatás segítségével ismerje meg, és határozza meg, ha az esemény történt, illetéktelen behatolás vagy biztonsági rés kiaknázása elleni az adatbázisban található adatok. Felhasználók is biztosítja a biztonsági áttekintéshez, amely tartalmazza a biztonságirés-felmérés és az észlelés és osztályozás eszközzel.

> [!NOTE]
> Egy példa fenyegetés SQL-injektálás, a folyamat, ahol a támadók rosszindulatú SQL helyezhet el alkalmazás bemenetei között meg. Egy alkalmazás tudtukon ezután a rosszindulatú SQL-utasítás végrehajtása és illetéktelen behatolás vagy módosítása az adatbázisban található adatok elérését teszik lehetővé a támadóknak.

Speciális biztonsági engedélyezése:

1. Az Azure Portalon, válassza ki a **SQL-adatbázisok** elemet a bal oldali menüben válassza ki az adatbázist a a **SQL-adatbázisok** lap.

1. Az a **áttekintése** lapon válassza ki a **kiszolgálónév** hivatkozásra. Megnyílik az adatbázis-kiszolgáló oldalára.

1. Az a **az SQL server** lapon, keresse meg a **biztonsági** szakaszt, és válassza **adatok biztonságú**.

   1. Válassza ki **ON** alatt **adatok biztonságú** a funkció engedélyezéséhez. Válasszon egy tárfiókot a sebezhetőségi felmérés eredmények mentése. Ezután válassza a **Save** (Mentés) lehetőséget.

      ![Navigációs ablaktábla](./media/sql-database-security-tutorial/threat-settings.png)

      Beállíthatja, hogy a biztonsági riasztások, és a tárolási adatok és a fenyegetéstípusok észlelési e-maileket.

1. Lépjen vissza a **SQL-adatbázisok** az adatbázisról, és válassza a lap **adatok biztonságú** alatt a **biztonsági** szakaszban. Itt találja a különböző biztonsági mutatók érhető el az adatbázis.

    ![Fenyegetés állapota](./media/sql-database-security-tutorial/threat-status.png)

Rendellenes tevékenységek észlelésekor kap egy e-mailt, amelyen az esemény vonatkozó információk. Ez a tevékenység, adatbázis, kiszolgáló, esemény időpontja, lehetséges okok jellege tartalmazza, és javasolt műveletek vizsgálata és enyhítése érdekében a potenciális fenyegetést. Ha ilyen e-mail érkezik, válassza ki a **Azure SQL-napló** indítsa el az Azure Portalon, és a kapcsolódó naplórekordok az idő az esemény megjelenítése mutató hivatkozást.

   ![Fenyegetésészlelési e-mail](./media/sql-database-security-tutorial/threat-email.png)

### <a name="auditing"></a>Naplózás

A naplózási szolgáltatás nyomon követi az adatbázisok eseményeit, és írja az eseményeket egy naplóba, vagy az Azure Storage, az Azure Monitor naplóira, vagy egy eseményközpontba. Naplózás segít a szabályozásoknak való megfelelőséget, adatbázis-tevékenység megértésében, valamint betekintést nyerhet az eltéréseket és rendellenességeket, amelyek jelezheti a potenciális biztonsági problémákat.

A naplózás engedélyezéséhez:

1. Az Azure Portalon, válassza ki a **SQL-adatbázisok** elemet a bal oldali menüben válassza ki az adatbázist a a **SQL-adatbázisok** lap.

1. Az a **biztonsági** szakaszban jelölje be **naplózási**.

1. A **naplózási** beállításait, állítsa be a következő értékeket:

   1. Állítsa be **naplózás** való **ON**.

   1. Válassza ki **naplózási naplócél** mint bármelyik, a következőket:

       - **Tárolási**, ahol biztonságiesemény-naplóinak lesznek mentve, és mint tölthető le egy Azure storage-fiók *.xel* fájlok

          > [!TIP]
          > Használja ugyanazt a tárfiókot az összes naplózott adatbázishoz jelentéssablont naplózás a legjobban.

       - **Log Analytics**, amely automatikusan tárolja az eseményeket lekérdezés vagy a további elemzés céljából

           > [!NOTE]
           > A **Log Analytics-munkaterület** elemzéseket, az egyéni riasztási szabályok és az Excel- vagy Power BI exportálási speciális szolgáltatások támogatásához szükség. Egy munkaterület nélkül csak a Lekérdezésszerkesztő érhető el.

       - **Event Hubs**, amely lehetővé teszi, hogy az eseményeket az átirányíthatók más alkalmazások használatra

   1. Kattintson a **Mentés** gombra.

      ![Naplózási beállítások](./media/sql-database-security-tutorial/audit-settings.png)

1. Most kiválaszthatja **naplók megtekintése** adatbázis események adatainak megtekintéséhez.

    ![Naplórekordok](./media/sql-database-security-tutorial/audit-records.png)

> [!IMPORTANT]
> Lásd: [SQL database naplózási szolgáltatásával](sql-database-auditing.md) további testreszabásáról az események naplózása a PowerShell vagy REST API használatával.

### <a name="dynamic-data-masking"></a>Dinamikus adatmaszkolás

Az adatok maszkolási funkció elrejti bizalmas adatokat az adatbázisban.

Adatmaszkolás engedélyezéséhez:

1. Az Azure Portalon, válassza ki a **SQL-adatbázisok** elemet a bal oldali menüben válassza ki az adatbázist a a **SQL-adatbázisok** lap.

1. Az a **biztonsági** szakaszban jelölje be **dinamikus Adatmaszkolás**.

1. A **dinamikus adatmaszkolás** beállításaiban, válassza **Hozzáadás maszk** hozzáadni egy maszkolási szabályra. Az Azure automatikusan kitölti a rendelkezésre álló adatbázissémák, táblák és oszlopok közül választhat.

    ![Affinitásmaszk beállításait](./media/sql-database-security-tutorial/mask-settings.png)

1. Kattintson a **Mentés** gombra. A kijelölt adatokat adatvédelmi most maszkolva van.

    ![A példában a maszk](./media/sql-database-security-tutorial/mask-query.png)

### <a name="transparent-data-encryption"></a>Transzparens adattitkosítás

A titkosítási funkció automatikusan titkosítja az inaktív adatokat, és nem kell módosítani a titkosított adatbázist elérő alkalmazást. Az új adatbázisokat, titkosítás alapértelmezés szerint engedélyezve van. Az adatokat az SSMS használatával is titkosíthatja a [Always encrypted](sql-database-always-encrypted.md) funkció.

Engedélyezheti vagy titkosítási ellenőrzése:

1. Az Azure Portalon, válassza ki a **SQL-adatbázisok** elemet a bal oldali menüben válassza ki az adatbázist a a **SQL-adatbázisok** lap.

1. Az a **biztonsági** szakaszban jelölje be **transzparens adattitkosítás**.

1. Ha szükséges, állítsa be **adattitkosítás** való **ON**. Kattintson a **Mentés** gombra.

    ![Transzparens adattitkosítás](./media/sql-database-security-tutorial/encryption-settings.png)

> [!NOTE]
> Titkosítás állapotának megtekintéséhez csatlakoznia kell az adatbázis használatával [SSMS](./sql-database-connect-query-ssms.md) és lekérdezése az `encryption_state` oszlopa a [sys.dm_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) megtekintése. Egy állapotát `3` azt jelzi, hogy az adatbázis titkosított.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban bemutattuk, mindössze néhány lépés végrehajtásával az adatbázis biztonságának javítása érdekében. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> - Kiszolgálószintű és adatbázisszintű tűzfalszabályok létrehozása
> - Az Azure Active Directory (AD) rendszergazda konfigurálása
> - Felhasználói hozzáférés felügyelete az SQL-hitelesítés, az Azure AD-hitelesítés és a biztonságos kapcsolati karakterláncok
> - Biztonsági funkciók, például a speciális biztonsági, naplózási adatmaszkolási, és a titkosítás engedélyezése

Folytassa a következő oktatóanyaggal, megtudhatja, hogyan valósíthat meg a földrajzi elosztás.

> [!div class="nextstepaction"]
>[Földrajzilag elosztott adatbázis implementálása](sql-database-implement-geo-distributed-database.md)
