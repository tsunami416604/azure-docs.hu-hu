---
title: Egy- és készletezésű adatbázisok kezelése áttelepítés után
description: Ismerje meg, hogyan kezelheti az adatbázist az Azure SQL Database-be való migrálás után.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: joesackmsft
ms.author: josack
ms.reviewer: sstein
ms.date: 02/13/2019
ms.openlocfilehash: ebb512fee0186bed3cc7f49f0525dac43e57da3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256184"
---
# <a name="new-dba-in-the-cloud--managing-your-single-and-pooled-databases-in-azure-sql-database"></a>Új DBA a felhőben – Egyetlen és összevont adatbázisok kezelése az Azure SQL Database-ben

A hagyományos, saját irányítású, önkontrollos környezetből paas környezetbe való áttérés elsőre kissé nyomasztónak tűnhet. Alkalmazásfejlesztőként vagy DBA-ként érdemes ismernie a platform alapvető képességeit, amelyek segítenek az alkalmazás rendelkezésre állásában, teljesítményében, biztonságosságában és rugalmasságában – mindig. Ez a cikk célja, hogy pontosan ezt. A cikk tömören szervezi az erőforrásokat, és útmutatást ad arról, hogyan lehet a legjobban kihasználni az SQL Database kulcsképességeit egy- és készletezésű adatbázisokkal az alkalmazás hatékony működésének kezeléséhez és megtartásához, valamint az optimális eredmények eléréséhez a felhőben. Tipikus közönség ezt a cikket lenne azok, akik:

- Az alkalmazás(ok) Azure SQL Database-be való áttelepítésének kiértékelése – az alkalmazás(ok) modernizálása.
- Vannak folyamatban az alkalmazás(ok) áttelepítése – folyamatban lévő áttelepítési forgatókönyv.
- Nemrég fejeződött be az Azure SQL DB – Új DBA a felhőben áttelepítése.

Ez a cikk ismerteti az Azure SQL Database néhány alapvető jellemzőit, amelyek platformként, amely könnyen kihasználható, ha rugalmas készletek ben végzett egyetlen adatbázisokkal és készletezett adatbázisokkal dolgozik. Ezek a következők:

- Adatbázis figyelése az Azure Portal használatával
- Üzletmenet-folytonosság és vészhelyreállítás (BCDR)
- Biztonság és megfelelőség
- Intelligens adatbázis-felügyelet és -karbantartás
- Adatáthelyezés

> [!NOTE]
> Ez a cikk a következő üzembe helyezési lehetőségek az Azure SQL Database:egyetlen adatbázisok és rugalmas készletek. Nem vonatkozik az SQL Database felügyelt példánytelepítési beállítására.

## <a name="monitor-databases-using-the-azure-portal"></a>Adatbázisok figyelése Azure Portal használatával

Az [Azure Portalon](https://portal.azure.com/)figyelheti az egyes adatbázisok kihasználtságát az adatbázis kiválasztásával, majd a **Figyelési** diagramra kattintva. Ekkor megjelenik a **Metrika** ablak, amelyet a **Diagram szerkesztése** gombra kattintva módosíthat. Adja hozzá a következő metrikákat:

- Processzorhasználat (%)
- DTU-kihasználtság (%)
- Adat IO kihasználtsága (%)
- Adatbázis méretének kihasználtsága

Miután hozzáadta ezeket a mutatókat, továbbra is megtekintheti őket a **Figyelés** diagramban, amely további információkat tartalmaz a **Metrika** ablakban. A négy metrika az átlagos kihasználtság százalékos arányát jeleníti meg az adatbázis **DTU-jához** viszonyítva. A szolgáltatási szintekről a [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md) és a [virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md) cikkek című témakörben talál további információt.  

![Adatbázis-teljesítményének szolgáltatásszint-figyelése.](./media/sql-database-single-database-monitoring/sqldb_service_tier_monitoring.png)

A metrikákhoz riasztásokat is lehet konfigurálni. Kattintson a **Riasztás hozzáadása** gombra a **Metrika** ablakban. A riasztás konfigurálásához kövesse a Varázslót. Lehetőség van riasztást kérni, ha a metrikák túllépnek egy bizonyos küszöböt, vagy egy bizonyos küszöb alá esnek.

Például ha az adatbázisban munkaterhelés-növekedésére számít, beállíthatja, hogy riasztást kapjon elektronikus üzenet formájában abban az esetben, ha az adatbázisra vonatkozó bármelyik metrika eléri a 80 százalékot. Ezt korai figyelmeztetésként használhatja annak kigondolásához, hogy mikor kell átváltania a következő legmagasabb számítási méretre.

A teljesítménymutatók segítségével meghatározhatja, hogy képes-e alacsonyabb számítási méretre visszaminősíteni. Tegyük fel, hogy Standard S2 adatbázist használ, és a metrikák azt mutatják, hogy az adatbázis átlagos kihasználtsága egy adott időpontban nem több, mint 10 százalék. Ebben az esetben valószínű, hogy az adatbázis Standard S1 teljesítményszinten is megfelelően fog működni. Azonban vegye figyelembe a számítási feladatok, amelyek kiugrása vagy ingadozik, mielőtt a döntést, hogy egy alacsonyabb számítási méret.

## <a name="business-continuity-and-disaster-recovery-bcdr"></a>Üzletmenet-folytonosság és vészhelyreállítás (BCDR)

Az üzletmenet folytonossága és a vész-helyreállítási képességek lehetővé teszik, hogy folytassa a szokásos módon, katasztrófa esetén. A katasztrófa lehet adatbázisszintű esemény (például valaki tévesen eldob egy fontos táblát) vagy egy adatközpont-szintű esemény (regionális katasztrófa, például szökőár).

### <a name="how-do-i-create-and-manage-backups-on-sql-database"></a>Hogyan hozhatok létre és kezelhetek biztonsági másolatokat az SQL Database-ben?

Nem hoz létre biztonsági mentéseket az Azure SQL DB-n, és ez azért van, mert nem kell. Az SQL Database automatikusan biztonsági másolatot készít az adatbázisokról, így többé nem kell aggódnia a biztonsági mentések ütemezése, készítése és kezelése miatt. A platform hetente teljes biztonsági mentést készít, néhány óránként különbözeti biztonsági mentést, és 5 percenként naplóbiztonsági mentést biztosít a vészhelyreállítás hatékonysága és az adatvesztés minimális. Az első teljes biztonsági mentés akkor történik, amikor létrehoz egy adatbázist. Ezek a biztonsági mentések egy bizonyos ideig elérhetők, az úgynevezett "megőrzési időszak", és a választott szolgáltatási szinttől függően változik. Az SQL Database lehetővé teszi, hogy a Point [in Time Recovery (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore)segítségével a megőrzési időszakon belül bármikor visszaállítson.

|Szolgáltatásszint|Megőrzési időszak napokban|
|---|:---:|
|Basic|7|
|Standard|35|
|Prémium|35|
|||

Emellett a [hosszú távú megőrzési (LTR)](sql-database-long-term-retention.md) funkció lehetővé teszi, hogy a biztonsági másolat fájljait sokkal hosszabb ideig, konkrétan legfeljebb 10 évig tartsa meg, és az adott időszakon belül bármikor visszaállítsa a biztonsági mentések adatait. Továbbá az adatbázis biztonsági mentései georeplikált tárolóban vannak tárolva a regionális katasztrófával szembeni ellenálló képesség biztosítása érdekében. Ezeket a biztonsági mentéseket bármely Azure-régióban bármikor visszaállíthatja a megőrzési időszakon belül. Lásd: [Üzletmenet-folytonossági áttekintés](sql-database-business-continuity.md).

### <a name="how-do-i-ensure-business-continuity-in-the-event-of-a-datacenter-level-disaster-or-regional-catastrophe"></a>Hogyan biztosíthatom az üzletmenet folytonosságát adatközpont-szintű katasztrófa vagy regionális katasztrófa esetén?

Mivel az adatbázis biztonsági mentései georeplikált tárolóban tárolódnak, így regionális katasztrófa esetén visszaállíthatja a biztonsági mentést egy másik Azure-régióba. Ezt nevezzük geo-visszaállításnak. Az RPO (Recovery Point Objective) erre általában < 1 óra, és az ERT (becsült helyreállítási idő - néhány perc óra).

Az alapvető fontosságú adatbázisok hoz az Azure SQL DB aktív georeplikációt kínál. Ez lényegében azt teszi, hogy létrehoz egy georeplikált másodlagos másolatot az eredeti adatbázisegy másik régióban. Ha például az adatbázis eredetileg az Azure West USA régióban található, és regionális katasztrófa-reziliát szeretne. Az usa nyugati részén lévő adatbázis aktív georeplikáját az USA keleti részén hozhatja létre. Amikor a csapás az USA nyugati régiójára csap le, átveheti a feladatát az USA keleti régiójában. Konfigurálása őket egy automatikus feladatátvételi csoport még jobb, mert ez biztosítja, hogy az adatbázis automatikusan átadja a másodlagos USA keleti részén katasztrófa esetén. Az RPO erre < 5 másodperc, és az ERT < 30 másodperc.

Ha egy automatikus feladatátvételi csoport nincs konfigurálva, majd az alkalmazás nak aktívan figyelnie kell egy katasztrófát, és meg kell indítania a feladatátvételt a másodlagos. Legfeljebb 4 ilyen aktív georeplikát hozhat létre különböző Azure-régiókban. Még jobb lesz. Ezek a másodlagos aktív georeplikák írásvédett hozzáféréshez is hozzáférhetnek. Ez nagyon hasznos, hogy csökkentse a késést egy földrajzilag elosztott alkalmazásforgatókönyv.

### <a name="how-does-my-disaster-recovery-plan-change-from-on-premises-to-sql-database"></a>Hogyan változik a vész-helyreállítási terv a helyszíni ről az SQL Database-re?

Összefoglalva, a hagyományos helyszíni SQL Server-beállítás megköveteli, hogy aktívan kezelje az elérhetőséget olyan szolgáltatások használatával, mint a feladatátvevő fürtözés, az adatbázis-tükrözés, a tranzakcióreplikáció vagy a log szállítás, valamint a biztonsági mentések karbantartása és kezelése a biztonsági mentések biztosítása érdekében Üzletmenet folytonossága. Az SQL Database segítségével a platform kezeli ezeket az Ön számára, így az adatbázis-alkalmazás fejlesztésére és optimalizálására összpontosíthat, és nem kell annyira aggódnia a katasztrófavédelem miatt. A biztonsági mentési és vész-helyreállítási tervek konfigurálva és az Azure Portalon (vagy néhány, a PowerShell API-k használatával néhány parancstal) konfigurálhatók és dolgozhatnak.

Ha többet szeretne megtudni a vészhelyreállításról, olvassa el a következő témakört: [Azure SQL Db Disaster Recovery 101](https://azure.microsoft.com/blog/azure-sql-databases-disaster-recovery-101/)

## <a name="security-and-compliance"></a>Biztonság és megfelelőség

Az SQL Database nagyon komolyan veszi a biztonságot és az adatvédelmet. Az SQL Database-en belüli biztonság adatbázis-szinten és platformszinten érhető el, és a legjobban akkor érthető, ha több rétegbe sorolható. Minden rétegnél vezérelheti és biztosíthatja az alkalmazás optimális biztonságát. A rétegek a következők:

- Identitás & hitelesítés ([SQL-hitelesítés és Azure Active Directory [AAD] hitelesítés).](sql-database-manage-logins.md)
- Tevékenység[figyelése](sql-database-auditing.md) ( Naplózás és [fenyegetésészlelés](sql-database-threat-detection.md)).
- A tényleges adatok védelme ([Transzparens adattitkosítás [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) és [Mindig titkosított [AE] ).](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- Bizalmas és kiemelt adatokhoz való hozzáférés szabályozása ([sorszintű biztonság](/sql/relational-databases/security/row-level-security) és [dinamikus adatmaszkolás](/sql/relational-databases/security/dynamic-data-masking)).

[Az Azure Security Center](https://azure.microsoft.com/services/security-center/) központosított biztonsági felügyeletet kínál az Azure-ban, a helyszíni és más felhőkben futó számítási feladatok között. Megtekintheti, hogy az SQL-adatbázis alapvető védelme, például [a naplózás](sql-database-auditing.md) és az [átlátszó adattitkosítás [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) konfigurálva van-e az összes erőforráson, és saját követelmények alapján hozhat létre házirendeket.

### <a name="what-user-authentication-methods-are-offered-in-sql-database"></a>Milyen felhasználói hitelesítési módszereket kínál az SQL Database?

Az SQL Database két hitelesítési módszert kínál:

- [Azure Active Directory-hitelesítés](sql-database-aad-authentication.md)
- [SQL-hitelesítés](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication)

A hagyományos windows-hitelesítés nem támogatott. Az Azure Active Directory (AD) egy központi identitás- és hozzáférés-kezelési szolgáltatás. Ezzel nagyon kényelmesen biztosíthat egyszeri bejelentkezési hozzáférést (SSO) a szervezet összes munkatársa számára. Ez azt jelenti, hogy a hitelesítő adatok meg vannak osztva az összes Azure-szolgáltatás között az egyszerűbb hitelesítés. Az AAD támogatja az [MFA -t (Multi Factor Authentication),](sql-database-ssms-mfa-authentication.md) és [néhány kattintással](../active-directory/hybrid/how-to-connect-install-express.md) az AAD integrálható a Windows Server Active Directoryval. Az SQL-hitelesítés pontosan úgy működik, ahogy korábban használta. Megadja a felhasználónevet/jelszót, és hitelesítheti a felhasználókat egy adott SQL Database-kiszolgáló bármely adatbázisában. Ez azt is lehetővé teszi, hogy az SQL Database és az SQL Data Warehouse többtényezős hitelesítést és vendégfelhasználói fiókokat kínáljon egy Azure AD-tartományban. Ha már rendelkezik a helyszíni Active Directoryval, a címtár összeegyezése az Azure Active Directoryval kiterjesztheti a címtár azure-ra.

|**Ha...**|**SQL adatbázis / SQL adattárház**|
|---|---|
|Nem szeretné használni az Azure Active Directoryt (AD) az Azure-ban|[SQL-hitelesítés](sql-database-security-overview.md) használata|
|Használt AD az SQL Server helyszíni kiszolgálóján|[AD-t az Azure AD-vel,](../active-directory/hybrid/whatis-hybrid-identity.md)és azure AD-hitelesítést használ. Ezzel használhatja az egyszeri bejelentkezést.|
|A többtényezős hitelesítés (MFA) kényszerítésének szükségessége|Többfa megkövetelése házirendként a [Microsoft feltételes hozzáférésen](sql-database-conditional-access.md)keresztül, és használja [az Azure AD Universal hitelesítést az MFA-támogatással.](sql-database-ssms-mfa-authentication.md)|
|Vendégfiókok microsoftos fiókok (live.com, outlook.com) vagy más tartományokból (gmail.com)|Azure [AD Univerzális hitelesítést](sql-database-ssms-mfa-authentication.md) használhat az SQL Database/Data Warehouse szolgáltatásban, amely az [Azure AD B2B együttműködést használja.](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)|
|Be vannak jelentkezve a Windows ba az Összevont tartományból származó Azure AD-hitelesítő adataival|Az [Azure AD integrált hitelesítésének](sql-database-aad-authentication-configure.md)használata.|
|Be vannak jelentkezve a Windows rendszerbe az Azure-ral nem rendelkező tartomány hitelesítő adataival|Az [Azure AD integrált hitelesítésének](sql-database-aad-authentication-configure.md)használata.|
|Közepes szintű szolgáltatásokkal kell rendelkeznie, amelyeknek csatlakozniuk kell az SQL Database-hez vagy az SQL Data Warehouse-hoz|Az [Azure AD integrált hitelesítésének](sql-database-aad-authentication-configure.md)használata.|
|||

### <a name="how-do-i-limit-or-control-connectivity-access-to-my-database"></a>Hogyan korlátozható vagy szabályozható az adatbázishoz való kapcsolódási hozzáférés

Több olyan technika áll az Ön rendelkezésére, amelyek segítségével optimális kapcsolat szervezet az alkalmazáshoz.

- Tűzfalszabályok
- VNet szolgáltatás végpontjai
- Fenntartott IP-címek

#### <a name="firewall"></a>Tűzfal

A tűzfal megakadályozza a hozzáférést a kiszolgálóhoz egy külső entitás, azáltal, hogy csak bizonyos entitások hozzáférést az SQL Database-kiszolgáló. Alapértelmezés szerint az SQL Database-kiszolgálón belüli összes kapcsolat és adatbázis nem engedélyezett, kivéve a más Azure Servicesből érkező kapcsolatokat. A tűzfalszabállyal csak az Ön által jóváhagyott entitások (például fejlesztői számítógép) számára nyithatja meg a hozzáférést a kiszolgálóhoz, ha engedélyezi a számítógép IP-címét a tűzfalon keresztül. Azt is lehetővé teszi, hogy adja meg a tartományt az IP-k, hogy szeretné engedélyezni a hozzáférést az SQL Database server. Például a fejlesztői számítógép IP-címei a szervezetben egyszerre hozzáadhatók egy tartomány megadásával a Tűzfal beállításai lapon.

Tűzfalszabályokat kiszolgálói vagy adatbázisszinten is létrehozhat. A kiszolgálószintű IP-tűzfalszabályok az Azure Portalon vagy az SSMS-ben hozhatók létre. A kiszolgálószintű és adatbázis-szintű tűzfalszabályok beállításáról a KÖVETKEZŐ témakörben tud részt adni: [IP-tűzfalszabályok létrehozása az SQL Database ben.](sql-database-security-tutorial.md#create-firewall-rules)

#### <a name="service-endpoints"></a>Szolgáltatásvégpontok

Alapértelmezés szerint az SQL-adatbázis "Az Azure-szolgáltatások hozzáférésének engedélyezése a kiszolgálóhoz" beállítással van konfigurálva – ami azt jelenti, hogy az Azure-ban lévő bármely virtuális gép megpróbálhat csatlakozni az adatbázishoz. Ezeka kísérletek még mindig hitelesített. Ha azonban nem szeretné, hogy az adatbázis bármely Azure IP-cím elérhető legyen, letilthatja az "Azure-szolgáltatások hozzáférés-kiszolgáló" letiltását. Ezenkívül konfigurálhatja a [VNet-szolgáltatás végpontjait is.](sql-database-vnet-service-endpoint-rule-overview.md)

A szolgáltatásvégpontok (SE) lehetővé teszik, hogy a kritikus Fontosságú Azure-erőforrásokat csak a saját privát virtuális hálózatának az Azure-ban tegye elérhetővé. Ezzel lényegében megszünteti a forrásokhoz való nyilvános hozzáférést. A virtuális hálózat és az Azure közötti forgalom az Azure gerinchálózatán marad. SE nélkül kényszerített bújtatási csomag-útválasztást kap. A virtuális hálózat kényszeríti az internetes forgalmat a szervezet és az Azure Service forgalmat, hogy ugyanazon az útvonalon haladjon. A szolgáltatás-végpontok, optimalizálhatja ezt, mivel a csomagok áramlását egyenesen a virtuális hálózatból a szolgáltatás az Azure gerinchálózat.

![VNet-szolgáltatásvégpontok](./media/sql-database-manage-after-migration/vnet-service-endpoints.png)

#### <a name="reserved-ips"></a>Fenntartott IP-címek

Egy másik lehetőség a [fenntartott IP-címek](../virtual-network/virtual-networks-reserved-public-ip.md) kiépítése a virtuális gépek számára, és adja hozzá az adott virtuális gép IP-címeket a kiszolgáló tűzfal beállításaiközött. A fenntartott IP-címek hozzárendelésével nem tudja frissíteni a tűzfalszabályokat az IP-címek módosításával.

### <a name="what-port-do-i-connect-to-sql-database-on"></a>Milyen porton csatlakozom az SQL-adatbázishoz?

1433-as kikötő. Az SQL Database ezen a porton keresztül kommunikál. Ha vállalati hálózaton belülről szeretne csatlakozni, hozzá kell adnia egy kimenő szabályt a szervezet tűzfalbeállításaiközött. Iránymutatásként ne tegye ki az 1433-as portot az Azure-határon kívül.

### <a name="how-can-i-monitor-and-regulate-activity-on-my-server-and-database-in-sql-database"></a>Hogyan figyelhetem és szabályozhatom a kiszolgálón és az adatbázison végzett tevékenységeket az SQL Database-ben?

#### <a name="sql-database-auditing"></a>SQL Database naplózás

Az SQL Database segítségével bekapcsolhatja a naplózást az adatbázis-események nyomon követéséhez. [SQL Database Auditing](sql-database-auditing.md) rekordok adatbázis-események, és írja őket egy naplófájlt az Azure Storage-fiókban. Az auditálás különösen akkor hasznos, ha betekintést kíván nyerni a potenciális biztonsági és irányelvsértésekbe, a jogszabályoknak való megfelelés fenntartásába stb. Lehetővé teszi bizonyos eseménykategóriák meghatározását és konfigurálását, amelyek ről úgy gondolja, hogy naplózásra van szükségük, és amelyek alapján előre konfigurált jelentéseket és irányítópultot kaphat az adatbázisban bekövetkező események áttekintéséhez. Ezeket a naplózási házirendeket az adatbázis vagy a kiszolgáló szintjén is alkalmazhatja. A kiszolgáló/adatbázis naplózásának bekapcsolását bemutató útmutató: [Sql Database Auditing engedélyezése](sql-database-security-tutorial.md#enable-security-features).

#### <a name="threat-detection"></a>Fenyegetések észlelése

A [fenyegetés észlelése](sql-database-threat-detection.md), akkor kap a képesség, hogy járjon el a biztonsági vagy irányelvmegsértése által felfedezett naplózás nagyon könnyen. Nem kell biztonsági szakértőnek lennie ahhoz, hogy kezelni tudja a rendszer potenciális fenyegetéseit vagy megsértéséit. A fenyegetésészlelés néhány beépített képességgel is rendelkezik, például az SQL Injection észlelésével. SQL injection egy kísérlet, hogy módosítsa vagy veszélyezteti az adatokat, és egy meglehetősen gyakori módja a támadó adatbázis-alkalmazás általában. A fenyegetésészlelés több algoritmuskészletet futtat, amelyek észlelik a potenciális biztonsági réseket és az SQL-injektálási támadásokat, valamint a rendellenes adatbázis-hozzáférési mintákat (például a szokatlan helyről vagy egy ismeretlen egyszerű felhasználótól való hozzáférést). A biztonsági tisztviselők vagy más kijelölt rendszergazdák e-mailértesítést kapnak, ha fenyegetést észlel nek az adatbázisban. Minden értesítés részletesen ismerteti a gyanús tevékenységet, és ajánlásokat tesz a fenyegetés további kivizsgálására és csökkentésére. A fenyegetésészlelés bekapcsolására vonatkozó témakörökből megtudhatja: [Fenyegetésészlelés engedélyezése.](sql-database-security-tutorial.md#enable-security-features)

### <a name="how-do-i-protect-my-data-in-general-on-sql-database"></a>Hogyan védhetem meg az adataimat általában az SQL Database-ben?

A titkosítás erős mechanizmust biztosít a bizalmas adatok védelmére és védelmére a behatolóktól. A titkosított adatok nem használhatók a behatoló számára a visszafejtési kulcs nélkül. Így egy további védelmi réteget ad hozzá az SQL Database beépített meglévő biztonsági rétegeihez. Az SQL Database adatainak védelmének két szempontja van:

- Az adatokban és a naplófájlokban lévő inszinten lévő adatok
- A repülés közbeni adatok

Az SQL Database-ben alapértelmezés szerint a tárolóalrendszer adataiban és naplófájljaiban tárolt adatok teljesen titkosítva vannak az [Átlátszó adattitkosítás [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)segítségével. A biztonsági mentések is titkosítva vannak. A TDE nincs szükség módosításokra az alkalmazás oldalon, amely az adatok eléréséhez. A titkosítás és a visszafejtés transzparens módon történik; innen a név.
A bizalmas adatok védelme repülés közben és nyugalmi formában, az SQL Database egy ["Mindig titkosított (AE)](/sql/relational-databases/security/encryption/always-encrypted-database-engine)nevű szolgáltatást biztosít. Az AE az ügyféloldali titkosítás egyik formája, amely titkosítja az adatbázis bizalmas oszlopait (így azok titkosításban vannak az adatbázis-rendszergazdák és a jogosulatlan felhasználók számára). A kiszolgáló kezdetben megkapja a titkosított adatokat. Az Always Encrypted kulcsa az ügyféloldalon is tárolódik, így csak az arra jogosult ügyfelek tudják visszafejteni a bizalmas oszlopokat. A kiszolgáló és az adatrendszergazdák nem látják a bizalmas adatokat, mivel a titkosítási kulcsok az ügyfélen tárolódnak. Az AE titkosítja a tábla bizalmas oszlopait, a jogosulatlan ügyfelektől a fizikai lemezig. Az AE ma támogatja az egyenlőség összehasonlítását, így a Rendszerték továbbra is lekérdezhetik a titkosított oszlopokat az SQL-parancsok részeként. Mindig titkosított számos kulcstároló-beállítással használható, például [az Azure Key Vault,](sql-database-always-encrypted-azure-key-vault.md)a Windows tanúsítványtároló és a helyi hardveres biztonsági modulokkal.

|**Jellemzők**|**Always Encrypted**|**Transzparens adattitkosítás**|
|---|---|---|
|**Titkosítási időtartam**|Végpontok között|Nyugalmi adatok|
|**Az adatbázis-kiszolgáló hozzáférhet a bizalmas adatokhoz**|Nem|Igen, mivel a titkosítás az inaktív adatokra van|
|**Engedélyezett T-SQL műveletek**|Egyenlőség-összehasonlítás|Az összes T-SQL felület elérhető|
|**A funkció használatához szükséges alkalmazásmódosítások**|Minimális|Nagyon minimális|
|**Titkosítás immára**|Oszlopszint|az adatbázis-szintű megadás helyett|
||||

### <a name="how-can-i-limit-access-to-sensitive-data-in-my-database"></a>Hogyan korlátozhatom a bizalmas adatokhoz való hozzáférést az adatbázisomban?

Minden alkalmazás rendelkezik egy bizonyos kis bizalmas adatokat az adatbázisban, amelyet meg kell védeni attól, hogy mindenki számára látható legyen. A szervezet bizonyos munkatársainak meg kell tekinteniük ezeket az adatokat, de mások nak nem kell megtekinteniük ezeket az adatokat. Erre példa a munkavállalói bérek. A vezetőnek hozzá kell férnem a közvetlen jelentéseihez a bérinformációkhoz, azonban az egyes csapattagoknak nem szabad hozzáférniük társaik béradataihoz. Egy másik forgatókönyv az adatfejlesztők, akik a fejlesztési szakaszok vagy tesztelés során esetleg bizalmas adatokkal kommunikálnak, például az ügyfelek SSN-jei. Ezt az információt ismét nem kell kitenni a fejlesztő. Ilyen esetekben a bizalmas adatokat vagy maszkolni kell, vagy egyáltalán nem szabad kitenni. Az SQL Database két ilyen módszert kínál annak megakadályozására, hogy illetéktelen felhasználók megtekinthessék a bizalmas adatokat:

[A dinamikus adatmaszkolás](sql-database-dynamic-data-masking-get-started.md) egy adatmaszkolási szolgáltatás, amely lehetővé teszi a bizalmas adatok expozíciójának korlátozását azáltal, hogy elfedi azt az alkalmazásréteg nem kiemelt jogosultságú felhasználói számára. Olyan maszkolási szabályt határoz meg, amely maszkolási mintát hozhat létre (például csak egy nemzeti azonosító SSN: XXX-XX-0000 utolsó négy számjegyét jeleníti meg, és a legtöbbjét X-ként jelöli meg), és azonosítja, hogy mely felhasználókat kell kizárni a maszkolási szabályból. A maszkolás menet közben történik, és különböző maszkolási funkciók állnak rendelkezésre a különböző adatkategóriákhoz. A dinamikus adatmaszkolás lehetővé teszi az adatbázis bizalmas adatainak automatikus észlelését és maszkolás alkalmazását.

[A Sorszintű biztonság](/sql/relational-databases/security/row-level-security) lehetővé teszi a hozzáférés szabályozását a sor szintjén. Ami azt jelenti, hogy az adatbázistábla bizonyos sorai a lekérdezést végrehajtó felhasználó (csoporttagság vagy végrehajtási környezet) alapján rejtve maradnak. A hozzáférési korlátozás az adatbázisrétegen történik, nem pedig egy alkalmazásszinten, hogy egyszerűsítse az alkalmazás logikáját. Először hozzon létre egy szűrőpredikátumot, kiszűri a nem elérhető sorokat, és a biztonsági házirend határozza meg, hogy ki férhet hozzá ezekhez a sorokhoz. Végül a végfelhasználó futtatja a lekérdezést, és a felhasználó jogosultságától függően vagy megtekinti a korlátozott sorokat, vagy egyáltalán nem látja őket.

### <a name="how-do-i-manage-encryption-keys-in-the-cloud"></a>Hogyan kezelhetem a titkosítási kulcsokat a felhőben?

Vannak kulcskezelési lehetőségek mind a mindig titkosított (ügyféloldali titkosítás) és az átlátszó adattitkosítás (inaktív titkosítás) számára. Javasoljuk, hogy rendszeresen forgassa a titkosítási kulcsokat. A rotációs gyakoriságnak igazodnia kell a belső szervezeti előírásokhoz és a megfelelőségi követelményekhez.

#### <a name="transparent-data-encryption-tde"></a>Transzparens adattitkosítás (TDE)

A TDE-ben kétkulcsos hierarchia van – az egyes felhasználói adatbázisokban lévő adatokat egy szimmetrikus AES-256 adatbázis-egyedi adatbázis-titkosítási kulcs (DEK) titkosítja, amelyet viszont egy kiszolgáló-egyedi aszimmetrikus RSA 2048 főkulcs titkosít. A főkulcs a következők ként kezelhető:

- Automatikusan a platform - SQL Database.
- Vagy az [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md) kulcstárolóként való használatával.

Alapértelmezés szerint az Átlátszó adattitkosítás főkulcsát az SQL Database szolgáltatás kezeli a kényelem érdekében. Ha a szervezet szeretné szabályozni a főkulcs, van egy lehetőség, hogy az Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md) a kulcstároló. Az Azure Key Vault használatával a szervezet átveszi a kulcskiépítés, a rotáció és az engedélyvezérlők vezérlését. [A TDE főkulcs típusának elforgatása vagy váltása](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql-key-rotation) gyors, mivel csak a DEK-t titkosítja újra. A biztonsági és adatkezelési szerepkörök elkülönítésével rendelkező szervezetek esetében a biztonsági rendszergazda kiépítheti a TDE főkulcs kulcsanyagát az Azure Key Vaultban, és megadhat egy Azure Key Vault-kulcsazonosítót az adatbázis rendszergazdájának, amelyhez használni kell. titkosítást a kiszolgálón. A Key Vault úgy lett kialakítva, hogy a Microsoft nem látja és nem bontja ki a titkosítási kulcsokat. A szervezet kulcsainak központi felügyeletét is megkapja.

#### <a name="always-encrypted"></a>Always Encrypted

Van egy [kétkulcsos hierarchia](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted) is az Always Encrypted (Mindig titkosított) titkosítással – a bizalmas adatok oszlopát egy AES 256 oszlopos titkosítási kulcs (CEK) titkosítja, amelyet viszont egy oszlopfőkulcs (CMK) titkosít. Az Always Encrypted számára biztosított ügyfél-illesztőprogramok nincsenek korlátozva a CMK-k hosszára vonatkozóan. A CEK titkosított értéke az adatbázisban tárolódik, a CMK pedig egy megbízható kulcstárolóban, például a Windows tanúsítványtárolóban, az Azure Key Vaultban vagy egy hardveres biztonsági modulban.

- Mind a [CEK, mind](/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell) a CMK elforgatható.
- A CEK elforgatása az adatműveletek mérete, és a titkosított oszlopokat tartalmazó táblák méretétől függően időigényes lehet. Ezért célszerű ennek megfelelően megtervezni a CEK-rotációkat.
- A CMK elforgatása azonban nem zavarja az adatbázis teljesítményét, és külön szerepkörökkel is elvégezhető.

Az alábbi ábra a Mindig titkosított oszlopfőkulcsok kulcstárolási beállításait mutatja be

![Mindig titkosított CMK áruházszolgáltatók](./media/sql-database-manage-after-migration/always-encrypted.png)

### <a name="how-can-i-optimize-and-secure-the-traffic-between-my-organization-and-sql-database"></a>Hogyan optimalizálhatom és biztosíthatom a szervezet és az SQL Database közötti forgalmat?

A szervezet és az SQL-adatbázis közötti hálózati forgalom általában a nyilvános hálózaton keresztül történik. Ha azonban úgy dönt, hogy optimalizálja ezt az elérési utat, és biztonságosabbá teszi, akkor az Expressz útvonalba is belenézhet. Az expressz útvonal lényegében lehetővé teszi a vállalati hálózat kiterjesztését az Azure platformra egy privát kapcsolaton keresztül. Ezzel nem megy át a nyilvános interneten. Emellett nagyobb biztonságot, megbízhatóságot és útválasztási optimalizálást is kap, amely alacsonyabb hálózati késéseket és sokkal gyorsabb sebességet eredményez, mint amit általában a nyilvános interneten keresztül szeretne tapasztalni. Ha azt tervezi, hogy jelentős adattömböt továbbít a szervezete és az Azure között, az Express Route használata költségelőnyökkel járhat. Három különböző kapcsolatmodell közül választhat a szervezet és az Azure közötti kapcsolathoz:

- [Felhőalapú csere közös elhelyezése](../expressroute/expressroute-connectivity-models.md#CloudExchange)
- [Bármi-minden](../expressroute/expressroute-connectivity-models.md#IPVPN)
- [Point-to-point](../expressroute/expressroute-connectivity-models.md#Ethernet)

Express Route is lehetővé teszi, hogy tört akár 2x a sávszélesség limit vásárol felár nélkül. Lehetőség van a régiók közötti kapcsolat konfigurálására is az Express útvonal használatával. Az ER-kapcsolatszolgáltatók listáját a Következő témakörben tetszhet: [Express Route Partners and Peering Locations](../expressroute/expressroute-locations.md). Az alábbi cikkek részletesebben ismertetik az Expressz útvonalat:

- [Bevezetés az expressz útvonalon](../expressroute/expressroute-introduction.md)
- [Előfeltételek](../expressroute/expressroute-prerequisites.md)
- [Munkafolyamatok](../expressroute/expressroute-workflows.md)

### <a name="is-sql-database-compliant-with-any-regulatory-requirements-and-how-does-that-help-with-my-own-organizations-compliance"></a>Az SQL Database megfelel a szabályozási követelményeknek, és ez hogyan segít a saját szervezetem megfelelőségének

Az SQL Database számos szabályozási kompatibilitásnak felel meg. Az SQL Database által teljesített legújabb kompatibilitási készletek megtekintéséhez keresse fel a [Microsoft Adatvédelmi központot,](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) és részletezze a szervezet számára fontos kompatibilitásokat, és nézze meg, hogy az SQL Database a megfelelő Azure-szolgáltatások részét képezi-e. Fontos megjegyezni, hogy bár az SQL Database megfelelő szolgáltatásként hitelesíthető, segíti a szervezet szolgáltatásának megfelelőségét, de nem garantálja automatikusan.

## <a name="intelligent-database-monitoring-and-maintenance-after-migration"></a>Intelligens adatbázis-figyelés és karbantartás áttelepítés után

Miután áttelepítette az adatbázist az SQL Database-be, figyelni szeretné az adatbázist (például ellenőrizni szeretné, hogy az erőforrás-kihasználtság hogyan hasonló vagy a DBCC-ellenőrzések), és rendszeres karbantartást kell végeznie (például újra építeni vagy átszervezni az indexeket, statisztikákat stb.). Szerencsére az SQL Database intelligens abban az értelemben, hogy a korábbi trendeket és rögzített mutatókat és statisztikákat használja az adatbázis figyeléséhez és karbantartásához, hogy az alkalmazás mindig optimálisan futjon. Bizonyos esetekben az Azure SQL DB automatikusan el tudja végezni a karbantartási feladatokat a konfiguráció beállításától függően. Az adatbázis sql-adatbázisban való figyelésének három aspektusa van:

- Teljesítményfigyelés és -optimalizálás.
- Biztonsági optimalizálás.
- Költségoptimalizálás.

### <a name="performance-monitoring-and-optimization"></a>Teljesítményfigyelés és -optimalizálás

A Query Performance Insights segítségével személyre szabott javaslatokat kaphat az adatbázis-számítási feladatokhoz, hogy az alkalmazások továbbra is optimális szinten futhassanak – mindig. Azt is beállíthatja, hogy ezek a javaslatok automatikusan alkalmazásra kerüljenek, és ne kelljen karbantartási feladatokat elvégeznie. Az Index Advisor segítségével automatikusan megvalósíthatja az indexjavaslatokat a számítási feladatok alapján – ezt nevezzük automatikus hangolásnak. A javaslatok az alkalmazás számítási feladatának változásával változnak, hogy a legrelevánsabb javaslatokat nyújtsák. Azt is kap a lehetőséget, hogy manuálisan felülvizsgálja ezeket a javaslatokat, és alkalmazza azokat saját belátása szerint.  

### <a name="security-optimization"></a>Biztonsági optimalizálás

Az SQL Database végrehajtható biztonsági javaslatokat nyújt az adatok és a fenyegetésészlelés biztonságossá tételéhez az adatbázis potenciális szálát jelentő gyanús adatbázis-tevékenységek azonosításához és kivizsgálásához. [A biztonsági rés felmérése](sql-vulnerability-assessment.md) egy adatbázis-vizsgálati és -jelentési szolgáltatás, amely lehetővé teszi az adatbázisok biztonsági állapotának nagy mértékű figyelését, a biztonsági kockázatok azonosítását és az Ön által meghatározott biztonsági alapkonfigurációtól való eltolódást. Minden vizsgálat után egy testre szabott listát a végrehajtható lépések és szervizelési parancsfájlok áll rendelkezésre, valamint egy értékelési jelentést, amely segítségével a megfelelőségi követelmények teljesítéséhez.

Az Azure Security Center segítségével azonosíthatja a biztonsági javaslatokat, és egyetlen kattintással alkalmazhatja azokat.

### <a name="cost-optimization"></a>Költségoptimalizálás

Az Azure SQL platform elemzi a kihasználtsági előzményeket a kiszolgáló adatbázisaiban, hogy kiértékelhesse és javasolhatja a költségoptimalizálási lehetőségeket. Ez az elemzés általában két hetet vesz igénybe, hogy elemezze és felépítse a végrehajtható ajánlásokat. Rugalmas medence az egyik ilyen lehetőség. Az ajánlás a portálon szalagcímként jelenik meg:

![rugalmas készlet ajánlások](./media/sql-database-manage-after-migration/elastic-pool-recommendations.png)

Ezt az elemzést az "Advisor" részben is megtekintheti:

![rugalmas medence ajánlások-tanácsadó](./media/sql-database-manage-after-migration/advisor-section.png)

### <a name="how-do-i-monitor-the-performance-and-resource-utilization-in-sql-database"></a>Hogyan figyelhetem a teljesítményt és az erőforrás-kihasználtságot az SQL Database-ben?

Az SQL Database-ben kihasználhatja a platform intelligens elemzési adatait a teljesítmény figyeléséhez és ennek megfelelően hangolására. Az SQL Database teljesítmény- és erőforrás-kihasználtságát a következő módszerekkel figyelheti:

#### <a name="azure-portal"></a>Azure portál

Az Azure Portalon az adatbázis kihasználtsága az adatbázis kiválasztásával, majd az áttekintő ablaktáblán a diagramra kattintva jeleníti meg. Módosíthatja a diagramot, hogy több metrika jelenjen meg, beleértve a PROCESSZOR százalékos arányát, a DTU-százalékot, az adatok I/o-százalékát, a munkamenetek százalékos arányát és az adatbázis méretszázalékát.

![Figyelési diagram](./media/sql-database-manage-after-migration/monitoring-chart.png)

![Figyelési diagram2](./media/sql-database-manage-after-migration/chart.png)

Ebből a diagramból erőforrásonként is konfigurálhatja a riasztásokat. Ezek a riasztások lehetővé teszik, hogy válaszoljon az erőforrás-feltételekre egy e-mailben, írjon egy HTTPS/HTTP-végpontra, vagy hajtson végre egy műveletet. További információt a [Riasztások létrehozása című témakörben talál.](sql-database-insights-alerts-portal.md)

#### <a name="dynamic-management-views"></a>Dinamikus felügyeleti nézetek

Lekérdezheti a [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) dinamikus felügyeleti nézetet az utolsó óra erőforrás-felhasználási statisztikáinak előzményeinek visszaadásához, és a [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) rendszerkatalógus nézetet az elmúlt 14 nap előzményeinek visszaadására.

#### <a name="query-performance-insight"></a>Lekérdezési terheléselemző

[A Lekérdezési teljesítmény betekintés](sql-database-query-performance.md) lehetővé teszi, hogy egy adott adatbázis leggyakoribb erőforrás-fogyasztó lekérdezéseinek és hosszú ideig futó lekérdezéseinek előzményeit tekintse meg. Gyorsan azonosíthatja a TOP-lekérdezéseket az erőforrás-kihasználtság, az időtartam és a végrehajtás gyakorisága szerint. Nyomon követheti a lekérdezéseket, és észlelheti a regressziót. Ehhez a szolgáltatáshoz engedélyezni kell a [Query Store-t,](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) és aktívnak kell lennie az adatbázisban.

![Lekérdezési terheléselemző](./media/sql-database-manage-after-migration/query-performance-insight.png)

#### <a name="azure-sql-analytics-preview-in-azure-monitor-logs"></a>Azure SQL Analytics (előzetes verzió) az Azure Monitor naplóiban

[Az Azure Monitor naplói](../azure-monitor/insights/azure-sql.md) lehetővé teszik az Azure SQL-adatbázis teljesítménymutatóinak összegyűjtését és megjelenítését, munkaterületenként akár 150 000 SQL-adatbázis és 5000 SQL elastic készlet támogatása érdekében. Segítségével figyelheti és fogadhatja az értesítéseket. Több Azure-előfizetés és rugalmas készletmetrikák figyelheti az SQL Database-t és a rugalmas készleteket, és az alkalmazásverem minden rétegében problémák azonosítására használható.

### <a name="i-am-noticing-performance-issues-how-does-my-sql-database-troubleshooting-methodology-differ-from-sql-server"></a>Teljesítményproblémákat kapok: Miben különbözik az SQL Database hibaelhárítási módszere az SQL Server től?

A lekérdezési és adatbázis-teljesítménnyel kapcsolatos problémák diagnosztizálására használt hibaelhárítási technikák jelentős része változatlan marad. Elvégre ugyanaz az SQL Server motor működteti a felhő. Azonban a platform - Az Azure SQL DB beépített "intelligencia". Ez segít a hibaelhárításban és a teljesítményproblémák még könnyebb diagnosztizálásában. Ezen korrekciós intézkedések némelyikét az Ön nevében is végrehajthatja, és bizonyos esetekben proaktív módon javíthatja azokat - automatikusan.

A teljesítményproblémák elhárításának megközelítése jelentősen hasznos lehet az olyan intelligens funkciók használatával, mint a [Query Performance Insight (QPI)](sql-database-query-performance.md) és az [Adatbázis-tanácsadó](sql-database-advisor.md) együtt, így a módszertani különbség ebben a tekintetben eltérő – már nem kell elvégeznie a manuális munkát az alapvető részletek kiőrlésével, amelyek segíthetnek a szóban forgó probléma megoldásában. A platform nem a kemény munka az Ön számára. Ennek egyik példája a QPI. A QPI segítségével a lekérdezés imént végigfúrhat, és megnézheti a korábbi trendeket, és kitalálhatja, hogy pontosan mikor regressziós a lekérdezés. Az adatbázis-tanácsadó ajánlásokat ad olyan dolgokkal kapcsolatban, amelyek segíthetnek az általános teljesítmény általános javításában általában, például a hiányzó indexek, az indexek eldobása, a lekérdezések paraméterezése stb.

A teljesítményhibaelhárítás, fontos annak megállapítása, hogy csak az alkalmazás vagy az adatbázis támogatja azt, amely hatással van az alkalmazás teljesítményét. Gyakran a teljesítmény probléma az alkalmazás rétegében rejlik. Ez lehet az architektúra vagy az adatelérési minta. Tegyünk példáha egy beszédes alkalmazást, amely érzékeny a hálózati késésre. Ebben az esetben az alkalmazás szenved, mert nem lenne sok rövid kérelmek megy oda-vissza ("beszédes") között az alkalmazás és a kiszolgáló és a zsúfolt hálózat, ezek a körbe-vissza utak gyorsan összeadódnak. Ebben az esetben a teljesítmény javítása érdekében használhatja a [Kötegelt lekérdezéseket.](sql-database-performance-guidance.md#batch-queries) A kötegek használata óriási segítséget nyújt, mert mostantól a kérelmek feldolgozása egy kötegben lesz; így segít csökkenteni a retúr késést, és javítja az alkalmazás teljesítményét.

Emellett ha az adatbázis általános teljesítményének romlását észleli, a PROCESSZOR, az IO és a memóriafelhasználás megismerése érdekében figyelheti a [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) és a [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) dinamikus felügyeleti nézeteket. A teljesítmény valószínűleg hatással volt, mert az adatbázis ki van éhezve az erőforrások. Előfordulhat, hogy módosítania kell a számítási méretet és/vagy a szolgáltatási szintet a növekvő és csökkenő munkaterhelési igények alapján.

A teljesítményhangolással kapcsolatos javaslatok átfogó készletét a Következő témakörben [tetszés: Az adatbázis finomhangolása](sql-database-performance-guidance.md#tune-your-database)című témakörben található.

### <a name="how-do-i-ensure-i-am-using-the-appropriate-service-tier-and-compute-size"></a>Hogyan győződhetek meg arról, hogy a megfelelő szolgáltatási szintet és számítási méretet használom?

Az SQL Database különböző szolgáltatási szinteket kínál alapszintű, standard és prémium szintű. Minden egyes szolgáltatási szint kap egy garantált kiszámítható teljesítmény kötődik az adott szolgáltatási szinthez. A számítási feladatoktól függően előfordulhat, hogy a tevékenység, ahol az erőforrás-kihasználtság előfordulhat, hogy elérje a felső határa az aktuális számítási méret, amelyben van. Ilyen esetekben célszerű először annak értékelésével kezdeni, hogy egy hangolás segíthet-e (például index hozzáadása vagy módosítása stb.). Ha továbbra is korlátozási problémákat tapasztal, fontolja meg egy magasabb szolgáltatási szintre vagy számítási méretre való áttérést.

|**Szolgáltatásszint**|**Gyakori használati esetek forgatókönyvei**|
|---|---|
|**Basic**|Alkalmazások egy maroknyi felhasználó és egy adatbázis, amely nem rendelkezik a magas egyidejűségi, méretezési és teljesítménykövetelményekkel. |
|**Standard**|A jelentős egyidejűségi, méretarányos és teljesítményigényű alkalmazások, valamint az alacsony és közepes I/O-igények párosulnak. |
|**Prémium**|Alkalmazások sok egyidejű felhasználóval, magas CPU/memória és magas i/o-igény. A magas egyidejűség, a nagy átviteli képesség és a késésérzékeny alkalmazások kihasználhatják a prémium szintet. |
|||

Győződjön meg arról, hogy a megfelelő számítási méret, figyelheti a lekérdezés és az adatbázis-erőforrás-felhasználás a fent említett módok egyikén keresztül a "Hogyan figyelhetem a teljesítmény és az erőforrás-használat az SQL Database". Ha úgy találja, hogy a lekérdezések / adatbázisok folyamatosan fut a CPU/ memória, stb, érdemes lehet skálázás akár egy nagyobb számítási méret. Hasonlóképpen, ha figyelembe vesszük, hogy még a csúcsidőben, úgy tűnik, nem használja a forrásokat annyira; fontolja meg az aktuális számítási mérettől való leskálázást.

Ha egy SaaS-alkalmazás minta vagy egy adatbázis-konszolidációs forgatókönyv, fontolja meg egy rugalmas készlet költségoptimalizálási használata. Rugalmas készlet egy nagyszerű módja annak, hogy adatbázis konszolidáció és a költség-optimalizálás. Ha többet szeretne tudni arról, hogy miként kezelhet több adatbázist rugalmas készlethasználatával, olvassa el a [Készletek és adatbázisok kezelése témakört.](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases)

### <a name="how-often-do-i-need-to-run-database-integrity-checks-for-my-database"></a>Milyen gyakran kell adatbázisintegritás-ellenőrzést futnom az adatbázisomhoz?

Az SQL Database olyan intelligens technikákat használ, amelyek lehetővé teszik az adatsérülés bizonyos osztályainak automatikus és adatvesztés nélküli kezelését. Ezek a technikák be vannak építve a szolgáltatásba, és szükség esetén a szolgáltatás kihasználja őket. Rendszeresen, az adatbázis biztonsági mentések a szolgáltatás on-val tesztelik azok visszaállításával és a DBCC CHECKDB futtatásával rajta. Ha problémák merülnek fel, az SQL Database proaktív módon foglalkozik velük. [Az automatikus oldaljavítás](/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring) a sérült vagy adatintegritási problémákkal bíró oldalak javításához hasznosul. Az adatbázislapok ellenőrzése mindig a lap integritását ellenőrző alapértelmezett CHECKSUM beállítással történik. Az SQL Database proaktív módon figyeli és áttekinti az adatbázis adatintegritását, és ha problémák merülnek fel, a legmagasabb prioritással kezeli őket. Ezeken kívül dönthet úgy is, hogy saját integritási ellenőrzéseit is saját akarata szerint futtatja.  További információ: [Data Integrity in SQL Database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)

## <a name="data-movement-after-migration"></a>Adatáthelyezés áttelepítés után

### <a name="how-do-i-export-and-import-data-as-bacpac-files-from-sql-database"></a>Hogyan lehet adatokat exportálni és importálni BACPAC-fájlként az SQL Adatbázisból?

- **Exportálás**: Az Azure SQL-adatbázist BACPAC-fájlként exportálhatja az Azure Portalról

   ![adatbázis exportálása](./media/sql-database-export/database-export1.png)

- **Importálás:** Az Azure Portalhasználatával BACPAC-fájlként is importálhat adatokat az adatbázisba.

   ![adatbázis importálása](./media/sql-database-import/import1.png)

### <a name="how-do-i-synchronize-data-between-sql-database-and-sql-server"></a>Hogyan szinkronizálható az adatok az SQL Database és az SQL Server között?

Ennek elérésére többféleképpen is el lehet érni:

- **[Adatszinkronizálás](sql-database-sync-data.md)** – Ez a szolgáltatás segít az adatok kétirányú szinkronizálásában több helyszíni SQL Server-adatbázis és az SQL Database között. A helyszíni SQL Server-adatbázisokkal való szinkronizáláshoz telepítenie és konfigurálnia kell a szinkronizálási ügynököt egy helyi számítógépen, és meg kell nyitnia az 1433-as kimenő TCP-portot.
- **[Tranzakcióreplikáció](https://azure.microsoft.com/blog/transactional-replication-to-azure-sql-database-is-now-generally-available/)** – A tranzakció replikációs szinkronizálhatja az adatokat a helyszíni Azure SQL DB a helyszíni, hogy a gyártó és az Azure SQL DB, hogy az előfizető. Egyelőre csak ez a beállítás támogatott. Ha többet szeretne tudni arról, hogy miként telepítheti át az adatokat a helyszíni sql-ről az Azure SQL-be minimális állásidővel, olvassa el a következő témakört: [Tranzakcióreplikáció használata](sql-database-single-database-migrate.md#method-2-use-transactional-replication)

## <a name="next-steps"></a>További lépések

További információ az [SQL Database adatbázisról.](sql-database-technical-overview.md)
