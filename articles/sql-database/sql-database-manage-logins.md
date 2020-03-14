---
title: Bejelentkezések és felhasználók
description: Ismerje meg, hogy a Azure SQL Database és az Azure szinapszis Analytics Hogyan hitelesíti a felhasználókat a bejelentkezések és a felhasználói fiókok használatával, valamint szerepköröket és explicit engedélyeket használ a bejelentkezések és a felhasználók számára, hogy az adatbázisokon, valamint a kiszolgálói szinten hajtsák végre a műveleteket.
keywords: sql database biztonság,adatbázis biztonságának felügyelete,bejelentkezési biztonság,adatbázis biztonsága,adatbázis-hozzáférés
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 03/12/2020
ms.openlocfilehash: 7c70d5dd19ec0495fe09152b5653363ad369347c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79268911"
---
# <a name="granting-database-access-and-authorization-to-sql-database-and-azure-synapse-analytics-using-logins-and-user-accounts"></a>Adatbázis-hozzáférés és-engedélyezés biztosítása a SQL Database és az Azure szinapszis Analytics számára a bejelentkezések és a felhasználói fiókok használatával

A Azure SQL Database és az Azure szinapszis Analytics (korábbi nevén Azure SQL Data Warehouse) adatbázisaihoz való hitelesített hozzáférést a rendszer a bejelentkezések és a felhasználói fiókok használatával felügyeli. A [**hitelesítés**](sql-database-security-overview.md#authentication) az a folyamat, amellyel a felhasználó igazolni kívánja.

- A bejelentkezési azonosító a Master adatbázisban lévő egyedi fiók
- A felhasználói fiók bármely adatbázisban egyedi fiók, és nem kell bejelentkezési azonosítóhoz társítania

> [!IMPORTANT]
> A Azure SQL Database-ben és az Azure szinapszis Analyticsben (korábban Azure SQL Data Warehouse) lévő adatbázisokat a cikk további részében Azure SQL Database (az egyszerűség kedvéért) együttesen kell megtekinteni.

Az adatbázis felhasználója felhasználói fiókkal csatlakozik egy Azure SQL Database-adatbázishoz, és az alábbi két módszer egyikével van hitelesítve:

- [SQL-hitelesítés](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication), amely egy bejelentkezési nevet vagy egy felhasználói fiók nevét, valamint a Azure SQL Database tárolt jelszót tartalmazza.
- [Azure Active Directory hitelesítés](sql-database-aad-authentication.md), amely a Azure Active Directory tárolt bejelentkezési hitelesítő adatokat használja

Az adatokhoz való hozzáféréshez és az Azure SQL Database-ben végzett különféle műveletek végrehajtásához az adatbázis-szerepkörök és a explicit engedélyek használatával kezelhetők az engedélyek. Az [**Engedélyezés**](sql-database-security-overview.md#authorization) egy Azure SQL Databaseon belüli felhasználóhoz rendelt engedélyekre utal, és meghatározza, hogy a felhasználó mit tehet. Az engedélyezést a felhasználói fiók adatbázis- [szerepköri tagsága](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles) és az [objektum szintű engedélyek](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine)vezérlik. Ajánlott eljárásként csak a minimálisan szükséges engedélyeket adja meg a felhasználóknak.

Ebben a cikkben a következőkkel ismerkedhet meg:

- A hozzáférési és engedélyezési konfiguráció a kezdeti új Azure SQL Database létrehozása után
- Bejelentkezések és felhasználói fiókok hozzáadása a főadatbázisban és a felhasználói fiókokban, majd a fiókok rendszergazdai engedélyeinek megadása
- Felhasználói fiókok hozzáadása felhasználói adatbázisokhoz a bejelentkezésekhez vagy a benne foglalt felhasználói fiókokhoz társítva
- Felhasználói fiókok konfigurálása felhasználói adatbázisokban lévő engedélyekkel adatbázis-szerepkörök és explicit engedélyek használatával

## <a name="existing-logins-and-user-accounts-after-creating-a-new-database"></a>Meglévő bejelentkezések és felhasználói fiókok új adatbázis létrehozása után

Az első Azure SQL Database központi telepítés létrehozásakor meg kell adnia egy rendszergazdai bejelentkezési azonosítót és egy hozzá tartozó jelszót a bejelentkezéshez. A rendszergazdai fiók neve **kiszolgáló-rendszergazda**. Az üzembe helyezés során a rendszer a következő konfigurációkat és felhasználókat a fő és a felhasználói adatbázisokban is megtörténik:

- A rendszergazdai jogosultságokkal rendelkező SQL-bejelentkezés a megadott bejelentkezési névvel hozható létre. A [Bejelentkezés](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine#sa-login) egy egyéni felhasználói fiók, amellyel bejelentkezhet a SQL Databaseba.
- Ez a bejelentkezési azonosító teljes rendszergazdai jogosultságokkal rendelkezik minden adatbázishoz [kiszolgálói szintű rendszerbiztonsági tagként](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine). Ez a bejelentkezés SQL Databaseon belül minden rendelkezésre álló engedéllyel rendelkezik, és nem korlátozható. Felügyelt példányokban ez a bejelentkezés a [sysadmin (rendszergazda) rögzített kiszolgálói szerepkörbe](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles) kerül (ez a szerepkör nem létezik egyetlen vagy készletezett adatbázissal).
- Ehhez a bejelentkezéshez létrejön egy `dbo` nevű [felhasználói fiók](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions#database-users) az egyes felhasználói adatbázisokban. A [dbo](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine) -felhasználó rendelkezik az adatbázis összes engedélyével, és a `db_owner` rögzített adatbázis-szerepkörre van leképezve. A cikk későbbi részében további rögzített adatbázis-szerepköröket is ismertetünk.

Az SQL Serverhez tartozó rendszergazdai fiókok azonosításához nyissa meg a Azure Portal, és keresse meg az SQL Server vagy SQL Database **Tulajdonságok** lapját.

![SQL Server-rendszergazdák](media/sql-database-manage-logins/sql-admins.png)

> [!IMPORTANT]
> A rendszergazdai bejelentkezési név nem módosítható a létrehozása után. A logikai kiszolgáló rendszergazdájának jelszavának alaphelyzetbe állításához lépjen a [Azure Portal](https://portal.azure.com), kattintson az **SQL-kiszolgálók**elemre, válassza ki a kiszolgálót a listából, majd kattintson a **jelszó alaphelyzetbe állítása**elemre. A felügyelt példányok kiszolgálójának jelszavának alaphelyzetbe állításához lépjen a Azure Portalre, kattintson a példányra, majd a **jelszó alaphelyzetbe állítása**elemre. Használhatja a PowerShellt vagy az Azure CLI-t is.

## <a name="create-additional-logins-and-users-having-administrative-permissions"></a>Rendszergazdai jogosultságokkal rendelkező további bejelentkezések és felhasználók létrehozása

Ezen a ponton a SQL Database csak egyetlen SQL-bejelentkezési és felhasználói fiókkal való hozzáférésre van konfigurálva. Teljes vagy részleges rendszergazdai engedélyekkel rendelkező további bejelentkezések létrehozásához a következő beállítások érhetők el (a telepítési módtól függően):

- **Teljes rendszergazdai engedélyekkel rendelkező Azure Active Directory rendszergazdai fiók létrehozása**

  Engedélyezze Azure Active Directory hitelesítést, és hozzon létre egy Azure AD-rendszergazdai bejelentkezési azonosítót. Az egyik Azure Active Directory fiók konfigurálható a teljes rendszergazdai jogosultságokkal rendelkező SQL Database központi telepítés rendszergazdájaként. Ez a fiók lehet egy egyéni vagy egy biztonsági csoport fiók. Az Azure AD- **rendszergazdát úgy kell** konfigurálni, hogy az Azure ad-fiókok használatával kapcsolódjon SQL Databasehoz. Az Azure AD-hitelesítés összes SQL Database központi telepítési típusra való engedélyezésével kapcsolatos részletes információkért tekintse meg a következő cikkeket:

  - [Azure Active Directory hitelesítés használata SQL-alapú hitelesítéshez](sql-database-aad-authentication.md)
  - [Azure Active Directory hitelesítés konfigurálása és kezelése SQL-sel](sql-database-aad-authentication-configure.md)

- **Felügyelt példányok üzembe helyezése esetén hozzon létre teljes rendszergazdai engedélyekkel rendelkező SQL-bejelentkezéseket**

  - További SQL Server bejelentkezés létrehozása a felügyelt példányban
  - Adja hozzá a bejelentkezést a [sysadmin rögzített kiszolgálói szerepkörhöz](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles) az [Alter Server role](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql) utasítás használatával. Ez a bejelentkezés teljes rendszergazdai jogosultságokkal fog rendelkezni.
  - Másik lehetőségként hozzon létre egy [Azure ad-bejelentkezést](sql-database-aad-authentication-configure.md?tabs=azure-powershell#new-azure-ad-admin-functionality-for-mi) a <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">bejelentkezési szintaxis létrehozása</a> paranccsal.

- **Egyetlen vagy készletezett üzembe helyezés esetén korlátozott rendszergazdai engedélyekkel rendelkező SQL-bejelentkezések hozhatók létre**

  - További SQL-bejelentkezés létrehozása a Master adatbázisban egyetlen vagy készletezett adatbázis-telepítéshez vagy felügyelt példányok központi telepítéséhez
  - Hozzon létre egy felhasználói fiókot az új bejelentkezéshez társított Master adatbázisban
  - Adja hozzá a felhasználói fiókot a `dbmanager`hoz, a `loginmanager` szerepkörhöz vagy mindkettőhöz a `master` adatbázisban az [Alter Server role](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql) utasítás használatával (az Azure szinapszis Analytics esetében használja az [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) utasítást).

  > [!NOTE]
  > a `dbmanager` és `loginmanager` szerepkörök **nem** vonatkoznak a felügyelt példányok központi telepítésére.

  Ezeknek a [speciális főadatbázis-szerepköröknek](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles#special-roles-for--and-) az egyetlen vagy készletezett adatbázishoz való tagjai lehetővé teszik a felhasználók számára, hogy adatbázisok létrehozásához és kezeléséhez, illetve bejelentkezések létrehozásához és kezeléséhez rendelkezzenek. A `dbmanager` szerepkörbe tartozó felhasználó által létrehozott adatbázisokban a tag a `db_owner` rögzített adatbázis-szerepkörre van leképezve, és az `dbo` felhasználói fiók használatával tud bejelentkezni az adatbázisba, és felügyelheti azt. Ezek a szerepkörök nem rendelkeznek explicit engedélyekkel a Master adatbázison kívül.

  > [!IMPORTANT]
  > Egyetlen vagy készletezett adatbázisban nem hozhat létre további SQL-bejelentkezést teljes rendszergazdai engedélyekkel.

## <a name="create-accounts-for-non-administrator-users"></a>Fiókok létrehozása nem rendszergazda felhasználók számára

A nem rendszergazda felhasználók számára a következő két módszer egyikét hozhatja létre:

- **Bejelentkezési azonosító létrehozása**

  Hozzon létre egy SQL-bejelentkezést a Master adatbázisban. Ezután hozzon létre egy felhasználói fiókot minden adatbázisban, amelyhez a felhasználónak hozzáférésre van szüksége, és társítsa a felhasználói fiókot a bejelentkezéshez. Ez a megközelítés akkor javasolt, ha a felhasználónak több adatbázishoz is hozzá kell férnie, és meg szeretné tartani a jelszavak szinkronizálását. Azonban ez a megközelítés összetett, ha a földrajzi replikálást használja, mivel a bejelentkezést az elsődleges kiszolgálón és a másodlagos kiszolgálón is létre kell hozni. További információt a [geo-visszaállítási vagy feladatátvételi Azure SQL Database biztonságának konfigurálása és kezelése](sql-database-geo-replication-security-config.md)című témakörben talál.
- **Felhasználói fiók létrehozása**

  Hozzon létre egy felhasználói fiókot abban az adatbázisban, amelyhez a felhasználónak hozzáférésre van szüksége (más néven [tárolt felhasználó](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable)).

  - Egyetlen vagy készletezett adatbázis esetén mindig létrehozhat ilyen típusú felhasználói fiókot.
  - Ha olyan felügyelt példány-adatbázist használ, amely nem támogatja az [Azure ad-kiszolgáló](sql-database-aad-authentication-configure.md?tabs=azure-powershell#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)rendszertagjait, csak egy [tárolt adatbázisban](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases)hozhatja létre ezt a típusú felhasználói fiókot. Az [Azure ad Server-rendszerbiztonsági tagokat](sql-database-aad-authentication-configure.md?tabs=azure-powershell#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)támogató felügyelt példányok esetében felhasználói fiókokat hozhat létre a felügyelt példány hitelesítéséhez anélkül, hogy az adatbázis-felhasználók számára a tárolt adatbázis-felhasználóként lenne szükség.

  Ezzel a módszerrel a rendszer az egyes adatbázisokban tárolja a felhasználói hitelesítési adatokat, és automatikusan replikálja a Geo-replikált adatbázisokat. Ha azonban ugyanaz a fiók több adatbázisban is létezik, és SQL-hitelesítést használ, akkor manuálisan kell megtartania a jelszavak szinkronizálását. Emellett, ha egy felhasználó különböző jelszavakkal rendelkező fiókkal rendelkezik, akkor a jelszó megemlékezése problémát jelenthet.

> [!IMPORTANT]
> Az Azure AD-identitásokhoz hozzárendelt, foglalt felhasználók létrehozásához olyan Azure AD-fiókkal kell bejelentkeznie, amely a SQL Database rendszergazdája. Felügyelt példányban az `sysadmin` engedélyekkel rendelkező SQL-bejelentkezések Azure AD-bejelentkezést vagy-felhasználót is létrehozhatnak.

A bejelentkezések és a felhasználók létrehozásának módját bemutató Példákért lásd:

- [Egyszeri vagy készletezett adatbázisok bejelentkezésének létrehozása](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current#examples-1)
- [A felügyelt példány adatbázisához tartozó bejelentkezés létrehozása](https://docs.microsoft.com/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current#examples-2)
- [Bejelentkezés létrehozása az Azure szinapszis Analytics-adatbázishoz](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest#examples-3)
- [Felhasználó létrehozása](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql#examples)
- [Azure AD-beli felhasználók létrehozása](sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)

> [!TIP]
> Egy olyan biztonsági oktatóanyaghoz, amely magában foglalja a SQL Server egy tárolt felhasználó egyetlen vagy készletezett adatbázisban való létrehozását, lásd [: oktatóanyag: egyetlen vagy készletezett adatbázis biztonságossá tétele](sql-database-security-tutorial.md).

## <a name="using-fixed-and-custom-database-roles"></a>Rögzített és egyéni adatbázis-szerepkörök használata

Miután létrehozott egy felhasználói fiókot egy adatbázisban, vagy egy bejelentkezés vagy egy tárolt felhasználó alapján, engedélyezheti, hogy a felhasználó különböző műveleteket hajtson végre, és hozzáférhessen az adatokhoz egy adott adatbázisban. A hozzáférés engedélyezéséhez a következő módszereket használhatja:

- **Rögzített adatbázis-szerepkörök**

  Adja hozzá a felhasználói fiókot egy [rögzített adatbázis-szerepkörhöz](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles). 9 rögzített adatbázis-szerepkör van, amelyek mindegyike meghatározott engedélyekkel rendelkezik. A leggyakoribb rögzített adatbázis-szerepkörök a következők: **db_owner**, **db_ddladmin**, **db_datawriter**, **db_datareader**, **db_denydatawriter**és **db_denydatareader**. A **db_owner** általában teljes körű engedélyek biztosítására szolgál néhány felhasználó számára. A többi rögzített adatbázis-szerepkör hasznos az egyszerű adatbázisok fejlesztésének gyors elkezdéséhez, de a legtöbb éles környezetbeli adatbázishoz nem ajánlott. A **db_datareader** rögzített adatbázis-szerepkör például olvasási hozzáférést biztosít az adatbázisban található összes táblához, ami nagyobb, mint a szigorúan szükséges.

  - Felhasználó hozzáadása rögzített adatbázis-szerepkörhöz:

    - Azure SQL Database használja az [Alter role](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql) utasítást. Példák: a [szerepkör-példák módosítása](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql#examples)
    - Az Azure szinapszis Analytics a [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) utasítást használja. Példák: [sp_addrolemember példák](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql).

- **Egyéni adatbázis-szerepkör**

  Hozzon létre egy egyéni adatbázis-szerepkört a [create role](https://docs.microsoft.com/sql/t-sql/statements/create-role-transact-sql) utasítás használatával. Az egyéni szerepkör lehetővé teszi saját, felhasználó által definiált adatbázis-szerepkörök létrehozását, és az üzleti igényeknek leginkább megfelelő jogosultságok megadását az egyes szerepkörök számára. Ezután felhasználókat adhat hozzá az egyéni szerepkörhöz. Ha a felhasználó egyszerre több szerepkörnek is tagja, akkor a rendszer összesíti az engedélyeket.
- **Engedélyek közvetlen megadása**

  Közvetlenül adja meg a felhasználói fiók [engedélyeit](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) . Az SQL Database-ben több mint 100 engedély adható vagy tagadható meg külön-külön. Ezek közül számos engedély beágyazott. Egy sémában található `UPDATE` engedély például a séma minden táblájára vonatkozó `UPDATE` engedélyt tartalmazza. A legtöbb engedélyrendszerhez hasonlóan az engedély megtagadása felülírja a megadását. Az engedélyek beágyazott jellege és száma miatt lehetséges, hogy alapos tervezés szükséges az adatbázis megfelelő védelmét biztosító engedélyrendszer kialakításához. Kezdje az [Engedélyek (Adatbázismotor)](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) szakaszban felsorolt engedélyek listájával, majd tekintse át az engedélyek [poszterméretű ábráját](https://docs.microsoft.com/sql/relational-databases/security/media/database-engine-permissions.png).

## <a name="using-groups"></a>Csoportok használata

A hatékony hozzáférés-vezérlés Active Directory biztonsági csoportokhoz, illetve rögzített vagy egyéni szerepkörökhöz hozzárendelt engedélyeket használ az egyes felhasználók helyett.

- Azure Active Directory hitelesítés használatakor Azure Active Directory felhasználókat Azure Active Directory biztonsági csoportba helyezheti. Hozzon létre a csoportban egy adatbázis-felhasználót. Helyezzen egy vagy több adatbázis-felhasználót egy egyéni adatbázis-szerepkörbe az adott felhasználói csoportnak megfelelő engedélyekkel.

- SQL-hitelesítés használatakor hozzon létre tárolt adatbázis-felhasználókat az adatbázisban. Helyezzen egy vagy több adatbázis-felhasználót egy egyéni adatbázis-szerepkörbe az adott felhasználói csoportnak megfelelő engedélyekkel.

  > [!NOTE]
  > Csoportokat is használhat a nem tárolt adatbázis-felhasználók számára.

Érdemes megismerkednie az alábbi funkciókkal, amelyek az engedélyek korlátozására vagy a szint emelésére is használhatók:

- A [megszemélyesítés](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/customizing-permissions-with-impersonation-in-sql-server) és a [modulaláírás](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server) az engedélyek biztonságos átmeneti növelésére használható.
- A [sorszintű biztonság](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) használatával korlátozhatja, hogy a felhasználó mely sorokhoz férhessen hozzá.
- Az [adatmaszkolás](sql-database-dynamic-data-masking-get-started.md) használatával korlátozhatja a bizalmas adatok megjelenítését.
- A [tárolt eljárások](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) segítségével korlátozhatja az adatbázisban végezhető műveleteket.

## <a name="next-steps"></a>További lépések

Az SQL Database összes biztonsági szolgáltatásáról [az SQL biztonsági szolgáltatásainak áttekintése](sql-database-security-overview.md) biztosít áttekintést.
