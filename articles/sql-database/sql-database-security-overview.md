---
title: Az Azure SQL Database biztonsági áttekintése | Microsoft Docs
description: Ismerje meg az Azure SQL Database és az SQL Server biztonságát, beleértve a felhőalapú és helyszíni SQL Server közötti különbségeket.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto, carlrab, emlisa
manager: craigg
ms.date: 04/08/2019
ms.openlocfilehash: f9387d68139119c13d57ebb135e0c0f5b7bba8ec
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2019
ms.locfileid: "59359048"
---
# <a name="an-overview-of-azure-sql-database-security-capabilities"></a>Azure SQL Database biztonsági lehetőségeinek áttekintése

Ez a cikk az Azure SQL-adatbázist használó alkalmazások adatrétegének alapjait ismerteti. Az ismertetett biztonsági stratégia jellegű defense réteges megközelítésének követi az alábbi képen látható módon, és áthelyezi a kívülről:

![sql-security-layer.png](media/sql-database-security-overview/sql-security-layer.png)

## <a name="network-security"></a>Hálózati biztonság

A Microsoft Azure SQL Database relációsadatbázis-szolgáltatás a felhőalapú és nagyvállalati alkalmazásokat biztosít. Felhasználói adatok védelme érdekében a tűzfalak mindaddig megakadályozzák hálózati hozzáférés, az adatbázis-kiszolgáló mindaddig, amíg explicit módon hozzáférés IP-cím vagy az Azure-beli virtuális hálózati forgalom forrása alapján.

### <a name="ip-firewall-rules"></a>IP-tűzfalszabályainak

IP-tűzfalszabályainak minden kérés eredeti IP-címe alapján adatbázisokhoz való hozzáférést. További információkért lásd: [áttekintése az Azure SQL Database és SQL Data Warehouse tűzfalszabályok](sql-database-firewall-configure.md).

### <a name="virtual-network-firewall-rules"></a>Virtuális hálózati tűzfalszabályt

[Virtuális hálózati Szolgáltatásvégpontok](../virtual-network/virtual-network-service-endpoints-overview.md) a virtuális hálózati kapcsolatot kiterjesztheti az Azure gerinchálózatán keresztül, és azonosíthatja a virtuális hálózat alhálózatához, amely a forgalom származik az Azure SQL Database engedélyezéséhez. Ahhoz, hogy a forgalom elérhesse az Azure SQL Database, az SQL használata [szolgáltatáscímkéket](../virtual-network/security-overview.md) hálózati biztonsági csoportokon keresztül kimenő adatforgalom engedélyezéséhez.

[A virtuális hálózati szabályok](sql-database-vnet-service-endpoint-rule-overview.md) engedélyezése az Azure SQL Database csak a kijelölt alhálózatot a virtuális hálózatokon belüli érkező-kommunikáció fogadására.

> [!NOTE]
> Hozzáférés-tűzfalszabályokkal does *nem* a alkalmazni **felügyelt példány**. További információ a hálózati konfiguráció szükséges, lásd: [csatlakozik a felügyelt példány](sql-database-managed-instance-connect-app.md)

## <a name="access-management"></a>Hozzáférés-kezelés

> [!IMPORTANT]
> Adatbázisok és az Azure database-kiszolgálók kezelése a portál felhasználói fiókjának szerepkör-hozzárendelések vezérlik majd. Ez a cikk további információkért lásd: [szerepköralapú hozzáférés-vezérlés az Azure Portalon](../role-based-access-control/overview.md).

### <a name="authentication"></a>Authentication

Hitelesítés az a folyamat, amely igazolja, a felhasználó, akinek lennie. Az Azure SQL Database két hitelesítési típust támogat:

- **SQL-hitelesítés**:

    SQL database hitelesítése hivatkozik felhasználói hitelesítéssel való kapcsolódáskor [Azure SQL Database](sql-database-technical-overview.md) felhasználónévvel és jelszóval. Az adatbázis az adatbázis kiszolgáló létrehozása során meg kell adni a "Kiszolgálói rendszergazda" bejelentkezés felhasználónévvel és jelszóval. Ezeket a hitelesítő adatokat használ, a "kiszolgálói rendszergazda" is minden adatbázisában hitelesítheti az adott kiszolgálón az adatbázis tulajdonosaként. Ezt követően további SQL-bejelentkezések és felhasználók hozható létre a kiszolgáló rendszergazdája, amelyek lehetővé teszik a felhasználónév és jelszó használatával.

- **Az Azure Active Directory-hitelesítés**:

    Az Azure Active Directory-hitelesítés olyan mechanizmus történő csatlakozás [Azure SQL Database](sql-database-technical-overview.md) és [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) identitások Azure Active Directoryban (Azure AD) használatával. Az Azure AD-hitelesítés lehetővé teszi a rendszergazdák központilag az identitások kezeléséhez és az adatbázis-felhasználók és más Microsoft-szolgáltatások egyetlen központi helyen engedélyek. Ezt jelszótárolás minimalizálásával tartalmazza, és lehetővé teszi a központi jelszóházirendek elforgatás.

     Egy kiszolgálói rendszergazda nevű a **Active Directory-rendszergazda** kell létrehozni az SQL Database az Azure AD-hitelesítés használatára. További információkért lásd: [SQL adatbázis Azure Active Directory-hitelesítéssel csatlakozik](sql-database-aad-authentication.md). Az Azure AD-hitelesítést támogatja a felügyelt és a összevont fiókokat. Az összevont fiókokat támogatja a Windows-felhasználók és csoportok az Azure ad-vel összevont felhasználói tartomány.

    További elérhető az Azure AD-hitelesítési lehetőségek a következők [Active Directory univerzális hitelesítéssel az SQL Server Management Studio](sql-database-ssms-mfa-authentication.md) többek között kapcsolatok [multi-factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md) és [ Feltételes hozzáférés](sql-database-conditional-access.md).

> [!IMPORTANT]
> A portál felhasználói fiókjának szerepkör-hozzárendelések kezeléséhez az adatbázisok és kiszolgálók Azure-ban vezérli. Ez a cikk további információkért lásd: [szerepköralapú hozzáférés-vezérlés az Azure Portalon](../role-based-access-control/overview.md). Hozzáférés-tűzfalszabályokkal does *nem* a alkalmazni **felügyelt példány**. További információt a következő [csatlakozik a felügyelt példány](sql-database-managed-instance-connect-app.md) bővebben a hálózati konfiguráció szükséges.

Engedélyezési hivatkozik az Azure SQL Database a felhasználóhoz rendelt engedélyeket, és meghatározza, hogy mi a felhasználó számára engedélyezett. Engedélyek hozzáadása a felhasználói fiókok által vezérelt [adatbázis-szerepkörök](/sql/relational-databases/security/authentication-access/database-level-roles) adatbázisszintű engedélyeket, vagy a bizonyos felhasználói definiáló [objektumszintű engedélyei](/sql/relational-databases/security/permissions-database-engine). További információkért lásd: [bejelentkezések és felhasználók](sql-database-manage-logins.md)

Ajánlott eljárásként felhasználók hozzáadása a szerepkörhöz a legkevesebb jogosultsággal ehhez a munkakörhöz szükséges. A kiszolgálói rendszergazdai fiók tagja a db_owner szerepkör, amely széles körű engedélyekkel rendelkezik, és kell biztosítani a felhasználók használja. Alkalmazások az Azure SQL Database használatakor használjon [alkalmazás-szerepkörök](/sql/relational-databases/security/authentication-access/application-roles) korlátozott engedélyekkel. Ez biztosítja, hogy az alkalmazás csatlakozik az adatbázishoz az alkalmazás számára szükséges lehető legkevesebb jogosultsággal rendelkezik-e.

### <a name="row-level-security"></a>Sorszintű biztonság

Sorszintű biztonság lehetővé teszi, hogy az ügyfelek számára, hogy szabályozzák egy adatbázistábla soraihoz (például csoporttagság vagy végrehajtási környezet) lekérdezést végrehajtó felhasználó jellemzői alapján való hozzáférést. További információkat a [sorszintű biztonsággal kapcsolatos](/sql/relational-databases/security/row-level-security) részben találhat.

![azure-database-rls.png](media/sql-database-security-overview/azure-database-rls.png)

  Ezt a hitelesítési módszert használ, egy felhasználónevet és jelszót. 

Az Azure SQL Database engedélyekkel áttekintését lásd: [bejelentkezések és felhasználók](sql-database-manage-logins.md#permissions)

## <a name="threat-protection"></a>Fenyegetések elleni védelem

Az SQL Database naplózási és fenyegetésészlelési képességek biztosításával védi a vásárlói adatokat.

### <a name="sql-auditing-in-azure-monitor-logs-and-event-hubs"></a>SQL-naplózás az Azure Monitor naplóira és az Event Hubs

Az SQL Database naplózási szolgáltatása nyomon követi az adatbázis-tevékenységekről, és segít fenntartani a biztonsági szabványoknak való megfelelőség rögzíti az adatbázissal kapcsolatos események egy naplófájlba, jelentkezzen be egy ügyfél saját Azure storage-fiók. Naplózás lehetővé teszi, hogy a felhasználók számára az adatbázisban folyamatban lévő tevékenységeket, figyelése, valamint elemezheti és kivizsgálhatja a azonosíthatja a potenciális fenyegetések vagy gyanús visszaélések és szabálysértések. További információ: Ismerkedés az [SQL Database naplózási szolgáltatásával](sql-database-auditing.md).  

### <a name="threat-detection"></a>Fenyegetések észlelése

Fenyegetés-észlelési fokozza a naplózási szokatlan jelenség a vizsgálati naplók elemzésével és vélhetően kárt okozó az adatbázisokat elérni vagy kiaknázni próbál. Riasztások jönnek létre a gyanús tevékenységeket és rendellenes hozzáférési mintákat, például az SQL-injektálási támadások, potenciális adatok beszivárgás és találgatásos jelszó támadásokkal szemben. A fenyegetésészlelés riasztásai tekinthetők meg a [az Azure Security Center](https://azure.microsoft.com/services/security-center/), amelyben a gyanús tevékenységek részletes információkat, és javaslatokat további műveletek mellett a fenyegetés megadott vizsgálatra. A fenyegetésészlelés $15/kiszolgálói/hó. Fontos ingyenes az első 60 nap. További információkért lásd: [Ismerkedés az SQL Database Threat detection](sql-database-threat-detection.md).

![azure-database-td.jpg](media/sql-database-security-overview/azure-database-td.jpg)

## <a name="information-protection-and-encryption"></a>Information protection és titkosítás

### <a name="transport-layer-security-tls-encryption-in-transit"></a>Transport Layer Security (– az átvitel közbeni titkosítás) TLS

Az SQL Database a mozgásban lévő adatok titkosításával védi a vásárlói adatokat [Transport Layer Security](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server).

Az SQL Server biztonsága ne sérüljön titkosítás (SSL/TLS) minden esetben az összes kapcsolat. Ez biztosítja, hogy az összes adat titkosítva van "átvitel" az ügyfél és kiszolgáló beállításától függetlenül között **titkosítása** vagy **TrustServerCertificate** a kapcsolati karakterláncban.

Ajánlott eljárásként javasoljuk, hogy az alkalmazás kapcsolati karakterláncot, a titkosított kapcsolatot adjon meg és _**nem**_ a kiszolgálótanúsítvány megbízhatósága. Ez kényszeríti a kiszolgálói tanúsítvány ellenőrzéséhez az alkalmazás, és így megakadályozza az alkalmazás nem érinti a streameket a középső típusú támadások.

Például amikor az ADO.NET-illesztő használatával ez történik keresztül **Encrypt = True** és **TrustServerCertificate = False**. A kapcsolati karakterlánc lekérését az Azure Portalról, ha a helyes beállításokat fog rendelkezni.

> [!IMPORTANT]
> Vegye figyelembe, hogy bizonyos nem Microsoft-illesztőprogramok nem alapértelmezés szerint használja a TLS vagy TLS egy régebbi verzióját használja (< 2.0) ahhoz, hogy működni. Ebben az esetben az SQL Server továbbra is lehetővé teszi, hogy csatlakozzon az adatbázishoz. Azonban azt javasoljuk, hogy értékeli, hogy az ilyen illesztőprogramok és -alkalmazás csatlakoztatása SQL Database-adatbázishoz, különösen akkor, ha a bizalmas adatokat tárolni a biztonsági kockázatokat. 
>
> A TLS és a kapcsolat kapcsolatos további információkért lásd: [TLS kapcsolatos szempontok](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity)

### <a name="transparent-data-encryption-encryption-at-rest"></a>Transzparens adattitkosítás (titkosítás inaktív)

[Transzparens adattitkosítás (TDE) az Azure SQL Database](transparent-data-encryption-azure-sql.md) nyers fájlokat vagy a biztonsági mentések illetéktelen vagy kapcsolat nélküli hozzáféréstől inaktív adatok védelme érdekében biztonsági réteget ad hozzá. Gyakori forgatókönyvek a következők: adatközpont ellopása vagy biztonságos felmondó hardver- vagy adathordózóról, például a lemezmeghajtók és biztonsági mentési szalagot. TDE használatával az AES titkosítási algoritmus, amely nem igényel a meglévő alkalmazások ne módosítsa az alkalmazásfejlesztők a teljes adatbázist titkosítja.

Az Azure-ban alapértelmezés szerint minden újonnan létrehozott SQL-adatbázisok titkosított, és a egy beépített kiszolgálói tanúsítvány védi az adatbázis-titkosítási kulcs.  Tanúsítvány karbantartási és váltogatásának a szolgáltatás által kezelt és nem igényel adatbevitelt a felhasználó elől. Ügyfelek, akik inkább a titkosítási kulcsok feletti kezelhetik a kulcsokat a [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md).

### <a name="key-management-with-azure-key-vault"></a>Az Azure Key Vault kulcskezelés

[Bring Your Own Key](transparent-data-encryption-byok-azure-sql.md) (BYOK) támogatása [transzparens adattitkosítás](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE) lehetővé teszi, hogy az ügyfelek számára, hogy saját tulajdonba vétele kulcskezelés és elforgatás használatával [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md), Azure, a felhő alapú külső kulcs felügyeleti rendszer. A key vault az adatbázis-hozzáférést visszavonták, ha egy adatbázis nem lehet visszafejteni és a memóriába. Az Azure Key Vault egy olyan központi kulcskezelési platformot nyújt, szorosan figyelt hardveres biztonsági modulokban (HSM) használ, és lehetővé teszi a kulcsok kezelését és az adatok között feladataik elválasztását biztonsági megfelelőségi követelmények kielégítése érdekében.

### <a name="always-encrypted-encryption-in-use"></a>Always Encrypted (titkosítási-használatban)

![azure-database-ae.png](media/sql-database-security-overview/azure-database-ae.png)

[Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) hozzáférést az adott adatbázis oszlopaiban tárolt bizalmas adatok védelme érdekében olyan szolgáltatás (például hitelkártyaszámokat tartalmazó, különféle nemzeti azonosítószámokat vagy az adatok egy _ismernie kell_ alapja). Ez magában foglalja az adatbázis-rendszergazdák vagy más kiemelt jogosultságú felhasználók, akik jogosultak a felügyeleti feladatokat hajthat végre, de kell érniük az adott a titkosított oszlopokban szereplő üzleti az adatbázis eléréséhez. Az adatok mindig titkosítva, ami azt jelenti, hogy a titkosított adatok csak a feldolgozás visszafejti ügyfél alkalmazások hozzáférése a titkosítási kulcshoz.  A titkosítási kulcs sosem hagyja el az SQL és tárolt vagy a [Windows tanúsítvány Store](sql-database-always-encrypted.md) vagy a [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md).

### <a name="masking"></a>Maszkolási

![azure-database-ddm.png](media/sql-database-security-overview/azure-database-ddm.png)

#### <a name="dynamic-data-masking"></a>Dinamikus adatmaszkolás

Az SQL Database dinamikus adatmaszkolása korlátozza a bizalmas adatok adatmaszkolás segít Önnek nem kiemelt jogosultságú felhasználók által. Dinamikus adatmaszkolás automatikusan észleli a potenciálisan bizalmas adatokat az Azure SQL Database és az alkalmazásrétegre gyakorolt minimális hatás mellett, ezen mezők maszkolására úgy végrehajtható javaslatokat tesz. Rejtjelezi a bizalmas adatokat egy kijelölt adatbázismezőkön végrehajtott lekérdezés eredményhalmazában, miközben az adatbázis adatait nem módosítja. További információkért lásd: [Ismerkedés az SQL Database dinamikus adatmaszkolása](sql-database-dynamic-data-masking-get-started.md).

#### <a name="static-data-masking"></a>Statikus adatok maszkolása

[Statikus Adatmaszkolási](/sql/relational-databases/security/static-data-masking) érhető el egy ügyféloldali eszköz [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 18,0 preview 5 és újabb verziók.  Statikus adatok maszkolása lehetővé teszi a felhasználóknak egy másolatot készít egy adatbázisról, a kijelölt oszlopokban szereplő adatok maszkolva véglegesen lett. A rendelkezésre álló maszkolási NULL maszkolási, egyetlen érték maszkolási, shuffle és maszkolás csoport shuffle, és összetett maszkolási karakterlánc. A maszkolt másolatot készít az adatbázisról, az olyan vállalatok, amelyek külön termelési és tesztelési környezetek a maszkolt példány megosztása. A bizalmas adatok megfelelően védett, és minden más adatbázis jellemzőit megmaradnak. Adatbázisok maszkolás ajánlott, ha adatbázisokhoz való külső hozzáférés szükség.

## <a name="security-management"></a>Biztonság kezelése

### <a name="vulnerability-assessment"></a>Sebezhetőségi felmérés

[A biztonságirés-értékelési](sql-vulnerability-assessment.md) egy könnyen beállítható a szolgáltatás, amely a felderítése, nyomon követheti, és megoldhatja a cél proaktív módon a teljes adatbázis-biztonság növelése érdekében az adatbázis biztonsági réseinek. Biztonságirés-felmérés (VA) a speciális biztonsági (ADS) ajánlat, amely egy SQL-biztonság speciális képességek egységes csomag részét képezi. A biztonságirés-értékelési érheti el és a központi SQL ADS portálon keresztül kezelhetők.

### <a name="data-discovery--classification"></a>Adatfelderítés és besorolás

Adatfelderítés és besorolás (jelenleg előzetes verzióban érhető el) biztosít a felderítése, besorolása, címkézése és védelme a az adatbázisokban a bizalmas adatokat az Azure SQL Database-be épített fejlettebb képességek. Felderítése és besorolása a lehető legnagyobb mértékben bizalmas adatok (üzleti pénzügyi, egészségügyi, a személyes adatok stb.) is kulcsfontosságú szerepet játszanak az Information protection szervezeti helyzetében. Az infrastruktúra szolgálhasson:

- Különféle biztonsági forgatókönyvek, például a (naplózás) figyelési és riasztási a bizalmas adatokhoz való rendellenes hozzáférést.
- Való hozzáférés szabályozásának és korlátozására, biztonságát a szigorúan bizalmas adatokat tartalmazó adatbázisok.
- Lehetővé teszi, hogy megfeleljen az adatok adatvédelmi szabványok és az előírt megfelelőségi követelmények teljesítését.

További információkért lásd: [Ismerkedés az adatfelderítés és besorolás](sql-database-data-discovery-and-classification.md).

### <a name="compliance"></a>Megfelelőség

Mellett a fent említett szolgáltatások és funkciók, amelyek segíthetnek az alkalmazás is megfelelnek a különböző biztonsági követelmények, Azure SQL Database rendszeres vizsgálatokon részt vesz, és a egy több megfelelőségi szabvány tanúsított. További információkért lásd: a [Microsoft Azure Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) ahol megtalálhatja a legfrissebb listáját az SQL Database megfelelőségi minősítései közül is bemutat.

## <a name="next-steps"></a>További lépések

- Az SQL Database említett hozzáférés-vezérlési funkcióinak használatával kapcsolatban lásd a [hozzáférés-vezérléssel](sql-database-control-access.md) foglalkozó témakört.
- Az adatbázis naplózási szolgáltatásával, lásd: [SQL Database naplózási funkciójának](sql-database-auditing.md).
- A fenyegetésészlelés, lásd: [SQL Database fenyegetésészlelési](sql-database-threat-detection.md).
