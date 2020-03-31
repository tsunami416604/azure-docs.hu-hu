---
title: Linux os virtuális merevlemez letöltése az Azure-ból
description: Töltsön le egy Linux-virtuális merevlemezt az Azure CLI és az Azure Portal használatával.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 08/21/2019
ms.author: cynthn
ms.openlocfilehash: 02c3ee483e6a31960fd5123070a49f568ac4c690
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968801"
---
# <a name="download-a-linux-vhd-from-azure"></a>Linux os virtuális merevlemez letöltése az Azure-ból

Ebben a cikkben megtudhatja, hogyan tölthet le egy Linux-alapú virtuális merevlemez-fájlt (VHD) az Azure CLI és az Azure Portal használatával. 

Ha még nem tette meg, telepítse az [Azure CLI](https://docs.microsoft.com/cli/azure/install-az-cli2)alkalmazást.

## <a name="stop-the-vm"></a>A virtuális gép leállítása

A virtuális merevlemez nem tölthető le az Azure-ból, ha egy futó virtuális géphez van csatolva. Le kell állítania a virtuális gép a virtuális merevlemez letöltéséhez. Ha egy virtuális merevlemezt szeretne [lemezképként](tutorial-custom-images.md) használni más virtuális gépek létrehozásához új lemezekkel, meg kell szüntetnie és általánosítania kell a fájlban található operációs rendszert, és le kell állítania a virtuális gépet. A virtuális merevlemez lemezként való használatához egy meglévő virtuális gép vagy adatlemez új példányához csak le kell állítania és fel kell osztania a virtuális gép.

Ha a virtuális merevlemezt lemezképként szeretné használni más virtuális gépek létrehozásához, hajtsa végre az alábbi lépéseket:

1. Használja az SSH-t, a fiók nevét és a virtuális gép nyilvános IP-címét, hogy csatlakozzon hozzá, és megszüntetheti azt. Megtalálható a nyilvános IP-címet [az az hálózati public-ip show](https://docs.microsoft.com/cli/azure/network/public-ip#az-network-public-ip-show). A +user paraméter eltávolítja az utolsó kiépített felhasználói fiókot is. Ha a virtuális gépfiók hitelesítő adatait sütés közben használja, hagyja ki ezt a +user paramétert. A következő példa eltávolítja az utoljára kiépített felhasználói fiókot:

    ```bash
    ssh azureuser@<publicIpAddress>
    sudo waagent -deprovision+user -force
    exit 
    ```

2. Jelentkezzen be Azure-fiókjába [az az bejelentkezéssel.](https://docs.microsoft.com/cli/azure/reference-index)
3. Állítsa le és szabadítsa fel a virtuális gép felszabadítását.

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

4. Általánosítsa a virtuális gép. 

    ```azurecli
    az vm generalize --resource-group myResourceGroup --name myVM
    ``` 

Ha a virtuális merevlemezt lemezként szeretné használni egy meglévő virtuális gép vagy adatlemez új példányához, hajtsa végre az alábbi lépéseket:

1.  Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
2.  A bal oldali menüben válassza a **Virtuális gépek**lehetőséget.
3.  Válassza ki a virtuális gép a listából.
4.  A virtuális gép lapján válassza a **Leállítás**lehetőséget.

    ![VM leállítása](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>SAS-URL létrehozása

A VHD-fájl letöltéséhez [létre](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) kell hoznia egy sas-url-címet. Az URL-cím létrehozásakor a lejárati idő hozzá van rendelve az URL-hez.

1.  A virtuális gép lapmenüjében válassza a **Lemezek**lehetőséget.
2.  Jelölje ki a virtuális gép operációsrendszer-lemezét, majd válassza a **Lemezexportálás lehetőséget.**
3.  Válassza **az URL létrehozása**lehetőséget.

    ![URL létrehozása](./media/download-vhd/export-generate.png)

## <a name="download-vhd"></a>A VHD letöltése

1.  A létrehozott URL-cím alatt válassza **a VHD fájl letöltése**lehetőséget.
**
    ![A VHD letöltése](./media/download-vhd/export-download.png)

2.  Előfordulhat, hogy a letöltés elindításához a **Mentés** a böngészőben lehetőséget kell választania. A virtuális merevlemez-fájl alapértelmezett neve *abcd*.

    ![Válassza a Mentés a böngészőben lehetőséget](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [tölthet fel és hozhat létre Linux virtuális gépet egyéni lemezről az Azure CLI segítségével.](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 
- [Az Azure-lemezek kezelése az Azure CLI.](tutorial-manage-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

