---
title: Windows Java-alkalmazások konfigurálása
description: Megtudhatja, hogyan konfigurálhat Java-alkalmazásokat a Azure App Service Windows rendszerű virtuálisgép-példányain való futtatáshoz. Ez a cikk a leggyakoribb konfigurációs feladatokat ismerteti.
keywords: Azure app Service, webalkalmazás, Windows, OSS, Java
author: jasonfreeberg
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18, devx-track-java
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 30d5fa329131cdfd380a84843b3ba202b2e22e39
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88080130"
---
# <a name="configure-a-java-app-for-azure-app-service"></a>Java-alkalmazás konfigurálása Azure App Servicehoz

::: zone pivot="platform-windows"  

Azure App Service lehetővé teszi a Java-fejlesztők számára, hogy a Tomcat webalkalmazásaikat teljes mértékben felügyelt, Windows-alapú szolgáltatáson hozza létre, helyezheti üzembe és méretezheti. Alkalmazásokat telepíthet a Maven beépülő modulokból a parancssorból vagy olyan szerkesztőkből, mint például a IntelliJ, az Eclipse vagy a Visual Studio code.

Ez az útmutató a Java-fejlesztőknek a App Service-ban való használatával kapcsolatos főbb fogalmakat és útmutatást tartalmazza. Ha még soha nem használta a Azure App Servicet, először olvassa el a [Java](quickstart-java.md) rövid útmutatóját. A Java-fejlesztésre nem jellemző App Service használatára vonatkozó általános kérdéseket a [Windows app Service gyakori](faq-configuration-and-management.md)kérdések című szakaszban találja.

## <a name="deploying-your-app"></a>Az alkalmazás üzembe helyezése

A [mavenhez készült Azure Web App beépülő modullal](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) üzembe helyezheti a. War fájlokat. A népszerű ide-val történő üzembe helyezést [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/) vagy [Azure Toolkit for Eclipse](/azure/developer/java/toolkit-for-eclipse)is támogatja.

Ellenkező esetben a telepítési módszer az archiválás típusától függ:

