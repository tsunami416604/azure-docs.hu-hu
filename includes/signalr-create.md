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
ms.openlocfilehash: c26e2de2c492c51c35527979ef0450ed7c2de499
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37100827"
---
1. Új Azure SignalR Services-erőforrás létrehozásához először jelentkezzen be az [Azure Portalra](https://portal.azure.com). Kattintson az **+ Erőforrás létrehozása** elemre az oldal bal felső oldalán. A **Keresés a piactéren** mezőbe írja be a **SignalR Service** kifejezést, és nyomja le az **Enter** billentyűt.

2. Kattintson a **SignalR Service** elemre a találatok között, és kattintson a **Létrehozás** elemre.

3. Az új **SignalR** beállítások oldalon adja hozzá a következő beállításokat az új SignalR-erőforráshoz:

    | Name (Név) | Javasolt érték | Leírás |
    | ---- | ----------------- | ----------- |
    | Erőforrás neve | *testsignalr* | Írja be a SignalR-erőforráshoz használandó egyedi erőforrásnevet. A névnek 1 és 63 karakter közötti sztringnek kell lennie, és kizárólag számokat, betűket és a `-` karaktert tartalmazhatja. A név nem kezdődhet a `-` karakterrel, és az egymást követő `-` karakterek nem érvényesek.|
    | Előfizetés | Válassza ki az előfizetését |  Válassza ki a SignalR teszteléséhez használni kívánt Azure-előfizetést. Ha a fiókja csak egyetlen előfizetéssel rendelkezik, automatikusan ez lesz kiválasztva, és az **Előfizetés** legördülő lista nem jelenik meg.|
    | Erőforráscsoport | Hozzon létre egy új *SignalRTestResources* nevű erőforráscsoportot| Válasszon ki vagy hozzon létre egy erőforráscsoportot a SignalR-erőforráshoz. Ez a csoport több erőforrás rendszerezéséhez hasznos, amelyeket az erőforráscsoport törlésekor egyszerre törölhet. További információk: [Using Resource groups to manage your Azure resources](../articles/azure-resource-manager/resource-group-overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez). |
    | Hely | *USA keleti régiója* | A **Hely** beállítással megadhatja a földrajzi helyet, ahol a SignalR-erőforrást üzemeltetni kívánja. A legjobb teljesítmény érdekében azt javasoljuk, hogy az erőforrást ugyanabban a régióban hozza létre, mint az alkalmazás többi összetevőjét. |
    | Tarifacsomag | *Ingyenes* | Jelenleg az **Ingyenes** és a **Standard** lehetőség érhető el. |
    | Rögzítés az irányítópulton | ✔ | Jelölje be ezt a jelölőnégyzetet az erőforrás irányítópulton való rögzítéséhez, hogy könnyebben megtalálja. |

4. Kattintson a **Create** (Létrehozás) gombra. Az üzembe helyezés befejezése eltarthat néhány percig.

5. Az üzembe helyezés befejezése után kattintson a **BEÁLLÍTÁSOK** területen található **Kulcsok** elemre. Másolja ki az elsődleges kulcs kapcsolati sztringjét. Később ezzel fogja konfigurálni, hogy az alkalmazás az Azure SignalR Service-erőforrást használja.

    A kapcsolati sztring a következőképpen fog kinézni:
    
        Endpoint=<service_endpoint>;AccessKey=<access_key>;
