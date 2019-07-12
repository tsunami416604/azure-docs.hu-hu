---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 05/10/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 742e0028b1f92beb8300cc97f09d8292259fbc0a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712469"
---
# <a name="enable-and-deploy-azure-ultra-ssds-preview"></a>Engedélyezze és üzembe helyezése az Azure ultranagy SSD-k (előzetes verzió)

Az Azure ultranagy szemben – tartós állapotú meghajtók (SSD) (előzetes verzió) az ajánlat nagy teljesítményű, magas iops-érték és egységes közel valós idejű lemezes tárolás az Azure IaaS virtuális gépek (VM). Az új ajánlat felső részén a meglévő lemezek ajánlataink, ugyanazon rendelkezésre állási szinten sor teljesítményt biztosít. Ultranagy SSD-k egyik fő előnye dinamikusan megváltoztathatja az SSD és a virtuális gépek újraindítása nélkül a számítási feladatok teljesítményére. Ultranagy SSD-k adatokat-igényes számítási feladatokhoz, például az SAP HANA, a felső szintű adatbázisok és a tranzakciós adatokat használó munkaterhelések vannak kialakítva.

Jelenleg ultranagy SSD-k előzetes verzióban érhető el, és meg kell [regisztrálása](https://aka.ms/UltraSSDPreviewSignUp) annak érdekében, hogy elérhesse azokat az előzetes verzióban érhető el.

## <a name="determine-your-availability-zone"></a>Határozza meg a rendelkezésre állási zónában

Amint jóváhagyják, meg kell határoznia, melyik rendelkezésre állási zónában ultranagy SSD-k használatához, a rendszer. Futtathatja az alábbiak közül melyik zónát, az USA keleti RÉGIÓJA 2, üzembe helyezéséhez a ultranagy lemez meghatározni a következő parancsokat:

PowerShell: `Get-AzComputeResourceSku | where {$_.ResourceType -eq "disks" -and $_.Name -eq "UltraSSD_LRS" }`

PARANCSSORI FELÜLET: `az vm list-skus --resource-type disks --query "[?name=='UltraSSD_LRS'].locationInfo"`

A válasz formáját az alábbi, ahol X a zóna használatára az USA keleti RÉGIÓJA 2 üzembe helyezéséhez hasonló lesz. X 1, 2 vagy 3 lehet.

Megőrizheti a **zónák** érték azt jelenti, hogy a rendelkezésre állási zóna és a egy ultra SSD üzembe helyezéséhez szükség lesz rá.

|ResourceType  |Name (Név)  |Location  |Zóna  |Korlátozás  |Képesség  |Érték  |
|---------|---------|---------|---------|---------|---------|---------|
|Lemezek     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

> [!NOTE]
> A parancs nem válaszol, akkor a regisztrációját, hogy a szolgáltatás még mindig vagy folyamatban lévő, vagy nincs még jóváhagyva.

Most, hogy tudja, hogy melyik zónában való üzembe helyezéséhez, kövesse az ebben a cikkben az első ultranagy SSD a telepített virtuális gépek beolvasni a telepítés lépéseit.

## <a name="deploy-an-ultra-ssd-using-azure-resource-manager"></a>Az ultranagy SSD, az Azure Resource Manager üzembe helyezése

Először határozza meg, a virtuális gép méretét, üzembe helyezéséhez. Ez az előzetes verzió része csak a DsV3 és EsV3 virtuális gép operációsrendszer-család támogatottak. A második táblázat tartalmazza ezen [blog](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/) ezen Virtuálisgép-méretekkel kapcsolatos további részletekért.

Ha több ultranagy SSD-kkel rendelkező virtuális Gépet létrehozni, tekintse meg a mintát [virtuális gép létrehozása több ultranagy SSD](https://aka.ms/UltraSSDTemplate).

Ha szeretne saját sablont használni, győződjön meg arról, hogy **API-verzió** a `Microsoft.Compute/virtualMachines` és `Microsoft.Compute/Disks` van beállítva, `2018-06-01` (vagy újabb).

Állítsa a lemez termékváltozat **UltraSSD_LRS**, majd állítsa be a lemezkapacitás, az IOPS, az rendelkezésre állási zóna és az átviteli sebesség MB/s ultranagy lemez létrehozása.

Után a virtuális gép ki van építve, particionálja és formázza az adatlemezeket, és konfigurálja azokat a számítási feladatokhoz.

## <a name="deploy-an-ultra-ssd-using-cli"></a>Az ultranagy SSD CLI-vel üzembe helyezése

Először határozza meg, a virtuális gép méretét, üzembe helyezéséhez. Ez az előzetes verzió része csak a DsV3 és EsV3 virtuális gép operációsrendszer-család támogatottak. A második táblázat tartalmazza ezen [blog](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/) ezen Virtuálisgép-méretekkel kapcsolatos további részletekért.

Ultranagy SSD-k használatához létre kell hoznia egy virtuális Gépet, amely képes a ultranagy SSD-k használatával.

Cserélje le, vagy állítsa be a **$vmname**, **$rgname**, **$diskname**, **$location**, **$password**, **$user** változók a saját értékeire. Állítsa be **$zone** a rendelkezésre állási zónában webhelyről származó alkalmazásazonosítóra értékéhez a [Ez a cikk elejére](#determine-your-availability-zone). Ezután futtassa a következő parancssori parancsot egy ultra engedélyezett virtuális Gépet hoz létre:

```azurecli-interactive
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --attach-data-disks $diskname --size Standard_D4s_v3 --location $location
```

### <a name="create-an-ultra-ssd-using-cli"></a>Hozzon létre egy ultra SSD parancssori felület használatával

Most, hogy egy virtuális Gépet, amely képes a ultranagy SSD-k használatával, hozzon létre, és egy ultra SSD csatlakoztatni.

```azurecli-interactive
location="eastus2"
subscription="xxx"
rgname="ultraRG"
diskname="ssd1"
vmname="ultravm1"
zone=123

#create an Ultra SSD disk
az disk create `
--subscription $subscription `
-n $diskname `
-g $rgname `
--size-gb 4 `
--location $location `
--zone $zone `
--sku UltraSSD_LRS `
--disk-iops-read-write 1000 `
--disk-mbps-read-write 50
```

### <a name="adjust-the-performance-of-an-ultra-ssd-using-cli"></a>Az ultranagy SSD CLI-vel teljesítményét

Ultranagy SSD meghajtókat kínálnak egy egyedi képesség, amely lehetővé teszi a teljesítmény, a következő parancsot a funkció használatát mutatja be:

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

## <a name="deploy-an-ultra-ssd-using-powershell"></a>Az ultranagy SSD PowerShell-lel üzembe helyezése

Először határozza meg, a virtuális gép méretét, üzembe helyezéséhez. Ez az előzetes verzió része csak a DsV3 és EsV3 virtuális gép operációsrendszer-család támogatottak. A második táblázat tartalmazza ezen [blog](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/) ezen Virtuálisgép-méretekkel kapcsolatos további részletekért.

Ultranagy SSD-k használatához létre kell hoznia egy virtuális Gépet, amely képes a ultranagy SSD-k használatával. Cserélje le, vagy állítsa be a **$resourcegroup** és **$vmName** változók a saját értékeire. Állítsa be **$zone** a rendelkezésre állási zónában webhelyről származó alkalmazásazonosítóra értékéhez a [Ez a cikk elejére](#determine-your-availability-zone). Majd futtassa a következő [New-azvm parancsmag](/powershell/module/az.compute/new-azvm) paranccsal hozzon létre egy ultra engedélyezve van a virtuális Géphez:

```powershell
New-AzVm `
    -ResourceGroupName $resourcegroup `
    -Name $vmName `
    -Location "eastus2" `
    -Image "Win2016Datacenter" `
    -EnableUltraSSD `
    -size "Standard_D4s_v3" `
    -zone $zone
```

### <a name="create-an-ultra-ssd-using-powershell"></a>Hozzon létre egy PowerShell-lel ultranagy SSD

Most, hogy egy virtuális Gépet, amely képes a ultranagy SSD meghajtók használata, létrehozása, és egy ultra SSD csatlakoztatása:

```powershell
$diskconfig = New-AzDiskConfig `
-Location 'EastUS2' `
-DiskSizeGB 8 `
-DiskIOPSReadWrite 1000 `
-DiskMBpsReadWrite 100 `
-AccountType UltraSSD_LRS `
-CreateOption Empty `
-zone $zone;

New-AzDisk `
-ResourceGroupName $resourceGroup `
-DiskName 'Disk02' `
-Disk $diskconfig;
```

### <a name="adjust-the-performance-of-an-ultra-ssd-using-powershell"></a>Egy PowerShell-lel ultranagy SSD teljesítményét

Ultranagy SSD-kkel rendelkezik egy egyedi képesség, amely lehetővé teszi a teljesítmény, a következő parancsot egy példa, amely beállítja a teljesítmény a lemez leválasztása nélkül:

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```

## <a name="next-steps"></a>További lépések

Ha szeretné-e meg az új lemez típusa [azt az előnézetet, a kérdőív hozzáférés kérése](https://aka.ms/UltraSSDPreviewSignUp).