---
title: GlusterFS az Azure-beli virtuális gépeken a Red Hat Enterprise Linux for SAP NetWeaver |} A Microsoft Docs
description: A Red Hat Enterprise Linux for SAP NetWeaver-alapú virtuális gépek Azure GlusterFS
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
ms.date: 08/16/2018
ms.author: sedusch
ms.openlocfilehash: bbb0bbc6ddbbabb37932edc6946394b020631231
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2018
ms.locfileid: "45637463"
---
# <a name="glusterfs-on-azure-vms-on-red-hat-enterprise-linux-for-sap-netweaver"></a>A Red Hat Enterprise Linux for SAP NetWeaver-alapú virtuális gépek Azure GlusterFS

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

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability-rhel.md

Ez a cikk ismerteti a virtuális gépek üzembe helyezése, a virtuális gépek konfigurálása és telepítése a magas rendelkezésre állású SAP-rendszer a megosztott adatok tárolására szolgáló GlusterFS fürt.
Ez az útmutató azt ismerteti, hogyan állítható be, amelyet két SAP-rendszereit, NW1 és NW2 GlusterFS. Az erőforrások (például virtuális gépek, virtuális hálózatok) a példában nevei azt feltételezik, hogy használja a [SAP file server sablon] [ template-file-server] erőforrás előtaggal **glust**.

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
* [Az Azure virtuális gépek üzembe helyezése, az SAP, Linux (Ez a cikk)][deployment-guide]
* [Az Azure Virtual Machines DBMS üzembe helyezése, az SAP, Linux rendszeren][dbms-guide]
* [Red Hat Gluster Storage termék dokumentációja](https://access.redhat.com/documentation/red_hat_gluster_storage/)
* Általános RHEL-dokumentáció
  * [Magas rendelkezésre állású bővítmény áttekintése](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Magas rendelkezésre állású kiegészítő felügyeleti](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Magas rendelkezésre állású bővítmény referencia](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
* Az Azure egyedi RHEL dokumentációja:
  * [RHEL magas rendelkezésre állású fürtöket – Microsoft Azure virtuális gépek a fürt tagjai terméktámogatási irányelveinek](https://access.redhat.com/articles/3131341)
  * [Telepítése és a Microsoft Azure egy Red Hat Enterprise Linux 7.4-es (és újabb verziók) magas rendelkezésre állású fürt konfigurálása](https://access.redhat.com/articles/3252491)

## <a name="overview"></a>Áttekintés

Magas rendelkezésre állás, az SAP NetWeaver megosztott tároló szükséges hozzá. GlusterFS külön fürtben van konfigurálva, és több SAP-rendszerek által használható.

![SAP NetWeaver magas rendelkezésre állás – Áttekintés](./media/high-availability-guide-rhel-glusterfs/rhel-glusterfs.png)

## <a name="set-up-glusterfs"></a>Állítsa be a GlusterFS

Vagy használhatja az Azure-sablont a githubból minden szükséges Azure-erőforrások üzembe helyezéséhez, például a virtuális gépek, a rendelkezésre állási csoport és a hálózati adaptereket, vagy telepítheti az erőforrásokat manuálisan.

### <a name="deploy-linux-via-azure-template"></a>Linux-n keresztül az Azure-sablon üzembe helyezése

Az Azure Marketplace-en kép Red Hat Enterprise Linux, amelyek segítségével telepíthet új virtuális gépeket tartalmazza.
Használhatja a gyorsindítási sablonok egyikét a githubon üzembe helyezéséhez szükséges összes erőforrást. A sablon üzembe helyezi a virtuális gépek rendelkezésre állási csoport stb. Kövesse az alábbi lépéseket a sablon üzembe helyezéséhez:

1. Nyissa meg a [SAP file server sablon] [ template-file-server] az Azure Portalon
1. Adja meg a következő paraméterek
   1. Erőforrás-előtag  
      Adja meg a használni kívánt előtagot. Az érték előtagjaként is szolgál az üzembe helyezett erőforrásokat.
   2. Az SAP-rendszer száma itt adhatja meg a fájlkiszolgáló által használt SAP-rendszerek számát. Ez telepíti a szükséges számú lemezeket stb.
   3. Operációs rendszer típusa  
      Válasszon ki egy Linux-disztribúció. Ebben a példában válassza ki az RHEL 7
   4. Rendszergazdai felhasználónév, a rendszergazdai jelszó vagy SSH-kulcs  
      Egy új felhasználót hoz létre, amely segítségével jelentkezzen be a számítógépen.
   5. Alhálózati azonosító  
      Ha azt szeretné, helyezheti üzembe a virtuális gép egy meglévő Vnetet, amelyekben egy meghatározott alhálózatot a virtuális gép hozzá kell rendelni, nevezze el a kívánt alhálózatot. Az azonosító általában néz ki: /subscriptions/**&lt;előfizetés-azonosító&gt;**/resourceGroups/**&lt;erőforráscsoport-név&gt;**/szolgáltatók/ Microsoft.Network/virtualNetworks/**&lt;virtuálishálózat-nevet&gt;**/subnets/**&lt;alhálózat neve&gt;**

### <a name="deploy-linux-manually-via-azure-portal"></a>Manuálisan üzembe helyezése Linux rendszerű Azure-portálon

Először a virtuális gépek a fürt létrehozásához. Ezt követően hozzon létre egy terheléselosztó, és a virtuális gépek használata a háttérkészletek.

1. Erőforráscsoport létrehozása
1. Virtuális hálózat létrehozása
1. Egy rendelkezésre állási csoport létrehozása  
   Maximális frissítési tartomány beállítása
1. 1 virtuális gép létrehozása  
   Legalább RHEL 7, ez például a Red Hat Enterprise Linux 7.4-lemezkép <https://ms.portal.azure.com/#create/RedHat.RedHatEnterpriseLinux74-ARM>  
   Válassza ki a korábban létrehozott rendelkezésre állási  
1. 2 virtuális gép létrehozása  
   Legalább RHEL 7, ez például a Red Hat Enterprise Linux 7.4-lemezkép <https://ms.portal.azure.com/#create/RedHat.RedHatEnterpriseLinux74-ARM>  
   Válassza ki a korábban létrehozott rendelkezésre állási  
1. Minden egyes SAP-rendszerhez egy adatlemez hozzá virtuális gépeket is.

### <a name="configure-glusterfs"></a>GlusterFS konfigurálása

A következő elemek van fűzve előtagként vagy **[A]** – az összes csomópont alkalmazandó **[1]** – csak érvényes 1,. csomópont **: [2]** – csak érvényes csomópont 2, **: [3].**  – 3 csomópont csak érvényes.

1. **[A]**  Állomásnév-feloldás beállítása

   DNS-kiszolgálót használjon, vagy módosítsa a Hosts az összes csomópontra. Ez a példa bemutatja, hogyan használhatja a Hosts fájlt.
   Cserélje le az IP-cím és a következő parancsokat az állomásnevet

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Helyezze be a következő sorokat Hosts. Módosítsa az IP-cím és a környezet megfelelő állomásnév

   <pre><code># IP addresses of the Gluster nodes
   <b>10.0.0.40 glust-0</b>
   <b>10.0.0.41 glust-1</b>
   <b>10.0.0.42 glust-2</b>
   </code></pre>

1. **[A]**  Regisztrálása

   Regisztrálja a virtuális gépek, és mellékelje egy készletet, amely tartalmazza a tárházak RHEL 7 és GlusterFS

   <pre><code>sudo subscription-manager register
   sudo subscription-manager attach --pool=&lt;pool id&gt;
   </code></pre>

1. **[A]**  Engedélyezése GlusterFS adattárakkal

   Annak érdekében, hogy a szükséges csomagok telepítéséhez a következő tárházak engedélyezése.

   <pre><code>sudo subscription-manager repos --disable "*"
   sudo subscription-manager repos --enable=rhel-7-server-rpms
   sudo subscription-manager repos --enable=rh-gluster-3-for-rhel-7-server-rpms
   </code></pre>
  
1. **[A]**  Telepítése GlusterFS csomagok

   Az ilyen csomagok telepítéséhez az összes GlusterFS csomóponton

   <pre><code>sudo yum -y install redhat-storage-server
   </code></pre>

   A telepítés után indítsa újra a csomópontokat.

1. **[A]**  Tűzfal módosítása

   Adja hozzá a tűzfalszabályokban GlusterFS csomópontjain ügyfél forgalmát.

   <pre><code># list the available zones
   firewall-cmd --get-active-zones
   
   sudo firewall-cmd --zone=public --add-service=glusterfs --permanent
   sudo firewall-cmd --zone=public --add-service=glusterfs
   </code></pre>

1. **[A]**  Engedélyezése és GlusterFS szolgáltatás indítása

   Az összes csomóponton, indítsa el a GlusterFS szolgáltatás.

   <pre><code>sudo systemctl start glusterd
   sudo systemctl enable glusterd
   </code></pre>

1. **[1]**  GluserFS létrehozása

   Futtassa az alábbi parancsokat a GlusterFS fürt létrehozása

   <pre><code>sudo gluster peer probe glust-1
   sudo gluster peer probe glust-2
   
   # Check gluster peer status
   sudo gluster peer status
   
   # Number of Peers: 2
   # 
   # Hostname: glust-1
   # Uuid: 10d43840-fee4-4120-bf5a-de9c393964cd
   # State: Accepted peer request (Connected)
   # 
   # Hostname: glust-2
   # Uuid: 9e340385-12fe-495e-ab0f-4f851b588cba
   # State: Accepted peer request (Connected)
   </code></pre>

1. **a(z) [2]**  Teszt társ állapota

   A második csomópont társ állapota tesztelése

   <pre><code>sudo gluster peer status
   # Number of Peers: 2
   #
   # Hostname: glust-0
   # Uuid: 6bc6927b-7ee2-461b-ad04-da123124d6bd
   # State: Peer in Cluster (Connected)
   #
   # Hostname: glust-2
   # Uuid: 9e340385-12fe-495e-ab0f-4f851b588cba
   # State: Peer in Cluster (Connected)
   </code></pre>

1. **[3]**  Teszt társ állapota

   A társ állapotát, a harmadik csomóponton tesztelése

   <pre><code>sudo gluster peer status
   # Number of Peers: 2
   #
   # Hostname: glust-0
   # Uuid: 6bc6927b-7ee2-461b-ad04-da123124d6bd
   # State: Peer in Cluster (Connected)
   #
   # Hostname: glust-1
   # Uuid: 10d43840-fee4-4120-bf5a-de9c393964cd
   # State: Peer in Cluster (Connected)
   </code></pre>

1. **[A]**  LVM létrehozása

   Ebben a példában a GlusterFS két SAP-rendszereit, NW1 és NW2 szolgál. A következő parancsok segítségével hozza létre ezeket az SAP-rendszerek LVM-konfigurációit.

   Ezek a parancsok használata NW1

   <pre><code>sudo pvcreate --dataalignment 1024K /dev/disk/azure/scsi1/lun0
   sudo pvscan
   sudo vgcreate --physicalextentsize 256K rhgs-<b>NW1</b> /dev/disk/azure/scsi1/lun0
   sudo vgscan
   sudo lvcreate -l 50%FREE -n rhgs-<b>NW1</b>/sapmnt
   sudo lvcreate -l 20%FREE -n rhgs-<b>NW1</b>/trans
   sudo lvcreate -l 10%FREE -n rhgs-<b>NW1</b>/sys
   sudo lvcreate -l 50%FREE -n rhgs-<b>NW1</b>/ascs
   sudo lvcreate -l 100%FREE -n rhgs-<b>NW1</b>/aers
   sudo lvscan
   
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW1</b>/sapmnt
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW1</b>/trans
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW1</b>/sys
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW1</b>/ascs
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW1</b>/aers
   
   sudo mkdir -p /rhs/<b>NW1</b>/sapmnt
   sudo mkdir -p /rhs/<b>NW1</b>/trans
   sudo mkdir -p /rhs/<b>NW1</b>/sys
   sudo mkdir -p /rhs/<b>NW1</b>/ascs
   sudo mkdir -p /rhs/<b>NW1</b>/aers
   
   sudo chattr +i /rhs/<b>NW1</b>/sapmnt
   sudo chattr +i /rhs/<b>NW1</b>/trans
   sudo chattr +i /rhs/<b>NW1</b>/sys
   sudo chattr +i /rhs/<b>NW1</b>/ascs
   sudo chattr +i /rhs/<b>NW1</b>/aers

   echo -e "/dev/rhgs-<b>NW1</b>/sapmnt\t/rhs/<b>NW1</b>/sapmnt\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW1</b>/trans\t/rhs/<b>NW1</b>/trans\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW1</b>/sys\t/rhs/<b>NW1</b>/sys\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW1</b>/ascs\t/rhs/<b>NW1</b>/ascs\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW1</b>/aers\t/rhs/<b>NW1</b>/aers\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   sudo mount -a
   </code></pre>

   Ezek a parancsok használata NW2

   <pre><code>sudo pvcreate --dataalignment 1024K /dev/disk/azure/scsi1/lun1
   sudo pvscan
   sudo vgcreate --physicalextentsize 256K rhgs-<b>NW2</b> /dev/disk/azure/scsi1/lun1
   sudo vgscan
   sudo lvcreate -l 50%FREE -n rhgs-<b>NW2</b>/sapmnt
   sudo lvcreate -l 20%FREE -n rhgs-<b>NW2</b>/trans
   sudo lvcreate -l 10%FREE -n rhgs-<b>NW2</b>/sys
   sudo lvcreate -l 50%FREE -n rhgs-<b>NW2</b>/ascs
   sudo lvcreate -l 100%FREE -n rhgs-<b>NW2</b>/aers
   
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW2</b>/sapmnt
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW2</b>/trans
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW2</b>/sys
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW2</b>/ascs
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW2</b>/aers
   
   sudo mkdir -p /rhs/<b>NW2</b>/sapmnt
   sudo mkdir -p /rhs/<b>NW2</b>/trans
   sudo mkdir -p /rhs/<b>NW2</b>/sys
   sudo mkdir -p /rhs/<b>NW2</b>/ascs
   sudo mkdir -p /rhs/<b>NW2</b>/aers
   
   sudo chattr +i /rhs/<b>NW2</b>/sapmnt
   sudo chattr +i /rhs/<b>NW2</b>/trans
   sudo chattr +i /rhs/<b>NW2</b>/sys
   sudo chattr +i /rhs/<b>NW2</b>/ascs
   sudo chattr +i /rhs/<b>NW2</b>/aers
   sudo lvscan
   
   echo -e "/dev/rhgs-<b>NW2</b>/sapmnt\t/rhs/<b>NW2</b>/sapmnt\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW2</b>/trans\t/rhs/<b>NW2</b>/trans\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW2</b>/sys\t/rhs/<b>NW2</b>/sys\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW2</b>/ascs\t/rhs/<b>NW2</b>/ascs\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW2</b>/aers\t/rhs/<b>NW2</b>/aers\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   sudo mount -a
   </code></pre>

1. **[1]**  Az elosztott kötet létrehozása

   Hozzon létre a GlusterFS kötet NW1, és indítsa el a következő parancsok használatával.

   <pre><code>sudo gluster vol create <b>NW1</b>-sapmnt replica 3 glust-0:/rhs/<b>NW1</b>/sapmnt glust-1:/rhs/<b>NW1</b>/sapmnt glust-2:/rhs/<b>NW1</b>/sapmnt force
   sudo gluster vol create <b>NW1</b>-trans replica 3 glust-0:/rhs/<b>NW1</b>/trans glust-1:/rhs/<b>NW1</b>/trans glust-2:/rhs/<b>NW1</b>/trans force
   sudo gluster vol create <b>NW1</b>-sys replica 3 glust-0:/rhs/<b>NW1</b>/sys glust-1:/rhs/<b>NW1</b>/sys glust-2:/rhs/<b>NW1</b>/sys force
   sudo gluster vol create <b>NW1</b>-ascs replica 3 glust-0:/rhs/<b>NW1</b>/ascs glust-1:/rhs/<b>NW1</b>/ascs glust-2:/rhs/<b>NW1</b>/ascs force
   sudo gluster vol create <b>NW1</b>-aers replica 3 glust-0:/rhs/<b>NW1</b>/aers glust-1:/rhs/<b>NW1</b>/aers glust-2:/rhs/<b>NW1</b>/aers force
   
   sudo gluster volume start <b>NW1</b>-sapmnt
   sudo gluster volume start <b>NW1</b>-trans
   sudo gluster volume start <b>NW1</b>-sys
   sudo gluster volume start <b>NW1</b>-ascs
   sudo gluster volume start <b>NW1</b>-aers
   </code></pre>

   Hozzon létre a GlusterFS kötet NW2, és indítsa el a következő parancsok használatával.

   <pre><code>sudo gluster vol create <b>NW2</b>-sapmnt replica 3 glust-0:/rhs/<b>NW2</b>/sapmnt glust-1:/rhs/<b>NW2</b>/sapmnt glust-2:/rhs/<b>NW2</b>/sapmnt force
   sudo gluster vol create <b>NW2</b>-trans replica 3 glust-0:/rhs/<b>NW2</b>/trans glust-1:/rhs/<b>NW2</b>/trans glust-2:/rhs/<b>NW2</b>/trans force
   sudo gluster vol create <b>NW2</b>-sys replica 3 glust-0:/rhs/<b>NW2</b>/sys glust-1:/rhs/<b>NW2</b>/sys glust-2:/rhs/<b>NW2</b>/sys force
   sudo gluster vol create <b>NW2</b>-ascs replica 3 glust-0:/rhs/<b>NW2</b>/ascs glust-1:/rhs/<b>NW2</b>/ascs glust-2:/rhs/<b>NW2</b>/ascs force
   sudo gluster vol create <b>NW2</b>-aers replica 3 glust-0:/rhs/<b>NW2</b>/aers glust-1:/rhs/<b>NW2</b>/aers glust-2:/rhs/<b>NW2</b>/aers force
   
   sudo gluster volume start <b>NW2</b>-sapmnt
   sudo gluster volume start <b>NW2</b>-trans
   sudo gluster volume start <b>NW2</b>-sys
   sudo gluster volume start <b>NW2</b>-ascs
   sudo gluster volume start <b>NW2</b>-aers
   </code></pre>

## <a name="next-steps"></a>További lépések

* [Az SAP ASCS és adatbázisának telepítése](high-availability-guide-rhel.md)
* [Az Azure virtuális gépek tervezése és megvalósítása SAP][planning-guide]
* [Az SAP az Azure virtuális gépek üzembe helyezése][deployment-guide]
* [Az SAP az Azure Virtual Machines DBMS üzembe helyezése][dbms-guide]
* Magas rendelkezésre állást és az Azure-ban (nagyméretű példányok) SAP Hana vész-helyreállítási terv létrehozásához, lásd: [SAP HANA (nagyméretű példányok) magas rendelkezésre állás és vészhelyreállítás recovery az Azure-ban](hana-overview-high-availability-disaster-recovery.md).
* Magas rendelkezésre állást és az Azure virtuális gépeken SAP Hana vész-helyreállítási terv létrehozásához, lásd: [magas rendelkezésre állás az SAP HANA Azure-beli virtuális gépeken (VM)][sap-hana-ha]
