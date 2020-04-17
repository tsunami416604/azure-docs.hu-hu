---
title: Java webalkalmazások teljesítményfigyelése – Azure Application Insights
description: A Java-webhely teljesítményének és használatának figyelése az Application Insights segítségével.
ms.topic: conceptual
ms.date: 01/10/2019
ms.openlocfilehash: b047a8dd8c67679a5cc8a45e8be82f9ab5227aa4
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537542"
---
# <a name="monitor-dependencies-caught-exceptions-and-method-execution-times-in-java-web-apps"></a>Függőségek figyelése, kivételek és metódusok végrehajtási idejének figyelése java webalkalmazásokban


Ha [a Java webalkalmazást az Application Insights segítségével instrumented,][java]a Java Agent segítségével mélyebb elemzéseket kaphat, kódmódosítások nélkül:

* **Függőségek:** Az alkalmazás által más összetevőknek kezdeményezett hívásokadatai, többek között a következők:
  * Az Apache HttpClient, OkHttp protokollon `java.net.HttpURLConnection` keresztül kezdeményezett **kimenő HTTP-hívások** rögzítésre kerülnek.
  * A Jedi kliensen keresztül kezdeményezett **Redis hívásokat** elfogták.
  * **JDBC-lekérdezések** - A MySQL és a PostgreSQL, ha a hívás több mint 10 másodpercig tart, az ügynök jelenti a lekérdezési terv.

* **Alkalmazás naplózása:** Az alkalmazásnaplók rögzítése és korrelálása HTTP-kérelmekkel és más telemetriai adatokkal
  * **Log4j 1,2**
  * **Log4j2**
  * **Visszamaradás**

* **Jobb műveletelnevezés:** (a kérelmek összesítésére szolgál a portálon)
  * **Tavasz** - `@RequestMapping`alapján .
  * **JAX-RS** - `@Path`alapján . 

A Java-ügynök használatához telepítse azt a kiszolgálóra. A webalkalmazásokat az [Application Insights Java SDK-val][java]kell elsajátította. 

## <a name="install-the-application-insights-agent-for-java"></a>Az Application Insights-ügynök Java-hoz történő telepítése
1. [Töltse le az ügynököt](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) a Java-kiszolgálót futtató számítógépre. Ügyeljen rá, hogy a Java ügynök verziója és az Application Insights Java SDK core és web csomagok verziója azonos legyen.
2. Az alkalmazáskiszolgáló indítási parancsfájljának szerkesztése és a következő JVM-argumentum hozzáadása:
   
    `-javaagent:<full path to the agent JAR file>`
   
    Például a Tomcat egy Linux gépen:
   
    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`
3. Indítsa újra az alkalmazáskiszolgálót.

## <a name="configure-the-agent"></a>Az ügynök konfigurálása
Hozzon létre `AI-Agent.xml` egy nevű fájlt, és helyezze ugyanabba a mappába, mint az ügynök JAR-fájl.

Állítsa be az XML-fájl tartalmát. A következő példa szerkesztésével a kívánt szolgáltatásokat is felveheti vagy kihagyhatja.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn enabled="true">

         <!-- capture logging via Log4j 1.2, Log4j2, and Logback, default is true -->
         <Logging enabled="true" />

         <!-- capture outgoing HTTP calls performed through Apache HttpClient, OkHttp,
              and java.net.HttpURLConnection, default is true -->
         <HTTP enabled="true" />

         <!-- capture JDBC queries, default is true -->
         <JDBC enabled="true" />

         <!-- capture Redis calls, default is true -->
         <Jedis enabled="true" />

         <!-- capture query plans for JDBC queries that exceed this value (MySQL, PostgreSQL),
              default is 10000 milliseconds -->
         <MaxStatementQueryLimitInMS>1000</MaxStatementQueryLimitInMS>

      </BuiltIn>
   </Instrumentation>
</ApplicationInsightsAgent>
```

## <a name="additional-config-spring-boot"></a>További konfiguráció (spring boot)

`java -javaagent:/path/to/agent.jar -jar path/to/TestApp.jar`

Az Azure App Services esetén tegye a következőket:

* Válassza a Beállítások > Alkalmazásbeállítások lehetőséget.
* Az alkalmazásbeállításoknál adjon meg egy új kulcs-érték párt:

Kulcs: `JAVA_OPTS` Érték:`-javaagent:D:/home/site/wwwroot/applicationinsights-agent-2.5.0.jar`

A Java-ügynök legújabb verziójáért tekintse meg a kiadásokat [itt.](https://github.com/Microsoft/ApplicationInsights-Java/releases
) 

Az ügynököt erőforrásként kell csomagolni a projektben, hogy az a D:/home/site/wwwroot/ könyvtárba kerüljön. Az ügynök helyes App Service-címtárban való beállítását a **Fejlesztőeszközök** > **speciális eszközök** > **hibakeresési konzoljára** és a helykönyvtár tartalmának vizsgálatával ellenőrizheti.    

* Mentse a beállításokat, és indítsa újra az alkalmazást. (Ezek a lépések csak a Windows rendszeren futó App Services szolgáltatásra vonatkoznak.)

> [!NOTE]
> Az AI-Agent.xml fájlnak és az ügynökjar fájlnak ugyanabban a mappában kell lennie. Gyakran együtt kerülnek a `/resources` projekt mappájába.  

#### <a name="enable-w3c-distributed-tracing"></a>W3C elosztott nyomkövetés engedélyezése

Adja hozzá az alábbiakat az AI-Agent.xml fájlhoz:

```xml
<Instrumentation>
   <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
   </BuiltIn>
</Instrumentation>
```

> [!NOTE]
> A visszamenőleges kompatibilitási mód alapértelmezés szerint engedélyezve van, és az enableW3CBackCompat paraméter nem kötelező, és csak akkor használható, ha ki szeretné kapcsolni. 

Ideális esetben ez a helyzet akkor fordulna elő, ha az összes szolgáltatás frissítve lett a W3C protokollt támogató SDK-k újabb verziójára. Erősen ajánlott az SDK-k újabb verziójára való mielőbbi W3C-támogatással való áttérés.

Győződjön meg arról, hogy **mind [a bejövő,](correlation.md#enable-w3c-distributed-tracing-support-for-java-apps) mind a kimenő (ügynök) konfigurációk** pontosan megegyeznek.

## <a name="view-the-data"></a>Az adatok megtekintése
Az Application Insights-erőforrásban az összesített távoli függőség és a metódusvégrehajtási idők [a Teljesítmény csempén][metrics]jelennek meg.

A függőségi, kivétel- és metódusjelentések egyedi példányainak kereséséhez nyissa meg a [Keresés (Keresés) (Keresés) (Keresés) (Keresés)][diagnostic]és metódusjelentések kereséséhez.

[Diagnosztizálása függőségi problémák - többet](../../azure-monitor/app/asp-net-dependencies.md#diagnosis).

## <a name="questions-problems"></a>Kérdései vannak? Problémákat tapasztal?
* Nincs adat? [Tűzfal-kivételek beállítása](../../azure-monitor/app/ip-addresses.md)
* [A Java hibaelhárítása](java-troubleshoot.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#track-exception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/platform/metrics-charts.md
