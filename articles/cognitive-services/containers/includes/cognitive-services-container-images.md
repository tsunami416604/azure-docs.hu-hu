---
title: Tárolók és lemezképek
services: cognitive-services
author: IEvangelist
manager: nitinme
description: Két tábla, amely az összes kognitív szolgáltatási ajánlat tároló-nyilvántartásait, tárházait és képneveit jelképezi.
ms.service: cognitive-services
ms.topic: include
ms.date: 10/09/2019
ms.author: dapine
ms.openlocfilehash: c1593cb3dad7ee1370a66747fa3fe47e93c19957
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499161"
---
### <a name="container-repositories-and-images"></a>Tárolók és lemezképek

Az alábbi táblázat az Azure Cognitive Services által kínált elérhető tároló-lemezképek átfogó listáját tartalmazza.

#### <a name="public-ungated-container-registry-mcrmicrosoftcom"></a>Nyilvános "ungated" (tároló-beállításjegyzék: `mcr.microsoft.com`)

A Microsoft Container Registry a Cognitive Services összes nyilvánosan elérhető "nem kihelyezett" tárolóját üzemelteti.

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
| [Anomáliadetektor](../../anomaly-detector/anomaly-detector-container-howto.md) | Anomáliadetektor | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector` |
| [Computer Vision](../../Computer-vision/computer-vision-how-to-install-containers.md) | Olvasás | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [Arc](../../face/face-how-to-install-containers.md) | Arcfelismerés | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Űrlap-felismerő](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Form Recognizer | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
< < < < < < < HEAD | [Beszédfelismerési szolgáltatás API](../../speech-service/speech-container-howto.md?tab=stt) | Beszéd – szöveg | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` | | [Beszédfelismerési szolgáltatás API](../../speech-service/speech-container-howto.md?tab=cstt) | Custom Speech – Text | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` | | [Beszédfelismerési szolgáltatás API](../../speech-service/speech-container-howto.md?tab=tts) | Szöveg – beszéd | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` | | [Beszédfelismerési szolgáltatás API](../../speech-service/speech-container-howto.md?tab=ctts) | Egyéni szöveg – beszéd | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` | ======= | [Beszédfelismerési szolgáltatás API](../../speech-service/speech-container-howto.md) | Beszéd – szöveg | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` | | [Beszédfelismerési szolgáltatás API](../../speech-service/speech-container-howto.md) | Szöveg – beszéd | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` | | [Translator Text](../../translator/how-to-install-containers.md) | Translator Text | `containerpreview.azurecr.io/microsoft/cognitive-services-translator-text` |
>>>>>>> refs/Remotes/MicrosoftDocs/Master
