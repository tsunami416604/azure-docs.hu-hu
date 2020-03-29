---
title: Beszédtárolók telepítése – Beszédszolgáltatás
titleSuffix: Azure Cognitive Services
description: Beszédtárolók telepítése és futtatása. A beszéd-szöveg üzenet valós időben átírja a hangadatfolyamokat olyan szöveggé, amelyet az alkalmazások, eszközök vagy eszközök használhatnak vagy jeleníthetnek meg. A szövegfelolvasás a bemeneti szöveget emberszerű szintetizált beszédmé alakítja.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: dapine
ms.openlocfilehash: 2beee81bc365d00e59a62cacabacc5f5d6b62a42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474781"
---
# <a name="install-and-run-speech-service-containers-preview"></a>Beszédfelismerési szolgáltatástárolók telepítése és futtatása (előzetes verzió)

A tárolók lehetővé teszik a beszédszolgáltatás API-k egy részének futtatását a saját környezetében. A tárolók kiválóan megfelelnek a speciális biztonsági és adatirányítási követelményeknek. Ebből a cikkből megtudhatja, hogyan tölthet le, telepíthet és futtathat egy beszédfelismerési tárolót.

A beszédtárolók lehetővé teszik az ügyfelek számára, hogy olyan beszédalkalmazás-architektúrát építsenek, amely robusztus felhőképességekre és peremhálózati helységre is optimalizálva van. Négy különböző tároló áll rendelkezésre. A két szabványos tároló a **szöveggé és** **a szövegfelolvasás.** A két egyéni tároló az **egyéni beszédfelismerés** és **az egyéni szövegfelolvasás**.

