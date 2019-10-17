---
title: Cordova-alkalmazás létrehozása az Azure App Service Mobile Apps szolgáltatásban| Microsoft Docs
description: Az útmutató bevezeti Önt az Azure-alapú mobil-háttéralkalmazások használatával megvalósítható, Apache Cordova keretrendszerben történő fejlesztésbe.
services: app-service\mobile
documentationcenter: javascript
author: elamalani
manager: crdun
editor: ''
tags: ''
keywords: cordova,javascript,mobil,ügyfél
ms.assetid: 0b08fc12-0a80-42d3-9cc1-9b3f8d3e3a3f
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: a1baa26fee46a4bba5db9652cf1e65e9d473284a
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388826"
---
# <a name="create-an-apache-cordova-app"></a>Apache Cordova-alkalmazás létrehozása
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> A Visual Studio App Center támogatja a végpontok közötti, valamint az integrált szolgáltatásközpont és a mobilalkalmazás közötti fejlesztést. A fejlesztők **buildelési**, **tesztelési** és **elosztási** szolgáltatásokkal állíthatják be a folyamatos integrációval és szolgáltatásnyújtással kapcsolatos folyamatot. Az alkalmazás üzembe helyezése után a fejlesztők **elemzési** és **diagnosztikai** szolgáltatásokkal monitorozhatják az alkalmazás állapotát és használatát, illetve **leküldéses** szolgáltatással kommunikálhatnak a felhasználókkal. Emellett a fejlesztők a **Hitelesítés** szolgáltatással hitelesíthetik felhasználóikat, az **Adatok** szolgáltatással pedig megőrizhetik és szinkronizálhatják az alkalmazásadatokat a felhőben.
>
> Ha szeretné integrálni a Cloud Servicest a mobil alkalmazásban, regisztráljon [app Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) még ma.

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag azt ismerteti, hogyan adhat felhőalapú háttérszolgáltatásokat az Apache Cordova-mobilalkalmazásokhoz egy Azure-alapú mobil-háttéralkalmazás segítségével.  A folyamat során létrehoz egy új mobil-háttéralkalmazást, illetve egy egyszerű *Tennivalólista* Apache Cordova-alkalmazást, amely alkalmazásadatokat tárol az Azure-ban.

Az oktatóanyag végrehajtása feltétele az Azure App Service Mobile Apps szolgáltatásának használatát ismertető többi Apache Cordova-oktatóanyag megértésének.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag teljesítéséhez a következő előfeltételekre lesz szüksége:

* Számítógép, amelyen fut a [Visual Studio Community 2017] vagy újabb verzió.
* [Visual Studio Tools for Apache Cordova].
* [Aktív Azure-fiók](https://azure.microsoft.com/pricing/free-trial/).

Közvetlenül az Apache Cordova parancssorának használata esetén nincs szükség a Visual Studióra.  A parancssor használata akkor hasznos, ha az oktatóanyagot Mac gépen szeretné elvégezni.  Az oktatóanyag nem tér ki arra, hogy miképpen lehet az Apache Cordova-ügyfélalkalmazásokat parancssor segítségével lefordítani.

## <a name="create-an-azure-mobile-app-backend"></a>Azure mobil-háttéralkalmazás létrehozása
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Adatbázis-kapcsolatok létrehozása és az ügyfél és a kiszolgáló projekt konfigurálása
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-apache-cordova-app"></a>Az Apache Cordova-alkalmazás letöltése és futtatása
[!INCLUDE [app-service-mobile-cordova-run-app](../../includes/app-service-mobile-cordova-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/

[Visual Studio Community 2017]: https://www.visualstudio.com/
[Visual Studio Tools for Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
