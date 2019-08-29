---
title: A Docker-tárolók receptjei
titleSuffix: Azure Cognitive Services
description: Ezekkel a tároló receptek használatával Cognitive Services tárolókat hozhat létre, amelyeket újra felhasználhat. A tárolók egy vagy több konfigurációs beállítással is létrehozhatók, így nem szükségesek a tároló indításakor. Ha ezt az új réteget (a beállításokkal), és helyileg tesztelte, akkor a tárolót egy tároló-beállításjegyzékben tárolhatja. A tároló indításakor csak azokat a beállításokat kell megadnia, amelyek jelenleg nincsenek tárolva a tárolóban.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: a8162f96051a73b9f6e6a6fe3ece020e0a94f08f
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70068824"
---
# <a name="create-containers-for-reuse"></a>Tárolók létrehozása újbóli használatra

Ezekkel a tároló receptek használatával Cognitive Services tárolókat hozhat létre, amelyeket újra felhasználhat. A tárolók egy vagy több konfigurációs beállítással is létrehozhatók, így _nem_ szükségesek a tároló indításakor.

Ha ezt az új réteget (a beállításokkal), és helyileg tesztelte, akkor a tárolót egy tároló-beállításjegyzékben tárolhatja. A tároló indításakor csak azokat a beállításokat kell megadnia, amelyek jelenleg nincsenek tárolva a tárolóban. A privát beállításjegyzék-tároló konfigurációs helyet biztosít a beállításoknak a alkalmazásban való átadásához.

## <a name="docker-run-syntax"></a>Docker-futtatási szintaxis

A jelen dokumentumban szereplő `^` példákegysorfolytatásikarakterrelrendelkezőWindows-konzoltfeltételeznek.`docker run` Saját használatra a következőket vegye figyelembe:

* Ne módosítsa az argumentumok sorrendje, kivéve, ha nagyon ismeri a docker-tárolókat.
* Ha a Windows rendszertől eltérő operációs rendszert használ, vagy a Windows-konzoltól eltérő konzolt használ, használja a megfelelő konzolt/terminált, a csatlakoztatások mappa szintaxisát, valamint a konzol és a rendszer vonal folytatási karakterét.  Mivel a Cognitive Services tároló egy Linux operációs rendszer, a cél csatlakoztatása a Linux-stílusú mappa szintaxisát használja.
* `docker run`a következő példák a `c:` meghajtón lévő könyvtárat használják, hogy elkerülje az engedélyek ütközését a Windowson. Ha szeretné használni a bemeneti könyvtár egy adott címtárhoz, előfordulhat, hogy kell biztosítania a docker szolgáltatás engedéllyel.

## <a name="store-no-configuration-settings-in-image"></a>A rendszerkép nem tárolja a konfigurációs beállításokat

Az egyes `docker run` szolgáltatásokra vonatkozó példák nem tárolnak konfigurációs beállításokat a tárolóban. Amikor a tárolót egy konzolról vagy egy beállításjegyzék-szolgáltatásból indítja el, ezeket a konfigurációs beállításokat át kell adni. A privát beállításjegyzék-tároló konfigurációs helyet biztosít a beállításoknak a alkalmazásban való átadásához.

## <a name="reuse-recipe-store-all-configuration-settings-with-container"></a>Recept újrafelhasználása: az összes konfigurációs beállítás tárolása tárolóval

Az összes konfigurációs beállítás tárolásához hozzon létre egy `Dockerfile` -t ezekkel a beállításokkal.

Az ezzel a módszerrel kapcsolatos problémák:

* Az új tároló külön névvel és címkével rendelkezik az eredeti tárolóban.
* Ezeknek a beállításoknak a módosításához módosítania kell a Docker értékeit, újra kell építenie a rendszerképet, és újra közzé kell tennie a beállításjegyzékben.
* Ha valaki hozzáfér a tároló-beállításjegyzékhez vagy a helyi gazdagéphez, akkor futtathatja a tárolót, és használhatja a Cognitive Services-végpontokat.
* Ha a kognitív szolgáltatás nem igényel bemeneti csatlakoztatásokat, ne adja hozzá `COPY` a sorokat a Docker.

Hozzon létre Docker, húzza a használni kívánt meglévő Cognitive Services-tárolóból, majd a tároló által igényelt információk beállításához és lekéréséhez használja a Docker Docker-parancsait.

Példa:

* Beállítja a számlázási végpontot `{BILLING_ENDPOINT}` a gazdagép környezeti kulcsának használatával. `ENV`
* Beállítja a számlázási API-kulcsot `{ENDPOINT_KEY}` a gazdagép környezeti kulcsáról a "env" használatával.

### <a name="reuse-recipe-store-billing-settings-with-container"></a>Recept újrafelhasználása: számlázási beállítások tárolása tárolóval

