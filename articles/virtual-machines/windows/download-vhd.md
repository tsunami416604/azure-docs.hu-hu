---
title: Windows rendszerű virtuális merevlemez letöltése az Azure-ból
description: Töltse le a Windows VHD-t a Azure Portal használatával.
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/13/2019
ms.author: cynthn
ms.openlocfilehash: 5c57ac0fb0ab377f00770e3c9c03a413dcb345bc
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87291031"
---
# <a name="download-a-windows-vhd-from-azure"></a>Windows rendszerű virtuális merevlemez letöltése az Azure-ból

Ebből a cikkből megtudhatja, hogyan tölthet le egy Windows rendszerű virtuális merevlemezt (VHD-fájlt) az Azure-ból a Azure Portal használatával.

## <a name="optional-generalize-the-vm"></a>Nem kötelező: a virtuális gép általánosítása

Ha a virtuális merevlemezt [képként](tutorial-custom-images.md) szeretné létrehozni más virtuális gépek létrehozásához, akkor a [sysprept](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) kell használnia az operációs rendszer általánosítása érdekében. 

Ha a virtuális merevlemezt képként szeretné használni más virtuális gépek létrehozásához, általánosítsa a virtuális gépet.

1. Ha még nem tette meg, jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. [Kapcsolódjon a virtuális géphez](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
3. A virtuális gépen nyissa meg rendszergazdaként a parancssorablakot.
4. Módosítsa a könyvtárat *%WINDIR%\system32\sysprep* , és futtassa sysprep.exe.
5. A rendszer-előkészítő eszköz párbeszédpanelen jelölje be a rendszerindítási folyamat **(OOBE) megadása**jelölőnégyzetet, és győződjön meg arról, hogy az **általánosítás** van kiválasztva.
6. A leállítási beállítások területen válassza a **Leállítás**lehetőséget, majd kattintson **az OK**gombra. 


## <a name="stop-the-vm"></a>A virtuális gép leállítása

Egy virtuális merevlemez nem tölthető le az Azure-ból, ha egy futó virtuális géphez van csatlakoztatva. A virtuális merevlemez letöltéséhez le kell állítania a virtuális gépet. 

1. A Azure Portal központi menüjében kattintson az **Virtual Machines**elemre.
1. Válassza ki a virtuális gépet a listából.
1. A virtuális gép paneljén kattintson a **Leállítás**gombra.


## <a name="generate-download-url"></a>Letöltési URL-cím előállítása

A VHD-fájl letöltéséhez egy [közös hozzáférési aláírás (SAS)](../../storage/common/storage-sas-overview.md?toc=/azure/virtual-machines/windows/toc.json) URL-címét kell létrehoznia. Az URL-cím létrehozásakor a rendszer lejárati időt rendel az URL-címhez.

1. A virtuális gép lapján kattintson a bal oldali menüben a **lemezek** elemre.
1. Válassza ki a virtuális gép operációsrendszer-lemezét.
1. A lemez lapján válassza a bal oldali menü **lemez exportálása** elemét.
1. Az URL-cím alapértelmezett lejárati ideje *3600* másodperc. Növelje a **36000** -et a Windows operációsrendszer-lemezek esetében.
1. Kattintson az **URL-cím előállítása**gombra.

> [!NOTE]
> A lejárati idő megnő az alapértelmezetttől, hogy elegendő időt biztosítson a nagyméretű VHD-fájl letöltésére a Windows Server operációs rendszer számára. A Windows Server operációs rendszert tartalmazó VHD-fájl várhatóan több órányi letöltést is igénybe vehet a kapcsolódástól függően. Ha egy adatlemezre letölt egy VHD-t, az alapértelmezett idő elegendő. 
> 
> 

## <a name="download-vhd"></a>VHD letöltése

1. A létrehozott URL-cím alatt kattintson a VHD-fájl letöltése elemre.
1. Előfordulhat, hogy a letöltés indításához a böngészőben a **Mentés** gombra kell kattintania. A VHD-fájl alapértelmezett neve *ABCD*.

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan [tölthet fel egy VHD-fájlt az Azure-](upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)ba. 
- [Felügyelt lemezek létrehozása a nem felügyelt lemezekről egy Storage-fiókban](attach-disk-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- [Azure-lemezek kezelése a PowerShell](tutorial-manage-data-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)-lel.
