---
title: A Red Hat Enterprise Linux az Azure-ban támasztja beállítása |} A Microsoft Docs
description: A Red Hat Enterprise Linux az Azure-ban támasztja beállítása
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
ms.date: 08/17/2018
ms.author: sedusch
ms.openlocfilehash: b844c93a1f3e83d682b51db6f9854f11b24d82e7
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59543735"
---
# <a name="setting-up-pacemaker-on-red-hat-enterprise-linux-in-azure"></a>A Red Hat Enterprise Linux az Azure-ban támasztja beállítása

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

[virtual-machines-linux-maintenance]:../../linux/maintenance-and-updates.md#maintenance-not-requiring-a-reboot

> [!NOTE]
> Red Hat Enterprise Linux-alapú támasztja az Azure-időkorlát ügynök használatával fence egy fürt csomópontja, ha szükséges. Feladatátvétel akár 15 percig is eltarthat, ha egy erőforrás leállítása sikertelen, vagy a fürt csomópontjai nem tud kommunikálni amely egymáshoz többé. További információkért olvassa el [RHEL magas rendelkezésre állású fürt tagként futtató Azure virtuális gép lehet az elkülönített nagyon hosszú időt igénybe, vagy az elkerítés sikertelen / időtúllépés következik be a virtuális gép leállítása előtt](https://access.redhat.com/solutions/3408711)

Először olvassa el az alábbi SAP-megjegyzések és tanulmányok:

* SAP-Jegyzetnek [1928533], amely rendelkezik:
  * A lista SAP szoftver központi telepítése által támogatott Azure-beli Virtuálisgép-méretek.
  * Fontos kapacitási adatainak Azure Virtuálisgép-méretet.
  * Támogatott SAP-szoftverek és operációs rendszer (OS) és adatbázis-kombinációk.
  * Szükséges SAP kernel verziója a Windows és Linux a Microsoft Azure-ban.
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
* [SAP HANA rendszerreplikáció támasztja fürtben](https://access.redhat.com/articles/3004101)
* Általános RHEL-dokumentáció
  * [Magas rendelkezésre állású bővítmény áttekintése](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Magas rendelkezésre állású kiegészítő felügyeleti](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Magas rendelkezésre állású bővítmény referencia](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
* Az Azure egyedi RHEL dokumentációja:
  * [RHEL magas rendelkezésre állású fürtöket – Microsoft Azure virtuális gépek a fürt tagjai terméktámogatási irányelveinek](https://access.redhat.com/articles/3131341)
  * [Telepítése és a Microsoft Azure egy Red Hat Enterprise Linux 7.4-es (és újabb verziók) magas rendelkezésre állású fürt konfigurálása](https://access.redhat.com/articles/3252491)

## <a name="cluster-installation"></a>Fürt telepítése

![Támasztja a RHEL áttekintése](./media/high-availability-guide-rhel-pacemaker/pacemaker-rhel.png)

A következő elemek van fűzve előtagként vagy **[A]** – az összes csomópont alkalmazandó **[1]** – 1. csomópont csak érvényes vagy **: [2]** – 2. csomópont csak érvényes.

1. **[A]**  Regisztrálása

   A virtuális gépek regisztrálását, és mellékelje egy készletet, amely tartalmazza a tárházak RHEL 7.

   <pre><code>sudo subscription-manager register
   # List the available pools
   sudo subscription-manager list --available --matches '*SAP*'
   sudo subscription-manager attach --pool=&lt;pool id&gt;
   </code></pre>

   Vegye figyelembe, hogy egy készletet egy Azure piactér PAYG RHEL-lemezkép csatolásával, fog hatékonyan a dupla számlázás az RHEL-használat: egyszer a Használatalapú lemezképet, valamint a RHEL jogosultság a készlet csatlakoztatása után. A hiba elhárítása érdekében az Azure mostantól saját RHEL lemezképet biztosít. További információk érhetők el [Itt](https://aka.ms/rhel-byos).

1. **[A]**  Engedélyezése RHEL for SAP-adattárakkal

   Annak érdekében, hogy a szükséges csomagok telepítéséhez a következő tárházak engedélyezése.

   <pre><code>sudo subscription-manager repos --disable "*"
   sudo subscription-manager repos --enable=rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable="rhel-sap-for-rhel-7-server-rpms"
   </code></pre>

1. **[A]**  RHEL magas RENDELKEZÉSREÁLLÁSI bővítmény telepítése

   <pre><code>sudo yum install -y pcs pacemaker fence-agents-azure-arm nmap-ncat
   </code></pre>

1. **[A]**  Állomásnév-feloldás beállítása

   DNS-kiszolgálót használjon, vagy módosítsa a Hosts az összes csomópontra. Ez a példa bemutatja, hogyan használhatja a Hosts fájlt.
   Cserélje le az IP-cím és az állomásnevet, az alábbi parancsokban. A Hosts használatával előnye, hogy a fürt válik független a DNS, amely túl lehet egyetlen pont, a hibák.

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Helyezze be a következő sorokat Hosts. Módosítsa az IP-cím és a környezet megfelelő állomásnév

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[A]**  Ugyanazt a jelszót hacluster jelszó módosítása

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]**  Támasztja vonatkozó tűzfalszabályok hozzáadása

   Adja hozzá a következő tűzfalszabályokat az összes fürt kommunikációs a fürt csomópontjai között.

   <pre><code>sudo firewall-cmd --add-service=high-availability --permanent
   sudo firewall-cmd --add-service=high-availability
   </code></pre>

1. **[A]**  Alapszintű fürt szolgáltatások engedélyezése

   Futtassa az alábbi parancsokat a támasztja szolgáltatás engedélyezését, és indítsa el.

   <pre><code>sudo systemctl start pcsd.service
   sudo systemctl enable pcsd.service
   </code></pre>

1. **[1]**  Támasztja létrehozása fürtben

   Futtassa az alábbi parancsokat a csomópontok hitelesíteni, és a fürt létrehozásához. A token beállítása, hogy a karbantartás megőrzése memória 30000. További információkért lásd: [Ez a cikk a Linux-][virtual-machines-linux-maintenance].

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

1. **[A]** Set Expected Votes

   <pre><code>sudo pcs quorum expected-votes 2
   </code></pre>

## <a name="create-stonith-device"></a>STONITH eszköz létrehozása

A STONITH eszköz hitelesítéséhez, szemben a Microsoft Azure egy egyszerű szolgáltatást használja. Kövesse az alábbi lépéseket egy szolgáltatásnév létrehozásához.

1. Nyissa meg a következőt: <https://portal.azure.com>
1. Nyissa meg az Azure Active Directory panelen válassza a tulajdonságok és írja le a címtár-azonosító. Ez a **bérlőazonosító**.
1. Kattintson az alkalmazásregisztrációk
1. Kattintson az Add (Hozzáadás) parancsra
1. Adjon meg egy nevet, válassza ki a "Web app/API" alkalmazástípus, adja meg a bejelentkezési URL-címet (például a http:\//localhost), és kattintson a Létrehozás gombra
1. A bejelentkezési URL-címet nem használja, és bármilyen érvényes URL-cím lehet
1. Válassza ki az új alkalmazást, és a beállítások lapon kattintson a kulcsok
1. Adja meg egy új kulcs leírását, válassza a "Soha nem jár le", és kattintson a Mentés gombra
1. Jegyezze fel az értéket. Használatban van a **jelszó** a Szolgáltatásnévhez
1. Jegyezze fel az alkalmazás azonosítóját. A felhasználónév használatban van (**bejelentkezési azonosító** az alábbi lépéseket a) a szolgáltatásnév

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]**  Az időkorlát-ügynökhöz tartozó egyéni szerepkör létrehozása

Az egyszerű szolgáltatás nem rendelkezik engedélyekkel alapértelmezés szerint az Azure-erőforrások eléréséhez. Elindíthatja és leállíthatja a szolgáltatásnév-engedélyt kell (szabadítsa fel) a fürt összes virtuális gépet. Ha nem hozott már létre az egyéni szerepkör, létrehozhat használatával [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell) vagy [Azure CLI-vel](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)

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

### <a name="a-assign-the-custom-role-to-the-service-principal"></a>**[A]**  Az egyéni szerepkör hozzárendelése a Szolgáltatásnévhez

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

### <a name="1-create-the-stonith-devices"></a>**[1]**  A STONITH eszközök létrehozása

Miután szerkesztette az engedélyek a virtuális gépek, konfigurálhatja úgy a STONITH eszközöket a fürtben.

<pre><code>
sudo pcs property set stonith-timeout=900
</code></pre>

A következő paranccsal konfigurálhatja az időkorlát eszközt.

> [!NOTE]
> A kapcsoló "pcmk_host_map" csak szükséges a parancsot, ha az RHEL állomásneveket és az Azure-csomópont neve nem azonos. Tekintse meg a parancsot a félkövérrel szedett szakaszában.

<pre><code>sudo pcs stonith create rsc_st_azure fence_azure_arm login="<b>login ID</b>" passwd="<b>password</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" subscriptionId="<b>subscription id</b>" <b>pcmk_host_map="prod-cl1-0:10.0.0.6;prod-cl1-1:10.0.0.7"</b> power_timeout=240 pcmk_reboot_timeout=900</code></pre>

### <a name="1-enable-the-use-of-a-stonith-device"></a>**[1]**  STONITH eszköz használatának engedélyezése

<pre><code>sudo pcs property set stonith-enabled=true
</code></pre>

## <a name="next-steps"></a>További lépések

* [Az Azure virtuális gépek tervezése és megvalósítása SAP][planning-guide]
* [Az SAP az Azure virtuális gépek üzembe helyezése][deployment-guide]
* [Az SAP az Azure Virtual Machines DBMS üzembe helyezése][dbms-guide]
* Magas rendelkezésre állást és az Azure virtuális gépeken SAP Hana vész-helyreállítási terv létrehozásához, lásd: [magas rendelkezésre állás az SAP HANA Azure-beli virtuális gépeken (VM)][sap-hana-ha]
