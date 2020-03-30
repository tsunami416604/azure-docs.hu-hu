---
title: Az SAP NW magas rendelkezésre állása az RHEL-en az Azure NetApp-fájlokkal| Microsoft dokumentumok
description: Az Azure Virtual Machines magas rendelkezésre állása az SAP NetWeaver számára a Red Hat Enterprise Linux on
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/26/2020
ms.author: radeltch
ms.openlocfilehash: 11119d193cd08944bdff4737e8182cc7bece0abc
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351248"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver-on-red-hat-enterprise-linux-with-azure-netapp-files-for-sap-applications"></a>Az Azure Virtual Machines magas rendelkezésre állása az SAP NetWeaver számára a Red Hat Enterprise Linux on Azure NetApp Files SAP-alkalmazásokhoz

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability-rhel.md
[glusterfs-ha]:high-availability-guide-rhel-glusterfs.md

Ez a cikk bemutatja, hogyan telepítheti a virtuális gépeket, konfigurálhatja a virtuális gépeket, telepítheti a fürtkeretrendszert, és hogyan telepíthet egy magas rendelkezésre állású SAP NetWeaver 7.50 rendszert az [Azure NetApp Files használatával.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/)
A példakonfigurációkban telepítési parancsok stb. AZ ASCS-példány száma 00, az ERS-példány a 01, az elsődleges alkalmazáspéldány (PAS) 02, az Application instance (AAS) pedig 03. Sap System ID QAS használatos. 

Ebben a cikkben az adatbázisréteg nem szerepel részletesen.  

Először olvassa el a következő SAP-megjegyzéseket és dokumentumokat:

* [Az Azure NetApp Files dokumentációja][anf-azure-doc] 
* Az SAP Note [1928533,]amely:
  * Az SAP-szoftverek telepítéséhez támogatott Azure virtuális gépméretek listája
  * Fontos kapacitásadatok az Azure virtuális gépek méretéhez
  * Támogatott SAP szoftverek, operációs rendszer (OS) és adatbázis-kombinációk
  * Szükséges SAP kernel verzió Windows és Linux rendszeren a Microsoft Azure-ban

