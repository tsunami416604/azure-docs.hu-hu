---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: d848b92da5d4181832adff8499b3531d020c30c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78155495"
---
Az ideiglenes operációsrendszer-lemezek a helyi virtuális gép (VM) tárolóján jönnek létre, és nem kerülnek mentésre a távoli Azure Storage-ba. Az ideiglenes operációsrendszer-lemezek jól működnek az állapotnélküli számítási feladatokhoz, ahol az alkalmazások toleránsak az egyes virtuálisgép-hibákkal szemben, de jobban érinti őket a virtuális gép telepítési ideje vagy az egyes virtuálisgép-példányok újrafelvételének. Az efemer operációs rendszer lemeze alacsonyabb olvasási/írási késleltetést kap az operációs rendszer lemezére, és gyorsabb virtuális gép-újraképezést kap. 
 
A fő jellemzői a múló lemezek a következők: 
- Ideális állapotnélküli alkalmazásokhoz.
- A Marketplace-en és az egyéni lemezképeken is használhatók.
- A virtuális gépek gyors alaphelyzetbe állítása vagy újraképe, valamint a set-példányok eredeti rendszerindítási állapotba méretezése.  
- Kisebb késés, hasonlóan egy ideiglenes lemezhez. 
- Az ideiglenes operációsrendszer-lemezek ingyenesek, az operációs rendszer lemezének nincs tárolási költsége.
- Ezek minden Azure-régióban elérhetők. 
- Az Efemer os lemezt a [Megosztott képtár](/azure/virtual-machines/linux/shared-image-galleries)támogatja. 
 

 
Az állandó és az ideiglenes operációsrendszer-lemezek közötti főbb különbségek:

