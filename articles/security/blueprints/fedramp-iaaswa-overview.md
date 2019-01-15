---
title: Azure biztonsági és megfelelőségi terv – IaaS-webalkalmazás fedramp-hoz
description: Azure biztonsági és megfelelőségi terv – IaaS-webalkalmazás fedramp-hoz
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 8fe47cff-9c24-49e0-aa11-06ff9892a468
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/08/2018
ms.author: jomolesk
ms.openlocfilehash: 59a3a92640c7f0bc434881921e520d1b9cb352c3
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2019
ms.locfileid: "54265498"
---
# <a name="azure-security-and-compliance-blueprint-iaas-web-application-for-fedramp"></a>Azure biztonsági és megfelelőségi terv: IaaS Web Application for FedRAMP

## <a name="overview"></a>Áttekintés

A [Federal Risk and Authorization Management Program (FedRAMP)](https://www.fedramp.gov) van egy amerikai közigazgatásra kiterjedő program, amely a biztonság értékeléséhez, engedélyezési és folyamatos monitorozással szabványos módszert biztosít a felhőalapú termékek és szolgáltatások. Az Azure biztonsági és megfelelőségi tervezet Automation szolgáltatás (IaaS) környezetként, megfelelő az internetre irányuló egyszerű webalkalmazások FedRAMP-kompatibilis infrastruktúra központi telepítésére vonatkozó útmutatást. Ez a megoldás automatizálja a központi telepítése és konfigurálása Azure-erőforrások általános referenciaarchitektúra, bemutatásához, amelyben ügyfeleink megfelel bizonyos biztonsági és megfelelőségi követelmények és szolgál az ügyfelek alapjaként módon és saját megoldások konfigurálásához az Azure-ban. A megoldás szabályozza a magas szintű FedRAMP alaptervből, NIST SP 800-53 előírásain alapján egy részhalmazát valósítja meg. FedRAMP-követelmények és a megoldás kapcsolatos további információkért lásd: a [megfelelőségi dokumentáció](#compliance-documentation).
> [!NOTE]
> Ez a megoldás üzembe helyezi az Azure Governmentre.

Az Azure biztonsági és megfelelőségi tervezet Automation automatikusan üzembe helyez egy IaaS webes alkalmazás referenciaarchitektúrájához, ha az előre konfigurált biztonsági vezérlők segítségével az ügyfelek elérjék a FedRAMP-követelményeknek való megfelelés. A megoldás Azure Resource Manager-sablonok és erőforrások üzembe helyezésének és konfigurációs PowerShell-parancsfájlok áll.

Ez az architektúra célja, hogy az ügyfelek számára az adott követelményekhez beállítása alapjaként szolgálja ki, és nem használható-éles környezetben van. Ebben a környezetben, módosítás nélküli egy alkalmazás telepítése azonban nem teljesen felel meg a magas szintű FedRAMP-alapkonfiguráció követelményeinek. Vegye figyelembe a következőket:
- Ez az architektúra egy alapkonfiguráció segítenek az ügyfeleknek az Azure FedRAMP-kompatibilis módon biztosít.
- Ügyfelek végző megfelelő biztonsági és megfelelőségi vizsgálata a beépített Ez az architektúra használatával, ahogy követelményei eltérőek lehetnek megoldásoktól alapján minden ügyfél megvalósítása tulajdonságairól.

Röviden, ez a megoldás működéséről, tekintse meg ezt [videó](https://aka.ms/fedrampblueprintvideo) pedig elmagyarázza és annak üzembe helyezési bemutatására.

Kattintson a [Itt](https://aka.ms/fedrampblueprintrepo) üzembe helyezési utasítások.

## <a name="architecture-diagram-and-components"></a>Architektúra és összetevők
Ez a megoldás az SQL Server-háttérrendszerét az IaaS-webalkalmazás referenciaarchitektúra üzembe helyezi. Az architektúra egy webes réteg, adatrétegbeli, Active Directory infrastruktúrával, az Application Gateway és terheléselosztó tartalmaz. A webes és az adatrétegekhez az üzembe helyezett virtuális gépek rendelkezésre állási csoportba vannak konfigurálva, és az SQL Server példányai vannak konfigurálva a magas rendelkezésre állás érdekében egy AlwaysOn rendelkezésre állási csoportban. Virtuális gépek tartományhoz csatlakoztatva, és az Active Directory-csoportházirendek segítségével érvényesíti a biztonsági és megfelelőségi konfigurációk az operációs rendszer szintjén. Bástyagazdagép biztonságos kapcsolatot biztosít a rendszergazdák számára a hozzáférés üzembe helyezett erőforrásokat. **Azure azt javasolja, hogy a VPN- vagy Azure ExpressRoute kapcsolat a felügyeleti és az adatok importálása a referencia architektúra alhálózatban.**

![IaaS-webalkalmazás FedRAMP architekturális diagramja](images/fedramp-iaaswa-architecture.png?raw=true "IaaS-webalkalmazás FedRAMP architekturális diagramja")

Ez a megoldás a következő Azure-szolgáltatásokat használ. Az üzembe helyezési architektúra részletek találhatók a [üzembe helyezési architektúrája](#deployment-architecture) szakaszban.

- Azure-alapú virtuális gépek
    - (1) bástyagazdagép (Windows Server 2016 Datacenter)
    - (2) az active Directory-tartományvezérlőhöz (Windows Server 2016 Datacenter)
    - (2) az SQL Server-fürt csomópontjának (Windows Server 2016-on futó SQL Server 2017)
    - (2) Web/IIS (Windows Server 2016 Datacenter)
- Rendelkezésre állási csoportok
    - (1) az active Directory-tartományvezérlők
    - (1) az SQL-fürtcsomópont
    - (1) Web/IIS
- Azure Virtual Network
    - ((1) /16 virtuális hálózatok
    - (5) /24 alhálózatok
    - DNS-beállítások mindkét tartományvezérlőn vannak beállítva
- Azure Load Balancer
- Azure Application Gateway
    - (1) WAF Application Gateway engedélyezve
        - Tűzfalmód: megelőzése
        - Szabálykészlet: OWASP 3.0
        - Figyelő: 443-as port
- Azure Storage
    - (7) georedundáns storage-fiókok
- Az Azure Felhőbeli tanúsító
- Recovery Services-tároló
- Azure Key Vault
- Azure Active Directory (Azure AD)
- Azure Resource Manager
- Log Analytics
- Azure Monitor

## <a name="deployment-architecture"></a>Üzembe helyezési architektúrája

A következő szakasz az fejlesztése és megvalósítása elemek részletei.

**Bástyagazdagép**: A megerősített gazdagép a központi hely, amely biztonságos kapcsolatot biztosít a rendszergazdák erőforrások eléréséhez telepítve. A megerősített gazdagép NSG lehetővé teszi a kapcsolatokat csak a 3389-es RDP-hez. Ügyfelek tovább konfigurálhatják a bástyagazdagép szervezet rendszer-megerősítési követelmények kielégítése érdekében.

### <a name="virtual-network"></a>Virtuális hálózat
Az architektúra egy 10.200.0.0/16 címtere a privát virtuális hálózat határozza meg.

**Hálózati biztonsági csoportok**: Ez a megoldás üzembe helyez egy másik alhálózatot, adatbázis-alhálózat, az Active Directory-alhálózathoz és felügyeleti alhálózaton egy virtuális gépen belüli – rendelkező erőforrásokat. Alhálózatok logikailag elválasztott korlátozhatja, hogy csak szükséges rendszer- és felügyeleti funkciók, az alhálózatok közötti adatforgalom az egyes alhálózatokra alkalmazott hálózati biztonsági csoport szabályait.

Tekintse át a konfigurációját a [hálózati biztonsági csoportok](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) üzembe helyezett ezzel a megoldással. Ügyfelek konfigurálhatják a hálózati biztonsági csoportok használatával fent a fájl szerkesztésével [ebben a dokumentációban](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) alapján.

Minden egyes alhálózatban a dedikált hálózati biztonsági csoport (NSG) rendelkezik:
- Az Application Gateway (LBNSG) 1 NSG
- 1 NSG bástyagazdagép (MGTNSG)
- 1 NSG-t elsődleges és tartalék tartományvezérlők (ADNSG)
- SQL-kiszolgálók (SQLNSG) 1 NSG
- 1 NSG webes réteg (WEBNSG)

**Alhálózatok**: Minden egyes alhálózathoz társítva a megfelelő NSG-t.

### <a name="data-at-rest"></a>Inaktív adat

Az architektúra számos titkosítási mértékek használatával védi az inaktív adatok.

**Az Azure Storage**: Inaktív adatok titkosítása követelményeinek összes storage-fiók használata [a Storage Service Encryption](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

**SQL Server**: Az SQL Server használatára van konfigurálva [transzparens adattitkosítási (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption), amely végrehajtja az adatok inaktív védelme adathoz és naplófájlhoz valós idejű titkosításához és visszafejtéséhez. TDE biztosítja, hogy a tárolt adatok nem lett bárki hozzáférhet.

Ügyfelek is előfordulhat, hogy konfigurálja a következő SQL Server biztonsági intézkedéseket:
-   [AD-hitelesítés és engedélyezés](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) lehetővé teszi az identitáskezelést adatbázis-felhasználók és más Microsoft-szolgáltatások egyetlen központi helyen.
-   [Az SQL database naplózási szolgáltatásával](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) nyomon követi az adatbázisok eseményeit és felvezeti ezeket egy naplófájlba, egy Azure storage-fiókban.
-   [Tűzfalszabályok](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) megakadályozzák elérését, adatbázis-kiszolgálók, amíg a megfelelő engedélyekkel. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján.
-   [Az SQL Fenyegetésészlelési](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) lehetővé teszi, hogy az észlelés és válasz a lehetséges veszélyforrásokra, bekövetkezésük pillanatában észlelhessék a biztonsági riasztásokat a gyanús adatbázis-tevékenységekről, a lehetséges biztonsági résekről, az SQL-injektálási támadások és a rendellenes adatbázis-hozzáférés minták.
-   [Always Encrypted az oszlopok](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) győződjön meg arról, hogy bizalmas adatok soha nem jelenik meg, mint belül az adatbázis-rendszer egyszerű szövegként. Adatok titkosításának engedélyezése után csak az ügyfélalkalmazások vagy alkalmazáskiszolgálók hozzáférést a kulcsokhoz hozzáférhet egyszerű szöveges adatokat.
-   [Az SQL Database dinamikus adatmaszkolása](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) teheti, miután a referenciaarchitektúra üzembe helyezte. Ügyfelek módosítania kell a dinamikus adatmaszkolási beállítások be kell tartaniuk az adatbázissémát.

**Az Azure Disk Encryption**: Az Azure Disk Encryption Windows IaaS virtuális gép titkosított lemezek segítségével. [Az Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) az operációs rendszer és az adatlemezek kötettitkosítást biztosít a Windows BitLocker funkcióját használja. A megoldás integrálva van az Azure Key Vault segítségével szabályozhatja, és kezelhetik a lemeztitkosítási kulcsokat.

### <a name="identity-management"></a>Identitáskezelés

A következő technológiákat biztosítja identitás az eszközkezelési funkciókat az Azure-környezetben:
- [Az Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) van a Microsoft több-bérlős felhőalapú címtár- és identitáskezelési szolgáltatás.
- Az Azure AD-hitelesítést egy ügyfél által üzembe helyezett webalkalmazás hajtható végre. További információkért lásd: [alkalmazások integrálása az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).  
- [Az Azure szerepköralapú hozzáférés-vezérlés (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) lehetővé teszi, hogy pontosan fókuszban lévő hozzáférés-kezelés az Azure-hoz. Előfizetéshez való hozzáférés korlátozva, az előfizetés rendszergazdája, és az erőforrásokhoz való hozzáférés is korlátozható a felhasználói szerepkör alapján.
- Egy üzembe helyezett IaaS Active Directory-példány üzembe helyezett IaaS virtuális gépek operációsrendszer-szintű Identitáskezelés biztosít.

### <a name="security"></a>Biztonság
**Titkok kezelése**: A megoldás [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) kulcsok és titkos kulcsok felügyeletéhez. Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. Az Azure Key Vault segít az IaaS virtuális gépek lemeztitkosítási kulcsokat és titkos kódokat, a referenciaarchitektúra.

**Javítások kezelése**: Windows virtuális gépek az Azure biztonsági és megfelelőségi tervezet Automation által üzembe helyezett automatikusan frissítéseket kapjanak a Windows Update szolgáltatás alapértelmezés szerint vannak konfigurálva. Ez a megoldás is telepíti az Azure Automation megoldás, amelyek Frissítéstelepítések üzembe helyezése javítások Windows kiszolgálókon szükség esetén hozható létre.

**Kártevők elleni védekezés**: [A Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) tartozó virtuális gépeket biztosít, amellyel a vírusok, kémprogramok és más kártevők azonosításában és valós idejű védelem funkció, konfigurálható riasztásokkal, ha ismert kártevő vagy nemkívánatos szoftver megkísérli Telepítse, vagy a védett virtuális gépek futtatásához.

**Az Application Gateway**: Az architektúra csökkenti a biztonsági réseket a webalkalmazási tűzfal (WAF) Application Gateway és az OWASP szabálykészletben engedélyezve van. További képességek:

- [End-to-End-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Engedélyezése [SSL-alapú Kiszervezéshez](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Tiltsa le [a TLS 1.0 és 1.1 verzió](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Webalkalmazási tűzfal](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (WAF mód)
- [Megelőzés üzemmód](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) az OWASP 3.0 szabálykészletben

### <a name="business-continuity"></a>Az üzletmenet folytonossága

**Magas rendelkezésre állású**: Legalább egy virtuális gép érhető el egy tervezett vagy nem tervezett karbantartási események esetén teljesíti a 99,95 %-os Azure SLA-t. A megoldás üzembe helyezése az összes webes szint, és adatokat csomagban lévő virtuális gépek egy [rendelkezésre állási csoport](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Rendelkezésre állási csoportok biztosítják, hogy a virtuális gépek rendelkezésre állás javítása érdekében több elkülönített hardverfürt között legyenek elosztva. Ezenkívül ez a megoldás üzembe helyezi az SQL Server virtuális gépek rendelkezésre állási csoportba, egy [AlwaysOn rendelkezésre állási csoport](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview). Az Always On rendelkezésre állási csoport funkciót a magas rendelkezésre állású és vész-helyreállítási lehetőségeket biztosít.

**Recovery Services-tároló**: A [Recovery Services-tároló](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) Kezelőkód biztonsági mentési adatokat, és védelmet biztosít az összes konfiguráció az Azure Virtual Machines ebben az architektúrában. Recovery Services-tárolóval, ügyfelek is fájlok és mappák visszaállítása az IaaS virtuális gépről a teljes virtuális Gépet, így gyorsabb helyreállítást visszaállítása nélkül.

**Felhőbeli tanúsító**: [Felhőbeli tanúsító](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) egy feladatátvevő fürt kvórum tanúsítójának a Windows Server 2016, amely Azure-t az egyeztetési pontként típusú. A Felhőbeli tanúsító – más kvórumtanúsítókhoz, például szavazattal rendelkezik, és részt vehet a kvórumszámításokban, de a standard szintű, nyilvánosan elérhető Azure Blob Storage használ. Ezzel elkerülhető a felesleges karbantartást a nyilvános felhőben üzemeltetett virtuális gépek.

### <a name="logging-and-auditing"></a>Naplózás és vizsgálat

A log Analytics biztosít széles körű naplózását, a rendszer és a felhasználói tevékenységek, valamint a helyrendszer állapotát. A [Log Analytics](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) megoldás gyűjti és elemzi az adatokat az Azure-erőforrások által létrehozott és a helyszíni környezetekben.

- **Tevékenységnaplók:**  [A Tevékenységnaplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) adjon meg egy előfizetéshez tartozó erőforrásokon végrehajtott műveletekkel kapcsolatos információk. A Tevékenységnaplók segítségével határozza meg a műveletet kezdeményező, az eseményt, és állapot ideje.
- **Diagnosztikai naplók:**  [Diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) minden erőforrás által kibocsátott az összes napló is. Ezek a naplók például a Windows rendszer-eseménynaplói, az Azure storage-naplók, a Key Vault-naplók és az Application Gateway hozzáférés és a tűzfal a naplókat.
- **Jelentkezzen be az archiválás:**  Az összes diagnosztikai naplók írni egy központosított, titkosított csatornákon történik az Azure storage-fiókját archiválási. A megőrzési felhasználó által konfigurálható, mentése és 730 nap között, a megőrzési a szervezet konkrét követelményeinek. Ezek a naplók csatlakozhat Azure Log Analytics feldolgozási, tárolására és-irányítópult jelentéseit.

Ezenkívül a következő figyelési megoldásokat, ez az architektúra részeként települnek. Ne feledje, hogy az ügyfél felelőssége, hogy ezek a megoldások, a FedRAMP biztonsági vezérlők igazítása konfigurálása:
-   [Az AD Assessmenthez](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Az Active Directory állapotának ellenőrzése megoldás a kockázat és kiszolgálói környezetek állapotát értékeli a rendszeres időközönkénti, és a telepített kiszolgálói infrastruktúra vonatkozó javaslatok rangsorolt listáját tartalmazza.
-   [Kártevőirtók felmérése](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): A kártevőirtó megoldás jelentések a kártevő szoftverek, a fenyegetések és a védelem állapota.
-   [Az Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): Az Azure Automation-megoldás tárolja, fut, és kezeli a runbookok.
-   [Biztonság és auditálás](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): A biztonsági és auditálási irányítópultja az erőforrások biztonsági állapotát egy magas szintű betekintést nyújt a metrikák azáltal, hogy a biztonsági tartományok, a jelentős problémák, a észlelések, a fenyegetésekkel kapcsolatos Tudásbázis és a gyakori biztonsági lekérdezések.
-   [SQL-értékeléssel](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): Az SQL Health Check megoldás a kockázat és kiszolgálói környezetek állapotát értékeli a rendszeres időközönkénti, és nyújt a felhasználók számára a telepített kiszolgálói infrastruktúra vonatkozó javaslatok rangsorolt listáját.
-   [Frissítéskezelés](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): Az Update Management megoldás lehetővé teszi, hogy az ügyfél felügyeleti operációs rendszer biztonsági frissítéseket, beleértve egy elérhető frissítések állapotát és a szükséges frissítések telepítése.
-   [Az ügynök állapota](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Az Agent Health megoldás hány ügynök van telepítve, és a földrajzi elosztás, valamint hány ügynök, amely nem válaszol és működési adatokat küld be, amely az ügynökök számát jelenti.
-   [Azure-Tevékenységnaplók](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Az Activity Log Analytics megoldás az ügyfél az összes Azure-előfizetések az Azure-Tevékenységnaplók elemzésének segíti.
-   [A változáskövetés](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): A Change Tracking megoldás lehetővé teszi, hogy az ügyfelek számára, hogy könnyen azonosíthassa a változtatásokat a környezetben.

**Az Azure Monitor**
[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) segít a felhasználóknak a teljesítmény, biztonság fenntartására és azonosíthatja a trendeket lehetővé teszi a cégeknek naplózása, riasztásokat hozhat létre, és archiválja az adatokat, beleértve az API-hívások nyomon követése az ügyfelek Azure-erőforrások.

## <a name="threat-model"></a>Fenyegetések modellezése
A referenciaarchitektúra az adatfolyam-diagram érhető el az [letöltése](https://aka.ms/fedrampWAdfd) vagy alább találja. Ez a modell segítségével az ügyfelek megismerhetik a pontokat a rendszer infrastruktúra esetleges kockázat, ha a végzett módosítások.

![IaaS-webalkalmazás FedRAMP fenyegetések modellezése](images/fedramp-iaaswa-threat-model.png?raw=true "FedRAMP fenyegetések modellezése az IaaS-webalkalmazás")

## <a name="compliance-documentation"></a>Megfelelőségi dokumentációja

A [Azure biztonsági és megfelelőségi terv - FedRAMP magas vevő felelőssége mátrix](https://aka.ms/blueprinthighcrm) sorolja fel a magas szintű FedRAMP-alapkonfiguráció által igényelt összes biztonsági vezérlő. A mátrix azt jelzi, hogy az egyes vezérlőelemek végrehajtása Microsoft, az ügyfél felelőssége, vagy a kettő között megosztott.

A [Azure biztonsági és -megfelelőségi terv - FedRAMP IaaS webes alkalmazás magas ellenőrzési megvalósítási mátrix](https://aka.ms/blueprintwacim) sorolja fel a magas szintű FedRAMP-alapkonfiguráció által igényelt összes biztonsági vezérlő. A mátrix, amelyen a vezérlők esnek az IaaS webalkalmazás-architektúra, beleértve a részletes leírását, hogyan végrehajtása megfelel egyes az érintett vezérlő információkat tartalmaz.

## <a name="deploy-the-solution"></a>A megoldás üzembe helyezése

Az Azure biztonsági és megfelelőségi tervezet Automation JSON konfigurációs fájloknak és a PowerShell-szkriptek üzembe helyezéséhez az Azure-erőforrások Azure Resource Manager API szolgáltatás által kezelt áll. Részletes üzembe helyezési utasítások [Itt](https://aka.ms/fedrampblueprintrepo).
> [!NOTE]
> Ez a megoldás üzembe helyezi az Azure Governmentre.

#### <a name="quickstart"></a>Első lépések
1. Klónozás vagy letöltés [ez](https://aka.ms/fedrampblueprintrepo) a helyi munkaállomáson GitHub-adattárban.

2. A központi telepítés előtti PowerShell-parancsprogrammal: azure-blueprint/predeploy/Orchestration_InitialSetup.ps1.

3. Az alábbi gombra kattintva jelentkezzen be az Azure Portalra, adja meg a szükséges ARM-sablon paramétereit, majd kattintson **beszerzési**.

    [![Üzembe helyezés az Azure-ban](http://azuredeploy.net/AzureGov.png)](https://portal.azure.us/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Ffedramp-iaas-webapp%2Fmaster%2Fazuredeploy.json)

## <a name="guidance-and-recommendations"></a>Útmutatás és javaslatok
### <a name="vpn-and-expressroute"></a>VPN és ExpressRoute
Biztonságos VPN-alagúton vagy [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) konfigurálni kell a biztonságos kapcsolatot az IaaS-webalkalmazás referenciaarchitektúra részeként üzembe helyezett erőforrásokat. Megfelelően állítja be a VPN-t vagy ExpressRoute, az ügyfelek hozzáadhatja egy védelmi réteget biztosít adatokat átvitel közben.

Az Azure-ral biztonságos VPN-alagút alkalmazásával egy helyszíni hálózat és a egy Azure virtuális hálózat közötti virtuális magánhálózati kapcsolat hozható létre. Ezt a kapcsolatot az interneten keresztül történik, és lehetővé teszi az ügyfelek számára, hogy egy titkosított kapcsolatot, az ügyfél hálózati és az Azure között lévő biztonságosan "csatorna" információkat. Site-to-Site VPN egy olyan biztonságos, érett technológia, évtizedes a vállalatok által központilag telepített. A [IPsec-alagút módú](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) ezt a beállítást, mint egy titkosítási mechanizmus használatban van.

Forgalom a VPN-alagút haladnak át a helyek közötti VPN-nel az interneten, mert a Microsoft kínál egy másik, még biztonságosabb kapcsolat lehetőséget. Az Azure ExpressRoute dedikált WAN Azure-ban és a egy helyszíni helyhez vagy egy Exchange-szolgáltató közötti kapcsolat. Az ExpressRoute-kapcsolatok az interneten keresztül halad, mivel a kapcsolatok az interneten keresztül ajánlja fel a további megbízhatóság megbízhatóbbak, gyorsabbak, kisebb a késésük, és biztonságosabbak a szokásos internetkapcsolatoknál. Továbbá mivel ez az ügyfél távközlési szolgáltató közvetlen kapcsolatot, az adatok nem az interneten keresztül továbbítani tartalmaz, és ezért nem érhető el.

Ajánlott eljárások végrehajtásához egy biztonságos hibrid hálózatot, amely kiterjeszti a helyszíni hálózatot az Azure-bA [elérhető](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Jogi nyilatkozat

- Ez a dokumentum csak tájékoztatási célokat szolgál. A MICROSOFT NEM VÁLLAL SEMMILYEN KIFEJEZETT, KIFEJEZETT, VÉLELMEZETT VAGY KÖTELEZŐ GARANCIÁT A DOKUMENTUMBAN SZEREPLŐ INFORMÁCIÓRA VONATKOZÓAN. Ez a dokumentum biztosított "as-van." Információk és vélemények ebben a dokumentumban URL-CÍMEK és más internetes webhelyekre utaló hivatkozások értesítés nélkül változhatnak. Ez a dokumentum olvasásakor ügyfelek felelősségére használja.  
- Ez a dokumentum nem biztosít semmilyen jogot semmilyen Microsoft-termék vagy a megoldások a szellemi tulajdonhoz ügyfelek.  
- Ügyfelek másolhatja és használhatja ezt a dokumentumot belső, hivatkozási célokból.  
- Bizonyos ajánlások a dokumentum eredményezhet megnövekedett adat-, hálózati vagy számítási erőforrás-használat az Azure-ban, és előfordulhat, hogy növelje az ügyfél Azure licencek vagy előfizetések költségeit.  
- Ez az architektúra célja, hogy az ügyfelek számára az adott követelményekhez beállítása alapjaként szolgálja ki, és nem használható-éles környezetben van.
- Ez a dokumentum referenciaként fejlesztik, és nem használható minden olyan eszközt, amely szerint egy ügyfél megfelel a megadott megfelelőségi követelmények és előírások meghatározásához. Ügyfelek jóváhagyott megvalósítás az ügyfeleknél a szervezet jogi támogatást kell kérnie.
