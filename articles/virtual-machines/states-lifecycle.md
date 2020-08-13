---
title: Az Azure-beli virtuális gépek életciklusa és állapota
description: Az Azure-beli virtuális gépek életciklusának áttekintése, beleértve azon állapotok leírását, amelyeket a virtuális gép bármikor tartalmazhat.
services: virtual-machines
author: shandilvarun
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 08/09/2018
ms.author: vashan
ms.openlocfilehash: 127604264850f9845846d0bb6a2768cac23cdc8c
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2020
ms.locfileid: "88169134"
---
# <a name="virtual-machines-lifecycle-and-states"></a>Virtuális gépek életciklusa és állapota

Az Azure Virtual Machines (VM-EK) különböző állapotokon haladnak át, amelyek beoszthatók *kiépítés* és *energiaellátási* állapotba. Ennek a cikknek a célja, hogy leírja ezeket az állapotokat, és kiemelje, hogy mikor kell kiszámítani az ügyfeleket a példányok használatáért. 

## <a name="power-states"></a>Energiaállapotok

A Power állapot a virtuális gép utolsó ismert állapotát jelöli.

![Virtuális gép energiagazdálkodási állapotának diagramja](./media/vm-power-states.png)

<br>
A következő táblázat ismerteti az egyes példányok állapotát, és azt jelzi, hogy a példány használatban van-e, vagy sem.

<table>
<tr>
<th>
Állam
</th>
<th>
Leírás
</th>
<th>
Példány használati számlázása
</th>
</tr>
<tr>
<td>
<p><b>Indítás</b></p>
</td>
<td>
<p>A virtuális gép elindul.</p>
<code>"statuses": [<br>
   {<br>
      "code": "PowerState/starting",<br>
       "level": "Info",<br>
        "displayStatus": "VM starting"<br>
    }<br>
    ]</code><br>
</td>
<td>
<p><b>Nem számlázott</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Futó</b></p>
</td>
<td>
<p>Virtuális gép normál működésének állapota</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/running",<br>
 "level": "Info",<br>
 "displayStatus": "VM running"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Számlázása</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Leállítás</b></p>
</td>
<td>
<p>Ez egy átmeneti állapot. Ha elkészült, a rendszer **Leállítottként**jeleníti meg.</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/stopping",<br>
 "level": "Info",<br>
 "displayStatus": "VM stopping"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Számlázása</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Leállítva</b></p>
</td>
<td>
<p>A virtuális gép le lett állítva a vendég operációs rendszerből, vagy az erő API-k használatával.</p>
<p>A hardver továbbra is a virtuális gép számára van lefoglalva, és a gazdagépen marad. </p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/stopped",<br>
 "level": "Info",<br>
 "displayStatus": "VM stopped"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Számlázott&#42;</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Felszabadítás</b></p>
</td>
<td>
<p>Átmeneti állapot. Ha elkészült, a virtuális gép fel lesz **töltve.**</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/deallocating",<br>
 "level": "Info",<br>
 "displayStatus": "VM deallocating"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Nem számlázott&#42;</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Felszabadítva</b></p>
</td>
<td>
<p>A virtuális gép sikeresen leállt, és el lett távolítva a gazdagépről. </p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/deallocated",<br>
 "level": "Info",<br>
 "displayStatus": "VM deallocated"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Nem számlázott</b></p>
</td>
</tr>
</tbody>
</table>


Bizonyos Azure-erőforrások, például a lemezek és a hálózatkezelés &#42;ért díjat számítunk fel. A példányon lévő szoftverlicenc-licencek nem számítanak fel díjat.

## <a name="provisioning-states"></a>Kiépítési állapotok

A kiépítési állapot a felhasználó által kezdeményezett, vezérlő-sík művelet állapota a virtuális gépen. Ezek az állapotok eltérhetnek a virtuális gép energiagazdálkodási állapotától.

- **Létrehozás** – virtuális gép létrehozása.

- **Update** – frissíti egy meglévő virtuális gép modelljét. Néhány nem Modelles változás a virtuális gépen, például az indítás/újraindítás alatt is a frissítés alatt marad.

- **Delete** – virtuális gép törlése.

- **Felszabadítás** – a virtuális gép leállítása és eltávolítása a gazdagépről. A virtuális gép felszabadítása frissítésnek minősül, ezért a frissítéshez kapcsolódó kiépítési állapotokat fogja megjeleníteni.



