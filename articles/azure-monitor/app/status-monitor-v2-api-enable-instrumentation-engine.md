---
title: 'Az Azure állapota figyelő v2 API-leírások: A rendszerállapot-motor |} A Microsoft Docs'
description: Állapot figyelő v2 API referencia engedélyezése – InstrumentationEngine. A webhely újbóli üzembe helyezése nélkül webhely teljesítményének megfigyeléséhez. A helyszíni, valamint a virtuális gépeken, illetve az Azure-ban üzemeltetett ASP.NET-webappokhoz is használható.
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
ms.openlocfilehash: 7d337c9b6b22f3abfcb4aea1c47127706ed9e9d7
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870507"
---
# <a name="status-monitor-v2-api-enable-instrumentationengine-v021-alpha"></a>A figyelő v2 API állapota: Enable-InstrumentationEngine (v0.2.1-alpha)

Ez a dokumentum ismerteti, amely tartalmazza a szükséges, amelynek a parancsmag a [Az.ApplicationMonitor PowerShell-modul](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Állapot figyelő v2 jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információkért lásd: [kiegészítő használati feltételek a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="description"></a>Leírás

Ez a parancsmag a rendszerállapot-motor engedélyezi bizonyos beállításkulcsok beállításával.
Indítsa újra az IIS a módosítások érvénybe léptetéséhez.

A rendszerállapot-motor egészítheti ki a .NET SDK-k által gyűjtött adatokat.
Események és az üzenetek gyűjtendő, amelyek a felügyelt folyamat végrehajtását ismertetik. Beleértve többek között a függőségi eredménykódok, HTTP-műveletek és SQL-parancsszöveg. 

A rendszerállapot-motor engedélyezze, ha:
- Már engedélyezve van a figyelést az engedélyezése a parancsmag használatával, de nem engedélyezte a InstrumentationEngine.
- A .NET SDK-k segítségével manuálisan úgy alakította, és szeretné, hogy további telemetriai funkciók.

> [!IMPORTANT] 
> Ez a parancsmag egy PowerShell-munkamenetet rendszergazdai jogosultság szükséges.

> [!NOTE] 
> Ez a parancsmag kell, hogy tekintse át és fogadja el a licenc és az adatvédelmi nyilatkozatot.

> [!NOTE] 
> A rendszerállapot-motor hozzáadja a további erőforrásokra, és alapértelmezés szerint ki van kapcsolva.

## <a name="examples"></a>Példák

```powershell
PS C:\> Enable-InstrumentationEngine
```

## <a name="parameters"></a>Paraméterek 

### <a name="-acceptlicense"></a>-AcceptLicense
**Nem kötelező.** Használja ezt a kapcsolót a licenc és az adatvédelmi nyilatkozatot távfelügyelt telepítés fogadására.

### <a name="-verbose"></a>-Verbose
**Általános paraméter.** Használja ezt a kapcsolót a kimenetben a részletes naplókat.

## <a name="output"></a>Kimenet


#### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>Példa a kimenetre való sikeres engedélyezése a rendszerállapot-motor

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
```
