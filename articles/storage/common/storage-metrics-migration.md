---
title: Azure Storage-metrikák migrálása | Microsoft Docs
description: Ismerje meg, hogyan telepítheti át a régi mérőszámokat az Azure Monitor által felügyelt új mérőszámokra.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/30/2018
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 537369c9466b1083723642ec9e93fcdf25056c5e
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855344"
---
# <a name="azure-storage-metrics-migration"></a>Azure Storage-metrikák migrálása

Az Azure Storage az Azure-beli monitorozási élmény egységesítésének stratégiájával összhangban integrálja a metrikákat a Azure Monitor platformra. A jövőben a régi mérőszámok szolgáltatásának az Azure-szabályzaton alapuló korai értesítéssel kell végződnie. Ha a régi tárolási mérőszámokat használja, át kell telepítenie a szolgáltatás befejezési dátuma előtt a metrikai adatok fenntartása érdekében.

Ez a cikk bemutatja, hogyan telepíthet át a régi mérőszámokból az új mérőszámokra.

## <a name="understand-old-metrics-that-are-managed-by-azure-storage"></a>Az Azure Storage által felügyelt régi metrikák ismertetése

Az Azure Storage a régi metrikai értékeket gyűjti, és egy adott Storage-fiókban található $Metric-táblákban összesíti és tárolja őket. A Azure Portal használatával beállíthatja a figyelési diagramot. Az Azure Storage SDK-k segítségével is beolvashatja a sémán alapuló $Metric táblák adatait. További információ: [Storage Analytics](./storage-analytics.md).

A régi mérőszámok csak az Azure Blob Storage-ban biztosítanak kapacitási metrikákat. A régi mérőszámok tranzakciós mérőszámokat biztosítanak a blob Storage, a Table Storage, a Azure Files és a várólista tárolásához.

A régi mérőszámok lapos sémában vannak kialakítva. A terv nulla metrikai értéket eredményez, ha nem rendelkezik a mérőszámot kiváltó forgalmi mintákkal. Például a **ServerTimeoutError** értéke 0 értékre van állítva $metric táblázatokban akkor is, ha nem kap kiszolgálói időtúllépési hibát az élő forgalomból egy Storage-fiókba.

## <a name="understand-new-metrics-managed-by-azure-monitor"></a>Az Azure Monitor által felügyelt új metrikák megismerése

