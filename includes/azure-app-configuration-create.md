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
ms.openlocfilehash: 71e63de247e05a4712687354ed548219b36e8f2f
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56884863"
---
1. Hozzon létre egy új alkalmazás-konfigurációs adattároló, először jelentkezzen be a [az Azure portal](https://aka.ms/azconfig/portal). Kattintson az **+ Erőforrás létrehozása** elemre az oldal bal felső oldalán. Az a **keresés a piactéren** szövegmezőbe írja be **Alkalmazáskonfiguráció** nyomja le az ENTER **Enter**.

    ![Keresse meg az alkalmazás konfigurációja](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-new.png)

2. Kattintson a **Alkalmazáskonfiguráció** a keresési eredmények között, majd **létrehozás**.

3. Az a **Alkalmazáskonfiguráció** > **létrehozás** lap, adja meg a következő beállításokat:

    | Beállítás | Ajánlott érték | Leírás |
    |---|---|---|
    | **Erőforrás neve** | Globálisan egyedi név | Adjon meg egy egyedi erőforráscsoport nevét az alkalmazás-konfigurációs tár erőforrást. A névnek 1 és 63 karakter közötti sztringnek kell lennie, és kizárólag számokat, betűket és a `-` karaktert tartalmazhatja. A név nem kezdődhet a `-` karakterrel, és az egymást követő `-` karakterek nem érvényesek.  |
    | **Előfizetés** | Az Ön előfizetése | Válassza ki az alkalmazás konfigurációjának tesztelése használni kívánt Azure-előfizetést. Ha a fiókja csak egyetlen előfizetéssel rendelkezik, automatikusan ez lesz kiválasztva, és az **Előfizetés** legördülő lista nem jelenik meg. |
    | **Erőforráscsoport** | *AppConfigTestResources* | Válassza ki vagy hozzon létre egy erőforráscsoportot az alkalmazás konfigurációs tároló-erőforrás. Ez a csoport több erőforrás rendszerezéséhez hasznos, amelyeket az erőforráscsoport törlésekor egyszerre törölhet. További információk: [Using Resource groups to manage your Azure resources](/azure/azure-resource-manager/resource-group-overview) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez). |
    | **Hely** | *USA középső RÉGIÓJA* | Használat **hely** , megadhatja a földrajzi helyet, ahol az alkalmazás a konfigurációs adattároló üzemel. A legjobb teljesítmény érdekében azt javasoljuk, hogy az erőforrást ugyanabban a régióban hozza létre, mint az alkalmazás többi összetevőjét. |

    ![Az alkalmazás konfigurációs tároló-erőforrás létrehozásához](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-create.png)

4. Kattintson a **Create** (Létrehozás) gombra. Az üzembe helyezés befejezése eltarthat néhány percig.

5. Ha a telepítés befejeződött, kattintson a **beállítások** > **Tárelérési kulcsok**. Jegyezze fel a bármelyik elsődleges csak olvasható vagy elsődleges írási-olvasási kulcs kapcsolati karakterláncára. Használhatjuk a ez később az imént létrehozott alkalmazás a konfigurációs adattároló folytatott kommunikációhoz az alkalmazás konfigurálása.

6. Kattintson a **kulcs/érték Explorer** és **+ létrehozás** a következő kulcs-érték párok hozzáadásához:

    | Kulcs | Érték |
    |---|---|
    | TestApp:Settings:BackgroundColor | Fehér |
    | TestApp:Settings:FontSize | 24 |
    | TestApp:Settings:FontColor | Fekete |
    | TestApp:Settings:Message | Azure-alkalmazás-konfigurációs adatait |

    Hagyja meg **címke** és **tartalomtípus** most üres.
