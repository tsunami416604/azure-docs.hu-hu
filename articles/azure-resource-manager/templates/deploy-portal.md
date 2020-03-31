---
title: Erőforrások üzembe helyezése az Azure Portalon
description: Az Azure Portal és az Azure Resource Manage használatával üzembe helyezheti erőforrásait egy erőforráscsoportra az előfizetésében.
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: ea2faf810b3a5d1b8fa46575201022a501cc7d58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153437"
---
# <a name="deploy-resources-with-arm-templates-and-azure-portal"></a>Erőforrások üzembe helyezése ARM-sablonokkal és Azure Portalon

Ismerje meg, hogyan használhatja az Azure [Resource Manager (ARM) sablonjait](overview.md) az [Azure-erőforrások](https://portal.azure.com) üzembe helyezéséhez. Az erőforrások kezeléséről az [Azure-erőforrások kezelése az Azure Portal használatával című témakörben](../management/manage-resources-portal.md)olvashat.

Az Azure-erőforrások üzembe helyezése az Azure Portal használatával általában két lépésből áll:

- Hozzon létre egy erőforráscsoportot.
- Erőforrások üzembe helyezése az erőforráscsoportba.

Emellett egy ARM-sablont is üzembe helyezhet az Azure-erőforrások létrehozásához.

Ez a cikk mindkét módszert bemutatja.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

1. Új erőforráscsoport létrehozásához válassza az **Erőforráscsoportok lehetőséget** az [Azure Portalon.](https://portal.azure.com)

   ![Erőforráscsoportok kijelölése](./media/deploy-portal/select-resource-groups.png)

1. Az Erőforráscsoportok csoportban válassza a **Hozzáadás**lehetőséget.

   ![Erőforráscsoport hozzáadása](./media/deploy-portal/add-resource-group.png)

1. Jelölje be vagy írja be a következő tulajdonságértékeket:

    - **Előfizetés**: Válasszon egy Azure-előfizetést.
    - **Erőforráscsoport**: Adjon nevet az erőforráscsoportnak.
    - **Régió**: Adjon meg egy Azure-helyet. Ez az a hely, ahol az erőforráscsoport metaadatokat tárol az erőforrásokról. Megfelelőségi okokból érdemes megadni, hogy hol tárolja a metaadatokat. Általában azt javasoljuk, hogy adjon meg egy helyet, ahol a legtöbb erőforrás található. Ugyanannak a helynek a használata leegyszerűsítheti a sablont.

   ![Csoportértékek beállítása](./media/deploy-portal/set-group-properties.png)

1. Válassza az **Áttekintés + létrehozás** lehetőséget.
1. tekintse át az értékeket, majd válassza a **Létrehozás gombot.**
1. Válassza a **Frissítés** lehetőséget, mielőtt az új erőforráscsoportot meglátná a listában.

## <a name="deploy-resources-to-a-resource-group"></a>Erőforrások telepítése erőforráscsoportba

Miután létrehozott egy erőforráscsoportot, erőforrásokat helyezhet üzembe a csoportba a Piactérről. A Piactér előre definiált megoldásokat biztosít a gyakori forgatókönyvekhez.

1. Központi telepítés indításához válassza az **Erőforrás létrehozása** az [Azure Portalon](https://portal.azure.com)lehetőséget.

   ![Új erőforrás](./media/deploy-portal/new-resources.png)

1. Keresse meg a telepíteni kívánt erőforrás típusát. Az erőforrások kategóriákba vannak rendezve. Ha nem látja az adott megoldást, amelyet telepíteni szeretne, kereshet a Marketplace-en. A következő képernyőkép azt mutatja, hogy az Ubuntu Server van kiválasztva.

   ![Erőforrástípus kiválasztása](./media/deploy-portal/select-resource-type.png)

1. A kiválasztott erőforrás típusától függően a telepítés előtt be kell állítania a megfelelő tulajdonságok gyűjteményét. Minden típushoz ki kell választania egy cél erőforráscsoportot. Az alábbi képen bemutatja, hogyan hozhat létre egy Linux-virtuális gépet, és hogyan telepítheti azt a létrehozott erőforráscsoportba.

   ![Erőforráscsoport létrehozása](./media/deploy-portal/select-existing-group.png)

   Másik lehetőségként dönthet úgy, hogy az erőforrások üzembe helyezésekor létrehoz egy erőforráscsoportot. Válassza **az Új létrehozása lehetőséget,** és adjon nevet az erőforráscsoportnak.

1. Megkezdődik a telepítés. A telepítés több percig is eltarthat. Egyes erőforrások hosszabb időt vesznek igénybe, mint más erőforrások. Amikor a központi telepítés befejeződött, megjelenik egy értesítés. A megnyitáshoz válassza **az Ugrás az erőforrásra** lehetőséget.

   ![Értesítés megtekintése](./media/deploy-portal/view-notification.png)

1. Az erőforrások üzembe helyezése után további erőforrásokat adhat hozzá az erőforráscsoporthoz a **Hozzáadás**gombra kattintva.

   ![Erőforrás hozzáadása](./media/deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>Erőforrások üzembe helyezése egyéni sablonból

Ha egy központi telepítést szeretne végrehajtani, de nem használja a sablonokat a Marketplace-en, létrehozhat egy testreszabott sablont, amely meghatározza a megoldás infrastruktúráját. A sablonok létrehozásáról az [ARM-sablonok szerkezetének és szintaxisának megismerése](template-syntax.md)című témakörben olvashat.

> [!NOTE]
> A portálfelület nem támogatja a [Key Vault ból származó titkos hivatkozásokat.](key-vault-parameter.md) Ehelyett használja a [PowerShell](deploy-powershell.md) vagy [az Azure CLI](deploy-cli.md) a sablon helyi vagy külső URI-ból való üzembe helyezéséhez.

1. Ha testre szabott sablont szeretne telepíteni a portálon keresztül, válassza az **Erőforrás létrehozása**lehetőséget, és keresse meg a **sablont.** majd válassza **a Sablon telepítése**lehetőséget.

   ![Keresési sablon központi telepítése](./media/deploy-portal/search-template.png)

1. Kattintson a **Létrehozás** gombra.
1. A sablon létrehozásának számos lehetősége látható:

    - **Készítsd el a saját sablonszerkesztő:** hozzon létre egy sablont a portál sablon szerkesztő.  A szerkesztő erőforrássablon-sémát adhat hozzá.
    - **Közös sablonok:** Négy közös sablonok egy Linux virtuális gép, Windows virtuális gép, egy webalkalmazás és egy Azure SQL-adatbázis létrehozásához.
    - **GitHub-rövid útmutató sablon betöltése:** meglévő [rövid útmutató sablonok](https://azure.microsoft.com/resources/templates/)használata .

   ![Beállítások megtekintése](./media/deploy-portal/see-options.png)

    Ez az oktatóanyag a rövid útmutató sablon betöltésére vonatkozó utasítást tartalmazza.

1. A **GitHub gyorsindítási sablonjának betöltése**csoportban írja be vagy válassza a **101 tárfiók-létrehozás parancsot.**

    Erre két lehetősége van:

    - **Sablon kiválasztása**: a sablon telepítése.
    - **Sablon szerkesztése:** a gyorsútmutató sablon szerkesztése a telepítés előtt.

1. A portálsablon-szerkesztő felfedezéséhez válassza a **Sablon szerkesztése** lehetőséget. A sablon betöltődik a szerkesztőbe. Figyelje meg, hogy két paraméter van: **storageAccountType** és **location**.

   ![Sablon létrehozása](./media/deploy-portal/show-json.png)

1. Kisebb módosítást kell elkészíteni a sablonon. Például frissítse a **storageAccountName** változót a következőre:

    ```json
    "storageAccountName": "[concat('azstore', uniquestring(resourceGroup().id))]"
    ```

1. Kattintson a **Mentés** gombra. Most már láthatja a portálsablon telepítési felületét. Figyelje meg a sablonban megadott két paramétert.
1. Adja meg vagy jelölje ki a tulajdonságértékeket:

    - **Előfizetés**: Válasszon egy Azure-előfizetést.
    - **Erőforráscsoport**: Válassza **az Új létrehozása** lehetőséget, és adjon nevet.
    - **Hely**: Válasszon egy Azure-helyet.
    - **Tárfiók típusa**: Használja az alapértelmezett értéket.
    - **Hely**: Használja az alapértelmezett értéket.
    - **Elfogadom a fenti feltételeket és kikötéseket**: (kiválasztás)

1. Válassza a **Beszerzés** lehetőséget.

## <a name="next-steps"></a>További lépések

- A naplók megtekintéséhez olvassa el a [Naplózási műveletek az Erőforrás-kezelővel](../management/view-activity-logs.md).
- A telepítési hibák elhárításához olvassa el a Telepítési műveletek megtekintése című [témakört.](deployment-history.md)
- Ha központi telepítésből vagy erőforráscsoportból szeretne sablont exportálni, olvassa el az [ARM-sablonok exportálása (Arm- sablonok) témakört.](export-template-portal.md)
- Ha biztonságosan szeretné elvonni a szolgáltatást több régióban, olvassa el az [Azure Deployment Manager](deployment-manager-overview.md).
