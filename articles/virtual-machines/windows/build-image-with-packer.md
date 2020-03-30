---
title: Windows virtuálisgép-lemezképek létrehozása a Packerrel az Azure-ban
description: Megtudhatja, hogy a Packer segítségével hogyan hozhat létre képeket az Azure-beli Windows-virtuális gépekről
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/22/2019
ms.author: cynthn
ms.openlocfilehash: b2ff9869b0de7a0285644bea462101cd1dc80b99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74039224"
---
# <a name="how-to-use-packer-to-create-windows-virtual-machine-images-in-azure"></a>A Packer használata Windows virtuálisgép-lemezképek létrehozásához az Azure-ban
Az Azure-ban minden virtuális gép (VM) egy olyan lemezképből jön létre, amely meghatározza a Windows-disztribúciót és az operációs rendszer verzióját. A képek előre telepített alkalmazásokat és konfigurációkat tartalmazhatnak. Az Azure Marketplace számos első és harmadik féltől származó lemezképet biztosít a leggyakoribb operációs rendszer- és alkalmazáskörnyezetekben, vagy létrehozhat jattot az igényeinek megfelelő egyéni lemezképekhez. Ez a cikk bemutatja, hogyan használhatja a nyílt forráskódú eszközt [Packer](https://www.packer.io/) egyéni lemezképek definiálására és létrehozására az Azure-ban.

Ezt a cikket utoljára 2019.21.21-én tesztelték az [Az PowerShell modul](https://docs.microsoft.com/powershell/azure/install-az-ps) 1.3.0-s és [a Packer](https://www.packer.io/docs/install/index.html) 1.3.4-es verziójával.

> [!NOTE]
> Az Azure most már rendelkezik egy szolgáltatás, az Azure Image Builder (előzetes verzió), a saját egyéni lemezképek meghatározásához és létrehozásához. Az Azure Image Builder a Packerre épül, így akár a meglévő Packer-rendszerhéj-kiépítési parancsfájlokat is használhatja vele. Az Azure Image Builder ismerkedése: Windows virtuális gép létrehozása az Azure Image Builder segítségével című [témakörben.](image-builder.md)

## <a name="create-azure-resource-group"></a>Azure-erőforráscsoport létrehozása
A létrehozási folyamat során packer ideiglenes Azure-erőforrásokat hoz létre a forrás virtuális gép létrehozása során. A forrás virtuális gép lemezképként való rögzítéséhez meg kell határoznia egy erőforráscsoportot. A Packer-létrehozási folyamat kimenete ebben az erőforráscsoportban tárolódik.

Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)segítségével. A következő példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen:

```azurepowershell
$rgName = "myResourceGroup"
$location = "East US"
New-AzResourceGroup -Name $rgName -Location $location
```

## <a name="create-azure-credentials"></a>Azure-beli hitelesítő adatok létrehozása
A Packer egy egyszerű szolgáltatás használatával hitelesíti magát az Azure-ral. Az Azure egyszerű szolgáltatásegy biztonsági identitás, amely az alkalmazások, szolgáltatások és automatizálási eszközök, például a Packer használható. Szabályozhatja és meghatározhatja az engedélyeket, hogy milyen műveleteket hajthat végre az egyszerű szolgáltatás az Azure-ban.

Hozzon létre egy egyszerű szolgáltatása a [New-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal) szolgáltatással, és rendeljen engedélyeket az egyszerű szolgáltatáshoz az erőforrások [new-azroleassignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment)segítségével való létrehozásához és kezeléséhez. Az értéknek `-DisplayName` egyedinek kell lennie; szükség szerint cserélje ki a saját értékére.  

```azurepowershell
$sp = New-AzADServicePrincipal -DisplayName "PackerServicePrincipal"
$BSTR = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($sp.Secret)
$plainPassword = [System.Runtime.InteropServices.Marshal]::PtrToStringAuto($BSTR)
New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

Ezután adja ki a jelszót és az alkalmazásazonosítót.

```powershell
$plainPassword
$sp.ApplicationId
```


Az Azure-ban való hitelesítéshez be kell szereznie az Azure-bérlői és előfizetési azonosítóit is a [Get-AzSubscription segítségével:](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription)

```powershell
Get-AzSubscription
```


## <a name="define-packer-template"></a>Packer-sablon definiálása
Képek létrehozásához hozzon létre egy sablont JSON-fájlként. A sablonban megadhatja a tényleges buildelő eljárást megvalósító építők és kiépítők. A Packer rendelkezik egy [Azure-szerkesztővel,](https://www.packer.io/docs/builders/azure.html) amely lehetővé teszi az Azure-erőforrások, például az előző lépésben létrehozott egyszerű szolgáltatáshitelesítő adatok meghatározását.

Hozzon létre egy *windows.json* nevű fájlt, és illessze be a következő tartalmat. Adja meg saját értékeit a következőkre:

| Paraméter                           | Hol lehet beszerezni |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | Egyszerű szolgáltatásazonosító megtekintése`$sp.applicationId` |
| *client_secret*                     | Az automatikusan létrehozott jelszó megtekintése`$plainPassword` |
| *tenant_id*                         | Kimenet `$sub.TenantId` parancsból |
| *subscription_id*                   | Kimenet `$sub.SubscriptionId` parancsból |
| *managed_image_resource_group_name* | Az első lépésben létrehozott erőforráscsoport neve |
| *managed_image_name*                | A létrehozott felügyelt lemezkép neve |

```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "client_secret": "ppppppp-pppp-pppp-pppp-ppppppppppp",
    "tenant_id": "zzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz",
    "subscription_id": "yyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyy",

    "managed_image_resource_group_name": "myResourceGroup",
    "managed_image_name": "myPackerImage",

    "os_type": "Windows",
    "image_publisher": "MicrosoftWindowsServer",
    "image_offer": "WindowsServer",
    "image_sku": "2016-Datacenter",

    "communicator": "winrm",
    "winrm_use_ssl": true,
    "winrm_insecure": true,
    "winrm_timeout": "5m",
    "winrm_username": "packer",

    "azure_tags": {
        "dept": "Engineering",
        "task": "Image deployment"
    },

    "location": "East US",
    "vm_size": "Standard_DS2_v2"
  }],
  "provisioners": [{
    "type": "powershell",
    "inline": [
      "Add-WindowsFeature Web-Server",
      "& $env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit",
      "while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 10  } else { break } }"
    ]
  }]
}
```

Ez a sablon létrehoz egy Windows Server 2016 virtuális gép, telepíti az IIS, majd általánosítja a virtuális gép Sysprep. Az IIS-telepítés bemutatja, hogyan használhatja a PowerShell-kiépítő további parancsok futtatásához. A végleges Packer-lemezkép tartalmazza a szükséges szoftvertelepítést és -konfigurációt.


## <a name="build-packer-image"></a>Packer-kép létrehozása
Ha még nem telepítette a Csomagolót a helyi számítógépre, [kövesse a Csomagoló telepítési utasításait.](https://www.packer.io/docs/install/index.html)

A rendszerkép létrehozása egy cmd-parancssor megnyitásával és a Packer-sablonfájl megadásával a következőképpen:

```
./packer build windows.json
```

Az előző parancsok kimenetének egyik példája a következő:

```bash
azure-arm output will be in this color.

