---
title: "Az Azure-ban PaaS-adatbázisok védelme |} Microsoft Docs"
description: " Tudnivalók Azure SQL Database és az SQL Data Warehouse biztonsági gyakorlati tanácsok a PaaS webes és mobilalkalmazásokhoz biztonságossá tételéhez. "
services: security
documentationcenter: na
author: techlake
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: ed15ff750e73179f6979d13e45ab27aeee5c82ab
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2017
---
# <a name="securing-paas-databases-in-azure"></a>Az Azure-ban PaaS-adatbázisok védelme

Ez a cikk arról lesz szó gyűjteménye [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) és [SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/) ajánlott biztonsági eljárások az PaaS webes és mobilalkalmazások védelme. Az alábbi gyakorlati tanácsok az Azure-ral tapasztalatunk és az ügyfelek, például a saját kezűleg feladatait származik.

## <a name="azure-sql-database-and-sql-data-warehouse"></a>Az Azure SQL Database és SQL-adatraktár
[Az Azure SQL Database](../sql-database/sql-database-technical-overview.md) és [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) adja meg az Internet-alapú alkalmazások relációs adatbázis-szolgáltatás. Nézzük szolgáltatások, amelyek segítenek megvédeni az alkalmazások és adatok Azure SQL Database és az SQL Data Warehouse PaaS központi telepítés használata esetén:

- Az Azure Active Directory authentication (helyett az SQL Server-hitelesítés)
- Az Azure SQL-tűzfal
- Az átlátható adattitkosítás (TDE)

## <a name="best-practices"></a>Ajánlott eljárások

### <a name="use-a-centralized-identity-repository-for-authentication-and-authorization"></a>Egy központi identitás-tárházat használja a hitelesítéshez és engedélyezéshez

Az Azure SQL-adatbázisok beállítható úgy, hogy a két típusú hitelesítés valamelyikével:

- **SQL-hitelesítés** felhasználónevet és jelszót használja. Az adatbázis logikai kiszolgálójának létrehozásakor megadta a „kiszolgálói rendszergazda” bejelentkezés felhasználónevét és jelszavát. Ezeket a hitelesítő adatokat használ, elvégezheti a hitelesítést bármely adatbázis a kiszolgálón az adatbázis tulajdonosa.

- **Az Azure Active Directory-hitelesítéssel** használja az Azure Active Directory által kezelt identitások és a felügyelt és integrált tartományok használata támogatott. Azure Active Directory-hitelesítés használatához létre kell hoznia egy másik kiszolgáló rendszergazdája, az "Azure AD admin," az Azure Active Directory-felhasználók és csoportok felügyelete engedélyezett nevű. Ez a rendszergazda a normál kiszolgálói rendszergazdák által elvégezhető összes műveletet is végrehajthatja.

[Az Azure Active Directory authentication](../active-directory/develop/active-directory-authentication-scenarios.md) egy mechanizmus a csatlakozás az Azure SQL Database és az SQL Data Warehouse szolgáltatáshoz identitások az Azure Active Directory (AD). Az Azure AD ahelyett, hogy az SQL Server-hitelesítést biztosít, így a felhasználói identitások elterjedése leállíthatja, adatbázis-kiszolgáló között. Az Azure AD-alapú hitelesítés lehetővé teszi, hogy központilag kezelheti az adatbázis-felhasználók és más Microsoft-szolgáltatásokban egyetlen központi helyen identitását. Központi azonosítófelügyeleti biztosít egy helyen adatbázis-felhasználók kezelése és egyszerűbbé teszi a jogosultság kezelése.  

SQL-hitelesítés helyett az Azure AD-alapú hitelesítés használatának előnyei a következők:

- Lehetővé teszi, hogy a jelszó Elforgatás egyetlen helyen.
- Kezeli az adatbázis-engedélyek használata külső Azure Active Directory csoportokat.
- Jelszavak tárolását megszünteti az integrált Windows-hitelesítés és más Azure AD által támogatott hitelesítési engedélyezésével.
- Használja az adatbázis-felhasználók számára az adatbázis szintjén személyazonosság tartalmazott.
- Csatlakozás az SQL Database alkalmazások jogkivonat-alapú hitelesítést is támogatja.
- AD FS (tartomány-összevonás) vagy a natív felhasználói/jelszó-hitelesítés támogat egy helyi az Azure AD tartományi szinkronizálás nélkül.
- Active Directory univerzális hitelesítést használó, beleértve az SQL Server Management Studio kapcsolatokat támogatja [multi-factor Authentication (MFA)](../multi-factor-authentication/multi-factor-authentication.md). Többtényezős hitelesítés tartalmazza, könnyen ellenőrzési lehetőségek széles erős hitelesítés – telefonhívás, szöveges üzenetet, intelligens kártyák PIN-kód és az értesítést a mobilalkalmazásban. További információkért lásd: [SSMS támogatása az Azure AD MFA az SQL-adatbázis és az SQL Data Warehouse](../sql-database/sql-database-ssms-mfa-authentication.md).

Az Azure AD-alapú hitelesítés kapcsolatos további információkért lásd:

