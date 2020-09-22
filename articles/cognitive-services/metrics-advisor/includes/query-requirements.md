---
title: Lekérdezési követelmények
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 23da214f4ffc9e589523f7ada00a77494183eb15
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90940140"
---
A lekérdezésben használja a `@StartTime` paramétert az adott időbélyeghez tartozó metrikai adatok lekéréséhez. Ezt a rendszer a `yyyy-MM-ddTHH:mm:ss` formázó sztringtel helyettesíti. 

> [!IMPORTANT]
> Győződjön meg arról, hogy a lekérdezés csak **egyetlen időbélyegből** származó metrikus adatokat ad vissza. A metrikai tanácsadó minden időbélyegen futtatja a lekérdezést a megfelelő metrikai adatok beolvasásához. Például a *napi* részletességgel rendelkező metrika lekérdezése csak egyetlen időbélyeget tartalmazhat, például `2020-06-21T00:00:00Z` Ha egyszer futtatja a lekérdezést. 
