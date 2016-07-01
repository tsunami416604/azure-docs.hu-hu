<properties
    pageTitle="Exportált Resource Manager sablon testreszabása | Microsoft Azure"
    description="Paraméterek hozzáadása Azure Resource Manager sablonhoz, valamint a sablon újratelepítése Azure PowerShell vagy Azure CLI eszközön keresztül."
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
    ms.date="05/10/2016"
    ms.author="tomfitz"/>

# Exportált Azure Resource Manager sablon testreszabása

Ez a cikk bemutatja, hogyan módosíthat egy exportált sablont, hogy további értékeket adhasson meg paraméterekként. Ez a [Resource Manager sablon exportálása](resource-manager-export-template.md) cikkben bemutatott lépéseken alapul, azonban nem feltétlenül szükséges, hogy először elolvassa az előző cikket. A szükséges sablont és parancsfájlokat ebben a cikkben találja.

## Exportált sablon megtekintése

Ha befejezte a [Resource Manager-sablon exportálása](resource-manager-export-template.md) műveletet, nyissa meg a letöltött sablont. A sablon neve: **template.json**. Ha rendelkezik a Visual Studio vagy Visual Code programmal, azokkal is szerkesztheti a sablont. Ellenkező esetben bármely JSON- vagy szövegszerkesztő alkalmazással megteheti ezt.

Ha még nem ment végig az előző bemutatón, hozzon létre egy **template.json** nevű fájlt, majd az exportált sablonból adja hozzá az alábbi tartalmat a fájlhoz.

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
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
    "variables": {},
    "resources": [
        {
            "comments": "Generalized from resource in walkthrough.",
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('virtualNetworks_VNET_name')]",
            "apiVersion": "2015-06-15",
            "location": "northeurope",
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "10.0.0.0/16"
                    ]
                },
                "subnets": [
                    {
                        "name": "default",
                        "properties": {
                            "addressPrefix": "10.0.0.0/24"
                        }
                    }
                ]
            },
            "dependsOn": []
        },
        {
            "comments": "Generalized from resource in walkthrough.",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('storageAccounts_storagetf05092016_name')]",
            "apiVersion": "2015-06-15",
            "location": "northeurope",
            "tags": {},
            "properties": {
                "accountType": "Standard_RAGRS"
            },
            "dependsOn": []
        }
    ]
}
```

A template.json sablon remekül működik, ha ugyanolyan típusú tárfiókot szeretne létrehozni ugyanabban a régióban, olyan virtuális hálózattal, amely minden telepítés esetén ugyanazt a címelőtagot és alhálózati előtagot használja. A Resource Manager által nyújtott lehetőségek azonban jóval rugalmasabb sablontelepítést tesznek lehetővé. Előfordulhat például, hogy telepítés során meg szeretné adni a létrehozni kívánt tárfiók típusát, vagy a virtuális hálózat címelőtagjához és alhálózati előtagjához használni kívánt értékeket.

## A sablon testreszabása

Ebben a szakaszban paramétereket adhat hozzá az exportált sablonhoz, hogy az erőforrások más környezetekben való telepítése során újra felhasználhassa azt. Emellett néhány olyan funkciót is hozzáadhat majd a sablonhoz, amelyekkel csökkentheti annak az esélyét, hogy hibába ütközik annak telepítése során. Többé már nem lesz szüksége arra, hogy egyedi nevet találjon ki tárfiókjának. Ezután ugyanis a sablon hozza majd létre az egyedi nevet. Korlátozhatja majd a tárfiók típusához megadható értékeket csak az érvényes lehetőségekre.

1. A telepítés során meghatározni kívánt értékek megadásához cserélje le a **parameters** (paraméterek) szakasz tartalmát az alábbi paraméterdefiníciókra. Figyelje meg a **storageAccount_accountType** paraméter **allowedValues** engedélyezett értékeit. Ha véletlenül érvénytelen értéket ad meg, a rendszer felismeri a hibát még a telepítés megkezdése előtt. Emellett vegye figyelembe azt is, hogy csak egy előtagot adott meg a tárfiók nevéhez, és az előtag legfeljebb 11 karakter lehet. Az előtag 11 karakterre történő korlátozásával biztosíthatja, hogy a teljes név nem haladja majd meg a tárfiókok esetén megengedett maximális karakterszámot. Az előtag lehetővé teszi, hogy elnevezési szabályokat alkalmazzon tárfiókjaira. Az egyedi nevek létrehozását a következő lépésben ismerheti meg.

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

2. A sablon **variables** (változók) szakasza jelenleg üres. Cserélje le ezt a szakaszt az alábbi kódra: A **variables** (változók) szakaszban Ön, mint a sablon szerzője, olyan értékeket hozhat létre, amelyekkel leegyszerűsíthető a sablon további szintaxisa. A **storageAccount_name** változó összefűzi a paraméterből  származó előtagot egy egyedi karakterlánccá, amelyet a rendszer az erőforráscsoport azonosítója alapján hoz létre.

        "variables": {
          "storageAccount_name": "[concat(parameters('storageAccount_prefix'), uniqueString(resourceGroup().id))]"
        },

3. Ha a paramétereket és változókat az erőforrás-definíciókban is használni szeretné, cserélje le a **resources** (erőforrások) szakaszt a következő definíciókra. Figyelje meg, hogy valójában az erőforrás-tulajdonsághoz rendelt értéken kívül nagyon kevés változás történt az erőforrás-definíciókban. A tulajdonságok pontosan megegyeznek az exportált sablon tulajdonságaival. Egyszerűen a nem változtatható értékek helyett a paraméter-értékekhez rendelt hozzá tulajdonságokat. Az erőforrások helye úgy van beállítva a **resourceGroup().location** kifejezésen keresztül, hogy az erőforráscsoporttal megegyező legyen. A tárfiók nevéhez létrehozott változóra a **variables** kifejezés hivatkozik.

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

## A paraméterfájl kijavítása

A letöltött paraméterfájl már nem egyezik a sablonban szereplő paraméterekkel. A paraméterfájl használata nem kötelező, de leegyszerűsítheti a környezetek újbóli telepítésének folyamatát. Számos paraméter esetében a sablonban megadott alapértelmezett értéket használja majd, így a paraméterfájlnak csupán két értéket kell tartalmaznia.

Cserélje le a parameters.json fájl tartalmát az alábbira:

```
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

## A sablon telepítése

A testre szabott sablont és paraméterfájlt az Azure PowerShell vagy az Azure parancssori felület (CLI) használatával telepítheti. Szükség esetén telepítse vagy az [Azure PowerShell](powershell-install-configure.md) alkalmazást, vagy az [Azure parancssori felületet](xplat-cli-install.md). A sablon telepítéséhez felhasználhatja az eredeti sablon exportálásakor a sablonnal együtt letöltött parancsfájlokat, vagy írhat sajátot is.
Az alábbi cikk mindkét lehetőséget bemutatja.

2. A saját parancsfájl használatával történő telepítéshez használja az alábbi lehetőségek egyikét:

     PowerShell esetén futtassa az alábbi parancsot:

        # login
        Add-AzureRmAccount

        # create a new resource group
        New-AzureRmResourceGroup -Name ExportGroup -Location "West Europe"

        # deploy the template to the resource group
        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExportGroup -TemplateFile {path-to-file}\template.json -TemplateParameterFile {path-to-file}\parameters.json

     Azure parancssori felület (CLI) esetén futtassa az alábbi parancsot:

        azure login

        azure group create -n ExportGroup -l "West Europe"

        azure group deployment create -f {path-to-file}\azuredeploy.json -e {path-to-file}\parameters.json -g ExportGroup -n ExampleDeployment

3. Ha letöltötte az exportált sablont és a parancsfájlokat, keresse meg a **deploy.ps1** fájlt (PowerShell esetén) vagy a **deploy.sh** fájlt (Azure parancssori felület esetén).

     PowerShell esetén futtassa az alábbi parancsot:

        Get-Item deploy.ps1 | Unblock-File

        .\deploy.ps1

     Azure parancssori felület (CLI) esetén futtassa az alábbi parancsot:

        .\deploy.sh

## További lépések

- Az [Útmutató Resource Manager sablonokhoz](resource-manager-template-walkthrough.md) című cikk a jelen cikkben megszerzett tudásra épít, és bemutatja, hogyan készítheti el bonyolultabb megoldások sablonjait. Segít a rendelkezésre álló erőforrások jobb megértésében, valamint a megadott értékek meghatározásában.
- A sablonok PowerShellen keresztül történő exportálásával kapcsolatos információk: [Az Azure PowerShell használata Azure Resource Managerrel](powershell-azure-resource-manager.md).
- A sablonok az Azure parancssori felületen keresztül történő exportálásával kapcsolatos információk: [A Mac, Linux és Windows eszközökhöz készült Azure CLI használata az Azure Resource Manager eszközzel](xplat-cli-azure-resource-manager.md).
- A sablonok strukturálásával kapcsolatos információk: [Azure Resource Manager-sablonok készítése](resource-group-authoring-templates.md).



<!--HONumber=Jun16_HO2-->


