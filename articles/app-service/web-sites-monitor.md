---
title: Alkalmazások figyelése
description: Ismerje meg, hogyan figyelheti az alkalmazásokat az Azure App Service-ben az Azure Portal használatával. Ismerje meg a jelentett kvótákat és mutatókat.
author: btardif
ms.assetid: d273da4e-07de-48e0-b99d-4020d84a425e
ms.topic: article
ms.date: 01/11/2019
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: d84340730391abd7dba4d13202503d37941c09b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79500435"
---
# <a name="monitor-apps-in-azure-app-service"></a>Alkalmazások figyelése az Azure App Service-ben
[Az Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) beépített figyelési funkciókat biztosít a webalkalmazásokhoz, a mobil- és AZ API-alkalmazásokhoz az [Azure Portalon.](https://portal.azure.com)

Az Azure Portalon áttekintheti az alkalmazás- és App Service-csomag *kvótáit* és *metrikáit,* és *riasztásokat* és *automatikus skálázási* szabályokon alapuló metrikákat állíthat be.

## <a name="understand-quotas"></a>A kvóták ismertetése

Az App Service-ben üzemeltetett alkalmazásokra bizonyos korlátozások vonatkoznak az általuk használható erőforrásokra vonatkozóan. A korlátokat az alkalmazáshoz társított App Service-csomag határozza meg.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Ha az alkalmazás *ingyenes* vagy *megosztott* csomagban található, az alkalmazás által használható erőforrások korlátait kvóták határozzák meg.

Ha az alkalmazás egy *alapszintű,* *standard*vagy *prémium szintű* csomagban található, a használható erőforrások korlátait az App Service-csomag *mérete* (kicsi, közepes, nagy) és *példányszáma* (1, 2, 3 és így tovább) határozza meg.

Az ingyenes vagy megosztott alkalmazások kvótái a következők:

| Kvóta | Leírás |
| --- | --- |
| **CPU (rövid)** | Az alkalmazás hoz engedélyezett processzor mennyisége 5 perces időközben. Ez a kvóta ötpercenként visszaáll. |
| **CPU (nap)** | Az alkalmazás hoz egy nap alatt engedélyezett processzor teljes mennyisége. Ez a kvóta 24 óránként, UTC éjfélkor áll vissza. |
| **Memory (Memória)** | Az alkalmazáshoz engedélyezett memória teljes mennyisége. |
| **Sávszélesség** | Az alkalmazás számára egy nap alatt engedélyezett kimenő sávszélesség teljes mennyisége. Ez a kvóta 24 óránként, UTC éjfélkor áll vissza. |
| **Fájlrendszer** | A teljes engedélyezett tárterület. |

Az *Alapszintű*, *Standard*és *Premium* rendszerben üzemeltetett alkalmazásokra vonatkozó egyetlen kvóta a Fájlrendszer.

A különböző App Service-ska-k számára elérhető kvótákról, korlátokról és funkciókról az [Azure Subscription szolgáltatáskorlátok](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)című témakörben talál további információt.

### <a name="quota-enforcement"></a>Kvótaérvényesítés

Ha egy alkalmazás túllépi a *CPU (rövid)*, *a CPU (nap)* vagy a sávszélesség-kvótát, az alkalmazás leáll, amíg a kvóta vissza nem áll. *bandwidth* Ez idő alatt minden bejövő kérelem HTTP 403-as hibát eredményez.

![403-as hibaüzenet][http403]

Ha túllépi az alkalmazás memóriakvótáját, az alkalmazás újraindul.

Ha túllépi a fájlrendszer kvótáját, minden írási művelet sikertelen lesz. Az írási művelet hibái tartalmazzák a naplókba írt írásokat.

Az App Service-csomag frissítésével növelheti vagy eltávolíthatja a kvótákat az alkalmazásból.

## <a name="understand-metrics"></a>A mérőszámok megismerése

> [!NOTE]
> **A fájlrendszer használata** egy új mérőszám, amelyet globálisan vezetnek be, csak akkor várható adat, ha a privát előzetes verzióhoz engedélyezési listán szerepel.
> 

> [!IMPORTANT]
> **Az átlagos válaszidő** elavult, hogy elkerülje a metrikaösszesítésekkel való összetévesztést. A **válaszidő** helyett használja.

A mérőszámok információt nyújtanak az alkalmazás vagy az App Service-csomag viselkedéséről.

Egy alkalmazás esetében az elérhető mutatók a következők:

| Metrika | Leírás |
| --- | --- |
| **Válasz ideje** | Az alkalmazás kéréseinek kiszolgálásához szükséges idő másodpercek alatt. |
| **Átlagos válaszidő (elavult)** | Az alkalmazás átlagos anamminda másodpercben a kérések kiszolgálásához szükséges idő. |
| **Átlagos memóriamunkakészlet** | Az alkalmazás által használt átlagos memóriamennyiség megabájtban (MiB). |
| **Kapcsolatok** | A homokozóban meglévő kötött aljzatok száma (w3wp.exe és gyermekfolyamatai).  A kötött szoftvercsatorna a bind()/connect() API-k hívásával jön létre, és addig marad, amíg az említett szoftvercsatorna be nem zárul a CloseHandle()/closesocket() segítségével. |
| **PROCESSZOR-idő** | Az alkalmazás által felhasznált processzor mennyisége másodpercben. Erről a metrikáról további információt a [PROCESSZOR idő és a PROCESSZOR százalékos aránya](#cpu-time-vs-cpu-percentage)című témakörben talál. |
| **Aktuális szerelvények** | Az alkalmazás összes AppDomains-ében betöltött szerelvények aktuális száma. |
| **Adatok a** | Az alkalmazás által a MiB-ben felhasznált bejövő sávszélesség mennyisége. |
| **Adatok ki** | Az alkalmazás által felhasznált kimenő sávszélesség mennyisége a MiB-ben. |
| **Fájlrendszer használata** | Az alkalmazás által felhasznált fájlrendszerkvóta százalékos aránya. |
| **Gen 0 Szemétgyűjtemények** | Az alkalmazásfolyamat kezdete óta gyűjtött 0 objektumok száma. A magasabb generációs gcs-ek tartalmazzák az összes alacsonyabb generációs gcs-et.|
| **Gen 1 Szemétgyűjtemények** | Az 1. A magasabb generációs gcs-ek tartalmazzák az összes alacsonyabb generációs gcs-et.|
| **Gen 2 Szemétgyűjtemények** | Az alkalmazásfolyamat kezdete óta gyűjtött 2 generációs objektumok száma.|
| **Leírók száma** | Az alkalmazásfolyamat által jelenleg megnyitott leírók teljes száma.|
| **Http 2xx** | A HTTP-állapotkódot eredményező kérelmek száma ≥ 200, de < 300. |
| **Http 3xx** | A HTTP-állapotkódot eredményező kérelmek száma ≥ 300, de < 400. |
| **Http 401** | A HTTP 401 állapotkódot eredményező kérelmek száma. |
| **Http 403** | A HTTP 403 állapotkódot eredményező kérelmek száma. |
| **Http 404** | A HTTP 404 állapotkódot eredményező kérelmek száma. |
| **Http 406** | A HTTP 406 állapotkódot eredményező kérelmek száma. |
| **Http 4xx** | A 400-as, de < 500-as HTTP-állapotkódot eredményező kérelmek száma. |
| **Http Server hibák** | A HTTP-állapotkódot eredményező kérelmek száma ≥ 500, de 600 <. |
| **Egyéb bájtok másodpercenként** | Az a sebesség, amellyel az alkalmazásfolyamat bájtokat bocsát ki olyan I/O-műveletek számára, amelyek nem tartalmaznak adatokat, például vezérlőműveleteket.|
| **Io egyéb műveletek másodpercenként** | Az alkalmazásfolyamat nem olvasási vagy írási műveleteket tartalmazó I/O-műveletek kiadásának sebessége.|
| **Io olvasási bájt másodpercenként** | Az a sebesség, amellyel az alkalmazásfolyamat bájtokat olvas be az I/O-műveletekből.|
| **Io olvasási műveletek másodpercenként** | Az alkalmazásfolyamat olvasási I/O-műveleteinek kiadási sebessége.|
| **Io írási bájt másodpercenként** | Az alkalmazásfolyamat bájtírási sebessége i/o-műveletekbe.|
| **I/O-írási műveletek másodpercenként** | Az a sebesség, amellyel az alkalmazásfolyamat írási I/O-műveleteket bocsát ki.|
| **Memóriamunkakészlet** | Az alkalmazás által a MiB-ben használt memória aktuális mennyisége. |
| **Privát bájtok** | A privát bájtok az alkalmazásfolyamat által lefoglalt memória aktuális mérete bájtban, amely nem osztható meg más folyamatokkal.|
| **Kérelmek** | A kérelmek teljes száma, függetlenül az eredményül kapott HTTP-állapotkódtól. |
| **Kérelmek az alkalmazásvárólistában** | Az alkalmazáskérelem-várólistában lévő kérelmek száma.|
| **Szálak száma** | Az alkalmazásfolyamatban jelenleg aktív szálak száma.|
| **Összes alkalmazástartomány** | Az alkalmazásba betöltött AppDomains-ek aktuális száma.|
| **A kiürített összes alkalmazástartomány** | Az alkalmazás kezdete óta eltávolított AppDomains tartományok teljes száma.|


Az App Service-csomag esetében az elérhető mérőszámok a következők:

> [!NOTE]
> Az App Service-csomag metrikák csak *alapszintű,* *standard*és *prémium* szintű csomagokhoz érhetők el.
> 

| Metrika | Leírás |
| --- | --- |
| **PROCESSZOR százaléka** | A terv összes példányában használt átlagos processzor. |
| **Memória százaléka** | A terv összes példányában használt átlagos memória. |
| **Adatok a** | A terv összes példányában használt átlagos bejövő sávszélesség. |
| **Adatok ki** | A terv összes példányában használt átlagos kimenő sávszélesség. |
| **Lemezvárólista hossza** | A storage-ban várólistára helyezett olvasási és írási kérelmek átlagos száma. A lemezvárólista magas hossza azt jelzi, hogy egy alkalmazás a lemez I/O-ja miatt lassulhat. |
| **Http Várólista hossza** | Azon HTTP-kérelmek átlagos száma, amelyeknek a várólistán kellett ülniük a teljesítés előtt. A http-várólista nagy vagy növekvő hossza a nagy terhelés alatt álló terv tünete. |

### <a name="cpu-time-vs-cpu-percentage"></a>CPU-idő és a PROCESSZOR százaléka
<!-- To do: Fix Anchor (#CPU-time-vs.-CPU-percentage) -->

A processzorhasználatot két mérőszám tükrözi:

**CPU-idő**: Ingyenes vagy megosztott csomagokban tárolt alkalmazások esetén hasznos, mivel az egyik kvótájuk az alkalmazás által használt CPU-percekben van meghatározva.

**CPU-százalék**: Alapszintű, standard és prémium csomagokban tárolt alkalmazások esetén hasznos, mivel ezek horizontálisan kiadhatók. A PROCESSZOR százalékos aránya jól jelzi a teljes használat ot az összes példányban.

## <a name="metrics-granularity-and-retention-policy"></a>Metrikák részletessége és adatmegőrzési szabályzata
Az alkalmazás- és alkalmazásszolgáltatási csomag metrikákat a szolgáltatás naplózza és összesíti, a következő részletes és adatmegőrzési szabályzatokkal:

* **A perc** részletességi mérőszámok 30 órán keresztül maradnak.
* **Az óra** részletességi mutatóit 30 napig őrzi meg a keszerint.
* **A napi** részletességi mutatók 30 napig vannak megőrizve.

## <a name="monitoring-quotas-and-metrics-in-the-azure-portal"></a>Kvóták és metrikák figyelése az Azure Portalon
Az alkalmazást érintő különböző kvóták és metrikák állapotának áttekintéséhez nyissa meg az [Azure Portalt.](https://portal.azure.com)

![Kvóták diagram az Azure Portalon][quotas]

A kvóták megkereséséhez válassza a **Beállítások** > **kvóták lehetőséget.** A diagramon áttekintheti a következőket: 
1. A kvóta neve.
1. A reset intervallum.
1. A jelenlegi határa.
1. A jelenlegi értéke.

![Metrikadiagram][metrics] az Azure portalon a metrikák at közvetlenül az erőforrás áttekintése oldalon **érheti** el. Itt láthatja az alkalmazások néhány mérőszámát ábrázoló diagramokat.

Ha ezekre a diagramokra kattint, akkor a mérőszámok nézetben lehet létrehozni egyéni diagramokat, különböző mutatókat és még sok mást. 

A metrikákról a [Szolgáltatásmetrikák figyelése (Figyel) témakörben olvashat bővebben.](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)

## <a name="alerts-and-autoscale"></a>Riasztások és automatikus skálázás
Egy alkalmazás vagy egy App Service-csomag metrikák is csatlakoztatható riasztásokhoz. További információ: [Receive alert notifications](../monitoring-and-diagnostics/insights-alerts-portal.md) (Riasztások fogadása).

Az Alapszintű vagy magasabb szintű App Service-csomagokban üzemeltetett App Service-alkalmazások támogatják az automatikus skálázást. Az automatikus skálázás segítségével konfigurálhatja az App Service-csomag metrikákat figyelő szabályokat. A szabályok növelhetik vagy csökkenthetik a példányok számát, ami szükség szerint további erőforrásokat biztosíthat. A szabályok abban is segíthetnek, hogy pénzt takarítson meg, ha az alkalmazás túlvan kiépítve.

Az automatikus skálázásról további információt a [Méretezés](../monitoring-and-diagnostics/insights-how-to-scale.md) és [az Azure Monitor automatikus skálázásával kapcsolatos gyakorlati tanácsok](../azure-monitor/platform/autoscale-best-practices.md)című témakörben talál.

[fzilla]:https://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:https://go.microsoft.com/fwlink/?LinkID=309169

<!-- Images. -->
[http403]: ./media/web-sites-monitor/http403.png
[quotas]: ./media/web-sites-monitor/quotas.png
[metrics]: ./media/web-sites-monitor/metrics.png