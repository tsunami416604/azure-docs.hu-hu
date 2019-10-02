---
title: Azure Block blob Storage – teljesítmény szintjei – Azure Storage
description: Az Azure Blob Storage teljesítmény-szintjei.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 10/02/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: ac483a338b7d71142b89b13e41fc048346ac037f
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802655"
---
# <a name="azure-block-blob-storage-performance-tiers"></a>Azure Block blob Storage – teljesítmény szintjei

Mivel a vállalatok teljesítményre érzékeny felhőalapú alkalmazásokat helyeznek üzembe, fontos, hogy a költséghatékony adattárolási lehetőségek különböző teljesítményszint esetén is elérhetők legyenek.

Az Azure Block blob Storage két különböző teljesítményszint kínál:

- Prémium: nagy tranzakciós díjszabásra és egyszámjegyű, konzisztens tárolási késésre optimalizált
- Standard: nagy kapacitású és magas átviteli sebességre optimalizált

A következő szempontokat kell figyelembe venni a különböző teljesítményszint esetén:

- A standard szintű teljesítmény minden [Azure-régióban](https://azure.microsoft.com/global-infrastructure/services/?products=storage)elérhető. A prémium szintű teljesítmény a [kiválasztott régiókban](https://azure.microsoft.com/global-infrastructure/services/?products=storage)érhető el.
- A prémium szintű teljesítmény optimális díjszabást biztosít a nagy tranzakciós díjszabású alkalmazások számára, így csökkentheti a számítási feladatokhoz szükséges [összes tárolási költséget](https://azure.microsoft.com/blog/reducing-overall-storage-costs-with-azure-premium-blob-storage/) .
- A prémium szintű teljesítmény működéséhez a blob Storage-fiókok blokkolása és a blob hozzáfűzése.
- A standard szintű teljesítmény általános célú v1, általános célú v2 és blob Storage-fiókokkal érhető el.
- A prémium és a standard szintű teljesítmény egyaránt támogatja a nagy teljesítményű [blokkos blobokat](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/). A nagy adatforgalom-blokkoló Blobok a prémium szintű teljesítmény érdekében érhetők el 256 KiB-nál nagyobb mértékben. A nagy teljesítményű blokk Blobok standard teljesítményhez érhetők el, mint 4 MiB Put blokk, vagy blob-méreteket tesznek elérhetővé.
- A prémium szintű teljesítmény jelenleg csak helyileg redundáns tárolással (LRS) érhető el.

## <a name="premium-performance"></a>Prémium szintű teljesítmény

A prémium szintű teljesítmény-blokkoló blob Storage nagy teljesítményű hardveren keresztül teszi elérhetővé az adattárolást. Az adatok tárolása SSD-meghajtókon történik, amelyek alacsony késésre vannak optimalizálva. Az SSD-k nagyobb átviteli sebességet biztosítanak, mint a hagyományos merevlemezek.

A prémium szintű teljesítmény-blokkoló blob Storage ideális olyan munkaterhelésekhez, amelyek gyors és konzisztens válaszidőt igényelnek. A legjobb a sok kis tranzakciót végző munkaterhelések esetében.

## <a name="standard-performance"></a>Standard teljesítmény

A standard szintű teljesítmény a különböző [hozzáférési szinteket](storage-blob-storage-tiers.md) támogatja az adattárolásra a legköltséghatékonyabb módon. A nagyméretű adathalmazok nagy kapacitásának és nagy teljesítményének növelésére van optimalizálva.

## <a name="blob-lifecycle-management"></a>BLOB-életciklus kezelése

A Blob Storage életciklus-kezelés egy részletes, szabályon alapuló szabályzatot kínál:

- Prémium – az életciklus végén lejáró adatmennyiség
- Standard – a legjobb hozzáférési szint felé irányuló adatváltási és az életciklus végén lejáró adatmennyiség

További információ: [Az Azure Blob Storage életciklusának kezelése](storage-lifecycle-management-concepts.md).

A prémium szintű blokk blob Storage-fiókban tárolt adatok nem helyezhetők át a gyors, a lassú elérésű és az archív rétegek között. Egy blokk blob Storage-fiókból is másolhat blobokat egy *másik* fiókban lévő gyors elérési szinthez. Az adatok egy másik fiókba való másolásához használja a [put blokkot az URL](/rest/api/storageservices/put-block-from-url) API vagy a [AzCopy v10](../common/storage-use-azcopy-v10.md) használatával. A **put blokk from URL** API szinkron módon másolja az adatok a kiszolgálón. A hívás csak azt követően fejeződik be, hogy az összes adatok át lettek helyezve az eredeti kiszolgáló helyéről a célhelyre.

## <a name="next-steps"></a>További lépések

A GPv2-és blob Storage-fiókok gyors, ritka és archív verzióinak kiértékelése

- [A gyakori és ritka elérésű, valamint az archív tárolási szint rendelkezésre állásának ellenőrzése régiónként](https://azure.microsoft.com/regions/#services)
- [Azure Blob Storage-életciklus felügyelete](storage-lifecycle-management-concepts.md)
- [Tudnivalók a blob-adatok archiválási szintről való kiszárításáról](storage-blob-rehydration.md)
- [Aktuális tárfiókjai használatának értékelése az Azure Storage mérőszámainak engedélyezésével](../common/storage-enable-and-view-metrics.md)
- [A gyakori és ritka elérésű, valamint az archív tárolási szint díjszabásának régiók szerinti ellenőrzése Blob Storage- és GPv2-fiókok esetében](https://azure.microsoft.com/pricing/details/storage/)
- [Az adatátviteli díjszabás megtekintése](https://azure.microsoft.com/pricing/details/data-transfers/)
