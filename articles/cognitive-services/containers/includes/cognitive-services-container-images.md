---
title: Tárolók és lemezképek
services: cognitive-services
author: IEvangelist
manager: nitinme
description: Két tábla, amely az összes kognitív szolgáltatási ajánlat tároló-nyilvántartásait, tárházait és képneveit jelképezi.
ms.service: cognitive-services
ms.topic: include
ms.date: 01/10/2020
ms.author: dapine
ms.openlocfilehash: 7c229ea306d89f85fb37a68fc84e3e4f6770e5ad
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2020
ms.locfileid: "75866944"
---
### <a name="container-repositories-and-images"></a>Tárolók és lemezképek

Az alábbi táblázatok az Azure Cognitive Services által kínált elérhető tároló-lemezképek listáját tartalmazzák. Az összes rendelkezésre álló tároló-rendszerkép nevének és a rendelkezésre álló címkék teljes listájáért tekintse meg [Cognitive Services tároló képcímkéi](../container-image-tags.md)című témakört. Jelenleg nincsenek általánosan elérhető Cognitive Services tárolók (GA). Addig is, amíg a további bejelentések nem történnek – a tárolók *nyilvános* , nem lezárt vagy *nyilvános előzetes*verzióként érhetők el.

 - *Nyilvános ungated*: a tárolók nyilvánosan elérhetők kapuzás mechanizmus nélkül.
 - *Nyilvános*megnyitható előzetes verzió: a tárolók nyilvánosan elérhetők, de először formális kérelemre van szükség a tároló-beállításjegyzék eléréséhez.

#### <a name="public-ungated-container-registry-mcrmicrosoftcom"></a>Nyilvános "ungated" (tároló-beállításjegyzék: `mcr.microsoft.com`)

A Microsoft Container Registry (MCR) a Cognitive Services összes nyilvánosan elérhető "nem kihelyezett" tárolóját szindikátusba helyezi. A tárolók közvetlenül a [Docker hub](https://hub.docker.com/_/microsoft-azure-cognitive-services)-ból is elérhetők.

| Szolgáltatás | Tároló | Container Registry/adattár/rendszerkép neve |
|--|--|--|
| [LUIS](../../LUIS/luis-container-howto.md) | LUIS | `mcr.microsoft.com/azure-cognitive-services/luis` |
| [Szövegelemzés](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | A kulcsfontosságú kifejezések kinyerése | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Szövegelemzés](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Nyelvfelismerés | `mcr.microsoft.com/azure-cognitive-services/language` |
| [Szövegelemzés](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Véleményelemzés | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>Nyilvános "lezárt" előzetes verzió (Container Registry: `containerpreview.azurecr.io`)

A tároló előzetes verziójának beállításjegyzéke az összes nyilvánosan elérhető "GateD" tárolót tárolja Cognitive Services számára. Ezeknek a tárolóknak formális kérelemre van szükségük ahhoz, hogy hozzáférjenek a tároló beállításjegyzékén keresztül.

| Szolgáltatás | Tároló | Container Registry/adattár/rendszerkép neve |
|--|--|--|
| [Anomália detektor](../../anomaly-detector/anomaly-detector-container-howto.md) | Anomáliadetektor | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector` |
| [Computer Vision](../../Computer-vision/computer-vision-how-to-install-containers.md) | Olvasás | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [Arc](../../face/face-how-to-install-containers.md) | Face | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Űrlap-felismerő](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Form Recognizer | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=stt) | Diktálás | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=cstt) | Custom Speech – szöveg | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=tts) | Szövegfelolvasás | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=ctts) | Egyéni szöveg – beszéd | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` |
