---
title: Telepítse és futtassa a tároló - űrlap felismerő
titleSuffix: Azure Cognitive Services
description: Megismerheti, hogyan használhatja a Form Recognizer-tárolót az űrlap- és táblaadatok elemzéséhez.
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: f38752928832b7dee6a7e55f1d25374a64391bbe
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67441886"
---
# <a name="install-and-run-form-recognizer-containers"></a>Telepítse és futtassa az űrlap felismerő tárolók
Az Azure űrlap felismerő gépi tanulási technológia azonosíthatja és kulcs-érték párok és táblák kinyerése űrlapok vonatkozik. Értékek és táblabejegyzéseket társítja a kulcs-érték párok, és azt majd megjeleníti a strukturált adatok, amely tartalmazza a kapcsolatokat az eredeti fájlt. 

Csökkentheti az összetettséget, és könnyedén integrálhatja egy egyéni űrlap felismerő modell a munkafolyamat automation vagy más alkalmazás, meghívhatja a modell egy egyszerű REST API-val. Szükség esetén, így eredmények gyorsan, pontosan, és az adott tartalom szabott csak öt űrlap dokumentumok (vagy egy üres képernyő és két ki vannak töltve formában). Nem nehéz kézi beavatkozás vagy kiterjedt data science szakértelmet nem szükséges. És nincs szükség adatok címkézési vagy adatok jegyzet.

|Függvény|Szolgáltatások|
|-|-|
|Form Recognizer| <li>PDF-, PNG és JPG-fájlok feldolgozása<li>Ugyanez az elrendezés 5 formáját legalább a szerelvények egyéni modellek <li>Kinyeri a kulcs-érték párok és a tábla adatai <li>Az Azure Cognitive Services számítógép Vision API-t ismeri fel a szöveges funkciót használja, és űrlapok rendszerképek nyomtatott szöveg kinyerése<li>Jegyzet vagy címkézés nem igényel|

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt használná az űrlap felismerő tárolók, a következő előfeltételeknek kell megfelelnie:

