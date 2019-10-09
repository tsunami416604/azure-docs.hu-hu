---
title: Splunk a Azure Monitor log lekérdezéshez | Microsoft Docs
description: Súgó olyan felhasználók számára, akik már ismerik a splunk a Azure Monitor log-lekérdezések megismerésében.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: bwren
ms.openlocfilehash: 03a0d755cf6d099f07a7c6d853e1d747908eec05
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177636"
---
# <a name="splunk-to-azure-monitor-log-query"></a>Splunk Azure Monitor naplózási lekérdezés

Ebből a cikkből megtudhatja, hogy a splunk-t ismerő felhasználók hogyan írhatják le a Kusto lekérdezési nyelvét Azure Monitor. A kettő közötti közvetlen összehasonlítások a legfontosabb különbségeket, valamint a meglévő ismeretek kihasználása melletti hasonlóságokat is magukban foglalhatnak.

## <a name="structure-and-concepts"></a>Struktúra és fogalmak

A következő táblázat összehasonlítja a splunk és az Azure Monitor-naplók közötti fogalmakat és adatstruktúrákat.

 | Fogalom  | Splunk | Azure Monitor |  Megjegyzés
 | --- | --- | --- | ---
 | Üzembe helyezési egység  | fürt |  fürt |  A Azure Monitor lehetővé teszi a fürtök tetszőleges típusú lekérdezését. A splunk nem. |
 | Adatgyorsítótárak |  vödör  |  Gyorsítótárazási és adatmegőrzési szabályzatok |  Az adatpontok és a gyorsítótárazási szint szabályozása. Ez a beállítás közvetlenül befolyásolja a lekérdezések teljesítményét és a telepítés költségeit. |
 | Az adatlogikai partíció  |  index  |  database  |  Lehetővé teszi az adatmennyiségek logikai elkülönítését. Mindkét implementáció lehetővé teszi a szakszervezetek és az ezekhez való csatlakozást a partíciók között. |
 | Strukturált esemény metaadatainak | – | table |  A splunk nem rendelkezik az esemény metaadatainak keresési nyelvén elérhető koncepcióval. Azure Monitor a naplók egy tábla fogalmával rendelkeznek, amelynek vannak oszlopai. Minden Event-példány egy sorra van leképezve. |
 | Adatrekord | esemény | sor |  Csak a terminológia módosul. |
 | Adatrekord-attribútum | Mező |  Oszlop |  A Azure Monitorban ez a tábla struktúrájának részeként van definiálva. A splunk minden esemény saját mezőket tartalmaz. |
 | Típusú | adattípus |  adattípus |  Azure Monitor adattípusok világosabbak, mert az oszlopokra vannak beállítva. Mindkettő képes dinamikusan dolgozni az adattípusokkal és nagyjából egyenértékű adattípusokkal, beleértve a JSON-támogatást is. |
 | Lekérdezés és keresés  | Keresés | query |  A fogalmak lényegében azonosak a Azure Monitor és a splunk között. |
 | Esemény betöltésének ideje | Rendszeridő | ingestion_time() |  A splunk-ben minden esemény az esemény indexelésének időpontjának időbélyegét kapja meg. Azure Monitorban megadhat egy ingestion_time nevű szabályzatot, amely egy olyan rendszeroszlopt tesz elérhetővé, amely a ingestion_time () függvényen keresztül hivatkozhat. |

## <a name="functions"></a>Functions

A következő táblázat a splunk függvényekkel egyenértékű Azure Monitor függvényeit határozza meg.

|Splunk | Azure Monitor |Megjegyzés
|---|---|---
|strcat | strcat()| (1) |
|split  | split() | (1) |
|Ha     | IFF ()   | (1) |
|tonumber | todouble()<br>tolong ()<br>toint() | (1) |
|felső<br>alacsonyabb |toupper()<br>tolower()|(1) |
| csere | replace() | (1)<br> Azt is vegye figyelembe, hogy míg a `replace()` három paramétert vesz igénybe mindkét termékben, a paraméterek eltérőek. |
| substr | alkarakterlánc () | (1)<br>Azt is vegye figyelembe, hogy a splunk egy-alapú indexeket használ. Azure Monitor Megjegyzés nulla alapú indexek. |
| ToLower |  tolower() | (1) |
| toupper | toupper() | (1) |
| mérkőzés | megfelel a regexnek |  (2)  |
| regex | megfelel a regexnek | A splunk-ben a `regex` operátor. A Azure Monitorban ez egy összehasonlító operátor. |
| searchmatch | == | A splunk `searchmatch` lehetővé teszi a pontos karakterlánc keresését.
| véletlenszerű | rand()<br>rand (n) | A splunk függvény nulla és 2<sup>31</sup>– 1 közötti számot ad vissza. A Azure Monitor "egy 0,0 és 1,0 közötti számot ad vissza, vagy ha a megadott paraméter 0 és n – 1 között van.
| most | now() | (1)
| relative_time | totimespan() | (1)<br>Azure Monitor a splunk egyenértékű a relative_time (datetimeVal, offsetVal) a datetimeVal + ToTimeSpan (offsetVal).<br>A <code>search &#124; eval n=relative_time(now(), "-1d@d")</code> például <code>...  &#124; extend myTime = now() - totimespan("1d")</code> lesz.

