---
title: Az Azure Monitor virtuális gépekhez (GA) – gyakori kérdések | Microsoft dokumentumok
description: Az Azure Monitor virtuális gépekhez egy olyan megoldás az Azure-ban, amely egyesíti az Azure VM operációs rendszer állapot- és teljesítményfigyelését, valamint automatikusan feltalálja az alkalmazás-összetevőket és -függőségeket más erőforrásokkal, és leképezi a kommunikációt a Őket. Ez a cikk választ ad a GA-kiadással kapcsolatos gyakori kérdésekre.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/31/2020
ms.openlocfilehash: 3d250ef1aba979be04a44acaf31a3d685f162e37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283888"
---
# <a name="azure-monitor-for-vms-generally-available-ga-frequently-asked-questions"></a>Az Általánosan elérhető virtuális gépek Azure-figyelője (GA) – gyakori kérdések
Ez az általános elérhetőségi GYIK a 2019 negyedik negyedévében és a 2020 első negyedévében a GA-ra készített változtatásokat tartalmazza.

## <a name="updates-for-azure-monitor-for-vms"></a>Frissítések az Azure Monitor szolgáltatáshoz virtuális gépekhez
2020 januárjában a GA-bejelentés előtt kiadtuk az Azure Monitor virtuális gépekhez szolgáltatásának új verzióját. A virtuális gépekhez az Azure Monitort engedélyező ügyfelek most megkapják a ga-verziót, de a 2019 negyedik negyedévétől és korábbi verziójú Azure Monitor szolgáltatás verzióját használó meglévő ügyfelek et a rendszer kérni fogja a frissítésre. Ez a gyakori kérdések útmutatást adnak a nagy méretű frissítés hez, ha több munkaterületen nagy központi telepítéseket végez.


Ezzel a frissítéssel az Azure Monitor virtuális gépek teljesítményadatait ugyanabban az *InsightsMetrics* táblában tárolják, mint az [Azure Monitor tárolókhoz,](container-insights-overview.md)ami megkönnyíti a két adatkészlet lekérdezését. Emellett több különböző adatkészletet is tárolhat, amelyeket nem tudtunk tárolni a korábban használt táblában. 

Teljesítménynézeteink most már az *InsightsMetrics* táblában tárolt adatokat használják.  Ha még nem frissítette a legújabb VMInsights-megoldás használatát a munkaterületen, a diagramok már nem jeleníti meg az információkat.  Az **Első lépések** oldalon az alábbiak szerint frissíthet.


## <a name="what-is-changing"></a>Mi változik?
Kiadtunk egy új megoldást, a VMInsights-et, amely további adatgyűjtési lehetőségeket, valamint egy új helyet tartalmaz az adatok log analytics-munkaterületen való tárolására. 

A múltban engedélyeztük a ServiceMap-megoldást a munkaterületen, és a Log Analytics-munkaterületen beállított teljesítményszámlálókat, hogy az adatokat a *Perf* táblába küldhesjük. Ez az új megoldás elküldi az adatokat egy tábla nevű *InsightsMetrics,* amely az Azure Monitor is használja a tárolók. Ez a táblaséma lehetővé teszi számunkra, hogy tárolja a további metrikák és szolgáltatás adatkészletek, amelyek nem kompatibilisek a *Perf* tábla formátumban.

Frissítettük teljesítménydiagramjainkat az *InsightsMetrics* táblázatban tárolt adatok használatához. Frissíthet az *InsightsMetrics* tábla használatára az **Első lépések** oldalon az alábbiak szerint.


## <a name="how-do-i-upgrade"></a>Hogyan frissíthetek?
Amikor egy Log Analytics-munkaterületet frissít az Azure Monitor legújabb verziójára virtuális gépekre, frissíti a függőségi ügynököt az adott munkaterülethez csatolt virtuális gépek mindegyikén. Minden frissítésre szoruló virtuális gép az Azure Portalon az Azure-portálon az Azure-figyelő virtuális gépekhez az **Első lépések** lapon lesz azonosítva. Ha úgy dönt, hogy egy virtuális gép frissítése, akkor frissíti a munkaterületet, hogy a virtuális gép, valamint bármely más virtuális gépek csatlakozik a munkaterülethez. Egyetlen virtuális gépet vagy több virtuális gépet, erőforráscsoportot vagy előfizetést választhat. 

A következő paranccsal frissíthet egy munkaterületet a PowerShell használatával:

```PowerShell
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName <resource-group-name> -WorkspaceName <workspace-name> -IntelligencePackName "VMInsights" -Enabled $True
```

