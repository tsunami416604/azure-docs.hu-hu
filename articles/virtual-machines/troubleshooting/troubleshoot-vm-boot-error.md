---
title: Linux VM csizma Grub Rescue
description: A virtuális gép nem tudott elindulni, mert a virtuális gép belépett egy mentőkonzolba
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
ms.openlocfilehash: c24a840716841d04537ac5b77bcaf26fca4b78cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77561949"
---
# <a name="linux-vm-boots-to-grub-rescue"></a>Linux VM csizma Grub Rescue

Azonosítottuk, hogy a virtuális gép (VM) belépett egy mentési konzolra. A probléma akkor fordul elő, amikor a Linux virtuális gép kernel módosításait a közelmúltban alkalmazták, például a kernel frissítése, és a rendszerindítási folyamat során a kernel hibák miatt már nem indul el megfelelően. A rendszerindítási folyamat során, amikor a rendszertöltő megpróbálja megtalálni a Linux kernelt, és átadja a rendszerindítási vezérlőt, a virtuális gép belép egy mentési konzolba, amikor a átadás sikertelen.

Ha úgy találja, hogy a jövőben nem tud csatlakozni egy virtuális géphez, megtekintheti a virtuális gép képernyőképének képernyőképet az Azure Portalon a rendszerindítási diagnosztikai panel használatával. Ez segíthet a probléma diagnosztizálásában és annak megállapításában, hogy egy hasonló rendszerindítási hiba okozza-e.

## <a name="recommended-steps"></a>Javasolt lépések

Kövesse az alábbi kockázatcsökkentési lépéseket a kapott hibától függően:

### <a name="error---unknown-filesystem"></a>Hiba - Ismeretlen fájlrendszer

* Ha az **Ismeretlen fájlrendszer**hibaüzenetet kapja, ez a hiba a rendszerindító partíció fájlrendszerének sérüléséből vagy a rendszermag konfigurációjának helytelen konfigurációjából eredhet.

   * A fájlrendszerrel kapcsolatos problémák esetén kövesse a [Linux Recovery: Nem lehet Az SSH-tól linuxos virtuális gépig a fájlrendszer hibái (fsck, inodes) miatt.](https://blogs.msdn.microsoft.com/linuxonazure/2016/09/13/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck-inodes/)
   * Kernel problémák esetén kövesse a [Linux Recovery: A kernelproblémákkal kapcsolatos nem rendszerindítási problémák manuális javítása,](https://blogs.msdn.microsoft.com/linuxonazure/2016/10/09/linux-recovery-manually-fixing-non-boot-issues-related-to-kernel-problems/)vagy [a Linux-helyreállítás: A chroot használatával kapcsolatos kernelproblémákkal kapcsolatos nem rendszerindítási problémák megoldása.](https://blogs.msdn.microsoft.com/linuxonazure/2016/10/09/linux-recovery-fixing-non-boot-issues-related-to-kernel-problems-using-chroot/)
   
### <a name="error---file-not-found"></a>Hiba - A fájl nem található

* Ha a **hiba 15: A fájl nem található, vagy a kezdeti RAM lemez** vagy **initrd /initramfs fájl nem található,** kövesse az alábbi lépéseket.

    * A hiányzó `/boot/grub2/grub.cfg` fájl `initrd/initramfs` esetén vagy folytassa a következő eljárással:

    1. Győződjön meg arról, `/etc/default/grub` hogy létezik, és a helyes / kívánt beállításokat. Ha nem tudja, hogy melyek az alapértelmezett beállítások, ellenőrizheti a működő virtuális gép.

    2. Ezután futtassa a következő parancsot a konfiguráció újragenerálásához:`grub2-mkconfig -o /boot/grub2/grub.cfg`

   * Ha a hiányzó `/boot/grub/menu.lst`fájl , ez a hiba a régebbi operációs rendszer verziók (**RHEL 6.x**, **Centos 6.x** és **Ubuntu 14.04**), így a parancsok eltérőek lehetnek. Meg kell felpörgetni egy régi szervert, és tesztelni eszükséges parancsokat biztosítani.

### <a name="error---no-such-partition"></a>Hiba - Nincs ilyen partíció

* Ha a hibaüzenet **nem ilyen partíció**, olvassa el [esetforgatókönyv : "nincs ilyen partíció" hiba, miközben megpróbálja elindítani a virtuális gép, miután megpróbálta kiterjeszteni az operációs rendszer meghajtóját](https://blogs.technet.microsoft.com/shwetanayak/2017/03/12/case-scenario-no-such-partition-error-while-trying-to-start-the-vm-after-attempting-to-extend-the-os-drive/).

### <a name="error---grubcfg-file-not-found"></a>Hiba - a grub.cfg fájl nem található

* Ha a **hibát a /boot/grub2/grub.cfg fájl nem találja,** kövesse az alábbi lépéseket.

    * A hiányzó `/boot/grub2/grub.cfg` fájl `initrd/initramfs` esetén vagy folytassa a következő eljárással:

    1. Győződjön meg arról, `/etc/default/grub` hogy létezik, és a helyes / kívánt beállításokat. Ha nem tudja, hogy melyek az alapértelmezett beállítások, ellenőrizheti a működő virtuális gép.

    2. Ezután futtassa a következő parancsot a konfiguráció újragenerálásához: `grub2-mkconfig -o /boot/grub2/grub.cfg`.

   * Ha a hiányzó `/boot/grub/menu.lst`fájl , ez a hiba a régebbi operációs rendszer verziók (**RHEL 6.x**, **Centos 6.x** és **Ubuntu 14.04**), így a parancsok elhalaszthatja. Hajtson fel egy régi kiszolgálót, és tesztelje, hogy biztosan a megfelelő parancsok álljanak rendelkezésre.

## <a name="next-steps"></a>További lépések

* [Az Azure virtuálisgép-ügynök – áttekintés](../extensions/agent-windows.md)
* [Virtuálisgép-bővítmények és -szolgáltatások a Windows rendszerhez](../extensions/features-windows.md)