Az alábbi átmeneti művelet azt jelzi, hogy a platform elfogadta a felhasználó által kezdeményezett műveletet:

<br>

<table>
<tbody>
<tr>
<td width="162">
<p><b>Állapotok</b></p>
</td>
<td width="366">
<p>Leírás</p>
</td>
</tr>
<tr>
<td width="162">
<p><b>Létrehozás</b></p>
</td>
<td width="366">
<code>"statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating",<br>
 "level": "Info",<br>
 "displayStatus": "Creating"<br>
 }</code><br>
</td>
</tr>
<tr>
<td width="162">
<p><b>Frissítése</b></p>
</td>
<td width="366">
<code>"statuses": [<br>
 {<br>
 "code": "ProvisioningState/updating",<br>
 "level": "Info",<br>
 "displayStatus": "Updating"<br>
 }<br>
 ]</code><br>
</td>
</tr>
<tr>
<td width="162">
<p><b>Törlés</b></p>
</td>
<td width="366">
<code>"statuses": [<br>
 {<br>
 "code": "ProvisioningState/deleting",<br>
 "level": "Info",<br>
 "displayStatus": "Deleting"<br>
 }<br>
 ]</code><br>
</td>
</tr>
<tr>
<td width="162">
<p><b>OPERÁCIÓSRENDSZER-kiépítési állapotok</b></p>
</td>
<td width="366">
<p>Ha egy virtuális gépet operációs rendszer rendszerképével hoztak létre, és nem speciális képpel, akkor a következő alállapotok figyelhetők meg:</p>
<p>1. <b>OSProvisioningInprogress</b> &ndash; A virtuális gép fut, és a vendég operációs rendszer telepítése folyamatban van. <p /> 
<code> "statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating/OSProvisioningInprogress",<br>
 "level": "Info",<br>
 "displayStatus": "OS Provisioning In progress"<br>
 }<br>
]</code><br>
<p>2. <b>OSProvisioningComplete</b> &ndash; Rövid életű állapot. A virtuális gép gyorsan átvált a **sikerre** , kivéve, ha a bővítményeket telepíteni kell. A bővítmények telepítése időt vehet igénybe. <br />
<code> "statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating/OSProvisioningComplete",<br>
 "level": "Info",<br>
 "displayStatus": "OS Provisioning Complete"<br>
 }<br>
]</code><br>
<p><b>Megjegyzés</b>: az operációs rendszer üzembe helyezése **sikertelen** állapotra vált, ha az operációs rendszer meghibásodása miatt nem sikerül időben telepíteni az operációs rendszert. Az ügyfeleket az infrastruktúrában az üzembe helyezett virtuális gép számlázása alapján számítjuk fel.</p>
</td>
</tr>
</table>


A művelet befejezését követően a virtuális gép a következő állapotok egyikére vált:

- **Sikeres** – a felhasználó által kezdeményezett műveletek befejeződtek.

    ```
  "statuses": [ 
  {
     "code": "ProvisioningState/succeeded",
     "level": "Info",
     "displayStatus": "Provisioning succeeded",
     "time": "time"
  }
  ]
    ```

 

- **Sikertelen** – sikertelen műveletet jelöl. További információért és lehetséges megoldásokhoz tekintse meg a hibakódokat.

    ```
  "statuses": [
    {
      "code": "ProvisioningState/failed/InternalOperationError",
      "level": "Error",
      "displayStatus": "Provisioning failed",
      "message": "Operation abandoned due to internal error. Please try again later.",
      "time": "time"
    }
    ]
    ```



## <a name="vm-instance-view"></a>Virtuálisgép-példány nézet

A példány nézet API a virtuális gép futtatásával kapcsolatos információkat biztosít. További információkért tekintse meg a [Virtual Machines-példány nézet](https://docs.microsoft.com/rest/api/compute/virtualmachines/instanceview) API dokumentációját.

Az Azure Resources Explorer egyszerű KEZELŐFELÜLETet biztosít a virtuális gép futási állapotának megtekintéséhez: [erőforrás-kezelő](https://resources.azure.com/).

A kiépítési állapotok a virtuális gép tulajdonságai és a példány nézetében láthatók. A Power állapotok a virtuális gép példány nézetében érhetők el.

## <a name="next-steps"></a>További lépések

További információ a virtuális gép figyeléséről: [Virtual Machines in Azure (virtuális gépek figyelése az Azure-ban](../azure-monitor/insights/monitor-vm-azure.md)).