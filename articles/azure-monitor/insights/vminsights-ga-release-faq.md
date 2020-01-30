---
title: Azure Monitor for VMs (GA) – gyakori kérdések | Microsoft Docs
description: A Azure Monitor for VMs az Azure-ban olyan megoldás, amely az Azure-beli virtuális gép operációs rendszerének állapotát és teljesítményét ötvözi, valamint az alkalmazás-összetevők és a függőségek automatikus felfedését más erőforrásokkal, és leképezi a közötti kommunikációt őket. Ez a cikk a GA kiadásával kapcsolatos gyakori kérdésekre ad választ.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/24/2020
ms.openlocfilehash: 3877632565c1ca2c9a16681e03f8931a94af0599
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2020
ms.locfileid: "76765760"
---
# <a name="azure-monitor-for-vms-generally-available-ga-frequently-asked-questions"></a>Azure Monitor for VMs általánosan elérhető (GA) gyakran ismételt kérdések

Ez az általános elérhetőségi gyakori kérdések a GA-kiadásra való felkészülés során Azure Monitor for VMsban bekövetkező változásokat fedik le. 

## <a name="updates-for-azure-monitor-for-vms"></a>Azure Monitor for VMs frissítései

Megjelent a Azure Monitor for VMs új verziója. A virtuális gépek Azure-figyelőit engedélyező ügyfelek mostantól megkapják az új verziót, de a már meglévő ügyfeleket a rendszer a Azure Monitor for VMs használatával fogja kérni. Ez a gyakori kérdések és a dokumentációnk útmutatást nyújt a nagy kapacitású verziófrissítések elvégzéséhez, ha több munkaterületen is vannak nagyméretű telepítések.

Ezzel a frissítéssel a Azure Monitor for VMs teljesítményadatokat ugyanabban a *InsightsMetrics* -táblában tároljuk, mint a [tárolók Azure monitor](container-insights-overview.md), ami megkönnyíti a két adathalmaz lekérdezését. Azt is megteheti, hogy több különböző adatkészletet is tárolhat, amelyeket nem sikerült tárolni a korábban használt táblázatban. 

A következő héten vagy kettőben a teljesítmény nézetei is frissülni fognak az új táblázat használatára.

Tisztában vagyunk azzal, hogy a meglévő ügyfelek frissítésének megkérdezése megszakítja a munkafolyamatot, ezért most úgy döntöttünk, hogy a GA után még a nyilvános előzetes verzióban is elvégezték.


## <a name="what-is-changing"></a>Mi változik?

Megjelent egy VMInsights nevű új megoldás, amely az adatgyűjtés további funkcióit tartalmazza, valamint egy új helyet az adattároláshoz a Log Analytics munkaterületen. 

A múltban engedélyezte a ServiceMap megoldást a munkaterületen, és a Log Analytics munkaterületen a teljesítményszámlálók beállításával elküldheti az adatait a *perf* táblába. Ez az új megoldás a *InsightsMetrics* nevű táblába küldi az adatokat, amelyet a tárolók Azure monitor is használ. Ez a táblázatos séma lehetővé teszi, hogy olyan további mérőszámokat és szolgáltatási adatkészleteket tároljon, amelyek nem kompatibilisek a *perf* Table formátumával.


## <a name="how-do-i-upgrade"></a>Hogyan frissíteni?
A frissítést igénylő virtuális gépeket a Azure Portal Azure Monitor for VMs első **lépések** lapján találhatja meg. Frissíthet egyetlen virtuális gépet, vagy kijelölhet egyszerre többet is a frissítéshez. A PowerShell használatával történő frissítéshez használja a következő parancsot:

```PowerShell
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName <resource-group-name> -WorkspaceName <workspace-name> -IntelligencePackName "VMInsights" -Enabled $True
```

## <a name="what-should-i-do-about-the-performance-counters-in-my-workspace-if-i-install-the-vminsights-solution"></a>Mi a teendő a saját munkaterület teljesítményszámlálói esetében, ha a VMInsights-megoldást telepítem?

A Azure Monitor for VMs engedélyezésének jelenlegi módszere a munkaterületen lévő teljesítményszámlálók használatát teszi lehetővé. Az új metódus a `InsightsMetrics`nevű új táblában tárolja ezeket az adattárakat.

