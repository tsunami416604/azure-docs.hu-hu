---
title: Azure Functions folyamatos üzembe helyezése | Microsoft Docs
description: A függvények közzétételéhez használja a Azure App Service folyamatos üzembe helyezési funkcióit.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/25/2016
ms.author: glenga
ms.openlocfilehash: fb3cd885c0a16b3dc3a79150043b25cb271040bd
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70097098"
---
# <a name="continuous-deployment-for-azure-functions"></a>Azure Functions – folyamatos üzembe helyezés

A Azure Functions segítségével folyamatosan üzembe helyezheti a kódot a [verziókövetés integrációjának](functions-deployment-technologies.md#source-control)használatával. A verziókövetés integrációja lehetővé teszi egy olyan munkafolyamat használatát, amelyben a kód frissítése elindítja az Azure-ba történő üzembe helyezést. Ha még nem Azure Functions, az első lépésekhez tekintse át az [Azure functions áttekintését](functions-overview.md).

A folyamatos üzembe helyezés jó választás olyan projektekhez, ahol több és gyakori hozzájárulásokat is integrálhat. Ha folyamatos üzembe helyezést használ, a kód egyetlen forrását őrzi meg, amely lehetővé teszi a csapatok számára, hogy egyszerűen működjenek együtt. A folyamatos üzembe helyezést Azure Functions a következő forráskód-helyekről állíthatja be:

* [Azure-repók](https://azure.microsoft.com/services/devops/repos/)
* [GitHubon](https://github.com)
* [Bitbucket](https://bitbucket.org/)

Az Azure-beli függvények üzembe helyezési egysége a Function alkalmazás. Egy Function alkalmazás összes funkciója egyszerre van üzembe helyezve. Miután engedélyezte a folyamatos üzembe helyezést, a Azure Portalhoz való hozzáférés *csak olvashatóként* van konfigurálva, mert az igazság forrása máshol van beállítva.

## <a name="requirements-for-continuous-deployment"></a>A folyamatos üzembe helyezésre vonatkozó követelmények

A sikeres üzembe helyezéshez a címtár struktúrájának kompatibilisnek kell lennie a Azure Functions várt alapszintű mappastruktúrát.

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

## <a name="credentials"></a>Folyamatos üzembe helyezés beállítása

Egy meglévő Function alkalmazás folyamatos üzembe helyezésének konfigurálásához végezze el a következő lépéseket. A lépések bemutatják a GitHub-adattárral való integrációt, de a hasonló lépések érvényesek az Azure Repos vagy más forráskód-Tárházak esetében.

1. A [Azure Portal](https://portal.azure.com)a Function alkalmazásban válassza a **platform szolgáltatások** > **központi telepítési központ**elemet.

    ![Központi telepítési központ megnyitása](./media/functions-continuous-deployment/platform-features.png)

2. A **központi telepítési központban**válassza a **GitHub**lehetőséget, majd válassza az **Engedélyezés**lehetőséget. Ha már engedélyezte a GitHubot, válassza a **Folytatás**lehetőséget. 

    ![Azure App Service központi telepítési központ](./media/functions-continuous-deployment/github.png)

3. A GitHubon kattintson a **AzureAppService engedélyezése** gombra. 

    ![Engedélyezés Azure App Service](./media/functions-continuous-deployment/authorize.png)
    
    A Azure Portal **központi telepítési központjában** válassza a **Folytatás**lehetőséget.

4. Válasszon egyet a következő Build-szolgáltatók közül:

    * **App Service Build szolgáltatás**: A legjobb, ha nincs szüksége buildre, vagy ha általános buildre van szüksége.
    * **Azure-folyamatok (előzetes verzió)** : A legjobb, ha nagyobb mértékű vezérlésre van szüksége a Build felett. Ez a szolgáltató jelenleg előzetes verzióban érhető el.

    ![Build szolgáltató kiválasztása](./media/functions-continuous-deployment/build.png)

5. Konfigurálja a megadott verziókövetés-beállításra vonatkozó adatokat. A GitHub esetében meg kell adnia vagy ki kell választania a **szervezet**, a **tárház**és az **ág**értékeit. Az értékek a kód helyétől függenek. Ezután válassza a **Folytatás**lehetőséget.

    ![GitHub konfigurálása](./media/functions-continuous-deployment/github-specifics.png)

6. Tekintse át az összes adatot, majd a **Befejezés** gombra kattintva fejezze be a telepítési konfigurációt.

    ![Összegzés](./media/functions-continuous-deployment/summary.png)

A folyamat befejezésekor a rendszer a megadott forrásból származó összes kódot telepíti az alkalmazásba. Ezen a ponton a központi telepítési forrás változásai elindítják ezeket a módosításokat az Azure-beli Function alkalmazásban.

## <a name="deployment-scenarios"></a>Üzembe helyezési forgatókönyvek

<a name="existing"></a>

### <a name="move-existing-functions-to-continuous-deployment"></a>Meglévő függvények áthelyezése a folyamatos üzembe helyezéshez

Ha már írt függvényeket a Azure Portalban [](https://portal.azure.com) , és szeretné letölteni az alkalmazás tartalmát, mielőtt a folyamatos üzembe helyezésre vált, lépjen a Function alkalmazás **Áttekintés** lapjára. Válassza az **alkalmazás tartalmának letöltése** gombot.

![Alkalmazás tartalmának letöltése](./media/functions-continuous-deployment/download.png)

> [!NOTE]
> A folyamatos integráció konfigurálása után már nem szerkesztheti a forrásfájlokat a functions portálon.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Azure Functions – ajánlott eljárások](functions-best-practices.md)
