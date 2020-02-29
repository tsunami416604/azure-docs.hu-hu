---
title: Fájlrendszer ellenőrzése Azure-beli virtuális gép indításakor | Microsoft Docs
description: Megtudhatja, hogyan oldja meg a virtuális gép által a rendszerindítás során megjelenő problémát? Microsoft Docs
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
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2020
ms.locfileid: "77921487"
---
# <a name="windows-shows-checking-file-system-when-booting-an-azure-vm"></a>A Windows egy Azure-beli virtuális gép indításakor a "fájlrendszer ellenőrzése" állapotot jeleníti meg

Ez a cikk a "fájlrendszer ellenőrzése" hibaüzenetet ismerteti, amely akkor fordulhat elő, amikor egy Windows rendszerű virtuális gépet (VM) indít el Microsoft Azure.


## <a name="symptom"></a>Hibajelenség 

Egy Windows rendszerű virtuális gép nem indul el. Amikor bejelöli a rendszerindítási [diagnosztika](boot-diagnostics.md)rendszerindítási funkcióit, láthatja, hogy a lemez ellenőrzése folyamat (chkdsk. exe) a következő üzenetek egyikével fut:

- Meghajtó (C:) ellenőrzése és javítása
- Fájlrendszer ellenőrzése a C-ben:

## <a name="cause"></a>Ok

Ha NTFS-hiba található a fájlrendszerben, a Windows a következő újraindításkor fogja ellenőriznie és kijavítani a lemez konzisztenciáját. Ez általában akkor fordul elő, ha a virtuális gép váratlan újraindítást észlelt, vagy ha a virtuális gép leállítási folyamata hirtelen megszakadt.

## <a name="solution"></a>Megoldás 

A Windows a lemez-ellenőrzési folyamat befejezése után általában elindul. Ha a virtuális gép beragadt a lemez-ellenőrzési folyamatba, próbálja meg futtatni a következőt a virtuális gépen lévő ellenőrzési lemezről:
1.  Készítsen pillanatképet az érintett virtuális gép operációsrendszer-lemezéről biztonsági másolatként. További információ: [lemez pillanatképe](../windows/snapshot-copy-managed-disk.md).
2.  [Csatlakoztassa az operációsrendszer-lemezt egy helyreállítási virtuális géphez](troubleshoot-recovery-disks-portal-windows.md).  
3.  A helyreállítási virtuális gépen futtassa a lemez-ellenőrzési lemezt a csatlakoztatott operációsrendszer-lemezen. A következő példában a csatolt operációsrendszer-lemez illesztőprogram-betűjele E: 
        
        chkdsk E: /f
4.  A lemezellenőrzés befejezése után válassza le a lemezt a helyreállítási virtuális gépről, majd csatlakoztassa újra a lemezt az érintett virtuális géphez operációsrendszer-lemezként. További információ: Windows rendszerű [virtuális gép hibáinak elhárítása az operációsrendszer-lemez egy helyreállítási virtuális géphez való csatolásával](troubleshoot-recovery-disks-portal-windows.md).
