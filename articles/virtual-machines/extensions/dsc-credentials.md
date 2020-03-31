---
title: Hitelesítő adatok átadása az Azure-nak a kívánt állapotkonfiguráció használatával
description: Megtudhatja, hogyan adhatja át biztonságosan a hitelesítő adatokat az Azure virtuális gépeinek a PowerShell kívánt állapotkonfigurációja (DSC) használatával.
services: virtual-machines-windows
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
tags: azure-resource-manager
keywords: Dsc
ms.assetid: ea76b7e8-b576-445a-8107-88ea2f3876b9
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 05/02/2018
ms.author: robreed
ms.openlocfilehash: f7edbd0fd8791829a2d9ffaa4e7c0ee0e561cc5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73748974"
---
# <a name="pass-credentials-to-the-azure-dscextension-handler"></a>Hitelesítő adatok átadása az Azure DSCExtension-kezelőnek

Ez a cikk ismerteti a kívánt állapotkonfigurációs (DSC) bővítmény az Azure-hoz. A DSC-bővítménykezelő áttekintését az [Azure kívánt állapotkonfigurációs bővítménykezelőjének bemutatása című témakörben találja.](dsc-overview.md)

 

## <a name="pass-in-credentials"></a>Hitelesítő adatok megadása

A konfigurációs folyamat részeként előfordulhat, hogy felhasználói fiókokat kell beállítania, szolgáltatásokhoz kell hozzáférnie, vagy felhasználói környezetben kell telepítenie egy programot. Ahhoz, hogy ezeket a dolgokat, meg kell adnia hitelesítő adatokat.

A DSC segítségével paraméterezett konfigurációkat állíthat be. Paraméterezett konfigurációesetén a hitelesítő adatok átkerülnek a konfigurációba, és biztonságosan tárolódnak .mof fájlokban. Az Azure-bővítménykezelő leegyszerűsíti a hitelesítő adatok kezelését a tanúsítványok automatikus kezelésével.

A következő DSC konfigurációs parancsfájl helyi felhasználói fiókot hoz létre a megadott jelszóval:

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

Fontos, hogy a **csomópont localhost** a konfiguráció részeként. A bővítménykezelő kifejezetten a **csomópont localhost** utasítását keresi. Ha ez az utasítás hiányzik, a következő lépések nem működnek. Az is fontos, hogy tartalmazza a typecast **[PsCredential]**. Ez a típus elindítja a bővítményt a hitelesítő adatok titkosításához.

A parancsfájl közzététele az Azure Blob storage-ban:

`Publish-AzVMDscConfiguration -ConfigurationPath .\user_configuration.ps1`

Az Azure DSC-bővítmény beállítása és a hitelesítő adatok megadása:

```powershell
$configurationName = 'Main'
$configurationArguments = @{ Credential = Get-Credential }
$configurationArchive = 'user_configuration.ps1.zip'
$vm = Get-AzVM -Name 'example-1'

$vm = Set-AzVMDscExtension -VMName $vm -ConfigurationArchive $configurationArchive -ConfigurationName $configurationName -ConfigurationArgument @configurationArguments

$vm | Update-AzVM
```

## <a name="how-a-credential-is-secured"></a>A hitelesítő adatok biztonságoslása

A kód futtatása hitelesítő adatokat kér. A hitelesítő adatok megadása után rövid ideig a memóriában tárolódik. Ha a hitelesítő adatok at a **Set-AzVMDscExtension** parancsmag használatával teszi közzé, a hitelesítő adatok HTTPS-en keresztül továbbítódnak a virtuális gépre. A virtuális gép, az Azure tárolja a hitelesítő adatokat a lemezen a helyi virtuális gép tanúsítvány használatával. A hitelesítő adatokat a rendszer rövid időre visszafejti a memóriában, majd újratitkosítja, hogy átadja a DSC-nek.

Ez a folyamat más, mint [a bővítménykezelő nélküli biztonságos konfigurációk használata.](/powershell/scripting/dsc/pull-server/securemof) Az Azure-környezet lehetővé teszi a konfigurációs adatok biztonságos továbbítását tanúsítványokon keresztül. A DSC-bővítménykezelő használatakor nem kell **$CertificatePath** vagy **$CertificateID**/ **$Thumbprint** a **ConfigurationData**alkalmazásban.

## <a name="next-steps"></a>További lépések

- Bevezetés [az Azure DSC-bővítménykezelőbe.](dsc-overview.md)
- Vizsgálja meg az [Azure Resource Manager sablont a DSC-bővítményhez.](dsc-template.md)
- A PowerShell DSC-ről további információt a [PowerShell dokumentációs központjában](/powershell/scripting/dsc/overview/overview)talál.
- A PowerShell DSC használatával kezelhető további funkciókért és további DSC-erőforrásokért keresse meg a [PowerShell-galériát.](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0)