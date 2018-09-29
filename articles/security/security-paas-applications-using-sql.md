---
title: Az Azure PaaS-adatbázisok védelme |} A Microsoft Docs
description: 'Tudnivalók Azure SQL Database és az SQL Data Warehouse biztonság ajánlott eljárások a PaaS webes és mobilalkalmazások védelme. '
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
ms.date: 09/28/2018
ms.author: terrylan
ms.openlocfilehash: 72d5ec09becc1f1d9e23e284e18bcc037ccb3072
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47452479"
---
# <a name="best-practices-for-securing-paas-databases-in-azure"></a>Ajánlott eljárások az Azure PaaS-adatbázisok védelme

Ebben a cikkben bemutatjuk, gyűjteménye [Azure SQL Database](../sql-database/sql-database-technical-overview.md) és [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) biztonsági védelmének bevált gyakorlata a platform--szolgáltatásként (PaaS) webes és mobilalkalmazások védelme. Ajánlott eljárások az funkciót az Azure-ral és a az ügyfelek, például a saját maga származik.

Az Azure SQL Database és az SQL Data Warehouse adjon meg egy relációsadatbázis-szolgáltatás, az internet-alapú alkalmazásokhoz. Nézzük, szolgáltatások, amelyek segítenek megvédeni alkalmazásait és adatait egy PaaS üzemelő példány az Azure SQL Database és az SQL Data Warehouse használatakor:

- Az Azure Active Directory-hitelesítés (nem az SQL Server-hitelesítés)
- Az Azure SQL-tűzfal
- Transzparens adattitkosítás (TDE)

## <a name="use-a-centralized-identity-repository"></a>Egy központi identitástárház használata
Az Azure SQL Database két különböző típusú hitelesítés használatára konfigurálható:

- **SQL-hitelesítés** felhasználónévvel és jelszóval. Az adatbázis logikai kiszolgálójának létrehozásakor megadta a „kiszolgálói rendszergazda” bejelentkezés felhasználónevét és jelszavát. Ezen hitelesítő adatokkal hitelesítheti magát, hogy a kiszolgáló bármely olyan adatbázisába az adatbázis tulajdonosaként.

- **Az Azure Active Directory-hitelesítés** Azure Active Directory által felügyelt identitásokat használ, és a felügyelt és integrált tartományok esetében támogatott. Azure Active Directory-hitelesítést használ, létre kell hoznia egy másik kiszolgálói rendszergazdát néven az "Azure AD-rendszergazda," Ez engedélyezett, az Azure AD-felhasználók és csoportok felügyeletére. Ez a rendszergazda a normál kiszolgálói rendszergazdák által elvégezhető összes műveletet is végrehajthatja.

[Az Azure Active Directory-hitelesítés](../active-directory/develop/authentication-scenarios.md) olyan mechanizmus az identitásokat az Azure Active Directory (AD) használatával csatlakozik az Azure SQL Database és az SQL Data warehouse-bA. Az Azure AD egy SQL Server-hitelesítés alternatívát kínál, így megszüntetheti a felhasználói identitások elterjedése adatbázis-kiszolgáló között. Az Azure AD-hitelesítés lehetővé teszi az adatbázis-felhasználók és a egy központi helyen található más Microsoft-szolgáltatások identitásainak központi kezelését. Központi azonosítófelügyeleti biztosít egy helyen adatbázis-felhasználók kezelése és egyszerűsíti az engedélyek kezelését.  

### <a name="benefits-of-using-azure-ad-instead-of-sql-authentication"></a>SQL-hitelesítés helyett az Azure AD előnyei
- Lehetővé teszi, hogy a jelszóváltoztatás egyetlen helyen.
- Kezeli a külső Azure database engedélyeket AD-csoportokat.
- Integrált Windows-hitelesítés és más Azure AD által támogatott hitelesítési engedélyezésével kiküszöböli a jelszavak tárolását.
- Használja a tartalmazott adatbázis-felhasználók az adatbázis szintjén identitások hitelesítésére.
- Jogkivonat-alapú hitelesítést támogatja az alkalmazások SQL Database csatlakoztatásához.
- Tartomány összevonási az Active Directory összevonási szolgáltatások (ADFS) vagy egy helyi tartomány szinkronizálás nélkül az Azure ad natív felhasználó/jelszó-hitelesítést támogatja.
- Támogatja, amelyek használják az Active Directory univerzális hitelesítéssel, amely tartalmazza az SQL Server Management Studio kapcsolatokat [multi-factor Authentication (MFA)](../active-directory/authentication/multi-factor-authentication.md). MFA magában foglalja a több egyszerű ellenőrzési lehetőség erős hitelesítés – telefonhívás, szöveges üzenet, intelligens kártyák PIN-kód vagy mobilalkalmazásbeli értesítés. További információkért lásd: [univerzális hitelesítés az SQL Database és az SQL Data Warehouse](../sql-database/sql-database-ssms-mfa-authentication.md).

Az Azure AD-hitelesítéssel kapcsolatos további információkért lásd:

