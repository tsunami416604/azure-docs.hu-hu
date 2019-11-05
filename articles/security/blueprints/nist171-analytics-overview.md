---
title: Azure Security and Compliance Blueprint – adatelemzés a NIST SP 800-171-hez
description: Azure Security and Compliance Blueprint – adatelemzés a NIST SP 800-171-hez
services: security
author: jomolesk
ms.assetid: ca75d2a8-4e20-489b-9a9f-3a61e74b032d
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: 0bed9f96ce04fae313672f2fa627c2e20bea2f6f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496412"
---
# <a name="azure-security-and-compliance-blueprint---data-analytics-for-nist-sp-800-171"></a>Azure Security and Compliance Blueprint – adatelemzés a NIST SP 800-171-hez

## <a name="overview"></a>Áttekintés
A 800-171-es [speciális kiadvány](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) a nem szövetségi információs rendszerekben és szervezeteknél található ellenőrzött, nem minősített információk (CUI-) védelmére vonatkozó irányelveket biztosít. A NIST SP 800-171 14 olyan biztonsági követelményt hoz létre, amely a CUI titkosságának védelmét védi.

Ez a Azure Security and Compliance Blueprint útmutatást nyújt az ügyfelek számára az Azure-ban olyan adatelemzési architektúra üzembe helyezéséhez, amely a NIST SP 800-171 vezérlők egy részhalmazát valósítja meg. Ez a megoldás azt mutatja be, hogy az ügyfelek milyen módon tudják kielégíteni az egyes biztonsági és megfelelőségi követelményeket. Emellett alapként szolgál az ügyfelek számára saját adatelemzési megoldások létrehozásához és konfigurálásához az Azure-ban.

Ez a hivatkozási architektúra, a társított megvalósítási útmutató és a veszélyforrás-modell arra szolgál, hogy az ügyfelek számára a konkrét követelményekhez alkalmazkodva alapot biztosítson. Nem használhatók éles környezetben. Az architektúra módosítás nélküli üzembe helyezése nem elegendő ahhoz, hogy teljesen megfeleljen a NIST SP 800-171 követelményeinek. Az ügyfelek felelősek az architektúrát használó megoldások megfelelő biztonsági és megfelelőségi felmérésének elvégzéséért. A követelmények az egyes ügyfelek implementációjának sajátosságai alapján változhatnak.

## <a name="architecture-diagram-and-components"></a>Architektúra ábrája és összetevői
Ez a megoldás egy elemzési platformot biztosít, amely alapján az ügyfelek létrehozhatják saját elemzési eszközeiket. A hivatkozási architektúra egy általános használati esetet vázol fel. Az ügyfelek az SQL/adatok rendszergazdája által a tömeges adatimportálások segítségével vihetik be az adatokat. Azt is felhasználhatják, hogy operatív adatokon keresztül adatokat adjanak az adatokhoz egy operatív felhasználó használatával. Mindkét workstreams belefoglalja Azure Functions az adatAzure SQL Databaseba való importáláshoz. A Azure Functionst az ügyfélnek kell konfigurálnia az Azure Portal segítségével, hogy az importálási feladatokat az ügyfél elemzési követelményeinek megfelelően kezelje.

Az Azure számos jelentéskészítési és elemzési szolgáltatást kínál az ügyfelek számára. Ez a megoldás a Azure Machine Learning és SQL Database használatával gyorsan böngészhet az adatkeresésen, és gyorsabb eredményeket biztosít az adatmodellezési szolgáltatásokon keresztül. Machine Learning a lekérdezések sebességének növelésére szolgál az adathalmazok közötti új kapcsolatok felfedezésével. Kezdetben az adatok több statisztikai funkcióval vannak betanítva. Ezt követően akár hét további lekérdezési készlet is szinkronizálható ugyanazzal a táblázatos modellekkel a lekérdezési munkaterhelés elosztása és a válaszadási idő csökkentése érdekében. Az ügyfél-kiszolgáló a lekérdezési készletek teljes számát nyolcra hozza.

