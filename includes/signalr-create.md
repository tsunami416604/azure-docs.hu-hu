---
title: fájl belefoglalása
description: fájl belefoglalása
services: signalr
author: wesmc7777
ms.service: signalr
ms.topic: include
ms.date: 04/17/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: 28d003e123069c47d87d81570b4a5b69b3b9d64b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179489"
---
1. Az Azure SignalR Service erőforrás létrehozásához, először jelentkezzen be a [az Azure portal](https://portal.azure.com). Válassza a lap bal felső oldalán **+ erőforrás létrehozása**. Az a **keresés a piactéren** szöveget adja meg **SignalR Service**.

2. Válassza ki **SignalR Service** az eredményeket, és válassza a **létrehozás**.

3. Az új **SignalR** beállítások lapon adja hozzá az új SignalR-erőforrás a következő beállításokat:

    | Name (Név) | Javasolt érték | Leírás |
    | ---- | ----------------- | ----------- |
    | Erőforrás neve | *testsignalr* | Írja be a SignalR-erőforráshoz használandó egyedi erőforrásnevet. A neve 1 – 63 karakter karakterláncot, és csak számokat, betűket és kötőjelet tartalmazhat (`-`) karaktert. A név nem kezdődhet vagy végződhet kötőjelet, és egymást követő kötőjel karakterek nem használhatók.|
    | Előfizetés | Válassza ki az előfizetését |  Válassza ki a SignalR teszteléséhez használni kívánt Azure-előfizetést. Ha a fiókja csak egyetlen előfizetéssel rendelkezik, a rendszer automatikusan kiválasztja és a **előfizetés** legördülő menü nem jelenik meg.|
    | Erőforráscsoport | Hozzon létre egy erőforráscsoportot *SignalRTestResources*| Válasszon ki vagy hozzon létre egy erőforráscsoportot a SignalR-erőforráshoz. Ennek a csoportnak több erőforrást, amelyeket érdemes az erőforráscsoport törlésével egy időben törlése rendszerezésének szempontjából hasznos. További információk: [Using Resource groups to manage your Azure resources](../articles/azure-resource-manager/resource-group-overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez). |
    | Location egység | *USA keleti régiója* | A **Hely** beállítással megadhatja a földrajzi helyet, ahol a SignalR-erőforrást üzemeltetni kívánja. A legjobb teljesítmény érdekében azt javasoljuk, hogy az erőforrást ugyanabban a régióban hozza létre, mint az alkalmazás többi összetevőjét. |
    | Tarifacsomag | *Ingyenes* | Jelenleg **ingyenes** és **Standard** beállítások érhetők el. |
    | Rögzítés az irányítópulton | ✔ | Válassza ki ezt a jelölőnégyzetet a az erőforrást, így könnyebben megtalálhatja az irányítópulton rögzítve van. |

4. Kattintson a **Létrehozás** gombra. Az üzembe helyezés eltarthat néhány percig.

5. Az üzembe helyezés befejezése után jelölje ki a **kulcsok** alatt **beállítások**. Másolja az elsődleges kulcs kapcsolati karakterláncára. Ez a karakterlánc később fogja használni az Azure SignalR Service erőforrás használatára az alkalmazás konfigurálása.

    A kapcsolati sztring a következőképpen fog kinézni:
    
        Endpoint=<service_endpoint>;AccessKey=<access_key>;