Miután frissítettük felhasználói felületét a `InsightsMetrics` táblában található információk használatára, frissítjük a dokumentációt, és több csatornán keresztül közlik ezt a hirdetményt, beleértve a szalagcím megjelenítését a Azure Portalban. Ezen a ponton dönthet úgy, hogy [letiltja ezeket a teljesítményszámlálók a](vminsights-enable-overview.md#performance-counters-enabled) munkaterületen, ha már nincs szükség rájuk. 

>[!NOTE]
>Ha vannak olyan riasztási szabályok, amelyek hivatkoznak ezekre a számlálóra a `Perf` táblában, frissítenie kell őket a `InsightsMetrics` táblában tárolt új adatértékekre való hivatkozáshoz. Tekintse meg a dokumentációt, például a táblázatra hivatkozó log-lekérdezéseket.
>

Ha úgy dönt, hogy a teljesítményszámlálók engedélyezve vannak, akkor a `Perf` táblában a [Log Analytics díjszabás [(https://azure.microsoft.com/pricing/details/monitor/) ) alapján betöltött és tárolt adatmennyiség után kell fizetnie.

## <a name="how-will-this-change-affect-my-alert-rules"></a>Hogyan befolyásolja ez a változás a riasztási szabályokat?

Ha olyan [naplózási riasztásokat](../platform/alerts-unified-log.md) hozott létre, amelyek lekérdezik a munkaterületen engedélyezett teljesítményszámlálók `Perf` táblázatát, akkor ezeket a szabályokat úgy kell frissíteni, hogy az `InsightsMetrics` táblára hivatkozzon. Ez az útmutató `ServiceMapComputer_CL` és `ServiceMapProcess_CL`használatával is vonatkozik a naplóbeli keresési szabályokra, mivel ezek az adathalmazok `VMComputer` és `VMProcess` táblákra vannak áthelyezve.

Frissíteni fogjuk ezt a GYIK-t és a dokumentációt, amely tartalmazza például a begyűjtött adathalmazokra vonatkozó naplóbeli keresési riasztási szabályokat.

## <a name="how-will-this-affect-my-bill"></a>Hogyan befolyásolja ez a számla?

A számlázás továbbra is a Log Analytics munkaterületen betöltött és megőrzött adatmennyiségen alapul.

A begyűjtött gépi szintű teljesítményadatok azonos méretűek a `Perf` táblázatban tárolt adatokhoz, és hozzávetőlegesen ugyanannyi összeget fognak fizetni.

## <a name="what-if-i-only-want-to-use-service-map"></a>Mi a teendő, ha csak a Service Map szeretném használni?

Ez rendben van. A rendszer a közelgő frissítéssel kapcsolatos Azure Monitor for VMs megtekintésekor megjelenő utasításokat fogja látni a Azure Portalban. A kiadást követően az új verzióra való frissítést kérő üzenet jelenik meg. Ha inkább csak a [Maps](vminsights-maps.md) szolgáltatást szeretné használni, válassza a nem a frissítés lehetőséget, és folytassa a Azure monitor for VMS Maps szolgáltatás használatát, valamint a munkaterületről vagy az irányítópult csempéről elérhető Service Map megoldást.

Ha úgy dönt, hogy manuálisan engedélyezte a teljesítményszámlálók számát a munkaterületen, akkor előfordulhat, hogy az adatok megtekinthetők a Azure Monitor alapján megtekintett teljesítmény-diagramokban. Az új megoldás kiadása után a `InsightsMetrics` táblában tárolt adat lekérdezéséhez frissíteni fogjuk a teljesítménnyel kapcsolatos diagramokat. Ha az adott táblából származó adatokkal szeretné megtekinteni ezeket a diagramokat, a Azure Monitor for VMs új verziójára kell frissítenie.

Az adatok `ServiceMapComputer_CL` és `ServiceMapProcess_CL` általi áthelyezésének módosításai a Service Map és a Azure Monitor for VMs is hatással lesznek, így továbbra is meg kell terveznie ezt a frissítést.

Ha úgy döntött, hogy nem frissíti a **VMInsights** -megoldást, továbbra is biztosítjuk a teljesítmény-munkafüzetek örökölt verzióit, amelyek a `Perf` táblában lévő adatmennyiségre vonatkoznak.  

## <a name="will-the-service-map-data-sets-also-be-stored-in-insightsmetrics"></a>A rendszer a Service Map adatkészleteket is tárolja a InsightsMetrics-ben?

Ha mindkét megoldást használja, az adatkészletek nem lesznek duplikálva. Mindkét ajánlat megosztja azokat az adatkészleteket, amelyeket a rendszer `VMComputer` (korábban ServiceMapComputer_CL), `VMProcess` (korábban ServiceMapProcess_CL), `VMConnection`és `VMBoundPort` táblákban tárol a gyűjtött térképi adatkészletek tárolásához.  

A `InsightsMetrics` táblázat tárolja a begyűjtött virtuális gépeket, folyamatokat és szolgáltatás-adatkészleteket, és csak akkor lesz feltöltve, ha Azure Monitor for VMst és a VM-elemzési megoldást használja. A Service Map megoldás nem gyűjti és nem tárolja az adatokat a `InsightsMetrics` táblában.

## <a name="will-i-be-double-charged-if-i-have-the-service-map-and-vminsights-solutions-in-my-workspace"></a>Kell-e dupla díjat fizetni, ha a saját munkaterületen van Service Map-és VMInsights-megoldásom?

Nem, a két megoldás közösen tárolja a `VMComputer` (korábban ServiceMapComputer_CL), `VMProcess` (korábban ServiceMapProcess_CL), `VMConnection`és `VMBoundPort`által tárolt Térkép-adatkészleteket. Ha mindkét megoldás szerepel a munkaterületen, akkor nem számítunk fel díjat.

## <a name="if-i-remove-either-the-service-map-or-vminsights-solution-will-it-remove-my-data"></a>Ha eltávolítom vagy a Service Map vagy a VMInsights megoldást, akkor eltávolítja az adataimat?

Nem, a két megoldás közösen tárolja a `VMComputer` (korábban ServiceMapComputer_CL), `VMProcess` (korábban ServiceMapProcess_CL), `VMConnection`és `VMBoundPort`által tárolt Térkép-adatkészleteket. Ha eltávolítja az egyik megoldást, ezek az adatkészletek azt észlelik, hogy még mindig van olyan megoldás, amely az adatmennyiséget használja, és a Log Analytics munkaterületen marad. Mindkét megoldást el kell távolítania a munkaterületről abból a célból, hogy az adatok el legyenek távolítva belőle.

## <a name="when-will-this-update-be-released"></a>Mikor jelenik meg a frissítés?

A Azure Monitor for VMs frissítését várhatóan január 2020-én Kiszabadítjuk. A januári kiadási dátumhoz közeledve a frissítések itt jelennek meg, és a Azure Monitor megnyitásakor bemutatjuk az értesítéseket a Azure Portal.

## <a name="health-feature-is-in-limited-public-preview"></a>A Health funkció korlátozott nyilvános előzetes verzióban érhető el

Nagy mennyiségű visszajelzést kaptunk az ügyfelektől a VM Health szolgáltatás készletével kapcsolatban. Sokan érdeklődnek a funkció iránt, és izgatottan várják, hogy az támogatni tudja majd a monitorozási munkafolyamatokat. Több változást is bevezetünk, új funkciókat adunk hozzá, és a beérkezett visszajelzéseknek is megpróbálunk eleget tenni. 

Ennek a változásnak az új ügyfelekre gyakorolt hatásának csökkentése érdekében a funkciót **korlátozott nyilvános előzetes**verzióra helyezte át. Ez a frissítés 2019 októberében történt.

Azt tervezzük, hogy a Azure Monitor for VMs a GA-ban, a 2020-es verzióban indítsa újra ezt az állapot-szolgáltatást.

## <a name="how-do-existing-customers-access-the-health-feature"></a>Hogyan érik el a meglévő ügyfelek az állapotfigyelő funkciót?

Az állapotfigyelő funkciót használó meglévő ügyfelek továbbra is hozzáférhetnek hozzájuk, de nem lesznek elérhetők az új ügyfelek számára.  

A szolgáltatás eléréséhez adja hozzá a következő szolgáltatás jelölőjét `feature.vmhealth=true` a Azure Portal URL-címhez [https://portal.azure.com](https://portal.azure.com). Példa `https://portal.azure.com/?feature.vmhealth=true`.

Ezt a rövid URL-címet is használhatja, amely automatikusan beállítja a szolgáltatás jelölőjét: [https://aka.ms/vmhealthpreview](https://aka.ms/vmhealthpreview).

Meglévő ügyfélként továbbra is használhatja az állapotfigyelő szolgáltatást olyan virtuális gépeken, amelyek a meglévő munkaterület-beállításhoz vannak csatlakoztatva az állapotfigyelő funkcióval.  

## <a name="i-use-vm-health-now-with-one-environment-and-would-like-to-deploy-it-to-a-new-one"></a>A VM Health-t most egy környezettel használom, és egy újat szeretnék telepíteni

Ha Ön az a felhasználó, aki az állapotfigyelő funkciót használja, és egy új bevezetéshez szeretné használni, lépjen kapcsolatba velünk a vminsights@microsoft.com címen, és kérjen útmutatást.

## <a name="next-steps"></a>Következő lépések

A virtuális gépek figyeléséhez szükséges követelmények és módszerek megismeréséhez tekintse át a [Azure monitor for VMS üzembe helyezését ismertető témakört](vminsights-enable-overview.md).
