---
title: Az Azure Stack PowerShell telepítése |} A Microsoft Docs
description: Ismerje meg az Azure Stack PowerShell telepítése.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 12/21/2018
ms.author: sethm
ms.reviewer: thoroet
ms.openlocfilehash: fe64011991732c7493d8efd06516efc664b674a4
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/22/2018
ms.locfileid: "53752775"
---
# <a name="install-powershell-for-azure-stack"></a>Az Azure Stack PowerShell telepítése

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Dolgozunk a felhőben, telepítenie kell az Azure Stack-kompatibilis PowerShell-modulok. Keresztül szolgáltatás engedélyezve van a kompatibilitási *API profilok*.

API-profilok kezeléséhez az Azure és az Azure Stack közötti tartalmaznak. Egy API-verzióprofil egy Azure Resource Manager PowerShell-modulok az adott API-verziókat. Minden felhőalapú platform a támogatott API-verzióprofilok készletével rendelkezik. Például Azure Stack is támogatja a felülírás Profilverzió például **2018-03-01-hibrid**. Amikor telepít egy profilt, az Azure Resource Manager PowerShell-modulok, amelyek megfelelnek a megadott profil vannak telepítve.

Azure Stack kompatibilis PowerShell-modulok az internethez csatlakoztatott, részben csatlakoztatva vagy leválasztott forgatókönyvek is telepítheti. Ez a cikk végigvezeti a részletes utasításokat az Azure Stack PowerShell telepítése az ilyen feladatokhoz szükséges.

## <a name="1-verify-your-prerequisites"></a>1. Az Előfeltételek ellenőrzése

Mielőtt elkezdené, az Azure Stack és a PowerShell, a következő előfeltételeknek kell rendelkeznie:

