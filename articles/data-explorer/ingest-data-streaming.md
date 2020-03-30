---
title: Az Azure Data Explorer betöltéséhez használja a streamelést az adatok betöltéséhez
description: Ismerje meg, hogyan töltheti be (töltheti be) az adatokat az Azure Data Explorerbe a streamelési betöltés használatával.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: d7d2bcf487c37fbb523b648d5aa4c572add5dfa9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297084"
---
# <a name="streaming-ingestion-preview"></a>Streamelés (előzetes verzió)

Akkor használja a streamelést, ha alacsony késésre van szüksége, és a különböző kötetadatok hoz 10 másodpercnél rövidebb betöltési időre van szüksége. Számos tábla működési feldolgozásának optimalizálására szolgál egy vagy több adatbázisban, ahol az adatfolyam az egyes táblákba viszonylag kicsi (kevés rekord másodpercenként), de az általános adatbetöltési kötet magas (több ezer rekord másodpercenként). 

Tömeges betöltést használjon a streamelés helyett, ha az adatok mennyisége 1 MB/másodperc/másodpercre nő. Olvassa el [az Adatok betöltési áttekintést,](/azure/data-explorer/ingest-data-overview) ha többet szeretne megtudni a különböző betöltési módszerekről.

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* Jelentkezzen be a [Webes felhasználói felületre](https://dataexplorer.azure.com/).
* [Azure Data Explorer-fürt és-adatbázis](create-cluster-database-portal.md) létrehozása

## <a name="enable-streaming-ingestion-on-your-cluster"></a>Streamelés engedélyezése a fürtön

> [!WARNING]
> Kérjük, olvassa el a [korlátozásokat,](#limitations) mielőtt lehetővé teszi a gőzölgő lenyelést.

1. Az Azure Portalon nyissa meg az Azure Data Explorer-fürt. A **Beállítások csoportban**válassza a **Konfigurációk**lehetőséget. 
1. A **Konfigurációk** ablaktáblán válassza a **Be** lehetőséget a **streamelés engedélyezéséhez.**
1. Kattintson a **Mentés** gombra.
 
    ![streamelés a](media/ingest-data-streaming/streaming-ingestion-on.png)
 
1. A [webes felhasználói felületen](https://dataexplorer.azure.com/)definiálja a [streamelési házirendet](/azure/kusto/management/streamingingestionpolicy) a tábla(ok)on vagy adatbázis(ok)on, amelyek streamelési adatokat fogadnak. 

    > [!NOTE]
    > * Ha a házirend adatbázis szinten van definiálva, az adatbázis összes táblája engedélyezve van a streameléshez.
    > * Az alkalmazott házirend csak az újonnan betöltött adatokra hivatkozhat, az adatbázis más tábláira nem.

## <a name="use-streaming-ingestion-to-ingest-data-to-your-cluster"></a>A streamelés használata adatok fürtbe történő betöltéséhez

Két támogatott streamelési típusú:


* [**Az adatforrásként**](/azure/data-explorer/ingest-data-event-hub)használt Event Hub
* **Egyéni betöltési** megköveteli, hogy írjon egy alkalmazást, amely az Azure Data Explorer ügyfélkódtárak egyikét használja. Tekintse meg a [streamelési minta](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/client/StreamingIngestionSample) egy mintaalkalmazás.

### <a name="choose-the-appropriate-streaming-ingestion-type"></a>Válassza ki a megfelelő streamelési betöltési típust

|   |Eseményközpont  |Egyéni betöltés  |
|---------|---------|---------|
|Adatkésleltetés a betöltés kezdeményezése és a lekérdezéshez rendelkezésre álló adatok között   |    Hosszabb késleltetés     |   Rövidebb késleltetés      |
|Fejlesztési többletterhelés    |   Gyors és egyszerű beállítás, nincs fejlesztési többletterhelés    |   Nagy fejlesztési többletterhelés az alkalmazások számára a hibák kezeléséhez és az adatok konzisztenciájának biztosításához     |

## <a name="disable-streaming-ingestion-on-your-cluster"></a>A streamelés letiltása a fürtön

> [!WARNING]
> A streamelés letiltása eltarthat néhány óráig.

1. A [streamelési házirend](/azure/kusto/management/streamingingestionpolicy) et az összes vonatkozó táblából és adatbázisból eldobhatom. A streamelési házirend eltávolítása elindítja a streamelési adatok áthelyezése a kezdeti tárolóból az oszloptárolóállandó tárolóba (terjedelem vagy szegmensek). Az adatáthelyezés néhány másodperc és néhány óra között tarthat, a kezdeti tárolóban lévő adatok mennyiségétől, valamint a processzor és a memória fürt általi felhasználási módjától függően.
1. Az Azure Portalon nyissa meg az Azure Data Explorer-fürt. A **Beállítások csoportban**válassza a **Konfigurációk**lehetőséget. 
1. A **Konfigurációk** ablaktáblán válassza a **Ki** lehetőséget a **Streamelés letiltásához.**
1. Kattintson a **Mentés** gombra.

    ![Lenyelés streamelése](media/ingest-data-streaming/streaming-ingestion-off.png)

## <a name="limitations"></a>Korlátozások

* A streamelés nem támogatja [az adatbázis-kurzorokat](/azure/kusto/management/databasecursor) vagy [az adatleképezést.](/azure/kusto/management/mappings) Csak [az előre létrehozott](/azure/kusto/management/create-ingestion-mapping-command) adatleképezés támogatott. 
* Streamelési teljesítmény- és kapacitásskálák megnövekedett virtuális gép- és fürtmérettel. Az egyidejű lenyelések magonként hat lenyelésre korlátozódnak. Például 16 magos su-k, például a D14 és L16 esetében a maximális támogatott terhelés 96 egyidejű betöltés. Két magos sk-ok, például a D11 esetében a maximális támogatott terhelés 12 egyidejű betöltés.
* Az adatméret korlátozása betöltési kérelemenként 4 MB.
* Séma frissítések, például a táblák létrehozása és módosítása és a letöltési leképezések, akár öt percet is igénybe vehet a streamelési betöltési szolgáltatás.
* A fürt öntélő betöltésének engedélyezése, még akkor is, ha az adatok streamelés közben nem történikk betöltéssel, a fürtgépek helyi SSD-lemezének egy részét használja a betöltési adatok streameléséhez, és csökkenti a gyorselérésű gyorsítótár számára rendelkezésre álló tárolót.
* [A terjedelmények nem](/azure/kusto/management/extents-overview#extent-tagging) állíthatók be a streamelési betöltési adatokon.

## <a name="next-steps"></a>További lépések

* [Adatok lekérdezése az Azure Data Explorerben](web-query-data.md)
