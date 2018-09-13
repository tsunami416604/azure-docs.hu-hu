---
title: Azure SQL Database – az áttelepítés után kezelése |} A Microsoft Docs
description: Megtudhatja, hogyan kezelheti az adatbázis áttelepítése az Azure SQL Database után.
services: sql-database
author: joesackmsft
manager: craigg
ms.service: sql-database
ms.custom: migrate
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: josack
ms.suite: sql
ms.prod_service: sql-database
ms.component: data-movement
ms.openlocfilehash: d82cc3ee1074e326c9e4dee7fd65e338cb95e19f
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "44722231"
---
# <a name="new-dba-in-the-cloud--managing-your-database-in-azure-sql-database"></a>Új adatbázis a felhőben – Azure SQL Database adatbázis

Való áttérés a hagyományos önállóan felügyelt, önálló ellenőrzött környezetben, egy PaaS-környezetben is kezdünk, egy kicsit először. Az alkalmazás fejlesztője vagy az adatbázis a platform, amely segít az, hogy az alkalmazás elérhető, nagy teljesítményű, biztonságos és rugalmas - legfontosabb funkcióit mindig tudni érdemes. Ez a cikk célja, hogy ezt pontosan. A cikk röviden rendezi az erőforrásokat, és a legjobb használatának legfontosabb funkciói az SQL Database kezelése és hatékonyan futtató alkalmazás megtartása és a felhőben az optimális eredmények elérése útmutatást biztosít. Ez a cikk tipikus közönségének lenne azokat, akik: 
- Az Azure SQL DB – az alkalmazások korszerűsítése társított alkalmazásokban való migrálásának értékelése.
- Saját alkalmazás – folyamatos áttelepítési forgatókönyvben való migrálása folyamatban van.
- Nemrégiben fejeződtek be az áttelepítést az Azure SQL DB – új adatbázis a felhőben.

Ez a cikk ismerteti az alapvető előrejelzéséhez, az Azure SQL DB platform, amely könnyen használhatja. A következők:- 
- Üzleti folytonosság és vészhelyreállítás helyreállítási (BCDR)
- Biztonság és megfelelőség
- Intelligens adatbázis figyelése és karbantartása
- Adatáthelyezés

## <a name="business-continuity-and-disaster-recovery-bcdr"></a>Üzleti folytonosság és vészhelyreállítás helyreállítási (BCDR)
Üzleti folytonossági és vészhelyreállítási helyreállítási képességek lehetővé teszik az üzleti szokásos módon folytatja egy esetleges vészhelyzet esetén. A vészhelyreállítási lehet egy adatbázis-szintű esemény (Ha például valaki véletlenül csökken kulcsfontosságú tábla) vagy egy adat-központ szintű esemény (regionális katasztrófa, például egy tsunami). 

