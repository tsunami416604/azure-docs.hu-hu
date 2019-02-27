---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: b802c9dbd0cef65325cb03538b68b49c57b85bb3
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56890945"
---
Válassza ki **összeállítása** egy párbeszédpanel megnyitásához. Ezután válassza ki egy mappát, amelybe exportálni az Xcode-projektben.

Az Exportálás befejeződése után egy mappa jelenik meg amely tartalmazza az exportált Xcode-projektben.

### <a name="open-the-xcode-project"></a>Nyissa meg az Xcode-projektben

Az exportált Xcode projekt mappában futtassa a következő parancsot a projekthez szükséges CocoaPods telepítéséhez:

```bash
pod install --repo-update
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