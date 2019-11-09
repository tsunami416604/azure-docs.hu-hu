---
title: Linuxos Java-alkalmazások konfigurálása – Azure App Service | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhatja a Linuxon Azure App Service futó Java-alkalmazásokat.
keywords: Azure app Service, webalkalmazás, Linux, OSS, Java, Java EE, JavaEE
services: app-service
author: bmitchell287
manager: barbkess
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 06/26/2019
ms.author: brendm
ms.custom: seodec18
ms.openlocfilehash: 8f6fb9737d3d8dad93a95f31d566f7cc4706ded3
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2019
ms.locfileid: "73886052"
---
# <a name="configure-a-linux-java-app-for-azure-app-service"></a>Linuxos Java-alkalmazás konfigurálása Azure App Servicehoz

A Linuxon Azure App Service lehetővé teszi a Java-fejlesztők számára, hogy a Tomcat vagy Java Standard Edition (SE) csomagban lévő webalkalmazásokat egy teljes körűen felügyelt Linux-alapú szolgáltatáson keresztül gyorsan építsék, telepítsék és méretezhetik. Alkalmazásokat telepíthet a Maven beépülő modulokból a parancssorból vagy olyan szerkesztőkből, mint például a IntelliJ, az Eclipse vagy a Visual Studio code.

Ez az útmutató olyan Java-fejlesztőknek nyújt főbb fogalmakat és útmutatást, amelyek a App Service beépített Linux-tárolóját használják. Ha még soha nem használta a Azure App Servicet, először kövesse a [Java](quickstart-java.md) gyors üzembe helyezést és a [Java-t a PostgreSQL oktatóanyaggal](tutorial-java-enterprise-postgresql-app.md) .

## <a name="deploying-your-app"></a>Az alkalmazás üzembe helyezése

A [Maven beépülő modult használhatja Azure app Service](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) a. jar és a. War fájlok telepítéséhez. A népszerű ide-val történő üzembe helyezést [Azure Toolkit for IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij) vagy [Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse)is támogatja.

Ellenkező esetben a telepítési módszer az archiválás típusától függ:

