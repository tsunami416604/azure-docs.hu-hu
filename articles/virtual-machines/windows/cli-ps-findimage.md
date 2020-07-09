---
title: Windowsos VM-rendszerképek kiválasztása az Azure-ban
description: A Azure PowerShell segítségével meghatározhatja a Piactéri virtuálisgép-rendszerképek közzétevőjét, ajánlatát, SKU-jának és verziószámát.
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/25/2019
ms.author: cynthn
ms.openlocfilehash: 46a2badbbe957f6a8a6af7f5a40633ea24cadcd4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82083365"
---
# <a name="find-windows-vm-images-in-the-azure-marketplace-with-azure-powershell"></a>Windows virtuálisgép-rendszerképek keresése az Azure Marketplace-en az Azure PowerShell-lel

Ez a cikk azt ismerteti, hogyan használhatók a Azure PowerShell a virtuálisgép-rendszerképek megkereséséhez az Azure Marketplace-en. Ezt követően megadhatja a piactér rendszerképét, ha programozott módon hoz létre virtuális gépet a PowerShell, a Resource Manager-sablonok vagy más eszközök használatával.

Az elérhető lemezképek és ajánlatok az [Azure Marketplace](https://azuremarketplace.microsoft.com/) kirakat, a [Azure Portal](https://portal.azure.com)vagy az [Azure CLI](../linux/cli-ps-findimage.md)használatával is tallózhatók. 

 

[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]

## <a name="table-of-commonly-used-windows-images"></a>A gyakran használt Windows-rendszerképek táblázata

Ez a táblázat a jelzett közzétevők és ajánlatok számára elérhető SKU-ket mutatja.

| Publisher | Ajánlat | SKU |
|:--- |:--- |:--- |
| MicrosoftWindowsServer |WindowsServer |2019 – Datacenter |
| MicrosoftWindowsServer |WindowsServer |2019 – Datacenter-Core |
| MicrosoftWindowsServer |WindowsServer |2019 – Datacenter – tárolók |
| MicrosoftWindowsServer |WindowsServer |2016 – Datacenter |
| MicrosoftWindowsServer |WindowsServer |2016 – Datacenter – Server-Core |
| MicrosoftWindowsServer |WindowsServer |2016 – Datacenter – tárolók |
| MicrosoftWindowsServer |WindowsServer |2012-R2-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2012-Datacenter |
| MicrosoftSharePoint |MicrosoftSharePointServer |sp2019 |
| MicrosoftSQLServer |SQL2019-WS2016 |Enterprise |
| MicrosoftRServer |RServer – WS2016 |Enterprise |

## <a name="navigate-the-images"></a>A képek navigálása

Az egyik lehetőség, hogy egy helyen található rendszerképet keres, a [Get-AzVMImagePublisher](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimagepublisher), a [Get-AzVMImageOffer](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimageoffer)és a [Get-AzVMImageSku](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimagesku) parancsmagokat a következő sorrendben futtassa:

1. Listázza a rendszerkép-közzétevőket.
2. Listázza egy adott közzétevő ajánlatait.
3. Listázza egy adott ajánlathoz tartozó termékváltozatokat.

Ezután a kiválasztott SKU-hoz futtassa a [Get-AzVMImage](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimage) parancsot a telepítendő verziók listázásához.

1. Közzétevők listázása:

    ```powershell
    $locName="<Azure location, such as West US>"
    Get-AzVMImagePublisher -Location $locName | Select PublisherName
    ```

2. Adja meg a választott közzétevő nevét, és sorolja fel az ajánlatokat:

    ```powershell
    $pubName="<publisher>"
    Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
    ```

3. Adja meg a választott ajánlat nevét, és sorolja fel az SKU-ket:

    ```powershell
    $offerName="<offer>"
    Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
    ```

4. Adja meg a választott SKU-nevet, és szerezze be a rendszerkép verzióját:

    ```powershell
    $skuName="<SKU>"
    Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Sku $skuName | Select Version
    ```
    
A parancs kimenetében `Get-AzVMImage` kiválaszthatja az új virtuális gép üzembe helyezéséhez használt lemezképet.

Az alábbi példa a parancsok és azok kimenetének teljes sorát mutatja be:

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

A *MicrosoftWindowsServer* -közzétevő esetében:

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

A *windowsserver* ajánlathoz:

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

Ezután az *2019-Datacenter* SKU esetében:

```powershell
$skuName="2019-Datacenter"
Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Sku $skuName | Select Version
```

Most már egyesítheti a kiválasztott közzétevőt, ajánlatot, SKU-t és verziót egy URN (:) által elválasztott értékekkel). `--image`Ha a [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) parancsmaggal hoz létre egy virtuális gépet, adja át ezt az urn-t a paraméterrel. A lemezkép legújabb verziójának lekéréséhez igény szerint lecserélheti az URN verziószámát a "legutóbbi" értékre.

