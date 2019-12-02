---
title: Android-alkalmazás készítése
description: Ezt az oktatóanyagot követve megismerheti Azure mobile app háttérrendszer használatát az Android-alkalmazások fejlesztéséhez.
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34a9
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: d63faf5c06e648e4bd886d6e62c35a2b451c8c44
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668586"
---
# <a name="create-an-android-app"></a>Android-alkalmazás készítése
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> A Visual Studio App Center támogatja a végpontok közötti, valamint az integrált szolgáltatásközpont és a mobilalkalmazás közötti fejlesztést. A fejlesztők **buildelési**, **tesztelési** és **elosztási** szolgáltatásokkal állíthatják be a folyamatos integrációval és szolgáltatásnyújtással kapcsolatos folyamatot. Az alkalmazás üzembe helyezése után a fejlesztők **elemzési** és **diagnosztikai** szolgáltatásokkal monitorozhatják az alkalmazás állapotát és használatát, illetve **leküldéses** szolgáltatással kommunikálhatnak a felhasználókkal. Emellett a fejlesztők a **Hitelesítés** szolgáltatással hitelesíthetik felhasználóikat, az **Adatok** szolgáltatással pedig megőrizhetik és szinkronizálhatják az alkalmazásadatokat a felhőben.
>
> Ha szeretné a felhőszolgáltatásokat a mobilalkalmazásba integrálni, regisztráljon az [App Centerbe](https://appcenter.ms/signup?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) még ma.

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag azt ismerteti, hogyan adhat felhőalapú háttérszolgáltatásokat az Android-mobilalkalmazásokhoz egy Azure-alapú mobil-háttéralkalmazás segítségével.  Létre fog hozni egy új mobil-háttéralkalmazást, illetve egy egyszerű *Tennivalólista* Android-alkalmazást, amely alkalmazásadatokat tárol az Azure-ban.

Az oktatóanyag végrehajtása feltétele az Azure App Service Mobile Apps szolgáltatásának használatát ismertető többi Android-oktatóanyag megértésének.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* [Android Developer Tools](https://developer.android.com/sdk/index.html): ez a csomag tartalmazza az androidos integrált fejlesztőkörnyezetet, valamint a legújabb Android-platformot.
* Azure Mobile Android SDK.
* [Aktív Azure-fiók](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-new-azure-mobile-app-backend"></a>Új Azure Mobile Apps-háttéralkalmazás létrehozása
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Adatbázis-kapcsolatok létrehozása és az ügyfél és a kiszolgáló projekt konfigurálása
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-android-app"></a>Az Android-alkalmazás futtatása
[!INCLUDE [app-service-mobile-android-run-app](../../includes/app-service-mobile-android-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
