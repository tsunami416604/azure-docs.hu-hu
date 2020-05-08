---
title: Cognitive Services-tárolók rendszerképcímkéi
titleSuffix: Azure Cognitive Services
description: A kognitív szolgáltatás tárolójának összes képcímkéje átfogó felsorolása.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 850d7b9732ad547d502b042e7eaad5d3b471030e
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82926000"
---
# <a name="azure-cognitive-services-container-image-tags"></a>Azure Cognitive Services tároló képcímkék

Az Azure Cognitive Services számos tároló-lemezképet kínál. A tároló-beállításjegyzékek és a megfelelő adattárak a tároló lemezképei között változnak. Mindegyik tároló rendszerképének neve több címkét is kínál. A tároló képcímkéje a tároló rendszerképének verziószámozási mechanizmusa. Ez a cikk átfogó referenciául szolgál az összes Cognitive Services-tároló lemezképének és azok elérhető címkének a listázásához.

> [!TIP]
> A használatakor [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/)ügyeljen rá, hogy a tároló beállításjegyzékének, a tárháznak, a tároló rendszerkép nevének és a hozzá tartozó címkének a becsomagolásával, a kis-és **nagybetűk megkülönböztetésével**.

## <a name="anomaly-detector"></a>Anomaly Detector

Az [anomália-detektor][ad-containers] tárolójának képe megtalálható a `containerpreview.azurecr.io` tároló beállításjegyzékében. A `microsoft` tárházban található, és neve `cognitive-services-anomaly-detector`. A teljes tároló rendszerképének neve: `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector`.

Ez a tároló-rendszerkép a következő címkéket tartalmazhatja:

| Képcímkék                    | Megjegyzések |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008500001-amd64-preview` |       |

## <a name="computer-vision"></a>Számítógépes látástechnológia

Az [Computer Vision][cv-containers] -tároló rendszerképe megtalálható a `containerpreview.azurecr.io` tároló beállításjegyzékében. A `microsoft` tárházban található, és neve `cognitive-services-read`. A teljes tároló rendszerképének neve: `containerpreview.azurecr.io/microsoft/cognitive-services-read`.

Ez a tároló-rendszerkép a következő címkéket tartalmazhatja:

| Képcímkék                    | Megjegyzések |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.011580001-amd64-preview` |       |
| `1.1.009920003-amd64-preview` |       |
| `1.1.009910003-amd64-preview` |       |

## <a name="face"></a>Arcfelismerés

Az [arc][fa-containers] -tároló képe megtalálható a `containerpreview.azurecr.io` tároló beállításjegyzékében. A `microsoft` tárházban található, és neve `cognitive-services-face`. A teljes tároló rendszerképének neve: `containerpreview.azurecr.io/microsoft/cognitive-services-face`.

Ez a tároló-rendszerkép a következő címkéket tartalmazhatja:

| Képcímkék                    | Megjegyzések |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008710001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |
| `1.1.006490002-amd64-preview` |       |
| `1.0.005940002-amd64-preview` |       |
| `1.0.005550001-amd64-preview` |       |

## <a name="form-recognizer"></a>Form Recognizer

Az [űrlap-felismerő][fr-containers] tároló rendszerképe megtalálható a `containerpreview.azurecr.io` tároló beállításjegyzékében. A `microsoft` tárházban található, és neve `cognitive-services-form-recognizer`. A teljes tároló rendszerképének neve: `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer`.

Ez a tároló-rendszerkép a következő címkéket tartalmazhatja:

| Képcímkék                    | Megjegyzések |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008640001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |

## <a name="language-understanding-luis"></a>Language Understanding (LUIS)

A [Luis][lu-containers] -tároló képe a `mcr.microsoft.com` Container Registry szindikátusban található. A `azure-cognitive-services` tárházban található, és neve `luis`. A teljes tároló rendszerképének neve: `mcr.microsoft.com/azure-cognitive-services/luis`.

Ez a tároló-rendszerkép a következő címkéket tartalmazhatja:

| Képcímkék                    | Megjegyzések |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.010330004-amd64-preview` |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008710001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.008010002-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.007020001-amd64-preview` |       |

## <a name="custom-speech-to-text"></a>Custom Speech – szöveg

A `containerpreview.azurecr.io` tároló beállításjegyzékében a [Custom Speech – Text][sp-cstt] tároló képe található. A `microsoft` tárházban található, és neve `cognitive-services-custom-speech-to-text`. A teljes tároló rendszerképének neve: `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text`.

Ez a tároló-rendszerkép a következő címkéket tartalmazhatja:

| Képcímkék            | Megjegyzések |
|-----------------------|:------|
| `latest`              |       |
| `2.2.0-amd64-preview` |       |
| `2.1.1-amd64-preview` |       |
| `2.1.0-amd64-preview` |       |
| `2.0.2-amd64-preview` |       |
| `2.0.0-amd64-preview` |       |

## <a name="custom-text-to-speech"></a>Egyéni szöveg – beszéd

Az [egyéni szöveg-beszéd][sp-ctts] tároló képe a `containerpreview.azurecr.io` Container registryben található. A `microsoft` tárházban található, és neve `cognitive-services-custom-text-to-speech`. A teljes tároló rendszerképének neve: `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech`.

Ez a tároló-rendszerkép a következő címkéket tartalmazhatja:

| Képcímkék            | Megjegyzések |
|-----------------------|:------|
| `latest`              |       |
| `1.3.0-amd64-preview` |       |

## <a name="speech-to-text"></a>Diktálás

A [beszédfelismerési][sp-stt] tároló rendszerképe a `containerpreview.azurecr.io` tároló beállításjegyzékében található. A `microsoft` tárházban található, és neve `cognitive-services-speech-to-text`. A teljes tároló rendszerképének neve: `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text`.

Ez a tároló-rendszerkép a következő címkéket tartalmazhatja:

