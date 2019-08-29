---
title: Hitelesítő adatok továbbítása az Azure-ba a kívánt állapot-konfigurációval
description: Ismerje meg, hogyan továbbíthatja biztonságosan a hitelesítő adatokat az Azure Virtual Machines szolgáltatásba a PowerShell desired State Configuration (DSC) használatával.
services: virtual-machines-windows
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
tags: azure-resource-manager
keywords: dsc
ms.assetid: ea76b7e8-b576-445a-8107-88ea2f3876b9
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 05/02/2018
ms.author: robreed
ms.openlocfilehash: 38a302545f2dd46a8123816a41c97ae26ee4c260
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70092513"
---
# <a name="pass-credentials-to-the-azure-dscextension-handler"></a>Hitelesítő adatok továbbítása az Azure DSCExtension-kezelőhöz

Ez a cikk az Azure kívánt State Configuration (DSC) bővítményét ismerteti. A DSC bővítmény kezelőjének áttekintését lásd: [Az Azure desired State Configuration Extension kezelő bemutatása](dsc-overview.md).

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="pass-in-credentials"></a>Hitelesítő adatok továbbítása

A konfigurációs folyamat részeként felhasználói fiókokat, hozzáférési szolgáltatásokat kell beállítania, vagy telepítenie kell egy programot egy felhasználói környezetben. Ezen dolgok végrehajtásához hitelesítő adatokat kell megadnia.

A DSC használatával lehet beállítani a paraméteres konfigurációkat. A paraméteres konfigurációban a hitelesítő adatok átkerülnek a konfigurációba, és biztonságosan tárolódnak a. MOF fájlban. Az Azure-bővítmény kezelője leegyszerűsíti a hitelesítő adatok kezelését a tanúsítványok automatikus felügyeletének biztosításával.

A következő DSC-konfigurációs szkript létrehoz egy helyi felhasználói fiókot a megadott jelszóval:

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

Fontos, hogy a konfiguráció részeként belefoglalja a **localhost csomópontot** . A bővítmény kezelője kifejezetten a **Node localhost** utasítást keresi. Ha ez az utasítás hiányzik, a következő lépések nem működnek. Fontos továbbá a typecast **[PsCredential]** belefoglalása is. Az adott típus aktiválja a bővítményt a hitelesítő adatok titkosításához.

A szkript közzététele az Azure Blob Storage-ban:

`Publish-AzVMDscConfiguration -ConfigurationPath .\user_configuration.ps1`

Az Azure DSC-bővítmény beállításához és a hitelesítő adatok megadásához:

```powershell
$configurationName = 'Main'
$configurationArguments = @{ Credential = Get-Credential }
$configurationArchive = 'user_configuration.ps1.zip'
$vm = Get-AzVM -Name 'example-1'

$vm = Set-AzVMDscExtension -VMName $vm -ConfigurationArchive $configurationArchive -ConfigurationName $configurationName -ConfigurationArgument @configurationArguments

$vm | Update-AzVM
```

## <a name="how-a-credential-is-secured"></a>A hitelesítő adatok biztonságossá tétele

A kód futtatása hitelesítő adatokat kér. A hitelesítő adatok megadása után a rendszer röviden tárolja a memóriában. Ha a hitelesítő adatok a **set-AzVMDscExtension** parancsmaggal lettek közzétéve, a hitelesítő adatokat a rendszer HTTPS-kapcsolaton keresztül továbbítja a virtuális géphez. A virtuális gépen az Azure a helyi virtuális gép tanúsítványával tárolja a lemezen titkosított hitelesítő adatokat. A hitelesítő adatokat a rendszer röviden visszafejti a memóriában, majd újra titkosítja, hogy átadja a DSC-nek.

Ez a folyamat eltér [a bővítmény-kezelő nélküli biztonságos konfigurációk használatával](/powershell/dsc/securemof). Az Azure-környezet lehetővé teszi, hogy biztonságos módon továbbítsa a konfigurációs adatokat a tanúsítványokon keresztül. A DSC bővítmény kezelőjének használatakor nem kell **$CertificatePath** vagy **$CertificateID**/  **$thumbprint** bejegyzést megadnia a **ConfigurationData**-ben.

## <a name="next-steps"></a>További lépések

- Bevezetés az [Azure DSC bővítmény kezelőjébe](dsc-overview.md).
- Vizsgálja [meg a DSC-bővítmény Azure Resource Manager sablonját](dsc-template.md).
- A PowerShell DSC-vel kapcsolatos további információkért nyissa meg a [PowerShell dokumentációs](/powershell/dsc/overview)központját.
- Ha további funkciókat szeretne kezelni a PowerShell DSC használatával, és további DSC-erőforrásokhoz, keresse fel a [PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0)-galériát.