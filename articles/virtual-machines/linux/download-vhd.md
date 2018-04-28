---
title: Töltse le a Linux virtuális merevlemez az Azure-ból |} Microsoft Docs
description: Töltse le az Azure parancssori felület és az Azure-portál használatával Linux virtuális Merevlemezt.
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
ms.date: 06/26/2017
ms.author: cynthn
ms.openlocfilehash: d62fe702e42929fde2bb2a0dee4ea2cc8621ff99
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="download-a-linux-vhd-from-azure"></a>Töltse le a Linux virtuális merevlemez az Azure-ból

Ebből a cikkből megismerheti, hogyan töltheti le a [Linux virtuális merevlemez (VHD)](about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) fájl az Azure CLI és az Azure portál használata az Azure-ból. 

Virtuális gépek (VM) Azure használatban [lemezek](../windows/managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) egy olyan hely, az operációs rendszerek, alkalmazások és adatok tárolására. Minden Azure virtuális gépek legalább két lemezt – a Windows operációs rendszer és egy ideiglenes lemezzel rendelkezik. Az operációs rendszer tárolására hoztak létre, lemezképpel, és mind az operációsrendszer-lemez, és a lemezkép az Azure storage-fiókban tárolt VHD-k. Virtuális gépek is rendelkeznek legalább egy adatlemezt, virtuális merevlemezekként is tárolt.

Ha még nem tette meg, telepítse [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2).

## <a name="stop-the-vm"></a>A virtuális gép leállítása

Virtuális merevlemez nem lehet letölteni az Azure-ból, ha egy virtuális gép csatlakozik. Szeretné megszüntetni a virtuális gép virtuális merevlemez letöltéséhez. Ha szeretne használni egy virtuális Merevlemezt egy [kép](tutorial-custom-images.md) új lemez a többi virtuális gép létrehozásához, kell kiosztásának megszüntetése és általánosítja az operációs rendszert, a fájlban, és állítsa le a virtuális Gépet. A VHD-t használja, amely lemezként egy új példányát egy meglévő virtuális gép vagy adatlemez, akkor csak kell leállítása és a virtuális gép felszabadítása.

Más virtuális gépek létrehozásához használja a virtuális merevlemez képként, végezze el az alábbi lépéseket:

1. SSH, a fiók nevét és a virtuális gép nyilvános IP-címe használatával kapcsolódni hozzá, és kiosztásának megszüntetése azt. A nyilvános IP-cím található [az hálózati nyilvános ip-megjelenítése](https://docs.microsoft.com/en-us/cli/azure/network/public-ip#az-network-public-ip-show). A + felhasználói paraméter is eltávolítja az utolsó kiépített felhasználói fiókot. Ha a fiók hitelesítő adatait a virtuális géphez vannak sütés, hagyja ki ennek + a user paraméterhez. A következő példában eltávolítjuk a legutóbbi kiépített felhasználói fiók:

    ```bash
    ssh azureuser@<publicIpAddress>
    sudo waagent -deprovision+user -force
    exit 
    ```

2. Jelentkezzen be az Azure-fiókja [az bejelentkezési](https://docs.microsoft.com/cli/azure/reference-index#az_login).
3. Állítsa le, és a virtuális gép felszabadítása.

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

4. A virtuális gép generalize. 

    ```azurecli
    az vm generalize --resource-group myResourceGroup --name myVM
    ``` 

A VHD-t használja, amely lemezként egy meglévő virtuális gép vagy adatlemez egy új példányát, végezze el az alábbi lépéseket:

1.  Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2.  A központi menüben kattintson a **Virtuális gépek** elemre.
3.  Válassza ki a virtuális Gépet a listából.
4.  A virtuális gép paneljén kattintson **leállítása**.

    ![Virtuális gép leállítása](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>SAS URL-cím létrehozása

A VHD-fájl letöltésére, szeretne létrehozni egy [közös hozzáférésű jogosultságkód (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) URL-CÍMÉT. Az URL-cím generálásakor lejárati idő az URL-cím van hozzárendelve.

1.  Kattintson a panel a virtuális gép menü, **lemezek**.
2.  Válassza ki az operációs rendszer lemezt a virtuális gép számára, és kattintson **exportálása**.
3.  Kattintson a **URL-cím készítése**.

    ![URL-cím létrehozása](./media/download-vhd/export-generate.png)

## <a name="download-vhd"></a>Töltse le a virtuális merevlemez

1.  Az URL-címen hozott létre kattintson a letöltés a VHD-fájl.

    ![Töltse le a virtuális merevlemez](./media/download-vhd/export-download.png)

2.  Kattintson a szeretne **mentése** menüpontot a böngészőben a letöltés megkezdéséhez. Az alapértelmezett név a VHD-fájl *abcd*.

    ![Kattintson a Mentés gombra a böngészőben](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan [feltöltése és a Linux virtuális gép létrehozása az Azure CLI 2.0 egyéni lemezről](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- [Az Azure CLI Azure-lemezeket kezelése](tutorial-manage-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

