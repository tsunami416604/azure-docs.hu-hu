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
ms.openlocfilehash: 05958f35f80a53da27e020d367799519ef5a9bd7
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2018
ms.locfileid: "48901583"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>Application Insights az Azure Functions támogatott szolgáltatások

Az alábbi, az a funkciók a jelenleg támogatott listája a [Application Insights integrációja az Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-monitoring). Tekintse át az Azure Functions útmutatója [bevezetés](https://github.com/Azure/Azure-Functions/wiki/App-Insights).


| Azure Functions                       | 1. verzió                | V2 (az Ignite 2018.)  | 
|-----------------------------------    |---------------    |------------------ |
| **Az Application Insights .NET SDK-val**         | **2.5.0**             | **2.7.2**                 |
| | | | 
| **Automatikus gyűjteménye**              |                   |                   |               
| &bull; Kérelmek                           | Igen               | Igen               | 
| &bull; Kivételek                         | Igen               | Igen               | 
| &bull; Függőségek               |                   |                   |               
| &mdash; HTTP                              |                   | Igen               | 
| &mdash; Szolgáltatásbusz                        |                   | Igen               | 
| &mdash; az eventHub                          |                   | Igen               | 
| &mdash; AZ SQL                               |                   | Igen               | 
| | | | 
| **Támogatott szolgáltatások**                    |                   |                   |               
| &bull; QuickPulse/LiveMetrics                         | Igen               | Igen               | 
| &bull; Mintavétel                           | Igen               | Igen               | 
| &bull; A szívverések                         |       | Igen               | 
| | | | 
| **Korreláció**                           |                   |                   |               
| &bull; Szolgáltatásbusz                         |                   | Igen               | 
| &bull; az eventHub                           |                   | Igen               | 
| | | | 
| **Konfigurálható**                  |                   |                   |           
| &bull;Teljes mértékben konfigurálhatók.<br/>Lásd: [Azure Functions](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852) útmutatást.<br/>Lásd: [Asp.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) összes beállításának.               |                   | Igen                   | 
