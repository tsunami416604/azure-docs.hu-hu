---
title: Speech containers – beszédfelismerési szolgáltatás telepítése
titleSuffix: Azure Cognitive Services
description: Beszédfelismerési tárolók telepítése és futtatása. A beszéd-szöveg szöveggé átmásolja a hangadatfolyamokat valós időben a szöveggé, hogy alkalmazásai, eszközei vagy eszközei képesek legyenek a felhasználásra vagy a megjelenítésre. A szöveg és a beszéd szöveggé alakítja át a bemeneti szöveget az emberi, például a szintetizált beszédbe.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/24/2020
ms.author: aahi
ms.openlocfilehash: be4b7f6a0f8afae9188b1f969956424041e77d9e
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91371677"
---
# <a name="install-and-run-speech-service-containers"></a>Beszédfelismerő szolgáltatás tárolóinak telepítése és futtatása 

A tárolók lehetővé teszik a Speech szolgáltatás API-jainak a saját környezetében való futtatását. A tárolók kiválóan alkalmasak adott biztonsági és adatszabályozási követelményekhez. Ez a cikk azt mutatja be, hogyan tölthet le, telepíthet és futtathat egy Speech-tárolót.

A Speech-tárolókkal az ügyfelek beszédalapú alkalmazások architektúráját hozhatják létre, amely a robusztus felhőbeli képességekhez és a helyi peremhálózathoz is optimalizálva van. Több tároló áll rendelkezésre, amelyek ugyanazt a [díjszabást](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) használják, mint a felhőalapú Azure Speech Services.


> [!IMPORTANT]
> A következő Speech containers mostantól általánosan elérhető:
> * Szabványos beszéd – szöveg
> * Custom Speech – szöveg
> * Normál szöveg – beszéd
> 
> A következő Speech-tárolók a lezárt előzetes verzióban érhetők el.
> * Egyéni szöveg – beszéd
> * Beszédfelismerési Nyelvfelismerés 
> * Neurális szöveg – beszéd
>
> A beszédfelismerési tárolók használatához online kérelmet kell elküldenie, és azt jóvá kell hagynia. További információért tekintse **meg a kérelem jóváhagyása a tároló futtatása** című szakaszt.

