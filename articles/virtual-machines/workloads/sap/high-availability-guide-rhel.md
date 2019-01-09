---
title: Az Azure virtuális gépek magas rendelkezésre állás az SAP NetWeaver a Red Hat Enterprise Linux |} A Microsoft Docs
description: Az Azure virtuális gépek magas rendelkezésre állás az SAP NetWeaver a Red Hat Enterprise Linux
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/27/2017
ms.author: sedusch
ms.openlocfilehash: c7805e64c4f387b870922dcb63e20d86f691092a
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54119016"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver-on-red-hat-enterprise-linux"></a>Az Azure virtuális gépek magas rendelkezésre állás az SAP NetWeaver a Red Hat Enterprise Linux

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

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

Ez a cikk ismerteti a virtuális gépek üzembe helyezése, konfigurálja a virtuális gépek, telepítse a fürt keretrendszert és egy magas rendelkezésre állású SAP NetWeaver 7.50 rendszer telepítéséhez.
A példa konfigurációk esetén a telepítési parancsokat stb. ASCS példányszámának 00, SSZON példányok száma 02, és az SAP-rendszer azonosító NW1 szolgál. Az erőforrások (például virtuális gépek, virtuális hálózatok) a példában nevei azt feltételezik, hogy használja a [ASCS/SCS sablon] [ template-multisid-xscs] az erőforrás előtag NW1 erőforrások létrehozásához.

Olvassa el először a következő SAP-megjegyzések és tanulmányok

* SAP-Jegyzetnek [1928533], amely rendelkezik:
  * SAP szoftver központi telepítése által támogatott Azure-beli Virtuálisgép-méretek
  * Azure-beli Virtuálisgép-méretek esetében fontos kapacitásadatok
  * Támogatott SAP-szoftverek és operációs rendszer (OS) és adatbázis-kombinációk
  * Szükséges SAP kernel verziója a Windows és Linux a Microsoft Azure