* Az SAP Note [2015553] felsorolja az SAP által támogatott SAP-szoftvertelepítések előfeltételeit az Azure-ban.
* Az SAP Note [2002167] ajánlott a Red Hat Enterprise Linux operációs rendszerbeállításaihoz
* Az SAP Note [2009879] SAP HANA irányelveket készített a Red Hat Enterprise Linux-hoz
* Az SAP Note [2178632] részletes információkat tartalmaz az Azure-ban az SAP-hoz jelentett összes figyelési metrikáról.
* Az SAP Note [2191498] rendelkezik a szükséges SAP Host Agent linuxos verzióval az Azure-ban.
* Az SAP Note [2243692] információkat tartalmaz az Sap-licencelésről az Azure-ban.
* Az SAP Note [1999351] további hibaelhárítási információkat tartalmaz az SAP-hoz kiadott Azure továbbfejlesztett figyelési bővítményhez.
* [Az SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) minden szükséges SAP-jegyzetet igényel Linuxhoz.
* [Az Azure virtuális gépek tervezése és megvalósítása az SAP-hoz Linuxon][planning-guide]
* [Az Azure virtuális gépek üzembe helyezése az SAP-hoz Linuxon][deployment-guide]
* [Az Azure virtual machines DBMS üzembe helyezése az SAP-hoz Linuxon][dbms-guide]
* [SAP Netweaver a pacemaker fürtben](https://access.redhat.com/articles/3150081)
* Általános RHEL dokumentáció
  * [Magas rendelkezésre állású bővítmény – áttekintés](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Magas rendelkezésre állású bővítmény felügyelete](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Magas rendelkezésre állású bővítményhivatkozás](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [ASCS/ERS konfigurálása az SAP Netweaver számára az RHEL 7.5-ös rendszerben önálló erőforrásokkal](https://access.redhat.com/articles/3569681)
  * [Sap S/4HANA ASCS/ERS konfigurálása önálló enqueue server 2 (ENSA2) kiszolgálóval az RHEL pacemakerében](https://access.redhat.com/articles/3974941)
* Azure-specifikus RHEL dokumentáció:
  * [Az RHEL magas rendelkezésre állású fürtjeinek támogatási szabályzatai – Microsoft Azure virtuális gépek fürttagként](https://access.redhat.com/articles/3131341)
  * [Red Hat Enterprise Linux 7.4 (és újabb) magas rendelkezésre állású fürt telepítése és konfigurálása a Microsoft Azure-ban](https://access.redhat.com/articles/3252491)
* [NetApp SAP-alkalmazások a Microsoft Azure-ban az Azure NetApp-fájlok használatával][anf-sap-applications-azure]

## <a name="overview"></a>Áttekintés

Az SAP Netweaver központi szolgáltatások magas rendelkezésre állása (HA) megosztott tárolást igényel.
Ahhoz, hogy ezt a Red Hat Linux-on eddig elérjük, külön, magas rendelkezésre állású GlusterFS fürtöt kellett létrehoznunk. 

Most már elérhető az SAP Netweaver HA az Azure NetApp-fájlokban telepített megosztott tárterület használatával. Az Azure NetApp Files használata a megosztott tárolóhoz szükségtelenné teszi a további [GlusterFS-fürt használatát.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs) Pacemaker továbbra is szükség van a HA az SAP Netweaver központi szolgáltatások (ASCS/SCS).

![SAP NetWeaver magas rendelkezésre állás – áttekintés](./media/high-availability-guide-rhel/high-availability-guide-rhel-anf.png)

SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS és az SAP HANA adatbázis virtuális állomásnév és virtuális IP-címek használata. Az Azure-ban egy terheléselosztó szükséges a virtuális IP-cím használatához. [A Standard terheléselosztó](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal)használatát javasoljuk. Az alábbi lista a terheléselosztó konfigurációját mutatja be, külön előtér-IP-kkel az (A)SCS és ERS számára.

### <a name="ascs"></a>A) a) Scs

* Előtér-konfiguráció
  * IP-cím 192.168.14.9
* Szonda port
  * Port 620<strong>&lt;nr&gt;</strong>
* Terheléselosztási szabályok
  * Standard terheléselosztó használata esetén válassza a **HA portok**
  * 32<strong>&lt;nr&gt; </strong> TCP
  * 36<strong>&lt;nr&gt; </strong> TCP
  * 39<strong>&lt;nr&gt; </strong> TCP
  * 81<strong>&lt;nr&gt; </strong> TCP
  * 5<strong>&lt;nr&gt;</strong>13 TCP
  * 5<strong>&lt;nr&gt;</strong>14 TCP
  * 5<strong>&lt;óra&gt;</strong>16 TCP

### <a name="ers"></a>Ers

* Előtér-konfiguráció
  * IP-cím 192.168.14.10
* Szonda port
  * Port 621<strong>&lt;nr&gt;</strong>
* Terheléselosztási szabályok
  * Standard terheléselosztó használata esetén válassza a **HA portok**
  * 32<strong>&lt;nr&gt; </strong> TCP
  * 33<strong>&lt;nr&gt; </strong> TCP
  * 5<strong>&lt;nr&gt;</strong>13 TCP
  * 5<strong>&lt;nr&gt;</strong>14 TCP
  * 5<strong>&lt;óra&gt;</strong>16 TCP

* Háttérrendszer-konfiguráció
  * Az (A)SCS/ERS-fürt részét alkotó összes virtuális gép elsődleges hálózati interfészeihez csatlakoztatva

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>Az Azure NetApp Files infrastruktúra beállítása 

Az SAP NetWeaver megosztott tárolást igényel az átviteli és profilkönyvtárhoz.  Mielőtt folytatná az Azure NetApp-fájlok infrastruktúrájának telepítését, ismerkedjen meg az [Azure NetApp Files][anf-azure-doc]dokumentációjával. Ellenőrizze, hogy a kiválasztott Azure-régió kínál-e Azure NetApp-fájlokat. Az alábbi hivatkozás az Azure NetApp-fájlok Azure-régiónkénti elérhetőségét mutatja be: [Az Azure NetApp-fájlok elérhetősége az Azure régió szerint.][anf-avail-matrix]

Az Azure NetApp-fájlok számos [Azure-régióban](https://azure.microsoft.com/global-infrastructure/services/?products=netapp)érhetők el. Az Azure NetApp-fájlok üzembe helyezése előtt kérjen bevezetést az Azure NetApp-fájlokba az [Azure NetApp-fájlok regisztrálása utasítások nak][anf-register]megfelelően. 

### <a name="deploy-azure-netapp-files-resources"></a>Az Azure NetApp Files erőforrásainak telepítése  

A lépések feltételezik, hogy már telepítette az [Azure Virtuális Hálózatot.](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) Az Azure NetApp-fájlok erőforrásait és a virtuális gépeket, ahol az Azure NetApp-fájlok erőforrások at kell telepíteni az azonos Azure virtuális hálózat vagy társviszonyt létesített Azure virtuális hálózatok.  

1. Ha még nem tette meg, kérje [a bevezetést az Azure NetApp Files alkalmazásba.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register)  
2. Hozza létre a NetApp-fiókot a kiválasztott Azure-régióban a [NetApp-fiók létrehozásához.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account)  
3. Állítsa be az Azure NetApp Files kapacitáskészletét az [Azure NetApp-fájlok kapacitáskészletének beállítására vonatkozó utasításokat](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool)követve.  
Az EBBEN a cikkben bemutatott SAP Netweaver architektúra egyetlen Azure NetApp Files kapacitáskészletet, prémium termékváltozatot használ. Az Azure NetApp Files Premium SKU az SAP Netweaver alkalmazás számítási feladatához az Azure-ban.  
4. Alhálózat delegálása az Azure NetApp-fájlokba az [Azure NetApp-fájlok alhálózatának delegálása](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)utasításokban leírtak szerint.  

5. Telepítse az Azure NetApp-fájlok köteteit az [Azure NetApp-fájlok kötetének létrehozásához szükséges utasításokat](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes)követve. Telepítse a köteteket a kijelölt Azure NetApp Files [alhálózatban.](https://docs.microsoft.com/rest/api/virtualnetwork/subnets) Ne feledje, hogy az Azure NetApp-fájlok erőforrásainak és az Azure virtuális gépeknek ugyanabban az Azure virtuális hálózatban vagy társviszonyt létesített Azure virtuális hálózatokban kell lenniük. Ebben a példában két Azure NetApp-fájlkötetet használunk: sap<b>QAS</b> és transSAP. A megfelelő csatlakoztatási pontokhoz csatlakoztatott fájlelérési utak a /usrsap<b>qas</b>/sapmnt<b>QAS</b>, /usrsap<b>qas</b>/usrsap<b>QAS</b>sys stb.  

   1. kötet sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/sapmnt<b>QAS)</b>
   2. kötet sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>ascs)
   3. kötet sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>sys)
   4. kötet sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>ers)
   5. kötet transzSAP (nfs://192.168.24.4/transSAP)
   6. kötet sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>pas)
   7. kötet sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>aas)
  
Ebben a példában az Azure NetApp-fájlokat használtuk az összes SAP Netweaver fájlrendszerhez, hogy bemutassuk, hogyan használhatók az Azure NetApp Files. Az SAP fájlrendszerek, amelyeket nem kell nfs-en keresztül csatlakoztatni, [Azure lemeztárolóként](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd) is telepíthetők. Ebben a példában <b>az a-e-nek</b> az Azure NetApp-fájlokon kell lennie, és <b>az f-g</b> (azaz /usr/sap/<b>QAS</b>/D<b>02</b>, /usr/sap/<b>QAS</b>/D<b>03)</b>Azure lemeztárolóként telepíthető. 

### <a name="important-considerations"></a>Fontos szempontok

Ha az SAP Netweaver sap Netweaver alkalmazásának Azure NetApp-fájljait veszi figyelembe a SUSE magas rendelkezésre állású architektúrán, vegye figyelembe az alábbi fontos szempontokat:

- A minimális kapacitású medence 4 TiB. A kapacitáskészlet mérete 1 TiB lépésekben növelhető.
- A minimális térfogat 100 GiB
- Az Azure NetApp-fájloknak és minden olyan virtuális gépnek, ahol az Azure NetApp-fájlok kötetei csatlakoztatva lesznek, ugyanabban az Azure virtuális hálózatban vagy [társviszonyban lévő virtuális hálózatokban](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) kell lennie ugyanabban a régióban. Az Azure NetApp-fájlok hozzáférés virtuális hálózaton keresztül társviszony-létesítés ugyanabban a régióban támogatott most. Az Azure NetApp-hozzáférés globális társviszony-létesítésen keresztül még nem támogatott.
- A kijelölt virtuális hálózatnak rendelkeznie kell egy alhálózattal, amelyet az Azure NetApp-fájlok delegálnak.
- Az Azure NetApp Files [exportálási szabályzatot](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)kínál: szabályozhatja az engedélyezett ügyfeleket, a hozzáférési típust (olvasás&írás, írás, írás stb.). 
- Az Azure NetApp Files szolgáltatás még nem ismeri a zónát. Jelenleg az Azure NetApp Files szolgáltatás nem telepíthető az Azure-régió összes rendelkezésre állási zónájában. Vegye figyelembe a lehetséges késési következményekkel jár egyes Azure-régiókban. 
- Az Azure NetApp Files kötetek NFSv3 vagy NFSv4.1 kötetként telepíthetők. Mindkét protokoll támogatja az SAP alkalmazásréteg (ASCS/ERS, SAP alkalmazáskiszolgálók). 

## <a name="setting-up-ascs"></a>Beállítás (A)SCS

Ebben a példában az erőforrások manuálisan lett telepítve az [Azure Portalon](https://portal.azure.com/#home)keresztül.

### <a name="deploy-linux-manually-via-azure-portal"></a>Linux manuális üzembe helyezése az Azure Portalon keresztül

Először létre kell hoznia az Azure NetApp Files köteteket. Telepítse a virtuális gépeket. Ezután hozzon létre egy terheléselosztót, és használja a háttérkészletben lévő virtuális gépeket.

1. Hozzon létre terheléselosztót (belső, standard):  
   1. Előtér-IP-címek létrehozása
      1. IP-cím 192.168.14.9 az ASCS-hez
         1. Nyissa meg a terheléselosztót, válassza az előtétes IP-készletet, és kattintson a Hozzáadás gombra
         1. Adja meg az új előtér-IP-készlet nevét (például **előtér. A kas. ASK**)
         1. Állítsa a hozzárendelést Statikusra, és adja meg az IP-címet (például **192.168.14.9**)
         1. Kattintson az OK gombra
      1. IP-cím 192.168.14.10 az ASCS ERS esetében
         * Ismételje meg a fenti lépéseket az "a" alatt az ERS IP-címének létrehozásához (például **192.168.14.10** és **előtér). A kas. ERS**)
   1. A háttérkészlet létrehozása
      1. Nyissa meg a terheléselosztót, jelölje ki a háttérkészleteket, és kattintson a Hozzáadás gombra
      1. Adja meg az új háttérkészlet nevét (például **háttér- készlet. Qas**)
      1. Kattintson a Virtuális gép hozzáadása gombra.
      1. Válassza a Virtuális gép lehetőséget. 
      1. Válassza ki az (A)SCS-fürt virtuális gépeit és IP-címeit.
      1. Kattintson az Add (Hozzáadás) parancsra
   1. Az állapotminta-szondák létrehozása
      1. 620**00-as** port ASCS-hez
         1. Nyissa meg a terheléselosztót, válassza ki az állapotmintakat, és kattintson a Hozzáadás gombra
         1. Adja meg az új állapotminta nevét (például **az állapotát. A kas. ASK**)
         1. Válassza a TCP protokollt, a 620**00-as**portot, az 5- es intervallumot és a nem megfelelő küszöbértéket 2
         1. Kattintson az OK gombra
      1. 621**01-es** port ASCS ERS-hez
            * Ismételje meg a fenti lépéseket a "c" alatt, hogy hozzon létre egy állapotminta az ERS (például 621**01** és **az egészségügyi. A kas. ERS**)
   1. Terheléselosztási szabályok
      1. Az ASCS terheléselosztási szabályai
         1. Nyissa meg a terheléselosztót, válassza a Terheléselosztási szabályok lehetőséget, és kattintson a Hozzáadás gombra.
         1. Adja meg az új terheléselosztó szabály nevét (például **lb. A kas. ASK**)
         1. Válassza ki az ELŐTÉR IP-címét az ASCS, a háttérkészlet és a korábban létrehozott állapotminta (például **előtér. A kas. ASCS**, **háttér. QAS** és **az egészség. A kas. ASK**)
         1. **HA-portok** kiválasztása
         1. Az tétlen időszám növelése 30 percre
         1. **A lebegő IP engedélyezése**
         1. Kattintson az OK gombra
         * Ismételje meg a fenti lépéseket az ERS terheléselosztási szabályainak létrehozásához (például **lb. A kas. ERS**)
1. Másik lehetőségként, ha a forgatókönyv alapvető terheléselosztót (belső) igényel, kövesse az alábbi lépéseket:  
   1. Előtér-IP-címek létrehozása
      1. IP-cím 192.168.14.9 az ASCS-hez
         1. Nyissa meg a terheléselosztót, válassza az előtétes IP-készletet, és kattintson a Hozzáadás gombra
         1. Adja meg az új előtér-IP-készlet nevét (például **előtér. A kas. ASK**)
         1. Állítsa a hozzárendelést Statikusra, és adja meg az IP-címet (például **192.168.14.9**)
         1. Kattintson az OK gombra
      1. IP-cím 192.168.14.10 az ASCS ERS esetében
         * Ismételje meg a fenti lépéseket az "a" alatt az ERS IP-címének létrehozásához (például **192.168.14.10** és **előtér). A kas. ERS**)
   1. A háttérkészlet létrehozása
      1. Nyissa meg a terheléselosztót, jelölje ki a háttérkészleteket, és kattintson a Hozzáadás gombra
      1. Adja meg az új háttérkészlet nevét (például **háttér- készlet. Qas**)
      1. Kattintson a Virtuális gép hozzáadása gombra.
      1. Válassza ki az ASCS-hez korábban létrehozott rendelkezésre állási készletet 
      1. Válassza ki az (A)SCS-fürt virtuális gépeit
      1. Kattintson az OK gombra
   1. Az állapotminta-szondák létrehozása
      1. 620**00-as** port ASCS-hez
         1. Nyissa meg a terheléselosztót, válassza ki az állapotmintakat, és kattintson a Hozzáadás gombra
         1. Adja meg az új állapotminta nevét (például **az állapotát. A kas. ASK**)
         1. Válassza a TCP protokollt, a 620**00-as**portot, az 5- es intervallumot és a nem megfelelő küszöbértéket 2
         1. Kattintson az OK gombra
      1. 621**01-es** port ASCS ERS-hez
            * Ismételje meg a fenti lépéseket a "c" alatt, hogy hozzon létre egy állapotminta az ERS (például 621**01** és **az egészségügyi. A kas. ERS**)
   1. Terheléselosztási szabályok
      1. 32**00** TCP ASCS-hez
         1. Nyissa meg a terheléselosztót, válassza a Terheléselosztási szabályok lehetőséget, és kattintson a Hozzáadás gombra.
         1. Adja meg az új terheléselosztó szabály nevét (például **lb. A kas. ASCS.3200**)
         1. Válassza ki az ELŐTÉR IP-címét az ASCS, a háttérkészlet és a korábban létrehozott állapotminta (például **előtér. A kas. ASK**)
         1. TCP protokoll **megtartása**, adja meg a **3200-as portot**
         1. Az tétlen időszám növelése 30 percre
         1. **A lebegő IP engedélyezése**
         1. Kattintson az OK gombra
      1. További portok az ASCS-hez
         * A fenti lépéseket a "d" alatt meg kell ismételni a 36**00,** 39**00,** 81**00**, 5**00**13, 5**00**14, 5**00**16 és tcp portok esetében az ASCS esetében
      1. További portok az ASCS ERS-hez
         * A fenti lépéseket a "d" alatt meg kell ismételni a 32**01,** 33**01,** 5**01**13, 5**01**14, 5**01 16**és tcp portok esetében az ASCS ERS esetében

      > [!Note]
      > Ha a nyilvános IP-címekkel nem rendelkező virtuális gépek a belső (nyilvános IP-cím nélküli) standard Azure-terheléselosztó háttérkészletébe kerülnek, nem lesz kimenő internetkapcsolat, kivéve, ha további konfigurációt hajt végre a nyilvános végpontok útválasztásának engedélyezéséhez. A kimenő kapcsolat eléréséről további információt a [nyilvános végpont-kapcsolat az Azure Standard Load Balancer használatával az SAP magas rendelkezésre állású forgatókönyvekben használó virtuális gépekhez.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections)  

      > [!IMPORTANT]
      > Ne engedélyezze a TCP-időbélyegeket az Azure Load Balancer mögött elhelyezett Azure-beli virtuális gépeken. A TCP-időbélyegek engedélyezése az állapotminta sikertelensítését eredményezi. Állítsa a **net.ipv4.tcp_timestamps** paramétert **0-ra**. További részletek: [Terheléselosztó állapotminta.](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)

## <a name="disable-id-mapping-if-using-nfsv41"></a>Azonosító-hozzárendelés letiltása (NFSv4.1 használata esetén)

Az ebben a szakaszban található utasítások csak akkor alkalmazhatók, ha az Azure NetApp Files kötetek NFSv4.1 protokollal használatával. Végezze el a konfigurációt az összes virtuális gépen, ahol az Azure NetApp Files NFSv4.1 kötetek lesznek csatlakoztatva.  

1. Ellenőrizze az NFS-tartomány beállítását. Győződjön meg arról, hogy a tartomány alapértelmezett Azure NetApp Files tartományként van konfigurálva, azaz **`defaultv4iddomain.com`** a leképezés **senkire**van állítva.  

    > [!IMPORTANT]
    > Győződjön meg arról, hogy `/etc/idmapd.conf` a virtuális gép NFS-tartományát úgy **`defaultv4iddomain.com`** állítja be, hogy megfeleljen az Azure NetApp-fájlok alapértelmezett tartománykonfigurációjának: . Ha eltérés van az NFS-ügyfél (azaz a virtuális gép) és az NFS-kiszolgáló tartománykonfigurációja, azaz az Azure NetApp-konfiguráció között, akkor a virtuális gépekre csatlakoztatott `nobody`Azure NetApp-kötetek fájljaira vonatkozó engedélyek a következőképpen jelennek meg: .  

    <pre><code>
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = <b>defaultv4iddomain.com</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** `nfs4_disable_idmapping`Ellenőrizze. Meg kell állítani, hogy **Y**. A címtárstruktúra `nfs4_disable_idmapping` létrehozásához hajtsa végre a csatlakoztatási parancsot. A könyvtárat nem lehet manuálisan létrehozni a /sys/modules kapcsoló alatt, mert a hozzáférés a kernel / illesztőprogramok számára van fenntartva.  

    <pre><code>
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 192.168.24.5:/sap<b>QAS</b>
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>

   A paraméter módosításáról `nfs4_disable_idmapping` további https://access.redhat.com/solutions/1749883részleteket a talál.

### <a name="create-pacemaker-cluster"></a>Szívritmus-szabályozó fürt létrehozása

Kövesse a Pacemaker beállítása a Red Hat Enterprise Linux az [Azure-ban,](high-availability-guide-rhel-pacemaker.md) hogy hozzon létre egy alapvető Pacemaker cluster ehhez (A)SCS-kiszolgálóhoz.

### <a name="prepare-for-sap-netweaver-installation"></a>Felkészülés az SAP NetWeaver telepítésére

A következő elemek előtaggal vannak ellátva **az [A]** - az összes csomópontra vonatkozóan, **[1]** - csak az 1- es vagy **a [2]** csomópontra vonatkozik, és csak a 2-es csomópontra vonatkoznak.

1. **[A]** Állomásnév feloldása

   Használhatja a DNS-kiszolgálót, vagy módosíthatja az /etc/hosts kapcsolót az összes csomóponton. Ez a példa az /etc/hosts fájl használatát mutatja be.
   Cserélje le az IP-címet és az állomásnevet a következő parancsokban

    ```
    sudo vi /etc/hosts
    ```

   Szúrja be a következő sorokat az /etc/hosts könyvtárba. Az IP-cím és az állomásnév módosítása a környezetnek megfelelően

    ```
    # IP address of cluster node 1
    192.168.14.5    anftstsapcl1
    # IP address of cluster node 2
    192.168.14.6     anftstsapcl2
    # IP address of the load balancer frontend configuration for SAP Netweaver ASCS
    192.168.14.9    anftstsapvh
    # IP address of the load balancer frontend configuration for SAP Netweaver ERS
    192.168.14.10    anftstsapers
    ```

1. **[1]** Hozzon létre SAP-könyvtárakat az Azure NetApp Files köteten.  
   Csatlakoztassa ideiglenesen az Azure NetApp Files kötetet az egyik virtuális gépre, és hozza létre az SAP-könyvtárakat (fájlelérési utakat).  

    ```
     # mount temporarily the volume
     sudo mkdir -p /saptmp
     # If using NFSv3
     sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 192.168.24.5:/sapQAS /saptmp
     # If using NFSv4.1
     sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys,tcp 192.168.24.5:/sapQAS /saptmp
     # create the SAP directories
     sudo cd /saptmp
     sudo mkdir -p sapmntQAS
     sudo mkdir -p usrsapQASascs
     sudo mkdir -p usrsapQASers
     sudo mkdir -p usrsapQASsys
     sudo mkdir -p usrsapQASpas
     sudo mkdir -p usrsapQASaas
     # unmount the volume and delete the temporary directory
     sudo cd ..
     sudo umount /saptmp
     sudo rmdir /saptmp
    ``` 

1. **[A]** A megosztott könyvtárak létrehozása

   ```
   sudo mkdir -p /sapmnt/QAS
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/QAS/SYS
   sudo mkdir -p /usr/sap/QAS/ASCS00
   sudo mkdir -p /usr/sap/QAS/ERS01
   
   sudo chattr +i /sapmnt/QAS
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/QAS/SYS
   sudo chattr +i /usr/sap/QAS/ASCS00
   sudo chattr +i /usr/sap/QAS/ERS01
   ```

1. **[A]** NFS-ügyfél és egyéb követelmények telepítése

   ```
   sudo yum -y install nfs-utils resource-agents resource-agents-sap
   ```

1. **[A]** Az erőforrás-ügynökök-sap verziójának ellenőrzése

   Győződjön meg arról, hogy a telepített erőforrás-ügynökök-sap csomag verziója legalább 3.9.5-124.el7
   ```
   sudo yum info resource-agents-sap
   
   # Loaded plugins: langpacks, product-id, search-disabled-repos
   # Repodata is over 2 weeks old. Install yum-cron? Or run: yum makecache fast
   # Installed Packages
   # Name        : resource-agents-sap
   # Arch        : x86_64
   # Version     : 3.9.5
   # Release     : 124.el7
   # Size        : 100 k
   # Repo        : installed
   # From repo   : rhel-sap-for-rhel-7-server-rpms
   # Summary     : SAP cluster resource agents and connector script
   # URL         : https://github.com/ClusterLabs/resource-agents
   # License     : GPLv2+
   # Description : The SAP resource agents and connector script interface with
   #          : Pacemaker to allow SAP instances to be managed in a cluster
   #          : environment.
   ```


1. **[A]** Csatlakoztatási bejegyzések hozzáadása

   NFSv3 használata esetén:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
    192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3
    192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS nfs rw,hard,rsize=65536,wsize=65536,vers=3
    192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   Az NFSv4.1 használata esetén:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
    192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
    192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
    192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   ```

   > [!NOTE]
   > Győződjön meg arról, hogy megfeleljen az Azure NetApp Files kötetek NFS protokollverziójának a kötetek csatlakoztatásakor. Ha az Azure NetApp Files kötetek NFSv3 kötetként jönnek létre, használja a megfelelő NFSv3 konfigurációt. Ha az Azure NetApp Files kötetek NFSv4.1 kötetként jönnek létre, kövesse az utasításokat az azonosítóleképezés letiltásához, és győződjön meg arról, hogy a megfelelő NFSv4.1 konfigurációt használja. Ebben a példában az Azure NetApp Files kötetek NFSv3 kötetekként jöttek létre.  

   Az új megosztások csatlakoztatása

   ```
   sudo mount -a  
   ```

1. **[A]** SWAP-fájl konfigurálása

   ```
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=y
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=2000
   ```

   A módosítás aktiválásához indítsa újra az ügynököt

   ```
   sudo service waagent restart
   ```

1. **[A]** RHEL konfiguráció

   Az RHEL konfigurálása az SAP [2002167] megjegyzésében leírtak szerint

### <a name="installing-sap-netweaver-ascsers"></a>Az SAP NetWeaver ASCS/ERS telepítése

1. **[1]** Hozzon létre egy virtuális IP-erőforrást és állapot-mintavételt az ASCS-példányhoz

   ```
   sudo pcs node standby anftstsapcl2
   # If using NFSv3
   sudo pcs resource create fs_QAS_ASCS Filesystem device='192.168.24.5:/sapQAS/usrsapQASascs' \
     directory='/usr/sap/QAS/ASCS00' fstype='nfs' force_unmount=safe \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-QAS_ASCS
   
   # If using NFSv4.1
   sudo pcs resource create fs_QAS_ASCS Filesystem device='192.168.24.5:/sapQAS/usrsapQASascs' \
     directory='/usr/sap/QAS/ASCS00' fstype='nfs' force_unmount=safe options='sec=sys,vers=4.1' \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-QAS_ASCS
   
   sudo pcs resource create vip_QAS_ASCS IPaddr2 \
     ip=192.168.14.9 cidr_netmask=24 \
     --group g-QAS_ASCS
   
   sudo pcs resource create nc_QAS_ASCS azure-lb port=62000 \
     --group g-QAS_ASCS
   ```

   Győződjön meg arról, hogy a fürt állapota rendben van, és hogy minden erőforrás elindult. Nem fontos, hogy melyik csomóponton futnak az erőforrások.

   ```
   sudo pcs status
   
   # Node anftstsapcl2: standby
   # Online: [ anftstsapcl1 ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
   ```

1. **[1]** Telepítse az SAP NetWeaver ASCS-eket  

   Telepítse az SAP NetWeaver ASCS-t gyökérként az első csomópontra egy virtuális állomásnév használatával, amely leképezi az ASCS terheléselosztó előtér-konfigurációjának IP-címét, például <b>anftstsapvh</b>, <b>192.168.14.9</b> és a terheléselosztó mintavételéhez használt példányszámot, például <b>00.</b>

   A sapinst paraméter SAPINST_REMOTE_ACCESS_USER segítségével engedélyezheti, hogy egy nem root felhasználó csatlakozzon a sapinsthoz.

   ```
   # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   Ha a telepítés nem sikerül almappát létrehozni a /usr/sap/**QAS**/ASCS**00**kapcsolóban, próbálja meg beadni az ASCS**00** mappa tulajdonosát és csoportját, majd próbálkozzon újra.

   ```
   sudo chown qasadm /usr/sap/QAS/ASCS00
   sudo chgrp sapsys /usr/sap/QAS/ASCS00
   ```

1. **[1]** Hozzon létre egy virtuális IP-erőforrást és állapot-mintavételt az ERS-példányhoz

   ```
   sudo pcs node unstandby anftstsapcl2
   sudo pcs node standby anftstsapcl1
   
   # If using NFSv3
   sudo pcs resource create fs_QAS_AERS Filesystem device='192.168.24.5:/sapQAS/usrsapQASers' \
     directory='/usr/sap/QAS/ERS01' fstype='nfs' force_unmount=safe \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-QAS_AERS
   
   # If using NFSv4.1
   sudo pcs resource create fs_QAS_AERS Filesystem device='192.168.24.5:/sapQAS/usrsapQASers' \
     directory='/usr/sap/QAS/ERS01' fstype='nfs' force_unmount=safe options='sec=sys,vers=4.1' \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-QAS_AERS
   
   sudo pcs resource create vip_QAS_AERS IPaddr2 \
     ip=192.168.14.10 cidr_netmask=24 \
    --group g-QAS_AERS
   
   sudo pcs resource create nc_QAS_AERS azure-lb port=62101 \
    --group g-QAS_AERS
   ```
 
   Győződjön meg arról, hogy a fürt állapota rendben van, és hogy minden erőforrás elindult. Nem fontos, hogy melyik csomóponton futnak az erőforrások.

   ```
   sudo pcs status
   
   # Node anftstsapcl1: standby
   # Online: [ anftstsapcl2 ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl2
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2<
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
   #  Resource Group: g-QAS_AERS
   #      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
   #      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
   #      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
   ```

1. **[2]** Telepítse az SAP NetWeaver ERS-t  

   Telepítse az SAP NetWeaver ERS-t gyökérként a második csomópontra egy virtuális állomásnév használatával, amely leképezi az ERS terheléselosztó előtér-konfigurációjának IP-címét, például <b>az anftstsapers</b>, <b>192.168.14.10</b> és a terheléselosztó mintavételéhez használt példányszámot, például <b>01.</b>

   A sapinst paraméter SAPINST_REMOTE_ACCESS_USER segítségével engedélyezheti, hogy egy nem root felhasználó csatlakozzon a sapinsthoz.

   ```
   # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp

   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   Ha a telepítés nem tud almappát létrehozni a /usr/sap/**QAS**/ERS**01**kapcsolóban, próbálja meg beadni az ERS**01** mappa tulajdonosát és csoportját, majd próbálkozzon újra.

   ```
   sudo chown qaadm /usr/sap/QAS/ERS01
   sudo chgrp sapsys /usr/sap/QAS/ERS01
   ```

1. **[1]** Az ASCS/SCS és ERS példányprofilok adaptálása

   * ASCS/SCS profil

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   ```

   * ERS profil

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ERS01_anftstsapers
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # remove Autostart from ERS profile
   # Autostart = 1
   ```


1. **[A]** A Keep Alive konfigurálása

   Az SAP NetWeaver alkalmazáskiszolgáló és az ASCS/SCS közötti kommunikáció egy szoftverterhelés-elosztón keresztül történik. A terheléselosztó konfigurálható időtúltöltés után bontja az inaktív kapcsolatokat. Ennek megakadályozása érdekében be kell állítania egy paramétert az SAP NetWeaver ASCS/SCS profilban, és módosítania kell a Linux rendszer beállításait. További információért olvassa el az [SAP Note 1410736][1410736] megjegyzést.

   Az ENQue/encni/set_so_keepalive ASCS/SCS profilparaméter már hozzá lett adva az utolsó lépésben.

   ```
   # Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   ```

1. **[A]** Frissítse a /usr/sap/sapservices fájlt

   Annak érdekében, hogy a sapinit indítási parancsfájl megakadályozza a példányok kezdetét, a Pacemaker által kezelt összes példányt a /usr/sap/sapservices fájlból kell kommentálni. Ne fűzzön megjegyzést az SAP HANA-példányhoz, ha hana sr-rel lesz használva.

   ```
   sudo vi /usr/sap/sapservices
   
   # On the node where you installed the ASCS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/QAS/ASCS00/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ASCS00/exe/sapstartsrv pf=/usr/sap/QAS/SYS/profile/QAS_ASCS00_anftstsapvh -D -u qasadm
   
   # On the node where you installed the ERS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/QAS/ERS01/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ERS01/exe/sapstartsrv pf=/usr/sap/QAS/ERS01/profile/QAS_ERS01_anftstsapers -D -u qasadm
   ```

1. **[1]** Az SAP-fürt erőforrásainak létrehozása

   Ha enqueue server 1 architektúrát (ENSA1) használ, az erőforrásokat a következőképpen határozza meg:

   ```
   sudo pcs property set maintenance-mode=true
   
    sudo pcs resource create rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_AERS
      
    sudo pcs constraint colocation add g-QAS_AERS with g-QAS_ASCS -5000
    sudo pcs constraint location rsc_sap_QAS_ASCS00 rule score=2000 runs_ers_QAS eq 1
    sudo pcs constraint order g-QAS_ASCS then g-QAS_AERS kind=Optional symmetrical=false
    
    sudo pcs node unstandby anftstsapcl1
    sudo pcs property set maintenance-mode=false
    ```

   Az SAP az SAP NW 7.52-es részéhez benyújtotta a 2-es kiszolgáló várólistára állításának támogatását, beleértve a replikációt is. Az ABAP Platform 1809-től kezdve a 2-es várólistára helyezett kiszolgáló alapértelmezés szerint telepítve van. A 2-es kiszolgáló várólistára állításáról az SAP [2630416 megjegyzése.](https://launchpad.support.sap.com/#/notes/2630416)
   Az enqueue server 2 architektúra ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)) használata esetén telepítse az erőforrás-ügynök erőforrás-ügynökeit sap-4.1.1-12.el7.x86_64 vagy újabb, és határozza meg az erőforrásokat az alábbiak szerint:

    ```
    sudo pcs property set maintenance-mode=true
    
    sudo pcs resource create rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_AERS
      
    sudo pcs constraint colocation add g-QAS_AERS with g-QAS_ASCS -5000
    sudo pcs constraint order g-QAS_ASCS then g-QAS_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-QAS_ASCS then stop g-QAS_AERS symmetrical=false
   
    sudo pcs node unstandby anftstsapcl1
    sudo pcs property set maintenance-mode=false
    ```

   Ha régebbi verzióról frissít, és a 2-es kiszolgáló várólistára lép, olvassa el az SAP [2641322 megjegyzését.](https://launchpad.support.sap.com/#/notes/2641322) 

   > [!NOTE]
   > A fenti konfigurációban az időtúltöltések csak példák, és előfordulhat, hogy az adott SAP-beállításhoz kell igazítani. 

   Győződjön meg arról, hogy a fürt állapota rendben van, és hogy minden erőforrás elindult. Nem fontos, hogy melyik csomóponton futnak az erőforrások.

    ```
    sudo pcs status
    
    # Online: [ anftstsapcl1 anftstsapcl2 ]
    #
    # Full list of resources:
    #
    # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl2
    #  Resource Group: g-QAS_ASCS
    #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
    #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
    #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
    #      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    #  Resource Group: g-QAS_AERS
    #      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
    #      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
    #      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
    #      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. **[A]** Adja hozzá az ASCS és az ERS tűzfalszabályait mindkét csomóponton Adja hozzá az ASCS és az ERS tűzfalszabályait mindkét csomóponton.
   ```
   # Probe Port of ASCS
   sudo firewall-cmd --zone=public --add-port=62000/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=62000/tcp
   sudo firewall-cmd --zone=public --add-port=3200/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3200/tcp
   sudo firewall-cmd --zone=public --add-port=3600/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3600/tcp
   sudo firewall-cmd --zone=public --add-port=3900/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3900/tcp
   sudo firewall-cmd --zone=public --add-port=8100/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=8100/tcp
   sudo firewall-cmd --zone=public --add-port=50013/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50013/tcp
   sudo firewall-cmd --zone=public --add-port=50014/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50014/tcp
   sudo firewall-cmd --zone=public --add-port=50016/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50016/tcp
   # Probe Port of ERS
   sudo firewall-cmd --zone=public --add-port=62101/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=62101/tcp
   sudo firewall-cmd --zone=public --add-port=3301/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3301/tcp
   sudo firewall-cmd --zone=public --add-port=50113/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50113/tcp
   sudo firewall-cmd --zone=public --add-port=50114/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50114/tcp
   sudo firewall-cmd --zone=public --add-port=50116/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50116/tcp
   ```

## <a name="sap-netweaver-application-server-preparation"></a><a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>SAP NetWeaver alkalmazáskiszolgáló előkészítése

   Egyes adatbázisok megkövetelik, hogy az adatbázispéldány telepítése egy alkalmazáskiszolgálón kerül-e végrehajtásra. Készítse elő az alkalmazáskiszolgáló virtuális gépeit, hogy ezekben az esetekben használhassák őket.  

   A lépések fújtató feltételezik, hogy az alkalmazáskiszolgálótelepítése az ASCS/SCS és HANA kiszolgálóktól eltérő kiszolgálóra. Ellenkező esetben az alábbi lépések némelyike (például az állomásnév feloldása) nem szükséges.  

   A következő elemek előtaggal vannak ellátva , amelyek **vagy [A]** - mind a PAS, mind az AAS, **[P]** - csak a PAS vagy **az [S]** - csak az AAS-ra vonatkoznak.  

1. **[A]** Telepítési állomásnévfeloldás: Használhatja a DNS-kiszolgálót, vagy módosíthatja az /etc/hosts kapcsolót az összes csomóponton. Ez a példa az /etc/hosts fájl használatát mutatja be.
   Cserélje le az IP-címet és az állomásnevet a következő parancsokban:  

   ```
   sudo vi /etc/hosts
   ```

   Szúrja be a következő sorokat az /etc/hosts könyvtárba. Módosítsa az IP-címet és az állomásnevet a környezetének megfelelően.

   ```
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   192.168.14.9 anftstsapvh
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   192.168.14.10 anftstsapers
   192.168.14.7 anftstsapa01
   192.168.14.8 anftstsapa02
   ```

1. **[A]** Hozza létre a sapmnt könyvtárlétrehozása a sapmnt könyvtárat.
   ```
   sudo mkdir -p /sapmnt/QAS
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/QAS
   sudo chattr +i /usr/sap/trans
   ```

1. **[A]** NFS-ügyfél és egyéb követelmények telepítése  

   ```
   sudo yum -y install nfs-utils uuidd
   ```

1. **[A]** Csatlakoztatási bejegyzések hozzáadása  
   NFSv3 használata esetén:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3
   192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   Az NFSv4.1 használata esetén:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   ```

   Az új megosztások csatlakoztatása

   ```
   sudo mount -a
   ```

1. **[P]** A PAS könyvtár létrehozása és csatlakoztatása  
   NFSv3 használata esetén:
   ```
   sudo mkdir -p /usr/sap/QAS/D02
   sudo chattr +i /usr/sap/QAS/D02
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # Mount
   sudo mount -a
   ```

   Az NFSv4.1 használata esetén:
   ```
   sudo mkdir -p /usr/sap/QAS/D02
   sudo chattr +i /usr/sap/QAS/D02
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   
   # Mount
   sudo mount -a
   ```

1. **[S]** Az AAS könyvtár létrehozása és csatlakoztatása  
   NFSv3 használata esetén:
   ```
   sudo mkdir -p /usr/sap/QAS/D03
   sudo chattr +i /usr/sap/QAS/D03
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # Mount
   sudo mount -a
   ```

   Az NFSv4.1 használata esetén:
   ```
   sudo mkdir -p /usr/sap/QAS/D03
   sudo chattr +i /usr/sap/QAS/D03
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   
   # Mount
   sudo mount -a
   ```

1. **[A]** SWAP-fájl konfigurálása
 
   ```
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=y
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=2000
   ```

   A módosítás aktiválásához indítsa újra az ügynököt

   ```
   sudo service waagent restart
   ```

## <a name="install-database"></a>Az adatbázis telepítése

Ebben a példában az SAP NetWeaver telepítve van az SAP HANA.In this example, SAP NetWeaver is installed on SAP HANA. A telepítéshez minden támogatott adatbázist használhat. Az SAP HANA Azure-beli telepítéséről az [SAP HANA magas rendelkezésre állású az Azure-beli virtuális gépeken a Red Hat Enterprise Linux][sap-hana-ha]. For a list of supported databases, see [SAP Note 1928533][1928533]on című témakörben talál további információt.

1. Az SAP-adatbázispéldány telepítésének futtatása

   Telepítse az SAP NetWeaver adatbázispéldányt gyökérként egy virtuális állomásnév használatával, amely leképezi az adatbázis terheléselosztó előtér-konfigurációjának IP-címét.

   A sapinst paraméter SAPINST_REMOTE_ACCESS_USER segítségével engedélyezheti, hogy egy nem root felhasználó csatlakozzon a sapinsthoz.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

## <a name="sap-netweaver-application-server-installation"></a>SAP NetWeaver alkalmazáskiszolgáló telepítése

Az SAP-alkalmazáskiszolgáló telepítéséhez kövesse az alábbi lépéseket.

1. Alkalmazáskiszolgáló előkészítése

   Kövesse az SAP [NetWeaver alkalmazáskiszolgáló fenti előkészítésének](high-availability-guide-rhel.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) fejezetében leírt lépéseket az alkalmazáskiszolgáló előkészítéséhez.

1. Sap NetWeaver alkalmazáskiszolgáló telepítése

   Telepítsen egy elsődleges vagy további SAP NetWeaver alkalmazáskiszolgálót.

   A sapinst paraméter SAPINST_REMOTE_ACCESS_USER segítségével engedélyezheti, hogy egy nem root felhasználó csatlakozzon a sapinsthoz.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

1. SAP HANA biztonságos tároló frissítése

   Frissítse az SAP HANA biztonságos tárolót, hogy az SAP HANA rendszerreplikációs telepítő virtuális nevére mutasson.

   A következő parancs futtatásával \<sapsid>adm néven listázható.

   ```
   hdbuserstore List
   ```

   Ennek fel kell sorolnia az összes bejegyzést, és
   ```
   DATA FILE       : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.DAT
   KEY FILE        : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 192.168.14.4:30313
     USER: SAPABAP1
     DATABASE: QAS
   ```

   A kimenet azt mutatja, hogy az alapértelmezett bejegyzés IP-címe a virtuális gépre mutat, és nem a terheléselosztó IP-címére. Ezt a bejegyzést módosítani kell, hogy a terheléselosztó virtuális állomásnevére mutasson. Győződjön meg róla, hogy ugyanazt a portot **(30313** a fenti kimenet) és az adatbázis nevét **(QAS** a fenti kimenet)!

   ```
   su - qasadm
   hdbuserstore SET DEFAULT qasdb:30313@QAS SAPABAP1 <password of ABAP schema>
   ```

## <a name="test-the-cluster-setup"></a>A fürt beállításának tesztelése

1. Az ASCS-példány manuális áttelepítése

   Erőforrás állapota a teszt megkezdése előtt:

   ```
    rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   Az ASCS-példány áttelepítéséhez futtassa a következő parancsokat gyökérként.

   ```
   [root@anftstsapcl1 ~]# pcs resource move rsc_sap_QAS_ASCS00
   
   [root@anftstsapcl1 ~]# pcs resource clear rsc_sap_QAS_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Erőforrás állapota a teszt után:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. Csomópontösszeomlás szimulálása

   Erőforrás állapota a teszt megkezdése előtt:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

   A következő parancs futtatása gyökérként azon a csomóponton, ahol az ASCS-példány fut

   ```
   [root@anftstsapcl2 ~]# echo b > /proc/sysrq-trigger
   ```

   A csomópont újraindítása utáni állapotnak így kell kinéznie.

   ```
   Online: [ anftstsapcl1 anftstsapcl2 ]
   
   Full list of resources:
   
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   
   Failed Actions:
   * rsc_sap_QAS_ERS01_monitor_11000 on anftstsapcl1 'not running' (7): call=45, status=complete, exitreason='',
   ```

   A következő paranccsal tisztítsa meg a meghibásodott erőforrásokat.

   ```
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Erőforrás állapota a teszt után:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. Üzenetkiszolgáló folyamatának leölése

   Erőforrás állapota a teszt megkezdése előtt:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```
   
   Futtassa a következő parancsokat gyökérként az üzenetkiszolgáló folyamatának azonosításához és megöléséhez.

   ```
   [root@anftstsapcl1 ~]# pgrep ms.sapQAS | xargs kill -9
   ```

   Ha csak egyszer öli meg az üzenetkiszolgálót, a program újraindítja. `sapstart` Ha elég gyakran öli meg, pacemaker végül áthelyezi az ASCS-példányt a másik csomópontra. Futtassa a következő parancsokat gyökérként az ASCS és az ERS-példány erőforrásállapotának karbantartásához a teszt után.

   ```
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ASCS00
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Erőforrás állapota a teszt után:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. Kill enqueue szerver folyamat

   Erőforrás állapota a teszt megkezdése előtt:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

   Futtassa a következő parancsokat gyökérként azon a csomóponton, ahol az ASCS-példány fut a várólistán lévő kiszolgáló kiiktatása érdekében.

   ```
   [root@anftstsapcl2 ~]# pgrep en.sapQAS | xargs kill -9
   ```

   Az ASCS-példánynak azonnal át kell adnia a másik csomópontnak. Az ERS-példánynak az ASCS-példány indítása után is át kell adnia a feladatát. Futtassa a következő parancsokat gyökérként az ASCS és az ERS-példány erőforrásállapotának karbantartásához a teszt után.

   ```
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ASCS00
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Erőforrás állapota a teszt után:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. A várólistán lévő replikációs kiszolgáló folyamatának leállítása

   Erőforrás állapota a teszt megkezdése előtt:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   Futtassa a következő parancsot gyökérként azon a csomóponton, ahol az ERS-példány fut, hogy megszakítsa a várólistán lévő replikációs kiszolgáló folyamatát.

   ```
   [root@anftstsapcl2 ~]# pgrep er.sapQAS | xargs kill -9
   ```

   Ha csak egyszer futtatja `sapstart` a parancsot, újraindítja a folyamatot. Ha elég gyakran futtatja, nem indítja újra a folyamatot, `sapstart` és az erőforrás leállított állapotban lesz. Futtassa a következő parancsokat gyökérként az ERS-példány erőforrásállapotának a teszt utáni karbantartásához.

   ```
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Erőforrás állapota a teszt után:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. Kill enqueue sapstartsrv folyamat

   Erőforrás állapota a teszt megkezdése előtt:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   Futtassa a következő parancsokat gyökérként azon a csomóponton, ahol az ASCS fut.

   ```
   [root@anftstsapcl1 ~]# pgrep -fl ASCS00.*sapstartsrv
   # 59545 sapstartsrv
   
   [root@anftstsapcl1 ~]# kill -9 59545
   ```

   A sapstartsrv folyamat mindig újra kell indítani a Pacemaker erőforrás ügynök a figyelés részeként. Erőforrás állapota a teszt után:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

## <a name="next-steps"></a>További lépések

* [HA SAP NW az Azure-beli virtuális gépek RHEL SAP-alkalmazások több SID útmutató](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid)
* [Az Azure virtuális gépek tervezése és megvalósítása az SAP-hoz][planning-guide]
* [Az Azure virtuális gépek üzembe helyezése az SAP-hoz][deployment-guide]
* [Az Azure virtual machines DBMS üzembe helyezése az SAP-hoz][dbms-guide]
* Ha meg szeretné tudni, hogyan hozhat létre magas rendelkezésre állást, és tervezze meg az SAP HANA vészutáni helyreállítását az Azure-ban (nagy példányok), tekintse meg az [SAP HANA (nagy példányok) magas rendelkezésre állású és vész-helyreállítási az Azure-ban.](hana-overview-high-availability-disaster-recovery.md)
* Ha meg szeretné tudni, hogyan hozhat létre magas rendelkezésre állást, és tervezze meg az SAP HANA vészutáni helyreállítását az Azure virtuális gépeken, olvassa el [az SAP HANA magas rendelkezésre állását az Azure virtuális gépeken (VM-ek) című témakört.][sap-hana-ha]
