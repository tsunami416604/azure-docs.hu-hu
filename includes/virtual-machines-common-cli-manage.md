---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: beece95164f0d82b1aa7f22d56f4dce02f4bb38c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59804156"
---
Az Azure parancssori felület lehetővé teszi, hogy hozhat létre, és a macOS, Linux és Windows Azure-erőforrások kezeléséhez. Ez a cikk részletesen hozhat létre és kezelhet virtuális gépeket (VM) leggyakoribb parancsai közül néhányat.

Ehhez a cikkhez az Azure CLI 2.0.4-es vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha frissíteni szeretne, olvassa el [Azure CLI telepítése](/cli/azure/install-azure-cli). Is [Cloud Shell](/azure/cloud-shell/quickstart) a böngészőben.

## <a name="basic-azure-resource-manager-commands-in-azure-cli"></a>Alapszintű Azure Resource Manager-parancsok az Azure CLI-ben
Az adott parancssori kapcsolókkal és beállításokkal kapcsolatban részletesebb, használhatja az online parancsok súgóját és lehetőségeit beírásával `az <command> <subcommand> --help`.

### <a name="create-vms"></a>Virtuális gépek létrehozása
| Tevékenység | Az Azure parancssori felület parancsai |
| --- | --- |
| Hozzon létre egy erőforráscsoportot | `az group create --name myResourceGroup --location eastus` |
| Linux rendszerű virtuális gép létrehozása | `az vm create --resource-group myResourceGroup --name myVM --image ubuntults` |
| Windows rendszerű virtuális gép létrehozása | `az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter` |

### <a name="manage-vm-state"></a>Virtuális gép állapot kezelése
| Tevékenység | Az Azure parancssori felület parancsai |
| --- | --- |
| Virtuális gép elindítása | `az vm start --resource-group myResourceGroup --name myVM` |
| Virtuális gép leállítása | `az vm stop --resource-group myResourceGroup --name myVM` |
| Virtuális gép felszabadítása | `az vm deallocate --resource-group myResourceGroup --name myVM` |
| Virtuális gép újraindítása | `az vm restart --resource-group myResourceGroup --name myVM` |
| Virtuális gép ismételt üzembe helyezése | `az vm redeploy --resource-group myResourceGroup --name myVM` |
| Virtuális gép törlése | `az vm delete --resource-group myResourceGroup --name myVM` |

### <a name="get-vm-info"></a>Virtuális gép adatainak beolvasása
| Tevékenység | Az Azure parancssori felület parancsai |
| --- | --- |
| Virtuális gépek felsorolása | `az vm list` |
| Virtuális gép adatainak lekérése | `az vm show --resource-group myResourceGroup --name myVM` |
| A virtuálisgép-erőforrások használatának megtekintése | `az vm list-usage --location eastus` |
| Minden elérhető virtuálisgép-méret megtekintése | `az vm list-sizes --location eastus` |

## <a name="disks-and-images"></a>Lemezek és lemezképek
| Tevékenység | Az Azure parancssori felület parancsai |
| --- | --- |
| Adatlemez hozzáadása egy virtuális géphez | `az vm disk attach --resource-group myResourceGroup --vm-name myVM --disk myDataDisk --size-gb 128 --new` |
| Adatlemez eltávolítása egy virtuális gépből | `az vm disk detach --resource-group myResourceGroup --vm-name myVM --disk myDataDisk` |
| Lemez átméretezése | `az disk update --resource-group myResourceGroup --name myDataDisk --size-gb 256` |
| Lemez pillanatképének elkészítése | `az snapshot create --resource-group myResourceGroup --name mySnapshot --source myDataDisk` |
| Egy virtuális gép rendszerképének létrehozása | `az image create --resource-group myResourceGroup --source myVM --name myImage` |
| Virtuális gép létrehozása a rendszerképből | `az vm create --resource-group myResourceGroup --name myNewVM --image myImage` |


## <a name="next-steps"></a>További lépések
A CLI-parancsok további példákért lásd a [létrehozása és kezelése a Linux rendszerű virtuális gépek az Azure CLI-vel](../articles/virtual-machines/linux/tutorial-manage-vm.md) oktatóanyag.

