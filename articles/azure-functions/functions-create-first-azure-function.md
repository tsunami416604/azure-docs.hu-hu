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
ms.date: 11/14/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: ae5837b4fce52aad4c8b39434c27c450aafc1310
ms.openlocfilehash: 6f42f79abed80df12148463e25935893a4bbdcde


---
# <a name="create-your-first-azure-function"></a>Az első Azure-függvény létrehozása
## <a name="overview"></a>Áttekintés
Az Azure Functions egy eseményvezérelt, számítási igények szerint működtethető eszköz, amely bővíti a meglévő Azure alkalmazásplatformot, lehetővé téve események által kiváltott kód megvalósítását más Azure-szolgáltatásokban, SaaS-termékekben, valamint a helyszíni rendszerekben. Az Azure Functions segítségével alkalmazásai igény szerint méretezhetők, és Ön csak a felhasznált erőforrásokért fizet. Az Azure Functions segítségével ütemezett és aktivált kódegységek hozhatók létre, különböző programnyelveken megvalósítva. További információk az Azure Functions szolgáltatásról: [Azure Functions Overview](functions-overview.md) (Az Azure Storage áttekintése).

Ez a témakör bemutatja, hogyan hozható létre az Azure Functions gyorsindítóval egy egyszerű „hello world” Node.js-függvény, amelyet egy HTTP-eseményindító hív meg. Az ehhez szükséges, a portálon elvégzendő lépésekről egy rövid videót is megtekinthet.

## <a name="watch-the-video"></a>Videó megtekintése
A következő videó bemutatja, hogyan végezheti el az ebben az oktatóprogramban található alapszintű lépéseket. 

> [!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Create-your-first-Azure-Function-simple/player]
> 
> 

## <a name="create-a-function-from-the-quickstart"></a>Függvény létrehozása a gyorsindítóból
A függvények végrehajtásához szükséges gazdaszolgáltatást az Azure-ban egy függvényalkalmazás biztosítja. Kövesse az alábbi lépéseket egy függvényalkalmazás létrehozásához. A függvényalkalmazás alapértelmezett konfigurációval jön létre. A függvényalkalmazás tényleges létrehozására példát [az egyéb Azure Functions gyorsútmutatóban](functions-create-first-azure-function-azure-portal.md) talál.

Ahhoz, hogy létrehozhassa az első függvényét, egy aktív Azure-fiókkal kell rendelkeznie. Ha még nem rendelkezik Azure-fiókkal, [létrehozhat egy ingyenes fiókot](https://azure.microsoft.com/free/).

1. Lépjen az [Azure Functions portálra](https://functions.azure.com/signin), és jelentkezzen be az Azure-fiókjával.
2. Adjon meg egy egyedi **nevet** az új függvényalkalmazásnak, vagy fogadja el az automatikusan létrehozott nevet, majd válassza ki a kívánt **régiót**, és kattintson a **Create + get started** (Létrehozás és első lépések) gombra. 
3. Az **Első lépések** lapon kattintson a **Webhook + API** és a **JavaScript** elemre, majd kattintson a **Függvény létrehozása** parancsra. Létrejön egy új, előre meghatározott Node.js-függvény. 
   
    ![](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)
4. (Választható lehetőség) A gyorsindító ezen pontján lehetősége van megtekinteni egy bemutatót a portál Azure Functions-szolgáltatásairól. A bemutató megtekintése vagy kihagyása után letesztelheti az új függvényt a HTTP-eseményindítóval.

## <a name="test-the-function"></a>A függvény tesztelése
Mivel az Azure Functions gyorsindítók működő kódokat tartalmaznak, az új függvények azonnal tesztelhetők.

1. A **Develop** (Fejlesztés) lapon tekintse át a **Code** (Kód) ablakot, és figyeljen arra, hogy ez a Node.js-kód egy olyan HTTP-kérést vár, amelyben a *name* (név) érték vagy az üzenet törzsében, vagy egy lekérdezési karakterláncban van továbbítva. Ha a függvény fut, a rendszer ezt az értéket a válaszüzenetben adja vissza.
   
2. Kattintson a **Test (Tesztelés)** elemre a függvény beépített HTTP-tesztkérési panelének megjelenítéséhez.
 
    ![](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

2. A **Request body** (Kéréstörzs) szövegdobozban, módosítsa a *name* (név) tulajdonságot a saját nevére, és kattintson a **Run** (Futtatás) parancsra. Látni fogja, ahogy egy próba HTTP-kérés aktiválja a végrehajtást, információ íródik a streamnaplókba, és a „hello” válasz megjelenik a **Kimenet** mezőben.
 
3. Ugyanannak a függvénynek egy másik böngészőablakból vagy lapról történő aktiválásához másolja ki a **Function URL** (Függvény URL) értéket a **Develop** (Fejlesztés) lapról, és illessze be egy böngészőcímsorba. Az URL-címhez fűzze hozzá a `&name=yourname` karakterláncértéket, és nyomja le az Enter billentyűt. Ekkor a rendszer beírója a naplókba ugyanazt az információt, és a böngésző ugyanúgy megjeleníti a „hello” választ, mint az előző esetben.

## <a name="next-steps"></a>Következő lépések
Ez a gyorsindító egy egyszerű módot mutat be egy alapszintű, HTTP-eseményindítóval aktivált függvény végrehajtására. Az Azure Functions alkalmazásokban való használatáról a következő témakörökben talál további információt:

* [Azure Functions – ajánlott eljárások](functions-best-practices.md)
* [Az Azure Functions fejlesztői segédanyagai](functions-reference.md)  
  Programozói segédanyagok függvények kódolásához, valamint eseményindítók és kötések meghatározásához.
* [Az Azure Functions tesztelése](functions-test-a-function.md)  
  Különböző függvénytesztelési eszközöket és technikákat ír le.
* [Az Azure Functions méretezése](functions-scale.md)  
  Az Azure Functions szolgáltatáshoz elérhető szolgáltatáscsomagokat ismerteti, köztük a Használatalapú futtatási csomagot, és segít a megfelelő csomag kiválasztásában. 
* [Az Azure App Service-ről](../app-service/app-service-value-prop-what-is.md)  
  Az Azure Functions az Azure App Service platform használatával biztosítja az olyan alapvető funkciókat, mint az üzembe helyezések, a környezeti változók és a diagnosztika. 

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]




<!--HONumber=Nov16_HO4-->


