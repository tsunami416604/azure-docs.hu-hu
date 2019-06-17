---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: e8daaaf5b6b15eb3095f11e94c707a33b4b18e28
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "67135217"
---
Válassza ki **összeállítása**. A megnyíló párbeszédpanelen válassza ki a mappa az Xcode-projektben való exportálásához.

Az Exportálás befejeződése után megjelenik egy mappa, amely tartalmazza az exportált Xcode-projektben.

> [!NOTE]
> Ha megjelenik egy ablak, amely felkéri, ha szeretné-e cserélje le- és hozzáfűző, azt javasoljuk, hogy bejelölte **Hozzáfűzés** , gyorsabb, mert. Csak akkor kell kiválasztásához **cseréje** eszközök módosítása a jelenet. (Ha például hozzáadása, eltávolítása, vagy a szülő-gyermek kapcsolat módosítása vagy hozzáadása, eltávolítása vagy módosítása a tulajdonságok.) Ha csak forrás kódmódosítás szükséges, **Hozzáfűzés** el kell érnie.

### <a name="open-the-xcode-project"></a>Nyissa meg az Xcode-projektben

Az exportált Xcode projekt mappában futtassa ezt a parancsot a terminálon a projekthez szükséges CocoaPods telepítéséhez:

```bash
pod install --repo-update
```

Most megnyithatja `Unity-iPhone.xcworkspace` megnyitja a projektet az xcode-ban:

```bash
open ./Unity-iPhone.xcworkspace
```

> [!NOTE]
> Ha megjelenik egy `library not found for -lPods-Unity-iPhone` hiba történt, valószínűleg nyitotta meg a `.xcodeproj` fájl helyett a `.xcworkspace` fájlt. 

Válassza ki a legfelső szintű **Unity-iPhone** megtekintheti a projekt beállításokat, és válassza a csomópontot a **általános** fülre.

A **aláírás**, győződjön meg arról, hogy **aláírás automatikus kezelése** engedélyezve van. Ha nem, az engedélyezéshez, és válassza **engedélyezze az automatikus** a build beállításainak visszaállítása a megjelenő párbeszédpanelen.

Alatt **üzembe helyezési adatok**, győződjön meg arról, hogy a **telepítési cél** értékre van állítva `11.0`.

### <a name="deploy-the-app-to-your-ios-device"></a>Az iOS-eszközön az alkalmazás üzembe helyezése

Az IOS-es eszköz csatlakoztatása a Mac és a készlet a **aktív séma** az iOS-eszközre.

![Válassza ki az eszközt](./media/spatial-anchors-unity/select-device.png)

Válassza ki **hozhat létre és futtassa az aktuális séma**.

![Üzembe helyezése és futtatása](./media/spatial-anchors-unity/deploy-run.png)