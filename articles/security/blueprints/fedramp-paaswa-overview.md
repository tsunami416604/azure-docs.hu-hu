---
title: Azure biztonsági és megfelelőségi terv – a PaaS webes alkalmazás a fedramp-hoz
description: Azure biztonsági és megfelelőségi terv – a PaaS webes alkalmazás a fedramp-hoz
services: security
author: jomolesk
ms.assetid: 41570fc1-4d74-48ed-afb0-ef1be857029e
ms.service: security
ms.topic: article
ms.date: 06/01/2018
ms.author: jomolesk
ms.openlocfilehash: 9bfd748e6b10106587e3fce64e15d7f1f6d50e59
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2019
ms.locfileid: "54261587"
---
# <a name="azure-security-and-compliance-blueprint-paas-web-application-for-fedramp"></a>Azure biztonsági és megfelelőségi terv: PaaS Web Application for FedRAMP

## <a name="overview"></a>Áttekintés

A [Federal Risk and Authorization Management Program (FedRAMP)](https://www.fedramp.gov/) egy Egyesült államokbeli közigazgatásra kiterjedő program, amely a biztonság értékeléséhez, engedélyezési és folyamatos monitorozással szabványos módszert biztosít a felhőhöz termékek és szolgáltatások. Az Azure biztonsági és megfelelőségi terv nyújt útmutatást a Microsoft Azure-platformon kínált egy platformszolgáltatás (PaaS) architektúrát, amely segít megvalósítani egy magas szintű FedRAMP vezérlőelemeket részét. Ez a megoldás telepítését és konfigurálását az Azure-erőforrások általános referenciaarchitektúra, bemutatásához, amelyben ügyfeleink megfelel bizonyos biztonsági és megfelelőségi követelmények módon útmutatást nyújt, és ismeretekkel szolgál az ügyfelek számára hozhat létre, és konfigurálja a saját megoldások az Azure-ban.

Ez a referenciaarchitektúra, társított vezérlőelem végrehajtási útmutatói és enyhítési célja, hogy az ügyfelek számára az adott követelményekhez beállítása alapjaként szolgálja ki, és nem használható-éles környezetben van. Erre a környezetre, módosítás nélkül az alkalmazás üzembe helyezése nem elegendő a teljesen felel meg a magas szintű FedRAMP-alapkonfiguráció követelményeinek. Vegye figyelembe a következőket:
- Az architektúra egy alapkonfiguráció segítenek az ügyfeleknek a számítási feladatok üzembe helyezéséhez az Azure FedRAMP-kompatibilis módon biztosít.
- Ügyfelek végző megfelelő biztonsági és megfelelőségi értékelést bármely megoldás használatával létrehozott ebben az architektúrában követelmények eltérőek lehetnek, hogy milyen ügyről van minden ügyfél megvalósítása alapján.

## <a name="architecture-diagram-and-components"></a>Architektúra és összetevők
Ez a megoldás egy referencia-architektúra biztosít egy PaaS-webalkalmazás, egy Azure SQL Database-háttérrendszerrel. A webalkalmazás elkülönített Azure App Service-környezet, amely egy privát, dedikált környezetben az Azure-adatközpontban lévő üzemeltetett. A környezet elosztja a forgalmat a webes alkalmazás az Azure által felügyelt virtuális gépek között. Ez az architektúra a hálózati biztonsági csoportok, egy Application Gateway, az Azure DNS-ben és a Load Balancer is tartalmaz. Továbbá a Azure Monitor, a fájlrendszer állapotának valós idejű elemzéseket készít. **Az Azure használatát javasolja, hogy a felügyeleti és az adatok importálása a referencia architektúra alhálózatában VPN vagy ExpressRoute-kapcsolat beállítása.**

![PaaS webes alkalmazás a FedRAMP architekturális diagramja](images/fedramp-paaswa-architecture.png?raw=true "PaaS webes alkalmazás a FedRAMP architekturális diagramja")

Ez a megoldás a következő Azure-szolgáltatásokat használ. Az üzembe helyezési architektúra részletek találhatók a [üzembe helyezési architektúrája](#deployment-architecture) szakaszban.

- Azure Active Directory
- Azure Key Vault
- Azure SQL Database
- Application Gateway
    - (1) a webalkalmazási tűzfal
        - Tűzfalmód: megelőzése
        - Szabálykészlet: OWASP 3.0
        - Figyelő: 443-as port
- Azure virtuális hálózat
- Network security groups (Hálózati biztonsági csoportok)
- Azure DNS
- Azure Storage
- Azure Monitor
- App Service Environment v2
- Azure Load Balancer
- Azure Web App
- Azure Resource Manager

## <a name="deployment-architecture"></a>Üzembe helyezési architektúrája
A következő szakaszt az üzembe helyezés és a megvalósítás elemek részletei.

**Az Azure Resource Manager**: [Az Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) lehetővé teszi az ügyfelek számára, hogy a megoldás egy csoportként erőforrásokat. Az ügyfelek központi telepítése, frissítése, vagy törölje az összes erőforrását egyetlen, koordinált műveletben lévő megoldás. Ügyfelek telepítéshez egy sablont használ, és amely különböző, például tesztelési, átmeneti és éles környezetben is képes működni. A Resource Manager biztosít a biztonsági, naplózási és címkézési szolgáltatásokat, hogy az ügyfelek az erőforrások kezelésében a telepítést követően.

**App Service Environment v2**: A [Azure App Service Environment (ASE)](https://docs.microsoft.com/azure/app-service/environment/intro) egy App Service-funkció, amely a biztonságos futtatása nagy méretű App Service-alkalmazások teljesen elkülönített és dedikált környezetet biztosít.

ASE legyenek különítve, csak az egyetlen ügyfél előfizetéséhez alkalmazások futtatására és mindig egy virtuális hálózatban üzemelnek. Mindkét alkalmazás bejövő és kimenő hálózati forgalom részletesen szabályozhatja az ügyfél rendelkezik, és az alkalmazások nagy sebességű, biztonságos kapcsolatot létesíthet virtuális hálózatokon keresztül kapcsolódjanak a helyi vállalati erőforrásokhoz.

Ebben az architektúrában az ASE használata engedélyezett a következő vezérlőket konfigurációk esetén:

- Egy biztonságos Azure virtuális hálózaton belüli üzemeltethet, és a hálózati biztonsági szabály
- ASE önaláírt ILB-tanúsítvány a HTTPS-kommunikációra konfigurálva
- [Belső terheléselosztási mód](https://docs.microsoft.com/azure/app-service-web/app-service-environment-with-internal-load-balancer)
- Tiltsa le [TLS 1.0-s](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Változás [TLS-titkosítás](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Vezérlő [bejövő forgalom N írási/olvasási portok](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic)
- [Webalkalmazási tűzfal – korlátozzák az adatokat](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- Lehetővé teszi [Azure SQL Database-forgalom](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-network-architecture-overview)

A [útmutatást és javaslatokat](#guidance-and-recommendations) szakasz ASE további információkat tartalmaz.

**Azure Web App**: [Az Azure App Service](https://docs.microsoft.com/azure/app-service/) lehetővé teszi ügyfeleink számára hozhat létre és üzemeltethet webalkalmazásokat az általuk választott programozási nyelven infrastruktúra kezelése nélkül. Automatikus méretezést és magas rendelkezésre állást kínál, támogatja a Windows és a Linux rendszert is, valamint lehetővé teszi az automatikus üzembe helyezéseket a GitHub, az Azure DevOps vagy bármely egyéb Git-adattár használatával.

### <a name="virtual-network"></a>Virtual Network
Az architektúra egy 10.200.0.0/16 címtere a privát virtuális hálózat határozza meg.

**Hálózati biztonsági csoportok**: [Hálózati biztonsági csoportok (NSG-k)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) tartalmazza a hozzáférés-vezérlési listák, amelyek engedélyezik vagy megtagadják a forgalmat egy virtuális hálózaton belül. Az NSG-ket egy alhálózatot vagy az egyes Virtuálisgép-szintű forgalom védelmére használható. Az alábbi NSG-k léteznek:
- Az Application Gateway 1 NSG
- Az App Service Environment-környezet 1 NSG
- 1 NSG-t az Azure SQL Database-hez

Az NSG-k mindegyike rendelkezik az adott portokhoz és protokollokhoz meg, hogy a megoldás működhet, biztonságos és megfelelően. Emellett a következő konfigurációk engedélyezve vannak az egyes NSG:
  - [Diagnosztikai naplók és események](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) engedélyezve van, és a storage-fiókban tárolt
  - A log Analytics csatlakozik a [NSG-t a diagnosztika](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Alhálózatok**: Minden egyes alhálózathoz társítva a megfelelő NSG-t.

**Azure DNS**: A Domain Name System, vagy a DNS-beli felelős fordítása (vagy feloldása) az IP-címét a webhely vagy szolgáltatás nevét. [Az Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) DNS-tartományok egy üzemeltetési szolgáltatás, amely Azure-infrastruktúra névfeloldáshoz. Az Azure-ban tartományt üzemeltet, felhasználók ugyanazon hitelesítő adatokkal, API-kkal, eszközökkel és számlázási információkkal más Azure-szolgáltatások DNS-rekordok is kezelheti. Az Azure DNS privát DNS-tartományokat is támogatja.

**Az Azure Load Balancer**: [Az Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) lehetővé teszi az alkalmazások méretezése és magas rendelkezésre állású szolgáltatások létrehozása. Load Balancer bejövő, valamint a kimenő forgatókönyveket teszi lehetővé, és alacsony késleltetésű, nagy teljesítményű, és akár több milliónyi összes TCP és UDP-alkalmazás méretezhető.

### <a name="data-in-transit"></a>Az átvitt adatok
Az Azure és az Azure adatközpontok bemenő kommunikáció alapértelmezés szerint titkosítja. Az Azure Portalon az Azure Storage összes tranzakció HTTPS-kapcsolaton keresztül történik.

### <a name="data-at-rest"></a>Inaktív adat
Az architektúra a titkosítás, az adatbázis naplózási és más intézkedéseket az inaktív adatok védi.

**Az Azure Storage**: Rest-követelményeket, a titkosított adatok kielégítése érdekében minden [Azure Storage](https://azure.microsoft.com/services/storage/) használ [a Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption).

**Az Azure Disk Encryption**
[az Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) az adatlemezek kötettitkosítását adja meg a Windows BitLocker funkcióját használja. A megoldás integrálható az Azure Key Vault segítségével szabályozhatja, és kezelhetik a lemeztitkosítási kulcsokat.

**Azure SQL Database**: Az Azure SQL Database-példány a következő adatbázis biztonsági intézkedések használja:
-   [AD-hitelesítés és engedélyezés](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) lehetővé teszi az identitáskezelést adatbázis-felhasználók és más Microsoft-szolgáltatások egyetlen központi helyen.
-   [Az SQL database naplózási szolgáltatásával](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) nyomon követi az adatbázisok eseményeit és felvezeti ezeket egy naplófájlba, egy Azure storage-fiókban.
-   Az Azure SQL Database használatára van konfigurálva [transzparens adattitkosítási (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), melyik teljesít valós időben titkosítja és fejti vissza az adatbázist, az azokhoz kapcsolódó biztonsági mentési, és a rest-tranzakciós naplófájlokat: adatok védelme érdekében.
-   [Tűzfalszabályok](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) megakadályozzák elérését, adatbázis-kiszolgálók, amíg a megfelelő engedélyekkel. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján.
-   [Az SQL Fenyegetésészlelési](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) lehetővé teszi, hogy az észlelés és válasz a lehetséges veszélyforrásokra, bekövetkezésük pillanatában észlelhessék a biztonsági riasztásokat a gyanús adatbázis-tevékenységekről, a lehetséges biztonsági résekről, az SQL-injektálási támadások és a rendellenes adatbázis-hozzáférés minták.
-   [Mindig titkosított oszlopokat](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) győződjön meg arról, hogy bizalmas adatok soha nem jelenik meg, mint belül az adatbázis-rendszer egyszerű szövegként. Adatok titkosításának engedélyezése után csak az ügyfélalkalmazások vagy alkalmazáskiszolgálók hozzáférést a kulcsokhoz hozzáférhet egyszerű szöveges adatokat.
- [Sorszintű biztonság](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) lehetővé teszi a felhasználóknak a feldolgozási lemondásához adatokhoz való hozzáférés korlátozása a házirendek meghatározása érdekében.
- [Az SQL Database dinamikus adatmaszkolása](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) teheti, miután a referenciaarchitektúra üzembe helyezte. Ügyfelek módosítania kell a dinamikus adatmaszkolási beállítások be kell tartaniuk az adatbázissémát.

### <a name="identity-management"></a>Identitáskezelés
A következő technológiákat biztosítja identitás az eszközkezelési funkciókat az Azure-környezetben:
-   [Az Azure Active Directory](https://azure.microsoft.com/services/active-directory/) van a Microsoft több-bérlős felhőalapú címtár- és identitáskezelési szolgáltatás. Ez a megoldás az összes felhasználó jönnek létre az aad-ben, beleértve az Azure SQL Database hozzáférő felhasználók.
-   Hitelesítés az alkalmazás az AAD használatával történik. További információkért lásd: [alkalmazások integrálása az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Ezenkívül az adatbázis oszloptitkosítás hitelesítheti az alkalmazást az Azure SQL Database, az Azure Active Directory. További információkért lásd: hogyan [bizalmas adatok védelme az Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) lehetővé teszi, hogy pontosan fókuszban lévő hozzáférés-kezelés az Azure-hoz. Előfizetéshez való hozzáférés korlátozva, az előfizetés rendszergazdája, és az erőforrásokhoz való hozzáférés is korlátozható a felhasználói szerepkör alapján.
- [Az Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) lehetővé teszi az ügyfeleknek minimalizálása érdekében a felhasználóknak a számát, akik hozzáférhetnek bizonyos adatokat a.  AAD Privileged Identity Management rendszergazdái a felderítését, korlátozását és emelt szintű identitások és azok erőforrásokhoz való hozzáférésének figyelése. Ez a funkció is használható igény szerinti – igény rendszergazdai hozzáférés kényszerítésére.
- [Az Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) észleli a szervezet identitásait érintő lehetséges biztonsági résekről, konfigurálja az automatikus reakciók, a szervezet identitásait kapcsolatos észlelt gyanús tevékenységeket és folytat gyanús incidensek a megfelelő műveletet a problémák megoldásához.

### <a name="security"></a>Biztonság
**Titkok kezelése** a megoldás [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) kulcsok és titkos kulcsok felügyeletéhez. Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. Az alábbi Azure Key Vault-képességek segítségével az ügyfelek, az adatok és az ilyen adatokhoz való hozzáférés védelme:
- Speciális hozzáférési szabályzatok kell alapon vannak konfigurálva.
- A Key Vault hozzáférési szabályzatok a minimálisan szükséges engedélyeket kulcsok és titkos kulcsok vannak meghatározva.
- Összes kulcsot és titkos kulcsok a Key Vaultban van lejárati dátumát.
- Speciális hardveres biztonsági modulokban (HSM) által védett összes kulcsok a Key Vaultban. A kulcs típusa egy HSM által védett 2048-bites RSA-kulcsot.
- Minden felhasználó és identitások kapnak a szerepköralapú hozzáférés-vezérlés használatával minimálisan szükséges engedélyeket.
- Diagnosztikai naplók a Key vault legalább 365 napos megőrzési idővel rendelkező engedélyezve vannak.
- A szükséges kapcsolatok engedélyezett titkosítási műveletek kulcsok korlátozódnak.

**Az Application Gateway** az architektúra csökkenti a biztonsági rések használatával az Application Gateway webalkalmazási tűzfal, és az OWASP szabálykészletben engedélyezve kockázatát. További képességek:
- [End-to-End-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Engedélyezése [SSL-alapú Kiszervezéshez](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Tiltsa le [a TLS 1.0 és 1.1 verzió](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Webalkalmazási tűzfal](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview)
- [Megelőzés üzemmód](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) az OWASP 3.0 szabálykészletben
- Engedélyezése [diagnosztikai naplózás](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Egyéni állapot-mintavételei](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- [Az Azure Security Center](https://azure.microsoft.com/services/security-center) és [az Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) nyújtanak további védelmet és értesítések. Az Azure Security Center megbízhatóságibesorolás-kezelési rendszert is biztosít.

### <a name="logging-and-auditing"></a>Naplózás és vizsgálat
Az Azure Monitor biztosít széles körű naplózását, a rendszer és a felhasználói tevékenységek, valamint a helyrendszer állapotát. Azt gyűjti és elemzi az adatokat az Azure-erőforrások által létrehozott és a helyszíni környezetekben.
- **A Tevékenységnaplók**: [A Tevékenységnaplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) adjon meg egy előfizetéshez tartozó erőforrásokon végrehajtott műveletekkel kapcsolatos információk. A Tevékenységnaplók segítségével határozza meg a műveletet kezdeményező, az eseményt, és állapot ideje.
- **Diagnosztikai naplók**: [Diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) minden erőforrás által kibocsátott az összes napló tartalmazza. Ezek a naplók például a Windows rendszer-eseménynaplói, az Azure Storage-naplók, a Key Vault-naplók és az Application Gateway hozzáférés és a tűzfal a naplókat.
- **Napló Archiválás**: Az összes diagnosztikai naplók írni egy központosított, titkosított csatornákon történik az Azure storage-fiókját archiválási. A megőrzési felhasználó által konfigurálható, mentése és 730 nap között, a megőrzési a szervezet konkrét követelményeinek. Ezek a naplók csatlakozhat Azure Log Analytics feldolgozási, tárolására és-irányítópult jelentéseit.

Ezenkívül a következő figyelési megoldások jelennek meg, ez az architektúra részeként:
-   [Az Active directory Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Az Active Directory állapotának ellenőrzése megoldás a kockázat és kiszolgálói környezetek állapotát értékeli a rendszeres időközönkénti, és a telepített kiszolgálói infrastruktúra vonatkozó javaslatok rangsorolt listáját tartalmazza.
-   [Kártevőirtók felmérése](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): A kártevőirtó megoldás jelentések a kártevő szoftverek, a fenyegetések és a védelem állapota.
-   [Az Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): Az Azure Automation-megoldás tárolja, fut, és kezeli a runbookok. Ebben a megoldásban lévő runbookok segítségével naplók gyűjtése az Application Insights és az Azure SQL Database.
-   [Biztonság és auditálás](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): A biztonsági és auditálási irányítópultja az erőforrások biztonsági állapotát egy magas szintű betekintést nyújt a metrikák azáltal, hogy a biztonsági tartományok, a jelentős problémák, a észlelések, a fenyegetésekkel kapcsolatos Tudásbázis és a gyakori biztonsági lekérdezések.
-   [SQL-értékeléssel](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): Az SQL Health Check megoldás a kockázat és kiszolgálói környezetek állapotát értékeli a rendszeres időközönkénti, és nyújt a felhasználók számára a telepített kiszolgálói infrastruktúra vonatkozó javaslatok rangsorolt listáját.
-   [Frissítéskezelés](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): Az Update Management megoldás lehetővé teszi, hogy az ügyfél felügyeleti operációs rendszer biztonsági frissítéseket, beleértve egy elérhető frissítések állapotát és a szükséges frissítések telepítése.
-   [Az ügynök állapota](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Az Agent Health megoldás hány ügynök van telepítve, és a földrajzi elosztás, valamint hány ügynök, amely nem válaszol és működési adatokat küld be, amely az ügynökök számát jelenti.
-   [Azure-Tevékenységnaplók](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Az Activity Log Analytics megoldás az ügyfél az összes Azure-előfizetések az Azure-Tevékenységnaplók elemzésének segíti.
-   [A változáskövetés](https://docs.microsoft.com/azure/automation/automation-change-tracking): A Change Tracking megoldás lehetővé teszi, hogy az ügyfelek számára, hogy könnyen azonosíthassa a változtatásokat a környezetben.

**Az Azure Monitor**
[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) segít a felhasználóknak a teljesítmény, biztonság fenntartására és azonosíthatja a trendeket lehetővé teszi a cégeknek naplózása, riasztásokat hozhat létre, és archiválja az adatokat, beleértve az API-hívások nyomon követése az ügyfelek Azure-erőforrások.

## <a name="threat-model"></a>Fenyegetések modellezése

A referenciaarchitektúra az adatfolyam-diagram érhető el az [letöltése](https://aka.ms/fedrampPaaSWebAppDFD) vagy alább találja. Ez a modell segítségével az ügyfelek megismerhetik a pontokat a rendszer infrastruktúra esetleges kockázat, ha a végzett módosítások.

![PaaS webes alkalmazás a FedRAMP fenyegetések modellezése](images/fedramp-paaswa-threat-model.png?raw=true "PaaS webes alkalmazás a FedRAMP fenyegetések modellezése")

## <a name="compliance-documentation"></a>Megfelelőségi dokumentációja
A [Azure biztonsági és megfelelőségi terv - FedRAMP magas vevő felelőssége mátrix](https://aka.ms/blueprinthighcrm) sorolja fel a magas szintű FedRAMP-alapkonfiguráció által igényelt összes biztonsági vezérlő. A mátrix azt jelzi, hogy az egyes vezérlőelemek végrehajtása Microsoft, az ügyfél felelőssége, vagy a kettő között megosztott.

A [Azure biztonsági és -megfelelőségi terv - FedRAMP PaaS WebApp magas ellenőrzési megvalósítási mátrix](https://aka.ms/fedrampPaaSWebAppCIM) sorolja fel a magas szintű FedRAMP-alapkonfiguráció által igényelt összes biztonsági vezérlő. A mátrix, amelyen vezérlőelemek tartoznak a PaaS webes alkalmazásarchitektúra, beleértve a részletes leírását, hogyan végrehajtása megfelel egyes az érintett vezérlő információkat tartalmaz.

## <a name="guidance-and-recommendations"></a>Útmutatás és javaslatok
### <a name="vpn-and-expressroute"></a>VPN és ExpressRoute
Biztonságos VPN-alagúton vagy [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) biztonságos kapcsolat a PaaS webes alkalmazás referenciaarchitektúra részeként üzembe helyezett erőforrások kell konfigurálni. Megfelelően állítja be a VPN-t vagy ExpressRoute, az ügyfelek hozzáadhatja egy védelmi réteget biztosít adatokat átvitel közben.

Az Azure-ral biztonságos VPN-alagút alkalmazásával egy helyszíni hálózat és a egy Azure virtuális hálózat közötti VPN-kapcsolat hozható létre. Ezt a kapcsolatot az interneten keresztül történik, és lehetővé teszi az ügyfelek számára, hogy egy titkosított kapcsolatot, az ügyfél hálózati és az Azure között lévő biztonságosan "csatorna" információkat. Site-to-Site VPN egy olyan biztonságos, érett technológia, évtizedes a vállalatok által központilag telepített. A [IPsec-alagút módú](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) ezt a beállítást, mint egy titkosítási mechanizmus használatban van.

Forgalom a VPN-alagút haladnak át a helyek közötti VPN-nel az interneten, mert a Microsoft kínál egy másik, még biztonságosabb kapcsolat lehetőséget. Az Azure ExpressRoute dedikált WAN Azure-ban és a egy helyszíni helyhez vagy egy Exchange-szolgáltató közötti kapcsolat. Az ExpressRoute-kapcsolatok az interneten keresztül halad, mivel a kapcsolatok az interneten keresztül ajánlja fel a további megbízhatóság megbízhatóbbak, gyorsabbak, kisebb a késésük, és biztonságosabbak a szokásos internetkapcsolatoknál. Továbbá mivel ez az ügyfél távközlési szolgáltató közvetlen kapcsolatot, az adatok nem az interneten keresztül továbbítani tartalmaz, és ezért nem érhető el.

Ajánlott eljárások végrehajtásához egy biztonságos hibrid hálózatot, amely kiterjeszti a helyszíni hálózatot az Azure-bA [elérhető](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Jogi nyilatkozat

 - Ez a dokumentum csak tájékoztatási célokat szolgál. A MICROSOFT NEM VÁLLAL SEMMILYEN KIFEJEZETT, KIFEJEZETT, VÉLELMEZETT VAGY KÖTELEZŐ GARANCIÁT A DOKUMENTUMBAN SZEREPLŐ INFORMÁCIÓRA VONATKOZÓAN. Ez a dokumentum biztosított "as-van." Információk és vélemények ebben a dokumentumban URL-CÍMEK és más internetes webhelyekre utaló hivatkozások értesítés nélkül változhatnak. Ez a dokumentum olvasásakor ügyfelek felelősségére használja.
 - Ez a dokumentum nem biztosít semmilyen jogot semmilyen Microsoft-termék vagy a megoldások a szellemi tulajdonhoz ügyfelek.
 - Ügyfelek másolhatja és használhatja ezt a dokumentumot belső, hivatkozási célokból.
 - Bizonyos ajánlások a dokumentum eredményezhet megnövekedett adat-, hálózati vagy számítási erőforrás-használat az Azure-ban, és előfordulhat, hogy növelje az ügyfél Azure licencek vagy előfizetések költségeit.
 - Ez az architektúra célja, hogy az ügyfelek számára az adott követelményekhez beállítása alapjaként szolgálja ki, és nem használható-éles környezetben van.
 - Ez a dokumentum referenciaként fejlesztik, és nem használható minden olyan eszközt, amely szerint egy ügyfél megfelel a megadott megfelelőségi követelmények és előírások meghatározásához. Ügyfelek jóváhagyott megvalósítás az ügyfeleknél a szervezet jogi támogatást kell kérnie.
