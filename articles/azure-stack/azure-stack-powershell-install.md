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
ms.date: 08/10/2018
ms.author: sethm
ms.reviewer: thoroet
ms.openlocfilehash: 2619f959dbefba84ea1a4d5aa974055998b78b5a
ms.sourcegitcommit: 974c478174f14f8e4361a1af6656e9362a30f515
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2018
ms.locfileid: "42056892"
---
# <a name="install-powershell-for-azure-stack"></a>Az Azure Stack PowerShell telepítése

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

Azure Stack kompatibilis PowerShell-modulok a felhővel való működéséhez telepíteni kell. Keresztül szolgáltatás engedélyezve van a kompatibilitási *API profilok*.

API-profilok kezeléséhez az Azure és az Azure Stack közötti tartalmaznak. Egy API-verzióprofil egy Azure Resource Manager PowerShell-modulok az adott API-verziókat. Minden felhőalapú platform a támogatott API-verzióprofilok készletével rendelkezik. Például Azure Stack is támogatja a felülírás Profilverzió például **2017-03-09-profile**, és az Azure támogatja a **legújabb** API-verzióprofil. Amikor telepít egy profilt, az Azure Resource Manager PowerShell-modulok, amelyek megfelelnek a megadott profil vannak telepítve.

Azure Stack-kompatibilis PowerShell-modulok is telepítheti, az internethez csatlakoztatott, részben csatlakoztatott vagy leválasztott forgatókönyvek esetében. Ez a cikk részletesen a részletes utasításokat az Azure Stack PowerShell telepítése az ilyen feladatokhoz szükséges.

## <a name="1-verify-your-prerequisites"></a>1. Az Előfeltételek ellenőrzése

A get előtt el az Azure Stack és a PowerShell, kell rendelkezniük kell néhány követelménynek.

