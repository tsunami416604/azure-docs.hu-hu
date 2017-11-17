---
title: "Az Azure tervezetének Automation - UK hivatalos háromrétegű webalkalmazások"
description: "Az Azure tervezetének Automation & - UK hivatalos háromrétegű webalkalmazások"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 9c32e836-0564-4906-9e15-f070d2707e63
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 5f5694367d9be2ae66c7303cfea063b7f4979307
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2017
---
# <a name="azure-blueprint-automation-three-tier-web-applications-for-uk-official"></a>Azure tervezetének Automation: UK hivatalos háromrétegű webes alkalmazások

## <a name="overview"></a>Áttekintés

 Ez a cikk útmutatást és automatizálási parancsfájlok képes biztosítani a Microsoft Azure háromrétegű web-alapú architektúra megfelelő kezelése az Egyesült Királyságban hivatalos osztályba sorolt számos különféle munkaterheléshez tartalmazza.

 Az infrastruktúra használatával kódú közelítse, készletét [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) (ARM) sablonok telepítéséhez olyan környezetet, hogy legyen az a UK nemzeti számítógépes biztonsági központ (NCSC) 14 [biztonsági alapelvei](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles)és az internetes biztonsági (CIS) [kritikus fontosságú biztonsági vezérlők](https://www.cisecurity.org/critical-controls.cfm).

 A NCSC használatát javasolja azok biztonsági alapelvei ügyfelek kiértékelése a szolgáltatás a biztonsági tulajdonságait, és annak megértésében, az osztálynak a feladata a felhasználói és a szállítók között. Nyújtunk egyes segít megérteni a felosztás feladatkörök alapelvek.

 Ezen architektúra és a megfelelő ARM-sablonokat a Microsoft tanulmány által támogatott [Azure szerkezeti terve UK kormánya](https://gallery.technet.microsoft.com/14-Cloud-Security-Controls-670292c1). A dokumentum az Azure szolgáltatások katalógusok megfelel-e a UK NCSC 14 biztonsági alapelvei, ezáltal a szervezetek gyorsított felel meg a használatával a Microsoft Azure felhőalapú szolgáltatások globális és a UK kötelezettségek képesek felhő.

 Ez a sablon a munkaterhelés infrastruktúráját telepíti. Alkalmazás kódjában és az azt támogató üzleti szint és az adatok réteg szoftvert telepíteni és konfigurálni kell. Részletes üzembe helyezési utasítások érhetők el [Itt](https://aka.ms/ukwebappblueprintrepo).

 Ha nincs Azure-előfizetésre, majd regisztrálhat gyorsan és egyszerűen - [Ismerkedés az Azure](https://azure.microsoft.com/get-started/).

## <a name="architecture-diagram-and-components"></a>Architektúrája és összetevői

 Az Azure-sablonok biztosítanak a háromrétegű webes alkalmazás architektúra, amely támogatja a UK hivatalos munkaterhelések Azure felhőalapú környezetben. Az architektúra biztonságos hibrid környezetben, amely kibővíti az Azure lehetővé tevő web alapú munkaterhelések biztonságosan elérhetőek, vállalati felhasználók és az internetről egy helyszíni hálózatot nyújt.

![helyettesítő szöveg](images/diagram.png?raw=true "Azure UK hivatalos három réteg architektúrája")

 A megoldás az Azure-szolgáltatásokat. Az üzembe helyezési architektúrája részleteit találhatók a [üzembe helyezési architektúrája](#deployment-architecture) szakasz.

(1) /16 Virtuálishálózat - működési virtuális hálózat
- (3) /24 alhálózatból állnak – 3 szintű (Web, Biz, adatok)
- (1) /27 alhálózat - hozzáadása
- ((1) /27 alhálózat - átjáró-alhálózatot
- ((1) /29 alhálózat - alkalmazás átjáró-alhálózatot
- Által használt alapértelmezett (Azure által biztosított) DNS-
- Társviszony engedélyezve van a felügyeleti virtuális hálózatba
- Hálózati biztonsági csoport (NSG) a forgalom kezelése

(1) /24 Virtuálishálózat - felügyeleti hálózatok
- ((1) /27 alhálózati
- (2) HOZZÁADJA DNS- és (1) Azure DNS-bejegyzések használ
- Engedélyezve van a virtuális hálózatba működési társviszony
- Hálózati biztonsági csoport (NSG) a forgalom kezelése

(1) Alkalmazásátjáró
- WAF - engedélyezve
- WAF mód - megelőzés
- Szabálykészlet: OWASP 3.0
- Port 80-as HTTP-figyelő
- Csatlakozási/forgalom szabályozott NSG keresztül
- Nyilvános IP-cím végponton definiálva (Azure)

(1) VPN - útválasztó-alapú, 2-webhelyek közötti IPSec VPN-alagút
- Nyilvános IP-cím végponton definiálva (Azure)
- Csatlakozási/forgalom szabályozott NSG keresztül
- (1) a helyi hálózati átjáró (a helyszíni végpont)
- (1) azure-hálózatot átjáró (Azure-végpont)

(9) virtuális gépek - minden virtuális gép Azure IaaS kártevőirtó DSC beállításokkal vannak telepítve.

- (2) active Directory tartományi szolgáltatások tartományvezérlő (a Windows Server 2012 R2)
  - (2) DNS-kiszolgálói szerepkörökre - 1 virtuális gépenként
  - (2) NIC csatlakozó operatív VNet - 1 virtuális gépenként
  - Mindkettő a tartományhoz a sablonban definiált tartományhoz
    - Hozza létre a központi telepítés részeként tartomány


- (1) Jumpbox (megerősített állomás) felügyeleti méretű VM
  - A felügyeleti virtuális hálózaton nyilvános IP-címmel 1 NIC
    - NSG használt ezzel a forgalom (a/out) meghatározott források
  - Nincsenek tartományhoz csatlakoztatva


- (2) webes réteg virtuális gépek
  - (2) az IIS-kiszolgálói szerepkörök - 1 virtuális gépenként
  - (2) NIC csatlakozó operatív VNet - 1 virtuális gépenként
  - Nincsenek tartományhoz csatlakoztatva


- (2) biz réteg virtuális gépek
  - (2) NIC csatlakozó operatív VNet - 1 virtuális gépenként
  - Nincsenek tartományhoz csatlakoztatva


- (2) adatok réteg virtuális gépek
  - (2) NIC csatlakozó operatív VNet - 1 virtuális gépenként
  - Nincsenek tartományhoz csatlakoztatva

Rendelkezésre állási csoportok
- (1) az active Directory-tartományvezérlő virtuális Gépnek be – 2 virtuális gép
- (1) webes réteg virtuális gép be – 2 virtuális gép
- (1) biz réteg virtuális gép be – 2 virtuális gép
- (1) Adatrétegbeli VM be – 2 virtuális gép

Load Balancer
- (1) webes réteg terheléselosztó
- (1) biz réteg terheléselosztó
- (1) adatokat a réteg terheléselosztó

Storage
- (14) teljes Storage-fiókok
  - Az Active Directory tartományi vezérlő rendelkezésre állási csoport
    - (2) elsődleges helyileg redundáns tárolás (LRS) fiókok – az egyes virtuális gépek 1  
    - (1) diagnosztikai helyileg redundáns tárolás (LRS) fiók hozzáadása rendelkezésre állási csoport
  - Felügyeleti Jumpbox méretű VM
    - (1) elsődleges helyileg redundáns tárolás (LRS) fiókot használja a Jumpbox VM
    - (1) diagnosztikai helyileg redundáns tárolás (LRS) fiók a Jumpbox virtuális géphez
  - Webes réteg virtuális gépek
    - (2) elsődleges helyileg redundáns tárolás (LRS) fiókok – az egyes virtuális gépek 1  
    - (1) a webes réteg rendelkezésre állási csoport fiókot diagnosztikai helyileg redundáns tárolás (LRS)
  - Biz réteg virtuális gépek
    - (2) elsődleges helyileg redundáns tárolás (LRS) fiókok – az egyes virtuális gépek 1  
    - (1) diagnosztikai helyileg redundáns tárolás (LRS) fiókot használja a Biz réteg rendelkezésre állási csoport
  - Adatok réteg virtuális gépek
    - (2) elsődleges helyileg redundáns tárolás (LRS) fiókok – az egyes virtuális gépek 1  
    - (1) diagnosztikai adatok réteg rendelkezésre állási csoport helyileg redundáns tárolás (LRS) fiók

### <a name="deployment-architecture"></a>Üzembe helyezési architektúrája:

**A helyszíni hálózat**: A helyi magánhálózat valósult meg a szervezet.

**Éles VNet**: az üzemi [VNet](https://docs.microsoft.com/azure/Virtual-Network/virtual-networks-overview) (virtuális hálózat) az alkalmazás és más Azure-beli működési erőforrások tárolja. Minden egyes virtuális hálózat elkülönítése, és a hálózati forgalom kezelésére használt több alhálózat tartalmazhat.

**Webes réteg**: bejövő HTTP-kérelmeket kezeli. Válaszok a réteg keresztül adja vissza.

**Üzleti szint**: megvalósítja az üzleti folyamatokat és az egyéb működési programot, a rendszer.

**Adatbázis-réteg**: állandó adatok tárolására szolgál használatával [SQL Server Always On rendelkezésre állási csoportok](https://msdn.microsoft.com/library/hh510230.aspx) a magas rendelkezésre állás érdekében. Az ügyfelek használhatják [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview) PaaS alternatíva.

**Átjáró**: A [VPN-átjáró](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) nyújt az útválasztók a helyszíni hálózat és az üzemi virtuális hálózat közötti kapcsolat.

**Internetes átjáró és a nyilvános IP-cím**: az internetes átjáró elérhetővé teszi a felhasználók számára az interneten keresztül alkalmazáskiszolgálókhoz. Ezek a szolgáltatások eléréséhez forgalom használatával lett biztonságossá téve egy [Alkalmazásátjáró](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) réteg 7 Útválasztás és a webes alkalmazás tűzfalat (WAF) védelmi funkciókat kínál.

**Felügyeleti VNet**: Ez [VNet](https://docs.microsoft.com/azure/Virtual-Network/virtual-networks-overviewcontains) olyan erőforrásokat tartalmaz, kezelési és figyelési képességeket az üzemi virtuális hálózaton futó feladatok végrehajtására.

**Jumpbox**: más néven a [megerősített állomás](https://en.wikipedia.org/wiki/Bastion_host), vagyis a hálózaton, amely a rendszergazdák az üzemi virtuális hálózatot a virtuális gépeken való csatlakozáskor használandó biztonságos virtuális gép. A jumpbox van egy NSG-t, amely lehetővé teszi a távoli forgalom csak nyilvános IP-címekről érkező biztonságos listán. Távoli asztal (RDP)-forgalmát engedélyezi, hogy a forgalom forrásának kell definiálni az NSG. A termelési erőforrások kezelésére van használatával egy biztonságos Jumpbox virtuális gép RDP-kapcsolaton keresztül.

**Felhasználó által megadott útvonalak**: [felhasználó által megadott útvonalak](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) meghatározásához az Azure Vnetekhez belüli IP-forgalom áramlását használják.

**Hálózati Társviszonyban Vnetek**: segítségével csatlakozik az éles tárhely és felügyeleti Vnetek [Vnetben társviszony-létesítés](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).
A Vnetek külön erőforrásként továbbra is felügyelt, de egy, a virtuális gépek minden kapcsolat célra jelennek meg. Ezek a hálózatok magánhálózati IP-címek segítségével közvetlenül kommunikálhatnak egymással. A Vnetek éppen egy Azure-régióban van az Vnetben társviszony-létesítés.

**Hálózati biztonsági csoportok**: [NSG-k](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) tartalmaz a hozzáférés-vezérlési listái, amelyek engedélyezik vagy megtagadják a forgalmat a Vneten belül. Az NSG-k segítségével biztonságos egy alhálózat vagy az egyes virtuális gép szintjén-forgalmat.

**Active Directory tartományi szolgáltatások (AD DS)**: Ez az architektúra biztosít egy dedikált [Active Directory tartományi szolgáltatások](https://technet.microsoft.com/library/hh831484.aspx) központi telepítés.

**Naplózás és a naplózási**: [Azure tevékenységnapló](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) az erőforrások, például, hogy ki kezdeményezte a műveletet, ha a művelet történt, a művelet állapotának és értékeit az előfizetésben végzett műveleteket rögzíti. más tulajdonságokat, amelyek segíthetnek vizsgálja meg a műveletet. Azure tevékenységnapló az Azure platform szolgáltatást az előfizetés összes műveleteket rögzíti. Naplók archiválható vagy exportált, ha szükséges.

**Figyelés és a riasztások hálózati**: [Azure hálózati figyelőt](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) platform szolgáltatás hálózati csomagrögzítéssel, a naplózás folyamata, a topológia eszközök és a diagnosztika biztosít hálózati traffics a Vnetek belül.

## <a name="guidance-and-recommendations"></a>Útmutatás és javaslatok

### <a name="business-continuity"></a>Üzleti folyamatok fenntarthatósága

**Magas rendelkezésre állású**: kiszolgáló-munkaterhelések vannak csoportosítva egy [rendelkezésre állási csoport](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) az Azure virtuális gépek magas rendelkezésre állásának biztosításához. Ez a konfiguráció segít, győződjön meg arról, hogy a tervezett vagy nem tervezett karbantartási események legalább egy virtuális gép lesz elérhető, és megfelelnek a 99,95 % Azure SLA-t.

### <a name="logging-and-audit"></a>Naplózási és naplózása

**Figyelési**: [Azure figyelő](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started) a platform szolgáltatás, amely biztosítja az egy forrásból a tevékenységnapló, metrikákat és az összes Azure-erőforrások diagnosztikai naplók figyelésére. Az Azure figyelő megjelenítése, lekérdezésére, továbbítani, archiválására és a metrikák és a naplók az Azure-erőforrások érkező intézkedjen konfigurálható. Javasoljuk, hogy az erőforrás-alapú hozzáférés-vezérlés a napló segítségével, győződjön meg arról, hogy a felhasználóknak nem kell a naplók módosítását biztonságossá tételére szolgál-e.

**Tevékenységi naplóit**: konfigurálása [Azure tevékenységi naplóit](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) arra, hogy az erőforrást az előfizetésében a végrehajtott műveletek betekintést.

**Diagnosztikai naplók**: [diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) az erőforrás által kibocsátott összes naplófájlt. Ezek a naplók tartalmazhatnak Windows rendszer-eseménynaplói, blob, table és várólista naplókat.

**Tűzfal-naplók**: Application Gateway teljes diagnosztikai és a hozzáférési naplók biztosít. A tűzfalnaplók olyan Application Gateway-erőforrásokhoz érhetők el, amelyekhez engedélyezve van a WAF.

**Archiválás jelentkezzen**: archiválási és a megadott megőrzési időtartam fiókot napló adattárolás beállítható úgy, hogy egy központosított az Azure storage írni. Naplók Azure Naplóelemzés dolgozhatók vagy harmadik féltől származó SIEM-rendszerekről.

### <a name="identity"></a>Identitás

**Active Directory tartományi szolgáltatások**: Ez az architektúra biztosítja az Active Directory tartományi szolgáltatások központi telepítése az Azure-ban. Az Azure Active Directory végrehajtási konkrét javaslatokért tekintse meg a következő cikkeket:

[Az Azure Active Directory tartományi szolgáltatások (AD DS) kiterjesztése](https://docs.microsoft.com/azure/guidance/guidance-identity-adds-extend-domain).

[Telepítési útmutatója Windows Server Active Directory Azure virtuális gépeken futó](https://msdn.microsoft.com/library/azure/jj156090.aspx).

**Active Directory-integráció**: egy dedikált Active Directory tartományi szolgáltatások architektúrája helyett, az ügyfelek is szeretné használni [Azure Active Directory](https://docs.microsoft.com/azure/guidance/guidance-ra-identity#using-azure-active-directory) integrációs vagy [egy helyszíni Azure Active Directory-tartományhoz erdő](https://docs.microsoft.com/azure/guidance/guidance-ra-identity#using-active-directory-in-azure-joined-to-an-on-premises-forest).

### <a name="security"></a>Biztonság

**Felügyeleti biztonsági**: az Azure tervezetének lehetővé teszi, hogy a rendszergazdák számára, hogy csatlakozzon a felügyeleti virtuális hálózat és Jumpbox RDP Funkciót használnak a megbízható forrásból származik. A felügyeleti virtuális hálózatot a hálózati forgalmat az NSG-k segítségével kezelhető. Hozzáférés 3389-es port korlátozódik forgalom férhetnek hozzá a Jumpbox tartalmazó alhálózat IP megbízható tartomány.

Az ügyfelek is is érdemes lehet egy [a fokozott biztonság felügyeleti modell](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/securing-privileged-access) a környezet biztonságos, amikor csatlakozik a felügyeleti virtuális hálózat és Jumpbox. Ajánlatos, hogy a fokozott biztonság használják, a [Privileged Access munkaállomás](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/privileged-access-workstations#what-is-a-privileged-access-workstation-paw) és RDGateway konfigurációját. A virtuális hálózati berendezéseket és a nyilvános és titkos DMZ-k felajánlja a további biztonsági fejlesztések.

**A hálózat védelme**: [hálózati biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) minden alhálózatban a második szintű védelmet biztosít a mellőzés, egy helytelenül konfigurált vagy letiltott átjáró bejövő forgalom (NSG-k) használata ajánlott. Példa - [ARM-sablon üzembe helyezéséhez egy NSG](https://github.com/mspnp/template-building-blocks/tree/v1.0.0/templates/buildingBlocks/networkSecurityGroups).

**Nyilvános végpontok védelme**: az internetes átjáró elérhetővé teszi a felhasználók számára az interneten keresztül alkalmazáskiszolgálókhoz. Ezek a szolgáltatások eléréséhez forgalom használatával lett biztonságossá téve egy [Alkalmazásátjáró](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction), amely biztosítja, hogy a webalkalmazási tűzfal és a HTTPS protokoll felügyeleti.

**IP-címtartományok**: az IP-címtartományok az architektúra javasolt tartományok. Az ügyfelek javasolja, hogy a saját környezetben fontolja meg, és megfelelő tartományok használja.

**A hibrid kapcsolat**: A felhő alapú munkaterhelések kapcsolódnak a helyszíni datacentre használata az Azure VPN Gateway IPSEC VPN-en keresztül. Az ügyfelek győződjön meg arról, hogy használják a megfelelő VPN-átjáró Azure való kapcsolódáshoz. Példa - [VPN Gateway ARM-sablon](https://github.com/mspnp/template-building-blocks/tree/v1.0.0/templates/buildingBlocks/vpn-gateway-vpn-connection). Az ügyfelek a nagyméretű fut, kritikus fontosságú számítási feladatokhoz a big Data típusú adatok követelményeknek megfelelő lehet, hogy szeretnének érdemes egy hibrid hálózati architektúra [ExpressRoute](https://docs.microsoft.com/azure/guidance/guidance-hybrid-network-expressroute) kapcsolattal a Microsoftnak a felhőalapú szolgáltatások.

**Aggályokat elkülönítése**: A referencia-architektúrában elválasztja a Vnetek felügyeleti műveleteket, illetve üzleti műveletek. Külön virtuális hálózatokat és alhálózatokat engedélyezze a forgalom felügyeletét, forgalom bemenő és kimenő korlátozások, beleértve a következő hálózati szegmensek közötti NSG-k használatával [Microsoft cloud services és a hálózati biztonság](https://docs.microsoft.com/azure/best-practices-network-security) ajánlott eljárások.

**Erőforrás-kezelés**: Azure-erőforrások, például a virtuális gépek, a virtuális hálózatokat és a terheléselosztók felügyelt révén azokat [Azure erőforráscsoportok](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groupsresource). Erőforrás-alapú hozzáférés-vezérlés szerepkörök majd rendelhetők minden erőforráscsoport csak a hitelesített felhasználóknak való hozzáférés korlátozása.

**Hozzáférés-vezérlési korlátozásoknak**: használata [szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) (RBAC) kezelése a felügyelt erőforrások közé az alkalmazást a [egyéni szerepkörök](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles) RBAC segítségével korlátozható a műveletek, DevOps az egyes rétegek hajthat végre. Engedélyeket ad, ha a [legalacsonyabb jogosultsági szint elve](https://msdn.microsoft.com/library/hdb58b2f(v=vs.110).aspx#Anchor_1). Jelentkezzen az összes felügyeleti műveletet, és végezze el annak érdekében, hogy a konfigurációs változásokat tervezett rendszeres ellenőrzéseket.

**Internet-hozzáférés**: A referencia-architektúrában használja fel a [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) , az internetes átjáró és a terheléselosztó. Egyes ügyfelek is is érdemes lehet harmadik féltől származó hálózati virtuális készülékek az további rétegek, a hálózati biztonsági alternatívájaként a [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction).

**Az Azure Security Center**: A [az Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) központi az előfizetésben szereplő erőforrások biztonsági állapotát jeleníti meg, és ajánlásokat, amelyekkel megakadályozható a fertőzött erőforrásokat. Is használható részletesebb szabályzatok engedélyezéséhez. Például a házirendek alkalmazhatók az adott erőforráscsoportban, amely lehetővé teszi a vállalat személyessé tétele érdekében a kockázat állapotát. Javasoljuk, hogy az ügyfelek engedélyezése az Azure Security Center az Azure-előfizetésben.

## <a name="ncsc-cloud-security-principles-compliance-documentation"></a>NCSC felhőalapú biztonsági elveket megfelelőségi dokumentációja

A korona kereskedelmi szolgáltatást (egy működik, és javítja a kereskedelmi és beszerzési tevékenység kormánya által Ügynökség) megújítani a Microsoft hatókör vállalati felhőalapú szolgáltatások G-felhő v6, a besorolás a Hivatalos szinten az összes ajánlatok lefedik. Azure és a G-felhő részletek megtalálhatók a [Azure UK G-felhő biztonsági felmérés összegzése](https://www.microsoft.com/en-us/trustcenter/compliance/uk-g-cloud).

A UK hivatalos Azure tervezetének megoldás illeszkedik a 14 biztonsági alapelvei a NCSC ismertetett [biztonsági alapelvei](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) olyan környezetet, amely támogatja a munkaterhelések UK hivatalos besorolása érdekében.

A [ügyfél felelősségi mátrix](https://aka.ms/blueprintuk-gcrm) (Excel-munkafüzet) az összes 14 biztonsági alapelvei, illetve a mátrix jelöli, minden elve (vagy elv alrész), e elv végrehajtása feladata A Microsoft, az ügyfél vagy a kettő között megosztott.

A [elv megvalósítási mátrix](https://aka.ms/ukwebappblueprintpim) (Excel-munkafüzet) listák összes 14 biztonsági alapelvei, és a mátrix jelöli, az egyes elve (vagy elv alrész), amely egy ügyfél felelős az ügyfelek van kijelölve Feladatkörök mátrix, 1.) Ha az Azure tervezetének Automation megvalósítja az elven, és 2) leírását hogyan végrehajtása az elv requirement(s) igazodik. Ez a tartalom is rendelkezésre áll [Itt](https://github.com/Azure/uk-official-three-tier-webapp/blob/master/principles-overview.md).

Ezenkívül a felhőalapú biztonsági Alliance (CSA) közzé a felhő vezérlő mátrix szolgáltatók értékelése az ügyfelek támogatásához, és azonosíthatja a kérdéseket kell a felhőalapú szolgáltatások áthelyezése előtt. Választ, a Microsoft Azure válaszolni a CSA együttműködési Assessment kezdeményezésére kérdőív ([CSA CAIQ](https://www.microsoft.com/en-us/TrustCenter/Compliance/CSA)), amely ismerteti, hogyan Microsoft címek javasolt elveit.

## <a name="deploy-the-solution"></a>A megoldás üzembe helyezéséhez

Két módon központi telepítés felhasználók használhatják a Azure tervezetének megoldás üzembe helyezéséhez. Az első módszer PowerShell-parancsfájlokra, míg a második módszer használja fel az Azure-portál telepítése a referencia-architektúrában. Részletes üzembe helyezési utasítások érhetők el [Itt](https://aka.ms/ukwebappblueprintrepo).

## <a name="disclaimer"></a>Jogi nyilatkozat

 - Ez a dokumentum csak tájékoztatási célokat szolgál. A MICROSOFT NEM VÁLLAL SEMMILYEN KIFEJEZETT, KIFEJEZETT, VÉLELMEZETT VAGY TÖRVÉNYES, HOGY A JELEN DOKUMENTUMBAN SZEREPLŐ INFORMÁCIÓK. Ez a dokumentum biztosított ",-van." Információk és nézetek ebben a dokumentumban, beleértve az URL-CÍMEK és más internetes webhelyet, értesítés nélkül változhatnak. Ez a dokumentum olvasásakor az ügyfelek az alkalmazást saját felelősségére használja.
 - Ez a dokumentum nem biztosít semmilyen jogot semmilyen Microsoft-termék vagy a megoldások található szellemi tulajdonhoz rendelkező ügyfelek.
 - Az ügyfelek azonban másolható és ez a dokumentum belső hivatkozási célokra használja.
 - Bizonyos ajánlások a dokumentum azt eredményezheti, megnövekedett adat-, hálózati vagy számítási erőforrás-használat az Azure-ban, és megnövelheti az ügyfél Azure licencek vagy előfizetések költségeit.
 - Ebbe az architektúrába célja, hogy az ügyfelek úgy, hogy az egyedi követelményeket alapjaként szolgálnak, és nem használható mint – egy termelési környezetben.
 - Ez a dokumentum hivatkozásként fejlesztése, és nem használható minden olyan eszközt, amely szerint az ügyfél megfelel adott megfelelőségi követelmények és szabályok meghatározásához. Az ügyfelek jóváhagyott ügyfél-hitelesítés megvalósításához a szervezetek jogi támogatnia kell kérnie.
