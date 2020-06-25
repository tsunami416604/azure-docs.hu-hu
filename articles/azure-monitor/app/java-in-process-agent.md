---
title: Java-alkalmazások figyelése bármilyen környezetben – Azure Monitor Application Insights
description: Alkalmazások teljesítményének figyelése bármilyen környezetben futó Java-alkalmazásokhoz az alkalmazás kialakítása nélkül. Elosztott nyomkövetési és alkalmazás-hozzárendelés.
ms.topic: conceptual
ms.date: 03/29/2020
ms.openlocfilehash: 3e3d108603ad6210143deea58049ff7b230bb6fa
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2020
ms.locfileid: "85319703"
---
# <a name="java-codeless-application-monitoring-azure-monitor-application-insights---public-preview"></a>A Java Code unapplication monitoring Azure monitor Application Insights – nyilvános előzetes verzió

A Java kód nélküli alkalmazások figyelése az egyszerűségről szól – a kód módosítása nem változik, a Java-ügynök csak néhány konfigurációs módosítással engedélyezhető.

 A Java-ügynök bármilyen környezetben működik, és lehetővé teszi a Java-alkalmazások figyelését. Más szóval, függetlenül attól, hogy a Java-alkalmazásait virtuális gépeken, a helyszínen, az AK-ban, Windowson, Linuxon futtatja, a Java 3,0-ügynök fogja figyelni az alkalmazást.

Az Application Insights Java SDK-nak az alkalmazáshoz való hozzáadása már nem szükséges, mivel az 3,0-ügynök a kérelmeket, a függőségeket és a naplókat saját maga gyűjti.

Továbbra is küldhet egyéni telemetria az alkalmazásból. Az 3,0-ügynök nyomon követheti és korrelálhatja azt az összes automatikusan összegyűjtött telemetria együtt.

## <a name="quickstart"></a>Első lépések

**1. Töltse le az ügynököt**

A [applicationinsights-Agent-3.0.0-preview. 5. jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.0-PREVIEW.5/applicationinsights-agent-3.0.0-PREVIEW.5.jar) letöltése

**2. irányítsa a JVM az ügynökre**

Hozzáadás `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.5.jar` az alkalmazás JVM argumentumai

A tipikus JVM argumentumok közé tartoznak `-Xmx512m` a és a `-XX:+UseG1GC` . Tehát ha tudja, hol adja hozzá ezeket, akkor már tudja, hová adja hozzá ezt a lehetőséget.

