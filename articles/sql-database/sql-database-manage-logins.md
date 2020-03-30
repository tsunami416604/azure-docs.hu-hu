---
title: Kiszolgáló- és adatbázis-hozzáférés engedélyezése bejelentkezési és felhasználói fiókok használatával
description: Ismerje meg, hogyan azure SQL Database és az Azure Synapse Analytics hitelesíti a felhasználók hozzáférés bejelentkezések és felhasználói fiókok használatával. Azt is megtudhatja, hogyan lehet adatbázis-szerepkörök és explicit engedélyeket engedélyezni bejelentkezések és a felhasználók műveletek végrehajtására és lekérdezési adatok.
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
ms.date: 03/23/2020
ms.openlocfilehash: 98c15fe11b64e8c177e60a2ea1eb7c50eaf69353
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124813"
---
# <a name="authorizing-database-access-to-authenticated-users-to-sql-database-and-azure-synapse-analytics-using-logins-and-user-accounts"></a>Adatbázis-hozzáférés engedélyezése a hitelesített felhasználók számára az SQL Database és az Azure Synapse Analytics számára bejelentkezések és felhasználói fiókok használatával

Ebben a cikkben a következőket ismerheti meg:

- Az Azure SQL Database és az Azure Synapse Analytics (korábbi években Azure SQL Data Warehouse) konfigurálásának lehetőségei lehetővé teszik a felhasználók számára a felügyeleti feladatok elvégzését és az adatbázisokban tárolt adatok elérését.
- A hozzáférési és engedélyezési konfiguráció az új Azure SQL-adatbázis létrehozása után
- Bejelentkezések és felhasználói fiókok hozzáadása a fő adatbázisba és a felhasználói fiókokba, majd a fiókok rendszergazdai engedélyeinek megadása
- Felhasználói fiókok hozzáadása a felhasználói adatbázisokhoz, bejelentkezésekhez vagy beépített felhasználói fiókokhoz társítva
- Felhasználói fiókok konfigurálása a felhasználói adatbázisokban engedélyekkel adatbázis-szerepkörök és explicit engedélyek használatával

> [!IMPORTANT]
> Adatbázisok az Azure SQL Database és az Azure Synapse Analytics (korábban Azure SQL Data Warehouse) a cikk fennmaradó részében, mint adatbázisok vagy az Azure SQL (az egyszerűség kedvéért).

## <a name="authentication-and-authorization"></a>Hitelesítés és engedélyezés

