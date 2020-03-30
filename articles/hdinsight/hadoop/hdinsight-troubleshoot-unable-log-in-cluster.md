---
title: Nem lehet bejelentkezni az Azure HDInsight-fürtbe
description: Hibaelhárítás, hogy miért nem lehet bejelentkezni az Apache Hadoop-fürtbe az Azure HDInsightban
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 2099d1b7583017733498946a5866ab37de43ba9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894057"
---
# <a name="scenario-unable-to-log-into-azure-hdinsight-cluster"></a>Eset: Nem lehet bejelentkezni az Azure HDInsight-fürtbe

Ez a cikk az Azure HDInsight-fürtökkel való kommunikáció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Nem lehet bejelentkezni az Azure HDInsight-fürtbe.

## <a name="cause"></a>Ok

Az okok eltérőek lehetnek. Ne feledje, hogy a fürt- vagy alkalmazás-irányítópultokra való bejelentkezéskor használja a "fürtbejelentkezési" vagy HTTP-hitelesítő adatokat. Távoli kapcsolódás esetén az SSH vagy a Távoli asztal hitelesítő adatait használja.

## <a name="resolution"></a>Megoldás:

A leggyakoribb hibákat az alábbi lépéseket követve háríthatja el.

* Nyissa meg a Fürt irányítópultját bizalmassági módban egy új böngészőlapon.

* Ha nem tudja visszahívni az SSH hitelesítő adatait, [alaphelyzetbe állíthatja a hitelesítő adatokat az Ambari felhasználói felületen belül.](../hdinsight-administer-use-portal-linux.md#change-passwords)

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

* Válaszokat kaphat az Azure szakértőitől az [Azure közösségi támogatásán](https://azure.microsoft.com/support/community/)keresztül.

* Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba a hivatalos Microsoft Azure-fiókkal, amely javítja az ügyfélélményt azáltal, hogy az Azure-közösséget a megfelelő erőforrásokhoz, válaszokhoz, támogatáshoz és szakértőkhöz csatlakoztatja.

* Ha további segítségre van szüksége, támogatási kérelmet nyújthat be az [Azure Portalról.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Válassza a **menüsor Támogatás parancsát,** vagy nyissa meg a **Súgó + támogatási** központot. További információkért tekintse át az Azure-támogatási kérelem létrehozása című, [továbbcímű tájékoztatót.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Az Előfizetés-kezelés hez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetésrészét képezi, a technikai támogatást pedig az [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/)egyike biztosítja.
