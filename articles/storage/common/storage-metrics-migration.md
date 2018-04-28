---
title: Az Azure Storage metrikák áttelepítése |} Microsoft Docs
description: További tudnivalók az örökölt metrikák áttelepítése új mérőszámok Azure figyelő által felügyelt.
services: storage
documentationcenter: na
author: fhryo-msft
manager: cbrooks
editor: fhryo-msft
ms.assetid: ''
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 03/30/2018
ms.author: fryu
ms.openlocfilehash: c4dc9b231668315af16c625314c737fee99d672d
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="azure-storage-metrics-migration"></a>Az Azure Storage metrikák áttelepítése

Azure Egységesítő figyelő kapcsolatos stratégia összhangban, Azure Storage a-modulok metrikák figyelő Azure platformra. A jövőben az örökölt metrikák szolgáltatás leállított korai küldött értesítést a Azure házirend alapján. Ha örökölt storage mérőszámainak használ, akkor áttelepítése előtt a szolgáltatás záró dátum metrika adatainak megőrzése érdekében.

Ez a cikk végigvezeti az örökölt metrikák áttelepítése az új mérőszámok.

## <a name="understand-legacy-metrics-managed-by-azure-storage"></a>A hagyományos metrikák kezeli az Azure Storage ismertetése

Az Azure Storage örökölt metrika értékek, összesítések, gyűjt, és tárolja őket a tárfiókon belül $Metric táblában. Azure portál segítségével diagram figyelés beállítása, és Azure Storage SDK-k beolvasni az adatokat a séma alapján $Metric táblák is használhatja. További részletekért jelentheti a [tárolási analitika](./storage-analytics.md).

Örökölt metrikák kapacitási mérőszámokat a Blob csak és biztosítanak tranzakciók metrikák Blob, Table, fájlt és várólista.

Örökölt metrikák strukturálatlan sémában készültek. A Tervező metrika értéke nulla eredményez, amikor nincs időt. a mérték forgalmat. Például **ServerTimeoutError** $Metric táblák 0 értékre van állítva, akkor is, ha nem kapott bármely kiszolgálói időtúllépést az élő forgalom tárfiókba.

## <a name="understand-new-metrics-managed-by-azure-monitor"></a>Azure-figyelő által felügyelt új mérőszámok ismertetése

