---
title: "Azure Resource Manager-sablonok exportálása | Microsoft Docs"
description: "Az Azure Resource Manager használatával sablonokat exportálhat létező erőforráscsoportokból."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 5f5ca940-eef8-4125-b6a0-f44ba04ab5ab
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: tomfitz
ms.openlocfilehash: c8f19a4f0aadbee2de97bb3ec85c2c85b493a394
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="export-an-azure-resource-manager-template-from-existing-resources"></a>Azure Resource Manager-sablonok exportálása létező erőforrásokból
Ebből a cikkből megtudja, hogyan exportálhatja az előfizetéshez tartozó meglévő erőforrások Resource Manager-sablonjait. A létrejött sablonnal jobban megértheti a sablon szintaxisát.

Kétféleképpen exportálhat sablont:

* Exportálhatja az **üzembe helyezéshez is használt tényleges sablont**. Ebben az esetben az exportált sablon pontosan úgy tartalmazza a különböző paramétereket és változókat, ahogy azok az eredeti sablonban szerepeltek. Ez a megközelítés akkor lehet hasznos, ha a portálon keresztül helyezte üzembe az erőforrásokat, és látni szeretné a sablont ezen erőforrások létrehozásához. Ez a sablon azonnal használható. 
* A másik megoldás, hogy úgy exportálja a **létrejött sablont, hogy az az erőforráscsoport aktuális állapotát tükrözze**. Ebben az esetben az exportált sablon nem az üzembe helyezéshez használt sablonon alapul. A rendszer ehelyett új sablont hoz létre az erőforráscsoport aktuális állapota alapján. Az exportált sablon számos nem módosítható értéket tartalmaz, és valószínűleg kevesebb paraméter található benne, mint amennyit általában használni szokott. Ez a megközelítés akkor lehet hasznos, ha az üzembe helyezés után módosította az erőforráscsoportot. Ez a sablon általában módosításokat igényel, mielőtt használható lenne.

Ebben a témakörben mind a két megoldást bemutatjuk a portálon keresztül.

## <a name="deploy-resources"></a>Erőforrások üzembe helyezése
Először helyezzünk üzembe erőforrásokat az Azure-ra, amelyeket aztán sablonként exportálhat. Ha már rendelkezik olyan erőforráscsoporttal az előfizetésében, amelyet sablonként szeretne exportálni, kihagyhatja ezt a szakaszt. A cikk többi része feltételezi, hogy üzembe helyezte az ebben a szakaszban látható webalkalmazást és SQL-adatbázist. Ha másik megoldást használ, a lépések kissé eltérők lehetnek, de a sablonok exportálásának lépései azonosak. 

