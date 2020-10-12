---
title: Splunk a Azure Monitor log lekérdezéshez | Microsoft Docs
description: Súgó olyan felhasználók számára, akik már ismerik a splunk a Azure Monitor log-lekérdezések megismerésében.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2018
ms.openlocfilehash: 00fdaf93553c97112c67caa66cb2246756b63c33
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "86207477"
---
# <a name="splunk-to-azure-monitor-log-query"></a>Splunk Azure Monitor naplózási lekérdezés

Ebből a cikkből megtudhatja, hogy a splunk-t ismerő felhasználók hogyan írhatják le a Kusto lekérdezési nyelvét Azure Monitor. A kettő közötti közvetlen összehasonlítások a legfontosabb különbségeket, valamint a meglévő ismeretek kihasználása melletti hasonlóságokat is magukban foglalhatnak.

## <a name="structure-and-concepts"></a>Struktúra és fogalmak

A következő táblázat összehasonlítja a splunk és az Azure Monitor-naplók közötti fogalmakat és adatstruktúrákat.

 | Fogalom  | Splunk | Azure Monitor |  Megjegyzés
 | --- | --- | --- | ---
 | Üzembe helyezési egység  | cluster |  cluster |  A Azure Monitor lehetővé teszi a fürtök tetszőleges típusú lekérdezését. A splunk nem. |
 | Adatgyorsítótárak |  vödör  |  Gyorsítótárazási és adatmegőrzési szabályzatok |  Az adatpontok és a gyorsítótárazási szint szabályozása. Ez a beállítás közvetlenül befolyásolja a lekérdezések teljesítményét és a telepítés költségeit. |
 | Az adatlogikai partíció  |  index  |  adatbázis  |  Lehetővé teszi az adatmennyiségek logikai elkülönítését. Mindkét implementáció lehetővé teszi a szakszervezetek és az ezekhez való csatlakozást a partíciók között. |
 | Strukturált esemény metaadatainak | N/A | tábla |  A splunk nem rendelkezik az esemény metaadatainak keresési nyelvén elérhető koncepcióval. Azure Monitor a naplók egy tábla fogalmával rendelkeznek, amelynek vannak oszlopai. Minden Event-példány egy sorra van leképezve. |
 | Adatrekord | esemény | sor |  Csak a terminológia módosul. |
 | Adatrekord-attribútum | mező |  oszlop |  A Azure Monitorban ez a tábla struktúrájának részeként van definiálva. A splunk minden esemény saját mezőket tartalmaz. |
 | Típusok | adattípus |  adattípus |  Azure Monitor adattípusok világosabbak, mert az oszlopokra vannak beállítva. Mindkettő képes dinamikusan dolgozni az adattípusokkal és nagyjából egyenértékű adattípusokkal, beleértve a JSON-támogatást is. |
 | Lekérdezés és keresés  | keresés | lekérdezés |  A fogalmak lényegében azonosak a Azure Monitor és a splunk között. |
 | Esemény betöltésének ideje | Rendszeridő | ingestion_time() |  A splunk-ben minden esemény az esemény indexelésének időpontjának időbélyegét kapja meg. Azure Monitorban megadhat egy ingestion_time nevű szabályzatot, amely egy olyan rendszeroszlopt tesz elérhetővé, amely a ingestion_time () függvénnyel hivatkozhat. |

## <a name="functions"></a>Functions

A következő táblázat a splunk függvényekkel egyenértékű Azure Monitor függvényeit határozza meg.

|Splunk | Azure Monitor |Megjegyzés
|---|---|---
|strcat | strcat()| 1 |
|felosztás  | split() | 1 |
|if     | IFF ()   | 1 |
|tonumber | todouble()<br>tolong()<br>toint() | 1 |
|felső<br>alacsonyabb |toupper()<br>tolower()|1 |
| csere | replace() | 1<br> Azt is vegye figyelembe, hogy míg `replace()` három paramétert vesz igénybe mindkét termékben, a paraméterek eltérőek. |
| substr | substring() | 1<br>Azt is vegye figyelembe, hogy a splunk egy-alapú indexeket használ. Azure Monitor Megjegyzés nulla alapú indexek. |
| ToLower |  tolower() | 1 |
| toupper | toupper() | 1 |
| mérkőzés | megfelel a regexnek |  (2)  |
| regex | megfelel a regexnek | A splunk-ben `regex` egy operátor. A Azure Monitorban ez egy összehasonlító operátor. |
| searchmatch | == | A splunk `searchmatch` lehetővé teszi a pontos karakterlánc keresését.
| véletlenszerű | rand()<br>rand (n) | A splunk függvény nulla és 2<sup>31</sup>– 1 közötti számot ad vissza. A Azure Monitor "egy 0,0 és 1,0 közötti számot ad vissza, vagy ha a megadott paraméter 0 és n – 1 között van.
| most | now() | 1
| relative_time | totimespan() | 1<br>A Azure Monitor relative_time (datetimeVal, offsetVal) a következő splunk egyenértékű: datetimeVal + ToTimeSpan (offsetVal).<br>Például a következő <code>search &#124; eval n=relative_time(now(), "-1d@d")</code> lesz: <code>...  &#124; extend myTime = now() - totimespan("1d")</code> .