|Szükséges|Cél|
|--|--|
|Docker-motor| A Docker-motor telepítve van szüksége egy [gazdaszámítógép](#the-host-computer). A docker csomagokat biztosít, a Docker-környezet konfigurálása a [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/), és [Linux](https://docs.docker.com/engine/installation/#supported-platforms). A Docker és a tárolók alapfogalmainak ismertetését lásd: a [a Docker áttekintése](https://docs.docker.com/engine/docker-overview/).<br><br> Docker kell konfigurálni, hogy a tárolók számlázási adatok küldése az Azure-ba történő csatlakozáshoz. <br><br> A Windows a Linux-tárolók támogatása is Docker kell konfigurálni.<br><br>|
|Docker-ismeretek | A Docker fő fogalmaira, például a beállításjegyzékek, adattárak, tárolók, és tárolórendszerképeket és alapszintű ismerete alapvető ismeretekkel kell `docker` parancsokat.|
|Az Azure CLI| Telepítse a [Azure CLI-vel](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) a gazdagépen.|
|Számítógépes Látástechnológiai API-erőforrás| Computer Vision erőforrás kell feldolgozni a beolvasott dokumentumokat és képeket. A szöveg felismerése funkció eléréséhez, vagy egy Azure-erőforrás (a REST API vagy SDK-t) vagy egy *cognitive services-felismerni-szöveges* [tároló](../Computer-vision/computer-vision-how-to-install-containers.md##get-the-container-image-with-docker-pull). A szokásos számlázási díjak érvényesek. <br><br>Adja át a kulcsot és a számlázási végpontok a Computer Vision erőforrás (Azure-felhőben vagy a Cognitive Services-tároló). Használja ezt a kulcsot, és a számlázási végpont {COMPUTER_VISION_API_KEY} és {COMPUTER_VISION_BILLING_ENDPOINT_URI}.<br><br> Ha használja a *cognitive services-felismerni-szöveges* tárolót, ügyeljen arra, hogy:<br><br>A Computer Vision kulcsa az űrlap felismerő tároló a Computer Vision megadott kulcs `docker run` parancsot a *cognitive services-felismerni-szöveges* tároló.<br>A számlázási végpont a tároló végpontja (például `https://localhost:5000`). Ha használ a Computer Vision tároló és a képernyő felismerő tároló együtt, ugyanarra a gazdagépre, azok nem is indítható el az alapértelmezett porton, *5000*.  |  
|Űrlap felismerő erőforrás |Ezek a tárolók használatához a következők kell rendelkeznie:<br><br>A _űrlap felismerő_ Azure-erőforráshoz tartozó számlázási kulcs és számlázási végpont URI azonosítója. Mindkét értéket érhetők el az Azure Portalon **felismerő áttekintés képernyő** és **űrlap felismerő áttekintése kulcsok** oldalak, és mindkét értéket igényel a tárolót.<br><br>**{BILLING_KEY}** : erőforrás-kulcs<br><br>**{BILLING_ENDPOINT_URI}** : végpont URI-példa `https://westus.api.cognitive.microsoft.com/forms/v1.0`| 

## <a name="request-access-to-the-container-registry"></a>A tároló-beállításjegyzék hozzáférés kérése

Először végezze el, és küldje el a [Cognitive Services űrlap felismerő tárolók eléréséhez űrlapot](https://aka.ms/FormRecognizerRequestAccess) hozzáférés kéréséhez a tárolóhoz. Így is bejelentkezik a Computer Vision regisztrálásához. Nem kell külön Regisztráljon a Computer Vision űrlapot. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>A számítógép

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Tároló-követelményeket és javaslatokat

A minimális és ajánlott processzormagot és memóriát lefoglalni az egyes űrlap felismerő tárolók a következő táblázat ismerteti:

| Tároló | Minimális | Ajánlott |
|-----------|---------|-------------|
|cognitive-services-form-recognizer | 2 mag, 4 GB memória | 4 mag, 8 GB memória |

* Egyes maghoz kell lennie legalább 2.6-os gigahertz (GHz) vagy gyorsabb.
* TPS - tranzakció / másodperc
* Core és a memória felel meg a `--cpus` és `--memory` beállítások, amelyek részeként használhatók a `docker run` parancsot.

> [!Note]
> A minimális és ajánlott értékek Docker korlátok alapulnak, és *nem* a fogadó számítógép-erőforrásokat.

## <a name="get-the-container-image-with-the-docker-pull-command"></a>A tároló rendszerképét a docker pull parancs beolvasása

Űrlap felismerő tárolórendszerképeket a következő tárházban érhetők el:

| Tároló | Tárház |
|-----------|------------|
| cognitive-services-form-recognizer | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest` |

Ha szeretne használni a `cognitive-services-recognize-text` [tároló](../Computer-vision/computer-vision-how-to-install-containers.md##get-the-container-image-with-docker-pull), az űrlap felismerő szolgáltatás helyett ellenőrizze, hogy használja a `docker pull` parancsot a helyes tároló neve: 

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-form-recognizer-container"></a>A képernyő felismerő tároló docker pull

#### <a name="form-recognizer"></a>Form Recognizer

A képernyő felismerő tároló lekéréséhez használja a következő parancsot:

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest
```

## <a name="how-to-use-the-container"></a>A tároló használata

Miután a tároló-e a a [gazdaszámítógép](#the-host-computer), a következő eljárás használható a tárolóval.

1. [A tároló futtatásához](#run-the-container-by-using-the-docker-run-command), a szükséges, de nem használt számlázási beállításokkal. További [példák](form-recognizer-container-configuration.md#example-docker-run-commands) , a `docker run` parancs érhetők el.
1. [A tároló előrejelzési végpontja lekérdezése](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-by-using-the-docker-run-command"></a>Futtassa a tároló parancs futtatása a docker használatával

Használja a [futtatása docker](https://docs.docker.com/engine/reference/commandline/run/) parancs futtatása bármely három tárolóra. A parancs paraméterei a következők:

| Helyőrző | Érték |
|-------------|-------|
|{BILLING_KEY} | Ez a kulcs segítségével a tárolót. Az Azure Portalon elérhető **űrlap felismerő kulcsok** lapot.  |
|{BILLING_ENDPOINT_URI} | A számlázási végpont URI érték érhető el az Azure Portalon **felismerő áttekintés képernyő** lapot.|
|{COMPUTER_VISION_API_KEY}| Az Azure Portalon érhető el a kulcs **számítógép Vision API-kulcsok** lapot.|
|{COMPUTER_VISION_ENDPOINT_URI}|A számlázási végpont. Ha egy felhőalapú számítógépes Látástechnológiai erőforrást használ, az URI értéket érhető el az Azure Portalon **számítógép Vision API – áttekintés** lapot. Ha használ egy `cognitive-services-recognize-text` tárolót, használja a számlázási végpont URL-címe a tárolóhoz, az átadott a `docker run` parancsot.|

Cserélje le ezeket a paramétereket a következő példában a saját értékeire `docker run` parancsot.

### <a name="form-recognizer"></a>Form Recognizer

```bash
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

Ezzel a paranccsal:

* Űrlap felismerő tárolóban futtatja a tároló rendszerképét.
* Foglalja le a 2 processzormagot és memóriát 8 gigabájt (GB).
* Elérhetővé teszi az 5000-es TCP-porton, és a egy pszeudo-TTY lefoglalja a tároló.
* Után kilép, automatikusan eltávolítja a tárolót. A tároló rendszerképét az továbbra is elérhető az állomáson.
* Egy /input és a egy/output kötet a tárolóhoz csatlakoztatja.

[!INCLUDE [Running multiple containers on the same host H2](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

### <a name="run-separate-containers-as-separate-docker-run-commands"></a>Külön tárolók futtató parancsok futtatása a önálló docker

Az űrlap felismerő és szöveges felismerő kombinációjával, ugyanazon a gazdagépen helyileg üzemeltetett használja a következő két példa Docker CLI-parancsokat:

Futtassa az első tároló 5000-es porton. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
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

A képernyő felismerő és a szöveg felismerő kombinációja ugyanazon a gazdagépen helyileg üzemeltetett tekintse meg a következő példában a Docker Compose YAML-fájlt. A szöveg felismerő `{COMPUTER_VISION_API_KEY}` azonosnak kell lennie mind a `formrecognizer` és `ocr` tárolók. A `{COMPUTER_VISION_ENDPOINT_URI}` kizárólag a szolgál a `ocr` tárolót, mert a `formrecognizer` tárolót használ a `ocr` nevét és portját. 

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
> A `Eula`, `Billing`, és `ApiKey`, valamint a `FormRecognizer:ComputerVisionApiKey` és `FormRecognizer:ComputerVisionEndpointUri` lehetőségeket, meg kell adni a tároló futtatásához; ellenkező esetben a tároló nem indul el. További információkért lásd: [számlázási](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>A tároló előrejelzési végpontja lekérdezése

|Tároló|Végpont|
|--|--|
|form-recognizer|http://localhost:5000


### <a name="form-recognizer"></a>Form Recognizer

A tároló websocket-alapú lekérdezési végpontot API-k, amelyek keresztül érhetők el biztosít [űrlap felismerő szolgáltatások SDK-dokumentáció](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/).

Alapértelmezés szerint az űrlap felismerő SDK-t használja az online szolgáltatások. A tároló használatához módosítania az inicializálási metódust. Az alábbi példák.

#### <a name="for-c"></a>AC#

Az Azure-felhő inicializálási hívás használatával módosítani:

```C#
var config = FormRecognizerConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

Ez a hívás, amely a tároló végpontja használja:

```C#
var config = FormRecognizerConfig.FromEndpoint("ws://localhost:5000/formrecognizer/v1.0-preview/custom", "YourSubscriptionKey");
```

#### <a name="for-python"></a>A Pythonhoz

Az Azure-felhő inicializálási hívás használatával módosítani:

```python
formrecognizer_config = formrecognizersdk.FormRecognizerConfig(subscription=formrecognizer_key, region=service_region)
```

Ez a hívás, amely a tároló végpontja használja:

```python
formrecognizer_config = formrecognizersdk.FormRecognizerConfig(subscription=formrecognizer_key, endpoint="ws://localhost:5000/formrecognizer/v1.0-preview/custom"
```

### <a name="form-recognizer"></a>Form Recognizer

A tárolót biztosít az API-k, amelyek a REST-végpont a [űrlap felismerő API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeWithCustomModel) lapot.


[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>Állítsa le a tároló

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Hibaelhárítás

A tároló futtatásakor használ-e a tároló **stdout** és **stderr** a kimeneti információt, amely segítségére lehet a hibaelhárítási problémák merülnek fel, amikor elindítja vagy a tároló futtatásához.

## <a name="billing"></a>Számlázás

Az űrlap felismerő tárolók küldjön számlázási adatokat az Azure-bA egy _űrlap felismerő_ erőforrást az Azure-fiókjával.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Ezek a beállítások kapcsolatos további információkért lásd: [tárolók konfigurálása](form-recognizer-container-configuration.md).

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Összefoglalás

Ebben a cikkben megtanulta, fogalmak és letöltése, telepítése és a futó tárolók űrlap felismerő munkafolyamat. Összegezve:

* Űrlap felismerő Docker egy Linux-tárolót biztosít.
* Tárolórendszerképek letöltődnek az Azure-ban a privát tárolóregisztrációs adatbázisból.
* Tárolórendszerképek futtatása a Docker.
* Használhatja a REST API vagy a REST SDK-val űrlap felismerő tárolóban műveletek hívására adja meg a gazdagép a tároló URI-t.
* Meg kell adnia a számlázási adatokat, amikor egy tároló elindításához példányosítania.

> [!IMPORTANT]
>  Cognitive Services-tárolók nem teszi lehetővé az Azure-méréshez való csatlakozás nélkül. Az ügyfeleknek kell ahhoz, hogy a tárolókkal való kommunikációhoz mindig a mérési szolgáltatással számlázási adatokat. Cognitive Services-tárolók nem (például a lemezkép vagy az elemezni kívánt szöveget) a vásárlói adatokat küldeni a Microsoftnak.

## <a name="next-steps"></a>További lépések

* Felülvizsgálat [tárolók konfigurálása](form-recognizer-container-configuration.md) a konfigurációs beállításokat.
* Több [Cognitive Services-tárolók](../cognitive-services-container-support.md).
