---
title: 'Az Azure állapota figyelő v2 API-leírások: Konfiguráció beolvasása |} A Microsoft Docs'
description: Állapot figyelő v2 API referencia Get-ApplicationInsightsMonitoringConfig. A webhely újbóli üzembe helyezése nélkül webhely teljesítményének megfigyeléséhez. A helyszíni, valamint a virtuális gépeken, illetve az Azure-ban üzemeltetett ASP.NET-webappokhoz is használható.
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
ms.openlocfilehash: 0e6b47c9b629aed28fa217cb6299edb57423fc6f
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870477"
---
# <a name="status-monitor-v2-api-get-applicationinsightsmonitoringconfig-v021-alpha"></a>A figyelő v2 API állapota: Get-ApplicationInsightsMonitoringConfig (v0.2.1-alpha)

Ez a dokumentum ismerteti, amely tartalmazza a szükséges, amelynek a parancsmag a [Az.ApplicationMonitor PowerShell-modul](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Állapot figyelő v2 jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információkért lásd: [kiegészítő használati feltételek a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="description"></a>Leírás

A konfigurációs fájl beolvasása, és nyomtassa ki az értékeket a konzolhoz.

> [!IMPORTANT] 
> Ez a parancsmag egy PowerShell-munkamenetet rendszergazdai jogosultság szükséges.

## <a name="examples"></a>Példák

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

## <a name="parameters"></a>Paraméterek 

(Nem kötelező paraméterek)

## <a name="output"></a>Kimenet


#### <a name="example-output-from-reading-the-config-file"></a>A konfigurációs fájl olvasásakor. a példa a kimenetre

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```
