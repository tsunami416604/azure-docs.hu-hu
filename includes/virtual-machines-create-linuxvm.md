---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: ae29451e3f7ec263f296e69656a5c66045334687
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50226753"
---
1. Jelentkezzen be az Azure-előfizetés a szükséges lépések [csatlakozás az Azure-bA az Azure klasszikus parancssori felületen](/cli/azure/authenticate-azure-cli).

2. Az alábbiak szerint győződjön meg róla, hogy a klasszikus üzembe helyezési módban van:

    ```azurecli
    azure config mode asm
    ```

3. Az alábbiak szerint keresse meg az elérhető rendszerképek közül a betölteni kívánt Linux-rendszerképeket:

   ```azurecli   
    azure vm image list | grep "Linux"
    ```
   
    Windows-parancsablakokban a grep helyett használja a **find** kifejezést.
   
4. Az `azure vm create` paranccsal hozzon létre egy virtuális gépet az előző listából kiválasztott Linux-rendszerképpel. Ezzel a lépéssel létrehoz egy felhőszolgáltatást és egy tárfiókot. A `-c` kapcsolóval egy meglévő felhőszolgáltatáshoz is csatlakoztathatja ezt a virtuális gépet. Az `-e` kapcsolóval létrehozhat egy SSH-végpontot a Linux-alapú virtuális gépre való bejelentkezéshez. Az alábbi példa egy `myVM` nevű virtuális gépet hoz létre az `Ubuntu-14_04_4-LTS` rendszerkép használatával a `West US` helyen, és hozzáadja a következő felhasználónevet: `ops`
   
    ```azurecli
    azure vm create myVM \
        b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB \
        -g ops -p P@ssw0rd! -z "Small" -e -l "West US"
    ```

    A kimenet a következő példához hasonló:

    ```azurecli
    info:    Executing command vm create
    + Looking up image b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB
    + Looking up cloud service
    info:    cloud service myVM not found.
    + Creating cloud service
    + Retrieving storage accounts
    + Creating VM
    info:    vm create command OK
    ```
   
   > [!NOTE]
   > Linuxos virtuális gép esetén meg kell adnia az `-e` kapcsolót a `vm create` parancsban. A virtuális gép létrehozása után nem lehet engedélyezni az SSH-t. További részletek az SSH-val kapcsolatban: [SSH használata Linuxon az Azure-on](../articles/virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

5. Az `azure vm show` parancs használatával ellenőrizheti a virtuális gép attribútumait. Az alábbi példa felsorolja a `myVM` nevű virtuális géppel kapcsolatos információkat:

    ```azurecli   
    azure vm show myVM
    ```

6. Indítsa el a virtuális gépet az `azure vm start` paranccsal a következőképpen:

    ```azurecli
    azure vm start myVM
    ```

## <a name="next-steps"></a>További lépések
Az összes ezeket az Azure klasszikus parancssori felület virtuális gépekre vonatkozó további tudnivalókért olvassa el a [az Azure klasszikus parancssori felület használatával a klasszikus üzembe helyezési API](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).

