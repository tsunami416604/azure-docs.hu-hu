---
title: Összegyűjti és elemzi a Windows eseménynaplóiban keresse meg az Azure Naplóelemzés |} Microsoft Docs
description: Windows-eseménynaplók a Naplóelemzési által használt leggyakrabban használt adatforrások közül.  Ez a cikk ismerteti a Windows-eseménynaplók gyűjteménye és a részletek a rekordok hoznak létre a Naplóelemzési munkaterület konfigurálása.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: ee52f564-995b-450f-a6ba-0d7b1dac3f32
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/11/2017
ms.author: bwren
ms.openlocfilehash: 7a7deb4d7a287b2e9613e6035a7ffd7bb6f14f9c
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/20/2017
ms.locfileid: "26782030"
---
# <a name="windows-event-log-data-sources-in-log-analytics"></a>A Windows Eseménynapló adatforrások Naplóelemzési
Windows-eseménynaplók olyan egyik leggyakoribb [adatforrások](log-analytics-data-sources.md) adatgyűjtéshez Windows ügynököt használ, mivel számos alkalmazás a Windows eseménynaplójába írhatja.  Események például a rendszer- és standard naplókból mellett adja meg a figyelni kívánt alkalmazások által létrehozott egyéni naplók összegyűjtésére.

![Windows-események](media/log-analytics-data-sources-windows-events/overview.png)     

## <a name="configuring-windows-event-logs"></a>Konfigurálása Windows-eseményt naplóz
Windows-eseménynaplók az konfigurálása a [Naplóelemzés beállításai adatok menüben](log-analytics-data-sources.md#configuring-data-sources).

A Naplóelemzési csak a Windows Eseménynapló a beállításokban megadott eseményeket gyűjti.  Írja be a napló nevére, majd kattintson az Eseménynapló adhat hozzá  **+** .  Az egyes naplókon csak a kijelölt súlyosságokkal események összegyűjtése.  Ellenőrizze a gyűjteni kívánt adott napló fokozatok.  Nem adhat meg semmilyen további feltételt az események szűrése.

Az Eseménynapló nevét írja be, Log Analyticshez közös eseménynaplók neve vonatkozó javaslatokat biztosít. Ha hozzá szeretne adni a napló nem jelenik meg a listában, továbbra is hozzáadhatja, írja be a naplófájl teljes neve. A naplófájl teljes neve található az Eseménynapló használatával. Az eseménynaplóban, nyissa meg a *tulajdonságok* a napló lapjáról, és másolja a karakterláncot, a *teljes nevét* mező.

![Windows-események konfigurálása](media/log-analytics-data-sources-windows-events/configure.png)

## <a name="data-collection"></a>Adatgyűjtés
A Naplóelemzési gyűjt minden esemény, amely megfelel a kiválasztott súlyossága a figyelt eseménynaplóban, az esemény jön létre.  Az ügynök a helyére azt gyűjti össze az egyes eseménynapló rögzíti.  Ha az ügynököt az adott időszakban offline állapotba kerül, majd Naplóelemzési gyűjti össze események ahol utolsó abbamaradtak, még akkor is, ha olyan eseményeket hozta létre, miközben az ügynök offline állapotban volt.  Nincs a lehetséges, hogy ezek az események nem gyűjthetők össze, ha az eseménynaplóban becsomagolja elveszne események felül, amikor az ügynök offline állapotban.

>[!NOTE]
>A Naplóelemzési nem gyűjt forrás SQL-kiszolgáló által létrehozott események naplózása *MSSQLSERVER* azonosítójú esemény 18453 kulcsszavak - tartalmazó *klasszikus* vagy *naplózási sikeres* és kulcsszó *0xa0000000000000*.
>

## <a name="windows-event-records-properties"></a>A Windows esemény-rekordok tulajdonságai
Windows eseményrekordok típusa lehet **esemény** és az alábbi táblázatban a jellemzőkkel rendelkezik:

| Tulajdonság | Leírás |
|:--- |:--- |
| Computer |Az esemény összegyűjtött a számítógép nevét. |
| EventCategory |Az esemény kategóriáját. |
| EventData |Összes esemény adatai nyers formátumban. |
| Eseményazonosító |Az esemény számát. |
| EventLevel |A numerikus formában esemény súlyossága. |
| EventLevelName |A szöveges formában esemény súlyossága. |
| Az eseménynaplóban talál |Az esemény az összegyűjtött Eseménynapló neve. |
| ParameterXml |Esemény paraméterértékek XML formátumban. |
| ManagementGroupName |A System Center Operations Manager-ügynököket a felügyeleti csoport neve.  Más ügynökök ezt az értéket a rendszer AOI-<workspace ID> |
| RenderedDescription |Esemény – leírás paraméterértékekkel |
| Forrás |Az esemény forrását. |
| SourceSystem |Az esemény gyűjtötte a program az ügynök típusa. <br> OpsManager – Windows-ügynök, vagy közvetlenül kapcsolódjon, vagy az Operations Manager által felügyelt <br> Linux – az összes Linux-ügynökök  <br> AzureStorage – az Azure Diagnostics |
| TimeGenerated |Dátum és idő, az esemény Windows jött létre. |
| Felhasználónév |Az esemény naplózójának a fiók felhasználónevét. |

## <a name="log-searches-with-windows-events"></a>Napló megkeresi a Windows-események
Az alábbi táblázat példákat különböző Windows-esemény lehívása napló kereséseket.

| Lekérdezés | Leírás |
|:---|:---|
| Esemény |Minden Windows-eseményeket. |
| Esemény &#124; Ha EventLevelName == "error" |Minden Windows-hiba az eseményeket. |
| Esemény &#124; forrás count() összefoglalója |Forrás száma a Windows-eseményeket. |
| Esemény &#124; Ha EventLevelName == "error" &#124; forrás count() összefoglalója |Hiba események száma a Windows forrás. |


## <a name="next-steps"></a>További lépések
* Naplóelemzési más gyűjtéséhez konfigurálja [adatforrások](log-analytics-data-sources.md) elemzés céljából.
* További tudnivalók [keresések jelentkezzen](log-analytics-log-searches.md) az adatforrások és a megoldások gyűjtött adatok elemzésére.  
* Használjon [egyéni mezők](log-analytics-custom-fields.md) elemzése a eseményrekordok egyes mezőkbe.
* Konfigurálása [teljesítményszámlálók gyűjteményét](log-analytics-data-sources-performance-counters.md) a Windows-ügynökök.
