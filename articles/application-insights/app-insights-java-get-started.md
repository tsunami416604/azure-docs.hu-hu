---
title: Java webalkalmazás elemzése az Azure Application Insights használatával | Microsoft Docs
description: 'Alkalmazásteljesítmény-figyelés Java-webalkalmazásokhoz az Application Insights használatával. '
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: 051d4285-f38a-45d8-ad8a-45c3be828d91
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 09/19/2018
ms.author: mbullwin
ms.openlocfilehash: 093124432314472da06065fad3a7cdff0f558d22
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46999817"
---
# <a name="get-started-with-application-insights-in-a-java-web-project"></a>Ismerkedés az Application Insights szolgáltatással Java webes projektben


Az [Application Insights](https://azure.microsoft.com/services/application-insights/) egy bővíthető elemzési szolgáltatás a webfejlesztők számára, amely segít megérteni az élő alkalmazása teljesítményét és használatát. Ezzel [észlelheti és diagnosztizálhatja a teljesítménnyel kapcsolatos hibákat és kivételeket](app-insights-detect-triage-diagnose.md), és [kódot][api] írhat annak követéséhez, hogy a felhasználók hogyan használják az alkalmazást.

![Képernyőkép a mintaadatok áttekintése](./media/app-insights-java-get-started/overview-graphs.png)

Az Application Insights a Linux, Unix vagy Windows rendszeren futó Java alkalmazásokat támogatja.

A következők szükségesek:

* A JRE 1.7-es vagy 1.8-as verziója
* Egy [Microsoft Azure](https://azure.microsoft.com/)-előfizetés.

*Ha már élő webalkalmazása van, az alternatív eljárást követve [hozzáadhatja az SDK-t a futásidőben a webkiszolgálón](app-insights-java-live.md). Ezzel az alternatívával nem kell újraépítenie a kódot, de nem tud kódot írni a felhasználói tevékenységek követése érdekében.*

Ha a Spring keretrendszert részesíti előnyben, tekintse meg a [Spring Boot inicializáló alkalmazás konfigurálása az Application Insights használatához](https://docs.microsoft.com/java/azure/spring-framework/configure-spring-boot-java-applicationinsights) című útmutatót.

## <a name="1-get-an-application-insights-instrumentation-key"></a>1. Application Insights-kialakítási kulcs beszerzése
1. Jelentkezzen be a [Microsoft Azure Portalra](https://portal.azure.com).
2. Hozzon létre egy Application Insights-erőforrást. Állítsa be a Java webalkalmazás alkalmazástípust.

    ![Adjon meg egy nevet, válassza ki a Java webalkalmazást, és kattintson a Létrehozás gombra.](./media/app-insights-java-get-started/02-create.png)
3. Keresse meg az új erőforrás kialakítási kulcsát. Ezt a kulcsot nemsokára a kódprojektbe kell illesztenie.

    ![Az új erőforrás áttekintésében kattintson a Tulajdonságok gombra, és másolja le a kialakítási kulcsot](./media/app-insights-java-get-started/03-key.png)

## <a name="2-add-the-application-insights-sdk-for-java-to-your-project"></a>2. A Javához készült Application Insights SDK hozzáadása a projekthez
*Válassza ki a projektnek megfelelő módszert.*

#### <a name="if-youre-using-maven-a-namemaven-setup-"></a>Ha Mavent használ... <a name="maven-setup" />
Ha a projekt már úgy van beállítva, hogy Mavent használ buildként, egyesítse a következő kódot a pom.xml fájllal.

Ezután frissítse a projektfüggőségeket, hogy letöltse a bináris fájlokat.

```XML

    <repositories>
       <repository>
          <id>central</id>
          <name>Central</name>
          <url>http://repo1.maven.org/maven2</url>
       </repository>
    </repositories>

    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-web</artifactId>
        <!-- or applicationinsights-core for bare API -->
        <version>[2.0,)</version>
      </dependency>
    </dependencies>
```

* *Build- vagy ellenőrzőösszeg-érvényesítési hibák?* Próbáljon egy adott verziót használni, például a következőt: `<version>2.0.n</version>`. A legújabb verziót az [SDK kiadási megjegyzéseiben](https://github.com/Microsoft/ApplicationInsights-Java#release-notes) vagy a [Maven-összetevőkben](http://search.maven.org/#search%7Cga%7C1%7Capplicationinsights) találja.
* *Új SDK-ra kell frissítenie?* Frissítse a projekt függőségeit.

#### <a name="if-youre-using-gradle-a-namegradle-setup-"></a>Ha Gradle-t használ... <a name="gradle-setup" />
Ha a projekt már úgy van beállítva, hogy Gradle-t használ buildként, egyesítse a következő kódot a build.gradle fájllal.

Ezután frissítse a projektfüggőségeket, hogy letöltse a bináris fájlokat.

```gradle

    repositories {
      mavenCentral()
    }

    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web', version: '2.+'
      // or applicationinsights-core for bare API
    }
```

#### <a name="if-youre-using-eclipse-to-create-a-dynamic-web-project-"></a>Ha az Eclipse-t használja dinamikus webes projekt létrehozásához...
Használja a [Javához készült Application Insights SDK beépülő modult][eclipse]. Megjegyzés: Annak ellenére, hogy ezzel a beépülő modullal gyorsabban kezdheti el az Application Insights használatát (feltéve, hogy nem használ Mavent/Gradle-t), ez nem függőségkezelési rendszer. A beépülő modul frissítése ezért nem frissíti automatikusan a projektben lévő Application Insights-kódtárakat.

* *Build- vagy ellenőrzőösszeg-érvényesítési hibák?* Próbáljon egy adott verziót használni, például a következőt: `version:'2.0.n'`. A legújabb verziót az [SDK kiadási megjegyzéseiben](https://github.com/Microsoft/ApplicationInsights-Java#release-notes) vagy a [Maven-összetevőkben](http://search.maven.org/#search%7Cga%7C1%7Capplicationinsights) találja.
* *Frissítés új SDK-ra* A projekt függőségeinek frissítése.

#### <a name="otherwise-if-you-are-manually-managing-dependencies-"></a>Egyéb esetben, ha manuálisan kezeli a függőségeket...
Töltse le a [legújabb verziót](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest), és a korábbi verziókat felülírva másolja a szükséges fájlokat a projektbe.

### <a name="questions"></a>Kérdések...
* *Mi a kapcsolat a `-core` és a `-web` összetevő között?*
  * `applicationinsights-core`– csak az API-t biztosítja. Erre az összetevőre mindig szüksége van.
  * `applicationinsights-web`– olyan mérőszámokat biztosít, amelyek nyomon követik a HTTP-kérések számát és a válaszidőket. Ezt az összetevőt kihagyhatja, ha nem szeretné automatikusan gyűjteni ezt a telemetriát. hanem például sajátot szeretne írni.
  
* *Hogyan frissíthetek az SDK legújabb verziójára?*
  * Ha Gradle-t vagy Mavent használ...
    * Frissítse a buildfájlt, hogy a legújabb verziót mutassa, vagy a Gradle/Maven helyettesítő szintaxisa segítségével vegye fel automatikusan a legfrissebb verziót. Ezután frissítse a projekt függőségeit. A fenti példákban láthatja a [Gradle](#gradle-setup) és a [Maven](#maven-setup) helyettesítő szintaxisát.
  * Ha manuálisan kezeli a függőségeket...
    * Töltse le a legújabb [Javához készült Application Insights SDK-t](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest), és cserélje le a régieket. A változások leírását az [SDK kiadási megjegyzéseiben](https://github.com/Microsoft/ApplicationInsights-Java#release-notes) találja.

## <a name="3-add-an-applicationinsightsxml-file"></a>3. ApplicationInsights.xml fájl hozzáadása
Adja az ApplicationInsights.xml fájlt a projekt erőforrások mappájához, vagy győződjön meg arról, hogy a projekt üzembe helyezési osztályának elérési útjához van adva. Másolja bele a következő XML-t.

Helyettesítse be az Azure Portalról kapott kialakítási kulcsot.

```XML

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">


      <!-- The key from the portal: -->
      <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>


      <!-- HTTP request component (not required for bare API) -->
      <TelemetryModules>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
      </TelemetryModules>

      <!-- Events correlation (not required for bare API) -->
      <!-- These initializers add context data to each event -->

      <TelemetryInitializers>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>

      </TelemetryInitializers>
    </ApplicationInsights>
```

A konfigurációs fájlt bármely, az alkalmazás számára elérhető helyen tárolhatja.  A `-Dapplicationinsights.configurationDirectory` rendszertulajdonság határozza meg, hogy melyik tár tartalmazza az ApplicationInsights.xml fájlt. Az `E:\myconfigs\appinsights\ApplicationInsights.xml` mappában tárolt konfigurációs fájl konfigurálásához például a `-Dapplicationinsights.configurationDirectory="E:\myconfigs\appinsights"` tulajdonság használható.

* A kialakítási kulcsot a telemetria minden elemével megkapja, és ez közli az Application Insights eszközzel, hogy megjelenítse azt az erőforrásban.
* A HTTP-kérelemösszetevő nem kötelező. Automatikusan telemetriát küld a kérelmekkel és válaszidőkkel kapcsolatban a portálra.
* Az eseménykorreláció a HTTP-kérelemösszetevő további eleme. Azonosítót rendel a kiszolgáló által fogadott összes kérelemhez, és az azonosítót „Operation.Id” tulajdonságként hozzáadja a telemetria minden eleméhez. Lehetővé teszi az egyes kérelmekkel társított telemetria korrelációját, ha beállít egy szűrőt a [diagnosztikai keresésben][diagnostic].

### <a name="alternative-ways-to-set-the-instrumentation-key"></a>A kialakítási kulcs beállításának egyéb módjai
Az Application Insights SDK ebben a sorrendben keresi a kulcsot:

1. Rendszertulajdonság: -DAPPLICATION_INSIGHTS_IKEY=saját_kialakítási_kulcs
2. Környezeti változó: APPLICATION_INSIGHTS_IKEY
3. Konfigurációs fájl: ApplicationInsights.xml

[Beállíthatja a programkódban](app-insights-api-custom-events-metrics.md#ikey) is:

```Java
    TelemetryConfiguration.getActive().setInstrumentationKey(iKey);
```

## <a name="4-add-an-http-filter"></a>4. HTTP-szűrő hozzáadása
Az utolsó konfigurációs lépéssel a HTTP-kérelemösszetevő mindegyik webes kérelmet naplózhatja. (Nem szükséges, ha csak az API-ra van szüksége.)

### <a name="spring-boot-applications"></a>Spring Boot-alkalmazások
Regisztrálja az Application Insights `WebRequestTrackingFilter` szűrőt a konfigurációs osztályban:

```Java
package devCamp.WebApp.configurations;

    import javax.servlet.Filter;

    import org.springframework.boot.web.servlet.FilterRegistrationBean;
    import org.springframework.context.annotation.Bean;
    import org.springframework.core.Ordered;
    import org.springframework.beans.factory.annotation.Value;
    import org.springframework.context.annotation.Configuration;
    import com.microsoft.applicationinsights.TelemetryConfiguration;
    import com.microsoft.applicationinsights.web.internal.WebRequestTrackingFilter;


    @Configuration
    public class AppInsightsConfig {

    //Initialize AI TelemetryConfiguration via Spring Beans
        @Bean
        public String telemetryConfig() {
            String telemetryKey = System.getenv("APPLICATION_INSIGHTS_IKEY");
            if (telemetryKey != null) {
                TelemetryConfiguration.getActive().setInstrumentationKey(telemetryKey);
            }
            return telemetryKey;
        }
    
    //Set AI Web Request Tracking Filter
        @Bean
        public FilterRegistrationBean aiFilterRegistration(@Value("${spring.application.name:application}") String applicationName) {
           FilterRegistrationBean registration = new FilterRegistrationBean();
           registration.setFilter(new WebRequestTrackingFilter(applicationName));
           registration.setName("webRequestTrackingFilter");
           registration.addUrlPatterns("/*");
           registration.setOrder(Ordered.HIGHEST_PRECEDENCE + 10);
           return registration;
       } 

    //Set up AI Web Request Tracking Filter
        @Bean(name = "WebRequestTrackingFilter")
        public Filter webRequestTrackingFilter(@Value("${spring.application.name:application}") String applicationName) {
            return new WebRequestTrackingFilter(applicationName);
        }   
    }
```

> [!NOTE]
> Ha a Spring Boot 1.3.8-as vagy régebbi verzióját használja, a FilterRegistrationBean helyére írja be az alábbi sort

```Java
    import org.springframework.boot.context.embedded.FilterRegistrationBean;
```

Ez az osztály úgy konfigurálja a `WebRequestTrackingFilter` szűrőt, hogy ez legyen a HTTP-szűrőlánc első szűrője. Emellett a kialakítási kulcsot is lekéri az operációsrendszer-változóból, ha elérhető.

> A Spring MVC-konfiguráció helyett a webes HTTP-szűrőkonfigurációt használjuk, mert ez egy Spring Boot-alkalmazás, és saját Spring MVC-konfigurációval rendelkezik. Az alábbi szakaszokban találja a Spring MVC-re jellemző konfigurációt.


### <a name="applications-using-webxml"></a>Web.xml fájlt használó alkalmazások
Keresse meg és nyissa meg a web.xml fájlt a projektben, és egyesítse a következő kódot azon webalkalmazás-csomópont alatt, ahol az alkalmazás szűrői konfigurálva vannak.

A legpontosabb eredmények érdekében le kell képezni a szűrőt az összes többi szűrő előtt.

```XML

    <filter>
      <filter-name>ApplicationInsightsWebFilter</filter-name>
      <filter-class>
        com.microsoft.applicationinsights.web.internal.WebRequestTrackingFilter
      </filter-class>
    </filter>
    <filter-mapping>
       <filter-name>ApplicationInsightsWebFilter</filter-name>
       <url-pattern>/*</url-pattern>
    </filter-mapping>
```

#### <a name="if-youre-using-spring-web-mvc-31-or-later"></a>Ha a Spring Web MVC 3.1-es vagy újabb verzióját használja
Szerkessze úgy ezeket az elemeket a *-servlet.xml fájlban, hogy tartalmazzák az Application Insights-csomagot:

```XML

    <context:component-scan base-package=" com.springapp.mvc, com.microsoft.applicationinsights.web.spring"/>

    <mvc:interceptors>
        <mvc:interceptor>
            <mvc:mapping path="/**"/>
            <bean class="com.microsoft.applicationinsights.web.spring.RequestNameHandlerInterceptorAdapter" />
        </mvc:interceptor>
    </mvc:interceptors>
```

#### <a name="if-youre-using-struts-2"></a>Ha Struts 2-t használ
Adja ezt az elemet a Struts konfigurációs fájlhoz (általában struts.xml vagy struts-default.xml a neve):

```XML

     <interceptors>
       <interceptor name="ApplicationInsightsRequestNameInterceptor" class="com.microsoft.applicationinsights.web.struts.RequestNameInterceptor" />
     </interceptors>
     <default-interceptor-ref name="ApplicationInsightsRequestNameInterceptor" />
```

Ha egy alapértelmezett veremben elfogók vannak meghatározva, az elfogó a veremhez adható.

## <a name="5-run-your-application"></a>5. Az alkalmazás futtatása
Futtassa hibakeresés módban a fejlesztési számítógépén, vagy tegye közzé a kiszolgálóján.

## <a name="6-view-your-telemetry-in-application-insights"></a>6. A telemetria megtekintése az Application Insights szolgáltatásban
Térjen vissza az Application Insights-erőforráshoz a [Microsoft Azure Portalon](https://portal.azure.com).

A HTTP-kérelemadatok az áttekintési panelen jelennek meg. (Ha nincsenek ott, várjon néhány másodpercig, majd kattintson a Frissítés gombra.)

![mintaadatok](./media/app-insights-java-get-started/5-results.png)

[További információk a metrikákról.][metrics]

Részletesebb összesített mérőszámokért kattintson bármelyik diagramra.

![](./media/app-insights-java-get-started/6-barchart.png)

> Az Application Insights feltételezi, hogy az MVC alkalmazások HTTP-kérelmeinek formátuma a következő: `VERB controller/action`. Például a `GET Home/Product/f9anuh81`, a `GET Home/Product/2dffwrf5` és a `GET Home/Product/sdf96vws` a következőbe van csoportosítva: `GET Home/Product`. Ez a csoportosítás lehetővé teszi a kérelmek fontos információkat biztosító összesítéseit, például a kérelmek számának és a kérelmek átlagos végrehajtási idejének meghatározását.
>
>

### <a name="instance-data"></a>Példányadatok
Kattintson az adott kérelemtípusokra az egyes példányok megtekintéséhez.

Két adattípus jelenik meg az Application Insights szolgáltatásban: összesített adatok, tárolva és átlagokként, számokként és összegekként megjelenítve; és példányadatok – a HTTP-kérelmek, kivételek, lapmegtekintések vagy egyéni események egyedi jelentései.

Kérelem tulajdonságainak megtekintésekor láthatja az ahhoz társított telemetriaeseményeket, például a kérelmeket és kivételeket.

![](./media/app-insights-java-get-started/7-instance.png)

### <a name="analytics-powerful-query-language"></a>Elemzés: Erőteljes lekérdezési nyelv
Ahogy egyre több adatot gyűjt össze, lekérdezéseket futtathat az adatok összegzéséhez és egyéni példányok megkereséséhez is.  Az [elemzés](app-insights-analytics.md) erőteljes eszköz a teljesítmény és a használat megértéséhez és diagnosztikai célokra is.

![Példa elemzésre](./media/app-insights-java-get-started/025.png)

## <a name="7-install-your-app-on-the-server"></a>7. Az alkalmazás telepítése a kiszolgálón
Most tegye közzé az alkalmazást a kiszolgálón, hagyja, hogy mások használják, és nézze, ahogyan a telemetria megjelenik a portálon.

* Győződjön meg arról, hogy a tűzfal lehetővé teszi, hogy az alkalmazás telemetriát küldjön ezekre a portokra:

  * dc.services.visualstudio.com:443
  * f5.services.visualstudio.com:443

* Ha a kimenő forgalmat át kell irányítani egy tűzfalon, adja meg a `http.proxyHost` és a `http.proxyPort` rendszertulajdonságot.

* Windows-kiszolgálókon telepítse a következőt:

  * [Microsoft Visual C++ újraterjeszthető csomag](http://www.microsoft.com/download/details.aspx?id=40784)

    (Ez az összetevő lehetővé teszi a teljesítményszámlálókat.)


## <a name="exceptions-and-request-failures"></a>Kivételek és kérelemhibák
A rendszer a nem kezelt kivételeket automatikusan begyűjti:

![Beállítások megnyitása, Hibák](./media/app-insights-java-get-started/21-exceptions.png)

Adatok és más kivételek gyűjtésére két lehetősége van:

* [Szúrja be a trackException() hívásait a kódba][apiexceptions].
* [Telepítse a Java ügynököt a kiszolgálón](app-insights-java-agent.md). Válassza ki a megtekinteni kívánt metódusokat.

## <a name="monitor-method-calls-and-external-dependencies"></a>Metódushívások és külső függőségek megfigyelése
[Telepítse a Java ügynököt](app-insights-java-agent.md) a megadott belső módszerek és a JDBC-n keresztül végzett hívások naplózásához, időzítési adatokkal.

## <a name="performance-counters"></a>Teljesítményszámlálók
Nyissa meg a **Beállítások**, **Kiszolgálók** elemet, ahol láthatja a teljesítményszámlálók készletét.

![](./media/app-insights-java-get-started/11-perf-counters.png)

### <a name="customize-performance-counter-collection"></a>Teljesítményszámláló-gyűjtemény testreszabása
A teljesítményszámlálók standard készlete gyűjtésének letiltásához adja a következő kódot az ApplicationInsights.xml fájl gyökércsomópontja alatt:

```XML
    <PerformanceCounters>
       <UseBuiltIn>False</UseBuiltIn>
    </PerformanceCounters>
```

### <a name="collect-additional-performance-counters"></a>További teljesítményszámlálók gyűjtése
További gyűjtendő teljesítményszámlálókat határozhat meg.

#### <a name="jmx-counters-exposed-by-the-java-virtual-machine"></a>JMX-számlálók (a Java virtuális gép által feltárt)

```XML
    <PerformanceCounters>
      <Jmx>
        <Add objectName="java.lang:type=ClassLoading" attribute="TotalLoadedClassCount" displayName="Loaded Class Count"/>
        <Add objectName="java.lang:type=Memory" attribute="HeapMemoryUsage.used" displayName="Heap Memory Usage-used" type="composite"/>
      </Jmx>
    </PerformanceCounters>
```

* `displayName`– Az Application Insights portálon megjelenő név.
* `objectName`– A JMX objektum neve.
* `attribute`– A JMX objektum nevének lehívni kívánt attribútuma
* `type`(választható) – A JMX objektum attribútumának típusa:
  * Alapértelmezett: egyszerű típus, például int vagy long.
  * `composite`: a teljesítményszámláló-adatok az „Attribútum.Adat” formátumban szerepelnek
  * `tabular`: a teljesítményszámláló-adatok táblázatsor formájában szerepelnek

#### <a name="windows-performance-counters"></a>Windows-teljesítményszámlálók
Mindegyik [Windows-teljesítményszámláló](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) egy kategória tagja (ugyanúgy, ahogyan a mezők osztályok tagjai). A kategóriák globálisak lehetnek, vagy számozott vagy elnevezett példányokkal rendelkezhetnek.

```XML
    <PerformanceCounters>
      <Windows>
        <Add displayName="Process User Time" categoryName="Process" counterName="%User Time" instanceName="__SELF__" />
        <Add displayName="Bytes Printed per Second" categoryName="Print Queue" counterName="Bytes Printed/sec" instanceName="Fax" />
      </Windows>
    </PerformanceCounters>
```

* displayName – Az Application Insights portálon megjelenő név.
* categoryName – A teljesítményszámláló kategóriája (teljesítményobjektum), amelyhez ez a teljesítményszámláló társítva van.
* counterName – A teljesítményszámláló neve.
* instanceName – A teljesítményszámláló-kategória példányneve, vagy üres sztring („”), ha a kategória egyetlen példányt tartalmaz. Ha a categoryName Folyamat, és a gyűjteni kívánt teljesítményszámláló az aktuális JVM folyamatról származik, amelyen az alkalmazása fut, adja meg a következőt: `"__SELF__"`.

A teljesítményszámlálói egyéni mérőszámokként láthatók a [Metrikaböngészőben][metrics].

![](./media/app-insights-java-get-started/12-custom-perfs.png)

### <a name="unix-performance-counters"></a>Unix-teljesítményszámlálók
* [Telepítse a gyűjteményt az Application Insights beépülő modullal](app-insights-java-collectd.md) számos rendszer- és hálózati adat lekéréséhez.

## <a name="local-forwarder"></a>Helyi továbbító

[Helyi továbbító](https://docs.microsoft.com/azure/application-insights/local-forwarder) olyan ügynök, amely gyűjti az Application Insights vagy [OpenCensus](https://opencensus.io/) használati adatok gyűjtése a különböző SDK-k és keretrendszereket és továbbítja azt az Application Insights. Windows és Linux alatt alkalmas állapotban.

```xml
<Channel type="com.microsoft.applicationinsights.channel.concrete.localforwarder.LocalForwarderTelemetryChannel">
<DeveloperMode>false</DeveloperMode>
<EndpointAddress><!-- put the hostname:port of your LocalForwarder instance here --></EndpointAddress>
<!-- The properties below are optional. The values shown are the defaults for each property -->
<FlushIntervalInSeconds>5</FlushIntervalInSeconds><!-- must be between [1, 500]. values outside the bound will be rounded to nearest bound -->
<MaxTelemetryBufferCapacity>500</MaxTelemetryBufferCapacity><!-- units=number of telemetry items; must be between [1, 1000] -->
</Channel>
```

SpringBoot alapszintű használja, ha a konfigurációs fájl (application.properies) adja hozzá a következő:

```yml
azure.application-insights.channel.local-forwarder.endpoint-address=<!--put the hostname:port of your LocalForwarder instance here-->
azure.application-insights.channel.local-forwarder.flush-interval-in-seconds=<!--optional-->
azure.application-insights.channel.local-forwarder.max-telemetry-buffer-capacity=<!--optional-->
```

Alapértelmezett értékek azonosak SpringBoot application.properties és applicationinsights.xml konfigurációját.

## <a name="get-user-and-session-data"></a>Felhasználói és munkamenetadatok lekérése
Telemetriát küld a webkiszolgálóról. Az alkalmazás teljes körű megfigyelése érdekében további megfigyelést adhat hozzá:

* [Adjon telemetriát a weblapokhoz][usage] a lapmegtekintések és a felhasználói mérőszámok megfigyelése érdekében.
* [Beállíthat webes teszteket][availability] annak biztosításához, hogy az alkalmazás mindig elérhető és válaszkész legyen.

## <a name="capture-log-traces"></a>Naplónyomkövetések rögzítése
Az Application Insights segítségével naplókat szeletelhet a Log4J, a Logback vagy más naplózási keretrendszerekből. HTTP-kérelmekkel és más telemetriával kapcsolhatja össze a naplókat. [Itt megismerkedhet az erre vonatkozó részletekkel][javalogs].

## <a name="send-your-own-telemetry"></a>Saját telemetria küldése
Most, hogy telepítette az SDK-t, az API-val saját telemetriát küldhet.

* [Nyomon követheti az egyéni eseményeket és mérőszámokat][api], hogy megtudja, hogyan használják a felhasználók az alkalmazását.
* [Eseményeket és naplókat kereshet][diagnostic], amelyek segítenek a problémák diagnosztizálásában.

## <a name="availability-web-tests"></a>Rendelkezésre állási webes tesztek
Az Application Insights rendszeres időközönként teszteli a webhelyét, hogy működik és jól válaszol-e. [A ][availability]beállításához kattintson a Webes tesztek elemre.

![Kattintson a Webes tesztek elemre, majd a Webes teszt hozzáadása elemre.](./media/app-insights-java-get-started/31-config-web-test.png)

Megkapja a válaszidők diagramjait, valamint e-mailes értesítéseket kap, ha a webhely leáll.

![Példa webes tesztre](./media/app-insights-java-get-started/appinsights-10webtestresult.png)

[További információk a rendelkezésre állási webes tesztekről.][availability]

## <a name="questions-problems"></a>Kérdései vannak? Problémákat tapasztal?
[A Java hibaelhárítása](app-insights-java-troubleshoot.md)

## <a name="video"></a>Videó

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>További lépések
* [Függőségi hívások figyelése](app-insights-java-agent.md)
* [Unix-teljesítményszámlálók figyelése](app-insights-java-collectd.md)
* [A weboldalak figyelésével](app-insights-javascript.md) megfigyelheti az oldalbetöltési időket, az AJAX-hívásokat és a böngészőkivételeket.
* [Egyéni telemetriát](app-insights-api-custom-events-metrics.md) írhat a böngészőben vagy a kiszolgálón való használat nyomon követése érdekében.
* [Irányítópultokat](app-insights-dashboards.md) hozhat létre a rendszer megfigyelésével kapcsolatos fő diagramok összegyűjtéséhez.
* Az [Elemzések](app-insights-analytics.md) használatával hatékony telemetriai lekérdezéseket hajthat végre az alkalmazásból
* További információ: [Azure Java-fejlesztőknek](/java/azure).

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#trackexception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: /app-insights-java-quick-start.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[usage]: app-insights-javascript.md
