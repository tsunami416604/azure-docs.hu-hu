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
ms.openlocfilehash: eda567fda13d6caca679d0ce4947e042eca9530d
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77652008"
---
# <a name="detecting-and-handling-batch-service-errors"></a>A Batch szolgáltatás hibáinak észlelése és feldolgozása

Fontos megjegyezni, hogy a REST-szolgáltatás API-jával való munka során hibákat kell megnéznie. Nem ritka, hogy a Batch-feladatok futtatásakor hibák történnek.

## <a name="common-errors"></a>Gyakori hibák 

- Hálózati hibák – ezek olyan kérések, amelyek soha nem érik el a köteget, vagy a Batch-válasz nem érte el időben az ügyfelet.
- Belső kiszolgálóhiba – a szabványos 5xx HTTP-válasz.
- A szabályozás olyan hibákat okozhat, mint például az 429-es vagy a 503-es állapotkód HTTP-válaszok az újrapróbálkozási fejléctel.
- 4xx hibák, amelyek a már létezik és a InvalidOperation ilyen hibákat tartalmaznak. Ez azt jelenti, hogy az erőforrás nincs megfelelő állapotban az állapot átváltásához.

## <a name="when-to-retry"></a>Mikor próbálkozzon újra

Ha hiba lép fel, a Batch API-k értesítik. Mindegyiket újra lehet próbálni, és mindegyikhez tartozik egy globális újrapróbálkozási kezelő erre a célra. A legjobb megoldás a beépített mechanizmus használata.

Hiba után várjon egy kicsit (több másodpercet az újrapróbálkozások között) az újrapróbálkozás előtt. Ha túl gyakran vagy túl gyorsan próbálkozik újra, a rendszer az újrapróbálkozási kezelőt fogja szabályozni.


Az egyes API-k és az alapértelmezett újrapróbálkozási szabályzatok részletes ismertetéséhez olvassa el a [Batch-állapot és-hibakódok](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes)című témakört.
