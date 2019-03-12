---
title: Az Azure-ban több-Bérlős üzemeltető jogosultságokkal rendelkező Windows 10-es üzembe helyezése
description: Ismerje meg, hogyan maximalizálhatja a Windows-frissítési garancia arra, hogy a helyszíni-licenceiket áthozzák az Azure-bA
services: virtual-machines-windows
documentationcenter: ''
author: xujing
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 1/24/2018
ms.author: xujing
ms.openlocfilehash: 7f43528c55cd22c2649ca0f1208da6f41695b98e
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57569977"
---
# <a name="how-to-deploy-windows-10-on-azure-with-multitenant-hosting-rights"></a>Az Azure-ban több-Bérlős üzemeltető jogosultságokkal rendelkező Windows 10-es üzembe helyezése 
Ügyfelek számára a Windows 10 Enterprise E3 és E5 felhasználó vagy a Windows virtuális asztali hozzáférés / felhasználónként (felhasználói előfizetési licenccel vagy felhasználói előfizetés licencek) több-Bérlős üzemeltető Rights for Windows 10 lehetővé teszi a felhőbe a Windows 10-licencek használata és Windows 10-es virtuális gépek futtatása az Azure-ban anélkül egy másik licenc. További információkért tekintse meg [több-Bérlős üzemeltető Windows 10-es](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx).

> [!NOTE]
> Ez a cikk bemutatja, hogy a licencek előnyeit a Windows 10 Pro asztali rendszerképek megvalósítása az Azure Marketplace-en.
> - Windows 7, 8.1, 10 Enterprise (x64) rendszerképek az Azure piactéren, az MSDN-előfizetések, tekintse meg [Windows ügyfél az Azure-ban fejlesztési/tesztelési célra](client-images.md)
> - A Windows Server licencelési előnyeit, tekintse meg [Azure hibrid előnyöket használata a Windows Server-rendszerképeket](hybrid-use-benefit-licensing.md).
>

## <a name="deploying-windows-10-image-from-azure-marketplace"></a>Az Azure Marketplace-ről Windows 10-lemezkép központi telepítése 
A Powershell, CLI és az Azure Resource Manager sablon-üzembehelyezések a Windows 10-es kép található a következő közzétevő neve, ajánlat, termékváltozat.

| Operációs rendszer  |      Közzétevő neve      |  Ajánlat | SKU |
|:----------|:-------------:|:------|:------|
| Windows 10 Pro    | MicrosoftWindowsDesktop | Windows-10  | RS2-Pro   |
| Windows 10 Pro N  | MicrosoftWindowsDesktop | Windows-10  | RS2-ProN  |
| Windows 10 Pro    | MicrosoftWindowsDesktop | Windows-10  | RS3-Pro   |
| Windows 10 Pro N  | MicrosoftWindowsDesktop | Windows-10  | RS3-ProN  |

## <a name="uploading-windows-10-vhd-to-azure"></a>A Windows 10-es feltöltése VHD-t az Azure-bA
Ha a Windows 10-es általánosított virtuális Merevlemezt tölt fel, vegye figyelembe a Windows 10-es nem rendelkezik beépített rendszergazdai fiók alapértelmezés szerint engedélyezve van. Ahhoz, hogy a beépített rendszergazdai fiók, a következő parancsot az egyéni szkriptek futtatására szolgáló bővítmény részeként tartalmazza.

```powershell
Net user <username> /active:yes
```

Az alábbi powershell-kódrészlettel, hogy minden rendszergazdai fiókok aktívként, beleértve a beépített rendszergazda megjelölni. Ebben a példában akkor hasznos, ha a beépített rendszergazdai jogosultságú felhasználónevet ismeretlen.
```powershell
$adminAccount = Get-WmiObject Win32_UserAccount -filter "LocalAccount=True" | ? {$_.SID -Like "S-1-5-21-*-500"}
if($adminAccount.Disabled)
{
    $adminAccount.Disabled = $false
    $adminAccount.Put()
}
```
További információk: 
* [VHD feltöltése az Azure-bA](upload-generalized-managed.md)
* [Töltse fel az Azure Windows virtuális merevlemez előkészítése](prepare-for-upload-vhd-image.md)


## <a name="deploying-windows-10-with-multitenant-hosting-rights"></a>Windows 10-es többvállalatos üzemeltetési jogosultságok a központi telepítése
Ellenőrizze, hogy [telepítette és konfigurálta az Azure PowerShell legújabb verzióját](/powershell/azure/overview). Miután előkészítette a VHD-t, a virtuális merevlemez feltöltése az Azure Storage fiók használatával a `Add-AzVhd` parancsmag az alábbiak szerint:

```powershell
Add-AzVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```


**Üzembe helyezés Azure Resource Manager-sablon telepítése** belül a Resource Manager-sablonok, egy további paraméter `licenseType` adható meg. További információ [Azure Resource Manager-sablonok készítése](../../resource-group-authoring-templates.md). Miután a VHD-t az Azure-bA feltöltött, szerkesztése, Resource Manager-sablont a licenc típusa részét képező a számítási szolgáltató, és helyezze üzembe a sablont a szokásos módon:
```json
"properties": {
    "licenseType": "Windows_Client",
    "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
    }
```

**Üzembe helyezés a PowerShell** Powershellen keresztül a Windows Server virtuális gép üzembe helyezésekor, egy további paramétere rendelkezik `-LicenseType`. Miután a VHD-t az Azure-bA feltöltött, létrehozhat egy virtuális gépet `New-AzVM` , és adja meg a licencelési típusa a következők szerint:
```powershell
New-AzVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Client"
```

## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>Ellenőrizze, hogy a virtuális gép a licencek előnyeit okból rendszerbetöltést végrehajtani
Miután telepítette a virtuális gép vagy a PowerShell vagy a Resource Manager üzembe helyezési módszerrel, ellenőrizze a licenc típusa a `Get-AzVM` módon:
```powershell
Get-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

A kimenet a megfelelő licenccel típusú Windows 10-es az alábbi példához hasonló:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

Ez a kimeneti hagyományosabb és a következő virtuális gép nem az Azure Hybrid Use Benefit licencelés, például az Azure katalógusából közvetlenül telepített virtuális Gépen:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="additional-information-about-joining-azure-ad"></a>Azure ad-ben való csatlakoztatásával kapcsolatos további információk
>[!NOTE]
>Az Azure beépített rendszergazdai fiók, amely nem használható AAD csatlakozni az összes Windows virtuális gép kiépítése. Például *beállítások > fiók > hozzáférés munkahelyi vagy iskolai rendszerhez > + Connect* nem fog működni. Kell létrehozni, és jelentkezzen be Azure ad-ben manuálisan csatlakozni egy második rendszergazdai fiókkal. Azure AD-bA a kiépítési csomagot is beállítható, a hivatkozás használata a *lépések* szakaszban talál.
>
>

## <a name="next-steps"></a>További lépések
- Tudjon meg többet [konfigurálása VDA Windows 10-es](https://docs.microsoft.com/windows/deployment/vda-subscription-activation)
- Tudjon meg többet [több-Bérlős üzemeltető Windows 10-es](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)