==> azure-arm: Running builder ...
    azure-arm: Creating Azure Resource Manager (ARM) client ...
==> azure-arm: Creating resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> Location          : ‘East US’
==> azure-arm:  -> Tags              :
==> azure-arm:  ->> task : Image deployment
==> azure-arm:  ->> dept : Engineering
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Getting the certificate’s URL ...
==> azure-arm:  -> Key Vault Name        : ‘pkrkvpq0mthtbtt’
==> azure-arm:  -> Key Vault Secret Name : ‘packerKeyVaultSecret’
==> azure-arm:  -> Certificate URL       : ‘https://pkrkvpq0mthtbtt.vault.azure.net/secrets/packerKeyVaultSecret/8c7bd823e4fa44e1abb747636128adbb'
==> azure-arm: Setting the certificate’s URL ...
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Getting the VM’s IP address ...
==> azure-arm:  -> ResourceGroupName   : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> PublicIPAddressName : ‘packerPublicIP’
==> azure-arm:  -> NicName             : ‘packerNic’
==> azure-arm:  -> Network Connection  : ‘PublicEndpoint’
==> azure-arm:  -> IP Address          : ‘40.76.55.35’
==> azure-arm: Waiting for WinRM to become available...
==> azure-arm: Connected to WinRM!
==> azure-arm: Provisioning with Powershell...
==> azure-arm: Provisioning with shell script: /var/folders/h1/ymh5bdx15wgdn5hvgj1wc0zh0000gn/T/packer-powershell-provisioner902510110
    azure-arm: #< CLIXML
    azure-arm:
    azure-arm: Success Restart Needed Exit Code      Feature Result
    azure-arm: ------- -------------- ---------      --------------
    azure-arm: True    No             Success        {Common HTTP Features, Default Document, D...
    azure-arm: <Objs Version=“1.1.0.1” xmlns=“http://schemas.microsoft.com/powershell/2004/04"><Obj S=“progress” RefId=“0"><TN RefId=“0”><T>System.Management.Automation.PSCustomObject</T><T>System.Object</T></TN><MS><I64 N=“SourceId”>1</I64><PR N=“Record”><AV>Preparing modules for first use.</AV><AI>0</AI><Nil /><PI>-1</PI><PC>-1</PC><T>Completed</T><SR>-1</SR><SD> </SD></PR></MS></Obj></Objs>
==> azure-arm: Querying the machine’s properties ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> ComputeName       : ‘pkrvmpq0mthtbtt’
==> azure-arm:  -> Managed OS Disk   : ‘/subscriptions/guid/resourceGroups/packer-Resource-Group-pq0mthtbtt/providers/Microsoft.Compute/disks/osdisk’
==> azure-arm: Powering off machine ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> ComputeName       : ‘pkrvmpq0mthtbtt’
==> azure-arm: Capturing image ...
==> azure-arm:  -> Compute ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> Compute Name              : ‘pkrvmpq0mthtbtt’
==> azure-arm:  -> Compute Location          : ‘East US’
==> azure-arm:  -> Image ResourceGroupName   : ‘myResourceGroup’
==> azure-arm:  -> Image Name                : ‘myPackerImage’
==> azure-arm:  -> Image Location            : ‘eastus’
==> azure-arm: Deleting resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm: Deleting the temporary OS disk ...
==> azure-arm:  -> OS Disk : skipping, managed disk was used...
Build ‘azure-arm’ finished.

==> Builds finished. The artifacts of successful builds are:
--> azure-arm: Azure.ResourceManagement.VMImage:

ManagedImageResourceGroupName: myResourceGroup
ManagedImageName: myPackerImage
ManagedImageLocation: eastus
```

Néhány percet vesz igénybe, amíg a Packer létrehozza a virtuális gép, a kiépítőket, és a központi telepítés karbantartása.


## <a name="create-a-vm-from-the-packer-image"></a>Virtuális gép létrehozása a Packer-lemezképből
Most már létrehozhat egy virtuális gép a lemezkép a [New-AzVM.](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) A támogató hálózati erőforrások létrejönnek, ha még nem léteznek. Amikor a rendszer kéri, adjon meg egy rendszergazdai felhasználónevet és jelszót, amelyet a virtuális gépen kell létrehozni. A következő példa létrehoz egy *myVM* nevű virtuális gép a *myPackerImage-ből:*

```powershell
New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80 `
    -Image "myPackerImage"
```

Ha a Packer-lemezképnél eltérő erőforráscsoportban vagy régióban szeretne virtuális gépeket létrehozni, adja meg a lemezkép neve helyett a lemezképazonosítót. A képazonosítót a [Get-AzImage segítségével](https://docs.microsoft.com/powershell/module/az.compute/Get-AzImage)szerezheti be.

A virtuális gép létrehozása a Packer-lemezképből néhány percet vesz igénybe.


## <a name="test-vm-and-webserver"></a>Virtuális gép és webkiszolgáló tesztelése
Szerezze be a virtuális gép nyilvános IP-címét a [Get-AzPublicIPAddress segítségével.](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) A következő példa a korábban létrehozott *myPublicIP* IP-címét kéri le:

```powershell
Get-AzPublicIPAddress `
    -ResourceGroupName $rgName `
    -Name "myPublicIPAddress" | select "IpAddress"
```

A virtuális gép megtekintéséhez, amely magában foglalja az IIS telepítése a Packer-kiépítő, működés közben adja meg a nyilvános IP-címet egy webböngészőbe.

![Alapértelmezett IIS-webhely](./media/build-image-with-packer/iis.png) 


## <a name="next-steps"></a>További lépések
Az [Azure Image Builder](image-builder.md)meglévő Packer-kiépítési parancsfájlokat is használhat.
