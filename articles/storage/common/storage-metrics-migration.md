---
title: Azure Storage-metrikák migrálás |} A Microsoft Docs
description: Ismerje meg, hogyan telepíthet át régi metrikák az új mérőszámok az Azure Monitor által felügyelt.
services: storage
author: fhryo-msft
ms.service: storage
ms.topic: article
ms.date: 03/30/2018
ms.author: fryu
ms.component: common
ms.openlocfilehash: fc11e29b03df617c4b5bb6f4fbb43cd478001d42
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39521421"
---
# <a name="azure-storage-metrics-migration"></a>Azure Storage-metrikák migrálás

Azure Storage integrálható stratégiával, amely egységes Azure-ban a figyelő élmény igazítva, mérőszámok az Azure Monitor platformra. A jövőben a régi metrikák szolgáltatás egy alapján az Azure policy előzetes értesítés megszűnik. Ha régi tármetrikák támaszkodnak, akkor áttelepítése előtt a szolgáltatás záró dátuma, a metrika-információkat fenntartása érdekében.

Ez a cikk bemutatja, hogyan át a régi metrikák az új mérőszámok.

## <a name="understand-old-metrics-that-are-managed-by-azure-storage"></a>Az Azure Storage által kezelt régi metrikák ismertetése

Az Azure Storage régi metrikaértékek gyűjti és összesíti, és tárolja őket $Metric táblák ugyanazon a tárfiókon belül. Az Azure portal segítségével állítsa be a figyelés diagramra. Az Azure Storage SDK-k használatával is beolvashatja az adatokat a séma alapuló $Metric táblából. További információkért lásd: [Storage Analytics](./storage-analytics.md).

Régi adatok csak az Azure Blob storage segítségével a kapacitási mérőszámot. Régi metrikák tranzakciók mérőszámait a Blob storage, Table storage, Azure Files és Queue storage nyújtanak. 

Régi metrikák egybesimított sémában tervezték. A Tervező metrika értéke nulla eredményez, ha nem rendelkezik a forgalom minták a metrika elindítása. Ha például a **ServerTimeoutError** értéke 0-$Metric táblák még akkor is, ha egy tárfiókba az élő adatforgalomban a kiszolgáló időtúllépési hibákat nem kap.

## <a name="understand-new-metrics-managed-by-azure-monitor"></a>Új mérőszámok az Azure Monitor által kezelt ismertetése

