---
title: PowerShell az Azure Cloud Shell (előzetes verzió) rövid |} A Microsoft Docs
description: A Cloud Shellbeli PowerShell a rövid útmutató
services: Azure
documentationcenter: ''
author: maertendmsft
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/27/2018
ms.author: damaerte
ms.openlocfilehash: adae7ea79ada9247382c88e58f1ba5331007985b
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39324493"
---
# <a name="quickstart-for-powershell-in-azure-cloud-shell-preview"></a>Rövid útmutató a Powershellhez az Azure Cloud Shellben (előzetes verzió)

Ez a dokumentum részletesen bemutatja a PowerShell használatával a Cloud shellben a [az Azure portal](https://aka.ms/PSCloudPreview).

> [!NOTE]
> A [az Azure Cloud Shell Bash](quickstart.md) a rövid útmutató esetében is elérhető.

## <a name="start-cloud-shell"></a>A Cloud Shell indítása

1. Kattintson a **Cloud Shell** gombot a felső navigációs sávban az Azure Portal

  ![](media/quickstart-powershell/shell-icon.png)

2. Válassza ki a PowerShell környezetet a legördülő listából, és az Azure-meghajtó `(Azure:)`

  ![](media/quickstart-powershell/environment-ps.png)

## <a name="run-powershell-commands"></a>PowerShell-parancsok futtatása

Például a Cloud shellben futtassa rendszeres PowerShell-parancsokat:

```azurepowershell-interactive
PS Azure:\> Get-Date

# Expected Output
Friday, July 27, 2018 7:08:48 AM

PS Azure:\> Get-AzureRmVM -Status

# Expected Output
ResourceGroupName       Name       Location                VmSize   OsType     ProvisioningState  PowerState
-----------------       ----       --------                ------   ------     -----------------  ----------
MyResourceGroup2        Demo        westus         Standard_DS1_v2  Windows    Succeeded           running
MyResourceGroup         MyVM1       eastus            Standard_DS1  Windows    Succeeded           running
MyResourceGroup         MyVM2       eastus   Standard_DS2_v2_Promo  Windows    Succeeded           deallocated
```

## <a name="navigate-azure-resources"></a>Keresse meg az Azure-erőforrások

 1. Az összes előfizetés listázásának `Azure` meghajtó

    ```azurepowershell-interactive
    PS Azure:\> dir
    ```

 2. `cd` az előnyben részesített előfizetéshez

    ```azurepowershell-interactive
    PS Azure:\> cd MySubscriptionName
    PS Azure:\MySubscriptionName>
    ```

 3. Az Azure-erőforrások az aktuális előfizetésben megtekintése

    Típus `dir` több nézetére is az Azure-erőforrások listáját.

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

### <a name="allresources-view"></a>AllResources megtekintése

Típus `dir` alatt `AllResources` címtár az Azure-erőforrások megtekintéséhez.

```azurepowershell-interactive
PS Azure:\MySubscriptionName> dir AllResources
```

### <a name="explore-resource-groups"></a>Ismerje meg az erőforráscsoportok

 Megnyithatja a `ResourceGroups` könyvtárat és a egy adott erőforráscsoportban található virtuális gépek.

```azureowershell-interactive
PS Azure:\MySubscriptionName> cd ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines

PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines> dir


    Directory: Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines


VMName    Location   ProvisioningState VMSize          OS            SKU             OSVersion AdminUserName  NetworkInterfaceName
------    --------   ----------------- ------          --            ---             --------- -------------  --------------------
TestVm1   westus     Succeeded         Standard_DS2_v2 WindowsServer 2016-Datacenter Latest    AdminUser      demo371
TestVm2   westus     Succeeded         Standard_DS1_v2 WindowsServer 2016-Datacenter Latest    AdminUser      demo271
```

> [!NOTE]
> A második alkalommal, amikor beírja is megfigyelheti, hogy `dir`, a cloud shellben viszont sokkal gyorsabb elemek megjelenítése.
> Ennek az oka a gyermekelemek jobb felhasználói élményt a memóriában gyorsítótárazza a rendszer.
Azonban bármikor használhat `dir -Force` friss adatok.

### <a name="navigate-storage-resources"></a>Keresse meg a storage-erőforrások

Írja be azokat a `StorageAccounts` könyvtárban, egyszerűen navigálhat a tároló-erőforrások

```azureowershell-interactive
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> dir

    Directory: Azure:\MySubscriptionNameStorageAccounts\MyStorageAccountName\Files

Name          ConnectionString
----          ----------------
MyFileShare1  \\MyStorageAccountName.file.core.windows.net\MyFileShare1;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare2  \\MyStorageAccountName.file.core.windows.net\MyFileShare2;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare3  \\MyStorageAccountName.file.core.windows.net\MyFileShare3;AccountName=MyStorageAccountName AccountKey=<key>
```

A kapcsolati karakterlánccal használhatja a következő parancsot az Azure-fájlmegosztást csatlakoztatni.

```azurepowershell-interactive
net use <DesiredDriveLetter>: \\<MyStorageAccountName>.file.core.windows.net\<MyFileShareName> <AccountKey> /user:Azure\<MyStorageAccountName>
```

További információkért lásd: [csatlakoztatása egy Azure-fájlmegosztást, és a Windows a megosztás elérése][azmount].

Az Azure-fájlmegosztási könyvtárakat módon is elérheti:

```azurepowershell-interactive
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> cd .\MyFileShare1\
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files\MyFileShare1> dir

Mode  Name
----  ----
+     TestFolder
.     hello.ps1
```

### <a name="interact-with-virtual-machines"></a>Virtuális gépek kezelése

Az összes a virtuális gépek keresztül az aktuális előfizetésben található `VirtualMachines` könyvtár.

```azurepowershell-interactive
PS Azure:\MySubscriptionName\VirtualMachines> dir

    Directory: Azure:\MySubscriptionName\VirtualMachines


Name       ResourceGroupName  Location  VmSize          OsType              NIC ProvisioningState  PowerState
----       -----------------  --------  ------          ------              --- -----------------  ----------
TestVm1    MyResourceGroup1   westus    Standard_DS2_v2 Windows       my2008r213         Succeeded     stopped
TestVm2    MyResourceGroup1   westus    Standard_DS1_v2 Windows          jpstest         Succeeded deallocated
TestVm10   MyResourceGroup2   eastus    Standard_DS1_v2 Windows           mytest         Succeeded     running
```

#### <a name="invoke-powershell-script-across-remote-vms"></a>PowerShell-szkript meghívása a távoli virtuális gépek között

 > [!WARNING]
 > Tekintse meg [hibáinak elhárítása Azure virtuális gépek távoli felügyeleti](troubleshooting.md#troubleshooting-remote-management-of-azure-vms).

  Feltételezve, hogy egy virtuális Gépet, a MyVM1, használjuk `Invoke-AzureRmVMCommand` meghívásához PowerShell parancsprogram-blokkot a távoli számítógépen.

  ```azurepowershell-interactive
  Invoke-AzureRmVMCommand -Name MyVM1 -ResourceGroupName MyResourceGroup -Scriptblock {Get-ComputerInfo} -EnableRemoting
  ```

  Is keresse meg a virtuális gép könyvtárba, és futtassa `Invoke-AzureRmVMCommand` módon.

  ```azurepowershell-interactive
  PS Azure:\> cd MySubscriptionName\MyResourceGroup\Microsoft.Compute\virtualMachines
  PS Azure:\MySubscriptionName\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Invoke-AzureRmVMCommand -Scriptblock {Get-ComputerInfo}

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

#### <a name="interactively-log-on-to-a-remote-vm"></a>Tartott erőforrásokra jelentkezhet be egy távoli virtuális Gépen

Használhat `Enter-AzureRmVM` hozzá az Azure-ban futó virtuális gépre.

  ```azurepowershell-interactive
  PS Azure:\> Enter-AzureRmVM -Name MyVM1 -ResourceGroupName MyResourceGroup -EnableRemoting
  ```

Ön is elérheti a `VirtualMachines` directory első és a futtatási `Enter-AzureRmVM` módon

  ```azurepowershell-interactive
 PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Enter-AzureRmVM
 ```

### <a name="discover-webapps"></a>Fedezze fel a Web Apps

Írja be azokat a `WebApps` könyvtárban, egyszerűen kiválaszthatja a web apps-erőforrások

```azurepowershell-interactive
PS Azure:\MySubscriptionName> dir .\WebApps\

    Directory: Azure:\MySubscriptionName\WebApps

Name            State    ResourceGroup      EnabledHostNames                  Location
----            -----    -------------      ----------------                  --------
mywebapp1       Stopped  MyResourceGroup1   {mywebapp1.azurewebsites.net...   West US
mywebapp2       Running  MyResourceGroup2   {mywebapp2.azurewebsites.net...   West Europe
mywebapp3       Running  MyResourceGroup3   {mywebapp3.azurewebsites.net...   South Central US

# You can use Azure cmdlets to Start/Stop your web apps
PS Azure:\MySubscriptionName\WebApps> Start-AzureRmWebApp -Name mywebapp1 -ResourceGroupName MyResourceGroup1

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

[A Win32-OpenSSH](https://github.com/PowerShell/Win32-OpenSSH) érhető el a PowerShell Cloud Shellben.
A hitelesítést a kiszolgálók vagy virtuális gépek SSH-val, a Cloud Shellben hozzon létre a nyilvános-titkos kulcspárt, és közzéteszi a nyilvános kulcsot a `authorized_keys` a távoli számítógépen, mint például `/home/user/.ssh/authorized_keys`.

> [!NOTE]
> Létrehozhat SSH nyilvános és titkos kulcsok használatával `ssh-keygen` és közzéteheti őket `$env:USERPROFILE\.ssh` a Cloud Shellben.

### <a name="using-ssh"></a>SSH-val

Kövesse az utasításokat [Itt](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-powershell) hozhat létre egy új Virtuálisgép-konfigurációt AzureRM-parancsmagok használatával.
Mielőtt hívása `New-AzureRmVM` felhőplatformos termékeiért az üzembe helyezés, adja hozzá az SSH nyilvános kulcs a virtuális gép konfigurációjához.
Az újonnan létrehozott virtuális gép a nyilvános kulcsot fogja tartalmazni a `~\.ssh\authorized_keys` helyét, ezáltal a hitelesítőadat-mentes SSH-munkamenetből a virtuális géphez.

```azurepowershell-interactive
# Create VM config object - $vmConfig using instructions on linked page above

# Generate SSH keys in Cloud Shell
ssh-keygen -t rsa -b 2048 -f $HOME\.ssh\id_rsa 

# Ensure VM config is updated with SSH keys
$sshPublicKey = Get-Content "$HOME\.ssh\id_rsa.pub"
Add-AzureRmVMSshPublicKey -VM $vmConfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

# Create a virtual machine
New-AzureRmVM -ResourceGroupName <yourResourceGroup> -Location <vmLocation> -VM $vmConfig

# SSH to the VM
ssh azureuser@MyVM.Domain.Com
```

## <a name="list-available-commands"></a>Elérhető parancsok listája

A `Azure` meghajtó, írja be a `Get-AzureRmCommand` környezetfüggő Azure-parancsok beolvasásához.

Azt is megteheti, bármikor használhat `Get-Command *azurerm* -Module AzureRM.*` megtudhatja az elérhető Azure-parancsok.

## <a name="install-custom-modules"></a>Egyéni modulok telepítése

Futtathat `Install-Module` a modulok telepítéséhez a [PowerShell-galériából][gallery].

## <a name="get-help"></a>Get-Help

Típus `Get-Help` információt szeretne kapni PowerShell az Azure Cloud Shellben.

```azurepowershell-interactive
Get-Help
```

Egy adott parancs továbbra is teheti `Get-Help` parancsmag követ.

```azurepowershell-interactive
Get-Help Get-AzureRmVM
```

## <a name="use-azure-files-to-store-your-data"></a>Az Azure Files használatával tárolja az adatait

Is létrehozhat egy parancsfájlt, például: `helloworld.ps1`, és mentse azt a `clouddrive` vele shell-munkamenetek között.

```azurepowershell-interactive
cd $HOME\clouddrive
code .\helloworld.ps1
# Add the content, such as 'Hello World!'
.\helloworld.ps1
Hello World!
```

PowerShell használhatja a Cloud Shellben, amikor legközelebb a `helloworld.ps1` fájl lesz találhatók a `$HOME\clouddrive` címtárhoz, amely csatlakoztatja az Azure-fájlmegosztást.

## <a name="use-custom-profile"></a>Egyéni profil

Testre szabható a PowerShell-környezet létrehozása a PowerShell - a kívánt módon `profile.ps1` (vagy `Microsoft.PowerShell_profile.ps1`).
Mentse a `$profile.CurrentUserAllHosts` (vagy `$profile.CurrentUserAllHosts`), így betölthetők minden PowerShell Cloud Shell-munkamenetben.

Hogyan hozzon létre egy profilt, tekintse meg a [kapcsolatos profilok][profile].

## <a name="use-git"></a>A Git használatával

A Cloud shellben a Git-tárház klónozásához, szeretne létrehozni egy [személyes hozzáférési tokent] [ githubtoken] és a felhasználónév, az azt. Után a token, a Klónozás a tárházat a következő:

```azurepowershell-interactive
  git clone https://<your-access-token>@github.com/username/repo.git
```

## <a name="exit-the-shell"></a>Kilépés a rendszerhéjból

Típus `exit` a munkamenet befejezéséhez.

[bashqs]:quickstart.md
[gallery]:https://www.powershellgallery.com/
[customex]:https://docs.microsoft.com/azure/virtual-machines/windows/extensions-customscript
[profile]: https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/about/about_profiles
[azmount]: https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows
[githubtoken]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
