---
title: A Mikrométer használata az Azure Application Insights Java SDK-val | Microsoft Docs
description: 'Részletes útmutató a Mikrométerek használatáról Application Insights Spring boot-és nem tavaszi rendszerindító alkalmazásaival. '
services: application-insights
documentationcenter: java
author: lgayhardt
manager: carmonm
ms.assetid: 051d4285-f38a-45d8-ad8a-45c3be828d91
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: lagayhar
ms.openlocfilehash: 5bef5a6037c6eb29d0dc48e313958e2d243904eb
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299577"
---
# <a name="how-to-use-micrometer-with-azure-application-insights-java-sdk"></a>A Mikrométer használata az Azure Application Insights Java SDK-val
A Mikrométer alkalmazás-figyelési mérőszámai a JVM-alapú alkalmazás kódjára vonatkoznak, és lehetővé teszi az adatok exportálását kedvenc megfigyelési rendszerbe. Ez a cikk bemutatja, hogyan használható a Mikrométer a Application Insights a Spring boot és a nem rugós rendszerindítási alkalmazásokhoz.

## <a name="using-spring-boot-15x"></a>Spring boot 1.5 x használata
Adja hozzá a következő függőségeket a Pom. XML vagy Build. gradle fájlhoz: 
* [Application Insights Spring-boot-Starter](https://github.com/Microsoft/ApplicationInsights-Java/tree/master/azure-application-insights-spring-boot-starter) 2.5.0 vagy újabb verzió
* Mikrométer Azure Registry 1.1.0 vagy újabb
* A [mikrométer rugó örökölt](https://micrometer.io/docs/ref/spring/1.5) 1.1.0-es vagy újabb (ez a backports az automatikus konfigurációs kód a Spring Framework-ben).
* [ApplicationInsights erőforrás](../../azure-monitor/app/create-new-resource.md )

Lépések

1. Frissítse a Spring boot-alkalmazás Pom. XML fájlját, és adja hozzá az alábbi függőségeket:

    ```XML
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-spring-boot-starter</artifactId>
        <version>2.5.0</version>
    </dependency>

    <dependency>
        <groupId>io.micrometer</groupId>
        <artifactId>micrometer-spring-legacy</artifactId>
        <version>1.1.0</version>
    </dependency>

    <dependency>
        <groupId>io.micrometer</groupId>
        <artifactId>micrometer-registry-azure-monitor</artifactId>
        <version>1.1.0</version>
    </dependency>

    ```
2. Frissítse az Application. properties vagy a YML fájlt a Application Insights rendszerállapot-kulcs használatával a következő tulajdonsággal:

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
1. Az alkalmazás létrehozása és futtatása
2. A fenti lépésekkel a Azure Monitorba automatikusan összegyűjtött, előre összevont metrikákkal kezdheti meg a szolgáltatást. A Application Insights Spring boot Starter finomhangolásával kapcsolatos részletekért tekintse meg a [githubon található Readme](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/azure-application-insights-spring-boot-starter/README.md)című témakört.

## <a name="using-spring-2x"></a>A Spring 2. x használata

Adja hozzá a következő függőségeket a Pom. XML vagy Build. gradle fájlhoz:

* Application Insights Spring-boot-Starter 2.1.2 vagy újabb verzió
* Azure-Spring-boot-metrikák-kezdő 2.0.7 vagy újabb
* [Erőforrás Application Insights](../../azure-monitor/app/create-new-resource.md )

Lépések:

1. Frissítse a Spring boot-alkalmazás Pom. XML fájlját, és adja hozzá a következő függőséget:

    ```XML
    <dependency> 
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-spring-boot-metrics-starter</artifactId>
          <version>2.0.7</version>
    </dependency>
    ```
1. Frissítse az Application. properties vagy a YML fájlt a Application Insights rendszerállapot-kulcs használatával a következő tulajdonsággal:

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
3. Az alkalmazás létrehozása és futtatása
4. A fentiekben a Azure Monitorba automatikusan összegyűjtött, előre összevont metrikákkal kell futnia. A Application Insights Spring boot Starter finomhangolásával kapcsolatos részletekért tekintse meg a [githubon található Readme](https://github.com/Microsoft/azure-spring-boot/releases/latest)című témakört.

Alapértelmezett mérőszámok:

*    A rendszer automatikusan konfigurálta a Tomcat, a JVM, a Logback metrikák, a Log4J metrikák, a üzemidő Metrikái, a processzor Metrikái és a FileDescriptorMetrics metrikáit.
*    Ha például a Netflix Hystrix megtalálható az osztály elérési úton, akkor a mérőszámokat is megkapjuk. 
*    A következő mérőszámok elérhetők a megfelelő bab hozzáadásával. 
        - CacheMetrics (CaffeineCache, EhCache2, GuavaCache, HazelcastCache, JCache)     
        - DataBaseTableMetrics 
        - HibernateMetrics 
        - JettyMetrics 
        - OkHttp3 metrikák 
        - Kafka-metrikák 

 

Az automatikus metrikák gyűjtésének kikapcsolása: 
 
- JVM metrikák: 
    - management.metrics.binders.jvm.enabled=false 
- Logback metrikák: 
    - management.metrics.binders.logback.enabled=false
- Üzemidő mérőszámai: 
    - management.metrics.binders.uptime.enabled=false 
- Processzor Metrikái:
    -  management.metrics.binders.processor.enabled=false 
- FileDescriptorMetrics:
    - management.metrics.binders.files.enabled=false 
- Hystrix metrikák, ha a osztályútvonal lévő könyvtár: 
    - management.metrics.binders.hystrix.enabled=false 
- AspectJ metrikák, ha a osztályútvonal lévő könyvtár: 
    - spring.aop.enabled=false 

> [!NOTE]
> Adja meg a fenti tulajdonságokat a Spring boot-alkalmazás Application. properties vagy Application. YML fájljában.

## <a name="use-micrometer-with-non-spring-boot-web-applications"></a>A Mikrométer használata nem rugós rendszerindító webalkalmazásokkal

Adja hozzá a következő függőségeket a Pom. XML vagy Build. gradle fájlhoz:

* Application Insights web Auto 2.5.0 vagy újabb verzió
* Mikrométer Azure Registry 1.1.0 vagy újabb
* [Erőforrás Application Insights](../../azure-monitor/app/create-new-resource.md )

Lépések:

1. Adja hozzá a következő függőségeket a Pom. XML vagy Build. gradle fájlhoz:

    ```XML
        <dependency>
            <groupId>io.micrometer</groupId>
            <artifactId>micrometer-registry-azure-monitor</artifactId>
            <version>1.1.0</version>
        </dependency>
        
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>applicationinsights-web-auto</artifactId>
            <version>2.5.0</version>
        </dependency>
     ```

2. Fájl `ApplicationInsights.xml` elhelyezése a Resources mappában:

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

3. Példa servlet-osztályra (időzítő metrikát bocsát ki):

    ```Java
        @WebServlet("/hello")
        public class TimedDemo extends HttpServlet {
    
          private static final long serialVersionUID = -4751096228274971485L;
    
          @Override
          @Timed(value = "hello.world")
          protected void doGet(HttpServletRequest request, HttpServletResponse response)
              throws ServletException, IOException {
    
            response.getWriter().println("Hello World!");
            MeterRegistry registry = (MeterRegistry) getServletContext().getAttribute("AzureMonitorMeterRegistry");
    
        //create new Timer metric
            Timer sampleTimer = registry.timer("timer");
            Stream<Integer> infiniteStream = Stream.iterate(0, i -> i+1);
            infiniteStream.limit(10).forEach(integer -> {
              try {
                Thread.sleep(1000);
                sampleTimer.record(integer, TimeUnit.MILLISECONDS);
              } catch (Exception e) {}
               });
          }
          @Override
          public void init() throws ServletException {
            System.out.println("Servlet " + this.getServletName() + " has started");
          }
          @Override
          public void destroy() {
            System.out.println("Servlet " + this.getServletName() + " has stopped");
          }
    
        }
    
    ```

4. Minta konfigurációs osztály:

    ```Java
         @WebListener
         public class MeterRegistryConfiguration implements ServletContextListener {
     
           @Override
           public void contextInitialized(ServletContextEvent servletContextEvent) {
     
         // Create AzureMonitorMeterRegistry
           private final AzureMonitorConfig config = new AzureMonitorConfig() {
             @Override
             public String get(String key) {
                 return null;
             }
            @Override
               public Duration step() {
                 return Duration.ofSeconds(60);}
     
             @Override
             public boolean enabled() {
                 return false;
             }
         };
     
      MeterRegistry azureMeterRegistry = AzureMonitorMeterRegistry.builder(config);
     
             //set the config to be used elsewhere
             servletContextEvent.getServletContext().setAttribute("AzureMonitorMeterRegistry", azureMeterRegistry);
     
           }
     
           @Override
           public void contextDestroyed(ServletContextEvent servletContextEvent) {
     
           }
         }
    ```

A metrikákkal kapcsolatos további tudnivalókért tekintse meg a [mikrométer dokumentációját](https://micrometer.io/docs/).

A különböző típusú metrikák létrehozásával kapcsolatos további mintakód[a hivatalos mikrométer GitHub](https://github.com/micrometer-metrics/micrometer/tree/master/samples/micrometer-samples-core/src/main/java/io/micrometer/core/samples)-tárházában található.

## <a name="how-to-bind-additional-metrics-collection"></a>További mérőszámok gyűjtésének kötése

### <a name="springbootspring"></a>SpringBoot/Spring

Hozzon létre egy megfelelő metrikai kategóriába tartozó Bean-osztályt. Tegyük fel például, hogy a guava gyorsítótár Metrikái vannak:

```Java
    @Bean
    GuavaCacheMetrics guavaCacheMetrics() {
        Return new GuavaCacheMetrics();
    }
```
Több mérőszám is van, amelyek alapértelmezés szerint nem engedélyezettek, de a fenti módon kötve lehet. A teljes listát [a hivatalos mikrométer GitHub](https://github.com/micrometer-metrics/micrometer/tree/master/micrometer-core/src/main/java/io/micrometer/core/instrument/binder )-tárházban találja.

### <a name="non-spring-apps"></a>Nem rugós alkalmazások
Adja hozzá a következő kötési kódot a konfigurációs fájlhoz:
```Java 
    New GuavaCacheMetrics().bind(registry);
```

## <a name="next-steps"></a>További lépések

* A mikrométersel kapcsolatos további tudnivalókért tekintse meg a hivatalos [mikrométer dokumentációját](https://micrometer.io/docs).
* A Spring on Azure-ról az Azure-beli hivatalos [tavaszi dokumentációban](https://docs.microsoft.com/java/azure/spring-framework/?view=azure-java-stable)olvashat bővebben.
