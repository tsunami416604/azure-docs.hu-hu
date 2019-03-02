---
title: Azure biztonsági és megfelelőségi terv – a PaaS-webalkalmazás NIST 800-171-SP rendszerhez
description: Azure biztonsági és megfelelőségi terv – a PaaS webes alkalmazás SP-NIST 800-171
services: security
author: jomolesk
ms.assetid: eea21a0a-5930-43e8-937f-5419c20744c9
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: f9773c3b372ab22cbcd99828e147d23c185c4eb6
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2019
ms.locfileid: "57244621"
---
# <a name="azure-security-and-compliance-blueprint---paas-web-application-for-nist-special-publication-800-171"></a>Azure biztonsági és megfelelőségi terv – a NIST Special Publication 800-171 PaaS webes alkalmazás

## <a name="overview"></a>Áttekintés
[NIST Special Publication 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) hasznos útmutatást ad a szabályozott nem titkos információ (CUI) nonfederal információs rendszerek és a szervezetek a védelméhez. 800-171 NIST SP hozza létre a 14 termékcsaládok CUI titkosságát védelme kapcsolatos biztonsági követelményeket.

Az Azure biztonsági és megfelelőségi terv útmutatást nyújt a segítségével az ügyfelek üzembe helyezhet egy platformot (PaaS) webes alkalmazás az Azure-ban, amely egy részhalmazát valósítja meg az NIST SP 800-171 szabályozza. Ez a megoldás bemutatja, amelyben ügyfeleink megfelel bizonyos biztonsági és megfelelőségi követelmények módon. Ez az ügyfelek számára hozhat létre, és a saját webalkalmazások konfigurálása az Azure-ban alapjaként is szolgál.

Ez a referenciaarchitektúra társított megvalósítási útmutató és fenyegetések modellezése célja, hogy az ügyfelek számára az adott követelményekhez a felmerülő ismeretekkel szolgál. Nem használható-éles környezetben van. Ez az architektúra módosítás nélküli üzembe helyezése nem elegendő a teljes mértékben a követelményeinek az NIST SP 800-171. Ügyfelei felelősek a megfelelő biztonsági és megfelelőségi értékelést, ez az architektúra használatával létrehozott megoldások elvégzéséhez. Követelmények minden ügyfél megvalósítása tulajdonságairól alapján változhat.

## <a name="architecture-diagram-and-components"></a>Architektúra és összetevők

Az Azure biztonsági és megfelelőségi terv biztosít a referenciaarchitektúra egy PaaS-webalkalmazás, egy Azure SQL Database háttérrendszerrel. A webalkalmazás izolált App Service-környezet, amely az Azure-adatközpontok privát, dedikált környezetben üzemeltetett. A környezet elosztja a forgalmat a webes alkalmazás között az Azure által kezelt virtuális gépek (VM). Ez az architektúra a hálózati biztonsági csoportok (NSG), egy Azure application gateway, az Azure DNS és az Azure Load Balancer is tartalmaz.

Bővített analitika és a jelentéskészítés az Azure SQL Database-adatbázisok is konfigurálhatók, az oszlopcentrikus indexek. Az Azure SQL Database horizontálisan fel vagy le vagy kapcsolja ki teljesen ügyfelek általi használatot válaszul is. SQL forgalmat titkosítja a rendszer SSL-lel azáltal, önaláírt tanúsítványokat. Ajánlott eljárásként az Azure a fokozott biztonság egy megbízható hitelesítésszolgáltatótól használatát javasolja.

A megoldás az Azure Storage-fiókok, amelyek a felhasználók beállíthatják az inaktív adatok bizalmas mivoltát a Storage Service Encryption segítségével. Az Azure rugalmasságot a kiválasztott-ügyfél adatközponton belül adatokat három példányban tárolja. Georedundáns tárolás biztosítja, hogy az adatok replikálása egy másodlagos adatközpontba több száz mérfölddel távolabb és újra, hogy az adatközponton belül három példányban tárolja. Ezzel az elrendezéssel fokozott megakadályozza, hogy az ügyfél elsődleges adatközpont káros eseményt adatvesztést eredményez.