| Képcímkék                  | Megjegyzések                                    |
|-----------------------------|:-----------------------------------------|
| `latest`                    | Tároló képe a `en-US` területi beállítással. |
| `2.2.0-amd64-ar-ae-preview` | Tároló képe a `ar-AE` területi beállítással. |
| `2.2.0-amd64-ar-eg-preview` | Tároló képe a `ar-EG` területi beállítással. |
| `2.2.0-amd64-ar-kw-preview` | Tároló képe a `ar-KW` területi beállítással. |
| `2.2.0-amd64-ar-qa-preview` | Tároló képe a `ar-QA` területi beállítással. |
| `2.2.0-amd64-ar-sa-preview` | Tároló képe a `ar-SA` területi beállítással. |
| `2.2.0-amd64-ca-es-preview` | Tároló képe a `ca-ES` területi beállítással. |
| `2.2.0-amd64-da-dk-preview` | Tároló képe a `da-DK` területi beállítással. |
| `2.2.0-amd64-de-de-preview` | Tároló képe a `de-DE` területi beállítással. |
| `2.2.0-amd64-en-au-preview` | Tároló képe a `en-AU` területi beállítással. |
| `2.2.0-amd64-en-ca-preview` | Tároló képe a `en-CA` területi beállítással. |
| `2.2.0-amd64-en-gb-preview` | Tároló képe a `en-GB` területi beállítással. |
| `2.2.0-amd64-en-in-preview` | Tároló képe a `en-IN` területi beállítással. |
| `2.2.0-amd64-en-nz-preview` | Tároló képe a `en-NZ` területi beállítással. |
| `2.2.0-amd64-en-us-preview` | Tároló képe a `en-US` területi beállítással. |
| `2.2.0-amd64-es-es-preview` | Tároló képe a `es-ES` területi beállítással. |
| `2.2.0-amd64-es-mx-preview` | Tároló képe a `es-MX` területi beállítással. |
| `2.2.0-amd64-fi-fi-preview` | Tároló képe a `fi-FI` területi beállítással. |
| `2.2.0-amd64-fr-ca-preview` | Tároló képe a `fr-CA` területi beállítással. |
| `2.2.0-amd64-fr-fr-preview` | Tároló képe a `fr-FR` területi beállítással. |
| `2.2.0-amd64-gu-in-preview` | Tároló képe a `gu-IN` területi beállítással. |
| `2.2.0-amd64-hi-in-preview` | Tároló képe a `hi-IN` területi beállítással. |
| `2.2.0-amd64-it-it-preview` | Tároló képe a `it-IT` területi beállítással. |
| `2.2.0-amd64-ja-jp-preview` | Tároló képe a `ja-JP` területi beállítással. |
| `2.2.0-amd64-ko-kr-preview` | Tároló képe a `ko-KR` területi beállítással. |
| `2.2.0-amd64-mr-in-preview` | Tároló képe a `mr-IN` területi beállítással. |
| `2.2.0-amd64-nb-no-preview` | Tároló képe a `nb-NO` területi beállítással. |
| `2.2.0-amd64-nl-nl-preview` | Tároló képe a `nl-NL` területi beállítással. |
| `2.2.0-amd64-pl-pl-preview` | Tároló képe a `pl-PL` területi beállítással. |
| `2.2.0-amd64-pt-br-preview` | Tároló képe a `pt-BR` területi beállítással. |
| `2.2.0-amd64-pt-pt-preview` | Tároló képe a `pt-PT` területi beállítással. |
| `2.2.0-amd64-ru-ru-preview` | Tároló képe a `ru-RU` területi beállítással. |
| `2.2.0-amd64-sv-se-preview` | Tároló képe a `sv-SE` területi beállítással. |
| `2.2.0-amd64-ta-in-preview` | Tároló képe a `ta-IN` területi beállítással. |
| `2.2.0-amd64-te-in-preview` | Tároló képe a `te-IN` területi beállítással. |
| `2.2.0-amd64-th-th-preview` | Tároló képe a `th-TH` területi beállítással. |
| `2.2.0-amd64-tr-tr-preview` | Tároló képe a `tr-TR` területi beállítással. |
| `2.2.0-amd64-zh-cn-preview` | Tároló képe a `zh-CN` területi beállítással. |
| `2.2.0-amd64-zh-hk-preview` | Tároló képe a `zh-HK` területi beállítással. |
| `2.2.0-amd64-zh-tw-preview` | Tároló képe a `zh-TW` területi beállítással. |
| `2.1.1-amd64-en-us-preview` | Tároló képe a `en-US` területi beállítással. |
| `2.1.1-amd64-ar-ae-preview` | Tároló képe a `ar-AE` területi beállítással. |
| `2.1.1-amd64-ar-eg-preview` | Tároló képe a `ar-EG` területi beállítással. |
| `2.1.1-amd64-ar-kw-preview` | Tároló képe a `ar-KW` területi beállítással. |
| `2.1.1-amd64-ar-qa-preview` | Tároló képe a `ar-QA` területi beállítással. |
| `2.1.1-amd64-ar-sa-preview` | Tároló képe a `ar-SA` területi beállítással. |
| `2.1.1-amd64-ca-es-preview` | Tároló képe a `ca-ES` területi beállítással. |
| `2.1.1-amd64-da-dk-preview` | Tároló képe a `da-DK` területi beállítással. |
| `2.1.1-amd64-de-de-preview` | Tároló képe a `de-DE` területi beállítással. |
| `2.1.1-amd64-en-au-preview` | Tároló képe a `en-AU` területi beállítással. |
| `2.1.1-amd64-en-ca-preview` | Tároló képe a `en-CA` területi beállítással. |
| `2.1.1-amd64-en-gb-preview` | Tároló képe a `en-GB` területi beállítással. |
| `2.1.1-amd64-en-in-preview` | Tároló képe a `en-IN` területi beállítással. |
| `2.1.1-amd64-en-nz-preview` | Tároló képe a `en-NZ` területi beállítással. |
| `2.1.1-amd64-en-us-preview` | Tároló képe a `en-US` területi beállítással. |
| `2.1.1-amd64-es-es-preview` | Tároló képe a `es-ES` területi beállítással. |
| `2.1.1-amd64-es-mx-preview` | Tároló képe a `es-MX` területi beállítással. |
| `2.1.1-amd64-fi-fi-preview` | Tároló képe a `fi-FI` területi beállítással. |
| `2.1.1-amd64-fr-ca-preview` | Tároló képe a `fr-CA` területi beállítással. |
| `2.1.1-amd64-fr-fr-preview` | Tároló képe a `fr-FR` területi beállítással. |
| `2.1.1-amd64-gu-in-preview` | Tároló képe a `gu-IN` területi beállítással. |
| `2.1.1-amd64-hi-in-preview` | Tároló képe a `hi-IN` területi beállítással. |
| `2.1.1-amd64-it-it-preview` | Tároló képe a `it-IT` területi beállítással. |
| `2.1.1-amd64-ja-jp-preview` | Tároló képe a `ja-JP` területi beállítással. |
| `2.1.1-amd64-ko-kr-preview` | Tároló képe a `ko-KR` területi beállítással. |
| `2.1.1-amd64-mr-in-preview` | Tároló képe a `mr-IN` területi beállítással. |
| `2.1.1-amd64-nb-no-preview` | Tároló képe a `nb-NO` területi beállítással. |
| `2.1.1-amd64-nl-nl-preview` | Tároló képe a `nl-NL` területi beállítással. |
| `2.1.1-amd64-pl-pl-preview` | Tároló képe a `pl-PL` területi beállítással. |
| `2.1.1-amd64-pt-br-preview` | Tároló képe a `pt-BR` területi beállítással. |
| `2.1.1-amd64-pt-pt-preview` | Tároló képe a `pt-PT` területi beállítással. |
| `2.1.1-amd64-ru-ru-preview` | Tároló képe a `ru-RU` területi beállítással. |
| `2.1.1-amd64-sv-se-preview` | Tároló képe a `sv-SE` területi beállítással. |
| `2.1.1-amd64-ta-in-preview` | Tároló képe a `ta-IN` területi beállítással. |
| `2.1.1-amd64-te-in-preview` | Tároló képe a `te-IN` területi beállítással. |
| `2.1.1-amd64-th-th-preview` | Tároló képe a `th-TH` területi beállítással. |
| `2.1.1-amd64-tr-tr-preview` | Tároló képe a `tr-TR` területi beállítással. |
| `2.1.1-amd64-zh-cn-preview` | Tároló képe a `zh-CN` területi beállítással. |
| `2.1.1-amd64-zh-hk-preview` | Tároló képe a `zh-HK` területi beállítással. |
| `2.1.1-amd64-zh-tw-preview` | Tároló képe a `zh-TW` területi beállítással. |
| `2.1.0-amd64-ar-ae-preview` | Tároló képe a `ar-AE` területi beállítással. |
| `2.1.0-amd64-ar-eg-preview` | Tároló képe a `ar-EG` területi beállítással. |
| `2.1.0-amd64-ar-kw-preview` | Tároló képe a `ar-KW` területi beállítással. |
| `2.1.0-amd64-ar-qa-preview` | Tároló képe a `ar-QA` területi beállítással. |
| `2.1.0-amd64-ar-sa-preview` | Tároló képe a `ar-SA` területi beállítással. |
| `2.1.0-amd64-ca-es-preview` | Tároló képe a `ca-ES` területi beállítással. |
| `2.1.0-amd64-da-dk-preview` | Tároló képe a `da-DK` területi beállítással. |
| `2.1.0-amd64-de-de-preview` | Tároló képe a `de-DE` területi beállítással. |
| `2.1.0-amd64-en-au-preview` | Tároló képe a `en-AU` területi beállítással. |
| `2.1.0-amd64-en-ca-preview` | Tároló képe a `en-CA` területi beállítással. |
| `2.1.0-amd64-en-gb-preview` | Tároló képe a `en-GB` területi beállítással. |
| `2.1.0-amd64-en-in-preview` | Tároló képe a `en-IN` területi beállítással. |
| `2.1.0-amd64-en-nz-preview` | Tároló képe a `en-NZ` területi beállítással. |
| `2.1.0-amd64-en-us-preview` | Tároló képe a `en-US` területi beállítással. |
| `2.1.0-amd64-es-es-preview` | Tároló képe a `es-ES` területi beállítással. |
| `2.1.0-amd64-es-mx-preview` | Tároló képe a `es-MX` területi beállítással. |
| `2.1.0-amd64-fi-fi-preview` | Tároló képe a `fi-FI` területi beállítással. |
| `2.1.0-amd64-fr-ca-preview` | Tároló képe a `fr-CA` területi beállítással. |
| `2.1.0-amd64-fr-fr-preview` | Tároló képe a `fr-FR` területi beállítással. |
| `2.1.0-amd64-gu-in-preview` | Tároló képe a `gu-IN` területi beállítással. |
| `2.1.0-amd64-hi-in-preview` | Tároló képe a `hi-IN` területi beállítással. |
| `2.1.0-amd64-it-it-preview` | Tároló képe a `it-IT` területi beállítással. |
| `2.1.0-amd64-ja-jp-preview` | Tároló képe a `ja-JP` területi beállítással. |
| `2.1.0-amd64-ko-kr-preview` | Tároló képe a `ko-KR` területi beállítással. |
| `2.1.0-amd64-mr-in-preview` | Tároló képe a `mr-IN` területi beállítással. |
| `2.1.0-amd64-nb-no-preview` | Tároló képe a `nb-NO` területi beállítással. |
| `2.1.0-amd64-nl-nl-preview` | Tároló képe a `nl-NL` területi beállítással. |
| `2.1.0-amd64-pl-pl-preview` | Tároló képe a `pl-PL` területi beállítással. |
| `2.1.0-amd64-pt-br-preview` | Tároló képe a `pt-BR` területi beállítással. |
| `2.1.0-amd64-pt-pt-preview` | Tároló képe a `pt-PT` területi beállítással. |
| `2.1.0-amd64-ru-ru-preview` | Tároló képe a `ru-RU` területi beállítással. |
| `2.1.0-amd64-sv-se-preview` | Tároló képe a `sv-SE` területi beállítással. |
| `2.1.0-amd64-ta-in-preview` | Tároló képe a `ta-IN` területi beállítással. |
| `2.1.0-amd64-te-in-preview` | Tároló képe a `te-IN` területi beállítással. |
| `2.1.0-amd64-th-th-preview` | Tároló képe a `th-TH` területi beállítással. |
| `2.1.0-amd64-tr-tr-preview` | Tároló képe a `tr-TR` területi beállítással. |
| `2.1.0-amd64-zh-cn-preview` | Tároló képe a `zh-CN` területi beállítással. |
| `2.1.0-amd64-zh-hk-preview` | Tároló képe a `zh-HK` területi beállítással. |
| `2.1.0-amd64-zh-tw-preview` | Tároló képe a `zh-TW` területi beállítással. |
| `2.0.3-amd64-ja-jp-preview` | Tároló képe a `ja-JP` területi beállítással. |
| `2.0.2-amd64-ar-ae-preview` | Tároló képe a `ar-AE` területi beállítással. |
| `2.0.2-amd64-ar-eg-preview` | Tároló képe a `ar-EG` területi beállítással. |
| `2.0.2-amd64-ar-kw-preview` | Tároló képe a `ar-KW` területi beállítással. |
| `2.0.2-amd64-ar-qa-preview` | Tároló képe a `ar-QA` területi beállítással. |
| `2.0.2-amd64-ar-sa-preview` | Tároló képe a `ar-SA` területi beállítással. |
| `2.0.2-amd64-ca-es-preview` | Tároló képe a `ca-ES` területi beállítással. |
| `2.0.2-amd64-da-dk-preview` | Tároló képe a `da-DK` területi beállítással. |
| `2.0.2-amd64-de-de-preview` | Tároló képe a `de-DE` területi beállítással. |
| `2.0.2-amd64-en-au-preview` | Tároló képe a `en-AU` területi beállítással. |
| `2.0.2-amd64-en-ca-preview` | Tároló képe a `en-CA` területi beállítással. |
| `2.0.2-amd64-en-gb-preview` | Tároló képe a `en-GB` területi beállítással. |
| `2.0.2-amd64-en-in-preview` | Tároló képe a `en-IN` területi beállítással. |
| `2.0.2-amd64-en-nz-preview` | Tároló képe a `en-NZ` területi beállítással. |
| `2.0.2-amd64-en-us-preview` | Tároló képe a `en-US` területi beállítással. |
| `2.0.2-amd64-es-es-preview` | Tároló képe a `es-ES` területi beállítással. |
| `2.0.2-amd64-es-mx-preview` | Tároló képe a `es-MX` területi beállítással. |
| `2.0.2-amd64-fi-fi-preview` | Tároló képe a `fi-FI` területi beállítással. |
| `2.0.2-amd64-fr-ca-preview` | Tároló képe a `fr-CA` területi beállítással. |
| `2.0.2-amd64-fr-fr-preview` | Tároló képe a `fr-FR` területi beállítással. |
| `2.0.2-amd64-gu-in-preview` | Tároló képe a `gu-IN` területi beállítással. |
| `2.0.2-amd64-hi-in-preview` | Tároló képe a `hi-IN` területi beállítással. |
| `2.0.2-amd64-it-it-preview` | Tároló képe a `it-IT` területi beállítással. |
| `2.0.2-amd64-ja-jp-preview` | Tároló képe a `ja-JP` területi beállítással. |
| `2.0.2-amd64-ko-kr-preview` | Tároló képe a `ko-KR` területi beállítással. |
| `2.0.2-amd64-mr-in-preview` | Tároló képe a `mr-IN` területi beállítással. |
| `2.0.2-amd64-nb-no-preview` | Tároló képe a `nb-NO` területi beállítással. |
| `2.0.2-amd64-nl-nl-preview` | Tároló képe a `nl-NL` területi beállítással. |
| `2.0.2-amd64-pl-pl-preview` | Tároló képe a `pl-PL` területi beállítással. |
| `2.0.2-amd64-pt-br-preview` | Tároló képe a `pt-BR` területi beállítással. |
| `2.0.2-amd64-pt-pt-preview` | Tároló képe a `pt-PT` területi beállítással. |
| `2.0.2-amd64-ru-ru-preview` | Tároló képe a `ru-RU` területi beállítással. |
| `2.0.2-amd64-sv-se-preview` | Tároló képe a `sv-SE` területi beállítással. |
| `2.0.2-amd64-ta-in-preview` | Tároló képe a `ta-IN` területi beállítással. |
| `2.0.2-amd64-te-in-preview` | Tároló képe a `te-IN` területi beállítással. |
| `2.0.2-amd64-th-th-preview` | Tároló képe a `th-TH` területi beállítással. |
| `2.0.2-amd64-tr-tr-preview` | Tároló képe a `tr-TR` területi beállítással. |
| `2.0.2-amd64-zh-cn-preview` | Tároló képe a `zh-CN` területi beállítással. |
| `2.0.2-amd64-zh-hk-preview` | Tároló képe a `zh-HK` területi beállítással. |
| `2.0.2-amd64-zh-tw-preview` | Tároló képe a `zh-TW` területi beállítással. |
| `2.0.1-amd64-ar-ae-preview` | Tároló képe a `ar-AE` területi beállítással. |
| `2.0.1-amd64-ar-eg-preview` | Tároló képe a `ar-EG` területi beállítással. |
| `2.0.1-amd64-ar-kw-preview` | Tároló képe a `ar-KW` területi beállítással. |
| `2.0.1-amd64-ar-qa-preview` | Tároló képe a `ar-QA` területi beállítással. |
| `2.0.1-amd64-ar-sa-preview` | Tároló képe a `ar-SA` területi beállítással. |
| `2.0.1-amd64-ca-es-preview` | Tároló képe a `ca-ES` területi beállítással. |
| `2.0.1-amd64-da-dk-preview` | Tároló képe a `da-DK` területi beállítással. |
| `2.0.1-amd64-de-de-preview` | Tároló képe a `de-DE` területi beállítással. |
| `2.0.1-amd64-en-au-preview` | Tároló képe a `en-AU` területi beállítással. |
| `2.0.1-amd64-en-ca-preview` | Tároló képe a `en-CA` területi beállítással. |
| `2.0.1-amd64-en-gb-preview` | Tároló képe a `en-GB` területi beállítással. |
| `2.0.1-amd64-en-in-preview` | Tároló képe a `en-IN` területi beállítással. |
| `2.0.1-amd64-en-nz-preview` | Tároló képe a `en-NZ` területi beállítással. |
| `2.0.1-amd64-en-us-preview` | Tároló képe a `en-US` területi beállítással. |
| `2.0.1-amd64-es-es-preview` | Tároló képe a `es-ES` területi beállítással. |
| `2.0.1-amd64-es-mx-preview` | Tároló képe a `es-MX` területi beállítással. |
| `2.0.1-amd64-fi-fi-preview` | Tároló képe a `fi-FI` területi beállítással. |
| `2.0.1-amd64-fr-ca-preview` | Tároló képe a `fr-CA` területi beállítással. |
| `2.0.1-amd64-fr-fr-preview` | Tároló képe a `fr-FR` területi beállítással. |
| `2.0.1-amd64-gu-in-preview` | Tároló képe a `gu-IN` területi beállítással. |
| `2.0.1-amd64-hi-in-preview` | Tároló képe a `hi-IN` területi beállítással. |
| `2.0.1-amd64-it-it-preview` | Tároló képe a `it-IT` területi beállítással. |
| `2.0.1-amd64-ja-jp-preview` | Tároló képe a `ja-JP` területi beállítással. |
| `2.0.1-amd64-ko-kr-preview` | Tároló képe a `ko-KR` területi beállítással. |
| `2.0.1-amd64-mr-in-preview` | Tároló képe a `mr-IN` területi beállítással. |
| `2.0.1-amd64-nb-no-preview` | Tároló képe a `nb-NO` területi beállítással. |
| `2.0.1-amd64-nl-nl-preview` | Tároló képe a `nl-NL` területi beállítással. |
| `2.0.1-amd64-pl-pl-preview` | Tároló képe a `pl-PL` területi beállítással. |
| `2.0.1-amd64-pt-br-preview` | Tároló képe a `pt-BR` területi beállítással. |
| `2.0.1-amd64-pt-pt-preview` | Tároló képe a `pt-PT` területi beállítással. |
| `2.0.1-amd64-ru-ru-preview` | Tároló képe a `ru-RU` területi beállítással. |
| `2.0.1-amd64-sv-se-preview` | Tároló képe a `sv-SE` területi beállítással. |
| `2.0.1-amd64-ta-in-preview` | Tároló képe a `ta-IN` területi beállítással. |
| `2.0.1-amd64-te-in-preview` | Tároló képe a `te-IN` területi beállítással. |
| `2.0.1-amd64-th-th-preview` | Tároló képe a `th-TH` területi beállítással. |
| `2.0.1-amd64-tr-tr-preview` | Tároló képe a `tr-TR` területi beállítással. |
| `2.0.1-amd64-zh-cn-preview` | Tároló képe a `zh-CN` területi beállítással. |
| `2.0.1-amd64-zh-hk-preview` | Tároló képe a `zh-HK` területi beállítással. |
| `2.0.1-amd64-zh-tw-preview` | Tároló képe a `zh-TW` területi beállítással. |
| `2.0.0-amd64-ar-eg-preview` | Tároló képe a `ar-EG` területi beállítással. |
| `2.0.0-amd64-ca-es-preview` | Tároló képe a `ca-ES` területi beállítással. |
| `2.0.0-amd64-da-dk-preview` | Tároló képe a `da-DK` területi beállítással. |
| `2.0.0-amd64-de-de-preview` | Tároló képe a `de-DE` területi beállítással. |
| `2.0.0-amd64-en-au-preview` | Tároló képe a `en-AU` területi beállítással. |
| `2.0.0-amd64-en-ca-preview` | Tároló képe a `en-CA` területi beállítással. |
| `2.0.0-amd64-en-gb-preview` | Tároló képe a `en-GB` területi beállítással. |
| `2.0.0-amd64-en-in-preview` | Tároló képe a `en-IN` területi beállítással. |
| `2.0.0-amd64-en-nz-preview` | Tároló képe a `en-NZ` területi beállítással. |
| `2.0.0-amd64-en-us-preview` | Tároló képe a `en-US` területi beállítással. |
| `2.0.0-amd64-es-es-preview` | Tároló képe a `es-ES` területi beállítással. |
| `2.0.0-amd64-es-mx-preview` | Tároló képe a `es-MX` területi beállítással. |
| `2.0.0-amd64-fi-fi-preview` | Tároló képe a `fi-FI` területi beállítással. |
| `2.0.0-amd64-fr-ca-preview` | Tároló képe a `fr-CA` területi beállítással. |
| `2.0.0-amd64-fr-fr-preview` | Tároló képe a `fr-FR` területi beállítással. |
| `2.0.0-amd64-hi-in-preview` | Tároló képe a `hi-IN` területi beállítással. |
| `2.0.0-amd64-it-it-preview` | Tároló képe a `it-IT` területi beállítással. |
| `2.0.0-amd64-ja-jp-preview` | Tároló képe a `ja-JP` területi beállítással. |
| `2.0.0-amd64-ko-kr-preview` | Tároló képe a `ko-KR` területi beállítással. |
| `2.0.0-amd64-nb-no-preview` | Tároló képe a `nb-NO` területi beállítással. |
| `2.0.0-amd64-nl-nl-preview` | Tároló képe a `nl-NL` területi beállítással. |
| `2.0.0-amd64-pl-pl-preview` | Tároló képe a `pl-PL` területi beállítással. |
| `2.0.0-amd64-pt-br-preview` | Tároló képe a `pt-BR` területi beállítással. |
| `2.0.0-amd64-pt-pt-preview` | Tároló képe a `pt-PT` területi beállítással. |
| `2.0.0-amd64-ru-ru-preview` | Tároló képe a `ru-RU` területi beállítással. |
| `2.0.0-amd64-sv-se-preview` | Tároló képe a `sv-SE` területi beállítással. |
| `2.0.0-amd64-th-th-preview` | Tároló képe a `th-TH` területi beállítással. |
| `2.0.0-amd64-tr-tr-preview` | Tároló képe a `tr-TR` területi beállítással. |
| `2.0.0-amd64-zh-cn-preview` | Tároló képe a `zh-CN` területi beállítással. |
| `2.0.0-amd64-zh-hk-preview` | Tároló képe a `zh-HK` területi beállítással. |
| `2.0.0-amd64-zh-tw-preview` | Tároló képe a `zh-TW` területi beállítással. |
| `1.2.0-amd64-de-de-preview` | Tároló képe a `de-DE` területi beállítással. |
| `1.2.0-amd64-en-au-preview` | Tároló képe a `en-AU` területi beállítással. |
| `1.2.0-amd64-en-ca-preview` | Tároló képe a `en-CA` területi beállítással. |
| `1.2.0-amd64-en-gb-preview` | Tároló képe a `en-GB` területi beállítással. |
| `1.2.0-amd64-en-in-preview` | Tároló képe a `en-IN` területi beállítással. |
| `1.2.0-amd64-en-us-preview` | Tároló képe a `en-US` területi beállítással. |
| `1.2.0-amd64-es-es-preview` | Tároló képe a `es-ES` területi beállítással. |
| `1.2.0-amd64-es-mx-preview` | Tároló képe a `es-MX` területi beállítással. |
| `1.2.0-amd64-fr-ca-preview` | Tároló képe a `fr-CA` területi beállítással. |
| `1.2.0-amd64-fr-fr-preview` | Tároló képe a `fr-FR` területi beállítással. |
| `1.2.0-amd64-it-it-preview` | Tároló képe a `it-IT` területi beállítással. |
| `1.2.0-amd64-ja-jp-preview` | Tároló képe a `ja-JP` területi beállítással. |
| `1.2.0-amd64-pt-br-preview` | Tároló képe a `pt-BR` területi beállítással. |
| `1.2.0-amd64-zh-cn-preview` | Tároló képe a `zh-CN` területi beállítással. |
| `1.1.3-amd64-de-de-preview` | Tároló képe a `de-DE` területi beállítással. |
| `1.1.3-amd64-en-au-preview` | Tároló képe a `en-AU` területi beállítással. |
| `1.1.3-amd64-en-ca-preview` | Tároló képe a `en-CA` területi beállítással. |
| `1.1.3-amd64-en-gb-preview` | Tároló képe a `en-GB` területi beállítással. |
| `1.1.3-amd64-en-in-preview` | Tároló képe a `en-IN` területi beállítással. |
| `1.1.3-amd64-en-us-preview` | Tároló képe a `en-US` területi beállítással. |
| `1.1.3-amd64-es-es-preview` | Tároló képe a `es-ES` területi beállítással. |
| `1.1.3-amd64-es-mx-preview` | Tároló képe a `es-MX` területi beállítással. |
| `1.1.3-amd64-fr-ca-preview` | Tároló képe a `fr-CA` területi beállítással. |
| `1.1.3-amd64-fr-fr-preview` | Tároló képe a `fr-FR` területi beállítással. |
| `1.1.3-amd64-it-it-preview` | Tároló képe a `it-IT` területi beállítással. |
| `1.1.3-amd64-ja-jp-preview` | Tároló képe a `ja-JP` területi beállítással. |
| `1.1.3-amd64-pt-br-preview` | Tároló képe a `pt-BR` területi beállítással. |
| `1.1.3-amd64-zh-cn-preview` | Tároló képe a `zh-CN` területi beállítással. |
| `1.1.2-amd64-de-de-preview` | Tároló képe a `de-DE` területi beállítással. |
| `1.1.2-amd64-en-au-preview` | Tároló képe a `en-AU` területi beállítással. |
| `1.1.2-amd64-en-ca-preview` | Tároló képe a `en-CA` területi beállítással. |
| `1.1.2-amd64-en-gb-preview` | Tároló képe a `en-GB` területi beállítással. |
| `1.1.2-amd64-en-in-preview` | Tároló képe a `en-IN` területi beállítással. |
| `1.1.2-amd64-en-us-preview` | Tároló képe a `en-US` területi beállítással. |
| `1.1.2-amd64-es-es-preview` | Tároló képe a `es-ES` területi beállítással. |
| `1.1.2-amd64-es-mx-preview` | Tároló képe a `es-MX` területi beállítással. |
| `1.1.2-amd64-fr-ca-preview` | Tároló képe a `fr-CA` területi beállítással. |
| `1.1.2-amd64-fr-fr-preview` | Tároló képe a `fr-FR` területi beállítással. |
| `1.1.2-amd64-it-it-preview` | Tároló képe a `it-IT` területi beállítással. |
| `1.1.2-amd64-ja-jp-preview` | Tároló képe a `ja-JP` területi beállítással. |
| `1.1.2-amd64-pt-br-preview` | Tároló képe a `pt-BR` területi beállítással. |
| `1.1.2-amd64-zh-cn-preview` | Tároló képe a `zh-CN` területi beállítással. |
| `1.1.1-amd64-de-de-preview` | Tároló képe a `de-DE` területi beállítással. |
| `1.1.1-amd64-en-au-preview` | Tároló képe a `en-AU` területi beállítással. |
| `1.1.1-amd64-en-ca-preview` | Tároló képe a `en-CA` területi beállítással. |
| `1.1.1-amd64-en-gb-preview` | Tároló képe a `en-GB` területi beállítással. |
| `1.1.1-amd64-en-in-preview` | Tároló képe a `en-IN` területi beállítással. |
| `1.1.1-amd64-en-us-preview` | Tároló képe a `en-US` területi beállítással. |
| `1.1.1-amd64-es-es-preview` | Tároló képe a `es-ES` területi beállítással. |
| `1.1.1-amd64-es-mx-preview` | Tároló képe a `es-MX` területi beállítással. |
| `1.1.1-amd64-fr-ca-preview` | Tároló képe a `fr-CA` területi beállítással. |
| `1.1.1-amd64-fr-fr-preview` | Tároló képe a `fr-FR` területi beállítással. |
| `1.1.1-amd64-it-it-preview` | Tároló képe a `it-IT` területi beállítással. |
| `1.1.1-amd64-ja-jp-preview` | Tároló képe a `ja-JP` területi beállítással. |
| `1.1.1-amd64-pt-br-preview` | Tároló képe a `pt-BR` területi beállítással. |
| `1.1.1-amd64-zh-cn-preview` | Tároló képe a `zh-CN` területi beállítással. |
| `1.1.0-amd64-de-de-preview` | Tároló képe a `de-DE` területi beállítással. |
| `1.1.0-amd64-en-au-preview` | Tároló képe a `en-AU` területi beállítással. |
| `1.1.0-amd64-en-ca-preview` | Tároló képe a `en-CA` területi beállítással. |
| `1.1.0-amd64-en-gb-preview` | Tároló képe a `en-GB` területi beállítással. |
| `1.1.0-amd64-en-in-preview` | Tároló képe a `en-IN` területi beállítással. |
| `1.1.0-amd64-en-us-preview` | Tároló képe a `en-US` területi beállítással. |
| `1.1.0-amd64-es-es-preview` | Tároló képe a `es-ES` területi beállítással. |
| `1.1.0-amd64-es-mx-preview` | Tároló képe a `es-MX` területi beállítással. |
| `1.1.0-amd64-fr-ca-preview` | Tároló képe a `fr-CA` területi beállítással. |
| `1.1.0-amd64-fr-fr-preview` | Tároló képe a `fr-FR` területi beállítással. |
| `1.1.0-amd64-it-it-preview` | Tároló képe a `it-IT` területi beállítással. |
| `1.1.0-amd64-ja-jp-preview` | Tároló képe a `ja-JP` területi beállítással. |
| `1.1.0-amd64-pt-br-preview` | Tároló képe a `pt-BR` területi beállítással. |
| `1.1.0-amd64-zh-cn-preview` | Tároló képe a `zh-CN` területi beállítással. |
| `1.0.0-amd64-de-de-preview` | Tároló képe a `de-DE` területi beállítással. |
| `1.0.0-amd64-en-au-preview` | Tároló képe a `en-AU` területi beállítással. |
| `1.0.0-amd64-en-ca-preview` | Tároló képe a `en-CA` területi beállítással. |
| `1.0.0-amd64-en-gb-preview` | Tároló képe a `en-GB` területi beállítással. |
| `1.0.0-amd64-en-in-preview` | Tároló képe a `en-IN` területi beállítással. |
| `1.0.0-amd64-en-us-preview` | Tároló képe a `en-US` területi beállítással. |
| `1.0.0-amd64-es-es-preview` | Tároló képe a `es-ES` területi beállítással. |
| `1.0.0-amd64-es-mx-preview` | Tároló képe a `es-MX` területi beállítással. |
| `1.0.0-amd64-fr-ca-preview` | Tároló képe a `fr-CA` területi beállítással. |
| `1.0.0-amd64-fr-fr-preview` | Tároló képe a `fr-FR` területi beállítással. |
| `1.0.0-amd64-it-it-preview` | Tároló képe a `it-IT` területi beállítással. |
| `1.0.0-amd64-ja-jp-preview` | Tároló képe a `ja-JP` területi beállítással. |
| `1.0.0-amd64-pt-br-preview` | Tároló képe a `pt-BR` területi beállítással. |
| `1.0.0-amd64-zh-cn-preview` | Tároló képe a `zh-CN` területi beállítással. |

