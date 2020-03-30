---
title: A Mikrométer használata az Azure Application Insights Java SDK-val
description: Lépésről lépésre útmutató a Micrometer használatáról az Application Insights tavaszi rendszerindítással és a nem rugós rendszerindítási alkalmazásokkal.
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 11/01/2018
ms.openlocfilehash: dd04087db32f0bbfa75dafa7e12c355e5ab7b515
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670066"
---
# <a name="how-to-use-micrometer-with-azure-application-insights-java-sdk"></a>A Mikrométer használata az Azure Application Insights Java SDK-val
A mikrométer alkalmazásfigyelés méri a JVM-alapú alkalmazáskód metrikáit, és lehetővé teszi az adatok exportálását a kedvenc figyelési rendszerekbe. Ez a cikk bemutatja, hogyan kell használni a Micrometer-t az Application Insights segítségével mind a tavaszi rendszerindításhoz, mind a nem tavaszi rendszerindítási alkalmazásokhoz.

## <a name="using-spring-boot-15x"></a>Tavaszi csomagtartó használata 1,5x
Adja hozzá a következő függőségeket a pom.xml vagy build.gradle fájlhoz: 
* [Application Insights tavaszi boot-starter](https://github.com/Microsoft/ApplicationInsights-Java/tree/master/azure-application-insights-spring-boot-starter) 2.5.0 vagy újabb
* Micrometer Azure Rendszerleíró adatbázis 1.1.0 vagy újabb
* [Micrometer Spring Legacy](https://micrometer.io/docs/ref/spring/1.5) 1.1.0 vagy újabb (ez backports az autoconfig kódot a tavaszi keret).
* [ApplicationInsights erőforrás](../../azure-monitor/app/create-new-resource.md )

Lépések

1. Frissítse a Spring Boot alkalmazás pom.xml fájlját, és adja hozzá a következő függőségeket:

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
2. Frissítse az application.properties vagy yml fájlt az Application Insights Instrumentation kulccsal a következő tulajdonsággal:

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
1. Az alkalmazás létrehozása és futtatása
2. A fentiek segítségével az Azure Monitorba automatikusan gyűjtött, előre összesített metrikák nak kell működésbe lépniük. Az Application Insights tavaszi rendszerindítási indításának finomhangolására vonatkozó részletekért tekintse meg a [GitHub on található fontos alkalmazást.](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/azure-application-insights-spring-boot-starter/README.md)

## <a name="using-spring-2x"></a>A Spring 2.x használata

Adja hozzá a következő függőségeket a pom.xml vagy build.gradle fájlhoz:

* Application Insights tavaszi boot-starter 2.1.2 vagy újabb
* Azure-spring-boot-metrikák-kezdők 2.0.7 vagy újabb
* [Application Insights-erőforrás](../../azure-monitor/app/create-new-resource.md )

Lépések:

1. Frissítse a Spring Boot alkalmazás pom.xml fájlját, és adja hozzá a következő függőséget:

    ```XML
    <dependency> 
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-spring-boot-metrics-starter</artifactId>
          <version>2.0.7</version>
    </dependency>
    ```
1. Frissítse az application.properties vagy yml fájlt az Application Insights Instrumentation kulccsal a következő tulajdonsággal:

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
3. Az alkalmazás létrehozása és futtatása
4. A fentiek az Azure Monitorba automatikusan gyűjtött előre összesített metrikák futtatását kell lehetővé tenni. Az Application Insights tavaszi rendszerindítási indításának finomhangolására vonatkozó részletekért tekintse meg a [GitHub on található fontos alkalmazást.](https://github.com/Microsoft/azure-spring-boot/releases/latest)

Alapértelmezett mutatók:

*    Automatikusan konfigurált metrikák Tomcat, JVM, Logback metrikák, Log4J metrikák, uptime metrikák, processzor metrikák, FileDescriptorMetrics.
*    Ha például a Netflix Hystrix jelen van az osztályútvonalon, akkor ezeket a mutatókat is megkapjuk. 
*    A következő metrikák érhetők el a megfelelő bab hozzáadásával. 
        - CacheMetrics (CaffeineCache, EhCache2, GuavaCache, HazelcastCache, JCache)     
        - DataBaseTableMetrics 
        - HibernálásI mutatók 
        - JettyMetrics 
        - OkHttp3 mérőszámok 
        - Kafka mérőszámok 

 

Az automatikus metrikagyűjtemény kikapcsolása: 
 
- JVM mérőszámok: 
    - management.metrics.binders.jvm.enabled=false 
- Logback metrikák: 
    - management.metrics.binders.logback.enabled=false
- Uptime mérőszámok: 
    - management.metrics.binders.uptime.enabled=false 
- Processzor metrikák:
    -  management.metrics.binders.processor.enabled=false 
- FileDescriptorMetrics:
    - management.metrics.iratgyűjtők.fájlok.enabled=hamis 
- Hystrix Metrics, ha a könyvtár osztálypapályán van: 
    - management.metrics.binders.hystrix.enabled=false 
- AspectJ metrikák, ha a könyvtár osztályelérési úton van: 
    - spring.aop.enabled=hamis 

> [!NOTE]
> Adja meg a fenti tulajdonságokat a Spring Boot alkalmazás application.properties vagy application.yml fájljában.

## <a name="use-micrometer-with-non-spring-boot-web-applications"></a>Micrometer használata nem rugós boot webes alkalmazásokkal

Adja hozzá a következő függőségeket a pom.xml vagy build.gradle fájlhoz:

* Application Insights Web Auto 2.5.0 vagy újabb
* Micrometer Azure Rendszerleíró adatbázis 1.1.0 vagy újabb
* [Application Insights-erőforrás](../../azure-monitor/app/create-new-resource.md )

Lépések:

1. Adja hozzá a következő függőségeket a pom.xml vagy build.gradle fájlhoz:

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

2. Fájl `ApplicationInsights.xml` elhelyezése az erőforrások mappájába:

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

3. Minta Servlet osztály (időzítőmérőt bocsát ki):

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

4. Mintakonfigurációs osztály:

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

Ha többet szeretne megtudni a mérőszámokról, olvassa el a [Mikrométer dokumentációját.](https://micrometer.io/docs/)

A[micrometer GitHub tárházban](https://github.com/micrometer-metrics/micrometer/tree/master/samples/micrometer-samples-core/src/main/java/io/micrometer/core/samples)található más mintakód a különböző típusú metrikák létrehozásához.

## <a name="how-to-bind-additional-metrics-collection"></a>További metrikagyűjtemény kötése

### <a name="springbootspring"></a>SpringBoot/Tavasz

Hozzon létre egy bab a megfelelő metrika kategóriában. Például, mondjuk szükségünk Guava cache Metrics:

```Java
    @Bean
    GuavaCacheMetrics guavaCacheMetrics() {
        Return new GuavaCacheMetrics();
    }
```
Számos olyan metrika van, amelyek alapértelmezés szerint nincsenek engedélyezve, de a fenti módon is megköthetők. A teljes listát a [Micrometer GitHub hivatalos tártárjában tájékozással tájékozott.](https://github.com/micrometer-metrics/micrometer/tree/master/micrometer-core/src/main/java/io/micrometer/core/instrument/binder )

### <a name="non-spring-apps"></a>Nem tavaszi alkalmazások
Adja hozzá a következő kötéskódot a konfigurációs fájlhoz:
```Java 
    New GuavaCacheMetrics().bind(registry);
```

## <a name="next-steps"></a>További lépések

* Ha többet szeretne megtudni a Mikrométerről, olvassa el a micrometer hivatalos [dokumentációját](https://micrometer.io/docs).
* Ha többet szeretne megtudni az Azure-on lévő tavaszról, tekintse meg a hivatalos [tavaszi azure-dokumentációt.](https://docs.microsoft.com/java/azure/spring-framework/?view=azure-java-stable)
