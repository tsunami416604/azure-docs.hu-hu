---
title: Windows Virtuálisgép-rendszerképek kiválasztása az Azure-ban |} Microsoft Docs
description: Megtudhatja, hogyan használhatja az Azure Powershellt közzétevő, az ajánlat, SKU, és verziója a piactér Virtuálisgép-rendszerképek meghatározásához.
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
ms.date: 02/28/2018
ms.author: danlep
ms.openlocfilehash: 885ee10bc63b65d936f5b433a18c4435b2503720
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2018
---
# <a name="how-to-find-windows-vm-images-in-the-azure-marketplace-with-azure-powershell"></a>Az Azure piactéren az Azure PowerShell Windows Virtuálisgép-lemezképek megkeresése

A cikkből megtudhatja, hogyan használható az Azure PowerShell található Virtuálisgép-rendszerképek az Azure piactéren. Ezen információk használatával adja meg a Piactéri rendszerkép létrehozásakor a virtuális gépek programozott módon PowerShell, a Resource Manager-sablonok vagy más eszközökkel.

Győződjön meg arról, hogy telepített és konfigurált legújabb [Azure PowerShell modul](/powershell/azure/install-azurerm-ps).

[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]

## <a name="table-of-commonly-used-windows-images"></a>A gyakran használt Windows-lemezképek tábla
| Kiadó | Ajánlat | Termékváltozat |
|:--- |:--- |:--- |:--- |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter-Server-Core |
| MicrosoftWindowsServer |WindowsServer |2016-adatközpont-az-tárolók |
| MicrosoftWindowsServer |WindowsServer |2016-Nano-Server |
| MicrosoftWindowsServer |WindowsServer |2012-R2-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2008-R2-SP1 |
| MicrosoftDynamicsNAV |DynamicsNAV |2017 |
| MicrosoftSharePoint |MicrosoftSharePointServer |2016 |
| MicrosoftSQLServer |SQL2014SP2-WS2012R2 |Vállalati |
| MicrosoftWindowsServerHPCPack |WindowsServerHPCPack |2012R2 |
| MicrosoftWindowsServerEssentials |WindowsServerEssentials |WindowsServerEssentials |

## <a name="navigate-the-images"></a>Lépjen a lemezképek

Lemezkép a helyen található egy másik módszer a [Get-AzureRMVMImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher), [Get-AzureRMVMImageOffer](/powershell/module/azurerm.compute/get-azurermvmimageoffer), és [Get-AzureRMVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku) parancsmagok sorrendben. A következő parancsokkal ezek az értékek határozzák meg:

1. Listázza a rendszerkép-közzétevőket.
2. Listázza egy adott közzétevő ajánlatait.
3. Listázza egy adott ajánlathoz tartozó termékváltozatokat.

Ezután a kiválasztott termékváltozat futtassa [Get-AzureRMVMImage](/powershell/module/azurerm.compute/get-azurermvmimage) telepítendő verziók listáját.

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

Adja meg a kiválasztott termékváltozat, és futtassa a következő parancsokat:

```powershell
$skuName="<SKU>"
Get-AzureRMVMImage -Location $locName -Publisher $pubName -Offer $offerName -Sku $skuName | Select Version
```

A kimenetét a `Get-AzureRMVMImage` parancsban, kiválaszthatja, hogy egy központi telepítése egy új virtuális gép lemezképe.

A következő parancsokat egy teljes példa megjelenítése:

```powershell
$locName="West US"
Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName

```

Kimenet:

```
PublisherName
-------------
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

Az a *MicrosoftWindowsServer* publisher:

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

Az a *WindowsServer* kínál:

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
2016-Nano-Server
```

Ezt követően az a *2016-Datacenter* Termékváltozat:

```powershell
$skuName="2016-Datacenter"
Get-AzureRMVMImage -Location $locName -Publisher $pubName -Offer $offerName -Sku $skuName | Select Version
```

Most a kijelölt közzétevő, az ajánlat, SKU és verzió URN kombinálható (értékeket elválasztani:). Adja át az e URN a `--image` paraméter, a virtuális gép létrehozásakor a [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) parancsmag. Ne feledje, hogy választhatóan lecserélheti a verziószámot a URN "legutóbbi". Ebben a verzióban mindig a lemezkép legújabb verziója. Használhatja a URN a [Set-AzureRMVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage) PowerShell-parancsmagot. 

