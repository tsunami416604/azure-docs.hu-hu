---
title: Lekérdezési követelmények
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 68b5946cb21bef1c8979a94c8780682cfe9defa6
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376264"
---
A lekérdezésben használja a `@StartTime` paramétert az adott időbélyeghez tartozó metrikai adatok lekéréséhez. Ezt a rendszer a `yyyy-MM-ddTHH:mm:ss` formázó sztringtel helyettesíti. 

> [!IMPORTANT]
> Győződjön meg arról, hogy a lekérdezés csak **egyetlen időbélyegből** származó metrikus adatokat ad vissza. A metrikai tanácsadó minden időbélyegen futtatja a lekérdezést a megfelelő metrikai adatok beolvasásához. Például a *napi* részletességgel rendelkező metrika lekérdezése csak egyetlen időbélyeget tartalmazhat, például `2020-06-21T00:00:00Z` Ha egyszer futtatja a lekérdezést. 
