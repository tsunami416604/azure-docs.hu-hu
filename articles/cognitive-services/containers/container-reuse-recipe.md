---
title: A Docker-tárolók receptjei
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan hozhat létre, tesztelheti és tárolhatja a tárolókat a telepítési és újrahasznosítási konfigurációs beállítások némelyikével vagy mindegyikével.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: 97342f1dd4f6ce343626ba6c294f09dabe3db5c0
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717205"
---
# <a name="create-containers-for-reuse"></a>Tárolók létrehozása újbóli felhasználáshoz

Ezekkel a tároló receptek használatával Cognitive Services tárolókat hozhat létre, amelyeket újra felhasználhat. A tárolók egy vagy több konfigurációs beállítással is létrehozhatók, így _nem_ szükségesek a tároló indításakor.

Ha ezt az új réteget (a beállításokkal), és helyileg tesztelte, akkor a tárolót egy tároló-beállításjegyzékben tárolhatja. A tároló indításakor csak azokat a beállításokat kell megadnia, amelyek jelenleg nincsenek tárolva a tárolóban. A privát beállításjegyzék-tároló konfigurációs helyet biztosít a beállításoknak a alkalmazásban való átadásához.

## <a name="docker-run-syntax"></a>Docker-futtatási szintaxis

A dokumentumban szereplő `docker run` példákban egy `^` vonal folytatási karakterrel rendelkező Windows-konzolra kerül sor. Saját használatra a következőket vegye figyelembe:

* Ne módosítsa az argumentumok sorrendjét, hacsak nem ismeri a Docker-tárolókat.
* Ha a Windows rendszertől eltérő operációs rendszert használ, vagy a Windows-konzoltól eltérő konzolt használ, használja a megfelelő konzolt/terminált, a csatlakoztatások mappa szintaxisát, valamint a konzol és a rendszer vonal folytatási karakterét.  Mivel a Cognitive Services tároló egy Linux operációs rendszer, a cél csatlakoztatása a Linux-stílusú mappa szintaxisát használja.
* `docker run` példákban a `c:` meghajtón lévő könyvtárat használja, hogy elkerülje az engedélyek ütközését a Windows rendszeren. Ha egy adott könyvtárat kell használnia bemeneti könyvtárként, előfordulhat, hogy meg kell adnia a Docker szolgáltatás engedélyét.

## <a name="store-no-configuration-settings-in-image"></a>A rendszerkép nem tárolja a konfigurációs beállításokat

Az egyes szolgáltatásokra vonatkozó példa `docker run` parancsok nem tárolnak konfigurációs beállításokat a tárolóban. Amikor a tárolót egy konzolról vagy egy beállításjegyzék-szolgáltatásból indítja el, ezeket a konfigurációs beállításokat át kell adni. A privát beállításjegyzék-tároló konfigurációs helyet biztosít a beállításoknak a alkalmazásban való átadásához.

## <a name="reuse-recipe-store-all-configuration-settings-with-container"></a>Recept újrafelhasználása: az összes konfigurációs beállítás tárolása tárolóval

Az összes konfigurációs beállítás tárolásához hozzon létre egy `Dockerfile` ezekkel a beállításokkal.

Az ezzel a módszerrel kapcsolatos problémák:

* Az új tároló külön névvel és címkével rendelkezik az eredeti tárolóban.
* Ezeknek a beállításoknak a módosításához módosítania kell a Docker értékeit, újra kell építenie a rendszerképet, és újra közzé kell tennie a beállításjegyzékben.
* Ha valaki hozzáfér a tároló-beállításjegyzékhez vagy a helyi gazdagéphez, akkor futtathatja a tárolót, és használhatja a Cognitive Services-végpontokat.
* Ha a kognitív szolgáltatás nem igényel bemeneti csatlakoztatásokat, ne adja hozzá a `COPY` sorokat a Docker.

Hozzon létre Docker, húzza a használni kívánt meglévő Cognitive Services-tárolóból, majd a tároló által igényelt információk beállításához és lekéréséhez használja a Docker Docker-parancsait.

Példa:

* Beállítja a számlázási végpontot, `{BILLING_ENDPOINT}` a gazdagép környezeti kulcsának `ENV`használatával.
* Beállítja a számlázási API-kulcsot, `{ENDPOINT_KEY}` a gazdagép környezeti kulcsával a "ENV" használatával.

### <a name="reuse-recipe-store-billing-settings-with-container"></a>Recept újrafelhasználása: számlázási beállítások tárolása tárolóval

Ez a példa bemutatja, hogyan hozhatja létre a Text Analytics "hangulati tárolót egy Docker.