> [!IMPORTANT]
> Jelenleg minden beszédtároló nyilvános ["gated" előzetes](../cognitive-services-container-support.md#public-gated-preview-container-registry-containerpreviewazurecrio)verzió részeként érhető el. A beszédtárolók általános elérhetősége (GA) felé történő haladásesetén bejelentés történik.

| Függvény | Szolgáltatások | Legújabb |
|--|--|--|
| Diktálás | Folyamatos valós idejű beszéd- vagy kötegelt hangfelvételeket ír át köztes eredményekkel rendelkező szöveggé. | 2.1.1 |
| Egyéni beszédfelismerési szöveg | Az [egyéni beszédportálon](https://speech.microsoft.com/customspeech)lévő egyéni modell használatával folyamatos, valós idejű beszéd- vagy kötegelt hangfelvételeket ír át köztes eredményeket tartalmazó szöveggé. | 2.1.1 |
| Szövegfelolvasás | A szöveget természetes beszédbeszédmé alakítja egyszerű szövegbevitellel vagy ssml-es szövegszintézist tartalmazó nyelvi adatokkal. | 1.3.0 |
| Egyéni szövegfelolvasás | Az [egyéni hangportálon](https://aka.ms/custom-voice-portal)lévő egyéni modell használatával a szöveget természetes hangzású beszédté alakítja egyszerű szövegbevitellel vagy ssml-es szövegszintézissel. | 1.3.0 |

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

A következő előfeltételek a beszédtárolók használata előtt:

| Kötelező | Cél |
|--|--|
| Docker-motor | A Docker-motort egy [gazdaszámítógépen](#the-host-computer)kell telepíteni. A Docker csomagokat biztosít a Docker-környezet konfigurálásához [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) és [Linux](https://docs.docker.com/engine/installation/#supported-platforms) rendszereken. A Docker és a tárolók alapszintű ismertetéséért lásd a [Docker felhasználói útmutatóját](https://docs.docker.com/engine/docker-overview/).<br><br> A Docker-t úgy kell konfigurálni, hogy a tárolók csatlakozhassanak az Azure-hoz, és számlázási adatokat küldjenek az Azure-ba. <br><br> **Windows**rendszeren a Docker-t is be kell állítani linuxos tárolók támogatására.<br><br> |
| A Docker ismerete | Alapvető ismeretekkel kell rendelkeznie a Docker-fogalmakról, például a nyilvántartásokról, az adattárakról, a tárolókról és a tárolórendszerképekről, valamint az alapvető `docker` parancsok ismeretéről. |
| Beszéd forrás | A tárolók használatához a következőkre van szüksége:<br><br>Egy Azure _Speech_ erőforrás a társított API-kulcs és végpont URI beolvasásához. Mindkét érték elérhető az Azure Portal **beszédfelismerési** áttekintése és a kulcsok oldalakon. Mindkettő szükséges a tároló elindításához.<br><br>**{API_KEY}**: A **Kulcsok** lapon elérhető két erőforráskulcs egyike<br><br>**{ENDPOINT_URI}**: Az **Áttekintés** lapon megadott végpont |

## <a name="request-access-to-the-container-registry"></a>Hozzáférés kérése a tároló beállításjegyzékéhez

Töltse ki és küldje el a [Cognitive Services beszédtárolók kérelem űrlapot](https://aka.ms/speechcontainerspreview/) a tárolóhoz való hozzáférés kéréséhez. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>A gazdaszámítógép

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Speciális vektorbővítmény támogatása

Az **állomás** a docker-tárolót futó számítógép. Az *állomásnak támogatnia kell* [a speciális vektorbővítményeket](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). Az AVX2-támogatás a Linux-állomásokon a következő paranccsal ellenőrizheti:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> A gazdaszámítógép az AVX2 támogatásához *szükséges.* A tartály *nem fog* megfelelően működni Az AVX2 támogatás nélkül.

### <a name="container-requirements-and-recommendations"></a>A tárolóra vonatkozó követelmények és ajánlások

Az alábbi táblázat az egyes beszédtárolók erőforrásainak minimális és ajánlott lefoglalását ismerteti.

# <a name="speech-to-text"></a>[Beszédfelismerés](#tab/stt)

| Tároló | Minimális | Ajánlott |
|-----------|---------|-------------|
| Diktálás | 2 mag, 2 GB memória | 4 mag, 4 GB memória |

# <a name="custom-speech-to-text"></a>[Egyéni beszédfelismerési szöveg](#tab/cstt)

| Tároló | Minimális | Ajánlott |
|-----------|---------|-------------|
| Egyéni beszédfelismerési szöveg | 2 mag, 2 GB memória | 4 mag, 4 GB memória |

# <a name="text-to-speech"></a>[Szövegfelolvasás](#tab/tts)

| Tároló | Minimális | Ajánlott |
|-----------|---------|-------------|
| Szövegfelolvasás | 1 mag, 2 GB memória | 2 mag, 3 GB memória |

# <a name="custom-text-to-speech"></a>[Egyéni szövegfelolvasás](#tab/ctts)

| Tároló | Minimális | Ajánlott |
|-----------|---------|-------------|
| Egyéni szövegfelolvasás | 1 mag, 2 GB memória | 2 mag, 3 GB memória |

***

* Minden magnak legalább 2,6 gigahertzesnek (GHz) vagy gyorsabbnak kell lennie.

A mag és `--cpus` a `--memory` memória megfelel nek a `docker run` és a beállításoknak, amelyek a parancs részeként használatosak.

> [!NOTE]
> A minimális és ajánlott docker-korlátokon alapulnak, *nem* a gazdagép erőforrásain. Például a beszéd-szöveg tárolók memórialeképezés részei egy nagy nyelvi modell, és *ajánlott,* hogy a teljes fájl elfér a memóriában, ami egy további 4-6 GB. Emellett bármelyik tároló első futtatása hosszabb időt vehet igénybe, mivel a modellek et a memóriába lapozgatják.

## <a name="get-the-container-image-with-docker-pull"></a>A tárolókép beszerezése`docker pull`

A beszédfelismerési tárolórendszerképek a következő tárolóbeállítás-beállításjegyzékben érhetők el.

# <a name="speech-to-text"></a>[Beszédfelismerés](#tab/stt)

| Tároló | Adattár |
|-----------|------------|
| Diktálás | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest` |

# <a name="custom-speech-to-text"></a>[Egyéni beszédfelismerési szöveg](#tab/cstt)

| Tároló | Adattár |
|-----------|------------|
| Egyéni beszédfelismerési szöveg | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest` |

# <a name="text-to-speech"></a>[Szövegfelolvasás](#tab/tts)

| Tároló | Adattár |
|-----------|------------|
| Szövegfelolvasás | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest` |

# <a name="custom-text-to-speech"></a>[Egyéni szövegfelolvasás](#tab/ctts)

| Tároló | Adattár |
|-----------|------------|
| Egyéni szövegfelolvasás | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest` |

***

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-speech-containers"></a>Docker-lekérésea a beszédtárolókhoz

# <a name="speech-to-text"></a>[Beszédfelismerés](#tab/stt)

#### <a name="docker-pull-for-the-speech-to-text-container"></a>Docker-lekérésea a beszédfelismerési tárolóhoz

A [docker-lekéréses](https://docs.docker.com/engine/reference/commandline/pull/) parancs segítségével töltsön le egy tárolórendszerképet a Container Preview beállításjegyzékből.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest
```

> [!IMPORTANT]
> A `latest` címke lekéri a `en-US` területi beállításokat. További területi beállításokról a [Beszéd-szöveg területi beállításokban.](#speech-to-text-locales)

#### <a name="speech-to-text-locales"></a>Beszéd-szöveg nyelv

Az összes címke, kivéve `latest` a következő formátumú, és a kis- és nagybetűket is figyelembe véve:

```
<major>.<minor>.<patch>-<platform>-<locale>-<prerelease>
```

A következő címke egy példa a formátumra:

```
2.1.1-amd64-en-us-preview
```

A **beszédfelismerési tároló** összes támogatott területi beállításáról a [Beszéd-szöveg képcímkék című témakörben](../containers/container-image-tags.md#speech-to-text)található.

# <a name="custom-speech-to-text"></a>[Egyéni beszédfelismerési szöveg](#tab/cstt)

#### <a name="docker-pull-for-the-custom-speech-to-text-container"></a>Docker-lekérések az egyéni beszédfelismerési tárolóhoz

A [docker-lekéréses](https://docs.docker.com/engine/reference/commandline/pull/) parancs segítségével töltsön le egy tárolórendszerképet a Container Preview beállításjegyzékből.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest
```

> [!NOTE]
> Az `locale` `voice` egyéni beszédtárolók at a tároló által betöltött egyéni modell határozza meg.

# <a name="text-to-speech"></a>[Szövegfelolvasás](#tab/tts)

#### <a name="docker-pull-for-the-text-to-speech-container"></a>Docker-lekérésea a szövegfelolvasó tárolóhoz

A [docker-lekéréses](https://docs.docker.com/engine/reference/commandline/pull/) parancs segítségével töltsön le egy tárolórendszerképet a Container Preview beállításjegyzékből.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest
```

> [!IMPORTANT]
> A `latest` címke húzza `en-US` a `jessarus` területi és hang. További területi beállításokról a [Szövegfelolvasó területi beállítások ban( Szövegfelolvasás) (Szövegfelolvasás) területi beállításokat](#text-to-speech-locales)is megtekintheti.

#### <a name="text-to-speech-locales"></a>Szövegfelolvasó területi beállítások

Az összes címke, kivéve `latest` a következő formátumú, és a kis- és nagybetűket is figyelembe véve:

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>-<prerelease>
```

A következő címke egy példa a formátumra:

```
1.3.0-amd64-en-us-jessarus-preview
```

A **szövegfelolvasó** tároló összes támogatott területi beállításáról és a hozzájuk tartozó hangokról a [Szövegfelolvasó képcímkék című témakörben található.](../containers/container-image-tags.md#text-to-speech)

> [!IMPORTANT]
> *Szabványos szövegfelolvasó* HTTP POST létrehozásához a [beszédszintetizáló nyelv (SSML)](speech-synthesis-markup.md) `voice` üzenethez `name` attribútummal rendelkező elemre van szükség. Az érték a megfelelő tároló területi beállítás és hang, más néven a ["rövid név"](language-support.md#standard-voices). A `latest` címkének például a `en-US-JessaRUS`hangja a .

# <a name="custom-text-to-speech"></a>[Egyéni szövegfelolvasás](#tab/ctts)

#### <a name="docker-pull-for-the-custom-text-to-speech-container"></a>Docker-lekéréses az egyéni szövegfelolvasó tárolóhoz

A [docker-lekéréses](https://docs.docker.com/engine/reference/commandline/pull/) parancs segítségével töltsön le egy tárolórendszerképet a Container Preview beállításjegyzékből.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest
```

> [!NOTE]
> Az `locale` `voice` egyéni beszédtárolók at a tároló által betöltött egyéni modell határozza meg.

***

## <a name="how-to-use-the-container"></a>A tároló használata

Miután a tároló a [gazdaszámítógépen](#the-host-computer)van, használja a következő eljárást a tárolóval való munkához.

1. [Futtassa a tárolót](#run-the-container-with-docker-run)a szükséges számlázási beállításokkal. További [példák](speech-container-configuration.md#example-docker-run-commands) a `docker run` parancs állnak rendelkezésre.
1. [Kérdezze meg a tároló előrejelzési végpontját.](#query-the-containers-prediction-endpoint)

## <a name="run-the-container-with-docker-run"></a>Futtassa a tartályt`docker run`

Használja a [docker run](https://docs.docker.com/engine/reference/commandline/run/) parancsot a tároló futtatásához. Tekintse meg [a szükséges paraméterek összegyűjtését](#gathering-required-parameters) `{API_Key}` a be- és értékek `{Endpoint_URI}` begyűjtésének részleteiről. A `docker run` parancstovábbi [példái](speech-container-configuration.md#example-docker-run-commands) is elérhetők.

# <a name="speech-to-text"></a>[Beszédfelismerés](#tab/stt)

A *beszédfelismerési tároló futtatásához* hajtsa végre a következő `docker run` parancsot.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

A parancs a következőket hajtja végre:

* *Beszédfelismerési tárolót* futtat a tárolórendszerképből.
* 4 processzormagot és 4 gigabájt (GB) memóriát foglal le.
* Elérhetővé teszi az 5000-es TCP-portot, és egy pszeudo-TTY-t rendel a tárolóhoz.
* A tároló automatikus eltávolítása kilépés után. A tárolórendszerkép továbbra is elérhető a gazdaszámítógépen.

# <a name="custom-speech-to-text"></a>[Egyéni beszédfelismerési szöveg](#tab/cstt)

Az *egyéni beszédfelismerési tároló* egy egyéni beszédmodellre támaszkodik. Az egyéni modellt be kell [tanítani](how-to-custom-speech-train-model.md) az [egyéni beszédportál](https://speech.microsoft.com/customspeech)használatával.

> [!IMPORTANT]
> Az egyéni beszédmodellt a következő modellverziók egyikéből kell betanítani:
> * **20181201 (3.3-as évvel)**
> * **20190520 (v4.14 Unified)**
> * **20190701 (v4.17 Unified)**<br>
> ![Egyéni beszédbetanítási tároló modell](media/custom-speech/custom-speech-train-model-container-scoped.png)

Az egyéni **beszédmodell-azonosító** szükséges a tároló futtatásához. Az egyéni beszédportál **Betanítás idomítási** lapján található. Az egyéni beszédportálon keresse meg a **Betanítás** lapot, és válassza ki a modellt.
<br>

![Egyéni beszédoktatás lap](media/custom-speech/custom-speech-model-training.png)

Szerezze be a `ModelId` `docker run` parancs paraméterének argumentumaként használandó **modellazonosítót.**
<br>

![Egyéni beszédmodell részletei](media/custom-speech/custom-speech-model-details.png)

A következő táblázat a `docker run` különböző paramétereket és azok megfelelő leírását mutatja be:

| Paraméter | Leírás |
|---------|---------|
| `{VOLUME_MOUNT}` | A gazdaszámítógép [kötetcsatlakoztatása](https://docs.docker.com/storage/volumes/), amelydocker az egyéni modell megőrzéséhez használja. Például a *C:\CustomSpeech,* ahol a *C meghajtó* található a gazdagépen. |
| `{MODEL_ID}` | Az egyéni **beszédmodell-azonosító** az egyéni beszédportál **Betanításlapjáról.** |
| `{ENDPOINT_URI}` | A végpont a méréshez és a számlázáshoz szükséges. További információ: [A szükséges paraméterek összegyűjtése](#gathering-required-parameters). |
| `{API_KEY}` | Az API-kulcs szükséges. További információ: [A szükséges paraméterek összegyűjtése](#gathering-required-parameters). |

Az *egyéni beszédfelismerési tároló futtatásához* hajtsa végre a következő `docker run` parancsot:

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

A parancs a következőket hajtja végre:

* Egy *egyéni beszéd-szöveg* tárolót futtat a tárolórendszerképből.
* 4 processzormagot és 4 gigabájt (GB) memóriát foglal le.
* Betölti az *egyéni beszédfelismerési modellt* a kötetbemeneti csatlakoztatásból, például *C:\CustomSpeech*.
* Elérhetővé teszi az 5000-es TCP-portot, és egy pszeudo-TTY-t rendel a tárolóhoz.
* Letölti a modellt a `ModelId` (ha nem található meg a kötettartón).
* Ha az egyéni modellt `ModelId` korábban letöltötték, a program figyelmen kívül hagyja.
* A tároló automatikus eltávolítása kilépés után. A tárolórendszerkép továbbra is elérhető a gazdaszámítógépen.

# <a name="text-to-speech"></a>[Szövegfelolvasás](#tab/tts)

A *szövegfelolvasó* tároló futtatásához hajtsa végre a következő `docker run` parancsot.

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

A parancs a következőket hajtja végre:

* Egy *szövegfelolvasó* tárolót futtat a tárolórendszerképből.
* 2 processzormagot és egy gigabájt (GB) memóriát foglal le.
* Elérhetővé teszi az 5000-es TCP-portot, és egy pszeudo-TTY-t rendel a tárolóhoz.
* A tároló automatikus eltávolítása kilépés után. A tárolórendszerkép továbbra is elérhető a gazdaszámítógépen.

# <a name="custom-text-to-speech"></a>[Egyéni szövegfelolvasás](#tab/ctts)

Az *egyéni szövegfelolvasó* tároló egy egyéni hangmodellre támaszkodik. Az egyéni modellt be kell [tanítani](how-to-custom-voice-create-voice.md) az [egyéni hangportál](https://aka.ms/custom-voice-portal)használatával. Az egyéni hang **modellazonosító** ja a tároló futtatásához szükséges. Az egyéni hangportál **Edzés** oldalán található. Az egyéni hangportálon keresse meg a **Betanítás** lapot, és válassza ki a modellt.
<br>

![Egyéni hangbetanítási lap](media/custom-voice/custom-voice-model-training.png)

Szerezze be a docker run parancs `ModelId` paraméterének argumentumaként használandó **modellazonosítót.**
<br>

![Egyéni hangmodell részletei](media/custom-voice/custom-voice-model-details.png)

A következő táblázat a `docker run` különböző paramétereket és azok megfelelő leírását mutatja be:

| Paraméter | Leírás |
|---------|---------|
| `{VOLUME_MOUNT}` | A gazdaszámítógép [kötetcsatlakoztatása](https://docs.docker.com/storage/volumes/), amelydocker az egyéni modell megőrzéséhez használja. Például a *C:\CustomSpeech,* ahol a *C meghajtó* található a gazdagépen. |
| `{MODEL_ID}` | Az egyéni **beszédmodell-azonosító** az egyéni hangportál **betanítási** lapjáról. |
| `{ENDPOINT_URI}` | A végpont a méréshez és a számlázáshoz szükséges. További információ: [A szükséges paraméterek összegyűjtése](#gathering-required-parameters). |
| `{API_KEY}` | Az API-kulcs szükséges. További információ: [A szükséges paraméterek összegyűjtése](#gathering-required-parameters). |

Az *egyéni szövegfelolvasó* tároló futtatásához hajtsa végre a következő `docker run` parancsot:

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

A parancs a következőket hajtja végre:

* Egy *egyéni szövegfelolvasó* tárolót futtat a tárolórendszerképből.
* 2 processzormagot és egy gigabájt (GB) memóriát foglal le.
* Betölti az *egyéni szövegfelolvasó modellt* a bemeneti csatlakoztatóból, például *C:\CustomVoice*.
* Elérhetővé teszi az 5000-es TCP-portot, és egy pszeudo-TTY-t rendel a tárolóhoz.
* Letölti a modellt a `ModelId` (ha nem található meg a kötettartón).
* Ha az egyéni modellt `ModelId` korábban letöltötték, a program figyelmen kívül hagyja.
* A tároló automatikus eltávolítása kilépés után. A tárolórendszerkép továbbra is elérhető a gazdaszámítógépen.

***

> [!IMPORTANT]
> A `Eula` `Billing`, `ApiKey` a lehetőséget és a beállításokat meg kell adni a tároló futtatásához; ellenkező esetben a tároló nem indul el.  További információ: [Billing](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>A tároló előrejelzési végpontjának lekérdezése

| Containers | SDK-állomás URL-címe | Protocol (Protokoll) |
|--|--|--|
| Beszéd-szöveg és egyéni beszéd-szöveg | `ws://localhost:5000` | WS |
| Szövegfelolvasás és egyéni szövegfelolvasás | `http://localhost:5000` | HTTP |

A WSS és https protokollok használatáról a [tárolóbiztonság](../cognitive-services-container-support.md#azure-cognitive-services-container-security)című témakörben talál további információt.

[!INCLUDE [Query Speech-to-text container endpoint](includes/speech-to-text-container-query-endpoint.md)]

### <a name="text-to-speech-or-custom-text-to-speech"></a>Szövegfelolvasó vagy egyéni szövegfelolvasás

[!INCLUDE [Query Text-to-speech container endpoint](includes/text-to-speech-container-query-endpoint.md)]

### <a name="run-multiple-containers-on-the-same-host"></a>Több tároló futtatása ugyanazon a gazdagépen

Ha több tárolót kíván futtatni a kitett portokkal, győződjön meg arról, hogy minden tárolót más elérhető porttal futtat. Futtassa például az első tárolót az 5000-es porton, a másodikat pedig az 5001-es porton.

Ez a tároló és egy másik Azure Cognitive Services-tároló együtt fut. Ugyanannak a Cognitive Services-tárolónak több tárolója is futtatható.

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>A tároló leállítása

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Hibaelhárítás

A tároló indításakor vagy futtatásakor problémák léphetnek fel. Használjon kimeneti [csatlakoztatást,](speech-container-configuration.md#mount-settings) és engedélyezze a naplózást. Ezzel lehetővé teszi, hogy a tároló olyan naplófájlokat hozzon létre, amelyek hasznosak a problémák elhárításakor.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Számlázás

A beszédtárolók számlázási adatokat küldenek az Azure-ba egy *beszédfelismerési* erőforrás használatával az Azure-fiókjában.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Ezekről a beállításokról a [Tárolók konfigurálása](speech-container-configuration.md)című témakörben talál további információt.

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Összefoglalás

Ebben a cikkben megtanulta a beszédtárolók letöltésével, telepítésével és futtatásával kapcsolatos fogalmakat és munkafolyamatokat. Összegezve:

* A Speech négy Linux-tárolót biztosít a Docker számára, amelyek különböző képességeket foglalnak magukban:
  * *Beszédfelismerés*
  * *Egyéni beszédfelismerési szöveg*
  * *Szövegfelolvasás*
  * *Egyéni szövegfelolvasás*
* A tárolórendszerképek az Azure-beli tárolóbeállításból töltődnek le.
* A tárolórendszerképek a Dockerben futnak.
* Akár a REST API-t (csak szövegfelolvasás) vagy az SDK-t (beszédfelismerés vagy szövegfelolvasó) használja, adja meg a tároló gazdaURI-ját. 
* A tároló példányosításakor számlázási adatokat kell megadnia.

> [!IMPORTANT]
>  A Cognitive Services-tárolók nem rendelkeznek licenccel anélkül, hogy az Azure-hoz csatlakoznának a méréshez. Az ügyfeleknek lehetővé kell tenni, hogy a tárolók mindig kommunikálják a számlázási adatokat a mérési szolgáltatással. A Cognitive Services-tárolók nem küldenek ügyféladatokat (például az elemzett képet vagy szöveget) a Microsoftnak.

## <a name="next-steps"></a>További lépések

* A konfigurációs beállítások [konfigurálásának](speech-container-configuration.md) áttekintése
* A [Beszédszolgáltatás-tárolók használata a Kubernetes és a Helm](speech-container-howto-on-premises.md) használatával
* További [Cognitive Services-tárolók használata](../cognitive-services-container-support.md)
