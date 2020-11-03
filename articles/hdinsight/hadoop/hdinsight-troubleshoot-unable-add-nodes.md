---
title: Nem lehet csomópontokat hozzáadni az Azure HDInsight-fürthöz
description: A csomópontok Apache Hadoop fürthöz való hozzáadása az Azure HDInsight-ben – problémamegoldás
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 089eab563c83384bdb7e1681044fa89f5eb7f6c0
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289041"
---
# <a name="scenario-unable-to-add-nodes-to-azure-hdinsight-cluster"></a>Forgatókönyv: nem lehet csomópontokat hozzáadni az Azure HDInsight-fürthöz

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Nem lehet csomópontokat hozzáadni az Azure HDInsight-fürthöz.

## <a name="cause"></a>Ok

Az okok eltérőek lehetnek.

## <a name="resolution"></a>Feloldás

A [fürt mérete](../hdinsight-scaling-best-practices.md) funkció használatával kiszámíthatja a fürthöz szükséges további magok számát. Ez a munkavégző csomópontok magjainak számától függ. Ezután próbálkozzon az alábbi lépések valamelyikével:

* Ellenőrizze, hogy vannak-e elérhető magok a fürt helyén.

* Ellenőrizze a más helyeken rendelkezésre álló magok számát. Fontolja meg a fürt egy másik, elegendő elérhető maggal rendelkező helyen való létrehozását.

* Ha egy adott hely magkvótáját szeretné növelni, hozzon létre egy támogatási jegyet a HDInsight magkvóta-növeléséhez.

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]