---
title: Az Azure Stack PowerShell telepítése |} A Microsoft Docs
description: Ismerje meg az Azure Stack PowerShell telepítése.
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
ms.date: 07/10/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: e2785b0beeab042d4b1ad9a9eb5f545dbb58b8b9
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38487501"
---
# <a name="install-powershell-for-azure-stack"></a>Az Azure Stack PowerShell telepítése

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

Az Azure Stack kompatibilis az Azure PowerShell-modulok használata az Azure Stack van szükség. Az útmutató részletesen az Azure stack PowerShell telepítéséhez szükséges lépéseket.

Ez a cikk az Azure Stack PowerShell telepítésének útmutatója részletesen.

> [!Note]  
> Az alábbi lépéseket a PowerShell 5.0 szükséges. A verzió ellenőrzéséhez futtassa a $PSVersionTable.PSVersion, és hasonlítsa össze a **fő** verzió.

Azure Stack PowerShell-parancsokat a PowerShell-galériából keresztül telepíti. Az alábbi eljárás segítségével ellenőrizheti, hogy a PSGallery regisztrálva van, mint a tárház, nyisson meg egy rendszergazda jogú PowerShell-munkamenetet, és futtassa a következő parancsot:

```PowerShell  
Get-PSRepository -Name "PSGallery"
```

Ha a tárház nincs regisztrálva, nyisson meg egy rendszergazda jogú PowerShell-munkamenetet, és futtassa a következő parancsot:

```PowerShell  
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```
> [!Note]  
> Ezt a lépést Internet-hozzáférést igényel. 

## <a name="uninstall-existing-versions-of-the-azure-stack-powershell-modules"></a>Az Azure Stack PowerShell-modulok meglévő verzióinak eltávolítása

A szükséges verzió telepítése előtt győződjön meg arról, hogy távolítsa el minden korábban telepített Azure Stack AzureRM PowerShell-modulok. Az alábbi két módszer egyikével eltávolíthatja őket:

 - Távolítsa el a meglévő AzureRM PowerShell-modulok, zárja be az összes aktív PowerShell-munkamenetet, és futtassa a következő parancsot:

  ```PowerShell
    Uninstall-Module AzureRM.AzureStackAdmin -Force
    Uninstall-Module AzureRM.AzureStackStorage -Force
    Uninstall-Module -Name AzureStack -Force
  ```

 - Az "Azure" kezdődő összes mappa törlése a `C:\Program Files\WindowsPowerShell\Modules` és `C:\Users\AzureStackAdmin\Documents\WindowsPowerShell\Modules` mappákat. Ezek a mappák törlése eltávolítja a bármely meglévő PowerShell-modulok.

A következő szakaszok ismertetik az Azure stack PowerShell telepítéséhez szükséges lépéseket. PowerShell az Azure Stacken, amely a szolgáltatást a csatlakoztatott, részben csatlakoztatva, vagy leválasztott forgatókönyvek esetében is telepíthető.

## <a name="install-the-azure-stack-powershell-modules-in-a-connected-scenario-with-internet-connectivity"></a>Az Azure Stack PowerShell-modulok telepítése csatlakoztatott forgatókönyvekben (internetes kapcsolattal rendelkező)

Az Azure Stack kompatibilis AzureRM-modulok API-verzióprofilok keresztül vannak telepítve. Az Azure Stack igényel a **2017-03-09-profile** API-verzió profilt, amely a AzureRM.Bootstrapper modul telepítésével érhető el. API-verzióprofilok, és biztosítják azokat a parancsmagokkal kapcsolatos további információkért tekintse meg a [API-verzióprofilok kezelése](user/azure-stack-version-profiles.md). Az AzureRM-modulok mellett is telepíteni kell az Azure Stack-specifikus PowerShell-modulok. Futtassa a következő PowerShell-parancsfájl ezeket a modulokat a fejlesztői munkaállomáson telepítendő:

  ```PowerShell  
# Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet 
Install-Module -Name AzureRm.BootStrapper 

# Install and import the API Version Profile required by Azure Stack into the current PowerShell session. 
Use-AzureRmProfile -Profile 2017-03-09-profile -Force 

# Install Module Version 1.3.0 if Azure Stack is running 1804 at a minimum 
Install-Module -Name AzureStack -RequiredVersion 1.3.0 

# Install Module Version 1.2.11 if Azure Stack is running a lower version than 1804 
Install-Module -Name AzureStack -RequiredVersion 1.2.11 
  ```

Erősítse meg a telepítést, futtassa a következő parancsot:

```PowerShell  
Get-Module -ListAvailable | where-Object {$_.Name -like "Azs*"}
```

Ha a telepítés sikeres, a kimenetben az AzureRM- és AzureStack modulok jelennek meg.

## <a name="install-the-azure-stack-powershell-modules-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity"></a>A leválasztott vagy részlegesen csatlakoztatva a forgatókönyv (korlátozott internetes kapcsolattal rendelkező) az Azure Stack PowerShell-modulok telepítése

Leválasztott forgatókönyvekben először töltse le a PowerShell-modulok olyan gépre, amely rendelkezik internetkapcsolattal, és utána az Azure Stack Development Kit telepítéséhez.

> [!IMPORTANT]  
> Az Azure Stack 1.3.0 PowerShell-modul kiadása tartalmaz parancsban történt használhatatlanná tévő listáját. Frissítse a 1.2.11 verzióját, lásd: a [áttelepítési útmutató](https://aka.ms/azspowershellmigration).

1. Jelentkezzen be egy számítógépre, ahol nincs internetkapcsolat, és használja a következő szkriptet az AzureRM letöltése és AzureStack csomagok a helyi számítógépre:

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
  > Ha nem futtatja az Azure Stack frissítéssel 1804-s vagy újabb, módosítsa a **requiredversion** paraméterérték `1.2.11`. 

2. A letöltött csomagokat másolja át az USB-eszközt.

3. Jelentkezzen be a munkaállomás, és másolja a csomagokat az USB-eszközt egy olyan helyre a munkaállomáson.

4. Most kell ezen a helyen az alapértelmezett tárház regisztrálásához és a tárházból az AzureRM- és az AzureStack modulok telepítéséhez:

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

Az internet eléréséhez proxykiszolgáló használatát igénylő forgatókönyvekben először konfigurálnia kell egy meglévő proxykiszolgáló használata a PowerShell-lel.

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
