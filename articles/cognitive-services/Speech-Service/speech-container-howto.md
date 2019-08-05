---
title: Speech containers – beszédfelismerési szolgáltatás telepítése
titleSuffix: Azure Cognitive Services
description: Beszédfelismerési tárolók telepítése és futtatása. A beszéd-szöveg szöveggé átmásolja a hangadatfolyamokat valós időben a szöveggé, hogy alkalmazásai, eszközei vagy eszközei képesek legyenek a felhasználásra vagy a megjelenítésre. A szöveg és a beszéd szöveggé alakítja át a bemeneti szöveget az emberi, például a szintetizált beszédbe.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: 14a360e7dc672266e8445a5ae5eb2168ae766af8
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2019
ms.locfileid: "68741865"
---
# <a name="install-and-run-speech-service-containers"></a>Beszédfelismerő szolgáltatás tárolóinak telepítése és futtatása

A beszédfelismerési tárolók lehetővé teszik, hogy az ügyfelek egy olyan beszédfelismerési alkalmazás-architektúrát építsenek ki, amely a robusztus Felhőbeli képességek és a peremhálózat környékének kihasználására optimalizált. 

A két beszédfelismerési tároló **beszéd – szöveg** és szöveg – **beszéd**. 

|Függvény|Szolgáltatások|Legutóbbi|
|-|-|--|
|Speech-to-text| <li>Folyamatos valós idejű beszédet vagy kötegelt hangfelvételeket vált ki közbenső eredményekkel rendelkező szövegbe.|1.1.3|
|Szövegfelolvasás| <li>Az írott szöveget természetesnek hangzó beszéddé alakítja. egyszerű szövegbevitel vagy beszéd szintézis Markup Language (SSML) nyelven. |1.1.0|

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

A Speech containers használata előtt meg kell felelnie a következő előfeltételeknek:

