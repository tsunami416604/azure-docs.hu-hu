---
title: Egyetlen vagy készletezett adatbázis biztonságossá tétele a Azure SQL Databaseban | Microsoft Docs
description: Egy oktatóanyag, amely bemutatja, hogyan biztosítható a Azure SQL Databaseban található egyetlen vagy készletezett adatbázis biztonságossá tételére szolgáló technikák és szolgáltatások.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 07/29/2019
ms.custom: seoapril2019
ms.openlocfilehash: 9850285482db4f5981c183b51152ba1a3ac4975c
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640123"
---
# <a name="tutorial-secure-a-single-or-pooled-database"></a>Oktatóanyag: Egyetlen vagy készletezett adatbázis védelme

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> - Kiszolgálói szintű és adatbázis-szintű tűzfalszabályok létrehozása
> - Azure Active Directory (AD) rendszergazda konfigurálása
> - Felhasználói hozzáférés kezelése SQL-hitelesítéssel, Azure AD-hitelesítéssel és biztonságos kapcsolati karakterláncokkal
> - Engedélyezheti a biztonsági funkciókat, például a speciális adatbiztonságot, a naplózást, az adatmaszkolást és a titkosítást

A Azure SQL Database a következőket teszi biztonságossá egy önálló vagy készletezett adatbázisban:

- Hozzáférés korlátozása tűzfalszabályok használatával
- Identitást igénylő hitelesítési mechanizmusok használata
- Hitelesítés használata szerepköralapú tagságokkal és engedélyekkel
- Biztonsági funkciók engedélyezése

> [!NOTE]
> A felügyelt példányokon található Azure SQL Database-t a hálózati biztonsági szabályok és privát végpontok védik az [Azure SQL Database felügyelt példány](sql-database-managed-instance-index.yml) és [kapcsolat architektúrája](sql-database-managed-instance-connectivity-architecture.md)című témakörben leírtak szerint.

További tudnivalókat a [Azure SQL Database biztonsági áttekintése](/azure/sql-database/sql-database-security-index) és [képességei](sql-database-security-overview.md) című cikkben talál.

