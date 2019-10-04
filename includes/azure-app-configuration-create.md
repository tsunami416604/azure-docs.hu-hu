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
ms.openlocfilehash: c98a17be394887ef4e008b079467c85d4ded7e09
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393305"
---
1. Hozzon létre egy új alkalmazás-konfigurációs adattároló, jelentkezzen be a [az Azure portal](https://portal.azure.com). Az ablaktábla bal felső sarkában válassza **+ erőforrás létrehozása**. Az a **keresés a piactéren** mezőbe írja be **Alkalmazáskonfiguráció** , és nyomja le az Enter.

    ![Keresse meg az alkalmazás konfigurációja](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-new.png)

1. Válassza ki **Alkalmazáskonfiguráció** a keresési eredmények, és válassza ki a **létrehozás**.

1. Az a **Alkalmazáskonfiguráció** > **létrehozás** panelen adja meg a következő beállításokat:

    | Beállítás | Ajánlott érték | Leírás |
    |---|---|---|
    | **Erőforrás neve** | Globálisan egyedi név | Adjon meg egy egyedi erőforráscsoport nevét az alkalmazás konfigurációs tár erőforrás. A névnek 1 és 63 karakter közötti sztringnek kell lennie, és kizárólag számokat, betűket és a `-` karaktert tartalmazhatja. A név nem kezdődhet vagy végződhet a `-` karakterrel, és egymást követő `-` karakter nem érvényes.  |
    | **Előfizetés** | Az Ön előfizetése | Válassza ki az alkalmazás konfigurációjának tesztelése használni kívánt Azure-előfizetést. Ha a fiókja csak egyetlen előfizetéssel rendelkezik, a rendszer automatikusan kiválasztja és a **előfizetés** lista nem jelenik meg. |
    | **Erőforráscsoport** | *AppConfigTestResources* | Válassza ki vagy hozzon létre egy erőforráscsoportot az Alkalmazáskonfigurációt tároló-erőforrás. Ennek a csoportnak több erőforrást, amelyeket érdemes az erőforráscsoport törlésével egy időben törlése rendszerezésének szempontjából hasznos. További információkat az [Azure-erőforrások erőforráscsoportokkal való kezeléséről](/azure/azure-resource-manager/resource-group-overview) szóló cikkben olvashat. |
    | **Hely** | *USA középső RÉGIÓJA* | Használat **hely** , megadhatja a földrajzi helyet, ahol az alkalmazás a konfigurációs adattároló üzemel. A legjobb teljesítmény érdekében az alkalmazás más összetevőit ugyanabban a régióban az erőforrás létrehozásához. |

    ![Áruházbeli alkalmazás konfigurációs erőforrás létrehozása](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-create.png)

1. Kattintson a **Létrehozás** gombra. A telepítés néhány percet is igénybe vehet.

1. Az üzembe helyezés befejezése után jelölje ki a **beállítások** > **Tárelérési kulcsok**. Jegyezze fel vagy az elsődleges csak olvasható vagy elsődleges írási-olvasási kulcs kapcsolati karakterláncára. Ez a kapcsolati karakterlánc később fogja használni a kommunikációhoz az alkalmazás a konfigurációs adattároló létrehozott alkalmazás konfigurálása.
