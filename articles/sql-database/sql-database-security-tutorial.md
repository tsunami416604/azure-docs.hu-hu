---
title: Egy vagy több halmazú adatbázis biztonságossá tétele
description: Egy oktatóanyag, amely bemutatja a technikák és funkciók védelmére egy vagy egy készletbe ágyazott adatbázis az Azure SQL Database-ben.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 09/03/2019
ms.custom: seoapril2019
ms.openlocfilehash: 9e3c5c12157a007bcad59a78b4623ff4d5a0041f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238831"
---
# <a name="tutorial-secure-a-single-or-pooled-database"></a>Oktatóanyag: Egy- vagy készletezésű adatbázis biztonságossá tétele

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> - Kiszolgálószintű és adatbázisszintű tűzfalszabályok létrehozása
> - Azure Active Directory (AD) rendszergazda konfigurálása
> - Felhasználói hozzáférés kezelése SQL-hitelesítéssel, Azure AD-hitelesítéssel és biztonságos kapcsolati karakterláncokkal
> - Biztonsági funkciók, például speciális adatbiztonság, naplózás, adatmaszkolás és titkosítás engedélyezése

Az Azure SQL Database egyetlen vagy készletbe adott adatbázisban biztosítja az adatokat, lehetővé téve a következőket:

- Hozzáférés korlátozása tűzfalszabályok használatával
- Identitást igénylő hitelesítési mechanizmusok használata
- Engedélyezés használata szerepköralapú tagsággal és engedélyekkel
- Biztonsági szolgáltatások engedélyezése

> [!NOTE]
> Egy felügyelt példányon lévő Azure SQL-adatbázis hálózati biztonsági szabályok és magánvégpontok használatával védett az [Azure SQL-adatbázis által felügyelt példány](sql-database-managed-instance-index.yml) és [kapcsolódási architektúra leírtak szerint.](sql-database-managed-instance-connectivity-architecture.md)

További információ: [az Azure SQL Database biztonsági áttekintése](/azure/sql-database/sql-database-security-index) és [képességeiről](sql-database-security-overview.md) szóló cikkek.

