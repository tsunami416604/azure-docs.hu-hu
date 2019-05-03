---
title: Telepítse és futtassa a tároló - űrlap felismerő
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan űrlap felismerő a tárolót használja az űrlap és a táblák adatait elemezni.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: pafarley
ms.openlocfilehash: 5d4374b329049e2e55966a28567c5232be77abda
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027067"
---
# <a name="install-and-run-form-recognizer-containers"></a>Telepítse és futtassa az űrlap felismerő tárolók
Űrlap felismerő gépi tanulási technológia azonosíthatja és kulcs-érték párok és táblák kinyerése űrlapok vonatkozik. Ez értékeket és hozzájuk táblabejegyzéseket társítja, és majd megjeleníti a strukturált adatok, amely tartalmazza a kapcsolatokat az eredeti fájl. Az egyéni űrlap felismerő modell annak érdekében, hogy csökkenthető, és könnyedén integrálhatja a munkafolyamat automation vagy más alkalmazásban egy egyszerű REST API használatával hívható meg. Csak öt dokumentumok (vagy egy üres képernyő) szükséges, így gyorsan, pontos eredményeket kaphat és fenntarthatja az adott tartalomra, nehéz kézi beavatkozás vagy kiterjedt data science szakértelem nélkül. Adatok címkézési vagy adatok jegyzet nem igényel.

|Függvény|Szolgáltatások|
|-|-|
|Form Recognizer| <li>Folyamatok fájlokat, írja be a PDF-, PNG és JPG.<li>Egyéni modellek ugyanez az elrendezés legalább 5 űrlapokkal betanítja. <li>Kinyeri a kulcs-érték párok és a tábla adatait. <li>Használja a Cognitive Service számítógép Vision API RecognizeText és nyomtatott szöveg kinyerése űrlapok rendszerképek.<li>Jegyzet vagy címkézés nem igényel.|

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Űrlap felismerő tárolók használata előtt a következő előfeltételeknek kell megfelelnie:

