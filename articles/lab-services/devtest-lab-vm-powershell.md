---
title: Virtuális gép létrehozása a DevTest Labs szolgáltatásban az Azure PowerShell-lel |} A Microsoft Docs
description: Ismerje meg, hogyan használható az Azure DevTest Labs szolgáltatásban hozhat létre és kezelhet virtuális gépeket az Azure PowerShell használatával.
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
ms.date: 04/02/2019
ms.author: spelluru
ms.openlocfilehash: df6d7e59c6c569a56162a738924b4ffd05b47da6
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58896444"
---
# <a name="create-a-virtual-machine-with-devtest-labs-using-azure-powershell"></a>Virtuális gép létrehozása a DevTest Labs szolgáltatással az Azure PowerShell-lel
Ez a cikk bemutatja, hogyan egy virtuális gép létrehozása az Azure DevTest Labs szolgáltatásban az Azure PowerShell használatával. PowerShell-parancsfájlok segítségével automatizálhatja a virtuális gépek az Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet létrehozását. 

## <a name="prerequisites"></a>Előfeltételek
Előkészületek:

- [Labor létrehozása](devtest-lab-create-lab.md) Ha nem kívánja a parancsfájlok vagy parancsok teszteléséhez, ebben a cikkben egy meglévő lab használatával. 
- [Azure PowerShell telepítése](/powershell/azure/azurerm/other-install) vagy használja az Azure Cloud Shellt, amely integrálva van az Azure Portalon. 

## <a name="powershell-script"></a>PowerShell-szkript
A minta parancsfájl ebben a szakaszban a [Invoke-AzureRmResourceAction](/powershell/module/azurerm.resources/invoke-azurermresourceaction) parancsmagot.  Ennél a parancsmagnál a labor létrehozása erőforrás-azonosító, a művelet végrehajtásához nevének (`createEnvironment`), és a szükséges paramétereket, hogy a művelet végrehajtására. A paraméterek egy kivonattábla, amely tartalmazza a virtuális gép teljes leírás tulajdonságai szerepelnek. 

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
        $SubscriptionId = (Get-AzureRmContext).Subscription.SubscriptionId
    }

    $API_VERSION = '2016-05-15'
    $lab = Get-AzureRmResource -ResourceId "/subscriptions/$SubscriptionId/resourceGroups/$LabResourceGroup/providers/Microsoft.DevTestLab/labs/$LabName"

    if ($lab -eq $null) {
       throw "Unable to find lab $LabName resource group $LabResourceGroup in subscription $SubscriptionId."
    }

    #For this example, we are getting the first allowed subnet in the first virtual network
    #  for the lab.
    #If a specific virtual network is needed use | to find it. 
    #ie $virtualNetwork = @(Get-AzureRmResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION) | Where-Object Name -EQ "SpecificVNetName"

    $virtualNetwork = @(Get-AzureRmResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION)[0]

    $labSubnetName = $virtualNetwork.properties.allowedSubnets[0].labSubnetName

    #Prepare all the properties needed for the createEnvironment
    # call used to create the new VM.
    # The properties will be slightly different depending on the base of the vm
    # (a marketplace image, custom image or formula).
    # The setup of the virtual network to be used may also affect the properties.
    
    $parameters = @{
       "name"      = $NewVmName;
       "location"  = $lab.Location;
       "properties" = @{
          "labVirtualNetworkId"     = $virtualNetwork.ResourceId;
          "labSubnetName"           = $labSubnetName;
          "notes"                   = "Windows Server 2016 Datacenter";
          "osType"                  = "windows"
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
       }
    }
    
    #The following line is the same as invoking
    # https://azure.github.io/projects/apis/#!/Labs/Labs_CreateEnvironment rest api

    Invoke-AzureRmResourceAction -ResourceId $lab.ResourceId -Action 'createEnvironment' -Parameters $parameters -ApiVersion $API_VERSION -Force -Verbose
}
finally {
   popd
}
```

A fenti szkript a virtuális gép tulajdonságainak lehetővé számunkra, hogy a virtuális gép létrehozása a Windows Server 2016 DataCenter, az operációs rendszer. Az egyes virtuális gépet ezeket a tulajdonságokat némiképp eltérő lesz. A [határozza meg a virtuális gép](#define-virtual-machine) szakasz bemutatja, hogyan használja ezt a szkriptet a tulajdonságok meghatározásához.

A következő parancsot a mentett fájl nevét a parancsfájl futtatásától szemlélteti: Create-LabVirtualMachine.ps1. 

```powershell
 PS> .\Create-LabVirtualMachine.ps1 -ResourceGroupName 'MyLabResourceGroup' -LabName 'MyLab' -userName 'AdminUser' -password 'Password1!' -VMName 'MyLabVM'
