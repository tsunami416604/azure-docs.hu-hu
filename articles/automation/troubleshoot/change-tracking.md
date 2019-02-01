---
title: Az Azure Change Tracking kapcsolatos hibák elhárítása
description: Ez a cikk információkat a Change Tracking hibaelhárítása
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: georgewallace
ms.author: gwallace
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2a6610b5cb3f01fc70b1737fc4492e09d9a7637b
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55507334"
---
# <a name="troubleshoot-change-tracking-and-inventory"></a>A Change Tracking és Inventory hibaelhárítása

## <a name="windows"></a>Windows

### <a name="records-not-showing-windows"></a>Forgatókönyv: Változáskövetési rekordok nem jelennek meg a Windows-gépeken

#### <a name="issue"></a>Probléma

Windows-gépek, amelyek előkészíteni a Change Tracking megoldásba készlet vagy a Change Tracking eredményt nem jelenik meg.

#### <a name="cause"></a>Ok

Ez a hiba oka lehet a következő okok miatt:

1. A **Microsoft Monitoring Agent** nem fut.
2. Térjen vissza az Automation-fiók kommunikációja blokkolva van folyamatban.
3. A felügyeleti csomagokat a Change Tracking megoldásba nincsenek letöltve.
4. A virtuális gép típusától függenek előfordulhat, hogy a klónozott gép, amelynek nem a Microsoft Monitoring Agent telepítése a Sysprep használatával előkészített kell származnia.

#### <a name="resolution"></a>Megoldás:

1. Ellenőrizze a **Microsoft Monitoring Agent** (HealthService.exe) fut a gépen.
1. Ellenőrizze **Eseménynapló** a gépre, és keresse a más eseményeket, amelyek szó szerepel `changetracking` bennük.
1. Látogasson el, [hálózattervezés](../automation-hybrid-runbook-worker.md#network-planning) további információt arról, hogy mely címeket és portokat engedélyezni kell, a Change Tracking megoldásba működjön.
1. Győződjön meg arról, hogy a Change Tracking and Inventory felügyeleti csomagokat létezik helyileg:
    * Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*
    * Microsoft.IntelligencePacks.InventoryChangeTracking.*
    * Microsoft.IntelligencePacks.SingletonInventoryCollection.*
1. Ha egy klónozott lemezképét, a sysprep lemezkép használatával először, és a Microsoft Monitoring Agent ügynök telepítése után az a tény.

Ha ezek a megoldások nem sikerül megoldani a problémát, és kapcsolatba lép a támogatással, futtathatja az alábbi parancsokat a diagnosztikai gyűjtése az ügynökön

Lépjen az ügynökgépen futó `C:\Program Files\Microsoft Monitoring Agent\Agent\Tools` , és futtassa a következő parancsokat:

```cmd
set stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

> [!NOTE]
> Alapértelmezett hibaüzenet szerint nyomkövetés engedélyezve van, ha engedélyezi a részletes hibaüzeneteket, például az előző példában használja `VER` paraméter. A nyomkövetési adatokat, használja `INF` meghívásakor `StartTracing.cmd`.

## <a name="next-steps"></a>További lépések

Ha nem jelenik meg a problémát, vagy nem lehet megoldani a problémát, látogasson el a következő csatornák további támogatás:

* Az [Azure fórumain](https://azure.microsoft.com/support/forums/) Azure-szakértőktől kaphat válaszokat.
* Az [@AzureSupport](https://twitter.com/azuresupport) a Microsoft Azure hivatalos Twitter-fiókja, amelyen keresztül a jobb felhasználói élmény érdekében igyekszünk az Azure-felhasználók közösségét ellátni a megfelelő forrásokkal: válaszokkal, támogatással és szakértői segítséggel.
* Ha további segítségre van szüksége, akkor is fájl egy Azure-támogatási esemény. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) válassza **támogatja az első**.