Ez a példa bemutatja, hogyan hozhatja létre a Text Analytics "hangulati tárolót egy Docker.

```Dockerfile
FROM mcr.microsoft.com/azure-cognitive-services/sentiment:latest
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
```

Igény szerint hozza létre és futtassa a tárolót [helyileg](#how-to-use-container-on-your-local-host) vagy a [saját beállításjegyzék](#how-to-add-container-to-private-registry) -tárolójában.

### <a name="reuse-recipe-store-billing-and-mount-settings-with-container"></a>Recept újrafelhasználása: a számlázás és a csatlakoztatási beállítások tárolása tárolóval

Ez a példa bemutatja, hogyan használhatók a Language Understanding, hogyan mentheti a számlázást és a modelleket a Docker.

* Az Language Understanding (LUIS) modell fájljának másolása a gazdagép fájlrendszeréről a használatával `COPY`.
* A LUIS-tároló több modellt is támogat. Ha az összes modell ugyanabban a mappában van tárolva, csak egy `COPY` utasítást kell megadnia.
* Futtassa a Docker-fájlt a modell bemeneti könyvtárának relatív szülőjének. Az alábbi példához futtassa a `docker build` és `docker run` a parancsot a relatív szülőtől `/input`. A `COPY` parancs `/input` első értéke a gazdaszámítógép könyvtára. A második `/input` a tároló könyvtára.

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
COPY /input /input
```

Igény szerint hozza létre és futtassa a tárolót [helyileg](#how-to-use-container-on-your-local-host) vagy a [saját beállításjegyzék](#how-to-add-container-to-private-registry) -tárolójában.

## <a name="how-to-use-container-on-your-local-host"></a>A tároló használata a helyi gazdagépen

A Docker-fájl létrehozásához cserélje `<your-image-name>` le a nevet a rendszerkép új nevére, majd használja a következőt:

```console
docker build -t <your-image-name> .
```

A rendszerkép futtatásához és a tároló leállításakor (`--rm`) való eltávolításához:

```console
docker run --rm <your-image-name>
```

## <a name="how-to-add-container-to-private-registry"></a>Tároló hozzáadása a privát beállításjegyzékhez

Az alábbi lépéseket követve használhatja a Docker, és elhelyezheti az új rendszerképet a privát tároló beállításjegyzékében.  

1. Hozzon `Dockerfile` létre egy szöveget az újrahasznosítási receptből. A `Dockerfile` nem rendelkezik bővítménysel.

1. A szögletes zárójelben lévő összes értéket cserélje le a saját értékeire.

1. A következő parancs használatával hozza létre a fájlt egy rendszerképre a parancssorban vagy a terminálon. Cserélje le az értékeket a szögletes zárójelben `<>`, a saját tároló nevével és címkéjével együtt.  

    A címke lehetőség, `-t`amely a tárolóban megváltoztatott adatok hozzáadását teszi lehetővé. A tároló neve `modified-LUIS` például azt jelzi, hogy az eredeti tároló rétegben van. A címke neve jelzi `with-billing-and-model` , hogy a Language Understanding (Luis) tároló hogyan lett módosítva.

    ```Bash
    docker build -t <your-new-container-name>:<your-new-tag-name> .
    ```

1. Jelentkezzen be az Azure CLI-be egy konzolról. Ez a parancs egy böngészőt nyit meg, és hitelesítést igényel. A hitelesítés után lezárhatja a böngészőt, és folytathatja a munkát a konzolon.

    ```azurecli
    az login
    ```

1. Jelentkezzen be a privát beállításjegyzékbe az Azure CLI-vel egy konzolról.

    A szögletes zárójelben `<my-registry>`lévő értékeket cserélje le a saját beállításjegyzékbeli nevére.  

    ```azurecli
    az acr login --name <my-registry>
    ```

    Ha egy egyszerű szolgáltatásnevet rendel hozzá, akkor is bejelentkezhet a Docker-bejelentkezéssel.

    ```Bash
    docker login <my-registry>.azurecr.io
    ```

1. Címkézze fel a tárolót a privát beállításjegyzék helyén. A szögletes zárójelben `<my-registry>`lévő értékeket cserélje le a saját beállításjegyzékbeli nevére. 

    ```Bash
    docker tag <your-new-container-name>:<your-new-tag-name> <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

    Ha nem használja a címke nevét, `latest` a feltételes.

1. Küldje le az új rendszerképet a saját tároló-beállításjegyzékbe. Ha megtekinti a saját tároló beállításjegyzékét, az alábbi CLI-parancsban használt tároló neve lesz a tárház neve.

    ```Bash
    docker push <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az Azure Container instance létrehozása és használata](azure-container-instance-recipe.md)

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