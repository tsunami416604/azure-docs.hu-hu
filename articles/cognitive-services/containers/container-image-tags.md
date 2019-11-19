---
title: Cognitive Services tároló képcímkéi
titleSuffix: Azure Cognitive Services
description: A kognitív szolgáltatás tárolójának összes képcímkéje átfogó felsorolása.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 11/18/2019
ms.author: dapine
ms.openlocfilehash: 0d8c7a36582c30975f3a408a2ea6e95d39e560ef
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173755"
---
# <a name="azure-cognitive-services-container-image-tags"></a>Azure Cognitive Services tároló képcímkék

Az Azure Cognitive Services számos tároló-lemezképet kínál. A tároló-beállításjegyzékek és a megfelelő adattárak a tároló lemezképei között változnak. Mindegyik tároló rendszerképének neve több címkét is kínál. A tároló képcímkéje a tároló rendszerképének verziószámozási mechanizmusa. Ez a cikk átfogó referenciául szolgál az összes Cognitive Services-tároló lemezképének és azok elérhető címkének a listázásához.

> [!TIP]
> [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/)használatakor a tároló-beállításjegyzék, a tárház, a tároló rendszerképének neve és a hozzá tartozó címke (a kis-és **nagybetűk megkülönböztetése**) terület házára figyeljen.

## <a name="anomaly-detector"></a>Anomaly Detector

Az [anomália-detektor][ad-containers] tárolójának képe a `containerpreview.azurecr.io` Container registryben található. A `microsoft` adattáron belül található, és a neve `cognitive-services-anomaly-detector`. A teljes tároló rendszerképének neve `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector`.

Ez a tároló-rendszerkép a következő címkéket tartalmazhatja:

| Képcímkék | Megjegyzések |
|------------|:------|
| `latest` | |
| `1.1.009301-amd64-preview` | |
| `1.1.008500001-amd64-preview` | |

## <a name="computer-vision"></a>Számítógépes látástechnológia

Az [Computer Vision][cv-containers] -tároló rendszerképe megtalálható a `containerpreview.azurecr.io` Container registryben. A `microsoft` adattáron belül található, és a neve `cognitive-services-read`. A teljes tároló rendszerképének neve `containerpreview.azurecr.io/microsoft/cognitive-services-read`.

Ez a tároló-rendszerkép a következő címkéket tartalmazhatja:

| Képcímkék | Megjegyzések |
|------------|:------|
| `latest` | |
| `1.1.009920003-amd64-preview` | |
| `1.1.009910003-amd64-preview` | |

## <a name="face"></a>Arcfelismerés

Az [arc][fa-containers] -tároló képe a `containerpreview.azurecr.io` Container registryben található. A `microsoft` adattáron belül található, és a neve `cognitive-services-face`. A teljes tároló rendszerképének neve `containerpreview.azurecr.io/microsoft/cognitive-services-face`.

Ez a tároló-rendszerkép a következő címkéket tartalmazhatja:

| Képcímkék | Megjegyzések |
|------------|:------|
| `latest` | |
| `1.1.009301-amd64-preview` | |
| `1.1.008710001-amd64-preview` | |
| `1.1.007750002-amd64-preview` | |
| `1.1.007360001-amd64-preview` | |
| `1.1.006770001-amd64-preview` | |
| `1.1.006490002-amd64-preview` | |
| `1.0.005940002-amd64-preview` | |
| `1.0.005550001-amd64-preview` | |

## <a name="form-recognizer"></a>Form Recognizer

Az [űrlap-felismerő][fr-containers] tároló képe megtalálható a `containerpreview.azurecr.io` tároló beállításjegyzékében. A `microsoft` adattáron belül található, és a neve `cognitive-services-form-recognizer`. A teljes tároló rendszerképének neve `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer`.

Ez a tároló-rendszerkép a következő címkéket tartalmazhatja:

| Képcímkék | Megjegyzések |
|------------|:------|
| `latest` | |
| `1.1.009301-amd64-preview` | |
| `1.1.008640001-amd64-preview` | |
| `1.1.008510001-amd64-preview` | |

## <a name="language-understanding-luis"></a>Language Understanding (LUIS)

A [Luis][lu-containers] -tároló képe megtalálható a `mcr.microsoft.com` Container Registry syndicateban. A `azure-cognitive-services` adattáron belül található, és a neve `luis`. A teljes tároló rendszerképének neve `mcr.microsoft.com/azure-cognitive-services/luis`.

Ez a tároló-rendszerkép a következő címkéket tartalmazhatja:

| Képcímkék | Megjegyzések |
|------------|:------|
| `latest` | |
| `1.1.010330004-amd64-preview` | |
| `1.1.009301-amd64-preview` | |
| `1.1.008710001-amd64-preview` | |
| `1.1.008510001-amd64-preview` | |
| `1.1.008010002-amd64-preview` | |
| `1.1.007750002-amd64-preview` | |
| `1.1.007360001-amd64-preview` | |
| `1.1.007020001-amd64-preview` | |

## <a name="custom-speech-to-text"></a>Custom Speech – szöveg

A [Custom Speech – szöveg][sp-cstt] tároló rendszerképet a `containerpreview.azurecr.io` tároló beállításjegyzékében találja. A `microsoft` adattáron belül található, és a neve `cognitive-services-custom-speech-to-text`. A teljes tároló rendszerképének neve `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text`.

Ez a tároló-rendszerkép a következő címkéket tartalmazhatja:

| Képcímkék | Megjegyzések |
|------------|:------|
| `latest` | |
| `2.0.0-amd64-preview` | |

## <a name="custom-text-to-speech"></a>Egyéni szöveg – beszéd

Az [egyéni szöveg-beszéd][sp-ctts] tároló képe a `containerpreview.azurecr.io` Container registryben található. A `microsoft` adattáron belül található, és a neve `cognitive-services-custom-text-to-speech`. A teljes tároló rendszerképének neve `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech`.

Ez a tároló-rendszerkép a következő címkéket tartalmazhatja:

| Képcímkék | Megjegyzések |
|------------|:------|
| `latest` | |
| `1.3.0-amd64-preview` | |

## <a name="speech-to-text"></a>Diktálás

A [beszédfelismerési][sp-stt] tároló rendszerképe megtalálható a `containerpreview.azurecr.io` tároló beállításjegyzékében. A `microsoft` adattáron belül található, és a neve `cognitive-services-speech-to-text`. A teljes tároló rendszerképének neve `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text`.