```

## <a name="define-virtual-machine"></a>Virtuális gép definíciója
Ez a szakasz bemutatja, hogyan hozhatja ki a létrehozni kívánt virtuális gép egy típusú jellemző tulajdonságok. 

### <a name="use-azure-portal"></a>Az Azure Portal használata
Az Azure Resource Manager-sablon is létrehozhat, ha a virtuális gép létrehozása az Azure Portalon. Nem kell végezze el a virtuális gép létrehozásának folyamatán. Ön csak akkor kövesse a lépéseket, amíg meg nem látja a sablont. Ez az a legjobb módszer a szükséges JSON-leírásuk kaphat, ha Ön még nem rendelkezik egy létrehozott virtuális laborban. 

1. Lépjen az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **minden szolgáltatás** a bal oldali navigációs menüben.
3. Keresse meg és válassza **DevTest Labs** a szolgáltatások listájából. 
4. Az a **DevTest Labs** lapon, a tesztkörnyezetek listájában válassza ki a labor.
5. Válassza ki a labor kezdőlapja, **+ Hozzáadás** az eszköztáron. 
6. Válassza ki a **alaplemezkép** a virtuális gép számára. 
7. Válassza ki **automatizálási lehetőségek** fenti az oldal alján a **küldés** gombra. 
8. Megjelenik a **Azure Resource Manager-sablon** a virtuális gép létrehozásához. 
9. A JSON-szegmenst a **erőforrások** szakasz a korábban kiválasztott kép típus definíciója. 

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

Ebben a példában bemutatjuk, hogyan Azure Marketplace-beli kép definíciójának beolvasása. Egyéni rendszerkép, egy képlet vagy egy környezet definíciójának azonos módon olvashatók be. Adja hozzá a virtuális géphez szükséges összes összetevőt, és állítsa be a szükséges a speciális beállításokat. Miután megadta az értékeket a kötelező mezőket, és a választható mezőket, mielőtt kiválasztja a **automatizálási lehetőségek** gombra.

### <a name="use-azure-rest-api"></a>Az Azure REST API használata
Az alábbi eljárás lépéseit egy lemezkép tulajdonságainak lekérése a REST API-val biztosít: Ezek a lépések csak a meglévő virtuális gép tesztkörnyezetben. 

1. Keresse meg a [listázása virtuálisgép -](/rest/api/dtl/virtualmachines/list) lapon jelölje be **kipróbálás** gombra. 
2. Válassza ki az **Azure-előfizetését**.
3. Adja meg a **erőforráscsoport a laborhoz**.
4. Adja meg a **neve a labor**. 
5. Válassza a **Futtatás** lehetőséget.
6. Megjelenik a **a lemezkép tulajdonságainak** alapján a virtuális gép létrejött. 



## <a name="next-steps"></a>További lépések
Tekintse meg az alábbi tartalommal: [Az Azure PowerShell-dokumentáció az Azure DevTest Labs szolgáltatásban](/powershell/module/az.devtestlabs/)