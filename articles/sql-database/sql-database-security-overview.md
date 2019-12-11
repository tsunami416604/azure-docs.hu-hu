---
title: Biztonsági áttekintés
description: Ismerje meg Azure SQL Database és SQL Server biztonságot, beleértve a Felhőbeli és a helyszíni SQL Server közötti különbségeket.
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
ms.openlocfilehash: c9f59eb8c299eb9319694d392c2b5d1d814ed9b8
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74997326"
---
# <a name="an-overview-of-azure-sql-database-security-capabilities"></a>A Azure SQL Database biztonsági képességeinek áttekintése

Ez a cikk ismerteti az alkalmazások adatszintjének Azure SQL Database használatával történő biztonságossá tételének alapjait. A leírt biztonsági stratégia az alábbi képen látható, többrétegű védelem részletes megközelítését követi, és a következőn kívülről lép:

![SQL-Security-Layer. png](media/sql-database-security-overview/sql-security-layer.png)

## <a name="network-security"></a>Hálózati biztonság

A Microsoft Azure SQL Database a Felhőbeli és a vállalati alkalmazások számára biztosít egy kapcsolódó adatbázis-szolgáltatást. Az ügyféladatok védelme érdekében a tűzfalak meggátolják a hálózati hozzáférést az adatbázis-kiszolgálóhoz, amíg az IP-cím vagy az Azure Virtual Network-forgalom forrása alapján explicit módon hozzáférést nem kapnak.

### <a name="ip-firewall-rules"></a>IP-tűzfalszabályok

Az IP-tűzfalszabályok az egyes kérések származó IP-címe alapján biztosítanak hozzáférést az adatbázisokhoz. További információ: [Azure SQL Database és SQL Data Warehouse tűzfalszabályok áttekintése](sql-database-firewall-configure.md).

### <a name="virtual-network-firewall-rules"></a>Virtuális hálózat tűzfalszabályai

A [virtuális hálózati szolgáltatás végpontok](../virtual-network/virtual-network-service-endpoints-overview.md) kiterjesztik a virtuális hálózati kapcsolatot az Azure-gerinc fölé, és lehetővé teszik Azure SQL Database azon virtuális hálózati alhálózat azonosítását, amelyből a forgalom származik. Ha engedélyezni szeretné, hogy a forgalom elérje a Azure SQL Database, használja az SQL- [szolgáltatás címkéit](../virtual-network/security-overview.md) , hogy engedélyezze a kimenő forgalmat a hálózati biztonsági csoportokon keresztül.

A [virtuális hálózati szabályok](sql-database-vnet-service-endpoint-rule-overview.md) lehetővé teszik, hogy a Azure SQL Database csak a virtuális hálózaton belüli kijelölt alhálózatokból továbbított kommunikációt fogadják.

> [!NOTE]
> A tűzfalszabályok hozzáférésének szabályozása *nem* vonatkozik **a felügyelt példányokra**. További információ a szükséges hálózati konfigurációról: [Csatlakozás felügyelt példányhoz](sql-database-managed-instance-connect-app.md)

## <a name="access-management"></a>Hozzáférés-kezelés

> [!IMPORTANT]
> Az adatbázisok és az adatbázis-kiszolgálók Azure-beli kezelését a portál felhasználói fiókjának szerepkör-hozzárendelései vezérlik. További információ erről a cikkről: [szerepköralapú hozzáférés-vezérlés Azure Portalban](../role-based-access-control/overview.md).

### <a name="authentication"></a>Hitelesítés

A hitelesítés az a folyamat, amellyel a felhasználó igazolni kívánja. Azure SQL Database két hitelesítési típust támogat:

- **SQL-hitelesítés**:

    Az SQL Database-hitelesítés a felhasználó hitelesítésére utal, amikor a felhasználónévvel és a jelszóval csatlakozik [Azure SQL Databasehoz](sql-database-technical-overview.md) . Az adatbázis-kiszolgáló létrehozásakor meg kell adni a "kiszolgáló rendszergazdája" felhasználónevét és jelszavát. Ezekkel a hitelesítő adatokkal a "kiszolgáló rendszergazdája" hitelesítheti az adatbázis-kiszolgáló bármely adatbázisát az adatbázis tulajdonosaként. Ezt követően további SQL-bejelentkezéseket és felhasználókat hozhat létre a kiszolgáló rendszergazdája, amely lehetővé teszi a felhasználók számára a Felhasználónév és a jelszó használatával történő kapcsolódást.

