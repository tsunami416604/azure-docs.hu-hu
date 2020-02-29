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
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2020
ms.locfileid: "78155495"
---
Az ideiglenes operációsrendszer-lemezek a helyi virtuális gép (VM) tárolóján jönnek létre, és nem lesznek mentve a távoli Azure-tárolóba. Az időszakos operációsrendszer-lemezek jól működnek az állapot nélküli munkaterhelések esetében, ahol az alkalmazások az egyes virtuálisgép-hibáktól függenek, de a virtuális gép üzembe helyezési ideje vagy az egyes virtuálisgép-példányok újrarendszerképe is befolyásolja. Az elmúló operációsrendszer-lemezzel alacsonyabb olvasási/írási késést kap az operációsrendszer-lemez és a virtuális gép lemezképének gyors alaphelyzetbe állítása. 
 
Az ideiglenes lemezek legfontosabb funkciói a következők: 
- Ideális állapot nélküli alkalmazásokhoz.
- A piactéren és az egyéni rendszerképeken is használhatók.
- Lehetővé teszi a virtuális gépek gyors alaphelyzetbe állítását vagy áttelepítését, illetve a méretezési csoport példányainak eredeti rendszerindítási állapotát.  
- Kisebb késés, hasonlóan egy ideiglenes lemezhez. 
- Az ideiglenes operációsrendszer-lemezek ingyenesek, az operációsrendszer-lemez tárolási díja nem jár.
- Ezek az összes Azure-régióban elérhetők. 
- A [megosztott rendszerkép](/azure/virtual-machines/linux/shared-image-galleries)-katalógus támogatja az ideiglenes operációsrendszer-lemezt. 
 

 
Az állandó és az elmúló operációsrendszer-lemezek közötti fő különbségek:

