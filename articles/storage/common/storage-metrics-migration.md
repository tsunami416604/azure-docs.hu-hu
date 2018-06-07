---
title: Az Azure Storage metrikák áttelepítése |} Microsoft Docs
description: Ismerje meg, hogyan telepíthet át régi metrikák csatlakozva új Azure-figyelő által felügyelt.
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
ms.openlocfilehash: c64061aee94e8c08a3f6bcae78cffca0b4172d97
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34650672"
---
# <a name="azure-storage-metrics-migration"></a>Az Azure Storage metrikák áttelepítése

Összhangban az egységes az Azure-ban a figyelő élmény stratégia, Azure Storage integrálja az Azure-figyelő platformra metrikákat. A régi metrikákat a szolgáltatást a jövőben egy Azure házirend alapján értesítésről végződhet. Ha régi storage mérőszámainak használ, akkor áttelepítése előtt a szolgáltatás záró dátum metrika adatainak megőrzése érdekében.

Ez a cikk bemutatja, hogyan telepíthetők át a régi metrikák az új mérőszámok.

## <a name="understand-old-metrics-that-are-managed-by-azure-storage"></a>Azure Storage által kezelt régi metrikák ismertetése

Az Azure Storage gyűjti a régi metrika értékeket, és összesíti, és tárolja őket a tárfiókon belül $Metric táblában. Az Azure portál segítségével állítsa be a figyelés diagramra. Az Azure Storage SDK-k segítségével is beolvasni az adatokat a séma alapján $Metric táblákhoz. További információkért lásd: [tárolási analitika](./storage-analytics.md).

Régi metrikák biztosítanak kapacitási mérőszámokat csak Azure Blob Storage tárolóban. Régi metrikák tranzakció metrikák biztosítanak Blob-tároló, a Table storage, Azure-fájlok és a Queue storage. 

Régi metrikák strukturálatlan séma készültek. A Tervező metrika értéke nulla eredményez, amikor nincs időt. a mérték forgalmat. Például a **ServerTimeoutError** értéke 0 $Metric táblák akkor is, ha nem kapott bármely kiszolgálói időtúllépést az élő forgalom tárfiókba.

## <a name="understand-new-metrics-managed-by-azure-monitor"></a>Azure-figyelő által felügyelt új mérőszámok ismertetése

