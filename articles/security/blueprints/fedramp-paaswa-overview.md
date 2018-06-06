---
title: Azure biztonsági és megfelelőségi tervezetének - PaaS-webalkalmazás FedRAMP
description: Azure biztonsági és megfelelőségi tervezetének - PaaS-webalkalmazás FedRAMP
services: security
author: jomolesk
ms.assetid: 41570fc1-4d74-48ed-afb0-ef1be857029e
ms.service: security
ms.topic: article
ms.date: 06/01/2018
ms.author: jomolesk
ms.openlocfilehash: 20aa842fb8168bc28a388c817f4e4eedbdd63ebd
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34727776"
---
# <a name="azure-security-and-compliance-blueprint-paas-web-application-for-fedramp"></a>Azure biztonsági és megfelelőségi tervezetének: PaaS-webalkalmazás FedRAMP

## <a name="overview"></a>Áttekintés

A [Szövetségi kockázat és engedélyezési felügyeleti Program (FedRAMP)](https://www.fedramp.gov/) egy Egyesült Államok kormánya kiterjedő program, amely a biztonsági értékelést, engedélyezési és állandó felügyelet szabványosított megközelítését ismerteti a felhő termékei és szolgáltatásai. Az Azure biztonsági és megfelelőségi tervezetének útmutatással szolgál hogyan telepíthet egy Microsoft Azure platform, egy szolgáltatás (PaaS) architektúra, amely segít a FedRAMP nagy vezérlők egy részhalmazát valósítja meg. Ez a megoldás a nyújt útmutatást a központi telepítés és az Azure-erőforrások konfigurációs egy közös referencia-architektúrában, módon, ahol az ügyfelek bizonyos biztonsági és megfelelőségi követelményeknek megfelel, amely tartalmazza, és alapjaként szolgál az ügyfelek számára hozza létre, és a saját megoldások konfigurálása az Azure-on.

A referencia-architektúrában, tartozó vezérlőelem alkalmazási útmutatók és fenyegetés modellek célja, hogy az ügyfelek úgy, hogy az egyedi követelményeket alapjaként szolgálnak, és nem használható mint – egy termelési környezetben. Erre a környezetre módosítás nélkül az alkalmazások központi telepítése nem elegendő teljesen követelményeinek teljesítése érdekében az FedRAMP nagy alaptervhez. Vegye figyelembe a következőket:
- Az architektúra munkaterhelések telepítése az Azure-bA FedRAMP megfelelő módon ügyfelek alapterv.
- Ügyfelek végző megfelelő biztonsági és megfelelőségi értékelése használatával készített ebbe az architektúrába követelmények változhatnak megoldások a részletekről az egyes ügyfelek általi alapján.

## <a name="architecture-diagram-and-components"></a>Architektúrája és összetevői
A megoldás egy referencia-architektúrában biztosít az Azure SQL Database-háttérrendszerrel PaaS webalkalmazás. A webalkalmazás elkülönített Azure App Service-környezetek, amelyek egy saját, dedikált környezet egy Azure-adatközpontban található van tárolva. Azure által kezelt virtuális gépek közötti forgalmat a webes alkalmazás egyenlegének a környezet betöltése. Ez az architektúra a hálózati biztonsági csoportok, egy Application Gateway, az Azure DNS-ben és a terheléselosztó is tartalmaz. Az Operations Management Suite továbbá valós idejű elemzési rendszerállapot és a biztonsági biztosít. **Azure azt javasolja, hogy a referencia architektúra alhálózati történő felügyelete és az adatok importálása a VPN- vagy ExpressRoute-kapcsolat beállítása.**

![A PaaS-webalkalmazás FedRAMP referencia architektúra diagramja](images/fedramp-paaswa-architecture.png?raw=true) "PaaS-webalkalmazás FedRAMP referencia architektúra diagramja"

A megoldás az Azure-szolgáltatásokat. Az üzembe helyezési architektúrája részleteit találhatók a [üzembe helyezési architektúrája](#deployment-architecture) szakasz.

- Azure Active Directory
- Azure Key Vault
- Azure SQL Database
- Application Gateway
    - (1) webalkalmazási tűzfal
        - tűzfal módban: megelőzése
        - Szabálykészlet: OWASP 3.0
        - figyelő: 443-as port
- Azure virtuális hálózat
- Network security groups (Hálózati biztonsági csoportok)
- Azure DNS
- Azure Storage
- Operations Management Suite
- Azure Monitor
- App Service Environment v2
- Azure Load Balancer
- Azure Web App
- Azure Resource Manager

## <a name="deployment-architecture"></a>Üzembe helyezési architektúrája
Az alábbi szakasz részletesen üzembe helyezése és megvalósítás elemek.

**Az Azure Resource Manager**: [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) lehetővé teszi az ügyfelek az alkalmazásában lévő erőforrásokat csoportként megoldásban. Az ügyfelek telepítéséhez, frissítéséhez, vagy törli az összes erőforrást a megoldás egyetlen, koordinált műveletben. Ügyfelek használjon központi telepítési sablont, és, hogy a sablon különböző, például tesztelési, átmeneti és üzemi környezetekben is képes működni. A Resource Manager biztosít a biztonsági, naplózási és címkézési szolgáltatásokat ügyfelek számára a telepítés utáni kezelhetik az erőforrásaikat.

**App Service Environment-környezet v2**: A [Azure App Service környezetben (ASE)](https://docs.microsoft.com/azure/app-service/environment/intro) egy teljesen elkülönített és dedikált környezetben történő biztonságos futtatására, nagy méretekben App Service-alkalmazás az App Service szolgáltatás.

ASEs csak az egyetlen ügyfél-alkalmazások futtatására elkülönített vannak, és mindig telepített virtuális hálózatba. Ügyfelek mindkét bejövő és kimenő hálózati forgalom részletes szabályozhatják, és alkalmazások képes kapcsolatot létrehozni a nagy sebességű biztonságos helyszíni vállalati erőforrások virtuális hálózatokon keresztül.

Ebbe az architektúrába ASEs használata engedélyezett a következő vezérlők konfigurációk:

- Egy védett Azure virtuális hálózaton belül gazdagép és a hálózati biztonsági szabályok
- Önaláírt ILB tanúsítványt a HTTPS-kommunikációra konfigurált ASE
- [Belső terheléselosztási mód](https://docs.microsoft.com/azure/app-service-web/app-service-environment-with-internal-load-balancer)
- Tiltsa le a [TLS 1.0](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Változás [TLS titkosító](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Vezérlő [bejövő forgalom N/W portok](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic)
- [Webalkalmazási tűzfal – adatok](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- Engedélyezése [Azure SQL adatbázis-forgalom](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-network-architecture-overview)

A [segítséget és javaslatokat](#guidance-and-recommendations) szakasz tartalmaz ASEs további információkat.

**Az Azure Web App**: [Azure Web Apps](https://docs.microsoft.com/azure/app-service/) lehetővé teszi az ügyfelek felépítéséhez és az általuk választott programozási nyelven webalkalmazások üzemeltetéséhez infrastruktúra kezelése nélkül. Automatikus méretezést és magas rendelkezésre állást kínál, támogatja a Windows és a Linux rendszert is, valamint lehetővé teszi az automatikus telepítéseket a GitHub, Visual Studio Team Services vagy bármely egyéb Git-adattárból.

### <a name="virtual-network"></a>Virtual Network
Az architektúra egy címterében 10.200.0.0/16 titkos virtuális hálózat határozza meg.

**Hálózati biztonsági csoportok**: [hálózati biztonsági csoportokkal (NSG-k)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) tartalmaz, amely a virtuális hálózaton belül adatforgalom engedélyezéséhez vagy letiltásához hozzáférés-vezérlési listák. Az NSG-k segítségével biztonságos egy alhálózat vagy az egyes virtuális gép szintjén-forgalmat. A következő NSG-k érhetők el:
- Az Alkalmazásátjáró 1 NSG
- App Service Environment-környezet 1 NSG
- Az Azure SQL Database 1 NSG

Az NSG-k, amelyek mindegyikének egyedi portokat és protokollokat nyissa meg, hogy a megoldás megfelelően és biztonságosan együttműködhet. Ezenkívül a következő konfigurációk engedélyezve vannak az egyes NSG:
  - [Diagnosztikai naplók és események](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) engedélyezve van, és a storage-fiókban tárolt
  - OMS szolgáltatáshoz csatlakozik-e a [NSG-t a diagnosztika](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Alhálózatok**: az egyes alhálózatokon társítva a megfelelő NSG.

**Az Azure DNS**: A tartománynévrendszer vagy DNS-, felelős fordítása (vagy feloldása) az IP-címét egy webhely vagy szolgáltatás neve. [Az Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) DNS-tartományok üzemeltetési szolgáltatás, amely az Azure-infrastruktúra használatával névfeloldást biztosít. Az Azure-tartományt üzemeltet, amelyet a felhasználók kezelhetik a DNS-rekordok ugyanazon hitelesítő adatokkal, API-k, eszközök segítségével, és más Azure-szolgáltatásokkal, számlázási. Az Azure DNS támogatja a saját DNS-tartományok is.

**Az Azure Load Balancer**: [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) lehetővé teszi a felhasználóknak az alkalmazások, és hozzon létre magas rendelkezésre állású szolgáltatások. Terheléselosztó bejövő, valamint az olyan kimenő-forgatókönyveket teszi lehetővé, és kis késés, nagyobb teljesítményt nyújt, és akár több millió összes TCP és UDP-alkalmazás flow méretezi.

### <a name="data-in-transit"></a>Adatokat átvitel közben
Azure alapértelmezés szerint minden kommunikáció és az Azure adatközpontjaiban titkosítja. Az Azure portálon keresztül Azure Storage összes tranzakciója történik a HTTPS használatával.

### <a name="data-at-rest"></a>Inaktív adat
Az architektúra történik a titkosítás, az adatbázis naplózását, és egyéb adatok védelmét.

**Az Azure Storage**: titkosított adatok rest követelmények teljesítéséhez összes [Azure Storage](https://azure.microsoft.com/services/storage/) használ [Storage szolgáltatás titkosítási](https://docs.microsoft.com/azure/storage/storage-service-encryption).

**Az Azure Disk Encryption**
[Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) kihasználja a Windows adatlemezek kötettitkosítást biztosít a BitLocker-szolgáltatás. A megoldás integrálódik az Azure Key Vault szabályozni, és a lemez-titkosítási kulcsok kezeléséhez.

**Az Azure SQL Database**: az Azure SQL Database-példányt használja a következő adatbázis biztonsági intézkedéseket:
-   [AD hitelesítési és engedélyezési](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) adatbázis-felhasználók és más Microsoft-szolgáltatásokban egyetlen központi helyen identitás kezelését teszi lehetővé.
-   [SQL-adatbázis naplózásának](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) nyomon követi az adatbázisok események, mind az írás őket naplózási jelentkezzen be Azure storage-fiók.
-   Az Azure SQL-adatbázis használatára van konfigurálva [átlátszó Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), melyik teljesít valós idejű titkosítási és visszafejtési az adatbázis, a társított biztonsági másolatok, és a rest-tranzakciós naplófájlok a következő információk védelme érdekében.
-   [Tűzfal-szabályok](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) tagadni a hozzáférést minden adatbázis-kiszolgálók csak a megfelelő engedélyekkel. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján.
-   [A Fenyegetésészlelés SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) lehetővé teszi, hogy felderítésére és a lehetséges veszélyforrásokra választ, adja meg a biztonsági riasztások adatbázis gyanús tevékenységek, a potenciális biztonsági réseket, az SQL injektálási támadások és a rendellenes adatbázis-hozzáférési előforduló minták.
-   [Mindig a titkosított oszlopok](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) győződjön meg arról, hogy bizalmas adatok soha nem jelenik meg az adatbázis rendszerében szövegként. Miután engedélyezte az adattitkosítást, csak az ügyfélalkalmazások vagy a kulcsoknak access rendelkező alkalmazáskiszolgáló hozzáférhet egyszerű szöveges adatokat.
- [A sorszintű biztonsági](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) lehetővé teszi, hogy a felhasználók házirendek adatok feldolgozási megszüntetheti való hozzáférésének korlátozásához.
- [SQL-adatbázis dinamikus adatmaszkolási](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) teheti, miután telepíti a referencia-architektúrában. Állítsa be a dinamikus adatmaszkolási beállítások igazodnia kell az adatbázis-séma esetén szükséges.

### <a name="identity-management"></a>Identitáskezelés
Az alábbi technológiákat biztosítja az identitás Eszközkezelési funkciókat az Azure környezetben:
-   [Az Azure Active Directory](https://azure.microsoft.com/services/active-directory/) a Microsoft több-bérlős felhőalapú címtár- és identitáskezelési szolgáltatás. Ebben a megoldásban minden felhasználó az aad-ben, beleértve az Azure SQL Database hozzáférő felhasználók jönnek létre.
-   Hitelesítés az alkalmazás aad-ben alapján történik. További információkért lásd: [alkalmazások integrálása az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Emellett az adatbázis oszlopok titkosítását az Azure Active Directory hitelesítéséhez az Azure SQL Database-alkalmazás. További információkért lásd: hogyan [bizalmas adatokat az Azure SQL-adatbázis védelme](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) lehetővé teszi, hogy pontosan célzott hozzáférés-kezelés az Azure-bA. Előfizetés hozzáférés korlátozódik előfizetés-rendszergazdaként, és az erőforrásokhoz való hozzáférés korlátozható a felhasználói szerepkör alapján.
- [Az Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) lehetővé teszi, hogy a felhasználók számának minimalizálása érdekében az ügyfelek, akik bizonyos információkhoz.  Rendszergazdák AAD Privileged Identity Management segítségével ismerje meg, korlátozása, és a kiemelt jogosultságú identitások és azok erőforrásokhoz való hozzáférés figyelésére. Ez a funkció érvényesítését igény szerint közvetlenül az időponthoz kötött rendszergazdai hozzáférés szükség esetén is használható.
- [Az Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) észleli a potenciális biztonsági réseket egy szervezet identitásait érintő, konfigurálja az automatikus válaszokat ad egy szervezet identitásait kapcsolódó észlelt gyanús tevékenységek és pedig megvizsgálja a gyanús incidensek a megfelelő művelettel hárítsa el őket.

### <a name="security"></a>Biztonság
**Titkos kulcsok kezelése** a megoldás az [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) kulcsok és titkos kulcsok kezeléséhez. Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. Az alábbi képességeket Azure Key Vault segít a felhasználóknak az adatok és az ezekhez az adatokhoz való hozzáférés védelme:
- Speciális hozzáférési házirendek konfigurálása a szükséges alapul.
- Kulcstároló hozzáférési házirendeket a kulcsok és titkos minimálisan szükséges engedélyeket.
- Minden kulcsok és titkos kulcs tárolóban lévő állapottal rendelkezik lejáratának dátumát.
- A Key Vault minden kulcs speciális hardveres biztonsági modulokkal (HSM) védett. A kulcs HSM-védelemmel 2048 bites RSA-kulcs esetében.
- Minden felhasználó és identitások szerepköralapú hozzáférés-vezérlés használatával minimálisan szükséges engedélyeket kapnak.
- Diagnosztikai naplókat a Key Vault legalább 365 nap megőrzési idővel rendelkező engedélyezve van.
- A kulcsok engedélyezett titkosítási műveleteket a meglévők közül szükséges korlátozódnak.

**Alkalmazásátjáró** architektúrájának csökkenti a biztonsági rések Alkalmazásátjáró használata webalkalmazási tűzfal, és az engedélyezett OWASP szabálykészletben kockázatát. További funkciók a következők:
- [End-to-End-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Engedélyezése [SSL kiürítése](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Tiltsa le a [TLS 1.0 és 1.1 verzió](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Webalkalmazási tűzfal](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview)
- [Megelőző módja](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) rendelkező OWASP 3.0 szabálykészletben
- Engedélyezése [diagnosztikai naplózás](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Egyéni állapot-mintavételi csomagjai](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- [Az Azure Security Center](https://azure.microsoft.com/services/security-center) és [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) adja meg a további védelmi és értesítések. Az Azure Security Center megbízhatóságibesorolás-kezelési rendszert is tartalmaz.

### <a name="logging-and-auditing"></a>Naplózás és naplózás
Az Operations Management suite rendszer és felhasználói tevékenységek, valamint a rendszerállapot kiterjedt naplózás biztosít. Az Operations Management suite [Naplóelemzési](https://azure.microsoft.com/services/log-analytics/) megoldás gyűjti és elemzi az adatokat az Azure-erőforrások által létrehozott és a helyszíni környezetben.
- **Tevékenységi naplóit**: [tevékenységi naplóit](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) előfizetés erőforrásainak végrehajtott műveletek betekintést nyújtanak. Tevékenységi naplóit is meghatározásához, hogy egy művelet kezdeményező az előfordulási és állapot ideje.
- **Diagnosztikai naplók**: [diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) minden erőforrás által kibocsátott összes naplók tartalmazzák. Ezek a naplók tartalmazzák a Windows rendszer-eseménynaplói, Azure Storage naplókat, Key Vault-naplók és Alkalmazásátjáró hozzáférés és a tűzfalon naplók.
- **Napló Archiválás**: minden diagnosztikai naplók írni egy központosított és titkosított Azure storage-fiókjához archiválási. A megőrzési felhasználó által konfigurálható, mentése 730 nap szervezet-specifikus megőrzési követelményeinek megfelelően. Ezek a naplók feldolgozása, tárolására és irányítópult reporting Azure Naplóelemzés csatlakozni.

Emellett a következő Operations Management suite megoldások tartoznak ebbe az architektúrába részeként:
-   [Az Active directory Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): az Active Directory állapotát ellenőrző megoldás a kockázat és környezetek állapotának értékelésére rendszeres időközönkénti, és a telepített kiszolgálói infrastruktúra vonatkozó javaslatok a rangsorolt listáját tartalmazza.
-   [Kártevőirtó Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): A kártevőirtó megoldást jelent a kártevő szoftver, a fenyegetések és a védelmi állapot.
-   [Azure Automation szolgáltatásbeli](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): az Azure Automation-megoldás tárolja, fut, és kezeli a runbookok. Ebben a megoldásban a runbookok naplóinak gyűjtése az Application insights szolgáltatással és az Azure SQL Database segítségével.
-   [Biztonság és a naplózási](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): A biztonság és a naplózási irányítópult erőforrások biztonsági állapotát egy magas szintű betekintést biztosít a biztonsági tartományok, jelentős problémák, észlelések, fenyegetésfelderítési adataival és közös biztonsági lekérdezések metrikák megadásával.
-   [SQL Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): megoldás az SQL állapotának ellenőrzése a kockázat és környezetek állapotának értékelésére rendszeres időközönkénti és nyújt a felhasználók számára a telepített kiszolgálói infrastruktúra vonatkozó javaslatok a rangsorolt listáját.
-   [Frissítéskezelés](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): A frissítés felügyeleti megoldás lehetővé teszi az operációs rendszer biztonsági frissítések, a rendelkezésre álló frissítések állapot és a kötelező frissítések telepítésének folyamatát, például felhasználói kezelését.
-   [Ügynök állapotfigyelő](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): az ügyfélállapot-megoldást jelent, hány ügynök van telepítve, és a földrajzi eloszlása, valamint hány ügynökök, amelyek nem válaszoló és ügynökök, amelyek a működési adatok elküldése.
-   [Az Azure tevékenységi naplóit](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): A tevékenység Naplóelemzési megoldás segítséget nyújt a elemzése az Azure tevékenységi naplóit az ügyfél az összes Azure-előfizetések között.
-   [A változáskövetés](https://docs.microsoft.com/azure/automation/automation-change-tracking): A változáskövetési megoldás lehetővé teszi az ügyfelek azonosítását a változtatásokat a környezetben.

**Az Azure figyelő**
[Azure figyelő](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) segít a felhasználóknak a teljesítmény nyomon követése, biztonsági karbantartása és azonosíthatja a trendeket szervezetek naplózni, létre riasztásokat, és archiválja, beleértve a követési API-hívások engedélyezésével a felhasználók Azure-erőforrások.

## <a name="threat-model"></a>Fenyegetések modellezése

Az adatfolyam-diagram a referenciaarchitektúra érhető el [letöltése](https://aka.ms/fedrampPaaSWebAppDFD) vagy alatt található. Ez a modell segít megérteni a pontokat a rendszer infrastruktúra kockázatokkal módosításakor az ügyfelek.

![FedRAMP fenyegetések modellezése PaaS-webalkalmazás](images/fedramp-paaswa-threat-model.png?raw=true "PaaS webes Applicaiton FedRAMP fenyegetés modell")

## <a name="compliance-documentation"></a>Megfelelőségi dokumentáció
A [Azure biztonsági és megfelelőségi tervezetének - FedRAMP magas ügyfél felelősségi mátrix](https://aka.ms/blueprinthighcrm) sorolja fel a FedRAMP nagy alapkonfiguráció által igényelt összes biztonsági vezérlő. A mátrix azt jelzi, hogy minden vezérlő végrehajtásának feladata a Microsoft, az ügyfél vagy a kettő között megosztott.

A [Azure biztonsági és megfelelőségi tervezetének - FedRAMP PaaS WebApp magas vezérlés megvalósítása mátrix](https://aka.ms/fedrampPaaSWebAppCIM) sorolja fel a FedRAMP nagy alapkonfiguráció által igényelt összes biztonsági vezérlő. A mátrix amelyen vezérlők hatálya a PaaS webes alkalmazás architektúra, például részletes leírást hogyan végrehajtása megfelel az érintett vezérlők információkat nyújt.

## <a name="guidance-and-recommendations"></a>Útmutatás és javaslatok
### <a name="vpn-and-expressroute"></a>VPN- és ExpressRoute
Biztonságos VPN-alagúton vagy [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) kell konfigurálni, hogy biztonságos kapcsolatot létrehozni a PaaS webes alkalmazás referencia-architektúrában részeként telepített erőforrások. Megfelelően állítja be a VPN- vagy ExpressRoute, az ügyfelek megteheti egy védelmi réteget biztosít adatokat az átvitel során.

Az Azure-ral biztonságos VPN-alagúton implementálásával egy a helyszíni hálózat és az Azure virtuális hálózat közötti VPN-kapcsolat hozhatók létre. Ezt a kapcsolatot az interneten keresztül történik, és lehetővé teszi az ügyfelek biztonságosan "tunnel" információkhoz belül az ügyfél hálózati és az Azure közötti egy titkosított kapcsolat. Telephelyek közötti VPN egy biztonságos, érett technológia, amely különböző méretű vállalatok által évtizedeken van telepítve. A [IPsec-bújtatásos mód](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) ezt a beállítást olyan titkosítási módszert használatban van.

Forgalom a VPN-alagút belül haladnak át a telephelyek közötti VPN az internethez, mert a Microsoft egy másik, a megoldás még biztonságosabbá kapcsolat lehetőséget is kínál. Az Azure ExpressRoute egy dedikált WAN Azure és egy helyszíni helyre vagy az Exchange-szolgáltató közötti kapcsolat. ExpressRoute-kapcsolatok az interneten keresztül halad, mivel ezeket a kapcsolatokat további megbízhatóságát, gyorsabb sebességű, kisebb késések fordulnak elő, és kínálnak nagyobb biztonságot nyújtana tipikus kapcsolatok az interneten keresztül. Továbbá mivel ez a közvetlen kapcsolat az ügyfél telekommunikációs szolgáltató, az adatok nem használható az interneten keresztül tartalmaz, és ezért nem érhető el azt.

Ajánlott eljárások a biztonságos hibrid hálózat, amely egy helyi hálózati kibővíti az Azure-bA [elérhető](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Jogi nyilatkozat

 - Ez a dokumentum csak tájékoztatási célokat szolgál. A MICROSOFT NEM VÁLLAL SEMMILYEN KIFEJEZETT, KIFEJEZETT, VÉLELMEZETT VAGY TÖRVÉNYES, HOGY A JELEN DOKUMENTUMBAN SZEREPLŐ INFORMÁCIÓK. Ez a dokumentum biztosított ",-van." Információk és nézetek ebben a dokumentumban, beleértve az URL-CÍMEK és más internetes webhelyet, értesítés nélkül változhatnak. Ez a dokumentum olvasásakor az ügyfelek az alkalmazást saját felelősségére használja.
 - Ez a dokumentum nem biztosít semmilyen jogot semmilyen Microsoft-termék vagy a megoldások található szellemi tulajdonhoz rendelkező ügyfelek.
 - Az ügyfelek azonban másolható és ez a dokumentum belső hivatkozási célokra használja.
 - Bizonyos ajánlások a dokumentum azt eredményezheti, megnövekedett adat-, hálózati vagy számítási erőforrás-használat az Azure-ban, és megnövelheti az ügyfél Azure licencek vagy előfizetések költségeit.
 - Ebbe az architektúrába célja, hogy az ügyfelek úgy, hogy az egyedi követelményeket alapjaként szolgálnak, és nem használható mint – egy termelési környezetben.
 - Ez a dokumentum hivatkozásként fejlesztése, és nem használható minden olyan eszközt, amely szerint az ügyfél megfelel adott megfelelőségi követelmények és szabályok meghatározásához. Az ügyfelek jóváhagyott ügyfél-hitelesítés megvalósításához a szervezetek jogi támogatnia kell kérnie.
