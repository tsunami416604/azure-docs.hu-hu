---
title: Helyi Linux-jelszó alaphelyzetbe állítása az Azure virtuális gépeken | Microsoft dokumentumok
description: A helyi Linux-jelszó alaphelyzetbe állításának lépései az Azure VM-en
services: virtual-machines-linux
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 08/20/2019
ms.author: delhan
ms.openlocfilehash: 83751538efe4f3d3af5928caa04b265b6c867442
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71153570"
---
# <a name="how-to-reset-local-linux-password-on-azure-vms"></a>Helyi Linux-jelszó visszaállítása Azure-beli virtuális gépeken

Ez a cikk számos módszert vezet be a helyi Linux virtuális gép (VM) jelszavak alaphelyzetbe állításához. Ha a felhasználói fiók lejárt, vagy csak új fiókot szeretne létrehozni, az alábbi módszerekkel új helyi rendszergazdai fiókot hozhat létre, és újra hozzáférhet a virtuális géphez.

## <a name="symptoms"></a>Probléma

Nem tud bejelentkezni a virtuális gépre, és egy üzenet jelenik meg, amely jelzi, hogy a használt jelszó helytelen. Emellett nem használhatja a VMAgent-et a jelszó alaphelyzetbe állításához az Azure Portalon.

## <a name="manual-password-reset-procedure"></a>Kézi jelszó-visszaállítási eljárás

> [!NOTE]
> A következő lépések nem vonatkoznak a nem felügyelt lemezzel rendelkező virtuális gépre.

1. Készítsen pillanatképet az érintett virtuális gép operációsrendszer-lemezéről, hozzon létre egy lemezt a pillanatképből, majd csatolja a lemezt egy hibaelhárítási virtuális géphez. További információt a [Windows-virtuális gép hibaelhárítása az operációs rendszer lemezének az Azure Portalon keresztül idomuló virtuális géphez való csatolásával](troubleshoot-recovery-disks-portal-linux.md)című témakörben talál.

2. Csatlakozzon a hibaelhárítási virtuális géphez a Távoli asztal használatával.

3.  Futtassa a következő SSH parancsot a hibaelhárítási virtuális gép lesz egy szuper-felhasználó.

    ```bash
    sudo su
    ```

4.  **Futtassa az fdisk -l** fájlt, vagy tekintse meg a rendszernaplókat az újonnan csatlakoztatott lemez megkereséséhez. Keresse meg a csatlakoztatni a meghajtó nevét. Ezután a temporális virtuális gép, keresse meg a megfelelő naplófájlt.

    ```bash
    grep SCSI /var/log/kern.log (ubuntu)
    grep SCSI /var/log/messages (centos, suse, oracle)
    ```

    A következő példa a grep parancs kimenete:

    ```bash
    kernel: [ 9707.100572] sd 3:0:0:0: [sdc] Attached SCSI disk
    ```

5.  Hozzon létre egy **tempmount**nevű csatlakoztatási pontot.

    ```bash
    mkdir /tempmount
    ```

6.  Csatlakoztassa az operációs rendszer lemezét a csatlakoztatási pontra. Általában *sdc1* vagy *sdc2*kell csatlakoztatnia. Ez attól függ, hogy a */etc* könyvtárban lévő gazdapartíció a hibás géplemezről.

    ```bash
    mount /dev/sdc1 /tempmount
    ```

7.  A módosítások végrehajtása előtt hozza létre az alapvető hitelesítő adatok másolatait:

    ```bash
    cp /etc/passwd /etc/passwd_orig    
    cp /etc/shadow /etc/shadow_orig    
    cp /tempmount/etc/passwd /etc/passwd
    cp /tempmount/etc/shadow /etc/shadow 
    cp /tempmount/etc/passwd /tempmount/etc/passwd_orig
    cp /tempmount/etc/shadow /tempmount/etc/shadow_orig
    ```

8.  Állítsa alaphelyzetbe a szükséges felhasználói jelszót:

    ```bash
    passwd <<USER>> 
    ```

9.  Helyezze át a módosított fájlokat a hibás gép lemezén lévő megfelelő helyre.

    ```bash
    cp /etc/passwd /tempmount/etc/passwd
    cp /etc/shadow /tempmount/etc/shadow
    cp /etc/passwd_orig /etc/passwd
    cp /etc/shadow_orig /etc/shadow
    ```

10. Lépjen vissza a gyökérhez, és oldja le a lemezt.

    ```bash
    cd /
    umount /tempmount
    ```

11. Az Azure Portalon válassza le a lemezt a hibaelhárítási virtuális gép.

12. [Módosítsa az érintett virtuális gép operációsrendszer-lemezét.](troubleshoot-recovery-disks-portal-linux.md#swap-the-os-disk-for-the-vm)

## <a name="next-steps"></a>További lépések

* [Az Azure VM hibaelhárítása operációsrendszer-lemez másik Azure-beli virtuális géphez való csatlakoztatásával](https://social.technet.microsoft.com/wiki/contents/articles/18710.troubleshoot-azure-vm-by-attaching-os-disk-to-another-azure-vm.aspx)

* [Azure CLI: Virtuális gép törlése és újratelepítése a virtuális merevlemezről](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/azure-cli-how-to-delete-and-re-deploy-a-vm-from-vhd/)
