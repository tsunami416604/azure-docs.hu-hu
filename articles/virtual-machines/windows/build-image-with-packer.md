---
title: Windows Azure Virtuálisgép-lemezképek létrehozása a csomagoló |} Microsoft Docs
description: Csomagoló használata Windows virtuális gépek létrehozását az Azure-ban
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/29/2018
ms.author: iainfou
ms.openlocfilehash: d84e8d41d79d9800c952ba3ef0e89e0cdfbdd8dd
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34850644"
---
# <a name="how-to-use-packer-to-create-windows-virtual-machine-images-in-azure"></a>Windows virtuális gép képek létrehozása az Azure-ban a csomagoló segítségével
Minden virtuális gép (VM) az Azure-ban, amely meghatározza a Windows terjesztési és az operációs rendszer verziója lemezkép jön létre. Lemezképek előre telepített alkalmazások és konfigurációk tartalmazhatnak. Az Azure piactéren sok első és a külső képek biztosít a leggyakrabban használt operációs rendszer, és alkalmazás környezetekben, vagy a saját egyéni lemezképek igényeinek igazított hozhat létre. Ez a cikk részletesen a nyílt forráskódú eszköz [csomagoló](https://www.packer.io/) definiálására és egyéni lemezképeket az Azure-ban.


## <a name="create-azure-resource-group"></a>Azure erőforráscsoport létrehozása
A létrehozási folyamat során a csomagoló ideiglenes Azure-erőforrások létrehozza a, a forrás Virtuálisgép-buildekről nyújtanak. A forrás virtuális gép képként rögzíti, meg kell határoznia egy erőforráscsoportot. Ez az erőforráscsoport tárolja a csomagoló felépítési folyamat kimenetét.

Hozzon létre egy erőforráscsoportot a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *EastUS* helyen:

```powershell
$rgName = "myResourceGroup"
$location = "East US"
New-AzureRmResourceGroup -Name $rgName -Location $location
```

## <a name="create-azure-credentials"></a>Az Azure hitelesítő adatok létrehozása
Csomagoló egyszerű szolgáltatás használatával végzi a hitelesítést. Egy Azure szolgáltatás egyszerű egy biztonsági azonosító, amely alkalmazások, szolgáltatások és automatizálási eszközökkel, például a csomagoló használható. Szabályozza, és adja meg az engedélyeket, hogy milyen műveletek a szolgáltatás egyszerű hajthat végre az Azure-ban.

Az egyszerű szolgáltatás létrehozása [New-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal) és engedélyeket a szolgáltatás egyszerű létrehozása és erőforrások kezelése [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment):

```powershell
$sp = New-AzureRmADServicePrincipal -DisplayName "AzurePacker" `
    -Password (ConvertTo-SecureString "P@ssw0rd!" -AsPlainText -Force)
Sleep 20
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

A hitelesítéshez az Azure-ba is be kell szereznie a Azure bérlői és előfizetés-azonosító a [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription):

```powershell
$sub = Get-AzureRmSubscription
$sub.TenantId[0]
$sub.SubscriptionId[0]
```

A két azonosító használja a következő lépésben.


## <a name="define-packer-template"></a>Adja meg a csomagoló sablon
Lemezképeket, létrehozhat egy sablon JSON-fájlként. A sablon megadása a létrehozói és a tényleges felépítési folyamat végrehajtott provisioners. Csomagoló rendelkezik egy [az Azure-szerkesztő](https://www.packer.io/docs/builders/azure.html) , amely lehetővé teszi, hogy adható meg az Azure-erőforrások, például a szolgáltatás egyszerű létrehozott hitelesítő adatok az előző lépésben.

Hozzon létre egy fájlt *windows.json* , majd illessze be a következő tartalmat. Adja meg a saját értékeket a következő:

| Paraméter                           | Beszerzési helyét |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | Nézet szolgáltatás résztvevő-azonosító az `$sp.applicationId` |
| *client_secret*                     | A megadott jelszó `$securePassword` |
| *tenant_id*                         | A kimeneti `$sub.TenantId` parancs |
| *subscription_id*                   | A kimeneti `$sub.SubscriptionId` parancs |
| *object_id*                         | A nézet szolgáltatás egyszerű objektum azonosítója: `$sp.Id` |
| *managed_image_resource_group_name* | Az első lépésben létrehozott erőforráscsoport nevét |
| *managed_image_name*                | A felügyelt lemezképe létrehozott nevét |

```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "0831b578-8ab6-40b9-a581-9a880a94aab1",
    "client_secret": "P@ssw0rd!",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "subscription_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "object_id": "a7dfb070-0d5b-47ac-b9a5-cf214fff0ae2",

    "managed_image_resource_group_name": "myResourceGroup",
    "managed_image_name": "myPackerImage",

    "os_type": "Windows",
    "image_publisher": "MicrosoftWindowsServer",
    "image_offer": "WindowsServer",
    "image_sku": "2016-Datacenter",

    "communicator": "winrm",
    "winrm_use_ssl": "true",
    "winrm_insecure": "true",
    "winrm_timeout": "3m",
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

Ez a sablon összeállít egy Windows Server 2016 virtuális Gépet, telepíti az IIS szolgáltatást, majd a virtuális Gépet a Sysprep használatúvá. Az IIS telepítését mutatja, hogyan használhatja a PowerShell webhelykiépítőt további parancsok futtatásához. A végső csomagoló lemezkép ezután tartalmazza a szükséges szoftverek telepítése és konfigurálása.


## <a name="build-packer-image"></a>Csomagoló lemezkép
Ha még nincs telepítve a helyi számítógépre csomagoló [csomagoló telepítési utasításokat](https://www.packer.io/docs/install/index.html).

Megadásával hozhat létre a lemezképet a csomagoló sablonfájl az alábbiak szerint:

```bash
./packer build windows.json
```

A kimenet a fenti parancsok például a következőképpen történik:

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

A virtuális gép létrehozása, a provisioners futtatására, és a központi telepítés tisztítása csomagoló néhány percet vesz igénybe.


## <a name="create-a-vm-from-the-packer-image"></a>Hozzon létre egy virtuális Gépet a csomagoló lemezképből
Mostantól létrehozhat egy virtuális Gépet a lemezkép [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). A támogató hálózati erőforrások jönnek létre, ha még nem léteznek. Amikor a rendszer kéri, adjon meg egy rendszergazda felhasználónevet és jelszót hozható létre a virtuális Gépen. Az alábbi példakód létrehozza a virtuális gépek nevű *myVM* a *myPackerImage*:

```powershell
New-AzureRmVm `
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

Ha létrehozott egy virtuális gépet egy másik erőforráscsoportban található vagy a régió, mint a csomagoló lemezkép, adja meg a lemezkép neve helyett a lemezkép-Azonosítót. Ezt úgy szerezheti be a lemezkép-Azonosítót a [Get-AzureRmImage](/powershell/module/AzureRM.Compute/Get-AzureRmImage).

A csomagoló lemezképből a virtuális gép létrehozásához néhány percet vesz igénybe.


## <a name="test-vm-and-webserver"></a>Teszt virtuális gép és a webkiszolgáló
Kérje le a virtuális gép nyilvános IP-címét a [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) paranccsal. A következő példa a korábban létrehozott *myPublicIP* IP-címét kéri le:

```powershell
Get-AzureRmPublicIPAddress `
    -ResourceGroupName $rgName `
    -Name "myPublicIPAddress" | select "IpAddress"
```

Tekintse meg a virtuális Gépet, az IIS telepítése a csomagoló webhelykiépítőt tartalmaz a művelet, írja be a nyilvános IP-címet a webböngésző.

![Alapértelmezett IIS-webhely](./media/build-image-with-packer/iis.png) 


## <a name="next-steps"></a>További lépések
Ebben a példában a csomagoló már telepített IIS szolgáltatást egy Virtuálisgép-lemezkép létrehozásához használt. A Virtuálisgép-lemezkép mellett a meglévő központi telepítési munkafolyamatai, például segítségével telepítse az alkalmazást az Team Services, Ansible, Chef vagy Puppet lemezkép alapján létrehozott virtuális gépek.

További példa csomagoló sablonokat más Windows disztribúciókkal, lásd: [a GitHub-tárház](https://github.com/hashicorp/packer/tree/master/examples/azure).
