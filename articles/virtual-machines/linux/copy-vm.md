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
ms.date: 09/25/2017
ms.author: cynthn
ms.openlocfilehash: 64b33fcd25582f6b1d3e7efe12aba85bb17c4cca
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46951202"
---
# <a name="create-a-copy-of-a-linux-vm-by-using-azure-cli-and-managed-disks"></a>Linux rendszerű virtuális gép másolatának létrehozása az Azure CLI-vel és a Managed Disks használatával

Ez a cikk bemutatja, hogyan hozhat létre egy példányát az Azure linuxos virtuális gép (VM) az Azure CLI és az Azure Resource Manager üzemi modell használatával. 

Emellett [feltöltése és a egy virtuális gép létrehozása virtuális merevlemezből](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="prerequisites"></a>Előfeltételek


-   Telepítés [az Azure CLI](/cli/azure/install-az-cli2)

-   Jelentkezzen be az Azure-fiók [az bejelentkezési](/cli/azure/reference-index#az_login).

-   A másolás forrásaként használandó Azure virtuális gép rendelkezik.

## <a name="step-1-stop-the-source-vm"></a>1. lépés: A forrás virtuális gép leállítása


A forrásoldali virtuális gép felszabadítása használatával [az vm deallocate](/cli/azure/vm#az_vm_deallocate).
Az alábbi példa felszabadítja a virtuális gép nevű **myVM** erőforráscsoportban **myResourceGroup**:

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

## <a name="step-2-copy-the-source-vm"></a>2. lépés: A forrás virtuális gép másolása


A virtuális gép másolása az alapul szolgáló virtuális merevlemez másolatának létrehozása. Ez a folyamat specializált virtuális Merevlemezt készítünk egy felügyelt lemez, amely tartalmazza a konfigurációs és a beállítások a forrásoldali virtuális Géppel.

További információ az Azure Managed Disksről: [Azure Managed Disks – áttekintés](../windows/managed-disks-overview.md). 

1.  A lista minden virtuális gép és az operációs rendszer neve lemezt [az virtuálisgép-lista](/cli/azure/vm#az_vm_list). Az alábbi példa felsorolja a nevű erőforráscsoport virtuális gépeinek **myResourceGroup**:
    
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

1.  Másolás a lemezt, hozzon létre egy új felügyelt lemez használata [az lemez létrehozása](/cli/azure/disk#az_disk_create). A következő példában létrehozunk egy nevű lemez **myCopiedDisk** nevű felügyelt lemezről **myDisk**:

    ```azurecli
    az disk create --resource-group myResourceGroup \
         --name myCopiedDisk --source myDisk
    ``` 

1.  Ellenőrizze a felügyelt lemezek mostantól az erőforráscsoportban [az Lemezlista](/cli/azure/disk#az_disk_list). Az alábbi példa felsorolja a felügyelt lemezek nevű erőforráscsoportot a **myResourceGroup**:

    ```azurecli
    az disk list --resource-group myResourceGroup --output table
    ```


## <a name="step-3-set-up-a-virtual-network"></a>3. lépés: Virtuális hálózat beállítása


Az alábbi opcionális lépéseket hozzon létre egy új virtuális hálózat, alhálózat, a nyilvános IP-cím és a virtuális hálózati kártya (NIC).

Ha egy virtuális Gépet másol hibaelhárítási célokra, vagy további központi telepítéseket, előfordulhat, hogy nem használni kívánt virtuális gép egy meglévő virtuális hálózaton.

Ha szeretne egy virtuális hálózati infrastruktúra létrehozása a másolt virtuális géphez, hajtsa végre a következő néhány lépést. Ha nem szeretné a virtuális hálózat létrehozása, ugorjon a [4. lépés: hozzon létre egy virtuális Gépet](#step-4-create-a-vm).

1.  A virtuális hálózat létrehozása használatával [az network vnet létrehozása](/cli/azure/network/vnet#az_network_vnet_create). A következő példában létrehozunk egy nevű virtuális hálózatot **myVnet** és a egy nevű alhálózatot **mySubnet**:

    ```azurecli
    az network vnet create --resource-group myResourceGroup \
        --location eastus --name myVnet \
        --address-prefix 192.168.0.0/16 \
        --subnet-name mySubnet \
        --subnet-prefix 192.168.1.0/24
    ```

1.  Hozzon létre egy nyilvános IP-cím használatával [az network public-ip létrehozása](/cli/azure/network/public-ip#az_network_public_ip_create). Az alábbi példa létrehoz egy nyilvános IP-cím nevű **myPublicIP** a DNS-nevét **mypublicdns**. (A DNS-nevének egyedinek kell lenniük, ezért adjon meg egy egyedi nevet.)

    ```azurecli
    az network public-ip create --resource-group myResourceGroup \
        --location eastus --name myPublicIP --dns-name mypublicdns \
        --allocation-method static --idle-timeout 4
    ```

1.  Hozzon létre a hálózati adapter [az network nic létrehozása](/cli/azure/network/nic#az_network_nic_create).
    A következő példában létrehozunk egy hálózati Adaptert **myNic** , amely csatolva van a **mySubnet** alhálózat:

    ```azurecli
    az network nic create --resource-group myResourceGroup \
        --location eastus --name myNic \
        --vnet-name myVnet --subnet mySubnet \
        --public-ip-address myPublicIP
    ```

## <a name="step-4-create-a-vm"></a>4. lépés: A virtuális gép létrehozása

Most már létrehozhat egy virtuális gép használatával [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create).

Adja meg az operációsrendszer-lemez használatára a másolt felügyelt lemez (--csatolása operációsrendszer-lemez), az alábbiak szerint:

```azurecli
az vm create --resource-group myResourceGroup \
    --name myCopiedVM --nics myNic \
    --size Standard_DS1_v2 --os-type Linux \
    --attach-os-disk myCopiedDisk
```

## <a name="next-steps"></a>További lépések

Az új virtuális gép kezelése az Azure CLI használatával kapcsolatban lásd: [Azure CLI-parancsok az Azure Resource Manager](../azure-cli-arm-commands.md).
