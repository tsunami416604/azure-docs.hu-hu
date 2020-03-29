---
title: Receptek a Docker-tárolókhoz
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan hozhat létre, tesztelheti és tárolhatja a tárolókat a központi telepítéshez és az újrafelhasználáshoz használt konfigurációs beállítások némelyikével vagy mindegyikével.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: 97342f1dd4f6ce343626ba6c294f09dabe3db5c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76717205"
---
# <a name="create-containers-for-reuse"></a>Tárolók létrehozása újbóli felhasználáshoz

Ezekkel a tárolóreceptekkel hozzon létre kognitív szolgáltatások tárolók, amelyek újra felhasználhatók. A tárolók néhány vagy az összes konfigurációs beállítással is létrehozhatók, így a tároló indításakor _nincs_ rájuk szükség.

Miután rendelkezik ezzel az új rétegtárolóval (beállításokkal), és helyileg tesztelte, a tárolót egy tárolóbeállítás-jegyzékben tárolhatja. Amikor a tároló elindul, csak azokra a beállításokra lesz szüksége, amelyek jelenleg nem vannak a tárolóban tárolva. A privát beállításjegyzék-tároló konfigurációs területet biztosít a beállítások átadására.

## <a name="docker-run-syntax"></a>Docker-futtatásszintaxis

A `docker run` dokumentumban szereplő példák egy `^` sorfolytatási karakterrel rendelkező Windows-konzolt feltételeznek. Vegye figyelembe a következőket saját használatra:

* Ne módosítsa az argumentumok sorrendjét, kivéve, ha nagyon ismeri a docker-tárolók.
* Ha nem Windows operációs rendszert vagy a Windows konzoltól eltérő konzolt használ, használja a megfelelő konzolt/terminált, a mappa szintaxisát a csatlakoztatásokhoz, és sorfolytatási karaktert a konzolhoz és a rendszerhez.  Mivel a Cognitive Services tároló egy Linux operációs rendszer, a cél-csatlakoztatás linuxos mappa szintaxist használ.
* `docker run`példák a meghajtón kívüli könyvtárat használják az `c:` engedélyütközések elkerülésére a Windows rendszeren. Ha egy adott könyvtárat kell használnia bemeneti könyvtárként, előfordulhat, hogy meg kell adnia a docker-szolgáltatás engedélyét.

## <a name="store-no-configuration-settings-in-image"></a>Nem tárolhat konfigurációs beállításokat a képen

Az `docker run` egyes szolgáltatások példaparancsai nem tárolnak konfigurációs beállításokat a tárolóban. Amikor a tárolót konzolról vagy beállításjegyzék-szolgáltatásból indítja el, ezeket a konfigurációs beállításokat át kell adni. A privát beállításjegyzék-tároló konfigurációs területet biztosít a beállítások átadására.

## <a name="reuse-recipe-store-all-configuration-settings-with-container"></a>Recept újrafelhasználása: az összes konfigurációs beállítás tárolása tárolóval

Az összes konfigurációs beállítás tárolásához hozzon létre egy `Dockerfile` beállítást ezekkel a beállításokkal.

Ezzel a megközelítéssel kapcsolatos kérdések:

* Az új tároló egy külön nevet és címkét az eredeti tároló.
* A beállítások módosításához módosítania kell a Docker-fájl értékeit, újra kell építenie a lemezképet, és újra közzé kell tennie a rendszerleíró adatbázisban.
* Ha valaki hozzáférést kap a tároló beállításjegyzékhez vagy a helyi állomáshoz, futtathatja a tárolót, és használhatja a Cognitive Services-végpontokat.
* Ha a Cognitive Service nem igényel bemeneti csatlakoztatások, ne adja hozzá a sorokat a `COPY` Docker-fájlhoz.

Hozzon létre Dockerfile, lekérésea a meglévő Cognitive Services-tároló használni kívánt, majd használja a Docker-parancsokat a Dockerfile beállítani vagy lekérése információkat a tároló szüksége van.

Ez a példa:

* Beállítja a `{BILLING_ENDPOINT}` számlázási végpontot a `ENV`gazdagép környezeti kulcsából a használatával.
* Beállítja a számlázási API-kulcs, `{ENDPOINT_KEY}` a gazdagép környezeti kulcs segítségével ENV.

### <a name="reuse-recipe-store-billing-settings-with-container"></a>Recept újrafelhasználása: tárolóval rendelkező számlázási beállítások tárolása

Ez a példa bemutatja, hogyan hozhat létre a Text Analytics hangulattároló egy Docker-fájlból.

```Dockerfile
FROM mcr.microsoft.com/azure-cognitive-services/sentiment:latest
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
```

