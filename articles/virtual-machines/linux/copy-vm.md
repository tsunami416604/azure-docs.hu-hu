---
title: Linuxos virtuális gép másolása az Azure CLI használatával
description: Ismerje meg, hogyan hozhat létre másolatot az Azure Linux virtuális gépről az Azure CLI és a felügyelt lemezek használatával.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 10/17/2018
ms.author: cynthn
ms.openlocfilehash: ed8574133eafe751699e90ea8cae832ee649fb00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969586"
---
# <a name="create-a-copy-of-a-linux-vm-by-using-azure-cli-and-managed-disks"></a>Linuxos virtuális gép másolatának létrehozása az Azure CLI és a Felügyelt lemezek használatával

Ez a cikk bemutatja, hogyan hozhat létre egy másolatot az Azure virtuális gép (VM) Linux linuxos az Azure CLI és az Azure Resource Manager telepítési modell használatával. 

Virtuális [gép is feltölthető és létrehozhat virtuális merevlemezről.](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="prerequisites"></a>Előfeltételek

-   Telepítse az [Azure CLI](/cli/azure/install-az-cli2).

-   Jelentkezzen be egy Azure-fiókba [az a bejelentkezéssel.](/cli/azure/reference-index#az-login)

-   Rendelkeznie kell egy Azure virtuális gép a példány forrásaként.

## <a name="stop-the-source-vm"></a>A forrásvirtuális gép leállítása

A forrásvirtuális gép felszabadítása az [vm felszabadításával.](/cli/azure/vm#az-vm-deallocate)
A következő példa felszabadítja a *myVM* nevű virtuális gép a *myResourceGroup*erőforráscsoportban:

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

## <a name="copy-the-source-vm"></a>A forrás virtuális gépének másolása

Virtuális gép másolásához hozzon létre egy másolatot az alapul szolgáló virtuális merevlemezről. Ez a folyamat egy speciális virtuális merevlemezt (VHD) hoz létre felügyelt lemezként, amely ugyanazt a konfigurációt és beállításokat tartalmazza, mint a forrás virtuális gép.

További információ az Azure Managed Disksről: [Azure Managed Disks – áttekintés](../windows/managed-disks-overview.md). 

1.  Sorolja fel az egyes virtuális gépeket és az operációsrendszer-lemez nevét az [az vm listával.](/cli/azure/vm#az-vm-list) A következő példa a *myResourceGroup*nevű erőforráscsoport összes virtuális gépét sorolja fel:
    
    ```azurecli
    az vm list -g myResourceGroup \
         --query '[].{Name:name,DiskName:storageProfile.osDisk.name}' \
         --output table
    ```

    A kimenet a következő példához hasonló:

    ```azurecli
    Name    DiskName
    ------  --------
    myVM    myDisk
    ```

1.  Másolja a lemezt új felügyelt lemez létrehozásával és az [az lemezlétrehozása](/cli/azure/disk#az-disk-create)használatával. A következő példa létrehoz egy *myCopiedDisk* nevű lemezt a *myDisk*nevű felügyelt lemezről:

    ```azurecli
    az disk create --resource-group myResourceGroup \
         --name myCopiedDisk --source myDisk
    ``` 

1.  Ellenőrizze az erőforráscsoportban lévő felügyelt lemezeket az [az lemezlista segítségével.](/cli/azure/disk#az-disk-list) A következő példa a *myResourceGroup*nevű erőforráscsoport kezelt lemezeit sorolja fel:

    ```azurecli
    az disk list --resource-group myResourceGroup --output table
    ```


## <a name="set-up-a-virtual-network"></a>Virtuális hálózat beállítása

A következő választható lépések új virtuális hálózatot, alhálózatot, nyilvános IP-címet és virtuális hálózati kártya (NIC) létrehozását hozzák létre.

Ha hibaelhárítási célokra vagy további központi telepítésekhez másolja a virtuális gépet, előfordulhat, hogy nem szeretne virtuális gépet használni egy meglévő virtuális hálózatban.

Ha virtuális hálózati infrastruktúrát szeretne létrehozni a másolt virtuális gépekhez, kövesse a következő néhány lépést. Ha nem szeretne virtuális hálózatot létrehozni, ugorjon [a Virtuális gép létrehozása](#create-a-vm)elemre.

1.  Hozza létre a virtuális hálózatot az [az hálózati virtuális hálózat létrehozása](/cli/azure/network/vnet#az-network-vnet-create)használatával. A következő példa létrehoz egy *myVnet* nevű virtuális hálózatot és egy *mySubnet*nevű alhálózatot:

    ```azurecli
    az network vnet create --resource-group myResourceGroup \
        --location eastus --name myVnet \
        --address-prefix 192.168.0.0/16 \
        --subnet-name mySubnet \
        --subnet-prefix 192.168.1.0/24
    ```

1.  Hozzon létre egy nyilvános IP segítségével [az hálózati nyilvános ip create](/cli/azure/network/public-ip#az-network-public-ip-create). A következő példa létrehoz egy nyilvános IP nevű *myPublicIP* a DNS neve *mypublicdns*. (Mivel a DNS-névnek egyedinek kell lennie, adjon meg egyedi nevet.)

    ```azurecli
    az network public-ip create --resource-group myResourceGroup \
        --location eastus --name myPublicIP --dns-name mypublicdns \
        --allocation-method static --idle-timeout 4
    ```

1.  Hozza létre a hálózati adaptert az [az network nic create](/cli/azure/network/nic#az-network-nic-create)használatával.
    A következő példa létrehoz egy *myNic* nevű hálózati adaptert, amely a *mySubnet* alhálózathoz van csatolva:

    ```azurecli
    az network nic create --resource-group myResourceGroup \
        --location eastus --name myNic \
        --vnet-name myVnet --subnet mySubnet \
        --public-ip-address myPublicIP
    ```

## <a name="create-a-vm"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gép segítségével [az vm create](/cli/azure/vm#az-vm-create).

Adja meg az operációs rendszer lemezeként`--attach-os-disk`( használandó másolt felügyelt lemezt az alábbiak szerint:

```azurecli
az vm create --resource-group myResourceGroup \
    --name myCopiedVM --nics myNic \
    --size Standard_DS1_v2 --os-type Linux \
    --attach-os-disk myCopiedDisk
```

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan használhat [megosztott képgalériát](shared-images.md) a virtuális géplemezképek kezeléséhez.
