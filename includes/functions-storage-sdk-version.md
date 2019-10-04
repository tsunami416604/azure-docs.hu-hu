---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: ggailey777
manager: cfowler
ms.service: azure-functions
ms.topic: include
ms.date: 05/17/2018
ms.author: tdykstra
ms.custom: include file
ms.openlocfilehash: c2fff707dcaafac69efcad3dbf33446a7b797396
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67608225"
---
### <a name="azure-storage-sdk-version-in-functions-1x"></a>A Functions az Azure Storage SDK-verzió 1.x

Függvények 1.x, tároló-eseményindítók és kötések használata az Azure Storage SDK 7.2.1 verzióját ([WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1) NuGet-csomag). A Storage SDK-t egy másik verziója hivatkozik, és a függvényfej a kötés Storage SDK-t típusra, a Functions futtatókörnyezete, hogy nem köthető az adott típusú feltétlenül jelentik. A megoldás az, hogy ellenőrizze, hogy a projekt referenciáihoz [WindowsAzure.Storage 7.2.1](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1).
