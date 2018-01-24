---
title: "Telepítse a PowerShell Azure verem |} Microsoft Docs"
description: "Megtudhatja, hogyan telepítse a PowerShell Azure verem."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 0CDD8B9B-EC32-4453-918A-D0A606C7BC10
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/23/2018
ms.author: mabrigg
ms.openlocfilehash: 18a697813fbeb11be7a599359285f824ed804216
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2018
---
# <a name="install-powershell-for-azure-stack"></a>A verem az Azure PowerShell telepítése  

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Azure verem kompatibilis Azure PowerShell-modulok az Azure veremnek megfelelő működéséhez szükségesek. Az útmutató azt végigvezetik Önt az Azure verem PowerShell telepítéséhez szükséges lépéseket. A lépést, ha a VPN-en keresztül kapcsolódik az Azure verem szoftverfejlesztői készlet, vagy a Windows-alapú külső ügyfél cikkben leírt használhatja.

Ez a cikk tartalmaz részletes utasításokat verem Azure PowerShell telepítése. Azonban ha azt szeretné, hogy gyorsan PowerShell telepítése és konfigurálása, a parancsfájlt használhatja, amely megtalálható a [, amelyekből megismerheti a PowerShell-lel](azure-stack-powershell-configure-quickstart.md) témakör. 

> [!NOTE]
> A következő lépések végrehajtásához szükséges PowerShell 5.0. A verzió ellenőrzéséhez $PSVersionTable.PSVersion futtatása, és hasonlítsa össze a "Fő" verziószáma.

Azure verem PowerShell-parancsokat a PowerShell-galériában keresztül telepített. Regisztrálja a PSGallery tárház, nyissa meg egy rendszergazda jogú PowerShell-munkamenetet a csomag vagy a Windows-alapú külső ügyfél Ha VPN-en keresztül csatlakozó és a következő parancsot:

```powershell
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted
```

## <a name="uninstall-existing-versions-of-powershell"></a>Távolítsa el a PowerShell meglévő verziói

A szükséges verzió telepítése előtt győződjön meg arról, hogy távolítsa el a meglévő Azure PowerShell modul. Az alábbi két módszer egyikével eltávolíthatja őket:

* Távolítsa el a meglévő PowerShell-modulok, jelentkezzen be a csomagban található, vagy a Windows-alapú külső ügyfél Ha azt tervezi, hogy a VPN-kapcsolatot. Zárja be az összes aktív PowerShell-munkamenetekben, és futtassa a következő parancsot: 

   ```powershell
   Get-Module -ListAvailable | where-Object {$_.Name -like “Azure*”} | Uninstall-Module
   ```

* Jelentkezzen be a csomagban található, vagy a Windows-alapú külső ügyfél Ha azt tervezi, hogy a VPN-kapcsolatot. Kezdje az "Azure" összes mappa törlése a `C:\Program Files\WindowsPowerShell\Modules` és `C:\Users\AzureStackAdmin\Documents\WindowsPowerShell\Modules` mappák. Törli a "AzureStackAdmin" és "globális" felhasználói hatókörök meglévő PowerShell modul. 

A következő szakaszok ismertetik a verem Azure PowerShell telepítéséhez szükséges lépéseket. PowerShell működtetett csatlakoztatva, részben kapcsolódó Azure-veremben, vagy egy leválasztott forgatókönyvben telepíthető. 

## <a name="install-powershell-in-a-connected-scenario-with-internet-connectivity"></a>Telepítse a PowerShell csatlakoztatott forgatókönyv esetében (internetes kapcsolattal rendelkező)

Azure verem kompatibilis AzureRM-modulok API-verzió profilok keresztül telepített. Azure verem igényel a **2017-03-09-profil** API-verzió profilt, amely érhető el a AzureRM.Bootstrapper modul telepítésével. API-verzió profilok és az általuk megadott parancsmagok kapcsolatos információkért tekintse meg a [API-verzió profilok kezeléséhez](azure-stack-version-profiles.md). AzureRM modulokat is telepíteni kell a verem vonatkozó Azure PowerShell-modulok. Ezek a modulok telepítése a fejlesztő munkaállomás a következő PowerShell-parancsfájl futtatása:

> [!IMPORTANT]
> A kiadási AzureRM 1.2.11 PowerShell modul megtörje a változások listájának tartalmaz. A 1.2.10 rendszerről verziója, az áttelepítési útmutatóban talál: [https://aka.ms/azspowershellmigration](https://aka.ms/azspowershellmigration).

  ```powershell
  # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet 
  Install-Module `
    -Name AzureRm.BootStrapper

  # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
  Use-AzureRmProfile `
    -Profile 2017-03-09-profile -Force

  Install-Module `
    -Name AzureStack `
    -RequiredVersion 1.2.11
  ```

A telepítés jóváhagyásához, futtassa a következő parancsot:

  ```powershell
  Get-Module `
    -ListAvailable | where-Object {$_.Name -like “Azure*”}
  ```
  Ha a telepítés sikeres, a kimenet a AzureRM és AzureStack modulok jelennek meg.

## <a name="install-powershell-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity"></a>Telepítse a PowerShell egy leválasztott vagy egy részben csatlakoztatott forgatókönyv (az korlátozott internetkapcsolat)

Kapcsolat nélküli forgatókönyv esetében először töltse le a PowerShell-modul egy internetkapcsolattal rendelkező gép, és majd átviszi a Azure verem csomag telepítéséhez.

> [!IMPORTANT]
> A kiadási AzureRM 1.2.11 PowerShell modul megtörje a változások listájának tartalmaz. A 1.2.10 rendszerről verziója, az áttelepítési útmutatóban talál: [https://aka.ms/azspowershellmigration](https://aka.ms/azspowershellmigration).

1. Jelentkezzen be a számítógépre, amelyen kapcsolódik az internethez, és a következő parancsfájl letöltése az Azure RM-ben, és AzureStack csomagok használata a helyi számítógépre:

   ```powershell
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
     -RequiredVersion 1.2.11 
   ```

2. A letöltött csomagok másolása keresztül egy USB-eszközt.

3. Jelentkezzen be a csomag és a csomagokat másolhatja az USB-eszközről, a csomag helyére. 

4. Most kell ezen a helyen az alapértelmezett tárház regisztrálásához és a AzureRM és AzureStack modulok telepítése ebben a tárházban lévő:

   ```powershell
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

## <a name="next-steps"></a>További lépések

* [Töltse le az Azure-verem eszközök a Githubról](azure-stack-powershell-download.md)
* [Az Azure-verem felhasználói PowerShell környezet konfigurálása](user/azure-stack-powershell-configure-user.md)  
* [Az Azure-verem operátor PowerShell környezet konfigurálása](azure-stack-powershell-configure-admin.md) 
* [Azure verem API-verzió profilok kezelése](azure-stack-version-profiles.md)  
