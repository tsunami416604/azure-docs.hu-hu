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
ms.openlocfilehash: f19478b29fd96cd2bd3ed5e0e994595d7b00edf3
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2018
ms.locfileid: "39391983"
---
# <a name="azure-security-and-compliance-blueprint---iaas-web-application-for-nist-sp-800-171"></a>Azure biztonsági és megfelelőségi terv – IaaS-webalkalmazás NIST 800-171-SP rendszerhez

## <a name="overview"></a>Áttekintés
[NIST Special Publication 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) hasznos útmutatást ad a szabályozott nem titkos információ (CUI) nonfederal információs rendszerek és a szervezetek a védelméhez. 800-171 NIST SP hozza létre a 14 termékcsaládok CUI titkosságát védelme kapcsolatos biztonsági követelményeket.

Az Azure biztonsági és megfelelőségi terv segítségével az ügyfelek központi telepítése egy webalkalmazás-architektúra az Azure-ban, amely megvalósítja egy részhalmazát NIST SP 800-171 vezérlők útmutatást nyújt. Ebben a megoldásban, amelyben ügyfeleink megfelel bizonyos biztonsági és megfelelőségi követelmények módszereket mutatja be, és az ügyfelek számára hozhat létre, és a saját webes alkalmazások konfigurálása az Azure-ban alapjaként szolgál.

Ez a referenciaarchitektúra társított megvalósítási útmutató és fenyegetések modellezése célja, hogy az ügyfelek számára az adott követelményekhez a felmerülő ismeretekkel szolgál, és nem használható – éles környezetben van. Ez az architektúra módosítás nélküli üzembe helyezése nem elegendő a teljes mértékben a követelményeinek az NIST SP 800-171. Ügyfelek végző megfelelő biztonsági és megfelelőségi értékelést bármely megoldás használatával létrehozott ebben az architektúrában követelmények eltérőek lehetnek, hogy milyen ügyről van minden ügyfél megvalósítása alapján.

## <a name="architecture-diagram-and-components"></a>Architektúra és összetevők
Az Azure biztonsági és megfelelőségi terv üzembe helyez egy referencia-architektúra, az SQL Server-háttérrendszerét az IaaS-webalkalmazás. Az architektúra egy webes réteg, adatrétegbeli, Active Directory infrastruktúrával, az Application Gateway és terheléselosztó tartalmaz. A webes és az adatrétegekhez az üzembe helyezett virtuális gépek rendelkezésre állási csoportban vannak konfigurálva, és az SQL Server-példányok úgy vannak konfigurálva, egy Always On rendelkezésre állási csoportban a magas rendelkezésre állás érdekében. Virtuális gépek tartományhoz csatlakoztatva, és az Active Directory-csoportházirendek segítségével érvényesíti a biztonsági és megfelelőségi konfigurációk az operációs rendszer szintjén.

A teljes megoldás az Azure Storage, amely az ügyfelek konfigurálása az Azure Portalról épül. Az Azure Storage titkosítja az inaktív adatok bizalmas mivoltát a Storage Service Encryption az összes adatot. Földrajzi Zónaredundáns tárolás biztosítja, hogy, hogy az ügyfél elsődleges adatközpont káros eseményt nem eredményez az adat, mert a másodpéldány lesz tárolva egy külön helyen több száz mérfölddel távolabb.

A fokozott biztonság érdekében ebben a megoldásban az összes erőforrás egy erőforráscsoportba tartozó Azure Resource Manageren keresztül felügyelt. Az Azure Active Directory szerepköralapú hozzáférés-vezérlés szabályozására szolgál az üzembe helyezett erőforrások és a kulcsok Azure Key vaultban. A fájlrendszer állapotának Azure monitoron keresztül figyel. Ügyfelek konfigurálása mindkét figyelési szolgáltatásokat naplók rögzítése és a fájlrendszer állapotának megjelenítése egyetlen, könnyen navigálható-irányítópulton.

Felügyeleti bástyagazdagép biztonságos kapcsolatot biztosít a rendszergazdák számára a hozzáférés üzembe helyezett erőforrásokat. **A Microsoft javasolja, hogy a felügyeleti és az adatok importálása a referencia architektúra alhálózatában VPN vagy ExpressRoute-kapcsolat beállítása.**


