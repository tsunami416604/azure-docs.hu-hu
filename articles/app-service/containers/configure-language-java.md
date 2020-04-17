---
title: Linux Java-alkalmazások konfigurálása
description: Ismerje meg, hogyan konfigurálhat előre elkészített Java-tárolót az alkalmazáshoz. Ez a cikk a leggyakoribb konfigurációs feladatokat mutatja be.
keywords: Azure app szolgáltatás, web app, linux, oss, java, java ee, jee, javaee
author: bmitchell287
manager: barbkess
ms.devlang: java
ms.topic: article
ms.date: 11/22/2019
ms.author: brendm
ms.reviewer: cephalin
ms.custom: seodec18
ms.openlocfilehash: f4f6de807628704051cdddf74bcefbed678f8fcd
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457892"
---
# <a name="configure-a-linux-java-app-for-azure-app-service"></a>Linux Java-alkalmazás konfigurálása az Azure App Service-hez

Az Azure App Service linuxos lehetővé teszi a Java-fejlesztők számára, hogy gyorsan elkészítsék, üzembe telepítsék és méretezzék a Tomcat vagy java standard edition (SE) csomagolt webalkalmazásaikat egy teljes körűen felügyelt Linux-alapú szolgáltatáson. Alkalmazásokat telepíthet a Maven beépülő modullal a parancssorból vagy olyan szerkesztőkben, mint az IntelliJ, az Eclipse vagy a Visual Studio Code.

Ez az útmutató kulcsfontosságú fogalmakat és utasításokat tartalmaz a Java fejlesztők számára, akik egy beépített Linux-tárolót használnak az App Service-ben. Ha még soha nem használta az Azure App Service szolgáltatást, kövesse a [Java gyorsútmutatót.](quickstart-java.md)

## <a name="deploying-your-app"></a>Az alkalmazás telepítése

A [Maven Plugin for Azure App Service](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) használatával .jar és .war fájlokat is telepíthet. A népszerű azonosítókkal való telepítést az [Azure Toolkit for IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij) vagy az [Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse)is támogatja.

Ellenkező esetben a telepítési módszer az archívum típusától függ:

- A .war fájlok Tomcat-ra `/api/wardeploy/` való telepítéséhez használja a végpontot az archív fájl postához. Az API-val kapcsolatos további információkért tekintse meg [ezt a dokumentációt.](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file)
- A .jar fájlok telepítése a Java SE-lemezképeken használja a `/api/zipdeploy/` Kudu hely végpontját. Az API-val kapcsolatos további információkért tekintse meg [ezt a dokumentációt.](https://docs.microsoft.com/azure/app-service/deploy-zip#rest)

Ne telepítse a .war vagy .jar FTP használatával. Az FTP-eszköz indítási parancsfájlok, függőségek vagy más futásidejű fájlok feltöltésére szolgál. Nem ez az optimális választás a webalkalmazások üzembe helyezéséhez.

## <a name="logging-and-debugging-apps"></a>Alkalmazások naplózása és hibakeresése

Teljesítményjelentések, forgalmi vizualizációk és állapotalapú szűrések érhetők el az egyes alkalmazásokhoz az Azure Portalon keresztül. További információ: [Azure App Service diagnostics overview](../overview-diagnostics.md).

### <a name="ssh-console-access"></a>SSH konzolhozzáférés

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>Diagnosztikai naplók streamelése

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

További információ: [Stream logs in Cloud Shell](../troubleshoot-diagnostic-logs.md#in-cloud-shell).

### <a name="app-logging"></a>Alkalmazásnaplózás

Engedélyezze az [alkalmazások naplózását](../troubleshoot-diagnostic-logs.md?toc=/azure/app-service/containers/toc.json#enable-application-logging-windows) az Azure Portalon vagy az [Azure CLI-n](/cli/azure/webapp/log#az-webapp-log-config) keresztül, hogy konfigurálja az App Service-t az alkalmazás szabványos konzolkimenetének és szabványos konzolhiba-streamjeinek írásához a helyi fájlrendszerbe vagy az Azure Blob Storage-ba. A helyi App Service fájlrendszer-példányra való naplózás a konfigurálás után 12 órával le van tiltva. Ha hosszabb megőrzésre van szüksége, konfigurálja az alkalmazást, hogy a kimenet et egy Blob storage-tárolóba írja. A Java és tomcat alkalmazásnaplók a */home/LogFiles/Application/* könyvtárban találhatók.

Ha az alkalmazás [logback](https://logback.qos.ch/) vagy [Log4j-t](https://logging.apache.org/log4j) használ a nyomkövetéshez, továbbíthatja ezeket a nyomkövetéseket az Azure Application Insightsban a Naplózási keretrendszer konfigurációs utasításainak használatával az [Application Insights Java-nyomkövetési naplóinak felfedezése](/azure/application-insights/app-insights-java-trace-logs)című részben.

### <a name="troubleshooting-tools"></a>Hibaelhárító eszközök

A beépített Java-lemezképek az [Alpine Linux](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) operációs rendszeren alapulnak. A `apk` csomagkezelő vel telepítheti a hibaelhárító eszközöket és parancsokat.

### <a name="flight-recorder"></a>Fedélzeti adatrögzítő

Az App Service összes Linux Java-lemeze in Zulu Flight Recorder telepítve van, így könnyedén csatlakozhat a JVM-hez, és elindíthat egy profilozó felvételt, vagy létrehoz egy halommemória-ot.

#### <a name="timed-recording"></a>Időzített felvétel

A kezdéshez ssh az App Service `jcmd` és futtassa a parancsot, hogy egy listát az összes Java folyamatok futó. Amellett, hogy jcmd magát, meg kell jelennie a Java alkalmazás fut egy folyamat azonosító száma (pid).

```shell
078990bbcd11:/home# jcmd
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
147 sun.tools.jcmd.JCmd
116 /home/site/wwwroot/app.jar
```

Az alábbi parancs végrehajtása a JVM 30 másodperces rögzítésének elindításához. Ez profilt készít a JVM-hez, és létrehoz egy *jfr_example.jfr* nevű JFR-fájlt a kezdőkönyvtárban. (Cserélje le a 116-os tao-t a Java-alkalmazás pid-jére.)

```shell
jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"
```

A 30 másodperces időköz alatt ellenőrizheti, `jcmd 116 JFR.check`hogy a felvétel a futva történik. Ez megmutatja az adott Java folyamat összes felvételét.

#### <a name="continuous-recording"></a>Folyamatos felvétel

A Zulu Flight Recorder segítségével folyamatosan profilozhatja Java alkalmazását, minimális hatással a futásidejű teljesítményre[(forrás).](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf) Ehhez futtassa a következő Azure CLI parancsot egy JAVA_OPTS nevű alkalmazásbeállítás létrehozásához a szükséges konfigurációval. A JAVA_OPTS alkalmazásbeállítás tartalma az `java` alkalmazás indításakor átkerül a parancsba.

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

A felvétel megkezdése után a parancs segítségével bármikor kidobhatja az `JFR.dump` aktuális felvételi adatokat.

```shell
jcmd <pid> JFR.dump name=continuous_recording filename="/home/recording1.jfr"
```

További információkért tekintse meg a [Jcmd parancs referencia](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190).

### <a name="analyzing-recordings"></a>Felvételek elemzése

Az [FTPS](../deploy-ftp.md) segítségével töltse le a JFR fájlt a helyi számítógépre. A JFR fájl elemzéséhez töltse le és telepítse a [Zulu Mission Control alkalmazást.](https://www.azul.com/products/zulu-mission-control/) A Zulu Mission Control ról az [Azul dokumentációjában](https://docs.azul.com/zmc/) és a [telepítési útmutatóban](https://docs.microsoft.com/java/azure/jdk/java-jdk-flight-recorder-and-mission-control)olvashat.

## <a name="customization-and-tuning"></a>Testreszabás és hangolás

Az Azure App Service for Linux támogatja a dobozból hangolást és testreszabást az Azure Portalon és a CLI-n keresztül. Tekintse át az alábbi cikkeket a nem Java-specifikus webalkalmazás-konfigurációról:

- [Alkalmazásbeállítások konfigurálása](../configure-common.md?toc=/azure/app-service/containers/toc.json#configure-app-settings)
- [Egyéni tartomány beállítása](../app-service-web-tutorial-custom-domain.md?toc=/azure/app-service/containers/toc.json)
- [SSL-kötések konfigurálása](../configure-ssl-bindings.md?toc=/azure/app-service/containers/toc.json)
- [CDN hozzáadása](../../cdn/cdn-add-to-web-app.md?toc=/azure/app-service/containers/toc.json)
- [A Kudu webhely konfigurálása](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>Java futásidejű beállítások megadása

Ha a Tomcat és a Java SE környezetben is meg szeretné adni a `JAVA_OPTS` lefoglalt memóriát vagy más JVM-futásidejű beállításokat, hozzon létre egy [alkalmazásbeállítást](../configure-common.md?toc=/azure/app-service/containers/toc.json#configure-app-settings) a beállításokkal. Az App Service Linux ezt a beállítást környezeti változóként adja át a Java-futásórának, amikor elindul.

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

Jar-alkalmazás telepítése esetén *az alkalmazás.jar* nevűnek kell lennie, hogy a beépített lemezkép megfelelően azonosíthassa az alkalmazást. (A Maven plugin nem ez az átnevezés automatikusan.) Ha nem szeretné átnevezni a JAR-t *az app.jar*fájlra, akkor a JAR futtatásához parancssal feltöltheti a shell parancsfájlt. Ezután illessze be a parancsfájl teljes elérési útját a portál konfigurációs szakaszának [Indítási fájl](app-service-linux-faq.md#built-in-images) szövegmezőjébe. Az indítási szkript nem abban a könyvtárban fut, amelyben el van helyezve. Ezért mindig abszolút elérési utakat használjon az indítási szkriptben található fájlokra való hivatkozáskor (például: `java -jar /home/myapp/myapp.jar`).

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

### <a name="adjust-startup-timeout"></a>Indítási időtúllépés módosítása

Ha a Java-alkalmazás különösen nagy, meg kell növelnie az indítási időkorlátot. Ehhez hozzon létre egy `WEBSITES_CONTAINER_START_TIME_LIMIT` alkalmazásbeállítást, és állítsa be, hogy az App Service várakozási idő túllépés előtt várjon. A maximális `1800` érték másodperc.

### <a name="pre-compile-jsp-files"></a>JSP-fájlok előfordítása

A Tomcat-alkalmazások teljesítményének javítása érdekében az App Service-be való telepítés előtt lefordíthatja a JSP-fájlokat. Használhatja a [Maven plugin](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) által biztosított Apache Sling, vagy használja ezt [a Hangya build fájlt](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation).

## <a name="secure-applications"></a>Biztonságos alkalmazások

A Linuxos App Service szolgáltatásban futó Java-alkalmazások ugyanazokat a [biztonsági bevált gyakorlatokat alkalmazzák,](/azure/security/security-paas-applications-using-app-services) mint más alkalmazások.

### <a name="authenticate-users-easy-auth"></a>Felhasználók hitelesítése (Egyszerű hitelesítés)

Állítsa be az alkalmazáshitelesítést az Azure Portalon a **hitelesítési és engedélyezési** beállítással. Innen engedélyezheti a hitelesítést az Azure Active Directory vagy a közösségi bejelentkezések, például a Facebook, a Google vagy a GitHub használatával. Az Azure Portal konfigurációja csak egyetlen hitelesítésszolgáltató konfigurálásakor működik. További információ: [Az App Service-alkalmazás konfigurálása az Azure Active Directory bejelentkezési](../configure-authentication-provider-aad.md?toc=/azure/app-service/containers/toc.json) és a kapcsolódó cikkek más identitásszolgáltatók. Ha több bejelentkezési szolgáltatót kell engedélyeznie, kövesse az [App Service hitelesítési](../app-service-authentication-how-to.md?toc=/azure/app-service/containers/toc.json) cikkének testreszabása című cikk utasításait.

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

#### <a name="spring-boot"></a>Spring Boot

A tavaszi rendszerindítás-fejlesztők az [Azure Active Directory tavaszi rendszerindítási kezdőszolgáltatásával](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) biztosíthatják a jól ismert tavaszi biztonsági jegyzetek és API-k használatával működő alkalmazásokat. Ügyeljen arra, hogy növelje a maximális fejlécméretet az *application.properties* fájlban. Javasoljuk, hogy `16384`az értéke.

### <a name="configure-tlsssl"></a>TLS/SSL konfigurálása

Kövesse a [Biztonságos egyéni DNS-név az Azure App Service-ben egy SSL-kötéssel](../configure-ssl-bindings.md?toc=/azure/app-service/containers/toc.json) című útmutatóban egy meglévő SSL-tanúsítvány feltöltéséhez és az alkalmazás tartománynevéhez kötéséhez. Alapértelmezés szerint az alkalmazás továbbra is engedélyezi a HTTP-kapcsolatokat az oktatóanyag ban az SSL és a TLS kényszerítéséhez szükséges konkrét lépések végrehajtásához.

### <a name="use-keyvault-references"></a>KeyVault-hivatkozások használata

[Az Azure KeyVault](../../key-vault/general/overview.md) központi titkos kezelést biztosít hozzáférési szabályzatokkal és naplózási előzményekkel. A keyvaultban tárolhatja a titkos kulcsokat (például a jelszavakat vagy a kapcsolati karakterláncokat), és környezeti változókon keresztül hozzáférhet ezekhez a titkos kulcsokhoz az alkalmazásban.

Először kövesse az [alkalmazás key vault-hozzáférésének megadására](../app-service-key-vault-references.md#granting-your-app-access-to-key-vault) vonatkozó utasításokat, és [az alkalmazásbeállításban a titkos kulcsra való hivatkozást.](../app-service-key-vault-references.md#reference-syntax) Ellenőrizheti, hogy a hivatkozás feloldódik-e a titkos kulcsot a környezeti változó távoli nyomtatásával, miközben távolról éri el az App Service-terminált.

Ha ezeket a titkokat tavaszi vagy Tomcat konfigurációs`${MY_ENV_VAR}`fájlba szeretné beadni, használja a környezeti változóinjektálásszintaxisát ( ). A tavaszi konfigurációs fájlokat lásd a [külső konfigurációkdokumentációjában.](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)

### <a name="using-the-java-key-store"></a>A Java kulcstároló használata

Alapértelmezés szerint az App [Service Linuxra feltöltött](../configure-ssl-certificate.md) nyilvános vagy privát tanúsítványok a tároló indításakor betöltődnek a megfelelő Java kulcstárolókba. A tanúsítvány feltöltése után újra kell indítania az App Service szolgáltatást ahhoz, hogy betöltődjön a Java kulcstárolóba. A nyilvános tanúsítványok betöltődnek a kulcstárolóba a alkalmazásban, `$JAVA_HOME/jre/lib/security/cacerts`a saját tanúsítványok pedig a alkalmazásban `$JAVA_HOME/lib/security/client.jks`tárolódnak.

A JDBC-kapcsolat javakulcs-tárolóbeli tanúsítványokkal való titkosításához további konfigurációra lehet szükség. Kérjük, olvassa el a kiválasztott JDBC illesztőprogram dokumentációját.

- [PostgreSQL](https://jdbc.postgresql.org/documentation/head/ssl-client.html)
- [SQL Server](https://docs.microsoft.com/sql/connect/jdbc/connecting-with-ssl-encryption?view=sql-server-ver15)
- [MySQL](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-reference-using-ssl.html)
- [MongoDB](https://mongodb.github.io/mongo-java-driver/3.4/driver/tutorials/ssl/)
- [Cassandra](https://docs.datastax.com/en/developer/java-driver/4.3/)

#### <a name="initializing-the-java-key-store"></a>A Java kulcstároló inicializálása

Az `import java.security.KeyStore` objektum inicializálásához töltse be a kulcstároló fájlt a jelszóval. Mindkét kulcstároló alapértelmezett jelszava a "changeit".

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

A tanúsítványokat manuálisan is betöltheti a kulcstárolóba. Hozzon létre `SKIP_JAVA_KEYSTORE_LOAD`egy alkalmazásbeállítást, `1` amelynek értéke letiltja, hogy az App Service automatikusan betöltse a tanúsítványokat a kulcstárolóba. Az Azure Portalon keresztül az App Serviceszolgáltatásba `/var/ssl/certs/`feltöltött összes nyilvános tanúsítvány a tárolóalatt található. A privát tanúsítványok `/var/ssl/private/`at a.

A Java kulcseszközt úgy kezelheti vagy debugolhatja, hogy [ssh-kapcsolatot nyit](app-service-linux-ssh-support.md) az App Service szolgáltatással, és futtatja a parancsot. `keytool` A parancsok listáját a [Kulcseszköz dokumentációjában](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) találja. A KeyStore API-val kapcsolatos további információkért tekintse meg [a hivatalos dokumentációt.](https://docs.oracle.com/javase/8/docs/api/java/security/KeyStore.html)

## <a name="configure-apm-platforms"></a>APM-platformok konfigurálása

Ez a szakasz bemutatja, hogyan kapcsolhatja össze a Linuxon az Azure App Service szolgáltatásban telepített Java-alkalmazásokat a NewRelic és az AppDynamics alkalmazásteljesítmény-figyelési (APM) platformokkal.

### <a name="configure-new-relic"></a>Új ereklye konfigurálása

1. NewRelic fiók létrehozása [NewRelic.com](https://newrelic.com/signup)
2. Töltse le a Java ügynök NewRelic, akkor lesz egy fájl neve hasonló *newrelic-java-x.x.zip*.
3. Másolja a licenckulcsot, szüksége lesz rá az ügynök későbbi konfigurálásához.
4. [SSH-t az App Service-példányba,](app-service-linux-ssh-support.md) és hozzon létre egy új könyvtárat */home/site/wwwroot/apm*.
5. Töltse fel a kicsomagolt NewRelic Java ügynök fájlokat egy könyvtárba a */home/site/wwwroot/apm*alatt. Az ügynök fájljainak a */home/site/wwwroot/apm/newrelic*könyvtárban kell lenniük.
6. Módosítsa a YAML-fájlt a */home/site/wwwroot/apm/newrelic/newrelic.yml oldalon,* és cserélje le a helyőrző licencértékét a saját licenckulcsára.
7. Az Azure Portalon keresse meg az alkalmazást az App Service-ben, és hozzon létre egy új alkalmazásbeállítást.
    - Ha az alkalmazás **Java SE-t**használ, hozzon létre egy a. `JAVA_OPTS` `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`
    - Ha **A Tomcat-ot**használja, hozzon létre egy környezeti változót, amelynek neve `CATALINA_OPTS` a . `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`

### <a name="configure-appdynamics"></a>Az AppDynamics konfigurálása

1. AppDynamics-fiók létrehozása [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Töltse le a Java-ügynököt az AppDynamics webhelyéről, a fájlnév hasonló lesz az *AppServerAgent-x.x.xxxxx.zip fájlhoz.*
3. [SSH-t az App Service-példányba,](app-service-linux-ssh-support.md) és hozzon létre egy új könyvtárat */home/site/wwwroot/apm*.
4. Töltse fel a Java ügynök fájlokat egy könyvtárba a */home/site/wwwroot/apm alatt.* Az ügynök fájljainak a */home/site/wwwroot/apm/appdynamics*könyvtárban kell lenniük.
5. Az Azure Portalon keresse meg az alkalmazást az App Service-ben, és hozzon létre egy új alkalmazásbeállítást.
    - Java **SE**használata esetén hozzon létre `JAVA_OPTS` egy környezeti `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` `<app-name>` változó nevű, az alkalmazásszolgáltatás neve.
    - Ha **a Tomcat**használatával, hozzon `CATALINA_OPTS` létre egy `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` `<app-name>` környezeti változó nevű az érték, ahol az App Service neve.

> [!NOTE]
> Ha már rendelkezik környezeti `JAVA_OPTS` `CATALINA_OPTS`változóval a `-javaagent:/...` vagyhoz, fűzze hozzá a beállítást az aktuális érték végéhez.

## <a name="configure-jar-applications"></a>JAR-alkalmazások konfigurálása

### <a name="starting-jar-apps"></a>Jar-alkalmazások indítása

Alapértelmezés szerint az App Service azt várja, hogy a JAR-alkalmazás neve *app.jar*legyen. Ha rendelkezik ezzel a névvel, akkor automatikusan elindul. A Maven-felhasználók számára beállíthatja a `<finalName>app</finalName>` JAR `<build>` nevet a *pom.xml*szakaszban való beleértve. [Ezt megteheti a Gradle-ben is](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.bundling.Jar.html#org.gradle.api.tasks.bundling.Jar:archiveFileName) a `archiveFileName` tulajdonság beállításával.

Ha a JAR-hoz más nevet szeretne használni, meg kell adnia a JAR-fájlt végrehajtó [Indítási parancsot](app-service-linux-faq.md#built-in-images) is. Például: `java -jar my-jar-app.jar`. Az Indítási parancs értékét a portálon, az Általános beállítások > konfigurációja `STARTUP_COMMAND`> csoportban vagy a program nevű alkalmazásbeállítással állíthatja be.

### <a name="server-port"></a>Kiszolgálóport

Az App Service Linux a bejövő kérelmeket a 80-as portra irányítja, így az alkalmazásnak a 80-as porton is figyelnie kell. Ezt az alkalmazás konfigurációjában (például a Spring *application.properties* fájljában) vagy az Indítási parancsban (például `java -jar spring-app.jar --server.port=80`) teheti meg. Kérjük, olvassa el a közös Java keretrendszerekhez tartozó alábbi dokumentációt:

- [Spring Boot](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-properties-and-configuration.html#howto-use-short-command-line-arguments)
- [SparkJava](http://sparkjava.com/documentation#embedded-web-server)
- [Mikronaut](https://docs.micronaut.io/latest/guide/index.html#runningSpecificPort)
- [Play keret](https://www.playframework.com/documentation/2.6.x/ConfiguringHttps#Configuring-HTTPS)
- [Vertx](https://vertx.io/docs/vertx-core/java/#_start_the_server_listening)
- [Kvarkus](https://quarkus.io/guides/application-configuration-guide)

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

#### <a name="shared-server-level-resources"></a>Megosztott kiszolgálószintű erőforrások

Megosztott, kiszolgálószintű adatforrás hozzáadásához a Tomcat server.xml fájljának szerkesztésére van szükség. Először töltsön fel egy [indítási parancsfájlt,](app-service-linux-faq.md#built-in-images) és állítsa be a parancsfájl elérési útját a **Configuration** > **Startup Command parancsban.** Az indítási parancsfájlt [ftp](../deploy-ftp.md)használatával töltheti fel.

Az indítási parancsfájl [xsl-fájlt alakít át](https://www.w3schools.com/xml/xsl_intro.asp) a server.xml `/usr/local/tomcat/conf/server.xml`fájlba, és az eredményül kapott XML-fájlt a programba adja ki. Az indítási parancsfájlnak telepítenie kell a libxslt-et az apk-on keresztül. Az xsl fájl és az indítási szkript FTP-n keresztül tölthető fel. Az alábbiakban egy példa indítási script.

```sh
# Install libxslt. Also copy the transform file to /home/tomcat/conf/
apk add --update libxslt

# Usage: xsltproc --output output.xml style.xsl input.xml
xsltproc --output /home/tomcat/conf/server.xml /home/tomcat/conf/transform.xsl /usr/local/tomcat/conf/server.xml
```

Az alábbiakban egy példa található az xsl fájlra. A példa xsl-fájl új összekötő csomópontot ad a Tomcat server.xml fájlhoz.

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

Végül helyezze az illesztőprogram-JARs-t a Tomcat classpath-ba, és indítsa újra az App Service-t.

1. Győződjön meg arról, hogy a JDBC illesztőprogram-fájlok elérhetők a Tomcat classloader számára a */home/tomcat/lib* könyvtárba helyezve. (Hozza létre ezt a könyvtárat, ha még nem létezik.) Ha ezeket a fájlokat az App Service-példányba szeretné feltölteni, hajtsa végre az alábbi lépéseket:

    1. A [Cloud Shellben](https://shell.azure.com)telepítse a webapp bővítményt:

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. SSH-alagút létrehozásához futtassa a következő CLI parancsot az App Service szolgáltatásba:

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. Csatlakozzon a helyi bújtatási porthoz az SFTP-ügyféllel, és töltse fel a fájlokat a */home/tomcat/lib* mappába.

    Másik lehetőségként ftp-ügyféllel is feltöltheti a JDBC-illesztőprogramot. Kövesse az alábbi [utasításokat az FTP-hitelesítő adatok megszerzéséhez.](../deploy-configure-credentials.md?toc=/azure/app-service/containers/toc.json)

2. Ha kiszolgálószintű adatforrást hozott létre, indítsa újra az App Service Linux-alkalmazást. A Tomcat `CATALINA_BASE` `/home/tomcat` visszaáll a frissített konfigurációra, és használja azt.

### <a name="spring-boot"></a>Spring Boot

Ha a Spring Boot alkalmazásokban szeretne adatforrásokhoz csatlakozni, javasoljuk, hogy hozzon létre kapcsolati karakterláncokat, és fecskendezze őket az *application.properties* fájlba.

1. Az App Service-lap "Konfiguráció" szakaszában adja meg a karakterlánc nevét, illessze be a JDBC kapcsolati karakterláncot az értékmezőbe, és állítsa a típust "Egyéni" értékre. Ezt a kapcsolati karakterláncot szükség esetén bővítőhely-beállításként is beállíthatja.

    Ez a kapcsolati karakterlánc az alkalmazásunk `CUSTOMCONNSTR_<your-string-name>`számára környezeti változóként érhető el. Például a fent létrehozott kapcsolati `CUSTOMCONNSTR_exampledb`karakterlánc neve lesz.

2. Az *application.properties* fájlban hivatkozzon erre a kapcsolati karakterláncra a környezeti változó nevével. A mi példánkban a következőket használjuk.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

A témával kapcsolatos további információkért tekintse meg az [adatelérésről](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html) és a külső konfigurációkról szóló tavaszi [rendszerindítási dokumentációt.](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)

## <a name="use-redis-as-a-session-cache-with-tomcat"></a>A Redis használata munkamenet-gyorsítótárként a Tomcat segítségével

A Tomcat beállítható külső munkamenet-tároló, például a [Redis Azure Cache használatára.](/azure/azure-cache-for-redis/) Ez lehetővé teszi a felhasználói munkamenet állapotának (például a bevásárlókosár adatai) megőrzését, amikor egy felhasználó tanusítaz alkalmazás egy másik példányába kerül, például automatikus skálázás, újraindítás vagy feladatátvétel esetén.

A Tomcat és a Redis használatához konfigurálnia kell az alkalmazást [persistentmanager-implementáció](https://tomcat.apache.org/tomcat-8.5-doc/config/manager.html) használatára. A következő lépések ismertetik ezt a folyamatot a [Pivotal Session Manager használatával: redis-store](https://github.com/pivotalsoftware/session-managers/tree/master/redis-store) példaként.

1. Nyisson meg egy `<variable>=<value>` Bash terminált, és az alábbi környezeti változók mindegyikének beállításához használja.

    | Változó                 | Érték                                                                      |
    |--------------------------|----------------------------------------------------------------------------|
    | RESOURCEGROUP_NAME       | Az App Service-példányt tartalmazó erőforráscsoport neve.       |
    | WEBAPP_NAME              | Az App Service-példány neve.                                     |
    | WEBAPP_PLAN_NAME         | Az App Service-csomag neve.                                         |
    | RÉGIÓ                   | Annak a régiónak a neve, ahol az alkalmazás található.                           |
    | REDIS_CACHE_NAME         | Az Azure Cache a Redis-példány neve.                           |
    | REDIS_PORT               | Az SSL-port, amelyen a Redis gyorsítótár figyel.                             |
    | REDIS_PASSWORD           | A példány elsődleges hozzáférési kulcsa.                                  |
    | REDIS_SESSION_KEY_PREFIX | Az alkalmazásból származó munkamenetkulcsok azonosítására megadott érték. |

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

    A név, a port és a kulcsinformációk az Azure Portalon található, a szolgáltatáspéldány **Tulajdonságok** vagy **Access kulcsok** szakaszának megkeresve.

2. Az alkalmazás *src/main/webapp/META-INF/context.xml* fájljának létrehozása vagy frissítése a következő tartalommal:

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

    Ez a fájl határozza meg és konfigurálja az alkalmazás munkamenet-kezelői implementációját. Az előző lépésben beállított környezeti változókat használja, hogy a fiókadatokat távol tartsa a forrásfájloktól.

3. Ftp használatával töltse fel a munkamenet-kezelő JAR-fájlját az App Service-példányba, és helyezze el a */home/tomcat/lib* könyvtárba. További információ: [Az alkalmazás telepítése az Azure App Service-ben FTP/S használatával](https://docs.microsoft.com/azure/app-service/deploy-ftp)című témakörben talál.

4. Tiltsa le a [munkamenet-affinitás cookie-t](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) az App Service-példányhoz. Ezt az Azure Portalon úgy teheti meg, hogy az alkalmazásra navigál, majd a **Konfigurációs > általános beállításait > az ARR affinitást** Ki beállításra **állítja.** Másik lehetőségként a következő parancsot is használhatja:

    ```azurecli
    az webapp update -g <resource group> -n <webapp name> --client-affinity-enabled false
    ```

    Alapértelmezés szerint az App Service munkamenet-affinitáscookie-kat használ annak biztosítására, hogy a meglévő munkamenetekkel rendelkező ügyfélkérelmek az alkalmazás ugyanazon példányához legyenek irányítva. Ez az alapértelmezett viselkedés nem igényel konfigurációt, de nem tudja megőrizni a felhasználói munkamenet állapotát az alkalmazáspéldány újraindításakor, vagy ha a forgalmat átirányítja egy másik példányba. Ha [letiltja a meglévő ARR-példány affinitás-konfigurációját](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) a munkamenet-cookie-alapú útválasztás kikapcsolására, engedélyezi a konfigurált munkamenet-tároló beavatkozás nélküli működését.

5. Nyissa meg az App Service-példány **Tulajdonságok** szakaszát, és keresse meg a **további kimenő IP-címeket.** Ezek az alkalmazás összes lehetséges kimenő IP-címét jelölik. Másolja ezeket a következő lépésben való használatra.

6. Minden IP-címhez hozzon létre egy tűzfalszabályt az Azure Cache for Redis-példányban. Ezt az Azure Portalon a Redis-példány **tűzfal** szakaszából teheti meg. Adjon meg egyedi nevet minden szabálynak, és állítsa az **IP-cím kezdete** és **vége IP-cím** értékeket ugyanarra az IP-címre.

7. Nyissa meg a Redis-példány **Speciális beállítások** szakaszát, és állítsa a **Csak SSL-en keresztüli hozzáférés engedélyezése** beállítást **Nem**beállításra. Ez lehetővé teszi, hogy az App Service-példány kommunikáljon a Redis-gyorsítótárral az Azure-infrastruktúrán keresztül.

8. Frissítse `azure-webapp-maven-plugin` az alkalmazás *pom.xml* fájljának konfigurációját, és tekintse meg a Redis-fiók adatait. Ez a fájl a korábban beállított környezeti változókat használja, hogy a fiókadatokat távol tartsa a forrásfájloktól.

    Szükség esetén frissítse a [Maven Plugin for Azure App Service](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme)`1.7.0`-ás verzióját a jelenlegire.

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
    mvn package -DskipTests azure-webapp:deploy
    ```

Az alkalmazás mostantól a Redis-gyorsítótárat fogja használni a munkamenetek kezeléséhez.

Az utasítások teszteléséhez használható minta: [a skálázás-stateful-java-web-app-on-azure](https://github.com/Azure-Samples/scaling-stateful-java-web-app-on-azure) tárműtára a GitHubon.

## <a name="docker-containers"></a>Docker-tárolók

Az Azure által támogatott Zulu JDK-t a tárolókban való használatához mindenképpen lekell kérnie és használnia kell az `Dockerfile` előre elkészített lemezképeket a [támogatott Azul Zulu Enterprise for Azure letöltési lapról](https://www.azul.com/downloads/azure-only/zulu/) dokumentálva, vagy használja a Microsoft Java [GitHub-tárházban](https://github.com/Microsoft/java/tree/master/docker)található példákat.

## <a name="statement-of-support"></a>Támogató nyilatkozat

### <a name="runtime-availability"></a>Futásidejű elérhetőség

Az App Service for Linux két futásidőt támogat a Java webalkalmazások felügyelt üzemeltetéséhez:

- A [Tomcat servlet konténer](https://tomcat.apache.org/) futó alkalmazások csomagolt web archív (WAR) fájlokat. A támogatott verziók a 8.5 és a 9.0.
- Java SE futásidejű környezet Java archív (JAR) fájlként csomagolt alkalmazások futtatásához. A támogatott verziók a Java 8 és 11.

### <a name="jdk-versions-and-maintenance"></a>JDK verziók és karbantartás

Az OpenJDK az Azul Zulu Enterprise buildjei az OpenJDK azure-hoz és az Azure Stackhez készült, költségmentes, többplatformos, éles használatra kész disztribúciót jelentenek, amelyet a Microsoft és az Azul Systems támogat. A Java SE-alkalmazások létrehozásához és futtatásához szükséges összes összetevőt tartalmazzák. A JDK-t java [JDK-telepítésből](https://aka.ms/azure-jdks)telepítheti.

A támogatott JDK-k automatikusan, minden év januárjában, áprilisában, júliusában és októberében negyedévente javításokat hoznak.

### <a name="security-updates"></a>Biztonsági frissítések

A jelentős biztonsági rések javításai és javításai azonnal megjelennek, amint azok elérhetővé válnak az Azul Systems rendszertől. A "jelentős" biztonsági rést a [NIST közös biztonsági réspontozási rendszer 2-es verziójának](https://nvd.nist.gov/cvss.cfm)9.0-s vagy magasabb alappontszáma határozza meg.

### <a name="deprecation-and-retirement"></a>Eprecáció és nyugdíjazás

Ha egy támogatott Java-futásidejű lesz kivonni, az Azure-fejlesztők az érintett futásidejű kap nak egy evehetetlenségi értesítést legalább hat hónappal a futásidő kivonása előtt.

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>További lépések

Látogasson el az [Azure For Java Developers](/java/azure/) centerbe, ahol megtalálja az Azure rövid útmutatóit, oktatóanyagait és java-referenciadokumentációját.

A Java-fejlesztésre nem jellemző, Nem kifejezettEn a Java-fejlesztésre vonatkozó általános kérdésekre az [App Service Linux GYIK](app-service-linux-faq.md)választ kap.
