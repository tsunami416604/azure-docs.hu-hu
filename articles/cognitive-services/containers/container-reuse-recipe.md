---
title: A Docker-tárolók készült receptek között
titleSuffix: Azure Cognitive Services
description: A tároló receptek használatával, amelyek felhasználhatók a Cognitive Services-tárolók létrehozása. Tárolók néhány vagy az összes konfigurációs beállításokkal épül fel, hogy nincs rájuk szükség, amikor a tároló elindult. Ha úgy, hogy ez a tároló új réteg (beállítások), és helyileg tesztelt, a tároló a tárolóregisztrációs tárolhatja. Ha a tároló elindult, csak kell ezeket a beállításokat, amelyek jelenleg nem tárolja a tárolót.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 37fa972d52c564c4d61e5923c2b3dc48bde9d2ee
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445812"
---
# <a name="create-containers-for-reuse"></a>Ismételt használata a tárolók létrehozása

A tároló receptek használatával, amelyek felhasználhatók a Cognitive Services-tárolók létrehozása. Tárolók építhetők fel néhány vagy az összes konfigurációs beállításokat, hogy azok _nem_ van szükség, amikor a tároló elindult.

Ha úgy, hogy ez a tároló új réteg (beállítások), és helyileg tesztelt, a tároló a tárolóregisztrációs tárolhatja. Ha a tároló elindult, csak kell ezeket a beállításokat, amelyek jelenleg nem tárolja a tárolót. A privát tárolójegyzék tároló itt konfigurációs adnia ezeket a beállításokat.

## <a name="docker-run-syntax"></a>Docker, futtassa a szintaxis

Bármely `docker run` ebben a dokumentumban szereplő példák feltételezik egy Windows konzolon egy `^` folytatási karakter. sor. Vegye figyelembe a következőket saját használatra:

* Ne módosítsa az argumentumok sorrendje, kivéve, ha nagyon ismeri a docker-tárolókat.
* Ha Windows operációs rendszert, vagy egy eltérő Windows konzol konzolt használ, használja a megfelelő konzol/terminálon, a mappa szintaxis csatlakoztatása és a konzol és a rendszer folytatási jelző karakter.  A Cognitive Services-tároló egy olyan Linux operációs rendszer, mert a célként megadott csatlakoztatási szintaxissal egy Linux-stílusú mappát.
* `docker run` Példák a könyvtárat használja ki a `c:` meghajtón a Windows-engedély ütközések elkerülése érdekében. Ha szeretné használni a bemeneti könyvtár egy adott címtárhoz, előfordulhat, hogy kell biztosítania a docker szolgáltatás engedéllyel.

## <a name="store-no-configuration-settings-in-image"></a>Nincsenek konfigurációs beállítások Store kép

A példában `docker run` parancsokat minden egyes szolgáltatás ne tárolja a konfigurációs beállítások a tárolóban. Amikor a tároló-konzol vagy a beállításjegyzék szolgáltatásból, ezeket a konfigurációs beállításokat kell megadni. A privát tárolójegyzék tároló itt konfigurációs adnia ezeket a beállításokat.

## <a name="reuse-recipe-store-all-configuration-settings-with-container"></a>Újból felhasználhatja a recept: a tároló összes konfigurációs beállítások tárolására

Annak érdekében, hogy minden beállítást tárol, hozzon létre egy `Dockerfile` ezekkel a beállításokkal.

Ezzel a módszerrel problémák:

* Az új tároló rendelkezik, külön nevét és az eredeti tároló-címke.
* Annak érdekében, hogy ezek a beállítások módosíthatók, akkor módosítsa az értékeket, a docker-fájlban, a rendszerkép újraépítése és ismételt közzététele a beállításjegyzékbe.
* Ha valaki megszerzi a tárolóregisztrációs adatbázis vagy a helyi gazdagép a hozzáférést, a tároló futtatásához, és a Cognitive Services-végpontok használata.
* A Cognitive Services-szolgáltatás bemeneti csatlakoztatása nem igényel, ha nem adja hozzá a `COPY` sorokat, a docker-fájlban.

Hozzon létre jogosultak-e a meglévő Cognitive Services-tárolót szeretne használni, a docker-fájlban, majd használja a docker-parancsokat a docker-fájlban vagy információkat lekérni a tároló van szüksége.

Ebben a példában:

* Beállítja a számlázási végpont `{BILLING_ENDPOINT}` a gazdagépről a környezet kulcs használatával `ENV`.
* A számlázási API-kulcsot, csoportok `{ENDPOINT_KEY}` a gazdagépről a környezet kulcs használata "ENV.

### <a name="reuse-recipe-store-billing-settings-with-container"></a>Újból felhasználhatja a recept: a tároló számlázási beállítások tárolásához

