---
title: "Windows 10 Azure több-Bérlős üzemeltető jogosultságokkal a központi telepítése"
description: "Útmutató a Windows Software Assurance előnyeit, hogy helyszíni licencek Azure maximalizálása"
services: virtual-machines-windows
documentationcenter: 
author: xujing
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 1/24/2018
ms.author: xujing
ms.openlocfilehash: 6ad3b294e1d53d03f6ceb61048c8f657d8b471c0
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2018
---
# <a name="how-to-deploy-windows-10-on-azure-with-multitenant-hosting-rights"></a>Windows 10 Azure több-Bérlős üzemeltető jogosultságokkal a központi telepítése 
Az ügyfelek a Windows 10 nagyvállalati E3/E5 egyes felhasználókra vagy a Windows virtuális asztal eléréséhez (bővítmény felhasználói előfizetési licencet vagy előfizetési licenceket) felhasználónként több-Bérlős üzemeltető Rights for Windows 10 lehetővé teszi a Windows 10 licencek kerüljön a felhőben és futtassa a Windows 10-alapú virtuális gépek Azure-on fizető egy másik licenc nélkül. További információkért lásd: [több-Bérlős üzemeltető Windows 10 rendszerhez készült](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx).

> [!NOTE]
> Ez a cikk bemutatja, hogy a Windows 10 Pro asztali képek licencelési juttatása megvalósítása az Azure piactéren.
> - A Windows 7, 8.1, az MSDN előfizetések Azure piactéren 10 Enterprise (x64) kép tekintse meg [Windows ügyfél az Azure-ban fejlesztési/Tesztelési forgatókönyvek](client-images.md)
> - A Windows Server licencelési előnyeit, tekintse meg [Azure hibrid előnyöket használata a Windows Server-lemezképekben](hybrid-use-benefit-licensing.md).
>

## <a name="deploying-windows-10-image-from-azure-marketplace"></a>Az Azure piactérről Windows 10-lemezkép központi telepítése 
Powershell, a parancssori felület és az Azure Resource Manager sablon központi telepítése esetén a Windows 10 kép a következő publishername, ajánlat, sku található.

| Operációs rendszer  |      Közzétevő neve      |  Ajánlat | SKU |
|:----------|:-------------:|:------|:------|
| Windows 10 Pro    | MicrosoftWindowsDesktop | Windows-10  | A Pro-RS2   |
| A Windows 10 Pro N  | MicrosoftWindowsDesktop | Windows-10  | RS2-ProN  |
| Windows 10 Pro    | MicrosoftWindowsDesktop | Windows-10  | RS3-Pro   |
| A Windows 10 Pro N  | MicrosoftWindowsDesktop | Windows-10  | RS3-ProN  |

## <a name="uploading-windows-10-vhd-to-azure"></a>Windows 10 feltöltése az Azure virtuális merevlemez
Ha Windows 10 általánosított virtuális Merevlemezt, vegye figyelembe a Windows 10 nem rendelkezik beépített rendszergazdai fiók alapértelmezés szerint engedélyezett. Ahhoz, hogy a beépített rendszergazdai fiók, tartalmazza a következő parancsot az egyéni parancsprogramok futtatására szolgáló bővítmény részeként.

```powershell
Net user <username> /active:yes
```

A következő powershell-részlet, hogy a rendszergazdai fiókoknak aktívnak, beleértve a beépített rendszergazda megjelölni. Ez a példa akkor hasznos, ha a beépített rendszergazdai felhasználónév érvénytelen.
```powershell
$adminAccount = Get-WmiObject Win32_UserAccount -filter "LocalAccount=True" | ? {$_.SID -Like "S-1-5-21-*-500"}
if($adminAccount.Disabled)
{
    $adminAccount.Disabled = $false
    $adminAccount.Put()
}
```
További információk: 
* [Az Azure virtuális merevlemez feltöltéséről](upload-generalized-managed.md)
* [Az Azure-bA feltölteni egy Windows virtuális merevlemez előkészítése](prepare-for-upload-vhd-image.md)


## <a name="deploying-windows-10-with-multitenant-hosting-rights"></a>Windows 10 több-Bérlős üzemeltetési jogosultságokkal központi telepítése
Győződjön meg arról, hogy [telepítette és konfigurálta a legújabb Azure PowerShell](/powershell/azure/overview). Miután előkészítette a VHD-t, a VHD-fájlt feltölti az Azure Storage segítségével a `Add-AzureRmVhd` parancsmag az alábbiak szerint:

```powershell
Add-AzureRmVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```


**Azure Resource Manager sablon-üzembehelyezés szabályzatsablonokat** belül a Resource Manager-sablonok, egy további paramétere `licenseType` adható meg. További tudnivalók [Azure Resource Manager sablonok készítése](../../resource-group-authoring-templates.md). Miután a virtuális merevlemez feltöltése az Azure-ba, szerkesztése, a licenc típusa a számítási szolgáltató részeként és a szokásos módon sablon üzembe helyezése a Resource Manager-sablon:
```json
"properties": {  
   "licenseType": "Windows_Client",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   }
```

**PowerShell telepítése** PowerShell a Windows Server virtuális Gépre való telepítése egy további paramétere van `-LicenseType`. Miután a virtuális merevlemez feltöltése az Azure-bA rendelkezik, hozzon létre egy virtuális gép használatával `New-AzureRmVM` és az alábbiak szerint adja meg a licencelési típusát:
```powershell
New-AzureRmVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Client"
```

## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>Ellenőrizze, hogy a virtuális Gépet a licencelési előnye van használata
A virtuális gép üzembe helyezett keresztül vagy a PowerShell vagy a Resource Manager üzembe helyezési módszer, ellenőrizze a licenc típusa a `Get-AzureRmVM` az alábbiak szerint:
```powershell
Get-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

A megfelelő licenc-típussal rendelkező Windows 10 rendszerhez az alábbi példához hasonló kimenete:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

Ezzel szemben a hagyományosabb kimeneti a a következő virtuális Géphez nem Azure hibrid használata juttatás licencelési, például a virtuális gépek közvetlenül az Azure katalógusából telepített:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="additional-information-about-joining-azure-ad"></a>További információk az Azure AD illesztése
>[!NOTE]
>Azure látja el az összes Windows virtuális gépek beépített rendszergazdai fiók, amely nem csatlakozik az aad-ben használható. Például *beállítások > fiók > hozzáférési munkahelyi vagy iskolai > + Connect* nem fog működni. Kell létrehozni, és jelentkezzen be az Azure AD mannually csatlakozni egy második rendszergazdai fiók. Beállíthatja úgy is üzembe helyezési csomag használata az Azure AD, a hivatkozás használata a *lépések* további szakasz.
>
>

## <a name="next-steps"></a>További lépések
- További információ [konfigurálása VDA Windows 10 rendszerhez](https://docs.microsoft.com/windows/deployment/vda-subscription-activation)
- További információ [több-Bérlős üzemeltető Windows 10 rendszerhez](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)


