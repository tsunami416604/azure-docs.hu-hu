---
title: A SUSE Linux Enterprise Server az Azure-ban támasztja beállítása |} A Microsoft Docs
description: A SUSE Linux Enterprise Server az Azure-ban támasztja beállítása
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/13/2018
ms.author: sedusch
ms.openlocfilehash: 9ce95bcf15d0186c1baea3df407d0fc0c4200f45
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39115476"
---
# <a name="setting-up-pacemaker-on-suse-linux-enterprise-server-in-azure"></a>A SUSE Linux Enterprise Server az Azure-ban támasztja beállítása

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[virtual-machines-linux-maintenance]:../../linux/maintenance-and-updates.md#memory-preserving-maintenance
[virtual-machines-windows-maintenance]:../../windows/maintenance-and-updates.md#memory-preserving-maintenance

Az Azure-ban támasztja fürt létrehozása két módon lehet. Használhat egy szintaxiskiemeléshez ügynököt, amely gondoskodik az Azure API-kon keresztül sikertelen csomópont újraindítása, vagy egy SBD eszközt is használhat.

A SBD eszköz, amely az iSCSI-tárolókiszolgáló funkcionál, és a egy SBD eszközt, egy további virtuális gép igényel. Az iSCSI-tárolókiszolgáló azonban lehetnek más támasztja fürtökkel osztva. Azt az előnyt SBD eszközt használ, gyorsabb feladatátvételt, valamint SBD a helyszíni eszközök, használatakor nem szükséges a támasztja fürt működési módját a módosításokat. A SBD szintaxiskiemeléshez továbbra is használhatja az Azure időkorlát ügynök elkerítjük mechanizmust, abban az esetben, ha az iSCSI-tárolókiszolgáló nem áll rendelkezésre biztonsági mentéséhez.

Ha nem szeretné, hogy egy további virtuális gép be, használhatja az Azure időkorlát ügynök. A hátránya az, hogy a feladatátvétel eltarthat 10 – 15 perc között, ha egy erőforrás leállítása sikertelen, vagy a fürt csomópontjai nem tud kommunikálni amely egymáshoz többé.

![Támasztja a SLES áttekintése](./media/high-availability-guide-suse-pacemaker/pacemaker.png)

