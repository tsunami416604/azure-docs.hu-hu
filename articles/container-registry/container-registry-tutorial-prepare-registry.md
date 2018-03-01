---
title: "Az Azure Container Registry oktatóanyaga – Georeplikált Azure Container Registry előkészítése"
description: "Létrehozhat egy Azure Container Registryt, georeplikációt konfigurálhat, előkészíthet egy Docker-rendszerképet, és üzembe helyezheti azt a tárolójegyzékben. Ez egy háromrészes sorozat első része."
services: container-registry
author: mmacy
manager: timlt
ms.service: container-registry
ms.topic: tutorial
ms.date: 10/26/2017
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: b73222d9b31ff840273bdb91a15f7eaf37ad2508
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="prepare-a-geo-replicated-azure-container-registry"></a>Georeplikált Azure Container Registry előkészítése

Az Azure Container Registryk az Azure-ban üzembe helyezett privát Docker-tárolójegyzékek, amelyeket a hálózaton elérhető távolságban tarthat az üzemelő példányokhoz. Ebből a háromrészes oktatóanyagból megtudhatja, hogyan használhat georeplikációt egy Linux-tárolóban futó ASP.NET Core-webalkalmazás két [Web Apps for Containers](../app-service/containers/index.yml)-példányban történő üzembe helyezéséhez. Láthatja, hogyan helyezi üzembe automatikusan az Azure a rendszerképet minden Web App-példányon a legközelebbi georeplikált adattárból.

A háromrészes sorozat első oktatóanyagának tartalma:

> [!div class="checklist"]
> * Egy georeplikált Azure Container Registry létrehozása
> * Alkalmazás forráskódjának klónozása a GitHubról
> * Docker-tároló rendszerképének összeállítása alkalmazásforrásból
> * Tároló rendszerképének leküldése a tárolójegyzékbe

A következő oktatóanyagokban egy két Azure-régióban futó webalkalmazás számára helyezi üzembe a tárolót a privát tárolójegyzékből. Ezután frissíti az alkalmazásban lévő programkódot, és egyetlen `docker push` paranccsal frissíti mindkét webalkalmazás-példányt a tárolójegyzékben.

## <a name="before-you-begin"></a>Előkészületek

Az oktatóanyag elvégzéséhez az Azure CLI 2.0.20-as vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli).

