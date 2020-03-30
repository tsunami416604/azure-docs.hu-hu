---
title: Watchdog BUG soft lockup CPU-hiba az Azure HDInsight-fürtből
description: Watchdog BUG soft lockup CPU jelenik meg kernel syslogs az Azure HDInsight klaszter
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: 701e314ad2a3762b1e8ca022ce18d9435ce2db37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894103"
---
# <a name="scenario-watchdog-bug-soft-lockup---cpu-error-from-an-azure-hdinsight-cluster"></a>Forgatókönyv: "Figyelő: BUG: soft lockup - CPU" hiba egy Azure HDInsight-fürtből

Ez a cikk az Azure HDInsight-fürtökkel való kommunikáció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

A kernel syslogs tartalmazza `watchdog: BUG: soft lockup - CPU`a következő hibaüzenetet: .

## <a name="cause"></a>Ok

A Linux Kernel [hibája](https://bugzilla.kernel.org/show_bug.cgi?id=199437) okozza a CPU puha zárolását.

## <a name="resolution"></a>Megoldás:

Alkalmazza a kernel javítást. Az alábbi szkript frissíti a linux kernelt, és 24 óra alatt különböző időpontokban újraindítja a gépeket. Hajtsa végre a parancsfájlműveletet két kötegben. Az első köteg a fő csomópont kivételével az összes csomóponton található. A második adag a főcsomóponton van. Ne fusson egyszerre a főcsomópontokon és más csomópontokon.

1. Nyissa meg a HDInsight-fürtöt az Azure Portalról.

1. Folytassa a parancsfájlműveleteket.

1. Válassza **az Új küldése lehetőséget,** és írja be a bemenetet az alábbiak szerint:

    | Tulajdonság | Érték |
    | --- | --- |
    | Parancsfájl típusa | -Egyéni |
    | Név |Javítsa ki a kernel soft lock kérdését |
    | Bash parancsfájl URI |`https://raw.githubusercontent.com/hdinsight/hdinsight.github.io/master/ClusterCRUD/KernelSoftLockFix/scripts/KernelSoftLockIssue_FixAndReboot.sh` |
    | Csomóponttípus(ok) |Dolgozó, Zookeeper |
    | Paraméterek |N/A |

    Válassza **a Parancsfájl-művelet megőrzése lehetőséget,** ha új csomópontok hozzáadásakor végrehajtja a parancsfájlt.

1. Kattintson a **Létrehozás** gombra.

1. Várja meg, amíg a végrehajtás sikeres lesz.

1. Hajtsa végre a parancsfájlműveletet a Head csomóponton a 3.

1. Várja meg, amíg a végrehajtás sikeres lesz.

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

* Válaszokat kaphat az Azure szakértőitől az [Azure közösségi támogatásán](https://azure.microsoft.com/support/community/)keresztül.

* Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba a hivatalos Microsoft Azure-fiókkal, amely javítja az ügyfélélményt azáltal, hogy az Azure-közösséget a megfelelő erőforrásokhoz, válaszokhoz, támogatáshoz és szakértőkhöz csatlakoztatja.

* Ha további segítségre van szüksége, támogatási kérelmet nyújthat be az [Azure Portalról.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Válassza a **menüsor Támogatás parancsát,** vagy nyissa meg a **Súgó + támogatási** központot. További információkért tekintse át az Azure-támogatási kérelem létrehozása című, [továbbcímű tájékoztatót.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Az Előfizetés-kezelés hez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetésrészét képezi, a technikai támogatást pedig az [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/)egyike biztosítja.
