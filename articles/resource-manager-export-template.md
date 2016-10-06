<properties
    pageTitle="Azure Resource Manager-sablonok exportálása | Microsoft Azure"
    description="Az Azure Resource Manager használatával sablonokat exportálhat létező erőforráscsoportokból."
    services="azure-resource-manager"
    documentationCenter=""
    authors="tfitzmac"
    manager="timlt"
    editor="tysonn"/>

<tags
    ms.service="azure-resource-manager"
    ms.workload="multiple"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/03/2016"
    ms.author="tomfitz"/>


# Azure Resource Manager-sablonok exportálása létező erőforrásokból

A Resource Manager lehetővé teszi az előfizetéshez tartozó meglévő erőforrások Resource Manager-sablonjainak exportálását. Az így létrehozott sablon használatával megismerheti a sablonok szintaxisát, illetve igény szerint automatizálhatja a megoldás újbóli telepítését.

Fontos megjegyezni, hogy sablonokat két különböző módon lehet exportálni:

- Exportálhatja az üzembe helyezéshez is használt tényleges sablont. Ebben az esetben az exportált sablon pontosan úgy tartalmazza a különböző paramétereket és változókat, ahogy azok az eredeti sablonban szerepeltek. Ez a megközelítés akkor lehet hasznos, ha a portálon keresztül helyezte üzembe az erőforrásokat, és most szeretné megtudni, hogy hogyan alkossa meg az ezekhez az erőforrásokhoz használt sablont.
- A másik megoldás, hogy úgy exportálja a sablont, hogy az az erőforráscsoport aktuális állapotát tükrözze. Ebben az esetben az exportált sablon nem az üzembe helyezéshez használt sablonon alapul. A rendszer ehelyett új sablont hoz létre az erőforráscsoport aktuális állapota alapján. Az exportált sablon számos nem módosítható értéket tartalmaz, és valószínűleg kevesebb paraméter található benne, mint amennyit általában használni szokott. Ez a megközelítés akkor lehet hasznos, ha a portálon vagy parancsprogramok segítségével módosította az erőforráscsoportot, és most szeretne létrehozni egy sablont az így létrejött egyedi erőforráscsoport alapján.

Ebben a témakörben mind a két megoldást bemutatjuk. A [Customize an exported Azure Resource Manager template](resource-manager-customize-template.md) (Exportált Azure Resource Manager-sablon testreszabása) című cikkből megtudhatja, hogyan alakíthatja át az erőforráscsoport aktuális állapotát tükröző sablonokat úgy, hogy azok megkönnyítsék a megoldás ismételt üzembe helyezését.

Ebben az oktatóanyagban be fog jelentkezni az Azure Portalra, létrehoz egy tárfiókot, majd ebbe a tárfiókba exportálja a sablont. Emellett hozzá fog adni egy virtuális hálózatot az erőforráscsoport módosításához. Végül pedig exportálja az aktuális állapotot képviselő új sablont. Habár a jelen cikk egy egyszerűsített infrastruktúrára összpontosít, ugyanezen lépések használatával egy bonyolultabb megoldás sablonját is exportálhatja.

## Create a storage account

