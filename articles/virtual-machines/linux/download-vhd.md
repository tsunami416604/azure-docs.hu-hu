---
title: Linux rendszerű VHD letöltése az Azure-ból |} A Microsoft Docs
description: Töltse le a Linux rendszerű VHD-t az Azure CLI és az Azure portal használatával.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: f72d49a3ab204ce64eb89d0f05630b640c138e0a
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2019
ms.locfileid: "56329251"
---
# <a name="download-a-linux-vhd-from-azure"></a>Linux rendszerű VHD letöltése az Azure-ból

Ebből a cikkből megismerheti, hogyan töltheti le egy Linux rendszerű virtuális merevlemez (VHD) fájl Azure-ban az Azure CLI-vel és az Azure portal használatával. 

Ha ezt még nem tette meg, telepítse a [Azure CLI-vel](https://docs.microsoft.com/cli/azure/install-az-cli2).

## <a name="stop-the-vm"></a>A virtuális gép leállítása

Virtuális merevlemez nem lehet letölteni az Azure-ból, ha egy futó virtuális Géphez van csatlakoztatva. Szeretné megszüntetni a virtuális gép virtuális merevlemez letöltésére. Ha azt szeretné, mint egy virtuális merevlemez használata egy [kép](tutorial-custom-images.md) új lemezeket hoz létre a többi virtuális géphez, kell megszüntetése és általánosítja az operációs rendszert, a fájlban található, és a virtuális gép leállítása. A VHD lemez egy meglévő virtuális gép vagy az adatlemez egy új példányát, használatához csak kell leállítja és felszabadítja a virtuális Gépet.

A virtuális merevlemez használata képként más virtuális gépek létrehozásához, a lépések végrehajtása:

1. SSH, a fiók nevét és a virtuális gép nyilvános IP-cím segítségével csatlakozzon hozzá, és szüntetheti meg azt. A nyilvános IP-címet annak [az network public-ip show](https://docs.microsoft.com/cli/azure/network/public-ip#az-network-public-ip-show). A + felhasználói paramétert is eltávolítja az utoljára kiépített felhasználói fiókot. Ha a fiók hitelesítő adatait a virtuális géphez vannak sütés, hagyja meg ezt + felhasználói paraméter. A következő példa eltávolítja az utoljára kiépített felhasználói fiókot:

    ```bash
    ssh azureuser@<publicIpAddress>
    sudo waagent -deprovision+user -force
    exit 
    ```

2. Jelentkezzen be az Azure-fiókjába [az bejelentkezési](https://docs.microsoft.com/cli/azure/reference-index).
3. Leállítja és felszabadítja a virtuális Gépet.

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

4. A virtuális gép általánosítása. 

    ```azurecli
    az vm generalize --resource-group myResourceGroup --name myVM
    ``` 

A VHD lemez egy meglévő virtuális gép vagy az adatlemez egy új példányát használja, hajtsa végre ezeket a lépéseket:

1.  Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2.  A központi menüben kattintson a **Virtuális gépek** elemre.
3.  Válassza ki a virtuális Gépet a listából.
4.  A virtuális gép paneljén kattintson **leállítása**.

    ![Virtuális gép leállítása](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>SAS URL-címet generálni

A VHD-fájl letöltéséhez, létre kell hoznia egy [közös hozzáférésű jogosultságkód (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) URL-CÍMÉT. Az URL-cím akkor jön létre, amikor egy lejárati idővel az URL-cím van hozzárendelve.

1.  Kattintson a menü, a virtuális gép paneljének **lemezek**.
2.  Válassza ki az operációsrendszer-lemez a virtuális gép, és kattintson **exportálása**.
3.  Kattintson a **URL-címet generálni**.

    ![URL-cím generálása](./media/download-vhd/export-generate.png)

## <a name="download-vhd"></a>Töltse le a virtuális merevlemez

1.  Kattintson a létrehozott URL-CÍMÉT, a VHD-fájl letöltése.

    ![Töltse le a virtuális merevlemez](./media/download-vhd/export-download.png)

2.  Szükség lehet kattintson **mentése** a böngészőben a letöltés megkezdéséhez. A VHD-fájl alapértelmezett neve: *abcd*.

    ![Kattintson a Mentés gombra a böngészőben](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [feltöltése és a egy Linux virtuális gép létrehozása az Azure CLI-vel egyéni lemezről](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- [Az Azure CLI Azure-lemezek kezelése](tutorial-manage-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

