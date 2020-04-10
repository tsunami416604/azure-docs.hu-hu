---
title: Windows virtuálisgép-lemezképek kiválasztása az Azure-ban
description: Az Azure PowerShell segítségével határozza meg a közzétevő, ajánlat, termékváltozat és a Marketplace virtuális gép lemezképek verziója.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 188b8974-fabd-4cd3-b7dc-559cbb86b98a
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/25/2019
ms.author: cynthn
ms.openlocfilehash: a96cf4b07e6d564be1cac7fbb62a45d2d9ea7943
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011157"
---
# <a name="find-windows-vm-images-in-the-azure-marketplace-with-azure-powershell"></a>Windows virtuálisgép-rendszerképek keresése az Azure Marketplace-en az Azure PowerShell-lel

Ez a cikk ismerteti, hogyan azure PowerShell segítségével virtuálisgép-lemezképek az Azure Marketplace-en. Ezután megadhatja a Marketplace-lemezképet, amikor programozott módon hoz létre virtuális gép powershell, erőforrás-kezelő sablonok vagy más eszközök segítségével.

Az [Azure Marketplace](https://azuremarketplace.microsoft.com/) kirakatában, az Azure [Portalon](https://portal.azure.com)vagy az [Azure CLI-ben](../linux/cli-ps-findimage.md)is böngészhet az elérhető képek és ajánlatok között. 

 

[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]

## <a name="table-of-commonly-used-windows-images"></a>Általánosan használt Windows-lemezképek táblázata

Ez a táblázat a megadott kiadók és ajánlatok elérhető Skus-készletét mutatja be.

| Közzétevő | Ajánlat | SKU |
|:--- |:--- |:--- |
| MicrosoftWindowsServer |WindowsServer |2019-Adatközpont |
| MicrosoftWindowsServer |WindowsServer |2019-Datacenter-Core |
| MicrosoftWindowsServer |WindowsServer |2019-Datacenter-tárolókkal |
| MicrosoftWindowsServer |WindowsServer |2016-Adatközpont |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter-Server-Core |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter-tárolókkal |
| MicrosoftWindowsServer |WindowsServer |2012-R2-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2012-Datacenter |
| MicrosoftSharePoint |MicrosoftSharePointServer |sp2019 |
| MicrosoftSQLServer |SQL2019-WS2016 |Enterprise |
| MicrosoftRServer |RServer-WS2016 |Enterprise |

## <a name="navigate-the-images"></a>Navigálás a képek között

Egy adott helyen a kép megkeresésének egyik módja a [Get-AzVMImagePublisher](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimagepublisher), [a Get-AzVMImageOffer](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimageoffer)és a [Get-AzVMImageSku](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimagesku) parancsmagok sorrendben történő futtatása:

1. Listázza a rendszerkép-közzétevőket.
2. Listázza egy adott közzétevő ajánlatait.
3. Listázza egy adott ajánlathoz tartozó termékváltozatokat.

Ezután a kiválasztott termékváltozat okán futtassa a [Get-AzVMImage futtatása](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimage) korlista a telepítendő verziókat.

1. Sorolja fel a kiadókat:

    ```powershell
    $locName="<Azure location, such as West US>"
    Get-AzVMImagePublisher -Location $locName | Select PublisherName
    ```

2. Töltse ki a kiválasztott kiadó nevét, és sorolja fel az ajánlatokat:

    ```powershell
    $pubName="<publisher>"
    Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
    ```

3. Töltse ki a kiválasztott ajánlat nevét, és sorolja fel a suk-okat:

    ```powershell
    $offerName="<offer>"
    Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
    ```

4. Töltse ki a kiválasztott Termékváltozat nevét, és töltse le a kép verzióverzióját:

    ```powershell
    $skuName="<SKU>"
    Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Sku $skuName | Select Version
    ```
    
A `Get-AzVMImage` parancs kimenetén kiválaszthat egy verzióképet egy új virtuális gép üzembe helyezéséhez.

A következő példa a parancsok teljes sorozatát és azok kimeneteit mutatja be:

```powershell
$locName="West US"
Get-AzVMImagePublisher -Location $locName | Select PublisherName
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

A *MicrosoftWindowsServer* kiadója számára:

```powershell
$pubName="MicrosoftWindowsServer"
Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
```

Kimenet:

```
Offer
-----
Windows-HUB
WindowsServer
WindowsServerSemiAnnual
```

A *WindowsServer* ajánlatesetén:

```powershell
$offerName="WindowsServer"
Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
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

Ezt követően a *2019-Datacenter* Termékváltozat:

```powershell
$skuName="2019-Datacenter"
Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Sku $skuName | Select Version
```

Mostmár kombinálhatja a kiválasztott közzétevőt, ajánlatot, termékváltozatot és verziót egy URN-ben (:) elválasztott értékek. Adja át ezt `--image` az URN-t a paraméterrel, amikor virtuális gép létrehozása a [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) parancsmaggal. Az URN verziószámát lecserélheti a "legújabb" verziószámra, hogy a rendszerkép legújabb verzióját kapja.

Ha egy Erőforrás-kezelő sablonnal rendelkező virtuális gép üzembe helyezése, majd a `imageReference` rendszerkép paramétereit külön-külön állítja be a tulajdonságokban. Tekintse meg a [sablonreferenciát](/azure/templates/microsoft.compute/virtualmachines).

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]

