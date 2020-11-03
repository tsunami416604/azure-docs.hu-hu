---
title: A Apache Spark streaming-alkalmazás 24 nap elteltével leáll az Azure HDInsight
description: Egy Apache Spark streaming-alkalmazás 24 nap elteltével leáll, és nincsenek hibák a naplófájlokban.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 83bdb4a9913ae817204fb37320f5bdb8174d5baf
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288036"
---
# <a name="scenario-apache-spark-streaming-application-stops-after-executing-for-24-days-in-azure-hdinsight"></a>Forgatókönyv: Apache Spark folyamatos átviteli alkalmazás az Azure HDInsight 24 napjainak végrehajtása után leáll

Ez a cikk a Apache Spark-összetevők Azure HDInsight-fürtökben való használatakor felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Egy Apache Spark streaming-alkalmazás 24 nap elteltével leáll, és nincsenek hibák a naplófájlokban.

## <a name="cause"></a>Ok

Az `livy.server.session.timeout` érték azt határozza meg, hogy az Apache Livy mennyi ideig várjon, amíg a munkamenet befejeződik. Ha a munkamenet hossza eléri az `session.timeout` értéket, a rendszer automatikusan leveszi a Livy-munkamenetet és az alkalmazást.

## <a name="resolution"></a>Feloldás

A hosszú ideig futó feladatok esetében növelje a `livy.server.session.timeout` Ambari felhasználói felületének értékét. A Livy konfigurációját a Ambari felhasználói felületén keresztül érheti el az URL-cím használatával `https://<yourclustername>.azurehdinsight.net/#/main/services/LIVY/configs` .

Cserélje le a `<yourclustername>` nevet a HDInsight-fürt nevére a portálon látható módon.

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]