|Szükséges|Cél|
|--|--|
|Docker-motor| A Docker-motor telepítve van szüksége egy [gazdaszámítógép](#the-host-computer). A docker csomagokat biztosít, a Docker-környezet konfigurálása a [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/), és [Linux](https://docs.docker.com/engine/installation/#supported-platforms). A Docker és a tárolók alapfogalmainak ismertetését lásd: a [a Docker áttekintése](https://docs.docker.com/engine/docker-overview/).<br><br> Docker kell konfigurálni, hogy a tárolók számlázási adatok küldése az Azure-ba történő csatlakozáshoz. <br><br> **A Windows**, a Docker Linux-tárolók támogatása is kell konfigurálni.<br><br>|
|Docker-ismeretek | A Docker fő fogalmaira, például a beállításjegyzékek, adattárak, tárolók, és tárolórendszerképeket, valamint alapszintű ismerete alapvető ismeretekkel kell `docker` parancsokat.|
|Azure CLI| Telepítenie kell a [Azure CLI-vel](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) a gazdagépen.|
|Számítógépes Látástechnológiai API-erőforrás| Annak érdekében, hogy feldolgozni a beolvasott dokumentumokat és képeket, egy **számítógépes Látástechnológiai erőforrás** megadása kötelező. Elérheti a **szöveg felismerése** funkció vagy Azure-erőforrásként (REST API vagy SDK-t), vagy pedig egy `cognitive-services-recognize-text` tároló. A szokásos számlázási díjak érvényesek. <br><br>Át kell adnia a kulcs és a számlázási végpont az adott Computer Vision-erőforrás (Azure-felhőben vagy a Cognitive Services-tároló). Használja ezt a kulcsot, és a számlázási végpont {COMPUTER_VISION_API_KEY} és {COMPUTER_VISION_BILLING_ENDPOINT_URI}.<br><br> Ha használja a  **`cognitive-services-recognize-text` tároló**, győződjön meg arról, hogy:<br><br>* A Computer Vision kulcsa az űrlap felismerő tároló a Computer Vision megadott kulcs `docker run` parancsot a `cognitive-services-recognize-text` tároló.<br>* A számlázási végpont el a tároló végpontja, például `https://localhost:5000`. A Computer Vision és a képernyő felismerő tárolók használata együtt, ugyanarra a gazdagépre, ha azok nem is indítható el az alapértelmezett porton, `5000`.  |  
|Űrlap felismerő erőforrás |Ezek a tárolók használatához rendelkeznie kell:<br><br>A _űrlap felismerő_ Azure-erőforráshoz tartozó számlázási kulcs és számlázási végpont URI azonosítója. Mindkét értéket érhetők el az Azure Portalon **űrlap felismerő** áttekintése és a kulcsok lapok és a szükséges a tárolót.<br><br>**{BILLING_KEY}** : erőforrás-kulcs<br><br>**{BILLING_ENDPOINT_URI}** : végpont URI-példa: `https://westus.api.cognitive.microsoft.com/forms/v1.0`| 

## <a name="request-access-to-the-container-registry"></a>A tároló-beállításjegyzék hozzáférés kérése

Először végezze el, és küldje el a [Cognitive Services űrlap felismerő tárolók kérésűrlapra](https://aka.ms/FormRecognizerRequestAccess) hozzáférés kéréséhez a tárolóhoz. Ez lesz is feliratkozását számítógépes Látástechnológiai. Nem kell külön Regisztráljon a Computer Vision űrlapot. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>A számítógép

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Tároló-követelményeket és javaslatokat

A következő táblázat ismerteti a minimális és ajánlott processzormagot és memóriát lefoglalni az egyes űrlap felismerő tárolók.

| Tároló | Minimális | Ajánlott |
|-----------|---------|-------------|
|cognitive-services-form-recognizer | 2 mag, 4 GB memória | 4 mag, 8 GB memória |

* Egyes maghoz kell lennie legalább 2.6-os gigahertz (GHz) vagy gyorsabb.
* TPS - tranzakció / másodperc

Core és a memória felel meg a `--cpus` és `--memory` beállítások, amelyek részeként használhatók a `docker run` parancsot.

> [!Note]
> A minimális és ajánlott értékek minden korlátokat Docker alapulnak, és *nem* a fogadó számítógép-erőforrásokat.

## <a name="get-the-container-image-with-docker-pull"></a>A tárolórendszerkép beolvasása `docker pull`

Űrlap felismerő tárolórendszerképeket érhetők el.

| Tároló | Adattár |
|-----------|------------|
| cognitive-services-form-recognizer | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]


### <a name="docker-pull-for-the-form-recognizer-container"></a>A képernyő felismerő tároló docker pull

#### <a name="form-recognizer"></a>Form Recognizer

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest
```

## <a name="how-to-use-the-container"></a>A tároló használata

Ha a tároló a [gazdaszámítógép](#the-host-computer), a következő eljárás használható a tárolóval.

1. [A tároló futtatásához](#run-the-container-with-docker-run), a szükséges, de nem használt számlázási beállításokkal. További [példák](form-recognizer-container-configuration.md#example-docker-run-commands) , a `docker run` parancs érhetők el.
1. [A tároló előrejelzési végpontja lekérdezése](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>A tároló futtatásához `docker run`

Használja a [futtatása docker](https://docs.docker.com/engine/reference/commandline/run/) parancs futtatása bármely három tárolóra. A parancs paraméterei a következők:

| Helyőrző | Érték |
|-------------|-------|
|{BILLING_KEY} | Ezt a kulcsot szolgál a tárolót, és az Azure Portalon űrlap felismerő kulcsok lapján található.  |
|{BILLING_ENDPOINT_URI} | A számlázási végpont URI azonosítóját az Azure Portalon az űrlap felismerő áttekintése oldalon érhető el.|
|{COMPUTER_VISION_API_KEY}| A kulcsot az Azure Portalon számítógép Vision API-kulcsok oldalon érhető el.|
|{COMPUTER_VISION_ENDPOINT_URI}|A számlázási végpont. Felhőalapú számítógépes Látástechnológiai erőforrás használja, ha az URI értéket érhető el az Azure Portalon számítógép Vision API – Áttekintés oldalon. Ha használ olyan `cognitive-services-recognize-text` tároló, használja a számlázási végpont URL-címe a tárolóhoz, az átadott a `docker run` parancsot.|

Cserélje le ezeket a paramétereket a következő példában a saját értékeire `docker run` parancsot.

### <a name="form-recognizer"></a>Form Recognizer

```bash
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

Ezzel a paranccsal:

* A tároló rendszerképét űrlap felismerő tárolóban fut
* Foglalja le a 2 processzormagot és memóriát 8 gigabájt (GB)
* Elérhetővé teszi az 5000-es TCP-porton és a egy pszeudo-TTY lefoglalja a tároló
* Után kilép, automatikusan eltávolítja a tárolót. A tároló rendszerképét az továbbra is elérhető az állomáson.
* Egy /input és a egy/output kötet a tárolóhoz csatlakoztatja

[!INCLUDE [Running multiple containers on the same host H2](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

### <a name="run-separate-containers-as-separate-docker-run-commands"></a>Külön tárolók futtató parancsok futtatása a önálló docker

Az űrlap felismerő és szöveges felismerő kombinációjával, ugyanazon a gazdagépen helyileg üzemeltetett kövesse a két példa Docker CLI-parancsokat.

Futtassa az első tároló 5000-es porton. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

Futtassa a második tárolót 5001 porton.


```bash 
docker run --rm -it -p 5001:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
Eula=accept \
Billing={COMPUTER_VISION_ENDPOINT_URI} \
ApiKey={COMPUTER_VISION_API_KEY}
```
Minden ezt követő tároló lehet egy másik porton. 

### <a name="run-separate-containers-with-docker-compose"></a>Különálló-tárolókat futtathat Docker Compose

Az űrlap felismerő és szöveges felismerő kombinációjával, ugyanazon a gazdagépen helyileg üzemeltetett a következő egy példa a Docker Compose YAML-fájlt. A szöveg felismerő `{COMPUTER_VISION_API_KEY}` azonosnak kell lennie mind a `formrecognizer` és `ocr` tárolók. A `{COMPUTER_VISION_ENDPOINT_URI}` csak használja a `ocr` tároló mert a `formrecognizer` tárolót használ a `ocr` nevét és portját. 

```docker
version: '3.3'
services:   
  ocr:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text"
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 8g
        reservations:
          cpus: '1'
          memory: 4g
    environment:
      eula: accept
      billing: "{COMPUTER_VISION_ENDPOINT_URI}"
      apikey: {COMPUTER_VISION_API_KEY}  

  formrecognizer:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer"
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 8g
        reservations:
          cpus: '1'
          memory: 4g
    environment:
      eula: accept
      billing: "{BILLING_ENDPOINT_URI}"
      apikey: {BILLING_KEY}
      FormRecognizer__ComputerVisionApiKey: {COMPUTER_VISION_API_KEY}
      FormRecognizer__ComputerVisionEndpointUri: "http://ocr:5000"
      FormRecognizer__SyncProcessTaskCancelLimitInSecs: 75
    links:
      - ocr
    volumes:
      - type: bind
        source: c:\output
        target: /output
      - type: bind
        source: c:\input
        target: /input
    ports:
      - "5000:5000"  
```


> [!IMPORTANT]
> A `Eula`, `Billing`, és `ApiKey` , valamint `FormRecognizer:ComputerVisionApiKey` és `FormRecognizer:ComputerVisionEndpointUri` beállítások meg kell adni a tároló futtatásához; ellenkező esetben a tároló nem indul el.  További információkért lásd: [számlázási](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>A tároló előrejelzési végpontja lekérdezése

|Tároló|Végpont|
|--|--|
|form-recognizer|http://localhost:5000


### <a name="form-recognizer"></a>Form Recognizer

A tároló websocket-alapú lekérdezési végpontot API-k, keresztül elért biztosít [űrlap felismerő szolgáltatások SDK-dokumentáció](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/).

Alapértelmezés szerint az űrlap felismerő SDK-t használja az online szolgáltatások. A tároló használatához módosítania az inicializálási metódust. Az alábbi példák.

#### <a name="for-c"></a>AC#

Az Azure-felhő inicializálási hívás használatával módosítani:

```C#
var config = FormRecognizerConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

a tároló végpontja használatával metódust:

```C#
var config = FormRecognizerConfig.FromEndpoint("ws://localhost:5000/formrecognizer/v1.0-preview/custom", "YourSubscriptionKey");
```

#### <a name="for-python"></a>A Pythonhoz

Az Azure-felhő inicializálási hívás használatáról

```python
formrecognizer_config = formrecognizersdk.FormRecognizerConfig(subscription=formrecognizer_key, region=service_region)
```

a tároló végpontja használatával metódust:

```python
formrecognizer_config = formrecognizersdk.FormRecognizerConfig(subscription=formrecognizer_key, endpoint="ws://localhost:5000/formrecognizer/v1.0-preview/custom"
```

### <a name="form-recognizer"></a>Form Recognizer

A tároló biztosít API-k, amelyek található REST-végpont [Itt](https://docs.microsoft.com/azure/cognitive-services/formrecognizer-service/rest-apis#formrecognier-api) és -példákat [Itt](https://azure.microsoft.com/resources/samples/cognitive-formrecognizer).


[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>Állítsa le a tároló

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Hibaelhárítás

A tároló futtatásakor használ-e a tároló **stdout** és **stderr** a kimeneti információt, amely segítségére lehet a hibaelhárítás indítása, vagy a tároló futtatása közben történik.

## <a name="billing"></a>Számlázás

Űrlap felismerő tárolók Küldés a számlázási adatokat az Azure-ba, a használatával egy _űrlap felismerő_ erőforrást az Azure-fiókjával.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Ezek a beállítások kapcsolatos további információkért lásd: [tárolók konfigurálása](form-recognizer-container-configuration.md).

## <a name="summary"></a>Összegzés

Ebben a cikkben megtanulta, fogalmak és letöltése, telepítése és a futó tárolók űrlap felismerő munkafolyamat. Összegezve:

* Űrlap felismerő Docker egy Linux-tárolót biztosít.
* Tárolórendszerképek letöltődnek az Azure-ban a privát tárolóregisztrációs adatbázisból.
* Tárolórendszerképek futtatása a Docker.
* Használhatja a REST API vagy SDK-val űrlap felismerő tárolóban műveletek hívására adja meg a gazdagép a tároló URI-t.
* Számlázási adatokat adjon meg egy tároló hárítható el.

> [!IMPORTANT]
>  Cognitive Services-tárolók nem teszi lehetővé az Azure-méréshez való csatlakozás nélkül. Az ügyfeleknek kell ahhoz, hogy a tárolókkal való kommunikációhoz mindig a mérési szolgáltatással számlázási adatokat. Cognitive Services-tárolók nem (például a lemezkép vagy az elemezni kívánt szöveget) a vásárlói adatokat küldeni a Microsoftnak.

## <a name="next-steps"></a>További lépések

* Felülvizsgálat [tárolók konfigurálása](form-recognizer-container-configuration.md) a konfigurációs beállítások
* Több [Cognitive Services-tárolók](../cognitive-services-container-support.md)
