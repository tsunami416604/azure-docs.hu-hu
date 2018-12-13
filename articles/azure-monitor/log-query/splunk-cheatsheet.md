---
title: Az Azure Log Analytics Splunk |} A Microsoft Docs
description: Segítséget nyújt a felhasználók esetében, akik ismerik Splunk a tanulást a Log Analytics lekérdezési nyelv.
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
ms.openlocfilehash: 61f0cff661c79f994a5b3c20646996f617a31b7e
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52883174"
---
# <a name="splunk-to-log-analytics"></a>A Log Analytics Splunk

Ebből a cikkből segítséget nyújtanak a felhasználók, akik ismerik az Splunk a tanulást a Log Analytics lekérdezési nyelv. Közvetlen összehasonlítások meglévő ismereteit használhatja, a két legfontosabb különbségek megértéséhez, és hasonlóságokat között történik.

## <a name="structure-and-concepts"></a>Struktúra és fogalmak

Az alábbi táblázat összehasonlítja a fogalmakat és adatstruktúrák Splunk és a Log Analytics között.

 | Fogalom  | Splunk | Log Analytics |  Megjegyzés
 | --- | --- | --- | ---
 | Jednotka nasazení  | fürt |  fürt |  A log Analytics lehetővé teszi tetszőleges fürt lekérdezések közötti. Splunk nem létezik. |
 | Felhasználóiadat-gyorsítótárak |  gyűjtők  |  Gyorsítótárazás és -megőrzési szabályok |  Azt szabályozza, az időszak és a gyorsítótárazás az adatok szintjét. Ezzel a beállítással közvetlenül hatással van, a lekérdezések teljesítménye és a költség az üzembe helyezés. |
 | Az adatok logikai partíció  |  index  |  adatbázis  |  Lehetővé teszi az adatok logikai elkülönítését. Mindkét megvalósítását lehetővé teszik, egyesítések és csatlakozás az érintett partíciók között. |
 | Strukturált esemény-metaadatok | – | tábla |  Splunk nem rendelkezik a fogalom a keresési nyelv, az esemény-metaadatok vannak kitéve. A log Analytics rendelkezik egy táblát, oszlopot tartalmaz, amely a fogalmát. Minden egyes esemény példány egy sorra van leképezve. |
 | Adatfelderítési rekordok | esemény | sor |  Csak terminológiai változás. |
 | Rekord adatattribútum | A mező |  Oszlop |  A Log Analyticsben ezzel előre a táblázat szerkezetét részeként. A Splunkban mindegyik esemény rendelkezik a saját mezőket. |
 | Típusok | adattípus |  adattípus |  Log Analytics adattípusok olyan több explicit módon az oszlopok vannak beállítva. Az adattípusok és adattípusok többek között a JSON-támogatás nagyjából készletét dinamikusan képességüket is rendelkezik. |
 | Lekérdezés és a keresés  | keresés | lekérdezés |  Fogalmak alapvetően ugyanazok, a Log Analytics és a Splunk között. |
 | Esemény betöltési ideje | Rendszer pontos ideje | ingestion_time() |  Splunk minden egyes esemény egy időbélyegző az idő, az esemény volt indexelt lesz. A Log Analyticsben meghatározhatja egy szabályzatot, amely elérhetővé teszi a rendszer oszlopot, amely a ingestion_time() funkción keresztül lehet rá hivatkozni ingestion_time nevű. |

## <a name="functions"></a>Functions

Az alábbi táblázat a Log Analytics Splunk funkciók egyenértékű funkcióit határozza meg.

|Splunk | Log Analytics |Megjegyzés
|---|---|---
|strcat | strcat()| (1) |
|felosztás  | split() | (1) |
|Ha     | IFF()   | (1) |
|tonumber | todouble()<br>tolong()<br>toint() | (1) |
|felső<br>Alacsonyabb |ToUpper()<br>ToLower()|(1) |
| cserélje le | replace() | (1)<br> Azt is vegye figyelembe, hogy közben `replace()` három paramétert vesz igénybe a mindkét terméket, a paraméterek különböznek. |
| SUBSTR | Substring() | (1)<br>Azt is vegye figyelembe, hogy a Splunk egy alapú indexek használja. A log Analytics megjegyzi a nulla alapú index. |
| ToLower |  ToLower() | (1) |
| ToUpper | ToUpper() | (1) |
| egyezés | megfelel reguláris kifejezés |  (2)  |
| reguláris kifejezés | megfelel reguláris kifejezés | A Splunkban `regex` operátor. A Log Analytics az összehasonlító operátor. |
| searchmatch | == | A Splunkban `searchmatch` lehetővé teszi, hogy a pontos karakterláncokat keresése.
| véletlenszerű | rand()<br>rand(n) | A Splunk függvény számot ad vissza 2 nulla<sup>31</sup>– 1. A log Analytics 0,0 és 1,0, közötti számot ad vissza, vagy ha egy 0 és n-1 között a megadott paraméter.
| most | now() | (1)
| relative_time | ToTimeSpan() | (1)<br>A Log Analytics a Splunk a egyenértékű relative_time (datetimeVal, offsetVal) datetimeVal + totimespan(offsetVal).<br>Ha például <code>search &#124; eval n=relative_time(now(), "-1d@d")</code> válik <code>...  &#124; extend myTime = now() - totimespan("1d")</code>.

