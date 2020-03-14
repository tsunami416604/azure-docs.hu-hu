---
title: Windows-eseménynaplók összegyűjtése és elemzése a Azure Monitorban | Microsoft Docs
description: Ismerteti, hogyan konfigurálható a Windows-eseménynaplók gyűjteménye Azure Monitor és a létrehozott rekordok részleteivel.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: aa34196233ce4037ef6fa49b782b9aa958f7632d
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79274683"
---
# <a name="windows-event-log-data-sources-in-azure-monitor"></a>Windows Eseménynapló-adatforrások a Azure Monitor-ban
A Windows-eseménynaplók a leggyakoribb [adatforrások](agent-data-sources.md) , amelyek a Windows-ügynökökkel való adatgyűjtéshez használják, mivel számos alkalmazás ír a Windows-eseménynaplóba.  Az eseményeket összegyűjtheti a szabványos naplókból, például a rendszerből és az alkalmazásból is, és megadhatja a figyelni kívánt alkalmazások által létrehozott egyéni naplókat.

![Windows-események](media/data-sources-windows-events/overview.png)     

## <a name="configuring-windows-event-logs"></a>Windows-eseménynaplók konfigurálása
Konfigurálja a Windows-eseménynaplókat a [Speciális beállítások adatok menüjéből](agent-data-sources.md#configuring-data-sources).

Azure Monitor csak a beállításokban megadott Windows-eseménynaplókban lévő eseményeket gyűjti.  Eseménynapló hozzáadásához írja be a napló nevét, majd kattintson a **+** elemre.  Minden napló esetében csak a kijelölt megszakításokkal rendelkező események lesznek összegyűjtve.  Tekintse át a gyűjteni kívánt naplóhoz tartozó megszakításokat.  Az események szűréséhez nem adhat meg további feltételeket.

Amikor beírja az Eseménynapló nevét, Azure Monitor a gyakori Eseménynapló-nevekre vonatkozó javaslatokat is tartalmaz. Ha a hozzáadni kívánt napló nem jelenik meg a listában, akkor a napló teljes nevének beírásával továbbra is hozzáadhatja azt. A napló teljes nevét az Eseménynapló használatával találja. Az eseménynaplóban nyissa meg a napló *Tulajdonságok* lapját, és másolja a karakterláncot a *teljes név* mezőből.

![Windows-események konfigurálása](media/data-sources-windows-events/configure.png)

> [!NOTE]
> A Windows-Eseménynapló kritikus eseményeinek súlyossága "Error" lesz a Azure Monitor naplókban.

## <a name="data-collection"></a>Adatgyűjtés
Azure Monitor gyűjt minden olyan eseményt, amely megfelel egy figyelt eseménynaplóból származó kiválasztott súlyosságnak, mivel az esemény létrejön.  Az ügynök rögzíti a helyét minden olyan eseménynaplóban, amelyről gyűjt.  Ha az ügynök egy ideig offline állapotba kerül, akkor akkor is gyűjti az eseményeket, amikor az ügynök offline állapotba került, még akkor is, ha ezek az események létre lettek hozva.  Ezek az események nem gyűjthetők össze, ha az Eseménynapló az ügynök offline állapotában felülírt, nem gyűjtött eseményekkel van becsomagolva.

>[!NOTE]
>A Azure Monitor nem gyűjti az SQL Server által létrehozott naplózási *eseményeket a 18453* -as azonosítójú azonosítójú eseménnyel, amely kulcsszavakat tartalmaz – a *klasszikus* vagy a *naplózási sikerességet* és a kulcsszavak *0xa0000000000000*.
>

## <a name="windows-event-records-properties"></a>Windows-események rekordjainak tulajdonságai
A Windows-események rekordjainak típusa **esemény** , és a tulajdonságok a következő táblázatban láthatók:

| Tulajdonság | Leírás |
|:--- |:--- |
| Computer |Annak a számítógépnek a neve, amelyre az eseményt gyűjtötték. |
| EventCategory |Az esemény kategóriája. |
| EventData |Az összes eseményvezérelt érték nyers formátumban. |
| EventID |Az esemény száma. |
| EventLevel |Az esemény súlyossága numerikus formában. |
| EventLevelName |Az esemény súlyossága szöveges formában. |
| EventLog |Annak az Eseménynaplónak a neve, amelyből az eseményt gyűjtötték. |
| ParameterXml |Az esemény paramétereinek értékei XML formátumban. |
| ManagementGroupName |System Center Operations Manager ügynökök felügyeleti csoportjának neve.  Más ügynökök esetében ez az érték `AOI-<workspace ID>` |
| RenderedDescription |Esemény leírása paraméter-értékekkel |
| Forrás |Az esemény forrása. |
| SourceSystem |Az a típusú ügynök, amelyből az eseményt gyűjtötték. <br> OpsManager – Windows-ügynök, közvetlen kapcsolat vagy Operations Manager felügyelt <br> Linux – minden Linux-ügynök  <br> AzureStorage – Azure Diagnostics |
| TimeGenerated |Az esemény Windowsban történő létrehozásának dátuma és időpontja. |
| UserName |Az eseményt naplózó fiók felhasználóneve. |

## <a name="log-queries-with-windows-events"></a>Lekérdezések naplózása Windows-eseményekkel
Az alábbi táblázat a Windows-események rekordjait lekérő lekérdezések különböző példáit ismerteti.

| Lekérdezés | Leírás |
|:---|:---|
| Esemény |Minden Windows-esemény. |
| Esemény &#124; , ahol EventLevelName = = "hiba" |Minden Windows-esemény, amelynek súlyossága a hiba. |
| Esemény &#124; összegzésének száma () forrás szerint |Windows-események száma forrás szerint. |
| Esemény &#124; , ahol a EventLevelName = = " &#124; Error" összefoglalók száma () forrás szerint |Windows-hibák száma forrás szerint. |


## <a name="next-steps"></a>További lépések
* A Log Analytics konfigurálásával más [adatforrásokat](agent-data-sources.md) is gyűjthet az elemzéshez.
* További információ az adatforrásokból és megoldásokból gyűjtött adatok elemzéséhez szükséges [naplók lekérdezéséről](../log-query/log-query-overview.md) .  
* [Teljesítményszámlálók gyűjteményének](data-sources-performance-counters.md) konfigurálása a Windows-ügynökökből.
