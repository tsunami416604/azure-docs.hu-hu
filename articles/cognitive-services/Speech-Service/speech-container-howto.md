---
title: Telepítse a beszédfelismerési tárolókhoz
titleSuffix: Azure Cognitive Services
description: Telepítheti és futtathatja a beszédfelismerési tárolókhoz. Hang-szöveg transzformációs transcribes audio-adatfolyamokat valós idejű, amelyek az alkalmazások, eszközök és eszközök használata vagy megjelenítendő szöveg. Szöveg-hang transzformációs emberszerű szintetizált alakítja át a bemeneti szöveg.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: e000c034d10efc652f328fa8d1db8d1902fac693
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026117"
---
# <a name="install-and-run-speech-service-containers"></a>Telepítse és futtassa a Speech Service-tárolók

Beszédfelismerési tárolókhoz ügyfeleink hozhat létre egy beszéd architektúra, amely hatékony felhő képességeit és a peremhálózati helye előnyeinek kihasználása érdekében optimalizáltuk. Mostantól támogatjuk a két speech-tárolók **hang-szöveg transzformációs** és **szöveg-hang transzformációs**. 

A két speech tárolók **hang-szöveg transzformációs** és **szöveg-hang transzformációs**. 

|Függvény|Szolgáltatások|Legújabb|
|-|-|--|
|Diktálás| <li>Folyamatos, valós idejű beszédet szöveggé alakít.<li>A hangfelvételekről képes egyszerre több beszédforrást is szöveggé alakítani. <li>Támogatja a köztes eredményeket, a beszéd végének észlelését, az automatikus szövegformázást és a profanitás kitakarását. <li>A [Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS) szolgáltatásra támaszkodva a szöveggé alakított beszédből képes kikövetkeztetni a felhasználói szándékot.\*|1.1.1|
|Szövegfelolvasás| <li>Az írott szöveget természetesnek hangzó beszéddé alakítja. <li>Számos támogatott nyelvhez elérhető több különféle nem és/vagy nyelvjárás. <li>Támogatja az egyszerű szöveges bevitelt és az SSML-t (Speech Synthesis Markup Language). |1.1.0|

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Beszédfelismerési tárolókhoz használata előtt a következő előfeltételeknek kell megfelelnie:

