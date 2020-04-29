---
title: Windows 10 üzembe helyezése az Azure-ban több-bérlős üzemeltetési jogosultságokkal
description: Ismerje meg, hogyan maximalizálhatja a Windows-frissítési garanciát a helyszíni licencek Azure-ba való bekapcsolásához
author: xujing
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 1/24/2018
ms.author: xujing
ms.openlocfilehash: c85eef1a5d035e23c7e63632ac92c21440b15cae
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82101552"
---
# <a name="how-to-deploy-windows-10-on-azure-with-multitenant-hosting-rights"></a>Windows 10 üzembe helyezése az Azure-ban több-bérlős üzemeltetési jogosultságokkal 
A Windows 10 Enterprise E3/E5 felhasználónkénti vagy a Windows virtuális asztali hozzáférés felhasználónként (felhasználói előfizetési licencek vagy kiegészítő felhasználói előfizetési licencek) rendelkező ügyfelei számára a Windows 10 rendszerhez készült több-bérlős üzemeltetési jogosultság lehetővé teszi a Windows 10-es licencek felhőbe való bevezetését, valamint a Windows 10 Virtual Machines Azure-beli futtatását anélkül, hogy más licenccel kellene fizetnie. További információ: több- [bérlős üzemeltetés a Windows 10 rendszerhez](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx).

> [!NOTE]
> Ebből a cikkből megtudhatja, hogyan valósítja meg a Windows 10 Pro asztali rendszerképek licencelési előnyeit az Azure Marketplace-en.
> - Windows 7, 8,1, 10 Enterprise (x64) rendszerképek az Azure Marketplace-en MSDN-előfizetések esetén: [fejlesztői és tesztelési forgatókönyvek az Azure-beli Windows-ügyfélen](client-images.md)
> - A Windows Server licencelési előnyeinek kihasználásához tekintse meg a [Windows Server rendszerképekhez készült Azure Hybrid use Benefits](hybrid-use-benefit-licensing.md)című témakört.
>

## <a name="deploying-windows-10-image-from-azure-marketplace"></a>Windows 10 rendszerkép üzembe helyezése az Azure Marketplace-en 
A PowerShell, a parancssori felület és a Azure Resource Manager sablon üzembe helyezése esetén a Windows 10-es rendszerkép a következő közzétevő neve, ajánlattal, SKU-val érhető el.

| Operációs rendszer  |      Közzétevő neve      |  Ajánlat | SKU |
|:----------|:-------------:|:------|:------|
| Windows 10 Pro    | MicrosoftWindowsDesktop | Windows-10  | RS2 – Pro   |
| Windows 10 Pro N  | MicrosoftWindowsDesktop | Windows-10  | RS2 – elterült  |
| Windows 10 Pro    | MicrosoftWindowsDesktop | Windows-10  | RS3 – Pro   |
| Windows 10 Pro N  | MicrosoftWindowsDesktop | Windows-10  | RS3 – elterült  |

## <a name="uploading-windows-10-vhd-to-azure"></a>Windows 10 virtuális merevlemez feltöltése az Azure-ba
Ha általánosított Windows 10 virtuális merevlemezt tölt fel, vegye figyelembe, hogy a Windows 10 alapértelmezés szerint nincs engedélyezve a beépített rendszergazdai fiókkal. A beépített rendszergazdai fiók engedélyezéséhez a következő parancsot adja meg az egyéni szkriptek bővítményének részeként.

```powershell
Net user <username> /active:yes
```

A következő PowerShell-kódrészlet az összes rendszergazdai fiók aktívként való megjelölése, beleértve a beépített rendszergazdát is. Ez a példa akkor hasznos, ha a beépített rendszergazda felhasználóneve ismeretlen.
```powershell
$adminAccount = Get-WmiObject Win32_UserAccount -filter "LocalAccount=True" | ? {$_.SID -Like "S-1-5-21-*-500"}
if($adminAccount.Disabled)
{
    $adminAccount.Disabled = $false
    $adminAccount.Put()
}
```
További információk: 
* [Virtuális merevlemez feltöltése az Azure-ba](upload-generalized-managed.md)
* [Windows rendszerű virtuális merevlemez előkészítése az Azure-ba való feltöltésre](prepare-for-upload-vhd-image.md)


## <a name="deploying-windows-10-with-multitenant-hosting-rights"></a>A Windows 10 telepítése több-bérlős üzemeltetési jogosultságokkal
Győződjön meg arról, hogy [telepítette és konfigurálta a legújabb Azure PowerShell](/powershell/azure/overview). Miután előkészítette a VHD-t, töltse fel a virtuális merevlemezt az Azure `Add-AzVhd` Storage-fiókjába az alábbi parancsmaggal:

```powershell
Add-AzVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```


**Üzembe helyezés Azure Resource Manager sablon használatával** A Resource Manager-sablonokon belül egy további paraméter `licenseType` is megadható. További információ a [Azure Resource Manager sablonok létrehozásáról](../../resource-group-authoring-templates.md). Miután feltöltötte a VHD-t az Azure-ba, szerkessze a Resource Manager-sablont, hogy tartalmazza a licenc típusát a számítási szolgáltató részeként, és telepítse a sablont a szokásos módon:
```json
"properties": {
    "licenseType": "Windows_Client",
    "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
    }
```

**Üzembe helyezés a PowerShell** használatával A Windows Server rendszerű virtuális gép PowerShell használatával történő telepítésekor további paramétert is megadhat `-LicenseType`. Miután feltöltötte a VHD-t az Azure-ba, létrehoz `New-AzVM` egy virtuális gépet a használatával, és megadja a licencelés típusát a következő módon:
```powershell
New-AzVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Client"
```

## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>Annak ellenőrzése, hogy a virtuális gép használja-e a licencelési kedvezményt
Miután telepítette a virtuális gépet a PowerShell vagy a Resource Manager üzembe helyezési módszerével, ellenőrizze a licenc típusát `Get-AzVM` a következő módon:
```powershell
Get-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

A kimenet a következő példához hasonló a Windows 10-es megfelelő licenccel:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

Ez a kimenet ellentétben áll a következő, Azure Hybrid Use Benefit licencelés nélkül üzembe helyezett virtuális géppel, például egy közvetlenül az Azure-katalógusból üzembe helyezett virtuális géppel:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="additional-information-about-joining-azure-ad"></a>További információ az Azure AD-hez való csatlakozásról
>[!NOTE]
>Az Azure minden olyan Windowsos virtuális gépet kiépít, amely beépített rendszergazdai fiókkal rendelkezik, ezért nem használható a HRE való csatlakozáshoz. Például a *beállítások > a fiók > hozzáférés munkahelyi vagy iskolai > + kapcsolódás* nem fog működni. Az Azure AD-hez való csatlakozáshoz második rendszergazdai fiókot kell létrehoznia és bejelentkeznie. Az Azure AD-t egy kiépítési csomaggal is konfigurálhatja, ha további információra van szüksége, használja a *következő lépések* szakaszt.
>
>

## <a name="next-steps"></a>Következő lépések
- További információ a [Windows 10 rendszerhez készült VDA konfigurálásáról](https://docs.microsoft.com/windows/deployment/vda-subscription-activation)
- További információ a [Windows 10 rendszerhez készült több-bérlős üzemeltetésről](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)


