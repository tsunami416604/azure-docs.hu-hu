---
title: Rendszerindítási hibák elhárítása az Azure Linux Virtual Machinesban | Microsoft Docs
description: Ez a cikk segítséget nyújt az Azure-Linux Virtual Machines rendszerindítási hibák elhárításához szükséges cikkek összekapcsolásához.
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
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74408741"
---
# <a name="troubleshoot-azure-linux-virtual-machines-boot-errors"></a>Azure Linux Virtual Machines rendszerindítási hibák elhárítása

Ez a cikk azokat a gyakori rendszerindítási hibákat sorolja fel, amelyeket a Linux rendszerű virtuális gépek (VM) indításakor kaphat a Microsoft Azure. További információ a hibákról: a **rendszerindítási hibák és megoldások** szakasz cikkei.

## <a name="boot-errors-and-solutions"></a>Rendszerindítási hibák és megoldások

* [GRUB-mentés](troubleshoot-vm-boot-error.md)

## <a name="next-steps"></a>Következő lépések

- [VM soros konzol](serial-console-linux.md)

Linux rendszerű virtuális gép hibáinak elhárítása az operációsrendszer-lemez egy helyreállítási virtuális géphez az Azure használatával történő csatlakoztatásával:

- [Azure-beli virtuális gép javítása](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)

 Disk swap – ez automatizálható a következők bármelyikével:
- [Rendszerhéj-helyreállítási parancsfájlok](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager)
- [bash helyreállítási parancsfájlok](https://github.com/sribs/azure-support-scripts)

- [Parancssori felület](troubleshoot-recovery-disks-linux.md)
- [Azure Portal](troubleshoot-recovery-disks-portal-linux.md)


## <a name="disk-swap-video"></a>Disk swap-videó:

Ha nem rendelkezik hozzáféréssel a GRUB-hoz, tekintse meg [ezt](https://youtu.be/m5t0GZ5oGAc) a videót, és nézze meg, hogyan automatizálhatja a lemezes swap-eljárást a virtuális gép helyreállításához

## <a name="unofficial-solution"></a>Nem hivatalos megoldás

A virtuális gép helyreállítása a nem támogatott BÉTAVERZIÓs [ALAR](https://github.com/malachma/azure-auto-recover) is próbálkozhat