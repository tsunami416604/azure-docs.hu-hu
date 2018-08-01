---
title: Azure biztonsági és megfelelőségi terv - Data Analytics NIST 800-171-SP rendszerhez
description: Azure biztonsági és megfelelőségi terv - Data Analytics NIST 800-171-SP rendszerhez
services: security
author: jomolesk
ms.assetid: ca75d2a8-4e20-489b-9a9f-3a61e74b032d
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: b2c96509b2b8fd61c1814d5b4a015048eeb1807d
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2018
ms.locfileid: "39391787"
---
# <a name="azure-security-and-compliance-blueprint---data-analytics-for-nist-sp-800-171"></a>Azure biztonsági és megfelelőségi terv - Data Analytics NIST 800-171-SP rendszerhez

## <a name="overview"></a>Áttekintés
[NIST Special Publication 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) hasznos útmutatást ad a szabályozott nem titkos információ (CUI) nonfederal információs rendszerek és a szervezetek a védelméhez. 800-171 NIST SP hozza létre a 14 termékcsaládok CUI titkosságát védelme kapcsolatos biztonsági követelményeket.

Az Azure biztonsági és megfelelőségi terv segítségével az ügyfelek központi telepítése egy data analytics architektúra az Azure-ban, amely megvalósítja egy részhalmazát NIST SP 800-171 vezérlők útmutatást nyújt. Ebben a megoldásban, amelyben ügyfeleink megfelel bizonyos biztonsági és megfelelőségi követelmények módszereket mutatja be, és az ügyfelek számára hozhat létre és konfigurálja a saját adatok elemzési megoldásokat az Azure-ban alapjaként szolgál.

Ez a referenciaarchitektúra társított megvalósítási útmutató és fenyegetések modellezése célja, hogy az ügyfelek számára az adott követelményekhez a felmerülő ismeretekkel szolgál, és nem használható – éles környezetben van. Ez az architektúra módosítás nélküli üzembe helyezése nem elegendő a teljes mértékben a követelményeinek az NIST SP 800-171. Ügyfelek végző megfelelő biztonsági és megfelelőségi értékelést bármely megoldás használatával létrehozott ebben az architektúrában követelmények eltérőek lehetnek, hogy milyen ügyről van minden ügyfél megvalósítása alapján.

## <a name="architecture-diagram-and-components"></a>Architektúra és összetevők
A megoldás olyan analitikai platformhoz, amelyre ügyfelek hozhat létre saját elemzőeszközöket biztosít. A referenciaarchitektúra bemutatja egy általános használati eset, ahol az ügyfelek bemeneti adatok tömeges adatokat importálja az SQL-adatok felügyeleti vagy működési adatok frissítések keresztül a egy működési felhasználói keresztül. Mindkét workstreams tartalmazzák az Azure Functions az adatok importálása az Azure SQL Database-be. Az Azure Functions egyedi az ügyfél analytics követelményeit az importálási feladatok kezeléséhez az Azure Portalon az ügyfélnek kell konfigurálni.

Az Azure kínál a különböző jelentéskészítési és elemzési szolgáltatások, az ügyfél; Ez a megoldás azonban magában foglalja az Azure Machine Learning services, Azure SQL Database keresztül adatokat gyorsan megkeresheti, és így gyorsabban gyorsabb keresztül az adatok okosabb modellezéssel együtt. Az Azure Machine Learninget az egy képernyő, a gépi tanulás lekérdezés megbízhatóbbak növelő adatkészletek között új kapcsolatok derítse fel. Miután az adatok révén több statisztikai függvényekkel rendelkezik betanítva, legfeljebb 7 további lekérdezési készletek (beleértve az ügyfél-kiszolgáló teljes 8) szinkronizálhatók az azonos táblázatos modellek futó számítási feladatok, és a válaszidők csökkentése érdekében.

Bővített analitika és a jelentéskészítés az Azure SQL Database úgy konfigurálható, az oszlopcentrikus indexek. Az Azure Machine Learning és az Azure SQL Database lehet horizontálisan fel vagy le vagy kapcsolja ki teljesen ügyfelek általi használatot adott válaszként. SQL forgalmat titkosítja a rendszer SSL-lel azáltal, önaláírt tanúsítványokat. Ajánlott eljárásként az Azure a fokozott biztonság egy megbízható hitelesítésszolgáltatótól használatát javasolja.

