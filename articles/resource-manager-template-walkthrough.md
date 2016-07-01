<properties
   pageTitle="Útmutató Resource Manager sablonokhoz | Microsoft Azure"
   description="Egy alapszintű Azure IaaS-architektúrát kiépítő Resource Manager-sablon részletes bemutatása."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="navalev"
   manager=""
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/04/2016"
   ms.author="navale;tomfitz"/>
   
# Útmutató Resource Manager sablonokhoz

A sablonok létrehozásakor feltett első kérdések egyike az, hogy „hogyan is kezdjünk bele”. Elkezdheti egy üres sablonból is a [Sablonok készítése](resource-group-authoring-templates.md#template-format) című cikkben leírt alapszintű struktúrát követve, és hozzáadhatja az erőforrásokat, valamint a megfelelő paramétereket és változókat. Az is jó megoldás, ha először átnézi a [gyorsindítási galériát](https://github.com/Azure/azure-quickstart-templates), és keres olyan forgatókönyveket, amelyek hasonlóak a létrehozni kívánt forgatókönyvhöz. Egyesíthet több sablont is, vagy szerkeszthet egy már létezőt, hogy az megfeleljen saját forgatókönyvének. 

Vessen egy pillantást egy gyakori infrastruktúrára:

* Két virtuális gép, amelyek ugyanazt a tárfiókot használják, ugyanahhoz a rendelkezésre állási csoporthoz tartoznak, és a virtuális hálózat ugyanazon alhálózatán találhatók.
* Mindkét virtuális gép egyetlen hálózati adapterrel és IP-címmel rendelkezik.
* Egy terheléselosztó, terheléselosztási szabállyal a 80-as porton

![architektúra](./media/resource-group-overview/arm_arch.png)

Ez a témakör bemutatja azon lépéseket, amelyekkel elkészíthető a fent leírt infrastruktúra Resource Manager-sablonja. A végső sablon, amelyet majd létrehoz, a következő Gyorsindítási sablonon alapul: [Két virtuális gép, egy terheléselosztó és terheléselosztási szabályok](https://azure.microsoft.com/documentation/templates/201-2-vms-loadbalancer-lbrules/).

Ez azonban túl sok ahhoz, hogy egyszerre hozza őket létre, ezért először hozzon létre egy tárfiókot, és telepítse azt. A tárfiókok létrehozásának elsajátítása után további erőforrásokat ad majd hozzá, és újból telepíti majd a sablont az infrastruktúra befejezéséhez.

>[AZURE.NOTE] A sablon létrehozásához bármilyen szerkesztőt használhat. A Visual Studio olyan eszközöket biztosít, amelyek egyszerűbbé teszik a sablonok fejlesztését. A jelen oktatóanyag elvégzéséhez azonban nincs szükség a Visual Studio használatára. A Webes alkalmazások és SQL Database telepítések Visual Studióval történő létrehozásával kapcsolatos oktatóanyag: [Azure erőforráscsoport-sablonok létrehozása és telepítése a Visual Studio használatával](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md). 

## A Resource Manager-sablon létrehozása

A sablon egy JSON-fájl, amely meghatározza a később telepítendő összes erőforrást. Emellett lehetővé teszi a telepítés során megadott paraméterek, a más értékekből és kifejezésekből összeállított változók, valamint a telepítés kimeneteinek meghatározását is. 

Kezdjük a legegyszerűbb sablonnal:

```json
    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {  },
      "variables": {  },
      "resources": [  ],
      "outputs": {  }
    }
 ```

Mentse el a fájlt **azuredeploy.json** néven (a sablon neve bármi lehet, de json-fájlnak kell lennie).

## Tárfiók létrehozása
A **resources** (erőforrás) szakaszban adjon hozzá egy objektumot, amely definiálja a tárfiókot az alább látható módon. 

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[parameters('storageAccountName')]",
    "apiVersion": "2015-06-15",
    "location": "[resourceGroup().location]",
    "properties": {
      "accountType": "Standard_LRS"
    }
  }
]
```

Megfordulhat a fejében, hogy honnan származnak ezek a tulajdonságok és értékek. A **type** (típus), **name** (név), **apiVersion** (API-verzió) és **location** (hely) tulajdonságok szabványos elemek, amelyek minden erőforrástípus esetén elérhetők. A gyakori elemekről az [Erőforrások](resource-group-authoring-templates.md#resources) című cikkben olvashat. A **name** paraméter olyan értékre van beállítva, amelyet a telepítés során adhat meg, a **location** paraméter pedig az erőforráscsoport által használt helyet jelöli. A **type** és **apiVersion** paraméterek megállapításának módját az alábbi szakasz ismerteti.

A **properties** (tulajdonságok) szakasz tartalmazza az egyes erőforrástípusok összes egyedi tulajdonságát. Az ebben a szakaszban megadott értékek pontosan megegyeznek az adott erőforrástípus létrehozásához szükséges REST API PUT-műveletével. Tárfiók létrehozásánál meg kell adnia az **accountType** (fióktípus) tulajdonságot. Figyelje meg a [Tárfiók létrehozásához szükséges REST API](https://msdn.microsoft.com/library/azure/mt163564.aspx) területen, hogy a REST-művelet tulajdonságszakasza szintén tartalmaz egy **accountType** tulajdonságot, és itt az engedélyezett értékeket is megtalálja. Ebben a példában a fiók típusa **Standard_LRS** értékre van állítva, de megadhat más értéket is, vagy engedélyezheti azt is, hogy a felhasználók megadhassák a fiók típusát paraméterként.

Most ugorjunk vissza a **parameters** (paraméterek) szakaszra, és nézzük meg, hogyan adható meg a tárfiók neve. A paraméterek használatával kapcsolatos további információkat a [Paraméterek](resource-group-authoring-templates.md#parameters) című részben találhat. 

```json
"parameters" : {
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Storage Account Name"
      }
    }
}
```
Itt megadott egy karakterlánc típusú paramétert, amely tartalmazza majd a tárfiók nevét. A paraméter értékét a sablon telepítése során adhatja majd meg.

## A sablon telepítése
Most már van egy teljes sablonunk egy új tárfiók létrehozásához. Emlékezzen vissza, hogy a sablont egy **azuredeploy.json** nevű fájlban mentette el:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters" : {
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Storage Account Name"
      }
    }
  },  
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[parameters('storageAccountName')]",
      "apiVersion": "2015-06-15",
      "location": "[resourceGroup().location]",
      "properties": {
        "accountType": "Standard_LRS"
      }
    }
  ]
}
```

Egy sablon telepítésének számos módja van, ahogyan ezt az [Erőforrások telepítése](resource-group-template-deploy.md) című cikkben is láthatja. A sablon Azure PowerShell használatával történő telepítéséhez használja a következőket:

```powershell
# create a new resource group
New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West Europe"

# deploy the template to the resource group
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile azuredeploy.json
```

Vagy, a sablon az Azure parancssori felülettel történő telepítéséhez használja a következőket:

```
azure group create -n ExampleResourceGroup -l "West Europe"

azure group deployment create -f azuredeploy.json -g ExampleResourceGroup -n ExampleDeployment
```

Ezennel Ön egy tárfiók büszke tulajdonosa lett!

A következő lépés az oktatóanyag elején leírt architektúra telepítéséhez szükséges erőforrások hozzáadása. Az erőforrásokat ugyanabban a sablonban kell majd hozzáadni, amelyben eddig is dolgozott.

## Rendelkezésre állási csoport
A tárfiók definiálása után adjon hozzá egy rendelkezésre állási csoportot a virtuális géphez. Ebben az esetben nincs szükség tovább tulajdonságokra, tehát a definiálás meglehetősen egyszerű. A teljes tulajdonságszakaszt megtalálhatja a [Rendelkezésre állási csoport létrehozásához szükséges REST API](https://msdn.microsoft.com/library/azure/mt163607.aspx) című részben, amennyiben meg szeretné adni a frissítési tartományok számának és tartalék tartományok számának értékét.

```json
{
   "type": "Microsoft.Compute/availabilitySets",
   "name": "[variables('availabilitySetName')]",
   "apiVersion": "2015-06-15",
   "location": "[resourceGroup().location]",
   "properties": {}
}
```

Figyelje meg, hogy a **name** (név) paraméter beállított értéke egy változó. A jelen sablon esetében a rendelkezésre állási csoport nevére több különböző helyen is szükség lesz. Megkönnyíti a sablon kezelését, ha ezt az értéket megadja egyszer, majd több helyen is felhasználja.

A **type** (típus) tulajdonság az erőforrás-szolgáltatót és az erőforrás típusát egyaránt tartalmazza. A rendelkezésre állási csoportok esetében az erőforrás-szolgáltató a **Microsoft.Compute**, az erőforrás típusa pedig **availabilitySets**. Az elérhető erőforrás-szolgáltatók listáját az alábbi PowerShell-parancs futtatásával tekintheti meg:

```powershell
    Get-AzureRmResourceProvider -ListAvailable
```

Ha pedig Azure parancssori felületet használ, futtassa az alábbi parancsot:
```
    azure provider list
```
Mivel ebben a témakörben tárfiókokat, virtuális gépeket és virtuális hálózatot használ a létrehozáshoz, az alábbi szolgáltatókkal fog dolgozni:

- Microsoft.Storage
- Microsoft.Compute
- Microsoft.Network

Az egyes szolgáltatók erőforrástípusainak megtekintéséhez futtassa az alábbi PowerShell-parancsot:

```powershell
    (Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute).ResourceTypes
```

Azure CLI használata esetén pedig az alábbi paranccsal lekérheti az elérhető típusokat JSON-formátumban, majd elmentheti azok listáját egy fájlba.

```
    azure provider show Microsoft.Compute --json > c:\temp.json
```

Az **availabilitySets** típusnak a **Microsoft.Compute** szolgáltatón belül kell megjelennie. A típus teljes neve: **Microsoft.Compute/availabilitySets**. A sablon bármely erőforrása esetén megadhatja annak erőforrástípusát.

## Nyilvános IP-cím
Adjon meg egy nyilvános IP-címet. A beállítandó tulajdonságokért tekintse meg a [Nyilvános IP-címekhez szükséges REST API](https://msdn.microsoft.com/library/azure/mt163590.aspx) című részt.

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "[parameters('publicIPAddressName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "publicIPAllocationMethod": "Dynamic",
    "dnsSettings": {
      "domainNameLabel": "[parameters('dnsNameforLBIP')]"
    }
  }
}
```

A kiosztási módszer **Dinamikusra** van állítva, de beállíthatja a szükséges értékre, vagy beállíthatja úgy, hogy elfogadjon paraméterértéket. Ön engedélyezte, hogy a sablon felhasználói megadják a tartománynév címkéjének értékét.

Az alábbiakban bemutatjuk, hogyan állítható be az **apiVersion** paraméter értéke. A megadott érték egyszerűen megegyezik az erőforrás létrehozásához használni kívánt REST API verziójával. Az erőforrás típusát tehát megtalálhatja a REST API dokumentációjában. Azt is megteheti, hogy lefuttatja az alábbi PowerShell-parancsot az egyes típusok esetében.

```powershell
    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Network).ResourceTypes | Where-Object ResourceTypeName -eq publicIPAddresses).ApiVersions
```
Ez a következő értékeket adja vissza:

    2015-06-15
    2015-05-01-preview
    2014-12-01-preview

Az API verziójának Azure parancssori felülettel történő megtekintéséhez futtassa le az előzőleg bemutatott **azure provider show** parancsot.

Új sablon létrehozásakor mindig válassza a legújabb API-verziót.

## Virtuális hálózat és alhálózat
Hozzon létre egy virtuális hálózatot egyetlen alhálózattal. A beállítandó tulajdonságokért tekintse meg a [Virtuális hálózatokhoz szükséges REST API](https://msdn.microsoft.com/library/azure/mt163661.aspx) című részt.

```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Network/virtualNetworks",
   "name": "[parameters('vnetName')]",
   "location": "[resourceGroup().location]",
   "properties": {
     "addressSpace": {
       "addressPrefixes": [
         "10.0.0.0/16"
       ]
     },
     "subnets": [
       {
         "name": "[variables('subnetName')]",
         "properties": {
           "addressPrefix": "10.0.0.0/24"
         }
       }
     ]
   }
}
```

## Load balancer
Most egy kívülre irányuló terheléselosztót hoz majd létre. Mivel a terheléselosztó a nyilvános IP-címet használja, deklarálnia kell annak a nyilvános IP-címtől való függőségét a **dependsOn** szakaszban. Ez azt jelenti, hogy a terheléselosztó nem lesz telepítve, amíg a nyilvános IP-cím telepítése be nem fejeződött. A függőség meghatározása nélkül hibaüzenetet kapna, mert a Resource Manager párhuzamosan próbálja meg telepíteni az erőforrásokat, és olyan IP-címre próbálná a terheléselosztót beállítani, amely még nem létezik. 

Emellett az alábbiakat is létrehozza majd: egy háttér címkészletet, néhány bejövő NAT-szabályt a virtuális gépek távoli asztali eléréséhez, valamint egy terheléselosztási szabályt egy TCP-hálózatfigyelővel a jelen erőforrás-definíció 80-as portján. Az összes tulajdonságért tekintse meg a [Terheléselosztóhoz szükséges REST API](https://msdn.microsoft.com/library/azure/mt163574.aspx) című részt.

```json
{
   "apiVersion": "2015-06-15",
   "name": "[parameters('lbName')]",
   "type": "Microsoft.Network/loadBalancers",
   "location": "[resourceGroup().location]",
   "dependsOn": [
     "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]"
   ],
   "properties": {
     "frontendIPConfigurations": [
       {
         "name": "LoadBalancerFrontEnd",
         "properties": {
           "publicIPAddress": {
             "id": "[variables('publicIPAddressID')]"
           }
         }
       }
     ],
     "backendAddressPools": [
       {
         "name": "BackendPool1"
       }
     ],
     "inboundNatRules": [
       {
         "name": "RDP-VM0",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "protocol": "tcp",
           "frontendPort": 50001,
           "backendPort": 3389,
           "enableFloatingIP": false
         }
       },
       {
         "name": "RDP-VM1",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "protocol": "tcp",
           "frontendPort": 50002,
           "backendPort": 3389,
           "enableFloatingIP": false
         }
       }
     ],
     "loadBalancingRules": [
       {
         "name": "LBRule",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "backendAddressPool": {
             "id": "[variables('lbPoolID')]"
           },
           "protocol": "tcp",
           "frontendPort": 80,
           "backendPort": 80,
           "enableFloatingIP": false,
           "idleTimeoutInMinutes": 5,
           "probe": {
             "id": "[variables('lbProbeID')]"
           }
         }
       }
     ],
     "probes": [
       {
         "name": "tcpProbe",
         "properties": {
           "protocol": "tcp",
           "port": 80,
           "intervalInSeconds": 5,
           "numberOfProbes": 2
         }
       }
     ]
   }
}
```

## Hálózati illesztő
2 hálózati illesztőt hoz majd létre, mindkét virtuális gép számára egyet-egyet. A hálózati illesztők duplikált bejegyzéseinek használata helyett a [copyIndex() függvény](resource-group-create-multiple.md) használatával megismételheti a másolási ciklust (más néven: nicLoop), és létrehozhat a `numberOfInstances` változókban megadott számú hálózati illesztőt. A hálózati illesztő a virtuális hálózat és a terheléselosztó létrehozásától függ. Az illesztő a virtuális hálózat létrehozása során meghatározott alhálózatot használja, és a terheléselosztó azonosítójával konfigurálja a terheléselosztó címkészletét, valamint a bejövő NAT-szabályokat.
A beállítandó tulajdonságokért tekintse meg a [Hálózati illesztőkhöz szükséges REST API](https://msdn.microsoft.com/library/azure/mt163668.aspx) című részt.

```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Network/networkInterfaces",
   "name": "[concat(parameters('nicNamePrefix'), copyindex())]",
   "location": "[resourceGroup().location]",
   "copy": {
     "name": "nicLoop",
     "count": "[variables('numberOfInstances')]"
   },
   "dependsOn": [
     "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]",
     "[concat('Microsoft.Network/loadBalancers/', parameters('lbName'))]"
   ],
   "properties": {
     "ipConfigurations": [
       {
         "name": "ipconfig1",
         "properties": {
           "privateIPAllocationMethod": "Dynamic",
           "subnet": {
             "id": "[variables('subnetRef')]"
           },
           "loadBalancerBackendAddressPools": [
             {
               "id": "[concat(variables('lbID'), '/backendAddressPools/BackendPool1')]"
             }
           ],
           "loadBalancerInboundNatRules": [
             {
               "id": "[concat(variables('lbID'),'/inboundNatRules/RDP-VM', copyindex())]"
             }
           ]
         }
       }
     ]
   }
}
```

## Virtuális gép
Két virtuális gépet hoz majd létre a copyIndex() függvény használatával, ahogyan ezt a [hálózati illesztők](#network-interface) esetében is tette.
A virtuális gépek létrehozása a tárfióktól, a hálózati illesztőtől és a rendelkezésre állási csoporttól függ. Ez a virtuális gép egy piactéri rendszerképből lesz létrehozva a `storageProfile` tulajdonság alapján. A kép közzétevőjének, ajánlatának, termékváltozatának és verziójának meghatározásához használja az `imageReference` parancsot. Az utolsó lépés a diagnosztikai profil konfigurálása, amely lehetővé teszi a virtuális gép diagnosztikáját. 

A piactéri rendszerkép kapcsolódó tulajdonságainak megkereséséhez kövesse a [Linux virtuálisgép-rendszerképek kiválasztása](./virtual-machines/virtual-machines-linux-cli-ps-findimage.md), vagy a [Windows virtuálisgép-rendszerképek kiválasztása](./virtual-machines/virtual-machines-windows-cli-ps-findimage.md) cikkben leírt utasításokat.

```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Compute/virtualMachines",
   "name": "[concat(parameters('vmNamePrefix'), copyindex())]",
   "copy": {
     "name": "virtualMachineLoop",
     "count": "[variables('numberOfInstances')]"
   },
   "location": "[resourceGroup().location]",
   "dependsOn": [
     "[concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]",
     "[concat('Microsoft.Network/networkInterfaces/', parameters('nicNamePrefix'), copyindex())]",
     "[concat('Microsoft.Compute/availabilitySets/', variables('availabilitySetName'))]"
   ],
   "properties": {
     "availabilitySet": {
       "id": "[resourceId('Microsoft.Compute/availabilitySets',variables('availabilitySetName'))]"
     },
     "hardwareProfile": {
       "vmSize": "[parameters('vmSize')]"
     },
     "osProfile": {
       "computerName": "[concat(parameters('vmNamePrefix'), copyIndex())]",
       "adminUsername": "[parameters('adminUsername')]",
       "adminPassword": "[parameters('adminPassword')]"
     },
     "storageProfile": {
       "imageReference": {
         "publisher": "[parameters('imagePublisher')]",
         "offer": "[parameters('imageOffer')]",
         "sku": "[parameters('imageSKU')]",
         "version": "latest"
       },
       "osDisk": {
         "name": "osdisk",
         "vhd": {
           "uri": "[concat('http://',parameters('storageAccountName'),'.blob.core.windows.net/vhds/','osdisk', copyindex(), '.vhd')]"
         },
         "caching": "ReadWrite",
         "createOption": "FromImage"
       }
     },
     "networkProfile": {
       "networkInterfaces": [
         {
           "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'),copyindex()))]"
         }
       ]
     },
     "diagnosticsProfile": {
       "bootDiagnostics": {
          "enabled": "true",
          "storageUri": "[concat('http://',parameters('storageAccountName'),'.blob.core.windows.net')]"
       }
     }
}
```

>[AZURE.NOTE] A **harmadik fél** által közzétett rendszerképek esetében meg kell adnia egy másik tulajdonságot is: `plan`. Erre példát a gyorsindítási galéria [alábbi sablonjában](https://github.com/Azure/azure-quickstart-templates/tree/master/checkpoint-single-nic) találhat. 

Ezennel befejezte a sablon erőforrásainak definiálását.

## Paraméterek

A paraméterek szakaszban határozza meg a sablon telepítésekor megadható értékeket. Csak azon értékek esetében határozzon meg paramétereket, amelyek Ön szerint eltérőek lehetnek a telepítés során. Megadhat egy alapértelmezett értéket is a paraméterek számára, amelyet a rendszer akkor használ, ha nem adtak meg értéket a telepítés során. Meghatározhatja a megengedett értékeket is, ahogy ezt az **imageSKU** paraméter esetében is láthatja.

```json
"parameters": {
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of storage account"
      }
    },
    "adminUsername": {
      "type": "string",
      "metadata": {
        "description": "Admin username"
      }
    },
    "adminPassword": {
      "type": "securestring",
      "metadata": {
        "description": "Admin password"
      }
    },
    "dnsNameforLBIP": {
      "type": "string",
      "metadata": {
        "description": "DNS for Load Balancer IP"
      }
    },
    "vmNamePrefix": {
      "type": "string",
      "defaultValue": "myVM",
      "metadata": {
        "description": "Prefix to use for VM names"
      }
    },
    "imagePublisher": {
      "type": "string",
      "defaultValue": "MicrosoftWindowsServer",
      "metadata": {
        "description": "Image Publisher"
      }
    },
    "imageOffer": {
      "type": "string",
      "defaultValue": "WindowsServer",
      "metadata": {
        "description": "Image Offer"
      }
    },
    "imageSKU": {
      "type": "string",
      "defaultValue": "2012-R2-Datacenter",
      "allowedValues": [
        "2008-R2-SP1",
        "2012-Datacenter",
        "2012-R2-Datacenter"
      ],
      "metadata": {
        "description": "Image SKU"
      }
    },
    "lbName": {
      "type": "string",
      "defaultValue": "myLB",
      "metadata": {
        "description": "Load Balancer name"
      }
    },
    "nicNamePrefix": {
      "type": "string",
      "defaultValue": "nic",
      "metadata": {
        "description": "Network Interface name prefix"
      }
    },
    "publicIPAddressName": {
      "type": "string",
      "defaultValue": "myPublicIP",
      "metadata": {
        "description": "Public IP Name"
      }
    },
    "vnetName": {
      "type": "string",
      "defaultValue": "myVNET",
      "metadata": {
        "description": "VNET name"
      }
    },
    "vmSize": {
      "type": "string",
      "defaultValue": "Standard_D1",
      "metadata": {
        "description": "Size of the VM"
      }
    }
  }
```

## Változók

A változók szakaszban meghatározhatja a sablonban egynél több helyen használt értékeket, vagy azokat az értékeket, amelyek több kifejezésből és változóból tevődnek össze. A változókat gyakran arra használják, hogy leegyszerűsítsék a sablon szintaxisát.

```json
"variables": {
    "availabilitySetName": "myAvSet",
    "subnetName": "Subnet-1",
    "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('vnetName'))]",
    "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables ('subnetName'))]",
    "publicIPAddressID": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]",
    "numberOfInstances": 2,
    "lbID": "[resourceId('Microsoft.Network/loadBalancers',parameters('lbName'))]",
    "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/LoadBalancerFrontEnd')]",
    "lbPoolID": "[concat(variables('lbID'),'/backendAddressPools/BackendPool1')]",
    "lbProbeID": "[concat(variables('lbID'),'/probes/tcpProbe')]"
  }
```

Elkészült a sablon! Összehasonlíthatja a sablont a [gyorsindítási galéria](https://github.com/Azure/azure-quickstart-templates) egyik teljes sablonjával a [Két virtuális gép terheléselosztóval és terheléselosztási szabályokkal sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-loadbalancer-lbrules) alatt. Az eltérő verziószámoktól függően az Ön sablonja enyhén eltérhet ezektől. 

A sablont újból telepítheti a tárfiók telepítésénél használt parancsokkal. Nem kell törölni a tárfiókot az újbóli telepítés előtt, mert a Resource Manager átugorja a már létező, és nem módosított erőforrások újbóli létrehozását.

## További lépések

- Az [Azure Resource Manager-sablon vizualizálója (ARMViz)](http://armviz.io/#/) egy remek eszköz, amellyel vizuálisan megjelenítheti Resource Manager-sablonjait, ha azok már túl nagyok, és nem láthatók át a JSON-fájlból olvasva.
- A sablonok struktúrájával kapcsolatos további információk: [Azure Resource Manager-sablonok készítése](resource-group-authoring-templates.md).
- A sablonok telepítésével kapcsolatos információkért lásd: [Erőforráscsoport telepítése Azure Resource Manager-sablon használatával](resource-group-template-deploy.md).



<!--HONumber=Jun16_HO2-->


