---
title: Cognitive Services-tárolók rendszerképcímkéi
titleSuffix: Azure Cognitive Services
description: A Cognitive Service tárolórendszerkép-címkéinek átfogó listája.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: eed2223dbfeee307b552cdd010530f27c379f5fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219444"
---
# <a name="azure-cognitive-services-container-image-tags"></a>Azure Cognitive Services tárolórendszerkép-címkéi

Az Azure Cognitive Services számos tárolórendszerképet kínál. A tároló-jegyzékek és a megfelelő adattárak tárolórendszerképek között változik. Minden tárolókép-kép név több címkét kínál. A tárolórendszerkép-címke a tárolórendszerkép verziószámozásának mechanizmusa. Ez a cikk célja, hogy átfogó referenciaként a Cognitive Services tárolórendszerképek és a rendelkezésre álló címkék listázása.

> [!TIP]
> Használatakor [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/), fordítson különös figyelmet a tároló rendszerleíró adatbázis, tárház, tárolókép neve és a megfelelő címke - mivel ezek a kis- és **nagybetűket.**

## <a name="anomaly-detector"></a>Anomaly Detector

Az [Anomáliadetektor][ad-containers] tárolólemezképe `containerpreview.azurecr.io` megtalálható a tároló beállításjegyzékében. A `microsoft` tárolón belül található, `cognitive-services-anomaly-detector`és a neve . A teljesen minősített tárolókép `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector`neve, .

Ez a tárolórendszerkép a következő címkékkel rendelkezik:

| Képcímkék                    | Megjegyzések |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008500001-amd64-preview` |       |

## <a name="computer-vision"></a>Számítógépes látástechnológia

A [Computer Vision][cv-containers] tárolórendszerkép `containerpreview.azurecr.io` megtalálható a tároló beállításjegyzékében. A `microsoft` tárolón belül található, `cognitive-services-read`és a neve . A teljesen minősített tárolókép `containerpreview.azurecr.io/microsoft/cognitive-services-read`neve, .

Ez a tárolórendszerkép a következő címkékkel rendelkezik:

| Képcímkék                    | Megjegyzések |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.011580001-amd64-preview` |       |
| `1.1.009920003-amd64-preview` |       |
| `1.1.009910003-amd64-preview` |       |

## <a name="face"></a>Arcfelismerés

A [Face][fa-containers] tároló rendszerkép `containerpreview.azurecr.io` megtalálható a tároló rendszerleíró adatbázisában. A `microsoft` tárolón belül található, `cognitive-services-face`és a neve . A teljesen minősített tárolókép `containerpreview.azurecr.io/microsoft/cognitive-services-face`neve, .

Ez a tárolórendszerkép a következő címkékkel rendelkezik:

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

Az [űrlapfelismerő][fr-containers] tárolórendszerképe megtalálható `containerpreview.azurecr.io` a tároló beállításjegyzékében. A `microsoft` tárolón belül található, `cognitive-services-form-recognizer`és a neve . A teljesen minősített tárolókép `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer`neve, .

Ez a tárolórendszerkép a következő címkékkel rendelkezik:

| Képcímkék                    | Megjegyzések |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008640001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |

## <a name="language-understanding-luis"></a>Language Understanding (LUIS)

A [LUIS-tároló][lu-containers] rendszerkép `mcr.microsoft.com` található a tároló beállításjegyzék-szindikátus. A `azure-cognitive-services` tárolón belül található, `luis`és a neve . A teljesen minősített tárolókép `mcr.microsoft.com/azure-cognitive-services/luis`neve, .

Ez a tárolórendszerkép a következő címkékkel rendelkezik:

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

## <a name="custom-speech-to-text"></a>Egyéni beszédfelismerési szöveg

Az [egyéni beszéd-szöveg][sp-cstt] tároló rendszerkép megtalálható `containerpreview.azurecr.io` a tároló beállításjegyzékében. A `microsoft` tárolón belül található, `cognitive-services-custom-speech-to-text`és a neve . A teljesen minősített tárolókép `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text`neve, .

Ez a tárolórendszerkép a következő címkékkel rendelkezik:

| Képcímkék            | Megjegyzések |
|-----------------------|:------|
| `latest`              |       |
| `2.1.1-amd64-preview` |       |
| `2.1.0-amd64-preview` |       |
| `2.0.2-amd64-preview` |       |
| `2.0.0-amd64-preview` |       |

## <a name="custom-text-to-speech"></a>Egyéni szövegfelolvasás

Az [egyéni text-to-speech][sp-ctts] tároló rendszerkép `containerpreview.azurecr.io` megtalálható a tároló beállításjegyzékben. A `microsoft` tárolón belül található, `cognitive-services-custom-text-to-speech`és a neve . A teljesen minősített tárolókép `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech`neve, .

Ez a tárolórendszerkép a következő címkékkel rendelkezik:

| Képcímkék            | Megjegyzések |
|-----------------------|:------|
| `latest`              |       |
| `1.3.0-amd64-preview` |       |

## <a name="speech-to-text"></a>Diktálás

A [beszéd-szöveg][sp-stt] tároló rendszerkép megtalálható `containerpreview.azurecr.io` a tároló beállításjegyzékben. A `microsoft` tárolón belül található, `cognitive-services-speech-to-text`és a neve . A teljesen minősített tárolókép `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text`neve, .

Ez a tárolórendszerkép a következő címkékkel rendelkezik:

| Képcímkék                  | Megjegyzések                                    |
|-----------------------------|:-----------------------------------------|
| `latest`                    | Tárolóképa `en-US` a területi beállítással. |
| `2.1.1-amd64-ar-ae-preview` | Tárolóképa `ar-AE` a területi beállítással. |
| `2.1.1-amd64-ar-eg-preview` | Tárolóképa `ar-EG` a területi beállítással. |
| `2.1.1-amd64-ar-kw-preview` | Tárolóképa `ar-KW` a területi beállítással. |
| `2.1.1-amd64-ar-qa-preview` | Tárolóképa `ar-QA` a területi beállítással. |
| `2.1.1-amd64-ar-sa-preview` | Tárolóképa `ar-SA` a területi beállítással. |
| `2.1.1-amd64-ca-es-preview` | Tárolóképa `ca-ES` a területi beállítással. |
| `2.1.1-amd64-da-dk-preview` | Tárolóképa `da-DK` a területi beállítással. |
| `2.1.1-amd64-de-de-preview` | Tárolóképa `de-DE` a területi beállítással. |
| `2.1.1-amd64-en-au-preview` | Tárolóképa `en-AU` a területi beállítással. |
| `2.1.1-amd64-en-ca-preview` | Tárolóképa `en-CA` a területi beállítással. |
| `2.1.1-amd64-en-gb-preview` | Tárolóképa `en-GB` a területi beállítással. |
| `2.1.1-amd64-en-in-preview` | Tárolóképa `en-IN` a területi beállítással. |
| `2.1.1-amd64-en-nz-preview` | Tárolóképa `en-NZ` a területi beállítással. |
| `2.1.1-amd64-en-us-preview` | Tárolóképa `en-US` a területi beállítással. |
| `2.1.1-amd64-es-es-preview` | Tárolóképa `es-ES` a területi beállítással. |
| `2.1.1-amd64-es-mx-preview` | Tárolóképa `es-MX` a területi beállítással. |
| `2.1.1-amd64-fi-fi-preview` | Tárolóképa `fi-FI` a területi beállítással. |
| `2.1.1-amd64-fr-ca-preview` | Tárolóképa `fr-CA` a területi beállítással. |
| `2.1.1-amd64-fr-fr-preview` | Tárolóképa `fr-FR` a területi beállítással. |
| `2.1.1-amd64-gu-in-preview` | Tárolóképa `gu-IN` a területi beállítással. |
| `2.1.1-amd64-hi-in-preview` | Tárolóképa `hi-IN` a területi beállítással. |
| `2.1.1-amd64-it-it-preview` | Tárolóképa `it-IT` a területi beállítással. |
| `2.1.1-amd64-ja-jp-preview` | Tárolóképa `ja-JP` a területi beállítással. |
| `2.1.1-amd64-ko-kr-preview` | Tárolóképa `ko-KR` a területi beállítással. |
| `2.1.1-amd64-mr-in-preview` | Tárolóképa `mr-IN` a területi beállítással. |
| `2.1.1-amd64-nb-no-preview` | Tárolóképa `nb-NO` a területi beállítással. |
| `2.1.1-amd64-nl-nl-preview` | Tárolóképa `nl-NL` a területi beállítással. |
| `2.1.1-amd64-pl-pl-preview` | Tárolóképa `pl-PL` a területi beállítással. |
| `2.1.1-amd64-pt-br-preview` | Tárolóképa `pt-BR` a területi beállítással. |
| `2.1.1-amd64-pt-pt-preview` | Tárolóképa `pt-PT` a területi beállítással. |
| `2.1.1-amd64-ru-ru-preview` | Tárolóképa `ru-RU` a területi beállítással. |
| `2.1.1-amd64-sv-se-preview` | Tárolóképa `sv-SE` a területi beállítással. |
| `2.1.1-amd64-ta-in-preview` | Tárolóképa `ta-IN` a területi beállítással. |
| `2.1.1-amd64-te-in-preview` | Tárolóképa `te-IN` a területi beállítással. |
| `2.1.1-amd64-th-th-preview` | Tárolóképa `th-TH` a területi beállítással. |
| `2.1.1-amd64-tr-tr-preview` | Tárolóképa `tr-TR` a területi beállítással. |
| `2.1.1-amd64-zh-cn-preview` | Tárolóképa `zh-CN` a területi beállítással. |
| `2.1.1-amd64-zh-hk-preview` | Tárolóképa `zh-HK` a területi beállítással. |
| `2.1.1-amd64-zh-tw-preview` | Tárolóképa `zh-TW` a területi beállítással. |
| `2.1.0-amd64-ar-ae-preview` | Tárolóképa `ar-AE` a területi beállítással. |
| `2.1.0-amd64-ar-eg-preview` | Tárolóképa `ar-EG` a területi beállítással. |
| `2.1.0-amd64-ar-kw-preview` | Tárolóképa `ar-KW` a területi beállítással. |
| `2.1.0-amd64-ar-qa-preview` | Tárolóképa `ar-QA` a területi beállítással. |
| `2.1.0-amd64-ar-sa-preview` | Tárolóképa `ar-SA` a területi beállítással. |
| `2.1.0-amd64-ca-es-preview` | Tárolóképa `ca-ES` a területi beállítással. |
| `2.1.0-amd64-da-dk-preview` | Tárolóképa `da-DK` a területi beállítással. |
| `2.1.0-amd64-de-de-preview` | Tárolóképa `de-DE` a területi beállítással. |
| `2.1.0-amd64-en-au-preview` | Tárolóképa `en-AU` a területi beállítással. |
| `2.1.0-amd64-en-ca-preview` | Tárolóképa `en-CA` a területi beállítással. |
| `2.1.0-amd64-en-gb-preview` | Tárolóképa `en-GB` a területi beállítással. |
| `2.1.0-amd64-en-in-preview` | Tárolóképa `en-IN` a területi beállítással. |
| `2.1.0-amd64-en-nz-preview` | Tárolóképa `en-NZ` a területi beállítással. |
| `2.1.0-amd64-en-us-preview` | Tárolóképa `en-US` a területi beállítással. |
| `2.1.0-amd64-es-es-preview` | Tárolóképa `es-ES` a területi beállítással. |
| `2.1.0-amd64-es-mx-preview` | Tárolóképa `es-MX` a területi beállítással. |
| `2.1.0-amd64-fi-fi-preview` | Tárolóképa `fi-FI` a területi beállítással. |
| `2.1.0-amd64-fr-ca-preview` | Tárolóképa `fr-CA` a területi beállítással. |
| `2.1.0-amd64-fr-fr-preview` | Tárolóképa `fr-FR` a területi beállítással. |
| `2.1.0-amd64-gu-in-preview` | Tárolóképa `gu-IN` a területi beállítással. |
| `2.1.0-amd64-hi-in-preview` | Tárolóképa `hi-IN` a területi beállítással. |
| `2.1.0-amd64-it-it-preview` | Tárolóképa `it-IT` a területi beállítással. |
| `2.1.0-amd64-ja-jp-preview` | Tárolóképa `ja-JP` a területi beállítással. |
| `2.1.0-amd64-ko-kr-preview` | Tárolóképa `ko-KR` a területi beállítással. |
| `2.1.0-amd64-mr-in-preview` | Tárolóképa `mr-IN` a területi beállítással. |
| `2.1.0-amd64-nb-no-preview` | Tárolóképa `nb-NO` a területi beállítással. |
| `2.1.0-amd64-nl-nl-preview` | Tárolóképa `nl-NL` a területi beállítással. |
| `2.1.0-amd64-pl-pl-preview` | Tárolóképa `pl-PL` a területi beállítással. |
| `2.1.0-amd64-pt-br-preview` | Tárolóképa `pt-BR` a területi beállítással. |
| `2.1.0-amd64-pt-pt-preview` | Tárolóképa `pt-PT` a területi beállítással. |
| `2.1.0-amd64-ru-ru-preview` | Tárolóképa `ru-RU` a területi beállítással. |
| `2.1.0-amd64-sv-se-preview` | Tárolóképa `sv-SE` a területi beállítással. |
| `2.1.0-amd64-ta-in-preview` | Tárolóképa `ta-IN` a területi beállítással. |
| `2.1.0-amd64-te-in-preview` | Tárolóképa `te-IN` a területi beállítással. |
| `2.1.0-amd64-th-th-preview` | Tárolóképa `th-TH` a területi beállítással. |
| `2.1.0-amd64-tr-tr-preview` | Tárolóképa `tr-TR` a területi beállítással. |
| `2.1.0-amd64-zh-cn-preview` | Tárolóképa `zh-CN` a területi beállítással. |
| `2.1.0-amd64-zh-hk-preview` | Tárolóképa `zh-HK` a területi beállítással. |
| `2.1.0-amd64-zh-tw-preview` | Tárolóképa `zh-TW` a területi beállítással. |
| `2.0.3-amd64-ja-jp-preview` | Tárolóképa `ja-JP` a területi beállítással. |
| `2.0.2-amd64-ar-ae-preview` | Tárolóképa `ar-AE` a területi beállítással. |
| `2.0.2-amd64-ar-eg-preview` | Tárolóképa `ar-EG` a területi beállítással. |
| `2.0.2-amd64-ar-kw-preview` | Tárolóképa `ar-KW` a területi beállítással. |
| `2.0.2-amd64-ar-qa-preview` | Tárolóképa `ar-QA` a területi beállítással. |
| `2.0.2-amd64-ar-sa-preview` | Tárolóképa `ar-SA` a területi beállítással. |
| `2.0.2-amd64-ca-es-preview` | Tárolóképa `ca-ES` a területi beállítással. |
| `2.0.2-amd64-da-dk-preview` | Tárolóképa `da-DK` a területi beállítással. |
| `2.0.2-amd64-de-de-preview` | Tárolóképa `de-DE` a területi beállítással. |
| `2.0.2-amd64-en-au-preview` | Tárolóképa `en-AU` a területi beállítással. |
| `2.0.2-amd64-en-ca-preview` | Tárolóképa `en-CA` a területi beállítással. |
| `2.0.2-amd64-en-gb-preview` | Tárolóképa `en-GB` a területi beállítással. |
| `2.0.2-amd64-en-in-preview` | Tárolóképa `en-IN` a területi beállítással. |
| `2.0.2-amd64-en-nz-preview` | Tárolóképa `en-NZ` a területi beállítással. |
| `2.0.2-amd64-en-us-preview` | Tárolóképa `en-US` a területi beállítással. |
| `2.0.2-amd64-es-es-preview` | Tárolóképa `es-ES` a területi beállítással. |
| `2.0.2-amd64-es-mx-preview` | Tárolóképa `es-MX` a területi beállítással. |
| `2.0.2-amd64-fi-fi-preview` | Tárolóképa `fi-FI` a területi beállítással. |
| `2.0.2-amd64-fr-ca-preview` | Tárolóképa `fr-CA` a területi beállítással. |
| `2.0.2-amd64-fr-fr-preview` | Tárolóképa `fr-FR` a területi beállítással. |
| `2.0.2-amd64-gu-in-preview` | Tárolóképa `gu-IN` a területi beállítással. |
| `2.0.2-amd64-hi-in-preview` | Tárolóképa `hi-IN` a területi beállítással. |
| `2.0.2-amd64-it-it-preview` | Tárolóképa `it-IT` a területi beállítással. |
| `2.0.2-amd64-ja-jp-preview` | Tárolóképa `ja-JP` a területi beállítással. |
| `2.0.2-amd64-ko-kr-preview` | Tárolóképa `ko-KR` a területi beállítással. |
| `2.0.2-amd64-mr-in-preview` | Tárolóképa `mr-IN` a területi beállítással. |
| `2.0.2-amd64-nb-no-preview` | Tárolóképa `nb-NO` a területi beállítással. |
| `2.0.2-amd64-nl-nl-preview` | Tárolóképa `nl-NL` a területi beállítással. |
| `2.0.2-amd64-pl-pl-preview` | Tárolóképa `pl-PL` a területi beállítással. |
| `2.0.2-amd64-pt-br-preview` | Tárolóképa `pt-BR` a területi beállítással. |
| `2.0.2-amd64-pt-pt-preview` | Tárolóképa `pt-PT` a területi beállítással. |
| `2.0.2-amd64-ru-ru-preview` | Tárolóképa `ru-RU` a területi beállítással. |
| `2.0.2-amd64-sv-se-preview` | Tárolóképa `sv-SE` a területi beállítással. |
| `2.0.2-amd64-ta-in-preview` | Tárolóképa `ta-IN` a területi beállítással. |
| `2.0.2-amd64-te-in-preview` | Tárolóképa `te-IN` a területi beállítással. |
| `2.0.2-amd64-th-th-preview` | Tárolóképa `th-TH` a területi beállítással. |
| `2.0.2-amd64-tr-tr-preview` | Tárolóképa `tr-TR` a területi beállítással. |
| `2.0.2-amd64-zh-cn-preview` | Tárolóképa `zh-CN` a területi beállítással. |
| `2.0.2-amd64-zh-hk-preview` | Tárolóképa `zh-HK` a területi beállítással. |
| `2.0.2-amd64-zh-tw-preview` | Tárolóképa `zh-TW` a területi beállítással. |
| `2.0.1-amd64-ar-ae-preview` | Tárolóképa `ar-AE` a területi beállítással. |
| `2.0.1-amd64-ar-eg-preview` | Tárolóképa `ar-EG` a területi beállítással. |
| `2.0.1-amd64-ar-kw-preview` | Tárolóképa `ar-KW` a területi beállítással. |
| `2.0.1-amd64-ar-qa-preview` | Tárolóképa `ar-QA` a területi beállítással. |
| `2.0.1-amd64-ar-sa-preview` | Tárolóképa `ar-SA` a területi beállítással. |
| `2.0.1-amd64-ca-es-preview` | Tárolóképa `ca-ES` a területi beállítással. |
| `2.0.1-amd64-da-dk-preview` | Tárolóképa `da-DK` a területi beállítással. |
| `2.0.1-amd64-de-de-preview` | Tárolóképa `de-DE` a területi beállítással. |
| `2.0.1-amd64-en-au-preview` | Tárolóképa `en-AU` a területi beállítással. |
| `2.0.1-amd64-en-ca-preview` | Tárolóképa `en-CA` a területi beállítással. |
| `2.0.1-amd64-en-gb-preview` | Tárolóképa `en-GB` a területi beállítással. |
| `2.0.1-amd64-en-in-preview` | Tárolóképa `en-IN` a területi beállítással. |
| `2.0.1-amd64-en-nz-preview` | Tárolóképa `en-NZ` a területi beállítással. |
| `2.0.1-amd64-en-us-preview` | Tárolóképa `en-US` a területi beállítással. |
| `2.0.1-amd64-es-es-preview` | Tárolóképa `es-ES` a területi beállítással. |
| `2.0.1-amd64-es-mx-preview` | Tárolóképa `es-MX` a területi beállítással. |
| `2.0.1-amd64-fi-fi-preview` | Tárolóképa `fi-FI` a területi beállítással. |
| `2.0.1-amd64-fr-ca-preview` | Tárolóképa `fr-CA` a területi beállítással. |
| `2.0.1-amd64-fr-fr-preview` | Tárolóképa `fr-FR` a területi beállítással. |
| `2.0.1-amd64-gu-in-preview` | Tárolóképa `gu-IN` a területi beállítással. |
| `2.0.1-amd64-hi-in-preview` | Tárolóképa `hi-IN` a területi beállítással. |
| `2.0.1-amd64-it-it-preview` | Tárolóképa `it-IT` a területi beállítással. |
| `2.0.1-amd64-ja-jp-preview` | Tárolóképa `ja-JP` a területi beállítással. |
| `2.0.1-amd64-ko-kr-preview` | Tárolóképa `ko-KR` a területi beállítással. |
| `2.0.1-amd64-mr-in-preview` | Tárolóképa `mr-IN` a területi beállítással. |
| `2.0.1-amd64-nb-no-preview` | Tárolóképa `nb-NO` a területi beállítással. |
| `2.0.1-amd64-nl-nl-preview` | Tárolóképa `nl-NL` a területi beállítással. |
| `2.0.1-amd64-pl-pl-preview` | Tárolóképa `pl-PL` a területi beállítással. |
| `2.0.1-amd64-pt-br-preview` | Tárolóképa `pt-BR` a területi beállítással. |
| `2.0.1-amd64-pt-pt-preview` | Tárolóképa `pt-PT` a területi beállítással. |
| `2.0.1-amd64-ru-ru-preview` | Tárolóképa `ru-RU` a területi beállítással. |
| `2.0.1-amd64-sv-se-preview` | Tárolóképa `sv-SE` a területi beállítással. |
| `2.0.1-amd64-ta-in-preview` | Tárolóképa `ta-IN` a területi beállítással. |
| `2.0.1-amd64-te-in-preview` | Tárolóképa `te-IN` a területi beállítással. |
| `2.0.1-amd64-th-th-preview` | Tárolóképa `th-TH` a területi beállítással. |
| `2.0.1-amd64-tr-tr-preview` | Tárolóképa `tr-TR` a területi beállítással. |
| `2.0.1-amd64-zh-cn-preview` | Tárolóképa `zh-CN` a területi beállítással. |
| `2.0.1-amd64-zh-hk-preview` | Tárolóképa `zh-HK` a területi beállítással. |
| `2.0.1-amd64-zh-tw-preview` | Tárolóképa `zh-TW` a területi beállítással. |
| `2.0.0-amd64-ar-eg-preview` | Tárolóképa `ar-EG` a területi beállítással. |
| `2.0.0-amd64-ca-es-preview` | Tárolóképa `ca-ES` a területi beállítással. |
| `2.0.0-amd64-da-dk-preview` | Tárolóképa `da-DK` a területi beállítással. |
| `2.0.0-amd64-de-de-preview` | Tárolóképa `de-DE` a területi beállítással. |
| `2.0.0-amd64-en-au-preview` | Tárolóképa `en-AU` a területi beállítással. |
| `2.0.0-amd64-en-ca-preview` | Tárolóképa `en-CA` a területi beállítással. |
| `2.0.0-amd64-en-gb-preview` | Tárolóképa `en-GB` a területi beállítással. |
| `2.0.0-amd64-en-in-preview` | Tárolóképa `en-IN` a területi beállítással. |
| `2.0.0-amd64-en-nz-preview` | Tárolóképa `en-NZ` a területi beállítással. |
| `2.0.0-amd64-en-us-preview` | Tárolóképa `en-US` a területi beállítással. |
| `2.0.0-amd64-es-es-preview` | Tárolóképa `es-ES` a területi beállítással. |
| `2.0.0-amd64-es-mx-preview` | Tárolóképa `es-MX` a területi beállítással. |
| `2.0.0-amd64-fi-fi-preview` | Tárolóképa `fi-FI` a területi beállítással. |
| `2.0.0-amd64-fr-ca-preview` | Tárolóképa `fr-CA` a területi beállítással. |
| `2.0.0-amd64-fr-fr-preview` | Tárolóképa `fr-FR` a területi beállítással. |
| `2.0.0-amd64-hi-in-preview` | Tárolóképa `hi-IN` a területi beállítással. |
| `2.0.0-amd64-it-it-preview` | Tárolóképa `it-IT` a területi beállítással. |
| `2.0.0-amd64-ja-jp-preview` | Tárolóképa `ja-JP` a területi beállítással. |
| `2.0.0-amd64-ko-kr-preview` | Tárolóképa `ko-KR` a területi beállítással. |
| `2.0.0-amd64-nb-no-preview` | Tárolóképa `nb-NO` a területi beállítással. |
| `2.0.0-amd64-nl-nl-preview` | Tárolóképa `nl-NL` a területi beállítással. |
| `2.0.0-amd64-pl-pl-preview` | Tárolóképa `pl-PL` a területi beállítással. |
| `2.0.0-amd64-pt-br-preview` | Tárolóképa `pt-BR` a területi beállítással. |
| `2.0.0-amd64-pt-pt-preview` | Tárolóképa `pt-PT` a területi beállítással. |
| `2.0.0-amd64-ru-ru-preview` | Tárolóképa `ru-RU` a területi beállítással. |
| `2.0.0-amd64-sv-se-preview` | Tárolóképa `sv-SE` a területi beállítással. |
| `2.0.0-amd64-th-th-preview` | Tárolóképa `th-TH` a területi beállítással. |
| `2.0.0-amd64-tr-tr-preview` | Tárolóképa `tr-TR` a területi beállítással. |
| `2.0.0-amd64-zh-cn-preview` | Tárolóképa `zh-CN` a területi beállítással. |
| `2.0.0-amd64-zh-hk-preview` | Tárolóképa `zh-HK` a területi beállítással. |
| `2.0.0-amd64-zh-tw-preview` | Tárolóképa `zh-TW` a területi beállítással. |
| `1.2.0-amd64-de-de-preview` | Tárolóképa `de-DE` a területi beállítással. |
| `1.2.0-amd64-en-au-preview` | Tárolóképa `en-AU` a területi beállítással. |
| `1.2.0-amd64-en-ca-preview` | Tárolóképa `en-CA` a területi beállítással. |
| `1.2.0-amd64-en-gb-preview` | Tárolóképa `en-GB` a területi beállítással. |
| `1.2.0-amd64-en-in-preview` | Tárolóképa `en-IN` a területi beállítással. |
| `1.2.0-amd64-en-us-preview` | Tárolóképa `en-US` a területi beállítással. |
| `1.2.0-amd64-es-es-preview` | Tárolóképa `es-ES` a területi beállítással. |
| `1.2.0-amd64-es-mx-preview` | Tárolóképa `es-MX` a területi beállítással. |
| `1.2.0-amd64-fr-ca-preview` | Tárolóképa `fr-CA` a területi beállítással. |
| `1.2.0-amd64-fr-fr-preview` | Tárolóképa `fr-FR` a területi beállítással. |
| `1.2.0-amd64-it-it-preview` | Tárolóképa `it-IT` a területi beállítással. |
| `1.2.0-amd64-ja-jp-preview` | Tárolóképa `ja-JP` a területi beállítással. |
| `1.2.0-amd64-pt-br-preview` | Tárolóképa `pt-BR` a területi beállítással. |
| `1.2.0-amd64-zh-cn-preview` | Tárolóképa `zh-CN` a területi beállítással. |
| `1.1.3-amd64-de-de-preview` | Tárolóképa `de-DE` a területi beállítással. |
| `1.1.3-amd64-en-au-preview` | Tárolóképa `en-AU` a területi beállítással. |
| `1.1.3-amd64-en-ca-preview` | Tárolóképa `en-CA` a területi beállítással. |
| `1.1.3-amd64-en-gb-preview` | Tárolóképa `en-GB` a területi beállítással. |
| `1.1.3-amd64-en-in-preview` | Tárolóképa `en-IN` a területi beállítással. |
| `1.1.3-amd64-en-us-preview` | Tárolóképa `en-US` a területi beállítással. |
| `1.1.3-amd64-es-es-preview` | Tárolóképa `es-ES` a területi beállítással. |
| `1.1.3-amd64-es-mx-preview` | Tárolóképa `es-MX` a területi beállítással. |
| `1.1.3-amd64-fr-ca-preview` | Tárolóképa `fr-CA` a területi beállítással. |
| `1.1.3-amd64-fr-fr-preview` | Tárolóképa `fr-FR` a területi beállítással. |
| `1.1.3-amd64-it-it-preview` | Tárolóképa `it-IT` a területi beállítással. |
| `1.1.3-amd64-ja-jp-preview` | Tárolóképa `ja-JP` a területi beállítással. |
| `1.1.3-amd64-pt-br-preview` | Tárolóképa `pt-BR` a területi beállítással. |
| `1.1.3-amd64-zh-cn-preview` | Tárolóképa `zh-CN` a területi beállítással. |
| `1.1.2-amd64-de-de-preview` | Tárolóképa `de-DE` a területi beállítással. |
| `1.1.2-amd64-en-au-preview` | Tárolóképa `en-AU` a területi beállítással. |
| `1.1.2-amd64-en-ca-preview` | Tárolóképa `en-CA` a területi beállítással. |
| `1.1.2-amd64-en-gb-preview` | Tárolóképa `en-GB` a területi beállítással. |
| `1.1.2-amd64-en-in-preview` | Tárolóképa `en-IN` a területi beállítással. |
| `1.1.2-amd64-en-us-preview` | Tárolóképa `en-US` a területi beállítással. |
| `1.1.2-amd64-es-es-preview` | Tárolóképa `es-ES` a területi beállítással. |
| `1.1.2-amd64-es-mx-preview` | Tárolóképa `es-MX` a területi beállítással. |
| `1.1.2-amd64-fr-ca-preview` | Tárolóképa `fr-CA` a területi beállítással. |
| `1.1.2-amd64-fr-fr-preview` | Tárolóképa `fr-FR` a területi beállítással. |
| `1.1.2-amd64-it-it-preview` | Tárolóképa `it-IT` a területi beállítással. |
| `1.1.2-amd64-ja-jp-preview` | Tárolóképa `ja-JP` a területi beállítással. |
| `1.1.2-amd64-pt-br-preview` | Tárolóképa `pt-BR` a területi beállítással. |
| `1.1.2-amd64-zh-cn-preview` | Tárolóképa `zh-CN` a területi beállítással. |
| `1.1.1-amd64-de-de-preview` | Tárolóképa `de-DE` a területi beállítással. |
| `1.1.1-amd64-en-au-preview` | Tárolóképa `en-AU` a területi beállítással. |
| `1.1.1-amd64-en-ca-preview` | Tárolóképa `en-CA` a területi beállítással. |
| `1.1.1-amd64-en-gb-preview` | Tárolóképa `en-GB` a területi beállítással. |
| `1.1.1-amd64-en-in-preview` | Tárolóképa `en-IN` a területi beállítással. |
| `1.1.1-amd64-en-us-preview` | Tárolóképa `en-US` a területi beállítással. |
| `1.1.1-amd64-es-es-preview` | Tárolóképa `es-ES` a területi beállítással. |
| `1.1.1-amd64-es-mx-preview` | Tárolóképa `es-MX` a területi beállítással. |
| `1.1.1-amd64-fr-ca-preview` | Tárolóképa `fr-CA` a területi beállítással. |
| `1.1.1-amd64-fr-fr-preview` | Tárolóképa `fr-FR` a területi beállítással. |
| `1.1.1-amd64-it-it-preview` | Tárolóképa `it-IT` a területi beállítással. |
| `1.1.1-amd64-ja-jp-preview` | Tárolóképa `ja-JP` a területi beállítással. |
| `1.1.1-amd64-pt-br-preview` | Tárolóképa `pt-BR` a területi beállítással. |
| `1.1.1-amd64-zh-cn-preview` | Tárolóképa `zh-CN` a területi beállítással. |
| `1.1.0-amd64-de-de-preview` | Tárolóképa `de-DE` a területi beállítással. |
| `1.1.0-amd64-en-au-preview` | Tárolóképa `en-AU` a területi beállítással. |
| `1.1.0-amd64-en-ca-preview` | Tárolóképa `en-CA` a területi beállítással. |
| `1.1.0-amd64-en-gb-preview` | Tárolóképa `en-GB` a területi beállítással. |
| `1.1.0-amd64-en-in-preview` | Tárolóképa `en-IN` a területi beállítással. |
| `1.1.0-amd64-en-us-preview` | Tárolóképa `en-US` a területi beállítással. |
| `1.1.0-amd64-es-es-preview` | Tárolóképa `es-ES` a területi beállítással. |
| `1.1.0-amd64-es-mx-preview` | Tárolóképa `es-MX` a területi beállítással. |
| `1.1.0-amd64-fr-ca-preview` | Tárolóképa `fr-CA` a területi beállítással. |
| `1.1.0-amd64-fr-fr-preview` | Tárolóképa `fr-FR` a területi beállítással. |
| `1.1.0-amd64-it-it-preview` | Tárolóképa `it-IT` a területi beállítással. |
| `1.1.0-amd64-ja-jp-preview` | Tárolóképa `ja-JP` a területi beállítással. |
| `1.1.0-amd64-pt-br-preview` | Tárolóképa `pt-BR` a területi beállítással. |
| `1.1.0-amd64-zh-cn-preview` | Tárolóképa `zh-CN` a területi beállítással. |
| `1.0.0-amd64-de-de-preview` | Tárolóképa `de-DE` a területi beállítással. |
| `1.0.0-amd64-en-au-preview` | Tárolóképa `en-AU` a területi beállítással. |
| `1.0.0-amd64-en-ca-preview` | Tárolóképa `en-CA` a területi beállítással. |
| `1.0.0-amd64-en-gb-preview` | Tárolóképa `en-GB` a területi beállítással. |
| `1.0.0-amd64-en-in-preview` | Tárolóképa `en-IN` a területi beállítással. |
| `1.0.0-amd64-en-us-preview` | Tárolóképa `en-US` a területi beállítással. |
| `1.0.0-amd64-es-es-preview` | Tárolóképa `es-ES` a területi beállítással. |
| `1.0.0-amd64-es-mx-preview` | Tárolóképa `es-MX` a területi beállítással. |
| `1.0.0-amd64-fr-ca-preview` | Tárolóképa `fr-CA` a területi beállítással. |
| `1.0.0-amd64-fr-fr-preview` | Tárolóképa `fr-FR` a területi beállítással. |
| `1.0.0-amd64-it-it-preview` | Tárolóképa `it-IT` a területi beállítással. |
| `1.0.0-amd64-ja-jp-preview` | Tárolóképa `ja-JP` a területi beállítással. |
| `1.0.0-amd64-pt-br-preview` | Tárolóképa `pt-BR` a területi beállítással. |
| `1.0.0-amd64-zh-cn-preview` | Tárolóképa `zh-CN` a területi beállítással. |

