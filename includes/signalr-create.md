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
ms.openlocfilehash: 39bff26baea622e6c0ed524ca68c3c8bae4e770d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "76021305"
---
1. Az Azure Signaler szolgáltatás erőforrásának létrehozásához először jelentkezzen be a [Azure Portalba](https://portal.azure.com). Az oldal bal felső részén válassza az **+ erőforrás létrehozása**lehetőséget. A **Keresés a piactéren** szövegmezőbe írja be a **signaler szolgáltatást**.

2. Válassza ki a **signaler szolgáltatást** az eredmények között, majd válassza a **Létrehozás**lehetőséget.

3. Az új **jelző** beállításai lapon adja hozzá a következő beállításokat az új jelző erőforráshoz:

    | Name (Név) | Javasolt érték | Leírás |
    | ---- | ----------------- | ----------- |
    | Erőforrás neve | *testsignalr* | Írja be a SignalR-erőforráshoz használandó egyedi erőforrásnevet. A névnek 1 és 63 karakter közötti sztringnek kell lennie, és csak számokat, betűket és kötőjel (`-`) karaktert tartalmazhat. A név nem kezdődhet és nem végződhet a kötőjel karakterrel, és az egymást követő kötőjelek nem érvényesek.|
    | Előfizetés | Válassza ki az előfizetését |  Válassza ki a SignalR teszteléséhez használni kívánt Azure-előfizetést. Ha a fiókja csak egyetlen előfizetéssel rendelkezik, akkor automatikusan ki van választva, és az **előfizetés** legördülő lista nem jelenik meg.|
    | Erőforráscsoport | Hozzon létre egy *SignalRTestResources* nevű erőforráscsoportot| Válasszon ki vagy hozzon létre egy erőforráscsoportot a SignalR-erőforráshoz. Ez a csoport akkor lehet hasznos, ha több olyan erőforrást szeretne szervezni, amelyet az erőforráscsoport törlésével egyszerre törölni kíván. További információk: [Using Resource groups to manage your Azure resources](../articles/azure-resource-manager/management/overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez). |
    | Hely | *USA keleti régiója* | A **Hely** beállítással megadhatja a földrajzi helyet, ahol a SignalR-erőforrást üzemeltetni kívánja. A legjobb teljesítmény érdekében azt javasoljuk, hogy az erőforrást ugyanabban a régióban hozza létre, mint az alkalmazás többi összetevőjét. |
    | Tarifacsomag | *Ingyenes* | Jelenleg az **ingyenes** és a **standard** lehetőségek állnak rendelkezésre. |
    | Rögzítés az irányítópulton | ✔ | Jelölje be ezt a jelölőnégyzetet, ha szeretné, hogy az erőforrás rögzítve legyen az irányítópulton, így könnyebben megtalálhatja. |

4. Kattintson a **Létrehozás** gombra. Az üzembe helyezés eltarthat néhány percig.

5. Az üzembe helyezés befejezése után válassza a **kulcsok** elemet a **Beállítások**területen. Másolja az elsődleges kulcshoz tartozó kapcsolatok karakterláncát. Ezt a karakterláncot később fogja használni, hogy az alkalmazás az Azure Signaler szolgáltatás erőforrásának használatára legyen konfigurálva.

    A kapcsolati sztring a következőképpen fog kinézni:
    
        Endpoint=<service_endpoint>;AccessKey=<access_key>;
