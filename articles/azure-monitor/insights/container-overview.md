---
title: Az Azure tárolók monitorozásának áttekintése |} A Microsoft Docs
description: Ez a cikk áttekintést tárolók az Azure-ban könnyen felismerhető, a fürt állapotának és rendelkezésre állás monitorozása az Azure-ban elérhető különböző módszereket.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: magoedte
ms.openlocfilehash: 825c6a7dd0c0163a1fd298ca7510f2483a5df2ad
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54076518"
---
# <a name="overview-of-monitoring-containers-in-azure"></a>Az Azure-beli tárolók áttekintése
Az Azure-ban, hatékonyan monitorozni és kezelni a számítási feladatok Azure-tárolók Kubernetes vagy a Docker telepítve. Fontos tudni, hogyan tárolók, mikroszolgáltatások több alkalmazással rendelkező végez annak érdekében, hogy egy megbízható szolgáltatás biztosításához ipari méretekben, és a figyelési csomag támogatja. Ez a cikk röviden áttekinti az a felügyeleti és monitorozási képességeket nyújt az Azure segítségével megismerheti azokat, és amelyek megfelelőek az igényei alapján.

Használatával [-tárolókhoz az Azure Monitor](container-insights-overview.md), a teljesítmény és a Linux tároló-infrastruktúra állapotát megtekintheti egy pillantással és gyorsan problémák kivizsgálásában. A telemetriai adatokat a Log Analytics-munkaterületen tárolja, és szűrése az Azure Portalon, ahol megismerheti, integrált és szegmens összesített adatok az irányítópultok a terhelés, teljesítmény és állapotának figyeléséhez.  

Az üzemeltetett Azure-beli Kubernetes-szolgáltatás, a Log Analytics-on kívül futó tárolók [Windows- és Docker-tároló megoldás](../../azure-monitor/insights/containers.md) megtekinthető és kezelhető a Windows és a Docker-tároló gazdagépek segít. A Log Analytics-munkaterületen, megtekintheti a környezet leltáradatait, teljesítmény és a csomópontok és a tárolók eseményeket. Részletes naplózási adatait megjelenítő parancsok használják tárolókhoz is megtekintheti, és a tárolók háríthatóak el megtekintésével és központosított naplók keresése távolról elérni a Docker és a Windows gazdagépekre nélkül.

Átfogó, vagy – teljes körű figyelést az alkalmazás elérése érdekében minden olyan függőséget, az Azure vagy a helyszíni erőforráshoz, hogy ellenőrizni kell az Azure Monitor vagy a Log Analytics.  Az alkalmazási rétegre tartalmaznia kell egy további rétegét állapotának figyelése, az Application Insights használatával platform és az alkalmazás szintjén egyaránt hozzáadásához. A platform szintjén vannak az Application Insights SDK-k [Kubernetes]( https://github.com/Microsoft/ApplicationInsights-Kubernetes), [Docker](https://hub.docker.com/r/microsoft/applicationinsights/), és [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-appinsights). Mikroszolgáltatás-alkalmazások esetén nincs támogatása [Java](../../azure-monitor/app/java-get-started.md), [Node.js](../../azure-monitor/learn/nodejs-quick-start.md), [.Net](../../azure-monitor/app/asp-net.md), [.Net Core](../../azure-monitor/app/asp-net-core.md), valamint számos más [nyelvek és keretrendszerek](../../azure-monitor/app/platforms.md). 

Ellenkező esetben problémák azonosítatlan kerül, amely hatással lehet az alkalmazás rendelkezésre állásának és a szolgáltatási szintű célok nem teljesül.  
