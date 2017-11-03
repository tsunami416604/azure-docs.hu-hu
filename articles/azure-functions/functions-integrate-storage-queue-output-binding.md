---
title: "Üzenetek hozzáadása az Azure Storage üzenetsorába függvények használatával |} Microsoft Docs"
description: "Használja az Azure Functions szolgáltatást olyan kiszolgáló nélküli függvények létrehozására, amelyeket az Azure Storage üzenetsorába elküldött üzenetek hívnak meg."
services: azure-functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
ms.assetid: 0b609bc0-c264-4092-8e3e-0784dcc23b5d
ms.service: functions
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/19/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 822879861ee8189cdd413f0061f26fb91819d88d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="add-messages-to-an-azure-storage-queue-using-functions"></a>Üzenetek hozzáadása az Azure Storage üzenetsorába a Functions szolgáltatás használatával

Az Azure Functions bemeneti és kimeneti kötései deklaratív módszert biztosítanak a külső szolgáltatások adataihoz a függvényből történő csatlakozásra. Ebben a témakörben megtudhatja, hogyan módosíthat egy meglévő függvényt egy kimeneti kötés hozzáadásával, amely üzeneteket küld az Azure üzenetsor-tárolójába.  

![Tekintse meg a naplókban található üzeneteket.](./media/functions-integrate-storage-queue-output-binding/functions-integrate-storage-binding-in-portal.png)

## <a name="prerequisites"></a>Előfeltételek 

[!INCLUDE [Previous topics](../../includes/functions-quickstart-previous-topics.md)]

* Telepítse a [Microsoft Azure Storage Explorert](http://storageexplorer.com/).

## <a name="add-binding"></a>Kimeneti kötés hozzáadása
 
1. Bontsa ki a függvényalkalmazást és a függvényt.

2. Válassza ki **integráció** és **+ új kimeneti**, majd válassza **Azure Queue storage** válassza **kiválasztása**.
    
    ![Vegye fel egy üzenetsor-tároló kimeneti kötését egy függvénybe az Azure Portalon.](./media/functions-integrate-storage-queue-output-binding/function-add-queue-storage-output-binding.png)

3. Használja a táblázatban megadott beállításokat: 

    ![Vegye fel egy üzenetsor-tároló kimeneti kötését egy függvénybe az Azure Portalon.](./media/functions-integrate-storage-queue-output-binding/function-add-queue-storage-output-binding-2.png)

    | Beállítás      |  Ajánlott érték   | Leírás                              |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Üzenetsor neve**   | myqueue-items    | A tárfiókhoz csatlakoztatni kívánt üzenetsor neve. |
    | **Tárfiók kapcsolata** | AzureWebJobStorage | Választhatja a függvényalkalmazás által már használt tárfiókkapcsolatot, vagy létrehozhat egy újat.  |
    | **Üzenet-paraméter neve** | outputQueueItem | A kimeneti kötés paraméterének neve. | 

4. Kattintson a **Mentés** gombra a kötés felvételéhez.
 
Miután meghatározta a kimeneti kötést, módosítania kell a kódot, hogy az a kötés használatával üzeneteket adjon hozzá az üzenetsorhoz.  

## <a name="update-the-function-code"></a>A függvénykód módosítása

1. A függvényre kattintva jelenítse meg a szerkesztőben a függvénykódot. 

2. C# függvény, frissítse a függvény definícióját a következőképpen vegye fel a **outputQueueItem** tárolási kötési paraméter. JavaScript-függvény esetében hagyja ki ezt a lépést.

    ```cs   
    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, 
        ICollector<string> outputQueueItem, TraceWriter log)
    {
        ....
    }
    ```

3. Adja hozzá a következő kódot a függvényhez a metódus visszatérése előtt. A függvény nyelvének megfelelő kódrészletet használjon.

    ```javascript
    context.bindings.outputQueueItem = "Name passed to the function: " + 
                (req.query.name || req.body.name);
    ```

    ```cs
    outputQueueItem.Add("Name passed to the function: " + name);     
    ```

4. A módosítások mentéséhez kattintson a **Mentés** elemre.

A HTTP-eseményindítónak üzenetben átadott érték bekerül az üzenetsorba.
 
## <a name="test-the-function"></a>A függvény tesztelése 

1. A kód módosításainak mentése után kattintson a **Futtatás** elemre. 

    ![Vegye fel egy üzenetsor-tároló kimeneti kötését egy függvénybe az Azure Portalon.](./media/functions-integrate-storage-queue-output-binding/functions-test-run-function.png)

2. A naplók ellenőrzésével győződjön meg arról, hogy sikeres volt a függvény futtatása. A Functions futtatókörnyezete egy **outqueue** nevű új üzenetsort hoz létre a tárfiókjában a kimeneti kötés első használatakor.

Ezután csatlakozhat a tárfiókhoz, hogy ellenőrizze az új üzenetsort és az abba felvett üzenetet. 

## <a name="connect-to-the-queue"></a>Csatlakozás az üzenetsorhoz

Hagyja ki az első három lépést, ha már telepítette a Storage Explorert, és már csatlakoztatta azt a tárfiókjához.    

1. Válassza ki a függvény **integráció** és az új **Azure Queue storage** kimeneti kötése, majd bontsa ki a **dokumentáció**. Másolja a **Fiók neve** és a **Fiók kulcsa** értéket. Ezekkel a hitelesítő adatokkal csatlakozhat a tárfiókhoz.
 
    ![Kérje le a tárfiókhoz való csatlakozáshoz szükséges hitelesítő adatokat.](./media/functions-integrate-storage-queue-output-binding/function-get-storage-account-credentials.png)

2. Futtassa a [Microsoft Azure Storage Explorer](http://storageexplorer.com/) eszközt, kattintson a bal oldalon található csatlakozási ikonra, válassza ki a **Tárfiók nevének és kulcsának használata** lehetőséget, és kattintson a **Tovább** elemre.

    ![Futtassa a Storage Account Explorer eszközt.](./media/functions-integrate-storage-queue-output-binding/functions-storage-manager-connect-1.png)
    
3. Illessze be az első lépésből a **Fiók neve** és a **Fiók kulcsa** értéket a megfelelő mezőkbe, majd kattintson a **Tovább**, és a **Csatlakozás** elemre. 
  
    ![Illessze be a tároló hitelesítő adatait, és csatlakozzon.](./media/functions-integrate-storage-queue-output-binding/functions-storage-manager-connect-2.png)

4. Bontsa ki a csatolt tárfiókot, **várólisták** , és ellenőrizze, hogy a várólista neve **Várólista_neve-elemek** létezik-e. Ezenkívül egy üzenetnek is kell szerepelnie már az üzenetsorban.  
 
    ![Hozzon létre egy tárolási üzenetsort.](./media/functions-integrate-storage-queue-output-binding/function-queue-storage-output-view-queue.png)
 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Felvett egy kimeneti kötést egy meglévő függvénybe. 

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

További információ a tárolási üzenetsor kötéséről: [Azure Functions – a tárolási üzenetsor kötései](functions-bindings-storage-queue.md). 



