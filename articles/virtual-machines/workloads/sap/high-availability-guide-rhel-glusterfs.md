---
title: GlusterFS az Azure-beli virtuális gépeken RHEL-en AZ SAP NetWeaver számára | Microsoft dokumentumok
description: Red Hat Enterprise Linuxon futó Azure-beli virtuális gépeken üzemelő GlusterFS SAP NetWeaverhez
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
ms.date: 08/16/2018
ms.author: radeltch
ms.openlocfilehash: 388a2db2c888be541d89c5f4274bd38b37e4ca28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77591914"
---
# <a name="glusterfs-on-azure-vms-on-red-hat-enterprise-linux-for-sap-netweaver"></a>Red Hat Enterprise Linuxon futó Azure-beli virtuális gépeken üzemelő GlusterFS SAP NetWeaverhez

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

Ez a cikk ismerteti, hogyan telepítheti a virtuális gépeket, konfigurálhatja a virtuális gépeket, és telepíthet egy GlusterFS-fürtöt, amely egy magas rendelkezésre állású SAP-rendszer megosztott adatainak tárolására használható.
Ez az útmutató bemutatja, hogyan lehet beállítani a GlusterFS-t, amelyet két SAP-rendszer, az NW1 és az NW2 használ. A példában szereplő erőforrások (például virtuális gépek, virtuális hálózatok) neve feltételezi, hogy az [SAP fájlkiszolgáló sablont][template-file-server] a **glust**erőforráselőtaggal használta.

