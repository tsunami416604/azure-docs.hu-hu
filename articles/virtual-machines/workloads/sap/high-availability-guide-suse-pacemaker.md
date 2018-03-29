---
title: A SUSE Linux Enterprise Server az Azure-ban támasztja beállítása |} Microsoft Docs
description: A SUSE Linux Enterprise Server az Azure-ban támasztja beállítása
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
ms.date: 03/20/2018
ms.author: sedusch
ms.openlocfilehash: 75615de523f1fba808f44fb1a1015138fb190edc
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="setting-up-pacemaker-on-suse-linux-enterprise-server-in-azure"></a>A SUSE Linux Enterprise Server az Azure-ban támasztja beállítása

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md

Az Azure-ban támasztja fürt beállításához két lehetőség áll rendelkezésre. Használhatja a kerítés ügynök, amely gondoskodik az Azure API-k segítségével sikertelen csomópont újraindítása, vagy egy SBD eszközt is használhat.

A SBD eszköz szükséges további virtuális gépek közül az iSCSI-tárolókiszolgáló funkcionál, és egy SBD eszközt biztosít. Az iSCSI-tárolókiszolgáló azonban lehet más támasztja fürtök megosztott. Egy SBD eszköz használatának előnye feladatátvételi gyorsabb és SBD a helyszíni eszközök, használatakor nem szükséges hogyan fog működni a támasztja fürt végzett módosításokat. A SBD kerítés továbbra is használhatja az Azure időkorlát ügynök biztonsági kerítésépítés mechanizmus, abban az esetben, ha az iSCSI-tárolókiszolgáló nem érhető el.

Ha nem szeretné, hogy további virtuális gépek beruházásának, az Azure időkorlát ügynök is használhatja. A hátránya az, hogy a feladatátvétel esetén is igénybe vehet 10 – 15 perc között erőforrás stop meghibásodik, vagy a fürt csomópontjai nem tud kommunikálni amelyek egymással többé.

![Támasztja a SLES áttekintése](./media/high-availability-guide-suse-pacemaker/pacemaker.png)

## <a name="sbd-fencing"></a>SBD kerítés

Ha azt szeretné, hogy egy SBD eszköz használandó kerítés, kövesse az alábbi lépéseket.

### <a name="set-up-an-iscsi-target-server"></a>Az iSCSI-tárolókiszolgáló beállítása

Először hozzon létre egy iSCSI cél virtuális gépre, ha még nem rendelkezik ilyennel már. iSCSI-célkiszolgálókhoz megosztható több támasztja fürt.

1. Új SLES 12 SP1 vagy újabb virtuális gépet telepít, és kapcsolódjon a géphez keresztül ssh. A gép nem kell tekintélyes lehet. A virtuális gép méretét, például Standard_E2s_v3 vagy Standard_D2s_v3 is használhatók.

1. SLES frissítése

   <pre><code>
   sudo zypper update
   </code></pre>

1. ISCSI cél csomagok telepítése

   <pre><code>
   sudo zypper install targetcli-fb dbus-1-python
   </code></pre>

1. Az iSCSI-tároló szolgáltatás engedélyezése

   <pre><code>   
   sudo systemctl enable target
   sudo systemctl enable targetcli
   sudo systemctl start target
   sudo systemctl start targetcli
   </code></pre>

### <a name="create-iscsi-device-on-iscsi-target-server"></a>Az iSCSI-tárolókiszolgáló iSCSI-eszköz létrehozása

ISCSI cél virtuális gép nem használható ehhez a fürthöz egy új adatlemezt csatolni. Az adatlemez lehet mérete 1 GB legyen, és kell helyezni a prémium szintű Storage-fiók vagy a prémium szintű felügyelt lemez is kihasználhatják a [egyetlen virtuális gép SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines).

