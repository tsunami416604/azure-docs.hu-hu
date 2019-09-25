---
title: Azure Security and Compliance Blueprint-FedRAMP webes alkalmazás
description: Azure Security and Compliance Blueprint-FedRAMP webes alkalmazás
services: security
author: jomolesk
ms.assetid: 41570fc1-4d74-48ed-afb0-ef1be857029e
ms.service: security
ms.topic: article
ms.date: 06/01/2018
ms.author: jomolesk
ms.openlocfilehash: e8458d505575a5bf39bbd0a9970c5044b9cbd0f0
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71257337"
---
# <a name="azure-security-and-compliance-blueprint-paas-web-application-for-fedramp"></a>Azure Security and Compliance Blueprint: FedRAMP-alapú webalkalmazás

## <a name="overview"></a>Áttekintés

A [Federal Risk and Authorization Management program (FedRAMP)](https://www.fedramp.gov/) egy olyan Egyesült Államok kormányzati szintű program, amely szabványosított megközelítést biztosít a biztonság értékeléséhez, engedélyezéséhez és a felhőalapú termékek és szolgáltatások folyamatos monitorozásához. Ez a Azure Security and Compliance Blueprint útmutatást nyújt a Microsoft Azure platform szolgáltatásként szolgáló architektúrájának megvalósításához, amely segít a FedRAMP magas szintű vezérlők egy részhalmazának megvalósításában. Ez a megoldás útmutatást nyújt az Azure-erőforrások általános hivatkozási architektúrához való üzembe helyezéséhez és konfigurálásához, és bemutatja, hogy az ügyfelek milyen módon tudják kielégíteni a konkrét biztonsági és megfelelőségi követelményeket, és alapként szolgálnak az ügyfelek számára saját megoldásaikat hozhat létre és konfigurálhat az Azure-ban.

Ez a hivatkozási architektúra, a társított vezérlés megvalósítási útmutatói és a veszélyforrási modellek arra szolgálnak, hogy az ügyfelek számára alapvető fontosságúak legyenek, és ne használják éles környezetben. Ha módosítás nélkül helyezi üzembe az alkalmazást a környezetbe, nem elegendő a FedRAMP magas alapkonfigurációjának követelményeinek teljes kielégítéséhez. Vegye figyelembe a következőket:
- Az architektúra olyan alapkonfigurációt biztosít, amellyel az ügyfelek FedRAMP-kompatibilis módon telepíthetik az Azure-beli számítási feladatokat.
- Az ügyfelek felelősek az architektúrával létrehozott megoldások megfelelő biztonsági és megfelelőségi felmérésének elvégzéséért, mivel a követelmények az egyes ügyfelek implementációjának sajátosságai alapján változhatnak.

## <a name="architecture-diagram-and-components"></a>Architektúra ábrája és összetevői
Ez a megoldás egy Azure SQL Database háttérrel rendelkező, a Pásti webalkalmazásra mutató hivatkozási architektúrát biztosít. A webalkalmazást egy elkülönített Azure App Service Environment üzemelteti, amely egy Azure-adatközpont privát, dedikált környezete. A környezeti terhelés kiegyensúlyozza a webalkalmazás forgalmát az Azure által felügyelt virtuális gépek között. Ez az architektúra hálózati biztonsági csoportokat, Application Gateway, Azure DNS és Load Balancer is tartalmaz. Emellett Azure Monitor a rendszerállapot valós idejű elemzését is biztosítja. **Az Azure azt javasolja, hogy VPN-vagy ExpressRoute-kapcsolaton keresztül konfigurálja a felügyeleti és az Adatimportálási kapcsolatokat a hivatkozási architektúra alhálózatára.**

![FedRAMP hivatkozási architektúra diagramja, a Pásti Web Application](images/fedramp-paaswa-architecture.png?raw=true "FedRAMP hivatkozási architektúra diagramja, a Pásti Web Application")

Ez a megoldás az alábbi Azure-szolgáltatásokat használja. Az üzembe helyezési architektúra részletei a [telepítési architektúra](#deployment-architecture) szakaszban találhatók.

- Azure Active Directory
- Azure Key Vault
- Azure SQL Database
- Application Gateway
    - (1) webalkalmazási tűzfal
        - Tűzfal mód: megelőzés
        - Szabály beállítása: OWASP 3,0
        - Figyelő: 443-es port
- Azure virtuális hálózat
- Network security groups (Hálózati biztonsági csoportok)
- Azure DNS
- Azure Storage
- Azure Monitor
- App Service Environment v2
- Azure Load Balancer
- Azure Web App
- Azure Resource Manager

## <a name="deployment-architecture"></a>Üzembe helyezési architektúra
A következő szakasz az üzembe helyezési és megvalósítási elemeket részletezi.

**Azure Resource Manager**: [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) lehetővé teszi az ügyfelek számára, hogy csoportként működjenek együtt a megoldás erőforrásaival. Az ügyfelek egyetlen, koordinált műveletben telepíthetik, frissíthetik vagy törölhetik a megoldás összes erőforrását. Az ügyfelek egy sablont használnak az üzembe helyezéshez, és a sablon különböző környezetekben, például tesztelési, átmeneti és éles környezetben is működhet. A Resource Manager biztonsági, naplózási és címkézési funkciókat biztosít az ügyfelek számára az üzembe helyezést követően az erőforrások kezeléséhez.

**App Service Environment v2**: A [Azure app Service Environment (](https://docs.microsoft.com/azure/app-service/environment/intro) benyújtó) egy app Service funkció, amely teljesen elkülönített és dedikált környezetet biztosít a app Service alkalmazások biztonságos, nagy léptékű futtatásához.

A ASE elkülönítettek, hogy csak egyetlen ügyfél alkalmazásait futtassák, és mindig virtuális hálózatba vannak telepítve. Az ügyfelek részletesen szabályozzák mind a bejövő, mind a kimenő alkalmazások hálózati forgalmát, és az alkalmazások nagy sebességű biztonságos kapcsolatokat hozhatnak létre a virtuális hálózatokon a helyszíni vállalati erőforrásokhoz.

Az architektúrához tartozó ASE használata a következő vezérlőkhöz/konfigurációkhoz engedélyezett:

- Gazdagép biztonságos Azure-Virtual Network és hálózati biztonsági szabályokon belül
- Önaláírt ILB-tanúsítvánnyal konfigurált, HTTPS-kommunikációra konfigurált önkiszolgáló
- [Belső terheléselosztási mód](../../app-service/environment/app-service-environment-with-internal-load-balancer.md)
- [TLS 1,0](../../app-service/environment/app-service-app-service-environment-custom-settings.md) letiltása
- [TLS-titkosítás](../../app-service/environment/app-service-app-service-environment-custom-settings.md) módosítása
- [Bejövő forgalom vezérlése N/W-portok](../../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md)
- [Webalkalmazási tűzfal – az adatkorlátozás](../../app-service/environment/app-service-app-service-environment-web-application-firewall.md)
- [Azure SQL Database forgalom](../../app-service/environment/app-service-app-service-environment-network-architecture-overview.md) engedélyezése

Az [útmutató és javaslatok](#guidance-and-recommendations) szakasz további információkat tartalmaz a ASE kapcsolatban.

**Azure Web App**: A [Azure app Service](https://docs.microsoft.com/azure/app-service/) lehetővé teszi, hogy az ügyfelek az infrastruktúra kezelése nélkül saját maguk által választott programozási nyelven hozzanak létre és működtessenek webalkalmazásokat. Automatikus méretezést és magas rendelkezésre állást kínál, támogatja a Windows és a Linux rendszert is, valamint lehetővé teszi az automatikus üzembe helyezéseket a GitHub, az Azure DevOps vagy bármely egyéb Git-adattár használatával.

### <a name="virtual-network"></a>Virtuális hálózat
Az architektúra a 10.200.0.0/16 címtartomány szerinti magánhálózati virtuális hálózatot definiálja.

**Hálózati biztonsági csoportok**: A [hálózati biztonsági csoportok (NSG-EK)](../../virtual-network/virtual-network-vnet-plan-design-arm.md) olyan hozzáférés-vezérlési listát tartalmaznak, amelyek engedélyezik vagy megtagadják a forgalmat a virtuális hálózaton belül. A NSG használatával biztonságossá teheti a forgalmat egy alhálózaton vagy egy adott virtuálisgép-szinten. A következő NSG léteznek:
- 1 NSG Application Gateway
- 1 NSG App Service Environment
- 1 NSG Azure SQL Database

Mindegyik NSG meg van nyitva bizonyos portok és protokollok, hogy a megoldás biztonságosan és megfelelően működjön. Emellett a következő konfigurációk is engedélyezve vannak az egyes NSG:
  - A [diagnosztikai naplók és események](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) engedélyezve vannak, és Storage-fiókban tárolódnak
  - Azure Monitor naplók a [NSG diagnosztikaához](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json) csatlakoznak

Alhálózatok: Minden alhálózat társítva van a hozzá tartozó NSG.

**Azure DNS**: A tartománynévrendszer vagy a DNS a webhely vagy szolgáltatás nevének az IP-címére való fordítására (vagy feloldására) felelős. A [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) olyan DNS-tartományok üzemeltetési szolgáltatása, amelyek névfeloldást biztosítanak az Azure-infrastruktúra használatával. Az Azure-ban a felhasználók a DNS-rekordokat a többi Azure-szolgáltatással azonos hitelesítő adatok, API-k, eszközök és számlázás használatával kezelhetik. A Azure DNS támogatja a magánhálózati DNS-tartományokat is.

**Azure Load Balancer**: [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) lehetővé teszi az ügyfelek számára az alkalmazások skálázását és magas rendelkezésre állású szolgáltatások létrehozását. Load Balancer támogatja a bejövő és a kimenő forgatókönyveket, valamint alacsony késést és nagy átviteli sebességet biztosít, és akár több millió folyamatot is képes az összes TCP-és UDP-alkalmazáshoz.

### <a name="data-in-transit"></a>Átvitt adatok
Az Azure alapértelmezés szerint titkosítja az Azure-adatközpontok és az összes kommunikációját. Az Azure Storage-ba irányuló összes tranzakció a Azure Portal a HTTPS-en keresztül történik.

### <a name="data-at-rest"></a>Inaktív adat
Az architektúra titkosítva, adatbázis-naplózással és egyéb mértékekkel védi a nyugalmi állapotban lévő adatok védelmét.

**Azure Storage**: A titkosított adatoknak a nyugalmi követelmények teljesítése érdekében az összes [Azure Storage](https://azure.microsoft.com/services/storage/) [Storage Service encryption](../../storage/common/storage-service-encryption.md)használ.

AzureDiskEncryption
[Azure Disk Encryption](../azure-security-disk-encryption-overview.md) a Windows BitLocker szolgáltatását használja az adatlemezek mennyiségi titkosításának biztosításához. A megoldás integrálva van Azure Key Vaultekkel a lemezes titkosítási kulcsok szabályozása és kezelése érdekében.

**Azure SQL Database**: A Azure SQL Database példány a következő adatbázis-biztonsági mértékeket használja:
-   Az [AD-hitelesítés és-engedélyezés](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) lehetővé teszi az adatbázis-felhasználók és más Microsoft-szolgáltatások identitásának kezelését egy központi helyen.
-   Az [SQL Database naplózása](../../sql-database/sql-database-auditing.md) nyomon követi az adatbázis eseményeit, és egy Azure Storage-fiókban lévő naplóba írja azokat.
-   A Azure SQL Database [transzparens adattitkosítás (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)használatára van konfigurálva, amely az adatbázis, a társított biztonsági másolatok és a tranzakciós naplófájlok valós idejű titkosítását és visszafejtését hajtja végre a nyugalmi információkkal szembeni védelem érdekében.
-   A [Tűzfalszabályok](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) megakadályozzák az adatbázis-kiszolgálók hozzáférését a megfelelő engedélyek megadása előtt. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján.
-   Az [SQL-veszélyforrások észlelése](../../sql-database/sql-database-threat-detection.md) lehetővé teszi az észlelést és a reagálást a potenciális fenyegetésekre, mivel ezek a hibák a gyanús adatbázis-tevékenységek, a potenciális sebezhetőségek, az SQL-injektálási támadások és a rendellenes adatbázis-hozzáférési minták esetében
-   [Always encrypted oszlopok](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) biztosítják, hogy a bizalmas adatok soha ne jelenjenek meg egyszerű szövegként az adatbázis-rendszeren belül. Az adattitkosítás engedélyezése után csak az ügyfélalkalmazások vagy az alkalmazások férhetnek hozzá a kulcsokhoz.
- A [sorok szintjének biztonsága](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) lehetővé teszi a felhasználók számára, hogy szabályzatokat határozzanak meg az adathozzáférés korlátozásához a feldolgozás megszüntetéséhez.
- [SQL Database a dinamikus adatmaszkolás](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) a hivatkozási architektúra telepítése után végezhető el. Az ügyfeleknek módosítaniuk kell a dinamikus adatmaszkolási beállításokat az adatbázis-sémájuk betartásához.

### <a name="identity-management"></a>Identitáskezelés
Az alábbi technológiák az Azure-környezet Identitáskezelés-kezelési funkcióit biztosítják:
-   [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) a Microsoft több-bérlős felhőalapú címtár-és Identitáskezelés-kezelő szolgáltatása. A megoldás minden felhasználója a HRE-ben jön létre, beleértve a Azure SQL Databasehoz hozzáférő felhasználókat is.
-   Az alkalmazáshoz való hitelesítés a HRE használatával történik. További információ: [alkalmazások integrálása a Azure Active Directorysal](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Emellett az adatbázis oszlopának titkosítása a Azure Active Directory használatával hitelesíti az alkalmazást a Azure SQL Database. További információ: a [bizalmas adatok védelme Azure SQL Databaseban](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   Az [Azure szerepköralapú hozzáférés-vezérlése](../../role-based-access-control/role-assignments-portal.md) lehetővé teszi az Azure-hoz való, precízen célzott hozzáférés-kezelést. Az előfizetés-hozzáférés az előfizetés-adminisztrátorra korlátozódik, és az erőforrásokhoz való hozzáférés a felhasználói szerepkör alapján korlátozott lehet.
- [Azure Active Directory Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) lehetővé teszi, hogy az ügyfelek csökkentsék azon felhasználók számát, akik hozzáféréssel rendelkeznek bizonyos adatokhoz.  A rendszergazdák HRE Privileged Identity Management használhatják az emelt szintű identitások felderítését, korlátozását és figyelését, valamint az erőforrásokhoz való hozzáférésüket. Ez a funkció az igény szerinti, igény szerinti rendszergazdai hozzáférés biztosítására is használható, ha szükséges.
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) észleli a szervezet identitásait érintő lehetséges biztonsági réseket, automatikus válaszokat konfigurál a szervezet identitásával kapcsolatos gyanús műveletekre, és megvizsgálja a gyanús incidensek, hogy megtegye a megfelelő lépéseket a megoldásához.

### <a name="security"></a>Biztonság
**Titkok kezelése** A megoldás a kulcsok és titkok kezeléséhez [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) használ. Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. A következő Azure Key Vault képességek segítenek az ügyfeleknek az adatvédelemben és az ilyen jellegű adathozzáférésben:
- A speciális hozzáférési szabályzatok a szükséges módon vannak konfigurálva.
- Key Vault hozzáférési házirendek minimálisan szükséges engedélyekkel vannak definiálva a kulcsokhoz és a titkokhoz.
- Key Vault összes kulcsának és titkának lejárati dátuma van.
- A Key Vault összes kulcsát speciális hardveres biztonsági modulok (HSM-EK) védik. A kulcs típusa HSM-védelemmel ellátott 2048-bites RSA-kulcs.
- Minden felhasználó és identitás a szerepköralapú hozzáférés-vezérlés használatával minimálisan szükséges engedélyeket kap.
- Key Vault diagnosztikai naplói legalább 365 napos megőrzési időtartammal engedélyezettek.
- A kulcsok számára engedélyezett titkosítási műveletek csak a szükségesek.

**Application Gateway** Az architektúra csökkenti a biztonsági rések kockázatát a webalkalmazási tűzfallal rendelkező Application Gateway és a OWASP-alapú szabályrendszert használatával. A további funkciók a következők:
- [End-to-End-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [SSL-kiszervezés](../../application-gateway/create-ssl-portal.md) engedélyezése
- [A TLS 1.0-s és 1.1-es verziójának](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell) letiltása
- [Webalkalmazási tűzfal](../../application-gateway/waf-overview.md)
- [Megelőzési mód](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) OWASP 3,0-es szabályrendszert
- [Diagnosztikai naplózás](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics) engedélyezése
- [Egyéni állapot-mintavételek](../../application-gateway/quick-create-portal.md)
- A [Azure Security Center](https://azure.microsoft.com/services/security-center) és [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) további védelmet és értesítéseket biztosítanak. A Azure Security Center a hírnév rendszerét is biztosítja.

### <a name="logging-and-auditing"></a>Naplózás
Azure Monitor a rendszer-és felhasználói tevékenységek, valamint a rendszer állapotának széles körű naplózását biztosítja. Az Azure-ban és a helyszíni környezetekben található erőforrások által generált adatokat gyűjti és elemzi.
- **Tevékenységek naplói**: A [tevékenységek naplói](../../azure-monitor/platform/activity-logs-overview.md) betekintést nyújtanak az előfizetésben lévő erőforrásokon végrehajtott műveletekre. A Tevékenységnaplók segítenek meghatározni a művelet kezdeményezőjét, az előfordulás időpontját és az állapotot.
- **Diagnosztikai naplók**: A [diagnosztikai naplók](../../azure-monitor/platform/resource-logs-overview.md) az összes erőforrás által kibocsátott összes naplót tartalmazzák. Ezek a naplók a Windows-eseménynaplókat, az Azure Storage-naplókat, a Key Vault naplókat, valamint Application Gateway hozzáférési és tűzfal-naplókat tartalmaznak.
- **Napló archiválása**: Az összes diagnosztikai napló egy központi és titkosított Azure Storage-fiókba írja az archiválást. A megőrzés a felhasználó által konfigurálható, akár 730 nap, hogy megfeleljen a szervezetre vonatkozó megőrzési követelményeknek. Ezek a naplók Azure Monitor naplókat csatlakoznak a feldolgozáshoz, tároláshoz és irányítópult-jelentéskészítéshez.

Emellett az alábbi figyelési megoldások is részét képezik ennek az architektúrának:
-   Az [Active Directory értékelése](../../azure-monitor/insights/ad-assessment.md): A Active Directory Health-ellenőrzési megoldás rendszeres időközönként kivizsgálja a kiszolgálói környezetek kockázatait és állapotát, és rangsorolja a telepített kiszolgálói infrastruktúrára jellemző ajánlásokat.
-   [Antimalware Assessment](../../security-center/security-center-install-endpoint-protection.md): Az antimalware-megoldás a kártevők, fenyegetések és védelem állapotáról nyújt jelentéseket.
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): A Azure Automation megoldás tárolja, futtatja és felügyeli a runbookok. Ebben a megoldásban a runbookok segítséget nyújt a naplók Application Insights és Azure SQL Databaseból való összegyűjtésében.
-   [Security and Audit](../../security-center/security-center-intro.md): A Security and Audit irányítópult magas szintű betekintést nyújt az erőforrások biztonsági állapotára azáltal, hogy mérőszámokat biztosít a biztonsági tartományokra, a jelentős problémákra, az észlelésekre, a fenyegetések felderítésére és az általános biztonsági lekérdezésekre.
-   [SQL Assessment](../../azure-monitor/insights/sql-assessment.md): Az SQL Health-ellenőrzési megoldás rendszeres időközönként kivizsgálja a kiszolgálói környezetek kockázatait és állapotát, és az ügyfelek számára az üzembe helyezett kiszolgáló-infrastruktúrára jellemző ajánlások rangsorolt listáját biztosítja.
-   [Update Management](../../automation/automation-update-management.md): Az Update Management megoldás lehetővé teszi az operációs rendszer biztonsági frissítéseinek az ügyfelek általi felügyeletét, beleértve az elérhető frissítések állapotát, valamint a szükséges frissítések telepítésének folyamatát.
-   [Agent Health](../../monitoring/monitoring-solution-agenthealth.md): Az Agent Health-megoldás jelentést készít, hogy hány ügynök van üzembe helyezve, valamint a földrajzi eloszlásuk, valamint a nem válaszoló ügynökök száma, valamint az operatív adatküldés alatt álló ügynökök száma.
-   [Azure-tevékenység naplói](../../azure-monitor/platform/collect-activity-logs.md): Az Activity Log Analytics-megoldás segítséget nyújt az Azure-tevékenységek naplóinak elemzéséhez az ügyfelek összes Azure-előfizetése között.
-   [Change Tracking](../../automation/change-tracking.md): A Change Tracking megoldás lehetővé teszi, hogy az ügyfelek könnyedén azonosítsák a környezet változásait.

**A Azure monitor**
[Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) segít a felhasználóknak a teljesítmény nyomon követésében, a biztonság fenntartásában és a trendek azonosításában azáltal, hogy lehetővé teszik a szervezetek számára a naplózást, a riasztások létrehozását és az adatok archiválását, beleértve az ügyfelek Azure-erőforrásaiban

## <a name="threat-model"></a>Veszélyforrások modellje

Az ehhez a hivatkozási architektúrához tartozó Adatfolyam-diagram [letölthető](https://aka.ms/fedrampPaaSWebAppDFD) , vagy a következő címen érhető el. Ez a modell lehetővé teszi az ügyfeleknek, hogy a módosítások végrehajtása során megértsék a rendszerinfrastruktúra lehetséges kockázatait.

![FedRAMP-veszélyforrások modellje: Pásti Web Application](images/fedramp-paaswa-threat-model.png?raw=true "FedRAMP-veszélyforrások modellje: Pásti Web Application")

## <a name="compliance-documentation"></a>Megfelelőségi dokumentáció
A [Azure Security and Compliance Blueprint-FedRAMP magas szintű vevői felelősségi mátrixa](https://aka.ms/blueprinthighcrm) felsorolja a FedRAMP magas alapkonfigurációja által igényelt összes biztonsági ellenőrzést. A mátrix azt jelzi, hogy az egyes vezérlőelemek megvalósítása a Microsoft, az ügyfél vagy a kettő közötti megosztás feladata-e.

A [Azure Security and Compliance Blueprint-FedRAMP Pásti WebApp High Control megvalósítási mátrixa](https://aka.ms/fedrampPaaSWebAppCIM) felsorolja az FedRAMP magas alapkonfigurációja által igényelt összes biztonsági ellenőrzést. A mátrix információkkal szolgál arról, hogy a rendszer milyen vezérlőket tartalmaz a Pásti webalkalmazás-architektúra, beleértve a részletes leírását, hogy a megvalósítás hogyan teljesíti az egyes kezelt vezérlők követelményeit.

## <a name="guidance-and-recommendations"></a>Útmutatás és javaslatok
### <a name="vpn-and-expressroute"></a>VPN-és ExpressRoute
Biztonságos VPN-alagutat vagy [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) kell konfigurálni, hogy biztonságosan létre lehessen hozni egy kapcsolatot a jelen Pásti webalkalmazás-hivatkozási architektúra részeként üzembe helyezett erőforrásokkal. A VPN-vagy ExpressRoute megfelelő beállításával az ügyfelek hozzáadhatnak egy védelmi réteget az átvitelhez.

A biztonságos VPN-alagút az Azure-nal való megvalósításával létrehozhat egy VPN-kapcsolatot egy helyszíni hálózat és egy Azure-Virtual Network között. Ez a kapcsolat az interneten keresztül történik, és lehetővé teszi az ügyfelek számára, hogy az ügyfél hálózata és az Azure közötti titkosított kapcsolaton belül biztonságosan "alagút" információkat használjanak. A helyek közötti VPN egy olyan biztonságos, érett technológia, amelyet évtizedek óta minden méretben üzembe helyezett a vállalatok. Ebben a beállításban az [IPsec-alagút mód](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) titkosítási mechanizmusként van használatban.

Mivel a VPN-alagúton belüli forgalom egy helyek közötti VPN-kapcsolaton keresztül halad át az interneten, a Microsoft egy másik, még biztonságosabb kapcsolódási lehetőséget kínál. Az Azure ExpressRoute egy dedikált WAN-kapcsolat az Azure és egy helyszíni hely vagy egy Exchange-szolgáltató között. Mivel a ExpressRoute-kapcsolatok nem az interneten keresztül haladnak át, ezek a kapcsolatok megbízhatóbbak, gyorsabbak, kisebb késések és nagyobb biztonságot biztosítanak, mint a szokásos kapcsolatok az interneten keresztül. Továbbá, mivel ez az ügyfél távközlési szolgáltatójának közvetlen kapcsolata, az adat nem az interneten keresztül történik, ezért nem lesz elérhető.

Ajánlott eljárások egy biztonságos hibrid hálózat megvalósításához, amely kiterjeszti a helyszíni hálózatot az Azure- [ra.](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)

## <a name="disclaimer"></a>Jogi nyilatkozat

 - Ez a dokumentum csak tájékoztató jellegű. A MICROSOFT NEM VÁLLAL SEMMILYEN KIFEJEZETT, VÉLELMEZETT VAGY TÖRVÉNYES GARANCIÁT A JELEN DOKUMENTUMBAN FOGLALT INFORMÁCIÓK ALAPJÁN. Ez a dokumentum "aktuálisként" van megadva. Az ebben a dokumentumban kifejezett információk és nézetek, beleértve az URL-címeket és az internetes webhelyek más hivatkozásait, értesítés nélkül változhatnak. A dokumentumot olvasó ügyfeleink a használatuk kockázatát viselik.
 - A jelen dokumentum nem biztosít semmilyen jogot semmilyen Microsoft-termékben vagy-megoldásban található szellemi tulajdonhoz.
 - Az ügyfelek belső referenciák szerint másolhatják és használhatják ezt a dokumentumot.
 - A jelen dokumentumban szereplő javaslatok megnövelték az adatok, a hálózat vagy a számítási erőforrások használatát az Azure-ban, és növelhetik az ügyfelek Azure-licencét vagy előfizetési költségeit.
 - Ez az architektúra arra szolgál, hogy az ügyfelek számára a konkrét követelményekhez igazodva és nem használható éles környezetben.
 - Ez a dokumentum hivatkozásként van kifejlesztve, és nem használható az összes olyan eszköz meghatározására, amelyekkel az ügyfél megfelel bizonyos megfelelőségi követelményeknek és előírásoknak. Az ügyfeleknek jóvá kell hagyniuk a szervezeten belüli jogi támogatást a jóváhagyott ügyfél-implementációkban.
