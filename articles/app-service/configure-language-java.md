---
title: Windows Java-alkalmazások konfigurálása – Azure App Service | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhat Java-alkalmazásokat a Azure App Service alapértelmezett Windows-példányain való futtatáshoz.
keywords: Azure app Service, webalkalmazás, Windows, OSS, Java
services: app-service
author: jasonfreeberg
manager: jeconnock
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18
ms.openlocfilehash: 0538b3ea4ac3a7999a3028cfd8b2cfafbbf7856c
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967280"
---
# <a name="configure-a-windows-java-app-for-azure-app-service"></a>Windows Java-alkalmazás konfigurálása Azure App Servicehoz

Azure App Service lehetővé teszi a Java-fejlesztők számára, hogy a Tomcat webalkalmazásaikat teljes mértékben felügyelt, Windows-alapú szolgáltatáson hozza létre, helyezheti üzembe és méretezheti. Alkalmazásokat telepíthet a Maven beépülő modulokból a parancssorból vagy olyan szerkesztőkből, mint például a IntelliJ, az Eclipse vagy a Visual Studio code.

Ez az útmutató a Java-fejlesztőknek a App Service-ban való használatával kapcsolatos főbb fogalmakat és útmutatást tartalmazza. Ha még soha nem használta a Azure App Servicet, először olvassa el a [Java](app-service-web-get-started-java.md) rövid útmutatóját. A Java-fejlesztésre nem jellemző App Service használatára vonatkozó általános kérdéseket a [Windows app Service gyakori](faq-configuration-and-management.md)kérdések című szakaszban találja.

## <a name="deploying-your-app"></a>Az alkalmazás üzembe helyezése

A. War fájlok telepítéséhez a [Maven beépülő modult használhatja a Azure app Servicehoz](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) . A népszerű ide-val történő üzembe helyezést [Azure Toolkit for IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij) vagy [Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse)is támogatja.

Ellenkező esetben a telepítési módszer az archiválás típusától függ:

- A. War fájlok tomcatbe való üzembe helyezéséhez `/api/wardeploy/` használja a végpontot az archív fájl közzétételéhez. Az API-val kapcsolatos további információkért tekintse meg [ezt](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file)a dokumentációt.

Ne telepítse a. War-t FTP használatával. Az FTP-eszköz indítási parancsfájlok, függőségek vagy más futásidejű fájlok feltöltésére szolgál. A webalkalmazások üzembe helyezése nem optimális megoldás.

## <a name="logging-and-debugging-apps"></a>Alkalmazások naplózása és hibakeresése

A teljesítménnyel kapcsolatos jelentések, a forgalmi vizualizációk és az állapot-kivizsgálások a Azure Portalon keresztül érhetők el az egyes alkalmazásokhoz. További információ: [Azure app Service diagnosztika áttekintése](overview-diagnostics.md).

### <a name="ssh-console-access"></a>SSH-konzolhoz való hozzáférés

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>Diagnosztikai naplók streamelése

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