Szükség szerint hozhatja létre és futtathatja a tárolót [helyileg](#how-to-use-container-on-your-local-host) vagy a [saját rendszerleíró adatbázis tárolójából.](#how-to-add-container-to-private-registry)

### <a name="reuse-recipe-store-billing-and-mount-settings-with-container"></a>Recept újrafelhasználása: tárolási számlázási és csatlakoztatási beállítások tárolóval

Ez a példa bemutatja, hogyan használhatja a nyelvi megértés, a számlázás és a modellek mentése a Docker-fájlból.

* A nyelvismertetési (LUIS) modellfájlt másolja az `COPY`állomás fájlrendszeréből a használatával.
* A LUIS-tároló egynél több modellt támogat. Ha az összes modell ugyanabban a mappában `COPY` van tárolva, akkor mindegyiknek szüksége van egy utasításra.
* Futtassa a docker-fájlt a modell bemeneti könyvtárának relatív szülőjéből. A következő példában `docker build` futtassa a `docker run` és `/input`a parancsokat a relatív szülőjétől. Az `/input` első `COPY` a parancsban a gazdaszámítógép könyvtára. A `/input` második a tároló könyvtára.

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
COPY /input /input
```

Szükség szerint hozhatja létre és futtathatja a tárolót [helyileg](#how-to-use-container-on-your-local-host) vagy a [saját rendszerleíró adatbázis tárolójából.](#how-to-add-container-to-private-registry)

## <a name="how-to-use-container-on-your-local-host"></a>A tároló használata a helyi gazdagépen

A Docker-fájl létrehozásához cserélje le `<your-image-name>` a lemezkép új nevét, majd használja:

```console
docker build -t <your-image-name> .
```

A lemezkép futtatásához és eltávolításához,`--rm`amikor a tároló leáll ( ):

```console
docker run --rm <your-image-name>
```

## <a name="how-to-add-container-to-private-registry"></a>Tároló hozzáadása a magánrendszerleíró adatbázishoz

Kövesse az alábbi lépéseket a Docker-fájl használatához, és helyezze el az új lemezképet a privát tároló beállításjegyzékében.  

1. Hozzon `Dockerfile` létre egy a szöveget újra recept. A-nak `Dockerfile` nincs hosszabbítása.

1. Cserélje le a szögletes zárójelben lévő értékeket a saját értékeire.

1. A fájlt a következő parancs segítségével a parancssorból vagy terminálból képpé alakítsa. Cserélje le a szögletes `<>`zárójelben lévő értékeket a saját tárolónevére és címkéjére.  

    A címke `-t`beállítás , egy módja annak, hogy információt adjon arról, hogy mit módosított a tároló. Például egy tároló `modified-LUIS` neve azt jelzi, hogy az eredeti tároló réteges. A címke `with-billing-and-model` neve azt jelzi, hogy a nyelvi megértés (LUIS) tároló módosult.

    ```Bash
    docker build -t <your-new-container-name>:<your-new-tag-name> .
    ```

1. Jelentkezzen be az Azure CLI-be egy konzolról. Ez a parancs megnyit egy böngészőt, és hitelesítést igényel. A hitelesítést követően bezárhatja a böngészőt, és folytathatja a munkát a konzolon.

    ```azurecli
    az login
    ```

1. Jelentkezzen be a privát beállításjegyzékébe az Azure CLI konzolról.

    Cserélje le a szögletes `<my-registry>`zárójelben lévő értékeket a saját beállításjegyzék-nevére.  

    ```azurecli
    az acr login --name <my-registry>
    ```

    A docker-bejelentkezéssel is bejelentkezhet, ha egyszerű szolgáltatáshoz van hozzárendelve.

    ```Bash
    docker login <my-registry>.azurecr.io
    ```

1. Címkézze fel a tárolót a saját rendszerleíró adatbázis helyével. Cserélje le a szögletes `<my-registry>`zárójelben lévő értékeket a saját beállításjegyzék-nevére. 

    ```Bash
    docker tag <your-new-container-name>:<your-new-tag-name> <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

    Ha nem használ címkenevet, `latest` a hallgatólagos.

1. Az új lemezkép leküldése a privát tároló rendszerleíró adatbázisába. A privát tároló beállításjegyzékének megtekintésekor a következő CLI-parancsban használt tárolónév lesz a tárház neve.

    ```Bash
    docker push <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Azure Container Instance létrehozása és használata](azure-container-instance-recipe.md)

<!--
## Store input and output configuration settings

Bake in input params only

FROM containerpreview.azurecr.io/microsoft/cognitive-services-luis:<tag>
COPY luisModel1 /input/
COPY luisModel2 /input/

## Store all configuration settings

If you are a single manager of the container, you may want to store all settings in the container. The new, resulting container will not need any variables passed in to run. 

Issues with this approach:

* In order to change these settings, you will have to change the values of the Dockerfile and rebuild the file. 
* If someone gets access to your container registry or your local host, they can run the container and use the Cognitive Services endpoints. 

The following _partial_ Dockerfile shows how to statically set the values for billing and model. This example uses the 

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing=<billing value>
ENV apikey=<apikey value>
COPY luisModel1 /input/
COPY luisModel2 /input/
```

->