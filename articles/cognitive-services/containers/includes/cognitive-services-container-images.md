---
title: Tárolók és lemezképek
services: cognitive-services
author: aahill
manager: nitinme
description: Két tábla, amely az összes kognitív szolgáltatási ajánlat tároló-nyilvántartásait, tárházait és képneveit jelképezi.
ms.service: cognitive-services
ms.topic: include
ms.date: 04/24/2020
ms.author: aahi
ms.openlocfilehash: 6d47829d32ed0b7ded545c73748cda1019d94bde
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83721464"
---
### <a name="container-repositories-and-images"></a>Tárolók és lemezképek

Az alábbi táblázatok az Azure Cognitive Services által kínált elérhető tároló-lemezképek listáját tartalmazzák. Az összes rendelkezésre álló tároló-rendszerkép nevének és a rendelkezésre álló címkék teljes listájáért tekintse meg [Cognitive Services tároló képcímkéi](../container-image-tags.md)című témakört. 

#### <a name="generally-available"></a>Általánosan elérhető 

A Microsoft Container Registry (MCR) a Cognitive Services összes általánosan elérhető tárolóját szindikátusba helyezi. A tárolók közvetlenül a [Docker hub](https://hub.docker.com/_/microsoft-azure-cognitive-services)-ból is elérhetők.

#### <a name="luis"></a>[LUIS](#tab/luis)

| LUIS-tároló | Container Registry/adattár/rendszerkép neve |
|--|--|
| LUIS | `mcr.microsoft.com/azure-cognitive-services/luis` |

További információt a [Luis-tárolók futtatása és telepítése](../../LUIS/luis-container-howto.md) című témakörben talál.

#### <a name="text-analytics"></a>[Szövegelemzés](#tab/text-analytics)

| Text Analytics tároló | Container Registry/adattár/rendszerkép neve |
|--|--|
| Hangulatelemzés v3 (angol) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-en` |
| Hangulatelemzés v3 (spanyol) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-es` |
| Hangulatelemzés v3 (francia) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-fr` |
| Hangulatelemzés v3 (olasz) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-it` |
| Hangulatelemzés v3 (német) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-de` |
| Hangulatelemzés v3 (kínai – egyszerűsített) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-zh` |
| Hangulatelemzés v3 (kínai – hagyományos) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-zht` |
| Hangulatelemzés v3 (Japán) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-ja` |
| Hangulatelemzés v3 (portugál) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-pt` |
| Hangulatelemzés v3 (holland) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-nl` |

További információért lásd: [text Analytics tárolók futtatása és telepítése](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) .

---

#### <a name="public-ungated-preview-container-registry-mcrmicrosoftcom"></a>Nyilvános "ungated" előzetes verzió (Container Registry: `mcr.microsoft.com` )

A következő előnézeti tárolók nyilvánosan elérhetők. A Microsoft Container Registry (MCR) az összes nyilvánosan elérhető, nem kihelyezett tárolót a Cognitive Services számára. A tárolók közvetlenül a [Docker hub](https://hub.docker.com/_/microsoft-azure-cognitive-services)-ból is elérhetők.

| Szolgáltatás | Tároló | Container Registry/adattár/rendszerkép neve |
|--|--|--|
| [Szövegelemzés](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Kulcskifejezések kinyerése | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Szövegelemzés](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Nyelvfelismerés | `mcr.microsoft.com/azure-cognitive-services/language` |
| [Anomália detektor](../../anomaly-detector/anomaly-detector-container-howto.md) | Anomaly Detector | `mcr.microsoft.com/azure-cognitive-services/anomaly-detector` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>Nyilvános "lezárt" előzetes verzió (Container Registry: `containerpreview.azurecr.io` )

A rendszer a tároló előnézetének beállításjegyzékében tárolja a következő lezárt előzetes tárolókat, és hozzáférést igényel az alkalmazáshoz. További információért tekintse meg a következő Container-cikkeket.

| Szolgáltatás | Tároló | Container Registry/adattár/rendszerkép neve |
|--|--|--|
| [Számítógépes látástechnológia](../../Computer-vision/computer-vision-how-to-install-containers.md) | Olvasás | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [Arcfelismerés](../../face/face-how-to-install-containers.md) | Arcfelismerés | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Űrlap-felismerő](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Form Recognizer | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=stt) | Diktálás | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=cstt) | Custom Speech – szöveg | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=tts) | Szövegfelolvasás | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=ctts) | Egyéni szöveg – beszéd | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` |
