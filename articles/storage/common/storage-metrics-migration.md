---
title: Azure Storage-mérőszámok áttelepítése | Microsoft dokumentumok
description: Ismerje meg, hogyan telepítheti át a régi metrikákat az Azure Monitor által felügyelt új metrikákba.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/30/2018
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 537369c9466b1083723642ec9e93fcdf25056c5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68855344"
---
# <a name="azure-storage-metrics-migration"></a>Az Azure Storage metrikák áttelepítése

Az Azure Storage az Azure Monitor-élményének egységesítésének stratégiájával összhangban integrálja a metrikákat az Azure Monitor platformba. A jövőben a szolgáltatás a régi metrikák végén egy korai értesítés az Azure-szabályzat alapján. Ha a régi tárolási metrikák, át kell telepítenie a szolgáltatás befejezési dátuma előtt, hogy a metrika adatok karbantartása érdekében.

Ez a cikk bemutatja, hogyan lehet áttelepíteni a régi metrikák az új metrikák.

## <a name="understand-old-metrics-that-are-managed-by-azure-storage"></a>Az Azure Storage által felügyelt régi metrikák ismertetése

Az Azure Storage régi metrikaértékeket gyűjt, és összesíti és tárolja őket $Metric táblákban ugyanazon a tárfiókon belül. Az Azure Portal segítségével figyelési diagramot állíthat be. Az Azure Storage SDK-k is használhatja az adatokat $Metric táblák, amelyek a séma alapján. További információ: [Storage Analytics](./storage-analytics.md).

A régi metrikák csak az Azure Blob storage kapacitásmetrikákat biztosítanak. A régi metrikák tranzakciós metrikákat biztosítanak a Blob-, a Table storage, az Azure Files és a Queue storage tranzakciós metrikáihoz.

A régi metrikák egy sima sémában vannak megtervezve. A terv nulla metrikaértéket eredményez, ha nem rendelkezik a metrikát kiváltó forgalmi mintákkal. A **ServerTimeoutError** értéke például 0 $Metric táblában akkor is, ha az élő forgalomból nem kap kiszolgálói időtúlhasználati hibákat egy tárfiókba.

## <a name="understand-new-metrics-managed-by-azure-monitor"></a>Az Azure Monitor által felügyelt új metrikák ismertetése

