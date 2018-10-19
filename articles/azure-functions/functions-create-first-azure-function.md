---
title: Az első függvény létrehozása az Azure portálon | Microsoft Docs
description: Ismerje meg, hogyan hozhatja létre az első Azure-függvényét kiszolgáló nélküli végrehajtáshoz az Azure Portalon.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: 96cf87b9-8db6-41a8-863a-abb828e3d06d
ms.service: azure-functions
ms.devlang: multiple
ms.topic: quickstart
ms.date: 03/28/2018
ms.author: glenga
ms.custom: mvc, devcenter, cc996988-fb4f-47
ms.openlocfilehash: a1a593dc037901f6661bc8c7268d835bc5a48c94
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900616"
---
# <a name="create-your-first-function-in-the-azure-portal"></a>Az első függvény létrehozása az Azure portálon

Az Azure Functions lehetővé teszi a kód [kiszolgáló nélküli](https://azure.microsoft.com/solutions/serverless/) környezetben történő végrehajtását anélkül, hogy először létre kellene hoznia egy virtuális gépet, vagy közzé kellene tennie egy webalkalmazást. Ebben a témakörben megismerheti, hogyan használhatja a Functions szolgáltatást egy „hello world” függvény létrehozására az Azure Portalon.

![Függvényalkalmazás létrehozása az Azure Portalon](./media/functions-create-first-azure-function/function-app-in-portal-editor.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> A C#-fejlesztők számára érdemes megfontolni, hogy [az első függvényt a Visual Studio 2017-ben](functions-create-your-first-function-visual-studio.md), és ne a portálon hozzák létre. 

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure Portalra a <http://portal.azure.com> webhelyen az Azure-fiókjával.

## <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

Rendelkeznie kell egy függvényalkalmazással a függvények végrehajtásának biztosításához. A függvényalkalmazás lehetővé teszi, hogy logikai egységbe csoportosítsa a függvényeket az erőforrások egyszerűbb felügyelete, telepítése és megosztása érdekében. 

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Ezután létrehozhat egy függvényt az új függvényalkalmazásban.

## <a name="create-function"></a>HTTP által aktivált függvény létrehozása

1. Bontsa ki az új függvényalkalmazást, majd kattintson a **Függvények** elem melletti **+** gombra, és válassza a **Portálba épített**, majd a **Folytatás** lehetőséget.

    ![Függvény rövid útmutatója – platform kiválasztása](./media/functions-create-first-azure-function/function-app-quickstart-choose-portal.png)

1. Válassza a **WebHook + API**, majd a **Létrehozás** lehetőséget.

    ![A függvények gyors létrehozása az Azure Portalon.](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)

A rendszer létrehoz egy függvényt egy HTTP által indított függvény nyelvspecifikus sablonjának használatával.

Mostantól egy HTTP-kérelem küldésével futtathatja az új függvényt.

## <a name="test-the-function"></a>A függvény tesztelése

1. Az új függvényben kattintson a **</> Függvény URL-címének beolvasása** elemre a jobb felső sarokban, válassza a **default (Function key)** (alapértelmezett (funkcióbillentyű)) lehetőséget, majd kattintson a **Másolás** gombra. 

    ![A függvény URL-címének másolása az Azure portálról](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

2. Illessze be a függvény URL-címét a böngésző címsorába. Az URL-cím végéhez adja hozzá a `&name=<yourname>` lekérdezési sztring értéket, majd nyomja le az `Enter` billentyűt a billentyűzeten a kérés végrehajtásához. Ekkor a függvény által visszaadott válasz jelenik meg a böngészőben.  

    Az alábbi példa a böngészőben visszaadott választ mutatja:

    ![A függvény által visszaadott válasz a böngészőben.](./media/functions-create-first-azure-function/function-app-browser-testing.png)

    A kérelem URL-címe alapértelmezés szerint tartalmazza a függvény HTTP protokollon keresztüli eléréséhez szükséges kulcsot.

3. A függvény futásakor a rendszer nyomkövetési adatok ír a naplókba. Az előző végrehajtás nyomkövetési adatainak megtekintéséhez térjen vissza a függvényhez a portálon, és kattintson a képernyő alján található nyílra a **Naplók** elem kibontásához.

   ![A függvények naplómegtekintője az Azure Portalon.](./media/functions-create-first-azure-function/function-view-logs.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>További lépések

Létrehozott egy függvényalkalmazást és egy HTTP által aktivált egyszerű függvényt.  

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

További információt az [Azure Functions HTTP-kötéseit](functions-bindings-http-webhook.md) ismertető cikkben talál.
