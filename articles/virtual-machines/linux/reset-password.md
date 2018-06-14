---
title: Az Azure virtuális gépeken futó helyi Linux-jelszó alaphelyzetbe állítása |} Microsoft Docs
description: A lépéseket az Azure virtuális gépen helyi Linux-jelszó alaphelyzetbe állítása bevezetése
services: virtual-machines-linux
documentationcenter: ''
author: Deland-Han
manager: cshepard
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: delhan
ms.openlocfilehash: b9182ec2a974de06c2bd45928b9964f253653bf6
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2018
ms.locfileid: "27578362"
---
# <a name="how-to-reset-local-linux-password-on-azure-vms"></a>Az Azure virtuális gépeken futó helyi Linux-jelszó alaphelyzetbe állítása

Ez a cikk több módszert is helyi Linux virtuális gép (VM) jelszavak alaphelyzetbe állítása be. Ha a felhasználói fiók lejárt, vagy szeretné hozzon létre egy új fiókot, az alábbi eljárások segítségével hozzon létre egy új helyi rendszergazdai fiókot és a virtuális gép újbóli eléréséhez.

## <a name="symptoms"></a>Probléma

Nem tud bejelentkezni a virtuális Gépet, és kap egy üzenetet, amely azt jelzi, hogy a használt jelszó helytelen. Emellett vmagent esetében nem használható az Azure-portál a jelszó. 

## <a name="manual-password-reset-procedure"></a>Manuális jelszó-átállítási eljárás

1.  Törölje a virtuális Gépet, és láthatóan tartja a csatlakoztatott lemezeket.

2.  Az operációs rendszer meghajtót adatok lemezként csatlakoztatni másik ideiglenes virtuális gép ugyanazon a helyen.

3.  A következő SSH parancsot a legyen, felettes felhasználói historikus virtuális Gépre.


    ~~~~
    sudo su
    ~~~~

4.  Futtatás **fdisk -l** vagy a rendszer talál az újonnan csatolt lemezen található. Keresse meg a meghajtó nevét csatlakoztatni. A historikus virtuális Gépre, keresse meg a megfelelő naplófájlban.

    ~~~~
    grep SCSI /var/log/kern.log (ubuntu)
    grep SCSI /var/log/messages (centos, suse, oracle)
    ~~~~

    A következő egy példa kimenet a grep parancs:

    ~~~~
    kernel: [ 9707.100572] sd 3:0:0:0: [sdc] Attached SCSI disk
    ~~~~

5.  Hozzon létre egy csatlakoztatási pontot nevű **tempmount**.

    ~~~~
    mkdir /tempmount
    ~~~~

6.  Az operációsrendszer-lemezképet csatlakoztatni a csatlakoztatási pont. Általában szükség csatlakoztatási sdc1 vagy sdc2. Ez a nem működő számítógép lemezről etc könyvtárban üzemeltetési partíció függ.

    ~~~~
    mount /dev/sdc1 /tempmount
    ~~~~

7.  Készítsen biztonsági másolatot a módosítások végrehajtása előtt:

    ~~~~
    cp /etc/passwd /etc/passwd_orig    
    cp /etc/shadow /etc/shadow_orig    
    cp /tempmount/etc/passwd /etc/passwd
    cp /tempmount/etc/shadow /etc/shadow 
    cp /tempmount/etc/passwd /tempmount/etc/passwd_orig
    cp /tempmount/etc/shadow /tempmount/etc/shadow_orig
    ~~~~

8.  Alaphelyzetbe állítja a jelszót, amelyekre szüksége van:

    ~~~~
    passwd <<USER>> 
    ~~~~

9.  Helyezze át a módosított fájlokat a megfelelő helyen a nem működő számítógép lemezen.

    ~~~~
    cp /etc/passwd /tempmount/etc/passwd
    cp /etc/shadow /tempmount/etc/shadow
    cp /etc/passwd_orig /etc/passwd
    cp /etc/shadow_orig /etc/shadow
    ~~~~

10. A legfelső szintű visszaléphet, és a lemez leválasztása.

    ~~~~
    cd /
    umount /tempmount
    ~~~~

11. Válassza le a lemezt a felügyeleti portálon.

12. Hozza létre újra a virtuális Gépet.

## <a name="next-steps"></a>További lépések

* [Hibaelhárítás Azure virtuális gép egy másik Azure virtuális gép operációsrendszer-lemez csatolása](http://social.technet.microsoft.com/wiki/contents/articles/18710.troubleshoot-azure-vm-by-attaching-os-disk-to-another-azure-vm.aspx)

* [Az Azure CLI: Hogyan törölje és hozza létre újra a virtuális gépek virtuális merevlemezből](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/azure-cli-how-to-delete-and-re-deploy-a-vm-from-vhd/)
