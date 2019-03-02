---
title: Azure biztonsági és megfelelőségi terv – a FedRAMP Automation Data warehouse-bA
description: Azure biztonsági és megfelelőségi terv – a FedRAMP Automation Data warehouse-bA
services: security
author: jomolesk
ms.assetid: 834d1ff6-8369-455f-b052-1ef301e3d7e6
ms.service: security
ms.topic: article
ms.date: 05/02/2018
ms.author: jomolesk
ms.openlocfilehash: 426293dbb3650b5a2bb3809b1e808f640392791d
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2019
ms.locfileid: "57241952"
---
# <a name="azure-security-and-compliance-blueprint-data-warehouse-for-fedramp-automation"></a>Azure biztonsági és megfelelőségi terv: A FedRAMP Automation Data warehouse-bA

## <a name="overview"></a>Áttekintés

A [Federal Risk and Authorization Management Program (FedRAMP)](https://www.fedramp.gov/) egy Egyesült államokbeli közigazgatásra kiterjedő program, amely a biztonság értékeléséhez, engedélyezési és folyamatos monitorozással szabványos módszert biztosít a felhőhöz termékek és szolgáltatások. Az Azure biztonsági és megfelelőségi terv nyújt útmutatást, hogy a Microsoft Azure data warehouse architektúrája, amely segít megvalósítani egy magas szintű FedRAMP vezérlőelemeket részét. Ez a megoldás telepítését és konfigurálását az Azure-erőforrások általános referenciaarchitektúra, bemutatásához, amelyben ügyfeleink megfelel bizonyos biztonsági és megfelelőségi követelmények módon útmutatást nyújt, és ismeretekkel szolgál az ügyfelek számára hozhat létre, és állítsa be a saját adattárház-megoldások az Azure-ban.

Ez a referenciaarchitektúra, társított vezérlőelem végrehajtási útmutatói és enyhítési célja, hogy az ügyfelek számára az adott követelményekhez beállítása alapjaként szolgálja ki, és nem használható-éles környezetben van. Erre a környezetre, módosítás nélkül az alkalmazás üzembe helyezése nem elegendő a teljesen felel meg a magas szintű FedRAMP-alapkonfiguráció követelményeinek. Vegye figyelembe a következőket:
- Az architektúra egy alapkonfiguráció segítenek az ügyfeleknek a számítási feladatok üzembe helyezéséhez az Azure FedRAMP-kompatibilis módon biztosít.
- Ügyfelek végző megfelelő biztonsági és megfelelőségi értékelést bármely megoldás használatával létrehozott ebben az architektúrában követelmények eltérőek lehetnek, hogy milyen ügyről van minden ügyfél megvalósítása alapján.

## <a name="architecture-diagram-and-components"></a>Architektúra és összetevők

A megoldás egy data warehouse referenciaarchitektúra egy nagy teljesítményű és biztonságos felhőalapú adattárház, amely biztosít. Ez az architektúra két külön szinten vannak: ahol importált adatokkal, az egyik tárolja, és egy fürtözött SQL-környezetben, és a egy másik készítve az Azure SQL Data warehouse, ha az adatok betöltése egy ETL-eszköz használatával (pl. [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase)T-SQL-lekérdezések) feldolgozás céljából. Miután az Azure SQL Data Warehouse tároljuk, analytics futtathat nagy méretű.

Az Azure különböző jelentéskészítési és elemzési szolgáltatások, az ügyfél számára kínál. Ez a megoldás gyors létrehozása a jelentések az Azure SQL Data Warehouse-ból az SQL Server Reporting Services (SSRS) tartalmaz. SQL forgalmat titkosítja a rendszer SSL-lel azáltal, önaláírt tanúsítványokat. Ajánlott eljárásként az Azure a fokozott biztonság egy megbízható hitelesítésszolgáltatótól használatát javasolja.

Az Azure SQL Data Warehouse az adatokat oszlopos szerkezetű jelentősen csökkenti a tárolási költségeket a lekérdezési teljesítmény javítása közben formátumú relációs tábláival tárolódik.  Használati követelményeitől függően Azure SQL Data Warehouse számítási erőforrások méretezése felfelé vagy lefelé vagy kapcsolja ki teljesen esetén nem igénylő számítási erőforrások nincs aktív folyamatok.

SQL terheléselosztó SQL forgalmi magas teljesítményt biztosítva kezeli. Ez a referenciaarchitektúra összes virtuális gépet egy rendelkezésre állási csoport magas rendelkezésre állású és vész-helyreállítási funkciók egy AlwaysOn rendelkezésre állási csoportban konfigurált SQL Server-példányok üzembe helyezhet.

A data warehouse referenciaarchitektúra egy Active Directory (AD) réteg az architektúra belüli erőforrások felügyeletéhez is tartalmaz. Az Active Directory-alhálózathoz lehetővé teszi, hogy könnyen bevezetési alatt egy nagyobb AD erdő struktúra, így folyamatos működése érdekében a környezetben, akkor is, ha a nagyobb erdőben való hozzáférés nem érhető el. Az összes virtuális gép tartományhoz csatlakoztatva az Active Directory-réteghez, és az Active Directory csoportházirend segítségével érvényesíti a biztonsági és megfelelőségi konfigurációk az operációs rendszer szintjén.

A virtuális gépek felügyeleti bástyagazdagép, biztonságos kapcsolatot biztosít a rendszergazdák erőforrások eléréséhez az üzembe helyezett funkcionál. Az adatok betöltődtek, a felügyeleti megerősített gazdagépen keresztül, az átmeneti területre. **Azure azt javasolja, hogy a VPN- vagy Azure ExpressRoute kapcsolat a felügyeleti és az adatok importálása a referencia architektúra alhálózatban.**

![Adatraktár-FedRAMP architekturális diagramja](images/fedramp-datawarehouse-architecture.png?raw=true "Data warehouse-ba, a FedRAMP architekturális diagramja")

Ez a megoldás a következő Azure-szolgáltatásokat használ. Az üzembe helyezési architektúra részletei a [üzembe helyezési architektúrája](#deployment-architecture) szakaszban.

Azure-alapú virtuális gépek
-   (1) Bástyagazdagép
-   (2) az active Directory-tartományvezérlő
-   (2) az SQL Server-fürt csomópontjának
-   (1) az SQL Server tanúsító

Rendelkezésre állási csoportok
-   (1) az active Directory-tartományvezérlők
-   (1) fürtcsomópontok az SQL és a tanúsító

Virtual Network
-   (4) alhálózatok
-   (4) a hálózati biztonsági csoportok

SQL Data Warehouse

SQL Server Reporting Services

Az Azure SQL-terheléselosztó

Azure Active Directory

Recovery Services-tároló

Azure Key Vault

Az Azure Monitor naplóira

## <a name="deployment-architecture"></a>Üzembe helyezési architektúrája

A következő szakasz az fejlesztése és megvalósítása elemek részletei.

**SQL Data Warehouse**: [Az SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) van egy vállalati adatok adattárház (EDW), amely ötvözi a nagymértékben párhuzamos feldolgozási (MPP) gyorsan több petabájtnyi adat az összetett lekérdezések futtatásához. Big Data típusú adatok importálása az SQL Data Warehouse-bA egyszerű PolyBase T-SQL-lekérdezések, és használja a MPP nyújtotta lehetőségeket kiaknázva nagy teljesítményű elemzési futtatásához.

**SQL Server Reporting Services**: [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) lehetővé teszi a táblák, diagramok, térképek, mérőműszer, mátrixokban és több Azure SQL Data Warehouse a jelentések gyors létrehozását.

**Bástyagazdagép**: A megerősített állomás bejegyzést, amely lehetővé teszi a felhasználóknak a ebben a környezetben üzembe helyezett erőforrásokhoz való hozzáférést a hibaérzékeny pont. A bástyagazdagép üzembe helyezett erőforrásokhoz való biztonságos kapcsolatot nyújt azáltal, hogy csak nyilvános IP-címekről érkező távoli forgalmat a biztonságos elemek listájához. Távoli asztali (RDP) forgalmat lehetővé teszik, hogy a forgalom forrását kell definiálni a hálózati biztonsági csoport (NSG).

Egy virtuális gépet egy tartományhoz csatlakoztatott bástyagazdagép az alábbi konfigurációkkal jött létre:
-   [A kártevőirtó bővítmény](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Log Analytics-bővítmény](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Az Azure Diagnostics bővítmény](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Az Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) az Azure Key Vaulttal (tiszteletben tartja az Azure Government, a PCI DSS, a HIPAA és egyéb követelmények)
-   Egy [automatikus leállítási házirend](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) csökkenthető a fogyasztása a virtuális gépi erőforrások, amikor nincs használatban
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) engedélyezve van, hogy a hitelesítő adatokat és egyéb titkos adatait, amely el van különítve a futó operációs rendszert a védett környezetben futnak

### <a name="virtual-network"></a>Virtuális hálózat
Ez a referenciaarchitektúra egy privát virtuális hálózatot a 10.0.0.0/16 címteret az határozza meg.

**Hálózati biztonsági csoportok**: [Az NSG-k](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) tartalmazza a hozzáférés-vezérlési listák (ACL), amelyek engedélyezik vagy megtagadják a forgalmat a Vneten belül. Az NSG-ket egy alhálózatot vagy az egyes Virtuálisgép-szintű forgalom védelmére használható. Az alábbi NSG-k léteznek:
  - Egy NSG-t (SQL Server-fürtöket, az SQL Server tanúsító és SQL terheléselosztó) az Adatréteg számára
  - A felügyeleti bástyagazdagép az NSG-t
  - Egy NSG-t az Active Directory
  - Egy NSG-t az SQL Server Reporting Services

Az NSG-k mindegyike rendelkezik az adott portokhoz és protokollokhoz meg, hogy a megoldás működhet, biztonságos és megfelelően. Emellett a következő konfigurációk engedélyezve vannak az egyes NSG:
  - [Diagnosztikai naplók és események](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) engedélyezve van, és a storage-fiókban tárolt
  - Az Azure Monitor naplóira csatlakozik a [NSG-t a diagnosztika](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Alhálózatok**: Minden egyes alhálózathoz társítva a megfelelő NSG-t.

### <a name="data-at-rest"></a>Inaktív adat
Az architektúra a titkosítás, az adatbázis naplózási és más intézkedéseket az inaktív adatok védi.

**Az Azure Storage** rest-követelményeket, a titkosított adatok kielégítése érdekében minden [Azure Storage](https://azure.microsoft.com/services/storage/) használ [a Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption).

**Az Azure Disk Encryption**
[az Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) az operációs rendszer és az adatlemezek kötettitkosítást biztosít a Windows BitLocker funkcióját használja. A megoldás integrálható az Azure Key Vault segítségével szabályozhatja, és kezelhetik a lemeztitkosítási kulcsokat.

**Az Azure SQL Database** az Azure SQL Database-példányt használja a következő adatbázis biztonsági intézkedéseket:
-   [AD-hitelesítés és engedélyezés](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) lehetővé teszi az identitáskezelést adatbázis-felhasználók és más Microsoft-szolgáltatások egyetlen központi helyen.
-   [Az SQL database naplózási szolgáltatásával](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) nyomon követi az adatbázisok eseményeit és felvezeti ezeket egy naplófájlba, egy Azure storage-fiókban.
-   SQL-adatbázis használatára van konfigurálva [transzparens adattitkosítási (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), amely végrehajtja az adatok inaktív védelme adathoz és naplófájlhoz valós idejű titkosításához és visszafejtéséhez. TDE biztosítja, hogy a tárolt adatok nem lett bárki hozzáférhet.
-   [Tűzfalszabályok](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) megakadályozzák elérését, adatbázis-kiszolgálók, amíg a megfelelő engedélyekkel. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján.
-   [Az SQL Fenyegetésészlelési](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) lehetővé teszi, hogy az észlelés és válasz a lehetséges veszélyforrásokra, bekövetkezésük pillanatában észlelhessék a biztonsági riasztásokat a gyanús adatbázis-tevékenységekről, a lehetséges biztonsági résekről, az SQL-injektálási támadások és a rendellenes adatbázis-hozzáférés minták.
-   [Always Encrypted az oszlopok](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) győződjön meg arról, hogy bizalmas adatok soha nem jelenik meg, mint belül az adatbázis-rendszer egyszerű szövegként. Adatok titkosításának engedélyezése után csak az ügyfélalkalmazások vagy alkalmazáskiszolgálók hozzáférést a kulcsokhoz hozzáférhet egyszerű szöveges adatokat.
-   [Az SQL Database dinamikus adatmaszkolása](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) teheti, miután a referenciaarchitektúra üzembe helyezte. Ügyfelek módosítania kell a dinamikus adatmaszkolási beállítások be kell tartaniuk az adatbázissémát.

### <a name="business-continuity"></a>Az üzletmenet folytonossága
**Magas rendelkezésre állású**: Server számítási feladatainak szerint vannak csoportosítva egy [rendelkezésre állási csoport](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) az Azure-beli virtuális gépek magas rendelkezésre állás biztosítása érdekében. Legalább egy virtuális gép érhető el egy tervezett vagy nem tervezett karbantartási események esetén teljesíti a 99,95 %-os Azure SLA-t.

**Recovery Services-tároló**: A [Recovery Services-tároló](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) Kezelőkód biztonsági mentési adatokat, és védelmet biztosít az összes konfiguráció az Azure Virtual Machines ebben az architektúrában. Recovery Services-tárolóval, ügyfelek is fájlok és mappák visszaállítása az IaaS virtuális gépről a teljes virtuális Gépet, így gyorsabb helyreállítást visszaállítása nélkül.

### <a name="logging-and-audit"></a>Naplózás és naplózása
[Az Azure Monitor naplóira](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) biztosít széles körű naplózását, a rendszer és a felhasználói tevékenységek, valamint a helyrendszer állapotát. A [naplózza az Azure Monitor](https://azure.microsoft.com/services/log-analytics/) megoldás gyűjti és elemzi az adatokat az Azure-erőforrások által létrehozott és a helyszíni környezetekben.
- **A Tevékenységnaplók**: [A Tevékenységnaplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) adjon meg egy előfizetéshez tartozó erőforrásokon végrehajtott műveletekkel kapcsolatos információk.
- **Diagnosztikai naplók**: [Diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) minden erőforrás által kibocsátott az összes napló tartalmazza. Ezek a naplók a Windows rendszer-eseménynaplói és az Azure Blob storage, táblák és üzenetsor-naplók tartalmazzák.
- **Tűzfal-naplók**: Az Application Gateway teljes diagnosztikai biztosít, és hozzáférni a naplókhoz. Az Application Gateway WAF-kompatibilis erőforrások tűzfalnaplók érhetők el.
- **Napló Archiválás**: Az összes diagnosztikai naplók írni egy központosított, titkosított csatornákon történik az Azure storage-fiókját archiválási 2 nap meghatározott megőrzési idővel rendelkező. Ezek a naplók csatlakozhat az Azure Monitor naplóira feldolgozásához, tárolásához és-irányítópult jelentéseit.

Ezenkívül a következő figyelési megoldások jelennek meg, ez az architektúra részeként:
-   [Az AD Assessmenthez](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Az Active Directory állapotának ellenőrzése megoldás a kockázat és kiszolgálói környezetek állapotát értékeli a rendszeres időközönkénti, és a telepített kiszolgálói infrastruktúra vonatkozó javaslatok rangsorolt listáját tartalmazza.
-   [Kártevőirtók felmérése](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): A kártevőirtó megoldás jelentések a kártevő szoftverek, a fenyegetések és a védelem állapota.
-   [Az Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): Az Azure Automation-megoldás tárolja, fut, és kezeli a runbookok.
-   [Biztonság és auditálás](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): A biztonsági és auditálási irányítópultja az erőforrások biztonsági állapotát egy magas szintű betekintést nyújt a metrikák azáltal, hogy a biztonsági tartományok, a jelentős problémák, a észlelések, a fenyegetésekkel kapcsolatos Tudásbázis és a gyakori biztonsági lekérdezések.
-   [SQL-értékeléssel](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): Az SQL Health Check megoldás a kockázat és kiszolgálói környezetek állapotát értékeli a rendszeres időközönkénti, és nyújt a felhasználók számára a telepített kiszolgálói infrastruktúra vonatkozó javaslatok rangsorolt listáját.
-   [Frissítéskezelés](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): Az Update Management megoldás lehetővé teszi, hogy az ügyfél felügyeleti operációs rendszer biztonsági frissítéseket, beleértve egy elérhető frissítések állapotát és a szükséges frissítések telepítése.
-   [Az ügynök állapota](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Az Agent Health megoldás hány ügynök van telepítve, és a földrajzi elosztás, valamint hány ügynök, amely nem válaszol és működési adatokat küld be, amely az ügynökök számát jelenti.
-   [Azure-Tevékenységnaplók](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Az Activity Log Analytics megoldás az ügyfél az összes Azure-előfizetések az Azure-Tevékenységnaplók elemzésének segíti.
-   [A változáskövetés](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): A Change Tracking megoldás lehetővé teszi, hogy az ügyfelek számára, hogy könnyen azonosíthassa a változtatásokat a környezetben.

### <a name="identity-management"></a>Identitáskezelés
A következő technológiákat biztosítja identitás az eszközkezelési funkciókat az Azure-környezetben:
-   [Az Active Directory (AD)](https://azure.microsoft.com/services/active-directory/) lehet a Microsoft több-bérlős felhőalapú címtár- és identitáskezelési szolgáltatás. A megoldás összes felhasználó jöttek létre az Azure Active Directoryban, beleértve a felhasználók az SQL-adatbázis elérésére.
-   Az alkalmazás-hitelesítést az Azure AD használatával történik. További információkért lásd: [alkalmazások integrálása az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Ezenkívül az adatbázis-oszlop titkosítás az Azure AD segítségével hitelesítheti az alkalmazást az Azure SQL Database. További információkért lásd: hogyan [bizalmas adatok védelme az SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Az Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) észleli a szervezet identitásait érintő lehetséges biztonsági résekről, konfigurálja az automatikus reakciók, a szervezet identitásait kapcsolatos észlelt gyanús tevékenységeket és folytat gyanús incidensek a megfelelő műveletet a problémák megoldásához.
-   [Az Azure szerepköralapú hozzáférés-vezérlés (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) a fókuszban lévő lehetővé teszi, hogy hozzáférés-kezelés az Azure-hoz. Az előfizetés-rendszergazda előfizetéshez való hozzáférés korlátozódik.

Az Azure SQL Database biztonsági szolgáltatásaival kapcsolatos további információkért tekintse meg a [Contoso Clinic Bemutatóalkalmazásában](https://github.com/Microsoft/azure-sql-security-sample) minta.

### <a name="security"></a>Biztonság
**Titkok kezelése**: A megoldás [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) kulcsok és titkos kulcsok felügyeletéhez. Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében.

**Kártevők elleni védekezés**: [A Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) tartozó virtuális gépeket biztosít, amellyel a vírusok, kémprogramok és más kártevők azonosításában és valós idejű védelem funkció, konfigurálható riasztásokkal, ha ismert kártevő vagy nemkívánatos szoftver megkísérli Telepítse, vagy a védett virtuális gépek futtatásához.

**Javítások kezelése**: Ez a referenciaarchitektúra részeként üzembe helyezett Windows virtuális gépek automatikusan frissítéseket kapjanak a Windows Update szolgáltatás alapértelmezés szerint vannak konfigurálva. Ez a megoldás is magában foglalja a [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) szolgáltatást, amelyen frissített telepítések hozható létre a javítás virtuális gépekhez szükség esetén.


## <a name="guidance-and-recommendations"></a>Útmutatás és javaslatok
### <a name="expressroute-and-vpn"></a>Az ExpressRoute és VPN
[Az ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy a biztonságos VPN-alagutat kell konfigurálni, hogy biztonságos kapcsolatot a data warehouse referenciaarchitektúra részeként üzembe helyezett erőforrásokkal. Az ExpressRoute-kapcsolatok az interneten keresztül halad, mivel a kapcsolatok az interneten keresztül ajánlja fel a további megbízhatóság megbízhatóbbak, gyorsabbak, kisebb a késésük, és biztonságosabbak a szokásos internetkapcsolatoknál. Megfelelően állítja be az expressroute-on vagy VPN, az ügyfelek hozzáadhatja egy védelmi réteget biztosít adatokat átvitel közben.

### <a name="extract-transform-load-etl-process"></a>Kinyerési, átalakítási-betöltési (ETL) folyamat
[A PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) adatok betöltése az Azure SQL Data Warehouse-bA egy külön ETL nélkül vagy eszköz importálása. A PolyBase lehetővé teszi, hogy a T-SQL-lekérdezések adatokhoz való hozzáférését. A Microsoft üzleti intelligencia és elemzés verem, valamint kompatibilis SQL Server, a harmadik felektől származó eszközök a polybase-zel használható.

### <a name="azure-active-directory-setup"></a>Az Azure Active Directory beállítása
[Az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) elengedhetetlen a központi telepítés kezelése és kiépítése a környezet implementálására csoporthoz való hozzáférést. Az AAD-ben meglévő Windows Server Active Directory integrálhatók [négy kattintással](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Ügyfelek egy meglévő AAD-be való elősegítsék a telepített Active Directory-infrastruktúrát (tartományvezérlők) is, azáltal, hogy a telepített Active Directory-infrastruktúra egy AAD-erdőhöz tartozó altartományban.

### <a name="additional-services"></a>További szolgáltatások
Bár a data warehouse architektúrája nem célja való üzembe helyezéshez a [Azure Commercial](https://azure.microsoft.com/overview/what-is-azure/) környezet, hasonló célokkal lehet elérni, ez a referenciaarchitektúra és a szolgáltatások, valamint További szolgáltatások csak az Azure kereskedelmi környezetben érhető el. Vegye figyelembe, hogy az Azure kereskedelmi fenntartja a FedRAMP JAB P-ATO szinten mérsékelt hatás, lehetővé téve a kormányzati szerveknek és a partnerek számára, Közepesen bizalmas adatokat a felhőben, kihasználva az Azure kereskedelmi környezetben üzembe helyezni.

Az Azure kereskedelmi ajánlatokat, számos különböző szolgáltatások, hogy formázva leíró és formázatlan adattárolási és -előkészítés, adattárházak, használható többek között:
-   [Az Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) egy felügyelt felhőszolgáltatás, amely a hibrid kinyerési, átalakítási-betöltési (ETL), a kivonat-terhelés-átalakítási (ELT), és adatintegrációs projektek. Azure Data Factoryt használó ügyfelek létrehozhatók és ütemezhetők a nevű folyamatok különböző adattárolókból adatokat beolvasó adatvezérelt munkafolyamatok. Ügyfelek, majd fel, és a kimeneti adatok átalakítása adattárakban, mint az Azure SQL Data warehouse-bA.
-   [Az Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) lehetővé teszi mindenféle méretű, típusú és feldolgozási sebességű egy egy helyen történő műveleti és felderítési jellegű adatok rögzítését. Az Azure Data Lake Store a Hadoop-ökoszisztéma legtöbb nyílt forráskódú összetevőjével kompatibilis, és jól integrálható más Azure-szolgáltatások például az Azure SQL Data warehouse-bA.

## <a name="threat-model"></a>Fenyegetések modellezése

A data folyamat diagramja (DFD) Ez a referenciaarchitektúra érhető el az [letöltése](https://aka.ms/blueprintdwthreatmodel) vagy alább tekintheti meg:

![Adatraktár-FedRAMP fenyegetések modellezése](images/fedramp-datawarehouse-threat-model.png?raw=true "Data warehouse-ba, a FedRAMP fenyegetések modellezése")

## <a name="compliance-documentation"></a>Megfelelőségi dokumentációja
A [Azure biztonsági és megfelelőségi terv – FedRAMP magas vevő felelőssége mátrix](https://aka.ms/blueprinthighcrm) sorolja fel a magas szintű FedRAMP-alapkonfiguráció által igényelt összes biztonsági vezérlő. Ehhez hasonlóan a [Azure biztonsági és megfelelőségi terv – FedRAMP mérsékelt vevő felelőssége mátrix](https://aka.ms/blueprintcrmmod) sorolja fel a FedRAMP mérsékelt alapkonfiguráció által igényelt összes biztonsági vezérlő. Mind a dokumentumok bemutatják, hogy az egyes vezérlőelemek végrehajtása feladata a Microsoft, az ügyfél vagy a kettő között megosztott.

A [Azure biztonsági és -megfelelőségi terv - FedRAMP magas ellenőrzési megvalósítási mátrix](https://aka.ms/blueprintdwcimhigh) és a [Azure biztonsági és -megfelelőségi terv - FedRAMP mérsékelt vezérlő megvalósítási mátrix](https://aka.ms/blueprintdwcimmod) megadása az adatokat, amelyen a vezérlők esnek a data warehouse architektúrája FedRAMP minden egyes referenciakonfigurációnál, beleértve a részletes leírását, hogyan végrehajtása megfelel egyes az érintett vezérlő.

## <a name="disclaimer"></a>Jogi nyilatkozat

 - Ez a dokumentum csak tájékoztatási célokat szolgál. A MICROSOFT NEM VÁLLAL SEMMILYEN KIFEJEZETT, KIFEJEZETT, VÉLELMEZETT VAGY KÖTELEZŐ GARANCIÁT A DOKUMENTUMBAN SZEREPLŐ INFORMÁCIÓRA VONATKOZÓAN. Ez a dokumentum biztosított "as-van." Információk és vélemények ebben a dokumentumban URL-CÍMEK és más internetes webhelyekre utaló hivatkozások értesítés nélkül változhatnak. Ez a dokumentum olvasásakor ügyfelek felelősségére használja.
 - Ez a dokumentum nem biztosít semmilyen jogot semmilyen Microsoft-termék vagy a megoldások a szellemi tulajdonhoz ügyfelek.
 - Ügyfelek másolhatja és használhatja ezt a dokumentumot belső, hivatkozási célokból.
 - Bizonyos ajánlások a dokumentum eredményezhet megnövekedett adat-, hálózati vagy számítási erőforrás-használat az Azure-ban, és előfordulhat, hogy növelje az ügyfél Azure licencek vagy előfizetések költségeit.
 - Ez az architektúra célja, hogy az ügyfelek számára az adott követelményekhez beállítása alapjaként szolgálja ki, és nem használható-éles környezetben van.
 - Ez a dokumentum referenciaként fejlesztik, és nem használható minden olyan eszközt, amely szerint egy ügyfél megfelel a megadott megfelelőségi követelmények és előírások meghatározásához. Ügyfelek jóváhagyott megvalósítás az ügyfeleknél a szervezet jogi támogatást kell kérnie.