|Szükséges|Cél|
|--|--|
|Docker-motor| A Docker-motor telepítve van szüksége egy [gazdaszámítógép](#the-host-computer). A docker csomagokat biztosít, a Docker-környezet konfigurálása a [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/), és [Linux](https://docs.docker.com/engine/installation/#supported-platforms). A Docker és a tárolók alapfogalmainak ismertetését lásd: a [a Docker áttekintése](https://docs.docker.com/engine/docker-overview/).<br><br> Docker kell konfigurálni, hogy a tárolók számlázási adatok küldése az Azure-ba történő csatlakozáshoz. <br><br> **A Windows**, a Docker Linux-tárolók támogatása is kell konfigurálni.<br><br>|
|Docker-ismeretek | A Docker fő fogalmaira, például a beállításjegyzékek, adattárak, tárolók, és tárolórendszerképeket, valamint alapszintű ismerete alapvető ismeretekkel kell `docker` parancsokat.| 
|Beszéd erőforrás |Ezek a tárolók használatához rendelkeznie kell:<br><br>A _Speech_ Azure-erőforráshoz tartozó számlázási kulcs és számlázási végpont URI azonosítója. Mindkét értéket érhetők el az Azure Portalon **Speech** áttekintése és a kulcsok lapok és a szükséges a tárolót.<br><br>**{BILLING_KEY}** : erőforrás-kulcs<br><br>**{BILLING_ENDPOINT_URI}** : végpont URI-példa: `https://westus.api.cognitive.microsoft.com/sts/v1.0`|

## <a name="request-access-to-the-container-registry"></a>A tároló-beállításjegyzék hozzáférés kérése

Először végezze el, és küldje el a [Cognitive Services beszéd tárolók űrlapot](https://aka.ms/speechcontainerspreview/) hozzáférés kéréséhez a tárolóhoz. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>A számítógép

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Speciális vektor bővítmények támogatása

A **gazdagép** van a számítógépen, amelyen a docker-tárolót. A gazdagépnek támogatnia kell [speciális vektor bővítmények](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). Ez a támogatás a Linux rendszerű gazdagépeken a következő paranccsal ellenőrizheti: 

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```

### <a name="container-requirements-and-recommendations"></a>Tároló-követelményeket és javaslatokat

A következő táblázat ismerteti a minimális és ajánlott processzormagot és memóriát lefoglalni a beszédfelismerési tárolókhoz.

| Tároló | Minimális | Ajánlott |
|-----------|---------|-------------|
|cognitive-services-speech-to-text | 2 mag<br>2 GB memória  | 4 mag<br>4 GB memória  |
|cognitive-services-text-to-speech | 1 mag, 0,5 GB memória| 2 mag, 1 GB memória |

* Egyes maghoz kell lennie legalább 2.6-os gigahertz (GHz) vagy gyorsabb.


Core és a memória felel meg a `--cpus` és `--memory` beállítások, amelyek részeként használhatók a `docker run` parancsot.

**Megjegyzés:**; A minimális és ajánlott alapulnak, Docker-korlátok, minden *nem* a fogadó számítógép-erőforrásokat. Például a hang-szöveg transzformációs tárolók memória térkép részeit nagy nyelvi modell, és van _ajánlott_ , amely a teljes fájlt a memóriába, ami egy további 4 – 6 GB megfelel-e. Ezenkívül az első futtatásakor vagy a tároló hosszabb időt vehet igénybe, mivel a rendszer – lapozható modellek a memóriába.

## <a name="get-the-container-image-with-docker-pull"></a>A tárolórendszerkép beolvasása `docker pull`

A beszédfelismerés tárolórendszerképek érhetők el. 

| Tároló | Adattár |
|-----------|------------|
| cognitive-services-speech-to-text | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest` |
| cognitive-services-text-to-speech | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="language-locale-is-in-container-tag"></a>Nyelvi kódot tároló címke szerepel.

A `latest` címkézése lekéri a `en-us` területi beállítás és `jessarus` hangalapú. 

#### <a name="speech-to-text-locales"></a>Beszéd szöveges területi beállításokhoz

Az összes címke kivételével `latest` a következő formátumban, ahol a `<culture>` azt jelzi, hogy a területibeállítás-tároló:

```
<major>.<minor>.<patch>-<platform>-<culture>-<prerelease>
```

A következő kód a következő példa a formátumra:

```
1.0.0-amd64-en-us-preview
```

A következő táblázat sorolja fel az támogatott nyelveit **hang-szöveg transzformációs** a 1.1.1 a verziót a tároló:

|Nyelvi kódot|Címkék|
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


#### <a name="text-to-speech-locales"></a>Szöveg-beszéd átalakítás területi beállítások

Az összes címke kivételével `latest` a következő formátumban, ahol a `<culture>` azt jelzi, hogy a területi beállítás és a `<voice>` azt jelzi, hogy a tároló a hang:

```
<major>.<minor>.<patch>-<platform>-<culture>-<voice>-<prerelease>
```

A következő kód a következő példa a formátumra:

```
1.0.0-amd64-en-us-jessarus-preview
```

A következő táblázat sorolja fel az támogatott nyelveit **szöveg-hang transzformációs** a az 1.1.0-s verzióját a tároló:

|Nyelvi kódot|Címkék|Támogatott beszédhangot|
|--|--|--|
|kínai|`zh-cn`|huihuirus<br>kangkang-apollo<br>yaoyao-apollo|
|Angol |`en-au`|catherine<br>hayleyrus|
|Angol |`en-gb`|george-apollo<br>hazelrus<br>susan-apollo|
|Angol |`en-in`|heera-apollo<br>priyarus<br>ravi-apollo<br>|
|Angol |`en-us`|jessarus<br>benjaminrus<br>jessa24krus<br>zirarus<br>guy24krus|
|francia|`fr-ca`|Caroline<br>harmonierus|
|francia|`fr-fr`|hortenserus<br>Ágnes-apollo<br>paul-apollo|
|német|`de-de`|hedda<br>heddarus<br>stefan-apollo|
|olasz|`it-it`|cosimo-apollo<br>luciarus|
|japán|`ja-jp`|ayumi-apollo<br>harukarus<br>ichiro-apollo|
|koreai|`ko-kr`|heamirus|
|portugál|`pt-br`|daniel-apollo<br>heloisarus|
|spanyol|`es-es`|elenarus<br>laura-apollo<br>pablo-apollo<br>|
|spanyol|`es-mx`|hildarus<br>raul-apollo|

### <a name="docker-pull-for-the-speech-containers"></a>A beszédfelismerési tárolókhoz docker pull

#### <a name="speech-to-text"></a>Diktálás

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest
```

#### <a name="text-to-speech"></a>Szövegfelolvasás

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest
```

## <a name="how-to-use-the-container"></a>A tároló használata

Ha a tároló a [gazdaszámítógép](#the-host-computer), a következő eljárás használható a tárolóval.

1. [A tároló futtatásához](#run-the-container-with-docker-run), a szükséges, de nem használt számlázási beállításokkal. További [példák](speech-container-configuration.md#example-docker-run-commands) , a `docker run` parancs érhetők el. 
1. [A tároló előrejelzési végpontja lekérdezése](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>A tároló futtatásához `docker run`

Használja a [futtatása docker](https://docs.docker.com/engine/reference/commandline/run/) parancs futtatása bármely három tárolóra. A parancs paraméterei a következők:

**A privát előzetes verzió ideje alatt**, a számlázási beállítások a tároló elindításához érvényesnek kell lennie, de nem használatra lesznek számlázva.

| Helyőrző | Érték |
|-------------|-------|
|{BILLING_KEY} | Ezt a kulcsot a tároló elindításához szolgál, és az Azure portal kulcsok Speech oldalon érhető el.  |
|{BILLING_ENDPOINT_URI} | A számlázási végpont URI azonosítóját az Azure Portalon beszédfelismerés – Áttekintés lapon érhető el.|

Cserélje le ezeket a paramétereket a következő példában a saját értékeire `docker run` parancsot.

### <a name="text-to-speech"></a>Szövegfelolvasás

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} 
```

### <a name="speech-to-text"></a>Diktálás

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} 
```

Ezzel a paranccsal:

* A tároló rendszerképét Speech tárolóban fut
* Foglalja le a 2 processzormagot és memóriát 2 gigabájt (GB)
* Elérhetővé teszi az 5000-es TCP-porton és a egy pszeudo-TTY lefoglalja a tároló
* Után kilép, automatikusan eltávolítja a tárolót. A tároló rendszerképét az továbbra is elérhető az állomáson. 

> [!IMPORTANT]
> A `Eula`, `Billing`, és `ApiKey` beállítások meg kell adni a tároló futtatásához; ellenkező esetben a tároló nem indul el.  További információkért lásd: [számlázási](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>A tároló előrejelzési végpontja lekérdezése

|Tároló|Végpont|
|--|--|
|Diktálás|ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1|
|Szövegfelolvasás|http://localhost:5000/speech/synthesize/cognitiveservices/v1|

### <a name="speech-to-text"></a>Diktálás

A tároló websocket-alapú lekérdezési végpontot API-k, keresztül elért biztosít [Speech services SDK-dokumentáció](https://docs.microsoft.com/azure/cognitive-services/speech-service/).

Alapértelmezés szerint a Speech SDK-t használja az online beszédszolgáltatások. A tároló használatához módosítania az inicializálási metódust. Az alábbi példák.

#### <a name="for-c"></a>AC#

Az Azure-felhő inicializálási hívás használatával módosítani:

```C#
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

a tároló végpontja használatával metódust:

```C#
var config = SpeechConfig.FromEndpoint("ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1", "YourSubscriptionKey");
```

#### <a name="for-python"></a>A Pythonhoz

Az Azure-felhő inicializálási hívás használatáról

```python
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
```

a tároló végpontja használatával metódust:

```python
speech_config = speechsdk.SpeechConfig(subscription=speech_key, endpoint="ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1")
```

### <a name="text-to-speech"></a>Szövegfelolvasás

A tároló található API-k REST-végpontot biztosít [Itt](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#text-to-speech-api) és -példákat [Itt](https://azure.microsoft.com/resources/samples/cognitive-speech-tts/).


[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>Állítsa le a tároló

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Hibaelhárítás

A tároló futtatásakor használ-e a tároló **stdout** és **stderr** a kimeneti információt, amely segítségére lehet a hibaelhárítás indítása, vagy a tároló futtatása közben történik. 

## <a name="billing"></a>Számlázás

Beszéd tárolók Küldés a számlázási adatokat az Azure-ba, a használatával egy _Speech_ erőforrást az Azure-fiókjával. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Ezek a beállítások kapcsolatos további információkért lásd: [tárolók konfigurálása](speech-container-configuration.md).

## <a name="summary"></a>Összegzés

Ebben a cikkben megtanulta, fogalmak és letöltése, telepítése és futtatása a beszédfelismerési tárolókhoz munkafolyamatokat. Összegezve:

* Beszéd biztosít két Linux-tárolók Docker, szöveg és a szöveg-beszéd átalakítás, beszéd fejlécbe foglalja.
* Tárolórendszerképek letöltődnek az Azure-ban a privát tárolóregisztrációs adatbázisból.
* Tárolórendszerképek futtatása a Docker.
* A beszédfelismerési tárolókhoz műveletek hívására adja meg a gazdagép a tároló URI-t használhatja a REST API vagy SDK-val.
* Számlázási adatokat adjon meg egy tároló hárítható el.

> [!IMPORTANT]
>  Cognitive Services-tárolók nem teszi lehetővé az Azure-méréshez való csatlakozás nélkül. Az ügyfeleknek kell ahhoz, hogy a tárolókkal való kommunikációhoz mindig a mérési szolgáltatással számlázási adatokat. Cognitive Services-tárolók nem (például a lemezkép vagy az elemezni kívánt szöveget) a vásárlói adatokat küldeni a Microsoftnak.

## <a name="next-steps"></a>További lépések

* Felülvizsgálat [tárolók konfigurálása](speech-container-configuration.md) a konfigurációs beállítások
* Több [Cognitive Services-tárolók](../cognitive-services-container-support.md)