(1) a Splunkban, a függvény meghívása a `eval` operátor. A Log Analyticsben, használatos részeként `extend` vagy `project`.<br>(2) a Splunkban, a függvény meghívása a `eval` operátor. A Log Analyticsben, hogy használható a `where` operátor.


## <a name="operators"></a>Operátorok

A következő szakaszokban eltérő operátorok Splunk és a Log Analytics között a példákat mutatnak be.

> [!NOTE]
> A következő példában a Splunk mező céljából _szabály_ egy táblát az Azure Log Analyticsben, térképek és a Splunk alapértelmezett időbélyeg képezi le a naplók elemzési _ingestion_time()_ oszlop.

### <a name="search"></a>Keresés
A Splunkban, kihagyhatja a `search` kulcsszó és a egy nem jegyzett karakterláncot kell megadni. Az Azure Log Analyticsben el kell indítania minden keresés `find`, nem jegyzett karakterláncra az oszlop nevét, és a keresési érték határolójeles karakterláncnak kell lennie. 

| |  | |
|:---|:---|:---|
| Splunk | **Keresés** | <code>search Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" earliest=-24h</code> |
| Log Analytics | **find** | <code>find Session.Id=="c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time()> ago(24h)</code> |
| | |

### <a name="filter"></a>Szűrés
Egy olyan táblázatos eredményhalmazt, amelyben az Azure Log Analytics lekérdezések kezdő a szűrőt. A Splunkban szűrés az alapértelmezett az aktuális index műveletet. Is `where` operátor Splunk, de nem ajánlott.

| |  | |
|:---|:---|:---|
| Splunk | **Keresés** | <code>Event.Rule="330009.2" Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" _indextime>-24h</code> |
| Log Analytics | **ahol** | <code>Office_Hub_OHubBGTaskError<br>&#124; where Session_Id == "c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time() > ago(24h)</code> |
| | |


### <a name="getting-n-eventsrows-for-inspection"></a>Hálózatfelügyeleti n események/sorok beolvasása 
Az Azure Log Analytics is támogatja a `take` az aliasként `limit`. A Splunkban, ha az eredmények vannak rendezve `head` az első n eredményeket adja vissza. Az Azure Log Analytics a korlát nem mutassa, de található első n sorát adja vissza.

| |  | |
|:---|:---|:---|
| Splunk | **a fő** | <code>Event.Rule=330009.2<br>&#124; head 100</code> |
| Log Analytics | **Korlát** | <code>Office_Hub_OHubBGTaskError<br>&#124; limit 100</code> |
| | |



### <a name="getting-the-first-n-eventsrows-ordered-by-a-fieldcolumn"></a>Bevezetés az első n események/sorok mező vagy oszlop szerint rendezve
Alsó eredményt, a Splunkban használhatja `tail`. Az Azure Log Analyticsben adja meg a rendezési iránya ütközik `asc`.

| |  | |
|:---|:---|:---|
| Splunk | **a fő** |  <code>Event.Rule="330009.2"<br>&#124; sort Event.Sequence<br>&#124; head 20</code> |
| Log Analytics | **felső** | <code>Office_Hub_OHubBGTaskError<br>&#124; top 20 by Event_Sequence</code> |
| | |




### <a name="extending-the-result-set-with-new-fieldscolumns"></a>Az eredmény kiterjesztése új mezőket és oszlopokat tartalmazó beállítása
Splunk is rendelkezik egy `eval` függvény, amely nem az összehasonlítható a `eval` operátor. Mindkét a `eval` a Splunkban operátor és a `extend` az Azure Log Analyticsben operátor csak a skaláris függvények és aritmetikai operátor támogatják.

| |  | |
|:---|:---|:---|
| Splunk | **Próbaverzió** |  <code>Event.Rule=330009.2<br>&#124; eval state= if(Data.Exception = "0", "success", "error")</code> |
| Log Analytics | **Kiterjesztése** | <code>Office_Hub_OHubBGTaskError<br>&#124; extend state = iif(Data_Exception == 0,"success" ,"error")</code> |
| | |


