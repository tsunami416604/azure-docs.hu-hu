---
title: Azure Resource Manager-csoportok kezelése az Azure portal használatával |} A Microsoft Docs
description: Az Azure portal használatával az Azure Resource Manager-csoportok kezelése.
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: cbbc710c9408c84c601d7b9eb9560641b5313a05
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58090933"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-the-azure-portal"></a>Azure Resource Manager-erőforráscsoportok kezelése az Azure portal használatával

Ismerje meg, hogyan használható a [az Azure portal](https://portal.azure.com) a [Azure Resource Manager](resource-group-overview.md) az Azure-erőforráscsoportok kezelése. Azure-erőforrások kezeléséhez, lásd: [Azure-erőforrások kezelése az Azure portal használatával](./manage-resources-portal.md).

Más cikkek erőforráscsoportok kezeléséről:

- [Az Azure-erőforráscsoportok kezelése az Azure CLI-vel](./manage-resources-cli.md)
- [Azure-erőforráscsoportok kezelése az Azure PowerShell használatával](./manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-resource-group"></a>Az erőforráscsoportok ismertetése

Az erőforráscsoport egy tároló, amely Azure-megoldásokhoz kapcsolódó erőforrásokat tárol. Az erőforráscsoport tartalmazhatja a megoldás összes erőforrását, vagy csak azokat az erőforrásokat, amelyeket Ön egy csoportként szeretne kezelni. A szervezet számára legideálisabb elosztás alapján eldöntheti, hogyan szeretné elosztani az erőforrásokat az erőforráscsoportok között. Általánosságban elmondható adjon hozzá erőforrásokat, azonos életciklussal ugyanabban az erőforráscsoportban, így könnyen telepítése, frissítése és csoportként törölheti őket.

Az erőforráscsoport erőforrásokra vonatkozó metaadatokat tárol. Ezért ha az erőforráscsoport számára megad egy helyet, akkor a metaadatok tárolási helyét adja meg. Megfelelőségi okokból szükség lehet arra, hogy az adatokat egy adott régióban tárolja.

Az erőforráscsoport erőforrásokra vonatkozó metaadatokat tárol. Ha megad egy helyet az erőforráscsoportnak, határozunk meg a metaadatok tárolására.

## <a name="create-resource-groups"></a>Erőforráscsoportok létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **erőforráscsoportok**

    ![erőforráscsoport hozzáadása](./media/manage-resource-groups-portal/manage-resource-groups-add-group.png)
3. Válassza a **Hozzáadás** lehetőséget.
4. Írja be a következő értékeket:

   - **Előfizetés**: Válassza ki az Azure-előfizetését. 
   - **Erőforráscsoport**: Adjon meg egy új erőforráscsoport-nevet. 
   - **Régió**: Válasszon ki egy Azure-helyen, például **USA középső RÉGIÓJA**.

     ![Erőforráscsoport létrehozása](./media/manage-resource-groups-portal/manage-resource-groups-create-group.png)
5. Válassza ki **felülvizsgálat + létrehozás**
6. Kattintson a **Létrehozás** gombra. Hozzon létre egy erőforráscsoportot néhány másodpercet vesz igénybe.
7. Válassza ki **frissítése** frissülnek az erőforráscsoport-lista, és válassza ki az újonnan létrehozott erőforráscsoport való megnyitásához a felső menüben. Vagy válasszon ki **értesítési**(Harang ikon) a felső, és válassza ki a **erőforráscsoport megnyitása** az újonnan létrehozott erőforráscsoport megnyitása

    ![Erőforráscsoport megnyitása](./media/manage-resource-groups-portal/manage-resource-groups-add-group-go-to-resource-group.png)

## <a name="list-resource-groups"></a>Erőforráscsoportok listázása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Az erőforráscsoportok listázásához válassza **erőforráscsoportok**

    ![Keresse meg az erőforráscsoportok](./media/manage-resource-groups-portal/manage-resource-groups-list-groups.png)

3. Az erőforráscsoportok megjelenő információk testreszabásához válassza **oszlopok szerkesztése**. A következő képernyőképen látható is hozzáadhatja a megjelenítendő oszlopok hozzáadásával:

## <a name="open-resource-groups"></a>Erőforráscsoportok megnyitása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza az **Erőforráscsoportok** lehetőséget.
3. Válassza ki a megnyitni kívánt erőforráscsoportot.

## <a name="delete-resource-groups"></a>Erőforráscsoport törlése

1. Nyissa meg a törölni kívánt erőforráscsoportot.  Lásd: [nyissa meg az erőforráscsoportok](#open-resource-groups).
2. Válassza az **Erőforráscsoport törlése** elemet.

    ![azure-erőforráscsoport törlése](./media/manage-resource-groups-portal/delete-group.png)

Milyen az Azure Resource Manager orders erőforrások törlését kapcsolatos további információkért lásd: [törölni az Azure Resource Manager-erőforrást csoport](./resource-group-delete.md).

## <a name="deploy-resources-to-a-resource-group"></a>Erőforrások üzembe helyezése egy erőforráscsoportot

Resource Manager-sablon létrehozása után az Azure-erőforrások üzembe helyezése az Azure portal használatával. A sablonok létrehozása, lásd: [a rövid útmutató: Létrehozása és üzembe helyezése Azure Resource Manager-sablonok az Azure portal használatával](./resource-manager-quickstart-create-templates-use-the-portal.md). A portál használatával sablonok telepítésével, lásd: [erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure portal](resource-group-template-deploy-portal.md).

## <a name="move-to-another-resource-group-or-subscription"></a>Helyezze át egy másik erőforráscsoportba vagy előfizetésbe

A csoport az erőforrásokat áthelyezheti egy másik erőforráscsoportot. További információ: [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](resource-group-move-resources.md).

## <a name="lock-resource-groups"></a>Zárolási erőforráscsoportok

Zárolás megakadályozza a véletlen törlését vagy módosítását a kritikus fontosságú erőforrások, például az Azure-előfizetésre, erőforráscsoportra vagy erőforrásra a szervezet más felhasználói. 

1. Nyissa meg a törölni kívánt erőforráscsoportot.  Lásd: [nyissa meg az erőforráscsoportok](#open-resource-groups).
2. A bal oldali panelen válassza ki a **zárolja**.
3. Egy zárolás ahhoz az erőforráscsoporthoz válassza **Hozzáadás**.
4. Adja meg **zárolás neve**, **zárolás típusa**, és **megjegyzések**. A zárolás típusok a következők **csak olvasható**, és **törlése**.

    ![azure-erőforráscsoport zárolása](./media/manage-resource-groups-portal/manage-resource-groups-add-lock.png)

További információkért lásd: [váratlan módosítások megelőzése érdekében zárolhat erőforrásokat](./resource-group-lock-resources.md).

## <a name="tag-resource-groups"></a>Címke erőforráscsoportok

A címkékkel erőforráscsoportok és erőforrásokhoz, hogy logikusan rendszerezhesse az eszközöket. További információ: [az Azure-erőforrások rendszerezése címkék használatával](./resource-group-using-tags.md#portal).

## <a name="export-resource-groups-to-templates"></a>Erőforráscsoportok exportálása sablonokhoz

Az erőforráscsoport sikeres beállítása után érdemes a Resource Manager-sablon az erőforráscsoport megtekintéséhez. A sablon exportálása két előnyöket kínál:

- Automatizálhatja a később üzembe helyezések, a megoldás, mert a sablon tartalmazza a teljes infrastruktúra.
- Ismerje meg a sablon szintaxisáról alapján, a JavaScript Object Notation (JSON), amely a megoldás jelöli.

Kétféleképpen exportálhat sablont:

- Exportálhatja az üzembe helyezéshez használt tényleges sablont. Ebben az esetben az exportált sablon pontosan úgy tartalmazza a különböző paramétereket és változókat, ahogy azok az eredeti sablonban szerepeltek. Ez a megközelítés akkor lehet hasznos, ha a portálon keresztül helyezte üzembe az erőforrásokat, és látni szeretné a sablont ezen erőforrások létrehozásához. Ez a sablon azonnal használható. 
- Exportálhatja az erőforráscsoport aktuális állapotát jeleníti meg egy létrehozott sablont. Az exportált sablon nem az üzembe helyezéshez használt sablonon alapul. Ehelyett egy sablont, amely egy "snapshot" vagy "Mentés" az erőforráscsoport hoz létre. Az exportált sablon számos nem módosítható értéket tartalmaz, és valószínűleg kevesebb paraméter található benne, mint amennyit általában használni szokott. Ez a beállítás használatával telepítse újra az erőforrásokat ugyanabban az erőforráscsoportban. A sablon használatához egy másik erőforrás-csoport, előfordulhat, jelentős mértékben módosítani azt.

### <a name="export-templates-from-deployment-history"></a>Sablonok exportálása az üzembe helyezési előzményekből

Ez a módszer exportálja a sablonokat az egyes központi telepítések. Ha módosította az erőforrásokat a portálon vagy a hozzáadja/törli erőforrás több üzemelő példányban, lásd: [sablonokat exportálhat erőforráscsoportok](#export-templates-from-resource-groups).

1. Nyissa meg a törölni kívánt erőforráscsoportot.  Lásd: [nyissa meg az erőforráscsoportok](#open-resource-groups).
2. A bal oldali panelen válassza ki a **központi telepítések**, vagy jelölje ki a hivatkozást **központi telepítések**.  Az alábbi képernyőképen látható **4 sikeres** négy különböző üzembe helyezési nevekkel négy elválasztott telepítések miatt. Előfordulhat, hogy látható **1 sikeres**.

    ![Exportálás Azure erőforráscsoport-sablonok](./media/manage-resource-groups-portal/manage-resource-groups-export-templates-deployment-history.png)

3. Válassza ki a központi telepítések közül a listából.
4. A bal oldali panelen válassza ki a **sablon**. A Resource Manager az alábbi hat fájlt kéri le:

   - **Sablon** – A megoldás infrastruktúráját meghatározó sablon. A tárfiók a portálon keresztül történő létrehozásakor a Resource Manager egy sablon használatával telepítette azt, és elmentette ezt a sablont későbbi felhasználás céljából.
   - **Paraméterek** – Az értékek az üzembe helyezés során történő megadásához szükséges paraméterfájl. Ez tartalmazza az első üzembe helyezés során megadott értékeket. Ezek bármelyike módosítható a sablon újbóli telepítése során.
   - **Parancssori felület** – a sablon üzembe helyezéséhez használhatja az Azure CLI-parancsfájl.
   - **PowerShell** – A sablon üzembe helyezéséhez használható Azure PowerShell-parancsfájl.
   - **.NET** – A sablon üzembe helyezéséhez használható .NET-osztály.
   - **Ruby** – A sablon üzembe helyezéséhez használható Ruby-osztály.

     Alapértelmezés szerint a portál megjeleníti a sablont.

5. Válassza ki **letöltése** sablon exportálása a helyi számítógépen.

    ![Exportálás Azure erőforráscsoport-sablonok](./media/manage-resource-groups-portal/manage-resource-groups-export-templates-deployment-history-download.png)

<a name="export-templates-from-resource-groups"></a>
### <a name="export-templates-from-resource-groups"></a>Az erőforráscsoport-sablonok exportálása

Ha módosította az erőforrásokat a portálról, vagy több üzemelő példányban erőforrások hozzáadása/eltávolítása, az üzembe helyezés előzményeiből a sablonok lekérése nem tükrözi az erőforráscsoport aktuális állapotát. Ez a szakasz bemutatja, hogyan exportálhat az erőforráscsoport aktuális állapotát tükröző sablont. A szándék szerint egy pillanatkép, az erőforráscsoport, amely segítségével ugyanabban az erőforráscsoportban üzembe helyezése. Használja az exportált sablon más megoldásokkal, jelentős mértékben módosítani kell azt.

1. Nyissa meg a törölni kívánt erőforráscsoportot.  Lásd: [nyissa meg az erőforráscsoportok](#open-resource-groups).
2. A bal oldali panelen válassza ki a **Automation-szkript**. A Resource Manager az alábbi hat fájlt kéri le:

   - **Sablon** – A megoldás infrastruktúráját meghatározó sablon. A tárfiók a portálon keresztül történő létrehozásakor a Resource Manager egy sablon használatával telepítette azt, és elmentette ezt a sablont későbbi felhasználás céljából.
   - **Paraméterek** – Az értékek az üzembe helyezés során történő megadásához szükséges paraméterfájl. Ez tartalmazza az első üzembe helyezés során megadott értékeket. Ezek bármelyike módosítható a sablon újbóli telepítése során.
   - **Parancssori felület** – a sablon üzembe helyezéséhez használhatja az Azure CLI-parancsfájl.
   - **PowerShell** – A sablon üzembe helyezéséhez használható Azure PowerShell-parancsfájl.
   - **.NET** – A sablon üzembe helyezéséhez használható .NET-osztály.
   - **Ruby** – A sablon üzembe helyezéséhez használható Ruby-osztály.

     Alapértelmezés szerint a portál megjeleníti a sablont.
3. Válassza ki **letöltése** sablon exportálása a helyi számítógépen.

Néhány exportált sablont kell bizonyos szerkesztések, használhatók legyenek. Ismerje meg, hogyan fejleszthet sablonokat, tekintse meg a [lépésről lépésre haladó oktatóanyagok](/azure/azure-resource-manager/).

### <a name="export-template-before-deploying"></a>Sablon üzembe helyezése előtt exportálása

A portál használatával adja meg egy erőforrást.  Az erőforrás üzembe helyezése előtt tekintheti meg és exportálja a sablont. Az utasításokért lásd: [a rövid útmutató: Létrehozása és üzembe helyezése Azure Resource Manager-sablonok az Azure portal használatával](./resource-manager-quickstart-create-templates-use-the-portal.md).

### <a name="fix-export-issues"></a>Az exportálással kapcsolatos problémák megoldása

A sablonexportálási funkciót nem támogatja az összes erőforrástípus. Csak hibák exportálása egy erőforráscsoportból, nem pedig az üzembe helyezési előzményekből exportálásakor. Ha a legutóbbi üzembe helyezés pontosan tükrözi az erőforráscsoport aktuális állapotát, érdemes az erőforráscsoport helyett az üzembe helyezési előzmények közül elvégezni a sablon exportálását. Ha módosította az erőforráscsoportot, amely egyetlen sablonban nem meghatározott csak exportálhatja az erőforráscsoport.

Exportálás problémák megoldása érdekében adja hozzá manuálisan a hiányzó erőforrásokat a sablonhoz vissza. A hibaüzenetben szerepelnek a nem exportálható erőforrástípusok. Ezt az erőforrástípust a [Sablonreferenciában](/azure/templates/) találja. Ha például manuálisan szeretne hozzáadni egy virtuális hálózati átjárót, lásd a [Microsoft.Network/virtualNetworkGateways sablonreferenciát](/azure/templates/microsoft.network/virtualnetworkgateways). Referencia a JSON-erőforrás hozzáadása a sablonhoz, biztosít.

Ha JSON formátumban az erőforrás, le szeretné kérni az erőforrás-értékeket. Az értékeket az erőforrás megtekintéséhez használja a GET műveletet a REST API-ban az erőforrástípushoz. A virtuális hálózati átjáró az értékek lekéréséhez lásd a [első virtuális hálózati átjárók -](/rest/api/network-gateway/virtualnetworkgateways/get).

## <a name="manage-access-to-resource-groups"></a>Erőforráscsoportok való hozzáférés kezelése

A [szerepköralapú hozzáférés-vezérlés (RBAC)](../role-based-access-control/overview.md) az erőforrásokhoz való hozzáférés kezelésének a módja az Azure-ban. További információkért lásd: [rbac-RÓL és az Azure portal-hozzáférés kezelése](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>További lépések

- Azure Resource Manager kapcsolatban lásd: [Azure Resource Manager áttekintése](./resource-group-overview.md).
- A Resource Manager-sablon szintaxisáról kapcsolatban lásd: [struktúra és az Azure Resource Manager-sablonok szintaxisát](./resource-group-authoring-templates.md).
- Ismerje meg, hogyan fejleszthet sablonokat, tekintse meg a [lépésről lépésre haladó oktatóanyagok](/azure/azure-resource-manager/).
- Az Azure Resource Manager-sablon sémák megtekintése: [sablonreferenciája](/azure/templates/).