---
title: "Az első függvény létrehozása az Azure portálon | Microsoft Docs"
description: "Ismerje meg, hogyan hozhatja létre az első Azure-függvényét kiszolgáló nélküli végrehajtáshoz az Azure Portalon."
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 96cf87b9-8db6-41a8-863a-abb828e3d06d
ms.service: functions
ms.devlang: multiple
ms.topic: hero-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 06/08/2017
ms.author: glenga
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: f00ca3b8a35c0c49277457bd42fe8a314520d5a5
ms.contentlocale: hu-hu
ms.lasthandoff: 06/09/2017


---
<a id="create-your-first-function-in-the-azure-portal" class="xliff"></a>

# Az első függvény létrehozása az Azure portálon

Az Azure Functions lehetővé teszi a kód végrehajtását kiszolgáló nélküli környezetben anélkül, hogy először létre kellene hoznia egy virtuális gépet vagy közzé kellene tennie egy webalkalmazást. Ebben a témakörben megismerheti, hogyan használhatja a Functions szolgáltatást egy „hello world” függvény létrehozására az Azure Portalon.

![Függvényalkalmazás létrehozása az Azure Portalon](./media/functions-create-first-azure-function/function-app-in-portal-editor.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

<a id="log-in-to-azure" class="xliff"></a>

## Jelentkezzen be az Azure-ba

Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

<a id="create-a-function-app" class="xliff"></a>

## Függvényalkalmazás létrehozása

Rendelkeznie kell egy függvényalkalmazással a függvények végrehajtásának biztosításához. A függvényalkalmazás lehetővé teszi, hogy logikai egységbe csoportosítsa a függvényeket az erőforrások egyszerűbb felügyelete, telepítése és megosztása érdekében. 

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![A függvényalkalmazás elkészült.](./media/functions-create-first-azure-function/function-app-create-success.png)

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]

Ezután létrehozhat egy függvényt az új függvényalkalmazásban.

## <a name="create-function"></a>HTTP által aktivált függvény létrehozása

1. Bontsa ki az új függvényalkalmazást, majd kattintson a **Függvények** elem melletti **+** gombra.

2.  A **Gyors kezdés** lapon kattintson a **WebHook + API** elemre, válassza ki a függvény nyelvét, és kattintson a **Függvény létrehozása** elemre. 
   
    ![A függvények gyors létrehozása az Azure Portalon.](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)

A rendszer létrehoz egy függvényt a választott nyelven a HTTP által indított függvények sablonjának használatával. Az új függvényt egy HTTP-kérelem küldésével futtathatja.

<a id="test-the-function" class="xliff"></a>

## A függvény tesztelése

1. Az új függvényben kattintson a **< /> Függvény URL-címének beolvasása** elemre, válassza a **default (Function key)** (alapértelmezett (funkcióbillentyű)) lehetőséget, majd kattintson a **Másolás** gombra. 

    ![A függvény URL-címének másolása az Azure portálról](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

2. Illessze be a HTTP-kérelem URL-címét a böngésző címsorába. Az URL-címhez fűzze hozzá a `&name=<yourname>` lekérdezési karakterláncot, és hajtsa végre a kérelmet. Az alábbiakban látható a böngészőben a GET kérelemre a függvény által visszaadott válasz:

    ![A függvény által visszaadott válasz a böngészőben.](./media/functions-create-first-azure-function/function-app-browser-testing.png)

    A kérelem URL-címe alapértelmezés szerint tartalmazza a függvény HTTP protokollon keresztüli eléréséhez szükséges kulcsot.   

<a id="view-the-function-logs" class="xliff"></a>

## A függvény naplóinak megtekintése 

A függvény futásakor a rendszer nyomkövetési adatok ír a naplókba. Az előző végrehajtás nyomkövetési adatainak megtekintéséhez térjen vissza a függvényhez a portálon, és a **Naplók** elem kibontásához kattintson a képernyő alján található felfelé mutató nyílra. 

![A függvények naplómegtekintője az Azure Portalon.](./media/functions-create-first-azure-function/function-view-logs.png)

<a id="clean-up-resources" class="xliff"></a>

## Az erőforrások eltávolítása

[!INCLUDE [Clean up resources](../../includes/functions-quickstart-cleanup.md)]

<a id="next-steps" class="xliff"></a>

## Következő lépések

Létrehozott egy függvényalkalmazást és egy HTTP által aktivált egyszerű függvényt.  

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

További információ: [Azure Functions – HTTP- és webhookkötések](functions-bindings-http-webhook.md).