A fokozott biztonság érdekében ebben a megoldásban az összes erőforrás egy erőforráscsoportba tartozó Azure Resource Manageren keresztül felügyelt. Az Azure Active Directory (Azure AD) szerepköralapú hozzáférés-vezérlés (RBAC) szabályozható használatos üzembe az erőforrásokat. Ilyen erőforrások többek között a vásárlói kulcsok Azure Key vaultban. A fájlrendszer állapotának Azure monitoron keresztül figyel. Ügyfelek konfigurálása a figyelési szolgáltatás naplók rögzítésére. A fájlrendszer állapotának, amely egyszerűen használható egyetlen irányítópulton jelenik meg.

SQL Database általában felügyelt, az SQL Server Management Studióban. Fut a helyi gépen, amely az SQL-adatbázist egy biztonságos VPN- vagy Azure ExpressRoute-kapcsolaton keresztüli elérésére.

Az Application Insights biztosítja a valós idejű alkalmazásteljesítmény-felügyelet és az Azure Monitor naplóira analitikát *a Microsoft azt javasolja, hogy, felügyeleti és az adatok importálása be a hivatkozást a VPN- vagy ExpressRoute-kapcsolat konfigurálása architektúra-alhálózat.*

![A NIST SP 800-171 architekturális diagramja PaaS-webalkalmazás](images/nist171-paaswa-architecture.png "PaaS webes alkalmazás a NIST SP 800-171 architekturális diagramja")