> [!TIP]
> A következő Microsoft Learn modul segít megtanulni a [Azure SQL Database biztonságossá](https://docs.microsoft.com/learn/modules/secure-your-azure-sql-database/)tételének ingyenes módját.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez győződjön meg arról, hogy rendelkezik a következő előfeltételekkel:

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)
- Azure SQL Server-kiszolgáló és-adatbázis
  - Létrehozás a [Azure Portal](sql-database-single-database-get-started.md), a [CLI](sql-database-cli-samples.md)vagy a [PowerShell használatával](sql-database-powershell-samples.md)

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Az oktatóanyagban szereplő összes lépéshez jelentkezzen be [Azure Portal](https://portal.azure.com/)

## <a name="create-firewall-rules"></a>Tűzfalszabályok létrehozása

Az SQL-adatbázisokat tűzfalak védik az Azure-ban. Alapértelmezés szerint a rendszer visszautasítja a kiszolgáló és az adatbázis összes kapcsolatát, kivéve a többi Azure-szolgáltatástól érkező kapcsolatokat. További információ: [Azure SQL Database kiszolgálói szintű és adatbázis-szintű tűzfalszabályok](sql-database-firewall-configure.md).

Állítsa be az Azure-szolgáltatásokhoz **való hozzáférés engedélyezése** beállítást a legbiztonságosabb konfigurációhoz. Ezután hozzon létre egy [fenntartott IP-címet (klasszikus központi telepítést)](../virtual-network/virtual-networks-reserved-public-ip.md) ahhoz az erőforráshoz, amelyhez csatlakoznia kell, például egy Azure-beli virtuális gépet vagy egy felhőalapú szolgáltatást, és csak az IP-cím elérését engedélyezze a tűzfalon keresztül. Ha a [Resource Manager](/azure/virtual-network/virtual-network-ip-addresses-overview-arm) -alapú üzemi modellt használja, az egyes erőforrásokhoz dedikált nyilvános IP-címet kell megadni.

> [!NOTE]
> Az SQL Database az 1433-as porton kommunikál. Ha vállalati hálózaton belülről próbál csatlakozni, lehetséges, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat az 1433-as porton keresztül. Ha igen, nem tud csatlakozni a Azure SQL Database-kiszolgálóhoz, ha a rendszergazda megnyitja a 1433-es portot.

### <a name="set-up-sql-database-server-firewall-rules"></a>SQL Database kiszolgálói tűzfalszabályok beállítása

A kiszolgálói szintű IP-tűzfalszabályok a SQL Database kiszolgálón található összes adatbázisra érvényesek.

Kiszolgáló szintű tűzfalszabály beállítása:

1. Azure Portal a bal oldali menüben válassza az **SQL-adatbázisok** lehetőséget, majd válassza ki az adatbázist az **SQL-adatbázisok** lapon.

    ![kiszolgálói tűzfalszabály](./media/sql-database-security-tutorial/server-name.png)

    > [!NOTE]
    > Ügyeljen arra, hogy a teljes kiszolgálónevet (például *YourServer.database.Windows.net*) másolja az oktatóanyag későbbi részében való használatra.

1. Az **Áttekintés** lapon válassza a **kiszolgáló tűzfalának beállítása**lehetőséget. Megnyílik az adatbázis-kiszolgáló **tűzfalbeállítások** lapja.

   1. Válassza az **ügyfél IP-** címének hozzáadása lehetőséget az eszköztáron az aktuális IP-cím új tűzfalszabályként való hozzáadásához. A szabály az 1433-as portot egyetlen IP-cím vagy IP-címtartomány számára nyithatja meg. Kattintson a **Mentés** gombra.

      ![kiszolgálótűzfal-szabály beállítása](./media/sql-database-security-tutorial/server-firewall-rule2.png)

   1. Kattintson **az OK gombra** , és a **tűzfalbeállítások** oldal bezárásához.

Mostantól a kiszolgáló bármelyik adatbázisához csatlakozhat a megadott IP-címmel vagy IP-címtartománnyal.

> [!IMPORTANT]
> Alapértelmezés szerint a SQL Database tűzfalon keresztüli hozzáférés engedélyezve van az összes Azure-szolgáltatáshoz, az **Azure-szolgáltatásokhoz való hozzáférés engedélyezése**területen. Az összes Azure-szolgáltatáshoz való hozzáférés letiltásához válassza a **ki** lehetőséget.

### <a name="setup-database-firewall-rules"></a>Adatbázis-tűzfalszabályok beállítása

Az adatbázis-szintű tűzfalszabályok csak az egyes adatbázisokra érvényesek. Az adatbázis a kiszolgálók feladatátvétele során megőrzi ezeket a szabályokat. Az adatbázis-szintű tűzfalszabályok csak Transact-SQL (T-SQL) utasításokkal konfigurálhatók, és csak a kiszolgálói szintű tűzfalszabály konfigurálása után.

Adatbázis szintű tűzfalszabály beállítása:

1. Kapcsolódjon az adatbázishoz, például [SQL Server Management Studio](./sql-database-connect-query-ssms.md)használatával.

1. **Object Explorer**kattintson a jobb gombbal az adatbázisra, és válassza az **Új lekérdezés**elemet.

1. A lekérdezési ablakban adja hozzá ezt az utasítást, és módosítsa az IP-címet a nyilvános IP-címére:

    ```sql
    EXECUTE sp_set_database_firewall_rule N'Example DB Rule','0.0.0.4','0.0.0.4';
    ```

1. Az eszköztáron kattintson az **Execute (végrehajtás** ) elemre a tűzfalszabály létrehozásához.

> [!NOTE]
> A [sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database?view=azuresqldb-current) paranccsal is létrehozhat egy kiszolgálói szintű TŰZFALSZABÁLYOK a SSMS-ben, de a *Master* adatbázishoz kell csatlakoznia.

## <a name="create-an-azure-ad-admin"></a>Azure AD-rendszergazda létrehozása

Győződjön meg arról, hogy a megfelelő Azure Active Directory (AD) felügyelt tartományt használja. Az AD-tartomány kiválasztásához használja a Azure Portal jobb felső sarkát. Ez a folyamat megerősíti, hogy ugyanazt az előfizetést használja az Azure AD-hez és az Azure SQL Database-t vagy adattárházat üzemeltető SQL Server.

   ![Válasszon-ad](./media/sql-database-security-tutorial/8choose-ad.png)

Az Azure AD-rendszergazda beállítása:

1. Azure Portal az **SQL Server** lapon válassza a **Active Directory rendszergazda**lehetőséget. Ezután válassza a **rendszergazda beállítása**lehetőséget.

    ![az Active Directory kiválasztása](./media/sql-database-security-tutorial/admin-settings.png)  

    > [!IMPORTANT]
    > A feladat végrehajtásához "vállalati rendszergazda" vagy "globális rendszergazda" értékűnek kell lennie.

1. A **rendszergazda hozzáadása** lapon keresse meg és válassza ki az ad felhasználót vagy csoportot, majd válassza a **kiválasztás**lehetőséget. A Active Directory összes tagja és csoportja szerepel a felsorolásban, és a szürkén megadott bejegyzések nem támogatottak Azure AD-rendszergazdákként. Lásd: [Azure ad-szolgáltatások és korlátozások](sql-database-aad-authentication.md#azure-ad-features-and-limitations).

    ![rendszergazda kiválasztása](./media/sql-database-security-tutorial/admin-select.png)

    > [!IMPORTANT]
    > A szerepköralapú hozzáférés-vezérlés (RBAC) csak a portálra vonatkozik, és a rendszer nem propagálja SQL Serverra.

1. A **Active Directory-rendszergazda** lap tetején válassza a **Mentés**lehetőséget.

    A rendszergazdák módosításának folyamata több percet is igénybe vehet. Az új rendszergazda megjelenik a **Active Directory felügyeleti** mezőben.

> [!NOTE]
> Az Azure AD-rendszergazda beállításakor az új rendszergazda neve (felhasználó vagy csoport) nem létezhet SQL Server hitelesítési felhasználóként a *Master* adatbázisban. Ha van ilyen, a telepítés sikertelen lesz, és visszaállítja a módosításokat, ami azt jelzi, hogy a rendszergazda neve már létezik. Mivel a SQL Server hitelesítési felhasználó nem része az Azure AD-nek, a felhasználó Azure AD-hitelesítéssel való összekapcsolására tett erőfeszítések meghiúsulnak.

Az Azure AD konfigurálásával kapcsolatos információkért lásd:

- [Helyszíni identitások integrálása az Azure AD-vel](../active-directory/hybrid/whatis-hybrid-identity.md)
- [Saját tartománynév hozzáadása az Azure AD-hez](../active-directory/active-directory-domains-add-azure-portal.md)
- [A Microsoft Azure mostantól támogatja a Windows Server Active Directoryval való összevonást](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)
- [Az Azure AD-címtár felügyelete](../active-directory/fundamentals/active-directory-administer.md)
- [Az Azure AD kezelése a PowerShell használatával](/powershell/azure/overview?view=azureadps-2.0)
- [Hibrid identitáshoz szükséges portok és protokollok](../active-directory/hybrid/reference-connect-ports.md)

## <a name="manage-database-access"></a>Adatbázis-hozzáférés kezelése

Az adatbázis-hozzáférés kezeléséhez felhasználókat adhat hozzá az adatbázishoz, vagy engedélyezheti a felhasználók hozzáférését a biztonságos kapcsolati karakterláncokkal. A külső alkalmazások esetében hasznos a kapcsolatok karakterlánca. További információ: [Azure SQL hozzáférés-vezérlés](sql-database-control-access.md) és [AD-hitelesítés](sql-database-aad-authentication.md).

Felhasználók hozzáadásához válassza ki az adatbázis-hitelesítés típusát:

- **SQL-hitelesítés**, Felhasználónév és jelszó használata a bejelentkezésekhez, és csak a kiszolgálón belüli adott adatbázis kontextusában érvényesek.

- **Azure ad-hitelesítés**, az Azure ad által felügyelt identitások használata

### <a name="sql-authentication"></a>SQL-hitelesítés

SQL-hitelesítéssel rendelkező felhasználó hozzáadása:

1. Kapcsolódjon az adatbázishoz, például [SQL Server Management Studio](./sql-database-connect-query-ssms.md)használatával.

1. **Object Explorer**kattintson a jobb gombbal az adatbázisra, és válassza az **Új lekérdezés**elemet.

1. A lekérdezési ablakban adja meg a következő parancsot:

    ```sql
    CREATE USER ApplicationUser WITH PASSWORD = 'YourStrongPassword1';
    ```

1. Az eszköztáron kattintson az **Execute (végrehajtás** ) elemre a felhasználó létrehozásához.

1. Alapértelmezés szerint a felhasználó csatlakozhat az adatbázishoz, de nem jogosult adatok olvasására vagy írására. Az engedélyek megadásához hajtsa végre a következő parancsokat egy új lekérdezési ablakban:

    ```sql
    ALTER ROLE db_datareader ADD MEMBER ApplicationUser;
    ALTER ROLE db_datawriter ADD MEMBER ApplicationUser;
    ```

> [!NOTE]
> Nem rendszergazdai fiókokat hozhat létre az adatbázis szintjén, kivéve, ha olyan rendszergazdai feladatokat kell végrehajtania, mint például az új felhasználók létrehozása.

### <a name="azure-ad-authentication"></a>Azure AD-hitelesítés

Azure Active Directory hitelesítéshez az adatbázis-felhasználók létrehozása szükséges. Egy tárolt adatbázis-felhasználó egy, az adatbázishoz társított Azure AD-címtárban található identitáshoz van társítva, és nincs bejelentkezés a *Master* adatbázisban. Az Azure AD-identitás lehet egyéni felhasználó vagy csoport is. További információ: a [tárolt adatbázis-felhasználók, az adatbázis hordozható,](https://msdn.microsoft.com/library/ff929188.aspx) valamint az Azure ad-vel történő hitelesítéssel kapcsolatos [Azure ad-oktatóanyag](./sql-database-aad-authentication-configure.md) áttekintése.

> [!NOTE]
> Az adatbázis-felhasználók (a rendszergazdák kivételével) nem hozhatók létre a Azure Portal használatával. Az Azure RBAC-szerepkörök nem terjednek ki az SQL-kiszolgálókra,-adatbázisokra vagy-adattárházra. Ezeket csak az Azure-erőforrások kezelésére használják, és nem vonatkoznak az adatbázis engedélyeire.
>
> A *SQL Server közreműködő* szerepkör például nem biztosít hozzáférést az adatbázishoz vagy az adattárházhoz való kapcsolódáshoz. Ezt az engedélyt T-SQL-utasítások használatával kell megadni az adatbázisban.

> [!IMPORTANT]
> A T-SQL `:` `&` `CREATE LOGIN` és`CREATE USER` a utasításokban nem támogatottak a speciális karakterek, például a kettőspont vagy az jel.

Azure AD-hitelesítéssel rendelkező felhasználó hozzáadása:

1. Kapcsolódjon az Azure SQL Serverhez egy Azure AD-fiókkal legalább az *Alter any User* engedély használatával.

1. **Object Explorer**kattintson a jobb gombbal az adatbázisra, és válassza az **Új lekérdezés**elemet.

1. A lekérdezési ablakban írja be a következő parancsot, és `<Azure_AD_principal_name>` módosítsa az Azure ad-felhasználó egyszerű nevére vagy az Azure ad-csoport megjelenítendő nevére:

   ```sql
   CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
   ```

> [!NOTE]
> Az Azure ad-felhasználók a csoportok típusával `E (EXTERNAL_USER)` és típusával `X (EXTERNAL_GROUPS)` vannak megjelölve az adatbázis-metaadatokban. További információ: [sys. database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql).

### <a name="secure-connection-strings"></a>Biztonságos kapcsolatok karakterláncai

Az ügyfélalkalmazás és az SQL Database közötti biztonságos, titkosított kapcsolat biztosítása érdekében a kapcsolati karakterláncot a következőkre kell konfigurálni:

- Titkosított kapcsolatok kérése
- Nem bízik meg a kiszolgálói tanúsítványban

A rendszer Transport Layer Security (TLS) használatával hozza meg a kapcsolatokat, és csökkenti a támadók általi támadás kockázatát. A kapcsolódási karakterláncok adatbázison keresztül érhetők el, és előre konfigurálva vannak az olyan ügyfelek illesztőprogramjainak támogatásához, mint például a ADO.NET, a JDBC, az ODBC és a PHP. További információ a TLS-ről és a kapcsolatokról: [A TLS megfontolandó szempontjai](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity).

Biztonságos kapcsolódási sztring másolása:

1. Azure Portal a bal oldali menüben válassza az **SQL-adatbázisok** lehetőséget, majd válassza ki az adatbázist az **SQL-adatbázisok** lapon.

1. Az **Áttekintés** lapon válassza az **adatbázis-kapcsolati karakterláncok megjelenítése**lehetőséget.

1. Válassza ki az illesztőprogram fület, és másolja a teljes hálózati karakterláncot.

    ![ADO.NET kapcsolati sztring](./media/sql-database-security-tutorial/connection.png)

## <a name="enable-security-features"></a>Biztonsági funkciók engedélyezése

A Azure SQL Database a Azure Portal használatával elérhető biztonsági funkciókat biztosít. Ezek a funkciók mind az adatbázis, mind a kiszolgáló számára elérhetők, kivéve az adatmaszkolást, amely csak az adatbázison érhető el. További információ: [speciális adatbiztonság](sql-database-advanced-data-security.md), [naplózás](sql-database-auditing.md), [dinamikus adatmaszkolás](sql-database-dynamic-data-masking-get-started.md)és [transzparens adattitkosítás](transparent-data-encryption-azure-sql.md).

### <a name="advanced-data-security"></a>Advanced Data Security

A speciális adatbiztonsági funkció észleli a lehetséges veszélyforrásokat, és biztonsági riasztásokat biztosít a rendellenes tevékenységekről. A felhasználók a naplózási funkcióval megismerhetik ezeket a gyanús eseményeket, és meghatározhatják, hogy az esemény az adatbázisban való hozzáféréshez, az illetéktelen behatoláshoz vagy a biztonsági rés kihasználásához kapcsolódott-e. A felhasználók biztonsági áttekintést is kapnak, amely tartalmazza a sebezhetőségi felmérést, valamint az adatfelderítési és besorolási eszközt.

> [!NOTE]
> Ilyen veszélyforrás például az SQL-injektálás, amely során a támadók rosszindulatú SQL-adatokat szúrnak be az alkalmazásba. Egy alkalmazás ezután nem tudja végrehajtani a rosszindulatú SQL-t, és lehetővé teszi a támadók számára, hogy hozzáférjenek az adatbázishoz, vagy módosíthassák azokat.

A speciális adatbiztonság engedélyezése:

1. Azure Portal a bal oldali menüben válassza az **SQL-adatbázisok** lehetőséget, majd válassza ki az adatbázist az **SQL-adatbázisok** lapon.

1. Az **Áttekintés** lapon válassza a **kiszolgáló neve** hivatkozást. Ekkor megnyílik az adatbázis-kiszolgáló lap.

1. Az **SQL Server** oldalon keresse meg a **Biztonság** szakaszt, és válassza a **speciális adatbiztonság**lehetőséget.

   1. A funkció engedélyezéséhez válassza a be lehetőséget **a** **speciális adatbiztonság** területen. Válasszon egy Storage-fiókot a sebezhetőségi felmérés eredményeinek mentéséhez. Ezután válassza a **Save** (Mentés) lehetőséget.

      ![Navigációs ablak](./media/sql-database-security-tutorial/threat-settings.png)

      Az e-maileket is konfigurálhatja a biztonsági riasztások, a tárolási adatok és a veszélyforrások észlelési típusai fogadására.

1. Térjen vissza az adatbázis **SQL-adatbázisok** lapjára, és válassza a **Biztonság** szakaszban a speciális Adatbiztonság lehetőséget. Itt számos, az adatbázishoz elérhető biztonsági mutatót talál.

    ![Fenyegetés állapota](./media/sql-database-security-tutorial/threat-status.png)

Ha a rendszer rendellenes tevékenységeket észlel, az eseményre vonatkozó információkat tartalmazó e-mailt kap. Ez magában foglalja a tevékenység, az adatbázis, a kiszolgáló, az esemény időpontja, a lehetséges okok és a javasolt műveletek természetét a lehetséges fenyegetés kivizsgálásához és enyhítéséhez. Ha ilyen e-mailt kap, válassza az **Azure SQL-naplózási napló** hivatkozást, hogy elindítsa a Azure Portal és megjeleníti a kapcsolódó naplózási rekordokat az esemény időpontjában.

   ![Fenyegetésészlelési e-mail](./media/sql-database-security-tutorial/threat-email.png)

### <a name="auditing"></a>Naplózás

Az auditálási szolgáltatás nyomon követi az adatbázis eseményeit, és az eseményeket egy Azure Storage-ban, Azure Monitor naplókban vagy egy Event hub-naplóba írja. Az auditálás segítségével megtarthatja a szabályozás megfelelőségét, megismerheti az adatbázis-tevékenységeket, és betekintést nyerhet a lehetséges biztonsági szabálysértéseket jelező eltérésekre és rendellenességekre.

A naplózás engedélyezése:

1. Azure Portal a bal oldali menüben válassza az **SQL-adatbázisok** lehetőséget, majd válassza ki az adatbázist az **SQL-adatbázisok** lapon.

1. A **Biztonság** szakaszban válassza a **naplózás**lehetőséget.

1. A **naplózási** beállítások területen állítsa be a következő értékeket:

   1. A **naplózás** beállítása **a**következőre:.

   1. Válassza ki a **napló célhelyét** az alábbi módon:

       - **Storage**, egy Azure Storage-fiók, amely az eseménynaplókat menti, és *. xel* fájlként tölthető le.

          > [!TIP]
          > Ugyanazt a Storage-fiókot használja az összes auditált adatbázishoz, hogy a lehető legtöbbet hozza ki a jelentési sablonokból.

       - **Log Analytics**, amely automatikusan tárolja az eseményeket lekérdezés vagy további elemzés céljából

           > [!NOTE]
           > **Log Analytics munkaterület** szükséges a speciális funkciók, például az elemzés, az egyéni riasztási szabályok és az Excel-vagy Power bi-exportálás támogatásához. Munkaterület nélkül csak a lekérdezés-szerkesztő érhető el.

       - **Event hub**, amely lehetővé teszi, hogy az események más alkalmazásokban való használatra legyenek átirányítva

   1. Kattintson a **Mentés** gombra.

      ![Naplózási beállítások](./media/sql-database-security-tutorial/audit-settings.png)

1. Most kiválaszthatja a **naplózási naplók megtekintése** lehetőséget az adatbázis-események adatmegjelenítéséhez.

    ![Naplórekordok](./media/sql-database-security-tutorial/audit-records.png)

> [!IMPORTANT]
> Lásd: az [SQL Database naplózása](sql-database-auditing.md) a naplózási események további testreszabásához a PowerShell vagy a REST API használatával.

### <a name="dynamic-data-masking"></a>Dinamikus adatmaszkolás

Az adatmaszkolási funkció automatikusan elrejti a bizalmas adatokat az adatbázisban.

Az adatmaszkolás engedélyezése:

1. Azure Portal a bal oldali menüben válassza az **SQL-adatbázisok** lehetőséget, majd válassza ki az adatbázist az **SQL-adatbázisok** lapon.

1. A **Biztonság** szakaszban válassza a **dinamikus adatmaszkolás**lehetőséget.

1. A **dinamikus** adatmaszkolási beállítások területen válassza a **maszk hozzáadása** lehetőséget a maszkolási szabály hozzáadásához. Az Azure automatikusan feltölti az elérhető adatbázis-sémákat, táblákat és oszlopokat, amelyek közül választhat.

    ![Maszk beállításai](./media/sql-database-security-tutorial/mask-settings.png)

1. Kattintson a **Mentés** gombra. A kiválasztott információk mostantól az adatvédelem során maszkoltak.

    ![Példa maszkra](./media/sql-database-security-tutorial/mask-query.png)

### <a name="transparent-data-encryption"></a>Transzparens adattitkosítás

A titkosítási funkció automatikusan titkosítja az adatait, és nem igényel módosításokat a titkosított adatbázishoz hozzáférő alkalmazásokban. Az új adatbázisok esetében a titkosítás alapértelmezés szerint be van kapcsolva. Az SSMS és az [Always encrypted](sql-database-always-encrypted.md) funkció használatával is titkosíthatja az adattitkosítást.

A titkosítás engedélyezése vagy ellenőrzése:

1. Azure Portal a bal oldali menüben válassza az **SQL-adatbázisok** lehetőséget, majd válassza ki az adatbázist az **SQL-adatbázisok** lapon.

1. A **Biztonság** szakaszban válassza az **transzparens adattitkosítás**lehetőséget.

1. Ha szükséges, állítsa be az adattitkosítást **a**következőre:. Kattintson a **Mentés** gombra.

    ![Transzparens adattitkosítás](./media/sql-database-security-tutorial/encryption-settings.png)

> [!NOTE]
> A titkosítási állapot megtekintéséhez kapcsolódjon az adatbázishoz az [SSMS](./sql-database-connect-query-ssms.md) használatával, és `encryption_state` kérdezze le a [sys. DM _database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) nézet oszlopát. A állapot `3` azt jelzi, hogy az adatbázis titkosítva van.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogy csak néhány egyszerű lépéssel javítsa az adatbázis biztonságát. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> - Kiszolgálói szintű és adatbázis-szintű tűzfalszabályok létrehozása
> - Azure Active Directory (AD) rendszergazda konfigurálása
> - Felhasználói hozzáférés kezelése SQL-hitelesítéssel, Azure AD-hitelesítéssel és biztonságos kapcsolati karakterláncokkal
> - Engedélyezheti a biztonsági funkciókat, például a speciális adatbiztonságot, a naplózást, az adatmaszkolást és a titkosítást

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan valósítható meg a Geo-eloszlás.

> [!div class="nextstepaction"]
>[Földrajzilag elosztott adatbázis implementálása](sql-database-implement-geo-distributed-database.md)
