---
title: Tárolók és lemezképek
services: cognitive-services
author: aahill
manager: nitinme
description: Két tábla, amely az összes kognitív szolgáltatási ajánlat tároló-nyilvántartásait, tárházait és képneveit jelképezi.
ms.service: cognitive-services
ms.topic: include
ms.date: 09/03/2020
ms.author: aahi
ms.openlocfilehash: 2c2ebe9b419100163ae55c1be85dd1464904e841
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2020
ms.locfileid: "94979516"
---
### <a name="container-repositories-and-images"></a>Tárolók és lemezképek

Az alábbi táblázatok az Azure Cognitive Services által kínált elérhető tároló-lemezképek listáját tartalmazzák. Az összes rendelkezésre álló tároló-rendszerkép nevének és a rendelkezésre álló címkék teljes listájáért tekintse meg [Cognitive Services tároló képcímkéi](../container-image-tags.md)című témakört. 

#### <a name="generally-available"></a>Általánosan elérhető 

A Microsoft Container Registry (MCR) a Cognitive Services összes általánosan elérhető tárolóját szindikátusba helyezi. A tárolók közvetlenül a [Docker hub](https://hub.docker.com/_/microsoft-azure-cognitive-services)-ból is elérhetők.

**LUIS**

| Tároló | Container Registry/adattár/rendszerkép neve |
|--|--|
| LUIS | `mcr.microsoft.com/azure-cognitive-services/language/luis` |

További információt a [Luis-tárolók futtatása és telepítése](../../LUIS/luis-container-howto.md) című témakörben talál.

**Text Analytics**

| Tároló | Container Registry/adattár/rendszerkép neve |
|--|--|
| Hangulatelemzés v3 (angol) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-en` |
| Hangulatelemzés v3 (spanyol) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-es` |
| Hangulatelemzés v3 (francia) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-fr` |
| Hangulatelemzés v3 (olasz) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-it` |
| Hangulatelemzés v3 (német) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-de` |
| Hangulatelemzés v3 (kínai – egyszerűsített) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-zh` |
| Hangulatelemzés v3 (kínai – hagyományos) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-zht` |
| Hangulatelemzés v3 (Japán) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-ja` |
| Hangulatelemzés v3 (portugál) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-pt` |
| Hangulatelemzés v3 (holland) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-nl` |

További információért lásd: [text Analytics tárolók futtatása és telepítése](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) .

**Anomáliadetektor** 

| Tároló | Container Registry/adattár/rendszerkép neve |
|--|--|
| Anomália detektor | `mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector` |

További információkért lásd: [az anomáliák-Kiderítő tárolók futtatása és telepítése](../../anomaly-detector/anomaly-detector-container-howto.md) .

**Beszédfelismerési szolgáltatás**

> [!NOTE]
> A beszédfelismerési tárolók használatához el kell végeznie egy [online kérelem űrlapját](https://aka.ms/csgate).

| Tároló | Container Registry/adattár/rendszerkép neve |
|--|--|
| [Diktálás](../../speech-service/speech-container-howto.md?tab=stt) | `mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text` |
| [Custom Speech – szöveg](../../speech-service/speech-container-howto.md?tab=cstt) | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text` |
| [Szövegfelolvasás](../../speech-service/speech-container-howto.md?tab=tts) | `mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech` |

#### <a name="ungated-preview"></a>"Ungated" előzetes verzió 

A következő előnézeti tárolók nyilvánosan elérhetők. A Microsoft Container Registry (MCR) az összes nyilvánosan elérhető, nem kihelyezett tárolót a Cognitive Services számára. A tárolók közvetlenül a [Docker hub](https://hub.docker.com/_/microsoft-azure-cognitive-services)-ból is elérhetők.

| Szolgáltatás | Tároló | Container Registry/adattár/rendszerkép neve |
|--|--|--|
| [Text Analytics](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Kulcskifejezések kinyerése | `mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase` |
| [Text Analytics](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Nyelvfelismerés | `mcr.microsoft.com/azure-cognitive-services/textanalytics/language` |


#### <a name="gated-preview"></a>"GateD" előzetes verzió

Korábban a lezárt előnézet tárolók a tárházban voltak tárolva `containerpreview.azurecr.io` . A 2020. szeptember 22-én kezdődően ezek a tárolók (kivéve a Text Analytics for Health szolgáltatást) a Microsoft Container Registry (MCR) szolgáltatásban találhatók, és a letöltéshez nem szükségesek a Docker login paranccsal. A tároló használatához a következőkre lesz szüksége:

1. Töltse ki az Azure-előfizetés AZONOSÍTÓját és a felhasználói forgatókönyvet [kérő űrlapot](https://aka.ms/csgate) . 
2. Jóváhagyás után töltse le a tárolót a MCR. 
3. A megfelelő Azure-erőforrás kulcsával és végpontjának használatával hitelesítheti a tárolót futásidőben. 

| Szolgáltatás | Tároló | Container Registry/adattár/rendszerkép neve |
|--|--|--|
| [Computer Vision](../../Computer-vision/computer-vision-how-to-install-containers.md) | A 2.0-s verzió olvasása | `mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview` |
| [Computer Vision](../../Computer-vision/computer-vision-how-to-install-containers.md) | A v 3.1 olvasása | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview` |
| [Computer Vision](../../computer-vision/spatial-analysis-container.md) | Térbeli elemzés | `mcr.microsoft.com/azure-cognitive-services/vision/spatial-analysis` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=ctts) | Egyéni szöveg – beszéd | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=lid) | Nyelvfelismerés | `mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=ntts) | Neurális szöveg – beszéd | `mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech` |
| [Egészségügyi Text Analytics](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=health) | Egészségügyi Text Analytics | `containerpreview.azurecr.io/microsoft/cognitive-services-healthcare` |