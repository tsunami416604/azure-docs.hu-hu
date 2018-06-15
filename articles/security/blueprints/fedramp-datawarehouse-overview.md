---
title: Azure biztonsági és megfelelőségi tervezetének - adatraktár a FedRAMP automatizálásához
description: Azure biztonsági és megfelelőségi tervezetének - adatraktár a FedRAMP automatizálásához
services: security
author: jomolesk
ms.assetid: 834d1ff6-8369-455f-b052-1ef301e3d7e6
ms.service: security
ms.topic: article
ms.date: 05/02/2018
ms.author: jomolesk
ms.openlocfilehash: 110ce131286f437e051dd859eb4d2baa29f106f6
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/04/2018
ms.locfileid: "33206964"
---
# <a name="azure-security-and-compliance-blueprint-data-warehouse-for-fedramp-automation"></a>Azure biztonsági és megfelelőségi tervezetének: adatraktár a FedRAMP automatizálásához

## <a name="overview"></a>Áttekintés

A [Szövetségi kockázat és engedélyezési felügyeleti Program (FedRAMP)](https://www.fedramp.gov/) egy Egyesült Államok kormánya kiterjedő program, amely a biztonsági értékelést, engedélyezési és állandó felügyelet szabványosított megközelítését ismerteti a felhő termékei és szolgáltatásai. Az Azure biztonsági és megfelelőségi tervezetének útmutatással szolgál hogyan telepíthet egy Microsoft Azure data warehouse architektúrája, amely segít a FedRAMP nagy vezérlők egy részhalmazát valósítja meg. Ez a megoldás a nyújt útmutatást a központi telepítés és az Azure-erőforrások konfigurációs egy közös referencia-architektúrában, módon, ahol az ügyfelek bizonyos biztonsági és megfelelőségi követelményeknek megfelel, amely tartalmazza, és alapjaként szolgál az ügyfelek számára hozza létre, és a saját data warehouse-megoldások konfigurálása az Azure-ban.

A referencia-architektúrában, tartozó vezérlőelem alkalmazási útmutatók és fenyegetés modellek célja, hogy az ügyfelek úgy, hogy az egyedi követelményeket alapjaként szolgálnak, és nem használható mint – egy termelési környezetben. Erre a környezetre módosítás nélkül az alkalmazások központi telepítése nem elegendő teljesen követelményeinek teljesítése érdekében az FedRAMP nagy alaptervhez. Vegye figyelembe a következőket:
- Az architektúra munkaterhelések telepítése az Azure-bA FedRAMP megfelelő módon ügyfelek alapterv.
- Ügyfelek végző megfelelő biztonsági és megfelelőségi értékelése használatával készített ebbe az architektúrába követelmények változhatnak megoldások a részletekről az egyes ügyfelek általi alapján.

## <a name="architecture-diagram-and-components"></a>Architektúrája és összetevői

Ez a megoldás a data warehouse referencia-architektúrában egy nagy teljesítményű és biztonságos felhőalapú adatraktára, amely biztosítja. Ebben az architektúrában van két külön adat szintet: egy adott adatok importálása, tárolt, és egy fürtözött SQL-környezetben, és egy másik készítve az Azure SQL Data warehouse Ha az adatok betöltése az ETL eszközzel (pl. [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase)T-SQL-lekérdezések) feldolgozásra. Amennyiben az adatokat az Azure SQL Data Warehouse tárolja, analytics a nagy léptékű futtathatja.

A Microsoft Azure számos ügyfél jelentéskészítés és elemzés szolgáltatások. Ez a megoldás az SQL Server Reporting Services (SSRS) gyors létrehozása az Azure SQL Data Warehouse jelentéseket tartalmazza. Az összes SQL-forgalom azáltal, hogy önaláírt tanúsítványokat SSL van titkosítva. Ajánlott eljárásként Azure azt javasolja, hogy egy megbízható hitelesítésszolgáltató használatát a biztonság növelése.

Az Azure SQL Data Warehouse adatainak oszlopos tárolást jelentősen csökkenti az adatok tárolási költségek a lekérdezési teljesítmény formátuma nem relációs táblákban tárolódik.  Használati követelményeitől függően Azure SQL Data Warehouse számítási erőforrásokat is méretezése felfelé vagy lefelé és le teljesen Ha nincs aktív folyamatok igénylő számítási erőforrásokat.

Egy SQL terheléselosztó SQL-forgalom, nagy teljesítményt biztosító kezeli. A referencia-architektúrában összes virtuális gépet üzembe helyezni a rendelkezésre állási készlet magas rendelkezésre állású és vész-helyreállítási lehetőségeket az AlwaysOn rendelkezésre állási csoportban konfigurált SQL Server-példánnyal.

A data warehouse referenciaarchitektúra egy Active Directory (AD) réteget architektúrájának erőforrások kezelését is. Az Active Directory-alhálózathoz lehetővé teszi, hogy könnyen elfogadása alatt egy nagyobb AD erdő struktúra, így a folyamatos működéshez a környezet még nem érhető el a nagyobb erdőben való hozzáférés esetén. Az összes virtuális gép tartományhoz az Active Directory-réteghez, és az Active Directory-csoportházirendek használatával kényszerítheti a biztonsági és megfelelőségi konfigurációk az operációs rendszer szintjén.

A virtuális gép lesz felügyeleti megerősített állomás, és biztonságos kapcsolatot biztosít a rendszergazdák központilag telepített hozzáférését az erőforrásokhoz. Az adatok betöltése az átmeneti területre, a felügyeleti megerősített gazdagépen keresztül. **Azure azt javasolja, hogy a referencia architektúra alhálózati történő felügyelete és az adatok importálása a VPN- vagy Azure ExpressRoute-kapcsolat beállítása.**

![Adatraktár-FedRAMP referencia architektúra diagramja](images/fedramp-datawarehouse-architecture.png?raw=true "adatraktár-FedRAMP referencia architektúra diagramja")

A megoldás az Azure-szolgáltatásokat. Az üzembe helyezési architektúrája részleteit szerepelnek a [üzembe helyezési architektúrája](#deployment-architecture) szakasz.

Azure-alapú virtuális gépek
-   (1) megerősített állomás
-   (2) az active Directory-tartományvezérlő
-   (2) az SQL Server-fürt csomópontjának
-   (1) az SQL Server tanúsító

Rendelkezésre állási csoportok
-   (1) az active Directory-tartományvezérlők
-   (1) SQL fürtcsomópontok és a tanúsító

Virtual Network
-   (4) alhálózatok
-   (4) hálózati biztonsági csoportok

SQL Data Warehouse

SQL Server Reporting Services szoftverben

Az Azure SQL terheléselosztó

Azure Active Directory

Recovery Services-tároló

Azure Key Vault

Az Operations Management Suite (OMS)

## <a name="deployment-architecture"></a>Üzembe helyezési architektúrája

Az alábbi szakasz részletesen fejlesztése és megvalósítása elemek.

**Az SQL Data Warehouse**: [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) egy vállalati adatok adatraktár (EDW), amely kihasználja a nagymértékben párhuzamos feldolgozási (MPP) gyorsan összetett lekérdezések futtatása adatmennyiségig között van. Big Data típusú adatok importálása az SQL Data Warehouse egyszerű PolyBase-T-SQL-lekérdezések és nagy teljesítményű analytics futtatásához használt MPP hatványa.

**SQL Server Reporting Services**: [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) lehetővé teszi a táblák, diagramokat, térképeket, mérőműszer, oszlopcsoportosításokhoz, valamint több Azure SQL Data Warehouse jelentések gyors létrehozását.

**Megerősített állomás**: A megerősített állomás a központi hely, amely lehetővé teszi a felhasználóknak ebben a környezetben telepített erőforrások elérését. A megerősített állomás csak átengedi a nyilvános IP-címekről távoli forgalmat biztonságos lista telepített erőforrások biztonságos kapcsolatot nyújt. Távoli asztal (RDP)-forgalmát engedélyezi, hogy a forgalom forrásának kell definiálni a hálózati biztonsági csoport (NSG).

A virtuális gép létrehozása a következő konfigurációk a tartományhoz csatlakoztatott megerősített gazdagépeként:
-   [Kártevőirtó-bővítmény](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [OMS-bővítmény](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Azure Diagnostics-bővítményt](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Az Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) (tiszteletben tartja a Azure Government, a PCI DSS, a HIPAA és a követelmények) az Azure Key Vault használatával
-   Egy [automatikus leállítási házirendet](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) használaton virtuálisgép-erőforrások fogyasztásának csökkentése érdekében
-   [A Windows Defender Credential őr](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) engedélyezve van, hogy a hitelesítő adatokat és más titkos adatokat futtatni egy olyan védett környezetben, amely el van különítve a futó operációs rendszer

### <a name="virtual-network"></a>Virtuális hálózat
A referencia-architektúrában egy címterében 10.0.0.0/16 titkos virtuális hálózat határozza meg.

**Hálózati biztonsági csoportok**: [NSG-k](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) tartalmaz a hozzáférés-vezérlési listái (ACL), amely engedélyezi vagy megtagadja a forgalmat a Vneten belül. Az NSG-k segítségével biztonságos egy alhálózat vagy az egyes virtuális gép szintjén-forgalmat. A következő NSG-k érhetők el:
  - Egy NSG-t az Adatréteg (SQL Server-fürtök, SQL Server tanúsító és SQL terheléselosztó)
  - Egy NSG-t a felügyeleti megerősített állomás
  - Egy NSG-t az Active Directory
  - Egy NSG-t az SQL Server Reporting Services szoftverben

Az NSG-k, amelyek mindegyikének egyedi portokat és protokollokat nyissa meg, hogy a megoldás megfelelően és biztonságosan együttműködhet. Ezenkívül a következő konfigurációk engedélyezve vannak az egyes NSG:
  - [Diagnosztikai naplók és események](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) engedélyezve van, és a storage-fiókban tárolt
  - OMS szolgáltatáshoz csatlakozik-e a [NSG-t a diagnosztika](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Alhálózatok**: az egyes alhálózatokon társítva a megfelelő NSG.

### <a name="data-at-rest"></a>Inaktív adat
Az architektúra történik a titkosítás, az adatbázis naplózását, és egyéb adatok védelmét.

**Az Azure Storage** titkosított adatok rest követelmények teljesítéséhez összes [Azure Storage](https://azure.microsoft.com/services/storage/) használ [Storage szolgáltatás titkosítási](https://docs.microsoft.com/azure/storage/storage-service-encryption).

**Az Azure Disk Encryption**
[Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) kihasználja a Windows operációs rendszer és az adatlemezek kötettitkosítást biztosít a BitLocker-szolgáltatás. A megoldás integrálódik az Azure Key Vault szabályozni, és a lemez-titkosítási kulcsok kezeléséhez.

**Az Azure SQL Database** az Azure SQL Database-példányt használja a következő adatbázis biztonsági intézkedéseket:
-   [AD hitelesítési és engedélyezési](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) adatbázis-felhasználók és más Microsoft-szolgáltatásokban egyetlen központi helyen identitás kezelését teszi lehetővé.
-   [SQL-adatbázis naplózásának](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) nyomon követi az adatbázisok események, mind az írás őket naplózási jelentkezzen be Azure storage-fiók.
-   SQL-adatbázis használatára van konfigurálva [átlátszó Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), amely végrehajtja a valós idejű titkosítási és visszafejtési adatainak és naplókönyvtárainak fájlok aktívan információk védelme érdekében. TDE biztosítja, hogy a tárolt adatok nem lett bárki hozzáférhet.
-   [Tűzfal-szabályok](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) tagadni a hozzáférést minden adatbázis-kiszolgálók csak a megfelelő engedélyekkel. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján.
-   [A Fenyegetésészlelés SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) lehetővé teszi, hogy felderítésére és a lehetséges veszélyforrásokra választ, adja meg a biztonsági riasztások adatbázis gyanús tevékenységek, a potenciális biztonsági réseket, az SQL injektálási támadások és a rendellenes adatbázis-hozzáférési előforduló minták.
-   [Mindig titkosítja az oszlopok](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) győződjön meg arról, hogy bizalmas adatok soha nem jelenik meg az adatbázis rendszerében szövegként. Miután engedélyezte az adattitkosítást, csak az ügyfélalkalmazások vagy a kulcsoknak access app kiszolgálók hozzáférhet egyszerű szöveges adatokat.
-   [SQL-adatbázis dinamikus adatmaszkolási](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) teheti, miután telepíti a referencia-architektúrában. Állítsa be a dinamikus adatmaszkolási beállítások igazodnia kell az adatbázis-séma esetén szükséges.

### <a name="business-continuity"></a>Az üzletmenet folytonossága
**Magas rendelkezésre állású**: kiszolgáló-munkaterhelések vannak csoportosítva egy [rendelkezésre állási csoport](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) az Azure virtuális gépek magas rendelkezésre állásának biztosításához. Legalább egy virtuális gép érhető el egy tervezett vagy nem tervezett karbantartási események esetén a 99,95 % értekezlet Azure SLA-t.

**Recovery Services-tároló**: A [Recovery Services-tároló](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) Kezelőkód biztonsági mentési adatokat, és az összes konfiguráció Azure virtuális gépek ebben az architektúrában védelmét. A Recovery Services-tároló, az ügyfelek is fájlok és mappák visszaállítása egy infrastruktúra-szolgáltatási virtuális gép helyreállítása a teljes virtuális gép, gyorsabb visszaállítás engedélyezése nélkül.

### <a name="logging-and-audit"></a>Naplózási és naplózása
[Az Operations Management Suite (OMS)](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) rendszer és felhasználói tevékenységek, valamint a rendszerállapot-részletes naplózást végez. Az OMS [Naplóelemzési](https://azure.microsoft.com/services/log-analytics/) megoldás gyűjti és elemzi az adatokat az Azure-erőforrások által létrehozott és a helyszíni környezetben.
- **Tevékenységi naplóit**: [tevékenységi naplóit](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) előfizetés erőforrásainak végrehajtott műveletek betekintést nyújtanak.
- **Diagnosztikai naplók**: [diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) minden erőforrás által kibocsátott összes naplók tartalmazzák. Ezek a naplók tartalmazzák a Windows rendszer-eseménynaplói és Azure Blob storage táblák és várólista naplókat.
- **Tűzfal-naplók**: az Alkalmazásátjáró biztosít teljes diagnosztikai és a naplók eléréséhez. Alkalmazásátjáró WAF-kompatibilis erőforrások elérhetők tűzfal naplók.
- **Archiválás jelentkezzen**: minden diagnosztikai naplók írni egy központosított és titkosított Azure storage-fiókjához archiválási 2 nap meghatározott megőrzési idővel rendelkező. Ezek a naplók feldolgozása, tárolására és irányítópult reporting Azure Naplóelemzés csatlakozni.

Emellett a következő OMS-megoldások tartoznak ebbe az architektúrába részeként:
-   [AD Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): az Active Directory állapotát ellenőrző megoldás a kockázat és környezetek állapotának értékelésére rendszeres időközönkénti, és a telepített kiszolgálói infrastruktúra vonatkozó javaslatok a rangsorolt listáját tartalmazza.
-   [Kártevőirtó Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): A kártevőirtó megoldást jelent a kártevő szoftver, a fenyegetések és a védelmi állapot.
-   [Azure Automation szolgáltatásbeli](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): az Azure Automation-megoldás tárolja, fut, és kezeli a runbookok.
-   [Biztonság és a naplózási](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): A biztonság és a naplózási irányítópult erőforrások biztonsági állapotát egy magas szintű betekintést biztosít a biztonsági tartományok, jelentős problémák, észlelések, fenyegetésfelderítési adataival és közös biztonsági lekérdezések metrikák megadásával.
-   [SQL Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): megoldás az SQL állapotának ellenőrzése a kockázat és környezetek állapotának értékelésére rendszeres időközönkénti és nyújt a felhasználók számára a telepített kiszolgálói infrastruktúra vonatkozó javaslatok a rangsorolt listáját.
-   [Frissítéskezelés](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): A frissítés felügyeleti megoldás lehetővé teszi az operációs rendszer biztonsági frissítések, a rendelkezésre álló frissítések állapot és a kötelező frissítések telepítésének folyamatát, például felhasználói kezelését.
-   [Ügynök állapotfigyelő](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): az ügyfélállapot-megoldást jelent, hány ügynök van telepítve, és a földrajzi eloszlása, valamint hány ügynökök, amelyek nem válaszoló és ügynökök, amelyek a működési adatok elküldése.
-   [Az Azure tevékenységi naplóit](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): A tevékenység Naplóelemzési megoldás segítséget nyújt a elemzése az Azure tevékenységi naplóit az ügyfél az összes Azure-előfizetések között.
-   [A változáskövetés](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): A változáskövetési megoldás lehetővé teszi az ügyfelek azonosítását a változtatásokat a környezetben.

### <a name="identity-management"></a>Identitáskezelés
Az alábbi technológiákat biztosítja az identitás Eszközkezelési funkciókat az Azure környezetben:
-   [Active Directory (AD)](https://azure.microsoft.com/services/active-directory/) lehet a Microsoft több-bérlős felhőalapú címtár- és identitáskezelési felügyeleti szolgáltatást. Minden felhasználó a megoldáshoz az Azure Active Directoryban, beleértve az SQL-adatbázist elérő felhasználók jöttek létre.
-   Az alkalmazás-hitelesítés az Azure AD használatával történik. További információkért lásd: [alkalmazások integrálása az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Emellett az adatbázis oszlop titkosítási segítségével az Azure AD hitelesíti az alkalmazás az Azure SQL Database. További információkért lásd: hogyan [bizalmas adatokat az SQL-adatbázis védelme](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Az Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) észleli a potenciális biztonsági réseket egy szervezet identitásait érintő, konfigurálja az automatikus válaszokat ad egy szervezet identitásait kapcsolódó észlelt gyanús tevékenységek és pedig megvizsgálja a gyanús incidensek a megfelelő művelettel hárítsa el őket.
-   [Azure szerepköralapú hozzáférés-vezérlés (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) lehetővé teszi, hogy az Azure kezelési kialakításával foglalkozik. Előfizetés hozzáférés korlátozódik előfizetés-rendszergazdaként.

Az Azure SQL Database biztonsági szolgáltatásaival kapcsolatos további tudnivalókért tekintse meg a [Contoso klinikán bemutató alkalmazás](https://github.com/Microsoft/azure-sql-security-sample) minta.

### <a name="security"></a>Biztonság
**Titkos kulcsok kezelése**: A megoldás az [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) kulcsok és titkos kulcsok kezeléséhez. Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében.

**Kártevők elleni védekezés**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) virtuális gépek számára lehetővé teszi a valós idejű védelem segítségével azonosíthatja, és távolítsa el a vírusok, kémprogramok és más, kártevő szoftverek, konfigurálható a riasztások Ha ismert kártevő vagy nemkívánatos szoftverek próbálják telepíteni futtassa a védett virtuális gépet.

**Felügyeleti javítás**: Windows virtuális gépek telepítése a referencia-architektúrában részeként az automatikus frissítések kap a Windows Update szolgáltatás alapértelmezés szerint vannak konfigurálva. Ez a megoldás is magában foglalja az OMS [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) szolgáltatás, amelyen keresztül a frissített telepítések is létrehozható a következő virtuális gépek szükség esetén.


## <a name="guidance-and-recommendations"></a>Útmutatás és javaslatok
### <a name="expressroute-and-vpn"></a>ExpressRoute- és VPN
[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy biztonságos VPN-alagúton kell úgy, hogy biztonságos kapcsolatot létrehozni a data warehouse referenciaarchitektúra részeként telepített erőforrások. ExpressRoute-kapcsolatok az interneten keresztül halad, mivel ezeket a kapcsolatokat további megbízhatóságát, gyorsabb sebességű, kisebb késések fordulnak elő, és kínálnak nagyobb biztonságot nyújtana tipikus kapcsolatok az interneten keresztül. Beállításával megfelelően expressroute-on vagy VPN-en, az ügyfelek adhat hozzá egy védelmi réteget biztosít adatokat átvitel közben.

### <a name="extract-transform-load-etl-process"></a>Kivonat-átalakítási-betöltési (ETL) folyamat
[A PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) adatok betöltése az Azure SQL Data Warehouse nincs szükség külön ETL vagy eszköz importálni. PolyBase-T-SQL lekérdezések adatokhoz való hozzáférés lehetővé teszi. A Microsoft üzleti intelligencia és elemzési verem, valamint külső eszközök kompatibilis az SQL Server, a polybase-zel használható.

### <a name="azure-active-directory-setup"></a>Az Azure Active Directory beállítása
[Az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) fontos a központi telepítés kezelése és a kiépítés a környezetben való interakció csoporthoz való hozzáférés. Meglévő Windows Server Active Directory integrálható az AAD-ben [négy kattintással](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Az ügyfelek is nagy terhelést jelent a telepített Active Directory-infrastruktúrát (tartományvezérlők) egy meglévő aad-ben való azáltal, hogy a telepített Active Directory-infrastruktúra altartománya: az AAD-erdőt.

### <a name="additional-services"></a>További szolgáltatások
Bár a data warehouse architektúrája nem szánt központi telepítést, hogy a [Azure kereskedelmi](https://azure.microsoft.com/overview/what-is-azure/) környezet, hasonló célok érhető el a referencia-architektúrában, és a szolgáltatások, valamint További szolgáltatások csak az Azure kereskedelmi környezetben elérhető. Vegye figyelembe, hogy Azure kereskedelmi tart fenn a FedRAMP JAB P-ATO szinten mérsékelt hatása, állami intézményekhez és a partnerek számára, hogy telepíti a felhőbe, az Azure kereskedelmi környezetben, ami mérsékelten bizalmas adatokat.

Az Azure kereskedelmi ajánlatokat széles körének a szolgáltatások adott formázott leíró és formázatlan adattárolás és átmeneti használhatók az adatraktározás terén, beleértve:
-   [Az Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) egy felügyelt felhőszolgáltatás, amely a kivonat-átalakítási-betöltési (ETL), összetett hibrid készült integrációs projektek kivonat-betöltési-átalakítás (ELT), és adatokat. Azure Data Factory használatával, az ügyfelek hozhat létre és folyamatok, amelyek különböző adattárolókhoz származó adatok nevű adatvezérelt munkafolyamatok ütemezni. Az ügyfelek ezután feldolgozni, és kimeneti adatok átalakítása például az Azure SQL Data Warehouse adattárolókhoz.
-   [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) lehetővé teszi, hogy a műveleti és felderítési jellegű egyetlen helyen bármilyen méretű, típusú és feldolgozási sebességű adatok rögzítését. Azure Data Lake Store a Hadoop-ökoszisztéma legtöbb nyílt forráskódú összetevőjével kompatibilis, és jól integrálható az Azure SQL Data Warehouse például más Azure-szolgáltatásokkal.

## <a name="threat-model"></a>Fenyegetések modellezése

Az adatok folyamatábrája (DFD) a referenciaarchitektúra érhető el [letöltése](https://aka.ms/blueprintdwthreatmodel) vagy alatt található:

![Adatraktár-FedRAMP fenyegetések modellezése](images/fedramp-datawarehouse-threat-model.png?raw=true "FedRAMP fenyegetések modellezése tartozó adatraktár")

## <a name="compliance-documentation"></a>Megfelelőségi dokumentáció
A [Azure biztonsági és megfelelőségi tervezetének – FedRAMP magas ügyfél felelősségi mátrix](https://aka.ms/blueprinthighcrm) sorolja fel a FedRAMP nagy alapkonfiguráció által igényelt összes biztonsági vezérlő. Hasonlóképpen a [Azure biztonsági és megfelelőségi tervezetének – FedRAMP mérsékelt ügyfél felelősségi mátrix](https://aka.ms/blueprintcrmmod) sorolja fel a FedRAMP mérsékelt alapkonfiguráció által igényelt összes biztonsági vezérlő. A dokumentumok részletességi, hogy minden vezérlő végrehajtásának feladata a Microsoft, az ügyfél vagy a kettő között megosztott.

A [Azure biztonsági és megfelelőségi tervezetének - FedRAMP magas vezérlés megvalósítása mátrix](https://aka.ms/blueprintdwcimhigh) és a [Azure biztonsági és megfelelőségi tervezetének - FedRAMP mérsékelt vezérlés megvalósítása mátrix](https://aka.ms/blueprintdwcimmod) adja meg Amikor vezérlők hatálya FedRAMP minden egyes referenciakonfigurációnál, például hogyan végrehajtása megfelel minden érintett vezérlő részletes leírást a data warehouse architektúrája adatokat.

## <a name="disclaimer"></a>Jogi nyilatkozat

 - Ez a dokumentum csak tájékoztatási célokat szolgál. A MICROSOFT NEM VÁLLAL SEMMILYEN KIFEJEZETT, KIFEJEZETT, VÉLELMEZETT VAGY TÖRVÉNYES, HOGY A JELEN DOKUMENTUMBAN SZEREPLŐ INFORMÁCIÓK. Ez a dokumentum biztosított ",-van." Információk és nézetek ebben a dokumentumban, beleértve az URL-CÍMEK és más internetes webhelyet, értesítés nélkül változhatnak. Ez a dokumentum olvasásakor az ügyfelek az alkalmazást saját felelősségére használja.
 - Ez a dokumentum nem biztosít semmilyen jogot semmilyen Microsoft-termék vagy a megoldások található szellemi tulajdonhoz rendelkező ügyfelek.
 - Az ügyfelek azonban másolható és ez a dokumentum belső hivatkozási célokra használja.
 - Bizonyos ajánlások a dokumentum azt eredményezheti, megnövekedett adat-, hálózati vagy számítási erőforrás-használat az Azure-ban, és megnövelheti az ügyfél Azure licencek vagy előfizetések költségeit.
 - Ebbe az architektúrába célja, hogy az ügyfelek úgy, hogy az egyedi követelményeket alapjaként szolgálnak, és nem használható mint – egy termelési környezetben.
 - Ez a dokumentum hivatkozásként fejlesztése, és nem használható minden olyan eszközt, amely szerint az ügyfél megfelel adott megfelelőségi követelmények és szabályok meghatározásához. Az ügyfelek jóváhagyott ügyfél-hitelesítés megvalósításához a szervezetek jogi támogatnia kell kérnie.
