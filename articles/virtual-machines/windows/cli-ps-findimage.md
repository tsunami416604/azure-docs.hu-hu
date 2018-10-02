---
title: Válassza ki a Windows VM-rendszerképek az Azure-ban |} A Microsoft Docs
description: Ismerje meg, hogyan határozza meg, a közzétevő, ajánlat, Termékváltozat és verzió Marketplace Virtuálisgép-rendszerképek Azure PowerShell használatával.
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 188b8974-fabd-4cd3-b7dc-559cbb86b98a
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/28/2018
ms.author: danlep
ms.openlocfilehash: 4fb718eb7247bddd8869b8973479377e3baebdda
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2018
ms.locfileid: "48017178"
---
# <a name="how-to-find-windows-vm-images-in-the-azure-marketplace-with-azure-powershell"></a>Az Azure Marketplace-en az Azure PowerShell használatával Windows Virtuálisgép-rendszerképek keresése

Ez a cikk ismerteti az Azure piactéren Virtuálisgép-rendszerképek keresése az Azure PowerShell használatával. Ezen információk használatával adja meg a Piactéri lemezképet, ha programozott módon létrehozhat egy virtuális Gépet a PowerShell-lel, Resource Manager-sablonok vagy más eszközök.

Elérhető rendszerképeket és a használatával ajánlatokkal keresse a [Azure Marketplace-en](https://azuremarketplace.microsoft.com/) kirakat, a [az Azure portal](https://portal.azure.com), vagy a [Azure CLI-vel](../linux/cli-ps-findimage.md). 

Ellenőrizze, hogy telepítve van, és a legújabb konfigurált [Azure PowerShell-modul](/powershell/azure/install-azurerm-ps).

[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]

## <a name="table-of-commonly-used-windows-images"></a>A gyakran használt Windows-rendszerképek táblája
| Közzétevő | Ajánlat | SKU |
|:--- |:--- |:--- |:--- |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter-Server-Core |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter-az-tárolók |
| MicrosoftWindowsServer |WindowsServer |2012-R2-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2012-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2008-R2-SP1 |
| MicrosoftDynamicsNAV |DynamicsNAV |2017 |
| MicrosoftSharePoint |MicrosoftSharePointServer |2016 |
| MicrosoftSQLServer |SQL2017-WS2016 |Enterprise |
| MicrosoftRServer |Az RServer-WS2016 |Enterprise |

## <a name="navigate-the-images"></a>Keresse meg a képek

Egy másik helyen megkeresni a rendszerképet módja futtatásához a [Get-AzureRMVMImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher), [Get-AzureRMVMImageOffer](/powershell/module/azurerm.compute/get-azurermvmimageoffer), és [Get-AzureRMVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku) parancsmagok a feladatütemezés. A következő parancsokkal határozza meg ezeket az értékeket:

1. Listázza a rendszerkép-közzétevőket.
2. Listázza egy adott közzétevő ajánlatait.
3. Listázza egy adott ajánlathoz tartozó termékváltozatokat.

Ezután a kiválasztott termékváltozat futtassa [Get-AzureRMVMImage](/powershell/module/azurerm.compute/get-azurermvmimage) üzembe helyezéséhez a verziók listázásához.

Először listázza a közzétevőket a következő parancsokkal:

```powershell
$locName="<Azure location, such as West US>"
Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName
```

Adja meg a kiválasztott közzétevő nevét, és futtassa a következő parancsokat:

```powershell
$pubName="<publisher>"
Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer
```

Adja meg a kiválasztott ajánlat nevét, és futtassa a következő parancsokat:

```powershell
$offerName="<offer>"
Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
```

Adja meg a kiválasztott Termékváltozat nevét, és futtassa a következő parancsokat:

```powershell
$skuName="<SKU>"
Get-AzureRMVMImage -Location $locName -Publisher $pubName -Offer $offerName -Sku $skuName | Select Version
```

Kimenetéből származó a `Get-AzureRMVMImage` parancsot, kiválaszthatja, hogy egy új virtuális gép üzembe helyezéséhez lemezképe.

A következő parancsok bemutatják egy teljes példa:

```powershell
$locName="West US"
Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName

```

Részleges kimenet:

```
PublisherName
-------------
...
a10networks
aiscaler-cache-control-ddos-and-url-rewriting-
alertlogic
AlertLogic.Extension
Barracuda.Azure.ConnectivityAgent
barracudanetworks
basho
boxless
bssw
Canonical
...
```

Az a *MicrosoftWindowsServer* közzétevő:

```powershell
$pubName="MicrosoftWindowsServer"
Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer
```

Kimenet:

```
Offer
-----
Windows-HUB
WindowsServer
WindowsServerSemiAnnual
```

Az a *WindowsServer* kínálnak:

```powershell
$offerName="WindowsServer"
Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
```

Kimenet:

```
Skus
----
2008-R2-SP1
2008-R2-SP1-smalldisk
2012-Datacenter
2012-Datacenter-smalldisk
2012-R2-Datacenter
2012-R2-Datacenter-smalldisk
2016-Datacenter
2016-Datacenter-Server-Core
2016-Datacenter-Server-Core-smalldisk
2016-Datacenter-smalldisk
2016-Datacenter-with-Containers
2016-Datacenter-with-RDSH
```

Ezután a *2016 Datacenter* Termékváltozat:

```powershell
$skuName="2016-Datacenter"
Get-AzureRMVMImage -Location $locName -Publisher $pubName -Offer $offerName -Sku $skuName | Select Version
```

Most a kijelölt közzétevő, ajánlat, Termékváltozat és verzió URN kombinálható (a következővel határolt értékekkel:). Ezt az URN adja át a `--image` paraméterrel rendelkező virtuális gép létrehozásakor a [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) parancsmag. Ne feledje, hogy igény szerint lecserélheti a verziószámot a URN "legutóbbi". Ez a verzió, mindig a rendszerkép legújabb verzióját.

Ha telepít egy virtuális Gépet a Resource Manager-sablonnal, egyenként a állítsa be a rendszerkép-paraméterek a `imageReference` tulajdonságait. Tekintse meg a [sablonreferenciája](/azure/templates/microsoft.compute/virtualmachines).

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]

