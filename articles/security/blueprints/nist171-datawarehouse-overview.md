---
title: Azure biztonsági és megfelelőségi terv – a NIST SP 800-171 Data warehouse-bA
description: Azure biztonsági és megfelelőségi terv – a NIST SP 800-171 Data warehouse-bA
services: security
author: jomolesk
ms.assetid: dbc9cafe-115d-4965-b0d4-fcf235a064c8
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: f774aed837fac1829413493c98b6df0e4ca30600
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47393213"
---
# <a name="azure-security-and-compliance-blueprint---data-warehouse-for-nist-sp-800-171"></a>Azure biztonsági és megfelelőségi terv – a NIST SP 800-171 Data warehouse-bA

## <a name="overview"></a>Áttekintés
[NIST Special Publication 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) hasznos útmutatást ad a szabályozott nem titkos információ (CUI) nonfederal információs rendszerek és a szervezetek a védelméhez. 800-171 NIST SP hozza létre a 14 termékcsaládok CUI titkosságát védelme kapcsolatos biztonsági követelményeket.

Az Azure biztonsági és megfelelőségi terv útmutatást nyújt a segítségével az ügyfelek üzembe helyezése a data warehouse architektúrája az Azure-ban, amely egy részhalmazát valósítja meg az NIST SP 800-171 szabályozza. Ez a megoldás bemutatja, amelyben ügyfeleink megfelel bizonyos biztonsági és megfelelőségi követelmények módon. Ez az ügyfelek létrehozása és konfigurálása saját adattárház-megoldások az Azure-ban alapjaként is szolgál.

Ez a referenciaarchitektúra társított megvalósítási útmutató és fenyegetések modellezése célja, hogy az ügyfelek számára az adott követelményekhez a felmerülő ismeretekkel szolgál. Nem használható-éles környezetben van. Ügyfelei felelősek a megfelelő biztonsági és megfelelőségi értékelést, ez az architektúra használatával létrehozott megoldások elvégzéséhez. Követelmények minden ügyfél megvalósítása tulajdonságairól alapján változhat.

## <a name="architecture-diagram-and-components"></a>Architektúra és összetevők
A megoldás biztosít egy referencia-architektúra, amely egy nagy teljesítményű és biztonságos felhőalapú adattárházat. Ez az architektúra két külön csomagban találhatók. Egy szint van ahol importált adatokkal, tárolja, és előkészített egy fürtözött SQL-környezetben. Az SQL data warehouse-hoz egy másik szintje. Ezzel a szinttel az adatok betöltése egy kinyerési, átalakítási-betöltési (ETL) eszközzel (például [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase) T-SQL-lekérdezések) feldolgozás céljából. Után az SQL Data Warehouse tároljuk, a analytics egy hatalmas méretekben futtathatja.

Az Azure kínál a különböző jelentéskészítési és elemzési szolgáltatások, az ügyfél számára. Ez a megoldás magában foglalja az SQL Server Reporting Services, az SQL data warehouse-ból jelentések gyors létrehozásához. SQL forgalmat titkosítja a rendszer SSL-lel azáltal, önaláírt tanúsítványokat. Ajánlott eljárásként javasoljuk, hogy egy megbízható hitelesítésszolgáltatótól a használatát a fokozott biztonságot.

Az Azure SQL Data Warehouse tárol adatokat oszlopos szerkezetű relációs tábláival. Ebben a formátumban jelentősen csökkenti tárolási költségeket, miközben ez növeli a lekérdezések teljesítményét. Használati követelményeitől függően az SQL Data Warehouse számítási erőforrások méretezése felfelé és lefelé vagy kapcsolja ki teljesen, ha nincs aktív folyamatok szükséges számítási erőforrásokért.

Az SQL Server terheléselosztó nagy teljesítmény biztosítása érdekében az SQL-forgalmat kezeli. Ez a referenciaarchitektúra összes virtuális gépek (VM) üzembe helyezhet egy rendelkezésre állási csoport egy Always On rendelkezésre állási csoportban konfigurált SQL Server-példányokat. Ez a konfiguráció a magas rendelkezésre állású és vész-helyreállítási lehetőségeket biztosít.

