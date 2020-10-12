---
title: A Linux rendszerű virtuális gép a grub Rescue-t indítja
description: Nem sikerült elindítani a virtuális gépet, mert a virtuális gép megadta a mentési konzolt
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 08/28/2019
ms.author: tiag
ms.openlocfilehash: 5a2fd7fcfdae8559bfb39bffff7c73c7082a86aa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87543282"
---
# <a name="linux-vm-boots-to-grub-rescue"></a>A Linux rendszerű virtuális gép a grub Rescue-t indítja

Azt azonosította, hogy a virtuális gép (VM) megadta a mentési konzolt. A probléma akkor fordul elő, ha a Linux rendszerű virtuális gép mostanában kernel-módosításokat hajtott végre, például a kernel frissítése során, és a rendszerindítási folyamat során a kernel hibái miatt már nem indul el megfelelően. A rendszerindítási folyamat során, amikor a rendszerindító betöltő megkísérli megkeresni a Linux-kernelt, és kikapcsolja a rendszerindítási vezérlőt, a virtuális gép a handoff meghibásodása esetén a mentési konzolba lép.

Ha azt tapasztalja, hogy a jövőben nem tud virtuális géphez csatlakozni, a Azure Portal rendszerindítási diagnosztika paneljén megtekintheti a virtuális gép képernyőképét. Ez segíthet a probléma diagnosztizálásában és annak megállapításában, hogy egy hasonló rendszerindítási hiba okozza-e.

## <a name="recommended-steps"></a>Javasolt lépések

A kapott hibától függően kövesse az alábbi kockázatcsökkentő lépéseket:

### <a name="error---unknown-filesystem"></a>Hiba – ismeretlen fájlrendszer

* Ha a rendszer **ismeretlen**hibát észlel, akkor ez a hiba a rendszerindító partíció fájlrendszerének sérülése vagy helytelen kernel-konfiguráció miatt eredményezhet.

   * Fájlrendszerrel kapcsolatos problémák esetén hajtsa végre a következő cikkben ismertetett lépéseket [: Linux Recovery: nem lehet SSH-t Linux virtuális géphez a fájlrendszer hibái miatt (fsck, inode)](/archive/blogs/linuxonazure/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck-inodes).
   * Kernel-problémák esetén kövesse az [Azure-beli linuxos virtuális gépek helyreállításához a kernelhez kapcsolódó rendszerindítási problémák vagy a](https://support.microsoft.com/help/4091524/how-recover-azure-linux-vm-from-kernel-related-boot-related-issues)Linux-helyreállítás című cikk lépéseit [: a rendszerindítási problémák elhárítása kernel-problémákról a kromát használatával](http://linuxonazure.azurewebsites.net/linux-recovery-fixing-non-boot-issues-related-to-kernel-problems-using-chroot/).
   
### <a name="error---file-not-found"></a>Hiba – a fájl nem található

* Ha a következő hibaüzenetet kapta **: 15. a fájl nem található, vagy a kezdeti RAM lemez** vagy a **initrd/initramfs fájl nem található**, kövesse az alábbi lépéseket.

    * A hiányzó fájlhoz, `/boot/grub2/grub.cfg` vagy `initrd/initramfs` folytassa a következő folyamattal:

    1. Győződjön meg arról `/etc/default/grub` , hogy létezik, és megfelelő/kívánt beállításokkal rendelkezik. Ha nem tudja, hogy melyek az alapértelmezett beállítások, megtekintheti a működő virtuális gépeket.

    2. Ezután futtassa a következő parancsot a konfiguráció újralétrehozásához: `grub2-mkconfig -o /boot/grub2/grub.cfg`

   * Ha a hiányzó fájl `/boot/grub/menu.lst` , akkor ez a hiba a régebbi operációsrendszer-verziók esetében (**RHEL 6. x**, **CentOS 6. x** és **Ubuntu 14,04**), így a parancsok eltérhetnek. A megfelelő parancsok biztosításához egy régi kiszolgálót kell megadnia, és tesztelni kell.

### <a name="error---no-such-partition"></a>Hiba – nincs ilyen partíció

* Ha **nem kap ilyen partíciót**, tekintse meg az [eseti forgatókönyvet: "nincs ilyen partíció" hibaüzenetet, miközben megpróbálta ELINDÍTANI a virtuális gépet az operációs rendszer meghajtójának kibővítésére tett kísérlet után](/archive/blogs/shwetanayak/case-scenario-no-such-partition-error-while-trying-to-start-the-vm-after-attempting-to-extend-the-os-drive).

### <a name="error---grubcfg-file-not-found"></a>Hiba – a grub. cfg fájl nem található

* Ha **nem találja a/boot/GRUB2/grub.cfg fájlt**, kövesse az alábbi lépéseket.

    * A hiányzó fájlhoz, `/boot/grub2/grub.cfg` vagy `initrd/initramfs` folytassa a következő folyamattal:

    1. Győződjön meg arról `/etc/default/grub` , hogy létezik, és megfelelő/kívánt beállításokkal rendelkezik. Ha nem tudja, hogy melyek az alapértelmezett beállítások, megtekintheti a működő virtuális gépeket.

    2. Ezután futtassa a következő parancsot a konfiguráció újralétrehozásához: `grub2-mkconfig -o /boot/grub2/grub.cfg` .

   * Ha a hiányzó fájl `/boot/grub/menu.lst` , akkor ez a hiba a régebbi operációsrendszer-verziók esetében (**RHEL 6. x**, **CentOS 6. x** és **Ubuntu 14,04**), így a parancsok elhalasztható. Hozzon létre egy régi kiszolgálót, és ellenőrizze, hogy a megfelelő parancsok vannak-e megadva.

## <a name="next-steps"></a>Következő lépések

* [Az Azure Virtual Machine Agent áttekintése](../extensions/agent-windows.md)
* [Virtuálisgép-bővítmények és-szolgáltatások a Windows rendszerhez](../extensions/features-windows.md)
