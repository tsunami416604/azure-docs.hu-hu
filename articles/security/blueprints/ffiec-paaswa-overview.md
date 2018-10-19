---
title: Azure biztonsági és megfelelőségi terv - FFIEC PaaS webes alkalmazás
description: Azure biztonsági és megfelelőségi terv - FFIEC PaaS webes alkalmazás
services: security
author: meladie
ms.assetid: a552e313-2b46-4001-b6e2-bed0b7757ac5
ms.service: security
ms.topic: article
ms.date: 06/20/2018
ms.author: meladie
ms.openlocfilehash: f4c25a65583fe700466cd63e5346316d0149b35a
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2018
ms.locfileid: "49409288"
---
# <a name="azure-security-and-compliance-blueprint-paas-web-application-for-ffiec-financial-services"></a>Azure biztonsági és megfelelőségi terv: PaaS-webalkalmazás FFIEC a pénzügyi szolgáltatások

## <a name="overview"></a>Áttekintés

Az Azure biztonsági és megfelelőségi tervezet Automation nyújt útmutatást a platformszolgáltatás (PaaS) környezetként, a gyűjtemény, tárolását és lekérését a Szövetségi pénzügyi intézmény által szabályozott módon pénzügyi adatok megfelelő platform központi telepítés Vizsgálat Tanács (FFIEC). Ez a megoldás automatizálja a központi telepítése és konfigurálása Azure-erőforrások általános referenciaarchitektúra, bemutatásához, amelyben ügyfeleink megfelel bizonyos biztonsági és megfelelőségi követelmények és szolgál az ügyfelek alapjaként módon és saját megoldások konfigurálásához az Azure-ban. A megoldás a FFIEC szakkönyveket elvárásoknak egy részhalmazát valósítja meg. FFIEC követelményeknek, és ez a megoldás kapcsolatos további információkért lásd: a [megfelelőségi dokumentáció](#compliance-documentation) szakaszban.

Az Azure biztonsági és megfelelőségi tervezet Automation üzembe helyez egy PaaS webes alkalmazás referenciaarchitektúrájához, ha az előre konfigurált biztonsági vezérlők segítségével az ügyfelek FFIEC követelményeknek való megfelelés eléréséhez. A megoldás Azure Resource Manager-sablonok és erőforrások üzembe helyezésének és konfigurációs PowerShell-parancsfájlok áll.

Ez az architektúra célja, hogy az ügyfelek számára az adott követelményekhez beállítása alapjaként szolgálja ki, és nem használható-éles környezetben van. Ebben a környezetben, módosítás nélküli egy alkalmazás telepítése azonban nem teljes mértékben a követelményeinek FFIEC célok. Vegye figyelembe a következőket:
- Ez az architektúra egy alapkonfiguráció segítenek az ügyfeleknek FFIEC megfelelő módon használhatja az Azure biztosít.
- Ügyfelek végző megfelelő biztonsági és megfelelőségi vizsgálata a beépített Ez az architektúra használatával, ahogy követelményei eltérőek lehetnek megoldásoktól alapján minden ügyfél megvalósítása tulajdonságairól.

FFIEC-megfelelőség eléréséhez szükséges, hogy a minősített auditorok tanúsítása üzemi ügyfél megoldást. Naplózás FFIEC a tag ügynökségek, beleértve a kormányzója tábla a Szövetségi tartalék rendszer (FRB), a Szövetségi letéti biztosítási Corporation (FDIC), a nemzeti Credit Union adminisztrációs (NCUA), a Hivatala az elbírálók felügyeletét a pénznem (OCC), és a fogyasztói pénzügyi védelmi iroda (CFPB). Ezek az elbírálók igazolja, hogy eseményeket naplózott intézménye függetlenség karbantartását végző assessors végzi. Ügyfelek végző megfelelő biztonsági és megfelelőségi értékelést bármely megoldás használatával létrehozott ebben az architektúrában követelmények eltérőek lehetnek, hogy milyen ügyről van minden ügyfél megvalósítása alapján.

Kattintson a [Itt](https://aka.ms/ffiec-paaswa-repo) üzembe helyezési utasítások.

## <a name="architecture-diagram-and-components"></a>Architektúra és összetevők

Az Azure biztonsági és megfelelőségi tervezet Automation üzembe helyez egy referencia-architektúra egy PaaS-webalkalmazás, egy Azure SQL Database-háttérrendszerrel. A webalkalmazás elkülönített Azure App Service-környezet, amely egy privát, dedikált környezetben az Azure-adatközpontban lévő üzemeltetett. A környezet elosztja a forgalmat a webes alkalmazás az Azure által kezelt virtuális gépek között. Ez az architektúra a hálózati biztonsági csoportok, egy Application Gateway, az Azure DNS-ben és a Load Balancer is tartalmaz.

Bővített analitika és a jelentéskészítés az Azure SQL Database-adatbázisok is konfigurálhatók, az oszlopcentrikus indexek. Az Azure SQL Database horizontálisan fel vagy le vagy kapcsolja ki teljesen ügyfelek általi használatot válaszul is. SQL forgalmat titkosítja a rendszer SSL-lel azáltal, önaláírt tanúsítványokat. Ajánlott eljárásként az Azure a fokozott biztonság egy megbízható hitelesítésszolgáltatótól használatát javasolja.

A megoldás az Azure Storage-fiókok, amelyek a felhasználók beállíthatják az inaktív adatok bizalmas mivoltát a Storage Service Encryption segítségével. Az Azure rugalmasságot ügyfél kiválasztott adatközpontban adatok három másolatát tárolja. Földrajzi georedundáns tárolás biztosítja, hogy az adatok replikálása másodlagos adatközpontba több száz mérföld távolságban, és újra azt az adatközpontot, az ügyfél elsődleges adatközpont káros eseményt megakadályozza az adatvesztést eredményez belül három példányban tárolja adatok.

A fokozott biztonság érdekében ebben a megoldásban az összes erőforrás egy erőforráscsoportba tartozó Azure Resource Manageren keresztül felügyelt. Az Azure Active Directory szerepköralapú hozzáférés-vezérlés való hozzáférés szabályozása használatos üzembe helyezett erőforrások, például a kulcsok Azure Key vaultban. A fájlrendszer állapotának Azure monitoron keresztül figyel. Ügyfelek konfigurálása mindkét figyelési szolgáltatásokat naplók rögzítése és a fájlrendszer állapotának megjelenítése egyetlen, könnyen navigálható-irányítópulton.

Az Azure SQL Database úgy konfigurálva, hogy az Azure SQL Database egy biztonságos VPN- vagy ExpressRoute-kapcsolaton keresztüli elérésére a helyi gépen futó SQL Server Management Studio gyakran kezeli.

Az Application Insights emellett valós idejű alkalmazásteljesítmény-felügyelet és a Log Analytics-analitikát biztosít. **A Microsoft javasolja, hogy a felügyeleti és az adatok importálása a referencia architektúra alhálózatában VPN vagy ExpressRoute-kapcsolat beállítása.**

![PaaS webes alkalmazás FFIEC architekturális diagramja](images/ffiec-paaswa-architecture.png "PaaS webes alkalmazás FFIEC architekturális diagramja")

Ez a megoldás a következő Azure-szolgáltatásokat használ. Az üzembe helyezési architektúra részletei a [üzembe helyezési architektúrája](#deployment-architecture) szakaszban.

- Application Gateway
    - Web application firewall (Webalkalmazási tűzfal)
        - Tűzfalmód: megelőzése
        - Set-szabály: OWASP
        - Figyelő portja: 443
- Application Insights
- Azure Active Directory
- Azure-alkalmazás Service Environment v2
- Azure Automation
- Azure DNS
- Azure Key Vault
- Azure Load Balancer
- Azure Monitor
- Azure Resource Manager
- Azure Security Center
- Azure SQL Database
- Azure Storage
- Azure Log Analytics
- Azure Virtual Network
    - (1) /16 hálózati
    - (4) /24 hálózatok
    - Network security groups (Hálózati biztonsági csoportok)
- Azure Web App

## <a name="deployment-architecture"></a>Üzembe helyezési architektúrája

A következő szakaszt az üzembe helyezés és a megvalósítás elemek részletei.

**Az Azure Resource Manager**: [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) lehetővé teszi az ügyfelek számára, hogy a megoldás egy csoportként erőforrásokat. Az ügyfelek központi telepítése, frissítése, vagy törölje az összes erőforrását egyetlen, koordinált műveletben lévő megoldás. Ügyfelek telepítéshez egy sablont használ, és amely különböző, például tesztelési, átmeneti és éles környezetben is képes működni. A Resource Manager biztosít a biztonsági, naplózási és címkézési szolgáltatásokat, hogy az ügyfelek az erőforrások kezelésében a telepítést követően.

**Bástyagazdagép**: A bástyagazdagép a központi hely, amely lehetővé teszi a felhasználók hozzáférhessenek a környezetben telepített erőforrás. A bástyagazdagép üzembe helyezett erőforrásokhoz való biztonságos kapcsolatot nyújt azáltal, hogy csak nyilvános IP-címekről érkező távoli forgalmat a biztonságos elemek listájához. Távoli asztali (RDP) forgalmat lehetővé teszik, hogy a forgalom forrását kell definiálni, a hálózati biztonsági csoport tagja.

Ez a megoldás létrehoz egy virtuális gépet egy tartományhoz csatlakoztatott megerősített gazdagépként az alábbi konfigurációkkal:
-   [A kártevőirtó bővítmény](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Az Azure Diagnostics bővítmény](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Az Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) az Azure Key Vaulttal
-   Egy [automatikus leállítási házirend](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) csökkenthető a fogyasztása a virtuális gépi erőforrások, amikor nincs használatban
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) engedélyezve van, hogy a hitelesítő adatokat és egyéb titkos adatait, amely el van különítve a futó operációs rendszert a védett környezetben futnak

**App Service Environment v2-környezetet**: az Azure App Service-környezet egy App Service-funkció, amely biztonságos, nagy léptékű az App Service-alkalmazások futtatásához egy teljesen elkülönített és dedikált környezetet biztosít. Ez a funkció elkülönítési FFIEC megfelelőségi követelmények teljesítése érdekében van szükség.

App Service Environment-környezetek legyenek különítve, csak az egyetlen ügyfél előfizetéséhez alkalmazások futtatására és mindig egy virtuális hálózatban üzemelnek. Az elkülönítési funkciója lehetővé teszi a referenciaarchitektúrát a teljes bérlők elkülönítésére, eltávolítja a az Azure több-bérlős környezetben az App Service Environment-környezet üzembe helyezett erőforrások számbavétele a ezeket több bérlő nem rendelkezik. Mindkét alkalmazás bejövő és kimenő hálózati forgalom részletesen szabályozhatja az ügyfél rendelkezik, és az alkalmazások nagy sebességű, biztonságos kapcsolatot létesíthet virtuális hálózatokon keresztül kapcsolódjanak a helyi vállalati erőforrásokhoz. Ügyfelek automatikusan skálázhatók a "" App Service environmenttel terhelési mérőszámok, a rendelkezésre álló költségvetési vagy egy meghatározott ütemezés alapján.

Az architektúra App Service Environment-környezet használata lehetővé teszi a következő beállításokat:

- Egy biztonságos Azure virtuális hálózaton belüli üzemeltethet, és a hálózati biztonsági szabály
- Önaláírt belső load balancer tanúsítványt a HTTPS-kommunikációra. Ajánlott eljárásként a Microsoft a fokozott biztonság egy megbízható hitelesítésszolgáltatótól használatát javasolja.
- [Belső terheléselosztási mód](https://docs.microsoft.com/azure/app-service-web/app-service-environment-with-internal-load-balancer)
- Tiltsa le [TLS 1.0-s](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Változás [TLS-titkosítás](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Vezérlő [bejövő forgalom N írási/olvasási portok](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic)
- [Webalkalmazási tűzfal – korlátozzák az adatokat](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- Lehetővé teszi [Azure SQL Database-forgalom](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-network-architecture-overview)

**Az Azure Web App**: [Azure Web Apps](https://docs.microsoft.com/azure/app-service/) lehetővé teszi ügyfeleink számára hozhat létre és üzemeltethet webalkalmazásokat az általuk választott programozási nyelven infrastruktúra kezelése nélkül. Automatikus méretezést és magas rendelkezésre állást kínál, támogatja a Windows és a Linux rendszert is, valamint lehetővé teszi az automatikus üzembe helyezéseket a GitHub, az Azure DevOps vagy bármely egyéb Git-adattár használatával.

### <a name="virtual-network"></a>Virtual Network

Az architektúra egy 10.200.0.0/16 címtere a privát virtuális hálózat határozza meg.

**Hálózati biztonsági csoportok**: [hálózati biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) tartalmazza a hozzáférés-vezérlési listák, amelyek engedélyezik vagy megtagadják a forgalmat egy virtuális hálózaton belül. Hálózati biztonsági csoportok segítségével biztonságos forgalom egy alhálózatot vagy különálló virtuális gép szintjén. Létezik a következő hálózati biztonsági csoportok:

- az Application Gateway 1 hálózati biztonsági csoport
- 1 hálózati biztonsági csoportot az App Service Environment-környezet
- az Azure SQL Database 1 hálózati biztonsági csoport
- 1 bástyagazdagép hálózati biztonsági csoport

A hálózati biztonsági csoportok rendelkezik bizonyos portokat és protokollokat nyissa meg, hogy a megoldás működhet, biztonságos és megfelelően. Emellett a következő konfigurációk engedélyezve vannak az egyes hálózati biztonsági csoport:

- [Diagnosztikai naplók és események](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) engedélyezve van, és a storage-fiókban tárolt
- A log Analytics csatlakozik a [hálózati biztonsági csoport&#39;s diagnosztikai naplók](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Alhálózatok**: minden egyes alhálózat a megfelelő hálózati biztonsági csoport társítva.

**Az Azure DNS**: A Domain Name System, vagy a DNS-beli felelős fordítása (vagy feloldása) az IP-címét a webhely vagy szolgáltatás nevét. [Az Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) DNS-tartományok egy üzemeltetési szolgáltatás, amely Azure-infrastruktúra névfeloldáshoz. Az Azure-ban tartományt üzemeltet, felhasználók ugyanazon hitelesítő adatokkal, API-kkal, eszközökkel és számlázási információkkal más Azure-szolgáltatások DNS-rekordok is kezelheti. Az Azure DNS privát DNS-tartományokat is támogatja.

**Az Azure Load Balancer**: [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) lehetővé teszi az alkalmazások méretezése és magas rendelkezésre állású szolgáltatások létrehozása. Load Balancer bejövő, valamint a kimenő forgatókönyveket teszi lehetővé, és alacsony késleltetésű, nagy teljesítményű, és akár több milliónyi összes TCP és UDP-alkalmazás méretezhető.

### <a name="data-in-transit"></a>Az átvitt adatok

Az Azure és az Azure adatközpontok bemenő kommunikáció alapértelmezés szerint titkosítja. Az Azure Portalon az Azure Storage összes tranzakció HTTPS-kapcsolaton keresztül történik.

### <a name="data-at-rest"></a>Inaktív adat

Az architektúra a titkosítás, az adatbázis naplózási és más intézkedéseket az inaktív adatok védi.

**Az Azure Storage**: rest-követelményeket, a titkosított adatok kielégítése érdekében minden [Azure Storage](https://azure.microsoft.com/services/storage/) használ [a Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption). Ez segít a szervezeti biztonsági kötelezettségeit és megfelelőségi követelmények FFIEC által meghatározott adatok biztonságos megőrzésében.

**Az Azure Disk Encryption**: [az Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) az adatlemezek kötettitkosítását adja meg a Windows BitLocker funkcióját használja. A megoldás integrálható az Azure Key Vault segítségével szabályozhatja, és kezelhetik a lemeztitkosítási kulcsokat.

**Az Azure SQL Database**: az Azure SQL Database-példányt használja a következő adatbázis biztonsági intézkedéseket:

- [Az Active Directory-hitelesítés és engedélyezés](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) lehetővé teszi az identitáskezelést adatbázis-felhasználók és más Microsoft-szolgáltatások egyetlen központi helyen.
- [Az SQL database naplózási szolgáltatásával](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) nyomon követi az adatbázisok eseményeit és felvezeti ezeket egy naplófájlba, egy Azure storage-fiókban.
- Az Azure SQL Database használatára van konfigurálva [transzparens adattitkosítás](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), melyik teljesít valós időben titkosítja és fejti vissza az adatbázist, az azokhoz kapcsolódó biztonsági mentési, és a rest-tranzakciós naplófájlokat: adatok védelme érdekében. Transzparens adattitkosítás biztosítja, hogy a tárolt adatok nem lett bárki hozzáférhet.
- [Tűzfalszabályok](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) megakadályozzák elérését, adatbázis-kiszolgálók, amíg a megfelelő engedélyekkel. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján.
- [Az SQL Fenyegetésészlelési](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) lehetővé teszi, hogy az észlelés és válasz a lehetséges veszélyforrásokra, bekövetkezésük pillanatában észlelhessék a biztonsági riasztásokat a gyanús adatbázis-tevékenységekről, a lehetséges biztonsági résekről, az SQL-injektálási támadások és a rendellenes adatbázis-hozzáférés minták.
- [Titkosított oszlopokat](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) győződjön meg arról, hogy bizalmas adatok soha nem jelenik meg, mint belül az adatbázis-rendszer egyszerű szövegként. Adatok titkosításának engedélyezése után csak az ügyfélalkalmazások vagy alkalmazáskiszolgálók hozzáférést a kulcsokhoz hozzáférhet egyszerű szöveges adatokat.
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

**Az Azure Security Center**: A [az Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), ügyfelek is központilag alkalmazása és a számítási feladatok biztonsági házirendek kezelése, korlátozhatja a fenyegetéseknek való kitettséget, felismeri és elháríthatja a támadásokat. Az Azure Security Center ezenkívül meglévő konfigurációk az Azure-szolgáltatások konfigurációs és szolgáltatási javaslatok javíthatja biztonsági helyzetét és adatok védelme érdekében fér hozzá.

Az Azure Security Center észlelési képességek széles használatával ügyfeleket a környezetük célzó lehetséges támadások esetén. Ezek a riasztások értékes információkat tartalmaznak arról, hogy mi váltotta ki a riasztást, valamint a támadás forrásáról és az általa célba vett erőforrásokról. Az Azure Security Center készletével rendelkezik [biztonsági riasztások az előre meghatározott](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), amely vannak aktiválódik, ha a fenyegetések vagy gyanús tevékenységek. [Egyéni riasztási szabályok](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) az Azure Security Center lehetővé teszi ügyfeleink számára a környezetből már begyűjtött adatok alapján új biztonsági riasztásokat definiálhat.

Az Azure Security Center itt rangsorolt biztonsági riasztások és incidensek, így egyszerűbb a vásárlók megtalálhatják és a potenciális biztonsági problémákat. A [fenyegetésfelderítési jelentés](https://docs.microsoft.com/azure/security-center/security-center-threat-report) jön létre a rendszer minden egyes észlelt fenyegetés incidensmegoldási csapat segítség vizsgálatában és védekezhet a fenyegetések.

**Az Azure Application Gateway**: az architektúra csökkenti a biztonsági rések használatával az Azure Application Gateway webalkalmazási tűzfal konfigurálása, és az OWASP szabálykészletben engedélyezve kockázatát. További képességek:

- [Vége a közötti SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Engedélyezése [SSL-alapú Kiszervezéshez](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Tiltsa le [a TLS 1.0 és 1.1 verzió](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Webalkalmazási tűzfal](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (megelőzés üzemmód)
- [Megelőzés üzemmód](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) az OWASP 3.0 szabálykészletben
- Engedélyezése [diagnosztikai naplózás](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Egyéni állapot-mintavételei](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- [Az Azure Security Center](https://azure.microsoft.com/services/security-center) és [az Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) nyújtanak további védelmet és értesítések. Az Azure Security Center megbízhatóságibesorolás-kezelési rendszert is biztosít.

### <a name="logging-and-auditing"></a>Naplózás és vizsgálat

Azure-szolgáltatások széles körben system és a felhasználói tevékenység, valamint a rendszerállapot jelentkezzen be:
- **A Tevékenységnaplók**: [tevékenységeket tartalmazó naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) adjon meg egy előfizetéshez tartozó erőforrásokon végrehajtott műveletekkel kapcsolatos információk. A Tevékenységnaplók segítségével határozza meg a műveletet kezdeményező, az eseményt, és állapot ideje.
- **Diagnosztikai naplók**: [diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) minden erőforrás által kibocsátott az összes napló tartalmazza. Ezek a naplók például a Windows rendszer-eseménynaplói, az Azure Storage-naplók, a Key Vault-naplók és az Application Gateway hozzáférés és a tűzfal a naplókat. Az összes diagnosztikai naplók írni egy központosított, titkosított csatornákon történik az Azure storage-fiókját archiválási. A megőrzési felhasználó által konfigurálható, mentése és 730 nap között, a megőrzési a szervezet konkrét követelményeinek.

**Log Analytics**: ezeket a naplókat a rendszer összevont [Log Analytics](https://azure.microsoft.com/services/log-analytics/) feldolgozási, tárolására és-irányítópult jelentéseit. Követően az adatok felépítéséről különböző adattípusokhoz belül a Log Analytics-munkaterületek, amely lehetővé teszi az adatok együttes elemzését az eredeti forrástól függetlenül a külön táblába. Ezen túlmenően az Azure Security Center integrálható a Log Analytics ami lehetővé teszi az ügyfelek számára a Log Analytics-lekérdezések használata a biztonsági események adatainak eléréséhez és más szolgáltatások származó adatokat kombinálni.

A következő Log Analytics [felügyeleti megoldások](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) Ez az architektúra egy része szerepel:
-   [Az Active Directory Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): az Active Directory állapot-ellenőrzés megoldás a kockázat és kiszolgálói környezetek állapotát értékeli a rendszeres időközönkénti, és a telepített kiszolgálói infrastruktúra vonatkozó javaslatok rangsorolt listáját tartalmazza.
- [SQL-értékeléssel](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): az SQL Health Check megoldás a kockázat és kiszolgálói környezetek állapotát értékeli a rendszeres időközönkénti, és nyújt a felhasználók számára a telepített kiszolgálói infrastruktúra vonatkozó javaslatok rangsorolt listáját.
- [Az ügynök állapota](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): az Agent Health megoldás hány ügynök van telepítve, és a földrajzi elosztás, valamint hány ügynök, amely nem válaszol és működési adatokat küld be, amely az ügynökök számát jelenti.
-   [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): az ügyfél az összes Azure-előfizetések az Azure-Tevékenységnaplók elemzésének segíti az Activity Log Analytics megoldás.

**Az Azure Automation**: [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) tárolja, fut, és kezeli a runbookok. Ebben a megoldásban lévő runbookok segítségével naplók gyűjtése az Azure SQL Database-ből. Az Automation [Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) megoldás lehetővé teszi, hogy az ügyfelek számára, hogy könnyen azonosíthassa a változtatásokat a környezetben.

**Az Azure Monitor**: [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) segít a felhasználóknak a teljesítmény, biztonság fenntartására és a trendek azonosítására által lehetővé teszi a cégeknek naplózása, riasztásokat hozhat létre, és archiválja az adatokat, többek között API-hívások nyomon követése az Azure-ban az erőforrásokat.

**Az Application Insights**: [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) egy bővíthető alkalmazásteljesítmény-felügyeleti szolgáltatás webfejlesztőknek, több platformon. Az Application Insights felismeri a teljesítményanomáliákat és az ügyfelek használhatják az élő webalkalmazásának figyelésére. Ez magában foglalja a rendkívül hatékony elemzési eszközöket segítenek az ügyfeleknek diagnosztizálhatja a problémákat, és megérteni, milyen felhasználók valójában hogyan használják az alkalmazást. Ez&#39;s, amelyek segítségével az ügyfelek folyamatosan javíthassa a teljesítményt és a használhatóságot.

## <a name="threat-model"></a>Fenyegetések modellezése

A referenciaarchitektúra az adatfolyam-diagram érhető el az [letöltése](https://aka.ms/ffiec-paaswa-tm) vagy alább találja. Ez a modell segítségével az ügyfelek megismerhetik a pontokat a rendszer infrastruktúra esetleges kockázat, ha a végzett módosítások.

![PaaS-webalkalmazás FFIEC threat modell](images/ffiec-paaswa-threat-model.png "FFIEC fenyegetések modellezése a PaaS-webalkalmazás")

## <a name="compliance-documentation"></a>Megfelelőségi dokumentációja

A [Azure biztonsági és megfelelőségi terv – FFIEC vevő felelőssége mátrix](https://aka.ms/ffiec-crm) FFIEC által igényelt összes biztonsági célkitűzések sorolja fel. Ez a mátrix részletezi a Microsoft, az ügyfél felelőssége egyes célok megvalósítását-e, vagy a kettő között megosztott.

A [Azure biztonsági és megfelelőségi terv – FFIEC PaaS webes kérelem végrehajtása mátrix](https://aka.ms/ffiec-paaswa-cim) melyik FFIEC a követelmények a PaaS webalkalmazás-architektúra, amelyekre információkat nyújt, beleértve a részletes Hogyan megvalósítása megfelel az összes érintett cél leírása.

## <a name="deploy-this-solution"></a>A megoldás üzembe helyezése
Az Azure biztonsági és megfelelőségi tervezet Automation JSON konfigurációs fájloknak és a PowerShell-szkriptek üzembe helyezéséhez az Azure-erőforrások Azure Resource Manager API szolgáltatás által kezelt áll. Részletes üzembe helyezési utasítások [Itt](https://aka.ms/ffiec-paaswa-repo).

#### <a name="quickstart"></a>Első lépések
1. Klónozás vagy letöltés [ez](https://aka.ms/ffiec-paaswa-repo) a helyi munkaállomáson GitHub-adattárban.

2. Tekintse át a 0 – telepítő-AdministrativeAccountAndPermission.md, és futtassa a megadott parancsokat.

3. A Contoso mintaadatok és a egy kezdeti éles környezetben próbaüzem tesztelési megoldás üzembe helyezése.
    - 1a – ContosoWebStoreDemoAzureResources.ps1
        - Ez a szkript üzembe helyezi az Azure-erőforrások a Contoso mintaadatokkal webáruházat bemutató célokra.
    - 1 – DeployAndConfigureAzureResources.ps1
        - Ez a parancsfájl telepíti az Azure-erőforrások felhasználói tulajdonú webalkalmazás éles környezetben támogatásához szükséges. Ebben a környezetben kell további testre az ügyfél szervezeti követelmények alapján.

## <a name="guidance-and-recommendations"></a>Útmutatás és javaslatok

### <a name="vpn-and-expressroute"></a>VPN és ExpressRoute

Biztonságos VPN-alagúton vagy [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) biztonságos kapcsolat a PaaS webes alkalmazás referenciaarchitektúra részeként üzembe helyezett erőforrások kell konfigurálni. Megfelelően állítja be a VPN-t vagy ExpressRoute, az ügyfelek hozzáadhatja egy védelmi réteget biztosít adatokat átvitel közben.

Az Azure-ral biztonságos VPN-alagút alkalmazásával egy helyszíni hálózat és a egy Azure virtuális hálózat közötti virtuális magánhálózati kapcsolat hozható létre. Ezt a kapcsolatot az interneten keresztül történik, és az ügyfelek számára, hogy biztonságosan lehetővé teszi, hogy &quot;alagút&quot; egy titkosított kapcsolatot, az ügyfél között lévő információkat&#39;s hálózat és az Azure. Site-to-site VPN egy olyan biztonságos, érett technológia, évtizedes a vállalatok által központilag telepített. A [IPsec-alagút módú](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) ezt a beállítást, mint egy titkosítási mechanizmus használatban van.

Forgalom a VPN-alagút haladnak át a helyek közötti VPN-nel az interneten, mert a Microsoft kínál egy másik, még biztonságosabb kapcsolat lehetőséget. Az Azure ExpressRoute dedikált WAN Azure-ban és a egy helyszíni helyhez vagy egy Exchange-szolgáltató közötti kapcsolat. Az ExpressRoute-kapcsolatok az interneten keresztül halad, mivel a kapcsolatok az interneten keresztül ajánlja fel a további megbízhatóság megbízhatóbbak, gyorsabbak, kisebb a késésük, és biztonságosabbak a szokásos internetkapcsolatoknál. Továbbá mivel ez az ügyfél közvetlen kapcsolatot&#39;s távközlési szolgáltató, az adatok nem az interneten keresztül továbbítani, és ezért nem érhető el azt.

Ajánlott eljárások végrehajtásához egy biztonságos hibrid hálózatot, amely kiterjeszti a helyszíni hálózatot az Azure-bA [elérhető](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Jogi nyilatkozat

- Ez a dokumentum csak tájékoztatási célokat szolgál. A MICROSOFT NEM VÁLLAL SEMMILYEN KIFEJEZETT, KIFEJEZETT, VÉLELMEZETT VAGY KÖTELEZŐ GARANCIÁT A DOKUMENTUMBAN SZEREPLŐ INFORMÁCIÓRA VONATKOZÓAN. Ez a dokumentum biztosított &quot;,-van.&quot; Információk és vélemények ebben a dokumentumban URL-CÍMEK és más internetes webhelyekre utaló hivatkozások értesítés nélkül változhatnak. Ez a dokumentum olvasásakor ügyfelek felelősségére használja.
- Ez a dokumentum nem biztosít semmilyen jogot semmilyen Microsoft-termék vagy a megoldások a szellemi tulajdonhoz ügyfelek.
- Ügyfelek másolhatja és használhatja ezt a dokumentumot belső, hivatkozási célokból.
- Bizonyos ajánlások a dokumentum vonhat megnövekedett adat-, hálózati vagy számítási erőforrás-használat az Azure-ban, és előfordulhat, hogy növelje az ügyfél&#39;s Azure licenc- vagy előfizetés költségek.
- Ez az architektúra célja, hogy az ügyfelek számára az adott követelményekhez beállítása alapjaként szolgálja ki, és nem használható-éles környezetben van.
- Ez a dokumentum referenciaként fejlesztik, és nem használható minden olyan eszközt, amely szerint egy ügyfél megfelel a megadott megfelelőségi követelmények és előírások meghatározásához. Ügyfelek jóváhagyott megvalósítás az ügyfeleknél a szervezet jogi támogatást kell kérnie.
