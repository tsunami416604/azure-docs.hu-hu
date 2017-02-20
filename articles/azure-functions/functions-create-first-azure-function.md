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
ms.date: 02/09/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: 7743bfb98552f7fa2334d8daa6a6f6935969f393
ms.openlocfilehash: d76630e0be4a021720d88e6d7e64cf2258f84266


---
# <a name="create-your-first-azure-function"></a>Az első Azure-függvény létrehozása
## <a name="overview"></a>Áttekintés
Az Azure Functions egy eseményvezérelt, számítási igények szerint működtethető eszköz, amely bővíti a meglévő Azure alkalmazásplatformot, lehetővé téve események által kiváltott kód megvalósítását más Azure-szolgáltatásokban, SaaS-termékekben, valamint a helyszíni rendszerekben. Az Azure Functions segítségével alkalmazásai igény szerint méretezhetők, és Ön csak a felhasznált erőforrásokért fizet. Az Azure Functions segítségével ütemezett és aktivált kódegységek hozhatók létre, különböző programnyelveken megvalósítva. További információk az Azure Functions szolgáltatásról: [Azure Functions Overview](functions-overview.md) (Az Azure Storage áttekintése).

Ez a témakör bemutatja, hogyan hozható létre az Azure Functions gyorsindítóval egy egyszerű „hello world” JavaScript-függvény, amelyet egy HTTP-eseményindító hív meg. Az ehhez szükséges, a portálon elvégzendő lépésekről egy rövid videót is megtekinthet.

## <a name="watch-the-video"></a>Videó megtekintése
A következő videó bemutatja, hogyan végezheti el az ebben az oktatóprogramban található alapszintű lépéseket. 

> [!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Create-your-first-Azure-Function-simple/player]
> 
> 

## <a name="create-a-function-from-the-quickstart"></a>Függvény létrehozása a gyorsindítóból
A függvények végrehajtásához szükséges gazdaszolgáltatást az Azure-ban egy függvényalkalmazás biztosítja. Kövesse az alábbi lépéseket egy függvényalkalmazás létrehozásához. A függvényalkalmazás alapértelmezett konfigurációval jön létre. A függvényalkalmazás tényleges létrehozására példát [az egyéb Azure Functions gyorsútmutatóban](functions-create-first-azure-function-azure-portal.md) talál.

Ahhoz, hogy létrehozhassa az első függvényét, egy aktív Azure-fiókkal kell rendelkeznie. Ha még nem rendelkezik Azure-fiókkal, [létrehozhat egy ingyenes fiókot](https://azure.microsoft.com/free/).

1. Lépjen az [Azure Functions portálra](https://functions.azure.com/signin), és jelentkezzen be az Azure-fiókjával.
2. Adjon meg egy egyedi **nevet** az új függvényalkalmazásnak, vagy fogadja el az automatikusan létrehozott nevet, majd válassza ki a kívánt **régiót**, és kattintson a **Create + get started** (Létrehozás és első lépések) gombra. Ügyeljen arra, hogy érvényes nevet adjon meg, amely csak betűket, számokat és kötőjelet tartalmazhat. Az aláhúzás (**_**) nem engedélyezett karakter.
3. Az **Első lépések** lapon kattintson a **Webhook + API** és a **JavaScript** elemre, majd kattintson a **Függvény létrehozása** parancsra. Létrejön egy új, előre meghatározott JavaScript-függvény. 
   
    ![](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)
4. (Választható lehetőség) A gyorsindító ezen pontján lehetősége van megtekinteni egy bemutatót a portál Azure Functions-szolgáltatásairól. A bemutató megtekintése vagy kihagyása után letesztelheti az új függvényt a HTTP-eseményindítóval.

## <a name="test-the-function"></a>A függvény tesztelése
[!INCLUDE [Functions quickstart test](../../includes/functions-quickstart-test.md)]

## <a name="next-steps"></a>Következő lépések
[!INCLUDE [Functions quickstart next steps](../../includes/functions-quickstart-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]




<!--HONumber=Feb17_HO2-->