### <a name="view-plan-properties"></a>Terv tulajdonságainak megtekintése

A lemezkép beszerzési tervadatainak megtekintéséhez futtassa a `Get-AzVMImage` parancsmagát. Ha `PurchasePlan` a tulajdonság a `null`kimenetnem, a lemezkép feltételeket el kell fogadnia, mielőtt az automatizált telepítés.  

A Windows *Server 2016 Datacenter-lemezkép* például nem `PurchasePlan` tartalmaz `null`további feltételeket, így az adatok a következők:

```powershell
$version = "2016.127.20170406"
Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName -Version $version
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

Az alábbi példa az *Adatelemzési virtuális gép – Windows 2016-lemezkép* hasonló parancsát mutatja be, amely a következő `PurchasePlan` tulajdonságokkal rendelkezik: `name`, `product`, és `publisher`. Néhány képhez `promotion code` ingatlan is található. A lemezkép központi telepítéséhez tekintse meg a következő szakaszokat a feltételek elfogadásához és az automatizált telepítés engedélyezéséhez.

```powershell
Get-AzVMImage -Location "westus" -PublisherName "microsoft-ads" -Offer "windows-data-science-vm" -Skus "windows2016" -Version "0.2.02"
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

### <a name="accept-the-terms"></a>A feltételek elfogadása

A licencfeltételek megtekintéséhez használja a [Get-AzMarketplaceterms](https://docs.microsoft.com/powershell/module/az.marketplaceordering/get-azmarketplaceterms) parancsmalt, és adja át a vásárlási terv paramétereit. A kimenet egy hivatkozást biztosít a Marketplace-lemezkép feltételeire, és megmutatja, hogy korábban elfogadta-e a feltételeket. Ügyeljen arra, hogy a paraméterértékekben minden kisbetűs betűt használjon.

```powershell
Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"
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

A [Set-AzMarketplaceterms](https://docs.microsoft.com/powershell/module/az.marketplaceordering/set-azmarketplaceterms) parancsmag segítségével fogadja el vagy utasítsa el a feltételeket. Előfizetésenként csak egyszer kell elfogadnod a feltételeket a lemezképhez. Ügyeljen arra, hogy a paraméterértékekben minden kisbetűs betűt használjon. 

```powershell
$agreementTerms=Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"

Set-AzMarketplaceTerms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016" -Terms $agreementTerms -Accept
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

### <a name="deploy-using-purchase-plan-parameters"></a>Telepítés a beszerzésiterv paramétereivel

Miután elfogadta a feltételeket egy lemezkép, üzembe helyezhetegy virtuális gép az adott előfizetésben. Ahogy az a következő kódrészletben látható, használja a [Set-AzVMPlan](https://docs.microsoft.com/powershell/module/az.compute/set-azvmplan) parancsmaga a Virtuálisgép-objektum Piactér-terv adatainak beállításához. A virtuális gép hálózati beállításainak létrehozásához és a központi telepítés befejezéséhez a [PowerShell-parancsfájlra vonatkozó példákat](powershell-samples.md)tartalmazó teljes parancsfájlt talál.

```powershell
...

$vmConfig = New-AzVMConfig -VMName "myVM" -VMSize Standard_D1

# Set the Marketplace plan information

$publisherName = "microsoft-ads"

$productName = "windows-data-science-vm"

$planName = "windows2016"

$vmConfig = Set-AzVMPlan -VM $vmConfig -Publisher $publisherName -Product $productName -Name $planName

$cred=Get-Credential

$vmConfig = Set-AzVMOperatingSystem -Windows -VM $vmConfig -ComputerName "myVM" -Credential $cred

# Set the Marketplace image

$offerName = "windows-data-science-vm"

$skuName = "windows2016"

$version = "19.01.14"

$vmConfig = Set-AzVMSourceImage -VM $vmConfig -PublisherName $publisherName -Offer $offerName -Skus $skuName -Version $version
...
```
Ezután adja át a virtuális gép konfigurációját `New-AzVM` a hálózati konfigurációs objektumokkal együtt a parancsmagnak.

## <a name="next-steps"></a>További lépések

Ha gyorsan szeretne virtuális `New-AzVM` gépet létrehozni a parancsmaggal az alapvető lemezképadatok használatával, olvassa el a Windows virtuális gép létrehozása a PowerShell használatával című [témakört.](quick-create-powershell.md)


Tekintse meg a PowerShell-parancsfájl példa [egy teljesen konfigurált virtuális gép létrehozása.](../scripts/virtual-machines-windows-powershell-sample-create-vm.md)


