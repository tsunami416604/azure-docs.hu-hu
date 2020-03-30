---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2020
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 6a37850eb6536c5399d63144e60ea210fbc194d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77198418"
---
#### <a name="azure-storage-sdk-version-in-functions-1x"></a>Az Azure Storage SDK-verziója az 1.x függvényekben

A Functions 1.x-ben a Storage eseményindítók és kötések az Azure Storage SDK[(WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1) NuGet csomag) 7.2.1-es verzióját használják. Ha a Storage SDK egy másik verziójára hivatkozik, és a függvényaláírásban egy Storage SDK-típushoz kötődik, a Functions futásidejű jelentheti, hogy nem köthető az adott típushoz. A megoldás annak biztosítása, hogy a projekt a [WindowsAzure.Storage 7.2.1](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1)rendszerre hivatkozzon.
