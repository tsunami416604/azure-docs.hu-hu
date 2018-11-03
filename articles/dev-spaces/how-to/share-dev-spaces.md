---
title: Hogyan oszthatja meg az Azure fejlesztési tárolóhelyek |} A Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: iainfoulds
ms.author: iainfou
ms.date: 05/11/2018
ms.topic: article
description: Gyors Kubernetes-fejlesztés tárolókkal és mikroszolgáltatásokkal az Azure-ban
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, tárolók
ms.openlocfilehash: 11c9f07731c58f619ff109def3ca5c436efac24e
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2018
ms.locfileid: "50977770"
---
# <a name="share-azure-dev-spaces"></a>Az Azure fejlesztői, szóközök megosztása

Azure fejlesztői szóközöket megoszthatja a csapat a fejlesztési tárhely másokkal. Minden fejlesztő saját tárolóhelyet, a használhatatlanná tévő mások lehetőségének nélkül is használhatók. Is együttműködése egy tárolóhely engedélyezheti, hogy tesztelje a kódot teljes körű mocks létrehozása és a függőségek szimulálása nélkül. Tekintse meg a [megismerheti a fejlesztési csapat](../team-development-nodejs.md) útmutató tartalmaz további tudnivalókat.

## <a name="set-up-a-dev-space-for-multiple-developers"></a>Állítsa be egy fejlesztési terület több fejlesztő

1. Hozzon létre egy fejlesztési tárolóhelyet az Azure-ban. Válasszon [.NET Core- és a VS Code](../get-started-netcore.md), [.NET Core és a Visual Studio](../get-started-netcore-visualstudio.md), vagy [Node.js és a VS Code](../get-started-nodejs.md). A kiválasztott Azure-előfizetés tulajdonosa vagy Közreműködője hozzá kell.
1. Konfigurálja az Azure fejlesztési területet **erőforráscsoport** való [közreműködői hozzáférés biztosítása](/azure/active-directory/role-based-access-control-configure) minden egyes csapattag számára. Ez a parancs futtatásával ellenőrizheti egy fejlesztési terület erőforráscsoportot: `azds list-up`
1. Kérje meg a csoport tagjai **jelölje ki a fejlesztői területet** , fejlesztése érdekében.
     * **Parancssor vagy a VS Code**:, tekintse meg a meglévő Azure fejlesztői tárolóhelyek való hozzáféréssel rendelkezik: `azds space list`. Jelölje be a fejlesztési szóközzel: `azds space select`.
     * **Visual Studio IDE**: nyisson meg egy projektet a Visual Studióban válassza **Azure fejlesztési tárolóhelyek** az indítási beállítások legördülő listából. A megnyíló párbeszédpanelen válassza ki egy meglévő fürthöz.

    ![A Visual Studio indítási beállítások legördülő menü](../media/get-started-netcore-visualstudio/LaunchSettings.png)

## <a name="next-steps"></a>További lépések

Lásd: [megismerheti a fejlesztési csapat](../team-development-nodejs.md) további információt.