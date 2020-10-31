---
author: baanders
description: fájl belefoglalása az Azure Digital Twins-modellek érvényesítéséhez
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: bccf92c6c912747e28b3075f12ac1558cced30c9
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130381"
---
> [!TIP]
> A modell létrehozása után ajánlott a modelleket offline állapotba helyezni, mielőtt feltölti őket az Azure Digital Twins-példányba.

A modell dokumentumainak érvényesítéséhez egy nyelvtől független minta áll rendelkezésre, amely biztosítja, hogy a DTDL helyes legyen, mielőtt feltölti azt a példányba. Itt található: [**DTDL validator minta**](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator).

* A DTDL-érvényesítő minta egy .NET DTDL-elemző könyvtárra épül, amely ügyféloldali kódtárként érhető el a NuGet-ben: [**Microsoft. Azure. DigitalTwins. Parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/). A könyvtárat közvetlenül is használhatja a saját ellenőrzési megoldás megtervezéséhez. Az elemző függvénytár használatakor ügyeljen arra, hogy olyan verziót használjon, amely kompatibilis az Azure Digital ikrek által futtatott verziójával. Jelenleg ez a verzió *3.12.4* .

További információ az érvényesítő mintáról és az elemző könyvtárról, beleértve a használati példákat is, a [*útmutatóban: modellek elemzése és érvényesítése*](../articles/digital-twins/how-to-parse-models.md).