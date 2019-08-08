---
title: Azure Security and Compliance Blueprint-Pásti Web Application for NIST SP 800-171
description: Azure Security and Compliance Blueprint-Pásti Web Application NIST SP 800-171
services: security
author: jomolesk
ms.assetid: eea21a0a-5930-43e8-937f-5419c20744c9
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: 101d7b621287972571fb5d3ba9ea02ace2ef1421
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780698"
---
# <a name="azure-security-and-compliance-blueprint---paas-web-application-for-nist-special-publication-800-171"></a>Azure Security and Compliance Blueprint-Pásti Web Application for NIST Special kiadvány 800-171

## <a name="overview"></a>Áttekintés
A 800-171-es [speciális kiadvány](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) a nem szövetségi információs rendszerekben és szervezeteknél található ellenőrzött, nem minősített információk (CUI-) védelmére vonatkozó irányelveket biztosít. A NIST SP 800-171 14 olyan biztonsági követelményt hoz létre, amely a CUI titkosságának védelmét védi.

Ez a Azure Security and Compliance Blueprint útmutatást nyújt az ügyfelek számára, hogy az Azure-ban üzembe helyezett platformot Service-(Péter-) webalkalmazásként implementálják, amely a NIST SP 800-171 vezérlők egy részhalmazát valósítja meg. Ez a megoldás azt mutatja be, hogy az ügyfelek milyen módon tudják kielégíteni az egyes biztonsági és megfelelőségi követelményeket. Emellett alapja az ügyfelek számára, hogy saját webalkalmazást hozzanak létre és konfiguráljanak az Azure-ban.

Ez a hivatkozási architektúra, a társított megvalósítási útmutató és a veszélyforrás-modell arra szolgál, hogy az ügyfelek számára a konkrét követelményekhez alkalmazkodva alapot biztosítson. Nem használhatók éles környezetben. Az architektúra módosítás nélküli üzembe helyezése nem elegendő ahhoz, hogy teljesen megfeleljen a NIST SP 800-171 követelményeinek. Az ügyfelek felelősek az architektúrával létrehozott megoldások megfelelő biztonsági és megfelelőségi felmérésének elvégzéséért. A követelmények az egyes ügyfelek implementációjának sajátosságai alapján változhatnak.

## <a name="architecture-diagram-and-components"></a>Architektúra ábrája és összetevői

Ez a Azure Security and Compliance Blueprint egy Azure SQL Database háttérrel rendelkező, a Pásti webalkalmazás hivatkozási architektúráját biztosítja. A webalkalmazás egy elkülönített App Service-környezetben üzemel, amely egy Azure-adatközpont privát, dedikált környezete. A környezet terhelése az Azure által felügyelt virtuális gépek (VM-EK) között elosztja a webalkalmazás forgalmát. Ez az architektúra hálózati biztonsági csoportokat (NSG), Azure Application Gateway-t, Azure DNS és Azure Load Balancer is tartalmaz.

A továbbfejlesztett elemzési és jelentéskészítési szolgáltatásokhoz az Azure SQL Database-adatbázisok oszlopcentrikus indexekkel konfigurálhatók. Az Azure SQL Database-adatbázisok vertikálisan fel-és leállíthatók, illetve teljes mértékben kikapcsolhatók az ügyfelek általi használatra válaszul. Az összes SQL-forgalom SSL-titkosítással rendelkezik az önaláírt tanúsítványok belefoglalásával. Az ajánlott eljárás az, hogy az Azure megbízható hitelesítésszolgáltató használatát javasolja a fokozott biztonság érdekében.

A megoldás Azure Storage-fiókokat használ, amelyekkel az ügyfelek a Storage Service Encryption használatával kezelhetik az inaktív adatok titkosságát. Az Azure a rugalmasság érdekében három másolatot tárol az ügyfél kiválasztott adatközpontjában. A Geo-redundáns tárolás biztosítja, hogy a rendszer replikálja az adatmennyiséget egy több száz kilométerre lévő másodlagos adatközpontba, és az adatközpontban három példányban tárolja őket. Ez a megoldás megakadályozza, hogy az ügyfél elsődleges adatközpontjában ne legyen adatvesztés.