## <a name="text-to-speech"></a>Szövegfelolvasás

A [text-to-speech][sp-tts] tároló rendszerkép `containerpreview.azurecr.io` megtalálható a tároló beállításjegyzékben. A `microsoft` tárolón belül található, `cognitive-services-text-to-speech`és a neve . A teljesen minősített tárolókép `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech`neve, .

Ez a tárolórendszerkép a következő címkékkel rendelkezik:

| Képcímkék                                  | Megjegyzések                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | Tárolókép a `en-US` területi `en-US-JessaRUS` beállítással és a hanggal.        |
| `1.3.0-amd64-ar-eg-hoda-preview`            | Tárolókép a `ar-EG` területi `ar-EG-Hoda` beállítással és a hanggal.            |
| `1.3.0-amd64-ar-sa-naayf-preview`           | Tárolókép a `ar-SA` területi `ar-SA-Naayf` beállítással és a hanggal.           |
| `1.3.0-amd64-bg-bg-ivan-preview`            | Tárolókép a `bg-BG` területi `bg-BG-Ivan` beállítással és a hanggal.            |
| `1.3.0-amd64-ca-es-herenarus-preview`       | Tárolókép a `ca-ES` területi `ca-ES-HerenaRUS` beállítással és a hanggal.       |
| `1.3.0-amd64-cs-cz-jakub-preview`           | Tárolókép a `cs-CZ` területi `cs-CZ-Jakub` beállítással és a hanggal.           |
| `1.3.0-amd64-da-dk-hellerus-preview`        | Tárolókép a `da-DK` területi `da-DK-HelleRUS` beállítással és a hanggal.        |
| `1.3.0-amd64-de-at-michael-preview`         | Tárolókép a `de-AT` területi `de-AT-Michael` beállítással és a hanggal.         |
| `1.3.0-amd64-de-ch-karsten-preview`         | Tárolókép a `de-CH` területi `de-CH-Karsten` beállítással és a hanggal.         |
| `1.3.0-amd64-de-de-hedda-preview`           | Tárolókép a `de-DE` területi `de-DE-Hedda` beállítással és a hanggal.           |
| `1.3.0-amd64-de-de-heddarus-preview`        | Tárolókép a `de-DE` területi `de-DE-Hedda` beállítással és a hanggal.           |
| `1.3.0-amd64-de-de-heddarus-preview`        | Tárolókép a `de-DE` területi `de-DE-HeddaRUS` beállítással és a hanggal.        |
| `1.3.0-amd64-de-de-stefan-apollo-preview`   | Tárolókép a `de-DE` területi `de-DE-Stefan-Apollo` beállítással és a hanggal.   |
| `1.3.0-amd64-el-gr-stefanos-preview`        | Tárolókép a `el-GR` területi `el-GR-Stefanos` beállítással és a hanggal.        |
| `1.3.0-amd64-en-au-catherine-preview`       | Tárolókép a `en-AU` területi `en-AU-Catherine` beállítással és a hanggal.       |
| `1.3.0-amd64-en-au-hayleyrus-preview`       | Tárolókép a `en-AU` területi `en-AU-HayleyRUS` beállítással és a hanggal.       |
| `1.3.0-amd64-en-ca-heatherrus-preview`      | Tárolókép a `en-CA` területi `en-CA-HeatherRUS` beállítással és a hanggal.      |
| `1.3.0-amd64-en-ca-linda-preview`           | Tárolókép a `en-CA` területi `en-CA-Linda` beállítással és a hanggal.           |
| `1.3.0-amd64-en-gb-george-apollo-preview`   | Tárolókép a `en-GB` területi `en-GB-George-Apollo` beállítással és a hanggal.   |
| `1.3.0-amd64-en-gb-hazelrus-preview`        | Tárolókép a `en-GB` területi `en-GB-HazelRUS` beállítással és a hanggal.        |
| `1.3.0-amd64-en-gb-susan-apollo-preview`    | Tárolókép a `en-GB` területi `en-GB-Susan-Apollo` beállítással és a hanggal.    |
| `1.3.0-amd64-en-ie-sean-preview`            | Tárolókép a `en-IE` területi `en-IE-Sean` beállítással és a hanggal.            |
| `1.3.0-amd64-en-in-heera-apollo-preview`    | Tárolókép a `en-IN` területi `en-IN-Heera-Apollo` beállítással és a hanggal.    |
| `1.3.0-amd64-en-in-priyarus-preview`        | Tárolókép a `en-IN` területi `en-IN-PriyaRUS` beállítással és a hanggal.        |
| `1.3.0-amd64-en-in-ravi-apollo-preview`     | Tárolókép a `en-IN` területi `en-IN-Ravi-Apollo` beállítással és a hanggal.     |
| `1.3.0-amd64-en-us-benjaminrus-preview`     | Tárolókép a `en-US` területi `en-US-BenjaminRUS` beállítással és a hanggal.     |
| `1.3.0-amd64-en-us-guy24krus-preview`       | Tárolókép a `en-US` területi `en-US-Guy24kRUS` beállítással és a hanggal.       |
| `1.3.0-amd64-en-us-jessa24krus-preview`     | Tárolókép a `en-US` területi `en-US-Jessa24kRUS` beállítással és a hanggal.     |
| `1.3.0-amd64-en-us-jessarus-preview`        | Tárolókép a `en-US` területi `en-US-JessaRUS` beállítással és a hanggal.        |
| `1.3.0-amd64-en-us-zirarus-preview`         | Tárolókép a `en-US` területi `en-US-ZiraRUS` beállítással és a hanggal.         |
| `1.3.0-amd64-es-es-helenarus-preview`       | Tárolókép a `es-ES` területi `es-ES-HelenaRUS` beállítással és a hanggal.       |
| `1.3.0-amd64-es-es-laura-apollo-preview`    | Tárolókép a `es-ES` területi `es-ES-Laura-Apollo` beállítással és a hanggal.    |
| `1.3.0-amd64-es-es-pablo-apollo-preview`    | Tárolókép a `es-ES` területi `es-ES-Pablo-Apollo` beállítással és a hanggal.    |
| `1.3.0-amd64-es-mx-hildarus-preview`        | Tárolókép a `es-MX` területi `es-MX-HildaRUS` beállítással és a hanggal.        |
| `1.3.0-amd64-es-mx-raul-apollo-preview`     | Tárolókép a `es-MX` területi `es-MX-Raul-Apollo` beállítással és a hanggal.     |
| `1.3.0-amd64-fi-fi-heidirus-preview`        | Tárolókép a `fi-FI` területi `fi-FI-HeidiRUS` beállítással és a hanggal.        |
| `1.3.0-amd64-fr-ca-caroline-preview`        | Tárolókép a `fr-CA` területi `fr-CA-Caroline` beállítással és a hanggal.        |
| `1.3.0-amd64-fr-ca-harmonierus-preview`     | Tárolókép a `fr-CA` területi `fr-CA-HarmonieRUS` beállítással és a hanggal.     |
| `1.3.0-amd64-fr-ch-guillaume-preview`       | Tárolókép a `fr-CH` területi `fr-CH-Guillaume` beállítással és a hanggal.       |
| `1.3.0-amd64-fr-fr-hortenserus-preview`     | Tárolókép a `fr-FR` területi `fr-FR-HortenseRUS` beállítással és a hanggal.     |
| `1.3.0-amd64-fr-fr-julie-apollo-preview`    | Tárolókép a `fr-FR` területi `fr-FR-Julie-Apollo` beállítással és a hanggal.    |
| `1.3.0-amd64-fr-fr-paul-apollo-preview`     | Tárolókép a `fr-FR` területi `fr-FR-Paul-Apollo` beállítással és a hanggal.     |
| `1.3.0-amd64-he-il-asaf-preview`            | Tárolókép a `he-IL` területi `he-IL-Asaf` beállítással és a hanggal.            |
| `1.3.0-amd64-hi-in-hemant-preview`          | Tárolókép a `hi-IN` területi `hi-IN-Hemant` beállítással és a hanggal.          |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview`  | Tárolókép a `hi-IN` területi `hi-IN-Kalpana-Apollo` beállítással és a hanggal.  |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview`  | Tárolókép a `hi-IN` területi `hi-IN-Kalpana` beállítással és a hanggal.         |
| `1.3.0-amd64-hi-in-kalpana-preview`         | Tárolókép a `hi-IN` területi `hi-IN-Kalpana` beállítással és a hanggal.         |
| `1.3.0-amd64-hr-hr-matej-preview`           | Tárolókép a `hr-HR` területi `hr-HR-Matej` beállítással és a hanggal.           |
| `1.3.0-amd64-hu-hu-szabolcs-preview`        | Tárolókép a `hu-HU` területi `hu-HU-Szabolcs` beállítással és a hanggal.        |
| `1.3.0-amd64-id-id-andika-preview`          | Tárolókép a `id-ID` területi `id-ID-Andika` beállítással és a hanggal.          |
| `1.3.0-amd64-it-it-cosimo-apollo-preview`   | Tárolókép a `it-IT` területi `it-IT-Cosimo-Apollo` beállítással és a hanggal.   |
| `1.3.0-amd64-it-it-luciarus-preview`        | Tárolókép a `it-IT` területi `it-IT-LuciaRUS` beállítással és a hanggal.        |
| `1.3.0-amd64-ja-jp-ayumi-apollo-preview`    | Tárolókép a `ja-JP` területi `ja-JP-Ayumi-Apollo` beállítással és a hanggal.    |
| `1.3.0-amd64-ja-jp-harukarus-preview`       | Tárolókép a `ja-JP` területi `ja-JP-HarukaRUS` beállítással és a hanggal.       |
| `1.3.0-amd64-ja-jp-ichiro-apollo-preview`   | Tárolókép a `ja-JP` területi `ja-JP-Ichiro-Apollo` beállítással és a hanggal.   |
| `1.3.0-amd64-ko-kr-heamirus-preview`        | Tárolókép a `ko-KR` területi `ko-KR-HeamiRUS` beállítással és a hanggal.        |
| `1.3.0-amd64-ms-my-rizwan-preview`          | Tárolókép a `ms-MY` területi `ms-MY-Rizwan` beállítással és a hanggal.          |
| `1.3.0-amd64-nb-no-huldarus-preview`        | Tárolókép a `nb-NO` területi `nb-NO-HuldaRUS` beállítással és a hanggal.        |
| `1.3.0-amd64-nl-nl-hannarus-preview`        | Tárolókép a `nl-NL` területi `nl-NL-HannaRUS` beállítással és a hanggal.        |
| `1.3.0-amd64-pl-pl-paulinarus-preview`      | Tárolókép a `pl-PL` területi `pl-PL-PaulinaRUS` beállítással és a hanggal.      |
| `1.3.0-amd64-pt-br-daniel-apollo-preview`   | Tárolókép a `pt-BR` területi `pt-BR-Daniel-Apollo` beállítással és a hanggal.   |
| `1.3.0-amd64-pt-br-heloisarus-preview`      | Tárolókép a `pt-BR` területi `pt-BR-HeloisaRUS` beállítással és a hanggal.      |
| `1.3.0-amd64-pt-pt-heliarus-preview`        | Tárolókép a `pt-PT` területi `pt-PT-HeliaRUS` beállítással és a hanggal.        |
| `1.3.0-amd64-ro-ro-andrei-preview`          | Tárolókép a `ro-RO` területi `ro-RO-Andrei` beállítással és a hanggal.          |
| `1.3.0-amd64-ru-ru-ekaterinarus-preview`    | Tárolókép a `ru-RU` területi `ru-RU-EkaterinaRUS` beállítással és a hanggal.    |
| `1.3.0-amd64-ru-ru-irina-apollo-preview`    | Tárolókép a `ru-RU` területi `ru-RU-Irina-Apollo` beállítással és a hanggal.    |
| `1.3.0-amd64-ru-ru-pavel-apollo-preview`    | Tárolókép a `ru-RU` területi `ru-RU-Pavel-Apollo` beállítással és a hanggal.    |
| `1.3.0-amd64-sk-sk-filip-preview`           | Tárolókép a `sk-SK` területi `sk-SK-Filip` beállítással és a hanggal.           |
| `1.3.0-amd64-sl-si-lado-preview`            | Tárolókép a `sl-SI` területi `sl-SI-Lado` beállítással és a hanggal.            |
| `1.3.0-amd64-sv-se-hedvigrus-preview`       | Tárolókép a `sv-SE` területi `sv-SE-HedvigRUS` beállítással és a hanggal.       |
| `1.3.0-amd64-ta-in-valluvar-preview`        | Tárolókép a `ta-IN` területi `ta-IN-Valluvar` beállítással és a hanggal.        |
| `1.3.0-amd64-te-in-chitra-preview`          | Tárolókép a `te-IN` területi `te-IN-Chitra` beállítással és a hanggal.          |
| `1.3.0-amd64-th-th-pattara-preview`         | Tárolókép a `th-TH` területi `th-TH-Pattara` beállítással és a hanggal.         |
| `1.3.0-amd64-tr-tr-sedarus-preview`         | Tárolókép a `tr-TR` területi `tr-TR-SedaRUS` beállítással és a hanggal.         |
| `1.3.0-amd64-vi-vn-an-preview`              | Tárolókép a `vi-VN` területi `vi-VN-An` beállítással és a hanggal.              |
| `1.3.0-amd64-zh-cn-huihuirus-preview`       | Tárolókép a `zh-CN` területi `zh-CN-HuihuiRUS` beállítással és a hanggal.       |
| `1.3.0-amd64-zh-cn-kangkang-apollo-preview` | Tárolókép a `zh-CN` területi `zh-CN-Kangkang-Apollo` beállítással és a hanggal. |
| `1.3.0-amd64-zh-cn-yaoyao-apollo-preview`   | Tárolókép a `zh-CN` területi `zh-CN-Yaoyao-Apollo` beállítással és a hanggal.   |
| `1.3.0-amd64-zh-hk-danny-apollo-preview`    | Tárolókép a `zh-HK` területi `zh-HK-Danny-Apollo` beállítással és a hanggal.    |
| `1.3.0-amd64-zh-hk-tracy-apollo-preview`    | Tárolókép a `zh-HK` területi `zh-HK-Tracy-Apollo` beállítással és a hanggal.    |
| `1.3.0-amd64-zh-hk-tracyrus-preview`        | Tárolókép a `zh-HK` területi `zh-HK-TracyRUS` beállítással és a hanggal.        |
| `1.3.0-amd64-zh-tw-hanhanrus-preview`       | Tárolókép a `zh-TW` területi `zh-TW-HanHanRUS` beállítással és a hanggal.       |
| `1.3.0-amd64-zh-tw-yating-apollo-preview`   | Tárolókép a `zh-TW` területi `zh-TW-Yating-Apollo` beállítással és a hanggal.   |
| `1.3.0-amd64-zh-tw-zhiwei-apollo-preview`   | Tárolókép a `zh-TW` területi `zh-TW-Zhiwei-Apollo` beállítással és a hanggal.   |
| `1.2.0-amd64-de-de-heddarus-preview`        | Tárolókép a `de-DE` területi `de-DE-Hedda` beállítással és a hanggal.           |
| `1.2.0-amd64-de-de-heddarus-preview`        | Tárolókép a `de-DE` területi `de-DE-HeddaRUS` beállítással és a hanggal.        |
| `1.2.0-amd64-de-de-stefan-apollo-preview`   | Tárolókép a `de-DE` területi `de-DE-Stefan-Apollo` beállítással és a hanggal.   |
| `1.2.0-amd64-en-au-catherine-preview`       | Tárolókép a `en-AU` területi `en-AU-Catherine` beállítással és a hanggal.       |
| `1.2.0-amd64-en-au-hayleyrus-preview`       | Tárolókép a `en-AU` területi `en-AU-HayleyRUS` beállítással és a hanggal.       |
| `1.2.0-amd64-en-gb-george-apollo-preview`   | Tárolókép a `en-GB` területi `en-GB-George-Apollo` beállítással és a hanggal.   |
| `1.2.0-amd64-en-gb-hazelrus-preview`        | Tárolókép a `en-GB` területi `en-GB-HazelRUS` beállítással és a hanggal.        |
| `1.2.0-amd64-en-gb-susan-apollo-preview`    | Tárolókép a `en-GB` területi `en-GB-Susan-Apollo` beállítással és a hanggal.    |
| `1.2.0-amd64-en-in-heera-apollo-preview`    | Tárolókép a `en-IN` területi `en-IN-Heera-Apollo` beállítással és a hanggal.    |
| `1.2.0-amd64-en-in-priyarus-preview`        | Tárolókép a `en-IN` területi `en-IN-PriyaRUS` beállítással és a hanggal.        |
| `1.2.0-amd64-en-in-ravi-apollo-preview`     | Tárolókép a `en-IN` területi `en-IN-Ravi-Apollo` beállítással és a hanggal.     |
| `1.2.0-amd64-en-us-benjaminrus-preview`     | Tárolókép a `en-US` területi `en-US-BenjaminRUS` beállítással és a hanggal.     |
| `1.2.0-amd64-en-us-guy24krus-preview`       | Tárolókép a `en-US` területi `en-US-Guy24kRUS` beállítással és a hanggal.       |
| `1.2.0-amd64-en-us-jessa24krus-preview`     | Tárolókép a `en-US` területi `en-US-Jessa24kRUS` beállítással és a hanggal.     |
| `1.2.0-amd64-en-us-jessarus-preview`        | Tárolókép a `en-US` területi `en-US-JessaRUS` beállítással és a hanggal.        |
| `1.2.0-amd64-en-us-zirarus-preview`         | Tárolókép a `en-US` területi `en-US-ZiraRUS` beállítással és a hanggal.         |
| `1.2.0-amd64-es-es-helenarus-preview`       | Tárolókép a `es-ES` területi `es-ES-HelenaRUS` beállítással és a hanggal.       |
| `1.2.0-amd64-es-es-laura-apollo-preview`    | Tárolókép a `es-ES` területi `es-ES-Laura-Apollo` beállítással és a hanggal.    |
| `1.2.0-amd64-es-es-pablo-apollo-preview`    | Tárolókép a `es-ES` területi `es-ES-Pablo-Apollo` beállítással és a hanggal.    |
| `1.2.0-amd64-es-mx-hildarus-preview`        | Tárolókép a `es-MX` területi `es-MX-HildaRUS` beállítással és a hanggal.        |
| `1.2.0-amd64-es-mx-raul-apollo-preview`     | Tárolókép a `es-MX` területi `es-MX-Raul-Apollo` beállítással és a hanggal.     |
| `1.2.0-amd64-fr-ca-caroline-preview`        | Tárolókép a `fr-CA` területi `fr-CA-Caroline` beállítással és a hanggal.        |
| `1.2.0-amd64-fr-ca-harmonierus-preview`     | Tárolókép a `fr-CA` területi `fr-CA-HarmonieRUS` beállítással és a hanggal.     |
| `1.2.0-amd64-fr-fr-hortenserus-preview`     | Tárolókép a `fr-FR` területi `fr-FR-HortenseRUS` beállítással és a hanggal.     |
| `1.2.0-amd64-fr-fr-julie-apollo-preview`    | Tárolókép a `fr-FR` területi `fr-FR-Julie-Apollo` beállítással és a hanggal.    |
| `1.2.0-amd64-fr-fr-paul-apollo-preview`     | Tárolókép a `fr-FR` területi `fr-FR-Paul-Apollo` beállítással és a hanggal.     |
| `1.2.0-amd64-it-it-cosimo-apollo-preview`   | Tárolókép a `it-IT` területi `it-IT-Cosimo-Apollo` beállítással és a hanggal.   |
| `1.2.0-amd64-it-it-luciarus-preview`        | Tárolókép a `it-IT` területi `it-IT-LuciaRUS` beállítással és a hanggal.        |
| `1.2.0-amd64-ja-jp-ayumi-apollo-preview`    | Tárolókép a `ja-JP` területi `ja-JP-Ayumi-Apollo` beállítással és a hanggal.    |
| `1.2.0-amd64-ja-jp-harukarus-preview`       | Tárolókép a `ja-JP` területi `ja-JP-HarukaRUS` beállítással és a hanggal.       |
| `1.2.0-amd64-ja-jp-ichiro-apollo-preview`   | Tárolókép a `ja-JP` területi `ja-JP-Ichiro-Apollo` beállítással és a hanggal.   |
| `1.2.0-amd64-ko-kr-heamirus-preview`        | Tárolókép a `ko-KR` területi `ko-KR-HeamiRUS` beállítással és a hanggal.        |
| `1.2.0-amd64-pt-br-daniel-apollo-preview`   | Tárolókép a `pt-BR` területi `pt-BR-Daniel-Apollo` beállítással és a hanggal.   |
| `1.2.0-amd64-pt-br-heloisarus-preview`      | Tárolókép a `pt-BR` területi `pt-BR-HeloisaRUS` beállítással és a hanggal.      |
| `1.2.0-amd64-zh-cn-huihuirus-preview`       | Tárolókép a `zh-CN` területi `zh-CN-HuihuiRUS` beállítással és a hanggal.       |
| `1.2.0-amd64-zh-cn-kangkang-apollo-preview` | Tárolókép a `zh-CN` területi `zh-CN-Kangkang-Apollo` beállítással és a hanggal. |
| `1.2.0-amd64-zh-cn-yaoyao-apollo-preview`   | Tárolókép a `zh-CN` területi `zh-CN-Yaoyao-Apollo` beállítással és a hanggal.   |
| `1.1.0-amd64-de-de-hedda-preview`           | Tárolókép a `de-DE` területi `de-DE-Hedda` beállítással és a hanggal.           |
| `1.1.0-amd64-de-de-heddarus-preview`        | Tárolókép a `de-DE` területi `de-DE-Hedda` beállítással és a hanggal.           |
| `1.1.0-amd64-de-de-heddarus-preview`        | Tárolókép a `de-DE` területi `de-DE-HeddaRUS` beállítással és a hanggal.        |
| `1.1.0-amd64-de-de-stefan-apollo-preview`   | Tárolókép a `de-DE` területi `de-DE-Stefan-Apollo` beállítással és a hanggal.   |
| `1.1.0-amd64-en-au-catherine-preview`       | Tárolókép a `en-AU` területi `en-AU-Catherine` beállítással és a hanggal.       |
| `1.1.0-amd64-en-au-hayleyrus-preview`       | Tárolókép a `en-AU` területi `en-AU-HayleyRUS` beállítással és a hanggal.       |
| `1.1.0-amd64-en-gb-george-apollo-preview`   | Tárolókép a `en-GB` területi `en-GB-George-Apollo` beállítással és a hanggal.   |
| `1.1.0-amd64-en-gb-hazelrus-preview`        | Tárolókép a `en-GB` területi `en-GB-HazelRUS` beállítással és a hanggal.        |
| `1.1.0-amd64-en-gb-susan-apollo-preview`    | Tárolókép a `en-GB` területi `en-GB-Susan-Apollo` beállítással és a hanggal.    |
| `1.1.0-amd64-en-in-heera-apollo-preview`    | Tárolókép a `en-IN` területi `en-IN-Heera-Apollo` beállítással és a hanggal.    |
| `1.1.0-amd64-en-in-priyarus-preview`        | Tárolókép a `en-IN` területi `en-IN-PriyaRUS` beállítással és a hanggal.        |
| `1.1.0-amd64-en-in-ravi-apollo-preview`     | Tárolókép a `en-IN` területi `en-IN-Ravi-Apollo` beállítással és a hanggal.     |
| `1.1.0-amd64-en-us-benjaminrus-preview`     | Tárolókép a `en-US` területi `en-US-BenjaminRUS` beállítással és a hanggal.     |
| `1.1.0-amd64-en-us-guy24krus-preview`       | Tárolókép a `en-US` területi `en-US-Guy24kRUS` beállítással és a hanggal.       |
| `1.1.0-amd64-en-us-jessa24krus-preview`     | Tárolókép a `en-US` területi `en-US-Jessa24kRUS` beállítással és a hanggal.     |
| `1.1.0-amd64-en-us-jessarus-preview`        | Tárolókép a `en-US` területi `en-US-JessaRUS` beállítással és a hanggal.        |
| `1.1.0-amd64-en-us-zirarus-preview`         | Tárolókép a `en-US` területi `en-US-ZiraRUS` beállítással és a hanggal.         |
| `1.1.0-amd64-es-es-helenarus-preview`       | Tárolókép a `es-ES` területi `es-ES-HelenaRUS` beállítással és a hanggal.       |
| `1.1.0-amd64-es-es-laura-apollo-preview`    | Tárolókép a `es-ES` területi `es-ES-Laura-Apollo` beállítással és a hanggal.    |
| `1.1.0-amd64-es-es-pablo-apollo-preview`    | Tárolókép a `es-ES` területi `es-ES-Pablo-Apollo` beállítással és a hanggal.    |
| `1.1.0-amd64-es-mx-hildarus-preview`        | Tárolókép a `es-MX` területi `es-MX-HildaRUS` beállítással és a hanggal.        |
| `1.1.0-amd64-es-mx-raul-apollo-preview`     | Tárolókép a `es-MX` területi `es-MX-Raul-Apollo` beállítással és a hanggal.     |
| `1.1.0-amd64-fr-ca-caroline-preview`        | Tárolókép a `fr-CA` területi `fr-CA-Caroline` beállítással és a hanggal.        |
| `1.1.0-amd64-fr-ca-harmonierus-preview`     | Tárolókép a `fr-CA` területi `fr-CA-HarmonieRUS` beállítással és a hanggal.     |
| `1.1.0-amd64-fr-fr-hortenserus-preview`     | Tárolókép a `fr-FR` területi `fr-FR-HortenseRUS` beállítással és a hanggal.     |
| `1.1.0-amd64-fr-fr-julie-apollo-preview`    | Tárolókép a `fr-FR` területi `fr-FR-Julie-Apollo` beállítással és a hanggal.    |
| `1.1.0-amd64-fr-fr-paul-apollo-preview`     | Tárolókép a `fr-FR` területi `fr-FR-Paul-Apollo` beállítással és a hanggal.     |
| `1.1.0-amd64-it-it-cosimo-apollo-preview`   | Tárolókép a `it-IT` területi `it-IT-Cosimo-Apollo` beállítással és a hanggal.   |
| `1.1.0-amd64-it-it-luciarus-preview`        | Tárolókép a `it-IT` területi `it-IT-LuciaRUS` beállítással és a hanggal.        |
| `1.1.0-amd64-ja-jp-ayumi-apollo-preview`    | Tárolókép a `ja-JP` területi `ja-JP-Ayumi-Apollo` beállítással és a hanggal.    |
| `1.1.0-amd64-ja-jp-harukarus-preview`       | Tárolókép a `ja-JP` területi `ja-JP-HarukaRUS` beállítással és a hanggal.       |
| `1.1.0-amd64-ja-jp-ichiro-apollo-preview`   | Tárolókép a `ja-JP` területi `ja-JP-Ichiro-Apollo` beállítással és a hanggal.   |
| `1.1.0-amd64-ko-kr-heamirus-preview`        | Tárolókép a `ko-KR` területi `ko-KR-HeamiRUS` beállítással és a hanggal.        |
| `1.1.0-amd64-pt-br-daniel-apollo-preview`   | Tárolókép a `pt-BR` területi `pt-BR-Daniel-Apollo` beállítással és a hanggal.   |
| `1.1.0-amd64-pt-br-heloisarus-preview`      | Tárolókép a `pt-BR` területi `pt-BR-HeloisaRUS` beállítással és a hanggal.      |
| `1.1.0-amd64-zh-cn-huihuirus-preview`       | Tárolókép a `zh-CN` területi `zh-CN-HuihuiRUS` beállítással és a hanggal.       |
| `1.1.0-amd64-zh-cn-kangkang-apollo-preview` | Tárolókép a `zh-CN` területi `zh-CN-Kangkang-Apollo` beállítással és a hanggal. |
| `1.1.0-amd64-zh-cn-yaoyao-apollo-preview`   | Tárolókép a `zh-CN` területi `zh-CN-Yaoyao-Apollo` beállítással és a hanggal.   |
| `1.0.0-amd64-en-us-benjaminrus-preview`     | Tárolókép a `en-US` területi `en-US-BenjaminRUS` beállítással és a hanggal.     |
| `1.0.0-amd64-en-us-guy24krus-preview`       | Tárolókép a `en-US` területi `en-US-Guy24kRUS` beállítással és a hanggal.       |
| `1.0.0-amd64-en-us-jessa24krus-preview`     | Tárolókép a `en-US` területi `en-US-Jessa24kRUS` beállítással és a hanggal.     |
| `1.0.0-amd64-en-us-jessarus-preview`        | Tárolókép a `en-US` területi `en-US-JessaRUS` beállítással és a hanggal.        |
| `1.0.0-amd64-en-us-zirarus-preview`         | Tárolókép a `en-US` területi `en-US-ZiraRUS` beállítással és a hanggal.         |
| `1.0.0-amd64-zh-cn-huihuirus-preview`       | Tárolókép a `zh-CN` területi `zh-CN-HuihuiRUS` beállítással és a hanggal.       |
| `1.0.0-amd64-zh-cn-kangkang-apollo-preview` | Tárolókép a `zh-CN` területi `zh-CN-Kangkang-Apollo` beállítással és a hanggal. |
| `1.0.0-amd64-zh-cn-yaoyao-apollo-preview`   | Tárolókép a `zh-CN` területi `zh-CN-Yaoyao-Apollo` beállítással és a hanggal.   |

