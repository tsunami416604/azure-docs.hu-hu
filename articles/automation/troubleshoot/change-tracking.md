---
title: Az Azure Change Tracking hibáinak elhárítása
description: Ez a cikk a hibaelhárítással kapcsolatos információkat tartalmaz Change Tracking
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: bobbytreed
ms.author: robreed
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: beb0b89bdbf143c89a83c0813313a8bbda7235d4
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564854"
---
# <a name="troubleshoot-change-tracking-and-inventory"></a>A Change Tracking és az Inventory hibaelhárítása

## <a name="windows"></a>Windows

### <a name="records-not-showing-windows"></a>Forgatókönyv Change Tracking a rekordok nem jelennek meg a Windows rendszerű gépeken

#### <a name="issue"></a>Probléma

Change Tracking számára készült Windows-gépekhez nem jelenik meg leltár-vagy Change Tracking-találat.

#### <a name="cause"></a>Ok

Ezt a hibát a következő okok okozhatják:

1. A **Microsoft monitoring Agent** nem fut
2. A rendszer blokkolja az Automation-fiókkal való kommunikációt.
3. A Change Tracking felügyeleti csomagjai nem tölthetők le.
4. Előfordulhat, hogy a bevezetésben lévő virtuális gép olyan klónozott gépről származott, amely nem volt telepítve a Microsoft monitoring Agent Sysprep használatával létrehozott.

#### <a name="resolution"></a>Megoldás:

1. Ellenőrizze, hogy a számítógépen fut-e a **Microsoft monitoring Agent** (HealthService. exe).
1. Ellenőrizze **Eseménynapló** a gépen, és keresse meg azokat az eseményeket, amelyekben `changetracking` szó van bennük.
1. Látogasson el a [Network Planning](../automation-hybrid-runbook-worker.md#network-planning) webhelyre, ahol megtudhatja, mely címeket és portokat kell engedélyezni a Change Tracking működéséhez.
1. Győződjön meg arról, hogy a következő Change Tracking és leltár-felügyeleti csomagok helyileg léteznek:
    * Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*
    * Microsoft.IntelligencePacks.InventoryChangeTracking.*
    * Microsoft.IntelligencePacks.SingletonInventoryCollection.*
1. Klónozott rendszerkép használata esetén először a rendszerképet kell telepíteni, és a Microsoft monitoring Agent ügynököt a tény után kell telepíteni.

Ha ezek a megoldások nem oldják meg a problémát, és felvesszük a kapcsolatot a támogatási szolgálattal, a következő parancsok futtatásával összegyűjtheti a diagnosztikai adatokat az ügynökön.

Az ügynök gépen navigáljon `C:\Program Files\Microsoft Monitoring Agent\Agent\Tools` a következő parancsokra, és futtassa az alábbi parancsokat:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

> [!NOTE]
> Alapértelmezés szerint a hibák nyomon követése engedélyezve van, ha az előző példához hasonlóan szeretné engedélyezni a részletes hibaüzeneteket `VER` , használja a paramétert. Az információs nyomkövetésekhez használja `INF` a meghívásakor. `StartTracing.cmd`

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Az [Azure fórumain](https://azure.microsoft.com/support/forums/) Azure-szakértőktől kaphat válaszokat.
* Az [@AzureSupport](https://twitter.com/azuresupport) a Microsoft Azure hivatalos Twitter-fiókja, amelyen keresztül a jobb felhasználói élmény érdekében igyekszünk az Azure-felhasználók közösségét ellátni a megfelelő forrásokkal: válaszokkal, támogatással és szakértői segítséggel.
* Ha további segítségre van szüksége, egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási](https://azure.microsoft.com/support/options/) webhelyét, és válassza a **támogatás kérése**lehetőséget.
