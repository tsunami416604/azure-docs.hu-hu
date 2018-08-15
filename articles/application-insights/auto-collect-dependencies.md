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
ms.devlang: multiple
ms.topic: reference
ms.date: 08/13/2018
ms.reviewer: mbullwin
ms.author: nimolnar
ms.openlocfilehash: f20963f030c9040b696f7d6a33b25bcee2dc517f
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2018
ms.locfileid: "40130290"
---
# <a name="dependency-auto-collection"></a>Függőség az automatikus gyűjtés

Alább látható, amely automatikusan észlelhetők a függőségek anélkül, hogy az alkalmazás kódjában további megvédhetők függőségi hívások jelenleg támogatott listája. Ez a kimenő hívások való kommunikációra való kódtárak, a storage-ügyfelek, a naplózás és a metrikák kódtárak, valamint alkalmazás-keretrendszerek és a kiszolgálók beérkező hívások áll. A függőségek jelenik meg az Application Insights [alkalmazástérkép](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) és [tranzakció diagnosztikája](https://docs.microsoft.com/azure/application-insights/app-insights-transaction-diagnostics) nézeteket. A függőség nem a lenti listában, ha továbbra is nyomon követheti azt manuálisan egy [függőségi hívás nyomon](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

## <a name="net"></a>.NET

| Alkalmazás-keretrendszerek| Verziók |
| ------------------------|----------|
| ASP.NET-fejlesztő | 4.5 + |
| ASP.NET MVC | 4 + |
| Az ASP.NET WebAPI | 4.5 + |
| ASP.NET Core | 1.1 + |
| <b> Kommunikációs könyvtárak</b> |
| [HttpClient](https://www.microsoft.com/net/) | 4.5 +, .NET core 1.1 + |
| [SqlClient](https://www.nuget.org/packages/System.Data.SqlClient) | A .NET core 1.0-s +, NuGet 4.3.0 verzióban |
| [EventHubs-Client SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 1.1.0 |
| [A ServiceBus ügyféloldali SDK-val](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) | 3.0.0 |
| <b>A Storage-ügyfelek</b>|  |
| ADO.NET | 4.5 + |
| <b>Naplózás kódtárak</b> |  |
| ILogger | 1.1 + |
| System.Diagnostics.Trace | 4.5 + |
| [nLog](https://www.nuget.org/packages/NLog/) | 4.4.12+ |
| [log4net](https://www.nuget.org/packages/log4net/) | a NetStandard 1.3-as, a .NET 4.5-ös + 2.0.6+ 2.0.8+ |

## <a name="java"></a>Java
| Alkalmazás-kiszolgálók | Verziók |
|-------------|----------|
| [Tomcat](https://tomcat.apache.org/) | 7, 8 | 
| [A JBoss EAP](https://developers.redhat.com/products/eap/download/) | 6, 7 |
| [Jetty](http://www.eclipse.org/jetty/) | 9 |
| <b>Alkalmazás-keretrendszerek </b> |  |
| [Spring](https://spring.io/) | 3.0 |
| [Spring Boot](https://spring.io/projects/spring-boot) | 1.5.9+<sup>*</sup> |
| A Java Servlet | 3.1 + |
| <b>Kommunikációs könyvtárak</b> |  |
| [Az Apache Http-ügyfelének létrehozása](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) | 4.3 +<sup>†</sup> |
| <b>A Storage-ügyfelek</b> | |
| [SQL Server]( https://mvnrepository.com/artifact/com.microsoft.sqlserver/mssql-jdbc) | 1 +<sup>†</sup> |
| [Oracle]( http://www.oracle.com/technetwork/database/application-development/jdbc/downloads/index.html) | 1 +<sup>†</sup> |
| [MySql]( https://mvnrepository.com/artifact/mysql/mysql-connector-java) | 1 +<sup>†</sup> |
| <b>Naplózás kódtárak</b> | |
| [Logback](https://logback.qos.ch/) | 1 + |
| [Log4j](https://logging.apache.org/log4j/) | 1.2 + |
| <b>Metrikák kódtárak</b> |  |
| JMX | 1.0-s + |

> [!NOTE]
> * Kivételével a reaktív jellegű olyan programozási támogatást.
> <br>†Requires telepítésének [JVM ügynök](https://docs.microsoft.com/azure/application-insights/app-insights-java-agent#install-the-application-insights-agent-for-java).

## <a name="nodejs"></a>Node.js

| Kommunikációs könyvtárak | Verziók |
| ------------------------|----------|
| [HTTP](https://nodejs.org/api/http.html), [HTTPS](https://nodejs.org/api/https.html) | 0.10-ás + |
| <b>A Storage-ügyfelek</b> | |
| [Redis](https://www.npmjs.com/package/redis) | 2.x |
| [MongoDb](https://www.npmjs.com/package/mongodb); [MongoDb Core](https://www.npmjs.com/package/mongodb-core) | 2.0.0-s - 2.3.0-át |
| [MySQL](https://www.npmjs.com/package/mysql) | 2.0.0-s - 2.14.x |
| [PostgreSql](https://www.npmjs.com/package/pg); | 6.x |
| [PG-készlet](https://www.npmjs.com/package/pg-pool) | 1.x |
| <b>Naplózás kódtárak</b> | |
| [Konzol](https://nodejs.org/api/console.html) | 0.10-ás + |
| [Bunyan](https://www.npmjs.com/package/bunyan) | 1.x |
| [Winston](https://www.npmjs.com/package/winston) | 2.x |

## <a name="javascript"></a>JavaScript

| Kommunikációs könyvtárak | Verziók |
| ------------------------|----------|
| [XMLHttpRequest](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) | Mind |

## <a name="next-steps"></a>További lépések

- Állítsa be a nyomon követése az egyéni függőség [.NET](app-insights-asp-net-dependencies.md).
- Állítsa be a nyomon követése az egyéni függőség [Java](app-insights-java-agent.md).
- [A függőségi egyéni telemetriát írhat](app-insights-api-custom-events-metrics.md#trackdependency)
- Lásd: [adatmodell](application-insights-data-model.md) Application Insights és modellhez.
- Tekintse meg [platformok](app-insights-platforms.md) Application Insights által támogatott.
