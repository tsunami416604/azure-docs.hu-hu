---
title: Azure Security and Compliance Blueprint – adattárház a FedRAMP Automation szolgáltatáshoz
description: Azure Security and Compliance Blueprint – adattárház a FedRAMP Automation szolgáltatáshoz
services: security
author: jomolesk
ms.assetid: 834d1ff6-8369-455f-b052-1ef301e3d7e6
ms.service: security
ms.topic: article
ms.date: 05/02/2018
ms.author: jomolesk
ms.openlocfilehash: ada041640cb66f756f8976fa5290592f11ff1cad
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/05/2019
ms.locfileid: "68778915"
---
# <a name="azure-security-and-compliance-blueprint-data-warehouse-for-fedramp-automation"></a>Azure Security and Compliance Blueprint: Adattárház a FedRAMP Automation szolgáltatáshoz

## <a name="overview"></a>Áttekintés

A [Federal Risk and Authorization Management program (FedRAMP)](https://www.fedramp.gov/) egy olyan Egyesült Államok kormányzati szintű program, amely szabványosított megközelítést biztosít a biztonság értékeléséhez, engedélyezéséhez és a felhőalapú termékek és szolgáltatások folyamatos monitorozásához. Ez a Azure Security and Compliance Blueprint útmutatást nyújt egy olyan Microsoft Azure adattárház-architektúra megvalósításához, amely segít a FedRAMP magas szintű vezérlők részhalmazának megvalósításában. Ez a megoldás útmutatást nyújt az Azure-erőforrások általános hivatkozási architektúrához való üzembe helyezéséhez és konfigurálásához, és bemutatja, hogy az ügyfelek milyen módon tudják kielégíteni a konkrét biztonsági és megfelelőségi követelményeket, és alapként szolgálnak az ügyfelek számára saját adattárház-megoldásaikat hozhat létre és konfigurálhat az Azure-ban.

Ez a hivatkozási architektúra, a társított vezérlés megvalósítási útmutatói és a veszélyforrási modellek arra szolgálnak, hogy az ügyfelek számára alapvető fontosságúak legyenek, és ne használják éles környezetben. Ha módosítás nélkül helyezi üzembe az alkalmazást a környezetbe, nem elegendő a FedRAMP magas alapkonfigurációjának követelményeinek teljes kielégítéséhez. Vegye figyelembe a következőket:
- Az architektúra olyan alapkonfigurációt biztosít, amellyel az ügyfelek FedRAMP-kompatibilis módon telepíthetik az Azure-beli számítási feladatokat.
- Az ügyfelek felelősek az architektúrával létrehozott megoldások megfelelő biztonsági és megfelelőségi felmérésének elvégzéséért, mivel a követelmények az egyes ügyfelek implementációjának sajátosságai alapján változhatnak.

## <a name="architecture-diagram-and-components"></a>Architektúra ábrája és összetevői

Ez a megoldás egy adattárház-hivatkozási architektúrát biztosít, amely nagy teljesítményű és biztonságos felhőalapú adattárházat valósít meg. Ebben az architektúrában két különálló adatréteg található: az egyik, hogy az adatok importálása, tárolása és előkészítése fürtözött SQL-környezetben történik, és egy másik a Azure SQL Data Warehouse, ahol az adatok egy ETL eszközzel töltődnek be (például albase T [](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase) -SQL-lekérdezések) feldolgozásra. Ha az adattárolást Azure SQL Data Warehouse tárolja, az elemzések nagy léptékben futhatnak.

Microsoft Azure számos jelentéskészítési és elemzési szolgáltatást kínál az ügyfelek számára. Ez a megoldás SQL Server Reporting Services (SSRS) szolgáltatást tartalmaz a jelentések gyors létrehozásához a Azure SQL Data Warehouse. Az összes SQL-forgalom SSL-titkosítással rendelkezik az önaláírt tanúsítványok belefoglalásával. Az ajánlott eljárás az, hogy az Azure megbízható hitelesítésszolgáltató használatát javasolja a fokozott biztonság érdekében.

A Azure SQL Data Warehouseban tárolt adat az oszlopos tárolással rendelkező, rokon táblákban tárolódik, amely jelentősen csökkenti az adattárolási költségeket, miközben javítja a lekérdezési teljesítményt.  A használati követelményektől függően Azure SQL Data Warehouse számítási erőforrások vertikálisan fel-vagy leállíthatók, vagy teljesen kikapcsolhatók, ha nincsenek olyan aktív folyamatok, amelyek számítási erőforrásokat igényelnek.

Az SQL Load Balancer az SQL-forgalmat felügyeli, és nagy teljesítményt biztosít. Az ebben a hivatkozási architektúrában található összes virtuális gép egy rendelkezésre állási csoportba helyez üzembe egy AlwaysOn rendelkezésre állási csoportban konfigurált SQL Server példányokkal a magas rendelkezésre állást és a vész-helyreállítási képességeket.

Ez az adattárház-hivatkozási architektúra az architektúrán belüli erőforrások kezelésére szolgáló Active Directory (AD) szintet is tartalmaz. Az Active Directory alhálózat lehetővé teszi, hogy az egyszerű bevezetést egy nagyobb AD-erdő struktúrája alapján engedélyezze, ami lehetővé teszi a környezet folyamatos működését akkor is, ha a nagyobb erdőhöz való hozzáférés nem érhető el. Az összes virtuális gép tartományhoz van csatlakoztatva a Active Directory réteghez, és Active Directory Csoportházirendek használatával kényszeríti ki a biztonsági és megfelelőségi konfigurációkat az operációs rendszer szintjén.

A virtuális gépek felügyeleti megerősített gazdagépként szolgálnak, és biztonságos kapcsolatot biztosítanak a rendszergazdák számára az üzembe helyezett erőforrások eléréséhez. Az adattárolási terület a felügyeleti megerősített gazdagépen keresztül töltődik be. **Az Azure javasolja a VPN-vagy Azure ExpressRoute-kapcsolat konfigurálását a felügyeleti és adatimportálási szolgáltatásokhoz a hivatkozási architektúra alhálózatában.**

![Adattárház a FedRAMP hivatkozási architektúrájának diagramja](images/fedramp-datawarehouse-architecture.png?raw=true "Adattárház a FedRAMP hivatkozási architektúrájának diagramja")

Ez a megoldás az alábbi Azure-szolgáltatásokat használja. Az üzembe helyezési architektúra részletei az [üzembe helyezési architektúra](#deployment-architecture) szakaszban találhatók.

Azure Virtual Machines
-   (1) megerősített állomás
-   (2) Active Directory tartományvezérlő
-   (2) SQL Server fürtcsomópont
-   (1) SQL Server tanúsító

Rendelkezésre állási csoportok
-   (1) Active Directory tartományvezérlők
-   (1) SQL-fürtcsomópontok és-tanúsító

Virtuális hálózat
-   (4) alhálózatok
-   (4) hálózati biztonsági csoportok

SQL Data Warehouse

SQL Server Reporting Services

Azure SQL Load Balancer

Azure Active Directory

Recovery Services-tároló

Azure Key Vault

Azure Monitor-naplók

## <a name="deployment-architecture"></a>Üzembe helyezési architektúra

A következő szakasz részletesen ismerteti a fejlesztési és megvalósítási elemeket.

**SQL Data Warehouse**: A [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) egy nagyvállalati adattárház (EDW), amely nagymértékben párhuzamos feldolgozást (MPP) használ, hogy gyorsan futtasson összetett lekérdezéseket az egyes petabájt. Importálja big datat a SQL Data Warehouseba egyszerű, a T-SQL-lekérdezésekkel, és használja az MPP hatékonyságát a nagy teljesítményű elemzések futtatásához.

**SQL Server Reporting Services**: [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) lehetővé teszi a jelentések gyors létrehozását táblázatok, diagramok, térképek, mérőműszerek, mátrixok és más Azure SQL Data Warehouseekhez.

**Megerősített gazdagép**: A megerősített gazdagép az egyetlen belépési pont, amely lehetővé teszi, hogy a felhasználók hozzáférhessenek az üzembe helyezett erőforrásokhoz ebben a környezetben. A megerősített gazdagép biztonságos kapcsolódást biztosít a központilag telepített erőforrásokhoz azáltal, hogy csak a nyilvános IP-címekről érkező távoli forgalmat engedélyezi biztonságos listán. A távoli asztali (RDP-) forgalom engedélyezéséhez meg kell határozni a forgalom forrását a hálózati biztonsági csoportban (NSG).

A virtuális gép tartományhoz csatlakozó megerősített gazdagépként lett létrehozva a következő konfigurációkkal:
-   [Antimalware-bővítmény](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
-   [Azure Monitor naplók bővítmény](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Azure Diagnostics bővítmény](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) Azure Key Vault használatával (tiszteletben Azure Government, PCI DSS, HIPAA és egyéb követelmények)
-   [Automatikus leállítási szabályzat](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) , amely csökkenti a virtuális gépek erőforrásainak felhasználását, ha nincs használatban
-   A [Windows Defender hitelesítőadat](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) -védelme engedélyezve van, hogy a hitelesítő adatok és egyéb Titkok a futó operációs rendszertől elkülönített védett környezetekben fussanak.

### <a name="virtual-network"></a>Virtuális hálózat
Ez a hivatkozási architektúra a 10.0.0.0/16 címtartomány szerinti magánhálózati virtuális hálózatot definiálja.

**Hálózati biztonsági csoportok**: A [NSG](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) olyan Access Control listákat (ACL-eket) tartalmaznak, amelyek engedélyezik vagy megtagadják a forgalmat a VNet belül. A NSG használatával biztonságossá teheti a forgalmat egy alhálózaton vagy egy adott virtuálisgép-szinten. A következő NSG léteznek:
  - Az adatcsomag NSG (SQL Server fürtök, SQL Server tanúsító és SQL Load Balancer)
  - Egy NSG a felügyeleti megerősített gazdagéphez
  - Active Directory NSG
  - SQL Server Reporting Services NSG

Mindegyik NSG meg van nyitva bizonyos portok és protokollok, hogy a megoldás biztonságosan és megfelelően működjön. Emellett a következő konfigurációk is engedélyezve vannak az egyes NSG:
  - A [diagnosztikai naplók és események](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) engedélyezve vannak, és Storage-fiókban tárolódnak
  - Azure Monitor naplók a [NSG diagnosztikaához](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json) csatlakoznak

Alhálózatok: Minden alhálózat társítva van a hozzá tartozó NSG.

### <a name="data-at-rest"></a>Inaktív adat
Az architektúra titkosítva, adatbázis-naplózással és egyéb mértékekkel védi a nyugalmi állapotban lévő adatok védelmét.

**Azure Storage** A titkosított adatoknak a nyugalmi követelmények teljesítése érdekében az összes [Azure Storage](https://azure.microsoft.com/services/storage/) [Storage Service encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption)használ.

AzureDiskEncryption
[Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) a Windows BitLocker szolgáltatásával biztosítja a kötetek titkosítását az operációs rendszer és az adatlemezek számára. A megoldás integrálva van Azure Key Vaultekkel a lemezes titkosítási kulcsok szabályozása és kezelése érdekében.

**Azure SQL Database** A Azure SQL Database példány a következő adatbázis-biztonsági mértékeket használja:
-   Az [AD-hitelesítés és-engedélyezés](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) lehetővé teszi az adatbázis-felhasználók és más Microsoft-szolgáltatások identitásának kezelését egy központi helyen.
-   Az [SQL Database naplózása](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) nyomon követi az adatbázis eseményeit, és egy Azure Storage-fiókban lévő naplóba írja azokat.
-   A SQL Database [transzparens adattitkosítás (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)használatára van konfigurálva, amely az adatok és naplófájlok valós idejű titkosítását és visszafejtését hajtja végre a további információk védelme érdekében. A TDE biztosítja, hogy a tárolt adataik nem érvényesek a jogosulatlan hozzáférésre.
-   A [Tűzfalszabályok](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) megakadályozzák az adatbázis-kiszolgálók hozzáférését a megfelelő engedélyek megadása előtt. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján.
-   Az [SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) -veszélyforrások észlelése lehetővé teszi az észlelést és a reagálást a potenciális fenyegetésekre, mivel ezek a hibák a gyanús adatbázis-tevékenységek, a potenciális sebezhetőségek, az SQL-injektálási támadások és a rendellenes adatbázis-hozzáférési minták esetében
-   [Always encrypted oszlopok](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) biztosítják, hogy a bizalmas adatok soha ne jelenjenek meg egyszerű szövegként az adatbázis-rendszeren belül. Az adattitkosítás engedélyezése után csak az ügyfélalkalmazások vagy az alkalmazások férhetnek hozzá a kulcsokhoz.
-   [SQL Database a dinamikus adatmaszkolás](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) a hivatkozási architektúra telepítése után végezhető el. Az ügyfeleknek módosítaniuk kell a dinamikus adatmaszkolási beállításokat az adatbázis-sémájuk betartásához.

### <a name="business-continuity"></a>Az üzletmenet folytonossága
**Magas rendelkezésre állás**: A kiszolgálói munkaterhelések egy [rendelkezésre állási](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) csoportba vannak csoportosítva, így biztosítva az Azure-beli virtuális gépek magas rendelkezésre állását. A tervezett vagy nem tervezett karbantartási események során legalább egy virtuális gép elérhető a 99,95%-os Azure SLA-val.

**Recovery Services**tároló: Az [Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) -tároló a biztonsági mentési és az Azure-Virtual Machines összes konfigurációját védi ebben az architektúrában. Recovery Services-tárolóval az ügyfelek a teljes virtuális gép visszaállítása nélkül állíthatják vissza a fájlokat és mappákat egy IaaS virtuális gépről, így a gyorsabb visszaállítási idő is megadható.

### <a name="logging-and-audit"></a>Naplózás és naplózás
[Azure monitor naplók](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) széles körű naplózást biztosítanak a rendszer és a felhasználók tevékenységéről, valamint a rendszer állapotáról. A [Azure monitor naplók](https://azure.microsoft.com/services/log-analytics/) megoldás az Azure-ban és a helyszíni környezetekben található erőforrások által generált adatokat gyűjti és elemzi.
- **Tevékenységek naplói**: A [tevékenységek naplói](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) betekintést nyújtanak az előfizetésben lévő erőforrásokon végrehajtott műveletekre.
- **Diagnosztikai naplók**: A [diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) az összes erőforrás által kibocsátott összes naplót tartalmazzák. Ezek a naplók a Windows-események rendszernaplóit és az Azure Blob Storage, Tables és üzenetsor-naplókat tartalmazzák.
- **Tűzfal naplófájljai**: A Application Gateway teljes körű diagnosztikai és hozzáférési naplókat biztosít. A WAF-kompatibilis Application Gateway erőforrásai számára elérhetők a tűzfalak.
- **Napló archiválása**: Az összes diagnosztikai napló egy központi és titkosított Azure Storage-fiókba írja az archiválást, amely a megadott megőrzési időtartam 2 nap. Ezek a naplók Azure Monitor naplókat csatlakoznak a feldolgozáshoz, tároláshoz és irányítópult-jelentéskészítéshez.

Emellett az alábbi figyelési megoldások is részét képezik ennek az architektúrának:
-   [Ad Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): A Active Directory Health-ellenőrzési megoldás rendszeres időközönként kivizsgálja a kiszolgálói környezetek kockázatait és állapotát, és rangsorolja a telepített kiszolgálói infrastruktúrára jellemző ajánlásokat.
-   [Antimalware Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): Az antimalware-megoldás a kártevők, fenyegetések és védelem állapotáról nyújt jelentéseket.
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): A Azure Automation megoldás tárolja, futtatja és felügyeli a runbookok.
-   [Security and Audit](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): A Security and Audit irányítópult magas szintű betekintést nyújt az erőforrások biztonsági állapotára azáltal, hogy mérőszámokat biztosít a biztonsági tartományokra, a jelentős problémákra, az észlelésekre, a fenyegetések felderítésére és az általános biztonsági lekérdezésekre.
-   [SQL Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): Az SQL Health-ellenőrzési megoldás rendszeres időközönként kivizsgálja a kiszolgálói környezetek kockázatait és állapotát, és az ügyfelek számára az üzembe helyezett kiszolgáló-infrastruktúrára jellemző ajánlások rangsorolt listáját biztosítja.
-   [Update Management](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): Az Update Management megoldás lehetővé teszi az operációs rendszer biztonsági frissítéseinek az ügyfelek általi felügyeletét, beleértve az elérhető frissítések állapotát, valamint a szükséges frissítések telepítésének folyamatát.
-   [Agent Health](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Az Agent Health-megoldás jelentést készít, hogy hány ügynök van üzembe helyezve, valamint a földrajzi eloszlásuk, valamint a nem válaszoló ügynökök száma, valamint az operatív adatküldés alatt álló ügynökök száma.
-   [Azure-tevékenység naplói](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Az Activity Log Analytics-megoldás segítséget nyújt az Azure-tevékenységek naplóinak elemzéséhez az ügyfelek összes Azure-előfizetése között.
-   [Change Tracking](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): A Change Tracking megoldás lehetővé teszi, hogy az ügyfelek könnyedén azonosítsák a környezet változásait.

### <a name="identity-management"></a>Identitáskezelés
Az alábbi technológiák az Azure-környezet Identitáskezelés-kezelési funkcióit biztosítják:
-   [Active Directory (ad)](https://azure.microsoft.com/services/active-directory/) a Microsoft több-bérlős felhőalapú címtár-és Identitáskezelés-kezelő szolgáltatása lehet. A megoldás összes felhasználója Azure Active Directory-ban lett létrehozva, beleértve a SQL Databasehoz hozzáférő felhasználókat is.
-   Az alkalmazáshoz való hitelesítés az Azure AD használatával történik. További információ: [alkalmazások integrálása a Azure Active Directorysal](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Emellett az adatbázis oszlopának titkosítása az Azure AD használatával hitelesíti az alkalmazást Azure SQL Database. További információ: a [bizalmas adatok védelme SQL Databaseban](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) észleli a szervezet identitásait érintő lehetséges biztonsági réseket, automatikus válaszokat konfigurál a szervezet identitásával kapcsolatos gyanús műveletekre, és megvizsgálja a gyanús incidensek, hogy megtegye a megfelelő lépéseket a megoldásához.
-   Az [Azure szerepköralapú Access Control (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) lehetővé teszi a célzott hozzáférés-kezelést az Azure-hoz. Az előfizetés-hozzáférés az előfizetés rendszergazdájára korlátozódik.

Ha többet szeretne megtudni a Azure SQL Database biztonsági funkcióinak használatáról, tekintse meg a [contoso Clinic bemutató alkalmazás](https://github.com/Microsoft/azure-sql-security-sample) mintáját.

### <a name="security"></a>Biztonság
**Titkok kezelése**: A megoldás a kulcsok és titkok kezeléséhez [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) használ. Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében.

**Kártevők elleni védelem**: A Virtual Machines rendszerhez készült [Microsoft antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware) olyan valós idejű védelmi képességet biztosít, amely lehetővé teszi a vírusok, kémprogramok és más kártékony szoftverek azonosítását és eltávolítását, amelyekkel konfigurálható riasztások állíthatók be, ha az ismert kártékony vagy nemkívánatos szoftverek megkísérlik Telepítsen vagy futtasson védett virtuális gépeken.

**Javítási felügyelet**: Az ebben a hivatkozási architektúrában üzembe helyezett Windows-beli virtuális gépek alapértelmezés szerint úgy vannak konfigurálva, hogy Windows Update szolgáltatásból fogadják az automatikus frissítéseket. Ez a megoldás tartalmazza azt a [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) szolgáltatást is, amelyen keresztül a frissített központi telepítések létrehozhatók a virtuális gépek szükség szerinti javításához.


## <a name="guidance-and-recommendations"></a>Útmutatás és javaslatok
### <a name="expressroute-and-vpn"></a>ExpressRoute és VPN
A [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy a biztonságos VPN-alagutat úgy kell konfigurálni, hogy biztonságosan létesítsen kapcsolatot az adattárház-hivatkozási architektúra részeként üzembe helyezett erőforrásokkal. Mivel a ExpressRoute-kapcsolatok nem az interneten keresztül haladnak át, ezek a kapcsolatok megbízhatóbbak, gyorsabbak, kisebb késések és nagyobb biztonságot biztosítanak, mint a szokásos kapcsolatok az interneten keresztül. A ExpressRoute vagy a VPN megfelelő beállításával az ügyfelek hozzáadhatnak egy védelmi réteget az átvitelhez.

### <a name="extract-transform-load-etl-process"></a>Kinyerés-átalakítás-betöltési (ETL) folyamat
[](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) A program az adatok Azure SQL Data Warehouseba való betöltését külön ETL vagy importálási eszköz nélkül tudja betölteni. A Base lehetővé teszi, hogy a T-SQL-lekérdezéseken keresztül hozzáférjen az adatokhoz. A Microsoft üzleti intelligenciával és elemzésével foglalkozó verem, valamint a SQL Server rendszerrel kompatibilis, külső gyártótól származó eszközök is használhatók.

### <a name="azure-active-directory-setup"></a>Azure Active Directory telepítő
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) elengedhetetlen az üzembe helyezés kezeléséhez és a környezettel kommunikáló személyzethez való hozzáféréshez. Egy meglévő Windows Server-Active Directory [négy kattintással](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express)integrálható a HRE. Az ügyfelek az üzembe helyezett Active Directory infrastruktúrát (tartományvezérlőket) egy meglévő HRE is összeállíthatják azáltal, hogy az üzembe helyezett Active Directory-infrastruktúrát egy HRE-erdő altartományának teszi.

### <a name="additional-services"></a>További szolgáltatások
Bár ez az adattárház-architektúra nem az [Azure kereskedelmi](https://azure.microsoft.com/overview/what-is-azure/) környezetbe való telepítéshez készült, hasonló célkitűzések érhetők el az ebben a hivatkozási architektúrában ismertetett szolgáltatásokkal, valamint az elérhető további szolgáltatásokkal is. csak az Azure-beli kereskedelmi környezetben. Vegye figyelembe, hogy az Azure-beli kereskedelmi szolgáltatás egy FedRAMP DÖF P-ATO-t tart fenn a mérsékelt hatás szintjén, amely lehetővé teszi a kormányzati szervek és partnerek számára, hogy mérsékelten bizalmas adatokat telepítsenek a felhőbe az Azure kereskedelmi környezetének kihasználásával.

Az Azure kereskedelmi szolgáltatása számos szolgáltatást kínál, amely a formázott és formázatlan adattárolást, valamint az adattárházban használatos előkészítést végzi, beleértve a következőket:
-   A [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) egy felügyelt felhőalapú szolgáltatás, amely összetett hibrid kinyerési, átalakítási és betöltési (etl), kinyerhető terhelés-átalakítási (elt) és adatintegrációs projektekhez készült. A Azure Data Factory használatával az ügyfelek olyan adatvezérelt munkafolyamatokat hozhatnak létre és ütemezhetnek, amelyek különböző adattárakból származó adatok betöltésére szolgáló folyamatok. Az ügyfelek ezután feldolgozhatják és átalakítják az adatokat a kimenet adattárakba, például a Azure SQL Data Warehouseba.
-   A [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) lehetővé teszi bármilyen méretű, típusú és betöltési sebességű adatrögzítést egyetlen helyen az operatív és a felderítő elemzéshez. A Azure Data Lake Store kompatibilis a Hadoop-ökoszisztémában található legtöbb nyílt forráskódú összetevővel, és szépen integrálható más Azure-szolgáltatásokkal, például Azure SQL Data Warehouseokkal.

## <a name="threat-model"></a>Veszélyforrások modellje

A viszonyítási architektúra adatáramlási diagramja (DFD) [letölthető](https://aka.ms/blueprintdwthreatmodel) , vagy a következő címen érhető el:

![Adattárház a FedRAMP Threat modelhez](images/fedramp-datawarehouse-threat-model.png?raw=true "Adattárház a FedRAMP Threat modelhez")

## <a name="compliance-documentation"></a>Megfelelőségi dokumentáció
A [Azure Security and Compliance Blueprint – FedRAMP nagy ügyfél-felelősségi mátrix](https://aka.ms/blueprinthighcrm) felsorolja a FedRAMP magas alapkonfigurációja által igényelt összes biztonsági ellenőrzést. Hasonlóképpen, a [Azure Security and Compliance Blueprint – FedRAMP mérsékelt ügyfél-felelősségi mátrix](https://aka.ms/blueprintcrmmod) felsorolja az FedRAMP mérsékelt alapkonfigurációhoz szükséges összes biztonsági ellenőrzést. Mindkét dokumentum részletesen ismerteti, hogy az egyes vezérlőelemek megvalósítása a Microsoft, az ügyfél vagy a kettő között megosztva van-e.

A [Azure Security and Compliance Blueprint-FedRAMP High Control implementációs mátrix](https://aka.ms/blueprintdwcimhigh) és a [Azure Security and Compliance Blueprint-FedRAMP mérsékelt vezérlés megvalósítási mátrixa](https://aka.ms/blueprintdwcimmod) olyan információkat biztosít, amelyekkel a az egyes FedRAMP adattárház-architektúrája, beleértve annak részletes leírását, hogy a megvalósítás hogyan teljesíti az egyes kezelt vezérlők követelményeit.

## <a name="disclaimer"></a>Jogi nyilatkozat

 - Ez a dokumentum csak tájékoztató jellegű. A MICROSOFT NEM VÁLLAL SEMMILYEN KIFEJEZETT, VÉLELMEZETT VAGY TÖRVÉNYES GARANCIÁT A JELEN DOKUMENTUMBAN FOGLALT INFORMÁCIÓK ALAPJÁN. Ez a dokumentum "aktuálisként" van megadva. Az ebben a dokumentumban kifejezett információk és nézetek, beleértve az URL-címeket és az internetes webhelyek más hivatkozásait, értesítés nélkül változhatnak. A dokumentumot olvasó ügyfeleink a használatuk kockázatát viselik.
 - A jelen dokumentum nem biztosít semmilyen jogot semmilyen Microsoft-termékben vagy-megoldásban található szellemi tulajdonhoz.
 - Az ügyfelek belső referenciák szerint másolhatják és használhatják ezt a dokumentumot.
 - A jelen dokumentumban szereplő javaslatok megnövelték az adatok, a hálózat vagy a számítási erőforrások használatát az Azure-ban, és növelhetik az ügyfelek Azure-licencét vagy előfizetési költségeit.
 - Ez az architektúra arra szolgál, hogy az ügyfelek számára a konkrét követelményekhez igazodva és nem használható éles környezetben.
 - Ez a dokumentum hivatkozásként van kifejlesztve, és nem használható az összes olyan eszköz meghatározására, amelyekkel az ügyfél megfelel bizonyos megfelelőségi követelményeknek és előírásoknak. Az ügyfeleknek jóvá kell hagyniuk a szervezeten belüli jogi támogatást a jóváhagyott ügyfél-implementációkban.
