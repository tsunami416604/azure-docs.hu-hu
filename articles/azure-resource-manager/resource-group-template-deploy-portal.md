---
title: Azure-erőforrások telepítése Azure portál használatával |} Microsoft Docs
description: Azure-portál és az Azure Resource Manager segítségével az erőforrások telepítése.
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2017
ms.author: tomfitz
ms.openlocfilehash: 7da04f5561615c2e05821d5ef52a3653cb08fda5
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-portal"></a>Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure Portallal

Ez a témakör bemutatja, hogyan használja a [Azure-portálon](https://portal.azure.com) rendelkező [Azure Resource Manager](resource-group-overview.md) központi telepítése az Azure-erőforrások. Az erőforrások kezelésével kapcsolatos információkért lásd: [kezelése Azure-erőforrások portálon keresztül](resource-group-portal.md).

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

1. Hozzon létre egy üres erőforráscsoportot, válassza ki **erőforráscsoportok**.

   ![Erőforráscsoportok kijelölése](./media/resource-group-template-deploy-portal/select-resource-groups.png)

1. Az erőforráscsoportok, válassza ki a **Hozzáadás**.

   ![csoport hozzáadása](./media/resource-group-template-deploy-portal/add-resource-group.png)

1. Adjon neki egy nevet és egy helyet, és szükség esetén válasszon egy előfizetést. Meg kell adnia az erőforrásnak a helyét, mert az erőforráscsoport erőforrásokra vonatkozó metaadatokat tárol. Megfelelőségi okokból érdemes lehet, hogy metaadatai tárolási helyének meghatározásához. Általában javasoljuk, hogy megadja a helyét, amelyben az erőforrások többségét tárolva lesz. Ugyanazon a helyen használatával egyszerűbbé teheti a sablont.

   ![csoport értékeket](./media/resource-group-template-deploy-portal/set-group-properties.png)

   Válassza a tulajdonságok beállítása után, **létrehozása**.

1. Az új erőforráscsoport megtekintéséhez válasszon **frissítése**.

   ![Erőforráscsoport-sablonok frissítése](./media/resource-group-template-deploy-portal/refresh-resource-groups.png)

## <a name="deploy-resources-from-marketplace"></a>Erőforrások a piactér telepítése

Miután létrehozott egy erőforráscsoport, a piactérről erőforrások telepítheti azt. A piactér előre definiált megoldást nyújt a gyakori forgatókönyvek.

1. A telepítés elindításához válassza ki a **hozzon létre egy erőforrást**.

   ![Új erőforrás](./media/resource-group-template-deploy-portal/new-resources.png)

1. Keresse meg a telepíteni kívánt erőforrás típusát.

   ![Válassza ki az erőforrás típusa](./media/resource-group-template-deploy-portal/select-resource-type.png)

1. Ha nem látja az adott megoldás szeretne telepíteni, akkor is kereshet a piactér azt. Például Wordpress megoldást találni, indítsa el beírni **Wordpress** válassza ki a kívánt beállítást.

   ![a piactéren](./media/resource-group-template-deploy-portal/search-resource.png)

1. Attól függően, hogy a kiválasztott erőforrás típusát hogy egy központi telepítés előtt állítsa be a megfelelő tulajdonságok gyűjteménye. Az összes ki kell választania a célként megadott erőforráscsoport. A következő kép bemutatja, hogyan hozzon létre egy webalkalmazást, és telepítse azt a létrehozott erőforráscsoportot.

   ![Erőforráscsoport létrehozása](./media/resource-group-template-deploy-portal/select-existing-group.png)

   Azt is megteheti Ha dönt, hogy hozzon létre egy erőforráscsoportot az erőforrások telepítése során. Válassza ki **hozzon létre új** , és nevezze el az erőforráscsoportot.

   ![Új erőforráscsoport létrehozása](./media/resource-group-template-deploy-portal/select-new-group.png)

1. A telepítés megkezdése. A központi telepítés eltarthat néhány percig. A telepítés befejezését, megjelenik egy értesítés.

   ![értesítési nézet](./media/resource-group-template-deploy-portal/view-notification.png)

1. Az erőforrások való telepítése után adhat hozzá további erőforrásokat az erőforráscsoport kiválasztásával **Hozzáadás**.

   ![Erőforrás felvétele](./media/resource-group-template-deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>Az egyéni sablont az erőforrások telepítése

Ha szeretné a központi telepítés hajtható végre, de nem használja a sablonok a piactéren, létrehozhat egy egyéni sablont, amely meghatározza a megoldás infrastruktúráját. Sablonok létrehozásával kapcsolatos további tudnivalókért lásd: [megérteni a felépítését és Azure Resource Manager-sablonok szintaxisát](resource-group-authoring-templates.md).

1. Egy egyéni sablon a portálon keresztül történő üzembe helyezéséhez válassza **hozzon létre egy erőforrást**, keresse meg a **sablon-üzembehelyezés** mindaddig, amíg a lehetőségek közül választhat.

   ![keresési sablon-üzembehelyezés](./media/resource-group-template-deploy-portal/search-template.png)

1. Kattintson a **Létrehozás** gombra.

   ![Válassza ki létrehozása](./media/resource-group-template-deploy-portal/show-template-option.png)

1. Megjelenik a sablonok létrehozásának számos lehetőség közül választhat. Válassza ki **a szerkesztőben saját sablon létrehozása**.

   ![Beállítások megtekintése](./media/resource-group-template-deploy-portal/see-options.png)

1. Rendelkezik egy üres sablont, amelyet a testreszabása.

   ![Sablon létrehozása](./media/resource-group-template-deploy-portal/blank-template.png)

1. Manuálisan szerkessze a JSON-szintaxis, vagy válasszon ki egy előre elkészített sablon a [gyorsindítási galéria-sablon](https://azure.microsoft.com/resources/templates/). Ebben a cikkben használhatja azonban a **erőforrás hozzáadása** lehetőséget.

   ![Sablon szerkesztése](./media/resource-group-template-deploy-portal/select-add-resource.png)

1. Válassza ki **tárfiók** , és adjon meg egy nevet. Amikor befejeződött az értékek biztosítása, válassza ki a **OK**.

   ![Adattároló fiók kiválasztása](./media/resource-group-template-deploy-portal/add-storage-account.png)

1. A szerkesztő JSON automatikusan hozzáadja az erőforrástípus. Figyelje meg, hogy tartalmazza-e egy paramétert definiáló tárfióknak a típusa. Kattintson a **Mentés** gombra.

   ![Sablon megjelenítése](./media/resource-group-template-deploy-portal/show-json.png)

1. Most lehetősége van telepíteni az erőforrásokat a sablonban definiált. Központi telepítése, fogadja el a használati feltételeket, és válassza ki **beszerzési**.

   ![Sablon üzembe helyezése](./media/resource-group-template-deploy-portal/provide-custom-template-values.png)

## <a name="deploy-resources-from-a-template-saved-to-your-account"></a>Egy sablonból fiókjába erőforrások telepítése

A portál lehetővé teszi egy sablon mentése az Azure-fiókjával, és telepítse újra később. További információ ezen sablonok, mentett használata [Ismerkedés az Azure portálon magánsablonok](../marketplace-consumer/mytemplates-getstarted.md).

1. A mentett sablonok találhatók, válassza ki a **további szolgáltatások**.

   ![További szolgáltatások](./media/resource-group-template-deploy-portal/more-services.png)

1. Keresse meg **sablonok** válassza ki ezt a beállítást.

   ![Sablonok keresése](./media/resource-group-template-deploy-portal/find-templates.png)

1. Fiókjába sablonok listájának megtekintéséhez jelölje ki a használni kívánt egy.

   ![mentett sablonok](./media/resource-group-template-deploy-portal/saved-templates.png)

1. Válassza ki **telepítés** újratelepíteni ezt a mentett sablont.

   ![mentett sablon üzembe helyezése](./media/resource-group-template-deploy-portal/deploy-saved-template.png)

## <a name="next-steps"></a>További lépések
* Naplók megtekintése: [naplózási műveletek a Resource Manager](resource-group-audit.md).
* Telepítési hibák elhárításához lásd: [üzembe helyezési műveleteinek megtekintése](resource-manager-deployment-operations.md).
* Egy központi telepítés vagy az erőforráscsoport a sablon lekéréséhez lásd: [Azure Resource Manager sablon exportálása létező erőforrásokból](resource-manager-export-template.md).
* Nagyvállalatoknak az [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Azure nagyvállalati struktúra - előíró előfizetés-irányítás) című cikk nyújt útmutatást az előfizetéseknek a Resource Managerrel való hatékony kezeléséről.
