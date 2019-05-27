---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 05/02/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 47407df90a83501b8739a428789e20cddc59e83d
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66145923"
---
A rövid élettartamú operációsrendszer-lemezek helyi virtuális gép (VM) tárolására létrehozni és a távoli Azure Storage nem megőrzött. A rövid élettartamú operációsrendszer-lemezek is állapot nélküli számítási feladatokhoz, ahol alkalmazások tűri az egyes virtuális gép hibáinak, de nagyobb méretű környezetek esetében szükséges idő- vagy időérték alaphelyzetbe állítja a Virtuálisgép-példányokhoz több ügyféladataikat működik. Emellett akkor is alkalmazásokhoz, a klasszikus üzemi modellel, helyezze át a Resource Manager-alapú üzemi modellbe való telepítése. Rövid élettartamú operációsrendszer-lemezzel alacsonyabb olvasási/írási késés tapasztalható az operációsrendszer-lemezen, és a rendszerkép gyorsabban alaphelyzetbe állítható. Emellett a rövid élettartamú operációsrendszer-lemez szabad, díjak operációsrendszer-lemez tárolási költségek nélkül. 
 
Ideiglenes lemezek a fő funkciói a következők: 
- Marketplace-rendszerképek és egyéni rendszerképeket is használható.
- Virtuálisgép-rendszerképek a Virtuálisgép-gyorsítótár mérete akár telepítheti.
- Lehetővé teszi a gyors rendszerképének alaphelyzetbe állítása az eredeti rendszerindítás állapotát a virtuális vagy állítható alaphelyzetbe.  
- Ideiglenes lemez hasonló a kisebb futásidejű késés. 
- Az operációsrendszer-lemez számára ingyenesen. 
 
 
Állandó és a rövid élettartamú operációsrendszer-lemezek közti különbségeket:

|                             | Állandó operációsrendszer-lemez                          | Rövid élettartamú operációsrendszer-lemez                              |    |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| Maximális operációsrendszer-lemez mérete      | 2 TiB                                                                                        | Gyorsítótár mérete a Virtuálisgép-méretet vagy 2TiB, amelyik érték kisebb - [DS](../articles/virtual-machines/linux/sizes-general.md), [ES](../articles/virtual-machines/linux/sizes-memory.md), [M](../articles/virtual-machines/linux/sizes-memory.md), [FS](../articles/virtual-machines/linux/sizes-compute.md), és [GS](../articles/virtual-machines/linux/sizes-memory.md)              |
| Támogatott VM-méretek          | Az összes                                                                                          | DSv1, DSv2, DSv3, Esv3, Fs, FsV2, GS, M                                               |
| Lemez típusa támogatása           | Felügyelt és nem felügyelt operációsrendszer-lemez                                                                | Felügyelt operációsrendszer-lemez csak                                                               |
| Régió támogatása              | Minden régió                                                                                  | Minden régió                              |
| Adatmegőrzés            | Az operációs rendszer lemez adatait, az operációs rendszer lemezre írt Azure Storage szolgáltatásban tárolódnak                                  | Operációsrendszer-lemezre írt adatok a helyi virtuális gép Storage tárolja, és az Azure Storage nincs megőrizve. |
| (Felszabadítva) állapot      | Virtuális gépek és a méretezési csoport példányaihoz felszabadítva és újraindítani az az (felszabadítva) állapot | Virtuális gépek és a méretezési csoport példányaihoz nem lehet, felszabadítva                                  |
| Specializált operációsrendszer-lemezek ügyfélszolgálatához | Igen                                                                                          | Nem                                                                                 |
| Operációsrendszer-lemez átméretezése              | Támogatott virtuális gépek létrehozása során, és miután virtuális gép felszabadítva                                | Csak a virtuális gépek létrehozása során támogatott                                                  |
| Az új Virtuálisgép-méret átméretezése   | Operációsrendszer-lemez adatok megőrződnek                                                                    | Az operációsrendszer-lemez az adatok törlődnek, az operációs rendszer újratelepítés                                      |

## <a name="register-for-the-preview"></a>Regisztráljon az előzetes verzió


Önálló regisztráljon az előzetes verziója a rövid élettartamú operációsrendszer-lemezek az Azure CLI-vel vagy az Azure PowerShell legújabb verzióját használja.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature –FeatureName LocalDiffDiskPreview -ProviderNamespace Microsoft.Compute
```

Annak ellenőrzése, ha regisztrálva van az előzetes verzió:

```azurepowershell-interactive
Get-AzProviderFeature –FeatureName LocalDiffDiskPreview -ProviderNamespace Microsoft.Compute
```

### <a name="cli"></a>CLI

```azurecli-interactive
az provider register --namespace Microsoft.Compute
az feature register --namespace Microsoft.Compute --name LocalDiffDiskPreview
```

Annak ellenőrzése, ha regisztrálva van az előzetes verzió:
 
```azurecli-interactive
az provider show --namespace Microsoft.Compute
```


## <a name="scale-set-deployment"></a>Méretezési csoport üzembe helyezés  
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

## <a name="vm-deployment"></a>Virtuális gép üzembe helyezése 
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

V: Az előzetes verzió támogatjuk a platform és/vagy a képek legfeljebb a virtuális gép gyorsítótár méretét, ahol minden olvasás/írás, az operációsrendszer-lemez a virtuális gép ugyanazon a csomóponton helyi lesz. 

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
