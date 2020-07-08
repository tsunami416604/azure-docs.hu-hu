---
title: Átmeneti környezet beállítása az Azure Spring Cloud-ban | Microsoft Docs
description: Ismerje meg, hogyan használható a kék-zöld üzembe helyezés az Azure Spring Cloud használatával
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: brendm
ms.openlocfilehash: 2e29f6a75b303518ac34ecf9b570bd7638cf0c3a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "79471030"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>Átmeneti környezet beállítása az Azure Spring Cloud-ban

Ebből a cikkből megtudhatja, hogyan állíthatja be az átmeneti üzembe helyezést a kék-zöld üzembe helyezési minta használatával az Azure Spring Cloud-ban. A kék/zöld üzembe helyezés egy Azure DevOps folyamatos kézbesítési minta, amelynek lényege, hogy működésben tart egy meglévő (kék) verziót, miközben üzembe helyez egy új (zöld) verziót. Ebből a cikkből megtudhatja, hogyan helyezheti üzembe az előkészítési telepítést éles környezetben anélkül, hogy az éles üzembe helyezést közvetlenül módosítaná.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy már üzembe helyezte a PiggyMetrics alkalmazást az [Azure Spring Cloud-alkalmazás elindításáról szóló oktatóanyagban](spring-cloud-quickstart-launch-app-portal.md). A PiggyMetrics három alkalmazást tartalmaz: "Gateway", "Account-Service" és "Auth-Service".  

Ha ehhez a példához egy másik alkalmazást szeretne használni, egyszerű módosítást kell végeznie az alkalmazás nyilvános részén.  Ez a változás megkülönbözteti az átmeneti üzembe helyezést az éles környezetben.

