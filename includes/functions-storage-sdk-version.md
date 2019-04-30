---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: tdykstra
manager: cfowler
ms.service: functions
ms.topic: include
origin.date: 05/17/2018
ms.date: 05/30/2018
ms.author: v-junlch
ms.custom: include file
ms.openlocfilehash: f8733ef907b8f31ace7ea72f705ba1b37d1adece
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60306748"
---
### <a name="azure-storage-sdk-version-in-functions-1x"></a>A Functions az Azure Storage SDK-verzió 1.x

Függvények 1.x, tároló-eseményindítók és kötések használata az Azure Storage SDK 7.2.1 verzióját ([WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1) NuGet-csomag). A Storage SDK-t egy másik verziója hivatkozik, és a függvényfej a kötés Storage SDK-t típusra, a Functions futtatókörnyezete, hogy nem köthető az adott típusú feltétlenül jelentik. A megoldás az, hogy ellenőrizze, hogy a projekt referenciáihoz [WindowsAzure.Storage 7.2.1](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1).

<!-- ms.date: 05/30/2018 -->