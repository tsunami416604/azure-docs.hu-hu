---
title: Jupyter Server 404 hiba a "Cross Origin API blokkolása" miatt az Azure HDInsight
description: A Jupyter-kiszolgáló 404 "nem található" hibát okozott a "Cross Origin API blokkolása" miatt az Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: 291fc7f385f652005f44c3e0cd52d4929e914989
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70736191"
---
# <a name="scenario-jupyter-server-404-not-found-error-due-to-blocking-cross-origin-api-in-azure-hdinsight"></a>Forgatókönyv: A Jupyter-kiszolgáló 404 "nem található" hibát okozott a "Cross Origin API blokkolása" miatt az Azure HDInsight

Ez a cikk a Apache Spark-összetevők Azure HDInsight-fürtökben való használatakor felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Ha a HDInsight-on keresztül fér hozzá a Jupyter szolgáltatáshoz, a "nem található" hibaüzenet jelenik meg. Ha bejelöli a Jupyter-naplókat, a következőhöz hasonló lesz:

```log
[W 2018-08-21 17:43:33.352 NotebookApp] 404 PUT /api/contents/PySpark/notebook.ipynb (10.16.0.144) 4504.03ms referer=https://pnhr01hdi-corpdir.msappproxy.net/jupyter/notebooks/PySpark/notebook.ipynb
Blocking Cross Origin API request.  
Origin: https://xxx.xxx.xxx, Host: hn0-pnhr01.j101qxjrl4zebmhb0vmhg044xe.ax.internal.cloudapp.net:8001
```

Az IP-címet a Jupyter napló "forrás" mezőjében is láthatja.

## <a name="cause"></a>Ok

Ezt a hibát egy pár dolog okozhatja:

- Ha konfigurálta a hálózati biztonsági csoport (NSG) szabályait, hogy korlátozza a hozzáférést a fürthöz. A NSG-szabályokkal való hozzáférés korlátozása továbbra is lehetővé teszi az Apache Ambari és más szolgáltatások közvetlen elérését a fürt neve helyett az IP-cím használatával. A Jupyter elérésekor azonban egy 404 "nem található" hibaüzenet jelenhet meg.

- Ha a HDInsight-átjárót a standardtól `xxx.azurehdinsight.net`eltérő egyéni DNS-névvel adta meg.

## <a name="resolution"></a>Megoldás:

1. Módosítsa a jupyter.py-fájlokat a következő két helyen:

    ```bash
    /var/lib/ambari-server/resources/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    /var/lib/ambari-agent/cache/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    ```

1. Keresse meg a sort, amely a következőket írja: `NotebookApp.allow_origin='\"https://{2}.{3}\"'`És módosítsa a következőre `NotebookApp.allow_origin='\"*\"'`:.

1. Indítsa újra a Jupyter szolgáltatást a Ambari.

1. A `ps aux | grep jupyter` parancssorba való beíráskor meg kell jeleníteni, hogy bármely URL-cím lehetővé teszi a kapcsolódást.

Ez kevésbé biztonságos, mint a már meglévő beállítás. A rendszer azonban feltételezi, hogy a fürthöz való hozzáférés korlátozott, és egy-egy kívülről csatlakozhat a fürthöz, mert NSG a helyükön.

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Csatlakozás az Azure-Közösség a megfelelő erőforrásokhoz való csatlakoztatásával – a hivatalos Microsoft Azure fiókkal – a felhasználói élmény javítása érdekében: válaszok, támogatás és szakértők. [@AzureSupport](https://twitter.com/azuresupport)

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