![IaaS-webalkalmazás NIST SP 800-171 architekturális diagramja](images/nist171-iaaswa-architecture.png "IaaS-webalkalmazás NIST SP 800-171 architekturális diagramja")

Ez a megoldás a következő Azure-szolgáltatásokat használ. További részleteket a [üzembe helyezési architektúrája](#deployment-architecture) szakaszban.

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

**Bástyagazdagép**: A bástyagazdagép a központi hely, amely lehetővé teszi a felhasználók hozzáférhessenek a környezetben telepített erőforrás. A bástyagazdagép üzembe helyezett erőforrásokhoz való biztonságos kapcsolatot nyújt azáltal, hogy csak nyilvános IP-címekről érkező távoli forgalmat a biztonságos elemek listájához. Távoli asztali (RDP) forgalmat lehetővé teszik, hogy a forgalom forrását kell definiálni, a hálózati biztonsági csoport tagja.

Ez a megoldás létrehoz egy virtuális gépet egy tartományhoz csatlakoztatott megerősített gazdagépként az alábbi konfigurációkkal:
-   [A kártevőirtó bővítmény](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Az Azure Diagnostics bővítmény](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Az Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) az Azure Key Vaulttal
-   Egy [automatikus leállítási házirend](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) csökkenthető a fogyasztása a virtuális gépi erőforrások, amikor nincs használatban
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) engedélyezve van, hogy a hitelesítő adatokat és egyéb titkos adatait, amely el van különítve a futó operációs rendszert a védett környezetben futnak

### <a name="virtual-network"></a>Virtuális hálózat
Az architektúra egy 10.200.0.0/16 címtere a privát virtuális hálózat határozza meg.

**Hálózati biztonsági csoportok**: Ez a megoldás üzembe helyez egy másik alhálózatot, adatbázis-alhálózat, az Active Directory-alhálózathoz és felügyeleti alhálózaton egy virtuális gépen belüli – rendelkező erőforrásokat. Alhálózatok logikailag elválasztott korlátozhatja, hogy csak szükséges rendszer- és felügyeleti funkciók, az alhálózatok közötti adatforgalom az egyes alhálózatokra alkalmazott hálózati biztonsági csoport szabályait.

Tekintse meg a konfiguráció a [hálózati biztonsági csoportok](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) üzembe helyezett ezzel a megoldással. Szervezetek számára a hálózati biztonsági csoportok fent a fájl szerkesztésével állíthatja [ebben a dokumentációban](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) alapján.

Dedikált hálózati biztonsági csoport minden egyes alhálózatban van:
- 1 hálózati biztonsági csoport az Application Gateway (LBNSG)
- 1 hálózati biztonsági csoportot a bástyagazdagép (MGTNSG)
- 1 hálózati biztonsági csoportot az elsődleges és tartalék tartományvezérlők (ADNSG)
- 1 hálózati biztonsági csoportot az SQL Server-kiszolgálók és a Felhőbeli tanúsító (SQLNSG)
- 1 hálózati biztonsági csoportot a webes réteg (WEBNSG)

### <a name="data-in-transit"></a>Az átvitt adatok
Az Azure és az Azure adatközpontok bemenő kommunikáció alapértelmezés szerint titkosítja. Emellett az Azure Portalon az Azure Storage összes tranzakció HTTPS-kapcsolaton keresztül történik.

### <a name="data-at-rest"></a>Inaktív adat
Az architektúra használatával több mértéket, beleértve a titkosítást és -adatbázis naplózási szolgáltatásával az inaktív adatok védi.