- A. War fájlok Tomcatbe való üzembe helyezéséhez használja a `/api/wardeploy/` végpontot az archív fájl közzétételéhez. Az API-val kapcsolatos további információkért tekintse meg [ezt a dokumentációt](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file).
- Ha a. jar fájlokat a Java SE-re szeretné telepíteni, használja a `/api/zipdeploy/` kudu-hely végpontját. Az API-val kapcsolatos további információkért tekintse meg [ezt a dokumentációt](https://docs.microsoft.com/azure/app-service/deploy-zip#rest).

Ne telepítse a. War vagy a. jar fájlt FTP használatával. Az FTP-eszköz indítási parancsfájlok, függőségek vagy más futásidejű fájlok feltöltésére szolgál. A webalkalmazások üzembe helyezése nem optimális megoldás.

## <a name="logging-and-debugging-apps"></a>Alkalmazások naplózása és hibakeresése

A teljesítménnyel kapcsolatos jelentések, a forgalmi vizualizációk és az állapot-kivizsgálások a Azure Portalon keresztül érhetők el az egyes alkalmazásokhoz. További információ: [Azure app Service diagnosztika áttekintése](overview-diagnostics.md).

### <a name="use-flight-recorder"></a>A Flight Recorder használata

Az Azul JVMs-t használó App Service Java-futtatókörnyezetek a Zulu Flight Recorder használatával jönnek. Ezt a JVM, a rendszerek és a Java szintű események rögzítésére használhatja a Java-alkalmazások viselkedésének figyeléséhez és a problémák elhárításához.

Ha időzített rögzítést szeretne készíteni, szüksége lesz a Java-alkalmazás PID-re (Process ID). A PID megkereséséhez nyisson meg egy böngészőt a webalkalmazás SCM-webhelyéhez a https://<saját-hely neve>. scm.azurewebsites.net/ProcessExplorer/. Ezen az oldalon a webalkalmazás futó folyamatai láthatók. Keresse meg a "Java" nevű folyamatot a táblában, és másolja a megfelelő PID-t (Process ID).

Ezután nyissa meg a **hibakeresési konzolt** az SCM-hely felső eszköztárán, és futtassa a következő parancsot. Cserélje le `<pid>` a helyére a korábban átmásolt folyamat azonosítóját. Ez a parancs elindítja a Java-alkalmazás 30 másodperces adatrögzítését, és létrehozza a `timed_recording_example.jfr` címtárban megnevezett fájlt `D:\home` .

```
jcmd <pid> JFR.start name=TimedRecording settings=profile duration=30s filename="D:\home\timed_recording_example.JFR"
```

További információkért tekintse meg a [Jcmd-parancs referenciáját](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190).

#### <a name="analyze-jfr-files"></a>`.jfr`Fájlok elemzése

A [FTPS](deploy-ftp.md) használatával töltse le a JFR-fájlt a helyi gépre. A JFR-fájl elemzéséhez töltse le és telepítse a [Zulu Mission Controlt](https://www.azul.com/products/zulu-mission-control/). A Zulu-feladatok ellenőrzésével kapcsolatos utasításokért tekintse meg az [Azul dokumentációját](https://docs.azul.com/zmc/) és a [telepítési utasításokat](https://docs.microsoft.com/java/azure/jdk/java-jdk-flight-recorder-and-mission-control).

### <a name="stream-diagnostic-logs"></a>Diagnosztikai naplók streamelése

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

További információ: [stream-naplók Cloud Shellban](troubleshoot-diagnostic-logs.md#in-cloud-shell).

### <a name="app-logging"></a>Alkalmazás naplózása

Az Azure Portal vagy az [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) -n keresztül történő [alkalmazás-naplózás](troubleshoot-diagnostic-logs.md#enable-application-logging-windows) engedélyezésével beállíthatja, hogy a app Service az alkalmazás szabványos konzoljának kimenetét és standard konzoljának hibáit a helyi fájlrendszerbe vagy az Azure-Blob Storageba írja. A helyi App Service filesystem-példányra való naplózás a konfigurálás után 12 órával le van tiltva. Ha nagyobb adatmegőrzésre van szüksége, konfigurálja úgy az alkalmazást, hogy egy blob Storage-tárolóba írja a kimenetet. A Java-és a Tomcat-alkalmazás naplói a */LogFiles/Application/* könyvtárban találhatók.

Ha az alkalmazás [Logback](https://logback.qos.ch/) -t vagy [Log4j](https://logging.apache.org/log4j) -t használ a nyomkövetéshez, továbbíthatja ezeket a nyomkövetéseket az Azure Application Insightsba való áttelepítéshez a naplózási keretrendszer konfigurációs utasításait követve, a [Java-nyomkövetési naplók megismeréséhez Application Insights](/azure/application-insights/app-insights-java-trace-logs).


## <a name="customization-and-tuning"></a>Testreszabás és Finomhangolás

A Azure App Service a Azure Portal és a CLI használatával támogatja a Box finomhangolását és testreszabását. Tekintse át a következő cikkeket a nem Java-specifikus webalkalmazás-konfigurációhoz:

- [Alkalmazásbeállítások konfigurálása](configure-common.md#configure-app-settings)
- [Egyéni tartomány beállítása](app-service-web-tutorial-custom-domain.md)
- [TLS-kötések konfigurálása](configure-ssl-bindings.md)
- [CDN hozzáadása](../cdn/cdn-add-to-web-app.md)
- [A kudu hely konfigurálása](https://github.com/projectkudu/kudu/wiki/Configurable-settings)

### <a name="set-java-runtime-options"></a>Java-futtatókörnyezet beállításainak megadása

A lefoglalt memória vagy más JVM futásidejű beállításainak megadásához hozzon létre egy nevű [alkalmazást](configure-common.md#configure-app-settings) `JAVA_OPTS` a következő beállításokkal:. App Service átadja ezt a beállítást környezeti változóként a Java futtatókörnyezetnek a indításakor.

A Azure Portal a webalkalmazás **Alkalmazásbeállítások** területén hozzon létre egy nevű új alkalmazás-beállítást `JAVA_OPTS` , amely tartalmazza a további beállításokat, például a következőt: `-Xms512m -Xmx1204m` .

Az Alkalmazásbeállítások a Maven beépülő modulból történő konfigurálásához adja hozzá a beállítás/érték címkéket az Azure beépülő modul szakaszban. A következő példa egy meghatározott minimális és maximális Java-halom méretét állítja be:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

A App Service tervében egyetlen üzembe helyezési ponttal rendelkező fejlesztők a következő lehetőségeket használhatják:

- B1 és S1 példányok:`-Xms1024m -Xmx1024m`
- B2 és S2 példányok:`-Xms3072m -Xmx3072m`
- B3-as és S3-példányok:`-Xms6144m -Xmx6144m`

Az alkalmazás-halom beállításainak hangolásához tekintse át a App Service terv részleteit, és vegye figyelembe, hogy a memória optimális elosztása érdekében több alkalmazásra és üzembe helyezési pontra van szükség.

### <a name="turn-on-web-sockets"></a>Webes szoftvercsatornák bekapcsolása

A WebSockets támogatásának bekapcsolása a Azure Portal az alkalmazás **alkalmazás-beállításaiban** . A beállítás érvénybe léptetéséhez újra kell indítania az alkalmazást.

Kapcsolja be a web socket-támogatást az Azure CLI használatával a következő paranccsal:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --web-sockets-enabled true
```

Ezután indítsa újra az alkalmazást:

```azurecli-interactive
az webapp stop --name <app-name> --resource-group <resource-group-name>
az webapp start --name <app-name> --resource-group <resource-group-name>
```

### <a name="set-default-character-encoding"></a>Alapértelmezett karakterkódolás beállítása

A Azure Portal a webalkalmazás **Alkalmazásbeállítások** területén hozzon létre egy új, értékkel ellátott alkalmazás-beállítást `JAVA_OPTS` `-Dfile.encoding=UTF-8` .

Azt is megteheti, hogy az App Service Maven beépülő modullal konfigurálhatja az alkalmazás beállításait. Adja hozzá a beállítás neve és értéke címkéket a beépülő modul konfigurációjában:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

### <a name="pre-compile-jsp-files"></a>JSP-fájlok előre fordítása

A Tomcat-alkalmazások teljesítményének növelése érdekében lefordíthatja a JSP-fájljait, mielőtt telepítené a App Service. Használhatja az Apache parittya által biztosított [Maven beépülő modult](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) , vagy használja ezt az [Ant-Build fájlt](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation).

## <a name="secure-applications"></a>Biztonságos alkalmazások

A App Serviceban futó Java-alkalmazásokhoz ugyanaz a [biztonsági eljárások](/azure/security/security-paas-applications-using-app-services) tartoznak, mint más alkalmazások.

### <a name="authenticate-users-easy-auth"></a>Felhasználók hitelesítése (egyszerű hitelesítés)

Az alkalmazás hitelesítésének beállítása a Azure Portal a **hitelesítés és engedélyezés** lehetőséggel. Itt engedélyezheti a hitelesítést Azure Active Directory vagy közösségi bejelentkezéssel, például a Facebook, a Google vagy a GitHub használatával. Azure Portal konfiguráció csak egyetlen hitelesítési szolgáltató konfigurálásakor működik. További információ: [a app Service alkalmazás konfigurálása Azure Active Directory bejelentkezési adatok használatára](configure-authentication-provider-aad.md) és a kapcsolódó cikkek más identitás-szolgáltatóknak való használatára. Ha több bejelentkezési szolgáltatót is engedélyeznie kell, kövesse az [app Service-hitelesítés testreszabása](app-service-authentication-how-to.md) című cikk utasításait.

#### <a name="tomcat"></a>Tomcat

A Tomcat-alkalmazás közvetlenül a servletből férhet hozzá a felhasználó jogcímeihez, ha a fő objektumot egy Térkép objektummá helyezi. A Térkép objektum az egyes jogcím-típusokat az adott típusú jogcímek gyűjteményéhez rendeli. Az alábbi kódban a `request` egy példánya `HttpServletRequest` .

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

Most már megvizsgálhatja az `Map` objektumot egy adott jogcím esetében is. A következő kódrészlet például megismétli az összes jogcím-típust, és kinyomtatja az egyes gyűjtemények tartalmát.

```java
for (Object key : map.keySet()) {
        Object value = map.get(key);
        if (value != null && value instanceof Collection {
            Collection claims = (Collection) value;
            for (Object claim : claims) {
                System.out.println(claims);
            }
        }
    }
```

A felhasználók kijelentkezéséhez használja az `/.auth/ext/logout` elérési utat. További műveletek elvégzéséhez tekintse meg [app Service hitelesítési és engedélyezési használati](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to)dokumentációját. A Tomcat [HttpServletRequest felületén](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) és annak módszerein is hivatalos dokumentáció található. A következő servlet metódusok is hidratálva vannak a App Service konfigurációja alapján:

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

A funkció letiltásához hozzon létre egy nevű Alkalmazásbeállítás `WEBSITE_AUTH_SKIP_PRINCIPAL` értéket `1` . A App Service által hozzáadott összes servlet-szűrő letiltásához hozzon létre egy nevű beállítást a következő `WEBSITE_SKIP_FILTERS` értékkel: `1` .

### <a name="configure-tlsssl"></a>A TLS/SSL konfigurálása

A meglévő TLS/SSL-tanúsítvány feltöltéséhez és az alkalmazás tartománynevéhez való kötéséhez kövesse az [Egyéni DNS-név biztonságossá tétele TLS-kötéssel Azure app Serviceban található](configure-ssl-bindings.md) utasításokat. Alapértelmezés szerint az alkalmazás továbbra is engedélyezi a HTTP-kapcsolatokat – az oktatóanyag adott lépéseinek végrehajtásával kényszerítheti az SSL és a TLS használatát.

### <a name="use-keyvault-references"></a>Kulcstartó-hivatkozások használata

Az [Azure](../key-vault/general/overview.md) kulcstartó központosított titkos felügyeletet biztosít a hozzáférési házirendekkel és a naplózási előzményekkel. A kulcsok (például jelszavak vagy kapcsolati karakterláncok) a kulcstartóban tárolhatók, és a titkos kulcsokat az alkalmazásban környezeti változókon keresztül érhetik el.

Először is kövesse az alkalmazáshoz [való hozzáférés megadására](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) vonatkozó utasításokat, és a [titkos kulcshoz tartozó kulcstároló-hivatkozást egy Alkalmazásbeállítások alapján](app-service-key-vault-references.md#reference-syntax)végezze el a Key Vault. Ellenőrizheti, hogy a hivatkozás feloldja-e a titkos kulcsot a környezeti változó kinyomtatásával, miközben távolról fér hozzá a App Service terminálhoz.

Ha ezeket a titkokat be szeretné szúrni a Spring vagy a Tomcat konfigurációs fájljába, használja a környezeti változók befecskendezésének szintaxisát ( `${MY_ENV_VAR}` ). A Spring konfigurációs fájlok esetében tekintse meg ezt a dokumentációt a [külső konfigurációkról](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).


## <a name="configure-apm-platforms"></a>APM-platformok konfigurálása

Ez a szakasz bemutatja, hogyan csatlakoztathatók a Linux rendszeren üzembe Azure App Service helyezett Java-alkalmazások a NewRelic és a AppDynamics Application Performance monitoring (APM) platformokkal.

### <a name="configure-new-relic"></a>Új ereklye konfigurálása

1. Új ereklye fiók létrehozása a [NewRelic.com](https://newrelic.com/signup) -on
2. Töltse le a Java-ügynököt a NewRelic webhelyről, és a fájl neve hasonló lesz a *newrelic-java-x.x.x.ziphoz *.
3. A licenckulcs másolásához az ügynököt később kell konfigurálnia.
4. Hozzon létre egy új címtár- */Home/site/wwwroot/APM*a [kudu-konzol](https://github.com/projectkudu/kudu/wiki/Kudu-console) használatával.
5. Töltse fel a kicsomagolt új ereklye Java-ügynök fájljait egy könyvtárba a */Home/site/wwwroot/APM*alatt. Az ügynök fájljainak a */Home/site/wwwroot/APM/newrelic*-ben kell lenniük.
6. Módosítsa a YAML fájlt a */Home/site/wwwroot/APM/newrelic/newrelic.YML* címen, és cserélje le a helyőrző licenc értékét a saját licenckulcs használatára.
7. A Azure Portal tallózással keresse meg az alkalmazást App Service és hozzon létre egy új alkalmazás-beállítást.
    - Ha az alkalmazás **Java SE**-t használ, hozzon létre egy nevű környezeti változót `JAVA_OPTS` az értékkel `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .
    - Ha a **tomcat**-t használja, hozzon létre egy nevű környezeti változót `CATALINA_OPTS` az értékkel `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .

### <a name="configure-appdynamics"></a>AppDynamics konfigurálása

1. AppDynamics-fiók létrehozása a [AppDynamics.com](https://www.appdynamics.com/community/register/) -ben
2. Töltse le a Java-ügynököt a AppDynamics webhelyről, a fájl neve hasonló lesz *AppServerAgent-x.x.x.xxxxx.zip*
3. Hozzon létre egy új címtár- */Home/site/wwwroot/APM*a [kudu-konzol](https://github.com/projectkudu/kudu/wiki/Kudu-console) használatával.
4. Töltse fel a Java-ügynök fájljait egy könyvtárba a */Home/site/wwwroot/APM*alatt. Az ügynök fájljainak a */Home/site/wwwroot/APM/appdynamics*-ben kell lenniük.
5. A Azure Portal tallózással keresse meg az alkalmazást App Service és hozzon létre egy új alkalmazás-beállítást.
    - Ha **Java SE**-t használ, hozzon létre egy nevű környezeti változót, amelynek `JAVA_OPTS` értéke a `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` `<app-name>` app Service neve.
    - Ha a **tomcat**-t használja, hozzon létre egy nevű környezeti változót, amelynek `CATALINA_OPTS` értéke a `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` `<app-name>` app Service neve.

>  Ha már rendelkezik környezeti változóval a vagy a esetében `JAVA_OPTS` `CATALINA_OPTS` , az `-javaagent:/...` aktuális érték végéhez fűzze hozzá a kapcsolót.

## <a name="data-sources"></a>Adatforrások

### <a name="tomcat"></a>Tomcat

Ezek az utasítások az összes adatbázis-kapcsolatra érvényesek. A helyőrzőket ki kell töltenie a kiválasztott adatbázis illesztőprogram-osztályának nevével és JAR-fájljával. A megadott tábla az osztályok neveivel és az illesztőprogramok letöltésével közös adatbázisokhoz.

| Adatbázis   | Illesztőprogram-osztály neve                             | JDBC-illesztőprogram                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Letöltés](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Letöltés](https://dev.mysql.com/downloads/connector/j/) (válassza a "platform független" lehetőséget) |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Letöltés](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#download)                                                           |

Ha a Tomcat-t a Java Database Connectivity (JDBC) vagy a Java perzisztencia API (közös parlamenti) használatára szeretné konfigurálni, először testre kell szabnia a `CATALINA_OPTS` tomcat által az indításkor beolvasott környezeti változót. Adja meg ezeket az értékeket a [app Service Maven beépülő modul](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md)alkalmazás-beállításán keresztül:

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Vagy állítsa be a környezeti változókat a Azure Portal **konfigurációs**  >  **alkalmazás beállításai** lapján.

Ezután állapítsa meg, hogy az adatforrásnak elérhetőnek kell lennie egy alkalmazáshoz vagy a Tomcat servletben futó összes alkalmazáshoz.

#### <a name="application-level-data-sources"></a>Alkalmazás szintű adatforrások

1. Hozzon létre egy *context.xml* fájlt a projekt *META-INF-* fájljában/könyvtárában. Ha nem létezik, hozza létre a *META-INF/* könyvtárat.

2. A *context.xmlban *adjon hozzá egy `Context` elemet, amely összekapcsolja az adatforrást egy JNDI-címnek. Cserélje le a `driverClassName` helyőrzőt az illesztőprogram osztályának nevére a fenti táblázatból.

    ```xml
    <Context>
        <Resource
            name="jdbc/dbconnection"
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}"
            password="${connURL}"
        />
    </Context>
    ```

3. Frissítse alkalmazása *web.xml* az alkalmazás adatforrásának használatára.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="finalize-configuration"></a>Konfiguráció véglegesítése

Végül elhelyezjük az illesztőprogram-tégelyeket a Tomcat osztályútvonal, és újra kell indítani a App Service. Győződjön meg arról, hogy a */Home/tomcat/lib* könyvtárba HELYEZVE a JDBC-illesztőprogram fájljai elérhetők a Tomcat ClassLoader. (Ha még nem létezik, hozza létre ezt a könyvtárat.) Ha ezeket a fájlokat fel szeretné tölteni a App Service-példányba, hajtsa végre a következő lépéseket:

1. A [Cloud Shell](https://shell.azure.com)telepítse a WebApp bővítményt:

    ```azurecli-interactive
    az extension add -–name webapp
    ```

2. A következő CLI-parancs futtatásával hozzon létre egy SSH-alagutat a helyi rendszerből a App Serviceához:

    ```azurecli-interactive
    az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
    ```

3. Kapcsolódjon a helyi bújtatási porthoz az SFTP-ügyféllel, és töltse fel a fájlokat a */Home/tomcat/lib* mappába.

Azt is megteheti, hogy FTP-ügyfél használatával tölti fel a JDBC-illesztőt. Az [FTP-hitelesítő adatok beszerzéséhez kövesse az alábbi utasításokat](deploy-configure-credentials.md).

## <a name="configuring-tomcat"></a>A Tomcat konfigurálása

A Tomcat `server.xml` vagy más konfigurációs fájlok szerkesztéséhez először jegyezze fel a Tomcat főverzióját a portálon.

1. A parancs futtatásával keresse meg a verzióhoz tartozó tomcat-kezdőkönyvtárat `env` . Keresse meg azt a környezeti változót, amely a-val kezdődik, `AZURE_TOMCAT` és megfelel a főverziónak. Például a Tomcat `AZURE_TOMCAT85_HOME` 8,5-es tomcat-könyvtárába mutat.
1. Miután azonosította a-verzióhoz tartozó tomcat-kezdőkönyvtárat, másolja a konfigurációs könyvtárat a következőre: `D:\home` . Ha például a `AZURE_TOMCAT85_HOME` értéke volt `D:\Program Files (x86)\apache-tomcat-8.5.37` , a másolt könyvtár új elérési útja a következő lesz: `D:\home\apache-tomcat-8.5.37` .

Végezetül indítsa újra a App Service. A központi telepítéseknek ugyanúgy kell megjelenniük, `D:\home\site\wwwroot\webapps` mint korábban.

## <a name="configure-java-se"></a>A Java SE konfigurálása

A futtatásakor. A Windows rendszeren futó Java SE-alkalmazás, `server.port` amely az alkalmazás indításakor parancssori kapcsolóként lesz átadva. A HTTP-port manuálisan is feloldható a környezeti változóból `HTTP_PLATFORM_PORT` . Ennek a környezeti változónak az értéke lesz az alkalmazás által figyelni kívánt HTTP-port. 

## <a name="java-runtime-statement-of-support"></a>A Java futtatókörnyezet támogatási nyilatkozata

### <a name="jdk-versions-and-maintenance"></a>JDK-verziók és-karbantartás

Az Azure által támogatott Java Development Kit (JDK) a [Azul Systems](https://www.azul.com/)által biztosított [Zulu](https://www.azul.com/downloads/azure-only/zulu/) .

A főverzió frissítései a Windows Azure App Service új futtatókörnyezeti lehetőségein keresztül lesznek elérhetők. Az ügyfelek a Java újabb verzióit frissítik a App Service üzembe helyezésének konfigurálásával, és felelősek a fő frissítés megfelelőségének teszteléséhez és biztosításához.

A támogatott JDK minden év januárjában, áprilisban, júliusban és októberben automatikusan megtörténik. Az Azure-beli Java-ról további információt [ebben a támogatási dokumentumban](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-long-term-support)talál.

### <a name="security-updates"></a>Biztonsági frissítések

A főbb biztonsági rések javításait és javításait a rendszer azonnal felszabadítja, amint azok elérhetők lesznek a Azul Systems-től. A "fő" biztonsági rést az 9,0-es vagy újabb alappontszám határozza meg a [NIST Common sebezhetőségi pontozási rendszer 2. verziójában](https://nvd.nist.gov/vuln-metrics/cvss).

A Tomcat 8,0 [2018. szeptember 30-ig elérte az élettartamot (EOL)](https://tomcat.apache.org/tomcat-80-eol.html). Habár a futtatókörnyezet továbbra is elérhető a Azure App Serviceon, az Azure nem alkalmazza a biztonsági frissítéseket a Tomcat 8,0-es verzióra. Ha lehetséges, telepítse át az alkalmazásokat a Tomcat 8,5 vagy a 9,0-es verzióra. A Tomcat 8,5 és 9,0 egyaránt elérhető Azure App Serviceon. További információért tekintse meg a [hivatalos tomcat-webhelyet](https://tomcat.apache.org/whichversion.html) . 

### <a name="deprecation-and-retirement"></a>Elavulás és nyugdíjazás

Ha egy támogatott Java-futtatókörnyezet megszűnik, az érintett futtatókörnyezetet használó Azure-fejlesztők elavult értesítést kapnak a futtatókörnyezet kivonása előtt legalább hat hónappal.

### <a name="local-development"></a>Helyi fejlesztés

A fejlesztők letölthetik az Azul Zulu Enterprise JDK éles kiadását helyi fejlesztésre az [Azul letöltési webhelyéről](https://www.azul.com/downloads/azure-only/zulu/).

### <a name="development-support"></a>Fejlesztési támogatás

Az Azure [által támogatott Azul ZULU JDK-](https://www.azul.com/downloads/azure-only/zulu/) vel kapcsolatos terméktámogatás a Microsofton keresztül érhető el az Azure-hoz való fejlesztéshez, illetve a [Azure stack](https://azure.microsoft.com/overview/azure-stack/) egy [minősített Azure-támogatási csomaggal](https://azure.microsoft.com/support/plans/).

### <a name="runtime-support"></a>Futtatókörnyezet támogatása

A fejlesztők az Azure-támogatással [megnyithatják](/azure/azure-portal/supportability/how-to-create-azure-support-request) az Azul Zulu JDK kapcsolatos problémákat, ha rendelkeznek [minősített támogatási csomaggal](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>További lépések

Ez a témakör a Java Runtime Azure App Service Windows rendszeren történő támogatására vonatkozó utasításait ismerteti.

- Ha többet szeretne megtudni a webalkalmazások Azure App Serviceről való üzemeltetéséről, tekintse meg a [app Service áttekintését](overview.md).
- További információ az Azure-beli Java-fejlesztésről: [Azure for Java fejlesztői központ](https://docs.microsoft.com/java/azure/?view=azure-java-stable).

::: zone-end

::: zone pivot="platform-linux"

A Linuxon futó Azure App Service lehetővé teszi a Java-fejlesztők számára, hogy a Tomcat vagy Java Standard Edition (SE) csomagban lévő webalkalmazásokat egy teljes körűen felügyelt Linux-alapú szolgáltatáson hozza létre, telepítse és méretezheti. Alkalmazásokat telepíthet a Maven beépülő modulokból a parancssorból vagy olyan szerkesztőkből, mint például a IntelliJ, az Eclipse vagy a Visual Studio code.

Ez az útmutató olyan Java-fejlesztőknek nyújt főbb fogalmakat és útmutatást, amelyek a App Service beépített Linux-tárolóját használják. Ha még soha nem használta a Azure App Servicet, kövesse a [Java](quickstart-java.md)rövid útmutatót.

## <a name="deploying-your-app"></a>Az alkalmazás üzembe helyezése

A [Maven beépülő modult használhatja Azure app Service](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) a. jar és a. War fájlok telepítéséhez. A népszerű ide-val történő üzembe helyezést [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/) vagy [Azure Toolkit for Eclipse](/azure/developer/java/toolkit-for-eclipse)is támogatja.

Ellenkező esetben a telepítési módszer az archiválás típusától függ:

- A. War fájlok Tomcatbe való üzembe helyezéséhez használja a `/api/wardeploy/` végpontot az archív fájl közzétételéhez. Az API-val kapcsolatos további információkért tekintse meg [ezt a dokumentációt](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file).
- A. jar fájlok Java SE-lemezképeken való üzembe helyezéséhez használja a `/api/zipdeploy/` kudu-hely végpontját. Az API-val kapcsolatos további információkért tekintse meg [ezt a dokumentációt](https://docs.microsoft.com/azure/app-service/deploy-zip#rest).

Ne telepítse a. War vagy a. jar fájlt FTP használatával. Az FTP-eszköz indítási parancsfájlok, függőségek vagy más futásidejű fájlok feltöltésére szolgál. A webalkalmazások üzembe helyezése nem optimális megoldás.

## <a name="logging-and-debugging-apps"></a>Alkalmazások naplózása és hibakeresése

A teljesítménnyel kapcsolatos jelentések, a forgalmi vizualizációk és az állapot-kivizsgálások a Azure Portalon keresztül érhetők el az egyes alkalmazásokhoz. További információ: [Azure app Service diagnosztika áttekintése](overview-diagnostics.md).

### <a name="ssh-console-access"></a>SSH-konzolhoz való hozzáférés

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>Diagnosztikai naplók streamelése

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

További információ: [stream-naplók Cloud Shellban](troubleshoot-diagnostic-logs.md#in-cloud-shell).

### <a name="app-logging"></a>Alkalmazás naplózása

Az Azure Portal vagy az [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) -n keresztül történő [alkalmazás-naplózás](troubleshoot-diagnostic-logs.md#enable-application-logging-windows) engedélyezésével beállíthatja, hogy a app Service az alkalmazás szabványos konzoljának kimenetét és standard konzoljának hibáit a helyi fájlrendszerbe vagy az Azure-Blob Storageba írja. A helyi App Service filesystem-példányra való naplózás a konfigurálás után 12 órával le van tiltva. Ha nagyobb adatmegőrzésre van szüksége, konfigurálja úgy az alkalmazást, hogy egy blob Storage-tárolóba írja a kimenetet. A Java-és a Tomcat-alkalmazás naplói a */Home/LogFiles/Application/* könyvtárban találhatók.

>[!NOTE]
>A helyi App Service fájlrendszerbe való bejelentkezés 12 óra elteltével letiltva, csak a Windows-alapú App Servicesokra vonatkozik. Az Azure Blob Storage Linux-alapú App Services naplózása csak [Azure monitor (előzetes verzió)](/azure/app-service/troubleshoot-diagnostic-logs#send-logs-to-azure-monitor-preview) használatával konfigurálható 

Ha az alkalmazás [Logback](https://logback.qos.ch/) -t vagy [Log4j](https://logging.apache.org/log4j) -t használ a nyomkövetéshez, továbbíthatja ezeket a nyomkövetéseket az Azure Application Insightsba való áttelepítéshez a naplózási keretrendszer konfigurációs utasításait követve, a [Java-nyomkövetési naplók megismeréséhez Application Insights](/azure/application-insights/app-insights-java-trace-logs).

### <a name="troubleshooting-tools"></a>Hibaelhárítási eszközök

A beépített Java-lemezképek az [alpesi Linux](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) operációs rendszeren alapulnak. A `apk` Package Manager használatával telepítse a hibaelhárítási eszközöket és parancsokat.

### <a name="flight-recorder"></a>Flight Recorder

A App Service összes linuxos Java-rendszerképe telepítve van a Zulu Flight Recorder használatával, így egyszerűen csatlakozhat a JVM, és megkezdheti a Profiler-rögzítést, vagy létrehozhat egy halom-memóriaképet.

#### <a name="timed-recording"></a>Időzített rögzítés

Első lépésként SSH-t a App Serviceba, és futtassa a parancsot, és `jcmd` tekintse meg az összes futó Java-folyamat listáját. A jcmd mellett egy folyamat-azonosító számmal (PID) rendelkező Java-alkalmazást is látnia kell.

```shell
078990bbcd11:/home# jcmd
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
147 sun.tools.jcmd.JCmd
116 /home/site/wwwroot/app.jar
```

Hajtsa végre az alábbi parancsot a JVM 30 másodperces rögzítésének elindításához. Ezzel felveszi a JVM, és létrehoz egy *jfr_example. JFR* nevű JFR-fájlt a saját könyvtárában. (A 116 helyére írja be a Java-alkalmazás PID-t.)

```shell
jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"
```

A 30 másodperces intervallumban ellenőrizheti, hogy a rögzítés a futtatásával zajlik-e `jcmd 116 JFR.check` . Ez a művelet megjeleníti a megadott Java-folyamat összes felvételét.

#### <a name="continuous-recording"></a>Folyamatos rögzítés

A Zulu Flight Recorder segítségével folyamatosan, a Java-alkalmazásait a futtatási teljesítményre ([forrás](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf)) gyakorolt minimális hatású profilt használhatja. Ehhez futtassa az alábbi Azure CLI-parancsot egy JAVA_OPTS nevű alkalmazás-beállítás létrehozásához a szükséges konfigurációval. Az alkalmazás elindításakor a rendszer átadja a parancsnak a JAVA_OPTS Alkalmazásbeállítások tartalmát `java` .

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

A rögzítés megkezdése után bármikor leküldheti az aktuális rögzítési időt a parancs használatával `JFR.dump` .

```shell
jcmd <pid> JFR.dump name=continuous_recording filename="/home/recording1.jfr"
```

További információkért tekintse meg a [Jcmd-parancs referenciáját](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190).

### <a name="analyzing-recordings"></a>Felvételek elemzése

A [FTPS](deploy-ftp.md) használatával töltse le a JFR-fájlt a helyi gépre. A JFR-fájl elemzéséhez töltse le és telepítse a [Zulu Mission Controlt](https://www.azul.com/products/zulu-mission-control/). A Zulu-feladatok ellenőrzésével kapcsolatos utasításokért tekintse meg az [Azul dokumentációját](https://docs.azul.com/zmc/) és a [telepítési utasításokat](https://docs.microsoft.com/java/azure/jdk/java-jdk-flight-recorder-and-mission-control).

## <a name="customization-and-tuning"></a>Testreszabás és Finomhangolás

A Linux rendszerhez készült Azure App Service a Azure Portal és a CLI használatával támogatja a Box finomhangolását és testreszabását. Tekintse át a következő cikkeket a nem Java-specifikus webalkalmazás-konfigurációhoz:

- [Alkalmazásbeállítások konfigurálása](configure-common.md#configure-app-settings)
- [Egyéni tartomány beállítása](app-service-web-tutorial-custom-domain.md)
- [SSL-kötések konfigurálása](configure-ssl-bindings.md)
- [CDN hozzáadása](../cdn/cdn-add-to-web-app.md)
- [A kudu hely konfigurálása](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>Java-futtatókörnyezet beállításainak megadása

Ha a Tomcat és a Java SE környezetekben lévő lefoglalt memóriát vagy egyéb JVM-futtatókörnyezetet is meg szeretné határozni, hozzon létre egy nevű [alkalmazást](configure-common.md#configure-app-settings) `JAVA_OPTS` a következő beállításokkal:. App Service a Linux indításakor ezt a beállítást környezeti változóként adja át a Java futtatókörnyezetnek.

A Azure Portal a webalkalmazás **Alkalmazásbeállítások** területén hozzon létre egy nevű új alkalmazás-beállítást `JAVA_OPTS` , amely tartalmazza a további beállításokat, például a következőt: `-Xms512m -Xmx1204m` .

Az Alkalmazásbeállítások a Maven beépülő modulból történő konfigurálásához adja hozzá a beállítás/érték címkéket az Azure beépülő modul szakaszban. A következő példa egy meghatározott minimális és maximális Java-halom méretét állítja be:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

A App Service tervében egyetlen üzembe helyezési ponttal rendelkező fejlesztők a következő lehetőségeket használhatják:

- B1 és S1 példányok:`-Xms1024m -Xmx1024m`
- B2 és S2 példányok:`-Xms3072m -Xmx3072m`
- B3-as és S3-példányok:`-Xms6144m -Xmx6144m`

Az alkalmazás-halom beállításainak hangolásához tekintse át a App Service terv részleteit, és vegye figyelembe, hogy a memória optimális elosztása érdekében több alkalmazásra és üzembe helyezési pontra van szükség.

Ha JAR-alkalmazást helyez üzembe, akkor az *app. jar* névvel kell rendelkeznie ahhoz, hogy a beépített lemezkép helyesen tudja azonosítani az alkalmazást. (A Maven beépülő modul automatikusan átnevezi ezt.) Ha nem kívánja átnevezni a JAR-t az *app. jar*névre, feltölthet egy rendszerhéj-parancsfájlt a PARANCCSAL a jar futtatásához. Ezután illessze be a parancsfájl teljes elérési útját a portál konfigurációs szakaszának [Indítási fájl](faq-app-service-linux.md#built-in-images) szövegmezőjébe. Az indítási szkript nem abban a könyvtárban fut, amelyben el van helyezve. Ezért mindig abszolút elérési utakat használjon az indítási szkriptben található fájlokra való hivatkozáskor (például: `java -jar /home/myapp/myapp.jar`).

### <a name="turn-on-web-sockets"></a>Webes szoftvercsatornák bekapcsolása

A WebSockets támogatásának bekapcsolása a Azure Portal az alkalmazás **alkalmazás-beállításaiban** . A beállítás érvénybe léptetéséhez újra kell indítania az alkalmazást.

Kapcsolja be a web socket-támogatást az Azure CLI használatával a következő paranccsal:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --web-sockets-enabled true
```

Ezután indítsa újra az alkalmazást:

```azurecli-interactive
az webapp stop --name <app-name> --resource-group <resource-group-name>
az webapp start --name <app-name> --resource-group <resource-group-name>
```

### <a name="set-default-character-encoding"></a>Alapértelmezett karakterkódolás beállítása

A Azure Portal a webalkalmazás **Alkalmazásbeállítások** területén hozzon létre egy új, értékkel ellátott alkalmazás-beállítást `JAVA_OPTS` `-Dfile.encoding=UTF-8` .

Azt is megteheti, hogy az App Service Maven beépülő modullal konfigurálhatja az alkalmazás beállításait. Adja hozzá a beállítás neve és értéke címkéket a beépülő modul konfigurációjában:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

### <a name="adjust-startup-timeout"></a>Indítási Időtúllépés beállítása

Ha a Java-alkalmazása különösen nagy, növelje az indítási időkorlátot. Ehhez hozzon létre egy alkalmazás-beállítást, `WEBSITES_CONTAINER_START_TIME_LIMIT` és állítsa be azt a másodpercek számát, ameddig app Service várnia kell az időtúllépés előtt. A maximális érték `1800` másodperc.

### <a name="pre-compile-jsp-files"></a>JSP-fájlok előre fordítása

A Tomcat-alkalmazások teljesítményének növelése érdekében lefordíthatja a JSP-fájljait, mielőtt telepítené a App Service. Használhatja az Apache parittya által biztosított [Maven beépülő modult](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) , vagy használja ezt az [Ant-Build fájlt](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation).

## <a name="secure-applications"></a>Biztonságos alkalmazások

A Linux App Service-on futó Java-alkalmazásokhoz ugyanaz a [biztonsági eljárások](/azure/security/security-paas-applications-using-app-services) tartoznak, mint más alkalmazások.

### <a name="authenticate-users-easy-auth"></a>Felhasználók hitelesítése (egyszerű hitelesítés)

Az alkalmazás hitelesítésének beállítása a Azure Portal a **hitelesítés és engedélyezés** lehetőséggel. Itt engedélyezheti a hitelesítést Azure Active Directory vagy közösségi bejelentkezéssel, például a Facebook, a Google vagy a GitHub használatával. Azure Portal konfiguráció csak egyetlen hitelesítési szolgáltató konfigurálásakor működik. További információ: [a app Service alkalmazás konfigurálása Azure Active Directory bejelentkezési adatok használatára](configure-authentication-provider-aad.md) és a kapcsolódó cikkek más identitás-szolgáltatóknak való használatára. Ha több bejelentkezési szolgáltatót is engedélyeznie kell, kövesse az [app Service-hitelesítés testreszabása](app-service-authentication-how-to.md) című cikk utasításait.

#### <a name="tomcat"></a>Tomcat

A Tomcat-alkalmazás közvetlenül a servletből férhet hozzá a felhasználó jogcímeihez, ha a fő objektumot egy Térkép objektummá helyezi. A Térkép objektum az egyes jogcím-típusokat az adott típusú jogcímek gyűjteményéhez rendeli. Az alábbi kódban a `request` egy példánya `HttpServletRequest` .

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

Most már megvizsgálhatja az `Map` objektumot egy adott jogcím esetében is. A következő kódrészlet például megismétli az összes jogcím-típust, és kinyomtatja az egyes gyűjtemények tartalmát.

```java
for (Object key : map.keySet()) {
        Object value = map.get(key);
        if (value != null && value instanceof Collection {
            Collection claims = (Collection) value;
            for (Object claim : claims) {
                System.out.println(claims);
            }
        }
    }
```

A felhasználók kijelentkezéséhez használja az `/.auth/ext/logout` elérési utat. További műveletek elvégzéséhez tekintse meg [app Service hitelesítési és engedélyezési használati](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to)dokumentációját. A Tomcat [HttpServletRequest felületén](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) és annak módszerein is hivatalos dokumentáció található. A következő servlet metódusok is hidratálva vannak a App Service konfigurációja alapján:

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

A funkció letiltásához hozzon létre egy nevű Alkalmazásbeállítás `WEBSITE_AUTH_SKIP_PRINCIPAL` értéket `1` . A App Service által hozzáadott összes servlet-szűrő letiltásához hozzon létre egy nevű beállítást a következő `WEBSITE_SKIP_FILTERS` értékkel: `1` .

#### <a name="spring-boot"></a>Spring Boot

A Spring boot-fejlesztők a [Azure Active Directory Spring boot Starter](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) használatával biztosíthatják az alkalmazásokat az ismerős rugós biztonsági megjegyzésekkel és API-kkal. Ügyeljen arra, hogy növelje a fejléc maximális méretét az *Application. properties* fájlban. Javasoljuk, hogy a értékét `16384` .

### <a name="configure-tlsssl"></a>A TLS/SSL konfigurálása

A meglévő SSL-tanúsítvány feltöltéséhez és az alkalmazás tartománynevéhez való kötéséhez kövesse az [Egyéni DNS-név biztonságossá tétele SSL-kötéssel Azure app Serviceban](configure-ssl-bindings.md) található utasításokat. Alapértelmezés szerint az alkalmazás továbbra is engedélyezi a HTTP-kapcsolatokat – az oktatóanyag adott lépéseinek végrehajtásával kényszerítheti az SSL és a TLS használatát.

### <a name="use-keyvault-references"></a>Kulcstartó-hivatkozások használata

Az [Azure](../key-vault/general/overview.md) kulcstartó központosított titkos felügyeletet biztosít a hozzáférési házirendekkel és a naplózási előzményekkel. A kulcsok (például jelszavak vagy kapcsolati karakterláncok) a kulcstartóban tárolhatók, és a titkos kulcsokat az alkalmazásban környezeti változókon keresztül érhetik el.

Először is kövesse az alkalmazáshoz [való hozzáférés megadására](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) vonatkozó utasításokat, és a [titkos kulcshoz tartozó kulcstároló-hivatkozást egy Alkalmazásbeállítások alapján](app-service-key-vault-references.md#reference-syntax)végezze el a Key Vault. Ellenőrizheti, hogy a hivatkozás feloldja-e a titkos kulcsot a környezeti változó kinyomtatásával, miközben távolról fér hozzá a App Service terminálhoz.

Ha ezeket a titkokat be szeretné szúrni a Spring vagy a Tomcat konfigurációs fájljába, használja a környezeti változók befecskendezésének szintaxisát ( `${MY_ENV_VAR}` ). A Spring konfigurációs fájlok esetében tekintse meg ezt a dokumentációt a [külső konfigurációkról](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).

### <a name="using-the-java-key-store"></a>A Java Key Store használata

Alapértelmezés szerint a rendszer a [app Service Linuxra feltöltött](configure-ssl-certificate.md) nyilvános vagy privát tanúsítványokat a tároló indításakor betölti a megfelelő Java-kulcs-tárolókban. A tanúsítvány feltöltése után újra kell indítania a App Service, hogy betöltse a Java Key Store-ba. A nyilvános tanúsítványok be vannak töltve a Kulcstárolóba `$JAVA_HOME/jre/lib/security/cacerts` , és a privát tanúsítványok a következő helyen tárolódnak: `$JAVA_HOME/lib/security/client.jks` .

További konfigurálásra lehet szükség a JDBC-kapcsolatok a Java Key Store-ban található tanúsítványokkal való titkosításához. Tekintse meg a kiválasztott JDBC-illesztőprogram dokumentációját.

- [PostgreSQL](https://jdbc.postgresql.org/documentation/head/ssl-client.html)
- [SQL Server](https://docs.microsoft.com/sql/connect/jdbc/connecting-with-ssl-encryption?view=sql-server-ver15)
- [MySQL](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-reference-using-ssl.html)
- [MongoDB](https://mongodb.github.io/mongo-java-driver/3.4/driver/tutorials/ssl/)
- [Cassandra](https://docs.datastax.com/en/developer/java-driver/4.3/)

#### <a name="initializing-the-java-key-store"></a>A Java-kulcs tárolójának inicializálása

Az objektum inicializálásához `import java.security.KeyStore` töltse be a-tároló fájlját a jelszóval. A Key Stores alapértelmezett jelszava a "changeit".

```java
KeyStore keyStore = KeyStore.getInstance("jks");
keyStore.load(
    new FileInputStream(System.getenv("JAVA_HOME")+"/lib/security/cacets"),
    "changeit".toCharArray());

KeyStore keyStore = KeyStore.getInstance("pkcs12");
keyStore.load(
    new FileInputStream(System.getenv("JAVA_HOME")+"/lib/security/client.jks"),
    "changeit".toCharArray());
```

#### <a name="manually-load-the-key-store"></a>A kulcstároló manuális betöltése

A tanúsítványokat manuálisan is betöltheti a Key Store-ba. Hozzon létre egy alkalmazás-beállítást, `SKIP_JAVA_KEYSTORE_LOAD` amelynek a értéke `1` letiltva app Service a tanúsítványok automatikus betöltése a kulcstárolóba. A App Service a Azure Portal keresztül feltöltött nyilvános tanúsítványokat a rendszer a következő helyen tárolja: `/var/ssl/certs/` . A privát tanúsítványok a alatt találhatók `/var/ssl/private/` .

A Java-kulcs eszköz interaktív vagy hibakereséséhez [Nyisson meg egy SSH-kapcsolatot](configure-linux-open-ssh-session.md) a app Service és futtassa a parancsot `keytool` . A parancsok listáját a [legfontosabb eszköz dokumentációjában](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) találja. A Webáruház API-val kapcsolatos további információkért tekintse meg [a hivatalos dokumentációt](https://docs.oracle.com/javase/8/docs/api/java/security/KeyStore.html).

## <a name="configure-apm-platforms"></a>APM-platformok konfigurálása

Ez a szakasz bemutatja, hogyan csatlakoztathatók a Linux rendszeren üzembe Azure App Service helyezett Java-alkalmazások a NewRelic és a AppDynamics Application Performance monitoring (APM) platformokkal.

### <a name="configure-new-relic"></a>Új ereklye konfigurálása

1. NewRelic-fiók létrehozása a [NewRelic.com](https://newrelic.com/signup) -ben
2. Töltse le a Java-ügynököt a NewRelic webhelyről, és a fájl neve hasonló lesz a *newrelic-java-x.x.x.ziphoz *.
3. A licenckulcs másolásához az ügynököt később kell konfigurálnia.
4. [SSH-t a app Service-példányba](configure-linux-open-ssh-session.md) , és hozzon létre egy új címtár- */Home/site/wwwroot/APM*.
5. Töltse fel a kicsomagolt NewRelic Java-ügynök fájljait egy könyvtárba a */Home/site/wwwroot/APM*alatt. Az ügynök fájljainak a */Home/site/wwwroot/APM/newrelic*-ben kell lenniük.
6. Módosítsa a YAML fájlt a */Home/site/wwwroot/APM/newrelic/newrelic.YML* címen, és cserélje le a helyőrző licenc értékét a saját licenckulcs használatára.
7. A Azure Portal tallózással keresse meg az alkalmazást App Service és hozzon létre egy új alkalmazás-beállítást.
    - Ha az alkalmazás **Java SE**-t használ, hozzon létre egy nevű környezeti változót `JAVA_OPTS` az értékkel `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .
    - Ha a **tomcat**-t használja, hozzon létre egy nevű környezeti változót `CATALINA_OPTS` az értékkel `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .

### <a name="configure-appdynamics"></a>AppDynamics konfigurálása

1. AppDynamics-fiók létrehozása a [AppDynamics.com](https://www.appdynamics.com/community/register/) -ben
2. Töltse le a Java-ügynököt a AppDynamics webhelyről, a fájl neve hasonló lesz *AppServerAgent-x.x.x.xxxxx.zip*
3. [SSH-t a app Service-példányba](configure-linux-open-ssh-session.md) , és hozzon létre egy új címtár- */Home/site/wwwroot/APM*.
4. Töltse fel a Java-ügynök fájljait egy könyvtárba a */Home/site/wwwroot/APM*alatt. Az ügynök fájljainak a */Home/site/wwwroot/APM/appdynamics*-ben kell lenniük.
5. A Azure Portal tallózással keresse meg az alkalmazást App Service és hozzon létre egy új alkalmazás-beállítást.
    - Ha **Java SE**-t használ, hozzon létre egy nevű környezeti változót, amelynek `JAVA_OPTS` értéke a `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` `<app-name>` app Service neve.
    - Ha a **tomcat**-t használja, hozzon létre egy nevű környezeti változót, amelynek `CATALINA_OPTS` értéke a `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` `<app-name>` app Service neve.

    > [!NOTE]
    > Ha már rendelkezik környezeti változóval a vagy a esetében `JAVA_OPTS` `CATALINA_OPTS` , az `-javaagent:/...` aktuális érték végéhez fűzze hozzá a kapcsolót.
    
## <a name="configure-jar-applications"></a>JAR-alkalmazások konfigurálása

### <a name="starting-jar-apps"></a>JAR-alkalmazások indítása

Alapértelmezés szerint a App Service a JAR-alkalmazást *app. jar*néven várja. Ha ezt a nevet adja, a rendszer automatikusan futtatja. A Maven-felhasználók esetében a JAR nevét a `<finalName>app</finalName>` `<build>` *pom.xml*szakaszának megfelelően állíthatja be. [Ugyanezt a Gradle is megteheti](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.bundling.Jar.html#org.gradle.api.tasks.bundling.Jar:archiveFileName) a tulajdonság beállításával `archiveFileName` .

Ha más nevet szeretne használni a JAR számára, meg kell adnia a JAR-fájlt végrehajtó [indítási parancsot](faq-app-service-linux.md#built-in-images) is. Például: `java -jar my-jar-app.jar`. Megadhatja az indítási parancs értékét a portálon, a konfiguráció > általános beállítások alatt, vagy egy nevű Alkalmazásbeállítás használatával `STARTUP_COMMAND` .

### <a name="server-port"></a>Kiszolgáló portja

App Service Linux a bejövő kérelmeket a 80-es portra irányítja, ezért az alkalmazásnak a 80-es porton is figyelnie kell. Ezt megteheti az alkalmazás konfigurációjában (például a Spring *alkalmazás. properties* fájljában) vagy az indítási parancsban (például: `java -jar spring-app.jar --server.port=80` ). Tekintse meg a következő dokumentációt a közös Java-keretrendszerekhez:

- [Spring Boot](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-properties-and-configuration.html#howto-use-short-command-line-arguments)
- [SparkJava](http://sparkjava.com/documentation#embedded-web-server)
- [Micronaut](https://docs.micronaut.io/latest/guide/index.html#runningSpecificPort)
- [Lejátszási keretrendszer](https://www.playframework.com/documentation/2.6.x/ConfiguringHttps#Configuring-HTTPS)
- [Vertx](https://vertx.io/docs/vertx-core/java/#_start_the_server_listening)
- [Quarkus](https://quarkus.io/guides/application-configuration-guide)

## <a name="data-sources"></a>Adatforrások

### <a name="tomcat"></a>Tomcat

Ezek az utasítások az összes adatbázis-kapcsolatra érvényesek. A helyőrzőket ki kell töltenie a kiválasztott adatbázis illesztőprogram-osztályának nevével és JAR-fájljával. A megadott tábla az osztályok neveivel és az illesztőprogramok letöltésével közös adatbázisokhoz.

| Adatbázis   | Illesztőprogram-osztály neve                             | JDBC-illesztőprogram                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Letöltés](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Letöltés](https://dev.mysql.com/downloads/connector/j/) (válassza a "platform független" lehetőséget) |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Letöltés](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#download)                                                           |

Ha a Tomcat-t a Java Database Connectivity (JDBC) vagy a Java perzisztencia API (közös parlamenti) használatára szeretné konfigurálni, először testre kell szabnia a `CATALINA_OPTS` tomcat által az indításkor beolvasott környezeti változót. Adja meg ezeket az értékeket a [app Service Maven beépülő modul](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md)alkalmazás-beállításán keresztül:

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Vagy állítsa be a környezeti változókat a Azure Portal **konfigurációs**  >  **alkalmazás beállításai** lapján.

Ezután állapítsa meg, hogy az adatforrásnak elérhetőnek kell lennie egy alkalmazáshoz vagy a Tomcat servletben futó összes alkalmazáshoz.

#### <a name="application-level-data-sources"></a>Alkalmazás szintű adatforrások

1. Hozzon létre egy *context.xml* fájlt a projekt *META-INF-* fájljában/könyvtárában. Ha nem létezik, hozza létre a *META-INF/* könyvtárat.

2. A *context.xmlban *adjon hozzá egy `Context` elemet, amely összekapcsolja az adatforrást egy JNDI-címnek. Cserélje le a `driverClassName` helyőrzőt az illesztőprogram osztályának nevére a fenti táblázatból.

    ```xml
    <Context>
        <Resource
            name="jdbc/dbconnection"
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}"
            password="${connURL}"
        />
    </Context>
    ```

3. Frissítse alkalmazása *web.xml* az alkalmazás adatforrásának használatára.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>Megosztott kiszolgálói szintű erőforrások

Megosztott, kiszolgálóoldali adatforrások hozzáadásához a Tomcat server.xml szerkesztésére lesz szükség. Először töltse fel az [indítási parancsfájlt](faq-app-service-linux.md#built-in-images) , és állítsa be a parancsfájl elérési útját a **konfigurációs**  >  **indítási parancsban**. Az indítási parancsfájlt az [FTP](deploy-ftp.md)használatával töltheti fel.

Az indítási parancsfájl [XSL-átalakítót](https://www.w3schools.com/xml/xsl_intro.asp) készít a server.xml fájlra, majd az eredményül kapott XML-fájlt kiírja a következőre: `/usr/local/tomcat/conf/server.xml` . Az indítási parancsfájlnak az apk használatával kell telepítenie a libxslt-t. Az XSL-fájl és az indítási parancsfájl FTP-n keresztül tölthető fel. Az alábbi példa egy indítási parancsfájlt mutat be.

```sh
# Install libxslt. Also copy the transform file to /home/tomcat/conf/
apk add --update libxslt

# Usage: xsltproc --output output.xml style.xsl input.xml
xsltproc --output /home/tomcat/conf/server.xml /home/tomcat/conf/transform.xsl /usr/local/tomcat/conf/server.xml
```

Alább található egy példa XSL-fájl. A példában szereplő XSL-fájl új összekötő csomópontot hoz létre a Tomcat server.xmlhoz.

```xml
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
  <xsl:output method="xml" indent="yes"/>

  <xsl:template match="@* | node()" name="Copy">
    <xsl:copy>
      <xsl:apply-templates select="@* | node()"/>
    </xsl:copy>
  </xsl:template>

  <xsl:template match="@* | node()" mode="insertConnector">
    <xsl:call-template name="Copy" />
  </xsl:template>

  <xsl:template match="comment()[not(../Connector[@scheme = 'https']) and
                                 contains(., '&lt;Connector') and
                                 (contains(., 'scheme=&quot;https&quot;') or
                                  contains(., &quot;scheme='https'&quot;))]">
    <xsl:value-of select="." disable-output-escaping="yes" />
  </xsl:template>

  <xsl:template match="Service[not(Connector[@scheme = 'https'] or
                                   comment()[contains(., '&lt;Connector') and
                                             (contains(., 'scheme=&quot;https&quot;') or
                                              contains(., &quot;scheme='https'&quot;))]
                                  )]
                      ">
    <xsl:copy>
      <xsl:apply-templates select="@* | node()" mode="insertConnector" />
    </xsl:copy>
  </xsl:template>

  <!-- Add the new connector after the last existing Connnector if there is one -->
  <xsl:template match="Connector[last()]" mode="insertConnector">
    <xsl:call-template name="Copy" />

    <xsl:call-template name="AddConnector" />
  </xsl:template>

  <!-- ... or before the first Engine if there is no existing Connector -->
  <xsl:template match="Engine[1][not(preceding-sibling::Connector)]"
                mode="insertConnector">
    <xsl:call-template name="AddConnector" />

    <xsl:call-template name="Copy" />
  </xsl:template>

  <xsl:template name="AddConnector">
    <!-- Add new line -->
    <xsl:text>&#xa;</xsl:text>
    <!-- This is the new connector -->
    <Connector port="8443" protocol="HTTP/1.1" SSLEnabled="true" 
               maxThreads="150" scheme="https" secure="true" 
               keystroreFile="${{user.home}}/.keystore" keystorePass="changeit"
               clientAuth="false" sslProtocol="TLS" />
  </xsl:template>
  
</xsl:stylesheet>
```

#### <a name="finalize-configuration"></a>Konfiguráció véglegesítése

Végül helyezze el az illesztőprogram-tégelyeket a Tomcat osztályútvonal, és indítsa újra a App Service.

1. Győződjön meg arról, hogy a */Home/tomcat/lib* könyvtárba HELYEZVE a JDBC-illesztőprogram fájljai elérhetők a Tomcat ClassLoader. (Ha még nem létezik, hozza létre ezt a könyvtárat.) Ha ezeket a fájlokat fel szeretné tölteni a App Service-példányba, hajtsa végre a következő lépéseket:

    1. A [Cloud Shell](https://shell.azure.com)telepítse a WebApp bővítményt:

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. A következő CLI-parancs futtatásával hozzon létre egy SSH-alagutat a helyi rendszerből a App Serviceához:

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. Kapcsolódjon a helyi bújtatási porthoz az SFTP-ügyféllel, és töltse fel a fájlokat a */Home/tomcat/lib* mappába.

    Azt is megteheti, hogy FTP-ügyfél használatával tölti fel a JDBC-illesztőt. Az [FTP-hitelesítő adatok beszerzéséhez kövesse az alábbi utasításokat](deploy-configure-credentials.md).

2. Ha létrehozta a kiszolgálói szintű adatforrást, indítsa újra a App Service Linux alkalmazást. A Tomcat alaphelyzetbe `CATALINA_BASE` áll, `/home/tomcat` és a frissített konfigurációt fogja használni.

### <a name="spring-boot"></a>Spring Boot

Ha a Spring boot-alkalmazásokban lévő adatforrásokhoz szeretne csatlakozni, javasoljuk, hogy hozzon létre kapcsolati karakterláncokat, és szúrja be őket az *Application. properties* fájlba.

1. A App Service lapon a konfiguráció szakaszban adja meg a karakterlánc nevét, illessze be a JDBC-kapcsolódási karakterláncot az érték mezőbe, és állítsa a típust "Custom" értékre. Ezt a kapcsolási karakterláncot tárolóhely-beállításként is megadhatja.

    Ez a kapcsolódási sztring elérhető az alkalmazáshoz egy nevű környezeti változóként `CUSTOMCONNSTR_<your-string-name>` . Például a fentiekben létrehozott kapcsolatok karakterlánc lesz elnevezve `CUSTOMCONNSTR_exampledb` .

2. Az *Application. properties* fájlban adja meg ezt a hivatkozási karakterláncot a környezeti változó nevével. Példánkban a következőt fogjuk használni.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

Erről a témakörről a [Spring boot dokumentációjában](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html) talál további információt az adathozzáférésről és a [külső konfigurációkról](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html) .

## <a name="use-redis-as-a-session-cache-with-tomcat"></a>A Redis használata munkamenet-gyorsítótárként a Tomcat használatával

A Tomcat úgy is beállítható, hogy külső munkamenet-tárolót használjon, például az [Azure cache-t a Redis](/azure/azure-cache-for-redis/). Így megőrizheti a felhasználói munkamenet állapotát (például a bevásárlókocsi adatait), amikor a felhasználó átkerül az alkalmazás egy másik példányára, például ha automatikus skálázás, újraindítás vagy feladatátvétel történik.

A Tomcat és a Redis használatához konfigurálnia kell az alkalmazást egy [PersistentManager](https://tomcat.apache.org/tomcat-8.5-doc/config/manager.html) -implementáció használatára. A következő lépések ismertetik ezt a folyamatot a [Pivotal Session Manager használatával: Redis-Store](https://github.com/pivotalsoftware/session-managers/tree/master/redis-store) példaként.

1. Nyisson meg egy bash-terminált, és használja a `<variable>=<value>` következő környezeti változók beállításához.

    | Változó                 | Érték                                                                      |
    |--------------------------|----------------------------------------------------------------------------|
    | RESOURCEGROUP_NAME       | Az App Service példányt tartalmazó erőforráscsoport neve.       |
    | WEBAPP_NAME              | Az App Service-példány neve.                                     |
    | WEBAPP_PLAN_NAME         | A App Service terv neve.                                         |
    | RÉGIÓ                   | Annak a régiónak a neve, ahol az alkalmazás üzemeltetve van.                           |
    | REDIS_CACHE_NAME         | A Redis-példány Azure-gyorsítótárának neve.                           |
    | REDIS_PORT               | A Redis cache által figyelt SSL-port.                             |
    | REDIS_PASSWORD           | A példány elsődleges hozzáférési kulcsa.                                  |
    | REDIS_SESSION_KEY_PREFIX | Az alkalmazásból érkező munkamenetkulcsok azonosítására megadott érték. |

    ```bash
    RESOURCEGROUP_NAME=<resource group>
    WEBAPP_NAME=<web app>
    WEBAPP_PLAN_NAME=<App Service plan>
    REGION=<region>
    REDIS_CACHE_NAME=<cache>
    REDIS_PORT=<port>
    REDIS_PASSWORD=<access key>
    REDIS_SESSION_KEY_PREFIX=<prefix>
    ```

    A Azure Portal nevét, portját és hozzáférési kulcsát a szolgáltatási példány **Tulajdonságok** vagy **hozzáférési kulcsok** szakaszában tekintheti meg.

2. Az alkalmazás *src/Main/WebApp/META-INF/context.xml* fájljának létrehozása vagy frissítése a következő tartalommal:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <Context path="">
        <!-- Specify Redis Store -->
        <Valve className="com.gopivotal.manager.SessionFlushValve" />
        <Manager className="org.apache.catalina.session.PersistentManager">
            <Store className="com.gopivotal.manager.redis.RedisStore"
                   connectionPoolSize="20"
                   host="${REDIS_CACHE_NAME}.redis.cache.windows.net"
                   port="${REDIS_PORT}"
                   password="${REDIS_PASSWORD}"
                   sessionKeyPrefix="${REDIS_SESSION_KEY_PREFIX}"
                   timeout="2000"
            />
        </Manager>
    </Context>
    ```

    Ez a fájl megadja és konfigurálja a munkamenet-kezelő megvalósítását az alkalmazáshoz. Az előző lépésben beállított környezeti változókat használja a fiókadatok megtartásához a forrásfájlok közül.

3. Az FTP használatával töltse fel a munkamenet-kezelő JAR-fájlját a App Service-példányba, és helyezze a */Home/tomcat/lib* könyvtárba. További információ: [az alkalmazás üzembe helyezése Azure app Service FTP/S használatával](https://docs.microsoft.com/azure/app-service/deploy-ftp).

4. Tiltsa le a [munkamenet-affinitás cookie](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) -t a app Service példányhoz. Ezt megteheti a Azure Portal az alkalmazásra való navigálással, majd a **konfiguráció > az általános beállítások > az ARR-affinitás** beállítás **kikapcsolásával**. Másik lehetőségként a következő parancsot használhatja:

    ```azurecli
    az webapp update -g <resource group> -n <webapp name> --client-affinity-enabled false
    ```

    Alapértelmezés szerint a App Service munkamenet-affinitási cookie-kat használ annak biztosítására, hogy a meglévő munkamenetekkel rendelkező ügyfélalkalmazások az alkalmazás ugyanazon példányára legyenek irányítva. Ez az alapértelmezett viselkedés nem igényel konfigurációt, de nem tudja megőrizni a felhasználói munkamenet állapotát az alkalmazás újraindításakor, vagy ha egy másik példányra irányítja át a forgalmat. Ha letiltja a munkamenet cookie-alapú útválasztásának kikapcsolásához a [meglévő ARR-példány affinitási](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) konfigurációját, a konfigurált munkamenet-tároló beavatkozás nélkül is működhet.

5. Keresse meg a App Service példány **Tulajdonságok** szakaszát, és keresse meg a **további kimenő IP-címeket**. Ezek az alkalmazás összes lehetséges kimenő IP-címét képviselik. Másolja ezeket a következő lépésben való használatra.

6. Az egyes IP-címekhez hozzon létre egy tűzfalszabályot az Azure cache-ben a Redis-példányhoz. Ezt a Redis-példány **tűzfal** szakaszának Azure Portal teheti meg. Adjon egyedi nevet az egyes szabályoknak, és állítsa be a **kezdő IP-cím** és a **záró IP-cím** értékeket ugyanahhoz az IP-címhez.

7. Nyissa meg a **Redis-példány** **Speciális beállítások** szakaszát, és állítsa a **hozzáférés engedélyezése csak SSL-kapcsolaton keresztül** beállítást. Ez lehetővé teszi, hogy az App Service-példány kommunikáljon a Redis cache-vel az Azure-infrastruktúrán keresztül.

8. Frissítse az `azure-webapp-maven-plugin` alkalmazás *pom.xml* fájljának konfigurációját, hogy a Redis-fiók adataira hivatkozzon. Ez a fájl a korábban beállított környezeti változókat használja, hogy a fiókadatok a forrásfájlok adatain kívül maradjanak.

    Szükség esetén frissítse a [Maven Plugin for Azure App Service](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme)`1.9.1`-ás verzióját a jelenlegire.

    ```xml
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.9.1</version>
        <configuration>            
            <!-- Web App information -->
            <schemaVersion>v2</schemaVersion>
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appServicePlanName>${WEBAPP_PLAN_NAME}-${REGION}</appServicePlanName>
            <appName>${WEBAPP_NAME}-${REGION}</appName>
            <region>${REGION}</region>            
            <runtime>
                <os>linux</os>
                <javaVersion>jre8</javaVersion>
                <webContainer>tomcat 9.0</webContainer>
            </runtime>

            <appSettings>
                <property>
                    <name>REDIS_CACHE_NAME</name>
                    <value>${REDIS_CACHE_NAME}</value>
                </property>
                <property>
                    <name>REDIS_PORT</name>
                    <value>${REDIS_PORT}</value>
                </property>
                <property>
                    <name>REDIS_PASSWORD</name>
                    <value>${REDIS_PASSWORD}</value>
                </property>
                <property>
                    <name>REDIS_SESSION_KEY_PREFIX</name>
                    <value>${REDIS_SESSION_KEY_PREFIX}</value>
                </property>
                <property>
                    <name>JAVA_OPTS</name>
                    <value>-Xms2048m -Xmx2048m -DREDIS_CACHE_NAME=${REDIS_CACHE_NAME} -DREDIS_PORT=${REDIS_PORT} -DREDIS_PASSWORD=${REDIS_PASSWORD} IS_SESSION_KEY_PREFIX=${REDIS_SESSION_KEY_PREFIX}</value>
                </property>

            </appSettings>

        </configuration>
    </plugin>
    ```

9. Építse újra és telepítse újra az alkalmazást.

    ```bash
    mvn package -DskipTests azure-webapp:deploy
    ```

Az alkalmazás mostantól a Redis cache-t fogja használni a munkamenet-kezeléshez.

Az utasítások tesztelésére használható minta: [Méretezés – állapot-nyilvántartó – Java-Web-App-on-Azure](https://github.com/Azure-Samples/scaling-stateful-java-web-app-on-azure) -tárház a githubon.

## <a name="docker-containers"></a>Docker-tárolók

Az Azure által támogatott Zulu a tárolókban való használatához győződjön meg arról, hogy az Azure-beli [támogatott Azul Zulu Enterprise for Azure letöltési oldalának](https://www.azul.com/downloads/azure-only/zulu/) megfelelően lekéri az előre elkészített rendszerképeket, vagy használja a `Dockerfile` [Microsoft Java GitHub](https://github.com/Microsoft/java/tree/master/docker)-tárház példáit.

## <a name="statement-of-support"></a>Támogatási nyilatkozat

### <a name="runtime-availability"></a>Futtatókörnyezet rendelkezésre állása

A Linux rendszerhez készült App Service két futtatókörnyezetet támogat a Java-webalkalmazások felügyelt üzemeltetéséhez:

- A [tomcat servlet tároló](https://tomcat.apache.org/) a Web Archive-(War-) fájlokként csomagolt alkalmazások futtatásához. A támogatott verziók a következők: 8,5 és 9,0.
- Java SE futtatókörnyezeti környezet Java Archive (JAR) fájlokként csomagolt alkalmazások futtatásához. A támogatott verziók a következők: Java 8 és 11.

### <a name="jdk-versions-and-maintenance"></a>JDK-verziók és-karbantartás

A Azul Zulu Enterprise-OpenJDK az Azure-hoz készült OpenJDK, valamint a Microsoft és a Azul rendszerek által támogatott, az Azure-ra vonatkozó, és a Azure Stack. A Java SE-alkalmazások létrehozásához és futtatásához szükséges összes összetevőt tartalmazzák. A JDK-t a [Java jdk-telepítésből](https://aka.ms/azure-jdks)telepítheti.

A támogatott JDK minden év januárjában, áprilisban, júliusban és októberben automatikusan megtörténik.

### <a name="security-updates"></a>Biztonsági frissítések

A főbb biztonsági rések javításait és javításait a rendszer azonnal felszabadítja, amint azok elérhetők lesznek a Azul Systems-től. A "fő" biztonsági rést az 9,0-es vagy újabb alappontszám határozza meg a [NIST Common sebezhetőségi pontozási rendszer 2. verziójában](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Elavulás és nyugdíjazás

Ha egy támogatott Java-futtatókörnyezet megszűnik, az érintett futtatókörnyezetet használó Azure-fejlesztők elavult értesítést kapnak a futtatókörnyezet kivonása előtt legalább hat hónappal.

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>További lépések

Látogasson el az Azure [for Java Developers](/java/azure/) Center webhelyre, ahol megtalálhatja az Azure rövid útmutatók, oktatóanyagok és a Java-dokumentációt.

A Linux rendszerhez készült App Service használatának általános kérdései a Java [app Service](faq-app-service-linux.md)-fejlesztésre nem vonatkoznak.

::: zone-end
