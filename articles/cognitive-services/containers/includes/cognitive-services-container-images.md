---
title: Tárolók és lemezképek
services: cognitive-services
author: IEvangelist
manager: nitinme
description: Két tábla, amely az összes kognitív szolgáltatási ajánlat tároló-nyilvántartásait, tárházait és képneveit jelképezi.
ms.service: cognitive-services
ms.topic: include
ms.date: 09/06/2019
ms.author: dapine
ms.openlocfilehash: 181f4acd86dfacb15592ded6f2df3287e3dc13bf
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2019
ms.locfileid: "71130150"
---
### <a name="container-repositories-and-images"></a>Tárolók és lemezképek

Az alábbi táblázat az Azure Cognitive Services által kínált elérhető tároló-lemezképek átfogó listáját tartalmazza.

#### <a name="public-ungated-container-registry-mcrmicrosoftcom"></a>Nyilvános "ungated" (tároló-beállításjegyzék `mcr.microsoft.com`:)

A Microsoft Container Registry a Cognitive Services összes nyilvánosan elérhető "nem kihelyezett" tárolóját üzemelteti.

| Szolgáltatás | Tároló | Container Registry/adattár/rendszerkép neve |
|--|--|--|
| [LUIS](../../LUIS/luis-container-howto.md) | LUIS | `mcr.microsoft.com/azure-cognitive-services/luis` |
| [Szövegelemzés](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Kulcskifejezések kinyerése | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Szövegelemzés](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Nyelvfelismerés | `mcr.microsoft.com/azure-cognitive-services/language` |
| [Szövegelemzés](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Véleményelemzés | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>Nyilvános "lezárt" előzetes verzió (Container `containerpreview.azurecr.io`Registry:)

A tároló előzetes verziójának beállításjegyzéke az összes nyilvánosan elérhető "GateD" tárolót tárolja Cognitive Services számára. Ezeknek a tárolóknak formális kérelemre van szükségük ahhoz, hogy használni lehessen őket.

| Szolgáltatás | Tároló | Container Registry/adattár/rendszerkép neve |
|--|--|--|
| [Anomália detektor](../../anomaly-detector/anomaly-detector-container-howto.md) | Anomáliadetektor | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector` |
| [Computer Vision](../../Computer-vision/computer-vision-how-to-install-containers.md) | szövegfelismerés | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text` |
| [Computer Vision](../../Computer-vision/computer-vision-how-to-install-containers.md) | Olvasás | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [Arcfelismerés](../../face/face-how-to-install-containers.md) | Face | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Űrlap-felismerő](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Form Recognizer | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [Speech Service API](../../speech-service/speech-container-howto.md) | Speech-to-text | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [Speech Service API](../../speech-service/speech-container-howto.md) | Szövegfelolvasás | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