Futtassa a következő parancsot a **iSCSI-tároló virtuális gép** iSCSI-lemez az új fürt létrehozásához. A következő példában **cl1** alapján határozza meg az új fürthöz és **termék-cl1-0** és **termék-cl1-1** a gazdagép neve a fürtcsomópontok vannak. Cserélje le azokat a gazdagép neve a csomópont.

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
sudo systemctl restart target
</code></pre>

### <a name="set-up-sbd-device"></a>SBD eszköz beállítása

Az iSCSI-eszközhöz a fürtből az előző lépésben létrehozott csatlakozni.
A következő parancsokat a csomópontokon szeretne létrehozni az új fürthöz.
A következő elemek fűzve előtagként vagy **[A]** – az összes csomópont alkalmazandó **[1]** – csak érvényes csomópont 1 vagy **[2]** - csomópont 2 csak érvényes.

1. **[A]**  Csatlakozás az iSCSI-eszközök

   Az iSCSI és a szolgáltatások SBD először, engedélyezése.

   <pre><code>
   sudo systemctl enable iscsid
   sudo systemctl enable iscsi
   sudo systemctl enable sbd
   </code></pre>

1. **[1]**  Az első csomóponton kezdeményező nevének módosítása

   <pre><code>
   sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   A fájl az ACL-ek az iSCSI-tárolókiszolgáló az iSCSI-eszközhöz létrehozásakor használt kereséséhez a tartalmának módosítása

   <pre><code>   
   InitiatorName=<b>iqn.2006-04.prod-cl1-0.local:prod-cl1-0</b>
   </code></pre>

1. **[2]**  a kezdeményező neve, a második csomópont módosítása

   <pre><code>
   sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   A fájl az ACL-ek az iSCSI-tárolókiszolgáló az iSCSI-eszközhöz létrehozásakor használt kereséséhez a tartalmának módosítása

   <pre><code>
   InitiatorName=<b>iqn.2006-04.prod-cl1-1.local:prod-cl1-1</b>
   </code></pre>

1. **[A]**  Az iSCSI-szolgáltatás újraindítása

   Most indítsa újra az iSCSI szolgáltatást, a módosítás alkalmazására
   
   <pre><code>
   sudo systemctl restart iscsid
   sudo systemctl restart iscsi
   </code></pre>

   Az iSCSI-eszközök csatlakoztatásához. Az alábbi példában 10.0.0.17 az iSCSI-tárolókiszolgáló IP-címe pedig 3260-as az alapértelmezett port. <b>IQN.2006-04.cl1.local:cl1</b> a cél neve, amely szerepel az első parancs futtatásakor.

   <pre><code>
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.17:3260</b>
   
   sudo iscsiadm -m node -T <b>iqn.2006-04.cl1.local:cl1</b> --login --portal=<b>10.0.0.17:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.17:3260</b> --op=update --name=node.startup --value=automatic
   </code></pre>

   Győződjön meg arról, hogy az iSCSI-eszköz rendelkezésre áll-e, és ne feledkezzen meg az eszköz nevét (a következő példa/dev/sde)

   <pre><code>
   lsscsi
   
   # [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
   # [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
   # [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
   # [5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd
   # <b>[6:0:0:0]    disk    LIO-ORG  cl1              4.0   /dev/sde</b>
   </code></pre>

   Most beolvasása az iSCSI-eszköz azonosítója.

   <pre><code>
   ls -l /dev/disk/by-id/scsi-* | grep <b>sde</b>
   
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-1LIO-ORG_cl1:3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   # <b>lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df -> ../../sde</b>
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-SLIO-ORG_cl1_3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   </code></pre>

   A parancs három eszközazonosítókat listán. Javasoljuk, hogy a scsi-3, a fenti példában kezdetű azonosító használata
   
   **/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df**

1. **[1]**  A SBD eszköz létrehozása

   Az Eszközazonosítót az iSCSI-eszköz segítségével hozzon létre egy új SBD eszköz az első csomóponton.

   <pre><code>
   sudo sbd -d <b>/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df</b> -1 10 -4 20 create
   </code></pre>

