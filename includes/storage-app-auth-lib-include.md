---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/18/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 5e605e4ad987db16b98649b32dc96fa1620b1564
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "69011995"
---
A .NET-hez készült [Microsoft Azure app Authentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) ügyféloldali kódtára (előzetes verzió) leegyszerűsíti a jogkivonat beszerzésének és megújításának folyamatát a kódból. Az alkalmazás-hitelesítési ügyféloldali kódtár automatikusan kezeli a hitelesítést. A könyvtár a fejlesztő hitelesítő adatait használja a helyi fejlesztés során végzett hitelesítéshez. A fejlesztői hitelesítő adatok használata a helyi fejlesztés során biztonságosabb, mert nem kell létrehoznia Azure AD-beli hitelesítő adatokat, vagy meg kell osztania a hitelesítő adatokat a fejlesztők között. Ha a megoldást később üzembe helyezi az Azure-ban, a könyvtár automatikusan az alkalmazás hitelesítő adatait használja.

Az alkalmazás-hitelesítési függvénytár Azure Storage-alkalmazásban való használatához telepítse a legújabb előzetes csomagot a [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)-ból, valamint az [Azure Storage közös .net-hez készült ügyféloldali kódtára](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/) és az [Azure Blob Storage ügyféloldali kódtára legújabb verzióját. a .NET-hez](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/). Adja hozzá a következő **using** utasításokat a kódhoz:

```csharp
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.Storage.Auth;
using Microsoft.Azure.Storage.Blob;
```
