---
title: Ismerje meg a nyomkövetési naplók az Azure Application Insights Java |} A Microsoft Docs
description: Az Application Insights keresés Log4J, vagy Logback nyomkövetések
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: fc0a9e2f-3beb-4f47-a9fe-3f86cd29d97a
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/12/2018
ms.author: mbullwin
ms.openlocfilehash: 934d5929048627bf378f50814f0cecde794ddab9
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52727851"
---
# <a name="explore-java-trace-logs-in-application-insights"></a>Nyomkövetési naplók az Application Insights Java felfedezése
Ha a Logback vagy Log4J használja (1.2-es verzió vagy 2.0-s verzió) nyomkövetés, az automatikusan elküldi az Application Insights, amelyen ismerje meg, és keresse meg azokat a nyomkövetési naplók rendelkezhet.

## <a name="install-the-java-sdk"></a>Telepítse a Java SDK

Kövesse az utasításokat követve telepítse [Javához készült Application Insights SDK][java], ha még nem tette meg.

## <a name="add-logging-libraries-to-your-project"></a>Naplózás kódtárak hozzáadása a projekthez
*Válassza ki a projektnek megfelelő módszert.*

#### <a name="if-youre-using-maven"></a>Ha Mavent használ...
Ha a projekt már úgy van beállítva, hogy Mavent használ buildként, egyesítse egyet az alábbi kódrészletek kód a pom.xml fájlt.

Ezután frissítse a projektfüggőségeket, hogy a Projektfüggőségek.

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
Build használandó gradle-t a projekt már be van állítva, hozzá egyet a következő sorokat a `dependencies` csoportjában a build.gradle fájllal:

Ezután frissítse a projektfüggőségeket, hogy a Projektfüggőségek.

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
Kövesse a manuálisan Application Insights Java SDK telepítése, töltse le a jar (miután Maven központi lapra jogokért letöltési szakaszban "jar" hivatkozásra kattintva) megfelelő naplóírói, és a letöltött naplóírói jar hozzáadása a projekthez.

| Naplózó | Letöltés | Erőforrástár |
| --- | --- | --- |
| Logback |[Logback naplóírói Jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-logback%22) |applicationinsights – naplózás – logback |
| Log4J v2.0 |[Log4J v2 naplóírói Jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j2%22) |applicationinsights-logging-log4j2 |
| Log4j 1.2-es verzió |[Log4J 1.2-es verzió naplóírói Jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j1_2%22) |applicationinsights-logging-log4j1_2 |


## <a name="add-the-appender-to-your-logging-framework"></a>Adja hozzá a naplóírót a naplózási keretrendszer
Nyomkövetés megkezdődik, egyesítse a megfelelő kódrészletét a Log4J, vagy Logback konfigurációs fájl: 

*Logback*

```XML

    <appender name="aiAppender" 
      class="com.microsoft.applicationinsights.logback.ApplicationInsightsAppender">
    </appender>
    <root level="trace">
      <appender-ref ref="aiAppender" />
    </root>
```

*Log4J v2.0*

```XML

    <Configuration packages="com.microsoft.applicationinsights.log4j.v2">
      <Appenders>
        <ApplicationInsightsAppender name="aiAppender" />
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
    </appender>
    <root>
      <priority value ="trace" />
      <appender-ref ref="aiAppender" />
    </root>
```

Az Application Insights appenders lehet rá hivatkozni bármely konfigurált naplózónak, és nem feltétlenül a legfelső szintű naplózó (ahogyan az a fenti Kódminták látható).

## <a name="explore-your-traces-in-the-application-insights-portal"></a>Prozkoumat trasování az Application Insights portálon
Most, hogy konfigurálta a projekthez, hogy elküldheti az Application Insightsba, megtekintheti és az Application Insights portálon a nyomkövetések keresni a [keresési] [ diagnostic] panelen.

Kivételek beküldésének keresztül másolása kivétel telemetriaként fog megjelenni a portálon.

![Az Application Insights portálon nyissa meg a keresés](./media/app-insights-java-trace-logs/10-diagnostics.png)

## <a name="next-steps"></a>További lépések
[Diagnosztikai keresés][diagnostic]

<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[java]: app-insights-java-get-started.md