1. **[A]**  Igazítja a SBD konfigurációja

   Nyissa meg a SBD konfigurációs fájl

   <pre><code>
   sudo vi /etc/sysconfig/sbd
   </code></pre>

   A tulajdonság a SBD eszköz módosításához, a támasztja integrációjának engedélyezése és SBD indítási módja módosítása.

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

   A modul most betöltése

   <pre><code>
   sudo modprobe -v softdog
   </code></pre>

## <a name="cluster-installation"></a>Fürt telepítése

A következő elemek fűzve előtagként vagy **[A]** – az összes csomópont alkalmazandó **[1]** – csak érvényes csomópont 1 vagy **[2]** - csomópont 2 csak érvényes.

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

2. **[2]**  Ssh hozzáférés engedélyezése

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

1. **[A]**  Telepítése magas rendelkezésre ÁLLÁSÚ bővítmény
   
   <pre><code>
   sudo zypper install sle-ha-release fence-agents
   </code></pre>

1. **[A]**  Állomásnév beállítása   

   DNS-kiszolgálót használjon, vagy módosítsa az/etc/hosts minden csomóponton. Ez a példa bemutatja, hogyan használható az/etc/hosts fájlt.
   Cserélje le az IP-cím és a következő parancsokat az állomásnév

   <pre><code>
   sudo vi /etc/hosts
   </code></pre>
   
   Helyezze be a következő sorokat/etc/hosts. Az IP-cím és a környezet megfelelő állomásnév módosítása   
   
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

1. **[2]**  Csomópont hozzáadása a fürthöz
   
   <pre><code> 
   sudo ha-cluster-join
   
   # Do you want to continue anyway? [y/N] -> y
   # IP address or hostname of existing node (for example: 192.168.1.1) [] -> IP address of node 1 for example 10.0.0.14
   # /root/.ssh/id_rsa already exists - overwrite? [y/N] N
   </code></pre>

1. **[A]**  Hacluster ugyanazt a jelszót a jelszó módosítása

   <pre><code> 
   sudo passwd hacluster
   </code></pre>

1. **[A]**  Egyéb átvitelt használ, és adja hozzá a csomópontlista corosync konfigurálása. Fürt egyébként nem működik.
   
   <pre><code> 
   sudo vi /etc/corosync/corosync.conf   
   </code></pre>

   Vegye fel a következő félkövér tartalmat a fájlba.
   
   <pre><code> 
   [...]
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

   Indítsa újra a corosync szolgáltatás

   <pre><code>
   sudo service corosync restart
   </code></pre>

1. **[1]**  Támasztja alapértelmezett beállításainak módosítása

   <pre><code>
   sudo crm configure rsc_defaults resource-stickiness="1"   
   </code></pre>

## <a name="create-stonith-device"></a>STONITH eszköz létrehozása

A STONITH eszköz egy egyszerű szolgáltatást használ, szemben a Microsoft Azure engedélyezése. Kövesse az alábbi lépéseket egy egyszerű szolgáltatásnév létrehozásához.

1. Nyissa meg a következőt: <https://portal.azure.com>
1. Nyissa meg az Azure Active Directory panelt  
   Nyissa meg tulajdonságait, és jegyezze fel a könyvtár-azonosító. Ez a **bérlői azonosító**.
1. Kattintson az alkalmazás-regisztráció
1. Kattintson az Add (Hozzáadás) parancsra
1. Adjon meg egy nevet, válassza ki a "Web app/API" alkalmazástípus, adja meg a bejelentkezési URL-címet (például http://localhost) , és kattintson a Létrehozás gombra
1. A bejelentkezési URL-címet nem használja, és bármilyen érvényes URL-CÍMEK lehetnek
1. Válassza ki az új alkalmazást, és a beállítások lapon kattintson a kulcsok
1. Adja meg egy új kulcs leírását, válassza a "Soha nem jár le", és kattintson a Mentés gombra
1. Jegyezze fel az értéket. Használják a **jelszó** a szolgáltatás egyszerű
1. Jegyezze fel az alkalmazás azonosítóját. A felhasználónév használják (**bejelentkezési azonosító** az alábbi lépéseket a) a szolgáltatás egyszerű

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]**  Hozzon létre egy egyéni biztonsági szerepkört az időkorlát ügynök

