---
title: Ajánlott biztonsági eljárások a Azure SQL Databasehoz | Microsoft Docs
description: Ez a cikk általános útmutatást nyújt az Azure SQL Database-ben bevált biztonsági eljárásokhoz.
ms.service: sql-database
ms.subservice: security
author: VanMSFT
ms.author: vanto
ms.topic: article
ms.date: 12/23/2019
ms.reviewer: ''
ms.openlocfilehash: 82297850bf6d03215963a1f81dda166550f2b0d5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76715179"
---
# <a name="azure-sql-database-security-best-practices-playbook"></a>Azure SQL Database ajánlott biztonsági eljárások

## <a name="overview"></a>Áttekintés

Ez a dokumentum útmutatást nyújt az új vagy meglévő alkalmazások Azure SQL Database-t használó gyakori biztonsági követelményeinek megoldásához. Ezt magas szintű biztonsági területek szervezik. Az adott veszélyforrások kezeléséhez tekintse meg a [gyakori biztonsági fenyegetéseket és a lehetséges kockázatcsökkentő](#common-security-threats-and-potential-mitigations) szakaszokat. Bár a bemutatott javaslatok némelyike az alkalmazások helyszíni rendszerből az Azure-ba történő áttelepítésekor alkalmazható, az áttelepítési forgatókönyvek nem a dokumentum középpontjában állnak.

### <a name="azure-sql-database-deployment-offers-covered-in-this-guide"></a>Azure SQL Database az útmutatóban foglalt központi telepítési ajánlatok

- [SQL-adatbázisok](https://docs.microsoft.com/azure/sql-database/sql-database-single-index): [önálló adatbázisok](sql-database-single-database.md) és [rugalmas készletek](sql-database-elastic-pool.md) [Azure SQL Database-kiszolgálókon](sql-database-servers.md)
- [Felügyelt példányok](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index)

### <a name="sql-deployment-offers-not-covered-in-this-guide"></a>A jelen útmutatóban nem szereplő SQL-telepítési ajánlatok

- Azure SQL Data Warehouse
- Azure SQL virtuális gépek (IaaS)
- Helyszíni SQL Server

### <a name="audience"></a>Közönség

Az útmutató célközönségei a Azure SQL Database biztonságossá tételével kapcsolatos kérdések. Az ebben az ajánlott eljárásban érintett szerepkörök a következők:

- Biztonsági építészek
- Biztonsági vezetők
- Megfelelőségi tisztviselők
- Adatvédelmi tisztviselők
- Biztonsági mérnökök

### <a id="using"></a>Az útmutató használata

A dokumentum célja, hogy a meglévő [Azure SQL Database biztonsági](sql-database-security-overview.md) dokumentációhoz tartozó társa legyen.

Ha másként nincs megadva, javasoljuk, hogy kövesse az egyes szakaszokban felsorolt ajánlott eljárásokat a megfelelő cél vagy követelmény eléréséhez. Az egyes biztonsági megfelelőségi szabványoknak vagy ajánlott eljárásoknak megfelelően a követelmények vagy célok szakaszban felsorolt fontos szabályozási megfelelőségi szabályozások, ahol alkalmazhatók. Ezek a dokumentumban hivatkozott biztonsági szabványok és rendeletek:

- [FedRAMP](https://www.fedramp.gov/documents/): AC-04, AC-06
- [SoC](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/sorhome.html): cm-3, SDL-3
- [ISO/IEC 27001](https://www.iso27001security.com/html/27001.html): Access Control, kriptográfia
- [Microsoft Operational Security Assurance (OSA) eljárások](https://www.microsoft.com/en-us/securityengineering/osa/practices): gyakorlat #1 – 6 és #9
- [NIST speciális kiadvány 800-53 biztonsági vezérlők](https://nvd.nist.gov/800-53): AC-5, AC-6
- [PCI DSS](https://www.pcisecuritystandards.org/document_library): 6.3.2, 6.4.2

### <a name="feedback"></a>Visszajelzés

Azt tervezzük, hogy továbbra is frissítjük az itt felsorolt ajánlásokat és ajánlott eljárásokat. Adja meg a dokumentum bemenetét vagy helyesbítéseit a cikk alján található **visszajelzési** hivatkozás használatával.

## <a name="authentication"></a>Authentication

A hitelesítés az a folyamat, amellyel a felhasználó igazolni kívánja. Azure SQL Database két hitelesítési típust támogat:

- SQL-hitelesítés
- Azure Active Directory-alapú hitelesítés

### <a name="central-management-for-identities"></a>Identitások központi kezelése

A központi Identitáskezelés a következő előnyöket kínálja:

- A csoportfiókok kezelése és a felhasználói engedélyek szabályozása anélkül, hogy a bejelentkezések megkettőzése Azure SQL Database-kiszolgálók és-adatbázisok között.
- Egyszerűsített és rugalmas engedélyek kezelése.
- Alkalmazások felügyelete nagy léptékben.

**Megvalósítás**:

- Használja a Azure Active Directory (Azure AD) hitelesítést a központosított Identitáskezelés kezeléséhez.

**Ajánlott eljárások**:

- Hozzon létre egy Azure AD-bérlőt, és [hozzon létre felhasználókat](../active-directory/fundamentals/add-users-azure-active-directory.md) az emberi felhasználók képviseletére, és hozzon létre [egyszerű szolgáltatásokat](../active-directory/develop/app-objects-and-service-principals.md) az alkalmazások, szolgáltatások és automatizálási eszközök megjelenítéséhez. Az egyszerű szolgáltatások a Windows és a Linux szolgáltatás fiókjainak felelnek meg. 

- Hozzáférési jogosultságok hozzárendelése az erőforrásokhoz az Azure AD résztvevői számára csoportos hozzárendelés használatával: Azure AD-csoportok létrehozása, hozzáférés biztosítása a csoportokhoz, és az egyes tagok hozzáadása a csoportokhoz. Az adatbázisban hozzon létre olyan tárolt adatbázis-felhasználókat, akik leképezik az Azure AD-csoportokat. 
  - Tekintse meg a cikkeket, [konfigurálja és kezelje Azure Active Directory hitelesítést az SQL](sql-database-aad-authentication-configure.md) -sel, és [használja az Azure ad-t az SQL-hitelesítéshez](sql-database-aad-authentication.md).
  > [!NOTE]
  > Felügyelt példányban létrehozhat olyan bejelentkezéseket is, amelyek az Azure AD-rendszerbiztonsági tag számára a Master adatbázisban képezhetők le. Lásd: [create login (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current).

- Az Azure AD-csoportok használata leegyszerűsíti az engedélyek kezelését és a csoport tulajdonosát, és az erőforrás tulajdonosa hozzáadhat/eltávolíthat tagokat a csoporthoz/csoportba. 

- Hozzon létre egy külön csoportot az Azure AD-rendszergazda számára az SQL DB-kiszolgálókhoz.

  - Tekintse meg a [Azure SQL Database-kiszolgáló Azure Active Directory rendszergazdájának kiépítése](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)című cikket.

- Az Azure AD-csoporttagság változásainak figyelése az Azure AD naplózási tevékenységeinek jelentéseivel. 

- Felügyelt példány esetén külön lépésre van szükség az Azure AD-rendszergazda létrehozásához. 
  - Tekintse meg a [felügyelt példányok Azure Active Directory rendszergazdájának kiépítése](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance)című cikket. 

> [!NOTE]
> - Az Azure AD-hitelesítés az Azure SQL-naplókban van rögzítve, de nem az Azure AD bejelentkezési naplóiban.
> - Az Azure-ban megadott RBAC engedélyek nem vonatkoznak az Azure SQL DB engedélyeire. Ezeket az engedélyeket manuálisan kell létrehozni/leképezni az SQL-adatbázisban meglévő SQL-engedélyek használatával.
> - Az ügyféloldali Azure AD-hitelesítéshez hozzá kell férnie az internethez, vagy a felhasználó által megadott útvonalon (UDR) keresztül egy VNet.
> - Az Azure AD hozzáférési jogkivonat gyorsítótárazva van az ügyféloldali oldalon, és az élettartama a jogkivonat konfigurációjától függ. Tekintse meg a következő cikket: [konfigurálható jogkivonat élettartama Azure Active Directory](../active-directory/develop/active-directory-configurable-token-lifetimes.md)

### <a name="multi-factor-authentication-mfa"></a>Multi-Factor Authentication (MFA)

> [!NOTE]
> Megemlítve: OSA Practice #2, ISO Access Control (AC)

Az Azure Multi-Factor Authentication (MFA) további biztonságot nyújt a több hitelesítési módszer megkövetelésével.

**Megvalósítás**:

- A feltételes hozzáférés használatával [engedélyezheti az MFA](../active-directory/authentication/concept-mfa-howitworks.md) használatát az Azure ad-ben, és interaktív hitelesítést használhat. 

- A másik lehetőség az MFA engedélyezése a teljes Azure AD-vagy AD-tartományon.

**Ajánlott eljárások**:

- Aktiválja a feltételes hozzáférést az Azure AD-ben (prémium szintű előfizetés szükséges). 
  - Tekintse meg a [feltételes hozzáférés az Azure ad-ben](../active-directory/conditional-access/overview.md)című cikket.  

- Azure AD-csoport (ok) létrehozása és az MFA-szabályzat engedélyezése a kiválasztott csoportokhoz az Azure AD feltételes hozzáférés használatával. 
  - Tekintse meg a [feltételes hozzáférés üzembe helyezésének megtervezése](../active-directory/conditional-access/plan-conditional-access.md)című cikket. 

- Az MFA engedélyezhető a teljes Azure AD-hez vagy az Azure AD-vel összevont teljes Active Directoryhoz. 

- Használja az Azure AD interaktív hitelesítési módot az SQL-ADATBÁZIShoz, ahol a jelszót interaktívan kérik, majd az MFA-hitelesítéssel:      
  - Használjon univerzális hitelesítést a SSMS-ben. Tekintse meg a [multi-Factor HRE-hitelesítés Azure SQL Database és Azure SQL Data Warehouse használatával történő használatát ismertető cikket (SSMS-támogatás az MFA-hoz)](sql-database-ssms-mfa-authentication.md).
  - SQL Server Data Tools (SSDT) által támogatott interaktív hitelesítés használata. Tekintse meg a [SQL Server Data Tools (SSDT) Azure Active Directory támogatását](https://docs.microsoft.com/sql/ssdt/azure-active-directory?view=azuresqldb-current)ismertető cikket.
  - Más, MFA-t támogató SQL-eszközök használata. 
    - A SSMS varázsló támogatja az adatbázis exportálását/kinyerését és üzembe helyezését  
    - [sqlpackage. exe](https://docs.microsoft.com/sql/tools/sqlpackage): "/ua" beállítás 
    - [Sqlcmd segédprogram](https://docs.microsoft.com/sql/tools/sqlcmd-utility): Option-G (interaktív)
    - [BCP-segédprogram](https://docs.microsoft.com/sql/tools/bcp-utility): Option-G (interaktív) 

- Az alkalmazások implementálása a Azure SQL Database való kapcsolódáshoz az MFA-támogatással rendelkező interaktív hitelesítés használatával. 
  - Tekintse meg a következő cikket: [Kapcsolódás a Azure SQL Databasehoz az Azure multi-Factor Authentication használatával](active-directory-interactive-connect-azure-sql-db.md). 
  > [!NOTE]
  > Ez a hitelesítési mód felhasználói identitásokat igényel. Olyan esetekben, amikor olyan megbízható identitású modellt használ, amely megkerüli az egyes Azure AD-felhasználók hitelesítését (például az Azure-erőforrások felügyelt identitásának használatával), az MFA nem alkalmazható.

### <a name="minimize-the-use-of-password-based-authentication-for-users"></a>A jelszó-alapú hitelesítés használatának csökkentése a felhasználók számára

> [!NOTE]
> Megemlítve: OSA Practice #4, ISO Access Control (AC)

A jelszó-alapú hitelesítési módszerek a hitelesítés gyengébb formája. A hitelesítő adatok biztonsága vagy tévedése nem lehetséges.

**Megvalósítás**:

- Használjon olyan integrált Azure AD-hitelesítést, amely kiküszöböli a jelszavak használatát.

**Ajánlott eljárások**:

- Egyszeri bejelentkezéses hitelesítés használata Windows rendszerbeli hitelesítő adatokkal. Összevonása a helyszíni AD-tartományt az Azure AD-vel, és integrált Windows-hitelesítést használ (tartományhoz csatlakoztatott gépekhez az Azure AD-vel).
  - Tekintse meg az [Azure ad integrált hitelesítésének SSMS-támogatását](sql-database-aad-authentication-configure.md#active-directory-integrated-authentication)ismertető cikket.

### <a name="minimize-the-use-of-password-based-authentication-for-applications"></a>A jelszó-alapú hitelesítés használatának csökkentése az alkalmazásoknál 

> [!NOTE]
> Megemlítve: OSA Practice #4, ISO Access Control (AC)

**Megvalósítás**:

- Az Azure által felügyelt identitás engedélyezése. Használhat integrált vagy tanúsítványalapú hitelesítést is. 

**Ajánlott eljárások**:

- [Felügyelt identitások használata az Azure-erőforrásokhoz](../active-directory/managed-identities-azure-resources/overview.md).
  - [Rendszer által hozzárendelt felügyelt identitás](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md) 
  - [Felhasználó által hozzárendelt felügyelt identitás](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
  - [Azure SQL Database használata az App Service-ben felügyelt identitással (kód módosítása nélkül)](https://github.com/Azure-Samples/app-service-msi-entityframework-dotnet)

- Tanúsítvány alapú hitelesítés használata alkalmazásokhoz. 
  - Tekintse meg ezt a [mintakód-mintát](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth/token). 

- Az Azure AD-hitelesítést az integrált összevont tartományhoz és a tartományhoz csatlakoztatott számítógépekhez használhatja (lásd a fenti szakaszt).
  - Tekintse [meg az integrált hitelesítéshez használható minta alkalmazást](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth/integrated).

### <a name="protect-passwords-and-secrets"></a>Jelszavak és titkos kódok elleni védelem

Olyan esetekben, amikor a jelszavak nem elkerülhetők, győződjön meg róla, hogy biztonságosak.

**Megvalósítás**:

- A jelszavak és a titkos kódok tárolására Azure Key Vault használhatja. Ha alkalmazható, használja az MFA-t Azure SQL Database Azure AD-felhasználókkal.

**Ajánlott eljárások**:

- Ha nem sikerül elkerülni a jelszavakat vagy a titkos kulcsokat, a felhasználói jelszavakat és az alkalmazás titkos kulcsait a Azure Key Vault és Key Vault hozzáférési házirendeken keresztül kezelheti. 

- A különböző alkalmazásfejlesztés-keretrendszerek olyan keretrendszer-specifikus mechanizmusokat is kínálnak, amelyek védik a titkokat az alkalmazásban. Például: [ASP.net Core alkalmazás](https://docs.microsoft.com/aspnet/core/security/app-secrets?view=aspnetcore-2.1&tabs=windows).

### <a name="use-sql-authentication-for-legacy-applications"></a>SQL-hitelesítés használata örökölt alkalmazásokhoz 

Az SQL-hitelesítés egy felhasználó hitelesítésére utal, amikor a felhasználónévvel és a jelszóval csatlakozik Azure SQL Databasehoz. A bejelentkezést minden SQL Database-kiszolgálón vagy felügyelt példányon létre kell hozni, valamint egy, az egyes adatbázisokban létrehozott felhasználót.

**Megvalósítás**:

- SQL-hitelesítés használata.

**Ajánlott eljárások**:

- Kiszolgáló-rendszergazdaként hozzon létre bejelentkezéseket és felhasználókat. Hacsak nem használ a jelszóval rendelkező tárolt adatbázis-felhasználókat, a rendszer az összes jelszót a Master adatbázisban tárolja.
  - Tekintse meg a következő cikket: az [adatbázis-hozzáférés szabályozása és biztosítása SQL Database és SQL Data Warehouse](sql-database-manage-logins.md).

- Kövesse a jelszó-kezelési ajánlott eljárásokat:
  - Adjon meg egy összetett jelszót, amely latin betűs és kisbetűkből, számjegyből (0-9) és nem alfanumerikus karakterből áll (például $,!, # vagy%).
  - Rövidebb, véletlenszerűen kiválasztott karakterek helyett használjon hosszabb hozzáférési kódot.
  - A jelszó manuális módosításának kikényszerítés legalább 90 naponta.

## <a name="access-management"></a>Hozzáférés-kezelés

A hozzáférés-kezelés a jogosult felhasználók hozzáférésének és a Azure SQL Databasehoz való jogosultságának szabályozása és kezelése.

### <a name="implement-principle-of-least-privilege"></a>A legalacsonyabb jogosultsági szint megvalósítási elve

> [!NOTE]
> Megemlítve: FedRamp Controls AC-06, NIST: AC-6, OSA Practice #3

A minimális jogosultság elve szerint a felhasználóknak a feladataik elvégzéséhez a szükségesnél több jogosultsággal kell rendelkezniük. További információ: [elég adminisztráció](https://docs.microsoft.com/powershell/scripting/learn/remoting/jea/overview).

**Megvalósítás**:

Csak a szükséges [engedélyek](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) kiosztása a szükséges feladatok elvégzéséhez:

- Az SQL-adatsíkon: 
    - Használjon részletes engedélyeket és felhasználó által definiált adatbázis-szerepköröket (vagy kiszolgálói szerepköröket a MI): 
        1. A szükséges szerepkörök létrehozása
            - [SZEREPKÖR LÉTREHOZÁSA](https://docs.microsoft.com/sql/t-sql/statements/create-role-transact-sql)
            - [KISZOLGÁLÓI SZEREPKÖR LÉTREHOZÁSA](https://docs.microsoft.com/sql/t-sql/statements/create-server-role-transact-sql)
        1. Szükséges felhasználók létrehozása
            - [FELHASZNÁLÓ LÉTREHOZÁSA](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql)
        1. Felhasználók hozzáadása tagként a szerepkörökhöz 
            - [SZEREPKÖR MÓDOSÍTÁSA](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql)
            - [A KISZOLGÁLÓI SZEREPKÖR MÓDOSÍTÁSA](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql)
        1. Ezután rendeljen engedélyeket a szerepkörökhöz. 
            - [GRANT](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) 
    - Ügyeljen arra, hogy ne rendeljen hozzá felhasználókat felesleges szerepkörökhöz.

- Azure Resource Manager:
  - Ha elérhető vagy egyéni RBAC-szerepköröket használ, használja a beépített szerepköröket, és rendelje hozzá a szükséges engedélyeket.
    - [Az Azure beépített szerepkörei](../role-based-access-control/built-in-roles.md) 
    - [Egyéni szerepkörök Azure-erőforrásokhoz](../role-based-access-control/custom-roles.md)

**Ajánlott eljárások**:

A következő ajánlott eljárások nem kötelezőek, de a biztonsági stratégia jobb kezelhetőségét és támogatását eredményezik: 

- Ha lehetséges, kezdje a lehető legkevesebb engedély megadásával, és az engedélyek hozzáadásának megkezdése egyenként, ha valós szükség van rá (és indoklással) – az ellenkező megközelítés helyett: az engedélyek elvégzése lépésről lépésre. 

- Ne rendeljen engedélyeket az egyes felhasználókhoz. A szerepköröket (adatbázis vagy kiszolgálói szerepkörök) következetesen használja helyette. A szerepkörök nagy mértékben segítik a jelentéskészítési és hibaelhárítási engedélyeket. (Az Azure RBAC csak szerepkörökön keresztül támogatja az engedélyek hozzárendelését.) 

- Beépített szerepkörök használata, ha a szerepkörök engedélyei pontosan egyeznek a felhasználóhoz szükséges engedélyekkel. A felhasználókat több szerepkörhöz is hozzárendelheti. 

- Egyéni szerepköröket hozhat létre és használhat, ha a beépített szerepkörök túl sok vagy nem megfelelő jogosultságot biztosítanak. A gyakorlatban használt tipikus szerepkörök: 
  - Biztonsági telepítés 
  - Rendszergazda 
  - Fejlesztői 
  - Támogatási munkatársak 
  - Auditor 
  - Automatizált folyamatok 
  - Végfelhasználó 

- Ne feledje, hogy SQL Server adatbázismotor engedélyei a következő hatókörökön alkalmazhatók. Minél kisebb a hatókör, annál kisebb a megadott engedélyek hatása: 
  - Azure SQL Database kiszolgáló (speciális szerepkörök a Master adatbázisban) 
  - Adatbázis 
  - Séma (lásd még: [séma-Design for SQL Server: a séma kialakítására vonatkozó javaslatok a biztonság szem előtt](http://andreas-wolter.com/en/schema-design-for-sql-server-recommendations-for-schema-design-with-security-in-mind/)tartásával)
  - Objektum (tábla, nézet, eljárás stb.) 
  > [!NOTE]
  > Az objektum szintjén nem ajánlott engedélyeket alkalmazni, mert ez a szint szükségtelen bonyolultságot okoz a teljes megvalósításban. Ha úgy dönt, hogy az objektum szintű engedélyeket használja, ezeket egyértelműen dokumentálni kell. Ugyanez vonatkozik az oszlop szintű engedélyekre is, amelyek ugyanezen okokból még kevésbé ajánlottak. A [Megtagadás](https://docs.microsoft.com/sql/t-sql/statements/deny-object-permissions-transact-sql) standard szabályai nem vonatkoznak az oszlopokra.

- Végezzen rendszeres ellenőrzéseket a [sebezhetőségi felmérés (va)](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment) használatával a túl sok engedély teszteléséhez.

### <a name="implement-separation-of-duties"></a>A feladatok elkülönítésének megvalósítása

> [!NOTE]
> Megemlítve: FedRamp: AC-04, NIST: AC-5, ISO: A. 6.1.2, PCI 6.4.2, SOC: CM-3, SDL-3

A feladatok elkülönítése – más néven a vámok elkülönítése – azt a követelményt írja le, hogy a bizalmas feladatokat több, különböző felhasználókhoz rendelt feladatba ossza fel, általában az adatok megsértésének megelőzése érdekében.

**Megvalósítás**:

- Azonosítsa a feladatok elkülönítésének szükséges szintjét. Példák: 
  - Fejlesztési, tesztelési és éles környezetek között 
  - Biztonság – a bizalmas feladatok és az adatbázis-adminisztrátor (DBA) felügyeleti szintű feladatai és fejlesztői feladatok. 
    - Példák: auditor, biztonsági szabályzat létrehozása szerepköralapú biztonsághoz (RLS), SQL Database objektumok implementálása DDL-engedélyekkel.

- Azonosítsa a rendszerhez hozzáférő felhasználók (és automatizált folyamatok) átfogó hierarchiáját.

- Hozzon létre szerepköröket a szükséges felhasználói csoportoknak megfelelően, és rendeljen engedélyeket a szerepkörökhöz. 
  - A Azure Portal vagy a PowerShell-Automation segítségével történő felügyeleti szintű feladatokhoz RBAC-szerepköröket használjon. Keressen egy beépített szerepkört, amely megfelel a követelménynek, vagy hozzon létre egy egyéni RBAC-szerepkört az elérhető engedélyek használatával. 
  - Kiszolgálói szerepköröket hozhat létre a felügyelt példányok kiszolgáló szintű feladatai számára (új bejelentkezések és adatbázisok létrehozása). 
  - Adatbázis-szintű feladatokhoz tartozó adatbázis-szerepkörök létrehozása.

- Bizonyos bizalmas feladatok esetében érdemes lehet egy tanúsítvány által aláírt speciális tárolt eljárásokat létrehozni a feladatok végrehajtásához a felhasználók nevében. 
  - Példa: [oktatóanyag: tárolt eljárások aláírása tanúsítvánnyal](https://docs.microsoft.com/sql/relational-databases/tutorial-signing-stored-procedures-with-a-certificate) 

- Transzparens adattitkosítás (TDE) implementálása az ügyfél által felügyelt kulcsokkal Azure Key Vault, hogy lehetővé tegye a feladatok elkülönítését az adatok tulajdonosa és a biztonsági tulajdonos között. 
  - Tekintse meg az [Azure Storage-titkosításhoz az ügyfél által felügyelt kulcsok konfigurálása a Azure Portalről](../storage/common/storage-encryption-keys-portal.md)című cikket. 

- Annak biztosítása érdekében, hogy a DBA ne lásson olyan adatokat, amelyek nagyon kényesek, és továbbra is a DBA-feladatokat tudják használni, a szerepkörök elkülönítésével Always Encrypted. 
  - Tekintse át a [Always encrypted kulcskezelő szolgáltatással](https://docs.microsoft.com/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted), a [szerepkör-elkülönítéssel való központi](https://docs.microsoft.com/sql/relational-databases/security/encryption/configure-always-encrypted-keys-using-powershell#KeyProvisionWithRoles)telepítéssel, valamint az [oszlop főkulcsának rotációs szerepkör-elkülönítéssel](https://docs.microsoft.com/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell#column-master-key-rotation-with-role-separation)kapcsolatos cikkeit. 

- Azokban az esetekben, amikor nem valósítható meg legalább olyan jelentős költségek és erőfeszítések nélkül, amelyek a rendszer használhatatlanná tételéhez vezethetnek, az olyan kompenzáló szabályozások használatával, mint például a következők: 
  - Emberi beavatkozás folyamatokban. 
  - Naplózási nyomvonalak – további információ a naplózásról: [kritikus fontosságú biztonsági események naplózása](#audit-critical-security-events).

**Ajánlott eljárások**:

- Győződjön meg arról, hogy a fejlesztési, tesztelési és éles környezetekben különböző fiókok vannak használatban. A különböző fiókok segítenek betartani a tesztelési & éles rendszerek elkülönítését.

- Ne rendeljen engedélyeket az egyes felhasználókhoz. A szerepköröket (adatbázis vagy kiszolgálói szerepkörök) következetesen használja helyette. A szerepkörök nagy mértékben segítik a jelentéskészítési és hibaelhárítási engedélyeket.

- Beépített szerepkörök használata, ha az engedélyek pontosan egyeznek a szükséges engedélyekkel – ha a több beépített szerepkörből álló engedélyek Uniója egy 100%-os egyeztetéshez vezet, több szerepkört is hozzárendelhet egyidejűleg. 

- Egyéni szerepkörök létrehozása és használata, ha a beépített szerepkörök túl sok engedélyt vagy nem megfelelő engedélyeket biztosítanak. 

- A szerepkör-hozzárendelések ideiglenesen is megvalósíthatók, más néven a feladatok dinamikus elkülönítése (DSD), vagy a T-SQL SQL Agent-feladatok lépésein belül, vagy az Azure PIM használata a RBAC-szerepkörökhöz. 

- Győződjön meg arról, hogy a Adattervezők nem férnek hozzá a kulcsok eléréséhez szükséges titkosítási kulcsokhoz, valamint a biztonsági rendszergazdákhoz, és nem fér hozzá az adatbázishoz. 

- Mindig ellenőrizze, hogy van-e naplózási nyomvonal a biztonsággal kapcsolatos műveletekhez. 

- A beépített RBAC-szerepkörök definíciójának beszerzésével megtekintheti a használatban lévő engedélyeket, és létrehozhat egy egyéni szerepkört a részletek és az ezek összesítései alapján a PowerShell használatával 

- Mivel a db_owner adatbázis-szerepkör bármelyik tagja módosíthatja a biztonsági beállításokat, például a transzparens adattitkosítás (TDE), vagy módosíthatja az SLO-t, ezt a tagságot körültekintően kell megadnia. Másfelől számos feladatot, például az adatbázis-beállítások módosítását, db_owner jogosultságokat kell megadnia. A naplózás kulcsfontosságú szerepet játszik bármely megoldásban.

- Nem lehet megőrizni db_owner a felhasználói adatok megtekintését csak engedélyekkel. Ha egy adatbázisban nagyon bizalmas adatok találhatók, Always Encrypted segítségével biztonságosan megakadályozhatja db_owners vagy bármely más DBA megtekintését.

> [!NOTE]
> A feladatok elkülönítésének megvalósítása (SoD) a biztonsággal kapcsolatos vagy hibaelhárítási feladatokra is kihívást jelent. Más területek, például a fejlesztés és a végfelhasználói szerepkörök könnyebben elkülöníthető. A többségi megfelelőséggel kapcsolatos vezérlők lehetővé teszik alternatív vezérlési függvények használatát, például a naplózást, ha más megoldások nem praktikusak.

A SoD-ban mélyebbre szánt olvasóknak a következő erőforrásokat ajánlott használni: 

- Azure SQL Database esetén:  
  - [Adatbázis-hozzáférés szabályozása és biztosítása SQL Database és SQL Data Warehouse számára](sql-database-manage-logins.md)
  - [Az alkalmazás fejlesztője feladatai elkülönítése](https://docs.microsoft.com/previous-versions/sql/sql-server-2008/cc974525(v=sql.100)) 
  - [A vámok elkülönítése SQL Server 2014](https://www.microsoft.com/download/details.aspx?id=39269)
  - [Tárolt eljárások aláírása SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server)

- Azure Resource Management esetén:
  - [Az Azure beépített szerepkörei](../role-based-access-control/built-in-roles.md) 
  - [Egyéni szerepkörök Azure-erőforrásokhoz](../role-based-access-control/custom-roles.md)
  - [A Azure AD Privileged Identity Management használata emelt szintű hozzáféréshez](https://www.microsoft.com/en-us/itshowcase/using-azure-ad-privileged-identity-management-for-elevated-access)

### <a name="perform-regular-code-reviews"></a>Szabványos kód-felülvizsgálatok végrehajtása

> [!NOTE]
> Megemlítve: PCI: 6.3.2, SOC: SDL-3 

A feladatok elkülönítése nem korlátozódik az adatbázisban lévő adatokra, de tartalmazza az alkalmazás kódját. A kártékony kódok megkerülhetik a biztonsági vezérlőket. Az egyéni kód éles környezetben való üzembe helyezése előtt elengedhetetlen, hogy áttekintse az üzembe helyezett környezeteket.

**Megvalósítás**:

- Használjon olyan adatbázis-eszközt, mint a verziókövetés használatát támogató Azure Data Studio. 

- Hozzon létre egy elkülönített programkód-telepítési folyamatot.

- A fő ág véglegesítése előtt egy személynek (a kód szerzőjének kivételével) meg kell vizsgálnia a jogosultsági szint lehetséges kiterjesztését, valamint a rosszindulatú adatmódosításokat a csalások és a tisztességtelen hozzáférés elleni védelem érdekében. Ez a forrás-ellenőrzési mechanizmusok használatával végezhető el.

**Ajánlott eljárások**:

- Szabványosítás: segítséget nyújt egy olyan szabványos eljárás megvalósításához, amelyet a kód frissítéseihez kell követni. 

- A sebezhetőségi felmérés olyan szabályokat tartalmaz, amelyek megkeresik a túlzott engedélyeket, a régi titkosítási algoritmusok használatát, valamint az adatbázis-sémán belüli egyéb biztonsági problémákat. 

- A további ellenőrzéseket a fejlett veszélyforrások elleni védelem segítségével végezheti el egy MINŐSÉGBIZTOSÍTÁSi vagy tesztelési környezetben, amely az SQL-injektálás miatt sebezhető kódot keres.

- Példák a következőkre: 
  - A felhasználó létrehozása vagy a biztonsági beállítások módosítása egy automatikus SQL-kód – frissítés telepítésen belülről. 
  - Egy tárolt eljárás, amely a megadott paraméterektől függően a cellában lévő pénzügyi értéket nem megfelelő módon frissíti. 

- Győződjön meg arról, hogy a felülvizsgálatot végző személy nem a kezdeményező kód szerzője, és a Code-Reviews és a Secure kódolással jól tájékozott.

- Ügyeljen arra, hogy a Code-Changes minden forrását megismerje: a kód T-SQL-szkriptekben is szerepelhet; lehet, hogy ad-hoc parancsok hajthatók végre, vagy a nézetek, függvények, eseményindítók és tárolt eljárások formájában telepíthetők. Emellett az SQL Agent-feladatdefiníciók (lépések) részét képezheti, SSIS-csomagokon, Azure Data Factoryeken és más szolgáltatásokon belül is végrehajtható.

## <a name="data-protection"></a>Adatvédelem

Az adatvédelem olyan képességek összessége, amelyekkel a titkosítás vagy a elhomályosítás révén biztosítható a fontos információk védelme.

> [!NOTE]
> A Microsoft tanúsítja, hogy Azure SQL Database FIPS 140-2 1. szintű megfelelőséggel rendelkezik. Ez a FIPS 140-2 1. szintű elfogadható algoritmusok és a FIPS 140-2 1. szintű hitelesített példányának ellenőrzése után történik, beleértve a szükséges kulcs hosszával, a kulcs-kezeléssel, a kulcs generálásával és a kulcsos tárolással való konzisztencia mértékét. Ez az igazolás arra szolgál, hogy ügyfeleink válaszolják meg a FIPS 140-2 1. szintű, ellenőrzött példányok használatát az adatfeldolgozásban, illetve a rendszerek vagy alkalmazások továbbítása során. A fenti nyilatkozatban a "FIPS 140-2 1. szintnek megfelelő" és "FIPS 140-2 1. szintű megfelelőség" kifejezést adjuk meg, amely igazolja, hogy az Amerikai Egyesült Államok és a kanadai kormány a különböző "FIPS 140-2 1. szintű" minősítéssel való használatra való alkalmazhatóságát mutatja be. 


### <a name="encrypt-data-in-transit"></a>Adatforgalom titkosítása

> [!NOTE]
> Megemlítve: OSA Practice #6, ISO Control Family: titkosítás

Védi az adatait, miközben az ügyfél és a kiszolgáló közötti adatátvitelt. Tekintse meg a [hálózati biztonság](#network-security)című témakört.

### <a name="encrypt-data-at-rest"></a>Inaktív adatok titkosítása

> [!NOTE]
> Megemlítve: OSA Practice #6, ISO Control Family: titkosítás

A inaktív adatok titkosítása az adatok titkosítási védelme, ha az adatbázis-, napló-és biztonságimásolat-fájlokban is megmarad.

**Megvalósítás**:

- A szolgáltatás által felügyelt kulcsokkal rendelkező [transzparens adatbázis-titkosítás (TDE)](transparent-data-encryption-azure-sql.md) alapértelmezés szerint engedélyezve van minden olyan adatbázis esetében, amelyet a 2017-es Azure SQL Database követően hozott létre.
- Felügyelt példány esetén, ha az adatbázis egy helyszíni kiszolgálóról történő visszaállítási művelet eredményeképpen jön létre, a rendszer tiszteletben tartja az eredeti adatbázis TDE-beállítását. Ha az eredeti adatbázis nem rendelkezik TDE-engedélyezéssel, javasoljuk, hogy a felügyelt példányhoz manuálisan be kell kapcsolni a TDE.

**Ajánlott eljárások**:

- Ne tárolja azokat az adatok, amelyek titkosítást igényelnek a Master adatbázisban. A Master adatbázis nem titkosítható a TDE.

- Az ügyfél által felügyelt kulcsok használata Azure Key Vault, ha nagyobb átláthatóságra és részletes szabályozásra van szükség a TDE-védelem felett. Azure Key Vault lehetővé teszi az engedélyek visszavonását, hogy az adatbázis nem érhető el. Központilag kezelheti a TDE-védőket más kulcsokkal együtt, vagy elforgathatja a TDE-védőt a saját ütemtervében Azure Key Vault használatával.

- Ha Azure Key Vault használja az ügyfél által felügyelt kulcsokat, kövesse a TDE és a [Azure Key Vault konfigurálására vonatkozó útmutatót](transparent-data-encryption-byok-azure-sql.md#recommendations-when-configuring-akv) , valamint a [geo-DR és a Azure Key Vault konfigurálásának módját](transparent-data-encryption-byok-azure-sql.md#geo-dr-and-customer-managed-tde).

### <a name="protect-sensitive-data-in-use-from-high-privileged-unauthorized-users"></a>Magas jogosultsági szintű, jogosulatlan felhasználók által használt bizalmas adatok védelme

A használatban lévő adatmennyiség az adatbázis-rendszer memóriájában tárolt, az SQL-lekérdezések végrehajtása során tárolt adatmennyiség. Ha az adatbázis bizalmas adatokat tárol, a szervezetnek meg kell győződnie arról, hogy a magas jogosultságú felhasználók, például a szervezeten belüli Microsoft-operátorok vagy a Adattervezők nem tudják kinyerni az adatokat a SQL Serveri folyamat memóriájában, és nem képesek az egyszerű szöveges adatértékek megtekintése az adatbázis lekérdezése során.

**Megvalósítás**:

- A [Always encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) használatával biztosíthatja, hogy a bizalmas adatok ne legyenek elérhetők a Azure SQL Databaseban, akár a memóriában, akár a használatban. Ez védi az adatbázis-rendszergazdák (Adattervezők-EK) és a Felhőbeli rendszergazdák (vagy a magas jogosultságú, de jogosulatlan felhasználók megszemélyesítését végző rossz szereplők) adatait, és így nagyobb mértékben szabályozhatja, hogy ki férhet hozzá az adataihoz.

**Ajánlott eljárások**:

- A Always Encrypted nem helyettesíti az inaktív adatok titkosítását (TDE) vagy átvitelét (SSL/TLS), és nem használható a nem bizalmas adatokhoz a teljesítmény és a funkciók hatásának csökkentése érdekében. A Always Encrypted a TDE és a TLS együttes használata ajánlott a REST-alapú, a tranzit-és a használatban lévő adatok átfogó védelméhez. 

- Always Encrypted kulcsok kezelése szerepkör-elkülönítéssel, ha Always Encryptedt használ a rosszindulatú Adattervezők származó adatok védelme érdekében. A szerepkörök elkülönítésével a biztonsági rendszergazda létrehozza a fizikai kulcsokat, és egy DBA oszlop főkulcsát és az oszlop titkosítási kulcsának metaadat-objektumait hozza létre, amelyek a fizikai kulcsokat ismertetik az adatbázisban. A folyamat során a biztonsági rendszergazdának nem kell hozzáférése az adatbázishoz, és a DBA-nek nem kell hozzáférése a fizikai kulcsokhoz az egyszerű szöveges verzióban. 
  - A részletekért tekintse meg a [kulcsok kezelése a szerepkörök elkülönítésével](https://docs.microsoft.com/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted#managing-keys-with-role-separation) című cikket. 

- Az egyszerű felügyelet érdekében az oszlopok főkulcsait Azure Key Vault tárolja. Kerülje a Windows-tanúsítványtároló használatát (és az elosztott kulcstároló-megoldásokat, amelyek nem rendelkeznek a kulcsfontosságú felügyeleti megoldásokkal). 

- Alaposan gondolja át a több kulcs (oszlop főkulcsa vagy az oszlop titkosítási kulcsa) használatának kompromisszumait. Csökkentse a kulcsok számát a legfontosabb felügyeleti díjak csökkentése érdekében. Az adatbázisokban az egyik főkulcs és egy oszlopos titkosítási kulcs általában elegendő az állandó állapotú környezetekben (nem a kulcs forgásának közepéhez), hacsak nem rendelkezik különböző felhasználói csoportokkal, amelyek mindegyike különböző kulcsokat használ, és különböző adatokhoz fér hozzá.  

- A megfelelőségi követelmények alapján forgassa el az oszlop főkulcsait. Ha az oszlop titkosítási kulcsait is el kell forgatni, érdemes lehet online titkosítást használni az alkalmazások leállásának minimalizálásához. 
  - Tekintse meg a cikk, a [teljesítmény és a rendelkezésre állás szempontjait](https://docs.microsoft.com/sql/relational-databases/security/encryption/configure-column-encryption-using-powershell#performance-and-availability-considerations). 

- Használjon determinisztikus titkosítást, ha az adatok számításait (egyenlőségét) támogatni kell. Egyéb esetben véletlenszerű titkosítást használjon. Kerülje a determinisztikus titkosítás használatát az alacsony entrópia-adatkészletekhez, vagy nyilvánosan ismert eloszlású adatkészletekhez. 

- Ha azt szeretné, hogy a harmadik fél az Ön beleegyezike nélkül férhet hozzá az adataihoz, akkor győződjön meg arról, hogy az összes olyan alkalmazás és eszköz, amely nem fér hozzá a kulcsokhoz és az adataihoz a Microsoft Azure felhőn kívülről. A kulcsokhoz való hozzáférés nélkül a harmadik fél semmilyen módon nem tudja visszafejteni az adattitkosítást, kivéve, ha megkerüli a titkosítást.

- Always Encrypted nem támogatja a kulcsok (és a védett adat) ideiglenes hozzáférésének megadását. Ha például a kulcsokat egy DBA-vel kell megosztania, hogy a DBA bizonyos tisztítási műveleteket hajtson végre a bizalmas és titkosított adatokon, akkor az egyetlen módszer a megbízhatóság visszavonására a DBA-ből származó adatokhoz való hozzáférés visszavonása az oszlop titkosítási kulcsainak és a oszlopok az adatok védelmét szolgáló MN főkulcsok költséges művelet. 

- A titkosított oszlopokban lévő egyszerű szöveges értékek eléréséhez a felhasználónak hozzáféréssel kell rendelkeznie a CMK, és védenie kell az oszlopokat, amely a CMK tároló kulcstárolóban van konfigurálva. Emellett a felhasználónak meg kell jelenítenie bármely oszlop főkulcs-DEFINÍCIÓját, és meg kell tekintenie az oszlop titkosítási KULCSának DEFINÍCIÓs adatbázisának engedélyeit.

### <a name="control-access-of-application-users-to-sensitive-data-through-encryption"></a>Az alkalmazás felhasználói bizalmas adatokhoz való hozzáférésének szabályozása titkosítás útján

A titkosítás segítségével biztosítható, hogy csak bizonyos alkalmazás-felhasználók férhessenek hozzá a titkosítási kulcsokhoz, és megtekinthetik vagy frissíthetik az adatfájlokat.

**Megvalósítás**:

- Használjon cella szintű titkosítást (CLE). A részletekért tekintse meg a következő cikket: [adatok adatoszlopának titkosítása](https://docs.microsoft.com/sql/relational-databases/security/encryption/encrypt-a-column-of-data) . 
- Azt is megteheti, hogy Always Encryptedt használ, de az alább felsorolt korlátozásokat figyelembe kell vennie.

**Ajánlott eljárások**

A CLE használatakor:

- A kulcsokhoz való hozzáférés szabályozása SQL-engedélyekkel és-szerepkörökkel. 

- Használjon AES-t (AES 256 ajánlott) az adattitkosításhoz. Az algoritmusok, például az RC4, a DES és a TripleDES elavultak, és az ismert biztonsági rések miatt nem használhatók fel. 

- A 3DES használatának elkerüléséhez használjon aszimmetrikus kulcsokkal vagy tanúsítványokkal (nem jelszavakkal) rendelkező szimmetrikus kulcsokat. 

- Ügyeljen arra, hogy az adatbázist a cella szintű titkosítás használatával exportálja és importálja (bacpac-fájlok). 
  - Tekintse meg a következő cikket: [javaslatok a cella szintű titkosítás használatára a Azure SQL Databaseban](https://blogs.msdn.microsoft.com/sqlsecurity/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database/) , hogy miként lehet megakadályozni a kulcsok elindítását az adatáttelepítés során, valamint az ajánlott eljárásokkal kapcsolatos további útmutatást.

Always Encrypted használatakor vegye figyelembe, hogy Always Encrypted elsődlegesen az Azure SQL Database (Cloud Operators, Adattervezők) magas jogosultsági szintű felhasználóitól származó bizalmas adatok védelme érdekében van kialakítva, lásd: [bizalmas adatok védelme magas jogosultságú, jogosulatlan felhasználóktól](#protect-sensitive-data-in-use-from-high-privileged-unauthorized-users). Az alkalmazás felhasználóitól származó adatok védelme Always Encrypted használatakor vegye figyelembe az alábbi kihívásokat:

- Miután megadta a felhasználó számára a bizalmas adatokhoz való hozzáférést azáltal, hogy a felhasználónak engedélyt ad az oszlop titkosítási kulcsának és az oszlop főkulcsának elérésére, hogy megbízhatóan visszavonja a hozzáférést az oszlop titkosítási kulcsának elforgatásához, ami egy költséges művelet, amelyre szükség van az összes oszlop újratitkosítása az oszlop titkosítási kulcsa véd. 

- Alapértelmezés szerint az összes Always Encrypted támogató Microsoft-ügyfél-illesztőprogram az oszlop titkosítási kulcsainak globális (egy-egy/alkalmazás-) gyorsítótárát tartja karban. Ha az ügyfél-illesztőprogram egy egyszerű szöveges oszlop titkosítási kulcsát kéri le egy olyan kulcstárolóval, amely egy oszlop főkulcsát tárolja, a rendszer gyorsítótárazza az egyszerű szöveges oszlop titkosítási kulcsát, amely a többfelhasználós alkalmazások felhasználóitól származó adatok elkülönítését teszi lehetővé. Ha az alkalmazás megszemélyesíti a végfelhasználókat a kulcstárolókkal való interakció során (például Azure Key Vault), azt követően, hogy egy felhasználó lekérdezése egy oszlopos titkosítási kulccsal tölti fel a gyorsítótárat, egy későbbi, ugyanazt a kulcsot igénylő lekérdezést, amelyet egy másik felhasználó indít el, a következőt fogja használni: gyorsítótárazott kulcs. Az illesztőprogram nem hívja meg a kulcstárolót, és nem fogja megnézni, hogy a második felhasználó rendelkezik-e engedéllyel az oszlop titkosítási kulcsának eléréséhez. Ennek eredményeképpen a felhasználó akkor is láthatja a titkosított információt, ha a felhasználó nem fér hozzá a kulcsokhoz. Ha a felhasználók elkülönítését egy többfelhasználós alkalmazásban szeretné elérni, előfordulhat, hogy le kell tiltania az oszlop-titkosítási kulcs gyorsítótárazását, ami további teljesítmény-ellenőrzéseket eredményez, mivel az illesztőprogramnak fel kell vennie a kapcsolatot a kulcstárolóval az egyes adattitkosítások vagy visszafejtések számára. művelet.

### <a name="protect-data-against-unauthorized-viewing-by-application-users-while-preserving-data-format"></a>Az alkalmazások felhasználói általi jogosulatlan megtekintésének védelme az adatformátum megőrzése mellett
Egy másik módszer, amellyel megakadályozható, hogy a jogosulatlan felhasználók megtekintsék az adatmegjelenítést, és az adattípusok és a formátumok megtartásával megőrizze az adattípusokat és a formátumokat, hogy a felhasználói alkalmazások továbbra is képesek legyenek kezelni

**Megvalósítás**:

- A [dinamikus Adatmaszkolás](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) használatával elhomályosíthatja a táblák oszlopait.

> [!NOTE]
> A Always Encrypted dinamikus adatmaszkolással nem működik. Egyazon oszlopot nem lehet titkosítani és eltakarni, ami azt jelenti, hogy a használatban lévő adatok védelméhez és a dinamikus adatmaszkoláson keresztül az alkalmazás felhasználóinak az adatok maszkolására van szükség.

**Ajánlott eljárások**:

> [!NOTE]
> A dinamikus adatmaszkolás nem használható a magas jogosultságú felhasználóktól származó adatok védelme érdekében. A maszkolási házirendek nem vonatkoznak olyan felhasználókra, akik rendszergazdai hozzáféréssel rendelkeznek, például db_owner.

- Ne engedélyezze az alkalmazások felhasználóinak alkalmi lekérdezések futtatását (mivel lehetséges, hogy a dinamikus adatmaszkolás megkerülheti őket).  
  - A részletekért tekintse meg a következő cikket: a [maszkolás kihagyása a következtetések és a találgatásos technikák használatával](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking#security-note-bypassing-masking-using-inference-or-brute-force-techniques) .  

- A megfelelő hozzáférés-vezérlési házirend (SQL-engedélyek, szerepkörök, RLS) használatával korlátozza a felhasználók engedélyeit a maszkolt oszlopokban lévő frissítések biztosításához. Egy oszlophoz tartozó maszk létrehozása nem akadályozza meg az adott oszlop frissítését. Ezért bár a felhasználók a maszkolt oszlop lekérdezése során maszkolt adatkérést kapnak, ugyanazokat a felhasználókat frissíthetik, ha írási engedéllyel rendelkeznek.    

-  A dinamikus adatmaszkolás nem őrzi meg a maszkolt értékek statisztikai tulajdonságait, ami hatással lehet a lekérdezés eredményeire (például a szűrési predikátumokat vagy a maszkolt adatokhoz tartozó illesztéseket tartalmazó lekérdezések).

## <a name="network-security"></a>Hálózati biztonság
A hálózati biztonság a hozzáférés-vezérlésre és az ajánlott eljárásokra hivatkozik, hogy az adatátvitelt az Azure SQL Databaseba.

### <a name="configure-my-client-to-connect-securely-to-azure-sql-database"></a>Az ügyfél konfigurálása, hogy biztonságosan kapcsolódjon Azure SQL Database 
Annak megakadályozása, hogy az ügyfélszámítógépek és alkalmazások a Azure SQL Databasehoz kapcsolódjanak jól ismert biztonsági rések miatt, a régebbi protokollok és a titkosítási csomagoktól való függőség miatt.

**Megvalósítás**:

- Győződjön meg arról, hogy a Azure SQL Databasehoz csatlakozó ügyfélszámítógépek [Transport Layer Security (TLS) protokollt](sql-database-security-overview.md#transport-layer-security-tls-encryption-in-transit)használnak.

**Ajánlott eljárások**:

- Az összes alkalmazás és eszköz konfigurálása az SQL-ADATBÁZIShoz való kapcsolódásra, ha engedélyezve van a titkosítás. 
  - Encrypt = on, TrustServerCertificate = off (vagy nem Microsoft-illesztőprogramokkal egyenértékű). 

- Ha az alkalmazás olyan illesztőprogramot használ, amely nem támogatja a TLS protokollt, vagy a TLS egy régebbi verzióját támogatja, cserélje le az illesztőprogramot, ha lehetséges. Ha nem lehetséges, gondosan értékelje ki a biztonsági kockázatokat. 

- Csökkentse a támadási vektorokat az SSL 2,0, az SSL 3,0, a TLS 1,0 és a TLS 1,1 biztonsági rések révén, ha letiltja azokat a Azure SQL Database [Transport Layer Security (TLS) beállításjegyzék-beállításokhoz](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-10)csatlakozó ügyfélszámítógépeken. 

- Az ügyfélen lévő titkosítási csomagokat a [TLS/SSL (Schannel SSP)](https://docs.microsoft.com/windows/desktop/SecAuthN/cipher-suites-in-schannel) kiszolgálón, és a [TLS titkosítási csomag konfigurálásakor](https://docs.microsoft.com/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order)kifejezetten tiltsa le a 3DES-t. 

- Ha felügyelt példányt használ, használja a **proxy** kapcsolattípus (alapértelmezett) értéket, mivel ez kikényszeríti a titkosítást a kiszolgáló oldaláról. Az **átirányítási** kapcsolat típusa jelenleg nem támogatja a titkosítás kényszerítését, és csak magánhálózati IP-kapcsolatokon érhető el. 
  - További információ: [Azure SQL connectivity Architecture – kapcsolati házirend](sql-database-connectivity-architecture.md#connection-policy).
  - A titkosítás a proxy és az átirányítás típusú kapcsolatok esetében is érvényben van. 

### <a name="minimize-attack-surface"></a>A támadási felület csökkentése
A Azure SQL Database hálózati hozzáférés-vezérlésének megvalósításával csökkentheti a kártékony felhasználók által megtámadható szolgáltatások számát.

> [!NOTE]
> Megemlítve: OSA Practice #5

**Megvalósítás**:

Egy Azure SQL Database-kiszolgálón (különálló adatbázist vagy rugalmas készleteket tartalmazó):
- Állítsa be az Azure-szolgáltatásokhoz való hozzáférés engedélyezése lehetőséget.

- Virtuális hálózati szolgáltatás-végpontok és virtuális hálózati tűzfalszabályok használata.

- Használjon privát hivatkozást (előzetes verzió).

Felügyelt példányban:
- Kövesse a [hálózati követelmények](sql-database-managed-instance-connectivity-architecture.md#network-requirements)című témakör útmutatását. 

**Ajánlott eljárások**:

- Azure SQL Database hozzáférésének korlátozása privát végponthoz való csatlakozással (például privát adatútvonal használatával): 
  - A külső hozzáférés megakadályozása érdekében a felügyelt példányok elkülöníthetők egy virtuális hálózaton belül. Azok az alkalmazások és eszközök, amelyek ugyanabban a régióban található azonos vagy egyenrangú virtuális hálózaton találhatók, közvetlenül is hozzáférhetnek. A különböző régiókban található alkalmazások és eszközök a kapcsolat létesítéséhez VNet-VNet kapcsolatot vagy ExpressRoute áramkör-társítást használhatnak. Az ügyfélnek hálózati biztonsági csoportokat (NSG) kell használnia az 1433-as porton keresztüli hozzáférés korlátozásához a felügyelt példányokhoz hozzáférést igénylő erőforrásokhoz. 
  - Egy SQL Database-kiszolgáló (amely önálló adatbázisokat vagy rugalmas készleteket tartalmaz) esetében használja a [privát kapcsolat](sql-database-private-endpoint-overview.md) funkciót, amely dedikált magánhálózati IP-címet biztosít a virtuális hálózaton belüli SQL Database-kiszolgálóhoz. Virtuális hálózati szolgáltatás- [végpontokat is használhat virtuális hálózati tűzfalszabályok](sql-database-vnet-service-endpoint-rule-overview.md) használatával a SQL Database-kiszolgálókhoz való hozzáférés korlátozására.
  - A mobil felhasználóknak a pont – hely VPN-kapcsolatokkal kell használniuk az adatútvonalon keresztüli csatlakozást.
  - A helyszíni hálózathoz csatlakozó felhasználóknak a helyek közötti VPN-kapcsolatot vagy ExpressRoute kell használniuk az adatútvonalon való csatlakozáshoz.

- A Azure SQL Database egy nyilvános végponthoz való csatlakozással (például nyilvános adatútvonal használatával) érheti el. A következő ajánlott eljárásokat kell figyelembe venni: 
  - SQL Database-kiszolgáló esetén az [IP-tűzfalszabályok](sql-database-firewall-configure.md) használatával korlátozza a hozzáférést csak a jogosult IP-címekhez.
  - Felügyelt példány esetén használjon hálózati biztonsági csoportokat (NSG) a 3342-as porton keresztüli hozzáférés korlátozásához a szükséges erőforrásokhoz. További információ: [Azure SQL Database felügyelt példány biztonságos használata nyilvános végpontokkal](sql-database-managed-instance-public-endpoint-securely.md). 

> [!NOTE]
> A felügyelt példányok nyilvános végpontja alapértelmezés szerint nincs engedélyezve, és explicit módon engedélyezve kell lennie. Ha a vállalati házirend nem engedélyezi a nyilvános végpontok használatát, a [Azure Policy](../governance/policy/overview.md) használatával megakadályozhatja, hogy az első helyen engedélyezze a nyilvános végpontokat.

- Azure hálózati összetevők beállítása: 
  - [A hálózati biztonságra vonatkozó Azure ajánlott eljárások](../security/fundamentals/network-best-practices.md)követése.
  - Tervezze meg az Virtual Network (VNet) konfigurációt az [Azure Virtual Network gyakori kérdések (GYIK)](../virtual-network/virtual-networks-faq.md) és a terv című szakaszban ismertetett ajánlott eljárások alapján. 
  - VNet szegmentálása több alhálózatra, és erőforrások kiosztása hasonló szerepkörhöz ugyanahhoz az alhálózathoz (például előtér-és háttér-erőforrások).
  - Használjon [hálózati biztonsági csoportokat (NSG)](../virtual-network/security-overview.md) az alhálózatok közötti adatforgalom vezérléséhez az Azure VNet határán belül.
  - Az előfizetéshez tartozó [Azure Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) engedélyezése a bejövő és kimenő hálózati forgalom figyelésére.

### <a name="configure-power-bi-for-secure-connections-to-azure-sql-database"></a>Power BI konfigurálása biztonságos kapcsolatokhoz Azure SQL Database

**Ajánlott eljárások**:

- Power BI Desktop esetén, amikor csak lehet, használja a személyes adatelérési utat. 

- Győződjön meg arról, hogy a Power BI Desktop a TLS 1.2-es verzióval csatlakozik, ha a beállításkulcsot [Transport Layer Security (TLS) beállításjegyzék-](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) beállítások szerint állítja be az ügyfélszámítógépen. 

- Az adott felhasználók adathozzáférésének korlátozása [a soros szintű biztonság (RLS) és a Power bi](https://docs.microsoft.com/power-bi/service-admin-rls)használatával. 

- Power BI szolgáltatás esetén használja a helyszíni [adatátjárót](https://docs.microsoft.com/power-bi/service-gateway-onprem), figyelembe véve a [korlátozásokat és a szempontokat](https://docs.microsoft.com/power-bi/service-gateway-deployment-guidance#installation-considerations-for-the-on-premises-data-gateway).

### <a name="configure-app-service-for-secure-connections-to-azure-sql-database"></a>App Service konfigurálása biztonságos kapcsolatokhoz Azure SQL Database

**Ajánlott eljárások**:

- Egy egyszerű webalkalmazáshoz a nyilvános végponthoz való csatlakozáshoz az **Azure-szolgáltatások engedélyezése** beállítást kell beállítani. 

- Az [alkalmazás integrálása Azure-Virtual Network](../app-service/web-sites-integrate-with-vnet.md) a felügyelt példányokhoz való magánhálózati adatelérési utakhoz való kapcsolódáshoz. Igény szerint egy webalkalmazást is üzembe helyezhet [app Service környezetekkel](../app-service/environment/intro.md). 

- A SQL Database-kiszolgálón található adatbázishoz csatlakozó vagy VNet integrált webalkalmazással rendelkező webalkalmazások esetében a [VNet szolgáltatás-végpontok és a VNet tűzfalszabályok](sql-database-vnet-service-endpoint-rule-overview.md) használatával korlátozhatja a hozzáférést egy adott VNet és alhálózatról, majd beállíthatja az **Azure-szolgáltatások** kikapcsolását. Egy felügyelt példányhoz is csatlakozhat egy privát adatútvonalon keresztül.  

- Győződjön meg arról, hogy a webalkalmazás konfigurálva van a következő cikkben [: ajánlott eljárások a Pásti webes és mobil alkalmazások biztonságossá tételéhez Azure app Service használatával](../security/security-paas-applications-using-app-services.md). 

- Telepítse a [webalkalmazási tűzfalat (WAF)](../application-gateway/waf-overview.md) , hogy megvédje a webalkalmazást a gyakori biztonsági rések és sebezhetőségek ellen.

### <a name="configure-azure-vm-hosting-for-secure-connections-to-azure-sql-database"></a>Azure-beli virtuális gép üzemeltetésének konfigurálása a Azure SQL Database biztonságos kapcsolataihoz

**Ajánlott eljárások**:

- Az engedélyezési és megtagadási szabályok kombinációját használhatja az Azure-beli virtuális gépek NSG, hogy megtudja, mely régiók érhetők el a virtuális gépről.

- Győződjön meg arról, hogy a virtuális gép az Azure-ban a IaaS számítási feladatokhoz [szükséges biztonsági eljárások](../security/azure-security-iaas.md)alapján van konfigurálva.

- Győződjön meg arról, hogy minden virtuális gép hozzá van rendelve egy adott VNet és alhálózathoz. 

- Ellenőrizze, hogy szükség van-e az alapértelmezett 0.0.0.0/Internet útvonalra a [kényszerített bújtatással kapcsolatos](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md#about-forced-tunneling)útmutatás alapján. 
  - Ha igen – például az előtér-alhálózat –, akkor tartsa meg az alapértelmezett útvonalat.
  - Ha nem – például a középső réteg vagy a háttérbeli alhálózat – lehetővé teszi a kényszerített bújtatást, hogy az interneten keresztüli forgalom ne haladjon át a helyszínen (a. k. egy telephelyen). 

- Nem [kötelező alapértelmezett útvonalakat](../virtual-network/virtual-networks-udr-overview.md#optional-default-routes) implementálhat, ha a társ-vagy a helyszíni hálózathoz csatlakozik. 

- A [felhasználó által megadott útvonalak](../virtual-network/virtual-networks-udr-overview.md#user-defined) implementálása, ha a VNet lévő összes forgalmat egy hálózati virtuális berendezésbe kell elküldeni a csomagok ellenőrzéséhez. 

- A [VNet szolgáltatás-végpontok](sql-database-vnet-service-endpoint-rule-overview.md) segítségével biztonságos hozzáférést biztosíthat az Azure Storage-hoz, például az Azure-tárolóhoz az Azure-beli gerinc hálózaton keresztül. 

### <a name="protect-against-distributed-denial-of-service-ddos-attacks"></a>Az elosztott szolgáltatásmegtagadási (DDoS) támadások elleni védelem
Az elosztott szolgáltatásmegtagadási (DDoS) támadások arra irányulnak, hogy a rosszindulatú felhasználók elküldjék a hálózati forgalom Azure SQL Databaseét az Azure-infrastruktúra túlnyomó részeként, és így az érvényes bejelentkezések és számítási feladatok elutasítását.

> [!NOTE]
> Megemlítve: OSA Practice #9

**Megvalósítás**:

A DDoS elleni védelem automatikusan engedélyezve van az Azure platform részeként. Magában foglalja a forgalom folyamatos figyelését és a nyilvános végpontok hálózati szintű támadásának valós idejű enyhítését. 

- A [Azure DDoS Protection](../virtual-network/ddos-protection-overview.md) használatával figyelheti a virtuális hálózatokban üzembe helyezett erőforrásokhoz társított nyilvános IP-címeket.

- A [Azure SQL Database komplex veszélyforrások elleni védelem](sql-database-threat-detection-overview.md) használatával észlelheti a szolgáltatásmegtagadási (DOS) támadásokat az adatbázisokon.

**Ajánlott eljárások**:

- A [támadási felület minimálisra csökkentése](#minimize-attack-surface) című cikkben ismertetett eljárások követésével csökkentheti a DDOS-támadások elleni fenyegetéseket. 

- A komplex veszélyforrások elleni védelem – az **SQL hitelesítő adataival** kapcsolatos riasztások segítségével észlelheti a találgatásos támadásokat. Bizonyos esetekben a riasztás a penetráció tesztelési feladatait is megkülönböztetheti. 

- SQL Databasehoz csatlakozó Azure-beli virtuális gépekhez: 
  - A Azure Security Center internetes végpontokon keresztüli hozzáférésének korlátozásához kövesse az ajánlást. 
  - A virtuálisgép-méretezési csoportok segítségével az alkalmazás több példányát futtathatja Azure-beli virtuális gépeken. 
  - Tiltsa le az internetről az RDP-t és az SSH-t a találgatásos támadás megelőzése érdekében. 

## <a name="monitoring-logging-and-auditing"></a>Figyelés, naplózás és naplózás  
Ez a szakasz olyan képességeket mutat be, amelyek segítségével szokatlan és potenciálisan ártalmas kísérletek észlelhetők az adatbázisok eléréséhez vagy kiaknázásához. Emellett ismerteti az adatbázis-naplózás konfigurálásának ajánlott eljárásait az adatbázis-események nyomon követéséhez és rögzítéséhez.

### <a name="protect-databases-against-attacks"></a>Adatbázisok elleni védelem támadásokkal szemben 
A komplex veszélyforrások elleni védelem lehetővé teszi, hogy a rendellenes tevékenységekre vonatkozó biztonsági riasztások révén észlelje és reagáljon a lehetséges fenyegetésekre.

**Megvalósítás**:

- A komplex [veszélyforrások elleni védelem](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts) használatával azonosíthatja a szokatlan és potenciálisan ártalmas támadási kísérleteket az adatbázisok eléréséhez vagy kiaknázásához, többek között:
  - SQL-injektálási támadás.
  - Hitelesítő adatok ellopása/szivárgása.
  - Jogosultságokkal való visszaélés.
  - Az adatkiszűrése.

**Ajánlott eljárások**:

- A [speciális adatbiztonsági](sql-database-advanced-data-security.md#getting-started-with-ads) Azure SQL Database konfigurálása egy adott SQL Database kiszolgálóhoz vagy felügyelt példányhoz, illetve az előfizetésben lévő összes SQL Database-kiszolgáló és felügyelt példány esetében [Azure Security Center Standard szintre](../security-center/security-center-pricing.md)váltással. 

- Teljes körű vizsgálat esetén ajánlott engedélyezni [SQL Database naplózást](sql-database-auditing.md) az adatbázis-események nyomon követéséhez és az Azure Storage-fiók vagy az Azure log Analytics-munkaterület naplójába való íráshoz. 

### <a name="audit-critical-security-events"></a>Kritikus biztonsági események naplózása
Az adatbázis-események nyomon követése segít megérteni az adatbázis-tevékenységeket, és olyan eltéréseket és rendellenességeket mutat be, amelyek üzleti problémákat vagy feltételezett biztonsági szabálysértéseket jelezhetnek. Emellett a megfelelőségi szabványok betartását is lehetővé teszi és megkönnyíti. 

**Megvalósítás**:

- Engedélyezze [SQL Database naplózást](sql-database-auditing.md) az adatbázis-események nyomon követéséhez és az Azure Storage-fiókjában, log Analytics munkaterület (előzetes verzió) vagy Event Hubs (előzetes verzió) naplóba való íráshoz. 

- A naplók beírhatók egy Azure Storage-fiókba, egy Log Analytics munkaterületre Azure Monitor naplók általi felhasználáshoz, illetve az Event hub használatával történő felhasználáshoz. Ezen beállítások bármely kombinációját konfigurálhatja, és a rendszer a naplókat is megírja a naplókba. 

**Ajánlott eljárások**:

- Ha [SQL Database naplózást](sql-database-auditing.md) konfigurál az adatbázis-kiszolgálón az események naplózásához, a rendszer naplózza az adott kiszolgálón lévő összes meglévő és újonnan létrehozott adatbázist.
- Alapértelmezés szerint a naplózási házirend minden műveletet (lekérdezéseket, tárolt eljárásokat és sikeres és sikertelen bejelentkezést) tartalmaz az adatbázisokon, ami nagy mennyiségű naplót eredményezhet. Javasoljuk, hogy az ügyfelek a [PowerShell használatával konfigurálja a különböző típusú műveletek és műveleti csoportok naplózását](sql-database-auditing.md#subheading-7), hogy a naplózott műveletek számát szabályozzák, valamint az események elvesztésének kockázatát. Ez lehetővé teszi az ügyfeleknek, hogy csak a valóban szükséges naplózási adatmennyiséget rögzítsen.
- A naplók közvetlenül a [Azure Portal](https://portal.azure.com/)vagy a konfigurált tárolási helyről is felhasználhatók. 


> [!NOTE]
> A Log Analytics naplózásának engedélyezése a betöltési díjak alapján jár. Vegye figyelembe a kapcsolódó költségeket ezzel a [lehetőséggel](https://azure.microsoft.com/pricing/details/monitor/), vagy a naplókat egy Azure Storage-fiókban kell tárolnia. 

**További források**:

- [SQL Database naplózás](sql-database-auditing.md)
- [SQL Server naplózás](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) 

### <a name="secure-audit-logs"></a>Biztonságos naplózási naplók
A feladatok elkülönítésének támogatásához és a-ellenőröktől való elkülönítéshez kulcsfontosságú, hogy a szükséges mértékeket a Storage-fiókhoz való hozzáférés korlátozására lehessen korlátozni. 

**Megvalósítás**:

- A naplók Azure Storage-ba mentésekor győződjön meg arról, hogy a Storage-fiókhoz való hozzáférés a minimális biztonsági alapelvekre korlátozódik a Storage-fiókhoz való hozzáférés szabályozásával.

- További információ: az [Azure Storage hozzáférésének engedélyezése](../storage/common/storage-auth.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

**Ajánlott eljárások**:

- A naplózási cél elérésének szabályozása kulcsfontosságú fogalom a DBA a könyvvizsgálók általi elkülönítéséhez. 

- A bizalmas adatokhoz való hozzáférés naplózásakor érdemes lehet az adatokat adattitkosítással biztosítani, hogy elkerülje az adatszivárgást a könyvvizsgálónak. További információkért lásd a [bizalmas adatok védelme magas jogosultságú, jogosulatlan felhasználóktól](#protect-sensitive-data-in-use-from-high-privileged-unauthorized-users)című szakaszt.

## <a name="security-management"></a>Biztonság kezelése

Ez a szakasz az adatbázisok biztonsági helyzetének kezelésére szolgáló különböző szempontokat és ajánlott eljárásokat ismerteti. Ajánlott eljárásokat tartalmaz, amelyekkel biztosítható, hogy az adatbázisok megfeleljenek a biztonsági normáknak, a felfedezéshez, valamint az adatbázisokban potenciálisan bizalmas adatokhoz való hozzáférés besorolásához és nyomon követéséhez. 

### <a name="ensure-that-the-databases-are-configured-to-meet-security-best-practices"></a>Győződjön meg arról, hogy az adatbázis (ok) úgy van konfigurálva, hogy megfeleljen az ajánlott biztonsági eljárásoknak 

Proaktív módon fejlesztheti adatbázisa biztonságát a lehetséges adatbázis-sebezhetőségek felderítésével és szervizelését.

**Megvalósítás**:

- Az [SQL sebezhetőségi felmérés](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment) (va) lehetővé teszi az adatbázis biztonsági problémákra való vizsgálatát, és rendszeres időközönként automatikusan futtatni az adatbázisokat.

**Ajánlott eljárások**:

- Először futtassa a VA-t az adatbázisain, és ismételje meg a szervizelését, hogy nem ellenőrzi az ajánlott biztonsági eljárásokat, és beállítja az alapkonfigurációkat az elfogadható konfigurációkhoz, amíg a vizsgálat kikerül a "tiszta" (minden ellenőrzés) értékre.  

- Konfigurálja a rendszeres ismétlődő vizsgálatokat hetente egyszer, és konfigurálja az érintett személyt az összegző e-mailek fogadására. 

- Tekintse át az összes heti vizsgálatot követő VA összefoglalót. Az észlelt biztonsági rések esetében értékelje ki az előző vizsgálat eredményének eltolódását, és állapítsa meg, hogy az ellenőrzést meg kell-e oldani, vagy ha a konfiguráció módosításának jogos oka van.   

- Oldja fel az ellenőrzéseket és a frissítési alapterveket, ahol szükséges, vagy hozzon létre jegyeket a műveletek feloldásához, és kövesse ezeket, amíg meg nem oldódik. 

**További források**:

- [SQL sebezhetőségi felmérés](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment) 
- [Az SQL sebezhetőség-felmérési szolgáltatás segítséget nyújt az adatbázis-sebezhetőségek azonosításában](sql-vulnerability-assessment.md)

### <a name="identify-and-tag-sensitive-data"></a>Bizalmas adatok azonosítása és címkézése 

Olyan oszlopok felderítése, amelyek potenciálisan bizalmas adatokat tartalmaznak. Osztályozza az oszlopokat a speciális érzékenységi alapú naplózási és védelmi forgatókönyvek kihasználása érdekében. 

**Megvalósítás**:

- Az [SQL-adatfelderítés és-besorolás](sql-database-data-discovery-and-classification.md) használatával felderítheti, osztályozhatja, címkézheti és megóvja a bizalmas adatokat az adatbázisaiban. 
  - Tekintse meg az automatikus észlelés által az SQL-adatfelderítés és besorolási irányítópulton létrehozott besorolási javaslatokat, és fogadja el a megfelelő besorolásokat, hogy a bizalmas adatok tartósan címkével legyenek ellátva besorolással Címkék. 
  - Adja meg manuálisan a besorolásokat az automatikus mechanizmus által nem felderített további bizalmas adatmezőkhöz. 
- További információ: SQL- [adatfelderítés & besorolása](https://docs.microsoft.com/sql/relational-databases/security/sql-data-discovery-and-classification).

**Ajánlott eljárások**:

- Az adatbázis besorolási állapotának pontos értékeléséhez rendszeresen figyelje a besorolási irányítópultot. Az adatbázis besorolási állapotáról szóló jelentés exportálható vagy kinyomtatható a megfelelőségi és naplózási célokra való megosztáshoz.

- Az ajánlott bizalmas adatok állapotát folyamatosan figyelemmel kísérheti az SQL sebezhetőségi felmérésben, a bizalmas adatfelderítési szabály nyomon követésével és az ajánlott oszlopok besorolással való eltolódásának azonosításával.  

- A besorolást a szervezet konkrét igényeihez igazított módon használhatja. Szabja testre a Information Protection szabályzatot (érzékenységi címkék, adattípusok, felderítési logika) a Azure Security Center [SQL Information Protection](../security-center/security-center-info-protection-policy.md) házirendjében. 

### <a name="track-access-to-sensitive-data"></a>Bizalmas adatokhoz való hozzáférés nyomon követése 
Figyelje meg, hogy ki fér hozzá a bizalmas adatokhoz, és hogyan rögzíthet lekérdezéseket bizalmas adatokon a naplókban.

**Megvalósítás**:

- Az SQL audit és az adatbesorolás együttes használata. 
  - A [SQL Database](sql-database-auditing.md) a naplóban a hozzáférést a bizalmas adatokhoz is nyomon követheti, és megtekintheti azokat az adatokat, mint például az elért adatok, valamint az érzékenységi címke (lásd: a [bizalmas adatokhoz való hozzáférés naplózása](sql-database-data-discovery-and-classification.md#subheading-3)). 

**Ajánlott eljárások**:

- Tekintse meg a naplózási és adatbesorolási szakaszban ajánlott eljárásokat: 
  - [Kritikus biztonsági események naplózása](#audit-critical-security-events) 
  - [Bizalmas adatok azonosítása és címkézése](#identify-and-tag-sensitive-data) 

### <a name="visualize-security-and-compliance-status"></a>Biztonsági és megfelelőségi állapot megjelenítése 

Olyan egységes infrastruktúra-alapú biztonsági felügyeleti rendszer használata, amely megerősíti az adatközpontok biztonsági állapotát (beleértve az SQL-adatbázisokat is). Az adatbázisok és a megfelelőségi állapot biztonságával kapcsolatos javaslatok listájának megtekintése.

**Megvalósítás**:

- Az SQL-hez kapcsolódó biztonsági javaslatok és az aktív fenyegetések figyelése [Azure Security Centerban](https://azure.microsoft.com/documentation/services/security-center/).

## <a name="common-security-threats-and-potential-mitigations"></a>Gyakori biztonsági fenyegetések és lehetséges enyhítések

Ez a szakasz segítséget nyújt az egyes támadási vektorokkal szembeni védelemhez szükséges biztonsági intézkedések megkeresésében. A fentiekben ismertetett biztonsági irányelvek közül egyet vagy többet a lehető legtöbb megoldással lehet megvalósítani.

### <a name="security-threat-data-exfiltration"></a>Biztonsági fenyegetés: az adatkiszűrése

Az adatok kiszűrése az adatok jogosulatlan másolása, továbbítása vagy lekérése egy számítógépről vagy kiszolgálóról. Tekintse meg a wikipedia [adatkiszűrése](https://en.wikipedia.org/wiki/Data_exfiltration) definícióját.

Ha egy nyilvános végponton keresztül csatlakozik Azure SQL Database-kiszolgálóhoz, az kiszűrése kockázatot jelent, mivel az ügyfeleknek meg kell nyitniuk a tűzfalat a nyilvános IP-címekre.  

**1. eset**: egy Azure-beli virtuális gépen futó alkalmazás egy Azure SQL Database-kiszolgáló adatbázisához csatlakozik. A szélhámos színész hozzáfér a virtuális géphez, és megsérül. Ebben az esetben az kiszűrése azt jelenti, hogy a szélhámos virtuális gépet használó külső entitás csatlakozik az adatbázishoz, átmásolja a személyes adataikat, és a blob Storage-ban tárolja, vagy egy másik SQL Database egy másik előfizetésben.

**2. forgatókönyv**: egy Rouge dBA. Ezt a forgatókönyvet gyakran a szabályozott iparágak biztonsági szempontból érzékeny ügyfelei okozják. Ebben a forgatókönyvben egy magas szintű jogosultságú felhasználó másolhatja az adatok Azure SQL Databaseról egy másik előfizetésre, amelyet nem az adatok tulajdonosa ellenőriz.

**Lehetséges enyhítések**:

Napjainkban Azure SQL Database a következő technikákat kínálja az adatkiszűrése fenyegetések enyhítésére: 

- Az engedélyezési és megtagadási szabályok kombinációját használhatja az Azure-beli virtuális gépek NSG, hogy megtudja, mely régiók érhetők el a virtuális gépről. 
- Ha Azure SQL Database-kiszolgálót használ (egyedi adatbázisokat vagy rugalmas készleteket tartalmaz), állítsa be a következőket:
  - Az Azure-szolgáltatások kikapcsolásának engedélyezése.
  - Csak az Azure-beli virtuális gépet tartalmazó alhálózatról érkező forgalom engedélyezése VNet tűzfalszabály beállításával.
  - [Privát hivatkozás](sql-database-private-endpoint-overview.md) használata
- Felügyelt példány esetén a magánhálózati IP-hozzáférés alapértelmezés szerint az első olyan kiszűrése vonatkozik, amely egy szélhámos virtuális gép esetében érintett. Kapcsolja be az alhálózati delegálás funkciót olyan alhálózaton, amely automatikusan beállítja a legszigorúbb házirendet egy felügyelt példány alhálózatán.
- A szélhámos DBA-mel kapcsolatos aggályok nagyobb mértékben vannak kitéve egy felügyelt példánnyal, mivel nagyobb felülettel rendelkeznek, és a hálózati követelmények láthatók az ügyfelek számára. A legjobb megoldás az ebben a biztonsági útmutatóban található összes eljárás alkalmazásával megakadályozza a szélhámos DBA-forgatókönyvet az első helyen (nem csak az adatkiszűrése esetében). Always Encrypted egy módszer a bizalmas adatok védelmére azáltal, hogy titkosítja, és a kulcs nem érhető el a DBA számára.

## <a name="security-aspects-of-business-continuity-and-availability"></a>Az üzletmenet folytonosságának és rendelkezésre állásának biztonsági szempontjai

A legtöbb biztonsági szabvány a működés folytonossága szempontjából az adatrendelkezésre állást kezeli, és a redundancia és a feladatátvételi funkciók megvalósításával kerülhető el, hogy elkerülje az egyes meghibásodási pontokat. Katasztrófa esetén az adatok és a naplófájlok biztonsági mentéseit gyakran érdemes megtartani. A következő szakasz áttekintést nyújt az Azure-ba beépített képességekről, valamint a speciális igényeknek megfelelő további beállításokról: 

- Az Azure beépített magas rendelkezésre állást kínál: [magas rendelkezésre állást és Azure SQL Database](sql-database-high-availability.md) 

- A üzletileg kritikus szint feladatátvételi csoportokat, több rendelkezésre állási zónákat, a teljes és differenciált biztonsági másolatokat, valamint az alapértelmezés szerint az időponthoz tartozó visszaállítási biztonsági mentéseket is lehetővé teszi:  
  - [Magas rendelkezésre állás és Azure SQL Database-zóna redundáns konfigurációja](sql-database-high-availability.md#zone-redundant-configuration)
  - [Automatikus biztonsági mentések](sql-database-automated-backups.md)
  - [Azure SQL Database helyreállítása automatizált adatbázis-biztonsági másolatokkal – időponthoz tartozó visszaállítás](sql-database-recovery-using-backups.md#point-in-time-restore)

- További üzletmenet-folytonossági funkciók, például automatikus feladatátvételi csoportok a különböző Azure-térségek az itt leírtak szerint konfigurálhatók: [az üzletmenet folytonosságának áttekintése Azure SQL Database](sql-database-business-continuity.md)

## <a name="next-steps"></a>Következő lépések

- Tekintse át [a Azure SQL Database biztonsági funkcióinak áttekintését](sql-database-security-overview.md)