A data warehouse referenciaarchitektúra egy Active Directory-réteget a architektúrája belüli erőforrások kezelését is. Az Active Directory-alhálózathoz lehetővé teszi, hogy könnyen bevezetési alatt egy nagyobb az Active Directory-erdő struktúrájába. Ezzel a módszerrel a környezet működhet folyamatosan, még ha a nagyobb erdőben való hozzáférés nem érhető el. Minden virtuális gép olyan tartományhoz az Active Directory-réteghez. Active Directory csoportházirend segítségével, biztonsági és megfelelőségi konfigurációk az operációs rendszer szintjén kényszerítése.

A megoldás az Azure Storage-fiókok, amelyek a felhasználók beállíthatják az inaktív adatok bizalmas mivoltát a Storage Service Encryption segítségével. Az Azure rugalmasságot a kiválasztott-ügyfél adatközponton belül adatokat három példányban tárolja. Georedundáns tárolás biztosítja, hogy a rendszer replikálja az adatokat a egy másodlagos adatközpontba több száz mérfölddel nyelvet a lenti listában, és újra, hogy az adatközponton belül három példányban tárolja. Ezzel az elrendezéssel fokozott megakadályozza, hogy az ügyfél elsődleges adatközpont káros eseményt adatvesztést eredményez.

A fokozott biztonság érdekében ebben a megoldásban az összes erőforrás egy erőforráscsoportba tartozó Azure Resource Manageren keresztül felügyelt. Az Azure Active Directory (Azure AD) szerepköralapú hozzáférés-vezérlés (RBAC) szabályozható használatos üzembe az erőforrásokat. Ilyen erőforrások többek között a vásárlói kulcsok Azure Key vaultban. A fájlrendszer állapotának az Azure Security Center és az Azure Monitor használatával felügyelik. Ügyfelek konfigurálása mindkét figyelési szolgáltatásokat naplók rögzítésére. A fájlrendszer állapotának, amely egyszerűen használható egyetlen irányítópulton jelenik meg.

Egy virtuális Gépet egy felügyeleti bástyagazdagép funkcionál. Biztonságos kapcsolatot biztosít a rendszergazdák üzembe helyezett hozzáférését az erőforrásokhoz. Az adatok betöltődtek, a felügyeleti megerősített gazdagépen keresztül, az átmeneti területre. *Azt javasoljuk, hogy a felügyeleti és az adatok importálása a referencia architektúra alhálózatban a VPN- vagy Azure ExpressRoute kapcsolat konfigurálása.*

![Az adatraktár az NIST SP 800-171 architekturális diagramja](images/nist171-dw-architecture.png "az adatraktár az NIST SP 800-171 architekturális diagramja")