### <a name="rename"></a>Átnevezés 
Az Azure Log Analytics az azonos operátort használja, átnevezése, és hozzon létre egy új mezőt. Splunk van két külön operátorok, `eval` és `rename`.

| |  | |
|:---|:---|:---|
| Splunk | **Nevezze át** |  <code>Event.Rule=330009.2<br>&#124; rename Date.Exception as execption</code> |
| Log Analytics | **Kiterjesztése** | <code>Office_Hub_OHubBGTaskError<br>&#124; extend exception = Date_Exception</code> |
| | |




### <a name="format-resultsprojection"></a>Eredmények/leképezési formátum
Splunk nem úgy tűnik, hasonló az operátornak nincs `project-away`. A felhasználói felület segítségével szűrheti a kötelező mezők.

| |  | |
|:---|:---|:---|
| Splunk | **Tábla** |  <code>Event.Rule=330009.2<br>&#124; table rule, state</code> |
| Log Analytics | **Projekt**<br>**Projekt kötelező** | <code>Office_Hub_OHubBGTaskError<br>&#124; project exception, state</code> |
| | |



### <a name="aggregation"></a>Összesítés
Tekintse meg a [Log Analytics-lekérdezések összesítések](aggregations.md) eltérő összesítési függvényeihez.

| |  | |
|:---|:---|:---|
| Splunk | **Stats** |  <code>search (Rule=120502.*)<br>&#124; stats count by OSEnv, Audience</code> |
| Log Analytics | **Összegzés** | <code>Office_Hub_OHubBGTaskError<br>&#124; summarize count() by App_Platform, Release_Audience</code> |
| | |



### <a name="join"></a>Csatlakozás
Csatlakozás a Splunkban jelentős korlátokkal rendelkezik. A segédlekérdezés korlátja (állítsa be a telepítési konfigurációs fájl) 10000 eredményeket, és ott való csatlakozás csak korlátozott számú változatban érhetők el.

| |  | |
|:---|:---|:---|
| Splunk | **csatlakozás** |  <code>Event.Rule=120103* &#124; stats by Client.Id, Data.Alias | Csatlakozás Client.Id max. = 0 [legkorábbi = 24 órás Event.Rule="150310.0 keresése" Data.Hresult=-2147221040]</code> |
| Log Analytics | **csatlakozás** | <code>cluster("OAriaPPT").database("Office PowerPoint").Office_PowerPoint_PPT_Exceptions<br>&#124; where  Data_Hresult== -2147221040<br>&#124; join kind = inner (Office_System_SystemHealthMetadata<br>&#124; summarize by Client_Id, Data_Alias)on Client_Id</code>   |
| | |



### <a name="sort"></a>Rendezés
A Splunkban, növekvő sorrendben kell használnia a `reverse` operátor. Az Azure Log Analytics is támogatja a meghatározása, hová kerüljön a null értékeket, elején vagy végén.

| |  | |
|:---|:---|:---|
| Splunk | **Rendezés** |  <code>Event.Rule=120103<br>&#124; sort Data.Hresult<br>&#124; reverse</code> |
| Log Analytics | **rendezési** | <code>Office_Hub_OHubBGTaskError<br>&#124; order by Data_Hresult,  desc</code> |
| | |



### <a name="multivalue-expand"></a>Bontsa ki a típushoz
Ez az a Splunk és a Log Analytics egy hasonló operátort.

| |  | |
|:---|:---|:---|
| Splunk | **mvexpand** |  `mvexpand foo` |
| Log Analytics | **mvexpand** | `mvexpand foo` |
| | |




### <a name="results-facets-interesting-fields"></a>Eredmények metszettel, érdekes mezők
A Log Analytics-portálon csak az első oszlop ki van téve. Az összes oszlop az API-n keresztül érhetők el.

| |  | |
|:---|:---|:---|
| Splunk | **Mezők** |  <code>Event.Rule=330009.2<br>&#124; fields App.Version, App.Platform</code> |
| Log Analytics | **aspektusokat** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; facet by App_Branch, App_Version</code> |
| | |




### <a name="de-duplicate"></a>Ismétlődő megszüntetése
Használhat `summarize arg_min()` inkább megfordításához, amelyek a rekord kiválasztott beolvasása.

| |  | |
|:---|:---|:---|
| Splunk | **a deduplikáció** |  <code>Event.Rule=330009.2<br>&#124; dedup device_id sortby -batterylife</code> |
| Log Analytics | **arg_max() összefoglalója** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; summarize arg_max(batterylife, *) by device_id</code> |
| | |




## <a name="next-steps"></a>További lépések

- A lecke meg a [lekérdezések írása a Log Analytics](get-started-queries.md).