---
title: Java- és MySQL-webalkalmazás létrehozása az Azure-ban
description: Megtudhatja, hogyan állíthat üzembe egy, az Azure MySQL-adatbázisszolgáltatáshoz csatlakozó Java-alkalmazást az Azure App Service-ben.
services: app-service\web
documentationcenter: Java
author: bbenz
manager: jeffsand
editor: jasonwhowell
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: tutorial
ms.date: 05/22/2017
ms.author: bbenz
ms.custom: mvc
ms.openlocfilehash: 5e1d7a88e5a1a8ab60a01aea6ca42e850ac6e0e3
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2018
ms.locfileid: "41919133"
---
# <a name="tutorial-build-a-java-and-mysql-web-app-in-azure"></a>Oktatóanyag: Java- és MySQL-webalkalmazás létrehozása az Azure-ban

> [!NOTE]
> Ebben a cikkben egy alkalmazást helyezünk üzembe a Windowson futó App Service-ben. Az App Service _Linuxon_ való üzembe helyezésével kapcsolatban lásd a [tárolóalapú Spring Boot-alkalmazás az Azure-ban történő üzembe helyezését](/java/azure/spring-framework/deploy-containerized-spring-boot-java-app-with-maven-plugin) ismertető szakaszt.
>

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy Java-webalkalmazást az Azure-ban, és hogyan csatlakoztathatja azt egy MySQL-adatbázishoz. Az oktatóanyag végére egy, az [Azure App Service Web Appsben](app-service-web-overview.md) futó, az adatokat az [Azure Database for MySQL-ben](https://docs.microsoft.com/azure/mysql/overview) tároló [Spring Boot](https://projects.spring.io/spring-boot/)-alkalmazással fog rendelkezni.

![Az Azure App Service-ben futó Java-alkalmazás](./media/app-service-web-tutorial-java-mysql/appservice-web-app.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * MySQL-adatbázis létrehozása az Azure-ban
> * Mintaalkalmazás csatlakoztatása az adatbázishoz
> * Az alkalmazás üzembe helyezése az Azure-ban
> * Az alkalmazás frissítése és ismételt üzembe helyezése
> * Diagnosztikai naplók streamelése az Azure-ból
> * Az alkalmazás monitorozása az Azure Portalon

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

1. [A git letöltése és telepítése](https://git-scm.com/)
1. [A Java 7 JDK vagy egy újabb verzió letöltése és telepítése](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
1. [A MySQL letöltése, telepítése és elindítása](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 

## <a name="prepare-local-mysql"></a>A helyi MySQL előkészítése 

Ebben a lépésben létrehozunk egy adatbázist egy helyi MySQL-kiszolgálón az alkalmazás helyi, a számítógépen történő teszteléséhez.

### <a name="connect-to-mysql-server"></a>Csatlakozás a MySQL-kiszolgálóhoz

Csatlakozzon a helyi MySQL-kiszolgálóhoz egy terminálablakban. Ezzel a terminálablakkal futtathatja az oktatóanyagban szereplő összes parancsot.

```bash
mysql -u root -p
```

Ha a rendszer jelszót kér, adja meg a `root` fiókhoz tartozó jelszót. Ha nem emlékszik a rendszergazdafiók jelszavára, tekintse meg a [MySQL-ben a rendszergazdai jelszó alaphelyzetbe állítását](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html) ismertető cikket.

Ha a parancs sikeresen lefut, a MySQL-kiszolgáló már fut. Ha nem, mindenképp a [MySQL telepítése utáni lépéseket](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html) követve indítsa el a helyi MySQL-kiszolgálót.

### <a name="create-a-database"></a>Adatbázis létrehozása 

A `mysql` parancssorában hozzon létre egy adatbázist, valamint egy táblát a teendők számára.

```sql
CREATE DATABASE tododb;
```

A kiszolgálókapcsolat bontásához írja be a `quit` parancsot.

```sql
quit
```

## <a name="create-and-run-the-sample-app"></a>A mintaalkalmazás létrehozása és futtatása 

Ebben a lépésben klónozzuk a Spring Boot-mintaalkalmazást, konfiguráljuk a helyi MySQL-adatbázis használatára, majd futtatjuk a számítógépen. 

### <a name="clone-the-sample"></a>A minta klónozása

A terminálablakban lépjen egy munkakönyvtárra, és klónozza a mintaadattárat. 

```bash
git clone https://github.com/azure-samples/mysql-spring-boot-todo
```

### <a name="configure-the-app-to-use-the-mysql-database"></a>Az alkalmazás konfigurálása a MySQL-adatbázis használatára

Frissítse a `spring.datasource.password` jelszót és értéket a *spring-boot-mysql-todo/src/main/resources/application.properties* helyen a MySQL-parancssor megnyitásához használt rendszergazdai jelszóra:

```
spring.datasource.password=mysqlpass
```

### <a name="build-and-run-the-sample"></a>A minta létrehozása és futtatása

Hozza létre és futtassa a mintát az adattárban foglalt Maven burkoló használatával:

```bash
cd spring-boot-mysql-todo
mvnw package spring-boot:run
```

Nyissa meg a böngészőben a `http://localhost:8080` címet, és ellenőrizze a minta működését. Ahogy feladatokat ad hozzá a listához, a MySQL-parancssorban a következő SQL-parancsokkal tekintheti meg a MySQL-ben tárolt adatokat.

```SQL
use testdb;
select * from todo_item;
```

Állítsa le az alkalmazást a terminálban kiadott `Ctrl`+`C` billentyűparanccsal. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-azure-mysql-database"></a>Azure MySQL-adatbázis létrehozása

Ebben a lépésben egy [Azure Database for MySQL](../mysql/quickstart-create-mysql-server-database-using-azure-cli.md)-példányt hozunk létre az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) használatával. Később az oktatóanyag során konfigurálni fogjuk a mintaalkalmazást ennek az adatbázisnak a használatára.

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy [erőforráscsoportot](../azure-resource-manager/resource-group-overview.md) az [`az group create`](/cli/azure/group#az-group-create) paranccsal. Az Azure-erőforráscsoport egy olyan logikai tároló, amelyben a rendszer üzembe helyezi és kezeli a kapcsolódó erőforrásokat (például webalkalmazásokat, adatbázisokat és tárfiókokat). 

A következő példában az Észak-Európa régióban hozunk létre egy erőforráscsoportot:

```azurecli-interactive
az group create --name myResourceGroup --location "North Europe"
```    

A `--location` paraméterhez használható lehetséges értékek megtekintéséhez, használja az [`az appservice list-locations`](/cli/azure/appservice#list-locations) parancsot.

### <a name="create-a-mysql-server"></a>MySQL-kiszolgáló létrehozása

A Cloud Shellben hozzon létre egy kiszolgálót az Azure Database for MySQL szolgáltatásban az [`az mysql server create`](/cli/azure/mysql/server?view=azure-cli-latest#az-mysql-server-create) paranccsal.

Az alábbi parancsban írjon egy egyedi kiszolgálónevet a *\<mysql_server_name>*, egy felhasználónevet az *\<admin_user>*, valamint egy jelszót az *\<admin_password>* helyőrző helyére. A kiszolgálónév a postgreSQL-végpont (`https://<mysql_server_name>.mysql.database.azure.com`) részét képezi majd, így egyedi kiszolgálónévnek kell lennie a teljes Azure-ban.

```azurecli-interactive
az mysql server create --resource-group myResourceGroup --name <mysql_server_name>--location "West Europe" --admin-user <admin_user> --admin-password <server_admin_password> --sku-name GP_Gen4_2
```

> [!NOTE]
> Mivel az oktatóanyag több hitelesítő adattal dolgozik, a félreértések elkerülése érdekében az `--admin-user` és az `--admin-password` helyőrző értékekre van beállítva. Éles környezet esetén kövesse az ajánlott biztonsági eljárásokat, amikor megfelelő felhasználónevet és jelszót választ az Azure-ban található MySQL-kiszolgálóhoz.
>
>

A MySQL-kiszolgáló létrehozása után az Azure CLI az alábbi példához hasonló információkat jelenít meg:

```json
{
  "location": "westeurope",
  "name": "<mysql_server_name>",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "additionalProperties": {},
    "capacity": 2,
    "family": "Gen4",
    "name": "GP_Gen4_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  ...   +  
  -  < Output has been truncated for readability >
}
```

### <a name="configure-server-firewall"></a>Kiszolgáló tűzfalának konfigurálása

A Cloud Shellben az [`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-create) parancs használatával hozzon létre egy tűzfalszabályt a MySQL-kiszolgáló számára az ügyfélkapcsolatok engedélyezésére. Ha a kezdő IP-cím és a záró IP-cím is 0.0.0.0 értékre van állítva, a tűzfal csak más Azure-erőforrások számára van nyitva. 

```azurecli-interactive
az mysql server firewall-rule create --name allAzureIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP] 
> Még szigorúbb tűzfalszabályt is megadhat, ha [csak azokat a kimenő IP-címeket használja, amelyeket alkalmazása használ](app-service-ip-addresses.md#find-outbound-ips).
>

## <a name="configure-the-azure-mysql-database"></a>Az Azure MySQL-adatbázis konfigurálása

A helyi terminálablakban csatlakozzon az Azure-ban található MySQL-kiszolgálóhoz. A _&lt;mysql_server_name>_ helyőrzőnél használja a korábban megadott értéket. Amikor a rendszer jelszót kér, használja azt a jelszót, amelyet az adatbázis létrehozásakor adott meg az Azure-ban.

```bash
mysql -u <admin_user>@<mysql_server_name> -h <mysql_server_name>.mysql.database.azure.com -P 3306 -p
```

### <a name="create-a-database"></a>Adatbázis létrehozása 

A `mysql` parancssorában hozzon létre egy adatbázist, valamint egy táblát a teendők számára.

```sql
CREATE DATABASE tododb;
```

### <a name="create-a-user-with-permissions"></a>Engedélyekkel rendelkező felhasználó létrehozása

Hozzon létre egy adatbázis-felhasználót, és adja meg neki az összes jogosultságot a `tododb` adatbázisban. A `<Javaapp_user>` és a `<Javaapp_password>` helyőrző helyébe a saját egyedi alkalmazásnevét és jelszavát írja.

```sql
CREATE USER '<Javaapp_user>' IDENTIFIED BY '<Javaapp_password>'; 
GRANT ALL PRIVILEGES ON tododb.* TO '<Javaapp_user>';
```

A kiszolgálókapcsolat bontásához írja be a `quit` parancsot.

```sql
quit
```

## <a name="deploy-the-sample-to-azure-app-service"></a>A minta üzembe helyezése az Azure App Service-ben

Az [`az appservice plan create`](/cli/azure/appservice/plan#az-appservice-plan-create) CLI-paranccsal hozzon létre egy Azure App Service-csomagot **INGYENES** tarifacsomaggal. Az App Service-csomag határozza meg az alkalmazások üzemeltetéséhez használt fizikai erőforrásokat. Az App Service-csomaghoz rendelt összes alkalmazás ugyanezeket az erőforrásokat használja, így több alkalmazás üzemeltetése esetén is csökkenthetők a költségek. 

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

Amikor készen áll a csomag, az Azure CLI az alábbi példához hasonló kimenetet jelenít meg:

```json
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "North Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "North Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  ...
  < Output has been truncated for readability >
} 
``` 

### <a name="create-an-azure-web-app"></a>Azure-webalkalmazás létrehozása

A Cloud Shellben az [`az webapp create`](/cli/azure/webapp#az-webapp-create) CLI-paranccsal hozzon létre egy webalkalmazás-definíciót a `myAppServicePlan` App Service-csomagban. A webalkalmazás-definíció egy URL-címet biztosít, amelyen keresztül az alkalmazás elérhető, valamint több beállítást is konfigurál a kód az Azure-ban történő üzembe helyezéséhez. 

```azurecli-interactive
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan
```

Az `<app_name>` helyőrző helyébe a saját egyedi alkalmazásnevét írja. Ez az egyedi név a webalkalmazás alapértelmezett tartománynevének részét képezi majd, így egyedi alkalmazásnévnek kell lennie a teljes Azure-ban. Leképezhet egy egyéni tartománynév-bejegyzést a webalkalmazásához, mielőtt elérhetővé teszi a felhasználók számára.

A webalkalmazás-definíció megadása után az Azure CLI az alábbi példához hasonló információkat jelenít meg: 

```json 
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
   ...
  < Output has been truncated for readability >
}
```

### <a name="configure-java"></a>A Java konfigurálása 

A Cloud Shellben az [`az webapp config set`](/cli/azure/webapp/config#az-webapp-config-set) paranccsal állítsa be a Java-futtatókörnyezet az alkalmazás által igényelt konfigurációját.

Az alábbi parancs a webalkalmazást arra konfigurálja, hogy az újabb Java 8 JDK-n és az [Apache Tomcat](http://tomcat.apache.org/) 8.0-s verzióján fusson.

```azurecli-interactive
az webapp config set --name <app_name> --resource-group myResourceGroup --java-version 1.8 --java-container Tomcat --java-container-version 8.0
```

### <a name="configure-the-app-to-use-the-azure-sql-database"></a>Az alkalmazás konfigurálása az Azure SQL-adatbázis használatára

A mintaalkalmazás futtatása előtt állítsa be a webalkalmazás alkalmazásbeállításait, hogy az az Azure-ban létrehozott Azure MySQL-adatbázist használja. Ezek a tulajdonságok környezeti változóként érhetők el a webalkalmazás által, és felülbírálják a csomagolt webalkalmazás application.properties tulajdonságaiban megadott értékeket. 

A Cloud Shellben állítsa be az alkalmazásbeállításokat az [`az webapp config appsettings`](https://docs.microsoft.com/cli/azure/webapp/config/appsettings) parancssori parancs kiadásával:

```azurecli-interactive
az webapp config appsettings set --settings SPRING_DATASOURCE_URL="jdbc:mysql://<mysql_server_name>.mysql.database.azure.com:3306/tododb?verifyServerCertificate=true&useSSL=true&requireSSL=false" --resource-group myResourceGroup --name <app_name>
```

```azurecli-interactive
az webapp config appsettings set --settings SPRING_DATASOURCE_USERNAME=Javaapp_user@mysql_server_name --resource-group myResourceGroup --name <app_name>
```

```azurecli-interactive
az webapp config appsettings set --settings SPRING_DATASOURCE_PASSWORD=Javaapp_password --resource-group myResourceGroup --name <app_name>
```

### <a name="get-ftp-deployment-credentials"></a>FTP-s üzembe helyezés hitelesítő adatainak lekérése 
Az alkalmazás az Azure App Service-ben való üzembe helyezésének számos módja létezik, így például az FTP, a helyi Git, a GitHub, a Visual Studio Team Services és a BitBucket. Példánkban FTP-n keresztül helyezzük üzembe a korábban a helyi gépen létrehozott .WAR-fájlt az Azure App Service-ben.

Ha szeretné kideríteni, hogy milyen hitelesítő adatokat kell megadni az FTP-parancsban a webalkalmazásnak, használja a Cloud Shellben az [`az appservice web deployment list-publishing-profiles`](https://docs.microsoft.com/cli/azure/webapp/deployment#az-appservice-web-deployment-list-publishing-profiles) parancsot: 

```azurecli-interactive
az webapp deployment list-publishing-profiles --name <app_name> --resource-group myResourceGroup --query "[?publishMethod=='FTP'].{URL:publishUrl, Username:userName,Password:userPWD}" --output json
```

```JSON
[
  {
    "Password": "aBcDeFgHiJkLmNoPqRsTuVwXyZ",
    "URL": "ftp://waws-prod-blu-069.ftp.azurewebsites.windows.net/site/wwwroot",
    "Username": "app_name\\$app_name"
  }
]
```

### <a name="upload-the-app-using-ftp"></a>Az alkalmazás feltöltése FTP használatával

Kedvenc FTP-eszközével telepítse a .WAR-fájlt a */site/wwwroot/webapps* mappába az előző parancs `URL` mezőjéből származó kiszolgálócímen. Távolítsa el a meglévő alapértelmezett (ROOT) alkalmazáskönyvtárat, és cserélje le a meglévő ROOT.war fájlt az oktatóanyag korábbi részében létrehozott .WAR-fájlra.

```bash
ftp waws-prod-blu-069.ftp.azurewebsites.windows.net
Connected to waws-prod-blu-069.drip.azurewebsites.windows.net.
220 Microsoft FTP Service
Name (waws-prod-blu-069.ftp.azurewebsites.windows.net:raisa): app_name\$app_name
331 Password required
Password:
cd /site/wwwroot/webapps
mdelete -i ROOT/*
rmdir ROOT/
put target/TodoDemo-0.0.1-SNAPSHOT.war ROOT.war
```

### <a name="test-the-web-app"></a>A webalkalmazás tesztelése

Egy böngészőben keresse fel az `http://<app_name>.azurewebsites.net/` címet, és vegyen fel néhány feladatot a listára. 

![Az Azure App Service-ben futó Java-alkalmazás](./media/app-service-web-tutorial-java-mysql/appservice-web-app.png)

**Gratulálunk!** Egy adatvezérelt Java-alkalmazást futtat az Azure App Service-ben.

## <a name="update-the-app-and-redeploy"></a>Az alkalmazás frissítése és ismételt üzembe helyezése

Módosítsa az alkalmazást, és vegyen fel egy további oszlopot a teendők listájába, amely azt mutatja, hogy az egyes elemek mely napon lettek létrehozva. A Spring Boots az adatmodell változásainak megfelelően frissíti Ön helyett az adatbázissémát anélkül, hogy módosítaná a meglévő adatbázisrekordokat.

1. A helyi számítógépen nyissa meg az *src/main/java/com/example/fabrikam/TodoItem.java* fájlt, és adja hozzá az alábbi importált elemeket az osztályhoz:   

    ```java
    import java.text.SimpleDateFormat;
    import java.util.Calendar;
    ```

2. Adja hozzá a `timeCreated` `String`-tulajdonságot az *src/main/java/com/example/fabrikam/TodoItem.java* fájlhoz, amely egy időbélyegzővel látja majd el azt az objektumok létrehozásakor. Adjon hozzá beolvasókat/beállítókat az új `timeCreated` tulajdonsághoz a fájl szerkesztésekor.

    ```java
    private String name;
    private boolean complete;
    private String timeCreated;
    ...

    public TodoItem(String category, String name) {
       this.category = category;
       this.name = name;
       this.complete = false;
       this.timeCreated = new SimpleDateFormat("MMMM dd, YYYY").format(Calendar.getInstance().getTime());
    }
    ...
    public void setTimeCreated(String timeCreated) {
       this.timeCreated = timeCreated;
    }

    public String getTimeCreated() {
        return timeCreated;
    }
    ```

3. Frissítse az *src/main/java/com/example/fabrikam/TodoDemoController.java* fájlt az `updateTodo` metódus egy sorával az időbélyeg beállításához:

    ```java
    item.setComplete(requestItem.isComplete());
    item.setId(requestItem.getId());
    item.setTimeCreated(requestItem.getTimeCreated());
    repository.save(item);
    ```

4. Adja hozzá az új mező támogatását a `Thymeleaf` sablonban. Frissítse az *src/main/resources/templates/index.html* fájlt az időbélyeg új fejlécével, valamint egy új mezővel, amely az időbélyeg értékét jeleníti meg a tábla egyes soraiban.

    ```html
    <th>Name</th>
    <th>Category</th>
    <th>Time Created</th>
    <th>Complete</th>
    ...
    <td th:text="${item.category}">item_category</td><input type="hidden" th:field="*{todoList[__${i.index}__].category}"/>
    <td th:text="${item.timeCreated}">item_time_created</td><input type="hidden" th:field="*{todoList[__${i.index}__].timeCreated}"/>
    <td><input type="checkbox" th:checked="${item.complete} == true" th:field="*{todoList[__${i.index}__].complete}"/></td>
    ```

5. Hozza újra létre az alkalmazást:

    ```bash
    mvnw clean package 
    ```

6. FTP-n töltse fel a frissített .WAR-fájlt, ahogy korábban is – ehhez törölje a meglévő *site/wwwroot/webapps/ROOT* könyvtárat és a *ROOT.war* fájlt, majd töltse fel a frissített .WAR-fájlt ROOT.war fájlként. 

Az alkalmazás frissítésekor megjelenik a **Time Created** (Létrehozás időpontja) oszlop. Ha egy új feladatot ad hozzá, az alkalmazás automatikusan kitölti az időbélyeg mezőjét. A meglévő feladatok nem változnak, és ugyanúgy működnek az alkalmazással, holott a mögöttes adatmodell módosult. 

![Java-alkalmazás egy új oszloppal frissítve](./media/app-service-web-tutorial-java-mysql/appservice-updates-java.png)
      
## <a name="stream-diagnostic-logs"></a>Diagnosztikai naplók streamelése 

Bár a Java-alkalmazás az Azure App Service-ben fut, a konzolnaplófájlokat megkaphatja közvetlenül a terminálban is. Így ugyanazokat a diagnosztikai üzeneteket kaphatja meg az alkalmazáshibák elhárításához.

A naplóstreamelés indításához használja az [`az webapp log tail`](/cli/azure/webapp/log?view=azure-cli-latest#az-webapp-log-tail) parancsot a Cloud Shellben.

```azurecli-interactive 
az webapp log tail --name <app_name> --resource-group myResourceGroup 
``` 

## <a name="manage-your-azure-web-app"></a>Az Azure-webalkalmazás kezelése

Lépjen az [Azure Portalra](https://portal.azure.com), és tekintse meg a létrehozott webalkalmazást.

A bal oldali menüben kattintson az **App Service** lehetőségre, majd az Azure-webapp nevére.

![Navigálás a portálon az Azure-webapphoz](./media/app-service-web-tutorial-java-mysql/access-portal.png)

Alapértelmezés szerint a webalkalmazás az **Áttekintés** oldalt mutatja. Ezen az oldalon megtekintheti az alkalmazás állapotát. Itt elvégezhet olyan felügyeleti feladatokat is, mint a leállítás, elindítás, újraindítás és törlés. Az oldal bal oldalán lévő lapok a különböző megnyitható konfigurációs oldalakat jelenítik meg.

![Az App Service lap az Azure Portalon](./media/app-service-web-tutorial-java-mysql/web-app-blade.png)

Az oldal ezen lapjai a webalkalmazáshoz hozzáadható nagyszerű funkciókat jelenítik meg. Az alábbi lista csupán néhány lehetőséget sorol fel:
* Egyéni DNS-név leképezése
* Egyéni SSL-tanúsítvány kötése
* Folyamatos üzembe helyezés konfigurálása
* Vertikális felskálázás és kibővítés
* Felhasználói hitelesítés hozzáadása

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha ezekre az erőforrásokra már nincs szüksége más oktatóanyagokhoz (lásd a [következő lépéseket](#next)), az alábbi parancs a Cloud Shellben való futtatásával törölheti azokat: 
  
```azurecli-interactive
az group delete --name myResourceGroup 
``` 

<a name="next"></a>

## <a name="next-steps"></a>További lépések

> [!div class="checklist"]
> * MySQL-adatbázis létrehozása az Azure-ban
> * Java-mintaalkalmazás csatlakoztatása a MySQL-hez
> * Az alkalmazás üzembe helyezése az Azure-ban
> * Az alkalmazás frissítése és ismételt üzembe helyezése
> * Diagnosztikai naplók streamelése az Azure-ból
> * Az alkalmazás kezelése az Azure Portalon

Lépjen a következő oktatóanyaghoz, amelyből megtudhatja, hogyan képezhet le egyedi DNS-nevet az alkalmazásokhoz.

> [!div class="nextstepaction"] 
> [Meglévő egyéni DNS-név hozzákapcsolása az Azure-webalkalmazásokhoz](app-service-web-tutorial-custom-domain.md)
