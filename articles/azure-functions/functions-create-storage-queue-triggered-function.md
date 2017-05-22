---
title: "Üzenetsorban lévő üzenetek által aktivált függvények létrehozása az Azure-ban | Microsoft Docs"
description: "Használja az Azure Functions szolgáltatást olyan kiszolgáló nélküli függvények létrehozására, amelyeket az Azure Storage üzenetsorába elküldött üzenetek hívnak meg."
services: azure-functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 361da2a4-15d1-4903-bdc4-cc4b27fc3ff4
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/25/2017
ms.author: glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: e6cf8797d08609f847e33f88e78fbcd3f3743a08
ms.contentlocale: hu-hu
ms.lasthandoff: 05/12/2017


---
# <a name="create-a-function-triggered-by-azure-queue-storage"></a>Azure Storage-üzenetsor által aktivált függvény létrehozása

Ismerje meg, hogyan hozhat létre az Azure Storage üzenetsorába küldött üzenetek által aktivált függvényt.

![Tekintse meg a naplókban található üzeneteket.](./media/functions-create-storage-queue-triggered-function/function-app-in-portal-editor.png)

A témakörben foglalt lépések végrehajtása legfeljebb 5 percet vehet igénybe.

## <a name="prerequisites"></a>Előfeltételek

A minta futtatása előtt az alábbiakat kell elvégeznie:

- A [Microsoft Azure Storage Explorer](http://storageexplorer.com/) letöltése és telepítése.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]

## <a name="create-an-azure-function-app"></a>Azure-függvényalkalmazás létrehozása

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![A függvényalkalmazás elkészült.](./media/functions-create-first-azure-function/function-app-create-success.png)

Ezután létrehozhat egy függvényt az új függvényalkalmazásban.

<a name="create-function"></a>

## <a name="create-a-queue-triggered-function"></a>Üzenetsor által aktivált függvény létrehozása

Bontsa ki a függvényalkalmazást, kattintson a **Függvények** elem melletti **+** gombra, majd kattintson a kívánt nyelvű **QueueTrigger** sablonra. Ezután használja a táblázatban megadott beállításokat, és kattintson a **Létrehozás** gombra.

![Hozza létre a tároló üzenetsora által aktivált függvényt.](./media/functions-create-storage-queue-triggered-function/functions-create-queue-storage-trigger-portal.png)

| Beállítás | Ajánlott érték | Leírás |
|---|---|---|
| **Üzenetsor neve**   | myqueue-items    | A tárfiókhoz csatlakoztatni kívánt üzenetsor neve. |
| **Tárfiók kapcsolata** | AzureWebJobStorage | Választhatja a függvényalkalmazás által már használt tárfiókkapcsolatot, vagy létrehozhat egy újat.  |
| **A függvény neve** | Egyedi a függvényalkalmazásban | Az üzenetsor által aktivált függvény neve. |

Ezután csatlakozzon az Azure Storage-fiókjához, és hozza létre a **myqueue-items** tárolót.

## <a name="create-the-queue"></a>Az üzenetsor létrehozása

1. A függvényben kattintson az **Integráció** elemre, bontsa ki a **Dokumentáció** elemet, és másolja a **Fiók neve** és a **Fiók kulcsa** értéket. Ezekkel a hitelesítő adatokkal csatlakozhat a tárfiókhoz. Ha már csatlakozott a tárfiókjához, folytassa a 4. lépéssel.

    ![Kérje le a tárfiókhoz való csatlakozáshoz szükséges hitelesítő adatokat.](./media/functions-create-storage-queue-triggered-function/functions-storage-account-connection.png)v

1. Futtassa a [Microsoft Azure Storage Explorer](http://storageexplorer.com/) eszközt, kattintson a bal oldalon található csatlakozási ikonra, válassza a **Tárfiók nevének és kulcsának használata** lehetőséget, és kattintson a **Tovább** gombra.

    ![Futtassa a Storage Account Explorer eszközt.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-connect-1.png)

1. Adja meg az 1. lépésben megállapított **Fiók neve** és **Fiók kulcsa** értéket, és kattintson a **Tovább**, majd a **Csatlakozás** gombra.

    ![Adja meg a tároló hitelesítő adatait, és csatlakozzon.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-connect-2.png)

1. Bontsa ki a csatolt tárfiókot, kattintson a jobb gombbal az **Üzenetsorok** elemre, kattintson az **Üzenetsor létrehozása** elemre, írja be a `myqueue-items` értéket, és nyomja meg az Enter billentyűt.

    ![Hozzon létre egy tárolási üzenetsort.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-create-queue.png)

Az üzenetsor létrehozása után tesztelheti a függvényt úgy, hogy felvesz egy üzenetet az üzenetsorba.

## <a name="test-the-function"></a>A függvény tesztelése

1. Térjen vissza az Azure Portalra, keresse meg a függvényt, bontsa ki a **Naplók** elemet a lap alján, és győződjön meg arról, hogy a naplózási adatfolyam nincs leállítva.

1. A Storage Explorerben bontsa ki a tárfiókot, az **Üzenetsorok**, majd az **üzenetsorelemek** elemet, és kattintson az **Üzenet hozzáadása** elemre.

    ![Vegyen fel egy üzenetet az üzenetsorba.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-add-message.png)

1. Írja be a „Hello World!” üzenetet az **Üzenet szövege** mezőbe, és kattintson az **OK** gombra.

1. Várjon néhány másodpercet, majd térjen vissza a függvény naplóihoz, és győződjön meg arról, hogy megtörtént az új üzenet olvasása az üzenetsorból.

    ![Tekintse meg a naplókban található üzeneteket.](./media/functions-create-storage-queue-triggered-function/functions-queue-storage-trigger-view-logs.png)

1. Térjen vissza a Storage Explorerbe, kattintson a **Frissítés** elemre, és ellenőrizze, hogy megtörtént-e az üzenet feldolgozása, és hogy el lett-e távolítva az üzenetsorból.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Létrehozott egy függvényt, amely akkor fut, amikor üzenet felvétele történik a tárolási üzenetsorba.

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

További információ a tárolási üzenetsor eseményindítóiról: [Azure Functions – a tárolási üzenetsor kötései](functions-bindings-storage-queue.md).