### <a name="view-plan-properties"></a>Csomag tulajdonságainak megtekintése

Egy rendszerkép beszerzési terv adatainak megtekintéséhez futtassa a `Get-AzureRMVMImage` parancsmagot. Ha a `PurchasePlan` a kimenetben tulajdonság nem `null`, a rendszerképre feltételeket el kell fogadnia a programozott telepítés előtt.  

Ha például a Windows Server 2016 Datacenter rendszerképet nem rendelkezik további feltételeket, mert a `PurchasePlan` információk `null`:

```powershell
$version = "2016.127.20170406"
Get-AzureRMVMImage -Location $locName -Publisher $pubName -Offer $offerName -Skus $skuName -Version $version
```

Kimenet:

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/
                   Versions/2016.127.20170406
Location         : westus
PublisherName    : MicrosoftWindowsServer
Offer            : WindowsServer
Skus             : 2016-Datacenter
Version          : 2016.127.20170406
FilterExpression :
Name             : 2016.127.20170406
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : null
DataDiskImages   : []

```

Egy hasonló parancs futtatása a az adatelemző virtuális gép – Windows 2016 kép mutatja be a következő `PurchasePlan` tulajdonságai: `name`, `product`, és `publisher`. (Egyes képeket is, hogy egy `promotion code` tulajdonság.) A lemezkép központi telepítése, lásd a következő szakaszok fogadja el a feltételeket, és engedélyezze a programozott telepítés.

```powershell
Get-AzureRMVMImage -Location "westus" -Publisher "microsoft-ads" -Offer "windows-data-science-vm" -Skus "windows2016" -Version "0.2.02"
```

Kimenet:

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/microsoft-ads/ArtifactTypes/VMIma
                   ge/Offers/windows-data-science-vm/Skus/windows2016/Versions/0.2.02
Location         : westus
PublisherName    : microsoft-ads
Offer            : windows-data-science-vm
Skus             : windows2016
Version          : 0.2.02
FilterExpression :
Name             : 0.2.02
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : {
                     "publisher": "microsoft-ads",
                     "name": "windows2016",
                     "product": "windows-data-science-vm"
                   }
DataDiskImages   : []

```

