---
title: Windows-eseménynaplók gyűjtése és elemzése az Azure Monitorban | Microsoft dokumentumok
description: Bemutatja, hogyan konfigurálhatja a Windows eseménynaplók gyűjtését az Azure Monitor és az általuk létrehozott rekordok részleteit.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: aa34196233ce4037ef6fa49b782b9aa958f7632d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274683"
---
# <a name="windows-event-log-data-sources-in-azure-monitor"></a>Windows-eseménynapló adatforrásai az Azure Monitorban
A Windows eseménynaplók az egyik leggyakoribb [adatforrások](agent-data-sources.md) a Windows-ügynökök használatával történő adatgyűjtéshez, mivel számos alkalmazás ír a Windows eseménynaplójába.  A rendszer naplóiból ( például a Rendszer és az Alkalmazás ) származó eseményeket is gyűjthet, valamint megadhatja a figyelni kívánt alkalmazások által létrehozott egyéni naplókat.

![Windows-események](media/data-sources-windows-events/overview.png)     

## <a name="configuring-windows-event-logs"></a>A Windows eseménynaplóinak konfigurálása
A Windows eseménynaplóinak konfigurálása a [Speciális beállítások párbeszédpanel Adatok menüjéből.](agent-data-sources.md#configuring-data-sources)

Az Azure Monitor csak a windows-i eseménynaplókban megadott eseményeket gyűjti.  Eseménynapló hozzáadásához írja be a napló nevét, majd **+** kattintson a gombra.  Az egyes naplók esetében csak a kiválasztott severities eseményeket gyűjti a rendszer.  Ellenőrizze az összegyűjteni kívánt napló hegedülésére vonatkozó aneszteziológiai adatokat.  Az események szűréséhez nem adhat meg további feltételeket.

Az eseménynapló nevének beírásakor az Azure Monitor javaslatokat tesz a gyakori eseménynapló-nevekre. Ha a hozzáadni kívánt napló nem jelenik meg a listában, akkor is hozzáadhatja a napló teljes nevének beírásával. A napló teljes nevét az eseménynapló segítségével találhatja meg. Az eseménynaplóban nyissa meg a napló *Tulajdonságok* lapját, és másolja a karakterláncot a *Teljes név* mezőből.

![Windows-események konfigurálása](media/data-sources-windows-events/configure.png)

> [!NOTE]
> A Windows eseménynaplójából származó kritikus események súlyossága "Hiba" lesz az Azure Monitor naplókban.

## <a name="data-collection"></a>Adatgyűjtés
Az Azure Monitor minden olyan eseményt gyűjt, amely megfelel a figyelt eseménynapló kiválasztott súlyosságának az esemény létrehozásakor.  Az ügynök rögzíti a helyét minden egyes eseménynaplóban, amelyből gyűjt.  Ha az ügynök offline állapotba kerül egy ideig, majd összegyűjti az eseményeket onnan, ahol az utolsó abbahagyta, még akkor is, ha ezek az események akkor jöttek létre, amikor az ügynök offline állapotban volt.  Előfordulhat, hogy ezek az események nem gyűjthetők, ha az eseménynapló a be nem gyűjtött események et felülírja, amíg az ügynök offline állapotban van.

>[!NOTE]
>Az Azure Monitor nem gyűjti az SQL Server által létrehozott naplózási eseményeket az *MSSQLSERVER* forrásból, a 18453-as eseményazonosítóval, amely kulcsszavakat tartalmaz - *Klasszikus* vagy *naplózási siker* és *0xa000000000000000*00000000000 kulcsszó.
>

## <a name="windows-event-records-properties"></a>A Windows eseményrekordok tulajdonságai
A Windows eseményrekordjainak **eseménytípusa van,** és az alábbi táblázatban található tulajdonságokkal rendelkeznek:

| Tulajdonság | Leírás |
|:--- |:--- |
| Computer |Annak a számítógépnek a neve, amelyről az eseményt gyűjtötték. |
| Eseménykategória |Az esemény kategóriája. |
| EventData (Eseményadatok) |Az összes eseményadat nyers formátumban. |
| Eseményazonosító |Az esemény száma. |
| Eseményszint |Az esemény súlyossága numerikus formában. |
| EventLevelName (Eseményszintneve) |Az esemény súlyossága szöveges formában. |
| Eseménynapló |Annak az eseménynaplónak a neve, amelyből az eseményt gyűjtötték. |
| ParameterXml |Eseményparaméter-értékek XML formátumban. |
| ManagementGroupName |A System Center Operations Manager-ügynökök felügyeleti csoportjának neve.  Más ügynökök esetében ez az érték`AOI-<workspace ID>` |
| Renderelt leírás |Esemény leírása paraméterértékekkel |
| Forrás |Az esemény forrása. |
| SourceSystem |Az esemény által gyűjtött ügynök típusa. <br> OpsManager – Windows-ügynök, közvetlen csatlakozás vagy operations manager által kezelt <br> Linux – Minden Linux ügynök  <br> AzureStorage – Azure diagnosztika |
| TimeGenerated |Az esemény windowsos létrehozásának dátuma és időpontja. |
| UserName (Felhasználónév) |Az eseményt naplózó fiók felhasználóneve. |

## <a name="log-queries-with-windows-events"></a>Lekérdezések naplózása Windows-eseményekkel
Az alábbi táblázat különböző példákat tartalmaz a Windows-eseményrekordokat lekérdező naplólekérdezésekre.

| Lekérdezés | Leírás |
|:---|:---|
| Esemény |Minden Windows-esemény. |
| Esemény &#124; ahol EventLevelName == "error" |Minden Windows-esemény, amely hibasúlyosságú. |
| Az esemény &#124; összegezze a count() -t forrás szerint |A Windows-események száma forrás szerint. |
| Esemény &#124; ahol Az EventLevelName == "hiba" &#124; a count() összegzését forrás szerint |A Windows hibaeseményeinek száma forrás szerint. |


## <a name="next-steps"></a>További lépések
* A Log Analytics konfigurálásával elemzésre más [adatforrásokat](agent-data-sources.md) gyűjtsön.
* Ismerje meg a [naplólekérdezéseket](../log-query/log-query-overview.md) az adatforrásokból és megoldásokból gyűjtött adatok elemzéséhez.  
* Konfigurálja [a teljesítményszámlálók gyűjteményét](data-sources-performance-counters.md) a Windows-ügynököktől.
