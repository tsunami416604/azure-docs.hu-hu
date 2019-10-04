---
title: Régiók – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: A beszédfelismerési szolgáltatás régiók referenciája.
services: cognitive-services
author: mahilleb-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: c0414277b4851891911908ba4f42e92abedc86e4
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68553195"
---
# <a name="speech-service-supported-regions"></a>Beszédszolgáltatás támogatott régiók

A Speech service lehetővé teszi, hogy az alkalmazás hanganyagot alakíthat át szöveggé, beszédalapú fordítási és fedett szöveg-beszéd átalakítás elvégzéséhez. A szolgáltatás az egyedi végpontok a Speech SDK és a REST API-k több régióban érhető el.

Győződjön meg arról, hogy használja-e a végpontot, amely megfelel annak a régiónak az előfizetéshez.

## <a name="speech-sdk"></a>Beszéd SDK

A [Speech SDK](speech-sdk.md)-ban a régiók karakterláncként vannak megadva (például paraméterként `SpeechConfig.FromSubscription` a Speech SDK for esetében C#).

### <a name="speech-to-text-text-to-speech-and-translation"></a>Beszéd – szöveg, szöveg – beszéd és fordítás

A Speech SDK a következő régiókban érhető el a beszédfelismerés, a **szöveg és a beszéd** **megjelenítéséhez**, valamint a **fordításhoz**:

  Régió | Beszéd SDK paraméter | Speech-portál testreszabása
 ------|-------|--------
 USA nyugati régiója | `westus` | https://westus.cris.ai
 USA nyugati régiója, 2. | `westus2` | https://westus2.cris.ai
 East US | `eastus` | https://eastus.cris.ai
 USA 2. keleti régiója | `eastus2` | https://eastus2.cris.ai
 USA középső régiója | `centralus` | https://centralus.cris.ai
 USA északi középső régiója | `northcentralus` | https://northcentralus.cris.ai
 USA déli középső régiója | `southcentralus` | https://southcentralus.cris.ai
 Közép-India | `centralindia` | https://centralindia.cris.ai
 Kelet-Ázsia | `eastasia` | https://eastasia.cris.ai
 Délkelet-Ázsia | `southeastasia` | https://southeastasia.cris.ai
 Kelet-Japán | `japaneast` | https://japaneast.cris.ai
 Korea középső régiója | `koreacentral` | https://koreacentral.cris.ai
 Kelet-Ausztrália | `australiaeast` | https://australiaeast.cris.ai
 Közép-Kanada | `canadacentral` | https://canadacentral.cris.ai
 Észak-Európa | `northeurope` | https://northeurope.cris.ai
 Nyugat-Európa | `westeurope` | https://westeurope.cris.ai
 Az Egyesült Királyság déli régiója | `uksouth` | https://uksouth.cris.ai
 Közép-Franciaország | `francecentral` | https://francecentral.cris.ai

### <a name="intent-recognition"></a>Szándékfelismerés

Az elérhető régiók a beszédfelismerési SDK-n keresztül történő **szándék** -felismeréshez a következők:

 Globális régió | Régió | Beszéd SDK paraméter
 ------|-------|--------
 Ázsia | Kelet-Ázsia | `eastasia`
 Ázsia | Délkelet-Ázsia | `southeastasia`
 Ausztrália | Kelet-Ausztrália | `australiaeast`
 Európa | Észak-Európa | `northeurope`
 Európa | Nyugat-Európa | `westeurope`
 Észak-Amerika | East US | `eastus`
 Észak-Amerika | USA 2. keleti régiója | `eastus2`
 Észak-Amerika | USA déli középső régiója | `southcentralus`
 Észak-Amerika | USA nyugati középső régiója | `westcentralus`
 Észak-Amerika | USA nyugati régiója | `westus`
 Észak-Amerika | USA nyugati régiója, 2. | `westus2`
 Dél-Amerika | Dél-Brazília | `brazilsouth`

Ez a [Language Understanding szolgáltatás (Luis)](/azure/cognitive-services/luis/luis-reference-regions)által támogatott közzétételi régiók részhalmaza.

### <a name="voice-first-virtual-assistants"></a>Hang – első virtuális asszisztens

A [SPEECH SDK](speech-sdk.md) a következő régiókban támogatja a **hang-első virtuális asszisztensi** funkciókat:

Régió | Beszéd SDK paraméter
-------|---------------------
USA nyugati régiója | `westus`
USA nyugati régiója, 2. | `westus2`
East US | `eastus`
USA 2. keleti régiója | `eastus2`
Nyugat-Európa | `westeurope`
Észak-Európa | `northeurope`
Délkelet-Ázsia | `southeastasia`

## <a name="rest-apis"></a>REST API-k

A beszédfelismerési szolgáltatás is elérhetővé teszi a hang-szöveg és a szöveg-hang transzformációs kérések REST-végpontokat.

### <a name="speech-to-text"></a>Speech-to-text

A beszéd – szöveg hivatkozási dokumentációja a [beszéd – szöveg REST API](rest-speech-to-text.md)című részben olvasható.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="text-to-speech"></a>Szövegfelolvasás

Szöveg – beszéd hivatkozási dokumentáció: [szöveg – beszéd REST API](rest-text-to-speech.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]