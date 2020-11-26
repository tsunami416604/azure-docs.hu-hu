---
author: baanders
description: fájl belefoglalása az Azure digitális Twins-ba – a példány kezelésének módjai
ms.service: digital-twins
ms.topic: include
ms.date: 11/11/2020
ms.author: baanders
ms.openlocfilehash: 887d185249f96b5d3be4aab6a96aa3c6c4a85690
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2020
ms.locfileid: "96231412"
---
Ez a cikk bemutatja, hogyan végezheti el a különböző felügyeleti műveleteket az [Azure Digital Twins .net (C#) **SDK**](/dotnet/api/overview/azure/digitaltwins/management?view=azure-dotnet&preserve-view=true)használatával. Ugyanezeket a felügyeleti hívásokat a következő témakörben ismertetett egyéb nyelvi SDK-kkal is elvégezheti [*: az Azure Digital Twins API-k és SDK*](../articles/digital-twins/how-to-use-apis-sdks.md)-k használatával.

> [!TIP] 
> Ne feledje, hogy minden SDK-módszer szinkron és aszinkron verziókban érhető el. A lapozási hívások esetében az aszinkron metódusok a `AsyncPageable<T>` szinkron verziók visszaadása után térnek vissza `Pageable<T>` .

Egy másik felügyeleti lehetőség a témakörben található Azure digitális ikrek [**REST API**](/rest/api/azure-digitaltwins/) -k hívása közvetlenül egy Rest-ügyfél, például a Poster használatával. Ennek módjáról a következő témakörben talál útmutatást [*: a kérelmek küldése a Poster használatával*](../articles/digital-twins/how-to-use-postman.md).

Végül az Azure Digital Twins **CLI** használatával is végrehajthatja ugyanezeket a kezelési műveleteket. Ha többet szeretne megtudni a parancssori felület használatáról, olvassa el a következő témakört [*: útmutató: az Azure digitális Twins parancssori*](../articles/digital-twins/how-to-use-cli.md)felületének használata.