Ez a megoldás a következő Azure-szolgáltatásokat használ. További információkért lásd: a [üzembe helyezési architektúrája](#deployment-architecture) szakaszban.

- Rendelkezésre állási csoportok
    - Az Active Directory-tartományvezérlők
    - Az SQL Server-fürtcsomópontnak és tanúsító
- Azure Active Directory
- Azure Data Catalog
- Azure Key Vault
- Azure Monitor
- Azure Security Center
- Azure Load Balancer
- Azure Storage
- Azure Log Analytics
- Azure-alapú virtuális gépek
    - (1) bástyagazdagép
    - (2) az active Directory-tartományvezérlő
    - (2) az SQL Server-fürt csomópontjának
    - (1) az SQL Server tanúsító
- Azure Virtual Network
    - ((1) /16 hálózati
    - (4) /24 hálózatok
    - (4) a hálózati biztonsági csoportok
- Recovery Services-tároló
- SQL Data Warehouse
- SQL Server Reporting Services

## <a name="deployment-architecture"></a>Üzembe helyezési architektúrája
A következő szakaszt az üzembe helyezés és a megvalósítás elemek részletei.

**Az Azure SQL Data Warehouse**: [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) egy vállalati adattárház, amely kihasználja a nagymértékben párhuzamos feldolgozási gyorsan összetett lekérdezések futtatása több petabájtnyi adat. Felhasználók egyszerű PolyBase T-SQL-lekérdezések segítségével big Data típusú adatok importálása az SQL data warehouse-ba, és használja a nagymértékben párhuzamos feldolgozási teljesítmény nagy teljesítményű elemzési futtatásához.

**SQL Server Reporting Services**: [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) gyors létrehozása a jelentések biztosít a táblák, diagramokat, térképeket, mérőműszerek, mátrixokban és az SQL Data Warehouse több.

**Az Azure Data Catalog**: [a Data Catalog](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog) megkönnyíti az adatforrások megtalálhatóvá és értelmezhetővé azon felhasználók számára, aki kezelheti az adatokat. Általános adatforrások regisztrálva, címkézett és adatokat keresni. A data marad, a meglévő hely, de a metaadatok másolatát bekerül a Data Catalog szolgáltatásba. Az adatforrás helyét egy hivatkozást részét képezi. A metaadatok, hogy az egyes adatforrások egyszerűen felderítheti, felvétellel indexelve van. Indexelő is teszi értelmezhetővé teszi a felhasználók számára.

**Bástyagazdagép**: A bástyagazdagép a központi hely, amely a felhasználók használhatják az ebben a környezetben üzembe helyezett erőforrások eléréséhez. A bástyagazdagép biztosítja, hogy a telepített erőforrások biztonságos kapcsolatot csak nyilvános IP-címekről érkező távoli forgalmat engedélyezi a biztonságos elemek listájához. Távoli asztali forgalmat lehetővé teszik, hogy a forgalom forrását definiálni kell a hálózati biztonsági csoport.

Ez a megoldás létrehoz egy virtuális Gépet egy tartományhoz csatlakoztatott megerősített gazdagépként az alábbi konfigurációkkal:
-   [A kártevőirtó bővítmény](https://docs.microsoft.com/azure/security/azure-security-antimalware).
-   [Az Azure Diagnostics bővítmény](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template).
-   [Az Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) Key Vault használatával.
-   Egy [automatikus leállítási házirend](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) amikor nincs használatban a Virtuálisgép-erőforrások felhasználásának csökkentése érdekében.
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) engedélyezve van, hogy a hitelesítő adatokat, valamint egyéb titkok futtassa az elkülönített védett környezetben futó operációs rendszert.

### <a name="virtual-network"></a>Virtuális hálózat
Ez a referenciaarchitektúra egy privát virtuális hálózatot a 10.0.0.0/16 címteret az határozza meg.

**Hálózati biztonsági csoportok**: [hálózati biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (NSG) tartalmazza a hozzáférés-vezérlési listák, amelyek engedélyezik vagy megtagadják a forgalmat egy virtuális hálózaton belül. Az NSG-ket egy alhálózatot vagy az egyes Virtuálisgép-szintű forgalom védelmére használható. Az alábbi NSG-k léteznek:
  - Egy NSG-t (SQL Server-fürtök, az SQL Server tanúsító és SQL terheléselosztó) az adatréteg számára
  - A felügyeleti bástyagazdagép az NSG-t
  - Egy NSG-t az Active Directory
  - Egy NSG-t az SQL Server Reporting Services

Az NSG-k mindegyike rendelkezik bizonyos portokat és protokollokat nyissa meg, hogy a megoldás működhet, biztonságos és megfelelően. Emellett a következő konfigurációk engedélyezve vannak az egyes NSG:
  - [Diagnosztikai naplók és események](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) engedélyezett és a egy tárfiókban tárolja.
  - A log Analytics csatlakozik a [NSG-t a diagnosztika](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json).

**Alhálózatok**: minden egyes alhálózathoz társítva a megfelelő NSG-t.

### <a name="data-at-rest"></a>Inaktív adat
Az architektúra használatával több mértéket az inaktív adatok védi. Ezeket a mértékeket tartalmazzák, titkosítás és az adatbázis naplózási szolgáltatásával.

**Az Azure Storage**: igényeinek megfelelően a titkosított adatok inaktív állapotban, minden [tárolási](https://azure.microsoft.com/services/storage/) használ [a Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption). Ez a funkció segít a szervezeti biztonsági kötelezettségeit és megfelelőségi követelmények támogatásához adatok biztonságos megőrzésében.

**Az Azure Disk Encryption**: [lemeztitkosítás](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) kötettitkosítást biztosít az operációs rendszer és az adatlemezeket a Windows BitLocker funkcióját használja. A megoldás segítségével szabályozhatja, és a lemeztitkosítási kulcsok kezelése a Key Vault integrálható.

**Az Azure SQL Database**: az SQL Database-példányt használja a következő adatbázis biztonsági intézkedéseket:
-   [Az Active Directory-hitelesítés és engedélyezés](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) lehetővé teszi az identitáskezelést adatbázis-felhasználók és más Microsoft-szolgáltatások egyetlen központi helyen.
-   [Az SQL Database naplózási szolgáltatásával](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) nyomon követi az adatbázisok eseményeit és felvezeti ezeket egy naplófájlba, egy Azure storage-fiókban.
-   SQL-adatbázis használatára van konfigurálva [transzparens adattitkosítás](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Valós idejű titkosítási és visszafejtési az adatbázis, azokhoz kapcsolódó biztonsági mentési és tranzakciós naplófájlokra tárolt adatok inaktív védelme hajtja végre. Transzparens adattitkosítás biztosítja, hogy a tárolt adatok még nem vonatkoznak a jogosulatlan hozzáférést.
-   [Tűzfalszabályok](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) megakadályozzák elérését, adatbázis-kiszolgálók, amíg a megfelelő engedélyekkel. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján.
-   [Az SQL Fenyegetésészlelési](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) lehetővé teszi, hogy az észlelés és válasz a lehetséges veszélyforrásokra azok bekövetkezésekor. Biztonsági riasztások biztosítja a gyanús adatbázis-tevékenységekről, a lehetséges biztonsági résekről, az SQL-injektálási támadások és a rendellenes adatbázis-hozzáférési mintákról.
-   [Titkosított oszlopokat](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) győződjön meg arról, hogy bizalmas adatok soha nem jelenik meg az adatbázis-rendszer egyszerű szövegként. Ha engedélyezett az adattitkosítás, csak az ügyfélalkalmazások vagy alkalmazáskiszolgálók hozzáférést a kulcsokhoz hozzáférhet egyszerű szöveges adatokat.
- [További tulajdonságok](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) lemondásához az Adattulajdonosok feldolgozását is használható. Felhasználók egyéni tulajdonságokat adhat hozzá az adatbázis-objektumok. Akkor is is megjelölheti adatok "Kifutott" Az alkalmazáslogika kapcsolódó pénzügyi adatok feldolgozásának elkerülése érdekében támogatása.
- [Sorszintű biztonság](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) lehetővé teszi a felhasználóknak a feldolgozási lemondásához adatokhoz való hozzáférés korlátozása a házirendek meghatározása érdekében.
- [Az SQL Database dinamikus adatmaszkolása](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) által a replikaadatok felhasználók vagy alkalmazások, adatok maszkolása korlátozza a bizalmas adatok. Automatikusan potenciálisan bizalmas adatok felderítéséhez és javasoljuk a alkalmazni lehessen a megfelelő maszkokkal. Dinamikus adatmaszkolás segít hozzáférés korlátozása, így bizalmas adatok nem az adatbázis jogosulatlan hozzáférést. *Be kell tartaniuk az adatbázisséma beállítások hangolását ügyfelek felelőssége.*

### <a name="identity-management"></a>Identitáskezelés
A következő technológiákat az Azure-beli adatokhoz való hozzáférés kezeléséhez képességeket biztosítják:
-   [Az Azure AD](https://azure.microsoft.com/services/active-directory/) van a Microsoft több-bérlős felhőalapú címtár- és identitáskezelési szolgáltatás. A megoldás minden felhasználó jönnek létre az Azure ad-ben, és az az SQL-adatbázishoz hozzáféréssel rendelkező felhasználókat tartalmazzák.
-   Hitelesítés az alkalmazás Azure AD használatával történik. További információkért lásd: hogyan [alkalmazások integrálása az Azure ad-vel](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Az adatbázis oszloptitkosítás is az Azure AD az SQL Database az alkalmazás hitelesítéséhez. További információkért lásd: hogyan [bizalmas adatok védelme az SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Az Azure RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) segítségével a rendszergazdák hozzáférés részletes engedélyeket határozhat meg. Azt, akkor is csak a mértékű hozzáférést biztosítson a felhasználóknak frissíteniük kell a munkája elvégzéséhez. Ahelyett, hogy minden felhasználó ugyanolyan korlátlan hozzáférést az Azure-erőforrások ad, a rendszergazdák engedélyezhetik csak bizonyos műveleteket, erőforrások és adatok eléréséhez. Az előfizetés-rendszergazda előfizetéshez való hozzáférés korlátozódik.
- [Az Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) minimalizálása érdekében a felhasználók száma, akik hozzáférhetnek bizonyos adatokat, például az adatok ügyfelek által használható. A rendszergazdák Azure AD Privileged Identity Management segítségével Fedezze fel, korlátozhatja és emelt szintű identitások és azok erőforrásokhoz való hozzáférésének figyelése. Ez a funkció is segítségével igény szerinti – igény rendszergazdai hozzáférés kényszerítésére.
- [Az Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) észleli a szervezet identitásait érintő lehetséges biztonsági résekről. Konfigurálja az automatikus válaszok a szervezet identitásait kapcsolódó észlelt gyanús tevékenységek. Azt is kivizsgálja a problémák megoldásához a megfelelő művelet végrehajtása a gyanús események.

### <a name="security"></a>Biztonság
**Titkok kezelése**: A megoldás [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) kulcsok és titkos kulcsok felügyeletéhez. Key Vault segít a védelem titkosítási kulcsok és titkos kódok felhőalapú alkalmazások és szolgáltatások által használt. A következő Key Vault-képességek segítségével az ügyfelek adatok védelme érdekében:
- Speciális hozzáférési szabályzatok kell alapon vannak konfigurálva.
- A Key Vault hozzáférési szabályzatok a minimálisan szükséges engedélyeket kulcsok és titkos kulcsok vannak meghatározva.
- Összes kulcsot és titkos kulcsok a Key Vaultban van lejárati dátumát.
- Speciális hardveres biztonsági modulok által védett összes kulcsok a Key Vaultban. A kulcs típusa a hardveres biztonsági modul által védetté 2048-bites RSA-kulcsok.
- Minden felhasználó és identitások minimálisan szükséges engedélyeket kapnak az RBAC használatával.
- Diagnosztikai naplók a Key vault legalább 365 napos megőrzési idővel rendelkező engedélyezve vannak.
- A szükséges kapcsolatok engedélyezett titkosítási műveletek kulcsok korlátozódnak.

**Javítások kezelése**: Windows virtuális gépeket telepíteni, mivel ez a referenciaarchitektúra része automatikusan frissítéseket kapjanak a Windows Update szolgáltatás alapértelmezés szerint vannak konfigurálva. Ez a megoldás is magában foglalja a [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) szolgáltatást, amelyen keresztül frissített telepítések hozható létre virtuális gépeket, ha a szükséges javítási.

**Kártevők elleni védekezés**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) virtuális gépek számára biztosítja a valós idejű védelem funkció, amely alapján azonosíthatja, és távolítsa el a vírusok, kémprogramok és más, kártevő szoftverek. Ügyfelek is beállíthat riasztásokat, amelyek készítése, ha ismert kártevő vagy nemkívánatos szoftverek megpróbálják telepíteni vagy futtatni védett virtuális gépeken.

**Az Azure Security Center**: A [a Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), ügyfelek is központilag alkalmazása és a számítási feladatok biztonsági házirendek kezelése, korlátozhatja a fenyegetéseknek való kitettséget, felismeri és elháríthatja a támadásokat. A Security Center is éri el a meglévő konfigurációk az Azure-szolgáltatások konfigurációs és szolgáltatási javaslatok javíthatja biztonsági helyzetét és adatok védelme érdekében.

A Security Center használatával észlelési képességek széles ügyfelek esetleges támadások, hogy a cél riasztás környezetük. Ezek a riasztások értékes információkat tartalmaznak arról, hogy mi váltotta ki a riasztást, valamint a támadás forrásáról és az általa célba vett erőforrásokról. A Security Center készletével rendelkezik [biztonsági riasztások az előre meghatározott](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) , amely vannak aktiválódik, ha a fenyegetések vagy gyanús tevékenység történik. Az ügyfelek használhatják [egyéni riasztási szabályok](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) meghatározásához a környezetből már begyűjtött adatok alapján új biztonsági riasztásokat.

A Security Center rangsorolt biztonsági riasztások és incidensek biztosít. A Security Center egyszerűbbé teszi az ügyfelek felderítéséhez és a potenciális biztonsági problémákat. A [fenyegetésfelderítési jelentés](https://docs.microsoft.com/azure/security-center/security-center-threat-report) jön létre a rendszer minden egyes észlelt fenyegetés. Incidensmegoldási csapat a jelentéseket használhatja, amikor elhárításban, és vizsgálja meg.

Ez a referenciaarchitektúra is használ a [biztonságirés-értékelési](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) képesség a Security Centerben. Beállítások konfigurálása után a partnerügynök (például a Qualys) jelenti a biztonsági adatok a partner felügyeleti platformjára. Ezután a partner felügyeleti platformja a biztonsági résekre vonatkozó és állapotmonitorozási adatokat küld vissza a Security Centernek. Ügyfelek használhatják ezeket az adatokat gyorsan azonosíthatja a sebezhető virtuális gépeket.

### <a name="business-continuity"></a>Az üzletmenet folytonossága
**Magas rendelkezésre állású**: Server számítási feladatainak szerint vannak csoportosítva egy [rendelkezésre állási csoport](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) az Azure-beli virtuális gépek magas rendelkezésre állás biztosítása érdekében. Legalább egy virtuális gép érhető el egy tervezett vagy nem tervezett karbantartási események, amely megfelel a 99,95 %-os Azure SLA-t.

**Recovery Services-tároló**: A [Recovery Services-tároló](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) Kezelőkód biztonsági mentési adatokat, és ebben az architektúrában a virtuális gépek minden konfigurációját védi. A Recovery Services-tárolóval, ügyfelek is fájlok és mappák visszaállítása az IaaS virtuális gépről a teljes virtuális gép visszaállítása nélkül. Ez a folyamat felgyorsítja a helyreállítási időt.

### <a name="logging-and-auditing"></a>Naplózás és vizsgálat

Azure-szolgáltatások széles körben system és a felhasználói tevékenység, valamint a rendszerállapot jelentkezzen be:
- **A Tevékenységnaplók**: [tevékenységeket tartalmazó naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) adjon meg egy előfizetéshez tartozó erőforrásokon végrehajtott műveletekkel kapcsolatos információk. A Tevékenységnaplók segítségével határozza meg a műveletet kezdeményező, az eseményt, és állapot ideje.
- **Diagnosztikai naplók**: [diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) minden erőforrás által kibocsátott az összes napló tartalmazza. Ezek a naplók például a Windows rendszer-eseménynaplói, a Storage-naplók, a Key Vault-naplók és az Azure Application Gateway hozzáférés és a tűzfal a naplókat. Az összes diagnosztikai naplók írni egy központosított, titkosított csatornákon történik az Azure storage-fiókját archiválási. Felhasználók is konfigurálhatók a megőrzés időtartamát, legfeljebb 730 napig, az adott igényeknek.

**Log Analytics**: ezeket a naplókat a rendszer összevont [Log Analytics](https://azure.microsoft.com/services/log-analytics/) feldolgozási, tárolására és-irányítópult jelentéseit. Az adatokat a begyűjtésük után azt rendszer adattípusonként külön táblába rendezi az egyes adattípusokhoz belül az Operations Management Suite-munkaterületet. Ily módon az összes adat elemezhetők együtt, az eredeti forrástól függetlenül. A Security Center integrálható a Log Analytics használatával. Ügyfelek használhatják a Log Analytics-lekérdezéseket a biztonsági események adatainak eléréséhez és más szolgáltatások származó adatokat kombinálni.

A következő Log Analytics [felügyeleti megoldások](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) Ez az architektúra egy része szerepel:
-   [Az Active Directory assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): A Active Directory Health Check megoldással felmérheti a kockázat és kiszolgálói környezetek állapotát rendszeres időközönkénti. Biztosít a telepített kiszolgálói infrastruktúra vonatkozó javaslatok rangsorolt listáját.
- [SQL-értékeléssel](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): az SQL Health Check megoldással felmérheti a kockázat és kiszolgálói környezetek állapotát rendszeres időközönkénti. Ügyfelek biztosít a telepített kiszolgálói infrastruktúra vonatkozó javaslatok rangsorolt listáját.
- [Az ügynök állapota](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): az Agent Health megoldás jelentések hány ügynök van telepítve, és a földrajzi eloszlása. Hány ügynök nem válaszol, és küldje el az operatív adatokat, az ügynökök számát is jelenti.
-   [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): az ügyfél az összes Azure-előfizetések az Azure-Tevékenységnaplók elemzésének segíti az Activity Log Analytics megoldás.

**Az Azure Automation**: [Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) tárolja, fut, és kezeli a runbookok. Ebben a megoldásban lévő runbookok segítségével naplók gyűjtése az SQL Database-ből. Az ügyfelek használhatják az Automation [Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) megoldás könnyedén azonosíthatja a változtatásokat a környezetben.

**Az Azure Monitor**: [figyelő](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) segít a felhasználóknak a teljesítmény, a biztonság fenntartására és a trendek azonosítására. Szervezetek használhatják azt a naplózása, létre riasztásokat és archiválhat adatokat. Akkor is nyomon követheti a saját Azure-erőforrások API-hívások.

## <a name="threat-model"></a>Fenyegetések modellezése

A referenciaarchitektúra az adatfolyam-diagram érhető el az [letöltése](https://aka.ms/nist171-dw-tm) vagy itt található. Ez a modell segítségével az ügyfelek megismerhetik a pontokat a rendszer infrastruktúra esetleges kockázat, ha azok hajtsa végre a módosításokat.

![Az adatraktár az NIST SP 800-171 fenyegetések modellezése](images/nist171-dw-threat-model.png "az adatraktár az NIST SP 800-171 fenyegetések modellezése")

## <a name="compliance-documentation"></a>Megfelelőségi dokumentációja
A [Azure biztonsági és megfelelőségi terv – 800-171 NIST SP vevő felelőssége mátrix](https://aka.ms/nist171-crm) NIST SP 800-171 által igényelt összes biztonsági vezérlő sorolja fel. Ez a mátrix részletezi a Microsoft, az ügyfél felelőssége végrehajtására az egyes vezérlőelemek-e, vagy a kettő között megosztott.

A [Azure biztonsági és megfelelőségi terv - NIST SP 800-171 Data Warehouse vezérlő végrehajtása mátrix](https://aka.ms/nist171-dw-cim) melyik NIST SP a 800-171 vezérlők esnek a data warehouse architektúrája információkat nyújt. Hogyan megvalósítása megfelel-e a követelményeknek, az egyes kezelt vezérlőelemek a részletes leírását tartalmazza.

## <a name="guidance-and-recommendations"></a>Útmutatás és javaslatok

### <a name="vpn-and-expressroute"></a>VPN és ExpressRoute
Biztonságos VPN-alagúton vagy [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) konfigurálni kell, hogy biztonságos kapcsolatot a data warehouse referenciaarchitektúra részeként üzembe helyezett erőforrásokkal. Megfelelően állítja be a VPN-t vagy ExpressRoute, az ügyfelek hozzáadhatja egy védelmi réteget biztosít adatokat átvitel közben.

Az Azure-ral biztonságos VPN-alagút alkalmazásával egy helyszíni hálózat és a egy Azure virtuális hálózat közötti virtuális magánhálózati kapcsolat hozható létre. Ezt a kapcsolatot az interneten keresztül történik. Ügyfelek használhatják az titkosított kapcsolaton az ügyfél hálózati és az Azure között lévő biztonságosan "csatorna" információkat ezen a kapcsolaton. Site-to-site VPN egy olyan biztonságos, érett technológia, évtizedes a vállalatok által központilag telepített. A [IPsec-alagút módú](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) ezt a beállítást, mint egy titkosítási mechanizmus használatban van.

A VPN-alagút belül adatforgalomra site-to-site VPN-nel az internethez, mert a Microsoft kínál egy másik még biztonságosabb kapcsolat lehetőséget. Az ExpressRoute dedikált WAN Azure-ban és a egy helyszíni helyhez vagy egy Exchange-szolgáltató közötti kapcsolat. Az ExpressRoute-kapcsolatok közvetlenül csatlakozhat az ügyfél távközlési szolgáltató. Ennek következtében az adatok nem az interneten keresztül továbbítani, és nincs kitéve. Ezek a kapcsolatok több megbízhatóság megbízhatóbbak, gyorsabbak, kisebb a késésük, és biztonságosabbak a szokásos internetkapcsolatoknál kínálnak.

Ajánlott eljárások végrehajtásához egy biztonságos hibrid hálózatot, amely kiterjeszti a helyszíni hálózatot az Azure-bA [elérhető](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

### <a name="extract-transform-load-process"></a>Kinyerési, átalakítási-betöltési folyamat
[A PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) adatok betöltése az SQL Data Warehouse-bA egy külön ETL nélkül vagy eszköz importálása. A PolyBase lehetővé teszi, hogy a T-SQL-lekérdezések adatokhoz való hozzáférését. A Microsoft üzleti intelligencia és elemzés verem és a harmadik felektől származó eszközök, amelyek kompatibilisek az SQL Server a polybase-zel használható.

### <a name="azure-ad-setup"></a>Az Azure AD beállítása
[Az Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) elengedhetetlen a központi telepítés kezelése és kiépítése a környezet implementálására csoporthoz való hozzáférést. A helyszíni Active Directory integrálhatók az Azure AD-t [négy kattintással](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Ügyfelek is elősegítsék a telepített Active Directory-infrastruktúrát (tartományvezérlők) az Azure ad-hez. Ehhez végezze el a telepített Active Directory-infrastruktúrát részterületét képezi az Azure AD-erdőben.

### <a name="optional-services"></a>Választható szolgáltatások
Az Azure sokféle szolgáltatást nyújt a tárolási és formátumú és formázatlan adatok átmeneti kínál. Ez a referenciaarchitektúra az ügyfelek igényei alapján is hozzáadhatók a következő szolgáltatásokat:
-   [Az Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) egy felügyelt felhőszolgáltatás, amely az összetett hibrid ETL-kivonat-terhelés-átalakítási és adatintegrációs projektek épül. A Data Factory és a nyomkövetési adatok képességekkel rendelkezik. Vizualizáció és monitorozási eszközökkel, azonosítsa a érkeznek be az adatok, és ahol származik. Ügyfelek létrehozhatók és ütemezhetők az adatvezérelt munkafolyamatok, a folyamatok, különböző adattárolókból adatokat beolvasó nevű. Használhatják a folyamatok belső és külső forrásból származó adatok betöltését. Ügyfelek, majd fel, és a kimeneti adatok átalakítása adattárakban, például az SQL Data warehouse-bA.
- Ügyfelek úgy végezheti el a strukturálatlan adatok [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) helyen történő műveleti és felderítési jellegű mindenféle méretű, típusú és feldolgozási sebességű adatok rögzítéséhez. Azure Data Lake esetében képességeket, amelyek lehetővé teszik a kivonása és adatok átalakítását. Data Lake Store a Hadoop-ökoszisztéma legtöbb nyílt forráskódú összetevőjével kompatibilis. Emellett együttműködik a szépen más Azure-szolgáltatásokkal, például az SQL Data warehouse-bA.

## <a name="disclaimer"></a>Jogi nyilatkozat

 - Ez a dokumentum csak tájékoztatási célokat szolgál. A MICROSOFT NEM VÁLLAL SEMMILYEN KIFEJEZETT, KIFEJEZETT, VÉLELMEZETT VAGY KÖTELEZŐ GARANCIÁT A DOKUMENTUMBAN SZEREPLŐ INFORMÁCIÓRA VONATKOZÓAN. Ez a dokumentum biztosított "as-van." Információk és vélemények ebben a dokumentumban URL-CÍMEK és más internetes webhelyekre utaló hivatkozások értesítés nélkül változhatnak. Ez a dokumentum olvasásakor ügyfelek felelősségére használja.
 - Ez a dokumentum nem biztosít semmilyen jogot semmilyen Microsoft-termék vagy a megoldások a szellemi tulajdonhoz ügyfelek.
 - Ügyfelek másolhatja és használhatja ezt a dokumentumot belső, hivatkozási célokból.
 - Bizonyos ajánlások a dokumentum eredményezhet megnövekedett adat-, hálózati vagy számítási erőforrás-használat az Azure-ban, és előfordulhat, hogy növelje az ügyfél Azure licencek vagy előfizetések költségeit.
 - Ez az architektúra célja, hogy az ügyfelek számára az adott követelményekhez beállítása alapjaként szolgálja ki, és nem használható-éles környezetben van.
 - Ez a dokumentum referenciaként fejlesztik, és nem használható minden olyan eszközt, amely szerint egy ügyfél megfelel a megadott megfelelőségi követelmények és előírások meghatározásához. Ügyfelek jóváhagyott megvalósítás az ügyfeleknél a szervezet jogi támogatást kell kérnie.