>[!IMPORTANT]
> Tervezési és üzembe helyezése Linux támasztja fürtözött csomópontok és SBD eszközöket, amikor ez elengedhetetlen az általános megbízhatóságot nyújt, amely a virtuális gépek közötti útválasztást az érintett, és nem továbbítja a SBD őket üzemeltető virtuális gép teljes fürtkonfiguráció bármely más eszközök, például [nva-k](https://azure.microsoft.com/solutions/network-appliances/). Ellenkező esetben problémákat és az nva-t a karbantartási események negatív hatással lehet a stabilitás és megbízhatóság teljes fürtkonfiguráció. Annak érdekében, hogy ezeket az akadályokat, nem határoznak meg Nva-útválasztási szabályok vagy [felhasználói meghatározott útválasztási szabályok](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) a fürtözött csomópontok és SBD eszközöknek az nva-k és hasonló eszközök tervezésekor és üzembe helyezése Linux közötti forgalom irányítása Fürtözött támasztja csomópontok és SBD eszközök. 
>


## <a name="sbd-fencing"></a>SBD szintaxiskiemeléshez

Ha szeretne egy SBD eszköz használata az elkerítés, kövesse az alábbi lépéseket.

### <a name="set-up-an-iscsi-target-server"></a>ISCSI-tárolókiszolgáló beállítása

Először hozzon létre egy iSCSI cél virtuális gépen, ha Ön nem rendelkezik ilyennel. iSCSI-célkiszolgálókhoz megoszthatók több támasztja fürtökkel.

1. Egy új SLES 12 SP1 vagy újabb virtuális gép üzembe helyezése és kapcsolódjon a gép ssh. Nem kell a gép nagy. A virtuális gép méretét, Standard_E2s_v3 vagy Standard_D2s_v3 nem elegendő.

1. SLES frissítése

   <pre><code>
   sudo zypper update
   </code></pre>

1. Csomagok eltávolítása

   SLES 12 SP3 és targetcli egy ismert probléma elkerülése érdekében távolítsa el a következő csomagok. Hibák, amelyek nem találhatók csomagok figyelmen kívül hagyhatja
   
   <pre><code>
   sudo zypper remove lio-utils python-rtslib python-configshell targetcli
   </code></pre>
   
1. ISCSI cél csomagok telepítése

   <pre><code>
   sudo zypper install targetcli-fb dbus-1-python
   </code></pre>

1. Az iSCSI-tároló szolgáltatás engedélyezése

   <pre><code>   
   sudo systemctl enable targetcli
   sudo systemctl start targetcli
   </code></pre>

### <a name="create-iscsi-device-on-iscsi-target-server"></a>Az iSCSI-tárolókiszolgáló iSCSI-eszköz létrehozása

Új adatlemez csatolása az iSCSI cél virtuális gépen, amely használható a fürt számára. Az adatlemez lehet kisebb 1 GB-os, és kell helyezni a Premium Storage-fiók vagy a prémium szintű felügyelt lemez számára, hogy a [egyetlen virtuális gép SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines).

Futtassa a következő parancsot a **iSCSI cél virtuális gép** létre iSCSI lemezt, az új fürt létrehozásához. A következő példában **cl1** azonosítja az új fürthöz és **éles-cl1-0** és **éles-cl1-1** fürtcsomópont a gazdanevek vannak. Cserélje le őket a fürtcsomópontok állomásnevét.

<pre><code>
# List all data disks with the following command
sudo ls -al /dev/disk/azure/scsi1/

# total 0
# drwxr-xr-x 2 root root  80 Mar 26 14:42 .
# drwxr-xr-x 3 root root 160 Mar 26 14:42 ..
# lrwxrwxrwx 1 root root  12 Mar 26 14:42 lun0 -> ../../../<b>sdc</b>
# lrwxrwxrwx 1 root root  12 Mar 26 14:42 lun1 -> ../../../sdd

# Then use the disk name to list the disk id
sudo ls -l /dev/disk/by-id/scsi-* | grep sdc

# lrwxrwxrwx 1 root root  9 Mar 26 14:42 /dev/disk/by-id/scsi-14d53465420202020a50923c92babda40974bef49ae8828f0 -> ../../sdc
# lrwxrwxrwx 1 root root  9 Mar 26 14:42 <b>/dev/disk/by-id/scsi-360022480a50923c92babef49ae8828f0 -> ../../sdc</b>

# Use the data disk that you attached for this cluster to create a new backstore
sudo targetcli backstores/block create <b>cl1</b> <b>/dev/disk/by-id/scsi-360022480a50923c92babef49ae8828f0</b>

sudo targetcli iscsi/ create iqn.2006-04.<b>cl1</b>.local:<b>cl1</b>
sudo targetcli iscsi/iqn.2006-04.<b>cl1</b>.local:<b>cl1</b>/tpg1/luns/ create /backstores/block/<b>cl1</b>
sudo targetcli iscsi/iqn.2006-04.<b>cl1</b>.local:<b>cl1</b>/tpg1/acls/ create iqn.2006-04.<b>prod-cl1-0.local:prod-cl1-0</b>
sudo targetcli iscsi/iqn.2006-04.<b>cl1</b>.local:<b>cl1</b>/tpg1/acls/ create iqn.2006-04.<b>prod-cl1-1.local:prod-cl1-1</b>

# save the targetcli changes
sudo targetcli saveconfig
</code></pre>

### <a name="set-up-sbd-device"></a>SBD eszköz beállítása

Az iSCSI-eszközt a fürtből az előző lépésben létrehozott csatlakozni.
Futtassa az alábbi parancsokat a létrehozni kívánt új fürt csomópontjain.
A következő elemek van fűzve előtagként vagy **[A]** – az összes csomópont alkalmazandó **[1]** – 1. csomópont csak érvényes vagy **: [2]** – 2. csomópont csak érvényes.

1. **[A]**  Csatlakozhat az iSCSI-eszközök

   Először engedélyezze az iSCSI és SBD szolgáltatásokat.

   <pre><code>
   sudo systemctl enable iscsid
   sudo systemctl enable iscsi
   sudo systemctl enable sbd
   </code></pre>

1. **[1]**  Első csomópontjára kezdeményező nevének módosítása

   <pre><code>
   sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   A megfelelő ACL-ek az iSCSI-tárolókiszolgáló az iSCSI-eszközt létrehozásakor használt a fájl tartalmának módosítása

   <pre><code>   
   InitiatorName=<b>iqn.2006-04.prod-cl1-0.local:prod-cl1-0</b>
   </code></pre>

1. **a(z) [2]**  Kezdeményező a második csomópont nevének módosítása

   <pre><code>
   sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   A megfelelő ACL-ek az iSCSI-tárolókiszolgáló az iSCSI-eszközt létrehozásakor használt a fájl tartalmának módosítása

   <pre><code>
   InitiatorName=<b>iqn.2006-04.prod-cl1-1.local:prod-cl1-1</b>
   </code></pre>

1. **[A]**  Az iSCSI-szolgáltatás újraindítása

   Most indítsa újra az iSCSI szolgáltatást, a módosítás alkalmazása
   
   <pre><code>
   sudo systemctl restart iscsid
   sudo systemctl restart iscsi
   </code></pre>

   Az iSCSI-eszközök csatlakoztatása. Az alábbi példában 10.0.0.17 az iSCSI-tárolókiszolgáló IP-címe pedig 3260-as az alapértelmezett portot. <b>IQN.2006-04.cl1.local:cl1</b> a cél neve, amely szerepel a listán az első parancs futtatásakor.

   <pre><code>
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.17:3260</b>
   
   sudo iscsiadm -m node -T <b>iqn.2006-04.cl1.local:cl1</b> --login --portal=<b>10.0.0.17:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.17:3260</b> --op=update --name=node.startup --value=automatic
   </code></pre>

   Győződjön meg arról, hogy elérhető legyen-e az iSCSI-eszközt, és jegyezze fel az eszköz neve (a következő példa/dev/sde) kész

   <pre><code>
   lsscsi
   
   # [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
   # [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
   # [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
   # [5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd
   # <b>[6:0:0:0]    disk    LIO-ORG  cl1              4.0   /dev/sde</b>
   </code></pre>

   Az eszköz azonosítója, az iSCSI, lekéréséhez.

   <pre><code>
   ls -l /dev/disk/by-id/scsi-* | grep <b>sde</b>
   
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-1LIO-ORG_cl1:3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   # <b>lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df -> ../../sde</b>
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-SLIO-ORG_cl1_3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   </code></pre>

   A parancs három eszközazonosítókat listája. Javasoljuk, hogy kezdetű scsi-3, ez a fenti példában az azonosító használata
   
   **/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df**

1. **[1]**  A SBD eszköz létrehozása

   Az eszköz azonosítója, az iSCSI használatával hozzon létre egy új SBD eszköz az első fürtcsomópontra.

   <pre><code>
   sudo sbd -d <b>/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df</b> -1 10 -4 20 create
   </code></pre>

1. **[A]**  Alkalmazkodnak a SBD config

   Nyissa meg a SBD konfigurációs fájlt

   <pre><code>
   sudo vi /etc/sysconfig/sbd
   </code></pre>

   A tulajdonság az SBD eszköz módosítása, támasztja integrációjának engedélyezése és SBD indítási módját módosítani.

   <pre><code>
   [...]
   <b>SBD_DEVICE="/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df"</b>
   [...]
   <b>SBD_PACEMAKER="yes"</b>
   [...]
   <b>SBD_STARTMODE="always"</b>
   </code></pre>

   A softdog konfigurációs fájl létrehozása

   <pre><code>
   echo softdog | sudo tee /etc/modules-load.d/softdog.conf
   </code></pre>

   Töltse be a modul

   <pre><code>
   sudo modprobe -v softdog
   </code></pre>

## <a name="cluster-installation"></a>Fürt telepítése

A következő elemek van fűzve előtagként vagy **[A]** – az összes csomópont alkalmazandó **[1]** – 1. csomópont csak érvényes vagy **: [2]** – 2. csomópont csak érvényes.

1. **[A]**  SLES frissítése

   <pre><code>
   sudo zypper update
   </code></pre>

1. **[1]**  Ssh hozzáférés engedélyezése

   <pre><code>
   sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

2. **a(z) [2]**  Ssh hozzáférés engedélyezése

   <pre><code>
   sudo ssh-keygen
   
   # insert the public key you copied in the last step into the authorized keys file on the second server
   sudo vi /root/.ssh/authorized_keys
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key   
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[1]**  Ssh hozzáférés engedélyezése

   <pre><code>
   # insert the public key you copied in the last step into the authorized keys file on the first server
   sudo vi /root/.ssh/authorized_keys
   </code></pre>

1. **[A]**  Ügynökök telepítése időkorlátja
   
   <pre><code>
   sudo zypper install fence-agents
   </code></pre>

1. **[A]**  Állomásnév-feloldás beállítása   

   DNS-kiszolgálót használjon, vagy módosítsa a Hosts az összes csomópontra. Ez a példa bemutatja, hogyan használhatja a Hosts fájlt.
   Cserélje le az IP-cím és az állomásnevet, az alábbi parancsokban. A Hosts használatával előnye, hogy a fürt válik független a DNS, amely túl lehet egyetlen pont, a hibák.

   <pre><code>
   sudo vi /etc/hosts
   </code></pre>
   
   Helyezze be a következő sorokat Hosts. Módosítsa az IP-cím és a környezet megfelelő állomásnév   
   
   <pre><code>
   # IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[1]**  Fürt telepítése
   
   <pre><code>
   sudo ha-cluster-init
   
   # Do you want to continue anyway? [y/N] -> y
   # Network address to bind to (for example: 192.168.1.0) [10.79.227.0] -> Press ENTER
   # Multicast address (for example: 239.x.x.x) [239.174.218.125] -> Press ENTER
   # Multicast port [5405] -> Press ENTER
   # Do you wish to configure an administration IP? [y/N] -> N
   </code></pre>

1. **a(z) [2]**  Csomópont hozzáadása a fürthöz
   
   <pre><code> 
   sudo ha-cluster-join
   
   # Do you want to continue anyway? [y/N] -> y
   # IP address or hostname of existing node (for example: 192.168.1.1) [] -> IP address of node 1 for example 10.0.0.14
   # /root/.ssh/id_rsa already exists - overwrite? [y/N] N
   </code></pre>

1. **[A]**  Ugyanazt a jelszót hacluster jelszó módosítása

   <pre><code> 
   sudo passwd hacluster
   </code></pre>

1. **[A]**  Más átviteli használhatja, és adja hozzá a csomópontlista corosync konfigurálása. Fürt egyébként nem működik.
   
   <pre><code> 
   sudo vi /etc/corosync/corosync.conf   
   </code></pre>

   Adja hozzá a következő félkövér tartalmat a fájlhoz, ha az értékek nem ott vagy eltérő. Ellenőrizze, hogy módosítsa a jogkivonatot, hogy a karbantartás megőrzése memória 30000. Lásd: [Ez a cikk a Linux-] [ virtual-machines-linux-maintenance] vagy [Windows] [ virtual-machines-windows-maintenance] további részletekért.
   
   <pre><code> 
   [...]
     <b>token:          30000
     token_retransmits_before_loss_const: 10
     join:           60
     consensus:      6000
     max_messages:   20</b>
     
     interface { 
        [...] 
     }
     <b>transport:      udpu</b>
   } 
   <b>nodelist {
     node {
      # IP address of <b>prod-cl1-0</b>
      ring0_addr:10.0.0.6
     }
     node {
      # IP address of <b>prod-cl1-1</b>
      ring0_addr:10.0.0.7
     } 
   }</b>
   logging {
     [...]
   }
   quorum {
        # Enable and configure quorum subsystem (default: off)
        # see also corosync.conf.5 and votequorum.5
        provider: corosync_votequorum
        <b>expected_votes: 2</b>
        <b>two_node: 1</b>
   }
   </code></pre>

   Indítsa újra az corosync

   <pre><code>
   sudo service corosync restart
   </code></pre>

