---
title: Mintaadatok betöltése az Azure Data Explorer
description: További tudnivalók az Azure Data Explorer (betöltés) Időjárással kapcsolatos mintaadatok betöltését.
services: data-explorer
author: mgblythe
ms.author: mblythe
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 7eb0e48a5b66775ac97ed0cab751db0ef367f667
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46964615"
---
# <a name="ingest-sample-data-into-azure-data-explorer"></a>Mintaadatok betöltése az Azure Data Explorer

Ez a cikk bemutatja, hogyan az Adatkezelőbe az Azure database-be (betöltés) mintaadatok betöltését. Nincsenek [több módot is gyűjthet adatokat](ingest-data-overview.md); Ez a cikk egy alapszintű megközelítés, amely ideális választás tesztelési célokra összpontosít.

> [!NOTE]
> Ha végrehajtotta már rendelkezik az adatok [a rövid útmutató: az Azure Data Explorer Python-kódtár használata az adatok betöltését](python-ingest-data.md).

## <a name="prerequisites"></a>Előfeltételek

[Egy teszt fürt és adatbázis](create-cluster-database-portal.md)

## <a name="ingest-data"></a>Adatok betöltése

A **StormEvents** minta adatkészlet Időjárással kapcsolatos adatokat tartalmaz a [környezeti információkat National Központjaiként](https://www.ncdc.noaa.gov/stormevents/).

1. Jelentkezzen be a [ https://dataexplorer.azure.com ](https://dataexplorer.azure.com).

1. Válassza a bal felső sarokban az alkalmazás, **hozzáadása fürthöz**.

1. Az a **hozzáadása fürthöz** párbeszédpanelen adja meg a fürt URL-cím formájában `https://<ClusterName>.<Region>.kusto.windows.net/`, majd válassza **hozzáadása**.

1. Illessze be a következő parancsot, és válassza ki **futtatása**.

    ```Kusto
    .create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)

    .ingest into table StormEvents h'https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (ignoreFirstRecord=true)
    ```

1. Adatbetöltési befejezése után illessze be a következő lekérdezést, válassza ki a lekérdezési ablakban, és válassza ki **futtatása**.

    ```Kusto
    StormEvents
    | sort by StartTime desc
    | take 10
    ```
    A lekérdezés a betöltött mintaadatok az alábbi eredményeket adja vissza.

    ![Lekérdezés eredményei](media/ingest-sample-data/query-results.png)

## <a name="next-steps"></a>További lépések

[A lekérdezésírásra összpontosíthat](write-queries.md)

[Az Azure Data Explorer adatbetöltés](ingest-data-overview.md)