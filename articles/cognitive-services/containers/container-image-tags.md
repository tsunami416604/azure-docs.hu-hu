---
title: Cognitive Services-tárolók rendszerképcímkéi
titleSuffix: Azure Cognitive Services
description: A kognitív szolgáltatás tárolójának összes képcímkéje átfogó felsorolása.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 11/06/2020
ms.author: aahi
ms.openlocfilehash: 0efb278c217ede94f67b47250633622501a24414
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2020
ms.locfileid: "94412544"
---
# <a name="azure-cognitive-services-container-image-tags-and-release-notes"></a>Azure Cognitive Services Container képcímkék és kibocsátási megjegyzések

Az Azure Cognitive Services számos tároló-lemezképet kínál. A tároló-beállításjegyzékek és a megfelelő adattárak a tároló lemezképei között változnak. Mindegyik tároló rendszerképének neve több címkét is kínál. A tároló képcímkéje a tároló rendszerképének verziószámozási mechanizmusa. Ez a cikk átfogó referenciául szolgál az összes Cognitive Services-tároló lemezképének és azok elérhető címkének a listázásához.

> [!TIP]
> A használatakor ügyeljen rá, [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) hogy a tároló beállításjegyzékének, a tárháznak, a tároló rendszerkép nevének és a hozzá tartozó címkének a becsomagolásával, a kis-és **nagybetűk megkülönböztetésével**.

## <a name="anomaly-detector"></a>Anomáliadetektor

Az [anomália-detektor][ad-containers] tárolójának képe a `mcr.microsoft.com` Container Registry szindikátusban található. A `azure-cognitive-services/decision` tárházban található, és neve `anomaly-detector` . A teljes tároló rendszerképének neve: `mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector` .

Ehhez a tároló-rendszerképhez a következő címkék érhetők el. Megtalálhatja a címkék teljes listáját is a [MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/decision/anomaly-detector/tags/list).

# <a name="latest-version"></a>[Legújabb verzió](#tab/current)

| Képcímkék                    | Jegyzetek |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013560003-amd64-preview` |      |

# <a name="previous-versions"></a>[Korábbi verziók](#tab/previous)

| Képcímkék                    | Jegyzetek |
|-------------------------------|:------|
| `1.1.012300001-amd64-preview` |       |

---

## <a name="read-ocr-optical-character-recognition"></a>OCR olvasása (optikai karakterfelismerés)

A [Computer Vision][cv-containers] az OCR-tároló rendszerképe megtalálható a `mcr.microsoft.com` Container Registry syndicateban. A `azure-cognitive-services` tárházban található, és neve `read` . A teljes tároló rendszerképének neve: `mcr.microsoft.com/azure-cognitive-services/vision/read` .

Ehhez a tároló-rendszerképhez a következő címkék érhetők el. Megtalálhatja a címkék teljes listáját is a [MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/vision/read/tags/list).

# <a name="latest-version"></a>[Legújabb verzió](#tab/current)

Kibocsátási megjegyzések a következőhöz `v2.0.013250001-amd64-preview` :

* A tároló memóriahasználat további csökkentése.
* A többrétegű telepítéshez külső gyorsítótár szükséges. Például állítsa be a Redis a gyorsítótárazáshoz.
* Rögzített hiányzó eredmények, ha a Redis cache be van állítva, és `ResultExpirationPeriod` 0 értékre van állítva.
* Távolítsa el a kérelem törzsének korlátozását a 26MB. A tároló mostantól képes fogadni >26MB fájlokat.
* Adja meg az időbélyeget, és készítsen verziót a konzol naplózásához.

| Képcímkék                    | Jegyzetek |
|-------------------------------|:------|
| `latest`                      |       |
| `2.0.013250001-amd64-preview` |  |

# <a name="previous-versions"></a>[Korábbi verziók](#tab/previous)

Kibocsátási megjegyzések a következőhöz: `1.1.013050001-amd64-preview`

* `ReadEngineConfig:ResultExpirationPeriod`A tároló inicializálási konfigurációja hozzáadva, ha a rendszernek meg kell tisztítania az eredmények felismerését. 
    * A beállítás óra, az alapértelmezett érték pedig 48 óra.
    * A beállítás csökkenti a memória használatát az eredmény tárolására, különösen a memóriában tárolt tárolók használata esetén.
    * 1. példa. ReadEngineConfig: ResultExpirationPeriod = 1, a rendszer törli a felismerés eredményének 1 óra a folyamat után.
    * Ha a konfiguráció értéke 0, a rendszer törli az eredmény beolvasása után az elismerés eredményét.

* Rögzített 500 belső kiszolgálóhiba, ha a rendszer érvénytelen képformátumot ad át a rendszernek. Ekkor 400-es hibát ad vissza:

    ```json
    {
        "error": {
        "code": "InvalidImageSize",
        "message": "Image must be between 1024 and 209715200 bytes."
        }
    }
    ```

| Képcímkék                    | Jegyzetek |
|-------------------------------|:------|
| `1.1.013050001-amd64-preview` |       |
| `1.1.011580001-amd64-preview` |       |
| `1.1.009920003-amd64-preview` |       |
| `1.1.009910003-amd64-preview` |       |

---


## <a name="form-recognizer"></a>Form Recognizer

Az [űrlap-felismerő][fr-containers] tároló rendszerképe megtalálható a `mcr.microsoft.com` Container Registry szindikátusban. A `azure-cognitive-services/custom-form` tárházban található, és neve `labeltool` . A teljes tároló rendszerképének neve: `mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool` .

Ehhez a tároló-rendszerképhez a következő címkék érhetők el. Megtalálhatja a címkék teljes listáját is a [MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/custom-form/labeltool/tags/list).

# <a name="latest-version"></a>[Legújabb verzió](#tab/current)

| Képcímkék                    | Jegyzetek |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |


# <a name="previous-versions"></a>[Korábbi verziók](#tab/previous)

| Képcímkék                    | Jegyzetek |
|-------------------------------|:------|
| `1.1.008640001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |

---

## <a name="language-understanding-luis"></a>Language Understanding (LUIS)

A [Luis][lu-containers] -tároló képe a `mcr.microsoft.com` Container Registry szindikátusban található. A `azure-cognitive-services/language` tárházban található, és neve `luis` . A teljes tároló rendszerképének neve: `mcr.microsoft.com/azure-cognitive-services/language/luis` .

Ehhez a tároló-rendszerképhez a következő címkék érhetők el. Megtalálhatja a címkék teljes listáját is a [MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/language/luis/tags/list).

# <a name="latest-version"></a>[Legújabb verzió](#tab/current)

