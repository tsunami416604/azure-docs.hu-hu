---
title: Azure biztonsági és megfelelőségi terv – a FFIEC Data warehouse-bA
description: Azure biztonsági és megfelelőségi terv – a FFIEC Data warehouse-bA
services: security
author: meladie
ms.assetid: eb841702-057b-4e48-8bc8-2873e155d10e
ms.service: security
ms.topic: article
ms.date: 06/20/2018
ms.author: meladie
ms.openlocfilehash: 3ee164b013eff6664e423f3356b64744a1fa6611
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2018
ms.locfileid: "37908265"
---
# <a name="azure-security-and-compliance-blueprint-data-warehouse-for-ffiec-financial-services"></a>Azure biztonsági és megfelelőségi terv: Data Warehouse FFIEC a pénzügyi szolgáltatások

## <a name="overview"></a>Áttekintés

Az Azure biztonsági és megfelelőségi terv nyújt útmutatást a központi telepítéséhez a data warehouse architektúrája az Azure-ban a storage, és a Szövetségi pénzügyi intézmény vizsgálat Tanács által szabályozott pénzügyi adatok lekérésének alkalmas. (FFIEC).

Ez a referencia-architektúra, megvalósítási útmutató és veszélyforrások elleni modellje alapját FFIEC követelményeknek megfelelő ügyfelek számára. Ez a megoldás egy alapkonfiguráció segítenek az ügyfeleknek a számítási feladatok üzembe helyezéséhez az Azure-bA FFIEC megfelelő módon biztosít, azonban ez a megoldás nem használható-éles környezetben van, mivel további konfigurálásra szükség.

FFIEC-megfelelőség eléréséhez szükséges, hogy a minősített auditorok tanúsítása üzemi ügyfél megoldást. Naplózás FFIEC a tag ügynökségek, beleértve a kormányzója tábla a Szövetségi tartalék rendszer (FRB), a Szövetségi letéti biztosítási Corporation (FDIC), a nemzeti Credit Union adminisztrációs (NCUA), a Hivatala az elbírálók felügyeletét a pénznem (OCC), és a fogyasztói pénzügyi védelmi iroda (CFPB). Ezek az elbírálók igazolja, hogy eseményeket naplózott intézménye függetlenség karbantartását végző assessors végzi. Ügyfelek végző megfelelő biztonsági és megfelelőségi értékelést bármely megoldás használatával létrehozott ebben az architektúrában követelmények eltérőek lehetnek, hogy milyen ügyről van minden ügyfél megvalósítása alapján.

## <a name="architecture-diagram-and-components"></a>Architektúra és összetevők

A megoldás egy nagy teljesítményű és biztonságos felhőalapú adattárház, amely referenciaarchitektúra biztosít. Ez az architektúra két külön szinten szerepelnek: egyet, ahol adatok importálása, tárolásának és előkészített egy fürtözött SQL-környezetben, és a egy másik, ahol az adatok betöltése az Azure SQL Data warehouse használatával a kinyerési, átalakítási, betölteni az eszköz (pl. [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase) T-SQL-lekérdezések) feldolgozás céljából. Miután az Azure SQL Data Warehouse tároljuk, analytics futtathat nagy méretű.

Az Azure kínál a különböző jelentéskészítési és elemzési szolgáltatások, az ügyfél számára. Ez a megoldás gyors létrehozása a jelentések az Azure SQL Data Warehouse-ból az SQL Server Reporting Services (SSRS) tartalmaz. SQL forgalmat titkosítja a rendszer SSL-lel azáltal, önaláírt tanúsítványokat. Ajánlott eljárásként az Azure a fokozott biztonság egy megbízható hitelesítésszolgáltatótól használatát javasolja.

Az Azure SQL Data Warehouse az adatokat oszlopos szerkezetű jelentősen csökkenti a tárolási költségeket a lekérdezési teljesítmény javítása közben formátumú relációs tábláival tárolódik. Használati követelményeitől függően Azure SQL Data Warehouse számítási erőforrások méretezése felfelé vagy lefelé vagy kapcsolja ki teljesen esetén nem igénylő számítási erőforrások nincs aktív folyamatok.

