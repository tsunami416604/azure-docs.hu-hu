---
title: Azure Security and Compliance Blueprint – adattárház az Egyesült királyságbeli NHS számára
description: Azure Security and Compliance Blueprint – adattárház az Egyesült királyságbeli NHS számára
services: security
author: jomolesk
ms.assetid: f4e4b939-88db-4d78-8fa9-c2a12b2c025b
ms.service: security
ms.topic: article
ms.date: 06/15/2018
ms.author: jomolesk
ms.openlocfilehash: 3519b3f2c13349c3764e95bcc6898eeb73a79a81
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946626"
---
# <a name="azure-security-and-compliance-blueprint-data-warehouse-for-uk-nhs"></a>Azure Security and Compliance Blueprint: Adattárház az Egyesült királyságbeli NHS számára

## <a name="overview"></a>Áttekintés

Ez a Azure Security and Compliance Blueprint egy olyan adattárház-megoldásra vonatkozó hivatkozási architektúrát és útmutatást biztosít, amely alkalmas a bizalmas egészségügyi adatok biztonságos betöltésére, előkészítésére, tárolására és interakcióra. Ez a megoldás azt mutatja be, hogy az ügyfelek milyen módon tudnak megfelelni az [NHS Digital](https://digital.nhs.uk/)által közzétett, a [Cloud Security bevált gyakorlatának útmutatójában](https://digital.nhs.uk/data-and-information/looking-after-information/data-security-and-information-governance/nhs-and-social-care-data-off-shoring-and-the-use-of-public-cloud-services/health-and-social-care-cloud-security-good-practice-guide) ismertetett útmutatásoknak, amely az Egyesült Királyság (Egyesült Királyság) egészségügyi Minisztériumának és szociális ellátásának (DHSC) partnere. A Cloud Security helyes gyakorlatának útmutatója az Egyesült Királyság nemzeti Cyber Security Center (NCSC) által közzétett 14 [Felhőbeli biztonsági](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) alapelveken alapul.

Ez a hivatkozási architektúra, a megvalósítási útmutató és a veszélyforrás-modell arra szolgál, hogy az ügyfelek számára alapvető fontosságúak legyenek az adott követelményekhez való alkalmazkodáshoz, és nem használhatók éles környezetben, további konfiguráció nélkül. Az ügyfelek felelősek az architektúrával létrehozott megoldások megfelelő biztonsági és megfelelőségi felmérésének elvégzéséért, mivel a követelmények az egyes ügyfelek implementációjának sajátosságai alapján változhatnak.

## <a name="architecture-diagram-and-components"></a>Architektúra ábrája és összetevői

Ez a megoldás egy olyan hivatkozási architektúrát biztosít, amely nagy teljesítményű és biztonságos felhőalapú adattárházat valósít meg. Ebben az architektúrában két különálló adatréteg található: az egyik, hogy az adatok importálása, tárolása és előkészítése fürtözött SQL-környezetben történik, és egy másik a Azure SQL Data Warehouse, ahol az adatok betöltésére egy kinyerési, átalakítási és betöltési eszköz (például a nyersanyag) használatával kerül be. [ ](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase)T-SQL-lekérdezések) feldolgozásra. Ha az adattárolást Azure SQL Data Warehouse tárolja, az elemzések nagy léptékben futhatnak.

Az Azure számos jelentéskészítési és elemzési szolgáltatást kínál az ügyfelek számára. Ez a megoldás SQL Server Reporting Servicest tartalmaz a jelentések gyors létrehozásához a Azure SQL Data Warehouse. Az összes SQL-forgalom SSL-titkosítással rendelkezik az önaláírt tanúsítványok belefoglalásával. Az ajánlott eljárás az, hogy az Azure megbízható hitelesítésszolgáltató használatát javasolja a fokozott biztonság érdekében.

A Azure SQL Data Warehouseban tárolt adat az oszlopos tárolással rendelkező, rokon táblákban tárolódik, amely jelentősen csökkenti az adattárolási költségeket, miközben javítja a lekérdezési teljesítményt.  A használati követelményektől függően Azure SQL Data Warehouse számítási erőforrások vertikálisan fel-vagy leállíthatók, vagy teljesen kikapcsolhatók, ha nincsenek olyan aktív folyamatok, amelyek számítási erőforrásokat igényelnek.

