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
ms.date: 12/06/2016
ms.author: Joe.Sack
ms.suite: sql
ms.prod_service: sql-database
ms.component: migration
ms.openlocfilehash: ee1d847e04e1f1fa0472d8702c7022d622b9fe0f
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="new-dba-in-the-cloud--managing-your-database-in-azure-sql-database"></a>A felhőben – az Azure SQL Database adatbázis új DBA

Átállás a hagyományos önállóan felügyelt, önálló ellenőrzött környezetben PaaS-környezethez is úgy tűnik, egy kicsit túlságosan először. Egy alkalmazást fejlesztői vagy a Kiszolgálóhoz célszerű mindig tudja, a platform, melyek segítenek megtartani az alkalmazás számára elérhető, performant, biztonságos és refs - legfontosabb funkcióit. Ez a cikk célja, hogy pontosan ez a szerepük. A cikk röviden rendezi az erőforrásokat, és lehetővé teszi az egyes útmutatást legjobb használatáról az SQL-adatbázis főbb képességei kezelése és az alkalmazás megfelelően fut és a felhőben optimális eredmények elérése. Ez a cikk tipikus célközönség lenne azokat, akik:-
- A társított alkalmazásokban az Azure SQL Adatbázishoz – a alkalmazás(ok) korszerűsítésénél áttelepítését értékelése.
- Van a migrálása folyamatban van a alkalmazás(ok) – folyamatos áttelepítési forgatókönyv szerint.
- Az Azure SQL Database – új DBA a felhőben való áttelepítés nemrégiben fejeződtek be.

A cikk ismerteti az egyes Azure SQL Database alapvető jellemzői, amelyek könnyen kihasználhatják platformként. -E a következő:- 
- Üzleti folytonossági és vészhelyreállítási helyreállítási (BCDR)
- Biztonság és megfelelőség
- Intelligens adatbázis figyelését és karbantartását
- Adatáthelyezés


## <a name="business-continuity-and-disaster-recovery-bcdr"></a>Üzleti folytonossági és vészhelyreállítási helyreállítási (BCDR)
Üzleti folytonossági és vészhelyreállítási helyreállítási képességek lehetővé teszik az üzleti szokásos módon folytatja a katasztrófa esetén. A vészhelyreállítás lehet egy adatbázis szintű esemény (például valaki véletlenül elutasítja azokat a kritikus fontosságú tábla) vagy egy adat-központ szintű esemény (regionális katasztrófa, például egy tsunami). 

### <a name="how-do-i-create-and-manage-backups-on-sql-database"></a>Hogyan létrehozása és kezelése az SQL-adatbázis a biztonsági mentések?
Az Azure SQL-adatbázis biztonsági másolatok ne hozzon létre és, mert nem kell. SQL-adatbázis automatikusan-adatbázisok biztonsági mentését, így Ön már nem kell foglalkoznia az ütemezés, véve, és a biztonsági mentések kezelése. A platform teljes biztonsági mentés hetente, különbözeti biztonsági mentési óránként és a napló biztonsági mentését 5 percenként annak biztosítása érdekében, a vész-helyreállítási hatékony, és az adatvesztés minimális vesz igénybe. Az első teljes biztonsági mentés, amint az adatbázis létrehozása történik. A biztonsági mentése "Megőrzési időtartam" nevű egy bizonyos ideig rendelkezésére álljanak, és úgy dönt, teljesítményszint függően változik.  SQL-adatbázis visszaállítása bármely belül a megőrzési időszak használata lehetőséget nyújt, [idő-helyreállítás (PITR) pont](sql-database-recovery-using-backups.md#point-in-time-restore).

|Teljesítményszint|Megőrzési időszak napban|
|---|:---:|
|Alapszintű|7|
|Standard|35|
|Prémium|35|
|||

Emellett a [hosszú távú megőrzési (LTR)](sql-database-long-term-retention.md) szolgáltatás lehetővé teszi, hogy tárolni tudja a biztonsági mentési fájljait, sokkal hosszabb ideig kifejezetten, legfeljebb 10 év, és adatok visszaállíthatók a biztonsági mentése bármely adott időszakon belül. Továbbá az adatbázis biztonsági másolatait tárolják a regionális katasztrófa rugalmasság biztosításához georeplikált tárolási. A biztonsági mentése bármely Azure régióban, a megőrzési időn belül minden helyen is állíthatja. Lásd: [üzleti folytonosság – áttekintés](sql-database-business-continuity.md).

### <a name="how-do-i-ensure-business-continuity-in-the-event-of-a-datacenter-level-disaster-or-regional-catastrophe"></a>Hogyan hajtsa végre a datacenter-szintű katasztrófa vagy regionális katasztrófa esetén az üzletmenet folytonosságának biztosításához?
Az adatbázis biztonsági másolatait találhatók a georeplikált tárolóalrendszer annak érdekében, hogy egy regionális katasztrófa esetén, mert egy másik Azure-régió, visszaállíthatja a biztonsági mentés. Ez a lehetőség georedundáns helyreállítás. Ez a helyreállítási Időkorlát (Helyreállításipont-célkitűzés) általában < 1 óra és a Beszúrása (becsült helyreállítási idő – néhány percet óra).

A kritikus fontosságú adatbázisokhoz Azure SQL Database kínál, aktív georeplikáció. Ez lényegében funkciója, hogy létrehoz egy georeplikált másodlagos az eredeti adatbázis másolatát egy másik régióban. Ha például az adatbázis Azure nyugati US régió először üzemel, és azt szeretné, hogy a regionális katasztrófa rugalmasság. Az adatbázis aktív földrajzi replikájának az USA nyugati régiója, USA keleti régiója azaz hozna létre. A calamity éri az USA nyugati régiója, USA keleti régiójában feladatátvétel megfelelően történhet. Az automatikus feladatátvételt csoportban a konfigurálás nem még élvezetesebbé, mert ez biztosítja, hogy az adatbázis automatikusan átadja a feladatokat a másodlagos egy katasztrófa esetén az USA keleti régiója. Ez a helyreállítási Időkorlát < 5 másodperc és a Beszúrása < 30 másodperc.

Ha az automatikus feladatátvételt csoportja van konfigurálva, majd az alkalmazást kell aktívan figyeli, hogy egy olyan vészhelyzet esetén, és kezdeményezze a feladatátvételt a másodlagos. A különböző Azure-régiók legfeljebb 4 ilyen active-georeplikációkhoz hozhat létre. Még élvezetesebbé kap. A másodlagos active-georeplikációkhoz a csak olvasási hozzáféréssel is elérhető. Ez nem származik földrajzilag elosztott alkalmazás forgatókönyvek késés csökkentése érdekében hasznos. 

### <a name="how-does-my-disaster-recovery-plan-change-from-on-premises-to-sql-database"></a>A vész-helyreállítási terv hogyan nem módosíthatja a helyszíni SQL Database?
Összefoglalva a hagyományos helyszíni SQL Server telepítéséhez szükséges aktívan kezelheti a rendelkezésre állási elérhető szolgáltatások, például a Feladatátvételi fürtszolgáltatás, az adatbázis-tükrözés, Tranzakcióreplikációt, napló mobilalkalmazások stb és karbantartása és biztonsági mentéseit, hogy Az üzletmenet folytonossága. Az SQL Database a platform kezeli ezeket, így fejlesztéséhez és az adatbázis-alkalmazás optimalizálása összpontosít, és nem kell foglalkoznia katasztrófa felügyeleti a lehető legtöbb. Biztonsági mentési és vész-helyreállítási tervek konfigurálva és végzett néhány kattintással az Azure-portál (vagy néhány parancsokat a PowerShell API-k) lehet. 

Vész-helyreállítási kapcsolatos további információkért lásd: [Azure SQL Db katasztrófa utáni helyreállítás 101](https://azure.microsoft.com/blog/azure-sql-databases-disaster-recovery-101/)

## <a name="security-and-compliance"></a>Biztonság és megfelelőség
SQL-adatbázis nagyon súlyos készít biztonsági és adatvédelmi. SQL-adatbázis biztonsági érhető el az adatbázis szintjén, és a platform szintjén, és legjobb értendő, amikor több rétegből kategóriákba. Minden egyes rétegben kap optimális biztonsága érdekében az alkalmazást, és szabályozhatja. A szintek a következők:
- Identitás- & hitelesítés ([Windows/SQL-hitelesítés és az Azure Active Directory [AAD] hitelesítés](sql-database-control-access.md)).
- Figyelése ([naplózási](sql-database-auditing.md) és [veszélyforrások detektálása](sql-database-threat-detection.md)).
- Tényleges adatok védelmének ([átlátható adattitkosítási [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) és [mindig titkosítja az [AE]](/sql/relational-databases/security/encryption/always-encrypted-database-engine)). 
- Adatok bizalmas és rendszerjogosultságú hozzáférés szabályozása ([sor biztonság](/sql/relational-databases/security/row-level-security) és [dinamikus Adatmaszkolási](/sql/relational-databases/security/dynamic-data-masking)).

[Az Azure Security Center](https://azure.microsoft.com/services/security-center/) központi felügyeleti kínál az Azure, a helyszíni és a többi felhőből futó számítási feladatok között. Megtekintheti, hogy az alapvető SQL-adatbázis védelme például [naplózási](sql-database-auditing.md) és [átlátható adattitkosítási [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) összes erőforrásra van konfigurálva, és a saját követelmények alapján házirendeket hozhat létre.

### <a name="what-user-authentication-methods-are-offered-in-sql-database"></a>SQL adatbázis felhasználói hitelesítési módszerek érhető el?
Nincsenek [két hitelesítési módszerek](sql-database-control-access.md#authentication) érhető el az SQL-adatbázis: 
- [Azure Active Directory Authentication](sql-database-aad-authentication.md)
- SQL-hitelesítést. 

A hagyományos windows-hitelesítés nem támogatott. Az Azure Active Directory (AD) egy olyan központi identitások és hozzáférések felügyeleti szolgáltatás. Ez nagyon kényelmesen tartalmaz egy egyszeri bejelentkezés Access (SSO) az összes személyek a szervezetében. Mi ez azt jelenti, hogy a hitelesítő adatok közösen használhatóak az összes Azure-szolgáltatások egyszerűbb hitelesítéshez. Támogatja az aad-ben [többtényezős hitelesítés (többtényezős hitelesítést)](sql-database-ssms-mfa-authentication.md) és egy, a [mindössze néhány kattintással](../active-directory/connect/active-directory-aadconnect-get-started-express.md) AAD integrálható a Windows Server Active Directory. SQL-hitelesítés már használta azt a múltban ugyanúgy működik. Megadja a felhasználónév/jelszó, és akkor is hitelesítheti a felhasználókat a bármely adatbázis a megadott logikai kiszolgálón. Ez is lehetővé teszi, hogy SQL-adatbázis és az SQL Data Warehouse a többtényezős hitelesítést és a Vendég felhasználói fiókok Azure AD-tartományban. Ha már rendelkezik egy Active Directory helyszíni, akkor is vonható össze, az Azure Active Directory segítségével terjed ki a címtár az Azure directory.

|**Ha Ön...**|**SQL-adatbázis SQL Data Warehouse /**|
|---|---|
|Nem kíván használni az Azure Active Directory (AD) az Azure-ban|Használjon [SQL-hitelesítés](sql-database-security-overview.md)|
|Helyszíni SQL-kiszolgálón használt AD|[AD az Azure ad-val összevonni](../active-directory/connect/active-directory-aadconnect.md), és az Azure AD-alapú hitelesítés használata. Ennek használhatja az egyszeri bejelentkezés.|
|Érvényesíteniük kell a többtényezős hitelesítés (MFA)|Házirend-keresztül megkövetelő [Microsoft feltételes hozzáférés](sql-database-conditional-access.md), és használja [Azure AD univerzális authentication, MFA-támogatással rendelkező](sql-database-ssms-mfa-authentication.md).|
|Vendég fiókot a Microsoft-fiókok (live.com, outlook.com) vagy más tartományok (gmail.com)|Használjon [Azure AD univerzális hitelesítési](sql-database-ssms-mfa-authentication.md) SQL adatbázis vagy adatraktár, amely kihasználja a [Azure AD B2B együttműködés](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).|
|A rendszer naplózza Windows Azure AD hitelesítő adatait használja az összevont tartományból|Használjon [az Azure AD integrált hitelesítés](sql-database-aad-authentication-configure.md).|
|A rendszer naplózza Windows hitelesítő adatokkal nem összevont Azure-ral tartomány|Használjon [az Azure AD integrált hitelesítés](sql-database-aad-authentication-configure.md).|
|A középső rétegbeli szolgáltatásokat, amelyhez csatlakoznia kell az SQL Database vagy az SQL Data Warehouse|Használjon [az Azure AD integrált hitelesítés](sql-database-aad-authentication-configure.md).|
|||

### <a name="how-do-i-limit-or-control-connectivity-access-to-my-database"></a>Hogyan korlátozása vagy szabályozhatja az adatbázis kapcsolódási hozzáférés?
Nincsenek több technikák a rendelkezésére, amelyek az alkalmazás optimális kapcsolat szervezet eléréséhez használhat. 
- Tűzfalszabályok
- Virtuális hálózat Szolgáltatásvégpontok
- Fenntartott IP-címek

#### <a name="firewall"></a>Tűzfal
Tűzfal megakadályozza, hogy hozzáférést a kiszolgálóhoz egy külső entitásban azáltal, hogy csak adott entitások hozzáférést a logikai kiszolgálóhoz. Alapértelmezés szerint minden kapcsolatok és a logikai kiszolgálón belül adatbázisok nem engedélyezettek, kivéve más Azure-szolgáltatásokhoz érkező kapcsolatokat. Egy tűzfalszabály úgy is megnyithatja hozzáférés csak az entitásokat (például a fejlesztői gépen), hogy jóváhagyja, azzal, hogy a számítógép IP-cím a tűzfalon keresztül lehetővé-kiszolgálóhoz. Lehetővé teszi IP-címtartományra, amely engedélyezi a hozzáférést a logikai kiszolgálóhoz történő kívánt megadását. Például fejlesztői IP-címeinek a szervezet egyszer vehető fel, a tűzfal beállításai lapon a címtartomány megadásával. 

Tűzfal-szabályokat hozhat létre, a kiszolgáló szintjén vagy az adatbázis szintjén. Szolgáltatásiszint-kiszolgáló tűzfalszabályainak vagy létrehozása a portálon keresztül vagy a szolgáltatáshoz az SSMS használatával is. További információk a kiszolgáló- és adatbázis szintű tűzfalszabály beállításával, lásd: [létre tűzfalszabályokat az SQL-adatbázis](sql-database-security-tutorial.md#create-a-server-level-firewall-rule-in-the-azure-portal).

#### <a name="service-endpoints"></a>Szolgáltatás-végpontok
Alapértelmezés szerint az SQL-adatbázis mérete úgy konfigurálva, hogy "Az összes Azure-szolgáltatások engedélyezése" – ami azt jelenti, hogy a virtuális gépek adatait az Azure-ban megkísérelhet csatlakozni az adatbázishoz. Ezeket a kísérleteket kell hitelesítik. Azonban ha nem szeretné, az adatbázis bármely Azure IP-címek által elérhetőnek kell lennie, letilthatja "Az összes Azure-szolgáltatások engedélyezése". Emellett konfigurálhatja [VNET Szolgáltatásvégpontok](sql-database-vnet-service-endpoint-rule-overview.md).

Szolgáltatásvégpontok (SE) teszik lehetővé teszi közzé a kritikus fontosságú Azure-erőforrások csak a saját titkos virtuális hálózat az Azure-ban. Ezzel a módszerrel lényegében nincs nyilvános hozzáférés az erőforrásokhoz. A virtuális hálózat az Azure közötti forgalom Azure hálózat marad. Használata nélkül kap a kényszerített bújtatás csomag útválasztást. A virtuális hálózat kényszeríti a szervezet internetes forgalmat és az Azure szolgáltatás forgalom az azonos útvonal áthaladhat. Szolgáltatás végpontokon optimalizálhatja ez óta a csomagok adatfolyam rögtön a virtuális hálózatról Azure gerincét hálózati szolgáltatáshoz.

![Virtuális hálózat Szolgáltatásvégpontok](./media/sql-database-manage-after-migration/vnet-service-endpoints.png) 

#### <a name="reserved-ips"></a>Fenntartott IP-címek
Másik lehetőség is rendelkezésre [fenntartott IP-címek](../virtual-network/virtual-networks-reserved-public-ip.md) a virtuális gépek és az engedélyezési lista azokat adott virtuális gép IP-címek, a kiszolgáló a tűzfalbeállítások. Fenntartott IP-címek hozzárendelése, mentse a nem a tűzfalszabályok az IP-címek módosításához frissíteni kell.

### <a name="what-port-do-i-connect-to-sql-database-on"></a>Milyen port kapcsolódni az SQL-adatbázis a?

1433-as port. SQL-adatbázis ezen a porton keresztül kommunikál. Csatlakozhat a vállalati hálózaton belül, hogy egy kimenő forgalomra vonatkozó szabály hozzáadása a tűzfalbeállítások, a szervezet a. Az eszközöket hogy csökkenjen az 1433-as port az Azure határán kívüli ne. Az Azure használatával is futtathatja a szolgáltatáshoz az SSMS [Azure RemoteApp](https://www.microsoft.com/cloud-platform/azure-remoteapp-client-apps). Ehhez nem szükséges, hogy nyissa meg a 1433-as port kimenő kapcsolatokat, az IP-cím statikus, ezért lehet, hogy az adatbázis csak a RemoteApp meg van nyitva, és támogatja a többszörös többtényezős hitelesítés (MFA).

### <a name="how-can-i-monitor-and-regulate-activity-on-my-server-and-database-in-sql-database"></a>Hogyan figyelheti és szabályozza a kiszolgálón és az SQL-adatbázis adatbázis tevékenység?
#### <a name="sql-database-auditing"></a>SQL Database Auditing
Az SQL Database kapcsolhatja ON nyomon követéséhez az adatbázis-események naplózása. [SQL Database Auditing](sql-database-auditing.md) rögzíti az adatbázissal kapcsolatos eseményeket, és egy naplófájlból, az Azure Storage-fiókot az adatbázisba írja őket. Naplózás különösen akkor hasznos, ha azt tervezi, biztonsági és házirend megfelelésnek betekintést, stb. törvényi megfelelőség fenntartásában. Ez lehetővé teszi, hogy határozza meg, és konfigurálja az egyes kategóriák eseményeket, amelyek úgy gondolja, hogy vizsgálati van szüksége, és hogy kaphat előre konfigurált jelentések és egy irányítópult áttekintése a folyamatban lévő az adatbázis események alapján. A naplózási házirendek, az adatbázis szintjén vagy a kiszolgáló szintjén is alkalmazhatja. A kiszolgáló, illetve az adatbázis, a naplózás bekapcsolása útmutató, lásd: [engedélyezése az SQL Database Auditing](sql-database-security-tutorial.md#enable-sql-database-auditing-if-necessary).

#### <a name="threat-detection"></a>Fenyegetésészlelés
A [veszélyforrások detektálása](sql-database-threat-detection.md), lehetővé teszi, hogy nagyon egyszerűen naplózás által észlelt biztonsági vagy házirend megsértésének alapján kap. Nem kell lenniük a rendszer potenciális fenyegetések vagy megsértésével megoldására szakértői biztonsági. A fenyegetésészlelés is rendelkezik néhány beépített szolgáltatásokat, például az SQL-injektálás észlelése. SQL-injektálás az alter, vagy az adatokat, majd egy elég általános módszer általában támadása egy adatbázis-alkalmazás hibát okoz. SQL-adatbázis Fenyegetésészlelés algoritmusokban, ami észleli a potenciális biztonsági réseket és az SQL injektálási támadások, valamint az adatbázis rendellenes hozzáférési minták (például egy szokatlan helyről, vagy egy ismeretlen tag hozzáférés) több példányban fut. Biztonsági tisztviselő vagy más kijelölt rendszergazdák e-mail értesítést kapni, amennyiben a fenyegetést észlel az adatbázis. Minden értesítés további vizsgálata, és a fenyegetések mérséklésére kapcsolatos a gyanús tevékenységeket és ajánlások részleteit tartalmazza. A fenyegetésészlelés bekapcsolása további tudnivalókért lásd:: [SQL adatbázishoz a Fenyegetésészlelés engedélyezéséhez](sql-database-security-tutorial.md#enable-sql-database-threat-detection). 
### <a name="how-do-i-protect-my-data-in-general-on-sql-database"></a>Hogyan tegye védeni az adatokat általában az SQL Database?
Titkosítási védelemmel való ellátásához, és megvédi a bizalmas adatokat a jogosulatlan behatolók erős mechanizmust biztosít. A titkosított adatok van, a behatolók a visszafejtési kulcs nélkül a nem használt. Ebből kifolyólag hozzáadja egy további védelmi réteg biztosítása a meglévő biztonsági réteg alkalmazása SQL Database beépített felett. Két szempontot kell az SQL-adatbázis az adatok védelme: 
- Az adatokat nyugalmi adatainak és naplókönyvtárainak fájlok 
- Az adatok, amely az üzenetsoroktól. 

Az SQL-adatbázis, alapértelmezés szerint az adatokat az alrendszer adatainak és naplókönyvtárainak fájlok aktívan teljesen és mindig titkosított keresztül [átlátható adattitkosítási [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). A biztonsági másolatok is titkosítva vannak. A TDE esetén nem fér hozzá ezeket az adatokat az alkalmazás oldalon igényelt módosításokat. A titkosítási és visszafejtési fordulhat elő, transzparens módon; ezért a neve. Az üzenetsoroktól a bizalmas adatok védelmének és aktívan SQL-adatbázis nevű funkcióval rendelkezik [mindig titkosítja (AE)](/sql/relational-databases/security/encryption/always-encrypted-database-engine). AE olyan ügyféloldali titkosítás, mely az adatbázis-és nagybetűket oszlopok titkosítja (úgy, hogy azok az adatbázis-rendszergazdák és a jogosulatlan felhasználók számára a titkosított szöveg) típusú. A kiszolgáló először kap a titkosított adatokat. Mindig titkosítja a kulcsot is tárolja az ügyféloldalon, így csak az arra jogosult ügyfelek vissza tudja fejteni a bizalmas oszlopok. A kiszolgáló és az adatok a rendszergazdák nem tekintse meg a bizalmas adatokat, mivel a titkosítási kulcsok tárolási helye az ügyfél. AE titkosítja a bizalmas oszlopok a tábla teljes körű, illetéktelen ügyfelekről a fizikai lemezhez. AE napjainkban támogatja egyenlőségi összehasonlítást, így a DBAs továbbra is a titkosított oszlopokban lekérdezni az SQL-parancsok részeként. Mindig titkosított használható számos tárolt kulcs lehetőséggel, például a [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md), Windows tanúsítványtárolójába, és helyi hardveres biztonsági modulok.

|**Jellemzői**|**Always Encrypted**|**Átlátható adattitkosítás**|
|---|---|---|
|**Titkosítási span**|Végpontok közötti|Nyugalmi adatok|
|**Adatbázis-kiszolgáló hozzáférhet bizalmas adatokhoz**|Nem|Igen, mert a titkosítás az adatok aktívan|
|**Engedélyezett T-SQL-műveletek**|Egyenlőségi összehasonlítás|Minden T-SQL-felületének érhető el|
|**A szolgáltatás használatához szükséges alkalmazások változásairól**|Minimális|Nagyon minimális|
|**Titkosítási granularitási**|Oszlop szint|Adatbázis szintje|
||||

### <a name="how-can-i-limit-access-to-sensitive-data-in-my-database"></a>Hogyan lehet korlátozni az adatbázisba bizalmas adatokhoz való hozzáférést?
Minden alkalmazás rendelkezik egy bizonyos bit a bizalmas adatok az adatbázis, amely nem látható a mindenki számára kell védeni. A szervezeten belüli egyes csoporthoz kell, hogy az adatok megtekintésére, azonban más nem tudják megjeleníteni ezeket az adatokat. Egy példa egy alkalmazott bére. A kezelő azonban lenne szükség a fizetési adatainak képes közvetlen jelentések eléréséhez, egyéni lehetnének az a partnerek a fizetési adatainak eléréséhez. Egy másik helyzet lehet adatok fejlesztőknek bizalmas adatokhoz való interakció fejlesztési szakaszában, vagy tesztelni, például az ügyfelek taj számok esetében. Ezt az információt újra ki vannak téve a fejlesztői nem szükséges. Ilyen esetben a bizalmas adatok vagy kell legyen maszkolva, vagy nem lesz elérhető. SQL-adatbázis ilyen így megakadályozhatja, hogy jogosulatlan felhasználók hozzáférhetnek a bizalmas adatok két módszer nyújtja:

[A dinamikus Adatmaszkolás](sql-database-dynamic-data-masking-get-started.md) , amely lehetővé teszi a bizalmas adatok veszélyeztetettségének korlátozása által maszkolás a jogosulatlan felhasználók az alkalmazási rétegre adatok maszkolása szolgáltatása. Megadhatja egy maszkolási szabályra, amely egy maszkolási mintát hozhat létre (például a csak megjelenítendő utolsó négy számjegye egy nemzeti azonosító SSN: XXX-XX-0000 és kell megjelölnie azt Xs a legtöbb), és azonosíthatja, hogy mely felhasználók vannak ki lesznek zárva a maszkolásból szabály a. A maszkolás történik, az azonnali, és nincsenek elérhető különböző az adatkategóriákat a különböző maszkolási függvénnyel. Dinamikus adatok maszkolása lehetővé teszi automatikus észlelése a bizalmas adatokat az adatbázisban, és maszkolási vonatkozik.

[Biztonság sor](/sql/relational-databases/security/row-level-security) lehetővé teszi, hogy a sor szintjén. Tehát, bizonyos a felhasználó a lekérdezés (csoport tagsági vagy a végrehajtási környezet) alapján adatbázis tábla sorainak rejtve maradnak. A hozzáférés korlátozása az adatbázis-rétegből ahelyett, hogy egy alkalmazás rétegben, egyszerűbbé teheti a app logika történik. Indítsa el a szűrőpredikátum, azon sorait, amelyek nem elérhetők és a biztonsági házirend tovább definiálása kiszűrése a sorokra hozzáféréssel rendelkező létrehozásával. Végezetül a végfelhasználó a lekérdezés futtatása és, attól függően, hogy a felhasználó jogosultságát, vagy azokat korlátozott sorok megtekintéséhez, vagy megtekintheti őket egyáltalán nem.

### <a name="how-do-i-manage-encryption-keys-in-the-cloud"></a>Hogyan kezelhetők a titkosítási kulcsokat a felhőben?

(Ügyféloldali titkosítás) mindig titkosítja és átlátható adattitkosítási (aktívan titkosítása) kulcskezelés lehetőség áll rendelkezésre. Ajánlott rendszeresen titkosítási kulcsok megváltoztatása. Az elforgatás gyakorisága a szervezet belső szabályozások és a megfelelőségi követelmények kell igazítása.

**Transzparens Data Encryption (TDE)**: a két kulcs hierarchiában van TDE – szimmetrikus AES-256 adatbázis egyedi adatbázis-titkosítási kulcs (adattitkosítási kulcs), amely pedig a rendszer titkosítja a kiszolgáló egyedi aszimmetrikus RSA titkosítja az adatokat az egyes felhasználói adatbázisban 2048 főkulcs. A főkulcs lehet felügyelt vagy:
- Automatikusan által a platform - SQL-adatbázis.
- Által használatával, illetve [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md) mint a kulcstároló.

Alapértelmezés szerint a főkulcs átlátható adattitkosítási kezeli az SQL Database szolgáltatás kényelmét szolgálja. Ha a szervezet céljai között a főkulcs szabályozhatják, akkor egy lehetőség, hogy az Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md) használják a kulcstároló. Az Azure Key Vault segítségével a szervezet azt feltételezi, hogy ellenőrzése alatt tartja a kulcs kiépítés, elforgatás és engedéllyel szabályozza. [Elforgatás vagy váltás TDE főkulcs típusú](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql-key-rotation) gyors,, akkor csak újból titkosítja az adattitkosítási kulcsot. A biztonsági és adatkezelés közötti szerepek elkülönítése rendelkező szervezetek számára egy biztonsági rendszergazdai a kulcsokat tárol az Azure Key Vault TDE főkulcs kiépítése nem sikerült, és adja meg a használandó adatbázis-rendszergazda az Azure Key Vault kulcsazonosító egy kiszolgálón titkosítását. A Key Vault kialakításának úgy, hogy a Microsoft nem tekintse meg és bontsa ki a titkosítási kulcsokat. A szervezet kulcsok egy központi kezelésére is kap. 

**Mindig titkosítja**: is egy [két kulcs hierarchia](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted) mindig titkosítja - a bizalmas adatok oszlop egy AES 256 oszlop titkosítási kulcsának (CEK), amely viszont titkosított oszlop főkulccsal (CMK) titkosítja. Az ügyfél-illesztőprogramok előírt mindig titkosítja a CMKs hossza nem korlátozásokkal rendelkezik. A CEK titkosított értékét az adatbázisban tárolja, és a CMK tárolása egy megbízható kulcs, például Windows tanúsítványtárolójába, az Azure Key Vault vagy egy hardveres biztonsági modul. 
- Mindkét a [CEK és CMK](/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell) forgatható el. 
- CEK Elforgatás Adatműveletek méretet és is lehet a táblák méretétől függően időigényes tartalmazó a titkosított oszlopokat. Ezért ajánlatos ennek megfelelően tervezheti CEK Forgatás műveletek. 
- CMK elforgatás, azonban nem ütközik az adatbázis teljesítményét, és az elkülönített szerepkörök is végezhető.
Az alábbi ábrán látható a kulcstároló-beállításait az oszlop főkulcsok mindig titkosítja a

![Mindig titkosítja a CMK tárolására szolgáltatók](./media/sql-database-manage-after-migration/always-encrypted.png)

### <a name="how-can-i-optimize-and-secure-the-traffic-between-my-organization-and-sql-database"></a>Hogyan optimalizálása és védik a forgalmat a szervezet és az SQL-adatbázis között?
A szervezet és az SQL-adatbázis közötti hálózati forgalom általában a nyilvános hálózaton keresztül kellene beolvasása irányítja. Azonban ha optimalizálása ezt az elérési utat, és lehetővé teszi a nagyobb biztonságot nyújt, megtekintheti az Express Route. Expressroute lényegében lehetővé teszi a vállalati hálózat kiterjeszti az Azure platformon titkos kapcsolaton keresztül. Ezzel a módszerrel nem lépjen a nyilvános interneten keresztül. Is kap magasabb szintű biztonságra, megbízhatóságra és útválasztási optimalizálás, amely alacsonyabb hálózati késések fordulnak elő, és sokkal gyorsabb sebességű, mint amennyit általában akkor tapasztalnak, a nyilvános interneten keresztül is. Ha azt tervezi, a jelentős adattömb az adatok átvitele a szervezet és az Azure között, használja az Express Route partíciónként akár költségelőnyökhöz juthat. Választhat a három különböző modellek a kapcsolat a szervezet az Azure-bA: 
- [Felhőalapú Exchange közös elhelyezése](../expressroute/expressroute-connectivity-models.md#CloudExchange)
- [Any-to-any](../expressroute/expressroute-connectivity-models.md#IPVPN)
- [Point-to-Point](../expressroute/expressroute-connectivity-models.md#Ethernet)

Expressroute is lehetővé teszi kapacitásnövelés legfeljebb 2 x a sávszélesség-korlátozás megvásárlása esetén nem kell külön fizetni. Akkor is közötti régió kapcsolat expressroute segítségével konfigurálhatja. ER kapcsolat szolgáltatók listájának megtekintéséhez lásd: [Express Route-partnerek és társviszony-létesítés helyek](../expressroute/expressroute-locations.md). Az alábbi cikkek Express Route részletesebben ismertetik:
- [Az Expressroute bemutatása](../expressroute/expressroute-introduction.md)
- [Előfeltételek](../expressroute/expressroute-prerequisites.md)
- [Munkafolyamatok](../expressroute/expressroute-workflows.md)

### <a name="is-sql-database-compliant-with-any-regulatory-requirements-and-how-does-that-help-with-my-own-organizations-compliance"></a>Az SQL-adatbázis bármely szabályozási követelményeknek megfelelő, és hogyan, amely segít a saját szervezet?
SQL-adatbázis szabályozó megfelelőségi számos megfelel. Tekintse meg a legújabb, hogy teljesülnek-megfelelőségi, látogasson el a [Microsoft Trust Center](https://www.microsoft.com/trustcenter/compliance/complianceofferings) és a megjelenítéséhez, ha SQL-adatbázis szerepel-e a megfelelő Azure-szolgáltatás a szervezet számára fontos a megfelelőségi részletek. Fontos megjegyezni, hogy bár SQL-adatbázis megfelelő szolgáltatásként igazolhatják, azt a szervezet szolgáltatása megfelelőségi támogatások, de automatikusan garantálja az.

## <a name="intelligent-database-monitoring-and-maintenance-after-migration"></a>Intelligens adatbázis figyelését és karbantartását az áttelepítés után

Miután az adatbázis áttelepítése az SQL Database, figyelheti az adatbázis (a példa, ellenőrizze az erőforrás-használat Mitől például vagy DBCC ellenőrzi) fog, és rendszeres karbantartás (például építse újra, vagy átszervez indexeket, statisztika stb.). Szerencsére SQL adatbázisa intelligens abban az értelemben, hogy az a múltbeli trendek és a rögzített metrikák és a statisztika segítségével proaktívan egyikével figyelheti és az adatbázis karbantartása, hogy az alkalmazás optimális mindig fusson. Bizonyos esetekben az Azure SQL Database is automatikusan karbantartási feladatait attól függően, hogy a konfigurációs beállításai. Az SQL-adatbázis az adatbázis-figyelés három facets van:
- Teljesítmény- és optimalizálás.
- Biztonsági optimalizálás.
- Optimalizálás költség.

**Teljesítmény- és optimalizálási**: A lekérdezési teljesítmény Insights kaphat a következőkhöz ideális javaslatok az adatbázisban munkaterhelés számára, hogy az alkalmazások is tovább futnak, optimális szinten – mindig. Is állíthatja, hogy ezek a javaslatok automatikus érvényben, és nem kell teljesítő karbantartási feladatok többet jelzi. Az Index Advisor automatikusan valósíthat meg a számítási feladatok alapján ajánlott index, mert ezt nevezzük automatikus hangolása. A javaslatok fejlődnek, az alkalmazás-munkaterhelési változások biztosítja a leginkább megfelelő javaslatok. A beállítással manuálisan ezek a javaslatok, majd alkalmazza azok közül is kap.  

**Biztonsági optimalizálási**: SQL-adatbázis segítségével az adatok és a fenyegetések észlelése azonosítására, és vizsgálja meg, amely egy potenciális szál adatbázis gyanús tevékenységek biztosíthatja végrehajthatóként biztonsági ajánlásaiban a az adatbázis. [SQL biztonsági réseinek értékelése](sql-vulnerability-assessment.md) átvizsgálja és szolgáltatás, amely lehetővé teszi léptékű az adatbázisok biztonsági állapotának figyelése és a biztonsági kockázatok azonosítása és az Ön által megadott biztonsági alaptervet eltolódás adatbázis. Minden vizsgálat után végrehajtandó lépéseket és javítási parancsfájlok testreszabott listájának biztosított, valamint segít abban, hogy megfeleljenek a megfelelőségi használható értékelési jelentést.

Az Azure Security Center a biztonsági javaslatok azonosíthatja a tábla között, és alkalmazni azokat egyetlen kattintással. 

**Optimalizálás költség**: Azure SQL-platform elemzi a kihasználtsági előzményeinek kiértékeléséhez és költség-optimalizálási beállítások, javasoljuk a kiszolgálón az adatbázisok között. Az elemzés általában elemzéséhez és végrehajthatóként javaslatok kialakításához kéthetente vesz igénybe. A rugalmas készlet egy ilyen beállítást. A javaslat jelenik meg a portál egy szalagcím:

![rugalmas készletekkel kapcsolatos javaslatok](./media/sql-database-manage-after-migration/elastic-pool-recommendations.png) 

Az elemzés "Advisor" szakaszban is megtekintheti:

![a rugalmas készlet javaslatok-tanácsadó](./media/sql-database-manage-after-migration/advisor-section.png)

### <a name="how-do-i-monitor-the-performance-and-resource-utilization-in-sql-database"></a>Hogyan figyelhetek a teljesítmény- és erőforrás-használat az SQL-adatbázis?

Az SQL-adatbázis kihasználhatja az intelligens insights figyelemmel kísérni a teljesítményét, és ennek megfelelően beállításának platform. Figyelhető a teljesítmény- és erőforrás-használat az SQL-adatbázis a következő módszerekkel:
- **Azure-portálon**: az Azure portál jelölje ki az adatbázist, a diagram az Áttekintés ablaktábláján kattintson egy önálló adatbázist Erőforrás kihasználtsága jeleníti meg. Módosíthatja a diagram megjelenítése több metrika, beleértve a processzor, DTU százalékos, adat IO százalékos, munkamenetek százalékos és adatbázis méretének százalékában.

   ![Figyelés diagramra](./media/sql-database-manage-after-migration/monitoring-chart.png)

   ![Figyelési chart2](./media/sql-database-manage-after-migration/chart.png)

Ez a diagram az erőforrás riasztásokat is konfigurálhatja. Ezek a riasztások teszik lehetővé az e-mailt erőforrás feltételek válaszolni, HTTPS vagy HTTP-végponttal való írás vagy egy műveletet. Tekintse meg a [adatbázis SQL-adatbázis teljesítményének figyelése](sql-database-single-database-monitor.md) részletes információkra van szüksége.

- **Dinamikus felügyeleti nézetek**: lekérheti a [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) erőforrás-fogyasztás statisztika előzmények visszaadására az elmúlt egy órában dinamikus kezelési nézetet és a [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) rendszer katalógusnézet használatával derítheti ki előzmények vissza az elmúlt 14 napban.
- **Lekérdezési Terheléselemzőhöz**: [lekérdezési Terheléselemző](sql-database-query-performance.md) lehetővé teszi az erőforrás-igényes leggyakoribb lekérdezések és a hosszan futó lekérdezések egy adott adatbázis egy előzményei között találja. Gyorsan azonosíthatja a LEGGYAKORIBB lekérdezések erőforrás-használat, időtartama és végrehajtási gyakorisága. Nyomon követheti a lekérdezéseket, és regressziós észleléséhez. A szolgáltatás használatához [Lekérdezéstár](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) engedélyezve van, és az adatbázis aktív.

   ![Lekérdezési terheléselemző](./media/sql-database-manage-after-migration/query-performance-insight.png)

- **Az Azure SQL elemzés (előzetes verzió) Naplóelemzési**: [Azure Naplóelemzés](../log-analytics/log-analytics-azure-sql.md) legfeljebb 150 000 SQL-adatbázisok és 5000 SQL rugalmas készletek / támogatása lehetővé teszi az adatgyűjtésre és az Azure SQL Azure fő teljesítménymutatók megjelenítéséhez munkaterület. Használhatja a figyelheti és értesítéseket kaphat. Figyelheti az SQL-adatbázis és a rugalmas készlet metrikák több Azure-előfizetések és rugalmas készletek és alkalmazás-készlet minden egyes rétegben problémák azonosításához használható.

### <a name="i-am-noticing-performance-issues-how-does-my-sql-database-troubleshooting-methodology-differ-from-sql-server"></a>Vagyok észre a teljesítményproblémák: az SQL-adatbázis hibaelhárítási módszertana eltérések a SQL Server?
Lekérdezés diagnosztizálására használja a hibaelhárítási eljárásokkal jelentős részét, és az adatbázis teljesítményproblémák változatlan marad. Után minden ugyanazt az SQL Server adatbázismotor a felhő megoldásaira épül. Azonban a platform - Azure SQL Database beépített rendelkezik "eszközintelligencia". Ez segítséget hibaelhárítását és diagnosztizálását még könnyebben teljesítményproblémákat. Azt is elvégezhetők a következő javítási műveletek a nevünkben, és egyes esetekben, proaktív automatikusan kijavítja azokat. 

A teljesítménnyel kapcsolatos problémák elhárításakor megközelítését jelentős mértékben kihasználhassa intelligens szolgáltatásaival, mint [lekérdezési teljesítmény Insight(QPI)](sql-database-query-performance.md) és [Database Advisor](sql-database-advisor.md) együtt és a különbség a módszer eltér, mert a tekintetben – már nem kell tennie, a fontos részleteket, amelyek segíthetnek őrlés manuális tevékenység hibaelhárítást az elvégzendő. A platform a rögzített munkát elvégzi. Egy példa, amely QPI. A QPI egészen a lekérdezés szint lebontva, és nézze meg a múltbeli trendek szabadon mérje fel, ha pontosan a lekérdezés közleményében szerepelt. A Database Advisor lehetővé teszi az ajánlott módszer az általános teljesítménye általában javítják hasonlóan – hiányzó indexek, amelyekkel a eldobja az indexeket, paraméterezése a lekérdezések stb. 

A teljesítmény a hibaelhárításban, fontos annak megállapítására, hogy csak az alkalmazás, vagy az adatbázis biztonsági, amely van hatással az alkalmazások teljesítményéről. Gyakran a teljesítmény probléma van az alkalmazás rétegben. Az architektúra vagy az adathozzáférési minták lehet. Tegyük fel, hogy egy chatty alkalmazás, amely a hálózati késés-és nagybetűket. Ebben az esetben az alkalmazás szenved, mert oda-vissza fog sok rövid kérelem lenne ("chatty") az alkalmazás és a kiszolgáló között, és a túlterhelt hálózatra csatlakozik, a használatával egyezzen gyors. Ebben az esetben javíthatja a teljesítményt, használhatja a [kötegelt lekérdezések](sql-database-performance-guidance.md#batch-queries). Kötegek használata segít rendkívüli mértékben mivel most egy kötegben; a kérelmek feldolgozása így segít a körbejárási késés kevesebb és az alkalmazások teljesítményének növelésére. 

Emellett ha azt tapasztalja, az általános teljesítményt, az adatbázis egy akár teljesítménycsökkenés, figyelheti a [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) és [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) dinamikus felügyeleti nézetek annak érdekében, hogy Ismerje meg a CPU, i/o és memória-felhasználás. A lehet a teljesítményre, mert az adatbázis-erőforrások van függeszteni. Lehet, hogy szükség lehet módosítani a teljesítményszintet és/vagy a növekvő és terheléshez zsugorítását alapján szolgáltatásréteget. 

Teljesítményproblémák hangolással kapcsolatos ajánlások átfogó gyűjteményét, lásd: [hangolja az adatbázis](sql-database-performance-guidance.md#tune-your-database).

### <a name="how-do-i-ensure-i-am-using-the-appropriate-service-tier-and-performance-level"></a>Hogyan biztosítása, a megfelelő és teljesítményszintet szolgáltatásszintet használok?
SQL Database különböző szolgáltatásrétegeiben használt funkciókkal Basic, Standard és Premium kínál. Egyes szolgáltatásszinteken kap egy szolgáltatási szint kötött garantált kiszámítható teljesítményt. Attól függően, hogy a munkaterhelés – előfordulhat, hogy felszakadásáig tevékenység ahol az erőforrás-használat előfordulhat, hogy elérte a aktuális teljesítményszintjének, amelynek Ön a határértéket. Ilyen esetekben célszerű kiértékelésével elindítása e bármely hangolása segítségével (például hozzáadása vagy módosítása egy index stb.). Ha továbbra is problémák korlátot, fontolja meg egy magasabb teljesítményszintre vagy a szolgáltatási szint. 

|**Szolgáltatási szint**|**Gyakori használati eset forgatókönyvek**|
|---|---|
|**Basic**|Alkalmazások egy néhány felhasználó és egy adatbázist, amely nem rendelkezik magas egyidejűségi, a méretezés és teljesítmény követelményeinek. |
|**Standard**|Jelentős feldolgozási, a méretezés és teljesítmény-követelményekkel rendelkező alkalmazások alacsony, közepes méretű IO igényeket támaszt alapján kialakulhat. |
|**Prémium**|Egyidejű felhasználók, a magas Processzor/memória és a nagy mennyiségű alkalmazások IO követelményeket. Nagy feldolgozási, a magas teljesítmény és a késés érzékeny alkalmazások használhatják fel a prémium szintű. |
|||

Adja meg a megfelelő arról, hogy a jobb teljesítmény szintjén, a lekérdezés- és adatbázis hálózatierőforrás-fogyasztás "Hogyan követhetem figyelemmel a teljesítmény- és erőforrás-használat az SQL-adatbázis" a fent említett eljárások valamelyikével keresztül figyelheti. Kell látnia, hogy a lekérdezések/adatbázisok következetesen futnak Processzor/memória stb. érdemes lehet egy magasabb teljesítményszintre vertikális felskálázásával a gyakran használt adatok. Hasonlóképpen vegye figyelembe, hogy a csúcsidőben még akkor is, ha nem úgy tűnik, az erőforrások használata a lehető legtöbb; Vegye figyelembe, hogy a jelenlegi teljesítményszintje a méretezés. 

Ha egy SaaS-alkalmazás mintát vagy egy adatbázis összevonási forgatókönyv esetén érdemes egy rugalmas készlet költség optimalizálásra vonatkozóan. A rugalmas készlet kiváló módja a adatbázis konszolidáció és költség-optimalizálás eléréséhez. További rugalmas készlet használatával több adatbázis-kezelés, lásd: [készletek és adatbázisok kezelése](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal). 

### <a name="how-often-do-i-need-to-run-database-integrity-checks-for-my-database"></a>Milyen gyakran kell az adatbázis adatbázis sértetlenségi ellenőrzések futtatásához?
SQL-adatbázis bizonyos intelligens módszereket, amelyek lehetővé teszik, hogy kezelni bizonyos osztályokat, az adatok sérülésének, automatikusan és adatvesztés nélkül használja. Ezek a technológiák a szolgáltatás beépített, és használja ki a szolgáltatás mikor kell merül fel. Rendszeres időközönként a szolgáltatásban, az adatbázis biztonsági másolatait kell megvizsgálni, helyreállítását és a DBCC CHECKDB fut rajta. Ha problémák vannak, SQL-adatbázis proaktív módon kezeli őket. [Automatikus javítás](/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring) olyan lapok, amelyek sérült, vagy adatok épségével kapcsolatos problémák elhárítása. Az adatbázis lapok mindig ellenőrzése igazolja, hogy a lap alapértelmezett ELLENŐRZŐÖSSZEG beállítású. SQL-adatbázis proaktív figyeli, és ellenőrzi, hogy az adatok integritását, az adatbázis és, ha problémák merülnek fel, címeinek őket a legmagasabb prioritású. Ezek mellett választhatja, hogy igény szerint futtathat a saját integritás-ellenőrzést a fog.  További információkért lásd: [adatintegritás az SQL-adatbázis](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)

## <a name="data-movement-after-migration"></a>Adatátvitel áttelepítés után

### <a name="how-do-i-export-and-import-data-as-bacpac-files-from-sql-database"></a>Hogyan exportálása és importálhat adatokat az SQL-adatbázis BACPAC-fájlok formájában?

- **Exportálás**: Azure-portálról BACPAC-fájlként exportálhatja az Azure SQL-adatbázis

   ![Adatbázis exportálása](./media/sql-database-export/database-export.png)

- **Importálás**: is importálhat adatokat BACPAC-fájlként az adatbázisba, az Azure portál használatával.

   ![Adatbázis importálása](./media/sql-database-import/import.png)

### <a name="how-do-i-synchronize-data-between-sql-database-and-sql-server"></a>Hogyan szinkronizálni a SQL Database és SQL Server közötti?
Ennek eléréséhez többféleképpen közül választhat: 
- **[Adatszinkronizálás](sql-database-sync-data.md)**  – Ez a szolgáltatás segít a két irányban több helyszíni SQL Server-adatbázisok és SQL-adatbázis közötti adatszinkronizáláshoz. A helyszíni SQL Server-adatbázisok szinkronizálni, szüksége telepítése és konfigurálása a sync-ügynök a helyi számítógépen, és nyissa meg a kimenő TCP 1433-as port.
- **[Tranzakciós replikáció](https://azure.microsoft.com/blog/transactional-replication-to-azure-sql-database-is-now-generally-available/)**  – tranzakciós replikáció szinkronizálhatja a helyszíni adatait az Azure SQL Adatbázishoz a helyi a közzétevő és az Azure SQL Database az előfizető alatt. Egyelőre csak a telepítés esetén támogatott. Hogyan telepíthetők át az adatokat a helyszíni Azure SQL minimális állásidővel további információkért lásd: [használata tranzakciós replikáció](sql-database-cloud-migrate.md#method-2-use-transactional-replication)

## <a name="next-steps"></a>További lépések
További tudnivalók [SQL-adatbázis](sql-database-technical-overview.md).

