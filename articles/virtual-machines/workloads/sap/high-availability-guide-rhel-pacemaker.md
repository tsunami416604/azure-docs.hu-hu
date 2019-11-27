---
title: A pacemaker beállítása a Red Hat Enterprise Linux az Azure-ban | Microsoft Docs
description: A pacemaker beállítása Red Hat Enterprise Linux az Azure-ban
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/17/2018
ms.author: sedusch
ms.openlocfilehash: ee67c811835d99bf2f4c00dc59b43e29f63c81d6
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533813"
---
# <a name="setting-up-pacemaker-on-red-hat-enterprise-linux-in-azure"></a>A pacemaker beállítása Red Hat Enterprise Linux az Azure-ban

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[2009879]: https://launchpad.support.sap.com/#/notes/2009879
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1999351]: https://launchpad.support.sap.com/#/notes/1999351

[virtual-machines-linux-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot


Először olvassa el a következő SAP-megjegyzéseket és dokumentumokat:

* SAP-Megjegyzés [1928533], amely a következőket tartalmazta:
  * Az SAP-szoftverek üzembe helyezéséhez támogatott Azure-beli virtuálisgép-méretek listája.
  * Az Azure-beli virtuális gépek méretével kapcsolatos fontos kapacitási információk.
  * A támogatott SAP-szoftverek és operációs rendszerek (OS) és adatbázis-kombinációk.
  * A Windows és a Linux szükséges SAP kernel-verziója Microsoft Azureon.
* Az SAP Note [2015553] az SAP által támogatott SAP-szoftverek Azure-beli üzembe helyezésének előfeltételeit sorolja fel.
* Az SAP Megjegyzés [2002167] ajánlott operációsrendszer-beállításokkal Red Hat Enterprise Linux
* A [2009879] -es SAP-Megjegyzés SAP HANA irányelvek a Red Hat Enterprise Linux
* Az [2178632] -es SAP-Megjegyzés részletes információkat tartalmaz az Azure-beli SAP-ban jelentett összes figyelési mérőszámról.
* A [2191498] -es SAP-Megjegyzés a szükséges SAP-gazdagép ügynökének verziója az Azure-ban linuxos.
* Az [2243692] -es SAP-Megjegyzés az Azure-beli Linuxon futó SAP-licenceléssel kapcsolatos információkat tartalmaz.
* Az SAP Megjegyzés [1999351] további hibaelhárítási információkat tartalmaz az SAP-hez készült Azure Enhanced monitoring bővítménnyel kapcsolatban.
* Az [SAP Community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) rendelkezik minden szükséges SAP-megjegyzéssel a Linux rendszerhez.
* [Azure Virtual Machines tervezése és implementálása Linux rendszeren az SAP-ban][planning-guide]
* [Azure Virtual Machines üzembe helyezés az SAP-hez Linux rendszeren (ez a cikk)][deployment-guide]
* [Azure Virtual Machines adatbázis-kezelői telepítés az SAP-hez Linux rendszeren][dbms-guide]
* [Rendszerreplikáció SAP HANA a pacemaker-fürtben](https://access.redhat.com/articles/3004101)
* Általános RHEL dokumentáció
  * [Magas rendelkezésre állású bővítmény – áttekintés](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Magas rendelkezésre állású bővítmények felügyelete](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Magas rendelkezésre állású bővítmények leírása](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [A magas rendelkezésre állású fürtök RHEL vonatkozó támogatási szabályzatok – SBD és fence_sbd](https://access.redhat.com/articles/2800691)
* Az Azure-specifikus RHEL dokumentációja:
  * [A RHEL magas rendelkezésre állású fürtökre vonatkozó támogatási szabályzatok – Microsoft Azure Virtual Machines a fürt tagjai](https://access.redhat.com/articles/3131341)
  * [Red Hat Enterprise Linux 7,4 (és újabb) magas rendelkezésre állású fürt telepítése és konfigurálása Microsoft Azure](https://access.redhat.com/articles/3252491)
  * [Az SAP S/4HANA ASCS/ERS konfigurálása önálló sorba helyezni-kiszolgáló 2 (ENSA2) segítségével a pacemaker on RHEL 7,6](https://access.redhat.com/articles/3974941)

## <a name="cluster-installation"></a>Fürt telepítése

![Pacemaker on RHEL – áttekintés](./media/high-availability-guide-rhel-pacemaker/pacemaker-rhel.png)

> [!NOTE]
> A Red Hat nem támogatja a szoftveresen emulált watchdog használatát. A Red Hat nem támogatja a SBD a felhőalapú platformokon. Részletekért lásd: [a RHEL magas rendelkezésre állású fürtökhöz kapcsolódó támogatási szabályzatai – SBD és fence_sbd](https://access.redhat.com/articles/2800691).
> Az egyetlen támogatott kerítési mechanizmus a pacemaker Red Hat Enterprise Linux-fürtökhöz az Azure-ban, az Azure kerítés ügynöke.  

A következő elemek a **[a]** előtaggal vannak ellátva, amelyek az összes csomópontra érvényesek, **[1]** – csak az 1. vagy **[2]** csomópontra érvényesek, csak a 2. csomópontra.

1. **[A]** regisztráció

   Regisztrálja a virtuális gépeket, és csatolja azt egy készlethez, amely a RHEL 7 tárházait tartalmazza.

   <pre><code>sudo subscription-manager register
   # List the available pools
   sudo subscription-manager list --available --matches '*SAP*'
   sudo subscription-manager attach --pool=&lt;pool id&gt;
   </code></pre>

   Vegye figyelembe, hogy a készletnek egy Azure Marketplace-TB RHEL-rendszerképhez való csatolásával a RHEL-használat után gyakorlatilag egyszer kell fizetnie, és egyszer kell megadnia a RHEL jogosultságot a csatolni kívánt készletben. Ennek enyhítése érdekében az Azure mostantól BYOS RHEL-lemezképeket biztosít. További információ [itt](https://aka.ms/rhel-byos)található.

1. **[A]** RHEL engedélyezése az SAP-repók számára

   A szükséges csomagok telepítéséhez engedélyezze a következő adattárakat.

   <pre><code>sudo subscription-manager repos --disable "*"
   sudo subscription-manager repos --enable=rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-sap-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-eus-rpms
   </code></pre>

1. **[A]** install RHEL ha bővítmény

   <pre><code>sudo yum install -y pcs pacemaker fence-agents-azure-arm nmap-ncat
   </code></pre>

   > [!IMPORTANT]
   > A következő Azure kerítés-ügynök (vagy újabb verziók) használatát javasoljuk az ügyfelek számára a gyorsabb feladatátvételi idő kihasználása érdekében, ha egy erőforrás leáll, vagy ha a fürtcsomópontok nem tudnak kommunikálni egymással:  
   > RHEL 7,6: kerítés-ügynökök-4.2.1-11. el7_6.8  
   > RHEL 7,5: kerítés-ügynökök-4.0.11-86. el7_5.8  
   > RHEL 7,4: kerítés-ügynökök-4.0.11-66. el7_4.12  
   > További információkért tekintse meg a [magas rendelkezésre állású RHEL futó Azure-beli virtuális gépeket, ha a virtuális gép leáll, vagy a kerítés sikertelen/időtúllépést](https://access.redhat.com/solutions/3408711)okoz.

   Keresse meg az Azure kerítés ügynökének verzióját. Ha szükséges, frissítse a fent megadott vagy újabb verzióra.

   <pre><code># Check the version of the Azure Fence Agent
    sudo yum info fence-agents-azure-arm
   </code></pre>

   > [!IMPORTANT]
   > Ha frissítenie kell az Azure kerítés-ügynököt, és ha egyéni szerepkört használ, ügyeljen arra, hogy frissítse az egyéni szerepkört a **következő művelettel**:. További részletekért lásd: [Egyéni szerepkör létrehozása a kerítési ügynökhöz](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker#1-create-a-custom-role-for-the-fence-agent).  

1. **[A]** telepítési állomásnév feloldása

   DNS-kiszolgálót használjon, vagy módosítsa a Hosts az összes csomópontra. Ez a példa bemutatja, hogyan használhatja a Hosts fájlt.
   Cserélje le az IP-cím és az állomásnevet, az alábbi parancsokban. A Hosts használatával előnye, hogy a fürt független a DNS, amely túl lehet egyetlen pont, a hibák válik.

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Helyezze be a következő sorokat Hosts. Módosítsa az IP-cím és a környezet megfelelő állomásnév

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[A]** hacluster jelszavának módosítása ugyanarra a jelszóra

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]** tűzfalszabályok hozzáadása a pacemakerhez

   Adja hozzá a következő tűzfalszabályok a fürt csomópontjai közötti összes fürt kommunikációhoz.

   <pre><code>sudo firewall-cmd --add-service=high-availability --permanent
   sudo firewall-cmd --add-service=high-availability
   </code></pre>

1. **[A]** alapszintű fürtszolgáltatások engedélyezése

   Futtassa a következő parancsokat a pacemaker szolgáltatás engedélyezéséhez, és indítsa el.

   <pre><code>sudo systemctl start pcsd.service
   sudo systemctl enable pcsd.service
   </code></pre>

1. **[1]** pacemaker-fürt létrehozása

   Futtassa a következő parancsokat a csomópontok hitelesítéséhez és a fürt létrehozásához. Állítsa a tokent 30000-re, hogy a memóriát megőrizve karbantartást engedélyezzen. További információkért tekintse meg [ezt a cikket a Linux rendszerhez][virtual-machines-linux-maintenance].

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

1. **[A] A** várt szavazatok beállítása

   <pre><code>sudo pcs quorum expected-votes 2
   </code></pre>

## <a name="create-stonith-device"></a>STONITH-eszköz létrehozása

A STONITH eszköz hitelesítéséhez, szemben a Microsoft Azure egy egyszerű szolgáltatást használja. Kövesse az alábbi lépéseket egy szolgáltatásnév létrehozásához.

1. Nyissa meg a következőt: <https://portal.azure.com>
1. Nyissa meg az Azure Active Directory panel  
   Lépjen a Tulajdonságok részhez, és jegyezze fel a címtár-azonosító. Ez a **bérlő azonosítója**.
1. Kattintson az alkalmazásregisztrációk
1. Kattintson az új regisztráció elemre.
1. Adjon meg egy nevet, válassza a "fiókok ebben a szervezeti címtárban" lehetőséget. 
2. Válassza az alkalmazás típusa "web" lehetőséget, írja be a bejelentkezési URL-címet (például http:\//localhost), és kattintson a Hozzáadás gombra.  
   A bejelentkezési URL-címet nem használja, és bármilyen érvényes URL-cím lehet
1. Válassza a tanúsítványok és titkos kulcsok lehetőséget, majd kattintson az új ügyfél titka elemre.
1. Adja meg az új kulcs leírását, válassza a "soha nem jár le" lehetőséget, majd kattintson a Hozzáadás gombra.
1. Jegyezze fel az értéket. Az egyszerű szolgáltatás **jelszavaként** van használatban
1. Válassza az Áttekintés lehetőséget. Jegyezze fel az alkalmazás azonosítóját. A szolgáltatás felhasználóneveként (**Bejelentkezési azonosítóként** az alábbi lépésekben) használatos az egyszerű szolgáltatásnév számára

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]** egyéni szerepkör létrehozása a kerítés ügynökéhez

Az egyszerű szolgáltatás nem rendelkezik engedélyekkel alapértelmezés szerint az Azure-erőforrások eléréséhez. Meg kell adnia a szolgáltatásnév számára a fürt összes virtuális gépe elindításához és leállításához (kikapcsolásához) szükséges engedélyeket. Ha még nem tette meg az egyéni szerepkört, akkor a [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell) vagy az [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) használatával hozhatja létre

A bemeneti fájl használja az alábbi tartalommal. Szeretne az előfizetések a tartalmat, amely alkalmazkodik c276fc76-9cd4-44c9-99a7-4fd71546436e és e91d47c4-76f3-4271-a796-21b4ecfe3624 cserélje le az előfizetés azonosítóját. Ha több előfizetéssel rendelkezik, távolítsa el a második bejegyzés AssignableScopes.

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

### <a name="a-assign-the-custom-role-to-the-service-principal"></a>**[A]** az egyéni szerepkör társítása az egyszerű szolgáltatáshoz

Rendelje hozzá az egyéni szerepkör "Linux időkorlát ügynök szerepkör", amely az előző fejezetben a szolgáltatásnév sikeresen létrehozva. A tulajdonosi szerepkör ne használjon többé!

1. Nyissa meg a következőt: https://portal.azure.com
1. Nyissa meg az összes erőforrás panelen
1. Válassza ki a virtuális gépet, az első fürtcsomópontra
1. Kattintson a hozzáférés-vezérlés (IAM)
1. Kattintson a szerepkör-hozzárendelés hozzáadása
1. A "Linux időkorlát ügynök szerepkör" szerepkör kiválasztása
1. Adja meg a fent létrehozott alkalmazás neve
1. Kattintson a Save (Mentés) gombra.

Ismételje meg a fenti lépéseket a második fürtcsomópontra.

### <a name="1-create-the-stonith-devices"></a>**[1]** a STONITH-eszközök létrehozása

Miután szerkesztette az engedélyek a virtuális gépek, konfigurálhatja úgy a STONITH eszközöket a fürtben.

<pre><code>
sudo pcs property set stonith-timeout=900
</code></pre>

A kerítés eszköz konfigurálásához használja a következő parancsot.

> [!NOTE]
> A (z) "pcmk_host_map" kapcsoló csak akkor szükséges a parancsban, ha a RHEL és az Azure-csomópontok nevei nem egyeznek. Tekintse meg az parancs félkövér szakaszát.

<pre><code>sudo pcs stonith create rsc_st_azure fence_azure_arm login="<b>login ID</b>" passwd="<b>password</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" subscriptionId="<b>subscription id</b>" <b>pcmk_host_map="prod-cl1-0:10.0.0.6;prod-cl1-1:10.0.0.7"</b> power_timeout=240 pcmk_reboot_timeout=900</code></pre>

### <a name="1-enable-the-use-of-a-stonith-device"></a>**[1]** STONITH-eszköz használatának engedélyezése

<pre><code>sudo pcs property set stonith-enabled=true
</code></pre>

## <a name="next-steps"></a>Következő lépések

* [Azure Virtual Machines az SAP tervezéséhez és megvalósításához][planning-guide]
* [Azure Virtual Machines üzembe helyezés az SAP-ban][deployment-guide]
* [Azure Virtual Machines adatbázis-kezelői telepítés az SAP-hoz][dbms-guide]
* A magas rendelkezésre állás és a SAP HANA Azure-beli virtuális gépeken történő vész-helyreállítási tervének megismeréséhez lásd: [Az Azure-beli SAP HANA magas rendelkezésre állása Virtual Machines (VM)][sap-hana-ha]