- A. War fájlok Tomcatbe való üzembe helyezéséhez használja a `/api/wardeploy/` végpontot az archív fájl KÖZZÉTÉTELéhez. Az API-val kapcsolatos további információkért tekintse meg [ezt a dokumentációt](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file).
- A. jar fájlok Java SE-lemezképeken való üzembe helyezéséhez használja a kudu webhely `/api/zipdeploy/` végpontját. Az API-val kapcsolatos további információkért tekintse meg [ezt a dokumentációt](https://docs.microsoft.com/azure/app-service/deploy-zip#rest).

Ne telepítse a. War vagy a. jar fájlt FTP használatával. Az FTP-eszköz indítási parancsfájlok, függőségek vagy más futásidejű fájlok feltöltésére szolgál. A webalkalmazások üzembe helyezése nem optimális megoldás.

## <a name="logging-and-debugging-apps"></a>Alkalmazások naplózása és hibakeresése

A teljesítménnyel kapcsolatos jelentések, a forgalmi vizualizációk és az állapot-kivizsgálások a Azure Portalon keresztül érhetők el az egyes alkalmazásokhoz. További információ: [Azure app Service diagnosztika áttekintése](../overview-diagnostics.md).

### <a name="ssh-console-access"></a>SSH-konzolhoz való hozzáférés

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>Diagnosztikai naplók streamelése

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

További információ: [stream-naplók Cloud Shellban](../troubleshoot-diagnostic-logs.md#in-cloud-shell).

### <a name="app-logging"></a>Alkalmazás naplózása

Az Azure Portal vagy az [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) -n keresztül történő [alkalmazás-naplózás](../troubleshoot-diagnostic-logs.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#enable-application-logging-windows) engedélyezésével beállíthatja, hogy a app Service az alkalmazás szabványos konzoljának kimenetét és standard konzoljának hibáit a helyi fájlrendszerbe vagy az Azure-Blob Storageba írja. A helyi App Service filesystem-példányra való naplózás a konfigurálás után 12 órával le van tiltva. Ha nagyobb adatmegőrzésre van szüksége, konfigurálja úgy az alkalmazást, hogy egy blob Storage-tárolóba írja a kimenetet. A Java-és a Tomcat-alkalmazás naplói a */Home/LogFiles/Application/* könyvtárban találhatók.

Ha az alkalmazás [Logback](https://logback.qos.ch/) vagy [Log4j](https://logging.apache.org/log4j) használ a nyomkövetéshez, ezeket a nyomkövetéseket áttekintheti az Azure [Application Insights-ba Application Insights ](/azure/application-insights/app-insights-java-trace-logs).

### <a name="troubleshooting-tools"></a>Hibaelhárítási eszközök

A beépített Java-lemezképek az [alpesi Linux](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) operációs rendszeren alapulnak. A `apk` csomagkezelő segítségével telepítheti a hibaelhárítási eszközöket és parancsokat.

### <a name="flight-recorder"></a>Flight Recorder

A App Service összes linuxos Java-rendszerképe telepítve van a Zulu Flight Recorder használatával, így egyszerűen csatlakozhat a JVM, és megkezdheti a Profiler-rögzítést, vagy létrehozhat egy halom-memóriaképet.

#### <a name="timed-recording"></a>Időzített rögzítés

Az első lépésekhez használja az SSH-t a App Service, majd futtassa a `jcmd` parancsot, és tekintse meg az összes futó Java-folyamat listáját. A jcmd mellett egy folyamat-azonosító számmal (PID) rendelkező Java-alkalmazást is látnia kell.

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

A 30 másodperces intervallumban ellenőrizheti, hogy a rögzítés a `jcmd 116 JFR.check`futtatásával zajlik-e. Ez a művelet megjeleníti a megadott Java-folyamat összes felvételét.

#### <a name="continuous-recording"></a>Folyamatos rögzítés

A Zulu Flight Recorder segítségével folyamatosan, a Java-alkalmazásait a futtatási teljesítményre ([forrás](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf)) gyakorolt minimális hatású profilt használhatja. Ehhez futtassa az alábbi Azure CLI-parancsot egy JAVA_OPTS nevű alkalmazás-beállítás létrehozásához a szükséges konfigurációval. Az alkalmazás elindításakor a rendszer átadja a JAVA_OPTS Alkalmazásbeállítások tartalmát az `java` parancsnak.

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

A rögzítés megkezdése után a `JFR.dump` parancs használatával bármikor kitörölheti az aktuális rögzítési adataikat.

```shell
jcmd <pid> JFR.dump name=continuous_recording filename="/home/recording1.jfr"
```

További információkért tekintse meg a [Jcmd-parancs referenciáját](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190).

### <a name="analyzing-recordings"></a>Felvételek elemzése

A [FTPS](../deploy-ftp.md) használatával töltse le a JFR-fájlt a helyi gépre. A JFR-fájl elemzéséhez töltse le és telepítse a [Zulu Mission Controlt](https://www.azul.com/products/zulu-mission-control/). A Zulu-feladatok ellenőrzésével kapcsolatos utasításokért tekintse meg az [Azul dokumentációját](https://docs.azul.com/zmc/) és a [telepítési utasításokat](https://docs.microsoft.com/java/azure/jdk/java-jdk-flight-recorder-and-mission-control).

## <a name="customization-and-tuning"></a>Testreszabás és Finomhangolás

A Linux rendszerhez készült Azure App Service a Azure Portal és a CLI használatával támogatja a Box finomhangolását és testreszabását. Tekintse át a következő cikkeket a nem Java-specifikus webalkalmazás-konfigurációhoz:

- [Alkalmazásbeállítások konfigurálása](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)
- [Egyéni tartomány beállítása](../app-service-web-tutorial-custom-domain.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [SSL-kötések konfigurálása](../configure-ssl-bindings.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [CDN hozzáadása](../../cdn/cdn-add-to-web-app.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [A kudu hely konfigurálása](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>Java-futtatókörnyezet beállításainak megadása

Ha a Tomcat és a Java SE környezetekben lévő lefoglalt memóriát vagy egyéb JVM-futtatókörnyezetet is meg szeretné határozni, hozzon létre egy `JAVA_OPTS` nevű [alkalmazás-beállítást](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) a beállításokkal. App Service a Linux indításakor ezt a beállítást környezeti változóként adja át a Java futtatókörnyezetnek.

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

Ha JAR-alkalmazást helyez üzembe, akkor az *app. jar* névvel kell rendelkeznie ahhoz, hogy a beépített lemezkép helyesen tudja azonosítani az alkalmazást. (A Maven beépülő modul automatikusan átnevezi ezt.) Ha nem kívánja átnevezni a JAR-t az *app. jar*névre, feltölthet egy rendszerhéj-parancsfájlt a PARANCCSAL a jar futtatásához. Ezután illessze be a parancsfájl teljes elérési útját az [indítási fájl](app-service-linux-faq.md#built-in-images) szövegmezőbe a portál konfiguráció szakaszában. Az indítási parancsfájl nem azon a címtáron fut, amelybe el van helyezve. Ezért mindig abszolút elérési utakat használjon az indítási parancsfájlban található fájlokra (például: `java -jar /home/myapp/myapp.jar`).

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

### <a name="adjust-startup-timeout"></a>Indítási Időtúllépés beállítása

Ha a Java-alkalmazása különösen nagy, növelje az indítási időkorlátot. Ehhez hozzon létre egy alkalmazás-beállítást, `WEBSITES_CONTAINER_START_TIME_LIMIT` és állítsa be azt a másodpercek számát, ameddig az időtúllépés előtt meg App Service várnia kell. A maximális érték `1800` másodperc.

### <a name="pre-compile-jsp-files"></a>JSP-fájlok előre fordítása

A Tomcat-alkalmazások teljesítményének növelése érdekében lefordíthatja a JSP-fájljait, mielőtt telepítené a App Service. Használhatja az Apache parittya által biztosított [Maven beépülő modult](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) , vagy használja ezt az [Ant-Build fájlt](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation).

## <a name="secure-applications"></a>Biztonságos alkalmazások

A Linux App Service-on futó Java-alkalmazásokhoz ugyanaz a [biztonsági eljárások](/azure/security/security-paas-applications-using-app-services) tartoznak, mint más alkalmazások.

### <a name="authenticate-users-easy-auth"></a>Felhasználók hitelesítése (egyszerű hitelesítés)

Az alkalmazás hitelesítésének beállítása a Azure Portal a **hitelesítés és engedélyezés** lehetőséggel. Itt engedélyezheti a hitelesítést Azure Active Directory vagy közösségi bejelentkezéssel, például a Facebook, a Google vagy a GitHub használatával. Azure Portal konfiguráció csak egyetlen hitelesítési szolgáltató konfigurálásakor működik. További információ: [a app Service alkalmazás konfigurálása Azure Active Directory bejelentkezési adatok használatára](../configure-authentication-provider-aad.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) és a kapcsolódó cikkek más identitás-szolgáltatóknak való használatára. Ha több bejelentkezési szolgáltatót is engedélyeznie kell, kövesse az [app Service-hitelesítés testreszabása](../app-service-authentication-how-to.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) című cikk utasításait.

#### <a name="tomcat-and-wildfly"></a>Tomcat és Wildfly

A Tomcat-vagy Wildfly-alkalmazás közvetlenül a servletből fér hozzá a felhasználó jogcímeihez, ha a fő objektumot egy Térkép objektummá helyezi. A Térkép objektum az egyes jogcím-típusokat az adott típusú jogcímek gyűjteményéhez rendeli. Az alábbi kódban `request` a `HttpServletRequest`egy példánya.

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

#### <a name="spring-boot"></a>Spring Boot

A Spring boot-fejlesztők a [Azure Active Directory Spring boot Starter](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) használatával biztosíthatják az alkalmazásokat az ismerős rugós biztonsági megjegyzésekkel és API-kkal. Ügyeljen arra, hogy növelje a fejléc maximális méretét az *Application. properties* fájlban. Javasoljuk, hogy `16384`értékét.

### <a name="configure-tlsssl"></a>A TLS/SSL konfigurálása

A meglévő SSL-tanúsítvány feltöltéséhez és az alkalmazás tartománynevéhez való kötéséhez kövesse az [Egyéni DNS-név biztonságossá tétele SSL-kötéssel Azure app Serviceban](../configure-ssl-bindings.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) található utasításokat. Alapértelmezés szerint az alkalmazás továbbra is engedélyezi a HTTP-kapcsolatokat – az oktatóanyag adott lépéseinek végrehajtásával kényszerítheti az SSL és a TLS használatát.

### <a name="use-keyvault-references"></a>Kulcstartó-hivatkozások használata

Az [Azure](../../key-vault/key-vault-overview.md) kulcstartó központosított titkos felügyeletet biztosít a hozzáférési házirendekkel és a naplózási előzményekkel. A kulcsok (például jelszavak vagy kapcsolati karakterláncok) a kulcstartóban tárolhatók, és a titkos kulcsokat az alkalmazásban környezeti változókon keresztül érhetik el.

Először is kövesse az alkalmazáshoz [való hozzáférés megadására](../app-service-key-vault-references.md#granting-your-app-access-to-key-vault) vonatkozó utasításokat, és a [titkos kulcshoz tartozó kulcstároló-hivatkozást egy Alkalmazásbeállítások alapján](../app-service-key-vault-references.md#reference-syntax)végezze el a Key Vault. Ellenőrizheti, hogy a hivatkozás feloldja-e a titkos kulcsot a környezeti változó kinyomtatásával, miközben távolról fér hozzá a App Service terminálhoz.

Ha ezeket a titkokat be szeretné szúrni a Spring vagy a Tomcat konfigurációs fájljába, használja a környezeti változók injekciós szintaxisát (`${MY_ENV_VAR}`). A Spring konfigurációs fájlok esetében tekintse meg ezt a dokumentációt a [külső konfigurációkról](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).

## <a name="using-the-java-key-store"></a>A Java Key Store használata

Alapértelmezés szerint a rendszer a [app Service Linuxra feltöltött](../configure-ssl-certificate.md) nyilvános vagy privát tanúsítványokat a tároló indításakor betölti a Java Key Store-ba. Ez azt jelenti, hogy a feltöltött tanúsítványok a kapcsolati környezetben lesznek elérhetők a kimenő TLS-kapcsolatok létrehozásakor.

A Java-kulcs eszköz interakcióját vagy hibakeresését úgy teheti meg, hogy [megnyit egy SSH-kapcsolatot](app-service-linux-ssh-support.md) a app Service és futtatja a parancsot `keytool`. A parancsok listáját a [legfontosabb eszköz dokumentációjában](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) találja. A tanúsítványokat a Java alapértelmezett tároló-fájljának helye tárolja, `$JAVA_HOME/jre/lib/security/cacerts`.

A JDBC-kapcsolatok titkosításához további konfigurálásra lehet szükség. Tekintse meg a kiválasztott JDBC-illesztőprogram dokumentációját.

- [PostgreSQL](https://jdbc.postgresql.org/documentation/head/ssl-client.html)
- [SQL Server](https://docs.microsoft.com/sql/connect/jdbc/connecting-with-ssl-encryption?view=sql-server-ver15)
- [MySQL](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-reference-using-ssl.html)

### <a name="manually-initialize-and-load-the-key-store"></a>A Key Store manuális inicializálása és betöltése

A kulcstárolót inicializálhatja, és manuálisan is hozzáadhat tanúsítványokat. Hozzon létre egy alkalmazás-beállítást, `SKIP_JAVA_KEYSTORE_LOAD`a `1` értékkel, hogy letiltsa App Service a tanúsítványok automatikus betöltését a Key Store-ba. Az Azure Portalon App Service feltöltött nyilvános tanúsítványokat a rendszer a `/var/ssl/certs/`alatt tárolja. A privát tanúsítványokat a `/var/ssl/private/`alatt tárolja.

A Webáruház API-val kapcsolatos további információkért tekintse meg [a hivatalos dokumentációt](https://docs.oracle.com/javase/8/docs/api/java/security/KeyStore.html).

## <a name="configure-apm-platforms"></a>APM-platformok konfigurálása

Ez a szakasz bemutatja, hogyan csatlakoztathatók a Linux rendszeren üzembe Azure App Service helyezett Java-alkalmazások a NewRelic és a AppDynamics Application Performance monitoring (APM) platformokkal.

### <a name="configure-new-relic"></a>Új ereklye konfigurálása

1. NewRelic-fiók létrehozása a [NewRelic.com](https://newrelic.com/signup) -ben
2. Töltse le a Java-ügynököt a NewRelic-ből, mert a fájlnév a *newrelic-Java-x. x. x. zip*fájlhoz hasonló lesz.
3. A licenckulcs másolásához az ügynököt később kell konfigurálnia.
4. [SSH-t a app Service-példányba](app-service-linux-ssh-support.md) , és hozzon létre egy új címtár- */Home/site/wwwroot/APM*.
5. Töltse fel a kicsomagolt NewRelic Java-ügynök fájljait egy könyvtárba a */Home/site/wwwroot/APM*alatt. Az ügynök fájljainak a */Home/site/wwwroot/APM/newrelic*-ben kell lenniük.
6. Módosítsa a YAML fájlt a */Home/site/wwwroot/APM/newrelic/newrelic.YML* címen, és cserélje le a helyőrző licenc értékét a saját licenckulcs használatára.
7. A Azure Portal tallózással keresse meg az alkalmazást App Service és hozzon létre egy új alkalmazás-beállítást.
    - Ha az alkalmazás **Java SE**-t használ, hozzon létre egy `JAVA_OPTS` nevű környezeti változót a `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`értékkel.
    - Ha a **tomcat**-t használja, hozzon létre egy `CATALINA_OPTS` nevű környezeti változót a (z) `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`értékkel.
    - Ha a **WildFly**-t használja [, az új ereklye dokumentációjában](https://docs.newrelic.com/docs/agents/java-agent/additional-installation/wildfly-version-11-installation-java) talál útmutatást a Java-ügynök és a JBoss-konfiguráció telepítéséhez.

### <a name="configure-appdynamics"></a>AppDynamics konfigurálása

1. AppDynamics-fiók létrehozása a [AppDynamics.com](https://www.appdynamics.com/community/register/) -ben
2. Töltse le a Java-ügynököt a AppDynamics webhelyről, és a fájlnév a *AppServerAgent-x. x. x. xxxxx. zip* fájlhoz hasonló lesz.
3. [SSH-t a app Service-példányba](app-service-linux-ssh-support.md) , és hozzon létre egy új címtár- */Home/site/wwwroot/APM*.
4. Töltse fel a Java-ügynök fájljait egy könyvtárba a */Home/site/wwwroot/APM*alatt. Az ügynök fájljainak a */Home/site/wwwroot/APM/appdynamics*-ben kell lenniük.
5. A Azure Portal tallózással keresse meg az alkalmazást App Service és hozzon létre egy új alkalmazás-beállítást.
    - Ha **Java SE**-t használ, hozzon létre egy `JAVA_OPTS` nevű környezeti változót az érték `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` ahol a `<app-name>` a app Service neve.
    - Ha a **tomcat**-t használja, hozzon létre egy `CATALINA_OPTS` nevű környezeti változót az érték `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` ahol a `<app-name>` a app Service neve.
    - Ha a **WildFly**-t használja, a Java-ügynök és a JBoss-konfiguráció telepítésével kapcsolatos útmutatásért tekintse [meg a](https://docs.appdynamics.com/display/PRO45/JBoss+and+Wildfly+Startup+Settings) AppDynamics dokumentációját.

>  Ha már rendelkezik környezeti változóval `JAVA_OPTS` vagy `CATALINA_OPTS`, fűzze hozzá az `-javaagent:/...` lehetőséget az aktuális érték végéhez.

## <a name="configure-jar-applications"></a>JAR-alkalmazások konfigurálása

### <a name="starting-jar-apps"></a>JAR-alkalmazások indítása

Alapértelmezés szerint a App Service a JAR-alkalmazást *app. jar*néven várja. Ha ezt a nevet adja, a rendszer automatikusan futtatja. A Maven-felhasználók esetében beállíthatja a JAR nevét a *Pom. xml fájl*`<build>` szakaszának `<finalName>app</finalName>` belefoglalásával. [Ugyanezt a Gradle is megteheti](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.bundling.Jar.html#org.gradle.api.tasks.bundling.Jar:archiveFileName) a `archiveFileName` tulajdonság beállításával.

Ha más nevet szeretne használni a JAR számára, meg kell adnia a JAR-fájlt végrehajtó [indítási parancsot](app-service-linux-faq.md#built-in-images) is. Például: `java -jar my-jar-app.jar`. Megadhatja az indítási parancs értékét a portálon, a konfiguráció > általános beállítások területen, vagy egy `STARTUP_COMMAND`nevű alkalmazás-beállítással.

### <a name="server-port"></a>Kiszolgáló portja

App Service Linux a bejövő kérelmeket a 80-es portra irányítja, ezért az alkalmazásnak a 80-es porton is figyelnie kell. Ezt megteheti az alkalmazás konfigurációjában (például a Spring *alkalmazás. properties* fájljában) vagy az indítási parancsban (például `java -jar spring-app.jar --server.port=80`). Tekintse meg a következő dokumentációt a közös Java-keretrendszerekhez:

- [Spring boot](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-properties-and-configuration.html#howto-use-short-command-line-arguments)
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

#### <a name="shared-server-level-resources"></a>Megosztott kiszolgálói szintű erőforrások

1. Másolja a */usr/local/tomcat/conf* tartalmát a app Service */Home/tomcat/conf* -be a Linux-példányon az SSH-val, ha már van ilyen konfiguráció.

    ```bash
    mkdir -p /home/tomcat
    cp -a /usr/local/tomcat/conf /home/tomcat/conf
    ```

2. Adjon hozzá egy környezeti elemet a *Server. xml fájlhoz* a `<Server>` elemen belül.

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

3. Frissítse az alkalmazás *web. XML* fájlját az alkalmazás adatforrásának használatára.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
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

    Azt is megteheti, hogy FTP-ügyfél használatával tölti fel a JDBC-illesztőt. Az [FTP-hitelesítő adatok beszerzéséhez kövesse az alábbi utasításokat](../deploy-configure-credentials.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

2. Ha létrehozta a kiszolgálói szintű adatforrást, indítsa újra a App Service Linux alkalmazást. A Tomcat alaphelyzetbe állítja `CATALINA_BASE` `/home/tomcat` és a frissített konfigurációt használja.

### <a name="spring-boot"></a>Spring Boot

Ha a Spring boot-alkalmazásokban lévő adatforrásokhoz szeretne csatlakozni, javasoljuk, hogy hozzon létre kapcsolati karakterláncokat, és szúrja be őket az *Application. properties* fájlba.

1. A App Service lapon a konfiguráció szakaszban adja meg a karakterlánc nevét, illessze be a JDBC-kapcsolódási karakterláncot az érték mezőbe, és állítsa a típust "Custom" értékre. Ezt a kapcsolási karakterláncot tárolóhely-beállításként is megadhatja.

    Ez a kapcsolódási sztring `CUSTOMCONNSTR_<your-string-name>`nevű környezeti változóként érhető el az alkalmazás számára. Például a fentiekben létrehozott kapcsolatok karakterlánca a következő lesz: `CUSTOMCONNSTR_exampledb`.

2. Az *Application. properties* fájlban adja meg ezt a hivatkozási karakterláncot a környezeti változó nevével. Példánkban a következőt fogjuk használni.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

Erről a témakörről a [Spring boot dokumentációjában](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html) talál további információt az adathozzáférésről és a [külső konfigurációkról](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html) .

## <a name="configure-java-ee-wildfly"></a>Java EE konfigurálása (WildFly)

> [!NOTE]
> A Java Enterprise Edition App Service Linux rendszeren jelenleg előzetes verzióban érhető el. Ez a verem éles munkához **nem** ajánlott.

A Linuxon Azure App Service lehetővé teszi a Java-fejlesztők számára a Java Enterprise-(Java EE-) alkalmazások kiépítését, üzembe helyezését és méretezését egy teljes mértékben felügyelt Linux-alapú szolgáltatáson.  A mögöttes Java Enterprise Runtime-környezet a nyílt forráskódú [WildFly](https://wildfly.org/) alkalmazáskiszolgáló.

Ez a szakasz a következő alszakaszokat tartalmazza:

- [Méretezés App Service](#scale-with-app-service)
- [Alkalmazáskiszolgáló konfigurációjának testreszabása](#customize-application-server-configuration)
- [Modulok és függőségek telepítése](#install-modules-and-dependencies)
- [Adatforrások konfigurálása](#configure-data-sources)
- [Üzenetkezelési szolgáltatók engedélyezése](#enable-messaging-providers)

### <a name="scale-with-app-service"></a>Méretezés App Service

A Linuxon App Service futó WildFly-alkalmazáskiszolgáló önálló módban fut, nem pedig tartományi konfigurációban. A App Service terv kiskálázásakor minden WildFly-példány önálló kiszolgálóként van konfigurálva.

Az alkalmazást függőlegesen vagy horizontálisan méretezheti méretezési [szabályokkal](../../monitoring-and-diagnostics/monitoring-autoscale-get-started.md) , és [növelheti a példányszámot](../manage-scale-up.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

### <a name="customize-application-server-configuration"></a>Alkalmazáskiszolgáló konfigurációjának testreszabása

A webalkalmazás-példányok állapot nélküliek, ezért minden egyes új példányt be kell állítani indításkor, hogy támogassa az alkalmazás által igényelt WildFly-konfigurációt.
A WildFly parancssori felületének meghívásához írhat egy indítási bash-szkriptet a következőre:

- Adatforrások beállítása
- Üzenetkezelési szolgáltatók konfigurálása
- További modulok és függőségek hozzáadása a WildFly-kiszolgáló konfigurációjához.

A szkript akkor fut le, amikor a WildFly fut, de az alkalmazás elindítása előtt. A parancsfájlnak a */opt/JBoss/wildfly/bin/JBoss-CLI.sh* nevű [JBoss CLI](https://docs.jboss.org/author/display/WFLY/Command+Line+Interface) -t kell használnia az alkalmazáskiszolgáló konfigurálásához a kiszolgáló elindítása után szükséges konfigurációval vagy módosításokkal.

Ne használja a CLI interaktív módját a WildFly konfigurálásához. Ehelyett a `--file` parancs használatával adhat meg parancsokat a JBoss CLI-hez, például:

```bash
/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli
```

Az FTP használatával töltse fel az indítási szkriptet a App Service-példányban található helyre a */Home* -címtárban, például: */Home/site/Deployments/Tools*. További információ: [az alkalmazás üzembe helyezése Azure app Service FTP/S használatával](https://docs.microsoft.com/azure/app-service/deploy-ftp).

A Azure Portal **indítási parancsfájl** mezőjében adja meg az indítási rendszerhéj parancsfájljának helyét (például */Home/site/Deployments/Tools/Your-Startup-script.sh*).

Adja [meg az](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) alkalmazás konfigurációjában a környezeti változók átadását a parancsfájlban való használathoz. Az Alkalmazásbeállítások megtartják a kapcsolatok karakterláncait és az alkalmazás verziószám-vezérlésének konfigurálásához szükséges egyéb titkokat.

### <a name="install-modules-and-dependencies"></a>Modulok és függőségek telepítése

Ha a JBoss CLI-n keresztül szeretné telepíteni a modulokat és a függőségeiket a WildFly-osztályútvonal, akkor a következő fájlokat kell létrehoznia a saját címtárában. Egyes moduloknak és függőségeknek további konfigurációra lehet szükségük, mint például a JNDI elnevezés vagy más API-specifikus konfiguráció, így ez a lista a legtöbb esetben a függőség konfigurálásához szükséges minimális készlet.

- Egy [XML-modul leírója](https://jboss-modules.github.io/jboss-modules/manual/#descriptors). Ez az XML-fájl határozza meg a modul nevét, attribútumait és függőségeit. Ez a [példa modul. xml fájl](https://access.redhat.com/documentation/en-us/jboss_enterprise_application_platform/6/html/administration_and_configuration_guide/example_postgresql_xa_datasource) definiál egy postgres modult, a jar-fájl JDBC-függőségét és más szükséges modul-függőségeket.
- A modulhoz szükséges JAR-fájlok függőségei.
- Egy, a JBoss CLI-parancsokat tartalmazó szkript az új modul konfigurálásához. Ez a fájl a JBoss CLI által végrehajtandó parancsokat fogja tartalmazni, hogy a kiszolgáló a függőség használatára legyen konfigurálva. A modulok, adatforrások és üzenetküldési szolgáltatók hozzáadására szolgáló parancsokkal kapcsolatos dokumentációért tekintse meg [ezt a dokumentumot](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli).
- Egy bash indítási parancsfájl a JBoss CLI meghívásához és az előző lépésben végrehajtandó parancsfájl végrehajtásához. Ezt a fájlt a rendszer a App Service-példány újraindításakor, illetve az új példányok kiépítés során történő kiosztásakor hajtja végre. Ez az indítási szkript az alkalmazás más konfigurációit is elvégezheti, mivel a JBoss-parancsok át lesznek adva a JBoss CLI-nek. Ez a fájl minimálisan egyetlen parancs lehet a JBoss CLI parancssori parancsfájl átadására a JBoss CLI-re:

```bash
/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli
```

Miután megadta a modulhoz tartozó fájlokat és tartalmakat, az alábbi lépések végrehajtásával adja hozzá a modult a WildFly alkalmazás-kiszolgálóhoz.

1. Az FTP használatával töltse fel a fájlokat a App Service példányában a */Home* -címtárban, például */Home/site/Deployments/Tools*. További információ: [az alkalmazás üzembe helyezése Azure app Service FTP/S használatával](../deploy-ftp.md).
2. A Azure Portal **konfigurációs** > **általános beállítások** lapján állítsa be az **indítási parancsfájl** mezőt az indítási rendszerhéj parancsfájljának helyére, például */Home/site/Deployments/Tools/Startup.sh*.
3. Indítsa újra a App Service példányt a portál **Áttekintés** szakaszában található **Újraindítás** gombra kattintva vagy az Azure CLI használatával.

### <a name="configure-data-sources"></a>Adatforrások konfigurálása

Az WildFly/JBoss konfigurálásához az adatforrás eléréséhez használja a fentiekben leírt általános folyamatot a "modulok és függőségek telepítése" szakaszban. A következő szakasz részletesen ismerteti a PostgreSQL, a MySQL és az SQL Server adatforrások folyamatát.

Ez a szakasz feltételezi, hogy már rendelkezik egy alkalmazással, egy App Service példánnyal és egy Azure Database Service-példánnyal. Az alábbi utasítások a App Service nevét, az erőforráscsoportot és az adatbázis-kapcsolati adatokat tekintik meg. Ezeket az információkat a Azure Portal találja meg.

Ha inkább a teljes folyamatot szeretné áttekinteni a minta alkalmazás elejéről, olvassa el az [oktatóanyag: Java EE-és postgres-Webalkalmazás létrehozása az Azure-ban](tutorial-java-enterprise-postgresql-app.md)című témakört.

A következő lépések ismertetik a meglévő App Service és adatbázis összekapcsolásának követelményeit.

1. Töltse le a [PostgreSQL](https://jdbc.postgresql.org/download.html)-hez, a [MySQL](https://dev.mysql.com/downloads/connector/j/)-hez vagy a [SQL Serverhoz](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)tartozó JDBC-illesztőt. Csomagolja ki a letöltött archívumot az illesztőprogram. jar fájljának beszerzéséhez.

2. Hozzon létre egy olyan nevű fájlt, mint a *Module. XML* , és adja hozzá a következő jelölést. Cserélje le a `<module name>` helyőrzőt (beleértve a szögletes zárójeleket is `org.postgres`) a PostgreSQL-hez, a MySQL-hez `com.mysql`, vagy SQL Server `com.microsoft`. Cserélje le a `<JDBC .jar file path>`t az előző lépésben található. jar-fájl nevére, beleértve a hely teljes elérési útját, amelyet a App Service-példányban fog elhelyezni. Ez bármely hely lehet a */Home* könyvtár alatt.

    ```xml
    <?xml version="1.0" ?>
    <module xmlns="urn:jboss:module:1.1" name="<module name>">
        <resources>
           <resource-root path="<JDBC .jar file path>" />
        </resources>
        <dependencies>
            <module name="javax.api"/>
            <module name="javax.transaction.api"/>
        </dependencies>
    </module>
    ```

3. Hozzon létre egy olyan nevű fájlt, mint a *DataSource-commands. CLI* , és adja hozzá a következő kódot. Cserélje le a `<JDBC .jar file path>` értéket az előző lépésben használt értékre. Cserélje le a `<module file path>` nevet az előző lépés fájlnevére és App Service elérési útjára, például */Home/Module.XML*.

    **PostgreSQL**

    ```console
    module add --name=org.postgres --resources=<JDBC .jar file path> --module-xml=<module file path>

    /subsystem=datasources/jdbc-driver=postgres:add(driver-name=postgres,driver-module-name=org.postgres,driver-class-name=org.postgresql.Driver,driver-xa-datasource-class-name=org.postgresql.xa.PGXADataSource)

    data-source add --name=postgresDS --driver-name=postgres --jndi-name=java:jboss/datasources/postgresDS --connection-url=$DATABASE_CONNECTION_URL --user-name=$DATABASE_SERVER_ADMIN_FULL_NAME --password=$DATABASE_SERVER_ADMIN_PASSWORD --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=org.postgresql.Driver --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLExceptionSorter --jta=true --use-java-context=true --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLValidConnectionChecker

    reload --use-current-server-config=true
    ```

    **MySQL**

    ```console
    module add --name=com.mysql --resources=<JDBC .jar file path> --module-xml=<module file path>

    /subsystem=datasources/jdbc-driver=mysql:add(driver-name=mysql,driver-module-name=com.mysql,driver-class-name=com.mysql.cj.jdbc.Driver)

    data-source add --name=mysqlDS --jndi-name=java:jboss/datasources/mysqlDS --connection-url=$DATABASE_CONNECTION_URL --driver-name=mysql --user-name=$DATABASE_SERVER_ADMIN_FULL_NAME --password=$DATABASE_SERVER_ADMIN_PASSWORD --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=com.mysql.cj.jdbc.Driver --jta=true --use-java-context=true --exception-sorter-class-name=com.mysql.cj.jdbc.integration.jboss.ExtendedMysqlExceptionSorter

    reload --use-current-server-config=true
    ```

    **SQL Server**

    ```console
    module add --name=com.microsoft --resources=<JDBC .jar file path> --module-xml=<module file path>

    /subsystem=datasources/jdbc-driver=sqlserver:add(driver-name=sqlserver,driver-module-name=com.microsoft,driver-class-name=com.microsoft.sqlserver.jdbc.SQLServerDriver,driver-datasource-class-name=com.microsoft.sqlserver.jdbc.SQLServerDataSource)

    data-source add --name=sqlDS --jndi-name=java:jboss/datasources/sqlDS --driver-name=sqlserver --connection-url=$DATABASE_CONNECTION_URL --validate-on-match=true --background-validation=false --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.mssql.MSSQLValidConnectionChecker --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.mssql.MSSQLExceptionSorter

    reload --use-current-server-config=true
    ```

    Ezt a fájlt a következő lépésben leírt indítási parancsfájl futtatja. Telepíti a JDBC illesztőprogramot WildFly modulként, létrehozza a megfelelő WildFly-adatforrást, és újratölti a kiszolgálót, hogy a módosítások érvénybe lépnek.

4. Hozzon létre egy olyan fájlt, amelynek neve például *Startup.sh* , és adja hozzá a következő kódot. A `<JBoss CLI script>` helyére írja be az előző lépésben létrehozott fájl nevét. Ügyeljen arra, hogy a hely teljes elérési útját adja meg a App Service-példányban, például */Home/DataSource-commands.CLI*.

    ```bash
    #!/usr/bin/env bash
    /opt/jboss/wildfly/bin/jboss-cli.sh -c --file=<JBoss CLI script>
    ```

5. Az FTP használatával töltse fel a JDBC. jar fájlt, a modul XML-fájlját, a JBoss CLI-parancsfájlt és a App Service példányának indítási parancsfájlját. Helyezze ezeket a fájlokat az előző lépésekben megadott helyre, például */Home*. Az FTP-vel kapcsolatos további információkért lásd: [az alkalmazás telepítése Azure app Service FTP/S használatával](https://docs.microsoft.com/azure/app-service/deploy-ftp).

6. Az Azure CLI-vel olyan beállításokat adhat hozzá az App Servicehoz, amelyek az adatbázis-kapcsolódási adatokat tárolják. Cserélje le a `<resource group>` és a `<webapp name>` értéket a App Service által használt értékekre. Cserélje le `<database server name>`, `<database name>`, `<admin name>`és `<admin password>` az adatbázis-kapcsolatok adataira. A App Service és az adatbázis adatai a Azure Portalból szerezhetők be.

    **PostgreSQL**

    ```bash
    az webapp config appsettings set \
        --resource-group <resource group> \
        --name <webapp name> \
        --settings \
            DATABASE_CONNECTION_URL=jdbc:postgresql://<database server name>:5432/<database name>?ssl=true \
            DATABASE_SERVER_ADMIN_FULL_NAME=<admin name> \
            DATABASE_SERVER_ADMIN_PASSWORD=<admin password>
    ```

    **MySQL**

    ```bash
    az webapp config appsettings set \
        --resource-group <resource group> \
        --name <webapp name> \
        --settings \
            DATABASE_CONNECTION_URL=jdbc:mysql://<database server name>:3306/<database name>?ssl=true\&useLegacyDatetimeCode=false\&serverTimezone=GMT \
            DATABASE_SERVER_ADMIN_FULL_NAME=<admin name> \
            DATABASE_SERVER_ADMIN_PASSWORD=<admin password>
    ```

    **SQL Server:**

    ```bash
    az webapp config appsettings set \
        --resource-group <resource group> \
        --name <webapp name> \
        --settings \
            DATABASE_CONNECTION_URL=jdbc:sqlserver://<database server name>:1433;database=<database name>;user=<admin name>;password=<admin password>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
    ```

    Az DATABASE_CONNECTION_URL értékek különbözőek az egyes adatbázis-kiszolgálók esetében, és eltérnek a Azure Portal értékeitől. Az itt látható URL-formátumok (és a fenti kódrészletek) esetében a WildFly használatához a következők szükségesek:

    * **PostgreSQL:** `jdbc:postgresql://<database server name>:5432/<database name>?ssl=true`
    * **MySQL:** `jdbc:mysql://<database server name>:3306/<database name>?ssl=true\&useLegacyDatetimeCode=false\&serverTimezone=GMT`
    * **SQL Server:** `jdbc:sqlserver://<database server name>:1433;database=<database name>;user=<admin name>;password=<admin password>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;`

7. A Azure Portal navigáljon a App Service, és keresse meg a **konfigurációs** > **általános beállítások** lapot. Állítsa az **indítási parancsfájl** mezőt az indítási parancsfájl nevére és helyére, például */Home/Startup.sh*.

A App Service következő újraindításakor futtatja az indítási parancsfájlt, és végrehajtja a szükséges konfigurációs lépéseket. Ha ellenőrizni szeretné, hogy ez a konfiguráció megfelelően működik-e, az SSH-val elérheti a App Service, majd a bash parancssorból futtathatja saját indítási parancsfájlját. A App Service naplókat is ellenőrizheti. További információ ezekről a lehetőségekről: [alkalmazások naplózása és hibakeresése](#logging-and-debugging-apps).

Ezután frissítenie kell az alkalmazás WildFly-konfigurációját, és újra kell telepítenie azt. Ehhez a következő lépések szükségesek:

1. Nyissa meg az alkalmazás *src/Main/Resources/META-INF/perzisztencia. XML* fájlját, és keresse meg a `<jta-data-source>` elemet. Cserélje le a tartalmát az itt látható módon:

    **PostgreSQL**

    ```xml
    <jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
    ```

    **MySQL**

    ```xml
    <jta-data-source>java:jboss/datasources/mysqlDS</jta-data-source>
    ```

    **SQL Server**

    ```xml
    <jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
    ```

2. Hozza újra létre és telepítse újra az alkalmazást a következő paranccsal a bash-parancssorban:

    ```bash
    mvn package -DskipTests azure-webapp:deploy
    ```

3. Indítsa újra a App Service példányt úgy, hogy a Azure Portal vagy az Azure CLI használatával megnyomja az **Újraindítás** gombot az **Áttekintés** részben.

A App Service-példánya már konfigurálva van az adatbázis elérésére.

További információ az adatbázis-kapcsolat WildFly való konfigurálásáról: [PostgreSQL](https://developer.jboss.org/blogs/amartin-blog/2012/02/08/how-to-set-up-a-postgresql-jdbc-driver-on-jboss-7), [MySQL](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#Using_other_Databases-Using_MySQL_as_the_Default_DataSource)vagy [SQL Server](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#d0e3898).

### <a name="enable-messaging-providers"></a>Üzenetkezelési szolgáltatók engedélyezése

Az üzenet-vezérelt bab engedélyezése a Service Bus használatával üzenetküldési mechanizmusként:

1. Használja az [Apache csontos JMS Messaging Library könyvtárat](https://qpid.apache.org/proton/index.html). Ezt a függőséget az alkalmazáshoz tartozó Pom. XML (vagy más Build-fájl) tartalmazza.

2. Hozzon létre [Service Bus erőforrásokat](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp). Hozzon létre egy Azure Service Bus névteret és várólistát az adott névtéren belül, valamint egy közös hozzáférési szabályzatot a küldési és fogadási funkciókkal.

3. Adja át a megosztott elérési házirend kulcsát a kódnak URL-kódolással – kódolja a házirend elsődleges kulcsát, vagy [használja a Service Bus SDK](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp#setup-jndi-context-and-configure-the-connectionfactory)-t.

4. Kövesse a modulok és függőségek telepítése szakasz lépéseit a modul XML-leírójának,. jar-függőségeinek, JBoss CLI-parancsainak és az JMS-szolgáltató indítási parancsfájljának használatával. A négy fájlon kívül létre kell hoznia egy XML-fájlt is, amely meghatározza a JMS-várólista és a témakör JNDI nevét. Tekintse meg [ezt a tárházat](https://github.com/JasonFreeberg/widlfly-server-configs/tree/master/appconfig) a hivatkozási konfigurációs fájlokhoz.

## <a name="use-redis-as-a-session-cache-with-tomcat"></a>A Redis használata munkamenet-gyorsítótárként a Tomcat használatával

A Tomcat úgy is beállítható, hogy külső munkamenet-tárolót használjon, például az [Azure cache-t a Redis](/azure/azure-cache-for-redis/). Így megőrizheti a felhasználói munkamenet állapotát (például a bevásárlókocsi adatait), amikor a felhasználó átkerül az alkalmazás egy másik példányára, például ha automatikus skálázás, újraindítás vagy feladatátvétel történik.

A Tomcat és a Redis használatához konfigurálnia kell az alkalmazást egy [PersistentManager](http://tomcat.apache.org/tomcat-8.5-doc/config/manager.html) -implementáció használatára. A következő lépések ismertetik ezt a folyamatot a [Pivotal Session Manager használatával: Redis-Store](https://github.com/pivotalsoftware/session-managers/tree/master/redis-store) példaként.

1. Nyisson meg egy bash-terminált, és a `export <variable>=<value>` használatával állítsa be az alábbi környezeti változók mindegyikét.

    | Változó                 | Érték                                                                      |
    |--------------------------|----------------------------------------------------------------------------|
    | RESOURCEGROUP_NAME       | Az App Service példányt tartalmazó erőforráscsoport neve.       |
    | WEBAPP_NAME              | Az App Service-példány neve.                                     |
    | WEBAPP_PLAN_NAME         | A App Service terv neve                                          |
    | RÉGIÓ                   | Annak a régiónak a neve, ahol az alkalmazás üzemeltetve van.                           |
    | REDIS_CACHE_NAME         | A Redis-példány Azure-gyorsítótárának neve.                           |
    | REDIS_PORT               | A Redis cache által figyelt SSL-port.                             |
    | REDIS_PASSWORD           | A példány elsődleges hozzáférési kulcsa.                                  |
    | REDIS_SESSION_KEY_PREFIX | Az alkalmazásból érkező munkamenetkulcsok azonosítására megadott érték. |

    A Azure Portal nevét, portját és hozzáférési kulcsát a szolgáltatási példány **Tulajdonságok** vagy **hozzáférési kulcsok** szakaszában tekintheti meg.

2. Hozza létre vagy frissítse az alkalmazás *src/Main/WebApp/META-INF/Context. XML* fájlját a következő tartalommal:

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

8. Frissítse az alkalmazás *Pom. XML* fájljának `azure-webapp-maven-plugin` konfigurációját, hogy a Redis-fiók adataira hivatkozzon. Ez a fájl a korábban beállított környezeti változókat használja, hogy a fiókadatok a forrásfájlok adatain kívül maradjanak.

    Ha szükséges, módosítsa `1.7.0` a [Maven beépülő modul](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme)aktuális verziójára Azure app Service.

    ```xml
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.7.0</version>
        <configuration>

            <!-- Web App information -->
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appServicePlanName>${WEBAPP_PLAN_NAME}-${REGION}</appServicePlanName>
            <appName>${WEBAPP_NAME}-${REGION}</appName>
            <region>${REGION}</region>
            <linuxRuntime>tomcat 9.0-jre8</linuxRuntime>

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
    mvn package
    mvn azure-webapp:deploy
    ```

Az alkalmazás mostantól a Redis cache-t fogja használni a munkamenet-kezeléshez.

Az utasítások tesztelésére használható minta: [Méretezés – állapot-nyilvántartó – Java-Web-App-on-Azure](https://github.com/Azure-Samples/scaling-stateful-java-web-app-on-azure) -tárház a githubon.

## <a name="docker-containers"></a>Docker-tárolók

Az Azure által támogatott Zulu a tárolókban való használatához győződjön meg róla, hogy lekéri és használja az előre elkészített rendszerképeket a [támogatott Azul Zulu Enterprise for Azure letöltési oldaláról](https://www.azul.com/downloads/azure-only/zulu/) , vagy használja a [Microsoft Java GitHub](https://github.com/Microsoft/java/tree/master/docker)-tárház `Dockerfile` példáit.

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

## <a name="next-steps"></a>További lépések

Látogasson el az Azure [for Java Developers](/java/azure/) Center webhelyre, ahol megtalálhatja az Azure rövid útmutatók, oktatóanyagok és a Java-dokumentációt.

A Linux rendszerhez készült App Service használatának általános kérdései a Java [app Service](app-service-linux-faq.md)-fejlesztésre nem vonatkoznak.