Az új tárolási metrikák esetében az Azure Storage a metrikus adatokat a Azure Monitor háttérbe bocsátja. A Azure Monitor egységes figyelési élményt nyújt, beleértve a portálon tárolt adatok, valamint az adatok betöltését. További részletekért tekintse meg ezt a [cikket](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Az új metrikák kapacitás-metrikákat és tranzakciós mérőszámokat biztosítanak a blob, a Table, a file, a üzenetsor és a Premium Storage szolgáltatásban.

A többdimenziós funkció a Azure Monitor által biztosított szolgáltatások egyike. Az Azure Storage az új metrikai séma definiálásával alkalmazza a kialakítást. A metrikák támogatott méretei esetében az [Azure Storage-metrikák](./storage-metrics-in-azure-monitor.md)részleteit Azure monitorban találhatja meg. A többdimenziós kialakítás költséghatékonyságot biztosít a betöltéstől és a kapacitástól a metrikák tárolására szolgáló sávszélességtől. Következésképpen, ha a forgalom nem váltott ki kapcsolódó metrikákat, a kapcsolódó metrikai adatok nem lesznek létrehozva. Ha például a forgalom nem váltott ki időtúllépési hibát a kiszolgálókon, a Azure Monitor nem ad vissza adatokat, amikor lekérdezi a metrikus **tranzakciók** értékét a **ServerTimeoutError** **ResponseType** egyenlő dimenzióval.

## <a name="metrics-mapping-between-old-metrics-and-new-metrics"></a>Metrikák leképezése a régi mérőszámok és az új metrikák között

Ha programozott módon olvas be metrikus adatokat, a programokban el kell fogadnia az új metrikai sémát. A módosítások jobb megismeréséhez tekintse meg a következő táblázatban felsorolt leképezést:

**Kapacitás metrikái**

| Régi metrika | Új metrika |
| ------------------- | ----------------- |
| **Kapacitás**            | **BlobCapacity** a **BlobType** dimenzióval egyenlő a **BlockBlob** vagy a **PageBlob** |
| **ObjectCount**        | **BlobCount** a **BlobType** dimenzióval egyenlő a **BlockBlob** vagy a **PageBlob** |
| **ContainerCount**      | **ContainerCount** |

A következő mérőszámok olyan új ajánlatok, amelyeket a régi mérőszámok nem támogatnak:
* **TableCapacity**
* **TableCount**
* **TableEntityCount**
* **QueueCapacity**
* **QueueCount**
* **QueueMessageCount**
* **FileCapacity**
* **FileCount**
* **FileShareCount**
* **UsedCapacity**

**Tranzakciós metrikák**

| Régi metrika | Új metrika |
| ------------------- | ----------------- |
| **AnonymousAuthorizationError** | A **AuthorizationError** és a dimenzió- **hitelesítéssel** egyenlő **ResponseType** rendelkező tranzakciók **névtelenek** |
| **AnonymousClientOtherError** | A **ClientOtherError** és a dimenzió- **hitelesítéssel** egyenlő **ResponseType** rendelkező tranzakciók **névtelenek** |
| **AnonymousClientTimeoutError** | A **ClientTimeoutError** és a dimenzió- **hitelesítéssel** egyenlő **ResponseType** rendelkező tranzakciók **névtelenek** |
| **AnonymousNetworkError** | A **NetworkError** és a dimenzió- **hitelesítéssel** egyenlő **ResponseType** rendelkező tranzakciók **névtelenek** |
| **AnonymousServerOtherError** | A **ServerOtherError** és a dimenzió- **hitelesítéssel** egyenlő **ResponseType** rendelkező tranzakciók **névtelenek** |
| **AnonymousServerTimeoutError** | A **ServerTimeoutError** és a dimenzió- **hitelesítéssel** egyenlő **ResponseType** rendelkező tranzakciók **névtelenek** |
| **AnonymousSuccess** | A dimenzióval rendelkező tranzakciók a **ResponseType** egyenlőek a **sikeres** és a dimenzió- **hitelesítéssel** . |
| **AnonymousThrottlingError** | A dimenzió **ResponseType** rendelkező tranzakciók **ClientThrottlingError** vagy **ServerBusyError** , és a dimenzió **hitelesítésének** értéke **Névtelen** |
| **AuthorizationError** | A **AuthorizationError** **ResponseType** egyenlő dimenzióval rendelkező tranzakciók |
| **Rendelkezésre állás** | **Rendelkezésre állás** |
| **AverageE2ELatency** | **SuccessE2ELatency** |
| **Averageserverlatency értéket mutatnak** | **SuccessServerLatency** |
| **ClientOtherError** | A **ClientOtherError** **ResponseType** egyenlő dimenzióval rendelkező tranzakciók |
| **ClientTimeoutError** | A **ClientTimeoutError** **ResponseType** egyenlő dimenzióval rendelkező tranzakciók |
| **NetworkError** | A **NetworkError** **ResponseType** egyenlő dimenzióval rendelkező tranzakciók |
| **PercentAuthorizationError** | A **AuthorizationError** **ResponseType** egyenlő dimenzióval rendelkező tranzakciók |
| **PercentClientOtherError** | A **ClientOtherError** **ResponseType** egyenlő dimenzióval rendelkező tranzakciók |
| **Percentnetworkerror értéket mutatnak** | A **NetworkError** **ResponseType** egyenlő dimenzióval rendelkező tranzakciók |
| **PercentServerOtherError** | A **ServerOtherError** **ResponseType** egyenlő dimenzióval rendelkező tranzakciók |
| **PercentSuccess** | A dimenzió **ResponseType** rendelkező tranzakciók sikerrel egyenlőek |
| **Percentthrottlingerror értéket mutatnak** | A **ClientThrottlingError** vagy ServerBusyError **ResponseType** egyenlő dimenzióval rendelkező tranzakciók |
| **Percenttimeouterror értéket mutatnak** | A **ServerTimeoutError** vagy ResponseType **ResponseType** egyenlő dimenzióval rendelkező tranzakciók a **ClientTimeoutError** |
| **SASAuthorizationError** | Az **AuthorizationError** és a dimenzió- **hitelesítéssel** egyenlő **ResponseType** rendelkező tranzakciók az **sas** -vel egyenlőek |
| **SASClientOtherError** | Az **ClientOtherError** és a dimenzió- **hitelesítéssel** egyenlő **ResponseType** rendelkező tranzakciók az **sas** -vel egyenlőek |
| **SASClientTimeoutError** | Az **ClientTimeoutError** és a dimenzió- **hitelesítéssel** egyenlő **ResponseType** rendelkező tranzakciók az **sas** -vel egyenlőek |
| **SASNetworkError** | Az **NetworkError** és a dimenzió- **hitelesítéssel** egyenlő **ResponseType** rendelkező tranzakciók az **sas** -vel egyenlőek |
| **SASServerOtherError** | Az **ServerOtherError** és a dimenzió- **hitelesítéssel** egyenlő **ResponseType** rendelkező tranzakciók az **sas** -vel egyenlőek |
| **SASServerTimeoutError** | Az **ServerTimeoutError** és a dimenzió- **hitelesítéssel** egyenlő **ResponseType** rendelkező tranzakciók az **sas** -vel egyenlőek |
| **SASSuccess** | A dimenzió **ResponseType** rendelkező tranzakciók a **sikeres** és a dimenzió- **hitelesítéssel** egyenlőek az **sas** -vel |
| **SASThrottlingError** | A **ClientThrottlingError** vagy **ServerBusyError** és a **ResponseType** egyenlő dimenzió- **hitelesítéssel** rendelkező tranzakciók |
| **ServerOtherError** | A **ServerOtherError** **ResponseType** egyenlő dimenzióval rendelkező tranzakciók |
| **ServerTimeoutError** | A **ServerTimeoutError** **ResponseType** egyenlő dimenzióval rendelkező tranzakciók |
| **Sikeres** | A dimenzió **ResponseType** rendelkező tranzakciók sikerrel egyenlőek |
| **ThrottlingError** | A **ClientThrottlingError** vagy ServerBusyError **ResponseType** egyenlő dimenzióval rendelkező **tranzakciók**|
| **TotalBillableRequests** | **Tranzakciók** |
| **TotalEgress** | **Kimenő forgalom** |
| **TotalIngress** | **Bejövő forgalom** |
| **TotalRequests** | **Tranzakciók** |

## <a name="faq"></a>GYIK

### <a name="how-should-i-migrate-existing-alert-rules"></a>Hogyan kell migrálni a meglévő riasztási szabályokat?

Ha a régi tárolási mérőszámok alapján klasszikus riasztási szabályokat hozott létre, új riasztási szabályokat kell létrehoznia az új metrikai séma alapján.

### <a name="is-new-metric-data-stored-in-the-same-storage-account-by-default"></a>Alapértelmezés szerint ugyanaz a Storage-fiók tárolja az új metrikai adatokat?

Nem. A metrikai adatok Storage-fiókba való archiválásához használja a [Azure monitor diagnosztikai beállítás API](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate)-t.

## <a name="next-steps"></a>További lépések

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
* [Tárolási metrikák a Azure Monitorban](./storage-metrics-in-azure-monitor.md)