- **Azure Active Directory hitelesítés**:

    Azure Active Directory a hitelesítés olyan mechanizmus, amellyel [Azure SQL Database](sql-database-technical-overview.md) és [SQL Data Warehousehoz](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) csatlakozhat az Azure Active Directory (Azure ad) identitások használatával. Az Azure AD-hitelesítés lehetővé teszi, hogy a rendszergazdák központilag kezeljék az adatbázis-felhasználók identitásait és engedélyeit, valamint a Microsoft más szolgáltatásait egy központi helyen. Ez magában foglalja a jelszó-tárolás minimalizálása és a központosított jelszó-rotációs házirendek használatát.

     Az Azure AD-hitelesítés SQL Database használatával való használatához létre kell hozni egy **Active Directory rendszergazdának** nevezett kiszolgáló-rendszergazdát. További információ: [csatlakozás SQL Databasehoz Azure Active Directory hitelesítés használatával](sql-database-aad-authentication.md). Az Azure AD-hitelesítés mind a felügyelt, mind az összevont fiókokat támogatja. Az összevont fiókok támogatják a Windows-felhasználókat és-csoportokat az Azure AD-vel összevont ügyfél-tartományokhoz.

    Az elérhető további Azure AD-hitelesítési lehetőségek a SQL Server Management Studio-kapcsolatokhoz [Active Directory univerzális hitelesítés,](sql-database-ssms-mfa-authentication.md) például [multi-Factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md) és [feltételes hozzáférés](sql-database-conditional-access.md).

> [!IMPORTANT]
> Az adatbázisok és kiszolgálók Azure-beli kezelését a portál felhasználói fiókjának szerepkör-hozzárendelései vezérlik. További információ erről a cikkről: [szerepköralapú hozzáférés-vezérlés Azure Portalban](../role-based-access-control/overview.md). A tűzfalszabályok hozzáférésének szabályozása *nem* vonatkozik **a felügyelt példányokra**. Ha további információkra van szüksége a hálózati konfigurációval kapcsolatban, tekintse meg a következő cikket a [felügyelt példányhoz való csatlakozásról](sql-database-managed-instance-connect-app.md) .

## <a name="authorization"></a>Engedélyezés

Az engedélyezés egy Azure SQL Databaseon belüli felhasználóhoz rendelt engedélyekre utal, és meghatározza, hogy a felhasználó mit tehet. Az engedélyek szabályozása úgy történik, hogy felhasználói fiókokat ad hozzá az [adatbázis szerepköreihez](/sql/relational-databases/security/authentication-access/database-level-roles) , és adatbázis-szintű engedélyeket rendel hozzájuk ezekhez a szerepkörökhöz, vagy ha a felhasználó számára bizonyos [objektum-szintű engedélyeket](/sql/relational-databases/security/permissions-database-engine)ad meg. További információ: [bejelentkezések és felhasználók](sql-database-manage-logins.md)

Ajánlott eljárásként hozzon létre egyéni szerepköröket, ha szükséges. Vegyen fel felhasználókat a szerepkörbe a feladat funkciójának végrehajtásához szükséges legalacsonyabb jogosultságokkal. Ne rendeljen engedélyeket közvetlenül a felhasználóknak. A kiszolgálói rendszergazdai fiók tagja a beépített db_owner szerepkörnek, amely kiterjedt engedélyekkel rendelkezik, és csak néhány rendszergazdai feladattal rendelkező felhasználónak kell megadnia. Azure SQL Database alkalmazások esetében a [végrehajtás másként](/sql/t-sql/statements/execute-as-clause-transact-sql) beállítással adhatja meg a hívott modul végrehajtási környezetét, vagy használjon korlátozott engedélyekkel rendelkező [alkalmazás-szerepköröket](/sql/relational-databases/security/authentication-access/application-roles) . Ez a gyakorlat biztosítja, hogy az adatbázishoz csatlakozó alkalmazáshoz az alkalmazás által igényelt legalacsonyabb jogosultságok szükségesek. Az alábbi ajánlott eljárások a feladatok elkülönítését is támogatják.

