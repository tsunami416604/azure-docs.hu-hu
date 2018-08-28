---
title: iOS-alkalmazás létrehozása az Azure App Service Mobile Apps szolgáltatásban| Microsoft Docs
description: Az útmutató bevezeti Önt az Azure-alapú mobil-háttéralkalmazások használatával megvalósítható, Objective-C vagy Swift nyelven történő iOS-fejlesztésbe.
services: app-service\mobile
documentationcenter: ios
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 6461a899-9340-42dd-b118-ffc5ba00e846
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: hero-article
ms.date: 08/17/2018
ms.author: crdun
ms.openlocfilehash: 9809b51f1279c99de69cd1c219ffc57351ff21ef
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2018
ms.locfileid: "41921053"
---
# <a name="create-an-ios-app"></a>iOS-alkalmazás létrehozása

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Áttekintés

Az útmutató azt mutatja be, hogyan adhatja hozzá az [Azure App Service Mobile Apps](app-service-mobile-value-prop.md) felhőalapú háttérszolgáltatást az iOS-alkalmazásokhoz. Az első lépés egy új mobil háttérszolgáltatás létrehozása az Azure-on. Ezt követően töltsön le egy egyszerű *Tennivalólista* iOS-mintaalkalmazást, amely az Azure-ban tárolja az adatokat.

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
