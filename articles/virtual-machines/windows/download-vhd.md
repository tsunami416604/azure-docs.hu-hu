---
title: Windows rendszerű virtuális merevlemez letöltése az Azure-ból | Microsoft Docs
description: Töltse le a Windows VHD-t a Azure Portal használatával.
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
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: c1c09382102045dd248b6771d8d0ea1ef090b6eb
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70079625"
---
# <a name="download-a-windows-vhd-from-azure"></a>Windows rendszerű virtuális merevlemez letöltése az Azure-ból

Ebből a cikkből megtudhatja, hogyan tölthet le egy Windows rendszerű virtuális merevlemezt (VHD-fájlt) az Azure-ból a Azure Portal használatával.

## <a name="stop-the-vm"></a>A virtuális gép leállítása

Egy virtuális merevlemez nem tölthető le az Azure-ból, ha egy futó virtuális géphez van csatlakoztatva. A virtuális merevlemez letöltéséhez le kell állítania a virtuális gépet. Ha a virtuális merevlemezt [képként](tutorial-custom-images.md) szeretné létrehozni más virtuális gépek új lemezekkel való létrehozásához, a [Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) segítségével általánosíthatja a fájlban található operációs rendszert, majd leállíthatja a virtuális gépet. Ha a virtuális merevlemezt lemezként szeretné használni egy meglévő virtuális gép vagy adatlemez új példánya számára, csak le kell állítania és felszabadítani kell a virtuális gépet.

Ha a virtuális merevlemezt képként szeretné használni más virtuális gépek létrehozásához, hajtsa végre a következő lépéseket:

1.  Ha még nem tette meg, jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2.  [Kapcsolódjon a virtuális géphez](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
3.  A virtuális gépen nyissa meg rendszergazdaként a parancssorablakot.
4.  Módosítsa a könyvtárat a *%WINDIR%\system32\sysprep* , és futtassa a Sysprep. exe fájlt.
5.  A rendszer-előkészítő eszköz párbeszédpanelen jelölje be a rendszerindítási folyamat **(OOBE) megadása**jelölőnégyzetet, és győződjön meg arról, hogy az **általánosítás** van kiválasztva.
6.  A leállítási beállítások területen válassza a **Leállítás**lehetőséget, majd kattintson **az OK**gombra. 

Ha a virtuális merevlemezt lemezként szeretné használni egy meglévő virtuális gép vagy adatlemez új példánya számára, hajtsa végre a következő lépéseket:

1.  A Azure Portal központi menüjében kattintson az **Virtual Machines**elemre.
2.  Válassza ki a virtuális gépet a listából.
3.  A virtuális gép paneljén kattintson a **Leállítás**gombra.

    ![Virtuális gép leállítása](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>SAS URL-cím előállítása

A VHD-fájl letöltéséhez egy [közös hozzáférési aláírás (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) URL-címét kell létrehoznia. Az URL-cím létrehozásakor a rendszer lejárati időt rendel az URL-címhez.

1.  A virtuális gép paneljének menüjében kattintson a **lemezek**elemre.
2.  Válassza ki a virtuális gép operációsrendszer-lemezét, majd kattintson a **lemez exportálása**lehetőségre.
3.  Állítsa be az URL-cím lejárati idejét *36000*-ra.
4.  Kattintson az **URL-cím**előállítása gombra.

    ![URL-cím generálása](./media/download-vhd/export-generate-new.png)

> [!NOTE]
> A lejárati idő megnő az alapértelmezetttől, hogy elegendő időt biztosítson a nagyméretű VHD-fájl letöltésére a Windows Server operációs rendszer számára. A Windows Server operációs rendszert tartalmazó VHD-fájl várhatóan több órányi letöltést is igénybe vehet a kapcsolódástól függően. Ha egy adatlemezre letölt egy VHD-t, az alapértelmezett idő elegendő. 
> 
> 

## <a name="download-vhd"></a>VHD letöltése

1.  A létrehozott URL-cím alatt kattintson a VHD-fájl letöltése elemre.

    ![VHD letöltése](./media/download-vhd/export-download.png)

2.  Előfordulhat, hogy a letöltés indításához a böngésző **Mentés** gombjára kell kattintania. A VHD-fájl alapértelmezett neve *ABCD*.

    ![Kattintson a Mentés gombra a böngészőben](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan [tölthet fel egy VHD-fájlt az Azure-](upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)ba. 
- [Felügyelt lemezek létrehozása a nem felügyelt lemezekről egy Storage-fiókban](attach-disk-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- [Azure-lemezek kezelése a PowerShell](tutorial-manage-data-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)-lel.

