---
title: Azure biztonsági és megfelelőségi terv – IaaS-webalkalmazás NIST 800-171-SP rendszerhez
description: Azure biztonsági és megfelelőségi terv – IaaS webes alkalmazás SP-NIST 800-171
services: security
author: jomolesk
ms.assetid: 1f1ad27f-32c3-4e76-abb9-ea768d01747f
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: 78c92f8d738dd675ac20c31bd8171bd4370a56f6
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2018
ms.locfileid: "49406245"
---
# <a name="azure-security-and-compliance-blueprint---iaas-web-application-for-nist-sp-800-171"></a>Azure biztonsági és megfelelőségi terv – IaaS-webalkalmazás NIST 800-171-SP rendszerhez

## <a name="overview"></a>Áttekintés
[NIST Special Publication 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) hasznos útmutatást ad a szabályozott nem titkos információ (CUI) nonfederal információs rendszerek és a szervezetek a védelméhez. 800-171 NIST SP hozza létre a 14 termékcsaládok CUI titkosságát védelme kapcsolatos biztonsági követelményeket.

Az Azure biztonsági és megfelelőségi terv segítségével az ügyfelek központi telepítése egy webalkalmazás-architektúra az Azure-ban, amely megvalósítja egy részhalmazát NIST SP 800-171 vezérlők útmutatást nyújt. Ez a megoldás bemutatja, amelyben ügyfeleink megfelel bizonyos biztonsági és megfelelőségi követelmények módon. Ez az ügyfelek számára hozhat létre, és a saját webes alkalmazások konfigurálása az Azure-ban alapjaként is szolgál.

Ez a referenciaarchitektúra társított megvalósítási útmutató és fenyegetések modellezése célja, hogy az ügyfelek számára az adott követelményekhez a felmerülő ismeretekkel szolgál. Nem használható-éles környezetben van. Ez az architektúra módosítás nélküli üzembe helyezése nem elegendő a teljes mértékben a követelményeinek az NIST SP 800-171. Ügyfelei felelősek a megfelelő biztonsági és megfelelőségi értékelést, ez az architektúra használatával létrehozott megoldások elvégzéséhez. Követelmények minden ügyfél megvalósítása tulajdonságairól alapján változhat.

## <a name="architecture-diagram-and-components"></a>Architektúra és összetevők
Az Azure biztonsági és megfelelőségi terv helyez üzembe egy SQL Server háttéralkalmazását az IaaS-webalkalmazás a referenciaarchitektúra. Az architektúra egy webes réteg, adatrétegbeli, az Active Directory infrastruktúra, az Azure Application Gateway és az Azure Load Balancer tartalmazza. A webes és az adatrétegekhez üzembe helyezett virtuális gépek (VM) egy rendelkezésre állási csoportban vannak konfigurálva. Always On rendelkezésre állási csoport magas rendelkezésre állású SQL Server-példányokat lehet konfigurálni. Virtuális gépek a tartományhoz. Az Active Directory csoportházirendjei érvényesíti a biztonsági és megfelelőségi konfigurációk az operációs rendszer szintjén.

A teljes megoldás az Azure Storage, amely az ügyfelek konfigurálása az Azure Portalról épül. Storage titkosítja az inaktív adatok bizalmas mivoltát a Storage Service Encryption az összes adatot. Georedundáns tárolás biztosítja, hogy az ügyfél elsődleges adatközpont káros eseményt sem adatvesztést eredményez. A másodpéldány egy külön helyen több száz mérfölddel távolabb tárolódik.

A fokozott biztonság érdekében ebben a megoldásban az összes erőforrás egy erőforráscsoportba tartozó Azure Resource Manageren keresztül felügyelt. Az Azure Active Directory (Azure AD) szerepköralapú hozzáférés-vezérlés (RBAC) segítségével üzembe helyezett erőforrásokról és kulcsok az Azure Key Vaultban történő hozzáférést. A fájlrendszer állapotának Azure monitoron keresztül figyel. Ügyfelek konfigurálása mindkét figyelési szolgáltatásokat naplók rögzítésére. A fájlrendszer állapotának, amely egyszerűen használható egyetlen irányítópulton jelenik meg.

