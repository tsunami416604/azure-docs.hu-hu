---
title: Az Azure Monitor log-lekérdezéshez Splunk |} A Microsoft Docs
description: Felhasználók, akik Splunk ismeri az Azure Monitor log-lekérdezések tanulási súgóját.
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
ms.openlocfilehash: dafafa8ff5d721034b3b10bdeb1a2fc09cd32835
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56267580"
---
# <a name="splunk-to-azure-monitor-log-query"></a>Az Azure Monitor log-lekérdezéshez Splunk

Ebből a cikkből segítséget nyújtanak a felhasználók, akik jól ismerik a Kusto-lekérdezési nyelvet, amely log lekérdezéseket írni az Azure monitorban további Splunk. Közvetlen összehasonlítások meglévő ismereteit használhatja, a két legfontosabb különbségek megértéséhez, és hasonlóságokat között történik.

## <a name="structure-and-concepts"></a>Struktúra és fogalmak

A következő táblázat összehasonlítja a fogalmakat és adatstruktúrák Splunk és az Azure Monitor naplók között.

 | Fogalom  | Splunk | Azure Monitor |  Megjegyzés
 | --- | --- | --- | ---
 | Jednotka nasazení  | fürt |  fürt |  Az Azure Monitor lehetővé teszi tetszőleges fürt lekérdezések közötti. Splunk nem létezik. |
 | Felhasználóiadat-gyorsítótárak |  gyűjtők  |  Gyorsítótárazás és -megőrzési szabályok |  Azt szabályozza, az időszak és a gyorsítótárazás az adatok szintjét. Ezzel a beállítással közvetlenül hatással van, a lekérdezések teljesítménye és a költség az üzembe helyezés. |
 | Az adatok logikai partíció  |  index  |  adatbázis  |  Lehetővé teszi az adatok logikai elkülönítését. Mindkét megvalósítását lehetővé teszik, egyesítések és csatlakozás az érintett partíciók között. |
 | Strukturált esemény-metaadatok | – | tábla |  Splunk nem rendelkezik a fogalom a keresési nyelv, az esemény-metaadatok vannak kitéve. Az Azure Monitor naplóira rendelkezik egy táblát, oszlopot tartalmaz, amely a fogalmát. Minden egyes esemény példány egy sorra van leképezve. |
 | Adatfelderítési rekordok | esemény | sor |  Csak terminológiai változás. |
 | Rekord adatattribútum | A mező |  Oszlop |  Az Azure monitorban ezzel előre a táblázat szerkezetét részeként. A Splunkban mindegyik esemény rendelkezik a saját mezőket. |
 | Típusok | adattípus |  adattípus |  Az Azure Monitor adattípusok több explicit módon az oszlopok vannak beállítva. Az adattípusok és adattípusok többek között a JSON-támogatás nagyjából készletét dinamikusan képességüket is rendelkezik. |
 | Lekérdezés és a keresés  | keresés | lekérdezés |  Fogalmak alapvetően ugyanazok, az Azure Monitor és a Splunk között. |
 | Esemény betöltési ideje | Rendszer pontos ideje | ingestion_time() |  Splunk minden egyes esemény egy időbélyegző az idő, az esemény volt indexelt lesz. Az Azure monitorban meghatározhatja egy szabályzatot, amely elérhetővé teszi a rendszer oszlopot, amely a ingestion_time() funkción keresztül lehet rá hivatkozni ingestion_time nevű. |

## <a name="functions"></a>Functions

Az alábbi táblázat felsorolja a functions az Azure monitorban Splunk funkciók egyenértékű.

|Splunk | Azure Monitor |Megjegyzés
|---|---|---
|strcat | strcat()| (1) |
|felosztás  | split() | (1) |
|Ha     | iff()   | (1) |
|tonumber | todouble()<br>tolong()<br>toint() | (1) |
|felső<br>Alacsonyabb |toupper()<br>tolower()|(1) |
| cserélje le | replace() | (1)<br> Azt is vegye figyelembe, hogy közben `replace()` három paramétert vesz igénybe a mindkét terméket, a paraméterek különböznek. |
| SUBSTR | Substring() | (1)<br>Azt is vegye figyelembe, hogy a Splunk egy alapú indexek használja. Az Azure Monitor megjegyzi a nulla alapú index. |
| ToLower |  tolower() | (1) |
| ToUpper | toupper() | (1) |
| egyezés | megfelel reguláris kifejezés |  (2)  |
| regex | megfelel reguláris kifejezés | A Splunkban `regex` operátor. Az Azure monitorban az összehasonlító operátor. |
| searchmatch | == | A Splunkban `searchmatch` lehetővé teszi, hogy a pontos karakterláncokat keresése.
| véletlenszerű | rand()<br>rand(n) | A Splunk függvény számot ad vissza 2 nulla<sup>31</sup>– 1. Az Azure Monitor "0,0 és 1,0, közötti számot ad vissza, vagy ha egy 0 és n-1 között a megadott paraméter.
| most | now() | (1)
| relative_time | ToTimeSpan() | (1)<br>Az Azure monitorban a Splunk a megfelelőjét (datetimeVal, offsetVal) relative_time datetimeVal + totimespan(offsetVal).<br>Ha például <code>search &#124; eval n=relative_time(now(), "-1d@d")</code> válik <code>...  &#124; extend myTime = now() - totimespan("1d")</code>.