[**A hitelesítés**](sql-database-security-overview.md#authentication) azt a folyamatot, amely bizonyítja, hogy a felhasználó az, akinek állítja magát. A felhasználó egy felhasználói fiók használatával csatlakozik egy adatbázishoz.
Amikor egy felhasználó megpróbál csatlakozni egy adatbázishoz, felhasználói fiókot és hitelesítési adatokat ad meg. A felhasználó hitelesítése az alábbi két hitelesítési módszer egyikével történik:

- [SQL-hitelesítés](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication).

  Ezzel a hitelesítési módszerrel a felhasználó elküldi a felhasználói fiók nevét és a kapcsolódó jelszót a kapcsolat létrehozásához. Ez a jelszó a bejelentkezéshez kapcsolódó vagy a bejelentkezéshez nem kapcsolódó felhasználói fiókok felhasználói fiókját tartalmazó felhasználói fiókok fő adatbázisában tárolódik.
- [Azure Active Directory-hitelesítés](sql-database-aad-authentication.md)

  Ezzel a hitelesítési módszerrel a felhasználó elküldi a felhasználói fiók nevét, és kéri, hogy a szolgáltatás használja az Azure Active Directoryban tárolt hitelesítő adatokat.

**Bejelentkezések és felhasználók:** Az Azure SQL-ben az adatbázisfelhasználói fiókja társítható a fő adatbázisban tárolt bejelentkezéshez, vagy lehet egy adott adatbázisban tárolt felhasználónév.

- A **bejelentkezés** a fő adatbázisban lévő egyedi fiók, amelyhez egy vagy több adatbázis felhasználói fiókja csatolható. A bejelentkezéssel a felhasználói fiók hitelesítő adatait a bejelentkezéssel tárolják.
- A **felhasználói fiók** egy egyedi fiók bármely adatbázisban, amely lehet, de nem kell csatolni a bejelentkezést. Ha a felhasználói fiók nem kapcsolódik bejelentkezéshez, a hitelesítő adatok a felhasználói fiókkal együtt tárolódnak.

Az adatok elérésére és a különböző műveletek végrehajtására vonatkozó [**engedélyeket**](sql-database-security-overview.md#authorization) adatbázis-szerepkörök és explicit engedélyek segítségével kezelik. Az engedélyezés a felhasználóhoz rendelt engedélyekre vonatkozik, és meghatározza, hogy a felhasználó mire jogosult. Az engedélyezést a felhasználói fiók [adatbázis-szerepkör-tagságai](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles) és [objektumszintű engedélyei szabályozzák.](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) Ajánlott eljárásként csak a minimálisan szükséges engedélyeket adja meg a felhasználóknak.

## <a name="existing-logins-and-user-accounts-after-creating-a-new-database"></a>Meglévő bejelentkezések és felhasználói fiókok új adatbázis létrehozása után

Az első Azure SQL-telepítés létrehozásakor meg kell adnia egy rendszergazdai bejelentkezési és egy hozzá tartozó jelszót a bejelentkezéshez. Ezt a rendszergazdai fiókot **kiszolgáló-rendszergazdai**fióknak hívják. A fő- és felhasználói adatbázisokban lévő bejelentkezések és felhasználók következő konfigurációja történik a telepítés során:

- A megadott bejelentkezési névvel rendszergazdai jogosultságokkal rendelkező SQL-bejelentkezés jön létre. A [bejelentkezés](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine#sa-login) az SQL Database-be való bejelentkezéshez szükséges egyéni felhasználói fiókok.
- Ez a bejelentkezés teljes körű rendszergazdai engedélyt kap minden [adatbázisra, mint kiszolgálószintű egyszerű.](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine) Ez a bejelentkezés rendelkezik az SQL Database összes elérhető engedélyével, és nem korlátozható. Egy felügyelt példányban ez a bejelentkezés hozzáadódik a [sysadmin rögzített kiszolgálói szerepkörhöz](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles) (ez a szerepkör nem létezik egy- vagy készletezett adatbázisokkal).
- Minden [user account](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions#database-users) felhasználói `dbo` adatbázisban létrejön egy hívott felhasználói fiók ehhez a bejelentkezéshez. A [dbo-felhasználó](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine) rendelkezik az adatbázis összes adatbázis-engedélyével, és a `db_owner` rögzített adatbázisszerepkörhöz van rendelve. A cikk későbbi részében további rögzített adatbázis-szerepköröket tárgyalunk.

Az adatbázis rendszergazdai fiókjainak azonosításához nyissa meg az Azure Portalt, és keresse meg a kiszolgáló vagy a felügyelt példány **Tulajdonságok** lapját.

![SQL Server-rendszergazdák](media/sql-database-manage-logins/sql-admins.png)

![SQL Server-rendszergazdák](media/sql-database-manage-logins/sql-admins2.png)

> [!IMPORTANT]
> A rendszergazdai bejelentkezési név nem módosítható a létrehozása után. A logikai kiszolgáló rendszergazdájának jelszavának alaphelyzetbe állításához nyissa meg az [Azure portalt,](https://portal.azure.com)kattintson az **SQL-kiszolgálók**elemre, válassza ki a kiszolgálót a listából, majd kattintson a **Jelszó alaphelyzetbe állítása parancsra.** Egy felügyelt példánykiszolgáló jelszavának alaphelyzetbe állításához nyissa meg az Azure Portalon, kattintson a példányra, majd a **Jelszó alaphelyzetbe állítása parancsra.** A PowerShell vagy az Azure CLI is használható.

## <a name="create-additional-logins-and-users-having-administrative-permissions"></a>További bejelentkezések és rendszergazdai jogosultságokkal rendelkező felhasználók létrehozása

Ezen a ponton az Azure SQL-példány csak egyetlen SQL bejelentkezési és felhasználói fiók használatával van konfigurálva a hozzáféréshez. További bejelentkezések teljes vagy részleges rendszergazdai engedélyekkel történő létrehozásához a következő lehetőségek közül választhat (a telepítési módtól függően):

- **Teljes rendszergazdai engedélyekkel rendelkező Azure Active Directory-rendszergazdai fiók létrehozása**

  Engedélyezze az Azure Active Directory-hitelesítést, és hozzon létre egy Azure AD-rendszergazdai bejelentkezést. Egy Azure Active Directory-fiók konfigurálható az SQL Database központi telepítésének teljes rendszergazdai engedélyekkel rendelkező rendszergazdájaként. Ez a fiók lehet egyéni vagy biztonsági csoportfiók. Az Azure AD-rendszergazdát konfigurálni **kell,** ha Az Sql Database-hez való csatlakozáshoz Azure AD-fiókokat szeretne használni. Az Azure AD-hitelesítés engedélyezéséről az SQL Database összes telepítési típusára vonatkozó engedélyezéséről az alábbi cikkekben talál részletes információt:

  - [Az Azure Active Directory-hitelesítés használata az SQL-hitelesítéshez](sql-database-aad-authentication.md)
  - [Az Azure Active Directory-hitelesítés konfigurálása és kezelése SQL-el](sql-database-aad-authentication-configure.md)

- **Felügyelt példány központi telepítésében hozzon létre teljes rendszergazdai engedélyekkel rendelkező SQL-bejelentkezéseket**

  - További SQL Server-bejelentkezés létrehozása a felügyelt példányban
  - Adja hozzá a [bejelentkezést a sysadmin fix kiszolgálói szerepkörhöz](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles) az [ALTER SERVER ROLE](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql) utasítás használatával. Ez a bejelentkezés teljes körű rendszergazdai engedélyekkel fog rendelkezni.
  - Másik lehetőségként hozzon létre egy [Azure AD bejelentkezést](sql-database-aad-authentication-configure.md?tabs=azure-powershell#new-azure-ad-admin-functionality-for-mi) a <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a> szintaxis használatával.

- **Egyetlen vagy készletes telepítés esetén hozzon létre korlátozott rendszergazdai engedélyekkel rendelkező SQL-bejelentkezéseket**

  - Hozzon létre egy további SQL-bejelentkezést a fő adatbázisban egyetlen vagy készletezett adatbázis-telepítéshez vagy felügyelt példány központi telepítéséhez
  - Felhasználói fiók létrehozása az új bejelentkezéshez társított főadatbázisban
  - Adja hozzá a `dbmanager`felhasználói `loginmanager` fiókot a , `master` a szerepkör, vagy mindkettő az adatbázisban az [ALTER SERVER ROLE](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql) utasítás (az Azure Synapse Analytics, használja a [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) utasítás).

  > [!NOTE]
  > `dbmanager`és `loginmanager` a szerepkörök **nem** vonatkoznak a felügyelt példány-központi telepítésekhez.

  A [speciális főadatbázis-szerepkörök](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles#special-roles-for--and-) tagjai egy- vagy készletezésű adatbázisokhoz lehetővé teszik, hogy a felhasználók nak jogkörük legyen adatbázisok létrehozására és kezelésére, illetve bejelentkezések létrehozására és kezelésére. A `dbmanager` szerepkörben tagsággal rendelkező felhasználó által létrehozott adatbázisokban a tag `db_owner` le van képezve a rögzített adatbázisszerepkörhöz, és a felhasználói fiók használatával bejelentkezhet az adatbázisba, és kezelheti azt. `dbo` Ezek a szerepkörök nem rendelkeznek kifejezett engedélyekkel a fő adatbázison kívül.

  > [!IMPORTANT]
  > Egyetlen vagy készletbe adott adatbázisban nem hozhat létre további SQL-bejelentkezést teljes körű rendszergazdai engedélyekkel.

## <a name="create-accounts-for-non-administrator-users"></a>Nem rendszergazdai felhasználók fiókjainak létrehozása

A nem rendszergazda felhasználók számára a következő két módszer egyikével hozhat létre fiókokat:

- **Bejelentkezés létrehozása**

  SQL-bejelentkezés létrehozása a fő adatbázisban. Ezután hozzon létre egy felhasználói fiókot minden olyan adatbázisban, amelyhez a felhasználónak hozzáférésre van szüksége, és társítsa a felhasználói fiókot a bejelentkezéshez. Ez a megközelítés akkor ajánlott, ha a felhasználónak több adatbázishoz kell hozzáférnie, és a jelszavakat szinkronizálni szeretné. Ez a megközelítés azonban összetett, ha georeplikációval használják, mivel a bejelentkezést mind az elsődleges kiszolgálón, mind a másodlagos kiszolgáló(k)on létre kell hozni. További információt az [Azure SQL Database biztonságának konfigurálása és kezelése geo-visszaállításhoz vagy feladatátvételhez](sql-database-geo-replication-security-config.md)című témakörben talál.
- **Felhasználói fiók létrehozása**

  Hozzon létre egy felhasználói fiókot az adatbázisban, amelyhez a felhasználónak hozzáférésre van szüksége (más néven [egy zárt felhasználónak).](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable)

  - Egyetlen vagy készletezésű adatbázissal mindig létrehozhat ilyen típusú felhasználói fiókot.
  - Olyan felügyelt példányadatbázis esetén, amely nem támogatja [az Azure AD-kiszolgálói egyszerű felhasználónéveket,](sql-database-aad-authentication-configure.md?tabs=azure-powershell#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)csak ilyen típusú felhasználói fiókot hozhat létre egy [tartalmazott adatbázisban.](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) Az Azure [AD-kiszolgálói egyszerű st.](sql-database-aad-authentication-configure.md?tabs=azure-powershell#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)felügyelt példányokkal létrehozhat felhasználói fiókokat a felügyelt példány hitelesítéséhez anélkül, hogy adatbázis-felhasználókat kellene létrehozni egy tartalmazott adatbázis-felhasználóként.

  Ezzel a megközelítéssel a felhasználói hitelesítési adatok minden adatbázisban tárolódnak, és automatikusan replikálják a georeplikált adatbázisokba. Ha azonban ugyanaz a fiók több adatbázisban is megtalálható, és SQL-hitelesítést használ, a jelszavakat manuálisan kell szinkronizálni. Továbbá, ha a felhasználó különböző adatbázisokban rendelkezik fiókkal különböző jelszavakkal, a jelszavak megjegyzése problémát okozhat.

> [!IMPORTANT]
> Az Azure AD-identitásokhoz leképezett, tartalmazott felhasználók létrehozásához be kell jelentkeznie egy Azure AD-fiók, amely az SQL-adatbázis rendszergazdája. Felügyelt példányban egy engedéllyel `sysadmin` rendelkező SQL-bejelentkezés azure AD-bejelentkezést vagy felhasználót is létrehozhat.

A bejelentkezések és felhasználók létrehozásának példáit a következő témakörökben talál:

- [Bejelentkezés létrehozása egy- vagy készletezésű adatbázisokhoz](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current#examples-1)
- [Bejelentkezés létrehozása felügyelt példányadatbázishoz](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current#examples-2)
- [Bejelentkezés létrehozása az Azure Synapse Analytics-adatbázishoz](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest#examples-3)
- [Felhasználó létrehozása](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql#examples)
- [Az Azure AD-t tartalmazó felhasználók létrehozása](sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)

> [!TIP]
> Az SQL Server egy beépített felhasználójának egyetlen vagy készletbe sorolt adatbázisban való létrehozását tartalmazó biztonsági oktatóanyagról [az Oktatóanyag: Egyetlen vagy készletezésű adatbázis biztonságossá tétele](sql-database-security-tutorial.md)című témakörben található.

## <a name="using-fixed-and-custom-database-roles"></a>Rögzített és egyéni adatbázis-szerepkörök használata

Miután létrehozott egy felhasználói fiókot egy adatbázisban, akár egy bejelentkezés alapján, akár egy beépített felhasználóként, engedélyezheti a felhasználónak, hogy különböző műveleteket hajtson végre, és hozzáférjen egy adott adatbázis adataihoz. A hozzáférés engedélyezéséhez a következő módszereket használhatja:

- **Rögzített adatbázis-szerepkörök**

  Adja hozzá a felhasználói fiókot egy [rögzített adatbázis-szerepkörhöz.](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles) 9 rögzített adatbázis-szerepkör van, amelyek mindegyike meghatározott engedélykészlettel rendelkezik. A leggyakoribb rögzített adatbázis-szerepkörök a következők: **db_owner**, **db_ddladmin**, **db_datawriter**, **db_datareader**, **db_denydatawriter**és **db_denydatareader**. A **db_owner** általában teljes körű engedélyek biztosítására szolgál néhány felhasználó számára. A többi rögzített adatbázis-szerepkör hasznos az egyszerű adatbázisok fejlesztésének gyors elkezdéséhez, de a legtöbb éles környezetbeli adatbázishoz nem ajánlott. A **db_datareader** rögzített adatbázis-szerepkör olvasási hozzáférést biztosít az adatbázis minden táblájához, ami több, mint ami feltétlenül szükséges.

  - Felhasználó hozzáadása rögzített adatbázis-szerepkörhöz:

    - Az Azure SQL Database-ben használja az [ALTER ROLE](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql) utasítást. Példák: [ALTER ROLE examples](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql#examples)
    - Azure Synapse Analytics, használja a [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) utasítást. Példákat [a sp_addrolemember példákban](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql)talál.

- **Egyéni adatbázisszerepkör**

  Hozzon létre egyéni adatbázis-szerepkört a [CREATE ROLE](https://docs.microsoft.com/sql/t-sql/statements/create-role-transact-sql) utasítás használatával. Az egyéni szerepkör lehetővé teszi, hogy saját, felhasználó által definiált adatbázis-szerepköröket hozzon létre, és gondosan adja meg minden szerepkörnek a vállalkozás igényéhez szükséges legkevesebb engedélyt. Ezután hozzáadhat felhasználókat az egyéni szerepkörhöz. Ha a felhasználó egyszerre több szerepkörnek is tagja, akkor a rendszer összesíti az engedélyeket.
- **Engedélyek megadása közvetlenül**

  Adja meg a felhasználói fiók [engedélyeit](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) közvetlenül. Az SQL Database-ben több mint 100 engedély adható vagy tagadható meg külön-külön. Ezek közül számos engedély beágyazott. Egy sémában található `UPDATE` engedély például a séma minden táblájára vonatkozó `UPDATE` engedélyt tartalmazza. A legtöbb engedélyrendszerhez hasonlóan az engedély megtagadása felülírja a megadását. Az engedélyek beágyazott jellege és száma miatt lehetséges, hogy alapos tervezés szükséges az adatbázis megfelelő védelmét biztosító engedélyrendszer kialakításához. Kezdje az [Engedélyek (Adatbázismotor)](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) szakaszban felsorolt engedélyek listájával, majd tekintse át az engedélyek [poszterméretű ábráját](https://docs.microsoft.com/sql/relational-databases/security/media/database-engine-permissions.png).

## <a name="using-groups"></a>Csoportok használata

A hatékony hozzáférés-kezelés az Active Directory biztonsági csoportjaihoz rendelt engedélyeket és rögzített vagy egyéni szerepköröket használja az egyes felhasználók helyett.

- Az Azure Active Directory-hitelesítés használataesetén helyezze az Azure Active Directory-felhasználókat egy Azure Active Directory biztonsági csoportba. Hozzon létre a csoportban egy adatbázis-felhasználót. Helyezzen egy vagy több adatbázis-felhasználót egy egyéni adatbázis-szerepkörbe, amely az adott felhasználói csoportnak megfelelő engedélyekkel rendelkezik.

- SQL-hitelesítés használata esetén hozzon létre egy bent lévő adatbázis-felhasználókat az adatbázisban. Helyezzen egy vagy több adatbázis-felhasználót egy egyéni adatbázis-szerepkörbe, amely az adott felhasználói csoportnak megfelelő engedélyekkel rendelkezik.

  > [!NOTE]
  > Csoportokat nem tartalmazott adatbázis-felhasználók számára is használhat.

Érdemes megismerkednie az alábbi funkciókkal, amelyek az engedélyek korlátozására vagy a szint emelésére is használhatók:

- A [megszemélyesítés](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/customizing-permissions-with-impersonation-in-sql-server) és a [modulaláírás](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server) az engedélyek biztonságos átmeneti növelésére használható.
- A [sorszintű biztonság](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) használatával korlátozhatja, hogy a felhasználó mely sorokhoz férhessen hozzá.
- Az [adatmaszkolás](sql-database-dynamic-data-masking-get-started.md) használatával korlátozhatja a bizalmas adatok megjelenítését.
- A [tárolt eljárások](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) segítségével korlátozhatja az adatbázisban végezhető műveleteket.

## <a name="next-steps"></a>További lépések

Az SQL Database összes biztonsági szolgáltatásáról [az SQL biztonsági szolgáltatásainak áttekintése](sql-database-security-overview.md) biztosít áttekintést.
