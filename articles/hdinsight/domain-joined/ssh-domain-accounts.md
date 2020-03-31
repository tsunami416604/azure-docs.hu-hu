---
title: SSH-hozzáférés kezelése tartományi fiókokhoz az Azure HDInsightban
description: Az Azure AD-fiókok SSH-hozzáférésének kezelésének lépései a HDInsightban.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 5529989384df75b592afa8f5e4960eb9817fb2d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472517"
---
# <a name="manage-ssh-access-for-domain-accounts-in-azure-hdinsight"></a>SSH-hozzáférés kezelése tartományi fiókokhoz az Azure HDInsightban

Biztonságos fürtökön alapértelmezés szerint az [Azure AD DS](../../active-directory-domain-services/overview.md) összes tartományi felhasználója [ssh-t](../hdinsight-hadoop-linux-use-ssh-unix.md) vihet a fej- és peremhálózati csomópontokba. Ezek a felhasználók nem részei a sudoers csoportnak, és nem kapnak root hozzáférést. A fürt létrehozása során létrehozott SSH-felhasználó root hozzáféréssel fog rendelkezni.

## <a name="manage-access"></a>Hozzáférés kezelése

Az SSH-hozzáférés módosítása adott felhasználókhoz vagy csoportokhoz, frissítse `/etc/ssh/sshd_config` az egyes csomópontok.

1. Az [ssh paranccsal](../hdinsight-hadoop-linux-use-ssh-unix.md) csatlakozhat a fürthöz. Az alábbi parancs szerkesztésével cserélje le a CLUSTERNAME-t a fürt nevére, majd írja be a parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Nyissa `ssh_confi`meg a g fájlt.

    ```bash
    sudo nano /etc/ssh/sshd_config
    ```

1. Módosítsa `sshd_config` a fájlt igény szerint. Ha a felhasználókat bizonyos csoportokra korlátozza, akkor a helyi fiókok nem tudnak SSH-t adni az adott csomópontba. Az alábbi példa csak egy példa a szintaxisra:

    ```bash
    AllowUsers useralias1 useralias2

    AllowGroups groupname1 groupname2
    ```

    Ezután mentse a módosításokat: **Ctrl + X**, **Y**, **Enter**.

1. Indítsa újra az sshd-t.

    ```bash
    sudo systemctl restart sshd
    ```

1. Ismételje meg a fenti lépéseket minden csomópontnál.

## <a name="ssh-authentication-log"></a>SSH hitelesítési napló

Az SSH hitelesítési `/var/log/auth.log`napló be van írva a rendszerbe. Ha a helyi vagy tartományi fiókok ssh-on keresztüli bejelentkezési hibákat lát, a hibák hibakereséséhez végig kell mennie a naplón. A probléma gyakran adott felhasználói fiókokhoz köthető, és általában célszerű más felhasználói fiókokat vagy SSH-t kipróbálni az alapértelmezett SSH-felhasználó (helyi fiók) használatával, majd megpróbálni egy kinitet.

## <a name="ssh-debug-log"></a>SSH hibakeresési napló

A részletes naplózás engedélyezéséhez újra kell `sshd` indítania a `-d` lehetőséget. Mint `/usr/sbin/sshd -d` ön is `sshd` futtatható egy egyéni port (például 2222), így nem kell megállítani a fő SSH démon. Az SSH-ügyféllel is használhatja `-v` a lehetőséget, hogy több naplót kapjon (a hibák ügyféloldali nézete).

## <a name="next-steps"></a>További lépések

* [HDInsight-fürtök kezelése vállalati biztonsági csomaggal](./apache-domain-joined-manage.md)
* [Csatlakozzon a HDInsighthoz (Apache Hadoop) az SSH használatával.](../hdinsight-hadoop-linux-use-ssh-unix.md)
