---
title: Tárolók és lemezképek
services: cognitive-services
author: IEvangelist
manager: nitinme
description: Két tábla, amely az összes kognitív szolgáltatási ajánlat tároló-nyilvántartásait, tárházait és képneveit jelképezi.
ms.service: cognitive-services
ms.topic: include
ms.date: 11/15/2019
ms.author: dapine
ms.openlocfilehash: 2058dd6e52ddb417e24368b27384df9a222c378e
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2019
ms.locfileid: "74142199"
---
### <a name="container-repositories-and-images"></a>Tárolók és lemezképek

Az alábbi táblázatok az Azure Cognitive Services által kínált elérhető tároló-lemezképek listáját tartalmazzák. Az összes rendelkezésre álló tároló-rendszerkép nevének és a rendelkezésre álló címkék teljes listájáért tekintse meg [Cognitive Services tároló képcímkéi](../container-image-tags.md)című témakört.

#### <a name="public-ungated-container-registry-mcrmicrosoftcom"></a>Nyilvános "ungated" (tároló-beállításjegyzék: `mcr.microsoft.com`)

A Microsoft Container Registry (MCR) a Cognitive Services összes nyilvánosan elérhető "nem kihelyezett" tárolóját szindikátusba helyezi. Ezek közvetlenül a [Docker hub](https://hub.docker.com/_/microsoft-azure-cognitive-services)-ból is elérhetők.

| Szolgáltatás | Tároló | Container Registry/adattár/rendszerkép neve |
|--|--|--|
| [LUIS](../../LUIS/luis-container-howto.md) | LUIS | `mcr.microsoft.com/azure-cognitive-services/luis` |
| [Szövegelemzés](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Kulcskifejezések kinyerése | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Szövegelemzés](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Nyelvfelismerés | `mcr.microsoft.com/azure-cognitive-services/language` |
| [Szövegelemzés](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Véleményelemzés | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>Nyilvános "lezárt" előzetes verzió (Container Registry: `containerpreview.azurecr.io`)

A tároló előzetes verziójának beállításjegyzéke az összes nyilvánosan elérhető "GateD" tárolót tárolja Cognitive Services számára. Ezeknek a tárolóknak formális kérelemre van szükségük ahhoz, hogy használni lehessen őket.

| Szolgáltatás | Tároló | Container Registry/adattár/rendszerkép neve |
|--|--|--|
| [Anomáliadetektor](../../anomaly-detector/anomaly-detector-container-howto.md) | Anomaly Detector | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector` |
| [Computer Vision](../../Computer-vision/computer-vision-how-to-install-containers.md) | Olvasás | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [Arcfelismerés](../../face/face-how-to-install-containers.md) | Arcfelismerés | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Űrlap-felismerő](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Form Recognizer | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=stt) | Diktálás | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=cstt) | Custom Speech – szöveg | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=tts) | Szövegfelolvasás | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=ctts) | Egyéni szöveg – beszéd | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` |
