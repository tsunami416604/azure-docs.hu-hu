---
title: 'Alkalmazásfrissítés: frissítési paraméterek'
description: A Service Fabric-alkalmazások frissítésével kapcsolatos paramétereket ismerteti, beleértve a végrehajtandó állapotellenőrzéseket és a frissítés automatikus visszavonására vonatkozó házirendeket.
ms.topic: conceptual
ms.date: 11/08/2018
ms.openlocfilehash: 42b5c52181cfb006ae57e43c183b96a059a9c63a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75377973"
---
# <a name="application-upgrade-parameters"></a>Alkalmazásfrissítési paraméterek
Ez a cikk ismerteti a különböző paramétereket, amelyek egy Azure Service Fabric-alkalmazás frissítése során alkalmazott különböző paramétereket ismerteti. Az alkalmazásfrissítési paraméterek szabályozzák a frissítés során alkalmazott idő- és állapotellenőrzéseket, és meghatározzák azokat a házirendeket, amelyeket a frissítés sikertelensesetben alkalmazni kell. Az alkalmazásparaméterei a következő kontsemést használó frissítésekre vonatkoznak:
- PowerShell
- Visual Studio
- SFCTL
- [Többi](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-startapplicationupgrade)

Az alkalmazásfrissítések a felhasználó által választható három frissítési mód egyikén keresztül történnek. Minden módnak saját alkalmazási paraméterkészlete van:
- Figyelt
- Nem figyelt automatikus
- Nem figyelt manuális

Az alkalmazandó előírt és választható paramétereket az egyes szakaszok a következőképpen írják le.

## <a name="visual-studio-and-powershell-parameters"></a>A Visual Studio és a PowerShell paraméterei

A Service Fabric alkalmazás frissítései a PowerShell használatával a [Start-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade) parancsot használják. A frissítési mód kiválasztása a **Figyelt**, **UnmonitoredAuto**vagy **UnmonitoredManual** paraméter tikulyában adja át a [Start-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade)paraméternek.

A Visual Studio Service Fabric alkalmazásfrissítési paraméterei a Visual Studio frissítési beállításai párbeszédpanelen állíthatók be. A Visual Studio frissítési módja a **Frissítési mód** legördülő lista használatával van kiválasztva a **Monitor**, **UnmonitoredAuto**vagy **UnmonitoredManual beállításhoz.** További információt [a Service Fabric-alkalmazások frissítésének konfigurálása a Visual Studióban című témakörben talál.](service-fabric-visualstudio-configure-upgrade.md)

### <a name="required-parameters"></a>Kötelező paraméterek
(PS=PowerShell, VS=Visual Studio)

| Paraméter | Érvényesség | Leírás |
| --- | --- | --- |
ApplicationName |PS| A frissítendő alkalmazás neve. Példák: fabric:/VisualObjects, fabric:/ClusterMonitor. |
ApplicationTypeVersion|PS|Az alkalmazástípusnak a frissítés által megcélozott verziója. |
FailureAction (Hibaművelet) |PS, VS|Az engedélyezett értékek a **visszaállítás**, **a Kézi**és **az Érvénytelen**. A kompenzációs művelet végrehajtása, ha egy *figyelt* frissítés találkozik figyelési szabályzat vagy az állapotházirend megsértése. <br>**A visszaállítás** azt adja meg, hogy a frissítés automatikusan visszaáll a frissítés előtti verzióra. <br>**A Kézi érték** azt jelzi, hogy a frissítés a *Nem figyeltkézi* frissítési módra vált. <br>**Az Érvénytelen** azt jelzi, hogy a hibaművelet érvénytelen.|
Figyelt |PS|Azt jelzi, hogy a frissítési mód figyelt. Miután a parancsmag befejezte a frissítési tartomány frissítését, ha a frissítési tartomány és a fürt állapota megfelel a megadott állapotházirendeknek, a Service Fabric frissíti a következő frissítési tartományt. Ha a frissítési tartomány vagy fürt nem felel meg az állapotházirendeknek, a frissítés sikertelen lesz, és a Service Fabric visszaállítja a frissítési tartomány frissítését, vagy a megadott házirend szerint visszaáll manuális módba. Ez az ajánlott mód az éles környezetben történő alkalmazásfrissítésekhez. |
UpgradeMode (Frissítésmód) | Vs | Az engedélyezett értékek **figyelt** (alapértelmezett), **UnmonitoredAuto**vagy **UnmonitoredManual**. Ebben a cikkben tekintse meg a PowerShell-paramétereket a cikkben szereplő egyes módokhoz. |
Nem figyeltAutomatikus | PS | Azt jelzi, hogy a frissítési mód nem figyelt automatikus. Miután a Service Fabric frissített egy frissítési tartományt, a Service Fabric az alkalmazás állapotától függetlenül frissíti a következő frissítési tartományt. Ez a mód nem ajánlott éles környezetben, és csak az alkalmazás fejlesztése során hasznos. |
Nem figyelt kézi | PS | Azt jelzi, hogy a frissítési mód nem figyelt manuális. Miután a Service Fabric frissített egy frissítési tartományt, megvárja, hogy frissítse a következő frissítési tartományt a *Resume-ServiceFabricApplicationUpgrade* parancsmag használatával. |

