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
ms.openlocfilehash: f79ba9ae60454d4e73c914fc1c8af675a6d07d5d
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2019
ms.locfileid: "57245828"
---
# <a name="azure-security-and-compliance-blueprint---data-analytics-for-nist-sp-800-171"></a>Azure biztonsági és megfelelőségi terv - Data Analytics NIST 800-171-SP rendszerhez

## <a name="overview"></a>Áttekintés
[NIST Special Publication 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) hasznos útmutatást ad a szabályozott nem titkos információ (CUI) nonfederal információs rendszerek és a szervezetek a védelméhez. 800-171 NIST SP hozza létre a 14 termékcsaládok CUI titkosságát védelme kapcsolatos biztonsági követelményeket.

Az Azure biztonsági és megfelelőségi terv segítségével az ügyfelek központi telepítése egy data analytics architektúra az Azure-ban, amely megvalósítja egy részhalmazát NIST SP 800-171 vezérlők útmutatást nyújt. Ez a megoldás bemutatja, amelyben ügyfeleink megfelel bizonyos biztonsági és megfelelőségi követelmények módon. Ez az ügyfelek számára hozhat létre és konfigurálja a saját adatok elemzési megoldásokat az Azure-ban alapjaként is szolgál.

Ez a referenciaarchitektúra társított megvalósítási útmutató és fenyegetések modellezése célja, hogy az ügyfelek számára az adott követelményekhez a felmerülő ismeretekkel szolgál. Nem használható-éles környezetben van. Ez az architektúra módosítás nélküli üzembe helyezése nem elegendő a teljes mértékben a követelményeinek az NIST SP 800-171. Ügyfelek végző megfelelő biztonsági és megfelelőségi értékelést, minden olyan megoldás, amely használja ezt az architektúrát. Követelmények minden ügyfél megvalósítása tulajdonságairól alapján változhat.

## <a name="architecture-diagram-and-components"></a>Architektúra és összetevők
A megoldás olyan analitikai platformhoz, amelyre ügyfelek hozhat létre saját elemzőeszközöket biztosít. A referenciaarchitektúra egy általános alkalmazási helyzet vázolja fel. Ügyfelek használhatják, a bemeneti adatok tömeges adatokat importálja az SQL-adatok rendszergazdája keresztül. Is használhatják, a bemeneti adatok keresztül a egy működési felhasználó operatív adatok frissítései között. Mindkét workstreams tartalmazzák az Azure Functions az adatok importálása az Azure SQL Database-be. Az Azure Functions egyedi az ügyfél analytics követelményeit az importálási feladatok kezeléséhez az Azure Portalon az ügyfélnek kell konfigurálni.

Az Azure kínál a különböző jelentéskészítési és elemzési szolgáltatások, az ügyfél számára. A megoldás keresztül adatokat gyorsan megkeresheti, és így gyorsabban gyorsabb keresztül az adatok okosabb modellezéssel használja az Azure Machine Learning-szolgáltatások és az SQL Database. Machine Learning célja adatkészletek között új kapcsolatok felderítésével lekérdezés sebesség növelése érdekében. Adatok alapesetben be van tanítva, néhány statisztikai függvényeken keresztül. A későbbiekben legfeljebb hét további lekérdezési készletek szinkronizálhatók az azonos táblázatos modellek futó számítási feladatok, és a válaszidők csökkentése érdekében. Az ügyfél-kiszolgáló méretezhetőséget kínál a lekérdezési készletek összesen nyolc.

Bővített analitika és a jelentéskészítési SQL Database úgy konfigurálható, az oszlopcentrikus indexek. Machine Learning és az SQL Database lehet horizontálisan fel vagy le vagy kapcsolja ki teljesen ügyfelek általi használatot adott válaszként. SQL forgalmat titkosítja a rendszer SSL-lel azáltal, önaláírt tanúsítványokat. Ajánlott eljárásként javasoljuk, hogy egy megbízható hitelesítésszolgáltatótól a használatát a fokozott biztonságot.

Adatok feltöltése az SQL Database-adatbázishoz, és a gépi tanulás betanított, miután a van kivonatolt működési felhasználói és a Power BI SQL-adatok rendszergazda által. A Power BI intuitív jeleníti meg az adatokat, és együttesen kéri le adatokat megrajzolásához nagyobb betekintést több adatkészlet között. A Power BI alkalmazkodóképesség magas fokú rendelkezik, és könnyen integrálható az SQL Database. Ügyfelek beállíthatja úgy, hogy saját üzleti igényeinek megfelelően által igényelt forgatókönyvek széles választékának kezelni.

