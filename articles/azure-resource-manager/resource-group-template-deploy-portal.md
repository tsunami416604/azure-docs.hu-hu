---
title: Az Azure-erőforrások üzembe helyezése az Azure portal használatával |} A Microsoft Docs
description: Az Azure portal és az Azure Resource Manager használatával helyezheti üzembe az erőforrásokat.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: tomfitz
ms.openlocfilehash: a171d9b4f054c942eebb08e7e11dd1164545f408
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67460330"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-portal"></a>Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure Portallal

Ismerje meg, hogyan használható a [az Azure portal](https://portal.azure.com) a [Azure Resource Manager](resource-group-overview.md) az Azure-erőforrások üzembe helyezéséhez. Az erőforrások kezelésével kapcsolatos további információkért lásd: [Azure-erőforrások kezelése az Azure portal használatával](manage-resources-portal.md).

Általában az Azure portal segítségével Azure-erőforrások telepítése két lépésből áll:

- Hozzon létre egy erőforráscsoportot.
- Erőforrások üzembe helyezése az erőforráscsoporthoz.

Emellett az Azure Resource Manager-sablon létrehozása az Azure-erőforrások is telepítheti.

Ez a cikk mindkét módszert bemutatja.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

1. Hozzon létre egy új erőforráscsoportot, válassza a **erőforráscsoportok** származó a [az Azure portal](https://portal.azure.com).

   ![Erőforráscsoportok kijelölése](./media/resource-group-template-deploy-portal/select-resource-groups.png)

1. Erőforráscsoportok, válassza a **Hozzáadás**.

   ![erőforráscsoport hozzáadása](./media/resource-group-template-deploy-portal/add-resource-group.png)

1. Válassza ki vagy adja meg az alábbi tulajdonságértékeit:

    - **Előfizetés**: Válasszon ki egy Azure-előfizetést.
    - **Erőforráscsoport**: Nevezze el az erőforráscsoportot.
    - **Régió**: Adja meg az Azure-helyen. Ez az, ahol az erőforráscsoport erőforrásokra vonatkozó metaadatokat tárol-e. Megfelelőségi okokból érdemes lehet, hogy ezekhez a metaadatokhoz helyének meghatározásához. Általában azt javasoljuk, hogy megad egy olyan helyre, ahol az erőforrások legnagyobb része helyezkednek el. Ugyanazon a helyen használatával egyszerűsítheti a sablon.

   ![Csoport értékek beállítása](./media/resource-group-template-deploy-portal/set-group-properties.png)

1. Válassza az **Áttekintés + létrehozás** lehetőséget.
1. Tekintse át az értékeket, és válassza ki **létrehozás**.
1. Válassza ki **frissítése** ahhoz, hogy az új erőforráscsoport, a listában.

## <a name="deploy-resources-to-a-resource-group"></a>Erőforrások üzembe helyezése egy erőforráscsoportot

Miután létrehozott egy erőforráscsoport, a Marketplace-ről telepítheti erőforrások a csoport. A Marketplace-en előre definiált megoldásokat kínál a gyakori forgatókönyvek.

1. Válassza ki a központi telepítés indításához **erőforrás létrehozása** a a [az Azure portal](https://portal.azure.com).

   ![Új erőforrás](./media/resource-group-template-deploy-portal/new-resources.png)

1. Keresse meg a telepíteni kívánt erőforrás típusát. Az erőforrások kategóriákba vannak rendezve. Ha nem látja az adott megoldás, amelyet szeretne telepíteni, a Marketplace-en, kereshet. Az alábbi képernyőképen látható, hogy Ubuntu Server van kiválasztva.

   ![Erőforrás típusának kiválasztása](./media/resource-group-template-deploy-portal/select-resource-type.png)

1. A kiválasztott erőforrás típusától függően, hogy a központi telepítés előtt állítsa be a releváns tulajdonságok gyűjteménye. Minden alkalmazástípus esetében jelöljön ki egy cél erőforráscsoport. Az alábbi képen egy Linux virtuális gép létrehozása és üzembe helyezése az Ön által létrehozott erőforráscsoportot.

   ![Erőforráscsoport létrehozása](./media/resource-group-template-deploy-portal/select-existing-group.png)

   Azt is megteheti Ha dönt, hogy az erőforrások üzembe helyezésekor, hozzon létre egy erőforráscsoportot. Válassza ki **új létrehozása** , és adja meg az erőforráscsoport nevét.

1. A központi telepítés megkezdődik. Az üzembe helyezés több percet is igénybe vehet. Bizonyos erőforrásokhoz, mint az egyéb erőforrások hosszabb időt vesz igénybe. Ha a telepítés véget ért, megjelenik egy értesítés. Válassza ki **erőforrás megnyitása** megnyitása

   ![Értesítés megtekintése](./media/resource-group-template-deploy-portal/view-notification.png)

1. Az erőforrások üzembe helyezése után is hozzáadhat további erőforrásokat az erőforráscsoport kiválasztásával **Hozzáadás**.

   ![Erőforrás hozzáadása](./media/resource-group-template-deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>Egyéni sablon erőforrások üzembe helyezése

Ha azt szeretné, hajtsa végre a központi telepítés, de nem használja a sablon a Marketplace-en, létrehozhat egy testre szabott a megoldás infrastruktúráját meghatározó sablon. Sablonok létrehozásával kapcsolatos további információkért lásd: [struktúra és az Azure Resource Manager-sablonok szintaxisát](resource-group-authoring-templates.md).

> [!NOTE]
> A portál felület nem támogatja a hivatkozó egy [a Key vault titkos](resource-manager-keyvault-parameter.md). Ehelyett használjon [PowerShell](resource-group-template-deploy.md) vagy [Azure CLI-vel](resource-group-template-deploy-cli.md) helyben vagy egy külső URI-ból a sablon üzembe helyezéséhez.

1. A portálon keresztül testreszabott sablon üzembe helyezéséhez válassza **erőforrás létrehozása**, keressen **sablon**. majd **sablonalapú telepítés**.

   ![Keresés sablon telepítése](./media/resource-group-template-deploy-portal/search-template.png)

1. Kattintson a **Létrehozás** gombra.
1. Megjelenik a sablonok létrehozásának számos lehetőség közül választhat:

    - **A szerkesztőben saját sablon készítése**: hozzon létre egy sablont, a portál sablonnal szerkesztő.  A szerkesztő meg adjon hozzá egy erőforrás sablonséma kezelésére képes.
    - **Gyakran használt sablonok**: Nincsenek négy közös templatess Linux rendszerű virtuális gép, a Windows virtuális gép, a webalkalmazás és a egy Azure SQL database létrehozásához.
    - **GitHub gyorsindítási sablon betöltése**: egy meglévő [gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/).

   ![Beállítások megtekintése](./media/resource-group-template-deploy-portal/see-options.png)

    Ez az oktatóanyag utasításait követve a gyorsindítási sablon betöltése biztosít.

1. A **GitHub gyorsindítási sablon betöltése**adja meg **101-storage-fiók – create**.

    Erre két lehetősége van:

    - **Sablon kiválasztása**: helyezze üzembe a sablont.
    - **Sablon szerkesztése**: a gyorsindítási sablon szerkesztése a telepítése előtt.

1. Válassza ki **szerkesztési sablon** megismerheti a portál sablon szerkesztőben. A sablon betöltése a szerkesztőben. Figyelje meg, hogy van két paramétert: **Tárfióktípus** és **hely**.

   ![Sablon létrehozása](./media/resource-group-template-deploy-portal/show-json.png)

1. Egy kis módosítást a sablonhoz. Ha például frissítése a **storageAccountName** változót:

    ```json
    "storageAccountName": "[concat('azstore', uniquestring(resourceGroup().id))]"
    ```

1. Kattintson a **Mentés** gombra. Most már megjelenik a portál sablon üzembe helyezési felület. Figyelje meg, hogy a két paramétert, amelyek definiálva a sablonban.
1. Adja meg, vagy válassza ki az indexattribútumokat:

    - **Előfizetés**: Válasszon ki egy Azure-előfizetést.
    - **Erőforráscsoport**: Válassza ki **új létrehozása** és adjon meg egy nevet.
    - **Hely**: Válasszon egy Azure-beli helyet.
    - **Tárfiók típusa**: Használja az alapértelmezett értéket.
    - **Hely**: Használja az alapértelmezett értéket.
    - **Elfogadom a fenti feltételeket és kikötéseket**: (kiválasztás)

1. Válassza a **Beszerzés** lehetőséget.

## <a name="next-steps"></a>További lépések

- Auditnaplók megtekintése: [auditálási műveletek a Resource Manager](./resource-group-audit.md).
- Telepítési hibák elhárításához tekintse meg a [üzembehelyezési műveletek megtekintése](./resource-manager-deployment-operations.md).
- Sablon exportálása egy üzembe helyezési vagy az erőforráscsoport, lásd: [exportálása az Azure Resource Manager-sablonok](./manage-resource-groups-portal.md#export-resource-groups-to-templates).
- Biztonságosan számára, hogy a szolgáltatás több régióban, lásd: [Azure Deployment Manager](./deployment-manager-overview.md).
