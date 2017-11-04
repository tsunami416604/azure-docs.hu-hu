---
title: "Az Azure tároló beállításjegyzék-oktatóanyag – egy Azure-tárolót georeplikált beállításjegyzék előkészítése"
description: "Hozzon létre egy Azure-tárolót beállításjegyzék, konfigurálja a georeplikáció, Docker lemezkép előkészítése és telepíteni kell a beállításjegyzéket. Egy háromrészes sorozat része."
services: container-registry
documentationcenter: 
author: mmacy
manager: timlt
editor: mmacy
tags: acr, azure-container-registry, geo-replication
keywords: "Docker, tárolók, beállításjegyzék, Azure"
ms.service: container-registry
ms.devlang: 
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/26/2017
ms.author: marsma
ms.custom: 
ms.openlocfilehash: 75408dc88b23b615971a23dc6235c563229d75aa
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="prepare-a-geo-replicated-azure-container-registry"></a>Egy Azure-tárolót georeplikált beállításjegyzék előkészítése

Egy Azure-tárolót beállításjegyzék titkos Docker beállításjegyzékbeli, amely a központi telepítésekre képes tárolni a hálózati zárja be az Azure-ban telepítve. Ez a három útmutató cikkek készletében, megismerheti, hogyan georeplikáció használatával futtatja a Linux-tároló két ASP.NET Core webes alkalmazás központi telepítése [tárolók webalkalmazásait](../app-service/containers/index.yml) példányok. Láthatja, hogyan Azure automatikusan telepíti a kép webalkalmazás feltünteti a legközelebbi georeplikált tárházból.

Ebben az oktatóanyagban része egy háromrészes:

> [!div class="checklist"]
> * Hozzon létre egy Azure-tárolót georeplikált beállításjegyzék
> * A Githubból az alkalmazás forráskódjához klónozása
> * Összeállíthatja a Docker tároló lemezképét alkalmazás adatforrás
> * A tároló kép leküldése a beállításjegyzék

A következő útmutatókból telepít a tároló a személyes beállításjegyzékből egy webalkalmazást, két Azure-régiók futtatja. Ezután frissítse a kódot az alkalmazásban, és mindkét Web App-példány frissítése egyetlen `docker push` a beállításjegyzékhez.

## <a name="before-you-begin"></a>Előkészületek

Ez az oktatóanyag megköveteli, hogy futnak-e az Azure parancssori felület 2.0.20 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli).

