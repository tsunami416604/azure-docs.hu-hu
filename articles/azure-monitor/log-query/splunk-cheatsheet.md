---
title: Splunk az Azure Monitor naplólekérdezés | Microsoft dokumentumok
description: Súgó azoknak a felhasználóknak, akik ismerik a Splunk-ot az Azure Monitor naplólekérdezéseinek tanulásában.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2018
ms.openlocfilehash: 6346055f1169bfa533d5dbfe441ecf27fb0d78a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75397745"
---
# <a name="splunk-to-azure-monitor-log-query"></a>Splunk az Azure Monitor naplólekérdezés

Ez a cikk célja, hogy segítse a felhasználók, akik ismerik a Splunk a Kusto lekérdezési nyelv írása naplólekérdezések az Azure Monitorban. Közvetlen összehasonlításokat végeznek a kettő között, hogy megértsék a legfontosabb különbségeket és hasonlóságokat is, ahol kihasználhatja meglévő tudását.

## <a name="structure-and-concepts"></a>Szerkezet és koncepciók

Az alábbi táblázat a Splunk és az Azure Monitor-naplók közötti fogalmakat és adatstruktúrákat hasonlítja össze.

 | Fogalom  | Splunk | Azure Monitor |  Megjegyzés
 | --- | --- | --- | ---
 | Központi telepítési egység  | cluster |  cluster |  Az Azure Monitor lehetővé teszi tetszőleges fürtalapú lekérdezések. Splunk nem. |
 | Adatgyorsítótárak |  Vödör  |  Gyorsítótárazási és adatmegőrzési házirendek |  Az adatok időszaki és gyorsítótárazási szintjét szabályozza. Ez a beállítás közvetlenül befolyásolja a lekérdezések teljesítményét és a központi telepítés költségét. |
 | Az adatok logikai felosztása  |  Index  |  adatbázis  |  Lehetővé teszi az adatok logikai elkülönítését. Mindkét implementáció lehetővé teszi a szakszervezetek és a csatlakozás ezeken a partíciókon. |
 | Strukturált esemény metaadatai | N/A | tábla |  A Splunk fogalma nem rendelkezik az esemény metaadatainak keresési nyelvével. Az Azure Monitor naplók egy tábla, amely oszlopokat tartalmaz. Minden eseménypéldány egy sorhoz van rendelve. |
 | Adatrekord | Esemény | Sor |  A terminológia csak változik. |
 | Adatrekord attribútum | mező |  oszlop |  Az Azure Monitorban ez előre definiálva a táblastruktúra részeként. A Splunk, minden esemény saját mezőket. |
 | Típusok | Adattípus |  Adattípus |  Az Azure Monitor adattípusai egyértelműbbek, mivel az oszlopokon vannak beállítva. Mindkettő képes dinamikusan dolgozni adattípusokkal és nagyjából egyenértékű adattípusokkal, beleértve a JSON-támogatást is. |
 | Lekérdezés és keresés  | keresés | lekérdezés |  Fogalmak lényegében azonosak az Azure Monitor és a Splunk között. |
 | Esemény betöltési ideje | Rendszeridő | ingestion_time() |  A Splunk, minden esemény kap egy rendszer időbélyege az esemény indexelése. Az Azure Monitorban definiálhat egy ingestion_time nevű szabályzatot, amely a ingestion_time() függvényen keresztül hivatkozható rendszeroszlopot tetszetős. |

## <a name="functions"></a>Functions

Az alábbi táblázat az Azure Monitorban azokat a függvényeket határozza meg, amelyek egyenértékűek a Splunk függvényekkel.

|Splunk | Azure Monitor |Megjegyzés
|---|---|---
|strcat között | strcat()| (1) |
|felosztás  | felosztás() | (1) |
|if     | iff()   | (1) |
|tonumber | todouble()<br>tolong()<br>toint() | (1) |
|Felső<br>Alsó |toupper()<br>tolower()|(1) |
| Helyettesít | csere() | (1)<br> Azt is `replace()` vegye figyelembe, hogy bár mindkét termékben három paramétert vesz igénybe, a paraméterek eltérőek. |
| rész-str | karakterlánc() | (1)<br>Azt is vegye figyelembe, hogy a Splunk egyalapú indexeket használ. Az Azure Monitor nulla alapú indexeket jegyez meg. |
| lassabb |  tolower() | (1) |
| toupper (vacsora) | toupper() | (1) |
| Mérkőzés | egyezik regex |  (2)  |
| Regex | egyezik regex | A Splunk, `regex` egy üzemeltető. Az Azure Monitorban ez egy relációs operátor. |
| keresés | == | A Splunk, `searchmatch` lehetővé teszi a pontos karakterlánc keresését.
| Véletlen | rand()<br>rand(n) | A Splunk függvénye nullától 2<sup>31</sup>-1-ig visszaad egy számot. Az Azure Monitor' 0.0 és 1.0 közötti számot ad vissza, vagy ha egy megadott paraméter 0 és n-1 között van.
| most | now() | (1)
| relative_time | totimespan() | (1)<br>Az Azure Monitorban a Splunk relative_time(datetimeVal, offsetVal) megfelelője datetimeVal + totimespan(offsetVal).<br>Például <code>search &#124; eval n=relative_time(now(), "-1d@d")</code> a <code>...  &#124; extend myTime = now() - totimespan("1d")</code>lesz .

