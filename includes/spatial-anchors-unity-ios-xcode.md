---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: 6768b1b8e0f5d7d3644779268025551c4e1aef9b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57964171"
---
Válassza ki **összeállítása** egy párbeszédpanel megnyitásához. Ezután válassza ki egy mappát, amelybe exportálni az Xcode-projektben.

Az Exportálás befejeződése után egy mappa jelenik meg amely tartalmazza az exportált Xcode-projektben.

> [!NOTE]
> Ha egy párbeszédpanel rákérdez, hogy felugró **cserélje le** vagy **Hozzáfűzés**, **Hozzáfűzés** használata javasolt, mivel ez gyorsabb. Csak akkor kell végrehajtani egy **cseréje** Ha eszközök változnak a jelenet (Hozzáadás, eltávolítás, módosítása a szülő-gyermek kapcsolat, a Tulajdonságok hozzáadása/eltávolítása/módosítása, stb). Ha csak forrás kódmódosítás szükséges, **Hozzáfűzés** el kell érnie.

### <a name="open-the-xcode-project"></a>Nyissa meg az Xcode-projektben

Az exportált Xcode projekt mappában futtassa a következő parancsot a terminálon a projekthez szükséges CocoaPods telepítéséhez:

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

A **aláírás**, ellenőrizze, hogy **aláírás automatikus kezelése** engedélyezve van. Ha nem, az aktiválást, és válassza **engedélyezze az automatikus** alaphelyzetbe állítja a megjelenő párbeszédpanelen összeállítási beállítások.

Alatt **üzembe helyezési adatok**, győződjön meg arról, hogy a **telepítési cél** értékre van állítva `11.0`.

### <a name="deploy-the-app-to-your-ios-device"></a>Az iOS-eszközön az alkalmazás üzembe helyezése

Az IOS-es eszköz csatlakoztatása a Mac és a készlet a **aktív séma** az iOS-eszközre.

![Válassza ki az eszközt](./media/spatial-anchors-unity/select-device.png)

Válassza ki **hozhat létre és futtassa az aktuális séma**.

![Üzembe helyezése és futtatása](./media/spatial-anchors-unity/deploy-run.png)