### <a name="row-level-security"></a>Sorszintű biztonság

A sor szintű biztonság lehetővé teszi az ügyfelek számára, hogy a lekérdezést végrehajtó felhasználó jellemzői alapján szabályozzák az adatbázistábla soraihoz való hozzáférést (például csoporttagság vagy végrehajtási környezet). A sor szintű biztonság egyéni címkézett biztonsági fogalmak megvalósítására is használható. További információkat a [sorszintű biztonsággal kapcsolatos](/sql/relational-databases/security/row-level-security) részben találhat.

![Azure-Database-RLS. png](media/sql-database-security-overview/azure-database-rls.png)

## <a name="threat-protection"></a>Fenyegetések elleni védelem

A SQL Database a naplózási és veszélyforrás-észlelési képességek biztosításával gondoskodik az ügyféladatok biztonságáról.

### <a name="sql-auditing-in-azure-monitor-logs-and-event-hubs"></a>SQL-naplózás Azure Monitor naplókban és Event Hubs

SQL Database naplózás nyomon követi az adatbázis-tevékenységeket, és segít a biztonsági szabványoknak való megfelelés fenntartásában azáltal, hogy adatbázis-eseményeket rögzít az ügyfél által birtokolt Azure Storage-fiókban lévő naplóba. A naplózás lehetővé teszi a felhasználók számára a folyamatos adatbázis-tevékenységek figyelését, valamint a múltbeli tevékenységek elemzését és kivizsgálását a potenciális fenyegetések vagy a gyanús visszaélések és biztonsági szabálysértések azonosítása érdekében. További információ: Ismerkedés a [SQL Database naplózással](sql-database-auditing.md).  

### <a name="advanced-threat-protection"></a>Komplex veszélyforrások elleni védelem

