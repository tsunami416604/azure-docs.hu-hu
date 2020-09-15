---
title: Port ütközés a szolgáltatások Azure HDInsight való indításakor
description: Hibaelhárítási lépések és lehetséges megoldások a portok ütközésével kapcsolatban az Azure HDInsight-fürtökkel való interakció során.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: 0d39f31e9d52359d0c91317280a7f8db06c1c1b0
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2020
ms.locfileid: "90530964"
---
# <a name="scenario-port-conflict-when-starting-services-in-azure-hdinsight"></a>Forgatókönyv: a port ütközik az Azure HDInsight szolgáltatásainak indításakor

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Egy szolgáltatás nem indul el.

## <a name="cause"></a>Ok

A port ütközése létezik.

## <a name="resolution"></a>Feloldás

### <a name="method-1"></a>1. módszer

Az alábbi parancsokkal lekérheti és leállíthatja az összes futó folyamatot, amelyeket a port hibája érint.

```bash
netstat -lntp | grep <port>
ps -ef | grep <service>
kill -9 <service>
```

Ezután indítsa el a szolgáltatást.

### <a name="method-2"></a>2. módszer

Indítsa újra a csomópontot.

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Kapcsolódjon [@AzureSupport](https://twitter.com/azuresupport) a-a hivatalos Microsoft Azure fiókhoz a felhasználói élmény javítása érdekében. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
