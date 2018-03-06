---
title: "GitHub-webhookok által meghívott függvények létrehozása az Azure-ban | Microsoft Docs"
description: "Használja az Azure Functions szolgáltatást olyan kiszolgáló nélküli függvények létrehozására, amelyeket GitHub-webhook hív meg."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
ms.assetid: 36ef34b8-3729-4940-86d2-cb8e176fcc06
ms.service: functions
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/31/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 98632c3276e7d9cccf29d05b903c2fac62a7d68e
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2018
---
# <a name="create-a-function-triggered-by-a-github-webhook"></a>GitHub-webhookok által meghívott függvények létrehozása

Ismerje meg, hogyan hozhat létre HTTP-webhookkérelem által meghívott függvényeket a GitHub hasznos adataival.

![GitHub-webhook által meghívott függvény az Azure portálon](./media/functions-create-github-webhook-triggered-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>Előfeltételek

+ Legalább egy projekttel rendelkező GitHub-fiók.
+ Azure-előfizetés. Ha még nincs előfizetése, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), mielőtt hozzákezd.

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]

## <a name="create-an-azure-function-app"></a>Azure-függvényalkalmazás létrehozása

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![A függvényalkalmazás elkészült.](./media/functions-create-first-azure-function/function-app-create-success.png)

Ezután létrehozhat egy függvényt az új függvényalkalmazásban.

<a name="create-function"></a>

## <a name="create-a-github-webhook-triggered-function"></a>GitHub-webhook által aktivált függvény létrehozása

1. Bontsa ki a függvényalkalmazást, és kattintson a **Függvények** elem melletti **+** gombra. Ha ez az első függvény a függvényalkalmazásban, jelölje ki az **Egyéni függvény** lehetőséget. Ez megjeleníti a függvénysablonok teljes készletét.

    ![Függvények gyors létrehozásának oldala az Azure Portalon](./media/functions-create-github-webhook-triggered-function/add-first-function.png)

2. A keresés mezőbe írja be a `github` kifejezést, majd válassza ki a kívánt nyelvet a GitHub-webhook eseményindító sablonjához. 

     ![A GitHub-webhook eseményindító sablonjának kiválasztása](./media/functions-create-github-webhook-triggered-function/functions-create-github-webhook-trigger.png) 

2. Adja meg a függvény **nevét**, majd válassza a **Létrehozás** elemet. 

     ![GitHub-webhook által meghívott függvény konfigurálása az Azure Portalon](./media/functions-create-github-webhook-triggered-function/functions-create-github-webhook-trigger-2.png) 

3. Az új függvényben kattintson a **</> Függvény URL-címének lekérése** elemre, majd másolja és mentse az értékeket. Járjon el ugyanígy a **</> GitHub-titok lekérése** esetében. Ezekre az értékekre a GitHub-webhook konfigurálásához lesz szükség.

    ![A függvénykód áttekintése](./media/functions-create-github-webhook-triggered-function/functions-copy-function-url-github-secret.png)

A következő lépésben egy webhookot hoz létre a GitHub-tárban.

## <a name="configure-the-webhook"></a>A webhook konfigurálása

1. A GitHubban lépjen a tulajdonában álló adattárra. Használhat bármely elágaztatott adattárat is. Ha egy tárház esetében elágazásra van szüksége, használja a következőt: <https://github.com/Azure-Samples/functions-quickstart>.

1. Kattintson a **Settings** (Beállítások), majd a **Webhooks** (Webhookok) és végül az **Add webhook** (Webhook hozzáadása) elemre.

    ![GitHub-webhook hozzáadása](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-2.png)

1. Használja a táblázatban megadott beállításokat, majd kattintson az **Add webhook** (Webhook hozzáadása) elemre.

    ![A webhook URL-címének és titkos kulcsának beállítása](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-3.png)

| Beállítás | Ajánlott érték | Leírás |
|---|---|---|
| **Hasznos adat URL-címe** | Másolt érték | Használja a **</> Függvény URL-címének lekérése** által visszaadott értéket. |
| **Tartalom típusa** | application/json | A függvény JSON hasznos adatot vár. |
| **Titkos kód**   | Másolt érték | Használja a **</> GitHub-titok lekérése** által visszaadott értéket. |
| Eseményindítók | Én szeretném kiválasztani az egyes eseményeket | Csak a problémákra vonatkozó megjegyzésekhez szeretnénk eseményindítókat használni.  |
| | Probléma megjegyzése |  |

Most a webhook arra van beállítva, hogy elindítsa a függvényt egy új, problémához fűzött megjegyzés hozzáadásakor.

## <a name="test-the-function"></a>A függvény tesztelése

1. A GitHub-tárban nyissa meg az **Issues** (Problémák) lapot egy új böngészőablakban.

1. Az új ablakban kattintson a **New Issue** (Új probléma) elemre, adjon meg egy címet, és kattintson a **Submit new issue** (Új probléma küldése) gombra.

1. A problémában írjon be egy megjegyzést, majd kattintson a **Comment** (Megjegyzés) elemre.

    ![Adja meg a GitHub-problémára vonatkozó megjegyzést.](./media/functions-create-github-webhook-triggered-function/functions-github-webhook-add-comment.png)

1. Lépjen vissza a portálra, és tekintse át a naplókat. A nyomkövetési bejegyzésnek kell megjelennie az új megjegyzés szövegével.

     ![Tekintse át a megjegyzések szövegét a naplókban.](./media/functions-create-github-webhook-triggered-function/function-app-view-logs.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>További lépések

Létrehozott egy függvényt, amely akkor aktiválódik, amikor kérelem érkezik egy GitHub-webhookból.

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

További információt a webhook-eseményindítókról az [Azure Functions – HTTP- és webhookkötések](functions-bindings-http-webhook.md) című témakörben talál.
