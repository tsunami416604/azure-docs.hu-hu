---
title: Az Azure PaaS-adatbázisok védelme |} A Microsoft Docs
description: " Tudnivalók Azure SQL Database és az SQL Data Warehouse biztonság ajánlott eljárások a PaaS webes és mobilalkalmazások védelme. "
services: security
documentationcenter: na
author: techlake
manager: MBaldwin
editor: ''
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: 00b2b249f5889888f34d57fd1577ccfea776d00c
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44347970"
---
# <a name="securing-paas-databases-in-azure"></a>Az Azure PaaS-adatbázisok védelme

Ebben a cikkben bemutatjuk, gyűjteménye [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) és [SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/) kapcsolódó ajánlott biztonsági eljárások a PaaS webes és mobilalkalmazások védelme. Ajánlott eljárások az funkciót az Azure-ral és a az ügyfelek, például a saját maga származik.

## <a name="azure-sql-database-and-sql-data-warehouse"></a>Az Azure SQL Database és az SQL Data warehouse-bA
[Az Azure SQL Database](../sql-database/sql-database-technical-overview.md) és [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) adjon meg egy relációsadatbázis-szolgáltatás, az Internet-alapú alkalmazásokhoz. Nézzük, szolgáltatások, amelyek segítenek megvédeni alkalmazásait és adatait egy PaaS üzemelő példány az Azure SQL Database és az SQL Data Warehouse használatakor:

- Az Azure Active Directory-hitelesítés (nem az SQL Server-hitelesítés)
- Az Azure SQL-tűzfal
- Transzparens adattitkosítás (TDE)

## <a name="best-practices"></a>Ajánlott eljárások

### <a name="use-a-centralized-identity-repository-for-authentication-and-authorization"></a>Egy központi identitástárház használja a hitelesítéshez és engedélyezéshez

Az Azure SQL Database két különböző típusú hitelesítés használatára konfigurálható:

- **SQL-hitelesítés** felhasználónévvel és jelszóval. Az adatbázis logikai kiszolgálójának létrehozásakor megadta a „kiszolgálói rendszergazda” bejelentkezés felhasználónevét és jelszavát. Ezen hitelesítő adatokkal hitelesítheti magát, hogy a kiszolgáló bármely olyan adatbázisába az adatbázis tulajdonosaként.

- **Az Azure Active Directory-hitelesítéssel** Azure Active Directory által felügyelt identitásokat használ, és a felügyelt és integrált tartományok esetében támogatott. Azure Active Directory-hitelesítést használ, létre kell hoznia egy másik kiszolgálói rendszergazdát néven az "Azure AD-rendszergazda," Ez engedélyezett, az Azure AD-felhasználók és csoportok felügyeletére. Ez a rendszergazda a normál kiszolgálói rendszergazdák által elvégezhető összes műveletet is végrehajthatja.

[Az Azure Active Directory-hitelesítés](../active-directory/develop/authentication-scenarios.md) olyan mechanizmus az identitásokat az Azure Active Directory (AD) használatával csatlakozik az Azure SQL Database és az SQL Data warehouse-bA. Az Azure AD egy SQL Server-hitelesítés alternatívát kínál, így megszüntetheti a felhasználói identitások elterjedése adatbázis-kiszolgáló között. Az Azure AD-hitelesítés lehetővé teszi az adatbázis-felhasználók és a egy központi helyen található más Microsoft-szolgáltatások identitásainak központi kezelését. Központi azonosítófelügyeleti biztosít egy helyen adatbázis-felhasználók kezelése és egyszerűsíti az engedélyek kezelését.  

Az Azure AD-hitelesítés helyett SQL-hitelesítés előnyei a következők:

- Lehetővé teszi, hogy a jelszóváltoztatás egyetlen helyen.
- Kezeli a külső Azure database engedélyeket AD-csoportokat.
- Integrált Windows-hitelesítés és más Azure AD által támogatott hitelesítési engedélyezésével kiküszöböli a jelszavak tárolását.
- Használja a tartalmazott adatbázis-felhasználók az adatbázis szintjén identitások hitelesítésére.
- Jogkivonat-alapú hitelesítést támogatja az alkalmazások SQL Database csatlakoztatásához.
- ADFS (tartomány-összevonási) vagy natív felhasználó/jelszó-hitelesítés támogat egy helyi Azure AD tartományi szinkronizálás nélkül.
- Támogatja, amelyek használják az Active Directory univerzális hitelesítéssel, amely tartalmazza az SQL Server Management Studio kapcsolatokat [multi-factor Authentication (MFA)](../active-directory/authentication/multi-factor-authentication.md). MFA magában foglalja a több egyszerű ellenőrzési lehetőség erős hitelesítés – telefonhívás, szöveges üzenet, intelligens kártyák PIN-kód vagy mobilalkalmazásbeli értesítés. További információkért lásd: [SSMS-támogatás az Azure AD MFA és az SQL Database és az SQL Data Warehouse](../sql-database/sql-database-ssms-mfa-authentication.md).

Az Azure AD-hitelesítéssel kapcsolatos további információkért lásd:

