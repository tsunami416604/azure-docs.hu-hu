---
title: Azure CLI-vel Linux rendszerű virtuális gép másolása |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egy másolatot az Azure Linux virtuális gépek Azure CLI és a Managed Disks használatával.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
tags: azure-resource-manager
ms.assetid: 770569d2-23c1-4a5b-801e-cddcd1375164
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/17/2018
ms.author: cynthn
ms.openlocfilehash: a9caaaa0dfe402339ba01be899073bb17de15906
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50962044"
---
# <a name="create-a-copy-of-a-linux-vm-by-using-azure-cli-and-managed-disks"></a>Linux rendszerű virtuális gép másolatának létrehozása az Azure CLI-vel és a Managed Disks használatával

Ez a cikk bemutatja, hogyan hozhat létre egy példányát az Azure linuxos virtuális gép (VM) az Azure CLI és az Azure Resource Manager üzemi modell használatával. 

Emellett [feltöltése és a egy virtuális gép létrehozása virtuális merevlemezből](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="prerequisites"></a>Előfeltételek

-   Telepítse az [Azure CLI-t](/cli/azure/install-az-cli2).

-   Jelentkezzen be az Azure-fiók [az bejelentkezési](/cli/azure/reference-index#az-login).

-   A másolás forrásaként használandó Azure virtuális gép rendelkezik.

## <a name="stop-the-source-vm"></a>A forrásoldali virtuális gép leállítása

A forrásoldali virtuális gép felszabadítása használatával [az vm deallocate](/cli/azure/vm#az-vm-deallocate).
Az alábbi példa felszabadítja a virtuális gép nevű *myVM* erőforráscsoportban *myResourceGroup*:

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

## <a name="copy-the-source-vm"></a>A forrásoldali virtuális gép másolása

A virtuális gép másolása az alapul szolgáló virtuális merevlemez másolatának létrehozása. Ez a folyamat létrehoz egy specializált virtuális merevlemezt (VHD) a forrásoldali virtuális Géppel azonos konfigurációs és beállításokat tartalmazó felügyelt lemezként.

További információ az Azure Managed Disksről: [Azure Managed Disks – áttekintés](../windows/managed-disks-overview.md). 

1.  A lista minden virtuális gép és az operációs rendszer neve lemezt [az virtuálisgép-lista](/cli/azure/vm#az-vm-list). Az alábbi példa felsorolja a nevű erőforráscsoport virtuális gépeinek *myResourceGroup*:
    
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

1.  Lemez másolása egy új felügyelt lemez létrehozásával és használatával [az lemez létrehozása](/cli/azure/disk#az-disk-create). A következő példában létrehozunk egy nevű lemez *myCopiedDisk* nevű felügyelt lemezről *myDisk*:

    ```azurecli
    az disk create --resource-group myResourceGroup \
         --name myCopiedDisk --source myDisk
    ``` 

1.  Ellenőrizze a felügyelt lemezek mostantól az erőforráscsoportban [az Lemezlista](/cli/azure/disk#az-disk-list). Az alábbi példa felsorolja a felügyelt lemezek nevű erőforráscsoportot a *myResourceGroup*:

    ```azurecli
    az disk list --resource-group myResourceGroup --output table
    ```


## <a name="set-up-a-virtual-network"></a>Egy virtuális hálózat beállítása

Az alábbi opcionális lépéseket hozzon létre egy új virtuális hálózat, alhálózat, a nyilvános IP-cím és a virtuális hálózati kártya (NIC).

Ha egy virtuális Gépet másol hibaelhárítási célokra, vagy további központi telepítéseket, előfordulhat, hogy nem használni kívánt virtuális gép egy meglévő virtuális hálózaton.

Ha szeretne egy virtuális hálózati infrastruktúra létrehozása a másolt virtuális géphez, hajtsa végre a következő néhány lépést. Ha nem szeretné a virtuális hálózat létrehozása, ugorjon a [hozzon létre egy virtuális Gépet](#create-a-vm).

1.  A virtuális hálózat létrehozása használatával [az network vnet létrehozása](/cli/azure/network/vnet#az-network-vnet-create). A következő példában létrehozunk egy nevű virtuális hálózatot *myVnet* és a egy nevű alhálózatot *mySubnet*:

    ```azurecli
    az network vnet create --resource-group myResourceGroup \
        --location eastus --name myVnet \
        --address-prefix 192.168.0.0/16 \
        --subnet-name mySubnet \
        --subnet-prefix 192.168.1.0/24
    ```

1.  Hozzon létre egy nyilvános IP-cím használatával [az network public-ip létrehozása](/cli/azure/network/public-ip#az-network-public-ip-create). Az alábbi példa létrehoz egy nyilvános IP-cím nevű *myPublicIP* a DNS-nevét *mypublicdns*. (A DNS-nevének egyedinek kell lennie, mert adjon egy egyedi nevet.)

    ```azurecli
    az network public-ip create --resource-group myResourceGroup \
        --location eastus --name myPublicIP --dns-name mypublicdns \
        --allocation-method static --idle-timeout 4
    ```

1.  A hálózati adapter létrehozása használatával [az network nic létrehozása](/cli/azure/network/nic#az-network-nic-create).
    A következő példában létrehozunk egy hálózati Adaptert *myNic* , amely csatolva van a *mySubnet* alhálózat:

    ```azurecli
    az network nic create --resource-group myResourceGroup \
        --location eastus --name myNic \
        --vnet-name myVnet --subnet mySubnet \
        --public-ip-address myPublicIP
    ```

## <a name="create-a-vm"></a>Virtuális gép létrehozása

A virtuális gép létrehozása [az virtuális gép létrehozása](/cli/azure/vm#az-vm-create).

Adja meg az operációsrendszer-lemez használatára a másolt felügyelt lemez (`--attach-os-disk`), az alábbiak szerint:

```azurecli
az vm create --resource-group myResourceGroup \
    --name myCopiedVM --nics myNic \
    --size Standard_DS1_v2 --os-type Linux \
    --attach-os-disk myCopiedDisk
```

## <a name="next-steps"></a>További lépések

Az új virtuális gép kezelése az Azure CLI használatával kapcsolatban lásd: [Azure CLI-parancsok az Azure Resource Manager](../azure-cli-arm-commands.md).
