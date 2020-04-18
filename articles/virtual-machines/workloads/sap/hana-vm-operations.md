---
title: SAP HANA infrastruktúra-konfigurációk és műveletek az Azure-ban | Microsoft dokumentumok
description: Az Azure virtuális gépeken üzembe helyezett SAP HANA-rendszerek üzemeltetési útmutatója.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/01/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7c4f3ec2727d06528eab788a2a24a6190fe26533
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606144"
---
# <a name="sap-hana-infrastructure-configurations-and-operations-on-azure"></a>SAP HANA infrastruktúrakonfigurációk és -műveletek az Azure-ban
Ez a dokumentum útmutatást nyújt az Azure-infrastruktúra konfigurálásához és az Azure natív virtuális gépeken (VM-eken) üzembe helyezett SAP HANA-rendszerek kezeléséhez. A dokumentum az M128s vm termékváltozathoz az SAP HANA kibővített konfigurációs adatait is tartalmazza. Ez a dokumentum nem helyettesíti a szabványos SAP dokumentációt, amely a következő tartalmat tartalmazza:

- [SAP felügyeleti útmutató](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [SAP telepítési útmutatók](https://service.sap.com/instguides)
- [SAP-jegyzetek](https://service.sap.com/notes)

## <a name="prerequisites"></a>Előfeltételek
Az útmutató használatához alapvető ismeretekre van szüksége a következő Azure-összetevőkről:

- [Azure-alapú virtuális gépek](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Azure-hálózatok és virtuális hálózatok](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Azure-tárhely](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Ha többet szeretne megtudni az SAP NetWeaverről és az Azure egyéb SAP-összetevőiről, tekintse meg az [Sap az Azure-on](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) szakaszt az [Azure dokumentációjában.](https://docs.microsoft.com/azure/)

## <a name="basic-setup-considerations"></a>Alapvető beállítási szempontok
Az alábbi szakaszok ismertetik az SAP HANA-rendszerek Azure virtuális gépeken való üzembe helyezésének alapvető beállítási szempontjait.

### <a name="connect-into-azure-virtual-machines"></a>Csatlakozás azure-beli virtuális gépekhez
Az [Azure virtuális gépek tervezési útmutatójában](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)dokumentált módon két alapvető módszer van az Azure virtuális gépekhez való csatlakozáshoz:

- Csatlakozzon az interneten keresztül és nyilvános végpontok egy jump virtuális gép, vagy a virtuális gép, amely az SAP HANA fut.
- Csatlakozás [VPN-en](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) vagy Azure [ExpressRoute-on](https://azure.microsoft.com/services/expressroute/)keresztül.

A VPN-en vagy ExpressRoute-on keresztüli helyek közötti kapcsolat éles környezetben szükséges. Ez a típusú kapcsolat is szükséges a nem éles forgatókönyvek, amelyek előcsatornázzák az éles forgatókönyvek, ahol az SAP-szoftver használatban van. Az alábbi képen egy példa látható a helyek közötti kapcsolatra:

![Helyek közötti kapcsolat](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>Válassza az Azure virtuálisgép-típusokat
Az éles környezetben használható Azure virtuálisgép-típusok az [IAAS SAP-dokumentációjában](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)találhatók. Nem éles környezetben forgatókönyvek esetén a natív Azure virtuálisgép-típusok szélesebb választéka érhető el.

>[!NOTE]
> Nem éles környezetben használja az [SAP-feljegyzésben](https://launchpad.support.sap.com/#/notes/1928533)#1928533 felsorolt virtuálisgép-típusokat. Az Azure-beli virtuális gépek éles környezetben való használatáért ellenőrizze az SAP HANA-tanúsítvánnyal rendelkező virtuális gépeket az SAP által [közzétett iaaS-platformok listában.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)

Telepítse a virtuális gépeket az Azure-ban a következők használatával:

- Az Azure Portal.
- Azure PowerShell-parancsmagok.
- Az Azure CLI.

Az [SAP Cloud platformon](https://cal.sap.com/)keresztül is üzembe helyezhet egy teljes telepített SAP HANA platformot az Azure virtuálisgép-szolgáltatásokon keresztül. A telepítési folyamat leírása az [SAP S/4HANA vagy bW/4HANA telepítése az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h) vagy az [itt](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)kiadott automatizálással.

>[!IMPORTANT]
> Annak érdekében, hogy M208xx_v2 virtuális gépek használata érdekében, gondosan ki kell választania a Linux-lemezképet az Azure virtuális gép képgalériából. Annak érdekében, hogy olvassa el a részleteket, olvassa el a cikket [Memória optimalizált virtuális gép méretei](../../mv2-series.md).
> 


### <a name="storage-configuration-for-sap-hana"></a>Az SAP HANA tárolási konfigurációja
Az Azure-beli SAP HANA-val használható tárolási konfigurációkés tárolási típusok esetében olvassa el az [SAP HANA Azure virtuálisgép-tároló konfigurációit tartalmazó dokumentumot.](./hana-vm-operations-storage.md)


### <a name="set-up-azure-virtual-networks"></a>Az Azure virtuális hálózatainak beállítása
Ha vpn-en vagy ExpressRoute-on keresztül rendelkezik helyek közötti kapcsolattal az Azure-ban, legalább egy Azure virtuális hálózattal kell rendelkeznie, amely virtuális átjárón keresztül csatlakozik a VPN- vagy ExpressRoute-kapcsolathoz. Egyszerű telepítések esetén a virtuális átjáró az SAP HANA-példányokat is tartalmazó Azure virtuális hálózat (VNet) alhálózatában telepíthető. Az SAP HANA telepítéséhez hozzon létre két további alhálózatot az Azure virtuális hálózaton belül. Egy alhálózat üzemelteti a virtuális gépeket az SAP HANA-példányok futtatásához. A másik alhálózat jumpbox vagy felügyeleti virtuális gépeket futtat az SAP HANA Studio, más felügyeleti szoftverek vagy az alkalmazásszoftver üzemeltetéséhez.

> [!IMPORTANT]
> A funkcionalitás, de ami még fontosabb a teljesítmény miatt, nem támogatott az [Azure Network virtuális készülékek](https://azure.microsoft.com/solutions/network-appliances/) konfigurálása az SAP-alkalmazás és az SAP NetWeaver, Hybris vagy S/4HANA alapú SAP-rendszer ADATBÁZIS-rétege közötti kommunikációs útvonalon. Az SAP alkalmazásréteg és a DBMS-réteg közötti kommunikációnak közvetlennek kell lennie. A korlátozás nem tartalmazza [az Azure ASG és NSG szabályokat](https://docs.microsoft.com/azure/virtual-network/security-overview) mindaddig, amíg ezek az ASG és NSG szabályok lehetővé teszik a közvetlen kommunikációt. További forgatókönyvek, ahol nva-k nem támogatottak a kommunikációs útvonalak között, hogy az Azure-fürt csomópontok és SBD-eszközök, amint azt a [magas rendelkezésre állás ú SAP NetWeaver az Azure virtuális gépek en SUSE Linux Enterprise Server SAP-alkalmazások.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse) Vagy az Azure virtuális gépek és a Windows Server SOFS közötti kommunikációs útvonalakban, amelyeket a fürtben leírtak szerint állítottak be [egy SAP ASCS/SCS-példány windowsos feladatátvételi fürtön az Azure-beli fájlmegosztás használatával.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share) A kommunikációs útvonalakon lévő nva-k könnyen megduplázhatják a hálózati késést két kommunikációs partner között, korlátozhatják az átviteli csatornát az SAP alkalmazásréteg és a DBMS-réteg közötti kritikus útvonalakon. Az ügyfelekkel megfigyelt egyes esetekben az NVa-k okozhat pacemaker Linux-fürtök sikertelen azokban az esetekben, ahol a Linux Pacemaker fürtcsomópontok közötti kommunikáció t an NVA-n keresztül kell kommunikálnia az SBD-eszköz.  
> 

> [!IMPORTANT]
> Egy másik terv, amely **nem** támogatott az SAP-alkalmazásréteg és a DBMS-réteg elkülönítése különböző Azure virtuális hálózatokra, amelyek nem [egyenrangúak](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) egymással. Ajánlott elkülöníteni az SAP-alkalmazásréteget és a DBMS-réteget az Azure virtuális hálózatán belüli alhálózatok használatával, ahelyett, hogy különböző Azure virtuális hálózatokat használna. Ha úgy dönt, hogy nem követi a javaslatot, és ehelyett a két réteget különböző virtuális hálózatra különíti el, a két virtuális hálózatot [társviszonyba](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)kell venni. Ne feledje, hogy a hálózati forgalom két [társviszony-létesítési](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) Azure virtuális hálózatok közötti átviteli költségek. Az SAP-alkalmazásréteg és a DBMS-réteg között számos Terabájtban lévő hatalmas adatmennyiséggel jelentős költségek halmozódhatnak fel, ha az SAP-alkalmazásréteg és a DBMS-réteg két társviszonyt létesített Azure virtuális hálózat között van elkülönítve. 

Amikor telepíti a virtuális gépeket az SAP HANA futtatásához, a virtuális gépeknek szükségük van:

- Két virtuális hálózati adapterek telepítve: egy hálózati adapter a felügyeleti alhálózathoz való csatlakozáshoz, és egy hálózati adapter csatlakozhat a helyszíni hálózatról vagy más hálózatok, az SAP HANA-példány az Azure virtuális gép.
- Statikus privát IP-címek, amelyek mindkét virtuális hálózati adapterhez telepítve vannak.

> [!NOTE]
> Statikus IP-címeket kell hozzárendelni e-n keresztül az Azure-eszközök az egyes virtuális adapterek. Ne rendeljen statikus IP-címeket a vendég operációs rendszeren belül egy virtuális hálózati adapterhez. Egyes Azure-szolgáltatások, például az Azure backup szolgáltatás támaszkodnak arra a tényre, hogy legalább az elsődleges virtuális adapter van beállítva, hogy DHCP, és nem statikus IP-címek. Lásd még a dokumentum [hibaelhárítása Azure virtuális gép biztonsági mentés .](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking) Ha több statikus IP-címet kell hozzárendelnie egy virtuális géphez, több virtuális adaptert kell hozzárendelnie egy virtuális géphez.
>
>

Azonban a telepítések, amelyek tartós, létre kell hoznia egy virtuális adatközpont hálózati architektúra az Azure-ban. Ez az architektúra azt javasolja, hogy az Azure VNet-átjáró, amely csatlakozik a helyszíni egy külön Azure virtuális hálózat. Ez a külön virtuális hálózat a helyszíni vagy az internetre hagyott összes forgalmat üzemeltetnie kell. Ez a megközelítés lehetővé teszi, hogy a felügyelt és naplózási forgalom, amely belép a virtuális adatközpont az Azure-ban ebben a külön hub virtuális hálózat. Így van egy virtuális hálózat, amely az Azure-üzembe helyezésbe irányuló és kimenő forgalomhoz kapcsolódó összes szoftvert és konfigurációt üzemelteti.

Az [Azure Virtual Datacenter: A Network Perspective](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter) és [az Azure Virtual Datacenter és a Enterprise Control Plane](https://docs.microsoft.com/azure/architecture/vdc/) cikkek további információt nyújtanak a virtuális adatközpont-megközelítésről és a kapcsolódó Azure virtuális hálózat kialakításáról.


>[!NOTE]
>A központi virtuális hálózat és a küllővirtuális hálózat között [az Azure virtuális hálózat társviszony-létesítése](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) során zajló forgalom további [költségeket von.](https://azure.microsoft.com/pricing/details/virtual-network/) Ezek alapján a költségek, előfordulhat, hogy meg kell fontolnia, hogy kompromisszumok közötti szigorú hub és a küllős hálózat kialakítása és több [Azure ExpressRoute-átjárók,](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways) hogy csatlakozik a "küllők", hogy megkerülje a virtuális hálózatok társviszony-létesítés. Az Azure ExpressRoute-átjárók azonban további [költségeket](https://azure.microsoft.com/pricing/details/vpn-gateway/) is eredményeznek. A hálózati forgalom naplózásához, naplózásához és figyeléséhez használt, harmadik féltől származó szoftverek további költségekkel is szembesülhetnek. Az egyik oldalon a virtuális hálózaton keresztüli társviszony-létesítésen keresztül történő adatcsere költségeitől és a további Azure ExpressRoute-átjárók és további szoftverlicencek által létrehozott költségektől függően dönthet úgy, hogy egy virtuális hálózaton belül mikroszegmentálást alkalmaz alhálózatokként a virtuális hálózatok helyett.


Az IP-címek hozzárendelésének különböző módszereit az [Azure-beli IP-címtípusok és foglalási módszerek című témakörben találja.](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm) 

Az SAP HANA-t futtató virtuális gépek esetén statikus IP-címekkel kell dolgoznia. Ennek oka, hogy a HANA referencia IP-címek egyes konfigurációs attribútumai.

[Az Azure Network Security Groups (NSG-k)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) az SAP HANA-példányra vagy a jumpboxra irányított forgalmat irányítják. Az NSG-k és végül [alkalmazásbiztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups) az SAP HANA alhálózathoz és a felügyeleti alhálózathoz vannak társítva.

Az alábbi képen az SAP HANA egy központi és küllővirtuális architektúrát követő durva központi telepítési séma áttekintése látható:

![Durva központi telepítési séma az SAP HANA-hoz](media/hana-vm-operations/hana-simple-networking-dmz.png)

Az SAP HANA üzembe helyezéséhez az Azure-ban hely-hely kapcsolat nélkül, továbbra is szeretné védeni az SAP HANA-példány a nyilvános internetről, és elrejti egy továbbító proxy mögé. Ebben az alapvető forgatókönyvben a központi telepítés az Azure beépített DNS-szolgáltatásaira támaszkodik az állomásnevek feloldásához. Egy összetettebb üzembe helyezés, ahol nyilvános IP-címeket használnak, az Azure beépített DNS-szolgáltatások különösen fontosak. Azure NSG-k és [az Azure NVA-k](https://azure.microsoft.com/solutions/network-appliances/) segítségével szabályozhatja, figyelheti az internetről az Azure virtuális hálózat architektúrájába irányuló útválasztást az Azure-ban. Az alábbi képen egy durva séma az SAP HANA hely-hely közötti kapcsolat nélküli központi és küllős virtuális hálózat architektúrájában történő üzembe helyezéséhez:
  
![Hozzávethetési központi telepítési séma az SAP HANA-hoz helyek közötti kapcsolat nélkül](media/hana-vm-operations/hana-simple-networking-dmz.png)
 

Egy másik leírás arról, hogyan azure NVA-k segítségével az internetről való hozzáférés vezérlésére és figyelésére anélkül, hogy a hub és a küllős virtuális hálózat architektúra megtalálható a [cikkben Telepítése magas rendelkezésre állású hálózati virtuális készülékek.](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha)


## <a name="configuring-azure-infrastructure-for-sap-hana-scale-out"></a>Az Azure-infrastruktúra konfigurálása az SAP HANA horizontális felskálázáshoz
Annak érdekében, hogy megtudja, az Azure virtuális gép típusok, amelyek olap horizontális felskálázás vagy S/4HANA horizontális felskálázás, ellenőrizze az [SAP HANA hardverkönyvtárban.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) A "Fürtözés" oszlopban lévő pipa a horizontális felskálázás támogatását jelzi. Az alkalmazás típusa azt jelzi, hogy az OLAP horizontális felskálázás vagy az S/4HANA horizontális felskálázás támogatott-e. Az egyes virtuális gépek hez hitelesített horizontális felskálázásban hitelesített csomópontok részleteiért tekintse meg az SAP HANA hardverkönyvtárában felsorolt virtuális gép termékváltozatának bejegyzéseit.

A horizontális felskálázási konfigurációk azure-beli virtuális gépeken való üzembe helyezéséhez szükséges minimális operációsrendszer-kiadások, tekintse meg az SAP HANA hardverkönyvtárában felsorolt virtuális gép termékváltozatának bejegyzéseinek részleteit. Az n-csomópont OLAP kibővített konfiguráció, egy csomópont függvények fő csomópontként. A többi csomópont a tanúsítvány korlátjáig feldolgozócsomópontként jár el. A további készenléti csomópontok nem számítanak bele a hitelesített csomópontok számába

>[!NOTE]
> Az SAP HANA készenléti csomókkal rendelkező Azure VM kibővített telepítései csak az [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) tároló használatával lehetségesek. Nincs más SAP HANA-tanúsítvánnyal rendelkező Azure-tároló, amely lehetővé teszi az SAP HANA készenléti csomópontok konfigurálását
>

A /hana/shared esetében az [Azure NetApp-fájlok](https://azure.microsoft.com/services/netapp/)használatát is javasoljuk. 

Egy horizontális felskálázási konfigurációban lévő egyetlen csomópont tipikus alapterve így fog kinézni:

![Egyetlen csomópont kibővített alapalapjai](media/hana-vm-operations/scale-out-basics-anf-shared.PNG)

Az SAP HANA horizontális felskálázáshoz egy virtuálisgép-csomópont alapkonfigurációja így néz ki:

- A **/hana/shared**kapcsolóhoz az Azure NetApp-fájlokon keresztül biztosított natív NFS-szolgáltatást használja. 
- Az összes többi lemezkötet nincs megosztva a különböző csomópontok között, és nem nfs-en alapulnak. A nem megosztott **/hana/data** és **/hana/log** kibővített HANA-telepítésekhez szükséges telepítési konfigurációk és a kibővített HANA-telepítések lépései a jelen dokumentum későbbi részében kerülnek rendelkezésre. A HANA minősített tároló, amely használható, tekintse meg a cikk [SAP HANA Azure virtuálisgép-tároló konfigurációk.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)


A kötetek vagy lemezek méretezése, meg kell néznie a dokumentumot [SAP HANA TDI tárolási követelmények,](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)a szükséges méretet a munkavégző csomópontok számától függően. A dokumentum kiad egy képletet, amelyet alkalmazni kell a kötet szükséges kapacitásának bekerüléséhez

A többi tervezési feltételek jelenik meg a grafikus egycsomópontos konfiguráció egy kibővített SAP HANA virtuális gép a virtuális hálózat, vagy jobb az alhálózati konfiguráció. Az SAP nagymértékben ajánlja az ügyfél/alkalmazás a HANA-csomópontok közötti kommunikációhoz való forgalom malszemben való elkülönítését. Ahogy az a grafikában látható, ez a cél érhető el, ha két különböző virtuális adapterek csatlakozik a virtuális géphez. Mindkét vNIC különböző alhálózatokban található, két különböző IP-címmel rendelkezik. Ezután az NSG-k vagy a felhasználó által definiált útvonalak használatával szabályozhatja a forgalom áramlását az útválasztási szabályokkal.

Különösen az Azure-ban nincsenek eszközök és módszerek a szolgáltatás minőségének és kvótáinak kényszerítésére az adott virtuális adaptereken. Ennek eredményeképpen az ügyfél/alkalmazás és a csomóponton belüli kommunikáció elkülönítése nem nyit meg lehetőséget arra, hogy az egyik forgalmi adatfolyamot a másikkal szemben rangsorolja. Ehelyett a szétválasztás továbbra is a horizontális felskálázási konfigurációk csomóponton belüli kommunikációjának árnyékolása.  

>[!NOTE]
>Az SAP azt javasolja, hogy a hálózati forgalmat az ügyfél/alkalmazás és a csomóponton belüli forgalom elkülönítése a jelen dokumentumban leírtak szerint válassza el. Ezért az architektúra elhelyezése az utolsó grafikában látható módon ajánlott. A biztonsági és megfelelőségi csapattal is tájékozódnak az ajánlástól eltántikulást mutató követelményekről. 
>

Hálózati szempontból a minimálisan szükséges hálózati architektúra a következőkre mutatna:

![Egyetlen csomópont kibővített alapalapjai](media/hana-vm-operations/overview-scale-out-networking.png)



### <a name="installing-sap-hana-scale-out-n-azure"></a>Az SAP HANA kibővített azure telepítése
Egy kibővített SAP-konfiguráció telepítésekor a következők durva lépéseit kell végrehajtania:

- Új azure-infrastruktúra üzembe helyezése vagy meglévő Azure-hálózat-infrastruktúra adaptálása
- Az új virtuális gépek üzembe helyezése az Azure Managed Premium Storage, Ultra lemezkötetek és/vagy NFS-kötetek használatával ANF alapján
- - A hálózati útválasztás átalakítása annak biztosításához, hogy például a virtuális gépek közötti csomóponton belüli kommunikáció ne legyen [csatornán keresztül.](https://azure.microsoft.com/solutions/network-appliances/) 
- Telepítse az SAP HANA főcsomópontot.
- Az SAP HANA főcsomópont konfigurációs paramétereinek adaptálása
- Folytassa az SAP HANA munkavégző csomópontok telepítésével

#### <a name="installation-of-sap-hana-in-scale-out-configuration"></a>Az SAP HANA telepítése kibővített konfigurációban
Az Azure virtuálisgép-infrastruktúra üzembe helyezésekor és az összes többi előkészület befejeződött, az alábbi lépésekben kell telepítenie az SAP HANA horizontális felskálázási konfigurációit:

- Telepítse az SAP HANA főcsomópontját az SAP dokumentációja szerint
- Az Azure Premium Storage vagy az Ultra lemeztároló használata esetén a /hana/data és a /hana/log nem megosztott lemezekkel meg kell változtatnia a global.ini fájlt, és hozzá kell adnia a "basepath_shared = no" paramétert a global.ini fájlhoz. Ez a paraméter lehetővé teszi, hogy az SAP HANA "megosztott" **/hana/data** és **/hana/log** kötetek nélkül fusson a csomópontok között. A részleteket az [SAP Note #2080991](https://launchpad.support.sap.com/#/notes/2080991)dokumentálja. Ha anf-alapú NFS-köteteket használ a /hana/data és a /hana/log kapcsolóhoz, akkor nem kell ezt a módosítást elkészítenie.
- A global.ini paraméter esetleges módosítása után indítsa újra az SAP HANA-példányt
- További munkavégző csomópontok hozzáadása. Lásd <https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US/0d9fe701e2214e98ad4f8721f6558c34.html>még: . Adja meg az SAP HANA csomópontok közötti kommunikáció belső hálózatát a telepítés során, vagy ezt követően például a helyi hdblcm használatával. További részletes dokumentációt az [SAP Note #2183363](https://launchpad.support.sap.com/#/notes/2183363)című témakörben talál. 

Az SAP HANA horizontális felskálázási rendszer Készenléti csomóponttal történő beállításának részleteit részletesen [ismertetjük az SAP HANA horizontális felskálázási rendszer telepítése az Azure virtuális gépeken álló készenléti csomópontokkal az Azure NetApp Files használatával a SUSE Linux Enterprise Server rendszeren.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) A Red Hat egyenértékű dokumentációja a cikkben [található: Az SAP HANA horizontális felskálázási rendszer telepítése készenléti csomóponttal az Azure virtuális gépeken az Azure NetApp Files használatával a Red Hat Enterprise Linux használatával.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel) 


## <a name="sap-hana-dynamic-tiering-20-for-azure-virtual-machines"></a>SAP HANA dinamikus rétegezés 2.0 Azure virtuális gépekhez

Az SAP HANA-tanúsítványok mellett az Sap HanA-sorozatú virtuális gépeken az SAP HANA dinamikus rétegezése 2.0 is támogatott a Microsoft Azure-ban (lásd: SAP HANA dinamikus rétegezési dokumentációs hivatkozások lejjebb). Bár nincs különbség a termék telepítése vagy üzemeltetése, például az SAP HANA Cockpit egy Azure virtuális gépen belül, van néhány fontos elem, amelyek kötelezőek az Azure hivatalos támogatása. Ezeket a kulcsfontosságú pontokat az alábbiakban ismertetjük. A cikkben a "DT 2.0" rövidítés t használjuk a Dynamic Tiering 2.0 teljes név helyett.

Az SAP HANA dinamikus rétegezése 2.0-t az SAP BW vagy az S4HANA nem támogatja. Fő használati esetek most natív HANA alkalmazások.


### <a name="overview"></a>Áttekintés

Az alábbi kép áttekintést nyújt a DT 2.0 Microsoft Azure-beli támogatásáról. A hivatalos tanúsításnak való megfelelés érdekében kötelező követelmények állnak fenn:

- A DT 2.0-t egy dedikált Azure-gépre kell telepíteni. Előfordulhat, hogy nem fut ugyanazon a virtuális számítógépen, ahol az SAP HANA fut
- Az SAP HANA-t és a DT 2.0 virtuális gépeket ugyanazon az Azure-n belül kell telepíteni
- Az SAP HANA és dt 2.0 virtuális gépeket az Azure gyorsított hálózati engedélyezésével kell telepíteni
- A DT 2.0 virtuális gépek tárolási típusának Azure Premium Storage-nak kell lennie
- Több Azure-lemezt kell csatolni a DT 2.0 virtuális géphez
- Ez szükséges, hogy hozzon létre egy szoftver raid / csíkos kötet (akár lvm vagy mdadm) csíkozás sal az Azure-lemezek

További részleteket a következő szakaszokban ismertethet.

![SAP HANA DT 2.0 architektúra – áttekintés](media/hana-vm-operations/hana-data-tiering.png)



### <a name="dedicated-azure-vm-for-sap-hana-dt-20"></a>Dedikált Azure virtuális gép az SAP HANA DT 2.0-hoz

Az Azure IaaS-en a DT 2.0 csak egy dedikált virtuális gép támogatott. Nem szabad dt 2.0-t futtatni ugyanazon az Azure-beli virtuális gépen, ahol a HANA-példány fut. Kezdetben két virtuálisgép-típus használható az SAP HANA DT 2.0 futtatásához:

- M64-32ms 
- E32sv3 között 

Lásd vm típus leírása [itt](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)

Tekintettel a DT 2.0 alapötletére, amely a "meleg" adatok kiszervezéséről szól a költségek csökkentése érdekében, érdemes a megfelelő virtuális gépméreteket használni. Nincs szigorú szabály azonban a lehetséges kombinációkat illetően. Ez az adott ügyfél-munkaterheléstől függ.

Az ajánlott konfigurációk a következők lennének:

| SAP HANA virtuális gép típusa | DT 2.0 virtuális gép típusa |
| --- | --- | 
| M128ms | M64-32ms |
| M128-asok | M64-32ms |
| M64ms | E32sv3 között |
| M64-esek | E32sv3 között |


Az SAP HANA-tanúsítvánnyal rendelkező M sorozatú virtuális gépek és a támogatott DT 2.0 virtuális gépek (M64-32ms és E32sv3) összes kombinációja lehetséges.


### <a name="azure-networking-and-sap-hana-dt-20"></a>Azure-hálózatés SAP HANA DT 2.0

A DT 2.0 telepítése egy dedikált virtuális gépen a DT 2.0 virtuális gép és a legalább 10 Gb-os SAP HANA virtuális gép közötti hálózati átviteli csatornát igényel. Ezért kötelező az összes virtuális gépet ugyanazon az Azure-beli virtuális hálózaton belül elhelyezni, és engedélyezni az Azure gyorsított hálózatba szolgáltatását.

Az Azure gyorsított hálózatkezelésével kapcsolatos további információk [itt](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)

### <a name="vm-storage-for-sap-hana-dt-20"></a>Virtuálisgép-tároló az SAP HANA DT 2.0-hoz

A DT 2.0 ajánlott eljárásokra vonatkozó útmutatása szerint a lemez IO átviteli sebességének fizikai magonként legalább 50 MB/mp-nek kell lennie. A két Azure virtuálisgép-típus specifikációját, amelyek et a DT 2.0-ban támogatják, a virtuális gép maximális lemez I/o átviteli korlátja a következőképpen néz ki:

- E32sv3 : 768 MB/mp (nem gyorsítótárazott), ami fizikai magonként 48 MB/mp arányt jelent
- M64-32 ms: 1000 MB/mp (nem gyorsítótárazott), ami fizikai magonként 62,5 MB/mp arányt jelent

Több Azure-lemez csatlakoztatásához kell a DT 2.0 virtuális géphez, és hozzon létre egy szoftveres raid (csíkozás) operációs rendszer szintjén a virtuális gépenkénti lemezátviteli sebesség maximális korlátjának eléréséhez. Egyetlen Azure-lemez nem tudja biztosítani az átviteli sebesség, hogy elérje a maximális virtuális gép korlátot ebben a tekintetben. Az Azure Premium storage használata kötelező a DT 2.0 futtatásához. 

- Az elérhető Azure-lemeztípusok részletei [itt találhatók.](../../windows/disks-types.md)
- Részletek létrehozása szoftver raid keresztül mdadm megtalálható [itt](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid)
- Az LVM beállításának a maximális átviteli sebességhez való csíkozott kötet létrehozásához itt [található](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm) részletek

A méretkövetelményektől függően különböző lehetőségek közül választhat a virtuális gép maximális átviteli sebességének elérése. Az alábbiakban a DT 2.0 virtuálisgép-típusok hoz a felső virtuálisgép átviteli korlát eléréséhez lehetséges adatkötet-lemezkonfigurációkat olvashat. Az E32sv3 virtuális gép kell tekinteni, mint egy belépő szintű kisebb számítási feladatok. Abban az esetben, ha kiderül, hogy nem elég gyors, szükség lehet a virtuális gép M64-32ms-re való átméretezésére.
Mivel az M64-32ms virtuális gép sok memóriával rendelkezik, az IO-terhelés nem feltétlenül éri el a korlátot, különösen az olvasási igényű számítási feladatok esetében. Ezért kevesebb lemez a csíkkészletben elegendő lehet az ügyfél-specifikus munkaterheléstől függően. De ahhoz, hogy a biztonságos oldalon a lemez konfigurációk alatt választották, hogy garantálja a maximális átviteli:


| Virtuális gép termékváltozata | Lemez Config 1 | Lemez Config 2 | Lemez Config 3 | Lemez Config 4 | Lemez Config 5 | 
| ---- | ---- | ---- | ---- | ---- | ---- | 
| M64-32ms | 4 x P50 -> 16 TB | 4 x P40 -> 8 TB | 5 x P30 -> 5 TB | 7 x P20 -> 3,5 TB | 8 x P15 -> 2 TB | 
| E32sv3 között | 3 x P50 -> 12 TB | 3 x P40 -> 6 TB | 4 x P30 -> 4 TB | 5 x P20 -> 2,5 TB | 6 x P15 -> 1,5 TB | 


Különösen abban az esetben, ha a számítási feladatok olvasási intenzív, ez növelheti az Io teljesítményét, hogy kapcsolja be az Azure gazdagép cache "csak olvasható", az adatbázis-szoftverek adatkötetei ajánlott. Mivel a tranzakciónapló ban az Azure gazdagép-gyorsítótárának "nincs" kell lennie. 

A naplókötet méretét illetően az ajánlott kiindulási pont az adatméret 15%-ának heurisztikusa. A naplókötet létrehozása a költség- és átviteli követelményektől függően különböző Azure-lemeztípusok használatával valósítható meg. A naplókötethez nagy I/O-átviteli sebességgel kell megadni.  Az M64-32ms típusú virtuális gép használata esetén kötelező engedélyezni az [írásgyorsítót.](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator) Az Azure Write Accelerator optimális lemezírási késleltetést biztosít a tranzakciós naplóhoz (csak az M sorozathoz érhető el). Vannak olyan elemek, amelyeket figyelembe kell venni, például a virtuális gép típusonként i lemezek maximális számát. Részletek a Write Accelerator megtalálható [itt](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)


Íme néhány példa a naplókötet méretezésére:

| adatkötet mérete és lemeztípusa | naplókötet és lemeztípus config 1 | naplókötet és lemeztípus config 2 |
| --- | --- | --- |
| 4 x P50 -> 16 TB | 5 x P20 -> 2,5 TB | 3 x P30 -> 3 TB |
| 6 x P15 -> 1,5 TB | 4 x P6 -> 256 GB | 1 x P15 -> 256 GB |


Az SAP HANA horizontális felskálázáshoz hasonlóan a /hana/shared directoryt meg kell osztani az SAP HANA virtuális gép és a DT 2.0 virtuális gép között. Ugyanaz tanarchitások, mint az SAP HANA horizontális felskálázás dedikált virtuális gépek használatával, amelyek magas rendelkezésre állású NFS-kiszolgálóként működnek. A megosztott biztonsági mentési kötet biztosításához az azonos kialakítás használható. De az ügyfél, ha ha lenne szükség, vagy ha elegendő, hogy csak egy dedikált virtuális gép elegendő tárolókapacitással jár el, mint egy tartalék kiszolgáló.



### <a name="links-to-dt-20-documentation"></a>A DT 2.0 dokumentációjára mutató hivatkozások 

- [SAP HANA dinamikus rétegezés telepítési és frissítési útmutató](https://help.sap.com/viewer/88f82e0d010e4da1bc8963f18346f46e/2.0.03/en-US)
- [SAP HANA dinamikus rétegezési oktatóanyagok és -erőforrások](https://help.sap.com/viewer/fb9c3779f9d1412b8de6dd0788fa167b/2.0.03/en-US)
- [SAP HANA dinamikus rétegezési poc](https://blogs.sap.com/2017/12/08/sap-hana-dynamic-tiering-delivering-on-low-tco-with-impressive-performance/)
- [SAP HANA 2.0 SPS 02 dinamikus rétegezési fejlesztések](https://blogs.sap.com/2017/07/31/sap-hana-2.0-sps-02-dynamic-tiering-enhancements/)




## <a name="operations-for-deploying-sap-hana-on-azure-vms"></a>Sap HANA üzembe helyezéséhez az Azure-beli virtuális gépeken való üzembe helyezési műveletek
A következő szakaszok ismertetik az SAP HANA-rendszerek üzembe helyezésével kapcsolatos műveletek et.

### <a name="back-up-and-restore-operations-on-azure-vms"></a>Műveletek biztonsági és visszaállítási műveleteinek biztonsági és visszaállítása az Azure virtuális gépeken
Az alábbi dokumentumok ismertetik, hogyan lehet biztonsági másolatot, és állítsa vissza az SAP HANA-telepítés:

- [SAP HANA biztonsági mentés áttekintése](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [SAP HANA fájlszintű biztonsági mentés](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [SAP HANA-tárolási pillanatkép-referenciaérték](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)


### <a name="start-and-restart-vms-that-contain-sap-hana"></a>SAP HANA-t tartalmazó virtuális gépek indítása és újraindítása
Az Azure nyilvános felhőjének egyik kiemelkedő jellemzője, hogy csak a számítási percekért kell fizetnie. Például, ha leállít egy virtuális gép, amely az SAP HANA-t futtató, csak a tárolási költségek et az adott időszakban. Egy másik szolgáltatás akkor érhető el, ha statikus IP-címeket ad meg a virtuális gépeka kezdeti központi telepítésben. Amikor újraindít egy virtuális gép, amely SAP HANA, a virtuális gép újraindul a korábbi IP-címekkel. 


### <a name="use-saprouter-for-sap-remote-support"></a>SAProuter használata az SAP távoli támogatásához
Ha a helyszíni helyek és az Azure között helyről helyekre létesít, és SAP-összetevőket futtat, akkor valószínűleg már saproutert futtat. Ebben az esetben végezze el a következő elemeket a távoli támogatáshoz:

- Az SAP HANA-t a SAProuter konfigurációban üzemeltetni kívánt virtuális gép privát és statikus IP-címét.
- Konfigurálja a HANA virtuális gép et tartalmazó alhálózat NSG-jét a 3299-es TCP/IP-porton keresztüli forgalom engedélyezéséhez.

Ha az interneten keresztül csatlakozik az Azure-hoz, és nem rendelkezik egy SAP-útválasztó val a virtuális gép SAP HANA, majd telepítenie kell az összetevőt. Telepítse a SAP-útválasztót egy külön virtuális gépre a Felügyeleti alhálózatban. Az alábbi képen egy durva séma látható az SAP HANA helyek közötti kapcsolat nélküli és SAProuter nélküli üzembe helyezéséhez:

![Durva telepítési séma sap HANA nélkül hely-hely kapcsolat és SAProuter](media/hana-vm-operations/hana-simple-networking-saprouter.png)

Ügyeljen arra, hogy a SAProuter-t külön virtuális gépben telepítse, és ne a Jumpbox VM-ben. A külön virtuális gépnek statikus IP-címmel kell rendelkeznie. A SAProuter sap által üzemeltetett SAProuterhez való csatlakoztatásához forduljon az SAP-hoz egy IP-címért. (Az SAP által üzemeltetett SAProuter a virtuális gépre telepített SAProuter példány megfelelője.) Használja az SAP IP-címét a SAProuter példány konfigurálásához. A konfigurációs beállításokban az egyetlen szükséges port a 3299-es TCP-port.

A távoli támogatási kapcsolatok SAProuteren keresztüli beállításáról és karbantartásáról az [SAP dokumentációjában](https://support.sap.com/en/tools/connectivity-tools/remote-support.html)olvashat bővebben.

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Magas rendelkezésre állás az SAP HANA-val az Azure natív virtuális gépein
Ha SUSE Linux Enterprise Server vagy Red Hat rendszert futtat, létrehozhat egy Pacemaker-fürtöt STONITH eszközökkel. Az eszközök segítségével konfigurálhatja az SAP HANA konfigurációt, amely szinkron replikációt használ a HANA rendszerreplikációval és az automatikus feladatátvételsel. További információ a "Következő lépések" című részben található.

## <a name="next-steps"></a>Következő lépések
Ismerkedjen meg a felsorolt cikkekkel
- [SAP HANA Azure-beli virtuális gépek tárkonfigurációi](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
- [SAP HANA horizontális felskálázási rendszer üzembe helyezése készenléti csomókkal az Azure virtuális gépein az Azure NetApp-fájlok használatával a SUSE Linux Enterprise Server en](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
- [SAP HANA horizontális felskálázási rendszer üzembe helyezése készenléti csomókkal az Azure virtuális gépein az Azure NetApp-fájlok használatával Red Hat Enterprise Linux on Red Hat Enterprise Linux on](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)
- [Az SAP HANA magas rendelkezésre állása az Azure-beli virtuális gépeken a SUSE Linux Enterprise Server szolgáltatásban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Az SAP HANA magas rendelkezésre állása az Azure-beli virtuális gépeken a Red Hat Enterprise Linuxon](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel)

 