Ez a tároló-rendszerkép a következő címkéket tartalmazhatja:

| Képcímkék | Megjegyzések |
|------------|:------|
| `latest` | A tároló képe a `en-US` területi beállítással. |
| `2.0.0-amd64-ar-eg-preview` | A tároló képe a `ar-EG` területi beállítással. |
| `2.0.0-amd64-ca-es-preview` | A tároló képe a `ca-ES` területi beállítással. |
| `2.0.0-amd64-da-dk-preview` | A tároló képe a `da-DK` területi beállítással. |
| `2.0.0-amd64-de-de-preview` | A tároló képe a `de-DE` területi beállítással. |
| `2.0.0-amd64-en-au-preview` | A tároló képe a `en-AU` területi beállítással. |
| `2.0.0-amd64-en-ca-preview` | A tároló képe a `en-CA` területi beállítással. |
| `2.0.0-amd64-en-gb-preview` | A tároló képe a `en-GB` területi beállítással. |
| `2.0.0-amd64-en-in-preview` | A tároló képe a `en-IN` területi beállítással. |
| `2.0.0-amd64-en-nz-preview` | A tároló képe a `en-NZ` területi beállítással. |
| `2.0.0-amd64-en-us-preview` | A tároló képe a `en-US` területi beállítással. |
| `2.0.0-amd64-es-es-preview` | A tároló képe a `es-ES` területi beállítással. |
| `2.0.0-amd64-es-mx-preview` | A tároló képe a `es-MX` területi beállítással. |
| `2.0.0-amd64-fi-fi-preview` | A tároló képe a `fi-FI` területi beállítással. |
| `2.0.0-amd64-fr-ca-preview` | A tároló képe a `fr-CA` területi beállítással. |
| `2.0.0-amd64-fr-fr-preview` | A tároló képe a `fr-FR` területi beállítással. |
| `2.0.0-amd64-hi-in-preview` | A tároló képe a `hi-IN` területi beállítással. |
| `2.0.0-amd64-it-it-preview` | A tároló képe a `it-IT` területi beállítással. |
| `2.0.0-amd64-ja-jp-preview` | A tároló képe a `ja-JP` területi beállítással. |
| `2.0.0-amd64-ko-kr-preview` | A tároló képe a `ko-KR` területi beállítással. |
| `2.0.0-amd64-nb-no-preview` | A tároló képe a `nb-NO` területi beállítással. |
| `2.0.0-amd64-nl-nl-preview` | A tároló képe a `nl-NL` területi beállítással. |
| `2.0.0-amd64-pl-pl-preview` | A tároló képe a `pl-PL` területi beállítással. |
| `2.0.0-amd64-pt-br-preview` | A tároló képe a `pt-BR` területi beállítással. |
| `2.0.0-amd64-pt-pt-preview` | A tároló képe a `pt-PT` területi beállítással. |
| `2.0.0-amd64-ru-ru-preview` | A tároló képe a `ru-RU` területi beállítással. |
| `2.0.0-amd64-sv-se-preview` | A tároló képe a `sv-SE` területi beállítással. |
| `2.0.0-amd64-th-th-preview` | A tároló képe a `th-TH` területi beállítással. |
| `2.0.0-amd64-tr-tr-preview` | A tároló képe a `tr-TR` területi beállítással. |
| `2.0.0-amd64-zh-cn-preview` | A tároló képe a `zh-CN` területi beállítással. |
| `2.0.0-amd64-zh-hk-preview` | A tároló képe a `zh-HK` területi beállítással. |
| `2.0.0-amd64-zh-tw-preview` | A tároló képe a `zh-TW` területi beállítással. |
| `1.2.0-amd64-de-de-preview` | A tároló képe a `de-DE` területi beállítással. |
| `1.2.0-amd64-en-au-preview` | A tároló képe a `en-AU` területi beállítással. |
| `1.2.0-amd64-en-ca-preview` | A tároló képe a `en-CA` területi beállítással. |
| `1.2.0-amd64-en-gb-preview` | A tároló képe a `en-GB` területi beállítással. |
| `1.2.0-amd64-en-in-preview` | A tároló képe a `en-IN` területi beállítással. |
| `1.2.0-amd64-en-us-preview` | A tároló képe a `en-US` területi beállítással. |
| `1.2.0-amd64-es-es-preview` | A tároló képe a `es-ES` területi beállítással. |
| `1.2.0-amd64-es-mx-preview` | A tároló képe a `es-MX` területi beállítással. |
| `1.2.0-amd64-fr-ca-preview` | A tároló képe a `fr-CA` területi beállítással. |
| `1.2.0-amd64-fr-fr-preview` | A tároló képe a `fr-FR` területi beállítással. |
| `1.2.0-amd64-it-it-preview` | A tároló képe a `it-IT` területi beállítással. |
| `1.2.0-amd64-ja-jp-preview` | A tároló képe a `ja-JP` területi beállítással. |
| `1.2.0-amd64-pt-br-preview` | A tároló képe a `pt-BR` területi beállítással. |
| `1.2.0-amd64-zh-cn-preview` | A tároló képe a `zh-CN` területi beállítással. |
| `1.1.3-amd64-de-de-preview` | A tároló képe a `de-DE` területi beállítással. |
| `1.1.3-amd64-en-au-preview` | A tároló képe a `en-AU` területi beállítással. |
| `1.1.3-amd64-en-ca-preview` | A tároló képe a `en-CA` területi beállítással. |
| `1.1.3-amd64-en-gb-preview` | A tároló képe a `en-GB` területi beállítással. |
| `1.1.3-amd64-en-in-preview` | A tároló képe a `en-IN` területi beállítással. |
| `1.1.3-amd64-en-us-preview` | A tároló képe a `en-US` területi beállítással. |
| `1.1.3-amd64-es-es-preview` | A tároló képe a `es-ES` területi beállítással. |
| `1.1.3-amd64-es-mx-preview` | A tároló képe a `es-MX` területi beállítással. |
| `1.1.3-amd64-fr-ca-preview` | A tároló képe a `fr-CA` területi beállítással. |
| `1.1.3-amd64-fr-fr-preview` | A tároló képe a `fr-FR` területi beállítással. |
| `1.1.3-amd64-it-it-preview` | A tároló képe a `it-IT` területi beállítással. |
| `1.1.3-amd64-ja-jp-preview` | A tároló képe a `ja-JP` területi beállítással. |
| `1.1.3-amd64-pt-br-preview` | A tároló képe a `pt-BR` területi beállítással. |
| `1.1.3-amd64-zh-cn-preview` | A tároló képe a `zh-CN` területi beállítással. |
| `1.1.2-amd64-de-de-preview` | A tároló képe a `de-DE` területi beállítással. |
| `1.1.2-amd64-en-au-preview` | A tároló képe a `en-AU` területi beállítással. |
| `1.1.2-amd64-en-ca-preview` | A tároló képe a `en-CA` területi beállítással. |
| `1.1.2-amd64-en-gb-preview` | A tároló képe a `en-GB` területi beállítással. |
| `1.1.2-amd64-en-in-preview` | A tároló képe a `en-IN` területi beállítással. |
| `1.1.2-amd64-en-us-preview` | A tároló képe a `en-US` területi beállítással. |
| `1.1.2-amd64-es-es-preview` | A tároló képe a `es-ES` területi beállítással. |
| `1.1.2-amd64-es-mx-preview` | A tároló képe a `es-MX` területi beállítással. |
| `1.1.2-amd64-fr-ca-preview` | A tároló képe a `fr-CA` területi beállítással. |
| `1.1.2-amd64-fr-fr-preview` | A tároló képe a `fr-FR` területi beállítással. |
| `1.1.2-amd64-it-it-preview` | A tároló képe a `it-IT` területi beállítással. |
| `1.1.2-amd64-ja-jp-preview` | A tároló képe a `ja-JP` területi beállítással. |
| `1.1.2-amd64-pt-br-preview` | A tároló képe a `pt-BR` területi beállítással. |
| `1.1.2-amd64-zh-cn-preview` | A tároló képe a `zh-CN` területi beállítással. |
| `1.1.1-amd64-de-de-preview` | A tároló képe a `de-DE` területi beállítással. |
| `1.1.1-amd64-en-au-preview` | A tároló képe a `en-AU` területi beállítással. |
| `1.1.1-amd64-en-ca-preview` | A tároló képe a `en-CA` területi beállítással. |
| `1.1.1-amd64-en-gb-preview` | A tároló képe a `en-GB` területi beállítással. |
| `1.1.1-amd64-en-in-preview` | A tároló képe a `en-IN` területi beállítással. |
| `1.1.1-amd64-en-us-preview` | A tároló képe a `en-US` területi beállítással. |
| `1.1.1-amd64-es-es-preview` | A tároló képe a `es-ES` területi beállítással. |
| `1.1.1-amd64-es-mx-preview` | A tároló képe a `es-MX` területi beállítással. |
| `1.1.1-amd64-fr-ca-preview` | A tároló képe a `fr-CA` területi beállítással. |
| `1.1.1-amd64-fr-fr-preview` | A tároló képe a `fr-FR` területi beállítással. |
| `1.1.1-amd64-it-it-preview` | A tároló képe a `it-IT` területi beállítással. |
| `1.1.1-amd64-ja-jp-preview` | A tároló képe a `ja-JP` területi beállítással. |
| `1.1.1-amd64-pt-br-preview` | A tároló képe a `pt-BR` területi beállítással. |
| `1.1.1-amd64-zh-cn-preview` | A tároló képe a `zh-CN` területi beállítással. |
| `1.1.0-amd64-de-de-preview` | A tároló képe a `de-DE` területi beállítással. |
| `1.1.0-amd64-en-au-preview` | A tároló képe a `en-AU` területi beállítással. |
| `1.1.0-amd64-en-ca-preview` | A tároló képe a `en-CA` területi beállítással. |
| `1.1.0-amd64-en-gb-preview` | A tároló képe a `en-GB` területi beállítással. |
| `1.1.0-amd64-en-in-preview` | A tároló képe a `en-IN` területi beállítással. |
| `1.1.0-amd64-en-us-preview` | A tároló képe a `en-US` területi beállítással. |
| `1.1.0-amd64-es-es-preview` | A tároló képe a `es-ES` területi beállítással. |
| `1.1.0-amd64-es-mx-preview` | A tároló képe a `es-MX` területi beállítással. |
| `1.1.0-amd64-fr-ca-preview` | A tároló képe a `fr-CA` területi beállítással. |
| `1.1.0-amd64-fr-fr-preview` | A tároló képe a `fr-FR` területi beállítással. |
| `1.1.0-amd64-it-it-preview` | A tároló képe a `it-IT` területi beállítással. |
| `1.1.0-amd64-ja-jp-preview` | A tároló képe a `ja-JP` területi beállítással. |
| `1.1.0-amd64-pt-br-preview` | A tároló képe a `pt-BR` területi beállítással. |
| `1.1.0-amd64-zh-cn-preview` | A tároló képe a `zh-CN` területi beállítással. |
| `1.0.0-amd64-de-de-preview` | A tároló képe a `de-DE` területi beállítással. |
| `1.0.0-amd64-en-au-preview` | A tároló képe a `en-AU` területi beállítással. |
| `1.0.0-amd64-en-ca-preview` | A tároló képe a `en-CA` területi beállítással. |
| `1.0.0-amd64-en-gb-preview` | A tároló képe a `en-GB` területi beállítással. |
| `1.0.0-amd64-en-in-preview` | A tároló képe a `en-IN` területi beállítással. |
| `1.0.0-amd64-en-us-preview` | A tároló képe a `en-US` területi beállítással. |
| `1.0.0-amd64-es-es-preview` | A tároló képe a `es-ES` területi beállítással. |
| `1.0.0-amd64-es-mx-preview` | A tároló képe a `es-MX` területi beállítással. |
| `1.0.0-amd64-fr-ca-preview` | A tároló képe a `fr-CA` területi beállítással. |
| `1.0.0-amd64-fr-fr-preview` | A tároló képe a `fr-FR` területi beállítással. |
| `1.0.0-amd64-it-it-preview` | A tároló képe a `it-IT` területi beállítással. |
| `1.0.0-amd64-ja-jp-preview` | A tároló képe a `ja-JP` területi beállítással. |
| `1.0.0-amd64-pt-br-preview` | A tároló képe a `pt-BR` területi beállítással. |
| `1.0.0-amd64-zh-cn-preview` | A tároló képe a `zh-CN` területi beállítással. |

