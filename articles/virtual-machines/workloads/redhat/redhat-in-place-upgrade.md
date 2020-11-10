---
title: Red Hat Enterprise Linux lemezképek helyben történő frissítése az Azure-ban
description: A Red Hat Enterprise 7. x lemezképekről a legújabb 8. x verzióra történő helyszíni frissítés végrehajtásához szükséges lépések megkeresése
author: mathapli
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 04/16/2020
ms.author: alsin
ms.reviewer: cynthn
ms.openlocfilehash: 48884e6faa5f26f027c772b44d5f960979a40d1d
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2020
ms.locfileid: "94447726"
---
# <a name="red-hat-enterprise-linux-in-place-upgrades"></a>Helyi frissítések Red Hat Enterprise Linux

Ez a cikk lépésről lépésre bemutatja, hogyan végezheti el a helyben történő frissítését Red Hat Enterprise Linux 7-ről Red Hat Enterprise Linux 8-ra az Azure-beli "Leapp" segédprogram használatával. A helyben történő frissítés során a meglévő RHEL 7 operációs rendszert a RHEL 8 verzió váltja fel.

>[!Note] 
> Az Red Hat Enterprise Linux-ajánlatok SQL Server nem támogatják az Azure-ban történő helyszíni frissítést.

## <a name="what-to-expect-during-the-upgrade"></a>Mire számíthat a frissítés során
A rendszer a frissítés során néhányszor újraindítja a rendszerindítást, és a szokásos módon történik. Az utolsó újraindítás a virtuális gépet a RHEL 8 legújabb másodlagos kiadására frissíti.

## <a name="preparations-for-the-upgrade"></a>Felkészülés a frissítésre
A Red Hat és az Azure által hivatalosan ajánlott módszer a helyszíni frissítés, amely lehetővé teszi, hogy az ügyfelek a rendszerét a következő főverzióra frissítse. A frissítés elvégzése előtt néhány dolgot érdemes figyelembe vennie. 

>[!Important] 
> A frissítés elvégzése előtt készítsen pillanatképet a rendszerképről.

>[!NOTE]
> A cikkben szereplő parancsokat a root fiók használatával kell futtatni.

* Győződjön meg arról, hogy a legújabb RHEL 7 verziót használja, amely jelenleg a RHEL 7,9. Ha zárolt verziót használ, és nem tud frissíteni a RHEL 7,9-re, az [itt leírt lépéseket követve válthat egy nem EUs adattárra](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/redhat-rhui#switch-a-rhel-7x-vm-back-to-non-eus-remove-a-version-lock).

* Futtassa az alábbi parancsot, hogy megtudja, hogyan zajlik a frissítés, és ha a művelet befejeződik. A parancsnak létre kell hoznia egy fájlt a "/var/log/leapp/leapp-report.txt" alatt, amely ismerteti a folyamatot, és azt, hogy mi történik, és ha a frissítés lehetséges vagy sem
    ```bash
    leapp preupgrade --no-rhsm
    ```

## <a name="steps-for-performing-the-upgrade"></a>A frissítés végrehajtásának lépései

Végezze el figyelmesen ezeket a lépéseket. Javasoljuk, hogy az éles példányok előtt próbálja megismételni a frissítést a tesztelési gépen.

1. Telepítsen egy yum-frissítést a legújabb ügyfél-csomagok beolvasásához.
    ```bash
    yum update
    ```

1. Telepítse a leapp-Client-Package csomagot.
    ```bash
    yum install leapp-rhui-azure
    ```
    
1. Használja a leapp-Data. tar. gz fájlt repomap.csv és pes-events.jsa [RedHat portálon](https://access.redhat.com/articles/3664871), és csomagolja ki őket. 
    1. Töltse le a fájlt.
    1. Bontsa ki a tartalmat, és távolítsa el a fájlt a következő parancs használatával:
    ```bash
     tar -xzf leapp-data12.tar.gz -C /etc/leapp/files && rm leapp-data12.tar.gz
    ```
    


1. Adja hozzá a "Answers" fájlt a következőhöz: "Leapp".
    ```bash
    leapp answer --section remove_pam_pkcs11_module_check.confirm=True --add
    ```
    
1. PermitRootLogin engedélyezése a/etc/ssh/sshd_config
    1. Nyissa meg a/etc/ssh/fájlt sshd_config
    1. A "#PermitRootLogin igen" kifejezés keresése
    1. A "#" eltávolítása a Megjegyzés törléséhez



1. A "Leapp" frissítés végrehajtása.
    ```bash
    leapp upgrade --no-rhsm
    ```
1. Az sshd szolgáltatás újraindítása a módosítások érvénybe léptetéséhez
    ```bash
    systemctl restart sshd
    ```
1. Megjegyzés a PermitRootLogin a/etc/ssh/sshd_config újra
    1. Nyissa meg a/etc/ssh/fájlt sshd_config
    1. A "#PermitRootLogin igen" kifejezés keresése
    1. A "#" Megjegyzés hozzáadása a következőhöz:

## <a name="next-steps"></a>További lépések
* További információ az [Azure-beli Red Hat-lemezképekről](./redhat-images.md).
* További információ a [Red Hat frissítési infrastruktúráról](./redhat-rhui.md).
* További információ a [RHEL BYOS ajánlatáról](./byos.md).
* A Red Hat helyszíni frissítési folyamataival kapcsolatos információk a Red Hat dokumentációjában találhatók, a [RHEL 7-ről a RHEL 8-ra való frissítéssel](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/upgrading_from_rhel_7_to_rhel_8/index)
* A Red Hat-támogatási házirendekkel kapcsolatos információk a RHEL összes verziójára vonatkozóan a [Red Hat Enterprise Linux életciklus](https://access.redhat.com/support/policy/updates/errata) oldalon találhatók.