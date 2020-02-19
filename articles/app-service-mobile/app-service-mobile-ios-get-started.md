---
title: iOS-alkalmazás létrehozása
description: Ezt az oktatóanyagot követve megkezdheti Azure mobile app háttérrendszer használatát az iOS-alapú fejlesztéshez az Objective-C-ben vagy a Swift-ben.
ms.assetid: 6461a899-9340-42dd-b118-ffc5ba00e846
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: 1b1114a22d33689f485aa228a8a1cf65eba719da
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461487"
---
# <a name="create-an-ios-app"></a>iOS-alkalmazás létrehozása

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]
## <a name="overview"></a>Áttekintés

Az útmutató azt mutatja be, hogyan adhatja hozzá az [Azure App Service Mobile Apps](app-service-mobile-value-prop.md) felhőalapú háttérszolgáltatást az iOS-alkalmazásokhoz. Az első lépés egy új mobil háttérszolgáltatás létrehozása az Azure-on. Ezt követően töltsön le egy egyszerű *Tennivalólista* iOS-mintaalkalmazást, amely az Azure-ban tárolja az adatokat.

Az oktatóanyag elvégzéséhez egy Mac gépre és [egy Azure-fiókra](https://azure.microsoft.com/pricing/free-trial/) lesz szüksége.

## <a name="create-a-new-azure-mobile-app-backend"></a>Új Azure Mobile Apps-háttéralkalmazás létrehozása

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Adatbázis-kapcsolatok létrehozása és az ügyfél és a kiszolgáló projekt konfigurálása
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-ios-app"></a>IOS-alkalmazás futtatása

[!INCLUDE [app-service-mobile-ios-run-app](../../includes/app-service-mobile-ios-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
