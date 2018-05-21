---
title: Azure Resource Manager-sablonok exportálása | Microsoft Docs
description: Az Azure Resource Manager használatával sablonokat exportálhat létező erőforráscsoportokból.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 5f5ca940-eef8-4125-b6a0-f44ba04ab5ab
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: tomfitz
ms.openlocfilehash: 14aa54277cac3369df739a1d84580624f2d3b401
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="export-an-azure-resource-manager-template-from-existing-resources"></a>Azure Resource Manager-sablonok exportálása létező erőforrásokból
Ebből a cikkből megtudja, hogyan exportálhatja az előfizetéshez tartozó meglévő erőforrások Resource Manager-sablonjait. A létrejött sablonnal jobban megértheti a sablon szintaxisát.

Kétféleképpen exportálhat sablont:

* Exportálhatja az **üzembe helyezéshez is használt tényleges sablont**. Ebben az esetben az exportált sablon pontosan úgy tartalmazza a különböző paramétereket és változókat, ahogy azok az eredeti sablonban szerepeltek. Ez a megközelítés akkor lehet hasznos, ha a portálon keresztül helyezte üzembe az erőforrásokat, és látni szeretné a sablont ezen erőforrások létrehozásához. Ez a sablon azonnal használható. 
* A másik megoldás, hogy úgy exportálja a **létrejött sablont, hogy az az erőforráscsoport aktuális állapotát tükrözze**. Ebben az esetben az exportált sablon nem az üzembe helyezéshez használt sablonon alapul. Ehelyett az alkalmazás létrehozza a sablont, amely a "snapshot" vagy "Mentés" az erőforráscsoport. Az exportált sablon számos nem módosítható értéket tartalmaz, és valószínűleg kevesebb paraméter található benne, mint amennyit általában használni szokott. Ez a beállítás segítségével telepítse újra az erőforrásokat ugyanabban az erőforráscsoportban. Egy másik erőforráscsoport a sablon használatához, előfordulhat, hogy jelentősen módosítható.

Ez a cikk bemutatja mindkét megközelítés a portálon keresztül.

## <a name="deploy-resources"></a>Erőforrások üzembe helyezése
Először helyezzünk üzembe erőforrásokat az Azure-ra, amelyeket aztán sablonként exportálhat. Ha már rendelkezik olyan erőforráscsoporttal az előfizetésében, amelyet sablonként szeretne exportálni, kihagyhatja ezt a szakaszt. Ez a cikk többi azt feltételezi, hogy a webalkalmazás és az itt látható SQL adatbázis-megoldás telepítése után. Ha másik megoldást használ, a lépések kissé eltérők lehetnek, de a sablonok exportálásának lépései azonosak. 