| Képcímkék                    | Jegyzetek |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.012280003-amd64-preview` |       |


# <a name="previous-version"></a>[Előző verzió](#tab/previous)

| Képcímkék                    | Jegyzetek |
|-------------------------------|:------|
| `1.1.012130003-amd64-preview` |       |

---

## <a name="custom-speech-to-text"></a>Custom Speech – szöveg

A [Custom Speech – szöveg][sp-cstt] tároló rendszerképet a `mcr.microsoft.com` Container Registry szindikátusban találhatja meg. A `azure-cognitive-services/speechservices/` tárházban található, és neve `custom-speech-to-text` . A teljes tároló rendszerképének neve: `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text` . Megtalálhatja a címkék teljes listáját is a [MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/custom-speech-to-text/tags/list).


# <a name="latest-version"></a>[Legújabb verzió](#tab/current)

Kiadási Megjegyzés a következőhöz `2.6.0-amd64` :

**Szolgáltatások**
* A phraselist v2 támogatása 
* A kifejezések listáját a következő területi beállítások támogatják:
    * EN-au
    * en-CA
    * en-GB
    * EN-in
    * EN-NZ
    * hu-hu
    * zh-cn
* Egyéni alapmodell letöltése – támogatás. 
    * Használat `BaseModelLocale=<locale>` a `docker run` paranccsal
* Teljes mértékben áttelepítve a .NET 3,1-es verzióra

**Hibajavítások**
* Kijavítva a hiba, ahol a megbízhatósági pontszám mindig 1 Diarization módban
* Áttelepítve a TextAnalytics 3,0 API-ra

Vegye figyelembe, hogy a mellékelt kifejezések listája a tároló képének mérete miatt megnőtt.

| Képcímkék                    | Jegyzetek |
|-------------------------------|:------|
| `latest`                      |       |
| `2.6.0-amd64`                 |       |


# <a name="previous-version"></a>[Előző verzió](#tab/previous)

Kiadási Megjegyzés a következőhöz `2.5.0-amd64` :

**Szolgáltatások**
* Egyéni kiejtés támogatása egyéni modelleken
* Az Azure és az USA kormányzati felhő támogatása

**Hibajavítások**
* A Futtatás nem gyökérszintű felhasználói probléma kijavítása Diarization módban

| Képcímkék                    | Jegyzetek               |
|-------------------------------|:--------------------|
| `2.5.0-amd64`                 |   első GA-verzió    |

---

## <a name="custom-text-to-speech"></a>Egyéni szöveg – beszéd

Az [egyéni szöveg-beszéd][sp-ctts] tároló képe a `mcr.microsoft.com` Container Registry szindikátusban található. A `azure-cognitive-services/speechservices/` tárházban található, és neve `custom-text-to-speech` . A teljes tároló rendszerképének neve: `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech` . Megtalálhatja a címkék teljes listáját is a [MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/custom-text-to-speech/tags/list).


# <a name="latest-version"></a>[Legújabb verzió](#tab/current)

Kiadási Megjegyzés a következőhöz `1.8.0-amd64` :

**Szolgáltatások**
* Teljes mértékben áttelepítve a .NET 3,1-es verzióra

| Képcímkék                    | Jegyzetek |
|-------------------------------|:------|
| `latest`                      |       |
| `1.8.0-amd64`                 |       |


# <a name="previous-version"></a>[Előző verzió](#tab/previous)

Kiadási Megjegyzés a következőhöz `1.7.0-amd64` :

**Jellemző**
* Részben migrálva a .NET 3,1-es verzióra

| Képcímkék                    | Jegyzetek               |
|-------------------------------|:--------------------|
| `1.7.0-amd64`                 |   első GA-verzió    |

---

## <a name="speech-to-text"></a>Diktálás

A [beszédfelismerési][sp-stt] tároló rendszerképe megtalálható a `mcr.microsoft.com` Container Registry szindikátusban. A `azure-cognitive-services/speechservices/` tárházban található, és neve `speech-to-text` . A teljes tároló rendszerképének neve: `mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text` . A címkék teljes listája megtalálható a [MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/speech-to-text/tags/list).

A beszédfelismerési v 2.5.0-ben a képek az USA-beli *közigazgatási Virginia* régióban támogatottak. Ezen régió használatakor használja az *Egyesült Államok kormányának Virginia* számlázási végpontját és API-kulcsait.

# <a name="latest-version"></a>[Legújabb verzió](#tab/current)

Kiadási Megjegyzés a következőhöz `2.6.0-amd64-<locale>` :

**Szolgáltatások**
* Frissítve a legújabb modellekre, és teljes mértékben migrálva a .NET 3,1-es verzióra
* A phraselist v2 támogatása
* A kifejezések listáját a következő területi beállítások támogatják:
    * EN-au
    * en-CA
    * en-GB
    * EN-in
    * EN-NZ
    * hu-hu
    * zh-cn
* Új területi beállítás támogatása `cs-CZ` 
    * A tőkésítés és a központozás jelenleg nem támogatott.

**Hibajavítások**
* Javít egy problémát, amelyben a megbízhatósági pontszámok mindig Diarization módban voltak
* Áttelepítve a TextAnalytics 3,0 API használatával

Vegye figyelembe, hogy a mellékelt kifejezések listája a tároló képének mérete miatt megnőtt. 

| Képcímkék                    | Jegyzetek                                                                                                | 
|-------------------------------|:-----------------------------------------------------------------------------------------------------|
| `latest`                      | Tároló képe a `en-US` területi beállítással.                                                             |
| `2.6.0-amd64-<locale>`        | Cserélje le az értékét `<locale>` az alább felsorolt elérhető területi beállítások egyikére. Például: `2.6.0-amd64-en-us`. |

Ehhez a tárolóhoz a következő területi beállítások állnak rendelkezésre.

| Területi beállítás a v 2.6.0 esetében           | Jegyzetek                                    |
|-----------------------------|:-----------------------------------------|
| `ar-ae`                     | Tároló képe a `ar-AE` területi beállítással. |
| `ar-eg`                     | Tároló képe a `ar-EG` területi beállítással. |
| `ar-kw`                     | Tároló képe a `ar-KW` területi beállítással. |
| `ar-qa`                     | Tároló képe a `ar-QA` területi beállítással. |
| `ar-sa`                     | Tároló képe a `ar-SA` területi beállítással. |
| `ca-es`                     | Tároló képe a `ca-ES` területi beállítással. |
| `cs-cz`                     | Tároló képe a `cs-CZ` területi beállítással. |
| `da-dk`                     | Tároló képe a `da-DK` területi beállítással. |
| `de-de`                     | Tároló képe a `de-DE` területi beállítással. |
| `en-au`                     | Tároló képe a `en-AU` területi beállítással. |
| `en-ca`                     | Tároló képe a `en-CA` területi beállítással. |
| `en-gb`                     | Tároló képe a `en-GB` területi beállítással. |
| `en-in`                     | Tároló képe a `en-IN` területi beállítással. |
| `en-nz`                     | Tároló képe a `en-NZ` területi beállítással. |
| `en-us`                     | Tároló képe a `en-US` területi beállítással. |
| `es-es`                     | Tároló képe a `es-ES` területi beállítással. |
| `es-mx`                     | Tároló képe a `es-MX` területi beállítással. |
| `fi-fi`                     | Tároló képe a `fi-FI` területi beállítással. |
| `fr-ca`                     | Tároló képe a `fr-CA` területi beállítással. |
| `fr-fr`                     | Tároló képe a `fr-FR` területi beállítással. |
| `gu-in`                     | Tároló képe a `gu-IN` területi beállítással. |
| `hi-in`                     | Tároló képe a `hi-IN` területi beállítással. |
| `it-it`                     | Tároló képe a `it-IT` területi beállítással. |
| `ja-jp`                     | Tároló képe a `ja-JP` területi beállítással. |
| `ko-kr`                     | Tároló képe a `ko-KR` területi beállítással. |
| `mr-in`                     | Tároló képe a `mr-IN` területi beállítással. |
| `nb-no`                     | Tároló képe a `nb-NO` területi beállítással. |
| `nl-nl`                     | Tároló képe a `nl-NL` területi beállítással. |
| `pl-pl`                     | Tároló képe a `pl-PL` területi beállítással. |
| `pt-br`                     | Tároló képe a `pt-BR` területi beállítással. |
| `pt-pt`                     | Tároló képe a `pt-PT` területi beállítással. |
| `ru-ru`                     | Tároló képe a `ru-RU` területi beállítással. |
| `sv-se`                     | Tároló képe a `sv-SE` területi beállítással. |
| `ta-in`                     | Tároló képe a `ta-IN` területi beállítással. |
| `te-in`                     | Tároló képe a `te-IN` területi beállítással. |
| `th-th`                     | Tároló képe a `th-TH` területi beállítással. |
| `tr-tr`                     | Tároló képe a `tr-TR` területi beállítással. |
| `zh-cn`                     | Tároló képe a `zh-CN` területi beállítással. |
| `zh-hk`                     | Tároló képe a `zh-HK` területi beállítással. |
| `zh-tw`                     | Tároló képe a `zh-TW` területi beállítással. |


# <a name="previous-version"></a>[Előző verzió](#tab/previous)

Kiadási Megjegyzés a következőhöz `2.5.0-amd64-<locale>` :

**Szolgáltatások**
* Az Azure US government Cloud támogatása

**Hibajavítások**
* Kijavít egy problémát, amely nem root felhasználóként fut Diarization módban

| Képcímkék                  | Jegyzetek                                    |
|-----------------------------|:-----------------------------------------|
| `2.5.0-amd64-<locale>`      | Cserélje le az értékét `<locale>` az alább felsorolt elérhető területi beállítások egyikére. Például: `2.5.0-amd64-en-us`.  |

Ehhez a tárolóhoz a következő területi beállítások állnak rendelkezésre.

| Területi beállítás a v 2.5.0-hoz           | Jegyzetek                                    |
|-----------------------------|:-----------------------------------------|
| `ar-ae`                     | Tároló képe a `ar-AE` területi beállítással. |
| `ar-eg`                     | Tároló képe a `ar-EG` területi beállítással. |
| `ar-kw`                     | Tároló képe a `ar-KW` területi beállítással. |
| `ar-qa`                     | Tároló képe a `ar-QA` területi beállítással. |
| `ar-sa`                     | Tároló képe a `ar-SA` területi beállítással. |
| `ca-es`                     | Tároló képe a `ca-ES` területi beállítással. |
| `da-dk`                     | Tároló képe a `da-DK` területi beállítással. |
| `de-de`                     | Tároló képe a `de-DE` területi beállítással. |
| `en-au`                     | Tároló képe a `en-AU` területi beállítással. |
| `en-ca`                     | Tároló képe a `en-CA` területi beállítással. |
| `en-gb`                     | Tároló képe a `en-GB` területi beállítással. |
| `en-in`                     | Tároló képe a `en-IN` területi beállítással. |
| `en-nz`                     | Tároló képe a `en-NZ` területi beállítással. |
| `en-us`                     | Tároló képe a `en-US` területi beállítással. |
| `es-es`                     | Tároló képe a `es-ES` területi beállítással. |
| `es-mx`                     | Tároló képe a `es-MX` területi beállítással. |
| `fi-fi`                     | Tároló képe a `fi-FI` területi beállítással. |
| `fr-ca`                     | Tároló képe a `fr-CA` területi beállítással. |
| `fr-fr`                     | Tároló képe a `fr-FR` területi beállítással. |
| `gu-in`                     | Tároló képe a `gu-IN` területi beállítással. |
| `hi-in`                     | Tároló képe a `hi-IN` területi beállítással. |
| `it-it`                     | Tároló képe a `it-IT` területi beállítással. |
| `ja-jp`                     | Tároló képe a `ja-JP` területi beállítással. |
| `ko-kr`                     | Tároló képe a `ko-KR` területi beállítással. |
| `mr-in`                     | Tároló képe a `mr-IN` területi beállítással. |
| `nb-no`                     | Tároló képe a `nb-NO` területi beállítással. |
| `nl-nl`                     | Tároló képe a `nl-NL` területi beállítással. |
| `pl-pl`                     | Tároló képe a `pl-PL` területi beállítással. |
| `pt-br`                     | Tároló képe a `pt-BR` területi beállítással. |
| `pt-pt`                     | Tároló képe a `pt-PT` területi beállítással. |
| `ru-ru`                     | Tároló képe a `ru-RU` területi beállítással. |
| `sv-se`                     | Tároló képe a `sv-SE` területi beállítással. |
| `ta-in`                     | Tároló képe a `ta-IN` területi beállítással. |
| `te-in`                     | Tároló képe a `te-IN` területi beállítással. |
| `th-th`                     | Tároló képe a `th-TH` területi beállítással. |
| `tr-tr`                     | Tároló képe a `tr-TR` területi beállítással. |
| `zh-cn`                     | Tároló képe a `zh-CN` területi beállítással. |
| `zh-hk`                     | Tároló képe a `zh-HK` területi beállítással. |
| `zh-tw`                     | Tároló képe a `zh-TW` területi beállítással. |

---

## <a name="text-to-speech"></a>Szövegfelolvasás

A [szöveg-beszéd][sp-tts] tároló képe a `mcr.microsoft.com` Container Registry syndicateban található. A `azure-cognitive-services/speechservices/` tárházban található, és neve `text-to-speech` . A teljes tároló rendszerképének neve: `mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech` .

Ehhez a tároló-rendszerképhez a következő címkék érhetők el. Megtalálhatja a címkék teljes listáját is a [MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/text-to-speech/tags/list).


# <a name="latest-version"></a>[Legújabb verzió](#tab/current)

Kiadási Megjegyzés a következőhöz `1.8.0-amd64-<locale-and-voice>` :

**Jellemző**
* Teljes mértékben áttelepítve a .NET 3,1-es verzióra

| Képcímkék                                  | Jegyzetek                                                                                                         |
|---------------------------------------------|:--------------------------------------------------------------------------------------------------------------|
| `latest`                                    | A tároló képe a `en-US` területi beállítással és a `en-US-AriaRUS` hanggal.                                            | 
| `1.8.0-amd64-<locale-and-voice>`            | Cserélje le az értékét `<locale>` az alább felsorolt elérhető területi beállítások egyikére. Például: `1.8.0-amd64-en-us-ariarus`.  |


| Területi beállítások a v 1.8.0-hoz                          | Jegyzetek                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `ar-eg-hoda`                                | A tároló képe a `ar-EG` területi beállítással és a `ar-EG-Hoda` hanggal.            |
| `ar-sa-naayf`                               | A tároló képe a `ar-SA` területi beállítással és a `ar-SA-Naayf` hanggal.           |
| `bg-bg-ivan`                                | A tároló képe a `bg-BG` területi beállítással és a `bg-BG-Ivan` hanggal.            |
| `ca-es-herenarus`                           | A tároló képe a `ca-ES` területi beállítással és a `ca-ES-HerenaRUS` hanggal.       |
| `cs-cz-jakub`                               | A tároló képe a `cs-CZ` területi beállítással és a `cs-CZ-Jakub` hanggal.           |
| `da-dk-hellerus`                            | A tároló képe a `da-DK` területi beállítással és a `da-DK-HelleRUS` hanggal.        |
| `de-at-michael`                             | A tároló képe a `de-AT` területi beállítással és a `de-AT-Michael` hanggal.         |
| `de-ch-karsten`                             | A tároló képe a `de-CH` területi beállítással és a `de-CH-Karsten` hanggal.         |
| `de-de-hedda`                               | A tároló képe a `de-DE` területi beállítással és a `de-DE-Hedda` hanggal.           |
| `de-de-heddarus`                            | A tároló képe a `de-DE` területi beállítással és a `de-DE-Hedda` hanggal.           |
| `de-de-stefan-apollo`                       | A tároló képe a `de-DE` területi beállítással és a `de-DE-Stefan-Apollo` hanggal.   |
| `el-gr-stefanos`                            | A tároló képe a `el-GR` területi beállítással és a `el-GR-Stefanos` hanggal.        |
| `en-au-catherine`                           | A tároló képe a `en-AU` területi beállítással és a `en-AU-Catherine` hanggal.       |
| `en-au-hayleyrus`                           | A tároló képe a `en-AU` területi beállítással és a `en-AU-HayleyRUS` hanggal.       |
| `en-ca-heatherrus`                          | A tároló képe a `en-CA` területi beállítással és a `en-CA-HeatherRUS` hanggal.      |
| `en-ca-linda`                               | A tároló képe a `en-CA` területi beállítással és a `en-CA-Linda` hanggal.           |
| `en-gb-george-apollo`                       | A tároló képe a `en-GB` területi beállítással és a `en-GB-George-Apollo` hanggal.   |
| `en-gb-hazelrus`                            | A tároló képe a `en-GB` területi beállítással és a `en-GB-HazelRUS` hanggal.        |
| `en-gb-susan-apollo`                        | A tároló képe a `en-GB` területi beállítással és a `en-GB-Susan-Apollo` hanggal.    |
| `en-ie-sean`                                | A tároló képe a `en-IE` területi beállítással és a `en-IE-Sean` hanggal.            |
| `en-in-heera-apollo`                        | A tároló képe a `en-IN` területi beállítással és a `en-IN-Heera-Apollo` hanggal.    |
| `en-in-priyarus`                            | A tároló képe a `en-IN` területi beállítással és a `en-IN-PriyaRUS` hanggal.        |
| `en-in-ravi-apollo`                         | A tároló képe a `en-IN` területi beállítással és a `en-IN-Ravi-Apollo` hanggal.     |
| `en-us-benjaminrus`                         | A tároló képe a `en-US` területi beállítással és a `en-US-BenjaminRUS` hanggal.     |
| `en-us-guy24krus`                           | A tároló képe a `en-US` területi beállítással és a `en-US-Guy24kRUS` hanggal.       |
| `en-us-aria24krus`                          | A tároló képe a `en-US` területi beállítással és a `en-US-Aria24kRUS` hanggal.      |
| `en-us-ariarus`                             | A tároló képe a `en-US` területi beállítással és a `en-US-AriaRUS` hanggal.         |
| `en-us-zirarus`                             | A tároló képe a `en-US` területi beállítással és a `en-US-ZiraRUS` hanggal.         |
| `es-es-helenarus`                           | A tároló képe a `es-ES` területi beállítással és a `es-ES-HelenaRUS` hanggal.       |
| `es-es-laura-apollo`                        | A tároló képe a `es-ES` területi beállítással és a `es-ES-Laura-Apollo` hanggal.    |
| `es-es-pablo-apollo`                        | A tároló képe a `es-ES` területi beállítással és a `es-ES-Pablo-Apollo` hanggal.    |
| `es-mx-hildarus`                            | A tároló képe a `es-MX` területi beállítással és a `es-MX-HildaRUS` hanggal.        |
| `es-mx-raul-apollo`                         | A tároló képe a `es-MX` területi beállítással és a `es-MX-Raul-Apollo` hanggal.     |
| `fi-fi-heidirus`                            | A tároló képe a `fi-FI` területi beállítással és a `fi-FI-HeidiRUS` hanggal.        |
| `fr-ca-caroline`                            | A tároló képe a `fr-CA` területi beállítással és a `fr-CA-Caroline` hanggal.        |
| `fr-ca-harmonierus`                         | A tároló képe a `fr-CA` területi beállítással és a `fr-CA-HarmonieRUS` hanggal.     |
| `fr-ch-guillaume`                           | A tároló képe a `fr-CH` területi beállítással és a `fr-CH-Guillaume` hanggal.       |
| `fr-fr-hortenserus`                         | A tároló képe a `fr-FR` területi beállítással és a `fr-FR-HortenseRUS` hanggal.     |
| `fr-fr-julie-apollo`                        | A tároló képe a `fr-FR` területi beállítással és a `fr-FR-Julie-Apollo` hanggal.    |
| `fr-fr-paul-apollo`                         | A tároló képe a `fr-FR` területi beállítással és a `fr-FR-Paul-Apollo` hanggal.     |
| `he-il-asaf`                                | A tároló képe a `he-IL` területi beállítással és a `he-IL-Asaf` hanggal.            |
| `hi-in-hemant`                              | A tároló képe a `hi-IN` területi beállítással és a `hi-IN-Hemant` hanggal.          |
| `hi-in-kalpana-apollo`                      | A tároló képe a `hi-IN` területi beállítással és a `hi-IN-Kalpana-Apollo` hanggal.  |
| `hi-in-kalpana`                             | A tároló képe a `hi-IN` területi beállítással és a `hi-IN-Kalpana` hanggal.         |
| `hr-hr-matej`                               | A tároló képe a `hr-HR` területi beállítással és a `hr-HR-Matej` hanggal.           |
| `hu-hu-szabolcs`                            | A tároló képe a `hu-HU` területi beállítással és a `hu-HU-Szabolcs` hanggal.        |
| `id-id-andika`                              | A tároló képe a `id-ID` területi beállítással és a `id-ID-Andika` hanggal.          |
| `it-it-cosimo-apollo`                       | A tároló képe a `it-IT` területi beállítással és a `it-IT-Cosimo-Apollo` hanggal.   |
| `it-it-luciarus`                            | A tároló képe a `it-IT` területi beállítással és a `it-IT-LuciaRUS` hanggal.        |
| `ja-jp-ayumi-apollo`                        | A tároló képe a `ja-JP` területi beállítással és a `ja-JP-Ayumi-Apollo` hanggal.    |
| `ja-jp-harukarus`                           | A tároló képe a `ja-JP` területi beállítással és a `ja-JP-HarukaRUS` hanggal.       |
| `ja-jp-ichiro-apollo`                       | A tároló képe a `ja-JP` területi beállítással és a `ja-JP-Ichiro-Apollo` hanggal.   |
| `ko-kr-heamirus`                            | A tároló képe a `ko-KR` területi beállítással és a `ko-KR-HeamiRUS` hanggal.        |
| `ms-my-rizwan`                              | A tároló képe a `ms-MY` területi beállítással és a `ms-MY-Rizwan` hanggal.          |
| `nb-no-huldarus`                            | A tároló képe a `nb-NO` területi beállítással és a `nb-NO-HuldaRUS` hanggal.        |
| `nl-nl-hannarus`                            | A tároló képe a `nl-NL` területi beállítással és a `nl-NL-HannaRUS` hanggal.        |
| `pl-pl-paulinarus`                          | A tároló képe a `pl-PL` területi beállítással és a `pl-PL-PaulinaRUS` hanggal.      |
| `pt-br-daniel-apollo`                       | A tároló képe a `pt-BR` területi beállítással és a `pt-BR-Daniel-Apollo` hanggal.   |
| `pt-br-heloisarus`                          | A tároló képe a `pt-BR` területi beállítással és a `pt-BR-HeloisaRUS` hanggal.      |
| `pt-pt-heliarus`                            | A tároló képe a `pt-PT` területi beállítással és a `pt-PT-HeliaRUS` hanggal.        |
| `ro-ro-andrei`                              | A tároló képe a `ro-RO` területi beállítással és a `ro-RO-Andrei` hanggal.          |
| `ru-ru-ekaterinarus`                        | A tároló képe a `ru-RU` területi beállítással és a `ru-RU-EkaterinaRUS` hanggal.    |
| `ru-ru-irina-apollo`                        | A tároló képe a `ru-RU` területi beállítással és a `ru-RU-Irina-Apollo` hanggal.    |
| `ru-ru-pavel-apollo`                        | A tároló képe a `ru-RU` területi beállítással és a `ru-RU-Pavel-Apollo` hanggal.    |
| `sk-sk-filip`                               | A tároló képe a `sk-SK` területi beállítással és a `sk-SK-Filip` hanggal.           |
| `sl-si-lado`                                | A tároló képe a `sl-SI` területi beállítással és a `sl-SI-Lado` hanggal.            |
| `sv-se-hedvigrus`                           | A tároló képe a `sv-SE` területi beállítással és a `sv-SE-HedvigRUS` hanggal.       |
| `ta-in-valluvar`                            | A tároló képe a `ta-IN` területi beállítással és a `ta-IN-Valluvar` hanggal.        |
| `te-in-chitra`                              | A tároló képe a `te-IN` területi beállítással és a `te-IN-Chitra` hanggal.          |
| `th-th-pattara`                             | A tároló képe a `th-TH` területi beállítással és a `th-TH-Pattara` hanggal.         |
| `tr-tr-sedarus`                             | A tároló képe a `tr-TR` területi beállítással és a `tr-TR-SedaRUS` hanggal.         |
| `vi-vn-an`                                  | A tároló képe a `vi-VN` területi beállítással és a `vi-VN-An` hanggal.              |
| `zh-cn-huihuirus`                           | A tároló képe a `zh-CN` területi beállítással és a `zh-CN-HuihuiRUS` hanggal.       |
| `zh-cn-kangkang-apollo`                     | A tároló képe a `zh-CN` területi beállítással és a `zh-CN-Kangkang-Apollo` hanggal. |
| `zh-cn-yaoyao-apollo`                       | A tároló képe a `zh-CN` területi beállítással és a `zh-CN-Yaoyao-Apollo` hanggal.   |
| `zh-hk-danny-apollo`                        | A tároló képe a `zh-HK` területi beállítással és a `zh-HK-Danny-Apollo` hanggal.    |
| `zh-hk-tracy-apollo`                        | A tároló képe a `zh-HK` területi beállítással és a `zh-HK-Tracy-Apollo` hanggal.    |
| `zh-hk-tracyrus`                            | A tároló képe a `zh-HK` területi beállítással és a `zh-HK-TracyRUS` hanggal.        |
| `zh-tw-hanhanrus`                           | A tároló képe a `zh-TW` területi beállítással és a `zh-TW-HanHanRUS` hanggal.       |
| `zh-tw-yating-apollo`                       | A tároló képe a `zh-TW` területi beállítással és a `zh-TW-Yating-Apollo` hanggal.   |
| `zh-tw-zhiwei-apollo`                       | A tároló képe a `zh-TW` területi beállítással és a `zh-TW-Zhiwei-Apollo` hanggal.   |


# <a name="previous-version"></a>[Előző verzió](#tab/previous)

Kiadási Megjegyzés a következőhöz `1.7.0-amd64-<locale-and-voice>` :

**Jellemző**
* Frissített összetevők a .NET 3,1-es verzióra

| Képcímkék                                  | Jegyzetek                                                                                                         |
|---------------------------------------------|:--------------------------------------------------------------------------------------------------------------|
| `1.7.0-amd64-<locale-and-voice>`            | első GA-verzió. Cserélje le az értékét `<locale>` az alább felsorolt elérhető területi beállítások egyikére. Például: `1.7.0-amd64-en-us-ariarus`.  |


| Területi beállítások a v 1.7.0                          | Jegyzetek                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `ar-eg-hoda`                                | A tároló képe a `ar-EG` területi beállítással és a `ar-EG-Hoda` hanggal.            |
| `ar-sa-naayf`                               | A tároló képe a `ar-SA` területi beállítással és a `ar-SA-Naayf` hanggal.           |
| `bg-bg-ivan`                                | A tároló képe a `bg-BG` területi beállítással és a `bg-BG-Ivan` hanggal.            |
| `ca-es-herenarus`                           | A tároló képe a `ca-ES` területi beállítással és a `ca-ES-HerenaRUS` hanggal.       |
| `cs-cz-jakub`                               | A tároló képe a `cs-CZ` területi beállítással és a `cs-CZ-Jakub` hanggal.           |
| `da-dk-hellerus`                            | A tároló képe a `da-DK` területi beállítással és a `da-DK-HelleRUS` hanggal.        |
| `de-at-michael`                             | A tároló képe a `de-AT` területi beállítással és a `de-AT-Michael` hanggal.         |
| `de-ch-karsten`                             | A tároló képe a `de-CH` területi beállítással és a `de-CH-Karsten` hanggal.         |
| `de-de-hedda`                               | A tároló képe a `de-DE` területi beállítással és a `de-DE-Hedda` hanggal.           |
| `de-de-heddarus`                            | A tároló képe a `de-DE` területi beállítással és a `de-DE-Hedda` hanggal.           |
| `de-de-stefan-apollo`                       | A tároló képe a `de-DE` területi beállítással és a `de-DE-Stefan-Apollo` hanggal.   |
| `el-gr-stefanos`                            | A tároló képe a `el-GR` területi beállítással és a `el-GR-Stefanos` hanggal.        |
| `en-au-catherine`                           | A tároló képe a `en-AU` területi beállítással és a `en-AU-Catherine` hanggal.       |
| `en-au-hayleyrus`                           | A tároló képe a `en-AU` területi beállítással és a `en-AU-HayleyRUS` hanggal.       |
| `en-ca-heatherrus`                          | A tároló képe a `en-CA` területi beállítással és a `en-CA-HeatherRUS` hanggal.      |
| `en-ca-linda`                               | A tároló képe a `en-CA` területi beállítással és a `en-CA-Linda` hanggal.           |
| `en-gb-george-apollo`                       | A tároló képe a `en-GB` területi beállítással és a `en-GB-George-Apollo` hanggal.   |
| `en-gb-hazelrus`                            | A tároló képe a `en-GB` területi beállítással és a `en-GB-HazelRUS` hanggal.        |
| `en-gb-susan-apollo`                        | A tároló képe a `en-GB` területi beállítással és a `en-GB-Susan-Apollo` hanggal.    |
| `en-ie-sean`                                | A tároló képe a `en-IE` területi beállítással és a `en-IE-Sean` hanggal.            |
| `en-in-heera-apollo`                        | A tároló képe a `en-IN` területi beállítással és a `en-IN-Heera-Apollo` hanggal.    |
| `en-in-priyarus`                            | A tároló képe a `en-IN` területi beállítással és a `en-IN-PriyaRUS` hanggal.        |
| `en-in-ravi-apollo`                         | A tároló képe a `en-IN` területi beállítással és a `en-IN-Ravi-Apollo` hanggal.     |
| `en-us-benjaminrus`                         | A tároló képe a `en-US` területi beállítással és a `en-US-BenjaminRUS` hanggal.     |
| `en-us-guy24krus`                           | A tároló képe a `en-US` területi beállítással és a `en-US-Guy24kRUS` hanggal.       |
| `en-us-aria24krus`                          | A tároló képe a `en-US` területi beállítással és a `en-US-Aria24kRUS` hanggal.      |
| `en-us-ariarus`                             | A tároló képe a `en-US` területi beállítással és a `en-US-AriaRUS` hanggal.         |
| `en-us-zirarus`                             | A tároló képe a `en-US` területi beállítással és a `en-US-ZiraRUS` hanggal.         |
| `es-es-helenarus`                           | A tároló képe a `es-ES` területi beállítással és a `es-ES-HelenaRUS` hanggal.       |
| `es-es-laura-apollo`                        | A tároló képe a `es-ES` területi beállítással és a `es-ES-Laura-Apollo` hanggal.    |
| `es-es-pablo-apollo`                        | A tároló képe a `es-ES` területi beállítással és a `es-ES-Pablo-Apollo` hanggal.    |
| `es-mx-hildarus`                            | A tároló képe a `es-MX` területi beállítással és a `es-MX-HildaRUS` hanggal.        |
| `es-mx-raul-apollo`                         | A tároló képe a `es-MX` területi beállítással és a `es-MX-Raul-Apollo` hanggal.     |
| `fi-fi-heidirus`                            | A tároló képe a `fi-FI` területi beállítással és a `fi-FI-HeidiRUS` hanggal.        |
| `fr-ca-caroline`                            | A tároló képe a `fr-CA` területi beállítással és a `fr-CA-Caroline` hanggal.        |
| `fr-ca-harmonierus`                         | A tároló képe a `fr-CA` területi beállítással és a `fr-CA-HarmonieRUS` hanggal.     |
| `fr-ch-guillaume`                           | A tároló képe a `fr-CH` területi beállítással és a `fr-CH-Guillaume` hanggal.       |
| `fr-fr-hortenserus`                         | A tároló képe a `fr-FR` területi beállítással és a `fr-FR-HortenseRUS` hanggal.     |
| `fr-fr-julie-apollo`                        | A tároló képe a `fr-FR` területi beállítással és a `fr-FR-Julie-Apollo` hanggal.    |
| `fr-fr-paul-apollo`                         | A tároló képe a `fr-FR` területi beállítással és a `fr-FR-Paul-Apollo` hanggal.     |
| `he-il-asaf`                                | A tároló képe a `he-IL` területi beállítással és a `he-IL-Asaf` hanggal.            |
| `hi-in-hemant`                              | A tároló képe a `hi-IN` területi beállítással és a `hi-IN-Hemant` hanggal.          |
| `hi-in-kalpana-apollo`                      | A tároló képe a `hi-IN` területi beállítással és a `hi-IN-Kalpana-Apollo` hanggal.  |
| `hi-in-kalpana`                             | A tároló képe a `hi-IN` területi beállítással és a `hi-IN-Kalpana` hanggal.         |
| `hr-hr-matej`                               | A tároló képe a `hr-HR` területi beállítással és a `hr-HR-Matej` hanggal.           |
| `hu-hu-szabolcs`                            | A tároló képe a `hu-HU` területi beállítással és a `hu-HU-Szabolcs` hanggal.        |
| `id-id-andika`                              | A tároló képe a `id-ID` területi beállítással és a `id-ID-Andika` hanggal.          |
| `it-it-cosimo-apollo`                       | A tároló képe a `it-IT` területi beállítással és a `it-IT-Cosimo-Apollo` hanggal.   |
| `it-it-luciarus`                            | A tároló képe a `it-IT` területi beállítással és a `it-IT-LuciaRUS` hanggal.        |
| `ja-jp-ayumi-apollo`                        | A tároló képe a `ja-JP` területi beállítással és a `ja-JP-Ayumi-Apollo` hanggal.    |
| `ja-jp-harukarus`                           | A tároló képe a `ja-JP` területi beállítással és a `ja-JP-HarukaRUS` hanggal.       |
| `ja-jp-ichiro-apollo`                       | A tároló képe a `ja-JP` területi beállítással és a `ja-JP-Ichiro-Apollo` hanggal.   |
| `ko-kr-heamirus`                            | A tároló képe a `ko-KR` területi beállítással és a `ko-KR-HeamiRUS` hanggal.        |
| `ms-my-rizwan`                              | A tároló képe a `ms-MY` területi beállítással és a `ms-MY-Rizwan` hanggal.          |
| `nb-no-huldarus`                            | A tároló képe a `nb-NO` területi beállítással és a `nb-NO-HuldaRUS` hanggal.        |
| `nl-nl-hannarus`                            | A tároló képe a `nl-NL` területi beállítással és a `nl-NL-HannaRUS` hanggal.        |
| `pl-pl-paulinarus`                          | A tároló képe a `pl-PL` területi beállítással és a `pl-PL-PaulinaRUS` hanggal.      |
| `pt-br-daniel-apollo`                       | A tároló képe a `pt-BR` területi beállítással és a `pt-BR-Daniel-Apollo` hanggal.   |
| `pt-br-heloisarus`                          | A tároló képe a `pt-BR` területi beállítással és a `pt-BR-HeloisaRUS` hanggal.      |
| `pt-pt-heliarus`                            | A tároló képe a `pt-PT` területi beállítással és a `pt-PT-HeliaRUS` hanggal.        |
| `ro-ro-andrei`                              | A tároló képe a `ro-RO` területi beállítással és a `ro-RO-Andrei` hanggal.          |
| `ru-ru-ekaterinarus`                        | A tároló képe a `ru-RU` területi beállítással és a `ru-RU-EkaterinaRUS` hanggal.    |
| `ru-ru-irina-apollo`                        | A tároló képe a `ru-RU` területi beállítással és a `ru-RU-Irina-Apollo` hanggal.    |
| `ru-ru-pavel-apollo`                        | A tároló képe a `ru-RU` területi beállítással és a `ru-RU-Pavel-Apollo` hanggal.    |
| `sk-sk-filip`                               | A tároló képe a `sk-SK` területi beállítással és a `sk-SK-Filip` hanggal.           |
| `sl-si-lado`                                | A tároló képe a `sl-SI` területi beállítással és a `sl-SI-Lado` hanggal.            |
| `sv-se-hedvigrus`                           | A tároló képe a `sv-SE` területi beállítással és a `sv-SE-HedvigRUS` hanggal.       |
| `ta-in-valluvar`                            | A tároló képe a `ta-IN` területi beállítással és a `ta-IN-Valluvar` hanggal.        |
| `te-in-chitra`                              | A tároló képe a `te-IN` területi beállítással és a `te-IN-Chitra` hanggal.          |
| `th-th-pattara`                             | A tároló képe a `th-TH` területi beállítással és a `th-TH-Pattara` hanggal.         |
| `tr-tr-sedarus`                             | A tároló képe a `tr-TR` területi beállítással és a `tr-TR-SedaRUS` hanggal.         |
| `vi-vn-an`                                  | A tároló képe a `vi-VN` területi beállítással és a `vi-VN-An` hanggal.              |
| `zh-cn-huihuirus`                           | A tároló képe a `zh-CN` területi beállítással és a `zh-CN-HuihuiRUS` hanggal.       |
| `zh-cn-kangkang-apollo`                     | A tároló képe a `zh-CN` területi beállítással és a `zh-CN-Kangkang-Apollo` hanggal. |
| `zh-cn-yaoyao-apollo`                       | A tároló képe a `zh-CN` területi beállítással és a `zh-CN-Yaoyao-Apollo` hanggal.   |
| `zh-hk-danny-apollo`                        | A tároló képe a `zh-HK` területi beállítással és a `zh-HK-Danny-Apollo` hanggal.    |
| `zh-hk-tracy-apollo`                        | A tároló képe a `zh-HK` területi beállítással és a `zh-HK-Tracy-Apollo` hanggal.    |
| `zh-hk-tracyrus`                            | A tároló képe a `zh-HK` területi beállítással és a `zh-HK-TracyRUS` hanggal.        |
| `zh-tw-hanhanrus`                           | A tároló képe a `zh-TW` területi beállítással és a `zh-TW-HanHanRUS` hanggal.       |
| `zh-tw-yating-apollo`                       | A tároló képe a `zh-TW` területi beállítással és a `zh-TW-Yating-Apollo` hanggal.   |
| `zh-tw-zhiwei-apollo`                       | A tároló képe a `zh-TW` területi beállítással és a `zh-TW-Zhiwei-Apollo` hanggal.   |

---

## <a name="neural-text-to-speech"></a>Neurális szöveg – beszéd

A [neurális szöveg-beszéd][sp-ntts] tároló képe a `mcr.microsoft.com` Container Registry szindikátusban található. A `azure-cognitive-services/speechservices/` tárházban található, és neve `neural-text-to-speech` . A teljes tároló rendszerképének neve: `mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech` .

Ehhez a tároló-rendszerképhez a következő címkék érhetők el. Megtalálhatja a címkék teljes listáját is a [MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/neural-text-to-speech/tags/list).

| Képcímkék                                  | Jegyzetek                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | A tároló képe a `en-US` területi beállítással és a `en-US-AriaNeural` hanggal.      |
| `1.2.0-amd64-<locale-and-voice>-preview`    | Cserélje le az értékét `<locale>` az alább felsorolt elérhető területi beállítások egyikére. Például: `1.2.0-amd64-en-us-arianeural-preview`. |


| a v 1.2.0 előzetes verziójának területi beállítása és Voices           | Jegyzetek                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | A tároló képe a `en-US` területi beállítással és a `en-US-AriaNeural` hanggal.      |
| `de-de-katjaneural-preview`                 | A tároló képe a `de-DE` területi beállítással és a `de-DE-KatjaNeural` hanggal.     |
| `en-au-natashaneural-preview`               | A tároló képe a `en-AU` területi beállítással és a `en-AU-NatashaNeural` hanggal.   |
| `en-ca-claraneural-preview`                 | A tároló képe a `en-CA` területi beállítással és a `en-CA-ClaraNeural` hanggal.     |
| `en-gb-libbyneural-preview`                 | A tároló képe a `en-GB` területi beállítással és a `en-GB-LibbyNeural` hanggal.     |
| `en-gb-mianeural-preview`                   | A tároló képe a `en-GB` területi beállítással és a `en-GB-MiaNeural` hanggal.       |
| `en-us-arianeural-preview`                  | A tároló képe a `en-US` területi beállítással és a `en-US-AriaNeural` hanggal.      |
| `en-us-guyneural-preview`                   | A tároló képe a `en-US` területi beállítással és a `en-US-GuyNeural` hanggal.       |
| `es-es-elviraneural-preview`                | A tároló képe a `es-ES` területi beállítással és a `es-ES-ElviraNeural` hanggal.    |
| `es-mx-dalianeural-preview`                 | A tároló képe a `es-MX` területi beállítással és a `es-MX-DaliaNeural` hanggal.     |
| `fr-ca-sylvieneural-preview`                | A tároló képe a `fr-CA` területi beállítással és a `fr-CA-SylvieNeural` hanggal.    |
| `fr-fr-deniseneural-preview`                | A tároló képe a `fr-FR` területi beállítással és a `fr-FR-DeniseNeural` hanggal.    |
| `it-it-elsaneural-preview`                  | A tároló képe a `it-IT` területi beállítással és a `it-IT-ElsaNeural` hanggal.      |
| `ja-jp-nanamineural-preview`                | A tároló képe a `ja-JP` területi beállítással és a `ja-JP-NanamiNeural` hanggal.    |
| `ko-kr-sunhineural-preview`                 | A tároló képe a `ko-KR` területi beállítással és a `ko-KR-SunHiNeural` hanggal.     |
| `pt-br-franciscaneural-preview`             | A tároló képe a `pt-BR` területi beállítással és a `pt-BR-FranciscaNeural` hanggal. |
| `zh-cn-xiaoxiaoneural-preview`              | A tároló képe a `zh-CN` területi beállítással és a `zh-CN-XiaoxiaoNeural` hanggal.  |

## <a name="speech-language-detection"></a>Beszéd nyelvének felismerése

A [beszédfelismerési nyelvi észlelési][sp-lid] tároló képe a `mcr.microsoft.com` Container Registry szindikátusban található. A `azure-cognitive-services/speechservices/` tárházban található, és neve `language-detection` . A teljes tároló rendszerképének neve: `mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection` .

Ehhez a tároló-rendszerképhez a következő címkék érhetők el. Megtalálhatja a címkék teljes listáját is a [MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/language-detection/tags/list).

| Képcímkék                                  | Jegyzetek                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                       |      |
| `1.1.0-amd64-preview`                       |      |

## <a name="key-phrase-extraction"></a>Kulcskifejezések kinyerése

a tároló rendszerképe megtalálható a `mcr.microsoft.com` Container Registry szindikátusban. A `azure-cognitive-services/textanalytics/` tárházban található, és neve `keyphrase` . A teljes tároló rendszerképének neve: `mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase` .

Ehhez a tároló-rendszerképhez a következő címkék érhetők el. Megtalálhatja a címkék teljes listáját is a [MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/keyphrase/tags/list).

# <a name="latest-version"></a>[Legújabb verzió](#tab/current)


| Képcímkék                    | Jegyzetek |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013570001-amd64` |       |

