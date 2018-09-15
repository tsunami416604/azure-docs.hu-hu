---
title: Hitelesítő adatokat küldenie a Desired State Configuration használatával
description: Útmutató a biztonságos hitelesítő adatok átadása az Azure virtual machines használatával a PowerShell Desired State Configuration (DSC).
services: virtual-machines-windows
documentationcenter: ''
author: bobbytreed
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
ms.author: robreed
ms.openlocfilehash: 52e115aa7f54eccc2be4e500c544aa38ca3bc32d
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2018
ms.locfileid: "45631276"
---
# <a name="pass-credentials-to-the-azure-dscextension-handler"></a>Hitelesítő adatokat adnak át az Azure DSCExtension kezelő

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Ez a cikk ismerteti a Desired State Configuration (DSC) bővítmény az Azure-hoz. A DSC-bővítmény kezelő áttekintését lásd: [bemutatása az Azure Desired State Configuration bővítmény kezelő](dsc-overview.md).

## <a name="pass-in-credentials"></a>A hitelesítő adatokat adja át

A konfigurációs folyamat részeként, előfordulhat, hogy kell beállítása a felhasználói fiókok,-szolgáltatások eléréséhez, vagy program telepítése a felhasználói környezetben. Ehhez az ezekről a dolgokról, meg kell adnia hitelesítő adatokat.

DSC segítségével állítsa be a paraméteres konfigurációkat. A paraméteres configuration hitelesítő adatok a konfiguráció átadott és .mof fájlokat biztonságosan tárolva. Az Azure Bővítménykezelő hitelesítőadat-kezelés azáltal, hogy a tanúsítványok automatikus felügyeleti egyszerűbbé teszi.

A következő DSC-konfiguráció parancsfájl egy helyi felhasználói fiókot hoz létre a megadott jelszó:

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

Fontos, hogy tartalmazzák **csomópont localhost** a konfiguráció részeként. A kiterjesztés kezelő kifejezetten keres a **csomópont localhost** utasítást. A jelen nyilatkozat hiányzik, a következő lépések nem működnek. Emellett az is fontos, hogy tartalmazza a typecast **[PsCredential]**. Az adott típusú elindítja a bővítményt a hitelesítő adatok titkosításához.

Ez a szkript közzététele az Azure Blob storage:

`Publish-AzureRmVMDscConfiguration -ConfigurationPath .\user_configuration.ps1`

Az Azure-DSC-bővítmény és a hitelesítő adatok megadása:

```powershell
$configurationName = 'Main'
$configurationArguments = @{ Credential = Get-Credential }
$configurationArchive = 'user_configuration.ps1.zip'
$vm = Get-AzureRmVM -Name 'example-1'

$vm = Set-AzureRmVMDscExtension -VMName $vm -ConfigurationArchive $configurationArchive -ConfigurationName $configurationName -ConfigurationArgument @configurationArguments

$vm | Update-AzureRmVM
```

## <a name="how-a-credential-is-secured"></a>A hitelesítő adatok titkosításának módját

Ezt a kódot futtató kéri a hitelesítő adatait. A hitelesítő adat áll rendelkezésre, miután rövid ideig tárolja a memóriában. Ha a hitelesítő adatok használatával van közzétéve a **Set-AzureRmVMDscExtension** parancsmagot, a hitelesítő adatok átkerülnek a virtuális gép HTTPS-kapcsolaton keresztül. A virtuális gépen az Azure tárolja a hitelesítő adatokat a lemezen a helyi virtuális gép tanúsítvány használatával titkosítja. A hitelesítő adatok visszafejtése röviden a memóriában, és ezután újból titkosított átadása, DSC.

Ez a folyamat eltér attól az [biztonságos konfigurációval anélkül, hogy a kiterjesztés kezelő](/powershell/dsc/securemof). Az Azure-környezet akadályozható meg a tanúsítványok segítségével biztonságosan konfigurációs adatok továbbítására. A DSC-bővítmény kezelő használatakor nem kell megadnia **$CertificatePath** vagy egy **$CertificateID**/ **$Thumbprint** bejegyzést**ConfigurationData**.

## <a name="next-steps"></a>További lépések

- Get- [Azure DSC bővítmény kezelő bemutatása](dsc-overview.md).
- Vizsgálja meg a [Azure Resource Manager-sablon a DSC-bővítmény](dsc-template.md).
- További információ a PowerShell DSC, nyissa meg a [PowerShell dokumentációs központban](/powershell/dsc/overview).
- A további funkciókat, amelyeket felügyelhet a PowerShell DSC használatával, és további DSC-erőforrásokat, keresse meg a [PowerShell-galériából](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).