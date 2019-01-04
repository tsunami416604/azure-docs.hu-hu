---
title: Java fejlesztői útmutatója az App Service linuxon – Azure |} A Microsoft Docs
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
ms.date: 12/10/2018
ms.author: routlaw
ms.custom: seodec18
ms.openlocfilehash: 6f6dac37d1114e8a9faa16c07fd5c14a90a5b0fb
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2019
ms.locfileid: "53976732"
---
# <a name="java-developers-guide-for-app-service-on-linux"></a>A linuxon futó App Service-hez Java fejlesztői útmutatója

Linuxon futó Azure App Service lehetővé teszi, hogy gyorsan elkészítheti, telepítheti és méretezheti a Tomcat a Java-fejlesztőknek vagy a Java Standard Edition (SE) egy teljes körűen felügyelt Linux-alapú szolgáltatás webes alkalmazások csomagolva. A parancssorból vagy a-szerkesztők, mint például az intellij-vel, az Eclipse vagy a Visual Studio Code Maven beépülő modulok az alkalmazások központi telepítése.

Ez az útmutató a főbb fogalmakat és a Linux App Service-ben használata Java-fejlesztőknek készült utasításokat tartalmaz. Ha soha nem használta az Azure App Service Linux, olvassa végig a [Java rövid](quickstart-java.md) első. Általános kérdések linuxos App Service használatával kapcsolatban, amelyek nem a Java fejlesztési adott válaszok a [App Service Linux – gyakori kérdések](app-service-linux-faq.md).

## <a name="logging-and-debugging-apps"></a>Bejelentkezés és hibakeresés alkalmazások

Teljesítményjelentések készítésére, forgalom Vizualizációk és egészségügyi checkups eeach alkalmazás az Azure Portalon keresztül érhetők el. Tekintse meg a [diagnosztikai áttekintése az Azure App Service](/azure/app-service/overview-diagnostics) eléréséhez, és ezek a diagnosztikai eszközök használatával kapcsolatban.

## <a name="application-performance-monitoring"></a>Alkalmazásteljesítmény-figyelés

Lásd: [az alkalmazásteljesítmény-figyelés Java-alkalmazások a Linuxon futó Azure App Service-eszközöket](how-to-java-apm-monitoring.md) útmutatókra New Relic és az AppDynamics konfigurálása az App Service Linux rendszeren futó Java alkalmazásokat.

### <a name="ssh-console-access"></a>SSH hozzáféréséhez a konzolhoz 

A Linuxos környezetben az alkalmazást futtató SSH-kapcsolatának áll rendelkezésre áll-e. Lásd: [SSH-támogatás a Linuxon futó Azure App Service](/azure/app-service/containers/app-service-linux-ssh-support) teljes körű útmutatás a webböngészőt vagy a helyi terminálban a Linux rendszerhez való csatlakozáshoz.

### <a name="streaming-logs"></a>Folyamatos átviteli naplók

Gyorsan Hibakeresés és hibaelhárítás céljából, naplók streamelheti a konzol az Azure CLI használatával. A CLI-t konfigurálhatja a `az webapp log config` naplózásának engedélyezése:

```azurecli-interactive
az webapp log config --name ${WEBAPP_NAME} \
 --resource-group ${RESOURCEGROUP_NAME} \
 --web-server-logging filesystem
```

