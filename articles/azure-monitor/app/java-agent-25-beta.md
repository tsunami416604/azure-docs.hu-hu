---
title: A Java Web Apps teljesítményének figyelése az Azure Application Insightsban (2.5.0-BETA) | Microsoft Docs
description: A Java-webhely kiterjesztett teljesítmény-és használati figyelése Application Insights (2.5.0-BETA) használatával.
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: 84017a48-1cb3-40c8-aab1-ff68d65e2128
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: mbullwin
ms.openlocfilehash: 0596cc78c464deb117def3178827ba4945f857ac
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68298320"
---
# <a name="monitor-dependencies-caught-exceptions-and-method-execution-times-in-java-web-apps"></a>Függőségek, kifogott kivételek és metódus-végrehajtási idők figyelése Java-webalkalmazásokban


Ha a Java-webalkalmazást [Application Insights][java]használatával állította be, a Java-ügynökkel mélyebb elemzéseket kaphat, kód módosítása nélkül:

* **Függőségek** Az alkalmazás által más összetevőkre irányuló hívásokkal kapcsolatos információk, beleértve a következőket:
  * Az Apache HttpClient, a OkHttp és `java.net.HttpURLConnection` a rögzített http-hívások rögzítése megtörtént.
  * A Jedis ügyféllel készített **Redis-hívások** rögzítése történik.
  * **JDBC-lekérdezések** – a MySQL és a PostgreSQL esetében, ha a hívás 10 másodpercnél hosszabb időt vesz igénybe, az ügynök jelentést készít a lekérdezési tervről.

* **Alkalmazás naplózása:** Az alkalmazás naplófájljainak rögzítése és korrelálása HTTP-kérelmekkel és egyéb telemetria
  * **Log4j 1,2**
  * **Log4j2**
  * **Logback**

* **Jobb működés elnevezése:** (a portálon található kérelmek összesítéséhez használatos)
  * **Rugó** – alapján `@RequestMapping`.
  * **Jax-RS** -alapján `@Path`. 

A Java-ügynök használatához telepítenie kell a-kiszolgálóra. A webalkalmazásokat a [Application Insights Java SDK][java]-val kell kiépíteni. 

## <a name="install-the-application-insights-agent-for-java"></a>A Javához készült Application Insights-ügynök telepítése
1. [Töltse le az ügynököt](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) a Java-kiszolgálót futtató számítógépre. Ügyeljen rá, hogy a Java ügynök verziója és az Application Insights Java SDK core és web csomagok verziója azonos legyen.
2. Szerkessze az alkalmazáskiszolgáló indítási parancsfájlját, és adja hozzá a következő JVM ARG-t:
   
    `-javaagent:<full path to the agent JAR file>`
   
    Például a Tomcat egy Linux rendszerű gépen:
   
    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`
3. Indítsa újra az alkalmazást.

## <a name="configure-the-agent"></a>Az ügynök konfigurálása
Hozzon létre egy `AI-Agent.xml` nevű fájlt, és helyezze ugyanabba a mappába, amelyben az ügynök jar-fájlja található.

Adja meg az XML-fájl tartalmát. Szerkessze az alábbi példát a kívánt funkciók belefoglalásához vagy kihagyásához.

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

## <a name="additional-config-spring-boot"></a>További konfiguráció (Spring boot)

`java -javaagent:/path/to/agent.jar -jar path/to/TestApp.jar`

Az Azure App Services esetében tegye a következőket:

* Válassza a Beállítások > Alkalmazásbeállítások lehetőséget.
* Az alkalmazásbeállításoknál adjon meg egy új kulcs-érték párt:

Kulcs: `JAVA_OPTS`Érték`-javaagent:D:/home/site/wwwroot/applicationinsights-agent-2.5.0-BETA.jar`

A Java-ügynök legújabb verziójáért tekintse meg a [kiadásokat](https://github.com/Microsoft/ApplicationInsights-Java/releases
)itt. 

Az ügynököt erőforrásként kell csomagolni a projektben úgy, hogy az a D:/Home/site/wwwroot/könyvtárban végződik. A **fejlesztői eszközök** > **speciális eszközök** > **hibakeresési konzolján** ellenőrizheti, hogy az ügynök a megfelelő app Service könyvtárban található-e, és megvizsgálja a hely könyvtárának tartalmát.    

* Mentse a beállításokat, és indítsa újra az alkalmazást. (Ezek a lépések csak Windows rendszeren futó App Services vonatkoznak.)

> [!NOTE]
> A AI-Agent. XML és az Agent jar-fájlnak ugyanabban a mappában kell lennie. Ezeket gyakran együtt helyezik el `/resources` a projekt mappájába.  

#### <a name="enable-w3c-distributed-tracing"></a>W3C elosztott nyomkövetés engedélyezése

Adja hozzá a következőt a AI-Agent. xml fájlhoz:

```xml
<Instrumentation>
   <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
   </BuiltIn>
</Instrumentation>
```

> [!NOTE]
> A visszafelé kompatibilitási mód alapértelmezés szerint engedélyezve van, és a enableW3CBackCompat paraméter nem kötelező, és csak akkor használható, ha ki szeretné kapcsolni. 

Ideális esetben ez az eset, amikor az összes szolgáltatás frissítve lett a W3C protokollt támogató SDK-k újabb verziójára. Javasoljuk, hogy a lehető leghamarabb váltson a W3C-támogatással rendelkező SDK-k újabb verziójára.

Győződjön meg arról, hogy a  **[bejövő](correlation.md#w3c-distributed-tracing) és a kimenő (ügynök) konfigurációk** is pontosan azonosak.

## <a name="view-the-data"></a>Az adatgyűjtés megtekintése
A Application Insights erőforrásban [a teljesítmény csempén][metrics]a távoli függőségi és metódus-végrehajtási idők szerepelnek.

A függőség, a kivétel és a metódus-jelentések egyes példányainak kereséséhez nyissa meg a [keresést][diagnostic].

[Függőségi problémák diagnosztizálása – további információ](../../azure-monitor/app/asp-net-dependencies.md#diagnosis).

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
[metrics]: ../../azure-monitor/app/metrics-explorer.md
