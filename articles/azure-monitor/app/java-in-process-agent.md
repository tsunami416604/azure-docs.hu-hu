---
title: Azure Monitor Application Insights Java
description: Alkalmazások teljesítményének figyelése bármilyen környezetben futó Java-alkalmazásokhoz programkód módosítása nélkül. Elosztott nyomkövetési és alkalmazás-hozzárendelés.
ms.topic: conceptual
ms.date: 03/29/2020
ms.openlocfilehash: 8423443abac90b87349a4a80fce0ec33a8b686da
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2020
ms.locfileid: "94444741"
---
# <a name="java-codeless-application-monitoring-azure-monitor-application-insights"></a>A Java Code unapplication monitoring Azure monitor Application Insights

A Java kód nélküli alkalmazások figyelése az egyszerűségről szól – a kód módosítása nem változik, a Java-ügynök csak néhány konfigurációs módosítással engedélyezhető.

 A Java-ügynök bármilyen környezetben működik, és lehetővé teszi a Java-alkalmazások figyelését. Más szóval, függetlenül attól, hogy a Java-alkalmazásait virtuális gépeken, a helyszínen, az AK-ban, Windowson, Linuxon futtatja, a Java 3,0-ügynök fogja figyelni az alkalmazást.

Az Application Insights Java SDK hozzáadása az alkalmazáshoz már nem szükséges, mivel az 3,0-ügynök automatikusan gyűjti a kérelmeket, a függőségeket és a naplókat.

Továbbra is küldhet egyéni telemetria az alkalmazásból. Az 3,0-ügynök nyomon követheti és korrelálhatja azt az összes automatikusan összegyűjtött telemetria együtt.

Az 3,0-ügynök támogatja a Java 8-as és újabb verzióit.

## <a name="quickstart"></a>Gyorsútmutató

**1. Töltse le az ügynököt**

> [!WARNING]
> **Ha 3,0 előzetes verzióról frissít**
>
> Figyelmesen tekintse át az összes [konfigurációs beállítást](./java-standalone-config.md) , mivel a JSON-struktúra teljes mértékben megváltozott, és a fájlneven kívül minden kisbetűt ment.

A [applicationinsights-Agent-3.0.0. jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.0/applicationinsights-agent-3.0.0.jar) letöltése

**2. irányítsa a JVM az ügynökre**

Hozzáadás `-javaagent:path/to/applicationinsights-agent-3.0.0.jar` az alkalmazás JVM argumentumai

A tipikus JVM argumentumok közé tartoznak `-Xmx512m` a és a `-XX:+UseG1GC` . Tehát ha tudja, hol adja hozzá ezeket, akkor már tudja, hová adja hozzá ezt a lehetőséget.

Ha további segítségre van az alkalmazás JVM argumentumok konfigurálásához, tekintse meg a [Tippek a JVM-argumentumok frissítéséhez](./java-standalone-arguments.md)című témakört.

**3. irányítsa az ügynököt a Application Insights erőforrásra**

Ha még nem rendelkezik Application Insights erőforrással, létrehozhat egy újat az [Erőforrás-létrehozási útmutató](./create-new-resource.md)lépéseit követve.

Mutasson az ügynököt a Application Insights erőforrásra, vagy egy környezeti változó beállításával:

```
APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=...
```

Vagy hozzon létre egy nevű konfigurációs fájlt `applicationinsights.json` , és helyezze ugyanabba a könyvtárba `applicationinsights-agent-3.0.0.jar` , a következő tartalommal:

```json
{
  "connectionString": "InstrumentationKey=..."
}
```

A Application Insights erőforrásban található a kapcsolatok karakterlánca:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights a kapcsolatok karakterlánca":::

**4. ennyi!**

Most indítsa el az alkalmazást, és nyissa meg a Application Insights erőforrást a Azure Portalban a figyelési adatai megtekintéséhez.

> [!NOTE]
> Eltarthat néhány percig, amíg a figyelési adatai megjelennek a portálon.