- [Hitelesítés az SQL Database felügyelt példányába vagy az SQL Data Warehouse az Azure Active Directory-hitelesítés használata](../sql-database/sql-database-aad-authentication.md)
- [Hitelesítés az Azure SQL Warehouse-szal](../sql-data-warehouse/sql-data-warehouse-authentication.md)
- [Jogkivonat-alapú hitelesítés támogatása az Azure SQL DB használatával az Azure AD-hitelesítés](../sql-database/sql-database-aad-authentication.md)

> [!NOTE]
> Győződjön meg arról, hogy az Azure Active Directory jó megoldás lehet a környezetben, lásd: [Azure AD-funkciók és korlátozások](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations).
>
>

## <a name="restrict-access-based-on-ip-address"></a>Hozzáférés korlátozása IP-cím alapján
Létrehozhat olyan tűzfalszabály, amely elfogadható IP-címek tartományát adja meg. Ezek a szabályok célozhatók lesznek a kiszolgáló és az adatbázis szintjén. Adatbázisszintű tűzfalszabályok, amikor csak lehetséges, a biztonságot, és az adatbázis hordozhatóságának használatát javasoljuk. Kiszolgálószintű tűzfalszabályok legjobb használják a rendszergazdák, és ha sok olyan adatbázisokat, amelyek azonos hozzáférési követelményt, de nem szeretne időt, minden adatbázisnak külön-külön konfigurálása.

SQL-adatbázis alapértelmezett forrás IP-címkorlátozások engedélyezze a hozzáférést minden olyan Azure címet, többek között a más előfizetéseket és a bérlők számára. Ez a kizárólag a eléréséhez a példány IP-címeket is korlátozhatja. Erős hitelesítés mellett is az SQL-tűzfal és az IP-címkorlátozások, továbbra is szükséges. Tekintse meg a korábban ebben a cikkben szereplő javaslatok útmutatóként.

Az Azure SQL-tűzfal- és IP-korlátozások kapcsolatos további információkért lásd:

- [Az Azure SQL Database és az SQL Data Warehouse hozzáférés-vezérlés](../sql-database/sql-database-control-access.md)
- [Az Azure SQL Database és az SQL Data Warehouse tűzfalszabályok](../sql-database/sql-database-firewall-configure.md)


## <a name="encrypt-data-at-rest"></a>Az inaktív adatok titkosítása
[Transzparens adattitkosítás (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) alapértelmezés szerint engedélyezve van. TDE az SQL Server, az Azure SQL Database és az Azure SQL Data Warehouse adathoz és naplófájlhoz transzparens módon titkosítja. TDE tranzakciómentes közvetlen hozzáférést a fájlokhoz vagy a biztonsági mentés biztonságának sérüléséhez. Ez lehetővé teszi az inaktív adatok titkosítását a meglévő alkalmazások módosítása nélkül. TDE mindig maradjon engedélyezve; Ez azonban nem állítja le a támadó a normál elérési út használatával. TDE lehetővé teszi a sok törvényekkel, előírásokkal és a különböző iparágak létrehozott iránymutatásokat ahhoz, hogy.

Az Azure SQL TDE kulcs kapcsolódó problémák kezeli. A TDE, a helyszíni speciális gondot kell fordítani helyreállíthatóságának érdekében, és adatbázisok áthelyezése. Az összetettebb forgatókönyveket a kulcsok explicit módon felügyelhető az Azure Key Vaultban bővíthető kulcskezelés. Lásd: [engedélyezni szeretné a TDE az SQL Server EKM használatával](/sql/relational-databases/security/encryption/enable-tde-on-sql-server-using-ekm). Ez lehetővé teszi a Bring Your Own Key (BYOK) Azure Key tárolók BYOK képesség révén.

Az Azure SQL keresztül oszlopokhoz titkosítást nyújt [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Ez lehetővé teszi a bizalmas oszlopok csak jogosult alkalmazások elérését. Az ilyen típusú titkosítás használata korlátozza a titkosított oszlopokban értékek egyenlőség-alapú SQL-lekérdezéseket.

Alkalmazás a blokkszintű titkosítás szelektív adatokat kell is használható. Adatok szuverenitását aggályokat néha enyhíthetők egy kulcsot, amely a megfelelő országot maradjanak az adatok titkosításához. Ez megakadályozza, hogy még véletlen adatátvitel problémát okoz, mivel nem lehet visszafejteni az adatokat, hogy a kulcs, feltéve, hogy egy erős algoritmust használja (például AES-256).

További óvintézkedéseket segítségével biztonságossá tétele az adatbázis, például a biztonságos rendszerek tervezése során, a bizalmas eszközök titkosítása és az adatbázis-kiszolgálók körül tűzfal létrehozásához.

## <a name="next-steps"></a>További lépések
Ebben a cikkben megismerkedhetett a SQL Database és az SQL Data Warehouse kapcsolódó ajánlott biztonsági eljárások a PaaS webes és mobilalkalmazások védelme gyűjteménye. A PaaS üzemelő példányok védelmével kapcsolatos további tudnivalókért lásd:

- [PaaS-környezetek védelme](security-paas-deployments.md)
- [PaaS web- és mobilalkalmazások az Azure App Services használatával biztonságossá tétele](security-paas-applications-using-app-services.md)
