---
title: "GitHub-webhookok által meghívott függvények létrehozása az Azure-ban | Microsoft Docs"
description: "Használja az Azure Functions szolgáltatást olyan kiszolgáló nélküli függvények létrehozására, amelyeket GitHub-webhook hív meg."
services: azure-functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 36ef34b8-3729-4940-86d2-cb8e176fcc06
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/02/2017
ms.author: glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 34988ef05a27062ca109a1640e39695b52b8773f
ms.contentlocale: hu-hu
ms.lasthandoff: 05/10/2017


---
# <a name="create-a-function-triggered-by-a-github-webhook"></a>GitHub-webhookok által meghívott függvények létrehozása

Ismerje meg, hogyan hozhat létre HTTP-webhookkérelem által meghívott függvényeket a GitHub hasznos adataival. 

![GitHub-webhook által meghívott függvény az Azure portálon](./media/functions-create-github-webhook-triggered-function/function-app-in-portal-editor.png)

A témakörben foglalt lépések végrehajtása legfeljebb 5 percet vehet igénybe.

## <a name="prerequisites"></a>Előfeltételek 

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

Szüksége lesz egy legalább egy projektet tartalmazó GitHub-fiókra. Ha még nincs fiókja, [regisztrálhat egy ingyenes GitHub-fiókra](https://github.com/join).

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)] 

## <a name="create-function"></a>GitHub-webhook által aktivált függvény létrehozása

1. Bontsa ki a függvényalkalmazást, kattintson a **Függvények** elem melletti **+** gombra, majd kattintson a kívánt nyelvű **GitHubWebHook** sablonra. **Nevezze el a függvényt**, majd kattintson a **Létrehozás** elemre. 

2. Az új függvényben kattintson a **</> Függvény URL-címének lekérése** elemre, majd másolja és mentse az értékeket. Járjon el ugyanígy a **</> GitHub-titok lekérése** esetében. Ezekre az értékekre a GitHub-webhook konfigurálásához lesz szükség. 

    ![A függvénykód áttekintése](./media/functions-create-github-webhook-triggered-function/functions-copy-function-url-github-secret.png) 
         
A következő lépésben egy webhookot hoz létre a GitHub-tárban. 

## <a name="configure-the-webhook"></a>A webhook konfigurálása
1. A GitHubban lépjen a tulajdonában álló adattárra. Használhat bármely elágaztatott adattárat is. Ha elágazásra van szüksége egy tárház esetében, tekintse át a következőt: <https://github.com/Azure-Samples/functions-quickstart>. 
 
2. Kattintson a **Settings** (Beállítások), majd a **Webhooks** (Webhookok) és végül az **Add webhook** (Webhook hozzáadása) elemre.
   
    ![GitHub-webhook hozzáadása](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-2.png)

3. Használja a táblázatban megadott beállításokat, majd kattintson az **Add webhook** (Webhook hozzáadása) elemre.
 
    ![A webhook URL-címének és titkos kulcsának beállítása](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-3.png)

    | Beállítás      |  Ajánlott érték   | Leírás                              |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Hasznos adat URL-címe** | Másolt érték | Használja a **</> Függvény URL-címének lekérése** által visszaadott értéket. |
    | **Titkos kód**   | Másolt érték | Használja a **</> GitHub-titok lekérése** által visszaadott értéket. |
    | **Tartalom típusa** | application/json | A függvény JSON hasznos adatot vár. |
    | Eseményindítók | Én szeretném kiválasztani az egyes eseményeket | Csak a problémákra vonatkozó megjegyzésekhez szeretnénk eseményindítókat használni.  |
    |                | Probléma megjegyzése                    |  |

Most a webhook arra van beállítva, hogy elindítsa a függvényt egy új, problémához fűzött megjegyzés hozzáadásakor. 

## <a name="test-the-function"></a>A függvény tesztelése
1. A GitHub-tárban nyissa meg az **Issues** (Problémák) lapot egy új böngészőablakban.

2. Az új ablakban kattintson a **New Issue** (Új probléma) elemre, adjon meg egy címet, és kattintson a **Submit new issue** (Új probléma küldése) gombra. 

2. A problémában írjon be egy megjegyzést, majd kattintson a **Comment** (Megjegyzés) elemre. 

    ![Adja meg a GitHub-problémára vonatkozó megjegyzést.](./media/functions-create-github-webhook-triggered-function/functions-github-webhook-add-comment.png) 

3. Lépjen vissza a portálra, és tekintse át a naplókat. A nyomkövetési bejegyzésnek kell megjelennie az új megjegyzés szövegével. 
    
     ![Tekintse át a megjegyzések szövegét a naplókban.](./media/functions-create-github-webhook-triggered-function/function-app-view-logs.png)
 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Létrehozott egy függvényt, amely akkor fut, amikor kérelem érkezik egy GitHub-webhookból. 
[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)] További információ a webhook eseményindítókról: [Azure Functions – HTTP- és webhookkötések](functions-bindings-http-webhook.md). 




