---
title: Forgatókönyv a közös biztonsági követelmények teljesítéséhez | Microsoft dokumentumok
titleSuffix: Azure SQL Database
description: Ez a cikk általános biztonsági követelményeket és gyakorlati tanácsokat tartalmaz az Azure SQL Database-ben.
ms.service: sql-database
ms.subservice: security
author: VanMSFT
ms.author: vanto
ms.topic: article
ms.date: 02/20/2020
ms.reviewer: ''
ms.openlocfilehash: 39747ac0a7133562bed526f44e30bf4a656127c0
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673611"
---
# <a name="playbook-for-addressing-common-security-requirements-with-azure-sql-database"></a>Forgatókönyv a gyakori biztonsági követelmények kezelésére az Azure SQL-adatbázissal

> [!NOTE]
> Ez a dokumentum gyakorlati tanácsokat tartalmaz a közös biztonsági követelmények megoldására. Nem minden követelmény vonatkozik minden környezetre, és tekintse meg az adatbázis és a biztonsági csapat, amely funkciók at kell végrehajtani.

## <a name="solving-common-security-requirements"></a>Közös biztonsági követelmények megoldása

Ez a dokumentum útmutatást nyújt az Azure SQL Database használatával az új vagy meglévő alkalmazások általános biztonsági követelményeinek megoldásához. Magas szintű biztonsági területek szervezik. Az egyes fenyegetések kezeléséhez tekintse meg a [közös biztonsági fenyegetések és a lehetséges megoldások című szakaszt.](#common-security-threats-and-potential-mitigations) Bár a bemutatott javaslatok egy része alkalmazható alkalmazások áttelepítésekor a helyszíni Azure-ba, áttelepítési forgatókönyvek nem a hangsúly ebben a dokumentumban.

### <a name="azure-sql-database-deployment-offers-covered-in-this-guide"></a>Az azure SQL Database telepítési ajánlatai az ebben az útmutatóban

- [SQL-adatbázisok:](https://docs.microsoft.com/azure/sql-database/sql-database-single-index) [egyetlen adatbázisok](sql-database-single-database.md) és [rugalmas készletek](sql-database-elastic-pool.md) az Azure [SQL Database-kiszolgálókban](sql-database-servers.md)
- [Felügyelt példányok](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index)

### <a name="sql-deployment-offers-not-covered-in-this-guide"></a>Az SQL központi telepítési ajánlatai nem szerepelnek ebben az útmutatóban

- Azure SQL Data Warehouse
- Azure SQL virtuális gépek (IaaS)
- AZ SQL Server helyszíni kiszolgálója

### <a name="audience"></a>Célközönség

Az útmutató célközönségei azok az ügyfelek, akiknek az Azure SQL Database biztonságossá tételére vonatkozó kérdésekkel kell szembenézniük. A bevált gyakorlatokról szóló cikk iránt érdeklődő szerepek többek között a következők:

- Biztonsági építészek
- Biztonsági menedzserek
- Megfelelési tisztviselők
- Adatvédelmi tisztviselők
- Biztonsági mérnökök

### <a name="using-this-guide"></a><a id="using"></a>Az útmutató használata

Ez a dokumentum az Azure [SQL Database](sql-database-security-overview.md) meglévő biztonsági dokumentációjának társaként szolgál.

Eltérő rendelkezés hiányában javasoljuk, hogy kövesse az egyes szakaszokban felsorolt összes ajánlott gyakorlatot a megfelelő cél vagy követelmény elérése érdekében. Az egyes biztonsági megfelelőségi szabványok nak vagy bevált gyakorlatoknak való megfelelés érdekében a fontos jogszabályi megfelelőségi ellenőrzések adott esetben a Követelmények vagy célok szakaszban találhatók. Ezek a jelen dokumentumban hivatkozott biztonsági szabványok és előírások:

- [FedRAMP](https://www.fedramp.gov/documents/): AC-04, AC-06
- [SOC](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/sorhome.html): CM-3, SDL-3
- [ISO/IEC 27001](https://www.iso27001security.com/html/27001.html): Hozzáférés-vezérlés, kriptográfia
- [A Microsoft működési biztonsági garanciával (OSA) kapcsolatos gyakorlatai](https://www.microsoft.com/en-us/securityengineering/osa/practices): #1-6 és #9
- [NIST 800-53 biztonsági vezérlők:](https://nvd.nist.gov/800-53)AC-5, AC-6
- [PCI DSS](https://www.pcisecuritystandards.org/document_library): 6.3.2, 6.4.2

### <a name="feedback"></a>Visszajelzés

Azt tervezzük, hogy továbbra is frissíti az itt felsorolt ajánlásokat és bevált gyakorlatokat. Adja meg a dokumentum bemenetét vagy javítását a cikk alján található **Visszajelzés** hivatkozással.

## <a name="authentication"></a>Hitelesítés

A hitelesítés azt a folyamatot, amely bizonyítja, hogy a felhasználó az, akinek állítja magát. Az Azure SQL Database kétféle hitelesítést támogat:

- SQL-hitelesítés
- Hitelesítés Azure Active Directory-fiókkal

> [!NOTE]
> Előfordulhat, hogy az Azure Active Directory-hitelesítés nem támogatott minden eszköz és külső alkalmazás esetén.

### <a name="central-management-for-identities"></a>Identitások központi kezelése

A központi identitáskezelés a következő előnyökkel jár:

- Csoportfiókok kezelése és felhasználói engedélyek szabályozása az Azure SQL Database-kiszolgálók és -adatbázisok bejelentkezéseinek megkettőzése nélkül.
- Egyszerűsített és rugalmas engedélykezelés.
- Az alkalmazások kezelése nagy méretekben.

**Hogyan kell végrehajtani:**

- Az Azure Active Directory (Azure AD) hitelesítés használata a központi identitáskezeléshez.

**Bevált gyakorlatok:**

- Hozzon létre egy Azure AD-bérlőt, és [hozzon létre felhasználókat](../active-directory/fundamentals/add-users-azure-active-directory.md) az emberi felhasználók képviseletére, és hozzon létre [szolgáltatásnéveket](../active-directory/develop/app-objects-and-service-principals.md) az alkalmazások, szolgáltatások és automatizálási eszközök képviseletére. A szolgáltatásnévi tagok egyenértékűek a Windows és Linux szolgáltatásfiókokkal. 

- Az erőforrásokhoz való hozzáférési jogok hozzárendelése az Azure AD-tagokhoz csoporthozzárendeléssel: Azure AD-csoportok létrehozása, csoportokhoz való hozzáférés biztosítása, és egyéni tagok hozzáadása a csoportokhoz. Az adatbázisban hozzon létre egy bent lévő adatbázis-felhasználókat, amelyek leképezik az Azure AD-csoportokat. Ha az adatbázison belül szeretne engedélyeket rendelni, a felhasználókat a megfelelő engedélyekkel rendelkező adatbázis-szerepkörökbe helyezze.
  - Tekintse meg az [Azure Active Directory-hitelesítés konfigurálása és kezelése SQL-sel](sql-database-aad-authentication-configure.md) című cikkeket, valamint [az Azure AD használata az SQL-hitelesítéshez című cikket.](sql-database-aad-authentication.md)
  > [!NOTE]
  > Egy felügyelt példányban is létrehozhat olyan bejelentkezéseket, amelyek leképezhetik az Azure AD-tagok a fő adatbázisban. Lásd: [CREATE LOGIN (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current).

- Az Azure AD-csoportok használata leegyszerűsíti az engedélykezelést, és mind a csoport tulajdonosa, és az erőforrás tulajdonosa adhat hozzá/eltávolíthat tagokat a csoportból. 

- Hozzon létre egy külön csoportot az Azure AD-rendszergazda sql DB-kiszolgálókhoz.

  - Tekintse meg az [Azure Active Directory-rendszergazda kiépítése az Azure SQL Database-kiszolgálóhoz című cikket.](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)

- Az Azure AD-csoporttagság változásainak figyelése az Azure AD naplózási tevékenységjelentései használatával. 

- Felügyelt példány esetén egy külön lépés szükséges az Azure AD-rendszergazda létrehozásához. 
  - Tekintse meg a témakört, [az Azure Active Directory rendszergazdájának kiépítése a felügyelt példányhoz című cikket.](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance) 

> [!NOTE]
> - Az Azure AD-hitelesítés az Azure SQL-naplózási naplókban kerül rögzítésre, de az Azure AD bejelentkezési naplóiban nem.
> - Az Azure-ban megadott RBAC-engedélyek nem vonatkoznak az Azure SQL DB-engedélyeire. Ezeket az engedélyeket manuálisan kell létrehozni/leképezni az SQL DB-ban a meglévő SQL-engedélyek használatával.
> - Az ügyféloldali Azure AD-hitelesítés internet-hozzáférésre vagy felhasználó által definiált útvonal (UDR) egy virtuális hálózathoz való hozzáférést igényel.
> - Az Azure AD-hozzáférési jogkivonat az ügyféloldalon gyorsítótárazva van, és élettartama a jogkivonat konfigurációjától függ. Tekintse meg a [cikket, konfigurálható jogkivonat élettartama az Azure Active Directoryban](../active-directory/develop/active-directory-configurable-token-lifetimes.md)
> - Az Azure AD-hitelesítéssel kapcsolatos problémák elhárításával kapcsolatos útmutatásért tekintse meg a következő blogot:<https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991>

### <a name="multi-factor-authentication-mfa"></a>Multi-Factor Authentication (MFA)

> Említett: OSA Gyakorlat #2, ISO Access Control (AC)

Az Azure többtényezős hitelesítés (MFA) segít a további biztonságot azáltal, hogy több hitelesítési forma.

**Hogyan kell végrehajtani:**

- [Engedélyezze](../active-directory/authentication/concept-mfa-howitworks.md) az MFA-t az Azure AD-ben feltételes hozzáférés használatával, és használjon interaktív hitelesítést. 

- A másik lehetőség, hogy a teljes Azure AD- vagy AD-tartományban engedélyezze az MFA-t.

**Bevált gyakorlatok:**

- Feltételes hozzáférés aktiválása az Azure AD-ben (prémium szintű előfizetést igényel). 
  - Tekintse meg a cikket, [feltételes hozzáférés az Azure AD-ben.](../active-directory/conditional-access/overview.md)  

- Hozzon létre Azure AD-csoport(ok) és engedélyezze az MFA-szabályzat a kiválasztott csoportok az Azure AD feltételes hozzáférés használatával. 
  - Lásd a [feltételes hozzáférés telepítésének megtervezése című cikket.](../active-directory/conditional-access/plan-conditional-access.md) 

- MFA engedélyezhető a teljes Azure AD vagy a teljes Active Directory összevont Azure AD. 

- Az Azure AD Interactive hitelesítési mód használata az SQL DB-hez, ahol a jelszót interaktív módon kérik, majd az MFA-hitelesítést:      
  - Az SSMS-ben használjon univerzális hitelesítést. Lásd a cikket: [A többtényezős AAD-hitelesítés használata az Azure SQL Database és az Azure SQL Data Warehouse (SSMS támogatása MFA)](sql-database-ssms-mfa-authentication.md)című cikkben.
  - Az SQL Server Data Tools (SSDT) által támogatott interaktív hitelesítés használata. Tekintse meg az Azure Active Directory támogatását az [SQL Server Data Tools (SSDT) szolgáltatásban című cikket.](https://docs.microsoft.com/sql/ssdt/azure-active-directory?view=azuresqldb-current)
  - Használjon más SQL-eszközöket, amelyek támogatják az MFA-t. 
    - Az SSMS varázsló támogatja az adatbázis exportálását/kinyerését/telepítését  
    - [sqlpackage.exe](https://docs.microsoft.com/sql/tools/sqlpackage): "/ua" opció 
    - [sqlcmd segédprogram](https://docs.microsoft.com/sql/tools/sqlcmd-utility): option -G (interaktív)
    - [bcp Segédprogram](https://docs.microsoft.com/sql/tools/bcp-utility): option -G (interaktív) 

- Valósítsa meg alkalmazásait az Azure SQL Database-hez való csatlakozáshoz az MFA-támogatással rendelkező interaktív hitelesítés használatával. 
  - Tekintse meg a Csatlakozás az [Azure SQL-adatbázishoz azure-beli többtényezős hitelesítéssel című cikket.](active-directory-interactive-connect-azure-sql-db.md) 
  > [!NOTE]
  > Ehhez a hitelesítési módhoz felhasználói identitások szükségesek. Azokban az esetekben, ahol egy megbízható identitásmodellt használnak, amely megkerüli az egyes Azure AD-felhasználói hitelesítést (például az Azure-erőforrások felügyelt identitásának használatával), az MFA nem vonatkozik.

### <a name="minimize-the-use-of-password-based-authentication-for-users"></a>A jelszóalapú hitelesítés használatának minimalizálása a felhasználók számára

> Említett: OSA Gyakorlat #4, ISO Access Control (AC)

A jelszóalapú hitelesítési módszerek a hitelesítés gyengébb formáját képezik. Hitelesítő adatok at lehet feltört vagy tévesen adott el.

**Hogyan kell végrehajtani:**

- Használjon egy Azure AD integrált hitelesítést, amely kiküszöböli a jelszavak használatát.

**Bevált gyakorlatok:**

- Használjon egyszeri bejelentkezési hitelesítést Windows-hitelesítő adatokkal. A helyszíni AD-tartomány tanusítsa az Azure AD-vel, és használjon integrált Windows-hitelesítést (az Azure AD-vel tartományhoz csatlakozó gépekhez).
  - Tekintse meg az [Azure AD integrált hitelesítéss sms-támogatáscímű cikkét.](sql-database-aad-authentication-configure.md#active-directory-integrated-authentication)

### <a name="minimize-the-use-of-password-based-authentication-for-applications"></a>A jelszóalapú hitelesítés használatának minimalizálása az alkalmazásokban 

> Említett: OSA Gyakorlat #4, ISO Access Control (AC)

**Hogyan kell végrehajtani:**

- Engedélyezze az Azure felügyelt identitását. Integrált vagy tanúsítványalapú hitelesítést is használhat. 

**Bevált gyakorlatok:**

- Felügyelt identitások használata [az Azure-erőforrásokhoz.](../active-directory/managed-identities-azure-resources/overview.md)
  - [Rendszerhez rendelt felügyelt identitás](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md) 
  - [Felhasználó által hozzárendelt felügyelt identitás](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
  - [Az Azure SQL Database használata az alkalmazásszolgáltatásból felügyelt identitással (kódmódosítás nélkül)](https://github.com/Azure-Samples/app-service-msi-entityframework-dotnet)

- Használjon tanúsítványalapú hitelesítést egy alkalmazáshoz. 
  - Lásd ezt a [kódmintát](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth/token). 

- Azure AD-hitelesítés tintegrált összevont tartomány és a tartományhoz csatlakozó gép (lásd a fenti szakaszt).
  - Az [integrált hitelesítést a mintaalkalmazásban tekintse](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth/integrated)meg.

### <a name="protect-passwords-and-secrets"></a>Jelszavak és titkok védelme

Olyan esetekben, amikor a jelszavak nem elkerülhetők, győződjön meg arról, hogy azok biztonságosak.

**Hogyan kell végrehajtani:**

- Az Azure Key Vault használatával jelszavakat és titkos kulcsokat tárol. Adott esetben használja az Azure SQL Database többfa-használatát az Azure AD-felhasználókkal.

**Bevált gyakorlatok:**

- Ha a jelszavak és a titkos kulcsok elkerülése nem lehetséges, tárolja a felhasználói jelszavakat és az alkalmazástitkos kulcsokat az Azure Key Vaultban, és kezelje a hozzáférést a Key Vault hozzáférési szabályzatokon keresztül. 

- Különböző alkalmazásfejlesztési keretrendszerek is kínálhat keretrendszer-specifikus mechanizmusok az alkalmazás ban a titkok védelmére. Például: [ASP.NET alapalkalmazás](https://docs.microsoft.com/aspnet/core/security/app-secrets?view=aspnetcore-2.1&tabs=windows).

### <a name="use-sql-authentication-for-legacy-applications"></a>SQL-hitelesítés használata örökölt alkalmazásokhoz 

AZ SQL-hitelesítés a felhasználó hitelesítésére utal, amikor felhasználónév vel és jelszóval csatlakozik az Azure SQL Database-hez. Minden SQL Database-kiszolgálón vagy felügyelt példányban létre kell hozni egy bejelentkezési adatokat, és minden adatbázisban létre kell hozni egy felhasználót.

**Hogyan kell végrehajtani:**

- SQL-hitelesítés használata.

**Bevált gyakorlatok:**

- Kiszolgálói rendszergazdaként hozzon létre bejelentkezéseket és felhasználókat. Ha nem használ szkonzist adatbázis-felhasználókat jelszóval, az összes jelszó a fő adatbázisban tárolódik.
  - Lásd az [ADATBÁZIS-hozzáférés szabályozása és megadása az SQL Database és az SQL Data Warehouse számára című cikket.](sql-database-manage-logins.md)

## <a name="access-management"></a>Hozzáférés-kezelés

A hozzáférés-kezelés az azure SQL Database-hez való hozzáférés és jogosultságok vezérlésének és kezelésének folyamata.

### <a name="implement-principle-of-least-privilege"></a>A legkisebb jogosultság elvének végrehajtása

> A következőben említi: FedRamp ellenőrzések AC-06, NIST: AC-6, OSA Practice #3

A minimális jogosultság elve kimondja, hogy a felhasználók nem rendelkeznek több jogosultsággal, mint szükséges a feladataik elvégzéséhez. További információ: A [Csak elég adminisztráció](https://docs.microsoft.com/powershell/scripting/learn/remoting/jea/overview).

**Hogyan kell végrehajtani:**

Csak a szükséges [engedélyeket](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) rendelje hozzá a szükséges feladatok elvégzéséhez:

- SQL adatsíkban: 
    - Részletes engedélyek és felhasználó által definiált adatbázis-szerepkörök (vagy kiszolgálói szerepkörök használata a hibajelzőben): 
        1. A szükséges szerepkörök létrehozása
            - [SZEREPKÖR LÉTREHOZÁSA](https://docs.microsoft.com/sql/t-sql/statements/create-role-transact-sql)
            - [KISZOLGÁLÓI SZEREPKÖR LÉTREHOZÁSA](https://docs.microsoft.com/sql/t-sql/statements/create-server-role-transact-sql)
        1. Szükséges felhasználók létrehozása
            - [FELHASZNÁLÓ LÉTREHOZÁSA](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql)
        1. Felhasználók hozzáadása tagként a szerepkörökhöz 
            - [SZEREP MÓDOSÍTÁSA](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql)
            - [KISZOLGÁLÓI SZEREPKÖR MÓDOSÍTÁSA](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql)
        1. Ezután rendeljen engedélyeket a szerepkörökhöz. 
            - [Támogatás](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) 
    - Ügyeljen arra, hogy a felhasználókat ne rendelje ni felesleges szerepkörökhöz.

- Az Azure Resource Managerben:
  - Használjon beépített szerepköröket, ha rendelkezésre áll, vagy egyéni RBAC-szerepköröket, és rendelje hozzá a szükséges engedélyeket.
    - [Beépített szerepkörök az Azure-hoz](../role-based-access-control/built-in-roles.md) 
    - [Egyéni szerepkörök Azure-erőforrásokhoz](../role-based-access-control/custom-roles.md)

**Bevált gyakorlatok:**

Az alábbi ajánlott eljárások nem kötelezőek, de a biztonsági stratégia jobb kezelhetőségét és támogathatóságát eredményezik: 

- Ha lehetséges, kezdje a lehető legkevesebb engedélykészlettel, és kezdje el egyenként hozzáadni az engedélyeket, ha valóban szükség van (és indoklás) – szemben az ellenkező megközelítéssel: az engedélyek lépésről lépésre. 

- Tartózkodjon attól, hogy engedélyeket jelöljön ki az egyes felhasználókhoz. Ehelyett következetesen használja a szerepköröket (adatbázis- vagy kiszolgálói szerepköröket). A szerepkörök nagyban hozzájárulnak a jelentéskészítési és hibaelhárítási engedélyekhez. (Az Azure RBAC csak szerepkörökön keresztüli engedélyhozzárendelést támogatja.) 

- Egyéni szerepkörök létrehozása és használata a szükséges engedélyekkel. A gyakorlatban használt tipikus szerepkörök: 
  - Biztonsági telepítés 
  - Rendszergazda 
  - Fejlesztői 
  - Támogató személyzet 
  - Könyvvizsgáló 
  - Automatizált folyamatok 
  - Végfelhasználó 
  
- Csak akkor használjon beépített szerepköröket, ha a szerepkörök engedélyei pontosan megfelelnek a felhasználó szükséges engedélyeinek. A felhasználókat több szerepkörhöz is hozzárendelheti. 

- Ne feledje, hogy az SQL Server Database Engine engedélyei a következő hatókörökre alkalmazhatók. Minél kisebb a hatókör, annál kisebb a megadott engedélyek hatása: 
  - Azure SQL Database-kiszolgáló (speciális szerepkörök a fő adatbázisban) 
  - Adatbázis 
  - Séma
      - Ajánlott sémák használatával engedélyeket adni az adatbázison belül. (lásd még: [Sématervezés az SQL Server kiszolgálóhoz: a sématervezésre vonatkozó javaslatok a biztonságot szem előtt tartva](http://andreas-wolter.com/en/schema-design-for-sql-server-recommendations-for-schema-design-with-security-in-mind/))
  - Objektum (táblázat, nézet, eljárás stb.) 
  > [!NOTE]
  > Nem ajánlott engedélyeket alkalmazni az objektum szintjén, mert ez a szint szükségtelen bonyolulttá teszi a teljes megvalósítást. Ha úgy dönt, hogy objektumszintű engedélyeket használ, azokat egyértelműen dokumentálni kell. Ugyanez vonatkozik az oszlopszintű engedélyekre is, amelyek ugyanezen okok miatt még kevésbé ajánlhatók. Azt is vegye figyelembe, hogy a táblaszintű [DENY](https://docs.microsoft.com/sql/t-sql/statements/deny-object-permissions-transact-sql) alapértelmezés szerint nem bírálja felül az oszlopszintű GRANT-et. Ehhez aktiválni kell a [kiszolgáló konfigurációjának közös feltételeket.](https://docs.microsoft.com/sql/database-engine/configure-windows/common-criteria-compliance-enabled-server-configuration-option)

- Rendszeres ellenőrzéseket végezhet [a biztonsági rés felmérésével (VA)](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment) a túl sok engedély ellenőrzéséhez.

### <a name="implement-separation-of-duties"></a>A vámok elkülönítésének végrehajtása

> Itt említi: FedRamp: AC-04, NIST: AC-5, ISO: A.6.1.2, PCI 6.4.2, SOC: CM-3, SDL-3

A feladatok elkülönítése, más néven a feladatok elkülönítése azt a követelményt írja le, hogy a bizalmas feladatokat több feladatra kell felosztani, amelyek különböző felhasználókhoz vannak rendelve. A feladatok elkülönítése segít megelőzni az adatvédelmi incidenseket.

**Hogyan kell végrehajtani:**

- A feladatok elkülönítésének szükséges szintjének meghatározása. Példák: 
  - Fejlesztési/teszt-és éles környezetek között 
  - Biztonsági szempontból érzékeny feladatok vs Adatbázis-rendszergazda (DBA) felügyeleti szintű feladatok vs fejlesztői feladatok. 
    - Példák: Auditor, szerepkörszintű biztonság (RLS) biztonsági házirendlétrehozása, SQL-adatbázis-objektumok megvalósítása DDL-engedélyekkel.

- A rendszerhez hozzáférő felhasználók (és automatizált folyamatok) átfogó hierarchiájának azonosítása.

- Hozzon létre szerepköröket a szükséges felhasználói csoportok nak megfelelően, és rendeljen engedélyeket a szerepkörökhöz. 
  - Felügyeleti szintű feladatok az Azure Portalon vagy a PowerShell-automatizálási rBAC-szerepkörök használata. Keressen egy a követelménynek megfelelő beépített szerepkört, vagy hozzon létre egy egyéni RBAC-szerepkört a rendelkezésre álló engedélyek használatával 
  - Kiszolgálói szerepkörök létrehozása kiszolgálószintű feladatokhoz (új bejelentkezések, adatbázisok létrehozása) felügyelt példányban. 
  - Adatbázis-szerepkörök létrehozása adatbázisszintű feladatokhoz.

- Bizonyos bizalmas feladatok esetén fontolja meg a tanúsítvány által aláírt speciális tárolt eljárások létrehozását a feladatok felhasználók nevében történő végrehajtásához. 
  - Példa: [Oktatóanyag: Tárolt eljárások aláírása tanúsítvánnyal](https://docs.microsoft.com/sql/relational-databases/tutorial-signing-stored-procedures-with-a-certificate) 

- Átlátható adattitkosítás (TDE) megvalósítása az ügyfél által felügyelt kulcsokkal az Azure Key Vaultban, hogy lehetővé tegye a feladatok elkülönítését az adatok tulajdonosa és a biztonság tulajdonosa között. 
  - Tekintse meg az [Ügyfél által felügyelt kulcsok konfigurálása az Azure Storage titkosításához az Azure Portalon című cikket.](../storage/common/storage-encryption-keys-portal.md) 

- Annak érdekében, hogy a DBA ne láthassa a rendkívül érzékenynek ítélt és a DBA-feladatokat továbbra is ellátható adatokat, használhatja a Mindig titkosított szerepkör-elkülönítést. 
  - Lásd a cikkeket, [A mindig titkosított kulcskezelés áttekintése](https://docs.microsoft.com/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted), [a szerepkör-elkülönítéssel történő kulcskiépítés](https://docs.microsoft.com/sql/relational-databases/security/encryption/configure-always-encrypted-keys-using-powershell#KeyProvisionWithRoles)és az [oszlopmesterkulcs elforgatása szerepkör-elkülönítéssel.](https://docs.microsoft.com/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell#column-master-key-rotation-with-role-separation) 

- Azokban az esetekben, amikor ez nem valósítható meg, legalábbis nem anélkül, hogy jelentős költségek és erőfeszítések, amelyek a rendszer közel használhatatlanná, kompromisszumok lehet tenni, és enyhíteni segítségével kompenzáló ellenőrzések, mint például: 
  - Emberi beavatkozás a folyamatokba. 
  - Ellenőrzési nyomvonalak – a naplózásról további információt a kritikus biztonsági események naplózása című [témakörben talál.](#audit-critical-security-events)

**Bevált gyakorlatok:**

- Győződjön meg arról, hogy a fejlesztési/tesztelési és éles környezetekben különböző fiókokat használ. A különböző fiókok segítenek a Test & Production rendszerek szétválasztásában.

- Tartózkodjon attól, hogy engedélyeket jelöljön ki az egyes felhasználókhoz. Ehelyett következetesen használja a szerepköröket (adatbázis- vagy kiszolgálói szerepköröket). A szerepkörök nagyban hozzájárulnak a jelentéskészítési és hibaelhárítási engedélyekhez.

- Akkor használjon beépített szerepköröket, ha az engedélyek pontosan megfelelnek a szükséges engedélyeknek – ha több beépített szerepkör összes engedélyének egyesítése 100%-os egyezést eredményez, egyszerre több szerepkört is hozzárendelhet. 

- Egyéni szerepkörök létrehozása és használata, ha a beépített szerepkörök túl sok engedélyt vagy nem megfelelő engedélyeket adnak. 

- Szerepkör-hozzárendelések is elvégezhető ideiglenesen, más néven dinamikus elkülönítése feladatok (DSD), vagy az SQL Agent Feladat lépéseit a T-SQL vagy az Azure PIM RBAC-szerepkörök használata. 

- Győződjön meg arról, hogy a Rendszerbiztosítási Rendszer nem fér hozzá a titkosítási kulcsokhoz vagy kulcstárolókhoz, és a kulcsokhoz hozzáféréssel rendelkező biztonsági rendszergazdák nem férhetnek hozzá az adatbázishoz. 

- Mindig győződjön meg arról, hogy a biztonsági műveletek naplózási nyomvonala. 

- A beépített RBAC-szerepkörök definícióját lekérheti a használt engedélyek megtekintéséhez, és hozzon létre egy egyéni szerepkört a PowerShell-en keresztül i részletek és kumulációk alapján.

- Mivel a db_owner adatbázis-szerepkör bármely tagja módosíthatja a biztonsági beállításokat, például az Átlátszó adattitkosítást (TDE), vagy módosíthatja az SLO-t, ezt a tagságot óvatosan kell megadni. Sok olyan feladat van azonban, amely db_owner jogosultságot igényel. Feladat, például az adatbázis-beállítások módosítása, például az adatbázis beállításainak módosítása. Az auditálás minden megoldásban kulcsszerepet játszik.

- A db_owner engedélyeit nem lehet korlátozni, ezért a rendszergazdai fiókok nem tekinthetik meg a felhasználói adatokat. Ha egy adatbázisban rendkívül érzékeny adatok vannak, a Mindig titkosított beállítás sal biztonságosan megakadályozhatja, hogy db_owners vagy bármely más DBA megtekintse azokat.

> [!NOTE]
> A feladatok elkülönítésének elérése kihívást jelent a biztonsággal kapcsolatos vagy hibaelhárítási feladatok esetében. Más területek, például a fejlesztési és a végfelhasználói szerepkörök könnyebbelkülöníten elkülöníteni. A legtöbb megfelelőségi vezérlés lehetővé teszi alternatív ellenőrzési funkciók használatát, például a naplózást, ha más megoldások nem praktikusak.

Azoknak az olvasóknak, akik mélyebbre szeretnének merülni a SoD-ben, a következő forrásokat javasoljuk: 

- Azure SQL-adatbázis esetén:  
  - [Adatbázis-hozzáférés szabályozása és megadása az SQL Database és az SQL Data Warehouse számára](sql-database-manage-logins.md)
  - [Az alkalmazásfejlesztő feladatainak motorszétválasztása](https://docs.microsoft.com/previous-versions/sql/sql-server-2008/cc974525(v=sql.100)) 
  - [A feladatok elkülönítése az SQL Server 2014-ben](https://www.microsoft.com/download/details.aspx?id=39269)
  - [Tárolt eljárások aláírása az SQL Server kiszolgálón](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server)

- Az Azure erőforrás-kezelése:
  - [Beépített szerepkörök az Azure-hoz](../role-based-access-control/built-in-roles.md) 
  - [Egyéni szerepkörök Azure-erőforrásokhoz](../role-based-access-control/custom-roles.md)
  - [Az Azure AD kiemelt identitáskezelés használata emelt szintű hozzáféréshez](https://www.microsoft.com/en-us/itshowcase/using-azure-ad-privileged-identity-management-for-elevated-access)

### <a name="perform-regular-code-reviews"></a>Rendszeres kódellenőrzések elvégzése

> Említett: PCI: 6.3.2, SOC: SDL-3 

A vámok elkülönítése nem korlátozódik az adatbázisban lévő adatokra, hanem alkalmazáskódot is tartalmaz. A rosszindulatú kódok potenciálisan megkerülhetik a biztonsági vezérlőket. Az egyéni kód éles környezetben való üzembe helyezése előtt fontos, hogy tekintse át, mi van üzembe helyezve.

**Hogyan kell végrehajtani:**

- Használjon egy adatbázis-eszköz, mint az Azure Data Stu, amely támogatja a forrásvezérlés. 

- Elkülönített kód telepítési folyamatának megvalósítása.

- A főágra való kötelezés előtt egy személynek (a kód szerzőjén kívül) meg kell vizsgálnia a kódot a jogosultságok esetleges kiterjesztésével kapcsolatos kockázatok, valamint a rosszindulatú adatmódosítások szempontjából a csalás és a csalás elleni védelem érdekében. Ez a forrás-ellenőrzési mechanizmusok használatával végezhető el.

**Bevált gyakorlatok:**

- Szabványosítás: Segít egy szabványos eljárás megvalósításában, amelyet követni kell a kódfrissítésekhez. 

- A biztonsági rés felmérése olyan szabályokat tartalmaz, amelyek ellenőrzik a túlzott engedélyeket, a régi titkosítási algoritmusok használatát és az adatbázissémán belüli egyéb biztonsági problémákat. 

- További ellenőrzésekvégezhetők minőségbiztosítási vagy tesztelési környezetben az SQL-injektálásnak kitett kód ot vizsgáló komplex veszélyforrások elleni védelem használatával.

- Példák arra, hogy mire kell odanézni: 
  - Felhasználó létrehozása vagy biztonsági beállítások módosítása egy automatikus SQL-kódfrissítési központi telepítésből. 
  - Olyan tárolt eljárás, amely a megadott paraméterektől függően nem megfelelő módon frissíti a cella pénzbeli értékét. 

- Győződjön meg róla, hogy a felülvizsgálatot végző személy nem a származási kód szerzője, és jól ismeri a kód-felülvizsgálatok és biztonságos kódolás.

- Ügyeljen arra, hogy ismeri a kódváltozások összes forrását. A kód lehet a T-SQL parancsfájlokban. Lehet ad-hoc parancsokat kell végrehajtani, vagy telepíteni a nézetek, funkciók, eseményindítók és tárolt eljárások formáiban. Része lehet az SQL Agent feladatdefiníciók (lépések). SSIS-csomagokból, Az Azure Data Factoryból és más szolgáltatásokból is végrehajtható.

## <a name="data-protection"></a>Adatvédelem

Az adatvédelem olyan képességek összese, amelyek titkosítással vagy homályosítással védik a fontos információkat a feltöréstől.

> [!NOTE]
> A Microsoft fips 140-2 11-es szintű kompatibilisnek tekinti az Azure SQL Database-t. Ez a FIPS 140-2 Level 1 elfogadható algoritmusok és a FIPS 140-2 Level 1 által ellenőrzött példányok, beleértve a szükséges kulcshosszokkal való konzisztenciát, a kulcskezelést, a kulcsgenerálást és a kulcstárolást is, ellenőrzése után történik. Ez az igazolás lehetővé teszi ügyfeleink számára, hogy válaszoljanak a FIPS 140-2 Level 1 validált példányok használatára vagy követelményére az adatok feldolgozása vagy a rendszerek vagy alkalmazások kézbesítése során. Meghatározzuk a "FIPS 140-2 Level 1 compliant" és a "FIPS 140-2 Level 1 compliance" kifejezéseket, amelyeket a fenti nyilatkozatban használunk annak bizonyítására, hogy szándékukban az Egyesült Államok és A kanadai kormány a "FIPS 140-2 Level 1 validált" kifejezést használja." 


### <a name="encrypt-data-in-transit"></a>Adatok titkosítása az átvitel során

> Említett: OSA Gyakorlat #6, ISO Control Család: Kriptográfia

Védi az adatokat, miközben az adatok az ügyfél és a kiszolgáló között mozognak. Lásd: [Hálózati biztonság](#network-security).

### <a name="encrypt-data-at-rest"></a>Inaktív adatok titkosítása

> Említett: OSA Gyakorlat #6, ISO Control Család: Kriptográfia

Az inaktív titkosítás az adatok titkosítási védelme, ha azok adatbázis-, napló- és biztonságimásolat-fájlokban vannak megőrizve.

**Hogyan kell végrehajtani:**

- [Az Átlátszó adatbázis-titkosítás (TDE)](transparent-data-encryption-azure-sql.md) szolgáltatás felügyelt kulcsokkal alapértelmezés szerint engedélyezve van az Azure SQL Database 2017 után létrehozott adatbázisaiban.
- Felügyelt példányban, ha az adatbázis egy helyszíni kiszolgálót használó visszaállítási műveletből jön létre, az eredeti adatbázis TDE-beállítása tiszteletben marad. Ha az eredeti adatbázisban nincs engedélyezve a TDE, azt javasoljuk, hogy a TDE-t manuálisan kell bekapcsolni a felügyelt példányhoz.

**Bevált gyakorlatok:**

- Ne tárolja az inaktív titkosítást igénylő adatokat a fő adatbázisban. A fő adatbázis nem titkosítható TDE-vel.

- Használja az ügyfél által felügyelt kulcsokat az Azure Key Vaultban, ha nagyobb átláthatóságra és a TDE-védelem részletes vezérlésére van szüksége. Az Azure Key Vault lehetővé teszi, hogy bármikor visszavonja az engedélyeket, hogy az adatbázis elérhetetlenné váljon. Központilag kezelheti a TDE-védőket más billentyűkkel együtt, vagy elforgathatja a TDE-védőt a saját ütemezése szerint az Azure Key Vault használatával.

- Ha ügyfél által felügyelt kulcsokat használ az Azure Key Vaultban, kövesse a [TDE Azure Key Vault szolgáltatással történő konfigurálásának irányelvei és](transparent-data-encryption-byok-azure-sql.md#recommendations-when-configuring-akv) [a Geo-DR konfigurálása az Azure Key Vault szolgáltatással](transparent-data-encryption-byok-azure-sql.md#geo-dr-and-customer-managed-tde)című témakört.

### <a name="protect-sensitive-data-in-use-from-high-privileged-unauthorized-users"></a>A használatban lévő bizalmas adatok védelme a magas jogosultságú, jogosulatlan felhasználókkal szemben

A használatban lévő adatok az adatbázis-rendszer memóriájában az SQL-lekérdezések végrehajtása során tárolt adatok. Ha az adatbázis bizalmas adatokat tárol, előfordulhat, hogy a szervezetnek biztosítania kell, hogy a kiemelt jogosultságokkal rendelkező felhasználók ne tekinthessék meg az adatbázis bizalmas adatait. A magas jogosultságú felhasználóknak, például a Microsoft-operátoroknak vagy a szervezet adattárrendszerének képesnek kell lenniük az adatbázis kezelésére, de nem tudják megtekinteni és potenciálisan kiszivárogni a bizalmas adatokat az SQL Server folyamatmemóriájából vagy az adatbázis lekérdezésével.

A szabályzatok, amelyek meghatározzák, hogy mely adatok bizalmasak, és hogy a bizalmas adatokat kell-e titkosítani a memóriában, és nem érhető el a rendszergazdák számára egyszerű szöveges, a szervezetre jellemző, és megfelelőségi előírásokat kell betartania. Kérjük, olvassa el a kapcsolódó követelményt: [Bizalmas adatok azonosítása és címkézése](#identify-and-tag-sensitive-data).

**Hogyan kell végrehajtani:**

- Használja [mindig titkosított](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) annak biztosítására, bizalmas adatok nem érhetők el egyszerű szöveges Azure SQL Database, még a memória/ használat ban. A Mindig titkosított rendszer megvédi az adatokat az adatbázis-rendszergazdáktól (KA-k) és a felhőalapú rendszergazdáktól (vagy a rossz szereplőktől, akik nagy jogosultságú, de nem engedélyezett felhasználóknak adnak megszemélyesítést), és nagyobb ellenőrzést biztosít az adatok elérésének szabályozása felett.

**Bevált gyakorlatok:**

- Mindig titkosítva nem helyettesíti az inaktív (TDE) vagy az átvitel (SSL/TLS) titkosítását. Mindig titkosított nem használható a nem érzékeny adatok a teljesítmény és a funkcionalitás hatását. A TDE és a Transport Layer Security (TLS) használatával együtt mindig titkosított használata ajánlott az inaktív, a tranzit- és a használat közbeni adatok átfogó védelméhez. 

- Mérje fel az azonosított bizalmas adatoszlopok titkosításának hatását, mielőtt a Mindig titkosítva van egy éles adatbázisban. Általában mindig titkosított csökkenti a titkosított oszlopok lekérdezéseinek funkcióit, és egyéb korlátozásokkal rendelkezik, amelyek a [Mindig titkosított - Szolgáltatás részletei](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine#feature-details). Ezért előfordulhat, hogy újra kell megtervezze az alkalmazást a funkció újbóli megvalósításához, a lekérdezés nem támogatja az ügyféloldalon vagy/és újrafaktorálja az adatbázissémát, beleértve a tárolt eljárások, függvények, nézetek és eseményindítók definícióit. Előfordulhat, hogy a meglévő alkalmazások nem működnek titkosított oszlopokkal, ha nem tartják be a Mindig titkosított korlátozásokat és korlátozásokat. Míg a Microsoft-eszközök, -termékek és -szolgáltatások ökoszisztémája folyamatosan bővül, számos nem működik titkosított oszlopokkal. Az oszlopok titkosítása a számítási feladatok jellemzőitől függően hatással lehet a lekérdezés teljesítményére is. 

- Mindig titkosított kulcsok kezelése szerepkör-elkülönítéssel, ha mindig titkosított az adatok védelme a rosszindulatú rendszerfájlok ellen. A szerepkör-elkülönítéssel a biztonsági rendszergazda hozza létre a fizikai kulcsokat. A DBA létrehozza az oszlopfőkulcs és az oszloptitkosítási kulcs metaadat-objektumait, amelyek a fizikai kulcsokat írják le az adatbázisban. A folyamat során a biztonsági rendszergazdának nincs szüksége az adatbázishoz való hozzáférésre, és a DBA-nak nem kell a fizikai kulcsokhoz való hozzáférést egyszerű szövegként. 
  - A részleteket a [Kulcsok kezelése szerepkör-elkülönítéssel](https://docs.microsoft.com/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted#managing-keys-with-role-separation) című cikkben találja. 

- Az oszlopfőkulcsokat az Azure Key Vaultban tárolja a felügyelet megkönnyítése érdekében. Kerülje a Windows Tanúsítványtároló (és általában az elosztott kulcstároló-megoldások használatát, szemben a központi kulcskezelési megoldásokkal), amelyek megnehezítik a kulcskezelést. 

- Alaposan gondolja át a több kulcs (oszloptörzs- vagy oszloptitkosítási kulcsok) használatának kompromisszumait. A kulcskezelési költségek csökkentése érdekében tartsa kicsiben a kulcsok számát. Adatbázisonként egy oszlopfőkulcs és egy oszloptitkosítási kulcs általában elegendő állandósult állapotú környezetekben (nem a kulcselforgatás közepén). További kulcsokra lehet szüksége, ha különböző felhasználói csoportokkal rendelkezik, amelyek mindegyike különböző kulcsokat használ, és különböző adatokhoz fér hozzá.  

- Az oszlopmesterkulcsok elforgatása a megfelelőségi követelmények nek megfelelően. Ha oszloptitkosítási kulcsokat is el kell forgatnia, fontolja meg az online titkosítás használatát az alkalmazás állásidejének minimalizálása érdekében. 
  - Lásd a [teljesítmény- és rendelkezésre állási szempontok című cikket.](https://docs.microsoft.com/sql/relational-databases/security/encryption/configure-column-encryption-using-powershell#performance-and-availability-considerations) 

- Akkor használjon determinisztikus titkosítást, ha az adatokkal kapcsolatos számításokat (egyenlőséget) támogatni kell. Ellenkező esetben használjon véletlenszerű titkosítást. Kerülje a determinisztikus titkosítás trópia vagy nyilvánosan ismert terjesztési adatkészletek használata determinisztikus titkosítást. 

- Ha aggódik amiatt, hogy harmadik fél az Ön beleegyezése nélkül legálisan fér hozzá az adataihoz, győződjön meg arról, hogy minden olyan alkalmazás és eszköz, amely egyszerű szöveges formátumban fér hozzá a kulcsokhoz és az adatokhoz, a Microsoft Azure Cloud on kívül fusson. A kulcsokhoz való hozzáférés nélkül a harmadik fél nem fogja visszafejteni az adatokat, hacsak nem kerülik meg a titkosítást.

- Mindig titkosított nem könnyen támogatja az ideiglenes hozzáférést a kulcsokhoz (és a védett adatokhoz). Ha például meg kell osztania a kulcsokat egy DBA-val, hogy a DBA bizonyos tisztítási műveleteket végezhet a bizalmas és titkosított adatokon. A megbízhatóság egyetlen módja az adatokhoz való hozzáférés visszavonásának a DBA-ból az lesz, hogy elforgatja mind az oszloptitkosítási kulcsokat, mind az adatokat védő oszlopfőkulcsokat, ami költséges művelet. 

- A titkosított oszlopokban lévő egyszerű szöveges értékek eléréséhez a felhasználónak hozzáféréssel kell rendelkeznie az oszlopokat védő CMK-hez, amely a CMK-t tároló kulcstárolóban van konfigurálva. A felhasználónak rendelkeznie kell a **VIEW ANY COLUMN MASTER KEY DEFINITION(OSZLOPMASTER KEY DEFINITION) és** bármely **oszloptitkosítási kulcsdefiníciós** adatbázis engedélyeinek megtekintése is.

### <a name="control-access-of-application-users-to-sensitive-data-through-encryption"></a>Az alkalmazás felhasználók bizalmas adatokhoz való hozzáférésének szabályozása titkosítással

A titkosítás segítségével biztosítható, hogy csak a kriptográfiai kulcsokhoz hozzáféréssel rendelkező alkalmazásfelhasználók tekinthessék meg vagy frissíthessék az adatokat.

**Hogyan kell végrehajtani:**

- Cellszintű titkosítás (CLE) használata. A részleteket lásd a cikkben: [Titkosíthatja az adatoszlopot.](https://docs.microsoft.com/sql/relational-databases/security/encryption/encrypt-a-column-of-data) 
- Használja mindig titkosított, de tudatában kell lennie annak korlátozását. A korlátozások az alábbiakban találhatók.

**Ajánlott eljárások**

Cle használata esetén:

- A kulcsokhoz való hozzáférés szabályozása SQL-engedélyek és szerepkörök segítségével. 

- Az adatok titkosításához használja az AES-t (AES 256 ajánlott). Az algoritmusok, például az RC4, A DES és a TripleDES elavultak, és ismert biztonsági rések miatt nem használhatók. 

- A szimmetrikus kulcsokat aszimmetrikus kulcsokkal/tanúsítványokkal (nem jelszóval) védheti a 3DES használatának elkerülése érdekében. 

- Legyen óvatos, amikor egy adatbázist cellszintű titkosítással telepít át exportálási/importálási (bacpac fájlok) segítségével. 
  - Tekintse meg a cikket, [javaslatok a cellaszintű titkosítás az Azure SQL Database-ben,](https://blogs.msdn.microsoft.com/sqlsecurity/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database/) hogyan lehet megakadályozni a kulcsok elvesztése az adatok áttelepítése során, és egyéb ajánlott eljárásokat.

Ne feledje, hogy a Mindig titkosított elsődleges célja a használatban lévő bizalmas adatok védelme az Azure SQL Database (felhőoperátorok, Adatrendszerű szolgáltatók) magas jogosultságú felhasználóitól – [lásd: Bizalmas adatok védelme a magas jogosultságú, jogosulatlan felhasználókkal szemben.](#protect-sensitive-data-in-use-from-high-privileged-unauthorized-users) Az alábbi kihívásokat kell figyelembe venni, amikor az Mindig titkosított használatával védi az adatokat az alkalmazás felhasználóitól:

- Alapértelmezés szerint a Mindig titkosított microsoftos ügyfélillesztők az oszloptitkosítási kulcsok globális (alkalmazásonként egyet) gyorsítótárát tartják fenn. Amint egy ügyfél-illesztőprogram beszerez egy egyszerű szöveges oszloptitkosítási kulcsot, és kapcsolatba lép egy oszlopfőkulcsot tároló kulcstárolóval, a rendszer gyorsítótárba helyezi az egyszerű szöveges oszloptitkosítási kulcsot. Ez kihívást jelent a többfelhasználós alkalmazások felhasználóitól származó adatok elkülönítése. Ha az alkalmazás megszemélyesíti a végfelhasználókat, amikor egy kulcstárolóval (például az Azure Key Vaultdal) kommunikálnak, miután a felhasználó lekérdezése oszloptitkosítási kulccsal feltölti a gyorsítótárat, egy későbbi lekérdezés, amely ugyanazt a kulcsot igényli, de egy másik felhasználó által aktivált, a gyorsítótárazott kulcsot fogja használni. Az illesztőprogram nem hívja meg a kulcstárolót, és nem ellenőrzi, hogy a második felhasználó rendelkezik-e engedéllyel az oszloptitkosítási kulcs eléréséhez. Ennek eredményeképpen a felhasználó akkor is láthatja a titkosított adatokat, ha a felhasználó nem fér hozzá a kulcsokhoz. A felhasználók többfelhasználós alkalmazásokon belüli elkülönítésének elérése érdekében letilthatja az oszloptitkosítási kulcs gyorsítótárazását. A gyorsítótárazás letiltása további teljesítménybeli költségeket eredményez, mivel az illesztőprogramnak minden adattitkosítási vagy visszafejtési művelethez kapcsolatba kell lépnie a kulcstárolóval.

### <a name="protect-data-against-unauthorized-viewing-by-application-users-while-preserving-data-format"></a>Az adatok védelme az alkalmazásfelhasználók általi jogosulatlan megtekintésellen az adatformátum megőrzése mellett
Egy másik módszer, amely megakadályozza, hogy a jogosulatlan felhasználók megtekinthessék az adatokat, az adatok elfedése vagy maszkolása az adattípusok és -formátumok megőrzése mellett annak érdekében, hogy a felhasználói alkalmazások továbbra is kezelni és megjeleníteni tudják az adatokat.

**Hogyan kell végrehajtani:**

- A [dinamikus adatmaszkolás használatával](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) elhomályosítja a táblázatoszlopokat.

> [!NOTE]
> A Mindig titkosított nem működik a dinamikus adatmaszkolással. Nem lehet ugyanazt az oszlopot titkosítani és maszkolni, ami azt jelenti, hogy rangsorolnia kell a használatban lévő adatok védelmét, vagy el kell fednie az adatokat az alkalmazás felhasználói számára a dinamikus adatmaszkoláson keresztül.

**Bevált gyakorlatok:**

> [!NOTE]
> A dinamikus adatmaszkolás nem használható az adatok védelmére a magas jogosultságú felhasználókkal szemben. A maszkolási házirendek nem vonatkoznak a rendszergazdai hozzáféréssel rendelkező felhasználókra, például a db_owner.

- Ne engedélyezze az alkalmazásfelhasználókszámára az alkalmi lekérdezések futtatását (mivel előfordulhat, hogy megkerülhetik a dinamikus adatmaszkolást).  
  - Lásd a cikket, [Megkerülése maszkolás segítségével következtetés vagy brute-force technikák](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking#security-note-bypassing-masking-using-inference-or-brute-force-techniques) a részletekért.  

- Használjon megfelelő hozzáférés-vezérlési házirendet (SQL-engedélyeken, szerepkörökön, RLS-en keresztül) a felhasználói engedélyek korlátozásához a maszkolt oszlopokban történő frissítéshez. A maszk létrehozása az oszlopban nem akadályozza meg az oszlop frissítését. Azok a felhasználók, akik maszkolt adatokat kapnak a maszkolt oszlop lekérdezésekekénekkor, frissíthetik az adatokat, ha rendelkeznek írási engedélyekkel.    

-  A dinamikus adatmaszkolás nem őrzi meg a maszkolt értékek statisztikai tulajdonságait. Ez hatással lehet a lekérdezés eredményeire (például a szűrési predikátumokat vagy a maszkolt adatokillesztéseket tartalmazó lekérdezéseket).

## <a name="network-security"></a>Hálózati biztonság
A hálózati biztonság a hozzáférés-vezérlések és a bevált gyakorlatok az Azure SQL Database-be történő átvitel során az adatok védelméhez.

### <a name="configure-my-client-to-connect-securely-to-azure-sql-database"></a>Az ügyfél konfigurálása az Azure SQL Database-hez való biztonságos csatlakozáshoz 
Gyakorlati tanácsok arról, hogyan akadályozhatja meg, hogy a jól ismert biztonsági résekkel rendelkező ügyfélgépek és alkalmazások (például régebbi TLS-protokollok és titkosítási csomagok használatával) csatlakozzanak az Azure SQL Database-hez.

**Hogyan kell végrehajtani:**

- Győződjön meg arról, hogy az Azure SQL Database-hez csatlakozó ügyfélgépek [átviteli rétegbiztonságot (TLS)](sql-database-security-overview.md#transport-layer-security-tls-encryption-in-transit)használnak.

**Bevált gyakorlatok:**

- Az összes alkalmazás és eszköz konfigurálása az SQL DB-hez való csatlakozáshoz engedélyezve a titkosításfunkcióval 
  - Encrypt = On, TrustServerCertificate = Ki (vagy ezzel egyenértékű a nem Microsoft-illesztőprogramokkal). 

- Ha az alkalmazás olyan illesztőprogramot használ, amely nem támogatja a TLS-t, vagy támogatja a TLS egy régebbi verzióját, ha lehetséges, cserélje ki az illesztőprogramot. Ha ez nem lehetséges, gondosan értékelje a biztonsági kockázatokat. 

- Csökkentse a támadási vektorokat az SSL 2.0, SSL 3.0, TLS 1.0 és TLS 1.1 biztonsági rései vel, ha letiltja azokat az Azure SQL [Database/Transport Layer Security (TLS) beállításjegyzék-beállításaihoz](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-10)csatlakozó ügyfélgépeken. 

- Ellenőrizze, hogy az ügyfélen elérhető titkosítási csomagok: [Cipher Suites in TLS/SSL (Schannel SSP)](https://docs.microsoft.com/windows/desktop/SecAuthN/cipher-suites-in-schannel). Pontosabban tiltsa le a 3DES-t [a TLS Cipher Suite Order konfigurálása korként.](https://docs.microsoft.com/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order) 

- Az Azure SQL Database esetében a titkosítás a proxy- és átirányítási kapcsolattípusokesetében is érvénybe lép. Ha felügyelt példányt használ, használja a **Proxy** kapcsolat típusát (alapértelmezett), mivel ez a kiszolgálói oldalról kényszeríti a titkosítást. Az **átirányítási** kapcsolat típusa jelenleg nem támogatja a titkosítás-kényszerítést, és csak privát IP-kapcsolatokon érhető el. 

- További információ: [Azure SQL Connectivity Architecture - Connection policy](sql-database-connectivity-architecture.md#connection-policy).


### <a name="minimize-attack-surface"></a>A támadási felület minimalizálása
Minimalizálja a rosszindulatú felhasználók által megtámadható szolgáltatások számát. Az Azure SQL Database hálózati hozzáférés-vezérlésének megvalósítása.

> Említett: OSA gyakorlat #5

**Hogyan kell végrehajtani:**

Egy Azure SQL Database-kiszolgálón (singleton adatbázist vagy rugalmas készleteket tartalmaz):
- Állítsa az Azure-szolgáltatások hoz való hozzáférés engedélyezése OFF beállításra.

- VNet-szolgáltatás végpontok és virtuálishálózati tűzfalszabályok használata.

- Privát hivatkozás használata (előzetes verzió).

Felügyelt példányban:
- Kövesse a [Hálózati követelmények című](sql-database-managed-instance-connectivity-architecture.md#network-requirements)irányelvet. 

**Bevált gyakorlatok:**

- Az Azure SQL-adatbázishoz való hozzáférés korlátozása egy privát végponthoz való csatlakozással (például egy személyes adatelérési út használatával): 
  - A felügyelt példány elkülöníthető egy virtuális hálózaton belül a külső hozzáférés megakadályozása érdekében. Alkalmazások és eszközök, amelyek ugyanabban a vagy társviszonyban lévő virtuális hálózat ugyanabban a régióban közvetlenül érheti el. A különböző régióban található alkalmazások és eszközök a virtuális hálózat és a vnet kapcsolat vagy az ExpressRoute-kapcsolat létesítése a virtuális hálózat hoz létre kapcsolatot. Az ügyfélnek a Hálózati biztonsági csoportok (NSG) segítségével csak azokra az erőforrásokra korlátozhatja a hozzáférést az 1433-as porton keresztül, amelyekhez hozzáférés szükséges egy felügyelt példányhoz. 
  - SQL Database-kiszolgáló (amely egyetlen adatbázisok vagy rugalmas készletek, használja a [Private Link](sql-database-private-endpoint-overview.md) szolgáltatás, amely egy dedikált privát IP az SQL Database-kiszolgáló a virtuális hálózaton belül. A [VNet-szolgáltatás virtuálishálózati tűzfalszabályokkal rendelkező végpontjai](sql-database-vnet-service-endpoint-rule-overview.md) val is korlátozhatja az SQL Database-kiszolgálókhoz való hozzáférést.
  - A mobilfelhasználóknak pont-hely VPN-kapcsolatokat kell használniuk az adatelérési úton való csatlakozáshoz.
  - A helyszíni hálózatukhoz csatlakozó felhasználóknak a helyek közötti VPN-kapcsolatot vagy az ExpressRoute-ot kell használniuk az adatelérési úton való csatlakozáshoz.

- Az Azure SQL Database-t egy nyilvános végponthoz való csatlakozással érheti el (például egy nyilvános adatelérési út használatával). A következő bevált gyakorlatokat kell figyelembe venni: 
  - SQL Database-kiszolgáló esetén [IP-tűzfalszabályok](sql-database-firewall-configure.md) használatával korlátozhatja a hozzáférést csak az engedélyezett IP-címekre.
  - Felügyelt példány esetén a Hálózati biztonsági csoportok (NSG) segítségével a hozzáférést a 3342-es porton keresztül csak a szükséges erőforrásokra korlátozhatja. További információ: [Azure SQL Database felügyelt példány biztonságos használata nyilvános végpontokkal.](sql-database-managed-instance-public-endpoint-securely.md) 

> [!NOTE]
> A felügyelt példány nyilvános végpontja alapértelmezés szerint nincs engedélyezve, és explicit módon engedélyezni kell. Ha a vállalati szabályzat nem engedélyezi a nyilvános végpontok használatát, használja az [Azure Policy használatával](../governance/policy/overview.md) a nyilvános végpontok engedélyezése az első helyen.

- Az Azure Networking-összetevők beállítása: 
  - Kövesse [az Azure hálózati biztonsággal kapcsolatos gyakorlati tanácsait.](../security/fundamentals/network-best-practices.md)
  - Tervezze meg a virtuális hálózat (VNet) konfigurációját az Azure Virtual Network által ismertetett gyakorlati tanácsok nak [megfelelően, gyakori kérdések ( GYIK)](../virtual-network/virtual-networks-faq.md) és a tervezés. 
  - A virtuális hálózat ot több alhálózatra szegmentálhatja, és erőforrásokat rendelhet ugyanahhoz az alhálózathoz (például előtér- és háttér-erőforrásokhoz).
  - [A hálózati biztonsági csoportok (NSG-k)](../virtual-network/security-overview.md) segítségével az Azure virtuális hálózat határán belüli alhálózatok közötti forgalom szabályozásához.
  - Engedélyezze [az Azure Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) előfizetéséhez a bejövő és kimenő hálózati forgalom figyeléséhez.

### <a name="configure-power-bi-for-secure-connections-to-azure-sql-database"></a>A Power BI konfigurálása az Azure SQL Database biztonságos kapcsolataihoz

**Bevált gyakorlatok:**

- A Power BI Desktop esetében lehetőség szerint használja a személyes adatelérési utat. 

- Győződjön meg arról, hogy a Power BI Desktop a TLS1.2 használatával csatlakozik, ha a beállításkulcsot az ügyfélgépen a [Transport Layer Security (TLS)](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) beállításjegyzék-beállításainak megfelelően állítja be. 

- Korlátozza az adatok hozvalót az egyes felhasználók számára [a Power BI-val való sorszintű biztonság (RLS)](https://docs.microsoft.com/power-bi/service-admin-rls)segítségével. 

- A Power BI-szolgáltatás esetében használja a [helyszíni adatátjárót,](https://docs.microsoft.com/power-bi/service-gateway-onprem)szem előtt tartva [a korlátozásokat és a szempontokat.](https://docs.microsoft.com/power-bi/service-gateway-deployment-guidance#installation-considerations-for-the-on-premises-data-gateway)

### <a name="configure-app-service-for-secure-connections-to-azure-sql-database"></a>Az App Service konfigurálása az Azure SQL Database biztonságos kapcsolataihoz

**Bevált gyakorlatok:**

- Egy egyszerű webalkalmazás esetén a nyilvános végponton való csatlakozáshoz be kell adni **az Azure Services bekapcsolása beállítást.** 

- [Integrálja az alkalmazást egy Azure virtuális hálózattal](../app-service/web-sites-integrate-with-vnet.md) a felügyelt példányhoz való személyes adatelérési út kapcsolatához. Szükség esetén telepíthet egy Web App [Service-környezettel (ASE)](../app-service/environment/intro.md)is. 

- Az SQL Database-kiszolgáló adatbázisához csatlakozó ASE vagy VNet Integrated Web App alkalmazás esetén [a VNet-szolgáltatás végpontjai és a Virtuálishálózati tűzfalszabályok](sql-database-vnet-service-endpoint-rule-overview.md) használatával korlátozhatja a hozzáférést egy adott virtuális hálózatról és alhálózatról. Ezután állítsa az **Azure-szolgáltatások engedélyezése** beállítását KI-ra. Az ASE-t egy felügyelt példányhoz is csatlakoztathatja egy személyes adatelérési úton keresztül.  

- Győződjön meg arról, hogy a Web App konfigurálva van a cikk, [Gyakorlati tanácsok a PaaS webes és mobilalkalmazások az Azure App Service használatával.](../security/security-paas-applications-using-app-services.md) 

- Telepítse [a Web Application Firewall (WAF) alkalmazást,](../application-gateway/waf-overview.md) hogy megvédje a webalkalmazást a gyakori biztonsági résektől és biztonsági résektől.

### <a name="configure-azure-vm-hosting-for-secure-connections-to-azure-sql-database"></a>Az Azure VM-üzemeltetési szolgáltatás konfigurálása az Azure SQL Database-hez való biztonságos kapcsolatokhoz

**Bevált gyakorlatok:**

- Az Azure-beli virtuális gépek NSG-ire vonatkozó engedélyezési és megtagadási szabályok kombinációjával szabályozhatja, hogy mely régiók érhetők el a virtuális gépről.

- Győződjön meg arról, hogy a virtuális gép konfigurálva van a cikk szerint, [az Azure-beli IaaS-számítási feladatok ajánlott eljárások.](../security/azure-security-iaas.md)

- Győződjön meg arról, hogy minden virtuális gép egy adott virtuális hálózathoz és alhálózathoz van társítva. 

- Értékelje ki, hogy az alapértelmezett 0.0.0.0/Internet útvonalra van-e szüksége a [kényszerített bújtatásról szóló](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md#about-forced-tunneling)útmutatásszerint. 
  - Ha igen – például előtér-alhálózat –, akkor tartsa meg az alapértelmezett útvonalat.
  - Ha nincs – például középső réteg vagy háttér-alhálózat –, akkor engedélyezze a kényszerbújtatást, így az interneten keresztül nem érhető el a forgalom a helyszíni (más néven több helyiséget is). 

- Valósítsa meg [a választható alapértelmezett útvonalakat,](../virtual-network/virtual-networks-udr-overview.md#optional-default-routes) ha társviszony-létesítést vagy helyszíni csatlakozást használ. 

- [Felhasználói definiált útvonalak megvalósítása,](../virtual-network/virtual-networks-udr-overview.md#user-defined) ha a virtuális hálózat összes forgalmát egy hálózati virtuális berendezésbe kell küldenie csomagvizsgálatra. 

- [VNet-szolgáltatás végpontok](sql-database-vnet-service-endpoint-rule-overview.md) használata a PaaS-szolgáltatások, például az Azure Storage az Azure gerinchálózaton keresztül biztonságos hozzáférést biztosít. 

### <a name="protect-against-distributed-denial-of-service-ddos-attacks"></a>Az elosztott szolgáltatásmegtagadási (DDoS) támadások elleni védelem
Az elosztott szolgáltatásmegtagadási (DDoS) támadások egy rosszindulatú felhasználó kísérletei arra, hogy hálózati forgalmat küldjenek az Azure SQL Database-be azzal a céllal, hogy elnyomják az Azure-infrastruktúrát, és elutasítsák az érvényes bejelentkezéseket és munkaterhelést.

> Említett: OSA gyakorlat #9

**Hogyan kell végrehajtani:**

A DDoS-védelem automatikusan engedélyezve van az Azure Platform részeként. Ez magában foglalja a mindig bekapcsolt forgalomfigyelést és a nyilvános végpontok elleni hálózati szintű támadások valós idejű enyhítését. 

- Az [Azure DDoS Protection](../virtual-network/ddos-protection-overview.md) használatával figyelheti a virtuális hálózatokban üzembe helyezett erőforrásokhoz társított nyilvános IP-címeket.

- Az [Azure SQL Database komplex veszélyforrások elleni védelem használatával](sql-database-threat-detection-overview.md) észlelheti a szolgáltatásmegtagadási (DoS) adatbázisok elleni támadásokat.

**Bevált gyakorlatok:**

- Kövesse a [Támadási felület kis méretűre állításában](#minimize-attack-surface) leírt gyakorlatokat, amelyek segítenek minimalizálni a DDoS-támadási fenyegetéseket. 

- A komplex fenyegetésvédelem **brute force SQL hitelesítő adatok** riasztás segít észlelni a találgatásos támadások. Bizonyos esetekben a riasztás még megkülönböztetheti a behatolástesztelési számítási feladatokat is. 

- AZ SQL-adatbázishoz csatlakozó Azure VM-üzemeltetési alkalmazások esetén: 
  - Kövesse a hozzáférést az Azure Security Center internetfelé irányuló végpontokon keresztüli korlátozása javaslatának követésére. 
  - A virtuálisgép-méretezési készletek használatával az alkalmazás több példányát futtathatja az Azure virtuális gépeken. 
  - Tiltsa le az RDP-t és az SSH-t az internetről a találgatásos támadás megelőzése érdekében. 

## <a name="monitoring-logging-and-auditing"></a>Figyelés, naplózás és naplózás  
Ez a szakasz olyan képességekre vonatkozik, amelyek segítenek észlelni az adatbázisok elérésére vagy kihasználására irányuló szokatlan és potenciálisan káros kísérleteket jelző rendellenes tevékenységeket. Azt is ismerteti, gyakorlati eljárások konfigurálása adatbázis naplózása nyomon követésére és rögzítésére adatbázis-események.

### <a name="protect-databases-against-attacks"></a>Adatbázisok védelme a támadásokkal szemben 
A komplex veszélyforrások elleni védelem lehetővé teszi a potenciális fenyegetések észlelését és az azokra való reagálást, amikor azok a rendellenes tevékenységekre vonatkozó biztonsági riasztásokat biztosítanak.

**Hogyan kell végrehajtani:**

- [Az SQL komplex veszélyforrások elleni védelem használatával](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts) észlelheti az adatbázisok elérésére és kihasználására irányuló szokatlan és potenciálisan káros kísérleteket, többek között a következőket:
  - SQL injekciós támadás.
  - Hitelesítő adatok lopás / szivárgás.
  - Kiváltság visszaélés.
  - Adatszivárgás.

**Bevált gyakorlatok:**

- [Konfigurálja a speciális adatbiztonságot (ADS)](sql-database-advanced-data-security.md#getting-started-with-ads) az Azure SQL Database számára egy adott SQL Database-kiszolgálóhoz vagy egy felügyelt példányhoz. Az Ads szolgáltatást az előfizetésben lévő összes SQL Database-kiszolgálóhoz és felügyelt példányhoz is konfigurálhatja, ha átvált [az Azure Security Center standard szintre.](../security-center/security-center-pricing.md) 

- A teljes vizsgálati élmény érdekében ajánlott engedélyezni az [SQL Database Auditing programot.](sql-database-auditing.md) A naplózással nyomon követheti az adatbázis-eseményeket, és naplóba írhatja őket egy Azure Storage-fiókban vagy az Azure Log Analytics-munkaterületen. 

### <a name="audit-critical-security-events"></a>Kritikus biztonsági események naplózása
Az adatbázis-események nyomon követése segít megérteni az adatbázis-tevékenységeket. Betekintést nyerhet az olyan eltérésekbe és anomáliákba, amelyek üzleti problémákra vagy feltételezett biztonsági jogsértésekre utalhatnak. Emellett lehetővé teszi és megkönnyíti a megfelelőségi szabványok betartását. 

**Hogyan kell végrehajtani:**

- Engedélyezze [az SQL Database Auditing segítségével](sql-database-auditing.md) nyomon követheti az adatbázis-eseményeket, és naplóba írhatja őket az Azure Storage-fiókban, a Log Analytics-munkaterületben (előzetes verzióban) vagy az Event Hubs -ban (előzetes verzió). 

- Naplónaplók írhatók egy Azure Storage-fiók, egy Log Analytics-munkaterület az Azure Monitor naplók, vagy az eseményközpont az eseményközpont használatával. Ezek a beállítások tetszőleges kombinációját konfigurálhatja, és mindegyikbe naplót írnak. 

**Bevált gyakorlatok:**

- Ha az adatbázis-kiszolgálón konfigurálja az [SQL Database Auditing rendszert](sql-database-auditing.md) az események naplózására, a rendszer az adott kiszolgálón lévő összes meglévő és újonnan létrehozott adatbázist naplózza.
- Alapértelmezés szerint a naplózási házirend tartalmazza az adatbázisokon végrehajtott összes műveletet (lekérdezéseket, tárolt eljárásokat és sikeres bejelentkezéseket), ami nagy mennyiségű naplózási naplót eredményezhet. A [PowerShell használatával a különböző típusú műveletek és műveletcsoportok naplózásának konfigurálása](sql-database-auditing.md#manage-auditing)ajánlott. Ennek konfigurálása segít a naplózott műveletek számának szabályozásában, és minimálisra csökkenti az eseményvesztés kockázatát. Az egyéni naplózási konfiguráció lehetővé teszi az ügyfelek számára, hogy csak a szükséges naplózási adatokat rögzítsék.
- A naplónaplók közvetlenül az [Azure Portalon](https://portal.azure.com/)vagy a konfigurált tárolási helyről használhatók fel. 


> [!NOTE]
> A Log Analytics naplózásának engedélyezése a betöltési díjakon alapuló költségeket von maga után. Kérjük, vegye figyelembe a társított költségek et ezzel a [beállítással,](https://azure.microsoft.com/pricing/details/monitor/)vagy fontolja meg a naplónaplók tárolása egy Azure-tárfiókban. 

**További források**:

- [SQL-adatbázis naplózása](sql-database-auditing.md)
- [SQL Server naplózása](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) 

### <a name="secure-audit-logs"></a>Biztonságos naplónaplók
Korlátozza a hozzáférést a tárfiókhoz, hogy támogassa a feladatok elkülönítését és a DBA-t a könyvvizsgálóktól. 

**Hogyan kell végrehajtani:**

- Ha naplónaplókat ment az Azure Storage-ba, győződjön meg arról, hogy a storage-fiókhoz való hozzáférés a minimális biztonsági alapelvekre korlátozódik. Annak szabályozása, hogy ki férhet hozzá a tárfiókhoz.
    - További információ: [Hozzáférés engedélyezése az Azure Storage-hoz.](../storage/common/storage-auth.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

**Bevált gyakorlatok:**

- Az ellenőrzési célhoz való hozzáférés ellenőrzése kulcsfontosságú fogalom a DBA és a könyvvizsgálók elválasztásában. 

- A bizalmas adatokhoz való hozzáférés naplózásakor fontolja meg az adatok titkosítással való biztonságossá tétele, hogy elkerülje az adatok nak az auditorba történő szivárgását. További információt a [Bizalmas adatok védelme a magas jogosultsági szintű, jogosulatlan felhasználókkal szemben című szakaszban talál.](#protect-sensitive-data-in-use-from-high-privileged-unauthorized-users)

## <a name="security-management"></a>Biztonságkezelés

Ez a szakasz ismerteti a különböző szempontokat és gyakorlati tanácsok az adatbázisok biztonsági állapotának kezelésére. Ez magában foglalja az ajánlott eljárásokat annak biztosítására, hogy az adatbázisok úgy vannak konfigurálva, hogy megfeleljenek a biztonsági szabványoknak, az adatbázisokpotenciálisan bizalmas adataihoz való hozzáférés felderítéséhez, osztályozásához és nyomon követéséhez. 

### <a name="ensure-that-the-databases-are-configured-to-meet-security-best-practices"></a>Annak ellenőrzése, hogy az adatbázis(ok) úgy vannak-e konfigurálva, hogy megfeleljenek a biztonsági gyakorlati tanácsoknak 

Proaktív módon javíthatja az adatbázis biztonságát az adatbázis esetleges biztonsági rései felderítésével és elhárításával.

**Hogyan kell végrehajtani:**

- Engedélyezze [az SQL biztonsági résfelmérést](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment) (VA) az adatbázis biztonsági problémáinak vizsgálatához, és rendszeres időközönként automatikusan futtassa az adatbázisokat.

**Bevált gyakorlatok:**

- Kezdetben futtassa a VA-t az adatbázisokon, és a biztonsági gyakorlati tanácsokkal ellentétes sikertelen ellenőrzések elhárításával. Állítsa be az alapkonfigurációkat az elfogadható konfigurációkhoz, amíg a vizsgálat nem jön ki _tiszta,_ vagy az összes ellenőrzés lenem telt.  

- Állítsa be az ismétlődő rendszeres vizsgálatokat, hogy hetente egyszer fussanak, és állítsa be az érintett személyt az összefoglaló e-mailek fogadására. 

- Tekintse át a VA összefoglalót minden heti vizsgálat után. A talált biztonsági rések esetében értékelje ki az előző vizsgálat eredményétől való eltolódást, és állapítsa meg, hogy az ellenőrzést meg kell-e oldani. Tekintse át, hogy van-e jogos oka a konfigurációváltozásnak.   

- Adott esetben az ellenőrzések feloldása és az alaptervek frissítése. Hozzon létre jegyelemeket a műveletek feloldásához, és kövesse nyomon ezeket, amíg fel nem oldódnak. 

**További források**:

- [SQL-sebezhetőségi felmérés](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment) 
- [Az SQL biztonsági résfelmérési szolgáltatása segít az adatbázis-biztonsági rések azonosításában](sql-vulnerability-assessment.md)

### <a name="identify-and-tag-sensitive-data"></a>Bizalmas adatok azonosítása és címkézése 

Fedezze fel a bizalmas adatokat potenciálisan tartalmazó oszlopokat. Az, hogy mi minősül bizalmas adatnak, nagymértékben függ az ügyféltől, a megfelelőségi szabályozástól stb., és az adatokért felelős felhasználóknak kell értékelniük. Osztályozza az oszlopokat a speciális érzékenységalapú naplózási és védelmi forgatókönyvek használatához. 

**Hogyan kell végrehajtani:**

- Az [SQL Data Discovery and Classification](sql-database-data-discovery-and-classification.md) használatával felderítheti, osztályozhatja, címkézheti és védheti az adatbázisokban lévő bizalmas adatokat. 
  - Tekintse meg az automatikus felderítés által létrehozott besorolási javaslatokat az SQL Data Discovery és classification irányítópulton. Fogadja el a megfelelő besorolásokat, hogy a bizalmas adatok folyamatosan címkézve besorolási címkék. 
  - Manuálisan adja hozzá a besorolásokat minden olyan további bizalmas adatmezőhöz, amelyet az automatikus mechanizmus nem fedezett fel. 
- További információt az [SQL Data Discovery & Besorolás című témakörben talál.](https://docs.microsoft.com/sql/relational-databases/security/sql-data-discovery-and-classification)

**Bevált gyakorlatok:**

- Rendszeresen figyelje a besorolási irányítópultot az adatbázis besorolási állapotának pontos értékeléséhez. Az adatbázis besorolási állapotáról szóló jelentés exportálható vagy nyomtatható, hogy megfelelőségi és naplózási célokra megosztásra kerül.A report on the database classification state can be exported or printed to share for compliance and auditing purposes.

- Az SQL biztonsági rés értékelésében folyamatosan figyelje az ajánlott bizalmas adatok állapotát. Kövesse nyomon a bizalmas adatfelderítési szabályt, és azonosítsa az esetleges eltolódást az ajánlott oszlopokban a besoroláshoz.  

- A besorolást a szervezet egyedi igényeihez igazított módon használhatja. Az Azure Security Center [SQL Information Protection](../security-center/security-center-info-protection-policy.md) házirendjében testreszabhatja az információvédelmi szabályzatot (érzékenységi címkéket, adattípusokat, felderítési logikát). 

### <a name="track-access-to-sensitive-data"></a>Érzékeny adatokhoz való hozzáférés nyomon követése 
Figyelheti, hogy ki fér hozzá a bizalmas adatokhoz, és a lekérdezési naplókban rögzíti a bizalmas adatok lekérdezéseit.

**Hogyan kell végrehajtani:**

- Az SQL Audit és az adatbesorolás használata együttesen. 
  - Az [SQL-adatbázis naplózási](sql-database-auditing.md) naplójában nyomon követheti a hozzáférést kifejezetten a bizalmas adatokhoz. Megtekintheti az olyan információkat is, mint például az elért adatok, valamint azok érzékenységi címkéje. További információ: [Data Discovery & Classification](sql-database-data-discovery-and-classification.md) and [Auditing access to sensitive data](sql-database-data-discovery-and-classification.md#audit-sensitive-data). 

**Bevált gyakorlatok:**

- Tekintse meg a naplózási és adatbesorolási szakaszok ajánlott gyakorlatait: 
  - [Kritikus biztonsági események naplózása](#audit-critical-security-events) 
  - [Bizalmas adatok azonosítása és címkézése](#identify-and-tag-sensitive-data) 

### <a name="visualize-security-and-compliance-status"></a>A biztonsági és megfelelőségi állapot megjelenítése 

Használjon egységes infrastruktúra-biztonsági felügyeleti rendszert, amely erősíti az adatközpontok (beleértve az SQL-adatbázisokat) biztonsági állapotát. Az adatbázisok biztonságával és megfelelőségi állapotával kapcsolatos javaslatok listájának megtekintése.

**Hogyan kell végrehajtani:**

- Az SQL-rel kapcsolatos biztonsági javaslatok és az aktív fenyegetések figyelése az [Azure Security Centerben.](https://azure.microsoft.com/documentation/services/security-center/)

## <a name="common-security-threats-and-potential-mitigations"></a>Közös biztonsági fenyegetések és lehetséges mérséklések

Ez a szakasz segítséget nyújt bizonyos támadási felületek elleni védelemre vonatkozó biztonsági intézkedések megkereséséhez. A legtöbb további megoldás várhatóan a fenti biztonsági irányelvek egy vagy több követésével érhető el.

### <a name="security-threat-data-exfiltration"></a>Biztonsági fenyegetés: Adatok kiszivárgása

Az adatok kiszivárgása az adatok jogosulatlan másolása, átvitele vagy lekérése a számítógépről vagy kiszolgálóról. Lásd az [adatok kiszivárgásának](https://en.wikipedia.org/wiki/Data_exfiltration) definícióját a Wikipédián.

Az Azure SQL Database-kiszolgálóhoz nyilvános végponton keresztül való csatlakozás adatkiszivárgási kockázatot jelent, mivel az ügyfeleknek meg kell nyitniuk a tűzfalakat a nyilvános IP-k számára.  

**1. forgatókönyv:** Egy Azure-beli virtuális gép egy alkalmazás csatlakozik egy azure SQL Database-kiszolgáló adatbázisához. Egy szélhámos szereplő hozzáférést kap a virtuális géphez, és kompromittálja azt. Ebben a forgatókönyvben az adatok kiszivárgása azt jelenti, hogy egy külső entitás az engedélyezetlen virtuális gépet használó csatlakozik az adatbázishoz, másolja a személyes adatokat, és tárolja azt egy blob storage vagy egy másik SQL-adatbázis egy másik előfizetésben.

**2. forgatókönyv:** A Rouge DBA. Ezt a forgatókönyvet gyakran a szabályozott iparágak biztonsági szempontból érzékeny ügyfelei vetik fel. Ebben a forgatókönyvben egy magas jogosultságú felhasználó adatokat másolhat az Azure SQL Database-ből egy másik előfizetésbe, amelyet nem az adattulajdonos vezérel.

**Lehetséges mérséklések:**

Az Azure SQL Database ma a következő technikákat kínálja az adatkiszivárgási fenyegetések csökkentésére: 

- Az Azure-beli virtuális gépek NSG-ire vonatkozó engedélyezési és megtagadási szabályok kombinációjával szabályozhatja, hogy mely régiók érhetők el a virtuális gépről. 
- Ha egy Azure SQL Database-kiszolgálót használ (amely egyedi adatbázisokat vagy rugalmas készleteket tartalmaz), adja meg a következő beállításokat:
  - Az Azure Services kikapcsolásának engedélyezése.
  - Csak az Azure virtuális gép ét tartalmazó alhálózatról való forgalmat engedélyezze egy virtuális hálózati tűzfalszabály beállításával.
  - [Privát hivatkozás](sql-database-private-endpoint-overview.md) használata
- Felügyelt példány használata, alapértelmezés szerint a privát IP-hozzáférés használatával az első adatok kiszivárgása aggodalomra ad okot egy engedélyezetlen virtuális gép. Kapcsolja be az alhálózat delegálási szolgáltatását, hogy automatikusan beállítsa a legszigorúbb házirendet egy felügyelt példány alhálózatán.
- A Rogue DBA probléma jobban ki van téve a felügyelt példány, mivel van egy nagyobb felület és a hálózati követelmények láthatók az ügyfelek számára. A legjobb megoldás erre az ebben a biztonsági útmutatóban szereplő összes gyakorlat alkalmazása a Rogue DBA-forgatókönyv megelőzéséhez (nem csak az adatok kiszivárgása esetén). Mindig titkosított az egyik módszer, hogy megvédje a bizalmas adatok titkosításával, és így a kulcs nem érhető el a DBA.

## <a name="security-aspects-of-business-continuity-and-availability"></a>Az üzletmenet folytonosságának és elérhetőségének biztonsági vonatkozásai

A legtöbb biztonsági szabvány az adatok rendelkezésre állását a működési folytonosság szempontjából kezeli, amelyet redundancia- és feladatátvételi képességek megvalósításával érnek el az egyes meghibásodási pontok elkerülése érdekében. A katasztrófa-forgatókönyvek, ez egy általános gyakorlat, hogy az adatok és a naplófájlok biztonsági mentések.A következő szakasz magas szintű áttekintést nyújt az Azure-ba beépített képességekről. További lehetőségeket is kínál, amelyek az egyedi igények kielégítésére konfigurálhatók: 

- Az Azure beépített, magas rendelkezésre állást kínál: [magas rendelkezésre állás és Azure SQL Database](sql-database-high-availability.md) 

- Az üzleti legkritikusabb réteg tartalmazza a feladatátvételi csoportokat, a több rendelkezésre állású zónákat, a teljes és különbözeti naplóbiztonsági mentéseket, valamint az alapértelmezés szerint engedélyezett időpont-időpont-visszaállítási biztonsági mentéseket:  
  - [Magas rendelkezésre állás és Az Azure SQL Database – Zónaredundáns konfiguráció](sql-database-high-availability.md#zone-redundant-configuration)
  - [Automatikus biztonsági mentések](sql-database-automated-backups.md)
  - [Az Azure SQL-adatbázis helyreállítása automatikus adatbázis-biztonsági mentésekkel – Időponthoz szedve visszaállítás](sql-database-recovery-using-backups.md#point-in-time-restore)

- További üzletmenet-folytonossági funkciók, például a különböző Azure-földrajzi geos-ok közötti automatikus feladatátvételi csoportok konfigurálhatók az itt leírtak szerint: [Az üzletmenet folytonosságának áttekintése az Azure SQL Database-lel](sql-database-business-continuity.md)

## <a name="next-steps"></a>További lépések

- [Lásd: Az Azure SQL Database biztonsági képességeinek áttekintése](sql-database-security-overview.md)
