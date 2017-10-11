---
title: "Java-webalkalmazások Azure Application Insights az alkalmazásteljesítmény-figyelés |} Microsoft Docs"
description: "Kiterjesztett teljesítmény és a Java-webhely, az Application Insights-használat figyelését."
services: application-insights
documentationcenter: java
author: harelbr
manager: carmonm
ms.assetid: 84017a48-1cb3-40c8-aab1-ff68d65e2128
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/24/2016
ms.author: bwren
ms.openlocfilehash: 4e56998382610ad3d7224e6a8de5aee5419ebe43
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="monitor-dependencies-exceptions-and-execution-times-in-java-web-apps"></a>Függőségek, kivételeket és végrehajtásának lassúságát a Java-webalkalmazások figyelése


Ha rendelkezik [a Java-webalkalmazás az Application insights szolgáltatással tagolva][java], a Java-ügynök részleteinek megtekintésével mélyebb betekintést kód módosítások nélkül használható:

* **Függőségek:** hívások, az alkalmazás által az egyéb összetevők, beleértve a vonatkozó adatokat:
  * **REST-hívások** HttpClient OkHttp és RestTemplate (forrás) keresztül történik.
  * **Redis** keresztül a Jedis ügyfél felé indított hívások. A hívás tovább tart, mint 10 egység, ha az ügynök is beolvassa a hívás argumentumokkal.
  * **[JDBC-hívások](http://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/)**  -MySQL, SQL Server, PostgreSQL, SQLite, Oracle DB vagy Apache Derby DB. "executeBatch" hívások támogatottak. A MySQL és PostgreSQL, ha a hívás tovább tart, mint 10 egység, az ügynök jelentéseket küld a lekérdezéstervben.
* **Kivétel lépett fel:** a kód által kezelt kivételek adatait.
* **Módszer végrehajtási ideje:** bizonyos eljárások végrehajtásához szükséges idő az adatait.

A Java-ügynök használatára, akkor a kiszolgálóra telepítette. A webalkalmazások kell tagolva, és a [Application Insights Java SDK][java]. 

## <a name="install-the-application-insights-agent-for-java"></a>A Javához készült Application Insights-ügynök telepítése
1. A számítógépen a Java Servert futtató [töltse le az ügynököt](https://aka.ms/aijavasdk).
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
