---
title: "Szolgáltatástelepítési konfigurációk az Azure verem Development Kit (ASDK) a POST |} Microsoft Docs"
description: "Az Azure verem Development Kit (ASDK) telepítése után ellenőrizze az ajánlott konfiguráció változásait ismerteti."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 2183576e87aa2fb31f8be8f676a5aee7d52f68df
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/17/2018
---
# <a name="post-asdk-installation-configuration-tasks"></a>ASDK telepítés utáni konfigurációs feladatok
Miután [telepítése a ASDK](asdk-install.md), van néhány ajánlott telepítés utáni konfigurációs módosításokat végezni. 

## <a name="install-azure-stack-powershell"></a>Az Azure Stack PowerShell telepítése 
Azure verem kompatibilis Azure PowerShell-modulok az Azure veremnek megfelelő működéséhez szükségesek.

Azure verem PowerShell-parancsokat a PowerShell-galériában keresztül telepített. Regisztrálja a PSGallery tárház, nyisson meg egy rendszergazda jogú PowerShell-munkamenetet, és futtassa a következő parancsot:

``` Powershell
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted
```

 Azure verem kompatibilis AzureRM-modulok API-verzió profilok keresztül telepített. Azure verem a 2017-03-09-profil API verziója profilt, amely érhető el a AzureRM.Bootstrapper-modul telepítése szükséges. 
 
 A verem Azure PowerShell vagy a ASDK gazdaszámítógéphez internetkapcsolat nélkül telepítheti:

- **Internetkapcsolat** az ASDK állomásról. Ezek a modulok telepítését az development kit telepítése a következő PowerShell-parancsfájl futtatása:

  ``` PowerShell
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
  Ha a telepítés sikeres, a kimenet a AzureRM és AzureStack modulok jelennek meg.

- **Internetkapcsolat nélkül** az ASDK állomásról. Kapcsolat nélküli forgatókönyv esetében először le kell töltenie a PowerShell-modul a következő PowerShell-parancsokkal internetkapcsolattal rendelkező géphez:

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
    -RequiredVersion 1.2.11
  ```
  Ezután a letöltött csomagok másolása a ASDK számítógép és a hely alapértelmezett tárházaként rögzítéséhez, és a AzureRM és AzureStack modulok telepítése ebben a tárházban lévő:

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

## <a name="download-the-azure-stack-tools"></a>Töltse le az Azure-verem eszközök
[AzureStack-eszközök](https://github.com/Azure/AzureStack-Tools) van egy GitHub-tárházban, amelyen a PowerShell-modulok kezelése és Azure verem erőforrásokat üzembe helyezi. Ezek az eszközök klónozza a GitHub-tárházban, vagy töltse le a AzureStack-eszközök mappa a következő parancsfájl futtatásával:

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
Győződjön meg arról, hogy a ASDK telepítése sikeres volt-e, használhatja a Test-AzureStack parancsmag az alábbiak szerint:

1. Jelentkezzen be az ASDK állomáson AzureStack\CloudAdmin.
2. Nyissa meg a Powershellt rendszergazdaként (nem a PowerShell ISE).
3. Futtatás: `Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint`
4. Futtatás: `Test-AzureStack`

A tesztek néhány percet igénybe vehet. Ha a telepítés sikeres volt, a kimeneti alábbihoz hasonló:

![test-azurestack](media/asdk-post-deploy/test-azurestack.png)

Hiba történt, ha a hibaelhárítási lépésekkel kapcsolatos súgó megjelenítése.

## <a name="activate-the-administrator-and-tenant-portals"></a>A rendszergazda és bérlői portálon aktiválása
Után használó központi telepítések az Azure AD aktiválnia kell mindkét az Azure verem rendszergazda és bérlői portálon. Az aktiválás hozzájárul a verem Azure portál és az Azure Resource Manager a megfelelő engedélyeket ad (a hozzájárulási oldalon felsorolt) az összes felhasználó számára a könyvtár.

- Lépjen a felügyeleti portál https://adminportal.local.azurestack.external/guest/signup, olvassa el az adatokat, és kattintson a **elfogadás**. Után elfogadása, a szolgáltatás kevésbé jártas rendszergazdák számára is directory bérlői rendszergazdákat is hozzáadhat.

- A bérlői portál navigáljon https://portal.local.azurestack.external/guest/signup, olvassa el az adatokat, és kattintson a **elfogadás**. Elfogadása után a címtárban szereplő felhasználó bármikor beléphet a bérlői portálra. 

> [!NOTE] 
> Ha nem aktiválta a portálon, a címtár csak a rendszergazda bejelentkezhet és használja a portálok. Ha egy másik felhasználó jelentkezik be, akkor hibaüzenetet kap, amely meghatározza, hogy a rendszergazda nem adott engedélyek más felhasználók számára. Ha a rendszergazda natív módon nem tartozik Azure verem regisztrálva van a könyvtárra, az Azure-verem directory csatolni kell az aktiválási URL-címet. Például, ha Azure verem van regisztrálva a fabrikam.onmicrosoft.com és a rendszergazdai jogú felhasználó nem admin@contoso.com, navigáljon a https://portal.local.azurestack.external/guest/signup/fabrikam.onmicrosoft.com aktiválásához a portálon. 

## <a name="reset-the-password-expiration-policy"></a>A jelszó-elévülési szabályzatának alaphelyzetbe állítása 
Győződjön meg arról, hogy a jelszót ahhoz, hogy a development kit gazdagép az értékelés időszak lejárta előtt nem lejár, tegye a következőket a ASDK telepítése után.

### <a name="to-change-the-password-expiration-policy-from-powershell"></a>A jelszó-elévülési szabályzatának módosítása a Powershellből:
Futtassa a parancsot egy rendszergazda jogú Powershell-konzolt:

```powershell
Set-ADDefaultDomainPasswordPolicy -MaxPasswordAge 180.00:00:00 -Identity azurestack.local
```

### <a name="to-change-the-password-expiration-policy-manually"></a>A jelszó-elévülési szabályzatának manuális módosítása:
1. Nyissa meg a development kit gazdagép **csoportházirend-kezelő** (GPMC. MMC), és keresse meg **csoportházirend-kezelő** – **erdő: azurestack.local** – **tartományok** – **azurestack.local**.
2. Kattintson a jobb gombbal **alapértelmezett tartományházirend** kattintson **szerkesztése**.
3. Navigáljon a a Csoportházirendkezelés-szerkesztő, **számítógép konfigurációja** – **házirendek** – **Windows-beállítások** – **biztonsági beállítások**– **Fiókházirendek** – **jelszóházirend**.
4. A jobb oldali ablaktáblában kattintson duplán a **jelszó maximális élettartama**.
5. Az a **jelszó maximális élettartama tulajdonságok** párbeszédpanelen módosítsa a **a jelszó lejár** egy érték **180**, és kattintson a **OK**.

![Csoportházirend kezelése konzol](media/asdk-post-deploy/gpmc.png)


## <a name="next-steps"></a>További lépések
[A ASDK regisztrálni Azure-ral](asdk-register.md)