# <a name="previous-versions"></a>[Korábbi verziók](#tab/previous)

| Képcímkék                    | Jegyzetek |
|-------------------------------|:------|
| `1.1.012840001-amd64` |       |
| `1.1.012830001-amd64`    |       |

---

## <a name="text-language-detection"></a>Szöveg nyelvének felismerése

Az [nyelvfelismerés][ta-la] -tároló rendszerképe megtalálható a `mcr.microsoft.com` Container Registry szindikátusban. A `azure-cognitive-services/textanalytics/` tárházban található, és neve `language` . A teljes tároló rendszerképének neve a (z), `mcr.microsoft.com/azure-cognitive-services/textanalytics/language`


Ehhez a tároló-rendszerképhez a következő címkék érhetők el. Megtalálhatja a címkék teljes listáját is a [MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/language/tags/list).

# <a name="latest-versions"></a>[Legújabb verziók](#tab/current)

| Képcímkék                    | Jegyzetek |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013570001-amd64` | |
   

# <a name="previous-versions"></a>[Korábbi verziók](#tab/previous)


| Képcímkék                    | Jegyzetek |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.012840001-amd64` |   |
| `1.1.012830001-amd64` |   |

---

## <a name="sentiment-analysis"></a>Hangulatelemzés

Az [Hangulatelemzés][ta-se] -tároló rendszerképe megtalálható a `mcr.microsoft.com` Container Registry szindikátusban. A `azure-cognitive-services/textanalytics/` tárházban található, és neve `sentiment` . A teljes tároló rendszerképének neve a (z), `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment`

