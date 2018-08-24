---
title: Java-webalkalmazások Azure Application Insights az alkalmazásteljesítmény-figyelés |} Microsoft Docs
description: Kiterjesztett teljesítmény és a Java-webhely, az Application Insights-használat figyelését.
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: 84017a48-1cb3-40c8-aab1-ff68d65e2128
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/24/2016
ms.author: mbullwin
ms.openlocfilehash: 3a771da2a1ef0333d49e1d83530b3d3032a550d2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32151630"
---
# <a name="monitor-dependencies-caught-exceptions-and-method-execution-times-in-java-web-apps"></a>Függőségek, kifogott kivételeket és metódus végrehajtásának lassúságát a Java-webalkalmazások figyelése


Ha rendelkezik [a Java-webalkalmazás az Application insights szolgáltatással tagolva][java], a Java-ügynök részleteinek megtekintésével mélyebb betekintést kód módosítások nélkül használható:

* **Függőségek:** hívások, az alkalmazás által az egyéb összetevők, beleértve a vonatkozó adatokat:
  * **REST-hívások** HttpClient keresztül történik, OkHttp és RestTemplate (forrás) a rendszer rögzíti.
  * **Redis** keresztül a Jedis ügyfél felé indított hívások a rendszer rögzíti.
  * **[JDBC-hívások](http://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/)**  -MySQL, SQL Server és Oracle DB parancsot a rendszer automatikusan rögzíti. A MySQL, ha a hívás tovább tart, mint 10 egység, az ügynök jelentéseket küld a lekérdezéstervben.
* **Kivétel lépett fel:** a kód által kezelt kivételek kapcsolatos információkat.
* **Módszer végrehajtási ideje:** bizonyos eljárások végrehajtásához szükséges idő kapcsolatos információkat.

A Java-ügynök használatára, akkor a kiszolgálóra telepítette. A webalkalmazások kell tagolva, és a [Application Insights Java SDK][java]. 

## <a name="install-the-application-insights-agent-for-java"></a>A Javához készült Application Insights-ügynök telepítése
1. A számítógépen a Java Servert futtató [töltse le az ügynököt](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest). Ellenőrizze, hogy az azonos verson Java-ügynök Application Insights Java SDK core és a web csomag letöltéséhez.
2. Az alkalmazás server indítási parancsfájl szerkesztése, és adja hozzá az alábbi JVM-et:
   
    `javaagent:`*az ügynök JAR-fájl teljes elérési útja*
   
    Például a Linux rendszerű gépen Tomcat:
   
    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`
3. Indítsa újra az alkalmazáskiszolgáló.

## <a name="configure-the-agent"></a>Az ügynök konfigurálása
Hozzon létre egy fájlt `AI-Agent.xml` és naplózza azt a mappában, amelyben az ügynök JAR-fájlra.

Állítsa be az XML-fájl tartalmát. Szerkessze a következő példa a azt szeretné, vagy hagyja el a szolgáltatásokat.

```XML

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsightsAgent>
      <Instrumentation>

        <!-- Collect remote dependency data -->
        <BuiltIn enabled="true">
           <!-- Disable Redis or alter threshold call duration above which arguments are sent.
               Defaults: enabled, 10000 ms -->
           <Jedis enabled="true" thresholdInMS="1000"/>

           <!-- Set SQL query duration above which query plan is reported (MySQL, PostgreSQL). Default is 10000 ms. -->
           <MaxStatementQueryLimitInMS>1000</MaxStatementQueryLimitInMS>
        </BuiltIn>

        <!-- Collect data about caught exceptions
             and method execution times -->

        <Class name="com.myCompany.MyClass">
           <Method name="methodOne"
               reportCaughtExceptions="true"
               reportExecutionTime="true"
               />

           <!-- Report on the particular signature
                void methodTwo(String, int) -->
           <Method name="methodTwo"
              reportExecutionTime="true"
              signature="(Ljava/lang/String;I)V" />
        </Class>

      </Instrumentation>
    </ApplicationInsightsAgent>

```

Kell engedélyezni a jelentések kivétel és az egyes módszerek metódus ütemezését.

Alapértelmezés szerint `reportExecutionTime` IGAZ és `reportCaughtExceptions` értéke "false".

## <a name="view-the-data"></a>Az adatok megjelenítése
Összesített távoli függőség és metódus végrehajtásának lassúságát jelenik meg az Application Insights-erőforrás [alatt a teljesítmény csempéje][metrics].

Nyissa meg a keresendő függőségi kivétel és metódus jelentések egyes példányai, [keresési][diagnostic].

[Diagnosztizálás függőségi problémákhoz – további](app-insights-asp-net-dependencies.md#diagnosis).

## <a name="questions-problems"></a>Kérdései vannak? Problémákat tapasztal?
* Nincs adat? [Tűzfalkivételek beállítása](app-insights-ip-addresses.md)
* [A Java hibaelhárítása](app-insights-java-troubleshoot.md)

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
