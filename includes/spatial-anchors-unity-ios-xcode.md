---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: b052772bbfe9d69e430d9f722d8db56b48db7610
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933476"
---
Válassza a **Létrehozás**lehetőséget. A megnyíló párbeszédpanelen válasszon ki egy mappát, amelybe exportálni szeretné a Xcode projektet.

Az Exportálás befejeztével megjelenik az exportált Xcode projektet tartalmazó mappa.

> [!NOTE]
> Ha megjelenik egy ablak, amely rákérdez, hogy szeretné-e cserélni vagy hozzáfűzni, javasoljuk, hogy a **hozzáfűzést** válassza, mert gyorsabb. Csak akkor kell kiválasztania a **replace (csere** ) lehetőséget, ha az eszközeit megváltoztatja a jelenetben. (Például a szülő-gyermek kapcsolatok hozzáadásával, eltávolításával vagy módosításával, illetve a tulajdonságok hozzáadásával, eltávolításával vagy módosításával.) Ha csak a forráskód módosításait hajtja végre, a **hozzáfűzésnek** elegendőnek kell lennie.

### <a name="convert-the-xcode-project-to-xcworkspace-containing-azure-spatial-anchors-references"></a>A Xcode-projekt átalakítása az Azure térbeli horgonyokra mutató hivatkozásokat tartalmazó xcworkspace

Az exportált Xcode-projekt mappában futtassa ezt a parancsot a terminálon a projekthez szükséges CocoaPods telepítéséhez:

```bash
pod install --repo-update
```

Most megnyithatja `Unity-iPhone.xcworkspace` a projekt megnyitásához a Xcode:

```bash
open ./Unity-iPhone.xcworkspace
```

> [!NOTE]
> Tekintse meg a hibaelhárítási lépéseket [itt](../articles/spatial-anchors/quickstarts/get-started-unity-ios.md#cocoapods-issues-on-macos-catalina-1015) , ha a MacOS Catalina (10,15) verzióra történő frissítés után CocoaPod problémák léptek fel.

Válassza ki a root **Unity-iPhone** csomópontot a projekt beállításainak megtekintéséhez, majd válassza az **általános** lapot.

Az **aláírás**területen győződjön meg arról, hogy az **aláírás automatikus kezelése** engedélyezve van. Ha nem, engedélyezze, majd válassza az **automatikus engedélyezése** lehetőséget a létrehozási beállítások alaphelyzetbe állításához megjelenő párbeszédpanelen.

Győződjön meg arról, hogy a központi **telepítési információ** **területen az `11.0`** érték van beállítva.

### <a name="deploy-the-app-to-your-ios-device"></a>Az alkalmazás üzembe helyezése iOS-eszközön

Az iOS-eszköz csatlakoztatása a Mac számítógéphez, és az **aktív séma** beállítása az iOS-eszközre.

![Válassza ki az eszközt](./media/spatial-anchors-unity/select-device.png)

Válassza **a létrehozás lehetőséget, majd futtassa az aktuális sémát**.

![Üzembe helyezés és Futtatás](./media/spatial-anchors-unity/deploy-run.png)

> [!NOTE]
> Ha `library not found for -lPods-Unity-iPhone` hiba jelenik meg, akkor az `.xcworkspace` fájl helyett valószínűleg megnyitotta a `.xcodeproj` fájlt.