(1) a splunk a függvényt a rendszer a `eval` kezelővel hívja meg. A Azure Monitor a vagy a részeként használatos `extend` `project` .<br>(2) a splunk a függvényt a rendszer a `eval` kezelővel hívja meg. A Azure Monitor a `where` kezelővel használható.


## <a name="operators"></a>Operátorok

A következő részekben példákat talál a splunk és a Azure Monitor közötti különböző operátorok használatára.

> [!NOTE]
> Az alábbi példában az splunk-mező _szabálya_ a Azure monitor egyik táblájára mutat, és a splunk alapértelmezett időbélyeg-leképezése a Logs Analytics _ingestion_time ()_ oszlopra mutat.

### <a name="search"></a>Keresés
A splunk kihagyhatja a `search` kulcsszót, és megadhat egy idézőjelet nem tartalmazó karakterláncot. Azure Monitor el kell indítania az egyes lekérdezéseket a alkalmazásban `find` , a nem jegyzett sztringek oszlop neve, a keresési értéknek pedig egy idézőjeles karakterláncnak kell lennie. 

| | Operátor | Példa |
|:---|:---|:---|
| **Splunk** | **Keresés** | <code>search Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" earliest=-24h</code> |
| **Azure Monitor** | **található** | <code>find Session.Id=="c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time()> ago(24h)</code> |


### <a name="filter"></a>Szűrő
Azure Monitor a naplók lekérdezése táblázatos eredményhalmaz alapján kezdődik, ahol a szűrő. A splunk-ben a szűrés az aktuális index alapértelmezett művelete. Használhatja `where` a splunk operátort is, de nem ajánlott.

| | Operátor | Példa |
|:---|:---|:---|
| **Splunk** | **Keresés** | <code>Event.Rule="330009.2" Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" _indextime>-24h</code> |
| **Azure Monitor** | **ahol** | <code>Office_Hub_OHubBGTaskError<br>&#124; where Session_Id == "c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time() > ago(24h)</code> |

### <a name="getting-n-eventsrows-for-inspection"></a>N esemény/sor beolvasása vizsgálathoz 
A Azure Monitor log-lekérdezések aliasként is támogatják a következőt: `take` `limit` . Ha a splunk az eredmények sorrendjét adják meg, `head` az az első n eredményt fogja visszaadni. Azure Monitor esetén a korlát nem rendezett, de a megtalált első n sort adja vissza.

| | Operátor | Példa |
|:---|:---|:---|
| **Splunk** | **fej** | <code>Event.Rule=330009.2<br>&#124; head 100</code> |
| **Azure Monitor** | **korlátot** | <code>Office_Hub_OHubBGTaskError<br>&#124; limit 100</code> |

### <a name="getting-the-first-n-eventsrows-ordered-by-a-fieldcolumn"></a>Az első n esemény/sor beolvasása egy mező/oszlop alapján rendezve
Az alsó eredmények esetében a splunk használja `tail` . A Azure Monitorban megadhatja a sorrend irányát a használatával `asc` .

| | Operátor | Példa |
|:---|:---|:---|
| **Splunk** | **fej** |  <code>Event.Rule="330009.2"<br>&#124; sort Event.Sequence<br>&#124; head 20</code> |
| **Azure Monitor** | **Top** | <code>Office_Hub_OHubBGTaskError<br>&#124; top 20 by Event_Sequence</code> |

### <a name="extending-the-result-set-with-new-fieldscolumns"></a>Az eredményhalmaz kiterjesztése új mezőkkel/oszlopokkal
A splunk is tartalmaz egy `eval` függvényt, amely nem hasonlítható össze az `eval` operátorral. Mind a `eval` splunk, mind a `extend` Azure monitor operátora csak a skaláris függvények és a aritmetikai operátorok támogatását támogatja.

| | Operátor | Példa |
|:---|:---|:---|
| **Splunk** | **eval** |  <code>Event.Rule=330009.2<br>&#124; eval state= if(Data.Exception = "0", "success", "error")</code> |
| **Azure Monitor** | **kiterjesztése** | <code>Office_Hub_OHubBGTaskError<br>&#124; extend state = iif(Data_Exception == 0,"success" ,"error")</code> |