Új tárolási metrikáihoz az Azure Storage a metrika adatokat az Azure-figyelő háttér bocsát ki. Az Azure biztosít, egy egységes figyelési kezelőfelülettel, beleértve a portálról adatokat továbbá az adatfeldolgozást. További részletekért jelentheti a [cikk](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Új mérőszámok kapacitási mérőszámokat és biztosítanak tranzakciók metrikák Blob, Table, fájl, várólista és prémium szintű storage.

Több dimenzió egyike a funkciókat, amelyeket az Azure biztosít. Az Azure Storage fogad el új metrika séma meghatározása a tervező. A mérőszámokat támogatott dimenziók esetében a részletek található [Azure figyelése az Azure Storage mérőszámainak](./storage-metrics-in-azure-monitor.md). Több dimenzió tervezési költséghatékonyságának adatfeldolgozást mindkét sávszélesség és a kapacitás tárolja a metrikákat biztosít. Ezért ha a forgalom nem kiváltása kapcsolódó metrikák, az arra vonatkozó Átjárómetrika adatok nem lesz létrehozva. Például ha a forgalom nem kiváltása bármely kiszolgálói időtúllépést, Azure-figyelő nem adnak vissza adatot a metrika értékének lekérdezésekor **tranzakciók** léptékű **ResponseType** egyenlő **ServerTimeoutError**.

## <a name="metrics-mapping-between-old-metrics-and-new-metrics"></a>Régi metrikák és új mérőszámok metrikák leképezése

Ha programozottan olvassa el a metrika értékét, akkor elfogadják az új metrika séma a Programok telepítése és. Jobb megértése érdekében a módosításokat, hivatkozhat a leképezés a következő táblázatban:

**A kapacitás metrikák**

| Régi metrika | Új metrika |
| ------------------- | ----------------- |
| **Kapacitás**            | **BlobCapacity** van-e a dimenzióval **BlobType** egyenlő **BlockBlob** vagy **PageBlob** |
| **ObjectCount**        | **BlobCount** van-e a dimenzióval **BlobType** egyenlő **BlockBlob** vagy **PageBlob** |
| **ContainerCount**      | **ContainerCount** |

A következő adatok gyűjtése le új ajánlatokat, amelyek a régi metrikák nem támogatják:
* **TableCapacity**
* **TableCount**
* **TableEntityCount**
* **QueueCapacity**
* **QueueCount**
* **QueueMessageCount**
* **FileCapacity**
* **Filecount;/%totalfilecount**
* **FileShareCount**
* **UsedCapacity**

**Tranzakció metrikák**

| Régi metrika | Új metrika |
| ------------------- | ----------------- |
| **AnonymousAuthorizationError** | Van-e a dimenzióval tranzakciók **ResponseType** egyenlő **AuthorizationError** |
| **AnonymousClientOtherError** | Van-e a dimenzióval tranzakciók **ResponseType** egyenlő **ClientOtherError** |
| **AnonymousClientTimeoutError** | Van-e a dimenzióval tranzakciók **ResponseType** egyenlő **ClientTimeoutError** |
| **AnonymousNetworkError** | Van-e a dimenzióval tranzakciók **ResponseType** egyenlő **NetworkError** |
| **AnonymousServerOtherError** | Van-e a dimenzióval tranzakciók **ResponseType** egyenlő **ServerOtherError** |
| **AnonymousServerTimeoutError** | Van-e a dimenzióval tranzakciók **ResponseType** egyenlő **ServerTimeoutError** |
| **AnonymousSuccess** | Van-e a dimenzióval tranzakciók **ResponseType** egyenlő **sikeres** |
| **AnonymousThrottlingError** | Van-e a dimenzióval tranzakciók **ResponseType** egyenlő **ClientThrottlingError** vagy **ServerBusyError** |
| **AuthorizationError** | Van-e a dimenzióval tranzakciók **ResponseType** egyenlő **AuthorizationError** |
| **Rendelkezésre állás** | **Rendelkezésre állás** |
| **AverageE2ELatency** | **SuccessE2ELatency** |
| **AverageServerLatency** | **SuccessServerLatency** |
| **ClientOtherError** | Van-e a dimenzióval tranzakciók **ResponseType** egyenlő **ClientOtherError** |
| **ClientTimeoutError** | Van-e a dimenzióval tranzakciók **ResponseType** egyenlő **ClientTimeoutError** |
| **NetworkError** | Van-e a dimenzióval tranzakciók **ResponseType** egyenlő **NetworkError** |
| **PercentAuthorizationError** | Van-e a dimenzióval tranzakciók **ResponseType** egyenlő **AuthorizationError** |
| **PercentClientOtherError** | Van-e a dimenzióval tranzakciók **ResponseType** egyenlő **ClientOtherError** |
| **PercentNetworkError** | Van-e a dimenzióval tranzakciók **ResponseType** egyenlő **NetworkError** |
| **PercentServerOtherError** | Van-e a dimenzióval tranzakciók **ResponseType** egyenlő **ServerOtherError** |
| **PercentSuccess** | Van-e a dimenzióval tranzakciók **ResponseType** egyenlő **sikeres** |
| **PercentThrottlingError** | Van-e a dimenzióval tranzakciók **ResponseType** egyenlő **ClientThrottlingError** vagy **ServerBusyError** |
| **PercentTimeoutError** | Van-e a dimenzióval tranzakciók **ResponseType** egyenlő **ServerTimeoutError** vagy **ResponseType** egyenlő **ClientTimeoutError** |
| **SASAuthorizationError** | Van-e a dimenzióval tranzakciók **ResponseType** egyenlő **AuthorizationError** |
| **SASClientOtherError** | Van-e a dimenzióval tranzakciók **ResponseType** egyenlő **ClientOtherError** |
| **SASClientTimeoutError** | Van-e a dimenzióval tranzakciók **ResponseType** egyenlő **ClientTimeoutError** |
| **SASNetworkError** | Van-e a dimenzióval tranzakciók **ResponseType** egyenlő **NetworkError** |
| **SASServerOtherError** | Van-e a dimenzióval tranzakciók **ResponseType** egyenlő **ServerOtherError** |
| **SASServerTimeoutError** | Van-e a dimenzióval tranzakciók **ResponseType** egyenlő **ServerTimeoutError** |
| **SASSuccess** | Van-e a dimenzióval tranzakciók **ResponseType** egyenlő **sikeres** |
| **SASThrottlingError** | Van-e a dimenzióval tranzakciók **ResponseType** egyenlő **ClientThrottlingError** vagy **ServerBusyError** |
| **ServerOtherError** | Van-e a dimenzióval tranzakciók **ResponseType** egyenlő **ServerOtherError** |
| **ServerTimeoutError** | Van-e a dimenzióval tranzakciók **ResponseType** egyenlő **ServerTimeoutError** |
| **Sikeres** | Van-e a dimenzióval tranzakciók **ResponseType** egyenlő **sikeres** |
| **ThrottlingError** | **Tranzakciók** van-e a dimenzióval **ResponseType** egyenlő **ClientThrottlingError** vagy **ServerBusyError**|
| **TotalBillableRequests** | **Tranzakciók** |
| **TotalEgress** | **Kimenő forgalom** |
| **TotalIngress** | **Bejövő forgalom** |
| **TotalRequests** | **Tranzakciók** |

## <a name="faq"></a>GYIK

### <a name="how-should-i-migrate-existing-alert-rules"></a>Hogyan kell áttelepíteni meglévő riasztási szabályok?

Ha klasszikus riasztási szabályok a régi tároló mérőszámok alapján létrehozott szabályok az új metrika séma alapján új riasztás létrehozása, szeretné.

### <a name="is-new-metric-data-stored-in-the-same-storage-account-by-default"></a>Új metrikaadatokat tárolódik a tárfiókon alapértelmezés szerint?

Nem. Archiválja a metrikaadatokat tárfiókba, használja a [Azure figyelő diagnosztikai beállítás API](https://docs.microsoft.com/en-us/rest/api/monitor/diagnosticsettings/createorupdate).

## <a name="next-steps"></a>További lépések

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
* [Figyelő Azure Storage mérőszámainak](./storage-metrics-in-azure-monitor.md)
