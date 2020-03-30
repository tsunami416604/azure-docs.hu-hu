---
title: Mi az az Azure Data Explorer?
description: Az Azure Adatkezelő egy gyors és hatékonyan skálázható adatáttekintési szolgáltatás napló- és telemetriaadatokhoz.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: overview
ms.date: 09/24/2018
ms.openlocfilehash: 02b239922f481240341225ea68d8d0ee7492c48b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "60445803"
---
# <a name="what-is-azure-data-explorer"></a>Mi az az Azure Data Explorer?

Az Azure Adatkezelő egy gyors és hatékonyan skálázható adatáttekintési szolgáltatás napló- és telemetriaadatokhoz. Segít kezelni a modern szoftverek által kibocsátott számos adatfolyamot, hogy begyűjthesse, tárolhassa és elemezhesse az adatokat. Az Azure Adatkezelő ideális választás nagy mennyiségű, különböző forrásokból (például webhelyekről, alkalmazásokból, IoT-eszközökről stb.) származó adatok elemzéséhez. Ezek az adatok a diagnosztikához, a monitorozáshoz, a jelentéskészítéshez, a gépi tanuláshoz és a további elemzési funkciókhoz szükségesek. Az Azure Data Explorer egyszerűvé teszi az adatok betöltését, és lehetővé teszi, hogy összetett ad hoc lekérdezéseket hajtson végre az adatokon másodpercek alatt.

## <a name="what-makes-azure-data-explorer-unique"></a>Mitől egyedi az Azure Data Explorer?

- Percek alatt több terabájtnyi adatra skálázható, ami lehetővé teszi az adatfeltárás gyors iterációit releváns megállapítások kinyeréséhez.

- Innovatív lekérdezési nyelvet kínál, amely nagy teljesítményű adatelemzésekre van optimalizálva.

- Támogatja nagy mennyiségű heterogén (strukturált és strukturálatlan) adat elemzését.

- Lehetővé teszi teljes mértékben a felhasználó igényeire szabott, más szolgáltatásokkal kombinált megoldások készítését és üzembe helyezését, így átfogó, hatékony és interaktív adatelemzési megoldást nyújt.

## <a name="data-warehousing-workflow"></a>Az adatraktározási munkafolyamat

Az Azure Data Explorer integrálható a főbb szolgáltatásokkal, így olyan teljes körű megoldást nyújt, amely magában foglalja az adatok gyűjtését, betöltését, tárolását, lekérdezését és megjelenítését. Kulcsszerepe van az adatraktározási folyamatban, mivel végrehajtja a **FELTÁRÁSI** lépést több terabájtnyi nyers adaton.

![Adatraktár-diagram](media/data-explorer-overview/data-warehouse.png)

Az Azure Data Explorer számos betöltési módszert támogat, például az összekötőket, amelyek az Event Hubshoz hasonló gyakori szolgáltatásokra mutatnak, az SDK-kal, például a .NET-tel és a Pythonnal történő programozott betöltést, valamint a motorhoz való közvetlen hozzáférést feltárás céljából. Az Azure Data Explorer integrálható az elemzési és modellezési szolgáltatásokkal az adatok további elemzése és megjelenítése céljából.

## <a name="azure-data-explorer-flow"></a>Azure Data Explorer-folyamat

Az alábbi diagram az Azure Data Explorerrel történő munkavégzés különböző területeit mutatja be.

![Azure Data Explorer-folyamat](media/data-explorer-overview/workflow.png)

Az Azure Data Explorerben történő munkavégzés általában a következő folyamatot követi:

1. **Adatbázis létrehozása:** Hozzon létre egy *fürtöt*, majd hozzon létre egy vagy több *adatbázist* a fürtben. [Rövid útmutató: Azure Data Explorer-fürt és -adatbázis létrehozása](create-cluster-database-portal.md)

1. **Adatok betöltése:** töltsön be adatokat az adatbázistáblákba, így futtathat rajtuk lekérdezéseket. [Rövid útmutató: Adatok betöltése az Event Hubsból az Azure Data Explorerbe](ingest-data-event-hub.md)

1. **Adatbázis lekérdezése:** Webalkalmazásunk használatával futtathatja, áttekintheti és megoszthatja a lekérdezéseket és az eredményeket. A webalkalmazás elérhető az Azure Portalon és különálló alkalmazásként is. Ráadásul küldhet lekérdezéseket programozott módon (SDK használatával) vagy REST API-végpontra. [Rövid útmutató: Adatok lekérdezése az Azure Data Explorerben](web-query-data.md)

## <a name="query-experience"></a>A lekérdezések használata

A lekérdezések az Azure Data Explorerben csak olvasható kérések az adatok feldolgozására és a feldolgozás eredményeinek visszaadására az adatok vagy metaadatok módosítása nélkül. Addig pontosíthatók a lekérdezések, amíg be nem fejeződik az elemzés. Az Azure Data Explorer nagyon gyors ad hoc lekérdezési élménye miatt egyszerűvé teszi ezt a folyamatot.

Az Azure Data Explorer egyaránt jól kezel nagy mennyiségű strukturált, félig strukturált (JSON-szerű beágyazott típusú) és strukturálatlan (szabad szöveges) adatot. Lehetővé teszi adott szöveges kifejezések és adott események keresését, valamint metrikus számítások végrehajtását a strukturált adatokon. Az Azure Data Explorer a szabad formátumú szövegmezők futásidejű értékeinek kinyerésével áthidalja a strukturálatlan szövegnaplók és a strukturált számok és dimenziók világát. A gyors szövegindexelés, az oszloptár és az idősorozat-műveletek kombinálásával egyszerűbbé válik az adatfeltárás.

Az Azure Data Explorer képességeit a hatékony lekérdezési nyelvre épülő egyéb szolgáltatások is bővítik, beleértve az [Azure Monitor naplóit,](/azure/log-analytics/) [az Application Insightsot,](/azure/application-insights/)a Time [Series Insightsot](/azure/time-series-insights/)és [a Windows Defender komplex veszélyforrások elleni védelmét.](/windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection/)

## <a name="feedback"></a>Visszajelzés

Örömmel várjuk visszajelzését az Azure Data Explorerről és lekérdezési nyelvéről a következő helyeken:

- Itt teheti fel kérdéseit
  - [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-data-explorer)
  - [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Azure-Data-Explorer/bd-p/Kusto)
  - [MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureKusto)
- [Tegyen javaslatokat a User Voice-on](https://aka.ms/AzureDataExplorer.UserVoice)

## <a name="next-steps"></a>További lépések

[Rövid útmutató: Azure Data Explorer-fürt és -adatbázis létrehozása](create-cluster-database-portal.md)

[Rövid útmutató: Adatok betöltése az Event Hubsból az Azure Data Explorerbe](ingest-data-event-hub.md)

[Rövid útmutató: Adatok lekérdezése az Azure Data Explorerben](web-query-data.md)