## <a name="text-to-speech"></a>Szövegfelolvasás

A [szöveg – beszéd][sp-tts] tároló képe megtalálható a `containerpreview.azurecr.io` tároló beállításjegyzékében. A `microsoft` adattáron belül található, és a neve `cognitive-services-text-to-speech`. A teljes tároló rendszerképének neve `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech`.

Ez a tároló-rendszerkép a következő címkéket tartalmazhatja:

| Képcímkék | Megjegyzések |
|------------|:------|
| `latest` | A tároló képe a `en-US` területi beállítással és a `en-US-JessaRUS` hanggal. |
| `1.3.0-amd64-ar-eg-hoda-preview` | A tároló képe a `ar-EG` területi beállítással és a `ar-EG-Hoda` hanggal. |
| `1.3.0-amd64-ar-sa-naayf-preview` | A tároló képe a `ar-SA` területi beállítással és a `ar-SA-Naayf` hanggal. |
| `1.3.0-amd64-bg-bg-ivan-preview` | A tároló képe a `bg-BG` területi beállítással és a `bg-BG-Ivan` hanggal. |
| `1.3.0-amd64-ca-es-herenarus-preview` | A tároló képe a `ca-ES` területi beállítással és a `ca-ES-HerenaRUS` hanggal. |
| `1.3.0-amd64-cs-cz-jakub-preview` | A tároló képe a `cs-CZ` területi beállítással és a `cs-CZ-Jakub` hanggal. |
| `1.3.0-amd64-da-dk-hellerus-preview` | A tároló képe a `da-DK` területi beállítással és a `da-DK-HelleRUS` hanggal. |
| `1.3.0-amd64-de-at-michael-preview` | A tároló képe a `de-AT` területi beállítással és a `de-AT-Michael` hanggal. |
| `1.3.0-amd64-de-ch-karsten-preview` | A tároló képe a `de-CH` területi beállítással és a `de-CH-Karsten` hanggal. |
| `1.3.0-amd64-de-de-hedda-preview` | A tároló képe a `de-DE` területi beállítással és a `de-DE-Hedda` hanggal. |
| `1.3.0-amd64-de-de-heddarus-preview` | A tároló képe a `de-DE` területi beállítással és a `de-DE-Hedda` hanggal. |
| `1.3.0-amd64-de-de-heddarus-preview` | A tároló képe a `de-DE` területi beállítással és a `de-DE-HeddaRUS` hanggal. |
| `1.3.0-amd64-de-de-stefan-apollo-preview` | A tároló képe a `de-DE` területi beállítással és a `de-DE-Stefan-Apollo` hanggal. |
| `1.3.0-amd64-el-gr-stefanos-preview` | A tároló képe a `el-GR` területi beállítással és a `el-GR-Stefanos` hanggal. |
| `1.3.0-amd64-en-au-catherine-preview` | A tároló képe a `en-AU` területi beállítással és a `en-AU-Catherine` hanggal. |
| `1.3.0-amd64-en-au-hayleyrus-preview` | A tároló képe a `en-AU` területi beállítással és a `en-AU-HayleyRUS` hanggal. |
| `1.3.0-amd64-en-ca-heatherrus-preview` | A tároló képe a `en-CA` területi beállítással és a `en-CA-HeatherRUS` hanggal. |
| `1.3.0-amd64-en-ca-linda-preview` | A tároló képe a `en-CA` területi beállítással és a `en-CA-Linda` hanggal. |
| `1.3.0-amd64-en-gb-george-apollo-preview` | A tároló képe a `en-GB` területi beállítással és a `en-GB-George-Apollo` hanggal. |
| `1.3.0-amd64-en-gb-hazelrus-preview` | A tároló képe a `en-GB` területi beállítással és a `en-GB-HazelRUS` hanggal. |
| `1.3.0-amd64-en-gb-susan-apollo-preview` | A tároló képe a `en-GB` területi beállítással és a `en-GB-Susan-Apollo` hanggal. |
| `1.3.0-amd64-en-ie-sean-preview` | A tároló képe a `en-IE` területi beállítással és a `en-IE-Sean` hanggal. |
| `1.3.0-amd64-en-in-heera-apollo-preview` | A tároló képe a `en-IN` területi beállítással és a `en-IN-Heera-Apollo` hanggal. |
| `1.3.0-amd64-en-in-priyarus-preview` | A tároló képe a `en-IN` területi beállítással és a `en-IN-PriyaRUS` hanggal. |
| `1.3.0-amd64-en-in-ravi-apollo-preview` | A tároló képe a `en-IN` területi beállítással és a `en-IN-Ravi-Apollo` hanggal. |
| `1.3.0-amd64-en-us-benjaminrus-preview` | A tároló képe a `en-US` területi beállítással és a `en-US-BenjaminRUS` hanggal. |
| `1.3.0-amd64-en-us-guy24krus-preview` | A tároló képe a `en-US` területi beállítással és a `en-US-Guy24kRUS` hanggal. |
| `1.3.0-amd64-en-us-jessa24krus-preview` | A tároló képe a `en-US` területi beállítással és a `en-US-Jessa24kRUS` hanggal. |
| `1.3.0-amd64-en-us-jessarus-preview` | A tároló képe a `en-US` területi beállítással és a `en-US-JessaRUS` hanggal. |
| `1.3.0-amd64-en-us-zirarus-preview` | A tároló képe a `en-US` területi beállítással és a `en-US-ZiraRUS` hanggal. |
| `1.3.0-amd64-es-es-helenarus-preview` | A tároló képe a `es-ES` területi beállítással és a `es-ES-HelenaRUS` hanggal. |
| `1.3.0-amd64-es-es-laura-apollo-preview` | A tároló képe a `es-ES` területi beállítással és a `es-ES-Laura-Apollo` hanggal. |
| `1.3.0-amd64-es-es-pablo-apollo-preview` | A tároló képe a `es-ES` területi beállítással és a `es-ES-Pablo-Apollo` hanggal. |
| `1.3.0-amd64-es-mx-hildarus-preview` | A tároló képe a `es-MX` területi beállítással és a `es-MX-HildaRUS` hanggal. |
| `1.3.0-amd64-es-mx-raul-apollo-preview` | A tároló képe a `es-MX` területi beállítással és a `es-MX-Raul-Apollo` hanggal. |
| `1.3.0-amd64-fi-fi-heidirus-preview` | A tároló képe a `fi-FI` területi beállítással és a `fi-FI-HeidiRUS` hanggal. |
| `1.3.0-amd64-fr-ca-caroline-preview` | A tároló képe a `fr-CA` területi beállítással és a `fr-CA-Caroline` hanggal. |
| `1.3.0-amd64-fr-ca-harmonierus-preview` | A tároló képe a `fr-CA` területi beállítással és a `fr-CA-HarmonieRUS` hanggal. |
| `1.3.0-amd64-fr-ch-guillaume-preview` | A tároló képe a `fr-CH` területi beállítással és a `fr-CH-Guillaume` hanggal. |
| `1.3.0-amd64-fr-fr-hortenserus-preview` | A tároló képe a `fr-FR` területi beállítással és a `fr-FR-HortenseRUS` hanggal. |
| `1.3.0-amd64-fr-fr-julie-apollo-preview` | A tároló képe a `fr-FR` területi beállítással és a `fr-FR-Julie-Apollo` hanggal. |
| `1.3.0-amd64-fr-fr-paul-apollo-preview` | A tároló képe a `fr-FR` területi beállítással és a `fr-FR-Paul-Apollo` hanggal. |
| `1.3.0-amd64-he-il-asaf-preview` | A tároló képe a `he-IL` területi beállítással és a `he-IL-Asaf` hanggal. |
| `1.3.0-amd64-hi-in-hemant-preview` | A tároló képe a `hi-IN` területi beállítással és a `hi-IN-Hemant` hanggal. |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview` | A tároló képe a `hi-IN` területi beállítással és a `hi-IN-Kalpana-Apollo` hanggal. |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview` | A tároló képe a `hi-IN` területi beállítással és a `hi-IN-Kalpana` hanggal. |
| `1.3.0-amd64-hi-in-kalpana-preview` | A tároló képe a `hi-IN` területi beállítással és a `hi-IN-Kalpana` hanggal. |
| `1.3.0-amd64-hr-hr-matej-preview` | A tároló képe a `hr-HR` területi beállítással és a `hr-HR-Matej` hanggal. |
| `1.3.0-amd64-hu-hu-szabolcs-preview` | A tároló képe a `hu-HU` területi beállítással és a `hu-HU-Szabolcs` hanggal. |
| `1.3.0-amd64-id-id-andika-preview` | A tároló képe a `id-ID` területi beállítással és a `id-ID-Andika` hanggal. |
| `1.3.0-amd64-it-it-cosimo-apollo-preview` | A tároló képe a `it-IT` területi beállítással és a `it-IT-Cosimo-Apollo` hanggal. |
| `1.3.0-amd64-it-it-luciarus-preview` | A tároló képe a `it-IT` területi beállítással és a `it-IT-LuciaRUS` hanggal. |
| `1.3.0-amd64-ja-jp-ayumi-apollo-preview` | A tároló képe a `ja-JP` területi beállítással és a `ja-JP-Ayumi-Apollo` hanggal. |
| `1.3.0-amd64-ja-jp-harukarus-preview` | A tároló képe a `ja-JP` területi beállítással és a `ja-JP-HarukaRUS` hanggal. |
| `1.3.0-amd64-ja-jp-ichiro-apollo-preview` | A tároló képe a `ja-JP` területi beállítással és a `ja-JP-Ichiro-Apollo` hanggal. |
| `1.3.0-amd64-ko-kr-heamirus-preview` | A tároló képe a `ko-KR` területi beállítással és a `ko-KR-HeamiRUS` hanggal. |
| `1.3.0-amd64-ms-my-rizwan-preview` | A tároló képe a `ms-MY` területi beállítással és a `ms-MY-Rizwan` hanggal. |
| `1.3.0-amd64-nb-no-huldarus-preview` | A tároló képe a `nb-NO` területi beállítással és a `nb-NO-HuldaRUS` hanggal. |
| `1.3.0-amd64-nl-nl-hannarus-preview` | A tároló képe a `nl-NL` területi beállítással és a `nl-NL-HannaRUS` hanggal. |
| `1.3.0-amd64-pl-pl-paulinarus-preview` | A tároló képe a `pl-PL` területi beállítással és a `pl-PL-PaulinaRUS` hanggal. |
| `1.3.0-amd64-pt-br-daniel-apollo-preview` | A tároló képe a `pt-BR` területi beállítással és a `pt-BR-Daniel-Apollo` hanggal. |
| `1.3.0-amd64-pt-br-heloisarus-preview` | A tároló képe a `pt-BR` területi beállítással és a `pt-BR-HeloisaRUS` hanggal. |
| `1.3.0-amd64-pt-pt-heliarus-preview` | A tároló képe a `pt-PT` területi beállítással és a `pt-PT-HeliaRUS` hanggal. |
| `1.3.0-amd64-ro-ro-andrei-preview` | A tároló képe a `ro-RO` területi beállítással és a `ro-RO-Andrei` hanggal. |
| `1.3.0-amd64-ru-ru-ekaterinarus-preview` | A tároló képe a `ru-RU` területi beállítással és a `ru-RU-EkaterinaRUS` hanggal. |
| `1.3.0-amd64-ru-ru-irina-apollo-preview` | A tároló képe a `ru-RU` területi beállítással és a `ru-RU-Irina-Apollo` hanggal. |
| `1.3.0-amd64-ru-ru-pavel-apollo-preview` | A tároló képe a `ru-RU` területi beállítással és a `ru-RU-Pavel-Apollo` hanggal. |
| `1.3.0-amd64-sk-sk-filip-preview` | A tároló képe a `sk-SK` területi beállítással és a `sk-SK-Filip` hanggal. |
| `1.3.0-amd64-sl-si-lado-preview` | A tároló képe a `sl-SI` területi beállítással és a `sl-SI-Lado` hanggal. |
| `1.3.0-amd64-sv-se-hedvigrus-preview` | A tároló képe a `sv-SE` területi beállítással és a `sv-SE-HedvigRUS` hanggal. |
| `1.3.0-amd64-ta-in-valluvar-preview` | A tároló képe a `ta-IN` területi beállítással és a `ta-IN-Valluvar` hanggal. |
| `1.3.0-amd64-te-in-chitra-preview` | A tároló képe a `te-IN` területi beállítással és a `te-IN-Chitra` hanggal. |
| `1.3.0-amd64-th-th-pattara-preview` | A tároló képe a `th-TH` területi beállítással és a `th-TH-Pattara` hanggal. |
| `1.3.0-amd64-tr-tr-sedarus-preview` | A tároló képe a `tr-TR` területi beállítással és a `tr-TR-SedaRUS` hanggal. |
| `1.3.0-amd64-vi-vn-an-preview` | A tároló képe a `vi-VN` területi beállítással és a `vi-VN-An` hanggal. |
| `1.3.0-amd64-zh-cn-huihuirus-preview` | A tároló képe a `zh-CN` területi beállítással és a `zh-CN-HuihuiRUS` hanggal. |
| `1.3.0-amd64-zh-cn-kangkang-apollo-preview` | A tároló képe a `zh-CN` területi beállítással és a `zh-CN-Kangkang-Apollo` hanggal. |
| `1.3.0-amd64-zh-cn-yaoyao-apollo-preview` | A tároló képe a `zh-CN` területi beállítással és a `zh-CN-Yaoyao-Apollo` hanggal. |
| `1.3.0-amd64-zh-hk-danny-apollo-preview` | A tároló képe a `zh-HK` területi beállítással és a `zh-HK-Danny-Apollo` hanggal. |
| `1.3.0-amd64-zh-hk-tracy-apollo-preview` | A tároló képe a `zh-HK` területi beállítással és a `zh-HK-Tracy-Apollo` hanggal. |
| `1.3.0-amd64-zh-hk-tracyrus-preview` | A tároló képe a `zh-HK` területi beállítással és a `zh-HK-TracyRUS` hanggal. |
| `1.3.0-amd64-zh-tw-hanhanrus-preview` | A tároló képe a `zh-TW` területi beállítással és a `zh-TW-HanHanRUS` hanggal. |
| `1.3.0-amd64-zh-tw-yating-apollo-preview` | A tároló képe a `zh-TW` területi beállítással és a `zh-TW-Yating-Apollo` hanggal. |
| `1.3.0-amd64-zh-tw-zhiwei-apollo-preview` | A tároló képe a `zh-TW` területi beállítással és a `zh-TW-Zhiwei-Apollo` hanggal. |
| `1.2.0-amd64-de-de-heddarus-preview` | A tároló képe a `de-DE` területi beállítással és a `de-DE-Hedda` hanggal. |
| `1.2.0-amd64-de-de-heddarus-preview` | A tároló képe a `de-DE` területi beállítással és a `de-DE-HeddaRUS` hanggal. |
| `1.2.0-amd64-de-de-stefan-apollo-preview` | A tároló képe a `de-DE` területi beállítással és a `de-DE-Stefan-Apollo` hanggal. |
| `1.2.0-amd64-en-au-catherine-preview` | A tároló képe a `en-AU` területi beállítással és a `en-AU-Catherine` hanggal. |
| `1.2.0-amd64-en-au-hayleyrus-preview` | A tároló képe a `en-AU` területi beállítással és a `en-AU-HayleyRUS` hanggal. |
| `1.2.0-amd64-en-gb-george-apollo-preview` | A tároló képe a `en-GB` területi beállítással és a `en-GB-George-Apollo` hanggal. |
| `1.2.0-amd64-en-gb-hazelrus-preview` | A tároló képe a `en-GB` területi beállítással és a `en-GB-HazelRUS` hanggal. |
| `1.2.0-amd64-en-gb-susan-apollo-preview` | A tároló képe a `en-GB` területi beállítással és a `en-GB-Susan-Apollo` hanggal. |
| `1.2.0-amd64-en-in-heera-apollo-preview` | A tároló képe a `en-IN` területi beállítással és a `en-IN-Heera-Apollo` hanggal. |
| `1.2.0-amd64-en-in-priyarus-preview` | A tároló képe a `en-IN` területi beállítással és a `en-IN-PriyaRUS` hanggal. |
| `1.2.0-amd64-en-in-ravi-apollo-preview` | A tároló képe a `en-IN` területi beállítással és a `en-IN-Ravi-Apollo` hanggal. |
| `1.2.0-amd64-en-us-benjaminrus-preview` | A tároló képe a `en-US` területi beállítással és a `en-US-BenjaminRUS` hanggal. |
| `1.2.0-amd64-en-us-guy24krus-preview` | A tároló képe a `en-US` területi beállítással és a `en-US-Guy24kRUS` hanggal. |
| `1.2.0-amd64-en-us-jessa24krus-preview` | A tároló képe a `en-US` területi beállítással és a `en-US-Jessa24kRUS` hanggal. |
| `1.2.0-amd64-en-us-jessarus-preview` | A tároló képe a `en-US` területi beállítással és a `en-US-JessaRUS` hanggal. |
| `1.2.0-amd64-en-us-zirarus-preview` | A tároló képe a `en-US` területi beállítással és a `en-US-ZiraRUS` hanggal. |
| `1.2.0-amd64-es-es-helenarus-preview` | A tároló képe a `es-ES` területi beállítással és a `es-ES-HelenaRUS` hanggal. |
| `1.2.0-amd64-es-es-laura-apollo-preview` | A tároló képe a `es-ES` területi beállítással és a `es-ES-Laura-Apollo` hanggal. |
| `1.2.0-amd64-es-es-pablo-apollo-preview` | A tároló képe a `es-ES` területi beállítással és a `es-ES-Pablo-Apollo` hanggal. |
| `1.2.0-amd64-es-mx-hildarus-preview` | A tároló képe a `es-MX` területi beállítással és a `es-MX-HildaRUS` hanggal. |
| `1.2.0-amd64-es-mx-raul-apollo-preview` | A tároló képe a `es-MX` területi beállítással és a `es-MX-Raul-Apollo` hanggal. |
| `1.2.0-amd64-fr-ca-caroline-preview` | A tároló képe a `fr-CA` területi beállítással és a `fr-CA-Caroline` hanggal. |
| `1.2.0-amd64-fr-ca-harmonierus-preview` | A tároló képe a `fr-CA` területi beállítással és a `fr-CA-HarmonieRUS` hanggal. |
| `1.2.0-amd64-fr-fr-hortenserus-preview` | A tároló képe a `fr-FR` területi beállítással és a `fr-FR-HortenseRUS` hanggal. |
| `1.2.0-amd64-fr-fr-julie-apollo-preview` | A tároló képe a `fr-FR` területi beállítással és a `fr-FR-Julie-Apollo` hanggal. |
| `1.2.0-amd64-fr-fr-paul-apollo-preview` | A tároló képe a `fr-FR` területi beállítással és a `fr-FR-Paul-Apollo` hanggal. |
| `1.2.0-amd64-it-it-cosimo-apollo-preview` | A tároló képe a `it-IT` területi beállítással és a `it-IT-Cosimo-Apollo` hanggal. |
| `1.2.0-amd64-it-it-luciarus-preview` | A tároló képe a `it-IT` területi beállítással és a `it-IT-LuciaRUS` hanggal. |
| `1.2.0-amd64-ja-jp-ayumi-apollo-preview` | A tároló képe a `ja-JP` területi beállítással és a `ja-JP-Ayumi-Apollo` hanggal. |
| `1.2.0-amd64-ja-jp-harukarus-preview` | A tároló képe a `ja-JP` területi beállítással és a `ja-JP-HarukaRUS` hanggal. |
| `1.2.0-amd64-ja-jp-ichiro-apollo-preview` | A tároló képe a `ja-JP` területi beállítással és a `ja-JP-Ichiro-Apollo` hanggal. |
| `1.2.0-amd64-ko-kr-heamirus-preview` | A tároló képe a `ko-KR` területi beállítással és a `ko-KR-HeamiRUS` hanggal. |
| `1.2.0-amd64-pt-br-daniel-apollo-preview` | A tároló képe a `pt-BR` területi beállítással és a `pt-BR-Daniel-Apollo` hanggal. |
| `1.2.0-amd64-pt-br-heloisarus-preview` | A tároló képe a `pt-BR` területi beállítással és a `pt-BR-HeloisaRUS` hanggal. |
| `1.2.0-amd64-zh-cn-huihuirus-preview` | A tároló képe a `zh-CN` területi beállítással és a `zh-CN-HuihuiRUS` hanggal. |
| `1.2.0-amd64-zh-cn-kangkang-apollo-preview` | A tároló képe a `zh-CN` területi beállítással és a `zh-CN-Kangkang-Apollo` hanggal. |
| `1.2.0-amd64-zh-cn-yaoyao-apollo-preview` | A tároló képe a `zh-CN` területi beállítással és a `zh-CN-Yaoyao-Apollo` hanggal. |
| `1.1.0-amd64-de-de-hedda-preview` | A tároló képe a `de-DE` területi beállítással és a `de-DE-Hedda` hanggal. |
| `1.1.0-amd64-de-de-heddarus-preview` | A tároló képe a `de-DE` területi beállítással és a `de-DE-Hedda` hanggal. |
| `1.1.0-amd64-de-de-heddarus-preview` | A tároló képe a `de-DE` területi beállítással és a `de-DE-HeddaRUS` hanggal. |
| `1.1.0-amd64-de-de-stefan-apollo-preview` | A tároló képe a `de-DE` területi beállítással és a `de-DE-Stefan-Apollo` hanggal. |
| `1.1.0-amd64-en-au-catherine-preview` | A tároló képe a `en-AU` területi beállítással és a `en-AU-Catherine` hanggal. |
| `1.1.0-amd64-en-au-hayleyrus-preview` | A tároló képe a `en-AU` területi beállítással és a `en-AU-HayleyRUS` hanggal. |
| `1.1.0-amd64-en-gb-george-apollo-preview` | A tároló képe a `en-GB` területi beállítással és a `en-GB-George-Apollo` hanggal. |
| `1.1.0-amd64-en-gb-hazelrus-preview` | A tároló képe a `en-GB` területi beállítással és a `en-GB-HazelRUS` hanggal. |
| `1.1.0-amd64-en-gb-susan-apollo-preview` | A tároló képe a `en-GB` területi beállítással és a `en-GB-Susan-Apollo` hanggal. |
| `1.1.0-amd64-en-in-heera-apollo-preview` | A tároló képe a `en-IN` területi beállítással és a `en-IN-Heera-Apollo` hanggal. |
| `1.1.0-amd64-en-in-priyarus-preview` | A tároló képe a `en-IN` területi beállítással és a `en-IN-PriyaRUS` hanggal. |
| `1.1.0-amd64-en-in-ravi-apollo-preview` | A tároló képe a `en-IN` területi beállítással és a `en-IN-Ravi-Apollo` hanggal. |
| `1.1.0-amd64-en-us-benjaminrus-preview` | A tároló képe a `en-US` területi beállítással és a `en-US-BenjaminRUS` hanggal. |
| `1.1.0-amd64-en-us-guy24krus-preview` | A tároló képe a `en-US` területi beállítással és a `en-US-Guy24kRUS` hanggal. |
| `1.1.0-amd64-en-us-jessa24krus-preview` | A tároló képe a `en-US` területi beállítással és a `en-US-Jessa24kRUS` hanggal. |
| `1.1.0-amd64-en-us-jessarus-preview` | A tároló képe a `en-US` területi beállítással és a `en-US-JessaRUS` hanggal. |
| `1.1.0-amd64-en-us-zirarus-preview` | A tároló képe a `en-US` területi beállítással és a `en-US-ZiraRUS` hanggal. |
| `1.1.0-amd64-es-es-helenarus-preview` | A tároló képe a `es-ES` területi beállítással és a `es-ES-HelenaRUS` hanggal. |
| `1.1.0-amd64-es-es-laura-apollo-preview` | A tároló képe a `es-ES` területi beállítással és a `es-ES-Laura-Apollo` hanggal. |
| `1.1.0-amd64-es-es-pablo-apollo-preview` | A tároló képe a `es-ES` területi beállítással és a `es-ES-Pablo-Apollo` hanggal. |
| `1.1.0-amd64-es-mx-hildarus-preview` | A tároló képe a `es-MX` területi beállítással és a `es-MX-HildaRUS` hanggal. |
| `1.1.0-amd64-es-mx-raul-apollo-preview` | A tároló képe a `es-MX` területi beállítással és a `es-MX-Raul-Apollo` hanggal. |
| `1.1.0-amd64-fr-ca-caroline-preview` | A tároló képe a `fr-CA` területi beállítással és a `fr-CA-Caroline` hanggal. |
| `1.1.0-amd64-fr-ca-harmonierus-preview` | A tároló képe a `fr-CA` területi beállítással és a `fr-CA-HarmonieRUS` hanggal. |
| `1.1.0-amd64-fr-fr-hortenserus-preview` | A tároló képe a `fr-FR` területi beállítással és a `fr-FR-HortenseRUS` hanggal. |
| `1.1.0-amd64-fr-fr-julie-apollo-preview` | A tároló képe a `fr-FR` területi beállítással és a `fr-FR-Julie-Apollo` hanggal. |
| `1.1.0-amd64-fr-fr-paul-apollo-preview` | A tároló képe a `fr-FR` területi beállítással és a `fr-FR-Paul-Apollo` hanggal. |
| `1.1.0-amd64-it-it-cosimo-apollo-preview` | A tároló képe a `it-IT` területi beállítással és a `it-IT-Cosimo-Apollo` hanggal. |
| `1.1.0-amd64-it-it-luciarus-preview` | A tároló képe a `it-IT` területi beállítással és a `it-IT-LuciaRUS` hanggal. |
| `1.1.0-amd64-ja-jp-ayumi-apollo-preview` | A tároló képe a `ja-JP` területi beállítással és a `ja-JP-Ayumi-Apollo` hanggal. |
| `1.1.0-amd64-ja-jp-harukarus-preview` | A tároló képe a `ja-JP` területi beállítással és a `ja-JP-HarukaRUS` hanggal. |
| `1.1.0-amd64-ja-jp-ichiro-apollo-preview` | A tároló képe a `ja-JP` területi beállítással és a `ja-JP-Ichiro-Apollo` hanggal. |
| `1.1.0-amd64-ko-kr-heamirus-preview` | A tároló képe a `ko-KR` területi beállítással és a `ko-KR-HeamiRUS` hanggal. |
| `1.1.0-amd64-pt-br-daniel-apollo-preview` | A tároló képe a `pt-BR` területi beállítással és a `pt-BR-Daniel-Apollo` hanggal. |
| `1.1.0-amd64-pt-br-heloisarus-preview` | A tároló képe a `pt-BR` területi beállítással és a `pt-BR-HeloisaRUS` hanggal. |
| `1.1.0-amd64-zh-cn-huihuirus-preview` | A tároló képe a `zh-CN` területi beállítással és a `zh-CN-HuihuiRUS` hanggal. |
| `1.1.0-amd64-zh-cn-kangkang-apollo-preview` | A tároló képe a `zh-CN` területi beállítással és a `zh-CN-Kangkang-Apollo` hanggal. |
| `1.1.0-amd64-zh-cn-yaoyao-apollo-preview` | A tároló képe a `zh-CN` területi beállítással és a `zh-CN-Yaoyao-Apollo` hanggal. |
| `1.0.0-amd64-en-us-benjaminrus-preview` | A tároló képe a `en-US` területi beállítással és a `en-US-BenjaminRUS` hanggal. |
| `1.0.0-amd64-en-us-guy24krus-preview` | A tároló képe a `en-US` területi beállítással és a `en-US-Guy24kRUS` hanggal. |
| `1.0.0-amd64-en-us-jessa24krus-preview` | A tároló képe a `en-US` területi beállítással és a `en-US-Jessa24kRUS` hanggal. |
| `1.0.0-amd64-en-us-jessarus-preview` | A tároló képe a `en-US` területi beállítással és a `en-US-JessaRUS` hanggal. |
| `1.0.0-amd64-en-us-zirarus-preview` | A tároló képe a `en-US` területi beállítással és a `en-US-ZiraRUS` hanggal. |
| `1.0.0-amd64-zh-cn-huihuirus-preview` | A tároló képe a `zh-CN` területi beállítással és a `zh-CN-HuihuiRUS` hanggal. |
| `1.0.0-amd64-zh-cn-kangkang-apollo-preview` | A tároló képe a `zh-CN` területi beállítással és a `zh-CN-Kangkang-Apollo` hanggal. |
| `1.0.0-amd64-zh-cn-yaoyao-apollo-preview` | A tároló képe a `zh-CN` területi beállítással és a `zh-CN-Yaoyao-Apollo` hanggal. |

