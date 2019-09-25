---
title: Azure Security and Compliance Blueprint – Péter webalkalmazása védett Ausztráliában
description: Azure Security and Compliance Blueprint – Péter webalkalmazása védett Ausztráliában
services: security
author: meladie
ms.assetid: 708aa129-b226-4e02-85c6-1f86e54564e4
ms.service: security
ms.topic: article
ms.date: 08/23/2018
ms.author: meladie
ms.openlocfilehash: 6d1b84894439010e5297ce010b2ece6dea8f3e56
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71257604"
---
# <a name="azure-security-and-compliance-blueprint---paas-web-application-for-australia-protected"></a>Azure Security and Compliance Blueprint – Péter webalkalmazása védett Ausztráliában

## <a name="overview"></a>Áttekintés

Ez a Azure Security and Compliance Blueprint útmutatást nyújt a platform szolgáltatásként való üzembe helyezéséhez, amely alkalmas az AU-védelemmel ellátott kormányzati adatok gyűjtésére, tárolására és lekérésére, amelyek megfelelnek a következő céloknak: Az ausztrál kormány információs biztonsági kézikönyve (ISM), amelyet az Australian Signals Igazgatósága (ASD) állít elő. Ez a terv egy közös hivatkozási architektúrát mutat be, és segít bemutatni a bizalmas kormányzati adatok megfelelő kezelését biztonságos, megfelelő, többrétegű környezetben.

Ez a hivatkozási architektúra, a megvalósítási útmutató és a veszélyforrás-modell egy alapot biztosít az ügyfeleknek a saját tervezési és rendszer-akkreditációs folyamataik elvégzéséhez, így az ügyfelek ASD-kompatibilis módon telepíthetik az Azure-beli számítási feladatokat. Az ügyfelek dönthetnek úgy, hogy egy Azure-VPN Gateway vagy ExpressRoute-t implementálnak az összevont szolgáltatások használatára és a helyszíni erőforrások Azure-erőforrásokkal való integrálására. Az ügyfeleknek figyelembe kell venniük a helyszíni erőforrások használatának biztonsági következményeit. A követelmények teljesítéséhez további konfigurációra van szükség, mivel ezek az ügyfelek különböző implementációinak sajátosságai alapján változhatnak.

Az ASD-Compliance megvalósítása megköveteli, hogy az Information Security által regisztrált bíráló naplózza a rendszerét. Az ügyfelek felelősek az architektúrával létrehozott megoldások megfelelő biztonsági és megfelelőségi felmérésének elvégzéséért, mivel a követelmények az egyes ügyfelek implementációjának sajátosságai alapján változhatnak.

## <a name="architecture-diagram-and-components"></a>Architektúra ábrája és összetevői
Ez a megoldás egy Azure SQL Database háttérrel rendelkező, a Pásti webalkalmazásra mutató hivatkozási architektúrát biztosít. A webalkalmazást egy elkülönített Azure App Service Environment üzemelteti, amely egy Azure-adatközpontot privát, dedikált környezete. A környezet terheléselosztása az Azure által felügyelt virtuális gépeken lévő webalkalmazások forgalmát terheli. Az összes webalkalmazás-kapcsolathoz a 1,2-es minimális verziószámú TLS szükséges. Ez az architektúra hálózati biztonsági csoportokat, Application Gateway, Azure DNS és Load Balancer is tartalmaz.

Az architektúra olyan biztonságos hibrid környezetet biztosíthat, amely kiterjeszti a helyszíni hálózatot az Azure-ba, lehetővé téve a webes munkaterhelések biztonságos elérését a szervezet helyi hálózatán vagy az internetről érkező vállalati felhasználók számára. A helyszíni megoldások esetében az ügyfél a biztonság, a műveletek és a megfelelőség valamennyi aspektusa számára egyaránt felelős és felelős.

