---
title: Oktatóanyag – georeplikált Docker-tárolójegyzék létrehozása az Azure-ban
description: Létrehozhat egy Azure Container Registryt, georeplikációt konfigurálhat, előkészíthet egy Docker-rendszerképet, és üzembe helyezheti azt a tárolójegyzékben. Ez egy háromrészes sorozat első része.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: tutorial
ms.date: 04/30/2017
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 7aec257335e3380fa99669c1191ee89857ec975d
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57533664"
---
# <a name="tutorial-prepare-a-geo-replicated-azure-container-registry"></a>Oktatóanyag: Georeplikált Azure Container Registry előkészítése

Az Azure Container Registryk az Azure-ban üzembe helyezett privát Docker-tárolójegyzékek, amelyeket a hálózaton elérhető távolságban tarthat az üzemelő példányokhoz. Ebből a háromrészes oktatóanyagból megtudhatja, hogyan használhat georeplikációt egy Linux-tárolóban futó ASP.NET Core-webalkalmazás két [Web Apps for Containers](../app-service/containers/index.yml)-példányban történő üzembe helyezéséhez. Láthatja, hogyan helyezi üzembe automatikusan az Azure a rendszerképet minden Web App-példányon a legközelebbi georeplikált adattárból.

A háromrészes sorozat első oktatóanyagának tartalma:

> [!div class="checklist"]
> * Egy georeplikált Azure Container Registry létrehozása
> * Alkalmazás forráskódjának klónozása a GitHubról
> * Docker-tároló rendszerképének összeállítása alkalmazásforrásból
> * Tároló rendszerképének leküldése a tárolójegyzékbe

A következő oktatóanyagokban egy két Azure-régióban futó webalkalmazás számára helyezi üzembe a tárolót a privát tárolójegyzékből. Ezután frissíti az alkalmazásban lévő programkódot, és egyetlen `docker push` paranccsal frissíti mindkét webalkalmazás-példányt a tárolójegyzékben.

## <a name="before-you-begin"></a>Előkészületek

Az oktatóanyaghoz az Azure CLI (2.0.31-es vagy újabb verzió) helyi telepítésére lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

