---
title: Az Azure SQL Database speciális biztonsági áttekintése |} A Microsoft Docs
description: Ez a témakör ismerteti az Azure SQL Database által nyújtott adatbiztonság speciális, és bemutatja, hogyan működik, és hogyan eltér az Azure SQL Database önálló vagy készletezett adatbázis.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab, vanto
manager: craigg
ms.date: 03/29/2019
ms.openlocfilehash: 5c9e11572bc142637066214e1a807a80ce711c48
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58877686"
---
# <a name="use-sql-database-advanced-data-security-with-virtual-networks-and-near-100-compatibility"></a>Speciális biztonsági adatokat, a virtuális hálózatok és a közel 100 %-os kompatibilitást SQL Database használata

Felügyelt példány az Azure SQL Database, így a legújabb SQL Server-közel 100 %-os kompatibilitást a helyszíni adatbázis-kezelő (Enterprise Edition), a natív új telepítési lehetősége [virtuális hálózat (VNet)](../virtual-network/virtual-networks-overview.md) megvalósítás, amelyben a közös biztonsági kérdéseket, és a egy [üzleti modell](https://azure.microsoft.com/pricing/details/sql-database/) kedvező a helyszíni SQL Server-ügyfelek számára. A felügyelt példány üzembe helyezési modell lehetővé teszi meglévő SQL Server a felhőre való minimális alkalmazás- és adatbázis módosításait azok a helyszíni alkalmazások átemelése. Egy időben, a felügyelt példány üzembe helyezési lehetőség megőrzi az összes PaaS-képességet (automatikus javítás és a verzió frissítések [automatizált biztonsági mentések](sql-database-automated-backups.md), [magas rendelkezésre állású](sql-database-high-availability.md) ), azaz jelentősen csökkenti a kezelési terheket és a teljes bekerülési Költséget.

> [!IMPORTANT]
> Egy régióban, amelyben a felügyelt példány üzembe helyezési lehetőség jelenleg elérhető listáját lásd: [támogatott régiók](sql-database-managed-instance-resource-limits.md#supported-regions).

Az alábbi ábrán a felügyelt példányok legfontosabb funkcióit ismerteti:

![a legfontosabb jellemzők](./media/sql-database-managed-instance/key-features.png)

A felügyelt példány üzembe helyezési modell célja az ügyfelek számára az alkalmazások nagy számú át a helyszíni vagy IaaS, saját használatra készült, vagy független Szoftverszállító előírt környezet teljes körűen felügyelt PaaS-felhőalapú környezethez, alacsony áttelepítési energiát, a lehető. Használja a teljes mértékben automatizált [Data Migration Service (DMS)](../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance) az Azure-on ügyfelei is átemelése a helyszíni SQL Server egy felügyelt példányra, amely kompatibilis a helyszíni SQL Server és a teljes elkülönítést ügyfél példány virtuális hálózatok közötti támogatást biztosít.  Frissítési garanciával működő, exchange is kedvezményes díjszabást kínál a felügyelt példány használatával a meglévő licenceit a [SQL Serverhez készült Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/).  Felügyelt példány az a legjobb áttelepítési hely a fokozott biztonságot és a egy gazdag programozhatóság surface igénylő SQL Server-példányokat a felhőben.

A felügyelt példány üzembe helyezési lehetőség célok közel 100 %-os támadási kompatibilitási legújabb verziójával a helyszíni SQL Server egy előkészített kiadási csomag tesz lehetővé.

Dönthet arról, hogy az Azure SQL Database üzembe helyezési lehetőségek között: önálló adatbázis, a készletezett adatbázis és a felügyelt példány és a virtuális gépen üzemeltetett SQL Serverrel lásd [hogyan választható ki a megfelelő verziót az SQL Server az Azure-ban](sql-database-paas-vs-sql-server-iaas.md).

## <a name="key-features-and-capabilities"></a>Legfontosabb funkciói és képességei

A felügyelt példány ötvözi a legjobb funkciókat, Azure SQL Database és SQL Server adatbázismotor is elérhető.

> [!IMPORTANT]
> Felügyelt példány az a funkciók az SQL Server online műveletek automatikus terv javításokat és más vállalati teljesítményt érintő továbbfejlesztés többek között a legújabb verziójának futtatja. Elérhető szolgáltatásainak összehasonlítása ezen [szolgáltatások összehasonlítása: Az Azure SQL Database és az SQL Server összehasonlítása](sql-database-features.md).

| **PaaS előnyei** | **Az üzletmenet folytonossága** |
| --- | --- |
|Hardver megvásárlása és kezelése <br>Nincs felügyeleti többletterhelést okoz az alapul szolgáló infrastruktúra kezelése <br>Gyors kiépítés és a szolgáltatás méretezése <br>Automatikus javítás és -verzió frissítése <br>Egyéb PaaS data services-integráció |99,99 %-os SLA-t  <br>A beépített [magas rendelkezésre állás](sql-database-high-availability.md) <br>A védett adatok [automatizált biztonsági mentések](sql-database-automated-backups.md) <br>Ügyfél konfigurálható biztonsági másolatainak megőrzési ideje <br>Felhasználó által kezdeményezett [biztonsági mentések](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql?view=azuresqldb-mi-current) <br>[Időponthoz kötött adatbázis-visszaállítás](sql-database-recovery-using-backups.md#point-in-time-restore) képesség |
|**Biztonság és megfelelőség** | **Kezelés**|
|Izolált környezet ([VNet-integráció](sql-database-managed-instance-connectivity-architecture.md), egyetlen dedikált számítási és a storage szolgáltatás bérlői) <br>[Transzparens adattitkosítás (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>[Az Azure AD-hitelesítés](sql-database-aad-authentication.md), egyszeri bejelentkezésének támogatása <br> <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Az Azure AD kiszolgáló rendszerbiztonsági tagok (Bejelentkezések)</a> (**nyilvános előzetes verzióban**) <br>Megfelelőségi szabványoknak megfelelő ugyanaz, mint az Azure SQL database <br>[SQL auditing (SQL-naplózás)](sql-database-managed-instance-auditing.md) <br>[Fenyegetések észlelése](sql-database-managed-instance-threat-detection.md) |Automatizálhatja a szolgáltatás üzembe helyezését és skálázás az Azure Resource Manager API <br>Az Azure portal funkciók manuális szolgáltatáshoz kiépítés és méretezés <br>Data Migration Service

> [!IMPORTANT]
> Az Azure SQL Database (az összes központi telepítési beállítások), egy több megfelelőségi szabvány tanúsított módon. További információkat az [Azure biztonsági és adatkezelési központban](https://azure.microsoft.com/support/trust-center/) talál, az [SQL Database megfelelőségi tanúsítványainak](https://www.microsoft.com/trustcenter/compliance/complianceofferings) aktuális listájával együtt.

A felügyelt példányok legfontosabb funkcióit az alábbi táblázatban láthatók:

|Szolgáltatás | Leírás|
|---|---|
| SQL Server-verzió létrehozása / | SQL Server Database Engine (poslední stabilní) |
| Automatikus biztonsági másolatok kezelése | Igen |
| Beépített példányt és az adatbázis figyelése és metrikák | Igen |
| A szoftverfrissítések automatikus javítás | Igen |
| A legújabb adatbázismotor-funkciók | Igen |
| Az adatbázisonkénti adatfájlt (sor) | Többszörös |
| Adatbázisonként (napló) fájlok száma | 1 |
| Virtuális hálózat – Azure Resource Manager üzembe helyezése | Igen |
| Virtuális hálózat – klasszikus üzemi modellben | Nem |
| Portal-támogatás | Igen|
| Beépített integrációs szolgáltatás (SSIS) | Nem – SSIS része [Azure Data Factory PaaS](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) |
| Beépített Analysis Service (SSAS) | Nem – SSAS elkülönül [PaaS](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) |
| Built-in Reporting Service (SSRS) | Nem – a Power bi-ban vagy az SSRS IaaS használata |
|||

## <a name="vcore-based-purchasing-model"></a>Virtuálismag-alapú vásárlási modell

A [Virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md) felügyelt példányok, rugalmasság, ellenőrzés és átláthatóságot biztosít, és a közvetlen módon lefordítani a helyszíni tevékenységprofil követelményeinek a felhőben. Ez a modell lehetővé teszi, hogy számítási, memória és a munkaterhelés igényei alapján tárolási módosíthatja. A Virtuálismag-modell nem is jogosult fel, a 30 %-os megtakarítást a [SQL Serverhez készült Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/).

A Virtuálismag-modell választhat hardver generációja.

- **Gen4** logikai processzorok alapuló Intel E5-2673 v3 (Haswell) 2,4 GHz-es processzor, a csatlakoztatott SSD fizikai mag, 7 GB RAM-MAL / core és a számítási méret közötti 24, és 8 virtuális maggal.
- **Gen5** logikai processzorok alapuló Intel E5-2673 v4 (Broadwell) 2.3 GHz-es processzorokkal, NVMe SSD, a hyper-threaded logikai mag, gyors, és 8 és 80 magok közötti méretek számítási.

További információ a különbség a hardvergenerációk [felügyelt példány erőforráskorlátok](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).

## <a name="managed-instance-service-tiers"></a>Felügyelt példány szolgáltatási szintek

Felügyelt példány szolgáltatáscsomagban érhető el:

- **Általános célú**: Tipikus teljesítménnyel és i/o-késésre vonatkozó követelmény az alkalmazások számára készült.
- **Üzletileg kritikus**: Alacsony i/o-késésre vonatkozó követelmény és az alapul szolgáló karbantartási műveleteket a számítási feladatok minimális hatása az alkalmazások számára készült.

Mindkét szolgáltatásszintek 99,99 %-os rendelkezésre állásának biztosítása, és egymástól függetlenül válassza ki a tároló mérete és a számítási kapacitást. Az Azure SQL Database a magas rendelkezésre állású architektúrával további információkért lásd: [magas rendelkezésre állást és az Azure SQL Database](sql-database-high-availability.md).

### <a name="general-purpose-service-tier"></a>Általános célú szolgáltatásszint

Az alábbi lista ismerteti az általános célú szolgáltatásszint kulcsfontosságú jellemzője:

- A legtöbb üzleti alkalmazások, az általános teljesítmény-követelmények tervezése
- Nagy teljesítményű Azure Blob storage (8 TB)
- Beépített [magas rendelkezésre állású](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) megbízható Azure Blob Storage-alapú és [Azure Service Fabric](../service-fabric/service-fabric-overview.md)

További információkért lásd: [tárolási réteg az általános célú csomagban](https://medium.com/azure-sqldb-managed-instance/file-layout-in-general-purpose-azure-sql-managed-instance-cf21fff9c76c) és [tárolási teljesítmény ajánlott eljárások és szempontjai felügyelt példányai (általános célú)](https://blogs.msdn.microsoft.com/sqlcat/2018/07/20/storage-performance-best-practices-and-considerations-for-azure-sql-db-managed-instance-general-purpose/).

További információ a szolgáltatási szintek közötti különbség [felügyelt példány erőforráskorlátok](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

### <a name="business-critical-service-tier"></a>Üzleti kritikus fontosságú szolgáltatási szint

Üzleti kritikus szolgáltatási szinten a nagy i/o-követelményekkel rendelkező alkalmazások épül. Több elkülönített replika használatával legmagasabb hibatűrést biztosít.

Az alábbi lista ismerteti azokat az üzletileg kritikus szolgáltatási rétegben főbb jellemzői:

- A lehető legjobb teljesítmény és a magas rendelkezésre ÁLLÁSÚ követelmények üzleti alkalmazásokhoz tervezve
- Együttműködik a adathozzáférésnek köszönhetően gyors helyi SSD-tárolás (legfeljebb 1 TB-ig a Gen4 és akár 4 TB-ra Gen5)
- Beépített [magas rendelkezésre állású](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) alapján [Always On rendelkezésre állási csoportok](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) és [Azure Service Fabric](../service-fabric/service-fabric-overview.md).
- További beépített [csak olvasható adatbázis-replika](sql-database-read-scale-out.md) , amely a jelentéskészítés és más csak olvasási számítási feladatokhoz használható
- [Memóriabeli OLTP](sql-database-in-memory.md) , amely a nagy teljesítményű követelményekkel rendelkező számítási feladatokhoz használható  

További információ a szolgáltatási szintek közötti különbség [felügyelt példány erőforráskorlátok](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

## <a name="advanced-security-and-compliance"></a>Magas szintű biztonság és megfelelőség

A felügyelt példány üzembe helyezési lehetőség egyesíti az Azure-felhőben és az SQL Server adatbázismotor által biztosított fejlett biztonsági funkciókat.

### <a name="managed-instance-security-isolation"></a>Felügyelt példány biztonsági elkülönítés

Felügyelt példány az Azure-felhőben más bérlőktől származó további biztonsági elkülönítés biztosít. Biztonsági elkülönítés tartalmazza:

- [Natív virtuálishálózat-implementáció](sql-database-managed-instance-connectivity-architecture.md) és a helyszíni környezet Azure Express Route vagy VPN-átjáró használatával való kapcsolódás.
- SQL-végpont csak egy magánhálózati IP-cím, lehetővé téve a biztonságos kapcsolat a privát Azure vagy hibrid hálózatok keresztül van közzétéve.
- Egybérlős dedikált alapjául szolgáló infrastruktúrával (számítási, tárolási).

A következő ábra ismerteti az alkalmazások különféle kapcsolódási lehetőségei:

![magas rendelkezésre állás](./media/sql-database-managed-instance/application-deployment-topologies.png)  

VNet-integráció és hálózatkezelési szabályzatok érvénybe léptetéséhez az alhálózat szintjén kapcsolatos további részletekért, lásd: [felügyelt példányok VNet-architektúrát](sql-database-managed-instance-connectivity-architecture.md) és [a felügyelt példány alkalmazás csatlakoztatása](sql-database-managed-instance-connect-app.md).

> [!IMPORTANT]
> Helyezze el a több felügyelt példány ugyanazon az alhálózaton, bárhol is, amely által engedélyezett a biztonsági követelmények szerint, amely tartalomtérkép érhető el, további előnyöket és juttatásokat. Helymegosztást példányok ugyanazon az alhálózaton jelentősen hálózati infrastruktúra-karbantartás leegyszerűsítésében, és csökkentheti a kiépítés ideje, mivel hosszú kiépítés időtartama társítva az alhálózat első felügyelt példány üzembe helyezése költsége példány.

### <a name="azure-sql-database-security-features"></a>Az Azure SQL Database biztonsági funkciók

Az Azure SQL Database biztosít, amelyek segítségével az adatok védelme a speciális biztonsági funkciók.

- [Felügyelt példány naplózás](sql-database-managed-instance-auditing.md) nyomon követi az adatbázisok eseményeit, és a egy naplófájl helyezi el az Azure storage-fiók írja őket. Naplózás segíthet a jogszabályi, adatbázis-tevékenység megértésében, valamint betekintést nyerhet az eltéréseket és rendellenességeket, amelyek üzleti aggályokra vagy biztonsági szabálysértések.
- Adattitkosítás menet közben – felügyelt példány adatait biztosításával védi a Transport Layer Security használatával mozgásban lévő adatok titkosítását. Mellett TLS, a felügyelt példány üzembe helyezési lehetőséget kínál a bizalmas adatok útban, inaktív és a Lekérdezésfeldolgozás közben [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Az Always Encrypted az iparágban elsőként kínál páratlan adatbiztonságot a bizalmas adatok eltulajdonításával járó illetéktelen behatolások ellen. Például az Always Encrypted, hitelkártyaszámokat tartalmazó tárolódnak az adatbázisban titkosítottan mindig, még akkor is lekérdezés feldolgozása, lehetővé téve a visszafejtési hitelkártyaszámai jogosult alkalmazottak vagy az alkalmazások számára, hogy az adatok feldolgozása során.
- [Veszélyforrások detektálása](sql-database-managed-instance-threat-detection.md) egészíti ki a [naplózás](sql-database-managed-instance-auditing.md) azáltal, hogy egy további biztonsági réteget a szolgáltatás által észlelt szokatlan és vélhetően kárt okozó beépített intelligencia próbál adatbázisokat elérni vagy kiaknázni. Gyanús tevékenységek, a lehetséges biztonsági résekről, figyelmeztetést, és az SQL-injektálási támadások, valamint a rendellenes adatbázis-hozzáférési mintákról. Fenyegetésészlelési riasztások tekinthetők [az Azure Security Center](https://azure.microsoft.com/services/security-center/) , és adja meg a gyanús tevékenység részleteit, és tegyen javaslatot a műveletre vizsgálata, valamint a fenyegetés.  
- [Dinamikus adatmaszkolás](/sql/relational-databases/security/dynamic-data-masking) korlátozza a bizalmas adatok közzétételét, hogy a nem kiemelt jogosultságú felhasználók maszkolja által. Dinamikus adatmaszkolás segít a bizalmas adatokhoz való illetéktelen hozzáférés megakadályozása által meghatározhatóvá a bizalmas adatok felfedéséhez az alkalmazásrétegre gyakorolt minimális hatás mellett. Ez a szabályzatalapú biztonsági funkció elrejti a bizalmas adatokat egy kijelölt adatbázismezőkön végrehajtott lekérdezés eredményhalmazában, miközben az adatbázis adatait nem módosítja.
- [Sorszintű biztonság](/sql/relational-databases/security/row-level-security) lehetővé teszi, hogy a vezérlőelem egy adatbázistábla soraihoz való eléréséhez a lekérdezést végrehajtó felhasználó jellemzői alapján (például csoporttagság vagy végrehajtási környezet szerint). A sorszintű biztonság (RLS) egyszerűsíti az alkalmazás védelmének megtervezését és kódolását. Az RLS használatával korlátozásokat érvényesíthet az adatsorokhoz való hozzáférésre. Például annak érdekében, hogy a dolgozók férhessenek hozzá csak azok a szervezeti egységükre vonatkozó az adatsorokat, vagy egy adat-hozzáférés korlátozása csak a releváns adatokat.
- [Transzparens adattitkosítás (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) titkosítja a felügyelt példány adatfájlok, más néven az inaktív adatok titkosítása. TDE valós idejű i/o-titkosításához és visszafejtéséhez az adathoz és naplófájlhoz hajt végre. A titkosítást használ egy adatbázis-titkosítási kulcs (adattitkosítási kulcsot), a helyreállítás során a rendelkezésre állási adatbázis rendszerindító rekordját tárolt. Transzparens adattitkosítás felügyelt példány az összes adatbázis védheti meg. TDE az SQL Server kipróbált inaktív titkosítási technológia, amely számos megfelelőségi szabvány szükséges elleni védelemként.

Egy felügyelt példányra titkosított adatbázis áttelepítése az Azure Database Migration Service (DMS) vagy a natív visszaállítási keresztül támogatott. Ha egy titkosított adatbázis natív visszaállítással való áttelepítését tervezi, akkor a TDE származó meglévő tanúsítványt a helyszíni SQL Server vagy SQL Server virtuális gépen a felügyelt példány az áttelepítése nem lépésre szükség. Áttelepítési beállítások kapcsolatos további információkért lásd: [felügyelt példány az SQL Server-példány migrálása](sql-database-managed-instance-migrate.md).

## <a name="azure-active-directory-integration"></a>Azure Active Directory-integráció

A felügyelt példány üzembe helyezési lehetőség támogatja a hagyományos SQL server Database engine bejelentkezések és az Azure Active Directory (AAD) integrált bejelentkezések. Az Azure AD kiszolgáló rendszerbiztonsági tagok (Bejelentkezések) (**nyilvános előzetes verzióban**) vannak az Azure-felhőalapú változata, a helyszíni adatbázis bejelentkezések, amelyek a helyszíni környezetben használja. Az Azure AD kiszolgáló rendszerbiztonsági tagok (Bejelentkezések) lehetővé teszi, hogy adja meg a felhasználók és csoportok az Azure Active Directory-bérlőben értékét igazként példány hatókörébe tartozó rendszerbiztonsági tagok, képes bármilyen példányszintű művelet, beleértve az előfizetésen belül adatbázisközi lekérdezések végrehajtása felügyelt példány.

Egy új szintaxis létrehozása az Azure AD kiszolgáló rendszerbiztonsági tagok (Bejelentkezések) jelenik meg (**nyilvános előzetes verzióban**), **az EXTERNAL PROVIDER**. A szintaxist további információkért lásd: <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a>, és tekintse át a [üzembe helyezése az Azure Active Directory-rendszergazda, a felügyelt példány](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance) cikk.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory-integráció és többtényezős hitelesítés

A felügyelt példány üzembe helyezési lehetőség lehetővé teszi, hogy az adatbázis-felhasználók és a más Microsoft-szolgáltatások identitásainak központi kezelését [Azure Active Directory-integráció](sql-database-aad-authentication.md). Ez a funkció egyszerűsíti az engedélyek kezelését és fokozza a biztonságot. Az Azure Active Directory a [többtényezős hitelesítés](sql-database-ssms-mfa-authentication-configure.md) (MFA) támogatásával javítja az adatok és alkalmazások biztonságát, miközben támogatja az egyszeri bejelentkezést.

### <a name="authentication"></a>Authentication

Felügyelt példány hitelesítés azt jelenti, hogy felhasználók személyazonossága az adatbázishoz való csatlakozáskor. Az SQL Database két hitelesítési típust támogat:  

- **SQL-hitelesítés**:

  Ezt a hitelesítési módszert használ, egy felhasználónevet és jelszót.
- **Az Azure Active Directory-hitelesítés**:

  Ez a hitelesítési módszer az Azure Active Directory által felügyelt identitásokat használ, és a felügyelt és integrált tartományok támogatott. [Amikor csak lehet](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode), használja az Active Directory-hitelesítést (beépített biztonság).

### <a name="authorization"></a>Engedélyezés

Engedélyezési milyen egy felhasználó teheti meg az Azure SQL Database, és a felhasználói fiókjának adatbázis szerepkörtagságai és objektumszintű engedélyei vezérli hivatkozik. Egy felügyelt példányra azonos engedélyezési lehetőségeket, mint az SQL Server 2017 rendelkezik.

## <a name="database-migration"></a>Adatbázis-áttelepítés

A felügyelt példány üzembe helyezési lehetőség tárolók felhasználói esetek tömeges adatbázis áttelepítése a helyszíni vagy IaaS adatbázis-hitelesítés megvalósításához. Felügyelt példány támogatja a több adatbázis-migrálási beállítások:

### <a name="back-up-and-restore"></a>Biztonsági mentés és visszaállítás  

A migrálási megközelítés használ az SQL biztonsági másolatokat az Azure Blob storage. Az Azure storage-blobban tárolt biztonsági másolatok közvetlenül állíthatók be a felügyelt példány használatával az [T-SQL RESTORE parancsot](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=azuresqldb-mi-current).

- A rövid útmutató, amely visszaállítása a Wide World Importers – Standard adatbázis biztonsági másolatát, lásd: [visszaállítása biztonsági másolatból egy felügyelt példányra](sql-database-managed-instance-get-started-restore.md). A rövid útmutató bemutatja, hogy a biztonsági mentési fájl feltöltése az Azure-blog storage és a biztonságos, egy közös hozzáférési jogosultságkód (SAS) kulccsal rendelkezik.
- URL-címről visszaállítási kapcsolatos információkért lásd: [natív VISSZAÁLLÍTÁSA URL-címről](sql-database-managed-instance-migrate.md#native-restore-from-url).

> [!IMPORTANT]
> Felügyelt példány biztonsági mentések csak állítható vissza egy másik felügyelt példányhoz. Ezek nem állítható vissza egy helyszíni SQL Server- vagy egy önálló adatbázis és rugalmas készlethez.

### <a name="data-migration-service"></a>Data Migration Service

Az Azure Database Migration Service egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a zökkenőmentes migrálást a több adatbázis-forrásokhoz, az Azure-adatplatformokra minimális állásidővel. Ez a szolgáltatás leegyszerűsíti a meglévő harmadik féltől származó és az SQL Server-adatbázisok Azure SQL Database (önálló adatbázisok, rugalmas készletek a készletezett adatbázisok és példányok adatbázisai a felügyelt példány) és az SQL Server Azure virtuális gép áthelyezése szükséges feladatok. Lásd: [a helyszíni adatbázis Migrálása a felügyelt példány DMS használatával](https://aka.ms/migratetoMIusingDMS).

## <a name="sql-features-supported"></a>Támogatott SQL-funkciók

A felügyelt példány üzembe helyezési lehetőség célja, hogy közel 100 %-os támadási kompatibilitást a helyszíni SQL Server szolgáltatás általános rendelkezésre állása amíg fázisában hamarosan. Az a funkciók és összehasonlító listában, lásd: [SQL Database funkcióinak összehasonlítása](sql-database-features.md), és a egy T-SQL eltérései az SQL Server és a felügyelt példányok listáját lásd: [a felügyelt példány T-SQL különbségek az SQL Server](sql-database-managed-instance-transact-sql-information.md).

A felügyelt példány üzembe helyezési lehetőség támogatja az előző verziókkal való kompatibilitás SQL 2008 adatbázis. Az SQL 2005 adatbázis-kiszolgálók közvetlen áttelepítése támogatott, az SQL 2005 migrált adatbázisok kompatibilitási szintje frissítve lett, hogy az SQL 2008.
  
A következő ábra a felügyelt példány támadási kompatibilitási ismerteti:  

![Áttelepítés](./media/sql-database-managed-instance/migration.png)

### <a name="key-differences-between-sql-server-on-premises-and-in-a-managed-instance"></a>Fontos különbség a helyszíni SQL Server között, és a egy felügyelt példány

A felügyelt példány üzembe helyezési lehetőség előnyöket nem mindig felfelé-elejétől számított a felhőben, ami azt jelenti, hogy egyes funkciók a helyszíni SQL Server lehet akár elavult, elavult, vagy rendelkezik alternatívákat. Ha ismeri fel, hogy egy adott funkció kissé eltérő módon működik-e, vagy az, hogy a szolgáltatás nem fut egy környezetben, akkor nem teljes mértékben szabályozzák, eszközöket kell, vannak bizonyos esetekben:

- Magas rendelkezésre állású jön létre, és előre konfigurálva van a hasonló technológiával [Always On rendelkezésre állási csoportok](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server).
- Automatikus biztonsági mentések és időponthoz kötött visszaállítás pont. Kezdeményezheti az ügyfél `copy-only` biztonsági mentések, amelyek nem zavarják a automatikus biztonsági mentési láncolatát.
- Felügyelt példány nem engedélyezi a teljes fizikai elérési út megadásával, így az összes megfelelő forgatókönyv kell másképp támogatja: Adatbázis VISSZAÁLLÍTÁSA nem támogatja a WITH MOVE DB létrehozása nem, itt engedélyezheti, fizikai elérési útját, az Azure Blobszolgáltatása révén a TÖMEGES Beszúrás works csak stb.
- Felügyelt példány által támogatott [Azure AD-hitelesítés](sql-database-aad-authentication.md) felhőalapú alternatív Windows-hitelesítés.
- Felügyelt példány automatikusan kezeli a XTP fájlcsoport és fájlok In-Memory OLTP objektumokat tartalmazó adatbázisok
- Felügyelt példány támogatja-e az SQL Server Integration Services (SSIS) és a gazdagép SSIS-katalógus (SSISDB), amely tárolja az SSIS-csomagokat is, de ezeket a rendszer végrehajtja a egy felügyelt Azure-SSIS integrációs modul (IR) az Azure Data Factory (ADF), lásd: [létrehozása Az Azure-SSIS integrációs modul az ADF-ben](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). Hasonlítsa össze az SSIS-funkciók az SQL Database, lásd: [hasonlítsa össze az Azure SQL Database önálló adatbázisok és rugalmas készletek és a felügyelt példány](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance).

### <a name="managed-instance-administration-features"></a>Felügyelt példány felügyeleti funkciók

A felügyelt példány üzembe helyezési beállítás lehetővé teszi a rendszergazdák számára, hogy kevesebb időt töltenek a felügyeleti feladatokat, mert az SQL Database szolgáltatás hajtja végre őket az Ön számára, vagy jelentősen leegyszerűsíti a ezeket a feladatokat. Például [operációs rendszer / relációsadatbázis-kezelő rendszer telepítési és a javítással](sql-database-high-availability.md), [dinamikus példány átméretezése és konfigurációs](sql-database-single-database-scale.md), [biztonsági mentések](sql-database-automated-backups.md), [adatbázis-replikáció](replication-with-sql-database-managed-instance.md)(beleértve a rendszeradatbázisok), [magas rendelkezésre állású konfiguráció](sql-database-high-availability.md), és konfigurációs állapotának és [alkalmazásteljesítmény-figyelési](../azure-monitor/insights/azure-sql.md) adatfolyamokat.

> [!IMPORTANT]
> Támogatott, részlegesen támogatott és nem támogatott funkciók listáját lásd: [SQL Database funkciói](sql-database-features.md). T-SQL eltérései az SQL Server és a felügyelt példányok listáját lásd: [felügyelt példány T-SQL különbségek az SQL Serverről](sql-database-managed-instance-transact-sql-information.md)

### <a name="how-to-programmatically-identify-a-managed-instance"></a>Programozott módon a felügyelt példányok azonosítása

Az alábbi táblázat Transact SQL-n keresztül elérhető számos tulajdonság, hogy észleli, hogy az alkalmazás a felügyelt példány működik, és fontos tulajdonságok beolvasása.

|Tulajdonság|Érték|Megjegyzés|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM) - 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation.|Ez ugyanaz, mint az SQL Database értéke.|
|`SERVERPROPERTY ('Edition')`|SQL Azure|Ez ugyanaz, mint az SQL Database értéke.|
|`SERVERPROPERTY('EngineEdition')`|8|Ez az érték a felügyelt példány egyedileg azonosítja.|
|`@@SERVERNAME`;  `SERVERPROPERTY ('ServerName')`|Példány teljes DNS-név a következő formátumban:`<instanceName>`.`<dnsPrefix>`.Database.Windows.NET, ahol `<instanceName>` az ügyfél által megadott név közben `<dnsPrefix>` a neve, ami garantálja az globális DNS neve egyedi-e automatikusan létrehozott része ("wcus17662feb9ce98", például)|Example: my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan hozhat létre az első felügyelt példányhoz, lásd: [a rövid útmutató](sql-database-managed-instance-get-started.md).
- Az a funkciók és összehasonlító listában, lásd: [általános SQL-szolgáltatások](sql-database-features.md).
- Virtuális hálózati konfigurációval kapcsolatos további információkért lásd: [felügyelt példányok Vnetjének konfigurálásával](sql-database-managed-instance-connectivity-architecture.md).
- Ha a rövid útmutató, amely létrehoz egy felügyelt példányt, és visszaállít egy adatbázist egy biztonsági mentési fájlból, lásd: [felügyelt példány létrehozása](sql-database-managed-instance-get-started.md).
- Áttelepítés az Azure Database Migration Service (DMS) használatával foglalkozó oktatóanyagért lásd: [DMS használatával példány migrálása felügyelt](../dms/tutorial-sql-server-to-managed-instance.md).
- A speciális hibaelhárítási beépített intelligenciával felügyelt példány adatbázis-teljesítmény figyelését: [figyelése Azure SQL Database az Azure SQL Analytics használatával](../azure-monitor/insights/azure-sql.md)
- Díjszabási információkért tekintse meg a [SQL Database felügyelt példány díjszabása](https://azure.microsoft.com/pricing/details/sql-database/managed/).
