---
title: Átviteli réteg biztonsága az Azure HDInsightban
description: Az átviteli réteg biztonsága (TLS) és a biztonságos szoftvercsatornák rétege (SSL) olyan kriptográfiai protokollok, amelyek számítógépes hálózaton keresztül biztosítják a kommunikáció biztonságát.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: fbe602581ebcea6385fb9cc9953d8e48272ce429
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81771962"
---
# <a name="transport-layer-security-in-azure-hdinsight"></a>Átviteli réteg biztonsága az Azure HDInsightban

A HDInsight-fürthöz a nyilvános `https://CLUSTERNAME.azurehdinsight.net` fürtvégponton keresztül létesített kapcsolatok a fürtátjáró-csomópontokon keresztül vannak proxied. Ezek a kapcsolatok a TLS nevű protokoll segítségével vannak biztosítva. A TLS magasabb verzióinak az átjárókon történő érvényesítése növeli a kapcsolatok biztonságát. A TLS újabb verzióinak használatáról a [TLS 1.0 probléma megoldása című témakörben](https://docs.microsoft.com/security/solving-tls1-problem)talál további információt.

Alapértelmezés szerint az Azure HDInsight-fürtök elfogadják a TLS 1.2-es kapcsolatokat nyilvános HTTPS-végpontokon, és a régebbi verziókat a visszamenőleges kompatibilitás érdekében. Az Azure Portal vagy a Resource Manager-sablon használatával szabályozhatja az átjárócsomópontokon támogatott minimális TLS-verziót a fürt létrehozása során. A portálhoz válassza ki a TLS-verziót a **Biztonság + hálózat lapon** a fürt létrehozása során. A Resource Manager sablon üzembe helyezéskor használja a **minSupportedTlsVersion** tulajdonságot. Mintasablonról a [HDInsight minimális TLS 1.2 rövid útmutató sablonja olvashat.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-minimum-tls) Ez a tulajdonság három értéket támogat: "1.0", "1.1" és "1.2", amelyek a TLS 1.0+, a TLS 1.1+ és a TLS 1.2+ értékeknek felelnek meg.

> [!IMPORTANT]
> 2020. június 30-tól kezdődően az Azure HDInsight minden HTTPS-kapcsolatra kikényszeríti a TLS 1.2-es vagy újabb verziókat. Azt javasoljuk, hogy győződjön meg arról, hogy minden ügyfele készen áll a TLS 1.2-es vagy újabb verziók kezelésére. További információ: [Azure HDInsight TLS 1.2 Enforcement](https://azure.microsoft.com/updates/azure-hdinsight-tls-12-enforcement/).

## <a name="next-steps"></a>További lépések

* [Virtuális hálózat tervezése az Azure HDInsight számára](./hdinsight-plan-virtual-network-deployment.md)
* [Hozzon létre virtuális hálózatokat az Azure HDInsight-fürtökhöz.](hdinsight-create-virtual-network.md)
* [Hálózati biztonsági csoportok](../virtual-network/security-overview.md).
