---
title: Virtuális gépek eltávolítása Azure Automation Change Tracking és leltárból
description: Ez a cikk azt ismerteti, Hogyan távolítható el a virtuális gépek a Change Trackingról és a leltárból.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 23dfad844c17b0b8c8a35b6a94d6a96327afe19c
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84117456"
---
# <a name="remove-vms-from-change-tracking-and-inventory"></a>Virtuális gépek eltávolítása a Change Trackingből és az Inventoryból

Amikor befejezte a környezetben lévő virtuális gépek változásainak telepítését, eltávolíthatja őket a [change Tracking és a leltár](change-tracking.md) szolgáltatásból.

1. Az Automation-fiókban válassza a **változások követése** vagy **leltározás** elemet a **konfiguráció**felügyelete alatt.

2. A következő paranccsal azonosíthatja egy olyan virtuális gép UUID-azonosítóját, amelyet el szeretne távolítani a felügyelet alól.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. Az **általános**területen lévő log Analytics munkaterületen nyissa meg a hatókör-konfiguráció mentett kereséseit `MicrosoftDefaultScopeConfig-ChangeTracking` .

4. A mentett kereséshez `MicrosoftDefaultComputerGroup` kattintson a jobb oldalon található három pontra, majd válassza a **Szerkesztés**lehetőséget. 

5. Távolítsa el a virtuális gép UUID-azonosítóját.

6. Ismételje meg a lépéseket minden más virtuális gép eltávolításához.

7. Mentse a mentett keresést a Szerkesztés befejezése után. 

## <a name="next-steps"></a>További lépések

* A Change Tracking és a leltár használatának folytatásához tekintse meg a [change Tracking és a leltár kezelése](change-tracking-file-contents.md)című témakört.
* Az általános szolgáltatással kapcsolatos problémák megoldásához lásd: [change Tracking és leltározási problémák elhárítása](troubleshoot/change-tracking.md).