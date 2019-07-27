---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: d8b6b1bfcbceb1168d0f74c73e72bd42b41bb2ec
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562513"
---
Válassza a **Létrehozás**lehetőséget. A megnyíló párbeszédpanelen válasszon ki egy mappát, amelybe exportálni szeretné a Xcode projektet.

Az Exportálás befejeztével megjelenik az exportált Xcode projektet tartalmazó mappa.

> [!NOTE]
> Ha megjelenik egy ablak, amely rákérdez, hogy szeretné-e cserélni vagy hozzáfűzni, javasoljuk  , hogy a hozzáfűzést válassza, mert gyorsabb. Csak akkor kell kiválasztania a Replace ( **Csere** ) lehetőséget, ha az eszközeit megváltoztatja a jelenetben. (Például a szülő-gyermek kapcsolatok hozzáadásával, eltávolításával vagy módosításával, illetve a tulajdonságok hozzáadásával, eltávolításával vagy módosításával.) Ha csak a forráskód módosításait hajtja végre  , a hozzáfűzésnek elegendőnek kell lennie.

### <a name="convert-the-xcode-project-to-xcworkspace-containing-azure-spatial-anchors-references"></a>A Xcode-projekt átalakítása az Azure térbeli horgonyokra mutató hivatkozásokat tartalmazó xcworkspace

Az exportált Xcode-projekt mappában futtassa ezt a parancsot a terminálon a projekthez szükséges CocoaPods telepítéséhez:

```bash
pod install --repo-update
```

Most már megnyithatja `Unity-iPhone.xcworkspace` a projektet a Xcode:

```bash
open ./Unity-iPhone.xcworkspace
```

> [!NOTE]
> Ha `library not found for -lPods-Unity-iPhone` hibaüzenet jelenik meg, akkor a fájl helyett `.xcworkspace` valószínűleg `.xcodeproj` megnyitotta a fájlt. 

Válassza ki a root **Unity-iPhone** csomópontot a projekt beállításainak megtekintéséhez, majd válassza az **általános** lapot.

Az **aláírás**területen győződjön meg arról, hogy az **aláírás automatikus kezelése** engedélyezve van. Ha nem, engedélyezze, majd válassza az **automatikus engedélyezése** lehetőséget a létrehozási beállítások alaphelyzetbe állításához megjelenő párbeszédpanelen.

Győződjön meg arról, hogy `11.0`a központi **telepítési adatok**területen a **telepítési cél** beállítás van megadva.

### <a name="deploy-the-app-to-your-ios-device"></a>Az alkalmazás üzembe helyezése iOS-eszközön

Az iOS-eszköz csatlakoztatása a Mac számítógéphez, és az **aktív séma** beállítása az iOS-eszközre.

![Válassza ki az eszközt](./media/spatial-anchors-unity/select-device.png)

Válassza **a létrehozás lehetőséget, majd futtassa az aktuális sémát**.

![Üzembe helyezés és Futtatás](./media/spatial-anchors-unity/deploy-run.png)