## <a name="what-should-i-do-about-the-performance-counters-in-my-workspace-if-i-install-the-vminsights-solution"></a>Mi a teendő a teljesítményszámlálókkal a munkaterületen, ha telepítem a VMInsights-megoldást?

Az azure-figyelő virtuális gépekhez használt teljesítményszámlálók a munkaterületen az előző módszer. Az aktuális verzió ezeket az `InsightsMetrics`adatokat egy . Ha már nincs szüksége ezekre a teljesítményszámlálókra, letilthatja ezeket a teljesítményszámlálókat a munkaterületen. 

>[!NOTE]
>Ha a `Perf` táblázatban szereplő számlálókra hivatkozó riasztási szabályokkal rendelkezik, frissítenie kell őket, hogy a `InsightsMetrics` táblában tárolt új adatokra hivatkozzon. Tekintse meg a dokumentációt, például napló lekérdezések, amelyek segítségével hivatkozhat erre a táblára.
>

Ha úgy dönt, hogy a teljesítményszámlálók engedélyezve maradnak, a [Log `Perf` Analytics-díjszabása[(https://azure.microsoft.com/pricing/details/monitor/).

## <a name="how-will-this-change-affect-my-alert-rules"></a>Milyen hatással lesz ez a módosítás a riasztási szabályokra?

Ha olyan [naplóriasztásokat](../platform/alerts-unified-log.md) hozott `Perf` létre, amelyek lekérdezik a táblázat ban engedélyezett teljesítményszámlálókat, `InsightsMetrics` frissítse ezeket a szabályokat, hogy inkább a táblára hivatkozzon. Ez az útmutató a naplókeresési `ServiceMapComputer_CL` `ServiceMapProcess_CL`szabályokra is vonatkozik, `VMComputer` `VMProcess` amelyek a , és mivel ezek az adatkészletek a táblákba és táblákba kerülnek.

Frissítjük ezt a GYIK-et és a dokumentációnkat, hogy tartalmazza az általunk gyűjtött adatkészletekre vonatkozó példa naplókeresési riasztási szabályokat.

## <a name="how-will-this-affect-my-bill"></a>Milyen hatással lesz ez a számlámra?

A számlázás továbbra is a Log Analytics-munkaterületen betöltött és tárolt adatokon alapul.

Az általunk gyűjtött gépszintű teljesítményadatok megegyeznek, a `Perf` táblázatban tárolt adatokhoz hasonló méretűek, és körülbelül ugyanannyiba kerülnek.

## <a name="what-if-i-only-want-to-use-service-map"></a>Mi a teendő, ha csak a Szolgáltatástérképet szeretném használni?

Az jó lesz. A közelgő frissítéssel kapcsolatos azure-figyelő a közelgő frissítéssel kapcsolatos kérések jelennek meg az Azure Portalon. A kiadást követően egy üzenet jelenik meg, amelyben kéri, hogy frissítsen az új verzióra. Ha csak a [Térképek](vminsights-maps.md) funkciót szeretné használni, választhatja azt, hogy nem frissíti a térképfunkciót, és továbbra is használja a Térkép szolgáltatást az Azure Monitor virtuális gépekhez és a munkaterületről vagy az irányítópult csempéről elérhető Szolgáltatástérkép-megoldást.

Ha úgy döntött, hogy manuálisan engedélyezi a teljesítményszámlálókat a munkaterületen, akkor előfordulhat, hogy az Azure Monitorból megtekintett teljesítménydiagramjaink némelyikében megtekintheti az adatokat. Az új megoldás megjelenése után frissítjük a teljesítménydiagramokat a `InsightsMetrics` táblázatban tárolt adatok lekérdezéséhez. Ha szeretné látni az adatokat, hogy a táblázat ezeka diagramok, frissítenie kell az Azure Monitor virtuális gépekre új verziója.

Az adatok áthelyezésének `ServiceMapComputer_CL` `ServiceMapProcess_CL` módosításai a Service Map és az Azure Monitor szolgáltatásfigyelő virtuális gépekhez, így továbbra is meg kell terveznie ezt a frissítést.

Ha úgy döntött, hogy nem frissít a **VMInsights-megoldásra,** továbbra is biztosítjuk a `Perf` teljesítmény-munkafüzetek, amelyek a táblázatban szereplő adatokra hivatkoznak.  

## <a name="will-the-service-map-data-sets-also-be-stored-in-insightsmetrics"></a>A szolgáltatástérkép-adatkészletek is tárolhatók az InsightsMetrics-ben?

Az adatkészletek nem lesznek duplikálva, ha mindkét megoldást használja. Mindkét ajánlat megosztja a (korábban `VMComputer` ServiceMapComputer_CL), `VMProcess` (korábban ServiceMapProcess_CL) `VMConnection`tárolt `VMBoundPort` adatkészleteket és az általunk gyűjtött térképadatkészletek tárolására szolgáló táblákat.  

A `InsightsMetrics` tábla tárolja a virtuális gép, a folyamat és a szolgáltatás adatkészletek, amelyeket gyűjtünk, és csak akkor lesz feltöltve, ha az Azure Monitor virtuális gépek hez és a Virtuálisgép-insights-megoldás. A Szolgáltatásleképezés megoldás nem gyűjt `InsightsMetrics` i.

## <a name="will-i-be-double-charged-if-i-have-the-service-map-and-vminsights-solutions-in-my-workspace"></a>Dupla díjat kell fizetnem, ha a szerviztérkép és a VMInsights-megoldások vannak a munkaterületen?

Nem, a két megoldás megosztja a `VMComputer` térképadatkészleteket, `VMProcess` amelyeket (korábban ServiceMapComputer_CL), (korábban ServiceMapProcess_CL) `VMConnection`tárolunk, és `VMBoundPort`. Nem számítunk fel kétszeres díjat ezekért az adatokért, ha mindkét megoldás megtalálható a munkaterületen.

## <a name="if-i-remove-either-the-service-map-or-vminsights-solution-will-it-remove-my-data"></a>Ha eltávolítom a Szolgáltatástérkép vagy a VMInsights-megoldás eltávolítása az adataimat?

Nem, a két megoldás megosztja a `VMComputer` térképadatkészleteket, `VMProcess` amelyeket (korábban ServiceMapComputer_CL), (korábban ServiceMapProcess_CL) `VMConnection`tárolunk, és `VMBoundPort`. Ha eltávolítja az egyik megoldást, ezek az adatkészletek észreveszik, hogy még mindig van egy megoldás, amely felhasználja az adatokat, és a Log Analytics-munkaterületen marad. Mindkét megoldást el kell távolítania a munkaterületről ahhoz, hogy az adatokat el lehessen távolítani belőle.

## <a name="health-feature-is-in-limited-public-preview"></a>Az egészségügyi funkció korlátozott nyilvános előzetes verzióban érhető el

Sok nagyszerű visszajelzést kaptunk az ügyfelektől a Virtuálisgép-állapot szolgáltatáskészletünkről. Van egy csomó érdeklődés körül ez a funkció, és izgalom felett lehetséges támogató felügyeleti munkafolyamatok. Azt tervezzük, hogy egy sor változtatást, hogy adjunk funkciók és a visszajelzéseket kaptunk. 

Annak érdekében, hogy a lehető legkisebbre csökkentsük ezeknek a változásoknak az új ügyfelekre gyakorolt hatását, ezt a funkciót **korlátozott nyilvános előzetes verzióba**helyeztük át. A frissítés 2019 októberében történt.

Azt tervezzük, hogy 2020-ban újra indítjuk ezt az Állapot funkciót, miután az Azure Monitor szolgáltatás a virtuális gépekhez ga-ban van.

## <a name="how-do-existing-customers-access-the-health-feature"></a>Hogyan férhetnek hozzá a meglévő ügyfelek az Állapot szolgáltatáshoz?

Az Állapot szolgáltatást használó meglévő ügyfelek továbbra is hozzáférhetnek, de nem lesznek elérhetők az új ügyfelek számára.  

A szolgáltatás eléréséhez hozzáadhatja a `feature.vmhealth=true` következő szolgáltatásjelzőt [https://portal.azure.com](https://portal.azure.com)az Azure Portal URL-címéhez. Példa `https://portal.azure.com/?feature.vmhealth=true`.

Ezt a rövid URL-t is használhatja, [https://aka.ms/vmhealthpreview](https://aka.ms/vmhealthpreview)amely automatikusan beállítja a funkciójelzőt: .

Meglévő ügyfélként továbbra is használhatja az Állapot szolgáltatást olyan virtuális gépeken, amelyek egy meglévő munkaterület-beállításhoz kapcsolódnak az egészségügyi funkciókkal.  

## <a name="i-use-vm-health-now-with-one-environment-and-would-like-to-deploy-it-to-a-new-one"></a>A VM Health-t most egy környezetben használom, és szeretném telepíteni egy újra

Ha Ön egy meglévő ügyfél, aki az Egészség funkciót használja, és új bevezetéshez vminsights@microsoft.com szeretné használni, kérjük, lépjen kapcsolatba velünk, hogy utasításokat kérjen.

## <a name="next-steps"></a>További lépések

A virtuális gépek figyeléséhez tekintse meg az [Azure Monitor virtuális gépekre való üzembe helyezését.](vminsights-enable-overview.md)