- [Csatlakozás az SQL Database vagy az SQL Data warehouse-bA az Azure Active Directory-hitelesítés használatával](../sql-database/sql-database-aad-authentication.md)
- [Hitelesítés az Azure SQL Warehouse-szal](../sql-data-warehouse/sql-data-warehouse-authentication.md)
- [Jogkivonat-alapú hitelesítés támogatása az Azure SQL DB használatával az Azure AD-hitelesítés](https://blogs.msdn.microsoft.com/sqlsecurity/2016/02/09/token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/)

> [!NOTE]
> Győződjön meg arról, hogy az Azure Active Directory jó megoldás lehet a környezetben, lásd: [Azure AD-funkciók és korlátozások](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations), kifejezetten a további szempontok.
>
>

### <a name="restrict-access-based-on-ip-address"></a>Hozzáférés korlátozása IP-cím alapján
Létrehozhat olyan tűzfalszabály, amely elfogadható IP-címek tartományát adja meg. Ezek a szabályok célozhatók lesznek a kiszolgáló és az adatbázis szintjén. Adatbázisszintű tűzfalszabályok, amikor csak lehetséges, a biztonságot, és az adatbázis hordozhatóságának használatát javasoljuk. Kiszolgálószintű tűzfalszabályok legjobb használják a rendszergazdák, és ha sok olyan adatbázisokat, amelyek azonos hozzáférési követelményt, de nem szeretne időt, minden adatbázisnak külön-külön konfigurálása.

Az SQL Database alapértelmezett forrás IP-címkorlátozások engedélyezze a hozzáférést minden olyan Azure címe (beleértve a más előfizetéseket és a bérlők). Ez a kizárólag a eléréséhez a példány IP-címeket is korlátozhatja. Erős hitelesítés mellett is az SQL-tűzfal és az IP-címkorlátozások, továbbra is szükséges. Tekintse meg a korábban ebben a cikkben szereplő javaslatok útmutatóként.

Az Azure SQL-tűzfal- és IP-korlátozások kapcsolatos további információkért lásd:

- [Az Azure SQL Database hozzáférés-vezérlés](../sql-database/sql-database-control-access.md)
- [– Azure SQL Database-tűzfalszabályok konfigurálása – áttekintés](../sql-database/sql-database-firewall-configure.md)
- [Konfigurálhatja egy Azure SQL Database kiszolgálószintű tűzfalszabályt az Azure portal használatával](../sql-database/sql-database-configure-firewall-settings.md)

### <a name="encryption-of-data-at-rest"></a>A tárolt adatok titkosítása
[Transzparens adattitkosítás (TDE)](https://msdn.microsoft.com/library/azure/bb934049) alapértelmezés szerint engedélyezve van. TDE az SQL Server, az Azure SQL Database és az Azure SQL Data Warehouse adathoz és naplófájlhoz transzparens módon titkosítja. TDE tranzakciómentes közvetlen hozzáférést a fájlokhoz vagy a biztonsági mentés biztonságának sérüléséhez. Ez lehetővé teszi az inaktív adatok titkosítását a meglévő alkalmazások módosítása nélkül. TDE mindig maradjon engedélyezve; Ez azonban nem állítja le a támadó a normál elérési út használatával. TDE lehetővé teszi a sok törvényekkel, előírásokkal és a különböző iparágak létrehozott iránymutatásokat ahhoz, hogy.

Az Azure SQL TDE kulcs kapcsolódó problémák kezeli. A TDE, a helyszíni speciális gondot kell fordítani helyreállíthatóságának érdekében, és adatbázisok áthelyezése. Az összetettebb forgatókönyveket, a kulcsok explicit módon felügyelhető az Azure Key Vaultban bővíthető kulcskezelés (lásd: [engedélyezése a TDE az SQL Server használatával EKM](/sql/relational-databases/security/encryption/enable-tde-on-sql-server-using-ekm)). Ez lehetővé teszi a Bring Your Own Key (BYOK) Azure Key tárolók BYOK képesség révén.

Az Azure SQL keresztül oszlopokhoz titkosítást nyújt [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Ez lehetővé teszi a bizalmas oszlopok csak jogosult alkalmazások elérését. Az ilyen típusú titkosítás használata korlátozza a titkosított oszlopokban értékek egyenlőség-alapú SQL-lekérdezéseket.

Alkalmazás a blokkszintű titkosítás szelektív adatokat kell is használható. Adatok szuverenitását aggályokat néha enyhíthetők egy kulcsot, amely a megfelelő országot maradjanak az adatok titkosításához. Ez megakadályozza, hogy még véletlen adatátvitel problémát okoz, mivel nem lehet visszafejteni az adatokat, hogy a kulcs, feltéve, hogy egy erős algoritmust használja (például AES-256).

További óvintézkedéseket használhatja például a biztonságos rendszerek tervezése során, a bizalmas eszközök titkosítása és az adatbázis-kiszolgálók körül tűzfal létrehozásához az adatbázis biztonságossá tételéhez.

## <a name="next-steps"></a>További lépések
Ebben a cikkben megismerkedhetett a SQL Database és az SQL Data Warehouse kapcsolódó ajánlott biztonsági eljárások a PaaS webes és mobilalkalmazások védelme gyűjteménye. A PaaS üzemelő példányok védelmével kapcsolatos további tudnivalókért lásd:

- [PaaS-környezetek védelme](security-paas-deployments.md)
- [PaaS web- és mobilalkalmazások az Azure App Services használatával biztonságossá tétele](security-paas-applications-using-app-services.md)
