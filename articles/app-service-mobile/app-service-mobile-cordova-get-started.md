---
title: Cordova alkalmazás létrehozása
description: Az útmutató bevezeti Önt az Azure-alapú mobil-háttéralkalmazások használatával megvalósítható, Apache Cordova keretrendszerben történő fejlesztésbe.
keywords: cordova,javascript,mobil,ügyfél
ms.assetid: 0b08fc12-0a80-42d3-9cc1-9b3f8d3e3a3f
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: 99fb4a4b07ecbd4a85abbc62ec52a0f5960654c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461572"
---
# <a name="create-an-apache-cordova-app"></a>Apache Cordova-alkalmazás létrehozása
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag azt ismerteti, hogyan adhat felhőalapú háttérszolgáltatásokat az Apache Cordova-mobilalkalmazásokhoz egy Azure-alapú mobil-háttéralkalmazás segítségével.  A folyamat során létrehoz egy új mobil-háttéralkalmazást, illetve egy egyszerű *Tennivalólista* Apache Cordova-alkalmazást, amely alkalmazásadatokat tárol az Azure-ban.

Az oktatóanyag végrehajtása feltétele az Azure App Service Mobile Apps szolgáltatásának használatát ismertető többi Apache Cordova-oktatóanyag megértésének.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag teljesítéséhez a következő előfeltételekre lesz szüksége:

* Számítógép, amelyen fut a [Visual Studio Community 2017] vagy újabb verzió.
* [Visual Studio Eszközök Apache Cordova].
* [Aktív Azure-fiók](https://azure.microsoft.com/pricing/free-trial/).

Közvetlenül az Apache Cordova parancssorának használata esetén nincs szükség a Visual Studióra.  A parancssor használata akkor hasznos, ha az oktatóanyagot Mac gépen szeretné elvégezni.  Az oktatóanyag nem tér ki arra, hogy miképpen lehet az Apache Cordova-ügyfélalkalmazásokat parancssor segítségével lefordítani.

## <a name="create-an-azure-mobile-app-backend"></a>Azure mobil-háttéralkalmazás létrehozása
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Adatbázis-kapcsolat létrehozása és az ügyfél- és kiszolgálóprojekt konfigurálása
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-apache-cordova-app"></a>Az Apache Cordova-alkalmazás letöltése és futtatása
[!INCLUDE [app-service-mobile-cordova-run-app](../../includes/app-service-mobile-cordova-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/

[Visual Studio Közösség 2017]: https://www.visualstudio.com/
[Visual Studio Tools for Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
