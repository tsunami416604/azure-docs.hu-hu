---
title: Java APM és monitorozási eszközökkel, linuxon – az Azure App Service konfigurálása
description: Ismerje meg, hogyan kell küldeni a naplókat és mérőszámokat a NewRelic és alkalmazás Dynamics APM-szolgáltatónak App Service Linux rendszeren futó Java-alkalmazások számára
services: app-service\web
author: rloutlaw
manager: angerobe
ms.service: app-service-web
ms.workload: web
ms.topic: article
ms.date: 11/27/2018
ms.author: astay;routlaw
ms.custom: seodec18
ms.openlocfilehash: b730c30d51a9f86adc889a9d6718d003674dbc27
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2018
ms.locfileid: "53260463"
---
# <a name="how-to-application-performance-monitoring-tools-with-java-apps-on-azure-app-service-on-linux"></a>Útmutató: Az alkalmazásteljesítmény-figyelés Java-alkalmazások a Linuxon futó Azure App Service-eszközöket

Ez a cikk bemutatja, hogyan csatlakozhat az Azure App Service Linux rendszeren a NewRelic és az AppDynamics alkalmazásteljesítmény-figyelési (APM) platformok az üzembe helyezett Java-alkalmazások.

## <a name="configure-new-relic"></a>Konfigurálja a New relic-bővítménnyel
1. Hozzon létre egy NewRelic fiókot [NewRelic.com](https://newrelic.com/signup)
2. Töltse le a Java ügynököt a NewRelic, fog rendelkezni a fájl nevét hasonló `newrelic-java-x.x.x.zip`.
3. Másolja a licenckulcsot, meg kell, hogy később konfigurálja az ügynököt.
4. [SSH-t az App Service-példányhoz](/azure/app-service/containers/app-service-linux-ssh-support) , és hozzon létre egy új könyvtárat `/home/site/wwwroot/apm`. 
5. A kicsomagolt NewRelic Java ügynököt fájlok feltöltése egy könyvtárat `/home/site/wwwroot/apm`. Az ügynök számára a fájlok kell lennie a `/home/site/wwwroot/apm/newrelic`.
6. A következő YAML-fájl módosítása `/home/site/wwwroot/apm/newrelic/newrelic.yml` és licenc helyőrző értékét cserélje le a saját licenckulcsot.
7. Az Azure Portalon keresse meg az alkalmazást az App Service-ben, és hozzon létre egy új alkalmazás-beállítás.
    - Ha az alkalmazás **Java használata**, nevű környezeti változó létrehozásához `JAVA_OPTS` értékkel `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Ha használ **Tomcat**, nevű környezeti változó létrehozásához `CATALINA_OPTS` értékkel `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Ha már rendelkezik egy környezeti változóhoz a `JAVA_OPTS` vagy `CATALINA_OPTS`, fűzze hozzá a `javaagent` beállítást, a jelenlegi érték végén.

## <a name="configure-appdynamics"></a>AppDynamics konfigurálása
1. Hozzon létre egy AppDynamics fiókot a [AppDynamics.com](https://www.appdynamics.com/community/register/)
1. Töltse le a Java ügynököt az AppDynamics webhelyről, a fájlnév hasonló lesz. `AppServerAgent-x.x.x.xxxxx.zip`
1. [SSH-t az App Service-példányhoz](/azure/app-service/containers/app-service-linux-ssh-support) , és hozzon létre egy új könyvtárat `/home/site/wwwroot/apm`. 
1. A Java ügynököt fájlok feltöltése egy könyvtárat `/home/site/wwwroot/apm`. Az ügynök számára a fájlok kell lennie a `/home/site/wwwroot/apm/appdynamics`.
1. IIn az Azure Portalon keresse meg az alkalmazást az App Service-ben, és hozzon létre egy új nastavení Aplikace.
    - Használata **Java használata**, nevű környezeti változó létrehozásához `JAVA_OPTS` értékkel `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<YOUR_SITE_NAME>` ahol `<YOUR_SITE_NAME>` az App Service neve.
    - Ha használ **Tomcat**, nevű környezeti változó létrehozásához `CATALINA_OPTS` értékkel `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<YOUR_SITE_NAME>` ahol `<YOUR_SITE_NAME>` az App Service neve.
