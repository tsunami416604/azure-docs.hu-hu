---
title: A Apache Hive OutOfMemory-hibába való csatlakozása az Azure HDInsight
description: A OutOfMemory hibák kezelése "a GC rezsi-korlátja túllépte a hibát"
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 182ca8243b2e6050a72c22f52b9fcd0d2cef37c5
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494218"
---
# <a name="scenario-joins-in-apache-hive-leads-to-an-outofmemory-error-in-azure-hdinsight"></a>Forgatókönyv: a Apache Hive lévő illesztések az Azure HDInsight OutOfMemory-hibába vezetnek.

Ez a cikk az interaktív lekérdezési összetevők Azure HDInsight-fürtökben való használatakor felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Apache Hive illesztések alapértelmezett viselkedése egy tábla teljes tartalmának betöltése a memóriába, így a csatlakozás a Térkép/csökkentés lépés végrehajtása nélkül is elvégezhető. Ha a kaptár tábla túl nagy a memóriához való illeszkedéshez, a lekérdezés sikertelen lehet.

## <a name="cause"></a>Ok

Ha az illesztések megfelelő méretű struktúrában futnak, a rendszer a következő hibát észlelte:

```
Caused by: java.lang.OutOfMemoryError: GC overhead limit exceeded error.
```

## <a name="resolution"></a>Felbontás

A következő struktúra konfigurációs értékének beállításával megakadályozhatja, hogy a struktúra betöltsön a táblákat a memóriába az illesztések között (egy Térkép/csökkentési lépés végrehajtása helyett):

```
hive.auto.convert.join=false
```

## <a name="next-steps"></a>További lépések

Ha a beállítás értéke nem oldja meg a problémát, látogasson el a következő témakör egyikére...

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Csatlakozás a [@AzureSupporthoz](https://twitter.com/azuresupport) – a hivatalos Microsoft Azure fiók a felhasználói élmény javításához az Azure-Közösség és a megfelelő erőforrások összekapcsolásával: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletes információkat az [Azure-támogatáskérések létrehozásával](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) foglalkozó témakörben talál. Az előfizetés-kezeléssel és számlázással kapcsolatos támogatás a Microsoft Azure-előfizetés részét képezi, míg a technikai támogatást [Azure-támogatási csomagjainkkal](https://azure.microsoft.com/support/plans/) biztosítjuk.