Adatok feltöltése az Azure SQL Database-adatbázishoz, és a szerint az Azure Machine Learning betanított, mind a működési felhasználó kivonatolt és a rendszergazda SQL-adatok a Power BI-jal. A Power BI intuitív jeleníti meg az adatokat, és együttesen kéri le adatokat megrajzolásához nagyobb betekintést több adatkészlet között. A magas fokú alkalmazkodóképesség és könnyen integrálható az Azure SQL Database biztosítja, hogy a felhasználók beállíthatják, hogy kezelni a saját üzleti igényeinek megfelelően által kért forgatókönyvek széles választékának.

A teljes megoldás az Azure Storage, amely az ügyfelek konfigurálása az Azure Portalról épül. Az Azure Storage titkosítja az inaktív adatok bizalmas mivoltát a Storage Service Encryption az összes adatot. Földrajzi zónaredundáns tárolás biztosítja, hogy, hogy az ügyfél elsődleges adatközpont káros eseményt nem eredményez az adat, mert a másodpéldány lesz tárolva egy külön helyen több száz mérfölddel távolabb.

A fokozott biztonság érdekében ebben a megoldásban az összes erőforrás egy erőforráscsoportba tartozó Azure Resource Manageren keresztül felügyelt. Az Azure Active Directory szerepköralapú hozzáférés-vezérlés való hozzáférés szabályozása használatos üzembe helyezett erőforrások, például a kulcsok Azure Key vaultban. A fájlrendszer állapotának az Azure Security Center és az Azure Monitor használatával felügyelik. Ügyfelek konfigurálása mindkét figyelési szolgáltatásokat naplók rögzítése és a fájlrendszer állapotának megjelenítése egyetlen, könnyen navigálható-irányítópulton.

Az Azure SQL Database úgy konfigurálva, hogy az Azure SQL Database egy biztonságos VPN- vagy ExpressRoute-kapcsolaton keresztüli elérésére a helyi gépen futó SQL Server Management Studio gyakran kezeli. **Az erőforráscsoport importálása a felügyeleti és az adatok egy VPN- vagy ExpressRoute-kapcsolat beállítása a Microsoft javasolja**.

![Adatelemzés a NIST SP 800-171 architekturális diagramja](images/nist171-analytics-architecture.png "Data Analytics a NIST SP 800-171 architekturális diagramja")