## <a name="configuration-options"></a>Beállítási lehetőségek

A `applicationinsights.json` fájlban emellett a következőket is konfigurálhatja:

* Felhőbeli szerepkör neve
* Felhőalapú szerepkör-példány
* Mintavételezés
* JMX metrikák
* Egyéni dimenziók
* Telemetria processzorok (előzetes verzió)
* Automatikusan összegyűjtött naplózás
* Automatikusan összegyűjtött mérőműszer-metrikák (beleértve a Spring boot indítószerkezet metrikáit)
* Szívverés
* HTTP-proxy
* Öndiagnosztika

További részletek: [konfigurációs beállítások](./java-standalone-config.md) .

## <a name="auto-collected-requests-dependencies-logs-and-metrics"></a>Automatikusan összegyűjtött kérelmek, függőségek, naplók és metrikák

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
* Log4j (beleértve a MDC tulajdonságokat)
* SLF4J/Logback (beleértve a MDC-tulajdonságokat)

### <a name="metrics"></a>Mérőszámok

* Mikrométer (beleértve a Spring boot indítószerkezet metrikáit)
* JMX metrikák

## <a name="sending-custom-telemetry-from-your-application"></a>Egyéni telemetria küldése az alkalmazásból

A 3.0-s célunk, hogy az egyéni telemetria standard API-k használatával küldje el.

Támogatjuk a mikrométert, a OpenTelemetry API-t és a népszerű naplózási keretrendszereket. Application Insights a Java 3,0 automatikusan rögzíti a telemetria, és összekapcsolja azt az összes automatikusan összegyűjtött telemetria együtt.

### <a name="supported-custom-telemetry"></a>Támogatott egyéni telemetria

Az alábbi táblázat a jelenleg támogatott egyéni telemetria-típusokat jeleníti meg, amelyeket a Java 3,0-ügynök kiegészítéseként engedélyezhet. Az összegzéshez az egyéni metrikákat a Mikrométerek támogatják, az egyéni kivételek és a nyomkövetési funkciók a naplózási keretrendszereken keresztül engedélyezhetők, és az egyéni telemetria bármilyen típusa támogatott a [Application Insights Java 2. x SDK](#sending-custom-telemetry-using-application-insights-java-sdk-2x)-n keresztül. 

|                     | Mikrométer | Log4j, logback, JUL | 2. x SDK |
|---------------------|------------|---------------------|---------|
| **Egyéni események**   |            |                     |  Yes    |
| **Egyéni metrikák**  |  Igen       |                     |  Yes    |
| **Függőségek**    |            |                     |  Yes    |
| **Kivételek**      |            |  Igen                |  Yes    |
| **Lapok nézetei**      |            |                     |  Yes    |
| **Kérelmek**        |            |                     |  Yes    |
| **Hívásláncok**          |            |  Igen                |  Yes    |

Jelenleg nem tervezzük Application Insights 3,0-es SDK kiadását.

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

Ha már használja a Application Insights Java SDK 2. x verzióját az alkalmazásban, nem kell eltávolítania.
A Java 3,0-ügynök észlelni fogja, és rögzíti és korrelálja a Java SDK 2. x által küldött összes egyéni telemetria, miközben letiltja a Java SDK 2. x által végrehajtott automatikus gyűjtést a duplikált telemetria elkerülése érdekében.

Ha Application Insights 2. x ügynököt használta, el kell távolítania a JVM ARG-t, `-javaagent:` amely a 2. x ügynökre mutat.

> [!NOTE]
> A Java SDK 2. x TelemetryInitializers és TelemetryProcessors nem fog futni az 3,0-ügynök használata esetén.
> A korábban megkövetelt használati esetek többsége 3,0-ban oldható meg az [Egyéni dimenziók](./java-standalone-config.md#custom-dimensions) konfigurálásával vagy a [telemetria processzorok](./java-standalone-telemetry-processors.md)konfigurálásával.

> [!NOTE]
> a 3,0 egyetlen JVM még nem támogatja több rendszerállapotú kulcs használatát.
