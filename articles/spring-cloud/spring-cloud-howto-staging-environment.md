---
title: Átmeneti környezet beállítása az Azure Spring Cloud-ban | Microsoft Docs
description: Ismerje meg, hogyan használható a kék-zöld üzembe helyezés az Azure Spring Cloud használatával
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: jeconnoc
ms.openlocfilehash: 24ce4dee04e4daf3eaee4144f8dc56de5867bbca
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607210"
---
# <a name="how-to-set-up-a-staging-environment"></a>Átmeneti környezet beállítása

Ebből a cikkből megtudhatja, hogyan használhatja az átmeneti üzembe helyezést a kék-zöld üzembe helyezési mintával az Azure Spring Cloud-ban. Emellett azt is bemutatja, hogyan helyezheti üzembe az előkészítési telepítést éles környezetben anélkül, hogy az éles üzembe helyezést közvetlenül módosítaná.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy már telepítette a PiggyMetrics alkalmazást az [oktatóanyagból egy alkalmazás elindításához](spring-cloud-quickstart-launch-app-portal.md). A PiggyMetrics három alkalmazást tartalmaz: "Gateway", "Account-Service" és "Auth-Service".  

Ha más alkalmazást szeretne használni ehhez a példához, az alkalmazás nyilvános részén egyszerű módosítást kell végeznie.  Ez a változás megkülönbözteti az átmeneti üzembe helyezést az éles környezetben.

>[!TIP]
> Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat.  A közös Azure-eszközök előre telepítve vannak, beleértve a git, a JDK, a Maven és az Azure CLI legújabb verzióit. Ha bejelentkezett az Azure-előfizetésbe, indítsa el a [Azure Cloud shellt](https://shell.azure.com) a shell.Azure.com webhelyről.  A Azure Cloud Shellről a [dokumentáció beolvasásával](../cloud-shell/overview.md) tájékozódhat bővebben

A cikk elvégzéséhez:


## <a name="install-the-azure-cli-extension"></a>Az Azure CLI-bővítmény telepítése

Telepítse az Azure CLI-hez készült Azure Spring Cloud-bővítményt az alábbi paranccsal

```azurecli
az extension add --name spring-cloud
```
    
## <a name="view-all-deployments"></a>Az összes üzemelő példány megtekintése

Nyissa meg a szolgáltatási példányt a Azure Portalban, és válassza a **központi telepítés kezelése** lehetőséget az összes központi telepítés megtekintéséhez. További részleteket az egyes központi telepítések közül választhat.

## <a name="create-a-staging-deployment"></a>Előkészítési központi telepítés létrehozása

1. A helyi fejlesztési környezetben kis módosítást kell végezni a Piggy metrika Gateway-alkalmazásához. Például módosítsa a színét `gateway/src/main/resources/static/css/launch.css` értékre. Ez lehetővé teszi a két üzemelő példány egyszerű megkülönböztetését. Futtassa a következő parancsot a jar-csomag létrehozásához: 

    ```azurecli
    mvn clean package
    ```

1. Hozzon létre egy új üzemelő példányt az Azure CLI-vel, amely a "zöld" átmeneti telepítési nevet adja.

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app gateway -n green --jar-path gateway/target/gateway.jar
    ```

1. Miután az üzembe helyezés sikeresen befejeződik, nyissa meg az átjáró lapot az **alkalmazás irányítópultján** , és a bal oldalon tekintse meg az összes példányát az **app instances** lapon.
  
> [!NOTE]
> A felderítési állapot "OUT_OF_SERVICE", így az ellenőrzés befejeződése előtt a rendszer nem irányítja át a forgalmat az üzemelő példányba.

## <a name="verify-the-staging-deployment"></a>Az átmeneti telepítés ellenőrzése

1. térjen vissza a **központi telepítés kezelése** lapra, és válassza ki az új központi telepítést. Az üzembe helyezési állapotnak **futnia**kell. A "tartomány kiosztása/megszüntetése" gomb le lesz tiltva, mivel átmeneti környezet.

1. Az **Áttekintés** oldalon egy **teszt végpontot**kell látnia. Másolja és illessze be egy új böngészőbe, és ekkor megjelenik az új Piggy mérőszámok oldal.

>[!TIP]
> * Győződjön meg arról, hogy a tesztelési végpont a "/" értékkel végződik, hogy a CSS betöltődik-e.  
> * Ha a böngésző megköveteli a bejelentkezési hitelesítő adatok megadását a lap megtekintéséhez, használja az [URL-címet](https://www.urldecoder.org/) a teszt végpont dekódolásához. Az URL-cím dekódolása egy URL-címet ad vissza "https://\<username >:\<Password > @\<cluster-Name >. test. azureapps. IO/Gateway/Green".  Ezzel elérheti a végpontot.

>[!NOTE]    
> A konfigurációs kiszolgáló beállításai az átmeneti környezetre és a gyártásra is érvényesek. Ha például az alkalmazás-átjáró környezeti elérési útját (`server.servlet.context-path`) a konfigurációs kiszolgálón *somepath*-ként állítja be, akkor a zöld telepítésének elérési útja: "https://\<username >:\<Password > @\<cluster-Name >. test.azureapps.io/gateway/green/somepath/... "
 
 Ha ezen a ponton látogatja meg a nyilvános alkalmazás-átjárót, az új módosítás nélkül látnia kell a régi oldalt.
    
## <a name="set-the-green-as-production-deployment"></a>A zöld állapot beállítása üzemi központi telepítésként

1. Miután ellenőrizte a változást az átmeneti környezetben, leküldheti azt az éles környezetbe. Térjen vissza a **központi telepítés felügyeletéhez** , és jelölje be a jelölőnégyzetet az "átjáró" alkalmazás előtt.
2. Válassza a "központi telepítés beállítása" lehetőséget.
3. Válassza a "zöld" lehetőséget az éles üzembe helyezés menüben, majd válassza az **alkalmaz** lehetőséget.
4. Nyissa meg az átjáró alkalmazás **Áttekintés** lapját. Ha már hozzárendelt egy tartományt az átjáró-alkalmazáshoz, akkor az URL-cím megjelenik az **Áttekintés** oldalon. Keresse fel az URL-címet, és látni fogja a módosított Piggy mérőszámok lapot.

>[!NOTE]
> Ha a zöld üzembe helyezés éles környezetben van beállítva, az előző üzemelő példány lesz az átmeneti üzembe helyezés.

## <a name="modify-the-staging-deployment"></a>Az átmeneti telepítés módosítása

Ha nem elégedett a módosítással, módosíthatja az alkalmazás kódját, létrehozhat egy új jar-csomagot, és feltöltheti azt a zöld üzembe helyezéshez az Azure CLI használatával.

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-a-staging-deployment"></a>Átmeneti központi telepítés törlése

Törölje az átmeneti üzembe helyezést az Azure-portról az átmeneti üzembe helyezési oldalra, majd kattintson a **Törlés** gombra.

Másik lehetőségként törölje az előkészítési telepítést az Azure CLI-ből a következő paranccsal:

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```
