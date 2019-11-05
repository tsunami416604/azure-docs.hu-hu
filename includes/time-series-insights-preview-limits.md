---
title: fájl belefoglalása
description: fájl belefoglalása
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 10/16/2019
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.custom: include file
ms.openlocfilehash: dfe16b4e965670d115cfa92f1cb3ca812d6375ad
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990857"
---
### <a name="general-availability-and-preview-comparison"></a>Általános elérhetőség és előzetes verzió összehasonlítása

Az alábbi táblázat a Azure Time Series Insights általánosan elérhető (GA) és az előzetes verziójú példányok közötti főbb különbségeket foglalja össze.

| | FE | Előzetes verzió |
| --- | --- | ---|
| Első osztályú polgár | Esemény-központú | Idősorozat-központú |
| Szemantikai indoklás | Alacsony szintű (hivatkozási adatértékek) | Magas szintű (modellek) |
| Az adatcontextualization | Nem eszköz szint | Eszköz és nem eszköz szintje |
| Számítási logikai tároló | Nem | Típus változói a modell részeként tárolva |
| Tárolás és hozzáférés-vezérlés | Nem | Modellen keresztül engedélyezve |
| Összesítések/mintavételezés | Nem | Az esemény súlyozása és az idő súlyozása |
| Jel újraépítése | Nem | Interpolációs |
| Származtatott idősorozatok előállítása | Nem | Igen, egyesítések és illesztések |
| Nyelvi rugalmasság | Nem komponálható | Algyűjteményeinek összefüggő |
| Kifejezés nyelve | Predikátum karakterlánca | Idősorozat-kifejezések (predikátum-karakterláncok, értékek, kifejezések és függvények) |

### <a name="property-limits"></a>Tulajdonságok korlátai

Time Series Insights a tulajdonságok korlátai 1 000-re növekedtek a GA-ban lévő maximális 800. A megadott esemény tulajdonságai a megfelelő JSON-, CSV-és diagram-oszlopokkal rendelkeznek, amelyeket megtekinthet a [Time Series Insights Preview Explorerben](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart).

| SKU | Maximális tulajdonságok |
| --- | --- |
| Előzetes verzió TB | 1 000 tulajdonságok (oszlopok) |
| GA S1 | 600 tulajdonságok (oszlopok) |
| GA S2 | 800 tulajdonságok (oszlopok) |

### <a name="event-sources"></a>Eseményforrások

A példányok száma legfeljebb két eseményforrás esetén támogatott. 

* Ismerje meg, hogyan [adhat hozzá egy Event hub-forrást](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).
* [IoT hub-forrás](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub)konfigurálása.

### <a name="api-limits"></a>API-korlátok

A Time Series Insights előzetes verziójának REST API korlátai a [REST API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#limits)dokumentációjában vannak megadva.