Ismernie kell a Docker fő fogalmait, például a tárolókat, a tárolórendszerképeket és a Docker parancssori felületének alapszintű parancsait. A tárolókkal kapcsolatos alapfogalmakért tekintse meg [a Docker használatának első lépéseivel]( https://docs.docker.com/get-started/) foglalkozó témakört.

Az oktatóanyag elvégzéséhez a szüksége lesz egy helyi Docker-telepítésre. A Docker a [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) és [Linux](https://docs.docker.com/engine/installation/#supported-platforms) rendszerhez biztosít telepítési utasításokat.

Az Azure Cloud Shell nem tartalmazza a jelen oktatóanyag lépéseinek elvégzéséhez szükséges Docker-összetevőket, Ezért ajánlott az Azure CLI és a Docker fejlesztőkörnyezet helyi telepítése.

## <a name="create-a-container-registry"></a>Tároló-beállításjegyzék létrehozása

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

Válassza az **Erőforrás létrehozása** > **Tárolók** > **Azure Container Registry** elemet.

![Tároló-beállításjegyzék létrehozása az Azure Portalon][tut-portal-01]

Konfigurálja az új tárolójegyzéket a következő beállításokkal:

* **Beállításjegyzék neve**: Hozzon létre egy beállításjegyzék-nevet, amely globálisan egyedi Azure-ban, és 5 – 50 alfanumerikus karaktereket tartalmaz
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

Most, hogy konfigurálta a georeplikációt, állítson össze egy tárolórendszerképet, és küldje le azt a tárolójegyzékbe. Először be kell jelentkeznie az ACR-példányba, mielőtt rendszerképeket próbálna leküldeni rá.

Az [az acr login](https://docs.microsoft.com/cli/azure/acr#az-acr-login) paranccsal hitelesítheti és gyorsítótárazhatja a tárolójegyzék hitelesítő adatait. Cserélje le az `<acrName>` elemet a korábban létrehozott tárolójegyzék nevére.

```azurecli
az acr login --name <acrName>
```

A parancs a `Login Succeeded` értéket adja vissza, ha befejeződött.

## <a name="get-application-code"></a>Az alkalmazáskód letöltése

Az oktatóanyagban foglalt példa egy, az [ASP.NET Core][aspnet-core] használatával létrehozott kis webalkalmazást tartalmaz. Az alkalmazás egy HTML-oldalt működtet, amely azt a régiót jeleníti meg, amelyről az Azure Container Registry a rendszerképet üzembe helyezte.

![Az oktatóanyag alkalmazása böngészőben megjelenítve][tut-app-01]

Töltse le a mintát a Git használatával egy helyi könyvtárba, és a `cd` használatával lépjen be a könyvtárba:

```bash
git clone https://github.com/Azure-Samples/acr-helloworld.git
cd acr-helloworld
```

Ha a `git` nincs telepítve, közvetlenül a GitHubról [töltheti le a ZIP-archívumot][acr-helloworld-zip].

## <a name="update-dockerfile"></a>A Dockerfile frissítése

A mintában szereplő Dockerfile bemutatja a tároló összeállításának menetét. Egy hivatalos [aspnetcore][dockerhub-aspnetcore]-rendszerképből indul ki, a tárolóba másolja az alkalmazásfájlokat, telepíti a függőségeket, összeállítja a hivatalos [aspnetcore-build][dockerhub-aspnetcore-build]-rendszerképet, és végül egy optimalizált aspnetcore-rendszerképet hoz létre.

A [Dockerfile][dockerfile] a klónozott forrásban a következő helyen található: `./AcrHelloworld/Dockerfile`.

```Dockerfile
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

Az *acr-helloworld* rendszerképben lévő alkalmazás megpróbálja meghatározni azt a régiót, amelyből a tárolója üzembe lett helyezve. Ehhez lekérdezi a DNS-ből a tárolójegyzék bejelentkezési kiszolgálójával kapcsolatos adatokat. A tárolójegyzék bejelentkezési kiszolgálójának teljes tartománynevét a Dockerfile `DOCKER_REGISTRY` környezeti változójában kell megadnia.

Először kérje le a tárolójegyzék bejelentkezési kiszolgálóját az `az acr show` paranccsal. Cserélje le az `<acrName>` elemet az előző lépésekben létrehozott tárolójegyzék nevére.

```azurecli
az acr show --name <acrName> --query "{acrLoginServer:loginServer}" --output table
```

Kimenet:

```bash
AcrLoginServer
-----------------------------
uniqueregistryname.azurecr.io
```

Ezután frissítse az `ENV DOCKER_REGISTRY` sort a tárolójegyzék bejelentkezési kiszolgálójának teljes tartománynevével. Ebben a példában a példa tárolójegyzékének neve (*uniqueregistryname*) látható:

```Dockerfile
ENV DOCKER_REGISTRY uniqueregistryname.azurecr.io
```

## <a name="build-container-image"></a>Tárolórendszerkép összeállítása

Most, hogy a frissítette a Dockerfile-t a tárolójegyzék bejelentkezési kiszolgálójának teljes tartománynevével, a `docker build` paranccsal hozhatja létre a tárolórendszerképet. Futtassa a következő parancsot a rendszerkép összeállításához és a privát tárolójegyzék URL-címével való felcímkézésével, majd ismét cserélje le az `<acrName>` elemet a tárolójegyzék nevére:

```bash
docker build . -f ./AcrHelloworld/Dockerfile -t <acrName>.azurecr.io/acr-helloworld:v1
```

Több kimeneti sor jelenik meg a Docker-rendszerkép összeállításakor (itt csonkolva láthatók):

```bash
Sending build context to Docker daemon  523.8kB
Step 1/18 : FROM microsoft/aspnetcore:2.0 AS base
2.0: Pulling from microsoft/aspnetcore
3e17c6eae66c: Pulling fs layer

[...]

Step 18/18 : ENTRYPOINT dotnet AcrHelloworld.dll
 ---> Running in 6906d98c47a1
 ---> c9ca1763cfb1
Removing intermediate container 6906d98c47a1
Successfully built c9ca1763cfb1
Successfully tagged uniqueregistryname.azurecr.io/acr-helloworld:v1
```

A `docker images` használatával tekintse meg a felépített és címkézett rendszerképet:

```console
$ docker images
REPOSITORY                                      TAG    IMAGE ID        CREATED               SIZE
uniqueregistryname.azurecr.io/acr-helloworld    v1     01ac48d5c8cf    About a minute ago    284MB
[...]
```

## <a name="push-image-to-azure-container-registry"></a>Rendszerkép leküldése az Azure Container Registrybe

Ezután a `docker push` paranccsal küldje le az *acr-helloworld*-rendszerképet a tárolójegyzékbe. Cserélje le az `<acrName>` elemet a tárolójegyzék nevére.

```bash
docker push <acrName>.azurecr.io/acr-helloworld:v1
```

Mivel georeplikációhoz konfigurálta a tárolójegyzéket, a rendszerkép ezzel az egy `docker push` paranccsal automatikusan replikálva lesz az *USA nyugati régióján* és az *USA keleti régióján* is.

```console
$ docker push uniqueregistryname.azurecr.io/acr-helloworld:v1
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

Ebben az oktatóanyagban létrehozott egy privát, georeplikált tárolójegyzéket, felépített egy tárolórendszerképet, majd ezt a rendszerképet leküldte a tárolójegyzékbe.

Folytassa a következő oktatóanyaggal, amely üzembe helyezi a tárolót több Web Apps for Containers-példányon, georeplikációt használva a rendszerképek helyi kiszolgálásához.

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

<!-- LINKS - External -->
[acr-helloworld-zip]: https://github.com/Azure-Samples/acr-helloworld/archive/master.zip
[aspnet-core]: https://dot.net
[dockerhub-aspnetcore]: https://hub.docker.com/r/microsoft/aspnetcore/
[dockerhub-aspnetcore-build]: https://store.docker.com/community/images/microsoft/aspnetcore-build
[dockerfile]: https://github.com/Azure-Samples/acr-helloworld/blob/master/AcrHelloworld/Dockerfile