(1) a Splunkban, a függvény meghívása a `eval` operátor. Az Azure monitorban használatos részeként `extend` vagy `project`.<br>(2) a Splunkban, a függvény meghívása a `eval` operátor. Az Azure monitorban használható együtt a `where` operátor.


## <a name="operators"></a>Operátorok

A következő szakaszokban eltérő operátorok közötti Splunk és az Azure Monitor használatával példákat mutatnak be.

> [!NOTE]
> A következő példában a Splunk mező céljából _szabály_ egy táblát az Azure monitorban vannak leképezve, és a Splunk alapértelmezett időbélyeg képezi le a naplók elemzési _ingestion_time()_ oszlop.

### <a name="search"></a>Keresés
A Splunkban, kihagyhatja a `search` kulcsszó és a egy nem jegyzett karakterláncot kell megadni. Az Azure monitorban el kell indítania az egyes lekérdezések `find`, nem jegyzett karakterláncra az oszlop nevét, és a keresési érték határolójeles karakterláncnak kell lennie. 

| |  | |
|:---|:---|:---|
| Splunk | **Keresés** | <code>search Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" earliest=-24h</code> |
| Azure Monitor | **find** | <code>find Session.Id=="c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time()> ago(24h)</code> |
| | |

### <a name="filter"></a>Szűrés
Az Azure Monitor log lekérdezések kezdő táblázatos eredmény where állítsa be a szűrőt. A Splunkban szűrés az alapértelmezett az aktuális index műveletet. Is `where` operátor Splunk, de nem ajánlott.

| |  | |
|:---|:---|:---|
| Splunk | **Keresés** | <code>Event.Rule="330009.2" Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" _indextime>-24h</code> |
| Azure Monitor | **ahol** | <code>Office_Hub_OHubBGTaskError<br>&#124; where Session_Id == "c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time() > ago(24h)</code> |
| | |


### <a name="getting-n-eventsrows-for-inspection"></a>Hálózatfelügyeleti n események/sorok beolvasása 
Az Azure Monitor log lekérdezéseket is támogatási `take` az aliasként `limit`. A Splunkban, ha az eredmények vannak rendezve `head` az első n eredményeket adja vissza. Az Azure monitorban korlát nem mutassa, de található első n sorát adja vissza.

| |  | |
|:---|:---|:---|
| Splunk | **head** | <code>Event.Rule=330009.2<br>&#124; head 100</code> |
| Azure Monitor | **limit** | <code>Office_Hub_OHubBGTaskError<br>&#124; limit 100</code> |
| | |



### <a name="getting-the-first-n-eventsrows-ordered-by-a-fieldcolumn"></a>Bevezetés az első n események/sorok mező vagy oszlop szerint rendezve
Alsó eredményt, a Splunkban használhatja `tail`. Az Azure monitorban adja meg a rendezési iránya ütközik `asc`.

| |  | |
|:---|:---|:---|
| Splunk | **head** |  <code>Event.Rule="330009.2"<br>&#124; sort Event.Sequence<br>&#124; head 20</code> |
| Azure Monitor | **felső** | <code>Office_Hub_OHubBGTaskError<br>&#124; top 20 by Event_Sequence</code> |
| | |




### <a name="extending-the-result-set-with-new-fieldscolumns"></a>Az eredmény kiterjesztése új mezőket és oszlopokat tartalmazó beállítása
Splunk is rendelkezik egy `eval` függvény, amely nem az összehasonlítható a `eval` operátor. Mindkét a `eval` a Splunkban operátor és a `extend` az Azure monitorban operátor csak a skaláris függvények és aritmetikai operátor támogatják.

| |  | |
|:---|:---|:---|
| Splunk | **Próbaverzió** |  <code>Event.Rule=330009.2<br>&#124; eval state= if(Data.Exception = "0", "success", "error")</code> |
| Azure Monitor | **extend** | <code>Office_Hub_OHubBGTaskError<br>&#124; extend state = iif(Data_Exception == 0,"success" ,"error")</code> |
| | |


