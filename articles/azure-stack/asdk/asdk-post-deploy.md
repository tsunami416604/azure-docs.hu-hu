---
title: Szolgáltatástelepítési konfigurációk az Azure Stack Development Kit (ASDK) a közzététele |} A Microsoft Docs
description: Az Azure Stack Development Kit (ASDK) telepítése után győződjön meg arról is, ajánlott konfigurációs változásait ismerteti.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: d3bfe2c472d48a68bd818ac06874db136528b470
ms.sourcegitcommit: 3d0295a939c07bf9f0b38ebd37ac8461af8d461f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2018
ms.locfileid: "43840269"
---
# <a name="post-asdk-installation-configuration-tasks"></a>ASDK telepítés utáni konfigurációs feladatok

Miután [telepítése az Azure Stack Development Kit (ASDK)](asdk-install.md), néhány ajánlott telepítés utáni konfigurációs változtatásokat kell.

## <a name="install-azure-stack-powershell"></a>Az Azure Stack PowerShell telepítése

Az Azure Stack kompatibilis az Azure PowerShell-modulok használata az Azure Stack van szükség.

Azure Stack PowerShell-parancsokat a PowerShell-galériából keresztül telepíti. A PSGallery tárház regisztrálása nyisson meg egy rendszergazda jogú PowerShell-munkamenetet, és futtassa a következő parancsot:

``` Powershell
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted
```

API-verzióprofilok segítségével adja meg az Azure Stack-kompatibilis AzureRM-modulok.  API-verzióprofilok kezelése az Azure és az Azure Stack közötti terveztek. Egy API-verzióprofil egy adott API-verziók az AzureRM PowerShell-modulok. A **AzureRM.Bootstrapper** modul, amely a PowerShell-galériából érhető el az API-verzióprofilok működéséhez szükséges PowerShell-parancsmagokat kínál.

Telepítheti a legújabb Azure Stack PowerShell-modul vagy a ASDK gazdagépnek internetkapcsolat nélkül:

> [!IMPORTANT]
> A szükséges verzió telepítése előtt győződjön meg arról, hogy [távolítsa el minden meglévő Azure PowerShell-modulok](.\.\azure-stack-powershell-install.md#3-uninstall-existing-versions-of-the-azure-stack-powershell-modules).

- **Internetkapcsolattal rendelkező** ASDK állomásról. Futtassa a következő PowerShell-parancsfájlt a development kit telepítésen ezeket a modulokat telepíteni:

  ``` PowerShell
  # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet. 
  Install-Module `
    -Name AzureRm.BootStrapper

  # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
  Use-AzureRmProfile `
    -Profile 2017-03-09-profile -Force

  # Install Azure Stack Module Version 1.4.0. If running a pre-1804 version of Azure Stack, change the -RequiredVersion value to 1.2.11.
  Install-Module -Name AzureStack -RequiredVersion 1.4.0 

  ```

  Ha a telepítés sikeres, a kimenetben az AzureRM- és AzureStack modulok jelennek meg.

- **Internetkapcsolat nélküli** ASDK állomásról. A leválasztott forgatókönyvekben, először le kell töltenie a PowerShell-modulok a gépre, amely rendelkezik internetkapcsolattal, a következő PowerShell-parancsokkal:

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
  # Install Azure Stack Module Version 1.4.0. If running a pre-1804 version of Azure Stack, change the -RequiredVersion value to 1.2.11.  
    -RequiredVersion 1.4.0
  ```

  Ezután másolja a letöltött csomagokat ASDK számítógépre, és regisztrálja a hely alapértelmezett tárházaként, és a tárházból az AzureRM- és az AzureStack modulok telepítéséhez:

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

## <a name="download-the-azure-stack-tools"></a>Az Azure Stack-eszközök letöltése

[1.2.9-es-eszközök](https://github.com/Azure/AzureStack-Tools) van egy GitHub-adattár, amely futtatja a PowerShell-modulok kezelésére és üzembe erőforrásokat az Azure Stack. Ezek az eszközök klónozza a GitHub-adattárát, vagy töltse le az AzureStack-eszközök mappát a következő szkript futtatásával:

  ```PowerShell
  # Change directory to the root directory. 
  cd \

  # Enforce usage of TLSv1.2 to download the Azure Stack tools archive from GitHub
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
  invoke-webrequest `
    https://github.com/Azure/AzureStack-Tools/archive/master.zip `
    -OutFile master.zip

  # Expand the downloaded files.
  expand-archive master.zip `
    -DestinationPath . `
    -Force

  # Change to the tools directory.
  cd AzureStack-Tools-master
  ```

## <a name="validate-the-asdk-installation"></a>A ASDK a telepítés ellenőrzése
Győződjön meg arról, hogy a ASDK központi telepítés sikeres volt-e, használhatja a Test-AzureStack parancsmag az alábbi lépéseket:

1. Jelentkezzen AzureStack\AzureStackAdmin ASDK a gazdagépen.
2. Nyissa meg a Powershellt rendszergazdaként (nem PowerShell ISE-ben).
3. Futtassa: `Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint`
4. Futtassa: `Test-AzureStack`

A tesztek végrehajtásához néhány percet igénybe vehet. Ha a telepítés sikeres volt, a következőhöz hasonló eredményt fog:

![test-azurestack](media/asdk-post-deploy/test-azurestack.png)

Hiba lépett fel, ha a hibaelhárítási lépésekkel segítséget kaphat.

## <a name="reset-the-password-expiration-policy"></a>Jelszó-elévülési szabályzatának alaphelyzetbe állítása 
Ahhoz, hogy, hogy a jelszó a development kit gazdagép le nem jár, a kiértékelési időszak vége előtt, kövesse az alábbi lépéseket a ASDK üzembe helyezése után.

### <a name="to-change-the-password-expiration-policy-from-powershell"></a>Jelszó-elévülési szabályzatának módosítása a Powershell:
Futtassa a parancsot egy rendszergazda jogú Powershell-konzolt:

```powershell
Set-ADDefaultDomainPasswordPolicy -MaxPasswordAge 180.00:00:00 -Identity azurestack.local
```

### <a name="to-change-the-password-expiration-policy-manually"></a>Jelszó-elévülési szabályzatának manuális módosítása:
1. Nyissa meg a fejlesztői csomag gazdagép **Csoportházirend kezelése** (GPMC. Az MMC), és keresse meg **Csoportházirend kezelése** – **erdő: azurestack.local** – **tartományok** – **azurestack.local**.
2. Kattintson a jobb gombbal **alapértelmezett tartományi házirend** kattintson **szerkesztése**.
3. Lépjen a a Csoportházirendkezelés-szerkesztő, **számítógép konfigurációja** – **házirendek** – **Windows beállítások** – **biztonsági beállítások**– **Fiókházirend** – **jelszóházirend**.
4. A jobb oldali ablaktáblában kattintson duplán a **jelszó maximális kora**.
5. Az a **jelszó maximális kora tulajdonságok** párbeszédpanelen módosítsa a **a jelszó lejár** értéket a következőre **180**, és kattintson a **OK**.

![Csoportházirend kezelése konzol](media/asdk-post-deploy/gpmc.png)


## <a name="next-steps"></a>További lépések
[A ASDK regisztrálása az Azure-ral](asdk-register.md)
