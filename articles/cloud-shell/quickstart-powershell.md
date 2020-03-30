---
title: Az Azure Cloud Shell rövid útmutatója – PowerShell
description: Ismerje meg, hogyan használhatja a PowerShellt a böngészőjében az Azure Cloud Shell segítségével.
author: maertendmsft
ms.author: damaerte
tags: azure-resource-manager
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 10/18/2018
ms.openlocfilehash: 72261989b7cee9d2251eb18b36431ec807b0e874
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273006"
---
# <a name="quickstart-for-powershell-in-azure-cloud-shell"></a>Rövid útmutató a PowerShellhez az Azure Cloud Shellben

Ez a dokumentum részletezi, hogyan használhatja a PowerShellt a Cloud Shellben az [Azure Portalon.](https://portal.azure.com/)

> [!NOTE]
> A [Bash az Azure Cloud Shell](quickstart.md) rövid útmutató is elérhető.

## <a name="start-cloud-shell"></a>Cloud Shell indítása

1. Kattintson a **Cloud Shell** gombra az Azure Portal felső navigációs sávján

   ![](media/quickstart-powershell/shell-icon.png)

2. Válassza ki a PowerShell-környezetet a legördülő menüből, és az Azure Drive-on lesz`(Azure:)`

   ![](media/quickstart-powershell/environment-ps.png)

## <a name="run-powershell-commands"></a>PowerShell-parancsok futtatása

Futtassa a normál PowerShell-parancsokat a Cloud Shellben, például:

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

## <a name="navigate-azure-resources"></a>Navigálás az Azure-erőforrásokban

 1. Az összes előfizetés `Azure` listázása a drive-ról

    ```azurepowershell-interactive
    PS Azure:\> dir
    ```

 2. `cd`a kívánt előfizetéshez

    ```azurepowershell-interactive
    PS Azure:\> cd MySubscriptionName
    PS Azure:\MySubscriptionName>
    ```

 3. Az összes Azure-erőforrás megtekintése az aktuális előfizetésben

    Írja `dir` be az Azure-erőforrások több nézetének listázásához.

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

Írja `dir` `AllResources` be a könyvtár alá az Azure-erőforrások megtekintéséhez.

```azurepowershell-interactive
PS Azure:\MySubscriptionName> dir AllResources
```

### <a name="explore-resource-groups"></a>Erőforráscsoportok felfedezése

 A címtárban, `ResourceGroups` és egy adott erőforráscsoporton belül virtuális gépek találhatók.

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
> Előfordulhat, hogy a második gépeléskor `dir`a Cloud Shell sokkal gyorsabban tudja megjeleníteni az elemeket.
> Ennek az az oka, hogy a gyermekelemek a jobb felhasználói élmény érdekében a memóriában vannak gyorsítótárazva.
Azonban mindig használhatja `dir -Force` a friss adatok beszerezhető.

### <a name="navigate-storage-resources"></a>Navigálás a tárolási erőforrásokban

A `StorageAccounts` könyvtárba való belépéssel könnyedén navigálhat az összes tárolási erőforrásban

```azurepowershell-interactive
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> dir

    Directory: Azure:\MySubscriptionNameStorageAccounts\MyStorageAccountName\Files

Name          ConnectionString
----          ----------------
MyFileShare1  \\MyStorageAccountName.file.core.windows.net\MyFileShare1;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare2  \\MyStorageAccountName.file.core.windows.net\MyFileShare2;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare3  \\MyStorageAccountName.file.core.windows.net\MyFileShare3;AccountName=MyStorageAccountName AccountKey=<key>
```

A kapcsolati karakterlánc, a következő paranccsal csatlakoztathatja az Azure Files share.

```azurepowershell-interactive
net use <DesiredDriveLetter>: \\<MyStorageAccountName>.file.core.windows.net\<MyFileShareName> <AccountKey> /user:Azure\<MyStorageAccountName>
```

További információt az [Azure-fájlok megosztásának csatlakoztatása és a Windows-megosztás elérése című témakörben talál.][azmount]

Az Azure Files-megosztás alatt a könyvtárakban az alábbiak szerint navigálhat:

```azurepowershell-interactive
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> cd .\MyFileShare1\
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files\MyFileShare1> dir

Mode  Name
----  ----
+     TestFolder
.     hello.ps1
```

### <a name="interact-with-virtual-machines"></a>Interakció a virtuális gépekkel

Az összes virtuális gépet az aktuális `VirtualMachines` előfizetés könyvtáron keresztül találja.

```azurepowershell-interactive
PS Azure:\MySubscriptionName\VirtualMachines> dir

    Directory: Azure:\MySubscriptionName\VirtualMachines


Name       ResourceGroupName  Location  VmSize          OsType              NIC ProvisioningState  PowerState
----       -----------------  --------  ------          ------              --- -----------------  ----------
TestVm1    MyResourceGroup1   westus    Standard_DS2_v2 Windows       my2008r213         Succeeded     stopped
TestVm2    MyResourceGroup1   westus    Standard_DS1_v2 Windows          jpstest         Succeeded deallocated
TestVm10   MyResourceGroup2   eastus    Standard_DS1_v2 Windows           mytest         Succeeded     running
```

#### <a name="invoke-powershell-script-across-remote-vms"></a>PowerShell-parancsfájl meghívása távoli virtuális gépeken

 > [!WARNING]
 > Tekintse meg az [Azure virtuális gépek távoli kezelésének hibaelhárítása című témakört.](troubleshooting.md#troubleshooting-remote-management-of-azure-vms)

  Feltételezve, hogy egy virtuális gép, MyVM1, használjuk `Invoke-AzVMCommand` meghívni a PowerShell-parancsfájl blokk a távoli gépen.

  ```azurepowershell-interactive
  Enable-AzVMPSRemoting -Name MyVM1 -ResourceGroupname MyResourceGroup
  Invoke-AzVMCommand -Name MyVM1 -ResourceGroupName MyResourceGroup -Scriptblock {Get-ComputerInfo} -Credential (Get-Credential)
  ```

  Azt is keresse meg a VirtualMachines `Invoke-AzVMCommand` könyvtárat először, és fuss az alábbiak szerint.

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

#### <a name="interactively-log-on-to-a-remote-vm"></a>Interaktív bejelentkezés távoli virtuális gépre

Interaktívan `Enter-AzVM` bejelentkezhet az Azure-ban futó virtuális gépbe.

  ```azurepowershell-interactive
  PS Azure:\> Enter-AzVM -Name MyVM1 -ResourceGroupName MyResourceGroup -Credential (Get-Credential)
  ```

Először a könyvtárhoz is navigálhat, és a `VirtualMachines` következőképpen futtatható: `Enter-AzVM`

  ```azurepowershell-interactive
 PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Enter-AzVM -Credential (Get-Credential)
 ```

### <a name="discover-webapps"></a>WebApps felfedezése

A `WebApps` címtárba való belépéssel könnyedén navigálhat a webalkalmazások erőforrásaiközött

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

Ha SSH-t használó kiszolgálókon vagy virtuális gépeken szeretne hitelesíteni magát, `authorized_keys` hozza létre a `/home/user/.ssh/authorized_keys`nyilvános és titkos kulcspárt a Cloud Shellben, és tegye közzé a nyilvános kulcsot a távoli számítógépen, például .

> [!NOTE]
> SSH nyilvános kulcsokat hozhat `ssh-keygen` létre a `$env:USERPROFILE\.ssh` Cloud Shell használatával, és közzéteheti őket.

### <a name="using-ssh"></a>Az SSH használata

Kövesse az [itt](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-powershell) található utasításokat egy új virtuálisgép-konfiguráció létrehozásához az Azure PowerShell-parancsmagok használatával.
`New-AzVM` Az üzembe helyezés megkezdése előtt adja hozzá az SSH nyilvános kulcsot a virtuális gép konfigurációjához.
Az újonnan létrehozott virtuális gép tartalmazza `~\.ssh\authorized_keys` a nyilvános kulcsot a helyen, ezáltal lehetővé téve a hitelesítő adatok nélküli SSH-munkamenetet a virtuális gépszámára.

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

## <a name="list-available-commands"></a>Az elérhető parancsok listázása

A `Azure` meghajtó `Get-AzCommand` alatt írja be a környezetspecifikus Azure-parancsok beírásához.

Azt is megteheti, `Get-Command *az* -Module Az.*` hogy mindig a rendelkezésre álló Azure-parancsok at.

## <a name="install-custom-modules"></a>Egyéni modulok telepítése

Futtathatja `Install-Module` a modulok telepítéséhez a [PowerShell-galériából.][gallery]

## <a name="get-help"></a>Segítség

Írja `Get-Help` be a PowerShell az Azure Cloud Shell ben.

```azurepowershell-interactive
Get-Help
```

Egy adott parancs esetén továbbra `Get-Help` is megteheti egy parancsmag követését.

```azurepowershell-interactive
Get-Help Get-AzVM
```

## <a name="use-azure-files-to-store-your-data"></a>Adatok tárolása azure-fájlok használatával

Létrehozhat egy parancsfájlt, `helloworld.ps1`például , `clouddrive` és mentheti azt a shell munkamenetek közötti használatra.

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

Legközelebb, amikor a PowerShellt használja `helloworld.ps1` a Cloud `$HOME\clouddrive` Shellben, a fájl az Azure Files-megosztást csatlakoztató könyvtár alatt fog létezni.

## <a name="use-custom-profile"></a>Egyéni profil használata

Testreszabhatja a PowerShell-környezetet, a PowerShell-profil(ok) - `profile.ps1` (vagy) `Microsoft.PowerShell_profile.ps1`létrehozásával.
Mentse el `$profile.CurrentUserAllHosts` a `$profile.CurrentUserAllHosts`(vagy ), hogy a Cloud Shell-munkamenet minden PowerShell-munkamenetében betölthető legyen.

A profil létrehozásáról a Profilok – ismertetés című lap ban [tájékozódhat.][profile]

## <a name="use-git"></a>A Git használata

Git-tárház klónozásához a Felhőrendszerhéjban létre kell hoznia egy [személyes hozzáférési jogkivonatot,][githubtoken] és felhasználónévként kell használnia. Miután rendelkezik a jogkivonatkal, klónozza a tárházat az alábbiak szerint:

```azurepowershell-interactive
  git clone https://<your-access-token>@github.com/username/repo.git
```

## <a name="exit-the-shell"></a>Kilépés a rendszerhéjból

Írja `exit` be a munkamenet leállítását.

[bashqs]:quickstart.md
[gallery]:https://www.powershellgallery.com/
[customex]:https://docs.microsoft.com/azure/virtual-machines/windows/extensions-customscript
[profile]: https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/about/about_profiles
[azmount]: https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows
[githubtoken]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
