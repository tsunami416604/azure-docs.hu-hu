---
title: Töltse le a virtuális merevlemez Windows Azure-ból |} A Microsoft Docs
description: Töltse le a Windows virtuális merevlemez az Azure portal használatával.
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
ms.openlocfilehash: 3d44a4a723c39bf9780475a2ac3088da94285f6e
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2019
ms.locfileid: "56329370"
---
# <a name="download-a-windows-vhd-from-azure"></a>Töltse le a virtuális merevlemez Windows Azure-ból

Ebből a cikkből megismerheti, hogyan töltheti le egy Windows virtuális merevlemez (VHD) fájlt az Azure portal használatával.

## <a name="stop-the-vm"></a>A virtuális gép leállítása

Virtuális merevlemez nem lehet letölteni az Azure-ból, ha egy futó virtuális Géphez van csatlakoztatva. Szeretné megszüntetni a virtuális gép virtuális merevlemez letöltésére. Ha azt szeretné, mint egy virtuális merevlemez használata egy [kép](tutorial-custom-images.md) hozhat létre más virtuális gépeket új lemezeket, használhat [Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) általánosítja az operációs rendszert, a fájlban található, és majd a virtuális gép leállítása. A VHD lemez egy meglévő virtuális gép vagy az adatlemez egy új példányát, használatához csak kell leállítja és felszabadítja a virtuális Gépet.

A virtuális merevlemez használata képként más virtuális gépek létrehozásához, a lépések végrehajtása:

1.  Ha még nem tette meg, jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2.  [Csatlakozzon a virtuális Géphez](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
3.  A virtuális gépen nyissa meg a parancssori ablakot rendszergazdaként.
4.  Módosítsa a könyvtárat a *%windir%\system32\sysprep* , és futtassa a sysprep.exe.
5.  Jelölje ki a rendszer-előkészítő eszköz párbeszédpanel **adja meg System Out-of-Box élmény (OOBE)**, és ellenőrizze, hogy **Generalize** van kiválasztva.
6.  Válassza ki a leállítási beállítások **leállítási**, és kattintson a **OK**. 

A VHD lemez egy meglévő virtuális gép vagy az adatlemez egy új példányát használja, hajtsa végre ezeket a lépéseket:

1.  Az Azure Portalon a központ menüben kattintson a **virtuális gépek**.
2.  Válassza ki a virtuális Gépet a listából.
3.  A virtuális gép paneljén kattintson **leállítása**.

    ![Virtuális gép leállítása](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>SAS URL-címet generálni

A VHD-fájl letöltéséhez, létre kell hoznia egy [közös hozzáférésű jogosultságkód (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) URL-CÍMÉT. Az URL-cím akkor jön létre, amikor egy lejárati idővel az URL-cím van hozzárendelve.

1.  Kattintson a menü, a virtuális gép paneljének **lemezek**.
2.  Válassza ki az operációsrendszer-lemez a virtuális gép, és kattintson **exportálása**.
3.  URL-címet a lejárati idő beállítása *36000*.
4.  Kattintson a **URL-címet generálni**.

    ![URL-cím generálása](./media/download-vhd/export-generate.png)

> [!NOTE]
> A lejárati idő a Windows Server operációs rendszer esetén nagy VHD-fájl letöltése idő elegendő az alapértelmezett emelkedett. Töltse le a kapcsolat függően több órát is igénybe, a Windows Server operációs rendszert tartalmazó VHD-fájl várható. Ha a virtuális merevlemez adatlemez tölti le, az alapértelmezett időtartamot is használhatók. 
> 
> 

## <a name="download-vhd"></a>Töltse le a virtuális merevlemez

1.  Kattintson a létrehozott URL-CÍMÉT, a VHD-fájl letöltése.

    ![Töltse le a virtuális merevlemez](./media/download-vhd/export-download.png)

2.  Szükség lehet kattintson **mentése** a böngészőben a letöltés megkezdéséhez. A VHD-fájl alapértelmezett neve: *abcd*.

    ![Kattintson a Mentés gombra a böngészőben](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [egy VHD-fájl feltöltése az Azure-bA](upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
- [Felügyelt lemez gyors létrehozásához a storage-fiókban lévő nem felügyelt lemezek](attach-disk-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- [Az Azure-lemezek kezelése a PowerShell](tutorial-manage-data-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