Az SQL Load Balancer az SQL-forgalmat felügyeli, és nagy teljesítményt biztosít. Az ebben a hivatkozási architektúrában található összes virtuális gép egy rendelkezésre állási csoportba helyezi az SQL Server példányokat egy always on rendelkezésre állási csoportban a magas rendelkezésre állás és a vész-helyreállítási képességek érdekében.

A fokozott biztonság érdekében az ebben a megoldásban lévő összes erőforrást erőforráscsoportként kezeli Azure Resource Manageron keresztül. Azure Active Directory szerepköralapú hozzáférés-vezérléssel szabályozható a telepített erőforrásokhoz és kulcsokhoz való hozzáférés a Azure Key Vaultban. A rendszer állapotát Azure Security Center és Azure Monitor figyeli. Az ügyfelek mindkét figyelési szolgáltatást a naplók rögzítéséhez és a rendszerállapot megjelenítéséhez egyetlen, könnyen navigálható irányítópulton.


Ez az adattárház-hivatkozási architektúra az architektúrán belüli erőforrások kezelésére szolgáló Active Directory szintet is tartalmaz. Az Active Directory alhálózat lehetővé teszi, hogy a rendszer a nagyobb Active Directory erdő struktúrája alatt könnyen befogadja a környezetet, ami lehetővé teszi a környezet folyamatos működését akkor is, ha a nagyobb erdőhöz való hozzáférés nem érhető el. Az összes virtuális gép tartományhoz van csatlakoztatva a Active Directory réteghez, és Active Directory Csoportházirendek használatával kényszeríti ki a biztonsági és megfelelőségi konfigurációkat az operációs rendszer szintjén.

A virtuális gépek felügyeleti megerősített gazdagépként szolgálnak, és biztonságos kapcsolatot biztosítanak a rendszergazdák számára az üzembe helyezett erőforrások eléréséhez. Az adattárolási terület a felügyeleti megerősített gazdagépen keresztül töltődik be. **A Microsoft javasolja a VPN-vagy Azure ExpressRoute-kapcsolat konfigurálását a felügyeleti és adatimportálási szolgáltatásba a hivatkozási architektúra alhálózatában.**

![Adattárház az Egyesült királyságbeli NHS Reference Architecture diagramhoz](images/uknhs-datawarehouse-architecture.png?raw=true "Adattárház az Egyesült királyságbeli NHS Reference Architecture diagramhoz")

