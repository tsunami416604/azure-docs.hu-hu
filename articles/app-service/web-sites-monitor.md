---
title: "Az Azure App Service-alkalmazások figyelése |} Microsoft Docs"
description: "Útmutató az Azure App Service-alkalmazások figyelése az Azure portál használatával."
services: app-service
documentationcenter: 
author: btardif
manager: erikre
editor: 
ms.assetid: d273da4e-07de-48e0-b99d-4020d84a425e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: byvinyal
ms.openlocfilehash: fdc4329806d416811352d0d4dbc8dd3bce25aa0b
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2018
---
# <a name="how-to-monitor-apps-in-azure-app-service"></a>Útmutató: az Azure App Service-alkalmazások figyelése
[App Service](http://go.microsoft.com/fwlink/?LinkId=529714) a beépített felügyeleti funkciókat biztosítja a [Azure-portálon](https://portal.azure.com).
Az Azure-portál lehetővé teszi a tekintse át **kvóták** és **metrikák** egy alkalmazást, valamint az App Service-csomag, beállítása **riasztások** és még **skálázás**  a metrikák alapján automatikusan.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="understanding-quotas-and-metrics"></a>Understanding kvóták és metrikák
### <a name="quotas"></a>Kvóták
Az App Service-ben üzemeltetett alkalmazások vannak bizonyos *korlátok* használhatnak erőforrásait. Határozza meg a korlátok a **App Service-csomag** az alkalmazással társított alkalmazások.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Ha az alkalmazás a egy **szabad** vagy **megosztott** tervezi, akkor határozza meg az erőforrások, az alkalmazás használati korlátait **kvóták**.

Ha az alkalmazás a egy **alapvető**, **szabványos** vagy **prémium** tervez, használati korlátait használhatják az erőforrásokat-beállításokat, majd a **mérete** (kis, közepes, nagy) és **példány száma** (1, 2, 3,...), a **App Service-csomag**.

**Kvóták** a **szabad** vagy **megosztott** -alkalmazásokat:

* **CPU(Short)**
  * Az alkalmazás egy 5 perces időszakban engedélyezett CPU mennyisége. Ez a kvóta alaphelyzetbe állítása, ötpercenként.
* **CPU(Day)**
  * Ehhez az alkalmazáshoz egy napon belül engedélyezett Processzor teljes mennyisége. Ez a kvóta alaphelyzetbe állítja az UTC idő szerint éjfélkor 24 óránként.
* **Memória**
  * Teljes memóriamennyiség engedélyezett ehhez az alkalmazáshoz.
* **Bandwidth**
  * Kimenő sávszélesség engedélyezett ehhez az alkalmazáshoz egy nap teljes mennyisége.
    Ez a kvóta alaphelyzetbe állítja az UTC idő szerint éjfélkor 24 óránként.
* **Filesystem**
  * Teljes méretű tárolóhely.

A futó alkalmazások alkalmazandó csak kvóta **alapvető**, **szabványos**, és **prémium** tervek van **fájlrendszer**.

További információ a konkrét kvóták, korlátok és funkciók érhetők el a másik App Service termékváltozatok itt található: [Azure előfizetés szolgáltatásra vonatkozó korlátozások](../azure-subscription-service-limits.md#app-service-limits)

#### <a name="quota-enforcement"></a>Kvóta kényszerítése
Ha egy alkalmazás meghaladja a **Processzor (rövid)**, **Processzor (nap)**, vagy **sávszélesség** kvóta majd az alkalmazás leáll, amíg a kvóta alaphelyzetbe állítja. Ebben az időszakban az összes bejövő kérelmek eredményezi egy **HTTP 403**.
![][http403]

Ha az alkalmazás **memória** kvóta túllépése, majd az alkalmazás újraindítása.

Ha a **fájlrendszer** kvóta túllépése, akkor bármelyik írási művelet sikertelen, mert tartalmazza a következő naplókban is bármilyen írás.

Kvóták növelhető vagy eltávolította a alkalmazás az App Service-csomag frissítése.

### <a name="metrics"></a>Mérőszámok
**Metrikák** információval szolgálnak az alkalmazást, illetve az App Service-csomag viselkedését.

Az egy **alkalmazás**, a rendelkezésre álló adatok gyűjtése le van:

* **Átlagos válaszidő**
  * Átlagos ideje az alkalmazásnak, hogy az ms-kérelmek kiszolgálását.
* **Munkakészlet átlagos memória**
  * A MIB-adatbázisból az alkalmazás által használt memória átlagos mennyisége.
* **CPU-idő**
  * Az alkalmazás által felhasznált másodpercben CPU összege. Ez a metrika kapcsolatos további információkért lásd: [CPU-időt és a CPU százaléka](#cpu-time-vs-cpu-percentage)
* **Az adatok**
  * A MIB-adatbázisból az alkalmazás által használt bejövő sávszélesség mennyiségét.
* **Kimenő adatforgalmat**
  * A MIB-adatbázisból az alkalmazás által felhasznált kimenő sávszélesség mennyiségét.
* **HTTP 2xx**
  * HTTP-állapotkódot eredményez kérelmek száma > = 200, de < 300.
* **HTTP 3xx**
  * HTTP-állapotkódot eredményez kérelmek száma > = < 400, de 300.
* **HTTP 401-es**
  * HTTP 401-es állapotkód eredményezve kérelmek száma.
* **A HTTP 403**
  * 403-as HTTP-állapotkódot eredményez kérelmek száma.
* **HTTP 404-es**
  * HTTP 404 állapotkódot eredményez kérelmek száma.
* **406 http**
  * HTTP 406-állapotkódot eredményez kérelmek száma.
* **HTTP 4xx**
  * HTTP-állapotkódot eredményez kérelmek száma > = 400, de a < 500.
* **HTTP-kiszolgáló hibák**
  * HTTP-állapotkódot eredményez kérelmek száma > = 500, de a < 600.
* **Memória munkakészlete**
  * A MIB-adatbázisból az alkalmazás által használt memória pillanatnyi mérete.
* **kérések**
  * Az eredményül kapott HTTP-állapotkód függetlenül kérelmek teljes száma.

Az egy **App Service-csomag**, a rendelkezésre álló adatok gyűjtése le van:

> [!NOTE]
> App Service-csomag metrikái csak érhetők el a tervek **alapvető**, **szabványos**, és **prémium** rétegek.
> 
> 

* **Processzor**
  * Az átlagos CPU, a csomag összes példányán keresztül használt.
* **Memóriahasználat (%)**
  * A csomag összes példányán keresztül használt átlagos memória.
* **Az adatok**
  * A csomag összes példányán keresztül használt átlagos bejövő sávszélesség.
* **Kimenő adatforgalmat**
  * A csomag összes példányán keresztül használt sávszélesség kimenő átlaga.
* **Lemezvárólista hossza**
  * Átlagos olvasási és írási váró kérelmek tárolón. A magas Lemezvárólista hossza arra utal, hogy egy alkalmazás, amely túlzott lemezes i/o-miatt előfordulhat, hogy lehet lelassult.
* **HTTP-várólista hossza**
  * Volna, hogy a várólista előtt teljesítését HTTP-kérelmek átlagos száma. Egy magas vagy növekvő HTTP-várólista hossza a jelenség a terv túl nagy terhelés alatt.

### <a name="cpu-time-vs-cpu-percentage"></a>CPU-időt és a CPU százaléka
<!-- To do: Fix Anchor (#CPU-time-vs.-CPU-percentage) -->

Nincsenek két metrikákat, amelyek CPU-használat tükrözik. **CPU-idő** és **processzor**

**CPU-idő** akkor hasznos, az üzemeltetett alkalmazások **szabad** vagy **megosztott** tervek, mert azok közül az alkalmazás által használt CPU percben értendő.

**Processzor** akkor hasznos, az üzemeltetett alkalmazások **alapvető**, **szabványos**, és **prémium** tervek, mert azok kiterjeszthető. Processzor jól jelzi az általános használat csomagbeli összes példányra.

## <a name="metrics-granularity-and-retention-policy"></a>Metrikák Granularitási és az adatmegőrzési házirend
Az alkalmazás és az app service-csomag metrikáinak naplózza, és a szolgáltatás a következő Granularitás van, és az adatmegőrzési összesítve:

* **Perc** granularitási metrikák megmaradnak a **30 üzemideje (óra)**
* **Óra** granularitási metrikák megmaradnak a **30 napban**
* **Nap** granularitási metrikák megmaradnak a **30 napban**

## <a name="monitoring-quotas-and-metrics-in-the-azure-portal"></a>Ellenőrzése kvóták és metrikákat az Azure-portálon.
Megtekintheti a különböző állapotának **kvóták** és **metrikák** érintő az alkalmazás a [Azure-portálon](https://portal.azure.com).

![][quotas]
**Kvóták** található a beállítások >**kvóták**. A UX lehetővé teszi, hogy tekintse át: (1) a kvóták nevét, (2) a átállítási időközt, (3) a jelenlegi korlátozását és (4) aktuális értékét.

![][metrics]
**Metrikák** elérhető közvetlenül az erőforrás-lapon. Testre szabhatja a diagram: (1) **kattintson** , és válassza ki (2) **diagram szerkesztése**.
Itt módosíthatja a (3) **időtartománynak**, (4) **diagramtípus**, és (5) **metrikák** megjelenítéséhez.  

Ön talál további információt itt metrikák: [szolgáltatás metrikát](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

## <a name="alerts-and-autoscale"></a>Riasztások és automatikus skálázás
Az alkalmazás vagy az App Service-csomag metrikáinak is csatlakoztatnia a riasztásokat. További információkért lásd: [riasztási értesítéseket](../monitoring-and-diagnostics/insights-alerts-portal.md).

Alapszintű, standard vagy prémium szintű App Service-csomagok támogatása az üzemeltetett App Service-alkalmazásokhoz **automatikus skálázás**. Automatikus skálázás lehetővé teszi az App Service-csomag metrikái figyelő szabályok konfigurálását. Szabályok növelheti vagy csökkentheti a példányok száma, így további erőforrások, igény szerint. Szabályok segítségével költségtakarékosabb munkavégzésben, amikor az alkalmazás túlzott ki van építve. Automatikus méretezésének Itt többet is megtudhat: [méretezési hogyan](../monitoring-and-diagnostics/insights-how-to-scale.md) és itt [ajánlott eljárások az Azure a figyelő automatikus skálázás](../monitoring-and-diagnostics/insights-autoscale-best-practices.md)

> [!NOTE]
> Ha az Azure App Service-t az Azure-fiók regisztrálása előtt szeretné kipróbálni, ugorjon [Az Azure App Service kipróbálása](https://azure.microsoft.com/try/app-service/) oldalra. Itt azonnal létrehozhat egy ideiglenes, kezdő szintű webalkalmazást az App Service szolgáltatásban. Ehhez nincs szükség bankkártyára, és nem jár kötelezettségekkel.
> 
> 

[fzilla]:http://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:http://go.microsoft.com/fwlink/?LinkID=309169



<!-- Images. -->
[http403]: ./media/web-sites-monitor/http403.png
[quotas]: ./media/web-sites-monitor/quotas.png
[metrics]: ./media/web-sites-monitor/metrics.png
