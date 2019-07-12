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
ms.openlocfilehash: a57335eccfce1e81fe0cc85ae6fa7b12aa27e1c3
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67805871"
---
A rövid élettartamú operációsrendszer-lemezek helyi virtuális gép (VM) tárolására létrehozni és a távoli Azure Storage nem menti. A rövid élettartamú operációsrendszer-lemezek jól működjenek a állapot nélküli számítási feladatokhoz, ahol alkalmazásokat tűrik az egyes virtuális gép hibáinak, de még több virtuális gép üzembe helyezési idő által érintett vagy az egyes Virtuálisgép-példányok rendszerképeinek alaphelyzetbe állítása. A rövid élettartamú operációsrendszer-lemez, az alacsonyabb írási/olvasási késés operációsrendszer-lemez és gyorsabb virtuális gép rendszerképét alaphelyzetbe állítani a szolgáltatás. 
 
Ideiglenes lemezek a fő funkciói a következők: 
- Állapot nélküli alkalmazások ideális választás.
- Marketplace-en és az egyéni rendszerképeket is használható.
- Lehetővé teszi a gyors visszaállítás vagy a rendszerkép alaphelyzetbe állítását virtuális gépek és a méretezési csoport példányai eredeti rendszerindító állapotúra állít.  
- Kisebb hálózati késést, hasonló ideiglenes lemez. 
- A rövid élettartamú operációsrendszer-lemezek használata díjmentes, díjak operációsrendszer-lemez tárolási költségek nélkül.
- Elérhetők az összes Azure-régióban. 
- A rövid élettartamú operációsrendszer-lemez által támogatott [megosztott képgyűjtemény](/azure/virtual-machines/linux/shared-image-galleries). 
 

 
Állandó és a rövid élettartamú operációsrendszer-lemezek közti különbségeket:

