---
title: Az Azure Resource Manager-sablon exportálása az Azure portal használatával
description: Az Azure portal segítségével Azure Resource Manager-sablon exportálása az előfizetéshez tartozó erőforrások.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: tomfitz
ms.openlocfilehash: ea9499da3dac67635a48704f439f6592c6ed467e
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/09/2019
ms.locfileid: "65515384"
---
# <a name="single-and-multi-resource-export-to-template-in-azure-portal"></a>Egy vagy több erőforrás exportálási sablont az Azure Portalon

Azure Resource Manager-sablonok létrehozásáról nyújtanak, egy sablon is exportálása létező erőforrásokból. Az exportált sablon segítségével megismerheti a JSON-szintaxist és a tulajdonságok, amelyek az erőforrások üzembe helyezése. Később üzembe helyezések automatizálását, az exportált sablonból indul, és módosíthatja azt a forgatókönyvet.

Resource Manager lehetővé teszi, hogy válasszon ki egy vagy több erőforrást a sablon exportálása. Pontosan a szükséges erőforrásokat a sablonban összpontosíthat.

## <a name="choose-the-right-export-option"></a>Válassza ki a megfelelő exportálási lehetőséget

Kétféleképpen exportálhat sablont:

* **Exportálhatja az erőforráscsoport vagy erőforrás**. Ezzel a beállítással meglévő erőforrások új sablont hoz létre. Az exportált sablon egy "pillanatkép" meg az erőforráscsoport aktuális állapotát. Egy egész erőforráscsoportot, vagy adott erőforráscsoporton belüli adott erőforrások exportálhatja.

* **Üzembe helyezés előtt, vagy az előzmények exportálása**. Ezt a beállítást a központi telepítéshez használt sablon pontos másolatát kérdezi le.

A lehetőséget választja, attól függően az exportált sablonok rendelkezik különböző tulajdonságait.

| Az erőforráscsoportra vagy erőforrásra | Üzembe helyezés előtt, vagy előzményekből |
| --------------------- | ----------------- |
| A pillanatkép-sablont az erőforrások aktuális állapot. Ez magában foglalja a manuális üzembe helyezés után végrehajtott módosításokat. | A sablon csak a telepítéshez erőforrások állapotát mutatja. Miután az üzembe helyezés nem tartalmazza a fájl végrehajtott manuális módosítások. |
| Erőforrások exportálása egy erőforráscsoportot, amelybe közül választhat. | Minden erőforrás egy adott központi telepítés részét képezik. Nem válasszon ezek az erőforrások egy részét, és adjon hozzá erőforrásokat, egy másik időpontra lett hozzáadva. |
| Sablon tartalmazza az összes tulajdonság az erőforrás, beleértve az egyes tulajdonságok nem megfelelően beállított üzembe helyezés során. Érdemes eltávolítani vagy törölni a sablon újbóli használata előtt ezeket a tulajdonságokat. | Sablon csak az üzembe helyezéshez szükséges tulajdonságokat tartalmazza. A sablon áll a használatra kész. |
| Sablon valószínűleg nem tartalmazza az összes ismételt használata a paraméterekhez. A legtöbb tulajdonságértékek módosíthatóként a sablonban. Ismételt üzembe a sablont más környezetekben, hozzá kell paramétereket, amelyek növelik a teszi az erőforrások konfigurálását. | Sablon paraméterei, amelyek megkönnyítik az ismételt üzembe helyezése különböző környezetekben tartalmazza. |

Exportálja a sablont egy erőforráscsoport vagy erőforrás, ha:

* Az erőforrást, amely az eredeti üzembe helyezés után történtek változások rögzítésére van szükség.
* Szeretné kiválasztani, hogy mely erőforrások lesznek exportálva.

Exportálja a sablont az üzembe helyezés előtt, vagy az előzményekben tekintheti át, ha:

* Azt szeretné, hogy egy egyszerű újbóli sablont.
* Nem kell az eredeti üzembe helyezés után végrehajtott módosításokat tartalmazza.

## <a name="export-template-from-resource-group"></a>Az erőforráscsoport a sablon exportálása

Erőforráscsoport exportálása egy vagy több erőforrást:

1. Válassza ki az exportálni kívánt erőforrásokat tartalmazó erőforráscsoportot.

1. Az erőforráscsoportban lévő összes erőforrás exportálásához válassza ki az összes, majd **sablon exportálása**. A **sablon exportálása** lehetőség csak aktívvá válik legalább egy erőforrás kiválasztása után.

   ![Erőforrások exportálása](./media/export-template-portal/select-all-resources.png)

1. Az Exportálás erőforrásoknál kiválasztásához jelölje be a jelölőnégyzeteket, ezek az erőforrások mellett. Ezután válassza ki **sablon exportálása**.

   ![Exportálandó erőforrások kiválasztása](./media/export-template-portal/select-resources.png)

1. Az exportált sablon jelenik meg, és tölthető le.

   ![Sablon megjelenítése](./media/export-template-portal/show-template.png)

## <a name="export-template-from-resource"></a>Az erőforrás sablon exportálása

Egy erőforrás exportálásához:

1. Válassza ki az exportálni kívánt erőforrást tartalmazó erőforráscsoportot.

1. Válassza ki az erőforrás exportálásához.

   ![Erőforrás kiválasztása](./media/export-template-portal/select-link-resource.png)

1. Válassza ki az adott erőforráshoz **sablon exportálása** a bal oldali panelen.

   ![Erőforráscsoport exportálása](./media/export-template-portal/export-single-resource.png)

1. Az exportált sablon jelenik meg, és tölthető le. A sablon csak az egyetlen erőforrást tartalmazza.

## <a name="export-template-before-deployment"></a>Sablon exportálása az üzembe helyezés előtt

1. Válassza ki az Azure-szolgáltatás számára telepíteni kívánja.

1. Adja meg az értékeket az új szolgáltatáshoz.

1. Érvényesítési elhagyása után, de a telepítés megkezdése előtt, válassza ki a **automatizálási sablon letöltése**.

   ![Sablon letöltése](./media/export-template-portal/download-before-deployment.png)

1. A sablon jelenik meg, és letölthető.

   ![Sablon megjelenítése](./media/export-template-portal/show-template-before-deployment.png)

## <a name="export-template-after-deployment"></a>Üzembe helyezés után a sablon exportálása

Exportálhatja a sablont, amellyel a meglévő erőforrások üzembe helyezése. A sablon kap arról, pontosan az üzembe helyezéshez használt.

1. Válassza ki az exportálni kívánt erőforráscsoportot.

1. A hivatkozásra kattintva **központi telepítések**.

   ![Válassza ki az üzembe helyezési előzmények](./media/export-template-portal/select-deployment-history.png)

1. Válassza ki azt a központi telepítéseket az üzembe helyezési előzmények.

   ![Válassza ki a központi telepítés](./media/export-template-portal/select-details.png)

1. Válassza ki **sablon**. A központi telepítéshez használt sablon jelenik meg, és letölthető.

   ![Sablon kiválasztása](./media/export-template-portal/show-template-from-history.png)

## <a name="next-steps"></a>További lépések

- Azure Resource Manager kapcsolatban lásd: [Azure Resource Manager áttekintése](./resource-group-overview.md).
- A Resource Manager-sablon szintaxisáról kapcsolatban lásd: [struktúra és az Azure Resource Manager-sablonok szintaxisát](./resource-group-authoring-templates.md).
- Ismerje meg, hogyan fejleszthet sablonokat, tekintse meg a [lépésről lépésre haladó oktatóanyagok](/azure/azure-resource-manager/).
- Az Azure Resource Manager-sablon sémák megtekintése: [sablonreferenciája](/azure/templates/).