|                             | Állandó operációsrendszer-lemez                          | Rövid élettartamú operációsrendszer-lemez                              |    |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| Az operációs rendszer lemezének méretkorlátja      | 2 TiB                                                                                        | Gyorsítótár mérete a virtuális gép mérete vagy 2TiB, amelyik kisebb. A **GiB gyorsítótárának méretét**lásd: [DS,](../articles/virtual-machines/linux/sizes-general.md) [ES,](../articles/virtual-machines/linux/sizes-memory.md) [M,](../articles/virtual-machines/linux/sizes-memory.md) [FS](../articles/virtual-machines/linux/sizes-compute.md)és [GS](/azure/virtual-machines/linux/sizes-previous-gen#gs-series)              |
| Támogatott virtuális gépek méretei          | Összes                                                                                          | DSv1, DSv2, DSv3, Esv3, Fs, FsV2, GS, M                                               |
| Lemeztípus-támogatás           | Felügyelt és nem felügyelt operációsrendszer-lemez                                                                | Csak felügyelt operációsrendszer-lemez                                                               |
| Régió támogatása              | Minden régió                                                                                  | Minden régió                              |
| Adatmegőrzés            | Az operációs rendszer lemezére írt operációsrendszer-lemez adatok az Azure Storage-ban tárolódnak                                  | Az operációs rendszer lemezére írt adatok a helyi virtuálisgép-tárolóban tárolódnak, és nem őrződnek meg az Azure Storage-ban. |
| Leállítva felszabadított állapot      | A virtuális gépek és a méretezési csoport példányai leállíthatók és újraindíthatók a leállítandó állapotból | A virtuális gépek és a méretezési csoport példányai nem állíthatók fel                                  |
| Speciális operációsrendszer-lemeztámogatás | Igen                                                                                          | Nem                                                                                 |
| Operációs rendszer lemeze átméretezése              | A virtuális gép létrehozása során és a virtuális gép leállítása után támogatott                                | Csak a virtuális gépek létrehozása során támogatott                                                  |
| Átméretezés új virtuális gépméretre   | Az operációs rendszer lemezadatai megőrződnek                                                                    | Az operációs rendszer lemezén lévő adatok törlődnek, az operációs rendszer újra kivan építve                                      |

## <a name="size-requirements"></a>Méretre vonatkozó követelmények

Virtuális gép és a példánylemezképek üzembe helyezhető a virtuális gép gyorsítótárának méretéig. Például a szabványos Windows Server-lemezképek a piactérről körülbelül 127 GiB, ami azt jelenti, hogy szüksége van egy virtuális gép mérete, amelya gyorsítótár nagyobb, mint 127 GiB. Ebben az esetben a [Standard_DS2_v2](~/articles/virtual-machines/dv2-dsv2-series.md) gyorsítótárának mérete 86 GiB, ami nem elég nagy. A Standard_DS3_v2 gyorsítótárának mérete 172 GiB, ami elég nagy. Ebben az esetben a Standard_DS3_v2 a DSv2 sorozat legkisebb mérete, amelyet ezzel a lemezképpel használhat. A Marketplace-en és a Windows Server-lemezképek, amelyek jelölik `[smallsize]` általában mintegy 30 GiB, és a legtöbb rendelkezésre álló virtuális gép méretei.

Az ideiglenes lemezekhez a virtuális gép mérete is megköveteli, hogy a prémium szintű tárolás t. A méretek általában (de `s` nem mindig) van egy a neve, mint a DSv2 és EsV3. További információ: [Az Azure virtuális gép méretei](../articles/virtual-machines/linux/sizes.md) című témakörben talál további részleteket, amelyek körül a méretek támogatják a prémium szintű tárhelyet.

## <a name="powershell"></a>PowerShell

Ha egy ideiglenes lemezt szeretne használni egy PowerShell virtuális gép központi telepítéséhez, használja a [Set-AzVMOSDisk-et](/powershell/module/az.compute/set-azvmosdisk) a virtuális gép konfigurációjában. Állítsa `-DiffDiskSetting` be `Local` `-Caching` a `ReadOnly`és a .     

```powershell
Set-AzVMOSDisk -DiffDiskSetting Local -Caching ReadOnly
```

Méretezési csoport központi telepítések, használja a [Set-AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) parancsmag a konfigurációban. Állítsa `-DiffDiskSetting` be `Local` `-Caching` a `ReadOnly`és a .


```powershell
Set-AzVmssStorageProfile -DiffDiskSetting Local -OsDiskCaching ReadOnly
```

## <a name="cli"></a>parancssori felület

Ha egy rövid élettartamú lemezt szeretne használni egy `--ephemeral-os-disk` CLI virtuális gép `true` központi `--os-disk-caching` telepítéséhez, állítsa be az az [vm create](/cli/azure/vm#az-vm-create) paraméterét, és állítsa a paramétert a számára. `ReadOnly`

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --ephemeral-os-disk true \
  --os-disk-caching ReadOnly \
  --admin-username azureuser \
  --generate-ssh-keys
```

A méretezési csoportok, `--ephemeral-os-disk true` használja ugyanazt a paramétert [az az-vmss-create](/cli/azure/vmss#az-vmss-create) és állítsa be a `--os-disk-caching` paramétert. `ReadOnly`

## <a name="portal"></a>Portál   

Az Azure Portalon dönthet úgy, hogy rövid élettartamú lemezeket használ a virtuális gép üzembe helyezésekor a **Lemezek** lap **Speciális** szakaszának megnyitásával. **A használatra ideiglenes operációs rendszer lemez** válassza **igen**.

![Képernyőkép a választógombról, amely a rövid élettartamú operációs rendszer lemezének használatát választja](./media/virtual-machines-common-ephemeral/ephemeral-portal.png)

Ha a rövid élettartamú lemez használatának lehetősége szürkén jelenik meg, előfordulhat, hogy olyan virtuális gépméretet választott, amelynek gyorsítótármérete nem nagyobb, mint az operációs rendszerlemez,vagy amely nem támogatja a prémium szintű tárhelyet. Lépjen vissza az **Alapok** lapra, és próbáljon meg másik virtuális gépméretet választani.

A portál használatával is létrehozhat méretezési csoportokat a rövid élettartamú operációs rendszer lemezeivel. Csak győződjön meg róla, hogy válasszon egy virtuális gép méretét elég nagy gyorsítótár mérete, majd a **Használata ideiglenes operációs rendszer lemez** válassza **igen**.

![Képernyőkép a választógombról, amely a méretezési csoport hoz egy ideiglenes operációsrendszer-lemez használatát választja](./media/virtual-machines-common-ephemeral/scale-set.png)

## <a name="scale-set-template-deployment"></a>Készletsablon telepítésének méretezése  
A folyamat, hogy hozzon létre egy méretezési csoport, `diffDiskSettings` amely egy `Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile` ideiglenes operációs rendszer lemez, hogy adja hozzá a tulajdonságot az erőforrás-típus a sablonban. Emellett a gyorsítótárazási házirendet `ReadOnly` a rövid élettartamú operációsrendszer-lemezre kell állítani. 


```json
{ 
  "type": "Microsoft.Compute/virtualMachineScaleSets", 
  "name": "myScaleSet", 
  "location": "East US 2", 
  "apiVersion": "2018-06-01", 
  "sku": { 
    "name": "Standard_DS2_v2", 
    "capacity": "2" 
  }, 
  "properties": { 
    "upgradePolicy": { 
      "mode": "Automatic" 
    }, 
    "virtualMachineProfile": { 
       "storageProfile": { 
        "osDisk": { 
          "diffDiskSettings": { 
                "option": "Local" 
          }, 
          "caching": "ReadOnly", 
          "createOption": "FromImage" 
        }, 
        "imageReference":  { 
          "publisher": "Canonical", 
          "offer": "UbuntuServer", 
          "sku": "16.04-LTS", 
          "version": "latest" 
        } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvmss", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
  } 
}  
```

## <a name="vm-template-deployment"></a>Virtuálisgép-sablon központi telepítése 
Egy ideiglenes operációsrendszer-lemezzel rendelkező virtuális gép üzembe helyezhető egy sablon használatával. A folyamat, amely ideiglenes operációsrendszer-lemezeket használó virtuális gépet `diffDiskSettings` hoz létre, a tulajdonság hozzáadása a sablonBan lévő Microsoft.Compute/virtualMachines erőforrástípushoz. Emellett a gyorsítótárazási házirendet `ReadOnly` a rövid élettartamú operációsrendszer-lemezre kell állítani. 

```json
{ 
  "type": "Microsoft.Compute/virtualMachines", 
  "name": "myVirtualMachine", 
  "location": "East US 2", 
  "apiVersion": "2018-06-01", 
  "properties": { 
       "storageProfile": { 
            "osDisk": { 
              "diffDiskSettings": { 
                "option": "Local" 
              }, 
              "caching": "ReadOnly", 
              "createOption": "FromImage" 
            }, 
            "imageReference": { 
                "publisher": "MicrosoftWindowsServer", 
                "offer": "WindowsServer", 
                "sku": "2016-Datacenter-smalldisk", 
                "version": "latest" 
            }, 
            "hardwareProfile": { 
                 "vmSize": "Standard_DS2_v2" 
             } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvirtualmachine", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
 } 
```


## <a name="reimage-a-vm-using-rest"></a>Virtuális gép újraképe rest használatával
A virtuális gép példányát ideiglenes operációsrendszer-lemezsel újrafeljátszhatja a REST API használatával az alábbiakban leírtak szerint, és az Azure Portalon keresztül a virtuális gép Áttekintés ablaktáblájára. A méretezési csoportok, reimaging már elérhető a Powershell, CLI és a portálon keresztül.

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2018-06-01" 
```
 
## <a name="frequently-asked-questions"></a>Gyakori kérdések

**K: Mekkora a helyi operációsrendszer-lemezek mérete?**

V: Támogatjuk a platform és az egyéni rendszerképek, a virtuális gép gyorsítótárának méretéig, ahol az összes olvasási/írási az operációs rendszer lemezén lesz helyi ugyanazon a csomóponton, mint a virtuális gép. 

**K: Átméretezhető-e az ideiglenes operációsrendszer-lemez?**

V: Nem, ha az ideiglenes operációsrendszer-lemez ki van építve, az operációs rendszer lemeze nem méretezhető át. 

**K: Csatlakoztathatok felügyelt lemezeket egy ideiglenes virtuális géphez?**

V: Igen, egy felügyelt adatlemezt csatolhat egy ideiglenes operációsrendszer-lemezt használó virtuális géphez. 

**K: Minden virtuálisgép-méret támogatott a rövid élettartamú operációs rendszer lemezek?**

V: Nem, minden prémium szintű storage virtuális gép méretek támogatottak (DS, ES, FS, GS és M), kivéve a B-sorozat, N-sorozat, és a H-sorozat méretei.  
 
**K: A rövid élettartamú operációs rendszer lemez alkalmazható a meglévő virtuális gépek és méretezési csoportok?**

V: Nem, a rövid élettartamú operációs rendszer lemeze csak a virtuális gép és a méretezési csoport létrehozása során használható. 

**K: Lehet keverni rövid élettartamú és normál operációs rendszer lemezek egy méretezési csoportban?**

V: Nem, nem lehet egy mix a rövid élettartamú és állandó operációsrendszer-lemezpéldányok ugyanazon a méretezési csoporton belül. 

**K: A rövid élettartamú operációs rendszer lemeze powershell vagy CLI használatával hozható létre?**

V: Igen, hozhat létre virtuális gépek et ideiglenes operációsrendszer-lemez rest, sablonok, PowerShell és CLI használatával.

**K: Milyen funkciók at nem támogatja a rövid élettartamú operációs rendszer lemez?**

V: Az efemer lemezek nem támogatják:
- Virtuálisgép-képek készítése
- Lemez-pillanatképek 
- Azure Disk Encryption 
- Azure Backup
- Azure Site Recovery  
- Operációs rendszer lemezelt- és lemezcseréje 
