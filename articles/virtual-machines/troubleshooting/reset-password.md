---
title: Az Azure virtuális gépek helyi Linux-jelszó alaphelyzetbe állítása |} A Microsoft Docs
description: Vezessen be a lépéseket az Azure virtuális Gépen futó helyi Linux-jelszó alaphelyzetbe állítása
services: virtual-machines-linux
documentationcenter: ''
author: Deland-Han
manager: cshepard
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: d96d75f4f2623476f7af4e6eea930c1f2c503e3a
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51226913"
---
# <a name="how-to-reset-local-linux-password-on-azure-vms"></a>Az Azure virtuális gépek helyi Linux-jelszó alaphelyzetbe állítása

Ez a cikk bemutatja a több módszert is helyi Linux rendszerű virtuális gép (VM) jelszavakat. Ha a felhasználói fiók lejárt vagy csak szeretné hozzon létre egy új fiókot, az alábbi módszerek segítségével hozzon létre egy új helyi rendszergazdai fiókot, valamint a virtuális gép újbóli eléréséhez.

## <a name="symptoms"></a>Probléma

Nem tud bejelentkezni a virtuális Gépet, és megjelenik az üzenet, amely azt jelzi, hogy a használt jelszó helytelen. Vmagent esetében ezenkívül az Azure Portalon a jelszó nem használható.

## <a name="manual-password-reset-procedure"></a>Manuális jelszó-visszaállítási eljárást

1.  Törölje a virtuális Gépet, és megtartása a csatlakoztatott lemezeket.

2.  Az operációs rendszer meghajtójának csatolása adatlemezként egy másik ideiglenes virtuális géphez ugyanazon a helyen.

3.  A következő SSH-parancs futtatása a virtuális gépen historikus felügyelője lesz.

    ```bash
    sudo su
    ```

4.  Futtatás **fdisk -l** vagy rendszernaplók az újonnan csatolt lemezen található meg. Keresse meg azt a meghajtó nevét csatlakoztatásához. A historikus virtuális Gépen, keresse meg az érintett naplófájlban.

    ```bash
    grep SCSI /var/log/kern.log (ubuntu)
    grep SCSI /var/log/messages (centos, suse, oracle)
    ```

    A következő egy példa kimenet a grep parancs:

    ```bash
    kernel: [ 9707.100572] sd 3:0:0:0: [sdc] Attached SCSI disk
    ```

5.  Hozzon létre egy csatlakoztatási pontot nevű **tempmount**.

    ```bash
    mkdir /tempmount
    ```

6.  Csatlakoztassa az operációsrendszer-lemez a csatlakoztatási ponton. Általában csatlakoztatni kell *sdc1* vagy *sdc2*. Ez függ, a üzemeltetési partíción */etc* könyvtárat abból a hibás gép lemezét.

    ```bash
    mount /dev/sdc1 /tempmount
    ```

7.  Hitelesítőadat-fájlok az alapvető fontosságú másolatokat készíthet változtatások előtt:

    ```bash
    cp /etc/passwd /etc/passwd_orig    
    cp /etc/shadow /etc/shadow_orig    
    cp /tempmount/etc/passwd /etc/passwd
    cp /tempmount/etc/shadow /etc/shadow 
    cp /tempmount/etc/passwd /tempmount/etc/passwd_orig
    cp /tempmount/etc/shadow /tempmount/etc/shadow_orig
    ```

8.  Szükséges a felhasználói jelszó alaphelyzetbe állítása:

    ```bash
    passwd <<USER>> 
    ```

9.  Helyezze át a módosított fájlokat a megfelelő helyre a hibás gép lemezen.

    ```bash
    cp /etc/passwd /tempmount/etc/passwd
    cp /etc/shadow /tempmount/etc/shadow
    cp /etc/passwd_orig /etc/passwd
    cp /etc/shadow_orig /etc/shadow
    ```

10. Lépjen vissza a legfelső szintű, és a lemez leválasztásához.

    ```bash
    cd /
    umount /tempmount
    ```

11. Válassza le a lemezt, a felügyeleti portálról.

12. Hozza létre újra a virtuális Gépet.

## <a name="next-steps"></a>További lépések

* [Hibaelhárítás Azure virtuális Gépen egy másik Azure virtuális gép operációsrendszer-lemez csatolása](https://social.technet.microsoft.com/wiki/contents/articles/18710.troubleshoot-azure-vm-by-attaching-os-disk-to-another-azure-vm.aspx)

* [Az Azure CLI: Hogyan lehet törölni, és helyezze újra üzembe a virtuális gép VHD-ből](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/azure-cli-how-to-delete-and-re-deploy-a-vm-from-vhd/)
