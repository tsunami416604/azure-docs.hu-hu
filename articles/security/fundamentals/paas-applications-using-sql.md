---
title: PaaS-adatbázisok biztonságossá tétele az Azure-ban | Microsoft dokumentumok
description: 'Ismerje meg az Azure SQL Database és az SQL Data Warehouse biztonsági gyakorlati tanácsait a PaaS-web- és mobilalkalmazások védelméhez. '
services: security
documentationcenter: na
author: techlake
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2018
ms.author: terrylan
ms.openlocfilehash: c73f585e3102618cea378716491f9354810a6db8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80125007"
---
# <a name="best-practices-for-securing-paas-databases-in-azure"></a>Gyakorlati tanácsok a PaaS-adatbázisok azure-beli védelméhez

Ebben a cikkben az [Azure SQL Database](../../sql-database/sql-database-technical-overview.md) és az [SQL Data Warehouse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) biztonsági gyakorlati tanácsok gyűjteménye a platform-szolgáltatásként (PaaS) webes és mobilalkalmazások védelmére. Ezek az ajánlott eljárások az Azure-ral kapcsolatos tapasztalatainkból és az önhöz hasonló ügyfelek tapasztalataiból származnak.

Az Azure SQL Database és az SQL Data Warehouse relációs adatbázis-szolgáltatást biztosít az internetalapú alkalmazásokhoz. Tekintsük meg azokat a szolgáltatásokat, amelyek segítenek megvédeni az alkalmazásokat és az adatokat az Azure SQL Database és az SQL Data Warehouse paaS-telepítésben való használatakor:

- Azure Active Directory-hitelesítés (SQL Server-hitelesítés helyett)
- Azure SQL tűzfal
- Transzparens adattitkosítás (TDE)

## <a name="use-a-centralized-identity-repository"></a>Központi identitástár használata
Az Azure SQL-adatbázisok beállíthatók kétféle hitelesítés imasnyi használatára:

- **Az SQL-hitelesítés** felhasználónevet és jelszót használ. Az adatbázis logikai kiszolgálójának létrehozásakor megadta a „kiszolgálói rendszergazda” bejelentkezés felhasználónevét és jelszavát. Ezekkel a hitelesítő adatokkal a kiszolgáló bármely adatbázisában hitelesíthető az adatbázis tulajdonosaként.

- **Az Azure Active Directory-hitelesítés** az Azure Active Directory által felügyelt identitásokat használja, és felügyelt és integrált tartományok ban támogatott. Az Azure Active Directory-hitelesítés használatához létre kell hoznia egy másik kiszolgálórendszergazdát, az "Azure AD-rendszergazda", amely az Azure AD-felhasználók és -csoportok felügyeletére engedélyezett. Ez a rendszergazda a normál kiszolgálói rendszergazdák által elvégezhető összes műveletet is végrehajthatja.

[Az Azure Active Directory-hitelesítés](../../active-directory/develop/authentication-scenarios.md) az Azure SQL Database és az SQL Data Warehouse az Azure Active Directoryban (AD) lévő identitások használatával történő kapcsolódás mechanizmusa. Az Azure AD az SQL Server-hitelesítés alternatívájaként szolgál, így leállíthatja a felhasználói identitások elterjedését az adatbázis-kiszolgálók között. Az Azure AD-hitelesítés lehetővé teszi, hogy központilag kezelje az adatbázis-felhasználók és más Microsoft-szolgáltatások identitását egy központi helyen. A központi azonosítófelügyelettel egyetlen helyen kezelheti az adatbázis-felhasználókat, így leegyszerűsítheti az engedélykezelést.  

### <a name="benefits-of-using-azure-ad-instead-of-sql-authentication"></a>Az Azure AD használatának előnyei az SQL-hitelesítés helyett
- Lehetővé teszi a jelszó elforgatását egyetlen helyen.
- Az adatbázis-engedélyeket külső Azure AD-csoportok használatával kezeli.
- Megszünteti a jelszavak tárolását azáltal, hogy engedélyezi az integrált Windows-hitelesítést és az Azure AD által támogatott egyéb hitelesítési formákat.
- A tartalmazott adatbázis-felhasználók segítségével hitelesíti az identitásokat az adatbázis szintjén.
- Támogatja az SQL-adatbázishoz csatlakozó alkalmazások tokenalapú hitelesítését.
- Támogatja a tartományösszevonás az Active Directory összevonási szolgáltatások (ADFS) vagy a natív felhasználói/jelszó-hitelesítés a helyi Azure AD tartomány szinkronizálása nélkül.
- Támogatja az SQL Server Management Studio active directory általános hitelesítést használó kapcsolatait, amely [többtényezős hitelesítést (MFA)](/azure/active-directory/authentication/multi-factor-authentication)is tartalmaz. A számos egyszerű ellenőrzési lehetőségnek (telefonhívás, SMS, intelligens kártya PIN-kóddal vagy mobilalkalmazásos értesítés) köszönhetően az MFA erős hitelesítést kínál. További információ: [Univerzális hitelesítés az SQL Database és az SQL Data Warehouse segítségével.](../../sql-database/sql-database-ssms-mfa-authentication.md)

Ha többet szeretne megtudni az Azure AD-hitelesítésről, olvassa el a következő témakört:

