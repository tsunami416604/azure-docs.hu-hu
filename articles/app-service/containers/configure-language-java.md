---
title: Linuxos Java-alkalmazások – az Azure App Service konfigurálása |} A Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja a linuxon futó Azure App Service-ben futó Java alkalmazásokat.
keywords: az Azure app service, webalkalmazás, linux, oss, a java
services: app-service
author: rloutlaw
manager: angerobe
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 03/28/2019
ms.author: routlaw
ms.custom: seodec18
ms.openlocfilehash: b659c076974b0659c645c9b6460e458dfac8974a
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59551232"
---
# <a name="configure-a-linux-java-app-for-azure-app-service"></a>A Linuxos Java-alkalmazás konfigurálása az Azure App Service-ben

Linuxon futó Azure App Service lehetővé teszi, hogy gyorsan elkészítheti, telepítheti és méretezheti a Tomcat a Java-fejlesztőknek vagy a Java Standard Edition (SE) egy teljes körűen felügyelt Linux-alapú szolgáltatás webes alkalmazások csomagolva. A parancssorból vagy a-szerkesztők, mint például az intellij-vel, az Eclipse vagy a Visual Studio Code Maven beépülő modulok az alkalmazások központi telepítése.

Ez az útmutató főbb fogalmakat és utasításokat a Java-fejlesztőknek szól, akik egy beépített Linux-tároló használata App Service-ben. Ha korábban nem használta az Azure App Service, kövesse a [Java rövid](quickstart-java.md) és [PostgreSQL – oktatóanyag Java](tutorial-java-enterprise-postgresql-app.md) első.

## <a name="logging-and-debugging-apps"></a>Bejelentkezés és hibakeresés alkalmazások

Teljesítményjelentések készítésére, forgalom Vizualizációk és egészségügyi checkups az egyes alkalmazások az Azure Portalon keresztül érhetők el. További információkért lásd: [diagnosztikai áttekintése az Azure App Service](../overview-diagnostics.md).

### <a name="ssh-console-access"></a>SSH hozzáféréséhez a konzolhoz

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>Diagnosztikai naplók streamelése

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