A továbbfejlesztett elemzési és jelentéskészítési szolgáltatáshoz a SQL Database is konfigurálható az oszlopok tárolására szolgáló indexekkel. A Machine Learning és az SQL Database az ügyfelek általi használatra adott válaszként felfelé vagy lefelé is méretezhető. Az összes SQL-forgalom SSL-titkosítással rendelkezik az önaláírt tanúsítványok belefoglalásával. Ajánlott eljárásként javasoljuk, hogy használjon megbízható hitelesítésszolgáltatót a fokozott biztonság érdekében.

Miután feltöltötte az SQL Database-be és Machine Learning által betanított adatfeldolgozást, a rendszer az operatív felhasználó és az SQL/adatkezelő Power BI használatával emésztette. Power BI intuitív módon jeleníti meg az adatokat, és több adatkészletben is összegyűjti az információkat, hogy jobban megtekintse a képet. Power BI magas fokú alkalmazkodóképesség, és könnyen integrálható a SQL Database. Az ügyfelek úgy konfigurálhatják, hogy az üzleti igényeknek megfelelő széles skálát kezeljenek.

A teljes megoldás az Azure Storage-ra épül, amelyet az ügyfelek a Azure Portal konfigurálnak. A Storage az összes adat titkosításával titkosítja a Storage Service Encryption az inaktív adatok titkosságának fenntartása érdekében. A Geo-redundáns tárolás biztosítja, hogy az ügyfél elsődleges adatközpontjában a kedvezőtlen esemény nem eredményez adatvesztést. A második példányt a rendszer egy külön helyen tárolja, több száz kilométer távolságban.

A fokozott biztonság érdekében az ebben a megoldásban lévő összes erőforrást erőforráscsoportként kezeli Azure Resource Manageron keresztül. A Azure Active Directory (Azure AD) szerepköralapú hozzáférés-vezérlés (RBAC) használatával szabályozható a telepített erőforrásokhoz való hozzáférés. Ezek az erőforrások a Azure Key Vaultban lévő ügyfelek kulcsait tartalmazzák. A rendszer állapotát Azure Security Center és Azure Monitor figyeli. Az ügyfelek mindkét figyelési szolgáltatást a naplók rögzítésére is konfigurálhatják. A rendszer állapota egyetlen irányítópulton jelenik meg, amely könnyen használható.

SQL Databaset általában a SQL Server Management Studio kezeli. Egy olyan helyi gépről fut, amely az SQL Database biztonságos VPN-en vagy Azure ExpressRoute-kapcsolaton keresztüli elérésére van konfigurálva. *Javasoljuk, hogy konfigurálja a VPN-vagy ExpressRoute-kapcsolatokat a felügyelethez és az adatimportáláshoz az erőforráscsoporthoz*.

![Adatelemzés a NIST SP 800-171 hivatkozási architektúrájának diagramja](images/nist171-analytics-architecture.png "Adatelemzés a NIST SP 800-171 hivatkozási architektúrájának diagramja")

