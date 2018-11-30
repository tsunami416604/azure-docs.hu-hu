---
title: Összegyűjtheti és elemezheti a Windows-eseménynaplók az Azure Log Analyticsben |} A Microsoft Docs
description: Windows-eseménynaplók a Log Analytics által használt leggyakrabban használt adatforrások tartoznak.  Ez a cikk ismerteti a Windows-eseménynaplók gyűjteményét és a Log Analytics-munkaterületen hoznak létre a rekordok részleteit konfigurálása.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: ee52f564-995b-450f-a6ba-0d7b1dac3f32
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/11/2017
ms.author: bwren
ms.component: ''
ms.openlocfilehash: 036378c5ed595ffbaeefad9407fba8519945cf5c
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52337003"
---
# <a name="windows-event-log-data-sources-in-log-analytics"></a>Windows Eseménynapló-adatforrások a Log Analyticsben
Windows-eseménynaplók közé tartoznak a leggyakoribb [adatforrások](agent-data-sources.md) Windows-ügynökök használata, mivel számos alkalmazásban a Windows eseménynaplójába írhatja az adatok gyűjtéséhez.  Események, például a rendszer- és standard szintű naplók is összegyűjtheti a bármely kell figyelnie alkalmazások által létrehozott egyéni naplók megadása mellett.

![Windows-események](media/data-sources-windows-events/overview.png)     

## <a name="configuring-windows-event-logs"></a>Windows eseménynaplók konfigurálása
A Windows eseménynaplók konfigurálása a [adatok Log Analytics-beállítások menüben](agent-data-sources.md#configuring-data-sources).

A log Analytics csak az a beállítások a megadott Windows-eseménynapló eseményeit gyűjti.  Írja be a napló nevét, majd kattintson az Eseménynapló is hozzáadhat **+**.  Az egyes naplókhoz csak a kiválasztott súlyossági szint esetén csak az események összegyűjtése.  Ellenőrizze a súlyossági szint esetén csak az adott napló, amely szeretne gyűjteni.  További feltételeket az események szűrése nem tud biztosítani.

Az Eseménynapló neve beírása a Log Analytics Eseménynapló köznapi nevek vonatkozó javaslatokat biztosít. Ha a hozzáadni kívánt napló nem jelenik meg a listában, továbbra is hozzáadhat, írja be a naplófájl teljes neve. Az Eseménynapló használatával is megtalálhatja a naplófájl teljes neve. Nyissa meg az eseménynaplóban a *tulajdonságok* a napló lapjáról, és másolja a karakterláncot, a *teljes fájlvisszaállítási név* mező.

![Windows-események konfigurálása](media/data-sources-windows-events/configure.png)

## <a name="data-collection"></a>Adatgyűjtés
A log Analytics gyűjt minden eseményt az esemény létrehozása egy kiválasztott súlyossága a figyelt eseménynaplóban a megfelelő.  Az ügynök a helyére az összegyűjtő minden eseménynaplóban rögzíti.  Ha az ügynököt egy ideig offline állapotba kerül, majd a Log Analytics gyűjti össze események ahol utolsó abbamaradtak, akkor is, ha az események jöttek létre, miközben az ügynök offline állapotban volt.  Lehetőség van, nem lesznek összegyűjtve, ha az ügynök nélküli állapotában felülírásra elveszne eseményekkel rendelkező burkolja az Eseménynapló ezeket az eseményeket.

>[!NOTE]
>A log Analytics nem gyűjt be a forrás SQL-kiszolgáló által létrehozott események naplózása *MSSQLSERVER* , amely tartalmazza a kulcsszavak - azonosítójú 18453 *klasszikus* vagy *naplózási sikeres* és kulcsszó *0xa0000000000000*.
>

## <a name="windows-event-records-properties"></a>Windows-esemény rekordok tulajdonságai
Windows eseményrekordok rendelkezik olyan típusú **esemény** , és a tulajdonságok a következő táblázatban:

| Tulajdonság | Leírás |
|:--- |:--- |
| Computer |Az esemény gyűjtötte a program a számítógép nevét. |
| EventCategory |Az esemény kategóriája. |
| EventData |Összes esemény adatai fájlt nyers formátumban. |
| Eseményazonosító |Az esemény számát. |
| eventLevel |A numerikus formában esemény súlyosságát. |
| EventLevelName |A szöveges formátumú esemény súlyosságát. |
| Eseménynapló |Az összegyűjtött az eseményt az Eseménynapló neve. |
| ParameterXml |Esemény paraméterértékek XML formátumban. |
| ManagementGroupName |A System Center Operations Manager-ügynököket a felügyeleti csoport neve.  Más ügynökök esetén ez az érték AOI-<workspace ID> |
| RenderedDescription |A paraméterértékek esemény leírása |
| Forrás |Az esemény forrását. |
| SourceSystem |Az esemény gyűjtötte a program az ügynök típusa. <br> Csatlakozás OpsManager – Windows-ügynök, közvetlenül vagy Operations Manager által felügyelt <br> Linux – az összes Linux-ügynökök  <br> AzureStorage – az Azure Diagnostics |
| TimeGenerated |Dátum és a Windows az esemény létrehozásának ideje. |
| Felhasználónév |Az esemény naplózójának a fiók felhasználóneve. |

## <a name="log-searches-with-windows-events"></a>Windows-eseményeket tartalmazó naplókeresések
Az alábbi táblázat példákat különböző naplókeresésekkel, amelyek lekérni a Windows-esemény.

| Lekérdezés | Leírás |
|:---|:---|
| Esemény |Minden Windows-eseményt. |
| Esemény &#124; ahol Error == "error" |Minden Windows események hiba súlyosságát. |
| Esemény &#124; forrás count() összegzése |Események száma a Windows forrás. |
| Esemény &#124; ahol Error == "error" &#124; forrás count() összegzése |Hiba történt események száma a Windows forrás. |


## <a name="next-steps"></a>További lépések
* Konfigurálja a gyűjtendő más Log Analytics [adatforrások](agent-data-sources.md) elemzés céljából.
* Ismerje meg [naplókereséseket](../../log-analytics/log-analytics-queries.md) az adatforrások és megoldások gyűjtött adatok elemzéséhez.  
* Használat [egyéni mezők](../../log-analytics/log-analytics-custom-fields.md) az eseményrekordok elemezni az egyes mezőket.
* Konfigurálása [teljesítményszámláló-gyűjtemény](data-sources-performance-counters.md) a Windows-ügynököktől.
