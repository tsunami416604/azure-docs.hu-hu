---
title: Sablon exportálása az Azure Portalon
description: Az Azure Portal használatával exportálhat egy Azure Resource Manager-sablont az előfizetésében lévő erőforrásokból.
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 8cdba58a7a2ba998bac7fc0225ff957047cd69b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273734"
---
# <a name="single-and-multi-resource-export-to-a-template-in-azure-portal"></a>Egy- és többerőforrásos exportálás sablonba az Azure Portalon

Az Azure Resource Manager-sablonok létrehozásához exportálhat egy sablont a meglévő erőforrásokból. Az exportált sablon segít megérteni a JSON szintaxisát és az erőforrásokat üzembe helyező tulajdonságokat. A jövőbeli központi telepítések automatizálásához kezdje az exportált sablonnal, és módosítsa azt a forgatókönyvhöz.

Az Erőforrás-kezelő lehetővé teszi egy vagy több erőforrás kiválasztását a sablonba való exportáláshoz. Pontosan a sablonban szükséges erőforrásokra összpontosíthat.

Ez a cikk bemutatja, hogyan exportálhat sablonokat a portálon keresztül. Használhatja [az Azure CLI,](../management/manage-resource-groups-cli.md#export-resource-groups-to-templates) [Az Azure PowerShell](../management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)vagy [a REST API használatát](/rest/api/resources/resourcegroups/exporttemplate)is.

## <a name="choose-the-right-export-option"></a>Válassza ki a megfelelő exportálási lehetőséget

Kétféleképpen exportálhat sablont:

* **Exportálás erőforráscsoportból vagy erőforrásból**. Ez a beállítás új sablont hoz létre a meglévő erőforrásokból. Az exportált sablon az erőforráscsoport aktuális állapotának "pillanatképe". Az adott erőforráscsoporton belül exportálhat egy teljes erőforráscsoportot vagy adott erőforrásokat.

* **Exportálás üzembe helyezés előtt vagy az előzményekből.** Ez a beállítás a központi telepítéshez használt sablon pontos másolatát olvassa be.

A választott beállítástól függően az exportált sablonok különböző tulajdonságokkal rendelkeznek.

| Erőforráscsoportból vagy erőforrásból | Telepítés előtt vagy előzményekből |
| --------------------- | ----------------- |
| A sablon az erőforrások aktuális állapotának pillanatképe. Ez magában foglalja a telepítés után végrehajtott manuális módosításokat. | A sablon csak az erőforrások állapotát jeleníti meg a telepítés időpontjában. Az üzembe helyezés után végrehajtott manuális módosításokat a csomag nem tartalmazza. |
| Kiválaszthatja, hogy egy erőforráscsoport mely erőforrásait szeretné exportálni. | Egy adott telepítéshez tartozó összes erőforrás szerepel. Ezeknek az erőforrásoknak egy részét nem választhatja ki, és nem adhat hozzá más időpontban hozzáadott erőforrásokat. |
| A sablon tartalmazza az erőforrások összes tulajdonságát, beleértve azüzembe helyezés során általában nem beállított tulajdonságokat is. Előfordulhat, hogy a sablon újbóli felhasználása előtt el szeretné távolítani vagy meg szeretné tisztítani ezeket a tulajdonságokat. | A sablon csak a központi telepítéshez szükséges tulajdonságokat tartalmazza. A sablon használatra kész. |
| A sablon valószínűleg nem tartalmazza az újrafelhasználáshoz szükséges összes paramétert. A legtöbb tulajdonságérték kódolva van a sablonban. A sablon más környezetekben való újratelepítéséhez olyan paramétereket kell hozzáadnia, amelyek növelik az erőforrások konfigurálását.  A Paraméterek **belefoglalása** lehetőséget is kivonhatja a saját paraméterek megszövegezéséhez. | A sablon olyan paramétereket tartalmaz, amelyek megkönnyítik a különböző környezetekben való újratelepítést. |

Exportálja a sablont egy erőforráscsoportból vagy erőforrásból, ha:

* Az eredeti telepítés után végrehajtott erőforrások módosításait rögzítenie kell.
* Ki szeretné választani az exportálandó erőforrásokat.

A sablon exportálása telepítés előtt vagy az előzményekből, ha:

* Egy könnyen újrahasználható sablont szeretne.
* Nem kell megadnia az eredeti telepítés után végrehajtott módosításokat.

## <a name="limitations"></a>Korlátozások

Erőforráscsoportból vagy erőforrásból történő exportáláskor az exportált sablon az egyes erőforrástípusok [közzétett sémákból](https://github.com/Azure/azure-resource-manager-schemas/tree/master/schemas) jön létre. Alkalmanként a séma nem rendelkezik az erőforrástípus legújabb verziójával. Ellenőrizze az exportált sablont, hogy az tartalmazza-e a szükséges tulajdonságokat. Ha szükséges, az exportált sablont a szükséges API-verzió használatához szerkesztheti.

Az exportálási sablon funkció nem támogatja az Azure Data Factory-erőforrások exportálását. Ha többet szeretne megtudni arról, hogyan exportálhatja a Data Factory-erőforrásokat, olvassa el [az Adatgyár másolása vagy klónozása az Azure Data Factoryban című témakört.](https://aka.ms/exportTemplateViaAdf)

A klasszikus központi telepítési modellen keresztül létrehozott erőforrások exportálásához át kell [telepítenie őket az Erőforrás-kezelő telepítési modelljébe.](https://aka.ms/migrateclassicresourcetoarm)

## <a name="export-template-from-a-resource-group"></a>Sablon exportálása erőforráscsoportból

Erőforráscsoportból egy vagy több erőforrás exportálása:

1. Jelölje ki az exportálni kívánt erőforrásokat tartalmazó erőforráscsoportot.

1. Jelöljön ki egy vagy több erőforrást a jelölőnégyzetek bejelölésével.  Az összes kijelöléséhez jelölje be a Név bal oldalán lévő **jelölőnégyzetet.** A **Sablon exportálása** menüelem csak akkor válik engedélyezve, ha legalább egy erőforrást kijelölt.

   ![Az összes erőforrás exportálása](./media/export-template-portal/select-all-resources.png)

    A képernyőképen csak a tárfiók van kiválasztva.
1. Válassza **a Sablon exportálása lehetőséget.**

1. Megjelenik az exportált sablon, amely letölthető és telepíthető.

   ![Sablon megjelenítése](./media/export-template-portal/show-template.png)

   **A paraméterek belefoglalása** alapértelmezés szerint be van jelölve.  Ha be van jelölve, a sablon létrehozásakor az összes sablonparaméter szerepelni fog. Ha saját paramétereket szeretne megszerzőnek, kapcsolja be ezt a jelölőnégyzetet, hogy ne tartalmazza őket.

## <a name="export-template-from-a-resource"></a>Sablon exportálása erőforrásból

Egy erőforrás exportálása:

1. Jelölje ki az exportálni kívánt erőforráscsoportot tartalmazó erőforráscsoportot.

1. Az erőforrás megnyitásához jelölje ki az exportálni kívánt erőforrást.

1. Az adott erőforráshoz válassza a **sablon exportálása** lehetőséget a bal oldali ablaktáblában.

   ![Erőforrás exportálása](./media/export-template-portal/export-single-resource.png)

1. Megjelenik az exportált sablon, amely letölthető és telepíthető. A sablon csak egyetlen erőforrást tartalmaz. **A paraméterek belefoglalása** alapértelmezés szerint be van jelölve.  Ha be van jelölve, a sablon létrehozásakor az összes sablonparaméter szerepelni fog. Ha saját paramétereket szeretne megszerzőnek, kapcsolja be ezt a jelölőnégyzetet, hogy ne tartalmazza őket.

## <a name="export-template-before-deployment"></a>Sablon exportálása telepítés előtt

1. Válassza ki a telepíteni kívánt Azure-szolgáltatást.

1. Töltse ki az új szolgáltatás értékeit.

1. Az ellenőrzés átadása után, de a telepítés megkezdése előtt válassza **a Sablon letöltése automatizáláshoz**lehetőséget.

   ![Sablon letöltése](./media/export-template-portal/download-before-deployment.png)

1. A sablon megjelenik, és letölthető és telepíthető.


## <a name="export-template-after-deployment"></a>Sablon exportálása telepítés után

Exportálhatja a meglévő erőforrások üzembe helyezéséhez használt sablont. A sablon kap pontosan az, amelyet használt üzembe helyezéshez.

1. Jelölje ki az exportálni kívánt erőforráscsoportot.

1. Válassza ki a hivatkozást a **Telepítések csoportban.**

   ![Telepítési előzmények kiválasztása](./media/export-template-portal/select-deployment-history.png)

1. Válassza ki az egyik központi telepítést a központi telepítési előzmények közül.

   ![Telepítés kiválasztása](./media/export-template-portal/select-details.png)

1. Válassza a **Sablon lehetőséget.** A központi telepítéshez használt sablon megjelenik, és letölthető.

   ![Sablon kiválasztása](./media/export-template-portal/show-template-from-history.png)

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogy miként exportálhat sablonokat [az Azure CLI,](../management/manage-resource-groups-cli.md#export-resource-groups-to-templates) [az Azure PowerShell](../management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)vagy [a REST API segítségével.](/rest/api/resources/resourcegroups/exporttemplate)
- Az Erőforrás-kezelő sablon szintaxisának megismeréséről [Az Azure Resource Manager-sablonok szerkezetének és szintaxisának megismerése című témakörben olvashat.](template-syntax.md)
- A sablonok fejlesztéséről részletesen olvashat [az oktatóanyagokról.](/azure/azure-resource-manager/)
- Az Azure Resource Manager sablonsémák megtekintéséhez olvassa el a [sablon hivatkozási .](/azure/templates/)
