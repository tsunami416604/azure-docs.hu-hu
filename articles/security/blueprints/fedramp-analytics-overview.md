---
title: Azure biztonsági és megfelelőségi terv - Analytics fedramp-hoz
description: Azure biztonsági és megfelelőségi terv - Analytics fedramp-hoz
services: security
author: jomolesk
ms.assetid: b4675715-668e-4557-9c1c-698aabf62ceb
ms.service: security
ms.topic: article
ms.date: 05/02/2018
ms.author: jomolesk
ms.openlocfilehash: fa10ff14bf893c268d6b6b1a0d181d11a3f27dc4
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57451875"
---
# <a name="azure-security-and-compliance-blueprint-analytics-for-fedramp"></a>Azure biztonsági és megfelelőségi terv: FedRAMP elemzés

## <a name="overview"></a>Áttekintés

A [Federal Risk and Authorization Management Program (FedRAMP)](https://www.fedramp.gov/) egy Egyesült államokbeli közigazgatásra kiterjedő program, amely a biztonság értékeléséhez, engedélyezési és folyamatos monitorozással szabványos módszert biztosít a felhőhöz termékek és szolgáltatások. Az Azure biztonsági és megfelelőségi terv nyújt útmutatást, hogy a Microsoft Azure analitikai architektúra, amely segít megvalósítani egy magas szintű FedRAMP vezérlőelemeket részét. Ez a megoldás telepítését és konfigurálását az Azure-erőforrások általános referenciaarchitektúra, bemutatásához, amelyben ügyfeleink megfelel bizonyos biztonsági és megfelelőségi követelmények módon útmutatást nyújt, és ismeretekkel szolgál az ügyfelek számára hozhat létre, és állítsa be a saját elemzési megoldásokat az Azure-ban.

Ez a referenciaarchitektúra, társított vezérlőelem végrehajtási útmutatói és enyhítési célja, hogy az ügyfelek számára az adott követelményekhez beállítása alapjaként szolgálja ki, és nem használható-éles környezetben van. Erre a környezetre, módosítás nélkül az alkalmazás üzembe helyezése nem elegendő a teljesen felel meg a magas szintű FedRAMP-alapkonfiguráció követelményeinek. Vegye figyelembe a következőket:
- Az architektúra egy alapkonfiguráció segítenek az ügyfeleknek a számítási feladatok üzembe helyezéséhez az Azure FedRAMP-kompatibilis módon biztosít.
- Ügyfelek végző megfelelő biztonsági és megfelelőségi értékelést bármely megoldás használatával létrehozott ebben az architektúrában követelmények eltérőek lehetnek, hogy milyen ügyről van minden ügyfél megvalósítása alapján.

## <a name="architecture-diagram-and-components"></a>Architektúra és összetevők

A megoldás olyan analitikai platformhoz, amelyre ügyfelek hozhat létre saját elemzőeszközöket biztosít. A referenciaarchitektúra bemutatja egy általános használati eset, ahol az ügyfelek bemeneti adatok tömeges adatokat importálja az SQL-adatok felügyeleti vagy működési adatok frissítések keresztül a egy működési felhasználói keresztül. Mindkét működik Streamek tartalmazzák [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) az adatok importálása az SQL Database-be. Az Azure Functions be kell állítani az ügyfél az Azure Portalon, az importálás tevékenységekkel, minden ügyfél saját egyedi analytics követelményeinek.

A Microsoft Azure kínál a különböző jelentéskészítési és elemzési szolgáltatások, az ügyfél; Ez a megoldás azonban együtt az Azure SQL Database keresztül adatokat gyorsan megkeresheti, és gyorsabban eredményeket keresztül ügyféladatok okosabb modellezéssel tartalmazza az Azure Analysis Services. Az Azure elemzési szolgáltatások az egy képernyő, a gépi tanulás lekérdezés megbízhatóbbak növelő adatkészletek között új kapcsolatok derítse fel. Miután az adatok révén több statisztikai függvényekkel rendelkezik betanítva, legfeljebb 7 további lekérdezési készletek (beleértve az ügyfél-kiszolgáló teljes 8) szinkronizálhatók az azonos táblázatos modellek futó számítási feladatok, és a válaszidők csökkentése érdekében.

Bővített analitika és a jelentéskészítési SQL-adatbázisok is konfigurálhatók, az oszlopcentrikus indexek. SQL-adatbázisok és az Azure elemzési szolgáltatások lehet horizontálisan fel vagy le vagy kapcsolja ki teljesen ügyfelek általi használatot adott válaszként. SQL forgalmat titkosítja a rendszer SSL-lel azáltal, önaláírt tanúsítványokat. Ajánlott eljárásként az Azure a fokozott biztonság egy megbízható hitelesítésszolgáltatótól használatát javasolja.

Adatok feltöltése az Azure SQL Database és az Azure Analysis Services betanított, ha azt a működési felhasználói és a Power BI-jal SQL-adatok rendszergazdai van kivonatolt. A Power BI intuitív jeleníti meg az adatokat, és együttesen kéri le adatokat megrajzolásához nagyobb betekintést több adatkészlet között. A magas fokú alkalmazkodóképesség és könnyen integrálható az Azure SQL Database biztosítja, hogy a felhasználók beállíthatják, hogy kezelni a saját üzleti igényeinek megfelelően által kért forgatókönyvek széles választékának.

A teljes megoldás egy Azure Storage, amely fiók ügyfelek konfigurálása az Azure Portalról épül. Az Azure Storage titkosítja az inaktív adatok bizalmas mivoltát a Storage Service Encryption az összes adatot.  Földrajzi Georedundáns tárolás (GRS) biztosítja, hogy az ügyfél elsődleges adatközpont káros eseményt nem eredményez az adat, mert a másodpéldány lesz tárolva egy külön helyen több száz mérfölddel távolabb.

A fokozott biztonság érdekében ez az architektúra kezeli az Azure Active Directory és az Azure Key Vault-erőforrások. A fájlrendszer állapotának Azure monitoron keresztül figyel. Ügyfelek konfigurálása mindkét figyelési szolgáltatásokat naplók rögzítése és a fájlrendszer állapotának megjelenítése egyetlen, könnyen navigálható-irányítópulton.

Az Azure SQL Database – SQL Server Management Studio (SSMS), egy eléréséhez az Azure SQL Database egy biztonságos VPN- vagy ExpressRoute-kapcsolaton keresztül konfigurált helyi gépen futtató gyakran kezelik. **Azure azt javasolja, hogy a VPN- vagy Azure ExpressRoute kapcsolat a felügyeleti és az adatok importálása a referencia architektúra erőforrás-csoportba.**

![A FedRAMP architekturális diagramja Analytics](images/fedramp-analytics-reference-architecture.png?raw=true "elemzések a FedRAMP architekturális diagramja")

### <a name="roles"></a>Szerepkörök
Az analytics tervezet ismerteti egy általános felhasználói három típusú forgatókönyv: a működési felhasználó, az SQL-adatok rendszergazdai és a Rendszermérnök. Az Azure szerepköralapú hozzáférés-vezérlés (RBAC) lehetővé teszi, hogy a beépített egyéni szerepkörök keresztül pontos kezelési megvalósítását. Erőforrások elérhetők a konfigurálás [szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) és tagolása és végrehajtási [előre definiált szerepkörök](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles).

#### <a name="systems-engineer"></a>Rendszermérnök
A Rendszermérnök az ügyfél Azure-előfizetés tulajdonosa, és konfigurálja a megoldás üzembe helyezése az Azure-portálon keresztül.

#### <a name="sqldata-administrator"></a>SQL-adatok rendszergazda
Az SQL-adatok rendszergazda létrehozza a tömeges adatok importálása és a működési adatok frissítési függvény feltöltése az Azure SQL database a. Az SQL-adatok rendszergazda nem felelős az adatbázis operatív adatok frissítéseket, de fogja tudni megtekinteni az adatokat a Power bi-ban.

#### <a name="operational-user"></a>Működési felhasználó
A működési felhasználó rendszeresen frissíti az adatokat, és a napi szintű adatok generálása tulajdonosa. A működési felhasználó is fog értelmezni az eredmények Power bi-ban.

### <a name="azure-services"></a>Azure-szolgáltatások

Ez a megoldás a következő Azure-szolgáltatásokat használ. Az üzembe helyezési architektúra részletei a [üzembe helyezési architektúrája](#deployment-architecture) szakaszban.
- Azure Functions
- Azure SQL Database
- Azure Analysis Service
- Azure Active Directory
- Azure Key Vault
- Az Azure Monitor (naplók)
- Azure Storage
- Az ExpressRoute/VPN-átjáró
- Power BI-irányítópult

## <a name="deployment-architecture"></a>Üzembe helyezési architektúrája
A következő szakasz az fejlesztése és megvalósítása elemek részletei.

![helyettesítő szöveg](images/fedramp-analytics-components.png?raw=true "elemzések a FedRAMP összetevőinek diagramja")

**Az Azure Functions**: [Az Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) megoldásai apró kódokat a felhőben futó, szinte bármelyik programozási nyelvével keresztül. Ebben a megoldásban függvények integrálása az automatikus elküldésének vásárlói adatokat a felhőbe megoldássablonként, más Azure-szolgáltatásokkal való integráció az Azure Storage. Függvények könnyen skálázható, és csak költségkezelési amikor futnak.

**Az Azure Analysis Service**: [Az Azure Analysis Service](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) biztosít a vállalati adatmodellezési és -integráció az Azure adatplatform-szolgáltatásaival. Az Azure Analysis Service felgyorsítja a nagy mennyiségű adat böngészés kombinálásával a több forrásból származó adatokat egy adatmodellbe.

**Power BI**: [Power bi-ban](https://docs.microsoft.com/power-bi/service-azure-and-power-bi) biztosít elemzési és jelentéskészítési funkciókat biztosít az ügyfelek nagyobb betekintést kívül adatfeldolgozási erőfeszítéseit lekéréses próbál.

### <a name="networking"></a>Hálózat
**Hálózati biztonsági csoportok**: [Az NSG-k](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) vannak beállítva, a telepített erőforrások és szolgáltatások felé irányuló forgalom kezelésére. Hálózati biztonsági csoportok vannak beállítva, hogy egy megtagadási alapértelmezés szerinti sémát, és csak engedélyezi a forgalmat, amely tartalmazza a az előre konfigurált hozzáférés-vezérlési lista (ACL).

Az NSG-k mindegyike rendelkezik az adott portokhoz és protokollokhoz meg, hogy a megoldás működhet, biztonságos és megfelelően. Emellett a következő konfigurációk engedélyezve vannak az egyes NSG:
  - [Diagnosztikai naplók és események](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) engedélyezve van, és a storage-fiókban tárolt
  - [Az Azure Monitor naplóira](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics) diagnosztikai naplók az NSG-hez csatlakozik.

### <a name="data-at-rest"></a>Inaktív adat
Az architektúra a titkosítás, az adatbázis naplózási és más intézkedéseket az inaktív adatok védi.

**Az adatreplikáció** Azure Government rendelkezik a két lehetőség [adatreplikáció](https://docs.microsoft.com/azure/storage/common/storage-redundancy):
 - Az alapértelmezett beállítás az adatreplikációt a **Georedundáns tárolást (GRS)**, amely aszinkron módon tárolja az ügyféladatokat kívül az elsődleges régióban egy különálló adatközpontban. Ez biztosítja, hogy az adat egy teljes elvesztésétől esemény az elsődleges adatközpont.
 - **Helyileg redundáns tárolás (LRS)** keresztül az Azure Storage-fiók is konfigurálható. LRS replikálja az adatokat lévő tárolóskálázási egységben, amely ugyanabban a régióban, amelyben az ügyfél hoz létre a fiók-ban. Az összes van replikálja az adatokat egyidejűleg, biztosítva, hogy egy elsődleges tárolási skálázási egység hibája nincs biztonsági mentési adatok elvesznek.

**Az Azure Storage** titkosított adatok a rest-követelmények teljesítéséhez, minden szolgáltatás a referencia architektúra használja ki az üzembe helyezett [Azure Storage](https://azure.microsoft.com/services/storage/), amely tárolja az adatokat a [a Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption).

**Az Azure Disk Encryption**
[az Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) az operációs rendszer és az adatlemezek kötettitkosítást biztosít a Windows BitLocker funkcióját használja. A megoldás integrálható az Azure Key Vault segítségével szabályozhatja, és kezelhetik a lemeztitkosítási kulcsokat.

**Az Azure SQL Database** az Azure SQL Database-példányt használja a következő adatbázis biztonsági intézkedéseket:
-   [AD-hitelesítés és engedélyezés](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) lehetővé teszi az identitáskezelést adatbázis-felhasználók és más Microsoft-szolgáltatások egyetlen központi helyen.
-   [Az SQL database naplózási szolgáltatásával](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) nyomon követi az adatbázisok eseményeit és felvezeti ezeket egy naplófájlba, egy Azure storage-fiókban.
-   SQL-adatbázis használatára van konfigurálva [transzparens adattitkosítási (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), amely végrehajtja az adatok inaktív védelme adathoz és naplófájlhoz valós idejű titkosításához és visszafejtéséhez. TDE biztosítja, hogy a tárolt adatok nem lett bárki hozzáférhet.
-   [Tűzfalszabályok](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) megakadályozzák elérését, adatbázis-kiszolgálók, amíg a megfelelő engedélyekkel. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján.
-   [Az SQL Fenyegetésészlelési](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) lehetővé teszi, hogy az észlelés és válasz a lehetséges veszélyforrásokra, bekövetkezésük pillanatában észlelhessék a biztonsági riasztásokat a gyanús adatbázis-tevékenységekről, a lehetséges biztonsági résekről, az SQL-injektálási támadások és a rendellenes adatbázis-hozzáférés minták.
-   [Always Encrypted az oszlopok](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) győződjön meg arról, hogy bizalmas adatok soha nem jelenik meg, mint belül az adatbázis-rendszer egyszerű szövegként. Adatok titkosításának engedélyezése után csak az ügyfélalkalmazások vagy alkalmazáskiszolgálók hozzáférést a kulcsokhoz hozzáférhet egyszerű szöveges adatokat.
-   [Az SQL Database dinamikus adatmaszkolása](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) teheti, miután a referenciaarchitektúra üzembe helyezte. Ügyfelek módosítania kell a dinamikus adatmaszkolási beállítások be kell tartaniuk az adatbázissémát.

### <a name="logging-and-audit"></a>Naplózás és naplózása
[Az Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started) hoz létre egy teljes megjelenített figyelési adatok, ideértve a tevékenységeket tartalmazó naplók, metrikák és diagnosztikai adatokat, így az ügyfelek hozhat létre a rendszer állapotának átfogó képet.  
[Az Azure Monitor naplóira](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) biztosít széles körű naplózását, a rendszer és a felhasználói tevékenységek, valamint a helyrendszer állapotát. Azt gyűjti és elemzi az adatokat az Azure-erőforrások által létrehozott és a helyszíni környezetekben.
- **A Tevékenységnaplók**: [A Tevékenységnaplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) adjon meg egy előfizetéshez tartozó erőforrásokon végrehajtott műveletekkel kapcsolatos információk.
- **Diagnosztikai naplók**: [Diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) minden erőforrás által kibocsátott az összes napló tartalmazza. Ezek a naplók a Windows rendszer-eseménynaplói és az Azure Blob storage, táblák és üzenetsor-naplók tartalmazzák.
- **Tűzfal-naplók**: Az Application Gateway teljes diagnosztikai biztosít, és hozzáférni a naplókhoz. Az Application Gateway WAF-kompatibilis erőforrások tűzfalnaplók érhetők el.
- **Napló Archiválás**: Az összes diagnosztikai naplók írni egy központosított, titkosított csatornákon történik az Azure storage-fiókját archiválási 2 nap meghatározott megőrzési idővel rendelkező. Ezek a naplók csatlakozhat az Azure Monitor naplóira feldolgozásához, tárolásához és-irányítópult jelentéseit.

Ezenkívül a következő figyelési megoldások jelennek meg, ez az architektúra részeként:
-   [Az Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): Az Azure Automation-megoldás tárolja, fut, és kezeli a runbookok.
-   [Biztonság és auditálás](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): A biztonsági és auditálási irányítópultja az erőforrások biztonsági állapotát egy magas szintű betekintést nyújt a metrikák azáltal, hogy a biztonsági tartományok, a jelentős problémák, a észlelések, a fenyegetésekkel kapcsolatos Tudásbázis és a gyakori biztonsági lekérdezések.
-   [SQL-értékeléssel](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): Az SQL Health Check megoldás a kockázat és kiszolgálói környezetek állapotát értékeli a rendszeres időközönkénti, és nyújt a felhasználók számára a telepített kiszolgálói infrastruktúra vonatkozó javaslatok rangsorolt listáját.
-   [Azure-Tevékenységnaplók](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Az Activity Log Analytics megoldás az ügyfél az összes Azure-előfizetések az Azure-Tevékenységnaplók elemzésének segíti.

### <a name="identity-management"></a>Identitáskezelés
-   Az alkalmazás-hitelesítést az Azure AD használatával történik. További információkért lásd: [alkalmazások integrálása az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Ezenkívül az adatbázis-oszlop titkosítás az Azure AD segítségével hitelesítheti az alkalmazást az Azure SQL Database. További információkért lásd: hogyan [bizalmas adatok védelme az SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Az Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) észleli a szervezet identitásait érintő lehetséges biztonsági résekről, konfigurálja az automatikus reakciók, a szervezet identitásait kapcsolatos észlelt gyanús tevékenységeket és folytat gyanús incidensek a megfelelő műveletet a problémák megoldásához.
-   [Az Azure szerepköralapú hozzáférés-vezérlés (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) a fókuszban lévő lehetővé teszi, hogy hozzáférés-kezelés az Azure-hoz. Az előfizetés-rendszergazda előfizetéshez való hozzáférés korlátozódik.

Az Azure SQL Database biztonsági szolgáltatásaival kapcsolatos további információkért tekintse meg a [Contoso Clinic Bemutatóalkalmazásában](https://github.com/Microsoft/azure-sql-security-sample) minta.

### <a name="security"></a>Biztonság
**Titkok kezelése**: A megoldás [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) kulcsok és titkos kulcsok felügyeletéhez. Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében.

## <a name="guidance-and-recommendations"></a>Útmutatás és javaslatok

### <a name="expressroute-and-vpn"></a>Az ExpressRoute és VPN
[Az ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy a biztonságos VPN-alagutat kell konfigurálni, hogy biztonságos kapcsolatot a data analytics referenciaarchitektúra részeként üzembe helyezett erőforrásokkal. Az ExpressRoute-kapcsolatok az interneten keresztül halad, mivel a kapcsolatok az interneten keresztül ajánlja fel a további megbízhatóság megbízhatóbbak, gyorsabbak, kisebb a késésük, és biztonságosabbak a szokásos internetkapcsolatoknál. Megfelelően állítja be az expressroute-on vagy VPN, az ügyfelek hozzáadhatja egy védelmi réteget biztosít adatokat átvitel közben.

### <a name="azure-active-directory-setup"></a>Az Azure Active Directory beállítása
[Az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) elengedhetetlen a központi telepítés kezelése és kiépítése a környezet implementálására csoporthoz való hozzáférést. Az AAD-ben meglévő Windows Server Active Directory integrálhatók [négy kattintással](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Ügyfelek egy meglévő AAD-be való elősegítsék a telepített Active Directory-infrastruktúrát (tartományvezérlők) is, azáltal, hogy a telepített Active Directory-infrastruktúra egy AAD-erdőhöz tartozó altartományban.

### <a name="additional-services"></a>További szolgáltatások
#### <a name="iaas---vm-considerations"></a>IaaS - virtuális gépekkel kapcsolatos szempontok
A PaaS-megoldás nem foglalják magukban az olyan Azure IaaS virtuális gépek. Egy ügyfél létrehozhat egy Azure virtuális gép számos, a PaaS-szolgáltatások futtatásához. Ebben az esetben a funkciók és szolgáltatások üzletmenet-folytonosság és az Azure Monitor naplóira sikerült jól használható:

##### <a name="business-continuity"></a>Az üzletmenet folytonossága
- **Magas rendelkezésre állású**: Server számítási feladatainak szerint vannak csoportosítva egy [rendelkezésre állási csoport](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) az Azure-beli virtuális gépek magas rendelkezésre állás biztosítása érdekében. Legalább egy virtuális gép érhető el egy tervezett vagy nem tervezett karbantartási események esetén teljesíti a 99,95 %-os Azure SLA-t.

- **Recovery Services-tároló**: A [Recovery Services-tároló](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) Kezelőkód biztonsági mentési adatokat, és védelmet biztosít az összes konfiguráció az Azure Virtual Machines ebben az architektúrában. Recovery Services-tárolóval, ügyfelek is fájlok és mappák visszaállítása az IaaS virtuális gépről a teljes virtuális Gépet, így gyorsabb helyreállítást visszaállítása nélkül.

##### <a name="monitoring-solutions"></a>Monitorozási megoldások
-   [Az AD Assessmenthez](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Az Active Directory állapotának ellenőrzése megoldás a kockázat és kiszolgálói környezetek állapotát értékeli a rendszeres időközönkénti, és a telepített kiszolgálói infrastruktúra vonatkozó javaslatok rangsorolt listáját tartalmazza.
-   [Kártevőirtók felmérése](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): A kártevőirtó megoldás jelentések a kártevő szoftverek, a fenyegetések és a védelem állapota.
-   [Frissítéskezelés](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): Az Update Management megoldás lehetővé teszi, hogy az ügyfél felügyeleti operációs rendszer biztonsági frissítéseket, beleértve egy elérhető frissítések állapotát és a szükséges frissítések telepítése.
-   [Az ügynök állapota](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Az Agent Health megoldás hány ügynök van telepítve, és a földrajzi elosztás, valamint hány ügynök, amely nem válaszol és működési adatokat küld be, amely az ügynökök számát jelenti.
-   [A változáskövetés](https://docs.microsoft.com/azure/automation/automation-change-tracking): A Change Tracking megoldás lehetővé teszi, hogy az ügyfelek számára, hogy könnyen azonosíthassa a változtatásokat a környezetben.

##### <a name="security"></a>Biztonság
- **Kártevők elleni védekezés**: [A Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) tartozó virtuális gépeket biztosít, amellyel a vírusok, kémprogramok és más kártevők azonosításában és valós idejű védelem funkció, konfigurálható riasztásokkal, ha ismert kártevő vagy nemkívánatos szoftver megkísérli Telepítse, vagy a védett virtuális gépek futtatásához.
- **Javítások kezelése**: Ez a referenciaarchitektúra részeként üzembe helyezett Windows virtuális gépek automatikusan frissítéseket kapjanak a Windows Update szolgáltatás alapértelmezés szerint vannak konfigurálva. Ez a megoldás is magában foglalja a [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) szolgáltatást, amelyen frissített telepítések hozható létre a javítás virtuális gépekhez szükség esetén.

#### <a name="azure-commercial"></a>Az Azure kereskedelmi
Bár a data analytics architektúrája nem célja való üzembe helyezéshez a [Azure Commercial](https://azure.microsoft.com/overview/what-is-azure/) környezet, hasonló célokkal lehet elérni, ez a referenciaarchitektúra és a szolgáltatások, valamint További szolgáltatások csak az Azure kereskedelmi környezetben érhető el. Vegye figyelembe, hogy az Azure kereskedelmi fenntartja a FedRAMP JAB P-ATO szinten mérsékelt hatás, lehetővé téve a kormányzati szerveknek és a partnerek számára, Közepesen bizalmas adatokat a felhőben, kihasználva az Azure kereskedelmi környezetben üzembe helyezni.

Az Azure kereskedelmi kínál számos elemzési szolgáltatások insights ki nagy mennyiségű adat lekérése:
-   [Az Azure Machine Learning Studio](https://docs.microsoft.com/azure/machine-learning/studio/what-is-ml-studio), amely egy összetevőjét a [Cortana Intelligence Suite](https://azure.microsoft.com/overview/ai-platform/), alakul ki egy vagy több adatforrásból prediktív elemzési modellek. Statisztikai függvényekkel modellje, amely majd használhatnak fel, mint a Power BI alkalmazások létrehozásához használt számos ismétlések keresztül.
-   [Az Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) lehetővé teszi mindenféle méretű, típusú és feldolgozási sebességű egy egy helyen történő műveleti és felderítési jellegű adatok rögzítését. Az Azure Data Lake Store a Hadoop-ökoszisztéma legtöbb nyílt forráskódú összetevőjével kompatibilis, és más Azure-szolgáltatásokkal is jól integrálható.

## <a name="threat-model"></a>Fenyegetések modellezése

A data folyamat diagramja (DFD) Ez a referenciaarchitektúra érhető el az [letöltése](https://aka.ms/blueprintanalyticsthreatmodel) vagy alább tekintheti meg:

## <a name="compliance-documentation"></a>Megfelelőségi dokumentációja
A [Azure biztonsági és megfelelőségi terv – FedRAMP magas vevő felelőssége mátrix](https://aka.ms/blueprinthighcrm) sorolja fel a magas szintű FedRAMP-alapkonfiguráció által igényelt összes biztonsági vezérlő. Ehhez hasonlóan a [Azure biztonsági és megfelelőségi terv – FedRAMP mérsékelt vevő felelőssége mátrix](https://aka.ms/blueprintanalyticscimmod) sorolja fel a FedRAMP mérsékelt alapkonfiguráció által igényelt összes biztonsági vezérlő. Mind a dokumentumok bemutatják, hogy az egyes vezérlőelemek végrehajtása feladata a Microsoft, az ügyfél vagy a kettő között megosztott.

A [Azure biztonsági és -megfelelőségi terv - FedRAMP magas ellenőrzési megvalósítási mátrix](https://aka.ms/blueprintanalyticscimhigh) és a [Azure biztonsági és -megfelelőségi terv - FedRAMP mérsékelt vezérlő megvalósítási mátrix](https://aka.ms/blueprintanalyticscimmod) megadása információ, amelyre a vezérlők esnek FedRAMP minden egyes referenciakonfigurációnál, beleértve a részletes leírását, hogyan végrehajtása megfelel egyes kezelt vezérlő az analytics architektúrájának.

## <a name="disclaimer"></a>Jogi nyilatkozat

 - Ez a dokumentum csak tájékoztatási célokat szolgál. A MICROSOFT NEM VÁLLAL SEMMILYEN KIFEJEZETT, KIFEJEZETT, VÉLELMEZETT VAGY KÖTELEZŐ GARANCIÁT A DOKUMENTUMBAN SZEREPLŐ INFORMÁCIÓRA VONATKOZÓAN. Ez a dokumentum biztosított "as-van." Információk és vélemények ebben a dokumentumban URL-CÍMEK és más internetes webhelyekre utaló hivatkozások értesítés nélkül változhatnak. Ez a dokumentum olvasásakor ügyfelek felelősségére használja.
 - Ez a dokumentum nem biztosít semmilyen jogot semmilyen Microsoft-termék vagy a megoldások a szellemi tulajdonhoz ügyfelek.
 - Ügyfelek másolhatja és használhatja ezt a dokumentumot belső, hivatkozási célokból.
 - Bizonyos ajánlások a dokumentum eredményezhet megnövekedett adat-, hálózati vagy számítási erőforrás-használat az Azure-ban, és előfordulhat, hogy növelje az ügyfél Azure licencek vagy előfizetések költségeit.
 - Ez az architektúra célja, hogy az ügyfelek számára az adott követelményekhez beállítása alapjaként szolgálja ki, és nem használható-éles környezetben van.
 - Ez a dokumentum referenciaként fejlesztik, és nem használható minden olyan eszközt, amely szerint egy ügyfél megfelel a megadott megfelelőségi követelmények és előírások meghatározásához. Ügyfelek jóváhagyott megvalósítás az ügyfeleknél a szervezet jogi támogatást kell kérnie.