A fokozott biztonság érdekében az ebben a megoldásban lévő összes erőforrást erőforráscsoportként kezeli Azure Resource Manageron keresztül. A Azure Active Directory (Azure AD) szerepköralapú hozzáférés-vezérlés (RBAC) használatával szabályozható a telepített erőforrásokhoz való hozzáférés. Ezek az erőforrások a Azure Key Vaultban lévő ügyfelek kulcsait tartalmazzák. A rendszer állapotának figyelése Azure Monitoron keresztül történik. Az ügyfelek a figyelési szolgáltatást a naplók rögzítésére konfigurálja. A rendszer állapota egyetlen irányítópulton jelenik meg, amely könnyen használható.

SQL Databaset általában a SQL Server Management Studio kezeli. Egy olyan helyi gépről fut, amely az SQL Database biztonságos VPN-vagy Azure ExpressRoute-kapcsolaton keresztüli elérésére van konfigurálva.

A Application Insights valós idejű alkalmazások teljesítmény-felügyeletet és elemzéseket biztosít a Azure Monitor naplókon keresztül, *a Microsoft javasolja, hogy VPN-vagy ExpressRoute-kapcsolaton keresztül konfigurálja a felügyeleti és az Adatimportálási szolgáltatásokat a hivatkozási architektúrába alhálózat.*

![Pásti Web Application for NIST SP 800-171 hivatkozási architektúra diagramja](images/nist171-paaswa-architecture.png "Pásti Web Application for NIST SP 800-171 hivatkozási architektúra diagramja")

