---
title: Transport Layer Security az Azure HDInsight
description: A Transport Layer Security (TLS) és a Secure Sockets Layer (SSL) olyan titkosítási protokollok, amelyek kommunikációs biztonságot biztosítanak a számítógép hálózatán.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: b74ca75b26d4d98c79091683f428eb39e5827665
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82183500"
---
# <a name="transport-layer-security-in-azure-hdinsight"></a>Transport Layer Security az Azure HDInsight

A HDInsight-fürthöz a nyilvános fürt végpontján keresztül létesített kapcsolatok a `https://CLUSTERNAME.azurehdinsight.net` fürt átjárójának csomópontjain keresztüli proxyk. Ezek a kapcsolatok a TLS nevű protokoll használatával biztonságosak. A TLS magasabb verzióinak kényszerítése az átjárók esetében javítja a kapcsolatok biztonságát. További információ a TLS újabb verzióinak használatáról: [a tls 1,0-probléma megoldása](https://docs.microsoft.com/security/solving-tls1-problem).

Alapértelmezés szerint az Azure HDInsight-fürtök elfogadják a TLS 1,2-kapcsolatokat a nyilvános HTTPS-végpontokon, valamint a régebbi verziókat a visszamenőleges kompatibilitás érdekében. A fürt létrehozása során a Azure Portal vagy egy Resource Manager-sablon használatával szabályozhatja az átjáró csomópontjain támogatott minimális TLS-verziót. A portálon válassza ki a TLS-verziót a **Biztonság + hálózatkezelés** lapon a fürt létrehozása során. A Resource Manager-sablonok központi telepítésének ideje alatt használja a **minSupportedTlsVersion** tulajdonságot. Minta sablon: [HDInsight minimális TLS 1,2 rövid útmutató sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-minimum-tls). Ez a tulajdonság három értéket támogat: "1,0", "1,1" és "1,2", amely a TLS 1.0 +, a TLS 1.1 + és a TLS 1.2 + értéknek felel meg.

> [!IMPORTANT]
> Az Azure HDInsight az 2020. június 30-ig az összes HTTPS-kapcsolathoz a TLS 1,2-es vagy újabb verziókat fogja érvényesíteni. Javasoljuk, hogy minden ügyfél készen álljon a TLS 1,2-es vagy újabb verzióinak kezelésére. További információ: [Azure HDINSIGHT TLS 1,2 kényszerítés](https://azure.microsoft.com/updates/azure-hdinsight-tls-12-enforcement/).

## <a name="next-steps"></a>További lépések

* [Virtuális hálózat megtervezése az Azure HDInsight](./hdinsight-plan-virtual-network-deployment.md)
* [Hozzon létre virtuális hálózatokat az Azure HDInsight-fürtökhöz](hdinsight-create-virtual-network.md).
* [Hálózati biztonsági csoportok](../virtual-network/security-overview.md).
