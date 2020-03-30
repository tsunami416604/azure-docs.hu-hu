---
title: Pacemaker beállítása rhel-en az Azure-ban | Microsoft dokumentumok
description: Pacemaker beállítása a Red Hat Enterprise Linuxon az Azure-ban
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
ms.date: 08/17/2018
ms.author: radeltch
ms.openlocfilehash: 21c551721815847eea4cb1435298ea6f7bf37966
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264478"
---
# <a name="setting-up-pacemaker-on-red-hat-enterprise-linux-in-azure"></a>Pacemaker beállítása a Red Hat Enterprise Linuxon az Azure-ban

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351

[virtual-machines-linux-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot


Először olvassa el a következő SAP-megjegyzéseket és dokumentumokat:

* Az SAP Note [1928533,]amely:
  * Az SAP-szoftverek üzembe helyezéséhez támogatott Azure virtuális gépméretek listája.
  * Fontos kapacitásadatok az Azure virtuális gép méreteihez.
  * A támogatott SAP szoftver, az operációs rendszer (OS) és az adatbázis-kombinációk.
  * A szükséges SAP kernel verzió Windows és Linux a Microsoft Azure-ban.
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
* [SAP HANA rendszer replikációja a pacemaker-fürtben](https://access.redhat.com/articles/3004101)
* Általános RHEL dokumentáció
  * [Magas rendelkezésre állású bővítmény – áttekintés](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Magas rendelkezésre állású bővítmény felügyelete](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Magas rendelkezésre állású bővítményhivatkozás](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Az RHEL magas rendelkezésre állású fürtök támogatási házirendjei – sbd és fence_sbd](https://access.redhat.com/articles/2800691)
* Azure-specifikus RHEL dokumentáció:
  * [Az RHEL magas rendelkezésre állású fürtjeinek támogatási szabályzatai – Microsoft Azure virtuális gépek fürttagként](https://access.redhat.com/articles/3131341)
  * [Red Hat Enterprise Linux 7.4 (és újabb) magas rendelkezésre állású fürt telepítése és konfigurálása a Microsoft Azure-ban](https://access.redhat.com/articles/3252491)
  * [Sap S/4HANA ASCS/ERS konfigurálása önálló enqueue server 2 (ENSA2) kiszolgálóval az RHEL 7.6 pacemakerében](https://access.redhat.com/articles/3974941)

## <a name="cluster-installation"></a>Fürt telepítése

![Pacemaker az RHEL áttekintés](./media/high-availability-guide-rhel-pacemaker/pacemaker-rhel.png)

> [!NOTE]
> Red Hat nem támogatja a szoftver-emulált házőrző. A Red Hat nem támogatja az SBD-t felhőalapú platformokon. További információt az [RHEL magas rendelkezésre állású fürtök – sbd és fence_sbd támogatási házirendjei](https://access.redhat.com/articles/2800691).
> Az azure-beli Red Hat Enterprise Linux-fürtök egyetlen támogatott kerítésmechanizmusa az Azure-beli kerítésügynök.  

A következő elemek előtaggal vannak ellátva **az [A]** - az összes csomópontra vonatkozóan, **[1]** - csak az 1- es vagy **a [2]** csomópontra vonatkozik, és csak a 2-es csomópontra vonatkoznak.

1. **[A]** Nyilvántartás

   Regisztrálja a virtuális gépeket, és csatolja egy rhel 7-es tárolókat tartalmazó készlethez.

   <pre><code>sudo subscription-manager register
   # List the available pools
   sudo subscription-manager list --available --matches '*SAP*'
   sudo subscription-manager attach --pool=&lt;pool id&gt;
   </code></pre>

   Vegye figyelembe, hogy egy készlet csatlakoztatásával egy Azure Marketplace PAYG RHEL-lemezképhez, akkor hatékonyan kétszeres számlázást az RHEL-használat: egyszer a PAYG-lemezkép, és egyszer az RHEL jogosultság a készletben csatolja. Ennek enyhítése érdekében az Azure most byos RHEL-lemezképeket biztosít. További információ [itt](../redhat/byos.md)érhető el .

1. **[A]** RHEL engedélyezése SAP-repókhoz

   A szükséges csomagok telepítéséhez engedélyezze a következő adattárakat.

   <pre><code>sudo subscription-manager repos --disable "*"
   sudo subscription-manager repos --enable=rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-sap-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-eus-rpms
   </code></pre>

1. **[A]** Telepítse rhel HA add-On

   <pre><code>sudo yum install -y pcs pacemaker fence-agents-azure-arm nmap-ncat
   </code></pre>

   > [!IMPORTANT]
   > Javasoljuk, hogy az Azure Fence ügynök (vagy újabb) következő verzióit az ügyfelek számára a gyorsabb feladatátvételi idő előnyeit élvezhetjük, ha egy erőforrás-leállítás sikertelen, vagy a fürtcsomópontok már nem tudnak kommunikálni, amelyeket egymás tól:  
   > RHEL 7.6: kerítés-ügynökök-4.2.1-11.el7_6,8  
   > RHEL 7.5: kerítés-ügynökök-4.0.11-86.el7_5,8  
   > RHEL 7.4: kerítés-ügynökök-4.0.11-66.el7_4.12  
   > További információ: [Az Azure virtuális gép fut, mint egy RHEL magas rendelkezésre állású fürt tagja, hogy egy nagyon hosszú ideig kell bekerített, vagy kerítés sikertelen / idő-out, mielőtt a virtuális gép leáll.](https://access.redhat.com/solutions/3408711)

   Ellenőrizze az Azure kerítésügynök verzióját. Szükség esetén frissítse azt a fent említettverzióval megegyező vagy későbbi verzióra.

   <pre><code># Check the version of the Azure Fence Agent
    sudo yum info fence-agents-azure-arm
   </code></pre>

   > [!IMPORTANT]
   > Ha frissítenie kell az Azure Fence-ügynököt, és egyéni szerepkört használ, győződjön meg arról, hogy frissíti az egyéni szerepkört, hogy tartalmazza a művelet **powerOff.** További részletek: [Egyéni szerepkör létrehozása a kerítésügynökhöz.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker#1-create-a-custom-role-for-the-fence-agent)  

1. **[A]** Állomásnév feloldása

   Használhatja a DNS-kiszolgálót, vagy módosíthatja az /etc/hosts kapcsolót az összes csomóponton. Ez a példa az /etc/hosts fájl használatát mutatja be.
   Cserélje le az IP-címet és az állomásnevet a következő parancsokban. Az /etc/hosts használatának előnye, hogy a fürt függetlenné válik a DNS-től, ami a hibák egyetlen pontja is lehet.

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Szúrja be a következő sorokat az /etc/hosts könyvtárba. Az IP-cím és az állomásnév módosítása a környezetnek megfelelően

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[A]** A hacluster jelszó módosítása ugyanarra a jelszóra

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]** Tűzfalszabályok hozzáadása a szívritmus-szabályozóhoz

   Adja hozzá a következő tűzfalszabályokat a fürtcsomópontok közötti összes fürtkommunikációhoz.

   <pre><code>sudo firewall-cmd --add-service=high-availability --permanent
   sudo firewall-cmd --add-service=high-availability
   </code></pre>

1. **[A]** Alapvető fürtszolgáltatások engedélyezése

   Futtassa a következő parancsokat a Pacemaker szolgáltatás engedélyezéséhez és elindításához.

   <pre><code>sudo systemctl start pcsd.service
   sudo systemctl enable pcsd.service
   </code></pre>

1. **[1]** Pacemaker-fürt létrehozása

   Futtassa a következő parancsokat a csomópontok hitelesítéséhez és a fürt létrehozásához. Állítsa a jogkivonatot 30000-re, hogy a memória megőrizhesse a karbantartást. További információt [ebben a Linux cikkben talál.][virtual-machines-linux-maintenance]

   <pre><code>sudo pcs cluster auth <b>prod-cl1-0</b> <b>prod-cl1-1</b> -u hacluster
   sudo pcs cluster setup --name <b>nw1-azr</b> <b>prod-cl1-0</b> <b>prod-cl1-1</b> --token 30000
   sudo pcs cluster start --all

   # Run the following command until the status of both nodes is online
   sudo pcs status

   # Cluster name: nw1-azr
   # WARNING: no stonith devices and stonith-enabled is not false
   # Stack: corosync
   # Current DC: <b>prod-cl1-1</b> (version 1.1.18-11.el7_5.3-2b07d5c5a9) - partition with quorum
   # Last updated: Fri Aug 17 09:18:24 2018
   # Last change: Fri Aug 17 09:17:46 2018 by hacluster via crmd on <b>prod-cl1-1</b>
   #
   # 2 nodes configured
   # 0 resources configured
   #
   # Online: [ <b>prod-cl1-0</b> <b>prod-cl1-1</b> ]
   #
   # No resources
   #
   #
   # Daemon Status:
   #   corosync: active/disabled
   #   pacemaker: active/disabled
   #   pcsd: active/enabled
   </code></pre>

1. **[A]** A várható szavazatok beállítása

   <pre><code>sudo pcs quorum expected-votes 2
   </code></pre>

## <a name="create-stonith-device"></a>STONITH eszköz létrehozása

A STONITH-eszköz egy egyszerű szolgáltatás használatával engedélyezi a Microsoft Azure-t. Az egyszerű szolgáltatás létrehozásához kövesse az alábbi lépéseket.

1. Nyissa meg a következőt: <https://portal.azure.com>
1. Az Azure Active Directory panel megnyitása  
   Nyissa meg a Tulajdonságok felet, és írja le a címtárazonosítót. Ez a **bérlőazonosítója.**
1. Kattintson az Alkalmazásregisztrációk elemre
1. Kattintson az Új regisztráció gombra
1. Írjon be egy nevet, és válassza a "Csak ebben a szervezeti címtárban lévő fiókok" lehetőséget. 
2. Válassza a "Web" alkalmazástípust, adjon meg egy\/bejelentkezési URL-címet (például http: /localhost), és kattintson a Hozzáadás gombra  
   A bejelentkezési URL nem használatos, és bármely érvényes URL-cím lehet
1. Válassza a Tanúsítványok és titkos kulcsok lehetőséget, majd kattintson az Új ügyféltitok elemre.
1. Adjon meg egy új kulcsot, válassza a "Soha nem jár le" lehetőséget, és kattintson a Hozzáadás gombra.
1. Írja le az Érték. A szolgáltatásegyszerű **jelszóként** szolgál.
1. Válassza az Áttekintés lehetőséget. Írja le az alkalmazásazonosítót. A Szolgáltatásnév felhasználóneveként (az alábbi lépésekben**bejelentkezési azonosító)**

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]** Egyéni szerepkör létrehozása a kerítésügynök számára

A szolgáltatásnév alapértelmezés szerint nem rendelkezik az Azure-erőforrások eléréséhez szükséges engedélyekkel. Meg kell adnia a Szolgáltatás egyszerű engedélyeket a fürt összes virtuális gépének indításához és leállításához (kikapcsolás). Ha még nem hozták létre az egyéni szerepkört, létrehozhatja azt [a PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell) vagy az [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) használatával

Használja a következő tartalmat a bemeneti fájlhoz. Meg kell igazítani a tartalmat, hogy az előfizetések, azaz cserélje c276fc76-9cd4-44c9-99a7-4fd71546436e és e91d47c4-76f3-4271-a796-21b4ecfe3624 az azonosítót az előfizetés. Ha csak egy előfizetéssel rendelkezik, távolítsa el a második bejegyzést a AssignableScopes alkalmazásból.

```json
{
  "Name": "Linux Fence Agent Role",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to power-off and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/powerOff/action",
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

### <a name="a-assign-the-custom-role-to-the-service-principal"></a>**[A]** Az egyéni szerepkör hozzárendelése az egyszerű szolgáltatáshoz

Rendelje hozzá az egyéni szerepkör "Linux kerítésügynök szerepkör", amely az utolsó fejezetben létrehozott a szolgáltatásnév. Ne használja többé a Tulajdonos szerepkört!

1. Nyissa meg a következőt: https://portal.azure.com
1. A Minden erőforrás panel megnyitása
1. Az első fürtcsomópont virtuális gépének kiválasztása
1. Kattintson a Hozzáférés-vezérlés (IAM) elemre
1. Kattintson a Szerepkör-hozzárendelés hozzáadása gombra.
1. Válassza ki a "Linux kerítésügynök szerepkör" szerepkört
1. Adja meg a fent létrehozott alkalmazás nevét.
1. Kattintson a Save (Mentés) gombra.

Ismételje meg a fenti lépéseket a második fürtcsomóponthoz.

### <a name="1-create-the-stonith-devices"></a>**[1]** A STONITH eszközök létrehozása

Miután szerkesztette a virtuális gépek engedélyeit, konfigurálhatja a STONITH-eszközöket a fürtben.

<pre><code>
sudo pcs property set stonith-timeout=900
</code></pre>

A következő paranccsal konfigurálhatja a kerítéseszközt.

> [!NOTE]
> A "pcmk_host_map" beállítás csak akkor szükséges a parancsban, ha az RHEL állomásnevek és az Azure-csomópont nevei NEM azonosak. Lásd a parancs félkövér szakaszát.

<pre><code>sudo pcs stonith create rsc_st_azure fence_azure_arm login="<b>login ID</b>" passwd="<b>password</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" subscriptionId="<b>subscription id</b>" <b>pcmk_host_map="prod-cl1-0:10.0.0.6;prod-cl1-1:10.0.0.7"</b> power_timeout=240 pcmk_reboot_timeout=900</code></pre>

### <a name="1-enable-the-use-of-a-stonith-device"></a>**[1]** STONITH eszköz használatának engedélyezése

<pre><code>sudo pcs property set stonith-enabled=true
</code></pre>

## <a name="next-steps"></a>További lépések

* [Az Azure virtuális gépek tervezése és megvalósítása az SAP-hoz][planning-guide]
* [Az Azure virtuális gépek üzembe helyezése az SAP-hoz][deployment-guide]
* [Az Azure virtual machines DBMS üzembe helyezése az SAP-hoz][dbms-guide]
* Ha meg szeretné tudni, hogyan hozhat létre magas rendelkezésre állást, és tervezze meg az SAP HANA vészutáni helyreállítását az Azure virtuális gépeken, olvassa el [az SAP HANA magas rendelkezésre állását az Azure virtuális gépeken (VM-ek) című témakört.][sap-hana-ha]
