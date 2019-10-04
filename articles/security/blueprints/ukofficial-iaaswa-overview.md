---
title: Azure Security and Compliance Blueprint-háromrétegű IaaS-webalkalmazás az Egyesült Királyság hivatalos tisztviselőjének
description: Azure Security and Compliance Blueprint-háromrétegű IaaS-webalkalmazás az Egyesült Királyság hivatalos tisztviselőjének
services: security
author: jomolesk
ms.assetid: 9c32e836-0564-4906-9e15-f070d2707e63
ms.service: security
ms.topic: article
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 4a30e496c96fcc90417e58b0f921717985b89693
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262797"
---
# <a name="azure-security-and-compliance-blueprint---three-tier-iaas-web-application-for-uk-official"></a>Azure Security and Compliance Blueprint-háromrétegű IaaS-webalkalmazás az Egyesült Királyság hivatalos tisztviselőjének

## <a name="overview"></a>Áttekintés

 Ez a cikk útmutatást és automatizálási parancsfájlokat tartalmaz egy olyan Microsoft Azure háromrétegű webes architektúra biztosításához, amely az Egyesült királyságbeli TISZTVISELŐként besorolt számos munkaterhelés kezeléséhez szükséges.

 Az infrastruktúra kódként való használatával a [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) sablonok készlete olyan környezetet helyez üzembe, amely igazodik az Egyesült Királyság nemzeti Cyber Security Center (NCSC) 14 [Cloud biztonsági alapelveihez](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) és az Internet Security (CIS) központhoz. [ Kritikus biztonsági vezérlők](https://www.cisecurity.org/critical-controls.cfm).

 A NCSC javasoljuk, hogy az ügyfelek a Felhőbeli biztonsági alapelveit használják a szolgáltatás biztonsági tulajdonságainak kiértékelésére, valamint az ügyfél és a szállító közötti felelősség megosztásának megismerésére. Ezeknek az elveknek a segítségével megértettük a felelősségek felosztásának megértéséhez szükséges információkat.

 Ezt az architektúrát és a hozzá tartozó Azure Resource Manager sablonokat a Microsoft tanulmánya, az [Egyesült királyságbeli felhőhöz tartozó 14 Felhőbeli biztonsági vezérlő támogatja Microsoft Azure használatával](https://gallery.technet.microsoft.com/14-Cloud-Security-Controls-670292c1). Ez a tanulmány azt mutatja be, hogy az Azure-szolgáltatások hogyan illeszkednek az Egyesült Királyság NCSC 14 Felhőbeli biztonsági alapelveihez, ezáltal lehetővé téve a szervezetek számára, hogy a felhőalapú szolgáltatásokkal globálisan és az Egyesült Királyságon belül gyorsan nyomon kövessék a megfelelőségi kötelezettségeiket a Microsoft Azure felhő.

 Ez a sablon telepíti az infrastruktúrát a munkaterhelés számára. Telepíteni és konfigurálni kell az alkalmazás kódját és az üzleti szintek és az adatszinteket támogató szoftvereket. A részletes üzembe helyezési utasítások [itt](https://aka.ms/ukwebappblueprintrepo)érhetők el.

 Ha nem rendelkezik Azure-előfizetéssel, gyorsan és egyszerűen regisztrálhat [Az Azure](https://azure.microsoft.com/get-started/)-ba.

## <a name="architecture-diagram-and-components"></a>Architektúra ábrája és összetevői

 Az Azure-sablonok egy háromrétegű webalkalmazás-architektúrát biztosítanak egy olyan Azure-beli felhőalapú környezetben, amely támogatja az Egyesült Királyság hivatalos munkaterheléseit. Az architektúra olyan biztonságos hibrid környezetet biztosít, amely kiterjeszti a helyszíni hálózatot az Azure-ba, lehetővé téve a webes munkaterhelések biztonságos elérését a vállalati felhasználók és az internet között.

![Háromrétegű IaaS-webalkalmazás az Egyesült Királyság hivatalos hivatkozási architektúrájának diagramja](images/ukofficial-iaaswa-architecture.png?raw=true "Háromrétegű IaaS-webalkalmazás az Egyesült Királyság hivatalos hivatkozási architektúrájának diagramja")

 Ez a megoldás az alábbi Azure-szolgáltatásokat használja. Az üzembe helyezési architektúra részletei a [telepítési architektúra](#deployment-architecture) szakaszban találhatók.

(1)/16 Virtual Network – operatív VNet
- (3)/24 alhálózat – 3 réteg (web, biz, adatforrások)
- (1)/27 alhálózat – HOZZÁADJA
- (1)/27 alhálózat – átjáró-alhálózat
- (1)/29 alhálózat – Application Gateway alhálózat
- Alapértelmezett (Azure által biztosított) DNS-t használ
- Felügyelet VNet engedélyezve
- Hálózati biztonsági csoport (NSG) a forgalmi forgalom kezeléséhez

(1)/24 Virtual Network felügyeleti VNet
- (1)/27 alhálózat
- A (2) használja a DNS-t és (1) Azure DNS bejegyzéseket.
- Az operatív VNet való társítás engedélyezve
- Hálózati biztonsági csoport (NSG) a forgalmi forgalom kezeléséhez

(1) Application Gateway
- WAF – engedélyezve
- WAF mód – megelőzés
- Szabály beállítása: OWASP 3,0
- HTTP-figyelő a 80-as porton
- A NSG-en keresztül szabályozott kapcsolatok/forgalom
- Nyilvános IP-cím végpont definiálva (Azure)

(1) VPN-Route-based, site-2-site IPSec VPN-alagút
- Nyilvános IP-cím végpont definiálva (Azure)
- A NSG-en keresztül szabályozott kapcsolatok/forgalom
- (1) helyi hálózati átjáró (helyszíni végpont)
- (1) Azure hálózati átjáró (Azure-végpont)

(9) Virtual Machines – az összes virtuális gép üzembe helyezése az Azure IaaS antimalware DSC-beállításaival történik

- (2) Active Directory tartományi szolgáltatások tartományvezérlők (Windows Server 2012 R2)
  - (2) DNS-kiszolgálói szerepkörök – 1 virtuális gépenként
  - (2) az operatív VNet-hez csatlakozó hálózati adapterek – 1 virtuális gépenként
  - Mindkettő tartományhoz van csatlakoztatva a sablonban definiált tartományhoz.
    - A központi telepítés részeként létrehozott tartomány


- (1) Jumpbox (megerősített gazdagép) felügyeleti virtuális gép
  - 1 hálózati adapter a felügyeleti VNet nyilvános IP-címmel
    - A NSG az egyes forrásokra vonatkozó adatforgalom korlátozására szolgál
  - Nincs tartományhoz csatlakoztatva


- (2) webes szintű virtuális gépek
  - (2) IIS kiszolgálói szerepkörök – 1 virtuális gépenként
  - (2) az operatív VNet-hez csatlakozó hálózati adapterek – 1 virtuális gépenként
  - Nincs tartományhoz csatlakoztatva


- (2) BIZ-szintű virtuális gépek
  - (2) az operatív VNet-hez csatlakozó hálózati adapterek – 1 virtuális gépenként
  - Nincs tartományhoz csatlakoztatva


- (2) adatszintes virtuális gépek
  - (2) az operatív VNet-hez csatlakozó hálózati adapterek – 1 virtuális gépenként
  - Nincs tartományhoz csatlakoztatva

Rendelkezésre állási csoportok
- (1) Active Directory-tartomány vezérlő virtuálisgép-készlete – 2 virtuális gép
- (1) webrétegbeli virtuálisgép-készlet – 2 virtuális gép
- (1) BIZ-réteg virtuálisgép-készlete – 2 virtuális gép
- (1) adatrétegbeli virtuálisgép-készlet – 2 virtuális gép

Terheléselosztó
- (1) webes szintű Load Balancer
- (1) BIZ-szintű Load Balancer
- (1) az adatszinteket Load Balancer

Storage
- (14) összes Storage-fiók
  - Active Directory-tartomány vezérlő rendelkezésre állási készlete
    - (2) elsődleges helyileg redundáns tárolási (LRS) fiókok – 1 minden virtuális géphez  
    - (1) diagnosztikai helyileg redundáns tárolási (LRS) fiók a rendelkezésre állási csoport HOZZÁADÁSához
  - Felügyeleti Jumpbox VM
    - (1) az Jumpbox virtuális gép elsődleges helyileg redundáns tárolási (LRS) fiókja
    - (1) a Jumpbox virtuális gép diagnosztikai helyileg redundáns tárolási (LRS) fiókja
  - Webes szintű virtuális gépek
    - (2) elsődleges helyileg redundáns tárolási (LRS) fiókok – 1 minden virtuális géphez  
    - (1) a webes rétegek rendelkezésre állási csoportjának diagnosztikai, helyileg redundáns tárolási (LRS) fiókja
  - Biz-szintű virtuális gépek
    - (2) elsődleges helyileg redundáns tárolási (LRS) fiókok – 1 minden virtuális géphez  
    - (1) diagnosztikai helyileg redundáns tárolási (LRS) fiók az BIZ-rétegek rendelkezésre állási készletéhez
  - Adatszintes virtuális gépek
    - (2) elsődleges helyileg redundáns tárolási (LRS) fiókok – 1 minden virtuális géphez  
    - (1) az adatszinteket tartalmazó rendelkezésre állási csoport diagnosztikai, helyileg redundáns tárolási (LRS) fiókja

### <a name="deployment-architecture"></a>Üzembe helyezési architektúra:

Helyszíni **hálózat**: Egy szervezeten belül egy privát helyi hálózat implementálása.

**Üzemi VNet**: Az éles [VNet](https://docs.microsoft.com/azure/Virtual-Network/virtual-networks-overview) (Virtual Network) üzemelteti az alkalmazást és az Azure-ban futó egyéb működési erőforrásokat. Mindegyik VNet több alhálózatot is tartalmazhat, amelyek a hálózati forgalom elkülönítésére és kezelésére szolgálnak.

**Webes rétegek**: Kezeli a bejövő HTTP-kérelmeket. A rendszer a válaszokat ezen a szinten adja vissza.

**Üzleti szintek**: Üzleti folyamatokat és más funkcionális logikát valósít meg a rendszeren.

**Adatbázis szintje**: Állandó adattárolást biztosít a [SQL Server always on rendelkezésre állási csoportok](https://msdn.microsoft.com/library/hh510230.aspx) használatával a magas rendelkezésre állás érdekében. Az ügyfelek a [Azure SQL Databaset](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview) is használhatják.

**Átjáró**: A [VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) kapcsolatot biztosít a helyszíni hálózat és az üzemi VNet útválasztói között.

**Internet Gateway és nyilvános IP-cím**: Az Internet Gateway az interneten keresztül teszi elérhetővé az alkalmazások szolgáltatásait. A szolgáltatásokhoz való hozzáférés az [Application Gateway](../../application-gateway/overview.md) 7. rétegbeli útválasztási és terheléselosztási funkciókat biztosít a webalkalmazási TŰZFAL (WAF) védelmének használatával.

**Felügyeleti VNet**: Ez a [VNet](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) olyan erőforrásokat tartalmaz, amelyek felügyeleti és figyelési funkciókat implementálnak az üzemi VNet futó munkaterhelések számára.

**Jumpbox**: Más néven a [megerősített gazdagép](https://en.wikipedia.org/wiki/Bastion_host), amely egy biztonságos virtuális gép a hálózaton, amelyet a rendszergazdák a virtuális gépekhez használnak a termelési VNet. A jumpbox olyan NSG-vel rendelkezik, amely csak a biztonságos elemek listáján szereplő nyilvános IP-címekről érkező távoli forgalmat engedélyezi. A távoli asztal (RDP) forgalmának engedélyezéséhez meg kell határozni a forgalom forrását a NSG. Az üzemi erőforrások kezelése az RDP protokollon keresztül biztonságos Jumpbox virtuális géppel történik.

**Felhasználó által megadott útvonalak**: A [felhasználó által megadott útvonalakkal](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) határozható meg az Azure virtuális hálózatok belüli IP-forgalom folyamata.

**Hálózati társ virtuális hálózatok**: Az éles és a felügyeleti virtuális hálózatok a [VNet](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)-társítással kapcsolódnak egymáshoz.
Ezeket a virtuális hálózatok a rendszer továbbra is külön erőforrásként kezeli, de ezek a virtuális gépek minden kapcsolódási célra megjelennek. Ezek a hálózatok közvetlenül a magánhálózati IP-címek használatával kommunikálnak egymással. A VNet-társítás a virtuális hálózatok ugyanazon az Azure-régióban található.

**Hálózati biztonsági csoportok**: A [NSG](../../virtual-network/virtual-network-vnet-plan-design-arm.md) olyan Access Control-listát tartalmaznak, amelyek engedélyezik vagy megtagadják a forgalmat a VNet belül. A NSG használatával biztonságossá teheti a forgalmat egy alhálózaton vagy egy adott virtuálisgép-szinten.

**Active Directory tartományi szolgáltatások (AD DS)** : Ez az architektúra egy dedikált [Active Directory tartományi szolgáltatások](https://technet.microsoft.com/library/hh831484.aspx) -telepítést biztosít.

**Naplózás és naplózás**: Az [Azure-Tevékenységnaplók](../../azure-monitor/platform/activity-logs-overview.md) rögzítik az előfizetésben lévő erőforrásokon végrehajtott műveleteket, például a művelet elindítását, a művelet állapotát, a művelet állapotát és más olyan tulajdonságok értékeit, amelyek segíthetnek a művelet megkutatásában. Az Azure Activity log egy Azure platform-szolgáltatás, amely az előfizetések összes műveletét rögzíti. A naplók archiválása vagy exportálása szükség esetén lehetséges.

**Hálózati figyelés és riasztás**: Az [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) egy platform-szolgáltatás, amely hálózati csomagok rögzítését, flow-naplózást, topológiai eszközöket és diagnosztikát biztosít a virtuális hálózatok belüli hálózati forgalomhoz.

## <a name="guidance-and-recommendations"></a>Útmutatás és javaslatok

### <a name="business-continuity"></a>Üzleti folytonosság

**Magas rendelkezésre állás**: A kiszolgálói munkaterhelések egy [rendelkezésre állási](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) csoportba vannak csoportosítva, így biztosítva az Azure-beli virtuális gépek magas rendelkezésre állását. Ez a konfiguráció segít biztosítani, hogy a tervezett vagy nem tervezett karbantartási események esetén legalább egy virtuális gép elérhető legyen, és teljesítse a 99,95%-os Azure SLA-t.

### <a name="logging-and-audit"></a>Naplózás és naplózás

**Figyelés**: A [Azure monitor](../../azure-monitor/overview.md) a platform szolgáltatás, amely egyetlen forrást biztosít az Azure-erőforrások tevékenységi naplójának, metrikáinak és diagnosztikai naplóinak figyelésére. A Azure Monitor konfigurálható úgy, hogy megjelenítse, lekérdezze, átirányítsa és archiválja az Azure-ban lévő erőforrásokból származó mérőszámokat és naplókat. Azt javasoljuk, hogy az erőforrás-alapú Access Control a napló védelmére szolgáljon, így biztosítva, hogy a felhasználók ne tudják módosítani a naplókat.

**Tevékenységek naplói**: Konfigurálja az [Azure-tevékenység naplóit](../../azure-monitor/platform/activity-logs-overview.md) , hogy betekintést nyújtson az előfizetése erőforrásaiban végrehajtott műveletekre.

**Diagnosztikai naplók**: A [diagnosztikai naplók](../../azure-monitor/platform/resource-logs-overview.md) az adott erőforrás által kibocsátott naplók. Ezek a naplók lehetnek például a Windows-események rendszernaplói, a blob-, a tábla-és a várólista-naplók.

**Tűzfal naplófájljai**: A Application Gateway teljes körű diagnosztikai és hozzáférési naplókat biztosít. A tűzfalnaplók olyan Application Gateway-erőforrásokhoz érhetők el, amelyekhez engedélyezve van a WAF.

**Napló archiválása**: A naplózási adatok tárolása úgy konfigurálható, hogy egy központi Azure Storage-fiókba írja az archiválást és egy meghatározott megőrzési időtartamot. A naplók feldolgozhatók Azure Monitor naplók vagy harmadik féltől származó SIEM-rendszerek használatával.

### <a name="identity"></a>Identitás

**Active Directory tartományi szolgáltatások**: Ez az architektúra egy Active Directory tartományi szolgáltatások üzembe helyezést biztosít az Azure-ban. Az Active Directory Azure-ban való megvalósítására vonatkozó konkrét ajánlásokért tekintse meg az alábbi cikkeket:

[Active Directory tartományi szolgáltatások (AD DS) kiterjesztése az Azure-](/azure/architecture/reference-architectures/identity/adds-extend-domain)ra.

[Irányelvek a Windows Server Active Directory Azure Virtual Machines történő telepítéséhez](https://msdn.microsoft.com/library/azure/jj156090.aspx).

**Active Directory integráció**: A dedikált AD DS architektúra alternatívájaként előfordulhat, hogy az ügyfelek [Azure Active Directory](/azure/architecture/reference-architectures/identity) integrációt vagy Active Directoryt szeretnének használni az [Azure-ban egy helyszíni erdőhöz csatlakoztatva](/azure/architecture/reference-architectures/identity).

### <a name="security"></a>Biztonság

**Felügyeleti biztonság**: Ez a terv lehetővé teszi, hogy a rendszergazdák megbízható forrásból származó RDP használatával csatlakozzanak a felügyeleti VNet és Jumpbox. A felügyeleti VNet hálózati forgalma a NSG használatával vezérelhető. Az 3389-es porthoz való hozzáférés olyan megbízható IP-címtartományból érkező forgalomra korlátozódik, amely hozzáfér a Jumpbox tartalmazó alhálózathoz.

Az ügyfelek a felügyelet VNet és Jumpbox való csatlakozáskor is fontolóra vehetik a [fokozott biztonsági felügyeleti modell](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/securing-privileged-access) használatát a környezet biztonságossá tételéhez. Azt javasoljuk, hogy a fokozott biztonságú ügyfelek esetében használjon egy emelt [szintű hozzáférési munkaállomást](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/privileged-access-workstations#what-is-a-privileged-access-workstation-paw) és RDGateway-konfigurációt. A hálózati virtuális berendezések és a nyilvános/privát DMZ használata további biztonsági fejlesztéseket is biztosít.

**A hálózat biztonságossá tétele**: [Hálózati biztonsági csoportok](../../virtual-network/virtual-network-vnet-plan-design-arm.md) A (NSG) minden alhálózat esetében ajánlott, hogy második szintű védelmet biztosítson a bejövő forgalomnak a helytelenül konfigurált vagy letiltott átjárók megkerülése érdekében. Példa – [Resource Manager-sablon egy NSG üzembe helyezéséhez](https://github.com/mspnp/template-building-blocks/tree/v1.0.0/templates/buildingBlocks/networkSecurityGroups).

**Nyilvános végpontok biztonságossá tétele**: Az Internet Gateway az interneten keresztül teszi elérhetővé az alkalmazások szolgáltatásait. A szolgáltatásokhoz való hozzáférés egy [Application Gateway](../../application-gateway/overview.md), amely webalkalmazási tűzfalat és HTTPS protokoll-kezelést biztosít.

**IP-címtartományok**: Az architektúra IP-tartományai javasolt tartományok. Javasoljuk, hogy az ügyfelek saját környezetét vegyék figyelembe, és a megfelelő tartományokat használják.

**Hibrid kapcsolat**: A felhőalapú munkaterhelések az Azure VPN Gateway használatával csatlakoznak a helyszíni adatközponthoz az IPSEC VPN-en keresztül. Az ügyfeleknek biztosítaniuk kell, hogy megfelelő VPN Gateway használják az Azure-hoz való kapcsolódásra. Példa – [VPN Gateway Resource Manager-sablon](https://github.com/mspnp/template-building-blocks/tree/v1.0.0/templates/buildingBlocks/vpn-gateway-vpn-connection). A nagy léptékű, kritikus fontosságú számítási feladatokhoz big data követelményekkel rendelkező ügyfelek a Microsoft Cloud Services szolgáltatással való magánhálózati kapcsolat [ExpressRoute](/azure/architecture/reference-architectures/hybrid-networking/expressroute) használatával fontolóra vehetik a hibrid hálózati architektúrát.

**A problémáinak elkülönítése**: Ez a viszonyítási architektúra elkülöníti a virtuális hálózatok a felügyeleti műveletekhez és az üzleti műveletekhez. A különböző virtuális hálózatok és alhálózatok lehetővé teszik a forgalom kezelését, beleértve a bejövő és a kimenő forgalomra vonatkozó korlátozásokat, a [Microsoft Cloud Services és a hálózati biztonsággal kapcsolatos](/azure/architecture/vdc/networking-virtual-datacenter) ajánlott eljárások a hálózati szegmensek közötti NSG használatával.

**Erőforrás-kezelés**: Az Azure-erőforrásokat, például a virtuális gépeket, a virtuális hálózatok és a terheléselosztó-t az [Azure-erőforráscsoportok](../../azure-resource-manager/resource-group-overview.md)együttes használatával felügyelheti. Az erőforrás-alapú Access Control szerepkörök minden erőforráscsoport számára hozzárendelhetők, hogy csak a jogosult felhasználók hozzáférését korlátozzák.

**Access Control korlátozások**: A [szerepköralapú Access Control](../../role-based-access-control/role-assignments-portal.md) (RBAC) használatával kezelheti az alkalmazásban lévő erőforrásokat az [Egyéni szerepkörök](../../role-based-access-control/custom-roles.md) RBAC használatával korlátozhatja a DevOps által az egyes rétegeken végrehajtható műveleteket. Engedélyek megadásakor használja a [legalacsonyabb jogosultsági szint elvét](https://msdn.microsoft.com/library/hdb58b2f(v=vs.110).aspx#Anchor_1). Naplózzon minden felügyeleti műveletet, és rendszeresen végezzen ellenőrzést. Így meggyőződhet arról, hogy minden konfigurációmódosítás tervezett volt.

**Internet-hozzáférés**: Ez a viszonyítási architektúra az [Azure Application Gatewayt](../../application-gateway/overview.md) használja az internet felé irányuló átjáróként és a terheléselosztóként. Egyes ügyfelek fontolóra vehetik a külső hálózati virtuális berendezések használatát is a hálózati biztonság további rétegeihez az [Azure Application Gateway](../../application-gateway/overview.md)alternatívájaként.

**Azure Security Center**: A [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) az előfizetésben található erőforrások biztonsági állapotának központi nézetét nyújtja, és javaslatokat tesz a feltört erőforrások megelőzésére. Emellett részletesebb szabályzatok engedélyezésére is használható. Például a szabályzatok alkalmazhatók bizonyos erőforráscsoportok esetében, ami lehetővé teszi a vállalat számára, hogy kialakítsa a kockázatokat. Javasoljuk, hogy az ügyfelek az Azure-előfizetésben Azure Security Center engedélyezzék.

## <a name="ncsc-cloud-security-principles-compliance-documentation"></a>A NCSC Cloud Security alapelvei – megfelelőségi dokumentáció

A Crown kereskedelmi szolgáltatás (egy ügynökség, amely a kormányzat kereskedelmi és beszerzési tevékenységének javítására dolgozik) megújította a Microsoft nagyvállalati szintű felhőalapú szolgáltatásainak a G-Cloud v6-ra való besorolását, amely a hivatalos szinten található összes ajánlatra kiterjed. Az Azure és a G-Cloud részletei az [Azure Egyesült királyságbeli G-Cloud Security Assessment összefoglalójában](https://www.microsoft.com/en-us/trustcenter/compliance/uk-g-cloud)találhatók meg.

Ez a terv a NCSC [Cloud biztonsági](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) alapelvekben ismertetett 14 Felhőbeli biztonsági alapelvekhez igazodik, így biztosítva az Egyesült Királyság-tisztviselőként besorolt munkaterheléseket támogató környezeteket.

Az [Ügyfél-felelősségi mátrix](https://aka.ms/ukofficial-crm) (Excel-munkafüzet) a 14 Felhőbeli biztonsági alapelveket sorolja fel, a mátrix pedig az egyes alapelvek (vagy elvi részek) alapján határozza meg, hogy a megvalósítás elve a Microsoft, az ügyfél vagy a vállalat feladata-e megosztva a kettő között.

Az [elv megvalósítási mátrixa](https://aka.ms/ukofficial-iaaswa-pim) (Excel-munkafüzet) a 14 Felhőbeli biztonsági alapelveket sorolja fel, és a mátrix azt is jelzi, hogy az ügyfél felelősségi körében szereplő minden egyes elv (vagy alapelv) a terv automatizálása megvalósítja az alapelvet és a 2.) annak leírását, hogy a megvalósítás hogyan igazodik a követelmény (ek) hez.

Emellett a Cloud Security Alliance (CSA) közzétette a Cloud Control matrixot, hogy támogassa az ügyfeleket a felhőalapú szolgáltatók kiértékelésében, valamint hogy azonosítsa azokat a kérdéseket, amelyeknek a Cloud Services szolgáltatásba való áttérés előtt válaszolniuk kell. Válaszként Microsoft Azure megválaszolta a CSA konszenzusos felmérési kezdeményezés kérdőívét ([CSA CAIQ](https://www.microsoft.com/en-us/TrustCenter/Compliance/CSA)), amely leírja, hogyan kezeli a Microsoft a javasolt alapelveket.

## <a name="deploy-the-solution"></a>A megoldás üzembe helyezése

Az üzembe helyezést használó felhasználók két módszerrel telepíthetik ezt a terv-automatizálást. Az első módszer egy PowerShell-szkriptet használ, míg a második módszer a Azure Portalt használja a hivatkozási architektúra üzembe helyezéséhez. A részletes üzembe helyezési utasítások [itt](https://aka.ms/ukofficial-iaaswa-repo)érhetők el.

## <a name="disclaimer"></a>Jogi nyilatkozat

 - Ez a dokumentum csak tájékoztató jellegű. A MICROSOFT NEM VÁLLAL SEMMILYEN KIFEJEZETT, VÉLELMEZETT VAGY TÖRVÉNYES GARANCIÁT A JELEN DOKUMENTUMBAN FOGLALT INFORMÁCIÓK ALAPJÁN. Ez a dokumentum "aktuálisként" van megadva. Az ebben a dokumentumban kifejezett információk és nézetek, beleértve az URL-címeket és az internetes webhelyek más hivatkozásait, értesítés nélkül változhatnak. A dokumentumot olvasó ügyfeleink a használatuk kockázatát viselik.
 - A jelen dokumentum nem biztosít semmilyen jogot semmilyen Microsoft-termékben vagy-megoldásban található szellemi tulajdonhoz.
 - Az ügyfelek belső referenciák szerint másolhatják és használhatják ezt a dokumentumot.
 - A jelen dokumentumban szereplő javaslatok megnövelték az adatok, a hálózat vagy a számítási erőforrások használatát az Azure-ban, és növelhetik az ügyfelek Azure-licencét vagy előfizetési költségeit.
 - Ez az architektúra arra szolgál, hogy az ügyfelek számára a konkrét követelményekhez igazodva és nem használható éles környezetben.
 - Ez a dokumentum hivatkozásként van kifejlesztve, és nem használható az összes olyan eszköz meghatározására, amelyekkel az ügyfél megfelel bizonyos megfelelőségi követelményeknek és előírásoknak. Az ügyfeleknek jóvá kell hagyniuk a szervezeten belüli jogi támogatást a jóváhagyott ügyfél-implementációkban.