A konzol használatával, naplók folyamatos átvitele majd `az webapp log tail`:

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```

További információkért lásd: [folyamatos átviteli naplók az Azure CLI-vel](../troubleshoot-diagnostic-logs.md#streaming-with-azure-cli).

### <a name="app-logging"></a>Alkalmazás-naplózás

Engedélyezése [alkalmazásnaplózás](/azure/app-service/troubleshoot-diagnostic-logs#enablediag) az Azure Portalon keresztül vagy [Azure CLI-vel](/cli/azure/webapp/log#az-webapp-log-config) App Service-ben az alkalmazás normál konzolkimenet és standard szintű konzol hibaadatfolyamok írni a helyi konfigurálása fájlrendszer- vagy Azure Blob Storage. Az App Service helyi fájlrendszer naplózása példány le van tiltva 12 óra után van konfigurálva. Ha hosszabb adatmegőrzés megadásához, az alkalmazás kiírhatja a kimenetet egy Blob storage-tároló konfigurálása.

Ha az alkalmazás [Logback](https://logback.qos.ch/) vagy [Log4j](https://logging.apache.org/log4j) nyomkövetés, is továbbíthatja, tekintse át az Azure Application Insights a nyomkövetések utasításai a naplózási keretrendszer konfigurációs [Ismerkedés a Java-nyomkövetési naplókat az Application Insights](/azure/application-insights/app-insights-java-trace-logs). 

## <a name="customization-and-tuning"></a>Testreszabás és hangolás

A box finomhangolásához és testreszabása az Azure Portal és CLI támogatja az Azure App Service Linux rendszeren. Tekintse át a Java meghatározott webes alkalmazások konfigurálása az alábbi cikkeket:

- [App Service szolgáltatás beállításainak konfigurálása](/azure/app-service/web-sites-configure?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Egyéni tartomány beállítása](/azure/app-service/app-service-web-tutorial-custom-domain?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Enable SSL](/azure/app-service/app-service-web-tutorial-custom-ssl?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [CDN hozzáadása](/azure/cdn/cdn-add-to-web-app?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)

### <a name="set-java-runtime-options"></a>Java-futtatókörnyezet beállításainak megadása

A Tomcat és a Java használata környezetben állítsa a lefoglalt memória vagy más JVM futásidejű beállítások, állítsa be a JAVA_OPTS, mint az alább látható módon egy [Alkalmazásbeállítás](/azure/app-service/web-sites-configure#app-settings). Az App Service Linux továbbítja ezt a beállítást környezeti változóban a Java-futtatókörnyezet indításakor.

Az Azure Portalon alatt **Alkalmazásbeállítások** a webalkalmazást, hozzon létre egy új alkalmazásbeállítást nevű `JAVA_OPTS` , amely tartalmazza a további beállításokat, például `$JAVA_OPTS -Xms512m -Xmx1204m`.

A alkalmazás beállításai az Azure App Service Linux Maven beépülő modul konfigurálásához beállításérték/címkéket adhat hozzá, az Azure beépülő modul szakaszban. Az alábbi példa egy adott minimális és maximális Java heapsize állítja be:

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
az webapp config set -n ${WEBAPP_NAME} -g ${WEBAPP_RESOURCEGROUP_NAME} --web-sockets-enabled true 
```

Indítsa újra az alkalmazást:

```azurecli-interactive
az webapp stop -n ${WEBAPP_NAME} -g ${WEBAPP_RESOURCEGROUP_NAME} 
az webapp start -n ${WEBAPP_NAME} -g ${WEBAPP_RESOURCEGROUP_NAME}  
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

Az Azure Portalon, az alkalmazás-hitelesítés beállítása a **hitelesítési és engedélyezési** lehetőséget. Itt engedélyezheti a hitelesítés az Azure Active Directory vagy a közösségi bejelentkezések például Facebook, Google vagy a GitHub használatával. Az Azure portál beállításai csak akkor működik, egy egyetlen hitelesítési szolgáltatót konfigurálásakor.  További információkért lásd: [konfigurálása az App Service-alkalmazás Azure Active Directory-bejelentkezés használatához](/azure/app-service/configure-authentication-provider-aad) és az egyéb identitás-szolgáltatóktól kapcsolódó cikkeket.

Ha több bejelentkezési szolgáltatók engedélyeznie kell, kövesse az utasításokat a a [testre szabhatja az App Service-hitelesítés](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to) cikk.

 Spring Boot fejlesztők használhatják a [Azure Active Directory Spring Boot starter](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) ismerős Spring biztonsági jegyzetek és API-kat használó alkalmazások biztonságossá tételéhez.

### <a name="configure-tlsssl"></a>A TLS/SSL konfigurálása

Kövesse az utasításokat a [meglévő egyéni SSL-tanúsítvány kötése](/azure/app-service/app-service-web-tutorial-custom-ssl) meglévő SSL-tanúsítvány feltöltéséhez, és kösse az alkalmazás tartománynév. Alapértelmezés szerint az alkalmazás továbbra is lehetővé teszi a HTTP kapcsolatok – az adott lépése az oktatóanyagban az SSL és TLS.

## <a name="tomcat"></a>Tomcat 

### <a name="connecting-to-data-sources"></a>Kapcsolódás az adatforrásokhoz

>[!NOTE]
> Ha az alkalmazás a Spring-keretrendszert vagy a Spring Boot, beállíthatja Spring adatok JPA adatbázis-kapcsolódási információt környezeti változókként [fájlban az alkalmazás Tulajdonságok]. Ezután [Alkalmazásbeállítások](/azure/app-service/web-sites-configure#app-settings) határozhat meg ezeket az értékeket az alkalmazás az Azure portal vagy a parancssori felület.

Ezek az utasítások érvényesek az összes adatbázis-kapcsolatok. Töltse ki a helyőrzőket a választott adatbázis illesztőprogram osztály névvel és a JAR-fájlt kell. A megadott osztálynevek és illesztőprogramok letöltése közös adatbázisok egy táblázat.

| Adatbázis   | Illesztőprogram-osztály neve                             | JDBC-illesztőprogram                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Drvier`                        | [Letöltés](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Töltse le](https://dev.mysql.com/downloads/connector/j/) (válassza a "Platform független") |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Letöltés](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#available-downloads-of-jdbc-driver-for-sql-server)                                                           |

Tomcat a Java adatbázis-kapcsolat (JDBC) vagy a Java adatmegőrzés API (JPA) használatára konfigurálja, hogy először testre szabhatja a `CATALINA_OPTS` környezeti változó, Tomcat indításakor a olvassa be. Állítsa be ezeket az értékeket az Alkalmazásbeállítás keresztül a [App Service-Maven bővítménnyel](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md):

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

#### <a name="for-application-level-data-sources"></a>Alkalmazásszintű adatforrások: 

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

#### <a name="for-shared-server-level-resources"></a>A kiszolgálói szintű közös erőforrások:

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

#### <a name="finally-place-the-driver-jars-in-the-tomcat-classpath-and-restart-your-app-service"></a>Végül a Tomcat osztályútvonal helyezze az illesztőprogram JAR-fájlok kivételével, és indítsa újra az App Service: 

1. Győződjön meg arról, hogy a JDBC-illesztőprogram fájlokat is helyezheti őket a Tomcat classloader rendelkezésére állnak a `/home/tomcat/lib` könyvtár. (Létrehozza ezt a könyvtárat, ha ezt még nem létezik.) Ezeket a fájlokat feltöltheti az App Service-példányhoz, hajtsa végre az alábbi lépéseket:  
    1. Az Azure App Service-webpp bővítményének telepítése:

      ```azurecli-interactive
      az extension add –name webapp
      ```

    2. Futtassa a következő CLI-parancsot hozhat létre egy SSH-alagutat a helyi rendszerről, App Service-ben:

      ```azurecli-interactive
      az webapp remote-connection create –g [resource group] -n [app name] -p [local port to open]
      ```

    3. Az SFTP-ügyféllel a helyi bújtatás port csatlakozik és tölt fel a fájlokat, és a `/home/tomcat/lib` mappát.

    Az FTP-ügyfél segítségével azt is megteheti, töltse fel a JDBC-illesztővel. Kövesse az alábbi [vonatkozó, az FTP-hitelesítő adatok első](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials).

2. Ha létrehozott egy kiszolgálószintű adatforrást, az App Service Linux alkalmazás újraindítása. Alaphelyzetbe állítja a tomcat `CATALINA_HOME` való `/home/tomcat/conf` és a frissített konfigurációt használja.

## <a name="docker-containers"></a>Docker-tárolók

Szeretné használni az Azure által támogatott Zulu JDK a tárolókban, ügyeljen arra, hogy lekéréséhez, és használja az előre elkészített rendszerképek a dokumentált módon a [Azul Zulu Enterprise támogatott Azure letöltési oldalát](https://www.azul.com/downloads/azure-only/zulu/) vagy használja a `Dockerfile` példákat a a[Microsoft Java GitHub-adattárat](https://github.com/Microsoft/java/tree/master/docker).

## <a name="runtime-availability-and-statement-of-support"></a>Futásidejű rendelkezésre állását és a rendszerállapot-támogatás

App Service Linux rendszeren Java-webalkalmazások felügyelt üzemeltetési két modulok támogatja:

- A [Tomcat-szervlet tároló](https://tomcat.apache.org/) csomagolt alkalmazások futtatásához, web archive-(WAR-) fájlok. Támogatott verziók a következők: 8.5 és 9.0-s.
- Java használata futtatókörnyezetének futó alkalmazások a csomagolt Java archiválására (JAR) fájlokat. Az egyetlen támogatott főbb verzió Java 8.

## <a name="java-runtime-statement-of-support"></a>Java runtime rendszerállapot-támogatás 

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
