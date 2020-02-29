---
title: Feladatok újrapróbálása hiba után
description: Keressen hibákat, és próbálkozzon újra.
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
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2020
ms.locfileid: "77919991"
---
# <a name="detecting-and-handling-batch-service-errors"></a>A Batch szolgáltatás hibáinak észlelése és feldolgozása

Fontos megjegyezni, hogy a REST-szolgáltatás API-jával való munka során hibákat kell megnéznie. Nem ritka, hogy a Batch-feladatok futtatásakor hibák történnek.

## <a name="common-errors"></a>Gyakori hibák 

- Hálózati hibák – ezek olyan kérések, amelyek soha nem érik el a köteget, vagy a Batch-válasz nem érte el időben az ügyfelet.
- Belső kiszolgálóhiba – a szabványos 5xx HTTP-válasz.
- A szabályozás olyan hibákat okozhat, mint például az 429-es vagy a 503-es állapotkód HTTP-válaszok az újrapróbálkozási fejléctel.
- 4xx hibák, amelyek a már létezik és a InvalidOperation ilyen hibákat tartalmaznak. Ez azt jelenti, hogy az erőforrás nincs megfelelő állapotban az állapot átváltásához.

A különböző típusú hibakódokról és az adott hibakódokról a [Batch állapot-és hibakódok](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes)című témakörben olvashat bővebben.

## <a name="when-to-retry"></a>Mikor próbálkozzon újra

Ha hiba lép fel, a Batch API-k értesítik. Mindegyiket újra lehet próbálni, és mindegyikhez tartozik egy globális újrapróbálkozási kezelő erre a célra. A legjobb megoldás a beépített mechanizmus használata.

Hiba után várjon egy kicsit (több másodpercet az újrapróbálkozások között) az újrapróbálkozás előtt. Ha túl gyakran vagy túl gyorsan próbálkozik újra, a rendszer az újrapróbálkozási kezelőt fogja szabályozni.

### <a name="for-more-information"></a>További tudnivalók  

A [Batch API-k és eszközök](batch-apis-tools.md) API-referenciára mutató hivatkozásokat tartalmaznak. A .NET API-nak például egy [RetryPolicyProvider osztálya]( https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.retrypolicyprovider?view=azure-dotnet) van, ahol meg kell adni a szükséges újrapróbálkozási házirendet. 

Az egyes API-k és az alapértelmezett újrapróbálkozási szabályzatok részletes ismertetéséhez olvassa el a [Batch-állapot és-hibakódok](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes)című témakört.
