---
title: Összegyűjtheti és elemezheti a Windows-eseménynaplók az Azure Monitor |} A Microsoft Docs
description: Ismerteti, hogyan konfigurálhatja a gyűjtemény Azure figyelő Windows Eseménynapló és hozhatnak létre a rekordok részleteit.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: ee52f564-995b-450f-a6ba-0d7b1dac3f32
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: bwren
ms.openlocfilehash: 1f55e03d9a925bf939d627f376d29edf27461e74
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "56001119"
---
# <a name="windows-event-log-data-sources-in-azure-monitor"></a>Windows Eseménynapló-adatforrások az Azure monitorban
Windows-eseménynaplók közé tartoznak a leggyakoribb [adatforrások](agent-data-sources.md) Windows-ügynökök használata, mivel számos alkalmazásban a Windows eseménynaplójába írhatja az adatok gyűjtéséhez.  Események, például a rendszer- és standard szintű naplók is összegyűjtheti a bármely kell figyelnie alkalmazások által létrehozott egyéni naplók megadása mellett.

![Windows-események](media/data-sources-windows-events/overview.png)     

## <a name="configuring-windows-event-logs"></a>Windows eseménynaplók konfigurálása
A Windows eseménynaplók konfigurálása a [adatok menü a Speciális beállítások](agent-data-sources.md#configuring-data-sources).

Az Azure Monitor csak a Windows Eseménynapló beállításaiban megadott eseményeket gyűjti.  Írja be a napló nevét, majd kattintson az Eseménynapló is hozzáadhat **+**.  Az egyes naplókhoz csak a kiválasztott súlyossági szint esetén csak az események összegyűjtése.  Ellenőrizze a súlyossági szint esetén csak az adott napló, amely szeretne gyűjteni.  További feltételeket az események szűrése nem tud biztosítani.

Írja be az Eseménynapló nevét, az Azure Monitor közös Eseménynapló nevének javaslatokat biztosít. Ha a hozzáadni kívánt napló nem jelenik meg a listában, továbbra is hozzáadhat, írja be a naplófájl teljes neve. Az Eseménynapló használatával is megtalálhatja a naplófájl teljes neve. Nyissa meg az eseménynaplóban a *tulajdonságok* a napló lapjáról, és másolja a karakterláncot, a *teljes fájlvisszaállítási név* mező.

![Windows-események konfigurálása](media/data-sources-windows-events/configure.png)

## <a name="data-collection"></a>Adatgyűjtés
Az Azure Monitor gyűjt minden eseményt, amely megfelel a kiválasztott súlyossági egy figyelt az eseménynaplóból, az esemény jön létre.  Az ügynök a helyére az összegyűjtő minden eseménynaplóban rögzíti.  Ha az ügynököt egy ideig offline állapotba kerül, majd azt eseményeit gyűjti, ahol utolsó abbamaradtak, akkor is, ha az események jöttek létre, miközben az ügynök offline állapotban volt.  Lehetőség van, nem lesznek összegyűjtve, ha az ügynök nélküli állapotában felülírásra elveszne eseményekkel rendelkező burkolja az Eseménynapló ezeket az eseményeket.

>[!NOTE]
>Az Azure Monitor nem gyűjt be a forrás SQL-kiszolgáló által létrehozott események naplózása *MSSQLSERVER* , amely tartalmazza a kulcsszavak - azonosítójú 18453 *klasszikus* vagy *naplózási sikeres* és kulcsszó *0xa0000000000000*.
>

## <a name="windows-event-records-properties"></a>Windows-esemény rekordok tulajdonságai
Windows eseményrekordok rendelkezik olyan típusú **esemény** , és a tulajdonságok a következő táblázatban:

| Tulajdonság | Leírás |
|:--- |:--- |
| Computer |Az esemény gyűjtötte a program a számítógép nevét. |
| EventCategory |Az esemény kategóriája. |
| EventData |Összes esemény adatai fájlt nyers formátumban. |
| EventID |Az esemény számát. |
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

## <a name="log-queries-with-windows-events"></a>Windows-események log lekérdezéseket.
Az alábbi táblázat példákat különböző Windows-esemény lekérő log lekérdezéseket.

| Lekérdezés | Leírás |
|:---|:---|
| Esemény |Minden Windows-eseményt. |
| Esemény &#124; ahol Error == "error" |Minden Windows események hiba súlyosságát. |
| Esemény &#124; forrás count() összegzése |Események száma a Windows forrás. |
| Esemény &#124; ahol Error == "error" &#124; forrás count() összegzése |Hiba történt események száma a Windows forrás. |


## <a name="next-steps"></a>További lépések
* Konfigurálja a gyűjtendő más Log Analytics [adatforrások](agent-data-sources.md) elemzés céljából.
* Ismerje meg [lekérdezések naplózását](../log-query/log-query-overview.md) az adatforrások és megoldások gyűjtött adatok elemzéséhez.  
* Konfigurálása [teljesítményszámláló-gyűjtemény](data-sources-performance-counters.md) a Windows-ügynököktől.