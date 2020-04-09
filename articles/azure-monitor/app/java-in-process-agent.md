---
title: Java-alkalmazások figyelése bármilyen környezetben – Azure Monitor Application Insights
description: Alkalmazásteljesítmény-figyelés e környezetben futó Java-alkalmazások számára az alkalmazás programozása nélkül. Elosztott nyomkövetés és alkalmazástérkép.
ms.topic: conceptual
ms.date: 03/29/2020
ms.openlocfilehash: 5a62be45320523ee0577d56eb557a4f87a58a1cc
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886857"
---
# <a name="java-codeless-application-monitoring-azure-monitor-application-insights---public-preview"></a>Java kód nélküli alkalmazásfigyelés az Azure Monitor Application Insights - nyilvános előzetes verzió

Java kód nélküli alkalmazás figyelés szól egyszerűség - nincsenek kódváltozások, a Java ügynök lehet engedélyezni keresztül csak egy pár konfigurációs változások.

 A Java ügynök működik bármilyen környezetben, és lehetővé teszi, hogy figyelemmel kíséri az összes Java alkalmazások. Más szóval, hogy a Java-alkalmazásokat virtuális gépeken, a helyszínen, az AKS-ben, a Windows, Linux - a neve, a Java 3.0-ügynök figyeli az alkalmazást.

Az Application Insights Java SDK hozzáadása az alkalmazáshoz már nem szükséges, mivel a 3.0-s ügynök automatikusan gyűjti a kérelmeket, függőségeket és naplózza az összes saját.

Továbbra is küldhet egyéni telemetriát az alkalmazásból. A 3.0-s ügynök nyomon követi és korrelálja azt az összes automatikusan gyűjtött telemetriai adatokkal együtt.

## <a name="quickstart"></a>Első lépések

**1. Töltse le az ügynököt**

[Alkalmazásinsights-agent-3.0.0-PREVIEW.2.jar letöltése](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.0-PREVIEW.2/applicationinsights-agent-3.0.0-PREVIEW.2.jar)

**2. Mutassa a JVM-et az ügynöknek**

Az `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.2.jar` alkalmazás JVM-argjainak hozzáadása

Tipikus JVM args közé tartozik, `-Xmx512m` és `-XX:+UseG1GC`. Tehát, ha tudod, hol kell hozzáadni ezeket, akkor már tudja, hol kell hozzáadni ezt.

