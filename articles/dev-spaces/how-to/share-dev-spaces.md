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
ms.openlocfilehash: c469a71ec1357dd6f260ab613fed72110d1ed880
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2018
ms.locfileid: "34824353"
---
# <a name="share-azure-dev-spaces"></a>Megosztás Azure fejlesztői szóközöket

A Azure fejlesztői szóközt tartalmaz a csapat a fejlesztői tárhely másokkal is megoszthatja. Minden egyes fejlesztői saját tárolóhelyet mások számára megtörje való nélkül is használhatók. Is egy helyről együttesen működő lehetővé teszik a kód-végpontok tesztelése mocks létrehozása vagy szimulálása függőségek nélkül. Tekintse meg a [további információ a fejlesztési csapat](../get-started-nodejs.md#learn-about-team-development) útmutatójában talál.

Fejlesztői adhatja beállítása több fejlesztőknek:
1. Hozzon létre egy fejlesztői tárolóhelyet az Azure-ban. Válasszon [.NET Core és a Visual STUDIO Code](../get-started-netcore.md), [.NET Core és a Visual Studio](../get-started-netcore-visualstudio.md), vagy [Node.js és a Visual STUDIO Code](../get-started-nodejs.md). A kijelölt Azure-előfizetés tulajdonosának vagy Közreműködőjének hozzáférése lesz szüksége.
1. Konfigurálja az Azure fejlesztői területet **erőforráscsoport** való [közreműködői hozzáférést](/azure/active-directory/role-based-access-control-configure) minden csapattag. A fejlesztői terület erőforráscsoport Ez a parancs futtatásával ellenőrizheti: `azds resource list`
1. Kérje meg a csoport tagjai **jelölje ki a fejlesztői területet** azt fejlesztése érdekében.
     * **Parancssor vagy a Visual STUDIO Code**: meglévő Azure fejlesztői Spacess megjelenítéséhez rendelkezik hozzáféréssel: `azds resource list`. Jelölje be a fejlesztési adhatja: `azds resource select`.
     * **Visual Studio IDE**: Nyissa meg a projektet a Visual Studio felületén válassza **Azure fejlesztői szóközöket** az indítási beállítások legördülő. A megnyíló párbeszédpanelen válasszon egy meglévő fürthöz.

![A Visual Studio indítási beállítások legördülő](../media/get-started-netcore-visualstudio/LaunchSettings.png)