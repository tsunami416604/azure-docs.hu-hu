---
title: "Az Azure Java és MySQL webalkalmazás létrehozása"
description: "Útmutató a Java-alkalmazást, amely az Azure-beli MySQL database szolgáltatás működik-e az Azure App Service kapcsolódik."
services: app-service\web
documentationcenter: Java
author: bbenz
manager: jeffsand
editor: jasonwhowell
ms.assetid: 
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: tutorial
ms.date: 05/22/2017
ms.author: bbenz
ms.custom: mvc
ms.openlocfilehash: ad53575b655ebec5a134c8d76b963708caf14334
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2017
---
# <a name="build-a-java-and-mysql-web-app-in-azure"></a>Az Azure Java és MySQL webalkalmazás létrehozása

> [!NOTE]
> Ez a cikk a Windows App Service egy alkalmazást telepíti. Az App Service üzembe _Linux_, lásd: [indexelése rugó rendszerindító alkalmazás telepítése az Azure-bA](/java/azure/spring-framework/deploy-containerized-spring-boot-java-app-with-maven-plugin).
>

Az oktatóanyag bemutatja, hogyan Java-webalkalmazás létrehozása az Azure-ban, és csatlakoztassa a MySQL-adatbázis. Ha elkészült, akkor egy [rugó rendszerindító](https://projects.spring.io/spring-boot/) adattárolásra alkalmazás [MySQL az Azure-adatbázis](https://docs.microsoft.com/azure/mysql/overview) futó [Azure App Service Web Apps](app-service-web-overview.md).

![Java-alkalmazás fusson az Azure App Service](./media/app-service-web-tutorial-java-mysql/appservice-web-app.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * MySQL-adatbázis létrehozása az Azure-ban
> * Egy mintaalkalmazás kapcsolódni az adatbázishoz
> * Az alkalmazás telepítése az Azure-bA
> * Az alkalmazás frissítése és ismételt üzembe helyezése
> * Az adatfolyam diagnosztikai naplók az Azure-ból
> * Figyelheti az alkalmazást az Azure-portálon


## <a name="prerequisites"></a>Előfeltételek

1. [Töltse le és telepítse a Git](https://git-scm.com/)
1. [Töltse le és telepítse a Java-7 JDK vagy újabb](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
1. [Töltse le, telepítse, és indítsa el a MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prepare-local-mysql"></a>Készítse elő a helyi MySQL 

Ebben a lépésben létrehoz egy adatbázis egy helyi MySQL kiszolgálón lévő helyileg a számítógépen alkalmazások tesztelése.

### <a name="connect-to-mysql-server"></a>Csatlakozás a MySQL-kiszolgálóhoz

Az egy terminálablakot a helyi MySQL-kiszolgálóhoz való csatlakozás. Ebben az oktatóanyagban a parancsok futtatásához használhatja a terminálablakot.

```bash
mysql -u root -p
```

Ha kéri a jelszót, írja be a jelszót a `root` fiók. Ha nem emlékszik a rendszergazdafiók jelszavával, lásd: [MySQL: a gyökér szintű jelszó alaphelyzetbe állításával](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html).

Ha a parancs sikeresen lefutott, majd a MySQL-kiszolgáló már fut. Ha nem, győződjön meg arról, hogy a helyi MySQL-kiszolgáló elindult-e követve a [MySQL telepítés utáni lépéseket](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html).

### <a name="create-a-database"></a>Adatbázis létrehozása 

Az a `mysql` kérdés, hozzon létre egy adatbázist és a táblát, a Tennivalólista elemein.

```sql
CREATE DATABASE tododb;
```

Lépjen ki a kiszolgálói kapcsolatot írja be a `quit`.

```sql
quit
```

## <a name="create-and-run-the-sample-app"></a>Hozzon létre, és a mintaalkalmazás futtatása 

Ebben a lépésben klónozza a mintaalkalmazást rugó rendszerindító, konfigurálja úgy, hogy a helyi MySQL-adatbázist használja, és futtassa azt a számítógépen. 

### <a name="clone-the-sample"></a>A minta klónozása

Az terminálablakot keresse meg a munkakönyvtárat, és klónozza a minta-tárházat. 

```bash
git clone https://github.com/azure-samples/mysql-spring-boot-todo
```

### <a name="configure-the-app-to-use-the-mysql-database"></a>A MySQL-adatbázis használata az alkalmazás konfigurálása

Frissítés a `spring.datasource.password` és értéket *spring-boot-mysql-todo/src/main/resources/application.properties* rendelkező a MySQL parancssor megnyitásához használt ugyanazon gyökér szintű jelszavát:

```
spring.datasource.password=mysqlpass
```

### <a name="build-and-run-the-sample"></a>Hozza létre, és futtathatja a

Build, és futtathatja a Maven burkoló szerepel az adattárban használatával:

```bash
cd spring-boot-mysql-todo
mvnw package spring-boot:run
```

Nyissa meg a böngészőben `http://localhost:8080` működés közben minta megtekintéséhez. Feladatok hozzáadása a listához, használja a következő SQL-parancsokat a MySQL kér MySQL tárolt adatok megtekintéséhez.

```SQL
use testdb;
select * from todo_item;
```

Állítsa le az alkalmazást úgy, hogy elérte-e `Ctrl` + `C` a terminálon. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-azure-mysql-database"></a>Hozzon létre egy Azure-beli MySQL database

Ebben a lépésben létrehoz egy [MySQL az Azure-adatbázis](../mysql/quickstart-create-mysql-server-database-using-azure-cli.md) példány használatával a [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). Az adatbázis meg használni az oktatóanyag a mintaalkalmazás konfigurálása

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy [erőforráscsoport](../azure-resource-manager/resource-group-overview.md) rendelkező a [az csoport létrehozása](/cli/azure/group#create) parancsot. Egy Azure erőforráscsoport egy olyan logikai tároló, ahol a kapcsolódó erőforrások, például webalkalmazások, adatbázisok és a storage-fiókok telepítése és kezelése. 

A következő példa egy erőforráscsoportot az Észak-Európa régióban hoz létre:

```azurecli-interactive
az group create --name myResourceGroup --location "North Europe"
```    

A lehetséges értékei, akkor is használja a `--location`, használja a [az App Service lista-helyek](/cli/azure/appservice#list-locations) parancsot.

### <a name="create-a-mysql-server"></a>A MySQL-kiszolgáló létrehozása

A felhő rendszerhéj a kiszolgáló létrehozása az Azure-adatbázisban a MySQL (előzetes verzió) a [az mysql kiszolgáló létrehozni](/cli/azure/mysql/server#create) parancsot.    
A saját egyedi MySQL kiszolgáló nevét, ahol láthatja a `<mysql_server_name>` helyőrző. Ez a név része a MySQL-kiszolgáló állomásneve, `<mysql_server_name>.mysql.database.azure.com`, így kell lennie a globálisan egyedi. Is `<admin_user>` és `<admin_password>` saját értékekkel.

```azurecli-interactive
az mysql server create --name <mysql_server_name> --resource-group myResourceGroup --location "North Europe" --admin-user <admin_user> --admin-password <admin_password>
```

A MySQL-kiszolgáló létrehozása esetén, az Azure parancssori felület kapcsolatos adatokat jeleníti meg az alábbi példához hasonló:

```json
{
  "administratorLogin": "admin_user",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mysql_server_name.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/mysql_server_name",
  "location": "northeurope",
  "name": "mysql_server_name",
  "resourceGroup": "mysqlJavaResourceGroup",
  ...
  < Output has been truncated for readability >
}
```

### <a name="configure-server-firewall"></a>Kiszolgáló tűzfal konfigurálása

A felhő rendszerhéj hozzon létre egy tűzfalszabályt az MySQL-kiszolgálót, hogy lehetővé tegyék az ügyfélkapcsolatokat használatával a [az mysql-tűzfalszabály létrehozása](/cli/azure/mysql/server/firewall-rule#create) parancsot. 

```azurecli-interactive
az mysql server firewall-rule create --name allIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!NOTE]
> MySQL (előzetes verzió) Azure-adatbázis automatikusan jelenleg nem engedélyezi a Azure-szolgáltatásokhoz érkező kapcsolatokat. Mivel a dinamikusan kiosztott IP-címek az Azure-ban, akkor most az összes IP-címek engedélyezése. A szolgáltatás folyamatosan mintájának, biztonságossá tétele az adatbázis jobban módszereinek engedélyezve lesz.

## <a name="configure-the-azure-mysql-database"></a>Konfigurálja az Azure-beli MySQL database

A helyi terminálablakot csatlakozzon a MySQL-kiszolgálóhoz, az Azure-ban. A korábban megadott értéket `<admin_user>` és `<mysql_server_name>`.

```bash
mysql -u <admin_user>@<mysql_server_name> -h <mysql_server_name>.mysql.database.azure.com -P 3306 -p
```

### <a name="create-a-database"></a>Adatbázis létrehozása 

Az a `mysql` kérdés, hozzon létre egy adatbázist és a táblát, a Tennivalólista elemein.

```sql
CREATE DATABASE tododb;
```

### <a name="create-a-user-with-permissions"></a>Hozzon létre egy felhasználói jogosultságokkal

Hozzon létre egy adatbázis-felhasználó, és adjon neki összes jogosultságot a `tododb` adatbázis. Cserélje le a helyőrzőket `<Javaapp_user>` és `<Javaapp_password>` a saját egyedi alkalmazás nevével.

```sql
CREATE USER '<Javaapp_user>' IDENTIFIED BY '<Javaapp_password>'; 
GRANT ALL PRIVILEGES ON tododb.* TO '<Javaapp_user>';
```

Lépjen ki a kiszolgálói kapcsolatot írja be a `quit`.

```sql
quit
```

## <a name="deploy-the-sample-to-azure-app-service"></a>A minta telepítése az Azure App Service

Az Azure App Service-csomagot hozzon létre a **szabad** árképzési szint használatával a [az App Service-csomagot hozzon létre](/cli/azure/appservice/plan#create) CLI parancsot. Az App Service-csomagot határoz meg a fizikai erőforrásokat, az alkalmazások tárolására szolgálnak. Az App Service-csomagra hozzárendelt összes alkalmazás ossza meg ezeket az erőforrásokat, lehetővé téve költség menthetők, ha több alkalmazást üzemeltető. 

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

Amikor készen áll a csomag, az Azure parancssori felület az alábbi példához hasonló kimenetét mutatja be:

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

 A felhő rendszerhéj használata a [az webalkalmazás létrehozása](/cli/azure/appservice/web#create) CLI parancs futtatásával hozzon létre egy webalkalmazás az app-definíciót a `myAppServicePlan` App Service-csomag. A web app-definíciót érhetik el az alkalmazást az URL-CÍMÉT tartalmazza, és beállítja a kód telepítésére, az Azure számos lehetőség közül választhat. 

```azurecli-interactive
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan
```

Helyettesítő a `<app_name>` helyőrző a saját egyedi alkalmazás nevével. A egyedi név része a webes alkalmazás esetén az alapértelmezett tartomány nevét, a nevének egyedinek kell lennie az Azure-ban minden alkalmazások között. A web app egy egyéni tartomány bejegyzése előtt a felhasználók számára közzétenni leképezheti.

Amikor készen áll a web app-definíciót, az Azure parancssori felület információkat jeleníti meg az alábbi példához hasonló: 

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

### <a name="configure-java"></a>Java konfigurálása 

A felhő rendszerhéj a Java runtime konfiguráció az alkalmazást igénylő beállítása a [az App Service web config frissítés](/cli/azure/appservice/web/config#update) parancsot.

A következő parancsot konfigurálása a webalkalmazás a legújabb Java 8 JDK futtathatnak és [Apache Tomcat](http://tomcat.apache.org/) 8.0.

```azurecli-interactive
az webapp config set --name <app_name> --resource-group myResourceGroup --java-version 1.8 --java-container Tomcat --java-container-version 8.0
```

### <a name="configure-the-app-to-use-the-azure-sql-database"></a>Az alkalmazás használatához az Azure SQL adatbázis konfigurálása

A mintaalkalmazás futtatása, előtt a webalkalmazás az Azure-ban létrehozott Azure-beli MySQL-adatbázis használata a Alkalmazásbeállítások be. Ezek a tulajdonságok érhetők el a webes alkalmazás környezeti változóként, és a felülíró belül a csomagolt webalkalmazás application.properties az értékek. 

A felhő rendszerhéj állítsa be az alkalmazás a beállításokat a [az webapp config appsettings](https://docs.microsoft.com/cli/azure/appservice/web/config/appsettings) a a parancssori felület:

```azurecli-interactive
az webapp config appsettings set --settings SPRING_DATASOURCE_URL="jdbc:mysql://<mysql_server_name>.mysql.database.azure.com:3306/tododb?verifyServerCertificate=true&useSSL=true&requireSSL=false" --resource-group myResourceGroup --name <app_name>
```

```azurecli-interactive
az webapp config appsettings set --settings SPRING_DATASOURCE_USERNAME=Javaapp_user@mysql_server_name --resource-group myResourceGroup --name <app_name>
```

```azurecli-interactive
az webapp config appsettings set --settings SPRING_DATASOURCE_PASSWORD=Javaapp_password --resource-group myResourceGroup --name <app_name>
```

### <a name="get-ftp-deployment-credentials"></a>FTP telepítési hitelesítő adatainak lekérése 
Az alkalmazás számára az Azure App Service többek között az FTP, helyi Git, GitHub, a Visual Studio Team Services és a Bitbucketből különböző módokon telepítheti. Ebben a példában az FTP telepítése a. Az Azure App Service a helyi számítógépen korábban létrehozott WAR-fájlt.

Határozhatja meg, milyen hitelesítő adatokat adják át egy FTP-parancsot a azt a webalkalmazást, [az App Service web deployment lista-közzététel-profilok](https://docs.microsoft.com/cli/azure/appservice/web/deployment#az_appservice_web_deployment_list_publishing_profiles) a felhő rendszerhéj parancsot: 

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

### <a name="upload-the-app-using-ftp"></a>FTP-használ, az alkalmazás feltöltése

A kedvenc FTP-eszköz segítségével telepítheti a. A WAR-fájlt a */site/wwwroot/webapps* vett kiszolgálócímet mappájába a `URL` mező mellett az előző parancsot. Távolítsa el a meglévő alapértelmezett (ROOT) alkalmazás könyvtárat, és cserélje le a meglévő ROOT.war a. Az oktatóanyag korábbi beépített WAR-fájlt.

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

### <a name="test-the-web-app"></a>A webes alkalmazás tesztelése

Keresse meg a `http://<app_name>.azurewebsites.net/` és néhány feladatot hozzáadása a listához. 

![Java-alkalmazás fusson az Azure App Service](./media/app-service-web-tutorial-java-mysql/appservice-web-app.png)

**Gratulálunk!** Adatalapú Java-alkalmazást használ az Azure App Service-ben.

## <a name="update-the-app-and-redeploy"></a>Az alkalmazás frissítése és ismételt üzembe helyezése

Frissítse az alkalmazás egy további oszlopot felvenni a teendőlista milyen napon létrehozták. Rugó rendszerindító kezeli frissítésekor az adatbázis-séma meg az adatok modell módosítás a meglévő üzenetadatbázis-rekordok módosítása nélkül.

1. A helyi számítógépen nyissa meg *src/main/java/com/example/fabrikam/TodoItem.java* osztály adja hozzá az alábbi importálásokat:   

    ```java
    import java.text.SimpleDateFormat;
    import java.util.Calendar;
    ```

2. Adja hozzá a `String` tulajdonság `timeCreated` való *src/main/java/com/example/fabrikam/TodoItem.java*, inicializálja azt az időbélyegzőnek a objektum létrehozásakor. Beolvasókat/Setter elemek hozzáadása az új `timeCreated` tulajdonság a fájl szerkesztése közben.

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

3. Frissítés *src/main/java/com/example/fabrikam/TodoDemoController.java* a vonallal a `updateTodo` módszer beállítása az időbélyeg:

    ```java
    item.setComplete(requestItem.isComplete());
    item.setId(requestItem.getId());
    item.setTimeCreated(requestItem.getTimeCreated());
    repository.save(item);
    ```

4. Támogatás az új mező hozzáadása a Thymeleaf sablonban. Frissítés *src/main/resources/templates/index.html* a Timestamp típusú, és a Timestamp érték megjelenő egyes táblazatsorok adatok új mező egy új tábla fejléccel.

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

5. Építse újra az alkalmazást:

    ```bash
    mvnw clean package 
    ```

6. Az FTP-a frissített. WAR, mint a meglévő eltávolítása *hely/wwwroot/webapps/ROOT* könyvtár és *ROOT.war*, majd a frissített feltöltése. Mint ROOT.war WAR-fájlt. 

Az alkalmazás frissítésekor egy **alkalommal létre** oszlop már látható. Ha egy új feladatot ad hozzá, az alkalmazás automatikusan a Timestamp típusú fogja majd feltölteni. A meglévő feladatokat továbbra is változatlan és a munkahelyi és az alkalmazás, annak ellenére, hogy a mögöttes adatmodell módosult. 

![Java-alkalmazást egy olyan új oszlop frissült](./media/app-service-web-tutorial-java-mysql/appservice-updates-java.png)
      
## <a name="stream-diagnostic-logs"></a>Diagnosztikai naplók adatfolyam 

Míg a Java-alkalmazások az Azure App Service-ben, közvetlenül a terminálon az adatcsatornán konzolnaplófájlokban kaphat. Ily módon kaphat segítséget nyújtanak az alkalmazáshibák debug diagnosztikai ugyanazokat az üzeneteket.

Napló streaming indításához használja a [az webapp napló végéről](/cli/azure/webapp/log?view=azure-cli-latest#az_webapp_log_tail) a felhő rendszerhéj parancsot.

```azurecli-interactive 
az webapp log tail --name <app_name> --resource-group myResourceGroup 
``` 

## <a name="manage-your-azure-web-app"></a>Az Azure-webalkalmazásban kezelése

Ugrás az Azure-portálon létrehozott webalkalmazás megjelenítéséhez.

Ehhez jelentkezzen be a következő címen: [https://portal.azure.com](https://portal.azure.com).

A bal oldali menüben kattintson az **App Service** lehetőségre, majd az Azure-webapp nevére.

![Navigálás a portálon az Azure-webapphoz](./media/app-service-web-tutorial-java-mysql/access-portal.png)

Alapértelmezés szerint a webapp panelje az **Áttekintés** oldalt mutatja. Ezen az oldalon megtekintheti az alkalmazás állapotát. Itt is elvégezheti stop, kezdő, újraindítását és delete felügyeleti feladatokhoz. A panel bal oldalán lévő lapok a különböző megnyitható konfigurációs oldalakat jelenítik meg.

![Az App Service panel az Azure Portalon](./media/app-service-web-tutorial-java-mysql/web-app-blade.png)

A panel ezen lapja a webapphoz hozzáadható nagyszerű szolgáltatásokat jelenítik meg. Az alábbi lista csupán néhány lehetőséget sorol fel:
* Egyéni DNS-név leképezése
* Egyéni SSL-tanúsítvány kötése
* Folyamatos üzembe helyezés konfigurálása
* Vertikális felskálázás és kibővítés
* Felhasználói hitelesítés hozzáadása

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha ezeket az erőforrásokat egy másik az oktatóanyaghoz nincs szüksége (lásd: [további lépések](#next)), a felhő rendszerhéj a következő parancs futtatásával törölheti: 
  
```azurecli-interactive
az group delete --name myResourceGroup 
``` 

<a name="next"></a>

## <a name="next-steps"></a>Következő lépések

> [!div class="checklist"]
> * MySQL-adatbázis létrehozása az Azure-ban
> * Egy minta Java-alkalmazás csatlakoztatása a MySQL
> * Az alkalmazás telepítése az Azure-bA
> * Az alkalmazás frissítése és ismételt üzembe helyezése
> * Az adatfolyam diagnosztikai naplók az Azure-ból
> * Felügyelheti az alkalmazást az Azure-portálon

Előzetes megtudhatja, hogyan képezheti egy egyéni DNS-nevet az alkalmazás következő oktatóanyagot.

> [!div class="nextstepaction"] 
> [Meglévő egyéni DNS-név hozzákapcsolása az Azure-webalkalmazásokhoz](app-service-web-tutorial-custom-domain.md)
