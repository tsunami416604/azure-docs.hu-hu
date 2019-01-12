---
title: – Az Azure App Service-alkalmazások figyelése |} A Microsoft Docs
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
ms.date: 01/11/2019
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: 3a49b30caa087295bbdcffe8762796fdc92f154b
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2019
ms.locfileid: "54247257"
---
# <a name="monitor-apps-in-azure-app-service"></a>Alkalmazások figyelése az Azure App Service-ben
[Az Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) beépített monitorozási funkciókat biztosít a webes, mobil háttérrendszerek, és az API-alkalmazások a [az Azure portal](https://portal.azure.com).

Az Azure Portalon tekintheti át *kvóták* és *metrikák* egy alkalmazáshoz, tekintse át az App Service-csomagot, és automatikus beállítása *riasztások* és *méretezése* , amely a metrikák alapulnak.

## <a name="understand-quotas"></a>Kvóták ismertetése

Az App Service-ben üzemeltetett alkalmazások is használhatják az erőforrásokat a bizonyos korlátozások vonatkoznak rájuk. A korlátok az alkalmazáshoz tartozó App Service-csomag határozza meg.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Ha az alkalmazás üzemel egy *ingyenes* vagy *megosztott* kvóták által meghatározott csomag, az erőforrásokat, az alkalmazás használhassa-korlátait.

Ha az alkalmazás üzemel egy *alapszintű*, *Standard*, vagy *prémium* csomag, az erőforrásokat, amelyekkel korlátait-beállításokat a *mérete* () Kis, közepes, nagy méretű) és *példányszám* (1, 2, 3, és így tovább), az App Service-csomag.

Az ingyenes és közös alkalmazások kvóták a következők:

| Kvóta | Leírás |
| --- | --- |
| **Processzor (rövid)** | A Processzor egy 5 perces időszakban az alkalmazás engedélyezett mennyisége. Ez a kvóta alaphelyzetbe állítja át 5 percenként. |
| **Processzor (nap)** | Az alkalmazás egy nap alatt engedélyezett Processzor teljes mennyisége. Ez a kvóta alaphelyzetbe állítja a 24 óránként éjfélkor (UTC). |
| **Memória** | A teljes memóriamennyiséget engedélyezett ehhez az alkalmazáshoz. |
| **Bandwidth** | A kimenő sávszélesség egy nap alatt az alkalmazás engedélyezett mennyisége. Ez a kvóta alaphelyzetbe állítja a 24 óránként éjfélkor (UTC). |
| **Filesystem** | Az engedélyezett tárolási mennyisége. |

A csak a tárolt alkalmazások alkalmazandó kvóta *alapszintű*, *Standard*, és *prémium* tervek fájlrendszer.

