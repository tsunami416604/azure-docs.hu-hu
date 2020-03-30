---
title: Azure Application Insights – Függőségautomatikus gyűjtés | Microsoft dokumentumok
description: Az Application Insights automatikusan összegyűjti és megjeleníti a függőségeket
ms.topic: reference
author: nikmd23
ms.author: nimolnar
ms.date: 04/29/2019
ms.reviewer: mbullwin
ms.openlocfilehash: eaafe19f5112b433d50a34aa551aa84d196726a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665816"
---
# <a name="dependency-auto-collection"></a>Függőségek automatikus összegyűjtése

Az alábbiakban a függőségi hívások jelenleg támogatott listáját, amelyek automatikusan függőségként észlelhető anélkül, hogy az alkalmazás kódjának további módosítása lenne. Ezek a függőségek az Application Insights [alkalmazástérkép](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) és a tranzakciódiagnosztikai nézetekben [láthatók.](https://docs.microsoft.com/azure/application-insights/app-insights-transaction-diagnostics) Ha a függőség nem szerepel az alábbi listán, akkor is nyomon követheti manuálisan a [pálya függőségi hívás](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

## <a name="net"></a>.NET

| Alkalmazáskeretrendszerek| Verziók |
| ------------------------|----------|
| ASP.NET webűrlapok | 4,5+ |
| ASP.NET, MVC | 4+ |
| ASP.NET WebAPI | 4,5+ |
| ASP.NET-mag | 1.1+ |
| <b>Kommunikációs könyvtárak</b> |
| [httpügyfél](https://www.microsoft.com/net/) | 4.5+, .NET Core 1.1+ |
| [SqlClient](https://www.nuget.org/packages/System.Data.SqlClient) | .NET Core 1.0+, NuGet 4.3.0 |
| [EventHubs ügyfél SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 1.1.0 |
| [ServiceBus ügyfél SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) | 3.0.0 |
| <b>Tárolóügyfelek</b>|  |
| ADO.NET | 4,5+ |

## <a name="java"></a>Java
| Alkalmazáskiszolgálók | Verziók |
|-------------|----------|
| [Tomcat](https://tomcat.apache.org/) | 7, 8 | 
| [JBoss EAP](https://developers.redhat.com/products/eap/download/) | 6, 7 |
| [Jetty](https://www.eclipse.org/jetty/) | 9 |
| <b>Alkalmazáskeretrendszerek</b> |  |
| [Spring](https://spring.io/) | 3.0 |
| [Spring Boot](https://spring.io/projects/spring-boot) | 1.5.9+<sup>*</sup> |
| Java Servlet | 3.1+ |
| <b>Kommunikációs könyvtárak</b> |  |
| [Apache http-ügyfél](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) | 4,3+<sup>†</sup> |
| <b>Tárolóügyfelek</b> | |
| [SQL Server]( https://mvnrepository.com/artifact/com.microsoft.sqlserver/mssql-jdbc) | 1+<sup>†</sup> |
| [PostgreSQL (Béta támogatás)](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/CHANGELOG.md#version-240-beta) | |
| [Oracle]( https://www.oracle.com/technetwork/database/application-development/jdbc/downloads/index.html) | 1+<sup>†</sup> |
| [Mysql]( https://mvnrepository.com/artifact/mysql/mysql-connector-java) | 1+<sup>†</sup> |
| <b>Tárak naplózása</b> | |
| [Visszamaradás](https://logback.qos.ch/) | 1+ |
| [Log4j](https://logging.apache.org/log4j/) | 1,2+ |
| <b>Metrikakönyvtárak</b> |  |
| JMX | 1,0+ |

> [!NOTE]
> *Kivéve a reaktív programozási támogatást.
> <br>†A [JVM-ügynök telepítését](https://docs.microsoft.com/azure/application-insights/app-insights-java-agent#install-the-application-insights-agent-for-java)igényli.

## <a name="nodejs"></a>Node.js

| Kommunikációs könyvtárak | Verziók |
| ------------------------|----------|
| [HTTP](https://nodejs.org/api/http.html), [HTTPS](https://nodejs.org/api/https.html) | 0,10+ |
| <b>Tárolóügyfelek</b> | |
| [Redis](https://www.npmjs.com/package/redis) | 2,x |
| [MongoDb](https://www.npmjs.com/package/mongodb); [MongoDb Mag](https://www.npmjs.com/package/mongodb-core) | 2.x - 3.x |
| [MySQL](https://www.npmjs.com/package/mysql) | 2.0.0 - 2.16.x |
| [PostgreSql](https://www.npmjs.com/package/pg); | 6.x - 7.x |
| [pg-medence](https://www.npmjs.com/package/pg-pool) | 1.x - 2.x |
| <b>Tárak naplózása</b> | |
| [Konzol](https://nodejs.org/api/console.html) | 0,10+ |
| [Bunyan](https://www.npmjs.com/package/bunyan) | 1,x |
| [Winston](https://www.npmjs.com/package/winston) | 2.x - 3.x |

## <a name="javascript"></a>JavaScript

| Kommunikációs könyvtárak | Verziók |
| ------------------------|----------|
| [XMLHttpRequest](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) | Összes |

## <a name="next-steps"></a>További lépések

- Egyéni függőségkövetés beállítása a [.NET](../../azure-monitor/app/asp-net-dependencies.md)rendszerhez.
- Egyéni függőségek nyomon [Java](../../azure-monitor/app/java-agent.md)követésének beállítása Java-hoz.
- Egyéni függőségkövetés beállítása az [OpenCensus Python](../../azure-monitor/app/opencensus-python-dependency.md)hoz.
- [Egyéni függőségi telemetriai adatok írása](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)
- Tekintse meg az Application Insights-típusok és adatmodell [adatmodelljét.](../../azure-monitor/app/data-model.md)
- Tekintse meg az Application Insights által támogatott [platformokat.](../../azure-monitor/app/platforms.md)