Ha virtuális gép és a Resource Manager-sablon, a külön-külön állítsa be a kép paraméterek a `imageReference` tulajdonságok. Tekintse meg a [sablonra való hivatkozást](/azure/templates/microsoft.compute/virtualmachines).

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]


### <a name="view-plan-properties"></a>Terv tulajdonságainak megtekintése

A képfájl beszerzési terv adatainak megtekintéséhez futtassa a `Get-AzureRMVMImage` parancsmag. Ha a `PurchasePlan` kimenet tulajdonság értéke nem `null`, a kép rendelkezik feltételeket el kell fogadnia a programozott telepítés előtt.  

Például a Windows Server 2016 Datacenter rendszerképet nem rendelkezik további feltételeket, mert a `PurchasePlan` információk `null`:

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

Egy hasonló parancs futtatása az adatok tudományos virtuálisgép - Windows 2016 képen látható a következő `PurchasePlan` tulajdonságok: `name`, `product`, és `publisher`. (Egyes képek is rendelkeznek egy `promotion code` tulajdonságot.) A lemezkép központi telepítéséhez elfogadja a feltételeket, és a programozott telepítés engedélyezése a következő szakaszban talál.

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

A licencszerződés megtekintéséhez használja a [Get-AzureRmMarketplaceterms](/powershell/module/azurerm.marketplaceordering/get-azurermmarketplaceterms) parancsmag és a beszerzési fázis terv paraméterek. A kimeneti a feltételeket a Piactéri lemezképhez egy hivatkozást kínál a, és bemutatja, hogy korábban elfogadott a feltételeket. Példa:

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

Használja a [Set-AzureRmMarketplaceterms](/powershell/module/azurerm.marketplaceordering/set-azurermmarketplaceterms) parancsmag elfogadja vagy elutasítja a feltételeket. Csak egyszer előfizetésenként kép feltételek elfogadásának kell. Példa:

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
Signature         : VNMTRJK3MNJ5SROEG2BYDA2YGECU33GXTD3UFPLPC4BAVKAUL3PDYL3KBKBLG4ZCDJZVNSA7KJWTGMDSYDD6KRLV3LV274DLBJSS4GQ
Accepted          : True
Signdate          : 2/23/2018 7:49:31 PM
```

### <a name="deploy-using-purchase-plan-parameters"></a>Telepítheti a beszerzési terv paraméterek használata
Elfogadja a feltételeket a lemezkép, telepítheti a virtuális gépek az előfizetéshez. Ahogy az az alábbi kódrészletet, használja a [Set-AzureRmVMPlan](/powershell/module/azurerm.compute/set-azurermvmplan) parancsmag a virtuális gép objektum a piactér terv adatainak beállításához. A teljes parancsfájl létrehozni a virtuális gép hálózati beállításait, majd fejezze be a központi telepítés, tekintse meg a [PowerShell parancsfájl példák](powershell-samples.md).

```powershell
...
$vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize Standard_D1

# Set the Marketplace plan information
$vmConfig = Set-AzureRmVMPlan -VM $vmConfig -Publisher "imagePlanPublisher" -Product "imagePlanProduct" -Name "imagePlanName"

$cred=Get-Credential

$vmConfig = Set-AzureRmVMOperatingSystem -Windows -VM $vmConfig -ComputerName "myVM" -Credential $cred

# Set the Marketplace image
$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName "imagePublisher" -Offer "imageOffer" -Skus "imageSku" -Version "imageVersion"
...
```
Majd adja meg a Virtuálisgép-konfiguráció hálózati konfigurációs objektumok együtt a `New-AzureRmVM` parancsmag.

## <a name="next-steps"></a>További lépések
A virtuális gép gyors létrehozásához `New-AzureRmVM` alapvető kép információk segítségével tekintse meg a [egy Windows rendszerű virtuális gép létrehozása a PowerShell használatával](quick-create-powershell.md).

Tekintse meg a PowerShell-mintaparancsfájl [teljesen konfigurált virtuális gép létrehozása](../scripts/virtual-machines-windows-powershell-sample-create-vm.md).


