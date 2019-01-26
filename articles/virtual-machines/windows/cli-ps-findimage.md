---
title: Válassza ki a Windows VM-rendszerképek az Azure-ban |} A Microsoft Docs
description: Azure PowerShell használatával a közzétevő, ajánlat, Termékváltozat és verzió Marketplace Virtuálisgép-rendszerképek határozza meg.
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
ms.date: 01/25/2019
ms.author: danlep
ms.openlocfilehash: 7a300826db512a813282eea71d2e898f0221a977
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2019
ms.locfileid: "55077743"
---
# <a name="find-windows-vm-images-in-the-azure-marketplace-with-azure-powershell"></a>Az Azure Marketplace-en az Azure PowerShell használatával Windows Virtuálisgép-rendszerképek keresése

Ez a cikk ismerteti az Azure piactéren Virtuálisgép-rendszerképek keresése az Azure PowerShell használatával. Ezt követően megadhatja a Piactéri rendszerkép programozott módon létrehozott egy virtuális Gépet a PowerShell-lel, Resource Manager-sablonok vagy más eszközök.

Elérhető lemezképek és ajánlatok használatával is megkeresheti a [Azure Marketplace-en](https://azuremarketplace.microsoft.com/) kirakat, a [az Azure portal](https://portal.azure.com), vagy a [Azure CLI-vel](../linux/cli-ps-findimage.md). 

Győződjön meg arról, hogy telepítve van, és a legújabb konfigurált [Azure PowerShell-modul](/powershell/azure/azurerm/install-azurerm-ps).

[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]

## <a name="table-of-commonly-used-windows-images"></a>A gyakran használt Windows-rendszerképek táblája

Ez a táblázat bemutatja a rendelkezésre álló SKU-k egy részét a jelzett közzétevők és ajánlatokat.

| Közzétevő | Ajánlat | SKU |
|:--- |:--- |:--- |:--- |
| MicrosoftWindowsServer |WindowsServer |A 2019-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2019-Datacenter-Core |
| MicrosoftWindowsServer |WindowsServer |2019-Datacenter-with-Containers |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter-Server-Core |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter-with-Containers |
| MicrosoftWindowsServer |WindowsServer |2012-R2-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2012-Datacenter |
| MicrosoftDynamicsNAV |DynamicsNAV |2017 |
| MicrosoftSharePoint |MicrosoftSharePointServer |2019 |
| MicrosoftSQLServer |SQL2019-WS2016 |Enterprise |
| MicrosoftRServer |RServer-WS2016 |Enterprise |

## <a name="navigate-the-images"></a>Keresse meg a képek

Futtassa a kép egy helyen található egyik módja a [Get-AzureRMVMImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher), [Get-AzureRMVMImageOffer](/powershell/module/azurerm.compute/get-azurermvmimageoffer), és [Get-AzureRMVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku) parancsmagok sorrendben:

1. Listázza a rendszerkép-közzétevőket.
2. Listázza egy adott közzétevő ajánlatait.
3. Listázza egy adott ajánlathoz tartozó termékváltozatokat.

Ezután a kiválasztott termékváltozat futtassa [Get-AzureRMVMImage](/powershell/module/azurerm.compute/get-azurermvmimage) üzembe helyezéséhez a verziók listázásához.

1. A közzétevők listázása:

    ```powershell
    $locName="<Azure location, such as West US>"
    Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName
    ```

2. Adja meg a kiválasztott közzétevő nevét, és az ajánlatok listázása:

    ```powershell
    $pubName="<publisher>"
    Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer
    ```

3. Adja meg a kiválasztott ajánlat nevét, és a termékváltozatok listázása:

    ```powershell
    $offerName="<offer>"
    Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
    ```

4. Adja meg a kiválasztott Termékváltozat nevét, és a lemezkép-verzió beolvasása:

    ```powershell
    $skuName="<SKU>"
    Get-AzureRMVMImage -Location $locName -Publisher $pubName -Offer $offerName -Sku $skuName | Select Version
    ```

Kimenetéből származó a `Get-AzureRMVMImage` parancsot, kiválaszthatja, hogy egy új virtuális gép üzembe helyezéséhez lemezképe.

Az alábbi példa bemutatja a parancsok és a kibocsátásukra teljes folyamata:

```powershell
$locName="West US"
Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName
```

Részleges kimenet:

```
PublisherName
-------------
...
abiquo
accedian
accellion
accessdata-group
accops
Acronis
Acronis.Backup
actian-corp
actian_matrix
actifio
activeeon
adgs
advantech
advantech-webaccess
advantys
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

Részleges kimenet:

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
2019-Datacenter
2019-Datacenter-Core
2019-Datacenter-Core-smalldisk
2019-Datacenter-Core-with-Containers
...
```

Ezután a *2019-Datacenter* Termékváltozat:

```powershell
$skuName="2019-Datacenter"
Get-AzureRMVMImage -Location $locName -Publisher $pubName -Offer $offerName -Sku $skuName | Select Version
```

Most a kijelölt közzétevő, ajánlat, Termékváltozat és verzió URN kombinálható (a következővel határolt értékekkel:). Ezt az URN adja át a `--image` paraméterrel rendelkező virtuális gép létrehozásakor a [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) parancsmag. Igény szerint lecserélheti a verziószámot a URN "legutóbbi" beolvasni a rendszerkép legújabb verzióját.

Resource Manager-sablonnal rendelkező virtuális Gépet telepít, akkor a beállításokat a lemezkép paraméterek a külön-külön az `imageReference` tulajdonságait. Tekintse meg a [sablonreferenciát](/azure/templates/microsoft.compute/virtualmachines).

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]