Ez a megoldás a következő Azure-szolgáltatásokat használ. További információkért lásd: a [üzembe helyezési architektúrája](#deployment-architecture) szakaszban.

- Azure-alapú virtuális gépek
    - (1) felügyeleti/megerősített (Windows Server 2016 Datacenter)
- Azure Virtual Network
    - ((1) /16 hálózati
    - (4) /24 hálózatok
    - (4) a hálózati biztonsági csoportok
- Azure Application Gateway
    - Web application firewall (Webalkalmazási tűzfal)
        - Tűzfalmód: megelőzése
        - Szabálykészlet: OWASP
        - Figyelő portja: 443
- Application Insights
- Azure Active Directory
- App Service Environment v2
- Azure Automation
- Azure DNS
- Azure Key Vault
- Azure Load Balancer
- Az Azure Monitor (naplók)
- Azure Resource Manager
- Azure Security Center
- Azure SQL Database
- Azure Storage
- Azure Automation
- Azure Web Apps

## <a name="deployment-architecture"></a>Üzembe helyezési architektúrája
A következő szakaszt az üzembe helyezés és a megvalósítás elemek részletei.

**Az Azure Resource Manager**: [Erőforrás-kezelő](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) ügyfelek általi erőforrásokat egy csoportként a megoldásban használható. Az ügyfelek központi telepítése, frissítése, vagy törölje az összes erőforrását egyetlen, koordinált műveletben lévő megoldás. Ügyfelek telepítéshez egy sablont használ. A sablon különböző környezetekben, például tesztelési, átmeneti és éles környezetben is képes működni. A Resource Manager biztosít a biztonsági, naplózási és címkézési szolgáltatásokat, hogy az ügyfelek az erőforrások kezelésében a telepítést követően.

**Bástyagazdagép**: A megerősített gazdagép a központi hely, amely a felhasználók használhatják az ebben a környezetben üzembe helyezett erőforrások eléréséhez. A bástyagazdagép biztosítja, hogy a telepített erőforrások biztonságos kapcsolatot csak nyilvános IP-címekről érkező távoli forgalmat engedélyezi a biztonságos elemek listájához. Távoli asztali forgalmat lehetővé teszik, hogy a forgalom forrását definiálni kell az NSG-t.

Ez a megoldás létrehoz egy virtuális Gépet egy tartományhoz csatlakoztatott megerősített gazdagépként az alábbi konfigurációkkal:
-   [A kártevőirtó bővítmény](https://docs.microsoft.com/azure/security/azure-security-antimalware).
-   [Az Azure Diagnostics bővítmény](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template).
-   [Az Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) Key Vault használatával.
-   Egy [automatikus leállítási házirend](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) amikor nincs használatban a Virtuálisgép-erőforrások felhasználásának csökkentése érdekében.
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) engedélyezve van, hogy a hitelesítő adatokat és egyéb titkos adatait, amely el van különítve a futó operációs rendszert a védett környezetben futnak.

**Web Apps**: [Web Apps](https://docs.microsoft.com/azure/app-service/) egy Azure App Service-funkció. Ügyfelek használhatják azt hozhat létre és üzemeltethet webalkalmazásokat az általuk választott programozási nyelven infrastruktúra kezelése nélkül. Automatikus méretezést és magas rendelkezésre állást biztosít. Akkor támogatja a Windows és Linux rendszerű, és lehetővé teszi az automatikus telepítéseket a GitHub-, Azure DevOps, vagy bármely egyéb Git-adattárból.

**App Service Environment**: [App Service Environment-környezet](https://docs.microsoft.com/azure/app-service/environment/intro) egy App Service-funkció. Biztonságos, nagy léptékű az App Service-alkalmazások futtatásához egy teljesen elkülönített és dedikált környezetet biztosít.

Az App Service-környezet el különítve, csak egyetlen alkalmazás futtatását. Mindig egy virtuális hálózatban van telepítve. Az elkülönítési szolgáltatás miatt a referenciaarchitektúrát rendelkezik teljes bérlők elkülönítését, és a rendszer eltávolítja az Azure több-bérlős környezetben. Mindkét alkalmazás bejövő és kimenő hálózati forgalom részletesen szabályozhatja az ügyfél rendelkezik. Alkalmazások nagy sebességű, biztonságos kapcsolatot létesíthet virtuális hálózatokon keresztül kapcsolódjanak a helyi vállalati erőforrásokhoz. Ügyfelek automatikusan skálázhatók a "" App Service environmenttel terhelési mérőszámok, a rendelkezésre álló költségvetési vagy egy meghatározott ütemezés alapján.

Ez az architektúra App Service Environment-környezet használata biztosítja a következő vezérlőket és konfigurációkat:

- Gazdagép egy biztonságos Azure virtuális hálózaton belül, és a hálózati biztonsági szabály.
- Önaláírt belső load balancer tanúsítványt a HTTPS-kommunikációra. Ajánlott eljárásként a Microsoft a fokozott biztonság egy megbízható hitelesítésszolgáltatótól használatát javasolja.
- [Belső terheléselosztási üzemmód](https://docs.microsoft.com/azure/app-service-web/app-service-environment-with-internal-load-balancer) (3 mód).
- Tiltsa le [TLS 1.0-s](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings).
- Változás [TLS titkosító](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings).
- Vezérlő [bejövő forgalom N írási/olvasási portok](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic).
- [Webalkalmazási tűzfal – korlátozzák az adatokat](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-web-application-firewall).
- Lehetővé teszi [Azure SQL Database-forgalom](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-network-architecture-overview).

### <a name="virtual-network"></a>Virtuális hálózat
Az architektúra egy 10.200.0.0/16 címtere a privát virtuális hálózat határozza meg.

**Hálózati biztonsági csoportok**: [Az NSG-k](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) tartalmazza a hozzáférés-vezérlési listák, amelyek engedélyezik vagy megtagadják a forgalmat egy virtuális hálózaton belül. Az NSG-ket egy alhálózatot vagy az egyes Virtuálisgép-szintű forgalom védelmére használható. Az alábbi NSG-k léteznek:
- Az Application Gateway egy NSG-t
- Az App Service Environment-környezet egy NSG-t
- Az SQL Database egy NSG-t
- Egy NSG-t a bástyagazdagép

Az NSG-k mindegyike rendelkezik bizonyos portokat és protokollokat nyissa meg, hogy a megoldás működhet, biztonságos és megfelelően. Emellett a következő konfigurációk engedélyezve vannak az egyes NSG:
  - [Diagnosztikai naplók és események](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) engedélyezett és a egy tárfiókban tárolja.
  - Az Azure Monitor naplóira csatlakozik a [NSG-t a diagnosztika](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json).

**Alhálózatok**: Minden egyes alhálózathoz társítva a megfelelő NSG-t.

**Azure DNS**: A tartománynévrendszer (DNS) felelős fordítása (vagy feloldása) az IP-címét a webhely vagy szolgáltatás nevét. [Az Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) DNS-tartományok egy üzemeltetési szolgáltatás, amely lehetővé teszi a névfeloldás az Azure-infrastruktúra használatával. Az Azure-ban tartományt üzemeltet, a felhasználók ugyanazon hitelesítő adatokkal, API-kkal, eszközökkel és számlázási információkkal más Azure-szolgáltatások által is kezelheti a DNS-rekordok. Az Azure DNS privát DNS-tartományokat is támogatja.

**Az Azure Load Balancer**: [Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) méretezheti az alkalmazásait, és hozzon létre magas rendelkezésre állású szolgáltatások ügyfelek általi használható. Load Balancer bejövő és kimenő forgatókönyveket támogatja. Kis késésű és nagy átviteli sebességet biztosít, és akár több milliónyi összes TCP és UDP-alkalmazás méretezhető.

### <a name="data-in-transit"></a>Az átvitt adatok
Az Azure és az Azure adatközpontok bemenő kommunikáció alapértelmezés szerint titkosítja. Az Azure Portalon az Azure Storage összes tranzakció HTTPS-kapcsolaton keresztül történik.

### <a name="data-at-rest"></a>Inaktív adat

Az architektúra a titkosítás, az adatbázis naplózási és más intézkedéseket az inaktív adatok védi.

**Az Azure Storage**: A titkosított adatok inaktív állapotban, igényeinek megfelelően minden [tárolási](https://azure.microsoft.com/services/storage/) használ [a Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption). Ez a funkció segít a szervezeti biztonsági kötelezettségeit, és a NIST SP 800-171 által meghatározott megfelelőségi követelmények támogatásához adatok biztonságos megőrzésében.

**Az Azure Disk Encryption**: [Lemeztitkosítás](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) használ a Windows BitLocker funkcióját adatlemezek kötettitkosítását. A megoldás segítségével szabályozhatja, és a lemeztitkosítási kulcsok kezelése a Key Vault integrálható.

**Azure SQL Database**: Az SQL Database-példány a következő adatbázis biztonsági intézkedések használja:
-   [Az Active Directory-hitelesítés és engedélyezés](https://docs.microsoft.com/azure/sql-database/sql-database-AAD-authentication) lehetővé teszi az identitáskezelést adatbázis-felhasználók és más Microsoft-szolgáltatások egyetlen központi helyen.
-   [Az SQL database naplózási szolgáltatásával](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) nyomon követi az adatbázisok eseményeit és felvezeti ezeket egy naplófájlba, egy Azure storage-fiókban.
-   SQL-adatbázis használatára van konfigurálva [transzparens adattitkosítás](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Valós idejű titkosítási és visszafejtési az adatbázis, azokhoz kapcsolódó biztonsági mentési és tranzakciós naplófájlokra tárolt adatok inaktív védelme hajtja végre. Transzparens adattitkosítás biztosítja, hogy a tárolt adatok még nem vonatkoznak a jogosulatlan hozzáférést.
-   [Tűzfalszabályok](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) megakadályozzák elérését, adatbázis-kiszolgálók, amíg a megfelelő engedélyekkel. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján.
-   [Az SQL Fenyegetésészlelési](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) lehetővé teszi, hogy az észlelés és válasz a lehetséges veszélyforrásokra azok bekövetkezésekor. Biztonsági riasztások biztosítja a gyanús adatbázis-tevékenységekről, a lehetséges biztonsági résekről, az SQL-injektálási támadások és a rendellenes adatbázis-hozzáférési mintákról.
-   [Titkosított oszlopokat](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) győződjön meg arról, hogy bizalmas adatok soha nem jelenik meg az adatbázis-rendszer egyszerű szövegként. Ha engedélyezett az adattitkosítás, csak az ügyfélalkalmazások vagy alkalmazáskiszolgálók hozzáférést a kulcsokhoz hozzáférhet egyszerű szöveges adatokat.
- [Dinamikus adatmaszkolás](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) által a replikaadatok felhasználók vagy alkalmazások, adatok maszkolása korlátozza a bizalmas adatok. Automatikusan potenciálisan bizalmas adatok felderítéséhez és javasoljuk a alkalmazni lehessen a megfelelő maszkokkal. Dinamikus adatmaszkolás segít hozzáférés korlátozása, így bizalmas adatok nem az adatbázis jogosulatlan hozzáférést. *Be kell tartaniuk az adatbázisséma beállítások hangolását ügyfelek felelőssége.*

### <a name="identity-management"></a>Identitáskezelés
A következő technológiákat az Azure-beli adatokhoz való hozzáférés kezeléséhez képességeket biztosítják:
-   [Az Azure AD](https://azure.microsoft.com/services/active-directory/) van a Microsoft több-bérlős felhőalapú címtár- és identitáskezelési szolgáltatás. Ez a megoldás az összes felhasználó jönnek létre az Azure ad-ben, és az SQL-adatbázishoz hozzáféréssel rendelkező felhasználókat tartalmazzák.
-   Hitelesítés az alkalmazás Azure AD használatával történik. További információkért lásd: hogyan [alkalmazások integrálása az Azure ad-vel](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Az adatbázis oszloptitkosítás is az Azure AD az SQL Database az alkalmazás hitelesítéséhez. További információkért lásd: hogyan [bizalmas adatok védelme az SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Az Azure RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) segítségével a rendszergazdák hozzáférés részletes engedélyeket határozhat meg. Azt, akkor is csak a mértékű hozzáférést biztosítson a felhasználóknak frissíteniük kell a munkája elvégzéséhez. Ahelyett, hogy minden felhasználó ugyanolyan korlátlan hozzáférést az Azure-erőforrások ad, a rendszergazdák engedélyezhetik csak bizonyos műveleteket, erőforrások és adatok eléréséhez. Az előfizetés-rendszergazda előfizetéshez való hozzáférés korlátozódik.
- [Az Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) minimalizálása érdekében a felhasználók száma, akik hozzáférhetnek bizonyos adatok ügyfelek által használható. A rendszergazdák Azure AD Privileged Identity Management segítségével Fedezze fel, korlátozhatja és emelt szintű identitások és azok erőforrásokhoz való hozzáférésének figyelése. Ez a funkció is segítségével igény szerinti – igény rendszergazdai hozzáférés kényszerítésére.
- [Az Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) észleli a szervezet identitásait érintő lehetséges biztonsági résekről. Konfigurálja az automatikus válaszok a szervezet identitásait kapcsolódó észlelt gyanús tevékenységek. Azt is kivizsgálja a problémák megoldásához a megfelelő művelet végrehajtása a gyanús események.

### <a name="security"></a>Biztonság
**Titkok kezelése**: A megoldás [Key Vault](https://azure.microsoft.com/services/key-vault/) kulcsok és titkos kulcsok felügyeletéhez. Key Vault segít a védelem titkosítási kulcsok és titkos kódok felhőalapú alkalmazások és szolgáltatások által használt. A következő Key Vault-képességek segítségével az ügyfelek adatok védelme érdekében:
- Speciális hozzáférési szabályzatok kell alapon vannak konfigurálva.
- A Key Vault hozzáférési szabályzatok a minimálisan szükséges engedélyeket kulcsok és titkos kulcsok vannak meghatározva.
- Összes kulcsot és titkos kulcsok a Key Vaultban van lejárati dátumát.
- Speciális hardveres biztonsági modulok által védett összes kulcsok a Key Vaultban. A kulcs típusa a hardveres biztonsági modul által védetté 2048-bites RSA-kulcsok.
- Minden felhasználó és identitások minimálisan szükséges engedélyeket kapnak az RBAC használatával.
- Diagnosztikai naplók a Key vault legalább 365 napos megőrzési idővel rendelkező engedélyezve vannak.
- A szükséges kapcsolatok engedélyezett titkosítási műveletek kulcsok korlátozódnak.

**Azure Security Center**: A [a Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), ügyfelek is központilag alkalmazása és a számítási feladatok biztonsági házirendek kezelése, korlátozhatja a fenyegetéseknek való kitettséget, felismeri és elháríthatja a támadásokat. A Security Center is éri el a meglévő konfigurációk az Azure-szolgáltatások konfigurációs és szolgáltatási javaslatok javíthatja biztonsági helyzetét és adatok védelme érdekében.

A Security Center használatával észlelési képességek széles ügyfelek esetleges támadások, hogy a cél riasztás környezetük. Ezek a riasztások értékes információkat tartalmaznak arról, hogy mi váltotta ki a riasztást, valamint a támadás forrásáról és az általa célba vett erőforrásokról. A Security Center készletével rendelkezik [biztonsági riasztások az előre meghatározott](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) , amely vannak aktiválódik, ha a fenyegetések vagy gyanús tevékenység történik. Az ügyfelek használhatják [egyéni riasztási szabályok](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) meghatározásához a környezetből már begyűjtött adatok alapján új biztonsági riasztásokat.

A Security Center rangsorolt biztonsági riasztások és incidensek biztosít. A Security Center egyszerűbbé teszi az ügyfelek felderítéséhez és a potenciális biztonsági problémákat. A [fenyegetésfelderítési jelentés](https://docs.microsoft.com/azure/security-center/security-center-threat-report) jön létre a rendszer minden egyes észlelt fenyegetés. Incidensmegoldási csapat a jelentéseket használhatja, amikor elhárításban, és vizsgálja meg.

**Az Azure Application Gateway**: Az architektúra csökkenti a biztonsági rések kockázatát az application gateway webalkalmazási tűzfal konfigurálva és engedélyezve van az OWASP szabálykészlet használatával. További képességek:

- [End-to-end-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- Engedélyezése [SSL-alapú kiszervezéshez](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal).
- Tiltsa le [a TLS 1.0 és 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- [Webalkalmazási tűzfal](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (megelőzés üzemmód).
- [Megelőzés üzemmód](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) az OWASP 3.0-s szabály beállítása.
- Engedélyezése [diagnosztikai célú naplózásának](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).
- [Egyéni állapotmintákkal](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal).
- [A Security Center](https://azure.microsoft.com/services/security-center) és [az Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) nyújtanak további védelmet és értesítések. A Security Center megbízhatóságibesorolás-kezelési rendszert is biztosít.

### <a name="logging-and-auditing"></a>Naplózás és vizsgálat

Azure-szolgáltatások széles körben system és a felhasználói tevékenység, valamint a rendszerállapot jelentkezzen be:
- **A Tevékenységnaplók**: [A Tevékenységnaplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) adjon meg egy előfizetéshez tartozó erőforrásokon végrehajtott műveletekkel kapcsolatos információk. A Tevékenységnaplók segítségével határozza meg a műveletet kezdeményező, az eseményt, és állapot ideje.
- **Diagnosztikai naplók**: [Diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) minden erőforrás által kibocsátott az összes napló tartalmazza. Ezek a naplók például a Windows rendszer-eseménynaplói, a Storage-naplók, a Key Vault-naplók és a Application Gateway hozzáférés és a tűzfal a naplókat. Az összes diagnosztikai naplók írni egy központosított, titkosított csatornákon történik az Azure storage-fiókját archiválási. Felhasználók is konfigurálhatók a megőrzés időtartamát, legfeljebb 730 napig, az adott igényeknek.

**Az Azure Monitor naplóira**: Naplókat a rendszer összevont [naplózza az Azure Monitor](https://azure.microsoft.com/services/log-analytics/) feldolgozási, tárolására és-irányítópult jelentéseit. Az adatgyűjtés után azt rendszer adattípusonként külön táblába rendezi az egyes adattípusokhoz belül a Log Analytics-munkaterületek. Ily módon az összes adat elemezhetők együtt, az eredeti forrástól függetlenül. A Security Center integrálható az Azure Monitor naplóira. Ügyfelek Kusto-lekérdezés segítségével a biztonsági események adatainak eléréséhez és más szolgáltatások származó adatokat kombinálni.

A következő Azure [figyelési megoldások](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) Ez az architektúra egy része szerepel:
-   [Az Active Directory assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Az Active Directory állapotának ellenőrzése megoldás rendszeres időközönkénti értékeli az a kockázat és kiszolgálói környezetek állapotát. Biztosít a telepített kiszolgálói infrastruktúra vonatkozó javaslatok rangsorolt listáját.
- [SQL-értékeléssel](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): Az SQL Health Check megoldás rendszeres időközönként felméri az a kockázat és kiszolgálói környezetek állapotát. Ügyfelek biztosít a telepített kiszolgálói infrastruktúra vonatkozó javaslatok rangsorolt listáját.
- [Az ügynök állapota](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Az Agent Health megoldás jelentések hány ügynök van telepítve, és a földrajzi eloszlása. Hány ügynök nem válaszol, és küldje el az operatív adatokat, az ügynökök számát is jelenti.
-   [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Az Activity Log Analytics megoldás az ügyfél az összes Azure-előfizetések az Azure-Tevékenységnaplók elemzésének segíti.

**Az Azure Automation**: [Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) tárolja, fut, és kezeli a runbookok. Ebben a megoldásban lévő runbookok segítségével naplók gyűjtése az SQL Database-ből. Az ügyfelek használhatják az Automation [Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) megoldás könnyedén azonosíthatja a változtatásokat a környezetben.

**Az Azure Monitor**: [A figyelő](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) segít a felhasználóknak a teljesítmény, a biztonság fenntartására és a trendek azonosítására. Szervezetek használhatják azt a naplózása, létre riasztásokat és archiválhat adatokat. Akkor is nyomon követheti a saját Azure-erőforrások API-hívások.

**Az Application Insights**: [Az Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) van egy bővíthető alkalmazásteljesítmény-felügyeleti szolgáltatás webfejlesztőknek, több platformon. Az Application Insights felismeri a teljesítményanomáliákat. Ügyfelek használhatják azt az élő webalkalmazásának figyelésére. Az Application Insights segítségével diagnosztizálhatja a problémákat, és megismerheti a felhasználók mire az alkalmazás az ügyfelek rendkívül hatékony elemzési eszközöket tartalmazza. Ez&#39;s, amelyek segítségével az ügyfelek folyamatosan javíthassa a teljesítményt és a használhatóságot.

## <a name="threat-model"></a>Fenyegetések modellezése

A referenciaarchitektúra az adatfolyam-diagram érhető el az [letöltése](https://aka.ms/nist171-paaswa-tm) vagy itt található. Ez a modell segítségével az ügyfelek megismerhetik a pontokat a rendszer infrastruktúra esetleges kockázat, ha azok hajtsa végre a módosításokat.

![A NIST SP 800-171 fenyegetések modellezése PaaS-webalkalmazás](images/nist171-paaswa-threat-model.png "NIST SP 800-171 fenyegetések modellezése a PaaS-webalkalmazás")

## <a name="compliance-documentation"></a>Megfelelőségi dokumentációja
A [Azure biztonsági és megfelelőségi terv - 800-171 NIST SP vevő felelőssége mátrix](https://aka.ms/nist171-crm) NIST SP 800-171 által igényelt összes biztonsági vezérlő sorolja fel. Ez a mátrix részletezi a Microsoft, az ügyfél felelőssége végrehajtására az egyes vezérlőelemek-e, vagy a kettő között megosztott.

A [Azure biztonsági és -megfelelőségi terv - 800-171 NIST SP PaaS webes alkalmazás ellenőrzési megvalósítási mátrix](https://aka.ms/nist171-paaswa-cim) melyik NIST SP a 800-171 szabályozza a PaaS webes alkalmazásarchitektúra tárgyalt információkkal. Hogyan megvalósítása megfelel-e a követelményeknek, az egyes kezelt vezérlőelemek a részletes leírását tartalmazza.

## <a name="guidance-and-recommendations"></a>Útmutatás és javaslatok
### <a name="vpn-and-expressroute"></a>VPN és ExpressRoute
Biztonságos VPN-alagúton vagy [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) konfigurálni kell, hogy biztonságos kapcsolatot a PaaS webes alkalmazás referenciaarchitektúra részeként üzembe helyezett erőforrásokkal. Megfelelően állítja be a VPN-t vagy ExpressRoute, az ügyfelek hozzáadhatja egy védelmi réteget biztosít adatokat átvitel közben.

Az Azure-ral biztonságos VPN-alagút alkalmazásával egy helyszíni hálózat és a egy Azure virtuális hálózat közötti virtuális magánhálózati kapcsolat hozható létre. Ezt a kapcsolatot az interneten keresztül történik, és lehetővé teszi az ügyfelek számára, hogy egy titkosított kapcsolatot, az ügyfél hálózati és az Azure között lévő biztonságosan "csatorna" információkat. Site-to-site VPN egy olyan biztonságos, érett technológia, évtizedes a vállalatok által központilag telepített. A [IPsec-alagút módú](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) ezt a beállítást, mint egy titkosítási mechanizmus használatban van.

A VPN-alagút belül adatforgalomra site-to-site VPN-nel az internethez, mert a Microsoft kínál egy másik még biztonságosabb kapcsolat lehetőséget. Az ExpressRoute dedikált WAN Azure-ban és a egy helyszíni helyhez vagy egy Exchange-szolgáltató közötti kapcsolat. Az ExpressRoute-kapcsolatok közvetlenül csatlakozhat az ügyfél távközlési szolgáltató. Ennek következtében az adatok nem az interneten keresztül továbbítani, és nincs kitéve. Ezek a kapcsolatok több megbízhatóság megbízhatóbbak, gyorsabbak, kisebb a késésük, és biztonságosabbak a szokásos internetkapcsolatoknál kínálnak.

Ajánlott eljárások végrehajtásához egy biztonságos hibrid hálózatot, amely kiterjeszti a helyszíni hálózatot az Azure-bA [elérhető](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Jogi nyilatkozat

 - Ez a dokumentum csak tájékoztatási célokat szolgál. A MICROSOFT NEM VÁLLAL SEMMILYEN KIFEJEZETT, KIFEJEZETT, VÉLELMEZETT VAGY KÖTELEZŐ GARANCIÁT A DOKUMENTUMBAN SZEREPLŐ INFORMÁCIÓRA VONATKOZÓAN. Ez a dokumentum biztosított "as-van." Információk és vélemények ebben a dokumentumban URL-CÍMEK és más internetes webhelyekre utaló hivatkozások értesítés nélkül változhatnak. Ez a dokumentum olvasásakor ügyfelek felelősségére használja.
 - Ez a dokumentum nem biztosít semmilyen jogot semmilyen Microsoft-termék vagy a megoldások a szellemi tulajdonhoz ügyfelek.
 - Ügyfelek másolhatja és használhatja ezt a dokumentumot belső, hivatkozási célokból.
 - Bizonyos ajánlások a dokumentum eredményezhet megnövekedett adat-, hálózati vagy számítási erőforrás-használat az Azure-ban, és előfordulhat, hogy növelje az ügyfél Azure licencek vagy előfizetések költségeit.
 - Ez az architektúra célja, hogy az ügyfelek számára az adott követelményekhez beállítása alapjaként szolgálja ki, és nem használható-éles környezetben van.
 - Ez a dokumentum referenciaként fejlesztik, és nem használható minden olyan eszközt, amely szerint egy ügyfél megfelel a megadott megfelelőségi követelmények és előírások meghatározásához. Ügyfelek jóváhagyott megvalósítás az ügyfeleknél a szervezet jogi támogatást kell kérnie.