- [Az Azure Active Directory-hitelesítés használata az SQL-adatbázissal, felügyelt példányokkal vagy SQL Adattárral való hitelesítéshez](../../sql-database/sql-database-aad-authentication.md)
- [Hitelesítés az Azure SQL Warehouse-szal](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-authentication.md)
- [Az Azure SQL DB tokenalapú hitelesítéstámogatása az Azure AD-hitelesítés használatával](../../sql-database/sql-database-aad-authentication.md)

> [!NOTE]
> Annak érdekében, hogy az Azure Active Directory megfelelő legyen a környezetéhez, olvassa el az [Azure AD-szolgáltatások és korlátozások című témakört.](../../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations)
>
>

## <a name="restrict-access-based-on-ip-address"></a>Hozzáférés korlátozása IP-cím alapján
Létrehozhat olyan tűzfalszabályokat, amelyek elfogadható IP-címtartományokat határoznak meg. Ezek a szabályok a kiszolgáló és az adatbázis szintjére egyaránt célozhatók. Javasoljuk, hogy amikor csak lehetséges, használjon adatbázisszintű tűzfalszabályokat a biztonság növelése és az adatbázis hordozhatóbbá teszi. A kiszolgálószintű tűzfalszabályok a rendszergazdák számára a legalkalmasbbak, és ha sok olyan adatbázissal rendelkezik, amelyek azonos hozzáférési követelményekkel rendelkeznek, de nem szeretné, hogy az egyes adatbázisok egyenként történő konfigurálásával időt töltsön.

AZ SQL Database alapértelmezett forrás IP-címkorlátozásai bármely Azure-címről engedélyezik a hozzáférést, beleértve más előfizetéseket és bérlőket is. Ezt korlátozhatja úgy, hogy az IP-címek csak a példányhoz férjenek hozzá. Még az SQL tűzfal és az IP-cím korlátozások, erős hitelesítésre van szükség. Tekintse meg a cikk korábbi javaslatait.

Az Azure SQL Tűzfalra és az IP-korlátozásokra vonatkozó további tudnivalók:

- [Az Azure SQL Database és az SQL Data Warehouse hozzáférés-vezérlése](../../sql-database/sql-database-manage-logins.md)
- [Az Azure SQL Database és az SQL Data Warehouse tűzfalszabályai](../../sql-database/sql-database-firewall-configure.md)


## <a name="encrypt-data-at-rest"></a>Inaktív adatok titkosítása
[Az átlátszó adattitkosítás (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) alapértelmezés szerint engedélyezve van. A TDE transzparens módon titkosítja az SQL Server, az Azure SQL Database és az Azure SQL Data Warehouse-adatokat és naplófájlokat. A TDE védelmet nyújt a fájlokhoz vagy biztonsági mentésükhöz való közvetlen hozzáférés feltörése ellen. Ez lehetővé teszi az inaktív adatok titkosítását a meglévő alkalmazások módosítása nélkül. A TDE-nek mindig engedélyezve kell maradnia; ez azonban nem akadályozza meg a támadót a normál elérési út használatával. A TDE lehetővé teszi a különböző iparágakban létrehozott számos törvény, rendelet és irányelv betartását.

Az Azure SQL kezeli a TDE kulcsfontosságú problémákat. A TDE-hez is, a helyszíni használathoz megfelelően különös figyelmet kell fordítani a helyreállíthatóság biztosítására és az adatbázisok áthelyezésére. Kifinomultabb forgatókönyvekben a kulcsok explicit módon kezelhetők az Azure Key Vaultban a bővíthető kulcskezelés révén. Lásd: [TDE engedélyezése az SQL Server en EKM használatával.](/sql/relational-databases/security/encryption/enable-tde-on-sql-server-using-ekm) Ez lehetővé teszi, hogy saját kulcsa (BYOK) az Azure Key Vaults BYOK képességen keresztül.

Az Azure SQL az Oszlopok titkosítását biztosítja az Always Encrypted ( [Mindig titkosított )](/sql/relational-databases/security/encryption/always-encrypted-database-engine)segítségével. Ez csak az engedélyezett alkalmazások számára teszi lehetővé a bizalmas oszlopok elérését. Az ilyen típusú titkosítás az SQL-lekérdezéseket a titkosított oszlopok ra korlátozza az egyenlőségen alapuló értékekre.

Alkalmazásszintű titkosítást kell használni a szelektív adatokhoz is. Az adatok szuverenitásával kapcsolatos aggodalmak néha csökkenthetők az adatok titkosításával a megfelelő országban/régióban tárolt kulccsal. Ez megakadályozza, hogy még a véletlen adatátvitel is problémát okozzon, mivel a kulcs nélkül lehetetlen visszafejteni az adatokat, feltéve, hogy erős algoritmust használnak (például AES 256).

További óvintézkedéseket is használhat az adatbázis biztonságossá tétele érdekében, például biztonságos rendszer tervezése, bizalmas eszközök titkosítása és tűzfal létrehozása az adatbázis-kiszolgálók körül.

## <a name="next-steps"></a>További lépések
Ez a cikk bemutatta az SQL Database és az SQL Data Warehouse biztonsági gyakorlati tanácsainak gyűjteményét a PaaS webes és mobilalkalmazások védelmére. A PaaS-telepítések védelméről a következő témakörökben olvashat bővebben:

- [PaaS-környezetek védelme](paas-deployments.md)
- [PaaS webes és mobilalkalmazások védelme az Azure App Services használatával](paas-applications-using-app-services.md)