## <a name="text-to-speech"></a>Szövegfelolvasás

A [szöveg-beszéd][sp-tts] tároló képe megtalálható a `containerpreview.azurecr.io` tároló beállításjegyzékében. A `microsoft` tárházban található, és neve `cognitive-services-text-to-speech`. A teljes tároló rendszerképének neve: `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech`.

Ez a tároló-rendszerkép a következő címkéket tartalmazhatja:

| Képcímkék                                  | Megjegyzések                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | A tároló képe a `en-US` területi beállítással `en-US-JessaRUS` és a hanggal.        |
| `1.3.0-amd64-ar-eg-hoda-preview`            | A tároló képe a `ar-EG` területi beállítással `ar-EG-Hoda` és a hanggal.            |
| `1.3.0-amd64-ar-sa-naayf-preview`           | A tároló képe a `ar-SA` területi beállítással `ar-SA-Naayf` és a hanggal.           |
| `1.3.0-amd64-bg-bg-ivan-preview`            | A tároló képe a `bg-BG` területi beállítással `bg-BG-Ivan` és a hanggal.            |
| `1.3.0-amd64-ca-es-herenarus-preview`       | A tároló képe a `ca-ES` területi beállítással `ca-ES-HerenaRUS` és a hanggal.       |
| `1.3.0-amd64-cs-cz-jakub-preview`           | A tároló képe a `cs-CZ` területi beállítással `cs-CZ-Jakub` és a hanggal.           |
| `1.3.0-amd64-da-dk-hellerus-preview`        | A tároló képe a `da-DK` területi beállítással `da-DK-HelleRUS` és a hanggal.        |
| `1.3.0-amd64-de-at-michael-preview`         | A tároló képe a `de-AT` területi beállítással `de-AT-Michael` és a hanggal.         |
| `1.3.0-amd64-de-ch-karsten-preview`         | A tároló képe a `de-CH` területi beállítással `de-CH-Karsten` és a hanggal.         |
| `1.3.0-amd64-de-de-hedda-preview`           | A tároló képe a `de-DE` területi beállítással `de-DE-Hedda` és a hanggal.           |
| `1.3.0-amd64-de-de-heddarus-preview`        | A tároló képe a `de-DE` területi beállítással `de-DE-Hedda` és a hanggal.           |
| `1.3.0-amd64-de-de-heddarus-preview`        | A tároló képe a `de-DE` területi beállítással `de-DE-HeddaRUS` és a hanggal.        |
| `1.3.0-amd64-de-de-stefan-apollo-preview`   | A tároló képe a `de-DE` területi beállítással `de-DE-Stefan-Apollo` és a hanggal.   |
| `1.3.0-amd64-el-gr-stefanos-preview`        | A tároló képe a `el-GR` területi beállítással `el-GR-Stefanos` és a hanggal.        |
| `1.3.0-amd64-en-au-catherine-preview`       | A tároló képe a `en-AU` területi beállítással `en-AU-Catherine` és a hanggal.       |
| `1.3.0-amd64-en-au-hayleyrus-preview`       | A tároló képe a `en-AU` területi beállítással `en-AU-HayleyRUS` és a hanggal.       |
| `1.3.0-amd64-en-ca-heatherrus-preview`      | A tároló képe a `en-CA` területi beállítással `en-CA-HeatherRUS` és a hanggal.      |
| `1.3.0-amd64-en-ca-linda-preview`           | A tároló képe a `en-CA` területi beállítással `en-CA-Linda` és a hanggal.           |
| `1.3.0-amd64-en-gb-george-apollo-preview`   | A tároló képe a `en-GB` területi beállítással `en-GB-George-Apollo` és a hanggal.   |
| `1.3.0-amd64-en-gb-hazelrus-preview`        | A tároló képe a `en-GB` területi beállítással `en-GB-HazelRUS` és a hanggal.        |
| `1.3.0-amd64-en-gb-susan-apollo-preview`    | A tároló képe a `en-GB` területi beállítással `en-GB-Susan-Apollo` és a hanggal.    |
| `1.3.0-amd64-en-ie-sean-preview`            | A tároló képe a `en-IE` területi beállítással `en-IE-Sean` és a hanggal.            |
| `1.3.0-amd64-en-in-heera-apollo-preview`    | A tároló képe a `en-IN` területi beállítással `en-IN-Heera-Apollo` és a hanggal.    |
| `1.3.0-amd64-en-in-priyarus-preview`        | A tároló képe a `en-IN` területi beállítással `en-IN-PriyaRUS` és a hanggal.        |
| `1.3.0-amd64-en-in-ravi-apollo-preview`     | A tároló képe a `en-IN` területi beállítással `en-IN-Ravi-Apollo` és a hanggal.     |
| `1.3.0-amd64-en-us-benjaminrus-preview`     | A tároló képe a `en-US` területi beállítással `en-US-BenjaminRUS` és a hanggal.     |
| `1.3.0-amd64-en-us-guy24krus-preview`       | A tároló képe a `en-US` területi beállítással `en-US-Guy24kRUS` és a hanggal.       |
| `1.3.0-amd64-en-us-jessa24krus-preview`     | A tároló képe a `en-US` területi beállítással `en-US-Jessa24kRUS` és a hanggal.     |
| `1.3.0-amd64-en-us-jessarus-preview`        | A tároló képe a `en-US` területi beállítással `en-US-JessaRUS` és a hanggal.        |
| `1.3.0-amd64-en-us-zirarus-preview`         | A tároló képe a `en-US` területi beállítással `en-US-ZiraRUS` és a hanggal.         |
| `1.3.0-amd64-es-es-helenarus-preview`       | A tároló képe a `es-ES` területi beállítással `es-ES-HelenaRUS` és a hanggal.       |
| `1.3.0-amd64-es-es-laura-apollo-preview`    | A tároló képe a `es-ES` területi beállítással `es-ES-Laura-Apollo` és a hanggal.    |
| `1.3.0-amd64-es-es-pablo-apollo-preview`    | A tároló képe a `es-ES` területi beállítással `es-ES-Pablo-Apollo` és a hanggal.    |
| `1.3.0-amd64-es-mx-hildarus-preview`        | A tároló képe a `es-MX` területi beállítással `es-MX-HildaRUS` és a hanggal.        |
| `1.3.0-amd64-es-mx-raul-apollo-preview`     | A tároló képe a `es-MX` területi beállítással `es-MX-Raul-Apollo` és a hanggal.     |
| `1.3.0-amd64-fi-fi-heidirus-preview`        | A tároló képe a `fi-FI` területi beállítással `fi-FI-HeidiRUS` és a hanggal.        |
| `1.3.0-amd64-fr-ca-caroline-preview`        | A tároló képe a `fr-CA` területi beállítással `fr-CA-Caroline` és a hanggal.        |
| `1.3.0-amd64-fr-ca-harmonierus-preview`     | A tároló képe a `fr-CA` területi beállítással `fr-CA-HarmonieRUS` és a hanggal.     |
| `1.3.0-amd64-fr-ch-guillaume-preview`       | A tároló képe a `fr-CH` területi beállítással `fr-CH-Guillaume` és a hanggal.       |
| `1.3.0-amd64-fr-fr-hortenserus-preview`     | A tároló képe a `fr-FR` területi beállítással `fr-FR-HortenseRUS` és a hanggal.     |
| `1.3.0-amd64-fr-fr-julie-apollo-preview`    | A tároló képe a `fr-FR` területi beállítással `fr-FR-Julie-Apollo` és a hanggal.    |
| `1.3.0-amd64-fr-fr-paul-apollo-preview`     | A tároló képe a `fr-FR` területi beállítással `fr-FR-Paul-Apollo` és a hanggal.     |
| `1.3.0-amd64-he-il-asaf-preview`            | A tároló képe a `he-IL` területi beállítással `he-IL-Asaf` és a hanggal.            |
| `1.3.0-amd64-hi-in-hemant-preview`          | A tároló képe a `hi-IN` területi beállítással `hi-IN-Hemant` és a hanggal.          |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview`  | A tároló képe a `hi-IN` területi beállítással `hi-IN-Kalpana-Apollo` és a hanggal.  |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview`  | A tároló képe a `hi-IN` területi beállítással `hi-IN-Kalpana` és a hanggal.         |
| `1.3.0-amd64-hi-in-kalpana-preview`         | A tároló képe a `hi-IN` területi beállítással `hi-IN-Kalpana` és a hanggal.         |
| `1.3.0-amd64-hr-hr-matej-preview`           | A tároló képe a `hr-HR` területi beállítással `hr-HR-Matej` és a hanggal.           |
| `1.3.0-amd64-hu-hu-szabolcs-preview`        | A tároló képe a `hu-HU` területi beállítással `hu-HU-Szabolcs` és a hanggal.        |
| `1.3.0-amd64-id-id-andika-preview`          | A tároló képe a `id-ID` területi beállítással `id-ID-Andika` és a hanggal.          |
| `1.3.0-amd64-it-it-cosimo-apollo-preview`   | A tároló képe a `it-IT` területi beállítással `it-IT-Cosimo-Apollo` és a hanggal.   |
| `1.3.0-amd64-it-it-luciarus-preview`        | A tároló képe a `it-IT` területi beállítással `it-IT-LuciaRUS` és a hanggal.        |
| `1.3.0-amd64-ja-jp-ayumi-apollo-preview`    | A tároló képe a `ja-JP` területi beállítással `ja-JP-Ayumi-Apollo` és a hanggal.    |
| `1.3.0-amd64-ja-jp-harukarus-preview`       | A tároló képe a `ja-JP` területi beállítással `ja-JP-HarukaRUS` és a hanggal.       |
| `1.3.0-amd64-ja-jp-ichiro-apollo-preview`   | A tároló képe a `ja-JP` területi beállítással `ja-JP-Ichiro-Apollo` és a hanggal.   |
| `1.3.0-amd64-ko-kr-heamirus-preview`        | A tároló képe a `ko-KR` területi beállítással `ko-KR-HeamiRUS` és a hanggal.        |
| `1.3.0-amd64-ms-my-rizwan-preview`          | A tároló képe a `ms-MY` területi beállítással `ms-MY-Rizwan` és a hanggal.          |
| `1.3.0-amd64-nb-no-huldarus-preview`        | A tároló képe a `nb-NO` területi beállítással `nb-NO-HuldaRUS` és a hanggal.        |
| `1.3.0-amd64-nl-nl-hannarus-preview`        | A tároló képe a `nl-NL` területi beállítással `nl-NL-HannaRUS` és a hanggal.        |
| `1.3.0-amd64-pl-pl-paulinarus-preview`      | A tároló képe a `pl-PL` területi beállítással `pl-PL-PaulinaRUS` és a hanggal.      |
| `1.3.0-amd64-pt-br-daniel-apollo-preview`   | A tároló képe a `pt-BR` területi beállítással `pt-BR-Daniel-Apollo` és a hanggal.   |
| `1.3.0-amd64-pt-br-heloisarus-preview`      | A tároló képe a `pt-BR` területi beállítással `pt-BR-HeloisaRUS` és a hanggal.      |
| `1.3.0-amd64-pt-pt-heliarus-preview`        | A tároló képe a `pt-PT` területi beállítással `pt-PT-HeliaRUS` és a hanggal.        |
| `1.3.0-amd64-ro-ro-andrei-preview`          | A tároló képe a `ro-RO` területi beállítással `ro-RO-Andrei` és a hanggal.          |
| `1.3.0-amd64-ru-ru-ekaterinarus-preview`    | A tároló képe a `ru-RU` területi beállítással `ru-RU-EkaterinaRUS` és a hanggal.    |
| `1.3.0-amd64-ru-ru-irina-apollo-preview`    | A tároló képe a `ru-RU` területi beállítással `ru-RU-Irina-Apollo` és a hanggal.    |
| `1.3.0-amd64-ru-ru-pavel-apollo-preview`    | A tároló képe a `ru-RU` területi beállítással `ru-RU-Pavel-Apollo` és a hanggal.    |
| `1.3.0-amd64-sk-sk-filip-preview`           | A tároló képe a `sk-SK` területi beállítással `sk-SK-Filip` és a hanggal.           |
| `1.3.0-amd64-sl-si-lado-preview`            | A tároló képe a `sl-SI` területi beállítással `sl-SI-Lado` és a hanggal.            |
| `1.3.0-amd64-sv-se-hedvigrus-preview`       | A tároló képe a `sv-SE` területi beállítással `sv-SE-HedvigRUS` és a hanggal.       |
| `1.3.0-amd64-ta-in-valluvar-preview`        | A tároló képe a `ta-IN` területi beállítással `ta-IN-Valluvar` és a hanggal.        |
| `1.3.0-amd64-te-in-chitra-preview`          | A tároló képe a `te-IN` területi beállítással `te-IN-Chitra` és a hanggal.          |
| `1.3.0-amd64-th-th-pattara-preview`         | A tároló képe a `th-TH` területi beállítással `th-TH-Pattara` és a hanggal.         |
| `1.3.0-amd64-tr-tr-sedarus-preview`         | A tároló képe a `tr-TR` területi beállítással `tr-TR-SedaRUS` és a hanggal.         |
| `1.3.0-amd64-vi-vn-an-preview`              | A tároló képe a `vi-VN` területi beállítással `vi-VN-An` és a hanggal.              |
| `1.3.0-amd64-zh-cn-huihuirus-preview`       | A tároló képe a `zh-CN` területi beállítással `zh-CN-HuihuiRUS` és a hanggal.       |
| `1.3.0-amd64-zh-cn-kangkang-apollo-preview` | A tároló képe a `zh-CN` területi beállítással `zh-CN-Kangkang-Apollo` és a hanggal. |
| `1.3.0-amd64-zh-cn-yaoyao-apollo-preview`   | A tároló képe a `zh-CN` területi beállítással `zh-CN-Yaoyao-Apollo` és a hanggal.   |
| `1.3.0-amd64-zh-hk-danny-apollo-preview`    | A tároló képe a `zh-HK` területi beállítással `zh-HK-Danny-Apollo` és a hanggal.    |
| `1.3.0-amd64-zh-hk-tracy-apollo-preview`    | A tároló képe a `zh-HK` területi beállítással `zh-HK-Tracy-Apollo` és a hanggal.    |
| `1.3.0-amd64-zh-hk-tracyrus-preview`        | A tároló képe a `zh-HK` területi beállítással `zh-HK-TracyRUS` és a hanggal.        |
| `1.3.0-amd64-zh-tw-hanhanrus-preview`       | A tároló képe a `zh-TW` területi beállítással `zh-TW-HanHanRUS` és a hanggal.       |
| `1.3.0-amd64-zh-tw-yating-apollo-preview`   | A tároló képe a `zh-TW` területi beállítással `zh-TW-Yating-Apollo` és a hanggal.   |
| `1.3.0-amd64-zh-tw-zhiwei-apollo-preview`   | A tároló képe a `zh-TW` területi beállítással `zh-TW-Zhiwei-Apollo` és a hanggal.   |
| `1.2.0-amd64-de-de-heddarus-preview`        | A tároló képe a `de-DE` területi beállítással `de-DE-Hedda` és a hanggal.           |
| `1.2.0-amd64-de-de-heddarus-preview`        | A tároló képe a `de-DE` területi beállítással `de-DE-HeddaRUS` és a hanggal.        |
| `1.2.0-amd64-de-de-stefan-apollo-preview`   | A tároló képe a `de-DE` területi beállítással `de-DE-Stefan-Apollo` és a hanggal.   |
| `1.2.0-amd64-en-au-catherine-preview`       | A tároló képe a `en-AU` területi beállítással `en-AU-Catherine` és a hanggal.       |
| `1.2.0-amd64-en-au-hayleyrus-preview`       | A tároló képe a `en-AU` területi beállítással `en-AU-HayleyRUS` és a hanggal.       |
| `1.2.0-amd64-en-gb-george-apollo-preview`   | A tároló képe a `en-GB` területi beállítással `en-GB-George-Apollo` és a hanggal.   |
| `1.2.0-amd64-en-gb-hazelrus-preview`        | A tároló képe a `en-GB` területi beállítással `en-GB-HazelRUS` és a hanggal.        |
| `1.2.0-amd64-en-gb-susan-apollo-preview`    | A tároló képe a `en-GB` területi beállítással `en-GB-Susan-Apollo` és a hanggal.    |
| `1.2.0-amd64-en-in-heera-apollo-preview`    | A tároló képe a `en-IN` területi beállítással `en-IN-Heera-Apollo` és a hanggal.    |
| `1.2.0-amd64-en-in-priyarus-preview`        | A tároló képe a `en-IN` területi beállítással `en-IN-PriyaRUS` és a hanggal.        |
| `1.2.0-amd64-en-in-ravi-apollo-preview`     | A tároló képe a `en-IN` területi beállítással `en-IN-Ravi-Apollo` és a hanggal.     |
| `1.2.0-amd64-en-us-benjaminrus-preview`     | A tároló képe a `en-US` területi beállítással `en-US-BenjaminRUS` és a hanggal.     |
| `1.2.0-amd64-en-us-guy24krus-preview`       | A tároló képe a `en-US` területi beállítással `en-US-Guy24kRUS` és a hanggal.       |
| `1.2.0-amd64-en-us-jessa24krus-preview`     | A tároló képe a `en-US` területi beállítással `en-US-Jessa24kRUS` és a hanggal.     |
| `1.2.0-amd64-en-us-jessarus-preview`        | A tároló képe a `en-US` területi beállítással `en-US-JessaRUS` és a hanggal.        |
| `1.2.0-amd64-en-us-zirarus-preview`         | A tároló képe a `en-US` területi beállítással `en-US-ZiraRUS` és a hanggal.         |
| `1.2.0-amd64-es-es-helenarus-preview`       | A tároló képe a `es-ES` területi beállítással `es-ES-HelenaRUS` és a hanggal.       |
| `1.2.0-amd64-es-es-laura-apollo-preview`    | A tároló képe a `es-ES` területi beállítással `es-ES-Laura-Apollo` és a hanggal.    |
| `1.2.0-amd64-es-es-pablo-apollo-preview`    | A tároló képe a `es-ES` területi beállítással `es-ES-Pablo-Apollo` és a hanggal.    |
| `1.2.0-amd64-es-mx-hildarus-preview`        | A tároló képe a `es-MX` területi beállítással `es-MX-HildaRUS` és a hanggal.        |
| `1.2.0-amd64-es-mx-raul-apollo-preview`     | A tároló képe a `es-MX` területi beállítással `es-MX-Raul-Apollo` és a hanggal.     |
| `1.2.0-amd64-fr-ca-caroline-preview`        | A tároló képe a `fr-CA` területi beállítással `fr-CA-Caroline` és a hanggal.        |
| `1.2.0-amd64-fr-ca-harmonierus-preview`     | A tároló képe a `fr-CA` területi beállítással `fr-CA-HarmonieRUS` és a hanggal.     |
| `1.2.0-amd64-fr-fr-hortenserus-preview`     | A tároló képe a `fr-FR` területi beállítással `fr-FR-HortenseRUS` és a hanggal.     |
| `1.2.0-amd64-fr-fr-julie-apollo-preview`    | A tároló képe a `fr-FR` területi beállítással `fr-FR-Julie-Apollo` és a hanggal.    |
| `1.2.0-amd64-fr-fr-paul-apollo-preview`     | A tároló képe a `fr-FR` területi beállítással `fr-FR-Paul-Apollo` és a hanggal.     |
| `1.2.0-amd64-it-it-cosimo-apollo-preview`   | A tároló képe a `it-IT` területi beállítással `it-IT-Cosimo-Apollo` és a hanggal.   |
| `1.2.0-amd64-it-it-luciarus-preview`        | A tároló képe a `it-IT` területi beállítással `it-IT-LuciaRUS` és a hanggal.        |
| `1.2.0-amd64-ja-jp-ayumi-apollo-preview`    | A tároló képe a `ja-JP` területi beállítással `ja-JP-Ayumi-Apollo` és a hanggal.    |
| `1.2.0-amd64-ja-jp-harukarus-preview`       | A tároló képe a `ja-JP` területi beállítással `ja-JP-HarukaRUS` és a hanggal.       |
| `1.2.0-amd64-ja-jp-ichiro-apollo-preview`   | A tároló képe a `ja-JP` területi beállítással `ja-JP-Ichiro-Apollo` és a hanggal.   |
| `1.2.0-amd64-ko-kr-heamirus-preview`        | A tároló képe a `ko-KR` területi beállítással `ko-KR-HeamiRUS` és a hanggal.        |
| `1.2.0-amd64-pt-br-daniel-apollo-preview`   | A tároló képe a `pt-BR` területi beállítással `pt-BR-Daniel-Apollo` és a hanggal.   |
| `1.2.0-amd64-pt-br-heloisarus-preview`      | A tároló képe a `pt-BR` területi beállítással `pt-BR-HeloisaRUS` és a hanggal.      |
| `1.2.0-amd64-zh-cn-huihuirus-preview`       | A tároló képe a `zh-CN` területi beállítással `zh-CN-HuihuiRUS` és a hanggal.       |
| `1.2.0-amd64-zh-cn-kangkang-apollo-preview` | A tároló képe a `zh-CN` területi beállítással `zh-CN-Kangkang-Apollo` és a hanggal. |
| `1.2.0-amd64-zh-cn-yaoyao-apollo-preview`   | A tároló képe a `zh-CN` területi beállítással `zh-CN-Yaoyao-Apollo` és a hanggal.   |
| `1.1.0-amd64-de-de-hedda-preview`           | A tároló képe a `de-DE` területi beállítással `de-DE-Hedda` és a hanggal.           |
| `1.1.0-amd64-de-de-heddarus-preview`        | A tároló képe a `de-DE` területi beállítással `de-DE-Hedda` és a hanggal.           |
| `1.1.0-amd64-de-de-heddarus-preview`        | A tároló képe a `de-DE` területi beállítással `de-DE-HeddaRUS` és a hanggal.        |
| `1.1.0-amd64-de-de-stefan-apollo-preview`   | A tároló képe a `de-DE` területi beállítással `de-DE-Stefan-Apollo` és a hanggal.   |
| `1.1.0-amd64-en-au-catherine-preview`       | A tároló képe a `en-AU` területi beállítással `en-AU-Catherine` és a hanggal.       |
| `1.1.0-amd64-en-au-hayleyrus-preview`       | A tároló képe a `en-AU` területi beállítással `en-AU-HayleyRUS` és a hanggal.       |
| `1.1.0-amd64-en-gb-george-apollo-preview`   | A tároló képe a `en-GB` területi beállítással `en-GB-George-Apollo` és a hanggal.   |
| `1.1.0-amd64-en-gb-hazelrus-preview`        | A tároló képe a `en-GB` területi beállítással `en-GB-HazelRUS` és a hanggal.        |
| `1.1.0-amd64-en-gb-susan-apollo-preview`    | A tároló képe a `en-GB` területi beállítással `en-GB-Susan-Apollo` és a hanggal.    |
| `1.1.0-amd64-en-in-heera-apollo-preview`    | A tároló képe a `en-IN` területi beállítással `en-IN-Heera-Apollo` és a hanggal.    |
| `1.1.0-amd64-en-in-priyarus-preview`        | A tároló képe a `en-IN` területi beállítással `en-IN-PriyaRUS` és a hanggal.        |
| `1.1.0-amd64-en-in-ravi-apollo-preview`     | A tároló képe a `en-IN` területi beállítással `en-IN-Ravi-Apollo` és a hanggal.     |
| `1.1.0-amd64-en-us-benjaminrus-preview`     | A tároló képe a `en-US` területi beállítással `en-US-BenjaminRUS` és a hanggal.     |
| `1.1.0-amd64-en-us-guy24krus-preview`       | A tároló képe a `en-US` területi beállítással `en-US-Guy24kRUS` és a hanggal.       |
| `1.1.0-amd64-en-us-jessa24krus-preview`     | A tároló képe a `en-US` területi beállítással `en-US-Jessa24kRUS` és a hanggal.     |
| `1.1.0-amd64-en-us-jessarus-preview`        | A tároló képe a `en-US` területi beállítással `en-US-JessaRUS` és a hanggal.        |
| `1.1.0-amd64-en-us-zirarus-preview`         | A tároló képe a `en-US` területi beállítással `en-US-ZiraRUS` és a hanggal.         |
| `1.1.0-amd64-es-es-helenarus-preview`       | A tároló képe a `es-ES` területi beállítással `es-ES-HelenaRUS` és a hanggal.       |
| `1.1.0-amd64-es-es-laura-apollo-preview`    | A tároló képe a `es-ES` területi beállítással `es-ES-Laura-Apollo` és a hanggal.    |
| `1.1.0-amd64-es-es-pablo-apollo-preview`    | A tároló képe a `es-ES` területi beállítással `es-ES-Pablo-Apollo` és a hanggal.    |
| `1.1.0-amd64-es-mx-hildarus-preview`        | A tároló képe a `es-MX` területi beállítással `es-MX-HildaRUS` és a hanggal.        |
| `1.1.0-amd64-es-mx-raul-apollo-preview`     | A tároló képe a `es-MX` területi beállítással `es-MX-Raul-Apollo` és a hanggal.     |
| `1.1.0-amd64-fr-ca-caroline-preview`        | A tároló képe a `fr-CA` területi beállítással `fr-CA-Caroline` és a hanggal.        |
| `1.1.0-amd64-fr-ca-harmonierus-preview`     | A tároló képe a `fr-CA` területi beállítással `fr-CA-HarmonieRUS` és a hanggal.     |
| `1.1.0-amd64-fr-fr-hortenserus-preview`     | A tároló képe a `fr-FR` területi beállítással `fr-FR-HortenseRUS` és a hanggal.     |
| `1.1.0-amd64-fr-fr-julie-apollo-preview`    | A tároló képe a `fr-FR` területi beállítással `fr-FR-Julie-Apollo` és a hanggal.    |
| `1.1.0-amd64-fr-fr-paul-apollo-preview`     | A tároló képe a `fr-FR` területi beállítással `fr-FR-Paul-Apollo` és a hanggal.     |
| `1.1.0-amd64-it-it-cosimo-apollo-preview`   | A tároló képe a `it-IT` területi beállítással `it-IT-Cosimo-Apollo` és a hanggal.   |
| `1.1.0-amd64-it-it-luciarus-preview`        | A tároló képe a `it-IT` területi beállítással `it-IT-LuciaRUS` és a hanggal.        |
| `1.1.0-amd64-ja-jp-ayumi-apollo-preview`    | A tároló képe a `ja-JP` területi beállítással `ja-JP-Ayumi-Apollo` és a hanggal.    |
| `1.1.0-amd64-ja-jp-harukarus-preview`       | A tároló képe a `ja-JP` területi beállítással `ja-JP-HarukaRUS` és a hanggal.       |
| `1.1.0-amd64-ja-jp-ichiro-apollo-preview`   | A tároló képe a `ja-JP` területi beállítással `ja-JP-Ichiro-Apollo` és a hanggal.   |
| `1.1.0-amd64-ko-kr-heamirus-preview`        | A tároló képe a `ko-KR` területi beállítással `ko-KR-HeamiRUS` és a hanggal.        |
| `1.1.0-amd64-pt-br-daniel-apollo-preview`   | A tároló képe a `pt-BR` területi beállítással `pt-BR-Daniel-Apollo` és a hanggal.   |
| `1.1.0-amd64-pt-br-heloisarus-preview`      | A tároló képe a `pt-BR` területi beállítással `pt-BR-HeloisaRUS` és a hanggal.      |
| `1.1.0-amd64-zh-cn-huihuirus-preview`       | A tároló képe a `zh-CN` területi beállítással `zh-CN-HuihuiRUS` és a hanggal.       |
| `1.1.0-amd64-zh-cn-kangkang-apollo-preview` | A tároló képe a `zh-CN` területi beállítással `zh-CN-Kangkang-Apollo` és a hanggal. |
| `1.1.0-amd64-zh-cn-yaoyao-apollo-preview`   | A tároló képe a `zh-CN` területi beállítással `zh-CN-Yaoyao-Apollo` és a hanggal.   |
| `1.0.0-amd64-en-us-benjaminrus-preview`     | A tároló képe a `en-US` területi beállítással `en-US-BenjaminRUS` és a hanggal.     |
| `1.0.0-amd64-en-us-guy24krus-preview`       | A tároló képe a `en-US` területi beállítással `en-US-Guy24kRUS` és a hanggal.       |
| `1.0.0-amd64-en-us-jessa24krus-preview`     | A tároló képe a `en-US` területi beállítással `en-US-Jessa24kRUS` és a hanggal.     |
| `1.0.0-amd64-en-us-jessarus-preview`        | A tároló képe a `en-US` területi beállítással `en-US-JessaRUS` és a hanggal.        |
| `1.0.0-amd64-en-us-zirarus-preview`         | A tároló képe a `en-US` területi beállítással `en-US-ZiraRUS` és a hanggal.         |
| `1.0.0-amd64-zh-cn-huihuirus-preview`       | A tároló képe a `zh-CN` területi beállítással `zh-CN-HuihuiRUS` és a hanggal.       |
| `1.0.0-amd64-zh-cn-kangkang-apollo-preview` | A tároló képe a `zh-CN` területi beállítással `zh-CN-Kangkang-Apollo` és a hanggal. |
| `1.0.0-amd64-zh-cn-yaoyao-apollo-preview`   | A tároló képe a `zh-CN` területi beállítással `zh-CN-Yaoyao-Apollo` és a hanggal.   |

