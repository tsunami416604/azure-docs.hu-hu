---
title: Az első függvény létrehozása az Azure portálon
description: Ismerje meg, hogyan hozhatja létre az első Azure-függvényét kiszolgáló nélküli végrehajtáshoz az Azure Portalon.
ms.assetid: 96cf87b9-8db6-41a8-863a-abb828e3d06d
ms.topic: quickstart
ms.date: 03/26/2020
ms.custom: mvc, devcenter, cc996988-fb4f-47
ms.openlocfilehash: 417ad96dc3dea25e322dbdb4d81c034a9b9c1e80
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656896"
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

1. A **Funkciók** ablak bal oldali menüjében válassza a **Funkciók**lehetőséget, majd válassza a felső menü **Hozzáadás** parancsát. 
 
1. Az **Új függvény ablakban** válassza a **Http trigger**lehetőséget.

    ![HTTP-eseményindító funkció kiválasztása](./media/functions-create-first-azure-function/function-app-select-http-trigger.png)

1. Az **Új függvény ablakban** fogadja el az **Új függvény**alapértelmezett nevét, vagy adjon meg egy új nevet. 

1. Válassza a **Névtelen** lehetőséget az **Engedélyezési szint** legördülő listában, majd válassza a Függvény **létrehozása lehetőséget.**

    Az Azure létrehozza a HTTP-trigger funkciót. Mostantól egy HTTP-kérelem küldésével futtathatja az új függvényt.

## <a name="test-the-function"></a>A függvény tesztelése

1. Az új HTTP trigger funkcióban válassza a bal oldali menü **Kód + Tesztelés** parancsát, majd a felső menü **Benget függvény URL-címének bekerülése** parancsát.

    ![Válassza a Függvény URL-címének bekerülése lehetőséget](./media/functions-create-first-azure-function/function-app-select-get-function-url.png)

1. A **Függvény URL-címének bemásolása** párbeszédpanelen válassza az **alapértelmezett** lehetőséget a legördülő listából, majd kattintson a Másolás a **vágólapra** ikonra. 

    ![A függvény URL-címének másolása az Azure portálról](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

1. Illessze be a függvény URL-címét a böngésző címsorába. Adja hozzá a `?name=<your_name>` lekérdezési karakterlánc értékét az URL-cím végéhez, és nyomja le az Enter billentyűt a kérelem futtatásához. 

    Az alábbi példa a böngészőben visszaadott választ mutatja:

    ![A függvény által visszaadott válasz a böngészőben.](./media/functions-create-first-azure-function/function-app-browser-testing.png)

    A kérelem URL-címe alapértelmezés szerint tartalmazza a függvény HTTP protokollon keresztüli eléréséhez szükséges kulcsot.

1. A függvény futásakor a rendszer nyomkövetési adatok ír a naplókba. A nyomkövetési kimenet megtekintéséhez térjen vissza a **portál Kód + Teszt** lapjára, és **bontsa** ki a lap alján található Naplók nyilat.

   ![A függvények naplómegtekintője az Azure Portalon.](./media/functions-create-first-azure-function/function-view-logs.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>További lépések

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

