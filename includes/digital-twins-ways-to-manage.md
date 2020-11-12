---
author: baanders
description: fájl belefoglalása az Azure digitális Twins-ba – a példány kezelésének módjai
ms.service: digital-twins
ms.topic: include
ms.date: 11/11/2020
ms.author: baanders
ms.openlocfilehash: 47de00780fb259e7476c16dea9b7c63bc512a211
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94533892"
---
Ez a cikk bemutatja, hogyan végezheti el a különböző felügyeleti műveleteket az [Azure Digital Twins .net (C#) **SDK**](/dotnet/api/overview/azure/digitaltwins/management?view=azure-dotnet&preserve-view=true)használatával. Ugyanezeket a felügyeleti hívásokat a következő témakörben ismertetett egyéb nyelvi SDK-kkal is elvégezheti [*: az Azure Digital Twins API-k és SDK*](../articles/digital-twins/how-to-use-apis-sdks.md)-k használatával.

> [!TIP] 
> Ne feledje, hogy minden SDK-módszer szinkron és aszinkron verziókban érhető el. A lapozási hívások esetében az aszinkron metódusok a `AsyncPageable<T>` szinkron verziók visszaadása után térnek vissza `Pageable<T>` .

Egy másik felügyeleti lehetőség, hogy az Azure Digital Twins [**REST API-kat**](/rest/api/azure-digitaltwins/) hívja közvetlenül a témakör területére.

Végül az Azure Digital Twins **CLI** használatával is végrehajthatja ugyanezeket a kezelési műveleteket. Ha többet szeretne megtudni a parancssori felület használatáról, olvassa el a következő témakört [*: útmutató: az Azure digitális Twins parancssori*](../articles/digital-twins/how-to-use-cli.md)felületének használata.