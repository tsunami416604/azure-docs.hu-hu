---
title: Azure Monitor for VMs (GA) – gyakori kérdések | Microsoft Docs
description: A Azure Monitor for VMs az Azure-ban olyan megoldás, amely az Azure-beli virtuális gép operációs rendszerének állapotát és teljesítményét ötvözi, valamint az alkalmazás-összetevők és a függőségek automatikus felfedését más erőforrásokkal, és leképezi a közötti kommunikációt őket. Ez a cikk a GA kiadásával kapcsolatos gyakori kérdésekre ad választ.
services: azure-monitor
author: mgoedtel
manager: carmonm
editor: ''
ms.service: azure-monitor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/07/2019
ms.author: magoedte
ms.openlocfilehash: cb21d3bed1efc8f6ee7e16a0976ce46d03404983
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72275964"
---
# <a name="azure-monitor-for-vms-generally-available-ga-frequently-asked-questions"></a>Azure Monitor for VMs általánosan elérhető (GA) gyakran ismételt kérdések

Nemrég bejelentettük az [Azure Update](https://azure.microsoft.com/blog/) blogon néhány tervezett változást, amelyek a 2019-es októberben és novemberben történnek. A változásokkal kapcsolatos további részletekért tekintse meg az általános elérhetőségi GYIK-t.

## <a name="updates-for-azure-monitor-for-vms"></a>Azure Monitor for VMs frissítései

Novemberben a Azure Monitor for VMs új verzióját szabadítjuk fel. Azok az ügyfelek, amelyek engedélyezik az Azure-figyelőket a virtuális gépek számára a jelen kiadás után, automatikusan megkapják az új verziót, de a meglévő ügyfeleket már használja Azure Monitor for VMs  Ez a gyakori kérdések és dokumentációnk segítséget nyújt a tömeges frissítés elvégzéséhez, ha több munkaterületen is nagyméretű üzemelő példányok vannak.

Ezzel a frissítéssel Azure Monitor for VMs teljesítmény-adatkészletek már ugyanabban a `InsightsMetrics` táblában vannak tárolva, mint a [tárolók Azure monitor](container-insights-overview.md), és megkönnyíti a két adathalmaz lekérdezését. Azt is megteheti, hogy a korábban használt táblázatban több különböző adatkészletet is tárolhat.  A teljesítmény nézetei az új tábla használatára is frissülni fognak.

A rendszer áthelyezi a kapcsolódási adatkészletek új adattípusait. Az egyéni naplókat használó `ServiceMapComputer_CL` és `ServiceMapProcess_CL` helyen tárolt adat a dedikált adattípusokra lesz áthelyezve, `VMComputer` és `VMProcess` néven.  A dedikált adattípusokra való áttéréssel megadhatjuk ezeket a prioritásokat az adatfeldolgozáshoz, és a tábla sémája minden ügyfélnél szabványosítva lesz.

Tisztában vagyunk azzal, hogy a meglévő ügyfelek frissítésének megkérdezése megzavarja a munkafolyamatot, ezért úgy döntöttünk, hogy most már nyilvános előzetes verzióban, a GA-ban érkezünk meg.

## <a name="what-will-change"></a>Mi lesz a változás?

Jelenleg a Azure Monitor for VMs bevezetési folyamatának befejezésekor engedélyezheti a Service Map megoldást a figyelési adatok tárolásához kiválasztott munkaterületen, majd konfigurálnia kell a virtuális gépekről gyűjtött adatokhoz tartozó teljesítményszámlálókat. A következő hetekben egy új, **VMInsights**nevű megoldást fogunk felszabadítani, amely az adatgyűjtés további funkcióit tartalmazza, valamint egy új helyet az adattároláshoz log Analyticsban.

A munkaterületen lévő teljesítményszámlálók használatának jelenlegi folyamata az adatokat a Log Analytics-ben lévő Perf táblába küldi.  Ez az új megoldás a `InsightsMetrics` nevű táblába küldi az adatAzure Monitor, amelyet a tárolók számára is használ. Ez a táblázatos séma lehetővé teszi, hogy olyan további mérőszámokat és szolgáltatási adatkészleteket tároljon, amelyek nem kompatibilisek a Perf Table formátumával.

## <a name="what-should-i-do-about-the-performance-counters-on-my-workspace-if-i-install-the-vminsights-solution"></a>Mi a teendő a saját munkaterület teljesítményszámlálói esetében, ha a VMInsights-megoldást telepítem?

A Azure Monitor for VMs engedélyezésének jelenlegi módszere a munkaterületen lévő teljesítményszámlálók használatát teszi lehetővé. Az új metódus a `InsightsMetrics` nevű új táblában tárolja ezeket az adattárakat.

Miután frissítettük felhasználói felületét a InsightsMetrics-ban található információk használatára, frissítjük a dokumentációt, és több csatornán keresztül közlik ezt a közleményt, beleértve a szalagcím megjelenítését a Azure Portalban. Ezen a ponton dönthet úgy, hogy [letiltja ezeket a teljesítményszámlálók a](vminsights-enable-overview.md#performance-counters-enabled) munkaterületen, ha már nem kívánja használni őket. 

[!NOTE]
>Ha vannak olyan riasztási szabályok, amelyek hivatkoznak ezekre a számlálóra a Perf táblában, frissítenie kell őket, hogy a `InsightsMetrics` tábla új adataira hivatkozzon.  Tekintse meg a dokumentációt, például a táblázatra hivatkozó log-lekérdezéseket.

Ha úgy dönt, hogy a teljesítményszámlálók engedélyezve vannak, a rendszer a [Log Analytics díjszabás [(https://azure.microsoft.com/pricing/details/monitor/) ) alapján betöltött és megőrzött adatmennyiséget fogja számlázni.

## <a name="how-will-this-change-affect-my-alert-rules"></a>Hogyan befolyásolja ez a változás a riasztási szabályokat?

Ha olyan [naplózási riasztásokat](../platform/alerts-unified-log.md) hozott létre, amelyek lekérdezik a munkaterületen engedélyezett teljesítményszámlálók `Perf` táblázatát, akkor ezeket a szabályokat úgy kell frissíteni, hogy a `InsightsMetrics` táblára hivatkozzon. Ez az útmutató az `ServiceMapComputer_CL` és a `ServiceMapProcess_CL` típusú naplókra is vonatkozik, mivel ezek az adatkészletek `VMComputer` és `VMProcess` táblázatra vannak áthelyezve.

Frissíteni fogjuk ezt a GYIK-t és a dokumentációt, amely tartalmazza például a begyűjtött adathalmazokra vonatkozó naplóbeli keresési riasztási szabályokat.

## <a name="will-there-be-any-changes-to-billing"></a>Lesznek a számlázás változásai?

A számlázás továbbra is a Log Analytics munkaterületen betöltött és megőrzött adatmennyiségen alapul.

A begyűjtött gépi szintű teljesítményadatok azonos méretűek, mint a Perf táblában tárolt adatok mérete, és a megközelítőleg azonos mennyiség kerül kifizetésre.

## <a name="what-if-i-only-want-to-use-service-map"></a>Mi a teendő, ha csak a Service Map szeretném használni?

Ez rendben van.  A rendszer a közelgő frissítéssel kapcsolatos Azure Monitor for VMs megtekintésekor megjelenő utasításokat fogja látni a Azure Portalban. A kiadást követően megjelenik egy üzenet, amely kéri, hogy frissítsen az új verzióra. Ha inkább csak a [Maps](vminsights-maps.md) szolgáltatást szeretné használni, akkor a frissítés lehetőségre kattintva továbbra is használhatja a maps szolgáltatást Azure monitor for VMS és a munkaterületről vagy az irányítópult csempéről elérhető Service Map megoldást.

Ha úgy dönt, hogy manuálisan engedélyezte a teljesítményszámlálók számát a munkaterületen, akkor előfordulhat, hogy az adatok megtekinthetők a Azure Monitor alapján megtekintett teljesítmény-diagramokban. Az új megoldás kiadása után a teljesítmény diagramokat a `InsightsMetrics` táblában tárolt adat lekérdezésére fogjuk frissíteni. Ha az adott táblából származó adatokkal szeretné megtekinteni ezeket a diagramokat, a Azure Monitor for VMs új verziójára kell frissítenie.

A `ServiceMapComputer_CL` és a `ServiceMapProcess_CL` adatáthelyezési módosításai a Service Map és a Azure Monitor for VMs is hatással lesznek, így továbbra is meg kell terveznie ezt a frissítést.

Ha úgy döntött, hogy nem frissíti a **VMInsights** -megoldást, továbbra is biztosítjuk a teljesítmény-munkafüzetek örökölt verzióit, amelyek a `Perf` táblában található adatra hivatkoznak.  

## <a name="will-the-service-map-data-sets-also-be-stored-in-insightsmetrics"></a>A rendszer a Service Map adatkészleteket is tárolja a InsightsMetrics-ben?

Ha mindkét megoldást használja, az adatkészletek nem lesznek duplikálva. Mindkét ajánlat megosztja azokat az adatkészleteket, amelyeket a rendszer `VMComputer` (korábban ServiceMapComputer_CL), `VMProcess` (korábban ServiceMapProcess_CL), `VMConnection` és `VMBoundPort` táblában tárol a gyűjtött térképi adatkészletek tárolására.  

A rendszer a `InsightsMetrics` táblát fogja használni a begyűjtött virtuális gépek, folyamatok és szolgáltatások adatkészletek tárolásához, és csak akkor lesz feltöltve, ha Azure Monitor for VMst használ.

## <a name="will-i-be-double-charged-if-i-have-the-service-map-and-vminsights-solutions-on-my-workspace"></a>Kell-e dupla díjat fizetni, ha a saját munkaterületen a Service Map és a VMInsights-megoldásom van?

Nem, a két megoldás közösen tárolja a `VMComputer` (korábban ServiceMapComputer_CL), `VMProcess` (korábban ServiceMapProcess_CL), `VMConnection` és `VMBoundPort` típusú adathalmazokat.  Ha mindkét megoldás szerepel a munkaterületen, akkor nem számítunk fel díjat.

## <a name="if-i-remove-either-the-service-map-or-vminsights-solution-will-it-remove-my-data-in-log-analytics"></a>Ha eltávolítom vagy a Service Map vagy a VMInsights-megoldás eltávolítja az adataimat Log Analytics?

Nem, a két megoldás közösen tárolja a `VMComputer` (korábban ServiceMapComputer_CL), `VMProcess` (korábban ServiceMapProcess_CL), `VMConnection` és `VMBoundPort` típusú adathalmazokat.  Ha eltávolítja az egyik megoldást, ezek az adatkészletek azt észlelik, hogy még mindig van olyan megoldás, amely az adatmennyiséget használja, és a Log Analytics marad.  Mindkét megoldást el kell távolítania a munkaterületről, hogy az adatok el legyenek távolítva a Log Analytics munkaterületről.

## <a name="when-will-this-update-be-released"></a>Mikor jelenik meg a frissítés?

Várhatóan november közepétől Kiszabadítjuk Azure Monitor for VMs frissítését. Ahogy közelebb kerülünk a kiadási dátumhoz, a frissítések itt jelennek meg, és a Azure Portalban jelennek meg értesítések, ha Azure Monitor navigál.

## <a name="health-feature-to-enter-limited-public-preview"></a>A korlátozott nyilvános előzetes verzió beírására szolgáló Health funkció

Nagy mennyiségű visszajelzést kaptunk az ügyfelektől a VM Health szolgáltatás készletével kapcsolatban.  Ez a funkció nagy érdeklődést mutat a figyelési munkafolyamatok támogatásával kapcsolatban. Azt tervezzük, hogy egy sor módosítást teszünk elérhetővé a funkciók hozzáadásához és a kapott visszajelzések kezeléséhez. Ennek a változásnak az új ügyfelekre gyakorolt hatásának csökkentése érdekében a funkciót korlátozott nyilvános előzetes verzióra helyezzük át.

Ez az átállás október elején kezdődik, és a hónap végéig kell végrehajtania.

Néhány terület a következőkre lesz összpontosítva:

- Az egészségügyi modell és a küszöbértékek nagyobb mértékű szabályozása
- A virtuális gépek hatókörén alkalmazható, méretezési állapotú modellek készítése
- A riasztások platform natív használata az állapotfigyelő riasztási szabályok kezeléséhez
- Az állapotot szélesebb hatókörben, például egy vagy több előfizetésben is megtekintheti.
- Csökkentett késés az állapot-és riasztási értesítések esetében

## <a name="how-do-existing-customers-access-the-health-feature"></a>Hogyan érik el a meglévő ügyfelek az állapotfigyelő funkciót?

Az állapotfigyelő funkciót használó meglévő ügyfelek továbbra is hozzáférhetnek hozzájuk, de nem lesznek elérhetők az új ügyfelek számára.  

A szolgáltatás eléréséhez adja hozzá a következő szolgáltatás jelölőjét @no__t – 0 a portál URL-címéhez [@no__t – 2](https://portal.azure.com). Példa `https://portal.azure.com/?feature.vmhealth=true`.

Ezt a rövid URL-címet is használhatja, amely automatikusan beállítja a szolgáltatás jelölőjét: [https://aka.ms/vmhealthpreview](https://aka.ms/vmhealthpreview).

Meglévő ügyfélként továbbra is használhatja az állapotfigyelő szolgáltatást olyan virtuális gépeken, amelyek a meglévő munkaterület-beállításhoz vannak csatlakoztatva az állapotfigyelő funkcióval.  

## <a name="i-use-vm-health-now-with-one-environment-and-would-like-to-deploy-it-for-a-new-environment"></a>A VM Health-t most egy környezettel használom, és egy új környezetbe szeretném telepíteni

Ha Ön az a felhasználó, aki az állapotfigyelő funkciót használja, és új bevezetésre szeretné használni, lépjen kapcsolatba velünk vminsights@microsoft.com címen, és kérjen útmutatást.

## <a name="next-steps"></a>Következő lépések

A virtuális gépek figyeléséhez szükséges követelmények és módszerek megismeréséhez tekintse át a [Azure monitor for VMS üzembe helyezését ismertető témakört](vminsights-enable-overview.md).
