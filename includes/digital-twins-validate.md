---
author: baanders
description: fájl belefoglalása az Azure Digital Twins-modellek érvényesítéséhez
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: 0146660fa50f9a136cd82483200ca2e6c6cc0b89
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2020
ms.locfileid: "87985876"
---
> [!TIP]
> A modell létrehozása után ajánlott a modelleket offline állapotba helyezni, mielőtt feltölti őket az Azure Digital Twins-példányba.

A DTDL helyességének biztosítása érdekében egy nyelvtől független minta érhető el a modell dokumentumainak ellenőrzéséhez. Itt található: [**DTDL validator minta**](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator).

A DTDL-érvényesítő minta egy .NET DTDL-elemző könyvtárra épül, amely ügyféloldali kódtárként érhető el a NuGet-ben: [**Microsoft. Azure. DigitalTwins. Parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/). A könyvtárat közvetlenül is használhatja a saját ellenőrzési megoldás megtervezéséhez. Az elemző függvénytár használatakor ügyeljen arra, hogy olyan verziót használjon, amely kompatibilis az Azure Digital ikrek által futtatott verziójával. Az előzetes verzióban ez a *3.7.0*verziója.

További információ az érvényesítő mintáról és az elemző könyvtárról, beleértve a használati példákat is, a [*útmutatóban: modellek elemzése és érvényesítése*](../articles/digital-twins/how-to-parse-models.md).