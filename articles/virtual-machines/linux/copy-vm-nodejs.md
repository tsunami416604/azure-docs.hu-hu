---
title: "Hozzon létre egy másolatot a Linux virtuális Gépet az Azure CLI 1.0 |} Microsoft Docs"
description: "Megtudhatja, hogyan hozzon létre egy példányát az Azure Linux virtuális gép az Azure CLI 1.0 a Resource Manager üzembe helyezési modellel"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
tags: azure-resource-manager
ms.assetid: 770569d2-23c1-4a5b-801e-cddcd1375164
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/22/2017
ms.author: cynthn
ms.openlocfilehash: 62ae54f3596c9383cbf3b401fcfdb42ecfdee63c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-copy-of-a-linux-virtual-machine-running-on-azure-with-the-azure-cli-10"></a>Az Azure CLI 1.0 az Azure-on futó Linux virtuális gépek másolatának létrehozása
Ez a cikk bemutatja, hogyan hozzon létre egy másolatot Linux a Resource Manager üzembe helyezési modellel rendszert futtató Azure virtuális gép (VM). Először másolja át az operációs rendszer és az adatlemezek új tárolót, majd állítsa be a hálózati erőforrások, majd az új virtuális gép létrehozása.

Emellett [töltse fel, és hozzon létre egy virtuális Gépet az egyéni lemezképet](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="cli-versions-to-complete-the-task"></a>A feladat befejezéséhez használható CLI-verziók
A következő CLI-verziók egyikével elvégezheti a feladatot:

- Azure CLI 1.0 – parancssori felületünk a klasszikus és a Resource Management üzemi modellekhez (a jelen cikkben)
- [Azure CLI 2.0](copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – a Resource Management üzemi modellhez tartozó parancssori felületek következő generációját képviseli.

## <a name="before-you-begin"></a>Előkészületek
Győződjön meg arról, hogy teljesülnek a következő előfeltételek teljesülését, a lépések megkezdése előtt:

* Rendelkezik a [Azure CLI](../../cli-install-nodejs.md) letöltötte és telepítette a számítógépen. 
* A meglévő Azure Linux virtuális Gépre vonatkozó kell néhány információt is:

| Forrás Virtuálisgép-adatok | Letöltés helye |
| --- | --- |
| a virtuális gép neve |`azure vm list` |
| Erőforráscsoport neve |`azure vm list` |
| Hely |`azure vm list` |
| A tárfiók neve |`azure storage account list -g <resourceGroup>` |
| Tárolónév |`azure storage container list -a <sourcestorageaccountname>` |
| Forrásfájl virtuális gép virtuális merevlemez neve |`azure storage blob list --container <containerName>` |

* Néhány az új virtuális gép kapcsolatos beállítások lesz szüksége:   <br> -Tároló neve   <br> Virtuálisgép - nevet   <br> Virtuálisgép - méret   <br> -vNet neve   <br> -Alhálózat neve   <br> -IP név   <br> -Hálózati név

## <a name="login-and-set-your-subscription"></a>Bejelentkezés és az előfizetés beállítása
1. A parancssori felület bejelentkezni.

    ```azurecli
    azure login
    ```
2. Győződjön meg arról, hogy az erőforrás-kezelő módban van.

    ```azurecli
    azure config mode arm
    ```
3. Állítsa be a megfelelő előfizetés. Használhatja a "list azure-fiók" az összes az előfizetések megtekintéséhez.

    ```azurecli
    azure account set mySubscriptionID
    ```

## <a name="stop-the-vm"></a>A virtuális gép leállítása
Állítsa le, és a forrás virtuális gép felszabadítása. Használhatja a "list azure virtuális gép" listájának az összes, a virtuális gépek az előfizetés és az erőforrás nevének.

```azurecli
azure vm stop myResourceGroup myVM
azure vm deallocate myResourceGroup MyVM
```


## <a name="copy-the-vhd"></a>Másolja a VHD-
Másolhatja a VHD-t a forrás tárolási a cél történő a `azure storage blob copy start`. Ebben a példában fogjuk másolja a VHD-ugyanazt a tárfiókot, de egy másik tárolóban.

Másolja a VHD-ugyanazt a tárfiókot egy másik tárolóhoz, írja be:

```azurecli
azure storage blob copy start \
        https://mystorageaccountname.blob.core.windows.net:8080/mycontainername/myVHD.vhd \
        myNewContainerName
```

## <a name="set-up-the-virtual-network-for-your-new-vm"></a>Az új virtuális gép a virtuális hálózat beállítása
A virtuális hálózat és a hálózati adapter beállítása az új virtuális gép. 

```azurecli
azure network vnet create myResourceGroup myVnet -l myLocation

azure network vnet subnet create -a <address.prefix.in.CIDR/format> myResourceGroup myVnet mySubnet

azure network public-ip create myResourceGroup myPublicIP -l myLocation

azure network nic create myResourceGroup myNic -k mySubnet -m myVnet -p myPublicIP -l myLocation
```


## <a name="create-the-new-vm"></a>Az új virtuális gép létrehozása
Mostantól létrehozhat egy virtuális Gépet a feltöltött virtuális lemez [resource manager-sablon használatával](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd) vagy a CLI-t, írja be a másolt lemezhez URI megadásával:

```azurecli
azure vm create -n myVM -l myLocation -g myResourceGroup -f myNic \
        -z Standard_DS1_v2 -y Linux \
        https://mystorageaccountname.blob.core.windows.net:8080/mycontainername/myVHD.vhd 
```



## <a name="next-steps"></a>Következő lépések
Azure CLI használata az új virtuális gép kezelése című témakörben talál [Azure parancssori felület parancsait az Azure Resource Manager](../azure-cli-arm-commands.md).