1. **[1]**  Támasztja alapértelmezett beállításainak módosítása

   <pre><code>
   sudo crm configure rsc_defaults resource-stickiness="1"   
   </code></pre>

## <a name="create-stonith-device"></a>STONITH eszköz létrehozása

A STONITH eszköz hitelesítéséhez, szemben a Microsoft Azure egy egyszerű szolgáltatást használja. Kövesse az alábbi lépéseket egy szolgáltatásnév létrehozásához.

1. Nyissa meg a következőt: <https://portal.azure.com>
1. Nyissa meg az Azure Active Directory panel  
   Lépjen a Tulajdonságok részhez, és jegyezze fel a címtár-azonosító. Ez a **bérlőazonosító**.
1. Kattintson az alkalmazásregisztrációk
1. Kattintson az Add (Hozzáadás) parancsra
1. Adjon meg egy nevet, válassza ki a "Web app/API" alkalmazástípus, adja meg a bejelentkezési URL-címet (például http://localhost) , és kattintson a Létrehozás gombra
1. A bejelentkezési URL-címet nem használja, és bármilyen érvényes URL-cím lehet
1. Válassza ki az új alkalmazást, és a beállítások lapon kattintson a kulcsok
1. Adja meg egy új kulcs leírását, válassza a "Soha nem jár le", és kattintson a Mentés gombra
1. Jegyezze fel az értéket. Használatban van a **jelszó** a Szolgáltatásnévhez
1. Jegyezze fel az alkalmazás azonosítóját. A felhasználónév használatban van (**bejelentkezési azonosító** az alábbi lépéseket a) a szolgáltatásnév

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]**  Az időkorlát-ügynökhöz tartozó egyéni szerepkör létrehozása

