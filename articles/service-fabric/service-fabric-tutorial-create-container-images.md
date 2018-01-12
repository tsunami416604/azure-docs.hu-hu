---
title: "Tároló képek létrehozása az Azure Service Fabric |} Microsoft Docs"
description: "Útmutató a több tároló Service Fabric-alkalmazás lemezképeket tároló létrehozásához."
services: service-fabric
documentationcenter: 
author: suhuruli
manager: timlt
editor: suhuruli
tags: servicefabric
keywords: "Docker, tárolók, mikroszolgáltatások létrehozására, a Service Fabric, Azure"
ms.assetid: 
ms.service: service-fabric
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2017
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: 9ea5be818cfc104c243ce31cc0e2d0f10135259f
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2018
---
# <a name="create-container-images-for-service-fabric"></a>A Service Fabric tároló lemezképek létrehozása

Ez az oktatóanyag egy bemutató oktatóanyag bemutatja, hogyan kell tárolók használata a Linux Service Fabric-fürt sorozat része. Ebben az oktatóanyagban egy több tároló alkalmazás Service Fabric való használatra kész. A következő útmutatókból ezeket a lemezképeket használja a Service Fabric-alkalmazás részeként. Ezen oktatóanyag segítségével megtanulhatja a következőket: 

> [!div class="checklist"]
> * Klónozott alkalmazás adatforrás a Githubról  
> * Az alkalmazás forrás tároló lemezkép létrehozása
> * Azure tároló beállításjegyzék (ACR) példányt telepítése
> * A tároló lemezkép címkéjében ACR
> * ACR a Rendszerkép feltöltése

Az oktatóanyag adatsorozat elsajátíthatja, hogyan: 

> [!div class="checklist"]
> * A Service Fabric tároló lemezképek létrehozása
> * [Hozza létre, és a tárolók a Service Fabric-alkalmazás futtatása](service-fabric-tutorial-package-containers.md)
> * [Hogyan történik meg a Service Fabric feladatátvételi és a méretezésről](service-fabric-tutorial-containers-failover.md)

## <a name="prerequisites"></a>Előfeltételek