Az új tárolási mérőszámokra az Azure Storage az Azure Monitor háttérrendszerének metrikaadatok bocsát ki. Az Azure Monitor egy egyesített figyelési környezetet, beleértve az adatokat a portálról is biztosít adatbetöltés és. További részletekért olvassa el ezt [cikk](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Új metrikák adja meg a kapacitási mérőszámot és a tranzakciók mérőszámait a Blob, tábla, fájl, várólista és a premium storage.

Mérőszámparancshoz egyik funkciója, amely az Azure Monitor biztosít. Az Azure Storage új metrika séma meghatározásakor a kialakítás fogad el. A metrikák támogatott dimenziók találhat további információkat a [Azure Storage-mérőszámok az Azure Monitor](./storage-metrics-in-azure-monitor.md). Több dimenzió terve költséghatékonyságának Adatbetöltési mindkét sávszélesség és a kapacitás tárolja a metrikákat nyújt. Ennek következtében ha a forgalom nem aktiválta-e kapcsolódó metrikák, a kapcsolódó metrikaadatok nem jön létre. Például, ha a forgalom nem aktiválta-e kiszolgáló időtúllépési hibákat, az Azure Monitor nem adnak vissza adatot metrika értékének lekérdezésekor **tranzakciók** léptékű **ResponseType** egyenlő **ServerTimeoutError**.

## <a name="metrics-mapping-between-old-metrics-and-new-metrics"></a>Metrikák leképezés metrikák régi és új metrikák között

Metrikus adatok programozott módon olvasási, meg kell alkalmaznia az új metrika séma a Programok telepítése és törlése. A módosítások segít jobban megérteni, hivatkozhat a leképezés a következő táblázatban:

**A kapacitás-metrikák**

| Régi mérőszám | Új metrika |
| ------------------- | ----------------- |
| **Kapacitás**            | **BlobCapacity** dimenzióhoz **BlobType** egyenlő **BlockBlob** vagy **PageBlob** |
| **ObjectCount**        | **BlobCount** dimenzióhoz **BlobType** egyenlő **BlockBlob** vagy **PageBlob** |
| **ContainerCount**      | **ContainerCount** |

A következő metrikák, új ajánlatokat, amelyek nem támogatják a a régi metrikák:
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

**Tranzakció-mérőszámot**

| Régi mérőszám | Új metrika |
| ------------------- | ----------------- |
| **AnonymousAuthorizationError** | A dimenzió tranzakció **ResponseType** egyenlő **AuthorizationError** |
| **AnonymousClientOtherError** | A dimenzió tranzakció **ResponseType** egyenlő **ClientOtherError** |
| **AnonymousClientTimeoutError** | A dimenzió tranzakció **ResponseType** egyenlő **ClientTimeoutError** |
| **AnonymousNetworkError** | A dimenzió tranzakció **ResponseType** egyenlő **NetworkError** |
| **AnonymousServerOtherError** | A dimenzió tranzakció **ResponseType** egyenlő **ServerOtherError** |
| **AnonymousServerTimeoutError** | A dimenzió tranzakció **ResponseType** egyenlő **ServerTimeoutError** |
| **AnonymousSuccess** | A dimenzió tranzakció **ResponseType** egyenlő **sikeres** |
| **AnonymousThrottlingError** | A dimenzió tranzakció **ResponseType** egyenlő **ClientThrottlingError** vagy **ServerBusyError** |
| **AuthorizationError** | A dimenzió tranzakció **ResponseType** egyenlő **AuthorizationError** |
| **Rendelkezésre állás** | **Rendelkezésre állás** |
| **AverageE2ELatency** | **SuccessE2ELatency** |
| **Averageserverlatency értéket mutatnak** | **SuccessServerLatency** |
| **ClientOtherError** | A dimenzió tranzakció **ResponseType** egyenlő **ClientOtherError** |
| **ClientTimeoutError** | A dimenzió tranzakció **ResponseType** egyenlő **ClientTimeoutError** |
| **NetworkError** | A dimenzió tranzakció **ResponseType** egyenlő **NetworkError** |
| **PercentAuthorizationError** | A dimenzió tranzakció **ResponseType** egyenlő **AuthorizationError** |
| **PercentClientOtherError** | A dimenzió tranzakció **ResponseType** egyenlő **ClientOtherError** |
| **Percentnetworkerror értéket mutatnak** | A dimenzió tranzakció **ResponseType** egyenlő **NetworkError** |
| **PercentServerOtherError** | A dimenzió tranzakció **ResponseType** egyenlő **ServerOtherError** |
| **PercentSuccess** | A dimenzió tranzakció **ResponseType** egyenlő **sikeres** |
| **Percentthrottlingerror értéket mutatnak** | A dimenzió tranzakció **ResponseType** egyenlő **ClientThrottlingError** vagy **ServerBusyError** |
| **Percenttimeouterror értéket mutatnak** | A dimenzió tranzakció **ResponseType** egyenlő **ServerTimeoutError** vagy **ResponseType** egyenlő **ClientTimeoutError** |
| **SASAuthorizationError** | A dimenzió tranzakció **ResponseType** egyenlő **AuthorizationError** |
| **SASClientOtherError** | A dimenzió tranzakció **ResponseType** egyenlő **ClientOtherError** |
| **SASClientTimeoutError** | A dimenzió tranzakció **ResponseType** egyenlő **ClientTimeoutError** |
| **SASNetworkError** | A dimenzió tranzakció **ResponseType** egyenlő **NetworkError** |
| **SASServerOtherError** | A dimenzió tranzakció **ResponseType** egyenlő **ServerOtherError** |
| **SASServerTimeoutError** | A dimenzió tranzakció **ResponseType** egyenlő **ServerTimeoutError** |
| **SASSuccess** | A dimenzió tranzakció **ResponseType** egyenlő **sikeres** |
| **SASThrottlingError** | A dimenzió tranzakció **ResponseType** egyenlő **ClientThrottlingError** vagy **ServerBusyError** |
| **ServerOtherError** | A dimenzió tranzakció **ResponseType** egyenlő **ServerOtherError** |
| **ServerTimeoutError** | A dimenzió tranzakció **ResponseType** egyenlő **ServerTimeoutError** |
| **Sikeres** | A dimenzió tranzakció **ResponseType** egyenlő **sikeres** |
| **ThrottlingError** | **Tranzakciók** dimenzióhoz **ResponseType** egyenlő **ClientThrottlingError** vagy **ServerBusyError**|
| **TotalBillableRequests** | **Tranzakciók** |
| **TotalEgress** | **Kimenő forgalom** |
| **TotalIngress** | **Bejövő forgalom** |
| **TotalRequests** | **Tranzakciók** |

## <a name="faq"></a>GYIK

### <a name="how-should-i-migrate-existing-alert-rules"></a>Hogyan kell áttelepíteni meglévő riasztási szabályok?

Ha a klasszikus riasztási szabályok régi storage-mérőszámok alapján létrehozott, szüksége szabályok alapján az új metrika-séma új riasztást létrehozni.

### <a name="is-new-metric-data-stored-in-the-same-storage-account-by-default"></a>Új metrika-adatok tárolva van ugyanazon a tárfiókon alapértelmezés szerint?

Nem. A metrikaadatokat tárfiókba archiválhatja, használja a [Azure Monitor diagnosztikai beállítás API](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate).

## <a name="next-steps"></a>További lépések

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
* [Storage-mérőszámok az Azure Monitor](./storage-metrics-in-azure-monitor.md)
