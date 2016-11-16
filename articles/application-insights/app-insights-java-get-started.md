---
title: "Java webalkalmazás elemzése az Application Insights használatával | Microsoft Docs"
description: "A Java-webhely teljesítményének és használatának megfigyelése az Application Insights segítségével. "
services: application-insights
documentationcenter: java
author: alancameronwills
manager: douge
ms.assetid: 051d4285-f38a-45d8-ad8a-45c3be828d91
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/17/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: cc0167ef78eb3ca84e959599473af5935e5da0d0


---
# <a name="get-started-with-application-insights-in-a-java-web-project"></a>Ismerkedés az Application Insights szolgáltatással Java webes projektben
*Az Application Insights jelenleg még előzetes verziójú kiadásban érhető el.*

Az [Application Insights](https://azure.microsoft.com/services/application-insights/) egy bővíthető elemzési szolgáltatás a webfejlesztők számára, amely segít megérteni az élő alkalmazása teljesítményét és használatát. Ezzel [észlelheti és diagnosztizálhatja a teljesítménnyel kapcsolatos hibákat és kivételeket](app-insights-detect-triage-diagnose.md), és [kód][api-t] írhat annak követéséhez, hogy a felhasználók hogyan használják az alkalmazást.

![mintaadatok](./media/app-insights-java-get-started/5-results.png)

Az Application Insights a Linux, Unix vagy Windows rendszeren futó Java alkalmazásokat támogatja.

A következők szükségesek:

* Oracle JRE 1.6 vagy újabb, vagy Zulu JRE 1.6 vagy újabb
* Egy [Microsoft Azure](https://azure.microsoft.com/)-előfizetés. (Kezdhet az [ingyenes próba](https://azure.microsoft.com/pricing/free-trial/).)

*Ha már élő webalkalmazása van, az alternatív eljárást követve [hozzáadhatja az SDK-t a futásidőben a webkiszolgálón](app-insights-java-live.md). Ezzel az alternatívával nem kell újraépítenie a kódot, de nem tud kódot írni a felhasználói tevékenységek követése érdekében.*

## <a name="1-get-an-application-insights-instrumentation-key"></a>1. Application Insights-kialakítási kulcs beszerzése
1. Jelentkezzen be a [Microsoft Azure Portalra](https://portal.azure.com).
2. Hozzon létre egy Application Insights-erőforrást. Állítsa be a Java webalkalmazás alkalmazástípust.
   
    ![Adjon meg egy nevet, válassza ki a Java webalkalmazást, és kattintson a Létrehozás gombra.](./media/app-insights-java-get-started/02-create.png)
3. Keresse meg az új erőforrás kialakítási kulcsát. Ezt a kulcsot nemsokára a kódprojektbe kell illesztenie.
   
    ![Az új erőforrás áttekintésében kattintson a Tulajdonságok gombra, és másolja le a kialakítási kulcsot](./media/app-insights-java-get-started/03-key.png)

## <a name="2-add-the-application-insights-sdk-for-java-to-your-project"></a>2. A Javához készült Application Insights SDK hozzáadása a projekthez
*Válassza ki a projektnek megfelelő módszert.*

#### <a name="if-youre-using-eclipse-to-create-a-maven-or-dynamic-web-project-"></a>Ha az Eclipse-t használja Maven vagy dinamikus webes projekt létrehozásához ...
Használja a [Javához készült Application Insights SDK beépülő modult][eclipse].

#### <a name="if-youre-using-maven"></a>Ha Mavent használ...
Ha a projekt már úgy van beállítva, hogy Mavent használ buildként, egyesítse a következő kódot a pom.xml fájllal.

Ezután frissítse a projektfüggőségeket, hogy letöltse a bináris fájlokat.

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
        <version>[1.0,)</version>
      </dependency>
    </dependencies>


* *Build- vagy ellenőrzőösszeg-érvényesítési hibák?* Próbáljon egy adott verziót használni, például a következőt: `<version>1.0.n</version>`. A legújabb verziót az [SDK kiadási megjegyzéseiben](https://github.com/Microsoft/ApplicationInsights-Java#release-notes) vagy a [Maven-összetevőkben](http://search.maven.org/#search%7Cga%7C1%7Capplicationinsights) találja.
* *Új SDK-ra kell frissítenie?* Frissítse a projekt függőségeit.

#### <a name="if-youre-using-gradle"></a>Ha Gradle-t használ...
Ha a projekt már úgy van beállítva, hogy Gradle-t használ buildként, egyesítse a következő kódot a build.gradle fájllal.

Ezután frissítse a projektfüggőségeket, hogy letöltse a bináris fájlokat.

    repositories {
      mavenCentral()
    }

    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web', version: '1.+'
      // or applicationinsights-core for bare API
    }

* *Build- vagy ellenőrzőösszeg-érvényesítési hibák? Próbáljon adott verziót használni, például a következőt:* `version:'1.0.n'`. *A legújabb verziót az [SDK kiadási megjegyzéseiben](https://github.com/Microsoft/ApplicationInsights-Java#release-notes) találja.*
* *Frissítés új SDK-ra*
  * Frissítse a projekt függőségeit.

#### <a name="otherwise-"></a>Egyéb esetben...
Kézzel adja hozzá az SDK-t:

1. Töltse le a [Javához készült Application Insights SDK-t](https://aka.ms/aijavasdk).
2. Bontsa ki a bináris fájlokat a zip-fájlból, és adja azokat a projekthez.

### <a name="questions"></a>Kérdések...
* *Mi a kapcsolat a zip-fájl `-core` és `-web` összetevője között?*
  
  * `applicationinsights-core`– csak az API-t biztosítja. Erre az összetevőre mindig szüksége van.
  * `applicationinsights-web`– olyan mérőszámokat biztosít, amelyek nyomon követik a HTTP-kérések számát és a válaszidőket. Ezt az összetevőt kihagyhatja, ha nem szeretné automatikusan gyűjteni ezt a telemetriát. hanem például sajátot szeretne írni.
* *Az SDK frissítése a változások közzétételekor*
  
  * Töltse le a legújabb [Javához készült Application Insights SDK-t](https://aka.ms/qqkaq6), és cserélje le a régieket.
  * A változások leírását az [SDK kiadási megjegyzéseiben](https://github.com/Microsoft/ApplicationInsights-Java#release-notes) találja.

## <a name="3-add-an-application-insights-xml-file"></a>3. Application Insights .xml fájl hozzáadása
Adja az ApplicationInsights.xml fájlt a projekt erőforrások mappájához, vagy győződjön meg arról, hogy a projekt üzembe helyezési osztályának elérési útjához van adva. Másolja bele a következő XML-t.

Helyettesítse be az Azure Portalról kapott kialakítási kulcsot.

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
        <Add   type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>

      </TelemetryInitializers>
    </ApplicationInsights>


* A kialakítási kulcsot a telemetria minden elemével megkapja, és ez közli az Application Insights eszközzel, hogy megjelenítse azt az erőforrásban.
* A HTTP-kérelemösszetevő nem kötelező. Automatikusan telemetriát küld a kérelmekkel és válaszidőkkel kapcsolatban a portálra.
* Az eseménykorreláció a HTTP-kérelemösszetevő további eleme. Azonosítót rendel a kiszolgáló által fogadott összes kérelemhez, és az azonosítót „Operation.Id” tulajdonságként hozzáadja a telemetria minden eleméhez. Lehetővé teszi az egyes kérelmekkel társított telemetria korrelációját, ha beállít egy szűrőt a [diagnosztikai keresésben][diagnosztika].
* Az Application Insights-kulcs dinamikusan továbbadható az Azure Portalról rendszertulajdonságként (-DAPPLICATION_INSIGHTS_IKEY=saját_kialakítási_kulcs). Ha nincs tulajdonság meghatározva, környezeti változót (APPLICATION_INSIGHTS_IKEY) keres az Azure App-beállításokban. Ha egyik tulajdonság sincs meghatározva, az alapértelmezett InstrumentationKey lesz használva az ApplicationInsights.xml-ből. Ez a sorozat segít a különböző környezetekhez tartozó InstrumentationKey-ek dinamikus kezelésében.

### <a name="alternative-ways-to-set-the-instrumentation-key"></a>A kialakítási kulcs beállításának egyéb módjai
Az Application Insights SDK ebben a sorrendben keresi a kulcsot:

1. Rendszertulajdonság: -DAPPLICATION_INSIGHTS_IKEY=saját_kialakítási_kulcs
2. Környezeti változó: APPLICATION_INSIGHTS_IKEY
3. Konfigurációs fájl: ApplicationInsights.xml

[Beállíthatja a programkódban](app-insights-api-custom-events-metrics.md#ikey) is:

    telemetryClient.InstrumentationKey = "...";


## <a name="4-add-an-http-filter"></a>4. HTTP-szűrő hozzáadása
Az utolsó konfigurációs lépéssel a HTTP-kérelemösszetevő mindegyik webes kérelmet naplózhatja. (Nem szükséges, ha csak az API-ra van szüksége.)

Keresse meg és nyissa meg a web.xml fájlt a projektben, és egyesítse a következő kódot azon webalkalmazás-csomópont alatt, ahol az alkalmazás szűrői konfigurálva vannak.

A legpontosabb eredmények érdekében le kell képezni a szűrőt az összes többi szűrő előtt.

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

#### <a name="if-youre-using-spring-web-mvc-31-or-later"></a>Ha a Spring Web MVC 3.1-es vagy újabb verzióját használja
Szerkessze úgy ezeket az elemeket, hogy tartalmazzák az Application Insights-csomagot:

    <context:component-scan base-package=" com.springapp.mvc, com.microsoft.applicationinsights.web.spring"/>

    <mvc:interceptors>
        <mvc:interceptor>
            <mvc:mapping path="/**"/>
            <bean class="com.microsoft.applicationinsights.web.spring.RequestNameHandlerInterceptorAdapter" />
        </mvc:interceptor>
    </mvc:interceptors>

#### <a name="if-youre-using-struts-2"></a>Ha Struts 2-t használ
Adja ezt az elemet a Struts konfigurációs fájlhoz (általában struts.xml vagy struts-default.xml a neve):

     <interceptors>
       <interceptor name="ApplicationInsightsRequestNameInterceptor" class="com.microsoft.applicationinsights.web.struts.RequestNameInterceptor" />
     </interceptors>
     <default-interceptor-ref name="ApplicationInsightsRequestNameInterceptor" />

(Ha egy alapértelmezett veremben elfogók vannak meghatározva, az elfogó egyszerűen a veremhez adható.)

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
Ahogy egyre több adatot gyűjt össze, lekérdezéseket futtathat az adatok összegzéséhez és egyéni példányok megkereséséhez is. Az [elemzés]() erőteljes eszköz a teljesítmény és a használat megértéséhez és diagnosztikai célokra is.

![Példa elemzésre](./media/app-insights-java-get-started/025.png)

## <a name="7-install-your-app-on-the-server"></a>7. Az alkalmazás telepítése a kiszolgálón
Most tegye közzé az alkalmazást a kiszolgálón, hagyja, hogy mások használják, és nézze, ahogyan a telemetria megjelenik a portálon.

* Győződjön meg arról, hogy a tűzfal lehetővé teszi, hogy az alkalmazás telemetriát küldjön ezekre a portokra:
  
  * dc.services.visualstudio.com:443
  * f5.services.visualstudio.com:443
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

    <PerformanceCounters>
       <UseBuiltIn>False</UseBuiltIn>
    </PerformanceCounters>

### <a name="collect-additional-performance-counters"></a>További teljesítményszámlálók gyűjtése
További gyűjtendő teljesítményszámlálókat határozhat meg.

#### <a name="jmx-counters-exposed-by-the-java-virtual-machine"></a>JMX-számlálók (a Java virtuális gép által feltárt)
    <PerformanceCounters>
      <Jmx>
        <Add objectName="java.lang:type=ClassLoading" attribute="TotalLoadedClassCount" displayName="Loaded Class Count"/>
        <Add objectName="java.lang:type=Memory" attribute="HeapMemoryUsage.used" displayName="Heap Memory Usage-used" type="composite"/>
      </Jmx>
    </PerformanceCounters>

* `displayName`– Az Application Insights portálon megjelenő név.
* `objectName`– A JMX objektum neve.
* `attribute`– A JMX objektum nevének lehívni kívánt attribútuma
* `type`(választható) – A JMX objektum attribútumának típusa:
  * Alapértelmezett: egyszerű típus, például int vagy long.
  * `composite`: a teljesítményszámláló-adatok az „Attribútum.Adat” formátumban szerepelnek
  * `tabular`: a teljesítményszámláló-adatok táblázatsor formájában szerepelnek

#### <a name="windows-performance-counters"></a>Windows-teljesítményszámlálók
Mindegyik [Windows-teljesítményszámláló](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) egy kategória tagja (ugyanúgy, ahogyan a mezők osztályok tagjai). A kategóriák globálisak lehetnek, vagy számozott vagy elnevezett példányokkal rendelkezhetnek.

    <PerformanceCounters>
      <Windows>
        <Add displayName="Process User Time" categoryName="Process" counterName="%User Time" instanceName="__SELF__" />
        <Add displayName="Bytes Printed per Second" categoryName="Print Queue" counterName="Bytes Printed/sec" instanceName="Fax" />
      </Windows>
    </PerformanceCounters>

* displayName – Az Application Insights portálon megjelenő név.
* categoryName – A teljesítményszámláló kategóriája (teljesítményobjektum), amelyhez ez a teljesítményszámláló társítva van.
* counterName – A teljesítményszámláló neve.
* instanceName – A teljesítményszámláló-kategória példányneve, vagy üres karakterlánc („”), ha a kategória egyetlen példányt tartalmaz. Ha a categoryName Folyamat, és a gyűjteni kívánt teljesítményszámláló az aktuális JVM folyamatról származik, amelyen az alkalmazása fut, adja meg a következőt: `"__SELF__"`.

A teljesítményszámlálói egyéni mérőszámokként láthatók a [Metrikaböngészőben][metrics].

![](./media/app-insights-java-get-started/12-custom-perfs.png)

### <a name="unix-performance-counters"></a>Unix-teljesítményszámlálók
* [Telepítse a gyűjteményt az Application Insights beépülő modullal](app-insights-java-collectd.md) számos rendszer- és hálózati adat lekéréséhez.

## <a name="get-user-and-session-data"></a>Felhasználói és munkamenetadatok lekérése
Telemetriát küld a webkiszolgálóról. Az alkalmazás teljes körű megfigyelése érdekében további megfigyelést adhat hozzá:

* [Adjon telemetriát a weblapokhoz][használat] a lapmegtekintések és a felhasználói mérőszámok megfigyelése érdekében.
* [Beállíthat webes teszteket][rendelkezésre állás]annak biztosításához, hogy az alkalmazás mindig elérhető és válaszkész legyen.

## <a name="capture-log-traces"></a>Naplónyomkövetések rögzítése
Az Application Insights segítségével naplókat szeletelhet a Log4J, a Logback vagy más naplózási keretrendszerekből. HTTP-kérelmekkel és más telemetriával kapcsolhatja össze a naplókat. [További tudnivalók][javalogs].

## <a name="send-your-own-telemetry"></a>Saját telemetria küldése
Most, hogy telepítette az SDK-t, az API-val saját telemetriát küldhet.

* [Nyomon követheti az egyéni eseményeket és mérőszámokat][api-t], hogy megtudja, hogyan használják a felhasználók az alkalmazását.
* [Eseményeket és naplókat kereshet][diagnosztika], amelyek segítenek a problémák diagnosztizálásában.

## <a name="availability-web-tests"></a>Rendelkezésre állási webes tesztek
Az Application Insights rendszeres időközönként teszteli a webhelyét, hogy működik és jól válaszol-e. [A ][rendelkezésre állás]beállításához kattintson a Webes tesztek elemre.

![Kattintson a Webes tesztek elemre, majd a Webes teszt hozzáadása elemre.](./media/app-insights-java-get-started/31-config-web-test.png)

Megkapja a válaszidők diagramjait, valamint e-mailes értesítéseket kap, ha a webhely leáll.

![Példa webes tesztre](./media/app-insights-java-get-started/appinsights-10webtestresult.png)

[További információk a rendelkezésre állási webes tesztekről.][rendelkezésre állás] 

## <a name="questions-problems"></a>Kérdései vannak? Problémákat tapasztal?
[A Java hibaelhárítása](app-insights-java-troubleshoot.md)

## <a name="next-steps"></a>Következő lépések
* [Függőségi hívások figyelése](app-insights-java-agent.md)
* [Unix-teljesítményszámlálók figyelése](app-insights-java-collectd.md)
* [A weboldalak figyelésével](app-insights-javascript.md) megfigyelheti az oldalbetöltési időket, az AJAX-hívásokat és a böngészőkivételeket.
* [Egyéni telemetriát](app-insights-api-custom-events-metrics.md) írhat a böngészőben vagy a kiszolgálón való használat nyomon követése érdekében.
* [Irányítópultokat](app-insights-dashboards.md) hozhat létre a rendszer megfigyelésével kapcsolatos fő diagramok összegyűjtéséhez.
* Az [elemzések](app-insights-analytics.md) használatával hatékony telemetriai lekérdezéseket hajthat végre az alkalmazásból
* További információ: [Java fejlesztői központ](/develop/java/).

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[rendelkezésre állás]: app-insights-monitor-web-app-availability.md
[diagnosztika]: app-insights-diagnostic-search.md
[Eclipse]: app-insights-java-eclipse.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[használat]: app-insights-web-track-usage.md



<!--HONumber=Nov16_HO2-->


