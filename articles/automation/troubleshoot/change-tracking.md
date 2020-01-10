---
title: Az Azure Change Tracking hibáinak elhárítása
description: Megtudhatja, hogyan lehet elhárítani a Azure Automation Change Tracking és a leltár szolgáltatással kapcsolatos problémákat.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 51a9dbf8be6538534c05a4b8b6fcd913ef8c6ae3
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769931"
---
# <a name="troubleshoot-change-tracking-and-inventory"></a>A Change Tracking és az Inventory hibaelhárítása

## <a name="windows"></a>Windows

### <a name="records-not-showing-windows"></a>Forgatókönyv: Change Tracking a rekordok nem jelennek meg a Windows rendszerű gépeken

#### <a name="issue"></a>Probléma

Change Tracking számára készült Windows-gépekhez nem jelenik meg leltár-vagy Change Tracking-találat.

#### <a name="cause"></a>Ok

Ezt a hibát a következő okok okozhatják:

1. A **Microsoft monitoring Agent** nem fut
2. A rendszer blokkolja az Automation-fiókkal való kommunikációt.
3. A Change Tracking felügyeleti csomagjai nem tölthetők le.
4. Előfordulhat, hogy a bevezetésben lévő virtuális gép olyan klónozott gépről származott, amely nem volt telepítve a Microsoft monitoring Agent Sysprep használatával létrehozott.

#### <a name="resolution"></a>Felbontás

1. Ellenőrizze, hogy a számítógépen fut-e a **Microsoft monitoring Agent** (HealthService. exe).
1. Ellenőrizze **Eseménynapló** a gépen, és keresse meg azokat az eseményeket, amelyekben a szó `changetracking` szerepel bennük.
1. Látogasson el a [Network Planning](../automation-hybrid-runbook-worker.md#network-planning) webhelyre, ahol megtudhatja, mely címeket és portokat kell engedélyezni a Change Tracking működéséhez.
1. Győződjön meg arról, hogy a következő Change Tracking és leltár-felügyeleti csomagok helyileg léteznek:
    * Microsoft. IntelligencePacks. ChangeTrackingDirectAgent. *
    * Microsoft. IntelligencePacks. InventoryChangeTracking. *
    * Microsoft. IntelligencePacks. SingletonInventoryCollection. *
1. Klónozott rendszerkép használata esetén először a rendszerképet kell telepíteni, és a Microsoft monitoring Agent ügynököt a tény után kell telepíteni.

Ha ezek a megoldások nem oldják meg a problémát, és felvesszük a kapcsolatot a támogatási szolgálattal, a következő parancsok futtatásával összegyűjtheti a diagnosztikai adatokat az ügynökön.

Az ügynök gépen navigáljon a `C:\Program Files\Microsoft Monitoring Agent\Agent\Tools`ra, és futtassa a következő parancsokat:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

> [!NOTE]
> Alapértelmezés szerint a hibák nyomon követése engedélyezve van, ha az előző példához hasonlóan szeretné engedélyezni a részletes hibaüzeneteket, használja a `VER` paramétert. Az információs nyomkövetéshez használja a `INF` a `StartTracing.cmd`meghívásakor.

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Az [Azure fórumain](https://azure.microsoft.com/support/forums/) Azure-szakértőktől kaphat válaszokat.
* Az [@AzureSupport](https://twitter.com/azuresupport) a Microsoft Azure hivatalos Twitter-fiókja, amelyen keresztül a jobb felhasználói élmény érdekében igyekszünk az Azure-felhasználók közösségét ellátni a megfelelő forrásokkal: válaszokkal, támogatással és szakértői segítséggel.
* Ha további segítségre van szüksége, egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a **támogatás kérése**lehetőséget.