További információkért lásd: [folyamatos átviteli naplók az Azure CLI-vel](../troubleshoot-diagnostic-logs.md#streaming-with-azure-cli).

### <a name="app-logging"></a>Alkalmazás-naplózás

Engedélyezése [alkalmazásnaplózás](../troubleshoot-diagnostic-logs.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#enablediag) az Azure Portalon keresztül vagy [Azure CLI-vel](/cli/azure/webapp/log#az-webapp-log-config) App Service-ben az alkalmazás normál konzolkimenet és standard szintű konzol hibaadatfolyamok írni a helyi konfigurálása fájlrendszer- vagy Azure Blob Storage. Az App Service helyi fájlrendszer naplózása példány le van tiltva 12 óra után van konfigurálva. Ha hosszabb adatmegőrzés megadásához, az alkalmazás kiírhatja a kimenetet egy Blob storage-tároló konfigurálása.

Ha az alkalmazás [Logback](https://logback.qos.ch/) vagy [Log4j](https://logging.apache.org/log4j) nyomkövetés, is továbbíthatja, tekintse át az Azure Application Insights a nyomkövetések utasításai a naplózási keretrendszer konfigurációs [Ismerkedés a Java-nyomkövetési naplókat az Application Insights](/azure/application-insights/app-insights-java-trace-logs).

## <a name="customization-and-tuning"></a>Testreszabás és hangolás

Az Azure App Service Linux rendszeren a box finomhangolásához és testreszabása az Azure portal és CLI keresztül támogatja. Tekintse át a nem a Java-specifikus webalkalmazás konfigurációjának az alábbi cikkeket:

- [App Service szolgáltatás beállításainak konfigurálása](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Egyéni tartomány beállítása](../app-service-web-tutorial-custom-domain.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Enable SSL](../app-service-web-tutorial-custom-ssl.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [CDN hozzáadása](../../cdn/cdn-add-to-web-app.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)

### <a name="set-java-runtime-options"></a>Java-futtatókörnyezet beállításainak megadása

A Tomcat és a Java használata környezetben állítsa a lefoglalt memória vagy más JVM futásidejű beállítások, állítsa be a JAVA_OPTS, mint az alább látható módon egy [Alkalmazásbeállítás](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings). Az App Service Linux továbbítja ezt a beállítást környezeti változóban a Java-futtatókörnyezet indításakor.

Az Azure Portalon alatt **Alkalmazásbeállítások** a webalkalmazást, hozzon létre egy új alkalmazásbeállítást nevű `JAVA_OPTS` , amely tartalmazza a további beállításokat, például `$JAVA_OPTS -Xms512m -Xmx1204m`.

A alkalmazás beállításai az Azure App Service Linux Maven beépülő modul konfigurálásához beállításérték/címkéket adhat hozzá, az Azure beépülő modul szakaszban. Az alábbi mintakód egy adott minimális és maximális Java halommemória mérete:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>$JAVA_OPTS -Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

Az egyetlen alkalmazást futtató és egy üzembe helyezési pont az App Service-csomag a fejlesztők használhatják a következő beállításokat:

- B1 és S1-példányok:-Xms1024m-Xmx1024m
- B2 és S2-példányok:-Xms3072m-Xmx3072m
- B3 és S3-példányok:-Xms6144m-Xmx6144m


Amikor alkalmazás halommemória finomhangolásának beállításai, tekintse át az App Service-csomag részletei, és több alkalmazás és üzembe helyezési pont figyelembe kell keresnie a optimális lefoglalt memória.

### <a name="turn-on-web-sockets"></a>Kapcsolja be a web sockets

Kapcsolja be az Azure Portalon, a web sockets támogatása a **Alkalmazásbeállítások** az alkalmazáshoz. Indítsa újra az alkalmazást a beállítás érvénybe kell.

Kapcsolja be, a web socket támogatása az Azure CLI használatával a következő paranccsal:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --web-sockets-enabled true
```

Indítsa újra az alkalmazást:

```azurecli-interactive
az webapp stop --name <app-name> --resource-group <resource-group-name>
az webapp start --name <app-name> --resource-group <resource-group-name>
```

### <a name="set-default-character-encoding"></a>Állítsa be alapértelmezett forráskarakter-kódolás

Az Azure Portalon alatt **Alkalmazásbeállítások** a webalkalmazást, hozzon létre egy új alkalmazásbeállítást nevű `JAVA_OPTS` értékkel `$JAVA_OPTS -Dfile.encoding=UTF-8`.

Másik lehetőségként konfigurálnia az alkalmazásbeállítást, az App Service-Maven bővítménnyel. A beállítás nevét és értékét címkék hozzáadása a beépülő modul konfiguráció:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>$JAVA_OPTS -Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

## <a name="secure-applications"></a>Alkalmazások védelme

Az App Service Linux rendszeren futó Java-alkalmazások rendelkeznek ugyanazokat a [ajánlott biztonsági eljárások](/azure/security/security-paas-applications-using-app-services) más alkalmazásokként. 

### <a name="authenticate-users"></a>Felhasználók hitelesítése

Az Azure Portalon, az alkalmazás-hitelesítés beállítása a **hitelesítési és engedélyezési** lehetőséget. Itt engedélyezheti a hitelesítés az Azure Active Directory vagy a közösségi bejelentkezések például Facebook, Google vagy a GitHub használatával. Az Azure portál beállításai csak akkor működik, egy egyetlen hitelesítési szolgáltatót konfigurálásakor. További információkért lásd: [konfigurálása az App Service-alkalmazás Azure Active Directory-bejelentkezés használatához](../configure-authentication-provider-aad.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) és az egyéb identitás-szolgáltatóktól kapcsolódó cikkeket.

Ha több bejelentkezési szolgáltatók engedélyeznie kell, kövesse az utasításokat a a [testre szabhatja az App Service-hitelesítés](../app-service-authentication-how-to.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) cikk.

 Spring Boot fejlesztők használhatják a [Azure Active Directory Spring Boot starter](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) ismerős Spring biztonsági jegyzetek és API-kat használó alkalmazások biztonságossá tételéhez.

### <a name="configure-tlsssl"></a>A TLS/SSL konfigurálása

Kövesse az utasításokat a [meglévő egyéni SSL-tanúsítvány kötése](../app-service-web-tutorial-custom-ssl.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) meglévő SSL-tanúsítvány feltöltéséhez, és kösse az alkalmazás tartománynév. Alapértelmezés szerint az alkalmazás továbbra is lehetővé teszi a HTTP kapcsolatok – az adott lépése az oktatóanyagban az SSL és TLS.

## <a name="configure-apm-platforms"></a>APM-platformok konfigurálása

Ez a szakasz bemutatja, hogyan csatlakozhat az Azure App Service Linux rendszeren a NewRelic és az AppDynamics alkalmazásteljesítmény-figyelési (APM) platformok az üzembe helyezett Java-alkalmazások.

[Konfigurálja a New relic-bővítménnyel](#configure-new-relic)
[AppDynamics konfigurálása](#configure-appdynamics)

### <a name="configure-new-relic"></a>Konfigurálja a New relic-bővítménnyel

1. Hozzon létre egy NewRelic fiókot [NewRelic.com](https://newrelic.com/signup)
2. Töltse le a Java ügynököt a NewRelic, fog rendelkezni a fájl nevét hasonló `newrelic-java-x.x.x.zip`.
3. Másolja a licenckulcsot, meg kell, hogy később konfigurálja az ügynököt.
4. [SSH-t az App Service-példányhoz](app-service-linux-ssh-support.md) , és hozzon létre egy új könyvtárat `/home/site/wwwroot/apm`.
5. A kicsomagolt NewRelic Java ügynököt fájlok feltöltése egy könyvtárat `/home/site/wwwroot/apm`. Az ügynök számára a fájlok kell lennie a `/home/site/wwwroot/apm/newrelic`.
6. A következő YAML-fájl módosítása `/home/site/wwwroot/apm/newrelic/newrelic.yml` és licenc helyőrző értékét cserélje le a saját licenckulcsot.
7. Az Azure Portalon keresse meg az alkalmazást az App Service-ben, és hozzon létre egy új alkalmazás-beállítás.
    - Ha az alkalmazás **Java használata**, nevű környezeti változó létrehozásához `JAVA_OPTS` értékkel `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Ha használ **Tomcat**, nevű környezeti változó létrehozásához `CATALINA_OPTS` értékkel `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Ha használ **WildFly**, a New relic-bővítménnyel dokumentációjában [Itt](https://docs.newrelic.com/docs/agents/java-agent/additional-installation/wildfly-version-11-installation-java) a Java ügynököt és a JBoss konfiguráció telepítésével kapcsolatos útmutatásért.
    - Ha már rendelkezik egy környezeti változóhoz a `JAVA_OPTS` vagy `CATALINA_OPTS`, fűzze hozzá a `javaagent` beállítást, a jelenlegi érték végén.

### <a name="configure-appdynamics"></a>AppDynamics konfigurálása

1. Hozzon létre egy AppDynamics fiókot a [AppDynamics.com](https://www.appdynamics.com/community/register/)
1. Töltse le a Java ügynököt az AppDynamics webhelyről, a fájlnév hasonló lesz. `AppServerAgent-x.x.x.xxxxx.zip`
1. [SSH-t az App Service-példányhoz](app-service-linux-ssh-support.md) , és hozzon létre egy új könyvtárat `/home/site/wwwroot/apm`.
1. A Java ügynököt fájlok feltöltése egy könyvtárat `/home/site/wwwroot/apm`. Az ügynök számára a fájlok kell lennie a `/home/site/wwwroot/apm/appdynamics`.
1. Az Azure Portalon keresse meg az alkalmazást az App Service-ben, és hozzon létre egy új alkalmazás-beállítás.
    - Használata **Java használata**, nevű környezeti változó létrehozásához `JAVA_OPTS` értékkel `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` ahol `<app-name>` az App Service neve.
    - Ha használ **Tomcat**, nevű környezeti változó létrehozásához `CATALINA_OPTS` értékkel `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` ahol `<app-name>` az App Service neve.
    - Ha használ **WildFly**, az AppDynamics dokumentációjában [Itt](https://docs.appdynamics.com/display/PRO45/JBoss+and+Wildfly+Startup+Settings) a Java ügynököt és a JBoss konfiguráció telepítésével kapcsolatos útmutatásért.

## <a name="configure-tomcat"></a>Tomcat konfigurálása

### <a name="connect-to-data-sources"></a>Kapcsolódás adatforrásokhoz

>[!NOTE]
> Ha az alkalmazás a Spring-keretrendszert vagy a Spring Boot, beállíthatja Spring adatok JPA adatbázis-kapcsolódási információt környezeti változókként [fájlban az alkalmazás Tulajdonságok]. Ezután [Alkalmazásbeállítások](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings) határozhat meg ezeket az értékeket az alkalmazás az Azure portal vagy a parancssori felület.

Ezek az utasítások érvényesek az összes adatbázis-kapcsolatok. Töltse ki a helyőrzőket a választott adatbázis illesztőprogram osztály névvel és a JAR-fájlt kell. A megadott osztálynevek és illesztőprogramok letöltése közös adatbázisok egy táblázat.

| Adatbázis   | Illesztőprogram-osztály neve                             | JDBC-illesztőprogram                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Letöltés](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Töltse le](https://dev.mysql.com/downloads/connector/j/) (válassza a "Platform független") |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Letöltés](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#available-downloads-of-jdbc-driver-for-sql-server)                                                           |

Tomcat a Java adatbázis-kapcsolat (JDBC) vagy a Java adatmegőrzés API (JPA) használatára konfigurálja, hogy először testre szabhatja a `CATALINA_OPTS` környezeti változó, a Tomcat a olvassa indításkor. Állítsa be ezeket az értékeket az Alkalmazásbeállítás keresztül a [App Service-Maven bővítménnyel](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md):

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Vagy állítsa be a környezeti változókat az Azure Portalon az "Alkalmazás beállítások" panelen.

Következő lépésként határozza meg, ha az adatforrás elérhető, több alkalmazást vagy a Tomcat servlet futó összes alkalmazás kell lennie.

#### <a name="application-level-data-sources"></a>Alkalmazásszintű adatforrások

1. Hozzon létre egy `context.xml` fájlt a `META-INF/` könyvtárat a projekthez. Hozzon létre a `META-INF/` könyvtárat, ha még nem létezik.

2. A `context.xml`, adjon hozzá egy `Context` elem az adatforrás JNDI címre mutat. Cserélje le a `driverClassName` helyőrzőt az illesztőprogram osztály neve a fenti táblázatból.

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

3. Az alkalmazás frissítése `web.xml` az alkalmazásban az adatforrás használata.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>Kiszolgálószintű megosztott erőforrások

1. Másolja ki a tartalmát `/usr/local/tomcat/conf` be `/home/tomcat/conf` az App Service Linux rendszeren az SSH használatával, ha nem rendelkezik olyan konfigurációs van már példány.
    ```
    mkdir -p /home/tomcat
    cp -a /usr/local/tomcat/conf /home/tomcat/conf
    ```

2. Adja hozzá az olyan környezet eleme a `server.xml` belül a `<Server>` elemet.

    ```xml
    <Server>
    ...
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
    ...
    </Server>
    ```

3. Az alkalmazás frissítése `web.xml` az alkalmazásban az adatforrás használata.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="finalize-configuration"></a>Konfiguráció véglegesítése

Végül a Tomcat osztályútvonal helyezze az illesztőprogram JAR-fájlok kivételével, és indítsa újra az App Service.

1. Győződjön meg arról, hogy a JDBC-illesztőprogram fájlokat is helyezheti őket a Tomcat classloader rendelkezésére állnak a `/home/tomcat/lib` könyvtár. (Létrehozza ezt a könyvtárat, ha ezt még nem létezik.) Ezeket a fájlokat feltöltheti az App Service-példányhoz, hajtsa végre az alábbi lépéseket:
    1. Az a [Cloud Shell](https://shell.azure.com), a webalkalmazás bővítményének telepítése:

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. Futtassa a következő CLI-parancsot az SSH-alagút létrehozása a helyi rendszerről az App Service-ben:

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. Az SFTP-ügyféllel a helyi bújtatás port csatlakozik és tölt fel a fájlokat, és a `/home/tomcat/lib` mappát.

    Az FTP-ügyfél segítségével azt is megteheti, töltse fel a JDBC-illesztővel. Kövesse az alábbi [vonatkozó, az FTP-hitelesítő adatok első](../deploy-configure-credentials.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

2. Ha létrehozott egy kiszolgálószintű adatforrást, az App Service Linux alkalmazás újraindítása. Alaphelyzetbe állítja a tomcat `CATALINA_HOME` való `/home/tomcat/conf` és a frissített konfigurációt használja.

## <a name="configure-wildfly-server"></a>WildFly kiszolgáló konfigurálása

[Méretezés App Service-szel](#scale-with-app-service)
[testreszabás alkalmazás kiszolgálókonfiguráció](#customize-application-server-configuration)
[modulok és a függőségek](#modules-and-dependencies)
[adatok források](#data-sources)
[üzenetkezelési szolgáltatók engedélyezése](#enable-messaging-providers)
[munkamenet felügyeleti gyorsítótár konfigurálása](#configure-session-management-caching)

### <a name="scale-with-app-service"></a>Méretezés App Service-ben

A linuxon futó App Service-ben WildFly kiszolgáló önálló módban, nem tartományi konfigurációban futtatja. Horizontális felskálázás az App Service-csomag, az egyes WildFly példányok önálló kiszolgálóként van konfigurálva.

 Az alkalmazás méretezése vízszintesen vagy függőlegesen a [szabályok méretezése](../../monitoring-and-diagnostics/monitoring-autoscale-get-started.md) , illetve [a példányszám növelése](../web-sites-scale.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

### <a name="customize-application-server-configuration"></a>Alkalmazás-kiszolgálókonfiguráció testreszabása

Webes alkalmazás példányai állapot nélküli, így minden egyes új példány indítása kell konfigurálni az alkalmazás számára szükséges Wildfly konfiguráció támogatásához indításakor.
Írhat egy indítási Bash-szkript a WildFly parancssori felület meghívásához:

- Adatforrás létrehozása
- Üzenetkezelési-szolgáltatók konfigurálása
- Más modulok és a függőségek hozzáadása a Wildfly kiszolgáló konfigurációját.

 A parancsfájl Wildfly működik-e, de az alkalmazás indítása előtt lefut. A parancsfájlt kell használnia a [JBOSS CLI](https://docs.jboss.org/author/display/WFLY/Command+Line+Interface) volat z `/opt/jboss/wildfly/bin/jboss-cli.sh` bármely konfigurációs vagy a kiszolgáló újraindítása után szükséges változtatásokat a kiszolgáló konfigurálása.

Ne használja az interaktív mód a parancssori felület Wildfly konfigurálásához. Helyette megadhat egy parancsfájlt a parancsok JBoss CLI használatával az `--file` parancsot, például:

```bash
/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli
```

Töltse fel az indítási parancsfájl `/home/site/deployments/tools` App Service-példányában. Lásd: [ebben a dokumentumban](../deploy-configure-credentials.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#userscope) hitelesítő adatait az FTP beszerzésével kapcsolatban.

Állítsa be a **indítási parancsfájl** mezőben az Azure Portalon, az indítási parancsfájl helyét például `/home/site/deployments/tools/your-startup-script.sh`.

Adja meg [Alkalmazásbeállítások](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings) átadandó környezeti változók a parancsfájl az alkalmazás konfigurációjában. Alkalmazásbeállítások tartsa meg a kapcsolati karakterláncok és egyéb titkok verziókövetés kívül az alkalmazás konfigurálásához szükséges.

### <a name="modules-and-dependencies"></a>Modulok és a függőségek

Modulok és azok függőségeit a Wildfly osztályútvonal JBoss parancssori felületén történő telepítéséhez szüksége lesz a következő fájlok létrehozása a saját címtárban. Egyes modulok és a függőségek előfordulhat, hogy kell az például JNDI elnevezési további konfigurációs vagy más API-specifikus konfigurációs, így ez a lista a következőkre lesz szüksége a legtöbb esetben egy függőségi konfigurálása minimális készletét.

- Egy [XML modul leíró](https://jboss-modules.github.io/jboss-modules/manual/#descriptors). Az XML-fájl nevét, attribútumokat és függőségeit, a modul határozza meg. Ez [module.xml példafájl](https://access.redhat.com/documentation/en-us/jboss_enterprise_application_platform/6/html/administration_and_configuration_guide/example_postgresql_xa_datasource) Postgres modul, a JAR-fájl JDBC függőség és egyéb szükséges modul függőségek meghatározása.
- Minden szükséges JAR csomagfájlfüggőségekről a modul.
- A JBoss CLI-parancsokkal, az új modul konfigurálása parancsprogram. Ez a fájl tartalmazza a parancsok a JBoss parancssori felület beállítása a függőségi használni kívánt kiszolgálót. A parancs használatával adja hozzá a modulok, az adatforrások és a üzenetkezelési szolgáltatók dokumentációért lásd [ebben a dokumentumban](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli).
- A Bash indítási szkript meghívása a JBoss parancssori Felületet, és hajtsa végre a parancsfájlt az előző lépésben. Ez a fájl lesz hajtható végre, ha az App Service-példány újraindítása, vagy ha új példányok horizontális felskálázás során felhasznált. Az indítási szkript, ahol hajthatja végre minden egyéb konfigurációt az alkalmazáshoz, a JBoss parancsokat a rendszer átadja a JBoss CLI. Legalább Ez a fájl lehet az JBoss CLI parancshoz készített parancsfájl átadása a JBoss CLI egyetlen paranccsal:

```bash
`/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli`
```

Ha a fájlok és tartalmak a modul, az alábbi lépésekkel, a modul hozzáadása a Wildfly kiszolgáló.

1. FTP-fájlok `/home/site/deployments/tools` App Service-példányában. Ez a dokumentum útmutatást talál első FTP hitelesítő adatait.
2. Az alkalmazás beállítások paneljén látható az Azure Portalon, a "Indítási parancsfájl" mezőt állítsa az indítási parancsfájl helyét például `/home/site/deployments/tools/your-startup-script.sh` .
3. Az App Service-példány újraindítása billentyűkombináció lenyomásával a **indítsa újra a** gombra a **áttekintése** szakaszában a portálon vagy az Azure CLI használatával.

### <a name="data-sources"></a>Adatforrások

Az adatforrás-kapcsolat Wildfly konfigurálásához kövesse a modulok telepítése és a függőségek szakaszban fent leírt eljárást. Követheti ugyanezeket a lépéseket minden olyan Azure-adatbázis szolgáltatás.

1. Az adatbázis íz a JDBC-illesztőprogram letöltése. Az egyszerűség kedvéért az alábbiakban a illesztőprogramokat [Postgres](https://jdbc.postgresql.org/download.html) és [MySQL](https://dev.mysql.com/downloads/connector/j/). A letöltés beolvasni a .jar-fájl kicsomagolása.
2. Kövesse a lépéseket vázlat "Modul és függőségei" létrehoznia és feltöltenie az XML-modul leíró, JBoss CLI-példaszkript, indítási szkriptet és .jar JDBC-függőséget a.

A Wildfly konfigurálásáról [PostgreSQL](https://developer.jboss.org/blogs/amartin-blog/2012/02/08/how-to-set-up-a-postgresql-jdbc-driver-on-jboss-7) , [MySQL](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#Using_other_Databases-Using_MySQL_as_the_Default_DataSource), és [SQL Database](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#d0e3898) érhető el. Ezeket testre szabott útmutatót, valamint a fent az általános megközelítés segítségével adatforrás-definíciók ad hozzá a kiszolgálóhoz.

### <a name="enable-messaging-providers"></a>Üzenetkezelési szolgáltatók engedélyezése

Service Bus üzenetkezelési módszerként használatával készített üzenet bab engedélyezése:

1. Használja a [Apache QPId JMS üzenetküldési kódtárat](https://qpid.apache.org/proton/index.html). Az alkalmazás ezt a függőséget a pom.xml (vagy más build-fájl) részeként.

2. Hozzon létre [Service Bus-erőforrások](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp). Hozzon létre egy Azure Service Bus-névtér és üzenetsor belül az adott névtérben és a egy megosztott elérési házirendet küldési, és a szolgáltatásokat.

3. Adja át a megosztott elérési házirend kulcsa a programkód ehhez az URL-Címének kódolása vagy a szabályzat az elsődleges kulcs vagy [a Service Bus SDK](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp#setup-jndi-context-and-configure-the-connectionfactory).

4. A JMS szolgáltató a modul XML leíró, .jar függőségek, a JBoss CLI-parancsok és indítási szkriptet a modulok telepítése és a függőségek szakaszban leírt lépésekkel. A négy fájlok mellett is szüksége lesz a JMS üzenetsorokkal és JNDI nevét definiáló XML-fájl létrehozásához. Lásd: [tárházhoz](https://github.com/JasonFreeberg/widlfly-server-configs/tree/master/appconfig) referencia konfigurációs fájlok.

### <a name="configure-session-management-caching"></a>Munkamenet-kezelés gyorsítótár konfigurálása

Alapértelmezés szerint a linuxon futó App Service használja munkamenet-affinitási cookie-kat, hogy az, hogy a meglévő munkameneteket ügyfélkérelmek vannak irányítva az alkalmazás példányát. Ez az alapértelmezett viselkedés nem igényel konfigurálást, de vannak bizonyos korlátai:

- Ha egy alkalmazáspéldány újraindítják, vagy horizontálisan, a kiszolgáló a felhasználói munkamenet-állapot elvesznek.
- Ha alkalmazások hosszú munkamenet időtúllépés beállításai vagy a felhasználók rögzített számú, új példányok terhelést kap, mivel csak az új munkamenetek a rendszer átirányítja az újonnan elindított példányok maximumára egy kis ideig is eltarthat.

Beállíthatja, hogy egy külső állapotszolgáltatót tároló használandó Wildfly [Azure Cache redis](/azure/azure-cache-for-redis/). Kell [tiltsa le a meglévő ARR-példány affinitás](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) konfigurációját, és kapcsolja ki a munkamenetek cookie-alapú útválasztást, és beavatkozás nélkül konfigurált Wildfly munkamenet áruház engedélyezése.

## <a name="docker-containers"></a>Docker-tárolók

Szeretné használni az Azure által támogatott Zulu JDK a tárolókban, ügyeljen arra, hogy lekéréséhez, és használja az előre elkészített rendszerképek a dokumentált módon a [Azul Zulu Enterprise támogatott Azure letöltési oldalát](https://www.azul.com/downloads/azure-only/zulu/) vagy használja a `Dockerfile` példákat a a[Microsoft Java GitHub-adattárat](https://github.com/Microsoft/java/tree/master/docker).

## <a name="statement-of-support"></a>Rendszerállapot-támogatás

### <a name="runtime-availability"></a>Futásidejű rendelkezésre állását.

App Service Linux rendszeren Java-webalkalmazások felügyelt üzemeltetési két modulok támogatja:

- A [Tomcat-szervlet tároló](https://tomcat.apache.org/) csomagolt alkalmazások futtatásához, web archive-(WAR-) fájlok. Támogatott verziók a következők: 8.5 és 9.0-s.
- Java használata futtatókörnyezetének futó alkalmazások a csomagolt Java archiválására (JAR) fájlokat. Támogatott verziók a következők Java 8- és 11.

### <a name="jdk-versions-and-maintenance"></a>JDK-verziók és karbantartás

Az Azure támogatott Java fejlesztői készlet (JDK) van [Zulu](https://www.azul.com/downloads/azure-only/zulu/) keresztül [Azul Systems](https://www.azul.com/).

Főverzió-frissítései linuxos Azure App Service-ben új futásidejű beállítások keresztül biztosítunk. Ügyfelek frissítése a Java ezen újabb verziói az alkalmazásszolgáltatás üzemelő példányának konfigurálásával, és felelős tesztelése, és biztosítja a fő frissítés megfelel az igényeiknek.

Támogatott segítségével negyedévente automatikusan javítani a januárban, áprilisban, júliusban és minden év október a.

### <a name="security-updates"></a>Biztonsági frissítések

Javítások és javításokat tartalmaz olyan súlyos biztonsági réseket elérhető lesz, amint az Azul Systems elérhetővé válnak. "Nagyobb" biztonsági rés alapján pontot 9.0-s vagy újabb a [NIST közös biztonsági rés pontozási rendszert, 2. verzió](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>És a használatból való kivonást egyaránt

Ha egy támogatott Java-futtatókörnyezet megszűnik, az érintett modul használatával Azure-fejlesztők számára kap elavulással kapcsolatos figyelmeztetés legalább hat hónapos előtt a futtatókörnyezet kivonják a forgalomból.

### <a name="local-development"></a>Helyi fejlesztés

A fejlesztők is letölthetik a éles kiadása az Azul Zulu Enterprise JDK a helyi fejlesztési [Azul a letöltési hely](https://www.azul.com/downloads/azure-only/zulu/).

### <a name="development-support"></a>Fejlesztés támogatása

Terméktámogatási a [Azure által támogatott Azul Zulu JDK](https://www.azul.com/downloads/azure-only/zulu/) érhető el, az Azure fejlesztésének vagy [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) együtt egy [minősített Azure-támogatási csomag](https://azure.microsoft.com/support/plans/).

### <a name="runtime-support"></a>Podpora modulu Runtime

A fejlesztők is [nyissa meg a probléma](/azure/azure-supportability/how-to-create-azure-support-request) az Azure-támogatási, ha rendelkezik a Azul Zulu segítségével egy [minősített támogatási csomag](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>További lépések

Látogasson el a [Azure Java-fejlesztőknek készült](/java/azure/) center az Azure gyors útmutatók, oktatóanyagok és Java-dokumentáció található.

Általános kérdések linuxos App Service használatával kapcsolatban, amelyek nem a Java fejlesztési adott válaszok a [App Service Linux – gyakori kérdések](app-service-linux-faq.md).