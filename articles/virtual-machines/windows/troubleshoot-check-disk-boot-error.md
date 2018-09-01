---
title: Fájlrendszer ellenőrzése, ha egy Azure virtuális gép elindítása |} A Microsoft Docs
description: Ismerje meg, hogyan hárítsa el a problémát, hogy a virtuális gép történő rendszerindítás esetén megjelenítése ellenőrzése fájlrendszer |} A Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
authors: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/31/2018
ms.author: genli
ms.openlocfilehash: 71619bed63d5d53e6d24eac434ce5a696a445165
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43345196"
---
# <a name="windows-shows-checking-file-system-when-booting-an-azure-vm"></a>"Fájlt a rendszer ellenőrzése a" Windows mutat be egy Azure virtuális gép indításakor
Ez a cikk ismerteti a "Fájlt a rendszer ellenőrzése a" hiba, amely felmerülhet, amikor elindítja a Windows virtuális gép (VM) a Microsoft Azure-ban.

> [!NOTE] 
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk ismerteti, javasoljuk, hogy az új központi telepítéseknél helyett a klasszikus üzemi modell használatával Resource Manager üzemi modell használatával.

## <a name="symptom"></a>Jelenség 

Windows virtuális gép nem indul el. Amikor ellenőrizheti a rendszerindító képernyőképek a [rendszerindítási diagnosztika](./boot-diagnostics.md), láthatja, hogy a lemez ellenőrzése folyamat (chkdsk.exe) fut-e az alábbi üzenetek:

- Keresés és a meghajtó (C:) javítása
- C: fájlrendszer vizsgálata

## <a name="cause"></a>Ok

Ha NTFS hiba található a fájlrendszerben, a Windows ellenőrizze és javítsa ki a konzisztencia, a lemez a következő újraindításkor. Általában ez történik, ha a virtuális gép volt bármilyen váratlan újraindítása, vagy ha a virtuális gép leállítási folyamat váratlanul megszakadt.

## <a name="solution"></a>Megoldás 

Windows általában fog elindulni, a lemez ellenőrzése folyamat befejezése után. Ha a virtuális gép elakadt a lemez ellenőrzése során, próbálja meg futtatni a lemez ellenőrzése offline állapotba a virtuális gépen:
1.  Pillanatkép készítése az operációsrendszer-lemez az érintett virtuális gépek biztonsági mentéséhez. További információkért lásd: [lemez pillanatképének elkészítése](snapshot-copy-managed-disk.md).
2.  [Csatlakoztassa az operációsrendszer-lemezt egy helyreállítási virtuális Géphez](.\troubleshoot-recovery-disks-portal.md).  
3.  A helyreállítási virtuális gép futtassa a csatlakoztatott operációsrendszer-lemez lemez ellenőrzése. A következő mintát, a csatlakoztatott operációsrendszer-lemez meghajtóbetűjelét az E: 
        
        chkdsk E: /f
4.  A lemez ellenőrzése után válassza le a lemezt a helyreállítási virtuális Gépet, és ezután csatlakoztassa újra a lemezt, hogy az érintett virtuális gép operációsrendszer-lemezként. További információkért lásd: [hibáinak elhárítása egy Windows virtuális Gépet az operációsrendszer-lemez egy helyreállítási virtuális Géphez való csatlakoztatásával](.\troubleshoot-recovery-disks-portal.md).