Új tároló metrikáihoz az Azure Storage Azure figyelő backend metrika értékét bocsát ki. Az Azure biztosít, egy egységes figyelési kezelőfelülettel, beleértve a portálról adatokat továbbá az adatfeldolgozást. További részletekért jelentheti a [cikk](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Új mérőszámok kapacitási mérőszámokat és biztosítanak tranzakciók metrikák Blob, Table, fájl, várólista és prémium szintű storage.

Több dimenzió az egyik Azure figyelő által nyújtott szolgáltatásokat. Az Azure Storage fogad el új metrika séma meghatározása a tervező. A mérőszámokat támogatott dimenziók található Részletek ezen [Azure figyelése az Azure Storage mérőszámainak](./storage-metrics-in-azure-monitor.md). Több dimenzió tervezési költséghatékonyságának adatfeldolgozást mindkét sávszélesség és a kapacitás tárolja a metrikákat biztosít. Ezért ha a forgalom nem kiváltása kapcsolódó metrikák, az arra vonatkozó Átjárómetrika adatok nem lesz létrehozva. Például, ha a forgalom nem kiváltása bármely kiszolgálói időtúllépést, Azure visszatérnek adatot nem előfordulhat, hogy a metrika értékének **tranzakciók** léptékű **ResponseType** egyenlő**ServerTimeoutError**.

## <a name="metrics-mapping-between-legacy-metrics-and-new-metrics"></a>A hagyományos metrikák és új mérőszámok metrikák leképezése

Ha programozottan olvassa el a metrika értékét, akkor elfogadják az új metrika séma a Programok telepítése és. Jobb megértése érdekében a módosításokat, olvassa el a leképezés a következő táblázatban felsorolt:

**A kapacitás metrikák**

| A hagyományos metrika | Új metrika |
| ------------------- | ----------------- |
| Kapacitás            | Van-e a dimenzióval BlockBlob vagy PageBlob BlobType BlobCapacity |
| ObjectCount         | Blobok száma egyenlő BlockBlob vagy PageBlob BlobType léptékű |
| ContainerCount      | ContainerCount |

A következő adatok gyűjtése le új ajánlatokat, amelyek nem támogatják a hagyományos metrikák:
* TableCapacity
* TableCount
* TableEntityCount
* QueueCapacity
* QueueCount
* QueueMessageCount
* FileCapacity
* FileCount
* FileShareCount
* UsedCapacity

**Tranzakció metrikák**

| A hagyományos metrika | Új metrika |
| ------------------- | ----------------- |
| AnonymousAuthorizationError | A dimenzió ResponseType AuthorizationError egyenlőnek tranzakciók |
| AnonymousClientOtherError | A dimenzió ResponseType ClientOtherError egyenlőnek tranzakciók |
| AnonymousClientTimeoutError | A dimenzió ResponseType ClientTimeoutError egyenlőnek tranzakciók |
| AnonymousNetworkError | A dimenzió ResponseType NetworkError egyenlőnek tranzakciók |
| AnonymousServerOtherError | A dimenzió ResponseType ServerOtherError egyenlőnek tranzakciók |
| AnonymousServerTimeoutError | A dimenzió ResponseType ServerTimeoutError egyenlőnek tranzakciók |
| AnonymousSuccess | A dimenzió ResponseType egyenlő sikeres tranzakciók |
| AnonymousThrottlingError | Van-e a dimenzióval ClientThrottlingError vagy ServerBusyError ResponseType tranzakciók |
| AuthorizationError | A dimenzió ResponseType AuthorizationError egyenlőnek tranzakciók |
| Rendelkezésre állás | Rendelkezésre állás |
| AverageE2ELatency | SuccessE2ELatency |
| AverageServerLatency | SuccessServerLatency |
| ClientOtherError | A dimenzió ResponseType ClientOtherError egyenlőnek tranzakciók |
| ClientTimeoutError | A dimenzió ResponseType ClientTimeoutError egyenlőnek tranzakciók |
| NetworkError | A dimenzió ResponseType NetworkError egyenlőnek tranzakciók |
| PercentAuthorizationError | A dimenzió ResponseType AuthorizationError egyenlőnek tranzakciók |
| PercentClientOtherError | A dimenzió ResponseType ClientOtherError egyenlőnek tranzakciók |
| PercentNetworkError | A dimenzió ResponseType NetworkError egyenlőnek tranzakciók |
| PercentServerOtherError | A dimenzió ResponseType ServerOtherError egyenlőnek tranzakciók |
| PercentSuccess | A dimenzió ResponseType egyenlő sikeres tranzakciók |
| PercentThrottlingError | Van-e a dimenzióval ClientThrottlingError vagy ServerBusyError ResponseType tranzakciók |
| PercentTimeoutError | A dimenzió ResponseType ServerTimeoutError egyenlőnek vagy annál ClientTimeoutError ResponseType tranzakciók|
| SASAuthorizationError | A dimenzió ResponseType AuthorizationError egyenlőnek tranzakciók |
| SASClientOtherError | A dimenzió ResponseType ClientOtherError egyenlőnek tranzakciók |
| SASClientTimeoutError | A dimenzió ResponseType ClientTimeoutError egyenlőnek tranzakciók |
| SASNetworkError | A dimenzió ResponseType NetworkError egyenlőnek tranzakciók |
| SASServerOtherError | A dimenzió ResponseType ServerOtherError egyenlőnek tranzakciók |
| SASServerTimeoutError | A dimenzió ResponseType ServerTimeoutError egyenlőnek tranzakciók |
| SASSuccess | A dimenzió ResponseType egyenlő sikeres tranzakciók |
| SASThrottlingError | Van-e a dimenzióval ClientThrottlingError vagy ServerBusyError ResponseType tranzakciók |
| ServerOtherError | A dimenzió ResponseType ServerOtherError egyenlőnek tranzakciók |
| ServerTimeoutError | A dimenzió ResponseType ServerTimeoutError egyenlőnek tranzakciók |
| Sikeres | A dimenzió ResponseType egyenlő sikeres tranzakciók |
| ThrottlingError | Van-e a dimenzióval ClientThrottlingError vagy ServerBusyError ResponseType tranzakciók |
| TotalBillableRequests | Tranzakciók |
| TotalEgress | Kimenő forgalom |
| TotalIngress | Belépő |
| TotalRequests | Tranzakciók |

## <a name="faq"></a>GYIK

* Hogyan kell áttelepíteni meglévő riasztási szabályok?

A:, ha a szabályok alapján örökölt storage mérőszámainak klasszikus riasztást hozott létre, szüksége hozzon létre új riasztás szabályok új metrika séma alapján.

* Alapértelmezés szerint ugyanabban a tárfiókban tárolt új metrika értékét fogja?

V: nem. Ha archiválja a metrika tárfiókkal van szüksége, használhatja [diagnosztikai beállítása az Azure-figyelő](https://azure.microsoft.com/blog/azure-monitor-multiple-diagnostic-settings/)

## <a name="next-steps"></a>További lépések

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
* [A figyelő az Azure Storage mérőszámainak](./storage-metrics-in-azure-monitor.md)
