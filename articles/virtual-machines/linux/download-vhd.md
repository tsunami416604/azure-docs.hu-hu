---
title: Linuxos virtuális merevlemez letöltése az Azure-ból
description: Töltsön le egy linuxos virtuális merevlemezt az Azure CLI és a Azure Portal használatával.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 08/21/2019
ms.author: cynthn
ms.openlocfilehash: 257f3f723fc8a971b8253699f4beb002cf46ce52
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036295"
---
# <a name="download-a-linux-vhd-from-azure"></a>Linuxos virtuális merevlemez letöltése az Azure-ból

Ebből a cikkből megtudhatja, hogyan tölthet le egy linuxos virtuális merevlemezt (VHD-fájlt) az Azure-ból az Azure CLI és a Azure Portal használatával. 

Ha még nem tette meg, telepítse az [Azure CLI](https://docs.microsoft.com/cli/azure/install-az-cli2)-t.

## <a name="stop-the-vm"></a>A virtuális gép leállítása

Egy virtuális merevlemez nem tölthető le az Azure-ból, ha egy futó virtuális géphez van csatlakoztatva. A virtuális merevlemez letöltéséhez le kell állítania a virtuális gépet. Ha a virtuális merevlemezt [képként](tutorial-custom-images.md) szeretné létrehozni más virtuális gépek új lemezekkel való létrehozásához, meg kell szüntetnie és általánosítani a fájlban található operációs rendszert, és le kell állítania a virtuális gépet. Ha a virtuális merevlemezt lemezként szeretné használni egy meglévő virtuális gép vagy adatlemez új példánya számára, csak le kell állítania és felszabadítani kell a virtuális gépet.

Ha a virtuális merevlemezt képként szeretné használni más virtuális gépek létrehozásához, hajtsa végre a következő lépéseket:

1. Használja az SSH-t, a fiók nevét és a virtuális gép nyilvános IP-címét, hogy csatlakozhasson hozzá, és kiépítse azt. A nyilvános IP-címet az [az Network Public-IP show](https://docs.microsoft.com/cli/azure/network/public-ip#az-network-public-ip-show)paranccsal érheti el. A + User paraméter eltávolítja az utolsó kiosztott felhasználói fiókot is. Ha a fiók hitelesítő adatait a virtuális gépre veszi fel, hagyja ki ezt a + felhasználói paramétert. Az alábbi példa eltávolítja az utolsó kiépített felhasználói fiókot:

    ```bash
    ssh azureuser@<publicIpAddress>
    sudo waagent -deprovision+user -force
    exit 
    ```

2. Jelentkezzen be az Azure-fiókjába az [az login](https://docs.microsoft.com/cli/azure/reference-index)paranccsal.
3. Állítsa le és szabadítsa fel a virtuális gépet.

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

4. A virtuális gép általánosítása. 

    ```azurecli
    az vm generalize --resource-group myResourceGroup --name myVM
    ``` 

Ha a virtuális merevlemezt lemezként szeretné használni egy meglévő virtuális gép vagy adatlemez új példánya számára, hajtsa végre a következő lépéseket:

1.  Bejelentkezés az [Azure Portalra](https://portal.azure.com/).
2.  A bal oldali menüben válassza a **Virtual Machines**lehetőséget.
3.  Válassza ki a virtuális gépet a listából.
4.  A virtuális gép lapján válassza a **Leállítás**lehetőséget.

    ![Virtuális gép leállítása](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>SAS URL-cím előállítása

A VHD-fájl letöltéséhez egy [közös hozzáférési aláírás (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) URL-címét kell létrehoznia. Az URL-cím létrehozásakor a rendszer lejárati időt rendel az URL-címhez.

1.  A virtuális gép oldalának menüjében válassza a **lemezek**lehetőséget.
2.  Válassza ki a virtuális gép operációsrendszer-lemezét, majd válassza a **lemez exportálása**lehetőséget.
3.  Válassza az **URL-cím előállítása**lehetőséget.

    ![URL-cím előállítása](./media/download-vhd/export-generate.png)

## <a name="download-vhd"></a>VHD letöltése

1.  A létrehozott URL-cím alatt válassza **a VHD-fájl letöltése**lehetőséget.
**
    ![VHD-fájl letöltése](./media/download-vhd/export-download.png)

2.  Előfordulhat, hogy a letöltés indításához a böngésző **Mentés** elemét kell választania. A VHD-fájl alapértelmezett neve *ABCD*.

    ![Válassza a mentés lehetőséget a böngészőben](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan [tölthet fel és hozhat létre Linux rendszerű virtuális gépet egyéni lemezről az Azure CLI-vel](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- [Azure-lemezek kezelése az Azure CLI](tutorial-manage-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)-vel.