Az új tárolási metrikák, az Azure Storage kibocsátja a metrika adatokat az Azure Monitor háttérrendszerbe. Az Azure Monitor egységes figyelési élményt biztosít, beleértve a portálról származó adatokat, valamint az adatok betöltését. További részletekért olvassa el ezt a [cikket](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Az új metrikák kapacitás-metrikákat és tranzakciós metrikákat biztosítanak a Blob, table, File, Queue és prémium szintű storage-on.

A többdimenziós az Azure Monitor egyik szolgáltatása. Az Azure Storage elfogadja a tervezésúj metrikaséma meghatározásában. A metrikák támogatott dimenzióihoz az [Azure Storage-metrikák](./storage-metrics-in-azure-monitor.md)ban talál részleteket az Azure Monitorban. A többdimenziós kialakítás költséghatékonyságot biztosít mind a betöltési sávszélesség, mind a mérőszámok tárolásából származó kapacitás tekintetében. Következésképpen, ha a forgalom nem indított kapcsolódó metrikák, a kapcsolódó metrika adatok nem jönlétre létre. Ha például a forgalom nem váltott ki kiszolgálói időtúllépések hibáit, az Azure Monitor nem ad vissza adatokat, amikor lekérdezi a **ResponseType** dimenzióval rendelkező **tranzakciók** értékét a **ServerTimeoutError értékével.**

## <a name="metrics-mapping-between-old-metrics-and-new-metrics"></a>Metrikák leképezése a régi metrikák és az új metrikák között

Ha programozott módon olvassa a metrikaadatokat, el kell fogadnia az új metrikasémát a programokban. A módosítások jobb megértéséhez tekintse meg az alábbi táblázatban felsorolt leképezéseket:

**Kapacitásmetrikák**

| Régi metrika | Új mérőszám |
| ------------------- | ----------------- |
| **Kapacitás**            | **BlobCapacity** a **BlobType** dimenzióval egyenlő **A BlockBlob** vagy **PageBlob** |
| **Objektumszám**        | **BlobCount** a **BlobType** dimenzióval, amely megegyezik **a BlockBlob** vagy **a PageBlob dimenzióval** |
| **Konténerszám**      | **Konténerszám** |

A következő mérőszámok olyan új ajánlatok, amelyeket a régi mérőszámok nem támogatnak:
* **Táblakapacitás**
* **Tábladarabszám**
* **Táblaentitásszáma**
* **Várólistakapacitás**
* **Várólistaszáma**
* **QueueMessageCount (Üzenetsor)**
* **Fájlkapacitás**
* **Fájlszám**
* **FileShareCount**
* **Használtkapacitás**

**Tranzakciós mutatók**

| Régi metrika | Új mérőszám |
| ------------------- | ----------------- |
| **AnonymousAuthorizationError (Névtelen engedélyezési hiba)** | A **ResponseType** dimenzióval egyenlő **műveletek az AuthorizationError** és a Név **nélküli** **hitelesítés dimenzióhitelesítéssel** |
| **AnonymousClientOtherhiba** | A **ResponseType** dimenzióval egyenlő **ügyfélhiba** és **a névtelennel** egyenlő **dimenzióhitelesítéssel** rendelkező tranzakciók |
| **AnonymousClientTimeouthiba** | A **ResponseType** dimenzióval egyenlő **ügyfélidő-hiba** és **a névtelennel** egyenlő **dimenzióhitelesítésdimenzióval** rendelkező tranzakciók |
| **AnonymousNetworkError** | A **ResponseType** dimenzióval egyenlő **a NetworkError** dimenzióval, a Hitelesítés pedig **névtelennel** egyenlő **dimenziójú tranzakciók** |
| **AnonymousServerOtherhiba** | A **ResponseType** dimenzióval egyenlő **műveletek A ServerOtherError** és a **Hitelesítés** dimenzió egyenlő **névtelennel** |
| **AnonymousServerTimeouthiba** | A **ResponseType** dimenzióval egyenlő **a ServerTimeoutError** és a **Névtelennel** egyenlő **dimenzióhitelesítéssel** rendelkező tranzakciók |
| **AnonymousSuccess** | A **ResponseType** dimenzióval **egyenlő, a Sikeres** és a Név **nélküli** **hitelesítés dimenziójú tranzakciók** |
| **AnonymousThrottlingError** | A **ResponseType** dimenzióval megegyező **tranzakciók az ÜgyfélthrottlingError** vagy **a ServerBusyError** és a **Névtelen hitelesítés dimenziójú hitelesítésdimenzióval** **Anonymous** |
| **Engedélyezési hiba** | A **ResponseType** dimenzióval egyenlő **az AuthorizationError** dimenzióval rendelkező tranzakciók |
| **Rendelkezésre állás** | **Rendelkezésre állás** |
| **AverageE2ELatency** | **SuccessE2ELatency (SuccessE2ELatency)** |
| **AverageServerLatency** | **SuccessServerLatency (Sikereskiszolgálók átkának)** |
| **ClientOtherhiba** | A **ResponseType** dimenzióval rendelkező tranzakciók megegyeznek **a ClientOtherError-al** |
| **Ügyfélidő-hiba** | A **ResponseType** dimenzióval rendelkező tranzakciók megegyeznek **a ClientTimeoutError-al** |
| **Hálózati hiba** | A **ResponseType** dimenzióval egyenlő **hálózati hibával** megegyező tranzakciók |
| **PercentAuthorizationError (PercentAuthorizationError)** | A **ResponseType** dimenzióval egyenlő **az AuthorizationError** dimenzióval rendelkező tranzakciók |
| **PercentClientOtherhiba** | A **ResponseType** dimenzióval rendelkező tranzakciók megegyeznek **a ClientOtherError-al** |
| **PercentNetworkError (Százalékhálózati hiba)** | A **ResponseType** dimenzióval egyenlő **hálózati hibával** megegyező tranzakciók |
| **PercentServerOtherhiba** | A **ResponseType** dimenzióval rendelkező tranzakciók a **ServerOtherError-nak** megfelelő |
| **Százalékos siker** | A **ResponseType** dimenzióval egyenlő **sikerrel** megegyező tranzakciók |
| **Százalékthrottlingerror** | A **ResponseType** dimenzióval megegyező **ügyfélthrottlingerror** vagy **ServerBusyError** dimenzióval rendelkező tranzakciók |
| **PercentTimeouterror (Idő-idő hiba)** | A ResponseType dimenzióval **megegyező, a ClientTimeoutError-al** egyenlő **ResponseType** vagy **ResponseType** dimenzióval rendelkező tranzakciók **ClientTimeoutError** |
| **SASAuthorizationError** | A **ResponseType** dimenzióval egyenlő **az AuthorizationError** dimenzióval, a Hitelesítés dimenzió pedig a **SAS-szal** egyenlő **dimenzióval** |
| **SASClientOtherError** | A **ResponseType** dimenzióval egyenlő **ügyfélhiba** és **a SAS-szal** egyenlő **dimenzióhitelesítéssel** rendelkező tranzakciók |
| **SASClientTimeoutHiba** | A **ResponseType** dimenzióval egyenlő **ügyfélidő-hiba** és **a SAS-nak** megfelelő **dimenzióhitelesítéssel** rendelkező tranzakciók |
| **SASNetworkHiba** | A **ResponseType** dimenzióval egyenlő **hálózatihiba** és **a SAS-szal** egyenlő **dimenzióhitelesítéssel** rendelkező tranzakciók |
| **SASServerOtherError** | A **ResponseType** dimenzióval egyenlő **műveletek A ServerOtherError** és a **SAS-nak** megfelelő **dimenzióhitelesítés** |
| **SASServerTimeoutHiba** | A **ResponseType** dimenzióval egyenlő **a ServerTimeoutError** és a **SAS-szal** egyenlő **dimenzióhitelesítéssel** rendelkező tranzakciók |
| **SASSiker** | A **ResponseType** dimenzióval **egyenlő, a** SAS-szal egyenlő **dimenziójú tranzakciók,** a Hitelesítés pedig a **SAS-szal** egyenlő |
| **SASThrottling Hiba** | A **ResponseType** dimenzióval megegyező **tranzakciók az ÜgyfélthrottlingError** vagy **a ServerBusyError** és a **SAS-szal** egyenlő **dimenzióhitelesítéssel** |
| **ServerOtherHiba** | A **ResponseType** dimenzióval rendelkező tranzakciók a **ServerOtherError-nak** megfelelő |
| **ServerTimeouthiba** | A **ResponseType** dimenzióval egyenlő **a ServerTimeoutError** dimenzióval rendelkező tranzakciók |
| **Siker** | A **ResponseType** dimenzióval egyenlő **sikerrel** megegyező tranzakciók |
| **ThrottlingError** | **A** **ResponseType** dimenzióval megegyező **ügyfélthrottlingerror** vagy **ServerBusyError** dimenzióval rendelkező tranzakciók|
| **Összesszámlázható kérések** | **Tranzakciók** |
| **TotalEgress** | **Kimenő forgalom** |
| **TotalIngress** | **Bejövő forgalom** |
| **TotalRequests** | **Tranzakciók** |

## <a name="faq"></a>GYIK

### <a name="how-should-i-migrate-existing-alert-rules"></a>Hogyan telepíthetem át a meglévő riasztási szabályokat?

Ha klasszikus riasztási szabályokat hozott létre a régi tárolási metrikák alapján, új riasztási szabályokat kell létrehoznia az új metrikaséma alapján.

### <a name="is-new-metric-data-stored-in-the-same-storage-account-by-default"></a>Alapértelmezés szerint az új metrikaadatok ugyanabban a tárfiókban tárolódnak?

Nem. A metrikaadatok tárfiókba archiválásához használja az [Azure Monitor diagnosztikai beállítási API-ját.](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate)

## <a name="next-steps"></a>További lépések

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
* [Tárolási metrikák az Azure Monitorban](./storage-metrics-in-azure-monitor.md)