Ha további segítségre van az alkalmazás JVM argumentumok konfigurálásához, tekintse meg a [3,0 előzetes verzió: Tippek a JVM argumentumok frissítéséhez](https://docs.microsoft.com/azure/azure-monitor/app/java-standalone-arguments)című témakört.

**3. irányítsa az ügynököt a Application Insights erőforrásra**

Ha még nem rendelkezik Application Insights erőforrással, létrehozhat egy újat az [Erőforrás-létrehozási útmutató](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)lépéseit követve.

Mutasson az ügynököt a Application Insights erőforrásra, vagy egy környezeti változó beállításával:

```
APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=00000000-0000-0000-0000-000000000000
```

Vagy hozzon létre egy nevű konfigurációs fájlt `ApplicationInsights.json` , és helyezze ugyanabba a könyvtárba `applicationinsights-agent-3.0.0-PREVIEW.5.jar` , a következő tartalommal:

```json
{
  "instrumentationSettings": {
    "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000"
  }
}
```

A Application Insights erőforrásban található a kapcsolatok karakterlánca:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights a kapcsolatok karakterlánca":::

**4. ennyi!**

Most indítsa el az alkalmazást, és nyissa meg a Application Insights erőforrást a Azure Portalban a figyelési adatai megtekintéséhez.

> [!NOTE]
> Eltarthat néhány percig, amíg a figyelési adatai megjelennek a portálon.


## <a name="configuration-options"></a>Beállítási lehetőségek

A `ApplicationInsights.json` fájlban emellett a következőket is konfigurálhatja:

* Felhőbeli szerepkör neve
* Felhőalapú szerepkör-példány
* Alkalmazásnapló-rögzítés
* JMX metrikák
* Mikrométer
* Szívverés
* Mintavételezés
* HTTP-proxy
* Saját diagnosztika

Tekintse meg a részleteket a [3,0 nyilvános előzetes verzióban: konfigurációs beállítások](https://docs.microsoft.com/azure/azure-monitor/app/java-standalone-config).

## <a name="autocollected-requests-dependencies-logs-and-metrics"></a>Az újragyűjtött kérelmek, függőségek, naplók és metrikák

### <a name="requests"></a>Kérelmek

* JMS-fogyasztók
* Kafka-fogyasztók
* Nettó/webflux
* Servlets
* Rugó ütemezése

### <a name="dependencies-with-distributed-trace-propagation"></a>Elosztott nyomkövetési propagálással rendelkező függőségek

* Apache HttpClient és HttpAsyncClient
* gRPC
* Java. net. HttpURLConnection
* JMS
* Kafka
* Nettó ügyfél
* OkHttp

### <a name="other-dependencies"></a>Egyéb függőségek

* Cassandra
* JDBC
* MongoDB (aszinkron és szinkronizálás)
* Redis (saláta és jedik)

### <a name="logs"></a>Naplók

* Java. util. Logging
* Log4j
* SLF4J/Logback

### <a name="metrics"></a>Mérőszámok

* Mikrométer (beleértve a Spring boot indítószerkezet metrikáit)
* JMX metrikák

## <a name="sending-custom-telemetry-from-your-application"></a>Egyéni telemetria küldése az alkalmazásból

A 3.0-s célunk, hogy az egyéni telemetria standard API-k használatával küldje el.

Támogatjuk a mikrométert, a OpenTelemetry API-t és a népszerű naplózási keretrendszereket. Application Insights a Java 3,0 automatikusan rögzíti a telemetria, és az összes automatikusan összegyűjtött telemetria együtt összekapcsolja azokat.

Ezért jelenleg nem tervezzük Application Insights 3,0-es SDK kiadását.

Application Insights Java 3,0 már figyeli a Application Insights Java SDK 2. x verzióban eljuttatott telemetria. Ez a funkció fontos részét képezi a meglévő 2. x-felhasználók frissítési történetének, és az egyéni telemetria-támogatás fontos hiányosságot tölt be, amíg a OpenTelemetry API nem a GA.

## <a name="sending-custom-telemetry-using-application-insights-java-sdk-2x"></a>Egyéni telemetria küldése Application Insights Java SDK 2. x használatával

Vegye fel az `applicationinsights-core-2.6.0.jar` alkalmazást az alkalmazásba (az összes 2. x verziót támogatja Application Insights Java 3,0, de érdemes a legújabbat használni, ha van ilyen választása):

```xml
  <dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>applicationinsights-core</artifactId>
    <version>2.6.0</version>
  </dependency>
```

Hozzon létre egy TelemetryClient:

  ```java
private static final TelemetryClient telemetryClient = new TelemetryClient();
```

és használja ezt az egyéni telemetria küldéséhez.

### <a name="events"></a>Események

  ```java
telemetryClient.trackEvent("WinGame");
```
### <a name="metrics"></a>Mérőszámok

Metrikus telemetria a [mikrométer](https://micrometer.io)használatával küldhet:

```java
  Counter counter = Metrics.counter("test_counter");
  counter.increment();
```

Emellett a Application Insights Java SDK 2. x verzióját is használhatja:

```java
  telemetryClient.trackMetric("queueLength", 42.0);
```

### <a name="dependencies"></a>Függőségek

```java
  boolean success = false;
  long startTime = System.currentTimeMillis();
  try {
      success = dependency.call();
  } finally {
      long endTime = System.currentTimeMillis();
      RemoteDependencyTelemetry telemetry = new RemoteDependencyTelemetry();
      telemetry.setTimestamp(new Date(startTime));
      telemetry.setDuration(new Duration(endTime - startTime));
      telemetryClient.trackDependency(telemetry);
  }
```

### <a name="logs"></a>Naplók
Az egyéni naplók telemetria a kedvenc naplózási keretrendszerén keresztül is elküldheti.

Emellett a Application Insights Java SDK 2. x verzióját is használhatja:

```java
  telemetryClient.trackTrace(message, SeverityLevel.Warning, properties);
```

### <a name="exceptions"></a>Kivételek
Az egyéni kivételek telemetria a kedvenc naplózási keretrendszerén keresztül is elküldheti.

Emellett a Application Insights Java SDK 2. x verzióját is használhatja:

```java
  try {
      ...
  } catch (Exception e) {
      telemetryClient.trackException(e);
  }
```

## <a name="upgrading-from-application-insights-java-sdk-2x"></a>Frissítés Application Insights Java SDK 2. x verzióról

Ha már használja a Application Insights Java SDK 2. x verzióját az alkalmazásban, nem kell eltávolítania. A Java 3,0-ügynök észlelni fogja, és rögzíti és korrelálja a Java SDK 2. x által küldött összes egyéni telemetria, miközben letiltja a Java SDK 2. x által végrehajtott automatikus gyűjtést a duplikált rögzítés megakadályozása érdekében.

> [!NOTE]
> Megjegyzés: a Java SDK 2. x TelemetryInitializers és TelemetryProcessors nem fog futni az 3,0-ügynök használatakor.