1. Az [Azure portálon](https://portal.azure.com) válassza az **Új** > **Adatok és Tárolás** > **Tárfiók** lehetőséget.

      ![tároló létrehozása](./media/resource-manager-export-template/create-storage.png)

2. Hozzon létre egy tárfiókot **storage** névvel, saját monogramjával, valamint az aktuális dátummal. A tárfiók nevének egyedinek kell lennie az egész Azure rendszerben. Ha először egy már használatban lévő nevet ad meg, próbálkozzon meg annak egy variációjával. Erőforráscsoportnak adja meg az **ExportGroup** csoportot. A többi tulajdonság esetén használhatja az alapértelmezett értékeket. Kattintson a **Létrehozás** gombra.

      ![a tároló értékeinek megadása](./media/resource-manager-export-template/provide-storage-values.png)

Miután a telepítés végzett, az előfizetése tartalmazza majd a tárfiókot.

## Sablon exportálása az üzembe helyezési előzményekből

1. Nyissa meg az új erőforráscsoport paneljét. Figyelje meg, hogy a panelen a legutóbbi üzembe helyezés részletes adatai láthatók. Kattintson erre a hivatkozásra.

      ![erőforráscsoport panel](./media/resource-manager-export-template/resource-group-blade.png)

2. Megjelennek a csoport üzembe helyezési előzményei. Az Ön esetében valószínűleg csak egyetlen üzembe helyezés látható a panelen. Válassza ki ezt a telepítést.

     ![legutóbbi telepítés](./media/resource-manager-export-template/last-deployment.png)

3. A panelen megjelenik az üzembe helyezés összegzése. Az összegzés tartalmazza a telepítés, valamint annak műveleteinek állapotát, és a paraméterek számára megadott értékeket. Az üzembe helyezéshez használt sablon megtekintéséhez válassza a **Sablon megtekintése** lehetőséget.

     ![telepítés összegzésének megtekintése](./media/resource-manager-export-template/deployment-summary.png)

4. A Resource Manager az alábbi hat fájlt kéri le:

   1. **Sablon** – A megoldás infrastruktúráját meghatározó sablon. A tárfiók a portálon keresztül történő létrehozásakor a Resource Manager egy sablon használatával telepítette azt, és elmentette ezt a sablont későbbi felhasználás céljából.
   2. **Paraméterek** – Az értékek az üzembe helyezés során történő megadásához szükséges paraméterfájl. Ez tartalmazza az első telepítés során megadott értékeket, de ezek bármelyike módosítható a sablon újbóli telepítése során.
   3. **CLI** – A sablon üzembe helyezéséhez használható Azure CLI-parancsfájl.
   4. **PowerShell** – A sablon üzembe helyezéséhez használható Azure PowerShell-parancsfájl.
   5. **.NET** – A sablon üzembe helyezéséhez használható .NET-osztály.
   6. **Ruby** – A sablon üzembe helyezéséhez használható Ruby-osztály.

     A fájlok a panelen található hivatkozásokon keresztül érhetők el. Alapértelmezés szerint a panelben a sablon jelenik meg.

       ![sablon megtekintése](./media/resource-manager-export-template/view-template.png)

     Különösen figyeljen oda a sablonra. A sablonnak az alábbihoz hasonlónak kell lennie:

        {     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",     "contentVersion": "1.0.0.0",     "parameters": {       "name": {         "type": "String"       },       "accountType": {         "type": "String"       },       "location": {         "type": "String"       },       "encryptionEnabled": {         "defaultValue": false,         "type": "Bool"       }     },     "resources": [       {         "type": "Microsoft.Storage/storageAccounts",         "sku": {           "name": "[parameters('accountType')]"         },         "kind": "Storage",         "name": "[parameters('name')]",         "apiVersion": "2016-01-01",         "location": "[parameters('location')]",         "properties": {           "encryption": {             "services": {               "blob": {                 "enabled": "[parameters('encryptionEnabled')]"               }             },             "keySource": "Microsoft.Storage"           }         }       }     ]   }
 
Ez maga a sablon, amelyet a tárfiók létrehozásához használt. Figyelje meg, hogy a benne szereplő paraméterek különböző típusú tárfiókok üzembe helyezését is lehetővé teszik. A sablonok struktúrájával kapcsolatos további információk: [Azure Resource Manager-sablonok készítése](resource-group-authoring-templates.md). A sablonokban használható függvények teljes listájáért olvassa el a következő cikket: [Azure Resource Manager template functions](resource-group-template-functions.md) (Az Azure Resource Manager-sablonokban használható függvények).


## Virtuális hálózat hozzáadása

Az előző szakaszban letöltött sablon az eredeti üzembe helyezés infrastruktúráját tükrözi. Az üzembe helyezést követően végzett módosításokat azonban nem tartalmazza.
A probléma bemutatása érdekében módosítsa az erőforráscsoportot, és adjon hozzá egy virtuális hálózatot a portálon keresztül.

1. Az erőforráscsoport paneljén válassza a **Hozzáadás** elemet.

      ![erőforrás hozzáadása](./media/resource-manager-export-template/add-resource.png)

2. Az elérhető erőforrások közül válassza a következőt: **Virtuális hálózat**.

      ![virtuális hálózat kiválasztása](./media/resource-manager-export-template/select-vnet.png)

2. A virtuális hálózatnak adja meg a **VNET** nevet, majd a többi tulajdonság esetén használja az alapértelmezett értékeket. Kattintson a **Létrehozás** gombra.

      ![riasztás beállítása](./media/resource-manager-export-template/create-vnet.png)

3. Miután sikeresen telepítette a virtuális hálózatot az erőforráscsoportba, tekintse meg ismét a telepítési előzményeket. Most már két üzembe helyezés jelenik itt meg. Ha nem látja a második üzembe helyezést, próbálja meg bezárni, majd újra megnyitni az erőforráscsoport paneljét. Válassza ki az újabb telepítést.

      ![telepítési előzmények](./media/resource-manager-export-template/deployment-history.png)

4. Tekintse meg a telepítés sablonját. Vegye figyelembe, hogy ez a sablon csak a virtuális hálózaton végzett módosításokat határozza meg.

Általában célszerű egyetlen sablonnal dolgozni, amely egyetlen műveletben képes telepíteni a megoldás teljes infrastruktúráját. Így nem szükséges több sablon üzembe helyezésével bajlódnia.


## Az erőforráscsoport sablonjának exportálása

Habár minden telepítés csak az erőforráscsoporton végzett módosításokat jeleníti meg, bármikor exportálhat egy olyan sablont, amely megmutatja a teljes erőforráscsoport attribútumait.  

> [AZURE.NOTE] Több mint 200 erőforrással rendelkező erőforráscsoport esetében nem exportálhat sablont.

1. Az egyes erőforráscsoportok sablonjának megtekintéséhez válassza az **Automation-szkript** lehetőséget.

      ![erőforráscsoportok exportálása](./media/resource-manager-export-template/export-resource-group.png)

     A sablonexportálási funkciót nem támogatja az összes erőforrástípus. Ha az erőforráscsoportban a cikkben említettek szerint kizárólag tárfiók és virtuális hálózat szerepel, nem fog hibaüzenetet kapni. Ha azonban más típusú erőforrásokat is létrehozott, előfordulhat, hogy hibaüzenet jelenik meg, amely tájékoztatja, hogy az exportálás során probléma merült fel. Ezeket a hibákat [Az exportálással kapcsolatos problémák megoldása](#fix-export-issues) című részben szereplő információk segítségével oldhatja meg.

      

2. Ismét megjelenik a megoldás újbóli üzembe helyezéséhez használható hat fájl, de ezúttal a sablon némileg eltérően jelenik meg. Ez a sablon csak két paraméterrel rendelkezik: a tárfiók nevével, valamint a virtuális hálózat nevével.

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

     A Resource Manager nem kérte le az üzembe helyezéshez használt sablonokat. Ehelyett létrehozott egy új sablont az erőforrások aktuális konfigurációja alapján. A sablon például a következő beállítást tartalmazza a tárfiók helyére és a replikálási értékre vonatkozóan:

        "location": "northeurope",
        "tags": {},
        "properties": {
            "accountType": "Standard_RAGRS"
        },

3. A sablon letöltésével helyben is dolgozhat rajta.

      ![sablon letöltése](./media/resource-manager-export-template/download-template.png)

4. Keresse meg a letöltött .zip fájlt, és csomagolja ki annak tartalmát. A letöltött sablon segítségével újból telepítheti az infrastruktúrát.

## Az exportálással kapcsolatos problémák megoldása

A sablonexportálási funkciót nem támogatja az összes erőforrástípus. A Resource Manager a bizalmas adatok megóvása érdekében szándékosan nem exportál bizonyos erőforrástípusokat. Valószínű például, hogy nem szeretné, hogy az oldal konfigurációjában található kapcsolati karakterlánc szerepeljen az exportált sablonban. A probléma megoldásához egyszerűen adja hozzá ismét manuálisan a hiányzó erőforrásokat a sablonhoz.

> [AZURE.NOTE] Az exportálási hibák csak akkor lépnek fel, ha az erőforráscsoportból, és nem az üzembe helyezési előzmények közül végez exportálást. Ha a legutóbbi üzembe helyezés pontosan tükrözi az erőforráscsoport aktuális állapotát, érdemes az erőforráscsoport helyett az üzembe helyezési előzmények közül elvégezni a sablon exportálását. Csak akkor exportáljon az erőforráscsoportból, ha olyan módosításokat végzett rajta, amelyeket nem lehet egyetlen sablonnal definiálni.

Ha például az erőforráscsoport helykonfigurációjában, amelynek sablonját exportálni szeretné webalkalmazás, SQL Database-adatbázis és kapcsolati karakterlánc egyaránt található, az alábbi üzenet fog megjelenni.

![hibaüzenet](./media/resource-manager-export-template/show-error.png)

Válassza ki az üzenetet, és a rendszer megjeleníti, hogy mely erőforrástípusokat nem exportált. 
     
![hibaüzenet](./media/resource-manager-export-template/show-error-details.png)

Ebben a részben néhány gyakori probléma megoldását mutatjuk be. Ezen erőforrások implementálásához különféle paramétereket kell a sablonhoz adnia. További információk a [Customize and redeploy exported template](resource-manager-customize-template.md) (Exportált sablonok testreszabása és újbóli üzembe helyezése) című cikkben olvashatók.

### Kapcsolati karakterlánc

A webhely erőforrásainál adja hozzá a kapcsolati karakterlánc definícióját az adatbázishoz:

```
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

### Webhely bővítménye

A webhely erőforrásainál adja hozzá a kód definícióját a telepítés érdekében:

```
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

### Virtuális gépi bővítmény

A virtuális gépekre vonatkozó példákért lásd: [Azure Windows VM Extension Configuration Samples](./virtual-machines/virtual-machines-windows-extensions-configuration-samples.md) (Windowsos Azure virtuális gépi bővítmények konfigurációs mintái).

### Virtuális hálózati átjáró

Adjon hozzá egy virtuális hálózati átjáró erőforrástípust.

```
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

### Helyi hálózati átjáró

Adjon hozzá egy helyi hálózati átjáró erőforrástípust.

```
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

### Kapcsolat

Adjon hozzá egy kapcsolat erőforrástípust.

```
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


## Következő lépések

Gratulálunk! Megtanulta, hogyan exportálhat sablonokat a portálon létrehozott erőforrásokból.

- Az oktatóanyag második felében további paraméterek hozzáadásával testre szabhatja a letöltött sablont, valamint ismét üzembe helyezheti azt egy parancsfájl segítségével. Lásd: [Customize and redeploy exported template](resource-manager-customize-template.md) (Exportált sablonok testreszabása és újbóli üzembe helyezése).
- A sablonok PowerShellen keresztül történő exportálásával kapcsolatos információk: [Az Azure PowerShell használata Azure Resource Managerrel](powershell-azure-resource-manager.md).
- A sablonok az Azure parancssori felületen keresztül történő exportálásával kapcsolatos információk: [A Mac, Linux és Windows eszközökhöz készült Azure CLI használata az Azure Resource Manager eszközzel](xplat-cli-azure-resource-manager.md).



<!--HONumber=Sep16_HO4-->


