---
title: Az Azure SQL Database felügyelt példány – áttekintés |} A Microsoft Docs
description: Ez a témakör ismerteti az Azure SQL Database felügyelt példányába, és azt ismerteti, hogyan működik, és hogyan eltér az Azure SQL Database egy adatbázis.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab
manager: craigg
ms.date: 10/15/2018
ms.openlocfilehash: f1b2f9c7ee9d2fbeec51315a5e348b197fb4c06b
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2018
ms.locfileid: "49342500"
---
# <a name="azure-sql-database-managed-instance-deployment-model-with-virtual-network-and-near-100-sql-server-compatibility"></a>Az Azure SQL Database felügyelt példányain üzemi modell virtuális hálózattal, és közel 100 %-os SQL-kompatibilitás

Az Azure SQL Database felügyelt példánya egy új központi telepítési modellt az Azure SQL Database, így a legújabb SQL Server-közel 100 %-os kompatibilitást a helyszíni adatbázis-kezelő (Enterprise Edition), a natív [virtuális hálózat (VNet)](../virtual-network/virtual-networks-overview.md) végrehajtását, amelyek közös biztonsági kérdéseket, és a egy [üzleti modell](https://azure.microsoft.com/pricing/details/sql-database/) kedvező a helyszíni SQL Server-ügyfelek számára. Felügyelt példány lehetővé teszi meglévő SQL Server a felhőre való minimális alkalmazás- és adatbázis módosításait azok a helyszíni alkalmazások átemelése. Felügyelt példány egyszerre, megőrzi az összes PaaS-képességet (automatikus javítás és a verzió frissítési [automatizált biztonsági mentések](sql-database-automated-backups.md), [magas rendelkezésre állású](sql-database-high-availability.md) ), amely jelentősen csökkenti felügyeleti költségeket és a teljes bekerülési Költséget.

> [!IMPORTANT]
> Felügyelt példány jelenleg elérhető, amelyben régiók listáját lásd: [támogatott régiók](sql-database-managed-instance-resource-limits.md#supported-regions).

A következő ábra a felügyelt példány legfontosabb funkcióit ismerteti:

![a legfontosabb jellemzők](./media/sql-database-managed-instance/key-features.png)

Az Azure SQL Database felügyelt példánya a szeretne áttelepítést végezni az alkalmazások nagy számú helyszíni vagy IaaS, önállóan létrehozott, ügyfelek számára lett kifejlesztve, vagy ISV előírt környezet teljes körűen felügyelt PaaS-felhőalapú környezethez, alacsony áttelepítési energiát, a lehető. Használja a teljes mértékben automatizált [Data Migration Service (DMS)](../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance) az Azure-on ügyfelei is átemelése a helyszíni SQL Server egy felügyelt példányra, amely kompatibilis a helyszíni SQL Server és a teljes elkülönítést ügyfél példány virtuális hálózatok közötti támogatást biztosít.  Frissítési garanciával működő, exchange is a kedvezményes díjszabás a egy SQL Database felügyelt példánya a meglévő licenceit a [SQL Serverhez készült Azure Hybrid Use Benefit](../virtual-machines/windows/hybrid-use-benefit-licensing.md).  SQL Database felügyelt példánya a legjobb áttelepítés célja a fokozott biztonságot és a egy gazdag programozhatóság surface igénylő SQL Server-példányokat a felhőben.

Általános rendelkezésre állás a felügyelt példány célja, hogy közel 100 %-os támadási kompatibilitási legújabb verziójával a helyszíni SQL Server egy előkészített kiadási csomag kézbesítése.

Dönthet arról, hogy az Azure SQL Database önálló adatbázisok, Azure SQL Database felügyelt példánya és a tárolt virtuális gép lásd az SQL Server IaaS között [hogyan választható ki a megfelelő verziót az SQL Server Azure-felhőben](sql-database-paas-vs-sql-server-iaas.md).

## <a name="key-features-and-capabilities"></a>Legfontosabb funkciói és képességei

Az Azure SQL Database felügyelt példánya a legjobb funkciókat, amelyek elérhetők mind az Azure SQL Database és SQL Server adatbázismotor egyesíti.

> [!IMPORTANT]
> Felügyelt példány az a funkciók az SQL Server online műveletek automatikus terv javításokat és más vállalati teljesítményt érintő továbbfejlesztés többek között a legújabb verziójának futtatja. Elérhető szolgáltatásainak összehasonlítása ezen [szolgáltatások összehasonlítása: Azure SQL Database és SQL Server összehasonlítása](sql-database-features.md).

| **PaaS előnyei** | **Az üzletmenet folytonossága** |
| --- | --- |
|Hardver megvásárlása és kezelése <br>Nincs felügyeleti többletterhelést okoz az alapul szolgáló infrastruktúra kezelése <br>Gyors kiépítés és a szolgáltatás méretezése <br>Automatikus javítás és -verzió frissítése <br>Egyéb PaaS data services-integráció |99,99 %-os SLA-t  <br>A beépített [magas rendelkezésre állás](sql-database-high-availability.md) <br>A védett adatok [automatizált biztonsági mentések](sql-database-automated-backups.md) <br>Ügyfél konfigurálható biztonsági másolatainak megőrzési ideje (7 nap nyilvános előzetes verzióban érhető el a fix) <br>Felhasználó által kezdeményezett [biztonsági mentések](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql?view=azuresqldb-mi-current) <br>[Időponthoz kötött adatbázis-visszaállítás](sql-database-recovery-using-backups.md#point-in-time-restore) képesség |
|**Biztonság és megfelelőség** | **Felügyeleti**|
|Izolált környezet ([VNet-integráció](sql-database-managed-instance-vnet-configuration.md), egyetlen dedikált számítási és a storage szolgáltatás bérlői) <br>[Transzparens adattitkosítás (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>[Az Azure AD-hitelesítés](sql-database-aad-authentication.md), egyszeri bejelentkezésének támogatása <br>Megfelelőségi szabványoknak megfelelő ugyanaz, mint az Azure SQL database <br>[SQL-naplózás](sql-database-managed-instance-auditing.md) <br>[Fenyegetések észlelése](sql-database-managed-instance-threat-detection.md) |Automatizálhatja a szolgáltatás üzembe helyezését és skálázás az Azure Resource Manager API <br>Az Azure portal funkciók manuális szolgáltatáshoz kiépítés és méretezés <br>Data Migration Service

Felügyelt példány legfontosabb funkcióit az alábbi táblázatban láthatók:

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
| Beépített jelentéskészítési szolgáltatás (SSRS) | Nem – a Power bi-ban vagy az SSRS IaaS használata |
|||

## <a name="vcore-based-purchasing-model"></a>Virtuálismag-alapú vásárlási modell

A [Virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md) a felügyelt példány lehetőséget kínál a rugalmasságot, ellenőrzés, átláthatóság és lefordítani a helyszíni tevékenységprofil követelményeinek felhőbe kézenfekvő módot. Ez a modell lehetővé teszi, hogy számítási, memória és a munkaterhelés igényei alapján tárolási módosíthatja. A Virtuálismag-modell nem is jogosult fel, a 30 %-os megtakarítást a [SQL Serverhez készült Azure Hybrid Use Benefit](../virtual-machines/windows/hybrid-use-benefit-licensing.md).

A Virtuálismag-modell választhat hardver generációja.

- **4. generációs** logikai processzorok alapuló Intel E5-2673 v3 (Haswell) 2,4 GHz-es processzor, a csatlakoztatott SSD fizikai mag, 7 GB RAM-MAL / core és a számítási méret közötti 24, és 8 virtuális maggal.
- **Velikost haldy 5** logikai processzorok alapuló Intel E5-2673 v4 (Broadwell) 2.3 GHz-es processzorokkal, SSD, logikai core többszálú eNVM gyors, és 8 és 80 magok közötti méretek számítási.

További információ a különbség a hardvergenerációk [felügyelt példány erőforráskorlátok](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).

## <a name="managed-instance-service-tiers"></a>Felügyelt példány szolgáltatási szintek

Felügyelt példány szolgáltatáscsomagban érhető el:

- **Általános célú**: tipikus teljesítmény- és i/o-késési követelményekkel rendelkező alkalmazások tervezve.
- **Üzletileg kritikus (előzetes verzió)**: alacsony i/o-késésre vonatkozó követelmény és az alapul szolgáló karbantartási műveleteket a számítási feladatok minimális hatása az alkalmazások számára készült.

Mindkét szolgáltatásszintek 99,99 %-os rendelkezésre állásának biztosítása, és egymástól függetlenül válassza ki a tároló mérete és a számítási kapacitást. Az Azure SQL Database a magas rendelkezésre állású architektúrával további információkért lásd: [magas rendelkezésre állást és az Azure SQL Database](sql-database-high-availability.md).

> [!IMPORTANT]
> Az általános célú üzletileg kritikus fontosságú vagy fordítva szolgáltatásszint módosítása nem támogatott nyilvános előzetes verzióban érhető el. Ha az adatbázisokat egy példány különböző szolgáltatási rétegben, új példány létrehozása, időponthoz kötött visszaállítás ponttal rendelkező adatbázisok visszaállítása az eredeti példányban, és majd eldobni az eredeti példányban nincs többé szükség esetén. Azonban a virtuális magok és a storage felfelé vagy lefelé üzemkimaradás nélkül szolgáltatási szinten belül számát skálázhatja.

### <a name="general-purpose-service-tier"></a>Általános célú szolgáltatásszint

Az alábbi lista ismerteti az általános célú szolgáltatásszint kulcsfontosságú jellemzője:

- A legtöbb üzleti alkalmazások, az általános teljesítmény-követelmények tervezése
- Nagy teljesítményű Azure Premium storage (8 TB)
- Beépített [magas rendelkezésre állású](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) alapján megbízható Azure Premium Storage és [Azure Service Fabric](../service-fabric/service-fabric-overview.md)

További információkért lásd: [tárolási réteg az általános célú csomagban](https://medium.com/azure-sqldb-managed-instance/file-layout-in-general-purpose-azure-sql-managed-instance-cf21fff9c76c) és [tárolási teljesítmény ajánlott eljárások és az Azure SQL DB felügyelt példányainak (általános célú) szempontjai](https://blogs.msdn.microsoft.com/sqlcat/2018/07/20/storage-performance-best-practices-and-considerations-for-azure-sql-db-managed-instance-general-purpose/).

További információ a szolgáltatási szintek közötti különbség [felügyelt példány erőforráskorlátok](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

### <a name="business-critical-service-tier-preview"></a>Üzleti kritikus fontosságú szolgáltatási szint (előzetes verzió)

Üzleti kritikus szolgáltatási szinten a nagy i/o-követelményekkel rendelkező alkalmazások épül. Több elkülönített replika használatával legmagasabb hibatűrést biztosít.

Az alábbi lista ismerteti azokat az üzletileg kritikus szolgáltatási rétegben főbb jellemzői:

- A lehető legjobb teljesítmény és a magas rendelkezésre ÁLLÁSÚ követelmények üzleti alkalmazásokhoz tervezve
- Együttműködik a villámgyors SSD-tárolás (legfeljebb 1 TB a Gen 4. és 4 TB-ra Gen 5)
- Beépített [magas rendelkezésre állású](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) alapján [Always On rendelkezésre állási csoportok](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) és [Azure Service Fabric](../service-fabric/service-fabric-overview.md).
- További beépített [csak olvasható adatbázis-replika](sql-database-read-scale-out.md) , amely a jelentéskészítés és más csak olvasási számítási feladatokhoz használható
- [Memóriabeli OLTP](sql-database-in-memory.md) , amely a nagy teljesítményű követelményekkel rendelkező számítási feladatokhoz használható  

> [!IMPORTANT]
> A **üzletileg kritikus fontosságú** szolgáltatási szint jelenleg előzetes verzióban.

További információ a szolgáltatási szintek közötti különbség [felügyelt példány erőforráskorlátok](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

## <a name="advanced-security-and-compliance"></a>Magas szintű biztonság és megfelelőség

Az Azure SQL Database felügyelt példányain egyesíti az Azure-felhőben és az SQL Server adatbázismotor által biztosított fejlett biztonsági funkciókat.

### <a name="managed-instance-security-isolation-in-azure-cloud"></a>Felügyelt példány biztonsági elkülönítés az Azure-felhőben

Felügyelt példány további biztonsági elkülönítés az Azure-felhőben található többi bérlő, adjon meg. Biztonsági elkülönítés tartalmazza:

- [Natív virtuálishálózat-implementáció](sql-database-managed-instance-vnet-configuration.md) és a kapcsolat a helyszíni környezetben Azure Express Route vagy VPN-átjárók használatával
- SQL-végpont csak egy magánhálózati IP-cím, lehetővé téve a biztonságos kapcsolat a privát Azure vagy hibrid hálózatok keresztül van közzétéve
- Egybérlős dedikált alapjául szolgáló infrastruktúrával (számítási, tárolási)

A következő ábra ismerteti az alkalmazások különféle kapcsolódási lehetőségei:

![magas rendelkezésre állás](./media/sql-database-managed-instance/application-deployment-topologies.png)  

VNet-integráció és hálózatkezelési szabályzatok érvénybe léptetéséhez az alhálózat szintjén kapcsolatos további részletekért, lásd: [virtuális hálózat konfigurálása az Azure SQL Database felügyelt példányába](sql-database-managed-instance-vnet-configuration.md) és [az Azure SQL Database-alkalmazás csatlakoztatása Felügyelt példány](sql-database-managed-instance-connect-app.md).

> [!IMPORTANT]
> Helyezze el a több felügyelt példány ugyanazon az alhálózaton, bárhol is, amely által engedélyezett a biztonsági követelmények szerint, amely tartalomtérkép érhető el, további előnyöket és juttatásokat. Helymegosztást példányok ugyanazon az alhálózaton jelentősen hálózati infrastruktúra-karbantartás leegyszerűsítésében, és csökkentheti a kiépítés ideje, mivel hosszú kiépítés időtartama társítva az első üzembe helyezése felügyelt példány alhálózatán költsége példány.

### <a name="azure-sql-database-security-features"></a>Az Azure SQL Database biztonsági funkciók

Az Azure SQL Database biztosít, amelyek segítségével az adatok védelme a speciális biztonsági funkciók.

- [Felügyelt példány naplózás](sql-database-managed-instance-auditing.md) nyomon követi az adatbázisok eseményeit, és a egy naplófájl helyezi el az Azure storage-fiók írja őket. Naplózás segíthet a jogszabályi, adatbázis-tevékenység megértésében, valamint betekintést nyerhet az eltéréseket és rendellenességeket, amelyek üzleti aggályokra vagy biztonsági szabálysértések.
- Adattitkosítás menet közben – felügyelt példány használatával a Transport Layer Security mozgásban lévő adatok titkosítását biztosításával védi az adatokat. Mellett TLS, SQL Database felügyelt példányain kínál a bizalmas adatok útban, inaktív és a Lekérdezésfeldolgozás közben [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Az Always Encrypted az iparágban elsőként kínál páratlan adatbiztonságot a bizalmas adatok eltulajdonításával járó illetéktelen behatolások ellen. Például az Always Encrypted, hitelkártyaszámokat tartalmazó tárolódnak az adatbázisban titkosítottan mindig, még akkor is lekérdezés feldolgozása, lehetővé téve a visszafejtési hitelkártyaszámai jogosult alkalmazottak vagy az alkalmazások számára, hogy az adatok feldolgozása során.
- [Veszélyforrások Detektálása](sql-database-managed-instance-threat-detection.md) egészíti ki a [felügyelt példány naplózás](sql-database-managed-instance-auditing.md) azáltal, hogy egy további biztonsági réteget a szolgáltatás által észlelt szokatlan és vélhetően kárt okozó beépített intelligencia próbál elérni vagy kiaknázni adatbázisok. Gyanús tevékenységek, a lehetséges biztonsági résekről, figyelmeztetést, és az SQL-injektálási támadások, valamint a rendellenes adatbázis-hozzáférési mintákról. Fenyegetésészlelési riasztások tekinthetők [az Azure Security Center](https://azure.microsoft.com/services/security-center/) , és adja meg a gyanús tevékenység részleteit, és tegyen javaslatot a műveletre vizsgálata, valamint a fenyegetés.  
- [Dinamikus adatmaszkolás](/sql/relational-databases/security/dynamic-data-masking) korlátozza a bizalmas adatok közzétételét, hogy a nem kiemelt jogosultságú felhasználók maszkolja által. Dinamikus adatmaszkolás segít a bizalmas adatokhoz való illetéktelen hozzáférés megakadályozása által meghatározhatóvá a bizalmas adatok felfedéséhez az alkalmazásrétegre gyakorolt minimális hatás mellett. Ez a szabályzatalapú biztonsági funkció elrejti a bizalmas adatokat egy kijelölt adatbázismezőkön végrehajtott lekérdezés eredményhalmazában, miközben az adatbázis adatait nem módosítja.
- [Sorszintű biztonság](/sql/relational-databases/security/row-level-security) lehetővé teszi, hogy a vezérlőelem egy adatbázistábla soraihoz való eléréséhez a lekérdezést végrehajtó felhasználó jellemzői alapján (például csoporttagság vagy végrehajtási környezet szerint). A sorszintű biztonság (RLS) egyszerűsíti az alkalmazás védelmének megtervezését és kódolását. Az RLS használatával korlátozásokat érvényesíthet az adatsorokhoz való hozzáférésre. Például annak érdekében, hogy a dolgozók férhessenek hozzá csak azok a szervezeti egységükre vonatkozó az adatsorokat, vagy egy adat-hozzáférés korlátozása csak a releváns adatokat.
- [Transzparens adattitkosítás (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) titkosítja az Azure SQL Database felügyelt példányába adatfájlok, más néven az inaktív adatok titkosítása. TDE valós idejű i/o-titkosításához és visszafejtéséhez az adathoz és naplófájlhoz hajt végre. A titkosítást használ egy adatbázis-titkosítási kulcs (adattitkosítási kulcsot), a helyreállítás során a rendelkezésre állási adatbázis rendszerindító rekordját tárolt. Összes adatbázis a felügyelt példány transzparens adattitkosítással védheti meg. A TDE az SQL kipróbált inaktív-adattitkosítási technológiája, amelyet számos megfelelőségi szabvány előír az adattárolók eltulajdonítása esetén hatékony védelemként. A nyilvános előzetes verzióban az Automatikus kulcskezelés modell támogatott (a PaaS-platform által végrehajtott).

Egy titkosított, az SQL Database áttelepítése támogatott, az Azure Database Migration Service (DMS) vagy a natív visszaállítási keresztül. Ha titkosított adatbázis natív visszaállítással való áttelepítését tervezi, a migrálás a helyszíni SQL Server vagy SQL Server rendszerű virtuális gép a felügyelt példánynak meglévő TDE tanúsítvány, lépésre szükség. Áttelepítési beállítások kapcsolatos további információkért lásd: [SQL Server-példány migrálása az Azure SQL Database felügyelt példányába történő](sql-database-managed-instance-migrate.md).

## <a name="azure-active-directory-integration"></a>Azure Active Directory-integráció

Az Azure SQL Database felügyelt példánya támogatja a hagyományos SQL server Database engine bejelentkezések és az Azure Active Directory (AAD) integrált bejelentkezések. AAD-bejelentkezések az Azure-felhőalapú változata, a helyszíni környezetben használt Windows-bejelentkezések.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory-integráció és többtényezős hitelesítés

Felügyelt példány pedig lehetővé teszi, hogy az adatbázis-felhasználók és a más Microsoft-szolgáltatások identitásainak központi kezelését [Azure Active Directory-integráció](sql-database-aad-authentication.md). Ez a funkció egyszerűsíti az engedélyek kezelését és fokozza a biztonságot. Az Azure Active Directory a [többtényezős hitelesítés](sql-database-ssms-mfa-authentication-configure.md) (MFA) támogatásával javítja az adatok és alkalmazások biztonságát, miközben támogatja az egyszeri bejelentkezést.

### <a name="authentication"></a>Hitelesítés

A felügyelt példány hitelesítés azt jelenti, hogy felhasználók személyazonossága az adatbázishoz való csatlakozáskor. Az SQL Database két hitelesítési típust támogat:  

- **SQL-hitelesítés**:

  Ezt a hitelesítési módszert használ, egy felhasználónevet és jelszót.
- **Az Azure Active Directory-hitelesítés**:

  Ez a hitelesítési módszer az Azure Active Directory által felügyelt identitásokat használ, és a felügyelt és integrált tartományok támogatott. [Amikor csak lehet](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode), használja az Active Directory-hitelesítést (beépített biztonság).

### <a name="authorization"></a>Engedélyezés

Engedélyezési milyen egy felhasználó teheti meg az Azure SQL Database, és a felhasználói fiókjának adatbázis szerepkörtagságai és objektumszintű engedélyei vezérli hivatkozik. Felügyelt példány van ugyanazon engedélyezési lehetőségeket, mint az SQL Server 2017-ben.

## <a name="database-migration"></a>Adatbázis-áttelepítés

A felügyelt példány céljainak felhasználói esetek tömeges adatbázis áttelepítése az a helyszíni vagy IaaS adatbázis megvalósításokhoz. Felügyelt példány támogatja több adatbázis-migrálási beállítások:

### <a name="backup-and-restore"></a>Biztonsági mentés és visszaállítás  

A migrálási megközelítés használ az SQL biztonsági másolatokat az Azure blob storage. Az Azure storage-blobban tárolt biztonsági másolatok közvetlenül állíthatók be a felügyelt példány használatával az [T-SQL RESTORE parancsot](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=azuresqldb-mi-current).

- A rövid útmutató, amely visszaállítása a Wide World Importers – Standard adatbázis biztonsági másolatát, lásd: [visszaállítása biztonsági másolatból egy felügyelt példányon](sql-database-managed-instance-get-started-restore.md). A rövid útmutató bemutatja, hogy a biztonsági mentési fájl feltöltése az Azure-blog storage és a biztonságos, egy közös hozzáférési jogosultságkód (SAS) kulccsal rendelkezik.
- URL-címről visszaállítási kapcsolatos információkért lásd: [natív VISSZAÁLLÍTÁSA URL-címről](sql-database-managed-instance-migrate.md#native-restore-from-url).

> [!IMPORTANT]
> Felügyelt példány biztonsági mentések csak állítható vissza egy másik, a felügyelt példányhoz. Ezek nem állítható vissza egy helyszíni SQL Server vagy egy Azure SQL Database logikai kiszolgálón egyetlen vagy készletezett adatbázis.

### <a name="data-migration-service"></a>Data Migration Service

Az Azure Database Migration Service egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a zökkenőmentes migrálást a több adatbázis-forrásokhoz, az Azure-adatplatformokra minimális állásidővel. Ez a szolgáltatás leegyszerűsíti a meglévő harmadik féltől származó és az SQL Server-adatbázisok áthelyezése az Azure-bA szükséges feladatok. Üzembe helyezési lehetőségek az Azure SQL Database felügyelt példány és az SQL Server Azure-beli virtuális gépen a nyilvános előzetes verzióban. Lásd: [a helyszíni adatbázis Migrálása a felügyelt példány DMS használatával](https://aka.ms/migratetoMIusingDMS).

## <a name="sql-features-supported"></a>Támogatott SQL-funkciók

A felügyelt példány célja, hogy közel 100 %-os támadási kompatibilitást a helyszíni SQL Server szolgáltatás általános rendelkezésre állása amíg fázisában hamarosan. Az a funkciók és összehasonlító listában, lásd: [SQL Database funkcióinak összehasonlítása](sql-database-features.md), és a egy T-SQL eltérései az SQL Server és a felügyelt példányok listáját lásd: [felügyelt példány T-SQL eltérései az SQL Serverből](sql-database-managed-instance-transact-sql-information.md).

A felügyelt példány támogatja az előző verziókkal való kompatibilitás SQL 2008 adatbázis. Az SQL 2005 adatbázis-kiszolgálók közvetlen áttelepítése támogatott, az SQL 2005 migrált adatbázisok kompatibilitási szintje frissítve lett, hogy az SQL 2008.
  
A következő ábra a felügyelt példány támadási kompatibilitási ismerteti:  

![Áttelepítés](./media/sql-database-managed-instance/migration.png)

### <a name="key-differences-between-sql-server-on-premises-and-managed-instance"></a>A helyszíni SQL Server és a felügyelt példány közötti lényeges különbségeket

Felügyelt példány előnyei nem mindig felfelé-elejétől számított a felhőben, ami azt jelenti, hogy egyes funkciók a helyszíni SQL Server lehet akár elavult, elavult, vagy rendelkezik alternatívákat. Ha ismeri fel, hogy egy adott funkció kissé eltérő módon működik-e, vagy az, hogy a szolgáltatás nem fut egy környezetben, akkor nem teljes mértékben szabályozzák, eszközöket kell, vannak bizonyos esetekben:

- Magas rendelkezésre állású jön létre, és előre konfigurálva van a hasonló technológiával [Always On rendelkezésre állási csoportok](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server).
- Automatikus biztonsági mentések és időponthoz kötött visszaállítás pont. Kezdeményezheti az ügyfél `copy-only` biztonsági mentések, amelyek nem zavarják a automatikus biztonsági mentési láncolatát.
- Felügyelt példány nem engedélyezi a teljes fizikai elérési út megadásával, így az összes megfelelő forgatókönyv kell másképp támogatja: adatbázis-VISSZAÁLLÍTÁS nem támogatja a WITH MOVE, DB létrehozása nem teszi lehetővé a fizikai elérési útját, TÖMEGES Beszúrás működik az Azure Blobszolgáltatása révén, csak stb.
- Felügyelt példány által támogatott [Azure AD-hitelesítés](sql-database-aad-authentication.md) felhőalapú alternatív Windows-hitelesítés.
- Felügyelt példány automatikusan kezeli a XTP fájlcsoport és fájlok In-Memory OLTP objektumokat tartalmazó adatbázisok
- Felügyelt példány támogatja-e az SQL Server Integration Services (SSIS) és a gazdagép SSIS-katalógus (SSISDB), amely tárolja az SSIS-csomagokat is, de ezeket a rendszer végrehajtja a egy felügyelt Azure-SSIS integrációs modul (IR) az Azure Data Factory (ADF), lásd: [létrehozása Az Azure-SSIS integrációs modul az ADF-ben](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). Hasonlítsa össze az SSIS-funkciók az SQL Database felügyelt példány, lásd: [hasonlítsa össze az SQL Database logikai kiszolgáló és a felügyelt példány](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-logical-server-and-sql-database-managed-instance).

### <a name="managed-instance-administration-features"></a>Felügyelt példány felügyeleti funkciók

A felügyelt példány engedélyezése rendszergazdák számára, hogy vállalati a legfontosabb dolgokra koncentrálni. Sok rendszergazda/DBA rendszertevékenységek nem szükségesek, vagy egyszerű. Ha például az operációs rendszer / relációsadatbázis-kezelő rendszer telepítési és javítása, a dinamikus példány, átméretezése és a konfiguráció, a biztonsági mentések [adatbázis-replikáció](replication-with-sql-database-managed-instance.md) (beleértve a rendszeradatbázisok), magas rendelkezésre állású, és rendszerállapot-konfigurációt és alkalmazásteljesítmény-figyelés adatfolyamokat.

> [!IMPORTANT]
> Támogatott, részlegesen támogatott és nem támogatott funkciók listáját lásd: [SQL Database funkciói](sql-database-features.md). Felügyelt példány az SQL Server és a T-SQL különbségek listája: [felügyelt példány T-SQL eltérései az SQL Serverről](sql-database-managed-instance-transact-sql-information.md)

### <a name="how-to-programmatically-identify-a-managed-instance"></a>Programozott módon a felügyelt példány azonosítása

Az alábbi táblázat Transact SQL-n keresztül elérhető számos tulajdonság, hogy észleli, hogy működik-e az alkalmazás felügyelt példány használatával és fontos tulajdonságok beolvasása.

|Tulajdonság|Érték|Megjegyzés|
|---|---|---|
|`@@VERSION`|A Microsoft SQL Azure (RTM) – 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation.|Ez ugyanaz, mint az SQL Database értéke.|
|`SERVERPROPERTY ('Edition')`|SQL Azure|Ez ugyanaz, mint az SQL Database értéke.|
|`SERVERPROPERTY('EngineEdition')`|8|Ez az érték a felügyelt példány egyedileg azonosítja.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|Példány teljes DNS-név a következő formátumban:`<instanceName>`.`<dnsPrefix>`.Database.Windows.NET, ahol `<instanceName>` az ügyfél által megadott név közben `<dnsPrefix>` a neve, ami garantálja az globális DNS neve egyedi-e automatikusan létrehozott része ("wcus17662feb9ce98", például)|Példa: saját-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan hozhat létre az első felügyelt példányhoz, lásd: [gyors üzembe helyezési útmutató](sql-database-managed-instance-get-started.md).
- Az a funkciók és összehasonlító listában, lásd: [általános SQL-szolgáltatások](sql-database-features.md).
- A VNetek konfigurálásával kapcsolatos további információkért tekintse meg a [felügyelt példányok VNetjének konfigurálásával kapcsolatos](sql-database-managed-instance-vnet-configuration.md) cikket.
- Ha a rövid útmutató, amely létrehoz egy felügyelt példányt, és visszaállít egy adatbázist egy biztonsági mentési fájlból, lásd: [létrehoz egy felügyelt példányt](sql-database-managed-instance-get-started.md).
- Az Azure Database Migration Service migráláshoz való használatát a [felügyelt példány DMS használatával történő migrálását](../dms/tutorial-sql-server-to-managed-instance.md) bemutató oktatóanyag ismerteti.
- Díjszabási információkért tekintse meg a [SQL Database felügyelt példányain díjszabás](https://azure.microsoft.com/pricing/details/sql-database/managed/).