Olvassa el először a következő SAP-megjegyzéseket és dokumentumokat

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
* [Az Azure virtuális gépek üzembe helyezése az SAP Linux on Linux (ez a cikk)][deployment-guide]
* [Az Azure virtual machines DBMS üzembe helyezése az SAP-hoz Linuxon][dbms-guide]
* [A Red Hat Gluster Storage termékdokumentációja](https://access.redhat.com/documentation/red_hat_gluster_storage/)
* Általános RHEL dokumentáció
  * [Magas rendelkezésre állású bővítmény – áttekintés](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Magas rendelkezésre állású bővítmény felügyelete](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Magas rendelkezésre állású bővítményhivatkozás](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
* Az Azure-specifikus RHEL dokumentáció:
  * [Az RHEL magas rendelkezésre állású fürtjeinek támogatási szabályzatai – Microsoft Azure virtuális gépek fürttagként](https://access.redhat.com/articles/3131341)
  * [Red Hat Enterprise Linux 7.4 (és újabb) magas rendelkezésre állású fürt telepítése és konfigurálása a Microsoft Azure-ban](https://access.redhat.com/articles/3252491)

## <a name="overview"></a>Áttekintés

A magas rendelkezésre állás eléréséhez az SAP NetWeaver megosztott tárolást igényel. A GlusterFS egy külön fürtben van konfigurálva, és több SAP-rendszer is használható.

![SAP NetWeaver magas rendelkezésre állás – áttekintés](./media/high-availability-guide-rhel-glusterfs/rhel-glusterfs.png)

## <a name="set-up-glusterfs"></a>A GlusterFS beállítása

Használhatja a Github Azure-sablonját az összes szükséges Azure-erőforrás üzembe helyezéséhez, beleértve a virtuális gépeket, a rendelkezésre állási készletet és a hálózati felületeket, vagy manuálisan is telepítheti az erőforrásokat.

### <a name="deploy-linux-via-azure-template"></a>Linux telepítése az Azure-sablonon keresztül

Az Azure Piactér tartalmaz egy lemezképet a Red Hat Enterprise Linux, amely segítségével új virtuális gépek üzembe helyezéséhez.
A githubon lévő rövid útmutató sablonok egyikével üzembe helyezheti az összes szükséges erőforrást. A sablon telepíti a virtuális gépeket, a rendelkezésre állási készletet stb. A sablon üzembe helyezéséhez kövesse az alábbi lépéseket:

1. Az [SAP-fájlkiszolgálósablon megnyitása][template-file-server] az Azure Portalon
1. Adja meg a következő paramétereket
   1. Erőforrás-előtag  
      Adja meg a használni kívánt előtagot. Az érték az üzembe helyezett erőforrások előtagjaként használatos.
   2. SAP-rendszerszám Adja meg a fájlkiszolgálót használó SAP-rendszerek számát. Ez telepíti a szükséges számú lemezt stb.
   3. Os típus  
      Válassza ki az egyik Linux disztribúciók. Ebben a példában válassza az RHEL 7
   4. Rendszergazdai felhasználónév, rendszergazdai jelszó vagy SSH-kulcs  
      Új felhasználó jön létre, amely a számítógépre való bejelentkezéshez használható.
   5. Alhálózati azonosító  
      Ha azt szeretné, hogy a virtuális gép egy meglévő virtuális hálózat, ahol a virtuális gép definiált alhálózat a virtuális gép kell hozzárendelni, nevezze el az adott alhálózat azonosítóját. Az azonosító általában úgy néz ki, mint a /subscription/**&lt;&gt;subscription ID**/resourceGroups/**&lt;erőforráscsoport neve&gt;**/providers/Microsoft.Network/virtualNetworks/**&lt;virtuális hálózat neve&gt;**/alhálózatok/**&lt;alhálózat neve&gt; **

### <a name="deploy-linux-manually-via-azure-portal"></a>Linux manuális üzembe helyezése az Azure Portalon keresztül

Először létre kell hoznia a fürt virtuális gépeit. Ezután hozzon létre egy terheléselosztót, és használja a háttérkészletekben lévő virtuális gépeket. Normál [terheléselosztót](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)ajánlunk.  

1. Erőforráscsoport létrehozása
1. Virtuális hálózat létrehozása
1. Rendelkezésre állási készlet létrehozása  
   Maximális frissítési tartomány beállítása
1. Virtuális gép létrehozása 1  
   Használja legalább RHEL 7, ebben a példában a Red Hat Enterprise Linux 7.4 kép<https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux74-ARM>  
   Korábbian létrehozott elérhetőségi készlet kiválasztása  
1. Virtuális gép létrehozása 2  
   Használja legalább RHEL 7, ebben a példában a Red Hat Enterprise Linux 7.4 kép<https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux74-ARM>  
   Korábbian létrehozott elérhetőségi készlet kiválasztása  
1. Adjon hozzá egy adatlemezt minden SAP-rendszerhez mindkét virtuális géphez.

### <a name="configure-glusterfs"></a>GlusterFS konfigurálása

A következő elemek előtaggal vannak ellátva **az [A]** - az összes csomópontra vonatkozóan,[1] - csak az 1-es csomópontra vonatkozik, **[1]** **[2]** - csak a 2-es csomópontra vonatkozik, **[3]** - csak a 3-as csomópontra vonatkozik.

1. **[A]** Állomásnév feloldása

   Használhatja a DNS-kiszolgálót, vagy módosíthatja az /etc/hosts kapcsolót az összes csomóponton. Ez a példa az /etc/hosts fájl használatát mutatja be.
   Cserélje le az IP-címet és az állomásnevet a következő parancsokban

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Szúrja be a következő sorokat az /etc/hosts könyvtárba. Az IP-cím és az állomásnév módosítása a környezetnek megfelelően

   <pre><code># IP addresses of the Gluster nodes
   <b>10.0.0.40 glust-0</b>
   <b>10.0.0.41 glust-1</b>
   <b>10.0.0.42 glust-2</b>
   </code></pre>

1. **[A]** Nyilvántartás

   Regisztrálja virtuális gépeit, és csatolja egy készlethez, amely rhel 7 és GlusterFS tárolóit tartalmazza

   <pre><code>sudo subscription-manager register
   sudo subscription-manager attach --pool=&lt;pool id&gt;
   </code></pre>

1. **[A]** GlusterFS-repók engedélyezése

   A szükséges csomagok telepítéséhez engedélyezze a következő adattárakat.

   <pre><code>sudo subscription-manager repos --disable "*"
   sudo subscription-manager repos --enable=rhel-7-server-rpms
   sudo subscription-manager repos --enable=rh-gluster-3-for-rhel-7-server-rpms
   </code></pre>
  
1. **[A]** GlusterFS csomagok telepítése

   Telepítse ezeket a csomagokat az összes GlusterFS csomópontra

   <pre><code>sudo yum -y install redhat-storage-server
   </code></pre>

   Indítsa újra a csomópontokat a telepítés után.

1. **[A]** Tűzfal módosítása

   Adja meg a tűzfalszabályokat, hogy az ügyfélforgalmat a GlusterFS-csomópontokhoz engedélyezze.

   <pre><code># list the available zones
   firewall-cmd --get-active-zones
   
   sudo firewall-cmd --zone=public --add-service=glusterfs --permanent
   sudo firewall-cmd --zone=public --add-service=glusterfs
   </code></pre>

1. **[A]** GlusterFS szolgáltatás engedélyezése és indítása

   Indítsa el a GlusterFS szolgáltatást az összes csomóponton.

   <pre><code>sudo systemctl start glusterd
   sudo systemctl enable glusterd
   </code></pre>

1. **[1]** GluserFS létrehozása

   A GlusterFS-fürt létrehozásához futtassa a következő parancsokat

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

1. **[2]** Társállapot ának tesztelése

   A társállapot tesztelése a második csomóponton

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

1. **[3]** Társállapot ának tesztelése

   A társállapot tesztelése a harmadik csomóponton

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

1. **[A]** LVM létrehozása

   Ebben a példában a GlusterFS két SAP-rendszerhez, az NW1-hez és az NW2-hez használatos. A következő parancsokkal lvm-konfigurációkat hozhat létre ezekhez az SAP-rendszerekhez.

   Használja ezeket a parancsokat az NW1-hez

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

   Használja ezeket a parancsokat az NW2-hez

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

1. **[1]** Az elosztott kötet létrehozása

   Az alábbi parancsokkal hozza létre a GlusterFS kötetet az NW1-hez, és indítsa el.

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

   Az alábbi parancsokkal hozza létre a GlusterFS kötetet az NW2-hez, és indítsa el.

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

* [Az SAP ASCS és az adatbázis telepítése](high-availability-guide-rhel.md)
* [Az Azure virtuális gépek tervezése és megvalósítása az SAP-hoz][planning-guide]
* [Az Azure virtuális gépek üzembe helyezése az SAP-hoz][deployment-guide]
* [Az Azure virtual machines DBMS üzembe helyezése az SAP-hoz][dbms-guide]
* Ha meg szeretné tudni, hogyan hozhat létre magas rendelkezésre állást, és tervezze meg az SAP HANA vészutáni helyreállítását az Azure-ban (nagy példányok), tekintse meg az [SAP HANA (nagy példányok) magas rendelkezésre állású és vész-helyreállítási az Azure-ban.](hana-overview-high-availability-disaster-recovery.md)
* Ha meg szeretné tudni, hogyan hozhat létre magas rendelkezésre állást, és tervezze meg az SAP HANA vészutáni helyreállítását az Azure virtuális gépeken, olvassa el [az SAP HANA magas rendelkezésre állását az Azure virtuális gépeken (VM-ek) című témakört.][sap-hana-ha]
