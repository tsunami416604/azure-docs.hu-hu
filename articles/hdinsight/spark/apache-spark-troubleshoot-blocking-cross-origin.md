---
title: Jupyter 404 hiba - "Blokkoló cross Origin API" - Azure HDInsight
description: Jupyter server 404 "Nem található" hibaüzenet az Azure HDInsight "Originek közötti API blokkolása" miatt
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: e241657186582955d21981f7dfe18856724aa692
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894413"
---
# <a name="scenario-jupyter-server-404-not-found-error-due-to-blocking-cross-origin-api-in-azure-hdinsight"></a>Eset: Jupyter server 404 "Nem található" hiba az Azure HDInsight "Originek közötti API blokkolása" miatt

Ez a cikk az Apache Spark-összetevők Azure HDInsight-fürtökben való használata során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Amikor a HDInsight Jupyter szolgáltatásához fér hozzá, megjelenik egy "Nem található" hibaüzenet. Ha megnézed a Jupyter naplókat, valami ilyesmit fogsz látni:

```log
[W 2018-08-21 17:43:33.352 NotebookApp] 404 PUT /api/contents/PySpark/notebook.ipynb (10.16.0.144) 4504.03ms referer=https://pnhr01hdi-corpdir.msappproxy.net/jupyter/notebooks/PySpark/notebook.ipynb
Blocking Cross Origin API request.  
Origin: https://xxx.xxx.xxx, Host: pnhr01.j101qxjrl4zebmhb0vmhg044xe.ax.internal.cloudapp.net:8001
```

A Jupyter-napló "Origin" mezőjében is megjelenhet egy IP-cím.

## <a name="cause"></a>Ok

Ezt a hibát néhány dolog okozhatja:

- Ha úgy konfigurálta a Hálózati biztonsági csoport (NSG) szabályait, hogy korlátozza a fürthöz való hozzáférést. Az NSG-szabályokkal való hozzáférés korlátozása továbbra is lehetővé teszi, hogy közvetlenül hozzáférjen az Apache Ambari-hoz és más szolgáltatásokhoz az IP-cím használatával a fürt neve helyett. Azonban a Jupyter elérésekor egy 404 -es "Nem található" hibaüzenet et láthat.

- Ha a HDInsight-átjárónak a szabványostól `xxx.azurehdinsight.net`eltérő testreszabott DNS-nevet adott meg.

## <a name="resolution"></a>Megoldás:

1. Módosítsa a jupyter.py fájlokat az alábbi két helyen:

    ```bash
    /var/lib/ambari-server/resources/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    /var/lib/ambari-agent/cache/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    ```

1. Keresse meg a `NotebookApp.allow_origin='\"https://{2}.{3}\"'` sort, hogy `NotebookApp.allow_origin='\"*\"'`azt mondja: És változtassa meg: .

1. Indítsa újra a Jupyter szolgáltatást az Ambari szolgáltatásból.

1. A `ps aux | grep jupyter` parancssorba beíráskor meg kell jelennie, hogy lehetővé teszi bármely URL-cím csatlakoztatását.

Ez kevésbé biztonságos, mint a már meglévő beállítás. De feltételezhető, hogy a fürthöz való hozzáférés korlátozott, és hogy egy kívülről is csatlakozhat a fürthöz, mivel mi nsg a helyén.

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

* Válaszokat kaphat az Azure szakértőitől az [Azure közösségi támogatásán](https://azure.microsoft.com/support/community/)keresztül.

* Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba a hivatalos Microsoft Azure-fiókkal, amely javítja az ügyfélélményt azáltal, hogy az Azure-közösséget a megfelelő erőforrásokhoz, válaszokhoz, támogatáshoz és szakértőkhöz csatlakoztatja.

* Ha további segítségre van szüksége, támogatási kérelmet nyújthat be az [Azure Portalról.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Válassza a **menüsor Támogatás parancsát,** vagy nyissa meg a **Súgó + támogatási** központot. További információkért tekintse át az Azure-támogatási kérelem létrehozása című, [továbbcímű tájékoztatót.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Az Előfizetés-kezelés hez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetésrészét képezi, a technikai támogatást pedig az [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/)egyike biztosítja.