A komplex veszélyforrások elleni védelem a SQL Server naplók elemzésével vizsgálja a szokatlan viselkedést és a potenciálisan ártalmas kísérleteket az adatbázisok eléréséhez vagy kiaknázásához. A riasztások olyan gyanús tevékenységekhez jönnek létre, mint például az SQL-injektálás, a lehetséges adatszivárgások és a találgatásos támadások, illetve a hozzáférési mintákban mutatkozó rendellenességek a jogosultság-eszkalációs és a megsértett hitelesítő adatok használata esetén. A riasztásokat a [Azure Security Center](https://azure.microsoft.com/services/security-center/)tekintik meg, ahol a gyanús tevékenységek részletei megtalálhatók, valamint a fenyegetést enyhítő műveletekkel kapcsolatos javaslatok a további vizsgálathoz. A komplex veszélyforrások elleni védelem felár ellenében engedélyezhető kiszolgálónként. További információ: Ismerkedés [a SQL Database komplex veszélyforrások elleni védelemmel](sql-database-threat-detection.md).

![Azure-Database-TD. jpg](media/sql-database-security-overview/azure-database-td.jpg)

## <a name="information-protection-and-encryption"></a>Adatvédelem és titkosítás

### <a name="transport-layer-security-tls-encryption-in-transit"></a>Transport Layer Security TLS (titkosítás átvitele)

A SQL Database a [Transport Layer Security](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server)használatával a mozgásban lévő adattitkosítással biztosítja az ügyféladatok védelmét.

Az SQL Server minden kapcsolathoz mindig kikényszeríti a titkosítást (SSL/TLS). Ez biztosítja, hogy az összes adatforgalom titkosítva legyen az ügyfél és a kiszolgáló között, függetlenül attól, hogy a **titkosítás** vagy a **TrustServerCertificate** a kapcsolati sztringben van-e beállítva.

Ajánlott eljárásként javasoljuk, hogy az alkalmazás kapcsolati karakterláncában adja meg a titkosított kapcsolatot, és _**ne**_ Bízzon meg a kiszolgálói tanúsítványban. Ez arra kényszeríti az alkalmazást, hogy ellenőrizze a kiszolgáló tanúsítványát, így megakadályozza, hogy az alkalmazás sebezhető legyen a középső típusú támadásokban lévő ember számára.

Például a ADO.NET-illesztőprogram használata esetén ez a **titkosítás = True** és a **TrustServerCertificate = false**használatával valósítható meg. Ha a Azure Portalból szerzi be a kapcsolatok karakterláncát, akkor a megfelelő beállításokkal fog rendelkezni.

> [!IMPORTANT]
> Vegye figyelembe, hogy egyes nem Microsoft-illesztőprogramok nem használhatják a TLS-t alapértelmezés szerint, vagy a TLS (< 1.2) régebbi verzióját használják a működéshez. Ebben az esetben SQL Server továbbra is lehetővé teszi az adatbázishoz való kapcsolódást. Javasoljuk azonban, hogy értékelje a biztonsági kockázatokat, amelyek lehetővé teszik, hogy az ilyen illesztőprogramok és alkalmazások csatlakozzanak a SQL Databasehoz, különösen ha bizalmas adatokat tárol. 
>
> A TLS-vel és a kapcsolattal kapcsolatos további információkért lásd: [TLS-megfontolások](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity)

### <a name="transparent-data-encryption-encryption-at-rest"></a>Transzparens adattitkosítás (titkosítás – REST)

[Transzparens adattitkosítás (TDE) for Azure SQL Database](transparent-data-encryption-azure-sql.md) egy biztonsági réteget biztosít, amellyel megvédheti az inaktív adatokat a nyers fájlokhoz vagy biztonsági másolatokhoz való jogosulatlan vagy offline hozzáféréstől. Gyakori forgatókönyvek például a Datacenter-lopás vagy a hardver vagy adathordozó nem biztonságos ártalmatlanítása, például a lemezmeghajtók és a biztonsági mentési szalagok. A TDE egy AES titkosítási algoritmussal titkosítja a teljes adatbázist, amely nem igényli, hogy az alkalmazás fejlesztői ne kelljen módosítaniuk a meglévő alkalmazásokat.

Az Azure-ban az összes újonnan létrehozott SQL-adatbázis alapértelmezés szerint titkosítva van, és az adatbázis-titkosítási kulcsot egy beépített kiszolgálótanúsítvány védi.  A tanúsítvány-karbantartási és-rotációs szolgáltatást a szolgáltatás felügyeli, és nem igényel bemenetet a felhasználótól. A titkosítási kulcsok felügyeletét előnyben részesített ügyfelek a [Azure Key Vaultban](../key-vault/key-vault-secure-your-key-vault.md)kezelhetik a kulcsokat.

### <a name="key-management-with-azure-key-vault"></a>Kulcskezelő Azure Key Vault

A [transzparens adattitkosítás](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE) [bring your own Key](transparent-data-encryption-byok-azure-sql.md) (BYOK) támogatása lehetővé teszi, hogy az ügyfelek a [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md), az Azure felhőalapú külső kulcskezelő rendszerének használatával magukénak vállaljanak a kulcskezelő és a rotációs szolgáltatásban. Ha az adatbázis a kulcstartóhoz való hozzáférését visszavonja, az adatbázist nem lehet visszafejteni és a memóriába beolvasni. A Azure Key Vault egy központi kulcskezelő platformot biztosít, amely szorosan felügyelt hardveres biztonsági modulokat (HSM-ket) használ, és lehetővé teszi a kulcsok és adatok kezelése közötti feladatok elkülönítését a biztonsági megfelelőségi követelmények kielégítése érdekében.

### <a name="always-encrypted-encryption-in-use"></a>Always Encrypted (titkosítás használatban)

![Azure-Database-AE. png](media/sql-database-security-overview/azure-database-ae.png)

A [Always encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) az adott adatbázis-oszlopokban tárolt bizalmas adatok hozzáférésének (például a hitelkártya-számok, a nemzeti azonosító számok vagy az adatok _ismerete_ alapján) való védelme. Ebbe beletartozik az adatbázis-rendszergazdák vagy más, jogosultsággal rendelkező felhasználók, akik jogosultak az adatbázis elérésére a felügyeleti feladatok elvégzéséhez, de nincs szükségük üzleti tevékenységre a titkosított oszlopokban lévő adatok eléréséhez. A rendszer mindig titkosítja az adattitkosítást, ami azt jelenti, hogy a titkosított adatforgalom csak olyan ügyfélalkalmazások általi feldolgozásra van visszafejtve, amelyek hozzáférnek a titkosítási kulcshoz.  A titkosítási kulcs soha nem érhető el az SQL-ben, és a [Windows tanúsítványtárolóban](sql-database-always-encrypted.md) vagy [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md)tárolható.

### <a name="dynamic-data-masking"></a>Dinamikus adatmaszkolás

![Azure-Database-DDM. png](media/sql-database-security-overview/azure-database-ddm.png)

SQL Database dinamikus adatmaszkolás korlátozza a bizalmas adatokkal való érintkezést azáltal, hogy a nem Kiemelt felhasználók számára maszkolást végez. A dinamikus adatmaszkolás automatikusan felfedi a Azure SQL Database potenciálisan bizalmas adatokat, és gyakorlati ajánlásokat biztosít ezen mezők maszkolásához, minimális hatással az alkalmazási rétegre. Rejtjelezi a bizalmas adatokat egy kijelölt adatbázismezőkön végrehajtott lekérdezés eredményhalmazában, miközben az adatbázis adatait nem módosítja. További információ: Ismerkedés [a SQL Database dinamikus adatmaszkolásával](sql-database-dynamic-data-masking-get-started.md).

## <a name="security-management"></a>Biztonságkezelés

### <a name="vulnerability-assessment"></a>Sebezhetőségi felmérés

A [sebezhetőségi felmérés](sql-vulnerability-assessment.md) egy könnyen konfigurálható szolgáltatás, amely felderítheti, nyomon követheti és javíthatja a lehetséges adatbázis-réseket azzal a céllal, hogy proaktív módon javítsa a teljes adatbázis-biztonságot. A biztonsági rések felmérése (VA) a fejlett adatbiztonsági (ADS) ajánlat része, amely a speciális SQL-alapú biztonsági funkciók egységes csomagja. A sebezhetőségi felmérés a központi SQL ADS portálon keresztül érhető el és kezelhető.

### <a name="data-discovery--classification"></a>Adatfelderítés és besorolás

Az adatfelderítési & besorolása (jelenleg előzetes verzióban érhető el) olyan speciális képességeket biztosít, amelyek a Azure SQL Database beépített, a bizalmas adatok felderítéséhez, besorolásához, címkézéséhez és védelméhez szükségesek. A rendkívül bizalmas adatok (üzleti/pénzügyi, egészségügyi, személyes adatok stb.) felfedése és besorolása kulcsfontosságú szerepet játszik a szervezeti adatok védelmében. Infrastruktúraként alkalmas lehet az alábbiakra:

- Különböző biztonsági forgatókönyvek, például a figyelés (naplózás) és a bizalmas adatok rendellenes hozzáférésének riasztása.
- A szigorúan bizalmas adatokat tartalmazó adatbázisok hozzáférésének szabályozása, és a biztonság megerősítése.
- Segíthet megfelelni az adatvédelmi szabványoknak és a szabályozási megfelelőség követelményeinek.

További információ: Ismerkedés [az adatfelderítési & besorolással](sql-database-data-discovery-and-classification.md).

### <a name="compliance"></a>Megfelelőség

A fenti szolgáltatások és funkciók mellett, amelyek segíthetnek az alkalmazásnak a különböző biztonsági követelmények teljesítésében, Azure SQL Database is részt vesz a rendszeres auditálásokban, és számos megfelelőségi szabványnak megfelelő minősítéssel rendelkezik. További információkért tekintse meg a [Microsoft Azure adatvédelmi központot](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) , ahol megtalálhatja a SQL Database megfelelőségi minősítések legfrissebb listáját.

## <a name="next-steps"></a>Következő lépések

- Az SQL Database említett hozzáférés-vezérlési funkcióinak használatával kapcsolatban lásd a [hozzáférés-vezérléssel](sql-database-control-access.md) foglalkozó témakört.
- Az adatbázis-naplózással kapcsolatos megbeszélések: [SQL Database naplózás](sql-database-auditing.md).
- A veszélyforrások észlelésével kapcsolatban lásd: [SQL Database veszélyforrások észlelése](sql-database-threat-detection.md).