Az alkalmazás JVM-argjainak konfigurálásával kapcsolatban további segítséget a [JVM-argok frissítéséhez lásd: 3.0 Előzetes verzió: Tippek a JVM-argok frissítéséhez.](https://github.com/microsoft/ApplicationInsights-Java/wiki/3.0-Preview:-Tips-for-updating-your-JVM-args)

**3. Irányítsa az ügynököt az Application Insights-erőforrásra**

Ha még nem rendelkezik Application Insights-erőforrással, az [erőforrás-létrehozási útmutató](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)lépéseit követve létrehozhat egy újat.
Hozzon létre `ApplicationInsights.json`egy , és helyezze ugyanabba a könyvtárba, mint `applicationinsights-agent-3.0.0-PREVIEW.2.jar`a következő tartalommal:

```json
{
  "instrumentationSettings": {
    "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000"
  }
}
```

A kapcsolati karakterláncot az Application Insights-erőforrásban találja meg:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights-kapcsolati karakterlánc":::

**4. Ez az!**

Most indítsa el az alkalmazást, és az Azure Insights-erőforrás az Azure Portalon a figyelési adatok megtekintéséhez.

> [!NOTE]
> Eltarthat néhány percig, amíg a figyelési adatok megjelennek a portálon.


## <a name="configuration-options"></a>Beállítási lehetőségek

A `ApplicationInsights.json` fájlban a következőket is beállíthatja:

* Felhőbeli szerepkör neve
* Felhőalapú szerepkörpéldány
* Alkalmazásnapló-rögzítés
* JMX-mutatók
* Mikrométer
* Szívverés
* Mintavételezés
* HTTP-proxy
* Öndiagnosztika

Részletek a [3.0 Nyilvános előzetes verzió: Konfigurációs beállítások](https://github.com/microsoft/ApplicationInsights-Java/wiki/3.0-Preview:-Configuration-Options).

## <a name="autocollected-requests-dependencies-logs-and-metrics"></a>Automatikusan begyűjtött kérelmek, függőségek, naplók és metrikák

### <a name="requests"></a>Kérelmek

* JMS fogyasztók
* Kafka fogyasztók
* Netty/WebFlux
* Servlets között
* Tavaszi menetrend

### <a name="dependencies-with-distributed-trace-propagation"></a>Függőségek elosztott nyomkövetés propagálásával

* Apache HttpClient és HttpAsyncClient
* gRPC
* java.net.HttpURLKapcsolat
* Jms
* Kafka
* Netty ügyfél
* OkHttp

### <a name="other-dependencies"></a>Egyéb függőségek

* Cassandra
* JDBC
* MongoDB (aszinkron és szinkronizálás)
* Redis (Saláta és Jedi)

### <a name="logs"></a>Naplók

* java.util.logging
* Log4j
* SLF4J/Logback

### <a name="metrics"></a>Mérőszámok

* Mikrométer
* JMX mérőszámok

## <a name="sending-custom-telemetry-from-your-application"></a>Egyéni telemetriai adatok küldése az alkalmazásból

A 3.0+ -ban az a célunk, hogy lehetővé tegyék az egyéni telemetriai adatokat a szabványos API-k használatával.

Támogatjuk a Micrometer, OpenTelemetry API-t és a népszerű naplózási keretrendszereket. Az Application Insights Java 3.0 automatikusan rögzíti a telemetriai adatokat, és korrelálja azt az összes automatikusan gyűjtött telemetriai adatokkal együtt.

Emiatt jelenleg nem tervezünk egy SDK-t az Application Insights 3.0-s kiadására.

Az Application Insights Java 3.0 már figyeli az Application Insights Java SDK 2.x-nek küldött telemetriai adatokat. Ez a funkció fontos része a frissítési történet a meglévő 2.x felhasználók számára, és kitölti egy fontos rés az egyéni telemetriai támogatás, amíg az OpenTelemetry API GA.

## <a name="sending-custom-telemetry-using-application-insights-java-sdk-2x"></a>Egyéni telemetriai adatok küldése az Application Insights Java SDK 2.x használatával

Adja `applicationinsights-core-2.6.0.jar` hozzá az alkalmazáshoz (az Application Insights Java 3.0 támogatja az összes 2.x verziót, de érdemes a legújabbat használni, ha van választása):

```xml
  <dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>applicationinsights-core</artifactId>
    <version>2.6.0</version>
  </dependency>
```

Telemetriai ügyfél létrehozása:

  ```java
private static final TelemetryClient telemetryClient = new TelemetryClient();
```

és használja, hogy az egyéni telemetriai adatok küldéséhez.

### <a name="events"></a>Események

  ```java
telemetryClient.trackEvent("WinGame");
```
### <a name="metrics"></a>Mérőszámok

Metrika telemetriai adatokat [mikrométeren](https://micrometer.io)keresztül küldhet:

```java
  Counter counter = Metrics.counter("test_counter");
  counter.increment();
```

Vagy használhatja az Application Insights Java SDK 2.x-et is:

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
Egyéni naplótelemetriát küldhet a kedvenc naplózási keretrendszeren keresztül.

Vagy használhatja az Application Insights Java SDK 2.x-et is:

```java
  telemetryClient.trackTrace(message, SeverityLevel.Warning, properties);
```

### <a name="exceptions"></a>Kivételek
Egyéni kivételtelemetriai adatokat küldhet a kedvenc naplózási keretrendszeren keresztül.

Vagy használhatja az Application Insights Java SDK 2.x-et is:

```java
  try {
      ...
  } catch (Exception e) {
      telemetryClient.trackException(e);
  }
```

## <a name="upgrading-from-application-insights-java-sdk-2x"></a>Frissítés az Application Insights Java SDK 2.x-ről

Ha már használja az Application Insights Java SDK 2.x az alkalmazásban, nincs szükség annak eltávolítására. A Java 3.0-ügynök észleli azt, és rögzíti és korrelálja a Java SDK 2.x-en keresztül küldött egyéni telemetriai adatokat, miközben letiltja a Java SDK 2.x által végrehajtott automatikus adatgyűjtést az ismétlődő rögzítés megakadályozása érdekében.

> [!NOTE]
> Megjegyzés: A Java SDK 2.x TelemettryInitializers és TelemetryProcessors nem fog futni a 3.0-s ügynök használatakor.
