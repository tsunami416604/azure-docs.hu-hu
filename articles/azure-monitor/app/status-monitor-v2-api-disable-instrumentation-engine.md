---
title: Az Azure Application Insights Ügynök API-hivatkozása
description: Application Insights Ügynök API-referencia. Disable-InstrumentationEngine. A webhely teljesítményének figyelése a webhely újratelepítése nélkül. Együttműködik ASP.NET helyszíni, virtuális gépeken vagy az Azure-ban üzemeltetett webalkalmazásokkal.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: dafa12db95a10df467bf0d042cfd9395720648b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671375"
---
# <a name="application-insights-agent-api-disable-instrumentationengine"></a>Application Insights-ügynök API: Disable-InstrumentationEngine

Ez a cikk az [Az.ApplicationMonitor PowerShell modul](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)tagjaként készült parancsmalapot ismerteti.

## <a name="description"></a>Leírás
Letiltja a műszerezési motort néhány beállításkulcs eltávolításával.
A módosítások érvénybe léptetéséhez indítsa újra az IIS-t.

> [!IMPORTANT] 
> Ehhez a parancsmaghoz rendszergazdai engedélyekkel rendelkező PowerShell-munkamenet szükséges.

## <a name="examples"></a>Példák

```powershell
PS C:\> Disable-InstrumentationEngine
```

## <a name="parameters"></a>Paraméterek 

### <a name="-verbose"></a>-Részletes
**Közös paraméter.** Ezzel a kapcsolóval részletes naplókat kell kiadnia.

## <a name="output"></a>Kimenet


#### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>Példa a műszermotor sikeres letiltásából származó kimenetre

```
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```


## <a name="next-steps"></a>További lépések

 További információk az Application Insights-ügynökkel:
 - Útmutatónk segítségével [elháríthatja](status-monitor-v2-troubleshoot.md) az Application Insights-ügynök hibáit.
