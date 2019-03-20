---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-app-configuration
author: yegu
ms.service: azure-app-configuration
ms.topic: include
ms.date: 01/22/2019
ms.author: yegu
ms.custom: include file
ms.openlocfilehash: 0f066915046e363932652c104fabe96ed0a9d4c4
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58227184"
---
1. Hozzon létre egy új alkalmazás-konfigurációs adattároló, jelentkezzen be a [az Azure portal](https://aka.ms/azconfig/portal). A lap bal felső sarkában válassza **+ erőforrás létrehozása**. Az a **keresés a piactéren** mezőbe írja be **Alkalmazáskonfiguráció** nyomja le az Enter billentyűt.

    ![Keresse meg az alkalmazás konfigurációja](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-new.png)

2. Válassza ki **Alkalmazáskonfiguráció** a keresési eredmények, és válassza ki a **létrehozás**.

3. Az a **Alkalmazáskonfiguráció** > **létrehozás** lapon, a következő beállításokat adja.

    | Beállítás | Ajánlott érték | Leírás |
    |---|---|---|
    | **Erőforrás neve** | Globálisan egyedi név | Adjon meg egy egyedi erőforráscsoport nevét az alkalmazás-konfigurációs tár erőforrást. A névnek 1 és 63 karakter közötti sztringnek kell lennie, és kizárólag számokat, betűket és a `-` karaktert tartalmazhatja. A név nem kezdődhet vagy végződhet a `-` karakterrel, és egymást követő `-` karakter nem érvényes.  |
    | **Előfizetés** | Az Ön előfizetése | Válassza ki az alkalmazás konfigurációjának tesztelése használni kívánt Azure-előfizetést. Ha a fiókja csak egyetlen előfizetéssel rendelkezik, a rendszer automatikusan kiválasztja és a **előfizetés** legördülő menü nem jelenik meg. |
    | **Erőforráscsoport** | *AppConfigTestResources* | Válassza ki vagy hozzon létre egy erőforráscsoportot az alkalmazás konfigurációs tároló-erőforrás. Ennek a csoportnak több erőforrást, amelyeket érdemes az erőforráscsoport törlésével egy időben törlése rendszerezésének szempontjából hasznos. További információkat az [Azure-erőforrások erőforráscsoportokkal való kezeléséről](/azure/azure-resource-manager/resource-group-overview) szóló cikkben olvashat. |
    | **Hely** | *USA középső RÉGIÓJA* | Használat **hely** , megadhatja a földrajzi helyet, ahol az alkalmazás a konfigurációs adattároló üzemel. A legjobb teljesítmény érdekében az alkalmazás más összetevőit ugyanabban a régióban az erőforrás létrehozásához. |

    ![Az alkalmazás konfigurációs tároló-erőforrás létrehozásához](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-create.png)

4. Kattintson a **Létrehozás** gombra. A telepítés néhány percet is igénybe vehet.

5. Az üzembe helyezés befejezése után jelölje ki a **beállítások** > **Tárelérési kulcsok**. Jegyezze fel vagy az elsődleges csak olvasható vagy elsődleges írási-olvasási kulcs kapcsolati karakterláncára. Használja ezt a kapcsolati karakterláncot később a létrehozott alkalmazás a konfigurációs adattároló folytatott kommunikációhoz az alkalmazás konfigurálásához.

6. Válassza ki **kulcs/érték Explorer** > **+ létrehozás** a következő kulcs-érték párok hozzáadásához:

    | Kulcs | Érték |
    |---|---|
    | TestApp:Settings:BackgroundColor | Fehér |
    | TestApp:Settings:FontSize | 24 |
    | TestApp:Settings:FontColor | Fekete |
    | TestApp:Settings:Message | Azure-alkalmazás-konfigurációs adatait |

    Hagyja **címke** és **tartalomtípus** most üres.
