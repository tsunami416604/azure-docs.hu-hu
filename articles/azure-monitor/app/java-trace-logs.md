---
title: Java-nyomkövetési naplók felfedezése az Azure Application Insightsban
description: Keresési napló4J vagy logback-nyomkövetések az Application Insightsban
ms.topic: conceptual
ms.date: 05/18/2019
ms.openlocfilehash: da1b76d52ab93f4d1be7196d6eb7286579481119
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657214"
---
# <a name="explore-java-trace-logs-in-application-insights"></a>Java-nyomkövetési naplók felfedezése az Application Insightsban
Ha a Logback vagy a Log4J (1.2-es vagy 2.0-s) a nyomkövetés, akkor a nyomkövetési naplók automatikusan elküldve Application Insights, ahol felfedezheti és kereshet rajtuk.

> [!TIP]
> Csak egyszer kell beállítania az Application Insights instrumentation kulcsát az alkalmazáshoz. Ha olyan keretrendszert használ, mint a Java Spring, előfordulhat, hogy már máshol regisztrálta a kulcsot az alkalmazás konfigurációjában.

## <a name="using-the-application-insights-java-agent"></a>Az Application Insights Java-ügynök használata

Alapértelmezés szerint az Application Insights Java-ügynök `WARN` automatikusan rögzíti a szinten és felett végrehajtott naplózást.

A fájl használatával rögzített naplózási küszöbértéket `AI-Agent.xml` módosíthatja:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn>
         <Logging threshold="info"/>
      </BuiltIn>
   </Instrumentation>
</ApplicationInsightsAgent>
```

A Java-ügynök naplózási rögzítését `AI-Agent.xml` a következő fájl segítségével tilthatja le:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn>
         <Logging enabled="false"/>
      </BuiltIn>
   </Instrumentation>
</ApplicationInsightsAgent>
```

## <a name="alternatively-as-opposed-to-using-the-java-agent-you-can-follow-the-instructions-below"></a>Másik lehetőségként (a Java-ügynök használatával ellentétben) kövesse az alábbi utasításokat

### <a name="install-the-java-sdk"></a>A Java SDK telepítése

Kövesse az utasításokat az [Application Insights SDK Java-hoz][java]történő telepítéséhez, ha még nem tette meg.

### <a name="add-logging-libraries-to-your-project"></a>Naplózási tárak hozzáadása a projekthez
*Válassza ki a projektnek megfelelő módszert.*

#### <a name="if-youre-using-maven"></a>Ha Mavent használ...
Ha a projekt már be van állítva a Maven létrehozásához, egyesítse az alábbi kódrészletek egyikét a pom.xml fájlba.

Ezután frissítse a projektfüggőségeket, hogy letölthesse a bináris fájlokat.

*Visszamaradás*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-logback</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J 2.0-s adatvevel*

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
Ha a projekt már be van állítva a Gradle buildelésre való használatára, adja hozzá az alábbi sorok egyikét a `dependencies` build.gradle fájlban lévő csoporthoz:

Ezután frissítse a projektfüggőségeket, hogy letölthesse a bináris fájlokat.

**Visszamaradás**

```

    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-logback', version: '2.0.+'
```

**Log4J 2.0-s adatvevel**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j2', version: '2.0.+'
```

**Log4J v1.2**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j1_2', version: '2.0.+'
```

#### <a name="otherwise-"></a>Egyéb esetben...
Kövesse az irányelveket, hogy manuálisan telepítse Application Insights Java SDK, töltse le a jar (Miután megérkezett A Maven Központi oldal kattintson a "jar" linkre letöltési szakaszban) a megfelelő appender és adja hozzá a letöltött appender jar a projekthez.

| Tuskózó | Letöltés | Erőforrástár |
| --- | --- | --- |
| Visszamaradás |[Logback appender Jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-logback%22) |applicationinsights-logging-logback |
| Log4J 2.0-s adatvevel |[Log4J v2 appender Jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j2%22) |applicationinsights-logging-log4j2 |
| Log4j v1.2 |[Log4J v1.2 appender Jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j1_2%22) |applicationinsights-naplózás-log4j1_2 |


### <a name="add-the-appender-to-your-logging-framework"></a>A hozzáfűző hozzáadása a naplózási keretrendszerhez
A nyomkövetések beszerzéséhez egyesítse a megfelelő kódrészletet a Log4J vagy a Logback konfigurációs fájllal: 

*Visszamaradás*

```XML

    <appender name="aiAppender" 
      class="com.microsoft.applicationinsights.logback.ApplicationInsightsAppender">
        <instrumentationKey>[APPLICATION_INSIGHTS_KEY]</instrumentationKey>
    </appender>
    <root level="trace">
      <appender-ref ref="aiAppender" />
    </root>
```

*Log4J 2.0-s adatvevel*

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

Az Application Insights appenders bármely konfigurált naplózó, és nem feltétlenül a gyökér naplózó (ahogy a fenti kódminták).

## <a name="explore-your-traces-in-the-application-insights-portal"></a>Fedezze fel nyomait az Application Insights portálon
Most, hogy beállította a projektet, hogy nyomkövetéseket küldjön az Application Insightsnak, megtekintheti és keresheti ezeket a nyomkövetéseket az Application Insights portálon, a [Keresés][diagnostic] panelen.

A naplózókon keresztül küldött kivételek kivételként jelennek meg a portálon kivételtelemetriaként.

![Az Application Insights portálon nyissa meg a Keresés](./media/java-trace-logs/01-diagnostics.png)

## <a name="next-steps"></a>További lépések
[Diagnosztikai keresés][diagnostic]

<!--Link references-->

[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[java]: java-get-started.md


