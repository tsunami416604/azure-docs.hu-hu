---
title: Linuxos virtuális merevlemez letöltése az Azure-ból
description: Töltsön le egy linuxos virtuális merevlemezt az Azure CLI és a Azure Portal használatával.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 08/03/2020
ms.author: cynthn
ms.openlocfilehash: 897cae53e589f4058e5499c0e6e941d4f1d9bb2f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87761056"
---
# <a name="download-a-linux-vhd-from-azure"></a>Linuxos virtuális merevlemez letöltése az Azure-ból

Ebből a cikkből megtudhatja, hogyan tölthet le egy linuxos virtuális merevlemezt (VHD-fájlt) az Azure-ból a Azure Portal használatával. 

## <a name="stop-the-vm"></a>A virtuális gép leállítása

Egy virtuális merevlemez nem tölthető le az Azure-ból, ha egy futó virtuális géphez van csatlakoztatva. A virtuális merevlemez letöltéséhez le kell állítania a virtuális gépet. 

1.  Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2.  A bal oldali menüben válassza a **Virtual Machines**lehetőséget.
3.  Válassza ki a virtuális gépet a listából.
4.  A virtuális gép lapján válassza a **Leállítás**lehetőséget.

    :::image type="content" source="./media/download-vhd/export-stop.PNG" alt-text="Megjeleníti a virtuális gép leállítására szolgáló menü gombot.":::

## <a name="generate-sas-url"></a>SAS URL-cím előállítása

A VHD-fájl letöltéséhez egy [közös hozzáférési aláírás (SAS)](../../storage/common/storage-sas-overview.md?toc=/azure/virtual-machines/windows/toc.json) URL-címét kell létrehoznia. Az URL-cím létrehozásakor a rendszer lejárati időt rendel az URL-címhez.

1. A virtuális gép oldalának menüjében válassza a **lemezek**lehetőséget.
2. Válassza ki a virtuális gép operációsrendszer-lemezét, majd válassza a **lemez exportálása**lehetőséget.
1. Ha szükséges, frissítse az **URL-cím érvényességét (másodpercben)** , hogy elegendő idő legyen a letöltés befejezésére. Az alapértelmezett érték 3600 másodperc (egy óra).
3. Válassza az **URL-cím előállítása**lehetőséget.
 
      
## <a name="download-vhd"></a>VHD letöltése

1.  A létrehozott URL-cím alatt válassza **a VHD-fájl letöltése**lehetőséget.

    :::image type="content" source="./media/download-vhd/export-download.PNG" alt-text="Megjeleníti a virtuális gép leállítására szolgáló menü gombot.":::

2.  Előfordulhat, hogy a letöltés indításához a böngésző **Mentés** elemét kell választania. A VHD-fájl alapértelmezett neve *ABCD*.

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan [tölthet fel és hozhat létre Linux rendszerű virtuális gépet egyéni lemezről az Azure CLI-vel](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- [Azure-lemezek kezelése az Azure CLI](tutorial-manage-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)-vel.