### <a name="how-do-i-create-and-manage-backups-on-sql-database"></a>Hogyan hozzon létre és kezelhető a biztonsági mentés az SQL Database?
Az Azure SQL Database biztonsági mentések nem hoz létre, és nem kell azért van, mert. Az SQL Database automatikusan adatbázisok biztonsági mentését, így Ön már nem kell aggódnia ütemezés, véve, és a biztonsági másolatok kezelése. A platform egy teljes biztonsági mentés hetente, különbözeti biztonsági mentési néhány óránként és a egy napló biztonsági mentését, hogy a vész-helyreállítási e hatékony, 5 percenként, és minimális az adatvesztés vesz igénybe. Az első teljes biztonsági mentés akkor történik meg, amint egy adatbázist hoz létre. Ezeket a biztonsági másolatokat lesznek elérhetők a "megőrzési időtartam" nevű bizonyos ideig, és úgy dönt, a teljesítményszint függvénye.  Az SQL Database lehetővé teszi a belül a megőrzési időszak segítségével bármely időpontra visszaállítása [mutasson az idő Recovery (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore).

|Teljesítményszint|Megőrzési időszak napban|
|---|:---:|
|Alapszintű|7|
|Standard|35|
|Prémium|35|
|||

Emellett a [hosszú távú adatmegőrzést (LTR)](sql-database-long-term-retention.md) funkció lehetővé teszi a megőrzendő a biztonsági másolat sokkal hosszabb ideig, akár 10 évig, és ezeket a biztonsági másolatokat bármikor adatainak visszaállítása ezen időszakon belül. Továbbá az adatbázis biztonsági mentéséhez őrzi meg a egy georeplikált tárolása a regionális katasztrófa rugalmasság biztosítása érdekében. Ezeket a biztonsági másolatokat a megőrzési időtartamon belül bármikor, bármely Azure-régióban is helyreállíthatja. Lásd: [üzleti folytonosság – áttekintés](sql-database-business-continuity.md).

### <a name="how-do-i-ensure-business-continuity-in-the-event-of-a-datacenter-level-disaster-or-regional-catastrophe"></a>Hogyan biztosítja a üzletmenet-folytonossági egy adatközpont-szintű vészhelyreállítás vagy regionális katasztrófa esetén?
Mivel az adatbázisok biztonsági másolatai egy georeplikált tárolóalrendszer annak érdekében, hogy egy regionális katasztrófa esetén tárolódnak, visszaállíthatja a biztonsági mentés egy másik Azure-régióba. Ezt nevezzük a geo-visszaállítás. A helyreállítási Időkorlát (Helyreállításipont-célkitűzés) alapvetően < 1 óra és a ERT (becsült helyreállítási idő – néhány percet, órát).

Az alapvető fontosságú adatbázisok Azure SQL DB kínál, aktív georeplikációt. Ez lényegében célja, hogy létrehoz egy georeplikált másodlagos az eredeti adatbázis másolatát egy másik régióban. Például ha az adatbázis kezdetben üzemel az Azure USA nyugati régiójában, és azt szeretné, hogy a regionális katasztrófákkal. Tegyük fel, USA keleti RÉGIÓJA, USA nyugati adatközpontjába az adatbázis aktív georedundáns replikájának hozna létre. A calamity feladatokat, az USA nyugati RÉGIÓJA, az USA keleti régiójában feladatátvételt is. Konfigurálja őket az automatikus feladatátvételi csoportok még jobb, mert ez biztosítja, hogy az adatbázis automatikusan átadja a feladatokat a másodlagos egy esetleges vészhelyzet esetén az USA keleti régiójában. Ezzel az RPO < 5 másodperc és a ERT < 30 másodperc.

Ha nincs konfigurálva az automatikus feladatátvételi csoportot, majd az alkalmazásnak kell aktívan figyeli, hogy katasztrófa, és kezdeményezzen feladatátvételt a másodlagos. Különböző Azure-régióban létrehozhat akár 4 ilyen aktív geo-replikák. Még jobb kap. A másodlagos aktív georedundáns-replikái csak olvasási hozzáféréssel is elérhető. Ezek akkor bizonyulnak nagyon földrajzilag elosztott alkalmazás esetén a késés csökkentése érdekében. 

### <a name="how-does-my-disaster-recovery-plan-change-from-on-premises-to-sql-database"></a>Hogyan saját vész-helyreállítási terv változik a helyszínről az SQL Database?
Összefoglalva a hagyományos helyszíni SQL Server-telepítés szükséges aktívan szolgáltatások, például feladatátvételi fürtözés, adatbázis-tükrözés, tranzakciós replikáció, Log szállítási stb. a rendelkezésre állás kezelése és fenntartása, és annak biztosítása érdekében a biztonsági másolatok kezelése Üzletmenet-folytonossági. Az SQL Database a platform kezeli ezeket, így fejlesztéséhez és az adatbázis-alkalmazás optimalizálására koncentrálhat, és nem kell foglalkoznia vészhelyreállítási felügyeleti annyi. Akkor is a biztonsági mentési és vész-helyreállítási tervek konfigurált és működő mindössze néhány kattintással az Azure Portalon (vagy néhány parancsot a PowerShell API-k használatával). 

Vész-helyreállítási kapcsolatos további információkért lásd: [Azure SQL Db katasztrófa utáni helyreállítás 101](https://azure.microsoft.com/blog/azure-sql-databases-disaster-recovery-101/)

## <a name="security-and-compliance"></a>Biztonság és megfelelőség
Az SQL Database nagyon komolyan veszi biztonság és adatvédelem. SQL Database biztonsági az adatbázis szintjén, és a platform szintjén érhető el, és a legjobb értendő, amikor több rétegekbe kategorizált. Minden egyes rétegben juthat el a vezérlőelemet, és adja meg az alkalmazás optimális biztonság biztosítása. A szintek a következők:
- Identitás és hitelesítés ([Windows/SQL-hitelesítés és Azure Active Directory [AAD] hitelesítési](sql-database-control-access.md)).
- Tevékenység figyelése ([naplózási](sql-database-auditing.md) és [fenyegetésészlelés](sql-database-threat-detection.md)).
- Tényleges adatok védelmét ([transzparens adattitkosítás [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) és [Always Encrypted [AE]](/sql/relational-databases/security/encryption/always-encrypted-database-engine)). 
- Bizalmas és a védett adatokhoz való hozzáférés szabályozása ([sorszintű biztonság](/sql/relational-databases/security/row-level-security) és [dinamikus Adatmaszkolás](/sql/relational-databases/security/dynamic-data-masking)).

[Az Azure Security Center](https://azure.microsoft.com/services/security-center/) lehetővé teszi a központi kezelését az Azure-ban, a helyszínen, és más felhőkben futó számítási feladatok között. Megtekintheti, hogy ez a alapvető SQL-adatbázis védelmét, például [naplózási](sql-database-auditing.md) és [transzparens adattitkosítás [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) , és saját igényei alapján szabályzatokat hozhat létre az összes erőforrás van beállítva.

### <a name="what-user-authentication-methods-are-offered-in-sql-database"></a>Az SQL Database felhasználói hitelesítési módszerek érhetők el?
Nincsenek [két hitelesítési módszerek](sql-database-control-access.md#authentication) SQL Database-ben érhető el: 
- [Az Azure Active Directory-hitelesítés](sql-database-aad-authentication.md)
- SQL-hitelesítés. 

A hagyományos windows-hitelesítés nem támogatott. Az Azure Active Directory (AD) egy olyan központi identitás- és hozzáférés a felügyeleti szolgáltatás. Ez nagyon kényelmesen megadhat egy egyszeri bejelentkezéses hozzáférést (SSO), a személyzet összes a szervezetben. Ez azt jelenti, hogy a hitelesítő adatok meg vannak osztva az összes Azure-szolgáltatások egyszerűbb hitelesítéshez. Támogatja az AAD [többtényezős hitelesítés (többtényezős hitelesítést)](sql-database-ssms-mfa-authentication.md) és a egy-egy [néhány kattintással](../active-directory/connect/active-directory-aadconnect-get-started-express.md) AAD integrálható a Windows Server Active Directoryval. SQL-hitelesítés már használja egy, az elmúlt ugyanúgy működik. Felhasználónevet/jelszót ad meg, és hitelesítheti a felhasználókat egy adott logikai kiszolgáló bármely olyan adatbázisába. Ez lehetővé teszi az SQL Database és az SQL Data Warehouse kínálta a multi-factor authentication és a Vendég felhasználói fiókok Azure AD-tartomány belül. Ha már rendelkezik egy Active Directory a helyszíni, meg is összevonható a címtár az Azure Active Directoryval, a címtár kiterjesztése az Azure-bA.

|**Ha Ön...**|**Az SQL Database / SQL Data Warehouse**|
|---|---|
|Nem szeretné használni az Azure Active Directory (AD) az Azure-ban|Használat [SQL-hitelesítés](sql-database-security-overview.md)|
|Helyszíni SQL Server-kiszolgálón használt AD|[Az Azure ad-vel AD összevonása](../active-directory/connect/active-directory-aadconnect.md), és az Azure AD-hitelesítés használatára. Ehhez használhatja, egyszeri bejelentkezést.|
|Többtényezős hitelesítés (MFA) kell|Többtényezős hitelesítés – házirend- [feltételes hozzáférés a Microsoft](sql-database-conditional-access.md), és használja [MFA-támogatással az Azure AD univerzális hitelesítéssel](sql-database-ssms-mfa-authentication.md).|
|A Microsoft-fiókok (live.com, Outlook.com-os) vagy más tartományok (gmail.com) vendégfiókok rendelkezik|Használat [Azure AD univerzális hitelesítéssel](sql-database-ssms-mfa-authentication.md) az SQL Database/Data Warehouse, amely kihasználja [Azure AD B2B együttműködés](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).|
|Az Azure AD hitelesítő adatok használatával összevont tartományok Windows jelentkezett be|Használat [integrált Azure AD-hitelesítés](sql-database-aad-authentication-configure.md).|
|A rendszer naplózza Windows hitelesítő adatok használatával az Azure-ral nem összevont tartományok|Használat [integrált Azure AD-hitelesítés](sql-database-aad-authentication-configure.md).|
|Középső rétegű szolgáltatásokat, amelyek az SQL Database vagy az SQL Data Warehouse csatlakoznia kell rendelkeznie|Használat [integrált Azure AD-hitelesítés](sql-database-aad-authentication-configure.md).|
|||

### <a name="how-do-i-limit-or-control-connectivity-access-to-my-database"></a>Hogyan korlátozza és szabályozhatja az adatbázis kapcsolati hozzáférés?
Nincsenek több technikák a rendelkezésére, amelyek rosszabb csatlakozási szervezet esetében az alkalmazás elérésére használhat. 
- Tűzfalszabályok
- Virtuális hálózati Szolgáltatásvégpontok
- Fenntartott IP-címek

#### <a name="firewall"></a>Tűzfal
A tűzfal megakadályozza, hogy hozzáférést a kiszolgálóhoz egy külső entitás azáltal, hogy csak adott entitásokhoz a hozzáférést a logikai kiszolgáló. Alapértelmezés szerint az összes kapcsolat és a logikai kiszolgálón található adatbázisok nem engedélyezettek, más Azure-szolgáltatásoktól érkező kapcsolatokat kivéve. A tűzfalszabály nyissa meg hozzáférés csak az entitások (például a fejlesztői gépen), amelyek azáltal, hogy a számítógép IP-cím a tűzfalon keresztül, jóváhagyása a kiszolgálóhoz. Azt is lehetővé teszi, hogy adjon meg egy IP-címtartományt, amely annak a logikai kiszolgálóhoz való hozzáférés engedélyezése. Például a szervezet fejlesztői gép IP-címek is hozzáadhatók egyszerre egy tartományt a tűzfalbeállítások oldal megadásával. 

A kiszolgálószintű vagy adatbázisszintű tűzfalszabályok létrehozhat. Kiszolgálói szintű tűzfalszabályokat is jött létre az ssms-t vagy a portálon keresztül. Tanulás bővebben tájékozódhat a kiszolgáló és adatbázis-szintű tűzfalszabályt beállítani, lásd: [tűzfalszabályok létrehozása az SQL Database](sql-database-security-tutorial.md#create-a-server-level-firewall-rule-in-the-azure-portal).

#### <a name="service-endpoints"></a>Szolgáltatásvégpontok
Alapértelmezés szerint az SQL-adatbázis a "engedélyezi az Azure-szolgáltatásokhoz" – ami azt jelenti, hogy bármelyik virtuális gépet az Azure-ban előfordulhat, hogy próbál kapcsolódni az adatbázishoz. Ezek a próbálkozások továbbra is kell hitelesítik. Azonban ha nem szeretne minden olyan Azure IP-címek által lesznek elérhetők az adatbázist, letilthatja a "Az összes Azure-szolgáltatások engedélyezése". Ezenkívül konfigurálhatja [virtuális hálózati Szolgáltatásvégpontok](sql-database-vnet-service-endpoint-rule-overview.md).

Szolgáltatásvégpontok (SE) lehetővé teszik, hogy a kritikus fontosságú Azure-erőforrások csak a saját privát virtuális hálózatot az Azure-ban. Ezzel a módszerrel lényegében letiltjuk a nyilvános hozzáférést az erőforrásokhoz. Az Azure virtuális hálózatok között a forgalmat az Azure gerinchálózatán marad. Anélkül, hogy ki kap az adatcsomag útválasztását kényszerített bújtatást végez. A virtuális hálózat kényszeríti a szervezet internetes forgalmat és az Azure szolgáltatás forgalmát haladnak át ugyanazon útvonal. A Szolgáltatásvégpontok használatakor optimalizálhatja ez óta a csomagok folyamat rögtön a virtuális hálózatról az Azure gerinchálózatán szolgáltatáshoz.

![Virtuális hálózati Szolgáltatásvégpontok](./media/sql-database-manage-after-migration/vnet-service-endpoints.png) 

#### <a name="reserved-ips"></a>Fenntartott IP-címek
Egy másik lehetőség az, hogy kiépítése [fenntartott IP-címek](../virtual-network/virtual-networks-reserved-public-ip.md) a virtuális gépek és az engedélyezési lista azokat adott virtuális gép IP-címekre a kiszolgáló tűzfalbeállítások. Fenntartott IP-címek hozzárendelése, mentse a nem szükséges frissíteni a tűzfalszabályok az IP-címének módosítása.

### <a name="what-port-do-i-connect-to-sql-database-on"></a>Melyik porton létesíthetek kapcsolatot az SQL Database-be?

1433-as porton. Az SQL Database ezen a porton keresztül kommunikál. Csatlakozhat a vállalati hálózaton belülről, fel kell vennie az kimenő szabályt a szervezet tűzfalbeállításainál. Iránymutatásként kerülje a 1433-as port közvetlenül az Azure határain kívülre. SSMS az Azure-ban futtatható [Azure RemoteApp](https://www.microsoft.com/cloud-platform/azure-remoteapp-client-apps). Nem igényel, hogy nyissa meg a 1433-as port a kimenő kapcsolatokat, az IP-cím statikus, ezért lehet, hogy az adatbázis csak a RemoteApp megnyitása és Multi Factor Authentication (MFA) támogatja.

### <a name="how-can-i-monitor-and-regulate-activity-on-my-server-and-database-in-sql-database"></a>Hogyan monitorozhatom és szabályozzák a tevékenység a saját kiszolgáló és az SQL Database-adatbázis?
#### <a name="sql-database-auditing"></a>Az SQL Database naplózási szolgáltatásával
Az SQL Database bekapcsolhatja a naplózást az adatbázissal kapcsolatos események nyomon követéséhez. [Az SQL Database naplózási szolgáltatásával](sql-database-auditing.md) rögzíti az adatbázisok eseményeit, és az Azure Storage-fiók egy naplózási naplófájlba írja őket. Naplózás funkció különösen akkor hasznos, ha azt tervezi, hogy betekintést nyerhet a potenciális biztonsági és a szabályzat megsértése miatt, szabályozásoknak való megfelelőség stb. Lehetővé teszi, hogy meghatározása és konfigurálása az egyes kategóriák eseményeket, amelyek úgy gondolja, hogy kell a naplózást és az alapján, hogy előre konfigurált jelentéseket és a egy irányítópultot, hogy az adatbázisban előforduló eseményeket áttekintést kaphat. Ezek a kiszolgálószintű vagy adatbázisszintű naplózási házirendek is alkalmazhat. A server/database Auditing szolgáltatása bekapcsolása útmutató, lásd: [engedélyezése az SQL Database Auditing](sql-database-security-tutorial.md#enable-sql-database-auditing-if-necessary).

#### <a name="threat-detection"></a>Fenyegetésészlelés
A [fenyegetésészlelés](sql-database-threat-detection.md), lehetővé teszi számára, hogy nagyon egyszerűen naplózás által észlelt biztonsági vagy házirend megsértésének kap. Nem kell lenniük egy biztonsági szakértői, a rendszer potenciális fenyegetések vagy szabálysértések megoldása érdekében. A fenyegetésészlelés is rendelkezik néhány beépített képességek, mint az SQL-injektálás felderítése. SQL-injektálás alter vagy veszélyeztetheti az adatokat és a egy adatbázis-alkalmazás intézményt általában elég általános módja. Az SQL Database Threat Detection algoritmusokban, ami észleli a potenciális biztonsági réseket és SQL-injektálásos támadásokról, valamint a rendellenes adatbázis-hozzáférési mintákról (például hozzáférés szokatlan helyről vagy résztvevő részéről) több példányban fut. Biztonsági tisztviselők, vagy más kijelölt rendszergazdák e-mailben értesítést kapni, fenyegetés észlelésekor az adatbázison. Minden értesítést a gyanús tevékenységeket és ajánlások részleteit tartalmazza a vonatkozó további vizsgálata és a fenyegetés. Megtudhatja fenyegetésészlelés bekapcsolása, lásd: [engedélyezése az SQL Database Threat Detection](sql-database-security-tutorial.md#enable-sql-database-threat-detection). 
### <a name="how-do-i-protect-my-data-in-general-on-sql-database"></a>Hogyan védhetem az adatok általában az SQL Database?
Titkosítás lehetővé teszi a erős védelemmel való ellátásához és a bizalmas adatok védelme a érdeklődhetnek. A titkosított adatokat az illetéktelen személyek a visszafejtési kulcs nélkül a nem használt van. Ebből kifolyólag hozzáadja egy extra réteg felül a meglévő SQL Database beépített biztonsági rétegek védelmét. Két szempontot kell SQL Database-ben az adatok védelmét: 
- Az adatok inaktív az adathoz és naplófájlhoz a 
- Az adatok átvitel közben. 

SQL Database-ben alapértelmezés szerint az adatok inaktív állapotban az adathoz és naplófájlhoz be a tárolási alrendszertől mindig és teljes mértékben titkosított keresztül [transzparens adattitkosítás [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). A biztonsági másolatok is titkosítva van. A TDE nem változtak az alkalmazás oldalán, hogy az adatok elérésének van szükség. A titkosítási és visszafejtési fordulhat elő, átlátható módon; ezért a neve. Az átvitel alatt bizalmas adatok védelmére és a tárolt SQL Database által biztosított szolgáltatás [mindig titkosított (AE)](/sql/relational-databases/security/encryption/always-encrypted-database-engine). AE az egy képernyő, ügyféloldali titkosítás, amely titkosítja a kényes oszlopokat az adatbázisban (tehát az adatbázis-rendszergazdák és a jogosulatlan felhasználók ciphertext vannak). A kiszolgáló először kap a titkosított adatokat. A kulcs az Always Encrypted is tárolja az ügyféloldalon, így csak az arra jogosult ügyfelek vissza tudja fejteni a bizalmas oszlopokat. A kiszolgáló és az adatok a rendszergazdák nem láthatják a bizalmas adatokat, mivel a titkosítási kulcsok tárolási az ügyfélen. AE titkosítja a kényes oszlopok a tábla teljes körű, az ügyfelek nem engedélyezett a fizikai lemez. AE jelenleg támogatja egyenlőségi összehasonlítást, így az adatbázisok lekérdezése a titkosított oszlopokat az SQL-parancsokat részeként továbbra is. Mindig titkosított használható különböző kulcstároló lehetőségeket, mint például [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md), Windows-tanúsítványtároló és a helyi hardveres biztonsági modulok.

|**Jellemzők**|**Always Encrypted**|**Transzparens adattitkosítás**|
|---|---|---|
|**Titkosítás az időtartományt**|Végpontok közötti|Inaktív adatok|
|**Adatbázis-kiszolgáló hozzáférhet bizalmas adatokhoz**|Nem|Igen, mivel az inaktív adatok titkosítása|
|**Engedélyezett a T-SQL-műveletek**|Közötti egyenlőségi összehasonlítás|Az összes T-SQL-támadási érhető el|
|**A szolgáltatás használatához szükséges alkalmazás módosításai**|Minimális|Minimális|
|**Titkosítási részletessége**|Oszlop szint|Adatbázis-szint|
||||

### <a name="how-can-i-limit-access-to-sensitive-data-in-my-database"></a>Hogyan lehet korlátozni a saját adatbázis bizalmas adatokhoz való hozzáférést?
Minden alkalmazás rendelkezik egy bizonyos bit a bizalmas adatok az adatbázisban, amelyek nem látható a mindenki számára kell védeni. Egyes munkatársak, a szervezeten belül kell, hogy az adatok megtekintésére, azonban mások nem szabadna az adatok megtekintésére. Ez egy alkalmazott bér. Egy alkalmazott felettesétől az azonban azt közvetlen beosztottak bérének hozzáférést kell, egyéni hozzáférése nem szabad a társaknak bérének. Egy másik helyzet lehet adatokat fejlesztőknek szól, akik bizalmas adatok használata a fejlesztési szakaszban, illetve tesztelési, például az ügyfelek taj számok esetében. Ezt az információt újra ki vannak téve a fejlesztő nem szükséges. Ezekben az esetekben a bizalmas adatok vagy maszkolva vagy kell nem lesz elérhető. SQL Database által nyújtott két az ilyen megközelítés képes arra, hogy az érzékeny adatokat a jogosulatlan felhasználók megakadályozása:

[A dinamikus Adatmaszkolás](sql-database-dynamic-data-masking-get-started.md) adatok maszkolási szolgáltatása, amely lehetővé teszi, hogy a bizalmas adatok elérésének korlátozását azáltal, hogy adatmaszkolás segít Önnek az alkalmazásrétegre nem kiemelt jogosultságú felhasználók. Megadhat egy maszkolási szabályra, amely egy maszkolási mintát hozhat létre (például a show csak az utolsó négy számjegye egy nemzeti azonosító SSN: XXX-XX-0000, és jelölje meg a legtöbb, Xs) és azonosítani, hogy mely felhasználók tartoznak a maszkolási szabály alól kizárandó. A maszkolás történik, a működés közbeni és érhetők el különböző maszkolási különböző adatkategóriák. Dinamikus adatmaszkolás automatikusan észleli a bizalmas adatokat az adatbázisban, és maszkolási vonatkozik teszi lehetővé.

[Sorszintű biztonság](/sql/relational-databases/security/row-level-security) segítségével szabályozható a hozzáférés sorszinten. Ami azt jelenti, egy adatbázistábla soraihoz, a felhasználó a lekérdezés (csoporttagság vagy végrehajtási környezet) alapján meghatározott sorok rejtve maradnak. A hozzáférés-korlátozás történik, az adatbázisszint egy alkalmazás szinten egyszerűsítése érdekében az alkalmazáslogika helyett. Első lépésként egy szűrőpredikátumnak egy struktúraoszlopot, szűri ki a sorokat, amelyek nem érhetők el, és a biztonsági szabályzat következő meghatározása ki férhet hozzá a sorok létrehozásával. Végül a végfelhasználó a lekérdezés futtatása és, attól függően, a felhasználó jogosultságát, vagy korlátozott sorokat megtekintéséhez, vagy megtekintheti őket egyáltalán nem.

### <a name="how-do-i-manage-encryption-keys-in-the-cloud"></a>Hogyan kezelhetem a titkosítási kulcsokat a felhőben?

Nincsenek a kulcskezelési beállításokat az Always Encrypted (ügyféloldali titkosítás) és transzparens adattitkosítási (inaktív adatok titkosítását). Javasolt rendszeresen titkosítási kulcsok megváltoztatása. Elforgatás gyakoriságát a belső szervezeti szabályozásoknak és a megfelelőségi követelményeket kell igazítani.

**Transzparens adattitkosítás (TDE)**: hierarchia két kulcs van a TDE – szimmetrikus AES-256 adatbázis egyedi adatbázis-titkosítási kulcs (adattitkosítási kulcsot), amely viszont van titkosítva, egy kiszolgáló egyedi aszimmetrikus RSA titkosítja az adatokat az egyes felhasználói adatbázisokban 2048 bites kulcs. A főkulcs lehet felügyelt vagy:
- Automatikusan, a platform - SQL-adatbázis.
- Által használatával, illetve [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md) mint a kulcstároló.

Alapértelmezés szerint a transzparens adattitkosítás főkulcsot kezeli az SQL Database szolgáltatás kényelmi célokat szolgál. Ha a szervezet szeretné szabályozni a fő kulcsot, nincs lehetőség az Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md) használja, mint a kulcstároló. Az Azure Key Vault használatával a szervezet feltételezi, hogy kézben kulcs kiépítése, rotálását és engedély szabályozza. [Elforgatás vagy a TDE főkulcs típusát Váltás](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql-key-rotation) gyors,, csak újra titkosítja az adattitkosítási kulcsot. Szerepkörök, biztonsági és adatkezelési közötti szétválasztása használó szervezetek számára a biztonsági rendszergazda üzembe helyezése az Azure Key Vaultban TDE főkulcs megosztottkulcs-anyag volt, és adja meg egy Azure Key Vault-kulcs azonosítója használandó adatbázis-rendszergazda számára titkosítás inaktív állapotban a kiszolgálón. A Key Vault célja, hogy Microsoft nem tekintheti meg és bontsa ki a titkosítási kulcs. Emellett beszerzése a szervezet számára kulcsok egy központi kezelésére. 

**Always Encrypted**: is egy [két kulcs hierarchia](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted) az Always Encrypted – bizalmas adatokat tartalmazó oszlop van titkosítva, egy AES 256 oszloptitkosítási kulcs (CEK), amely viszont egy oszlop főkulccsal (CMK) van titkosítva. Az ügyfél-illesztőprogramok az Always Encrypted megadott nincs korlátozás hossza CMKs rendelkezik. A CEK titkosított értékét az adatbázisban tárolja, és a CMK megbízható kulcstároló, például a Windows tanúsítvány Store, az Azure Key Vault vagy egy hardveres biztonsági modulban tárolja. 
- Mindkét a [CEK és CMK](/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell) forgatható. 
- CEK-rotációs egy adatművelet méretét, és képes lehet a táblák méretétől függően időigényes tartalmazó a titkosított oszlopokat. Ezért fontos körültekintő CEK cserélgetésére tervezését. 
- CMK forgatását, azonban ne zavarja meg az adatbázis teljesítményét, és elválasztott szerepkörökkel teheti meg.
Az alábbi ábrán az oszlop főkulcsok a kulcstároló beállításai láthatók az Always Encrypted

![Mindig titkosított CMK tárolására szolgáltatók](./media/sql-database-manage-after-migration/always-encrypted.png)

### <a name="how-can-i-optimize-and-secure-the-traffic-between-my-organization-and-sql-database"></a>Hogyan optimalizálása és biztonságossá tétele a saját szervezet és az SQL Database közötti adatforgalom?
A szervezet és a SQL Database közötti hálózati forgalom általában akkor irányítva a nyilvános hálózaton keresztül. Azonban ha ezt az elérési utat optimalizálása, és biztonságosabbá teszi választja, megkeresheti az Express Route. Az expressz útvonal lényegében lehetővé teszi, hogy a vállalati hálózat kiterjesztheti az Azure platform egy privát kapcsolaton keresztül. Ezzel a módszerrel nem haladnak át a nyilvános interneten. Nagyobb biztonságot, a megbízhatóság és az Útválasztás optimalizálása, hogy a rendszer lefordítja arra az alacsonyabb hálózati késések is kap, és sokkal nagyobb sebességre képes, mint általában lenne élmény, a nyilvános interneten keresztül történik. Ha azt tervezi, az adatok az jelentős adattömb átvitele a szervezet és az Azure között, Express Route használatával is árelőnyökkel. Választhat a kapcsolat három különböző kapcsolati modellek a szervezet az Azure-bA: 
- [Cloud Exchange közös elhelyezés](../expressroute/expressroute-connectivity-models.md#CloudExchange)
- [Bármely](../expressroute/expressroute-connectivity-models.md#IPVPN)
- [Point-to-Point](../expressroute/expressroute-connectivity-models.md#Ethernet)

Express Route lehetővé teszi akár 2-a sávszélességre vonatkozó korlátját, nem kell külön fizetni megvásárlása eszközcsomag is. Egyben közötti terület kapcsolat expressroute használatával konfigurálható. ER-kapcsolatszolgáltatók listájának megtekintéséhez lásd: [Express Route-partnerek és Társkommunikációs helyek](../expressroute/expressroute-locations.md). Az alábbi cikkek részletesen ismertetik a Express Route:
- [Az Expressroute bemutatása](../expressroute/expressroute-introduction.md)
- [Előfeltételek](../expressroute/expressroute-prerequisites.md)
- [Munkafolyamatok](../expressroute/expressroute-workflows.md)

### <a name="is-sql-database-compliant-with-any-regulatory-requirements-and-how-does-that-help-with-my-own-organizations-compliance"></a>Az SQL Database minden olyan szabályozási követelményeknek megfelelő, és hogyan, amely segít a saját szervezet megfelelőségéről?
SQL Database egy széles körű szabályozásoknak megfelelő. Szeretné megtekinteni, hogy teljesül-e megfelelőségre legfrissebb gyűjteményét, látogasson el a [Microsoft Trust Center](https://microsoft.com/en-us/trustcenter/compliance/complianceofferings) és a megfelelőségre, megtekintheti, ha az SQL Database tartalmazza a megfelelő Azure-szolgáltatásokat a szervezet számára fontos részletes keresztül. Fontos megjegyezni, hogy bár az SQL Database előfordulhat, hogy minősítéssel kell egy megfelelő szolgáltatás, akkor célszerű a szervezeti szolgáltatás a megfelelőségi, de nem automatikusan garantálja, hogy.

## <a name="intelligent-database-monitoring-and-maintenance-after-migration"></a>Intelligens adatbázis figyelését és karbantartását az áttelepítés után

Miután az adatbázis áttelepítése az SQL Database, monitorozásához az adatbázishoz (például az erőforrás-használat ugyanúgy történik, hogy ellenőrizze a vagy a DBCC ellenőrzi) szeretne, és rendszeres karbantartási feladatok (például építse újra, vagy reorganize indexeli, statisztikai stb.). Szerencsére a SQL Database egy intelligens abban az értelemben, hogy az a korábbi trendek és a rögzített metrikák és a statisztikák segítségével proaktív módon segíthetnek megfigyelni és az adatbázis karbantartása érdekében, hogy az alkalmazás optimális mindig fusson. Bizonyos esetekben az Azure SQL Database automatikusan karbantartási feladatok végrehajtására konfigurációs konfigurációtól függően. Nincsenek figyelési az adatbázis az SQL Database három értékkorlátozással:
- Alkalmazásteljesítmény-figyelés és optimalizálását.
- Biztonsági optimalizálása.
- Költségek optimalizálása.

**Alkalmazásteljesítmény-figyelés és optimalizálási**: A lekérdezési teljesítmény Elemzéseihez is igénybe személyre szabott ajánlások az adatbázis-munkaterhelés úgy, hogy az alkalmazások optimális teljesítményének fenntartásához – mindig futtassa. Akkor is állíthatja azt, hogy ezekkel az ajánlásokkal automatikusan alkalmazva és nem kell teljesítő karbantartási feladatok többet jelzi. Az Index Advisor szolgáltatással automatikusan valósítható meg a számítási feladatok alapján ajánlott indexek – automatikus finomhangolási ezt nevezik. A javaslatok tudja biztosítani a leginkább releváns javaslatok, az alkalmazás számítási feladatok változásának megfelelően fejlesztheti tovább. Érhet el manuálisan ezeket a javaslatokat tekintse át, és szabadon alkalmazza őket.  

**Biztonsági optimalizálási**: SQL Database által biztosított végrehajtható biztonsági ajánlásokat azonosítása, és vizsgálja meg a gyanús adatbázis-tevékenységekről, amely egy lehetséges szálat az ismeretlen forrásból származó adatait és a Fenyegetésészlelés védelmét a az adatbázis. [SQL-sebezhetőségi felmérés](sql-vulnerability-assessment.md) egy adatbázis vizsgálatát, és a jelentéskészítési szolgáltatás, amely lehetővé teszi, hogy nagy mennyiségű az adatbázisok biztonsági állapotának figyelése és a biztonsági kockázatokat és a egy Ön által definiált alapvető biztonsági eltérő. Minden ellenőrzését követően a végrehajtható lépéseket és javítási parancsfájlok testreszabott listájának biztosított, valamint az értékelési jelentés segít abban, hogy megfeleljenek a megfelelőségi használható.

Az Azure Security Center azonosíthatja a melyen a biztonsági javaslatok láthatók, és alkalmazza azokat egyetlen kattintással. 

**Költségek optimalizálása**: Azure SQL-platform elemzi a kihasználtsági előzményekkel adatbázisai kiértékelése és költség-optimalizálási beállítások, a kiszolgáló között. Ez az elemzés általában eltart kéthetente elemzésére és gyakorlati ajánlásokat készítünk állíthatja össze. Az egyik lehetőség a rugalmas készlet használata. A javaslat a portálon, egy szalagcím jelenik meg:

![rugalmas készletbe](./media/sql-database-manage-after-migration/elastic-pool-recommendations.png) 

Ez az elemzés a "Advisor" szakaszában is megtekintheti:

![javaslatok-tanácsadó a rugalmas készlet](./media/sql-database-manage-after-migration/advisor-section.png)

### <a name="how-do-i-monitor-the-performance-and-resource-utilization-in-sql-database"></a>Hogyan figyelhetem a teljesítmény- és erőforrás-használat az SQL Database?

SQL Database-ben kihasználhatja az intelligens elemzéseket a platform teljesítményét monitorozásakor és finomhangolásakor ennek megfelelően. Figyelemmel kísérheti a teljesítmény- és erőforrás-használat az SQL Database, a következő módszerekkel:
- **Az Azure portal**: az Azure Portalon látható egy önálló adatbázis kihasználtságát az adatbázis kiválasztásával, és kattintson a diagram az Áttekintés panelen. Módosíthatja a diagram megjelenítése több metrikát, többek között a Processzorhasználat (%), DTU százalékos értéke, adat IO, munkamenetek százaléka, és adatbázis mérete (%).

   ![Figyelés diagramra](./media/sql-database-manage-after-migration/monitoring-chart.png)

   ![2. diagram csoportosítási figyelése](./media/sql-database-manage-after-migration/chart.png)

Ez a diagram is konfigurálhatja riasztások erőforrás szerint. Ezek a riasztások lehetővé teszik az e-mailt ügyfélerőforrás-feltételek válaszolni, HTTPS vagy HTTP-végponton írási vagy egy műveletet. Tekintse meg a [SQL Database adatbázis teljesítményének figyelése](sql-database-single-database-monitor.md) részletes útmutatást.

- **Dinamikus felügyeleti nézetek**: lekérdezheti a [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) dinamikus felügyeleti nézet erőforrás használati statisztikák előzmények visszaadása az elmúlt egy óra és a [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) rendszer katalógusnézet előzmények az elmúlt 14 napban esetében visszaadandó.
- **Lekérdezési Terheléselemző**: [lekérdezési Terheléselemző](sql-database-query-performance.md) lehetővé teszi, hogy a felső erőforrás-igényes lekérdezéseket és a egy adott adatbázis sokáig futó lekérdezések előzményeit. Gyorsan azonosíthatja a LEGGYAKORIBB lekérdezések erőforrás-használatot, időtartama és a végrehajtás gyakoriságát. Nyomon követheti a lekérdezéseket, és regressziós észlelése. A szolgáltatás használatához [Query Store](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) , hogy engedélyezve van és aktív az adatbázis.

   ![Lekérdezési terheléselemző](./media/sql-database-manage-after-migration/query-performance-insight.png)

- **Az Azure SQL Analytics (előzetes verzió) a Log Analytics**: [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md) legfeljebb 150 000 SQL-adatbázisok és 5000 rugalmas SQL-készletek száma támogatása lehetővé teszi, hogy gyűjtése és az Azure SQL Azure fő teljesítménymutatók, megjelenítése munkaterület. Használhatja figyelésére és az értesítések fogadásához. Képes figyelni az SQL Database és rugalmas készletekkel kapcsolatos metrikák több Azure-előfizetések és a rugalmas készletek, és a egy alkalmazáscsoportokat minden rétegében problémák azonosításához használható.

### <a name="i-am-noticing-performance-issues-how-does-my-sql-database-troubleshooting-methodology-differ-from-sql-server"></a>E vagyok észre a teljesítménnyel kapcsolatos problémák: saját SQL Database hibaelhárítás módszertana Miben különbözik az SQL Serverről?
Az lekérdezési diagnosztizálásakor használja a hibaelhárítási eljárások jelentős részét, és adatbázis teljesítményproblémák változatlan marad. Után minden ugyanazt az SQL Server adatbázismotor működteti a felhőben. Azonban a platform – Azure SQL Database beépített rendelkezik "intelligens". Hibaelhárítása és diagnosztizálhatja a teljesítménybeli problémákat még könnyebben segíthet. Azt is elvégezhetők a javítási műveleteket az Ön nevében, és bizonyos esetekben, proaktív módon javítsa ki őket – automatikusan. 

Teljesítménnyel kapcsolatos problémák elhárítása a megközelítését, például intelligens funkciók használatával jelentősen előnyeit [lekérdezési teljesítmény Insight(QPI)](sql-database-query-performance.md) és [a Database Advisor](sql-database-advisor.md) együtt és így módszertan közötti különbség abban különbözik a tekintetben – már nem kell tennie, hogy a őrlés a fontos részleteket, amelyek segíthetnek a manuális tevékenység csak olyan mélységben hiba elhárításához. A platform elvégzi a nehezét. Egy példa arra, hogy a QPI. A QPI is szintjéig egészen lekérdezést, és tekintse meg a korábbi trendek és döntse el, ha a lekérdezés pontosan közleményében szerepelt. A Database Advisor javaslatokat ad a dolgokat, amelyek segíthetnek az általános teljesítmény javítása általában hasonlóan – hiányzó indexek, a stb lekérdezéseihez paraméterezése indexek elvetését. 

A teljesítménnyel kapcsolatos hibaelhárítás, fontos annak megállapítására, hogy csak az alkalmazással, vagy hogy biztonsági másolatot készít az adatbázis, amely negatív hatással van az alkalmazás teljesítményét. A teljesítményprobléma okára gyakran az alkalmazásrétegre rejlik. Annak oka az lehet, az architektúra vagy adathozzáférési mintájának. Vegyük példaként egy forgalmas alkalmazás, amely a hálózati késés-és nagybetűket. Ebben az esetben az alkalmazás szenved, mert sok rövid küldött kérelmeket oda-vissza lenne ("forgalmas") az alkalmazás és a kiszolgáló közötti és a egy túlterhelt hálózaton, ezek életű könyvtárgyorsítótárból hozzáadása gyorsan. Ebben az esetben a teljesítmény javítása érdekében használhatja [kötegelt](sql-database-performance-guidance.md#batch-queries). Használatával a kötegekben segít visszaesés mivel most egy kötegbe; a kérelmek feldolgozása így segít a körbejárási késéssel kivágása és hozzájárulhat az alkalmazás teljesítményéhez. 

Emellett, ha azt tapasztalja, hogy a az adatbázis az általános teljesítménye, figyelemmel kísérheti a [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) és [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) dinamikus felügyeleti nézetek annak érdekében, hogy Ismerje meg, CPU, IO és memória-felhasználás. A teljesítmény lehetséges akkor működik, mert az adatbázis-erőforrások fogy van ki. Annak oka az lehet, hogy szükség lehet a teljesítményszint és/vagy a növekvő és terheléshez zsugorítását alapján szolgáltatásszintet. 

Teljesítményproblémák hangolási ajánlásokat átfogó készletét, lásd: [az adatbázis hangolása](sql-database-performance-guidance.md#tune-your-database).

### <a name="how-do-i-ensure-i-am-using-the-appropriate-service-tier-and-performance-level"></a>Hogyan biztosítja, a megfelelő szolgáltatási csomagot és teljesítményszintet szintű használok?
Az SQL Database a Basic, Standard és prémium szintű különböző szolgáltatásszinttel rendelkezik. Minden szolgáltatási szint kap egy garantált kiszámítható teljesítményt biztosítanak a szolgáltatási szint kötve. A számítási feladatok függően előfordulhat, adatlöketekkel tevékenység, nyomja le az erőforrások kihasználtságát előfordulhat, hogy a felső határa, amely a jelenlegi teljesítményszint. Ezekben az esetekben hasznos, első lépésként kiértékelésével e bármely finomhangolása segítségével (például hozzáadása vagy módosítása egy index stb.). Ha továbbra is problémák merülnek fel korlátot, fontolja meg egy magasabb teljesítményi szinthez, vagy a szolgáltatási szint. 

|**Szolgáltatási szint**|**Gyakori használati esetek**|
|---|---|
|**Basic**|Alkalmazások egy néhány felhasználók és a egy adatbázis, amely nem rendelkezik magas egyidejűségi, a méretezési csoport és a teljesítményre vonatkozó követelményeknek. |
|**Standard**|Így jelentős feldolgozási, méretezést és teljesítményt követelményekkel rendelkező alkalmazások alacsony és közepes i/o-igényeknek. |
|**Prémium**|Az igénylést i/o-alkalmazásokat az egyidejű felhasználók, a magas CPU/memória és a magas. A prémium szint nagy feldolgozási, nagy átviteli sebességű és késés bizalmas adatokat kezelő alkalmazásokhoz használhatja. |
|||

A gondoskodik róla, hogy a megfelelő teljesítményszintet használ, a lekérdezés és az adatbázis erőforrás-használat a fent említett módon, a "Hogyan követhetem figyelemmel az SQL Database a teljesítmény- és erőforrás-kihasználtság" egyikével tanulják követheti nyomon. Látnia, hogy a lekérdezések és adatbázisok folyamatosan fut CPU/memória stb. érdemes lehet egy magasabb teljesítményi szinthez vertikális felskálázása a gyakran használt adatok. Ehhez hasonlóan, vegye figyelembe, hogy a csúcsidőszakon során is, ha nem úgy tűnik, hogy az erőforrások használatára annyi; Vegye figyelembe, hogy a jelenlegi teljesítményszint a vertikális leskálázást. 

Ha egy SaaS-alkalmazás minta vagy egy adatbázis összevonási forgatókönyvben, fontolja meg egy rugalmas készlet használatát a költségek optimalizálása. Rugalmas készlet kiválóan alkalmas adatbázis összevonása és a költség-optimalizálás érhető el. További használó rugalmas készletek több adatbázis-kezelés, lásd: [készletek és adatbázisok kezelése](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases). 

### <a name="how-often-do-i-need-to-run-database-integrity-checks-for-my-database"></a>Milyen gyakran kell az adatbázis-adatbázisomat sértetlenségi ellenőrzések futtatásához?
Az SQL Database egy intelligens technikákat, amelyek lehetővé teszik, hogy az adatok sérülésének bizonyos osztályokat kezeli automatikusan és adatvesztés nélkül használja. Ezek a technológiák a szolgáltatás beépített, és a szolgáltatás integritással amikor szüksége van merül fel. Rendszeres időközönként a szolgáltatásban az adatbázisok biztonsági másolatai kell megvizsgálni, helyreállítását és a rajta való futtatásához a DBCC CHECKDB UTASÍTÁST. Probléma merül fel, az SQL Database proaktív módon oldja meg őket. [Automatikus javítás](/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring) lapokat, sérült vagy van az adatok épségével kapcsolatos problémákat rögzítési-t használja. Az adatbázis-oldalak mindig az alapértelmezett ELLENŐRZŐÖSSZEG-beállítás, amely igazolja, hogy az oldal ellenőrzése. SQL Database proaktív módon figyeli, és áttekinti az adatok integritásának megőrzése, az adatbázis és, ha problémák merülnek fel, címét őket a legmagasabb prioritású. Ezek dönthet úgy, hogy igény szerint futtathatja a saját Épségellenőrzés, a rendszer.  További információkért lásd: [adatok integritásának megőrzése, SQL Database-ben](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)

## <a name="data-movement-after-migration"></a>Adatok áthelyezése a migrálás után

### <a name="how-do-i-export-and-import-data-as-bacpac-files-from-sql-database"></a>Hogyan exportálja és adatok importálása az SQL-adatbázis BACPAC-fájlok formájában?

- **Exportálás**: exportálhatja az Azure SQL-adatbázis BACPAC-fájl, az Azure Portalról

   ![adatbázis exportálása](./media/sql-database-export/database-export.png)

- **Importálása**: is importálhat adatokat egy BACPAC-fájlba az adatbázisba, az Azure portal használatával.

   ![adatbázis importálása](./media/sql-database-import/import.png)

### <a name="how-do-i-synchronize-data-between-sql-database-and-sql-server"></a>Az SQL Database és SQL Server közötti szinkronizálása?
Ennek eléréséhez több módszert is van: 
- **[Adatszinkronizálás](sql-database-sync-data.md)**  – Ez a funkció segítségével szinkronizálhatják helyszíni SQL Server-adatbázisok és az SQL Database több közötti adatszinkronizáláshoz. Szinkronizálás a helyszíni SQL Server-adatbázisok, szüksége telepítése és konfigurálása a sync-ügynök a helyi számítógépen, és nyissa meg az 1433-as kimenő TCP port.
- **[Tranzakciós replikáció](https://azure.microsoft.com/blog/transactional-replication-to-azure-sql-database-is-now-generally-available/)**  – tranzakciós replikáció, szinkronizálhatja az adatok a helyszínről az Azure SQL DB-t a helyszíni a közzétevő és az Azure SQL DB alatt az előfizető. Egyelőre csak a beállítás használata támogatott. Az adatok migrálását a helyszíni az Azure SQL minimális állásidővel kapcsolatos további információkért lásd: [tranzakciós replikáció használata](sql-database-cloud-migrate.md#method-2-use-transactional-replication)

## <a name="next-steps"></a>További lépések
Ismerje meg [SQL-adatbázis](sql-database-technical-overview.md).

