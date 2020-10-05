---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 08/14/2020
ms.author: rgarcia
ms.openlocfilehash: b93243a537fafce6d865ec207b12dc2654cafd20
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "89536259"
---
Válassza a **Létrehozás**lehetőséget. A megnyíló párbeszédpanelen válasszon ki egy mappát, amelybe exportálni szeretné a Xcode projektet.

Az Exportálás befejeztével megjelenik az exportált Xcode projektet tartalmazó mappa.

> [!NOTE]
> Ha megjelenik egy ablak, amely rákérdez, hogy szeretné-e cserélni vagy hozzáfűzni, javasoljuk, hogy a **hozzáfűzést** válassza, mert gyorsabb. Csak akkor kell kiválasztania a **replace (csere** ) lehetőséget, ha az eszközeit megváltoztatja a jelenetben. (Például a szülő-gyermek kapcsolatok hozzáadásával, eltávolításával vagy módosításával, illetve a tulajdonságok hozzáadásával, eltávolításával vagy módosításával.) Ha csak a forráskód módosításait hajtja végre, a **hozzáfűzésnek** elegendőnek kell lennie.

## <a name="open-the-xcode-project"></a>A Xcode projekt megnyitása

Most már megnyithatja `Unity-iPhone.xcodeproj` a Xcode. Indítsa el a Xcode, és nyissa meg az exportált `Unity-iPhone.xcodeproj` projektet, vagy indítsa el a projektet a Xcode-ben úgy, hogy a következő parancsot futtatja a projekt exportálásának helyétől:

```bash
open ./Unity-iPhone.xcodeproj
```

Válassza ki a root **Unity-iPhone** csomópontot a projekt beállításainak megtekintéséhez, majd válassza az **általános** lapot.

Az **aláírás**területen győződjön meg arról, hogy az **aláírás automatikus kezelése** engedélyezve van. Ha nem, engedélyezze, majd válassza az **automatikus engedélyezése** lehetőséget a létrehozási beállítások alaphelyzetbe állításához megjelenő párbeszédpanelen.

Győződjön meg arról, hogy a központi **telepítési adatok**területen a **telepítési cél** beállítás van megadva `11.0` .

## <a name="deploy-the-app-to-your-ios-device"></a>Az alkalmazás üzembe helyezése iOS-eszközön

Az iOS-eszköz csatlakoztatása a Mac számítógéphez, és az **aktív séma** beállítása az iOS-eszközre.

![Eszköz kiválasztása](./media/spatial-anchors-unity/select-device.png)

Válassza **a létrehozás lehetőséget, majd futtassa az aktuális sémát**.

![Üzembe helyezés és Futtatás](./media/spatial-anchors-unity/deploy-run.png)
