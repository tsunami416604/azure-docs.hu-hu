---
title: A tároló telepítése és futtatása a Form Recognizer számára
titleSuffix: Azure Cognitive Services
description: Ez a cikk ismerteti, hogyan használhatja az Azure Cognitive Services űrlapfelismerő tároló űrlap- és táblaadatok elemzéséhez.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 8cfa9114c5a5e57882cb84b604c1cf71be9acc52
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878340"
---
# <a name="install-and-run-form-recognizer-containers-preview"></a>Űrlapfelismerő tárolók telepítése és futtatása (előzetes verzió)

Az Azure Form Recognizer gépi tanulási technológiát alkalmaz a kulcs-érték párok és táblák azonosítására és kinyerésére az űrlapokból. Az értékeket és a táblabejegyzéseket a kulcs-érték párokhoz társítja, majd olyan strukturált adatokat ad ki, amelyek tartalmazzák az eredeti fájlban lévő kapcsolatokat. 

Az összetettség csökkentése és az egyéni Űrlapfelismerő modell egyszerű integrálása érdekében a munkafolyamat-automatizálási folyamatba vagy más alkalmazásba egyszerű REST API használatával hívhatja meg a modellt. Csak öt űrlapdokumentumra (vagy egy üres űrlapra és két kitöltött űrlapra) van szükség, így gyorsan, pontosan és az adott tartalomhoz igazítva kaphat eredményeket. Nincs szükség nehéz manuális beavatkozásra vagy kiterjedt adatelemzési szakértelemre. És nem igényel adatcímkézést vagy adatjegyzetet.

> [!IMPORTANT]
> A Form Recognizer tárolók jelenleg a Form Recognizer API 1.0-s verzióját használják. Az API legújabb verzióját a felügyelt szolgáltatás használatával érheti el.

| Függvény | Szolgáltatások |
|----------|----------|
| Form Recognizer | <li>PDF-, PNG- és JPG-fájlok feldolgozása<li>Vonatok egyéni modellek legalább öt formája azonos elrendezésű <li>Kulcsérték-párok és táblaadatok kinyerése <li>Az Azure Cognitive Services Computer Vision API Recognize Text szolgáltatással észleli és kinyeri a nyomtatott szöveget az űrlapokon belüli képekből<li>Nincs szükség jegyzetre vagy címkézésre |

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Űrlapfelismerő tárolók használata előtt meg kell felelnie az alábbi előfeltételeknek:

