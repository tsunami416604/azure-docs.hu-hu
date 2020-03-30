---
title: Windows-alapú merevlemez letöltése az Azure-ból
description: Windows-virtuális merevlemez letöltése az Azure Portalon.
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
ms.date: 01/13/2019
ms.author: cynthn
ms.openlocfilehash: d1c98fa4f3572c40279978d787b1719746478a06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75940453"
---
# <a name="download-a-windows-vhd-from-azure"></a>Windows-alapú merevlemez letöltése az Azure-ból

Ebben a cikkben megtudhatja, hogyan tölthet le egy Windows-alapú virtuális merevlemez-fájlt (VHD) az Azure-ból az Azure Portalon keresztül.

## <a name="optional-generalize-the-vm"></a>Nem kötelező: A virtuális gép általánosítása

Ha a virtuális merevlemezt [lemezképként](tutorial-custom-images.md) szeretné használni más virtuális gépek létrehozásához, a [Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) programmal általánosíthatja az operációs rendszert. 

Ha a virtuális merevlemezt lemezképként szeretné használni más virtuális gépek létrehozásához, általánosítsa a virtuális gépet.

1. Ha még nem tette meg, jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. [Csatlakozzon a virtuális géphez](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
3. A virtuális gépen nyissa meg a parancssori ablakot rendszergazdaként.
4. Módosítsa a könyvtárat *%windir%\system32\sysprep* értékre, és futtassa a sysprep.exe programot.
5. A Rendszerelőkészítő eszköz párbeszédpanelen válassza **a Kezdő élmény megadása (OOBE)** lehetőséget, és győződjön meg arról, hogy a **Generalize** beállítás van kiválasztva.
6. A Leállítási beállítások párbeszédpanelen válassza a **Leállítás**lehetőséget, majd kattintson **az OK**gombra. 


## <a name="stop-the-vm"></a>A virtuális gép leállítása

A virtuális merevlemez nem tölthető le az Azure-ból, ha egy futó virtuális géphez van csatolva. Le kell állítania a virtuális gép a virtuális merevlemez letöltéséhez. 

1. Az Azure Portal Hub menüjében kattintson a **Virtuális gépek parancsra.**
1. Válassza ki a virtuális gép a listából.
1. A virtuális gép paneljén kattintson a **Leállítás**gombra.


## <a name="generate-download-url"></a>Letöltési URL létrehozása

A VHD-fájl letöltéséhez [létre](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) kell hoznia egy sas-url-címet. Az URL-cím létrehozásakor a lejárati idő hozzá van rendelve az URL-hez.

1. A virtuális gép lapján kattintson a bal oldali menü **Lemezek parancsára.**
1. Válassza ki a virtuális gép operációsrendszer-lemezét.
1. A lemez lapján válassza a bal oldali menü **Lemezexportálás parancsát.**
1. Az URL alapértelmezett lejárati ideje *3600* másodperc. Növelje ezt **36000-re** Windows operációs rendszer lemezek esetén.
1. Kattintson **az URL létrehozása gombra.**

> [!NOTE]
> A lejárati idő az alapértelmezettállapotról növekszik, így elegendő idő áll a Windows Server operációs rendszer nagy virtuális merevlemez-fájljának letöltésére. A Windows Server operációs rendszert tartalmazó VHD-fájl letöltése a kapcsolattól függően több órát vesz igénybe. Ha egy adatlemez virtuális merevlemezét tölti le, az alapértelmezett idő elegendő. 
> 
> 

## <a name="download-vhd"></a>A VHD letöltése

1. A létrehozott URL-cím alatt kattintson a VHD-fájl letöltése elemre.
1. Előfordulhat, hogy a letöltés elindításához a **Böngészőben gombra** kell kattintania a Mentés gombra. A virtuális merevlemez-fájl alapértelmezett neve *abcd*.

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan [tölthet fel vhd-fájlt az Azure-ba.](upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 
- [Felügyelt lemezek létrehozása nem felügyelt lemezekről egy tárfiókban.](attach-disk-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
- [Az Azure-lemezek kezelése a PowerShell használatával.](tutorial-manage-data-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

