---
title: Tárolótárolók és -képek
services: cognitive-services
author: IEvangelist
manager: nitinme
description: Két tábla, amely a tárolójegyzékeket, az adattárakat és a rendszerképneveket jelöli az összes Cognitive Service-ajánlathoz.
ms.service: cognitive-services
ms.topic: include
ms.date: 03/10/2020
ms.author: dapine
ms.openlocfilehash: 55a3bb5f894d3ab753cfec64687abc9c7cae53cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082281"
---
### <a name="container-repositories-and-images"></a>Tárolótárolók és -képek

Az alábbi táblázatok az Azure Cognitive Services által kínált rendelkezésre álló tárolórendszerképek listáját tartalmazza. Az összes rendelkezésre álló tárolórendszerkép-nevek és azok rendelkezésre álló címkéinek teljes listáját a [Cognitive Services tárolórendszerkép-címkéi című témakörben olvashat.](../container-image-tags.md) Jelenleg nincsenek olyan Cognitive Services-tárolók, amelyek általánosan elérhetők (GA). Egyelőre, amíg további bejelentések készülnek - konténerek állnak rendelkezésre, mint akár *nyilvános unated* vagy *nyilvános kapuzott preview*.

 - *Nyilvános ungated*: konténerek nyilvánosan elérhető nélkül gating mechanizmus.
 - *Nyilvános kapuval rendelkező előzetes verzió:* a tárolók nyilvánosan elérhetők, de először hivatalos kérésszükséges a tároló beállításjegyzékéhez való hozzáféréshez.

#### <a name="public-ungated-container-registry-mcrmicrosoftcom"></a>Nyilvános "Ungated" (konténer `mcr.microsoft.com`nyilvántartás: )

A Microsoft Container Registry (MCR) a Cognitive Services összes nyilvánosan elérhető "unated" tárolóját szinnómiai. A tárolók közvetlenül a [Docker hubról](https://hub.docker.com/_/microsoft-azure-cognitive-services)is elérhetők.

| Szolgáltatás | Tároló | Konténer-nyilvántartás / Adattár / Képneve |
|--|--|--|
| [LUIS](../../LUIS/luis-container-howto.md) | LUIS | `mcr.microsoft.com/azure-cognitive-services/luis` |
| [Szövegelemzés](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Kulcskifejezések kinyerése | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Szövegelemzés](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Nyelvfelismerés | `mcr.microsoft.com/azure-cognitive-services/language` |
| [Szövegelemzés](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Hangulatelemzés | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>Nyilvános "Gated" előzetes verzió `containerpreview.azurecr.io`(tároló beállításjegyzék: )

A Container Preview beállításjegyzék tárolja az összes nyilvánosan elérhető "kapuzott" tárolók Cognitive Services. Ezek a tárolók hivatalos kérelmet igényelnek a tárolóik beállításjegyzékén keresztül történő hozzáféréshez.

| Szolgáltatás | Tároló | Konténer-nyilvántartás / Adattár / Képneve |
|--|--|--|
| [Anomáliadetektor](../../anomaly-detector/anomaly-detector-container-howto.md) | Anomaly Detector | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector` |
| [Számítógépes látás](../../Computer-vision/computer-vision-how-to-install-containers.md) | Olvasás | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [Arcfelismerés](../../face/face-how-to-install-containers.md) | Arcfelismerés | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Űrlapfelismerő](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Form Recognizer | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=stt) | Diktálás | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=cstt) | Egyéni beszédfelismerési szöveg | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=tts) | Szövegfelolvasás | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=ctts) | Egyéni szövegfelolvasás | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` |
