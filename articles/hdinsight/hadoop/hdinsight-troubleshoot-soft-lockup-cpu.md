---
title: hiba az Azure HDInsight-fürtben
description: Az Azure HDInsight-fürtben található kernel syslogs szolgáltatásban megjelenik a watchdog BUG Soft kizárható PROCESSZORa
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/05/2019
ms.openlocfilehash: 618a871bee762900d4c99700cde42d759506b4b9
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/09/2019
ms.locfileid: "70810579"
---
# <a name="scenario-watchdog-bug-soft-lockup---cpu-error-from-an-azure-hdinsight-cluster"></a>Forgatókönyv: "watchdog: Hiba: egy Azure HDInsight-fürtből származó, lágy lefagyási hiba

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

A kernel syslogs a következő hibaüzenetet tartalmazza `watchdog: BUG: soft lockup - CPU`:.

## <a name="cause"></a>Ok

A Linux kernel [hibája](https://bugzilla.kernel.org/show_bug.cgi?id=199437) okozza a processzorok lágy lefagyását.

## <a name="resolution"></a>Megoldás:

Kernel-javítás alkalmazása. Az alábbi szkript frissíti a Linux-kernelt, és 24 órán belül különböző időpontokban újraindítja a gépeket. Hajtsa végre a parancsfájl-műveletet két kötegben. Az első köteg az összes csomóponton van, kivéve a fő csomópontot. A második köteg a fő csomóponton található. Ne futtassa a főcsomópontot és más csomópontokat egyszerre.

1. Navigáljon a HDInsight-fürt Azure Portal.

1. Ugrás a parancsfájlok műveleteire.

1. Válassza az **új küldése** lehetőséget, és adja meg a bemenetet a következőképpen

    | Tulajdonság | Value |
    | --- | --- |
    | Szkripttípus | – Egyéni |
    | Name (Név) |A kernel Soft Lock hibájának javítása |
    | Bash parancsfájl URI azonosítója |`https://raw.githubusercontent.com/hdinsight/hdinsight.github.io/master/ClusterCRUD/KernelSoftLockFix/scripts/KernelSoftLockIssue_FixAndReboot.sh` |
    | Csomópont típusa (i) |Worker, Zookeeper |
    | Paraméterek |– |

    Válassza a parancsfájl megtartása **művelet...** lehetőséget, ha azt szeretné, hogy az új csomópontok hozzáadásakor végrehajtsa a parancsfájlt.

1. Kattintson a **Létrehozás** gombra.

1. Várjon, amíg a végrehajtás sikeres lesz.

1. Hajtsa végre a parancsfájl műveletét a fej csomóponton a 3. lépéssel megegyező lépéseket követve, de ezúttal a csomópont-típusokkal: Fej.

1. Várjon, amíg a végrehajtás sikeres lesz.

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Csatlakozás az Azure-Közösség a megfelelő erőforrásokhoz való csatlakoztatásával – a hivatalos Microsoft Azure fiókkal – a felhasználói élmény javítása érdekében: válaszok, támogatás és szakértők. [@AzureSupport](https://twitter.com/azuresupport)

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