- **5.0-s verzió PowerShell** a verzió ellenőrzéséhez futtassa **$PSVersionTable.PSVersion** és hasonlítsa össze a **fő** verzió. Ha nem rendelkezik a PowerShell 5.0, kövesse a [Windows PowerShell telepítése](https://docs.microsoft.com/powershell/scripting/setup/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell).

  > [!Note]
  > PowerShell 5.0 szükséges egy Windows-gépen.

- **Egy rendszergazda jogú parancssorban futtassa a Powershellt**.
  PowerShell rendszergazdai jogosultsággal kell futtatni.

- **PowerShell-galériából hozzáférés** hozzá kell férnie a [PowerShell-galériából](https://www.powershellgallery.com). A katalógus a PowerShell-tartalmak központi adattára. A **PowerShellGet** modul felderítése, telepítése, frissítése és PowerShell-összetevőket, például a modulok, DSC-erőforrások, szerepkörrel képességeket és szkripteket a PowerShell-galériában és egyéb titkos közzétételéhez szükséges parancsmagokat tartalmazza tárházak. PowerShell használatakor a leválasztott forgatókönyvek esetében kell kérnie a erőforrások kapcsolat rendelkező számítógépről az interneten és tárolja őket egy helyen érhető el kapcsolat nélküli számítógépre.


## <a name="2-validate-the-powershell-gallery-accessibility"></a>2. A PowerShell-galériából kisegítő ellenőrzése

Ha egy adattár, regisztrálva van a PSGallery ellenőrzése.

> [!Note]
> Ezt a lépést Internet-hozzáférést igényel.

Nyisson meg egy rendszergazda jogú PowerShell-parancssort, és futtassa a következő parancsmagokat:

```PowerShell
Import-Module -Name PowerShellGet -ErrorAction Stop
Import-Module -Name PackageManagement -ErrorAction Stop
Get-PSRepository -Name "PSGallery"
```

Ha a tárház nincs regisztrálva, nyisson meg egy rendszergazda jogú PowerShell-munkamenetet, és futtassa a következő parancsot:

```PowerShell
Register-PsRepository -Default
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

## <a name="3-uninstall-existing-versions-of-the-azure-stack-powershell-modules"></a>3. Az Azure Stack PowerShell-modulok meglévő verzióinak eltávolítása

A szükséges verzió telepítése előtt győződjön meg arról, hogy távolítsa el minden korábban telepített Azure Stack AzureRM PowerShell-modulok. Az alábbi két módszer egyikével eltávolíthatja őket:

1. Távolítsa el a meglévő AzureRM PowerShell-modulok, zárja be az összes aktív PowerShell-munkamenetet, és futtassa a következő parancsmagokat:

    ```PowerShell
    Get-Module -Name Azs.* -ListAvailable | Uninstall-Module -Force -Verbose
    Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose
    ```
    Ha hibát észlel például "a modul már használatban van", zárja be a PowerShell-munkamenetekben, amelyek a modulokat használ, és futtassa újra a fenti szkript.

2. Indítsa el az összes mappa törlése `Azure` vagy `Azs.` a a `C:\Program Files\WindowsPowerShell\Modules` és `C:\Users\{yourusername}\Documents\WindowsPowerShell\Modules` mappákat. Ezek a mappák törlése eltávolítja a bármely meglévő PowerShell-modulok.

## <a name="4-connected-install-powershell-for-azure-stack-with-internet-connectivity"></a>4. Csatlakoztatva: Az Azure Stack PowerShell telepítése az internetkapcsolattal rendelkező

Az Azure Stack igényel a **2018-03-01-hibrid** API-verzióprofil 1808 Azure Stack-verzió. A profil telepítésével érhető el a **AzureRM.Bootstrapper** modul. Emellett az AzureRM-modulok kell is telepíti az Azure Stack-specifikus PowerShell-modulok. Az API-verzióprofil és van szüksége az Azure Stack PowerShell-modulok függ az Azure Stack verzióját a rendszer futó.

Futtassa a következő PowerShell-parancsfájl ezeket a modulokat a fejlesztői munkaállomáson telepítendő:

- Az Azure Stack 1811 vagy újabb verziója.

    ```PowerShell
    # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet
    Install-Module -Name AzureRm.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force

    Install-Module -Name AzureStack -RequiredVersion 1.6.0
    ```

    Szeretné, hogy a további tárolási funkciók (csatlakoztatott szakaszban említett), használja a letöltési és a következő csomagok telepítéséhez.

    ```PowerShell
    # Install the Azure.Storage module version 4.5.0
    Install-Module -Name Azure.Storage -RequiredVersion 4.5.0 -Force -AllowClobber

    # Install the AzureRm.Storage module version 5.0.4
    Install-Module -Name AzureRM.Storage -RequiredVersion 5.0.4 -Force -AllowClobber

    # Remove incompatible storage module installed by AzureRM.Storage
    Uninstall-Module Azure.Storage -RequiredVersion 4.6.1 -Force

    # Load the modules explicitly specifying the versions
    Import-Module -Name Azure.Storage -RequiredVersion 4.5.0
    Import-Module -Name AzureRM.Storage -RequiredVersion 5.0.4
    ```

> [!Note]
> Frissítése az Azure PowerShell-lel a **2017-03-09-profile** való **2018-03-01-hibrid**, tekintse át a [áttelepítési útmutató](https://github.com/azure/azure-powershell/blob/AzureRM/documentation/migration-guides/Stack/migration-guide.2.3.0.md).


- Az Azure Stack 1808 vagy újabb verziója.

    ```PowerShell
    # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet
    Install-Module -Name AzureRm.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force

    Install-Module -Name AzureStack -RequiredVersion 1.5.0
    ```

- Az Azure Stack 1807 vagy valamely korábbi rendszerrel.

  ```PowerShell
  Install-Module -Name AzureRm.BootStrapper
  Use-AzureRmProfile -Profile 2017-03-09-profile -Force
  Install-Module -Name AzureStack -RequiredVersion 1.4.0
  ```

Erősítse meg a telepítést a következő parancs futtatásával:

```PowerShell
Get-Module -Name "Azure*" -ListAvailable
Get-Module -Name "Azs*" -ListAvailable
```

Ha a telepítés sikeres, a kimenetben az AzureRM- és AzureStack modulok jelennek meg.

## <a name="5-disconnected-install-powershell-without-an-internet-connection"></a>5. Megszakadt: Telepítse a PowerShell internetkapcsolat nélkül

Leválasztott forgatókönyvekben először töltse le a PowerShell-modulok olyan gépre, amely rendelkezik internetkapcsolattal, és utána az Azure Stack Development Kit telepítéséhez.

Jelentkezzen be a számítógépre az internetkapcsolattal rendelkező, és az Azure Resource Manager és az AzureStack-csomagok letöltése, Azure Stack verziójától függően az alábbi parancsfájlok használata:

  - Az Azure Stack 1811 vagy újabb verziója.

    ```PowerShell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.6.0
    ```

    Szeretné, hogy a további tárolási funkciók (csatlakoztatott szakaszban említett), használja a letöltési és a következő csomagok telepítéséhez.

    ```PowerShell
    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name Azure.Storage -Path $Path -Force -RequiredVersion 4.5.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRm.Storage -Path $Path -Force -RequiredVersion 5.0.4
    ```

  - Az Azure Stack 1808 vagy újabb verziója.

    ```PowerShell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.5.0
    ```

  - Az Azure Stack 1807 vagy valamely korábbi rendszerrel.

    > [!Note]
    Frissítse a 1.2.11 verzióját, lásd: a [áttelepítési útmutató](https://aka.ms/azspowershellmigration).

    ```PowerShell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 1.2.11
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.4.0
    ```

2. Másolja a letöltött csomagokat egy USB-eszközt.

3. Jelentkezzen be a munkaállomás, és másolja a csomagokat az USB-eszközt egy olyan helyre a munkaállomáson.

4. Most már ezen a helyen az alapértelmezett tárház regisztrálja, és a tárházból az AzureRM- és az AzureStack modulok telepítéséhez:

   ```PowerShell
   #requires -Version 5
   #requires -RunAsAdministrator
   #requires -Module PowerShellGet
   #requires -Module PackageManagement

   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation  -InstallationPolicy Trusted

   Install-Module -Name AzureRM -Repository $RepoName

   Install-Module -Name AzureStack -Repository $RepoName
   ```

## <a name="6-configure-powershell-to-use-a-proxy-server"></a>6. Proxykiszolgáló használata a PowerShell konfigurálása

Az Internet eléréséhez proxykiszolgáló használatát igénylő forgatókönyvekben először konfigurálnia kell a PowerShell használata a meglévő proxykiszolgáló:

1. Nyisson meg egy rendszergazda jogú PowerShell-parancssorban.
2. Futtassa az alábbi parancsot:

   ```PowerShell
   #To use Windows credentials for proxy authentication
   [System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials

   #Alternatively, to prompt for separate credentials that can be used for #proxy authentication
   [System.Net.WebRequest]::DefaultWebProxy.Credentials = Get-Credential
   ```

## <a name="next-steps"></a>További lépések

 - [Azure Stack-eszközök letöltése a githubról](azure-stack-powershell-download.md)
 - [Az Azure Stack felhasználói PowerShell-környezet konfigurálása](user/azure-stack-powershell-configure-user.md)
 - [Az Azure Stack-operátorokról PowerShell-környezet konfigurálása](azure-stack-powershell-configure-admin.md)
 - [Az Azure Stackben API-verzióprofilok kezelése](user/azure-stack-version-profiles.md)