Ehhez a tároló-rendszerképhez a következő címkék érhetők el. Megtalálhatja a címkék teljes listáját is a [MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/sentiment/tags/list).

| Képcímkék | Jegyzetek                                         |
|------------|:----------------------------------------------|
| `latest`   |                                               |
| `3.0-en`   | Hangulatelemzés v3 (angol)               |
| `3.0-es`   | Hangulatelemzés v3 (spanyol)               |
| `3.0-fr`   | Hangulatelemzés v3 (francia)                |
| `3.0-it`   | Hangulatelemzés v3 (olasz)               |
| `3.0-de`   | Hangulatelemzés v3 (német)                |
| `3.0-zh`   | Hangulatelemzés v3 (kínai – egyszerűsített)  |
| `3.0-zht`  | Hangulatelemzés v3 (kínai – hagyományos) |
| `3.0-ja`   | Hangulatelemzés v3 (Japán)              |
| `3.0-pt`   | Hangulatelemzés v3 (portugál)            |
| `3.0-nl`   | Hangulatelemzés v3 (holland)                 |
| `2.1`    | Hangulatelemzés v2      |

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-stt]: ../speech-service/speech-container-howto.md?tabs=stt
[sp-cstt]: ../speech-service/speech-container-howto.md?tabs=cstt
[sp-tts]: ../speech-service/speech-container-howto.md?tabs=tts
[sp-ctts]: ../speech-service/speech-container-howto.md?tabs=ctts
[sp-ntts]: ../speech-service/speech-container-howto.md?tabs=ntts
[sp-lid]: ../speech-service/speech-container-howto.md?tabs=lid
[ta-kp]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-la]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-se]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment
