---
title: Virtuális gép létrehozása a DevTest Labs ben az Azure PowerShell használatával
description: Megtudhatja, hogy az Azure DevTest Labs használatával hogyan hozhat létre és kezelhet virtuális gépeket az Azure PowerShell használatával.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 13014c39641203bddadf858c34cff67462b3a4b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76167110"
---
# <a name="create-a-virtual-machine-with-devtest-labs-using-azure-powershell"></a>Hozzon létre egy virtuális gépet a DevTest Labs segítségével az Azure PowerShell használatával
Ez a cikk bemutatja, hogyan hozhat létre egy virtuális gépet az Azure DevTest Labs az Azure PowerShell használatával. A PowerShell-parancsfájlok segítségével automatizálhatja a virtuális gépek létrehozását az Azure DevTest Labs laborjában. 

## <a name="prerequisites"></a>Előfeltételek
Előkészületek:

- [Hozzon létre egy tesztkörnyezetet,](devtest-lab-create-lab.md) ha nem szeretne egy meglévő tesztkörnyezetben a parancsfájl vagy parancsok ebben a cikkben. 
- [Telepítse az Azure PowerShellt,](/powershell/azure/install-az-ps?view=azps-1.7.0) vagy használja az Azure Cloud Portalba integrált Azure Cloud Shellt. 

## <a name="powershell-script"></a>PowerShell-szkript
Ebben a szakaszban a mintaparancsfájl az [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction?view=azps-1.7.0) parancsmagot használja.  Ez a parancsmag veszi a labor erőforrás-azonosító, a`createEnvironment`művelet nevét végrehajtani ( ), és a szükséges paramétereket, hogy a művelet végrehajtása. A paraméterek egy kivonattábla, amely tartalmazza az összes virtuális gép leírásának tulajdonságait. 

```powershell
[CmdletBinding()]

Param(
[Parameter(Mandatory = $false)]  $SubscriptionId,
[Parameter(Mandatory = $true)]   $LabResourceGroup,
[Parameter(Mandatory = $true)]   $LabName,
[Parameter(Mandatory = $true)]   $NewVmName,
[Parameter(Mandatory = $true)]   $UserName,
[Parameter(Mandatory = $true)]   $Password
)
 
pushd $PSScriptRoot

try {
    if ($SubscriptionId -eq $null) {
        $SubscriptionId = (Get-AzContext).Subscription.SubscriptionId
    }

    $API_VERSION = '2016-05-15'
    $lab = Get-AzResource -ResourceId "/subscriptions/$SubscriptionId/resourceGroups/$LabResourceGroup/providers/Microsoft.DevTestLab/labs/$LabName"

    if ($lab -eq $null) {
       throw "Unable to find lab $LabName resource group $LabResourceGroup in subscription $SubscriptionId."
    }

    #For this example, we are getting the first allowed subnet in the first virtual network
    #  for the lab.
    #If a specific virtual network is needed use | to find it. 
    #ie $virtualNetwork = @(Get-AzResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION) | Where-Object Name -EQ "SpecificVNetName"

    $virtualNetwork = @(Get-AzResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION)[0]

    $labSubnetName = $virtualNetwork.properties.allowedSubnets[0].labSubnetName

    #Prepare all the properties needed for the createEnvironment
    # call used to create the new VM.
    # The properties will be slightly different depending on the base of the vm
    # (a marketplace image, custom image or formula).
    # The setup of the virtual network to be used may also affect the properties.
    # This sample includes the properties to add an additional disk under dataDiskParameters
    
    $parameters = @{
       "name"      = $NewVmName;
       "location"  = $lab.Location;
       "properties" = @{
          "labVirtualNetworkId"     = $virtualNetwork.ResourceId;
          "labSubnetName"           = $labSubnetName;
          "notes"                   = "Windows Server 2016 Datacenter";
          "osType"                  = "windows"
          "expirationDate"          = "2019-12-01"
          "galleryImageReference"   = @{
             "offer"     = "WindowsServer";
             "publisher" = "MicrosoftWindowsServer";
             "sku"       = "2016-Datacenter";
             "osType"    = "Windows";
             "version"   = "latest"
          };
          "size"                    = "Standard_DS2_v2";
          "userName"                = $UserName;
          "password"                = $Password;
          "disallowPublicIpAddress" = $true;
          "dataDiskParameters" = @(@{
            "attachNewDataDiskOptions" = @{
                "diskName" = "adddatadisk"
                "diskSizeGiB" = "1023"
                "diskType" = "Standard"
                }
          "hostCaching" = "ReadWrite"
          })
       }
    }
    
    #The following line is the same as invoking
    # https://azure.github.io/projects/apis/#!/Labs/Labs_CreateEnvironment rest api

    Invoke-AzResourceAction -ResourceId $lab.ResourceId -Action 'createEnvironment' -Parameters $parameters -ApiVersion $API_VERSION -Force -Verbose
}
finally {
   popd
}
```

A virtuális gép tulajdonságai a fenti parancsfájlban lehetővé teszik számunkra, hogy hozzon létre egy virtuális gépet a Windows Server 2016 DataCenter, mint az operációs rendszer. A virtuális gépek minden típusa esetében ezek a tulajdonságok némileg eltérőek lesznek. A [Virtuálisgép definiálása szakasz bemutatja,](#define-virtual-machine) hogyan határozhatja meg, hogy mely tulajdonságokat kell használni ebben a parancsfájlban.

A következő parancs egy példa a fájlnévben mentett parancsfájl futtatására: Create-LabVirtualMachine.ps1. 

```powershell
 PS> .\Create-LabVirtualMachine.ps1 -ResourceGroupName 'MyLabResourceGroup' -LabName 'MyLab' -userName 'AdminUser' -password 'Password1!' -VMName 'MyLabVM'
```

## <a name="define-virtual-machine"></a>Virtuális gép definiálása
Ez a szakasz bemutatja, hogyan szerezheti be a létrehozni kívánt virtuális géptípusra jellemző tulajdonságokat. 

### <a name="use-azure-portal"></a>Az Azure Portal használata
Létrehozhat egy Azure Resource Manager-sablont, amikor virtuális gép az Azure Portalon. Nem kell befejezni a virtuális gép létrehozásának folyamatát. Csak addig kövesse a lépéseket, amíg meg nem jelenik a sablon. Ez a legjobb módja annak, hogy a szükséges JSON-leírást, ha még nem hozott létre egy labor virtuális gép. 

1. Nyissa meg az [Azure Portalt.](https://portal.azure.com)
2. Válassza a bal oldali navigációs menü **Minden szolgáltatás lehetőséget.**
3. Keresse meg és válassza ki a **DevTest Labs** a szolgáltatások listájából. 
4. A **DevTest Labs** lapon válassza ki a labor a laborok listájában.
5. A tesztkörnyezet kezdőlapján válassza a **+ Hozzáadás lehetőséget** az eszköztáron. 
6. Válasszon ki egy **alaplemezképet** a virtuális géphez. 
7. Válassza ki az **automatizálási beállításokat** a **Küldés** gomb feletti oldal alján. 
8. Megjelenik az **Azure Resource Manager sablon** a virtuális gép létrehozásához. 
9. Az **erőforrások** szakasz JSON szegmense rendelkezik a korábban kiválasztott képtípus definíciójával. 

    ```json
    {
      "apiVersion": "2018-10-15-preview",
      "type": "Microsoft.DevTestLab/labs/virtualmachines",
      "name": "[variables('vmName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "labVirtualNetworkId": "[variables('labVirtualNetworkId')]",
        "notes": "Windows Server 2019 Datacenter",
        "galleryImageReference": {
          "offer": "WindowsServer",
          "publisher": "MicrosoftWindowsServer",
          "sku": "2019-Datacenter",
          "osType": "Windows",
          "version": "latest"
        },
        "size": "[parameters('size')]",
        "userName": "[parameters('userName')]",
        "password": "[parameters('password')]",
        "isAuthenticationWithSshKey": false,
        "labSubnetName": "[variables('labSubnetName')]",
        "disallowPublicIpAddress": true,
        "storageType": "Standard",
        "allowClaim": false,
        "networkInterface": {
          "sharedPublicIpAddressConfiguration": {
            "inboundNatRules": [
              {
                "transportProtocol": "tcp",
                "backendPort": 3389
              }
            ]
          }
        }
      }
    }
    ```

Ebben a példában láthatja, hogyan kaphatja meg az Azure Market Place-lemezkép definícióját. Az egyéni lemezkép, képlet vagy környezet definícióját ugyanúgy kaphatja meg. Adja hozzá a virtuális géphez szükséges összetevőket, és adja meg a szükséges speciális beállításokat. Miután megadta a szükséges mezők és a választható mezők értékeit, az **Automatizálási beállítások** gomb kiválasztása előtt.

### <a name="use-azure-rest-api"></a>Az Azure REST API használata
Az alábbi eljárás lépéseket ad egy lemezkép tulajdonságainak lehívásához a REST API használatával: Ezek a lépések csak egy tesztkörnyezetben lévő meglévő virtuális gép esetén működnek. 

1. Nyissa meg a [Virtuális gépek - listalapot,](/rest/api/dtl/virtualmachines/list) és válassza a **Próbálja ki** gombot. 
2. Válassza ki az **Azure-előfizetését**.
3. Adja meg **a tesztkörnyezet erőforráscsoportját.**
4. Adja meg **a labor nevét.** 
5. Válassza a **Futtatás** lehetőséget.
6. Láthatja **a tulajdonságokat a lemezkép,** amely alapján a virtuális gép jött létre. 

## <a name="set-expiration-date"></a>Lejárati dátum beállítása
Például a betanítás, a bemutatók és a próbaverziók esetén érdemes lehet virtuális gépeket létrehozni, és automatikusan törölni őket egy meghatározott időtartam után, hogy ne merüljenek fel szükségtelen költségek. Beállíthat egy lejárati dátumot egy virtuális géphez, miközben a PowerShell használatával hozza létre, ahogy az a példa [PowerShell-parancsfájl](#powershell-script) szakaszban látható.

Az alábbiakban egy powershell-parancsfájlból áll, amely egy tesztkörnyezetben összes meglévő virtuális gép lejárati dátumát állítja be:

```powershell
# Values to change
$subscriptionId = '<Enter the subscription Id that contains lab>'
$labResourceGroup = '<Enter the lab resource group>'
$labName = '<Enter the lab name>'
$VmName = '<Enter the VmName>'
$expirationDate = '<Enter the expiration date e.g. 2019-12-16>'

# Log into your Azure account
Login-AzureRmAccount

Select-AzureRmSubscription -SubscriptionId $subscriptionId
$VmResourceId = "subscriptions/$subscriptionId/resourcegroups/$labResourceGroup/providers/microsoft.devtestlab/labs/$labName/virtualmachines/$VmName"

$vm = Get-AzureRmResource -ResourceId $VmResourceId -ExpandProperties

# Get all the Vm properties
$VmProperties = $vm.Properties

# Set the expirationDate property
If ($VmProperties.expirationDate -eq $null) {
    $VmProperties | Add-Member -MemberType NoteProperty -Name expirationDate -Value $expirationDate
} Else {
    $VmProperties.expirationDate = $expirationDate
}

Set-AzureRmResource -ResourceId $VmResourceId -Properties $VmProperties -Force
```


## <a name="next-steps"></a>További lépések
Tekintse meg a következő tartalmat: [Azure PowerShell dokumentáció az Azure DevTest Labs](/powershell/module/az.devtestlabs/)
