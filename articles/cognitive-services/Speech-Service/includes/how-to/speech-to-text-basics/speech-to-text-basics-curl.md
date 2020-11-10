---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 10/09/2020
ms.author: v-jawe
ms.openlocfilehash: 0964872d03d6e321d25d51a18edbb4a6f0be8a4f
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2020
ms.locfileid: "94424958"
---
Ebből a rövid útmutatóból megtudhatja, hogyan alakíthat át beszédet szöveggé a Speech Service és a cURL használatával.

A beszédfelismerési fogalmakat ismertető cikkben tekintse meg az [áttekintést](../../../speech-to-text.md) ismertető cikket.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy rendelkezik Azure-fiókkal és Speech Service-előfizetéssel. Ha nem rendelkezik fiókkal és előfizetéssel, [próbálja ki ingyenesen a Speech szolgáltatást](../../../overview.md#try-the-speech-service-for-free).

## <a name="convert-speech-to-text"></a>Beszéd szöveggé alakítása

A parancssorban futtassa a következő parancsot. A következő értékeket kell beszúrnia a parancsba.
- A beszédfelismerési szolgáltatás előfizetési kulcsa.
- A Speech Service-régió.
- A bemeneti hangfájl elérési útja Hangfájlokat [a szöveg és a beszéd](../../../get-started-text-to-speech.md)között is létrehozhat.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speech-to-text.sh" id="request":::

A következőhöz hasonló választ kell kapnia.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speech-to-text.sh" id="response":::

További információért lásd a [beszéd – szöveg REST API referenciát](../../../rest-speech-to-text.md).