## <a name="key-phrase-extraction"></a>Kulcskifejezések kinyerése

Az [kulcsszókeresés][ta-kp] -tároló rendszerképe megtalálható a `mcr.microsoft.com` Container Registry syndicateban. A `azure-cognitive-services` adattáron belül található, és a neve `keyphrase`. A teljes tároló rendszerképének neve `mcr.microsoft.com/azure-cognitive-services/keyphrase`.

Ez a tároló-rendszerkép a következő címkéket tartalmazhatja:

| Képcímkék | Megjegyzések |
|------------|:------|
| `latest` | |
| `1.1.009301-amd64-preview` | |
| `1.1.008510001-amd64-preview` | |
| `1.1.007750002-amd64-preview` | |
| `1.1.007360001-amd64-preview` | |
| `1.1.006770001-amd64-preview` | |

## <a name="language-detection"></a>Nyelvfelismerés

Az [nyelvfelismerés][ta-la] -tároló rendszerképe megtalálható a `mcr.microsoft.com` Container Registry syndicateban. A `azure-cognitive-services` adattáron belül található, és a neve `language`. A teljes tároló rendszerképének neve `mcr.microsoft.com/azure-cognitive-services/language`.

Ez a tároló-rendszerkép a következő címkéket tartalmazhatja:

| Képcímkék | Megjegyzések |
|------------|:------|
| `latest` | |
| `1.1.009301-amd64-preview` | |
| `1.1.008510001-amd64-preview` | |
| `1.1.007750002-amd64-preview` | |
| `1.1.007360001-amd64-preview` | |
| `1.1.006770001-amd64-preview` | |

## <a name="sentiment-analysis"></a>Véleményelemzés

Az [Hangulatelemzés][ta-se] -tároló rendszerképe megtalálható a `mcr.microsoft.com` Container Registry syndicateban. A `azure-cognitive-services` adattáron belül található, és a neve `sentiment`. A teljes tároló rendszerképének neve `mcr.microsoft.com/azure-cognitive-services/sentiment`.

Ez a tároló-rendszerkép a következő címkéket tartalmazhatja:

| Képcímkék | Megjegyzések |
|------------|:------|
| `latest` | |
| `1.1.009301-amd64-preview` | |
| `1.1.008510001-amd64-preview` | |
| `1.1.007750002-amd64-preview` | |
| `1.1.007360001-amd64-preview` | |
| `1.1.006770001-amd64-preview` | |

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
