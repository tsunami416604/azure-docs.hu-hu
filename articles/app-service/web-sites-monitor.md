---
title: Az Azure App Service-alkalmazások figyelése |} A Microsoft Docs
description: Ismerje meg az Azure App Service-alkalmazások figyelése az Azure portal használatával.
services: app-service
documentationcenter: ''
author: btardif
manager: erikre
editor: ''
ms.assetid: d273da4e-07de-48e0-b99d-4020d84a425e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: byvinyal
ms.openlocfilehash: 6334b4cc50bfa6dca709fdc9d65938f0fec3ad1c
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52956750"
---
# <a name="how-to-monitor-apps-in-azure-app-service"></a>Útmutató: az Azure App Service-alkalmazások figyelése
[App Service-ben](https://go.microsoft.com/fwlink/?LinkId=529714) a beépített monitorozási funkciókat biztosít a [az Azure portal](https://portal.azure.com).
Az Azure Portalon lehetőség tekintse át **kvóták** és **metrikák** az alkalmazás, valamint az App Service-csomag, beállítása **riasztások** és még **méretezése**  metrikák alapján automatikusan.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="understanding-quotas-and-metrics"></a>Understanding kvóták és metrikák
### <a name="quotas"></a>Kvóták
Az App Service-ben üzemeltetett alkalmazások vannak bizonyos *korlátok* az erőforrások használatához. A korlátok által meghatározott a **App Service-csomag** az alkalmazáshoz társított.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Az alkalmazás működik, ha egy **ingyenes** vagy **megosztott** tervezi, akkor korlátait az erőforrásokat, az alkalmazás által meghatározott **kvóták**.

Az alkalmazás működik, ha egy **alapszintű**, **Standard** vagy **prémium** tervezi, majd az erőforrások használatához korlátait-beállításokat a **mérete**(Kicsi, közepes, nagy méretű) és **példányszám** (1, 2, 3,...), a **App Service-csomag**.

**Kvóták** a **ingyenes** vagy **megosztott** -alkalmazásokat:

* **CPU(Short)**
  * Ehhez az alkalmazáshoz egy 5 perces intervallum engedélyezett CPU mennyisége. Ez a kvóta alaphelyzetbe állítja át 5 percenként.
* **CPU(Day)**
  * Ehhez az alkalmazáshoz egy nap alatt engedélyezett Processzor teljes összege. Ez a kvóta alaphelyzetbe állítja a 24 óránként éjfélkor (UTC).
* **Memória**
  * Teljes memóriamennyiség engedélyezett ehhez az alkalmazáshoz.
* **Bandwidth**
  * Teljes összege a kimenő sávszélesség engedélyezett ehhez az alkalmazáshoz egy nap alatt.
    Ez a kvóta alaphelyzetbe állítja a 24 óránként éjfélkor (UTC).
* **Filesystem**
  * Engedélyezett tárterület teljes mérete.

A csak lévő üzemeltetett alkalmazások vonatkozó kvóta **alapszintű**, **Standard**, és **prémium** tervek van **fájlrendszer**.

További információ a konkrét kvóták, korlátozások és a másik App Service Termékváltozatai elérhető funkciók itt található: [Azure-előfizetési szolgáltatási korlátok](../azure-subscription-service-limits.md#app-service-limits)

#### <a name="quota-enforcement"></a>Kvóta kényszerítése
Ha egy alkalmazás nagyobb, mint a **CPU (rövid)**, **CPU (nap)**, vagy **sávszélesség** kvóta majd az alkalmazás le van állítva, amíg a kvóta alaphelyzetbe állítja. Ebben az időszakban az összes bejövő kéréseket eredményez olyan **HTTP 403**.
![][http403]

Ha az alkalmazás **memória** kvótát túllépi, akkor az alkalmazás újraindulna.

Ha a **fájlrendszer** kvótát túllépi, akkor minden írási művelet sikertelen, amely tartalmazza a naplókba írási.

Kvóták növelhető vagy eltávolítja az alkalmazásból az App Service-csomag frissítése.

### <a name="metrics"></a>Mérőszámok
**Metrikák** az alkalmazást, vagy az App Service-csomag viselkedés kapcsolatos adatok megadása.

Az egy **alkalmazás**, a rendelkezésre álló metrikák:

* **Átlagos válaszidő**
  * Az átlagos idő milliszekundumban kiszolgálni a kérelmeket az alkalmazás.
* **Átlagos memória-munkakészlet**
  * Az átlagos MIB-adatbázisból az alkalmazás által használt memória mennyiségét.
* **CPU-idő**
  * Az alkalmazás által felhasznált másodpercek alatt CPU mérete. Ez a metrika kapcsolatos további információkért lásd: [időhányad vs CPU Processzor (%)](#cpu-time-vs-cpu-percentage)
* **Az adatok**
  * A MIB-alkalmazás által használt bejövő sávszélesség mennyiségét.
* **Kimenő adatforgalom**
  * A MIB-alkalmazás által használt kimenő sávszélesség mennyiségét.
* **HTTP 2xx**
  * HTTP-állapotkódot eredményez kérelmek száma > = 200-as, de < 300.
* **HTTP 3xx**
  * HTTP-állapotkódot eredményez kérelmek száma > = 300, de < 400.
* **HTTP 401-es**
  * HTTP 401-es állapotkódot eredményez kérelmek száma.
* **A HTTP 403-as**
  * A HTTP 403-as állapotkódot eredményez kérelmek száma.
* **A HTTP 404-es**
  * A HTTP 404 állapotkódot eredményez kérelmek száma.
* **HTTP 406**
  * HTTP 406 állapotkódot eredményez kérelmek száma.
* **HTTP 4xx**
  * HTTP-állapotkódot eredményez kérelmek száma > 400-as, de < 500 =.
* **HTTP Kiszolgálóhibák**
  * HTTP-állapotkódot eredményez kérelmek száma > = 500-as, de < 600.
* **Memória-Munkakészlet**
  * A MIB-alkalmazás által használt memória jelenlegi mérete.
* **Kérelmek**
  * Függetlenül az eredményül kapott HTTP-állapotkód: kérelmek teljes száma.

Az egy **App Service-csomag**, a rendelkezésre álló metrikák:

> [!NOTE]
> App Service-csomag metrikái csak érhetők el a csomagok **alapszintű**, **Standard**, és **prémium** szinten.
> 
> 

* **Processzorhasználat (%)**
  * Az átlagos CPU-használatának megjelenítése a csomag összes példányán.
* **Memóriahasználat (%)**
  * Az átlagos memóriafelhasználás a csomag összes példányán használt.
* **Az adatok**
  * A csomag összes példányán használt átlagos bejövő sávszélesség.
* **Kimenő adatforgalom**
  * Az átlag, a kimenő sávszélesség-használatot a csomag összes példányán.
* **Lemezvárólista hossza**
  * Átlagos olvasási és írási várólistára került kérelmeket tárolón. Egy magas várólista hossza arra utalhat, hogy egy alkalmazás, amely túlzott mértékű lemez i/o-miatt előfordulhat, hogy lehet lelassul.
* **HTTP-várólista hossza**
  * Az üzenetsor teljesítése előtt szeretném rendelkezett a HTTP-kérések átlagos száma. Egy nagy és egyre nagyobb HTTP-várólista hossza jele csomag nagy terhelés alatt.

### <a name="cpu-time-vs-cpu-percentage"></a>Processzorhasználat idő vs CPU (%)
<!-- To do: Fix Anchor (#CPU-time-vs.-CPU-percentage) -->

Nincsenek két mérőszám, amely a CPU-használat tükrözik. **CPU-idő** és **Processzorhasználat (%)**

**CPU-idő** lévő üzemeltetett alkalmazások hasznos **ingyenes** vagy **megosztott** tervek, mert azok kvóták egyike használja az alkalmazás CPU percben értendő.

**Processzorhasználat (%)** lévő üzemeltetett alkalmazások hasznos **alapszintű**, **standard**, és **prémium** tervek, mert azok kiterjeszthető. Processzorhasználat (%) összes példányra vetítve az összesített használati jól jelzi.

## <a name="metrics-granularity-and-retention-policy"></a>Metrikák és a megőrzéshez
Egy alkalmazás és az app service-csomag metrikáinak naplózza, és a szolgáltatás a következő granularitással és adatmegőrzési szabályzatok szerint összesítve:

* **Perc** granularitási metrikák megmaradnak a **30 óra**
* **Óra** granularitási metrikák megmaradnak a **30 nap**
* **Nap** granularitási metrikák megmaradnak a **30 nap**

## <a name="monitoring-quotas-and-metrics-in-the-azure-portal"></a>Kvóták és metrikák figyelése az Azure Portalon.
Megtekintheti a különböző állapotának **kvóták** és **metrikák** hatással lenne az alkalmazás a [az Azure portal](https://portal.azure.com).

![][quotas]
**Kvóták** találja a beállítások >**kvóták**. A felhasználói felület lehetővé teszi, hogy tekintse át: (1) a kvóták nevét, (2) a átállítási időközt, (3) az aktuális korlát és (4) a jelenlegi érték.

![][metrics]
**Metrikák** elérhető közvetlenül az erőforrás-oldalon. A diagram a testre is szabhatja: (1) **kattintson** , és (2) válassza a **diagram szerkesztése**.
Itt módosíthatja a (3) **időtartomány**, (4) **diagramtípus**, és (5) **metrikák** megjelenítéséhez.  

További információk a metrikákról itt: [Monitorozza a szolgáltatások mérőszámait](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

## <a name="alerts-and-autoscale"></a>Riasztások és az automatikus méretezés
Egy alkalmazás vagy az App Service-csomagra metrikák riasztásokat is lehet csatolja. További tudnivalókért lásd: [riasztási értesítések fogadása](../monitoring-and-diagnostics/insights-alerts-portal.md).

Alapszintű, standard vagy prémium szintű App Service-csomagok támogatási üzemeltetett App Service-alkalmazások **automatikus skálázási**. Automatikus skálázási szabályokat, amelyek az App Service-csomag metrikái figyelése konfigurálását teszi lehetővé. Szabályok növelheti vagy csökkentheti a példányszám igény szerint a további erőforrások rendelkezésre bocsátása. Szabályok segítségével pénzt takaríthat meg, ha az alkalmazás fölösleges van kiépítve. További tudnivalók itt az automatikus skálázás: [méretezése](../monitoring-and-diagnostics/insights-how-to-scale.md) és itt [ajánlott eljárások az Azure Monitor automatikus skálázáshoz](../monitoring-and-diagnostics/insights-autoscale-best-practices.md)

> [!NOTE]
> Ha az Azure App Service-t az Azure-fiók regisztrálása előtt szeretné kipróbálni, ugorjon [Az Azure App Service kipróbálása](https://azure.microsoft.com/try/app-service/) oldalra. Itt azonnal létrehozhat egy ideiglenes, kezdő szintű webalkalmazást az App Service szolgáltatásban. Ehhez nincs szükség bankkártyára, és nem jár kötelezettségekkel.
> 
> 

[fzilla]:https://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:https://go.microsoft.com/fwlink/?LinkID=309169



<!-- Images. -->
[http403]: ./media/web-sites-monitor/http403.png
[quotas]: ./media/web-sites-monitor/quotas.png
[metrics]: ./media/web-sites-monitor/metrics.png