A szolgáltatás egyszerű nincs engedélye a alapértelmezés szerint az Azure-erőforrások eléréséhez. Hozzá kell rendelnie a szolgáltatás egyszerű engedélyek indítása és leállítása (felszabadítása) a fürt összes virtuális gépet. Ha nem már Ön a egyéni biztonsági szerepkört, létrehozhat használatával [PowerShell](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-powershell#create-a-custom-role) vagy [Azure parancssori felület](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-azure-cli#create-a-custom-role)

A következő tartalmat használni, a bemeneti fájl. Az előfizetések a tartalmat, amely igazítja, c276fc76-9cd4-44c9-99a7-4fd71546436e és e91d47c4-76f3-4271-a796-21b4ecfe3624 cserélje le az azonosítók, az előfizetés kell. Ha több előfizetéssel rendelkezik, távolítsa el a második bejegyzés AssignableScopes.

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

### <a name="1-assign-the-custom-role-to-the-service-principal"></a>**[1]**  Rendelje hozzá az egyéni biztonsági szerepkört a szolgáltatás egyszerű

A "Linux időkorlát ügynök szerepkör" az előző fejezetben a szolgáltatás egyszerű a létrehozott egyéni szerepkör hozzárendelése. Nem használható a tulajdonosi szerepkört már!

1. Nyissa meg a következőt: https://portal.azure.com
1. Nyissa meg az összes erőforrás panel
1. Válassza ki a virtuális gépet, az első fürtcsomópont
1. Kattintson a hozzáférés-vezérlés (IAM)
1. Kattintson az Add (Hozzáadás) parancsra
1. Válassza ki a szerepkört "Linux időkorlát ügynök szerepkör"
1. Adja meg az előbb létrehozott alkalmazás nevét
1. Kattintson az OK gombra

Ismételje meg a fenti lépéseket a második fürt összes csomópontjára vonatkozóan.

### <a name="1-create-the-stonith-devices"></a>**[1]**  STONITH eszközök létrehozása

Után szerkeszteni a virtuális gépek engedélyeit, beállíthatja a STONITH eszközök a fürtben.

<pre><code>
# replace the bold string with your subscription ID, resource group, tenant ID, service principal ID and password
sudo crm configure property stonith-timeout=900

sudo crm configure primitive rsc_st_azure stonith:fence_azure_arm \
   params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>login ID</b>" passwd="<b>password</b>"

</code></pre>

### <a name="1-create-fence-topology-for-sbd-fencing"></a>**[1]**  SBD kerítés időkorlát topológia létrehozása

Ha egy SBD eszközt használni kívánt, továbbra is használatát javasoljuk Azure időkorlát ügynök biztonsági abban az esetben, ha az iSCSI-tárolókiszolgáló nem érhető el.

<pre><code>
sudo crm configure fencing_topology \
  stonith-sbd rsc_st_azure

</code></pre>
### **[1] ** STONITH eszköz használatának engedélyezése

<pre><code>
sudo crm configure property stonith-enabled=true 
</code></pre>


## <a name="next-steps"></a>További lépések
* [Az Azure virtuális gépek tervezési és megvalósítási az SAP][planning-guide]
* [Az SAP Azure virtuális gépek telepítése][deployment-guide]
* [Az SAP Azure virtuális gépek adatbázis-kezelő telepítése][dbms-guide]
* Magas rendelkezésre állás és az Azure virtuális gépeken az SAP HANA vész-helyreállítási terv létrehozásához, lásd: [magas rendelkezésre állás az SAP HANA Azure virtuális gépek (VM)][sap-hana-ha]