Az egyszerű szolgáltatás nem rendelkezik engedélyekkel alapértelmezés szerint az Azure-erőforrások eléréséhez. Elindíthatja és leállíthatja a szolgáltatásnév-engedélyt kell (szabadítsa fel) a fürt összes virtuális gépet. Ha nem hozott már létre az egyéni szerepkör, létrehozhat használatával [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell#create-a-custom-role) vagy [Azure CLI-vel](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli#create-a-custom-role)

A bemeneti fájl használja az alábbi tartalommal. Szeretne az előfizetések a tartalmat, amely alkalmazkodik c276fc76-9cd4-44c9-99a7-4fd71546436e és e91d47c4-76f3-4271-a796-21b4ecfe3624 cserélje le az előfizetés azonosítóját. Ha több előfizetéssel rendelkezik, távolítsa el a második bejegyzés AssignableScopes.

```json
{
  "Name": "Linux Fence Agent Role",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to deallocate and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/deallocate/action",
    "Microsoft.Compute/virtualMachines/start/action"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```

### <a name="1-assign-the-custom-role-to-the-service-principal"></a>**[1]**  Az egyéni szerepkör hozzárendelése a Szolgáltatásnévhez

Rendelje hozzá az egyéni szerepkör "Linux időkorlát ügynök szerepkör", amely az előző fejezetben a szolgáltatásnév sikeresen létrehozva. A tulajdonosi szerepkör ne használjon többé!

1. Nyissa meg a következőt: https://portal.azure.com
1. Nyissa meg az összes erőforrás panelen
1. Válassza ki a virtuális gépet, az első fürtcsomópontra
1. Kattintson a hozzáférés-vezérlés (IAM)
1. Kattintson az Add (Hozzáadás) parancsra
1. A "Linux időkorlát ügynök szerepkör" szerepkör kiválasztása
1. Adja meg a fent létrehozott alkalmazás neve
1. Kattintson az OK gombra

Ismételje meg a fenti lépéseket a második fürtcsomópontra.

### <a name="1-create-the-stonith-devices"></a>**[1]**  A STONITH eszközök létrehozása

Miután szerkesztette az engedélyek a virtuális gépek, konfigurálhatja úgy a STONITH eszközöket a fürtben.

<pre><code>
# replace the bold string with your subscription ID, resource group, tenant ID, service principal ID and password
sudo crm configure property stonith-timeout=900

sudo crm configure primitive rsc_st_azure stonith:fence_azure_arm \
   params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>login ID</b>" passwd="<b>password</b>"

</code></pre>

### <a name="1-create-fence-topology-for-sbd-fencing"></a>**[1]**  SBD szintaxiskiemeléshez időkorlát topológia létrehozása

Ha szeretne egy SBD eszközt használja, továbbra is használatát javasoljuk Azure időkorlát ügynök biztonsági abban az esetben, ha az iSCSI-tárolókiszolgáló nem érhető el.

<pre><code>
sudo crm configure fencing_topology \
  stonith-sbd rsc_st_azure

</code></pre>
### **[1] ** STONITH eszköz használatának engedélyezése

<pre><code>
sudo crm configure property stonith-enabled=true 
</code></pre>


## <a name="next-steps"></a>További lépések
* [Az Azure virtuális gépek tervezése és megvalósítása SAP][planning-guide]
* [Az SAP az Azure virtuális gépek üzembe helyezése][deployment-guide]
* [Az SAP az Azure Virtual Machines DBMS üzembe helyezése][dbms-guide]
* Magas rendelkezésre állást és az Azure virtuális gépeken SAP Hana vész-helyreállítási terv létrehozásához, lásd: [magas rendelkezésre állás az SAP HANA Azure-beli virtuális gépeken (VM)][sap-hana-ha]
