---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: shandilvarun
ms.service: virtual-machines
ms.topic: include
ms.date: 08/09/2018
ms.author: vashan, cynthn, rajsqr
ms.custom: include file
ms.openlocfilehash: 57f557a812ec5e4eea75b76ca1394ca360a85d30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67179058"
---
Az Azure virtuális gépek (VM-ek) különböző állapotokon megy keresztül, amelyek *kiépítésbe* és *energiagazdálkodási* állapotokba sorolhatók. A cikk célja, hogy leírja ezeket az állapotokat, és különösen kiemeli, ha a vevők nek például a használat ért. 

## <a name="power-states"></a>Energiaállapotok

Az energiaállapot a virtuális gép utolsó ismert állapotát jelöli.

![Virtuális gép energiaállapot-diagramja](./media/virtual-machines-common-states-lifecycle/vm-power-states.png)

<br>
Az alábbi táblázat az egyes példányállapotok leírását tartalmazza, és azt jelzi, hogy a rendszer kiszámlázta-e a példány használatát.

<table>
<tr>
<th>
Állapot
</th>
<th>
Leírás
</th>
<th>
Példányhasználati számlázás
</th>
</tr>
<tr>
<td>
<p><b>Kezdő</b></p>
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
<p><b>Nincs számlázva</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Fut</b></p>
</td>
<td>
<p>Normál munkaállapot egy virtuális géphez</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/running",<br>
 "level": "Info",<br>
 "displayStatus": "VM running"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Számlázott</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Leállítás</b></p>
</td>
<td>
<p>Ez egy átmeneti állapot. Ha elkészült, akkor megjelenik a **Leállítva**.</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/stopping",<br>
 "level": "Info",<br>
 "displayStatus": "VM stopping"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Számlázott</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Leállítva</b></p>
</td>
<td>
<p>A virtuális gép leállt a vendég operációs rendszeren belülről vagy a PowerOff API-k használatával.</p>
<p>A hardver továbbra is le van foglalva a virtuális gép, és továbbra is a gazdagépen marad. </p>
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
<p>Átmeneti állapot. Ha elkészült, a virtuális gép **felszabadítottként**jelenik meg.</p>
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
<p>A virtuális gép sikeresen leállt, és eltávolították a gazdagép. </p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/deallocated",<br>
 "level": "Info",<br>
 "displayStatus": "VM deallocated"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Nincs számlázva</b></p>
</td>
</tr>
</tbody>
</table>


&#42;egyes Azure-erőforrások, például a lemezek és a hálózatkezelés, díjakat vonnak maga után. A példányon található szoftverlicencek nem terhelnek díjat.

## <a name="provisioning-states"></a>Kiépítési állapotok

A kiépítési állapot a felhasználó által kezdeményezett, vezérlő-sík művelet állapota a virtuális gépen. Ezek az állapotok elkülönülnek a virtuális gép energiaállapotától.

- **Hozzon létre** – virtuális gép létrehozása.

- **Frissítés** – frissíti a modell egy meglévő virtuális gép. A virtuális gép néhány nem modelles módosítása, például a Start/Restart szintén frissítés alá esik.

- **Törlés** – Virtuális gép törlése.

- **Felszabadítás –** ahol a virtuális gép leáll, és eltávolítja a gazdagép. A virtuális gép üzembe helyezése frissítésnek minősül, így a frissítéshez kapcsolódó kiépítési állapotokat jelenít meg.



Az átmeneti művelet állapotai a platform által kezdeményezett művelet elfogadása után találhatók:

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
<p><b>Operációs rendszer kiépítési állapotai</b></p>
</td>
<td width="366">
<p>Ha egy virtuális gép operációs rendszerképpel jön létre, és nem egy speciális lemezképpel, akkor a következő alállapotok figyelhetők meg:</p>
<p>1. <b>OSProvisioningInprogress</b> &ndash; A virtuális gép fut, és a vendég operációs rendszer telepítése folyamatban van. <p /> 
<code> "statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating/OSProvisioningInprogress",<br>
 "level": "Info",<br>
 "displayStatus": "OS Provisioning In progress"<br>
 }<br>
]</code><br>
<p>2. <b>OSProvisioningComplete</b> &ndash; rövid életű állapot. A virtuális gép gyorsan áttér a **Sikeres,** kivéve, ha bármilyen bővítményt kell telepíteni. A bővítmények telepítése időbe telhet. <br />
<code> "statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating/OSProvisioningComplete",<br>
 "level": "Info",<br>
 "displayStatus": "OS Provisioning Complete"<br>
 }<br>
]</code><br>
<p><b>Megjegyzés:</b>Az operációs rendszer kiépítése **átválthat sikertelenre,** ha operációs rendszer hibás, vagy az operációs rendszer nem települ időben. Az ügyfelek nek fizetnie kell az infrastruktúra üzembe helyezett virtuális gépért.</p>
</td>
</tr>
</table>


A művelet befejezése után a virtuális gép a következő állapotok egyikébe vált:

- **Sikeres –** a felhasználó által kezdeményezett műveletek befejeződtek.

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

 

- **Sikertelen** – sikertelen műveletet jelöl. További információkért és lehetséges megoldásokért tekintse meg a hibakódokat.

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



## <a name="vm-instance-view"></a>Virtuálisgép-példány nézete

A példánynézet API virtuális gép futásállapot-információkat biztosít. További információt a [Virtuális gépek – Példánynézet](https://docs.microsoft.com/rest/api/compute/virtualmachines/instanceview) API dokumentációjában talál.

Az Azure Resources Explorer egy egyszerű felhasználói felületet biztosít a virtuális gép futó állapotának megtekintéséhez: [Erőforrás-kezelő](https://resources.azure.com/).

A kiépítési állapotok láthatók a virtuális gép tulajdonságai és a példánynézetben. A teljesítményállapotok a virtuális gép példánynézetében érhetők el. 