SQL terheléselosztó kezeli az SQL-forgalmat, biztosítva a nagy teljesítményű. Ez a referenciaarchitektúra összes virtuális gépet egy rendelkezésre állási csoport egy Always On rendelkezésre állási csoportban magas rendelkezésre állású és vész-helyreállítási funkciók konfigurált SQL Server-példányok üzembe helyezhet.

A data warehouse referenciaarchitektúra egy Active Directory-réteget a architektúrája belüli erőforrások kezelését is. Az Active Directory-alhálózathoz lehetővé teszi, hogy könnyen bevezetési alatt egy nagyobb az Active Directory erdő struktúra, így folyamatos működése érdekében a környezetben, akkor is, ha a nagyobb erdőben való hozzáférés nem érhető el. Az összes virtuális gép tartományhoz csatlakoztatva az Active Directory-réteghez, és az Active Directory csoportházirend segítségével érvényesíti a biztonsági és megfelelőségi konfigurációk az operációs rendszer szintjén.

A megoldás az Azure Storage-fiókok, amelyek a felhasználók beállíthatják az inaktív adatok bizalmas mivoltát a Storage Service Encryption segítségével. Az Azure rugalmasságot ügyfél kiválasztott adatközpontban adatok három másolatát tárolja. Földrajzi georedundáns tárolás biztosítja, hogy az adatok replikálása másodlagos adatközpontba több száz mérföld távolságban, és újra azt az adatközpontot, az ügyfél elsődleges adatközpont káros eseményt megakadályozza az adatvesztést eredményez belül három példányban tárolja adatok.

A fokozott biztonság érdekében ebben a megoldásban az összes erőforrás egy erőforráscsoportba tartozó Azure Resource Manageren keresztül felügyelt. Az Azure Active Directory szerepköralapú hozzáférés-vezérlés való hozzáférés szabályozása használatos üzembe helyezett erőforrások, például a kulcsok Azure Key vaultban. A fájlrendszer állapotának az Azure Security Center és az Azure Monitor használatával felügyelik. Ügyfelek konfigurálása mindkét figyelési szolgáltatásokat naplók rögzítése és a fájlrendszer állapotának megjelenítése egyetlen, könnyen navigálható-irányítópulton.

A virtuális gépek felügyeleti bástyagazdagép, biztonságos kapcsolatot biztosít a rendszergazdák erőforrások eléréséhez az üzembe helyezett funkcionál. Az adatok betöltődtek, a felügyeleti megerősített gazdagépen keresztül, az átmeneti területre. **A Microsoft javasolja, hogy a felügyeleti és az adatok importálása a referencia architektúra alhálózatban egy VPN- vagy Azure ExpressRoute-kapcsolat beállítása.**

![Adatraktár-FFIEC architekturális diagramja](images/ffiec-dw-architecture.png "adatraktár-FFIEC architekturális diagramja")

