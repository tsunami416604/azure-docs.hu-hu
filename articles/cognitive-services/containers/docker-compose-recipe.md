---
title: A Docker Compose használata több tároló üzembe helyezéséhez
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan telepíthet több Cognitive Services-tárolót. Ez a cikk bemutatja, hogyan vezényelheti több Docker-tároló rendszerképek segítségével Docker Compose.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 458cda927a6a123fcd9962efc6ab705e13f43286
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878782"
---
# <a name="use-docker-compose-to-deploy-multiple-containers"></a>A Docker Compose használata több tároló üzembe helyezéséhez

Ez a cikk bemutatja, hogyan telepíthet több Azure Cognitive Services-tárolót. Pontosabban megtudhatja, hogyan használhatja a Docker-összeállítást több Docker-tárolórendszerkép-rendszervezéshez.

> [A Docker Compose](https://docs.docker.com/compose/) egy többtárolós Docker-alkalmazások definiálására és futtatására szolgáló eszköz. A Compose alkalmazásban yaml-fájlt használ az alkalmazás szolgáltatásainak konfigurálásához. Ezután egyetlen parancs futtatásával hozza létre és indítja el az összes szolgáltatást a konfigurációból.

Hasznos lehet több tárolórendszerkép egyetlen gazdaszámítógépen való vezénylése. Ebben a cikkben az Olvasás i és űrlapfelismerő tárolókat fogjuk összeszedni.

## <a name="prerequisites"></a>Előfeltételek

Ez az eljárás több olyan eszközt igényel, amelyeket helyileg kell telepíteni és futtatni:

* Azure-előfizetés. Ha még nem rendelkezik ilyen, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.
* [Docker motor](https://www.docker.com/products/docker-engine). Ellenőrizze, hogy a Docker CLI működik-e egy konzolablakban.
* Egy Azure-erőforrás a megfelelő tarifacsomaggal. Ezzel a tárolóval csak a következő tarifacsomagok működnek:
  * **Computer Vision** erőforrás csak F0 vagy Standard tarifacsomaggal.
  * **Form Recognizer** erőforrás csak F0 vagy Standard tarifacsomaggal.
  * **Cognitive Services** erőforrás az S0 tarifacsomaggal.

## <a name="request-access-to-the-container-registry"></a>Hozzáférés kérése a tároló beállításjegyzékéhez

Töltse ki és küldje el a [Cognitive Services beszédtárolók kérelem űrlap.](https://aka.ms/speechcontainerspreview/) 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="docker-compose-file"></a>Docker-írásfájl

A YAML-fájl határozza meg az összes telepítendő szolgáltatást. Ezek a szolgáltatások `DockerFile` egy vagy meglévő tárolórendszerképre támaszkodnak. Ebben az esetben két előnézeti képet fogunk használni. Másolja és illessze be a következő YAML-fájlt, és mentse *docker-compose.yaml*néven. Adja meg a megfelelő **apikey**, **számlázási**és **EndpointUri** értékeket a fájlban.

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
         source: E:\publicpreview\output
         target: /output
       - type: bind
         source: E:\publicpreview\input
         target: /input
    ports:
      - "5010:5000"

  ocr:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-read"
    environment:
      eula: accept
      apikey: # < Your computer vision API key >
      billing: # < Your computer vision billing URL >
    ports:
      - "5021:5000"
```

> [!IMPORTANT]
> Hozza létre a kötetcsomópontban megadott könyvtárakat a **gazdagépen.** Erre a megközelítésre azért van szükség, mert a könyvtáraknak létezniük kell, mielőtt kötetkötések használatával próbálna lemezképet csatlakoztatni.

## <a name="start-the-configured-docker-compose-services"></a>A konfigurált Docker-compose szolgáltatások indítása

A Docker Compose fájl lehetővé teszi a szolgáltatás életciklusának összes szakaszának kezelését: szolgáltatások indítása, leállítása és újjáépítése; a szolgáltatás állapotának megtekintése; és naplózza a streamelést. Nyisson meg egy parancssori felületet a projekt könyvtárából (ahol a docker-compose.yaml fájl található).

> [!NOTE]
> A hibák elkerülése érdekében győződjön meg arról, hogy a gazdagép megfelelően osztja meg a meghajtókat a Docker Engine-el. Ha például *az E:\publicpreview* könyvtárként van használatos a *docker-compose.yaml* fájlban, ossza meg **az E** meghajtót a Docker-rel.

A parancssori felületről hajtsa végre a következő parancsot a *docker-compose.yaml* fájlban definiált összes szolgáltatás elindításához (vagy újraindításához):

```console
docker-compose up
```

Az első alkalommal, amikor a Docker végrehajtja a **docker-compose up** parancsot ezzel a konfigurációval, lekéri a **szolgáltatáscsomópont** alatt konfigurált lemezképeket, majd letölti és csatlakoztatja őket:

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
Pulling ocr (containerpreview.azurecr.io/microsoft/cognitive-services-read:)...
latest: Pulling from microsoft/cognitive-services-read
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

A képek letöltése után a rendszer elindul a képszolgáltatások:

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

## <a name="verify-the-service-availability"></a>A szolgáltatás elérhetőségének ellenőrzése

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

Íme néhány példa kimenet:

```
IMAGE ID            REPOSITORY                                                                 TAG
2ce533f88e80        containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer   latest
4be104c126c5        containerpreview.azurecr.io/microsoft/cognitive-services-read              latest
```

### <a name="test-containers"></a>Vizsgálati tartályok

Nyisson meg egy böngészőt a gazdagépen, és lépjen a **localhost** webhelyre a *docker-compose.yaml* fájlban megadott port használatával, például http://localhost:5021/swagger/index.html. Például **használhatja** a Try It funkciót az API-ban az űrlapfelismerő végpont teszteléséhez. Mindkét tároló swagger oldalak elérhetőnek és tesztelhetőnek kell lennie.

![Űrlapfelismerő tároló](media/form-recognizer-swagger-page.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Cognitive Services-tárolók](../cognitive-services-container-support.md)
