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
ms.topic: get-started-article
ms.date: 03/30/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: f41fbee742daf2107b57caa528e53537018c88c6
ms.openlocfilehash: cee4748a0b24e11cd8a8ee46471418680fcf7b33
ms.lasthandoff: 03/31/2017


---
# <a name="export-an-azure-resource-manager-template-from-existing-resources"></a>Azure Resource Manager-sablonok exportálása létező erőforrásokból
A Resource Manager lehetővé teszi az előfizetéshez tartozó meglévő erőforrások Resource Manager-sablonjainak exportálását. Az így létrehozott sablon használatával megismerheti a sablonok szintaxisát, illetve igény szerint automatizálhatja a megoldás újbóli telepítését.

Fontos megjegyezni, hogy sablonokat két különböző módon lehet exportálni:

* Exportálhatja az üzembe helyezéshez is használt tényleges sablont. Ebben az esetben az exportált sablon pontosan úgy tartalmazza a különböző paramétereket és változókat, ahogy azok az eredeti sablonban szerepeltek. Ez a megközelítés akkor lehet hasznos, ha a portálon keresztül helyezte üzembe az erőforrásokat, és most szeretné megtudni, hogy hogyan alkossa meg az ezekhez az erőforrásokhoz használt sablont.
* A másik megoldás, hogy úgy exportálja a sablont, hogy az az erőforráscsoport aktuális állapotát tükrözze. Ebben az esetben az exportált sablon nem az üzembe helyezéshez használt sablonon alapul. A rendszer ehelyett új sablont hoz létre az erőforráscsoport aktuális állapota alapján. Az exportált sablon számos nem módosítható értéket tartalmaz, és valószínűleg kevesebb paraméter található benne, mint amennyit általában használni szokott. Ez a megközelítés akkor lehet hasznos, ha a portálon vagy parancsprogramok segítségével módosította az erőforráscsoportot, és most szeretne létrehozni egy sablont az így létrejött egyedi erőforráscsoport alapján.

Ebben a témakörben mind a két megoldást bemutatjuk.

Ebben az oktatóanyagban be fog jelentkezni az Azure Portalra, létrehoz egy tárfiókot, majd ebbe a tárfiókba exportálja a sablont. Emellett hozzá fog adni egy virtuális hálózatot az erőforráscsoport módosításához. Végül pedig exportálja az aktuális állapotot képviselő új sablont. Habár a jelen cikk egy egyszerűsített infrastruktúrára összpontosít, ugyanezen lépések használatával egy bonyolultabb megoldás sablonját is exportálhatja.