### <a name="rename"></a>Átnevezés 
Az Azure Monitor az azonos operátort használja, átnevezése, és hozzon létre egy új mezőt. Splunk van két külön operátorok, `eval` és `rename`.

| |  | |
|:---|:---|:---|
| Splunk | **Nevezze át** |  <code>Event.Rule=330009.2<br>&#124; rename Date.Exception as execption</code> |
| Azure Monitor | **extend** | <code>Office_Hub_OHubBGTaskError<br>&#124; extend exception = Date_Exception</code> |
| | |




### <a name="format-resultsprojection"></a>Eredmények/leképezési formátum
Splunk nem úgy tűnik, hasonló az operátornak nincs `project-away`. A felhasználói felület segítségével szűrheti a kötelező mezők.

| |  | |
|:---|:---|:---|
| Splunk | **Tábla** |  <code>Event.Rule=330009.2<br>&#124; table rule, state</code> |
| Azure Monitor | **project**<br>**project-away** | <code>Office_Hub_OHubBGTaskError<br>&#124; project exception, state</code> |
| | |



### <a name="aggregation"></a>Összesítés
Tekintse meg a [összesítések az Azure monitorban lekérdezések naplózását](aggregations.md) eltérő összesítési függvényeihez.

| |  | |
|:---|:---|:---|
| Splunk | **Stats** |  <code>search (Rule=120502.*)<br>&#124; stats count by OSEnv, Audience</code> |
| Azure Monitor | **summarize** | <code>Office_Hub_OHubBGTaskError<br>&#124; summarize count() by App_Platform, Release_Audience</code> |
| | |



### <a name="join"></a>Csatlakozás
Csatlakozás a Splunkban jelentős korlátokkal rendelkezik. A segédlekérdezés korlátja (állítsa be a telepítési konfigurációs fájl) 10000 eredményeket, és ott való csatlakozás csak korlátozott számú változatban érhetők el.

| |  | |
|:---|:---|:---|
| Splunk | **csatlakozás** |  <code>Event.Rule=120103* &#124; stats by Client.Id, Data.Alias | Csatlakozás Client.Id max. = 0 [legkorábbi = 24 órás Event.Rule="150310.0 keresése" Data.Hresult=-2147221040]</code> |
| Azure Monitor | **csatlakozás** | <code>cluster("OAriaPPT").database("Office PowerPoint").Office_PowerPoint_PPT_Exceptions<br>&#124; where  Data_Hresult== -2147221040<br>&#124; join kind = inner (Office_System_SystemHealthMetadata<br>&#124; summarize by Client_Id, Data_Alias)on Client_Id</code>   |
| | |



### <a name="sort"></a>Rendezés
A Splunkban, növekvő sorrendben kell használnia a `reverse` operátor. Az Azure figyelése is támogatja, határozza meg, hová kerüljön a null értékeket, elején vagy végén.

| |  | |
|:---|:---|:---|
| Splunk | **Rendezés** |  <code>Event.Rule=120103<br>&#124; sort Data.Hresult<br>&#124; reverse</code> |
| Azure Monitor | **rendezési** | <code>Office_Hub_OHubBGTaskError<br>&#124; order by Data_Hresult,  desc</code> |
| | |



### <a name="multivalue-expand"></a>Bontsa ki a típushoz
Ez az a Splunk és az Azure Monitor egy hasonló operátort.

| |  | |
|:---|:---|:---|
| Splunk | **mvexpand** |  `mvexpand foo` |
| Azure Monitor | **mvexpand** | `mvexpand foo` |
| | |




### <a name="results-facets-interesting-fields"></a>Eredmények metszettel, érdekes mezők
A Log Analytics az Azure Portalon csak az első oszlop ki van téve. Az összes oszlop az API-n keresztül érhetők el.

| |  | |
|:---|:---|:---|
| Splunk | **Mezők** |  <code>Event.Rule=330009.2<br>&#124; fields App.Version, App.Platform</code> |
| Azure Monitor | **aspektusokat** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; facet by App_Branch, App_Version</code> |
| | |




### <a name="de-duplicate"></a>De-duplicate
Használhat `summarize arg_min()` inkább megfordításához, amelyek a rekord kiválasztott beolvasása.

| |  | |
|:---|:---|:---|
| Splunk | **dedup** |  <code>Event.Rule=330009.2<br>&#124; dedup device_id sortby -batterylife</code> |
| Azure Monitor | **arg_max() összefoglalója** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; summarize arg_max(batterylife, *) by device_id</code> |
| | |




## <a name="next-steps"></a>További lépések

- A lecke meg a [log lekérdezések írásának módját az Azure monitorban](get-started-queries.md).