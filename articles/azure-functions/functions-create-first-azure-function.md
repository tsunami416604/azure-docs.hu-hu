---
title: "Az első Azure-függvény létrehozása | Microsoft Docs"
description: "Első Azure-függvényét, egy kiszolgáló nélküli alkalmazást, kevesebb mint két perc alatt építhet fel."
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 4a1669e7-233e-4ea2-9b83-b8624f2dbe59
ms.service: functions
ms.devlang: multiple
ms.topic: hero-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/14/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: a797910e286cd2aacf5a8aa6c509e2b0f5f60276
ms.lasthandoff: 03/17/2017


---
# <a name="create-your-first-azure-function"></a>Az első Azure-függvény létrehozása

Ez a témakör bemutatja, hogyan hozható létre az Azure Functions gyorsindítóval egy egyszerű „hello world” függvény, amelyet egy HTTP-kérelem hív meg. További információk az Azure Functions szolgáltatásról: [Azure Functions Overview](functions-overview.md) (Az Azure Storage áttekintése).

A kezdéshez Azure-fiókkal kell rendelkeznie. [Ingyenes fiókot](https://azure.microsoft.com/free/) is létrehozhat. Az [Azure Functions kipróbáláshoz](https://azure.microsoft.com/try/app-service/functions/) nem kell regisztrálnia az Azure-ba.

## <a name="create-a-function-from-the-portal-quickstart"></a>Függvény létrehozása a portál gyorsindítójából

1. Lépjen az [Azure Functions portálra](https://functions.azure.com/signin), és jelentkezzen be az Azure-fiókjával. 
 
2. Adjon meg egy egyedi **nevet** az új függvényalkalmazásnak, vagy fogadja el az automatikusan létrehozott nevet, majd válassza ki a kívánt **régiót**, és kattintson a **Create + get started** (Létrehozás és első lépések) gombra. Az érvényes nevek csak betűket, számokat és kötőjelet tartalmazhatnak. Az aláhúzás (**_**) nem engedélyezett karakter.

3. A **Gyors üzembe helyezés** lapon kattintson a **Webhook + API** elemre, válasszon egy nyelvet a függvényhez, majd kattintson a **Függvény létrehozása** parancsra. Létrejön egy új, előre meghatározott függvény a választott nyelven. 
   
    ![](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)

4. (Választható lehetőség) A gyorsindító ezen pontján lehetősége van megtekinteni egy bemutatót a portál Azure Functions-szolgáltatásairól. A bemutató megtekintése vagy kihagyása után letesztelheti az új függvényt egy HTTP-kérelem küldésével.

## <a name="test-the-function"></a>A függvény tesztelése
[!INCLUDE [Functions quickstart test](../../includes/functions-quickstart-test.md)]

## <a name="watch-the-video"></a>Videó megtekintése
A következő videó bemutatja, hogyan végezheti el az ebben az oktatóprogramban található alapszintű lépéseket. 

> [!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Create-your-first-Azure-Function-simple/player]
> 


## <a name="next-steps"></a>Következő lépések
[!INCLUDE [Functions quickstart next steps](../../includes/functions-quickstart-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]