Ez a megoldás az alábbi Azure-szolgáltatásokat használja. További információ: [telepítési architektúra](#deployment-architecture) szakasz.

- Application Insights
- Azure Active Directory
- Azure Data Catalog
- Azure Disk Encryption
- Azure Event Grid
- Azure Functions
- Azure Key Vault
- Azure Machine Learning
- Azure Monitor (naplók)
- Azure Security Center
- Azure SQL Database
- Azure Storage
- Azure Virtual Network
    - (1)/16 hálózat
    - (2)/24 hálózat
    - (2) hálózati biztonsági csoportok
- Power BI-irányítópult

## <a name="deployment-architecture"></a>Üzembe helyezési architektúra
A következő szakasz az üzembe helyezési és megvalósítási elemeket részletezi.

**Azure Event Grid**: a [Event Grid](https://docs.microsoft.com/azure/event-grid/overview)segítségével az ügyfelek könnyedén hozhatnak létre alkalmazásokat eseményvezérelt architektúrákkal. A felhasználók kiválaszthatják azt az Azure-erőforrást, amelyre előfizethetnek. Ezután egy végpontot adnak az eseménykezelőnek vagy a webhooknak, hogy elküldjék az eseményt. Az ügyfelek az esemény-előfizetések létrehozásakor is biztonságossá tehetik a webhook-végpontokat, ha lekérdezési paramétereket adnak hozzá a webhook URL-címéhez. A Event Grid csak a HTTPS-alapú webhook-végpontokat támogatja. A Event Grid segítségével az ügyfelek különböző felügyeleti műveleteket végezhetnek a különböző felhasználók számára megadott hozzáférési szint szabályozásához. A felhasználók listázhatja az esemény-előfizetéseket, újakat hozhat létre, és kulcsokat állíthat elő. Event Grid az Azure RBAC használja.

**Azure functions**: a [Azure functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) egy kiszolgáló nélküli számítási szolgáltatás, amely igény szerinti kódot futtat. Nem kell explicit módon kiépíteni vagy kezelni az infrastruktúrát. Az Azure Functions használatával különféle eseményekre reagálva futtathat szkripteket vagy kódrészleteket.

**Azure Machine learning**: a [Machine learning](https://docs.microsoft.com/azure/machine-learning/service/) egy adatelemzési módszer, amely lehetővé teszi a számítógépek számára a meglévő adatelemzések használatát a jövőbeli viselkedések, eredmények és trendek előrejelzéséhez.

**Azure Data Catalog**: a [Data Catalog](../../data-catalog/overview.md) az adatforrásokat könnyen felderítheti és értelmezheti az adatkezelő felhasználók számára. Az általános adatforrások regisztrálása, címkézése és adatkeresése is lehetséges. Az adatok a meglévő helyükön maradnak, de a metaadatok másolata Data Catalogba kerül. A rendszer az adatforrásra mutató hivatkozást tartalmaz. A metaadatok indexelve vannak, hogy minden adatforrás könnyen felderíthető legyen a keresésen keresztül. Az indexelés azt is lehetővé teszi, hogy a felhasználók megértsék a felderített felhasználókat.

### <a name="virtual-network"></a>Virtuális hálózat
Ez a hivatkozási architektúra a 10.0.0.0/16 címtartomány szerinti magánhálózati virtuális hálózatot definiálja.

**Hálózati biztonsági csoportok**: a [hálózati biztonsági csoportok](../../virtual-network/virtual-network-vnet-plan-design-arm.md) (NSG-EK) olyan hozzáférés-vezérlési listát tartalmaznak, amelyek engedélyezik vagy megtagadják a forgalmat a virtuális hálózaton belül. A NSG használatával biztonságossá teheti a forgalmat egy alhálózaton vagy egy virtuális gép szintjén. A következő NSG léteznek:
  - Active Directory NSG
  - A munkaterhelés NSG

A NSG minden egyes portja és protokollja nyitva van, hogy a megoldás biztonságosan és megfelelően működjön. Emellett a következő konfigurációk is engedélyezve vannak az egyes NSG:
  - A [diagnosztikai naplók és események](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) engedélyezve vannak, és Storage-fiókban tárolódnak
  - Azure Monitor naplók a [NSG diagnosztikaához](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json) csatlakoznak

**Alhálózatok**: minden alhálózat társítva van a hozzá tartozó NSG.

### <a name="data-in-transit"></a>Átvitt adatok
Az Azure alapértelmezés szerint titkosítja az Azure-adatközpontok felé irányuló és onnan érkező összes kommunikációt. A Azure Portal a Storage-ba irányuló összes tranzakció HTTPS-n keresztül történik.

### <a name="data-at-rest"></a>Inaktív adatok

Az architektúra titkosítva, adatbázis-naplózással és egyéb mértékekkel védi a nyugalmi állapotban lévő adatok védelmét.

**Azure Storage**: a titkosított adatokra vonatkozó követelmények teljesítése érdekében az [](https://azure.microsoft.com/services/storage/) összes tárterület [Storage Service encryption](../../storage/common/storage-service-encryption.md)használ. Ez a szolgáltatás segít megvédeni és megőrizni az adatvédelmet a NIST SP 800-171 által meghatározott szervezeti biztonsági kötelezettségvállalások és megfelelőségi követelmények támogatásával.

**Azure Disk Encryption**: a [lemez titkosítása](../azure-security-disk-encryption-overview.md) a Windows BitLocker szolgáltatásával biztosítja az adatlemezek mennyiségi titkosítását. A megoldás integrálva van Azure Key Vaultekkel a lemezes titkosítási kulcsok szabályozása és kezelése érdekében.

**Azure SQL Database**: a SQL Database-példány a következő adatbázis-biztonsági mértékeket használja:
-   [Active Directory a hitelesítés és az engedélyezés](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) lehetővé teszi az adatbázis-felhasználók és más Microsoft-szolgáltatások Identitáskezelés kezelését egy központi helyen.
-   Az [SQL Database naplózása](../../sql-database/sql-database-auditing.md) nyomon követi az adatbázis eseményeit, és egy Azure Storage-fiókban lévő naplóba írja azokat.
-   A SQL Database [transzparens adattitkosítás](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)használatára van konfigurálva. A szolgáltatás valós idejű titkosítást és visszafejtést végez az adatbázis, a társított biztonsági másolatok és a tranzakciós naplófájlok között, hogy megvédje az adatokat a nyugalmi állapotban. Az transzparens adattitkosítás biztosítja, hogy a tárolt adataik nem vonatkoznak a jogosulatlan hozzáférésre.
-   A [Tűzfalszabályok](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) megakadályozzák az adatbázis-kiszolgálók hozzáférését a megfelelő engedélyek megadása előtt. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján.
-   Az [SQL-veszélyforrások észlelése](../../sql-database/sql-database-threat-detection.md) lehetővé teszi az észlelést és a válaszadást a lehetséges fenyegetésekkel szemben. Biztonsági riasztásokat biztosít a gyanús adatbázis-tevékenységekről, a lehetséges sebezhetőségekről, az SQL-injektálási támadásokról, valamint a rendellenes adatbázis-hozzáférési mintákról.
-   A [titkosított oszlopok](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) biztosítják, hogy a bizalmas adatok soha nem egyszerű szövegként jelenjenek meg az adatbázis-rendszeren belül. Az adattitkosítás engedélyezése után csak a kulcsokhoz hozzáféréssel rendelkező ügyfélalkalmazások vagy kiszolgálóalkalmazások férhetnek hozzá az egyszerű szöveges adatértékekhez.
- [SQL Database a dinamikus adatmaszkolás](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) korlátozza a bizalmas adatok megjelenítését azáltal, hogy az adatokat nem Kiemelt felhasználók vagy alkalmazások számára fedi le. A szolgáltatás automatikusan képes felderíteni a potenciálisan bizalmas adatokat, és javaslatot tesz a megfelelő maszkok alkalmazására. A dinamikus adatmaszkolás segít csökkenteni a hozzáférést, hogy a bizalmas adatok jogosulatlan hozzáférés nélkül ne lépjenek ki az adatbázisból. *Az ügyfelek felelősek a beállítások módosításához az adatbázis-sémájuk betartásához.*

### <a name="identity-management"></a>Identitáskezelés
Az alábbi technológiák az Azure-környezetben tárolt adathozzáférések kezelésére szolgáló képességeket biztosítanak:
-   Az [Azure ad](https://azure.microsoft.com/services/active-directory/) a Microsoft több-bérlős felhőalapú címtár-és Identitáskezelés-kezelő szolgáltatása. Az ehhez a megoldáshoz tartozó összes felhasználó létrejön az Azure AD-ben, és tartalmazza a SQL Databasehoz hozzáférő felhasználókat.
-   Az alkalmazáshoz való hitelesítés az Azure AD használatával történik. További információ: [alkalmazások integrálása az Azure ad-vel](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Az adatbázis oszlopának titkosítása az Azure AD-t is használja az alkalmazás SQL Database való hitelesítéséhez. További információ: a [bizalmas adatok védelme SQL Databaseban](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   Az [Azure RBAC](../../role-based-access-control/role-assignments-portal.md) segítségével a rendszergazdák részletes hozzáférési engedélyeket adhatnak meg. Ezzel csak azt a hozzáférést biztosíthatják, ameddig a felhasználóknak el kell végezniük a munkájukat. Ahelyett, hogy az összes felhasználó számára korlátlan engedélyeket adna az Azure-erőforrásokhoz, a rendszergazdák csak bizonyos műveleteket végezhetnek el az adatokhoz való hozzáféréshez. Az előfizetés-hozzáférés az előfizetés rendszergazdájára korlátozódik.
- A [Azure Active Directory Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) az ügyfelek használhatják a bizonyos információkhoz (például adatokhoz) hozzáférő felhasználók számának minimalizálására. A rendszergazdák Azure AD Privileged Identity Management használhatják az emelt szintű identitások felderítését, korlátozását és figyelését, valamint az erőforrásokhoz való hozzáférésüket. Ez a funkció az igény szerinti, igény szerinti rendszergazdai hozzáférés biztosítására is használható, ha szükséges.
-   [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) észleli a szervezet identitásait érintő lehetséges biztonsági réseket. Automatikus válaszokat konfigurál a szervezet identitásával kapcsolatos gyanús műveletek észlelésére. Emellett a gyanús incidenseket is megvizsgálja, hogy megfelelő lépéseket tegyenek a megoldásához.

### <a name="security"></a>Biztonság
**Titkok kezelése**: a megoldás [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) használ a kulcsok és titkok kezeléséhez. Key Vault segít megőrizni a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsokat és titkokat. Az alábbi Key Vault-képességek segítenek az ügyfeleknek az adatvédelemben:
- A speciális hozzáférési szabályzatok a szükséges módon vannak konfigurálva.
- Key Vault hozzáférési házirendek minimálisan szükséges engedélyekkel vannak definiálva a kulcsokhoz és a titkokhoz.
- Key Vault összes kulcsának és titkának lejárati dátuma van.
- A Key Vault összes kulcsát speciális hardveres biztonsági modulok védik. A kulcs típusa egy hardveres biztonsági modul által védett 2048 bites RSA-kulcs.
- Minden felhasználó és identitás minimálisan szükséges engedélyeket kap a RBAC használatával.
- Key Vault diagnosztikai naplói legalább 365 napos megőrzési időtartammal engedélyezettek.
- A kulcsok számára engedélyezett titkosítási műveletek csak a szükségesek.

**Azure Security Center**: a [Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)segítségével az ügyfelek központilag alkalmazhatják és kezelhetik a biztonsági házirendeket a számítási feladatokban, korlátozhatja a fenyegetéseket, és észlelheti és reagálhat a támadásokra. Security Center az Azure-szolgáltatások meglévő konfigurációit is elérheti, hogy konfigurációs és szolgáltatási javaslatokat nyújtson a biztonsági helyzetek és az adatvédelem javításához.

 Security Center különböző észlelési képességekkel figyelmezteti az ügyfeleket a környezetét célba vevő lehetséges támadásokra. Ezek a riasztások értékes információkat tartalmaznak arról, hogy mi váltotta ki a riasztást, valamint a támadás forrásáról és az általa célba vett erőforrásokról. A Security Center [előre meghatározott biztonsági riasztásokat](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) tartalmaz, amelyek akkor aktiválódnak, ha fenyegetés vagy gyanús tevékenység zajlik. Az ügyfelek [Egyéni riasztási szabályok](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) segítségével meghatározhatnak új biztonsági riasztásokat a környezetből már összegyűjtött adatok alapján.

 A Security Center rangsorolt biztonsági riasztásokat és incidenseket biztosít. Security Center megkönnyíti az ügyfelek számára a potenciális biztonsági problémák felderítését és megoldását. Minden észlelt fenyegetéshez létrejön egy [fenyegetési intelligencia jelentés](https://docs.microsoft.com/azure/security-center/security-center-threat-report) . Az incidensek válaszait használó csapatok a jelentéseket a fenyegetések kivizsgálására és szervizelésére használhatják.

### <a name="logging-and-auditing"></a>Naplózás

Az Azure-szolgáltatások széles körben naplózzák a rendszer és a felhasználó tevékenységét, valamint a rendszer állapotát:
- **Tevékenységnaplók**: a [Tevékenységnaplók](../../azure-monitor/platform/activity-logs-overview.md) betekintést nyújtanak az előfizetésben lévő erőforrásokon végrehajtott műveletekre. A Tevékenységnaplók segítenek meghatározni a művelet kezdeményezőjét, az előfordulás időpontját és az állapotot.
- **Diagnosztikai naplók**: a [diagnosztikai naplók](../../azure-monitor/platform/resource-logs-overview.md) a minden erőforrás által kibocsátott összes naplót tartalmazzák. Ezek a naplók a Windows-eseménynaplókat, a tárolási naplókat, a Key Vault a naplókat, valamint az Azure Application Gateway hozzáférés-és tűzfal-naplókat tartalmazzák. Az összes diagnosztikai napló egy központi és titkosított Azure Storage-fiókba írja az archiválást. A felhasználók a megőrzési időtartamot akár 730 napig is konfigurálhatják, hogy megfeleljenek a rájuk vonatkozó követelményeknek.

**Azure monitor naplók**: a rendszer a naplókat a feldolgozás, tárolás és irányítópult-jelentéskészítés [Azure monitor naplófájljaiban](https://azure.microsoft.com/services/log-analytics/) összesíti. Az adatgyűjtés után a rendszer külön táblákba rendezi az adattípusokat Log Analytics munkaterületeken belül. Így az összes adatforrás együtt is elemezhető, az eredeti forrásától függetlenül. A Security Center Azure Monitor-naplókkal integrálódik. Az ügyfelek használhatnak Kusto-lekérdezéseket a biztonsági események adatainak eléréséhez és más szolgáltatásokból származó adatokkal való összekapcsolásához.

Az architektúra részeként az alábbi Azure- [figyelési megoldások](../../monitoring/monitoring-solutions.md) szerepelnek:
-   [Active Directory Assessment](../../azure-monitor/insights/ad-assessment.md): az Active Directory Health ellenőrzési megoldás rendszeres időközönként értékeli a kiszolgálói környezetek kockázatait és állapotát. A szolgáltatás a központilag telepített kiszolgálói infrastruktúrára vonatkozó javaslatok rangsorolt listáját tartalmazza.
- [SQL Assessment](../../azure-monitor/insights/sql-assessment.md): az SQL Health-ellenőrzési megoldás rendszeres időközönként értékeli a kiszolgálói környezetek kockázatait és állapotát. Az ügyfelek számára az üzembe helyezett kiszolgálói infrastruktúrára jellemző ajánlások rangsorolt listáját kínálja.
- [Agent Health](../../monitoring/monitoring-solution-agenthealth.md): a Agent Health-megoldás azt jelenti, hogy hány ügynök van üzembe helyezve és a földrajzi eloszlása. Azt is jelenti, hogy hány ügynök nem válaszol, és az operatív adatküldő ügynökök száma.
-   [Activity log Analytics](../../azure-monitor/platform/collect-activity-logs.md): a Activity log Analytics megoldás segítséget nyújt az Azure-tevékenységek naplóinak elemzésében az ügyfelekhez tartozó összes Azure-előfizetésen belül.

**Azure Automation**: az [Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) tárolja, futtatja és felügyeli a runbookok. Ebben a megoldásban a runbookok segítséget nyújt a naplók SQL Databaseból való gyűjtésében. Az Automation [change Tracking](../../automation/change-tracking.md) megoldás segítségével könnyedén azonosíthatja a környezet változásait.

**Azure monitor**: a [monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) segítségével a felhasználók nyomon követhetik a teljesítményt, kezelhetik a biztonságot, és azonosíthatják a trendeket. A szervezetek felhasználhatják a naplózásra, a riasztások létrehozására és az adatok archiválására. Emellett az Azure-erőforrásokban is nyomon követhetik az API-hívásokat.

**Application Insights**: a [Application Insights](https://docs.microsoft.com/azure/application-insights/) egy bővíthető Application Performance Management szolgáltatás a webes fejlesztők számára több platformon. Észleli a teljesítménnyel kapcsolatos rendellenességeket, és hatékony elemzési eszközöket tartalmaz. Az eszközök segítenek diagnosztizálni a problémákat, és segítenek az ügyfeleknek megérteni, hogy mit csinálnak a felhasználók az alkalmazással. A szolgáltatás úgy lett kialakítva, hogy a felhasználók folyamatosan javítsák a teljesítményt és a használhatóságot.

## <a name="threat-model"></a>Veszélyforrások modellje

A viszonyítási architektúra adatáramlási diagramja [letölthető](https://aka.ms/nist171-analytics-tm) , vagy itt található. Ez a modell segítheti az ügyfeleket abban, hogy megértsék a rendszerinfrastruktúra lehetséges kockázatait, amikor módosításokat végeznek.

![Adatelemzés a NIST SP 800-171 Threat modelhez](images/nist171-analytics-threat-model.png "Adatelemzés a NIST SP 800-171 Threat modelhez")

## <a name="compliance-documentation"></a>Megfelelőségi dokumentáció
A [Azure Security and Compliance Blueprint-NIST sp 800-171 Customer felelősségi mátrix](https://aka.ms/nist171-crm) FELSOROLJA a NIST SP 800-171 által igényelt összes biztonsági ellenőrzést. Ez a mátrix részletesen ismerteti, hogy az egyes vezérlőelemek megvalósítása a Microsoft, az ügyfél vagy a kettő közötti megosztás feladata-e.

Az [Azure Security and Compliance Blueprint-NIST sp 800-171 adatelemzési vezérlő megvalósítási mátrixa](https://aka.ms/nist171-analytics-cim) információt nyújt arról, hogy mely NIST SP 800-171-vezérlőkkel foglalkozik az adatelemzési architektúra. Részletes leírást tartalmaz arról, hogy a megvalósítás hogyan teljesíti az egyes kezelt vezérlők követelményeit.


## <a name="guidance-and-recommendations"></a>Útmutatás és javaslatok
### <a name="vpn-and-expressroute"></a>VPN-és ExpressRoute
A biztonságos VPN-alagutat vagy [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) úgy kell konfigurálni, hogy biztonságosan létesítsen kapcsolatot az adatelemzési hivatkozási architektúra részeként üzembe helyezett erőforrásokkal. A VPN-vagy ExpressRoute megfelelő beállításával az ügyfelek hozzáadhatnak egy védelmi réteget az átvitelhez.

A biztonságos VPN-alagút az Azure-nal való megvalósításával létrehozhat egy virtuális magánhálózati kapcsolatot egy helyszíni hálózat és egy Azure-beli virtuális hálózat között. Ez a kapcsolat az interneten keresztül zajlik. Az ügyfelek a kapcsolatot a hálózat és az Azure közötti titkosított kapcsolaton belül biztonságosan "alagút" információkkal használhatják. A helyek közötti VPN egy olyan biztonságos, érett technológia, amelyet évtizedek óta minden méretben üzembe helyezett a vállalatok. Ebben a beállításban az [IPsec-alagút mód](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) titkosítási mechanizmusként van használatban.

Mivel a VPN-alagúton belüli forgalom helyek közötti VPN-kapcsolaton keresztül halad át az interneten, a Microsoft még egy biztonságosabb kapcsolódási lehetőséget kínál. A ExpressRoute egy dedikált WAN-kapcsolat az Azure és egy helyszíni hely vagy egy Exchange-szolgáltató között. A ExpressRoute-kapcsolatok közvetlenül az ügyfél távközlési szolgáltatójának csatlakoznak. Ennek eredményeképpen az adat nem utazik az interneten keresztül, és nem teszi elérhetővé. Ezek a kapcsolatok nagyobb megbízhatóságot, gyorsabb sebességet, kisebb késést és nagyobb biztonságot nyújtanak, mint a szokásos kapcsolatok. 

Ajánlott eljárások egy biztonságos hibrid hálózat megvalósításához, amely kiterjeszti a helyszíni hálózatot az Azure- [ra.](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)

### <a name="extract-transform-load-process"></a>Kinyerés – átalakítás – betöltési folyamat
A kinyert adatok betölthetők SQL Databaseba anélkül, hogy külön kinyerési, átalakítási vagy importálási eszközt [kellene használnia](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) . A Base lehetővé teszi, hogy a T-SQL-lekérdezéseken keresztül hozzáférjen az adatokhoz. A Microsoft Business Intelligence and Analysis stack és a külső gyártótól származó eszközök, amelyek kompatibilisek a SQL Serverekkel, a Base használatával használhatók.

### <a name="azure-ad-setup"></a>Azure AD-telepítés
Az [Azure ad](../../active-directory/fundamentals/active-directory-whatis.md) elengedhetetlen az üzembe helyezés kezeléséhez és a környezettel kommunikáló személyzethez való hozzáféréshez. A helyszíni Active Directory [négy kattintással](../../active-directory/hybrid/how-to-connect-install-express.md)integrálható az Azure ad-vel. Az ügyfelek az üzembe helyezett Active Directory infrastruktúrát (tartományvezérlőket) az Azure AD-be is tudják kötni. Ehhez a központilag telepített Active Directory infrastruktúra egy Azure AD-erdő altartománya legyen.

## <a name="disclaimer"></a>Jogi nyilatkozat

 - Ez a dokumentum csak tájékoztató jellegű. A MICROSOFT NEM VÁLLAL SEMMILYEN KIFEJEZETT, VÉLELMEZETT VAGY TÖRVÉNYES GARANCIÁT A JELEN DOKUMENTUMBAN FOGLALT INFORMÁCIÓK ALAPJÁN. Ez a dokumentum "aktuálisként" van megadva. Az ebben a dokumentumban kifejezett információk és nézetek, beleértve az URL-címeket és az internetes webhelyek más hivatkozásait, értesítés nélkül változhatnak. A dokumentumot olvasó ügyfeleink a használatuk kockázatát viselik.
 - A jelen dokumentum nem biztosít semmilyen jogot semmilyen Microsoft-termékben vagy-megoldásban található szellemi tulajdonhoz.
 - Az ügyfelek belső referenciák szerint másolhatják és használhatják ezt a dokumentumot.
 - A jelen dokumentumban szereplő javaslatok megnövelték az adatok, a hálózat vagy a számítási erőforrások használatát az Azure-ban, és növelhetik az ügyfelek Azure-licencét vagy előfizetési költségeit.
 - Ez az architektúra arra szolgál, hogy az ügyfelek számára a konkrét követelményekhez igazodva és nem használható éles környezetben.
 - Ez a dokumentum hivatkozásként van kifejlesztve, és nem használható az összes olyan eszköz meghatározására, amelyekkel az ügyfél megfelel bizonyos megfelelőségi követelményeknek és előírásoknak. Az ügyfeleknek jóvá kell hagyniuk a szervezeten belüli jogi támogatást a jóváhagyott ügyfél-implementációkban.