### <a name="accept-the-terms"></a>Feltételek elfogadása

A licencszerződés megtekintéséhez használja a [Get-AzureRmMarketplaceterms](/powershell/module/azurerm.marketplaceordering/get-azurermmarketplaceterms) parancsmagot, és adja meg a vásárlás megtervezése paramétereket. A kimenet egy hivatkozást kínál a feltételeket, a Piactéri lemezképhez a, és bemutatja, hogy Ön korábban elfogadta a használati. Győződjön meg arról, csak kisbetűk használata a paraméterértékek megadásához. Példa:

```powershell
Get-AzureRmMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"
```

Kimenet:

```
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DV
                    M%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : 2UMWH6PHSAIM4U22HXPXW25AL2NHUJ7Y7GRV27EBL6SUIDURGMYG6IIDO3P47FFIBBDFHZHSQTR7PNK6VIIRYJRQ3WXSE6BTNUNENXA
Accepted          : False
Signdate          : 2/23/2018 7:43:00 PM
```

Használja a [Set-AzureRmMarketplaceterms](/powershell/module/azurerm.marketplaceordering/set-azurermmarketplaceterms) parancsmag elfogadja vagy elutasítja a feltételeket. Csak egyszer előfizetésenként elfogadására a lemezképhez kell. Győződjön meg arról, csak kisbetűk használata a paraméterértékek megadásához. Példa:

```powershell

$agreementTerms=Get-AzureRmMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"

Set-AzureRmMarketplaceTerms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016" -Terms $agreementTerms -Accept

```

Kimenet:

```
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DV
                    M%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : XXXXXXK3MNJ5SROEG2BYDA2YGECU33GXTD3UFPLPC4BAVKAUL3PDYL3KBKBLG4ZCDJZVNSA7KJWTGMDSYDD6KRLV3LV274DLBXXXXXX
Accepted          : True
Signdate          : 2/23/2018 7:49:31 PM
```

### <a name="deploy-using-purchase-plan-parameters"></a>Üzembe helyezés beszerzési terv paraméterei

Miután elfogadja a feltételeket a lemezkép, az előfizetésben található virtuális gép is telepítheti. Ahogy az alábbi kódrészletben látható módon, a [Set-AzureRmVMPlan](/powershell/module/azurerm.compute/set-azurermvmplan) -parancsmaggal beállítható a Marketplace-en csomagadatokat a VM-objektumhoz. Teljes szkript létrehozása a virtuális gép hálózati beállításait, és a telepítés befejezéséhez, lásd: a [PowerShell-példaszkriptek](powershell-samples.md).

```powershell
...

$vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize Standard_D1

# Set the Marketplace plan information

$publisherName = "microsoft-ads"

$productName = "windows-data-science-vm"

$planName = "windows2016"

$vmConfig = Set-AzureRmVMPlan -VM $vmConfig -Publisher $publisherName -Product $productName -Name $planName

$cred=Get-Credential

$vmConfig = Set-AzureRmVMOperatingSystem -Windows -VM $vmConfig -ComputerName "myVM" -Credential $cred

# Set the Marketplace image

$offerName = "windows-data-science-vm"

$skuName = "windows2016"

$version = "0.2.02"

$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName $publisherName -Offer $offerName -Skus $skuName -Version $version
...
```
Akkor továbbítja a Virtuálisgép-konfigurációt és hálózati konfigurációs objektumot a `New-AzureRmVM` parancsmagot.

## <a name="next-steps"></a>További lépések
A virtuális gép gyors létrehozása `New-AzureRmVM` alapvető információk alapján tekintse meg a [Windows virtuális gép létrehozása a PowerShell-lel](quick-create-powershell.md).

Tekintse meg a PowerShell-példaszkript [teljes konfigurációjú virtuális gép létrehozása](../scripts/virtual-machines-windows-powershell-sample-create-vm.md).


