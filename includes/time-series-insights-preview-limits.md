---
title: fájlbefoglalás
description: fájlbefoglalás
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 02/07/2020
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.custom: include file
ms.openlocfilehash: 7bc6938523a6d66a2bc20b37c659568fc5ca494d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "77123153"
---
### <a name="general-availability-and-preview-comparison"></a>Általános elérhetőség és előzetes verzió összehasonlítása

Az alábbi táblázat a Azure Time Series Insights általánosan elérhető (GA) és az előzetes verziójú példányok közötti főbb különbségeket foglalja össze.

| | FE | Előnézet |
| --- | --- | ---|
| Első osztályú polgár | Esemény-központú | Idősorozat-központú |
| Szemantikai indoklás | Alacsony szintű (hivatkozási adatértékek) | Magas szintű (modellek) |
| Az adatcontextualization | Nem eszköz szint | Eszköz és nem eszköz szintje |
| Számítási logikai tároló | No | Típus változói a modell részeként tárolva |
| Tárolás és hozzáférés-vezérlés | No | Modellen keresztül engedélyezve |
| Összesítések/mintavételezés | No | Az esemény súlyozása és az idő súlyozása |
| Jel újraépítése | No | Interpolációs |
| Származtatott idősorozatok előállítása | No | Igen, egyesítések és illesztések |
| Nyelvi rugalmasság | Nem komponálható | Algyűjteményeinek összefüggő |
| Kifejezés nyelve | Predikátum karakterlánca | Idősorozat-kifejezések (predikátum-karakterláncok, értékek, kifejezések és függvények) |

### <a name="property-limits"></a>Tulajdonságok korlátai

Time Series Insights a tulajdonságok korlátai 1 000-re növekedtek a GA-ban lévő maximális 800. A megadott esemény tulajdonságai a megfelelő JSON-, CSV-és diagram-oszlopokkal rendelkeznek, amelyeket megtekinthet a [Time Series Insights Preview Explorerben](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart).

| Termékváltozat | Maximális tulajdonságok |
| --- | --- |
| Előzetes verzió TB | 1 000 tulajdonságok (oszlopok) |
| GA S1 | 600 tulajdonságok (oszlopok) |
| GA S2 | 800 tulajdonságok (oszlopok) |

### <a name="event-sources"></a>Eseményforrások

A példányok száma legfeljebb két eseményforrás esetén támogatott. 

* Ismerje meg, hogyan [adhat hozzá egy Event hub-forrást](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).
* [IoT hub-forrás](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub)konfigurálása.

Alapértelmezés szerint az [előzetes verziójú környezetek](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-storage-ingress#ingress-scale-and-preview-limitations) legfeljebb **1 megabájt/másodperc (MB/s)** sebességű bejövő forgalmat támogatnak. Ha szükséges, az ügyfelek akár **16 MB/s** sebességig méretezhetik az előzetes verziójú környezeteket. A partíciós korlát **0,5 MB/s**. 

### <a name="api-limits"></a>API-korlátok

A Time Series Insights előzetes verziójának REST API korlátai a [REST API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#limits)dokumentációjában vannak megadva.
