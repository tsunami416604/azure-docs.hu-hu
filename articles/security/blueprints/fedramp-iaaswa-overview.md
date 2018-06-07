---
title: Azure biztonsági és megfelelőségi tervezetének - webalkalmazás infrastruktúra a FedRAMP
description: Azure biztonsági és megfelelőségi tervezetének - webalkalmazás infrastruktúra a FedRAMP
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
ms.openlocfilehash: 6a2a72f46c4d5faacb7d5871f4c917a5cd578e96
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2018
ms.locfileid: "34809165"
---
# <a name="azure-security-and-compliance-blueprint-iaas-web-application-for-fedramp"></a>Azure biztonsági és megfelelőségi tervezetének: IaaS-webalkalmazás FedRAMP

## <a name="overview"></a>Áttekintés

A [Szövetségi kockázat és engedélyezési felügyeleti Program (FedRAMP)](https://www.fedramp.gov) egy Egyesült államokbeli kormányzati kiterjedő program, amely a biztonsági értékelést, engedélyezési és folyamatos figyelés szabványosított megközelítését ismerteti a felhő termékeket és szolgáltatások. Az Azure biztonsági és megfelelőségi tervezetének Automation útmutatással szolgál a központi telepítésben FedRAMP-kompatibilis infrastruktúra egy egy egyszerű Internet felé néző webes alkalmazáshoz megfelelő szolgáltató (IaaS) környezetben is. Ez a megoldás automatizálja az üzembe helyezési és konfigurálási egy közös referencia-architektúrában, amelyben ügyfelek megfelel konkrét biztonsági és megfelelőségi követelmények és szolgál az ügyfelek számára a build alapjaként módon, amely tartalmazza az Azure-erőforrások és a saját megoldások konfigurálása az Azure-on. A megoldás a FedRAMP nagy alaptervből, NIST SP 800-53 alapján szabályozza egy részhalmazát valósítja meg. FedRAMP követelmények és a megoldás kapcsolatos további információkért tekintse meg a [megfelelőségi dokumentáció](#compliance-documentation).
> [!NOTE]
> Ez a megoldás Azure Government telepíti.

Az Azure biztonsági és megfelelőségi tervezetének Automation automatikusan telepíti az infrastruktúra-szolgáltatási webes alkalmazás referencia-architektúrában előre beállított biztonsági vezérlőkkel ügyfelek FedRAMP követelményeknek való megfelelés eléréséhez. A megoldás Azure Resource Manager-sablonok és az erőforrás üzembe helyezési és konfigurációs útmutató PowerShell-parancsfájlok áll.

Ebbe az architektúrába célja, hogy az ügyfelek úgy, hogy az egyedi követelményeket alapjaként szolgálnak, és nem használható mint – egy termelési környezetben. Erre a környezetre módosítás nélkül az alkalmazások központi telepítése nincs teljesen teljesítik a FedRAMP nagy alapterv követelményeinek. Vegye figyelembe a következőket:
- Ezen architektúra FedRAMP megfelelő módon használja a Azure ügyfelek alapterv.
- Ügyfelek végző megfelelő biztonsági és megfelelőségi vizsgálata használatával készített ebbe az architektúrába követelmények változhatnak megoldások a részletekről az egyes ügyfelek általi alapján.

Nézze meg ezt a gyors áttekintést a megoldás működéséről, [videó](https://aka.ms/fedrampblueprintvideo) foglalja össze, és a központi telepítés bemutatására.

Kattintson a [Itt](https://aka.ms/fedrampblueprintrepo) üzembe helyezési utasítások.

## <a name="architecture-diagram-and-components"></a>Architektúrája és összetevői
Ez a megoldás egy referencia-architektúrában, az infrastruktúra-szolgáltatási webalkalmazás egy SQL Server-háttérrendszerrel telepíti. Az architektúra tartalmazza egy webes réteg, adatrétegbeli, Active Directory infrastruktúra, Application Gateway és terheléselosztóhoz. A webes és adatok rétegek telepített virtuális gépek rendelkezésre állási csoport vannak konfigurálva, és az SQL Server-példányokat a magas rendelkezésre állású AlwaysOn rendelkezésre állási csoportban vannak konfigurálva. Virtuális gépek a tartományhoz, és az Active Directory-csoportházirendek segítségével az operációs rendszer szintjén és megfelelőségi beállítások kényszerítése. A megerősített állomás biztonságos kapcsolatot biztosít a rendszergazdák központilag telepített hozzáférését az erőforrásokhoz. **Azure azt javasolja, hogy a referencia architektúra alhálózati történő felügyelete és az adatok importálása a VPN- vagy Azure ExpressRoute-kapcsolat beállítása.**

![FedRAMP referencia architektúra diagramja IaaS-webalkalmazás](images/fedramp-iaaswa-architecture.png?raw=true "IaaS-webalkalmazás FedRAMP referencia architektúra diagramja")

A megoldás az Azure-szolgáltatásokat. Az üzembe helyezési architektúrája részleteit találhatók a [üzembe helyezési architektúrája](#deployment-architecture) szakasz.

- Azure-alapú virtuális gépek
    - (1) megerősített állomás (a Windows Server 2016 Datacenter)
    - (2) az active Directory-tartományvezérlőhöz (Windows Server 2016 Datacenter)
    - (2) az SQL Server-fürt csomópontjának (Windows Server 2016 SQL Server 2017)
    - (2) webkiszolgáló/IIS (Windows Server 2016 Datacenter)
- Rendelkezésre állási csoportok
    - (1) az active Directory-tartományvezérlők
    - (1) az SQL-fürt csomópontjain
    - (1) webkiszolgáló/IIS
- Azure Virtual Network
    - ((1) /16 virtuális hálózatok
    - (5) /24 alhálózatok
    - DNS-beállítások mindkét tartományvezérlőn van beállítva
- Azure Load Balancer
- Azure Application Gateway
    - (1) az Alkalmazásátjáró WAF engedélyezve
        - tűzfal módban: megelőzése
        - Szabálykészlet: OWASP 3.0
        - figyelő: 443-as port
- Azure Storage
    - (7) georedundáns storage-fiókok
- Azure-felhőbe tanúsító
- Recovery Services-tároló
- Azure Key Vault
- Azure Active Directory (Azure AD)
- Azure Resource Manager
- Az Operations Management Suite (OMS)
- Azure Monitor

## <a name="deployment-architecture"></a>Üzembe helyezési architektúrája

Az alábbi szakasz részletesen fejlesztése és megvalósítása elemek.

**Megerősített állomás**: A megerősített állomás a központi hely, amely biztonságos kapcsolatot biztosít a rendszergazdák központilag telepített hozzáférését az erőforrásokhoz. A megerősített állomás NSG kapcsolatait csak a 3389-es TCP-port RDP. Az ügyfelek tovább konfigurálhatják a megerősített gazdagép szervezet rendszer-megerősítési követelmények teljesítéséhez.

### <a name="virtual-network"></a>Virtuális hálózat
Az architektúra egy címterében 10.200.0.0/16 titkos virtuális hálózat határozza meg.

**Hálózati biztonsági csoportok**: Ez a megoldás telepíti egy külön webkiszolgáló alhálózati, adatbázis alhálózati, Active Directory-alhálózathoz és felügyeleti alhálózaton belül egy virtuális hálózati architektúrát erőforrásokat. Alhálózatok logikailag elválasztott hálózati biztonsági csoport szabályokat az egyes alhálózatok korlátozzák a forgalmat, amely csak szükséges rendszer- és felügyeleti funkciókat az alhálózatok között.

Tekintse meg a konfigurációs [hálózati biztonsági csoportok](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) Ez a megoldás üzembe helyezéséhez. Az ügyfelek a fent fájl szerkesztésével konfigurálhatja hálózati biztonsági csoportok [ebben a dokumentációban](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) útmutatóként.

Egyes alhálózatok rendelkezik egy dedikált hálózati biztonsági csoport (NSG):
- Az Alkalmazásátjáró (LBNSG) 1 NSG
- 1 NSG megerősített állomás (MGTNSG)
- Az elsődleges és tartalék tartományvezérlő (ADNSG) 1 NSG
- Az SQL-kiszolgálókra (SQLNSG) 1 NSG
- 1 NSG a webes réteg (WEBNSG)

**Alhálózatok**: az egyes alhálózatokon társítva a megfelelő NSG.

### <a name="data-at-rest"></a>Inaktív adat

Az architektúra inaktív adatok több titkosítási mértékek használatával védi.

**Az Azure Storage**: adatokat nyugalmi titkosítási követelmények teljesítéséhez az összes tárfiók használata [Storage szolgáltatás titkosítási](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

**SQL Server**: SQL-kiszolgáló használatára van konfigurálva [átlátszó Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption), amely végrehajtja a valós idejű titkosítási és visszafejtési adatainak és naplókönyvtárainak fájlok aktívan információk védelme érdekében. TDE biztosítja, hogy a tárolt adatok nem lett bárki hozzáférhet.

Az ügyfelek is beállíthatja a következő SQL Server biztonsági intézkedéseket:
-   [AD hitelesítési és engedélyezési](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) adatbázis-felhasználók és más Microsoft-szolgáltatásokban egyetlen központi helyen identitás kezelését teszi lehetővé.
-   [SQL-adatbázis naplózásának](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) nyomon követi az adatbázisok események, mind az írás őket naplózási jelentkezzen be Azure storage-fiók.
-   [Tűzfal-szabályok](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) tagadni a hozzáférést minden adatbázis-kiszolgálók csak a megfelelő engedélyekkel. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján.
-   [A Fenyegetésészlelés SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) lehetővé teszi, hogy felderítésére és a lehetséges veszélyforrásokra választ, adja meg a biztonsági riasztások adatbázis gyanús tevékenységek, a potenciális biztonsági réseket, az SQL injektálási támadások és a rendellenes adatbázis-hozzáférési előforduló minták.
-   [Mindig titkosítja az oszlopok](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) győződjön meg arról, hogy bizalmas adatok soha nem jelenik meg az adatbázis rendszerében szövegként. Miután engedélyezte az adattitkosítást, csak az ügyfélalkalmazások vagy a kulcsoknak access app kiszolgálók hozzáférhet egyszerű szöveges adatokat.
-   [SQL-adatbázis dinamikus adatmaszkolási](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) teheti, miután telepíti a referencia-architektúrában. Állítsa be a dinamikus adatmaszkolási beállítások igazodnia kell az adatbázis-séma esetén szükséges.

**Az Azure Disk Encryption**: Azure Disk Encryption windowsos infrastruktúra-szolgáltatási virtuális gép lemezeinek titkosított szolgál. [Az Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) kihasználja a Windows operációs rendszer és az adatlemezek kötettitkosítást biztosít a BitLocker-szolgáltatás. A megoldás integrálva van az Azure Key Vault szabályozni, és a lemez-titkosítási kulcsok kezeléséhez.

### <a name="identity-management"></a>Identitáskezelés

Az alábbi technológiákat biztosítja az identitás Eszközkezelési funkciókat az Azure környezetben:
- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) a Microsoft több-bérlős felhőalapú címtár- és identitáskezelési szolgáltatás.
- Egy ügyfél telepített webalkalmazás-hitelesítés használatával végezheti el az Azure AD. További információkért lásd: [alkalmazások integrálása az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).  
- [Azure szerepköralapú hozzáférés-vezérlés (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) lehetővé teszi, hogy pontosan célzott hozzáférés-kezelés az Azure-bA. Előfizetés hozzáférés korlátozódik előfizetés-rendszergazdaként, és az erőforrásokhoz való hozzáférés korlátozható a felhasználói szerepkör alapján.
- Egy telepített IaaS Active Directory példánnyal üzembe helyezett infrastruktúra-szolgáltatási virtuális gépeknél operációsrendszer-szintű identitás-felügyeletet biztosít.

### <a name="security"></a>Biztonság
**Titkos kulcsok kezelése**: A megoldás az [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) kulcsok és titkos kulcsok kezeléséhez. Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. Az Azure Key Vault segít a infrastruktúra-szolgáltatási virtuális gép lemez-titkosítási kulcsok és titkos kulcsokat a referenciaarchitektúra kezelésében.

**Felügyeleti javítás**: az Azure biztonsági és megfelelőségi tervezetének automatizálás által telepített Windows virtuális gépek automatikus frissítések kap a Windows Update szolgáltatás alapértelmezés szerint vannak konfigurálva. Ez a megoldás is telepíti az Azure Automation-megoldást, amelyen keresztül a központi telepítést is létrehozható kiszolgálókra történő központi telepítéséhez javítások Windows szükség esetén.

**Kártevők elleni védekezés**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) virtuális gépek számára lehetővé teszi a valós idejű védelem segítségével azonosíthatja, és távolítsa el a vírusok, kémprogramok és más, kártevő szoftverek, konfigurálható a riasztások Ha ismert kártevő vagy nemkívánatos szoftverek próbálják telepíteni futtassa a védett virtuális gépet.

**Alkalmazásátjáró**: architektúrájának csökkenti a biztonsági réseket Alkalmazásátjáró használata a webalkalmazási tűzfal (waf-ot), és a OWASP szabálykészletben engedélyezve van. További funkciók a következők:

- [End-to-End-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Engedélyezése [SSL kiürítése](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Tiltsa le a [TLS 1.0 és 1.1 verzió](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Webalkalmazási tűzfal](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (WAF mód)
- [Megelőző módja](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) rendelkező OWASP 3.0 szabálykészletben

### <a name="business-continuity"></a>Az üzletmenet folytonossága

**Magas rendelkezésre állású**: legalább egy virtuális gép érhető el egy tervezett vagy nem tervezett karbantartási események esetén a 99,95 % értekezlet Azure SLA-t. A megoldás összes webes réteg telepíti, és adatok rétegben lévő virtuális gépek egy [rendelkezésre állási csoport](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Rendelkezésre állási készletek győződjön meg arról, hogy a virtuális gépek rendelkezésre állásának javítása érdekében több elkülönített hardver fürt különböző pontjain. Továbbá a megoldás telepíti az SQL Server virtuális gépek rendelkezésre állási csoport, egy [AlwaysOn rendelkezésre állási csoport](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview). Az Always On rendelkezésre állási csoport funkciót a magas rendelkezésre állású és vész-helyreállítási lehetőségeket biztosít.

**Recovery Services-tároló**: A [Recovery Services-tároló](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) Kezelőkód biztonsági mentési adatokat, és az összes konfiguráció Azure virtuális gépek ebben az architektúrában védelmét. A Recovery Services-tároló, az ügyfelek is fájlok és mappák visszaállítása egy infrastruktúra-szolgáltatási virtuális gép helyreállítása a teljes virtuális gép, gyorsabb visszaállítás engedélyezése nélkül.

**A felhő tanúsító**: [felhő tanúsító](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) a feladatátvevő fürt kvórum tanúsítójának a Windows Server 2016-os, amely kihasználja az Azure-bA az egyeztetési pont típusa. A felhő tanúsító, mint bármely más kvórum tanúsító szavazás lekérdezi és részt vesznek a kvórumszámításokat, de a szabványos nyilvánosan elérhető Azure Blob Storage használ. Ez megszünteti az extra karbantartási növeli a nyilvános felhőben üzemeltetett virtuális gépeket.

### <a name="logging-and-auditing"></a>Naplózás és naplózás

OMS rendszer és felhasználói tevékenységek, valamint a rendszerállapot kiterjedt naplózás biztosít. Az OMS [Naplóelemzési](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) megoldás gyűjti és elemzi az adatokat az Azure-erőforrások által létrehozott és a helyszíni környezetben.

- **Tevékenységi naplóit:**[tevékenységi naplóit](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) előfizetés erőforrásainak végrehajtott műveletek betekintést nyújtanak. Tevékenységi naplóit is meghatározásához, hogy egy művelet kezdeményező az előfordulási és állapot ideje.
- **Diagnosztikai naplók:**[diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) minden erőforrás által kibocsátott összes naplófájlt. Ezek a naplók tartalmazzák a Windows rendszer-eseménynaplói, az Azure storage naplókat, Key Vault-naplók és Alkalmazásátjáró hozzáférés és a tűzfalon naplókat.
- **Napló archiválás:** összes diagnosztikai naplók írni egy központosított és titkosított Azure storage-fiókjához archiválási. A megőrzési felhasználó által konfigurálható, mentése 730 nap szervezet-specifikus megőrzési követelményeinek megfelelően. Ezek a naplók feldolgozása, tárolására és irányítópult reporting Azure Naplóelemzés csatlakozni.

Emellett a következő OMS-megoldások ebbe az architektúrába részeként telepített. Ne feledje, hogy az ügyfelek felelőssége, hogy ezek a megoldások való megfelelés érdekében FedRAMP biztonsági vezérlők konfigurálása:
-   [AD Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): az Active Directory állapotát ellenőrző megoldás a kockázat és környezetek állapotának értékelésére rendszeres időközönkénti, és a telepített kiszolgálói infrastruktúra vonatkozó javaslatok a rangsorolt listáját tartalmazza.
-   [Kártevőirtó Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): A kártevőirtó megoldást jelent a kártevő szoftver, a fenyegetések és a védelmi állapot.
-   [Azure Automation szolgáltatásbeli](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): az Azure Automation-megoldás tárolja, fut, és kezeli a runbookok.
-   [Biztonság és a naplózási](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): A biztonság és a naplózási irányítópult erőforrások biztonsági állapotát egy magas szintű betekintést biztosít a biztonsági tartományok, jelentős problémák, észlelések, fenyegetésfelderítési adataival és közös biztonsági lekérdezések metrikák megadásával.
-   [SQL Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): megoldás az SQL állapotának ellenőrzése a kockázat és környezetek állapotának értékelésére rendszeres időközönkénti és nyújt a felhasználók számára a telepített kiszolgálói infrastruktúra vonatkozó javaslatok a rangsorolt listáját.
-   [Frissítéskezelés](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): A frissítés felügyeleti megoldás lehetővé teszi az operációs rendszer biztonsági frissítések, a rendelkezésre álló frissítések állapot és a kötelező frissítések telepítésének folyamatát, például felhasználói kezelését.
-   [Ügynök állapotfigyelő](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): az ügyfélállapot-megoldást jelent, hány ügynök van telepítve, és a földrajzi eloszlása, valamint hány ügynökök, amelyek nem válaszoló és ügynökök, amelyek a működési adatok elküldése.
-   [Az Azure tevékenységi naplóit](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): A tevékenység Naplóelemzési megoldás segítséget nyújt a elemzése az Azure tevékenységi naplóit az ügyfél az összes Azure-előfizetések között.
-   [A változáskövetés](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): A változáskövetési megoldás lehetővé teszi az ügyfelek azonosítását a változtatásokat a környezetben.

**Az Azure figyelő**
[Azure figyelő](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) segít a felhasználóknak a teljesítmény nyomon követése, biztonsági karbantartása és azonosíthatja a trendeket szervezetek naplózni, létre riasztásokat, és archiválja, beleértve a követési API-hívások engedélyezésével a felhasználók Azure-erőforrások.

## <a name="threat-model"></a>Fenyegetések modellezése
Az adatfolyam-diagram a referenciaarchitektúra érhető el [letöltése](https://aka.ms/fedrampWAdfd) vagy alatt található. Ez a modell segít megérteni a pontokat a rendszer infrastruktúra kockázatokkal módosításakor az ügyfelek.

![FedRAMP fenyegetések modellezése IaaS-webalkalmazás](images/fedramp-iaaswa-threat-model.png?raw=true "IaaS webes Applicaiton FedRAMP fenyegetés modell")

## <a name="compliance-documentation"></a>Megfelelőségi dokumentáció

A [Azure biztonsági és megfelelőségi tervezetének - FedRAMP magas ügyfél felelősségi mátrix](https://aka.ms/blueprinthighcrm) sorolja fel a FedRAMP nagy alapkonfiguráció által igényelt összes biztonsági vezérlő. A mátrix azt jelzi, hogy minden vezérlő végrehajtásának feladata a Microsoft, az ügyfél vagy a kettő között megosztott.

A [Azure biztonsági és megfelelőségi tervezetének - FedRAMP IaaS webes alkalmazás magas vezérlés megvalósítása mátrix](https://aka.ms/blueprintwacim) sorolja fel a FedRAMP nagy alapkonfiguráció által igényelt összes biztonsági vezérlő. Amikor az infrastruktúra-szolgáltatási webes alkalmazás architektúra, például hogyan végrehajtása megfelel minden érintett vezérlő részletes leírást vezérlők tartoznak a mátrix információival.

## <a name="deploy-the-solution"></a>A megoldás üzembe helyezése

Az Azure biztonsági és megfelelőségi tervezetének Automation JSON-konfigurációs fájlok és az Azure erőforrások telepítése Azure Resource Manager API szolgáltatás által kezelt PowerShell-parancsfájlok áll. Részletes üzembe helyezési utasítások érhetők el [Itt](https://aka.ms/fedrampblueprintrepo).
> [!NOTE]
> Ez a megoldás Azure Government telepíti.

#### <a name="quickstart"></a>Első lépések
1. Klónozza vagy letöltése [ez](https://aka.ms/fedrampblueprintrepo) GitHub-tárházban történő helyi munkaállomáson.

2. A központi telepítés előtti PowerShell-parancsprogrammal: azure-blueprint/predeploy/Orchestration_InitialSetup.ps1.

3. Az alábbi gombra kattintva, jelentkezzen be az Azure-portálra, adja meg a szükséges ARM-sablon paramétereket, és kattintson **beszerzési**.

    [![Üzembe helyezés az Azure-ban](http://azuredeploy.net/AzureGov.png)](https://portal.azure.us/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Ffedramp-iaas-webapp%2Fmaster%2Fazuredeploy.json)

## <a name="guidance-and-recommendations"></a>Útmutatás és javaslatok
### <a name="vpn-and-expressroute"></a>VPN- és ExpressRoute
Biztonságos VPN-alagúton vagy [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) kell konfigurálni, hogy biztonságos kapcsolatot létrehozni a IaaS webes alkalmazás referencia-architektúrában részeként telepített erőforrások. Megfelelően állítja be a VPN- vagy ExpressRoute, az ügyfelek megteheti egy védelmi réteget biztosít adatokat az átvitel során.

Az Azure-ral biztonságos VPN-alagúton implementálásával egy a helyszíni hálózat és az Azure virtuális hálózat közötti virtuális magánhálózati kapcsolat hozhatók létre. Ezt a kapcsolatot az interneten keresztül történik, és lehetővé teszi az ügyfelek biztonságosan "tunnel" információkhoz belül az ügyfél hálózati és az Azure közötti egy titkosított kapcsolat. Telephelyek közötti VPN egy biztonságos, érett technológia, amely különböző méretű vállalatok által évtizedeken van telepítve. A [IPsec-bújtatásos mód](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) ezt a beállítást olyan titkosítási módszert használatban van.

Forgalom a VPN-alagút belül haladnak át a telephelyek közötti VPN az internethez, mert a Microsoft egy másik, a megoldás még biztonságosabbá kapcsolat lehetőséget is kínál. Az Azure ExpressRoute egy dedikált WAN Azure és egy helyszíni helyre vagy az Exchange-szolgáltató közötti kapcsolat. ExpressRoute-kapcsolatok az interneten keresztül halad, mivel ezeket a kapcsolatokat további megbízhatóságát, gyorsabb sebességű, kisebb késések fordulnak elő, és kínálnak nagyobb biztonságot nyújtana tipikus kapcsolatok az interneten keresztül. Továbbá mivel ez a közvetlen kapcsolat az ügyfél telekommunikációs szolgáltató, az adatok nem használható az interneten keresztül tartalmaz, és ezért nem érhető el azt.

Ajánlott eljárások a biztonságos hibrid hálózat, amely egy helyi hálózati kibővíti az Azure-bA [elérhető](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Jogi nyilatkozat

- Ez a dokumentum csak tájékoztatási célokat szolgál. A MICROSOFT NEM VÁLLAL SEMMILYEN KIFEJEZETT, KIFEJEZETT, VÉLELMEZETT VAGY TÖRVÉNYES, HOGY A JELEN DOKUMENTUMBAN SZEREPLŐ INFORMÁCIÓK. Ez a dokumentum biztosított ",-van." Információk és nézetek ebben a dokumentumban, beleértve az URL-CÍMEK és más internetes webhelyet, értesítés nélkül változhatnak. Ez a dokumentum olvasásakor az ügyfelek az alkalmazást saját felelősségére használja.  
- Ez a dokumentum nem biztosít semmilyen jogot semmilyen Microsoft-termék vagy a megoldások található szellemi tulajdonhoz rendelkező ügyfelek.  
- Az ügyfelek azonban másolható és ez a dokumentum belső hivatkozási célokra használja.  
- Bizonyos ajánlások a dokumentum azt eredményezheti, megnövekedett adat-, hálózati vagy számítási erőforrás-használat az Azure-ban, és megnövelheti az ügyfél Azure licencek vagy előfizetések költségeit.  
- Ebbe az architektúrába célja, hogy az ügyfelek úgy, hogy az egyedi követelményeket alapjaként szolgálnak, és nem használható mint – egy termelési környezetben.
- Ez a dokumentum hivatkozásként fejlesztése, és nem használható minden olyan eszközt, amely szerint az ügyfél megfelel adott megfelelőségi követelmények és szabályok meghatározásához. Az ügyfelek jóváhagyott ügyfél-hitelesítés megvalósításához a szervezetek jogi támogatnia kell kérnie.
