---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 713068b940532331ee4a6c5f5a92816d328e41ec
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2019
ms.locfileid: "55736161"
---
Mielőtt az Azure CLI-t Resource Manager-parancsokkal és -sablonokkal használhatná az Azure-erőforrások és számítási feladatok erőforráscsoportokkal való üzembe helyezéséhez, szüksége lesz egy Azure-fiókra. Ha nem rendelkezik fiókkal, [itt feliratkozhat az ingyenes Azure-próbaidőszakra](https://azure.microsoft.com/pricing/free-trial/).

Ha még nem telepítette az Azure CLI-t, és nem csatlakoztatta az előfizetéséhez, tekintse meg az [Azure CLI telepítéséről szóló](../articles/cli-install-nodejs.md) részt, állítsa a módot `arm`-re az `azure config mode arm` paranccsal, és csatlakozzon az Azure-hoz az `azure login` paranccsal.

## <a name="cli-versions-to-complete-the-task"></a>A feladat befejezéséhez használható CLI-verziók
A következő CLI-verziók egyikével elvégezheti a feladatot:

- Az Azure klasszikus parancssori felület – parancssori felületünk a klasszikus és a resource management üzemi modellekhez (a jelen cikkben)
- [Az Azure CLI](../articles/virtual-machines/linux/cli-manage.md) – a parancssori következő generációját képviseli a resource management üzemi modellhez tartozó

## <a name="basic-azure-resource-manager-commands-in-azure-classic-cli"></a>Alapszintű Azure Resource Manager-parancsok az Azure klasszikus parancssori felület

Ez a cikk ismerteti az alapszintű parancsokat, amelyeket a klasszikus Azure CLI segítségével kezelhesse és használhassa az Azure-előfizetésében az erőforrások (elsősorban virtuális gépek).  Az adott parancssori kapcsolókkal és beállításokkal kapcsolatos részletesebb segítségért használja az online parancsok súgóját és lehetőségeit az `azure <command> <subcommand> --help` vagy `azure help <command> <subcommand>` beírását követően.

> [!NOTE]
> Ezek a példák nem tartalmazzák a sablonalapú műveleteket, amelyek általában ajánlottak a virtuális gépek a Resource Managerben történő üzembe helyezéséhez. További információt [az Azure CLI az Azure Resource Managerrel történő használatát](../articles/xplat-cli-azure-resource-manager.md) és a [virtuális gépek Azure Resource Manager-sablonokkal és az Azure CLI-vel történő üzembe helyezését és kezelését](../articles/virtual-machines/linux/create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ismertető cikkben talál.
> 
> 

| Tevékenység | Resource Manager |
| --- | --- | --- |
| Legalapvetőbb virtuális gép létrehozása |`azure vm quick-create [options] <resource-group> <name> <location> <os-type> <image-urn> <admin-username> <admin-password>`<br/><br/>(Az `image-urn` az `azure vm image list` parancs használatával szerezhető be. Példákért tekintse meg [ezt a cikket](../articles/virtual-machines/linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).) |
| Linux rendszerű virtuális gép létrehozása |`azure  vm create [options] <resource-group> <name> <location> -y "Linux"` |
| Windows rendszerű virtuális gép létrehozása |`azure  vm create [options] <resource-group> <name> <location> -y "Windows"` |
| Virtuális gépek felsorolása |`azure  vm list [options]` |
| Virtuális gép adatainak lekérése |`azure  vm show [options] <resource_group> <name>` |
| Virtuális gép elindítása |`azure vm start [options] <resource_group> <name>` |
| Virtuális gép leállítása |`azure vm stop [options] <resource_group> <name>` |
| Virtuális gép felszabadítása |`azure vm deallocate [options] <resource-group> <name>` |
| Virtuális gép újraindítása |`azure vm restart [options] <resource_group> <name>` |
| Virtuális gép törlése |`azure vm delete [options] <resource_group> <name>` |
| Virtuális gép rögzítése |`azure vm capture [options] <resource_group> <name>` |
| Virtuális gép létrehozása felhasználói rendszerképből |`azure  vm create [options] –q <image-name> <resource-group> <name> <location> <os-type>` |
| Virtuális gép létrehozása specializált lemezből |`azure  vm create [options] –d <os-disk-vhd> <resource-group> <name> <location> <os-type>` |
| Adatlemez hozzáadása egy virtuális géphez |`azure  vm disk attach-new [options] <resource-group> <vm-name> <size-in-gb> [vhd-name]` |
| Adatlemez eltávolítása egy virtuális gépből |`azure  vm disk detach [options] <resource-group> <vm-name> <lun>` |
| Általános bővítmény hozzáadása egy virtuális géphez |`azure  vm extension set [options] <resource-group> <vm-name> <name> <publisher-name> <version>` |
| Virtuálisgép-hozzáférési bővítmény hozzáadása egy virtuális géphez |`azure vm reset-access [options] <resource-group> <name>` |
| Docker-bővítmény hozzáadása egy virtuális géphez |`azure  vm docker create [options] <resource-group> <name> <location> <os-type>` |
| Virtuálisgép-bővítmény eltávolítása |`azure  vm extension set [options] –u <resource-group> <vm-name> <name> <publisher-name> <version>` |
| A virtuálisgép-erőforrások használatának megtekintése |`azure vm list-usage [options] <location>` |
| Minden elérhető virtuálisgép-méret megtekintése |`azure vm sizes [options]` |

## <a name="next-steps"></a>További lépések
* Az alapszintű virtuálisgép-kezelésen túlmutató CLI-parancsok további példáit [az Aure CLI az Azure Resource Managerrel történő használatát](../articles/virtual-machines/azure-cli-arm-commands.md) ismertető cikkből ismerheti meg.
