---
title: A docker compose tároló receptek
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan helyezhet üzembe több Cognitive Services-tároló. Ez az eljárás bemutatja, miként kell levezényelni a több Docker-tárolók lemezképeit a Docker Compose.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 8afb7e866bc2a5fefe28a71653c4a2a87fdc7a5b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445798"
---
# <a name="use-multiple-containers-in-a-private-network-with-docker-compose"></a>A Docker Compose egy magánhálózaton lévő több tároló használata

Ismerje meg, hogyan helyezhet üzembe több Cognitive Services-tároló. Ez az eljárás bemutatja, miként kell levezényelni a több Docker-tárolók lemezképeit a Docker Compose.

> [A docker Compose](https://docs.docker.com/compose/) meghatározása és a többtárolós Docker alkalmazásokat futtató eszköz. Az összeállítás egy YAML-fájlt az alkalmazás szolgáltatások konfigurálásához használja. Ezután, egyetlen paranccsal hozzon létre, és indítsa el az összes szolgáltatást a konfigurációból.

Ha szükséges, meggyőző replikálásával segít a vállalatnak egy egyetlen számítógépen több tárolórendszerképek lehet. Ebben a cikkben azt fogjuk lekérheti a szöveg felismerése és űrlap felismerő szolgáltatás együtt.

## <a name="prerequisites"></a>Előfeltételek

Ez az eljárás több eszközt, hogy telepítve legyen, és helyi futtatása szükséges.

* Használja az Azure-előfizetéssel. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* [Docker-motor](https://www.docker.com/products/docker-engine) , és ellenőrizze, hogy működik-e a Docker parancssori felületén a konzolablakban.
* Egy Azure-erőforrás a megfelelő tarifacsomagot. Nem minden tarifacsomag használata ebben a tárolóban:
  * **Számítógépes Látástechnológia** erőforrás F0 vagy Standard díjszabás csak szint esetében.
  * **Űrlap-felismerő** erőforrás F0 vagy Standard díjszabás csak szint esetében.
  * **A cognitive Services** tarifacsomag az S0 erőforrás.

## <a name="request-access-to-the-container-registry"></a>A tároló-beállításjegyzék hozzáférés kérése

És küldje el a [Cognitive Services beszéd tárolók űrlapot](https://aka.ms/speechcontainerspreview/) hozzáférés kéréséhez a tárolóhoz. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="docker-compose-file"></a>A docker compose-fájl

A YAML-fájl üzembe helyezni a szolgáltatások határozza meg. Ezek a szolgáltatások támaszkodnak, vagy egy `DockerFile` egy meglévő tárolórendszerképet, és ebben az esetben fogjuk használni, vagy két előzetes verziójú lemezképeket. Másolja és illessze be a következő YAML-fájlt, és mentse *docker-compose.yaml*. Adja meg a megfelelő _apikey tulajdonsággal végzett tesztelése_, _számlázási_, és _végpont URI_ lévő értékeknek a _docker-compose.yml_ az alábbi fájl.

```yaml
version: '3.7'
services:
  forms:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer"
    environment:
       eula: accept
       billing: # < Your form recognizer billing URL >
       apikey: # < Your form recognizer API key >
       FormRecognizer__ComputerVisionApiKey: # < Your form recognizer API key >
       FormRecognizer__ComputerVisionEndpointUri: # < Your form recognizer URI >
    volumes:
       - type: bind
         source: e:\publicpreview\output
         target: /output
       - type: bind
         source: e:\publicpreview\input
         target: /input
    ports:
      - "5010:5000"

  ocr:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text"
    environment:
      eula: accept
      apikey: # < Your recognize text API key >
      billing: # < Your recognize text billing URL >
    ports:
      - "5021:5000"
```

> [!IMPORTANT]
> A könyvtárak létrehozása a gazdagépen, amely alapján vannak megadva a `volumes` csomópont. Ez azért szükséges, ahogy a könyvtárak a lemezkép csatlakoztatása a kötet kötések megkísérlése előtt léteznie kell.

## <a name="start-the-configured-docker-compose-services"></a>Kezdő a konfigurált docker-compose szolgáltatások

Egy a docker compose fájl lehetővé teszi, hogy a meghatározott szolgáltatási összes-életciklusait; felügyeletét indítása/leállítása és a szolgáltatások újraépítését, a szolgáltatás állapota, és a naplózási adatfolyam megtekintése. A projekt könyvtárában nyissa meg a parancssori felület (ahol a *docker-compose.yaml* megszabhatja fájlt).

> [!NOTE]
> Hibák elkerülése érdekében, hogy a gazdagép frissítéséből megfelelően megosztása rendelkező meghajtókat a **Docker-motor**. Például ha *e:\publicpreview* szolgál egy-egy könyvtárat a *docker-compose.yaml* megosztani a *E meghajtó* a docker használatával.

A parancssori felületről, hajtsa végre a következő parancsot a definiált szolgáltatások indítása (vagy újraindítása) a *docker-compose.yaml*:

```console
docker-compose up
```

Az első végrehajtás ideje a `docker-compose up` parancsot ezzel a konfigurációval **Docker** alatt vannak konfigurálva a lemezképeket fogja lekérni a `services` csomópont--letöltése vagy csatlakoztatási őket:

```console
Pulling forms (containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:)...
latest: Pulling from microsoft/cognitive-services-form-recognizer
743f2d6c1f65: Pull complete
72befba99561: Pull complete
2a40b9192d02: Pull complete
c7715c9d5c33: Pull complete
f0b33959f1c4: Pull complete
b8ab86c6ab26: Pull complete
41940c21ed3c: Pull complete
e3d37dd258d4: Pull complete
cdb5eb761109: Pull complete
fd93b5f95865: Pull complete
ef41dcbc5857: Pull complete
4d05c86a4178: Pull complete
34e811d37201: Pull complete
Pulling ocr (containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:)...
latest: Pulling from microsoft/cognitive-services-recognize-text
f476d66f5408: Already exists
8882c27f669e: Already exists
d9af21273955: Already exists
f5029279ec12: Already exists
1a578849dcd1: Pull complete
45064b1ab0bf: Download complete
4bb846705268: Downloading [=========================================>         ]  187.1MB/222.8MB
c56511552241: Waiting
e91d2aa0f1ad: Downloading [==============================================>    ]  162.2MB/176.1MB
```

A rendszerképek lesznek letöltve, majd a lemezkép-szolgáltatás elindult.

```console
Starting docker_ocr_1   ... done
Starting docker_forms_1 ... doneAttaching to docker_ocr_1, docker_forms_1forms_1  | forms_1  | forms_1  | Notice: This Preview is made available to you on the condition that you agree to the Supplemental Terms of Use for Microsoft Azure Previews [https://go.microsoft.com/fwlink/?linkid=2018815], which supplement your agreement [https://go.microsoft.com/fwlink/?linkid=2018657] governing your use of Azure. If you do not have an existing agreement governing your use of Azure, you agree that your agreement governing use of Azure is the Microsoft Online Subscription Agreement [https://go.microsoft.com/fwlink/?linkid=2018755] (which incorporates the Online Services Terms [https://go.microsoft.com/fwlink/?linkid=2018760]). By using the Preview you agree to these terms.
forms_1  | 
forms_1  | 
forms_1  | Using '/input' for reading models and other read-only data.
forms_1  | Using '/output/forms/812d811d1bcc' for writing logs and other output data.
forms_1  | Logging to console.
forms_1  | Submitting metering to 'https://westus2.api.cognitive.microsoft.com/'.
forms_1  | WARNING: No access control enabled!
forms_1  | warn: Microsoft.AspNetCore.Server.Kestrel[0]
forms_1  |       Overriding address(es) 'http://+:80'. Binding to endpoints defined in UseKestrel() instead.
forms_1  | Hosting environment: Production
forms_1  | Content root path: /app/forms
forms_1  | Now listening on: http://0.0.0.0:5000
forms_1  | Application started. Press Ctrl+C to shut down.
ocr_1    | 
ocr_1    | 
ocr_1    | Notice: This Preview is made available to you on the condition that you agree to the Supplemental Terms of Use for Microsoft Azure Previews [https://go.microsoft.com/fwlink/?linkid=2018815], which supplement your agreement [https://go.microsoft.com/fwlink/?linkid=2018657] governing your use of Azure. If you do not have an existing agreement governing your use of Azure, you agree that your agreement governing use of Azure is the Microsoft Online Subscription Agreement [https://go.microsoft.com/fwlink/?linkid=2018755] (which incorporates the Online Services Terms [https://go.microsoft.com/fwlink/?linkid=2018760]). By using the Preview you agree to these terms.
ocr_1    |
ocr_1    | 
ocr_1    | Logging to console.
ocr_1    | Submitting metering to 'https://westcentralus.api.cognitive.microsoft.com/'.
ocr_1    | WARNING: No access control enabled!
ocr_1    | Hosting environment: Production
ocr_1    | Content root path: /
ocr_1    | Now listening on: http://0.0.0.0:5000
ocr_1    | Application started. Press Ctrl+C to shut down.
```

## <a name="verify-the-service-availability"></a>A szolgáltatás rendelkezésre állásának ellenőrzése

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

Alább a következő egy példa a kimenetre:

```
IMAGE ID            REPOSITORY                                                                 TAG
2ce533f88e80        containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer   latest
4be104c126c5        containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text    latest
```

### <a name="test-the-recognize-text-container"></a>A felismerés szöveg tároló tesztelése

Nyisson meg egy böngészőt a gazdagépen, és navigáljon a `localhost` a megadott porton érkező a *docker-compose.yaml*, például `http://localhost:5021/swagger/index.html`. Használhatja a szolgáltatást, próbálja meg az API tesztelése a felismerése szöveg végpontot.

![Szöveg Swagger felismerése](media/recognize-text-swagger-page.png)

### <a name="test-the-form-recognizer-container"></a>A képernyő felismerő tároló tesztelése

Nyisson meg egy böngészőt a gazdagépen, és navigáljon a `localhost` a megadott porton érkező a *docker-compose.yaml*, például `http://localhost:5010/swagger/index.html`. Használhatja a szolgáltatást, próbálja meg az API tesztelése az űrlap felismerő végpont.

![Űrlap felismerő Swagger](media/form-recognizer-swagger-page.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A cognitive Services-tárolók](../cognitive-services-container-support.md)