Az oktatóanyag feltételezi, hogy rendelkezik a Docker fő fogalmaira, például a tárolókra, tárolórendszerképekre és az alapszintű Docker-parancsokra vonatkozó alapvető ismeretekkel. Amennyiben szükséges, tekintse meg a tárolók alapfogalmainak ismertetését a [Bevezetés a Docker használatába]( https://docs.docker.com/get-started/) című cikkben.

Az oktatóanyag elvégzéséhez szüksége lesz egy Docker-fejlesztési környezetre. A Docker csomagokat biztosít, amelyekkel a Docker egyszerűen konfigurálható bármely [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) vagy [Linux](https://docs.docker.com/engine/installation/#supported-platforms) rendszeren.

Azure Cloud rendszerhéj nem tartalmazza a Docker-összetevők minden egyes lépéseinek befejezéséhez szükséges az oktatóanyag. Ezért ajánlott az Azure CLI és a Docker fejlesztőkörnyezet helyi telepítését.

> [!IMPORTANT]
> A georeplikáció Azure tároló beállításjegyzék használata a jelenleg a **előzetes**. Az előzetes verziójú funkciók elérhetővé válnak, a feltétellel, hogy elfogadja a [kiegészítő használati feltételek](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Ez a funkció az egyes funkcióit általános elérhetőségével (GA) előtt módosíthatja.
>

## <a name="create-a-container-registry"></a>Tároló-beállításjegyzék létrehozása

Jelentkezzen be az [Azure Portalra](http://portal.azure.com).

Válassza ki **új** > **tárolók** > **Azure tároló beállításjegyzék**.

![Egy tároló beállításjegyzék létrehozása az Azure-portálon][tut-portal-01]

Az új beállításjegyzék konfigurálása a következő beállításokkal:

* **Beállításjegyzék neve**: hozzon létre egy beállításjegyzék-nevet, amely globálisan egyedi Azure-ban, és 5-50 alfanumerikus karaktereket tartalmaz
* **Erőforráscsoport**: **új létrehozása** > `myResourceGroup`
* **Hely**:`West US`
* **A rendszergazdai jogú felhasználó**: `Enable` (Web App a képek lekéréses tárolók esetén szükséges)
* **SKU**: `Premium` (georeplikáció esetén szükséges)

Válassza ki **létrehozása** az ACR-példány telepítése.

![Egy tároló beállításjegyzék létrehozása az Azure-portálon][tut-portal-02]

Ez az oktatóanyag a többi, egész használjuk `<acrName>` a tároló helyőrzőként **beállításjegyzék neve** választott.

> [!TIP]
> Mivel az Azure-tárolót nyilvántartó általában hosszú élettartamú tároló több gazdagépen is használt példaerőforrásokat, azt javasoljuk, hogy a saját erőforráscsoportban hozza létre a beállításkulcs. Georeplikált nyilvántartó és webhookokkal konfigurálását, a további erőforrások kerülnek ugyanabban az erőforráscsoportban.
>

## <a name="configure-geo-replication"></a>Aktív georeplikáció konfigurálása

Most, hogy prémium beállításjegyzékbeli, beállíthatja a georeplikáció. A webalkalmazás, amely konfigurálja a következő oktatóanyag futtatásához két régióban, majd lekérni a tároló ábráit a legközelebbi beállításjegyzékből.

Keresse meg az Azure portál, és válassza ki az új tároló regisztrációs **replikációk** alatt **szolgáltatások**:

![Az Azure portál tároló beállításjegyzék felhasználói felület replikációk][tut-portal-03]

Olyan térképet jelenik meg, melyen a georeplikációért elérhető Azure régiókhoz zöld hatszögek:

 ![Az Azure portálon régió térkép][tut-map-01]

USA keleti régiójában a rendszerleíró adatbázis replikálása a zöld hatszög kiválasztásával, majd válasszon **létrehozása** alatt **létrehozni a replikációs**:

 ![Replikációs felhasználói felület létrehozása az Azure portálon][tut-portal-04]

Ha a replikáció befejeződött, a portál által adott jelentéseket tükrözik *készen* mindkét régiókhoz. Használja a **frissítése** gombra kattint, hogy a replikációs állapotának frissítése; is igénybe vehet, egy perc vagy így létrehozni és szinkronizálása a replikák számára.

![Replikációs állapot felhasználói felületi az Azure-portálon][tut-portal-05]

## <a name="container-registry-login"></a>Tároló beállításjegyzék bejelentkezés

Most, hogy konfigurálta az georeplikáció, tároló-lemezkép elkészítése, és hogy a beállításjegyzékben. Kell először jelentkezik be a ACR példányát előtt képek rá. A [Basic, Standard és Premium termékváltozat](container-registry-skus.md), az Azure-identitás használatával hitelesítheti.

Használja a [az acr bejelentkezési](https://docs.microsoft.com/en-us/cli/azure/acr#az_acr_login) parancs és a beállításjegyzék a hitelesítő adatok gyorsítótárazása hitelesítéséhez. Cserélje le `<acrName>` az előző lépésekben létrehozott beállításjegyzék nevével.

```azurecli
az acr login --name <acrName>
```

A parancs visszaadja `Login Succeeded` teljes.

## <a name="get-application-code"></a>Az alkalmazáskód letöltése

Ebben az oktatóanyagban a minta tartalmazza-val készült kis webalkalmazás [ASP.NET Core](http://dot.net). Az alkalmazás szolgál, amely megjeleníti a régió, ahol a lemezkép használatával telepített, Azure tároló beállításjegyzék HTML-lapot.

![Az oktatóanyag alkalmazása böngészőben megjelenítve][tut-app-01]

A git segítségével töltse le a minta egy helyi könyvtárba, és `cd` a könyvtárba:

```bash
git clone https://github.com/Azure-Samples/acr-helloworld.git
cd acr-helloworld
```

## <a name="update-dockerfile"></a>Frissítés Dockerfile

A mintában szereplő Dockerfile bemutatja, hogyan épül fel a tárolót. Elindul egy hivatalos [aspnetcore](https://store.docker.com/community/images/microsoft/aspnetcore) lemezképet, az alkalmazás-fájlokat a tárolóba másolja függőségek telepíti, akkor a segítségével a hivatalos kimeneti [aspnetcore-build](https://store.docker.com/community/images/microsoft/aspnetcore-build) lemezképet, és végül optimalizált aspnetcore képet alkot.

A Dockerfile itt található: `./AcrHelloworld/Dockerfile` a klónozott forrásból.

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

Az alkalmazás a *acr-helloworld* kép ellenőrzi a régió, ahol tárolója állított, amelyet a DNS a beállításjegyzék bejelentkezési kiszolgáló kapcsolatos információk lekérdezése. A beállításjegyzék bejelentkezési kiszolgáló URL-címet kell megadnia a `DOCKER_REGISTRY` a Dockerfile környezeti változót.

Először, a beállításjegyzék bejelentkezési kiszolgáló URL-cím lekérése, a `az acr show` parancsot. Cserélje le `<acrName>` az előző lépésekben létrehozott beállításjegyzék nevével.

```azurecli
az acr show --name <acrName> --query "{acrLoginServer:loginServer}" --output table
```

Kimenet:

```bash
AcrLoginServer
-----------------------------
uniqueregistryname.azurecr.io
```

Következő lépésként frissítse a `DOCKER_REGISTRY` sor a beállításjegyzék bejelentkezési URL-címére. Ebben a példában a sort, hogy a példa a beállításjegyzék nevére, frissítjük *uniqueregistryname*:

```dockerfile
ENV DOCKER_REGISTRY uniqueregistryname.azurecr.io
```

## <a name="build-container-image"></a>Tároló lemezkép

Most, hogy a Dockerfile frissítése a beállításjegyzékben URL-címét, használhat `docker build` tároló lemezkép létrehozásához. A következő parancsot a lemezkép és címke azt az URL-CÍMÉT a titkos beállításjegyzék ismét cserélje le a `<acrName>` nevű, a beállításjegyzék:

```bash
docker build . -f ./AcrHelloworld/Dockerfile -t <acrName>.azurecr.io/acr-helloworld:v1
```

Kimeneti több sornyi jelennek meg a Docker-lemezkép összeállítása (Itt látható csonkolt):

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

Használja a `docker images` parancs a beépített kép megtekintéséhez:

```bash
docker images
```

Kimenet:

```bash
REPOSITORY                                     TAG                 IMAGE ID            CREATED              SIZE
uniqueregistryname.azurecr.io/acr-helloworld   v1                  c9ca1763cfb1        About a minute ago   285MB
...
```

## <a name="push-image-to-azure-container-registry"></a>Azure-tároló beállításjegyzék leküldéses kép

Végül a `docker push` parancs végrehajtásával küldje be a *acr-helloworld* kép a beállításjegyzékhez. Cserélje le `<acrName>` a beállításjegyzék nevével.

```bash
docker push <acrName>.azurecr.io/acr-helloworld:v1
```

A beállításjegyzékben a georeplikáció konfigurálását, mert a lemezkép egyaránt automatikusan replikálódik a *USA nyugati régiója* és *USA keleti régiója* a egyetlen régiók `docker push` parancs.

Kimenet:

```bash
The push refers to a repository [uniqueregistryname.azurecr.io/acr-helloworld]
9716cfe18412: Pushed
074867a942d5: Pushed
a77666945b96: Pushed
953ff32f2036: Pushed
aa2e77726d3c: Pushed
98b800c91d50: Pushed
a75caa09eb1f: Pushed
v1: digest: sha256:c515bcebf249b591b558318e2d0ec21d1320340dbf335730eb32372ff7d34255 size: 1792
```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létre egy saját, a georeplikált tároló beállításjegyzék, a tároló lemezkép beépített, és majd leküldött lemezkép a beállításjegyzékhez. Ebben az oktatóanyagban szereplő lépések végrehajtásával meg:

> [!div class="checklist"]
> * Egy Azure-tárolót georeplikált beállításjegyzék létrehozása
> * Klónozott alkalmazás forráskódjához a Githubról
> * A beépített alkalmazás forrásból tároló Docker képként
> * A tároló kép leküldött a beállításjegyzéket

Előzetes központi telepítése a tároló több tárolók példányok használó webalkalmazások georeplikáció helyileg a lemezképek kiszolgálásához megismeréséhez következő oktatóanyagot.

> [!div class="nextstepaction"]
> [Azure-tároló beállításjegyzékből webalkalmazás telepítése](container-registry-tutorial-deploy-app.md)

<!-- IMAGES -->
[tut-portal-01]: ./media/container-registry-tutorial-prepare-registry/tut-portal-01.png
[tut-portal-02]: ./media/container-registry-tutorial-prepare-registry/tut-portal-02.png
[tut-portal-03]: ./media/container-registry-tutorial-prepare-registry/tut-portal-03.png
[tut-portal-04]: ./media/container-registry-tutorial-prepare-registry/tut-portal-04.png
[tut-portal-05]: ./media/container-registry-tutorial-prepare-registry/tut-portal-05.png
[tut-app-01]: ./media/container-registry-tutorial-prepare-registry/tut-app-01.png
[tut-map-01]: ./media/container-registry-tutorial-prepare-registry/tut-map-01.png