- **PowerShell 5.0-s verzió**  
A verzió ellenőrzéséhez futtassa a $PSVersionTable.PSVersion, és hasonlítsa össze a **fő** verzió. Ha nem rendelkezik a PowerShell 5.0, kövesse a [hivatkozás](https://docs.microsoft.com/powershell/scripting/setup/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell) frissítése a PowerShell 5.0-s.

  > [!Note]  
  > PowerShell 5.0 szükséges egy Windows-gépen.

- **Egy rendszergazda jogú parancssorban futtassa a Powershellt**  
  Tudni futtassa a PowerShell környezetet rendszergazdai jogosultságokkal kell.

- **PowerShell-galériából hozzáférés**  
  Frissítenie kell a hozzáférést a [PowerShell-galériából](https://www.powershellgallery.com). A katalógus a PowerShell-tartalmak központi adattára. A **PowerShellGet** modul felderítése, telepítése, frissítése és PowerShell-összetevőket, például a modulok, DSC-erőforrások, szerepkörrel képességeket és szkripteket a PowerShell-galériában és egyéb titkos közzétételéhez szükséges parancsmagokat tartalmazza tárházak. Ha Powershellt használ a kapcsolat nélküli forgatókönyvek esetében, szüksége lesz a erőforrások lekérése egy gép kapcsolattal az internethez, és tárolja őket egy helyen érhető el kapcsolat nélküli számítógépre.


<!-- Nuget? -->

## <a name="2-validate-if-the-powershell-gallery-is-accessible"></a>2. Ellenőrizheti, hogy a PowerShell-galériából érhető el

Ha egy adattár, regisztrálva van a PSGallery ellenőrzése.

> [!Note]  
> Ezt a lépést Internet-hozzáférést igényel. 

Nyisson meg egy rendszergazda jogú PowerShell-parancssort, és futtassa a következő parancsmagokat:

````PowerShell  
Import-Module -Name PowerShellGet -ErrorAction Stop
Import-Module -Name PackageManagement -ErrorAction Stop
Get-PSRepository -Name "PSGallery"
````

Ha a tárház nincs regisztrálva, nyisson meg egy rendszergazda jogú PowerShell-munkamenetet, és futtassa a következő parancsot:

```PowerShell
Register-PsRepository -Default
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

## <a name="3-uninstall-existing-versions-of-the-azure-stack-powershell-modules"></a>3. Az Azure Stack PowerShell-modulok meglévő verzióinak eltávolítása

A szükséges verzió telepítése előtt győződjön meg arról, hogy távolítsa el minden korábban telepített Azure Stack AzureRM PowerShell-modulok. Az alábbi két módszer egyikével eltávolíthatja őket:

1. Távolítsa el a meglévő AzureRM PowerShell-modulok, zárja be az összes aktív PowerShell-munkamenetet, és futtassa a következő parancsmagokat:

  ````PowerShell  
    Uninstall-Module AzureRM.AzureStackAdmin -Force
    Uninstall-Module AzureRM.AzureStackStorage -Force
    Uninstall-Module -Name AzureStack -Force
  ````

2. Törölje az összes kezdődő mappáját `Azure` a a `C:\Program Files\WindowsPowerShell\Modules` és `C:\Users\AzureStackAdmin\Documents\WindowsPowerShell\Modules` mappákat. Ezek a mappák törlése eltávolítja a bármely meglévő PowerShell-modulok.

## <a name="4-connected-install-powershell-for-azure-stack-with-internet-connectivity"></a>4. Csatlakoztatva: Az internetkapcsolattal rendelkező Azure stack telepítése PowerShell

Az Azure Stack igényel a **2017-03-09-profile** API-verzió profilt, amely telepítésével érhető el a **AzureRM.Bootstrapper** modul. Az AzureRM-modulok mellett is telepíteni kell az Azure Stack-specifikus PowerShell-modulok. 

Futtassa a következő PowerShell-parancsfájl ezeket a modulokat a fejlesztői munkaállomáson telepítendő:

  - **Verzió 1.4.0-s** (az Azure Stack 1804-s vagy újabb)

    ```PowerShell  
    # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet 
    Install-Module -Name AzureRm.BootStrapper 

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session. 
    Use-AzureRmProfile -Profile 2017-03-09-profile -Force 

    # Install Module Version 1.4.0 if Azure Stack is running 1804 at a minimum 
    Install-Module -Name AzureStack -RequiredVersion 1.4.0
    ```

- **Verzió 1.2.11** (előtt 1804)

    ```PowerShell  
    # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet 
    Install-Module -Name AzureRm.BootStrapper 

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session. 
    Use-AzureRmProfile -Profile 2017-03-09-profile -Force 

    # Install Module Version 1.2.11 if Azure Stack is running a lower version than 1804 
    Install-Module -Name AzureStack -RequiredVersion 1.2.11 
    ```

Erősítse meg a telepítést a következő parancs futtatásával:

```PowerShell  
Get-Module -ListAvailable | where-Object {$_.Name -like "Azs*"}
```

Ha a telepítés sikeres, a kimenetben az AzureRM- és AzureStack modulok jelennek meg.

## <a name="5-disconnected-install-powershell-without-an-internet-connection"></a>5. Megszakadt: Internetkapcsolat nélküli telepítés PowerShell

Leválasztott forgatókönyvekben először töltse le a PowerShell-modulok olyan gépre, amely rendelkezik internetkapcsolattal, és utána az Azure Stack Development Kit telepítéséhez.

Jelentkezzen be egy számítógépre, ahol nincs internetkapcsolat, és töltse le az Azure Resource Manager és AzureStack-csomagokat a helyi számítógépre, az Azure Stack verziójától függően az alábbi parancsfájlok használatával.


  - **Verzió 1.3.0** (az Azure Stack 1804-s vagy újabb)
  
    > [!Note]  
    Frissítse a 1.2.11 verzióját, lásd: a [áttelepítési útmutató](https://aka.ms/azspowershellmigration).

    ````PowerShell  
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

      $Path = "<Path that is used to save the packages>"
      Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.4.0
    ````

  - **Verzió 1.2.11** (előtt 1804)

    ````PowerShell  
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

      $Path = "<Path that is used to save the packages>"

      Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 1.2.11
    ````

2. A letöltött csomagokat másolja át az USB-eszközt.

3. Jelentkezzen be a munkaállomás, és másolja a csomagokat az USB-eszközt egy olyan helyre a munkaállomáson.

4. Most kell ezen a helyen az alapértelmezett tárház regisztrálásához és a tárházból az AzureRM- és az AzureStack modulok telepítéséhez:

   ```PowerShell
   #requires -Version 5
   #requires -RunAsAdministrator
   #requires -Module PowerShellGet
   #requires -Module PackageManagement

   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation  -InstallationPolicy Trusted

   Install-Module AzureRM -Repository $RepoName

   Install-Module AzureStack -Repository $RepoName 
   ```

## <a name="6-configure-powershell-to-use-a-proxy-server"></a>6. Proxykiszolgáló használata a PowerShell konfigurálása

Az Internet eléréséhez proxykiszolgáló használatát igénylő forgatókönyvekben először konfigurálnia kell egy meglévő proxykiszolgáló használata a PowerShell-lel.

1. Nyisson meg egy rendszergazda jogú PowerShell-parancssorban.
2. Futtassa az alábbi parancsot:

````PowerShell  
  #To use Windows credentials for proxy authentication
  [System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials

  #Alternatively, to prompt for separate credentials that can be used for #proxy authentication

  [System.Net.WebRequest]::DefaultWebProxy.Credentials = Get-Credential
````

## <a name="next-steps"></a>További lépések

 - [Azure Stack-eszközök letöltése a githubról](azure-stack-powershell-download.md)
 - [Az Azure Stack felhasználói PowerShell-környezet konfigurálása](user/azure-stack-powershell-configure-user.md)  
 - [Az Azure Stack-operátorokról PowerShell-környezet konfigurálása](azure-stack-powershell-configure-admin.md) 
 - [Az Azure Stackben API-verzióprofilok kezelése](user/azure-stack-version-profiles.md)  