|Kötelező|Cél|
|--|--|
|Docker-motor| A Docker-motornak telepítve kell lennie a [gazdagépen](#the-host-computer). A Docker csomagokat biztosít a Docker-környezet konfigurálásához [MacOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/)és [Linux](https://docs.docker.com/engine/installation/#supported-platforms)rendszereken. A Docker és a tárolók alapfogalmainak ismertetését lásd: a [a Docker áttekintése](https://docs.docker.com/engine/docker-overview/).<br><br> Docker kell konfigurálni, hogy a tárolók számlázási adatok küldése az Azure-ba történő csatlakozáshoz. <br><br> **Windows rendszeren a**Docker-t is konfigurálni kell a Linux-tárolók támogatásához.<br><br>|
|A Docker ismerete | Alapvető ismeretekkel kell rendelkeznie a Docker-fogalmakról, például a kibocsátásiegység-forgalmi jegyzékekről, a adattárakról, a tárolók és a `docker` tárolók lemezképéről, valamint az alapszintű parancsokról.| 
|Beszédfelismerési erőforrás |A tárolók használatához a következőket kell tennie:<br><br>Egy Azure _Speech_ -erőforrás a társított API-kulcs és végpont URI-azonosító lekéréséhez. Mindkét érték elérhető a Azure Portal beszédének áttekintése és a kulcsok oldalain. Mindkettő szükséges a tároló elindításához.<br><br>**{API_KEY}** : A **kulcsok** oldalon található két elérhető erőforrás-kulcs egyike<br><br>**{ENDPOINT_URI}** : Az **Áttekintés** oldalon megadott végpont|

## <a name="request-access-to-the-container-registry"></a>Hozzáférés kérése a tároló beállításjegyzékéhez

Először be kell fejeznie és el kell küldenie a [Cognitive Services Speech containers kérelem űrlapját](https://aka.ms/speechcontainerspreview/) , hogy hozzáférést Kérjen a tárolóhoz. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>A gazdaszámítógép

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Speciális vektoros bővítmény támogatása

A **gazdagép** a Docker-tárolót futtató számítógép. A gazdagépnek támogatnia kell a [speciális vektoros bővítményeket](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). Ezt a támogatást Linux-gazdagépeken a következő paranccsal tekintheti meg: 

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```

### <a name="container-requirements-and-recommendations"></a>Tároló-követelményeket és javaslatokat

Az alábbi táblázat az egyes beszédfelismerési tárolók számára lefoglalható minimális és ajánlott CPU-magokat és memóriát ismerteti.

| Tároló | Minimális | Ajánlott |
|-----------|---------|-------------|
|cognitive-services-speech-to-text | 2 mag<br>2 GB memória  | 4 mag<br>4 GB memória  |
|kognitív szolgáltatások – szöveg – beszéd | 1 mag, 0,5 – GB memória| 2 mag, 1 GB memória |

* Minden mag legalább 2,6 gigahertz (GHz) vagy gyorsabb lehet.

Az alap és a memória a `--cpus` `docker run` parancs `--memory` részeként használt és beállításoknak felel meg.

**Megjegyzés**; A minimális és ajánlott a Docker korlátain kívüli, *nem* pedig a gazdagép erőforrásai. Például a beszéd-szöveg típusú tárolók a nagyméretű nyelvi modell részei, és azt _javasoljuk_ , hogy a teljes fájl elfér a memóriában, ami egy további 4-6 GB. A tárolók első futtatása hosszabb időt is igénybe vehet, mivel a modellek a memóriába kerülnek.

## <a name="get-the-container-image-with-docker-pull"></a>A tároló rendszerképének beolvasása a`docker pull`

A beszédfelismerési lemezképek elérhetők.

| Tároló | Tárház |
|-----------|------------|
| cognitive-services-speech-to-text | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest` |
| kognitív szolgáltatások – szöveg – beszéd | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="language-locale-is-in-container-tag"></a>A nyelvi területi beállítás a Container címkében van

A `latest` címke lekéri a `en-us` területi beállításokat és `jessarus` a hangot.

#### <a name="speech-to-text-locales"></a>Szövegbeli területi beállítások

A (z) `latest` kivételével az összes címke a következő formátumban van, ahol az a `<culture>` területi beállítású tárolót jelzi:

```
<major>.<minor>.<patch>-<platform>-<culture>-<prerelease>
```

A következő címke egy példa a formátumra:

```
1.1.3-amd64-en-us-preview
```

A következő táblázat felsorolja a **beszéd és a szöveg** támogatott területi beállítását a tároló 1.1.3-es verziójában:

|Nyelvi területi beállítás|Tags|
|--|--|
|kínai|`zh-cn`|
|Angol |`en-us`<br>`en-gb`<br>`en-au`<br>`en-in`|
|francia |`fr-ca`<br>`fr-fr`|
|német|`de-de`|
|olasz|`it-it`|
|japán|`ja-jp`|
|koreai|`ko-kr`|
|portugál|`pt-br`|
|spanyol|`es-es`<br>`es-mx`|

#### <a name="text-to-speech-locales"></a>Szöveg-beszéd területi beállítások

A (z) `latest` kivételével az összes címke a következő formátumban van, ahol a `<culture>` területi beállítás és `<voice>` a tároló hangja látható:

```
<major>.<minor>.<patch>-<platform>-<culture>-<voice>-<prerelease>
```

A következő címke egy példa a formátumra:

```
1.1.0-amd64-en-us-jessarus-preview
```

A következő táblázat a tároló 1.1.0-verziójában lévő **szöveg-beszéd** támogatott területi beállításokat sorolja fel:

|Nyelvi területi beállítás|Tags|Támogatott hangok|
|--|--|--|
|kínai|`zh-cn`|huihuirus<br>kangkang-apollo<br>yaoyao-apollo|
|Angol |`en-au`|Catherine<br>hayleyrus|
|Angol |`en-gb`|george-apollo<br>hazelrus<br>Susan – Apollo|
|Angol |`en-in`|heera – Apollo<br>priyarus<br>fosztogatás – Apollo<br>|
|Angol |`en-us`|jessarus<br>benjaminrus<br>jessa24krus<br>zirarus<br>guy24krus|
|francia|`fr-ca`|Caroline<br>harmonierus|
|francia|`fr-fr`|hortenserus<br>Julie – Apollo<br>paul-apollo|
|német|`de-de`|hedda<br>heddarus<br>Stefan – Apollo|
|olasz|`it-it`|cosimo-apollo<br>luciarus|
|japán|`ja-jp`|Ayumi – Apollo<br>harukarus<br>Ichiro – Apollo|
|koreai|`ko-kr`|heamirus|
|portugál|`pt-br`|daniel-apollo<br>heloisarus|
|spanyol|`es-es`|elenarus<br>Laura – Apollo<br>pablo-apollo<br>|
|spanyol|`es-mx`|hildarus<br>raul-apollo|

### <a name="docker-pull-for-the-speech-containers"></a>Docker-lekérés a beszédfelismerési tárolók számára

#### <a name="speech-to-text"></a>Speech-to-text

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest
```

#### <a name="text-to-speech"></a>Szövegfelolvasás

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest
```

## <a name="how-to-use-the-container"></a>A tároló használata

Miután a tároló a gazdagépen [](#the-host-computer)található, a következő eljárással dolgozhat a tárolóval.

1. [Futtassa a tárolót](#run-the-container-with-docker-run)a kötelező számlázási beállításokkal. További [példák](speech-container-configuration.md#example-docker-run-commands) a `docker run` parancsra.
1. [A tároló előrejelzési végpontjának lekérdezése](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>A tároló futtatása a`docker run`

A három tároló bármelyikének futtatásához használja a [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) parancsot. A parancs a következő paramétereket használja:

Az **előzetes verzió ideje alatt**a számlázási beállításoknak érvényesnek kell lenniük a tároló elindításához, de a használatért nem kell fizetnie.

| Helyőrző | Value |
|-------------|-------|
|{API_KEY} | Ez a kulcs a tároló elindítására szolgál, és a Azure Portal beszédfelismerési kulcsok lapján érhető el.  |
|{ENDPOINT_URI} | A számlázási végpont URI-ja a Azure Portal beszédének áttekintése oldalon érhető el.|

Cserélje le ezeket a paramétereket a saját értékeire a következő `docker run` példában szereplő parancsban.

### <a name="text-to-speech"></a>Szövegfelolvasás

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="speech-to-text"></a>Speech-to-text

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Ez a parancs:

* Beszédfelismerési tároló futtatása a tároló képéből
* 2 CPU-mag és 2 gigabájt (GB) memória kiosztása
* Elérhetővé teszi az 5000-es TCP-porton és a egy pszeudo-TTY lefoglalja a tároló
* A automatikusan eltávolítja a tárolót a kilépés után. A tároló rendszerképe továbbra is elérhető a gazdaszámítógépen.

> [!IMPORTANT]
> A `Eula`, `Billing`, és `ApiKey` beállítások meg kell adni a tároló futtatásához; ellenkező esetben a tároló nem indul el.  További információkért lásd: [számlázási](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>A tároló előrejelzési végpontjának lekérdezése

|Tároló|Végpont|
|--|--|
|Speech-to-text|ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1|
|Szövegfelolvasás|http://localhost:5000/speech/synthesize/cognitiveservices/v1|

### <a name="speech-to-text"></a>Speech-to-text

A tároló olyan WebSocket-alapú lekérdezési végpont API-kat biztosít, amelyek a [SPEECH SDK](index.yml)-n keresztül érhetők el.

Alapértelmezés szerint a Speech SDK az online Speech Services szolgáltatást használja. A tároló használatához módosítania kell az inicializálási módszert. Tekintse meg az alábbi példákat.

#### <a name="for-c"></a>AC#

Váltás az Azure-felhő inicializálási hívásának használatával:

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

Ehhez a híváshoz a tároló végpontja használatával:

```csharp
var config = SpeechConfig.FromEndpoint(
    new Uri("ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1"),
    "YourSubscriptionKey");
```

#### <a name="for-python"></a>Python esetén

Váltás az Azure-felhő inicializálási hívásának használatával

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

Ehhez a híváshoz a tároló végpontja használatával:

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, endpoint="ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1")
```

### <a name="text-to-speech"></a>Szövegfelolvasás

A tároló olyan REST-végponti API-kat biztosít, amelyek [itt](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech) találhatók, és mintákat [itt](https://azure.microsoft.com/resources/samples/cognitive-speech-tts/)találhat.

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>A tároló leállítása

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Hibaelhárítás

Amikor futtatja a tárolót, a tároló az **StdOut** és a **stderr** használatával adja meg azokat a hibákat, amelyek a tároló indításakor vagy futtatásakor felmerülő hibák elhárításában nyújtanak segítséget.

## <a name="billing"></a>Számlázás

A beszédfelismerési tárolók számlázási adatokat küldenek az Azure -nak az Azure-fiókjában lévő beszédfelismerési erőforrás használatával.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Ezek a beállítások kapcsolatos további információkért lásd: [tárolók konfigurálása](speech-container-configuration.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Összegzés

Ebben a cikkben megtanulta a beszédfelismerési tárolók letöltésére, telepítésére és futtatására vonatkozó fogalmakat és munkafolyamatokat. Összegezve:

* A Speech két Linux-tárolót biztosít a Docker számára, szövegbe és szövegbe ágyazva beszédet.
* A tároló lemezképeit a rendszer az Azure-beli privát tároló-beállításjegyzékből tölti le.
* Tárolórendszerképek futtatása a Docker.
* Használhatja a REST API vagy az SDK-t a műveleteknek a beszédfelismerési tárolókban való meghívásához a tároló gazdagép URI azonosítójának megadásával.
* A tárolók példányának létrehozásakor számlázási adatokat kell megadnia.

> [!IMPORTANT]
>  Cognitive Services-tárolók nem teszi lehetővé az Azure-méréshez való csatlakozás nélkül. Az ügyfeleknek kell ahhoz, hogy a tárolókkal való kommunikációhoz mindig a mérési szolgáltatással számlázási adatokat. Cognitive Services-tárolók nem (például a lemezkép vagy az elemezni kívánt szöveget) a vásárlói adatokat küldeni a Microsoftnak.

## <a name="next-steps"></a>További lépések

* Felülvizsgálat [tárolók konfigurálása](speech-container-configuration.md) a konfigurációs beállítások
* További [Cognitive Services tárolók](../cognitive-services-container-support.md) használata