(1) A Splunk, a funkció `eval` meghívja az üzemeltető. Az Azure Monitorban a részeként használják, `extend` vagy `project`.<br>(2) A Splunk, a funkció `eval` meghívja az üzemeltető. Az Azure Monitorban használható az `where` operátorral.


## <a name="operators"></a>Operátorok

A következő szakaszok példákat a splunk és az Azure Monitor közötti különböző operátorok használatával.

> [!NOTE]
> A következő példa alkalmazásában a Splunk _mezőszabály_ leképezi egy táblára az Azure Monitorban, és a Splunk alapértelmezett időbélyege leképezi a Naplók analytics _ingestion_time()_ oszlop.

### <a name="search"></a>Keresés
A Splunk alkalmazásban kihagyhatja a `search` kulcsszót, és megadhat egy nem idézett karakterláncot. Az Azure Monitorban minden `find`lekérdezést a használatával kell elindítania, egy nem idézett karakterlánc oszlopnév, és a kerestértéknek idézett karakterláncnak kell lennie. 

| |  | |
|:---|:---|:---|
| Splunk | **Keresés** | <code>search Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" earliest=-24h</code> |
| Azure Monitor | **find** | <code>find Session.Id=="c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time()> ago(24h)</code> |
| | |

### <a name="filter"></a>Szűrés
Az Azure Monitor naplólekérdezései egy táblázatos eredményhalmazból indulnak, ahol a szűrő. A Splunk alkalmazásban a szűrés az aktuális index alapértelmezett művelete. `where` Használhatja a splunk operátort is, de nem ajánlott.

| |  | |
|:---|:---|:---|
| Splunk | **Keresés** | <code>Event.Rule="330009.2" Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" _indextime>-24h</code> |
| Azure Monitor | **Ahol** | <code>Office_Hub_OHubBGTaskError<br>&#124; where Session_Id == "c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time() > ago(24h)</code> |
| | |


### <a name="getting-n-eventsrows-for-inspection"></a>Első n események / sorok ellenőrzés 
Az Azure Monitor `take` naplólekérdezései `limit`aliasként is támogatottak. A Splunk, ha az `head` eredmények megrendelt, visszaadja az első n eredményeket. Az Azure Monitorban a korlát nincs rendezve, de az első n-sorokat adja vissza.

| |  | |
|:---|:---|:---|
| Splunk | **Fej** | <code>Event.Rule=330009.2<br>&#124; head 100</code> |
| Azure Monitor | **Korlátoz** | <code>Office_Hub_OHubBGTaskError<br>&#124; limit 100</code> |
| | |



### <a name="getting-the-first-n-eventsrows-ordered-by-a-fieldcolumn"></a>Az első n események/sorok mező/oszlop által rendezett beolvasása
Az alsó eredmények, a Splunk használja `tail`. Az Azure Monitorban megadhatja `asc`a rendezési irányt a segítségével.

| |  | |
|:---|:---|:---|
| Splunk | **Fej** |  <code>Event.Rule="330009.2"<br>&#124; sort Event.Sequence<br>&#124; head 20</code> |
| Azure Monitor | **Top** | <code>Office_Hub_OHubBGTaskError<br>&#124; top 20 by Event_Sequence</code> |
| | |




### <a name="extending-the-result-set-with-new-fieldscolumns"></a>Az eredményhalmaz kiterjesztése új mezőkkel/oszlopokkal
Splunk is `eval` van egy funkció, amely nem `eval` hasonlítható össze a kezelő. A `eval` Splunk operátora `extend` és az Azure Monitor operátora is csak skalárfüggvényeket és számtani operátorokat támogat.

| |  | |
|:---|:---|:---|
| Splunk | **Eval** |  <code>Event.Rule=330009.2<br>&#124; eval state= if(Data.Exception = "0", "success", "error")</code> |
| Azure Monitor | **Kiterjesztése** | <code>Office_Hub_OHubBGTaskError<br>&#124; extend state = iif(Data_Exception == 0,"success" ,"error")</code> |
| | |