(1) a splunk a függvényt a `eval` operátor hívja meg. A Azure Monitor `extend` vagy `project` részeként használatos.<br>(2) a splunk a függvényt a `eval` operátor hívja meg. A Azure Monitor a `where` operátorral használható.


## <a name="operators"></a>Operátorok

A következő részekben példákat talál a splunk és a Azure Monitor közötti különböző operátorok használatára.

> [!NOTE]
> Az alábbi példában az splunk-mező _szabálya_ egy Azure monitor táblára mutat, és a splunk alapértelmezett időbélyeg-leképezése a logs Analytics _ingestion_time ()_ oszlopra mutat.

### <a name="search"></a>Keresés
A splunk kihagyhatja a `search` kulcsszót, és megadhat egy idézőjelet nem tartalmazó karakterláncot. Azure Monitor el kell indítania az egyes lekérdezéseket a `find` értékkel, a nem idézőjeles sztringek oszlop neve, a keresési értéknek pedig egy idézőjeles karakterláncnak kell lennie. 

| |  | |
|:---|:---|:---|
| Splunk | **Keresés** | <code>search Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" earliest=-24h</code> |
| Azure Monitor | **find** | <code>find Session.Id=="c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time()> ago(24h)</code> |
| | |

### <a name="filter"></a>Szűrés
Azure Monitor a naplók lekérdezése táblázatos eredményhalmaz alapján kezdődik, ahol a szűrő. A splunk-ben a szűrés az aktuális index alapértelmezett művelete. @No__t-0 operátort is használhat a splunk-ben, de nem ajánlott.

| |  | |
|:---|:---|:---|
| Splunk | **Keresés** | <code>Event.Rule="330009.2" Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" _indextime>-24h</code> |
| Azure Monitor | **ahol** | <code>Office_Hub_OHubBGTaskError<br>&#124; where Session_Id == "c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time() > ago(24h)</code> |
| | |


### <a name="getting-n-eventsrows-for-inspection"></a>N esemény/sor beolvasása vizsgálathoz 
Azure Monitor a naplózási lekérdezések a `take` aliast is támogatják `limit` értékként. A splunk-ben, ha az eredmények sorrendbe vannak rendezve, `head` az első n eredményt fogja visszaadni. Azure Monitor esetén a korlát nem rendezett, de a megtalált első n sort adja vissza.

| |  | |
|:---|:---|:---|
| Splunk | **fej** | <code>Event.Rule=330009.2<br>&#124; head 100</code> |
| Azure Monitor | **limit** | <code>Office_Hub_OHubBGTaskError<br>&#124; limit 100</code> |
| | |



### <a name="getting-the-first-n-eventsrows-ordered-by-a-fieldcolumn"></a>Az első n esemény/sor beolvasása egy mező/oszlop alapján rendezve
Az alsó eredmények esetében a splunk `tail` használatát használja. Azure Monitor megadhatja a sorrend irányát `asc` értékkel.

| |  | |
|:---|:---|:---|
| Splunk | **fej** |  <code>Event.Rule="330009.2"<br>&#124; sort Event.Sequence<br>&#124; head 20</code> |
| Azure Monitor | **Top** | <code>Office_Hub_OHubBGTaskError<br>&#124; top 20 by Event_Sequence</code> |
| | |




### <a name="extending-the-result-set-with-new-fieldscolumns"></a>Az eredményhalmaz kiterjesztése új mezőkkel/oszlopokkal
A splunk `eval` függvényt is tartalmaz, amely nem hasonlítható össze az `eval` operátorral. A splunk `eval` operátora és a Azure Monitor `extend` operátora csak a skaláris függvényeket és a aritmetikai operátorokat támogatja.

| |  | |
|:---|:---|:---|
| Splunk | **eval** |  <code>Event.Rule=330009.2<br>&#124; eval state= if(Data.Exception = "0", "success", "error")</code> |
| Azure Monitor | **kiterjesztése** | <code>Office_Hub_OHubBGTaskError<br>&#124; extend state = iif(Data_Exception == 0,"success" ,"error")</code> |
| | |