A teljes megoldás az Azure Storage, amely az ügyfelek konfigurálása az Azure Portalról épül. Storage titkosítja az inaktív adatok bizalmas mivoltát a Storage Service Encryption az összes adatot. Georedundáns tárolás biztosítja, hogy az ügyfél elsődleges adatközpont káros eseményt sem adatvesztést eredményez. A másodpéldány egy külön helyen több száz mérfölddel távolabb tárolódik.

A fokozott biztonság érdekében ebben a megoldásban az összes erőforrás egy erőforráscsoportba tartozó Azure Resource Manageren keresztül felügyelt. Az Azure Active Directory (Azure AD) szerepköralapú hozzáférés-vezérlés (RBAC) szabályozható használatos üzembe az erőforrásokat. Ilyen erőforrások többek között a vásárlói kulcsok Azure Key vaultban. A fájlrendszer állapotának az Azure Security Center és az Azure Monitor használatával felügyelik. Ügyfelek konfigurálása mindkét figyelési szolgáltatásokat naplók rögzítésére. A fájlrendszer állapotának, amely egyszerűen használható egyetlen irányítópulton jelenik meg.

SQL Database általában felügyelt, az SQL Server Management Studióban. Egy biztonságos VPN-en vagy egy Azure ExpressRoute-kapcsolaton keresztül az SQL-adatbázis eléréséhez konfigurált helyi gépen futtatja. *Azt javasoljuk, hogy, felügyeleti és az adatok importálása az erőforrás-csoportba tartozó VPN- vagy ExpressRoute-kapcsolat konfigurálása*.

![Adatelemzés a NIST SP 800-171 architekturális diagramja](images/nist171-analytics-architecture.png "Data Analytics a NIST SP 800-171 architekturális diagramja")

