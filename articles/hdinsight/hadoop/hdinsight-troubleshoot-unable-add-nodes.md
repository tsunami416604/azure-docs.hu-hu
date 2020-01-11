---
title: Nem lehet csomópontokat hozzáadni az Azure HDInsight-fürthöz
description: A csomópontok Apache Hadoop fürthöz való hozzáadása az Azure HDInsight-ben – problémamegoldás
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 97d7f34fff324a9959292460e534c15110c3e532
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75894100"
---
# <a name="scenario-unable-to-add-nodes-to-azure-hdinsight-cluster"></a>Forgatókönyv: nem lehet csomópontokat hozzáadni az Azure HDInsight-fürthöz

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Nem lehet csomópontokat hozzáadni az Azure HDInsight-fürthöz.

## <a name="cause"></a>Ok

Az okok eltérőek lehetnek.

## <a name="resolution"></a>Felbontás

A [fürt mérete](../hdinsight-scaling-best-practices.md) funkció használatával kiszámíthatja a fürthöz szükséges további magok számát. Ez a munkavégző csomópontok magjainak számától függ. Ezután próbálkozzon az alábbi lépések valamelyikével:

* Ellenőrizze, hogy vannak-e elérhető magok a fürt helyén.

* Ellenőrizze a más helyeken rendelkezésre álló magok számát. Fontolja meg a fürt egy másik, elegendő elérhető maggal rendelkező helyen való létrehozását.

* Ha egy adott hely magkvótáját szeretné növelni, hozzon létre egy támogatási jegyet a HDInsight magkvóta-növeléséhez.

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Csatlakozás a [@AzureSupporthoz](https://twitter.com/azuresupport) – a hivatalos Microsoft Azure fiók a felhasználói élmény javításához az Azure-Közösség és a megfelelő erőforrások összekapcsolásával: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