Ha Resource Manager-sablonnal helyez üzembe egy virtuális gépet, akkor a tulajdonságok paramétereit egyenként kell beállítania `imageReference` . Tekintse meg a [sablonreferenciát](/azure/templates/microsoft.compute/virtualmachines).

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]

### <a name="view-plan-properties"></a>Terv tulajdonságainak megtekintése

Ha meg szeretné tekinteni a rendszerkép vásárlási tervének adatait, futtassa a `Get-AzVMImage` parancsmagot. Ha a `PurchasePlan` kimenetben szereplő tulajdonság nem `null` , a rendszerképnek a programozott üzembe helyezés előtt el kell fogadnia a feltételeket.  

Például a *Windows Server 2016 Datacenter* -rendszerkép nem rendelkezik további feltételekkel, így az `PurchasePlan` információ `null` :

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

Az alábbi példa egy hasonló parancsot mutat be a *Data Science Virtual Machine-Windows 2016* rendszerképhez, amely a következő `PurchasePlan` tulajdonságokkal rendelkezik:, `name` `product` és `publisher` . Néhány rendszerkép is rendelkezik egy `promotion code` tulajdonsággal. A rendszerkép üzembe helyezéséhez tekintse át a következő részeket a feltételek elfogadásához és a programozott telepítés engedélyezéséhez.

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

A licencfeltételek megtekintéséhez használja a [Get-AzMarketplaceterms](https://docs.microsoft.com/powershell/module/az.marketplaceordering/get-azmarketplaceterms) parancsmagot, és adja meg a vásárlási terv paramétereit. A kimenet a Piactéri rendszerkép feltételeire mutató hivatkozást tartalmaz, és megjeleníti, hogy korábban elfogadta-e a feltételeket. Ügyeljen arra, hogy az összes kisbetűs betűt használja a paraméterek értékeiben.

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

Használja a [set-AzMarketplaceterms](https://docs.microsoft.com/powershell/module/az.marketplaceordering/set-azmarketplaceterms) parancsmagot a feltételek elfogadásához vagy elutasításához. A rendszerképhez csak egyszer kell elfogadnia a feltételeket. Ügyeljen arra, hogy az összes kisbetűs betűt használja a paraméterek értékeiben. 

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

### <a name="deploy-using-purchase-plan-parameters"></a>Üzembe helyezés a vásárlási terv paramétereinek használatával

A rendszerkép feltételeinek elfogadása után üzembe helyezhet egy virtuális gépet az előfizetésben. Ahogy az az alábbi kódrészletben is látható, a [set-AzVMPlan](https://docs.microsoft.com/powershell/module/az.compute/set-azvmplan) parancsmaggal állíthatja be a virtuálisgép-objektumhoz tartozó Piactéri terv adatait. A virtuális gép hálózati beállításainak létrehozásához és a telepítés befejezéséhez teljes parancsfájlt a PowerShell- [szkriptek példái](powershell-samples.md)című témakörben talál.

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
Ezután továbbítja a virtuális gép konfigurációját a hálózati konfigurációs objektumokkal együtt a `New-AzVM` parancsmaghoz.

## <a name="next-steps"></a>További lépések

A virtuális gép `New-AzVM` egyszerű rendszerkép-információkkal történő gyors létrehozásával kapcsolatban lásd: [Windows rendszerű virtuális gép létrehozása a PowerShell](quick-create-powershell.md)használatával.


A [teljes mértékben konfigurált virtuális gépek létrehozásához](../scripts/virtual-machines-windows-powershell-sample-create-vm.md)tekintse meg a PowerShell-parancsfájl példáját.


