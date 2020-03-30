---
title: A Windows 10 telepítése az Azure-ban több-bérlős üzemeltetési jogokkal
description: Megtudhatja, hogy miként maximalizálhatja a Windows frissítési garanciával kapcsolatos előnyeit, hogy helyszíni licenceket hozhasson az Azure-ba
services: virtual-machines-windows
documentationcenter: ''
author: xujing
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 1/24/2018
ms.author: xujing
ms.openlocfilehash: 9ff8cc64266375a2d439763b222870843136f67a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70101500"
---
# <a name="how-to-deploy-windows-10-on-azure-with-multitenant-hosting-rights"></a>A Windows 10 telepítése az Azure-ban több-bérlős üzemeltetési jogokkal 
A Windows 10 Enterprise E3/E5 rendszerrel felhasználónként i vagy Windows Virtual Desktop Access felhasználónkénti (felhasználói előfizetési licencek vagy bővítményfelhasználói-előfizetési licencek) rendelkező ügyfelek számára a Windows 10 több-bérlős üzemeltetési jogai lehetővé teszik, hogy windows 10-es licenceit a felhőbe hozza, és a Windows 10 virtuális gépeket az Azure-ban anélkül futtatja, hogy másik licencért fizetne. További információ: [Több-bérlős tárhely a Windows 10-hez.](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)

> [!NOTE]
> Ez a cikk bemutatja, hogy valósítsa meg a licencelési előnyt a Windows 10 Pro asztali rendszerképek az Azure Marketplace-en.
> - Windows 7, 8.1, 10 Enterprise (x64) rendszerképek esetén az Azure Piactéren az MSDN-előfizetésekhez, tekintse meg [a Windows-ügyfél az Azure-ban a fejlesztési és tesztelési forgatókönyveket](client-images.md)
> - A Windows Server licencelési előnyeiről az [Azure Hybrid Windows Server-lemezképekhez való használatának előnyeit](hybrid-use-benefit-licensing.md)olvassa el.
>

## <a name="deploying-windows-10-image-from-azure-marketplace"></a>A Windows 10-lemezkép telepítése az Azure Piactérről 
A Powershell, cli és az Azure Resource Manager sablon telepítések, a Windows 10-lemezkép megtalálható a következő közzétevőnév, ajánlat, sku.

| Operációs rendszer  |      Közzétevő neve      |  Ajánlat | SKU |
|:----------|:-------------:|:------|:------|
| Windows 10 Pro    | MicrosoftWindowsDesktop | Windows-10 rendszerben  | RS2-Pro   |
| Windows 10 Pro N  | MicrosoftWindowsDesktop | Windows-10 rendszerben  | RS2-ProN  |
| Windows 10 Pro    | MicrosoftWindowsDesktop | Windows-10 rendszerben  | RS3-Pro   |
| Windows 10 Pro N  | MicrosoftWindowsDesktop | Windows-10 rendszerben  | RS3-ProN  |

## <a name="uploading-windows-10-vhd-to-azure"></a>Windows 10 virtuális merevlemez feltöltése az Azure-ba
ha általános Windows 10 vhd-t tölt fel, kérjük, vegye figyelembe, hogy a Windows 10 alapértelmezés szerint nem rendelkezik beépített rendszergazdai fiókkal. A beépített rendszergazdai fiók engedélyezéséhez vegye fel a következő parancsot az egyéni parancsfájl-bővítménybe.

```powershell
Net user <username> /active:yes
```

A következő powershell-kódrészlet az összes rendszergazdai fiók aktívként való megjelölése, beleértve a beépített rendszergazdát is. Ez a példa akkor hasznos, ha a beépített rendszergazdai felhasználónév ismeretlen.
```powershell
$adminAccount = Get-WmiObject Win32_UserAccount -filter "LocalAccount=True" | ? {$_.SID -Like "S-1-5-21-*-500"}
if($adminAccount.Disabled)
{
    $adminAccount.Disabled = $false
    $adminAccount.Put()
}
```
További információk: 
* [A virtuális merevlemez feltöltése az Azure-ba](upload-generalized-managed.md)
* [Windows VHD előkészítése az Azure-ba való feltöltéshez](prepare-for-upload-vhd-image.md)


## <a name="deploying-windows-10-with-multitenant-hosting-rights"></a>A Windows 10 telepítése több-bérlős üzemeltetési jogokkal
Győződjön meg arról, hogy [telepítette és konfigurálta a legújabb Azure PowerShellt.](/powershell/azure/overview) Miután elkészítette a virtuális merevlemezt, töltse fel a `Add-AzVhd` virtuális merevlemezt az Azure Storage-fiókba a parancsmag használatával az alábbiak szerint:

```powershell
Add-AzVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```


**Üzembe helyezés az Azure Resource Manager-sablon telepítésével** Az Erőforrás-kezelő sablonokon belül `licenseType` egy további paraméter is megadható. Az [Azure Resource Manager-sablonok szerzőiről olvashat bővebben.](../../resource-group-authoring-templates.md) Miután feltöltötte a virtuális merevlemezt az Azure-ba, szerkesztheti a Resource Manager-sablont, hogy az tartalmazza a licenctípust a számítási szolgáltató részeként, és telepítse a sablont a szokásos módon:
```json
"properties": {
    "licenseType": "Windows_Client",
    "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
    }
```

**Üzembe helyezés a PowerShellen keresztül** A Windows Server virtuális gép PowerShellen keresztüli telepítésekor rendelkezik egy további paraméterrel. `-LicenseType` Miután feltöltötte a virtuális merevlemezt az Azure-ba, virtuális gépet hoz létre a licencelés im-je használatával, `New-AzVM` és a következőképpen adhatja meg a licencelés típusát:
```powershell
New-AzVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Client"
```

## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>Ellenőrizze, hogy a virtuális gép használja-e a licencelési előnyt
Miután üzembe helyezte a virtuális gépet a PowerShell vagy az Erőforrás-kezelő telepítési módszerén keresztül, ellenőrizze a licenctípusát az `Get-AzVM` alábbiak szerint:
```powershell
Get-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

A kimenet hasonló a következő példához a megfelelő licenctípussal rendelkező Windows 10 esetében:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

Ez a kimenet ellentétben áll az Azure Hybrid Use Benefit licencelés e nélkül üzembe helyezett következő virtuális gépekkel, például egy közvetlenül az Azure Gallery-ből telepített virtuális géptel:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="additional-information-about-joining-azure-ad"></a>További információ az Azure AD-hez való csatlakozásról
>[!NOTE]
>Az Azure minden beépített rendszergazdai fiókkal rendelkező Windows-virtuális gépet tartalmaz, amely nem használható az AAD-hez való csatlakozáshoz. Például *a Beállítások > a fiók > az Access Work vagy az Iskolai > +Connect* nem fog működni. Létre kell hoznia, és jelentkezzen be, mint egy második rendszergazdai fiók, hogy manuálisan csatlakozzon az Azure AD-hez. Az Azure AD-t is konfigurálhatja egy kiépítési csomag használatával, használja a hivatkozást a *Következő lépések* szakaszban, hogy további.
>
>

## <a name="next-steps"></a>Következő lépések
- További információ [a VDA windows 10-es konfigurálásáról](https://docs.microsoft.com/windows/deployment/vda-subscription-activation)
- További információ [a Windows 10 többbérlős üzemeltetéséről](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)


