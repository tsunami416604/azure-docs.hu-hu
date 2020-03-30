---
title: Átmeneti környezet beállítása az Azure Spring Cloud ban | Microsoft dokumentumok
description: Ismerje meg, hogyan használhatja a kék-zöld üzembe helyezést az Azure Spring Cloud segítségével
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: brendm
ms.openlocfilehash: 2e29f6a75b303518ac34ecf9b570bd7638cf0c3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471030"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>Átmeneti környezet beállítása az Azure Spring Cloudban

Ez a cikk ismerteti, hogyan állíthat be egy átmeneti központi telepítés használatával a kék-zöld üzembe helyezési minta az Azure Spring Cloud. A kék/zöld üzembe helyezés egy Azure DevOps folyamatos kézbesítési minta, amelynek lényege, hogy működésben tart egy meglévő (kék) verziót, miközben üzembe helyez egy új (zöld) verziót. Ez a cikk bemutatja, hogyan helyezheti üzembe az átmeneti központi telepítés éles környezetben az éles környezet módosítása nélkül közvetlenül.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy már telepítette a PiggyMetrics alkalmazást az [Azure Spring Cloud-alkalmazás elindításáról szóló oktatóanyagunkból.](spring-cloud-quickstart-launch-app-portal.md) A PiggyMetrics három alkalmazásból áll: "átjáró", "fiókszolgáltatás" és "hitelesítési szolgáltatás".  

Ha ebben a példában egy másik alkalmazást szeretne használni, egyszerű módosítást kell ellátnia az alkalmazás nyilvános anamnézisében.  Ez a módosítás megkülönbözteti az átmeneti üzembe helyezést az éles környezettől.

>[!TIP]
> Az Azure Cloud Shell egy ingyenes interaktív rendszerhéj, amely segítségével futtathatja a cikkben található utasításokat.  Gyakori, előre telepített Azure-eszközökkel rendelkezik, beleértve a Git, a JDK, a Maven és az Azure CLI legújabb verzióit. Ha be van jelentkezve az Azure-előfizetésbe, indítsa el az [Azure Cloud Shell.](https://shell.azure.com)  További információ: [Az Azure Cloud Shell áttekintése.](../cloud-shell/overview.md)

Átmeneti környezet beállítása az Azure Spring Cloudban kövesse a következő szakaszokban található utasításokat.

## <a name="install-the-azure-cli-extension"></a>Az Azure CLI bővítmény telepítése

Telepítse az Azure Spring Cloud bővítményt az Azure CLI-hez a következő paranccsal:

```azurecli
az extension add --name spring-cloud
```
    
## <a name="view-all-deployments"></a>Az összes központi telepítés megtekintése

Nyissa meg a szolgáltatáspéldányt az Azure Portalon, és válassza **a Központi telepítés az** összes központi telepítés megtekintéséhez. További részletek megtekintéséhez kiválaszthatja az egyes központi telepítéseket.

## <a name="create-a-staging-deployment"></a>Átmeneti központi telepítés létrehozása

1. A helyi fejlesztői környezetben hajtson végre egy kis módosítást a PiggyMetrics átjáróalkalmazáson. Például módosítsa a színét az *átjáró/src/main/resources/static/css/launch.css* fájlban. Ezzel könnyedén megkülönböztetheti a két központi telepítést. A jar csomag létrehozásához futtassa a következő parancsot: 

    ```console
    mvn clean package
    ```

1. Az Azure CLI-ben hozzon létre egy új központi telepítést, és adja meg az átmeneti központi telepítés nevét "zöld".

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app gateway -n green --jar-path gateway/target/gateway.jar
    ```

1. Miután a központi telepítés sikeresen befejeződött, lépjen be az átjárólapra az **alkalmazás irányítópultjáról,** és tekintse meg az összes példányt a bal oldali **Alkalmazáspéldányok** lapon.
  
> [!NOTE]
> A felderítési állapot *OUT_OF_SERVICE,* így a forgalom nem lesz irányítva erre a központi telepítésre, mielőtt az ellenőrzés befejeződött.

## <a name="verify-the-staging-deployment"></a>Az átmeneti telepítés ellenőrzése

1. Térjen vissza a **Telepítés kezelés** lapra, és válassza ki az új központi telepítést. A központi telepítés állapotának *futnia*kell. A **Tartomány hozzárendelése/visszavonása** gombszürkének szürkén jelenjen meg, mivel a környezet átmeneti környezet.

1. Az **Áttekintő** ablaktáblán meg kell jelennie egy **tesztvégpontnak.** Másolja és illessze be egy új böngészőablakba, és az új PiggyMetrics oldal nak meg kell jelennie.

>[!TIP]
> * Ellenőrizze, hogy a tesztvégpont perjellel (/) végződik-e, hogy a CSS-fájl megfelelően töltődjön be.  
> * Ha a böngésző megköveteli, hogy adja meg a bejelentkezési hitelesítő adatokat, hogy megtekinthesse a lapot, [url-dekódolni](https://www.urldecoder.org/) a teszt végpontját. URL-dekódolni ad vissza\<egy URL-t a "https:// felhasználónév>:\<jelszó>@\<cluster-name>.test.azureapps.io/gateway/green".  Ezen a képernyőn érheti el a végpontot.

>[!NOTE]    
> A konfigurációs kiszolgáló beállításai az átmeneti környezetre és az éles környezetre egyaránt vonatkoznak. Ha például a konfigurációs`server.servlet.context-path`kiszolgálón lévő alkalmazásátjáró ( ) környezeti elérési útját ( ) *állítja be,* a zöld központi telepítés elérési útja "https://\<felhasználónév>:\<jelszó>@\<cluster-name>.test.azureapps.io/gateway/green/somepath/...".
 
 Ha ezen a ponton látogat ja meg a nyilvános alkalmazásátjárót, az új módosítás nélkül kell látnia a régi oldalt.
    
## <a name="set-the-green-deployment-as-the-production-environment"></a>A zöld telepítés beállítása éles környezetként

1. Miután ellenőrizte a változást az átmeneti környezetben, leküldéses éles környezetbe. Térjen vissza a **Központi telepítés kezeléséhez**, és jelölje be az **átjáróalkalmazás** jelölőnégyzetet.

2. Válassza **a Telepítés beállítása**lehetőséget.
3. Az **Éles környezetben futó telepítés** listában válassza a **Zöld**lehetőséget, majd az **Alkalmaz**lehetőséget.
4. Nyissa meg az átjáróalkalmazás **áttekintése** lapot. Ha már rendelt egy tartományt az átjáróalkalmazáshoz, az URL-cím megjelenik az **Áttekintő** ablaktáblán. A módosított PiggyMetrics lap megtekintéséhez jelölje ki az URL-címet, és nyissa meg a webhelyet.

>[!NOTE]
> Miután beállította a zöld üzembe helyezést az éles környezetben, az előző központi telepítés lesz az átmeneti központi telepítés.

## <a name="modify-the-staging-deployment"></a>Az átmeneti központi telepítés módosítása

Ha nem elégedett a módosítással, módosíthatja az alkalmazás kódját, új jar csomagot hozhat létre, és feltöltheti a zöld üzembe helyezéshez az Azure CLI használatával.

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-the-staging-deployment"></a>Az átmeneti központi telepítés törlése

Ha törölni szeretné az átmeneti központi telepítést az Azure-portról, lépjen az átmeneti központi telepítés lapra, és válassza a **Törlés** gombot.

Másik lehetőségként törölje az átmeneti központi telepítést az Azure CLI-ből a következő parancs futtatásával:

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```