Ez a példa bemutatja, hogyan hozhat létre a Text Analytics sentiment tárolót egy Docker-fájlból.

```Dockerfile
FROM mcr.microsoft.com/azure-cognitive-services/sentiment:latest
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
```

Hozza létre és futtassa a tároló [helyileg](#how-to-use-container-on-your-local-host) vagy a [privát regisztrációs adatbázis tároló](#how-to-add-container-to-private-registry) igény szerint.

### <a name="reuse-recipe-store-billing-and-mount-settings-with-container"></a>Újból felhasználhatja a recept: számlázási tárolja, és a csatlakoztatási beállítások tárolóval

Ez a példa bemutatja, hogyan használható a Language Understanding, számlázás és a modellek a docker-fájl mentésekor.

* A fájl a Language Understanding (LUIS) modellben a szolgáltató beágyazható fájlt másolja rendszer használatával `COPY`.
* A LUIS-tárolót egy modellt támogatja. Ha minden modell ugyanabban a mappában találhatók, az összes van szüksége egy `COPY` utasítást.
* Futtassa a docker-fájlt a modell bemeneti könyvtár relatív szülője. Futtassa a következő példában a `docker build` és `docker run` relatív szülőjének parancsokat `/input`. Az első `/input` a a `COPY` parancs a gazdaszámítógép könyvtár. A második `/input` a tároló könyvtár.

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
COPY /input /input
```

Hozza létre és futtassa a tároló [helyileg](#how-to-use-container-on-your-local-host) vagy a [privát regisztrációs adatbázis tároló](#how-to-add-container-to-private-registry) igény szerint.

## <a name="how-to-use-container-on-your-local-host"></a>A helyi gazdagépen tároló használata

Hozhat létre a Docker-fájlt, cserélje le a `<your-image-name>` a rendszerkép új nevére, majd használja:

```console
docker build -t <your-image-name> .
```

A lemezkép futtatásához, és távolítsa el, ha a tároló leáll (`--rm`):

```console
docker run --rm <your-image-name>
```

## <a name="how-to-add-container-to-private-registry"></a>Tároló privát regisztrációs adatbázis hozzáadása

Kövesse az alábbi lépéseket, használhatja a docker-fájlban, és helyezze az új rendszerképet a privát tárolóregisztrációs adatbázis.  

1. Hozzon létre egy `Dockerfile` újbóli recept szöveggel. A `Dockerfile` bővítmény nem rendelkezik.

1. Cserélje le a csúcsos zárójeleket értékeket a saját értékeire.

1. A fájl beépítheti egy rendszerképet a parancssor vagy a terminálon, a következő paranccsal. Cserélje le a csúcsos zárójeleket értékei `<>`, a saját tároló nevét és címkéjét.  

    A címke lehetőségnél `-t`, mi módosította a tároló kapcsolatos információval módja. Például a tároló nevére `modified-LUIS` azt jelzi, hogy az eredeti tároló szintekre épülő már megtörtént. A címke neve `with-billing-and-model` hogyan a Language Understanding (LUIS) tároló módosult.

    ```Bash
    docker build -t <your-new-container-name>:<your-new-tag-name> .
    ```

1. Jelentkezzen be az Azure CLI-konzolról. Ez a parancs egy böngészőablakban megnyitja, és megköveteli a hitelesítést. A hitelesítést követően zárja be a böngészőt, és folytatni a munkát a konzolon.

    ```azure-cli
    az login
    ```

1. Jelentkezzen be az Azure CLI-vel a privát tárolójegyzék konzolról.

    Cserélje le a csúcsos zárójeleket értékei `<my-registry>`, a saját beállításjegyzék nevére.  

    ```azure-cli
    az acr login --name <my-registry>
    ```

    Docker-bejelentkezési is jelentkezik, ha hozzá van rendelve egy egyszerű szolgáltatást.

    ```Bash
    docker login <my-registry>.azurecr.io
    ```

1. A címke a tárolóhoz, az a privát beállításjegyzék-helyhez. Cserélje le a csúcsos zárójeleket értékei `<my-registry>`, a saját beállításjegyzék nevére. 

    ```Bash
    docker tag <your-new-container-name>:<your-new-tag-name> <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

    Ha nem használja a címke nevét `latest` implicit.

1. Az új rendszerkép leküldése a privát tárolóregisztrációs adatbázisba. Megtekintheti a privát tárolóregisztrációs adatbázisba, a következő CLI-parancsot a tároló név lesz a tárház nevét.

    ```Bash
    docker push <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Létrehozása és használata az Azure-Tárolópéldányon](azure-container-instance-recipe.md)

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