## <a name="key-phrase-extraction"></a>Kulcskifejezések kinyerése

Az [kulcsszókeresés][ta-kp] -tároló rendszerképe megtalálható a `mcr.microsoft.com` Container Registry szindikátusban. A `azure-cognitive-services` tárházban található, és neve `keyphrase`. A teljes tároló rendszerképének neve: `mcr.microsoft.com/azure-cognitive-services/keyphrase`.

Ez a tároló-rendszerkép a következő címkéket tartalmazhatja:

| Képcímkék                    | Megjegyzések |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

## <a name="language-detection"></a>Nyelvfelismerés

Az [nyelvfelismerés][ta-la] -tároló rendszerképe megtalálható a `mcr.microsoft.com` Container Registry szindikátusban. A `azure-cognitive-services` tárházban található, és neve `language`. A teljes tároló rendszerképének neve: `mcr.microsoft.com/azure-cognitive-services/language`.

Ez a tároló-rendszerkép a következő címkéket tartalmazhatja:

| Képcímkék                    | Megjegyzések |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

## <a name="sentiment-analysis"></a>Hangulatelemzés

Az [Hangulatelemzés][ta-se] -tároló rendszerképe megtalálható a `mcr.microsoft.com` Container Registry szindikátusban. A `azure-cognitive-services` tárházban található, és neve `sentiment`. A teljes tároló rendszerképének neve: `mcr.microsoft.com/azure-cognitive-services/sentiment`.

Ez a tároló-rendszerkép a következő címkéket tartalmazhatja:

| Képcímkék                    | Megjegyzések |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-stt]: ../speech-service/speech-container-howto.md?tabs=stt
[sp-cstt]: ../speech-service/speech-container-howto.md?tabs=cstt
[sp-tts]: ../speech-service/speech-container-howto.md?tabs=tts
[sp-ctts]: ../speech-service/speech-container-howto.md?tabs=ctts
[ta-kp]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-la]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-se]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment
