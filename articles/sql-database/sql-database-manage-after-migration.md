---
title: "Áttelepítés – az Azure SQL Database után kezelése |} Microsoft Docs"
description: "Útmutató: az adatbázis kezelése az Azure SQL Database-áttelepítés után."
services: sql-database
documentationcenter: 
author: joesackmsft
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Inactive
ms.date: 10/14/2016
ms.author: Joe.Sack
ms.suite: sql
ms.prod_service: sql-database
ms.component: migration
ms.openlocfilehash: e562c33cabc7d39d1f6a911c21343f85da205c0b
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2017
---
# <a name="how-should-i-manage-my-azure-sql-database-after-migration"></a>Hogyan kell kezelni az áttelepítés után az Azure SQL adatbázis?

*Gyakori kérdések körül kezelése az Azure SQL Database beruházások értékét*

Ezért nemrég áthelyezte SQL Server-adatbázisok Azure SQL Database, vagy esetleg arra készül, nagyon rövid időn áthelyezni. Miután áthelyezte, mi az a következő? Fényében, hogy az SQL-adatbázis egy *Platformszolgáltatást*, a Microsoft kezeli az Ön nevében több területet. De pontosan hogyan ez változik a vállalat eljárásokat, például a biztonsági, az üzletmenet folytonossága, adatbázis-karbantartási, teljesítményhangolás, figyelés és több kulcsfontosságú terület körül? 

Ez a cikk célja röviden rendszerezéséhez az erőforrások és útmutatást az SQL-adatbázis befektetéseit kezelése a shift tételére lesz szüksége. Ebben a cikkben a főbb területet fedik le, az üzletmenet folytonossága, a biztonsági, az adatbázis-karbantartási és a figyelés, a teljesítmény- és adatok mozgása. Oktatóanyag a következőket ismerteti, amelyek különböznek az SQL Server és SQL-adatbázis, meghívják a kimenő gyakorlati tanácsok az üzemeltetéshez, amelyek segítségével maximalizálhatja az előnyöket, és minimálisra kulcsfontosságú terület. 

## <a name="manage-business-continuity-after-migration"></a>Üzleti folytonosság kezelése az áttelepítés után

### <a name="how-do-i-create-and-manage-backups-on-sql-database"></a>Hogyan létrehozása és kezelése az SQL-adatbázis a biztonsági mentések? 
SQL-adatbázis automatikusan-adatbázisok biztonsági mentését, és a megőrzési időtartam időpont bármely visszaállítása lehetővé. A megőrzési idő 35 napon Standard és Premium adatbázisok és az alapszintű adatbázisok 7 nap. Emellett a hosszú távú megőrzési funkció lehetővé teszi a biztonságimásolat-fájlok tárolására hosszabb időt (akár 10 év), és állítsa vissza a biztonsági mentése bármely pontján. Továbbá az adatbázisok biztonsági másolatain, gondoskodjon róla, hogy minden régióban georedundáns Helyreállítás katasztrófa vagy regionális katasztrófa esetén történő georeplikált. Lásd: [üzleti folytonosság – áttekintés](sql-database-business-continuity.md).

### <a name="how-do-i-ensure-business-continuity-in-the-event-of-a-datacenter-level-disaster-or-a-regional-catastrophe"></a>Hogyan hajtsa végre a datacenter-szintű katasztrófa vagy regionális katasztrófa esetén az üzletmenet folytonosságának biztosításához? 

Adatbázis biztonsági mentése, gondoskodjon róla, hogy minden régióban georedundáns Helyreállítás katasztrófa vagy regionális katasztrófa esetén történő georeplikált. Lásd: [üzleti folytonosság – áttekintés](sql-database-business-continuity.md). Ezenkívül az SQL-adatbázis lehetővé teszi a karbantartása aktívan georeplikált másodlagos adatbázisok egy másik régióban. Az automatikus feladatátvételt csoport konfigurálásával biztosítja, hogy az adatbázisok automatikusan áthelyezze a feladatokat a másodlagos a vészhelyreállítási forgatókönyvek. Ha az automatikus feladatátvételt csoportja van konfigurálva, majd az alkalmazást kell aktívan figyeli, hogy egy olyan vészhelyzet esetén, és kezdeményezze a feladatátvételt a másodlagos. 
### <a name="sql-server-provided-me-readable-secondary-replicas-can-i-access-the-secondaries-on-sql-database"></a>SQL Server megadott me olvasható másodlagos Másodpéldányokra, a másodlagos SQL-adatbázis elérhető? 

Igen, az aktív Georeplikáció funkció olvasható másodlagos replika létrehozásához használt. 

### <a name="how-does-my-disaster-recovery-plan-change-from-on-premise-to-sql-database"></a>Hogyan a vész-helyreállítási terv változik a helyszíni SQL Database? 
SQL Server-hitelesítés megvalósításához szükséges aktívan kezelheti a biztonsági mentések elérhető szolgáltatások, például a Feladatátvételi fürtszolgáltatás, az adatbázis-tükrözés, replikáció, a Naplóküldés vagy egyszerű eredeti BACPAC biztonsági mentéseket. Azonban az SQL-adatbázis biztonsági mentések teljes mértékben a Microsoft által felügyelt, és konfigurálva, és néhány kattintással használata az Azure-portál (vagy a PowerShell néhány parancsok) biztonsági mentési és vész-helyreállítási tervek csak lehet. ‌
### <a name="in-the-event-of-disaster-how-do-i-recover-my-databases"></a>Katasztrófa, esetén hogyan tegye helyreállíthatók a adatbázisok? 
SQL-adatbázis automatikusan lehetővé teszi az adatbázisok visszaállítása bármely pontra az elmúlt 35 napon időpont. Ez a lehetőség az adatvesztés, vagy az alkalmazással kapcsolatos vész szembesülhetnek. 

Abban az esetben, ha egy olyan regionális vészhelyzet esetén, akkor szembesülhetnek, ha a georeplikált másodlagos adatbázisok vannak konfigurálva, akkor helyreállíthatja a földrajzi-másodlagos adatbázisból egy másik régióban. Az alkalmazások valós idejű hozzáférés átveheti a többi régiónál a földrajzi-másodlagos manuálisan. Azt is megteheti Ha automatikus-feladatátvételi csoport van konfigurálva, a feladatátvételi a földrajzi másodlagos automatikusan történik, egy vészhelyreállítási forgatókönyvhöz. Ha nem rendelkezik beállított georeplikált másodlagos adatbázishoz, továbbra is helyreállíthatja az adatbázisokat automatikus replikált biztonsági mentés fájljaiból (beépített funkcióval, nincs szükség konfigurációs), viszonylag hosszabb helyreállítási idő (12 óra RTO), és legfeljebb egy óra adatvesztés. 

### <a name="are-the-failovers-to-secondary-transparent-how-does-my-application-handle-database-failovers"></a>A feladatátvételi tesztek másodlagos átlátszóak? Hogyan alkalmazás adatbázis feladatátvétel kezelni? 
Ha automatikus feladatátvételt-csoportokhoz, a feladatátvételt a másodlagos az transzparens. Azonban ha nem rendelkezik, majd az alkalmazás kell tartalmaznia a figyelheti az elsődleges rendelkezésre állását, és majd kézi feladatátvételt a másodlagos logika. 
 
## <a name="manage-security-after-migration"></a>Az áttelepítés után a biztonság kezeléséhez

### <a name="how-can-i-restrict-access-to-my-sql-database"></a>Hogyan lehet hozzáférés korlátozása az SQL-adatbázis? 
 
Néhány módon való zárolását, az SQL-adatbázisok kapcsolat elérésére. 
1. Korlát forgalmát az Internet Express Route dedikált fiber biztosít az Azure-hálózatot, hogy az adatok nem használható az interneten keresztül. Akkor is közötti régió kapcsolat expressroute segítségével konfigurálhatja. Az alábbi hivatkozások Express Route részletesebben ismertetik: 
 - [Az Expressroute bemutatása](../expressroute/expressroute-introduction.md)
 - [Előfeltételek](../expressroute/expressroute-prerequisites.md) 
 - [Munkafolyamatok](../expressroute/expressroute-workflows.md) 
 
2. Válassza ki, mely erőforrásokat csatlakozzon az SQL Database: 

   Alapértelmezés szerint az SQL-adatbázis mérete úgy konfigurálva, hogy "Az összes Azure-szolgáltatások engedélyezése" – Ez azt jelenti, hogy minden virtuális gép az Azure-ban is próbál kapcsolódni az adatbázishoz.  Minden bejelentkezések továbbra is hitelesíteniük kell. Ha nem szeretné az összes Azure IP-címek érhető el az adatbázist, tiltsa le a "Az összes Azure-szolgáltatások engedélyezése", és használjon [VNET Szolgáltatásvégpontok](sql-database-vnet-service-endpoint-rule-overview.md) bejövő hozzáféréssel az adatbázishoz csak Azure-erőforrások belüli korlátozhatja, hogy egy adott Az Azure VNET alhálózat. 

   ![Virtuális hálózat Szolgáltatásvégpontok](./media/sql-database-manage-after-migration/vnet-service-endpoints.png) 

   Egy másik lehetőség egy kiépítése [fenntartott IP-címek](../virtual-network/virtual-networks-reserved-public-ip.md) a virtuális gépek és az engedélyezési lista azokat adott virtuális gép IP-címek, a kiszolgáló a tűzfalbeállítások. (Lásd az alábbi képernyőfelvételen az Azure portálon példaként). Fenntartott IP-címek hozzárendelése, mentse a nem a tűzfalszabályok az IP-címek módosításához frissíteni kell. 

3. Kiküszöbölése kívül Azure 1433-as port

   Futtassa az SSMS Azure használatával [Azure RemoteApp](https://www.microsoft.com/cloud-platform/azure-remoteapp-client-apps). Ehhez nem szükséges, hogy nyissa meg a 1433-as port kimenő kapcsolatok az IP-cím statikus, ezért lehet, hogy az adatbázis csak a RemoteApp meg van nyitva, akkor támogatja a többszörös hitelesítés (MFA) többtényezős hitelesítési és többfelhasználós. 

### <a name="what-authentication-methods-are-offered-in-sql-database"></a>SQL-adatbázis hitelesítési módszerek érhető el?

A fő hitelesítési módszereket kínál az SQL-adatbázis és az SQL Data Warehouse: Azure Active Directory-hitelesítés és SQL-hitelesítést. Az Azure Active Directory (AD) egy olyan központi identitások és hozzáférések felügyeleti szolgáltatás, és SQL csak az Azure ad-vel integrált számos Azure szolgáltatás. Egy központi felügyelt szolgáltatás előnye, hogy egy felhasználó hitelesítő adatainak által megosztott egyszerűbb hitelesítéshez használandó összes Azure-szolgáltatásokhoz. Ez is lehetővé teszi, hogy SQL-adatbázis és az SQL Data Warehouse a többtényezős hitelesítést és a Vendég felhasználói fiókok Azure AD-tartományban. 

Ha már rendelkezik egy Active Directory helyszíni, akkor is vonható össze, az Azure Active Directory segítségével terjed ki a címtár az Azure directory. 


|||
|---|---|
| Ha Ön…|Az Azure SQL adatbázis Azure SQL Data Warehouse /|
| Nem kíván használni az Azure Active Directory (AD) az Azure-ban|Használjon [SQL-hitelesítés](sql-database-security-overview.md)|
| Helyszíni SQL-kiszolgálón használt AD|[AD az Azure ad-val összevonni](../active-directory/connect/active-directory-aadconnect.md), és az Azure AD-alapú hitelesítés használata. Ennek használhatja az egyszeri bejelentkezés.|
| Érvényesíteniük kell a többtényezős hitelesítés (MFA)|Házirend-keresztül megkövetelő [Microsoft feltételes hozzáférés](sql-database-conditional-access.md), és használja [Azure AD univerzális authentication, MFA-támogatással rendelkező](sql-database-ssms-mfa-authentication.md).|
| Vendég fiókot a Microsoft-fiókok (live.com, outlook.com) vagy más tartományok (gmail.com)|Használjon [Azure AD univerzális hitelesítési](sql-database-ssms-mfa-authentication.md) SQL adatbázis vagy adatraktár, amely kihasználja a [Azure AD B2B együttműködés](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).|
| A rendszer naplózza Windows Azure AD hitelesítő adatait használja az összevont tartományból|Használjon [az Azure AD integrált hitelesítés](sql-database-aad-authentication-configure.md).|
| A rendszer naplózza Windows hitelesítő adatokkal nem összevont Azure-ral tartomány|Használjon [az Azure AD-jelszó-hitelesítés](sql-database-aad-authentication-configure.md).|
| A középső rétegbeli szolgáltatásokat, amelyhez csatlakoznia kell az Azure SQL Database vagy az adatraktár|Használjon [az Azure AD-tokent használó hitelesítés](sql-database-aad-authentication-configure.md).
|||

### <a name="how-can-i-limit-access-to-sensitive-data-in-my-databases-from-the-application-side"></a>Hogyan lehet korlátozni hozzáféréssel a bizalmas adatokhoz a adatbázisokban alkalmazás oldaláról? 

Hogy illetéktelen felhasználók nem tudnak az érzékeny adatokat, mindössze néhány lehetőség áll rendelkezésre SQL-adatbázisban elérhető: 

- [Mindig titkosítja](/sql/relational-databases/security/encryption/always-encrypted-database-engine) olyan ügyféloldali titkosítás, mely az adatbázis-és nagybetűket oszlopok titkosítja (úgy, hogy azok az adatbázis-rendszergazdák és a jogosulatlan felhasználók számára a titkosított szöveg) típusú. A kulcs mindig titkosítja az ügyféloldalon tárolja, így csak az arra jogosult ügyfelek vissza tudja fejteni a bizalmas oszlopok. Mindig titkosított támogatja egyenlőségi összehasonlítást ma, így DBAs továbbra is a titkosított oszlopokban lekérdezni az SQL-parancsok részeként. Mindig titkosított használható számos tárolt kulcs lehetőséggel, például a [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md), Windows tanúsítványtárolójába, és helyi hardveres biztonsági modulok.
- [A dinamikus Adatmaszkolás](sql-database-dynamic-data-masking-get-started.md) által maszkolás a jogosulatlan felhasználók az alkalmazási rétegre a bizalmas adatok veszélyeztetettségének korlátozó adatok maszkolása szolgáltatása. Megadhatja egy maszkolási szabályra, amely egy maszkolási mintát hozhat létre (például csak megjelenítendő utolsó 4 számjegyével nemzeti azonosítója, és jelölje meg a többi, x-ek), és azonosíthatja, hogy mely felhasználók kizárhatók a maszkolási szabályra.
- [A sorszintű biztonsági](/sql/relational-databases/security/row-level-security) szabályozható a hozzáférést egy adatbázis tábla sorainak lehetővé teszi, hogy a felhasználó, a lekérdezés (csoport tagsági vagy a végrehajtási környezet) alapján. A hozzáférés korlátozása az adatbázis-rétegből ahelyett, hogy egy alkalmazás rétegben, egyszerűbbé teheti a app logika történik. 

### <a name="what-encryption-options-do-i-have-in-sql-database-and-what-actors-does-the-encryption-protect-from"></a>Milyen titkosítási lehetőségek állnak rendelkezésre az SQL-adatbázis, és milyen szereplője nyújt a titkosítási védelmet a?
SQL-adatbázisban elérhető három fő titkosítási technológiák van: 
- [Mindig titkosítja](/sql/relational-databases/security/encryption/always-encrypted-database-engine) (amely szerepel a fenti kérdés): bizalmas oszlopok a tábla teljes körű, illetéktelen ügyfelekről a fizikai lemezre titkosítja. A kiszolgáló és az adatok a rendszergazdák nem látható a bizalmas adatokat, mivel a titkosítási kulcsok tárolása az ügyfélen. 
- [Az átlátható adattitkosítási](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) (TDE): titkosítását, amely az adatbázis szintjén titkosítja, és az adatfájlokat, a naplófájlok és a társított biztonsági másolatok védelmet nyújt a fizikai adathordozó lopás elleni védelmét. Az újonnan létrehozott összes adatbázisra alapértelmezés szerint engedélyezve van a TDE.
 
  Az alábbi ábra a technológia lehetőségek a titkosítási áttekintését tartalmazza.

   ![Titkosítási áttekintése](./media/sql-database-manage-after-migration/overview-encryption.png)

### <a name="how-should-i-manage-encryption-keys-in-the-cloud"></a>Hogyan kezelje a titkosítási kulcsokat a felhőben? 
(Ügyféloldali titkosítás) mindig titkosítja és átlátható adattitkosítási (aktívan titkosítása) kulcskezelés lehetőség áll rendelkezésre. Titkosítási kulcsok megváltoztatása rendszeresen javasoljuk, és gyakorisággal, amely igazodik belső szabályozások és a megfelelőségi követelményeknek.

- **Mindig titkosítja**: van egy [két kulcs hierarchia](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted) mindig titkosítja - a bizalmas adatok oszlop egy AES 256 oszlop titkosítási kulcsának (CEK), amely viszont titkosított oszlop főkulccsal (CMK) titkosítja. Az ügyfél-illesztőprogramok előírt mindig titkosítja a CMKs hossza nem korlátozásokkal rendelkezik.

  A CEK titkosított értékét az adatbázisban tárolja, és a CMK tárolása egy megbízható kulcs, például Windows tanúsítványtárolójába, az Azure Key Vault vagy egy hardveres biztonsági modul. 
  
  Mindkét a [CEK és CMK](/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell) forgatható el. CEK Elforgatás lehet időigényes a titkosított oszlopokat tartalmazó táblák méretétől függően. Ezért alaposan tervezze CEK Forgatás műveletek. CMK elforgatási, másrészt nem ütközik az adatbázis teljesítményét, és az elkülönített szerepkörök is végezhető.

  Az alábbi ábrán látható a kulcstároló-beállításait az oszlop főkulcsok mindig titkosítja a 

   ![Mindig titkosítja a CMK tárolására szolgáltatók](./media/sql-database-manage-after-migration/always-encrypted.png)

- **Transzparens Data Encryption (TDE)**: a két kulcs hierarchiában van TDE – szimmetrikus AES-256 adatbázis egyedi adatbázis-titkosítási kulcs (adattitkosítási kulcs), amely pedig a rendszer titkosítja a kiszolgáló egyedi aszimmetrikus RSA titkosítja az adatokat az egyes felhasználói adatbázisban 2048 főkulcs. 

  Alapértelmezés szerint a főkulcs átlátható adattitkosítási kezeli az SQL Database szolgáltatás kényelmét szolgálja. Ha a szervezet céljai között a főkulcs szabályozhatják, hogyan szeretné van [Azure Key Vault](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql) mint a kulcstároló. 

  Az Azure Key Vault segítségével a szervezet azt feltételezi, hogy ellenőrzése alatt tartja a kulcs kiépítés, elforgatás és engedéllyel szabályozza. [Elforgatás vagy váltás TDE főkulcs típusú](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql-key-rotation) gyors,, akkor csak újból titkosítja az adattitkosítási kulcsot. 

  A biztonsági és adatkezelés közötti szerepek elkülönítése rendelkező szervezetek számára egy biztonsági rendszergazdai sikerült kiépíteni az Azure Key Vault TDE főkulcs kulcsokat tárol, és adja meg a használandó adatbázis-rendszergazda az Azure Key Vault kulcsazonosító egy kiszolgálón titkosítását. 

## <a name="monitoring-and-compliance-after-migration"></a>Figyelés és megfelelőség áttelepítés után

### <a name="how-do-i-monitor-database-activities-in-sql-database"></a>Hogyan figyelhetek az SQL-adatbázis adatbázis-tevékenységek?
Van néhány SQL Database-be beépített funkcióinak figyelés, amely nyomon biztonsági és egyéb események az adatbázishoz:
- [SQL-naplózás](sql-database-auditing.md) gyűjthet az adatbázissal kapcsolatos eseményeket a saját Azure-tárfiókban lévő naplókat.
- [A Fenyegetésészlelés SQL](sql-database-threat-detection.md) szolgáltatással észlelheti a gyanús tevékenységeket utaló esetleges rosszindulatú behatolással szemben szeretne használni, megszegi vagy kihasználhatja az adatbázisban. SQL-adatbázis Fenyegetésészlelés algoritmusokban, ami észleli a potenciális biztonsági réseket és az SQL injektálási támadások, valamint az adatbázis rendellenes hozzáférési minták (például egy szokatlan helyről, vagy egy ismeretlen tag hozzáférés) több példányban fut. Biztonsági tisztviselő vagy más kijelölt rendszergazdák e-mail értesítést kapni, amennyiben a fenyegetést észlel az adatbázis. Minden értesítés további vizsgálata, és a fenyegetések mérséklésére kapcsolatos a gyanús tevékenységeket és ajánlások részleteit tartalmazza. 
- [SQL biztonsági réseinek értékelése](sql-vulnerability-assessment.md) átvizsgálja és szolgáltatás, amely lehetővé teszi léptékű az adatbázisok biztonsági állapotának figyelése és a biztonsági kockázatok azonosítása és az Ön által megadott biztonsági alaptervet eltolódás adatbázis. Minden vizsgálat után végrehajtandó lépéseket és javítási parancsfájlok testreszabott listájának biztosított, valamint segít abban, hogy megfeleljenek a megfelelőségi használható értékelési jelentést. 
- [SQL-OMS biztonsági alkalmazás szinkronizálása](https://github.com/Microsoft/Azure-SQL-DB-auditing-OMS-integration) Operations Management Suite (OMS) a nyilvános API felületeket, amelyekkel SQL naplók OMS naplóelemzési és határozhatnak meg egyéni észlelési riasztásokat, beleértve a használja: 
 - Az SQL adatbázishoz Audit csempe & irányítópult, amely az adatbázis-tevékenységeket világos és összefüggő jelentést. 
 - A Naplóelemzési SQL adatbázis-tevékenység elemzéséhez, és vizsgálja meg az eltérések és rendellenességek feltárását, biztonsági problémát jelző.
 - Speciális riasztások speciális szabályok a megfigyelt e-mail kiváltó eseményeket, a Webhook és az Azure automation-forgatókönyv (vagyis a jelszó módosítására, after-hours, az adott SQL-parancsok) riasztást küld.
- [Az Azure Security Center](../security-center/security-center-intro.md) központi felügyeleti kínál az Azure, a helyszíni és a többi felhőből futó számítási feladatok között. Megtekintheti e alapvető SQL-adatbázis védelmét, például a naplózás és az átlátható adattitkosítási összes erőforrásra konfigurálva vannak-e, és a saját követelmények alapján házirendeket hozhat létre. 

### <a name="is-sql-database-compliant-with-any-regulatory-requirements-and-how-does-that-help-with-my-own-organizations-compliance"></a>Az SQL-adatbázis bármely szabályozási követelményeknek megfelelő, és hogyan, amely segít a saját szervezet? 
Az Azure SQL Database szabályozó megfelelőségi számos megfelel. Tekintse meg a legújabb, hogy teljesülnek-megfelelőségi, látogasson el a [Microsoft Trust Center](https://www.microsoft.com/trustcenter/compliance/complianceofferings) és a megjelenítéséhez, ha az Azure SQL Database szerepel-e a megfelelő Azure alatt a szervezet számára fontos a megfelelőségi részletek szolgáltatások. Fontos megjegyezni, hogy bár az Azure SQL Database megfelelő szolgáltatásként igazolhatják, akkor a szervezet szolgáltatása megfelelőségi támogatások, de automatikusan garantálja az. 

## <a name="database-maintenance-and-monitoring-after-migration"></a>Adatbázis karbantartásáról és figyeléséről az áttelepítés után

### <a name="how-do-i-monitor-growth-in-data-size-and-resource-utilization"></a>Hogyan figyelhetek az adatok mérete és erőforrás-használat növekedése?

- A figyelő metrikák kapcsolatos az adatbázis méretének és erőforrás-használat a "Figyelés" diagram az Azure-portálon tekintheti meg. 

  ![Figyelés diagramra](./media/sql-database-manage-after-migration/monitoring-chart.png)

- Mélyebb betekintést és részletekbe menően tárhatják fel lekérdezések részleteit, használhat "Lekérdezési Terheléselemző" érhető el az Azure portálon. Ehhez szükséges, hogy a Lekérdezéstár"az adatbázis aktív.

  ![Lekérdezési terheléselemző](./media/sql-database-manage-after-migration/query-performance-insight.png)

- Azt is megteheti, megtekintheti a metrikák túl használatával dinamikus felügyeleti nézetek (dinamikus felügyeleti nézetek) – segítségével [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) és [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database). 

### <a name="how-often-do-i-need-to-run-consistency-checks-like-dbcccheckdb"></a>Milyen gyakran kell végezzen konzisztencia-ellenőrzést, például a DBCC_CHECKDB?
DBCC_CHECKDB az adatbázisban lévő összes objektum logikai és fizikai sértetlenségét ellenőrzi. Már nem kell tennie az ellenőrzések, mert ezek a Microsoft Azure által kezelt. További információkért lásd: [adatintegritás az Azure SQL-adatbázis](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)

## <a name="monitor-performance-and-resource-utilization-after-migration"></a>Az áttelepítés után a teljesítmény- és Erőforrás kihasználtsága

### <a name="how-do-i-monitor-performance-and-resource-utilization-in-azure-sql-database"></a>Hogyan figyelhetek teljesítmény- és erőforrás-használat az Azure SQL Database?
Figyelheti az Azure SQL-adatbázis a következő módszerekkel a teljesítmény- és erőforrás-használat:

- **Azure-portálon**: az Azure portál jelölje ki az adatbázist, a diagram az Áttekintés ablaktábláján kattintson egy önálló adatbázist Erőforrás kihasználtsága jeleníti meg. Módosíthatja a diagram megjelenítése több metrika, beleértve a processzor, DTU százalékos, adat IO százalékos, munkamenetek százalékos és adatbázis méretének százalékában. 
  ![erőforrás-használat](./media/sql-database-manage-after-migration/resource-utilization.png)

  Ez a diagram az erőforrás riasztásokat is konfigurálhatja. Ezek a riasztások engedélyezése egy e-mailt az erőforrás-feltételek válaszolni, HTTPS vagy HTTP-végponttal való írás vagy egy műveletet. Tekintse meg a [Azure SQL Database adatbázis teljesítményének figyelése](sql-database-single-database-monitor.md) részletes információkra van szüksége.

- **Nézetek**: lekérheti a [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) erőforrás-fogyasztás statisztika előzmények visszaadására az elmúlt egy órában dinamikus kezelési nézetet és a [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) katalógus rendszernézet előzmények vissza az elmúlt 14 napban. 

- **Lekérdezési Terheléselemzőhöz**: [lekérdezési Terheléselemző](sql-database-query-performance.md) lehetővé teszi az erőforrás-igényes leggyakoribb lekérdezések és a hosszan futó lekérdezések egy adott adatbázis egy előzményei között találja. A szolgáltatás használatához [Lekérdezéstár](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) engedélyezve van, és az adatbázis aktív.

- **Az Azure SQL elemzés (előzetes verzió) Naplóelemzési**: [Azure Naplóelemzés](../log-analytics/log-analytics-azure-sql.md) legfeljebb 150 000 Azure SQL-adatbázisok és 5000 SQL rugalmas támogatása lehetővé teszi az adatgyűjtésre és az Azure SQL Azure fő teljesítménymutatók megjelenítéséhez Készletek / munkaterületen. Használhatja a figyelheti és értesítéseket kaphat. Figyelheti az Azure SQL Database és a rugalmas készlet metrikák több Azure-előfizetések és rugalmas készletek és alkalmazás-készlet minden egyes rétegben problémák azonosításához használható. 

### <a name="how-do-i-ensure-i-am-using-the-appropriate-service-tier-and-performance-level"></a>Hogyan biztosítása, a megfelelő és teljesítményszintet szolgáltatásszintet használok?
A figyelő a [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) és [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) dinamikus felügyeleti nézetekkel Processzor, i/o- és memória-felhasználás megértése. Is használhatja az SQL-adatbázis [lekérdezési Terheléselemző](sql-database-query-performance.md) hálózatierőforrás-fogyasztás megjelenítéséhez. Ha folyamatosan fut, a magas százalékos arányú rendelkezésre álló erőforrások, fontolja meg egy magasabb teljesítményszintre belül a meglévő szolgáltatási rétegben, vagy helyezze át egy magasabb szintű szolgáltatás használható. Ezzel ellentétben használatakor folyamatosan rendelkezésre álló erőforrások alacsony százalékos, akkor előfordulhat, hogy alacsonyabb teljesítményszintre fontolja meg vagy szolgáltatásréteget.

### <a name="i-am-seeing-performance-issues-how-does-my-azure-sql-database-troubleshooting-methodology-differ-from-sql-server"></a>Teljesítményproblémák hivatkozva. Eltérések a SQL Server az Azure SQL Database hibaelhárítás módszertana?
A teljesítmény hibaelhárítás módszertana sok területén az Azure SQL Database változatlan marad, de néhány különbség lesz. Például ha egy akár teljesítménycsökkenés általános, figyelheti a [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) és [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) dinamikus felügyeleti nézetekkel ahhoz, hogy a Processzor, i/o- és memória ismertetése használat. Szükség lehet módosítani a teljesítményszintet és/vagy terheléshez szolgáltatásréteget.
Teljesítményproblémák hangolással kapcsolatos ajánlások átfogó gyűjteményét, lásd: [az Azure SQL-adatbázis teljesítményének hangolása](sql-database-performance-guidance.md). 

### <a name="do-i-need-to-maintain-indexes-and-statistics"></a>Kell indexeket és statisztikákat karbantartása?
Az Azure SQL-adatbázis nem tart fenn indexeket és statisztikákat automatikusan a szolgáltatás részeként. Ön felelősséggel tartozik az indexeket és statisztikákat karbantartásának ütemezése. A következő cikkben, az Azure Automation módszerek, részletezi az Azure SQL adatbázis karbantartási feladatok ütemezése számos lehetőség közül választhat.

## <a name="data-movement-after-migration"></a>Adatátvitel áttelepítés után

### <a name="how-do-i-export-and-import-data-as-bacpac-files-from-azure-sql-database"></a>Hogyan exportálása és importálhat adatokat az Azure SQL-adatbázis BACPAC-fájlok formájában? 

- **Exportálás**: az Azure SQL Database az Azure portálról BACPAC-fájlként exportálhatja.

  ![Egy BACPAC exportálása](./media/sql-database-export/database-export.png)

- **Importálás**: az Azure portál használatával adatbázis BACPAC-fájlként importálhatja.

  ![Egy BACPAC importálása](./media/sql-database-import/import.png)

### <a name="how-do-i-synchronize-data-between-azure-sql-database-sql-server-2016--2012"></a>Hogyan szinkronizálni adatokat között, az Azure SQL adatbázis SQL Server 2016 / 2012?
A [adatszinkronizálás](sql-database-sync-data.md) adatszinkronizálás kétirányúan több helyszíni SQL Server-adatbázisok és az Azure SQL Database szolgáltatás segítségével. Mivel ez alapú eseményindító, végleges konzisztencia garantáltan (adatvesztés nélküli), azonban tranzakció konzisztencia nem garantált. 

## <a name="next-steps"></a>Következő lépések
További tudnivalók [Azure SQL adatbázis](sql-database-technical-overview.md).
