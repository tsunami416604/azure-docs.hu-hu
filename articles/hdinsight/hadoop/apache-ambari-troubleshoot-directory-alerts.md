---
title: Apache Ambari Directory-riasztások az Azure HDInsight
description: Az Apache Ambari Directory-riasztások lehetséges okainak és megoldásainak megvitatása és elemzése a HDInsight-ben.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: d8b1f705eb08d1c58b32e0cbd7c57722a6de93cc
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2020
ms.locfileid: "93285522"
---
# <a name="scenario-apache-ambari-directory-alerts-in-azure-hdinsight"></a>Forgatókönyv: Apache Ambari Directory-riasztások az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Az Apache Ambari az alábbihoz hasonló hibákat kap:

```
1/1 local-dirs have errors: [ /mnt/resource/hadoop/yarn/local : Cannot create directory: /mnt/resource/hadoop/yarn/local ]
1/1 log-dirs have errors: [ /mnt/resource/hadoop/yarn/log : Cannot create directory: /mnt/resource/hadoop/yarn/log ]
```

## <a name="cause"></a>Ok

Az érintett munkavégző csomópont (ok) ból a Ambari-riasztásból említett könyvtárak hiányoznak.

## <a name="resolution"></a>Feloldás

Manuálisan hozza létre a hiányzó könyvtárakat az érintett munkavégző csomópont (ok) hoz.

1. SSH-t a megfelelő munkavégző csomóponthoz.

1. Root felhasználó beolvasása: `sudo su` .

1. A szükséges könyvtárak rekurzív létrehozása.

1. A könyvtárak tulajdonosának és csoportjának módosítása.

    ```bash
    chown -R yarn /mnt/resource/hadoop/yarn/local
    chgrp -R hadoop /mnt/resource/hadoop/yarn/local
    chown -R yarn /mnt/resource/hadoop/yarn/log
    chgrp -R hadoop /mnt/resource/hadoop/yarn/log
    ```

1. Az Apache Ambari felhasználói felületén tiltsa le, majd engedélyezze a riasztást.

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]