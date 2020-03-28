---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: b052772bbfe9d69e430d9f722d8db56b48db7610
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "72933476"
---
Válassza **a Build**lehetőséget. A megnyíló párbeszédpanelen jelölje ki azt a mappát, amelybe az Xcode projektet exportálni szeretné.

Amikor az exportálás befejeződött, megjelenik egy mappa, amely az exportált Xcode projektet tartalmazza.

> [!NOTE]
> Ha megjelenik egy ablak, amely megkérdezi, hogy cserélni vagy hozzáfűzni szeretne-e, javasoljuk, hogy válassza a **Hozzáfűzés** lehetőséget, mert az gyorsabb. Csak akkor kell a **Csere** lehetőséget választania, ha a jelenetben eszközcserét módosít. (Például szülő-gyermek kapcsolatok hozzáadása, eltávolítása vagy módosítása, illetve tulajdonságok hozzáadása, eltávolítása vagy módosítása esetén.) Ha csak a forráskód módosítása, **hozzáfűzés** e kell lennie.

### <a name="convert-the-xcode-project-to-xcworkspace-containing-azure-spatial-anchors-references"></a>Az Xcode-projekt átalakítása Az Azure Spatial Anchors-hivatkozásokat tartalmazó xcworkspace-té

Az exportált Xcode projekt mappában futtassa ezt a parancsot a terminálon a projekthez szükséges CocoaPods telepítéséhez:

```bash
pod install --repo-update
```

Most megnyithatja `Unity-iPhone.xcworkspace` a projekt megnyitását Xcode-ban:

```bash
open ./Unity-iPhone.xcworkspace
```

> [!NOTE]
> A hibaelhárítási lépéseket [itt](../articles/spatial-anchors/quickstarts/get-started-unity-ios.md#cocoapods-issues-on-macos-catalina-1015) olvashatja, ha a macOS Catalina (10.15) rendszerre való frissítés után a CocoaPod-problémákkal kapcsolatos problémákat okoz.

A projektbeállítások megtekintéséhez válassza a gyökér **Unity-iPhone** csomópontot, majd válassza az **Általános** lapot.

Az **Aláírás csoportban**győződjön meg arról, hogy az **Aláírás automatikus kezelése** engedélyezve van. Ha nem, engedélyezze, majd válassza az **Automatikus engedélyezés lehetőséget** a buildbeállítások visszaállításához megjelenő párbeszédpanelen.

A **Telepítési adatok csoportban**győződjön `11.0`meg arról, hogy a telepítési **cél** beállítása a.

### <a name="deploy-the-app-to-your-ios-device"></a>Az alkalmazás telepítése iOS-készülékre

Csatlakoztassa az iOS-készüléket a Machez, és állítsa be az **aktív sémát** az iOS-készülékre.

![Eszköz kiválasztása](./media/spatial-anchors-unity/select-device.png)

Válassza **a Build( Build, majd az aktuális séma) lehetőséget.**

![Telepítés és futtatás](./media/spatial-anchors-unity/deploy-run.png)

> [!NOTE]
> Ha hibaüzenetet `library not found for -lPods-Unity-iPhone` lát, valószínűleg `.xcodeproj` a fájlt `.xcworkspace` nyitotta meg a fájl helyett.
