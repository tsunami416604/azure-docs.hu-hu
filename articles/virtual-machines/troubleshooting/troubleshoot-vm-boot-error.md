---
title: Az Azure Virtual Machine rendszerindítási hibája
description: Nem sikerült elindítani a virtuális gépet, mert a virtuális gép megadta a mentési konzolt
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: ''
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 08/28/2019
ms.author: tiag
ms.openlocfilehash: 9995b9049378a0ab4f3450ec577d034598d171e9
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2019
ms.locfileid: "70984843"
---
# <a name="vm-boot-error"></a>Virtuális gép rendszerindítási hibája

Azt azonosította, hogy a virtuális gép (VM) megadta a mentési konzolt. A probléma akkor fordul elő, ha a Linux rendszerű virtuális gép mostanában kernel-módosításokat hajtott végre, például a kernel frissítése során, és a rendszerindítási folyamat során a kernel hibái miatt már nem indul el megfelelően. A rendszerindítási folyamat során, amikor a rendszerindító betöltő megkísérli megkeresni a Linux-kernelt, és kikapcsolja a rendszerindítási vezérlőt, a virtuális gép a handoff meghibásodása esetén a mentési konzolba lép.

Ha azt tapasztalja, hogy a jövőben nem tud virtuális géphez csatlakozni, a Azure Portal rendszerindítási diagnosztika paneljén megtekintheti a virtuális gép képernyőképét. Ez segíthet a probléma diagnosztizálásában és annak megállapításában, hogy egy hasonló rendszerindítási hiba okozza-e.

## <a name="recommended-steps"></a>Javasolt lépések

A kapott hibától függően kövesse az alábbi kockázatcsökkentő lépéseket:

### <a name="error---unknown-filesystem"></a>Hiba – ismeretlen fájlrendszer

* Ha a rendszer **ismeretlen**hibát észlel, akkor ez a hiba a rendszerindító partíció fájlrendszerének sérülése vagy helytelen kernel-konfiguráció miatt eredményezhet.

   * Fájlrendszerrel kapcsolatos problémák esetén kövesse a Linux-helyreállítás című [cikk lépéseit: Fájlrendszerbeli hibák miatt nem lehet SSH-t Linux rendszerű virtuális géphez csatlakozni](https://blogs.msdn.microsoft.com/linuxonazure/2016/09/13/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck-inodes/)(fsck, inode).
   * Kernel-problémák esetén kövesse a Linux-helyreállítás című [cikk lépéseit: Kernel-problémákkal](https://blogs.msdn.microsoft.com/linuxonazure/2016/10/09/linux-recovery-manually-fixing-non-boot-issues-related-to-kernel-problems/)vagy [Linux-helyreállítással kapcsolatos nem rendszerindítási problémák manuális javítása: Kernel-problémákkal kapcsolatos nem rendszerindítási problémák kijavítása a kromát](https://blogs.msdn.microsoft.com/linuxonazure/2016/10/09/linux-recovery-fixing-non-boot-issues-related-to-kernel-problems-using-chroot/)használatával.
   
### <a name="error---file-not-found"></a>Hiba – a fájl nem található

* Ha a **15. hibaüzenetet kapta: A fájl nem található, vagy a** kezdeti RAM lemez vagy a **initrd/initramfs fájl nem található**, kövesse az alábbi lépéseket.

    * A hiányzó fájlhoz `/boot/grub2/grub.cfg` , `initrd/initramfs` vagy folytassa a következő folyamattal:

    1. Győződjön `/etc/default/grub` meg arról, hogy létezik, és megfelelő/kívánt beállításokkal rendelkezik. Ha nem tudja, hogy melyek az alapértelmezett beállítások, megtekintheti a működő virtuális gépeket.

    2. Ezután futtassa a következő parancsot a konfiguráció újralétrehozásához:`grub2-mkconfig -o /boot/grub2/grub.cfg`

   * Ha `/boot/grub/menu.lst`a hiányzó fájl, akkor ez a hiba a régebbi operációsrendszer-verziók esetében (**RHEL 6. x**, **CentOS 6. x** és **Ubuntu 14,04**), így a parancsok eltérhetnek. A megfelelő parancsok biztosításához egy régi kiszolgálót kell megadnia, és tesztelni kell.

### <a name="error---no-such-partition"></a>Hiba – nincs ilyen partíció

* Ha **nem kap ilyen partíciót**, tekintse meg az [eseti forgatókönyvet: "nincs ilyen partíció" hibaüzenetet, miközben megpróbálta ELINDÍTANI a virtuális gépet az operációs rendszer meghajtójának kibővítésére tett kísérlet után](https://blogs.technet.microsoft.com/shwetanayak/2017/03/12/case-scenario-no-such-partition-error-while-trying-to-start-the-vm-after-attempting-to-extend-the-os-drive/).

### <a name="error---grubcfg-file-not-found"></a>Hiba – a grub. cfg fájl nem található

* Ha **nem találja a/boot/GRUB2/grub.cfg fájlt**, kövesse az alábbi lépéseket.

    * A hiányzó fájlhoz `/boot/grub2/grub.cfg` , `initrd/initramfs` vagy folytassa a következő folyamattal:

    1. Győződjön `/etc/default/grub` meg arról, hogy létezik, és megfelelő/kívánt beállításokkal rendelkezik. Ha nem tudja, hogy melyek az alapértelmezett beállítások, megtekintheti a működő virtuális gépeket.

    2. Ezután futtassa a következő parancsot a konfiguráció újralétrehozásához: `grub2-mkconfig -o /boot/grub2/grub.cfg`.

   * Ha `/boot/grub/menu.lst`a hiányzó fájl, akkor ez a hiba a régebbi operációsrendszer-verziók esetében (**RHEL 6. x**, **CentOS 6. x** és **Ubuntu 14,04**), így a parancsok elhalasztható. Hozzon létre egy régi kiszolgálót, és ellenőrizze, hogy a megfelelő parancsok vannak-e megadva.

## <a name="next-steps"></a>További lépések

* [Az Azure Virtual Machine Agent áttekintése](../extensions/agent-windows.md)
* [Virtuálisgép-bővítmények és-szolgáltatások a Windows rendszerhez](../extensions/features-windows.md)