Ez a megoldás a következő Azure-szolgáltatásokat használ. További információkért lásd: a [üzembe helyezési architektúrája](#deployment-architecture) szakaszban.

- Application Insights
- Azure Active Directory
- Azure Data Catalog
- Azure Disk Encryption
- Azure Event Grid
- Azure Functions
- Azure Key Vault
- Azure Machine Learning
- Az Azure Monitor (naplók)
- Azure Security Center
- Azure SQL Database
- Azure Storage
- Azure Virtual Network
    - (1) /16 hálózati
    - (2) /24 hálózatok
    - (2) a hálózati biztonsági csoportok
- Power BI-irányítópult

## <a name="deployment-architecture"></a>Üzembe helyezési architektúrája
A következő szakaszt az üzembe helyezés és a megvalósítás elemek részletei.

**Az Azure Event Grid**: A [Event Grid](https://docs.microsoft.com/azure/event-grid/overview), ügyfelek egyszerűen hozhat létre eseményalapú architektúrákkal rendelkező alkalmazások. Az Azure-előfizetés a riasztásoktól szeretnének erőforrás kell kiválasztania. Ezután adnak az eseménykezelő vagy webhook egy végpontot, az eseményt. Ügyfelek gondoskodhat a webhook végpontok lekérdezési paraméterek hozzáadása a webhook URL-CÍMÉT, egy esemény-előfizetés létrehozásához. Event Grid támogatja a csak webhook koncové body protokolu HTTPS. Az Event Grid ügyfelek szabályozhatja a különböző felügyeleti műveletek végrehajtásához a különböző felhasználókhoz megadott hozzáférési szintet. A felhasználók listában esemény-előfizetések, újakat hoz létre, és kulcsok létrehozása. Event Grid Azure RBAC használatával történik.

**Az Azure Functions**: [Az Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) egy kiszolgáló nélküli számítási szolgáltatás, amely igény szerinti futtatja. Nem kell explicit módon infrastruktúrát kiépíteniük vagy kezelniük. Az Azure Functions használatával különféle eseményekre reagálva futtathat szkripteket vagy kódrészleteket.

**Az Azure Machine Learning szolgáltatás**: [Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/) van olyan adatelemzési módszer, amely lehetővé teszi a számítógépek a meglévő adatok jelezheti előre a jövőbeni viselkedéseket, kimeneteket és trendeket.

**Az Azure Data Catalog**: [A Data Catalog](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog) megkönnyíti az adatforrások megtalálhatóvá és értelmezhetővé azon felhasználók számára, aki kezelheti az adatokat. Általános adatforrások regisztrálva, címkézett és adatokat keresni. A data marad, a meglévő hely, de a metaadatok másolatát bekerül a Data Catalog szolgáltatásba. Az adatforrás helyét egy hivatkozást részét képezi. A metaadatok, hogy az egyes adatforrások egyszerűen felderítheti, felvétellel indexelve van. Indexelő is teszi értelmezhetővé teszi a felhasználók számára.

### <a name="virtual-network"></a>Virtuális hálózat
Ez a referenciaarchitektúra egy privát virtuális hálózatot a 10.0.0.0/16 címteret az határozza meg.

**Hálózati biztonsági csoportok**: [Hálózati biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (NSG) tartalmazza a hozzáférés-vezérlési listák, amelyek engedélyezik vagy megtagadják a forgalmat egy virtuális hálózaton belül. Az NSG-ket egy alhálózatot vagy különálló virtuális gép szintjén forgalom védelmére használható. Az alábbi NSG-k léteznek:
  - Egy NSG-t az Active Directory
  - A számítási feladathoz tartozó NSG-t

Az NSG-k mindegyike rendelkezik bizonyos portokat és protokollokat nyissa meg, hogy a megoldás működhet, biztonságos és megfelelően. Emellett a következő konfigurációk engedélyezve vannak az egyes NSG:
  - [Diagnosztikai naplók és események](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) engedélyezve van, és a storage-fiókban tárolt
  - Az Azure Monitor naplóira csatlakozik a [NSG-t a diagnosztika](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Alhálózatok**: Minden egyes alhálózathoz társítva a megfelelő NSG-t.

### <a name="data-in-transit"></a>Az átvitt adatok
Az Azure és az Azure adatközpontok bemenő kommunikáció alapértelmezés szerint titkosítja. Az Azure Portalon keresztül a tárolási tranzakciók HTTPS-kapcsolaton keresztül történik.

### <a name="data-at-rest"></a>Inaktív adat

Az architektúra a titkosítás, az adatbázis naplózási és más intézkedéseket az inaktív adatok védi.

**Az Azure Storage**: A titkosított adatok inaktív állapotban, igényeinek megfelelően minden [tárolási](https://azure.microsoft.com/services/storage/) használ [a Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption). Ez a funkció segít a szervezeti biztonsági kötelezettségeit, és a NIST SP 800-171 által meghatározott megfelelőségi követelmények támogatásához adatok biztonságos megőrzésében.

**Az Azure Disk Encryption**: [Lemeztitkosítás](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) használ a Windows BitLocker funkcióját adatlemezek kötettitkosítását. A megoldás integrálható az Azure Key Vault segítségével szabályozhatja, és kezelhetik a lemeztitkosítási kulcsokat.

**Azure SQL Database**: Az SQL Database-példány a következő adatbázis biztonsági intézkedések használja:
-   [Az Active Directory-hitelesítés és engedélyezés](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) lehetővé teszi az identitáskezelést adatbázis-felhasználók és más Microsoft-szolgáltatások egyetlen központi helyen.
-   [Az SQL database naplózási szolgáltatásával](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) nyomon követi az adatbázisok eseményeit és felvezeti ezeket egy naplófájlba, egy Azure storage-fiókban.
-   SQL-adatbázis használatára van konfigurálva [transzparens adattitkosítás](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Valós idejű titkosítási és visszafejtési az adatbázis, azokhoz kapcsolódó biztonsági mentési és tranzakciós naplófájlokra tárolt adatok inaktív védelme hajtja végre. Transzparens adattitkosítás biztosítja, hogy a tárolt adatok még nem vonatkoznak a jogosulatlan hozzáférést.
-   [Tűzfalszabályok](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) megakadályozzák elérését, adatbázis-kiszolgálók, amíg a megfelelő engedélyekkel. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján.
-   [Az SQL Fenyegetésészlelési](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) lehetővé teszi, hogy az észlelés és válasz a lehetséges veszélyforrásokra azok bekövetkezésekor. Biztonsági riasztások biztosítja a gyanús adatbázis-tevékenységekről, a lehetséges biztonsági résekről, az SQL-injektálási támadások és a rendellenes adatbázis-hozzáférési mintákról.
-   [Titkosított oszlopokat](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) győződjön meg arról, hogy bizalmas adatok soha nem jelenik meg az adatbázis-rendszer egyszerű szövegként. Ha engedélyezett az adattitkosítás, csak az ügyfélalkalmazások vagy alkalmazáskiszolgálók hozzáférést a kulcsokhoz hozzáférhet egyszerű szöveges adatokat.
- [Az SQL Database dinamikus adatmaszkolása](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) által a replikaadatok felhasználók vagy alkalmazások, adatok maszkolása korlátozza a bizalmas adatok. Automatikusan potenciálisan bizalmas adatok felderítéséhez és javasoljuk a alkalmazni lehessen a megfelelő maszkokkal. Dinamikus adatmaszkolás segít hozzáférés korlátozása, így bizalmas adatok nem az adatbázis jogosulatlan hozzáférést. *Be kell tartaniuk az adatbázisséma beállítások hangolását ügyfelek felelőssége.*

### <a name="identity-management"></a>Identitáskezelés
A következő technológiákat az Azure-beli adatokhoz való hozzáférés kezeléséhez képességeket biztosítják:
-   [Az Azure AD](https://azure.microsoft.com/services/active-directory/) van a Microsoft több-bérlős felhőalapú címtár- és identitáskezelési szolgáltatás. Ez a megoldás az összes felhasználó jönnek létre az Azure ad-ben, és az SQL-adatbázishoz hozzáféréssel rendelkező felhasználókat tartalmazzák.
-   Hitelesítés az alkalmazás Azure AD használatával történik. További információkért lásd: hogyan [alkalmazások integrálása az Azure ad-vel](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Az adatbázis oszloptitkosítás is az Azure AD az SQL Database az alkalmazás hitelesítéséhez. További információkért lásd: hogyan [bizalmas adatok védelme az SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Az Azure RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) segítségével a rendszergazdák hozzáférés részletes engedélyeket határozhat meg. Azt, akkor is csak a mértékű hozzáférést biztosítson a felhasználóknak frissíteniük kell a munkája elvégzéséhez. Helyett az Azure-erőforrások minden felhasználó ugyanolyan korlátlan engedélyeket ad, a rendszergazdák engedélyezhetik csak bizonyos adatok eléréséhez szükséges műveleteket. Az előfizetés-rendszergazda előfizetéshez való hozzáférés korlátozódik.
- [Az Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) minimalizálása érdekében a felhasználók száma, akik hozzáférhetnek bizonyos adatokat, például az adatok ügyfelek által használható. A rendszergazdák Azure AD Privileged Identity Management segítségével Fedezze fel, korlátozhatja és emelt szintű identitások és azok erőforrásokhoz való hozzáférésének figyelése. Ez a funkció is segítségével igény szerinti – igény rendszergazdai hozzáférés kényszerítésére.
-   [Az Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) észleli a szervezet identitásait érintő lehetséges biztonsági résekről. Konfigurálja az automatikus válaszok a szervezet identitásait kapcsolódó észlelt gyanús tevékenységek. Azt is kivizsgálja a problémák megoldásához a megfelelő művelet végrehajtása a gyanús események.

### <a name="security"></a>Biztonság
**Titkok kezelése**: A megoldás [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) kulcsok és titkos kulcsok felügyeletéhez. Key Vault segít a védelem titkosítási kulcsok és titkos kódok felhőalapú alkalmazások és szolgáltatások által használt. A következő Key Vault-képességek segítségével az ügyfelek adatok védelme érdekében:
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

### <a name="logging-and-auditing"></a>Naplózás és vizsgálat

Azure-szolgáltatások széles körben system és a felhasználói tevékenység, valamint a rendszerállapot jelentkezzen be:
- **A Tevékenységnaplók**: [A Tevékenységnaplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) adjon meg egy előfizetéshez tartozó erőforrásokon végrehajtott műveletekkel kapcsolatos információk. A Tevékenységnaplók segítségével határozza meg a műveletet kezdeményező, az eseményt, és állapot ideje.
- **Diagnosztikai naplók**: [Diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) minden erőforrás által kibocsátott az összes napló tartalmazza. Ezek a naplók például a Windows rendszer-eseménynaplói, a Storage-naplók, a Key Vault-naplók és az Azure Application Gateway hozzáférés és a tűzfal a naplókat. Az összes diagnosztikai naplók írni egy központosított, titkosított csatornákon történik az Azure storage-fiókját archiválási. Felhasználók is konfigurálhatók a megőrzés időtartamát, legfeljebb 730 napig, az adott igényeknek.

**Az Azure Monitor naplóira**: Naplókat a rendszer összevont [naplózza az Azure Monitor](https://azure.microsoft.com/services/log-analytics/) feldolgozási, tárolására és-irányítópult jelentéseit. Az adatokat a begyűjtésük után azt rendszer adattípusonként külön táblába rendezi az egyes adattípusokhoz Log Analytics-munkaterületek belül. Ily módon az összes adat elemezhetők együtt, az eredeti forrástól függetlenül. A Security Center integrálható az Azure Monitor naplóira. Ügyfelek Kusto-lekérdezés segítségével a biztonsági események adatainak eléréséhez és más szolgáltatások származó adatokat kombinálni.

A következő Azure [figyelési megoldások](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) Ez az architektúra egy része szerepel:
-   [Az Active Directory assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Az Active Directory állapotának ellenőrzése megoldás rendszeres időközönkénti értékeli az a kockázat és kiszolgálói környezetek állapotát. Biztosít a telepített kiszolgálói infrastruktúra vonatkozó javaslatok rangsorolt listáját.
- [SQL-értékeléssel](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): Az SQL Health Check megoldás rendszeres időközönként felméri az a kockázat és kiszolgálói környezetek állapotát. Ügyfelek biztosít a telepített kiszolgálói infrastruktúra vonatkozó javaslatok rangsorolt listáját.
- [Az ügynök állapota](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Az Agent Health megoldás jelentések hány ügynök van telepítve, és a földrajzi eloszlása. Azt is jelentések hány ügynök nem válaszol, és küldje el az operatív adatokat, az ügynökök számát.
-   [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Az Activity Log Analytics megoldás az ügyfél az összes Azure-előfizetések az Azure-Tevékenységnaplók elemzésének segíti.

**Az Azure Automation**: [Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) tárolja, fut, és kezeli a runbookok. Ebben a megoldásban lévő runbookok segítségével naplók gyűjtése az SQL Database-ből. Az ügyfelek használhatják az Automation [Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) megoldás könnyedén azonosíthatja a változtatásokat a környezetben.

**Az Azure Monitor**: [A figyelő](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) segít a felhasználóknak a teljesítmény, a biztonság fenntartására és a trendek azonosítására. Szervezetek használhatják azt a naplózása, létre riasztásokat és archiválhat adatokat. Akkor is nyomon követheti a saját Azure-erőforrások API-hívások.

**Az Application Insights**: [Az Application Insights](https://docs.microsoft.com/azure/application-insights/) egy bővíthető alkalmazásteljesítmény-felügyeleti szolgáltatás webfejlesztőknek, több platformon. Felismeri a teljesítményanomáliákat és a hatékony elemzőeszközöket tartalmaz. Az eszközök segítségével diagnosztizálhatja a problémákat, és segít a felhasználóknak megérteni, hogy felhasználók használják az alkalmazást. Feladata, hogy a felhasználók folyamatosan javíthassa a teljesítményt és a használhatóságot.

## <a name="threat-model"></a>Fenyegetések modellezése

A referenciaarchitektúra az adatfolyam-diagram érhető el az [letöltése](https://aka.ms/nist171-analytics-tm) vagy itt található. Ez a modell segítségével az ügyfelek megismerhetik a pontokat a rendszer infrastruktúra esetleges kockázat, ha azok hajtsa végre a módosításokat.

![Adatelemzés a NIST SP 800-171 fenyegetések modellezése](images/nist171-analytics-threat-model.png "Data Analytics a NIST SP 800-171 fenyegetések modellezése")

## <a name="compliance-documentation"></a>Megfelelőségi dokumentációja
A [Azure biztonsági és megfelelőségi terv - 800-171 NIST SP vevő felelőssége mátrix](https://aka.ms/nist171-crm) NIST SP 800-171 által igényelt összes biztonsági vezérlő sorolja fel. Ez a mátrix részletezi a Microsoft, az ügyfél felelőssége végrehajtására az egyes vezérlőelemek-e, vagy a kettő között megosztott.

A [Azure biztonsági és megfelelőségi terv - NIST SP 800-171 Data Analytics vezérlő megvalósítási mátrix](https://aka.ms/nist171-analytics-cim) melyik NIST SP a 800-171 szabályozza a data analytics architektúra tárgyalt információkkal. Hogyan megvalósítása megfelel-e a követelményeknek, az egyes kezelt vezérlőelemek a részletes leírását tartalmazza.


## <a name="guidance-and-recommendations"></a>Útmutatás és javaslatok
### <a name="vpn-and-expressroute"></a>VPN és ExpressRoute
Biztonságos VPN-alagúton vagy [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) konfigurálni kell, hogy biztonságos kapcsolatot a data analytics referenciaarchitektúra részeként üzembe helyezett erőforrásokkal. Megfelelően állítja be a VPN-t vagy ExpressRoute, az ügyfelek hozzáadhatja egy védelmi réteget biztosít adatokat átvitel közben.

Az Azure-ral biztonságos VPN-alagút alkalmazásával egy helyszíni hálózat és a egy Azure virtuális hálózat közötti virtuális magánhálózati kapcsolat hozható létre. Ezt a kapcsolatot az interneten keresztül történik. Ügyfelek használhatják a kapcsolatot egy titkosított kapcsolatot a hálózat és az Azure között lévő biztonságosan "csatorna" információkat. Site-to-site VPN egy olyan biztonságos, érett technológia, évtizedes a vállalatok által központilag telepített. A [IPsec-alagút módú](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) ezt a beállítást, mint egy titkosítási mechanizmus használatban van.

A VPN-alagút belül adatforgalomra site-to-site VPN-nel az internethez, mert a Microsoft kínál egy másik még biztonságosabb kapcsolat lehetőséget. Az ExpressRoute dedikált WAN Azure-ban és a egy helyszíni helyhez vagy egy Exchange-szolgáltató közötti kapcsolat. Az ExpressRoute-kapcsolatok közvetlenül csatlakozhat az ügyfél távközlési szolgáltató. Ennek következtében az adatok nem az interneten keresztül továbbítani, és nincs kitéve. Ezek a kapcsolatok több megbízhatóság megbízhatóbbak, gyorsabbak, kisebb a késésük, és biztonságosabbak a szokásos internetkapcsolatoknál kínálnak. 

Ajánlott eljárások végrehajtásához egy biztonságos hibrid hálózatot, amely kiterjeszti a helyszíni hálózatot az Azure-bA [elérhető](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

### <a name="extract-transform-load-process"></a>Kinyerési, átalakítási-betöltési folyamat
[A PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) adatok betöltése az SQL Database-be egy külön kinyerési, átalakítási betöltési nélkül vagy eszköz importálása. A PolyBase lehetővé teszi, hogy a T-SQL-lekérdezések adatokhoz való hozzáférését. A Microsoft üzleti intelligencia és elemzés verem és a harmadik felektől származó eszközök, amelyek kompatibilisek az SQL Server a polybase-zel használható.

### <a name="azure-ad-setup"></a>Az Azure AD beállítása
[Az Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) elengedhetetlen a központi telepítés kezelése, és üzembe helyezését a környezetben dolgozhat személyzet a hozzáférést. A helyszíni Active Directory integrálhatók az Azure AD-t [négy kattintással](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Ügyfelek is elősegítsék a telepített Active Directory-infrastruktúrát (tartományvezérlők) az Azure ad-hez. Ehhez végezze el a telepített Active Directory-infrastruktúrát részterületét képezi az Azure AD-erdőben.

## <a name="disclaimer"></a>Jogi nyilatkozat

 - Ez a dokumentum csak tájékoztatási célokat szolgál. A MICROSOFT NEM VÁLLAL SEMMILYEN KIFEJEZETT, KIFEJEZETT, VÉLELMEZETT VAGY KÖTELEZŐ GARANCIÁT A DOKUMENTUMBAN SZEREPLŐ INFORMÁCIÓRA VONATKOZÓAN. Ez a dokumentum biztosított "as-van." Információk és vélemények ebben a dokumentumban URL-CÍMEK és más internetes webhelyekre utaló hivatkozások értesítés nélkül változhatnak. Ez a dokumentum olvasásakor ügyfelek felelősségére használja.
 - Ez a dokumentum nem biztosít semmilyen jogot semmilyen Microsoft-termék vagy a megoldások a szellemi tulajdonhoz ügyfelek.
 - Ügyfelek másolhatja és használhatja ezt a dokumentumot belső, hivatkozási célokból.
 - Bizonyos ajánlások a dokumentum eredményezhet megnövekedett adat-, hálózati vagy számítási erőforrás-használat az Azure-ban, és előfordulhat, hogy növelje az ügyfél Azure licencek vagy előfizetések költségeit.
 - Ez az architektúra célja, hogy az ügyfelek számára az adott követelményekhez beállítása alapjaként szolgálja ki, és nem használható-éles környezetben van.
 - Ez a dokumentum referenciaként fejlesztik, és nem használható minden olyan eszközt, amely szerint egy ügyfél megfelel a megadott megfelelőségi követelmények és előírások meghatározásához. Ügyfelek jóváhagyott megvalósítás az ügyfeleknél a szervezet jogi támogatást kell kérnie.
