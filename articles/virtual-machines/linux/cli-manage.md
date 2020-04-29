---
title: Az Azure parancssori felület gyakori parancsai
description: Ismerkedjen meg a gyakori Azure CLI-parancsokkal, amelyekkel megkezdheti a virtuális gépek Azure Resource Manager módban való felügyeletének megkezdését
author: RicksterCDN
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 05/12/2017
ms.author: rclaus
ms.openlocfilehash: 253f2ab1b192d22f43e4082766adf4ec4f86fe71
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "78969253"
---
# <a name="common-azure-cli-commands-for-managing-azure-resources"></a>Gyakori Azure CLI-parancsok az Azure-erőforrások kezeléséhez

Az Azure CLI használatával macOS, Linux és Windows rendszeren is létrehozhatja és kezelheti Azure-erőforrásait. Ez a cikk a virtuális gépek (VM-EK) létrehozásával és kezelésével kapcsolatos leggyakoribb parancsokat ismerteti.

Ehhez a cikkhez az Azure CLI 2.0.4 vagy újabb verziójára van szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha frissítenie kell, tekintse meg az [Azure CLI telepítését](/cli/azure/install-azure-cli)ismertető témakört. A böngészőből [Cloud Shell](/azure/cloud-shell/quickstart) is használhatja.

## <a name="basic-azure-resource-manager-commands-in-azure-cli"></a>Alapszintű Azure Resource Manager-parancsok az Azure CLI-ben
Az adott parancssori kapcsolókkal és lehetőségekkel kapcsolatos részletesebb segítségért írja `az <command> <subcommand> --help`be a következőt: az online parancs súgója és beállításai.

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
| Virtuális gép rendszerképének létrehozása | `az image create --resource-group myResourceGroup --source myVM --name myImage` |
| Virtuális gép létrehozása rendszerképből | `az vm create --resource-group myResourceGroup --name myNewVM --image myImage` |


## <a name="next-steps"></a>További lépések
A CLI-parancsokkal kapcsolatos további példákért tekintse meg a Linux rendszerű [virtuális gépek létrehozása és kezelése az Azure CLI-vel](tutorial-manage-vm.md) című oktatóanyagot.



