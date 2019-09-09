---
title: Nem lehet csomópontokat hozzáadni az Azure HDInsight-fürthöz
description: A csomópontok Apache Hadoop fürthöz való hozzáadása az Azure HDInsight-ben – problémamegoldás
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/31/2019
ms.openlocfilehash: 0c8fdf11f435cc459dfb8475f8983b29dfbc5d9f
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/09/2019
ms.locfileid: "70810566"
---
# <a name="scenario-unable-to-add-nodes-to-azure-hdinsight-cluster"></a>Forgatókönyv: Nem lehet csomópontokat hozzáadni az Azure HDInsight-fürthöz

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Nem lehet csomópontokat hozzáadni az Azure HDInsight-fürthöz.

## <a name="cause"></a>Ok

Az okok eltérőek lehetnek.

## <a name="resolution"></a>Megoldás:

A [fürt mérete](../hdinsight-scaling-best-practices.md) funkció használatával kiszámíthatja a fürthöz szükséges további magok számát. Ez a munkavégző csomópontok magjainak számától függ. Ezután próbálkozzon az alábbi lépések valamelyikével:

* Ellenőrizze, hogy vannak-e elérhető magok a fürt helyén.

* Ellenőrizze a más helyeken rendelkezésre álló magok számát. Fontolja meg a fürt egy másik, elegendő elérhető maggal rendelkező helyen való létrehozását.

* Ha egy adott hely magkvótáját szeretné növelni, hozzon létre egy támogatási jegyet a HDInsight magkvóta-növeléséhez.

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Csatlakozás az Azure-Közösség a megfelelő erőforrásokhoz való csatlakoztatásával – a hivatalos Microsoft Azure fiókkal – a felhasználói élmény javítása érdekében: válaszok, támogatás és szakértők. [@AzureSupport](https://twitter.com/azuresupport)

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
