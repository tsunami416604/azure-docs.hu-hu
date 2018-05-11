---
title: Hitelesítő adatok továbbítása az Azure-ban a célállapot-konfiguráció
description: Tudnivalók a biztonságos hitelesítő adatok továbbítása PowerShell kívánt állapot konfigurációs szolgáltatása (DSC) használata az Azure virtuális gépek számára.
services: virtual-machines-windows
documentationcenter: ''
author: DCtheGeek
manager: carmonm
editor: ''
tags: azure-resource-manager
keywords: dsc
ms.assetid: ea76b7e8-b576-445a-8107-88ea2f3876b9
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 05/02/2018
ms.author: dacoulte
ms.openlocfilehash: 666253d16ac51dcc21174211f71794f8b0ede07d
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="pass-credentials-to-the-azure-dscextension-handler"></a>Hitelesítő adatok továbbítása az Azure DSCExtension-kezelőnek

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Ez a cikk az Azure a kívánt állapot konfigurációs szolgáltatása (DSC) bővítményt tartalmazza. A DSC-kiterjesztés kezelője áttekintését lásd: [bemutatása az Azure célállapot-konfiguráció bővítmény kezelő](dsc-overview.md).

## <a name="pass-in-credentials"></a>A hitelesítő adatok átadására

A konfigurációs folyamat során, akkor előfordulhat, hogy kell felhasználói fiókok beállítása szolgáltatások elérésére és a program telepítése a felhasználói környezetben. Mindezek, meg kell adnia a hitelesítő adatokat.

A DSC paraméteres konfigurációk beállításához használhatja. Paraméteres konfigurációban hitelesítő adatokat a konfiguráció lett átadva, és biztonságos módon tárolja az .mof fájlok. Az Azure-bővítményt kezelő egyszerűbbé teszi a hitelesítőadat-kezelés, adja meg a tanúsítványok automatikus kezelését.

A következő DSC-konfiguráció parancsfájl helyi felhasználói fiókot hoz létre a megadott jelszóval:

```powershell
configuration Main
{
    param(
        [Parameter(Mandatory=$true)]
        [ValidateNotNullorEmpty()]
        [PSCredential]
        $Credential
    )
    Node localhost {
        User LocalUserAccount
        {
            Username = $Credential.UserName
            Password = $Credential
            Disabled = $false
            Ensure = "Present"
            FullName = "Local User Account"
            Description = "Local User Account"
            PasswordNeverExpires = $true
        }
    }
}
```

Fontos, hogy tartalmazzák **csomópont localhost** a konfigurálás részeként. A bővítmény kezelő kifejezetten keresi a **csomópont localhost** utasítást. Ha a jelen nyilatkozat hiányzik, a következő lépések nem működnek. Szintén fontos, hogy tartalmazzák a typecast **[PsCredential]**. Az adott típusú váltja ki a bővítményt a hitelesítő adatok titkosításához.

Ez a parancsfájl közzététele az Azure Blob storage:

`Publish-AzureRmVMDscConfiguration -ConfigurationPath .\user_configuration.ps1`

Állítsa be a Azure DSC-bővítményt, és adja meg a hitelesítő adat:

```powershell
$configurationName = 'Main'
$configurationArguments = @{ Credential = Get-Credential }
$configurationArchive = 'user_configuration.ps1.zip'
$vm = Get-AzureRmVM -Name 'example-1'

$vm = Set-AzureRmVMDscExtension -VMName $vm -ConfigurationArchive $configurationArchive -ConfigurationName $configurationName -ConfigurationArgument @configurationArguments

$vm | Update-AzureRmVM
```

## <a name="how-a-credential-is-secured"></a>A hitelesítő adatok titkosításának módját

Ezt a kódot futtató hitelesítő adatokat kér. Miután a hitelesítő adatok megadott röviden a memóriában tárolt. Ha a hitelesítő adatok használatával van közzétéve a **Set-AzureRmVMDscExtension** parancsmag, a hitelesítő adatokat a virtuális gép HTTPS-KAPCSOLATON keresztül továbbított. A virtuális gép Azure tárolja a hitelesítő adatokat a lemezen a helyi virtuális gép tanúsítvány használatával titkosítja. A hitelesítő adatok röviden visszafejti a memóriában, és ezután újra titkosított átadása akkor DSC.

Ez a folyamat eltér [nélkül a bővítmény kezelő biztonságos konfigurációk használatával](/powershell/dsc/securemof). Az Azure környezetbe akadályozható meg a tanúsítványok segítségével biztonságosan konfigurációs adatok átviteléhez. A DSC-kiterjesztés kezelője használatakor nem kell adnia **$CertificatePath** vagy egy **$CertificateID**/ **$Thumbprint** bejegyzést**ConfigurationData**.

## <a name="next-steps"></a>További lépések

- Első egy [Bevezetés az Azure DSC-kiterjesztés kezelője](dsc-overview.md).
- Vizsgálja meg a [Azure Resource Manager sablon a DSC-bővítmény](dsc-template.md).
- A PowerShell DSC kapcsolatos további információkért lásd a [PowerShell dokumentációs központban](/powershell/dsc/overview).
- A PowerShell DSC használatával felügyelhető további funkciókat, és további DSC-erőforrásokat, keresse meg a [PowerShell-galériában](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).