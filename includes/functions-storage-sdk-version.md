---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: tdykstra
manager: cfowler
ms.service: functions
ms.topic: include
ms.date: 05/17/2018
ms.author: tdykstra
ms.custom: include file
ms.openlocfilehash: 97c56b07833f7d93541bb0b3747889f5a50a8203
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34675275"
---
### <a name="azure-storage-sdk-version-in-functions-1x"></a>A funkciók az Azure Storage szolgáltatás SDK-verzió 1.x

Funkciók 1.x, a tárolási eseményindítók és kötések használja az Azure Storage szolgáltatás SDK 7.2.1 verzióját ([windowsazure.Storage kifejezésre](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1) NuGet-csomag). A Storage szolgáltatás SDK eltérő verziójú hivatkozik, és a függvényaláíráshoz a Storage szolgáltatás SDK típusú kötés, ha a Functions futtatókörnyezete, hogy nem köthető, hogy a típus lehet, hogy jelentést. A megoldás, hogy ellenőrizze, hogy a projekt hivatkozásait [windowsazure.Storage kifejezésre 7.2.1](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1).
