---
title: Azure Cloud Shell rövid útmutató – PowerShell
description: Megtudhatja, hogyan használhatja a PowerShellt a böngészőben a Azure Cloud Shell használatával.
author: maertendmsft
ms.author: damaerte
tags: azure-resource-manager
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 10/18/2018
ms.openlocfilehash: 72261989b7cee9d2251eb18b36431ec807b0e874
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79273006"
---
# <a name="quickstart-for-powershell-in-azure-cloud-shell"></a>A PowerShell rövid útmutatója Azure Cloud Shell

Ez a dokumentum részletesen ismerteti, hogyan használható a PowerShell a [Azure Portal](https://portal.azure.com/)Cloud Shellban.

> [!NOTE]
> Azure Cloud Shell rövid útmutatóban is elérhető [bash](quickstart.md) .

## <a name="start-cloud-shell"></a>Kezdés Cloud Shell

1. Kattintson **Cloud Shell** gombra a Azure Portal felső navigációs sávján.

   ![](media/quickstart-powershell/shell-icon.png)

2. Válassza ki a PowerShell-környezetet a legördülő menüből, és az Azure Drive `(Azure:)`

   ![](media/quickstart-powershell/environment-ps.png)

## <a name="run-powershell-commands"></a>PowerShell-parancsok futtatása

Futtasson rendszeres PowerShell-parancsokat a Cloud Shellban, például:

```azurepowershell-interactive
PS Azure:\> Get-Date

# Expected Output
Friday, July 27, 2018 7:08:48 AM

PS Azure:\> Get-AzVM -Status

# Expected Output
ResourceGroupName       Name       Location                VmSize   OsType     ProvisioningState  PowerState
-----------------       ----       --------                ------   ------     -----------------  ----------
MyResourceGroup2        Demo        westus         Standard_DS1_v2  Windows    Succeeded           running
MyResourceGroup         MyVM1       eastus            Standard_DS1  Windows    Succeeded           running
MyResourceGroup         MyVM2       eastus   Standard_DS2_v2_Promo  Windows    Succeeded           deallocated
```

## <a name="navigate-azure-resources"></a>Azure-erőforrások navigálása

 1. `Azure` meghajtóról származó összes előfizetés listázása

    ```azurepowershell-interactive
    PS Azure:\> dir
    ```

 2. `cd` az előnyben részesített előfizetéshez

    ```azurepowershell-interactive
    PS Azure:\> cd MySubscriptionName
    PS Azure:\MySubscriptionName>
    ```

 3. Az összes Azure-erőforrás megtekintése a jelenlegi előfizetésben

    Írja be a `dir`t az Azure-erőforrások több nézetének listázásához.

    ```azurepowershell-interactive
    PS Azure:\MySubscriptionName> dir

        Directory: azure:\MySubscriptionName

    Mode Name
    ---- ----
    +    AllResources
    +    ResourceGroups
    +    StorageAccounts
    +    VirtualMachines
    +    WebApps
    ```

### <a name="allresources-view"></a>AllResources nézet

Az Azure-erőforrások megtekintéséhez írja be a `dir` `AllResources` könyvtár alatt.

```azurepowershell-interactive
PS Azure:\MySubscriptionName> dir AllResources
```

### <a name="explore-resource-groups"></a>Erőforráscsoportok megismerése

 Megtekintheti a `ResourceGroups` könyvtárat, és egy adott erőforráscsoporthoz is megtalálhatja a virtuális gépeket.

```azurepowershell-interactive
PS Azure:\MySubscriptionName> cd ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines

PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines> dir


    Directory: Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines


VMName    Location   ProvisioningState VMSize          OS            SKU             OSVersion AdminUserName  NetworkInterfaceName
------    --------   ----------------- ------          --            ---             --------- -------------  --------------------
TestVm1   westus     Succeeded         Standard_DS2_v2 WindowsServer 2016-Datacenter Latest    AdminUser      demo371
TestVm2   westus     Succeeded         Standard_DS1_v2 WindowsServer 2016-Datacenter Latest    AdminUser      demo271
```

> [!NOTE]
> Megfigyelheti, hogy a `dir`beírásakor a második alkalommal a Cloud Shell sokkal gyorsabban képes megjeleníteni az elemeket.
> Ennek az az oka, hogy a gyermek elemeket a rendszer a memóriában gyorsítótárazza a jobb felhasználói élmény érdekében.
Azonban mindig a `dir -Force` használatával kérheti le a friss adatgyűjtést.

### <a name="navigate-storage-resources"></a>Tárolási erőforrások navigálása

Ha beírja a `StorageAccounts` könyvtárat, könnyedén megtekintheti az összes tárolási erőforrást

```azurepowershell-interactive
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> dir

    Directory: Azure:\MySubscriptionNameStorageAccounts\MyStorageAccountName\Files

Name          ConnectionString
----          ----------------
MyFileShare1  \\MyStorageAccountName.file.core.windows.net\MyFileShare1;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare2  \\MyStorageAccountName.file.core.windows.net\MyFileShare2;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare3  \\MyStorageAccountName.file.core.windows.net\MyFileShare3;AccountName=MyStorageAccountName AccountKey=<key>
```

A kapcsolódási karakterlánccal a következő parancs használatával csatlakoztathatja a Azure Files-megosztást.

```azurepowershell-interactive
net use <DesiredDriveLetter>: \\<MyStorageAccountName>.file.core.windows.net\<MyFileShareName> <AccountKey> /user:Azure\<MyStorageAccountName>
```

Részletekért lásd: [Azure Files-megosztás csatlakoztatása és a megosztás elérése a Windowsban][azmount].

A Azure Files megosztás alatt lévő címtárakat a következőképpen is megtekintheti:

```azurepowershell-interactive
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> cd .\MyFileShare1\
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files\MyFileShare1> dir

Mode  Name
----  ----
+     TestFolder
.     hello.ps1
```

### <a name="interact-with-virtual-machines"></a>Kommunikáció a virtuális gépekkel

A jelenlegi előfizetéshez tartozó összes virtuális gép `VirtualMachines` Directoryn keresztül található.

```azurepowershell-interactive
PS Azure:\MySubscriptionName\VirtualMachines> dir

    Directory: Azure:\MySubscriptionName\VirtualMachines


Name       ResourceGroupName  Location  VmSize          OsType              NIC ProvisioningState  PowerState
----       -----------------  --------  ------          ------              --- -----------------  ----------
TestVm1    MyResourceGroup1   westus    Standard_DS2_v2 Windows       my2008r213         Succeeded     stopped
TestVm2    MyResourceGroup1   westus    Standard_DS1_v2 Windows          jpstest         Succeeded deallocated
TestVm10   MyResourceGroup2   eastus    Standard_DS1_v2 Windows           mytest         Succeeded     running
```

#### <a name="invoke-powershell-script-across-remote-vms"></a>PowerShell-parancsfájl meghívása távoli virtuális gépek között

 > [!WARNING]
 > Tekintse meg az Azure-beli [virtuális gépek távoli felügyeletének hibaelhárítását ismertető témakört](troubleshooting.md#troubleshooting-remote-management-of-azure-vms).

  Feltételezve, hogy rendelkezik egy virtuális géppel, a MyVM1 `Invoke-AzVMCommand` használatával meghívja a távoli gépen a PowerShell-szkriptek blokkját.

  ```azurepowershell-interactive
  Enable-AzVMPSRemoting -Name MyVM1 -ResourceGroupname MyResourceGroup
  Invoke-AzVMCommand -Name MyVM1 -ResourceGroupName MyResourceGroup -Scriptblock {Get-ComputerInfo} -Credential (Get-Credential)
  ```

  Először is megnyithatja a VirtualMachines könyvtárat, és a következőképpen futtathatja `Invoke-AzVMCommand`.

  ```azurepowershell-interactive
  PS Azure:\> cd MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines
  PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Invoke-AzVMCommand -Scriptblock {Get-ComputerInfo} -Credential (Get-Credential)

  # You will see output similar to the following:

  PSComputerName                                          : 65.52.28.207
  RunspaceId                                              : 2c2b60da-f9b9-4f42-a282-93316cb06fe1
  WindowsBuildLabEx                                       : 14393.1066.amd64fre.rs1_release_sec.170327-1835
  WindowsCurrentVersion                                   : 6.3
  WindowsEditionId                                        : ServerDatacenter
  WindowsInstallationType                                 : Server
  WindowsInstallDateFromRegistry                          : 5/18/2017 11:26:08 PM
  WindowsProductId                                        : 00376-40000-00000-AA947
  WindowsProductName                                      : Windows Server 2016 Datacenter
  WindowsRegisteredOrganization                           :
   ...
  ```

#### <a name="interactively-log-on-to-a-remote-vm"></a>Interaktív bejelentkezés egy távoli virtuális gépre

Az Azure-ban futó virtuális gépeken való interaktív bejelentkezéshez `Enter-AzVM`t használhat.

  ```azurepowershell-interactive
  PS Azure:\> Enter-AzVM -Name MyVM1 -ResourceGroupName MyResourceGroup -Credential (Get-Credential)
  ```

Először is megnyithatja a `VirtualMachines` könyvtárat, és a következőképpen futtathatja `Enter-AzVM`

  ```azurepowershell-interactive
 PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Enter-AzVM -Credential (Get-Credential)
 ```

### <a name="discover-webapps"></a>WebApps felderítése

Ha beírja a `WebApps` könyvtárat, könnyedén navigálhat a Web Apps-erőforrásokhoz

```azurepowershell-interactive
PS Azure:\MySubscriptionName> dir .\WebApps\

    Directory: Azure:\MySubscriptionName\WebApps

Name            State    ResourceGroup      EnabledHostNames                  Location
----            -----    -------------      ----------------                  --------
mywebapp1       Stopped  MyResourceGroup1   {mywebapp1.azurewebsites.net...   West US
mywebapp2       Running  MyResourceGroup2   {mywebapp2.azurewebsites.net...   West Europe
mywebapp3       Running  MyResourceGroup3   {mywebapp3.azurewebsites.net...   South Central US

# You can use Azure cmdlets to Start/Stop your web apps
PS Azure:\MySubscriptionName\WebApps> Start-AzWebApp -Name mywebapp1 -ResourceGroupName MyResourceGroup1

Name           State    ResourceGroup        EnabledHostNames                   Location
----           -----    -------------        ----------------                   --------
mywebapp1      Running  MyResourceGroup1     {mywebapp1.azurewebsites.net ...   West US

# Refresh the current state with -Force
PS Azure:\MySubscriptionName\WebApps> dir -Force

    Directory: Azure:\MySubscriptionName\WebApps

Name            State    ResourceGroup      EnabledHostNames                  Location
----            -----    -------------      ----------------                  --------
mywebapp1       Running  MyResourceGroup1   {mywebapp1.azurewebsites.net...   West US
mywebapp2       Running  MyResourceGroup2   {mywebapp2.azurewebsites.net...   West Europe
mywebapp3       Running  MyResourceGroup3   {mywebapp3.azurewebsites.net...   South Central US
```

## <a name="ssh"></a>SSH

Ha SSH-val szeretne hitelesítést végezni a kiszolgálókon vagy virtuális gépeken, hozza a nyilvános titkos kulcspár Cloud Shell, és tegye közzé a nyilvános kulcsot a távoli gépen `authorized_keys`, például `/home/user/.ssh/authorized_keys`.

> [!NOTE]
> Az SSH nyilvános kulcsokat a `ssh-keygen` segítségével hozhatja létre, és közzéteheti őket a Cloud Shell `$env:USERPROFILE\.ssh`.

### <a name="using-ssh"></a>Az SSH használata

Az új [virtuálisgép-konfiguráció](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-powershell) Azure PowerShell parancsmagokkal történő létrehozásához kövesse az alábbi utasításokat.
Mielőtt megkezdi az üzembe helyezést a `New-AzVM` a telepítés elindításához, adjon hozzá egy nyilvános SSH-kulcsot a virtuális gép konfigurációjához.
Az újonnan létrehozott virtuális gép a `~\.ssh\authorized_keys` helyen található nyilvános kulcsot fogja tartalmazni, ami lehetővé teszi a hitelesítő adatokkal való ingyenes SSH-munkamenetet a virtuális géphez.

```azurepowershell-interactive
# Create VM config object - $vmConfig using instructions on linked page above

# Generate SSH keys in Cloud Shell
ssh-keygen -t rsa -b 2048 -f $HOME\.ssh\id_rsa 

# Ensure VM config is updated with SSH keys
$sshPublicKey = Get-Content "$HOME\.ssh\id_rsa.pub"
Add-AzVMSshPublicKey -VM $vmConfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

# Create a virtual machine
New-AzVM -ResourceGroupName <yourResourceGroup> -Location <vmLocation> -VM $vmConfig

# SSH to the VM
ssh azureuser@MyVM.Domain.Com
```

## <a name="list-available-commands"></a>Elérhető parancsok listázása

A `Azure` meghajtó alatt írja be a `Get-AzCommand` parancsot a környezetfüggő Azure-parancsok beszerzéséhez.

Azt is megteheti, hogy az elérhető Azure-parancsok megkereséséhez mindig `Get-Command *az* -Module Az.*` használ.

## <a name="install-custom-modules"></a>Egyéni modulok telepítése

`Install-Module` futtatásával modulokat telepíthet a [PowerShell-galériaból][gallery].

## <a name="get-help"></a>Get-Help

Írja be a `Get-Help`t a PowerShell-lel kapcsolatos információk beszerzéséhez Azure Cloud Shellban.

```azurepowershell-interactive
Get-Help
```

Egy adott parancs esetében továbbra is `Get-Help`, majd egy parancsmag követhető.

```azurepowershell-interactive
Get-Help Get-AzVM
```

## <a name="use-azure-files-to-store-your-data"></a>Az adattároláshoz használjon Azure Files

Létrehozhat egy parancsfájlt, például `helloworld.ps1`t, és mentheti a `clouddrive` a rendszerhéj-munkamenetek közötti használathoz.

```azurepowershell-interactive
cd $HOME\clouddrive
# Create a new file in clouddrive directory
New-Item helloworld.ps1
# Open the new file for editing
code .\helloworld.ps1
# Add the content, such as 'Hello World!'
.\helloworld.ps1
Hello World!
```

Amikor legközelebb a PowerShellt használja Cloud Shellban, a `helloworld.ps1` fájl az `$HOME\clouddrive` könyvtár alatt található, amely a Azure Files-megosztást csatlakoztatja.

## <a name="use-custom-profile"></a>Egyéni profil használata

A PowerShell-környezet testreszabásához PowerShell-profil (ok) létrehozásával – `profile.ps1` (vagy `Microsoft.PowerShell_profile.ps1`) hozható létre.
Mentse azt `$profile.CurrentUserAllHosts` (vagy `$profile.CurrentUserAllHosts`) alá, hogy az Cloud Shell-munkamenetben lévő összes PowerShellben betölthető legyen.

A profilok létrehozásával kapcsolatban tekintse meg a [About Profiles című témakört][profile].

## <a name="use-git"></a>A git használata

Git-tárház klónozásához a Cloud Shell létre kell hoznia egy [személyes hozzáférési jogkivonatot][githubtoken] , és azt felhasználónévként kell használni. Ha rendelkezik a jogkivonattal, a következő módon klónozott a tárházat:

```azurepowershell-interactive
  git clone https://<your-access-token>@github.com/username/repo.git
```

## <a name="exit-the-shell"></a>Kilépés a rendszerhéjból

A munkamenet megszakításához írja be a `exit`.

[bashqs]:quickstart.md
[gallery]:https://www.powershellgallery.com/
[customex]:https://docs.microsoft.com/azure/virtual-machines/windows/extensions-customscript
[profile]: https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/about/about_profiles
[azmount]: https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows
[githubtoken]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