Az adott kvóták, korlátozások és a különféle App Service-termékváltozatok elérhető funkciók kapcsolatos további információkért lásd: [Azure-előfizetési szolgáltatási korlátok](../azure-subscription-service-limits.md#app-service-limits).

### <a name="quota-enforcement"></a>Kvóta kényszerítése

Ha egy alkalmazás nagyobb, mint a *CPU (rövid)*, *CPU (nap)*, vagy *sávszélesség* kvótát, az alkalmazás le van állítva, amíg a kvóta alaphelyzetbe állítja. Ebben az időszakban minden bejövő kérelem HTTP 403-as hibát eredményez.

![403-as hibaüzenet][http403]

Ha az alkalmazás a memóriakvóta eltelik, az alkalmazás újraindítása.

A fájlrendszer kvóta túllépése esetén minden írási művelet meghiúsul. Az írási művelet hibák közé tartozik a naplókba írási.

Növelheti vagy kvóták eltávolítása az alkalmazás az App Service-csomag frissítése.

## <a name="understand-metrics"></a>Metrikák ismertetése

Metrikák ismertetik az alkalmazás vagy az App Service-csomag viselkedését.

Az alkalmazás a rendelkezésre álló metrikák a következők:

| Metrika | Leírás |
| --- | --- |
| **Átlagos válaszidő** | Az átlagos idő ezredmásodpercben kiszolgálni a kérelmeket, az alkalmazás. |
| **Átlagos memória-munkakészlet** | Az átlagos felhasznált memóriamennyiség (MB) (MiB), az alkalmazás által. |
| **CPU-idő** | Másodpercek alatt az alkalmazás által felhasznált CPU mennyisége. Ez a metrika kapcsolatos további információkért lásd: [idő vs CPU processzorhasználat](#cpu-time-vs-cpu-percentage). |
| **Az adatok** | Az alkalmazást, a MiB által felhasznált bejövő sávszélesség mennyiségét. |
| **Kimenő adatforgalom** | Az alkalmazást, a MiB használja fel a kimenő sávszélesség mennyiségét. |
| **HTTP 2xx** | Egy HTTP-állapotkód: legalább 200-as, de < 300 eredményező kérések száma. |
| **HTTP 3xx** | Eredményez olyan HTTP-állapotkód: legalább 300, de < 400 kérelmek száma. |
| **HTTP 401-es** | HTTP 401-es állapotkódot eredményez kérelmek száma. |
| **A HTTP 403-as** | A HTTP 403-as állapotkódot eredményez kérelmek száma. |
| **A HTTP 404-es** | A HTTP 404 állapotkódot eredményez kérelmek száma. |
| **HTTP 406** | HTTP 406 állapotkódot eredményez kérelmek száma. |
| **HTTP 4xx** | Egy HTTP-állapotkód: legalább 400-as, de < 500 eredményező kérések száma. |
| **HTTP Kiszolgálóhibák** | Egy HTTP-állapotkód: legalább 500-as, de < 600 eredményező kérések száma. |
| **Memória-Munkakészlet** | Az aktuális MIB, az alkalmazás által használt memória mennyisége. |
| **Kérelmek** | Függetlenül az eredményül kapott HTTP-állapotkód: kérelmek teljes száma. |

App Service-csomag a rendelkezésre álló metrikák a következők:

> [!NOTE]
> App Service-csomag metrikái csak a csomagok érhetők *alapszintű*, *Standard*, és *prémium* szinten.
> 

| Metrika | Leírás |
| --- | --- |
| **Processzorhasználat (%)** | Az átlagos CPU-használatának megjelenítése a csomag összes példányán. |
| **Memóriahasználat (%)** | Az átlagos memóriafelhasználás a csomag összes példányán használt. |
| **Az adatok** | A csomag összes példányán használt átlagos bejövő sávszélesség. |
| **Kimenő adatforgalom** | Az átlag, a kimenő sávszélesség-használatot a csomag összes példányán. |
| **Lemezvárólista hossza** | Átlagos olvasási és írási várólistára került kérelmeket tárolón. Egy magas várólista hossza arra utalhat, hogy egy alkalmazást, amely túlzott mértékű lemez i/o-miatt előfordulhat, hogy lehet lelassul. |
| **HTTP-várólista hossza** | Az üzenetsor teljesítése előtt szeretném rendelkezett a HTTP-kérések átlagos száma. Egy nagy és egyre nagyobb HTTP-várólista hossza jele csomag nagy terhelés alatt. |

### <a name="cpu-time-vs-cpu-percentage"></a>Processzorhasználat idő vs CPU (%)
<!-- To do: Fix Anchor (#CPU-time-vs.-CPU-percentage) -->

Nincsenek két mérőszám, amely tükrözi a CPU-használat:

**CPU-idő**: Az alkalmazások hasznos üzemeltetett ingyenes vagy megosztott tervek, mert a kvóták egyike használja az alkalmazás CPU percben értendő.

**Processzorhasználat (%)**: Akkor hasznos, mert azok kiterjeszthető az alapszintű, Standard és prémium csomagokban üzemeltetett alkalmazásokhoz. Processzorhasználat (%) összes példányra vetítve az összesített használati jól jelzi.

## <a name="metrics-granularity-and-retention-policy"></a>Metrikák és a megőrzési házirend
Egy alkalmazás és az app service-csomag metrikáinak naplózza, és a szolgáltatás a következő granularitással és adatmegőrzési szabályzatok szerint összesítve:

* **Perc** granularitási metrikák 30 óra megmaradnak.
* **Óra** granularitási metrikák 30 napig megőrződnek.
* **Nap** granularitási metrikák 30 napig megőrződnek.

## <a name="monitoring-quotas-and-metrics-in-the-azure-portal"></a>Kvóták és az Azure Portalon metrikák figyelése
A különböző kvóták és a egy alkalmazást érintő mérőszámokat állapotának áttekintéséhez keresse fel a [az Azure portal](https://portal.azure.com).

![Az Azure Portalon kvóták diagram][quotas]

Kvóták megkereséséhez válassza ki a **beállítások** > **kvóták**. A diagramra megtekintheti: 
1. A kvóta neve.
1. A átállítási időközt.
1. A jelenlegi korlátozását.
1. Az aktuális érték.

![Az Azure Portalon metrikadiagram][metrics] metrikák közvetlenül elérheti a **erőforrás** lap. A diagram testreszabása: 
1. Válassza ki a diagramon.
1. Válassza ki **diagram szerkesztése**.
1. Szerkessze a **időtartomány**.
1. Szerkessze a **diagramtípus**.
1. Szerkessze a megjeleníteni kívánt metrikák.  

További információk a metrikákról, lásd: [Monitorozza a szolgáltatások mérőszámait](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

## <a name="alerts-and-autoscale"></a>Riasztások és az automatikus méretezés
Egy alkalmazás vagy az App Service-csomag metrikáinak riasztásokat is lehet csatolja. További információ: [Receive alert notifications](../monitoring-and-diagnostics/insights-alerts-portal.md) (Riasztások fogadása).

App Service-ben a Basic, Standard vagy prémium szintű App Service-ben csomagok támogatási automatikus skálázási üzemeltetett alkalmazások. Az Automatikus méretezéssel beállíthatja a szabályokat, amelyek az App Service-csomag metrikái figyelése. Szabályok növelheti vagy csökkentheti a példányok száma, ami lehetővé teszi további erőforrások, igény szerint. Szabályok segítségével pénzt takaríthat meg, ha az alkalmazás fölösleges van kiépítve.

Automatikus skálázási kapcsolatos további információkért lásd: [méretezése](../monitoring-and-diagnostics/insights-how-to-scale.md) és [ajánlott eljárások az Azure Monitor automatikus skálázásához](../azure-monitor/platform/autoscale-best-practices.md).

[fzilla]:https://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:https://go.microsoft.com/fwlink/?LinkID=309169

<!-- Images. -->
[http403]: ./media/web-sites-monitor/http403.png
[quotas]: ./media/web-sites-monitor/quotas.png
[metrics]: ./media/web-sites-monitor/metrics.png