| Kötelező | Cél |
|----------|---------|
| Docker-motor | A Docker-motort egy [gazdaszámítógépen](#the-host-computer)kell telepíteni. A Docker csomagokat biztosít a Docker-környezet konfigurálásához [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) és [Linux](https://docs.docker.com/engine/installation/#supported-platforms) rendszereken. A Docker és a tárolók alapszintű ismertetéséért lásd a [Docker felhasználói útmutatóját](https://docs.docker.com/engine/docker-overview/).<br><br> A Docker-t úgy kell konfigurálni, hogy a tárolók csatlakozhassanak az Azure-hoz, és számlázási adatokat küldjenek az Azure-ba. <br><br> Windows rendszeren a Dockert is be kell állítani linuxos tárolók támogatására.<br><br> |
| A Docker ismerete | Alapvető ismeretekkel kell rendelkeznie a Docker-fogalmakról, például a nyilvántartásokról, az `docker` adattárakról, a tárolókról és a tárolórendszerképekről, valamint az alapvető parancsok ismeretéről. |
| Azure CLI | Telepítse az [Azure CLI-t](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) a gazdagépre. |
| Computer Vision API erőforrás | A beolvasott dokumentumok és képek feldolgozásához számítógép-látomás-erőforrásra van szükség. A Szöveg felismerése szolgáltatás azure-erőforrásként (REST API vagy SDK) vagy *kognitív szolgáltatások felismerése-szöveg* [tárolóként](../Computer-vision/computer-vision-how-to-install-containers.md#get-the-container-image-with-docker-pull)érhető el. A szokásos számlázási díjak érvényesek. <br><br>Adja át az API-kulcsot és a végpontokat a Computer Vision erőforrás (Azure cloud vagy Cognitive Services tároló). Használja ezt az API-kulcsot és a végpontot **{COMPUTER_VISION_API_KEY}** és **{COMPUTER_VISION_ENDPOINT_URI}** néven.<br><br> Ha a *cognitive-services-recognize-text* tárolót használja, győződjön meg arról, hogy:<br><br>A Form Recognizer tároló Számítógép-vision kulcsa a `docker run` *Cognitive-services-recognize-text* tároló Computer Vision parancsában megadott kulcs.<br>A számlázási végpont a tároló végpontja (például). `http://localhost:5000` Ha a Computer Vision tárolót és a Form Recognizer tárolót is együtt használja ugyanazon a gazdagépen, akkor nem lehet mindkettőt az *5000-es*alapértelmezett porttal elindítani. |
| Űrlapfelismerő erőforrás | A tárolók használatához a következőkre van szüksége:<br><br>Egy **Azure-űrlapfelismerő** erőforrás a társított API-kulcs és végpont URI lehívásához. Mindkét érték elérhető az Azure Portal **űrlapfelismerő** áttekintése és a kulcsok oldalakon, és mindkét érték szükséges a tároló elindításához.<br><br>**{FORM_RECOGNIZER_API_KEY}**: A Kulcsok lapon elérhető két erőforráskulcs egyike<br><br>**{FORM_RECOGNIZER_ENDPOINT_URI}**: Az Áttekintés lapon megadott végpont |

> [!NOTE]
> A Computer Vision erőforrás nevének egyetlen szónak `-` kell lennie, kötőjel és más speciális karakterek nélkül. Ez a korlátozás biztosítja a Képernyőfelismerő és a Szövegtároló kompatibilitásának felismerését.

## <a name="gathering-required-parameters"></a>A szükséges paraméterek összegyűjtése

A Cognitive Services összes tárolójának három elsődleges paramétere van, amelyek szükségesek. A végfelhasználói licencszerződésnek (EULA) a értékűnek `accept`kell lennie. Emellett mind az Endpoint URL-címre, mind az API-kulcsra szükség van.

### <a name="endpoint-uri-computer_vision_endpoint_uri-and-form_recognizer_endpoint_uri"></a>Végpont URI `{COMPUTER_VISION_ENDPOINT_URI}` és`{FORM_RECOGNIZER_ENDPOINT_URI}`

Az **Endpoint** URI-értéke elérhető az Azure Portal *áttekintése* lapon a megfelelő Cognitive Service-erőforrás. Nyissa meg az *Áttekintés* lapot, mutasson `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> a Végpontra, és megjelenik egy ikon. Szükség esetén másolja és használja.

![Gyűjtse össze a végpont uri későbbi használatra](../containers/media/overview-endpoint-uri.png)

### <a name="keys-computer_vision_api_key-and-form_recognizer_api_key"></a>Billentyűk `{COMPUTER_VISION_API_KEY}` és`{FORM_RECOGNIZER_API_KEY}`

Ez a kulcs a tároló indításához használható, és elérhető az Azure Portal Keys oldalán a megfelelő Cognitive Service-erőforrás. Keresse meg a *Kulcsok* lapot, `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> és kattintson az ikonra.

![A két billentyű egyikének beszereznie későbbi használatra](../containers/media/keys-copy-api-key.png)

> [!IMPORTANT]
> Ezek az előfizetési kulcsok a Cognitive Service API eléréséhez használatosak. Ne ossza meg a kulcsait. Tárolja őket biztonságosan, például az Azure Key Vault használatával. Azt is javasoljuk, hogy rendszeresen regenerálja ezeket a kulcsokat. Csak egy kulcs szükséges egy API-hívás hoz. Az első kulcs újragenerálásakor a második kulccsal folyamatos hozzáférést biztosíthat a szolgáltatáshoz.

## <a name="request-access-to-the-container-registry"></a>Hozzáférés kérése a tároló beállításjegyzékéhez

Először ki kell töltenie és el kell küldenie a [Cognitive Services űrlapfelismerő tárolók hozzáférési kérelem űrlap](https://aka.ms/FormRecognizerContainerRequestAccess) hozzáférést a tárolóhoz. Ezzel is feliratkozik a Computer Vision. Nem kell külön regisztrálnia a Computer Vision kérelem űrlapra. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>A gazdaszámítógép

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>A tárolóra vonatkozó követelmények és ajánlások

Az egyes Form Recognizer tárolókhoz lefoglalandó minimális és ajánlott processzormamokat és memóriát az alábbi táblázat ismerteti:

| Tároló | Minimális | Ajánlott |
|-----------|---------|-------------|
| Form Recognizer | 2 mag, 4 GB memória | 4 mag, 8 GB memória |
| Szöveg felismerése | 1 mag, 8 GB memória | 2 mag, 8 GB memória |

* Minden magnak legalább 2,6 gigahertzesnek (GHz) vagy gyorsabbnak kell lennie.
* A mag és `--cpus` a `--memory` memória megfelel nek a `docker run` és a beállításoknak, amelyek a parancs részeként használatosak.

> [!Note]
> A minimális és ajánlott értékek docker-korlátokon *alapulnak, és nem* a gazdagép erőforrásain.

## <a name="get-the-container-images-with-the-docker-pull-command"></a>A tárolórendszerképek bekérése a docker lekéréseparancsmal

Az **űrlapfelismerő** és a **szövegfelismerési** ajánlatok tárolólemezképei a következő tárolóbeállítási adatbázisban érhetők el:

| Tároló | Teljesen minősített képnév |
|-----------|------------|
| Form Recognizer | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest` |
| Szöveg felismerése | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest` |

Mindkét tárolóra szüksége lesz, kérjük, vegye figyelembe, hogy a **recognizer text** tároló [a cikken kívül van részletezve.](../Computer-vision/computer-vision-how-to-install-containers.md#get-the-container-image-with-docker-pull)

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-form-recognizer-container"></a>Docker-lekérésea a Form Recognizer tárolóhoz

#### <a name="form-recognizer"></a>Form Recognizer

Az Űrlapfelismerő tároló beszerezéséhez használja a következő parancsot:

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest
```
### <a name="docker-pull-for-the-recognize-text-container"></a>Docker-lekérésea a Szöveg felismerése tárolóhoz

#### <a name="recognize-text"></a>Szöveg felismerése

A Szöveg felismerése tároló beszerezhető, a következő paranccsal:

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

## <a name="how-to-use-the-container"></a>A tároló használata

Miután a tároló a [gazdaszámítógépen](#the-host-computer)van, a következő eljárással dolgozzon a tárolóval.

1. [Futtassa a tárolót](#run-the-container-by-using-the-docker-run-command)a szükséges számlázási beállításokkal. További [példák](form-recognizer-container-configuration.md#example-docker-run-commands) a `docker run` parancs állnak rendelkezésre.
1. [Kérdezze meg a tároló előrejelzési végpontját.](#query-the-containers-prediction-endpoint)

## <a name="run-the-container-by-using-the-docker-run-command"></a>A tároló futtatása a docker run paranccsal

Használja a [docker run](https://docs.docker.com/engine/reference/commandline/run/) parancsot a tároló futtatásához. A [szükséges paraméterek begyűjtését](#gathering-required-parameters) a , `{COMPUTER_VISION_ENDPOINT_URI}` `{COMPUTER_VISION_API_KEY}`a `{FORM_RECOGNIZER_ENDPOINT_URI}` `{FORM_RECOGNIZER_API_KEY}` , és az értékek begyűjtésének részleteit találja.

[Példák](form-recognizer-container-configuration.md#example-docker-run-commands) `docker run` a parancs állnak rendelkezésre.

### <a name="form-recognizer"></a>Form Recognizer

```bash
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

A parancs a következőket hajtja végre:

* Űrlapfelismerő tárolót futtat a tárolólemezképből.
* 2 processzormagot és 8 gigabájt (GB) memóriát foglal le.
* Elérhetővé teszi az 5000-es TCP-portot, és egy pszeudo-TTY-t rendel a tárolóhoz.
* A tároló automatikus eltávolítása kilépés után. A tárolórendszerkép továbbra is elérhető a gazdaszámítógépen.
* A /input és a /output kötetet csatlakoztatja a tárolóhoz.

[!INCLUDE [Running multiple containers on the same host H2](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

### <a name="run-separate-containers-as-separate-docker-run-commands"></a>Külön tárolók futtatása külön docker futtatási parancsként

Az ugyanazon az állomáson helyileg üzemeltetett Űrlapfelismerő és szövegfelismerő kombinációhoz használja a következő két példa Docker CLI-parancsokat:

Futtassa az első tárolót az 5000-es porton. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY}
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

Futtassa a második tárolót az 5001-es porton.

```bash 
docker run --rm -it -p 5001:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
Eula=accept \
Billing={COMPUTER_VISION_ENDPOINT_URI} \
ApiKey={COMPUTER_VISION_API_KEY}
```
Minden további tárolónak egy másik porton kell lennie. 

### <a name="run-separate-containers-with-docker-compose"></a>Külön tárolók futtatása a Docker Compose segítségével

Az űrlapfelismerő és a szövegfelismerő kombináció, amely helyileg üzemelteti az ugyanazon a gazdagépen, lásd a következő példát Docker Compose YAML fájlt. A szövegfelismerőnek `{COMPUTER_VISION_API_KEY}` meg kell `formrecognizer` egyeznie mind a `ocr` tárolók, mind a tárolók esetében. A `{COMPUTER_VISION_ENDPOINT_URI}` csak a `ocr` tárolóban használatos, mert a `formrecognizer` tároló a nevet és a `ocr` portot használja. 

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
      apikey: "{COMPUTER_VISION_API_KEY}"

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
      billing: "{FORM_RECOGNIZER_ENDPOINT_URI}"
      apikey: "{FORM_RECOGNIZER_API_KEY}"
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
> A `Eula` `Billing`, `ApiKey`és , valamint `FormRecognizer:ComputerVisionApiKey` `FormRecognizer:ComputerVisionEndpointUri` a és a beállításokat kell megadni a tároló futtatásához; ellenkező esetben a tároló nem indul el. További információ: [Billing](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>A tároló előrejelzési végpontjának lekérdezése

|Tároló|Végpont|
|--|--|
|űrlapfelismerő|http://localhost:5000

### <a name="form-recognizer"></a>Form Recognizer

A tároló websocket-alapú lekérdezési végpontAPI-kat biztosít, amelyeket [a Form Recognizer services SDK dokumentációjával](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/)érhet el.

Alapértelmezés szerint a Form Recognizer SDK az online szolgáltatásokat használja. A tároló használatához módosítania kell az inicializálási módszert. Ez az alábbi példákon látható.

#### <a name="for-c"></a>A C #

Az Azure-felhőalapú inicializálási hívás használatának módosítása:

```csharp
var config =
    FormRecognizerConfig.FromSubscription(
        "YourSubscriptionKey",
        "YourServiceRegion");
```
a híváshoz, amely a tároló végpontját használja:

```csharp
var config =
    FormRecognizerConfig.FromEndpoint(
        "ws://localhost:5000/formrecognizer/v1.0-preview/custom",
        "YourSubscriptionKey");
```

#### <a name="for-python"></a>Python-hoz

Az Azure-felhőalapú inicializálási hívás használatának módosítása:

```python
formrecognizer_config =
    formrecognizersdk.FormRecognizerConfig(
        subscription=formrecognizer_key, region=service_region)
```

a híváshoz, amely a tároló végpontját használja:

```python
formrecognizer_config = 
    formrecognizersdk.FormRecognizerConfig(
        subscription=formrecognizer_key,
        endpoint="ws://localhost:5000/formrecognizer/v1.0-preview/custom"
```

### <a name="form-recognizer"></a>Form Recognizer

A tároló REST-végpont API-kat biztosít, amelyek a [Form Recognizer API-lapon](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeWithCustomModel) találhatók.


[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>A tároló leállítása

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a tárolót kimeneti [csatlakoztatással](form-recognizer-container-configuration.md#mount-settings) és naplózással futtatja, a tároló naplófájlokat hoz létre, amelyek hasznosak a tároló indításakor vagy futtatásakor felmerülő problémák elhárításához.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Számlázás

Az űrlapfelismerő tárolók számlázási adatokat küldenek az Azure-ba az Azure-fiókjában _lévő Űrlapfelismerő_ erőforrás használatával.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Ezekről a beállításokról a [Tárolók konfigurálása](form-recognizer-container-configuration.md)című témakörben talál további információt.

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Összefoglalás

Ebben a cikkben ismertetheti a Form Recognizer tárolók letöltésére, telepítésére és futtatására vonatkozó fogalmakat és munkafolyamatokat. Összegezve:

* A Form Recognizer egy Linux-tárolót biztosít a Docker számára.
* A tárolórendszerképek az Azure-beli privát tároló beállításjegyzékből töltődnek le.
* A tárolórendszerképek a Dockerben futnak.
* A REST API vagy a REST SDK segítségével a tároló gazdaURI-jának megadásával hívhatja meg a műveleteket a Form Recognizer tárolóban.
* Meg kell adnia a számlázási adatokat, amikor példányosítja a tárolót.

> [!IMPORTANT]
>  A Cognitive Services-tárolók nem rendelkeznek licenccel anélkül, hogy az Azure-hoz csatlakoznának a méréshez. Az ügyfeleknek lehetővé kell tenni, hogy a tárolók mindig kommunikálják a számlázási adatokat a mérési szolgáltatással. A Cognitive Services-tárolók nem küldenek ügyféladatokat (például az elemzett képet vagy szöveget) a Microsoftnak.

## <a name="next-steps"></a>További lépések

* Tekintse át [a Tárolók konfigurálása](form-recognizer-container-configuration.md) a konfigurációs beállításokat.
* További [Cognitive Services-tárolók használata](../cognitive-services-container-support.md).
