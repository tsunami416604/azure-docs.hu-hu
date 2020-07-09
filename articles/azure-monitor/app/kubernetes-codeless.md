---
title: Alkalmazások figyelése az Azure Kubernetes szolgáltatásban (ak) Application Insights-Azure Monitorsal | Microsoft Docs
description: A Azure Monitor zökkenőmentesen integrálható az Kubernetes-on futó alkalmazással, és lehetővé teszi, hogy az alkalmazásaival kapcsolatos problémák ne legyenek idő alatt.
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/13/2020
ms.openlocfilehash: 76f9f922697ef6be6c959ea7f9bafd0872dba6ff
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "83773756"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes---azure-monitor-application-insights"></a>Zero Instrumentation-alkalmazás figyelése a Kubernetes-Azure Monitor Application Insights

> [!IMPORTANT]
>  A Kubernetes-on futó Java-alkalmazások figyelését jelenleg a kód kihelyezése nélkül engedélyezheti – használja a [Java önálló ügynököt](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent). Habár az alkalmazások figyelésének zökkenőmentes engedélyezése a megoldás más nyelveken is működik, az SDK-k segítségével figyelheti az AK-on futó alkalmazásokat: [ASP.net Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core), [ASP.net](https://docs.microsoft.com/azure/azure-monitor/app/asp-net), [Node.js](https://docs.microsoft.com/azure/azure-monitor/app/nodejs), [JavaScript](https://docs.microsoft.com/azure/azure-monitor/app/javascript)és [Python](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python).

## <a name="application-monitoring-without-instrumenting-the-code"></a>Alkalmazások figyelése a kód beszerelése nélkül
Jelenleg csak a Java lehetővé teszi, hogy az alkalmazás figyelését a kód kivezetése nélkül engedélyezze. Más nyelveken futó alkalmazások figyelése az SDK-k használatával. 

## <a name="java"></a>Java
Ha engedélyezve van, a Java-ügynök automatikusan gyűjt számos kérést, függőséget, naplót és metrikát a legszélesebb körben használt könyvtárakból és keretrendszerekből.

A Kubernetes-alkalmazásokban, valamint más környezetekben futó Java-alkalmazások figyeléséhez kövesse [a részletes útmutatást](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent) . 

## <a name="other-languages"></a>További nyelvek

Az SDK-k használata esetén jelenleg a következő nyelveken javasolt az alkalmazások használata:
* [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)
* [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)
* [Node.js](https://docs.microsoft.com/azure/azure-monitor/app/nodejs) 
* [JavaScript](https://docs.microsoft.com/azure/azure-monitor/app/javascript)
* [Python](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python)

## <a name="next-steps"></a>További lépések

* További információ a [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview) és a [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)
* Tekintse át az [elosztott nyomkövetést](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing) , és tekintse meg, milyen [alkalmazás-hozzárendelést](https://docs.microsoft.com/azure/azure-monitor/app/app-map?tabs=net) végezhet a vállalata számára