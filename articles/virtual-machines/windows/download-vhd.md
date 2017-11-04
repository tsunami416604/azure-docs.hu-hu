---
title: "Töltse le a virtuális merevlemez Windows Azure-ból |} Microsoft Docs"
description: "Töltse le az Azure portál használatával Windows virtuális Merevlemezt."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/26/2017
ms.author: davidmu
ms.openlocfilehash: d8bf89a4b7c2a158302f9ba09a182a3d8d062adc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="download-a-windows-vhd-from-azure"></a>Töltse le a virtuális merevlemez Windows Azure-ból

Ebből a cikkből megismerheti, hogyan töltheti le a [Windows virtuális merevlemez (VHD)](about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) fájl az Azure portál használata az Azure-ból. 

Virtuális gépek (VM) Azure használatban [lemezek](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) egy olyan hely, az operációs rendszerek, alkalmazások és adatok tárolására. Minden Azure virtuális gépek legalább két lemezt – a Windows operációs rendszer és egy ideiglenes lemezzel rendelkezik. Az operációs rendszer tárolására hoztak létre, lemezképpel, és mind az operációsrendszer-lemez, és a lemezkép az Azure storage-fiókban tárolt VHD-k. Virtuális gépek is rendelkeznek legalább egy adatlemezt, virtuális merevlemezekként is tárolt.

## <a name="stop-the-vm"></a>A virtuális gép leállítása

Virtuális merevlemez nem lehet letölteni az Azure-ból, ha egy virtuális gép csatlakozik. Szeretné megszüntetni a virtuális gép virtuális merevlemez letöltéséhez. Ha szeretne használni egy virtuális Merevlemezt egy [kép](tutorial-custom-images.md) új lemez a többi virtuális gép létrehozásához, használhatja [Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) általánosítja az operációs rendszert, a fájlban, és állítsa le a virtuális Gépet. A VHD-t használja, amely lemezként egy új példányát egy meglévő virtuális gép vagy adatlemez, akkor csak kell leállítása és a virtuális gép felszabadítása.

Más virtuális gépek létrehozásához használja a virtuális merevlemez képként, végezze el az alábbi lépéseket:

1.  Ha még nem tette meg, jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2.  [Csatlakoztassa a virtuális Gépet](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
3.  A virtuális Gépre nyissa meg a parancssort rendszergazdaként.
4.  Lépjen be *%windir%\system32\sysprep* , és futtassa a sysprep.exe.
5.  Jelölje ki a rendszer-előkészítő eszköz párbeszédpanel **adja meg a rendszer Out-of-Box élmény (OOBE)**, és győződjön meg arról, hogy **Generalize** van kiválasztva.
6.  Válassza a leállítási beállítások **leállítási**, és kattintson a **OK**. 

A VHD-t használja, amely lemezként egy meglévő virtuális gép vagy adatlemez egy új példányát, végezze el az alábbi lépéseket:

1.  Az Azure-portálon a központ menüben kattintson **virtuális gépek**.
2.  Válassza ki a virtuális Gépet a listából.
3.  A virtuális gép paneljén kattintson **leállítása**.

    ![Virtuális gép leállítása](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>SAS URL-cím létrehozása

A VHD-fájl letöltésére, szeretne létrehozni egy [közös hozzáférésű jogosultságkód (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) URL-CÍMÉT. Az URL-cím generálásakor lejárati idő az URL-cím van hozzárendelve.

1.  Kattintson a panel a virtuális gép menü, **lemezek**.
2.  Válassza ki az operációs rendszer lemezt a virtuális gép számára, és kattintson **exportálása**.
3.  Az URL-címet a lejárati idő beállítása *36000*.
4.  Kattintson a **URL-cím készítése**.

    ![URL-cím létrehozása](./media/download-vhd/export-generate.png)

> [!NOTE]
> A lejárati ideje elegendő időt letölteni a nagy VHD-fájlt a Windows Server operációs rendszert biztosítsanak az alapértelmezett nő. A VHD-fájl letöltése attól függően, hogy a kapcsolat több órát vesz igénybe, a Windows Server operációs rendszert tartalmazó számíthat. Ha adatlemezt virtuális, az alapértelmezett érték megfelelő. 
> 
> 

## <a name="download-vhd"></a>Töltse le a virtuális merevlemez

1.  Az URL-címen hozott létre kattintson a letöltés a VHD-fájl.

    ![Töltse le a virtuális merevlemez](./media/download-vhd/export-download.png)

2.  Kattintson a szeretne **mentése** menüpontot a böngészőben a letöltés megkezdéséhez. Az alapértelmezett név a VHD-fájl *abcd*.

    ![Kattintson a Mentés gombra a böngészőben](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan [VHD-fájl feltöltése az Azure-bA](upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
- [Hozzon létre felügyelt lemezek tárfiókokban nem felügyelt lemezekből](attach-disk-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- [Azure-lemezeket a PowerShell-lel kezelése](tutorial-manage-data-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