>[!TIP]
> A Azure Cloud Shell egy ingyenes interaktív felület, amelyet a cikkben szereplő utasítások futtatására használhat.  Gyakori, előre telepített Azure-eszközöket tartalmaz, beleértve a git, a JDK, a Maven és az Azure CLI legújabb verzióit. Ha bejelentkezett az Azure-előfizetésbe, indítsa el a [Azure Cloud Shell](https://shell.azure.com).  További információ: [Azure Cloud Shell áttekintése](../cloud-shell/overview.md).

Ha átmeneti környezetet szeretne beállítani az Azure Spring Cloud-ban, kövesse a következő szakasz utasításait.

## <a name="install-the-azure-cli-extension"></a>Az Azure CLI-bővítmény telepítése

Telepítse az Azure CLI-hez készült Azure Spring Cloud-bővítményt az alábbi paranccsal:

```azurecli
az extension add --name spring-cloud
```
    
## <a name="view-all-deployments"></a>Az összes üzemelő példány megtekintése

Nyissa meg a szolgáltatási példányt a Azure Portalban, és válassza a **központi telepítés kezelése** lehetőséget az összes központi telepítés megtekintéséhez. További részletek megtekintéséhez kiválaszthatja az egyes központi telepítéseket.

## <a name="create-a-staging-deployment"></a>Előkészítési központi telepítés létrehozása

1. A helyi fejlesztési környezetben végezze el a PiggyMetrics-átjáró alkalmazás kis módosítását. Például módosítsa az *átjáró/forrás/fő/erőforrás/statikus/CSS/Launch. css* fájl színét. Így egyszerűen megkülönböztetheti a két üzemelő példányt. A jar-csomag létrehozásához futtassa a következő parancsot: 

    ```console
    mvn clean package
    ```

1. Az Azure CLI-ben hozzon létre egy új központi telepítést, és adja meg a "zöld" átmeneti telepítési nevet.

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app gateway -n green --jar-path gateway/target/gateway.jar
    ```

1. Az üzembe helyezés sikeres befejeződése után nyissa meg az átjáró lapját az **alkalmazás irányítópultján**, és tekintse meg az összes példányát a bal oldali **alkalmazás példányai** lapon.
  
> [!NOTE]
> A felderítési állapot *OUT_OF_SERVICE* , így az ellenőrzés befejeződése előtt a rendszer nem irányítja át a forgalmat ehhez a központi telepítéshez.

## <a name="verify-the-staging-deployment"></a>Az átmeneti telepítés ellenőrzése

1. Térjen vissza a **központi telepítés kezelése** lapra, és válassza ki az új központi telepítést. Az üzembe helyezési állapotnak *futnia*kell. A **tartomány kiosztása/megszüntetése** gomb szürkén jelenik meg, mert a környezet átmeneti környezet.

1. Az **Áttekintés** ablaktáblán egy **teszt végpontot**kell látnia. Másolja és illessze be egy új böngészőablakba, és megjelenik az új PiggyMetrics oldal.

>[!TIP]
> * Győződjön meg arról, hogy a tesztelési végpont perjel (/) karakterrel végződik, hogy a CSS-fájl megfelelően van-e betöltve.  
> * Ha a böngésző megköveteli a bejelentkezési hitelesítő adatok megadását a lap megtekintéséhez, használja az [URL-címet](https://www.urldecoder.org/) a teszt végpont dekódolásához. Az URL-cím dekódolása egy "https:// \<username> : \<password> @ \<cluster-name> . test.azureapps.IO/Gateway/Green" formátumú URL-címet ad vissza.  Ezt az űrlapot használhatja a végpont eléréséhez.

>[!NOTE]    
> A konfigurációs kiszolgáló beállításai az átmeneti környezetre és a termelésre egyaránt érvényesek. Ha például az alkalmazás-átjáró környezeti elérési útját ( `server.servlet.context-path` ) a konfigurációs kiszolgálón a *somepath*értékre állítja, a zöld telepítés elérési útja a "https:// \<username> : \<password> @ \<cluster-name> . test.azureapps.IO/Gateway/Green/somepath/..." értékre változik.
 
 Ha ezen a ponton látogatja meg a nyilvános alkalmazás-átjárót, az új módosítás nélkül látnia kell a régi oldalt.
    
## <a name="set-the-green-deployment-as-the-production-environment"></a>A zöld telepítés beállítása éles környezetként

1. Miután ellenőrizte a változást az átmeneti környezetben, leküldheti azt az éles környezetbe. Térjen vissza a **központi telepítés felügyeletéhez**, és jelölje be az **átjáró** alkalmazás jelölőnégyzetet.

2. Válassza a **telepítés beállítása**lehetőséget.
3. Az **éles üzembe helyezés** listában válassza a **zöld**lehetőséget, majd kattintson az **alkalmaz**gombra.
4. Nyissa meg az átjáró alkalmazás **Áttekintés** lapját. Ha már hozzárendelt egy tartományt az átjáró-alkalmazáshoz, az URL-cím megjelenik az **Áttekintés** ablaktáblán. A módosított PiggyMetrics megtekintéséhez válassza ki az URL-címet, és lépjen a webhelyre.

>[!NOTE]
> Miután beállította a zöld telepítést üzemi környezetként, az előző üzemelő példány lesz az átmeneti üzembe helyezés.

## <a name="modify-the-staging-deployment"></a>Az átmeneti telepítés módosítása

Ha nem elégedett a módosítással, módosíthatja az alkalmazás kódját, létrehozhat egy új jar-csomagot, és feltöltheti azt a zöld üzembe helyezéshez az Azure CLI használatával.

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-the-staging-deployment"></a>Az előkészítési központi telepítés törlése

Ha törölni szeretné az átmeneti üzembe helyezést az Azure-portról, nyissa meg az átmeneti üzembe helyezés lapot, majd kattintson a **Törlés** gombra.

Azt is megteheti, hogy az alábbi parancs futtatásával törli az átmeneti üzembe helyezést az Azure CLI-ből:

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```
