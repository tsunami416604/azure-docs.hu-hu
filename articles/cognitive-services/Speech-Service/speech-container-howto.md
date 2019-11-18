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
ms.date: 11/15/2019
ms.author: dapine
ms.openlocfilehash: 4170db596d3d4f4b197120770afa2f6e8b0f8a1c
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132603"
---
# <a name="install-and-run-speech-service-containers"></a>Beszédfelismerő szolgáltatás tárolóinak telepítése és futtatása

A tárolók lehetővé teszik a beszédfelismerési szolgáltatás egyes API-jai futtatását a saját környezetében. A tárolók az adott biztonsági és adatirányítási követelményekhez is kiválóak. Ebből a cikkből megtudhatja, hogyan töltheti le, telepítheti és futtathatja a beszédfelismerési tárolókat.

A beszédfelismerési tárolók lehetővé teszik, hogy az ügyfelek olyan beszédfelismerési alkalmazás-architektúrát hozzanak létre, amely robusztus Felhőbeli képességekre és Edge-helyekre optimalizált Négy különböző tároló érhető el. A két szabványos tároló a **beszédfelismerés** és a **szöveg közötti**kommunikáció. A két egyéni tároló **Custom Speech szöveg** és **egyéni szöveg-beszéd**.

> [!IMPORTANT]
> Az összes beszédfelismerési tároló jelenleg egy [nyilvános "GateD" előzetes](../cognitive-services-container-support.md#public-gated-preview-container-registry-containerpreviewazurecrio)verzió részeként érhető el. Bejelentést kell készíteni, amikor a beszédfelismerési tárolók az általánosan elérhetővé vált (GA).

| Függvény | Szolgáltatások | Legújabb |
|--|--|--|
| Diktálás | Folyamatos valós idejű beszédet vagy kötegelt hangfelvételeket vált ki közbenső eredményekkel rendelkező szövegbe. | 2.0.0 |
| Custom Speech – szöveg | A [Custom Speech portál](https://speech.microsoft.com/customspeech)egyéni modelljét használva folyamatos valós idejű beszédet vagy batch-hangfelvételeket vált ki közbenső eredményekkel rendelkező szövegbe. | 2.0.0 |
| Szövegfelolvasás | A szöveget természetes hangú beszédre konvertálja egyszerű szöveges bevitelsel vagy beszéd szintézis Markup Language (SSML) nyelvvel. | 1.3.0 |
| Egyéni szöveg – beszéd | Ha egyéni modellt használ az [Egyéni hangportálról](https://aka.ms/custom-voice-portal), a szövegeket természetes hangú beszédre alakítja egyszerű szöveges bevitel vagy beszédfelismerési leíró nyelv (SSML) használatával. | 1.3.0 |

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

A Speech containers használata előtt a következő előfeltételek szükségesek:

| Kötelező | Cél |
|--|--|
| Docker-motor | A Docker-motornak telepítve kell lennie a [gazdagépen](#the-host-computer). A Docker csomagokat biztosít a Docker-környezet konfigurálásához [MacOS](https://docs.docker.com/docker-for-mac/), Windows és [Linux](https://docs.docker.com/engine/installation/#supported-platforms) [rendszereken](https://docs.docker.com/docker-for-windows/). A Docker és a tárolók alapfogalmainak ismertetését lásd: a [a Docker áttekintése](https://docs.docker.com/engine/docker-overview/).<br><br> Docker kell konfigurálni, hogy a tárolók számlázási adatok küldése az Azure-ba történő csatlakozáshoz. <br><br> **Windows rendszeren a**Docker-t is konfigurálni kell a Linux-tárolók támogatásához.<br><br> |
| A Docker ismerete | Alapvető ismeretekkel kell rendelkeznie a Docker-fogalmakról, például a kibocsátásiegység-forgalmi jegyzékekről, a adattárakról, a tárolók és a tárolók rendszerképeiről, valamint az alapszintű `docker` parancsok megismeréséről. |
| Beszédfelismerési erőforrás | A tárolók használatához a következőket kell tennie:<br><br>Egy Azure _Speech_ -erőforrás a társított API-kulcs és végpont URI-azonosító lekéréséhez. Mindkét érték elérhető a Azure Portal **beszédének** áttekintése és a kulcsok oldalain. Mindkettő szükséges a tároló elindításához.<br><br>**{API_KEY}** : a **kulcsok** oldalon található két elérhető erőforrás-kulcs egyike<br><br>**{ENDPOINT_URI}** : az **Áttekintés** lapon megadott végpont |

## <a name="request-access-to-the-container-registry"></a>Hozzáférés kérése a tároló beállításjegyzékéhez

Töltse ki és küldje el a [Cognitive Services Speech containers kérelem űrlapját](https://aka.ms/speechcontainerspreview/) , hogy hozzáférést Kérjen a tárolóhoz. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>A gazdaszámítógép

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Speciális vektoros bővítmény támogatása

A **gazdagép** a Docker-tárolót futtató számítógép. A gazdagépnek *támogatnia kell* a [speciális vektoros bővítményeket](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). A Linux-gazdagépek AVX2-támogatását a következő paranccsal tekintheti meg:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> A AVX2 támogatásához a gazdaszámítógép *szükséges* . A tároló *nem fog* megfelelően működni AVX2-támogatás nélkül.

### <a name="container-requirements-and-recommendations"></a>Tároló-követelményeket és javaslatokat

Az alábbi táblázat az egyes beszédfelismerési tárolók minimális és ajánlott erőforrás-elosztását ismerteti.

# <a name="speech-to-texttabstt"></a>[Beszédfelismerés](#tab/stt)

| Tároló | Minimális | Ajánlott |
|-----------|---------|-------------|
| Diktálás | 2 mag, 2 GB memória | 4 mag, 4 GB memória |

# <a name="custom-speech-to-texttabcstt"></a>[Custom Speech – szöveg](#tab/cstt)

| Tároló | Minimális | Ajánlott |
|-----------|---------|-------------|
| Custom Speech – szöveg | 2 mag, 2 GB memória | 4 mag, 4 GB memória |

# <a name="text-to-speechtabtts"></a>[Szövegfelolvasás](#tab/tts)

| Tároló | Minimális | Ajánlott |
|-----------|---------|-------------|
| Szövegfelolvasás | 1 mag, 2 GB memória | 2 mag, 3 GB memória |

# <a name="custom-text-to-speechtabctts"></a>[Egyéni szöveg – beszéd](#tab/ctts)

| Tároló | Minimális | Ajánlott |
|-----------|---------|-------------|
| Egyéni szöveg – beszéd | 1 mag, 2 GB memória | 2 mag, 3 GB memória |

***

* Minden mag legalább 2,6 gigahertz (GHz) vagy gyorsabb lehet.

Az alap és a memória a `docker run` parancs részeként használt `--cpus` és `--memory` beállításoknak felel meg.

> [!NOTE]
> A minimális és ajánlott a Docker korlátain kívüli, *nem* pedig a gazdagép erőforrásai. Például a beszéd-szöveg típusú tárolók a nagyméretű nyelvi modell részei, és azt *javasoljuk* , hogy a teljes fájl elfér a memóriában, ami egy további 4-6 GB. A tárolók első futtatása hosszabb időt is igénybe vehet, mivel a modellek a memóriába kerülnek.

## <a name="get-the-container-image-with-docker-pull"></a>A tároló rendszerképének beolvasása a `docker pull`

A Speech tároló lemezképei a következő Container Registry érhetők el.

# <a name="speech-to-texttabstt"></a>[Beszédfelismerés](#tab/stt)

| Tároló | Tárház |
|-----------|------------|
| Diktálás | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest` |

# <a name="custom-speech-to-texttabcstt"></a>[Custom Speech – szöveg](#tab/cstt)

| Tároló | Tárház |
|-----------|------------|
| Custom Speech – szöveg | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest` |

# <a name="text-to-speechtabtts"></a>[Szövegfelolvasás](#tab/tts)

| Tároló | Tárház |
|-----------|------------|
| Szövegfelolvasás | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest` |

# <a name="custom-text-to-speechtabctts"></a>[Egyéni szöveg – beszéd](#tab/ctts)

| Tároló | Tárház |
|-----------|------------|
| Egyéni szöveg – beszéd | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest` |

***

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-speech-containers"></a>Docker-lekérés a beszédfelismerési tárolók számára

# <a name="speech-to-texttabstt"></a>[Beszédfelismerés](#tab/stt)

#### <a name="docker-pull-for-the-speech-to-text-container"></a>Docker-lekérés a beszéd-szöveg tárolóhoz

A [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) parancs használatával letöltheti a tároló rendszerképét a Container Preview beállításjegyzékből.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest
```

> [!IMPORTANT]
> A `latest` címke lekéri a `en-US` területi beállítást. További területi beállítások: [beszéd – szöveg területi beállítások](#speech-to-text-locales).

#### <a name="speech-to-text-locales"></a>Beszéd – szöveg területi beállítások

A `latest` kivételével minden címke a következő formátumban van, ahol a `<culture>` a területi tárolót jelzi:

```
<major>.<minor>.<patch>-<platform>-<culture>-<prerelease>
```

A következő címke egy példa a formátumra:

```
2.0.0-amd64-en-us-preview
```

A **beszéd-szöveg** típusú tároló összes támogatott területi beállítását lásd: [beszéd – szöveg képcímkék](../containers/container-image-tags.md#speech-to-text).

# <a name="custom-speech-to-texttabcstt"></a>[Custom Speech – szöveg](#tab/cstt)

#### <a name="docker-pull-for-the-custom-speech-to-text-container"></a>Docker-lekérés a Custom Speech – Text tárolóhoz

A [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) parancs használatával letöltheti a tároló rendszerképét a Container Preview beállításjegyzékből.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest
```

> [!NOTE]
> Az egyéni beszédfelismerési tárolók `locale` és `voice` a tároló által betöltött egyéni modell határozza meg.

# <a name="text-to-speechtabtts"></a>[Szövegfelolvasás](#tab/tts)

#### <a name="docker-pull-for-the-text-to-speech-container"></a>Docker-lekérés a szöveg-beszéd tárolóhoz

A [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) parancs használatával letöltheti a tároló rendszerképét a Container Preview beállításjegyzékből.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest
```

> [!IMPORTANT]
> A `latest` címke lekéri a `en-US` területi beállítását és `jessarus` hangot. További területi beállítások: [szöveg – beszéd területi beállítások](#text-to-speech-locales).

#### <a name="text-to-speech-locales"></a>Szöveg – beszéd területi beállítások

A `latest` kivételével az összes címke a következő formátumban van, ahol a `<culture>` a területi beállítást jelzi, a `<voice>` pedig a tároló hangját jelzi:

```
<major>.<minor>.<patch>-<platform>-<culture>-<voice>-<prerelease>
```

A következő címke egy példa a formátumra:

```
1.3.0-amd64-en-us-jessarus-preview
```

A **szöveg-beszéd** típusú tároló összes támogatott területi beállítása és a hozzájuk tartozó hangok esetében lásd: [szöveg – beszéd képcímkék](../containers/container-image-tags.md#text-to-speech).

> [!IMPORTANT]
> *Szabványos szöveg-beszéd http-* bejegyzés létrehozásakor a [Speech szintézis MARKUP Language (SSML)](speech-synthesis-markup.md) üzenetéhez `voice` elemnek `name` attribútummal kell rendelkeznie. Az érték a megfelelő tároló területi beállítása és hangja, más néven ["rövid név"](language-support.md#standard-voices). Például a `latest` címke neve `en-US-JessaRUS`.

# <a name="custom-text-to-speechtabctts"></a>[Egyéni szöveg – beszéd](#tab/ctts)

#### <a name="docker-pull-for-the-custom-text-to-speech-container"></a>Docker-lekérés az egyéni szöveg-beszéd tárolóhoz

A [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) parancs használatával letöltheti a tároló rendszerképét a Container Preview beállításjegyzékből.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest
```

> [!NOTE]
> Az egyéni beszédfelismerési tárolók `locale` és `voice` a tároló által betöltött egyéni modell határozza meg.

***

## <a name="how-to-use-the-container"></a>A tároló használata

Miután a tároló a [gazdagépen](#the-host-computer) található, a következő eljárással dolgozhat a tárolóval.

1. [Futtassa a tárolót](#run-the-container-with-docker-run)a kötelező számlázási beállításokkal. További [példák](speech-container-configuration.md#example-docker-run-commands) a `docker run` parancsra.
1. [A tároló előrejelzési végpontjának lekérdezése](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>A tároló futtatása `docker run`

A tároló futtatásához használja a [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) parancsot. A `{Endpoint_URI}` és `{API_Key}` értékek beszerzésével kapcsolatos részletekért tekintse meg a [szükséges paraméterek összegyűjtését](#gathering-required-parameters) ismertető témakört. A `docker run` parancs további [példái](speech-container-configuration.md#example-docker-run-commands) is elérhetők.

# <a name="speech-to-texttabstt"></a>[Beszédfelismerés](#tab/stt)

A *beszéd-szöveg* tároló futtatásához hajtsa végre a következő `docker run` parancsot.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Ez a parancs:

* Egy *beszéd-szöveg* tárolót futtat a tároló képéből.
* 4 CPU-magot és 4 GB memóriát foglal le.
* Elérhetővé teszi a 5000-es TCP-portot, és egy pszeudo-TTY-t foglal le a tárolóhoz.
* A automatikusan eltávolítja a tárolót a kilépés után. A tároló rendszerképe továbbra is elérhető a gazdaszámítógépen.

# <a name="custom-speech-to-texttabcstt"></a>[Custom Speech – szöveg](#tab/cstt)

A *Custom Speech-szöveg* típusú tároló egy egyéni beszédfelismerési modellre támaszkodik. Az egyéni modellt a [Custom Speech Portal](https://speech.microsoft.com/customspeech)használatával kell [betanítani](how-to-custom-speech-train-model.md) . A tároló futtatásához az egyéni beszédfelismerési **modell azonosítója** szükséges. Ez a Custom Speech Portal **képzés** lapján található. Az egyéni Speech Portalon navigáljon a **képzés** lapra, és válassza ki a modellt.
<br>

![Egyéni beszédfelismerési oldal](media/custom-speech/custom-speech-model-training.png)

Szerezze be a `docker run` parancs `ModelId` paraméteréhez argumentumként használandó **modell azonosítóját** .
<br>

![Egyéni beszédfelismerési modell részletei](media/custom-speech/custom-speech-model-details.png)

A következő táblázat a különböző `docker run` paramétereket és a hozzájuk tartozó leírásokat tartalmazza:

| Paraméter | Leírás |
|---------|---------|
| `{VOLUME_MOUNT}` | A gazdaszámítógép [kötetének csatlakoztatása](https://docs.docker.com/storage/volumes/), amelyet a Docker az egyéni modell megtartására használ. Például a *C:\CustomSpeech* , ahol a *C meghajtó* található a gazdagépen. |
| `{MODEL_ID}` | A Custom Speech **modell azonosítója** az egyéni beszédfelismerési portál **képzés** lapján. |
| `{ENDPOINT_URI}` | A méréshez és a számlázáshoz a végpont szükséges. További információ: a [szükséges paraméterek összegyűjtése](#gathering-required-parameters). |
| `{API_KEY}` | Az API-kulcs megadása kötelező. További információ: a [szükséges paraméterek összegyűjtése](#gathering-required-parameters). |

A *Custom Speech – Text* tároló futtatásához hajtsa végre a következő `docker run` parancsot:

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Ez a parancs:

* *Custom Speech – Text* tárolót futtat a tároló rendszerképből.
* 4 CPU-magot és 4 GB memóriát foglal le.
* Betölti a *Custom Speech-szöveg* modellt a Volume bemeneti csatlakoztatásból, például *C:\CustomSpeech*.
* Elérhetővé teszi a 5000-es TCP-portot, és egy pszeudo-TTY-t foglal le a tárolóhoz.
* Letölti a modellt a `ModelId` (ha nem található a kötet csatlakoztatása).
* Ha az egyéni modell korábban le lett töltve, a rendszer figyelmen kívül hagyja a `ModelId`.
* A automatikusan eltávolítja a tárolót a kilépés után. A tároló rendszerképe továbbra is elérhető a gazdaszámítógépen.

# <a name="text-to-speechtabtts"></a>[Szövegfelolvasás](#tab/tts)

A *szöveg-beszéd* tároló futtatásához hajtsa végre a következő `docker run` parancsot.

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Ez a parancs:

* Egy *szöveg-beszéd* tárolót futtat a tároló képéből.
* 2 CPU-magot és egy gigabájt (GB) memóriát foglal le.
* Elérhetővé teszi a 5000-es TCP-portot, és egy pszeudo-TTY-t foglal le a tárolóhoz.
* A automatikusan eltávolítja a tárolót a kilépés után. A tároló rendszerképe továbbra is elérhető a gazdaszámítógépen.

# <a name="custom-text-to-speechtabctts"></a>[Egyéni szöveg – beszéd](#tab/ctts)

Az *egyéni szöveg – beszéd* tároló egy egyéni hangmodellen alapul. Az egyéni modellt az [Egyéni hangportál](https://aka.ms/custom-voice-portal)használatával kell [tanítani](how-to-custom-voice-create-voice.md) . A tároló futtatásához egyéni **hangmodell-azonosító** szükséges. Ez az egyéni hangportál **képzés** lapján található. Az egyéni hangportálon navigáljon a **képzés** lapra, és válassza ki a modellt.
<br>

![Egyéni hang betanítása lap](media/custom-voice/custom-voice-model-training.png)

Szerezze be a Docker Run parancs `ModelId` paraméteréhez argumentumként használandó **modell azonosítóját** .
<br>

![Egyéni hangmodell részletei](media/custom-voice/custom-voice-model-details.png)

A következő táblázat a különböző `docker run` paramétereket és a hozzájuk tartozó leírásokat tartalmazza:

| Paraméter | Leírás |
|---------|---------|
| `{VOLUME_MOUNT}` | A gazdaszámítógép [kötetének csatlakoztatása](https://docs.docker.com/storage/volumes/), amelyet a Docker az egyéni modell megtartására használ. Például a *C:\CustomSpeech* , ahol a *C meghajtó* található a gazdagépen. |
| `{MODEL_ID}` | A Custom Speech **modell azonosítója** az egyéni hangportál **képzés** lapján. |
| `{ENDPOINT_URI}` | A méréshez és a számlázáshoz a végpont szükséges. További információ: a [szükséges paraméterek összegyűjtése](#gathering-required-parameters). |
| `{API_KEY}` | Az API-kulcs megadása kötelező. További információ: a [szükséges paraméterek összegyűjtése](#gathering-required-parameters). |

Az *egyéni szöveg-beszéd* tároló futtatásához hajtsa végre a következő `docker run` parancsot:

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Ez a parancs:

* *Egyéni szöveg-beszéd* tárolót futtat a tároló rendszerképből.
* 2 CPU-magot és egy gigabájt (GB) memóriát foglal le.
* Betölti az *egyéni szöveg-beszéd* modellt a kötet bemeneti csatlakoztatásáról, például *C:\CustomVoice*.
* Elérhetővé teszi a 5000-es TCP-portot, és egy pszeudo-TTY-t foglal le a tárolóhoz.
* Letölti a modellt a `ModelId` (ha nem található a kötet csatlakoztatása).
* Ha az egyéni modell korábban le lett töltve, a rendszer figyelmen kívül hagyja a `ModelId`.
* A automatikusan eltávolítja a tárolót a kilépés után. A tároló rendszerképe továbbra is elérhető a gazdaszámítógépen.

***

> [!IMPORTANT]
> A `Eula`, `Billing`, és `ApiKey` beállítások meg kell adni a tároló futtatásához; ellenkező esetben a tároló nem indul el.  További információkért lásd: [számlázási](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>A tároló előrejelzési végpontjának lekérdezése

| Tároló | Végpont | Protokoll |
|--|--|--|
| Diktálás | `ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1` | WS |
| Custom Speech – szöveg | `ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1` | WS |
| Szövegfelolvasás | `http://localhost:5000/speech/synthesize/cognitiveservices/v1` | HTTP |
| Egyéni szöveg – beszéd | `http://localhost:5000/speech/synthesize/cognitiveservices/v1` | HTTP |

A WSS és a HTTPS protokollok használatával kapcsolatos további információkért lásd: [tárolók biztonsága](../cognitive-services-container-support.md#azure-cognitive-services-container-security).

[!INCLUDE [Query Speech-to-text container endpoint](includes/speech-to-text-container-query-endpoint.md)]

### <a name="text-to-speech-or-custom-text-to-speech"></a>Szöveg – beszéd vagy egyéni szöveg – beszéd

[!INCLUDE [Query Text-to-speech container endpoint](includes/text-to-speech-container-query-endpoint.md)]

### <a name="run-multiple-containers-on-the-same-host"></a>Több tároló futtatása ugyanazon a gazdagépen

Ha több tárolót kíván futtatni a kihelyezett portokkal, ügyeljen arra, hogy az egyes tárolókat egy másik elérhető porton futtassa. Futtassa például az első tárolót az 5000-as porton és a második tárolót a 5001-es porton.

Ezt a tárolót és egy másik, a GAZDAGÉPen futó Azure Cognitive Services tárolót is használhat. Több tároló is lehet ugyanazon a Cognitive Services tárolón.

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>A tároló leállítása

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Hibaelhárítás

A tároló indításakor vagy futtatásakor problémák merülhetnek fel. Használjon kimeneti [csatlakoztatást](speech-container-configuration.md#mount-settings) , és engedélyezze a naplózást. Ezzel lehetővé teszi, hogy a tároló olyan naplófájlokat állítson elő, amelyek hasznosak a hibák elhárítása során.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Számlázás

A beszédfelismerési tárolók számlázási adatokat küldenek az Azure-nak az Azure-fiókjában lévő *beszédfelismerési* erőforrás használatával.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Ezek a beállítások kapcsolatos további információkért lásd: [tárolók konfigurálása](speech-container-configuration.md).

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Összefoglalás

Ebben a cikkben megtanulta a beszédfelismerési tárolók letöltésére, telepítésére és futtatására vonatkozó fogalmakat és munkafolyamatokat. Összegezve:

* A Speech négy Linux-tárolót biztosít a Docker számára, és különböző képességeket ágyaz be:
  * *Beszédfelismerés*
  * *Custom Speech – szöveg*
  * *Szövegfelolvasás*
  * *Egyéni szöveg – beszéd*
* A tároló lemezképeit a rendszer az Azure-beli tároló-beállításjegyzékből tölti le.
* Tárolórendszerképek futtatása a Docker.
* Használhatja a REST API vagy az SDK-t a műveleteknek a beszédfelismerési tárolókban való meghívásához a tároló gazdagép URI azonosítójának megadásával.
* A tárolók példányának létrehozásakor számlázási adatokat kell megadnia.

> [!IMPORTANT]
>  Cognitive Services-tárolók nem teszi lehetővé az Azure-méréshez való csatlakozás nélkül. Az ügyfeleknek kell ahhoz, hogy a tárolókkal való kommunikációhoz mindig a mérési szolgáltatással számlázási adatokat. Cognitive Services-tárolók nem (például a lemezkép vagy az elemezni kívánt szöveget) a vásárlói adatokat küldeni a Microsoftnak.

## <a name="next-steps"></a>További lépések

* A [tárolók konfigurálásának](speech-container-configuration.md) áttekintése konfigurációs beállításokhoz
* Ismerje meg, hogyan [használhatja a Speech Service-tárolókat a Kubernetes és a Helm használatával](speech-container-howto-on-premises.md)
* További [Cognitive Services tárolók](../cognitive-services-container-support.md) használata