Felügyeleti bástyagazdagép biztonságos kapcsolatot biztosít a rendszergazdák számára a hozzáférés üzembe helyezett erőforrásokat. *A Microsoft azt javasolja, hogy a felügyeleti és az adatok importálása a referencia architektúra alhálózatban a VPN- vagy Azure ExpressRoute kapcsolat konfigurálása.*


![IaaS-webalkalmazás NIST SP 800-171 architekturális diagramja](images/nist171-iaaswa-architecture.png "IaaS-webalkalmazás NIST SP 800-171 architekturális diagramja")

Ez a megoldás a következő Azure-szolgáltatásokat használ. További információkért lásd: a [üzembe helyezési architektúrája](#deployment-architecture) szakaszban.

- Azure-alapú virtuális gépek
    - (1) felügyeleti/megerősített (Windows Server 2016 Datacenter)
    - (2) az active Directory-tartományvezérlőhöz (Windows Server 2016 Datacenter)
    - (2) az SQL Server-fürt csomópontjának (Windows Server 2016-on futó SQL Server 2017)
    - (2) webkiszolgáló és IIS (Windows Server 2016 Datacenter)
- Azure Virtual Network
    - ((1) /16 hálózati
    - (5) /24 hálózatok
    - (5) a hálózati biztonsági csoportok
- Rendelkezésre állási csoportok
    - (1) az active Directory-tartományvezérlők
    - (1) az SQL-fürtcsomópont
    - (1) webkiszolgáló és IIS
- Azure Application Gateway
    - (1) a webalkalmazási tűzfal
        - Tűzfalmód: megelőzése
        - Set-szabály: OWASP 3.0
        - Figyelő portja: 443
- Azure Active Directory
- Azure Key Vault
- Azure Load Balancer
- Azure Monitor
- Azure Resource Manager
- Azure Security Center
- Azure Storage
- Azure Log Analytics
- Azure Automation
- Felhőbeli tanúsító
- Recovery Services-tároló

## <a name="deployment-architecture"></a>Üzembe helyezési architektúrája
A következő szakaszt az üzembe helyezés és a megvalósítás elemek részletei.

**Bástyagazdagép**: A bástyagazdagép a központi hely, amely a felhasználók használhatják az ebben a környezetben üzembe helyezett erőforrások eléréséhez. A bástyagazdagép biztosítja, hogy a telepített erőforrások biztonságos kapcsolatot csak nyilvános IP-címekről érkező távoli forgalmat engedélyezi a biztonságos elemek listájához. Távoli asztali forgalmat lehetővé teszik, hogy a forgalom forrását definiálni kell a hálózati biztonsági csoport (NSG).

Ez a megoldás létrehoz egy virtuális Gépet egy tartományhoz csatlakoztatott megerősített gazdagépként az alábbi konfigurációkkal:
-   [A kártevőirtó bővítmény](https://docs.microsoft.com/azure/security/azure-security-antimalware).
-   [Az Azure Diagnostics bővítmény](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template).
-   [Az Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) Key Vault használatával.
-   Egy [automatikus leállítási házirend](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) amikor nincs használatban a Virtuálisgép-erőforrások felhasználásának csökkentése érdekében.
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) engedélyezve van, hogy a hitelesítő adatokat, valamint egyéb titkok futtassa az elkülönített védett környezetben futó operációs rendszert.

### <a name="virtual-network"></a>Virtuális hálózat
Az architektúra egy 10.200.0.0/16 címtere a privát virtuális hálózat határozza meg.

**Hálózati biztonsági csoportok**: Ez a megoldás üzembe helyezi a webalkalmazás, adatbázis, az Active Directory és felügyeleti virtuális hálózatokon belüli külön alhálózatra rendelkező erőforrásokat. Alhálózatok logikailag elválasztott az egyes alhálózatokra alkalmazza az NSG-szabályok. A szabályok korlátozzák, hogy csak szükséges rendszer- és felügyeleti funkciók, az alhálózatok közötti adatforgalom.

Tekintse meg a konfiguráció a [NSG-k](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) üzembe helyezett ezzel a megoldással. Szervezetek konfigurálhatja az NSG-k használatával az előző fájl szerkesztésével [ebben a dokumentációban](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) alapján.

Egy dedikált NSG minden egyes alhálózatban van:
- Egy NSG-t az Application Gateway (LBNSG)
- Egy NSG-t a bástyagazdagép (MGTNSG)
- Egy NSG-t elsődleges és tartalék tartományvezérlők (ADNSG)
- Egy NSG-t az SQL Server-kiszolgálók és a Felhőbeli tanúsító (SQLNSG)
- Egy NSG-t webes réteg (WEBNSG)

### <a name="data-in-transit"></a>Az átvitt adatok
Az Azure és az Azure adatközpontok bemenő kommunikáció alapértelmezés szerint titkosítja. Emellett az Azure Portalon keresztül a tárolási tranzakciók HTTPS-kapcsolaton keresztül történik.

### <a name="data-at-rest"></a>Inaktív adat
Az architektúra használatával több mértéket az inaktív adatok védi. Ezeket a mértékeket tartalmazzák, titkosítás és az adatbázis naplózási szolgáltatásával.

**Az Azure Storage**: igényeinek megfelelően a titkosított adatok inaktív állapotban, minden [tárolási](https://azure.microsoft.com/services/storage/) használ [a Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption). Ez a funkció segít a szervezeti biztonsági kötelezettségeit, és a NIST 800-171 SP által meghatározott megfelelőségi követelmények támogatásához adatok biztonságos megőrzésében.

**Az Azure Disk Encryption**: Disk Encryption Windows IaaS virtuális gép titkosított lemezek szolgál. [Lemeztitkosítás](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) kötettitkosítást biztosít az operációs rendszer és az adatlemezeket a Windows BitLocker funkcióját használja. A megoldás integrálva van a Key Vault segítségével szabályozhatja, és kezelhetik a lemeztitkosítási kulcsokat.

**Az SQL Server**: az SQL Server-példányt használja a következő adatbázis biztonsági intézkedéseket:
-   [Az SQL Server-naplózás](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine?view=sql-server-2017) nyomon követi az adatbázisok eseményeit, és írja őket az auditnaplók.
-   [Transzparens adattitkosítás](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) hajtja végre a valós idejű titkosításához és visszafejtéséhez az adatbázis, azokhoz kapcsolódó biztonsági mentési és tranzakciós naplófájlokra adatok inaktív védelme érdekében. Transzparens adattitkosítás biztosítja, hogy a tárolt adatok még nem vonatkoznak a jogosulatlan hozzáférést.
-   [Tűzfalszabályok](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) megakadályozzák elérését, adatbázis-kiszolgálók, amíg a megfelelő engedélyekkel. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján.
-   [Titkosított oszlopokat](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-wizard?view=sql-server-2017) győződjön meg arról, hogy bizalmas adatok soha nem jelenik meg az adatbázis-rendszer egyszerű szövegként. Ha engedélyezett az adattitkosítás, csak az ügyfélalkalmazások vagy alkalmazáskiszolgálók hozzáférést a kulcsokhoz hozzáférhet egyszerű szöveges adatokat.
- [Dinamikus adatmaszkolás](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?view=sql-server-2017) által a replikaadatok felhasználók vagy alkalmazások, adatok maszkolása korlátozza a bizalmas adatok. Automatikusan potenciálisan bizalmas adatok felderítéséhez és javasoljuk a alkalmazni lehessen a megfelelő maszkokkal. Dinamikus adatmaszkolás segít hozzáférés korlátozása, így bizalmas adatok nem az adatbázis jogosulatlan hozzáférést. *Be kell tartaniuk az adatbázisséma beállítások hangolását ügyfelek felelőssége.*

### <a name="identity-management"></a>Identitáskezelés
A következő technológiákat az Azure-beli adatokhoz való hozzáférés kezeléséhez képességeket biztosítják:
-   [Az Azure AD](https://azure.microsoft.com/services/active-directory/) van a Microsoft több-bérlős felhőalapú címtár- és identitáskezelési szolgáltatás. Ez a megoldás az összes felhasználó jönnek létre az Azure ad-ben, és az SQL Server-példány hozzáféréssel rendelkező felhasználókat tartalmazzák.
-   Hitelesítés az alkalmazás Azure AD használatával történik. További információkért lásd: hogyan [alkalmazások integrálása az Azure ad-vel](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).
-   [Az Azure RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) segítségével a rendszergazdák csak olyan mértékű hozzáférést biztosítson, a felhasználóknak frissíteniük kell a munkája elvégzéséhez a minden részletre kiterjedő hozzáférési engedélyek megadása. Helyett az Azure-erőforrások minden felhasználó ugyanolyan korlátlan engedélyeket ad, a rendszergazdák engedélyezhetik csak bizonyos adatok eléréséhez szükséges műveleteket. Az előfizetés-rendszergazda előfizetéshez való hozzáférés korlátozódik.
- [Az Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) bizonyos erőforrásokhoz hozzáférő felhasználók számának csökkentése érdekében az ügyfelek által használható. A rendszergazdák Azure AD Privileged Identity Management segítségével Fedezze fel, korlátozhatja és emelt szintű identitások és azok erőforrásokhoz való hozzáférésének figyelése. Ez a funkció is segítségével igény szerinti – igény rendszergazdai hozzáférés kényszerítésére.
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
- A megoldás integrálva van a Key Vault IaaS virtuális gépek lemeztitkosítási kulcsokat és titkos kulcsok kezeléséhez.

**Javítások kezelése**: Windows virtuális gépeket telepíteni, mivel ez a referenciaarchitektúra része automatikusan frissítéseket kapjanak a Windows Update szolgáltatás alapértelmezés szerint vannak konfigurálva. Ez a megoldás is magában foglalja a [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) szolgáltatást, amelyen keresztül frissített telepítések hozható létre virtuális gépeket, ha a szükséges javítási.

**Kártevők elleni védekezés**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) virtuális gépek számára biztosítja a valós idejű védelem funkció, amely alapján azonosíthatja, és távolítsa el a vírusok, kémprogramok és más, kártevő szoftverek. Ügyfelek is beállíthat riasztásokat, amelyek készítése, ha ismert kártevő vagy nemkívánatos szoftverek megpróbálják telepíteni vagy futtatni védett virtuális gépeken.

**Az Azure Security Center**: A [a Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), ügyfelek is központilag alkalmazása és a számítási feladatok biztonsági házirendek kezelése, korlátozhatja a fenyegetéseknek való kitettséget, felismeri és elháríthatja a támadásokat. A Security Center is éri el a meglévő konfigurációk az Azure-szolgáltatások konfigurációs és szolgáltatási javaslatok javíthatja biztonsági helyzetét és adatok védelme érdekében.

A Security Center használatával észlelési képességek széles ügyfelek esetleges támadások, hogy a cél riasztás környezetük. Ezek a riasztások értékes információkat tartalmaznak arról, hogy mi váltotta ki a riasztást, valamint a támadás forrásáról és az általa célba vett erőforrásokról. A Security Center készletével rendelkezik [biztonsági riasztások az előre meghatározott](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) , amely vannak aktiválódik, ha a fenyegetések vagy gyanús tevékenységek. Az ügyfelek használhatják [egyéni riasztási szabályok](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) meghatározásához a környezetből már begyűjtött adatok alapján új biztonsági riasztásokat.

A Security Center rangsorolt biztonsági riasztások és incidensek biztosít. A Security Center egyszerűbbé teszi az ügyfelek felderítéséhez és a potenciális biztonsági problémákat. A [fenyegetésfelderítési jelentés](https://docs.microsoft.com/azure/security-center/security-center-threat-report) jön létre a rendszer minden egyes észlelt fenyegetés. Incidensmegoldási csapat a jelentéseket használhatja, amikor elhárításban, és vizsgálja meg.

Ez a referenciaarchitektúra használja a [biztonságirés-értékelési](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) képesség a Security Centerben. Beállítások konfigurálása után a partnerügynök (például a Qualys) jelenti a biztonsági adatok a partner felügyeleti platformjára. Ezután a partner felügyeleti platformja a biztonsági résekre vonatkozó és állapotmonitorozási adatokat küld vissza a Security Centernek. Ügyfelek használhatják ezeket az adatokat gyorsan azonosíthatja a sebezhető virtuális gépeket.

**Az Azure Application Gateway**: az architektúra csökkenti a biztonsági réseket egy application gateway webalkalmazási tűzfal konfigurálva és engedélyezve van az OWASP szabálykészlet használatával. További képességek:

- [Vége a közötti SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- Engedélyezése [SSL-alapú kiszervezéshez](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal).
- Tiltsa le [a TLS 1.0 és 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- [Webalkalmazási tűzfal](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (megelőzés üzemmód).
- [Megelőzés üzemmód](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) az OWASP 3.0-s szabály beállítása.
- Engedélyezése [diagnosztikai célú naplózásának](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).
- [Egyéni állapotmintákkal](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal).
- [A Security Center](https://azure.microsoft.com/services/security-center) és [az Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) nyújtanak további védelmet és értesítések. A Security Center megbízhatóságibesorolás-kezelési rendszert is biztosít.

### <a name="business-continuity"></a>Az üzletmenet folytonossága

**Magas rendelkezésre állású**: A megoldás az összes virtuális gép üzembe helyezése egy [rendelkezésre állási csoport](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Rendelkezésre állási csoportok biztosítják, hogy a virtuális gépek rendelkezésre állás javítása érdekében több elkülönített hardverfürt között legyenek elosztva. Legalább egy virtuális gép érhető el egy tervezett vagy nem tervezett karbantartási események, amely megfelel a 99,95 %-os Azure SLA-t.

**Recovery Services-tároló**: A [Recovery Services-tároló](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) Kezelőkód biztonsági mentési adatokat, és védelmet biztosít az összes konfiguráció az Azure Virtual Machines ebben az architektúrában. A Recovery Services-tárolóval, ügyfelek is fájlok és mappák visszaállítása az IaaS virtuális gépről a teljes virtuális gép visszaállítása nélkül. Ez a folyamat felgyorsítja a helyreállítási időt.

**Felhőbeli tanúsító**: [Felhőbeli tanúsító](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) egy feladatátvevő fürt kvórum tanúsítójának a Windows Server 2016, amely Azure használja egyeztetési pontként típusú. Felhőbeli tanúsító – más kvórumtanúsítókhoz, például szavazattal rendelkezik, és részt vehet a kvórumszámításokban. A standard szintű nyilvánosan elérhető Azure Blob storage használ. Ezzel az elrendezéssel fokozott kiküszöböli a felesleges karbantartási a nyilvános felhőben üzemeltetett virtuális gépek.

### <a name="logging-and-auditing"></a>Naplózás és vizsgálat

Azure-szolgáltatások széles körben system és a felhasználói tevékenység, valamint a rendszerállapot jelentkezzen be:
- **A Tevékenységnaplók**: [tevékenységeket tartalmazó naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) adjon meg egy előfizetéshez tartozó erőforrásokon végrehajtott műveletekkel kapcsolatos információk. A Tevékenységnaplók segítségével határozza meg a műveletet kezdeményező, az eseményt, és állapot ideje.
- **Diagnosztikai naplók**: [diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) minden erőforrás által kibocsátott az összes napló tartalmazza. Ezek a naplók például a Windows rendszer-eseménynaplói, a Storage-naplók, a Key Vault-naplók és a Application Gateway hozzáférés és a tűzfal a naplókat. Az összes diagnosztikai naplók írni egy központosított, titkosított csatornákon történik az Azure storage-fiókját archiválási. Felhasználók is konfigurálhatók a megőrzés időtartamát, legfeljebb 730 napig, az adott igényeknek.

**Log Analytics**: ezeket a naplókat a rendszer összevont [Log Analytics](https://azure.microsoft.com/services/log-analytics/) feldolgozási, tárolására és-irányítópult jelentéseit. Az adatokat a begyűjtésük után azt rendszer adattípusonként külön táblába rendezi az egyes adattípusokhoz Log Analytics-munkaterületek belül. Ily módon az összes adat elemezhetők együtt, az eredeti forrástól függetlenül. A Security Center integrálható a Log Analytics használatával. Ügyfelek használhatják a Log Analytics-lekérdezéseket a biztonsági események adatainak eléréséhez és más szolgáltatások származó adatokat kombinálni.

A következő Log Analytics [felügyeleti megoldások](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) Ez az architektúra egy része szerepel:
-   [Az Active Directory assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): A Active Directory Health Check megoldással felmérheti a kockázat és kiszolgálói környezetek állapotát rendszeres időközönkénti. Biztosít a telepített kiszolgálói infrastruktúra vonatkozó javaslatok rangsorolt listáját.
- [SQL-értékeléssel](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): az SQL Health Check megoldással felmérheti a kockázat és kiszolgálói környezetek állapotát rendszeres időközönkénti. Ügyfelek biztosít a telepített kiszolgálói infrastruktúra vonatkozó javaslatok rangsorolt listáját.
- [Az ügynök állapota](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): az Agent Health megoldás jelentések hány ügynök van telepítve, és a földrajzi eloszlása. Hány ügynök nem válaszol, és küldje el az operatív adatokat, az ügynökök számát is jelenti.
-   [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): az ügyfél az összes Azure-előfizetések az Azure-Tevékenységnaplók elemzésének segíti az Activity Log Analytics megoldás.

**Az Azure Automation**: [Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) tárolja, fut, és kezeli a runbookok. Ebben a megoldásban a runbookok gyűjteni az SQL Server segítségével. Az ügyfelek használhatják az Automation [Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) megoldás könnyedén azonosíthatja a változtatásokat a környezetben.

**Az Azure Monitor**: [figyelő](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) segít a felhasználóknak a teljesítmény, a biztonság fenntartására és a trendek azonosítására. Szervezetek használhatják azt a naplózása, létre riasztásokat és archiválhat adatokat. Akkor is nyomon követheti a saját Azure-erőforrások API-hívások.

## <a name="threat-model"></a>Fenyegetések modellezése

A referenciaarchitektúra az adatfolyam-diagram érhető el az [letöltése](https://aka.ms/nist171-iaaswa-tm) vagy itt található. Ez a modell segítségével az ügyfelek megismerhetik a pontokat a rendszer infrastruktúra esetleges kockázat, ha azok hajtsa végre a módosításokat.

![IaaS-webalkalmazás NIST SP 800-171 fenyegetések modellezése](images/nist171-iaaswa-threat-model.png "NIST SP 800-171 fenyegetések modellezése az IaaS-webalkalmazás")

## <a name="compliance-documentation"></a>Megfelelőségi dokumentációja

A [Azure biztonsági és megfelelőségi terv - 800-171 NIST SP vevő felelőssége mátrix](https://aka.ms/nist171-crm) NIST SP 800-171 által igényelt összes biztonsági vezérlő sorolja fel. Ez a mátrix részletezi a Microsoft, az ügyfél felelőssége végrehajtására az egyes vezérlőelemek-e, vagy a kettő között megosztott.

A [Azure biztonsági és -megfelelőségi terv - 800-171 NIST SP IaaS webes alkalmazás ellenőrzési megvalósítási mátrix](https://aka.ms/nist171-iaaswa-cim) melyik NIST SP a 800-171 szabályozza az IaaS webalkalmazás-architektúra tárgyalt információkkal. Hogyan megvalósítása megfelel-e a követelményeknek, az egyes kezelt vezérlőelemek a részletes leírását tartalmazza.

## <a name="guidance-and-recommendations"></a>Útmutatás és javaslatok
### <a name="vpn-and-expressroute"></a>VPN és ExpressRoute
Biztonságos VPN-alagúton vagy [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) konfigurálni kell, hogy biztonságos kapcsolatot az IaaS webalkalmazás referenciaarchitektúrája részeként üzembe helyezett erőforrásokkal. Megfelelően állítja be a VPN-t vagy ExpressRoute, az ügyfelek hozzáadhatja egy védelmi réteget biztosít adatokat átvitel közben.

Az Azure-ral biztonságos VPN-alagút alkalmazásával egy helyszíni hálózat és a egy Azure virtuális hálózat közötti virtuális magánhálózati kapcsolat hozható létre. Ezt a kapcsolatot az interneten keresztül történik. Ügyfelek használhatják az titkosított kapcsolaton az ügyfél hálózati és az Azure között lévő biztonságosan "csatorna" információkat ezen a kapcsolaton. Site-to-site VPN egy olyan biztonságos, érett technológia, évtizedes a vállalatok által központilag telepített. A [IPsec-alagút módú](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) ezt a beállítást, mint egy titkosítási mechanizmus használatban van.

A VPN-alagút belül adatforgalomra site-to-site VPN-nel az internethez, mert a Microsoft kínál egy másik még biztonságosabb kapcsolat lehetőséget. Az ExpressRoute dedikált WAN Azure-ban és a egy helyszíni helyhez vagy egy Exchange-szolgáltató közötti kapcsolat. Az ExpressRoute-kapcsolatok közvetlenül csatlakozhat az ügyfél távközlési szolgáltató. Ennek következtében az adatok nem az interneten keresztül továbbítani, és nincs kitéve. Ezek a kapcsolatok több megbízhatóság megbízhatóbbak, gyorsabbak, kisebb a késésük, és biztonságosabbak a szokásos internetkapcsolatoknál kínálnak. 

Ajánlott eljárások végrehajtásához egy biztonságos hibrid hálózatot, amely kiterjeszti a helyszíni hálózatot az Azure-bA [elérhető](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Jogi nyilatkozat

- Ez a dokumentum csak tájékoztatási célokat szolgál. A MICROSOFT NEM VÁLLAL SEMMILYEN KIFEJEZETT, KIFEJEZETT, VÉLELMEZETT VAGY KÖTELEZŐ GARANCIÁT A DOKUMENTUMBAN SZEREPLŐ INFORMÁCIÓRA VONATKOZÓAN. Ez a dokumentum biztosított "as-van." Információk és vélemények ebben a dokumentumban URL-CÍMEK és más internetes webhelyekre utaló hivatkozások értesítés nélkül változhatnak. Ez a dokumentum olvasásakor ügyfelek felelősségére használja. 
- Ez a dokumentum nem biztosít semmilyen jogot semmilyen Microsoft-termék vagy a megoldások a szellemi tulajdonhoz ügyfelek. 
- Ügyfelek másolhatja és használhatja ezt a dokumentumot belső, hivatkozási célokból. 
- Bizonyos ajánlások a dokumentum eredményezhet megnövekedett adat-, hálózati vagy számítási erőforrás-használat az Azure-ban, és előfordulhat, hogy növelje az ügyfél Azure licencek vagy előfizetések költségeit. 
- Ez az architektúra célja, hogy az ügyfelek számára az adott követelményekhez beállítása alapjaként szolgálja ki, és nem használható-éles környezetben van.
- Ez a dokumentum referenciaként fejlesztik, és nem használható minden olyan eszközt, amely szerint egy ügyfél megfelel a megadott megfelelőségi követelmények és előírások meghatározásához. Ügyfelek jóváhagyott megvalósítás az ügyfeleknél a szervezet jogi támogatást kell kérnie.