1. Az [Azure Portalon](https://portal.azure.com) válassza az **Új** lehetőséget.
   
      ![új kiválasztása](./media/resource-manager-export-template/new.png)
2. Keressen a **webalkalmazás + SQL** kifejezésre, és válassza ki azt az elérhető lehetőségek közül.
   
      ![webalkalmazás és SQL keresése](./media/resource-manager-export-template/webapp-sql.png)

3. Kattintson a **Létrehozás** gombra.

      ![létrehozás kiválasztása](./media/resource-manager-export-template/create.png)

4. Adja meg a webalkalmazás és az SQL-adatbázis szükséges értékeit. Kattintson a **Létrehozás** gombra.

      ![web és SQL érték megadása](./media/resource-manager-export-template/provide-web-values.png)

Az üzembe helyezés egy percig is eltarthat. Az üzembe helyezés befejezése után az előfizetése már tartalmazza a megoldást.

## <a name="view-template-from-deployment-history"></a>Sablon megtekintése az üzembehelyezési előzményekből
1. Nyissa meg az új erőforráscsoport paneljét. Figyelje meg, hogy a panelen a legutóbbi üzembe helyezés részletes adatai láthatók. Kattintson erre a hivatkozásra.
   
      ![erőforráscsoport panel](./media/resource-manager-export-template/select-deployment.png)
2. Megjelennek a csoport üzembe helyezési előzményei. Az Ön esetében valószínűleg csak egyetlen üzembe helyezés látható a panelen. Válassza ki ezt a telepítést.
   
     ![legutóbbi telepítés](./media/resource-manager-export-template/select-history.png)
3. A panelen megjelenik az üzembe helyezés összegzése. Az összegzés tartalmazza a telepítés, valamint annak műveleteinek állapotát, és a paraméterek számára megadott értékeket. Az üzembe helyezéshez használt sablon megtekintéséhez válassza a **Sablon megtekintése** lehetőséget.
   
     ![telepítés összegzésének megtekintése](./media/resource-manager-export-template/view-template.png)
4. A Resource Manager az alábbi hét fájlt kéri le:
   
   1. **Sablon** – A megoldás infrastruktúráját meghatározó sablon. A tárfiók a portálon keresztül történő létrehozásakor a Resource Manager egy sablon használatával telepítette azt, és elmentette ezt a sablont későbbi felhasználás céljából.
   2. **Paraméterek** – Az értékek az üzembe helyezés során történő megadásához szükséges paraméterfájl. Ez tartalmazza az első üzembe helyezés során megadott értékeket. Ezek bármelyike módosítható a sablon újbóli telepítése során.
   3. **CLI** – A sablon üzembe helyezéséhez használható Azure CLI-parancsfájl.
   3. **CLI 2.0** – A sablon üzembe helyezéséhez használható Azure CLI-szkriptfájl.
   4. **PowerShell** – A sablon üzembe helyezéséhez használható Azure PowerShell-parancsfájl.
   5. **.NET** – A sablon üzembe helyezéséhez használható .NET-osztály.
   6. **Ruby** – A sablon üzembe helyezéséhez használható Ruby-osztály.
      
      A fájlok a panelen található hivatkozásokon keresztül érhetők el. Alapértelmezés szerint a panelben a sablon jelenik meg.
      
       ![sablon megtekintése](./media/resource-manager-export-template/see-template.png)
      
Ez maga a sablon, amelyet a webalkalmazás és az SQL-adatbázis létrehozásához használt. Figyelje meg, hogy a benne szereplő paraméterek különböző értékek megadását is lehetővé teszik az üzembe helyezés során. A sablonok struktúrájával kapcsolatos további információk: [Azure Resource Manager-sablonok készítése](resource-group-authoring-templates.md).

## <a name="export-the-template-from-resource-group"></a>Az erőforráscsoport sablonjának exportálása
Ha manuálisan módosította az erőforrásokat, vagy több üzemelő példányban adott hozzá erőforrásokat, az üzembe helyezés előzményeiből a sablonok lekérése nem tükrözi az erőforráscsoport aktuális állapotát. Ez a szakasz bemutatja, hogyan exportálhat az erőforráscsoport aktuális állapotát tükröző sablont. 

> [!NOTE]
> Több mint 200 erőforrással rendelkező erőforráscsoport esetében nem exportálhat sablont.
> 
> 

1. Az egyes erőforráscsoportok sablonjának megtekintéséhez válassza az **Automation-szkript** lehetőséget.
   
      ![erőforráscsoportok exportálása](./media/resource-manager-export-template/select-automation.png)
   
     A Resource Manager kiértékeli az erőforráscsoportban lévő erőforrásokat, és létrehoz egy sablont ezekhez az erőforrásokhoz. A sablonexportálási funkciót nem támogatja az összes erőforrástípus. Előfordulhat, hogy egy hibaüzenet jelenik meg, amely arról tájékoztatja, hogy az exportálás során probléma merült fel. Ezeket a hibákat [Az exportálással kapcsolatos problémák megoldása](#fix-export-issues) című részben szereplő információk segítségével oldhatja meg.
2. Ismét megjelenik a megoldás újbóli üzembe helyezéséhez használható hat fájl. De ezúttal a sablon némileg eltérően jelenik meg. Figyelje meg, hogy a létrejött sablon kevesebb paramétert tartalmaz, mint az előző szakasz sablonja. Ezenkívül sok érték (például a hely és a termékváltozat) paraméterértékek fogadása helyett nem módosíthatóként jelenik meg ebben a sablonban. A sablon újbóli használata előtt érdemes lehet szerkeszteni a sablont, hogy jobban kihasználja a paraméterekben rejlő lehetőségeket. 
   
3. Van néhány további lehetőség is arra, hogy továbbra is ezzel a sablonnal dolgozzon. Letöltheti a sablont, és dolgozhat rajta helyben egy JSON-szerkesztővel. Mentheti a sablont a saját tárába, és a portálon keresztül dolgozhat rajta.
   
     Ha járatos egy JSON-szerkesztő, például a [VS Code](https://code.visualstudio.com/) vagy a [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) használatában, érdemes a sablont helyileg letölteni, és az adott szerkesztőt használni. Ha helyben kíván dolgozni, válassza a **Letöltés** lehetőséget.
   
      ![sablon letöltése](./media/resource-manager-export-template/download-template.png)
   
     Ha nem állította be a JSON-szerkesztőt, inkább a portálon keresztül szerkessze a sablont. A témakör további részében azt feltételezzük, hogy mentette a sablont a saját tárába a portálon. Azonban ugyanazon szintaxismódosításokat végezheti el a sablonon a JSON-szerkesztőben helyileg és a portálon keresztül egyaránt. Ha a portálon keresztül dolgozna, válassza a **Hozzáadás a dokumentumtárhoz** lehetőséget.
   
      ![hozzáadás a dokumentumtárhoz](./media/resource-manager-export-template/add-to-library.png)
   
     Egy sablonnak a dokumentumtárhoz való hozzáadásakor adja meg a sablon nevét és leírását. Ezt követően válassza a **Mentés** lehetőséget.
   
     ![sablon értékeinek megadása](./media/resource-manager-export-template/save-library-template.png)
4. Egy, a saját tárába mentett sablon megtekintéséhez válassza a **További szolgáltatások** lehetőséget, írja be a **Sablonok** szöveget az eredmények szűréséhez, majd válassza a **Sablonok** elemet.
   
      ![sablonok keresése](./media/resource-manager-export-template/find-templates.png)
5. Válassza mentett nevű sablont.
   
      ![sablon kiválasztása](./media/resource-manager-export-template/select-saved-template.png)

## <a name="customize-the-template"></a>A sablon testreszabása
Az exportált sablon jól működik, ha ugyanazt a webalkalmazást és SQL-adatbázist szeretné létrehozni mindegyik üzemelő példányhoz. A Resource Manager által nyújtott lehetőségek azonban lehetővé teszik, hogy a sablonokat rugalmasabb módon helyezze üzembe. Ez a cikk bemutatja, hogyan adhat paramétereket az adatbázis rendszergazdai nevéhez és jelszavához. Ugyanezzel a módszerrel láthatja el további rugalmassággal a sablon más értékeit.

1. A sablon testreszabásához válassza a **Szerkesztés** lehetőséget.
   
     ![sablon megjelenítése](./media/resource-manager-export-template/select-edit.png)
2. Válassza ki a sablont.
   
     ![sablon szerkesztése](./media/resource-manager-export-template/select-added-template.png)
3. A telepítés során meghatározni kívánt értékek megadásához adja a következő két paramétert a sablon **paraméterek** szakaszához:

   ```json
   "administratorLogin": {
       "type": "String"
   },
   "administratorLoginPassword": {
       "type": "SecureString"
   },
   ```

4. Az új paraméterek használatához cserélje le az SQL-kiszolgáló definícióját az **erőforrások** szakaszban. Figyelje meg, hogy az **administratorLogin** és az **administratorLoginPassword** most paraméterértékeket használ.

   ```json
   {
       "comments": "Generalized from resource: '/subscriptions/{subscription-id}/resourceGroups/exportsite/providers/Microsoft.Sql/servers/tfserverexport'.",
       "type": "Microsoft.Sql/servers",
       "kind": "v12.0",
       "name": "[parameters('servers_tfserverexport_name')]",
       "apiVersion": "2014-04-01-preview",
       "location": "South Central US",
       "scale": null,
       "properties": {
           "administratorLogin": "[parameters('administratorLogin')]",
           "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
           "version": "12.0"
       },
       "dependsOn": []
   },
   ```

6. Amikor befejezte a sablon szerkesztését, kattintson az **OK** gombra.
7. Kattintson a **Mentés** gombra a sablon módosításainak mentéséhez.
   
     ![sablon mentése](./media/resource-manager-export-template/save-template.png)
8. A frissített sablon újbóli üzembe helyezéséhez válassza a **Telepítés** lehetőséget.
   
     ![sablon üzembe helyezése](./media/resource-manager-export-template/redeploy-template.png)
9. Adja meg a paraméterértékeket, és válassza ki azt az erőforráscsoportot, amelybe az erőforrásokat üzembe kívánja helyezni.


## <a name="fix-export-issues"></a>Az exportálással kapcsolatos problémák megoldása
A sablonexportálási funkciót nem támogatja az összes erőforrástípus. A probléma megoldásához adja hozzá ismét manuálisan a hiányzó erőforrásokat a sablonhoz. A hibaüzenetben szerepelnek a nem exportálható erőforrástípusok. Ezt az erőforrástípust a [Sablonreferenciában](/azure/templates/) találja. Ha például manuálisan szeretne hozzáadni egy virtuális hálózati átjárót, lásd a [Microsoft.Network/virtualNetworkGateways sablonreferenciát](/azure/templates/microsoft.network/virtualnetworkgateways).

> [!NOTE]
> Az exportálási hibák csak akkor lépnek fel, ha az erőforráscsoportból, és nem az üzembe helyezési előzmények közül végez exportálást. Ha a legutóbbi üzembe helyezés pontosan tükrözi az erőforráscsoport aktuális állapotát, érdemes az erőforráscsoport helyett az üzembe helyezési előzmények közül elvégezni a sablon exportálását. Csak akkor exportáljon az erőforráscsoportból, ha olyan módosításokat végzett rajta, amelyeket nem lehet egyetlen sablonnal definiálni.
> 
> 

## <a name="next-steps"></a>Következő lépések
Megtanulta, hogyan exportálhat sablonokat a portálon létrehozott erőforrásokból.

* Sablont a [PowerShell](resource-group-template-deploy.md), az [Azure parancssori felülete](resource-group-template-deploy-cli.md) vagy a [REST API](resource-group-template-deploy-rest.md) használatával helyezhet üzembe.
* A sablonok PowerShellen keresztül történő exportálásával kapcsolatos információk: [Az Azure PowerShell használata Azure Resource Managerrel](powershell-azure-resource-manager.md).
* A sablonok az Azure parancssori felületen keresztül történő exportálásával kapcsolatos információk: [A Mac, Linux és Windows eszközökhöz készült Azure CLI használata az Azure Resource Manager eszközzel](xplat-cli-azure-resource-manager.md).

