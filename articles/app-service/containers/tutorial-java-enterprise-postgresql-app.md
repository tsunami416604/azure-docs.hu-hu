---
title: Java Enterprise Web App létrehozása Linux rendszeren – Azure App Service | Microsoft Docs
description: Ismerje meg, hogyan szerezhet be egy Java Enterprise-alkalmazást a Linuxon Azure App Service Wildfly.
author: JasonFreeberg
manager: routlaw
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: tutorial
ms.date: 11/13/2018
ms.author: jafreebe
ms.custom: seodec18
ms.openlocfilehash: 2d26d9e145030e5972289c224dc2f76078d67527
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68498487"
---
# <a name="tutorial-build-a-java-ee-and-postgres-web-app-in-azure"></a>Oktatóanyag: Java EE-és postgres-alapú Webalkalmazás létrehozása az Azure-ban

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre a Java Enterprise Edition (EE) webalkalmazást Azure App Service és hogyan csatlakoztatható egy postgres-adatbázishoz. Ha elkészült, egy [WildFly](https://www.wildfly.org/about/) -alkalmazás fogja tárolni az [Azure Database](https://azure.microsoft.com/services/postgresql/) -ben tárolt postgres, amely az Azure- [app Service Linux](app-service-linux-intro.md)rendszeren fut.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Java EE-alkalmazás üzembe helyezése az Azure-ban a Maven használatával
> * Postgres-adatbázis létrehozása az Azure-ban
> * A WildFly-kiszolgáló konfigurálása a postgres használatára
> * Az alkalmazás frissítése és ismételt üzembe helyezése
> * Egységbeli tesztek futtatása a WildFly

## <a name="prerequisites"></a>Előfeltételek

1. [A git letöltése és telepítése](https://git-scm.com/)
2. [A Maven 3 letöltése és telepítése](https://maven.apache.org/install.html)
3. [Töltse le és telepítse az Azure CLI-t](https://docs.microsoft.com/cli/azure/install-azure-cli)

## <a name="clone-and-edit-the-sample-app"></a>A minta alkalmazás klónozása és szerkesztése

Ebben a lépésben klónozott a minta alkalmazást, és konfigurálja a Maven projekt-objektum modelljét (POM vagy *Pom. XML*) az üzembe helyezéshez.

### <a name="clone-the-sample"></a>A minta klónozása

A terminál ablakban navigáljon egy munkakönyvtárhoz, és [a minta tárház](https://github.com/Azure-Samples/wildfly-petstore-quickstart)klónozásához.

```bash
git clone https://github.com/Azure-Samples/wildfly-petstore-quickstart.git
```

### <a name="update-the-maven-pom"></a>A Maven POM frissítése

Frissítse a Maven Azure beépülő modult a App Service kívánt nevével és erőforráscsoporthoz. Nem kell előre létrehoznia a App Service tervet vagy példányt. A Maven beépülő modul létrehozza az erőforráscsoportot, és App Service, ha még nem létezik.

A módosítások elvégzéséhez görgessen lefelé a `<plugins>` *Pom. XML*, 200. sor szakaszához.

```xml
<!-- Azure App Service Maven plugin for deployment -->
<plugin>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>azure-webapp-maven-plugin</artifactId>
  <version>${version.maven.azure.plugin}</version>
  <configuration>
    <appName>YOUR_APP_NAME</appName>
    <resourceGroup>YOUR_RESOURCE_GROUP</resourceGroup>
    <linuxRuntime>wildfly 14-jre8</linuxRuntime>
  ...
</plugin>  
```

Cserélje `YOUR_APP_NAME` le `YOUR_RESOURCE_GROUP` a és a nevet a app Service és az erőforráscsoport nevére.

## <a name="build-and-deploy-the-application"></a>Az alkalmazás létrehozása és üzembe helyezése

Most a Maven használatával építjük be az alkalmazást, és üzembe helyezzük a App Service.

### <a name="build-the-war-file"></a>A. War fájl összeállítása

A projekthez tartozó POM úgy van konfigurálva, hogy az alkalmazást egy webarchívum (WAR) fájlba csomagolja. Az alkalmazás létrehozása a Maven használatával:

```bash
mvn clean install -DskipTests
```

Az alkalmazásban lévő tesztelési esetek úgy vannak kialakítva, hogy az alkalmazás WildFly történő telepítésekor fusson. A tesztek kihagyása helyileg történik, és a tesztek futtatása után futtatja a teszteket, ha az alkalmazás üzembe helyezése App Service.

### <a name="deploy-to-app-service"></a>Üzembe helyezés az App Service-ben

Most, hogy elkészült a háború, az Azure beépülő modullal üzembe helyezhetjük App Service:

```bash
mvn azure-webapp:deploy
```

A telepítés befejeződése után folytassa a következő lépéssel.

### <a name="create-a-record"></a>Rekord létrehozása

Nyisson meg egy böngészőt, majd lépjen a következő helyre: `https://<your_app_name>.azurewebsites.net/`. Gratulálunk, egy Java EE-alkalmazást helyezett üzembe a Azure App Service!

Ezen a ponton az alkalmazás egy memóriában tárolt H2-adatbázist használ. Kattintson a navigációs sávon a "rendszergazda" elemre, és hozzon létre egy új kategóriát. A memóriában tárolt adatbázisban lévő rekord el fog veszni, ha újraindítja App Service példányát. A következő lépésekben a postgres-adatbázis Azure-beli üzembe helyezésével és a WildFly használatára történő konfigurálásával javít.

![Rendszergazdai gomb helye](media/tutorial-java-enterprise-postgresql-app/admin_button.JPG)

## <a name="provision-a-postgres-database"></a>Postgres-adatbázis kiépítése

Postgres adatbázis-kiszolgáló kiépítéséhez nyisson meg egy terminált, és használja az az [postgres Server Create](https://docs.microsoft.com/cli/azure/postgres/server) parancsot az alábbi példában látható módon. Cserélje le a helyőrzőket (a szögletes zárójeleket is beleértve) a választott értékekkel, és használja ugyanazt az erőforráscsoportot, amelyet korábban a App Service-példányhoz adott meg. Az Ön által megadott rendszergazdai hitelesítő adatok lehetővé teszik a jövőbeli hozzáférést, ezért ügyeljen arra, hogy a későbbi használat érdekében jegyezze fel őket.

```bash
az postgres server create \
    --name <server name> \
    --resource-group <resource group> \
    --location <location>
    --sku-name GP_Gen5_2 \
    --admin-user <administrator username> \
    --admin-password <administrator password> \
```

A parancs futtatása után keresse meg a Azure Portal, és navigáljon a postgres-adatbázishoz. Ha a panel fel van készítve, másolja a "kiszolgáló neve" és a "kiszolgáló-rendszergazda bejelentkezési név" értékeket, később szüksége lesz rájuk.

### <a name="allow-access-to-azure-services"></a>Azure-szolgáltatásokhoz való hozzáférés engedélyezése

Az Azure Database panel **kapcsolatbiztonsági** paneljén kapcsolja be az "Azure-szolgáltatások hozzáférésének engedélyezése" gombot a bekapcsolási  pozícióhoz.

![Azure-szolgáltatásokhoz való hozzáférés engedélyezése](media/tutorial-java-enterprise-postgresql-app/postgress_button.JPG)

## <a name="update-your-java-app-for-postgres"></a>A Java-alkalmazás frissítése a postgres-hez

Most végre fogunk változtatni a Java-alkalmazáson, hogy az a postgres-adatbázis használatát lehetővé tegye.

### <a name="add-postgres-credentials-to-the-pom"></a>Postgres hitelesítő adatainak hozzáadása a POM-hoz

A *Pom. xml fájlban*cserélje le a tőkésített helyőrző értékeket a postgres-kiszolgáló nevére, a rendszergazdai bejelentkezési névre és a jelszóra. Ezek a mezők az Azure Maven beépülő modulján belül találhatók. (Ne felejtse el `YOUR_SERVER_NAME`lecserélni `YOUR_PG_PASSWORD` , `YOUR_PG_USERNAME`és `<value>` a címkére... nem a `<name>` címkéken belül!)

```xml
<plugin>
      ...
      <appSettings>
      <property>
        <name>POSTGRES_CONNECTIONURL</name>
        <value>jdbc:postgresql://YOUR_SERVER_NAME:5432/postgres?ssl=true</value>
      </property>
      <property>
        <name>POSTGRES_USERNAME</name>
        <value>YOUR_PG_USERNAME</value>
      </property>
      <property>
        <name>POSTGRES_PASSWORD</name>
        <value>YOUR_PG_PASSWORD</value>
      </property>
    </appSettings>
  </configuration>
</plugin>
```

### <a name="update-the-java-transaction-api"></a>A Java Transaction API frissítése

A következő lépés a Java Transaction API (közös parlamenti) konfigurációjának szerkesztése, hogy a Java-alkalmazás a korábban használt memóriabeli H2-adatbázis helyett a postgres kommunikáljon. Nyisson meg egy szerkesztőt az *src/Main/Resources/META-INF/perzisztencia. xml fájlhoz*. Cserélje le az `<jta-data-source>` értéket a `java:jboss/datasources/postgresDS` értékre. A JTA XML-nek most már a következő beállítással kell rendelkeznie:

```xml
<jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
```

## <a name="configure-the-wildfly-application-server"></a>A WildFly alkalmazás-kiszolgáló konfigurálása

Az újrakonfigurált alkalmazás üzembe helyezése előtt frissítenie kell a WildFly alkalmazáskiszolgáló a postgres modullal és annak függőségeivel. További konfigurációs információk a [WildFly-kiszolgáló konfigurálása](configure-language-java.md#configure-java-ee-wildfly)című témakörben találhatók.

A kiszolgáló konfigurálásához a következő négy fájlra lesz szükség a *wildfly_config/* könyvtárban:

- **PostgreSQL-42.2.5. jar**: Ez a JAR-fájl a postgres JDBC-illesztőprogramja. További információ: [hivatalos webhely](https://jdbc.postgresql.org/index.html).
- **postgres-Module. XML**: Ez az XML-fájl deklarálja a postgres modul (org. postgres) nevét. Meghatározza továbbá a modul használatához szükséges erőforrásokat és függőségeket is.
- **jboss_cli_commands. CLI**: Ez a fájl olyan konfigurációs parancsokat tartalmaz, amelyek a JBoss CLI-vel lesznek végrehajtva. A parancsok hozzáadja a postgres modult a WildFly-alkalmazáshoz, megadja a hitelesítő adatokat, deklarálja a JNDI nevét, beállítja az időtúllépési küszöbértéket stb. Ha nem ismeri a JBoss CLI-t, tekintse meg a [hivatalos dokumentációt](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli).
- **startup_script. sh**: Végezetül ezt a rendszerhéj-parancsfájlt a rendszer a App Service-példány indításakor hajtja végre. A parancsfájl csak egyetlen függvényt hajt végre: a *jboss_cli_commands. CLI* parancsait a JBoss CLI-be.

Javasoljuk, hogy olvassa el ezeknek a fájloknak a tartalmát, különösen a *jboss_cli_commands. CLI*fájlt.

### <a name="ftp-the-configuration-files"></a>FTP a konfigurációs fájlok

A *wildfly_config és* a app Service-példány TARTALMÁnak FTP-re van szükségük. Az FTP-hitelesítő adatok lekéréséhez kattintson a Azure Portal App Service paneljének **közzétételi profil** beolvasása gombjára. Az FTP-Felhasználónév és a jelszó a letöltött XML-dokumentumba kerül. A közzétételi profillal kapcsolatos további információkért tekintse meg [ezt a dokumentumot](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials).

Egy tetszőleges FTP-eszköz használatával vigye át a négy fájlt a *wildfly_config vagy* a */Home/site/Deployments/Tools/* . (Vegye figyelembe, hogy ne vigye át a könyvtárat, csak a fájlokat.)

### <a name="finalize-app-service"></a>App Service véglegesítése

A App Service panelen navigáljon az "alkalmazás beállításai" panelre. A "Runtime" alatt állítsa be az "indítási fájl" mezőt a */Home/site/Deployments/Tools/startup_script.sh*értékre. Ezzel biztosíthatja, hogy a rendszerhéj-parancsfájl a App Service példány létrehozása után fusson, de a WildFly-kiszolgáló elindítása előtt.

Végezetül indítsa újra a App Service. A gomb az "áttekintés" panelen található.

## <a name="redeploy-the-application"></a>Az alkalmazás újbóli üzembe helyezése

Egy terminál ablakban építse újra az alkalmazást, majd telepítse újra.

```bash
mvn clean install -DskipTests azure-webapp:deploy
```

Gratulálunk! Az alkalmazás mostantól egy postgres-adatbázist használ, és az alkalmazásban létrehozott összes rekord a postgres-ben lesz tárolva, az előző H2 memóriában tárolt adatbázis helyett. Ennek megerősítéséhez létrehozhat egy rekordot, és újraindíthatja a App Service. Az alkalmazás újraindításakor a rekordok továbbra is elérhetők lesznek.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Ha nincs szüksége ezekre az erőforrásokra egy másik oktatóanyaghoz (lásd a következő lépéseket), akkor a következő parancs futtatásával törölheti őket:

```bash
az group delete --name <your-resource-group>
```

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Java EE-alkalmazás üzembe helyezése az Azure-ban a Maven használatával
> * Postgres-adatbázis létrehozása az Azure-ban
> * A WildFly-kiszolgáló konfigurálása a postgres használatára
> * Az alkalmazás frissítése és ismételt üzembe helyezése
> * Egységbeli tesztek futtatása a WildFly

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan képezhető le egyéni DNS-név az alkalmazáshoz.

> [!div class="nextstepaction"]
> [Oktatóanyag: Egyéni DNS-név leképezése az alkalmazáshoz](../app-service-web-tutorial-custom-domain.md)

Vagy tekintse meg a többi erőforrást:

> [!div class="nextstepaction"]
> [Java-alkalmazás konfigurálása](configure-language-java.md)
