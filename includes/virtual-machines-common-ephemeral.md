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
ms.openlocfilehash: 0e59a28ce1fb3c562bf76420a5e62e347230e964
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68669676"
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
| OPERÁCIÓSRENDSZER-lemezre vonatkozó méretkorlát      | 2 TiB                                                                                        | A virtuális gép méretének vagy 2TiB a gyorsítótár mérete, attól függően, hogy melyik a kisebb. A **GIB gyorsítótár**-méretének megtekintéséhez lásd: [DS](../articles/virtual-machines/linux/sizes-general.md), [es](../articles/virtual-machines/linux/sizes-memory.md), [M](../articles/virtual-machines/linux/sizes-memory.md), [FS](../articles/virtual-machines/linux/sizes-compute.md)és [GS](/azure/virtual-machines/linux/sizes-previous-gen#gs-series)              |
| Támogatott VM-méretek          | Összes                                                                                          | DSv1, DSv2, DSv3, Esv3, FS, FsV2, GS, M                                               |
| Lemez típusának támogatása           | Felügyelt és nem felügyelt operációsrendszer-lemez                                                                | Csak felügyelt operációsrendszer-lemez                                                               |
| Régió támogatása              | Minden régió                                                                                  | Minden régió                              |
| Adatmegőrzés            | Az operációsrendszer-lemezre írt operációsrendszer-lemezeket az Azure Storage tárolja                                  | Az operációsrendszer-lemezre írt adatkészleteket a rendszer a helyi virtuálisgép-tárolóba tárolja, és nem őrzi meg az Azure Storage szolgáltatásban. |
| Lefoglalt állapot leállítása      | A virtuális gépek és a méretezési csoport példányai leállíthatók, és a Leállított állapotból indíthatók újra. | A virtuális gépek és a méretezési csoport példányai nem állíthatók le – nem foglalható le.                                  |
| Speciális operációsrendszer-lemezek támogatása | Igen                                                                                          | Nem                                                                                 |
| OPERÁCIÓSRENDSZER-lemez átméretezése              | A virtuális gép létrehozásakor és a virtuális gép leállítása után támogatott                                | Csak a virtuális gépek létrehozásakor támogatott                                                  |
| Átméretezés új virtuálisgép-méretre   | Az operációsrendszer-lemezre vonatkozó adatmegőrzés                                                                    | Az operációsrendszer-lemezen lévő adatlemez törölve lett, az operációs rendszer újból kiépítve                                      |

## <a name="size-requirements"></a>Méretre vonatkozó követelmények

A virtuális gépek és a példányok rendszerképeinek üzembe helyezése a virtuális gép gyorsítótárának méretétől függetlenül végezhető el. A piactér standard szintű Windows Server-rendszerképei például a 127 GiB-ra vonatkoznak, ami azt jelenti, hogy a virtuális gép méretének a 127 GiB-nál nagyobb gyorsítótárral kell rendelkeznie. Ebben az esetben a [Standard_DS2_v2](/azure/virtual-machines/windows/sizes-general#dsv2-series) 86 GIB gyorsítótár-mérettel rendelkezik, ami nem elég nagy. A Standard_DS3_v2 gyorsítótárának mérete 172 GiB, ami elég nagy. Ebben az esetben a Standard_DS3_v2 a DSv2 sorozat legkisebb mérete, amelyet ezzel a képpel lehet használni. Az alapszintű Linux-rendszerképek a piactéren és a Windows Server- `[smallsize]` lemezképekben, amelyeknek a célja az, hogy körülbelül 30 GIB, és a rendelkezésre álló virtuálisgép-méretek többségét használhatják.

Az ideiglenes lemezek esetében a virtuális gép mérete is támogatja a Premium Storage-ot. A méretek általában (de nem mindig) rendelkeznek `s` a névvel, például a DSv2 és a EsV3. További információ: Azure-beli virtuálisgép- [méretek](../articles/virtual-machines/linux/sizes.md) a Premium Storage-t támogató méretek részleteiről.

## <a name="powershell"></a>PowerShell

Ha ideiglenes lemezt szeretne használni egy PowerShell virtuálisgép-telepítéshez, használja a [set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) a virtuális gép konfigurációjában. Állítsa a `-DiffDiskSetting` `Local` és `-Caching` aértékrea`ReadOnly`következőt:.     

```powershell
Set-AzVMOSDisk -DiffDiskSetting Local -Caching ReadOnly
```

A méretezési csoport üzembe helyezéséhez használja a konfigurációban a [set-AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) parancsmagot. Állítsa a `-DiffDiskSetting` `Local` és `-Caching` aértékrea`ReadOnly`következőt:.


```powershell
Set-AzVmssStorageProfile -DiffDiskSetting Local -OsDiskCaching ReadOnly
```

## <a name="cli"></a>parancssori felület

Ha ideiglenes lemezt szeretne használni egy CLI virtuálisgép-telepítéshez, állítsa `--ephemeral-os-disk` be a paramétert az [az VM Create](/cli/azure/vm#az-vm-create) to `true` és `ReadOnly`a `--os-disk-caching` paraméter értékeként.

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

A `--ephemeral-os-disk true` méretezési csoportok esetében ugyanazt a paramétert használja az az [-vmss-Create](/cli/azure/vmss#az-vmss-create) paranccsal, `--os-disk-caching` és `ReadOnly`állítsa a paramétert a következőre:.

## <a name="portal"></a>Portál   

A Azure Portal a **lemezek** lap **speciális** szakaszának megnyitásával kiválaszthatja, hogy a virtuális gépek üzembe helyezése során az ideiglenes lemezeket használja-e. Az **ideiglenes operációsrendszer-lemez használata** beállításnál válassza az **Igen**lehetőséget.

![Képernyőfelvétel: az elmúló operációsrendszer-lemez használatának kiválasztására szolgáló választógombot ábrázoló gomb](./media/virtual-machines-common-ephemeral/ephemeral-portal.png)

Ha egy ideiglenes lemez használatának lehetősége szürkén jelenik meg, lehetséges, hogy olyan virtuálisgép-méretet választott ki, amely nem rendelkezik az operációsrendszer-lemezképnél nagyobb gyorsítótár-mérettel, vagy amely nem támogatja a Premium Storage-ot. Lépjen vissza az **alapvető beállítások** lapra, és válasszon másik virtuális gép méretét.

A portálon létrehozhat olyan méretezési csoportokat is, amelyek elmúló operációsrendszer-lemezzel rendelkeznek. Csak győződjön meg arról, hogy a virtuális gép méretét nagy méretű gyorsítótár-mérettel, majd az **elmúló operációsrendszer-lemez használata** területen válassza az **Igen**lehetőséget.

![Képernyőfelvétel: a választógomb, amely a méretezési csoport elmúló operációsrendszer-lemezének kiválasztására használható](./media/virtual-machines-common-ephemeral/scale-set.png)

## <a name="scale-set-template-deployment"></a>Méretezési csoport sablonjának üzembe helyezése  
Az elmúló operációsrendszer-lemezt használó méretezési csoport létrehozásának folyamata a `diffDiskSettings` tulajdonság `Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile` hozzáadása a sablonban lévő erőforrástípus. Emellett a gyorsítótárazási házirendet az ideiglenes operációsrendszer `ReadOnly` -lemezre kell beállítani. 


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
Sablon használatával üzembe helyezhet egy ideiglenes operációsrendszer-lemezzel rendelkező virtuális gépet. Az elmúló operációsrendszer-lemezeket használó virtuális gépek létrehozásának folyamata az, `diffDiskSettings` hogy hozzáadja a tulajdonságot a sablonban található Microsoft. számítási/virtualMachines erőforrástípus számára. Emellett a gyorsítótárazási házirendet az ideiglenes operációsrendszer `ReadOnly` -lemezre kell beállítani. 

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
Jelenleg az egyetlen módszer, amely az ideiglenes operációsrendszer-lemezzel rendelkező virtuálisgép-példányok rendszerképének alaphelyzetbe állítására szolgál REST API használatával. A méretezési csoportok esetében az Alaphelyzetbe állítás már elérhető a PowerShell, a CLI és a portál használatával.

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2018-06-01" 
```
 
## <a name="frequently-asked-questions"></a>Gyakori kérdések

**K: Mekkora a helyi operációsrendszer-lemezek mérete?**

V: A platform és az Egyéni rendszerképek támogatása a virtuális gép gyorsítótárának méretétől függően történik, ahol az operációs rendszer lemezének összes olvasási/írási helye a virtuális géppel megegyező csomóponton lesz. 

**K: Átméretezi az ideiglenes operációsrendszer-lemezt?**

V: Nem, az ideiglenes operációsrendszer-lemez kiépítés után az operációsrendszer-lemez nem méretezhető át. 

**K: Csatolhatok Managed Disks egy ideiglenes virtuális géphez?**

V: Igen, az elmúló operációsrendszer-lemezt használó virtuális gépekhez csatolhat felügyelt adatlemezt. 

**K: A rendszer minden virtuálisgép-méretet támogat az elmúló operációsrendszer-lemezek esetén?**

V: Nem, az összes Premium Storage VM-méret támogatott (DS, ES, FS, GS és M), kivéve a B sorozat, az N sorozat és a H-sorozat méretét.  
 
**K: Alkalmazható az ideiglenes operációsrendszer-lemez a meglévő virtuális gépekre és méretezési csoportokra?**

V: Nem, a rendszer csak a virtuális gép és a méretezési csoport létrehozása során használhatja a múló operációsrendszer-lemezt. 

**K: Használhat egy méretezési csoportba tartozó ideiglenes és normál operációsrendszer-lemezeket?**

V: Nem, egyazon méretezési csoporton belül nem lehet elmúló és állandó operációsrendszer-lemez-példányokat kombinálni. 

**K: Hozható létre az ideiglenes operációsrendszer-lemez a PowerShell vagy a parancssori felület használatával?**

V: Igen, a REST, a Templates, a PowerShell és a parancssori felület használatával hozhat létre virtuális gépeket az ideiglenes operációsrendszer-lemezzel.

**K: Milyen funkciók nem támogatottak az elmúló operációsrendszer-lemezzel?**

V: Az ideiglenes lemezek nem támogatják a következőket:
- VM-lemezképek rögzítése
- Lemez-pillanatképek 
- Azure Disk Encryption 
- Azure Backup
- Azure Site Recovery  
- OPERÁCIÓSRENDSZER-lemez cseréje 
