---
title: Telepítse a PowerShell Azure verem |} Microsoft Docs
description: Megtudhatja, hogyan telepítse a PowerShell Azure verem.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 5/17/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: cb672c8e378db80707db1b0cf77a3196e36b1eb5
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
---
# <a name="install-powershell-for-azure-stack"></a>A verem az Azure PowerShell telepítése

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Azure verem kompatibilis Azure PowerShell-modulok az Azure veremnek megfelelő működéséhez szükségesek. Az útmutató azt végigvezetik Önt az Azure verem PowerShell telepítéséhez szükséges lépéseket.

Ez a cikk tartalmaz részletes utasításokat verem Azure PowerShell telepítése.

> [!Note]
> A következő lépések végrehajtásához szükséges PowerShell 5.0. A verzió ellenőrzéséhez futtassa $PSVersionTable.PSVersion, és hasonlítsa össze a **fő** verziója.

Azure verem PowerShell-parancsokat a PowerShell-galériában keresztül telepített. Az alábbi eljárás segítségével Ha PSGallery tára ellenőrzéséhez nyisson meg egy rendszergazda jogú PowerShell-munkamenetet, és futtassa a következő parancsot:

```PowerShell  
Get-PSRepository -Name "PSGallery"
```

Ha a tárház nincs regisztrálva, nyisson meg egy rendszergazda jogú PowerShell-munkamenetet, és futtassa a következő parancsot:

```PowerShell  
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```
> [!Note]  
> Ez a lépés Internet-hozzáférést igényel. 

## <a name="uninstall-existing-versions-of-powershell"></a>Távolítsa el a PowerShell meglévő verziói

A szükséges verzió telepítése előtt győződjön meg arról, hogy távolítsa el a korábban telepített Azure verem PowerShell modul. Az alábbi két módszer egyikével eltávolíthatja őket:

 - Távolítsa el a meglévő PowerShell-modulok, zárja be az összes aktív PowerShell-munkamenetekben, és futtassa a következő parancsot:

  ```PowerShell
    Uninstall-Module AzureRM.AzureStackAdmin -Force
    Uninstall-Module AzureRM.AzureStackStorage -Force
    Uninstall-Module -Name AzureStack -Force
  ```

 - Kezdje az "Azure" összes mappa törlése a `C:\Program Files\WindowsPowerShell\Modules` és `C:\Users\AzureStackAdmin\Documents\WindowsPowerShell\Modules` mappák. Törli a meglévő PowerShell-modulok.

A következő szakaszok ismertetik a verem Azure PowerShell telepítéséhez szükséges lépéseket. PowerShell működtetett csatlakoztatva, részben kapcsolódó Azure-veremben, vagy egy leválasztott forgatókönyvben telepíthető.

## <a name="install-powershell-in-a-connected-scenario-with-internet-connectivity"></a>Telepítse a PowerShell csatlakoztatott forgatókönyv esetében (internetes kapcsolattal rendelkező)

Azure verem kompatibilis AzureRM-modulok API-verzió profilok keresztül telepített. Azure verem igényel a **2017-03-09-profil** API-verzió profilt, amely érhető el a AzureRM.Bootstrapper modul telepítésével. API-verzió profilok és az általuk megadott parancsmagok kapcsolatos információkért tekintse meg a [API-verzió profilok kezeléséhez](user/azure-stack-version-profiles.md). AzureRM modulokat is telepíteni kell a verem vonatkozó Azure PowerShell-modulok. Ezek a modulok telepítése a fejlesztő munkaállomás a következő PowerShell-parancsfájl futtatása:

  ```PowerShell  
# Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet 
Install-Module -Name AzureRm.BootStrapper 

# Install and import the API Version Profile required by Azure Stack into the current PowerShell session. 
Use-AzureRmProfile -Profile 2017-03-09-profile -Force 

# Install Module Version 1.3.0 if Azure Stack is running 1804 at a minimum 
Install-Module -Name AzureStack -RequiredVersion 1.3.0 

# Install Module Version 1.2.11 if Azure Stack is running a lower version then 1804 
Install-Module -Name AzureStack -RequiredVersion 1.2.11 
  ```

