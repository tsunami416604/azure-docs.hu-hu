---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 10/09/2020
ms.author: v-jawe
ms.openlocfilehash: 029cb1e59876eb01c609ef8b7ba0412ed35a2a1b
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2020
ms.locfileid: "91940743"
---
Ebből a rövid útmutatóból megtudhatja, hogyan alakíthat át szöveget beszédre a Speech Service és a cURL használatával.

A szöveg – beszéd fogalmak áttekintéséhez tekintse meg az [áttekintő](../../../text-to-speech.md) cikket.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy rendelkezik Azure-fiókkal és Speech Service-előfizetéssel. Ha nem rendelkezik fiókkal és előfizetéssel, [próbálja ki ingyenesen a Speech szolgáltatást](../../../overview.md#try-the-speech-service-for-free).

## <a name="convert-text-to-speech"></a>Szöveg konvertálása beszédre

A parancssorban futtassa a következő parancsot. A következő értékeket kell beszúrnia a parancsba.
- A beszédfelismerési szolgáltatás előfizetési kulcsa.
- A Speech Service-régió.

Előfordulhat, hogy módosítani szeretné a következő értékeket is.
- A `X-Microsoft-OutputFormat` fejléc értéke, amely a hangkimenet formátumát vezérli. A támogatott hangkimeneti formátumok listáját a [szöveg-beszéd REST API referenciában](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech#audio-outputs)találja.
- A kimeneti hang. A beszédfelismerési végponthoz elérhető hangok listájának megtekintéséhez tekintse meg a következő szakaszt.
- A kimeneti fájl. Ebben a példában a kiszolgáló válaszát egy nevű fájlba irányítjuk `output.wav` .

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/text-to-speech.sh":::

## <a name="list-available-voices-for-your-speech-endpoint"></a>A beszédfelismerési végponthoz elérhető hangok listázása

A beszédfelismerési végponthoz elérhető hangok listázásához futtassa a következő parancsot.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/get-voices.sh" id="request":::

A következőhöz hasonló választ kell kapnia.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/get-voices.sh" id="response":::
