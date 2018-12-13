---
title: Az Azure Time Series Insights (előzetes verzió) környezet megtervezése |} A Microsoft Docs
description: Az Azure Time Series Insights (előzetes verzió) környezet megtervezése
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/03/2018
ms.openlocfilehash: c385d10aac01c844f1d4b390c0bb3d064b9befa3
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52878703"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>Az Azure Time Series Insights (előzetes verzió) környezet megtervezése

Ez a cikk ismerteti az ajánlott eljárások és első lépések az Azure Time Series Insights (előzetes verzió) használatával.

## <a name="best-practices-for-planning-and-preparation"></a>Ajánlott eljárások tervezése és előkészítése

Ismerkedés a Time Series Insights (TSI), érdemes a következő megismerése:

* Mi azért kapta a TSI (előzetes verzió) környezet üzembe helyezésekor.
* Mi a **Time Series azonosítók** és **időbélyeg** a tulajdonságok akkor vannak.
* Milyen új **Idősorozat-modell** van, és hogyan hozhat létre saját.
* Hogyan küldhet eseményeket hatékonyan a JSON-ban.  
* A TSI üzleti vész-helyreállítási lehetőségeket.

A Time Series Insights frissítés egy üzleti használatalapú modellt alkalmaz.  További információ a költségek és a kapacitás: [Time Series Insights díjszabása](https://azure.microsoft.com/pricing/details/time-series-insights/).

## <a name="the-time-series-insights-preview-environment"></a>A Time Series Insights (előzetes verzió) környezet

A TSI (előzetes verzió) környezet üzembe helyezésekor, hozzon létre két Azure-erőforrások:

* A TSI (előzetes verzió) környezet
* Az Azure storage általános célú V1 fiók

A termék kell három további elemeket.  Az első egy [Idősorozat-modell](./time-series-insights-update-tsm.md), a második pedig egy [eseményforrás csatlakozik a Time Series Insights](./time-series-insights-how-to-add-an-event-source-iothub.md), és a harmadik pedig [tárfiókba kerülnek az eseményforrás események](./time-series-insights-send-events.md) , amelyek a modell hozzárendelve, és érvényes JSON formátumban vannak.  

## <a name="configure-your-time-series-ids-and-timestamp-properties"></a>A Time Series azonosítók és időbélyegzés tulajdonságainak konfigurálása

Hozzon létre egy új TSI-környezetben, válasszon egy **Time Series azonosító**. Ez tehát az adatok logikai partíció funkcionál. Feljegyzett, győződjön meg arról, hogy a **Time Series azonosítók** készen áll.

> [!IMPORTANT]
> **Time Series azonosítók** vannak **nem módosítható** és **később már nem módosítható**. Minden egyes végső kiválasztása előtt ellenőrizze, és először használja.

Akár választhatja **három** egyedileg megkülönböztetéséhez az erőforrások (3) kulcsokat. Olvassa el a [ajánlott eljárások a Time Series ID kiválasztására vonatkozó](./time-series-insights-update-how-to-id.md) és [tárolási a bejövő és kimenő](./time-series-insights-update-storage-ingress.md) cikkekben további információt.

A **időbélyeg** tulajdonság is nagyon fontos. Ez a tulajdonság is kijelölhet, eseményforrások hozzáadásakor. Minden egyes eseményforrás van egy nem kötelező **időbélyeg** tulajdonságot, amely segítségével nyomon követése eseményforrások idővel. **Időbélyeg** értékek kis-és nagybetűket, és az egyes specifikációjának egyes eseményforrás kell formázni.

> [!TIP]
> Ellenőrizze a eseményforrások formázás és elemzés követelményei.

Ha üresen hagyja, a **eseményt sorba idő** esemény adatforrást használja, az eseménnyel **időbélyeg**. Előzményadatok vagy kötegelt eseményeket küld, ha valószínűleg megtalálja testreszabása a **időbélyeg** tulajdonsághoz való hasznosabb lehet, mint az alapértelmezett **esemény sorba ideje**. További információkért olvassa el [eseményforrások hozzáadása az IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md).  

## <a name="understand-the-time-series-model"></a>Megismerheti a Time Series modell

A TSI-környezet konfigurálhat **Idősorozat-modell**. Az új modell egyszerűen keresése és elemzése IoT-adatot. Úgy valósítja meg a válogatott, a karbantartással és a felderítési bővítést az idősorozat-adatok engedélyezésével és a fogyasztói használatra kész adatkészleteket előkészítése segítségével. A modell **Time Series azonosítók**, amelyek leképezése egy példányt az egyedi erőforráscsoport társítását (más néven típusok) változók és a hierarchiák. Olvassa el az új [Idősorozat-modell](./time-series-insights-update-tsm.md).

A modell a dinamikus, így bármikor építhetők azt. Azonban meg fogja tudni első lépések gyorsabban történik, ha létrehozott és feltöltött, adatok leküldése a TSI megkezdése előtt. A modellek létrehozását, tekintse át a [TSM használata](./time-series-insights-update-how-to-tsm.md) cikk.

Sok felhasználónál várhatóan a **Idősorozat-modell** leképezése egy meglévő eszköz modell vagy a rendszer ERP már megfelel. Azon ügyfelek számára, amelyek nem rendelkeznek egy meglévő modell, egy előre elkészített felhasználói élmény van [megadott](https://github.com/Microsoft/tsiclient) gyors üzembe helyezéséig. Hogyan modell segíthetnek megtekintésével elképzeli a [minta bemutató környezetben](https://insights.timeseries.azure.com/preview/demo).  

## <a name="shaping-your-events"></a>Az események alakításra.

Fontos ellenőrizni a TSI eseményeket küldeni módon. Ideális esetben az események fog kell denormalizált hatékony és jól.

Jó tapasztalatok:

* Metaadatok kell tárolni a **Idősorozat-modell**
* **Time Series módban;**  szolgáltatáspéldány-mezők és az események kell csak a szükséges információk például:
  * **Time Series-azonosító**
  * **Időbélyeg**

Tekintse át a [alakzat eseményeket hogyan](./time-series-insights-send-events.md#json) részletesen ismertető cikket.

## <a name="business-disaster-recovery"></a>Üzleti katasztrófa utáni helyreállítás

Azure-szolgáltatásként TSI szolgáltatás magas rendelkezésre ÁLLÁS redundanciát használatával az Azure-régió szintjén. Semmilyen beállítást nem kell használnia ezen rejlő funkcióit. A Microsoft Azure platform olyan funkciókat, hozzon létre megoldásokat a vész-helyreállítási lehetőségei vagy régiók rendelkezésre állása érdekében is. Ha azt szeretné, adja meg a globális, eszközöket vagy felhasználókat, a régiók közötti magas rendelkezésre ÁLLÁSÚ ezen Azure-beli Vészhelyreállítási funkciók előnyeit. A cikk [Azure üzleti folytonossági műszaki útmutatóját](https://docs.microsoft.com/azure/resiliency/resiliency-technical-guidance) üzletmenet-folytonosság és Vészhelyreállítás az Azure-ban a beépített szolgáltatásait ismerteti. A [vészhelyreállítási](https://docs.microsoft.com/azure/architecture/resiliency/index) helyreállítási és magas rendelkezésre állás az Azure-alkalmazások a tanulmány útmutatást nyújt az architektúra stratégiák az Azure-alkalmazások magas rendelkezésre ÁLLÁS és Vészhelyreállítás megvalósítása.

> [!NOTE]
> Az Azure Time Series Insights nem rendelkezik beépített BCDR.
> Alapértelmezés szerint az Azure Storage, Azure IoT Hub és az Event Hubs rendelkezik visszaállítást biztosít.

További tudnivalók:

* További információ [Azure tárhely-redundancia](https://docs.microsoft.com/azure/storage/common/storage-redundancy).
* További információ [az IoT Hub magas rendelkezésre ÁLLÁSÚ DR](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr).
* További információ [az Eseményközpont-szabályzatok](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr).

Mindazonáltal BCDR igénylő ügyfelek továbbra is valósíthat meg helyreállítási stratégia hozzon létre egy második TSI-környezetben a biztonsági másolat az Azure-régióban. Ügyfelek események küldése a másodlagos környezetbe az elsődleges eseményforrás, a második dedikált fogyasztói csoportot és az adott esemény forrása BCDR irányelveket.

Ehhez a konkrét lépések a következők:

1. Hozzon létre egy környezetet egy második régióba. További információ [TSI környezetek](./time-series-insights-get-started.md).
1. Hozzon létre az eseményforrás második dedikált fogyasztói csoportot, és az esemény-adatforráshoz csatlakozhat az új környezet. Győződjön meg arról, hogy a második, dedikált fogyasztói csoportot kijelölje. Ismerje meg alaposabban a [az IoT Hub dokumentációja](./time-series-insights-how-to-add-an-event-source-iothub.md) vagy a [Event Hub dokumentáció](./time-series-insights-data-access.md).
1. Ha az elsődleges régió kihatással van a vészhelyreállítási incidens alatt, új vonalvezetés műveletek a biztonsági mentési TSI-környezetet.

> [!IMPORTANT]
> * Vegye figyelembe, hogy a késés előfordulhat, hogy észlelt feladatátvétel esetén.
> * Feladatátvétel mometary ugrásszerű az üzenet feldolgozását műveletek foglaltsága okozhat.
> * További információkért tekintse át a [a TSI-ben a késés csökkentése](./time-series-insights-environment-mitigate-latency.md).

## <a name="next-steps"></a>További lépések

Olvassa el a [Azure TSI (előzetes verzió) tárolási a bejövő és kimenő](./time-series-insights-update-storage-ingress.md).

További információ [adatmodellezés](./time-series-insights-update-tsm.md).