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
ms.date: 04/18/2017
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: d4354546f3342d65353a86a4cec7d02547ab92e7
ms.lasthandoff: 04/22/2017


---
# <a name="create-a-function-triggered-by-a-github-webhook"></a>GitHub-webhookok által meghívott függvények létrehozása

Itt megismerkedhet a GitHub-webhookok által meghívott függvények létrehozásával. 

![Függvényalkalmazás létrehozása az Azure Portalon](./media/functions-create-github-webhook-triggered-function/function-app-in-portal-editor.png)

A témakörhöz szükség lesz [Az első Azure-függvény létrehozása](functions-create-first-azure-function.md) témakörben létrehozott erőforrásokra.

Szüksége lesz továbbá egy GitHub-fiókra. Ha még nincs fiókja, [regisztrálhat egy ingyenes GitHub-fiókra](https://github.com/join). 

A témakörben foglalt lépések végrehajtása legfeljebb 5 percet vehet igénybe.

## <a name="find-your-function-app"></a>A függvényalkalmazás megkeresése    

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/). 

2. A portál tetején a keresősávba írja be a függvényalkalmazás nevét, majd válassza ki a listáról.

## <a name="create-function"></a>GitHub-webhook által aktivált függvény létrehozása

1. A függvényalkalmazásban kattintson a **Függvények** mellett a **+** gombra, majd a **GitHubWebHook** sablonra a kívánt nyelven, és végül a **Létrehozás** gombra.
   
    ![Hozzon létre egy GitHub-webhook által aktivált függvényt az Azure Portalon.](./media/functions-create-github-webhook-triggered-function/functions-create-github-webhook-trigger.png) 

2. Kattintson a **</> Függvény URL-címének lekérése** elemre, majd másolja és mentse az értékeket. Járjon el ugyanígy a **</> GitHub-titok lekérése** esetében. Ezekre az értékekre a GitHub-webhook konfigurálásához lesz szükség. 

    ![A függvénykód áttekintése](./media/functions-create-github-webhook-triggered-function/functions-copy-function-url-github-secret.png) 
         
A következő lépésben egy webhookot hoz létre a GitHub-tárban. 

## <a name="configure-the-webhook"></a>A webhook konfigurálása
1. A GitHubban lépjen a tulajdonában álló adattárra. Használhat bármely elágaztatott adattárat is.
 
2. Kattintson a **Settings** (Beállítások), majd a **Webhooks** (Webhookok) és végül az **Add webhook** (Webhook hozzáadása) elemre.
   
    ![GitHub-webhook hozzáadása](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-2.png)

3. Illessze be a függvény URL-jét és titkos kulcsát a **Payload URL** (Hasznos adat URL) és a **Secret** (Titkos kulcs) mezőkbe, majd a **Content type** (Tartalom típusa) elemnél adja meg az **application/json** (alkalmazás/json) értéket.

4. Kattintson a **Let me select individual events** (Egyes események kiválasztása) gombra, válassza az **Issue comment** (Problémához fűzött megjegyzés) lehetőséget, majd kattintson az **Add webhook** (Webhook hozzáadása) parancsra.
   
    ![A webhook URL-címének és titkos kulcsának beállítása](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-3.png)

Most a webhook arra van beállítva, hogy elindítsa a függvényt egy új, problémához fűzött megjegyzés hozzáadásakor. 

## <a name="test-the-function"></a>A függvény tesztelése
1. A GitHub-tárban nyissa meg az **Issues** (Problémák) lapot egy új böngészőablakban.

2. Az új ablakban kattintson a **New Issue** (Új probléma) elemre, adjon meg egy címet, majd kattintson a **Submit new issue** (Új probléma küldése) gombra. 

2. A problémában írjon be egy megjegyzést, majd kattintson a **Comment** (Megjegyzés) elemre. 

3. A másik GitHub-ablakban kattintson az új webhook melletti **Edit** (Szerkesztés) elemre, görgessen lefelé a **Recent Deliveries** (Legutóbbi kézbesítések) pontig, és itt ellenőrizze, hogy a függvény dolgozott-e fel webhook-kérelmet. 
 
    ![A webhook URL-címének és titkos kulcsának beállítása](./media/functions-create-github-webhook-triggered-function/functions-github-webhook-triggered.png)

   A függvény válaszának tartalmaznia kell a következőt: `New GitHub comment: <Your issue comment text>`.

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]