A telepítés jóváhagyásához, futtassa a következő parancsot:

```PowerShell  
Get-Module -ListAvailable | where-Object {$_.Name -like "Azs*"}
```

Ha a telepítés sikeres, a kimenet a AzureRM és AzureStack modulok jelennek meg.

## <a name="install-powershell-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity"></a>Telepítse a PowerShell egy leválasztott vagy egy részben csatlakoztatott forgatókönyv (az korlátozott internetkapcsolat)

Kapcsolat nélküli forgatókönyv esetében először töltse le a PowerShell-modul egy internetkapcsolattal rendelkező gép, és majd átviszi a Azure verem csomag telepítéséhez.

> [!IMPORTANT]  
> A kiadás a verem 1.3.0 Azure PowerShell modul megtörje a változások listájának tartalmaz. A 1.2.11 rendszerről verzióját, tekintse meg a [áttelepítési útmutató](https://aka.ms/azspowershellmigration).

1. Jelentkezzen be a számítógépre, amelyen kapcsolódik az internethez, és a következő parancsfájl letöltése az Azure RM-ben, és AzureStack csomagok használata a helyi számítógépre:

   ```PowerShell  
   $Path = "<Path that is used to save the packages>"

   Save-Package `
     -ProviderName NuGet `
     -Source https://www.powershellgallery.com/api/v2 `
     -Name AzureRM `
     -Path $Path `
     -Force `
     -RequiredVersion 1.2.11

   Save-Package `
     -ProviderName NuGet `
     -Source https://www.powershellgallery.com/api/v2 `
     -Name AzureStack `
     -Path $Path `
     -Force `
     -RequiredVersion 1.3.0 
   ```

  > [!Important]  
  > Ha nem futtatja Azure verem frissítés 1804 vagy nagyobb, módosítsa a **requiredversion** paraméterérték `1.2.11`. 

2. A letöltött csomagok másolása keresztül egy USB-eszközt.

3. Jelentkezzen be a munkaállomás és az USB-eszközt a csomagokat másolhat egy helyet a munkaállomáson.

4. Most kell ezen a helyen az alapértelmezett tárház regisztrálásához és a AzureRM és AzureStack modulok telepítése ebben a tárházban lévő:

   ```PowerShell
   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository `
     -Name $RepoName `
     -SourceLocation $SourceLocation `
     -InstallationPolicy Trusted

   Install-Module AzureRM `
     -Repository $RepoName

   Install-Module AzureStack `
     -Repository $RepoName 
   ```

## <a name="configure-powershell-to-use-a-proxy-server"></a>Proxykiszolgáló használata a PowerShell konfigurálása

Az internet eléréséhez proxykiszolgáló szükséges esetben először konfigurálnia kell a PowerShell egy meglévő proxykiszolgáló használatára.

1. Nyisson meg egy rendszergazda jogú PowerShell-parancssort.
2. Futtassa az alábbi parancsot:

````PowerShell  
  #To use Windows credentials for proxy authentication
  [System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials

  #Alternatively, to prompt for separate credentials that can be used for #proxy authentication

  [System.Net.WebRequest]::DefaultWebProxy.Credentials = Get-Credential
````

## <a name="next-steps"></a>További lépések

 - [Töltse le az Azure-verem eszközök a Githubról](azure-stack-powershell-download.md)
 - [Az Azure-verem felhasználói PowerShell környezet konfigurálása](user/azure-stack-powershell-configure-user.md)  
 - [Az Azure-verem operátor PowerShell környezet konfigurálása](azure-stack-powershell-configure-admin.md) 
 - [Azure verem API-verzió profilok kezelése](user/azure-stack-version-profiles.md)  
