---
title: Rendszerindítási hibák elhárítása az Azure Linux virtuális gépekben | Microsoft dokumentumok
description: Ez a cikk az Azure Linux virtuális gépek rendszerindítási hibáinak elhárításához cikkekre mutató hivatkozásokat tartalmaz.
services: virtual-machines-linux
documentationCenter: ''
author: vilibert
manager: spogge
editor: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/20/2019
ms.author: vilibert
ms.openlocfilehash: 37cb201751f72918838efe5837aa0e357d483f24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74408741"
---
# <a name="troubleshoot-azure-linux-virtual-machines-boot-errors"></a>Az Azure Linux virtuális gépek rendszerindítási hibáinak elhárítása

Ez a cikk felsorolja a linuxos virtuális gép (VM) a Microsoft Azure-ban indításakor kapott gyakori rendszerindítási hibákat. A hibákról további információt a **Rendszerindítási hibák és megoldások** című részben található cikkekben talál.

## <a name="boot-errors-and-solutions"></a>Rendszerindítási hibák és megoldások

* [GRUB mentési](troubleshoot-vm-boot-error.md)

## <a name="next-steps"></a>További lépések

- [Virtuális gép soros konzolja](serial-console-linux.md)

Linuxos virtuális gép hibaelhárítása az operációs rendszer lemezének az Azure-ral történő helyreállítási virtuális géphez való csatlakoztatásával:

- [Az Azure virtuális gép javítása](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)

 Disk Swap – ez automatizálható segítségével vagy:
- [Power Shell helyreállítási parancsfájlok](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager)
- [bash helyreállítási parancsfájlok](https://github.com/sribs/azure-support-scripts)

- [parancssori felület](troubleshoot-recovery-disks-linux.md)
- [Azure-portál](troubleshoot-recovery-disks-portal-linux.md)


## <a name="disk-swap-video"></a>Lemezcsere videó:

Ha nem férnek hozzá a GRUB nézni [ezt a](https://youtu.be/m5t0GZ5oGAc) videót, és nézze meg, hogyan lehet könnyen automatizálni a lemezcsere eljárás, hogy visszaszerezze a VM

## <a name="unofficial-solution"></a>Nem hivatalos megoldás

A virtuális gép helyreállítása a nem támogatott [ALAR](https://github.com/malachma/azure-auto-recover) parancsfájllal is megkísérelhető