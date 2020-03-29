---
title: Feladatok újrapróbálkozása hiba után
description: Ellenőrizze a hibákat, majd próbálkozzon újra.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
tags: azure-batch
ms.assetid: 16279b23-60ff-4b16-b308-5de000e4c028
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/15/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 94ed936e619461a2dbf7ec837c2d80e21c01c88e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919991"
---
# <a name="detecting-and-handling-batch-service-errors"></a>Kötegelt szolgáltatási hibák észlelése és kezelése

Fontos megjegyezni, hogy ellenőrizze a hibákat, ha egy REST-szolgáltatás API-val dolgozik. Nem ritka, hogy hibák fordulnak elő kötegelt feladatok futtatásakor.

## <a name="common-errors"></a>Gyakori hibák 

- Hálózati hibák – ezek olyan kérelmek, amelyek soha nem érték el a Batch-et, vagy a Batch válasz nem érte el időben az ügyfelet.
- Belső szerverhibák - ezek szabványos 5xx állapotkód HTTP válasz.
- A szabályozás hibákat okozhat, például 429 vagy 503 állapotkód HTTP válaszok at a Retry-after fejléc.
- 4xx hibák, amelyek olyan hibákat tartalmaznak, mint a AlreadyExists és az InvalidOperation. Ez azt jelenti, hogy az erőforrás nem a megfelelő állapotban van az állapotátmenethez.

A hibakódok különböző típusairól és a konkrét hibakódokról a [Kötegállapota és a Hibakódok című témakörben talál](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes)részletes információt.

## <a name="when-to-retry"></a>Mikor kell újrapróbálkozni?

A batch API-k értesítést küld, ha hiba van. Ezek mindegyike újra próbálkozható, és ezek mindegyike tartalmaz egy globális újrapróbálkozási kezelőt erre a célra. A legjobb, ha ezt a beépített mechanizmust használja.

Hiba után várjon egy kicsit (néhány másodpercet az újrapróbálkozások között), mielőtt újrapróbálkozna. Ha túl gyakran vagy túl gyorsan próbálja meg újra, az újrapróbálkozáskezelő szabályozza a szabályozást.

### <a name="for-more-information"></a>További tudnivalók  

[A batch API-k és az eszközök](batch-apis-tools.md) API-referenciaadatokra mutató hivatkozások. A .NET API például rendelkezik egy [RetryPolicyProvider osztállyal,]( https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.retrypolicyprovider?view=azure-dotnet) ahol meg kell adni a szükséges újrapróbálkozási házirendet. 

Az egyes API-król és azok alapértelmezett újrapróbálkozási házirendjeiről a [Kötegállapot és hibakódok](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes)című olvasni tudó részletes információt talál.