|                             | Állandó operációsrendszer-lemez                          | Rövid élettartamú operációsrendszer-lemez                              |    |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| Maximális operációsrendszer-lemez mérete      | 2 TiB                                                                                        | Gyorsítótár mérete a Virtuálisgép-méretet vagy 2TiB, amelyik érték kisebb. Az a **gyorsítótár mérete GiB-ban**, lásd: [DS](../articles/virtual-machines/linux/sizes-general.md), [ES](../articles/virtual-machines/linux/sizes-memory.md), [M](../articles/virtual-machines/linux/sizes-memory.md), [FS](../articles/virtual-machines/linux/sizes-compute.md), és [GS](/azure/virtual-machines/linux/sizes-previous-gen#gs-series)              |
| Támogatott VM-méretek          | Összes                                                                                          | DSv1, DSv2, DSv3, Esv3, Fs, FsV2, GS, M                                               |
| Lemez típusa támogatása           | Felügyelt és nem felügyelt operációsrendszer-lemez                                                                | Felügyelt operációsrendszer-lemez csak                                                               |
| Régió támogatása              | Minden régió                                                                                  | Minden régió                              |
| Adatmegőrzés            | Az operációs rendszer lemez adatait, az operációs rendszer lemezre írt Azure Storage szolgáltatásban tárolódnak                                  | Operációsrendszer-lemezre írt adatok a helyi virtuális gép Storage tárolja, és az Azure Storage nincs megőrizve. |
| (Felszabadítva) állapot      | Virtuális gépek és a méretezési csoport példányaihoz felszabadítva és újraindítani az az (felszabadítva) állapot | Virtuális gépek és a méretezési csoport példányaihoz nem lehet, felszabadítva                                  |
| Specializált operációsrendszer-lemezek ügyfélszolgálatához | Igen                                                                                          | Nem                                                                                 |
| Operációsrendszer-lemez átméretezése              | Támogatott virtuális gépek létrehozása során, és miután virtuális gép felszabadítva                                | Csak a virtuális gépek létrehozása során támogatott                                                  |
| Az új Virtuálisgép-méret átméretezése   | Operációsrendszer-lemez adatok megőrződnek                                                                    | Az operációsrendszer-lemez az adatok törlődnek, az operációs rendszer újratelepítés                                      |

## <a name="size-requirements"></a>Méretkorlát

A virtuális gép gyorsítótár mérete akár virtuális gép és a példány lemezképeket telepíthet. Például Standard Windows Server a marketplace-ről rendszerképekkel készül 127 GB, ami azt jelenti, hogy kell-e a virtuális gép méretét, amely rendelkezik 127 GiB-nál nagyobb gyorsítótár. Ebben az esetben a [Standard_DS2_v2](/azure/virtual-machines/windows/sizes-general#dsv2-series) 86 GIB-ra, a gyorsítótár méretét rendelkezik, amely nincs elég nagy. A Standard_DS2_v2 rendelkezik a gyorsítótár mérete 172 GIB-ra, amely elég nagy. Ebben az esetben a Standard_DS3_v2 a DSv2-sorozat rendszerképben használható legkisebb méretet. A Marketplace-en és a Windows Server lemezképeket, amely jelöli az alapszintű Linux-rendszerképeket `[smallsize]` általában körülbelül 30 GIB-ra, és használhatja az elérhető Virtuálisgép-méretek a legtöbb.

A rövid élettartamú lemezeket is megkövetelheti, hogy a Virtuálisgép-méretet támogatja a Premium storage. A méretek általában (de nem mindig) rendelkezik egy `s` a nevében, például a DSv2 és EsV3. További információkért lásd: [Azure Virtuálisgép-méretek](../articles/virtual-machines/linux/sizes.md) méretek körül, amely támogatja a Premium storage a részletekért.

## <a name="powershell"></a>PowerShell

Egy rövid élettartamú lemezt egy PowerShell-Virtuálisgép-telepítéshez használja az [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) a Virtuálisgép-konfiguráció. Állítsa be a `-DiffDiskSetting` való `Local` és `-Caching` való `ReadOnly`.     

```powershell
Set-AzVMOSDisk -DiffDiskSetting Local -Caching ReadOnly
```

A méretezési központi telepítések, használja a [Set-AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) parancsmag a konfigurációban. Állítsa be a `-DiffDiskSetting` való `Local` és `-Caching` való `ReadOnly`.


```powershell
Set-AzVmssStorageProfile -DiffDiskSetting Local -OsDiskCaching ReadOnly
```

## <a name="cli"></a>parancssori felület

A CLI virtuális gép üzembe helyezése egy rövid élettartamú lemez használatához állítsa a `--ephemeral-os-disk` paraméter [az virtuális gép létrehozása](/cli/azure/vm#az-vm-create) való `true` és a `--os-disk-caching` paramétert `ReadOnly`.

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

A méretezési csoportokhoz, használja ugyanazt `--ephemeral-os-disk true` paramétere [az vmss create](/cli/azure/vmss#az-vmss-create) és állítsa be a `--os-disk-caching` paramétert `ReadOnly`.

## <a name="portal"></a>Portál   

Az Azure Portalon lehet váltani, nyissa meg a virtuális gép telepítésekor a rövid élettartamú lemezek használata a **speciális** szakaszában a **lemezek** fülre. A **használja a rövid élettartamú operációsrendszer-lemez** kiválasztása **Igen**.

![Képernyőfelvétel: a választ egy rövid élettartamú operációsrendszer-lemezt a választógomb](./media/virtual-machines-common-ephemeral/ephemeral-portal.png)

Ha a beállítást egy rövid élettartamú lemezt használó szürkén jelenik meg, előfordulhat, hogy a virtuális gép méretét, amely nem rendelkezik a gyorsítótár mérete nagyobb, mint az operációsrendszer-képet, vagy, amely nem támogatja a Premium storage választott. Lépjen vissza a **alapjai** lapon, és válasszon egy másik Virtuálisgép-méretet.

Méretezési – a rövid élettartamú operációsrendszer-lemezek, a portál használatával is létrehozhat. Csak ellenőrizze, hogy kiválaszt egy Virtuálisgép-méret, gyorsítótár elég nagy méretű, majd a **használja a rövid élettartamú operációsrendszer-lemez** kiválasztása **Igen**.

![Képernyőfelvétel a választógomb, hogy használjon egy rövid élettartamú operációsrendszer-lemezt a méretezési csoporthoz:](./media/virtual-machines-common-ephemeral/scale-set.png)

## <a name="scale-set-template-deployment"></a>Méretezési csoport sablon telepítése  
A folyamat egy rövid élettartamú operációsrendszer-lemezt használó méretezési csoport létrehozása az, hogy adja hozzá a `diffDiskSettings` tulajdonságot a `Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile` erőforrástípust a sablonban. Emellett a gyorsítótárazási házirend értékre kell állítani `ReadOnly` a rövid élettartamú operációsrendszer-lemez. 


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

## <a name="vm-template-deployment"></a>Virtuálisgép-sablon telepítése 
Egy virtuális Gépet telepíthet egy rövid élettartamú operációsrendszer-lemezzel sablon használatával. A rövid élettartamú operációsrendszer-lemezeket használó virtuális gép létrehozásának folyamata az, hogy adja hozzá a `diffDiskSettings` a sablonban Microsoft.Compute/virtualMachines erőforrástípus tulajdonságot. Emellett a gyorsítótárazási házirend értékre kell állítani `ReadOnly` a rövid élettartamú operációsrendszer-lemez. 

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


## <a name="reimage-a-vm-using-rest"></a>Alaphelyzetbe állítja a virtuális gép a REST használatával
Jelenleg az egyetlen módszer a rövid élettartamú operációsrendszer-lemez egy virtuálisgép-példány lemezképének újbóli létrehozása a REST API használatával el. A méretezési csoportok rendszerképének alaphelyzetbe állítása már Powershell, CLI és a portálon keresztül érhető el.

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2018-06-01" 
```
 
## <a name="frequently-asked-questions"></a>Gyakori kérdések

**K: Mi az a helyi operációs rendszer lemezének mérete?**

V: Támogatott platform és egyéni rendszerképek, akár a virtuális gép gyorsítótár méretét, ahol minden olvasás/írás, az operációsrendszer-lemez a virtuális gép ugyanazon a csomóponton helyi lesz. 

**K: A rövid élettartamú operációsrendszer-lemez méretezhető?**

V: Nem, a rövid élettartamú operációsrendszer-lemez ki van építve, miután az operációsrendszer-lemez nem méretezhető át. 

**K: Hozzácsatlakoztathatok egy felügyelt lemezt egy ideiglenes virtuális géphez?**

V: Igen, egy felügyelt adatlemezre csatolhat egy rövid élettartamú operációsrendszer-lemezt használó virtuális gép. 

**K: Virtuálisgép-méretek támogatják a rövid élettartamú operációsrendszer-lemezek esetén?**

V: Nem, prémium szintű tárolás Virtuálisgép-méretek támogatott (DS, ES, FS, GS és M) a B sorozat, kivéve az N-sorozat, és a H-sorozat méretei.  
 
**K: A rövid élettartamú operációsrendszer-lemez alkalmazhatja a meglévő virtuális gépek és a méretezési csoportokat?**

V: Nem, elmúló operációsrendszer-lemez csak akkor használható Virtuálisgép során, és méretezhető létrehozását. 

**K: Kombinálhatja a rövid élettartamú és normál egy méretezési csoportban lévő operációsrendszer-lemezek?**

V: Nem, nem lehet ideiglenes és állandó az operációs rendszer lemez példányok ugyanazt a méretezési belül vegyesen. 

**K: A rövid élettartamú operációsrendszer-lemez hozható létre a Powershell vagy parancssori felület használatával?**

V: Igen, virtuális gépeket hozhat létre rövid élettartamú operációsrendszer-lemezzel REST, a sablonok, a PowerShell és a parancssori felület használatával.

**K: Milyen funkciók használata nem támogatott a rövid élettartamú operációsrendszer-lemez?**

V: A rövid élettartamú lemezek nem támogatják:
- Virtuálisgép-lemezképek rögzítését.
- Lemez-pillanatképek 
- Azure Disk Encryption 
- Azure Backup
- Azure Site Recovery  
- OS Disk Swap 
