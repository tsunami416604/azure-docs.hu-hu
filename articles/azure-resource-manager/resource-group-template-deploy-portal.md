---
title: Az Azure-erőforrások üzembe helyezése az Azure portal használatával |} A Microsoft Docs
description: Az Azure portal és az Azure Resource Manager használatával helyezheti üzembe az erőforrásokat.
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: tfitzmac
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: tomfitz
ms.openlocfilehash: 0711743d46aaac08f8572917d91e452ee919134c
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56816902"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-portal"></a>Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure Portallal

Ez a cikk bemutatja, hogyan használhatja a [az Azure portal](https://portal.azure.com) a [Azure Resource Manager](resource-group-overview.md) az Azure-erőforrások üzembe helyezéséhez. Az erőforrások kezelésével kapcsolatos további információkért lásd: [Azure-erőforrások kezelése az Azure portal használatával](manage-resources-portal.md).

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

1. Üres erőforráscsoport létrehozásához válassza **erőforráscsoportok**.

   ![Erőforráscsoportok kijelölése](./media/resource-group-template-deploy-portal/select-resource-groups.png)

1. Erőforráscsoportok, válassza a **Hozzáadás**.

   ![Erőforráscsoport hozzáadása](./media/resource-group-template-deploy-portal/add-resource-group.png)

1. Adjon meg egy nevet és egy helyet, és szükség esetén válasszon ki egy előfizetést. Meg kell adnia az erőforráscsoport helyét, mert az erőforráscsoport erőforrásokra vonatkozó metaadatokat tárol. Megfelelőségi okokból érdemes lehet, hogy ezekhez a metaadatokhoz helyének meghatározásához. Általában azt javasoljuk, hogy megad egy olyan helyre, ahol az erőforrások legnagyobb része helyezkednek el. Ugyanazon a helyen használatával egyszerűsítheti a sablon.

   ![Csoport értékek beállítása](./media/resource-group-template-deploy-portal/set-group-properties.png)

   A tulajdonságok beállítása után, válassza ki a **létrehozás**.

1. Az új erőforráscsoport megtekintéséhez válasszon **frissítése**.

   ![Erőforráscsoport frissítése](./media/resource-group-template-deploy-portal/refresh-resource-groups.png)

## <a name="deploy-resources-from-marketplace"></a>Piactéri erőforrások üzembe helyezése

Miután létrehozott egy erőforráscsoport, a Marketplace-ről erőforrások telepítheti azt. A Marketplace-en előre definiált megoldásokat kínál a gyakori forgatókönyvek.

1. Válassza ki a központi telepítés indításához **erőforrás létrehozása**.

   ![Új erőforrás](./media/resource-group-template-deploy-portal/new-resources.png)

1. Keresse meg a telepíteni kívánt erőforrás típusát.

   ![Erőforrás típusának kiválasztása](./media/resource-group-template-deploy-portal/select-resource-type.png)

1. Ha nem látja az adott megoldás, amelyet szeretne telepíteni, a Marketplace-en, kereshet. Például Wordpress megoldást keres, kezdje a beírni **Wordpress** , és válassza ki a kívánt beállítást.

   ![A piactéren](./media/resource-group-template-deploy-portal/search-resource.png)

1. A kiválasztott erőforrás típusától függően, hogy a központi telepítés előtt állítsa be a releváns tulajdonságok gyűjteménye. Minden alkalmazástípus esetében jelöljön ki egy cél erőforráscsoport. A következő kép bemutatja, hogyan webes alkalmazás létrehozása és üzembe helyezése az Ön által létrehozott erőforráscsoportot.

   ![Erőforráscsoport létrehozása](./media/resource-group-template-deploy-portal/select-existing-group.png)

   Azt is megteheti Ha dönt, hogy az erőforrások üzembe helyezésekor, hozzon létre egy erőforráscsoportot. Válassza ki **új létrehozása** , és adja meg az erőforráscsoport nevét.

   ![Új erőforráscsoport létrehozása](./media/resource-group-template-deploy-portal/select-new-group.png)

1. A központi telepítés megkezdődik. Az üzembe helyezés eltarthat néhány percig. Ha a telepítés véget ért, megjelenik egy értesítés.

   ![Értesítés megtekintése](./media/resource-group-template-deploy-portal/view-notification.png)

1. Az erőforrások üzembe helyezése után is hozzáadhat további erőforrásokat az erőforráscsoport kiválasztásával **Hozzáadás**.

   ![Erőforrás felvétele](./media/resource-group-template-deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>Egyéni sablon erőforrások üzembe helyezése

Ha azt szeretné, hajtsa végre a központi telepítés, de nem használja a sablon a Marketplace-en, létrehozhat egy testre szabott a megoldás infrastruktúráját meghatározó sablon. Sablonok létrehozásával kapcsolatos további információkért lásd: [struktúra és az Azure Resource Manager-sablonok szintaxisát](resource-group-authoring-templates.md).

> [!NOTE]
> A portál felület nem támogatja a hivatkozó egy [a Key vault titkos](resource-manager-keyvault-parameter.md). Ehelyett használjon [PowerShell](resource-group-template-deploy.md) vagy [Azure CLI-vel](resource-group-template-deploy-cli.md) helyben vagy egy külső URI-ból a sablon üzembe helyezéséhez.

1. A portálon keresztül testreszabott sablon üzembe helyezéséhez válassza **erőforrás létrehozása**, és keressen rá a **sablonalapú telepítés** mindaddig, amíg a lehetőségek választhatók ki.

   ![Keresés sablon telepítése](./media/resource-group-template-deploy-portal/search-template.png)

1. Kattintson a **Létrehozás** gombra.

   ![Kattintás a Létrehozás gombra](./media/resource-group-template-deploy-portal/show-template-option.png)

1. Megjelenik a sablonok létrehozásának számos lehetőség közül választhat. Válassza a **Saját sablon készítése a szerkesztőben** lehetőséget.

   ![Beállítások megtekintése](./media/resource-group-template-deploy-portal/see-options.png)

1. Üres sablon testreszabásához elérhető van.

   ![Sablon létrehozása](./media/resource-group-template-deploy-portal/blank-template.png)

1. Manuálisan szerkessze a JSON-szintaxist, vagy válasszon ki egy előre elkészített sablont a a [gyorsindítási sablon galéria](https://azure.microsoft.com/resources/templates/). Ebben a cikkben használhatja azonban a **erőforrás hozzáadása** lehetőséget.

   ![Sablon szerkesztése](./media/resource-group-template-deploy-portal/select-add-resource.png)

1. Válassza ki **tárfiók** és adjon meg egy nevet. Amikor végzett az értékek megadásával, válassza ki a **OK**.

   ![Adattároló fiók kiválasztása](./media/resource-group-template-deploy-portal/add-storage-account.png)

1. A szerkesztő JSON automatikusan hozzáadja az erőforrástípushoz. Figyelje meg, hogy az tartalmaz egy paraméter meghatározása a tárfiók típusát. Kattintson a **Mentés** gombra.

   ![Sablon megjelenítése](./media/resource-group-template-deploy-portal/show-json.png)

1. Most már lehetősége van definiálva a sablonban az erőforrásokat üzembe kívánja. Telepíthet, fogadja el a feltételeket és kikötéseket, és válassza ki **beszerzési**.

   ![Sablon üzembe helyezése](./media/resource-group-template-deploy-portal/provide-custom-template-values.png)

## <a name="deploy-resources-from-a-template-saved-to-your-account"></a>A sablon fiókjába erőforrások üzembe helyezése

A portál lehetővé teszi, hogy a sablon mentése az Azure-fiókjába, és telepítse újra később. A sablonok további információkért lásd: [hozzon létre és helyezhet üzembe az első Azure Resource Manager-sablon](resource-manager-create-first-template.md).

1. A mentett sablonok megkereséséhez válassza ki a **további szolgáltatások**.

   ![További szolgáltatások](./media/resource-group-template-deploy-portal/more-services.png)

1. Keresse meg **sablonok** , és válassza ezt a lehetőséget.

   ![Sablonok keresése](./media/resource-group-template-deploy-portal/find-templates.png)

1. Fiókjába sablonok listájából válassza ki a kívánt dolgozhat.

   ![Mentett sablonok](./media/resource-group-template-deploy-portal/saved-templates.png)

1. Válassza ki **telepítés** újbóli üzembe helyezéséhez a mentett sablonnak.

   ![Mentett sablon üzembe helyezése](./media/resource-group-template-deploy-portal/deploy-saved-template.png)

## <a name="next-steps"></a>További lépések
* Auditnaplók megtekintése: [auditálási műveletek a Resource Manager](./resource-group-audit.md).
* Telepítési hibák elhárításához tekintse meg a [üzembehelyezési műveletek megtekintése](./resource-manager-deployment-operations.md).
* Sablon exportálása egy üzembe helyezési vagy az erőforráscsoport, lásd: [exportálása az Azure Resource Manager-sablonok](./manage-resource-groups-portal.md#export-resource-groups-to-templates).
* A szolgáltatás több régióban, tekintse meg biztonságosan bevezetési [Azure Deployment Manager](./deployment-manager-overview.md).
