---
title: Bármilyen környezetben futó Java-alkalmazások figyelése – Azure Monitor Application Insights
description: Alkalmazások teljesítményének figyelése bármilyen környezetben futó Java-alkalmazásokhoz a Java önálló ügynökkel az alkalmazás kialakítása nélkül. Elosztott nyomkövetési és alkalmazás-hozzárendelés.
ms.topic: conceptual
ms.date: 04/16/2020
ms.openlocfilehash: 527f1eaf04be7b5e8c89c12912a06d2f5d50321f
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82508037"
---
# <a name="configuring-jvm-args-java-standalone-agent-for-azure-monitor-application-insights"></a>A JVM-argumentumok konfigurálása a Java önálló ügynöke Azure Monitor Application Insights



## <a name="azure-environments"></a>Azure-környezetek

[App Services](https://docs.microsoft.com/azure/app-service/configure-language-java#set-java-runtime-options)konfigurálása.

## <a name="spring-boot"></a>Spring Boot

Adja hozzá a JVM `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` ARG `-jar`-t valahol, például:

```
java -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar -jar <myapp.jar>
```

## <a name="spring-boot-via-docker-entry-point"></a>Spring boot a Docker belépési pontján keresztül

Ha az *exec* űrlapot használja, adja hozzá `"-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"` a paramétert a paraméterhez a paraméter előtt `"-jar"` (például:).

```
ENTRYPOINT ["java", "-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar", "-jar", "<myapp.jar>"]
```

Ha a *rendszerhéj* -űrlapot használja, adja hozzá a JVM ARG `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` `-jar`-t valahol, például:

```
ENTRYPOINT java -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar -jar <myapp.jar>
```

## <a name="tomcat-8-linux"></a>Tomcat 8 (Linux)

### <a name="tomcat-installed-via-apt-get-or-yum"></a>Tomcat telepítve a `apt-get` vagy a használatával`yum`

Ha a Tomcat-t `apt-get` a `yum`vagy a használatával telepítette, akkor `/etc/tomcat8/tomcat8.conf`rendelkeznie kell egy fájllal.  Adja hozzá ezt a sort a fájl végéhez:

```
JAVA_OPTS="$JAVA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

### <a name="tomcat-installed-via-download-and-unzip"></a>A Tomcat a letöltés és a kicsomagolás használatával lett telepítve

Ha a Tomcat-t a [https://tomcat.apache.org](https://tomcat.apache.org)letöltésével és kicsomagolásával telepítette, akkor `<tomcat>/bin/catalina.sh`rendelkeznie kell egy fájllal.  Hozzon létre egy új fájlt a nevű `<tomcat>/bin/setenv.sh` könyvtárban a következő tartalommal:

```
CATALINA_OPTS="$CATALINA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

Ha a fájl `<tomcat>/bin/setenv.sh` már létezik, módosítsa a fájlt, és adja `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` hozzá `CATALINA_OPTS`a következőt:.


## <a name="tomcat-8-windows"></a>Tomcat 8 (Windows)

### <a name="running-tomcat-from-the-command-line"></a>A Tomcat futtatása a parancssorból

Keresse meg a `<tomcat>/bin/catalina.bat`fájlt.  Hozzon létre egy új fájlt a nevű `<tomcat>/bin/setenv.bat` könyvtárban a következő tartalommal:

```
set CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```

Az idézőjelek nem szükségesek, de ha be szeretné vonni őket, a megfelelő elhelyezés a következő:

```
set "CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

Ha a fájl `<tomcat>/bin/setenv.bat` már létezik, csak módosítsa a fájlt, és `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` adja `CATALINA_OPTS`hozzá a következőt:.

### <a name="running-tomcat-as-a-windows-service"></a>A Tomcat futtatása Windows-szolgáltatásként

Keresse meg a `<tomcat>/bin/tomcat8w.exe`fájlt.  Futtassa ezt a végrehajtható fájlt `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` , és `Java Options` adja hozzá `Java` a parancsot a lapon.


## <a name="jboss-eap-7"></a>JBoss EAP 7

### <a name="standalone-server"></a>Önálló kiszolgáló

Hozzáadás `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` a fájl `JAVA_OPTS` `JBOSS_HOME/bin/standalone.conf` meglévő környezeti változóhoz (Linux) vagy `JBOSS_HOME/bin/standalone.conf.bat` (Windows):

```java    ...
    JAVA_OPTS="<b>-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar</b> -Xms1303m -Xmx1303m ..."
    ...
```

### <a name="domain-server"></a>Tartományi kiszolgáló

Hozzáadás `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` a meglévőhöz `jvm-options` a `JBOSS_HOME/domain/configuration/host.xml`következőben:

```xml
...
<jvms>
    <jvm name="default">
        <heap size="64m" max-size="256m"/>
        <jvm-options>
            <option value="-server"/>
            <!--Add Java agent jar file here-->
            <option value="-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"/>
            <option value="-XX:MetaspaceSize=96m"/>
            <option value="-XX:MaxMetaspaceSize=256m"/>
        </jvm-options>
    </jvm>
</jvms>
...
```

Ha több felügyelt kiszolgálót futtat egyetlen gazdagépen, akkor mindegyikhez `applicationinsights.agent.id` `system-properties` `server`hozzá kell adnia a következőhöz:

```xml
...
<servers>
    <server name="server-one" group="main-server-group">
        <!--Edit system properties for server-one-->
        <system-properties> 
            <property name="applicationinsights.agent.id" value="..."/>
        </system-properties>
    </server>
    <server name="server-two" group="main-server-group">
        <socket-bindings port-offset="150"/>
        <!--Edit system properties for server-two-->
        <system-properties>
            <property name="applicationinsights.agent.id" value="..."/> 
        </system-properties>
    </server>
</servers>
...
```

A megadott `applicationinsights.agent.id` értéknek egyedinek kell lennie. A rendszer a applicationinsights könyvtárában létrehoz egy alkönyvtárat, mivel minden JVM-folyamathoz saját helyi applicationinsights-konfiguráció és helyi applicationinsights-naplófájl szükséges. Ha a központi gyűjtőnek jelent jelentést, a `applicationinsights.properties` fájlt a több felügyelt kiszolgáló is megosztja, ezért a megadott `applicationinsights.agent.id` érték szükséges a megosztott fájl `agent.id` beállításainak felülbírálásához. `applicationinsights.agent.rollup.id`Hasonlóképpen megadható a kiszolgálón, `system-properties` ha a `agent.rollup.id` beállítást egy felügyelt kiszolgálón kell felülbírálni.


## <a name="jetty-9"></a>3. Jetty

Sorok hozzáadása a következőhöz`start.ini`

```
--exec
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```


## <a name="payara-5"></a>5. Payara

Hozzáadás `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` a meglévőhöz `jvm-options` a `glassfish/domains/domain1/config/domain.xml`következőben:

```xml
...
<java-config ...>
    <!--Edit the JVM options here-->
    <jvm-options>
        -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar>
    </jvm-options>
        ...
</java-config>
...
```

## <a name="websphere-8"></a>WebSphere 8

A felügyeleti konzol megnyitásához nyissa meg a **kiszolgálók > WebSphere Application servers > Application Servers**elemet, válassza ki a megfelelő alkalmazásokat, és kattintson a következőre: 

```
Java and Process Management > Process definition >  Java Virtual Machine
```
Az "általános JVM argumentumok" elemnél adja hozzá a következőket:
```
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```
Ezután mentse és indítsa újra az alkalmazást.


## <a name="openliberty-18"></a>OpenLiberty 18

Hozzon létre egy `jvm.options` új fájlt a kiszolgáló könyvtárában ( `<openliberty>/usr/servers/defaultServer`például), és adja hozzá a következő sort:
```
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```
