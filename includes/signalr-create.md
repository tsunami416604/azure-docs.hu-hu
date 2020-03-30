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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "76021305"
---
1. Azure SignalR-szolgáltatás-erőforrás létrehozásához először jelentkezzen be az [Azure Portalra.](https://portal.azure.com) A lap bal felső részén válassza a **+ Erőforrás létrehozása**lehetőséget. A **Keresés a piactéren** mezőbe írja be a **SignalR Service kifejezést.**

2. Az eredmények között válassza a **SignalR szolgáltatás** lehetőséget, majd a **Létrehozás gombot.**

3. Az új **SignalR** beállítások lapon adja meg az új SignalR-erőforrás beállításait:

    | Név | Javasolt érték | Leírás |
    | ---- | ----------------- | ----------- |
    | Erőforrás neve | *testsignalr* | Írja be a SignalR-erőforráshoz használandó egyedi erőforrásnevet. A név nek 1 és 63 karakteres karakterláncnak kell lennie,`-`és csak számokat, betűket és kötőjelet ( ) tartalmazhat. A név nem kezdődhet és végződhet kötőjellel, és az egymást követő kötőjel karakterek érvénytelenek.|
    | Előfizetés | Válassza ki az előfizetését |  Válassza ki a SignalR teszteléséhez használni kívánt Azure-előfizetést. Ha a fiókjában csak egy előfizetés van, a rendszer automatikusan kiválasztja, és az **Előfizetés** legördülő lista nem jelenik meg.|
    | Erőforráscsoport | *SignalRTestResources* nevű erőforráscsoport létrehozása| Válasszon ki vagy hozzon létre egy erőforráscsoportot a SignalR-erőforráshoz. Ez a csoport több olyan erőforrás rendszerezéséhez hasznos, amelyeket az erőforráscsoport törlésével egyszerre szeretne törölni. További információk: [Using Resource groups to manage your Azure resources](../articles/azure-resource-manager/management/overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez). |
    | Hely | *USA keleti régiója* | A **Hely** beállítással megadhatja a földrajzi helyet, ahol a SignalR-erőforrást üzemeltetni kívánja. A legjobb teljesítmény érdekében azt javasoljuk, hogy az erőforrást ugyanabban a régióban hozza létre, mint az alkalmazás többi összetevőjét. |
    | Tarifacsomag | *Ingyenes* | Jelenleg **ingyenes** és **standard** opciók állnak rendelkezésre. |
    | Rögzítés az irányítópulton | ✔ | Jelölje be ezt a jelölőnégyzetet, ha azt szeretné, hogy az erőforrás az irányítópulton legyen rögzítve, így könnyebben megtalálhatja. |

4. Kattintson a **Létrehozás** gombra. A központi telepítés néhány percet is igénybe vehet.

5. A telepítés befejezése után válassza a **Kulcsok** lehetőséget a **BEÁLLÍTÁSOK csoportban.** Másolja a kapcsolati karakterláncot az elsődleges kulcsba. Ezt a karakterláncot később fogja használni az alkalmazás konfigurálásához az Azure SignalR Service erőforrás használatára.

    A kapcsolati sztring a következőképpen fog kinézni:
    
        Endpoint=<service_endpoint>;AccessKey=<access_key>;