### <a name="view-plan-properties"></a>Csomag tulajdonságainak megtekintése

Egy rendszerkép beszerzési terv adatainak megtekintéséhez futtassa a `Get-AzureRMVMImage` parancsmagot. Ha a `PurchasePlan` a kimenetben tulajdonság nem `null`, a rendszerképre feltételeket el kell fogadnia a programozott telepítés előtt.  

Például a *Windows Server 2016 Datacenter* lemezkép nem rendelkezik további feltételeket, ezért a `PurchasePlan` információk `null`:

```powershell
$version = "2019.0.20190115"
Get-AzureRMVMImage -Location $locName -Publisher $pubName -Offer $offerName -Skus $skuName -Version $version
```

Kimenet:

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2019.0.20190115
Location         : westus
PublisherName    : MicrosoftWindowsServer
Offer            : WindowsServer
Skus             : 2019-Datacenter
Version          : 2019.0.20190115
FilterExpression :
Name             : 2019.0.20190115
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : null
DataDiskImages   : []

```

Az alábbi példában látható a hasonló parancsot a *adatelemző virtuális gép – Windows 2016* lemezképet, amely rendelkezik a következő `PurchasePlan` tulajdonságai: `name`, `product`, és `publisher`. Bizonyos képek is, hogy egy `promotion code` tulajdonság. A lemezkép központi telepítése, lásd a következő szakaszok fogadja el a feltételeket és a programozott telepítés engedélyezése.

```powershell
Get-AzureRMVMImage -Location "westus" -Publisher "microsoft-ads" -Offer "windows-data-science-vm" -Skus "windows2016" -Version "19.01.14"
```

Kimenet:

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/microsoft-ads/ArtifactTypes/VMImage/Offers/windows-data-science-vm/Skus/windows2016/Versions/19.01.14
Location         : westus
PublisherName    : microsoft-ads
Offer            : windows-data-science-vm
Skus             : windows2016
Version          : 19.01.14
FilterExpression :
Name             : 19.01.14
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

A licencszerződés megtekintéséhez használja a [Get-AzureRmMarketplaceterms](/powershell/module/azurerm.marketplaceordering/get-azurermmarketplaceterms) parancsmagot, és adja meg a vásárlás megtervezése paramétereket. A kimenet egy hivatkozást kínál a feltételeket, a Piactéri lemezképhez a, és bemutatja, hogy Ön korábban elfogadta a használati. Győződjön meg arról, csak kisbetűk használata a paraméterértékek megadásához.

```powershell
Get-AzureRmMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"
```

Kimenet:

```
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DVM%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : 2UMWH6PHSAIM4U22HXPXW25AL2NHUJ7Y7GRV27EBL6SUIDURGMYG6IIDO3P47FFIBBDFHZHSQTR7PNK6VIIRYJRQ3WXSE6BTNUNENXA
Accepted          : False
Signdate          : 1/25/2019 7:43:00 PM
```

Használja a [Set-AzureRmMarketplaceterms](/powershell/module/azurerm.marketplaceordering/set-azurermmarketplaceterms) parancsmag elfogadja vagy elutasítja a feltételeket. Csak egyszer előfizetésenként elfogadására a lemezképhez kell. Győződjön meg arról, csak kisbetűk használata a paraméterértékek megadásához. 

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

Után elfogadja a feltételeket a lemezkép, telepíthet egy virtuális Gépet, az adott előfizetésben. Ahogy az alábbi kódrészletben látható módon, a [Set-AzureRmVMPlan](/powershell/module/azurerm.compute/set-azurermvmplan) -parancsmaggal beállítható a Marketplace-en csomagadatokat a VM-objektumhoz. Teljes szkript létrehozása a virtuális gép hálózati beállításait, és a telepítés befejezéséhez, lásd: a [PowerShell-példaszkriptek](powershell-samples.md).

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

$version = "19.01.14"

$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName $publisherName -Offer $offerName -Skus $skuName -Version $version
...
```
Majd fogja adja át a hálózati konfigurációs objektumot és a Virtuálisgép-konfigurációt a `New-AzureRmVM` parancsmagot.

## <a name="next-steps"></a>További lépések

A virtuális gép gyors létrehozása a `New-AzureRmVM` futtatásával alapvető információkat, lásd: [Windows virtuális gép létrehozása a PowerShell-lel](quick-create-powershell.md).

Tekintse meg a PowerShell-példaszkript [teljes konfigurációjú virtuális gép létrehozása](../scripts/virtual-machines-windows-powershell-sample-create-vm.md).