Ez a megoldás a következő Azure-szolgáltatásokat használ. További részleteket a [üzembe helyezési architektúrája](#deployment-architecture) szakaszban.

- Application Insights
- Azure Active Directory
- Azure Data Catalog
- Azure Disk Encryption
- Azure Event Grid
- Azure Functions
- Azure Key Vault
- Azure Machine Learning
- Azure Monitor
- Azure Security Center
- Azure SQL Database
- Azure Storage
- Azure Log Analytics
- Azure Virtual Network
    - (1) /16 hálózati
    - (2) /24 hálózatok
    - (2) a hálózati biztonsági csoportok
- Power BI-irányítópult

## <a name="deployment-architecture"></a>Üzembe helyezési architektúrája
A következő szakaszt az üzembe helyezés és a megvalósítás elemek részletei.

**Az Azure Event Grid**: [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview) lehetővé teszi a felhasználóknak, amellyel egyszerűen készíthet az eseményalapú architektúrákkal rendelkező alkalmazások. Az Azure-előfizetés, és adjon meg az eseménykezelő vagy egy végpontot az eseményt a webhook szeretnék erőforrás kell kiválasztania. Ügyfelek gondoskodhat a webhook végpontok lekérdezési paraméterek hozzáadása a webhook URL-CÍMÉT, egy esemény-előfizetés létrehozásakor. Az Azure Event Grid csak koncové body protokolu HTTPS webhook támogat. Az Azure Event Grid lehetővé teszi, hogy az ügyfelek számára a különböző felügyeleti műveleteket, például az esemény-előfizetések listája, újakat hoz létre, és hozzon létre kulcsokat, a különböző felhasználókhoz megadott hozzáférési szintet szabályozza. Event Grid révén az Azure szerepköralapú hozzáférés-vezérlés.

**Az Azure Functions**: [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) egy kiszolgáló nélküli számítási szolgáltatás, amely lehetővé teszi a felhasználók igény szerinti explicit kiépítésére vagy infrastruktúra kezelése nélkül futtathat. Az Azure Functions használatával különféle eseményekre reagálva futtathat szkripteket vagy kódrészleteket.

**Az Azure Machine Learning**: [Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/preview/) van olyan adatelemzési módszer, amely lehetővé teszi a számítógépek a meglévő adatok jelezheti előre a jövőbeni viselkedéseket, kimeneteket és trendeket.

**Az Azure Data Catalog**: [a Data Catalog](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog) teszi az adatforrásokat könnyen felfedezhetővé és értelmezhetővé teszi azon felhasználók számára, aki kezelheti az adatokat. Általános adatforrások regisztrálva, címkézett és adatokat keresni. A data marad, a meglévő hely, de a metaadatok másolatát bekerül a Data Catalog szolgáltatást, az adatforrás helye mutató hivatkozásokkal együtt. A metaadatok indexelésének köszönhetően az adatforrások egy egyszerű keresés által felfedezhetővé és könnyen értelmezhetővé válnak a felhasználók számára.

### <a name="virtual-network"></a>Virtuális hálózat
Ez a referenciaarchitektúra egy privát virtuális hálózatot a 10.0.0.0/16 címteret az határozza meg.

**Hálózati biztonsági csoportok**: [hálózati biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) tartalmazza a hozzáférés-vezérlési listák, amelyek engedélyezik vagy megtagadják a forgalmat egy virtuális hálózaton belül. Hálózati biztonsági csoportok segítségével biztonságos forgalom egy alhálózatot vagy különálló virtuális gép szintjén. Létezik a következő hálózati biztonsági csoportok:
  - Az Active Directory hálózati biztonsági csoport
  - A számítási feladathoz tartozó hálózati biztonsági csoport

A hálózati biztonsági csoportok rendelkezik bizonyos portokat és protokollokat nyissa meg, hogy a megoldás működhet, biztonságos és megfelelően. Emellett a következő konfigurációk engedélyezve vannak az egyes hálózati biztonsági csoport:
  - [Diagnosztikai naplók és események](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) engedélyezve van, és a storage-fiókban tárolt
  - A log Analytics csatlakozik a [hálózati biztonsági csoport diagnosztikai](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Alhálózatok**: minden egyes alhálózat a megfelelő hálózati biztonsági csoport társítva.

### <a name="data-in-transit"></a>Az átvitt adatok
Az Azure és az Azure adatközpontok bemenő kommunikáció alapértelmezés szerint titkosítja. Az Azure Portalon az Azure Storage összes tranzakció HTTPS-kapcsolaton keresztül történik.

### <a name="data-at-rest"></a>Az inaktív adatok

Az architektúra a titkosítás, az adatbázis naplózási és más intézkedéseket az inaktív adatok védi.

**Az Azure Storage**: rest-követelményeket, a titkosított adatok kielégítése érdekében minden [Azure Storage](https://azure.microsoft.com/services/storage/) használ [a Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption).  Ez segít a szervezeti biztonsági kötelezettségeit, és a NIST SP 800-171 által meghatározott megfelelőségi követelmények támogatásához adatok biztonságos megőrzésében.

**Az Azure Disk Encryption**: [az Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) az adatlemezek kötettitkosítását adja meg a Windows BitLocker funkcióját használja. A megoldás integrálható az Azure Key Vault segítségével szabályozhatja, és kezelhetik a lemeztitkosítási kulcsokat.

**Az Azure SQL Database**: az Azure SQL Database-példányt használja a következő adatbázis biztonsági intézkedéseket:
-   [AD-hitelesítés és engedélyezés](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) lehetővé teszi az identitáskezelést adatbázis-felhasználók és más Microsoft-szolgáltatások egyetlen központi helyen.
-   [Az SQL database naplózási szolgáltatásával](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) nyomon követi az adatbázisok eseményeit és felvezeti ezeket egy naplófájlba, egy Azure storage-fiókban.
-   Az Azure SQL Database használatára van konfigurálva [transzparens adattitkosítás](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), melyik teljesít valós időben titkosítja és fejti vissza az adatbázist, az azokhoz kapcsolódó biztonsági mentési, és a rest-tranzakciós naplófájlokat: adatok védelme érdekében. Transzparens adattitkosítás biztosítja, hogy a tárolt adatok nem lett bárki hozzáférhet.
-   [Tűzfalszabályok](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) megakadályozzák elérését, adatbázis-kiszolgálók, amíg a megfelelő engedélyekkel. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján.
-   [Az SQL Fenyegetésészlelési](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) lehetővé teszi, hogy az észlelés és válasz a lehetséges veszélyforrásokra, bekövetkezésük pillanatában észlelhessék a biztonsági riasztásokat a gyanús adatbázis-tevékenységekről, a lehetséges biztonsági résekről, az SQL-injektálási támadások és a rendellenes adatbázis-hozzáférés minták.
-   [Titkosított oszlopokat](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) győződjön meg arról, hogy bizalmas adatok soha nem jelenik meg, mint belül az adatbázis-rendszer egyszerű szövegként. Adatok titkosításának engedélyezése után csak az ügyfélalkalmazások vagy alkalmazáskiszolgálók hozzáférést a kulcsokhoz hozzáférhet egyszerű szöveges adatokat.
- [Az SQL Database dinamikus adatmaszkolása](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) korlátozza a bizalmas adatok maszkolása az adatokat nem kiemelt jogosultságú felhasználók vagy alkalmazások által. Dinamikus adatmaszkolás automatikusan potenciálisan bizalmas adatok felderítéséhez, és javasolja a alkalmazni lehessen a megfelelő maszkokkal. Ez csökkenti a hozzáférés úgy, hogy a bizalmas adatok nem létezik az adatbázis jogosulatlan hozzáférést az nyújtanak segítséget. Dinamikus adatmaszkolási beállítások igazodnia kell az adatbázis-séma módosításával ügyfelek felelőssége.

### <a name="identity-management"></a>Identitáskezelés
A következő technológiákat az Azure-beli adatokhoz való hozzáférés kezeléséhez képességeket biztosítják:
-   [Az Azure Active Directory](https://azure.microsoft.com/services/active-directory/) van a Microsoft több-bérlős felhőalapú címtár- és identitáskezelési szolgáltatás. Ez a megoldás az összes felhasználó jönnek létre az Azure Active Directoryban, beleértve az Azure SQL Database hozzáférő felhasználók.
-   Az alkalmazás-hitelesítést az Azure Active Directory használatával történik. További információkért lásd: [alkalmazások integrálása az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Ezenkívül az adatbázis oszloptitkosítás hitelesítheti az alkalmazást az Azure SQL Database, az Azure Active Directory. További információkért lásd: hogyan [bizalmas adatok védelme az SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) lehetővé teszi a rendszergazdák számára, hogy a felhasználóknak frissíteniük kell a munkája elvégzéséhez csak olyan mértékű hozzáférést biztosítson a minden részletre kiterjedő hozzáférési engedélyek megadása. Helyett az Azure-erőforrások minden felhasználó ugyanolyan korlátlan engedélyeket ad, a rendszergazdák engedélyezhetik csak bizonyos adatok eléréséhez szükséges műveleteket. Az előfizetés-rendszergazda előfizetéshez való hozzáférés korlátozódik.
- [Az Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) lehetővé teszi az ügyfeleknek minimalizálása érdekében a felhasználóknak a számát, akik hozzáférhetnek bizonyos adatokat, például az adatok a.  Az Azure Active Directory Privileged Identity Management rendszergazdái a felderítését, korlátozását és emelt szintű identitások és azok erőforrásokhoz való hozzáférésének figyelése. Ez a funkció is használható igény szerinti – igény rendszergazdai hozzáférés kényszerítésére.
-   [Az Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) észleli a szervezet identitásait érintő lehetséges biztonsági résekről, konfigurálja az automatikus reakciók, a szervezet identitásait kapcsolatos észlelt gyanús tevékenységeket és folytat gyanús incidensek a megfelelő műveletet a problémák megoldásához.

### <a name="security"></a>Biztonság
**Titkok kezelése**: A megoldás [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) kulcsok és titkos kulcsok felügyeletéhez. Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. Az alábbi Azure Key Vault-képességek segítségével az adatok védelme ügyfelek:
- Speciális hozzáférési szabályzatok kell alapon vannak konfigurálva.
- A Key Vault hozzáférési szabályzatok a minimálisan szükséges engedélyeket kulcsok és titkos kulcsok vannak meghatározva.
- Összes kulcsot és titkos kulcsok a Key Vaultban van lejárati dátumát.
- Speciális hardveres biztonsági modulok által védett összes kulcsok a Key Vaultban. A kulcs típusa a hardveres biztonsági modulok védett 2048-bites RSA-kulcs.
- Minden felhasználó és identitások kapnak a szerepköralapú hozzáférés-vezérlés használatával minimálisan szükséges engedélyeket.
- Diagnosztikai naplók a Key vault legalább 365 napos megőrzési idővel rendelkező engedélyezve vannak.
- A szükséges kapcsolatok engedélyezett titkosítási műveletek kulcsok korlátozódnak.

**Az Azure Security Center**: A [az Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), ügyfelek is központilag alkalmazása és a számítási feladatok biztonsági házirendek kezelése, korlátozhatja a fenyegetéseknek való kitettséget, felismeri és elháríthatja a támadásokat. Az Azure Security Center ezenkívül meglévő konfigurációk az Azure-szolgáltatások konfigurációs és szolgáltatási javaslatok javíthatja biztonsági helyzetét és adatok védelme érdekében fér hozzá.

Az Azure Security Center észlelési képességek széles használatával ügyfeleket a környezetük célzó lehetséges támadások esetén. Ezek a riasztások értékes információkat tartalmaznak arról, hogy mi váltotta ki a riasztást, valamint a támadás forrásáról és az általa célba vett erőforrásokról. Az Azure Security Center készletével rendelkezik [biztonsági riasztások az előre meghatározott](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), amely vannak aktiválódik, ha a fenyegetések vagy gyanús tevékenységek. [Egyéni riasztási szabályok](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) az Azure Security Center lehetővé teszi ügyfeleink számára a környezetből már begyűjtött adatok alapján új biztonsági riasztásokat definiálhat.

Az Azure Security Center itt rangsorolt biztonsági riasztások és incidensek, így egyszerűbb a vásárlók megtalálhatják és a potenciális biztonsági problémákat. A [fenyegetésfelderítési jelentés](https://docs.microsoft.com/azure/security-center/security-center-threat-report) jön létre a rendszer minden egyes észlelt fenyegetés incidensmegoldási csapat segítség vizsgálatában és védekezhet a fenyegetések.

### <a name="logging-and-auditing"></a>Naplózás és vizsgálat

Azure-szolgáltatások széles körben system és a felhasználói tevékenység, valamint a rendszerállapot jelentkezzen be:
- **A Tevékenységnaplók**: [tevékenységeket tartalmazó naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) adjon meg egy előfizetéshez tartozó erőforrásokon végrehajtott műveletekkel kapcsolatos információk. A Tevékenységnaplók segítségével határozza meg a műveletet kezdeményező, az eseményt, és állapot ideje.
- **Diagnosztikai naplók**: [diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) minden erőforrás által kibocsátott az összes napló tartalmazza. Ezek a naplók például a Windows rendszer-eseménynaplói, az Azure Storage-naplók, a Key Vault-naplók és az Application Gateway hozzáférés és a tűzfal a naplókat. Az összes diagnosztikai naplók írni egy központosított, titkosított csatornákon történik az Azure storage-fiókját archiválási. A megőrzési felhasználó által konfigurálható, mentése és 730 nap között, a megőrzési a szervezet konkrét követelményeinek.

**Log Analytics**: ezeket a naplókat a rendszer összevont [Log Analytics](https://azure.microsoft.com/services/log-analytics/) feldolgozási, tárolására és-irányítópult jelentéseit. Követően az adatok felépítéséről különböző adattípusokhoz belül az Operations Management Suite-munkaterületet, amely lehetővé teszi az adatok együttes elemzését az eredeti forrástól függetlenül, külön táblába. Ezen túlmenően az Azure Security Center integrálható a Log Analytics ami lehetővé teszi az ügyfelek számára a Log Analytics-lekérdezések használata a biztonsági események adatainak eléréséhez és más szolgáltatások származó adatokat kombinálni.

A következő Log Analytics [felügyeleti megoldások](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) Ez az architektúra egy része szerepel:
-   [Az Active Directory Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): az Active Directory állapot-ellenőrzés megoldás a kockázat és kiszolgálói környezetek állapotát értékeli a rendszeres időközönkénti, és a telepített kiszolgálói infrastruktúra vonatkozó javaslatok rangsorolt listáját tartalmazza.
- [SQL-értékeléssel](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): az SQL Health Check megoldás a kockázat és kiszolgálói környezetek állapotát értékeli a rendszeres időközönkénti, és nyújt a felhasználók számára a telepített kiszolgálói infrastruktúra vonatkozó javaslatok rangsorolt listáját.
- [Az ügynök állapota](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): az Agent Health megoldás hány ügynök van telepítve, és a földrajzi elosztás, valamint hány ügynök, amely nem válaszol és működési adatokat küld be, amely az ügynökök számát jelenti.
-   [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): az ügyfél az összes Azure-előfizetések az Azure-Tevékenységnaplók elemzésének segíti az Activity Log Analytics megoldás.

**Az Azure Automation**: [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) tárolja, fut, és kezeli a runbookok. Ebben a megoldásban lévő runbookok segítségével naplók gyűjtése az Azure SQL Database-ből. Az Automation [Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) megoldás lehetővé teszi, hogy az ügyfelek számára, hogy könnyen azonosíthassa a változtatásokat a környezetben.

**Az Azure Monitor**: [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) segít a felhasználóknak a teljesítmény, biztonság fenntartására és a trendek azonosítására által lehetővé teszi a cégeknek naplózása, riasztásokat hozhat létre, és archiválja az adatokat, többek között API-hívások nyomon követése az Azure-ban az erőforrásokat.

**Az Application Insights**: [Application Insights](https://docs.microsoft.com/azure/application-insights/) egy bővíthető alkalmazásteljesítmény-felügyeleti szolgáltatás webfejlesztőknek, több platformon. Felismeri a teljesítményanomáliákat és a problémák diagnosztizálásához és megérteni a felhasználók mire az alkalmazás hatékony elemzőeszközöket tartalmaz. Feladata, hogy a felhasználók folyamatosan javíthassa a teljesítményt és a használhatóságot.

## <a name="threat-model"></a>Fenyegetések modellezése

A referenciaarchitektúra az adatfolyam-diagram érhető el az [letöltése](https://aka.ms/nist171-analytics-tm) vagy alább találja. Ez a modell segítségével az ügyfelek megismerhetik a pontokat a rendszer infrastruktúra esetleges kockázat, ha a végzett módosítások.

![Adatelemzés a NIST SP 800-171 fenyegetések modellezése](images/nist171-analytics-threat-model.png "Data Analytics a NIST SP 800-171 fenyegetések modellezése")

## <a name="compliance-documentation"></a>Megfelelőségi dokumentációja
A [Azure biztonsági és megfelelőségi terv - 800-171 NIST SP vevő felelőssége mátrix](https://aka.ms/nist171-crm) NIST SP 800-171 által igényelt összes biztonsági vezérlő sorolja fel. Ez a mátrix részletezi a Microsoft, az ügyfél felelőssége végrehajtására az egyes vezérlőelemek-e, vagy a kettő között megosztott.

A [Azure biztonsági és megfelelőségi terv - NIST SP 800-171 Data Analytics vezérlő megvalósítási mátrix](https://aka.ms/nist171-analytics-cim) melyik NIST SP a 800-171 vezérlők címzett által a data analytics architektúra biztosít többek között Hogyan végrehajtása megfelel egyes az érintett vezérlő részletes leírása.


## <a name="guidance-and-recommendations"></a>Útmutatás és javaslatok
### <a name="vpn-and-expressroute"></a>VPN és ExpressRoute
Biztonságos VPN-alagúton vagy [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) kell konfigurálni kell az ezeket az adatokat egy részeként üzembe helyezett erőforrások kapcsolatot létesíteni a biztonságos elemzési referencia architektúra. Megfelelően állítja be a VPN-t vagy ExpressRoute, az ügyfelek hozzáadhatja egy védelmi réteget biztosít adatokat átvitel közben.

Az Azure-ral biztonságos VPN-alagút alkalmazásával egy helyszíni hálózat és a egy Azure virtuális hálózat közötti virtuális magánhálózati kapcsolat hozható létre. Ezt a kapcsolatot az interneten keresztül történik, és lehetővé teszi az ügyfelek számára, hogy egy titkosított kapcsolatot, az ügyfél hálózati és az Azure között lévő biztonságosan "csatorna" információkat. Site-to-site VPN egy olyan biztonságos, érett technológia, évtizedes a vállalatok által központilag telepített. A [IPsec-alagút módú](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) ezt a beállítást, mint egy titkosítási mechanizmus használatban van.

Forgalom a VPN-alagút haladnak át a helyek közötti VPN-nel az interneten, mert a Microsoft kínál egy másik, még biztonságosabb kapcsolat lehetőséget. Az Azure ExpressRoute dedikált WAN Azure-ban és a egy helyszíni helyhez vagy egy Exchange-szolgáltató közötti kapcsolat. Az ExpressRoute-kapcsolatok az interneten keresztül halad, mivel a kapcsolatok az interneten keresztül ajánlja fel a további megbízhatóság megbízhatóbbak, gyorsabbak, kisebb a késésük, és biztonságosabbak a szokásos internetkapcsolatoknál. Továbbá mivel ez az ügyfél távközlési szolgáltató közvetlen kapcsolatot, az adatok nem az interneten keresztül továbbítani tartalmaz, és ezért nem érhető el.

Ajánlott eljárások végrehajtásához egy biztonságos hibrid hálózatot, amely kiterjeszti a helyszíni hálózatot az Azure-bA [elérhető](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

### <a name="extract-transform-load-process"></a>Kinyerési, átalakítási-betöltési folyamat
[A PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) is az adatok betöltése az Azure SQL Database-be nélkül egy külön van szükség a kinyerési, átalakítási, betölteni vagy importálni eszköz. A PolyBase lehetővé teszi, hogy a T-SQL-lekérdezések adatokhoz való hozzáférését. A Microsoft üzleti intelligencia és elemzés verem, valamint kompatibilis SQL Server, a harmadik felektől származó eszközök a polybase-zel használható.

### <a name="azure-active-directory-setup"></a>Az Azure Active Directory beállítása
[Az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) elengedhetetlen a központi telepítés kezelése és kiépítése a környezet implementálására csoporthoz való hozzáférést. Egy meglévő Windows Server Active Directory integrálhatók az Azure Active Directory- [négy kattintással](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Ügyfelek egy meglévő Azure Active Directory elősegítsék a telepített Active Directory-infrastruktúrát (tartományvezérlők) is, azáltal, hogy a telepített Active Directory-infrastruktúrát az Azure Active Directory-erdő egy altartományt.

## <a name="disclaimer"></a>Jogi nyilatkozat

 - Ez a dokumentum csak tájékoztatási célokat szolgál. A MICROSOFT NEM VÁLLAL SEMMILYEN KIFEJEZETT, KIFEJEZETT, VÉLELMEZETT VAGY KÖTELEZŐ GARANCIÁT A DOKUMENTUMBAN SZEREPLŐ INFORMÁCIÓRA VONATKOZÓAN. Ez a dokumentum biztosított "as-van." Információk és vélemények ebben a dokumentumban URL-CÍMEK és más internetes webhelyekre utaló hivatkozások értesítés nélkül változhatnak. Ez a dokumentum olvasásakor ügyfelek felelősségére használja.
 - Ez a dokumentum nem biztosít semmilyen jogot semmilyen Microsoft-termék vagy a megoldások a szellemi tulajdonhoz ügyfelek.
 - Ügyfelek másolhatja és használhatja ezt a dokumentumot belső, hivatkozási célokból.
 - Bizonyos ajánlások a dokumentum eredményezhet megnövekedett adat-, hálózati vagy számítási erőforrás-használat az Azure-ban, és előfordulhat, hogy növelje az ügyfél Azure licencek vagy előfizetések költségeit.
 - Ez az architektúra célja, hogy az ügyfelek számára az adott követelményekhez beállítása alapjaként szolgálja ki, és nem használható-éles környezetben van.
 - Ez a dokumentum referenciaként fejlesztik, és nem használható minden olyan eszközt, amely szerint egy ügyfél megfelel a megadott megfelelőségi követelmények és előírások meghatározásához. Ügyfelek jóváhagyott megvalósítás az ügyfeleknél a szervezet jogi támogatást kell kérnie.
