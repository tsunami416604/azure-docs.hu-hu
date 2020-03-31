---
title: Windows Java-alkalmazások konfigurálása
description: Megtudhatja, hogyan konfigurálhatja a Java-alkalmazásokat a Windows virtuálisgép-példányok azure App Service-ben való futtatására. Ez a cikk a leggyakoribb konfigurációs feladatokat mutatja be.
keywords: Azure app szolgáltatás, web app, windows, oss, java
author: jasonfreeberg
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18
ms.openlocfilehash: 2b21061e8a939b91c637ef05bbe6375c0b3f82e8
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2020
ms.locfileid: "80383973"
---
# <a name="configure-a-windows-java-app-for-azure-app-service"></a>Windows Java-alkalmazás konfigurálása az Azure App Service szolgáltatáshoz

Az Azure App Service lehetővé teszi a Java-fejlesztők számára, hogy tomcat webalkalmazásaikat egy teljes körűen felügyelt Windows-alapú szolgáltatáson gyorsan létrehessék, telepíthessék és méretezhessék. Alkalmazásokat telepíthet a Maven beépülő modullal a parancssorból vagy olyan szerkesztőkben, mint az IntelliJ, az Eclipse vagy a Visual Studio Code.

Ez az útmutató az App Service-ben használó Java-fejlesztők számára tartalmaz kulcsfontosságú fogalmakat és utasításokat. Ha még soha nem használta az Azure App Service- t, először olvassa el a [Java gyorsútmutatót.](app-service-web-get-started-java.md) Az App Service nem a Java-fejlesztésre vonatkozó használatával kapcsolatos általános kérdésekre az [App Service Windows gyakori kérdések](faq-configuration-and-management.md)című kérdések ben ad választ.

## <a name="deploying-your-app"></a>Az alkalmazás telepítése

Használhatja [az Azure Web App Plugin for Maven](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) a .war fájlok üzembe helyezéséhez. A népszerű azonosítókkal való telepítést az [Azure Toolkit for IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij) vagy az [Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse)is támogatja.

Ellenkező esetben a telepítési módszer az archívum típusától függ:

- A .war fájlok Tomcat-ra `/api/wardeploy/` való telepítéséhez használja a végpontot az archív fájl postához. Az API-val kapcsolatos további információkért tekintse meg [ezt a dokumentációt.](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file)
- A .jar fájlok Java SE-re való telepítéséhez használja a `/api/zipdeploy/` Kudu hely végpontját. Az API-val kapcsolatos további információkért tekintse meg [ezt a dokumentációt.](https://docs.microsoft.com/azure/app-service/deploy-zip#rest)

Ne telepítse a .war FTP használatával. Az FTP-eszköz indítási parancsfájlok, függőségek vagy más futásidejű fájlok feltöltésére szolgál. Nem ez az optimális választás a webalkalmazások üzembe helyezéséhez.

## <a name="logging-and-debugging-apps"></a>Alkalmazások naplózása és hibakeresése

Teljesítményjelentések, forgalmi vizualizációk és állapotalapú szűrések érhetők el az egyes alkalmazásokhoz az Azure Portalon keresztül. További információ: [Azure App Service diagnostics overview](overview-diagnostics.md).

### <a name="use-flight-recorder"></a>A Fedélzeti rögzítő használata

Az App Service-ben az Azul JVM-eket használó összes Java-futtatási idő a Zulu flight recordervel érkezik. Ezzel rögzítheti a JVM- és rendszer- és Java-szintű eseményeket a Java-alkalmazások viselkedésének figyeléséhez és a problémák elhárításához.

Ahhoz, hogy egy időzített felvétel lesz szükség a PID (Process ID) a Java alkalmazás. A PID megkereséséhez nyisson meg egy böngészőt a webalkalmazás SCM-webhelyére a https://<webhelyneve>.scm.azurewebsites.net/ProcessExplorer/ címen. Ez a lap a webalkalmazás futó folyamatait jeleníti meg. Keresse meg a "java" nevű folyamatot a táblázatban, és másolja a megfelelő PID-t (process ID).

Ezután nyissa meg a **Hibakeresési konzolt** az SCM-webhely felső eszköztárán, és futtassa a következő parancsot. Cserélje `<pid>` le a korábban másolt folyamatazonosítóval. Ez a parancs elindítja a Java-alkalmazás 30 másodperces `timed_recording_example.jfr` profilkészítői felvételét, és létrehoz egy, a `D:\home` címtárban megnevezett fájlt.

```
jcmd <pid> JFR.start name=TimedRecording settings=profile duration=30s filename="D:\home\timed_recording_example.JFR"
```

További információkért tekintse meg a [Jcmd parancs referencia](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190).

#### <a name="analyze-jfr-files"></a>Fájlok `.jfr` elemzése

Az [FTPS](deploy-ftp.md) segítségével töltse le a JFR fájlt a helyi számítógépre. A JFR fájl elemzéséhez töltse le és telepítse a [Zulu Mission Control alkalmazást.](https://www.azul.com/products/zulu-mission-control/) A Zulu Mission Control ról az [Azul dokumentációjában](https://docs.azul.com/zmc/) és a [telepítési útmutatóban](https://docs.microsoft.com/java/azure/jdk/java-jdk-flight-recorder-and-mission-control)olvashat.

### <a name="stream-diagnostic-logs"></a>Diagnosztikai naplók streamelése

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

További információ: [Stream logs in Cloud Shell](troubleshoot-diagnostic-logs.md#in-cloud-shell).

### <a name="app-logging"></a>Alkalmazásnaplózás

Engedélyezze az [alkalmazások naplózását](troubleshoot-diagnostic-logs.md#enable-application-logging-windows) az Azure Portalon vagy az [Azure CLI-n](/cli/azure/webapp/log#az-webapp-log-config) keresztül, hogy konfigurálja az App Service-t az alkalmazás szabványos konzolkimenetének és szabványos konzolhiba-streamjeinek írásához a helyi fájlrendszerbe vagy az Azure Blob Storage-ba. A helyi App Service fájlrendszer-példányra való naplózás a konfigurálás után 12 órával le van tiltva. Ha hosszabb megőrzésre van szüksége, konfigurálja az alkalmazást, hogy a kimenet et egy Blob storage-tárolóba írja. A Java és tomcat alkalmazásnaplók a */LogFiles/Application/* könyvtárban találhatók.

Ha az alkalmazás [logback](https://logback.qos.ch/) vagy [Log4j-t](https://logging.apache.org/log4j) használ a nyomkövetéshez, továbbíthatja ezeket a nyomkövetéseket az Azure Application Insightsban a Naplózási keretrendszer konfigurációs utasításainak használatával az [Application Insights Java-nyomkövetési naplóinak felfedezése](/azure/application-insights/app-insights-java-trace-logs)című részben.


## <a name="customization-and-tuning"></a>Testreszabás és hangolás

Az Azure App Service támogatja a dobozon kívüli finomhangolást és testreszabást az Azure Portalon és a CLI-n keresztül. Tekintse át az alábbi cikkeket a nem Java-specifikus webalkalmazás-konfigurációról:

- [Alkalmazásbeállítások konfigurálása](configure-common.md#configure-app-settings)
- [Egyéni tartomány beállítása](app-service-web-tutorial-custom-domain.md)
- [SSL-kötések konfigurálása](configure-ssl-bindings.md)
- [CDN hozzáadása](../cdn/cdn-add-to-web-app.md)
- [A Kudu webhely konfigurálása](https://github.com/projectkudu/kudu/wiki/Configurable-settings)

### <a name="set-java-runtime-options"></a>Java futásidejű beállítások megadása

A lefoglalt memória vagy más JVM-futásidejű beállítások `JAVA_OPTS` beállításához hozzon létre egy a beállításokkal elnevezett [alkalmazásbeállítást.](configure-common.md#configure-app-settings) Az App Service ezt a beállítást környezeti változóként továbbítja a Java-futásórának, amikor elindul.

Az Azure Portalon a webalkalmazás **alkalmazásbeállításai** csoportban hozzon létre egy új alkalmazásbeállítást, `JAVA_OPTS` amely tartalmazza a további beállításokat, például `-Xms512m -Xmx1204m`a.

Az alkalmazás beállításának konfigurálásához a Maven beépülő modulból adja hozzá a beállítási/értékcímkéket az Azure plugin szakaszban. A következő példa egy adott minimális és maximális Java halomméretet állít be:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

Az alkalmazásszolgáltatási csomagban egyetlen üzembe helyezési bővítőhellyel rendelkező alkalmazást futtató fejlesztők a következő lehetőségeket használhatják:

- B1 és S1 példányok:`-Xms1024m -Xmx1024m`
- B2 és S2 példányok:`-Xms3072m -Xmx3072m`
- B3 és S3 példányok:`-Xms6144m -Xmx6144m`

Az alkalmazáshalom-beállítások finomhangolásakor tekintse át az App Service-csomag részleteit, és vegye figyelembe, hogy több alkalmazás nak és üzembe helyezési helynek kell megtalálnia a memória optimális lefoglalását.

### <a name="turn-on-web-sockets"></a>Webes szoftvercsatornák bekapcsolása

Kapcsolja be a webes szoftvercsatornák támogatását az Azure Portalon az **alkalmazás beállításaiban.** A beállítás érvénybe léptetéséhez újra kell indítania az alkalmazást.

Kapcsolja be a webes szoftvercsatorna-támogatást az Azure CLI használatával a következő paranccsal:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --web-sockets-enabled true
```

Ezután indítsa újra az alkalmazást:

```azurecli-interactive
az webapp stop --name <app-name> --resource-group <resource-group-name>
az webapp start --name <app-name> --resource-group <resource-group-name>
```

### <a name="set-default-character-encoding"></a>Alapértelmezett karakterkódolás beállítása

Az Azure Portalon a webalkalmazás **alkalmazásbeállításai** csoportban hozzon `-Dfile.encoding=UTF-8`létre egy új alkalmazásbeállítást, amelynek neve értékkel van elnevezve. `JAVA_OPTS`

Azt is megteheti, hogy konfigurálja az alkalmazás beállítást az App Service Maven beépülő modul használatával. Adja hozzá a beállítás nevét és értékcímkéket a plugin konfiguráció:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

### <a name="pre-compile-jsp-files"></a>JSP-fájlok előfordítása

A Tomcat-alkalmazások teljesítményének javítása érdekében az App Service-be való telepítés előtt lefordíthatja a JSP-fájlokat. Használhatja a [Maven plugin](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) által biztosított Apache Sling, vagy használja ezt [a Hangya build fájlt](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation).

## <a name="secure-applications"></a>Biztonságos alkalmazások

Az App Service-ben futó Java-alkalmazások ugyanazokat a [biztonsági gyakorlati tanácsokat,](/azure/security/security-paas-applications-using-app-services) mint más alkalmazások.

### <a name="authenticate-users-easy-auth"></a>Felhasználók hitelesítése (Egyszerű hitelesítés)

Állítsa be az alkalmazáshitelesítést az Azure Portalon a **hitelesítési és engedélyezési** beállítással. Innen engedélyezheti a hitelesítést az Azure Active Directory vagy a közösségi bejelentkezések, például a Facebook, a Google vagy a GitHub használatával. Az Azure Portal konfigurációja csak egyetlen hitelesítésszolgáltató konfigurálásakor működik. További információ: [Az App Service-alkalmazás konfigurálása az Azure Active Directory bejelentkezési](configure-authentication-provider-aad.md) és a kapcsolódó cikkek más identitásszolgáltatók. Ha több bejelentkezési szolgáltatót kell engedélyeznie, kövesse az [App Service hitelesítési](app-service-authentication-how-to.md) cikkének testreszabása című cikk utasításait.

#### <a name="tomcat"></a>Tomcat

A Tomcat alkalmazás közvetlenül a servletből érheti el a felhasználó jogcímeket úgy, hogy a Fő objektumot egy Map objektumba irányítja. A Map objektum minden jogcímtípust leképez az adott típus jogcímeinek gyűjteményéhez. Az alábbi kódban `request` található `HttpServletRequest`a példánya.

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

Most már megvizsgálhatja az `Map` objektumot bármely konkrét jogcímért. Például a következő kódrészlet végighalad az összes jogcímtípuson, és kinyomtatja az egyes gyűjtemények tartalmát.

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

A felhasználók kijelentkeztetéséhez használja az `/.auth/ext/logout` elérési utat. További műveletek végrehajtásához olvassa el az [App Service hitelesítési és engedélyezési használatának dokumentációját.](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to) Van is hivatalos dokumentációt a Tomcat [HttpServletRequest felület](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) és annak módszereit. A következő servlet módszerek is hidratáltak az App Service konfigurációja alapján:

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

A szolgáltatás letiltásához hozzon `WEBSITE_AUTH_SKIP_PRINCIPAL` létre egy `1`alkalmazásbeállítást, amelynek értéke . Az App Service által hozzáadott összes servlet `WEBSITE_SKIP_FILTERS` szűrő letiltásához hozzon létre egy `1`.

### <a name="configure-tlsssl"></a>TLS/SSL konfigurálása

Kövesse a [Biztonságos egyéni DNS-név az Azure App Service-ben egy SSL-kötéssel](configure-ssl-bindings.md) című útmutatóban egy meglévő SSL-tanúsítvány feltöltéséhez és az alkalmazás tartománynevéhez kötéséhez. Alapértelmezés szerint az alkalmazás továbbra is engedélyezi a HTTP-kapcsolatokat az oktatóanyag ban az SSL és a TLS kényszerítéséhez szükséges konkrét lépések végrehajtásához.

### <a name="use-keyvault-references"></a>KeyVault-hivatkozások használata

[Az Azure KeyVault](../key-vault/key-vault-overview.md) központi titkos kezelést biztosít hozzáférési szabályzatokkal és naplózási előzményekkel. A keyvaultban tárolhatja a titkos kulcsokat (például a jelszavakat vagy a kapcsolati karakterláncokat), és környezeti változókon keresztül hozzáférhet ezekhez a titkos kulcsokhoz az alkalmazásban.

Először kövesse az [alkalmazás key vault-hozzáférésének megadására](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) vonatkozó utasításokat, és [az alkalmazásbeállításban a titkos kulcsra való hivatkozást.](app-service-key-vault-references.md#reference-syntax) Ellenőrizheti, hogy a hivatkozás feloldódik-e a titkos kulcsot a környezeti változó távoli nyomtatásával, miközben távolról éri el az App Service-terminált.

Ha ezeket a titkokat tavaszi vagy Tomcat konfigurációs`${MY_ENV_VAR}`fájlba szeretné beadni, használja a környezeti változóinjektálásszintaxisát ( ). A tavaszi konfigurációs fájlokat lásd a [külső konfigurációkdokumentációjában.](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)


## <a name="configure-apm-platforms"></a>APM-platformok konfigurálása

Ez a szakasz bemutatja, hogyan kapcsolhatja össze a Linuxon az Azure App Service szolgáltatásban telepített Java-alkalmazásokat a NewRelic és az AppDynamics alkalmazásteljesítmény-figyelési (APM) platformokkal.

### <a name="configure-new-relic"></a>Új ereklye konfigurálása

1. Új relic fiók létrehozása [NewRelic.com](https://newrelic.com/signup)
2. Töltse le a Java ügynök NewRelic, akkor lesz egy fájl neve hasonló *newrelic-java-x.x.zip*.
3. Másolja a licenckulcsot, szüksége lesz rá az ügynök későbbi konfigurálásához.
4. A [Kudu konzolsegítségével](https://github.com/projectkudu/kudu/wiki/Kudu-console) hozzon létre egy új könyvtárat */home/site/wwwroot/apm*.
5. Töltse fel a kicsomagolt Új Relic Java ügynök fájlokat egy könyvtárba a */home/site/wwwroot/apm*alatt. Az ügynök fájljainak a */home/site/wwwroot/apm/newrelic*könyvtárban kell lenniük.
6. Módosítsa a YAML-fájlt a */home/site/wwwroot/apm/newrelic/newrelic.yml oldalon,* és cserélje le a helyőrző licencértékét a saját licenckulcsára.
7. Az Azure Portalon keresse meg az alkalmazást az App Service-ben, és hozzon létre egy új alkalmazásbeállítást.
    - Ha az alkalmazás **Java SE-t**használ, hozzon létre egy a. `JAVA_OPTS` `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`
    - Ha **A Tomcat-ot**használja, hozzon létre egy környezeti változót, amelynek neve `CATALINA_OPTS` a . `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`

### <a name="configure-appdynamics"></a>Az AppDynamics konfigurálása

1. AppDynamics-fiók létrehozása [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Töltse le a Java-ügynököt az AppDynamics webhelyéről, a fájlnév hasonló lesz az *AppServerAgent-x.x.xxxxx.zip fájlhoz.*
3. A [Kudu konzolsegítségével](https://github.com/projectkudu/kudu/wiki/Kudu-console) hozzon létre egy új könyvtárat */home/site/wwwroot/apm*.
4. Töltse fel a Java ügynök fájlokat egy könyvtárba a */home/site/wwwroot/apm alatt.* Az ügynök fájljainak a */home/site/wwwroot/apm/appdynamics*könyvtárban kell lenniük.
5. Az Azure Portalon keresse meg az alkalmazást az App Service-ben, és hozzon létre egy új alkalmazásbeállítást.
    - Java **SE**használata esetén hozzon létre `JAVA_OPTS` egy környezeti `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` `<app-name>` változó nevű, az alkalmazásszolgáltatás neve.
    - Ha **a Tomcat**használatával, hozzon `CATALINA_OPTS` létre egy `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` `<app-name>` környezeti változó nevű az érték, ahol az App Service neve.

>  Ha már rendelkezik környezeti `JAVA_OPTS` `CATALINA_OPTS`változóval a `-javaagent:/...` vagyhoz, fűzze hozzá a beállítást az aktuális érték végéhez.

## <a name="data-sources"></a>Adatforrások

### <a name="tomcat"></a>Tomcat

Ezek az utasítások minden adatbázis-kapcsolatra vonatkoznak. A helyőrzőket ki kell töltenie a kiválasztott adatbázis illesztőprogram-osztálynevével és JAR fájljával. A feltéve, hogy egy táblázat osztályneveket és illesztőprogram-letöltéseket a közös adatbázisok.

| Adatbázis   | Illesztőprogram-osztály neve                             | JDBC illesztőprogram                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Letöltés](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Letöltés](https://dev.mysql.com/downloads/connector/j/) (Válassza a "Platform Independent" lehetőséget) |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Letöltés](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#download)                                                           |

Ha a Tomcat-ot a Java Database Connectivity (JDBC) vagy a `CATALINA_OPTS` Java Persistence API (JPA) használatára szeretné beállítani, először testre szabhatja a Tomcat által az indításkor beolvasott környezeti változót. Állítsa be ezeket az értékeket egy alkalmazásbeállítással az [App Service Maven beépülő modulban:](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md)

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Vagy állítsa be a környezeti változókat az Azure Portal **konfigurációs** > **alkalmazásbeállításai** lapján.

Ezután határozza meg, hogy az adatforrás elérhető legyen-e egy alkalmazás vagy a Tomcat servleten futó összes alkalmazás számára.

#### <a name="application-level-data-sources"></a>Alkalmazásszintű adatforrások

1. Hozzon létre egy *context.xml* fájlt a projekt *META-INF/* könyvtárában. Hozza létre a *META-INF/* könyvtárat, ha az nem létezik.

2. A *context.xml fájlban*adjon hozzá egy `Context` elemet, amellyel az adatforrást JNDI-címhez csatolja. Cserélje `driverClassName` le a helyőrzőt a fenti táblázatból származó illesztőprogram osztálynevére.

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

3. Frissítse az alkalmazás *web.xml fájlját* az alkalmazás adatforrásának használatához.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="finalize-configuration"></a>Konfiguráció véglegesítése

Végül a vezető JARs-t a Tomcat classpath-ba helyezzük, és újraindítjuk az App Service-t. Győződjön meg arról, hogy a JDBC illesztőprogram-fájlok elérhetők a Tomcat classloader számára a */home/tomcat/lib* könyvtárba helyezve. (Hozza létre ezt a könyvtárat, ha még nem létezik.) Ha ezeket a fájlokat az App Service-példányba szeretné feltölteni, hajtsa végre az alábbi lépéseket:

1. A [Cloud Shellben](https://shell.azure.com)telepítse a webapp bővítményt:

    ```azurecli-interactive
    az extension add -–name webapp
    ```

2. SSH-alagút létrehozásához futtassa a következő CLI parancsot az App Service szolgáltatásba:

    ```azurecli-interactive
    az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
    ```

3. Csatlakozzon a helyi bújtatási porthoz az SFTP-ügyféllel, és töltse fel a fájlokat a */home/tomcat/lib* mappába.

Másik lehetőségként ftp-ügyféllel is feltöltheti a JDBC-illesztőprogramot. Kövesse az alábbi [utasításokat az FTP-hitelesítő adatok megszerzéséhez.](deploy-configure-credentials.md)

## <a name="configuring-tomcat"></a>Tomcat konfigurálása

A Tomcat `server.xml` vagy más konfigurációs fájlok szerkesztéséhez először jegyezze fel a Tomcat főverzióját a portálon.

1. Keresse meg a Tomcat kezdőkönyvtárat `env` a verziójához a parancs futtatásával. Keresse meg a főverzióval `AZURE_TOMCAT`kezdődő és egyező környezeti változót. Például `AZURE_TOMCAT85_HOME` a Tomcat 8.5 Tomcat könyvtárára mutat.
1. Miután azonosította a tomcat kezdőkönyvtárat a verziójához, másolja a konfigurációs könyvtárat a programba. `D:\home` Ha például `AZURE_TOMCAT85_HOME` a értéke `D:\Program Files (x86)\apache-tomcat-8.5.37`a , a másolt könyvtár `D:\home\apache-tomcat-8.5.37`új elérési útja a .

Végül indítsa újra az App Service-t. A bevetések kell `D:\home\site\wwwroot\webapps` menni, mint korábban.

## <a name="configure-java-se"></a>Java SE konfigurálása

Amikor a fut a . JAR alkalmazás Java SE `server.port` a Windows, át, mint egy parancssori opciót, mint az alkalmazás elindul. A HTTP-portmanuálisan feloldható `HTTP_PLATFORM_PORT`a környezeti változóból. Ennek a környezeti változónak az értéke az a HTTP-port lesz, amelyet az alkalmazásnak figyelnie kell. 

## <a name="java-runtime-statement-of-support"></a>Java futásidejű támogatási nyilatkozat

### <a name="jdk-versions-and-maintenance"></a>JDK verziók és karbantartás

Az Azure által támogatott Java Development Kit (JDK) a [Zulu](https://www.azul.com/downloads/azure-only/zulu/) az [Azul Systems](https://www.azul.com/)en keresztül érhető el.

A főverziók frissítései az Azure App Service for Windows új futásidejű beállításaiban lesznek elérhetők. Az ügyfelek a Java újabb verzióira frissítenek az App Service-telepítés konfigurálásával, és felelősek a tesztelésért és annak biztosításáért, hogy a nagyobb frissítés megfeleljen az igényeiknek.

A támogatott JDK-k automatikusan, minden év januárjában, áprilisában, júliusában és októberében negyedévente javításokat hoznak. Az Azure-beli Java-val kapcsolatos további információkért tekintse meg [ezt a támogatási dokumentumot.](https://docs.microsoft.com/azure/java/jdk/)

### <a name="security-updates"></a>Biztonsági frissítések

A jelentős biztonsági rések javításai és javításai azonnal megjelennek, amint azok elérhetővé válnak az Azul Systems rendszertől. A "jelentős" biztonsági rést a [NIST közös biztonsági réspontozási rendszer 2-es verziójának](https://nvd.nist.gov/cvss.cfm)9.0-s vagy magasabb alappontszáma határozza meg.

A Tomcat 8.0 [2018.](https://tomcat.apache.org/tomcat-80-eol.html) Bár a futásidejű továbbra is elérhető az Azure App Service szolgáltatásban, az Azure nem alkalmazza a biztonsági frissítéseket a Tomcat 8.0-s rendszerre. Ha lehetséges, telepítse át alkalmazásait a Tomcat 8.5 vagy 9.0-ra. A Tomcat 8.5 és a 9.0 egyaránt elérhető az Azure App Service szolgáltatásban. További információkért lásd a [hivatalos Tomcat oldalon.](https://tomcat.apache.org/whichversion.html) 

### <a name="deprecation-and-retirement"></a>Eprecáció és nyugdíjazás

Ha egy támogatott Java-futásidejű lesz kivonni, az Azure-fejlesztők az érintett futásidejű kap nak egy evehetetlenségi értesítést legalább hat hónappal a futásidő kivonása előtt.

### <a name="local-development"></a>Helyi fejlesztés

A fejlesztők az Azul Zulu Enterprise JDK Production Edition kiadását helyi fejlesztésre tölthetik le az [Azul letöltési webhelyéről.](https://www.azul.com/downloads/azure-only/zulu/)

### <a name="development-support"></a>Fejlesztési támogatás

Az Azure [által támogatott Azul Zulu JDK](https://www.azul.com/downloads/azure-only/zulu/) terméktámogatása a Microsofton keresztül érhető el, amikor az Azure-hoz vagy az [Azure Stackhez](https://azure.microsoft.com/overview/azure-stack/) fejleszt [egy minősített Azure-támogatási csomaggal.](https://azure.microsoft.com/support/plans/)

### <a name="runtime-support"></a>Futásidejű támogatás

Ha a fejlesztők az Azure-támogatáson keresztül [megnyithatnak egy problémát](/azure/azure-portal/supportability/how-to-create-azure-support-request) az Azul Zulu JDK-kkal, ha [rendelkeznek minősített támogatási csomaggal.](https://azure.microsoft.com/support/plans/)

## <a name="next-steps"></a>További lépések

Ez a témakör a Windows-alapú Azure App Service Java Runtime támogatási nyilatkozatát tartalmazza.

- Ha többet szeretne tudni awebalkalmazások Azure App Service szolgáltatással való üzemeltetéséről, olvassa el az App Service áttekintése című [témakört.](overview.md)
- Az Azure-beli fejlesztésjavakról az [Azure Dev Center című témakörben](https://docs.microsoft.com/java/azure/?view=azure-java-stable)talál további információt.
