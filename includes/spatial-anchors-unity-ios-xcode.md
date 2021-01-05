---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 81d2804d99896200ea6f68592ea168112e172c20
ms.sourcegitcommit: 6cca6698e98e61c1eea2afea681442bd306487a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/24/2020
ms.locfileid: "97762604"
---
Válassza a **Létrehozás** lehetőséget. A megnyíló ablaktáblán válasszon ki egy mappát, amelybe exportálni szeretné a Xcode projektet.

   Az Exportálás befejezésekor megjelenik az exportált Xcode projektet tartalmazó mappa.

   > [!NOTE]
   > Ha egy ablakban megjelenik egy üzenet, amely megkérdezi, hogy szeretné-e cserélni vagy hozzáfűzni, javasoljuk, hogy a **Hozzáfűzés** lehetőséget válassza, mert gyorsabb. Csak akkor válassza a **replace (csere** ) lehetőséget, ha az eszközeit megváltoztatja a jelenetben. Előfordulhat például, hogy felveszi, eltávolítja vagy megváltoztatja a szülő/gyermek kapcsolatokat, vagy lehet, hogy a tulajdonságok hozzáadása, eltávolítása vagy módosítása folyamatban van. Ha csak a forráskód módosításait hajtja végre, a **hozzáfűzésnek** elegendőnek kell lennie.

## <a name="open-the-xcode-project"></a>A Xcode projekt megnyitása

Most már megnyithatja a `Unity-iPhone.xcodeproj` projektet a Xcode-ben. 

Indítsa el a Xcode, és nyissa meg az exportált `Unity-iPhone.xcodeproj` projektet, vagy indítsa el a projektet a Xcode-ben a következő parancs futtatásával arról a helyről, ahol a projektet exportálta:

 ```bash
open ./Unity-iPhone.xcodeproj
```

Válassza ki a root **Unity-iPhone** csomópontot a projekt beállításainak megtekintéséhez, majd válassza az **általános** lapot.

Győződjön meg arról, hogy az üzembe **helyezési adatok** területen az **iOS 11,0** értékre van beállítva.

Válassza az **aláírás & képességek** fület, és győződjön meg arról, hogy az **aláírás automatikus kezelése** engedélyezve van. Ha nem, engedélyezze, majd állítsa alaphelyzetbe a Build-beállításokat a megjelenő ablaktábla **automatikus engedélyezése** elemének kiválasztásával.

## <a name="deploy-the-app-to-your-ios-device"></a>Az alkalmazás üzembe helyezése iOS-eszközön

Az iOS-eszköz csatlakoztatása a Mac számítógéphez, és az **aktív séma** beállítása az iOS-eszközre.

   ![Képernyőfelvétel az eszköz kiválasztásához az iPhone gombról.](./media/spatial-anchors-unity/select-device.png)

Válassza **a létrehozás lehetőséget, majd futtassa az aktuális sémát**.

   ![Képernyőkép az "üzembe helyezés és Futtatás" nyíl gombról.](./media/spatial-anchors-unity/deploy-run.png)
