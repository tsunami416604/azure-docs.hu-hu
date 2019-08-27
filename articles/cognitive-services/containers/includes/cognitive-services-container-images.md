---
ms.openlocfilehash: fe9879f9574fe1496ebdf20ac76fe5b759d4ea6a
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051233"
---
### <a name="container-repositories-and-images"></a>Tárolók és lemezképek

Az alábbi táblázat az Azure Cognitive Services által kínált elérhető tároló-lemezképek átfogó listáját tartalmazza.

#### <a name="public-container-registry-mcrmicrosoftcom"></a>Nyilvános (Container Registry: `mcr.microsoft.com`)

A Microsoft Container Registry a Cognitive Services összes általánosan elérhető (GA) tárolóját üzemelteti.

| Szolgáltatás | Tároló | Container Registry/adattár/rendszerkép neve |
|--|--|--|
| [LUIS](../../LUIS/luis-container-howto.md) | LUIS | `mcr.microsoft.com/azure-cognitive-services/luis` |
| [Szövegelemzés](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Kulcskifejezések kinyerése | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Szövegelemzés](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Nyelvfelismerés | `mcr.microsoft.com/azure-cognitive-services/language` |
| [Szövegelemzés](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Véleményelemzés | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

#### <a name="public-preview-container-registry-containerpreviewazurecrio"></a>Nyilvános előzetes (Container Registry: `containerpreview.azurecr.io`)

A tároló előzetes verziójának beállításjegyzéke tartalmazza az összes "nyilvános előzetes" tárolót a Cognitive Services számára, amelyek még nem haladták meg az általános elérhetőséget (GA). Ezeknek a tárolóknak formális kérelemre van szükségük ahhoz, hogy használni lehessen őket.

| Szolgáltatás | Tároló | Container Registry/adattár/rendszerkép neve |
|--|--|--|
| [Anomália detektor](../../anomaly-detector/anomaly-detector-container-howto.md) | Anomáliadetektor | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector` |
| [Computer Vision](../../Computer-vision/computer-vision-how-to-install-containers.md) | szövegfelismerés | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text` |
| [Arcfelismerés](../../face/face-how-to-install-containers.md) | Face | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Űrlap-felismerő](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Form Recognizer | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [Speech Service API](../../speech-service/speech-container-howto.md) | Speech-to-text | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [Speech Service API](../../speech-service/speech-container-howto.md) | Szövegfelolvasás | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
