---
title: Mintaadatok beolvasása az Azure Adatkezelőba
description: Ismerje meg, hogyan tölthetők be az időjárással kapcsolatos mintaadatok az Azure Adatkezelőba.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 08/12/2019
ms.openlocfilehash: c803de599f6be98512b15e927c6d15f1c7d95ff1
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515741"
---
# <a name="quickstart-ingest-sample-data-into-azure-data-explorer"></a>Gyors útmutató: Mintaadatok beolvasása az Azure Adatkezelőba

Ez a cikk bemutatja, hogyan végezheti el a mintaadatok betöltését egy Azure Adatkezelő-adatbázisba. Az [adatmennyiség](ingest-data-overview.md)többféleképpen is betölthető; Ez a cikk egy olyan alapszintű megközelítésre összpontosít, amely tesztelési célokra alkalmas.

> [!NOTE]
> Ha [Az Azure adatkezelő Python Library használatával](python-ingest-data.md)végezte el az adatfeldolgozást, akkor már rendelkezik ezekkel az adataival.

## <a name="prerequisites"></a>Előfeltételek

[Egy tesztfürt és -adatbázis](create-cluster-database-portal.md)

## <a name="ingest-data"></a>Adatok betöltése

A **StormEvents** mintaadatkészlet a [környezeti adatok nemzeti központjaiból](https://www.ncdc.noaa.gov/stormevents/) származó, időjárással kapcsolatos adatokat tartalmaz.

1. Jelentkezzen be itt: [https://dataexplorer.azure.com](https://dataexplorer.azure.com).

1. A bal felső sarokban válassza a **Fürt hozzáadása** lehetőséget.

1. A **fürt hozzáadása** párbeszédpanelen adja meg a fürt URL-címét az űrlapon `https://<ClusterName>.<Region>.kusto.windows.net/`, majd válassza a **Hozzáadás**lehetőséget.

1. Illessze be a következő parancsot, és válassza a **Futtatás**lehetőséget.

    ```Kusto
    .create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)

    .ingest into table StormEvents h'https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (ignoreFirstRecord=true)
    ```

1. A betöltés befejezése után illessze be a következő lekérdezést, válassza ki a lekérdezést az ablakban, és válassza a **Futtatás**lehetőséget.

    ```Kusto
    StormEvents
    | sort by StartTime desc
    | take 10
    ```
    A lekérdezés a betöltött mintaadatok következő eredményeit adja vissza.

    ![Lekérdezés eredményei](media/ingest-sample-data/query-results.png)

## <a name="next-steps"></a>További lépések

* Az [Azure adatkezelő](ingest-data-overview.md) a betöltési módszerekkel kapcsolatos további információért.
* [Rövid útmutató: Az Azure adatkezelő](web-query-data.md) webes felhasználói felületének lekérdezése.
* [Lekérdezések írása](write-queries.md) Kusto-lekérdezési nyelvvel.
