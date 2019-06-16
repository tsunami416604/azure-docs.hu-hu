---
title: Régió – beszédszolgáltatások
titlesuffix: Azure Cognitive Services
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
ms.openlocfilehash: 518f1048224a97943756c5b51b83cd509f82e11e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67072502"
---
# <a name="speech-service-supported-regions"></a>Beszédszolgáltatás támogatott régiók

A Speech service lehetővé teszi, hogy az alkalmazás hanganyagot alakíthat át szöveggé, beszédalapú fordítási és fedett szöveg-beszéd átalakítás elvégzéséhez. A szolgáltatás az egyedi végpontok a Speech SDK és a REST API-k több régióban érhető el.

Győződjön meg arról, hogy használja-e a végpontot, amely megfelel annak a régiónak az előfizetéshez.

## <a name="speech-sdk"></a>Beszéd SDK

Az a [beszéd SDK](speech-sdk.md), régiók karakterláncként vannak megadva (például, hogy paraméterként `SpeechConfig.FromSubscription` beszéd használata a C# SDK-ban).

### <a name="speech-to-text-text-to-speech-and-translation"></a>Hang-szöveg, szöveg-hang transzformációs és fordítás

A beszédfelismerés SDK érhető el ezekben a régiókban a **beszédfelismerés**, **szöveg-hang transzformációs**, és **fordítási**:

  Régió | Beszéd SDK paraméter | Speech-portál testreszabása
 ------|-------|--------
 USA nyugati régiója | `westus` | https://westus.cris.ai
 USA 2. nyugati régiója | `westus2` | https://westus2.cris.ai
 USA keleti régiója | `eastus` | https://eastus.cris.ai
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

Az elérhető régiók **szándékának felismerése** a Speech SDK-n keresztül tartoznak a következők:

 Globális régió | Régió | Beszéd SDK paraméter
 ------|-------|--------
 Ázsia | Kelet-Ázsia | `eastasia`
 Ázsia | Délkelet-Ázsia | `southeastasia`
 Ausztrália | Kelet-Ausztrália | `australiaeast`
 Európa | Észak-Európa | `northeurope`
 Európa | Nyugat-Európa | `westeurope`
 Észak-Amerika | USA keleti régiója | `eastus`
 Észak-Amerika | USA 2. keleti régiója | `eastus2`
 Észak-Amerika | USA déli középső régiója | `southcentralus`
 Észak-Amerika | USA nyugati középső régiója | `westcentralus`
 Észak-Amerika | USA nyugati régiója | `westus`
 Észak-Amerika | USA nyugati régiója, 2. | `westus2`
 Dél-Amerika | Dél-Brazília | `brazilsouth`

Ez a közzétételi által támogatott régiók részhalmazát a [hangfelismerési szolgáltatás (LUIS)](/azure/cognitive-services/luis/luis-reference-regions).

## <a name="rest-apis"></a>REST API-k

A beszédfelismerési szolgáltatás is elérhetővé teszi a hang-szöveg és a szöveg-hang transzformációs kérések REST-végpontokat.

### <a name="speech-to-text"></a>Speech-to-text

Referencia a hang-szöveg transzformációs dokumentációjáért lásd: [hang-szöveg transzformációs REST API-val](rest-speech-to-text.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="text-to-speech"></a>Szövegfelolvasás

Szöveg-hang transzformációs dokumentációja, lásd: [szöveg-hang transzformációs REST API-val](rest-text-to-speech.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