Ez a megoldás a következő Azure-szolgáltatásokat használ. Az üzembe helyezési architektúra részletei a [üzembe helyezési architektúrája](#deployment-architecture) szakaszban.

- Rendelkezésre állási csoportok
    - Az Active Directory-tartományvezérlők
    - SQL-fürtcsomópontok és a tanúsító
- Azure Active Directory
- Azure Data Catalog
- Azure Key Vault
- Azure Monitor
- Azure Security Center
- Azure Load Balancer
- Azure Storage
- Azure Log Analytics
- Azure-alapú virtuális gépek
    - (1) Bástyagazdagép
    - (2) az active Directory-tartományvezérlő
    - (2) az SQL Server-fürt csomópontjának
    - (1) az SQL Server tanúsító
- Azure Virtual Network
    - (1) /16 hálózati
    - (4) /24 hálózatok
    - (4) a hálózati biztonsági csoportok
- Recovery Services-tároló
- SQL Data Warehouse
- SQL Server Reporting Services

## <a name="deployment-architecture"></a>Üzembe helyezési architektúrája

A következő szakaszt az üzembe helyezés és a megvalósítás elemek részletei.

**Az SQL Data Warehouse**: [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) van egy vállalati adatok adattárház (EDW), amely ötvözi a nagymértékben párhuzamos feldolgozási (MPP), gyorsan összetett lekérdezések futtatása több petabájtnyi adat, így a felhasználók pénzügyi adatok hatékony azonosítását. Felhasználók használhatják a egyszerű PolyBase T-SQL-lekérdezések big Data típusú adatok importálása az SQL Data Warehouse-ba, és a teljesítmény MPP nyújtotta lehetőségeket kiaknázva nagy teljesítményű elemzés futtatása felhasználását.

**Az SQL Server Reporting Services (SSRS)**: [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) gyors létrehozása a jelentések biztosít a táblák, diagramokat, térképeket, mérőműszerek, mátrixokban és több Azure SQL Data Warehouse számára.

**A Data Catalog**: [a Data Catalog](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog) teszi az adatforrásokat könnyen felfedezhetővé és értelmezhetővé teszi azon felhasználók számára, aki kezelheti az adatokat. Általános adatforrások regisztrálva, címkézett és pénzügyi adatokat keresni. A data marad, a meglévő hely, de a metaadatok másolatát bekerül a Data Catalog szolgáltatást, az adatforrás helye mutató hivatkozásokkal együtt. A metaadatok indexelésének köszönhetően az adatforrások egy egyszerű keresés által felfedezhetővé és könnyen értelmezhetővé válnak a felhasználók számára.

**Bástyagazdagép**: A bástyagazdagép a központi hely, amely lehetővé teszi a felhasználók hozzáférhessenek a környezetben telepített erőforrás. A bástyagazdagép üzembe helyezett erőforrásokhoz való biztonságos kapcsolatot nyújt azáltal, hogy csak nyilvános IP-címekről érkező távoli forgalmat a biztonságos elemek listájához. Távoli asztali (RDP) forgalmat lehetővé teszik, hogy a forgalom forrását kell definiálni, a hálózati biztonsági csoport tagja.

Ez a megoldás létrehoz egy virtuális gépet egy tartományhoz csatlakoztatott megerősített gazdagépként az alábbi konfigurációkkal:
-   [A kártevőirtó bővítmény](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Az Azure Diagnostics bővítmény](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Az Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) az Azure Key Vaulttal
-   Egy [automatikus leállítási házirend](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) csökkenthető a fogyasztása a virtuális gépi erőforrások, amikor nincs használatban
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) engedélyezve van, hogy a hitelesítő adatokat és egyéb titkos adatait, amely el van különítve a futó operációs rendszert a védett környezetben futnak.

### <a name="virtual-network"></a>Virtuális hálózat

Ez a referenciaarchitektúra egy privát virtuális hálózatot a 10.0.0.0/16 címteret az határozza meg.

**Hálózati biztonsági csoportok**: [hálózati biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) tartalmazza a hozzáférés-vezérlési listák, amelyek engedélyezik vagy megtagadják a forgalmat egy virtuális hálózaton belül. Hálózati biztonsági csoportok segítségével biztonságos forgalom egy alhálózatot vagy különálló virtuális gép szintjén. Létezik a következő hálózati biztonsági csoportok:

  - Egy hálózati biztonsági csoport (SQL Server-fürtöket, az SQL Server tanúsító és SQL terheléselosztó) az Adatréteg számára
  - Egy hálózati biztonsági csoport a felügyeleti bástyagazdagép
  - Az Active Directory hálózati biztonsági csoport
  - Az SQL Server Reporting Services egy hálózati biztonsági csoport

A hálózati biztonsági csoportok rendelkezik bizonyos portokat és protokollokat nyissa meg, hogy a megoldás működhet, biztonságos és megfelelően. Emellett a következő konfigurációk engedélyezve vannak az egyes hálózati biztonsági csoport:

- [Diagnosztikai naplók és események](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) engedélyezve van, és a storage-fiókban tárolt
- A log Analytics csatlakozik a [hálózati biztonsági csoport&#39;s diagnosztikai naplók](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Alhálózatok**: minden egyes alhálózat a megfelelő hálózati biztonsági csoport társítva.

### <a name="data-at-rest"></a>Inaktív adat

Az architektúra használatával több mértéket, beleértve a titkosítást és -adatbázis naplózási szolgáltatásával az inaktív adatok védi.

**Az Azure Storage**: rest-követelményeket, a titkosított adatok kielégítése érdekében minden [Azure Storage](https://azure.microsoft.com/services/storage/) használ [a Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption). Ez segít a szervezeti biztonsági kötelezettségeit és megfelelőségi követelmények FFIEC által meghatározott adatok biztonságos megőrzésében.

**Az Azure Disk Encryption**: [az Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) az adatlemezek kötettitkosítását adja meg a Windows BitLocker funkcióját használja. A megoldás integrálható az Azure Key Vault segítségével szabályozhatja, és kezelhetik a lemeztitkosítási kulcsokat.

**Az Azure SQL Database**: az Azure SQL Database-példányt használja a következő adatbázis biztonsági intézkedéseket:

- [Az Active Directory-hitelesítés és engedélyezés](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) lehetővé teszi az identitáskezelést adatbázis-felhasználók és más Microsoft-szolgáltatások egyetlen központi helyen.
- [Az SQL database naplózási szolgáltatásával](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) nyomon követi az adatbázisok eseményeit és felvezeti ezeket egy naplófájlba, egy Azure storage-fiókban.
- Az Azure SQL Database használatára van konfigurálva [transzparens adattitkosítás](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), melyik teljesít valós időben titkosítja és fejti vissza az adatbázist, az azokhoz kapcsolódó biztonsági mentési, és a rest-tranzakciós naplófájlokat: adatok védelme érdekében. Transzparens adattitkosítás biztosítja, hogy a tárolt adatok nem lett bárki hozzáférhet.
- [Tűzfalszabályok](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) megakadályozzák elérését, adatbázis-kiszolgálók, amíg a megfelelő engedélyekkel. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján.
- [Az SQL Fenyegetésészlelési](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) lehetővé teszi, hogy az észlelés és válasz a lehetséges veszélyforrásokra, bekövetkezésük pillanatában észlelhessék a biztonsági riasztásokat a gyanús adatbázis-tevékenységekről, a lehetséges biztonsági résekről, az SQL-injektálási támadások és a rendellenes adatbázis-hozzáférés minták.
- [Titkosított oszlopokat](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) győződjön meg arról, hogy bizalmas adatok soha nem jelenik meg, mint belül az adatbázis-rendszer egyszerű szövegként. Adatok titkosításának engedélyezése után csak az ügyfélalkalmazások vagy alkalmazáskiszolgálók hozzáférést a kulcsokhoz hozzáférhet egyszerű szöveges adatokat.
- [További tulajdonságok](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) lemondásához a feldolgozást az érintettek, is használható, mivel lehetővé teszi a felhasználók egyéni tulajdonságokat adhat az adatbázis-objektumok és adatok megjelölheti az, hogy a feldolgozást az alkalmazáslogika támogatása "Kifutott" kapcsolódó pénzügyi adatokat.
- [Sorszintű biztonság](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) lehetővé teszi a felhasználóknak a feldolgozási lemondásához adatokhoz való hozzáférés korlátozása a házirendek meghatározása érdekében.
- [Az SQL Database dinamikus adatmaszkolása](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) korlátozza a bizalmas adatok maszkolása az adatokat nem kiemelt jogosultságú felhasználók vagy alkalmazások által. Dinamikus adatmaszkolás automatikusan potenciálisan bizalmas adatok felderítéséhez, és javasolja a alkalmazni lehessen a megfelelő maszkokkal. Ez segít azonosítani és adatokhoz való hozzáférés korlátozása úgy, hogy azt nem létezik az adatbázis jogosulatlan hozzáférést. Dinamikus adatmaszkolási beállítások igazodnia kell az adatbázis-séma módosításával ügyfelek felelőssége.

### <a name="identity-management"></a>Identitáskezelés

A következő technológiákat az Azure-beli adatokhoz való hozzáférés kezeléséhez képességeket biztosítják:

- [Az Azure Active Directory](https://azure.microsoft.com/services/active-directory/) Microsoft&#39;s több-bérlős felhőalapú címtár- és identitáskezelési szolgáltatás. Ez a megoldás az összes felhasználó jönnek létre az Azure Active Directoryban, beleértve az Azure SQL Database hozzáférő felhasználók.
- Az alkalmazás-hitelesítést az Azure Active Directory használatával történik. További információkért lásd: [alkalmazások integrálása az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Ezenkívül az adatbázis oszloptitkosítás hitelesítheti az alkalmazást az Azure SQL Database, az Azure Active Directory. További információkért lásd: hogyan [bizalmas adatok védelme az Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- [Azure szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) lehetővé teszi a rendszergazdák számára, hogy a felhasználóknak frissíteniük kell a munkája elvégzéséhez csak olyan mértékű hozzáférést biztosítson a minden részletre kiterjedő hozzáférési engedélyek megadása. Ahelyett, hogy minden felhasználó ugyanolyan korlátlan jogosultságot ad az Azure-erőforrások, a rendszergazdák engedélyezhetik csak bizonyos adatok eléréséhez szükséges műveleteket. Az előfizetés-rendszergazda előfizetéshez való hozzáférés korlátozódik.
- [Az Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) lehetővé teszi az ügyfeleknek minimalizálása érdekében a felhasználóknak a számát, akik hozzáférhetnek bizonyos adatokat a. Az Azure Active Directory Privileged Identity Management rendszergazdái a felderítését, korlátozását és emelt szintű identitások és azok erőforrásokhoz való hozzáférésének figyelése. Ez a funkció is használható igény szerinti – igény rendszergazdai hozzáférés kényszerítésére.
- [Az Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) észleli a szervezet érintő lehetséges biztonsági résekről&#39;s identitásokat, konfigurálja az automatikus válaszok egy szervezet kapcsolódó észlelt gyanús tevékenységek&#39;s identitások , és megvizsgálja a gyanús események a problémák megoldásához a megfelelő műveletet.

### <a name="security"></a>Biztonság

**Titkok kezelése**: A megoldás [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) kulcsok és titkos kulcsok felügyeletéhez. Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. Az alábbi Azure Key Vault-képességek segítségével az ügyfelek védelme és az ilyen adatok elérése:

- Speciális hozzáférési szabályzatok kell alapon vannak konfigurálva.
- A Key Vault hozzáférési szabályzatok a minimálisan szükséges engedélyeket kulcsok és titkos kulcsok vannak meghatározva.
- Összes kulcsot és titkos kulcsok a Key Vaultban van lejárati dátumát.
- Speciális hardveres biztonsági modulok által védett összes kulcsok a Key Vaultban. A kulcs típusa egy HSM által védett 2048-bites RSA-kulcsot.
- Minden felhasználó és identitások kapnak a szerepköralapú hozzáférés-vezérlés használatával minimálisan szükséges engedélyeket.
- Diagnosztikai naplók a Key vault legalább 365 napos megőrzési idővel rendelkező engedélyezve vannak.
- A szükséges kapcsolatok engedélyezett titkosítási műveletek kulcsok korlátozódnak.

**Javítások kezelése**: Ez a referenciaarchitektúra részeként üzembe helyezett Windows virtuális gépek úgy vannak konfigurálva, az automatikusan frissítéseket kapjanak a Windows Update szolgáltatás alapértelmezés szerint. Ez a megoldás is magában foglalja a [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) szolgáltatást, amelyen frissített telepítések hozható létre a javítás virtuális gépekhez szükség esetén.

**Kártevők elleni védekezés**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) virtuális gépek számára biztosítja a valós idejű védelem funkció, amely alapján azonosíthatja, és távolítsa el a vírusok, kémprogramok és más, kártevő szoftverek, konfigurálható riasztásokkal Ha az ismert kártevő vagy nemkívánatos szoftverek megpróbálják telepíteni vagy futtatni védett virtuális gépeken.

**Az Azure Security Center**: A [az Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), ügyfelek is központilag alkalmazása és a számítási feladatok biztonsági házirendek kezelése, korlátozhatja a fenyegetéseknek való kitettséget, felismeri és elháríthatja a támadásokat. Az Azure Security Center ezenkívül meglévő konfigurációk az Azure-szolgáltatások konfigurációs és szolgáltatási javaslatok javíthatja biztonsági helyzetét és adatok védelme érdekében fér hozzá.

Az Azure Security Center észlelési képességek széles használatával ügyfeleket a környezetük célzó lehetséges támadások esetén. Ezek a riasztások értékes információkat tartalmaznak arról, hogy mi váltotta ki a riasztást, valamint a támadás forrásáról és az általa célba vett erőforrásokról. Az Azure Security Center készletével rendelkezik [biztonsági riasztások az előre meghatározott](https://docs.microsoft.com/en-us/azure/security-center/security-center-alerts-type), amely vannak aktiválódik, ha a fenyegetések vagy gyanús tevékenységek. [Egyéni riasztási szabályok](https://docs.microsoft.com/en-us/azure/security-center/security-center-custom-alert) az Azure Security Center lehetővé teszi ügyfeleink számára a környezetből már begyűjtött adatok alapján új biztonsági riasztásokat definiálhat.

Az Azure Security Center itt rangsorolt biztonsági riasztások és incidensek, így egyszerűbb a vásárlók megtalálhatják és a potenciális biztonsági problémákat. A [fenyegetésfelderítési jelentés](https://docs.microsoft.com/azure/security-center/security-center-threat-report) jön létre a rendszer minden egyes észlelt fenyegetés incidensmegoldási csapat segítség vizsgálatában és védekezhet a fenyegetések.

### <a name="business-continuity"></a>Az üzletmenet folytonossága

**Magas rendelkezésre állású**: Server számítási feladatainak szerint vannak csoportosítva egy [rendelkezésre állási csoport](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) az Azure-beli virtuális gépek magas rendelkezésre állás biztosítása érdekében. Legalább egy virtuális gép érhető el egy tervezett vagy nem tervezett karbantartási események esetén teljesíti a 99,95 %-os Azure SLA-t.

**Recovery Services-tároló**: A [Recovery Services-tároló](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) Kezelőkód biztonsági mentési adatokat, és ebben az architektúrában az Azure-beli virtuális gépek minden konfigurációját védi. Recovery Services-tárolóval ügyfelek is helyreállíthat fájlokat és mappákat IaaS virtuális gépről a teljes virtuális gép visszaállítása nélkül engedélyezése a gyorsabb helyreállítást.

### <a name="logging-and-auditing"></a>Naplózás és vizsgálat

Azure-szolgáltatások széles körben system és a felhasználói tevékenység, valamint a rendszerállapot jelentkezzen be:
- **A Tevékenységnaplók**: [tevékenységeket tartalmazó naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) adjon meg egy előfizetéshez tartozó erőforrásokon végrehajtott műveletekkel kapcsolatos információk. A Tevékenységnaplók segítségével határozza meg a műveletet kezdeményező, az eseményt, és állapot ideje.
- **Diagnosztikai naplók**: [diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) minden erőforrás által kibocsátott az összes napló tartalmazza. Ezek a naplók például a Windows rendszer-eseménynaplói, az Azure Storage-naplók, a Key Vault-naplók és az Application Gateway hozzáférés és a tűzfal a naplókat. Az összes diagnosztikai naplók írni egy központosított, titkosított csatornákon történik az Azure storage-fiókját archiválási. A megőrzési felhasználó által konfigurálható, mentése és 730 nap között, a megőrzési a szervezet konkrét követelményeinek.

**Log Analytics**: ezeket a naplókat a rendszer összevont [Log Analytics](https://azure.microsoft.com/services/log-analytics/) feldolgozási, tárolására és-irányítópult jelentéseit. Követően az adatok felépítéséről különböző adattípusokhoz belül az Operations Management Suite-munkaterületet, amely lehetővé teszi az adatok együttes elemzését az eredeti forrástól függetlenül, külön táblába. Ezen túlmenően az Azure Security Center integrálható a Log Analytics ami lehetővé teszi az ügyfelek számára a Log Analytics-lekérdezések használata a biztonsági események adatainak eléréséhez és más szolgáltatások származó adatokat kombinálni.

A következő Log Analytics [felügyeleti megoldások](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) Ez az architektúra egy része szerepel:
-   [Az Active Directory Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): az Active Directory állapot-ellenőrzés megoldás a kockázat és kiszolgálói környezetek állapotát értékeli a rendszeres időközönkénti, és a telepített kiszolgálói infrastruktúra vonatkozó javaslatok rangsorolt listáját tartalmazza.
- [SQL-értékeléssel](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): az SQL Health Check megoldás a kockázat és kiszolgálói környezetek állapotát értékeli a rendszeres időközönkénti, és nyújt a felhasználók számára a telepített kiszolgálói infrastruktúra vonatkozó javaslatok rangsorolt listáját.
- [Az ügynök állapota](https://docs.microsoft.com/en-us/azure/operations-management-suite/oms-solution-agenthealth): az Agent Health megoldás hány ügynök van telepítve, és a földrajzi elosztás, valamint hány ügynök, amely nem válaszol és működési adatokat küld be, amely az ügynökök számát jelenti.
-   [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): az ügyfél az összes Azure-előfizetések az Azure-Tevékenységnaplók elemzésének segíti az Activity Log Analytics megoldás.

**Az Azure Automation**: [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) tárolja, fut, és kezeli a runbookok. Ebben a megoldásban lévő runbookok segítségével naplók gyűjtése az Azure SQL Database-ből. Az Automation [Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) megoldás lehetővé teszi, hogy az ügyfelek számára, hogy könnyen azonosíthassa a változtatásokat a környezetben.

**Az Azure Monitor**: [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) segít a felhasználóknak a teljesítmény, biztonság fenntartására és a trendek azonosítására által lehetővé teszi a cégeknek naplózása, riasztásokat hozhat létre, és archiválja az adatokat, többek között API-hívások nyomon követése az Azure-ban az erőforrásokat.

## <a name="threat-model"></a>Fenyegetések modellezése

A referenciaarchitektúra az adatfolyam-diagram érhető el az [letöltése](https://aka.ms/ffiec-dw-tm/) vagy alább találja. Ez a modell segítségével az ügyfelek megismerhetik a pontokat a rendszer infrastruktúra esetleges kockázat, ha a végzett módosítások.

![Adatraktár-FFIEC fenyegetések modellezése](images/ffiec-dw-threat-model.png "FFIEC fenyegetések modellezése a Data warehouse-bA")

## <a name="compliance-documentation"></a>Megfelelőségi dokumentációja

A [Azure biztonsági és megfelelőségi terv – FFIEC vevő felelőssége mátrix](https://aka.ms/ffiec-crm) FFIEC által igényelt összes célok sorolja fel. Ez a mátrix részletezi a Microsoft, az ügyfél felelőssége egyes célok megvalósítását-e, vagy a kettő között megosztott.

A [Azure biztonsági és megfelelőségi terv – FFIEC Data Warehouse megvalósítási mátrix](https://aka.ms/ffiec-dw-cim) melyik FFIEC a célok címzett által a data warehouse architektúrája, beleértve a részletes leírását, hogy miként biztosít a megvalósítás megfelel az összes érintett cél.

## <a name="guidance-and-recommendations"></a>Útmutatás és javaslatok

### <a name="vpn-and-expressroute"></a>VPN és ExpressRoute

Biztonságos VPN-alagúton vagy [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) biztonságos kapcsolat a data warehouse referenciaarchitektúra részeként üzembe helyezett erőforrások kell konfigurálni. Megfelelően állítja be a VPN-t vagy ExpressRoute, az ügyfelek hozzáadhatja egy védelmi réteget biztosít adatokat átvitel közben.

Az Azure-ral biztonságos VPN-alagút alkalmazásával egy helyszíni hálózat és a egy Azure virtuális hálózat közötti virtuális magánhálózati kapcsolat hozható létre. Ezt a kapcsolatot az interneten keresztül történik, és az ügyfelek számára, hogy biztonságosan lehetővé teszi, hogy &quot;alagút&quot; egy titkosított kapcsolatot, az ügyfél között lévő információkat&#39;s hálózat és az Azure. Site-to-site VPN egy olyan biztonságos, érett technológia, évtizedes a vállalatok által központilag telepített. A [IPsec-alagút módú](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) ezt a beállítást, mint egy titkosítási mechanizmus használatban van.

Forgalom a VPN-alagút haladnak át a helyek közötti VPN-nel az interneten, mert a Microsoft kínál egy másik, még biztonságosabb kapcsolat lehetőséget. Az Azure ExpressRoute dedikált WAN Azure-ban és a egy helyszíni helyhez vagy egy Exchange-szolgáltató közötti kapcsolat. Az ExpressRoute-kapcsolatok az interneten keresztül halad, mivel a kapcsolatok az interneten keresztül ajánlja fel a további megbízhatóság megbízhatóbbak, gyorsabbak, kisebb a késésük, és biztonságosabbak a szokásos internetkapcsolatoknál. Továbbá mivel ez az ügyfél közvetlen kapcsolatot&#39;s távközlési szolgáltató, az adatok nem az interneten keresztül továbbítani, és ezért nem érhető el azt.

Ajánlott eljárások végrehajtásához egy biztonságos hibrid hálózatot, amely kiterjeszti a helyszíni hálózatot az Azure-bA [elérhető](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

### <a name="extract-transform-load-process"></a>Kinyerési, átalakítási-betöltési folyamat

[A PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) is adatok betöltése az Azure SQL Data Warehouse-bA nélkül egy külön van szükség a kinyerési, átalakítási, betölteni vagy importálni eszközt. A PolyBase lehetővé teszi, hogy a T-SQL-lekérdezések adatokhoz való hozzáférését. A Microsoft üzleti intelligencia és elemzés verem, valamint kompatibilis SQL Server, a harmadik felektől származó eszközök a polybase-zel használható.

### <a name="azure-active-directory-setup"></a>Az Azure Active Directory beállítása

[Az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) elengedhetetlen a központi telepítés kezelése és kiépítése a környezet implementálására csoporthoz való hozzáférést. Egy meglévő Windows Server Active Directory integrálhatók az Azure Active Directory- [négy kattintással](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Ügyfelek egy meglévő Azure Active Directory elősegítsék a telepített Active Directory-infrastruktúrát (tartományvezérlők) is, azáltal, hogy a telepített Active Directory-infrastruktúrát az Azure Active Directory-erdő egy altartományt.

### <a name="optional-services"></a>Választható szolgáltatások

Az Azure sokféle szolgáltatást nyújt a tárolási és formátumú és formázatlan adatok átmeneti kínál. Ez a referenciaarchitektúra ügyfél követelményeitől függően is hozzáadhatók a következő szolgáltatásokat:
-   [Az Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) egy felügyelt felhőszolgáltatás, amely a hibrid kinyerési, átalakítási-betöltési és adatintegrációs projektek. Az Azure Data Factory nyomkövetési és pénzügyi adatok képességekkel rendelkezik, többek között a vizualizációt, és monitorozási eszközökkel, amikor érkeznek be az adatok, és ahol származik. Azure Data Factoryt használó ügyfelek létrehozhatók és ütemezhetők a nevű folyamatok különböző adattárolókból adatokat beolvasó adatvezérelt munkafolyamatok. Ezek a folyamatok lehetővé teszi ügyfeleink számára egyaránt belső és külső forrásból származó adatokat. Ügyfelek, majd fel, és a kimeneti adatok átalakítása adattárakban, mint az Azure SQL Data warehouse-bA.
- Ügyfelek úgy végezheti el a strukturálatlan adatok [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview), amely lehetővé teszi, hogy a rögzítést, típusú és méretű, típusú, és a betöltési sebesség egyetlen helyen történő műveleti és felderítési elemzés céljából.  Azure Data Lake esetében képességeket, amelyek lehetővé teszik a kivonása és adatok átalakítását. Az Azure Data Lake Store a Hadoop-ökoszisztéma legtöbb nyílt forráskódú összetevőjével kompatibilis, és jól integrálható más Azure-szolgáltatások például az Azure SQL Data warehouse-bA.

## <a name="disclaimer"></a>Jogi nyilatkozat

 - Ez a dokumentum csak tájékoztatási célokat szolgál. A MICROSOFT NEM VÁLLAL SEMMILYEN KIFEJEZETT, KIFEJEZETT, VÉLELMEZETT VAGY KÖTELEZŐ GARANCIÁT A DOKUMENTUMBAN SZEREPLŐ INFORMÁCIÓRA VONATKOZÓAN. Ez a dokumentum biztosított "as-van." Információk és vélemények ebben a dokumentumban URL-CÍMEK és más internetes webhelyekre utaló hivatkozások értesítés nélkül változhatnak. Ez a dokumentum olvasásakor ügyfelek felelősségére használja.
 - Ez a dokumentum nem biztosít semmilyen jogot semmilyen Microsoft-termék vagy a megoldások a szellemi tulajdonhoz ügyfelek.
 - Ügyfelek másolhatja és használhatja ezt a dokumentumot belső, hivatkozási célokból.
 - Bizonyos ajánlások a dokumentum eredményezhet megnövekedett adat-, hálózati vagy számítási erőforrás-használat az Azure-ban, és előfordulhat, hogy növelje az ügyfél Azure licencek vagy előfizetések költségeit.
 - Ez az architektúra célja, hogy az ügyfelek számára az adott követelményekhez beállítása alapjaként szolgálja ki, és nem használható-éles környezetben van.
 - Ez a dokumentum referenciaként fejlesztik, és nem használható minden olyan eszközt, amely szerint egy ügyfél megfelel a megadott megfelelőségi követelmények és előírások meghatározásához. Ügyfelek jóváhagyott megvalósítás az ügyfeleknél a szervezet jogi támogatást kell kérnie.