További információ: [folyamatos átvitelű naplók az Azure CLI-vel](troubleshoot-diagnostic-logs.md#streaming-with-azure-cli).

### <a name="app-logging"></a>Alkalmazás naplózása

Az Azure Portal vagy az [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) -n keresztül történő [alkalmazás-naplózás](troubleshoot-diagnostic-logs.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#enablediag) engedélyezésével beállíthatja, hogy a app Service az alkalmazás szabványos konzoljának kimenetét és standard konzoljának hibáit a helyi fájlrendszerbe vagy az Azure-Blob Storageba írja. A helyi App Service filesystem-példányra való naplózás a konfigurálás után 12 órával le van tiltva. Ha nagyobb adatmegőrzésre van szüksége, konfigurálja úgy az alkalmazást, hogy egy blob Storage-tárolóba írja a kimenetet. A Java-és a Tomcat-alkalmazás naplói a */LogFiles/Application/* könyvtárban találhatók.

Ha az alkalmazás [Logback](https://logback.qos.ch/) vagy [Log4j](https://logging.apache.org/log4j) használ a nyomkövetéshez, ezeket a nyomkövetéseket áttekintheti az Azure [Application Insights-ba Application Insights ](/azure/application-insights/app-insights-java-trace-logs).


## <a name="customization-and-tuning"></a>Testreszabás és Finomhangolás

A Azure App Service a Azure Portal és a CLI használatával támogatja a Box finomhangolását és testreszabását. Tekintse át a következő cikkeket a nem Java-specifikus webalkalmazás-konfigurációhoz:

- [Alkalmazásbeállítások konfigurálása](configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)
- [Egyéni tartomány beállítása](app-service-web-tutorial-custom-domain.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Enable SSL](app-service-web-tutorial-custom-ssl.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [CDN hozzáadása](../cdn/cdn-add-to-web-app.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [A kudu hely konfigurálása](https://github.com/projectkudu/kudu/wiki/Configurable-settings)

### <a name="set-java-runtime-options"></a>Java-futtatókörnyezet beállításainak megadása

A lefoglalt memória vagy más JVM futásidejű beállításainak megadásához hozzon létre egy `JAVA_OPTS` nevű [alkalmazást](configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) a következő beállításokkal:. App Service átadja ezt a beállítást környezeti változóként a Java futtatókörnyezetnek a indításakor.

A Azure Portal a webalkalmazás **Alkalmazásbeállítások** területén hozzon létre egy nevű `JAVA_OPTS` új alkalmazás-beállítást, amely tartalmazza a további `-Xms512m -Xmx1204m`beállításokat, például a következőt:.

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

A WebSockets támogatásának bekapcsolása a Azure Portal az alkalmazás **alkalmazás** -beállításaiban. A beállítás érvénybe léptetéséhez újra kell indítania az alkalmazást.

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

A Azure Portal a webalkalmazás **Alkalmazásbeállítások** területén hozzon létre egy új, értékkel `JAVA_OPTS` `-Dfile.encoding=UTF-8`ellátott alkalmazás-beállítást.

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

## <a name="secure-applications"></a>Alkalmazások védelme

A App Serviceban futó Java-alkalmazásokhoz ugyanaz a [biztonsági eljárások](/azure/security/security-paas-applications-using-app-services) tartoznak, mint más alkalmazások.

### <a name="authenticate-users-easy-auth"></a>Felhasználók hitelesítése (egyszerű hitelesítés)

Az alkalmazás hitelesítésének beállítása a Azure Portal a **hitelesítés és engedélyezés** lehetőséggel. Itt engedélyezheti a hitelesítést Azure Active Directory vagy közösségi bejelentkezéssel, például a Facebook, a Google vagy a GitHub használatával. Azure Portal konfiguráció csak egyetlen hitelesítési szolgáltató konfigurálásakor működik. További információ: [a app Service alkalmazás konfigurálása Azure Active Directory bejelentkezési adatok használatára](configure-authentication-provider-aad.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) és a kapcsolódó cikkek más identitás-szolgáltatóknak való használatára. Ha több bejelentkezési szolgáltatót is engedélyeznie kell, kövesse az [app Service-hitelesítés testreszabása](app-service-authentication-how-to.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) című cikk utasításait.

#### <a name="tomcat-and-wildfly"></a>Tomcat és Wildfly

A Tomcat-vagy Wildfly-alkalmazás közvetlenül a servletből fér hozzá a felhasználó jogcímeihez, ha a fő objektumot egy Térkép objektummá helyezi. A Térkép objektum az egyes jogcím-típusokat az adott típusú jogcímek gyűjteményéhez rendeli. Az alábbi `request` kódban a egy `HttpServletRequest`példánya.

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

Most már megvizsgálhatja `Map` az objektumot egy adott jogcím esetében is. A következő kódrészlet például megismétli az összes jogcím-típust, és kinyomtatja az egyes gyűjtemények tartalmát.

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

A funkció letiltásához hozzon létre egy nevű `WEBSITE_AUTH_SKIP_PRINCIPAL` Alkalmazásbeállítás `1`értéket. A app Service által hozzáadott összes servlet-szűrő letiltásához hozzon `WEBSITE_SKIP_FILTERS` létre egy nevű beállítást `1`a következő értékkel:.

### <a name="configure-tlsssl"></a>A TLS/SSL konfigurálása

A meglévő SSL-tanúsítvány feltöltéséhez és az alkalmazás tartománynevéhez való kötéséhez kövesse a [meglévő egyéni SSL-tanúsítvány kötése](app-service-web-tutorial-custom-ssl.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) témakör utasításait. Alapértelmezés szerint az alkalmazás továbbra is engedélyezi a HTTP-kapcsolatokat – az oktatóanyag adott lépéseinek végrehajtásával kényszerítheti az SSL és a TLS használatát.

### <a name="use-keyvault-references"></a>Kulcstartó-hivatkozások használata

Az [Azure](../key-vault/key-vault-overview.md) kulcstartó központosított titkos felügyeletet biztosít a hozzáférési házirendekkel és a naplózási előzményekkel. A kulcsok (például jelszavak vagy kapcsolati karakterláncok) a kulcstartóban tárolhatók, és a titkos kulcsokat az alkalmazásban környezeti változókon keresztül érhetik el.

Először is kövesse az alkalmazáshoz [való hozzáférés](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) megadására vonatkozó utasításokat, és a titkos kulcshoz tartozó kulcstároló [-hivatkozást egy Alkalmazásbeállítások alapján](app-service-key-vault-references.md#reference-syntax)végezze el a Key Vault. Ellenőrizheti, hogy a hivatkozás feloldja-e a titkos kulcsot a környezeti változó kinyomtatásával, miközben távolról fér hozzá a App Service terminálhoz.

Ha ezeket a titkokat be szeretné szúrni a Spring vagy a Tomcat konfigurációs fájljába, használja`${MY_ENV_VAR}`a környezeti változók befecskendezésének szintaxisát (). A Spring konfigurációs fájlok esetében tekintse meg ezt a dokumentációt a [külső konfigurációkról](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).


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
    - Ha az alkalmazás **Java SE**-t használ, hozzon létre egy `JAVA_OPTS` nevű környezeti változót az értékkel. `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`
    - Ha a **tomcat**-t használja, hozzon létre egy `CATALINA_OPTS` nevű környezeti változót az értékkel `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.

### <a name="configure-appdynamics"></a>AppDynamics konfigurálása

1. AppDynamics-fiók létrehozása a [AppDynamics.com](https://www.appdynamics.com/community/register/) -ben
2. Töltse le a Java-ügynököt a AppDynamics webhelyről, és a fájlnév a *AppServerAgent-x. x. x. xxxxx. zip* fájlhoz hasonló lesz.
3. Hozzon létre egy új címtár- */Home/site/wwwroot/APM*a [kudu-konzol](https://github.com/projectkudu/kudu/wiki/Kudu-console) használatával.
4. Töltse fel a Java-ügynök fájljait egy könyvtárba a */Home/site/wwwroot/APM*alatt. Az ügynök fájljainak a */Home/site/wwwroot/APM/appdynamics*-ben kell lenniük.
5. A Azure Portal tallózással keresse meg az alkalmazást App Service és hozzon létre egy új alkalmazás-beállítást.
    - Ha **Java SE**-t használ, hozzon létre egy nevű `JAVA_OPTS` környezeti változót `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` , `<app-name>` amelynek értéke a app Service neve.
    - Ha a **tomcat**- `<app-name>` t használja, hozzon létre egy `CATALINA_OPTS` nevű környezeti változót, amelynek értéke `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` a app Service neve.

>  Ha már rendelkezik környezeti változóval `JAVA_OPTS` a vagy `CATALINA_OPTS`a esetében, az `-javaagent:/...` aktuális érték végéhez fűzze hozzá a kapcsolót.

## <a name="data-sources"></a>Adatforrások

### <a name="tomcat"></a>Tomcat

Ezek az utasítások az összes adatbázis-kapcsolatra érvényesek. A helyőrzőket ki kell töltenie a kiválasztott adatbázis illesztőprogram-osztályának nevével és JAR-fájljával. A megadott tábla az osztályok neveivel és az illesztőprogramok letöltésével közös adatbázisokhoz.

| Adatbázis   | Illesztőprogram-osztály neve                             | JDBC-illesztőprogram                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Letöltés](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Töltse le](https://dev.mysql.com/downloads/connector/j/) (Válassza a "platform független" lehetőséget) |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Letöltés](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#available-downloads-of-jdbc-driver-for-sql-server)                                                           |

Ha a Tomcat-t a Java Database Connectivity (JDBC) vagy a Java perzisztencia API (közös parlamenti) használatára szeretné `CATALINA_OPTS` konfigurálni, először testre kell szabnia a Tomcat által az indításkor beolvasott környezeti változót. Adja meg ezeket az értékeket a [app Service Maven beépülő modul](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md)alkalmazás-beállításán keresztül:

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Vagy állítsa be a környezeti változókat a Azure Portal **konfigurációs** > **alkalmazás beállításai** lapján.

Ezután állapítsa meg, hogy az adatforrásnak elérhetőnek kell lennie egy alkalmazáshoz vagy a Tomcat servletben futó összes alkalmazáshoz.

#### <a name="application-level-data-sources"></a>Alkalmazás szintű adatforrások

1. Hozzon létre egy *Context. XML* fájlt a projekt *META-INF-* fájljában/könyvtárában. Ha nem létezik, hozza létre a *META-INF/* könyvtárat.

2. A *Context. xml fájlban*adjon hozzá `Context` egy elemet az adatforrás JNDI való összekapcsolásához. Cserélje le `driverClassName` a helyőrzőt az illesztőprogram osztályának nevére a fenti táblázatból.

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

Azt is megteheti, hogy FTP-ügyfél használatával tölti fel a JDBC-illesztőt. Az [FTP-hitelesítő adatok](deploy-configure-credentials.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)beszerzéséhez kövesse az alábbi utasításokat.

## <a name="configuring-tomcat"></a>A Tomcat konfigurálása

A Tomcat `server.xml` vagy más konfigurációs fájlok szerkesztéséhez először jegyezze fel a Tomcat főverzióját a portálon.

1. A `env` parancs futtatásával keresse meg a verzióhoz tartozó tomcat-kezdőkönyvtárat. Keresse meg azt a környezeti változót, `AZURE_TOMCAT`amely a-val kezdődik, és megfelel a főverziónak. Például a Tomcat `AZURE_TOMCAT85_HOME` 8,5-es tomcat-könyvtárába mutat.
1. Miután azonosította a-verzióhoz tartozó tomcat-kezdőkönyvtárat, másolja a konfigurációs könyvtárat `D:\home`a következőre:. Ha `AZURE_TOMCAT85_HOME` például a `D:\Program Files (x86)\apache-tomcat-8.5.37`értéke volt, a másolt könyvtár új elérési útja a következő lesz `D:\home\apache-tomcat-8.5.37`:.

Végezetül indítsa újra a App Service. A központi telepítéseknek ugyanúgy kell `D:\home\site\wwwroot\webapps` megjelenniük, mint korábban.

## <a name="java-runtime-statement-of-support"></a>A Java futtatókörnyezet támogatási nyilatkozata

### <a name="jdk-versions-and-maintenance"></a>JDK-verziók és-karbantartás

Az Azure által támogatott Java Development Kit (JDK) a [Azul Systems](https://www.azul.com/)által biztosított [Zulu](https://www.azul.com/downloads/azure-only/zulu/) .

A főverzió frissítései a Windows Azure App Service új futtatókörnyezeti lehetőségein keresztül lesznek elérhetők. Az ügyfelek a Java újabb verzióit frissítik a App Service üzembe helyezésének konfigurálásával, és felelősek a fő frissítés megfelelőségének teszteléséhez és biztosításához.

A támogatott JDK minden év januárjában, áprilisban, júliusban és októberben automatikusan megtörténik. Az Azure-beli Java-ról további információt [ebben a támogatási dokumentumban](https://docs.microsoft.com/azure/java/jdk/)talál.

### <a name="security-updates"></a>Biztonsági frissítések

A főbb biztonsági rések javításait és javításait a rendszer azonnal felszabadítja, amint azok elérhetők lesznek a Azul Systems-től. A "fő" biztonsági rést az 9,0-es vagy újabb alappontszám határozza meg a [NIST Common sebezhetőségi pontozási rendszer 2](https://nvd.nist.gov/cvss.cfm). verziójában.

### <a name="deprecation-and-retirement"></a>Elavulás és nyugdíjazás

Ha egy támogatott Java-futtatókörnyezet megszűnik, az érintett futtatókörnyezetet használó Azure-fejlesztők elavult értesítést kapnak a futtatókörnyezet kivonása előtt legalább hat hónappal.

### <a name="local-development"></a>Helyi fejlesztés

A fejlesztők letölthetik az Azul Zulu Enterprise JDK éles kiadását helyi fejlesztésre az [Azul letöltési](https://www.azul.com/downloads/azure-only/zulu/)webhelyéről.

### <a name="development-support"></a>Fejlesztési támogatás

Az Azure [által támogatott Azul ZULU JDK-](https://www.azul.com/downloads/azure-only/zulu/) vel kapcsolatos terméktámogatás a Microsofton keresztül érhető el az Azure-hoz való fejlesztéshez, illetve a [Azure stack](https://azure.microsoft.com/overview/azure-stack/) egy [minősített Azure-támogatási csomaggal](https://azure.microsoft.com/support/plans/).

### <a name="runtime-support"></a>Podpora modulu Runtime

A fejlesztők az Azure-támogatással megnyithatják az Azul Zulu JDK [kapcsolatos problémákat](/azure/azure-supportability/how-to-create-azure-support-request) , ha rendelkeznek [minősített támogatási csomaggal](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>További lépések

Ez a témakör a Java Runtime Azure App Service Windows rendszeren történő támogatására vonatkozó utasításait ismerteti.

- Ha többet szeretne megtudni a webalkalmazások Azure App Serviceről való üzemeltetéséről, tekintse meg a [app Service áttekintését](overview.md).
- További információ az Azure-beli Java-fejlesztésről: [Azure for Java fejlesztői központ](https://docs.microsoft.com/java/azure/?view=azure-java-stable).
