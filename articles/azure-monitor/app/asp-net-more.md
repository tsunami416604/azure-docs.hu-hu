---
title: Hozzon ki többet az Azure Application Insightsból | Microsoft dokumentumok
description: Az Application Insights első lépései után az alábbiakban összefoglalhatja a feltárható funkciókat.
ms.topic: conceptual
ms.date: 02/03/2017
ms.openlocfilehash: 3c4a9a736f34d4f7dbfeb004d0837f1f2efa55dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77666024"
---
# <a name="more-telemetry-from-application-insights"></a>További telemetriai adatok az Application Insightsból
Miután [hozzáadta az Application Insightsot a ASP.NET kódhoz,](../../azure-monitor/app/asp-net.md)néhány dolgot tehet, hogy még több telemetriai adatokat kapjon. 

| Műveletek | Amihez jut|
|---|---|
|(IIS-kiszolgálók) [Telepítse az Állapotfigyelőt](https://go.microsoft.com/fwlink/?LinkId=506648) minden kiszolgálószámítógépre.<br/>(Azure-webalkalmazások) A webalkalmazás Azure vezérlőpultján nyissa meg az Application Insights panelt.| [**Teljesítményszámlálók**](../../azure-monitor/app/performance-counters.md)<br/>[**Kivételek**](asp-net-exceptions.md) - részletes veremnyomkövetések<br/>[**Függőségek**](../../azure-monitor/app/asp-net-dependencies.md)|
|[A JavaScript-kódrészlet hozzáadása a weblapokhoz](../../azure-monitor/app/javascript.md)|[Oldal teljesítménye](../../azure-monitor/app/usage-overview.md), böngésző kivételek, AJAX teljesítmény. Egyéni ügyféloldali telemetriai adatok.|
|[Rendelkezésre állási webes tesztek létrehozása](../../azure-monitor/app/monitor-web-app-availability.md)|Értesítések et kaphat, ha webhelye elérhetetlenné válik|
|[Győződjön meg róla, hogy a buildinfo.config-ot](https://msdn.microsoft.com/library/dn449058.aspx) az MSBuild hozza létre|[Jegyzetek készítése metrikadiagramokban](https://docs.microsoft.com/azure/azure-monitor/app/annotations)
|[Egyéni események és mutatók írása](../../azure-monitor/app/api-custom-events-metrics.md)|Megszámlálja az üzleti eseményeket és mutatókat, nyomon követheti a részletes használatot stb.|
|[Az élő webhely profilozása](https://aka.ms/AIProfilerPreview)|Részletes funkcióidőzítések az élő webalkalmazásból|