### <a name="rename"></a>Átnevezés 
Azure Monitor az `project-rename` operátor használatával nevezi át a mezőt. `project-rename` lehetővé teszi a lekérdezés számára, hogy kihasználhassa a mezőhöz előre elkészített indexeket. A splunk rendelkezik egy `rename` operátorral, amely ugyanezt teszi.

| | Operátor | Példa |
|:---|:---|:---|
| **Splunk** | **átnevezése** |  <code>Event.Rule=330009.2<br>&#124; rename Date.Exception as execption</code> |
| **Azure Monitor** | **projekt – Átnevezés** | <code>Office_Hub_OHubBGTaskError<br>&#124; project-rename exception = Date_Exception</code> |

### <a name="format-resultsprojection"></a>Találatok és leképezések formázása
Úgy tűnik, hogy a splunk nem rendelkezik hasonló operátorral `project-away` . A felhasználói felület használatával szűrheti a mezőket.

| | Operátor | Példa |
|:---|:---|:---|
| **Splunk** | **table** |  <code>Event.Rule=330009.2<br>&#124; table rule, state</code> |
| **Azure Monitor** | **projekt**<br>**projekt – vendég** | <code>Office_Hub_OHubBGTaskError<br>&#124; project exception, state</code> |

### <a name="aggregation"></a>Összesítés
Tekintse meg az összesítéseket Azure Monitor a különböző aggregációs függvények [naplózási lekérdezéseit](aggregations.md) .

| | Operátor | Példa |
|:---|:---|:---|
| **Splunk** | **stats** |  <code>search (Rule=120502.*)<br>&#124; stats count by OSEnv, Audience</code> |
| **Azure Monitor** | **Összegzés** | <code>Office_Hub_OHubBGTaskError<br>&#124; summarize count() by App_Platform, Release_Audience</code> |


### <a name="join"></a>Csatlakozás
A splunk való csatlakozás jelentős korlátozásokkal rendelkezik. A segédlekérdezés korlátja 10000 eredmény (a telepítési konfigurációs fájlban van beállítva), és korlátozott számú JOIN-íz van.

| | Operátor | Példa |
|:---|:---|:---|
| **Splunk** | **csatlakozás** |  <code>Event.Rule=120103* &#124; stats by Client.Id, Data.Alias \| join Client.Id max=0 [search earliest=-24h Event.Rule="150310.0" Data.Hresult=-2147221040]</code> |
| **Azure Monitor** | **csatlakozás** | <code>cluster("OAriaPPT").database("Office PowerPoint").Office_PowerPoint_PPT_Exceptions<br>&#124; where  Data_Hresult== -2147221040<br>&#124; join kind = inner (Office_System_SystemHealthMetadata<br>&#124; summarize by Client_Id, Data_Alias)on Client_Id</code>   |

### <a name="sort"></a>Rendezés
A splunk-ben növekvő sorrendbe kell rendezni a `reverse` kezelőt. A Azure Monitor támogatja a null értékek meghatározásának helyét is, az elején vagy a végén.

| | Operátor | Példa |
|:---|:---|:---|
| **Splunk** | **Rendezés** |  <code>Event.Rule=120103<br>&#124; sort Data.Hresult<br>&#124; reverse</code> |
| **Azure Monitor** | **rendezési sorrend** | <code>Office_Hub_OHubBGTaskError<br>&#124; order by Data_Hresult,  desc</code> |

### <a name="multivalue-expand"></a>Többértékes Kibontás
Ez egy hasonló operátor a splunk és a Azure Monitorban is.

| | Operátor | Példa |
|:---|:---|:---|
| **Splunk** | **mvexpand** |  `mvexpand foo` |
| **Azure Monitor** | **mvexpand** | `mvexpand foo` |

### <a name="results-facets-interesting-fields"></a>Eredmények aspektusai, érdekes mezők
A Azure Portal Log Analyticsban csak az első oszlop van kitéve. Az összes oszlop elérhető az API-n keresztül.

| | Operátor | Példa |
|:---|:---|:---|
| **Splunk** | **mezők** |  <code>Event.Rule=330009.2<br>&#124; fields App.Version, App.Platform</code> |
| **Azure Monitor** | **metszettel** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; facet by App_Branch, App_Version</code> |

### <a name="de-duplicate"></a>De-Duplicate
`summarize arg_min()`Ehelyett a kiválasztott rekord sorrendjének megfordítására használható.

| | Operátor | Példa |
|:---|:---|:---|
| **Splunk** | **deduplikáció** |  <code>Event.Rule=330009.2<br>&#124; dedup device_id sortby -batterylife</code> |
| **Azure Monitor** | **összefoglalás arg_max ()** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; summarize arg_max(batterylife, *) by device_id</code> |

## <a name="next-steps"></a>További lépések

- Ugorjon végig egy leckét a [Azure monitor írási napló lekérdezéséhez](get-started-queries.md).
