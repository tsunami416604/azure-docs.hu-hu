---
title: Az Azure parancssori felület gyakori parancsai
description: Ismerje meg a gyakori Azure CLI-parancsokat a virtuális gépek azure Resource Manager módban való kezelésének megkezdéséhez
author: RicksterCDN
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 05/12/2017
ms.author: rclaus
ms.openlocfilehash: 253f2ab1b192d22f43e4082766adf4ec4f86fe71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969253"
---
# <a name="common-azure-cli-commands-for-managing-azure-resources"></a>Gyakori Azure CLI-parancsok az Azure-erőforrások kezeléséhez

Az Azure CLI lehetővé teszi az Azure-erőforrások létrehozását és kezelését macOS, Linux és Windows rendszeren. Ez a cikk a virtuális gépek (VM-ek) létrehozásához és kezeléséhez használt leggyakoribb parancsokat ismerteti.

Ez a cikk az Azure CLI 2.0.4-es vagy újabb verzióját igényli. A verzió azonosításához futtassa a következőt: `az --version`. Ha frissítenie kell, olvassa el [az Azure CLI telepítése](/cli/azure/install-azure-cli)című témakört. A Cloud [Shellt](/azure/cloud-shell/quickstart) a böngészőjéből is használhatja.

## <a name="basic-azure-resource-manager-commands-in-azure-cli"></a>Alapszintű Azure Resource Manager-parancsok az Azure CLI-ben
Ha részletesebb segítséget szeretne kérni az adott parancssori kapcsolókkal és beállításokkal `az <command> <subcommand> --help`kapcsolatban, az online parancs súgóját és beállításait a beírásával használhatja.

### <a name="create-vms"></a>Virtuális gépek létrehozása
| Tevékenység | Az Azure parancssori felület parancsai |
| --- | --- |
| Erőforráscsoport létrehozása | `az group create --name myResourceGroup --location eastus` |
| Linux rendszerű virtuális gép létrehozása | `az vm create --resource-group myResourceGroup --name myVM --image ubuntults` |
| Windows rendszerű virtuális gép létrehozása | `az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter` |

### <a name="manage-vm-state"></a>Virtuális gép állapotának kezelése
| Tevékenység | Az Azure parancssori felület parancsai |
| --- | --- |
| Virtuális gép elindítása | `az vm start --resource-group myResourceGroup --name myVM` |
| Virtuális gép leállítása | `az vm stop --resource-group myResourceGroup --name myVM` |
| Virtuális gép felszabadítása | `az vm deallocate --resource-group myResourceGroup --name myVM` |
| Virtuális gép újraindítása | `az vm restart --resource-group myResourceGroup --name myVM` |
| Virtuális gép ismételt üzembe helyezése | `az vm redeploy --resource-group myResourceGroup --name myVM` |
| Virtuális gép törlése | `az vm delete --resource-group myResourceGroup --name myVM` |

### <a name="get-vm-info"></a>Virtuális gép adatainak beszerezni
| Tevékenység | Az Azure parancssori felület parancsai |
| --- | --- |
| Virtuális gépek felsorolása | `az vm list` |
| Virtuális gép adatainak lekérése | `az vm show --resource-group myResourceGroup --name myVM` |
| A virtuálisgép-erőforrások használatának megtekintése | `az vm list-usage --location eastus` |
| Minden elérhető virtuálisgép-méret megtekintése | `az vm list-sizes --location eastus` |

## <a name="disks-and-images"></a>Lemezek és képek
| Tevékenység | Az Azure parancssori felület parancsai |
| --- | --- |
| Adatlemez hozzáadása egy virtuális géphez | `az vm disk attach --resource-group myResourceGroup --vm-name myVM --disk myDataDisk --size-gb 128 --new` |
| Adatlemez eltávolítása egy virtuális gépből | `az vm disk detach --resource-group myResourceGroup --vm-name myVM --disk myDataDisk` |
| Lemez átméretezése | `az disk update --resource-group myResourceGroup --name myDataDisk --size-gb 256` |
| Lemez pillanatképének elkészítése | `az snapshot create --resource-group myResourceGroup --name mySnapshot --source myDataDisk` |
| Virtuális gép lemezképének létrehozása | `az image create --resource-group myResourceGroup --source myVM --name myImage` |
| Virtuális gép létrehozása a képből | `az vm create --resource-group myResourceGroup --name myNewVM --image myImage` |


## <a name="next-steps"></a>További lépések
További példák a CLI-parancsok, tekintse meg a [Linux létrehozása és kezelése Linux os azure CLI](tutorial-manage-vm.md) oktatóanyag.



