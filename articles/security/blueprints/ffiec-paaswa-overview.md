---
title: Azure Security and Compliance Blueprint-FFIEC webes alkalmazás
description: Azure Security and Compliance Blueprint-FFIEC webes alkalmazás
services: security
author: meladie
ms.assetid: a552e313-2b46-4001-b6e2-bed0b7757ac5
ms.service: security
ms.topic: article
ms.date: 06/20/2018
ms.author: meladie
ms.openlocfilehash: 5f6cde099c72313df9f0e9a1a7a517c195a6da12
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71257216"
---
# <a name="azure-security-and-compliance-blueprint-paas-web-application-for-ffiec-financial-services"></a>Azure Security and Compliance Blueprint: A FFIEC pénzügyi szolgáltatásainak Péter-webalkalmazása

## <a name="overview"></a>Áttekintés

Ez a Azure Security and Compliance Blueprint Automation útmutatást nyújt a szövetségi pénzügyi intézmény által szabályozott pénzügyi adatok gyűjtésére, tárolására és lekérésére alkalmas, szolgáltatásként nyújtott platform ( Vizsgálati Tanács (FFIEC). Ez a megoldás automatizálja az Azure-erőforrások üzembe helyezését és konfigurálását egy közös hivatkozási architektúrához, amely megmutatja, hogy az ügyfelek milyen módon tudják kielégíteni a konkrét biztonsági és megfelelőségi követelményeket, és alapként szolgálnak az ügyfelek számára saját megoldások konfigurálása az Azure-ban. A megoldás a követelmények egy részhalmazát valósítja meg a FFIEC-kézikönyvekből. A FFIEC-követelményekkel és a megoldással kapcsolatos további információkért tekintse meg a [megfelelőségi dokumentáció](#compliance-documentation) című szakaszt.

Ez a Azure Security and Compliance Blueprint Automation az előre konfigurált biztonsági ellenőrzésekkel üzembe helyez egy Pásti webalkalmazás-referenciát, amely segít az ügyfeleknek a FFIEC követelményeinek való megfelelésben. A megoldás Azure Resource Manager sablonokból és PowerShell-parancsfájlokból áll, amelyek az erőforrások üzembe helyezését és konfigurálását ismertetik.

Ez az architektúra arra szolgál, hogy az ügyfelek számára a konkrét követelményekhez igazodva és nem használható éles környezetben. Ha módosítás nélkül helyezi üzembe az alkalmazást ebbe a környezetbe, nem elegendő ahhoz, hogy teljesen megfeleljen a FFIEC-célkitűzések követelményeinek. Vegye figyelembe a következőket:
- Ez az architektúra olyan alapkonfigurációt biztosít, amellyel az ügyfelek FFIEC-kompatibilis módon használhatják az Azure-t.
- Az ügyfelek felelősek az architektúrával létrehozott megoldások megfelelő biztonsági és megfelelőségi értékelésének elvégzéséért, mivel a követelmények az egyes ügyfelek implementációjának sajátosságai alapján változhatnak.

A FFIEC-megfelelőség megvalósítása megköveteli, hogy a minősített ellenőrök igazolják az üzemi ügyfelek megoldásait. A naplózást a FFIEC tagjainak, többek között a Federal Reserve System (FRB), a Federal deposit Insurance Corporation (FDIC), a National Credit Union Administration (NCUA), a számvevő irodájának elnökei látják el. a pénznem (OCC) és a fogyasztói pénzügyi védelmi iroda (CFPB). Ezek a vizsgáztatók tanúsítják, hogy a naplózást az auditált intézménytől független értékelők végzik. Az ügyfelek felelősek az architektúrával létrehozott megoldások megfelelő biztonsági és megfelelőségi felmérésének elvégzéséért, mivel a követelmények az egyes ügyfelek implementációjának sajátosságai alapján változhatnak.

Kattintson [ide](https://aka.ms/ffiec-paaswa-repo) az üzembe helyezési utasításokhoz.

## <a name="architecture-diagram-and-components"></a>Architektúra ábrája és összetevői

Ez a Azure Security and Compliance Blueprint Automation egy Azure SQL Database-háttérrel rendelkező, a Pásti webalkalmazáshoz tartozó hivatkozási architektúrát helyez üzembe. A webalkalmazást egy elkülönített Azure App Service Environment üzemelteti, amely egy Azure-adatközpont privát, dedikált környezete. A környezet terheléselosztása az Azure által felügyelt virtuális gépeken lévő webalkalmazások forgalmát terheli. Ez az architektúra hálózati biztonsági csoportokat, Application Gateway, Azure DNS és Load Balancer is tartalmaz.

A továbbfejlesztett elemzési és jelentéskészítési szolgáltatásokhoz az Azure SQL Database-adatbázisok oszlopcentrikus indexekkel konfigurálhatók. Az Azure SQL Database-adatbázisok vertikálisan fel-és leállíthatók, illetve teljes mértékben kikapcsolhatók az ügyfelek általi használatra válaszul. Az összes SQL-forgalom SSL-titkosítással rendelkezik az önaláírt tanúsítványok belefoglalásával. Az ajánlott eljárás az, hogy az Azure megbízható hitelesítésszolgáltató használatát javasolja a fokozott biztonság érdekében.

A megoldás Azure Storage-fiókokat használ, amelyekkel az ügyfelek a Storage Service Encryption használatával kezelhetik az inaktív adatok titkosságát. Az Azure a rugalmasság érdekében három másolatot tárol az ügyfél kiválasztott adatközpontjában. A földrajzi redundáns tárolás biztosítja, hogy az adatok replikálása a másodlagos adatközpontba több száz kilométer távolságra történik, és az adatközpontban három példányban tárolja őket, ami megakadályozza, hogy az ügyfél elsődleges adatközpontjában negatív esemény legyen adatok.

A fokozott biztonság érdekében az ebben a megoldásban lévő összes erőforrást erőforráscsoportként kezeli Azure Resource Manageron keresztül. Azure Active Directory szerepköralapú hozzáférés-vezérléssel szabályozható az üzembe helyezett erőforrásokhoz való hozzáférés, beleértve azok kulcsait is Azure Key Vault. A rendszer állapotának figyelése Azure Monitoron keresztül történik. Az ügyfelek mindkét figyelési szolgáltatást a naplók rögzítéséhez és a rendszerállapot megjelenítéséhez egyetlen, könnyen navigálható irányítópulton.

Azure SQL Database általában a SQL Server Management Studio segítségével történik, amely egy olyan helyi gépről fut, amely a Azure SQL Database biztonságos VPN-vagy ExpressRoute-kapcsolaton keresztül történő elérésére van konfigurálva.

Emellett a Application Insights valós idejű alkalmazások teljesítmény-felügyeletet és elemzéseket biztosít Azure Monitor naplókon keresztül. **A Microsoft javasolja a VPN-vagy ExpressRoute-kapcsolat konfigurálását a felügyeleti és adatimportálási szolgáltatásba a hivatkozási architektúra alhálózatában.**

![FFIEC hivatkozási architektúra diagramja, a Pásti Web Application](images/ffiec-paaswa-architecture.png "FFIEC hivatkozási architektúra diagramja, a Pásti Web Application")

Ez a megoldás az alábbi Azure-szolgáltatásokat használja. Az üzembe helyezési architektúra részletei az [üzembe helyezési architektúra](#deployment-architecture) szakaszban találhatók.

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
- Azure Monitor (naplók)
- Azure Resource Manager
- Azure Security Center
- Azure SQL Database
- Azure Storage
- Azure Virtual Network
    - (1)/16 hálózat
    - (4)/24 hálózat
    - Network security groups (Hálózati biztonsági csoportok)
- Azure App Service

## <a name="deployment-architecture"></a>Üzembe helyezési architektúra

A következő szakasz az üzembe helyezési és megvalósítási elemeket részletezi.

**Azure Resource Manager**: [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) lehetővé teszi az ügyfelek számára, hogy csoportként működjenek együtt a megoldás erőforrásaival. Az ügyfelek egyetlen, koordinált műveletben telepíthetik, frissíthetik vagy törölhetik a megoldás összes erőforrását. Az ügyfelek egy sablont használnak az üzembe helyezéshez, és a sablon különböző környezetekben, például tesztelési, átmeneti és éles környezetben is működhet. A Resource Manager biztonsági, naplózási és címkézési funkciókat biztosít az ügyfelek számára az üzembe helyezést követően az erőforrások kezeléséhez.

**Megerősített gazdagép**: A megerősített gazdagép az egyetlen belépési pont, amely lehetővé teszi, hogy a felhasználók hozzáférhessenek az üzembe helyezett erőforrásokhoz ebben a környezetben. A megerősített gazdagép biztonságos kapcsolódást biztosít a központilag telepített erőforrásokhoz azáltal, hogy csak a nyilvános IP-címekről érkező távoli forgalmat engedélyezi biztonságos listán. A távoli asztal (RDP) forgalmának engedélyezéséhez a hálózati biztonsági csoportban meg kell határozni a forgalom forrását.

Ez a megoldás egy virtuális gépet hoz létre tartományhoz csatlakozó megerősített gazdagépként a következő konfigurációkkal:
-   [Antimalware-bővítmény](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
-   [Azure Diagnostics bővítmény](../../virtual-machines/windows/extensions-diagnostics-template.md)
-   [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) a Azure Key Vault használatával
-   [Automatikus leállítási szabályzat](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) , amely csökkenti a virtuális gépek erőforrásainak felhasználását, ha nincs használatban
-   A [Windows Defender hitelesítőadat](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) -védelme engedélyezve van, hogy a hitelesítő adatok és egyéb Titkok a futó operációs rendszertől elkülönített védett környezetekben fussanak.

**App Service Environment v2**: A Azure App Service Environment egy App Service funkció, amely teljesen elkülönített és dedikált környezetet biztosít a App Service alkalmazások biztonságos, nagy léptékű futtatásához. Az elkülönítési funkciónak meg kell felelnie a FFIEC megfelelőségi követelményeinek.

App Service környezetek el vannak különítve, hogy csak egyetlen ügyfél alkalmazásait futtassák, és mindig virtuális hálózatba vannak telepítve. Ez az elkülönítési funkció lehetővé teszi, hogy a hivatkozási architektúra teljes bérlői elkülönítést végezzen, és eltávolítsa azt az Azure több-bérlős környezetéről, amely tiltja, hogy ezek a több bérlők a telepített App Service Environment erőforrások enumerálásával rendelkezzenek. Az ügyfelek részletesen szabályozzák mind a bejövő, mind a kimenő alkalmazások hálózati forgalmát, és az alkalmazások nagy sebességű biztonságos kapcsolatokat hozhatnak létre a virtuális hálózatokon a helyszíni vállalati erőforrásokhoz. Az ügyfelek a betöltési metrikák, a rendelkezésre álló költségvetés vagy egy meghatározott ütemterv alapján "Automatikus méretezéssel" rendelkezhetnek App Service Environment.

A App Service Environment használata ehhez az architektúrához a következő konfigurációkat teszi lehetővé:

- Biztonságos Azure-beli virtuális hálózati és hálózati biztonsági szabályokon belüli gazdagép
- Önaláírt belső terheléselosztó tanúsítványa HTTPS-kommunikációhoz. Ajánlott eljárásként a Microsoft egy megbízható hitelesítésszolgáltató használatát javasolja a fokozott biztonság érdekében.
- [Belső terheléselosztási mód](../../app-service/environment/app-service-environment-with-internal-load-balancer.md)
- [TLS 1,0](../../app-service/environment/app-service-app-service-environment-custom-settings.md) letiltása
- [TLS-titkosítás](../../app-service/environment/app-service-app-service-environment-custom-settings.md) módosítása
- [Bejövő forgalom vezérlése N/W-portok](../../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md)
- [Webalkalmazási tűzfal – az adatkorlátozás](../../app-service/environment/app-service-app-service-environment-web-application-firewall.md)
- [Azure SQL Database forgalom](../../app-service/environment/app-service-app-service-environment-network-architecture-overview.md) engedélyezése

**Azure app Service**: A [Azure app Service](https://docs.microsoft.com/azure/app-service/) lehetővé teszi, hogy az ügyfelek az infrastruktúra kezelése nélkül saját maguk által választott programozási nyelven hozzanak létre és működtessenek webalkalmazásokat. Automatikus méretezést és magas rendelkezésre állást kínál, támogatja a Windows és a Linux rendszert is, valamint lehetővé teszi az automatikus üzembe helyezéseket a GitHub, az Azure DevOps vagy bármely egyéb Git-adattár használatával.

### <a name="virtual-network"></a>Virtuális hálózat

Az architektúra a 10.200.0.0/16 címtartomány szerinti magánhálózati virtuális hálózatot definiálja.

**Hálózati biztonsági csoportok**: A [hálózati biztonsági csoportok](../../virtual-network/virtual-network-vnet-plan-design-arm.md) olyan hozzáférés-vezérlési listát tartalmaznak, amelyek engedélyezik vagy megtagadják a forgalmat a virtuális hálózaton belül. A hálózati biztonsági csoportok segítségével biztonságossá teheti a forgalmat alhálózat vagy egyedi virtuálisgép-szinten. A következő hálózati biztonsági csoportok léteznek:

- 1 hálózati biztonsági csoport Application Gateway
- 1 hálózati biztonsági csoport App Service Environment
- 1 hálózati biztonsági csoport Azure SQL Database
- 1 hálózati biztonsági csoport a megerősített gazdagéphez

A hálózati biztonsági csoportok mindegyike megnyitotta a megadott portokat és protokollokat, hogy a megoldás biztonságosan és megfelelően működjön. Emellett a következő konfigurációk mindegyik hálózati biztonsági csoport esetében engedélyezve vannak:

- A [diagnosztikai naplók és események](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) engedélyezve vannak, és Storage-fiókban tárolódnak
- Azure Monitor naplók csatlakoznak a [hálózati biztonsági csoport&#39;s diagnosztikai naplóihoz](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

Alhálózatok: Minden alhálózat társítva van a hozzá tartozó hálózati biztonsági csoporttal.

**Azure DNS**: A tartománynévrendszer vagy a DNS a webhely vagy szolgáltatás nevének az IP-címére való fordítására (vagy feloldására) felelős. A [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) olyan DNS-tartományok üzemeltetési szolgáltatása, amelyek névfeloldást biztosítanak az Azure-infrastruktúra használatával. Az Azure-ban a felhasználók a DNS-rekordokat a többi Azure-szolgáltatással azonos hitelesítő adatok, API-k, eszközök és számlázás használatával kezelhetik. A Azure DNS támogatja a magánhálózati DNS-tartományokat is.

**Azure Load Balancer**: [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) lehetővé teszi az ügyfelek számára az alkalmazások skálázását és magas rendelkezésre állású szolgáltatások létrehozását. Load Balancer támogatja a bejövő és a kimenő forgatókönyveket, valamint alacsony késést és nagy átviteli sebességet biztosít, és akár több millió folyamatot is képes az összes TCP-és UDP-alkalmazáshoz.

### <a name="data-in-transit"></a>Átvitt adatok

Az Azure alapértelmezés szerint titkosítja az Azure-adatközpontok és az összes kommunikációját. Az Azure Storage-ba irányuló összes tranzakció a Azure Portal a HTTPS-en keresztül történik.

### <a name="data-at-rest"></a>Inaktív adat

Az architektúra titkosítva, adatbázis-naplózással és egyéb mértékekkel védi a nyugalmi állapotban lévő adatok védelmét.

**Azure Storage**: A titkosított adatoknak a nyugalmi követelmények teljesítése érdekében az összes [Azure Storage](https://azure.microsoft.com/services/storage/) [Storage Service encryption](../../storage/common/storage-service-encryption.md)használ. Ez segít megvédeni és megőrizni a FFIEC által meghatározott szervezeti biztonsági kötelezettségvállalások és megfelelőségi követelmények támogatásával kapcsolatos adatvédelmet.

**Azure Disk Encryption**: [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) kihasználja a Windows BitLocker szolgáltatását, hogy mennyiségi titkosítást biztosítson az adatlemezek számára. A megoldás integrálva van Azure Key Vaultekkel a lemezes titkosítási kulcsok szabályozása és kezelése érdekében.

**Azure SQL Database**: A Azure SQL Database példány a következő adatbázis-biztonsági mértékeket használja:

- [Active Directory a hitelesítés és az engedélyezés](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) lehetővé teszi az adatbázis-felhasználók és más Microsoft-szolgáltatások Identitáskezelés kezelését egy központi helyen.
- Az [SQL Database naplózása](../../sql-database/sql-database-auditing.md) nyomon követi az adatbázis eseményeit, és egy Azure Storage-fiókban lévő naplóba írja azokat.
- Azure SQL Database úgy van konfigurálva, hogy [transzparens adattitkosítást](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)használjon, amely valós idejű titkosítást és visszafejtést végez az adatbázis, a társított biztonsági másolatok és a tranzakciós naplófájlok számára, hogy megvédje az adatokat a nyugalmi állapotban. Az transzparens adattitkosítás biztosítja, hogy a tárolt adataik nem érvényesek a jogosulatlan hozzáférésre.
- A [Tűzfalszabályok](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) megakadályozzák az adatbázis-kiszolgálók hozzáférését a megfelelő engedélyek megadása előtt. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján.
- Az [SQL-veszélyforrások észlelése](../../sql-database/sql-database-threat-detection.md) lehetővé teszi az észlelést és a reagálást a potenciális fenyegetésekre, mivel ezek a hibák a gyanús adatbázis-tevékenységek, a potenciális sebezhetőségek, az SQL-injektálási támadások és a rendellenes adatbázis-hozzáférési minták esetében
- A [titkosított oszlopok](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) biztosítják, hogy a bizalmas adatok soha ne jelenjenek meg az adatbázis-rendszeren belüli egyszerű szövegként. Az adattitkosítás engedélyezése után csak az ügyfélalkalmazások vagy az alkalmazások férhetnek hozzá a kulcsokhoz.
- [SQL Database a dinamikus adatmaszkolás](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) korlátozza a bizalmas adatok megjelenítését azáltal, hogy az adatokat nem Kiemelt felhasználók vagy alkalmazások számára fedi le. A dinamikus adatmaszkolás automatikusan képes észlelni a potenciálisan bizalmas adatokat, és javaslatot tesz a megfelelő maszkok alkalmazására. Ez segít az adathozzáférés azonosításában és csökkentésében, hogy ne lépjen ki az adatbázisból jogosulatlan hozzáférés útján. Az ügyfelek feladata a dinamikus adatmaszkolási beállítások módosítása az adatbázis-sémájuk betartásához.

### <a name="identity-management"></a>Identitáskezelés

Az alábbi technológiák az Azure-környezetben tárolt adathozzáférések kezelésére szolgáló képességeket biztosítanak:

- [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) a Microsoft&#39;s több-bérlős felhőalapú címtár-és Identitáskezelés-kezelő szolgáltatás. A megoldás összes felhasználója Azure Active Directoryban jön létre, beleértve a Azure SQL Databasehoz hozzáférő felhasználókat is.
- Az alkalmazáshoz való hitelesítés Azure Active Directory használatával történik. További információ: [alkalmazások integrálása a Azure Active Directorysal](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Emellett az adatbázis oszlopának titkosítása a Azure Active Directory használatával hitelesíti az alkalmazást a Azure SQL Database. További információ: a [bizalmas adatok védelme Azure SQL Databaseban](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- Az [Azure szerepköralapú hozzáférés-vezérlés](../../role-based-access-control/role-assignments-portal.md) lehetővé teszi a rendszergazdáknak, hogy részletes hozzáférési engedélyeket határozzanak meg, amelyek csak a felhasználóknak a feladataik elvégzéséhez szükséges hozzáférést biztosítják. Ahelyett, hogy minden felhasználó számára korlátlan engedélyt adna az Azure-erőforrásokhoz, a rendszergazdák csak bizonyos műveleteket végezhetnek el az adatokhoz való hozzáféréshez. Az előfizetés-hozzáférés az előfizetés rendszergazdájára korlátozódik.
- [Azure Active Directory Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) lehetővé teszi, hogy az ügyfelek csökkentsék azon felhasználók számát, akik hozzáféréssel rendelkeznek bizonyos adatokhoz. A rendszergazdák Azure Active Directory Privileged Identity Management használhatják az emelt szintű identitások felderítését, korlátozását és figyelését, valamint az erőforrásokhoz való hozzáférésüket. Ez a funkció az igény szerinti, igény szerinti rendszergazdai hozzáférés biztosítására is használható, ha szükséges.
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) észleli a szervezet&#39;s identitásait érintő lehetséges biztonsági réseket, automatikus válaszokat konfigurál a szervezet&#39;s identitásával kapcsolatos gyanús műveletekre, és megvizsgálja a gyanús incidenseket, hogy tegye meg a megfelelő lépéseket a megoldásához.

### <a name="security"></a>Biztonság

**Titkok kezelése**: A megoldás a kulcsok és titkok kezeléséhez [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) használ. Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. Az alábbi Azure Key Vault-funkciók segítenek az ügyfeleknek az ilyen jellegű adatvédelemben és hozzáférésben:

- A speciális hozzáférési szabályzatok a szükséges módon vannak konfigurálva.
- Key Vault hozzáférési házirendek minimálisan szükséges engedélyekkel vannak definiálva a kulcsokhoz és a titkokhoz.
- Key Vault összes kulcsának és titkának lejárati dátuma van.
- A Key Vault összes kulcsát speciális hardveres biztonsági modulok védik. A kulcs típusa HSM-védelemmel ellátott 2048-bites RSA-kulcs.
- Minden felhasználó és identitás a szerepköralapú hozzáférés-vezérlés használatával minimálisan szükséges engedélyeket kap.
- Key Vault diagnosztikai naplói legalább 365 napos megőrzési időtartammal engedélyezettek.
- A kulcsok számára engedélyezett titkosítási műveletek csak a szükségesek.

**Azure Security Center**: A [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)segítségével az ügyfelek központilag alkalmazhatják és kezelhetik a munkaterhelések biztonsági szabályzatait, korlátozhatja a fenyegetéseket, és észlelheti és reagálhat a támadásokra. Azure Security Center az Azure-szolgáltatások meglévő konfigurációit is elérheti, hogy konfigurációs és szolgáltatási javaslatokat nyújtson a biztonsági helyzetek és az adatvédelem javításához.

Azure Security Center különböző észlelési képességekkel figyelmezteti az ügyfeleket a környezetekhez kapcsolódó lehetséges támadásokra. Ezek a riasztások értékes információkat tartalmaznak arról, hogy mi váltotta ki a riasztást, valamint a támadás forrásáról és az általa célba vett erőforrásokról. A Azure Security Center [előre meghatározott biztonsági riasztásokkal](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)rendelkezik, amelyek egy fenyegetés vagy gyanús tevékenység bekövetkeztekor aktiválódnak. A Azure Security Center [Egyéni riasztási szabályai](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) lehetővé teszik, hogy az ügyfelek új biztonsági riasztásokat határozzanak meg a környezetből már összegyűjtött adatok alapján.

A Azure Security Center rangsorolt biztonsági riasztásokat és incidenseket biztosít, így egyszerűbbé válik az ügyfelek számára a potenciális biztonsági problémák felderítése és kezelése. Az egyes észlelt fenyegetésekkel kapcsolatos [fenyegetési intelligenciáról szóló jelentés](https://docs.microsoft.com/azure/security-center/security-center-threat-report) jön létre, amely segít az incidensek megválaszolásában a fenyegetések kivizsgálásában és szervizelését.

**Azure Application Gateway**: Az architektúra csökkenti a biztonsági rések kockázatát egy olyan Azure-Application Gateway használatával, amely konfigurálva van egy webalkalmazási tűzfallal, és a OWASP szabályrendszert engedélyezve van. A további funkciók a következők:

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

**Naplók Azure monitor**: Ezeket a naplókat a rendszer a feldolgozás, tárolás és irányítópult-jelentéskészítés [Azure monitor naplófájljaiban](https://azure.microsoft.com/services/log-analytics/) összesíti. Az adatgyűjtés után a rendszer külön táblákba rendezi az adatokat Log Analytics munkaterületeken belül minden adattípushoz, ami lehetővé teszi, hogy az összes adatokat együtt elemezze az eredeti forrástól függetlenül. Emellett a Azure Security Center integrálható Azure Monitor naplókkal, így az ügyfelek Kusto-lekérdezéseket használhatnak a biztonsági események adatainak eléréséhez és más szolgáltatásokból származó adatokkal való összekapcsolásához.

Az architektúra részeként az alábbi Azure- [figyelési megoldások](../../monitoring/monitoring-solutions.md) szerepelnek:
-   [Active Directory Assessment](../../azure-monitor/insights/ad-assessment.md): A Active Directory Health-ellenőrzési megoldás rendszeres időközönként kivizsgálja a kiszolgálói környezetek kockázatait és állapotát, és rangsorolja a telepített kiszolgálói infrastruktúrára jellemző ajánlásokat.
- [SQL Assessment](../../azure-monitor/insights/sql-assessment.md): Az SQL Health-ellenőrzési megoldás rendszeres időközönként kivizsgálja a kiszolgálói környezetek kockázatait és állapotát, és az ügyfelek számára az üzembe helyezett kiszolgáló-infrastruktúrára jellemző ajánlások rangsorolt listáját biztosítja.
- [Agent Health](../../monitoring/monitoring-solution-agenthealth.md): Az Agent Health-megoldás jelentést készít, hogy hány ügynök van üzembe helyezve, valamint a földrajzi eloszlásuk, valamint a nem válaszoló ügynökök száma, valamint az operatív adatküldés alatt álló ügynökök száma.
-   [Activity log Analytics](../../azure-monitor/platform/collect-activity-logs.md): Az Activity Log Analytics-megoldás segítséget nyújt az Azure-tevékenységek naplóinak elemzéséhez az ügyfelek összes Azure-előfizetése között.

**Azure Automation**: [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) tárolja, futtatja és felügyeli a runbookok. Ebben a megoldásban a runbookok segítséget nyújt a naplók Azure SQL Databaseból való gyűjtésében. Az Automation [change Tracking](../../automation/change-tracking.md) megoldás lehetővé teszi, hogy az ügyfelek könnyedén azonosíthassák a környezet változásait.

**Azure monitor**: [Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) segíti a felhasználókat a teljesítmény nyomon követésében, a biztonság fenntartásában és a trendek azonosításában azáltal, hogy lehetővé teszi a szervezetek számára, hogy naplózzák, riasztásokat hozzon létre és archiválják az adatok archiválását, beleértve az Azure

**Application Insights**: A [Application Insights](../../azure-monitor/app/app-insights-overview.md) egy bővíthető Application Performance Management szolgáltatás a webes fejlesztők számára több platformon. Application Insights észleli a teljesítménnyel kapcsolatos rendellenességeket, és az ügyfelek használhatják az élő webalkalmazás figyelését. Hatékony elemzési eszközöket tartalmaz, amelyek segítségével az ügyfelek diagnosztizálják a problémákat, és megtudhatják, hogy a felhasználók hogyan használják ténylegesen az alkalmazást. &#39;A megoldás célja, hogy az ügyfelek folyamatosan javítsák a teljesítményt és a használhatóságot.

## <a name="threat-model"></a>Veszélyforrások modellje

Az ehhez a hivatkozási architektúrához tartozó Adatfolyam-diagram [letölthető](https://aka.ms/ffiec-paaswa-tm) , vagy a következő címen érhető el. Ez a modell lehetővé teszi az ügyfeleknek, hogy a módosítások végrehajtása során megértsék a rendszerinfrastruktúra lehetséges kockázatait.

![FFIEC-veszélyforrások modellje: Pásti Web Application](images/ffiec-paaswa-threat-model.png "FFIEC-veszélyforrások modellje: Pásti Web Application")

## <a name="compliance-documentation"></a>Megfelelőségi dokumentáció

A [Azure Security and Compliance Blueprint – FFIEC Customer felelősségi mátrix](https://aka.ms/ffiec-crm) FELSOROLJA a FFIEC által igényelt összes biztonsági célkitűzést. Ez a mátrix részletesen ismerteti, hogy az egyes célok megvalósítása a Microsoft, az ügyfél vagy a kettő közötti megosztás feladata-e.

A [Azure Security and Compliance Blueprint – FFIEC Pásti webalkalmazás-implementációs mátrixa](https://aka.ms/ffiec-paaswa-cim) információt nyújt arról, hogy mely FFIEC-követelményekkel foglalkozik a Pásti webalkalmazási architektúrája, beleértve a részletes leírását is, hogyan történik a a megvalósítás megfelel az egyes érintett célkitűzések követelményeinek.

## <a name="deploy-this-solution"></a>A megoldás üzembe helyezése
Ez az Azure Security and Compliance Blueprint-automatizálás olyan JSON-konfigurációs fájlokból és PowerShell-parancsfájlokból áll, amelyeket a Azure Resource Manager API-szolgáltatása kezel, hogy erőforrásokat helyezzen üzembe az Azure-ban. A részletes üzembe helyezési utasítások [itt](https://aka.ms/ffiec-paaswa-repo)érhetők el.

#### <a name="quickstart"></a>Gyorsútmutató
1. [A GitHub-](https://aka.ms/ffiec-paaswa-repo) tárház klónozása vagy letöltése a helyi munkaállomásra.

2. Tekintse át a 0-Setup-AdministrativeAccountAndPermission.md, és futtassa a megadott parancsokat.

3. Tesztelési megoldás üzembe helyezése contoso-mintaadatok vagy kezdeti éles környezet kipróbálása.
    - 1A-ContosoWebStoreDemoAzureResources.ps1
        - Ez a szkript üzembe helyezi az Azure-erőforrásokat a Webáruház contoso-mintaadatok használatával történő bemutatásához.
    - 1-DeployAndConfigureAzureResources.ps1
        - Ez a szkript üzembe helyezi az Azure-erőforrásokat, amelyek szükségesek az ügyfél által birtokolt webalkalmazások éles környezetének támogatásához. Ezt a környezetet az ügyfélnek továbbra is testre kell szabnia a szervezeti követelmények alapján.

## <a name="guidance-and-recommendations"></a>Útmutatás és javaslatok

### <a name="vpn-and-expressroute"></a>VPN-és ExpressRoute

Biztonságos VPN-alagutat vagy [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) kell konfigurálni, hogy biztonságosan létre lehessen hozni egy kapcsolatot a jelen Pásti webalkalmazás-hivatkozási architektúra részeként üzembe helyezett erőforrásokkal. A VPN-vagy ExpressRoute megfelelő beállításával az ügyfelek hozzáadhatnak egy védelmi réteget az átvitelhez.

A biztonságos VPN-alagút az Azure-nal való megvalósításával létrehozhat egy helyszíni hálózat és egy Azure-Virtual Network közötti virtuális magánhálózati kapcsolatot. Ez a kapcsolat az interneten keresztül történik, és lehetővé teszi, &quot;hogy&quot; az ügyfelek biztonságosan továbbítsák az adatokat egy&#39;titkosított kapcsolaton belül az ügyfél hálózata és az Azure között. A helyek közötti VPN egy olyan biztonságos, érett technológia, amelyet évtizedek óta minden méretben üzembe helyezett a vállalatok. Ebben a beállításban az [IPsec-alagút mód](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) titkosítási mechanizmusként van használatban.

Mivel a VPN-alagúton belüli forgalom egy helyek közötti VPN-kapcsolaton keresztül halad át az interneten, a Microsoft egy másik, még biztonságosabb kapcsolódási lehetőséget kínál. Az Azure ExpressRoute egy dedikált WAN-kapcsolat az Azure és egy helyszíni hely vagy egy Exchange-szolgáltató között. Mivel a ExpressRoute-kapcsolatok nem az interneten keresztül haladnak át, ezek a kapcsolatok megbízhatóbbak, gyorsabbak, kisebb késések és nagyobb biztonságot biztosítanak, mint a szokásos kapcsolatok az interneten keresztül. Továbbá, mivel ez az ügyfél&#39;-távközlési szolgáltató közvetlen kapcsolata, az adatforgalom nem az interneten keresztül történik, ezért nem teszi elérhetővé.

Ajánlott eljárások egy biztonságos hibrid hálózat megvalósításához, amely kiterjeszti a helyszíni hálózatot az Azure- [ra.](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)

## <a name="disclaimer"></a>Jogi nyilatkozat

- Ez a dokumentum csak tájékoztató jellegű. A MICROSOFT NEM VÁLLAL SEMMILYEN KIFEJEZETT, VÉLELMEZETT VAGY TÖRVÉNYES GARANCIÁT A JELEN DOKUMENTUMBAN FOGLALT INFORMÁCIÓK ALAPJÁN. Ez a dokumentum a &quot;következőként van megadva:.&quot; Az ebben a dokumentumban kifejezett információk és nézetek, beleértve az URL-címeket és az internetes webhelyek más hivatkozásait, értesítés nélkül változhatnak. A dokumentumot olvasó ügyfeleink a használatuk kockázatát viselik.
- A jelen dokumentum nem biztosít semmilyen jogot semmilyen Microsoft-termékben vagy-megoldásban található szellemi tulajdonhoz.
- Az ügyfelek belső referenciák szerint másolhatják és használhatják ezt a dokumentumot.
- A jelen dokumentumban szereplő javaslatok megnövelték az adatok, a hálózat vagy a számítási erőforrások használatát az Azure-ban, és növelheti az&#39;ügyfelek Azure-licencét vagy előfizetési költségeit.
- Ez az architektúra arra szolgál, hogy az ügyfelek számára a konkrét követelményekhez igazodva és nem használható éles környezetben.
- Ez a dokumentum hivatkozásként van kifejlesztve, és nem használható az összes olyan eszköz meghatározására, amelyekkel az ügyfél megfelel bizonyos megfelelőségi követelményeknek és előírásoknak. Az ügyfeleknek jóvá kell hagyniuk a szervezeten belüli jogi támogatást a jóváhagyott ügyfél-implementációkban.