### <a name="rename"></a>Átnevezés 
Az Azure `project-rename` Monitor az operátor t használja egy mező átnevezéséhez. `project-rename`Lehetővé teszi, hogy a lekérdezés kihasználja a mezőhöz előre elkészített indexeket. Splunk van `rename` egy üzemeltető, hogy ugyanezt tegye.

| |  | |
|:---|:---|:---|
| Splunk | **Átnevez** |  <code>Event.Rule=330009.2<br>&#124; rename Date.Exception as execption</code> |
| Azure Monitor | **projekt átnevezése** | <code>Office_Hub_OHubBGTaskError<br>&#124; project-rename exception = Date_Exception</code> |
| | |




### <a name="format-resultsprojection"></a>Eredmények/vetítés formázása
Splunk nem úgy tűnik, `project-away`hogy egy üzemeltető hasonló . A felhasználói felület segítségével szűrheti a mezőket.

| |  | |
|:---|:---|:---|
| Splunk | **Táblázat** |  <code>Event.Rule=330009.2<br>&#124; table rule, state</code> |
| Azure Monitor | **Projekt**<br>**projekt-el** | <code>Office_Hub_OHubBGTaskError<br>&#124; project exception, state</code> |
| | |



### <a name="aggregation"></a>Összesítés
Tekintse meg az [Aggregations az Azure Monitor naplólekérdezések](aggregations.md) a különböző összesítési függvények.

| |  | |
|:---|:---|:---|
| Splunk | **Statisztika** |  <code>search (Rule=120502.*)<br>&#124; stats count by OSEnv, Audience</code> |
| Azure Monitor | **Összefoglalni** | <code>Office_Hub_OHubBGTaskError<br>&#124; summarize count() by App_Platform, Release_Audience</code> |
| | |



### <a name="join"></a>Csatlakozás
Csatlakozz on Splunk jelentős korlátai vannak. A segédlekérdezés legfeljebb 10000 találatot ért el (a központi telepítési konfigurációs fájlban állítható be), és korlátozott számú illesztési aroma található.

| |  | |
|:---|:---|:---|
| Splunk | **csatlakozás** |  <code>Event.Rule=120103* &#124; stats by Client.Id, Data.Alias \| join Client.Id max=0 [search earliest=-24h Event.Rule="150310.0" Data.Hresult=-2147221040]</code> |
| Azure Monitor | **csatlakozás** | <code>cluster("OAriaPPT").database("Office PowerPoint").Office_PowerPoint_PPT_Exceptions<br>&#124; where  Data_Hresult== -2147221040<br>&#124; join kind = inner (Office_System_SystemHealthMetadata<br>&#124; summarize by Client_Id, Data_Alias)on Client_Id</code>   |
| | |



### <a name="sort"></a>Rendezés
A Splunk,a rendezés növekvő sorrendben `reverse` kell használni a kezelő. Az Azure Monitor azt is támogatja, hogy meghatározza, hová helyezze nulls, az elején vagy a végén.

| |  | |
|:---|:---|:---|
| Splunk | **Rendezés** |  <code>Event.Rule=120103<br>&#124; sort Data.Hresult<br>&#124; reverse</code> |
| Azure Monitor | **sorrend ben** | <code>Office_Hub_OHubBGTaskError<br>&#124; order by Data_Hresult,  desc</code> |
| | |



### <a name="multivalue-expand"></a>Többértékű kibontás
Ez egy hasonló operátor splunk és az Azure Monitor.

| |  | |
|:---|:---|:---|
| Splunk | **mvexpand** |  `mvexpand foo` |
| Azure Monitor | **mvexpand** | `mvexpand foo` |
| | |




### <a name="results-facets-interesting-fields"></a>Eredmények facets, érdekes mezők
Az Azure Portalon a Log Analytics csak az első oszlop látható. Az összes oszlop elérhető az API-n keresztül.

| |  | |
|:---|:---|:---|
| Splunk | **Mezők** |  <code>Event.Rule=330009.2<br>&#124; fields App.Version, App.Platform</code> |
| Azure Monitor | **Arcát** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; facet by App_Branch, App_Version</code> |
| | |




### <a name="de-duplicate"></a>Másolat törlése
Ehelyett megfordíthatja `summarize arg_min()` a kiválasztott rekord sorrendjét.

| |  | |
|:---|:---|:---|
| Splunk | **dedup között** |  <code>Event.Rule=330009.2<br>&#124; dedup device_id sortby -batterylife</code> |
| Azure Monitor | **arg_max összegzése()** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; summarize arg_max(batterylife, *) by device_id</code> |
| | |




## <a name="next-steps"></a>További lépések

- Az Azure Monitor [írási naplólekérdezéseiről](get-started-queries.md)szóló leckében halad át.
