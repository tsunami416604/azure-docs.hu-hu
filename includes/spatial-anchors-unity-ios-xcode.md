---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: 36d509bdcd1fda61cb85fae7fa38ed126697f888
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2019
ms.locfileid: "56752206"
---
Válassza ki **összeállítása** egy párbeszédpanel megnyitásához. Ezután válassza ki egy mappát, amelybe exportálni az Xcode-projektben.

Az Exportálás befejeződése után egy mappa jelenik meg amely tartalmazza az exportált Xcode-projektben.

### <a name="open-the-xcode-project"></a>Nyissa meg az Xcode-projektben

Az exportált Xcode projekt mappában futtassa a következő parancsot a projekthez szükséges CocoaPods telepítéséhez:

```bash
pod install
```

Most is nyissa meg a `Unity-iPhone.xcworkspace` megnyitja a projektet az xcode-ban:

```bash
open ./Unity-iPhone.xcworkspace
```

> [!NOTE]
> Ha megjelenik egy `library not found for -lPods-Unity-iPhone` hiba történt, valószínűleg nyitotta meg a `.xcodeproj` fájl helyett a `.xcworkspace`. Nyissa meg a `.xcworkspace` , és próbálkozzon újra.

Válassza ki a legfelső szintű **Unity-iPhone** megtekintése a Projektbeállítások között, és válassza a csomópontot a **általános** fülre.

A **aláírás**válassza **aláírás automatikus kezelése**. Válassza ki **engedélyezze az automatikus** alaphelyzetbe állítja a megjelenő párbeszédpanelen összeállítási beállítások.

Alatt **üzembe helyezési adatok**, győződjön meg arról, hogy a **telepítési cél** értékre van állítva `11.0`.

### <a name="deploy-the-app-to-your-ios-device"></a>Az iOS-eszközön az alkalmazás üzembe helyezése

Az IOS-es eszköz csatlakoztatása a Mac és a készlet a **aktív séma** az iOS-eszközre.

![Válassza ki az eszközt](./media/spatial-anchors-unity/select-device.png)

Válassza ki **hozhat létre és futtassa az aktuális séma**.

![Üzembe helyezése és futtatása](./media/spatial-anchors-unity/deploy-run.png)