Ez a megoldás az alábbi Azure-szolgáltatásokat használja. Az üzembe helyezési architektúra részletei az [üzembe helyezési architektúra](#deployment-architecture) szakaszban találhatók.

- Rendelkezésre állási csoportok
    - Active Directory tartományvezérlők
    - SQL-fürt csomópontjai és tanúsító
- Azure Active Directory
- Azure Data Catalog
- Azure Key Vault
- Azure Monitor
- Azure Security Center
- Azure Load Balancer
- Azure Storage
- Azure Automation
- Azure Virtual Machines
    - (1) megerősített állomás
    - (2) Active Directory tartományvezérlő
    - (2) SQL Server fürtcsomópont
    - (1) SQL Server tanúsító
- Azure Virtual Network
    - (1)/16 hálózat
    - (4)/24 hálózat
    - (4) hálózati biztonsági csoportok
- Recovery Services-tároló
- SQL Data Warehouse
- SQL Server Reporting Services

## <a name="deployment-architecture"></a>Üzembe helyezési architektúra

A következő szakasz az üzembe helyezési és megvalósítási elemeket részletezi.

**SQL Data Warehouse**: A [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) egy nagyvállalati adattárház, amely nagymértékben párhuzamos feldolgozást használ, hogy gyorsan futtasson összetett lekérdezéseket az adatpetabájton keresztül, így a felhasználók hatékonyan azonosíthatják az egészségügyi adatmennyiséget. A felhasználók használhatnak egyszerű, alapszintű T-SQL-lekérdezéseket big data importálására a SQL Data Warehouseba, és a nagymértékben párhuzamos feldolgozás hatékonyságát a nagy teljesítményű elemzések futtatására használják.

**SQL Server Reporting Services**: [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) lehetővé teszi a jelentések gyors létrehozását táblázatok, diagramok, térképek, mérőműszerek, mátrixok és más Azure SQL Data Warehouseekhez.

**Data Catalog**: [Data Catalog](../../data-catalog/overview.md) az adatforrásokat könnyen felderíthetővé és érthetővé teszi az adatkezelést végző felhasználók számára. Az általános adatforrások az állapottal kapcsolatos adat regisztrálása, címkézése és keresése lehet. Az adatok a meglévő helyükön maradnak, de a metaadatok egy példánya hozzá lett adva a Data Cataloghoz, valamint az adatforrás helyére mutató hivatkozás. A metaadatok indexelésének köszönhetően az adatforrások egy egyszerű keresés által felfedezhetővé és könnyen értelmezhetővé válnak a felhasználók számára.

**Megerősített gazdagép**: A megerősített gazdagép az egyetlen belépési pont, amely lehetővé teszi, hogy a felhasználók hozzáférhessenek az üzembe helyezett erőforrásokhoz ebben a környezetben. A megerősített gazdagép biztonságos kapcsolódást biztosít a központilag telepített erőforrásokhoz azáltal, hogy csak a nyilvános IP-címekről érkező távoli forgalmat engedélyezi biztonságos listán. A távoli asztal (RDP) forgalmának engedélyezéséhez a hálózati biztonsági csoportban meg kell határozni a forgalom forrását.

Ez a megoldás egy virtuális gépet hoz létre tartományhoz csatlakozó megerősített gazdagépként a következő konfigurációkkal:
-   [Antimalware-bővítmény](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
-   [Azure Diagnostics bővítmény](../../virtual-machines/windows/extensions-diagnostics-template.md)
-   [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) a Azure Key Vault használatával
-   [Automatikus leállítási szabályzat](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) , amely csökkenti a virtuális gépek erőforrásainak felhasználását, ha nincs használatban
-   A [Windows Defender hitelesítőadat](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) -védelme engedélyezve van, hogy a hitelesítő adatok és egyéb Titkok a futó operációs rendszertől elkülönített védett környezetekben fussanak.

### <a name="virtual-network"></a>Virtuális hálózat

Ez a hivatkozási architektúra a 10.0.0.0/16 címtartomány szerinti magánhálózati virtuális hálózatot definiálja.

**Hálózati biztonsági csoportok**: A [hálózati biztonsági csoportok](../../virtual-network/virtual-network-vnet-plan-design-arm.md) olyan hozzáférés-vezérlési listát tartalmaznak, amelyek engedélyezik vagy megtagadják a forgalmat a virtuális hálózaton belül. A hálózati biztonsági csoportok segítségével biztonságossá teheti a forgalmat alhálózat vagy egyedi virtuálisgép-szinten. A következő hálózati biztonsági csoportok léteznek:

  - Hálózati biztonsági csoport az adatcsomaghoz (SQL Server fürtök, SQL Server tanúsító és SQL Load Balancer)
  - Hálózati biztonsági csoport a felügyeleti megerősített gazdagéphez
  - Hálózati biztonsági csoport Active Directory
  - Hálózati biztonsági csoport SQL Server Reporting Services

A hálózati biztonsági csoportok mindegyike megnyitotta a megadott portokat és protokollokat, hogy a megoldás biztonságosan és megfelelően működjön. Emellett a következő konfigurációk mindegyik hálózati biztonsági csoport esetében engedélyezve vannak:

- A [diagnosztikai naplók és események](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) engedélyezve vannak, és Storage-fiókban tárolódnak
- Azure Monitor naplók a [hálózati biztonsági csoport&#39;s diagnosztikai naplóihoz](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json) vannak csatlakoztatva

Alhálózatok: Minden alhálózat társítva van a hozzá tartozó hálózati biztonsági csoporttal.

### <a name="data-at-rest"></a>Inaktív adat

Az architektúra több mértéken keresztül védi az inaktív adatok védelmét, beleértve a titkosítást és az adatbázis-naplózást is.

Az architektúra titkosítva, adatbázis-naplózással és egyéb mértékekkel védi a nyugalmi állapotban lévő adatok védelmét.

**Azure Storage**: A titkosított adatoknak a nyugalmi követelmények teljesítése érdekében az összes [Azure Storage](https://azure.microsoft.com/services/storage/) [Storage Service encryption](../../storage/common/storage-service-encryption.md)használ. Ez segíti az adatvédelmet és védelmét az NHS Digital által meghatározott szervezeti biztonsági kötelezettségvállalások és megfelelőségi követelmények támogatásával.

**Azure Disk Encryption**: [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) kihasználja a Windows BitLocker szolgáltatását, hogy mennyiségi titkosítást biztosítson az adatlemezek számára. A megoldás integrálva van Azure Key Vaultekkel a lemezes titkosítási kulcsok szabályozása és kezelése érdekében.

**Azure SQL Database**: A Azure SQL Database példány a következő adatbázis-biztonsági mértékeket használja:

- [Active Directory a hitelesítés és az engedélyezés](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) lehetővé teszi az adatbázis-felhasználók és más Microsoft-szolgáltatások Identitáskezelés kezelését egy központi helyen.
- Az [SQL Database naplózása](../../sql-database/sql-database-auditing.md) nyomon követi az adatbázis eseményeit, és egy Azure Storage-fiókban lévő naplóba írja azokat.
- Azure SQL Database úgy van konfigurálva, hogy [transzparens](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)adattitkosítást használjon, amely valós idejű titkosítást és visszafejtést végez az adatbázis, a társított biztonsági másolatok és a tranzakciós naplófájlok számára, hogy megvédje az adatokat a nyugalmi állapotban. Az transzparens adattitkosítás biztosítja, hogy a tárolt adataik nem érvényesek a jogosulatlan hozzáférésre.
- A [Tűzfalszabályok](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) megakadályozzák az adatbázis-kiszolgálók hozzáférését a megfelelő engedélyek megadása előtt. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján.
- Az [SQL](../../sql-database/sql-database-threat-detection.md) -veszélyforrások észlelése lehetővé teszi az észlelést és a reagálást a potenciális fenyegetésekre, mivel ezek a hibák a gyanús adatbázis-tevékenységek, a potenciális sebezhetőségek, az SQL-injektálási támadások és a rendellenes adatbázis-hozzáférési minták esetében
- A [titkosított oszlopok](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) biztosítják, hogy a bizalmas adatok soha ne jelenjenek meg az adatbázis-rendszeren belüli egyszerű szövegként. Az adattitkosítás engedélyezése után csak az ügyfélalkalmazások vagy az alkalmazások férhetnek hozzá a kulcsokhoz.
- [SQL Database a dinamikus adatmaszkolás](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) korlátozza a bizalmas adatok megjelenítését azáltal, hogy az adatokat nem Kiemelt felhasználók vagy alkalmazások számára fedi le. A dinamikus adatmaszkolás automatikusan képes észlelni a potenciálisan bizalmas adatokat, és javaslatot tesz a megfelelő maszkok alkalmazására. Ez segít az adathozzáférés azonosításában és csökkentésében, hogy ne lépjen ki az adatbázisból jogosulatlan hozzáférés útján. Az ügyfelek feladata a dinamikus adatmaszkolási beállítások módosítása az adatbázis-sémájuk betartásához.

### <a name="identity-management"></a>Identitáskezelés

Az alábbi technológiák az Azure-környezetben tárolt adathozzáférések kezelésére szolgáló képességeket biztosítanak:

- [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) a Microsoft&#39;s több-bérlős felhőalapú címtár-és Identitáskezelés-kezelő szolgáltatás. A megoldás összes felhasználója Azure Active Directoryban jön létre, beleértve a Azure SQL Databasehoz hozzáférő felhasználókat is.
- Az alkalmazáshoz való hitelesítés Azure Active Directory használatával történik. További információ: [alkalmazások integrálása a Azure Active Directorysal](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Emellett az adatbázis oszlopának titkosítása a Azure Active Directory használatával hitelesíti az alkalmazást a Azure SQL Database. További információ: a [bizalmas adatok védelme Azure SQL Databaseban](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- Az [Azure szerepköralapú hozzáférés-vezérlés](../../role-based-access-control/role-assignments-portal.md) lehetővé teszi a rendszergazdáknak, hogy részletes hozzáférési engedélyeket határozzanak meg, amelyek csak a felhasználóknak a feladataik elvégzéséhez szükséges hozzáférést biztosítják. Ahelyett, hogy minden felhasználó számára korlátlan engedélyt adna az Azure-erőforrásokhoz, a rendszergazdák csak bizonyos műveleteket végezhetnek el az adatokhoz való hozzáféréshez. Az előfizetés-hozzáférés az előfizetés rendszergazdájára korlátozódik.
- [Azure Active Directory Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) lehetővé teszi, hogy az ügyfelek csökkentsék azon felhasználók számát, akik hozzáféréssel rendelkeznek bizonyos adatokhoz. A rendszergazdák Azure Active Directory Privileged Identity Management használhatják az emelt szintű identitások felderítését, korlátozását és figyelését, valamint az erőforrásokhoz való hozzáférésüket. Ez a funkció az igény szerinti, igény szerinti rendszergazdai hozzáférés biztosítására is használható, ha szükséges.
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) észleli a szervezet&#39;s identitásait érintő lehetséges biztonsági réseket, automatikus válaszokat konfigurál a szervezet&#39;s identitásával kapcsolatos gyanús műveletekre, és megvizsgálja a gyanús incidenseket, hogy tegye meg a megfelelő lépéseket a megoldásához.

### <a name="security"></a>Biztonság

**Titkok kezelése**: A megoldás a kulcsok és titkok kezeléséhez [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) használ. Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. Az alábbi Azure Key Vault-funkciók segítenek az ügyfeleknek az ilyen jellegű adatvédelemben és hozzáférésben:

- A speciális hozzáférési szabályzatok a szükséges módon vannak konfigurálva.
- Key Vault hozzáférési házirendek minimálisan szükséges engedélyekkel vannak definiálva a kulcsokhoz és a titkokhoz.
- Key Vault összes kulcsának és titkának lejárati dátuma van.
- A Key Vault összes kulcsát speciális hardveres biztonsági modulok védik. A kulcs típusa egy 2048 bites RSA-kulcsot tartalmazó hardveres biztonsági modul.
- Minden felhasználó és identitás a szerepköralapú hozzáférés-vezérlés használatával minimálisan szükséges engedélyeket kap.
- Key Vault diagnosztikai naplói legalább 365 napos megőrzési időtartammal engedélyezettek.
- A kulcsok számára engedélyezett titkosítási műveletek csak a szükségesek.

**Javítási felügyelet**: Az ebben a hivatkozási architektúrában üzembe helyezett Windows-beli virtuális gépek alapértelmezés szerint úgy vannak konfigurálva, hogy Windows Update szolgáltatásból fogadják az automatikus frissítéseket. Ez a megoldás tartalmazza azt a [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) szolgáltatást is, amelyen keresztül a frissített központi telepítések létrehozhatók a virtuális gépek szükség szerinti javításához.

**Kártevők elleni védelem**: A [Microsoft antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware) virtuális gépek valós idejű védelmet biztosítanak a vírusok, kémprogramok és más kártékony szoftverek azonosításához és eltávolításához, amelyekkel konfigurálható riasztások állíthatók be, ha az ismert kártékony vagy nemkívánatos szoftverek megkísérlik Telepítsen vagy futtasson védett virtuális gépeken.

**Azure Security Center**: A [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)segítségével az ügyfelek központilag alkalmazhatják és kezelhetik a munkaterhelések biztonsági szabályzatait, korlátozhatja a fenyegetéseket, és észlelheti és reagálhat a támadásokra. Azure Security Center az Azure-szolgáltatások meglévő konfigurációit is elérheti, hogy konfigurációs és szolgáltatási javaslatokat nyújtson a biztonsági helyzetek és az adatvédelem javításához.

Azure Security Center különböző észlelési képességekkel figyelmezteti az ügyfeleket a környezetekhez kapcsolódó lehetséges támadásokra. Ezek a riasztások értékes információkat tartalmaznak arról, hogy mi váltotta ki a riasztást, valamint a támadás forrásáról és az általa célba vett erőforrásokról. A Azure Security Center [előre meghatározott biztonsági riasztásokkal](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)rendelkezik, amelyek egy fenyegetés vagy gyanús tevékenység bekövetkeztekor aktiválódnak. A Azure Security Center [Egyéni riasztási szabályai](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) lehetővé teszik, hogy az ügyfelek új biztonsági riasztásokat határozzanak meg a környezetből már összegyűjtött adatok alapján.

A Azure Security Center rangsorolt biztonsági riasztásokat és incidenseket biztosít, így egyszerűbbé válik az ügyfelek számára a potenciális biztonsági problémák felderítése és kezelése. Az egyes észlelt fenyegetésekkel kapcsolatos [fenyegetési intelligenciáról szóló jelentés](https://docs.microsoft.com/azure/security-center/security-center-threat-report) jön létre, amely segít az incidensek megválaszolásában a fenyegetések kivizsgálásában és szervizelését.

Ez a hivatkozási architektúra emellett a sebezhetőségi [felmérést](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) is felhasználja Azure Security Centerban. A konfigurálást követően a partner ügynök (például a Qualys) biztonsági réseket küld a partner felügyeleti platformjának. A partner felügyeleti platformja pedig visszaigazolja a biztonsági réseket és az állapot-figyelési adatvédelmet Azure Security Center, így az ügyfelek gyorsan azonosíthatják a sebezhető virtuális gépeket.

### <a name="business-continuity"></a>Az üzletmenet folytonossága

**Magas rendelkezésre állás**: A kiszolgálói munkaterhelések egy [rendelkezésre állási](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) csoportba vannak csoportosítva, így biztosítva az Azure-beli virtuális gépek magas rendelkezésre állását. A tervezett vagy nem tervezett karbantartási események során legalább egy virtuális gép elérhető a 99,95%-os Azure SLA-val.

**Recovery Services**tároló: Az [Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) -tároló a biztonsági mentési és az Azure-beli virtuális gépek összes konfigurációját védi ebben az architektúrában. A Recovery Services-tárolóval az ügyfelek a teljes virtuális gép visszaállítása nélkül állíthatják vissza a fájlokat és mappákat egy IaaS virtuális gépről.

### <a name="logging-and-auditing"></a>Naplózás és naplózás

Az Azure-szolgáltatások széles körben naplózzák a rendszer és a felhasználó tevékenységét, valamint a rendszer állapotát:
- **Tevékenységek naplói**: A [tevékenységek naplói](../../azure-monitor/platform/activity-logs-overview.md) betekintést nyújtanak az előfizetésben lévő erőforrásokon végrehajtott műveletekre. A Tevékenységnaplók segítenek meghatározni a művelet kezdeményezőjét, az előfordulás időpontját és az állapotot.
- **Diagnosztikai naplók**: A [diagnosztikai naplók](../../azure-monitor/platform/diagnostic-logs-overview.md) az összes erőforrás által kibocsátott összes naplót tartalmazzák. Ezek a naplók a Windows-eseménynaplókat, az Azure Storage-naplókat, a Key Vault naplókat, valamint Application Gateway hozzáférési és tűzfal-naplókat tartalmaznak. Az összes diagnosztikai napló egy központi és titkosított Azure Storage-fiókba írja az archiválást. A megőrzés a felhasználó által konfigurálható, akár 730 nap, hogy megfeleljen a szervezetre vonatkozó megőrzési követelményeknek.

**Naplók Azure monitor**: Ezeket a naplókat a rendszer a feldolgozás, tárolás és irányítópult-jelentéskészítés [Azure monitor naplófájljaiban](https://azure.microsoft.com/services/log-analytics/) összesíti. Az adatgyűjtés után a rendszer adattípusonként külön táblába rendezi az adatokat, ez az eredeti forrástól függetlenül lehetővé teszi az adatok együttes elemzését. Emellett a Azure Security Center integrálható Azure Monitor naplókkal, így az ügyfelek Kusto-lekérdezéseket használhatnak a biztonsági események adatainak eléréséhez és más szolgáltatásokból származó adatokkal való összekapcsolásához.

Az architektúra részeként az alábbi Azure- [figyelési megoldások](../../monitoring/monitoring-solutions.md) szerepelnek:
-   [Active Directory Assessment](../../azure-monitor/insights/ad-assessment.md): A Active Directory Health-ellenőrzési megoldás rendszeres időközönként kivizsgálja a kiszolgálói környezetek kockázatait és állapotát, és rangsorolja a telepített kiszolgálói infrastruktúrára jellemző ajánlásokat.
- [SQL Assessment](../../azure-monitor/insights/sql-assessment.md): Az SQL Health-ellenőrzési megoldás rendszeres időközönként kivizsgálja a kiszolgálói környezetek kockázatait és állapotát, és az ügyfelek számára az üzembe helyezett kiszolgáló-infrastruktúrára jellemző ajánlások rangsorolt listáját biztosítja.
- [Agent Health](../../monitoring/monitoring-solution-agenthealth.md): Az Agent Health-megoldás jelentést készít, hogy hány ügynök van üzembe helyezve, valamint a földrajzi eloszlásuk, valamint a nem válaszoló ügynökök száma, valamint az operatív adatküldés alatt álló ügynökök száma.
-   [Activity log Analytics](../../azure-monitor/platform/collect-activity-logs.md): Az Activity Log Analytics-megoldás segítséget nyújt az Azure-tevékenységek naplóinak elemzéséhez az ügyfelek összes Azure-előfizetése között.

**Azure Automation**: [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) tárolja, futtatja és felügyeli a runbookok. Ebben a megoldásban a runbookok segítséget nyújt a naplók Azure SQL Databaseból való gyűjtésében. Az Automation [change Tracking](../../automation/change-tracking.md) megoldás lehetővé teszi, hogy az ügyfelek könnyedén azonosíthassák a környezet változásait.

**Azure monitor**: [Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) segíti a felhasználókat a teljesítmény nyomon követésében, a biztonság fenntartásában és a trendek azonosításában azáltal, hogy lehetővé teszi a szervezetek számára, hogy naplózzák, riasztásokat hozzon létre és archiválják az adatok archiválását, beleértve az Azure

## <a name="threat-model"></a>Veszélyforrások modellje

Az ehhez a hivatkozási architektúrához tartozó Adatfolyam-diagram [letölthető](https://aka.ms/uknhs-dw-tm) , vagy a következő címen érhető el. Ez a modell lehetővé teszi az ügyfeleknek, hogy a módosítások végrehajtása során megértsék a rendszerinfrastruktúra lehetséges kockázatait.

![Adattárház az Egyesült királyságbeli NHS Threat modellhez](images/uknhs-datawarehouse-threat-model.png?raw=true "Adattárház az Egyesült királyságbeli NHS Threat modellhez")

## <a name="compliance-documentation"></a>Megfelelőségi dokumentáció

Az [Azure Security and Compliance Blueprint – Egyesült Királysági NHS Customer felelősségi mátrixa](https://aka.ms/uknhs-crm) felsorolja az Egyesült királyságbeli NHS-követelményeket. Ez a mátrix részletesen ismerteti, hogy az egyes elvek megvalósítása a Microsoft, az ügyfél vagy a kettő közötti megosztás feladata-e.

Az [Azure Security and Compliance Blueprint – Egyesült Királysági NHS-adattárház megvalósítási mátrixa](https://aka.ms/uknhs-dw-cim) információt nyújt arról, hogy az adatraktár-architektúra milyen módon tárgyalja az Egyesült Királysági NHS követelményeit, beleértve a megvalósítás részletes leírásait is teljesíti az egyes érintett elvek követelményeit.

## <a name="guidance-and-recommendations"></a>Útmutatás és javaslatok

### <a name="vpn-and-expressroute"></a>VPN-és ExpressRoute

Biztonságos VPN-alagutat vagy [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) kell konfigurálni, hogy biztonságosan létre lehessen hozni egy kapcsolatot a jelen Pásti webalkalmazás-hivatkozási architektúra részeként üzembe helyezett erőforrásokkal. A VPN-vagy ExpressRoute megfelelő beállításával az ügyfelek hozzáadhatnak egy védelmi réteget az átvitelhez.

A biztonságos VPN-alagút az Azure-nal való megvalósításával létrehozhat egy virtuális magánhálózati kapcsolatot egy helyszíni hálózat és egy Azure-beli virtuális hálózat között. Ez a kapcsolat az interneten keresztül történik, és lehetővé teszi, &quot;hogy&quot; az ügyfelek biztonságosan továbbítsák az adatokat egy&#39;titkosított kapcsolaton belül az ügyfél hálózata és az Azure között. A helyek közötti VPN egy olyan biztonságos, érett technológia, amelyet évtizedek óta minden méretben üzembe helyezett a vállalatok. Ebben a beállításban az [IPsec-alagút mód](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) titkosítási mechanizmusként van használatban.

Mivel a VPN-alagúton belüli forgalom egy helyek közötti VPN-kapcsolaton keresztül halad át az interneten, a Microsoft egy másik, még biztonságosabb kapcsolódási lehetőséget kínál. Az Azure ExpressRoute egy dedikált WAN-kapcsolat az Azure és egy helyszíni hely vagy egy Exchange-szolgáltató között. Mivel a ExpressRoute-kapcsolatok nem az interneten keresztül haladnak át, ezek a kapcsolatok megbízhatóbbak, gyorsabbak, kisebb késések és nagyobb biztonságot biztosítanak, mint a szokásos kapcsolatok az interneten keresztül. Továbbá, mivel ez az ügyfél&#39;-távközlési szolgáltató közvetlen kapcsolata, az adatforgalom nem az interneten keresztül történik, ezért nem teszi elérhetővé.

Ajánlott eljárások egy biztonságos hibrid hálózat megvalósításához, amely kiterjeszti a helyszíni hálózatot az Azure- [](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)ra.

### <a name="extract-transform-load-process"></a>Kinyerés – átalakítás – betöltési folyamat

[](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) A kinyert adatok betölthetők Azure SQL Data Warehouseba anélkül, hogy külön kinyerési, átalakítási, betöltési vagy importálási eszközt kellene létrehoznia. A Base lehetővé teszi, hogy a T-SQL-lekérdezéseken keresztül hozzáférjen az adatokhoz. A Microsoft üzleti intelligenciával és elemzésével foglalkozó verem, valamint a SQL Server rendszerrel kompatibilis, külső gyártótól származó eszközök is használhatók.

### <a name="azure-active-directory-setup"></a>Azure Active Directory telepítő

[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) elengedhetetlen az üzembe helyezés kezeléséhez és a környezettel kommunikáló személyzethez való hozzáféréshez. Egy meglévő Windows Server-Active Directory [négy kattintással](../../active-directory/hybrid/how-to-connect-install-express.md)integrálható Azure Active Directoryba. Az ügyfelek az üzembe helyezett Active Directory infrastruktúrát (tartományvezérlőket) egy meglévő Azure Active Directory számára is megadhatják, ha a központilag telepített Active Directory infrastruktúra egy Azure Active Directory erdő altartománya.

### <a name="optional-services"></a>Választható szolgáltatások

Az Azure számos szolgáltatást kínál a formázott és formázatlan adattárolási és-előkészítési támogatáshoz. Az ügyfélre vonatkozó követelményektől függően a következő szolgáltatások adhatók hozzá ehhez a hivatkozási architektúrához:
-   A [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) egy felügyelt felhőalapú szolgáltatás, amely összetett hibrid kinyerési, átalakítási és adatintegrációs projektekhez készült. Azure Data Factory rendelkezik olyan képességekkel, amelyek segítségével nyomon követheti és megkeresheti az egészségüggyel kapcsolatos adatait, beleértve a vizualizációkat és a figyelési eszközöket, amelyekkel azonosíthatók az adatok, illetve honnan származnak. A Azure Data Factory használatával az ügyfelek olyan adatvezérelt munkafolyamatokat hozhatnak létre és ütemezhetnek, amelyek különböző adattárakból származó adatok betöltésére szolgáló folyamatok. Ezek a folyamatok lehetővé teszik az ügyfeleknek a belső és külső forrásokból származó adatok betöltését. Az ügyfelek ezután feldolgozhatják és átalakítják az adatokat a kimenet adattárakba, például a Azure SQL Data Warehouseba.
- Az ügyfelek a strukturálatlan adatmennyiséget [Azure Data Lake Storeba](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview)helyezhetik, ami lehetővé teszi bármilyen méretű, típusú és betöltési sebességű adat rögzítését egyetlen helyen az operatív és a felderítő elemzéshez.  Azure Data Lake rendelkezik olyan képességekkel, amelyek lehetővé teszik az adatfeldolgozást és-átalakítást. A Azure Data Lake Store kompatibilis a Hadoop-ökoszisztémában található legtöbb nyílt forráskódú összetevővel, és szépen integrálható más Azure-szolgáltatásokkal, például Azure SQL Data Warehouseokkal.

## <a name="disclaimer"></a>Jogi nyilatkozat

 - Ez a dokumentum csak tájékoztató jellegű. A MICROSOFT NEM VÁLLAL SEMMILYEN KIFEJEZETT, VÉLELMEZETT VAGY TÖRVÉNYES GARANCIÁT A JELEN DOKUMENTUMBAN FOGLALT INFORMÁCIÓK ALAPJÁN. Ez a dokumentum "aktuálisként" van megadva. Az ebben a dokumentumban kifejezett információk és nézetek, beleértve az URL-címeket és az internetes webhelyek más hivatkozásait, értesítés nélkül változhatnak. A dokumentumot olvasó ügyfeleink a használatuk kockázatát viselik.
 - A jelen dokumentum nem biztosít semmilyen jogot semmilyen Microsoft-termékben vagy-megoldásban található szellemi tulajdonhoz.
 - Az ügyfelek belső referenciák szerint másolhatják és használhatják ezt a dokumentumot.
 - A jelen dokumentumban szereplő javaslatok megnövelték az adatok, a hálózat vagy a számítási erőforrások használatát az Azure-ban, és növelhetik az ügyfelek Azure-licencét vagy előfizetési költségeit.
 - Ez az architektúra arra szolgál, hogy az ügyfelek számára a konkrét követelményekhez igazodva és nem használható éles környezetben.
 - Ez a dokumentum hivatkozásként van kifejlesztve, és nem használható az összes olyan eszköz meghatározására, amelyekkel az ügyfél megfelel bizonyos megfelelőségi követelményeknek és előírásoknak. Az ügyfeleknek jóvá kell hagyniuk a szervezeten belüli jogi támogatást a jóváhagyott ügyfél-implementációkban.