> [!TIP]
> A következő Microsoft Learn modul segítségével ingyenesen megtudhatja, hogyan [biztosíthatja az Azure SQL-adatbázist.](https://docs.microsoft.com/learn/modules/secure-your-azure-sql-database/)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag befejezéséhez győződjön meg arról, hogy rendelkezik a következő előfeltételekkel:

- [SQL Server Management Stúdió](/sql/ssms/download-sql-server-management-studio-ssms)
- Azure SQL-kiszolgáló és -adatbázis
  - Hozzon létre őket az [Azure Portalon,](sql-database-single-database-get-started.md) [a CLI-n](sql-database-cli-samples.md)vagy a [PowerShellen](sql-database-powershell-samples.md)

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Az oktatóanyag összes lépéséhez jelentkezzen be az [Azure Portalra](https://portal.azure.com/)

## <a name="create-firewall-rules"></a>Tűzfalszabályok létrehozása

Az SQL-adatbázisokat az Azure-ban tűzfalak védik. Alapértelmezés szerint a kiszolgálóval és az adatbázissal létesített összes kapcsolat elutasításra kerülnek. További információ: [Azure SQL Database server- és adatbázis-szintű tűzfalszabályok.](sql-database-firewall-configure.md)

Állítsa be **az Azure-szolgáltatásokhoz való hozzáférés engedélyezése** **off** beállítását a legbiztonságosabb konfigurációhoz. Ezután hozzon létre egy [fenntartott IP-címet (klasszikus üzembe helyezés)](../virtual-network/virtual-networks-reserved-public-ip.md) az erőforrás, amely csatlakoznia kell, például egy Azure virtuális gép vagy felhőszolgáltatás, és csak a tűzfalon keresztül engedélyezi az IP-cím elérését. Ha az [erőforrás-kezelő](/azure/virtual-network/virtual-network-ip-addresses-overview-arm) telepítési modelljét használja, minden erőforráshoz külön nyilvános IP-cím szükséges.

> [!NOTE]
> Az SQL Database az 1433-as porton kommunikál. Ha vállalati hálózaton belülről próbál csatlakozni, előfordulhat, hogy az 1433-as porton keresztüli kimenő forgalmat a hálózat tűzfala nem engedélyezi. Ha igen, nem tud csatlakozni az Azure SQL Database-kiszolgálóhoz, kivéve, ha a rendszergazda megnyitja az 1433-as portot.

### <a name="set-up-sql-database-server-firewall-rules"></a>Sql Database server tűzfalszabályainak beállítása

A kiszolgálószintű IP-tűzfal szabályai az ugyanazon SQL Database-kiszolgálón belüli összes adatbázisra vonatkoznak.

Kiszolgálószintű tűzfalszabály beállítása:

1. Az Azure Portalon válassza az **SQL-adatbázisok a** bal oldali menüben, és válassza ki az adatbázist az **SQL-adatbázisok** lapon.

    ![kiszolgálói tűzfalszabály](./media/sql-database-security-tutorial/server-name.png)

    > [!NOTE]
    > Ügyeljen arra, hogy másolja a teljesen minősített kiszolgáló nevét (például *yourserver.database.windows.net)* későbbi használatra az oktatóanyagban.

1. Az **Áttekintés** lapon válassza a **Kiszolgálótűzfal beállítása**lehetőséget. Megnyílik az adatbázis-kiszolgáló **tűzfalbeállításai** lap.

   1. Válassza az **eszköztár Ügyfél IP hozzáadása** lehetőséget, ha az aktuális IP-címet új tűzfalszabályhoz szeretné hozzáadni. A szabály egyetlen IP-címhez vagy IP-címtartományhoz is megnyithatja az 1433-as portot. Kattintson a **Mentés** gombra.

      ![kiszolgálótűzfal-szabály beállítása](./media/sql-database-security-tutorial/server-firewall-rule2.png)

   1. Válassza **az OK gombot,** és zárja be a **Tűzfal beállításai** lapot.

Mostantól a kiszolgáló bármelyik adatbázisához csatlakozhat a megadott IP-címmel vagy IP-címtartománnyal.

### <a name="setup-database-firewall-rules"></a>Adatbázis tűzfalszabályainak beállítása

Az adatbázis-szintű tűzfalszabályok csak az egyes adatbázisokra vonatkoznak. Az adatbázis megőrzi ezeket a szabályokat a kiszolgáló feladatátvétele során. Az adatbázis-szintű tűzfalszabályok csak Transact-SQL (T-SQL) utasításokkal konfigurálhatók, és csak kiszolgálószintű tűzfalszabály konfigurálása után.

Adatbázisszintű tűzfalszabály beállítása:

1. Csatlakozzon az adatbázishoz, például az [SQL Server Management Studio](./sql-database-connect-query-ssms.md)használatával.

1. Az **Objektumkezelőben**kattintson a jobb gombbal az adatbázisra, és válassza az **Új lekérdezés**parancsot.

1. A lekérdezésablakban adja hozzá ezt az utasítást, és módosítsa az IP-címet a nyilvános IP-címhez:

    ```sql
    EXECUTE sp_set_database_firewall_rule N'Example DB Rule','0.0.0.4','0.0.0.4';
    ```

1. Az eszköztáron válassza a **Végrehajtás** gombot a tűzfalszabály létrehozásához.

> [!NOTE]
> Az SSMS-ben kiszolgálószintű tűzfalszabályt is létrehozhat az [sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database?view=azuresqldb-current) paranccsal, de a fő adatbázishoz kell kapcsolódnia. *master*

## <a name="create-an-azure-ad-admin"></a>Azure AD-rendszergazda létrehozása

Győződjön meg arról, hogy a megfelelő Azure Active Directory (AD) felügyelt tartományt használja. Az AD-tartomány kiválasztásához használja az Azure Portal jobb felső sarkát. Ez a folyamat megerősíti, hogy ugyanazt az előfizetést használja az Azure AD és az Azure SQL-adatbázist vagy adatraktárat üzemeltető SQL Server.

   ![választás-hirdetés](./media/sql-database-security-tutorial/8choose-ad.png)

Az Azure AD-rendszergazda beállítása:

1. Az Azure **PortalSQL-kiszolgáló** lapján válassza az **Active Directory rendszergazdája**lehetőséget. Ezután válassza **a Rendszergazda beállítása lehetőséget.**

    ![az Active Directory kiválasztása](./media/sql-database-security-tutorial/admin-settings.png)  

    > [!IMPORTANT]
    > A feladat végrehajtásához "vállalati rendszergazdának" vagy "globális rendszergazdának" kell lennie.

1. A **Rendszergazda hozzáadása** lapon keressen rá az AD-felhasználóra vagy -csoportra, és válassza a **Kijelölés lehetőséget.** Az Active Directory összes tagja és csoportja szerepel a listában, és a bejegyzések szürkén jelennek meg, és az Azure AD-rendszergazdák nem támogatottak. Lásd: [Az Azure AD funkciói és korlátai.](sql-database-aad-authentication.md#azure-ad-features-and-limitations)

    ![rendszergazda kiválasztása](./media/sql-database-security-tutorial/admin-select.png)

    > [!IMPORTANT]
    > A szerepköralapú hozzáférés-vezérlés (RBAC) csak a portálra vonatkozik, és nem lesz propagálva az SQL Server kiszolgálóra.

1. Az **Active Directory felügyeleti** lapjának tetején válassza a Mentés **lehetőséget.**

    A rendszergazda módosításának folyamata több percet is igénybe vehet. Az új rendszergazda megjelenik az **Active Directory felügyeleti** mezőjében.

> [!NOTE]
> Az Azure AD-rendszergazda beállításakor az új rendszergazdai név (felhasználó vagy csoport) nem létezhet SQL Server hitelesítési felhasználóként a *fő* adatbázisban. Ha jelen van, a telepítő sikertelen lesz, és visszaállítja a módosításokat, jelezve, hogy ilyen rendszergazdai név már létezik. Mivel az SQL Server hitelesítési felhasználó nem része az Azure AD, minden erőfeszítést, hogy csatlakoztassa a felhasználót az Azure AD-hitelesítés sikertelen lesz.

Az Azure AD konfigurálásáról a következő témakörben talál további információt:

- [A helyszíni identitások integrálása az Azure AD-vel](../active-directory/hybrid/whatis-hybrid-identity.md)
- [Saját tartománynév hozzáadása az Azure AD-hez](../active-directory/active-directory-domains-add-azure-portal.md)
- [A Microsoft Azure mostantól támogatja a Windows Server AD-vel való összevonást](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)
- [Az Azure AD-címtár felügyelete](../active-directory/fundamentals/active-directory-administer.md)
- [Az Azure AD kezelése a PowerShell használatával](/powershell/azure/overview?view=azureadps-2.0)
- [Hibrid identitás szükséges portok és protokollok](../active-directory/hybrid/reference-connect-ports.md)

## <a name="manage-database-access"></a>Adatbázis-hozzáférés kezelése

Az adatbázis-hozzáférés kezelése felhasználók adatbázishoz való hozzáadásával, vagy a felhasználói hozzáférés biztonságos kapcsolati karakterláncokkal való engedélyezésével. A kapcsolati karakterláncok külső alkalmazásokhoz hasznosak. További információ: [Bejelentkezések és felhasználói fiókok kezelése,](sql-database-manage-logins.md) valamint [AD-hitelesítés.](sql-database-aad-authentication.md)

Felhasználók hozzáadásához válassza ki az adatbázis-hitelesítés típusát:

- **SQL-hitelesítés**, felhasználónevet és jelszót használ a bejelentkezéshez, és csak a kiszolgálón belüli adott adatbázis környezetében érvényes

- **Azure AD-hitelesítés**, az Azure AD által kezelt identitások használata

### <a name="sql-authentication"></a>SQL-hitelesítés

Sql-hitelesítéssel rendelkező felhasználó hozzáadása:

1. Csatlakozzon az adatbázishoz, például az [SQL Server Management Studio](./sql-database-connect-query-ssms.md)használatával.

1. Az **Objektumkezelőben**kattintson a jobb gombbal az adatbázisra, és válassza az **Új lekérdezés**parancsot.

1. A lekérdezésablakban adja meg a következő parancsot:

    ```sql
    CREATE USER ApplicationUser WITH PASSWORD = 'YourStrongPassword1';
    ```

1. Az eszköztáron válassza a **Végrehajtás** gombot a felhasználó létrehozásához.

1. Alapértelmezés szerint a felhasználó csatlakozhat az adatbázishoz, de nem jogosult adatok olvasására vagy írására. Az engedélyek megadásához hajtsa végre a következő parancsokat egy új lekérdezési ablakban:

    ```sql
    ALTER ROLE db_datareader ADD MEMBER ApplicationUser;
    ALTER ROLE db_datawriter ADD MEMBER ApplicationUser;
    ```

> [!NOTE]
> Nem rendszergazdai fiókokat hozhat létre adatbázisszinten, kivéve, ha olyan rendszergazdai feladatokat kell végrehajtaniuk, mint például új felhasználók létrehozása.

### <a name="azure-ad-authentication"></a>Azure AD-hitelesítés

Az Azure Active Directory-hitelesítés megköveteli, hogy az adatbázis-felhasználók a tartalmazottként jönnek létre. A tartalmazott adatbázis-felhasználó leképezi az adatbázishoz társított Azure AD-címtárban lévő identitást, és nem rendelkezik bejelentkezéssel a *fő* adatbázisban. Az Azure AD-identitás lehet egy adott felhasználó vagy egy csoport. További információ: [Tartalmazott adatbázis-felhasználók, hogy az adatbázis hordozható,](https://msdn.microsoft.com/library/ff929188.aspx) és tekintse át az [Azure AD oktatóanyag,](./sql-database-aad-authentication-configure.md) hogyan hitelesítheti az Azure AD használatával.

> [!NOTE]
> Adatbázis-felhasználók (kivéve a rendszergazdák) nem hozhatók létre az Azure Portal használatával. Az Azure RBAC-szerepkörök nem propagálnak SQL-kiszolgálókra, adatbázisokra vagy adatraktárakba. Ezek csak az Azure-erőforrások kezelésére szolgálnak, és nem vonatkoznak az adatbázis-engedélyekre.
>
> Az SQL *Server közreműködői* szerepkör például nem ad hozzáférést egy adatbázishoz vagy adattárházhoz való csatlakozáshoz. Ezt az engedélyt az adatbázison belül T-SQL utasítások használatával kell megadni.

> [!IMPORTANT]
> A `:` T-SQL `&` `CREATE LOGIN` és az utasítások ban a felhasználónevek nem `CREATE USER` támogatják a speciális karaktereket, például a kettőspontot vagy az ampersand-ot.

Azure AD-hitelesítéssel rendelkező felhasználó hozzáadása:

1. Csatlakozzon az Azure SQL-kiszolgálóhoz egy Azure AD-fiók használatával, amely legalább az *ALTER ANY USER* engedéllyel rendelkezik.

1. Az **Objektumkezelőben**kattintson a jobb gombbal az adatbázisra, és válassza az **Új lekérdezés**parancsot.

1. A lekérdezési ablakban írja be `<Azure_AD_principal_name>` a következő parancsot, és módosítsa az Azure AD-felhasználó vagy az Azure AD-csoport megjelenítendő nevére:

   ```sql
   CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
   ```

> [!NOTE]
> Az Azure AD-felhasználók vannak megjelölve `E (EXTERNAL_USER)` az `X (EXTERNAL_GROUPS)` adatbázis metaadatai a csoportok típusa és típusa. További információ: [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql).

### <a name="secure-connection-strings"></a>Biztonságos kapcsolati karakterláncok

Az ügyfélalkalmazás és az SQL-adatbázis közötti biztonságos, titkosított kapcsolat biztosításához a kapcsolati karakterláncot a következőkre kell konfigurálni:

- Titkosított kapcsolat kérése
- Nem megbízható a kiszolgáló tanúsítványa

A kapcsolat a Transport Layer Security (TLS) használatával jön létre, és csökkenti a közbeékelődéses támadások kockázatát. A kapcsolati karakterláncok adatbázisonként érhetők el, és előre konfigurálva vannak az olyan ügyfél-illesztőprogramok támogatására, mint a ADO.NET, a JDBC, az ODBC és a PHP. További információ a TLS-ről és a kapcsolatokról: [A TLS megfontolandó szempontjai](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity).

Biztonságos kapcsolati karakterlánc másolása:

1. Az Azure Portalon válassza az **SQL-adatbázisok a** bal oldali menüben, és válassza ki az adatbázist az **SQL-adatbázisok** lapon.

1. Az **Áttekintés** lapon válassza az **Adatbázis-kapcsolati karakterláncok megjelenítése**lehetőséget.

1. Jelöljön ki egy illesztőprogram-lapot, és másolja a teljes kapcsolati karakterláncot.

    ![ADO.NET kapcsolati sztring](./media/sql-database-security-tutorial/connection.png)

## <a name="enable-security-features"></a>Biztonsági szolgáltatások engedélyezése

Az Azure SQL Database olyan biztonsági funkciókat biztosít, amelyek az Azure Portalon keresztül érhetők el. Ezek a szolgáltatások mind az adatbázis, mind a kiszolgáló számára elérhetők, kivéve az adatmaszkolást, amely csak az adatbázisban érhető el. További információ: [Speciális adatbiztonság](sql-database-advanced-data-security.md), [Naplózás](sql-database-auditing.md), [Dinamikus adatmaszkolás](sql-database-dynamic-data-masking-get-started.md)és [Transzparens adattitkosítás.](transparent-data-encryption-azure-sql.md)

### <a name="advanced-data-security"></a>Advanced Data Security

A speciális adatbiztonsági szolgáltatás észleli a potenciális fenyegetéseket, amint azok bekövetkeznek, és biztonsági riasztásokat biztosít a rendellenes tevékenységekről. A felhasználók a naplózási funkció segítségével fedezhetik fel ezeket a gyanús eseményeket, és megállapíthatják, hogy az esemény az adatbázisban lévő adatok elérésére, megsértésére vagy kihasználására szolgál-e. A felhasználók biztonsági áttekintést is kapnak, amely tartalmazza a biztonsági rés felmérését, valamint az adatfelderítési és -besorolási eszközt.

> [!NOTE]
> Egy példa fenyegetés SQL-injektálás, egy folyamat, ahol a támadók befecskendezése rosszindulatú SQL alkalmazás bemenetek. Az alkalmazások ezután tudtán kívül végrehajthatják a rosszindulatú SQL-t, és lehetővé tehetik a támadók számára az adatbázis adatainak megsértését vagy módosítását.

A speciális adatbiztonság engedélyezése:

1. Az Azure Portalon válassza az **SQL-adatbázisok a** bal oldali menüben, és válassza ki az adatbázist az **SQL-adatbázisok** lapon.

1. Az **Áttekintés** lapon válassza a **Kiszolgáló név hivatkozását.** Megnyílik az adatbázis-kiszolgáló lapja.

1. Az **SQL server** lapon keresse meg a **Biztonság szakaszt,** és válassza a **Speciális adatbiztonság**lehetőséget.

   1. A funkció engedélyezéséhez válassza **a Be** lehetőséget a **Speciális adatbiztonság** csoportban. Válasszon egy tárfiókot a biztonsági rés értékelési eredményeinek mentésához. Ezután válassza a **Save** (Mentés) lehetőséget.

      ![Navigációs ablaktábla](./media/sql-database-security-tutorial/threat-settings.png)

      Az e-maileket úgy is beállíthatja, hogy biztonsági riasztásokat, tárolási részleteket és fenyegetésészlelési típusokat kapjanak.

1. Térjen vissza az adatbázis **SQL-adatbázisok** lapjára, és válassza a **Speciális adatbiztonság** lehetőséget a **Biztonság** szakaszban. Itt különböző biztonsági mutatókat talál az adatbázishoz.

    ![Fenyegetés állapota](./media/sql-database-security-tutorial/threat-status.png)

Ha rendellenes tevékenységeket észlel, e-mailt kap az eseményről. Ez magában foglalja a tevékenység jellegét, adatbázis, kiszolgáló, esemény idő, lehetséges okok, és ajánlott műveletek et a potenciális fenyegetés kivizsgálására és csökkentésére. Ha ilyen e-mail érkezik, válassza az **Azure SQL naplózási napló** hivatkozást az Azure Portal elindításához, és az esemény időpontjára vonatkozó releváns naplózási rekordok megjelenítése.

   ![Fenyegetésészlelési e-mail](./media/sql-database-security-tutorial/threat-email.png)

### <a name="auditing"></a>Naplózás

A naplózási funkció nyomon követi az adatbázis-eseményeket, és az Azure storage-ban, az Azure Monitor naplóiban vagy egy eseményközpontban naplózza az eseményeket egy naplóba. A naplózás segít a jogszabályi megfelelőség fenntartásában, az adatbázis-tevékenységek megértésében, valamint az olyan eltérésekben és anomáliákban, amelyek a biztonság esetleges megsértésére utalhatnak.

A naplózás engedélyezése:

1. Az Azure Portalon válassza az **SQL-adatbázisok a** bal oldali menüben, és válassza ki az adatbázist az **SQL-adatbázisok** lapon.

1. A **Biztonság csoportban** válassza a **Naplózás**lehetőséget.

1. A **Naplózási** beállítások csoportban állítsa be a következő értékeket:

   1. A **Naplózás** beállítása **BE beállításra.**

   1. Válassza a **napló céljának** egyikét a következők egyikeként:

       - **Storage**, egy Azure-tárfiók, amelyen a rendszer eseménynaplókat ment, és *.xel* fájlként tölthető le

          > [!TIP]
          > Használja ugyanazt a tárfiókot az összes naplózott adatbázishoz, hogy a legtöbbet hozd ki a jelentéssablonok naplózásából.

       - **Log Analytics**, amely automatikusan tárolja az eseményeket lekérdezésvagy további elemzés céljából

           > [!NOTE]
           > A **Log Analytics munkaterület** szükséges a speciális funkciók, például az elemzés, az egyéni riasztási szabályok és az Excel vagy a Power BI exportálásának támogatásához. Munkaterület nélkül csak a lekérdezésszerkesztő érhető el.

       - **Az Event Hub**, amely lehetővé teszi az események más alkalmazásokban való használatra történő továbbítását

   1. Kattintson a **Mentés** gombra.

      ![Naplózási beállítások](./media/sql-database-security-tutorial/audit-settings.png)

1. Most **kiválaszthatja a Naplónaplók megtekintése** lehetőséget az adatbázis-események adatainak megtekintéséhez.

    ![Naplórekordok](./media/sql-database-security-tutorial/audit-records.png)

> [!IMPORTANT]
> Tekintse meg [az SQL-adatbázis naplózását](sql-database-auditing.md) arról, hogyan szabhatja testre a naplózási eseményeket a PowerShell vagy a REST API használatával.

### <a name="dynamic-data-masking"></a>Dinamikus adatmaszkolás

Az adatmaszkolási funkció automatikusan elrejti a bizalmas adatokat az adatbázisban.

Az adatok maszkolásának engedélyezése:

1. Az Azure Portalon válassza az **SQL-adatbázisok a** bal oldali menüben, és válassza ki az adatbázist az **SQL-adatbázisok** lapon.

1. A **Biztonság csoportban** válassza a **Dinamikus adatmaszkolás lehetőséget.**

1. A **Dinamikus adatmaszkolási** beállítások csoportban válassza a **Maszk hozzáadása** lehetőséget a maszkolási szabály hozzáadásához. Az Azure automatikusan feltölti az elérhető adatbázissémákat, táblákat és oszlopokat.

    ![Maszk beállítások](./media/sql-database-security-tutorial/mask-settings.png)

1. Kattintson a **Mentés** gombra. A kiválasztott információk mostantól el vannak fedve az adatvédelemhez.

    ![Példa maszkolása](./media/sql-database-security-tutorial/mask-query.png)

### <a name="transparent-data-encryption"></a>Transzparens adattitkosítás

A titkosítási szolgáltatás automatikusan titkosítja az inaktív adatokat, és nem igényel módosításokat a titkosított adatbázishoz hozzáférő alkalmazásokban. Új adatbázisok esetén a titkosítás alapértelmezés szerint be van kapcsolva. Az adatokat az SSMS és a [Mindig titkosított](sql-database-always-encrypted.md) funkció használatával is titkosíthatja.

Titkosítás engedélyezése vagy ellenőrzése:

1. Az Azure Portalon válassza az **SQL-adatbázisok a** bal oldali menüben, és válassza ki az adatbázist az **SQL-adatbázisok** lapon.

1. A **Biztonság** csoportban válassza az **Átlátszó adattitkosítás**lehetőséget.

1. Szükség esetén állítsa **az Adattitkosítást** **BE beállításra.** Kattintson a **Mentés** gombra.

    ![Transzparens adattitkosítás](./media/sql-database-security-tutorial/encryption-settings.png)

> [!NOTE]
> A titkosítási állapot megtekintéséhez csatlakozzon az `encryption_state` adatbázishoz az [SSMS](./sql-database-connect-query-ssms.md) használatával, és kérdezze le a [sys.dm_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) nézet oszlopát. Az állapot `3` azt jelzi, hogy az adatbázis titkosított.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban néhány egyszerű lépéssel megtanulta az adatbázis biztonságának javítását. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> - Kiszolgálószintű és adatbázisszintű tűzfalszabályok létrehozása
> - Azure Active Directory (AD) rendszergazda konfigurálása
> - Felhasználói hozzáférés kezelése SQL-hitelesítéssel, Azure AD-hitelesítéssel és biztonságos kapcsolati karakterláncokkal
> - Biztonsági funkciók, például speciális adatbiztonság, naplózás, adatmaszkolás és titkosítás engedélyezése

A következő oktatóanyagra a geodisztribúció megvalósításának megismeréséhez jusson tovább.

> [!div class="nextstepaction"]
>[Földrajzilag elosztott adatbázis implementálása](sql-database-implement-geo-distributed-database.md)
