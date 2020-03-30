---
title: Mintaadatok betöltése az Azure Data Explorerbe
description: Ismerje meg, hogyan töltheti be (töltheti be) az időjárással kapcsolatos mintaadatokat az Azure Data Explorerbe.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 08/12/2019
ms.openlocfilehash: 3ece5a9d225e48654a0a3a96c3b7b78327565841
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "74975176"
---
# <a name="quickstart-ingest-sample-data-into-azure-data-explorer"></a>Rövid útmutató: Mintaadatok betöltése az Azure Data Explorerbe

Ez a cikk bemutatja, hogyan tölthetbe be (tölthetbe be) mintaadatokat egy Azure Data Explorer-adatbázisba. Az [adatok betöltése többféleképpen is betöltésre szolgál;](ingest-data-overview.md) ez a cikk a tesztelési célokra alkalmas alapvető megközelítésre összpontosít.

> [!NOTE]
> Már rendelkezik ezekkel az adatokkal, ha az Azure Data Explorer Python-kódtár használatával töltötte be [a betöltési adatokat.](python-ingest-data.md)

## <a name="prerequisites"></a>Előfeltételek

[Egy tesztfürt és egy adatbázis](create-cluster-database-portal.md)

## <a name="ingest-data"></a>Adatok betöltése

A **StormEvents** mintaadatkészlet a [környezeti adatok nemzeti központjaiból](https://www.ncdc.noaa.gov/stormevents/) származó, időjárással kapcsolatos adatokat tartalmaz.

1. Jelentkezzen be [https://dataexplorer.azure.com](https://dataexplorer.azure.com)a ikonra.

1. A bal felső sarokban válassza a **Fürt hozzáadása** lehetőséget.

1. A **Fürt hozzáadása** párbeszédpanelen adja meg a `https://<ClusterName>.<Region>.kusto.windows.net/`fürt URL-címét az űrlapon, majd válassza a **Hozzáadás lehetőséget.**

1. Illessze be a következő parancsot, és válassza a **Futtatás** lehetőséget a StormEvents tábla létrehozásához.

    ```Kusto
    .create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)
    ```
1. Illessze be a következő parancsot, és válassza a **Futtatás lehetőséget,** hogy adatokat szeretne beadni a StormEvents táblába.

    ```Kusto
    .ingest into table StormEvents h'https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (ignoreFirstRecord=true)
    ```

1. A betöltés befejezése után illessze be a következő lekérdezésbe, jelölje ki a lekérdezést az ablakban, és válassza a **Futtatás lehetőséget.**

    ```Kusto
    StormEvents
    | sort by StartTime desc
    | take 10
    ```
    A lekérdezés a következő eredményeket adja vissza a bevitt mintaadatokból.

    ![Lekérdezés eredményei](media/ingest-sample-data/query-results.png)

## <a name="next-steps"></a>További lépések

* [Az Azure Data Explorer adatbetöltése](ingest-data-overview.md) további információ a betöltési módszerekről.
* [Rövid útmutató: Lekérdezési adatok az Azure Data Explorerben](web-query-data.md) Webes felhasználói felület.
* [Lekérdezések írása](write-queries.md) a Kusto lekérdezési nyelvével.