* SAP-Jegyzetnek [2015553] az Azure-beli SAP az SAP által támogatott szoftverek központi telepítéséhez szükséges előfeltételeket ismerteti.
* SAP-Jegyzetnek [2002167] javasolt a Red Hat Enterprise Linux operációs rendszer beállításai
* SAP-Jegyzetnek [2009879] Red Hat Enterprise Linux for SAP HANA-irányelvek rendelkezik
* SAP-Jegyzetnek [2178632] részletes jelentett az Azure-beli SAP-figyelési metrikákat kapcsolatos információkat tartalmaz.
* SAP-Jegyzetnek [2191498] rendelkezik a szükséges SAP gazdagép-ügynök verziója Linux az Azure-ban.
* SAP-Jegyzetnek [2243692] SAP linuxon az Azure-beli licenceléssel kapcsolatos információkat tartalmaz.
* SAP-Jegyzetnek [1999351] további információkat talál az Azure Enhanced Monitoring bővítményt az SAP rendelkezik.
* [Az SAP közösségi WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) rendelkezik az összes szükséges SAP-megjegyzések Linux rendszeren.
* [Az Azure virtuális gépek tervezése és megvalósítása az SAP, Linux rendszeren][planning-guide]
* [Az Azure virtuális gépek üzembe helyezése, az SAP, Linux rendszeren][deployment-guide]
* [Az Azure Virtual Machines DBMS üzembe helyezése, az SAP, Linux rendszeren][dbms-guide]
* [Red Hat Gluster Storage termék dokumentációja](https://access.redhat.com/documentation/red_hat_gluster_storage/)
* [SAP Netweaver támasztja fürtben](https://access.redhat.com/articles/3150081)
* Általános RHEL-dokumentáció
  * [Magas rendelkezésre állású bővítmény áttekintése](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Magas rendelkezésre állású kiegészítő felügyeleti](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Magas rendelkezésre állású bővítmény referencia](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [SAP Netweaver RHEL 7.5 önálló erőforrásokkal ASCS/SSZON konfigurálása](https://access.redhat.com/articles/3569681)
* Az Azure egyedi RHEL dokumentációja:
  * [RHEL magas rendelkezésre állású fürtöket – Microsoft Azure virtuális gépek a fürt tagjai terméktámogatási irányelveinek](https://access.redhat.com/articles/3131341)
  * [Telepítése és a Microsoft Azure egy Red Hat Enterprise Linux 7.4-es (és újabb verziók) magas rendelkezésre állású fürt konfigurálása](https://access.redhat.com/articles/3252491)

## <a name="overview"></a>Áttekintés

Magas rendelkezésre állás, az SAP NetWeaver megosztott tároló szükséges hozzá. GlusterFS külön fürtben van konfigurálva, és több SAP-rendszerek által használható.

![SAP NetWeaver magas rendelkezésre állás – Áttekintés](./media/high-availability-guide-rhel/ha-rhel.png)

SAP NetWeaver ascs rendszerbe fut be, az SAP NetWeaver SCS, az SAP NetWeaver SSZON és az SAP HANA-adatbázis virtuális állomásnevet és a virtuális IP-címek használata. Az Azure-ban a terheléselosztó virtuális IP-cím szükséges. Az alábbi lista tartalmazza (A) konfigurációjának SCS és SSZON terheléselosztó.

### <a name="ascs"></a>(A)SCS

* Előtér-konfigurációjához
  * IP-cím 10.0.0.7
* Háttér-konfiguráció
  * (A) részének kell lennie az összes virtuális gépek elsődleges hálózati adaptere csatlakozik SCS/SSZON fürt
* Mintavételi Port
  * Port 620**&lt;nr&gt;**
* Terheléselosztás szabályok
  * 32**&lt;nr&gt;**  TCP
  * 36**&lt;nr&gt;**  TCP
  * 39**&lt;nr&gt;**  TCP
  * 81-es**&lt;nr&gt;**  TCP
  * 5**&lt;nr&gt;** 13 TCP
  * 5**&lt;nr&gt;** 14 TCP
  * 5**&lt;nr&gt;** 16 TCP

### <a name="ers"></a>ERS

* Előtér-konfigurációjához
  * IP-cím 10.0.0.8
* Háttér-konfiguráció
  * (A) részének kell lennie az összes virtuális gépek elsődleges hálózati adaptere csatlakozik SCS/SSZON fürt
* Mintavételi Port
  * Port 621**&lt;nr&gt;**
* Terheléselosztás szabályok
  * 33**&lt;nr&gt;**  TCP
  * 5**&lt;nr&gt;** 13 TCP
  * 5**&lt;nr&gt;** 14 TCP
  * 5**&lt;nr&gt;** 16 TCP

## <a name="setting-up-glusterfs"></a>GlusterFS beállítása

SAP NetWeaver az átvitel és a profil Directory megosztott tároló szükséges hozzá. Olvasási [az Azure Virtual machines Red Hat Enterprise Linux for SAP NetWeaver GlusterFS] [ glusterfs-ha] a GlusterFS beállítása az SAP NetWeaver számára.

## <a name="setting-up-ascs"></a>(A) SCS beállítása

Minden szükséges Azure-erőforrás, beleértve a virtuális gépek üzembe helyezéséhez vagy használhatja az Azure-sablont a githubból, rendelkezésre állás beállítása és a terheléselosztó vagy manuálisan telepítheti az erőforrásokat.

### <a name="deploy-linux-via-azure-template"></a>Linux-n keresztül az Azure-sablon üzembe helyezése

Az Azure Marketplace-en kép Red Hat Enterprise Linux, amelyek segítségével telepíthet új virtuális gépeket tartalmazza. Használhatja a gyorsindítási sablonok egyikét a githubon üzembe helyezéséhez szükséges összes erőforrást. A sablon üzembe helyezi a virtuális gépek, a terheléselosztó, a rendelkezésre állási csoport stb. Kövesse az alábbi lépéseket a sablon üzembe helyezéséhez:

1. Nyissa meg a [ASCS/SCS sablon] [ template-multisid-xscs] az Azure Portalon  
1. Adja meg a következő paraméterek
   1. Erőforrás-előtag  
      Adja meg a használni kívánt előtagot. Az érték előtagjaként is szolgál az üzembe helyezett erőforrásokat.
   1. A készlet típusa  
      Az SAP NetWeaver stack típusának kiválasztása
   1. Operációs rendszer típusa  
      Válasszon ki egy Linux-disztribúció. Ebben a példában válassza ki az RHEL 7
   1. Adatbázis típusa  
      Válassza ki a HANA
   1. Az SAP-rendszer száma  
      A fürtben futó SAP-rendszer száma. Válassza az 1.
   1. Rendszer rendelkezésre állását  
      Válassza ki a magas rendelkezésre ÁLLÁS
   1. Rendszergazdai felhasználónév, a rendszergazdai jelszó vagy SSH-kulcs  
      Egy új felhasználót hoz létre, amely segítségével jelentkezzen be a számítógépen.
   1. Alhálózati azonosító  
   Ha azt szeretné, helyezheti üzembe a virtuális gép egy meglévő Vnetet, amelyekben egy meghatározott alhálózatot a virtuális gép hozzá kell rendelni, nevezze el a kívánt alhálózatot. Az azonosító általában néz ki: /subscriptions/**&lt;előfizetés-azonosító&gt;**/resourceGroups/**&lt;erőforráscsoport-név&gt;**/szolgáltatók/ Microsoft.Network/virtualNetworks/**&lt;virtuálishálózat-nevet&gt;**/subnets/**&lt;alhálózat neve&gt;**

### <a name="deploy-linux-manually-via-azure-portal"></a>Manuálisan üzembe helyezése Linux rendszerű Azure-portálon

Először a virtuális gépek a fürt létrehozásához. Ezt követően hozzon létre egy terheléselosztó, és a virtuális gépek használata a háttérkészletek.

1. Erőforráscsoport létrehozása
1. Virtuális hálózat létrehozása
1. Egy rendelkezésre állási csoport létrehozása  
   Maximális frissítési tartomány beállítása
1. 1 virtuális gép létrehozása  
   Legalább RHEL 7, ez például a Red Hat Enterprise Linux 7.4-lemezkép <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux74-ARM>  
   Válassza ki a korábban létrehozott rendelkezésre állási  
1. 2 virtuális gép létrehozása  
   Legalább RHEL 7, ez például a Red Hat Enterprise Linux 7.4-lemezkép <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux74-ARM>  
   Válassza ki a korábban létrehozott rendelkezésre állási  
1. Mindkét virtuális gép legalább egy adatlemez hozzáadása  
   Az adatlemezek használtusr/sap/`<SAPSID`> könyvtár
1. Load Balancer létrehozása (belső)  
   1. Hozzon létre az előtérbeli IP-címek
      1. Az ASCS 10.0.0.7 IP-címe
         1. Nyissa meg a terheléselosztó, válassza ki az előtérbeli IP-címkészlet, kattintson a Hozzáadás gombra
         1. Adja meg az új előtérbeli IP-címkészlet nevét (például **nw1 – ascs-frontend**)
         1. A hozzárendelés Static értékre, és adja meg az IP-címet (például **10.0.0.7**)
         1. Kattintson az OK gombra
      1. Az ASCS SSZON 10.0.0.8 átjáró IP-címe
         * Ismételje meg a fenti IP-cím létrehozása a SSZON lépéseket (például **10.0.0.8** és **nw1-aers-háttérrendszer**)
   1. Hozzon létre a háttérkészletek
      1. Az ASCS háttérkészlet létrehozása
         1. Nyissa meg a terheléselosztó, válassza ki a háttérkészletek, és kattintson a Hozzáadás gombra
         1. Adja meg az új háttérkészlet nevét (például **nw1 – ascs-háttérrendszer**)
         1. Kattintson a Hozzáadás elemre egy virtuális gépet.
         1. Válassza ki a korábban létrehozott rendelkezésre állási csoport
         1. Válassza ki a virtuális gépek (A) SCS-fürt
         1. Kattintson az OK gombra
      1. Az ASCS SSZON háttérkészlet létrehozása
         * Ismételje meg a fenti lépéseket, és hozzon létre egy háttérkészlet on a felhasználók számára (például **nw1-aers-háttérrendszer**)
   1. Az állapotminták létrehozása
      1. Port 620**00** ASCS számára
         1. Nyissa meg a terheléselosztó, válassza ki az állapotmintákat, kattintson a Hozzáadás gombra
         1. Adja meg az új állapotadat-mintavétel nevét (például **a hp ascs nw1**)
         1. Válassza ki a TCP protokoll, port 620**00**, időköz 5 és a nem kifogástalan állapot küszöbértéke 2
         1. Kattintson az OK gombra
      1. Port 621**02** ASCS SSZON számára
         * Ismételje meg a fenti lépéseket, és hozzon létre egy állapotmintát az on-felhasználók számára (például 621**02** és **a hp aers nw1**)
   1. Terheléselosztás szabályok
      1. 32**00** TCP ASCS használata
         1. Nyissa meg a terheléselosztó, terheléselosztási szabályok kiválasztása, és kattintson a Hozzáadás gombra
         1. Adja meg az új terheléselosztó-szabályt nevét (például **nw1-lb-3200**)
         1. Válassza ki az előtérbeli IP-cím, a háttérkészlet és a korábban létrehozott állapotadat-mintavétel (például **nw1 – ascs-frontend**)
         1. Tartsa protokoll **TCP**, adja meg a port **3200**
         1. Üresjárati időkorlát akár 30 percig növelése
         1. **Ügyeljen arra, hogy Floating IP engedélyezése**
         1. Kattintson az OK gombra
      1. Az ASCS további portokat
         * Ismételje meg a fenti lépéseket a portokhoz 36**00**, 39**00**, 81**00**, 5**00**13, 5**00**14., 5**00**16 és a TCP az ASCS használata
      1. Az ASCS SSZON további portokat
         * Ismételje meg a fenti lépéseket a portokhoz 33**02**, 5**02**13, 5**02**14, 5**02**16 és a TCP az ASCS SSZON használata

### <a name="create-pacemaker-cluster"></a>Támasztja fürt létrehozása

Kövesse a [támasztja a Red Hat Enterprise Linux az Azure-beli beállítása](high-availability-guide-rhel-pacemaker.md) hozzon létre egy alapszintű támasztja fürtről ehhez (A) SCS-kiszolgálón.

### <a name="prepare-for-sap-netweaver-installation"></a>SAP NetWeaver-telepítés előkészítése

A következő elemek van fűzve előtagként vagy **[A]** – az összes csomópont alkalmazandó **[1]** – 1. csomópont csak érvényes vagy **: [2]** – 2. csomópont csak érvényes.

1. **[A]**  Állomásnév-feloldás beállítása

   DNS-kiszolgálót használjon, vagy módosítsa a Hosts az összes csomópontra. Ez a példa bemutatja, hogyan használhatja a Hosts fájlt.
   Cserélje le az IP-cím és a következő parancsokat az állomásnevet

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Helyezze be a következő sorokat Hosts. Módosítsa az IP-cím és a környezet megfelelő állomásnév

   <pre><code># IP addresses of the GlusterFS nodes
   <b>10.0.0.40 glust-0</b>
   <b>10.0.0.41 glust-1</b>
   <b>10.0.0.42 glust-2</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.11 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.12 nw1-aers</b>
   </code></pre>

1. **[A]**  a közös könyvtárak létrehozása

   <pre><code>sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/<b>NW1</b>/SYS
   sudo mkdir -p /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo mkdir -p /usr/sap/<b>NW1</b>/ERS<b>02</b>

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/<b>NW1</b>/SYS
   sudo chattr +i /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo chattr +i /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>

1. **[A]**  Telepítése GlusterFS ügyfél- és egyéb követelmények

   <pre><code>sudo yum -y install glusterfs-fuse resource-agents resource-agents-sap
   </code></pre>

1. **[A]**  Erőforrás-ügynökök – sap verziójának ellenőrzése

   Győződjön meg arról, hogy a telepített erőforrás-ügynökök – sap-csomag verziója legalább 3.9.5-124.el7
   <pre><code>sudo yum info resource-agents-sap
   
   # Loaded plugins: langpacks, product-id, search-disabled-repos
   # Repodata is over 2 weeks old. Install yum-cron? Or run: yum makecache fast
   # Installed Packages
   # Name        : resource-agents-sap
   # Arch        : x86_64
   # Version     : <b>3.9.5</b>
   # Release     : <b>124.el7</b>
   # Size        : 100 k
   # Repo        : installed
   # From repo   : rhel-sap-for-rhel-7-server-rpms
   # Summary     : SAP cluster resource agents and connector script
   # URL         : https://github.com/ClusterLabs/resource-agents
   # License     : GPLv2+
   # Description : The SAP resource agents and connector script interface with
   #          : Pacemaker to allow SAP instances to be managed in a cluster
   #          : environment.
   </code></pre>


1. **[A]**  Csatlakoztatási bejegyzések hozzáadása

   <pre><code>sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   <b>glust-0</b>:/<b>NW1</b>-sapmnt /sapmnt/<b>NW1</b> glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   <b>glust-0</b>:/<b>NW1</b>-trans /usr/sap/trans glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   <b>glust-0</b>:/<b>NW1</b>-sys /usr/sap/<b>NW1</b>/SYS glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   </code></pre>

   Csatlakoztassa az új megosztások

   <pre><code>sudo mount -a
   </code></pre>

1. **[A]**  Fájl cseréje konfigurálása

   <pre><code>sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Indítsa újra az ügynököt, a módosítás aktiválása

   <pre><code>sudo service waagent restart
   </code></pre>

1. **[A]**  RHEL-konfiguráció

   Konfigurálja az RHEL, a SAP-Jegyzetnek leírtak szerint [2002167]

### <a name="installing-sap-netweaver-ascsers"></a>SAP NetWeaver ASCS/SSZON telepítése

1. **[1]**  Egy virtuális IP- és állapot-mintavételi ASCS-példány létrehozása

   <pre><code>sudo pcs node standby <b>nw1-cl-1</b>
   
   sudo pcs resource create fs_<b>NW1</b>_ASCS Filesystem device='<b>glust-0</b>:/<b>NW1</b>-ascs' \
     directory='/usr/sap/<b>NW1</b>/ASCS<b>00</b>' fstype='glusterfs' \
     options='backup-volfile-servers=<b>glust-1:glust-2</b>' \
     --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create vip_<b>NW1</b>_ASCS IPaddr2 \
     ip=<b>10.0.0.11</b> cidr_netmask=<b>24</b> \
     --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create nc_<b>NW1</b>_ASCS azure-lb port=620<b>00</b> \
     --group g-<b>NW1</b>_ASCS
   </code></pre>

   Győződjön meg arról, hogy a fürt állapota rendben, és elindulnak, hogy az összes erőforrás. Nem számít mely erőforrásokat futtató csomóponton.

   <pre><code>sudo pcs status
   
   # Node <b>nw1-cl-1</b>: standby
   # Online: [ <b>nw1-cl-0</b> ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started <b>nw1-cl-0</b>
   #  Resource Group: g-<b>NW1</b>_ASCS
   #      fs_<b>NW1</b>_ASCS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-0</b>
   #      nc_<b>NW1</b>_ASCS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-0</b>
   #      vip_<b>NW1</b>_ASCS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-0</b>
   </code></pre>

1. **[1]**  SAP NetWeaver ASCS telepítése  

   SAP NetWeaver ASCS telepítése egy virtuális állomásnevet, amely leképezi a terheléselosztó előtérbeli konfigurációját a ascs rendszerbe fut be, az IP-címe például használatával első csomópontjára legfelső szintű <b>nw1 – ascs</b>, <b>10.0.0.11</b> és a példányok száma, amelyek például használja a terheléselosztó a mintavétel <b>00</b>.

   A sapinst paraméter SAPINST_REMOTE_ACCESS_USER használhatja, hogy a nem gyökér felhasználó sapinst csatlakozni.

   <pre><code># Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   Ha a telepítés sikertelen lesz, hozzon létre egy almappát a usr/sap/**NW1**/ASCS**00**, próbálja meg a tulajdonos és a ASCS csoportja**00** mappát, majd próbálkozzon újra.

   <pre><code>sudo chown nw1adm /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo chgrp sapsys /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   </code></pre>

1. **[1]**  Egy virtuális IP- és állapot-mintavételi SSZON-példány létrehozása

   <pre><code>sudo pcs node unstandby <b>nw1-cl-1</b>
   sudo pcs node standby <b>nw1-cl-0</b>
   
   sudo pcs resource create fs_<b>NW1</b>_AERS Filesystem device='<b>glust-0</b>:/<b>NW1</b>-aers' \
     directory='/usr/sap/<b>NW1</b>/ERS<b>02</b>' fstype='glusterfs' \
     options='backup-volfile-servers=<b>glust-1:glust-2</b>' \
    --group g-<b>NW1</b>_AERS
   
   sudo pcs resource create vip_<b>NW1</b>_AERS IPaddr2 \
     ip=<b>10.0.0.12</b> cidr_netmask=<b>24</b> \
    --group g-<b>NW1</b>_AERS
   
   sudo pcs resource create nc_<b>NW1</b>_AERS azure-lb port=621<b>02</b> \
    --group g-<b>NW1</b>_AERS
   </code></pre>
 
   Győződjön meg arról, hogy a fürt állapota rendben, és elindulnak, hogy az összes erőforrás. Nem számít mely erőforrásokat futtató csomóponton.

   <pre><code>sudo pcs status
   
   # Node <b>nw1-cl-0</b>: standby
   # Online: [ <b>nw1-cl-1</b> ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started <b>nw1-cl-1</b>
   #  Resource Group: g-<b>NW1</b>_ASCS
   #      fs_<b>NW1</b>_ASCS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-1</b>
   #      nc_<b>NW1</b>_ASCS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-1</b>
   #      vip_<b>NW1</b>_ASCS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-1</b>
   #  Resource Group: g-<b>NW1</b>_AERS
   #      fs_<b>NW1</b>_AERS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-1</b>
   #      nc_<b>NW1</b>_AERS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-1</b>
   #      vip_<b>NW1</b>_AERS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-1</b>
   </code></pre>

1. **a(z) [2]**  SAP NetWeaver SSZON telepítése  

   SAP NetWeaver SSZON telepíti, a második csomópont használatával egy virtuális állomásnevet, amely leképezi a terheléselosztó előtérbeli konfigurációját a SSZON az IP-címét, például a legfelső szintű <b>nw1-aers</b>, <b>10.0.0.12</b> és a példányok száma, amelyek például használja a terheléselosztó a mintavétel <b>02</b>.

   A sapinst paraméter SAPINST_REMOTE_ACCESS_USER használhatja, hogy a nem gyökér felhasználó sapinst csatlakozni.

   <pre><code># Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp

   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   Ha a telepítés sikertelen lesz, hozzon létre egy almappát a usr/sap/**NW1**/ERS**02**, próbálja meg a tulajdonos és a SSZON csoportja**02** mappát, majd próbálkozzon újra.

   <pre><code>sudo chown nw1adm /usr/sap/<b>NW1</b>/ERS<b>02</b>
   sudo chgrp sapsys /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>

1. **[1]**  Adapt ASCS/SCS és SSZON példány profilok

   * ASCS/SCS-profil

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_<b>ASCS00</b>_<b>nw1-ascs</b>
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   </code></pre>

   * SSZON profil

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # remove Autostart from ERS profile
   # Autostart = 1
   </code></pre>


1. **[A]**  Életben tartási konfigurálása

   A SAP NetWeaver-kiszolgáló és az ASC/SCS közötti kommunikáció áthalad egy szoftveres terheléselosztót. A load balancer inaktív kapcsolatok leválasztása után konfigurálható időkorlát. Ennek megelőzéséhez szüksége egy paraméter az SAP NetWeaver ASCS/SCS-profilban és a Linux rendszer beállításait módosíthatja. Olvasási [SAP Megjegyzés 1410736] [ 1410736] további információt.

   Az ASCS/SCS profil paraméter célzó/encni/set_so_keepalive már lett adva az előző lépésben.

   <pre><code># Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   </code></pre>

1. **[A]**  a /usr/sap/sapservices fájl frissítése

   A példányok kezdetét az sapinit indítási parancsfájl megelőzése támasztja által kezelt összes példányát kell megjegyzésként /usr/sap/sapservices fájlból. Nem tegye megjegyzésbe az SAP HANA-példány Ha, HANA vezető együtt használható

   <pre><code>
   sudo vi /usr/sap/sapservices
   
   # On the node where you installed the ASCS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/<b>NW1</b>/ASCS<b>00</b>/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/<b>NW1</b>/ASCS<b>00</b>/exe/sapstartsrv pf=/usr/sap/<b>NW1</b>/SYS/profile/<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b> -D -u <b>nw1</b>adm
   
   # On the node where you installed the ERS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/<b>NW1</b>/ERS<b>02</b>/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/<b>NW1</b>/ERS<b>02</b>/exe/sapstartsrv pf=/usr/sap/<b>NW1</b>/ERS<b>02</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b> -D -u <b>nw1</b>adm
   </code></pre>

1. **[1]**  Az SAP-fürt erőforrásainak létrehozása

   <pre><code>sudo pcs property set maintenance-mode=true
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ASCS00 SAPInstance \
    InstanceName=<b>NW1</b>_ASCS00_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS00_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 \
    --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    InstanceName=<b>NW1</b>_ERS02_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS02_<b>nw1-aers</b>" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    --group g-<b>NW1</b>_AERS
      
   sudo pcs constraint colocation add g-<b>NW1</b>_AERS with g-<b>NW1</b>_ASCS -5000
   sudo pcs constraint location rsc_sap_<b>NW1</b>_ASCS<b>00</b> rule score=2000 runs_ers_<b>NW1</b> eq 1
   
   sudo pcs constraint order g-<b>NW1</b>_ASCS then g-<b>NW1</b>_AERS kind=Optional symmetrical=false
   
   sudo pcs node unstandby <b>nw1-cl-0</b>
   sudo pcs property set maintenance-mode=false
   </code></pre>

   Győződjön meg arról, hogy a fürt állapota rendben, és elindulnak, hogy az összes erőforrás. Nem számít mely erőforrásokat futtató csomóponton.

   <pre><code>sudo pcs status
   
   # Online: [ <b>nw1-cl-0</b> <b>nw1-cl-1</b> ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started <b>nw1-cl-0</b>
   #  Resource Group: g-<b>NW1</b>_ASCS
   #      fs_<b>NW1</b>_ASCS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-1</b>
   #      nc_<b>NW1</b>_ASCS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-1</b>
   #      vip_<b>NW1</b>_ASCS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-1</b>
   #      rsc_sap_<b>NW1</b>_ASCS00 (ocf::heartbeat:SAPInstance):   Started <b>nw1-cl-1</b>
   #  Resource Group: g-<b>NW1</b>_AERS
   #      fs_<b>NW1</b>_AERS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-0</b>
   #      nc_<b>NW1</b>_AERS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-0</b>
   #      vip_<b>NW1</b>_AERS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-0</b>
   #      rsc_sap_<b>NW1</b>_ERS02  (ocf::heartbeat:SAPInstance):   Started <b>nw1-cl-0</b>
   </code></pre>

1. **[A]**  ASCS és SSZON mindkét csomópontjának Tűzfalszabályok hozzáadása

   <pre><code># Probe Port of ASCS
   sudo firewall-cmd --zone=public --add-port=620<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=620<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=32<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=32<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=36<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=36<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=39<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=39<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=81<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=81<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>13/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>13/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>14/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>14/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>16/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>16/tcp
   # Probe Port of ERS
   sudo firewall-cmd --zone=public --add-port=621<b>02</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=621<b>02</b>/tcp
   sudo firewall-cmd --zone=public --add-port=33<b>02</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=33<b>02</b>/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>13/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>13/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>14/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>14/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>16/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>16/tcp
   </code></pre>

## <a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>SAP NetWeaver alkalmazás kiszolgáló előkészítése

Egyes adatbázisokhoz szükséges, hogy az adatbázis-példány telepítése alkalmazáskiszolgáló hajtja végre. Készítse elő az alkalmazás server virtuális gépek ezekben az esetekben használhatja őket.

A lépések Csengő azt feltételezik, hogy a kiszolgáló egy kiszolgálón telepíti a ASCS/SCS- és HANA-kiszolgálókról különböző. Ellenkező esetben néhány (pl. állomásnév-feloldás konfigurálása) az alábbi lépéseket nem szükséges.

1. Állomásnév-feloldás beállítása

   DNS-kiszolgálót használjon, vagy módosítsa a Hosts az összes csomópontra. Ez a példa bemutatja, hogyan használhatja a Hosts fájlt.
   Cserélje le az IP-cím és a következő parancsokat az állomásnevet

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Helyezze be a következő sorokat Hosts. Módosítsa az IP-cím és a környezet megfelelő állomásnév

   <pre><code># IP addresses of the GlusterFS nodes
   <b>10.0.0.40 glust-0</b>
   <b>10.0.0.41 glust-1</b>
   <b>10.0.0.42 glust-2</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.11 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.12 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   # IP address of all application servers
   <b>10.0.0.8 nw1-di-0</b>
   <b>10.0.0.7 nw1-di-1</b>
   </code></pre>

1. A sapmnt könyvtár létrehozása

   <pre><code>sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. Telepítse a GlusterFS ügyfél- és egyéb követelmények

   <pre><code>sudo yum -y install glusterfs-fuse uuidd
   </code></pre>

1. Csatlakoztatási bejegyzés hozzáadása

   <pre><code>sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   <b>glust-0</b>:/<b>NW1</b>-sapmnt /sapmnt/<b>NW1</b> glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   <b>glust-0</b>:/<b>NW1</b>-trans /usr/sap/trans glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   </code></pre>

   Csatlakoztassa az új megosztások

   <pre><code>sudo mount -a
   </code></pre>

1. Lapozófájl konfigurálása
 
   <pre><code>
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Indítsa újra az ügynököt, a módosítás aktiválása

   <pre><code>
   sudo service waagent restart
   </code></pre>

## <a name="install-database"></a>Adatbázis telepítése

Ebben a példában SAP Netweavertől az SAP HANA telepítve van. Minden támogatott adatbázis használhatja a telepítéshez. Az Azure-beli SAP HANA telepítése További információkért lásd: [magas rendelkezésre állás az SAP HANA az Azure Virtual machines Red Hat Enterprise Linux][sap-hana-ha]. Támogatott adatbázisok listájának megtekintéséhez lásd: [SAP Megjegyzés 1928533][1928533].

1. Futtassa az SAP adatbázis-példány telepítése

   Gyökér szintű használatával egy virtuális állomásnevet, amely leképezi a terheléselosztó előtérbeli konfigurációját az adatbázis IP-címét, például az SAP NetWeaver-adatbázispéldány telepítése <b>nw1-db</b> és <b>10.0.0.13</b>.

   A sapinst paraméter SAPINST_REMOTE_ACCESS_USER használhatja, hogy a nem gyökér felhasználó sapinst csatlakozni.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>SAP NetWeaver alkalmazás kiszolgáló telepítése

Kövesse az alábbi lépéseket egy SAP-alkalmazáskiszolgáló telepítése.

1. Alkalmazás-kiszolgáló előkészítése

   Kövesse a lépéseket a fejezetben [SAP NetWeaver alkalmazás kiszolgáló előkészítése](high-availability-guide-rhel.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) fenti a kiszolgáló előkészítéséhez.

1. SAP NetWeaver-alkalmazáskiszolgáló telepítése

   Elsődleges vagy a további SAP NetWeaver alkalmazást kiszolgáló telepítése.

   A sapinst paraméter SAPINST_REMOTE_ACCESS_USER használhatja, hogy a nem gyökér felhasználó sapinst csatlakozni.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

1. SAP HANA a biztonságos tár frissítése

   Frissítse az SAP HANA biztonságos tároló, a SAP HANA-Rendszerreplikálást beállítása virtuális nevére mutasson.

   Futtassa a következő parancsot, a tételek felsorolása \<sapsid > adm

   <pre><code>hdbuserstore List
   </code></pre>

   Ez felsorolásban szerepelnie kell az összes bejegyzést, és kell kinéznie:
   <pre><code>
   DATA FILE       : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.DAT
   KEY FILE        : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.0.0.14:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: <b>NW1</b>
   </code></pre>

   A kimenet mutatja, hogy a virtuális gépet, és nem a terheléselosztó IP-cím mutat-e alapértelmezett bejegyzését IP-címét. Ez a bejegyzés kell módosítani, hogy a terheléselosztó virtuális állomásnevét mutasson. Győződjön meg arról, hogy ugyanazt a portot használja (**30313** a fenti kimenetben) és az adatbázis nevét (**HN1** a fenti kimenetben)!

   <pre><code>su - <b>nw1</b>adm
   hdbuserstore SET DEFAULT <b>nw1-db</b>:<b>30313@NW1</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="test-the-cluster-setup"></a>Tesztelje a fürt beállítása

1. Az ASCS-példány manuális áttelepítésével kapcsolatban

   Erőforrás állapotának a vizsgálat megkezdése előtt:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Futtassa az alábbi parancsokat rendszergazdaként az ASCS-példányát.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource move rsc_sap_NW1_ASCS00
   
   [root@nw1-cl-0 ~]# pcs resource clear rsc_sap_NW1_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   [root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Erőforrás állapotának a vizsgálat után:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Csomópont-összeomlás szimulálása

   Erőforrás állapotának a vizsgálat megkezdése előtt:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Az alábbi parancsot rendszergazdaként a csomóponton a ASCS-példányt futtató

   <pre><code>[root@nw1-cl-1 ~]# echo b > /proc/sysrq-trigger
   </code></pre>

   Az állapot, miután elindult a csomópontot újra kell kinéznie.

   <pre><code>Online: [ nw1-cl-0 nw1-cl-1 ]
   
   Full list of resources:
   
   rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   
   Failed Actions:
   * rsc_sap_NW1_ERS02_monitor_11000 on nw1-cl-0 'not running' (7): call=45, status=complete, exitreason='',
       last-rc-change='Tue Aug 21 13:52:39 2018', queued=0ms, exec=0ms
   </code></pre>

   A következő paranccsal tisztítsa meg a sikertelen erőforrásokat.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Erőforrás állapotának a vizsgálat után:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Üzenet kiszolgálói folyamat leállítása

   Erőforrás állapotának a vizsgálat megkezdése előtt:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Futtassa az alábbi parancsokat a folyamat az üzenet-kiszolgáló azonosításához, és állítsa le a legfelső szintű.

   <pre><code>[root@nw1-cl-0 ~]# pgrep ms.sapNW1 | xargs kill -9
   </code></pre>

   Ha Ön csak kill a üzenetkiszolgáló egyszer, által sapstart újraindul. Ha Ön kill Ez gyakran elegendő támasztja a rendszer végül helyezze át az ASCS-példány egy másik csomópontra. Futtassa az alábbi parancsokat rendszergazdaként a vizsgálat után az erőforrás állapotának ASCS és SSZON példány karbantartása.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ASCS00
   [root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Erőforrás állapotának a vizsgálat után:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Sorba kiszolgálói folyamat leállítása

   Erőforrás állapotának a vizsgálat megkezdése előtt:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Futtassa az alábbi parancsokat rendszergazdaként azon a csomóponton hol futnak az ASCS-példány a sorba kiszolgáló leállítása.

   <pre><code>[root@nw1-cl-1 ~]# pgrep en.sapNW1 | xargs kill -9
   </code></pre>

   Az ASCS példányt kell azonnal átadja a feladatokat egy másik csomópontra. A SSZON példányt is feladatátvételt az ASCS-példány indítása után. Futtassa az alábbi parancsokat rendszergazdaként a vizsgálat után az erőforrás állapotának ASCS és SSZON példány karbantartása.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ASCS00
   [root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Erőforrás állapotának a vizsgálat után:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Sorba replikációs kiszolgálói folyamat leállítása

   Erőforrás állapotának a vizsgálat megkezdése előtt:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Futtassa a következő parancsot rendszergazdaként azon a csomóponton a SSZON példányt futtató kiszolgáló a sorba replikációs kiszolgálói folyamat leállítása.

   <pre><code>[root@nw1-cl-1 ~]# pgrep er.sapNW1 | xargs kill -9
   </code></pre>

   Csak a parancs futtatása után, sapstart újraindítja a folyamatot. Kell futtatnia, ha gyakran elegendő sapstart lesz indítsa újra a folyamatot, és az erőforrás egy leállított állapotba kerül. Futtassa az alábbi parancsokat rendszergazdaként a vizsgálat után az erőforrás állapotának SSZON példány karbantartása.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Erőforrás állapotának a vizsgálat után:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Sorba sapstartsrv folyamat leállítása

   Erőforrás állapotának a vizsgálat megkezdése előtt:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Futtassa az alábbi parancsokat rendszergazdaként azon a csomóponton hol futnak az ascs rendszerbe fut be.

   <pre><code>[root@nw1-cl-0 ~]# pgrep -fl ASCS00.*sapstartsrv
   # 59545 sapstartsrv
   
   [root@nw1-cl-0 ~]# kill -9 59545
   </code></pre>

   A sapstartsrv folyamat mindig újra kell indítani az támasztja erőforrás ügynök figyelési részeként. Erőforrás állapotának a vizsgálat után:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

## <a name="next-steps"></a>További lépések

* [Az Azure virtuális gépek tervezése és megvalósítása SAP][planning-guide]
* [Az SAP az Azure virtuális gépek üzembe helyezése][deployment-guide]
* [Az SAP az Azure Virtual Machines DBMS üzembe helyezése][dbms-guide]
* Magas rendelkezésre állást és az Azure-ban (nagyméretű példányok) SAP Hana vész-helyreállítási terv létrehozásához, lásd: [SAP HANA (nagyméretű példányok) magas rendelkezésre állás és vészhelyreállítás recovery az Azure-ban](hana-overview-high-availability-disaster-recovery.md).
* Magas rendelkezésre állást és az Azure virtuális gépeken SAP Hana vész-helyreállítási terv létrehozásához, lásd: [magas rendelkezésre állás az SAP HANA Azure-beli virtuális gépeken (VM)][sap-hana-ha]