**Az Azure Storage**: rest-követelményeket, a titkosított adatok kielégítése érdekében minden [Azure Storage](https://azure.microsoft.com/services/storage/) használ [a Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption). Ez segít a szervezeti biztonsági kötelezettségeit, és a NIST 800-171 SP által meghatározott megfelelőségi követelmények támogatásához adatok biztonságos megőrzésében.

**Az Azure Disk Encryption**: az Azure Disk Encryption Windows IaaS virtuális gépek lemezeinek titkosított szolgál. [Az Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) kötettitkosítást biztosít az operációs rendszer és az adatlemezek Windows BitLocker funkcióját használja. A megoldás integrálva van az Azure Key Vault segítségével szabályozhatja, és kezelhetik a lemeztitkosítási kulcsokat.

**Az SQL Server**: az SQL Server-példányt használja a következő adatbázis biztonsági intézkedéseket:
-   [Az SQL Server-naplózás](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine?view=sql-server-2017) nyomon követi az adatbázisok eseményeit, és írja őket az auditnaplók.
-   [Transzparens adattitkosítás](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) hajtja végre a valós idejű titkosításához és visszafejtéséhez az adatbázis, azokhoz kapcsolódó biztonsági mentési és tranzakciós naplófájlokra adatok inaktív védelme érdekében. Transzparens adattitkosítás biztosítja, hogy a tárolt adatok nem lett bárki hozzáférhet.
-   [Tűzfalszabályok](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) megakadályozzák elérését, adatbázis-kiszolgálók, amíg a megfelelő engedélyekkel. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján.
-   [Titkosított oszlopokat](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-wizard?view=sql-server-2017) győződjön meg arról, hogy bizalmas adatok soha nem jelenik meg, mint belül az adatbázis-rendszer egyszerű szövegként. Adatok titkosításának engedélyezése után csak az ügyfélalkalmazások vagy alkalmazáskiszolgálók hozzáférést a kulcsokhoz hozzáférhet egyszerű szöveges adatokat.
- [Dinamikus adatmaszkolás](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?view=sql-server-2017) korlátozza a bizalmas adatok maszkolása az adatokat nem kiemelt jogosultságú felhasználók vagy alkalmazások által. Dinamikus adatmaszkolás automatikusan potenciálisan bizalmas adatok felderítéséhez, és javasolja a alkalmazni lehessen a megfelelő maszkokkal. Ez csökkenti a hozzáférés úgy, hogy a bizalmas adatok nem létezik az adatbázis jogosulatlan hozzáférést az nyújtanak segítséget. **Dinamikus adatmaszkolási beállítások igazodnia kell az adatbázis-séma módosításával ügyfelek felelőssége.**

### <a name="identity-management"></a>Identitáskezelés
A következő technológiákat az Azure-beli adatokhoz való hozzáférés kezeléséhez képességeket biztosítják:
-   [Az Azure Active Directory](https://azure.microsoft.com/services/active-directory/) van a Microsoft több-bérlős felhőalapú címtár- és identitáskezelési szolgáltatás. Ez a megoldás az összes felhasználó jönnek létre az Azure Active Directoryban, beleértve az SQL Server hozzáférő felhasználók.
-   Az alkalmazás-hitelesítést az Azure Active Directory használatával történik. További információkért lásd: [alkalmazások integrálása az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).
-   [Azure szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) lehetővé teszi a rendszergazdák számára, hogy a felhasználóknak frissíteniük kell a munkája elvégzéséhez csak olyan mértékű hozzáférést biztosítson a minden részletre kiterjedő hozzáférési engedélyek megadása. Helyett az Azure-erőforrások minden felhasználó ugyanolyan korlátlan engedélyeket ad, a rendszergazdák engedélyezhetik csak bizonyos adatok eléréséhez szükséges műveleteket. Az előfizetés-rendszergazda előfizetéshez való hozzáférés korlátozódik.
- [Az Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) lehetővé teszi az ügyfeleknek minimalizálása érdekében a felhasználóknak a számát, aki hozzáféréssel rendelkezik az egyes erőforrásokhoz.  Az Azure Active Directory Privileged Identity Management rendszergazdái a felderítését, korlátozását és emelt szintű identitások és azok erőforrásokhoz való hozzáférésének figyelése. Ez a funkció is használható igény szerinti – igény rendszergazdai hozzáférés kényszerítésére.
- [Az Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) észleli a szervezet identitásait érintő lehetséges biztonsági résekről, konfigurálja az automatikus reakciók, a szervezet identitásait kapcsolatos észlelt gyanús tevékenységeket és folytat gyanús incidensek a megfelelő műveletet a problémák megoldásához.

### <a name="security"></a>Biztonság
**Titkok kezelése**: A megoldás [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) kulcsok és titkos kulcsok felügyeletéhez. Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. Az alábbi Azure Key Vault-képességek segítségével az adatok védelme ügyfelek:
- Speciális hozzáférési szabályzatok kell alapon vannak konfigurálva.
- A Key Vault hozzáférési szabályzatok a minimálisan szükséges engedélyeket kulcsok és titkos kulcsok vannak meghatározva.
- Összes kulcsot és titkos kulcsok a Key Vaultban van lejárati dátumát.
- A kulcs típusa a hardveres biztonsági modul által védett speciális hardveres biztonsági modulok által védett összes kulcsok a Key Vaultban 2048-bites RSA-kulcsot.
- Minden felhasználó és identitások kapnak a szerepköralapú hozzáférés-vezérlés használatával minimálisan szükséges engedélyeket.
- Diagnosztikai naplók a Key vault legalább 365 napos megőrzési idővel rendelkező engedélyezve vannak.
- A szükséges kapcsolatok engedélyezett titkosítási műveletek kulcsok korlátozódnak.
- A megoldás integrálva van az Azure Key Vault IaaS virtuális gépek lemeztitkosítási kulcsokat és titkos kulcsok kezeléséhez.

**Javítások kezelése**: Ez a referenciaarchitektúra részeként üzembe helyezett Windows virtuális gépek úgy vannak konfigurálva, az automatikusan frissítéseket kapjanak a Windows Update szolgáltatás alapértelmezés szerint. Ez a megoldás is magában foglalja a [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) szolgáltatást, amelyen frissített telepítések hozható létre a javítás virtuális gépekhez szükség esetén.

**Kártevők elleni védekezés**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) virtuális gépek számára biztosítja a valós idejű védelem funkció, amely alapján azonosíthatja, és távolítsa el a vírusok, kémprogramok és más, kártevő szoftverek, konfigurálható riasztásokkal Ha az ismert kártevő vagy nemkívánatos szoftverek megpróbálják telepíteni vagy futtatni védett virtuális gépeken.

**Az Azure Security Center**: A [az Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), ügyfelek is központilag alkalmazása és a számítási feladatok biztonsági házirendek kezelése, korlátozhatja a fenyegetéseknek való kitettséget, felismeri és elháríthatja a támadásokat. Az Azure Security Center ezenkívül meglévő konfigurációk az Azure-szolgáltatások konfigurációs és szolgáltatási javaslatok javíthatja biztonsági helyzetét és adatok védelme érdekében fér hozzá.

Az Azure Security Center észlelési képességek széles használatával ügyfeleket a környezetük célzó lehetséges támadások esetén. Ezek a riasztások értékes információkat tartalmaznak arról, hogy mi váltotta ki a riasztást, valamint a támadás forrásáról és az általa célba vett erőforrásokról. Az Azure Security Center készletével rendelkezik [biztonsági riasztások az előre meghatározott](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), amely vannak aktiválódik, ha a fenyegetések vagy gyanús tevékenységek. [Egyéni riasztási szabályok](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) az Azure Security Center lehetővé teszi ügyfeleink számára a környezetből már begyűjtött adatok alapján új biztonsági riasztásokat definiálhat.

Az Azure Security Center itt rangsorolt biztonsági riasztások és incidensek, így egyszerűbb a vásárlók megtalálhatják és a potenciális biztonsági problémákat. A [fenyegetésfelderítési jelentés](https://docs.microsoft.com/azure/security-center/security-center-threat-report) jön létre a rendszer minden egyes észlelt fenyegetés incidensmegoldási csapat segítség vizsgálatában és védekezhet a fenyegetések.

Ezenkívül ez a referenciaarchitektúra használja a [biztonságirés-értékelési](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) az Azure Security Centerben. Konfigurálása után a partnerügynök (pl. Qualys) jelenti a biztonsági adatok a partner felügyeleti platformjára. Ugyanakkor a partner felügyeleti platformjára biztosít biztonsági résekre vonatkozó és állapotmonitorozási adatokat vissza az Azure Security Center lehetővé teszi az ügyfelek számára, hogy gyorsan azonosíthatja a sebezhető virtuális gépeket.

**Az Application Gateway**: az architektúra csökkenti a biztonsági rések használ, az Application Gateway webalkalmazási tűzfal konfigurálása és az OWASP szabálykészletben engedélyezve kockázatát. További képességek:

- [Vége a közötti SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Engedélyezése [SSL-alapú Kiszervezéshez](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Tiltsa le [a TLS 1.0 és 1.1 verzió](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Webalkalmazási tűzfal](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (megelőzés üzemmód)
- [Megelőzés üzemmód](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) az OWASP 3.0 szabálykészletben
- Engedélyezése [diagnosztikai naplózás](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Egyéni állapot-mintavételei](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- [Az Azure Security Center](https://azure.microsoft.com/services/security-center) és [az Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) nyújtanak további védelmet és értesítések. Az Azure Security Center megbízhatóságibesorolás-kezelési rendszert is biztosít.

### <a name="business-continuity"></a>Az üzletmenet folytonossága

**Magas rendelkezésre állású**: A megoldás összes virtuális gépet helyez üzembe egy [rendelkezésre állási csoport](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Rendelkezésre állási csoportok biztosítják, hogy a virtuális gépek rendelkezésre állás javítása érdekében több elkülönített hardverfürt között legyenek elosztva. Legalább egy virtuális gép érhető el egy tervezett vagy nem tervezett karbantartási események esetén teljesíti a 99,95 %-os Azure SLA-t.

**Recovery Services-tároló**: A [Recovery Services-tároló](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) Kezelőkód biztonsági mentési adatokat, és védelmet biztosít az összes konfiguráció az Azure Virtual Machines ebben az architektúrában. Recovery Services-tárolóval, ügyfelek is fájlok és mappák visszaállítása az IaaS virtuális gépről a teljes virtuális Gépet, így gyorsabb helyreállítást visszaállítása nélkül.

**Felhőbeli tanúsító**: [Felhőbeli tanúsító](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) egy feladatátvevő fürt kvórum tanúsítójának a Windows Server 2016, amely Azure-t az egyeztetési pontként típusú. A Felhőbeli tanúsító – más kvórumtanúsítókhoz, például szavazattal rendelkezik, és részt vehet a kvórumszámításokban, de a standard szintű, nyilvánosan elérhető Azure Blob Storage használ. Ezzel elkerülhető a felesleges karbantartást a nyilvános felhőben üzemeltetett virtuális gépek.

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

**Az Azure Automation**: [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) tárolja, fut, és kezeli a runbookok. Ebben a megoldásban a runbookok naplók gyűjtése az Azure SQL Server segítségével. Az Automation [Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) megoldás lehetővé teszi, hogy az ügyfelek számára, hogy könnyen azonosíthassa a változtatásokat a környezetben.

**Az Azure Monitor**: [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) segít a felhasználóknak a teljesítmény, biztonság fenntartására és a trendek azonosítására által lehetővé teszi a cégeknek naplózása, riasztásokat hozhat létre, és archiválja az adatokat, többek között API-hívások nyomon követése az Azure-ban az erőforrásokat.

## <a name="threat-model"></a>Fenyegetések modellezése

A referenciaarchitektúra az adatfolyam-diagram érhető el az [letöltése](https://aka.ms/nist171-iaaswa-tm) vagy alább találja. Ez a modell segítségével az ügyfelek megismerhetik a pontokat a rendszer infrastruktúra esetleges kockázat, ha a végzett módosítások.

![IaaS-webalkalmazás NIST SP 800-171 fenyegetések modellezése](images/nist171-iaaswa-threat-model.png "NIST SP 800-171 fenyegetések modellezése az IaaS-webalkalmazás")

## <a name="compliance-documentation"></a>Megfelelőségi dokumentációja

A [Azure biztonsági és megfelelőségi terv - 800-171 NIST SP vevő felelőssége mátrix](https://aka.ms/nist171-crm) NIST SP 800-171 által igényelt összes biztonsági vezérlő sorolja fel. Ez a mátrix részletezi a Microsoft, az ügyfél felelőssége végrehajtására az egyes vezérlőelemek-e, vagy a kettő között megosztott.

A [Azure biztonsági és -megfelelőségi terv - 800-171 NIST SP IaaS webes alkalmazás ellenőrzési megvalósítási mátrix](https://aka.ms/nist171-iaaswa-cim) melyik NIST SP a 800-171 szabályozza az IaaS webalkalmazás-architektúra, amelyekre az információival például hogyan végrehajtása megfelel egyes az érintett vezérlő részletes leírást.

## <a name="guidance-and-recommendations"></a>Útmutatás és javaslatok
### <a name="vpn-and-expressroute"></a>VPN és ExpressRoute
Biztonságos VPN-alagúton vagy [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) konfigurálni kell a biztonságos kapcsolatot az IaaS-webalkalmazás referenciaarchitektúra részeként üzembe helyezett erőforrásokat. Megfelelően állítja be a VPN-t vagy ExpressRoute, az ügyfelek hozzáadhatja egy védelmi réteget biztosít adatokat átvitel közben.

Az Azure-ral biztonságos VPN-alagút alkalmazásával egy helyszíni hálózat és a egy Azure virtuális hálózat közötti virtuális magánhálózati kapcsolat hozható létre. Ezt a kapcsolatot az interneten keresztül történik, és lehetővé teszi az ügyfelek számára, hogy egy titkosított kapcsolatot, az ügyfél hálózati és az Azure között lévő biztonságosan "csatorna" információkat. Site-to-site VPN egy olyan biztonságos, érett technológia, évtizedes a vállalatok által központilag telepített. A [IPsec-alagút módú](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) ezt a beállítást, mint egy titkosítási mechanizmus használatban van.

Forgalom a VPN-alagút haladnak át a helyek közötti VPN-nel az interneten, mert a Microsoft kínál egy másik, még biztonságosabb kapcsolat lehetőséget. Az Azure ExpressRoute dedikált WAN Azure-ban és a egy helyszíni helyhez vagy egy Exchange-szolgáltató közötti kapcsolat. Az ExpressRoute-kapcsolatok az interneten keresztül halad, mivel a kapcsolatok az interneten keresztül ajánlja fel a további megbízhatóság megbízhatóbbak, gyorsabbak, kisebb a késésük, és biztonságosabbak a szokásos internetkapcsolatoknál. Továbbá mivel ez az ügyfél távközlési szolgáltató közvetlen kapcsolatot, az adatok nem az interneten keresztül továbbítani tartalmaz, és ezért nem érhető el.

Ajánlott eljárások végrehajtásához egy biztonságos hibrid hálózatot, amely kiterjeszti a helyszíni hálózatot az Azure-bA [elérhető](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Jogi nyilatkozat

- Ez a dokumentum csak tájékoztatási célokat szolgál. A MICROSOFT NEM VÁLLAL SEMMILYEN KIFEJEZETT, KIFEJEZETT, VÉLELMEZETT VAGY KÖTELEZŐ GARANCIÁT A DOKUMENTUMBAN SZEREPLŐ INFORMÁCIÓRA VONATKOZÓAN. Ez a dokumentum biztosított "as-van." Információk és vélemények ebben a dokumentumban URL-CÍMEK és más internetes webhelyekre utaló hivatkozások értesítés nélkül változhatnak. Ez a dokumentum olvasásakor ügyfelek felelősségére használja.  
- Ez a dokumentum nem biztosít semmilyen jogot semmilyen Microsoft-termék vagy a megoldások a szellemi tulajdonhoz ügyfelek.  
- Ügyfelek másolhatja és használhatja ezt a dokumentumot belső, hivatkozási célokból.  
- Bizonyos ajánlások a dokumentum eredményezhet megnövekedett adat-, hálózati vagy számítási erőforrás-használat az Azure-ban, és előfordulhat, hogy növelje az ügyfél Azure licencek vagy előfizetések költségeit.  
- Ez az architektúra célja, hogy az ügyfelek számára az adott követelményekhez beállítása alapjaként szolgálja ki, és nem használható-éles környezetben van.
- Ez a dokumentum referenciaként fejlesztik, és nem használható minden olyan eszközt, amely szerint egy ügyfél megfelel a megadott megfelelőségi követelmények és előírások meghatározásához. Ügyfelek jóváhagyott megvalósítás az ügyfeleknél a szervezet jogi támogatást kell kérnie.
