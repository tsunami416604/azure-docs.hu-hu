---
title: Microsoft Azure Maps Creator kezelése (előzetes verzió)
description: Ebből a cikkből megtudhatja, hogyan kezelheti Microsoft Azure Maps Creatort (előzetes verzió).
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 1ed3a9033f9be39774e1c52982f63259cc477d29
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2020
ms.locfileid: "96906097"
---
# <a name="manage-azure-maps-creator-preview"></a>Azure Maps létrehozójának kezelése (előzetes verzió) 

> [!IMPORTANT]
> A Azure Maps Creator Services jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Maps létrehozója lehetővé teszi, hogy saját beltéri térképi adatkészletet hozzon létre. A Azure Maps API és a beltéri térképek modul használatával interaktív és dinamikus beltéri térképes webalkalmazásokat fejleszthet. Jelenleg a Creator csak az S1 árképzési szinten érhető el a Egyesült Államok.

Ebből a cikkből megtudhatja, hogyan hozhat létre és törölhet egy létrehozói erőforrást egy Azure Maps fiókban.

## <a name="create-creator-preview-resource"></a>Létrehozó (előzetes) erőforrás létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com)

2. Válassza ki a Azure Maps fiókját. Ha a **legutóbbi erőforrások** alatt nem látja a Azure Maps-fiókját, akkor navigáljon a Azure Portal menüre. Válassza a **Minden erőforrás** elemet. Keresse meg és válassza ki a Azure Maps fiókját.

    ![Azure Maps portál kezdőlapja](./media/how-to-manage-creator/select-maps-account.png)

3. Miután megtörtént a Azure Maps-fiók lapon, navigáljon az **Áttekintés** lehetőségre a **Creator** alatt. Azure Maps létrehozó erőforrás létrehozásához kattintson a  **Létrehozás**  gombra.

    ![Azure Maps létrehozó lap létrehozása](./media/how-to-manage-creator/creator-blade-settings.png)

4. Adja meg a létrehozó erőforrás nevét és helyét. Jelenleg a Creator csak a Egyesült Államok támogatott. Kattintson a **Felülvizsgálat + létrehozás** elemre.

   ![Adja meg a létrehozó fiókadatok oldalát](./media/how-to-manage-creator/creator-creation-dialog.png)

5. Tekintse át a beállításokat, és kattintson a **Létrehozás** gombra.

    ![Létrehozó fiók beállításainak megerősítése lap](./media/how-to-manage-creator/creator-create-dialog.png)

6. Ha az üzembe helyezés befejeződik, egy lap jelenik meg, amely sikeres vagy hibaüzenetet jelenít meg.

   ![Erőforrás központi telepítésének állapota lap](./media/how-to-manage-creator/creator-resource-created.png)

7. Kattintson az **Erőforrás megnyitása** lehetőségre. A létrehozó erőforrás nézet oldal megjeleníti a létrehozó erőforrás állapotát és a kiválasztott demográfiai régiót.

    ![Létrehozó állapota lap](./media/how-to-manage-creator/creator-resource-view.png)

   >[!NOTE]
   >A létrehozó erőforrás lapon visszatérhet a Azure Maps fiókhoz, amelyhez a Azure Maps fiókra kattint.

## <a name="delete-creator-preview-resource"></a>Létrehozó (előzetes) erőforrás törlése

A létrehozó erőforrás törléséhez navigáljon a Azure Maps-fiókjához. Válassza az **Áttekintés** lehetőséget a **létrehozó** területen. Kattintson a **Törlés** gombra.

>[!WARNING]
>Ha törli a Azure Maps-fiók létrehozó erőforrását, akkor a Creator Services használatával létrehozott adatkészleteket, tilesets és szolgáltatás-statesets is törli.

![Létrehozó lap törlés gombbal](./media/how-to-manage-creator/creator-delete.png)

Kattintson a **Törlés** gombra, és írja be a létrehozó nevét a törlés megerősítéséhez. Az erőforrás törlése után egy megerősítő oldal jelenik meg, például az alábbi képen:

![Létrehozó lap törlési megerősítéssel](./media/how-to-manage-creator/creator-confirmdelete.png)

## <a name="authentication"></a>Hitelesítés

A Creator (előzetes verzió) a Azure Maps Access Control (IAM) beállításait örökli. Az adathozzáféréshez szükséges API-hívásokat hitelesítési és engedélyezési szabályokkal kell elküldeni.

A létrehozói használati adatok a Azure Maps használati diagramokban és a tevékenység naplójában vannak beépítve.  További információ: [a hitelesítés kezelése Azure Mapsban](./how-to-manage-authentication.md).

## <a name="access-to-creator-services"></a>Hozzáférés a Creator szolgáltatásaihoz

A Creator Services (előzetes verzió) csak a létrehozáskor kiválasztott helyről érhető el. Ha a rendszer a kiválasztott helyen kívülről kezdeményezi a létrehozói szolgáltatásokat, a rendszer egy felhasználói hibaüzenetet küld vissza. Ha a kiválasztott helyen kívülről szeretne hívásokat kezdeményezni, a szolgáltatás URL-címének tartalmaznia kell a földrajzi előtagot a kiválasztott helyekhez. Ha például a létrehozót a Egyesült Államok hozza létre, a konverziós szolgáltatásnak küldött összes hívást el kell küldenie a következőnek: `us.atlas.microsoft.com/conversion/convert` .

Emellett a Creatorbe importált összes adathalmazt a létrehozó erőforrással megegyező földrajzi helyre kell feltölteni. Ha például az Creator ki van töltve az Egyesült-ban, az összes nyers adattal fel kell tölteni a-on keresztül `us.atlas.microsoft.com/mapData/upload` .

## <a name="next-steps"></a>Következő lépések

Bevezetés a Creator Services (előzetes verzió) szolgáltatásba a beltéri leképezéshez:

> [!div class="nextstepaction"]
> [Adatok feltöltése](creator-indoor-maps.md#upload-a-drawing-package)

> [!div class="nextstepaction"]
> [Adatátalakítás](creator-indoor-maps.md#convert-a-drawing-package)

> [!div class="nextstepaction"]
> [Adathalmaz](creator-indoor-maps.md#datasets)

> [!div class="nextstepaction"]
> [Tileset](creator-indoor-maps.md#tilesets)

> [!div class="nextstepaction"]
> [Szolgáltatás állapotának beállítása](creator-indoor-maps.md#feature-statesets)

Ismerje meg, hogyan használhatja a Creator Services (előzetes verzió) szolgáltatást beltéri térképek megjelenítéséhez az alkalmazásban:

> [!div class="nextstepaction"]
> [Azure Maps Creator oktatóanyag](tutorial-creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Beltéri Térkép dinamikus stílusa](indoor-map-dynamic-styling.md)

> [!div class="nextstepaction"]
> [A beltéri térképek modul használata](how-to-use-indoor-module.md)