Ez a megoldás az alábbi Azure-szolgáltatásokat használja. További információ: [telepítési architektúra](#deployment-architecture) szakasz.

- Azure Virtual Machines
    - (1) felügyelet/megerősített (Windows Server 2016 Datacenter)
- Azure Virtual Network
    - (1)/16 hálózat
    - (4)/24 hálózat
    - (4) hálózati biztonsági csoportok
- Azure Application Gateway
    - Webalkalmazási tűzfal
        - Tűzfal mód: megelőzés
        - Szabály beállítása: OWASP
        - Figyelő portja: 443
- Application Insights
- Azure Active Directory
- App Service Environment v2
- Azure Automation
- Azure DNS
- Azure Key Vault
- Azure Load Balancer
- Azure Monitor (naplók)
- Azure Resource Manager
- Azure Security Center
- Azure SQL Database
- Azure Storage
- Azure Automation
- Azure-webalkalmazások

## <a name="deployment-architecture"></a>Üzembe helyezési architektúra
A következő szakasz az üzembe helyezési és megvalósítási elemeket részletezi.

**Azure Resource Manager**: A [Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) segítségével a felhasználók a megoldásban lévő erőforrásokkal dolgozhatnak csoportként. Az ügyfelek egyetlen, koordinált műveletben telepíthetik, frissíthetik vagy törölhetik a megoldás összes erőforrását. Az ügyfelek egy sablont használnak az üzembe helyezéshez. A sablon különböző környezetekben, például tesztelési, átmeneti és éles környezetben is működhet. A Resource Manager biztonsági, naplózási és címkézési funkciókat biztosít az ügyfelek számára az üzembe helyezést követően az erőforrások kezeléséhez.

**Megerősített gazdagép**: A megerősített gazdagép az a belépési pont, amelyet a felhasználók a környezetbe helyezett erőforrások elérésére használhatnak. A megerősített gazdagép biztonságos kapcsolódást biztosít a központilag telepített erőforrásokhoz azáltal, hogy csak a nyilvános IP-címekről érkező távoli forgalmat biztonságos listaként engedélyezi. A távoli asztal forgalmának engedélyezéséhez meg kell határozni a forgalom forrását a NSG.

Ez a megoldás egy virtuális gépet hoz létre tartományhoz csatlakozó megerősített gazdagépként a következő konfigurációkkal:
-   [Antimalware-bővítmény](https://docs.microsoft.com/azure/security/fundamentals/antimalware).
-   [Azure Diagnostics bővítmény](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template).
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) Key Vault használatával.
-   [Automatikus leállítási szabályzat](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) , amely csökkenti a virtuálisgép-erőforrások felhasználását, ha nincs használatban.
-   A [Windows Defender hitelesítő adatainak](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) védelme engedélyezve van, így a hitelesítő adatok és egyéb Titkok a futó operációs rendszertől elkülönített védett környezetekben futnak.

**Web Apps**: A [Web Apps](https://docs.microsoft.com/azure/app-service/) Azure app Service szolgáltatás. Az ügyfelek használhatják a webalkalmazások kiépítését és üzemeltetését az infrastruktúra kezelése nélkül az általuk választott programozási nyelven. Automatikus méretezést és magas rendelkezésre állást biztosít. Támogatja a Windows és a Linux rendszert, és lehetővé teszi az automatikus üzembe helyezést a GitHubról, az Azure DevOps vagy bármely git-tárházból.

**App Service Environment**: A [App Service Environment](https://docs.microsoft.com/azure/app-service/environment/intro) app Service szolgáltatás. Teljesen elkülönített és dedikált környezetet biztosít App Service alkalmazások biztonságos futtatásához nagy léptékben.

A App Service környezet csak egyetlen alkalmazás futtatására van elkülönítve. A rendszer mindig virtuális hálózatba helyezi üzembe. Az elkülönítési funkció miatt a hivatkozási architektúra befejezte a bérlők elkülönítését, és a rendszer eltávolítja az Azure több-bérlős környezetéről. Az ügyfelek részletesen szabályozzák a bejövő és a kimenő alkalmazások hálózati forgalmát. Az alkalmazások nagy sebességű biztonságos kapcsolatokat hozhatnak létre a virtuális hálózatokon a helyszíni vállalati erőforrásokhoz. Az ügyfelek a betöltési metrikák, a rendelkezésre álló költségvetés vagy egy meghatározott ütemterv alapján "Automatikus méretezéssel" rendelkezhetnek App Service Environment.

A App Service Environment használata ehhez az architektúrához a következő vezérlőket és konfigurációkat biztosítja:

- A gazdagép biztonságos Azure-beli virtuális hálózati és hálózati biztonsági szabályokon belül található.
- Önaláírt belső terheléselosztó tanúsítványa HTTPS-kommunikációhoz. Ajánlott eljárásként a Microsoft egy megbízható hitelesítésszolgáltató használatát javasolja a fokozott biztonság érdekében.
- [Belső terheléselosztási mód](https://docs.microsoft.com/azure/app-service-web/app-service-environment-with-internal-load-balancer) (3. mód).
- A [TLS 1,0](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)letiltása.
- Módosítsa a [TLS](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)-titkosítást.
- Bejövő adatforgalom vezérlése [N/W portokon](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic).
- [Webalkalmazási tűzfal – korlátozza az](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-web-application-firewall)adatvédelmet.
- [Azure SQL Database forgalom](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-network-architecture-overview)engedélyezése.

### <a name="virtual-network"></a>Virtuális hálózat
Az architektúra a 10.200.0.0/16 címtartomány szerinti magánhálózati virtuális hálózatot definiálja.

**Hálózati biztonsági csoportok**: A [NSG](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) olyan hozzáférés-vezérlési listát tartalmaznak, amelyek engedélyezik vagy megtagadják a forgalmat a virtuális hálózaton belül. A NSG használatával biztonságossá teheti a forgalmat egy alhálózaton vagy egy adott virtuálisgép-szinten. A következő NSG léteznek:
- Application Gateway egy NSG
- App Service Environment egy NSG
- SQL Database egy NSG
- Egy NSG a megerősített gazdagéphez

A NSG minden egyes portja és protokollja nyitva van, hogy a megoldás biztonságosan és megfelelően működjön. Emellett a következő konfigurációk is engedélyezve vannak az egyes NSG:
  - A [diagnosztikai naplók és események](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) engedélyezve vannak, és egy Storage-fiókban tárolódnak.
  - A Azure Monitor naplók a [NSG diagnosztikaához](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)kapcsolódnak.

Alhálózatok: Minden alhálózat társítva van a hozzá tartozó NSG.

**Azure DNS**: A tartománynévrendszer (DNS) felelős a webhelyek vagy szolgáltatások nevének az IP-címére való fordításához (vagy feloldásához). A [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) olyan DNS-tartományok üzemeltetési szolgáltatása, amelyek névfeloldást biztosítanak az Azure-infrastruktúra használatával. Az Azure-ban a felhasználók a DNS-rekordokat a többi Azure-szolgáltatással azonos hitelesítő adatok, API-k, eszközök és számlázás használatával kezelhetik. A Azure DNS támogatja a magánhálózati DNS-tartományokat is.

**Azure Load Balancer**: Az ügyfelek az alkalmazások méretezéséhez és magas rendelkezésre állású szolgáltatások létrehozásához is használhatják a [Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) . Load Balancer támogatja a bejövő és kimenő forgatókönyveket. Alacsony késést és nagy átviteli sebességet biztosít, és akár több millió folyamatra is méretezhető az összes TCP-és UDP-alkalmazás esetében.

### <a name="data-in-transit"></a>Átvitt adatok
Az Azure alapértelmezés szerint titkosítja az Azure-adatközpontok felé irányuló és onnan érkező összes kommunikációt. Az Azure Storage-ba irányuló összes tranzakció a Azure Portal a HTTPS-en keresztül történik.

### <a name="data-at-rest"></a>Inaktív adat

Az architektúra titkosítva, adatbázis-naplózással és egyéb mértékekkel védi a nyugalmi állapotban lévő adatok védelmét.

**Azure Storage**: A titkosított adatokra vonatkozó követelmények teljesítése érdekében az [](https://azure.microsoft.com/services/storage/) összes tárterület [Storage Service Encryptiont](https://docs.microsoft.com/azure/storage/storage-service-encryption)használ. Ez a szolgáltatás segít megvédeni és megőrizni az adatvédelmet a NIST SP 800-171 által meghatározott szervezeti biztonsági kötelezettségvállalások és megfelelőségi követelmények támogatásával.

**Azure Disk Encryption**: A [lemezes titkosítás](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) a Windows BitLocker szolgáltatásával biztosítja az adatlemezek mennyiségi titkosítását. A megoldás integrálva van Key Vaultekkel a lemezes titkosítási kulcsok szabályozása és kezelése érdekében.

**Azure SQL Database**: A SQL Database példány a következő adatbázis-biztonsági mértékeket használja:
-   [Active Directory a hitelesítés és az engedélyezés](https://docs.microsoft.com/azure/sql-database/sql-database-AAD-authentication) lehetővé teszi az adatbázis-felhasználók és más Microsoft-szolgáltatások Identitáskezelés kezelését egy központi helyen.
-   Az [SQL Database naplózása](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) nyomon követi az adatbázis eseményeit, és egy Azure Storage-fiókban lévő naplóba írja azokat.
-   A SQL Database [transzparens adattitkosítás](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)használatára van konfigurálva. A szolgáltatás valós idejű titkosítást és visszafejtést végez az adatbázis, a társított biztonsági másolatok és a tranzakciós naplófájlok között, hogy megvédje az adatokat a nyugalmi állapotban. Az transzparens adattitkosítás biztosítja, hogy a tárolt adataik nem vonatkoznak a jogosulatlan hozzáférésre.
-   A [Tűzfalszabályok](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) megakadályozzák az adatbázis-kiszolgálók hozzáférését a megfelelő engedélyek megadása előtt. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján.
-   Az [SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) -veszélyforrások észlelése lehetővé teszi az észlelést és a válaszadást a lehetséges fenyegetésekkel szemben. Biztonsági riasztásokat biztosít a gyanús adatbázis-tevékenységekről, a lehetséges sebezhetőségekről, az SQL-injektálási támadásokról, valamint a rendellenes adatbázis-hozzáférési mintákról.
-   A [titkosított oszlopok](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) biztosítják, hogy a bizalmas adatok soha nem egyszerű szövegként jelenjenek meg az adatbázis-rendszeren belül. Az adattitkosítás engedélyezése után csak a kulcsokhoz hozzáféréssel rendelkező ügyfélalkalmazások vagy kiszolgálóalkalmazások férhetnek hozzá az egyszerű szöveges adatértékekhez.
- A [dinamikus adatmaszkolás](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) korlátozza a bizalmas adatokra való adatvédelmet azáltal, hogy az adatokat nem Kiemelt felhasználók vagy alkalmazások számára fedi le. A szolgáltatás automatikusan képes felderíteni a potenciálisan bizalmas adatokat, és javaslatot tesz a megfelelő maszkok alkalmazására. A dinamikus adatmaszkolás segít csökkenteni a hozzáférést, hogy a bizalmas adatok jogosulatlan hozzáférés nélkül ne lépjenek ki az adatbázisból. *Az ügyfelek felelősek a beállítások módosításához az adatbázis-sémájuk betartásához.*

### <a name="identity-management"></a>Identitáskezelés
Az alábbi technológiák az Azure-környezetben tárolt adathozzáférések kezelésére szolgáló képességeket biztosítanak:
-   Az [Azure ad](https://azure.microsoft.com/services/active-directory/) a Microsoft több-bérlős felhőalapú címtár-és Identitáskezelés-kezelő szolgáltatása. A megoldás minden felhasználója az Azure AD-ben jön létre, és az SQL Database-hez hozzáférő felhasználókat is tartalmazza.
-   Az alkalmazáshoz való hitelesítés az Azure AD használatával történik. További információ: [alkalmazások integrálása az Azure ad-vel](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Az adatbázis oszlopának titkosítása az Azure AD-t is használja az alkalmazás SQL Database való hitelesítéséhez. További információ: a [bizalmas adatok védelme SQL Databaseban](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   Az [Azure RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) segítségével a rendszergazdák részletes hozzáférési engedélyeket adhatnak meg. Ezzel csak azt a hozzáférést biztosíthatják, ameddig a felhasználóknak el kell végezniük a munkájukat. Ahelyett, hogy minden felhasználó számára korlátlan hozzáférést adna az Azure-erőforrásokhoz, a rendszergazdák csak bizonyos műveleteket végezhetnek el az erőforrásokhoz és adatokhoz való hozzáféréshez. Az előfizetés-hozzáférés az előfizetés rendszergazdájára korlátozódik.
- A [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) az ügyfelek használhatják a bizonyos információkhoz hozzáférő felhasználók számának minimalizálására. A rendszergazdák Azure AD Privileged Identity Management használhatják az emelt szintű identitások felderítését, korlátozását és figyelését, valamint az erőforrásokhoz való hozzáférésüket. Ez a funkció az igény szerinti, igény szerinti rendszergazdai hozzáférés biztosítására is használható, ha szükséges.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) észleli a szervezet identitásait érintő lehetséges biztonsági réseket. Automatikus válaszokat konfigurál a szervezet identitásával kapcsolatos gyanús műveletek észlelésére. Emellett a gyanús incidenseket is megvizsgálja, hogy megfelelő lépéseket tegyenek a megoldásához.

### <a name="security"></a>Biztonság
**Titkok kezelése**: A megoldás a kulcsok és titkok kezeléséhez [Key Vault](https://azure.microsoft.com/services/key-vault/) használ. Key Vault segít megőrizni a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsokat és titkokat. Az alábbi Key Vault-képességek segítenek az ügyfeleknek az adatvédelemben:
- A speciális hozzáférési szabályzatok a szükséges módon vannak konfigurálva.
- Key Vault hozzáférési házirendek minimálisan szükséges engedélyekkel vannak definiálva a kulcsokhoz és a titkokhoz.
- Key Vault összes kulcsának és titkának lejárati dátuma van.
- A Key Vault összes kulcsát speciális hardveres biztonsági modulok védik. A kulcs típusa egy hardveres biztonsági modul által védett 2048 bites RSA-kulcs.
- Minden felhasználó és identitás minimálisan szükséges engedélyeket kap a RBAC használatával.
- Key Vault diagnosztikai naplói legalább 365 napos megőrzési időtartammal engedélyezettek.
- A kulcsok számára engedélyezett titkosítási műveletek csak a szükségesek.

**Azure Security Center**: A [Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)segítségével az ügyfelek központilag alkalmazhatják és kezelhetik a munkaterhelések biztonsági szabályzatait, korlátozhatja a fenyegetéseket, és észlelheti és reagálhat a támadásokra. Security Center az Azure-szolgáltatások meglévő konfigurációit is elérheti, hogy konfigurációs és szolgáltatási javaslatokat nyújtson a biztonsági helyzetek és az adatvédelem javításához.

Security Center különböző észlelési képességekkel figyelmezteti az ügyfeleket a környezetét célba vevő lehetséges támadásokra. Ezek a riasztások értékes információkat tartalmaznak arról, hogy mi váltotta ki a riasztást, valamint a támadás forrásáról és az általa célba vett erőforrásokról. A Security Center [előre meghatározott biztonsági riasztásokat](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) tartalmaz, amelyek akkor aktiválódnak, ha fenyegetés vagy gyanús tevékenység zajlik. Az ügyfelek [Egyéni riasztási szabályok](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) segítségével meghatározhatnak új biztonsági riasztásokat a környezetből már összegyűjtött adatok alapján.

A Security Center rangsorolt biztonsági riasztásokat és incidenseket biztosít. Security Center megkönnyíti az ügyfelek számára a potenciális biztonsági problémák felderítését és megoldását. Minden észlelt fenyegetéshez létrejön egy [fenyegetési intelligencia jelentés](https://docs.microsoft.com/azure/security-center/security-center-threat-report) . Az incidensek válaszait használó csapatok a jelentéseket a fenyegetések kivizsgálására és szervizelésére használhatják.

**Azure Application Gateway**: Az architektúra csökkenti a biztonsági rések kockázatát, ha olyan Application Gateway-t használ, amely konfigurálva van egy webalkalmazási tűzfallal, és a OWASP szabálykészlet engedélyezve van. A további funkciók a következők:

- [End-to-end-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- Engedélyezze az [SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)-kiszervezést.
- Tiltsa le [a TLS 1.0-s és 1.1-es verzióit](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- [Webalkalmazási tűzfal](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (megelőzési mód).
- [Megelőzési mód](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) a OWASP 3,0 szabálykészlet beállításával.
- [Diagnosztikai naplózás](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)engedélyezése.
- [Egyéni állapot](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)-mintavételek.
- A [Security Center](https://azure.microsoft.com/services/security-center) és [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) további védelmet és értesítéseket biztosítanak. A Security Center a hírnév rendszerét is biztosítja.

### <a name="logging-and-auditing"></a>Naplózás és naplózás

Az Azure-szolgáltatások széles körben naplózzák a rendszer és a felhasználó tevékenységét, valamint a rendszer állapotát:
- **Tevékenységek naplói**: A [tevékenységek naplói](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) betekintést nyújtanak az előfizetésben lévő erőforrásokon végrehajtott műveletekre. A Tevékenységnaplók segítenek meghatározni a művelet kezdeményezőjét, az előfordulás időpontját és az állapotot.
- **Diagnosztikai naplók**: A [diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) az összes erőforrás által kibocsátott összes naplót tartalmazzák. Ezek a naplók a Windows-események rendszernaplóit, a tárolási naplókat, a Key Vault a naplókat, valamint Application Gateway hozzáférési és tűzfal-naplókat tartalmaznak. Az összes diagnosztikai napló egy központi és titkosított Azure Storage-fiókba írja az archiválást. A felhasználók a megőrzési időtartamot akár 730 napig is konfigurálhatják, hogy megfeleljenek a rájuk vonatkozó követelményeknek.

**Naplók Azure monitor**: A naplókat a rendszer a feldolgozás, tárolás és irányítópult-jelentéskészítés [Azure monitor naplófájljaiban](https://azure.microsoft.com/services/log-analytics/) összesíti. Az adatgyűjtés után a rendszer külön táblákba rendezi a Log Analytics munkaterületeken belüli adattípusokat. Így az összes adatforrás együtt is elemezhető, az eredeti forrásától függetlenül. A Security Center Azure Monitor-naplókkal integrálódik. Az ügyfelek használhatnak Kusto-lekérdezéseket a biztonsági események adatainak eléréséhez és más szolgáltatásokból származó adatokkal való összekapcsolásához.

Az architektúra részeként az alábbi Azure- [figyelési megoldások](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) szerepelnek:
-   [Active Directory értékelés](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): A Active Directory Health-ellenőrzési megoldás rendszeres időközönként kivizsgálja a kiszolgálói környezetek kockázatait és állapotát. A szolgáltatás a központilag telepített kiszolgálói infrastruktúrára vonatkozó javaslatok rangsorolt listáját tartalmazza.
- [SQL-értékelés](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): Az SQL Health-ellenőrzési megoldás rendszeres időközönként kivizsgálja a kiszolgálói környezetek kockázatait és állapotát. Az ügyfelek számára az üzembe helyezett kiszolgálói infrastruktúrára jellemző ajánlások rangsorolt listáját kínálja.
- [Agent Health](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): A Agent Health-megoldás azt jelenti, hogy hány ügynök van üzembe helyezve és a földrajzi eloszlása. Azt is jelenti, hogy hány ügynök nem válaszol, és az operatív adatküldő ügynökök száma.
-   [Activity log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Az Activity Log Analytics-megoldás segítséget nyújt az Azure-tevékenységek naplóinak elemzéséhez az ügyfelek összes Azure-előfizetése között.

**Azure Automation**: Az [Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) tárolja, futtatja és felügyeli a runbookok. Ebben a megoldásban a runbookok segítséget nyújt a naplók SQL Databaseból való gyűjtésében. Az Automation [change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) megoldás segítségével könnyedén azonosíthatja a környezet változásait.

**Azure monitor**: A [figyelő](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) segít a felhasználóknak a teljesítmény nyomon követésében, a biztonság fenntartásában és a trendek felismerésében. A szervezetek felhasználhatják a naplózásra, a riasztások létrehozására és az adatok archiválására. Emellett az Azure-erőforrásokban is nyomon követhetik az API-hívásokat.

**Application Insights**: A [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) egy bővíthető Application Performance Management szolgáltatás a webes fejlesztők számára több platformon. Application Insights észleli a teljesítménnyel kapcsolatos rendellenességeket. Az ügyfelek használhatják az élő webalkalmazás figyelését. A Application Insights hatékony elemzési eszközöket tartalmaz, amelyek segítségével az ügyfelek diagnosztizálják a problémákat, és megismerhetik, hogy a felhasználók hogyan használják az alkalmazást. &#39;A megoldás célja, hogy az ügyfelek folyamatosan javítsák a teljesítményt és a használhatóságot.

## <a name="threat-model"></a>Veszélyforrások modellje

A viszonyítási architektúra adatáramlási diagramja [letölthető](https://aka.ms/nist171-paaswa-tm) , vagy itt található. Ez a modell segítheti az ügyfeleket abban, hogy megértsék a rendszerinfrastruktúra lehetséges kockázatait, amikor módosításokat végeznek.

![Pásti-webalkalmazás a NIST SP 800-171 Threat modelhez](images/nist171-paaswa-threat-model.png "Pásti-webalkalmazás a NIST SP 800-171 Threat modelhez")

## <a name="compliance-documentation"></a>Megfelelőségi dokumentáció
A [Azure Security and Compliance Blueprint-NIST sp 800-171 Customer felelősségi mátrix](https://aka.ms/nist171-crm) FELSOROLJA a NIST SP 800-171 által igényelt összes biztonsági ellenőrzést. Ez a mátrix részletesen ismerteti, hogy az egyes vezérlőelemek megvalósítása a Microsoft, az ügyfél vagy a kettő közötti megosztás feladata-e.

A [Azure Security and Compliance Blueprint-NIST sp 800-171 Pásti webalkalmazás-vezérlő megvalósítási mátrixa](https://aka.ms/nist171-paaswa-cim) információt nyújt arról, hogy mely NIST SP 800-171-vezérlőkkel foglalkozik a Pásti webalkalmazás-architektúra. Részletes leírást tartalmaz arról, hogy a megvalósítás hogyan teljesíti az egyes kezelt vezérlők követelményeit.

## <a name="guidance-and-recommendations"></a>Útmutatás és javaslatok
### <a name="vpn-and-expressroute"></a>VPN-és ExpressRoute
Biztonságos VPN-alagutat vagy [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) úgy kell konfigurálni, hogy biztonságosan létesítsen kapcsolatot a jelen Pásti-webalkalmazás-hivatkozási architektúra részeként üzembe helyezett erőforrásokkal. A VPN-vagy ExpressRoute megfelelő beállításával az ügyfelek hozzáadhatnak egy védelmi réteget az átvitelhez.

A biztonságos VPN-alagút az Azure-nal való megvalósításával létrehozhat egy virtuális magánhálózati kapcsolatot egy helyszíni hálózat és egy Azure-beli virtuális hálózat között. Ez a kapcsolat az interneten keresztül történik, és lehetővé teszi az ügyfelek számára, hogy az ügyfél hálózata és az Azure közötti titkosított kapcsolaton belül biztonságosan "alagút" információkat használjanak. A helyek közötti VPN egy olyan biztonságos, érett technológia, amelyet évtizedek óta minden méretben üzembe helyezett a vállalatok. Ebben a beállításban az [IPsec-alagút mód](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) titkosítási mechanizmusként van használatban.

Mivel a VPN-alagúton belüli forgalom helyek közötti VPN-kapcsolaton keresztül halad át az interneten, a Microsoft még egy biztonságosabb kapcsolódási lehetőséget kínál. A ExpressRoute egy dedikált WAN-kapcsolat az Azure és egy helyszíni hely vagy egy Exchange-szolgáltató között. A ExpressRoute-kapcsolatok közvetlenül az ügyfél távközlési szolgáltatójának csatlakoznak. Ennek eredményeképpen az adat nem utazik az interneten keresztül, és nem teszi elérhetővé. Ezek a kapcsolatok nagyobb megbízhatóságot, gyorsabb sebességet, kisebb késést és nagyobb biztonságot nyújtanak, mint a szokásos kapcsolatok.

Ajánlott eljárások egy biztonságos hibrid hálózat megvalósításához, amely kiterjeszti a helyszíni hálózatot az Azure- [](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)ra.

## <a name="disclaimer"></a>Jogi nyilatkozat

 - Ez a dokumentum csak tájékoztató jellegű. A MICROSOFT NEM VÁLLAL SEMMILYEN KIFEJEZETT, VÉLELMEZETT VAGY TÖRVÉNYES GARANCIÁT A JELEN DOKUMENTUMBAN FOGLALT INFORMÁCIÓK ALAPJÁN. Ez a dokumentum "aktuálisként" van megadva. Az ebben a dokumentumban kifejezett információk és nézetek, beleértve az URL-címeket és az internetes webhelyek más hivatkozásait, értesítés nélkül változhatnak. A dokumentumot olvasó ügyfeleink a használatuk kockázatát viselik.
 - A jelen dokumentum nem biztosít semmilyen jogot semmilyen Microsoft-termékben vagy-megoldásban található szellemi tulajdonhoz.
 - Az ügyfelek belső referenciák szerint másolhatják és használhatják ezt a dokumentumot.
 - A jelen dokumentumban szereplő javaslatok megnövelték az adatok, a hálózat vagy a számítási erőforrások használatát az Azure-ban, és növelhetik az ügyfelek Azure-licencét vagy előfizetési költségeit.
 - Ez az architektúra arra szolgál, hogy az ügyfelek számára a konkrét követelményekhez igazodva és nem használható éles környezetben.
 - Ez a dokumentum hivatkozásként van kifejlesztve, és nem használható az összes olyan eszköz meghatározására, amelyekkel az ügyfél megfelel bizonyos megfelelőségi követelményeknek és előírásoknak. Az ügyfeleknek jóvá kell hagyniuk a szervezeten belüli jogi támogatást a jóváhagyott ügyfél-implementációkban.
