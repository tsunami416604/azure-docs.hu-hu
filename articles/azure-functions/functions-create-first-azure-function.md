---
title: Az első függvény létrehozása az Azure portálon
description: Ismerje meg, hogyan hozhatja létre az első Azure-függvényét kiszolgáló nélküli végrehajtáshoz az Azure Portalon.
ms.assetid: 96cf87b9-8db6-41a8-863a-abb828e3d06d
ms.topic: quickstart
ms.date: 03/06/2020
ms.custom: mvc, devcenter, cc996988-fb4f-47
ms.openlocfilehash: e7bb5e7b387c3ab1140a3fe475911bd0e428e2a5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80057047"
---
# <a name="create-your-first-function-in-the-azure-portal"></a>Az első függvény létrehozása az Azure portálon

Az Azure Functions lehetővé teszi a kód futtatását kiszolgáló nélküli környezetben anélkül, hogy először virtuális gépet (VM) vagy közzé egy webalkalmazást. Ebben a cikkben megtudhatja, hogyan használhatja az Azure Functions egy "hello world" HTTP-aktivált függvény az Azure Portalon.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Ha C# fejlesztő, fontolja meg [az első függvény létrehozását a Visual Studio 2019-ben](functions-create-your-first-function-visual-studio.md) a portál helyett. 

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókjával.

## <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

Rendelkeznie kell egy függvényalkalmazással a függvények végrehajtásának biztosításához. A függvényalkalmazás lehetővé teszi a függvények logikai egységként történő csoportosítását az erőforrások egyszerűbb kezelése, üzembe helyezése, méretezése és megosztása érdekében.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Ezután hozzon létre egy függvényt az új függvényalkalmazásban.

## <a name="create-an-http-triggered-function"></a><a name="create-function"></a>HTTP által aktivált függvény létrehozása

1. Bontsa ki az **+** új függvényalkalmazást, kattintson a **Funkciók**gomb melletti gombra, válassza **a Portálon**lehetőséget, majd kattintson a **Folytatás gombra.**

    ![Gyorsútmutató a platform kiválasztásához.](./media/functions-create-first-azure-function/function-app-quickstart-choose-portal.png)

1. Válassza **a WebHook + API**lehetőséget, majd a Create **(Létrehozás) lehetőséget.**

    ![A függvények gyors létrehozása az Azure Portalon.](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)

   A rendszer létrehoz egy függvényt egy HTTP által indított függvény nyelvspecifikus sablonjának használatával.

Mostantól egy HTTP-kérelem küldésével futtathatja az új függvényt.

## <a name="test-the-function"></a>A függvény tesztelése

1. Az új függvényben válassza **a</> A függvény URL-címének bekerülése** lehetőséget a jobb felső sarokban. 

1. A **Függvény URL-címének bemásolása** párbeszédpanelen válassza az **alapértelmezett (Funkcióbillentyű)** elemet a legördülő listából, majd válassza a **Másolás**lehetőséget. 

    ![A függvény URL-címének másolása az Azure portálról](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

1. Illessze be a függvény URL-címét a böngésző címsorába. Adja hozzá a `&name=<your_name>` lekérdezési karakterlánc értékét az URL-cím végéhez, és nyomja le az Enter billentyűt a kérelem futtatásához. 

    Az alábbi példa a böngészőben visszaadott választ mutatja:

    ![A függvény által visszaadott válasz a böngészőben.](./media/functions-create-first-azure-function/function-app-browser-testing.png)

    A kérelem URL-címe alapértelmezés szerint tartalmazza a függvény HTTP protokollon keresztüli eléréséhez szükséges kulcsot.

1. A függvény futásakor a rendszer nyomkövetési adatok ír a naplókba. Az előző végrehajtás nyomkövetési kimenetének megtekintéséhez térjen vissza a függvényhez a portálon, és válassza ki a képernyő alján lévő nyilat a **Naplók kibontásához.**

   ![A függvények naplómegtekintője az Azure Portalon.](./media/functions-create-first-azure-function/function-view-logs.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>További lépések

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

