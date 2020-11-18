---
title: Red Hat Enterprise Linux lemezképek helyben történő frissítése az Azure-ban
description: A Red Hat Enterprise 7. x lemezképekről a legújabb 8. x verzióra történő helyszíni frissítés végrehajtásához szükséges lépések megkeresése
author: mathapli
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 04/16/2020
ms.author: alsin
ms.reviewer: cynthn
ms.openlocfilehash: 1160bc43db0dc9ec1714b1766c8cadf09660e291
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844564"
---
# <a name="red-hat-enterprise-linux-in-place-upgrades"></a>Helyi frissítések Red Hat Enterprise Linux

Ez a cikk lépésről lépésre bemutatja, hogyan végezheti el a helyben történő frissítését Red Hat Enterprise Linux 7-ről Red Hat Enterprise Linux 8-ra az Azure-beli "Leapp" segédprogram használatával. A helyben történő frissítés során a meglévő RHEL 7 operációs rendszert a RHEL 8 verzió váltja fel.

>[!Note] 
> Az Red Hat Enterprise Linux-ajánlatok SQL Server nem támogatják az Azure-ban történő helyszíni frissítést.

## <a name="what-to-expect-during-the-upgrade"></a>Mire számíthat a frissítés során
A rendszer a frissítés során néhányszor újraindítja a rendszerindítást, és a szokásos módon történik. Az utolsó újraindítás a virtuális gépet a RHEL 8 legújabb másodlagos kiadására frissíti. 

A frissítési folyamat akár 20 perctől akár több óráig is eltarthat, ez számos tényezőtől függ, például a virtuális gép méretétől és a rendszerre telepített csomagok számától.

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
* Győződjön meg arról, hogy a soros konzol működik, mivel ez lehetővé teszi a figyelést a frissítési folyamat során.

* SSH-beli rendszergazdai hozzáférés engedélyezése a-ben `/etc/ssh/sshd_config`
    1. Nyissa meg az `/etc/ssh/sshd_config` fájlt
    1. A "#PermitRootLogin igen" kifejezés keresése
    1. A "#" eltávolítása a Megjegyzés törléséhez

## <a name="steps-for-performing-the-upgrade"></a>A frissítés végrehajtásának lépései

Végezze el figyelmesen ezeket a lépéseket. Javasoljuk, hogy az éles példányok előtt próbálja megismételni a frissítést a tesztelési gépen.

1. Telepítsen egy yum-frissítést a legújabb ügyfél-csomagok beolvasásához.
    ```bash
    yum update -y
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

1. A "Leapp" frissítés végrehajtása.
    ```bash
    leapp upgrade --no-rhsm
    ```
1.  A `leapp upgrade` parancs sikeres befejezése után a folyamat befejezéséhez manuálisan indítsa újra a rendszerét. A rendszer újraindul néhány alkalommal, amikor nem lesz elérhető. Figyelje meg a folyamatot a soros konzol használatával.

1.  Ellenőrizze, hogy a frissítés sikeresen befejeződött-e.
    ```bash
    uname -a && cat /etc/redhat-release
    ```

1. A frissítés befejeződése után távolítsa el a root SSH-hozzáférést.
    1. Nyissa meg az `/etc/ssh/sshd_config` fájlt
    1. A "#PermitRootLogin igen" kifejezés keresése
    1. A "#" Megjegyzés hozzáadása a következőhöz:

1. Az sshd szolgáltatás újraindítása a módosítások érvénybe léptetéséhez
    ```bash
    systemctl restart sshd
    ```

## <a name="common-issues"></a>Gyakori problémák
Ezek a leggyakoribb példányok, amelyek a vagy a `leapp preupgrade` `leapp upgrade` folyamat sikertelenek lehetnek.

**Hiba: nem találhatók egyezések a következő letiltott plugin-mintákhoz**
```plaintext
STDERR:
No matches found for the following disabled plugin patterns: subscription-manager
Warning: Packages marked by Leapp for upgrade not found in repositories metadata: gpg-pubkey
```
**Megoldás**\
Tiltsa le az előfizetés-kezelő beépülő modult a fájl szerkesztésével `/etc/yum/pluginconf.d/subscription-manager.conf` és az Engedélyezve értékre való módosítással `enabled=0` .

Ezt az okozza, hogy az előfizetés-kezelő yum beépülő modulja engedélyezve van, ami nem használatos TB virtuális gépekhez.

**Hiba: lehetséges problémák a távoli bejelentkezéssel a root használatával** A `leapp preupgrade` hiba a következő hibával meghiúsulhat:
```structured-text
============================================================
                     UPGRADE INHIBITED
============================================================

Upgrade has been inhibited due to the following problems:
    1. Inhibitor: Possible problems with remote login using root account
Consult the pre-upgrade report for details and possible remediation.

============================================================
                     UPGRADE INHIBITED
============================================================
```
**Megoldás**\
Rendszergazdai hozzáférés engedélyezése a alkalmazásban `/etc/sshd_config` .
Ennek oka az, hogy a rendszer nem engedélyezi a root SSH-hozzáférést `/etc/sshd_config` a "[frissítés előkészítése](#preparations-for-the-upgrade)" című szakaszban leírtak szerint. 

## <a name="next-steps"></a>Következő lépések
* További információ az [Azure-beli Red Hat-lemezképekről](./redhat-images.md).
* További információ a [Red Hat frissítési infrastruktúráról](./redhat-rhui.md).
* További információ a [RHEL BYOS ajánlatáról](./byos.md).
* A Red Hat helyszíni frissítési folyamataival kapcsolatos információk a Red Hat dokumentációjában találhatók, a [RHEL 7-ről a RHEL 8-ra való frissítéssel](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/upgrading_from_rhel_7_to_rhel_8/index)
* A Red Hat-támogatási házirendekkel kapcsolatos információk a RHEL összes verziójára vonatkozóan a [Red Hat Enterprise Linux életciklus](https://access.redhat.com/support/policy/updates/errata) oldalon találhatók.
