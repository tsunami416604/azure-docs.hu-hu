---
title: Figyelési Azure tárolók áttekintés |} Microsoft Docs
description: Ez a cikk áttekintést nyújt a tárolókat az Azure gyors megérteni a fürtök állapotának és rendelkezésre állásának figyelése Azure-ban a különböző módszerekre.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2018
ms.author: magoedte
ms.openlocfilehash: 0d511c1f6dfd482e5754741da15b2852ee77c11e
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/04/2018
---
# <a name="overview-of-monitoring-containers-in-azure"></a>Az Azure-ban tárolók figyelése áttekintése
Az Azure-ral hatékonyan figyelheti és kezelheti a az Azure-tárolókon Kubernetes vagy Docker üzembe helyezett munkaterhelések tekintetében. Fontos tudni, hogyan működnek tárolók mikroszolgáltatási több alkalmazással rendelkező ahhoz, hogy egy megbízható szolgáltatás biztosításához léptékű, és támogatja a felügyeleti csomag. Ez a cikk röviden áttekinti a felügyeleti és figyelési képességek segítségével az Azure-ban minden egyes ismernie és megfelelő ezek a követelmények alapján.

Használatával [Azure figyelő tároló állapotát](monitoring-container-health.md), a teljesítmény és a Linux tároló-infrastruktúra állapotát egy pillanat alatt tekintheti meg és vizsgálja meg a problémák gyorsan. A telemetriai adatok tárolva van a Naplóelemzési munkaterület, és az Azure portálon, ahol megismerheti, integrált szűréséhez, és szegmens összevont adatok nyomon követheti a terhelés, teljesítmény és állapotát az irányítópultok.  

Az üzemeltetett Azure Kubernetes szolgáltatás, a Naplóelemzési kívül futó tárolókat [Windows és a Docker-tároló megoldás](../log-analytics/log-analytics-containers.md) megtekinthető és kezelhető a Windows és a Docker-tároló gazdagépek nyújt segítséget. A Naplóelemzési munkaterület megtekintheti leltár részleteit, a teljesítmény és a csomópontok és a tárolók származó események a környezetben. Tárolók használt parancsok bemutató részletes naplózási információk is megtekinthetők, és elháríthatja az tárolók megtekintésével és központosított naplók keresése anélkül, hogy a Docker vagy a Windows rendszerű távoli eléréséhez.

Körű vagy végpontok figyelés az alkalmazás eléréséhez bármely függőségi azt egy Azure vagy a helyi erőforrás, ellenőrizni kell az Azure-megfigyelőt vagy a Naplóelemzési.  Az alkalmazási rétegre ahhoz, hogy egy további tájékoztatáshoz állapotfigyelő mindkét szinten a platformot, és az alkalmazás használatával az Application Insights réteget tartalmaznia kell. A platform szintjén nincs Application Insights SDK-k a [Kubernetes]( https://github.com/Microsoft/ApplicationInsights-Kubernetes), [Docker](https://hub.docker.com/r/microsoft/applicationinsights/), és [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-appinsights). Mikroszolgáltatási alkalmazások esetén a rendszer támogatja a [Java](../application-insights/app-insights-java-get-started.md), [Node.js](../application-insights/app-insights-nodejs-quick-start.md), [.Net](../application-insights/app-insights-asp-net.md), [.Net Core](../application-insights/app-insights-asp-net-core.md), valamint egyéb számos[nyelvet vagy keretrendszert](../application-insights/app-insights-platforms.md). 

Ellenkező esetben problémák azonosítatlan kerül, amely jelentős hatással lehet az alkalmazás rendelkezésre állásának és a szolgáltatási szint célkitűzések nem teljesül.  
