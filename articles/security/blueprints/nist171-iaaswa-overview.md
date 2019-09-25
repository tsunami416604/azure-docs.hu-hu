---
title: Azure Security and Compliance Blueprint-IaaS Web Application for NIST SP 800-171
description: Azure Security and Compliance Blueprint-IaaS Web Application NIST SP 800-171
services: security
author: jomolesk
ms.assetid: 1f1ad27f-32c3-4e76-abb9-ea768d01747f
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: 83d368e419550f38c173a7a1dca42c84db7d542f
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71259840"
---
# <a name="azure-security-and-compliance-blueprint---iaas-web-application-for-nist-sp-800-171"></a>Azure Security and Compliance Blueprint-IaaS Web Application for NIST SP 800-171

## <a name="overview"></a>Áttekintés
A 800-171-es [speciális kiadvány](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) a nem szövetségi információs rendszerekben és szervezeteknél található ellenőrzött, nem minősített információk (CUI-) védelmére vonatkozó irányelveket biztosít. A NIST SP 800-171 14 olyan biztonsági követelményt hoz létre, amely a CUI titkosságának védelmét védi.

Ez a Azure Security and Compliance Blueprint útmutatást nyújt az ügyfeleknek a webalkalmazás-architektúrák üzembe helyezéséhez az Azure-ban, amely a NIST SP 800-171 vezérlők egy részhalmazát valósítja meg. Ez a megoldás azt mutatja be, hogy az ügyfelek milyen módon tudják kielégíteni az egyes biztonsági és megfelelőségi követelményeket. Emellett alapja az ügyfelek számára, hogy saját webalkalmazásaikat hozzanak létre és konfiguráljanak az Azure-ban.

Ez a hivatkozási architektúra, a társított megvalósítási útmutató és a veszélyforrás-modell arra szolgál, hogy az ügyfelek számára a konkrét követelményekhez alkalmazkodva alapot biztosítson. Nem használhatók éles környezetben. Az architektúra módosítás nélküli üzembe helyezése nem elegendő ahhoz, hogy teljesen megfeleljen a NIST SP 800-171 követelményeinek. Az ügyfelek felelősek az architektúrával létrehozott megoldások megfelelő biztonsági és megfelelőségi felmérésének elvégzéséért. A követelmények az egyes ügyfelek implementációjának sajátosságai alapján változhatnak.

## <a name="architecture-diagram-and-components"></a>Architektúra ábrája és összetevői
Ez a Azure Security and Compliance Blueprint egy SQL Server háttérrel rendelkező IaaS-webalkalmazás hivatkozási architektúráját telepíti. Az architektúra egy webes szintet, adatszintet, Active Directory infrastruktúrát, Azure Application Gateway és Azure Load Balancer tartalmaz. A webes és az adatszinten üzembe helyezett virtuális gépek (VM-EK) egy rendelkezésre állási csoportban vannak konfigurálva. A magas rendelkezésre állás érdekében a SQL Server példányok egy always on rendelkezésre állási csoportba vannak konfigurálva. A virtuális gépek tartományhoz csatlakoznak. Active Directory csoportházirendek az operációs rendszer szintjén kényszerítik a biztonsági és megfelelőségi konfigurációkat.

A teljes megoldás az Azure Storage-ra épül, amelyet az ügyfelek a Azure Portal konfigurálnak. A Storage az összes adat titkosításával titkosítja a Storage Service Encryption az inaktív adatok titkosságának fenntartása érdekében. A Geo-redundáns tárolás biztosítja, hogy az ügyfél elsődleges adatközpontjában a kedvezőtlen esemény nem eredményez adatvesztést. A második példányt a rendszer egy külön helyen tárolja, több száz kilométer távolságban.

