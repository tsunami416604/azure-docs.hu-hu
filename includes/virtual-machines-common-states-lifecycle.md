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
ms.openlocfilehash: 603e7c3a0c30eb42cb75d6a6ff87a96d847b7c9f
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2018
ms.locfileid: "40100665"
---
Azure-beli virtuális gépek (VM) halad át, amely osztályozhatók állapotot *kiépítés* és *power* állapotok. Ez a cikk célja be ezeket az állapotokat, és kifejezetten kiemelés, ha az ügyfelek a számlázás például használat. 

## <a name="power-states"></a>Energiaállapotok

Az áramellátási állapot a virtuális gép az utolsó ismert állapotot jelöl.

![Virtuális gép power állapot diagramja](./media/virtual-machines-common-states-lifecycle/vm-power-states.png)

<br>
Az alábbi táblázat ismerteti, minden példány állapota, és azt jelzi, hogy azt alapján példány használata, vagy nem.

<table>
<tr>
<th>
Állapot
</th>
<th>
Leírás
</th>
<th>
Példány használati számlázás
</th>
</tr>
<tr>
<td>
<p><b>Indítása</b></p>
</td>
<td>
<p>Virtuális gép indítása folyamatban van.</p>
<code>"statuses": [<br>
   {<br>
      "code": "PowerState/starting",<br>
       "level": "Info",<br>
        "displayStatus": "VM starting"<br>
    }<br>
    ]</code><br>
</td>
<td>
<p><b>Nem számítunk fel díjat</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Fut</b></p>
</td>
<td>
<p>Virtuális gép normál működő állapotban</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/running",<br>
 "level": "Info",<br>
 "displayStatus": "VM running"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>A számlázás</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Leállítása</b></p>
</td>
<td>
<p>Ez csak átmeneti állapot. Amikor elkészült, állapotúként jelenik meg **leállítva**.</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/stopping",<br>
 "level": "Info",<br>
 "displayStatus": "VM stopping"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>A számlázás</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Leállítva</b></p>
</td>
<td>
<p>A virtuális gép lett állítva a vendég operációs rendszeren belül lefelé a, vagy a kikapcsolt API-k használatával.</p>
<p>Hardver még hozzá van rendelve a virtuális gép, és a gazdagép marad. </p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/stopped",<br>
 "level": "Info",<br>
 "displayStatus": "VM stopped"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Nem számítunk fel díjat&#42;</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Felszabadítása</b></p>
</td>
<td>
<p>Átmeneti állapot. Amikor elkészült, a virtuális gép jelenik meg: **Deallocated**.</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/deallocating",<br>
 "level": "Info",<br>
 "displayStatus": "VM deallocating"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Nem számítunk fel díjat&#42;</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Felszabadítása</b></p>
</td>
<td>
<p>A virtuális gép sikeresen leállítva, és a eltávolítása a gazdagépről. </p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/deallocated",<br>
 "level": "Info",<br>
 "displayStatus": "VM deallocated"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Nem számítunk fel díjat</b></p>
</td>
</tr>
</tbody>
</table>


&#42;Egyes Azure-erőforrások, például a lemezek és a hálózatkezelés, a példány állapota függetlenül díjkötelesek. 

## <a name="provisioning-states"></a>Kiépítési állapotok

Egy kiépítési állapota a felhasználó által kezdeményezett, a vezérlősík művelet a virtuális gép állapotát. Ezeket az állapotokat a virtuális gép energiaállapota elkülönülnek.

- **Hozzon létre** – a virtuális gép létrehozása.

- **Frissítés** – frissíti egy meglévő virtuális Gépet a modell. Néhány nem modell vált virtuális gép például Start/Restart frissítés is tartozik.

- **Törlés** – virtuális gép törlése.

- **Szabadítsa fel** – itt egy virtuális gép leállítása és eltávolítása a gazdagépről. Virtuális gép felszabadítása egy adott frissítés számít, így frissítésével kapcsolatos kiépítési állapotok fognak megjelenni.



A platform elfogadta a felhasználó által kezdeményezett művelet után az alábbiakban az átmeneti művelet állapotok:

<br>

<table>
<tbody>
<tr>
<td width="162">
<p><b>állapotok</b></p>
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
<p><b>Törlése</b></p>
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
<p><b>Az operációs rendszer kiépítési állapotok</b></p>
</td>
<td width="366">
<p>Ha egy virtuális gép létrehozása egy operációs rendszer lemezképe és nem a speciális rendszerképek, majd következő alállapottal figyelhető meg:</p>
<p>1. <b>OSProvisioningInprogress</b> &ndash; a virtuális gép fut, és a vendég operációs rendszer telepítése folyamatban van. <p /> 
<code> "statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating/OSProvisioningInprogress",<br>
 "level": "Info",<br>
 "displayStatus": "OS Provisioning In progress"<br>
 }<br>
]</code><br>
<p>2. <b>OSProvisioningComplete</b> &ndash; rövid ideig tartó állapota. A virtuális gép átkerül gyorsan **sikeres** , kivéve, ha bármely bővítményeket külön kell telepíteni. Bővítmények telepítése időt is igénybe vehet. <br />
<code> "statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating/OSProvisioningComplete",<br>
 "level": "Info",<br>
 "displayStatus": "OS Provisioning Complete"<br>
 }<br>
]</code><br>
<p><b>Megjegyzés:</b>: operációs rendszer telepítése váltáshoz **sikertelen** Ha egy operációs rendszer hibája, vagy az operációs rendszer telepítése nem időben. Ügyfeleink az infrastruktúra a központilag telepített virtuális gép díját felszámítjuk.</p>
</td>
</tr>
</table>


A művelet végrehajtása után a virtuális gép fognak váltani a következő állapotok valamelyikével:

- **Sikeres** – a felhasználó által kezdeményezett műveletek végrehajtása.

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

 

- **Nem sikerült** – a sikertelen műveletet jelöl. Tekintse meg a hibakódok további információkért és a lehetséges megoldásokról.

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



## <a name="vm-instance-view"></a>Virtuális gép példányait tartalmazó nézet

API példányait tartalmazó nézetet biztosít a virtuális gép fut-állapot információt. További információkért lásd: a [virtuálisgép - példányokat tartalmazó nézet](https://docs.microsoft.com/rest/api/compute/virtualmachines/instanceview) API dokumentációja.

Az Azure erőforrás-kezelő egy egyszerű felhasználói Felületet biztosít a virtuális gép futó állapotban megtekintése: [az erőforrás-kezelő] (https://resources.azure.com/).

Kiépítési állapotok a virtuális gép tulajdonságait, és példányait tartalmazó nézet látható. Virtuális gép példányait tartalmazó nézet energiaállapotok érhetők el. 

