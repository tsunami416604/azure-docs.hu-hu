---
title: Az Azure Application Insights Agent API-referenciája
description: Application Insights Agent API-referenciája. Disable-InstrumentationEngine. Webhelyek teljesítményének figyelése a webhely újbóli üzembe helyezése nélkül. Együttműködik a helyszínen, a virtuális gépeken vagy az Azure-on üzemeltetett ASP.NET Web Apps szolgáltatásokkal.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: dafa12db95a10df467bf0d042cfd9395720648b2
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671375"
---
# <a name="application-insights-agent-api-disable-instrumentationengine"></a>Application Insights ügynök API: disable-InstrumentationEngine

Ez a cikk olyan parancsmagot ismertet, amely az az [. ApplicationMonitor PowerShell-modul](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)tagja.

## <a name="description"></a>Leírás
Letiltja a rendszerállapot-kezelő motort néhány beállításkulcs eltávolításával.
A módosítások érvénybe léptetéséhez indítsa újra az IIS-t.

> [!IMPORTANT] 
> Ehhez a parancsmaghoz rendszergazdai engedélyekkel rendelkező PowerShell-munkamenet szükséges.

## <a name="examples"></a>Példák

```powershell
PS C:\> Disable-InstrumentationEngine
```

## <a name="parameters"></a>Paraméterek 

### <a name="-verbose"></a>-Részletes
**Általános paraméter.** Ezt a kapcsolót használja a részletes naplók kinyomtatásához.

## <a name="output"></a>Kimenet


#### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>Példa a Instrumentation-motor sikeres letiltásának kimenetére

```
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```


## <a name="next-steps"></a>Következő lépések

 Több Application Insights-ügynökkel:
 - Az útmutató segítségével Application Insights-ügynököt lehet [elhárítani](status-monitor-v2-troubleshoot.md) .