A fokozott biztonság érdekében az ebben a megoldásban lévő összes erőforrást erőforráscsoportként kezeli Azure Resource Manageron keresztül. A Azure Active Directory (Azure AD) szerepköralapú hozzáférés-vezérlés (RBAC) használatával szabályozható a telepített erőforrásokhoz és kulcsokhoz való hozzáférés a Azure Key Vaultban. A rendszer állapotának figyelése Azure Monitoron keresztül történik. Az ügyfelek mindkét figyelési szolgáltatást a naplók rögzítésére is konfigurálhatják. A rendszer állapota egyetlen irányítópulton jelenik meg, amely könnyen használható.

A felügyeleti megerősített gazdagépek biztonságos kapcsolatot biztosítanak a rendszergazdák számára az üzembe helyezett erőforrások eléréséhez. *A Microsoft azt javasolja, hogy VPN-vagy Azure ExpressRoute-kapcsolaton keresztül konfigurálja a felügyeleti és adatimportálási kapcsolatokat a hivatkozási architektúra alhálózatára.*


![IaaS Web Application for NIST SP 800-171 hivatkozási architektúra diagram](images/nist171-iaaswa-architecture.png "IaaS Web Application for NIST SP 800-171 hivatkozási architektúra diagram")

Ez a megoldás az alábbi Azure-szolgáltatásokat használja. További információ: [telepítési architektúra](#deployment-architecture) szakasz.

- Azure Virtual Machines
    - (1) felügyelet/megerősített (Windows Server 2016 Datacenter)
    - (2) Active Directory tartományvezérlő (Windows Server 2016 Datacenter)
    - (2) SQL Server fürtcsomópont (SQL Server 2017 a Windows Server 2016 rendszeren)
    - (2) Web/IIS (Windows Server 2016 Datacenter)
- Azure Virtual Network
    - (1)/16 hálózat
    - (5)/24 hálózat
    - (5) hálózati biztonsági csoportok
- Rendelkezésre állási csoportok
    - (1) Active Directory tartományvezérlők
    - (1) SQL-fürtcsomópontok
    - (1) Web/IIS
- Azure Application Gateway
    - (1) webalkalmazási tűzfal
        - Tűzfal mód: megelőzés
        - Szabály beállítása: OWASP 3,0
        - Figyelő portja: 443
- Azure Active Directory
- Azure Key Vault
- Azure Load Balancer
- Azure Monitor (naplók)
- Azure Resource Manager
- Azure Security Center
- Azure Storage
- Azure Automation
- Felhőbeli tanúsító
- Helyreállítási tár

## <a name="deployment-architecture"></a>Üzembe helyezési architektúra
A következő szakasz az üzembe helyezési és megvalósítási elemeket részletezi.

**Megerősített gazdagép**: A megerősített gazdagép az a belépési pont, amelyet a felhasználók a környezetbe helyezett erőforrások elérésére használhatnak. A megerősített gazdagép biztonságos kapcsolódást biztosít a központilag telepített erőforrásokhoz azáltal, hogy csak a nyilvános IP-címekről érkező távoli forgalmat biztonságos listaként engedélyezi. A távoli asztal forgalmának engedélyezéséhez meg kell határozni a forgalom forrását a hálózati biztonsági csoportban (NSG).

Ez a megoldás egy virtuális gépet hoz létre tartományhoz csatlakozó megerősített gazdagépként a következő konfigurációkkal:
-   [Antimalware-bővítmény](https://docs.microsoft.com/azure/security/fundamentals/antimalware).
-   [Azure Diagnostics bővítmény](../../virtual-machines/windows/extensions-diagnostics-template.md).
-   [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) Key Vault használatával.
-   [Automatikus leállítási szabályzat](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) , amely csökkenti a virtuálisgép-erőforrások felhasználását, ha nincs használatban.
-   A [Windows Defender hitelesítő adatainak](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) védelme engedélyezve van, így a hitelesítő adatok és egyéb Titkok a futó operációs rendszertől elkülönített védett környezetekben futnak.

### <a name="virtual-network"></a>Virtuális hálózat
Az architektúra a 10.200.0.0/16 címtartomány szerinti magánhálózati virtuális hálózatot definiálja.

**Hálózati biztonsági csoportok**: Ez a megoldás egy olyan architektúrán helyez üzembe erőforrásokat, amely külön alhálózatokat biztosít a webes, adatbázis-, Active Directory-és felügyelethez egy virtuális hálózaton belül. Az alhálózatokat az egyes alhálózatokra alkalmazott NSG-szabályok logikailag elkülönítik. A szabályok az alhálózatok közötti forgalmat csak a rendszer-és felügyeleti funkciókhoz szükséges módon korlátozzák.

Tekintse meg a megoldással üzembe helyezett [NSG](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) konfigurációját. A szervezetek az előző fájl szerkesztésével konfigurálhatják a NSG a [jelen dokumentáció](../../virtual-network/virtual-network-vnet-plan-design-arm.md) használatával.

Az alhálózatok mindegyike dedikált NSG rendelkezik:
- Egy NSG a Application Gatewayhoz (LBNSG)
- Egy NSG a megerősített gazdagéphez (MGTNSG)
- Egy NSG az elsődleges és a tartalék tartományvezérlők számára (ADNSG)
- Egy NSG az SQL-kiszolgálók és a Felhőbeli tanúsító (SQLNSG) számára
- Egy NSG webes réteghez (WEBNSG)

### <a name="data-in-transit"></a>Átvitt adatok
Az Azure alapértelmezés szerint titkosítja az Azure-adatközpontok felé irányuló és onnan érkező összes kommunikációt. Emellett a Azure Portalon keresztül lebonyolított összes tranzakció HTTPS-n keresztül történik.

### <a name="data-at-rest"></a>Inaktív adat
Az architektúra több mértéken keresztül védi a nyugalmi állapotban lévő adatok védelmét. Ezek a mértékek közé tartozik a titkosítás és az adatbázis naplózása.

**Azure Storage**: A titkosított adatokra vonatkozó követelmények teljesítése érdekében az [](https://azure.microsoft.com/services/storage/) összes tárterület [Storage Service Encryptiont](../../storage/common/storage-service-encryption.md)használ. Ez a szolgáltatás segít megvédeni és megőrizni az adatvédelmet a NIST SP 800-171 által meghatározott szervezeti biztonsági kötelezettségvállalások és megfelelőségi követelmények támogatásával.

**Azure Disk Encryption**: A lemezes titkosítás a Windows IaaS VM-lemezek titkosítására szolgál. A [lemezes titkosítás](../azure-security-disk-encryption-overview.md) a Windows BitLocker szolgáltatásával biztosítja a kötetek titkosítását az operációs rendszer és az adatlemezek számára. A megoldás integrálva van Key Vault a lemezes titkosítási kulcsok szabályozása és kezelése érdekében.

**SQL Server**: A SQL Server példány a következő adatbázis-biztonsági mértékeket használja:
-   [SQL Server naplózás](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine?view=sql-server-2017) nyomon követi az adatbázis eseményeit, és beírja azokat a naplókba.
-   Az [transzparens adattitkosítás](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) az adatbázis, a társított biztonsági másolatok és a tranzakciós naplófájlok valós idejű titkosítását és visszafejtését hajtja végre a további információk védelme érdekében. Az transzparens adattitkosítás biztosítja, hogy a tárolt adataik nem vonatkoznak a jogosulatlan hozzáférésre.
-   A [Tűzfalszabályok](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) megakadályozzák az adatbázis-kiszolgálók hozzáférését a megfelelő engedélyek megadása előtt. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján.
-   A [titkosított oszlopok](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-wizard?view=sql-server-2017) biztosítják, hogy a bizalmas adatok soha nem egyszerű szövegként jelenjenek meg az adatbázis-rendszeren belül. Az adattitkosítás engedélyezése után csak a kulcsokhoz hozzáféréssel rendelkező ügyfélalkalmazások vagy kiszolgálóalkalmazások férhetnek hozzá az egyszerű szöveges adatértékekhez.
- A [dinamikus adatmaszkolás](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?view=sql-server-2017) korlátozza a bizalmas adatokra való adatvédelmet azáltal, hogy az adatokat nem Kiemelt felhasználók vagy alkalmazások számára fedi le. A szolgáltatás automatikusan képes felderíteni a potenciálisan bizalmas adatokat, és javaslatot tesz a megfelelő maszkok alkalmazására. A dinamikus adatmaszkolás segít csökkenteni a hozzáférést, hogy a bizalmas adatok jogosulatlan hozzáférés nélkül ne lépjenek ki az adatbázisból. *Az ügyfelek felelősek a beállítások módosításához az adatbázis-sémájuk betartásához.*

### <a name="identity-management"></a>Identitáskezelés
Az alábbi technológiák az Azure-környezetben tárolt adathozzáférések kezelésére szolgáló képességeket biztosítanak:
-   Az [Azure ad](https://azure.microsoft.com/services/active-directory/) a Microsoft több-bérlős felhőalapú címtár-és Identitáskezelés-kezelő szolgáltatása. Az ehhez a megoldáshoz tartozó összes felhasználó létrejön az Azure AD-ben, és tartalmazza azokat a felhasználókat, akik hozzáférnek a SQL Server-példányhoz.
-   Az alkalmazáshoz való hitelesítés az Azure AD használatával történik. További információ: [alkalmazások integrálása az Azure ad-vel](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md).
-   Az [Azure RBAC](../../role-based-access-control/role-assignments-portal.md) lehetővé teheti a rendszergazdák számára, hogy részletes hozzáférési engedélyeket határozzanak meg, amelyek csak a felhasználóknak a feladataik elvégzéséhez szükséges hozzáférési engedélyek megadására szolgálnak. Ahelyett, hogy az összes felhasználó számára korlátlan engedélyeket adna az Azure-erőforrásokhoz, a rendszergazdák csak bizonyos műveleteket végezhetnek el az adatokhoz való hozzáféréshez. Az előfizetés-hozzáférés az előfizetés rendszergazdájára korlátozódik.
- A [Azure Active Directory Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) az ügyfelek használhatják az egyes erőforrásokhoz hozzáférő felhasználók számának minimalizálására. A rendszergazdák Azure AD Privileged Identity Management használhatják az emelt szintű identitások felderítését, korlátozását és figyelését, valamint az erőforrásokhoz való hozzáférésüket. Ez a funkció az igény szerinti, igény szerinti rendszergazdai hozzáférés biztosítására is használható, ha szükséges.
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) észleli a szervezet identitásait érintő lehetséges biztonsági réseket. Automatikus válaszokat konfigurál a szervezet identitásával kapcsolatos gyanús műveletek észlelésére. Emellett a gyanús incidenseket is megvizsgálja, hogy megfelelő lépéseket tegyenek a megoldásához.

### <a name="security"></a>Biztonság
**Titkok kezelése**: A megoldás a kulcsok és titkok kezeléséhez [Key Vault](https://azure.microsoft.com/services/key-vault/) használ. Key Vault segít megőrizni a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsokat és titkokat. Az alábbi Key Vault-képességek segítenek az ügyfeleknek az adatvédelemben:
- A speciális hozzáférési szabályzatok a szükséges módon vannak konfigurálva.
- Key Vault hozzáférési házirendek minimálisan szükséges engedélyekkel vannak definiálva a kulcsokhoz és a titkokhoz.
- Key Vault összes kulcsának és titkának lejárati dátuma van.
- A Key Vault összes kulcsát speciális hardveres biztonsági modulok védik. A kulcs típusa egy hardveres biztonsági modul által védett 2048 bites RSA-kulcs.
- Minden felhasználó és identitás minimálisan szükséges engedélyeket kap a RBAC használatával.
- Key Vault diagnosztikai naplói legalább 365 napos megőrzési időtartammal engedélyezettek.
- A kulcsok számára engedélyezett titkosítási műveletek csak a szükségesek.
- A megoldás integrálva van Key Vault a IaaS virtuális gép lemez-titkosítási kulcsainak és titkos kulcsainak kezeléséhez.

**Javítási felügyelet**: A jelen viszonyítási architektúra részeként telepített Windows rendszerű virtuális gépek alapértelmezés szerint a Windows Update szolgáltatásból származó automatikus frissítések fogadására vannak konfigurálva. Ez a megoldás tartalmazza azt a [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) szolgáltatást is, amelyen keresztül a frissített központi telepítések létrehozhatók a virtuális gépek szükség szerinti javításához.

**Kártevők elleni védelem**: A virtuális gépekhez készült [Microsoft antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware) olyan valós idejű védelmi képességet biztosít, amely lehetővé teszi a vírusok, kémprogramok és más kártevő szoftverek azonosítását és eltávolítását. Az ügyfelek olyan riasztásokat állíthatnak be, amelyek az ismert kártékony vagy nemkívánatos szoftverek által a védett virtuális gépeken történő telepítésére vagy futtatására tett kísérleteket eredményeznek.

**Azure Security Center**: A [Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)segítségével az ügyfelek központilag alkalmazhatják és kezelhetik a munkaterhelések biztonsági szabályzatait, korlátozhatja a fenyegetéseket, és észlelheti és reagálhat a támadásokra. Security Center az Azure-szolgáltatások meglévő konfigurációit is elérheti, hogy konfigurációs és szolgáltatási javaslatokat nyújtson a biztonsági helyzetek és az adatvédelem javításához.

Security Center különböző észlelési képességekkel figyelmezteti az ügyfeleket a környezetét célba vevő lehetséges támadásokra. Ezek a riasztások értékes információkat tartalmaznak arról, hogy mi váltotta ki a riasztást, valamint a támadás forrásáról és az általa célba vett erőforrásokról. A Security Center [előre meghatározott biztonsági riasztásokat](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) tartalmaz, amelyek egy fenyegetés vagy gyanús tevékenység bekövetkeztekor aktiválódnak. Az ügyfelek [Egyéni riasztási szabályok](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) segítségével meghatározhatnak új biztonsági riasztásokat a környezetből már összegyűjtött adatok alapján.

A Security Center rangsorolt biztonsági riasztásokat és incidenseket biztosít. Security Center megkönnyíti az ügyfelek számára a potenciális biztonsági problémák felderítését és megoldását. Minden észlelt fenyegetéshez létrejön egy [fenyegetési intelligencia jelentés](https://docs.microsoft.com/azure/security-center/security-center-threat-report) . Az incidensek válaszait használó csapatok a jelentéseket a fenyegetések kivizsgálására és szervizelésére használhatják.

Ez a hivatkozási architektúra a [sebezhetőség-felmérési](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) funkciót használja Security Centerban. A konfigurálását követően a partner ügynök (például a Qualys) biztonsági réseket küld a partner felügyeleti platformjának. Ezután a partner felügyeleti platformja a biztonsági résekre vonatkozó és állapotmonitorozási adatokat küld vissza a Security Centernek. Az ügyfelek ezeket az információkat a sebezhető virtuális gépek gyors azonosítására használhatják.

**Azure Application Gateway**: Az architektúra csökkenti a biztonsági rések kockázatát, ha olyan Application Gateway-t használ, amely konfigurálva van egy webalkalmazási tűzfallal, és a OWASP szabálykészlet engedélyezve van. A további funkciók a következők:

- [End-to-end-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- Engedélyezze az [SSL-kiszervezést](../../application-gateway/create-ssl-portal.md).
- Tiltsa le [a TLS 1.0-s és 1.1-es verzióit](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- [Webalkalmazási tűzfal](../../application-gateway/waf-overview.md) (megelőzési mód).
- [Megelőzési mód](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) a OWASP 3,0 szabálykészlet beállításával.
- [Diagnosztikai naplózás](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)engedélyezése.
- [Egyéni állapot](../../application-gateway/quick-create-portal.md)-mintavételek.
- A [Security Center](https://azure.microsoft.com/services/security-center) és [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) további védelmet és értesítéseket biztosítanak. A Security Center a hírnév rendszerét is biztosítja.

### <a name="business-continuity"></a>Az üzletmenet folytonossága

**Magas rendelkezésre állás**: A megoldás minden virtuális gépet üzembe helyez egy [rendelkezésre állási csoporton](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)belül. A rendelkezésre állási csoportok biztosítják, hogy a virtuális gépek több elkülönített hardveres fürt között legyenek elosztva a rendelkezésre állás javítása érdekében. Legalább egy virtuális gép elérhető egy tervezett vagy nem tervezett karbantartási esemény során, amely megfelel az 99,95%-os Azure SLA-nak.

**Recovery Services**tároló: Az [Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) -tároló a biztonsági mentési és az Azure-Virtual Machines összes konfigurációját védi ebben az architektúrában. Recovery Services-tárolóval az ügyfelek a teljes virtuális gép visszaállítása nélkül állíthatják vissza a fájlokat és mappákat egy IaaS virtuális gépről. Ez a folyamat felgyorsítja a visszaállítási időt.

**Felhőbeli tanúsító**: A [Felhőbeli tanúsító](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) a feladatátvevő fürt Kvórumának tanúsító típusa a Windows Server 2016 rendszerben, amely az Azure-t használja a választottbírósági pontként. A Felhőbeli tanú, mint bármely más kvórum, szavaz, és részt vehet a kvórum számításaiban. A szabványos nyilvánosan elérhető Azure Blob Storage-tárolót használja. Ez a megoldás megszünteti a nyilvános felhőben üzemeltetett virtuális gépek extra karbantartási terhelését.

### <a name="logging-and-auditing"></a>Naplózás

Az Azure-szolgáltatások széles körben naplózzák a rendszer és a felhasználó tevékenységét, valamint a rendszer állapotát:
- **Tevékenységek naplói**: A [tevékenységek naplói](../../azure-monitor/platform/activity-logs-overview.md) betekintést nyújtanak az előfizetésben lévő erőforrásokon végrehajtott műveletekre. A Tevékenységnaplók segítenek meghatározni a művelet kezdeményezőjét, az előfordulás időpontját és az állapotot.
- **Diagnosztikai naplók**: A [diagnosztikai naplók](../../azure-monitor/platform/resource-logs-overview.md) az összes erőforrás által kibocsátott összes naplót tartalmazzák. Ezek a naplók a Windows-események rendszernaplóit, a tárolási naplókat, a Key Vault a naplókat, valamint Application Gateway hozzáférési és tűzfal-naplókat tartalmaznak. Az összes diagnosztikai napló egy központi és titkosított Azure Storage-fiókba írja az archiválást. A felhasználók a megőrzési időtartamot akár 730 napig is konfigurálhatják, hogy megfeleljenek a rájuk vonatkozó követelményeknek.

**Naplók Azure monitor**: Ezeket a naplókat a rendszer a feldolgozás, tárolás és irányítópult-jelentéskészítés [Azure monitor naplófájljaiban](https://azure.microsoft.com/services/log-analytics/) összesíti. Az adatgyűjtés után a rendszer külön táblákba rendezi az adattípusokat Log Analytics munkaterületeken belül. Így az összes adatforrás együtt is elemezhető, az eredeti forrásától függetlenül. A Security Center Azure Monitor-naplókkal integrálódik. Az ügyfelek használhatnak Kusto-lekérdezéseket a biztonsági események adatainak eléréséhez és más szolgáltatásokból származó adatokkal való összekapcsolásához.

Az architektúra részeként az alábbi Azure- [figyelési megoldások](../../monitoring/monitoring-solutions.md) szerepelnek:
-   [Active Directory értékelés](../../azure-monitor/insights/ad-assessment.md): A Active Directory Health-ellenőrzési megoldás rendszeres időközönként kivizsgálja a kiszolgálói környezetek kockázatait és állapotát. A szolgáltatás a központilag telepített kiszolgálói infrastruktúrára vonatkozó javaslatok rangsorolt listáját tartalmazza.
- [SQL-értékelés](../../azure-monitor/insights/sql-assessment.md): Az SQL Health-ellenőrzési megoldás rendszeres időközönként kivizsgálja a kiszolgálói környezetek kockázatait és állapotát. Az ügyfelek számára az üzembe helyezett kiszolgálói infrastruktúrára jellemző ajánlások rangsorolt listáját kínálja.
- [Agent Health](../../monitoring/monitoring-solution-agenthealth.md): A Agent Health-megoldás azt jelenti, hogy hány ügynök van üzembe helyezve és a földrajzi eloszlása. Azt is jelenti, hogy hány ügynök nem válaszol, és az operatív adatküldő ügynökök száma.
-   [Activity log Analytics](../../azure-monitor/platform/collect-activity-logs.md): Az Activity Log Analytics-megoldás segítséget nyújt az Azure-tevékenységek naplóinak elemzéséhez az ügyfelek összes Azure-előfizetése között.

**Azure Automation**: Az [Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) tárolja, futtatja és felügyeli a runbookok. Ebben a megoldásban a runbookok segítséget nyújt a naplók SQL Serverból való gyűjtésében. Az Automation [change Tracking](../../automation/change-tracking.md) megoldás segítségével könnyedén azonosíthatja a környezet változásait.

**Azure monitor**: A [figyelő](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) segít a felhasználóknak a teljesítmény nyomon követésében, a biztonság fenntartásában és a trendek felismerésében. A szervezetek felhasználhatják a naplózásra, a riasztások létrehozására és az adatok archiválására. Emellett az Azure-erőforrásokban is nyomon követhetik az API-hívásokat.

## <a name="threat-model"></a>Veszélyforrások modellje

A viszonyítási architektúra adatáramlási diagramja [letölthető](https://aka.ms/nist171-iaaswa-tm) , vagy itt található. Ez a modell segítheti az ügyfeleket abban, hogy megértsék a rendszerinfrastruktúra lehetséges kockázatait, amikor módosításokat végeznek.

![IaaS-webalkalmazás a NIST SP 800-171 Threat modelhez](images/nist171-iaaswa-threat-model.png "IaaS-webalkalmazás a NIST SP 800-171 Threat modelhez")

## <a name="compliance-documentation"></a>Megfelelőségi dokumentáció

A [Azure Security and Compliance Blueprint-NIST sp 800-171 Customer felelősségi mátrix](https://aka.ms/nist171-crm) FELSOROLJA a NIST SP 800-171 által igényelt összes biztonsági ellenőrzést. Ez a mátrix részletesen ismerteti, hogy az egyes vezérlőelemek megvalósítása a Microsoft, az ügyfél vagy a kettő közötti megosztás feladata-e.

Az [Azure Security and Compliance Blueprint-NIST sp 800-171 IaaS Web Application Control megvalósítási mátrixa](https://aka.ms/nist171-iaaswa-cim) információt nyújt arról, hogy mely NIST SP 800-171-vezérlőkkel foglalkozik a IaaS webalkalmazás-architektúra. Részletes leírást tartalmaz arról, hogy a megvalósítás hogyan teljesíti az egyes kezelt vezérlők követelményeit.

## <a name="guidance-and-recommendations"></a>Útmutatás és javaslatok
### <a name="vpn-and-expressroute"></a>VPN-és ExpressRoute
A biztonságos VPN-alagutat vagy [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) úgy kell konfigurálni, hogy biztonságosan létesítsen kapcsolatot a IaaS webalkalmazás-hivatkozási architektúrájának részeként üzembe helyezett erőforrásokkal. A VPN-vagy ExpressRoute megfelelő beállításával az ügyfelek hozzáadhatnak egy védelmi réteget az átvitelhez.

A biztonságos VPN-alagút az Azure-nal való megvalósításával létrehozhat egy helyszíni hálózat és egy Azure-Virtual Network közötti virtuális magánhálózati kapcsolatot. Ez a kapcsolat az interneten keresztül zajlik. Az ügyfelek ezt a kapcsolatot az ügyfél hálózata és az Azure közötti titkosított kapcsolaton belül biztonságosan "alagút" információkkal használhatják. A helyek közötti VPN egy olyan biztonságos, érett technológia, amelyet évtizedek óta minden méretben üzembe helyezett a vállalatok. Ebben a beállításban az [IPsec-alagút mód](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) titkosítási mechanizmusként van használatban.

Mivel a VPN-alagúton belüli forgalom helyek közötti VPN-kapcsolaton keresztül halad át az interneten, a Microsoft még egy biztonságosabb kapcsolódási lehetőséget kínál. A ExpressRoute egy dedikált WAN-kapcsolat az Azure és egy helyszíni hely vagy egy Exchange-szolgáltató között. A ExpressRoute-kapcsolatok közvetlenül az ügyfél távközlési szolgáltatójának csatlakoznak. Ennek eredményeképpen az adat nem utazik az interneten keresztül, és nem teszi elérhetővé. Ezek a kapcsolatok nagyobb megbízhatóságot, gyorsabb sebességet, kisebb késést és nagyobb biztonságot nyújtanak, mint a szokásos kapcsolatok. 

Ajánlott eljárások egy biztonságos hibrid hálózat megvalósításához, amely kiterjeszti a helyszíni hálózatot az Azure- [ra.](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)

## <a name="disclaimer"></a>Jogi nyilatkozat

- Ez a dokumentum csak tájékoztató jellegű. A MICROSOFT NEM VÁLLAL SEMMILYEN KIFEJEZETT, VÉLELMEZETT VAGY TÖRVÉNYES GARANCIÁT A JELEN DOKUMENTUMBAN FOGLALT INFORMÁCIÓK ALAPJÁN. Ez a dokumentum "aktuálisként" van megadva. Az ebben a dokumentumban kifejezett információk és nézetek, beleértve az URL-címeket és az internetes webhelyek más hivatkozásait, értesítés nélkül változhatnak. A dokumentumot olvasó ügyfeleink a használatuk kockázatát viselik. 
- A jelen dokumentum nem biztosít semmilyen jogot semmilyen Microsoft-termékben vagy-megoldásban található szellemi tulajdonhoz. 
- Az ügyfelek belső referenciák szerint másolhatják és használhatják ezt a dokumentumot. 
- A jelen dokumentumban szereplő javaslatok megnövelték az adatok, a hálózat vagy a számítási erőforrások használatát az Azure-ban, és növelhetik az ügyfelek Azure-licencét vagy előfizetési költségeit. 
- Ez az architektúra arra szolgál, hogy az ügyfelek számára a konkrét követelményekhez igazodva és nem használható éles környezetben.
- Ez a dokumentum hivatkozásként van kifejlesztve, és nem használható az összes olyan eszköz meghatározására, amelyekkel az ügyfél megfelel bizonyos megfelelőségi követelményeknek és előírásoknak. Az ügyfeleknek jóvá kell hagyniuk a szervezeten belüli jogi támogatást a jóváhagyott ügyfél-implementációkban.