```Dockerfile
FROM mcr.microsoft.com/azure-cognitive-services/sentiment:latest
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
```

Igény szerint hozza létre és futtassa a tárolót [helyileg](#how-to-use-container-on-your-local-host) vagy a [saját beállításjegyzék-tárolójában](#how-to-add-container-to-private-registry) .

### <a name="reuse-recipe-store-billing-and-mount-settings-with-container"></a>Recept újrafelhasználása: a számlázás és a csatlakoztatási beállítások tárolása tárolóval

Ez a példa bemutatja, hogyan használhatók a Language Understanding, hogyan mentheti a számlázást és a modelleket a Docker.

* A Language Understanding (LUIS) modell fájljának másolása a gazdagép fájlrendszeréről `COPY`használatával.
* A LUIS-tároló több modellt is támogat. Ha az összes modell ugyanabban a mappában van tárolva, csak egy `COPY` utasítást kell megadnia.
* Futtassa a Docker-fájlt a modell bemeneti könyvtárának relatív szülőjének. A következő példához futtassa a `docker build` és `docker run` parancsokat a `/input`relatív szülőjének. A `COPY` parancs első `/input` a gazdaszámítógép könyvtára. A második `/input` a tároló könyvtára.

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
COPY /input /input
```

Igény szerint hozza létre és futtassa a tárolót [helyileg](#how-to-use-container-on-your-local-host) vagy a [saját beállításjegyzék-tárolójában](#how-to-add-container-to-private-registry) .

## <a name="how-to-use-container-on-your-local-host"></a>A tároló használata a helyi gazdagépen

A Docker-fájl létrehozásához cserélje le a `<your-image-name>`t a rendszerkép új nevére, majd használja a következőt:

```console
docker build -t <your-image-name> .
```

A rendszerkép futtatásához és a tároló leállításakor való eltávolításához (`--rm`):

```console
docker run --rm <your-image-name>
```

## <a name="how-to-add-container-to-private-registry"></a>Tároló hozzáadása a privát beállításjegyzékhez

Az alábbi lépéseket követve használhatja a Docker, és elhelyezheti az új rendszerképet a privát tároló beállításjegyzékében.  

1. Hozzon létre egy `Dockerfile`t az újbóli receptből származó szöveggel. Egy `Dockerfile` nem rendelkezik bővítménysel.

1. A szögletes zárójelben lévő összes értéket cserélje le a saját értékeire.

1. A következő parancs használatával hozza létre a fájlt egy rendszerképre a parancssorban vagy a terminálon. Cserélje le az értékeket a szögletes zárójelek közé, `<>`a saját tároló nevét és címkéjét.  

    A címke beállítás, `-t`, a tárolóban megváltoztatott adatokkal kapcsolatos információk hozzáadásának módja. A `modified-LUIS` tároló neve például azt jelzi, hogy az eredeti tárolót rétegzetták. `with-billing-and-model` címke neve azt jelzi, hogyan módosult a Language Understanding (LUIS) tároló.

    ```Bash
    docker build -t <your-new-container-name>:<your-new-tag-name> .
    ```

1. Jelentkezzen be az Azure CLI-be egy konzolról. Ez a parancs egy böngészőt nyit meg, és hitelesítést igényel. A hitelesítés után lezárhatja a böngészőt, és folytathatja a munkát a konzolon.

    ```azurecli
    az login
    ```

1. Jelentkezzen be a privát beállításjegyzékbe az Azure CLI-vel egy konzolról.

    Cserélje le az értékeket a szögletes zárójelek közé, `<my-registry>`a saját beállításjegyzék-nevére.  

    ```azurecli
    az acr login --name <my-registry>
    ```

    Ha egy egyszerű szolgáltatásnevet rendel hozzá, akkor is bejelentkezhet a Docker-bejelentkezéssel.

    ```Bash
    docker login <my-registry>.azurecr.io
    ```

1. Címkézze fel a tárolót a privát beállításjegyzék helyén. Cserélje le az értékeket a szögletes zárójelek közé, `<my-registry>`a saját beállításjegyzék-nevére. 

    ```Bash
    docker tag <your-new-container-name>:<your-new-tag-name> <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

    Ha nem használja a címke nevét, `latest` hallgatólagos.

1. Küldje le az új rendszerképet a saját tároló-beállításjegyzékbe. Ha megtekinti a saját tároló beállításjegyzékét, az alábbi CLI-parancsban használt tároló neve lesz a tárház neve.

    ```Bash
    docker push <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

## <a name="next-steps"></a>Következő lépések

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