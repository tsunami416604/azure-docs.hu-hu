---
title: Azure-beli ütemterven futó függvény létrehozása
description: Megtudhatja, hogyan hozhat olyan függvényt az Azure-ban, amely az Ön által meghatározott ütemezés alapján fut.
ms.assetid: ba50ee47-58e0-4972-b67b-828f2dc48701
ms.topic: how-to
ms.date: 04/16/2020
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 9e542c493c02174364072f91d092f05ad9ec69cf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90973089"
---
# <a name="create-a-function-in-azure-that-is-triggered-by-a-timer"></a>Időzítő által aktivált függvény létrehozása az Azure-ban

Megtudhatja, hogyan használhatja a Azure Functionst olyan [kiszolgáló](https://azure.microsoft.com/solutions/serverless/) nélküli függvény létrehozásához, amely az Ön által meghatározott ütemterv alapján fut.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

+ Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), mielőtt hozzákezd.

## <a name="create-an-azure-function-app"></a>Azure-függvényalkalmazás létrehozása

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Az új Function-alkalmazás készen áll a használatra. Ezután létre fog hozni egy függvényt az új Function alkalmazásban.

:::image type="content" source="./media/functions-create-scheduled-function/function-app-create-success.png" alt-text="A függvényalkalmazás elkészült." border="true":::

<a name="create-function"></a>

## <a name="create-a-timer-triggered-function"></a>Időzítő által aktivált függvény létrehozása

1. A Function alkalmazásban válassza a **függvények**, majd a **+ Hozzáadás** lehetőséget. 

   :::image type="content" source="./media/functions-create-scheduled-function/function-add-function.png" alt-text="A függvényalkalmazás elkészült." border="true":::

1. Válassza ki az **időzítő trigger** sablonját. 

    :::image type="content" source="./media/functions-create-scheduled-function/function-select-timer-trigger.png" alt-text="A függvényalkalmazás elkészült." border="true":::

1. Konfigurálja az új triggert a rendszerkép alatti táblázatban megadott beállításokkal, majd válassza a **create Function (függvény létrehozása**) lehetőséget.

    :::image type="content" source="./media/functions-create-scheduled-function/function-configure-timer-trigger.png" alt-text="A függvényalkalmazás elkészült." border="true":::
    
    | Beállítás | Ajánlott érték | Leírás |
    |---|---|---|
    | **Név** | Alapértelmezett | Az időzítő által aktivált függvény nevét adja meg. |
    | **Ütemezés** | 0 \* /1 \* \* \*\* | Hat mezőből álló [CRON-kifejezés](functions-bindings-timer.md#ncrontab-expressions), amely úgy ütemezi a függvényt, hogy minden percben fusson. |

## <a name="test-the-function"></a>A függvény tesztelése

1. A függvényben válassza a **Code + test (kód + tesztelés** ) lehetőséget, és bontsa ki a naplókat.

    :::image type="content" source="./media/functions-create-scheduled-function/function-test-timer-trigger.png" alt-text="A függvényalkalmazás elkészült." border="true":::

1. A naplókba írt információk megtekintésével ellenőrizze a végrehajtást.

    :::image type="content" source="./media/functions-create-scheduled-function/function-view-timer-logs.png" alt-text="A függvényalkalmazás elkészült." border="true":::

Most módosíthatja a függvény ütemezését, hogy ne percenként, hanem óránként egyszer fusson.

## <a name="update-the-timer-schedule"></a>Az időzítő ütemezésének módosítása

1. A függvényben válassza az **integráció**lehetőséget. Itt adhatja meg a függvény bemeneti és kimeneti kötéseit, és beállíthatja az ütemtervet is. 

1. Válassza az **időzítő (myTimer)** lehetőséget.

    :::image type="content" source="./media/functions-create-scheduled-function/function-update-timer-schedule.png" alt-text="A függvényalkalmazás elkészült." border="true":::

1. Frissítse az **ütemezett** értéket a értékre `0 0 */1 * * *` , majd válassza a **Mentés**lehetőséget.  

    :::image type="content" source="./media/functions-create-scheduled-function/function-edit-timer-schedule.png" alt-text="A függvényalkalmazás elkészült." border="true":::

Mostantól óránként egyszer fut a függvény.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Létrehozott egy ütemezett függvényt, amely egy ütemterv alapján fut. Az időzítő eseményindítókkal kapcsolatos további információkért lásd: [kód végrehajtásának elvégzése Azure Functionssal](functions-bindings-timer.md).

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
