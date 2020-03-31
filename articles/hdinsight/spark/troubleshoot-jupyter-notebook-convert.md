---
title: Nem lehet létrehozni a Jupyter-jegyzetfüzetet az Azure HDInsightban
description: Az Azure HDInsight-fürtökkel való kommunikáció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/11/2020
ms.openlocfilehash: 61e7cd8d37108b8f4eea88c4f6b6b2a8cdbfd605
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77186801"
---
# <a name="unable-to-create-jupyter-notebook-in-azure-hdinsight"></a>Nem lehet létrehozni a Jupyter-jegyzetfüzetet az Azure HDInsightban

Ez a cikk az Azure HDInsight-fürtökkel való kommunikáció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Jupyter-jegyzetfüzet indításakor a következő hibaüzenet jelenik meg:

```error
Cannot convert notebook to v5 because that version doesn't exist
```

## <a name="cause"></a>Ok

Nem egyezik a verzió.

## <a name="resolution"></a>Megoldás:

1. Az [ssh paranccsal](../hdinsight-hadoop-linux-use-ssh-unix.md) csatlakozhat a fürthöz. Az alábbi parancs szerkesztésével cserélje le a CLUSTERNAME-t a fürt nevére, majd írja be a parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Nyissa `_version.py` meg a következő parancs végrehajtásával:

    ```bash
    sudo nano /usr/bin/anaconda/lib/python2.7/site-packages/nbformat/_version.py
    ```

1. 5-4-es módosítás, hogy a módosított sor a következőképpen jelenjen meg: **5** **4**

    ```python
    version_info = (4, 0, 3)
    ```

    A módosítások mentése **a Ctrl + X,** **Y**, **Enter**.

1. Egy webböngészőből keresse `https://CLUSTERNAME.azurehdinsight.net/#/main/services/JUPYTER`meg `CLUSTERNAME` a , ahol a fürt neve.

1. Indítsa újra a Jupyter szolgáltatást.

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

* Válaszokat kaphat az Azure szakértőitől az [Azure közösségi támogatásán](https://azure.microsoft.com/support/community/)keresztül.

* Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba a hivatalos Microsoft Azure-fiókkal az ügyfélélmény javítása érdekében. Az Azure-közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérelmet nyújthat be az [Azure Portalról.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Válassza a **menüsor Támogatás parancsát,** vagy nyissa meg a **Súgó + támogatási** központot. További információkért tekintse [át az Azure-támogatási kérelem létrehozása című áttekintést.](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) Az Előfizetés-kezelés hez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetésrészét képezi, a technikai támogatást pedig az [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/)egyike biztosítja.