|                             | Állandó operációsrendszer-lemez                          | Rövid élettartamú operációsrendszer-lemez                              |    |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| OPERÁCIÓSRENDSZER-lemezre vonatkozó méretkorlát      | 2 TiB                                                                                        | A virtuális gép méretének vagy 2TiB a gyorsítótár mérete, attól függően, hogy melyik a kisebb. A **GIB gyorsítótár-méretének**megtekintéséhez lásd [: DS](../articles/virtual-machines/linux/sizes-general.md), [es](../articles/virtual-machines/linux/sizes-memory.md), [M](../articles/virtual-machines/linux/sizes-memory.md), [FS](../articles/virtual-machines/linux/sizes-compute.md)és [GS](/azure/virtual-machines/linux/sizes-previous-gen#gs-series)              |
| Támogatott VM-méretek          | Összes                                                                                          | DSv1, DSv2, DSv3, Esv3, FS, FsV2, GS, M                                               |
| Lemez típusának támogatása           | Felügyelt és nem felügyelt operációsrendszer-lemez                                                                | Csak felügyelt operációsrendszer-lemez                                                               |
| Régió támogatása              | Minden régió                                                                                  | Minden régió                              |
| Adatmegőrzés            | Az operációsrendszer-lemezre írt operációsrendszer-lemezeket az Azure Storage tárolja                                  | Az operációsrendszer-lemezre írt adatkészleteket a rendszer a helyi virtuálisgép-tárolóba tárolja, és nem őrzi meg az Azure Storage szolgáltatásban. |
| Lefoglalt állapot leállítása      | A virtuális gépek és a méretezési csoport példányai leállíthatók, és a Leállított állapotból indíthatók újra. | A virtuális gépek és a méretezési csoport példányai nem állíthatók le – nem foglalható le.                                  |
| Speciális operációsrendszer-lemezek támogatása | Igen                                                                                          | Nem                                                                                 |
| OPERÁCIÓSRENDSZER-lemez átméretezése              | A virtuális gép létrehozásakor és a virtuális gép leállítása után támogatott                                | Csak a virtuális gépek létrehozásakor támogatott                                                  |
| Átméretezés új virtuálisgép-méretre   | Az operációsrendszer-lemezre vonatkozó adatmegőrzés                                                                    | Az operációsrendszer-lemezen lévő adatlemez törölve lett, az operációs rendszer újból kiépítve                                      |

## <a name="size-requirements"></a>Méretre vonatkozó követelmények

A virtuális gépek és a példányok rendszerképeinek üzembe helyezése a virtuális gép gyorsítótárának méretétől függetlenül végezhető el. A piactér standard szintű Windows Server-rendszerképei például a 127 GiB-ra vonatkoznak, ami azt jelenti, hogy a virtuális gép méretének a 127 GiB-nál nagyobb gyorsítótárral kell rendelkeznie. Ebben az esetben a [Standard_DS2_v2](~/articles/virtual-machines/dv2-dsv2-series.md) 86 GIB gyorsítótár-mérettel rendelkezik, ami nem elég nagy. A Standard_DS3_v2 gyorsítótárának mérete 172 GiB, ami elég nagy. Ebben az esetben a Standard_DS3_v2 a DSv2-sorozat legkisebb mérete, amelyet ezzel a képpel lehet használni. A piactéren található alapszintű Linux-rendszerképek és a `[smallsize]` által jegyzett Windows Server-lemezképek általában körülbelül 30 GiB, és a rendelkezésre álló virtuálisgép-méretek többségét használhatják.

Az ideiglenes lemezek esetében a virtuális gép mérete is támogatja a Premium Storage-ot. A méret általában (de nem mindig) `s` a névben, például a DSv2 és a EsV3. További információ: Azure-beli virtuálisgép- [méretek](../articles/virtual-machines/linux/sizes.md) a Premium Storage-t támogató méretek részleteiről.

## <a name="powershell"></a>PowerShell

Ha ideiglenes lemezt szeretne használni egy PowerShell virtuálisgép-telepítéshez, használja a [set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) a virtuális gép konfigurációjában. A `-DiffDiskSetting` beállítása `Local` és `-Caching` `ReadOnly`.     

```powershell
Set-AzVMOSDisk -DiffDiskSetting Local -Caching ReadOnly
```

A méretezési csoport üzembe helyezéséhez használja a konfigurációban a [set-AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) parancsmagot. A `-DiffDiskSetting` beállítása `Local` és `-Caching` `ReadOnly`.


```powershell
Set-AzVmssStorageProfile -DiffDiskSetting Local -OsDiskCaching ReadOnly
```

## <a name="cli"></a>parancssori felület

Ha ideiglenes lemezt szeretne használni egy CLI virtuálisgép-telepítéshez, állítsa a `--ephemeral-os-disk` paramétert az [az VM Create](/cli/azure/vm#az-vm-create) to `true` és a `--os-disk-caching` paraméter értékét `ReadOnly`értékre.

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

A méretezési csoportok esetében ugyanazt a `--ephemeral-os-disk true` paramétert használja az [az-vmss-Create](/cli/azure/vmss#az-vmss-create) értékre, és állítsa a `--os-disk-caching` paramétert `ReadOnly`re.

## <a name="portal"></a>Portál   

A Azure Portal a **lemezek** lap **speciális** szakaszának megnyitásával kiválaszthatja, hogy a virtuális gépek üzembe helyezése során az ideiglenes lemezeket használja-e. Az **ideiglenes operációsrendszer-lemez használata** beállításnál válassza az **Igen**lehetőséget.

![Képernyőfelvétel: az elmúló operációsrendszer-lemez használatának kiválasztására szolgáló választógombot ábrázoló gomb](./media/virtual-machines-common-ephemeral/ephemeral-portal.png)

Ha egy ideiglenes lemez használatának lehetősége szürkén jelenik meg, lehetséges, hogy olyan virtuálisgép-méretet választott ki, amely nem rendelkezik az operációsrendszer-lemezképnél nagyobb gyorsítótár-mérettel, vagy amely nem támogatja a Premium Storage-ot. Lépjen vissza az **alapvető beállítások** lapra, és válasszon másik virtuális gép méretét.

A portálon létrehozhat olyan méretezési csoportokat is, amelyek elmúló operációsrendszer-lemezzel rendelkeznek. Csak győződjön meg arról, hogy a virtuális gép méretét nagy méretű gyorsítótár-mérettel, majd az **elmúló operációsrendszer-lemez használata** területen válassza az **Igen**lehetőséget.

![Képernyőfelvétel: a választógomb, amely a méretezési csoport elmúló operációsrendszer-lemezének kiválasztására használható](./media/virtual-machines-common-ephemeral/scale-set.png)

## <a name="scale-set-template-deployment"></a>Méretezési csoport sablonjának üzembe helyezése  
Az elmúló operációsrendszer-lemezt használó méretezési csoport létrehozásának folyamata az `diffDiskSettings` tulajdonság hozzáadása a sablon `Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile` erőforrástípus számára. Emellett a gyorsítótárazási házirendet úgy kell beállítani, hogy `ReadOnly` az ideiglenes operációsrendszer-lemezre. 


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

## <a name="vm-template-deployment"></a>Virtuálisgép-sablon üzembe helyezése 
Sablon használatával üzembe helyezhet egy ideiglenes operációsrendszer-lemezzel rendelkező virtuális gépet. Az elmúló operációsrendszer-lemezeket használó virtuális gépek létrehozásának folyamata az `diffDiskSettings` tulajdonság hozzáadása a Microsoft. számítási/virtualMachines erőforrástípus a sablonban. Emellett a gyorsítótárazási házirendet úgy kell beállítani, hogy `ReadOnly` az ideiglenes operációsrendszer-lemezre. 

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


## <a name="reimage-a-vm-using-rest"></a>Virtuális gép rendszerképének alaphelyzetbe állítása a REST használatával
A virtuálisgép-példányokat az alábbi módon, az Azure Portalon, a virtuális gép áttekintés paneljén található REST API segítségével alaphelyzetbe helyezheti egy ideiglenes operációsrendszer-lemezzel. A méretezési csoportok esetében az Alaphelyzetbe állítás már elérhető a PowerShell, a CLI és a portál használatával.

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2018-06-01" 
```
 
## <a name="frequently-asked-questions"></a>Gyakori kérdések

**K: mekkora a helyi operációsrendszer-lemezek mérete?**

A: támogatjuk a platformot és az egyéni rendszerképeket, akár a virtuális gép gyorsítótárának méretétől, ahol az operációs rendszer lemezének összes olvasási/írási helye a virtuális géppel megegyező csomóponton lesz. 

**K: az ideiglenes operációsrendszer-lemez átméretezése is lehetséges?**

A: nem, az ideiglenes operációsrendszer-lemez kiépítés után az operációsrendszer-lemez nem méretezhető át. 

**K: Csatolhatok Managed Disks egy ideiglenes virtuális géphez?**

Válasz: igen, a felügyelt adatlemezt egy ideiglenes operációsrendszer-lemezt használó virtuális géphez csatolhatja. 

**K: az összes virtuálisgép-méret támogatott az ideiglenes operációsrendszer-lemezek esetében?**

A: nem, az összes Premium Storage VM-méret támogatott (DS, ES, FS, GS és M), kivéve a B sorozat, az N sorozat és a H-sorozat méretét.  
 
**K: az ideiglenes operációsrendszer-lemez alkalmazható a meglévő virtuális gépekre és méretezési csoportokra?**

A: nem, az ideiglenes operációsrendszer-lemez csak a virtuális gép és a méretezési csoport létrehozásakor használható. 

**K: a méretezési csoportokban elmúló és normál operációsrendszer-lemezek is használhatók?**

A: nem, nem rendelkezhet az egyazon méretezési csoportba tartozó ideiglenes és állandó operációsrendszer-példányok keverékéből. 

**K: az ideiglenes operációsrendszer-lemez a PowerShell vagy a parancssori felület használatával hozható létre?**

Válasz: igen, a REST, a Templates, a PowerShell és a parancssori felület használatával hozhat létre virtuális gépeket az ideiglenes operációsrendszer-lemezzel.

**K: milyen funkciók nem támogatottak az elmúló operációsrendszer-lemezzel?**

A: az ideiglenes lemezek nem támogatják A következőket:
- VM-lemezképek rögzítése
- Lemez-pillanatképek 
- Azure Disk Encryption 
- Azure Backup
- Azure Site Recovery  
- OPERÁCIÓSRENDSZER-lemez cseréje 