Az ebben a megoldásban foglalt Azure-erőforrások csatlakozni tudnak egy helyszíni hálózathoz vagy adatközpontot-létesítési létesítményhez (pl. CDC a Canberra-ban) egy IPSec VPN-en keresztül VPN Gateway és a ExpressRoute segítségével. A VPN használatára vonatkozó döntést a továbbított adatok és a hálózati elérési út besorolásával kell elvégezni. A nagy léptékű, kritikus fontosságú számítási feladatokat végző ügyfeleinknek big data követelményekkel rendelkező hibrid hálózati architektúrát kell fontolóra venniük a ExpressRoute használatával az Azure-szolgáltatásokhoz való magánhálózati kapcsolathoz. Az Azure kapcsolati mechanizmusaival kapcsolatos további részletekért tekintse meg az [útmutató és javaslatok](#guidance-and-recommendations) szakaszt.

A Azure Active Directoryekkel való összevonással lehetővé teheti a felhasználók számára a helyszíni hitelesítő adatokkal történő hitelesítést, és a felhőben lévő összes erőforrás elérését helyszíni Active Directory összevonási szolgáltatások (AD FS) infrastruktúra használatával. A Active Directory összevonási szolgáltatások (AD FS) egyszerűsített, biztonságos identitás-összevonást és webes egyszeri bejelentkezési képességeket biztosíthat ehhez a hibrid környezethez. További részletekért tekintse meg az [útmutató és javaslatok](#guidance-and-recommendations) szakaszt Azure Active Directory a telepítőt.

A megoldás Azure Storage-fiókokat használ, amelyekkel az ügyfelek a Storage Service Encryption használatával kezelhetik az inaktív adatok titkosságát. Az Azure a rugalmasság érdekében három példányban tárolja az adatmennyiséget az ügyfél kiválasztott régiójában. Az Azure-régiók rugalmas régiókban vannak üzembe helyezve, és a földrajzi redundáns tárolás biztosítja, hogy az adatküldés a második régióba is kerüljön három másolattal. Ez megakadályozza, hogy az ügyfél elsődleges adathelyénél negatív esemény kerüljön adatvesztés miatt.

A fokozott biztonság érdekében az ebben a megoldásban lévő összes Azure-erőforrás a Azure Resource Manageron keresztül erőforráscsoportként van kezelve. Azure Active Directory szerepköralapú hozzáférés-vezérléssel szabályozható a telepített erőforrásokhoz és kulcsokhoz való hozzáférés a Azure Key Vaultban. A rendszer állapotát Azure Security Center és Azure Monitor figyeli. Az ügyfelek mindkét figyelési szolgáltatást a naplók rögzítéséhez és a rendszerállapot megjelenítéséhez egyetlen, könnyen navigálható irányítópulton. Az Azure Application Gateway a megelőzési módban tűzfalként van konfigurálva, és nem engedélyezi a TLS 2.0-s vagy újabb verzióval nem rendelkező forgalmat. A megoldás az Azure Application Service Environment v2-t használja a webes réteg elkülönítésére egy nem több-bérlős környezetben.

![Pásti-webalkalmazás az au által védett hivatkozási architektúrához](images/au-protected-paaswa-architecture.png?raw=true "Pásti-webalkalmazás az au által védett hivatkozási architektúra diagramhoz")

Ez a megoldás az alábbi Azure-szolgáltatásokat használja. További részletek az [üzembe helyezési architektúra](#deployment-architecture) szakaszban olvashatók.

- Application Gateway
    - Webalkalmazási tűzfal
        - Tűzfal mód: megelőzés
        - Szabály beállítása: OWASP
        - Figyelő portja: 443
- Application Insights
- Azure Active Directory
- Azure Application Service Environment v2
- Azure Automation
- Azure DNS
- Azure Key Vault
- Azure Load Balancer
- Azure Monitor
- Azure Resource Manager
- Azure Security Center
- Azure SQL Database
- Azure Storage
- Azure Monitor-naplók
- Azure Virtual Network
    - (1)/16 hálózat
    - (4)/24 hálózat
    - Network security groups (Hálózati biztonsági csoportok)
- Network security groups (Hálózati biztonsági csoportok)
- Helyreállítási tár
- Azure Web App

Ez a terv olyan Azure-szolgáltatásokat tartalmaz, amelyek nem lettek hitelesítve az ausztrál Cyber Security Center (ASCS) által védett besorolásban való használatra. A Microsoft azt javasolja, hogy az ügyfelek az ezen Azure-szolgáltatásokhoz kapcsolódó közzétett biztonsági és auditálási jelentéseket tekintsék át, és használják a kockázatkezelési keretrendszert annak megállapítására, hogy az Azure-szolgáltatás alkalmas-e a belső akkreditálásra, és hogy a Védett besorolás.

## <a name="deployment-architecture"></a>Üzembe helyezési architektúra
A következő szakasz az üzembe helyezési és megvalósítási elemeket részletezi.

**Azure Resource Manager**: [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) lehetővé teszi az ügyfelek számára, hogy csoportként működjenek együtt a megoldás erőforrásaival. Az ügyfelek egyetlen, koordinált műveletben telepíthetik, frissíthetik vagy törölhetik a megoldás összes erőforrását. Az ügyfelek egy sablont használnak az üzembe helyezéshez, és a sablon különböző környezetekben, például tesztelési, átmeneti és éles környezetben is működhet. A Resource Manager biztonsági, naplózási és címkézési funkciókat biztosít az ügyfelek számára az üzembe helyezést követően az erőforrások kezeléséhez.

**Megerősített gazdagép**: A megerősített gazdagép az egyetlen belépési pont, amely lehetővé teszi, hogy a felhasználók hozzáférhessenek az üzembe helyezett erőforrásokhoz ebben a környezetben. A megerősített gazdagép biztonságos kapcsolódást biztosít a központilag telepített erőforrásokhoz azáltal, hogy csak a nyilvános IP-címekről érkező távoli forgalmat engedélyezi biztonságos listán. A távoli asztal (RDP) forgalmának engedélyezéséhez a hálózati biztonsági csoportban meg kell határozni a forgalom forrását.

Ez a megoldás egy virtuális gépet hoz létre tartományhoz csatlakozó megerősített gazdagépként a következő konfigurációkkal:
-   [Antimalware-bővítmény](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
-   [Azure Diagnostics bővítmény](../../virtual-machines/windows/extensions-diagnostics-template.md)
-   [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) a Azure Key Vault használatával
-   [Automatikus leállítási szabályzat](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) , amely csökkenti a virtuális gépek erőforrásainak felhasználását, ha nincs használatban

**App Service Environment v2**: A [Azure app Service Environment](https://docs.microsoft.com/azure/app-service/environment/intro) egy app Service funkció, amely teljesen elkülönített és dedikált környezetet biztosít a app Service alkalmazások biztonságos, nagy léptékű futtatásához.

App Service környezetek el vannak különítve, hogy csak egyetlen ügyfél alkalmazásait futtassák, és mindig virtuális hálózatba vannak telepítve. Az ügyfelek részletesen szabályozzák mind a bejövő, mind a kimenő alkalmazások hálózati forgalmát, és az alkalmazások nagy sebességű biztonságos kapcsolatokat hozhatnak létre a virtuális hálózatokon a helyszíni vállalati erőforrásokhoz.

App Service környezetek használata ehhez az architektúrához a következő vezérlőket/konfigurációkat teszi lehetővé:

- App Service környezeteket az elkülönített szolgáltatáscsomag használatára kell konfigurálni
    - Különböző App Service környezetek konfigurálása különböző besorolású alkalmazásokhoz
- Biztonságos Azure-beli virtuális hálózati és hálózati biztonsági szabályokon belüli gazdagép
- App Service környezetek, amelyek önaláírt belső terheléselosztó tanúsítvánnyal vannak konfigurálva a HTTPS-kommunikációhoz. Ajánlott eljárásként a Microsoft egy megbízható hitelesítésszolgáltató használatát javasolja a fokozott biztonság érdekében.
- [Belső terheléselosztási mód](../../app-service/environment/app-service-environment-with-internal-load-balancer.md) (3. mód)
- [A TLS 1.0-s és 1.1-es verziójának](../../app-service/environment/app-service-app-service-environment-custom-settings.md) letiltása
- [TLS-titkosítás](../../app-service/environment/app-service-app-service-environment-custom-settings.md) módosítása
- [Bejövő forgalom vezérlése N/W-portok](../../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md)
- [Webalkalmazási tűzfal – az adatkorlátozás](../../app-service/environment/app-service-app-service-environment-web-application-firewall.md)
- [Azure SQL Database forgalom](../../app-service/environment/app-service-app-service-environment-network-architecture-overview.md) engedélyezése

**Azure Web App**: A [Azure app Service](https://docs.microsoft.com/azure/app-service/) lehetővé teszi, hogy az ügyfelek az infrastruktúra kezelése nélkül saját maguk által választott programozási nyelven hozzanak létre és működtessenek webalkalmazásokat. Automatikus méretezést és magas rendelkezésre állást kínál, támogatja a Windows és a Linux rendszert is, valamint lehetővé teszi az automatikus üzembe helyezéseket a GitHub, az Azure DevOps Services vagy bármely egyéb Git-adattár használatával.

### <a name="virtual-network"></a>Virtuális hálózat
Az architektúra a 10.200.0.0/16 címtartomány szerinti magánhálózati virtuális hálózatot definiálja.

**Hálózati biztonsági csoportok**: A [hálózati biztonsági csoportok](../../virtual-network/virtual-network-vnet-plan-design-arm.md) olyan hozzáférés-vezérlési listát tartalmaznak, amelyek engedélyezik vagy megtagadják a forgalmat a virtuális hálózaton belül. A hálózati biztonsági csoportok segítségével biztonságossá teheti a forgalmat alhálózat vagy egyedi virtuálisgép-szinten. A következő hálózati biztonsági csoportok léteznek:
- 1 hálózati biztonsági csoport Application Gateway
- 1 hálózati biztonsági csoport App Service Environment
- 1 hálózati biztonsági csoport Azure SQL Database
- 1 hálózati biztonsági csoport a megerősített gazdagéphez

A hálózati biztonsági csoportok mindegyike megnyitotta a megadott portokat és protokollokat, hogy a megoldás biztonságosan és megfelelően működjön. Emellett a következő konfigurációk mindegyik hálózati biztonsági csoport esetében engedélyezve vannak:

  - A [diagnosztikai naplók és események](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) engedélyezve vannak, és Storage-fiókban tárolódnak
  - Azure Monitor naplók a [hálózati biztonsági csoport diagnosztikaához](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json) vannak csatlakoztatva

Alhálózatok: Minden alhálózat társítva van a hozzá tartozó hálózati biztonsági csoporttal.

**Azure DNS**: A tartománynévrendszer vagy a DNS a webhely vagy szolgáltatás nevének az IP-címére való fordítására (vagy feloldására) felelős. A [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) olyan DNS-tartományok üzemeltetési szolgáltatása, amelyek névfeloldást biztosítanak az Azure-infrastruktúra használatával. Az Azure-ban a felhasználók a DNS-rekordokat a többi Azure-szolgáltatással azonos hitelesítő adatok, API-k, eszközök és számlázás használatával kezelhetik. A Azure DNS támogatja a magánhálózati DNS-tartományokat is.

**Azure Load Balancer**: [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) lehetővé teszi az ügyfelek számára az alkalmazások skálázását és magas rendelkezésre állású szolgáltatások létrehozását. Load Balancer támogatja a bejövő és a kimenő forgatókönyveket, valamint alacsony késést és nagy átviteli sebességet biztosít, és akár több millió folyamatot is képes az összes TCP-és UDP-alkalmazáshoz.

### <a name="data-in-transit"></a>Átvitt adatok
Az Azure alapértelmezés szerint titkosítja az Azure-adatközpontok és az összes kommunikációját. 

Az ügyfél tulajdonában lévő hálózatokból érkező védett adatok esetében az architektúra az Azure-t használja az interneten vagy a ExpressRoute az IPSEC-sel konfigurált VPN Gateway.

Emellett az Azure felügyeleti portálon keresztül az Azure-ba irányuló összes tranzakció a TLS-t használó HTTPS protokollon keresztül történik.

### <a name="data-at-rest"></a>Inaktív adat
Az architektúra titkosítva, adatbázis-naplózással és egyéb mértékekkel védi a nyugalmi állapotban lévő adatok védelmét.

**Azure Storage**: A titkosított adatoknak a nyugalmi követelmények teljesítése érdekében az összes [Azure Storage](https://azure.microsoft.com/services/storage/) [Storage Service encryption](../../storage/common/storage-service-encryption.md)használ. Ez segít megvédeni és megőrizni a szervezeti biztonsági kötelezettségvállalások és a megfelelőségi követelmények támogatását az Australian Government ISM által meghatározott, a szervezet számára.

**Azure Disk Encryption**: [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) kihasználja a Windows BitLocker szolgáltatását, hogy mennyiségi titkosítást biztosítson az adatlemezek számára. A megoldás integrálva van Azure Key Vaultekkel a lemezes titkosítási kulcsok szabályozása és kezelése érdekében.

**Azure SQL Database**: A Azure SQL Database példány a következő adatbázis-biztonsági mértékeket használja:
-   [Active Directory a hitelesítés és az engedélyezés](https://docs.microsoft.com/azure/sql-database/sql-database-AAD-authentication) lehetővé teszi az adatbázis-felhasználók és más Microsoft-szolgáltatások Identitáskezelés kezelését egy központi helyen.
-   Az [SQL Database naplózása](../../sql-database/sql-database-auditing.md) nyomon követi az adatbázis eseményeit, és egy Azure Storage-fiókban lévő naplóba írja azokat.
-   Azure SQL Database úgy van konfigurálva, hogy [transzparens adattitkosítást](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)használjon, amely valós idejű titkosítást és visszafejtést végez az adatbázis, a társított biztonsági másolatok és a tranzakciós naplófájlok számára, hogy megvédje az adatokat a nyugalmi állapotban. Az transzparens adattitkosítás biztosítja, hogy a tárolt adataik nem érvényesek a jogosulatlan hozzáférésre.
-   A [Tűzfalszabályok](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) megakadályozzák az adatbázis-kiszolgálók hozzáférését a megfelelő engedélyek megadása előtt. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján.
-   Az [SQL-veszélyforrások észlelése](../../sql-database/sql-database-threat-detection.md) lehetővé teszi az észlelést és a reagálást a potenciális fenyegetésekre, mivel ezek a hibák a gyanús adatbázis-tevékenységek, a potenciális sebezhetőségek, az SQL-injektálási támadások és a rendellenes adatbázis-hozzáférési minták esetében Az SQL Threat Detection [Azure Security Centerokkal](https://azure.microsoft.com/services/security-center/)integrálja a riasztásokat, beleértve a gyanús tevékenységek részleteit, valamint a fenyegetés kivizsgálásával és enyhítésével kapcsolatos javasolt lépéseket.
-   [Always encrypted oszlopok](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) biztosítják, hogy a bizalmas adatok soha ne jelenjenek meg egyszerű szövegként az adatbázis-rendszeren belül. Az adattitkosítás engedélyezése után csak az ügyfélalkalmazások vagy az alkalmazások férhetnek hozzá a kulcsokhoz.
- [SQL Database a dinamikus adatmaszkolás](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) korlátozza a bizalmas adatok megjelenítését azáltal, hogy az adatokat nem Kiemelt felhasználók vagy alkalmazások számára fedi le. A dinamikus adatmaszkolás automatikusan képes észlelni a potenciálisan bizalmas adatokat, és javaslatot tesz a megfelelő maszkok alkalmazására. Ez segít a hozzáférés csökkentésében, hogy a bizalmas adatok ne lépjék ki az adatbázist jogosulatlan hozzáférés útján. Az ügyfeleknek módosítaniuk kell a dinamikus adatmaszkolási beállításokat az adatbázis-sémájuk betartásához.

### <a name="identity-management"></a>Identitáskezelés
Az ügyfelek a helyszíni Active Directory összevont szolgáltatásokat használhatják a összevonása a [Azure Active Directory](https://azure.microsoft.com/services/active-directory/), amely a Microsoft több-bérlős felhőalapú címtár-és Identitáskezelés-kezelő szolgáltatása. A [Azure Active Directory Connect](../../active-directory/hybrid/whatis-hybrid-identity.md) Azure Active Directoryokkal integrálja a helyszíni címtárakat. A megoldás minden felhasználója Azure Active Directory fiókokat igényel, beleértve a Azure SQL Databasehoz hozzáférő felhasználókat is. Az összevonási bejelentkezéssel a felhasználók bejelentkezhetnek Azure Active Directoryba, és a helyszíni hitelesítő adatok használatával hitelesíthetők az Azure-erőforrásokkal.

Emellett a következő Azure Active Directory képességek segítenek az Azure-környezetben tárolt adathozzáférések kezelésében:
- Az alkalmazáshoz való hitelesítés Azure Active Directory használatával történik. További információ: [alkalmazások integrálása a Azure Active Directorysal](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Emellett az adatbázis oszlopának titkosítása a Azure Active Directory használatával hitelesíti az alkalmazást a Azure SQL Database. További információ: a [bizalmas adatok védelme Azure SQL Databaseban](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- Az [Azure szerepköralapú hozzáférés-vezérlés](../../role-based-access-control/role-assignments-portal.md) lehetővé teszi a rendszergazdáknak, hogy részletes hozzáférési engedélyeket határozzanak meg, amelyek csak a felhasználóknak a feladataik elvégzéséhez szükséges hozzáférést biztosítják. Ahelyett, hogy minden felhasználó számára korlátlan engedélyt adna az Azure-erőforrásokhoz, a rendszergazdák csak bizonyos műveleteket végezhetnek el az adatokhoz való hozzáféréshez. Az előfizetés-hozzáférés az előfizetés rendszergazdájára korlátozódik.
- [Azure Active Directory Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) lehetővé teszi, hogy az ügyfelek csökkentsék azon felhasználók számát, akik hozzáféréssel rendelkeznek bizonyos adatokhoz. A rendszergazdák Azure Active Directory Privileged Identity Management használhatják az emelt szintű identitások felderítését, korlátozását és figyelését, valamint az erőforrásokhoz való hozzáférésüket. Ez a funkció az igény szerinti, igény szerinti rendszergazdai hozzáférés biztosítására is használható, ha szükséges.
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) észleli a szervezetek identitásait érintő lehetséges biztonsági réseket, automatikusan választ állít be a szervezet identitásával kapcsolatos gyanús műveletekre, és megvizsgálja a gyanús incidensek, hogy megtegye a megfelelő lépéseket a megoldásához.

**Azure multi-Factor Authentication**: Az identitások elleni védelem érdekében meg kell valósítani a többtényezős hitelesítést. Az [Azure multi-Factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/) egy könnyen használható, méretezhető és megbízható megoldás, amely második hitelesítési módszert biztosít a felhasználók számára. Az Azure Multi-Factor Authentication a felhő hatékonyságát használja, és integrálható a helyszíni Active Directory és az egyéni alkalmazásokkal. Ez a védelem nagy mennyiségű, kritikus fontosságú forgatókönyvekre terjed ki.

### <a name="security"></a>Biztonság
**Titkok kezelése**: A megoldás a kulcsok és titkok kezeléséhez [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) használ. Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. Az alábbi Azure Key Vault-képességek segítenek az ügyfeleknek az adatvédelemben:
- A speciális hozzáférési szabályzatok a szükséges módon vannak konfigurálva.
- Key Vault hozzáférési házirendek minimálisan szükséges engedélyekkel vannak definiálva a kulcsokhoz és a titkokhoz.
- Key Vault összes kulcsának és titkának lejárati dátuma van.
- A Key Vault összes kulcsát speciális hardveres biztonsági modulok védik. A kulcs típusa egy 2048 bites RSA-kulcsot tartalmazó hardveres biztonsági modul.
- Minden felhasználó és identitás a szerepköralapú hozzáférés-vezérlés használatával minimálisan szükséges engedélyeket kap.
- Key Vault diagnosztikai naplói legalább 365 napos megőrzési időtartammal engedélyezettek.
- A kulcsok számára engedélyezett titkosítási műveletek csak a szükségesek.

**Azure Security Center**: A [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)segítségével az ügyfelek központilag alkalmazhatják és kezelhetik a munkaterhelések biztonsági szabályzatait, korlátozhatja a fenyegetéseket, és észlelheti és reagálhat a támadásokra. Azure Security Center az Azure-szolgáltatások meglévő konfigurációit is elérheti, hogy konfigurációs és szolgáltatási javaslatokat nyújtson a biztonsági helyzetek és az adatvédelem javításához.

Azure Security Center különböző észlelési képességekkel figyelmezteti az ügyfeleket a környezetekhez kapcsolódó lehetséges támadásokra. Ezek a riasztások értékes információkat tartalmaznak arról, hogy mi váltotta ki a riasztást, valamint a támadás forrásáról és az általa célba vett erőforrásokról. A Azure Security Center [előre meghatározott biztonsági riasztásokkal](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)rendelkezik, amelyek egy fenyegetés vagy gyanús tevékenység bekövetkeztekor aktiválódnak. A Azure Security Center [Egyéni riasztási szabályai](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) lehetővé teszik, hogy az ügyfelek új biztonsági riasztásokat határozzanak meg a környezetből már összegyűjtött adatok alapján.

A Azure Security Center rangsorolt biztonsági riasztásokat és incidenseket biztosít, így egyszerűbbé válik az ügyfelek számára a potenciális biztonsági problémák felderítése és kezelése. Az egyes észlelt fenyegetésekkel kapcsolatos [fenyegetési intelligenciáról szóló jelentés](https://docs.microsoft.com/azure/security-center/security-center-threat-report) jön létre, amely segít az incidensek megválaszolásában a fenyegetések kivizsgálásában és szervizelését.

**Application Gateway**: Az architektúra csökkenti a biztonsági rések kockázatát, ha egy webalkalmazási tűzfallal rendelkező Application Gatewayt használ, és engedélyezve van a OWASP-alapú szabályrendszert. A további funkciók a következők:

- [End-to-end-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [SSL-kiszervezés](../../application-gateway/create-ssl-portal.md) engedélyezése
- [A TLS 1.0-s és 1.1-es verziójának](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell) letiltása
- [Webalkalmazási tűzfal](../../application-gateway/waf-overview.md) (megelőzési mód)
- [Megelőzési mód](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) OWASP 3,0-es szabályrendszert
- [Diagnosztikai naplózás](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics) engedélyezése
- [Egyéni állapot-mintavételek](../../application-gateway/quick-create-portal.md)
- A [Azure Security Center](https://azure.microsoft.com/services/security-center) és [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) további védelmet és értesítéseket biztosítanak. A Azure Security Center a hírnév rendszerét is biztosítja.

### <a name="logging-and-auditing"></a>Naplózás

Az Azure-szolgáltatások széles körben naplózzák a rendszer és a felhasználó tevékenységét, valamint a rendszer állapotát:
- **Tevékenységek naplói**: A [tevékenységek naplói](../../azure-monitor/platform/activity-logs-overview.md) betekintést nyújtanak az előfizetésben lévő erőforrásokon végrehajtott műveletekre. A Tevékenységnaplók segítenek meghatározni a művelet kezdeményezőjét, az előfordulás időpontját és az állapotot.
- **Diagnosztikai naplók**: A [diagnosztikai naplók](../../azure-monitor/platform/resource-logs-overview.md) az összes erőforrás által kibocsátott összes naplót tartalmazzák. Ezek a naplók a Windows-eseménynaplókat, az Azure Storage-naplókat, a Key Vault naplókat, valamint Application Gateway hozzáférési és tűzfal-naplókat tartalmaznak. Az összes diagnosztikai napló egy központi és titkosított Azure Storage-fiókba írja az archiválást. A megőrzés a felhasználó által konfigurálható, akár 730 nap, hogy megfeleljen a szervezetre vonatkozó megőrzési követelményeknek.

**Naplók Azure monitor**: Ezeket a naplókat a rendszer a feldolgozás, tárolás és irányítópult-jelentéskészítés [Azure monitor naplófájljaiban](https://azure.microsoft.com/services/log-analytics/) összesíti. Az adatgyűjtés után a rendszer adattípusonként külön táblába rendezi az adatokat, ez az eredeti forrástól függetlenül lehetővé teszi az adatok együttes elemzését. Emellett a Azure Security Center integrálható Azure Monitor naplókkal, így az ügyfelek Kusto-lekérdezéseket használhatnak a biztonsági események adatainak eléréséhez és más szolgáltatásokból származó adatokkal való összekapcsolásához.

Az architektúra részeként az alábbi Azure- [figyelési megoldások](../../monitoring/monitoring-solutions.md) szerepelnek:
-   [Active Directory Assessment](../../azure-monitor/insights/ad-assessment.md): A Active Directory Health-ellenőrzési megoldás rendszeres időközönként kivizsgálja a kiszolgálói környezetek kockázatait és állapotát, és rangsorolja a telepített kiszolgálói infrastruktúrára jellemző ajánlásokat.
- [SQL Assessment](../../azure-monitor/insights/sql-assessment.md): Az SQL Health-ellenőrzési megoldás rendszeres időközönként kivizsgálja a kiszolgálói környezetek kockázatait és állapotát, és az ügyfelek számára az üzembe helyezett kiszolgáló-infrastruktúrára jellemző ajánlások rangsorolt listáját biztosítja.
- [Agent Health](../../monitoring/monitoring-solution-agenthealth.md): Az Agent Health-megoldás jelentést készít, hogy hány ügynök van üzembe helyezve, valamint a földrajzi eloszlásuk, valamint a nem válaszoló ügynökök száma, valamint az operatív adatküldés alatt álló ügynökök száma.
-   [Activity log Analytics](../../azure-monitor/platform/collect-activity-logs.md): Az Activity Log Analytics-megoldás segítséget nyújt az Azure-tevékenységek naplóinak elemzéséhez az ügyfelek összes Azure-előfizetése között.

**Azure Automation**: [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) tárolja, futtatja és felügyeli a runbookok. Ebben a megoldásban a runbookok segítséget nyújt a naplók Azure SQL Databaseból való gyűjtésében. Az Automation [change Tracking](../../automation/change-tracking.md) megoldás lehetővé teszi, hogy az ügyfelek könnyedén azonosíthassák a környezet változásait.

**Azure monitor**: [Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) segíti a felhasználókat a teljesítmény nyomon követésében, a biztonság fenntartásában és a trendek azonosításában azáltal, hogy lehetővé teszi a szervezetek számára, hogy naplózzák, riasztásokat hozzon létre és archiválják az adatok archiválását, beleértve az Azure

Azure Network Watcher: Az [azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) eszközöket biztosít az Azure-beli virtuális hálózatokban található erőforrások figyelésére, diagnosztizálására, megtekintésére, valamint a naplók engedélyezésére vagy letiltására.  A nemzetközösségi entitásoknak Network Watcher flow-naplókat kell alkalmazniuk a NSG és a Virtual Machineshoz. Ezeket a naplókat egy dedikált Storage-fiókban kell tárolni, amely csak a biztonsági naplókat tárolja, és a Storage-fiókhoz való hozzáférést a szerepköralapú hozzáférés-vezérléssel kell biztosítani.

## <a name="threat-model"></a>Veszélyforrások modellje

Az ehhez a hivatkozási architektúrához tartozó Adatfolyam-diagram [letölthető](https://aka.ms/au-protected-paaswa-tm) , vagy a következő címen érhető el. Ez a modell lehetővé teszi az ügyfeleknek, hogy a módosítások végrehajtása során megértsék a rendszerinfrastruktúra lehetséges kockázatait.

![Pásti-webalkalmazás az au által védett veszélyforrások modelljéhez](images/au-protected-paaswa-threat-model.png?raw=true "Pásti-webalkalmazás az au által védett veszélyforrások modelljének diagramja")

## <a name="compliance-documentation"></a>Megfelelőségi dokumentáció
Ezt a megfelelőségi dokumentációt a Microsoft a Microsoft által elérhető platformok és szolgáltatások alapján állítja elő. Az ügyfelek központi telepítésének széles választéka miatt ez a dokumentáció egy általános megközelítést biztosít a megoldáshoz, amelyet csak az Azure-környezetben üzemeltet. Az ügyfelek a saját működési környezetük és az üzleti eredmények alapján azonosítják és használhatják az alternatív termékeket és szolgáltatásokat. A helyszíni erőforrások használatára kiválasztott ügyfeleknek a helyszíni erőforrások biztonságával és műveleteivel kell foglalkoznia. A dokumentált megoldást az ügyfelek testre szabhatók az adott helyszíni és biztonsági követelmények kielégítése érdekében.

Az [Azure Security and Compliance Blueprint-au által védett ügyfél-felelősségi mátrix](https://aka.ms/au-protected-crm) FELSOROLJA az au-Prot által igényelt összes biztonsági ellenőrzést. Ez a mátrix részletesen ismerteti, hogy az egyes vezérlőelemek megvalósítása a Microsoft, az ügyfél vagy a kettő közötti megosztás feladata-e.

A [Azure Security and Compliance Blueprint-au által védett Pásti webalkalmazás-implementációs mátrix](https://aka.ms/au-protected-paaswa-cim) olyan információkat nyújt, amelyekkel az au-alapú webalkalmazás-architektúra által tárgyalt, többek között a részletes leírását a következő témakör tartalmazza: hogyan teljesíti a megvalósítás az egyes kezelt vezérlők követelményeit.

## <a name="guidance-and-recommendations"></a>Útmutatás és javaslatok
### <a name="vpn-and-expressroute"></a>VPN-és ExpressRoute

Besorolt információk esetén a biztonságos IPSec VPN-alagutat úgy kell konfigurálni, hogy biztonságosan kapcsolatot létesítsen a IaaS webalkalmazás-hivatkozási architektúrájának részeként üzembe helyezett erőforrásokkal. Az IPSec VPN megfelelő beállításával az ügyfelek hozzáadhatnak egy védelmi réteget az átvitelhez.

A biztonságos IPSec VPN-alagút Azure-nal való megvalósításával létrehozhat egy helyszíni hálózat és egy Azure-beli virtuális hálózat közötti virtuális magánhálózati kapcsolatot. Ez a kapcsolat az interneten keresztül végezhető el, és lehetővé teszi az ügyfeleknek, hogy az ügyfél hálózata és az Azure közötti titkosított kapcsolaton belül biztonságosan "alagút" adatokat használjanak. A helyek közötti VPN egy olyan biztonságos, érett technológia, amelyet évtizedek óta minden méretben üzembe helyezett a vállalatok. 

Mivel a VPN-alagúton belüli forgalom egy helyek közötti VPN-kapcsolaton keresztül halad át az interneten, a Microsoft privát kapcsolódási lehetőséget kínál. Az Azure ExpressRoute egy dedikált kapcsolat az Azure és egy helyszíni hely vagy egy Exchange-szolgáltató között, és privát hálózatnak számít. Mivel a ExpressRoute-kapcsolatok nem az interneten keresztül haladnak át, ezek a kapcsolatok megbízhatóbbak, gyorsabbak és kevesebb késéssel rendelkeznek, mint az interneten keresztüli szokásos kapcsolatok. Továbbá, mivel ez az ügyfél távközlési szolgáltatójának közvetlen kapcsolata, az adat nem az interneten keresztül történik, ezért nem lesz elérhető.

Ajánlott eljárások egy biztonságos hibrid hálózat megvalósításához, amely kiterjeszti a helyszíni hálózatot az Azure- [ra.](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid) 

Az Internet vagy az Azure ExpressRoute védelme érdekében az ügyfeleknek az alábbi beállítások alkalmazásával kell konfigurálniuk az IPSec VPN-t:

• Az ügyfél VPN-kezdeményezőjét olyan SA élettartamra kell konfigurálni, amely nem haladja meg a 14400 másodpercet.
• Az ügyfél VPN-kezdeményezője le kell tiltania a IKEv1 agresszív üzemmódját.
• Az ügyfél VPN-kezdeményezője számára a tökéletes továbbítási titoktartást kell konfigurálni.
• A Customer VPN-kezdeményezőnek konfigurálnia kell a HMAC-SHA256 vagy újabb használatát.

A VPN-eszközök és az IPSec/IKE-paraméterek konfigurációs beállításai [elérhetők](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices) véleményezésre.

### <a name="azure-active-directory-setup"></a>Azure Active Directory telepítő
[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) elengedhetetlen az üzembe helyezés kezeléséhez és a környezettel kommunikáló személyzethez való hozzáféréshez. Egy meglévő Windows Server-Active Directory [négy kattintással](../../active-directory/hybrid/how-to-connect-install-express.md)integrálható Azure Active Directoryba.

Emellett [Azure Active Directory Connect](../../active-directory/hybrid/whatis-hybrid-identity.md) lehetővé teszi az ügyfeleknek, hogy a helyszíni [Active Directory összevonási szolgáltatások (AD FS)]( ../../active-directory/hybrid/how-to-connect-fed-azure-adfs.md) és a Azure Active Directory használatával konfigurálja az összevonást. Az összevonási bejelentkezés lehetővé teszi, hogy a felhasználók a helyi jelszavakkal jelentkezzenek be Azure Active Directory-alapú szolgáltatásba, és ne kelljen újra megadniuk a jelszavukat a vállalati hálózaton. A Active Directory összevonási szolgáltatások (AD FS) összevonásával telepítheti Active Directory összevonási szolgáltatások (AD FS) új telepítését, vagy megadhat egy meglévő telepítést egy Windows Server 2012 R2-farmban.

Ha meg szeretné akadályozni, hogy a besorolt adatok szinkronizálva legyenek Azure Active Directoryre, az ügyfelek a következő beállítások alkalmazásával korlátozhatják a Azure Active Directory replikált attribútumokat Azure Active Directory Connect:

- [Szűrés engedélyezése](../../active-directory/hybrid/how-to-connect-sync-configure-filtering.md)
- [Jelszó-kivonat szinkronizálásának letiltása](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)
-   [Jelszó visszaírási letiltása](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)
-   [Eszköz visszaírási letiltása](../../active-directory/hybrid/how-to-connect-device-writeback.md)
-   Hagyja meg az alapértelmezett beállításokat a véletlen törlés és az [automatikus frissítés](../../active-directory/hybrid/how-to-connect-install-automatic-upgrade.md) [megakadályozása érdekében](../../active-directory/hybrid/how-to-connect-sync-feature-prevent-accidental-deletes.md)


## <a name="disclaimer"></a>Jogi nyilatkozat

 - Ez a dokumentum csak tájékoztató jellegű. A MICROSOFT NEM VÁLLAL SEMMILYEN KIFEJEZETT, VÉLELMEZETT VAGY TÖRVÉNYES GARANCIÁT A JELEN DOKUMENTUMBAN FOGLALT INFORMÁCIÓK ALAPJÁN. Ez a dokumentum "aktuálisként" van megadva. Az ebben a dokumentumban kifejezett információk és nézetek, beleértve az URL-címeket és az internetes webhelyek más hivatkozásait, értesítés nélkül változhatnak. A dokumentumot olvasó ügyfeleink a használatuk kockázatát viselik.
 - A jelen dokumentum nem biztosít semmilyen jogot semmilyen Microsoft-termékben vagy-megoldásban található szellemi tulajdonhoz.
 - Az ügyfelek belső referenciák szerint másolhatják és használhatják ezt a dokumentumot.
 - A jelen dokumentumban szereplő javaslatok megnövelték az adatok, a hálózat vagy a számítási erőforrások használatát az Azure-ban, és növelhetik az ügyfelek Azure-licencét vagy előfizetési költségeit.
 - Ez az architektúra arra szolgál, hogy az ügyfelek számára a konkrét követelményekhez igazodva és nem használható éles környezetben.
 - Ez a dokumentum hivatkozásként van kifejlesztve, és nem használható az összes olyan eszköz meghatározására, amelyekkel az ügyfél megfelel bizonyos megfelelőségi követelményeknek és előírásoknak. Az ügyfeleknek jóvá kell hagyniuk a szervezeten belüli jogi támogatást a jóváhagyott ügyfél-implementációkban.
