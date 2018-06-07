---
title: Az Azure-felhőbe rendszerhéj (előzetes verzió) gyors üzembe helyezés PowerShell |} Microsoft Docs
description: A felhő rendszerhéj PowerShell gyors üzembe helyezés
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
ms.date: 01/19/2018
ms.author: damaerte
ms.openlocfilehash: a85d718d1c524a240f4b59b4db5004595fe1902f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34608610"
---
# <a name="quickstart-for-powershell-in-azure-cloud-shell-preview"></a>Gyors üzembe helyezés PowerShell Azure-felhőbe rendszerhéj (előzetes verzió)

Ez a dokumentum részletesen a PowerShell használatával a felhő rendszerhéj a [Azure-portálon](https://aka.ms/PSCloudPreview).

> [!NOTE]
> A [Azure Cloud rendszerhéj Bash](quickstart.md) gyors üzembe helyezés akkor is elérhető.

## <a name="start-cloud-shell"></a>Indítsa el a felhő rendszerhéj

1. Kattintson a **felhő rendszerhéj** gomb az Azure portál a felső navigációs sáv

  ![](media/quickstart-powershell/shell-icon.png)

2. A legördülő listán válassza ki a PowerShell-környezet és az Azure meghajtóban fogja `(Azure:)`

  ![](media/quickstart-powershell/environment-ps.png)

## <a name="run-powershell-commands"></a>PowerShell-parancsok

Rendszeres PowerShell-parancsok futtatásával a felhő rendszerhéj, például:

```PowerShell
PS Azure:\> Get-Date
Monday, September 25, 2017 08:55:09 AM

PS Azure:\> Get-AzureRmVM -Status

ResourceGroupName       Name       Location                VmSize   OsType     ProvisioningState  PowerState
-----------------       ----       --------                ------   ------     -----------------  ----------
MyResourceGroup2        Demo        westus         Standard_DS1_v2  Windows    Succeeded           running
MyResourceGroup         MyVM1       eastus            Standard_DS1  Windows    Succeeded           running
MyResourceGroup         MyVM2       eastus   Standard_DS2_v2_Promo  Windows    Succeeded           deallocated
```

## <a name="navigate-azure-resources"></a>Keresse meg az Azure-erőforrások

 1. Az előfizetések felsorolása

    ``` PowerShell
    PS Azure:\> dir
    ```

 2. `cd` az előnyben részesített előfizetéséhez

    ``` PowerShell
    PS Azure:\> cd MySubscriptionName
    PS Azure:\MySubscriptionName>
    ```

 3. Az Azure erőforrások a jelenlegi előfizetéséhez tartozó megtekintése
 
    Típus `dir` több nézetében az Azure-erőforrások listáját.
 
    ``` PowerShell
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
Típus `dir` alatt `AllResources` címtár az Azure-erőforrások megtekintése.
    
    PS Azure:\MySubscriptionName> dir AllResources

### <a name="explore-resource-groups"></a>Erőforráscsoportok felfedezés

 Nyissa meg a `ResourceGroups` könyvtárhoz, és egy adott erőforráscsoportban található virtuális gépek.

``` PowerShell
PS Azure:\MySubscriptionName> cd ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines

PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines> dir


    Directory: Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines


VMName    Location   ProvisioningState VMSize          OS            SKU             OSVersion AdminUserName  NetworkInterfaceName
------    --------   ----------------- ------          --            ---             --------- -------------  --------------------
TestVm1   westus     Succeeded         Standard_DS2_v2 WindowsServer 2016-Datacenter Latest    AdminUser      demo371
TestVm2   westus     Succeeded         Standard_DS1_v2 WindowsServer 2016-Datacenter Latest    AdminUser      demo271

```
> [!NOTE]
> Előfordulhat, hogy a második alkalommal, amikor beírja `dir`, a felhő rendszerhéj alkalmas sokkal gyorsabb elemek megjelenítése céljából.
> Ennek az az oka a gyermekincidenseket a memóriában jobb felhasználói élményt gyorsítótárba kerüljenek-e.
Azonban mindig használhatja `dir -Force` friss adatok eléréséhez.

### <a name="navigate-storage-resources"></a>Keresse meg a tárolási erőforrások
    
Írja be azokat a `StorageAccounts` egyszerűen nyissa meg a tárolási erőforrások könyvtár
    
``` PowerShell 
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> dir

    Directory: Azure:\MySubscriptionNameStorageAccounts\MyStorageAccountName\Files


Name          ConnectionString
----          ----------------
MyFileShare1  \\MyStorageAccountName.file.core.windows.net\MyFileShare1;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare2  \\MyStorageAccountName.file.core.windows.net\MyFileShare2;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare3  \\MyStorageAccountName.file.core.windows.net\MyFileShare3;AccountName=MyStorageAccountName AccountKey=<key>


```

A megadott kapcsolati karakterlánccal a következő parancs segítségével a Azure fájlok megosztás csatlakoztatásához.
        
``` PowerShell
net use <DesiredDriveLetter>: \\<MyStorageAccountName>.file.core.windows.net\<MyFileShareName> <AccountKey> /user:Azure\<MyStorageAccountName>


```

További információkért lásd: [egy Azure fájlok megosztás csatlakoztathatja és érheti el a Windows a megosztás][azmount].

Az alábbiak szerint is megtalálhatja a könyvtárakat Azure fájlok:

            
``` PowerShell
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> cd .\MyFileShare1\
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files\MyFileShare1> dir

Mode  Name
----  ----
+     TestFolder
.     hello.ps1

    
```

### <a name="interact-with-virtual-machines"></a>Virtuális gépek kommunikál

Az összes a virtuális gép az aktuális előfizetésben keresztül található `VirtualMachines` könyvtár.
    
``` PowerShell
PS Azure:\MySubscriptionName\VirtualMachines> dir

    Directory: Azure:\MySubscriptionName\VirtualMachines


Name       ResourceGroupName  Location  VmSize          OsType              NIC ProvisioningState  PowerState
----       -----------------  --------  ------          ------              --- -----------------  ----------
TestVm1    MyResourceGroup1   westus    Standard_DS2_v2 Windows       my2008r213         Succeeded     stopped
TestVm2    MyResourceGroup1   westus    Standard_DS1_v2 Windows          jpstest         Succeeded deallocated
TestVm10   MyResourceGroup2   eastus    Standard_DS1_v2 Windows           mytest         Succeeded     running


```

#### <a name="invoke-powershell-script-across-remote-vms"></a>PowerShell parancsfájl meghívása távoli virtuális gépek között

 > [!WARNING]
 > Tekintse meg [hibaelhárítása az Azure virtuális gépek távoli felügyeleti](troubleshooting.md#troubleshooting-remote-management-of-azure-vms).

  Feltéve, hogy egy virtuális MyVM1, most használja `Invoke-AzureRmVMCommand` meghívni a PowerShell parancsprogram-blokkot tartalmazzon a távoli számítógépen.

  ``` Powershell
  Invoke-AzureRmVMCommand -Name MyVM1 -ResourceGroupName MyResourceGroup -Scriptblock {Get-ComputerInfo} -EnableRemoting
  ```
  Is keresse meg a virtuális gép könyvtárba, és futtassa `Invoke-AzureRmVMCommand` az alábbiak szerint.

  ``` PowerShell
  PS Azure:\> cd MySubscriptionName\MyResourceGroup\Microsoft.Compute\virtualMachines
  PS Azure:\MySubscriptionName\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Invoke-AzureRmVMCommand -Scriptblock {Get-ComputerInfo}
  ```
  A kimenet az alábbihoz hasonló jelenik meg:

  ``` PowerShell
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

#### <a name="interactively-log-on-to-a-remote-vm"></a>Interaktív bejelentkezést egy távoli virtuális géphez

Használhat `Enter-AzureRmVM` Azure-beli virtuális gép interaktívan bejelentkezni.

  ``` PowerShell
  Enter-AzureRmVM -Name MyVM1 -ResourceGroupName MyResourceGroup -EnableRemoting
  ```

Akkor is megtalálhatja a `VirtualMachines` directory első és futtatási `Enter-AzureRmVM` az alábbiak szerint

  ``` PowerShell
 PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Enter-AzureRmVM
 ```

### <a name="discover-webapps"></a>Webalkalmazás felderítése

Írja be azokat a `WebApps` egyszerűen nyissa meg a webes alkalmazások erőforrások könyvtár

``` PowerShell
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

[A Win32-OpenSSH](https://github.com/PowerShell/Win32-OpenSSH) PowerShell felhő rendszerhéj érhető el.
Hitelesítés kiszolgálók vagy az SSH használatával virtuális gépek, a nyilvános-titkos kulcsból álló kulcspárt felhő rendszerhéj létrehozni, és közzéteszi a nyilvános kulcsot a `authorized_keys` a távoli számítógépen, mint például `/home/user/.ssh/authorized_keys`.

> [!NOTE]
> SSH nyilvános és titkos kulcsok használatával hozhat létre `ssh-keygen` és közzétenni a `$env:USERPROFILE\.ssh` felhő rendszerhéjban.

### <a name="using-a-custom-profile-to-persist-git-and-ssh-settings"></a>Egy egyéni profilt használ a GIT és az SSH-beállítások megtartására

Mivel munkamenetek nem maradnak kijelentkezési, mentse a `$env:USERPROFILE\.ssh` könyvtárból `clouddrive` , vagy hozzon létre egy symlink felhő rendszerhéj lekérdezi indításakor.
Adja hozzá a következő kódrészletet a profile.ps1 egy symlink történő létrehozásához a `clouddrive`.

``` PowerShell
# Check if the .ssh directory exists
if( -not (Test-Path $home\clouddrive\.ssh)){
    mkdir $home\clouddrive\.ssh
}

# .ssh path relative to this script
$script:sshFolderPath = Join-Path $PSScriptRoot .ssh

# Create a symlink to .ssh in user's $home
if(Test-Path $script:sshFolderPath){
   if(-not (Test-Path (Join-Path $HOME .ssh ))){
        New-Item -ItemType SymbolicLink -Path $HOME -Name .ssh -Value $script:sshFolderPath
   }
}

```

### <a name="using-ssh"></a>SSH használatával

Kövesse az utasításokat [Itt](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-powershell) AzureRM-parancsmagok használatával új Virtuálisgép-konfiguráció létrehozásához.
Mielőtt hívja be `New-AzureRmVM` indító a központi telepítést, hogy adja hozzá az SSH nyilvános kulcsát a Virtuálisgép-konfigurációhoz.
Az újonnan létrehozott virtuális gép a nyilvános kulcsot fogja tartalmazni az `~\.ssh\authorized_keys` helyét, ezáltal a hitelesítőadat-mentes SSH-munkamenetet a virtuális géphez.

``` PowerShell

# Create VM config object - $vmConfig using instructions on linked page above

# Generate SSH keys in Cloud Shell
ssh-keygen -t rsa -b 2048 -f $HOME\.ssh\id_rsa 

# Ensure VM config is updated with SSH keys
$sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
Add-AzureRmVMSshPublicKey -VM $vmConfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

# Create a virtual machine
New-AzureRmVM -ResourceGroupName <yourResourceGroup> -Location <vmLocation> -VM $vmConfig

# SSH to the VM
ssh azureuser@MyVM.Domain.Com

```


## <a name="list-available-commands"></a>Rendelkezésre álló parancslista

A `Azure` meghajtó, írja be a `Get-AzureRmCommand` környezetfüggő Azure parancsok eléréséhez.

Azt is megteheti, hogy mindig használhatja `Get-Command *azurerm* -Module AzureRM.*` elérhető Azure parancsok megállapítása.

## <a name="install-custom-modules"></a>Az egyéni modulok telepítése

Futtathat `Install-Module` modulok telepítése a [PowerShell-galériában][gallery].

## <a name="get-help"></a>Get-Help

Típus `Get-Help` Azure Cloud rendszerhéj PowerShell lekérése.

``` PowerShell
PS Azure:\> Get-Help
```

Egy adott parancs továbbra is mindent `Get-Help` parancsmag követ.

``` PowerShell
PS Azure:\> Get-Help Get-AzureRmVM
```

## <a name="use-azure-files-to-store-your-data"></a>Azure-fájlok használatával tárolja az adatait

Létrehozhat egy parancsfájlt, mondja ki `helloworld.ps1`, és menti a `clouddrive` rendszerhéj-munkamenetek között használandó.

``` PowerShell
cd C:\users\ContainerAdministrator\clouddrive
PS C:\users\ContainerAdministrator\clouddrive> vim .\helloworld.ps1
# Add the content, such as 'Hello World!'
PS C:\users\ContainerAdministrator\clouddrive> .\helloworld.ps1
Hello World!
```

Használatakor PowerShell felhő rendszerhéj, amikor legközelebb a `helloworld.ps1` fájl alapján lesznek megtalálhatók, a `clouddrive` címtárhoz, amely csatlakoztatja a Azure fájlok megosztáshoz.

## <a name="use-custom-profile"></a>Egyéni profil használata

Testre szabhatja a PowerShell-környezet létrehozása a PowerShell - profil `profile.ps1` vagy `Microsoft.PowerShell_profile.ps1`. E menteni a `clouddrive` , hogy minden PowerShell-munkamenetben, ha indítsa el a felhő rendszerhéj betölteni.

Arról, hogyan hozza létre a profilt, hogy [kapcsolatos profilok][profile].

## <a name="use-git"></a>A Git

A felhő rendszerhéj egy Git-tárház klónozása, szeretne létrehozni egy [személyes hozzáférési jogkivonat] [ githubtoken] , és használja azt a felhasználónevet. Ha már van a token, klónozás a tárházban az alábbiak szerint:

 ``` PowerShell
  git clone https://<your-access-token>@github.com/username/repo.git

```
Felhő rendszerhéj munkamenetek nem maradnak, ha kijelentkezik, vagy a munkamenet lejárata, mivel a Git-konfigurációs fájl nem létezik a következő bejelentkezéskor. Ahhoz, hogy a Git-config továbbra is fennáll, mentenie kell a .gitconfig, hogy a `clouddrive` , és másolja, vagy hozzon létre egy symlink, a felhő rendszerhéj lekérdezi indításakor. A profile.ps1, a következő kódrészletet segítségével hozzon létre egy symlink való `clouddrive`.

 ``` PowerShell
 
# .gitconfig path relative to this script
$script:gitconfigPath = Join-Path $PSScriptRoot .gitconfig

# Create a symlink to .gitconfig in user's $home
if(Test-Path $script:gitconfigPath){

    if(-not (Test-Path (Join-Path $home .gitconfig ))){
         New-Item -ItemType SymbolicLink -Path $home -Name .gitconfig -Value $script:gitconfigPath
    }
}

```
## <a name="exit-the-shell"></a>Kilépés a rendszerhéjból

Típus `exit` a munkamenet befejezéséhez.

[bashqs]:quickstart.md
[gallery]:https://www.powershellgallery.com/
[customex]:https://docs.microsoft.com/azure/virtual-machines/windows/extensions-customscript
[profile]: https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/about/about_profiles
[azmount]: https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows
[githubtoken]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
