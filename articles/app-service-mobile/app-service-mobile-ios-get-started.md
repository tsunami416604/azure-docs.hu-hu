---
title: "iOS-alkalmazás létrehozása az Azure App Service Mobile Apps szolgáltatásban| Microsoft Docs"
description: "Az útmutató bevezeti Önt az Azure-alapú mobil-háttéralkalmazások használatával megvalósítható, Objective-C vagy Swift nyelven történő iOS-fejlesztésbe."
services: app-service\mobile
documentationcenter: ios
author: ggailey777
manager: syntaxc4
editor: 
ms.assetid: 6461a899-9340-42dd-b118-ffc5ba00e846
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: glenga
ms.openlocfilehash: 36936ae66c458fcbedeec95cfa2f573a40c8af53
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-ios-app"></a>iOS-alkalmazás létrehozása
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Áttekintés
Az útmutató azt mutatja be, hogyan adhatja hozzá az [Azure Mobile Apps](app-service-mobile-value-prop.md) felhőalapú háttérszolgáltatást az iOS-alkalmazásokhoz. Először létre kell hoznunk egy új mobil-háttéralkalmazást. Ezt követően egy egyszerű *Tennivalólista* iOS-alkalmazást fogunk használni az adatoknak az Azure-ban való tárolására.

Az oktatóanyag elvégzéséhez egy Mac gépre és [egy Azure-fiókra](https://azure.microsoft.com/pricing/free-trial/) lesz szüksége.

## <a name="step-i-create-a-new-azure-mobile-app-backend"></a>1. lépés: Új Azure Mobile Apps-háttéralkalmazás létrehozása
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="step-ii-configure-the-backend-project"></a>2. lépés: A háttéralkalmazás-projekt konfigurálása
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="step-iii-download-and-run-the-ios-app"></a>3. lépés: Az iOS-alkalmazás letöltése és futtatása
[!INCLUDE [app-service-mobile-ios-run-app](../../includes/app-service-mobile-ios-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
