---
title: "Hitelesítő adatok továbbításához a DSC használata Azure-bA |} Microsoft Docs"
description: "A biztonságos hitelesítő adatok átadni PowerShell célállapot-konfiguráció használata az Azure virtuális gépek – áttekintés"
services: virtual-machines-windows
documentationcenter: 
author: zjalexander
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
keywords: 
ms.assetid: ea76b7e8-b576-445a-8107-88ea2f3876b9
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 09/15/2016
ms.author: zachal
ms.openlocfilehash: acd768c0219ec23c0453a65c575faf5213d9c616
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="passing-credentials-to-the-azure-dsc-extension-handler"></a>Hitelesítő adatok átadni a Azure DSC-kiterjesztés kezelője
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Ez a cikk az Azure a célállapot-konfiguráció bővítményt tartalmazza. A DSC-kiterjesztés kezelője áttekintést található [bemutatása az Azure célállapot-konfiguráció bővítmény kezelő](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

## <a name="passing-in-credentials"></a>A hitelesítő adatok sikeres
A konfigurációs folyamat részeként, akkor előfordulhat, hogy kell beállítása a felhasználói fiókok, szolgáltatások elérésére és a program telepítése a felhasználói környezetben. Mindezek, meg kell adnia a hitelesítő adatokat. 

A DSC lehetővé teszi a paraméteres konfigurációk, ahol hitelesítő adatokat a konfiguráció lett átadva, és biztonságos módon tárolja az MOF-fájlok. Az Azure-bővítmény kezelő egyszerűbbé teszi a hitelesítőadat-kezelés, adja meg a tanúsítványok automatikus kezelését. 

Vegye figyelembe a következő DSC konfigurációs parancsfájl, amely létrehoz egy helyi felhasználói fiókot a megadott jelszóval:

*user_configuration.ps1*

```
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

Fontos, hogy tartalmazzák *csomópont localhost* a konfigurálás részeként. Ha a jelen nyilatkozat hiányzik, az alábbi lépéseket nem működik, a bővítmény kezelő kifejezetten keresi a csomópont localhost utasítást. Szintén fontos, hogy tartalmazzák a typecast *[PsCredential]*, mert az adott típusú váltja ki a bővítményt a hitelesítő adatok titkosításához. 

Ez a parancsfájl közzététele a blob storage:

`Publish-AzureVMDscConfiguration -ConfigurationPath .\user_configuration.ps1`

Állítsa be az Azure DSC-bővítményt, és adja meg a hitelesítő adat:

```
$configurationName = "Main"
$configurationArguments = @{ Credential = Get-Credential }
$configurationArchive = "user_configuration.ps1.zip"
$vm = Get-AzureVM "example-1"

$vm = Set-AzureVMDSCExtension -VM $vm -ConfigurationArchive $configurationArchive 
-ConfigurationName $configurationName -ConfigurationArgument @configurationArguments

$vm | Update-AzureVM
```
## <a name="how-credentials-are-secured"></a>Hitelesítő adatok titkosításának módját
Ezt a kódot futtató hitelesítő adatokat kér. Amennyiben azt biztosítja, tárolódik a memóriában rövid időre. Ha közzé van téve a `Set-AzureVmDscExtension` parancsmag, azt a virtuális gép HTTPS-KAPCSOLATON keresztül továbbított, ahol az Azure tárolja a lemezen, a helyi virtuális gép tanúsítvány használatával titkosítja. Majd röviden visszafejti a memóriában és DSC átadandó újra titkosítja.

Ez a viselkedés eltér [nélkül a bővítmény kezelő biztonságos konfigurációk használatával](https://msdn.microsoft.com/powershell/dsc/securemof). Az Azure környezetbe lehetőséget nyújt az tanúsítványok segítségével biztonságosan konfigurációs adatok átviteléhez. A DSC-bővítmény kezelő használata esetén nincs szükség az $CertificatePath vagy egy $CertificateID / ConfigurationData $Thumbprint bejegyzést.

## <a name="next-steps"></a>Következő lépések
Az Azure DSC-bővítmény kezelő további információkért lásd: [bemutatása az Azure célállapot-konfiguráció bővítmény kezelő](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Vizsgálja meg a [Azure Resource Manager sablon a DSC-bővítmény](extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

További információ a PowerShell DSC [látogasson el a PowerShell dokumentációs központban](https://msdn.microsoft.com/powershell/dsc/overview). 

A PowerShell DSC kezelhető további funkciók kereséséhez [keresse meg a PowerShell-galériában](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0) további DSC-erőforrásokat.