### <a name="optional-parameters"></a>Választható paraméterek

Az állapotkiértékelési paraméterek megadása nem kötelező. Ha az állapotkiértékelési feltételek nincsenek megadva a frissítés indításakor, a Service Fabric az alkalmazáspéldány ApplicationManifest.xml fájljában megadott alkalmazásállapot-házirendeket használja.

> [!div class="mx-tdBreakAll"]
> | Paraméter | Érvényesség | Leírás |
> | --- | --- | --- |
> | ApplicationParameter (Alkalmazásparaméter) |PS, VS| Megadja az alkalmazásparaméterek felülbírálásait.<br>A PowerShell alkalmazásparaméterei kivonatoló név/értékpárként vannak megadva. Például @{ "VotingData_MinReplicaSetSize" = "3"; "VotingData_PartitionCount" = "1" }.<br>A Visual Studio alkalmazásparaméterei megadhatók az **Alkalmazásparaméter-fájl** párbeszédpanel Szolgáltatásháló-alkalmazás közzététele párbeszédpanelen.
> | Jóváhagyás |PS| Az engedélyezett **értékek: Igaz** és **Hamis**. Megerősítést kér a parancsmag futtatása előtt. |
> | ConsiderWarningAsError (Figyelemfigyelmeztesse- |PS, VS |Az engedélyezett **értékek: Igaz** és **Hamis**. Az alapértelmezett érték a **False** (Hamis). Kezelje az alkalmazás figyelmeztetési állapoteseményeit hibaként az alkalmazás állapotának kiértékelésekor a frissítés során. Alapértelmezés szerint a Service Fabric nem értékeli ki a figyelmeztetési állapoteseményeket, hogy hibák (hibák), így a frissítés akkor is folytatható, ha vannak figyelmeztető események. |
> | DefaultServiceTypeHealthPolicy | PS, VS |A figyelt frissítéshez a MaxPercentUnhealthyPartitionsPerService, MaxPercentUnhealthyReplicasPerPartition, MaxPercentUnhealthyServicesService, MaxPercentUnhealthyServicesServices, MaxPercentUnhealthyServices. Például 5,10,15 a következő értékeket jelzi: MaxPercentUnhealthyPartitionsPerService = 5, MaxPercentUnhealthyReplicasPerPartition = 10, MaxPercentUnhealthyServices = 15. |
> | Force | PS, VS | Az engedélyezett **értékek: Igaz** és **Hamis**. Azt jelzi, hogy a frissítési folyamat kihagyja a figyelmeztető üzenetet, és akkor is kényszeríti a frissítést, ha a verziószám nem változott. Ez hasznos a helyi tesztelés, de nem ajánlott éles környezetben, mivel el kell távolítani a meglévő központi telepítés, amely leállást és potenciális adatvesztést okoz. |
> | ForceRestart (Kényszerítésrestart) |PS, VS |Ha a szolgáltatáskód frissítése nélkül frissít egy konfigurációt vagy adatcsomagot, a szolgáltatás csak akkor indul újra, ha a ForceRestart tulajdonság **értéke True**. Amikor a frissítés befejeződött, a Service Fabric értesíti a szolgáltatást, hogy egy új konfigurációs csomag vagy adatcsomag érhető el. A szolgáltatás felelős a módosítások alkalmazásáért. Szükség esetén a szolgáltatás újraindíthatja magát. |
> | ÁllapotcheckRetryTimeoutSec |PS, VS |Az időtartam (másodpercben), amely a Service Fabric továbbra is állapotkiértékelés végrehajtása, mielőtt a frissítés sikertelenként deklarálása. Az alapértelmezett érték 600 másodperc. Ez az időtartam a *HealthCheckWaitDurationSec* elérése után kezdődik. Ebben a *HealthCheckRetryTimeout,* Service Fabric előfordulhat, hogy az alkalmazás állapotának több állapotát is végezhet. Az alapértelmezett érték 10 perc, és az alkalmazásnak megfelelően kell testre szabni. |
> | HealthCheckStableDurationSec |PS, VS |Az időtartam (másodpercben) annak ellenőrzésére, hogy az alkalmazás stabil-e, mielőtt a következő frissítési tartományba lépne, vagy befejezné a frissítést. Ez a várakozási időtartam az állapotfelmérés után közvetlenül az állapotfelmérés észrevétlen változásainak megelőzésére szolgál. Az alapértelmezett érték 120 másodperc, és az alkalmazáshoz megfelelően kell testre szabni. |
> | HealthCheckWaitDurationSec |PS, VS | A frissítés befejezése után a frissítési tartományban, mielőtt a Service Fabric kiértékeli az alkalmazás állapotát, a várakozási idő (másodpercben). Ez az időtartam is tekinthető az idő egy alkalmazás fut, mielőtt kifogástalannak tekinthető. Ha az állapot-ellenőrzés sikeres, a frissítési folyamat a következő frissítési tartományba kerül.  Ha az állapot-ellenőrzés sikertelen, a Service Fabric megvárja [az UpgradeHealthCheckInterval-ot,](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-fabric-settings#clustermanager) mielőtt újra megpróbálkozna az állapotellenőrzéssel, amíg el nem éri a *HealthCheckRetryTimeoutSec adatbázist.* Az alapértelmezett és az ajánlott érték 0 másodperc. |
> | MaxPercentUnhealthyDeployedApplications|PS, VS |Az alapértelmezett és ajánlott érték 0. Adja meg a telepített alkalmazások maximális számát (lásd az [Állapot szakaszt),](service-fabric-health-introduction.md)amely nem megfelelő állapotú lehet, mielőtt az alkalmazás nem megfelelő állapotúnak minősülne, és nem felel meg a frissítésnek. Ez a paraméter határozza meg az alkalmazás állapotát a csomóponton, és segít a frissítések során fellépő problémák észlelésén. Általában az alkalmazás replikái terheléselosztással a másik csomópontra, amely lehetővé teszi az alkalmazás kifogástalan állapotúnak tűnik, így lehetővé teszi a frissítés folytatásához. A szigorú *MaxPercentUnhealthyDeployedApplications* állapot megadásával a Service Fabric gyorsan észleli az alkalmazáscsomaggal kapcsolatos problémát, és segít a sikertelen gyors frissítés létrehozásában. |
> | MaxPercentUnhealthyServices |PS, VS |*A DefaultServiceTypeHealthPolicy* és a *ServiceTypeHealthPolicyMap*paramétere. Az alapértelmezett és ajánlott érték 0. Adja meg az alkalmazáspéldányban lévő szolgáltatások maximális számát, amelyek nem kifogástalanállapotúak lehetnek, mielőtt az alkalmazás nem megfelelő állapotúnak minősülne, és nem felel meg a frissítésnek. |
> | MaxPercentUnhealthyPartitionsPerService|PS, VS |*A DefaultServiceTypeHealthPolicy* és a *ServiceTypeHealthPolicyMap*paramétere. Az alapértelmezett és ajánlott érték 0. Adja meg a szolgáltatás ban található partíciók maximális számát, amelyek nem kifogástalanállapotúak lehetnek, mielőtt a szolgáltatás nem kifogástalannak minősülne. |
> | MaxPercentUnhealthyReplicasPerPartition|PS, VS |*A DefaultServiceTypeHealthPolicy* és a *ServiceTypeHealthPolicyMap*paramétere. Az alapértelmezett és ajánlott érték 0. Adja meg a partíción lévő replikák maximális számát, amelyek nem kifogástalanok lehetnek, mielőtt a partíció nem kifogástalannak minősülne. |
> | ServiceTypeHealthPolicyMap | PS, VS | A szolgáltatástípushoz tartozó szolgáltatások állapotának kiértékeléséhez használt állapotházirendet jelöli. Kivonattábla-bevitelt a következő formátumban: @ {"ServiceTypeName" : "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"} Például: @{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" } |
> | IdőoutSec | PS, VS | A művelet idő-elmisztikai idejét adja meg másodpercben. |
> | UpgradeDomainTimeoutSec |PS, VS |Egyetlen frissítési tartomány frissítésének maximális ideje (másodpercben). Ha ez az időtúllépés elérésekor a frissítés leáll, és a *FailureAction*beállításalapján folytatja. Az alapértelmezett érték soha (Végtelen), és az alkalmazáshoz megfelelően testre kell szabni. |
> | FrissítésReplicaSetCheckTimeoutSec |PS, VS |Másodpercben mérve.<br>**Állapotnélküli szolgáltatás**- egyetlen frissítési tartományon belül a Service Fabric megpróbálja biztosítani, hogy a szolgáltatás további példányai érhetők el. Ha a célpéldányok száma több, mint egy, a Service Fabric megvárja, hogy egynél több példány érhető el, legfeljebb egy maximális időtúllépést érték. Ezt az idő-megadást az *UpgradeReplicaSetCheckTimeoutSec* tulajdonság határozza meg. Ha az idő-kiállás lejár, a Service Fabric folytatja a frissítést, függetlenül a szolgáltatáspéldányok számától. Ha a célpéldányok száma egy, a Service Fabric nem vár, és azonnal folytatja a frissítést.<br><br>**Állapotalapú szolgáltatás**- egyetlen frissítési tartományon belül a Service Fabric megpróbálja biztosítani, hogy a replikakészlet kvórummal rendelkezzen. A Service Fabric megvárja, hogy a kvórum elérhető legyen, legfeljebb egy (az *UpgradeReplicaSetCheckTimeoutSec* tulajdonság által megadott) időtúllépésre. Ha az idő-kiállás lejár, a Service Fabric folytatja a frissítést, kvórumtól függetlenül. Ez a beállítás soha (végtelen) beállítás, amikor előregörgetéskor, és 1200 másodpercként van beállítva a visszagörgetéskor. |
> | UpgradeTimeoutSec |PS, VS |A teljes frissítésre vonatkozó idő-meghosszabbítás (másodpercben). Ha ez az időtúllépés elérésekor a frissítés leáll, és *a FailureAction* aktiválódik. Az alapértelmezett érték soha (Végtelen), és az alkalmazáshoz megfelelően testre kell szabni. |
> | WhatIf | PS | Az engedélyezett **értékek: Igaz** és **Hamis**. Bemutatja, mi történne a parancsmag futtatásakor. A parancsmag nem fut. |

A *MaxPercentUnhealthyServices*, *A MaxPercentUnhealthyPartitionsPerService*és a *MaxPercentUnhealthyReplicasPerPartition* feltételek egy alkalmazáspéldány szolgáltatástípusánként adhatók meg. Ezek a paraméterek szolgáltatásonkénti beállítása lehetővé teszi, hogy egy alkalmazás különböző szolgáltatástípusokat tartalmazzon különböző értékelési szabályzatokkal. Egy állapotmentes átjárószolgáltatás-típus például rendelkezhet egy *MaxPercentUnhealthyPartitionsPerService szolgáltatással,* amely eltér egy adott alkalmazáspéldány állapotalapú motorszolgáltatás-típusától.

## <a name="sfctl-parameters"></a>SFCTL paraméterek

A Service Fabric alkalmazás frissítései a Service Fabric CLI használatával az [sfctl alkalmazás frissítési](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-upgrade) parancs átvételét, valamint a következő szükséges és választható paramétereket használja.

### <a name="required-parameters"></a>Kötelező paraméterek

| Paraméter | Leírás |
| --- | --- |
| alkalmazás-azonosító  |A frissítés alatt álló alkalmazás azonosítója. <br> Ez általában az alkalmazás teljes neve a "fabric:" URI-séma nélkül. A 6.0-s verziótól kezdődően a hierarchikus neveka ' '\~karakterrel van elválasztva. Ha például az alkalmazás neve "fabric:/myapp/app1", az alkalmazás identitása a korábbi verziókban "myapp app1" lesz, a korábbi verziókban pedig "myapp/app1".\~|
alkalmazás-verzió |Az alkalmazástípusnak a frissítés által megcélozott verziója.|
paraméterek  |Az alkalmazás frissítésekor alkalmazandó alkalmazásparaméter-felülbírálások JSON-kódolású listája.|

### <a name="optional-parameters"></a>Választható paraméterek

| Paraméter | Leírás |
| --- | --- |
alapértelmezett-szolgáltatás-egészségügyi-házirend | [JSON](https://docs.microsoft.com/rest/api/servicefabric/sfclient-model-servicetypehealthpolicy) kódolt specifikációja az állapotházirend alapértelmezés szerint egy szolgáltatástípus állapotának kiértékeléséhez használt. A térkép alapértelmezés szerint üres. |
hiba-művelet | Az engedélyezett értékek a **visszaállítás**, **a Kézi**és **az Érvénytelen**. A kompenzációs művelet végrehajtása, ha egy *figyelt* frissítés találkozik figyelési szabályzat vagy az állapotházirend megsértése. <br>**A visszaállítás** azt adja meg, hogy a frissítés automatikusan visszaáll a frissítés előtti verzióra. <br>**A Kézi érték** azt jelzi, hogy a frissítés a *Nem figyeltkézi* frissítési módra vált. <br>**Az Érvénytelen** azt jelzi, hogy a hibaművelet érvénytelen.|
erő-újraindítás | Ha a szolgáltatáskód frissítése nélkül frissít egy konfigurációt vagy adatcsomagot, a szolgáltatás csak akkor indul újra, ha a ForceRestart tulajdonság **értéke True**. Amikor a frissítés befejeződött, a Service Fabric értesíti a szolgáltatást, hogy egy új konfigurációs csomag vagy adatcsomag érhető el. A szolgáltatás felelős a módosítások alkalmazásáért. Szükség esetén a szolgáltatás újraindíthatja magát. |
állapot-ellenőrzés-újrapróbálkozás-időállás | Az állapotkiértékelés újrapróbálkozási ideje, ha az alkalmazás vagy a fürt nem kifogástalan állapotú a *FailureAction* végrehajtása előtt. Először egy ISO 8601 időtartamot képviselő karakterláncként értelmezik. Ha ez nem sikerül, akkor a rendszer a teljes ezredmásodpercszámot jelző számként értelmezi. Alapértelmezett: PT0H10m0s. |
állapot-ellenőrzés-stabil időtartamú | Az az idő, amerre az alkalmazásnak vagy fürtnek kifogástalan állapotban kell maradnia, mielőtt a frissítés a következő frissítési tartományra lépne. Először egy ISO 8601 időtartamot képviselő karakterláncként értelmezik. Ha ez nem sikerül, akkor a rendszer a teljes ezredmásodpercszámot jelző számként értelmezi. Alapértelmezett: PT0H2M0S. |
állapot-ellenőrzés-várakozás-időtartam | Az állapotházirendek alkalmazása előtt a frissítési tartomány befejezése után várakoznivaló idő. Először egy ISO 8601 időtartamot képviselő karakterláncként értelmezik. Ha ez nem sikerül, akkor a rendszer a teljes ezredmásodpercszámot jelző számként értelmezi. Alapértelmezett: 0.|
max-egészségtelen-apps | Az alapértelmezett és ajánlott érték 0. Adja meg a telepített alkalmazások maximális számát (lásd az [Állapot szakaszt),](service-fabric-health-introduction.md)amely nem megfelelő állapotú lehet, mielőtt az alkalmazás nem megfelelő állapotúnak minősülne, és nem felel meg a frissítésnek. Ez a paraméter határozza meg az alkalmazás állapotát a csomóponton, és segít a frissítések során fellépő problémák észlelésén. Általában az alkalmazás replikái terheléselosztással a másik csomópontra, amely lehetővé teszi az alkalmazás kifogástalan állapotúnak tűnik, így lehetővé teszi a frissítés folytatásához. A szigorú *max-unhealthy-apps* állapot megadásával service fabric gyorsan észleli a problémát az alkalmazáscsomaggal, és segít a sikertelen gyors frissítés. 0 és 100 közötti számként jelölve. |
mód | Az engedélyezett értékek **figyelt**, **UpgradeMode**, **UnmonitoredAuto**, **UnmonitoredManual**. Az alapértelmezett érték **UnmonitoredAuto**. Lásd: A Visual Studio és a PowerShell *szükséges paraméterek* szakaszban ezeket az értékeket.|
replika-készlet-check-időout |Másodpercben mérve. <br>**Állapotnélküli szolgáltatás**- egyetlen frissítési tartományon belül a Service Fabric megpróbálja biztosítani, hogy a szolgáltatás további példányai érhetők el. Ha a célpéldányok száma több, mint egy, a Service Fabric megvárja, hogy egynél több példány érhető el, legfeljebb egy maximális időtúllépést érték. Ezt az idő-out a *replika-set-check-timeout* tulajdonság használatával van megadva. Ha az idő-kiállás lejár, a Service Fabric folytatja a frissítést, függetlenül a szolgáltatáspéldányok számától. Ha a célpéldányok száma egy, a Service Fabric nem vár, és azonnal folytatja a frissítést.<br><br>**Állapotalapú szolgáltatás**- egyetlen frissítési tartományon belül a Service Fabric megpróbálja biztosítani, hogy a replikakészlet kvórummal rendelkezzen. A Service Fabric megvárja, hogy a kvórum elérhető legyen, a *kópia-set-check-timeout* tulajdonság által megadott maximális időtúllépésre (a kópia-set-check-timeout tulajdonság által megadottig). Ha az idő-kiállás lejár, a Service Fabric folytatja a frissítést, kvórumtól függetlenül. Ez a beállítás soha (végtelen) beállítás, amikor előregörgetéskor, és 1200 másodpercként van beállítva a visszagörgetéskor. |
szolgáltatás-egészségügyi politika | JSON kódolású leképezés szolgáltatástípus-állapotházirenddel szolgáltatástípus neve szerint. A térkép üres, legyen az alapértelmezett. [Paraméter JSON formátum.](https://docs.microsoft.com/rest/api/servicefabric/sfclient-model-applicationhealthpolicy#servicetypehealthpolicymap). Az "Érték" rész JSON-ja tartalmazza a **MaxPercentUnhealthyServices**, **MaxPercentUnhealthyPartitionsPerService**és **A MaxPercentUnhealthyReplicasPerPartition értéket.** Lásd: a Visual Studio és a PowerShell választható paraméterek szakaszban ezeket a paramétereket.
timeout | A művelet idő-elmisztikai idejét adja meg másodpercben. Alapértelmezett: 60. |
frissítés-tartomány-idő-időhatos | Az egyes frissítési tartományoknak a *FailureAction* végrehajtása előtt be kell fejezniük az időt. Először egy ISO 8601 időtartamot képviselő karakterláncként értelmezik. Ha ez nem sikerül, akkor a rendszer a teljes ezredmásodpercszámot jelző számként értelmezi. Az alapértelmezett érték soha (Végtelen), és az alkalmazáshoz megfelelően testre kell szabni. Alapértelmezett: P10675199DT02H48M05.4775807s. |
frissítés-időeltetés | Az egyes frissítési tartományoknak a *FailureAction* végrehajtása előtt be kell fejezniük az időt. Először egy ISO 8601 időtartamot képviselő karakterláncként értelmezik. Ha ez nem sikerül, akkor a rendszer a teljes ezredmásodpercszámot jelző számként értelmezi. Az alapértelmezett érték soha (Végtelen), és az alkalmazáshoz megfelelően testre kell szabni. Alapértelmezett: P10675199DT02H48M05.4775807s.|
figyelmeztetés hibaként | Az engedélyezett **értékek: Igaz** és **Hamis**. Az alapértelmezett érték a **False** (Hamis). Lehet átadni, mint a zászló. Kezelje az alkalmazás figyelmeztetési állapoteseményeit hibaként az alkalmazás állapotának kiértékelésekor a frissítés során. Alapértelmezés szerint a Service Fabric nem értékeli ki a figyelmeztetési állapoteseményeket, hogy hibák (hibák), így a frissítés akkor is folytatható, ha vannak figyelmeztető események. |

## <a name="next-steps"></a>További lépések
[Az alkalmazás Visual Studio használatával történő frissítése](service-fabric-application-upgrade-tutorial.md) végigvezeti az alkalmazás frissítésén a Visual Studió használatával.

[Az alkalmazás PowerShell használatával történő frissítése](service-fabric-application-upgrade-tutorial-powershell.md) végigvezeti a PowerShell használatával történő alkalmazásfrissítésen.

[Az alkalmazás frissítése a Service Fabric CLI Linux on Linux](service-fabric-application-lifecycle-sfctl.md#upgrade-application) használatával végigvezeti önt egy alkalmazás frissítési szolgáltatás használatával A Service Fabric CLI használatával.

[Az alkalmazás frissítése a Service Fabric Eclipse beépülő modul használatával](service-fabric-get-started-eclipse.md#upgrade-your-service-fabric-java-application)

Tegye kompatibilissé az alkalmazásfrissítéseket az [adatszerializálás](service-fabric-application-upgrade-data-serialization.md)használatának elsajátításával.

A speciális funkciók használata az alkalmazás frissítése során a [Speciális témakörökre](service-fabric-application-upgrade-advanced.md)hivatkozva.

Az alkalmazásfrissítések gyakori problémáinak megoldása az [Alkalmazásfrissítések hibaelhárítása](service-fabric-application-upgrade-troubleshooting.md)című témakör lépéseire hivatkozva.
