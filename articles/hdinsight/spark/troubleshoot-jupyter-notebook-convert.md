---
title: Nem sikerült létrehozni a Jupyter-jegyzetfüzetet az Azure HDInsight
description: Leírja az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/11/2020
ms.openlocfilehash: 03b23d86bca10077ede43f3b4e5a6c5fa6f5dd28
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77165461"
---
# <a name="unable-to-create-jupyter-notebook-in-azure-hdinsight"></a>Nem sikerült létrehozni a Jupyter-jegyzetfüzetet az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Jupyter-jegyzetfüzet indításakor a következő hibaüzenet jelenik meg:

```error
Cannot convert notebook to v5 because that version doesn't exist
```

## <a name="cause"></a>Ok

A verziószám nem egyezik.

## <a name="resolution"></a>Megoldás:

1. A fürthöz való kapcsolódáshoz használja az [SSH-parancsot](../hdinsight-hadoop-linux-use-ssh-unix.md) . Szerkessze az alábbi parancsot az CLUSTERNAME helyére a fürt nevével, majd írja be a következő parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Nyissa meg `_version.py` a következő parancs végrehajtásával:

    ```bash
    sudo nano /usr/bin/anaconda/lib/python2.7/site-packages/nbformat/_version.py
    ```

1. Módosítsa az **5** – **4** értékre, hogy a módosított sor a következőképpen jelenik meg:

    ```python
    version_info = (4, 0, 3)
    ```

    Mentse a módosításokat a **CTRL + X**, **Y**, **ENTER**billentyűkombináció megadásával.

1. Egy webböngészőből navigáljon `https://CLUSTERNAME.azurehdinsight.net`, ahol a `CLUSTERNAME` a fürt neve.

1. Válassza a **Jupyter** lehetőséget, majd indítsa újra a szolgáltatást.

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Kapcsolódjon a [@AzureSupporthoz](https://twitter.com/azuresupport) – a hivatalos Microsoft Azure fiókot a felhasználói élmény javításához. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
