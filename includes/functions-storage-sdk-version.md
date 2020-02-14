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
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198418"
---
#### <a name="azure-storage-sdk-version-in-functions-1x"></a>Az Azure Storage SDK verziója a functions 1. x verziójában

Az 1. x függvények esetében a tároló-eseményindítók és-kötések az Azure Storage SDK ([WindowsAzure. Storage](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1) NuGet-csomag) verzióját használják a 7.2.1. Ha a Storage SDK egy másik verziójára hivatkozik, és egy Storage SDK-típushoz kötődik a függvény aláírása során, akkor a functions futtatókörnyezet azt jelentheti, hogy az nem köthető ehhez a típushoz. A megoldás annak biztosítása, hogy a projekt a [WindowsAzure. Storage 7.2.1](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1)hivatkozik.