## <a name="key-phrase-extraction"></a>Kulcskifejezések kinyerése

A [kulcskifejezés kivonása][ta-kp] tárolórendszerkép megtalálható a `mcr.microsoft.com` tároló regisztrációs szindikátus. A `azure-cognitive-services` tárolón belül található, `keyphrase`és a neve . A teljesen minősített tárolókép `mcr.microsoft.com/azure-cognitive-services/keyphrase`neve, .

Ez a tárolórendszerkép a következő címkékkel rendelkezik:

| Képcímkék                    | Megjegyzések |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

## <a name="language-detection"></a>Nyelvfelismerés

A [Language Detection][ta-la] tároló rendszerkép `mcr.microsoft.com` megtalálható a tároló regisztrációs szindikátus. A `azure-cognitive-services` tárolón belül található, `language`és a neve . A teljesen minősített tárolókép `mcr.microsoft.com/azure-cognitive-services/language`neve, .

Ez a tárolórendszerkép a következő címkékkel rendelkezik:

| Képcímkék                    | Megjegyzések |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

## <a name="sentiment-analysis"></a>Hangulatelemzés

A [hangulatelemzés][ta-se] tárolórendszerképe megtalálható `mcr.microsoft.com` a tároló beállításjegyzék-szindikátus. A `azure-cognitive-services` tárolón belül található, `sentiment`és a neve . A teljesen minősített tárolókép `mcr.microsoft.com/azure-cognitive-services/sentiment`neve, .

Ez a tárolórendszerkép a következő címkékkel rendelkezik:

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
