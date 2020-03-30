---
title: Biztonság – áttekintés
description: Ismerje meg az Azure SQL Database és az SQL Server biztonságát, beleértve a felhő és a helyszíni SQL Server közötti különbségeket.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto, carlrab, emlisa
ms.date: 05/14/2019
ms.openlocfilehash: 2a4c6dd3dd6f1bb2f15e31226086c73fb8e63521
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124829"
---
# <a name="an-overview-of-azure-sql-database-security-capabilities"></a>Az Azure SQL Database biztonsági képességeinek áttekintése

Ez a cikk ismerteti az azure-beli SQL Database-alkalmazás adatrétegének védelmének alapjait. A leírt biztonsági stratégia követi a réteges védelmi mélyreható megközelítést, amint az az alábbi képen látható, és kívülről mozog:

![sql-security-layer.png](media/sql-database-security-overview/sql-security-layer.png)

## <a name="network-security"></a>Hálózati biztonság

A Microsoft Azure SQL Database relációs adatbázis-szolgáltatást biztosít felhőalapú és nagyvállalati alkalmazásokszámára. Az ügyféladatok védelme érdekében a tűzfalak megakadályozzák az adatbázis-kiszolgáló hálózati hozzáférését, amíg a hozzáférés explicit módon nem érhető el az IP-cím vagy az Azure virtuális hálózati forgalom eredete alapján.

### <a name="ip-firewall-rules"></a>IP tűzfalszabályai

Az IP-tűzfal szabályai hozzáférést biztosítanak az adatbázisokhoz az egyes kérelmek eredeti IP-címe alapján. További információt az [Azure SQL Database és az SQL Data Warehouse tűzfalszabályainak áttekintése](sql-database-firewall-configure.md)című témakörben talál.

### <a name="virtual-network-firewall-rules"></a>Virtuális hálózat tűzfalszabályai

[A virtuális hálózati szolgáltatás végpontjai kiterjesztik](../virtual-network/virtual-network-service-endpoints-overview.md) a virtuális hálózati kapcsolatot az Azure gerinchálózatán keresztül, és lehetővé teszik az Azure SQL Database számára, hogy azonosítsa a virtuális hálózati alhálózatot, amelyből a forgalom származik. Ahhoz, hogy a forgalom elérje az Azure SQL Database-t, használja az SQL [szolgáltatás címkék](../virtual-network/security-overview.md) et a hálózati biztonsági csoportokon keresztül érkező kimenő forgalom engedélyezéséhez.

[A virtuális hálózati szabályok](sql-database-vnet-service-endpoint-rule-overview.md) lehetővé teszik, hogy az Azure SQL Database csak a virtuális hálózaton belül kiválasztott alhálózatokról küldött kommunikációt fogadja el.

> [!NOTE]
> A hozzáférés tűzfalszabályokkal való szabályozása *nem* vonatkozik **a felügyelt példányokra.** A szükséges hálózati konfigurációról további információt a [felügyelt példányhoz való csatlakozás](sql-database-managed-instance-connect-app.md) című témakörben talál.

## <a name="access-management"></a>Hozzáférés-kezelés

> [!IMPORTANT]
> Az Azure-on belüli adatbázisok és adatbázis-kiszolgálók kezelését a portál felhasználói fiókjának szerepkör-hozzárendelései szabályozzák. Erről a cikkről további információt az [Azure Portal szerepköralapú hozzáférés-vezérlése című témakörben talál.](../role-based-access-control/overview.md)

### <a name="authentication"></a>Hitelesítés

A hitelesítés azt a folyamatot, amely bizonyítja, hogy a felhasználó az, akinek állítja magát. Az Azure SQL Database kétféle hitelesítést támogat:

- **SQL-hitelesítés**:

    AZ SQL-adatbázis hitelesítése a felhasználó hitelesítésére utal, amikor felhasználónév vel és jelszóval csatlakozik az [Azure SQL Database-hez.](sql-database-technical-overview.md) Az adatbázis-kiszolgáló létrehozása során meg kell adni egy "Kiszolgáló-rendszergazda" bejelentkezést felhasználónévvel és jelszóval. Ezekkel a hitelesítő adatokkal a "kiszolgáló rendszergazdája" az adatbázis-kiszolgáló bármely adatbázisában hitelesíthető az adatbázis tulajdonosaként. Ezt követően további SQL bejelentkezéseket és felhasználókat hozhat létre a kiszolgáló rendszergazdája, amely lehetővé teszi a felhasználók számára, hogy felhasználónévvel és jelszóval csatlakozzanak.

- **Azure Active Directory-hitelesítés:**

    Az Azure Active Directory-hitelesítés az [Azure ACTIVE Database](sql-database-technical-overview.md) és az SQL Data [Warehouse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) identitások használatával az Azure Active Directoryban (Azure AD) való csatlakozás mechanizmusa. Az Azure AD-hitelesítés lehetővé teszi a rendszergazdák számára, hogy központilag kezeljék az adatbázis-felhasználók identitásait és engedélyeit más Microsoft-szolgáltatásokkal együtt egy központi helyen. Ez magában foglalja a jelszótárolás minimalizálását, és lehetővé teszi a központi jelszóelforgatási házirendeket.

     Az **Active Directory-rendszergazdának** létre kell hozniaz Azure AD-hitelesítés SQL-adatbázissal való használatához. További információt az [Sql Database-hez való csatlakozás az Azure Active Directory-hitelesítés használatával című témakörben talál.](sql-database-aad-authentication.md) Az Azure AD-hitelesítés támogatja a felügyelt és összevont fiókokat is. Az összevont fiókok támogatják a Windows-felhasználókat és az Azure AD-vel összevont ügyféltartomány csoportjait.

    További Elérhető Azure AD-hitelesítési lehetőségek: [Active Directory univerzális hitelesítés sql server management studio kapcsolatokhoz,](sql-database-ssms-mfa-authentication.md) beleértve a [többtényezős hitelesítést](../active-directory/authentication/concept-mfa-howitworks.md) és [a feltételes hozzáférést.](sql-database-conditional-access.md)

> [!IMPORTANT]
> Az Azure-on belüli adatbázisok és kiszolgálók kezelését a portál felhasználói fiókjának szerepkör-hozzárendelései szabályozzák. Erről a cikkről további információt az [Azure Portal szerepköralapú hozzáférés-vezérlése című témakörben talál.](../role-based-access-control/overview.md) A hozzáférés tűzfalszabályokkal való szabályozása *nem* vonatkozik **a felügyelt példányokra.** A szükséges hálózati konfigurációról további információt a [felügyelt példányhoz való csatlakozásról](sql-database-managed-instance-connect-app.md) szóló alábbi cikkben talál.

## <a name="authorization"></a>Engedélyezés

Az engedélyezés az Azure SQL-adatbázison belül egy felhasználóhoz rendelt engedélyekre vonatkozik, és meghatározza, hogy a felhasználó mit tehet. Az engedélyeket úgy szabályozzák, hogy felhasználói fiókokat adnak [az adatbázisszerepkörökhöz,](/sql/relational-databases/security/authentication-access/database-level-roles) és adatbázisszintű engedélyeket rendelnek ezekhez a szerepkörökhöz, vagy bizonyos [objektumszintű engedélyeket](/sql/relational-databases/security/permissions-database-engine)adnak a felhasználónak. További információ: [Bejelentkezések és felhasználók](sql-database-manage-logins.md)

Ajánlott eljárásként szükség esetén hozzon létre egyéni szerepköröket. Felhasználók hozzáadása a szerepkörhöz a feladatfunkciójuk hoz szükséges legkevesebb jogosultsággal. Ne rendeljen engedélyeket közvetlenül a felhasználókhoz. A kiszolgálófelügyeleti fiók a beépített db_owner szerepkör tagja, amely kiterjedt engedélyekkel rendelkezik, és csak néhány rendszergazdai feladattal rendelkező felhasználó számára adható meg. Az Azure SQL Database-alkalmazások, az [EXECUTE AS](/sql/t-sql/statements/execute-as-clause-transact-sql) használatával adja meg a hívott modul végrehajtási környezetében, vagy használja [alkalmazási szerepkörök](/sql/relational-databases/security/authentication-access/application-roles) korlátozott engedélyekkel. Ez a gyakorlat biztosítja, hogy az adatbázishoz csatlakozó alkalmazás az alkalmazás által szükséges legkevesebb jogosultsággal rendelkezzen. Ezen bevált gyakorlatok követése elősegíti a feladatok szétválasztását is.

### <a name="row-level-security"></a>Sorszintű biztonság

A Sorszintű biztonság lehetővé teszi az ügyfelek számára, hogy a lekérdezést végrehajtó felhasználó (például csoporttagság vagy végrehajtási környezet) jellemzői alapján szabályozzák az adatbázistábla soraihoz való hozzáférést. A sorszintű biztonság egyéni címkealapú biztonsági fogalmak megvalósítására is használható. További információkat a [sorszintű biztonsággal kapcsolatos](/sql/relational-databases/security/row-level-security) részben találhat.

![azúr-adatbázis-rls.png](media/sql-database-security-overview/azure-database-rls.png)

## <a name="threat-protection"></a>Fenyegetések elleni védelem

Az SQL Database naplózási és fenyegetésészlelési lehetőségek biztosításával biztosítja az ügyféladatokat.

### <a name="sql-auditing-in-azure-monitor-logs-and-event-hubs"></a>SQL-naplózás az Azure Monitor naplóiban és az Eseményközpontokban

Az SQL Database naplózása nyomon követi az adatbázis-tevékenységeket, és segít fenntartani a biztonsági szabványoknak való megfelelést azáltal, hogy az adatbázis-eseményeket egy ügyfél tulajdonában lévő Azure-tárfiók naplójába rögzíti. A naplózás lehetővé teszi a felhasználók számára a folyamatban lévő adatbázis-tevékenységek nyomon követését, valamint a korábbi tevékenységek elemzését és kivizsgálását a potenciális fenyegetések vagy feltételezett visszaélések és a biztonsági jogsértések azonosítása érdekében. További információt az [SQL-adatbázis-naplózás –](sql-database-auditing.md)első lépések című témakörben talál.  

### <a name="advanced-threat-protection"></a>Fejlett fenyegetésvédelem

A Komplex veszélyforrások elleni védelem elemzi az SQL Server naplóit a szokatlan viselkedés és az adatbázisok elérésére vagy kihasználására irányuló potenciálisan káros kísérletek észlelése érdekében. Riasztások jönnek létre a gyanús tevékenységek, például az SQL-injektálás, a potenciális adatok beszivárgása, és a találgatásos kényszerítési támadások, vagy a hozzáférési minták anomáliák a jogosultságeszkalációk és a megsértett hitelesítő adatok használata. Riasztások az Azure [Security Center,](https://azure.microsoft.com/services/security-center/)ahol a gyanús tevékenységek részleteit, és javaslatokat a további vizsgálatra adott műveleteket a fenyegetés csökkentésére. A komplex veszélyforrások elleni védelem kiszolgálónként további díj ellenében engedélyezhető. További információ: [Az SQL Database Komplex veszélyforrások elleni védelem – első lépések](sql-database-threat-detection.md)című témakörben található.

![azure-database-td.jpg](media/sql-database-security-overview/azure-database-td.jpg)

## <a name="information-protection-and-encryption"></a>Információvédelem és titkosítás

### <a name="transport-layer-security-tls-encryption-in-transit"></a>Átviteli réteg biztonsági TLS (titkosítás-in-transit)

Az SQL Database a [Transport Layer Security](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server)segítségével titkosítja a mozgásban lévő adatokat.

Az Sql Server minden kapcsolatesetében mindig kényszeríti a titkosítást (SSL/TLS). Ez biztosítja, hogy az ügyfél és a kiszolgáló közötti összes adat "átvitel közben" titkosítva legyen, függetlenül a ttól, hogy a **kapcsolati** karakterláncban a Titkosítás vagy a **TrustServerCertificate** van-e.

Ajánlott eljárásként azt javasoljuk, hogy az alkalmazás kapcsolati karakterláncában adjon meg egy titkosított kapcsolatot, és _**ne**_ bízzon meg a kiszolgáló tanúsítványában. Ez arra kényszeríti az alkalmazást, hogy ellenőrizze a kiszolgáló tanúsítványát, és így megakadályozza, hogy az alkalmazás sebezhető az ember a középső típusú támadások.

Ha például a ADO.NET illesztőprogramot használja, ez az **Encrypt=True** és a **TrustServerCertificate=False**. Ha a kapcsolati karakterláncot az Azure Portalon kapja meg, akkor a megfelelő beállításokat fogja kapni.

> [!IMPORTANT]
> Előfordulhat, hogy egyes nem microsoftos illesztőprogramok alapértelmezés szerint nem használják a TLS-t, vagy a TLS (<1.2) régebbi verziójára támaszkodnak a működéshez. Ebben az esetben az SQL Server továbbra is lehetővé teszi az adatbázishoz való csatlakozást. Azonban azt javasoljuk, hogy értékelje ki a biztonsági kockázatokat, amelyek lehetővé teszik az ilyen illesztőprogramok és alkalmazások csatlakozhatnak az SQL Database-hez, különösen, ha bizalmas adatokat tárol.
>
> A TLS-ről és a kapcsolatról a [TLS-ekkel kapcsolatos szempontok](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity) című témakörben talál további információt.

### <a name="transparent-data-encryption-encryption-at-rest"></a>Transzparens adattitkosítás (inaktív titkosítás)

[Az Azure SQL Database transzparens adattitkosítása (TDE)](transparent-data-encryption-azure-sql.md) egy biztonsági réteget ad hozzá, amely segít megvédeni az inaktív adatokat a nyers fájlokhoz vagy biztonsági mentésekhöz való jogosulatlan vagy offline hozzáféréssel szemben. Gyakori esetek közé tartozik az adatközpontok lopása vagy a hardver vagy adathordozó, például a lemezmeghajtók és a biztonsági másolatkazetták nem biztonságos ártalmatlanítása.A TDE a teljes adatbázist a teljes AES titkosítási algoritmussal titkosítja, amely nem követeli meg az alkalmazásfejlesztőktől a meglévő alkalmazások módosítását.

Az Azure-ban az összes újonnan létrehozott SQL-adatbázis alapértelmezés szerint titkosítva van, és az adatbázis-titkosítási kulcsot egy beépített kiszolgálói tanúsítvány védi.  A tanúsítvány karbantartását és rotációját a szolgáltatás kezeli, és nem igényel a felhasználó tól származó adatokat. Azok az ügyfelek, akik szívesebben veszik át az irányítást a titkosítási kulcsok felett, kezelhetik a kulcsokat az [Azure Key Vaultban.](../key-vault/key-vault-secure-your-key-vault.md)

### <a name="key-management-with-azure-key-vault"></a>Kulcskezelés az Azure Key Vault segítségével

[Hozza el saját kulcsa](transparent-data-encryption-byok-azure-sql.md) (BYOK) támogatja [az átlátszó adattitkosítás](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE) lehetővé teszi az ügyfelek számára, hogy átvegye a kulcskezelés és a rotáció az [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md), az Azure felhőalapú külső kulcskezelő rendszer használatával. Ha az adatbázis hozzáférése a key vault vissza van vonva, az adatbázis nem fejthető vissza, és olvassa be a memóriába. Az Azure Key Vault központi kulcskezelési platformot biztosít, kihasználja a szigorúan ellenőrzött hardveres biztonsági modulokat (HSM-ek), és lehetővé teszi a kulcsok és az adatok kezelése közötti feladatok elkülönítését a biztonsági megfelelőségi követelmények teljesítése érdekében.

### <a name="always-encrypted-encryption-in-use"></a>Mindig titkosított (használat közbeni titkosítás)

![azúr-adatbázis-ae.png](media/sql-database-security-overview/azure-database-ae.png)

[A Mindig titkosítva](/sql/relational-databases/security/encryption/always-encrypted-database-engine) egy olyan szolgáltatás, amely megvédi az egyes adatbázisoszlopokban tárolt bizalmas adatokat a hozzáféréstől (például hitelkártyaszámok, nemzeti azonosító számok vagy _a szükséges ismeret_ alapján). Ez magában foglalja az adatbázis-rendszergazdák vagy más kiemelt jogosultsággal rendelkező felhasználók, akik jogosultak hozzáférni az adatbázisfelügyeleti feladatok, de nem üzleti kell hozzáférni az adott adatokat a titkosított oszlopok. Az adatok mindig titkosítva vannak, ami azt jelenti, hogy a titkosított adatokat csak a titkosítási kulcshoz hozzáféréssel rendelkező ügyfélalkalmazások feldolgozása érdekében fejti vissza.  A titkosítási kulcs soha nem érhető el az SQL-ben, és a [Windows tanúsítványtárolóban](sql-database-always-encrypted.md) vagy az [Azure Key Vaultban](sql-database-always-encrypted-azure-key-vault.md)tárolható.

### <a name="dynamic-data-masking"></a>Dinamikus adatmaszkolás

![azúr-adatbázis-ddm.png](media/sql-database-security-overview/azure-database-ddm.png)

Az SQL Database dinamikus adatmaszkolása korlátozza a bizalmas adatok nak való kitettséget azáltal, hogy nem emelt szintű jogosultsággal rendelkező felhasználók számára maszkolja azt. A dinamikus adatmaszkolás automatikusan felderíti a potenciálisan bizalmas adatokat az Azure SQL Database-ben, és végrehajtható javaslatokat nyújt a mezők maszkolásához, minimális hatással az alkalmazásrétegre. Rejtjelezi a bizalmas adatokat egy kijelölt adatbázismezőkön végrehajtott lekérdezés eredményhalmazában, miközben az adatbázis adatait nem módosítja. További információ: [Az SQL Database dinamikus adatmaszkolásának első lépései](sql-database-dynamic-data-masking-get-started.md)című témakörben talál.

## <a name="security-management"></a>Biztonság kezelése

### <a name="vulnerability-assessment"></a>Sebezhetőségi felmérés

[A biztonsági rés felmérése](sql-vulnerability-assessment.md) egy könnyen konfigurálható szolgáltatás, amely képes felderíteni, nyomon követni és javítani az adatbázis esetleges biztonsági réseit azzal a céllal, hogy proaktív módon javítsa az adatbázis általános biztonságát. A biztonsági rés felmérése (VA) része a fejlett adatbiztonsági (ADS) ajánlatnak, amely egy egységes csomag a fejlett SQL biztonsági képességekhez. A biztonsági rés felmérése a központi SQL ADS portálon keresztül érhető el és kezelhető.

### <a name="data-discovery--classification"></a>Adatfelderítés és besorolás

Az adatok felderítése & besorolás (jelenleg előzetes verzióban) speciális funkciókat biztosít az Azure SQL Database-be beépített adatokfelderítéséhez, besorolásához, címkézéséhez és az adatbázisokbizalmas adatainak védelméhez. A legérzékenyebb adatok (üzleti/pénzügyi, egészségügyi, személyes adatok stb.) felfedezése és osztályozása döntő szerepet játszhat a szervezeti információvédelem ben. Infrastruktúraként alkalmas lehet az alábbiakra:

- Különböző biztonsági forgatókönyvek, például figyelése (naplózása) és riasztása a rendellenes hozzáférést a bizalmas adatokhoz.
- A rendkívül bizalmas adatokat tartalmazó adatbázisokhoz való hozzáférés szabályozása és biztonságának szigorítása.
- Segíthet megfelelni az adatvédelmi szabványoknak és a szabályozási megfelelőség követelményeinek.

További információ: [Az adatok felderítésének & besorolásának első lépései](sql-database-data-discovery-and-classification.md)című témakörben található.

### <a name="compliance"></a>Megfelelőség

A fenti funkciók és funkciók mellett, amelyek segítségével az alkalmazás megfelel a különböző biztonsági követelményeknek, az Azure SQL Database is részt vesz a rendszeres naplózás, és számos megfelelőségi szabványok igazolt. További információt a [Microsoft Azure Adatvédelmi központban](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) talál, ahol megtalálhatja az SQL Database megfelelőségi tanúsítványainak legfrissebb listáját.

## <a name="next-steps"></a>További lépések

- A bejelentkezések, a felhasználói fiókok, az adatbázis-szerepkörök és az engedélyek SQL Database-ben való használatáról a [Bejelentkezések és felhasználói fiókok kezelése című](sql-database-manage-logins.md)témakörben található.
- Az adatbázis-naplózásról az [SQL Database naplózása](sql-database-auditing.md)című témakörben lehet előbeszélni.
- A fenyegetések észleléséről az [SQL Database fenyegetésészleléscímű](sql-database-threat-detection.md)témakörében lehet előbeszélni.
