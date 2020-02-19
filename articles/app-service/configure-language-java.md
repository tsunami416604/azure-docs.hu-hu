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
ms.custom: seodec18
ms.openlocfilehash: e5beb60107b3632da336a20f167e1c2f5b53140a
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461266"
---
# <a name="configure-a-windows-java-app-for-azure-app-service"></a>Windows Java-alkalmazás konfigurálása Azure App Servicehoz

Azure App Service lehetővé teszi a Java-fejlesztők számára, hogy a Tomcat webalkalmazásaikat teljes mértékben felügyelt, Windows-alapú szolgáltatáson hozza létre, helyezheti üzembe és méretezheti. Alkalmazásokat telepíthet a Maven beépülő modulokból a parancssorból vagy olyan szerkesztőkből, mint például a IntelliJ, az Eclipse vagy a Visual Studio code.

Ez az útmutató a Java-fejlesztőknek a App Service-ban való használatával kapcsolatos főbb fogalmakat és útmutatást tartalmazza. Ha még soha nem használta a Azure App Servicet, először olvassa el a [Java](app-service-web-get-started-java.md) rövid útmutatóját. A Java-fejlesztésre nem jellemző App Service használatára vonatkozó általános kérdéseket a [Windows app Service gyakori](faq-configuration-and-management.md)kérdések című szakaszban találja.

## <a name="deploying-your-app"></a>Az alkalmazás üzembe helyezése

A [mavenhez készült Azure Web App beépülő modullal](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) üzembe helyezheti a. War fájlokat. A népszerű ide-val történő üzembe helyezést [Azure Toolkit for IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij) vagy [Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse)is támogatja.

Ellenkező esetben a telepítési módszer az archiválás típusától függ:

- A. War fájlok Tomcatbe való üzembe helyezéséhez használja a `/api/wardeploy/` végpontot az archív fájl KÖZZÉTÉTELéhez. Az API-val kapcsolatos további információkért tekintse meg [ezt a dokumentációt](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file).
- A. jar fájlok Java SE-re való telepítéséhez használja a kudu webhely `/api/zipdeploy/` végpontját. Az API-val kapcsolatos további információkért tekintse meg [ezt a dokumentációt](https://docs.microsoft.com/azure/app-service/deploy-zip#rest).

Ne telepítse a. War-t FTP használatával. Az FTP-eszköz indítási parancsfájlok, függőségek vagy más futásidejű fájlok feltöltésére szolgál. A webalkalmazások üzembe helyezése nem optimális megoldás.

## <a name="logging-and-debugging-apps"></a>Alkalmazások naplózása és hibakeresése

A teljesítménnyel kapcsolatos jelentések, a forgalmi vizualizációk és az állapot-kivizsgálások a Azure Portalon keresztül érhetők el az egyes alkalmazásokhoz. További információ: [Azure app Service diagnosztika áttekintése](overview-diagnostics.md).

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
- [SSL-kötések konfigurálása](configure-ssl-bindings.md)
- [CDN hozzáadása](../cdn/cdn-add-to-web-app.md)
- [A kudu hely konfigurálása](https://github.com/projectkudu/kudu/wiki/Configurable-settings)

### <a name="set-java-runtime-options"></a>Java-futtatókörnyezet beállításainak megadása

A lefoglalt memória vagy más JVM futásidejű beállításainak megadásához hozzon létre egy `JAVA_OPTS` nevű [alkalmazást](configure-common.md#configure-app-settings) a beállításokkal. App Service átadja ezt a beállítást környezeti változóként a Java futtatókörnyezetnek a indításakor.

A Azure Portal a webalkalmazás **Alkalmazásbeállítások** területén hozzon létre egy új, `JAVA_OPTS` nevű alkalmazás-beállítást, amely tartalmazza a további beállításokat, például a `-Xms512m -Xmx1204m`.

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

- B1 és S1 példányok: `-Xms1024m -Xmx1024m`
- B2 és S2 példányok: `-Xms3072m -Xmx3072m`
- B3-as és S3-példányok: `-Xms6144m -Xmx6144m`

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

A Azure Portal a webalkalmazás **Alkalmazásbeállítások** területén hozzon létre egy új, `JAVA_OPTS` nevű alkalmazás-beállítást a következő értékkel: `-Dfile.encoding=UTF-8`.

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

A Tomcat-alkalmazás közvetlenül a servletből férhet hozzá a felhasználó jogcímeihez, ha a fő objektumot egy Térkép objektummá helyezi. A Térkép objektum az egyes jogcím-típusokat az adott típusú jogcímek gyűjteményéhez rendeli. Az alábbi kódban `request` a `HttpServletRequest`egy példánya.

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

Most megvizsgálhatja a `Map` objektumot bármely adott jogcím esetében. A következő kódrészlet például megismétli az összes jogcím-típust, és kinyomtatja az egyes gyűjtemények tartalmát.

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

A felhasználók kijelentkezéséhez használja a `/.auth/ext/logout` elérési utat. További műveletek elvégzéséhez tekintse meg [app Service hitelesítési és engedélyezési használati](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to)dokumentációját. A Tomcat [HttpServletRequest felületén](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) és annak módszerein is hivatalos dokumentáció található. A következő servlet metódusok is hidratálva vannak a App Service konfigurációja alapján:

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

A funkció letiltásához hozzon létre egy `WEBSITE_AUTH_SKIP_PRINCIPAL` nevű Alkalmazásbeállítás `1`értékkel. A App Service által hozzáadott összes servlet-szűrő letiltásához hozzon létre egy `WEBSITE_SKIP_FILTERS` nevű beállítást `1`értékkel.

### <a name="configure-tlsssl"></a>A TLS/SSL konfigurálása

A meglévő SSL-tanúsítvány feltöltéséhez és az alkalmazás tartománynevéhez való kötéséhez kövesse az [Egyéni DNS-név biztonságossá tétele SSL-kötéssel Azure app Serviceban](configure-ssl-bindings.md) található utasításokat. Alapértelmezés szerint az alkalmazás továbbra is engedélyezi a HTTP-kapcsolatokat – az oktatóanyag adott lépéseinek végrehajtásával kényszerítheti az SSL és a TLS használatát.

### <a name="use-keyvault-references"></a>Kulcstartó-hivatkozások használata

Az [Azure](../key-vault/key-vault-overview.md) kulcstartó központosított titkos felügyeletet biztosít a hozzáférési házirendekkel és a naplózási előzményekkel. A kulcsok (például jelszavak vagy kapcsolati karakterláncok) a kulcstartóban tárolhatók, és a titkos kulcsokat az alkalmazásban környezeti változókon keresztül érhetik el.

Először is kövesse az alkalmazáshoz [való hozzáférés megadására](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) vonatkozó utasításokat, és a [titkos kulcshoz tartozó kulcstároló-hivatkozást egy Alkalmazásbeállítások alapján](app-service-key-vault-references.md#reference-syntax)végezze el a Key Vault. Ellenőrizheti, hogy a hivatkozás feloldja-e a titkos kulcsot a környezeti változó kinyomtatásával, miközben távolról fér hozzá a App Service terminálhoz.

Ha ezeket a titkokat be szeretné szúrni a Spring vagy a Tomcat konfigurációs fájljába, használja a környezeti változók injekciós szintaxisát (`${MY_ENV_VAR}`). A Spring konfigurációs fájlok esetében tekintse meg ezt a dokumentációt a [külső konfigurációkról](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).


## <a name="configure-apm-platforms"></a>APM-platformok konfigurálása

Ez a szakasz bemutatja, hogyan csatlakoztathatók a Linux rendszeren üzembe Azure App Service helyezett Java-alkalmazások a NewRelic és a AppDynamics Application Performance monitoring (APM) platformokkal.

### <a name="configure-new-relic"></a>Új ereklye konfigurálása

1. Új ereklye fiók létrehozása a [NewRelic.com](https://newrelic.com/signup) -on
2. Töltse le a Java-ügynököt a NewRelic-ből, mert a fájlnév a *newrelic-Java-x. x. x. zip*fájlhoz hasonló lesz.
3. A licenckulcs másolásához az ügynököt később kell konfigurálnia.
4. Hozzon létre egy új címtár- */Home/site/wwwroot/APM*a [kudu-konzol](https://github.com/projectkudu/kudu/wiki/Kudu-console) használatával.
5. Töltse fel a kicsomagolt új ereklye Java-ügynök fájljait egy könyvtárba a */Home/site/wwwroot/APM*alatt. Az ügynök fájljainak a */Home/site/wwwroot/APM/newrelic*-ben kell lenniük.
6. Módosítsa a YAML fájlt a */Home/site/wwwroot/APM/newrelic/newrelic.YML* címen, és cserélje le a helyőrző licenc értékét a saját licenckulcs használatára.
7. A Azure Portal tallózással keresse meg az alkalmazást App Service és hozzon létre egy új alkalmazás-beállítást.
    - Ha az alkalmazás **Java SE**-t használ, hozzon létre egy `JAVA_OPTS` nevű környezeti változót a `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`értékkel.
    - Ha a **tomcat**-t használja, hozzon létre egy `CATALINA_OPTS` nevű környezeti változót a (z) `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`értékkel.

### <a name="configure-appdynamics"></a>AppDynamics konfigurálása

1. AppDynamics-fiók létrehozása a [AppDynamics.com](https://www.appdynamics.com/community/register/) -ben
2. Töltse le a Java-ügynököt a AppDynamics webhelyről, és a fájlnév a *AppServerAgent-x. x. x. xxxxx. zip* fájlhoz hasonló lesz.
3. Hozzon létre egy új címtár- */Home/site/wwwroot/APM*a [kudu-konzol](https://github.com/projectkudu/kudu/wiki/Kudu-console) használatával.
4. Töltse fel a Java-ügynök fájljait egy könyvtárba a */Home/site/wwwroot/APM*alatt. Az ügynök fájljainak a */Home/site/wwwroot/APM/appdynamics*-ben kell lenniük.
5. A Azure Portal tallózással keresse meg az alkalmazást App Service és hozzon létre egy új alkalmazás-beállítást.
    - Ha **Java SE**-t használ, hozzon létre egy `JAVA_OPTS` nevű környezeti változót az érték `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` ahol a `<app-name>` a app Service neve.
    - Ha a **tomcat**-t használja, hozzon létre egy `CATALINA_OPTS` nevű környezeti változót az érték `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` ahol a `<app-name>` a app Service neve.

>  Ha már rendelkezik környezeti változóval `JAVA_OPTS` vagy `CATALINA_OPTS`, fűzze hozzá az `-javaagent:/...` lehetőséget az aktuális érték végéhez.

## <a name="data-sources"></a>Adatforrások

### <a name="tomcat"></a>Tomcat

Ezek az utasítások az összes adatbázis-kapcsolatra érvényesek. A helyőrzőket ki kell töltenie a kiválasztott adatbázis illesztőprogram-osztályának nevével és JAR-fájljával. A megadott tábla az osztályok neveivel és az illesztőprogramok letöltésével közös adatbázisokhoz.

| Adatbázis   | Illesztőprogram-osztály neve                             | JDBC-illesztőprogram                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Letöltés](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Letöltés](https://dev.mysql.com/downloads/connector/j/) (válassza a "platform független" lehetőséget) |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Letöltés](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#available-downloads-of-jdbc-driver-for-sql-server)                                                           |

Ahhoz, hogy a Tomcat a Java Database Connectivity (JDBC) vagy a Java perzisztencia API (közgyűlés) használatára legyen konfigurálva, először testre kell szabnia a Tomcat által az indításkor beolvasott `CATALINA_OPTS` környezeti változót. Adja meg ezeket az értékeket a [app Service Maven beépülő modul](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md)alkalmazás-beállításán keresztül:

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Vagy állítsa be a környezeti változókat a Azure Portal **konfigurációs** > **Alkalmazásbeállítások** lapján.

Ezután állapítsa meg, hogy az adatforrásnak elérhetőnek kell lennie egy alkalmazáshoz vagy a Tomcat servletben futó összes alkalmazáshoz.

#### <a name="application-level-data-sources"></a>Alkalmazás szintű adatforrások

1. Hozzon létre egy *Context. XML* fájlt a projekt *META-INF-* fájljában/könyvtárában. Ha nem létezik, hozza létre a *META-INF/* könyvtárat.

2. A *Context. xml fájlban*adjon hozzá egy `Context` elemet az adatforrás JNDI való összekapcsolásához. Cserélje le az `driverClassName` helyőrzőt az illesztőprogram osztályának nevére a fenti táblázatból.

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

3. Frissítse az alkalmazás *web. XML* fájlját az alkalmazás adatforrásának használatára.

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

1. A `env` parancs futtatásával keresse meg a verzióhoz tartozó tomcat-kezdőkönyvtárat. Keresse meg azt a környezeti változót, amely `AZURE_TOMCAT`kezdődik, és megfelel a főverziónak. `AZURE_TOMCAT85_HOME` például a Tomcat-címtárra mutat a Tomcat 8,5.
1. Miután azonosította a Tomcat-kezdőkönyvtárat a verzióhoz, másolja a konfigurációs könyvtárat `D:\home`ra. Ha például `AZURE_TOMCAT85_HOME` `D:\Program Files (x86)\apache-tomcat-8.5.37`értékkel rendelkezik, a másolt könyvtár új elérési útja `D:\home\apache-tomcat-8.5.37`lesz.

Végezetül indítsa újra a App Service. A központi telepítések csak az előzőekben `D:\home\site\wwwroot\webapps`.

## <a name="configure-java-se"></a>A Java SE konfigurálása

A futtatásakor. JAR-alkalmazás Java SE-on Windows rendszeren, `server.port` az alkalmazás indításakor a parancssori kapcsolóként lesz átadva. A HTTP-portot manuálisan is feloldható a környezeti változóból, `HTTP_PLATFORM_PORT`. Ennek a környezeti változónak az értéke lesz az alkalmazás által figyelni kívánt HTTP-port. 

## <a name="java-runtime-statement-of-support"></a>A Java futtatókörnyezet támogatási nyilatkozata

### <a name="jdk-versions-and-maintenance"></a>JDK-verziók és-karbantartás

Az Azure által támogatott Java Development Kit (JDK) a [Azul Systems](https://www.azul.com/)által biztosított [Zulu](https://www.azul.com/downloads/azure-only/zulu/) .

A főverzió frissítései a Windows Azure App Service új futtatókörnyezeti lehetőségein keresztül lesznek elérhetők. Az ügyfelek a Java újabb verzióit frissítik a App Service üzembe helyezésének konfigurálásával, és felelősek a fő frissítés megfelelőségének teszteléséhez és biztosításához.

A támogatott JDK minden év januárjában, áprilisban, júliusban és októberben automatikusan megtörténik. Az Azure-beli Java-ról további információt [ebben a támogatási dokumentumban](https://docs.microsoft.com/azure/java/jdk/)talál.

### <a name="security-updates"></a>Biztonsági frissítések

A főbb biztonsági rések javításait és javításait a rendszer azonnal felszabadítja, amint azok elérhetők lesznek a Azul Systems-től. A "fő" biztonsági rést az 9,0-es vagy újabb alappontszám határozza meg a [NIST Common sebezhetőségi pontozási rendszer 2. verziójában](https://nvd.nist.gov/cvss.cfm).

A Tomcat 8,0 [2018. szeptember 30-ig elérte az élettartamot (EOL)](https://tomcat.apache.org/tomcat-80-eol.html). Habár a futtatókörnyezet továbbra is a Azure App Service avialable, az Azure nem fogja alkalmazni a Tomcat 8,0-es biztonsági frissítéseit. Ha lehetséges, telepítse át az alkalmazásokat a Tomcat 8,5 vagy a 9,0-es verzióra. A Tomcat 8,5 és 9,0 egyaránt elérhető Azure App Serviceon. További információért tekintse meg a [hivatalos tomcat-webhelyet](https://tomcat.apache.org/whichversion.html) . 

### <a name="deprecation-and-retirement"></a>Elavulás és nyugdíjazás

Ha egy támogatott Java-futtatókörnyezet megszűnik, az érintett futtatókörnyezetet használó Azure-fejlesztők elavult értesítést kapnak a futtatókörnyezet kivonása előtt legalább hat hónappal.

### <a name="local-development"></a>Helyi fejlesztés

A fejlesztők letölthetik az Azul Zulu Enterprise JDK éles kiadását helyi fejlesztésre az [Azul letöltési webhelyéről](https://www.azul.com/downloads/azure-only/zulu/).

### <a name="development-support"></a>Fejlesztési támogatás

Az Azure [által támogatott Azul ZULU JDK-](https://www.azul.com/downloads/azure-only/zulu/) vel kapcsolatos terméktámogatás a Microsofton keresztül érhető el az Azure-hoz való fejlesztéshez, illetve a [Azure stack](https://azure.microsoft.com/overview/azure-stack/) egy [minősített Azure-támogatási csomaggal](https://azure.microsoft.com/support/plans/).

### <a name="runtime-support"></a>Podpora modulu Runtime

A fejlesztők az Azure-támogatással [megnyithatják](/azure/azure-portal/supportability/how-to-create-azure-support-request) az Azul Zulu JDK kapcsolatos problémákat, ha rendelkeznek [minősített támogatási csomaggal](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Következő lépések

Ez a témakör a Java Runtime Azure App Service Windows rendszeren történő támogatására vonatkozó utasításait ismerteti.

- Ha többet szeretne megtudni a webalkalmazások Azure App Serviceről való üzemeltetéséről, tekintse meg a [app Service áttekintését](overview.md).
- További információ az Azure-beli Java-fejlesztésről: [Azure for Java fejlesztői központ](https://docs.microsoft.com/java/azure/?view=azure-java-stable).
