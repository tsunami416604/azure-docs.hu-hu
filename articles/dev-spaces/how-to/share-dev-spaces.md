---
title: Hogyan oszthatja meg Azure fejlesztői szóközöket |} Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: article
description: Gyors Kubernetes-fejlesztés tárolókkal és mikroszolgáltatásokkal az Azure-ban
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, tárolók
manager: douge
ms.openlocfilehash: ca644cc412ad0c8f0e2a0781d9419fba58ed8d12
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35247704"
---
# <a name="share-azure-dev-spaces"></a>Megosztás Azure fejlesztői szóközöket

A Azure fejlesztői szóközt tartalmaz a csapat a fejlesztői tárhely másokkal is megoszthatja. Minden egyes fejlesztői saját tárolóhelyet mások számára megtörje való nélkül is használhatók. Is egy helyről együttesen működő lehetővé teszik a kód-végpontok tesztelése mocks létrehozása vagy szimulálása függőségek nélkül. Tekintse meg a [további információ a fejlesztési csapat](../get-started-nodejs.md#learn-about-team-development) útmutatójában talál.

Fejlesztői adhatja beállítása több fejlesztőknek:
1. Hozzon létre egy fejlesztői tárolóhelyet az Azure-ban. Válasszon [.NET Core és a Visual STUDIO Code](../get-started-netcore.md), [.NET Core és a Visual Studio](../get-started-netcore-visualstudio.md), vagy [Node.js és a Visual STUDIO Code](../get-started-nodejs.md). A kijelölt Azure-előfizetés tulajdonosának vagy Közreműködőjének hozzáférése lesz szüksége.
1. Konfigurálja az Azure fejlesztői területet **erőforráscsoport** való [közreműködői hozzáférést](/azure/active-directory/role-based-access-control-configure) minden csapattag. A fejlesztői terület erőforráscsoport Ez a parancs futtatásával ellenőrizheti: `azds list`
1. Kérje meg a csoport tagjai **jelölje ki a fejlesztői területet** azt fejlesztése érdekében.
     * **Parancssor vagy a Visual STUDIO Code**: meglévő Azure fejlesztői szóközöket megjelenítéséhez rendelkezik hozzáféréssel: `azds space list`. Jelölje be a fejlesztési adhatja: `azds space select`.
     * **Visual Studio IDE**: Nyissa meg a projektet a Visual Studio felületén válassza **Azure fejlesztői szóközöket** az indítási beállítások legördülő. A megnyíló párbeszédpanelen válasszon egy meglévő fürthöz.

![A Visual Studio indítási beállítások legördülő](../media/get-started-netcore-visualstudio/LaunchSettings.png)