Az oktatóanyag feltételezi, hogy rendelkezik a Docker fő fogalmaira, például a tárolókra, tárolórendszerképekre és az alapszintű Docker-parancsokra vonatkozó alapvető ismeretekkel. Amennyiben szükséges, tekintse meg a tárolók alapfogalmainak ismertetését a [Bevezetés a Docker használatába]( https://docs.docker.com/get-started/) című cikkben.

Az oktatóanyag elvégzéséhez szüksége lesz egy Docker-fejlesztési környezetre. A Docker csomagokat biztosít, amelyekkel a Docker egyszerűen konfigurálható bármely [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) vagy [Linux](https://docs.docker.com/engine/installation/#supported-platforms) rendszeren.

Az Azure Cloud Shell nem tartalmazza a jelen oktatóanyag lépéseinek elvégzéséhez szükséges Docker-összetevőket, Ezért ajánlott az Azure CLI és a Docker fejlesztőkörnyezet helyi telepítése.

> [!IMPORTANT]
> Az Azure Container Registry georeplikációs funkciója jelenleg **előzetes verzióban** érhető el. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.
>

## <a name="create-a-container-registry"></a>Tároló-beállításjegyzék létrehozása

Jelentkezzen be az [Azure Portalra](http://portal.azure.com).

Válassza az **Erőforrás létrehozása** > **Tárolók** > **Azure Container Registry** elemet.

![Tároló-beállításjegyzék létrehozása az Azure Portalon][tut-portal-01]

Konfigurálja az új tárolójegyzéket a következő beállításokkal:

* **Regisztrációs adatbázis neve**: Olyan nevet hozzon létre a tárolójegyzékhez, amely globálisan egyedi az Azure-ban, és 5–50 alfanumerikus karakterből áll.
* **Erőforráscsoport**: **Új létrehozása** > `myResourceGroup`
* **Hely**: `West US`
* **Rendszergazdai felhasználó**: `Enable` (a Web App for Containershez szükséges, a rendszerképek lekéréséhez)
* **Termékváltozat**: `Premium` (a georeplikációhoz szükséges)

Kattintson a **Létrehozás** elemre az ACR-példány üzembe helyezéséhez.

![Tároló-beállításjegyzék létrehozása az Azure Portalon][tut-portal-02]

Az oktatóanyag hátralevő részében az `<acrName>` elem helyettesíti a tárolójegyzék **választott nevét**.

> [!TIP]
> Mivel az Azure tárolójegyzékek általában több tárológazdagép által használt hosszútávú erőforrások, ajánlott a saját erőforráscsoportjában létrehozni a tárolójegyzéket. A georeplikált tárolójegyzékek és webhookok konfigurálásakor a további erőforrások ugyanabba az erőforráscsoportba kerülnek.
>

## <a name="configure-geo-replication"></a>Aktív georeplikáció konfigurálása

Most, hogy egy prémium szintű tárolójegyzékkel rendelkezik, konfigurálhatja a georeplikációt. A webalkalmazás – amelyet a következő oktatóanyagban konfigurál a két régióban való futtatáshoz – ezután le tudja kérni a tároló rendszerképeit a legközelebbi tárolójegyzékből.

Keresse meg az új tárolójegyzéket az Azure Portalon, és válassza a **SZOLGÁLTATÁSOK** területen lévő **Replikációk** lehetőséget:

![Replikációk az Azure Portal tárolójegyzékeinek felhasználói felületén][tut-portal-03]

Megjelenik egy térkép a georeplikációhoz elérhető Azure-régiókat jelző zöld hatszögekkel:

 ![Régiótérkép az Azure Portalon][tut-map-01]

Replikálja a tárolójegyzékét az USA keleti régiójába a zöld hatszög kiválasztásával, majd válassza a **Replikáció létrehozása** területen lévő **Létrehozás** lehetőséget:

 ![Replikáció létrehozásának felhasználói felülete az Azure Portalon][tut-portal-04]

A replikáció befejezése után a portál mindkét régió esetén a *Kész* állapotot jeleníti meg. A **Frissítés** gombbal frissítse a replikáció állapotát. Körülbelül egy percbe telhet a replikák létrehozása és szinkronizálása.

![Replikációs állapot felhasználói felülete az Azure Portalon][tut-portal-05]

## <a name="container-registry-login"></a>Bejelentkezés a tárolóregisztrációs adatbázisba

Most, hogy konfigurálta a georeplikációt, állítson össze egy tárolórendszerképet, és küldje le azt a tárolójegyzékbe. Először be kell jelentkeznie az ACR-példányba, mielőtt rendszerképeket próbálna leküldeni rá. Az [Alapszintű, Standard és Prémium termékváltozatokban](container-registry-skus.md) az Azure-identitással végezheti el a hitelesítést.

Az [az acr login](https://docs.microsoft.com/cli/azure/acr#az_acr_login) paranccsal hitelesítheti és gyorsítótárazhatja a tárolójegyzék hitelesítő adatait. Cserélje le az `<acrName>` elemet az előző lépésekben létrehozott tárolójegyzék nevére.

```azurecli
az acr login --name <acrName>
```

A parancs a `Login Succeeded` értéket adja vissza, ha befejeződött.

## <a name="get-application-code"></a>Az alkalmazáskód letöltése

Az oktatóanyagban foglalt példa egy, az [ASP.NET Core](http://dot.net) használatával létrehozott kis webalkalmazást tartalmaz. Az alkalmazás egy HTML-oldalt működtet, amely azt a régiót jeleníti meg, amelyről az Azure Container Registry a rendszerképet üzembe helyezte.

![Az oktatóanyag alkalmazása böngészőben megjelenítve][tut-app-01]

Töltse le a mintát a Git használatával egy helyi könyvtárba, és a `cd` használatával lépjen be a könyvtárba:

```bash
git clone https://github.com/Azure-Samples/acr-helloworld.git
cd acr-helloworld
```

## <a name="update-dockerfile"></a>A Dockerfile frissítése

A mintában szereplő Dockerfile bemutatja a tároló összeállításának menetét. Egy hivatalos [aspnetcore](https://store.docker.com/community/images/microsoft/aspnetcore)-rendszerképből indul ki, a tárolóba másolja az alkalmazásfájlokat, telepíti a függőségeket, összeállítja a hivatalos [aspnetcore-build](https://store.docker.com/community/images/microsoft/aspnetcore-build)-rendszerképet, és végül egy optimalizált aspnetcore-rendszerképet hoz létre.

A Dockerfile a klónozott forrásban a következő helyen található: `./AcrHelloworld/Dockerfile`.

```dockerfile
FROM microsoft/aspnetcore:2.0 AS base
# Update <acrName> with the name of your registry
# Example: uniqueregistryname.azurecr.io
ENV DOCKER_REGISTRY <acrName>.azurecr.io
WORKDIR /app
EXPOSE 80

FROM microsoft/aspnetcore-build:2.0 AS build
WORKDIR /src
COPY *.sln ./
COPY AcrHelloworld/AcrHelloworld.csproj AcrHelloworld/
RUN dotnet restore
COPY . .
WORKDIR /src/AcrHelloworld
RUN dotnet build -c Release -o /app

FROM build AS publish
RUN dotnet publish -c Release -o /app

FROM base AS production
WORKDIR /app
COPY --from=publish /app .
ENTRYPOINT ["dotnet", "AcrHelloworld.dll"]
```

Az *acr-helloworld* rendszerképben lévő alkalmazás megpróbálja meghatározni azt a régiót, amelyből a tárolója üzembe lett helyezve. Ehhez lekérdezi a DNS-ből a tárolójegyzék bejelentkezési kiszolgálójával kapcsolatos adatokat. A tárolójegyzék bejelentkezési kiszolgálójának URL-címét a Dockerfile `DOCKER_REGISTRY` környezeti változójában kell megadnia.

Először kérje le a tárolójegyzék bejelentkezési kiszolgálójának URL-címét az `az acr show` paranccsal. Cserélje le az `<acrName>` elemet az előző lépésekben létrehozott tárolójegyzék nevére.

```azurecli
az acr show --name <acrName> --query "{acrLoginServer:loginServer}" --output table
```

Kimenet:

```bash
AcrLoginServer
-----------------------------
uniqueregistryname.azurecr.io
```

Ezután frissítse a `DOCKER_REGISTRY` sort a tárolójegyzék bejelentkezési kiszolgálójának URL-címével. Ebben a példában úgy frissítjük a sort, hogy az tükrözze a példa tárolójegyzékének nevét (*uniqueregistryname*):

```dockerfile
ENV DOCKER_REGISTRY uniqueregistryname.azurecr.io
```

## <a name="build-container-image"></a>Tárolórendszerkép összeállítása

Most, hogy a frissítette a Dockerfile-t a tárolójegyzék URL-címével, a `docker build` paranccsal hozhatja létre a tárolórendszerképet. Futtassa a következő parancsot a rendszerkép összeállításához és a privát tárolójegyzék URL-címével való felcímkézésével, majd ismét cserélje le az `<acrName>` elemet a tárolójegyzék nevére:

```bash
docker build . -f ./AcrHelloworld/Dockerfile -t <acrName>.azurecr.io/acr-helloworld:v1
```

Több kimeneti sor jelenik meg a Docker-rendszerkép összeállításakor (itt csonkolva láthatók):

```bash
Sending build context to Docker daemon  523.8kB
Step 1/18 : FROM microsoft/aspnetcore:2.0 AS base
2.0: Pulling from microsoft/aspnetcore
3e17c6eae66c: Pulling fs layer
...
Step 18/18 : ENTRYPOINT dotnet AcrHelloworld.dll
 ---> Running in 6906d98c47a1
 ---> c9ca1763cfb1
Removing intermediate container 6906d98c47a1
Successfully built c9ca1763cfb1
Successfully tagged uniqueregistryname.azurecr.io/acr-helloworld:v1
```

A `docker images` paranccsal megtekintheti az összeállított rendszerképet:

```bash
docker images
```

Kimenet:

```bash
REPOSITORY                                      TAG    IMAGE ID        CREATED               SIZE
uniqueregistryname.azurecr.io/acr-helloworld    v1     01ac48d5c8cf    About a minute ago    284MB
...
```

## <a name="push-image-to-azure-container-registry"></a>Rendszerkép leküldése az Azure Container Registrybe

Végül a `docker push` paranccsal küldje le az *acr-helloworld*-rendszerképet a tárolójegyzékbe. Cserélje le az `<acrName>` elemet a tárolójegyzék nevére.

```bash
docker push <acrName>.azurecr.io/acr-helloworld:v1
```

Mivel georeplikációhoz konfigurálta a tárolójegyzéket, a rendszerkép ezzel az egy `docker push` paranccsal automatikusan replikálva lesz az *USA nyugati régióján* és az *USA keleti régióján* is.

Kimenet:

```bash
The push refers to a repository [uniqueregistryname.azurecr.io/acr-helloworld]
cd54739c444b: Pushed
d6803756744a: Pushed
b7b1f3a15779: Pushed
a89567dff12d: Pushed
59c7b561ff56: Pushed
9a2f9413d9e4: Pushed
a75caa09eb1f: Pushed
v1: digest: sha256:0799014f91384bda5b87591170b1242bcd719f07a03d1f9a1ddbae72b3543970 size: 1792
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy privát, georeplikált tárolójegyzéket, felépített egy tárolórendszerképet, majd ezt a rendszerképet leküldte a tárolójegyzékbe. A jelen oktatóanyag lépéseit követve a következőket végezte el:

> [!div class="checklist"]
> * Létrehozott egy georeplikált Azure Container Registryt
> * Klónozta az alkalmazás forráskódját a GitHubról
> * Összeállította egy Docker-tároló rendszerképét az alkalmazásforrásból
> * Leküldte a tárolórendszerképet a tárolójegyzékbe

Folytassa a következő oktatóanyaggal, amely azt ismerteti, hogyan helyezhető üzembe a tároló több Web Apps for Containers -példányon, georeplikációt használva a rendszerképek helyi kiszolgálásához.

> [!div class="nextstepaction"]
> [Webalkalmazás üzembe helyezése az Azure Container Registryből](container-registry-tutorial-deploy-app.md)

<!-- IMAGES -->
[tut-portal-01]: ./media/container-registry-tutorial-prepare-registry/tut-portal-01.png
[tut-portal-02]: ./media/container-registry-tutorial-prepare-registry/tut-portal-02.png
[tut-portal-03]: ./media/container-registry-tutorial-prepare-registry/tut-portal-03.png
[tut-portal-04]: ./media/container-registry-tutorial-prepare-registry/tut-portal-04.png
[tut-portal-05]: ./media/container-registry-tutorial-prepare-registry/tut-portal-05.png
[tut-app-01]: ./media/container-registry-tutorial-prepare-registry/tut-app-01.png
[tut-map-01]: ./media/container-registry-tutorial-prepare-registry/tut-map-01.png
