---
title: Fájlrendszer ellenőrzése Azure-beli virtuális gép indításakor| Microsoft dokumentumok
description: Megtudhatja, hogyan oldható meg a probléma, amelyet a virtuális gép a fájlrendszer ellenőrzése során jelenít meg indításkor| Microsoft dokumentumok
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/31/2018
ms.author: genli
ms.openlocfilehash: 86938c582745cb0759eda9cd0693f407471a0529
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921487"
---
# <a name="windows-shows-checking-file-system-when-booting-an-azure-vm"></a>A Windows az "ellenőrző fájlrendszert" jeleníti meg az Azure virtuális gép indításakor

Ez a cikk a "Fájlrendszer ellenőrzése" hibaüzenetet ismerteti, amely a Windows virtuális gép (VM) Microsoft Azure-beli indításakor találkozhat.


## <a name="symptom"></a>Hibajelenség 

A Windows virtuális gép nem indul el. Amikor a rendszerindítási képernyőképeket a [rendszerindítási diagnosztikában](boot-diagnostics.md)ellenőrzi, láthatja, hogy a Lemezellenőrzése folyamat (chkdsk.exe) az alábbi üzenetek egyikével fut:

- Szkennelés és javítás meghajtó (C:)
- A Fájlrendszer ellenőrzése a C-n:

## <a name="cause"></a>Ok

Ha ntfs-hiba található a fájlrendszerben, a Windows a következő újraindításkor ellenőrzi és kijavítja a lemez konzisztenciáját. Ez általában akkor fordul elő, ha a virtuális gép váratlan újraindítása történt, vagy ha a virtuális gép leállítási folyamata hirtelen megszakadt.

## <a name="solution"></a>Megoldás 

A Windows a Lemezellenőrzés folyamat befejezése után megfelelően indul el. Ha a virtuális gép beragadt a Lemezellenőrzés folyamatba, próbálja meg offline módban futtatni a check lemezt a virtuális gépen:
1.  Készítsen pillanatképet az érintett virtuális gép operációsrendszer-lemezéről biztonsági másolatként. További információt a [Lemez pillanatképe](../windows/snapshot-copy-managed-disk.md)című témakörben talál.
2.  [Csatlakoztassa az operációs rendszer lemezét egy helyreállítási virtuális géphez.](troubleshoot-recovery-disks-portal-windows.md)  
3.  A helyreállítási virtuális gép, ellenőrizze a lemez a csatlakoztatott operációsrendszer-lemezen. A következő mintában a csatlakoztatott operációsrendszer-lemez illesztőprogram-betűje E: 
        
        chkdsk E: /f
4.  A lemez ellenőrzése befejezése után válassza le a lemezt a helyreállítási virtuális gépről, majd csatlakoztassa újra a lemezt az érintett virtuális géphez operációs rendszer lemezeként. További információt a [Windows-virtuális gép hibaelhárítása az operációs rendszer lemezének helyreállítási virtuális géphez való csatolásával című témakörben talál.](troubleshoot-recovery-disks-portal-windows.md)
