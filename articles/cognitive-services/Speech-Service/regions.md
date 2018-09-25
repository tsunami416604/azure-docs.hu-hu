---
title: Beszéd szolgáltatási régiók
description: A beszédfelismerési szolgáltatás régiók referenciája.
services: cognitive-services
author: mahilleb-msft
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 09/24/2018
ms.author: mahilleb
ms.openlocfilehash: 8485caeff3a7c96ed8f7403befac0026fae16e90
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46987560"
---
# <a name="regions-of-the-speech-service"></a>A beszédfelismerési szolgáltatás régiók

A beszédfelismerési szolgáltatás különböző régiókban érhető el.
Ha egy előfizetést hoz létre, az igényei szerint rendelkezésre álló régiók hajthatók végre.

Az előfizetés használatakor számolnia a kiválasztott régióban.

## <a name="rest-api"></a>REST API

A REST API segítségével válassza ki a megfelelő régióspecifikus végpontok.
Lásd: [REST API-k](rest-apis.md) részleteiről.

## <a name="speech-sdk"></a>Beszéd SDK

Az a [beszéd SDK](speech-sdk.md), régiók karakterláncként vannak megadva (például, hogy paraméterként `SpeechConfig.FromSubscription` beszéd használata a C# SDK-ban).

### <a name="regions-for-speech-recognition-and-translation"></a>A beszédfelismerés és -fordítási régiók

A következő táblázat felsorolja az elérhető régiók **beszédfelismerés** és **fordítási**.

  Régió | Beszéd SDK paraméter | Portál
 ------|-------|--------
 USA nyugati régiója | `westus` | https://westus.cris.ai
 USA 2. nyugati régiója | `westus2` | https://westus2.cris.ai 
 USA keleti régiója | `eastus` | https://eastus.cris.ai
 USA 2. keleti régiója | `eastus2` | https://eastus2.cris.ai
 Kelet-Ázsia | `eastasia` | https://eastasia.cris.ai
 Délkelet-Ázsia | `southeastasia` | https://southeastasia.cris.ai
 Észak-Európa | `northeurope` | https://northeurope.cris.ai
 Nyugat-Európa | `westeurope` | https://westeurope.cris.ai


### <a name="regions-for-intent-recognition"></a>Régióban a szándékának felismerése

Választható régiók **szándékának felismerése** a Speech SDK-n keresztül jelennek meg a [Language Understanding szolgáltatásoldal régió](/azure/cognitive-services/luis/luis-reference-regions).
Minden felsorolt közzétételi régió a végpont a tartománynév első része a megfelelő beszédfelismerési SDK régió paraméter határozza meg.
Például `westus` , adja meg az USA nyugati közzétételi régiója.
