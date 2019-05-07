---
title: 'Az Azure állapota figyelő v2 API-leírások: Tiltsa le a kialakítási motor |} A Microsoft Docs'
description: Állapot figyelő v2 API referencia Disable-InstrumentationEngine. A webhely újbóli üzembe helyezése nélkül webhely teljesítményének megfigyeléséhez. A helyszíni, valamint a virtuális gépeken, illetve az Azure-ban üzemeltetett ASP.NET-webappokhoz is használható.
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
ms.openlocfilehash: 7d1b658da59c0e778c82301077ef27bdd9c9e614
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65144907"
---
# <a name="status-monitor-v2-api-disable-instrumentationengine-v021-alpha"></a>A figyelő v2 API állapota: Disable-InstrumentationEngine (v0.2.1-alpha)

Ez a dokumentum ismerteti, amely tartalmazza a szükséges, amelynek a parancsmag a [Az.ApplicationMonitor PowerShell-modul](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Állapot figyelő v2 jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információkért lásd: [kiegészítő használati feltételek a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="description"></a>Leírás

Ez a parancsmag letiltja a rendszerállapot-motor bizonyos beállításkulcsok eltávolítása.
Indítsa újra az IIS a módosítások érvénybe léptetéséhez.

> [!IMPORTANT] 
> Ez a parancsmag egy PowerShell-munkamenetet rendszergazdai jogosultság szükséges.

## <a name="examples"></a>Példák

```powershell
PS C:\> Disable-InstrumentationEngine
```

## <a name="parameters"></a>Paraméterek 

### <a name="-verbose"></a>-Verbose
**Általános paraméter.** Használja ezt a kapcsolót a kimenetben a részletes naplókat.

## <a name="output"></a>Kimenet


#### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>Sikerült letiltani a rendszerállapot-motor példa a kimenetre

```
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```


## <a name="next-steps"></a>További lépések

 Hozzon ki még többet Állapotfigyelőt v2:
 - Használja az útmutatóban [hibaelhárítás](status-monitor-v2-troubleshoot.md) Állapotfigyelőt v2.
