---
title: Alkalmazások figyelése – Azure App Service | Microsoft Docs
description: Megtudhatja, hogyan figyelheti Azure App Service alkalmazásait a Azure Portal használatával.
services: app-service
documentationcenter: ''
author: btardif
manager: erikre
editor: ''
ms.assetid: d273da4e-07de-48e0-b99d-4020d84a425e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/11/2019
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: 7edff127bb981db985bebb41740744f325306bc8
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74546197"
---
# <a name="monitor-apps-in-azure-app-service"></a>Alkalmazások figyelése Azure App Service
A [Azure app Service](https://go.microsoft.com/fwlink/?LinkId=529714) beépített figyelési funkciókat biztosít a webalkalmazásokhoz, a mobil háttér-és API-alkalmazásokhoz a [Azure Portal](https://portal.azure.com).

A Azure Portalban áttekintheti az alkalmazások *kvótáit* és *mérőszámait* , valamint app Service megtervezheti az alkalmazásokat, és beállíthatja a *riasztásokat* és az *automatikus skálázást* is.

## <a name="understand-quotas"></a>A kvóták ismertetése

A App Serviceban üzemeltetett alkalmazásokra bizonyos korlátozások vonatkoznak a használható erőforrásokra. A korlátokat az alkalmazáshoz társított App Service-csomag határozza meg.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Ha az alkalmazás *ingyenes* vagy *közös* csomagban található, az alkalmazás által használható erőforrások korlátai a kvóták alapján definiálhatók.

Ha az alkalmazás *Alapszintű*, standard vagy *prémium* *szintű*csomagban található, akkor a felhasználható erőforrásokra vonatkozó korlátokat a app Service csomag *mérete* (kis, közepes, nagy) és a *Példányszám* (1, 2, 3 stb.) határozza meg.

Az ingyenes vagy a megosztott alkalmazások kvótái a következők:

| Kvóta | Leírás |
| --- | --- |
| **CPU (rövid)** | Az alkalmazás számára engedélyezett CPU-mennyiség 5 perces intervallumban. Ez a kvóta öt percenként alaphelyzetbe áll. |
| **CPU (nap)** | Az alkalmazáshoz egy nap alatt engedélyezett CPU teljes mennyisége. Ez a kvóta 24 óránként visszaállítja az UTC-t éjfélkor. |
| **Memória** | Az alkalmazás számára engedélyezett memória teljes mennyisége. |
| **Sávszélesség** | Az alkalmazáshoz egy nap alatt engedélyezett kimenő sávszélesség teljes mennyisége. Ez a kvóta 24 óránként visszaállítja az UTC-t éjfélkor. |
| **Fájlrendszer** | Az engedélyezett tárterület teljes mennyisége. |

Az *Alapszintű*, standard és *prémium* *szintű*alkalmazások esetében az egyetlen kvóta a fájlrendszer.

További információ a különböző App Service SKU-ban elérhető konkrét kvótákkal, korlátozásokkal és szolgáltatásokkal kapcsolatban: [Azure-előfizetési szolgáltatás korlátai](../azure-subscription-service-limits.md#app-service-limits).

### <a name="quota-enforcement"></a>Kvóta kényszerítése

Ha egy alkalmazás meghaladja a *CPU-t (röviden)* , a CPU-t *(nap)* vagy a *sávszélesség* -kvótát, az alkalmazás a kvóta alaphelyzetbe állítása után leáll. Ebben az időszakban az összes bejövő kérelem HTTP 403-es hibát eredményez.

![403-hibaüzenet][http403]

Ha túllépte az alkalmazás memóriájának kvótáját, az alkalmazás újraindul.

Ha túllépte a fájlrendszer kvótáját, az írási műveletek meghiúsulnak. Az írási művelet hibái a naplókba való írásokat is tartalmazzák.

Az App Service terv frissítésével növelheti vagy eltávolíthatja az alkalmazás kvótáit.

## <a name="understand-metrics"></a>A metrikák ismertetése

> [!NOTE]
> A **fájlrendszer használata** egy új metrika, amely globálisan zajlik, és nem számítunk fel adatokat, kivéve, ha Ön rendelkezik a privát előzetes verzióra vonatkozó engedélyezési listával.
> 

A metrikák az alkalmazással vagy a App Service terv működésével kapcsolatos információkat biztosítanak.

Egy alkalmazás esetében az elérhető metrikák a következők:

| Metrika | Leírás |
| --- | --- |
| **Átlagos válaszidő** | Az alkalmazásnak a kérelmek kiszolgálásához szükséges átlagos ideje másodpercben. |
| **Memória átlagos munkakészlete** | Az alkalmazás által használt memória átlagos mérete (MB) (MiB). |
| **Kapcsolatok** | A homokozóban meglévő kötött szoftvercsatornák (w3wp. exe és annak alárendelt folyamatai) száma.  A kötött szoftvercsatorna a kötési ()/Connect () API-k meghívásával jön létre, és addig marad, amíg az említett szoftvercsatorna le nem zárul a CloseHandle függvény hívásakor ()/closesocket () használatával. |
| **CPU-idő** | Az alkalmazás által felhasznált CPU mennyisége másodpercben. További információ erről a metrikáról: [CPU Time vs CPU százalék](#cpu-time-vs-cpu-percentage). |
| **Aktuális szerelvények** | Az alkalmazás összes alkalmazástartományok betöltött szerelvények aktuális száma. |
| **A-ben tárolt adatértékek** | Az alkalmazás által felhasznált bejövő sávszélesség mennyisége a MiB-ben. |
| **Kimenő adatvesztés** | Az alkalmazás által felhasznált kimenő sávszélesség mennyisége a MiB-ben. |
| **Fájlrendszer használata** | Az alkalmazás által felhasznált fájlrendszerbeli kvóta százalékaránya. |
| **0. generációs Garbage-gyűjtemények** | Az alkalmazási folyamat kezdete óta a 0. generációs objektumok számát gyűjti a rendszer. A magasabb generációs GCs közé tartozik az összes alacsonyabb generációs GCs.|
| **1. generációs Garbage gyűjtemények** | Azon alkalmak száma, amikor az 1. generációs objektumok az alkalmazási folyamat kezdete óta beszedett szemetet gyűjtenek. A magasabb generációs GCs közé tartozik az összes alacsonyabb generációs GCs.|
| **2. generációs Garbage gyűjtemények** | A 2. generációs objektumok száma az alkalmazási folyamat kezdete óta beszedett szemetet.|
| **Kezelők száma** | Az alkalmazási folyamat által jelenleg megnyitott kezelők teljes száma.|
| **Http-2xx** | A kérelmek száma a következő HTTP-állapotkódot eredményezi: ≥ 200, de < 300. |
| **Http-3xx** | A kérelmek száma a következő HTTP-állapotkódot eredményezi: ≥ 300, de < 400. |
| **HTTP 401** | A HTTP 401 állapotkódot eredményező kérések száma. |
| **Http 403** | A HTTP 403 állapotkódot eredményező kérések száma. |
| **Http 404** | A HTTP 404 állapotkódot eredményező kérések száma. |
| **Http 406** | A HTTP 406 állapotkódot eredményező kérések száma. |
| **Http-4xx** | A kérelmek száma a következő HTTP-állapotkódot eredményezi: ≥ 400, de < 500. |
| **Http-kiszolgálói hibák** | A kérelmek száma a következő HTTP-állapotkódot eredményezi: ≥ 500, de < 600. |
| **IO – egyéb bájtok másodpercenként** | Az alkalmazási folyamat által az adatokhoz nem kapcsolódó I/O-műveletekhez (például vezérlési műveletekhez) tartozó bájtok kibocsátásának sebessége.|
| **IO egyéb műveletek másodpercenként** | Az alkalmazás folyamata olyan I/O-műveletek kiadására szolgál, amelyek nem olvasási vagy írási műveletek.|
| **IO olvasási bájtok másodpercenként** | Az alkalmazási folyamat által az I/O-műveletek bájtjainak olvasási sebessége.|
| **I/o-olvasási műveletek másodpercenként** | Az alkalmazás folyamata olvasási I/O-műveletekre vonatkozó kiadási sebessége.|
| **IO írási bájtok másodpercenként** | Az a sebesség, amellyel az alkalmazás a bájtok írását az I/O-műveletekhez.|
| **IO írási műveletek másodpercenként** | Az alkalmazás folyamata írási I/O-műveletek kiadásának gyakorisága.|
| **Memória munkakészlete** | Az alkalmazás által a MiB-ben használt memória aktuális mennyisége. |
| **Saját bájtok** | A saját bájtok az alkalmazás által lefoglalt memória jelenlegi mérete (bájtban), amely nem osztható meg más folyamatokkal.|
| **Kérelmek** | A kérelmek teljes száma a létrejövő HTTP-állapotkódtől függetlenül. |
| **Kérelmek az alkalmazás-várólistán** | Az alkalmazás-kérelmek várólistájában lévő kérelmek száma.|
| **Szálak száma** | Az alkalmazás folyamatában jelenleg aktív szálak száma.|
| **Alkalmazás összes tartománya** | Az alkalmazásban betöltött alkalmazástartományok aktuális száma.|
| **Összes kitöltött alkalmazás-tartomány** | Az alkalmazás indítása óta eltávolított alkalmazástartományok teljes száma.|


App Service csomag esetében az elérhető metrikák a következők:

> [!NOTE]
> A App Service terv metrikái csak *Alapszintű*, *standard*és *prémium* szintű csomagokban érhetők el.
> 

| Metrika | Leírás |
| --- | --- |
| **CPU-százalék** | A csomag összes példányán használt átlagos CPU. |
| **Memória százaléka** | A csomag összes példányán használt átlagos memória. |
| **A-ben tárolt adatértékek** | A csomag összes példányán használt átlagos bejövő sávszélesség. |
| **Kimenő adatvesztés** | A csomag összes példányán használt átlagos kimenő sávszélesség. |
| **Lemezvezérlő-várólista hossza** | A tárolás során várólistára vett olvasási és írási kérelmek átlagos száma. A lemez magas várólistájának hossza egy olyan alkalmazásra utal, amely a lemezes I/O-műveletek miatt lelassulhat. |
| **Http-várólista hossza** | Azoknak a HTTP-kéréseknek az átlagos száma, amelyeket a várólistán kellett ülnie a teljesítése előtt. A magas vagy növekvő HTTP-várólista hossza a nagy terhelés alatt álló csomag tünete. |

### <a name="cpu-time-vs-cpu-percentage"></a>CPU-idő vs CPU-százalék
<!-- To do: Fix Anchor (#CPU-time-vs.-CPU-percentage) -->

A CPU-használatot két mérőszám mutatja:

**CPU-idő**: az ingyenes vagy közös tervekben üzemeltetett alkalmazások esetében hasznos, mert az egyik kvóta az alkalmazás által használt CPU-percben van meghatározva.

**CPU-százalék**: az alapszintű, a standard és a prémium csomagokban üzemeltetett alkalmazásokhoz hasznos, mivel azok felskálázásra képesek. A CPU-százalék jól jelzi az összes példány teljes használatát.

## <a name="metrics-granularity-and-retention-policy"></a>Mérőszámok részletességi és adatmegőrzési szabályzata
Az alkalmazások és az App Service-csomag metrikáit a szolgáltatás naplózza és összesíti, a következő részletességi és adatmegőrzési szabályzatokkal:

* A **percenkénti** részletességi metrikák 30 óráig őrződnek meg.
* Az **óra** részletességi metrikáit 30 napig őrzi meg a rendszer.
* A **napi** részletességi metrikákat 30 napig őrzi meg a rendszer.

## <a name="monitoring-quotas-and-metrics-in-the-azure-portal"></a>Kvóták és metrikák figyelése a Azure Portalban
Az alkalmazást érintő különböző kvóták és mérőszámok állapotának áttekintéséhez lépjen a [Azure Portal](https://portal.azure.com).

![Kvóta diagram a Azure Portal][quotas]

A kvóták megkereséséhez válassza a **beállítások** > **kvóták**lehetőséget. A diagramon a következőket tekintheti át: 
1. A kvóta neve.
1. Az alaphelyzetbe állítási időköz.
1. Jelenlegi korlátja.
1. A jelenlegi értéke.

![metrikai diagram a Azure Portal][metrics] a metrikák közvetlenül az **erőforrás** lapról érhetők el. A diagram testreszabása: 
1. Válassza ki a diagramot.
1. Válassza a **diagram szerkesztése**lehetőséget.
1. Módosítsa az **időtartományt**.
1. Szerkessze a **diagram típusát**.
1. Szerkessze a megjeleníteni kívánt metrikákat.  

További információ a metrikákkal kapcsolatban: a [szolgáltatás metrikáinak figyelése](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

## <a name="alerts-and-autoscale"></a>Riasztások és autoskálázás
Az alkalmazásokhoz vagy App Service tervekhez tartozó metrikák összekapcsolható riasztásokkal. További információ: [Receive alert notifications](../monitoring-and-diagnostics/insights-alerts-portal.md) (Riasztások fogadása).

Az alapszintű, standard vagy prémium szintű App Service-csomagokban üzemeltetett alkalmazások támogatják az autoskálázást. App Service Az autoscale használatával olyan szabályokat konfigurálhat, amelyek figyelik a App Service terv mérőszámait. A szabályok növelhetik vagy csökkenthetik a példányszámot, ami igény szerint további erőforrásokat is biztosíthat. A szabályok segítségével pénzt takaríthat meg, ha az alkalmazás túl van kiépítve.

További információ az automatikus méretezésről: az automatikus [skálázás Azure monitorának](../azure-monitor/platform/autoscale-best-practices.md) [skálázása](../monitoring-and-diagnostics/insights-how-to-scale.md) és ajánlott eljárásai.

[fzilla]:https://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:https://go.microsoft.com/fwlink/?LinkID=309169

<!-- Images. -->
[http403]: ./media/web-sites-monitor/http403.png
[quotas]: ./media/web-sites-monitor/quotas.png
[metrics]: ./media/web-sites-monitor/metrics.png