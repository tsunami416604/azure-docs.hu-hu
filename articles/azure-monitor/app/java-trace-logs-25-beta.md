---
title: Ismerkedés a Java-nyomkövetési naplókkal az Azure Application Insightsban (2.5.0-BETA) | Microsoft Docs
description: Log4J-vagy Logback-nyomkövetés keresése a Application Insightsban (2.5.0-BETA)
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: fc0a9e2f-3beb-4f47-a9fe-3f86cd29d97a
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/18/2019
ms.author: mbullwin
ms.openlocfilehash: 028563658256f7bd8e016b5c7bbf703a5030a3de
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68298359"
---
# <a name="explore-java-trace-logs-in-application-insights-250-beta"></a>A Java-nyomkövetési naplók megismerése a Application Insightsban (2.5.0-BETA)
Ha a nyomkövetéshez Logback vagy Log4J (v 1.2 vagy v 2.0) használ, a nyomkövetési naplókat automatikusan elküldheti Application Insights ahol megtekintheti és megkeresheti őket.

## <a name="note-if-you-are-using-the-application-insights-java-agent"></a>Megjegyzés: Ha a Application Insights Java-ügynököt használja

Az Application Insights Java-ügynököt beállíthatja úgy, hogy automatikusan rögzítse a naplókat, ha `AI-Agent.xml` engedélyezi a funkciót a fájlban:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn enabled="true">
         <Logging enabled="true" />
      </BuiltIn>
   </Instrumentation>
   <AgentLogger />
</ApplicationInsightsAgent>
```

Ellenkező esetben...

## <a name="install-the-java-sdk"></a>A Java SDK telepítése

Ha még nem tette meg, kövesse az utasításokat a [Java Application INSIGHTS SDK][java]telepítéséhez.

## <a name="add-logging-libraries-to-your-project"></a>Naplózási kódtárak hozzáadása a projekthez
*Válassza ki a projektnek megfelelő módszert.*

#### <a name="if-youre-using-maven"></a>Ha Mavent használ...
Ha a projekt már úgy van beállítva, hogy a Mavent a buildhez használja, egyesítse az alábbi kódrészletek egyikét a Pom. XML fájlba.

Ezután frissítse a projekt függőségeit a letöltött bináris fájlok letöltéséhez.

*Logback*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-logback</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J v2.0*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j2</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J v1.2*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j1_2</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

#### <a name="if-youre-using-gradle"></a>Ha Gradle-t használ...
Ha a projekt már be van állítva az Gradle for Build használatára, adja hozzá a következő sorok egyikét a `dependencies` Build. Gradle fájljában található csoporthoz:

Ezután frissítse a projekt függőségeit a letöltött bináris fájlok letöltéséhez.

**Logback**

```

    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-logback', version: '2.0.+'
```

**Log4J v2.0**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j2', version: '2.0.+'
```

**Log4J v1.2**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j1_2', version: '2.0.+'
```

#### <a name="otherwise-"></a>Egyéb esetben...
Kövesse az irányelveket a Application Insights Java SDK manuális telepítéséhez, töltse le a jar-t (a Maven Central oldalának megérkezése után kattintson a "jar" hivatkozásra a letöltési szakaszban) a megfelelő hozzáfűzéshez, és adja hozzá a letöltött append jar-t a projekthez.

| Tuskózó | Letöltés | Erőforrástár |
| --- | --- | --- |
| Logback |[Logback-hozzáfűző jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-logback%22) |applicationinsights-logging-logback |
| Log4J v2.0 |[Log4J v2 hozzáfűzése jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j2%22) |applicationinsights-logging-log4j2 |
| Log4j 2.0-s verzió |[Log4J v 1.2 hozzáfűzése jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j1_2%22) |applicationinsights-logging-log4j1_2 |


## <a name="add-the-appender-to-your-logging-framework"></a>A hozzáfűzése hozzáadása a naplózási keretrendszerhez
A nyomkövetési lépések elindításához egyesítse a megfelelő kódrészletet a Log4J vagy a Logback konfigurációs fájlba: 

*Logback*

```XML

    <appender name="aiAppender" 
      class="com.microsoft.applicationinsights.logback.ApplicationInsightsAppender">
        <instrumentationKey>[APPLICATION_INSIGHTS_KEY]</instrumentationKey>
    </appender>
    <root level="trace">
      <appender-ref ref="aiAppender" />
    </root>
```

*Log4J v2.0*

```XML

    <Configuration packages="com.microsoft.applicationinsights.log4j.v2">
      <Appenders>
        <ApplicationInsightsAppender name="aiAppender" instrumentationKey="[APPLICATION_INSIGHTS_KEY]" />
      </Appenders>
      <Loggers>
        <Root level="trace">
          <AppenderRef ref="aiAppender"/>
        </Root>
      </Loggers>
    </Configuration>
```

*Log4J v1.2*

```XML

    <appender name="aiAppender" 
         class="com.microsoft.applicationinsights.log4j.v1_2.ApplicationInsightsAppender">
        <param name="instrumentationKey" value="[APPLICATION_INSIGHTS_KEY]" />
    </appender>
    <root>
      <priority value ="trace" />
      <appender-ref ref="aiAppender" />
    </root>
```

A Application Insights-hozzáfűző hivatkozásokat bármely konfigurált naplózó hivatkozhat, és nem feltétlenül a root naplózó (a fenti mintakód-példákban látható).

## <a name="explore-your-traces-in-the-application-insights-portal"></a>A Nyomkövetések megismerése a Application Insights portálon
Most, hogy úgy konfigurálta a projektet, hogy nyomkövetéseket küldjön a Application Insightsnak, megtekintheti és megkeresheti ezeket a nyomkövetéseket a Application Insights portálon, a [Keresés][diagnostic] panelen.

A gyűjtők által küldött kivételek a portálon a kivétel telemetria jelennek meg.

![A Application Insights portálon nyissa meg a keresést](./media/java-trace-logs/01-diagnostics.png)

## <a name="next-steps"></a>További lépések
[Diagnosztikai keresés][diagnostic]

<!--Link references-->

[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[java]: java-get-started.md


