---
title: Az Azure Application Insights – az Azure Functions támogatott funkciók |} A Microsoft Docs
description: Az Application Insights az Azure Functions támogatott szolgáltatások
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: ''
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: reference
ms.date: 10/05/2018
ms.reviewer: mbullwin
ms.author: tilee
ms.openlocfilehash: 0f4eaaefb7d2080218e19574621a4962e61057c3
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49394306"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>Application Insights az Azure Functions támogatott szolgáltatások

Azure Functions-ajánlatok [beépített integráció](https://docs.microsoft.com/azure/azure-functions/functions-monitoring) az Application insights szolgáltatással, amely a ILogger felületen keresztül érhető el. Az alábbi, a jelenleg támogatott funkciók listáját. Tekintse át az Azure Functions útmutatója [bevezetés](https://github.com/Azure/Azure-Functions/wiki/App-Insights).

## <a name="supported-features"></a>Támogatott szolgáltatások

| Azure Functions                       | 1. verzió                | V2 (az Ignite 2018.)  | 
|-----------------------------------    |---------------    |------------------ |
| **Az Application Insights .NET SDK-val**   | **2.5.0**       | **2.7.2**         |
| | | | 
| **Automatikus gyűjteménye**        |                 |                   |               
| &bull; Kérelmek                     | Igen             | Igen               | 
| &bull; Kivételek                   | Igen             | Igen               | 
| &bull; Függőségek                   |                   |                   |               
| &nbsp;&nbsp;&nbsp;&mdash; HTTP      |                 | Igen               | 
| &nbsp;&nbsp;&nbsp;&mdash; Szolgáltatásbusz|                 | Igen               | 
| &nbsp;&nbsp;&nbsp;&mdash; az eventHub  |                 | Igen               | 
| &nbsp;&nbsp;&nbsp;&mdash; AZ SQL       |                 | Igen               | 
| | | | 
| **Támogatott szolgáltatások**                |                   |                   |               
| &bull; QuickPulse/LiveMetrics       | Igen             | Igen               | 
| &bull; Mintavétel                     | Igen             | Igen               | 
| &bull; A szívverések                   |                 | Igen               | 
| | | | 
| **Korreláció**                       |                   |                   |               
| &bull; Szolgáltatásbusz                     |                   | Igen               | 
| &bull; az eventHub                       |                   | Igen               | 
| | | | 
| **Konfigurálható**                      |                   |                   |           
| &bull;Teljes mértékben konfigurálhatók.<br/>Lásd: [Azure Functions](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852) útmutatást.<br/>Lásd: [Asp.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) összes beállításának.               |                   | Igen                   | 


## <a name="sampling"></a>Mintavételezés

Az Azure Functions lehetővé teszi a mintavétel a konfigurálásukban alapértelmezés szerint. További információkért lásd: [mintavétel konfigurálása](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling).
