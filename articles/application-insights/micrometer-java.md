---
title: Használata esetén az Azure Application Insights Java SDK |} A Microsoft Docs
description: 'Lépésenkénti útmutató, esetén használja az Application Insights Spring Boot és a Spring Boot alkalmazásokkal. '
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
ms.openlocfilehash: 3bf24a4ed17986ff948a7396e608a51ae38208a5
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54076501"
---
# <a name="how-to-use-micrometer-with-azure-application-insights-java-sdk"></a>Használata esetén az Azure Application Insights Java SDK
Esetén figyelési mértékek alkalmazásmetrikák JVM-alapú alkalmazás code, és exportálja az adatokat a kívánt rendszerek figyelése. Ez a cikk használata esetén az Application insights segítségével a Spring Boot és a Spring Boot-alkalmazások hogyan meg.

## <a name="using-spring-boot-15x"></a>1,5 x Spring használatával rendszerindító
Adja hozzá a következő függőségeket a build.gradle vagy a pom.xml fájlba: 
* [Application Insights spring-boot-starter](https://github.com/Microsoft/ApplicationInsights-Java/tree/master/azure-application-insights-spring-boot-starter)1.1.0-BETA vagy újabb
* Esetén az Azure Registry 1.1.0-ás vagy újabb
* [Örökölt esetén Spring](https://micrometer.io/docs/ref/spring/1.5) 1.1.0-ás vagy újabb (Ez a hálózat automatikus beállítása kódot a Spring keretrendszer portolások).
* [ApplicationInsights erőforrás](../azure-monitor/app/create-new-resource.md )

Lépések

1. Frissítse a pom.xml fájlt a Spring Boot-alkalmazás, és adja hozzá a következő függőségeket benne:

    ```XML
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-spring-boot-starter</artifactId>
        <version>1.1.0-BETA</version>
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
2. Frissítse a application.properties vagy yml fájlt az Application Insights-kialakítási kulcsot a következő tulajdonságot:

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
1. Az alkalmazás létrehozása és futtatása
2. A fenti kell, és az Azure Monitor összegyűjtött előre összesített mérőszámok automatikus futtatása. Application Insights Spring Boot starter finomhangolásához részleteiért tekintse meg a [a GitHub információs](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/azure-application-insights-spring-boot-starter/README.md).

## <a name="using-spring-2x"></a>A Spring 2.x használatával

Adja hozzá a következő függőségeket a build.gradle vagy a pom.xml fájlba:

* Application Insights Spring boot-starter 2.1.2 vagy újabb
* Az Azure-spring-rendszerindítás-mérőszámok – például 2.0.7 vagy újabb  
* [Application Insights-erőforrás](../azure-monitor/app/create-new-resource.md )

Lépések:

1. Frissítse a pom.xml fájlt a Spring Boot-alkalmazás, és adja hozzá a következő függőséget benne:

    ```XML
    <dependency> 
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-spring-boot-metrics-starter</artifactId>
          <version>2.0.7</version>
    </dependency>
    ```
1. Frissítse a application.properties vagy yml fájlt az Application Insights-kialakítási kulcsot a következő tulajdonságot:

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
3. Az alkalmazás létrehozása és futtatása
4. A fenti szerezheti be, az Azure Monitor összegyűjtött előre összesített mérőszámok automatikus futtatása. Application Insights Spring Boot starter finomhangolásához részleteiért tekintse meg a [a GitHub információs](https://github.com/Microsoft/azure-spring-boot/releases/latest).

Alapértelmezett metrikák:

*    Tomcat, a JVM, a Logback metrikák, a Log4J mérőszámokat, hasznos üzemidőt metrikák, processzor-metrikák, FileDescriptorMetrics automatikusan konfigurálva metrikákat.
*    Például ha a netflix hystrix osztály elérési úton található kapunk is ezeket a metrikákat. 
*    Lehet, hogy a következő metrikák megfelelő bab hozzáadásával érhető el. 
        - CacheMetrics (CaffineCache, EhCache2, GuavaCache, HazelcaseCache, Jcache)     
        - DataBaseTableMetrics 
        - HibernateMetrics 
        - JettyMetrics 
        - OkHttp3 metrikák 
        - A Kafka-metrikák 

 

Útmutató az automatikus metrikák gyűjtésének kikapcsolása: 
 
- JVM-metrikák: 
    - Management.Metrics.binders.JVM.Enabled=FALSE 
- Logback metrikák: 
    - Management.Metrics.binders.logback.Enabled=FALSE
- Hasznos üzemidő metrikák: 
    - Management.Metrics.binders.uptime.Enabled=FALSE 
- Processzor-metrikák:
    -  Management.Metrics.binders.Processor.Enabled=FALSE 
- FileDescriptorMetrics:
    - Management.Metrics.binders.Files.Enabled=FALSE 
- Hystrix metrikák Ha osztályútvonal könyvtárában: 
    - Management.Metrics.binders.hystrix.Enabled=FALSE 
- AspectJ metrikák Ha osztályútvonal könyvtárában: 
    - Spring.aop.Enabled=FALSE 

> [!NOTE]
> A Spring Boot-alkalmazás application.properties vagy application.yml fájlban a fenti tulajdonságainak megadása

## <a name="use-micrometer-with-non-spring-boot-web-applications"></a>Spring Boot-webalkalmazásokkal való használata esetén

Adja hozzá a következő függőségeket a build.gradle vagy a pom.xml fájlba:
 
* [Application Insight Core 2.2.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights/2.2.0) vagy újabb
* [Az Application Insights webalkalmazás-2.2.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/2.2.0) vagy újabb
* [Webszűrő regisztrálása](https://docs.microsoft.com/azure/application-insights/app-insights-java-get-started)
* Esetén az Azure Registry 1.1.0-ás vagy újabb
* [Application Insights-erőforrás](../azure-monitor/app/create-new-resource.md )

Lépések:

1. Adja hozzá a következő függőségeket a pom.xml vagy build.gradle fájlban:

    ```XML
        <dependency>
            <groupId>io.micrometer</groupId>
            <artifactId>micrometer-registry-azure-monitor</artifactId>
            <version>1.1.0</version>
        </dependency>
        
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>applicationinsights-web</artifactId>
            <version>2.2.0</version>
        </dependency
     ```

2. Alkalmazás Insights.xml helyezze az erőforrások mappában

    Minta servletosztályra (bocsát ki egy időzítő metrika):

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

      Minta konfigurációs osztályt:

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

További információk a metrikákról, tekintse meg a [esetén dokumentáció](https://micrometer.io/docs/).

Más mintakód létrehozásával különböző típusú metrikák található[a hivatalos esetén GitHub-adattárat](https://github.com/micrometer-metrics/micrometer/tree/master/samples/micrometer-samples-core/src/main/java/io/micrometer/core/samples).

## <a name="how-to-bind-additional-metrics-collection"></a>Hogyan köthet további metrikák gyűjtési

### <a name="springbootspring"></a>SpringBoot/Spring szolgáltatásokat

Hozzon létre egy, a megfelelő metrikakategória bean. Tegyük fel például, hogy guajáva kell gyorsítótár-metrikák:

```Java
    @Bean
    GuavaCacheMetrics guavaCacheMetrics() {
        Return new GuavaCacheMetrics();
    }
```
Nincsenek alapértelmezés szerint nincs engedélyezve, de a fenti módon köthető több metrikát. Teljes listáját, tekintse meg [a hivatalos esetén GitHub-adattárat](https://github.com/micrometer-metrics/micrometer/tree/master/micrometer-core/src/main/java/io/micrometer/core/instrument/binder ).

### <a name="non-spring-apps"></a>Nem Spring alkalmazások
Adja hozzá a következő kötőkódot a konfigurációs fájl:
```Java 
    New GuavaCacheMetrics().bind(registry);
```

## <a name="next-steps"></a>További lépések

* További információ a esetén tekintse meg a hivatalos további [esetén dokumentáció](https://micrometer.io/docs).
* Spring megismerheti az Azure-ban tekintse meg a hivatalos [Spring on Azure dokumentációja](https://docs.microsoft.com/java/azure/spring-framework/?view=azure-java-stable).