### <a name="rename"></a>Átnevezés 
A Azure Monitor a `project-rename` operátor használatával nevezi át a mezőt. a `project-rename` lehetővé teszi, hogy a lekérdezés kihasználhassa a mezőhöz előre elkészített indexeket. A splunk `rename` operátorral rendelkezik.

| |  | |
|:---|:---|:---|
| Splunk | **átnevezése** |  <code>Event.Rule=330009.2<br>&#124; rename Date.Exception as execption</code> |
| Azure Monitor | **projekt – Átnevezés** | <code>Office_Hub_OHubBGTaskError<br>&#124; project-rename exception = Date_Exception</code> |
| | |




### <a name="format-resultsprojection"></a>Találatok és leképezések formázása
Úgy tűnik, hogy a splunk nem rendelkezik a `project-away` értékhez hasonló operátorral. A felhasználói felület használatával szűrheti a mezőket.

| |  | |
|:---|:---|:---|
| Splunk | **tábla** |  <code>Event.Rule=330009.2<br>&#124; table rule, state</code> |
| Azure Monitor | **project**<br>**project-away** | <code>Office_Hub_OHubBGTaskError<br>&#124; project exception, state</code> |
| | |



### <a name="aggregation"></a>Összesítés
Tekintse meg az összesítéseket Azure Monitor a különböző aggregációs függvények [naplózási lekérdezéseit](aggregations.md) .

| |  | |
|:---|:---|:---|
| Splunk | **statisztikák** |  <code>search (Rule=120502.*)<br>&#124; stats count by OSEnv, Audience</code> |
| Azure Monitor | **Összegzés** | <code>Office_Hub_OHubBGTaskError<br>&#124; summarize count() by App_Platform, Release_Audience</code> |
| | |



### <a name="join"></a>Csatlakozás
A splunk való csatlakozás jelentős korlátozásokkal rendelkezik. A segédlekérdezés korlátja 10000 eredmény (a telepítési konfigurációs fájlban van beállítva), és korlátozott számú JOIN-íz van.

| |  | |
|:---|:---|:---|
| Splunk | **join** |  <code>Event.Rule=120103* &#124; stats by Client.Id, Data.Alias \| join Client.Id max=0 [search earliest=-24h Event.Rule="150310.0" Data.Hresult=-2147221040]</code> |
| Azure Monitor | **join** | <code>cluster("OAriaPPT").database("Office PowerPoint").Office_PowerPoint_PPT_Exceptions<br>&#124; where  Data_Hresult== -2147221040<br>&#124; join kind = inner (Office_System_SystemHealthMetadata<br>&#124; summarize by Client_Id, Data_Alias)on Client_Id</code>   |
| | |



### <a name="sort"></a>Rendezés
A splunk-ben a növekvő sorrendbe rendezéshez a `reverse` operátort kell használnia. A Azure Monitor támogatja a null értékek meghatározásának helyét is, az elején vagy a végén.

| |  | |
|:---|:---|:---|
| Splunk | **Rendezés** |  <code>Event.Rule=120103<br>&#124; sort Data.Hresult<br>&#124; reverse</code> |
| Azure Monitor | **rendezési sorrend** | <code>Office_Hub_OHubBGTaskError<br>&#124; order by Data_Hresult,  desc</code> |
| | |



### <a name="multivalue-expand"></a>Többértékes Kibontás
Ez egy hasonló operátor a splunk és a Azure Monitorban is.

| |  | |
|:---|:---|:---|
| Splunk | **mvexpand** |  `mvexpand foo` |
| Azure Monitor | **mvexpand** | `mvexpand foo` |
| | |




### <a name="results-facets-interesting-fields"></a>Eredmények aspektusai, érdekes mezők
A Azure Portal Log Analyticsban csak az első oszlop van kitéve. Az összes oszlop elérhető az API-n keresztül.

| |  | |
|:---|:---|:---|
| Splunk | **mezők** |  <code>Event.Rule=330009.2<br>&#124; fields App.Version, App.Platform</code> |
| Azure Monitor | **metszettel** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; facet by App_Branch, App_Version</code> |
| | |




### <a name="de-duplicate"></a>De-duplicate
A `summarize arg_min()` helyett használhatja a kiválasztott rekord sorrendjének megfordítását.

| |  | |
|:---|:---|:---|
| Splunk | **deduplikáció** |  <code>Event.Rule=330009.2<br>&#124; dedup device_id sortby -batterylife</code> |
| Azure Monitor | **arg_max összegzése ()** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; summarize arg_max(batterylife, *) by device_id</code> |
| | |




## <a name="next-steps"></a>További lépések

- Ugorjon végig egy leckét a [Azure monitor írási napló lekérdezéséhez](get-started-queries.md).