- [Kapcsolódás az SQL Database vagy az SQL Data Warehouse az Azure Active Directory-hitelesítés használatával](../sql-database/sql-database-aad-authentication.md)
- [Hitelesítés az Azure SQL Warehouse-szal](../sql-data-warehouse/sql-data-warehouse-authentication.md)
- [Azure SQL DB használatával az Azure AD hitelesítési jogkivonat-alapú hitelesítés támogatása](https://blogs.msdn.microsoft.com/sqlsecurity/2016/02/09/token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/)

> [!NOTE]
> Győződjön meg arról, hogy Azure Active Directory remekül beválik, ha a környezetben, lásd: [Azure AD-funkciókat és korlátozások](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations), kifejezetten a további szempontok.
>
>

### <a name="restrict-access-based-on-ip-address"></a>IP-címen alapuló hozzáférés korlátozása
Létrehozhat olyan tűzfalszabály, amely elfogadható IP-címek tartományát adja meg. Ezek a szabályok telepíthető a kiszolgálók és az adatbázis szintjén. Adatbázis-szintű tűzfalszabályok amikor csak lehetséges, növelje a biztonságot, és ellenőrizze az adatbázis több hordozható használatát javasoljuk. Kiszolgálószintű tűzfal-szabályok használata leginkább a rendszergazdák, és ha sok adatbázisok esetén, amelyek ugyanazt a hozzáférési követelmények rendelkezik, de nem kívánja minden adatbázisnak külön-külön konfigurálása időt.

SQL-adatbázis alapértelmezett forrás IP-címkorlátozások engedélyezi a hozzáférést a következő bármely Azure-(beleértve más előfizetések és a bérlők). Korlátozhatja, hogy csak az IP-címek, a példány eléréséhez. Erős hitelesítés még akkor is, az SQL-tűzfal és az IP-címkorlátozások, továbbra is szükséges. Lásd az ebben a cikkben ajánlásokat.

Azure SQL tűzfal és az IP-korlátozások kapcsolatos további információkért lásd:

- [Az Azure SQL Database hozzáférés-vezérlés](../sql-database/sql-database-control-access.md)
- [Az Azure SQL Database tűzfalszabályok - konfigurálása – áttekintés](../sql-database/sql-database-firewall-configure.md)
- [Az Azure portál használata az Azure SQL Database kiszolgálószintű tűzfal szabály konfigurálása](../sql-database/sql-database-configure-firewall-settings.md)

### <a name="encryption-of-data-at-rest"></a>A tárolt adatok titkosítása
[Transzparens Data Encryption (TDE)](https://msdn.microsoft.com/library/azure/bb934049) alapértelmezés szerint engedélyezve van. TDE transzparens módon titkosítja az SQL Server, az Azure SQL Database és Azure SQL Data Warehouse adatainak és naplókönyvtárainak fájlokat. TDE közvetlen hozzáférést a fájlokhoz vagy a biztonsági mentés biztonsági sérülése véd. Ez lehetővé teszi, hogy inaktív adatok titkosítása a meglévő alkalmazások módosítása nélkül. TDE mindig maradjon engedélyezett; Ez azonban nem állítja le a támadó a szokásos hozzáférés elérési úton. TDE lehetővé teszi a sok törvényi, a szabályozókat és a különböző iparágak létrehozott iránymutatásokat ahhoz, hogy.

Az Azure SQL kulcs kapcsolatos probléma TDE kezeli. A TDE, a helyszínen különös gondot kell fordítani helyreállíthatóságának érdekében, amikor adatbázisok áthelyezése. Az összetettebb forgatókönyveket, a kulcsok explicit módon felügyelhető az Azure Key Vault a bővíthető kulcskezelési (lásd: [TDE engedélyezése az SQL Server használatával EKM](/security/encryption/enable-tde-on-sql-server-using-ekm)). Ezenkívül lehetővé teszi a kapcsolja a saját kulcs használatának (BYOK) Azure kulcs tárolók BYOK funkció segítségével.

Az Azure SQL az oszlopok titkosítását biztosítja [mindig titkosítja](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Ez lehetővé teszi a bizalmas oszlopok csak engedélyezett alkalmazások elérésére. Ilyen típusú titkosítás használata korlátozza az SQL-lekérdezések a titkosított oszlopokban egyenlőség alapú értékekre.

Alkalmazás a blokkszintű titkosítás szelektív adatok is használható. Adatok közös joghatóság alá aggályokat néha kivédhető, hogy a helyes országban kulccsal adatok titkosításával. Ez megakadályozza, hogy még véletlen adatátvitel problémát okozó óta nem lehet visszafejteni az adatokat a kulcs, feltéve, hogy egy erős algoritmust használnak (például az AES 256) nélkül.

További óvintézkedéseket segítségével biztonságossá tétele az adatbázisban, például a biztonságos rendszerek tervezése, bizalmas eszközök titkosítása és az adatbázis-kiszolgálók körül tűzfal létrehozása.

## <a name="next-steps"></a>Következő lépések
Ez a cikk bevezetett SQL-adatbázis és az SQL Data Warehouse ajánlott biztonsági eljárások az PaaS webes és mobilalkalmazások védelme gyűjteménye. A PaaS üzemelő példányok biztosításával kapcsolatos további tudnivalókért lásd:

- [PaaS-környezetek védelme](security-paas-deployments.md)
- [A PaaS webes és mobilalkalmazásokhoz, az Azure App Services biztonságossá tétele](security-paas-applications-using-app-services.md)