## <a name="create-a-storage-account"></a>Create a storage account
1. Az [Azure-portálon](https://portal.azure.com) válassza az **Új** > **Tárolás** > **Tárfiók** lehetőséget.
   
      ![tároló létrehozása](./media/resource-manager-export-template/create-storage.png)
2. Hozzon létre egy tárfiókot **storage** névvel, saját monogramjával, valamint az aktuális dátummal. A tárfiók nevének egyedinek kell lennie az egész Azure rendszerben. Ha a név már használatban van, megjelenik egy üzenet, miszerint a név használatban van. Próbálkozzon egy módosított névvel. Az erőforráscsoporthoz válassza az **Új létrehozása** lehetőséget, majd adja neki az **ExportGroup** nevet. A többi tulajdonság esetén használhatja az alapértelmezett értékeket. Kattintson a **Létrehozás** gombra.
   
      ![a tároló értékeinek megadása](./media/resource-manager-export-template/provide-storage-values.png)

Az üzembe helyezés egy percig is eltarthat. Miután a telepítés végzett, az előfizetése tartalmazza majd a tárfiókot.

## <a name="view-a-template-from-deployment-history"></a>Sablon megtekintése az üzembe helyezési előzményekből
1. Nyissa meg az új erőforráscsoport paneljét. Figyelje meg, hogy a panelen a legutóbbi üzembe helyezés részletes adatai láthatók. Kattintson erre a hivatkozásra.
   
      ![erőforráscsoport panel](./media/resource-manager-export-template/resource-group-blade.png)
2. Megjelennek a csoport üzembe helyezési előzményei. Az Ön esetében valószínűleg csak egyetlen üzembe helyezés látható a panelen. Válassza ki ezt a telepítést.
   
     ![legutóbbi telepítés](./media/resource-manager-export-template/last-deployment.png)
3. A panelen megjelenik az üzembe helyezés összegzése. Az összegzés tartalmazza a telepítés, valamint annak műveleteinek állapotát, és a paraméterek számára megadott értékeket. Az üzembe helyezéshez használt sablon megtekintéséhez válassza a **Sablon megtekintése** lehetőséget.
   
     ![telepítés összegzésének megtekintése](./media/resource-manager-export-template/deployment-summary.png)
4. A Resource Manager az alábbi hét fájlt kéri le:
   
   1. **Sablon** – A megoldás infrastruktúráját meghatározó sablon. A tárfiók a portálon keresztül történő létrehozásakor a Resource Manager egy sablon használatával telepítette azt, és elmentette ezt a sablont későbbi felhasználás céljából.
   2. **Paraméterek** – Az értékek az üzembe helyezés során történő megadásához szükséges paraméterfájl. Ez tartalmazza az első telepítés során megadott értékeket, de ezek bármelyike módosítható a sablon újbóli telepítése során.
   3. **CLI** – A sablon üzembe helyezéséhez használható Azure CLI-parancsfájl.
   3. **CLI 2.0** – A sablon üzembe helyezéséhez használható Azure CLI-szkriptfájl.
   4. **PowerShell** – A sablon üzembe helyezéséhez használható Azure PowerShell-parancsfájl.
   5. **.NET** – A sablon üzembe helyezéséhez használható .NET-osztály.
   6. **Ruby** – A sablon üzembe helyezéséhez használható Ruby-osztály.
      
      A fájlok a panelen található hivatkozásokon keresztül érhetők el. Alapértelmezés szerint a panelben a sablon jelenik meg.
      
       ![sablon megtekintése](./media/resource-manager-export-template/view-template.png)
      
      Különösen figyeljen oda a sablonra. A sablonnak az alábbihoz hasonlónak kell lennie:
      
      ```json
      {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
          "name": {
            "type": "String"
          },
          "accountType": {
            "type": "String"
          },
          "location": {
            "type": "String"
          },
          "encryptionEnabled": {
            "defaultValue": false,
            "type": "Bool"
          }
        },
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "sku": {
              "name": "[parameters('accountType')]"
            },
            "kind": "Storage",
            "name": "[parameters('name')]",
            "apiVersion": "2016-01-01",
            "location": "[parameters('location')]",
            "properties": {
              "encryption": {
                "services": {
                  "blob": {
                    "enabled": "[parameters('encryptionEnabled')]"
                  }
                },
                "keySource": "Microsoft.Storage"
              }
            }
          }
        ]
      }
      ```

Ez maga a sablon, amelyet a tárfiók létrehozásához használt. Figyelje meg, hogy a benne szereplő paraméterek különböző típusú tárfiókok üzembe helyezését is lehetővé teszik. A sablonok struktúrájával kapcsolatos további információk: [Azure Resource Manager-sablonok készítése](resource-group-authoring-templates.md). A sablonokban használható függvények teljes listájáért olvassa el a következő cikket: [Azure Resource Manager template functions](resource-group-template-functions.md) (Az Azure Resource Manager-sablonokban használható függvények).

## <a name="add-a-virtual-network"></a>Virtuális hálózat hozzáadása
Az előző szakaszban letöltött sablon az eredeti üzembe helyezés infrastruktúráját tükrözi. Az üzembe helyezést követően végzett módosításokat azonban nem tartalmazza.
A probléma bemutatása érdekében módosítsa az erőforráscsoportot, és adjon hozzá egy virtuális hálózatot a portálon keresztül.

1. Az erőforráscsoport paneljén válassza a **Hozzáadás** elemet.
   
      ![erőforrás hozzáadása](./media/resource-manager-export-template/add-resource.png)
2. Az elérhető erőforrások közül válassza a következőt: **Virtuális hálózat**.
   
      ![virtuális hálózat kiválasztása](./media/resource-manager-export-template/select-vnet.png)
3. A virtuális hálózatnak adja meg a **VNET** nevet, majd a többi tulajdonság esetén használja az alapértelmezett értékeket. Kattintson a **Létrehozás** gombra.
   
      ![riasztás beállítása](./media/resource-manager-export-template/create-vnet.png)
4. Miután sikeresen telepítette a virtuális hálózatot az erőforráscsoportba, tekintse meg ismét a telepítési előzményeket. Most már két üzembe helyezés jelenik itt meg. Ha nem látja a második üzembe helyezést, próbálja meg bezárni, majd újra megnyitni az erőforráscsoport paneljét. Válassza ki az újabb telepítést.
   
      ![telepítési előzmények](./media/resource-manager-export-template/deployment-history.png)
5. Tekintse meg a telepítés sablonját. Figyelje meg, hogy csak a virtuális hálózatot definiálja. Nem tartalmazza a korábban telepített tárfiókot. Már nem rendelkezik a csoport összes erőforrását képviselő sablonnal.

## <a name="export-the-template-from-resource-group"></a>Az erőforráscsoport sablonjának exportálása
Az erőforráscsoport aktuális állapotának megismeréséhez exportáljon egy, az erőforráscsoport pillanatképét megjelenítő sablont.  

> [!NOTE]
> Több mint 200 erőforrással rendelkező erőforráscsoport esetében nem exportálhat sablont.
> 
> 

1. Az egyes erőforráscsoportok sablonjának megtekintéséhez válassza az **Automation-szkript** lehetőséget.
   
      ![erőforráscsoportok exportálása](./media/resource-manager-export-template/export-resource-group.png)
   
     A sablonexportálási funkciót nem támogatja az összes erőforrástípus. Ha az erőforráscsoportban a cikkben említettek szerint kizárólag tárfiók és virtuális hálózat szerepel, nem kap hibaüzenetet. Ha azonban más típusú erőforrásokat is létrehozott, előfordulhat, hogy hibaüzenet jelenik meg, amely tájékoztatja, hogy az exportálás során probléma merült fel. Ezeket a hibákat [Az exportálással kapcsolatos problémák megoldása](#fix-export-issues) című részben szereplő információk segítségével oldhatja meg.
2. Ismét megjelenik a megoldás újbóli üzembe helyezéséhez használható hat fájl, de ezúttal a sablon némileg eltérően jelenik meg. Ez a sablon csak két paraméterrel rendelkezik: a tárfiók nevével, valamint a virtuális hálózat nevével.

   ```json
   "parameters": {
     "virtualNetworks_VNET_name": {
       "defaultValue": "VNET",
       "type": "String"
     },
     "storageAccounts_storagetf05092016_name": {
       "defaultValue": "storagetf05092016",
       "type": "String"
     }
   },
   ```
   
   A Resource Manager nem kérte le az üzembe helyezéshez használt sablonokat. Ehelyett létrehozott egy új sablont az erőforrások aktuális konfigurációja alapján. A sablon például a következő beállítást tartalmazza a tárfiók helyére és a replikálási értékre vonatkozóan:

   ```json 
   "location": "northeurope",
   "tags": {},
   "properties": {
     "accountType": "Standard_RAGRS"
   },
   ```
3. Van néhány további lehetőség is arra, hogy továbbra is ezzel a sablonnal dolgozzon. Letöltheti a sablont, és dolgozhat rajta helyben egy JSON-szerkesztővel. Mentheti a sablont a saját tárába, és a portálon keresztül dolgozhat rajta.
   
     Ha járatos egy JSON-szerkesztő, például a [VS Code](resource-manager-vs-code.md) vagy a [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) használatában, érdemes a sablont helyileg letölteni, és az adott szerkesztőt használni. Ha nem állította be a JSON-szerkesztőt, inkább a portálon keresztül szerkessze a sablont. A témakör további részében azt feltételezzük, hogy mentette a sablont a saját tárába a portálon. Azonban ugyanazon szintaxismódosításokat végezheti el a sablonon a JSON-szerkesztőben helyileg és a portálon keresztül egyaránt.
   
     Ha helyben kíván dolgozni, válassza a **Letöltés** lehetőséget.
   
      ![sablon letöltése](./media/resource-manager-export-template/download-template.png)
   
     Ha a portálon keresztül dolgozna, válassza a **Hozzáadás a dokumentumtárhoz** lehetőséget.
   
      ![hozzáadás a dokumentumtárhoz](./media/resource-manager-export-template/add-to-library.png)
   
     Egy sablonnak a dokumentumtárhoz való hozzáadásakor adja meg a sablon nevét és leírását. Ezt követően válassza a **Mentés** lehetőséget.
   
     ![sablon értékeinek megadása](./media/resource-manager-export-template/set-template-values.png)
4. Egy, a saját tárába mentett sablon megtekintéséhez válassza a **További szolgáltatások** lehetőséget, írja be a **Sablonok** szöveget az eredmények szűréséhez, majd válassza a **Sablonok** elemet.
   
      ![sablonok keresése](./media/resource-manager-export-template/find-templates.png)
5. Válassza mentett nevű sablont.
   
      ![sablon kiválasztása](./media/resource-manager-export-template/select-library-template.png)

## <a name="customize-the-template"></a>A sablon testreszabása
Az exportált sablon jól működik, ha ugyanazt a tárfiókot és a virtuális hálózatot szeretné létrehozni mindegyik üzemelő példányhoz. A Resource Manager által nyújtott lehetőségek azonban lehetővé teszik, hogy a sablonokat rugalmasabb módon helyezze üzembe. Előfordulhat például, hogy telepítés során meg szeretné adni a létrehozni kívánt tárfiók típusát, vagy a virtuális hálózat címelőtagjához és alhálózati előtagjához használni kívánt értékeket.

Ebben a szakaszban paramétereket adhat hozzá az exportált sablonhoz, hogy az erőforrások más környezetekben való üzembe helyezése során újra felhasználhassa azt. Emellett néhány olyan funkciót is hozzáadhat a sablonhoz, amelyekkel csökkentheti annak az esélyét, hogy hibába ütközik annak üzembe helyezése során. Többé már nincs szüksége arra, hogy egyedi nevet találjon ki tárfiókjának. Ön helyett ugyanis a sablon hozza létre az egyedi nevet. Korlátozhatja a tárfiók típusához megadható értékeket csak az érvényes lehetőségekre.

1. A sablon testreszabásához válassza a **Szerkesztés** lehetőséget.
   
     ![sablon megjelenítése](./media/resource-manager-export-template/show-template.png)
2. Válassza ki a sablont.
   
     ![sablon szerkesztése](./media/resource-manager-export-template/edit-template.png)
3. A telepítés során meghatározni kívánt értékek megadásához cserélje le a **parameters** (paraméterek) szakasz tartalmát az új paraméterdefiníciókra. Figyelje meg a **storageAccount_accountType** paraméter **allowedValues** engedélyezett értékeit. Ha véletlenül érvénytelen értéket ad meg, a rendszer felismeri a hibát még a telepítés megkezdése előtt. Emellett vegye figyelembe azt is, hogy csak egy előtagot adott meg a tárfiók nevéhez, és az előtag legfeljebb 11 karakter lehet. Az előtag 11 karakterre történő korlátozásával biztosíthatja, hogy a teljes név nem haladja meg a tárfiókok esetén megengedett maximális karakterszámot. Az előtag lehetővé teszi, hogy elnevezési szabályokat alkalmazzon tárfiókjaira. Az egyedi nevek létrehozását a következő lépésben ismerheti meg.

   ```json
   "parameters": {
     "storageAccount_prefix": {
       "type": "string",
       "maxLength": 11
     },
     "storageAccount_accountType": {
       "defaultValue": "Standard_RAGRS",
       "type": "string",
       "allowedValues": [
         "Standard_LRS",
         "Standard_ZRS",
         "Standard_GRS",
         "Standard_RAGRS",
         "Premium_LRS"
       ]
     },
     "virtualNetwork_name": {
       "type": "string"
     },
     "addressPrefix": {
       "defaultValue": "10.0.0.0/16",
       "type": "string"
     },
     "subnetName": {
       "defaultValue": "subnet-1",
       "type": "string"
     },
     "subnetAddressPrefix": {
       "defaultValue": "10.0.0.0/24",
       "type": "string"
     }
   },
   ```

4. A sablon **variables** (változók) szakasza jelenleg üres. A **variables** (változók) szakaszban olyan értékeket hozhat létre, amelyekkel leegyszerűsíthető a sablon további szintaxisa. Ez a szakasz cserélje ki egy új változódefinícióra. A **storageAccount_name** változó összefűzi a paraméterből származó előtagot egy egyedi karakterlánccá, amelyet a rendszer az erőforráscsoport azonosítója alapján hoz létre. Már nem kell egy egyedi nevet kitalálnia a paraméter értékének megadásakor.

   ```json
   "variables": {
     "storageAccount_name": "[concat(parameters('storageAccount_prefix'), uniqueString(resourceGroup().id))]"
   },
   ```

5. Ha a paramétereket és változókat az erőforrás-definíciókban is használni szeretné, cserélje le a **resources** (erőforrások) szakaszt az új erőforrás-definíciókra. Figyelje meg, hogy az erőforrás-tulajdonsághoz rendelt értéken kívül kevés változás történt az erőforrás-definíciókban. A tulajdonságok megegyeznek az exportált sablon tulajdonságaival. Egyszerűen a nem változtatható értékek helyett a paraméter-értékekhez rendelt hozzá tulajdonságokat. Az erőforrások helye úgy van beállítva a **resourceGroup().location** kifejezésen keresztül, hogy az erőforráscsoporttal megegyező legyen. A tárfiók nevéhez létrehozott változóra a **variables** kifejezés hivatkozik.

   ```json
   "resources": [
     {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "[parameters('virtualNetwork_name')]",
       "apiVersion": "2015-06-15",
       "location": "[resourceGroup().location]",
       "properties": {
         "addressSpace": {
           "addressPrefixes": [
             "[parameters('addressPrefix')]"
           ]
         },
         "subnets": [
           {
             "name": "[parameters('subnetName')]",
             "properties": {
               "addressPrefix": "[parameters('subnetAddressPrefix')]"
             }
           }
         ]
       },
       "dependsOn": []
     },
     {
       "type": "Microsoft.Storage/storageAccounts",
       "name": "[variables('storageAccount_name')]",
       "apiVersion": "2015-06-15",
       "location": "[resourceGroup().location]",
       "tags": {},
       "properties": {
         "accountType": "[parameters('storageAccount_accountType')]"
       },
       "dependsOn": []
     }
   ]
   ```

6. Amikor befejezte a sablon szerkesztését, kattintson az **OK** gombra.
7. Kattintson a **Mentés** gombra a sablon módosításainak mentéséhez.
   
     ![sablon mentése](./media/resource-manager-export-template/save-template.png)
8. A frissített sablon üzembe helyezéséhez válassza a **Telepítés** lehetőséget.
   
     ![sablon üzembe helyezése](./media/resource-manager-export-template/deploy-template.png)
9. Adja meg a paraméterértékeket, és válassza ki az új erőforráscsoportot, amelybe az erőforrásokat üzembe kívánja helyezni.

## <a name="update-the-downloaded-parameters-file"></a>A letöltött paraméterfájl frissítése
Ha a letöltött fájlokkal dolgozik (a portál dokumentumtára helyett), frissítse a letöltött paraméterfájlt. Az ugyanis már nem egyezik a sablonban szereplő paraméterekkel. A paraméterfájl használata nem kötelező, de leegyszerűsítheti a környezetek újbóli telepítésének folyamatát. Számos paraméter esetében a sablonban megadott alapértelmezett értéket használja, így a paraméterfájlnak csupán két értéket kell tartalmaznia.

Cserélje le a parameters.json fájl tartalmát az alábbira:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccount_prefix": {
      "value": "storage"
    },
    "virtualNetwork_name": {
      "value": "VNET"
    }
  }
}
```

A frissített paraméterfájl csak azon paraméterekhez tartalmaz értékeket, amelyek nem rendelkeznek alapértelmezett értékkel. A többi paraméter esetén is megadhat értékeket, ha az alapértelmezett értéktől eltérőt szeretne használni.

## <a name="fix-export-issues"></a>Az exportálással kapcsolatos problémák megoldása
A sablonexportálási funkciót nem támogatja az összes erőforrástípus. A Resource Manager a bizalmas adatok megóvása érdekében szándékosan nem exportál bizonyos erőforrástípusokat. Valószínű például, hogy nem szeretné, hogy az oldal konfigurációjában található kapcsolati karakterlánc szerepeljen az exportált sablonban. A probléma megoldásához adja hozzá ismét manuálisan a hiányzó erőforrásokat a sablonhoz.

> [!NOTE]
> Az exportálási hibák csak akkor lépnek fel, ha az erőforráscsoportból, és nem az üzembe helyezési előzmények közül végez exportálást. Ha a legutóbbi üzembe helyezés pontosan tükrözi az erőforráscsoport aktuális állapotát, érdemes az erőforráscsoport helyett az üzembe helyezési előzmények közül elvégezni a sablon exportálását. Csak akkor exportáljon az erőforráscsoportból, ha olyan módosításokat végzett rajta, amelyeket nem lehet egyetlen sablonnal definiálni.
> 
> 

Ha például az erőforráscsoport helykonfigurációjában, amelynek sablonját exportálni szeretné webalkalmazás, SQL Database-adatbázis és kapcsolati karakterlánc egyaránt található, az alábbi üzenet jelenik meg:

![hibaüzenet](./media/resource-manager-export-template/show-error.png)

Válassza ki az üzenetet, és a rendszer megjeleníti, hogy mely erőforrástípusokat nem exportált. 

![hibaüzenet](./media/resource-manager-export-template/show-error-details.png)

Ez a témakör a gyakori javításokat mutatja be.

### <a name="connection-string"></a>Kapcsolati karakterlánc
A webhely erőforrásainál adja hozzá a kapcsolati karakterlánc definícióját az adatbázishoz:

```json
{
  "type": "Microsoft.Web/sites",
  ...
  "resources": [
    {
      "apiVersion": "2015-08-01",
      "type": "config",
      "name": "connectionstrings",
      "dependsOn": [
          "[concat('Microsoft.Web/Sites/', parameters('<site-name>'))]"
      ],
      "properties": {
          "DefaultConnection": {
            "value": "[concat('Data Source=tcp:', reference(concat('Microsoft.Sql/servers/', parameters('<database-server-name>'))).fullyQualifiedDomainName, ',1433;Initial Catalog=', parameters('<database-name>'), ';User Id=', parameters('<admin-login>'), '@', parameters('<database-server-name>'), ';Password=', parameters('<admin-password>'), ';')]",
              "type": "SQLServer"
          }
      }
    }
  ]
}
```    

### <a name="web-site-extension"></a>Webhely bővítménye
A webhely erőforrásainál adja hozzá a kód definícióját a telepítés érdekében:

```json
{
  "type": "Microsoft.Web/sites",
  ...
  "resources": [
    {
      "name": "MSDeploy",
      "type": "extensions",
      "location": "[resourceGroup().location]",
      "apiVersion": "2015-08-01",
      "dependsOn": [
        "[concat('Microsoft.Web/sites/', parameters('<site-name>'))]"
      ],
      "properties": {
        "packageUri": "[concat(parameters('<artifacts-location>'), '/', parameters('<package-folder>'), '/', parameters('<package-file-name>'), parameters('<sas-token>'))]",
        "dbType": "None",
        "connectionString": "",
        "setParameters": {
          "IIS Web Application Name": "[parameters('<site-name>')]"
        }
      }
    }
  ]
}
```

### <a name="virtual-machine-extension"></a>Virtuális gépi bővítmény
A virtuális gépekre vonatkozó példákért lásd: [Azure Windows VM Extension Configuration Samples](../virtual-machines/windows/extensions-configuration-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Windowsos Azure virtuális gépi bővítmények konfigurációs mintái).

### <a name="virtual-network-gateway"></a>Virtuális hálózati átjáró
Adjon hozzá egy virtuális hálózati átjáró erőforrástípust.

```json
{
  "type": "Microsoft.Network/virtualNetworkGateways",
  "name": "[parameters('<gateway-name>')]",
  "apiVersion": "2015-06-15",
  "location": "[resourceGroup().location]",
  "properties": {
    "gatewayType": "[parameters('<gateway-type>')]",
    "ipConfigurations": [
      {
        "name": "default",
        "properties": {
          "privateIPAllocationMethod": "Dynamic",
          "subnet": {
            "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('<vnet-name>'), parameters('<new-subnet-name>'))]"
          },
          "publicIpAddress": {
            "id": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('<new-public-ip-address-Name>'))]"
          }
        }
      }
    ],
    "enableBgp": false,
    "vpnType": "[parameters('<vpn-type>')]"
  },
  "dependsOn": [
    "Microsoft.Network/virtualNetworks/codegroup4/subnets/GatewaySubnet",
    "[concat('Microsoft.Network/publicIPAddresses/', parameters('<new-public-ip-address-Name>'))]"
  ]
},
```

### <a name="local-network-gateway"></a>Helyi hálózati átjáró
Adjon hozzá egy helyi hálózati átjáró erőforrástípust.

```json
{
    "type": "Microsoft.Network/localNetworkGateways",
    "name": "[parameters('<local-network-gateway-name>')]",
    "apiVersion": "2015-06-15",
    "location": "[resourceGroup().location]",
    "properties": {
      "localNetworkAddressSpace": {
        "addressPrefixes": "[parameters('<address-prefixes>')]"
      }
    }
}
```

### <a name="connection"></a>Kapcsolat
Adjon hozzá egy kapcsolat erőforrástípust.

```json
{
    "apiVersion": "2015-06-15",
    "name": "[parameters('<connection-name>')]",
    "type": "Microsoft.Network/connections",
    "location": "[resourceGroup().location]",
    "properties": {
        "virtualNetworkGateway1": {
        "id": "[resourceId('Microsoft.Network/virtualNetworkGateways', parameters('<gateway-name>'))]"
      },
      "localNetworkGateway2": {
        "id": "[resourceId('Microsoft.Network/localNetworkGateways', parameters('<local-gateway-name>'))]"
      },
      "connectionType": "IPsec",
      "routingWeight": 10,
      "sharedKey": "[parameters('<shared-key>')]"
    }
},
```


## <a name="next-steps"></a>Következő lépések
Gratulálunk! Megtanulta, hogyan exportálhat sablonokat a portálon létrehozott erőforrásokból.

* Sablont a [PowerShell](resource-group-template-deploy.md), az [Azure parancssori felülete](resource-group-template-deploy-cli.md) vagy a [REST API](resource-group-template-deploy-rest.md) használatával helyezhet üzembe.
* A sablonok PowerShellen keresztül történő exportálásával kapcsolatos információk: [Az Azure PowerShell használata Azure Resource Managerrel](powershell-azure-resource-manager.md).
* A sablonok az Azure parancssori felületen keresztül történő exportálásával kapcsolatos információk: [A Mac, Linux és Windows eszközökhöz készült Azure CLI használata az Azure Resource Manager eszközzel](xplat-cli-azure-resource-manager.md).