| Tároló | Funkciók | Legutóbbi |
|--|--|--|
| Diktálás | Elemzi az érzelmeket, és átírja a folyamatos valós idejű beszédet vagy a Batch hangfelvételeket közbenső eredményekkel.  | 2.5.0 |
| Custom Speech – szöveg | A [Custom Speech portál](https://speech.microsoft.com/customspeech)egyéni modelljét használva folyamatos valós idejű beszédet vagy batch-hangfelvételeket vált ki közbenső eredményekkel rendelkező szövegbe. | 2.5.0 |
| Szövegfelolvasás | A szöveget természetes hangú beszédre konvertálja egyszerű szöveges bevitelsel vagy beszéd szintézis Markup Language (SSML) nyelvvel. | 1.7.0 |
| Egyéni szöveg – beszéd | Ha egyéni modellt használ az [Egyéni hangportálról](https://aka.ms/custom-voice-portal), a szövegeket természetes hangú beszédre alakítja egyszerű szöveges bevitel vagy beszédfelismerési leíró nyelv (SSML) használatával. | 1.7.0 |
| Beszédfelismerési Nyelvfelismerés | A hangfájlokban elhangzott nyelv észlelése. | 1,0 |
| Neurális szöveg – beszéd | A mély neurális hálózati technológiával természetes hangú beszédre alakítja át a szöveget, ami lehetővé teszi a természetes szintetizált beszédek használatát. | 1.2.0 |

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/cognitive-services/), mielőtt hozzákezd.

## <a name="prerequisites"></a>Előfeltételek

A Speech containers használata előtt a következő előfeltételek szükségesek:

| Kötelező | Rendeltetés |
|--|--|
| A Docker-motor | A Docker-motornak telepítve kell lennie a [gazdagépen](#the-host-computer). A Docker csomagokat biztosít a Docker-környezet konfigurálásához [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) és [Linux](https://docs.docker.com/engine/installation/#supported-platforms) rendszereken. A Docker és a tárolók alapszintű ismertetéséért lásd a [Docker felhasználói útmutatóját](https://docs.docker.com/engine/docker-overview/).<br><br> A Docker-t úgy kell konfigurálni, hogy lehetővé tegye a tárolók számára az Azure-ba való kapcsolódást és a számlázási információk küldését. <br><br> **Windows rendszeren a**Docker-t is konfigurálni kell a Linux-tárolók támogatásához.<br><br> |
| A Docker ismerete | Alapvető ismeretekkel kell rendelkeznie a Docker-fogalmakról, például a kibocsátásiegység-forgalmi jegyzékekről, a adattárakról, a tárolók és a tárolók lemezképéről, valamint az alapszintű `docker` parancsokról. |
| Beszédfelismerési erőforrás | A tárolók használatához a következőket kell tennie:<br><br>Egy Azure _Speech_ -erőforrás a társított API-kulcs és végpont URI-azonosító lekéréséhez. Mindkét érték elérhető a Azure Portal **beszédének** áttekintése és a kulcsok oldalain. Mindkettő szükséges a tároló elindításához.<br><br>**{API_KEY}**: a **kulcsok** oldalon található két elérhető erőforrás-kulcs egyike<br><br>**{ENDPOINT_URI}**: az **Áttekintés** lapon megadott végpont |

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

### <a name="container-requirements-and-recommendations"></a>A tárolóra vonatkozó követelmények és javaslatok

Az alábbi táblázat az egyes beszédfelismerési tárolók minimális és ajánlott erőforrás-elosztását ismerteti.

| Tároló | Minimális | Ajánlott |
|-----------|---------|-------------|
| Diktálás | 2 mag, 2 GB memória | 4 mag, 4 GB memória |
| Custom Speech – szöveg | 2 mag, 2 GB memória | 4 mag, 4 GB memória |
| Szövegfelolvasás | 1 mag, 2 GB memória | 2 mag, 3 GB memória |
| Egyéni szöveg – beszéd | 1 mag, 2 GB memória | 2 mag, 3 GB memória |
| Beszédfelismerési Nyelvfelismerés | 1 mag, 1 GB memória | 1 mag, 1 GB memória |
| Neurális szöveg – beszéd | 6 mag, 12 GB memória | 8 mag, 16 GB memória |

* Minden mag legalább 2,6 gigahertz (GHz) vagy gyorsabb lehet.

Az alap és a memória a `--cpus` `--memory` parancs részeként használt és beállításoknak felel meg `docker run` .

> [!NOTE]
> A minimális és ajánlott a Docker korlátain kívüli, *nem* pedig a gazdagép erőforrásai. Például a beszéd-szöveg típusú tárolók a nagyméretű nyelvi modell részei, és azt *javasoljuk* , hogy a teljes fájl elfér a memóriában, ami egy további 4-6 GB. A tárolók első futtatása hosszabb időt is igénybe vehet, mivel a modellek a memóriába kerülnek.

## <a name="request-approval-to-the-run-the-container"></a>Jóváhagyás kérése a tároló futtatásához

Töltse ki és küldje el a [kérelem űrlapját](https://aka.ms/csgate) , hogy hozzáférést Kérjen a tárolóhoz. 

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]


## <a name="get-the-container-image-with-docker-pull"></a>A tároló rendszerképének beolvasása a `docker pull`

A Speech tároló lemezképei a következő Container Registry érhetők el.

# <a name="speech-to-text"></a>[Beszéd – szöveg](#tab/stt)

| Tároló | Adattár |
|-----------|------------|
| Diktálás | `mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text:latest` |

# <a name="custom-speech-to-text"></a>[Custom Speech – szöveg](#tab/cstt)

| Tároló | Adattár |
|-----------|------------|
| Custom Speech – szöveg | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text:latest` |

# <a name="text-to-speech"></a>[Szöveg – beszéd](#tab/tts)

| Tároló | Adattár |
|-----------|------------|
| Szövegfelolvasás | `mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech:latest` |

# <a name="neural-text-to-speech"></a>[Neurális szöveg – beszéd](#tab/ntts)

| Tároló | Adattár |
|-----------|------------|
| Neurális szöveg – beszéd | `mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech:latest` |

# <a name="custom-text-to-speech"></a>[Egyéni szöveg – beszéd](#tab/ctts)

| Tároló | Adattár |
|-----------|------------|
| Egyéni szöveg – beszéd | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech:latest` |

# <a name="speech-language-detection"></a>[Beszédfelismerési Nyelvfelismerés](#tab/lid)

| Tároló | Adattár |
|-----------|------------|
| Beszédfelismerési Nyelvfelismerés | `mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection:latest` |

***

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-speech-containers"></a>Docker-lekérés a beszédfelismerési tárolók számára

# <a name="speech-to-text"></a>[Beszéd – szöveg](#tab/stt)

#### <a name="docker-pull-for-the-speech-to-text-container"></a>Docker-lekérés a beszéd-szöveg tárolóhoz

A [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) parancs használatával letöltheti a tároló rendszerképét a Container Preview beállításjegyzékből.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text:latest
```

> [!IMPORTANT]
> A `latest` címke lekéri a `en-US` területi beállításokat. További területi beállítások: [beszéd – szöveg területi beállítások](#speech-to-text-locales).

#### <a name="speech-to-text-locales"></a>Beszéd – szöveg területi beállítások

A (z) kivételével az összes címke `latest` a következő formátumban van, és megkülönbözteti a kis-és nagybetűket:

```
<major>.<minor>.<patch>-<platform>-<locale>-<prerelease>
```

A következő címke egy példa a formátumra:

```
2.4.0-amd64-en-us-preview
```

A **beszéd-szöveg** típusú tároló összes támogatott területi beállítását lásd: [beszéd – szöveg képcímkék](../containers/container-image-tags.md#speech-to-text).

# <a name="custom-speech-to-text"></a>[Custom Speech – szöveg](#tab/cstt)

#### <a name="docker-pull-for-the-custom-speech-to-text-container"></a>Docker-lekérés a Custom Speech – Text tárolóhoz

A [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) parancs használatával letöltheti a tároló rendszerképét a Container Preview beállításjegyzékből.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text:latest
```

> [!NOTE]
> A `locale` és az `voice` Egyéni beszédfelismerési tárolókat a tároló által betöltött egyéni modell határozza meg.

# <a name="text-to-speech"></a>[Szöveg – beszéd](#tab/tts)

#### <a name="docker-pull-for-the-text-to-speech-container"></a>Docker-lekérés a szöveg-beszéd tárolóhoz

A [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) parancs használatával letöltheti a tároló rendszerképét a Container Preview beállításjegyzékből.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech:latest
```

> [!IMPORTANT]
> A `latest` címke lekéri a `en-US` területi beállításokat és a `ariarus` hangot. További területi beállítások: [szöveg – beszéd területi beállítások](#text-to-speech-locales).

#### <a name="text-to-speech-locales"></a>Szöveg – beszéd területi beállítások

A (z) kivételével az összes címke `latest` a következő formátumban van, és megkülönbözteti a kis-és nagybetűket:

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>-<prerelease>
```

A következő címke egy példa a formátumra:

```
1.6.0-amd64-en-us-ariarus-preview
```

A **szöveg-beszéd** típusú tároló összes támogatott területi beállítása és a hozzájuk tartozó hangok esetében lásd: [szöveg – beszéd képcímkék](../containers/container-image-tags.md#text-to-speech).

> [!IMPORTANT]
> *Szöveg – beszéd http-* bejegyzés létrehozásakor a [Speech szintézis MARKUP Language (SSML)](speech-synthesis-markup.md) üzenethez `voice` attribútummal rendelkező elem szükséges `name` . Az érték a megfelelő tároló területi beállítása és hangja, más néven ["rövid név"](language-support.md#standard-voices). A címke neve például a következő lesz: `latest` `en-US-AriaRUS` .

# <a name="neural-text-to-speech"></a>[Neurális szöveg – beszéd](#tab/ntts)

#### <a name="docker-pull-for-the-neural-text-to-speech-container"></a>Docker-lekérés a neurális szöveg-beszéd tárolóhoz

A [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) parancs használatával letöltheti a tároló rendszerképét a Container Preview beállításjegyzékből.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech:latest
```

> [!IMPORTANT]
> A `latest` címke lekéri a `en-US` területi beállításokat és a `arianeural` hangot. További területi beállítások: [neurális szöveg – beszéd területi beállítások](#neural-text-to-speech-locales).

#### <a name="neural-text-to-speech-locales"></a>Neurális szöveg-beszéd területi beállítások

A (z) kivételével az összes címke `latest` a következő formátumban van, és megkülönbözteti a kis-és nagybetűket:

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>-<prerelease>
```

A következő címke egy példa a formátumra:

```
1.2.0-amd64-en-us-arianeural-preview
```

Az összes támogatott területi beállításhoz és a **neurális szöveg – beszéd** tárolóhoz kapcsolódó hangokért lásd: [neurális szöveg – beszéd képcímkék](../containers/container-image-tags.md#neural-text-to-speech).

> [!IMPORTANT]
> Amikor egy *neurális szöveg-beszéd* http-bejegyzést hoz létre, a [Speech szintézis MARKUP Language (SSML)](speech-synthesis-markup.md) üzenethez `voice` attribútummal rendelkező elem szükséges `name` . Az érték a megfelelő tároló területi beállítása és hangja, más néven ["rövid név"](language-support.md#neural-voices). A címke neve például a következő lesz: `latest` `en-US-AriaNeural` .

# <a name="custom-text-to-speech"></a>[Egyéni szöveg – beszéd](#tab/ctts)

#### <a name="docker-pull-for-the-custom-text-to-speech-container"></a>Docker-lekérés az egyéni szöveg-beszéd tárolóhoz

A [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) parancs használatával letöltheti a tároló rendszerképét a Container Preview beállításjegyzékből.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech:latest
```

> [!NOTE]
> A `locale` és az `voice` Egyéni beszédfelismerési tárolókat a tároló által betöltött egyéni modell határozza meg.

# <a name="speech-language-detection"></a>[Beszédfelismerési Nyelvfelismerés](#tab/lid)

#### <a name="docker-pull-for-the-speech-language-detection-container"></a>Docker-lekérés a Speech Nyelvfelismerés tárolóhoz

A [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) parancs használatával letöltheti a tároló rendszerképét a Container Preview beállításjegyzékből.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection:latest
```

***

## <a name="how-to-use-the-container"></a>A tároló használata

Miután a tároló a [gazdagépen](#the-host-computer)található, a következő eljárással dolgozhat a tárolóval.

1. [Futtassa a tárolót](#run-the-container-with-docker-run)a kötelező számlázási beállításokkal. További [példák](speech-container-configuration.md#example-docker-run-commands) a `docker run` parancsra.
1. [A tároló előrejelzési végpontjának lekérdezése](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>A tároló futtatása a `docker run`

A tároló futtatásához használja a [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) parancsot. A és értékek beszerzésével kapcsolatos részletekért tekintse meg a [szükséges paraméterek összegyűjtését](#gathering-required-parameters) ismertető témakört `{Endpoint_URI}` `{API_Key}` . A [examples](speech-container-configuration.md#example-docker-run-commands) `docker run` parancshoz további példák is elérhetők.

# <a name="speech-to-text"></a>[Beszéd – szöveg](#tab/stt)

A normál *beszéd – szöveg* tároló futtatásához hajtsa végre a következő `docker run` parancsot.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

A parancs a következőket hajtja végre:

* Egy *beszéd-szöveg* tárolót futtat a tároló képéből.
* 4 CPU-magot és 4 GB memóriát foglal le.
* Elérhetővé teszi a 5000-es TCP-portot, és egy pszeudo-TTY-t foglal le a tárolóhoz.
* A automatikusan eltávolítja a tárolót a kilépés után. A tároló rendszerképe továbbra is elérhető a gazdaszámítógépen.


#### <a name="analyze-sentiment-on-the-speech-to-text-output"></a>Az érzelmek elemzése a beszédfelismerési kimenetre 

A beszéd-szöveg típusú tárolóhoz tartozó v 2.2.0-tól kezdve a kimeneten meghívhatja a [hangulat elemzése V3 API](../text-analytics/how-tos/text-analytics-how-to-sentiment-analysis.md) -t. A hangulat elemzésének meghívásához szüksége lesz egy Text Analytics API erőforrás-végpontra. Például: 
* `https://westus2.api.cognitive.microsoft.com/text/analytics/v3.0-preview.1/sentiment`
* `https://localhost:5000/text/analytics/v3.0-preview.1/sentiment`

Ha a felhőben szöveges elemzési végpontot használ, szüksége lesz egy kulcsra. Ha Text Analytics helyileg futtatja, előfordulhat, hogy nem kell megadnia ezt.

A kulcs és a végpont argumentumként kerül átadásra a Speech tárolónak, ahogy az alábbi példában is látható.

```bash
docker run -it --rm -p 5000:5000 \
mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text:latest \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
CloudAI:SentimentAnalysisSettings:TextAnalyticsHost={TEXT_ANALYTICS_HOST} \
CloudAI:SentimentAnalysisSettings:SentimentAnalysisApiKey={SENTIMENT_APIKEY}
```

A parancs a következőket hajtja végre:

* A fenti paranccsal megegyező lépéseket hajt végre.
* Egy Text Analytics API végpontot és kulcsot tárol a hangulati elemzési kérelmek küldéséhez. 


# <a name="custom-speech-to-text"></a>[Custom Speech – szöveg](#tab/cstt)

A *Custom Speech-szöveg* típusú tároló egy egyéni beszédfelismerési modellre támaszkodik. Az egyéni modellt a [Custom Speech Portal](https://speech.microsoft.com/customspeech)használatával kell [betanítani](how-to-custom-speech-train-model.md) .

> [!IMPORTANT]
> A Custom Speech modellt a következő modellek egyikéről kell tanítani:
> * **20181201 (v 3.3 Unified)**
> * **20190520 (v 4.14 Unified)**
> * **20190701 (v 4.17 Unified)**<br>
> ![Custom Speech Train Container Model](media/custom-speech/custom-speech-train-model-container-scoped.png)

A tároló futtatásához az egyéni beszédfelismerési **modell azonosítója** szükséges. Ez a Custom Speech Portal **képzés** lapján található. Az egyéni Speech Portalon navigáljon a **képzés** lapra, és válassza ki a modellt.
<br>

![Egyéni beszédfelismerési oldal](media/custom-speech/custom-speech-model-training.png)

Szerezze be a parancs paraméteréhez argumentumként használandó **modell azonosítóját** `ModelId` `docker run` .
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
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

A parancs a következőket hajtja végre:

* *Custom Speech – Text* tárolót futtat a tároló rendszerképből.
* 4 CPU-magot és 4 GB memóriát foglal le.
* Betölti a *Custom Speech-szöveg* modellt a Volume bemeneti csatlakoztatásból, például *C:\CustomSpeech*.
* Elérhetővé teszi a 5000-es TCP-portot, és egy pszeudo-TTY-t foglal le a tárolóhoz.
* Letölti az adott modellt `ModelId` (ha nem található a kötet csatlakoztatása).
* Ha az egyéni modell korábban le lett töltve, a `ModelId` figyelmen kívül lesz hagyva.
* A automatikusan eltávolítja a tárolót a kilépés után. A tároló rendszerképe továbbra is elérhető a gazdaszámítógépen.

# <a name="text-to-speech"></a>[Szöveg – beszéd](#tab/tts)

A szabványos *szöveg-beszéd* tároló futtatásához hajtsa végre a következő `docker run` parancsot.

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

A parancs a következőket hajtja végre:

* Szabványos *szöveg-beszéd* tárolót futtat a tároló rendszerképből.
* 1 CPU-mag és 2 gigabájt (GB) memóriát foglal le.
* Elérhetővé teszi a 5000-es TCP-portot, és egy pszeudo-TTY-t foglal le a tárolóhoz.
* A automatikusan eltávolítja a tárolót a kilépés után. A tároló rendszerképe továbbra is elérhető a gazdaszámítógépen.

# <a name="neural-text-to-speech"></a>[Neurális szöveg – beszéd](#tab/ntts)

A *neurális szöveg-beszéd* tároló futtatásához hajtsa végre a következő `docker run` parancsot.

```bash
docker run --rm -it -p 5000:5000 --memory 12g --cpus 6 \
mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

A parancs a következőket hajtja végre:

* Egy *neurális szöveg-beszéd* tárolót futtat a tároló képéből.
* 6 CPU-magot és 12 GB memóriát foglal le.
* Elérhetővé teszi a 5000-es TCP-portot, és egy pszeudo-TTY-t foglal le a tárolóhoz.
* A automatikusan eltávolítja a tárolót a kilépés után. A tároló rendszerképe továbbra is elérhető a gazdaszámítógépen.

# <a name="custom-text-to-speech"></a>[Egyéni szöveg – beszéd](#tab/ctts)

Az *egyéni szöveg – beszéd* tároló egy egyéni hangmodellen alapul. Az egyéni modellt az [Egyéni hangportál](https://aka.ms/custom-voice-portal)használatával kell [tanítani](how-to-custom-voice-create-voice.md) . A tároló futtatásához egyéni **hangmodell-azonosító** szükséges. Ez az egyéni hangportál **képzés** lapján található. Az egyéni hangportálon navigáljon a **képzés** lapra, és válassza ki a modellt.
<br>

![Egyéni hang betanítása lap](media/custom-voice/custom-voice-model-training.png)

Szerezze be a Docker Run parancs paraméterének argumentumként használandó **modell azonosítóját** `ModelId` .
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
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

A parancs a következőket hajtja végre:

* *Egyéni szöveg-beszéd* tárolót futtat a tároló rendszerképből.
* 1 CPU-mag és 2 gigabájt (GB) memóriát foglal le.
* Betölti az *egyéni szöveg-beszéd* modellt a kötet bemeneti csatlakoztatásáról, például *C:\CustomVoice*.
* Elérhetővé teszi a 5000-es TCP-portot, és egy pszeudo-TTY-t foglal le a tárolóhoz.
* Letölti az adott modellt `ModelId` (ha nem található a kötet csatlakoztatása).
* Ha az egyéni modell korábban le lett töltve, a `ModelId` figyelmen kívül lesz hagyva.
* A automatikusan eltávolítja a tárolót a kilépés után. A tároló rendszerképe továbbra is elérhető a gazdaszámítógépen.

# <a name="speech-language-detection"></a>[Beszédfelismerési Nyelvfelismerés](#tab/lid)

A *Speech nyelvfelismerés* tároló futtatásához hajtsa végre a következő `docker run` parancsot.

```bash
docker run --rm -it -p 5003:5003 --memory 1g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

A parancs a következőket hajtja végre: 

* Futtat egy beszédfelismerési nyelvi felismerő tárolót a tároló rendszerképből. Jelenleg nem számítunk fel díjat a rendszerkép futtatásához. 
* 1 CPU-mag és 1 gigabájt (GB) memóriát foglal le.
* Elérhetővé teszi a 5003-es TCP-portot, és egy pszeudo-TTY-t foglal le a tárolóhoz.
* A automatikusan eltávolítja a tárolót a kilépés után. A tároló rendszerképe továbbra is elérhető a gazdaszámítógépen.

Ha ezt a tárolót a beszéd-szöveg tárolóval szeretné futtatni, ezt a [Docker-rendszerképet](https://hub.docker.com/r/antsu/on-prem-client)használhatja. Miután mindkét tárolót elindította, használja ezt a Docker Run parancsot a végrehajtáshoz `speech-to-text-with-languagedetection-client` .

```Docker
docker run --rm -v ${HOME}:/root -ti antsu/on-prem-client:latest ./speech-to-text-with-languagedetection-client ./audio/LanguageDetection_en-us.wav --host localhost --lport 5003 --sport 5000
```

> [!NOTE]
> Az egyidejű hívások számának növelése befolyásolhatja a megbízhatóságot és a késést. A nyelvfelismerés esetében legfeljebb 4 egyidejű hívást ajánlunk 1 CPU-val és 1 GB memóriát használva. 2 CPU-t és 2GB memóriát tartalmazó gazdagépek esetén legfeljebb 6 egyidejű hívást ajánlunk.

***

> [!IMPORTANT]
> A `Eula` , a `Billing` és a `ApiKey` beállításokat meg kell adni a tároló futtatásához; egyéb esetben a tároló nem indul el.  További információ: [számlázás](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>A tároló előrejelzési végpontjának lekérdezése

> [!NOTE]
> Ha több tárolót futtat, használjon egyedi portszámot.

| Tárolók | SDK-gazda URL-címe | Protokoll |
|--|--|--|
| Normál beszéd – szöveg és Custom Speech – szöveg | `ws://localhost:5000` | WS |
| Szöveg-beszéd (beleértve a standard, az egyéni és a neurális), a beszédfelismerés nyelvének észlelése | `http://localhost:5000` | HTTP |

A WSS és a HTTPS protokollok használatával kapcsolatos további információkért lásd: [tárolók biztonsága](../cognitive-services-container-support.md#azure-cognitive-services-container-security).

### <a name="speech-to-text-standard-and-custom"></a>Beszéd – szöveg (standard és egyéni)

[!INCLUDE [Query Speech-to-text container endpoint](includes/speech-to-text-container-query-endpoint.md)]

#### <a name="analyze-sentiment"></a>Vélemények elemzése

Ha megadták a Text Analytics API hitelesítő adatait [a tárolóhoz](#analyze-sentiment-on-the-speech-to-text-output), a Speech SDK használatával elküldheti a beszédfelismerési kérelmeket az érzelmek elemzésével. Az API-válaszokat beállíthatja *egyszerű* vagy *részletes* formátum használatára.
> [!NOTE]
> a beszédfelismerési szolgáltatás Python SDK-hoz készült v 1.13-as verziójának észlelési problémája van. A beszédfelismerési szolgáltatás Python SDK-ban használja a v 1.12. x vagy korábbi verzióját.

# <a name="simple-format"></a>[Egyszerű formátum](#tab/simple-format)

Ha úgy szeretné beállítani a beszédfelismerési ügyfelet, hogy egyszerű formátumot használjon, adja hozzá a `"Sentiment"` értéket a értékhez `Simple.Extensions` . Ha ki szeretne választani egy adott Text Analytics modell verzióját, `'latest'` a tulajdonság konfigurációjában cserélje le a elemet `speechcontext-phraseDetection.sentimentAnalysis.modelversion` .

```python
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Simple.Extensions',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentAnalysis.modelversion',
    value='latest',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

`Simple.Extensions` a válasz gyökérelem eredményét fogja visszaadni.

```json
{
   "DisplayText":"What's the weather like?",
   "Duration":13000000,
   "Id":"6098574b79434bd4849fee7e0a50f22e",
   "Offset":4700000,
   "RecognitionStatus":"Success",
   "Sentiment":{
      "Negative":0.03,
      "Neutral":0.79,
      "Positive":0.18
   }
}
```

# <a name="detailed-format"></a>[Részletes formátum](#tab/detailed-format)

Ha úgy szeretné beállítani a beszédfelismerési ügyfelet, hogy egy részletes formátumot használjon, adja hozzá a `"Sentiment"` értéket a `Detailed.Extensions` , a `Detailed.Options` vagy mindkettő értékhez. Ha ki szeretne választani egy adott Text Analytics modell verzióját, `'latest'` a tulajdonság konfigurációjában cserélje le a elemet `speechcontext-phraseDetection.sentimentAnalysis.modelversion` .

```python
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Detailed.Options',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Detailed.Extensions',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentAnalysis.modelversion',
    value='latest',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

`Detailed.Extensions` a válasz legfelső rétegében biztosítja a hangulati eredményt. `Detailed.Options` a válasz rétegében adja meg az eredményt `NBest` . Külön vagy együtt is használhatók.

```json
{
   "DisplayText":"What's the weather like?",
   "Duration":13000000,
   "Id":"6a2aac009b9743d8a47794f3e81f7963",
   "NBest":[
      {
         "Confidence":0.973695,
         "Display":"What's the weather like?",
         "ITN":"what's the weather like",
         "Lexical":"what's the weather like",
         "MaskedITN":"What's the weather like",
         "Sentiment":{
            "Negative":0.03,
            "Neutral":0.79,
            "Positive":0.18
         }
      },
      {
         "Confidence":0.9164971,
         "Display":"What is the weather like?",
         "ITN":"what is the weather like",
         "Lexical":"what is the weather like",
         "MaskedITN":"What is the weather like",
         "Sentiment":{
            "Negative":0.02,
            "Neutral":0.88,
            "Positive":0.1
         }
      }
   ],
   "Offset":4700000,
   "RecognitionStatus":"Success",
   "Sentiment":{
      "Negative":0.03,
      "Neutral":0.79,
      "Positive":0.18
   }
}
```

---

Ha szeretné teljesen letiltani a hangulat elemzését, adjon hozzá egy értéket a következőhöz: `false` `sentimentanalysis.enabled` .

```python
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentanalysis.enabled',
    value='false',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

### <a name="text-to-speech-standard-neural-and-custom"></a>Szöveg-beszéd (standard, neurális és egyéni)

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

További információ ezekről a beállításokról: [tárolók konfigurálása](speech-container-configuration.md).

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Összefoglalás

Ebben a cikkben megtanulta a beszédfelismerési tárolók letöltésére, telepítésére és futtatására vonatkozó fogalmakat és munkafolyamatokat. Összegezve:

* A Speech négy Linux-tárolót biztosít a Docker számára, és különböző képességeket ágyaz be:
  * *Beszéd – szöveg*
  * *Custom Speech – szöveg*
  * *Szöveg – beszéd*
  * *Egyéni szöveg – beszéd*
  * *Neurális szöveg – beszéd*
  * *Beszédfelismerési Nyelvfelismerés*
* A tároló lemezképeit a rendszer az Azure-beli tároló-beállításjegyzékből tölti le.
* A tároló lemezképei a Docker-ben futnak.
* Annak megadása, hogy a REST API (csak szöveg – beszéd) vagy az SDK (beszéd – szöveg vagy szöveg – beszéd) használatával kell-e megadnia a tároló gazda URI-JÁT. 
* A tárolók példányának létrehozásakor számlázási adatokat kell megadnia.

> [!IMPORTANT]
>  Cognitive Services tárolók nem futtathatók az Azure-hoz való csatlakozás nélkül. Az ügyfeleknek engedélyeznie kell, hogy a tárolók a számlázási adatokat mindig a mérési szolgáltatással kommunikáljanak. Cognitive Services tárolók nem küldenek ügyféladatokat (például az elemzett képet vagy szöveget) a Microsoftnak.

## <a name="next-steps"></a>Következő lépések

* A [tárolók konfigurálásának](speech-container-configuration.md) áttekintése konfigurációs beállításokhoz
* Ismerje meg, hogyan [használhatja a Speech Service-tárolókat a Kubernetes és a Helm használatával](speech-container-howto-on-premises.md)
* További [Cognitive Services tárolók](../cognitive-services-container-support.md) használata