1. Az a [Azure-portálon](https://portal.azure.com), jelölje be **hozzon létre egy erőforrást**.
   
      ![új kiválasztása](./media/resource-manager-export-template/new.png)
2. Keressen a **webalkalmazás + SQL** kifejezésre, és válassza ki azt az elérhető lehetőségek közül.
   
      ![keresési web app és az SQL](./media/resource-manager-export-template/webapp-sql.png)

3. Kattintson a **Létrehozás** gombra.

      ![Válassza ki létrehozása](./media/resource-manager-export-template/create.png)

4. Adja meg a webalkalmazás és az SQL-adatbázis szükséges értékeit. Kattintson a **Létrehozás** gombra.

      ![Adja meg a web- és SQL érték](./media/resource-manager-export-template/provide-web-values.png)

Az üzembe helyezés egy percig is eltarthat. Az üzembe helyezés befejezése után az előfizetése már tartalmazza a megoldást.

## <a name="view-template-from-deployment-history"></a>Sablon megtekintése az üzembehelyezési előzményekből
1. Nyissa meg az új erőforráscsoporthoz tartozó erőforráscsoport. Figyelje meg, hogy a portál a legutóbbi telepítés eredményeket jeleníti meg. Kattintson erre a hivatkozásra.
   
      ![Erőforráscsoport](./media/resource-manager-export-template/select-deployment.png)
2. Megjelennek a csoport üzembe helyezési előzményei. Abban az esetben a portál valószínűleg csak egy központi telepítési sorolja fel. Válassza ki ezt a telepítést.
   
     ![legutóbbi telepítés](./media/resource-manager-export-template/select-history.png)
3. A portál a központi telepítés összegzését jeleníti meg. Az összegzés tartalmazza a telepítés, valamint annak műveleteinek állapotát, és a paraméterek számára megadott értékeket. Az üzembe helyezéshez használt sablon megtekintéséhez válassza a **Sablon megtekintése** lehetőséget.
   
     ![telepítés összegzésének megtekintése](./media/resource-manager-export-template/view-template.png)
4. A Resource Manager az alábbi hét fájlt kéri le:
   
   1. **Sablon** – A megoldás infrastruktúráját meghatározó sablon. A tárfiók a portálon keresztül történő létrehozásakor a Resource Manager egy sablon használatával telepítette azt, és elmentette ezt a sablont későbbi felhasználás céljából.
   2. **Paraméterek** – Az értékek az üzembe helyezés során történő megadásához szükséges paraméterfájl. Ez tartalmazza az első üzembe helyezés során megadott értékeket. Ezek bármelyike módosítható a sablon újbóli telepítése során.
   3. **CLI** – A sablon üzembe helyezéséhez használható Azure CLI-parancsfájl.
   3. **CLI 2.0** – A sablon üzembe helyezéséhez használható Azure CLI-szkriptfájl.
   4. **PowerShell** – A sablon üzembe helyezéséhez használható Azure PowerShell-parancsfájl.
   5. **.NET** – A sablon üzembe helyezéséhez használható .NET-osztály.
   6. **Ruby** – A sablon üzembe helyezéséhez használható Ruby-osztály.
      
      Alapértelmezés szerint a portál megjeleníti a sablont.
      
       ![Sablon megtekintése](./media/resource-manager-export-template/see-template.png)
      
Ez maga a sablon, amelyet a webalkalmazás és az SQL-adatbázis létrehozásához használt. Figyelje meg, hogy a benne szereplő paraméterek különböző értékek megadását is lehetővé teszik az üzembe helyezés során. A sablonok struktúrájával kapcsolatos további információk: [Azure Resource Manager-sablonok készítése](resource-group-authoring-templates.md).

## <a name="export-the-template-from-resource-group"></a>Az erőforráscsoport sablonjának exportálása
Ha Ön már manuálisan az erőforrások új és módosított erőforrások több telepítések esetén, egy sablon lekérése az üzembe helyezési előzményeket nem aktuális állapotát jeleníti meg az erőforráscsoport. Ez a szakasz bemutatja, hogyan exportálhat az erőforráscsoport aktuális állapotát tükröző sablont. Az erőforráscsoport, amelyet felhasználhat az ugyanabban az erőforráscsoportban újratelepíteni pillanatképként szolgál. Az exportált sablon használata az egyéb megoldások, jelentősen módosítania kell azt.

> [!NOTE]
> Több mint 200 erőforrásokat tartalmazó erőforráscsoport a sablon nem exportálható.
> 
> 

1. Az egyes erőforráscsoportok sablonjának megtekintéséhez válassza az **Automation-szkript** lehetőséget.
   
      ![Erőforráscsoportok exportálása](./media/resource-manager-export-template/select-automation.png)
   
     A Resource Manager kiértékeli az erőforráscsoportban lévő erőforrásokat, és létrehoz egy sablont ezekhez az erőforrásokhoz. A sablonexportálási funkciót nem támogatja az összes erőforrástípus. Előfordulhat, hogy egy hibaüzenet jelenik meg, amely arról tájékoztatja, hogy az exportálás során probléma merült fel. Ezeket a hibákat [Az exportálással kapcsolatos problémák megoldása](#fix-export-issues) című részben szereplő információk segítségével oldhatja meg.
2. Ismét megjelenik a megoldás újbóli üzembe helyezéséhez használható hat fájl. De ezúttal a sablon némileg eltérően jelenik meg. Figyelje meg, hogy a létrejött sablon kevesebb paramétert tartalmaz, mint az előző szakasz sablonja. Ezenkívül sok érték (például a hely és a termékváltozat) paraméterértékek fogadása helyett nem módosíthatóként jelenik meg ebben a sablonban. A sablon újbóli használata előtt érdemes lehet szerkeszteni a sablont, hogy jobban kihasználja a paraméterekben rejlő lehetőségeket. 
   
3. Van néhány további lehetőség is arra, hogy továbbra is ezzel a sablonnal dolgozzon. Letöltheti a sablont, és dolgozhat rajta helyben egy JSON-szerkesztővel. Mentheti a sablont a saját tárába, és a portálon keresztül dolgozhat rajta.
   
     Ha járatos egy JSON-szerkesztő, például a [VS Code](https://code.visualstudio.com/) vagy a [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) használatában, érdemes a sablont helyileg letölteni, és az adott szerkesztőt használni. Ha helyben kíván dolgozni, válassza a **Letöltés** lehetőséget.
   
      ![Sablon letöltése](./media/resource-manager-export-template/download-template.png)
   
     Ha nem állít be egy JSON-szerkesztővel, előfordulhat, hogy inkább a sablon a portálon keresztül szerkesztése. Ez a cikk többi azt feltételezi, hogy a sablon a portálon a könyvtárba mentett. Azonban ugyanazon szintaxismódosításokat végezheti el a sablonon a JSON-szerkesztőben helyileg és a portálon keresztül egyaránt. Ha a portálon keresztül dolgozna, válassza a **Hozzáadás a dokumentumtárhoz** lehetőséget.
   
      ![Hozzáadás a dokumentumtárhoz](./media/resource-manager-export-template/add-to-library.png)
   
     A könyvtárhoz történő hozzáadásával egy sablon, amikor kell adnia a sablon nevét és leírását. Ezt követően válassza a **Mentés** lehetőséget.
   
     ![sablon értékeket](./media/resource-manager-export-template/save-library-template.png)
4. Egy, a saját tárába mentett sablon megtekintéséhez válassza a **További szolgáltatások** lehetőséget, írja be a **Sablonok** szöveget az eredmények szűréséhez, majd válassza a **Sablonok** elemet.
   
      ![sablonok](./media/resource-manager-export-template/find-templates.png)
5. Válassza mentett nevű sablont.
   
      ![Sablon kiválasztása](./media/resource-manager-export-template/select-saved-template.png)

## <a name="customize-the-template"></a>A sablon testreszabása
Az exportált sablon jól működik, ha ugyanazt a webalkalmazást és SQL-adatbázist szeretné létrehozni mindegyik üzemelő példányhoz. A Resource Manager által nyújtott lehetőségek azonban lehetővé teszik, hogy a sablonokat rugalmasabb módon helyezze üzembe. Ez a cikk bemutatja, hogyan adhat paramétereket az adatbázis rendszergazdai nevéhez és jelszavához. Ugyanezzel a módszerrel láthatja el további rugalmassággal a sablon más értékeit.

1. A sablon testreszabásához válassza a **Szerkesztés** lehetőséget.
   
     ![Sablon megjelenítése](./media/resource-manager-export-template/select-edit.png)
2. Válassza ki a sablont.
   
     ![Sablon szerkesztése](./media/resource-manager-export-template/select-added-template.png)
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
   
     ![Sablon mentése](./media/resource-manager-export-template/save-template.png)
8. A frissített sablon újbóli üzembe helyezéséhez válassza a **Telepítés** lehetőséget.
   
     ![Sablon üzembe helyezése](./media/resource-manager-export-template/redeploy-template.png)
9. Adja meg a paraméterértékeket, és válassza ki azt az erőforráscsoportot, amelybe az erőforrásokat üzembe kívánja helyezni.


## <a name="fix-export-issues"></a>Az exportálással kapcsolatos problémák megoldása
A sablonexportálási funkciót nem támogatja az összes erőforrástípus. A probléma megoldásához adja hozzá ismét manuálisan a hiányzó erőforrásokat a sablonhoz. A hibaüzenetben szerepelnek a nem exportálható erőforrástípusok. Ezt az erőforrástípust a [Sablonreferenciában](/azure/templates/) találja. Ha például manuálisan szeretne hozzáadni egy virtuális hálózati átjárót, lásd a [Microsoft.Network/virtualNetworkGateways sablonreferenciát](/azure/templates/microsoft.network/virtualnetworkgateways).

> [!NOTE]
> Az exportálási hibák csak akkor lépnek fel, ha az erőforráscsoportból, és nem az üzembe helyezési előzmények közül végez exportálást. Ha a legutóbbi üzembe helyezés pontosan tükrözi az erőforráscsoport aktuális állapotát, érdemes az erőforráscsoport helyett az üzembe helyezési előzmények közül elvégezni a sablon exportálását. Csak akkor exportáljon az erőforráscsoportból, ha olyan módosításokat végzett rajta, amelyeket nem lehet egyetlen sablonnal definiálni.
> 
> 

## <a name="next-steps"></a>További lépések

* Sablont a [PowerShell](resource-group-template-deploy.md), az [Azure parancssori felülete](resource-group-template-deploy-cli.md) vagy a [REST API](resource-group-template-deploy-rest.md) használatával helyezhet üzembe.
* A sablonok Powershellen keresztül exportálása, olvassa el [exportálása Azure Resource Manager-sablon is van a PowerShell](resource-manager-export-template-powershell.md).
* Azure parancssori felületen keresztül sablon exportálása, olvassa el [exportálása Azure Resource Manager-sablon is van az Azure parancssori felület](resource-manager-export-template-cli.md).

