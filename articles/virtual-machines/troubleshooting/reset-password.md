---
title: Helyi Linux-jelszó alaphelyzetbe állítása Azure-beli virtuális gépeken | Microsoft Docs
description: A helyi Linux-jelszó alaphelyzetbe állításának lépései az Azure-beli virtuális gépen
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
ms.openlocfilehash: c6bfd5b9ff3626593916533f27c5c2755cebcb13
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87028481"
---
# <a name="how-to-reset-local-linux-password-on-azure-vms"></a>Helyi Linux-jelszó visszaállítása Azure-beli virtuális gépeken

Ez a cikk számos módszert ismertet a helyi linuxos virtuális gépek (VM) jelszavának alaphelyzetbe állításához. Ha a felhasználói fiók lejárt, vagy csak egy új fiókot szeretne létrehozni, akkor a következő módszerekkel hozhat létre új helyi rendszergazdai fiókot, és újból hozzáférhet a virtuális géphez.

## <a name="symptoms"></a>Hibajelenségek

Nem tud bejelentkezni a virtuális gépre, és megjelenik egy üzenet, amely jelzi, hogy a használt jelszó helytelen. Emellett a VMAgent nem használható a jelszó alaphelyzetbe állításához a Azure Portal.

## <a name="manual-password-reset-procedure"></a>Manuális jelszó-visszaállítási eljárás

> [!NOTE]
> A következő lépések nem vonatkoznak a nem felügyelt lemezzel rendelkező virtuális gépre.

1. Készítsen pillanatképet az érintett virtuális gép operációsrendszer-lemezéről, hozzon létre egy lemezt a pillanatképből, majd csatolja a lemezt egy hibakeresési virtuális géphez. További információ: Windows rendszerű [virtuális gép hibáinak elhárítása az operációsrendszer-lemez egy helyreállítási virtuális géphez való csatolásával a Azure Portal használatával](troubleshoot-recovery-disks-portal-linux.md).

2. Kapcsolódjon a hibaelhárítási virtuális géphez Távoli asztal használatával.

3.  Futtassa a következő SSH-parancsot a hibaelhárítási virtuális gépen, hogy legyen egy felügyelő.

    ```bash
    sudo su
    ```

4.  Futtassa az **fdisk-l** parancsot, vagy tekintse meg a rendszernaplókat az újonnan csatlakoztatott lemez megtalálásához. Keresse meg a csatlakoztatni kívánt meghajtó nevét. Ezután az időszakos virtuális gépen keresse meg a megfelelő naplófájlt.

    ```bash
    grep SCSI /var/log/kern.log (ubuntu)
    grep SCSI /var/log/messages (centos, suse, oracle)
    ```

    A következő példa a GREP-parancs kimenetét szemlélteti:

    ```bash
    kernel: [ 9707.100572] sd 3:0:0:0: [sdc] Attached SCSI disk
    ```

5.  Hozzon létre egy **tempmount**nevű csatlakoztatási pontot.

    ```bash
    mkdir /tempmount
    ```

6.  Csatlakoztassa az operációsrendszer-lemezt a csatlakoztatási ponthoz. Általában csatlakoztatnia kell a *sdc1* -t vagy a *sdc2*-t. Ez attól függ, hogy az */etc* könyvtárban lévő üzemeltetési partíció a hibás számítógép lemezéről származik-e.

    ```bash
    mount /dev/sdc1 /tempmount
    ```

7.  A módosítások végrehajtása előtt készítsen másolatot az alapvető hitelesítő adatok fájljairól:

    ```bash
    cp /etc/passwd /etc/passwd_orig    
    cp /etc/shadow /etc/shadow_orig    
    cp /tempmount/etc/passwd /etc/passwd
    cp /tempmount/etc/shadow /etc/shadow 
    cp /tempmount/etc/passwd /tempmount/etc/passwd_orig
    cp /tempmount/etc/shadow /tempmount/etc/shadow_orig
    ```

8.  A felhasználó jelszavának alaphelyzetbe állítása:

    ```bash
    passwd <<USER>> 
    ```

9.  Helyezze át a módosított fájlokat a hibás számítógép lemezének megfelelő helyére.

    ```bash
    cp /etc/passwd /tempmount/etc/passwd
    cp /etc/shadow /tempmount/etc/shadow
    cp /etc/passwd_orig /etc/passwd
    cp /etc/shadow_orig /etc/shadow
    ```

10. Lépjen vissza a gyökérkönyvtárba, és válassza le a lemezt.

    ```bash
    cd /
    umount /tempmount
    ```

11. A Azure Portalban válassza le a lemezt a hibaelhárítási virtuális gépről.

12. [Módosítsa az érintett virtuális gép operációsrendszer-lemezét](troubleshoot-recovery-disks-portal-linux.md#swap-the-os-disk-for-the-vm).

## <a name="next-steps"></a>További lépések

* [Azure-beli virtuális gép – operációsrendszer-lemez csatlakoztatása egy másik Azure-beli virtuális géphez](https://social.technet.microsoft.com/wiki/contents/articles/18710.troubleshoot-azure-vm-by-attaching-os-disk-to-another-azure-vm.aspx)

* [Azure CLI: virtuális gép törlése és újbóli üzembe helyezése virtuális merevlemezről](/archive/blogs/linuxonazure/azure-cli-how-to-delete-and-re-deploy-a-vm-from-vhd)
