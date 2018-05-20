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
description: Gyors Kubernetes fejlesztése a tárolók és mikroszolgáltatások létrehozására az Azure-on
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes szolgáltatás, tárolók
manager: douge
ms.openlocfilehash: 140a487a0a605e0f03122868af0d2d12bcdba35b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="share-azure-dev-spaces"></a>Megosztás Azure fejlesztői szóközöket

A Azure fejlesztői szóközt tartalmaz a csapat a fejlesztői tárhely másokkal is megoszthatja. Minden egyes fejlesztői saját tárolóhelyet mások számára megtörje való nélkül is használhatók. Is egy helyről együttesen működő lehetővé teszik a kód-végpontok tesztelése mocks létrehozása vagy szimulálása függőségek nélkül. Tekintse meg a [további információ a fejlesztési csapat](../get-started-nodejs.md#learn-about-team-development) útmutatójában talál.

Egy környezet beállítása több fejlesztőknek:
1. Hozzon létre egy fejlesztői tárolóhelyet az Azure-ban. Válasszon [.NET Core és a Visual STUDIO Code](../get-started-netcore.md), [.NET Core és a Visual Studio](../get-started-netcore-visualstudio.md), vagy [Node.js és a Visual STUDIO Code](../get-started-nodejs.md). A kijelölt Azure-előfizetés tulajdonosának vagy Közreműködőjének hozzáférése lesz szüksége.
1. Konfigurálja az Azure fejlesztői területet **erőforráscsoport** való [közreműködői hozzáférést](/azure/active-directory/role-based-access-control-configure) minden csapattag. Ez a parancs futtatásával ellenőrizheti egy környezet erőforráscsoport: `azds resource list`
1. Kérje meg a csoport tagjai **válassza ki azt a környezetet** azt fejlesztése érdekében.
     * **Parancssor vagy a Visual STUDIO Code**: meglévő Azure fejlesztői Spacess megjelenítéséhez rendelkezik hozzáféréssel: `azds resource list`. Jelölje be egy olyan környezetben: `azds resource select`.
     * **Visual Studio IDE**: Nyissa meg a projektet a Visual Studio felületén válassza **Azure fejlesztői szóközöket** az indítási beállítások legördülő. A megnyíló párbeszédpanelen válasszon egy meglévő fejlesztőkörnyezet.

![A Visual Studio indítási beállítások legördülő](../media/get-started-netcore-visualstudio/LaunchSettings.png)