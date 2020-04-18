---
title: Bármilyen környezetben futó Java-alkalmazások figyelése – Azure Monitor Application Insights
description: Alkalmazás teljesítményének figyelése java alkalmazások futó bármilyen környezetben a Java önálló ügynök az alkalmazás programozása nélkül. Elosztott nyomkövetés és alkalmazástérkép.
ms.topic: conceptual
ms.date: 04/16/2020
ms.openlocfilehash: 08a83fbc05276808b62a0391a5c4217cc09f6d00
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641874"
---
# <a name="configuring-jvm-args-java-standalone-agent-for-azure-monitor-application-insights"></a>JVM args Java önálló ügynök konfigurálása az Azure Monitor Application Insights számára



## <a name="azure-environments"></a>Azure-környezetek

[Az Alkalmazásszolgáltatások](https://docs.microsoft.com/azure/app-service/configure-language-java#set-java-runtime-options)konfigurálása .

## <a name="spring-boot"></a>Spring Boot

Adja hozzá a `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` JVM arg-ot valahol, `-jar <myapp.jar>`például:

```
java -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar -jar <myapp.jar>
```

> [!NOTE]
> Args után `-jar <myapp.jar>` elhelyezett át az alkalmazás program args.


## <a name="tomcat-8-linux"></a>Tomcat 8 (Linux)

### <a name="tomcat-installed-via-apt-get-or-yum"></a>A Tomcat-ot a `apt-get``yum`

Ha a Tomcat-ot a vagy `apt-get` `yum`a `/etc/tomcat8/tomcat8.conf`on keresztül telepítette, akkor rendelkeznie kell egy fájllal.  Adja hozzá ezt a sort a fájl végéhez:

```
JAVA_OPTS="$JAVA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

### <a name="tomcat-installed-via-download-and-unzip"></a>Tomcat keresztül telepített letöltés és csomagolja ki

Ha telepítette a Tomcat-ot [https://tomcat.apache.org](https://tomcat.apache.org)letöltéssel és a `<tomcat>/bin/catalina.sh`kicsomagolással a ból, akkor rendelkeznie kell egy fájllal.  Hozzon létre egy új `<tomcat>/bin/setenv.sh` fájlt ugyanabban a könyvtárban, amelynek neve a következő tartalommal:

```
CATALINA_OPTS="$CATALINA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

Ha a `<tomcat>/bin/setenv.sh` fájl már létezik, `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` módosítsa `CATALINA_OPTS`a fájlt, és adja hozzá a hoz.


## <a name="tomcat-8-windows"></a>Tomcat 8 (Windows)

### <a name="running-tomcat-from-the-command-line"></a>A Tomcat futtatása a parancssorból

Keresse meg `<tomcat>/bin/catalina.bat`a fájlt .  Hozzon létre egy új `<tomcat>/bin/setenv.bat` fájlt ugyanabban a könyvtárban, amelynek neve a következő tartalommal:

```
set CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```

Az árajánlatok nem szükségesek, de ha meg szeretné adni őket, a megfelelő elhelyezés:

```
set "CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

Ha a `<tomcat>/bin/setenv.bat` fájl már létezik, `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` csak `CATALINA_OPTS`módosítsa a fájlt, és adja hozzá a hoz.

### <a name="running-tomcat-as-a-windows-service"></a>A Tomcat futtatása Windows-szolgáltatásként

Keresse meg `<tomcat>/bin/tomcat8w.exe`a fájlt .  Futtassa a `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` végrehajtható `Java Options` fájlt, `Java` és adja hozzá a lap alá.


## <a name="jboss-eap-7"></a>JBoss EAP, 7.

### <a name="standalone-server"></a>Önálló kiszolgáló

Hozzáadás `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` a `JAVA_OPTS` fájlban `JBOSS_HOME/bin/standalone.conf` (Linux) vagy `JBOSS_HOME/bin/standalone.conf.bat` (Windows) meglévő környezeti változóhoz:

```java    ...
    JAVA_OPTS="<b>-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar</b> -Xms1303m -Xmx1303m ..."
    ...
```

### <a name="domain-server"></a>Tartományi kiszolgáló

Adja `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` hozzá `jvm-options` a `JBOSS_HOME/domain/configuration/host.xml`meglévő:

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

Ha több felügyelt kiszolgálót futtat egyetlen állomáson, `applicationinsights.agent.id` akkor `system-properties` mindegyikhez `server`hozzá kell adnia a következőket:

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

A megadott `applicationinsights.agent.id` értéknek egyedinek kell lennie. Az alkalmazáselemzési könyvtár alatt alkönyvtár at hozhat létre, mivel minden JVM-folyamatnak saját helyi applicationinsights konfigurációra és helyi applicationinsights naplófájlra van szüksége. Ha a központi gyűjtőnek is `applicationinsights.properties` jelentést tesz, a fájlt a több `applicationinsights.agent.id` felügyelt kiszolgáló megosztja, ezért a megadott fájlra van szükség a `agent.id` megosztott fájl ban lévő beállítás felülbírálásához. `applicationinsights.agent.rollup.id`hasonlóan megadható a kiszolgálónál, `system-properties` ha felül kell bírálnia a `agent.rollup.id` felügyelt kiszolgálónkénti beállítást.


## <a name="jetty-9"></a>Móló 9

Adja hozzá ezeket a sorokat`start.ini`

```
--exec
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```


## <a name="payara-5"></a>Payara, 5.

Adja `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` hozzá `jvm-options` a `glassfish/domains/domain1/config/domain.xml`meglévő:

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

Nyissa meg a Management Console alkalmazást, nyissa meg **a websphere alkalmazáskiszolgálókat > > alkalmazáskiszolgálókat,** válassza ki a megfelelő alkalmazáskiszolgálókat, és kattintson a következőkre: 

```
Java and Process Management > Process definition >  Java Virtual Machine
```
Az "Általános JVM-argumentumok" adja hozzá a következőket:
```
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```
Ezután mentse és indítsa újra az alkalmazáskiszolgálót.


## <a name="openliberty-18"></a>OpenLiberty 18

Hozzon létre `jvm.options` egy új fájlt `<openliberty>/usr/servers/defaultServer`a kiszolgálókönyvtárban (például ), és adja hozzá ezt a sort:
```
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```
