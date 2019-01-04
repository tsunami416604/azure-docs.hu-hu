---
title: Az Azure Application Insights Java-webalkalmazások alkalmazásteljesítmény-figyelés |} A Microsoft Docs
description: A kiterjesztett teljesítmény és használat monitorozása az Application insights szolgáltatással Java webhelyét.
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: 84017a48-1cb3-40c8-aab1-ff68d65e2128
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/24/2016
ms.author: mbullwin
ms.openlocfilehash: c0478b320afca1b82a79fa43e7b60c29a2cb2e7c
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2019
ms.locfileid: "53997928"
---
# <a name="monitor-dependencies-caught-exceptions-and-method-execution-times-in-java-web-apps"></a>Függőségek, kivételek kivétel történt, és metódus végrehajtási időpontok a Java-webalkalmazások monitorozása


Ha rendelkezik [kialakítva az Application Insights Java-webalkalmazását][java], a Java ügynököt segítségével részletesebb elemzéseket kódváltoztatás nélkül:

* **Függőségek:** Az alkalmazás által az egyéb összetevők, mint a hívások adatait:
  * **REST-hívások** HttpClient keresztül történik, OkHttp és RestTemplate (Spring) rögzítve lesznek.
  * **Redis Cache** a Jedis ügyfél-n keresztül végzett hívások rögzítve lesznek.
  * **[JDBC-hívások](https://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/)**  -MySQL, az SQL Server és Oracle DB parancsok automatikusan rögzítve lesznek. A MySQL a hívás hosszabb időt vesz igénybe, mint a 10s, ha az ügynök jelentéseket küld a lekérdezésterv.
* **Kivétel történt kivétel:** A kód által kezelt kivételek kapcsolatos információk.
* **Metódus végrehajtási idő:** Információ az idő adott módszerek végrehajtásához szükséges.

A Java ügynököt használatához telepítheti a kiszolgálón. A web apps kell lesznek tagolva a [Application Insights Java SDK][java]. 

## <a name="install-the-application-insights-agent-for-java"></a>A Javához készült Application Insights-ügynök telepítése
1. A gépen, amelyen fut a Java kiszolgáló [töltse le az ügynököt](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest). Ellenőrizze, hogy az azonos verson Java-ügynök az Application Insights Java SDK core és a web csomag letöltéséhez.
2. Az alkalmazás-kiszolgáló indítási parancsfájl szerkesztése, és adja hozzá az alábbi JVM:
   
    `javaagent:`*az ügynök JAR-fájl teljes elérési útja*
   
    Például a Linux rendszerű gépen Tomcat:
   
    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`
3. Indítsa újra az alkalmazáskiszolgáló.

## <a name="configure-the-agent"></a>Az ügynök konfigurálása
Hozzon létre egy fájlt `AI-Agent.xml` és helyezze ugyanabba a mappába, az ügynök JAR-fájlt.

Állítsa be az XML-fájl tartalmát. Szerkessze a következő példa a azt szeretné, vagy hagyja ki a szolgáltatásokat.

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

Jelentések kivétel- és az egyes módszerek metódus időzítési engedélyeznie kell.

Alapértelmezés szerint `reportExecutionTime` IGAZ és `reportCaughtExceptions` false (hamis).

## <a name="view-the-data"></a>Az adatok megtekintése
Összesített távoli függőség- és metódus végrehajtási időpontok jelenik meg az Application Insights-erőforrást [alatt a teljesítmény csempéje][metrics].

Keresse meg az egyes példányok függőségi, kivételek és módszer jelentéseknek, nyissa meg a [keresési][diagnostic].

[Diagnosztizálás függőségi kapcsolatos problémák – további](../../azure-monitor/app/asp-net-dependencies.md#diagnosis).

## <a name="questions-problems"></a>Kérdései vannak? Problémákat tapasztal?
* Nincs adat? [Végezze el a tűzfalkivételek beállítása](../../azure-monitor/app/ip-addresses.md)
* [A Java hibaelhárítása](java-troubleshoot.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#track-exception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
