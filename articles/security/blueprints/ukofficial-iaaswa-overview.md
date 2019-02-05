---
title: Azure biztonsági és megfelelőségi terv – a háromrétegű IaaS-webalkalmazás a UK-OFFICIAL
description: Azure biztonsági és megfelelőségi terv – a háromrétegű IaaS-webalkalmazás a UK-OFFICIAL
services: security
author: jomolesk
ms.assetid: 9c32e836-0564-4906-9e15-f070d2707e63
ms.service: security
ms.topic: article
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: a7461f6160c4c848106b16b1a9eaacb96ddf7499
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2019
ms.locfileid: "55699142"
---
# <a name="azure-security-and-compliance-blueprint---three-tier-iaas-web-application-for-uk-official"></a>Azure biztonsági és megfelelőségi terv – a háromrétegű IaaS-webalkalmazás a UK-OFFICIAL

## <a name="overview"></a>Áttekintés

 Ez a cikk útmutatást és automatizálási szkriptek, hogy a Microsoft Azure-alapú háromszintű webalkalmazásban-architektúra megfelelő az Egyesült Királyságban hivatalos besorolt sok feladat elvégezhető.

 Az infrastruktúra kódként való használatával megközelítést, készletét [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) sablonok üzembe helyezése egy környezetet, amely megfelel a az Egyesült Királyság nemzeti Kibertámadások biztonsági központ (NCSC) 14 [Felhőbiztonsági irányelveinek](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) és az internetes biztonság (CIS) központ [kritikus fontosságú biztonsági vezérlők](https://www.cisecurity.org/critical-controls.cfm).

 A NCSC használatát javasolja azok Felhőbiztonsági irányelveinek ügyfelek általi értékelheti ki a szolgáltatás a biztonsági tulajdonságait, és annak megértésében, az osztálynak a felelős az ügyfél és a szolgáltató között. Megadtuk az egyes ezeket az alapelveket segítenek megérteni a feladatkörök a felosztás az adatokat.

 Ez az architektúra és a megfelelő Azure Resource Manager-sablonok a Microsoft tanulmány által támogatott [UK 14 Felhőbeli biztonsági vezérlők használatával a Microsoft Azure felhőalapú](https://gallery.technet.microsoft.com/14-Cloud-Security-Controls-670292c1). Ez a tanulmány az Azure-szolgáltatások katalógusok összhangba kerüljenek az Egyesült Királyság NCSC 14 Felhőbiztonsági irányelveinek, ezáltal a szervezetek számára a gyorsított képesek kötelezettségeiket megfelelőségi használatával a Microsoft Azure felhőalapú szolgáltatások globális és az Egyesült Királyság felhő.

 Ez a sablon üzembe helyezi az a számítási infrastruktúra. Alkalmazáskód és a támogató üzleti szint és az adatok szint szoftvert kell telepíteni és konfigurálni. Részletes üzembe helyezési utasítások [Itt](https://aka.ms/ukwebappblueprintrepo).

 Ha nem rendelkezik Azure-előfizetés már be is jelentkezhet gyorsan és egyszerűen - [első lépései az Azure-ral](https://azure.microsoft.com/get-started/).

## <a name="architecture-diagram-and-components"></a>Architektúra és összetevők

 Az Azure-sablonok nyújthat egy háromszintű webalkalmazásban architektúra, amely támogatja az Egyesült Királyság hivatalos számítási feladatokat az Azure felhőalapú környezetben. Az architektúra egy biztonságos hibrid környezetben, amely kiterjeszti a helyszíni hálózatot a vállalati felhasználó által vagy az internetről biztonságosan elérhetők az Azure-beli lehetővé tevő web-alapú számítási tesz lehetővé.

![Háromrétegű IaaS-webalkalmazás UK hivatalos architekturális diagramja](images/ukofficial-iaaswa-architecture.png?raw=true "háromrétegű IaaS-webalkalmazás UK hivatalos architekturális diagramja")

 Ez a megoldás a következő Azure-szolgáltatásokat használ. Az üzembe helyezési architektúra részletek találhatók a [üzembe helyezési architektúrája](#deployment-architecture) szakaszban.

((1) /16 virtual Network - működési VNet
- (3) /24 alhálózatok – 3 szintű (Web, Biz, adatok)
- (1) / 27-eset - alhálózat hozzáadása
- ((1) / 27-es alhálózat - átjáró-alhálózat
- ((1), akár/29 méretű alhálózatot - Application Gateway alhálózatának
- Használja alapértelmezett (Azure által biztosított) DNS
- Engedélyezve van a felügyeleti virtuális hálózatok közötti társviszony
- Hálózati biztonsági csoport (NSG) forgalom kezeléséhez.

((1) /24 virtual Network - felügyeleti VNet
- ((1) / 27-es alhálózat
- AD DNS (2) és (1) az Azure DNS-bejegyzések
- Társviszony-létesítés engedélyezve működési virtuális hálózathoz
- Hálózati biztonsági csoport (NSG) forgalom kezeléséhez.

(1) az application Gateway
- WAF - kompatibilis
- WAF mód – megelőzése
- Szabálykészlet: OWASP 3.0
- 80-as Port HTTP-figyelő
- Kapcsolat/forgalom NSG keresztül szabályozott
- Nyilvános IP-cím végponthoz meghatározott (Azure)

(1) VPN - Átjárómat útvonalalapúra 2 helyek közötti IPSec VPN-alagút
- Nyilvános IP-cím végponthoz meghatározott (Azure)
- Kapcsolat/forgalom NSG keresztül szabályozott
- (1) a helyi hálózati átjáró (a helyi végpont)
- (1) az azure-hálózati átjáró (Azure-végpont)

(9) virtuális gépek – minden virtuális gépre telepített Azure IaaS kártevőirtó DSC beállításokkal

- (2) active Directory Domain Services tartományvezérlő (Windows Server 2012 R2)
  - (2) a DNS-kiszolgálói szerepkörökre – 1 virtuális gépenként
  - (2) a NIC csatlakozik a működési virtuális hálózatok közötti - virtuális gépenként 1
  - Mindkét tartományhoz csatlakoztatva a tartományhoz, definiálva a sablonban
    - Az üzembe helyezés részeként létrehozott tartomány


- (1) Jumpbox (Bástyagazdagép) felügyeleti virtuális gép
  - Nyilvános IP-címmel a felügyeleti virtuális hálózati Adapterén 1
    - NSG-t használt-meghatározott források (bejövő és kimenő) forgalom korlátozása
  - Nincsenek tartományhoz csatlakoztatva


- (2) a webes szintű virtuális gépek
  - (2) az IIS-kiszolgálói szerepkörök – 1 virtuális gépenként
  - (2) a NIC csatlakozik a működési virtuális hálózatok közötti - virtuális gépenként 1
  - Nincsenek tartományhoz csatlakoztatva


- (2) biz szintű virtuális gépek
  - (2) a NIC csatlakozik a működési virtuális hálózatok közötti - virtuális gépenként 1
  - Nincsenek tartományhoz csatlakoztatva


- (2) adatok szintű virtuális gépeket
  - (2) a NIC csatlakozik a működési virtuális hálózatok közötti - virtuális gépenként 1
  - Nincsenek tartományhoz csatlakoztatva

Rendelkezésre állási csoportok
- (1) beállítva az active Directory-tartományvezérlő virtuális gép – 2 virtuális gépen
- (1) beállítva a webes szint virtuális gép – 2 virtuális gépen
- (1) virtuális gép biz szint beállítása – 2 virtuális gépen
- (1) beállítva Adatrétegbeli virtuális gép – 2 virtuális gépen

Load Balancer
- (1) a webes rétegben lévő terheléselosztóhoz
- (1) biz rétegben lévő terheléselosztóhoz
- (1) adatok rétegben lévő terheléselosztóhoz

Storage
- (14) összes Storage-fiókok
  - Az Active Directory Domain Controller rendelkezésre állási csoport
    - (2) elsődleges helyileg redundáns tárolás (LRS) fiókok – az egyes virtuális Gépekhez 1  
    - (1) diagnosztikai helyileg redundáns tárolás (LRS) fiókot a HOZZÁADJA rendelkezésre állási csoport
  - Felügyeleti Jumpbox virtuális Géphez
    - (1) elsődleges helyileg redundáns tárolás (LRS) fiókot a Jumpbox virtuális géphez
    - (1) diagnosztikai helyileg redundáns tárolás (LRS) fiókot a Jumpbox virtuális géphez
  - Webes szintű virtuális gépek
    - (2) elsődleges helyileg redundáns tárolás (LRS) fiókok – az egyes virtuális Gépekhez 1  
    - (1) diagnosztikai helyileg redundáns tárolás (LRS) fiókot a webes szint rendelkezésre állási csoport
  - Biz szintű virtuális gépek
    - (2) elsődleges helyileg redundáns tárolás (LRS) fiókok – az egyes virtuális Gépekhez 1  
    - (1) diagnosztikai helyileg redundáns tárolás (LRS) fiókot a Biz szint rendelkezésre állási csoport
  - Adatok szintű virtuális gépek
    - (2) elsődleges helyileg redundáns tárolás (LRS) fiókok – az egyes virtuális Gépekhez 1  
    - (1) diagnosztikai helyileg redundáns tárolás (LRS) fiókot az adatok szint rendelkezésre állási csoport

### <a name="deployment-architecture"></a>Üzembe helyezési architektúra:

**A helyszíni hálózat**: Egy helyi magánhálózat a szervezet implementálva.

**Éles VNet**: Az üzemi [VNet](https://docs.microsoft.com/azure/Virtual-Network/virtual-networks-overview) (virtuális hálózat) futtatja az alkalmazást és egyéb Azure-ban futó operatív erőforrásokat. Minden egyes virtuális hálózatok közötti tartalmazhat több elkülönítése, és a hálózati forgalom kezelésére használt alhálózat.

**Webalkalmazás-csomag**: A bejövő HTTP-kérelmeket kezeli. Válaszok a rendszer ezen a szinten keresztül adja vissza.

**Üzleti szint**: Valósítja meg üzleti folyamatokat és egyéb működési logika, a rendszer.

**Adatbázis-szintű**: Állandó adattárolást, biztosít használatával [SQL Server Always On rendelkezésre állási csoportok](https://msdn.microsoft.com/library/hh510230.aspx) magas rendelkezésre állás érdekében. Vállalt [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview) egy PaaS helyett.

**Átjáró**: A [VPN-átjáró](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) kapcsolatot biztosít a helyszíni hálózat útválasztói és az éles virtuális hálózat között.

**Nyilvános IP-cím és az internetes Átjárónkhoz**: Az internetes átjárónkhoz alkalmazásszolgáltatások felhasználók az interneten keresztül elérhetővé teszi. Ezek a szolgáltatások elérése forgalom védi egy [Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) 7. rétegbeli útválasztási és terheléselosztási funkciók webes alkalmazásvédelemmel tűzfal (WAF).

**Felügyeleti virtuális hálózati**: Ez [VNet](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) olyan erőforrásokat tartalmaz, felügyeleti és monitorozási képességeket a VNet éles környezetben futó számítási feladatok végrehajtására.

**Jumpbox**: Más néven egy [bástyagazdagép](https://en.wikipedia.org/wiki/Bastion_host), azaz egy biztonságos virtuális gép, amely a rendszergazdák használhatják az éles virtuális hálózat virtuális gépekhez csatlakozhat a hálózaton. A jumpbox olyan NSG-vel rendelkezik, amely csak a biztonságos elemek listáján szereplő nyilvános IP-címekről érkező távoli forgalmat engedélyezi. Távoli asztali (RDP) forgalmat lehetővé teszik, hogy a forgalom forrását kell definiálni az NSG-ben. Éles erőforrásait használatával biztonságos Jumpbox virtuális gép RDP-n keresztül történik.

**Felhasználó által megadott útvonalak**: [Felhasználó által megadott útvonalak](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) segítségével határozhatók meg az IP-forgalom Azure vnetekben.

**Társviszonyban lévő virtuális hálózatok hálózati**: Éles üzemi pontjának és felügyeleti virtuális hálózatok segítségével csatlakozik [virtuális hálózatok közötti társviszony](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).
Ezek a virtuális hálózatok továbbra is külön erőforrásként történik, de ezek a virtuális gépek az összes csatlakozás szempontjából egyetlen hálózatnak látszik. Ezek a hálózatok magánhálózati IP-címek segítségével közvetlenül kommunikálnak egymással. Virtuális hálózatok közötti társviszony vonatkozik a virtuális hálózat között, hogy az azonos Azure-régióban van.

**Hálózati biztonsági csoportok**: [Az NSG-k](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) tartalmazza a hozzáférés-vezérlési listák, amelyek engedélyezik vagy megtagadják a forgalmat a Vneten belül. Az NSG-ket egy alhálózatot vagy az egyes Virtuálisgép-szintű forgalom védelmére használható.

**Az Active Directory Domain Services (AD DS)**: Ez az architektúra biztosít egy dedikált [Active Directory Domain Services](https://technet.microsoft.com/library/hh831484.aspx) központi telepítés.

**Naplózás és a naplózási**: [Azure-tevékenységnapló](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) rögzíti műveletek végrehajtását az erőforrást az előfizetésében, mint például az kezdeményező a műveletet, amikor a művelet végrehajtásának, a művelet állapotának és más tulajdonságokat, amelyek segíthetnek a kutatási a a művelet. Azure-tevékenységnapló egy Azure platformszolgáltatás, amely egy adott előfizetés összes műveleteket rögzíti. Naplók archiválhatók vagy exportált, ha szükséges.

**Hálózati figyelés és riasztás**: [Az Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) platformszolgáltatás biztosít a hálózat, csomagrögzítés, a csoportforgalom naplózása, a topológia eszközök és a diagnosztika belül a virtuális hálózatok hálózati traffics.

## <a name="guidance-and-recommendations"></a>Útmutatás és javaslatok

### <a name="business-continuity"></a>Üzleti folytonosság

**Magas rendelkezésre állású**: Server számítási feladatainak szerint vannak csoportosítva egy [rendelkezésre állási csoport](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) az Azure-beli virtuális gépek magas rendelkezésre állás biztosítása érdekében. Ez a konfiguráció biztosítja, hogy tervezett vagy nem tervezett karbantartási események legalább egy virtuális gép lesz elérhető, és megfeleljen a 99,95 %-os Azure SLA-t.

### <a name="logging-and-audit"></a>Naplózás és naplózása

**Figyelés**: [Az Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started) a platformszolgáltatás, amely egyetlen adatforrás biztosít a tevékenységnapló, metrikák és diagnosztikai naplók az Azure-erőforrások figyeléséhez. Az Azure Monitor megjelenítése lekérdezésére, a irányítani, archív tárolási szint, és reagálhat rájuk, metrikákat és naplókat az Azure-erőforrások érkező konfigurálható. Javasoljuk, hogy a napló segítségével győződjön meg arról, hogy a felhasználók nem rendelkeznek a naplók módosítását biztonságos erőforráshoz szerepköralapú hozzáférés-vezérlés szolgál.

**A Tevékenységnaplók**: Konfigurálása [Azure-tevékenységnaplóinak](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) nyújt betekintést az előfizetésében erőforrásokon végrehajtott műveletek.

**Diagnosztikai naplók**: [Diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) az összes napló, az erőforrás által kibocsátott. Ezek a naplók magukban foglalhatják a Windows rendszer-eseménynaplói, blob, tábla és üzenetsor-naplók.

**Tűzfal-naplók**: Application Gateway teljes diagnosztikát és hozzáférési naplókat biztosít. A tűzfalnaplók olyan Application Gateway-erőforrásokhoz érhetők el, amelyekhez engedélyezve van a WAF.

**Napló Archiválás**: Napló adattárolás írni egy központosított Azure storage-fiókját az archiválási és a egy meghatározott adatmegőrzési időszakot konfigurálható. Naplók az Azure Log Analytics feldolgozható, vagy harmadik féltől származó SIEM rendszerekbe.

### <a name="identity"></a>Identitás

**Active Directory Domain Services**: Ez az architektúra egy Active Directory Domain Services telepítési ennek az az Azure-ban. Az Active Directory Azure-ban való megvalósítására vonatkozó konkrét ajánlásokért tekintse meg az alábbi cikkeket:

[Az Active Directory Domain Services (AD DS) kiterjesztése az Azure-bA](https://docs.microsoft.com/azure/guidance/guidance-identity-adds-extend-domain).

[Telepítési útmutatója Windows Server Active Directory Azure-beli virtuális gépeken](https://msdn.microsoft.com/library/azure/jj156090.aspx).

**Active Directory-integráció**: Egy dedikált AD DS-architektúra alternatívájaként ügyfelek Kezdésként használhatja [Azure Active Directory](https://docs.microsoft.com/azure/guidance/guidance-ra-identity) integrációs vagy [az Azure Active Directoryhoz csatlakoztatott helyszíni erdőhöz](https://docs.microsoft.com/azure/guidance/guidance-ra-identity).

### <a name="security"></a>Biztonság

**Felügyeleti biztonsági**: Ez a megoldás lehetővé teszi a rendszergazdák csatlakozni a felügyeleti virtuális hálózat és a Jumpboxhoz RDP-vel megbízható forrásból származik. A felügyeleti virtuális hálózatok közötti hálózati forgalom NSG-k segítségével kezelhető. Hozzáférés a 3389-es portra egy megbízható IP-címtartományt, amelyet elérhet tartalmazó a Jumpbox alhálózatról származó forgalmat korlátozni.

Ügyfelek előfordulhat, hogy is érdemes egy [továbbfejlesztett biztonsági felügyeleti modell](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/securing-privileged-access) a környezet biztonságos, amikor csatlakozik a felügyeleti virtuális hálózat és a Jumpboxhoz. Javasoljuk, hogy a fokozott biztonság használják, egy [Privileged Access Workstation](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/privileged-access-workstations#what-is-a-privileged-access-workstation-paw) és RDGateway konfigurációját. A hálózati virtuális berendezések és a nyilvános/titkos használható DMZ-ket felajánlja a további biztonsági fejlesztések.

**A hálózat biztonságának beállítása**: [Hálózati biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) minden alhálózatban szintet biztosítanak a bejövő forgalmat egy helytelenül konfigurált vagy letiltott átjáró kihagyásával elleni védelem (NSG-k) használata ajánlott. Példa – [Resource Manager-sablon üzembe helyezéséhez az NSG-KET](https://github.com/mspnp/template-building-blocks/tree/v1.0.0/templates/buildingBlocks/networkSecurityGroups).

**Nyilvános végpontok védelme**: Az internetes átjárónkhoz alkalmazásszolgáltatások felhasználók az interneten keresztül elérhetővé teszi. Ezek a szolgáltatások elérése forgalom védi egy [Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction), amely biztosítja a webalkalmazás-tűzfal és a HTTPS protokoll felügyeleti.

**IP-címtartományok**: Az IP-címtartományok, az architektúra olyan javasolt tartományok. Ügyfelek javasolja, hogy fontolja meg a saját környezetben és megfelelő tartományok használata.

**Hibrid kapcsolat**: A felhőalapú számítási feladatokat a helyszíni adatközpont használata az Azure VPN Gateway IPSEC VPN-kapcsolaton keresztül csatlakoznak. Ügyfelek biztosítania kell, hogy a megfelelő VPN-átjáró csatlakozni az Azure-ban használják. Példa – [VPN Gateway Resource Manager-sablon](https://github.com/mspnp/template-building-blocks/tree/v1.0.0/templates/buildingBlocks/vpn-gateway-vpn-connection). Ügyfelek, amellyel nagy méretű, alapvető fontosságú számítási feladataikat a big data-követelményekhez Kezdésként érdemes lehet egy hibrid hálózati architektúra az [ExpressRoute](https://docs.microsoft.com/azure/guidance/guidance-hybrid-network-expressroute) magánhálózati kapcsolatot a Microsoft felhőszolgáltatások.

**Kockázatok elkülönítése**: Ez a referenciaarchitektúra elkülöníti a felügyeleti műveleteket és üzleti műveletekhez a virtuális hálózat között. Különálló virtuális hálózatok és alhálózatok lehetővé teszik a forgalom kezeléséhez, többek között a forgalmat bejövő és kimenő korlátozások, a következő hálózati szegmensek közötti NSG-k használatával [Microsoft cloud services és a hálózati biztonsági](https://docs.microsoft.com/azure/best-practices-network-security) ajánlott eljárások.

**Az erőforrás-kezelés**: Azure-erőforrások, például virtuális gépek, virtuális hálózatok és terheléselosztók által erőforráscsoportokba helyezi őket felügyelt [Azure-erőforráscsoportok](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). Erőforrásszerepkörök szerepköralapú hozzáférés-vezérlés aztán rendelhet az egyes erőforráscsoportokhoz a hozzáférés korlátozása csak a jogosult felhasználók.

**Hozzáférés-vezérlési korlátozásoknak**: Használat [szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) (RBAC) felügyelheti az erőforrásokat az alkalmazást a [egyéni szerepkörök](https://docs.microsoft.com/azure/role-based-access-control/custom-roles) RBAC fejlesztési és üzemeltetési az egyes rétegekben végrehajtható műveletek korlátozására használható. Engedélyek megadása esetén használja a [elvét](https://msdn.microsoft.com/library/hdb58b2f(v=vs.110).aspx#Anchor_1). Naplózzon minden felügyeleti műveletet, és rendszeresen végezzen ellenőrzést. Így meggyőződhet arról, hogy minden konfigurációmódosítás tervezett volt.

**Internet-hozzáférés**: Ez a referenciaarchitektúra használja fel a [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) , az internetkapcsolattal rendelkező átjáró és a load balancer. Egyes ügyfeleink is érdemes megfontolni, harmadik féltől származó hálózati virtuális berendezések használata a hálózati biztonsági alternatívájaként további rétegekre a [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction).

**Azure Security Center**: A [az Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) biztosít egy helyen jeleníti meg az előfizetés erőforrásainak biztonsági állapotát, és ajánlásokkal segíti a feltört erőforrásokat a megelőzése érdekében. Is használható részletesebb szabályzatok engedélyezéséhez. Ha például a szabályzatok alkalmazhatók adott erőforráscsoportokhoz, amely lehetővé teszi a vállalatok számára, hogy testre szabni a kockázat állapotáról. Javasoljuk, hogy az ügyfelek engedélyezik az Azure Security Center az Azure-előfizetés.

## <a name="ncsc-cloud-security-principles-compliance-documentation"></a>NCSC Cloud biztonsági alapelveket megfelelőségi – dokumentáció

A Királyi kereskedelmi szolgáltatást (egy működik, kereskedelmi és beszerzési javítható a kormányzati hivatal) megújítani a Microsoft releváns enterprise cloud services – G-Cloud 6-os verziójának besorolása kiterjedő az ajánlatokat a hivatalos szintjén. Az Azure és a G-Cloud részletei megtalálhatók a [Azure Egyesült Királyságbeli G-Cloud biztonsági értékelés összegzése](https://www.microsoft.com/en-us/trustcenter/compliance/uk-g-cloud).

Ez a megoldás igazodnak-e a 14 felhőbiztonsági irányelveinek, amelyek szerepelnek a NCSC [Felhőbiztonsági irányelveinek](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) segítségével olyan környezetet, amely támogatja a számítási feladatok UK-OFFICIAL besorolva.

A [vevő felelőssége mátrix](https://aka.ms/ukofficial-crm) (Excel-munkafüzet) összes 14 felhőbiztonsági irányelveinek sorolja fel, és a mátrix azt jelzi, hogy, minden egyes elve (vagy elv alrészben), elvének megvalósítása feladata, hogy A Microsoft, az ügyfél vagy a kettő között megosztott.

A [elvének megvalósítása mátrix](https://aka.ms/ukofficial-iaaswa-pim) (Excel-munkafüzet) lista összes 14 felhőbiztonsági irányelveinek, és a mátrix azt jelzi, minden elve (vagy elv alrészben) ki van jelölve, hogy az ügyfél az ügyfél felelőssége Mátrix feladatkörei, 1.) Ha a tervezet automation elve és (2) leírását, hogyan illeszkedik az megvalósítása az elv blokkolására végeznek.

Továbbá a Cloud Security Alliance (CSA) közzététele a felhőalapú vezérlő mátrix ügyfelek támogatása a kiértékelés a felhőszolgáltatók és a kérdéseket, amelyeket a felhőalapú szolgáltatásokhoz való áthelyezése előtt kell megválaszolni azonosításához. Reagálva a Microsoft Azure CSA konszenzus értékelés kezdeményezés kérdőív választ ([CSA-RÓL](https://www.microsoft.com/en-us/TrustCenter/Compliance/CSA)), amely azt ismerteti, hogyan Microsoft szünteti meg a javasolt alapelveket.

## <a name="deploy-the-solution"></a>A megoldás üzembe helyezése

Két módszerrel, amely a központi telepítés felhasználók használhatják a tervezet automatizálás telepítése. Az első módszer egy PowerShell-parancsfájlt használja, míg a második módszer az Azure Portalon, a referenciaarchitektúra üzembe helyezéséhez használja fel. Részletes üzembe helyezési utasítások [Itt](https://aka.ms/ukofficial-iaaswa-repo).

## <a name="disclaimer"></a>Jogi nyilatkozat

 - Ez a dokumentum csak tájékoztatási célokat szolgál. A MICROSOFT NEM VÁLLAL SEMMILYEN KIFEJEZETT, KIFEJEZETT, VÉLELMEZETT VAGY KÖTELEZŐ GARANCIÁT A DOKUMENTUMBAN SZEREPLŐ INFORMÁCIÓRA VONATKOZÓAN. Ez a dokumentum biztosított "as-van." Információk és vélemények ebben a dokumentumban URL-CÍMEK és más internetes webhelyekre utaló hivatkozások értesítés nélkül változhatnak. Ez a dokumentum olvasásakor ügyfelek felelősségére használja.
 - Ez a dokumentum nem biztosít semmilyen jogot semmilyen Microsoft-termék vagy a megoldások a szellemi tulajdonhoz ügyfelek.
 - Ügyfelek másolhatja és használhatja ezt a dokumentumot belső, hivatkozási célokból.
 - Bizonyos ajánlások a dokumentum eredményezhet megnövekedett adat-, hálózati vagy számítási erőforrás-használat az Azure-ban, és előfordulhat, hogy növelje az ügyfél Azure licencek vagy előfizetések költségeit.
 - Ez az architektúra célja, hogy az ügyfelek számára az adott követelményekhez beállítása alapjaként szolgálja ki, és nem használható-éles környezetben van.
 - Ez a dokumentum referenciaként fejlesztik, és nem használható minden olyan eszközt, amely szerint egy ügyfél megfelel a megadott megfelelőségi követelmények és előírások meghatározásához. Ügyfelek jóváhagyott megvalósítás az ügyfeleknél a szervezet jogi támogatást kell kérnie.
