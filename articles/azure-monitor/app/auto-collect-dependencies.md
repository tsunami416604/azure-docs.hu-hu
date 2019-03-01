---
title: Az Azure Application Insights – függőségi az automatikus gyűjtés |} A Microsoft Docs
description: Az Application Insights automatikusan gyűjtheti és függőségek vizualizálása
services: application-insights
documentationcenter: .net
author: nikmd23
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: reference
ms.date: 08/13/2018
ms.reviewer: mbullwin
ms.author: nimolnar
ms.openlocfilehash: 7f152de89f5d374a5b97368eafe7287fc9377291
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2019
ms.locfileid: "57011330"
---
# <a name="dependency-auto-collection"></a>Függőségek automatikus összegyűjtése

Alább látható, amely automatikusan észlelhetők a függőségek anélkül, hogy az alkalmazás kódjában további megvédhetők függőségi hívások jelenleg támogatott listája. Ez a kimenő hívások való kommunikációra való kódtárak, a storage-ügyfelek, a naplózás és a metrikák kódtárak, valamint alkalmazás-keretrendszerek és a kiszolgálók beérkező hívások áll. A függőségek jelenik meg az Application Insights [alkalmazástérkép](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) és [tranzakció diagnosztikája](https://docs.microsoft.com/azure/application-insights/app-insights-transaction-diagnostics) nézeteket. A függőség nem a lenti listában, ha továbbra is nyomon követheti azt manuálisan egy [függőségi hívás nyomon](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

## <a name="net"></a>.NET

| Alkalmazás-keretrendszerek| Verziók |
| ------------------------|----------|
| ASP.NET Webforms | 4.5+ |
| ASP.NET, MVC | 4+ |
| ASP.NET WebAPI | 4.5+ |
| ASP.NET-mag | 1.1+ |
| <b> Kommunikációs könyvtárak</b> |
| [HttpClient](https://www.microsoft.com/net/) | 4.5+, .NET Core 1.1+ |
| [SqlClient](https://www.nuget.org/packages/System.Data.SqlClient) | A .NET core 1.0-s +, NuGet 4.3.0 verzióban |
| [EventHubs Client SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 1.1.0 |
| [ServiceBus Client SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) | 3.0.0 |
| <b>A Storage-ügyfelek</b>|  |
| ADO.NET | 4.5+ |
| <b>Naplózás kódtárak</b> |  |
| ILogger | 1.1+ |
| System.Diagnostics.Trace | 4.5+ |
| [nLog](https://www.nuget.org/packages/NLog/) | 4.4.12+ |
| [log4net](https://www.nuget.org/packages/log4net/) | a NetStandard 1.3-as, a .NET 4.5-ös + 2.0.6+ 2.0.8+ |

## <a name="java"></a>Java
| Alkalmazás-kiszolgálók | Verziók |
|-------------|----------|
| [Tomcat](https://tomcat.apache.org/) | 7, 8 | 
| [JBoss EAP](https://developers.redhat.com/products/eap/download/) | 6, 7 |
| [Jetty](https://www.eclipse.org/jetty/) | 9 |
| <b>Alkalmazás-keretrendszerek </b> |  |
| [Spring](https://spring.io/) | 3.0 |
| [Spring Boot](https://spring.io/projects/spring-boot) | 1.5.9+<sup>*</sup> |
| Java Servlet | 3.1+ |
| <b>Kommunikációs könyvtárak</b> |  |
| [Az Apache Http-ügyfelének létrehozása](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) | 4.3+<sup>†</sup> |
| <b>A Storage-ügyfelek</b> | |
| [SQL Server]( https://mvnrepository.com/artifact/com.microsoft.sqlserver/mssql-jdbc) | 1+<sup>†</sup> |
| [Oracle]( https://www.oracle.com/technetwork/database/application-development/jdbc/downloads/index.html) | 1+<sup>†</sup> |
| [MySql]( https://mvnrepository.com/artifact/mysql/mysql-connector-java) | 1+<sup>†</sup> |
| <b>Naplózás kódtárak</b> | |
| [Logback](https://logback.qos.ch/) | 1+ |
| [Log4j](https://logging.apache.org/log4j/) | 1.2+ |
| <b>Metrikák kódtárak</b> |  |
| JMX | 1.0+ |

> [!NOTE]
> * Kivételével a reaktív jellegű olyan programozási támogatást.
> <br>†Requires telepítésének [JVM ügynök](https://docs.microsoft.com/azure/application-insights/app-insights-java-agent#install-the-application-insights-agent-for-java).

## <a name="nodejs"></a>Node.js

| Kommunikációs könyvtárak | Verziók |
| ------------------------|----------|
| [HTTP](https://nodejs.org/api/http.html), [HTTPS](https://nodejs.org/api/https.html) | 0.10+ |
| <b>A Storage-ügyfelek</b> | |
| [Redis](https://www.npmjs.com/package/redis) | 2.x |
| [MongoDb](https://www.npmjs.com/package/mongodb); [MongoDb Core](https://www.npmjs.com/package/mongodb-core) | 2.x - 3.x |
| [MySQL](https://www.npmjs.com/package/mysql) | 2.0.0 - 2.16.x |
| [PostgreSql](https://www.npmjs.com/package/pg); | 6.x - 7.x |
| [pg-pool](https://www.npmjs.com/package/pg-pool) | 1.x - 2.x |
| <b>Naplózás kódtárak</b> | |
| [Konzol](https://nodejs.org/api/console.html) | 0.10+ |
| [Bunyan](https://www.npmjs.com/package/bunyan) | 1.x |
| [Winston](https://www.npmjs.com/package/winston) | 2.x - 3.x |

## <a name="javascript"></a>JavaScript

| Kommunikációs könyvtárak | Verziók |
| ------------------------|----------|
| [XMLHttpRequest](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) | Összes |

## <a name="next-steps"></a>További lépések

- Állítsa be a nyomon követése az egyéni függőség [.NET](../../azure-monitor/app/asp-net-dependencies.md).
- Állítsa be a nyomon követése az egyéni függőség [Java](../../azure-monitor/app/java-agent.md).
- [A függőségi egyéni telemetriát írhat](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)
- Lásd: [adatmodell](../../azure-monitor/app/data-model.md) Application Insights és modellhez.
- Tekintse meg [platformok](../../azure-monitor/app/platforms.md) Application Insights által támogatott.
