---
title: 'Az Azure Állapotfigyelőt v2 API-leírások: Tiltsa le a figyelés |} A Microsoft Docs'
description: Állapot figyelő v2 API-hivatkozás. Disable-ApplicationInsightsMonitoring. A webhely újbóli üzembe helyezése nélkül webhely teljesítményének megfigyeléséhez. ASP.NET-webalkalmazásokat együttműködik a helyileg üzemeltetett, a virtuális gépek vagy az Azure-ban.
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: 98171692284f5f28fb189e2658d207fb80d428ba
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807143"
---
# <a name="status-monitor-v2-api-disable-applicationinsightsmonitoring-v040-alpha"></a>A figyelő v2 API állapota: Disable-ApplicationInsightsMonitoring (v0.4.0-alpha)

Ez a cikk ismerteti, amely tagja a parancsmag a [Az.ApplicationMonitor PowerShell-modul](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Állapot figyelő v2 jelenleg nyilvános előzetes verzióban érhető el.
> Ez az előnézeti verzió egy szolgáltatásiszint-megállapodás nélkül biztosított, és ezt nem javasoljuk a termelési számítási feladatokhoz. Előfordulhat, hogy néhány funkció nem támogatott, és néhány előfordulhat, hogy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="description"></a>Leírás

Letiltja a figyelést a célszámítógépen.
Ez a parancsmag eltávolítja az IIS applicationHost.config módosításainak és beállításkulcsok eltávolítása.

> [!IMPORTANT] 
> Ez a parancsmag megköveteli egy PowerShell-munkamenetet rendszergazdai jogosultságokkal rendelkezik.

## <a name="examples"></a>Példák

```powershell
PS C:\> Disable-ApplicationInsightsMonitoring
```

## <a name="parameters"></a>Paraméterek 

### <a name="-verbose"></a>-Verbose
**Általános paraméter.** Ez a kapcsoló használatával jeleníti meg a részletes naplókat.

## <a name="output"></a>Output


#### <a name="example-output-from-successfully-disabling-monitoring"></a>Sikerült letiltani a figyelést a példa a kimenetre

```
Initiating Disable Process
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-00z'
in :1,237
No element in the source document matches '/configuration/location[@path='']/system.webServer/modules/add[@name='ManagedHttpModuleHelper']'
Not executing RemoveAll (transform line 1, 546)
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'disable'
GAC Module will not be removed, since this operation might cause IIS instabilities
Configuring IIS Environment for codeless attach...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring IIS Environment for instrumentation engine...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring registry for instrumentation engine...
Successfully disabled Application Insights Status Monitor
```


## <a name="next-steps"></a>További lépések

 Hozzon ki még többet Állapotfigyelőt v2:
 - Használja az útmutatóban [hibaelhárítása](status-monitor-v2-troubleshoot.md) Állapotfigyelőt v2.