- Linux-fejlesztési környezet beállítása a Service Fabric. Kövesse az utasításokat [Itt](service-fabric-get-started-linux.md) a Linux-környezetet. 
- Ez az oktatóanyag megköveteli, hogy futnak-e az Azure parancssori felület 2.0.4 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 
- Emellett szükséges, hogy rendelkezik-e elérhető Azure-előfizetéssel. További információt az ingyenes próbaverzióját, [Itt](https://azure.microsoft.com/free/).

## <a name="get-application-code"></a>Az alkalmazáskód letöltése

Ebben az oktatóanyagban használt mintaalkalmazás szavazó app. Az alkalmazás egy előtér-webkiszolgáló és egy háttér-Redis-példányt tartalmaz. Az összetevőket tárolóban képek vannak csomagolva. 

A git segítségével töltse le az alkalmazást a fejlesztési környezetet.

```bash
git clone https://github.com/Azure-Samples/service-fabric-containers.git

cd service-fabric-containers/Linux/container-tutorial/
```

A megoldás két mappát tartalmaz, és a "docker-compse.yml" fájl. Az "azure-szavazás" mappa a Python előtér-szolgáltatás együtt a lemezkép használatával Dockerfile tartalmazza. A "Voting" könyvtárban található a Service Fabric-alkalmazáscsomagot, amely a fürt központi telepítése. Ezeket a könyvtárakat tartalmazza a szükséges eszközök ehhez az oktatóanyaghoz.  

## <a name="create-container-images"></a>Tároló képek létrehozása

Belül a **azure-szavazat** directory, a következő parancsot az előtér-webkiszolgáló-összetevő lemezkép létrehozására. A parancs a Dockerfile ebben a könyvtárban a lemezkép. 

```bash
docker build -t azure-vote-front .
```

Ez a parancs némi időbe telhet, mert a szükséges függőségek igénylése a Docker Hub kell. Amikor elkészült, használja a [docker képek](https://docs.docker.com/engine/reference/commandline/images/) parancsot a létrehozott lemezképek.

```bash
docker images
```

Figyelje meg, hogy két lemezképet letöltése vagy létrehozni. A *azure-szavazat-front* rendszerkép tartalmazza az alkalmazás. Az származik a *python* Docker központi lemezkép.

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-front             latest              052c549a75bf        About a minute ago   708MB
tiangolo/uwsgi-nginx-flask   python3.6           590e17342131        5 days ago           707MB

```

## <a name="deploy-azure-container-registry"></a>Telepítse az Azure tároló beállításjegyzék

Először futtassa a **az bejelentkezési** parancs futtatásával jelentkezzen be az Azure-fiókjával. 

```bash
az login
```

Ezután használhatja a **az fiók** parancs segítségével hozza létre az Azure-tárolóba beállításkulcs előfizetése kiválasztásához. Meg kell adnia az előfizetés-azonosító az Azure-előfizetés < ELŐFIZETÉS_AZONOSÍTÓJA > helyett. 

```bash
az account set --subscription <subscription_id>
```

Egy Azure-tároló beállításjegyzék való telepítésekor, először egy erőforráscsoportot. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot az **az group create** paranccsal. Ebben a példában az erőforráscsoport neve *myResourceGroup* jön létre a *westus* régióban.

```bash
az group create --name <myResourceGroup> --location westus
```

Hozzon létre egy Azure-tárolóba beállításjegyzéket a **az acr létrehozása** parancsot. Cserélje le \<acrName > nevű, a tároló beállításjegyzék szeretne létrehozni az előfizetésben. Ez a név nem egyedi és betűket is tartalmazhat. 

```bash
az acr create --resource-group <myResourceGroup> --name <acrName> --sku Basic --admin-enabled true
```

Ez az oktatóanyag a többi, egész használjuk "acrName" helyőrzőként a választott beállításjegyzék Tárolónév. Ellenőrizze, hogy jegyezze fel ezt az értéket. 

## <a name="log-in-to-your-container-registry"></a>Jelentkezzen be a tároló beállításjegyzék

Jelentkezzen be a ACR példányát előtt képek rá. Használja a **az acr bejelentkezési** parancs használatával végrehajtani a műveletet. Adjon meg egyedi név, a tároló beállításjegyzék létrehozásakor.

```bash
az acr login --name <acrName>
```

A parancs visszaadja a "Sikeres bejelentkezés" üzenet, amint befejeződött.

## <a name="tag-container-images"></a>Címke tároló lemezképek

Minden egyes tároló lemezképet kell címkézését a beállításjegyzék loginServer nevét. Ezt a címkét tároló képek terjesztése egy kép beállításjegyzék műveletterv szolgál.

Lemezképek aktuális listájának megtekintéséhez használja a [docker képek](https://docs.docker.com/engine/reference/commandline/images/) parancsot.

```bash
docker images
```

Kimenet:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-front             latest              052c549a75bf        About a minute ago   708MB
tiangolo/uwsgi-nginx-flask   python3.6           590e17342131        5 days ago           707MB
```

Ahhoz, hogy a loginServer nevét, a következő parancsot:

```bash
az acr show --name <acrName> --query loginServer --output table
```

Ez adja kimenetként, egy tábla a következő eredményekkel. Ez az eredmény használandó címke a **azure-szavazat-front** kép előtt, a tároló beállításjegyzék a következő lépésben.

```bash
Result
------------------
<acrName>.azurecr.io
```

Most, címkét a *azure-szavazat-front* a tároló beállításjegyzék loginServer lemezkép. Továbbá adja hozzá `:v1` , a lemezkép neve végén. Ezt a címkét azt jelzi, hogy a lemezkép verziója.

```bash
docker tag azure-vote-front <acrName>.azurecr.io/azure-vote-front:v1
```

Miután megjelölve, futtassa az "docker képek" a művelet megerősítéséhez.


Kimenet:

```bash
REPOSITORY                             TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front                       latest              052c549a75bf        23 minutes ago      708MB
<acrName>.azurecr.io/azure-vote-front   v1                  052c549a75bf       23 minutes ago      708MB
tiangolo/uwsgi-nginx-flask             python3.6           590e17342131        5 days ago          707MB

```

## <a name="push-images-to-registry"></a>Leküldéses képek beállításjegyzék

Leküldéses a *azure-szavazat-front* kép a beállításjegyzékhez. 

Az alábbi példa használatával, cserélje le a környezetből loginServer loginServer ACR neve.

```bash
docker push <acrName>.azurecr.io/azure-vote-front:v1
```

A docker leküldéses parancsok néhány percet igénybe vehet.

## <a name="list-images-in-registry"></a>A beállításjegyzékben lemezképek felsorolása

Olyan lemezképkészlet, amellyel az Azure-tárolóba beállításjegyzék értesítését listáját adja vissza, használja a [az acr tárház lista](/cli/azure/acr/repository#list) parancsot. A parancs frissíti az ACR-példány neve.

```bash
az acr repository list --name <acrName> --output table
```

Kimenet:

```bash
Result
----------------
azure-vote-front
```

Oktatóanyag befejezése után a tároló kép rendelkezik lett tárolja, a saját Azure tároló beállításjegyzék-példányban. Ez a rendszerkép rendszer ACR a Service Fabric-fürt a következő útmutatókból.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy alkalmazást a Githubról lett lekért és tároló képek létrehozott és a beállításjegyzék leküldve. A következő lépéseket hajtotta végre:

> [!div class="checklist"]
> * Klónozott alkalmazás adatforrás a Githubról  
> * Az alkalmazás forrás tároló lemezkép létrehozása
> * Azure tároló beállításjegyzék (ACR) példányt telepítése
> * A tároló lemezkép címkéjében ACR
> * ACR a Rendszerkép feltöltése

Előzetes csomagolás tárolók tájékozódhat az Yeoman használatával a Service Fabric-alkalmazás a következő oktatóanyagot. 

> [!div class="nextstepaction"]
> [Csomag és a Service Fabric-alkalmazásként tároló üzembe helyezése](service-fabric-tutorial-package-containers.md)