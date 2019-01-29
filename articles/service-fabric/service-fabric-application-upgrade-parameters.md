---
title: 'Alkalmazásfrissítés: frissítési paraméterek |} A Microsoft Docs'
description: Verzióra történő frissítéséhez a Service Fabric-alkalmazás, például állapot-ellenőrzések elvégzéséhez és a házirendek automatikusan visszavonja a frissítés paramétereket ismerteti.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: a4170ac6-192e-44a8-b93d-7e39c92a347e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/08/2018
ms.author: subramar
ms.openlocfilehash: 73b48525566f9bf0107ba3b029c516ca294ca141
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55099192"
---
# <a name="application-upgrade-parameters"></a>Alkalmazásfrissítési paraméterek
Ez a cikk ismerteti a különböző paraméterek, amelyek érvényesek az Azure Service Fabric-alkalmazás frissítése során. Alkalmazásfrissítési paraméterek szabályozhatja az időtúllépéseket és állapot-ellenőrzést, a frissítés során alkalmazott, és azok adja meg a szabályzatokban, amelyek a alkalmazni kell, amikor a sikertelen frissítés. Alkalmazás-paraméterek használatával verziófrissítések vonatkoznak:
- PowerShell
- Visual Studio
- SFCTL
- [REST](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-startapplicationupgrade)

Alkalmazásfrissítések kezdeményezett keresztül három felhasználó által választható frissítési módok egyikét. Minden mód rendelkezik a saját alkalmazás paraméterei:
- Figyelés alatt
- Nem figyelt automatikus
- Nem figyelt manuális

A megfelelő szükséges és választható paramétereket módon minden szakasz ismerteti.

## <a name="visual-studio-and-powershell-parameters"></a>A Visual Studio és a PowerShell-paraméterek

A Service Fabric-alkalmazások frissítéséhez használata a PowerShell használata a [Start-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade) parancsot. A frissítési mód van kiválasztva, vagy adja át a **figyelt**, **UnmonitoredAuto**, vagy **UnmonitoredManual** paramétert [ Start-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade).

A Visual Studio a Service Fabric alkalmazásfrissítési paraméterek vannak beállítva, a Visual Studio beállítások párbeszédpanelen keresztül. A Visual Studio frissítési mód van kiválasztva, használja a **frissítési mód** legördülő listából vagy **figyelt**, **UnmonitoredAuto**, vagy **UnmonitoredManual** . További információkért lásd: [Service Fabric-alkalmazás verziófrissítésének konfigurálása Visual studióban](service-fabric-visualstudio-configure-upgrade.md).

### <a name="required-parameters"></a>Szükséges paraméterek
(PS=PowerShell, VS=Visual Studio)

| Paraméter | Erre vonatkozik | Leírás |
| --- | --- | --- |
Alkalmazásnév |PS| Neve az alkalmazás, amely frissítés alatt áll. Példák: fabric: / VisualObjects, a fabric: / ClusterMonitor. |
ApplicationTypeVersion|PS|Az alkalmazás verziója írja be, amely a frissítési célokat. |
FailureAction |PS, VS|Engedélyezett értékek a következők **visszaállítási**, **manuális**, és **érvénytelen**. A művelet végrehajtásához, amikor egy *figyelt* frissítési szabályzat vagy egészségügyi szabálymegsértéseknek figyelési ütközik. <br>**Visszaállítás** Megadja, hogy a frissítés automatikusan állítja vissza a frissítés előtti verziót. <br>**Manuális** azt jelzi, hogy a frissítés átvált a *UnmonitoredManual* frissítési módban. <br>**Érvénytelen** azt jelzi, hogy a sikertelen művelet érvénytelen.|
Figyelés alatt |PS|Azt jelzi, hogy a frissítési mód figyel. Miután a parancsmag befejezi a frissítési tartomány, frissítése, ha a frissítési tartomány és a fürt állapotának felel meg az Ön által meghatározott házirendek, a Service Fabric frissíti a következő frissítési tartományra. Ha a frissítési tartomány vagy a fürt nem teljesíti az állapotházirendeket, a frissítés sikertelen lesz, és a Service Fabric visszaállítja a frissítés a frissítési tartomány, illetve a megadott házirend szerint kézi módra vált át. Ez az alkalmazásfrissítések éles környezetben az ajánlott módja. |
Upgrademode tulajdonság | VS | Engedélyezett értékek a következők **figyelt** (alapértelmezett), **UnmonitoredAuto**, vagy **UnmonitoredManual**. Tekintse meg a részleteket ebben a cikkben minden üzemmódban a PowerShell-paramétereket. |
UnmonitoredAuto | PS | Azt jelzi, hogy a frissítési mód nem figyelt automatikus. Miután a Service Fabric egy frissítési tartományt frissít, a Service Fabric frissíti attól függetlenül, az alkalmazás állapota a következő frissítési tartományra. Ebben a módban nem javasolt éles környezetben, és csak hasznos az alkalmazások fejlesztése során. |
UnmonitoredManual | PS | Azt jelzi, hogy a frissítési mód nem figyelt manuális. Service Fabric egy frissítési tartományt frissít, miután arra vár, hogy a következő frissítési tartományra frissítése használatával a *Resume-ServiceFabricApplicationUpgrade* parancsmagot. |

### <a name="optional-parameters"></a>Választható paraméterek

Az egészségügyi értékelési paraméter megadása nem kötelező. Ha a frissítés indításakor az egészségügyi értékelési feltételek nincsenek megadva, a Service Fabric az egészségügyi szabályzatok az ApplicationManifest.xml alkalmazáspéldány megadott használ.

A tábla alján a vízszintes görgetősávok segítségével megtekintheti a teljes Leírás mezőben.

(PS=PowerShell, VS=Visual Studio)

| Paraméter | Erre vonatkozik | Leírás |
| --- | --- | --- |
| ApplicationParameter |PS, VS| Adja meg az alkalmazás paramétereit felülbírálásokat.<br>PowerShell alkalmazás paraméterek kivonattábla név/érték párokként vannak megadva. Ha például @{"VotingData_MinReplicaSetSize" = "3"; "VotingData_PartitionCount" = "1"}.<br>A Service Fabric-alkalmazás közzététele párbeszédpanelen, a Visual Studio alkalmazás paraméterei adható meg a **alkalmazás Paraméterfájlja** mező.
| Megerősítés |PS| Engedélyezett értékek a következők **igaz** és **hamis**. A parancsmag futtatása előtt megerősítést kér. |
| ConsiderWarningAsError |PS, VS |Engedélyezett értékek a következők **igaz** és **hamis**. Az alapértelmezett érték a **False** (Hamis). Az alkalmazás a figyelmeztető állapot események tekinti a hibákat a frissítés során az alkalmazás állapotának kiértékelésekor. Alapértelmezés szerint a Service Fabric nem értékeli figyelmeztetés hálózatállapot-események kell hiba (hibák), így a frissítést folytatni lehessen, még akkor is, ha a figyelmeztetési esemény. |
| DefaultServiceTypeHealthPolicy | PS, VS |Adja meg a házirend az alapértelmezett service Type MaxPercentUnhealthyReplicasPerPartition, MaxPercentUnhealthyServices a formátumban MaxPercentUnhealthyPartitionsPerService, a figyelt frissítéshez használni. Ha például 5,10,15 azt jelzi, hogy a következő értékeket: MaxPercentUnhealthyPartitionsPerService = 5, MaxPercentUnhealthyReplicasPerPartition = 10, MaxPercentUnhealthyServices = 15. |
| Kényszerített | PS, VS | Engedélyezett értékek a következők **igaz** és **hamis**. Azt jelzi, hogy a frissítési folyamat kihagyja a figyelmeztető üzenetet, és a frissítés kényszeríti, még akkor is, ha a verziószám nem változott. Ez akkor hasznos, ha helyi tesztelés, de a használata nem ajánlott éles környezetben szükséges, eltávolítás, a meglévő üzemelő példányt, amely-ideje és a lehetséges adatvesztést okoz. |
| ForceRestart |PS, VS |Konfiguráció vagy adat-csomag frissítése a szolgáltatást kód frissítése nélkül, ha a szolgáltatás újraindításakor csak akkor, ha a ForceRestart tulajdonság értéke **igaz**. A frissítés befejeződése után a Service Fabric értesíti a szolgáltatást, hogy egy új csomagot vagy adat-csomag érhető el. A szolgáltatás felelős a módosítások alkalmazása. Ha szükséges, a szolgáltatás újraindíthatja magát. |
| HealthCheckRetryTimeoutSec |PS, VS |Az időtartam (másodpercben), hogy a Service Fabric továbbra is állapotának kiértékelését előtt deklaráló a frissítés, mert nem sikerült végrehajtani. Az alapértelmezett érték 600 másodperc. Ez az időtartam után elindul *HealthCheckWaitDurationSec* elérésekor. Ez belül *HealthCheckRetryTimeout*, Service Fabric előfordulhat, hogy hajtsa végre az alkalmazás állapotáról, több állapot-ellenőrzések. Az alapértelmezett érték 10 perc, és az alkalmazás kell szabható meg megfelelően. |
| HealthCheckStableDurationSec |PS, VS |Az időtartam (másodpercben), győződjön meg arról, hogy az alkalmazás stabil áthelyezése a következő frissítési tartományra vagy a frissítés befejezése előtt. Ez a várakozási időtartam szolgál, hogy nem észlelt állapotváltozások megfelelő, az állapot-ellenőrzés elvégzése után. Az alapértelmezett érték 120 másodperc, és az alkalmazás kell szabható meg megfelelően. |
| HealthCheckWaitDurationSec |PS, VS | A frissítés befejezése után a Service Fabric előtt frissítési tartományban (másodpercben) várakozási időt kiértékeli az alkalmazás állapotát. Ennek az időtartamnak az idő, az alkalmazásnak megfelelően kellene futnia, mielőtt azt is figyelembe kell venni kifogástalan is tekinthető. Ha az állapot-ellenőrzés továbbítja, a frissítési folyamat abból a következő frissítési tartományra.  Ha az állapot-ellenőrzés nem sikerül, a Service Fabric várakozik a [UpgradeHealthCheckInterval](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-fabric-settings#clustermanager) állapotát újrapróbálkozás előtt ellenőrizze újra, amíg a *HealthCheckRetryTimeoutSec* elérésekor. Az alapértelmezett és ajánlott érték: 0 másodperc. |
| MaxPercentUnhealthyDeployedApplications|PS, VS |Alapértelmezett és ajánlott érték: 0. Adja meg a központilag telepített alkalmazások maximális számát (lásd a [rész állapotfigyelő](service-fabric-health-introduction.md)), amely lehet nem megfelelő az alkalmazás nem megfelelő állapotúnak számít, és nem sikerül a frissítés előtt. Ez a paraméter határozza meg az alkalmazás állapota a csomóponton, és segít a hibák észlelése, a frissítés során. Általában a replikákat az alkalmazás első kiegyenlített terhelésű egy másik csomópontra, amely lehetővé teszi az alkalmazás megjelenik a kifogástalan állapotú, ezáltal lehetővé téve a folytatja a frissítést. Adjon meg egy szigorú *MaxPercentUnhealthyDeployedApplications* állapotát, a Service Fabric gyors észlelése az alkalmazáscsomag problémájára, és adatsávokat egy gyors frissítése sikertelen. |
| MaxPercentUnhealthyServices |PS, VS |Egy paraméter *DefaultServiceTypeHealthPolicy* és *ServiceTypeHealthPolicyMap*. Alapértelmezett és ajánlott érték: 0. A szolgáltatások maximális számát adja meg az alkalmazáspéldány, amelyek nem megfelelő állapotú lehetnek az alkalmazás nem megfelelő állapotúnak számít, és nem sikerül a frissítés előtt. |
| MaxPercentUnhealthyPartitionsPerService|PS, VS |Egy paraméter *DefaultServiceTypeHealthPolicy* és *ServiceTypeHealthPolicyMap*. Alapértelmezett és ajánlott érték: 0. Adja meg a partíciók maximális száma egy szolgáltatás, amely nem megfelelő állapotú is lehet, mielőtt a szolgáltatás nem megfelelő állapotúnak számít. |
| MaxPercentUnhealthyReplicasPerPartition|PS, VS |Egy paraméter *DefaultServiceTypeHealthPolicy* és *ServiceTypeHealthPolicyMap*. Alapértelmezett és ajánlott érték: 0. Adja meg a másodpéldányok maximális száma partíció, amely nem megfelelő állapotú is lehet, mielőtt a partíció nem megfelelő állapotúnak számít. |
| ServiceTypeHealthPolicyMap | PS, VS | Szolgáltatástípus tartozó szolgáltatások állapotának értékeléséhez használt állapotházirend jelöli. A kivonatoló tábla bemenete veszi a következő formátumban: @ {"servicetypename tulajdonság is": "MaxPercentUnhealthyServices MaxPercentUnhealthyPartitionsPerService, MaxPercentUnhealthyReplicasPerPartition,"} például: @{"ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5"} |
| TimeoutSec | PS, VS | Adja meg az időtúllépés másodpercben. a művelet. |
| UpgradeDomainTimeoutSec |PS, VS |Maximális idő (másodperc) egyetlen frissítési tartomány frissítésére. Ha az időkorlát lejár, a frissítés leállítja, és folytatódik a beállítása alapján *FailureAction*. Az alapértelmezett érték: soha nem (végtelen) és az alkalmazás kell szabható meg megfelelően. |
| UpgradeReplicaSetCheckTimeoutSec |PS, VS |Idejének mérése másodpercben.<br>**Az állapotmentes szolgáltatás**--belül egyetlen frissítési tartományt, a Service Fabric próbál győződjön meg arról, hogy rendelkezésre állnak-e a szolgáltatás további példányai. Ha a cél-példányok száma egynél több, a Service Fabric megvárja, amíg elérhető, a maximális időkorlátot akár több példányt. Ez az időtúllépési használatával van megadva a *UpgradeReplicaSetCheckTimeoutSec* tulajdonság. Ha az időkorlát lejár, a Service Fabric folytathatná a frissítést, függetlenül a szolgáltatás példányainak számát. Ha a cél-példányok száma, a Service Fabric nem várja meg, és azonnal folytathatná a frissítést.<br><br>**Állapotalapú szolgáltatás**--belül egyetlen frissítési tartományt, a Service Fabric próbál ügyeljen arra, hogy a replikakészlethez kvóruma. Service Fabric várakozik a kvóruma számára elérhető, akár a maximális időkorlátot (azokat a *UpgradeReplicaSetCheckTimeoutSec* tulajdonság). Ha az időkorlát lejár, a Service Fabric folytathatná a frissítést, függetlenül a kvórum. A beállítás értéke szerint soha nem set (végtelen), ha a működés közbeni előre, és az 1200-as másodperc visszaállításakor. |
| UpgradeTimeoutSec |PS, VS |Egy időtúllépés (másodpercben), amely a teljes frissítésre vonatkozik. Ha ez az időkorlát lejár, a frissítés leállítja és *FailureAction* aktiválódik. Az alapértelmezett érték: soha nem (végtelen) és az alkalmazás kell szabható meg megfelelően. |
| WhatIf | PS | Engedélyezett értékek a következők **igaz** és **hamis**. Megmutatja, hogy mi történne a parancsmag futtatásakor. A parancsmag nem fut. |

A *MaxPercentUnhealthyServices*, *MaxPercentUnhealthyPartitionsPerService*, és *MaxPercentUnhealthyReplicasPerPartition* feltételek szerint adható meg szolgáltatás típusa az alkalmazáspéldány. Ezen paraméterek szolgáltatási beállítás lehetővé teszi az alkalmazás számára a különböző értékelési házirendek különböző szolgáltatásokat típusokat tartalmazhat. Például állapotmentes szolgáltatás átjárótípus rendelkezhet egy *MaxPercentUnhealthyPartitionsPerService* , amely eltér az adott alkalmazáspéldány állapot-nyilvántartó motor szolgáltatástípus.

## <a name="sfctl-parameters"></a>SFCTL-paraméterek

A Service Fabric-alkalmazások frissítéséhez használatával a Service Fabric parancssori felület használata a [sfctl-alkalmazás frissítése](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-upgrade) parancsot a következő szükséges és választható paramétereket együtt.

### <a name="required-parameters"></a>Szükséges paraméterek

| Paraméter | Leírás |
| --- | --- |
| alkalmazásazonosító  |Az alkalmazás, amely frissítés alatt áll azonosítója. <br> Ez általában a nélkül az alkalmazás teljes nevét a "fabric:" Schéma identifikátoru URI. 6.0-s verzió kezdve hierarchikus nevek vannak tagolva, az a "~" karaktert. Például, ha az alkalmazás neve "fabric: / myapp/app1", lesz az identitása "myapp ~ app1" 6.0 + és "myapp/app1" korábbi verzióiban.|
alkalmazás-verzió |Az alkalmazás verziója írja be, amely a frissítési célokat.|
paraméterek  |Parametr aplikace JSON-kódolású listáját felülbírálja a alkalmazni, ha az alkalmazás frissítését.|

### <a name="optional-parameters"></a>Választható paraméterek
| Paraméter | Leírás |
| --- | --- |
default-service-health-policy | [JSON](https://docs.microsoft.com/rest/api/servicefabric/sfclient-model-servicetypehealthpolicy) alapértelmezés szerint egy bizonyos szolgáltatástípusként állapotának értékeléséhez használt állapotházirend specifikace kódolva. A térkép alapértelmezés szerint üres. |
a művelet sikertelen | Engedélyezett értékek a következők **visszaállítási**, **manuális**, és **érvénytelen**. A művelet végrehajtásához, amikor egy *figyelt* frissítési szabályzat vagy egészségügyi szabálymegsértéseknek figyelési ütközik. <br>**Visszaállítás** Megadja, hogy a frissítés automatikusan állítja vissza a frissítés előtti verziót. <br>**Manuális** azt jelzi, hogy a frissítés átvált a *UnmonitoredManual* frissítési módban. <br>**Érvénytelen** azt jelzi, hogy a sikertelen művelet érvénytelen.|
kényszerített újraindítás | Konfiguráció vagy adat-csomag frissítése a szolgáltatást kód frissítése nélkül, ha a szolgáltatás újraindításakor csak akkor, ha a ForceRestart tulajdonság értéke **igaz**. A frissítés befejeződése után a Service Fabric értesíti a szolgáltatást, hogy egy új csomagot vagy adat-csomag érhető el. A szolgáltatás felelős a módosítások alkalmazása. Ha szükséges, a szolgáltatás újraindíthatja magát. |
health-check-retry-timeout | Mennyi ideig állapotának kiértékelését újra, amikor az alkalmazás vagy a fürt állapota nem kifogástalan, mielőtt *FailureAction* hajtja végre. Először kerül értelmezésre egy karakterlánc, amely az ISO 8601 időtartama. Ha ez nem sikerül, majd kerül értelmezésre egy számot jelölő ezredmásodperc teljes száma. Alapértelmezett: PT0H10M0S. |
health-check-stable-duration | Mennyi ideig, hogy az alkalmazás vagy a fürt kell megfelelő állapotú marad a következő frissítési tartományra abból a frissítés előtt. Először kerül értelmezésre egy karakterlánc, amely az ISO 8601 időtartama. Ha ez nem sikerül, majd kerül értelmezésre egy számot jelölő ezredmásodperc teljes száma. Alapértelmezett: PT0H2M0S. |
health-check-wait-duration | A házirendek alkalmazása előtt a frissítési tartomány befejezése után várakozási idő mennyisége. Először kerül értelmezésre egy karakterlánc, amely az ISO 8601 időtartama. Ha ez nem sikerül, majd kerül értelmezésre egy számot jelölő ezredmásodperc teljes száma. Alapértelmezett: 0.|
max-unhealthy-apps | Alapértelmezett és ajánlott érték: 0. Adja meg a központilag telepített alkalmazások maximális számát (lásd a [rész állapotfigyelő](service-fabric-health-introduction.md)), amely lehet nem megfelelő az alkalmazás nem megfelelő állapotúnak számít, és nem sikerül a frissítés előtt. Ez a paraméter határozza meg az alkalmazás állapota a csomóponton, és segít a hibák észlelése, a frissítés során. Általában a replikákat az alkalmazás első kiegyenlített terhelésű egy másik csomópontra, amely lehetővé teszi az alkalmazás megjelenik a kifogástalan állapotú, ezáltal lehetővé téve a folytatja a frissítést. Adjon meg egy szigorú *maximális száma nem megfelelő alkalmazások* állapotát, a Service Fabric gyors észlelése az alkalmazáscsomag problémájára, és adatsávokat egy gyors frissítése sikertelen. Egy 0 és 100 közötti számot jelöli. |
mód | Engedélyezett értékek a következők **figyelt**, **upgrademode tulajdonság**, **UnmonitoredAuto**, **UnmonitoredManual**. Alapértelmezett érték a **UnmonitoredAuto**. Tekintse meg a Visual Studio és a PowerShell *szükséges paraméterek* szakasz ezeket az értékeket leírását.|
replica-set-check-timeout |Idejének mérése másodpercben. <br>**Az állapotmentes szolgáltatás**--belül egyetlen frissítési tartományt, a Service Fabric próbál győződjön meg arról, hogy rendelkezésre állnak-e a szolgáltatás további példányai. Ha a cél-példányok száma egynél több, a Service Fabric megvárja, amíg elérhető, a maximális időkorlátot akár több példányt. Ez az időtúllépési használatával van megadva a *replika-set-ellenőrzése – időtúllépés* tulajdonság. Ha az időkorlát lejár, a Service Fabric folytathatná a frissítést, függetlenül a szolgáltatás példányainak számát. Ha a cél-példányok száma, a Service Fabric nem várja meg, és azonnal folytathatná a frissítést.<br><br>**Állapotalapú szolgáltatás**--belül egyetlen frissítési tartományt, a Service Fabric próbál ügyeljen arra, hogy a replikakészlethez kvóruma. Service Fabric várakozik a kvóruma számára elérhető, akár a maximális időkorlátot (azokat a *replika-set-ellenőrzése – időtúllépés* tulajdonság). Ha az időkorlát lejár, a Service Fabric folytathatná a frissítést, függetlenül a kvórum. A beállítás értéke szerint soha nem set (végtelen), ha a működés közbeni előre, és az 1200-as másodperc visszaállításakor. |
service-health-policy | JSON kódolású térkép rajzolása szolgáltatás típus állapotházirend / szolgáltatás neve. A térkép az üres lehet alapértelmezett. [A paraméter JSON formátumban. ](https://docs.microsoft.com/rest/api/servicefabric/sfclient-model-applicationhealthpolicy#servicetypehealthpolicymap). A "Value" részében a JSON-fájl tartalmaz **MaxPercentUnhealthyServices**, **MaxPercentUnhealthyPartitionsPerService**, és **MaxPercentUnhealthyReplicasPerPartition**. Ezek a paraméterek leírását a Visual Studio és a választható paraméterek: PowerShell című szakaszában talál.
timeout | Adja meg az időtúllépés másodpercben. a művelet. Alapértelmezett: 60. |
frissítés-tartomány-időkorlátja | Mennyi ideig mindegyik frissítési tartományon van befejezését, mielőtt *FailureAction* hajtja végre. Először kerül értelmezésre egy karakterlánc, amely az ISO 8601 időtartama. Ha ez nem sikerül, majd kerül értelmezésre egy számot jelölő ezredmásodperc teljes száma. Az alapértelmezett érték: soha nem (végtelen) és az alkalmazás kell szabható meg megfelelően. Alapértelmezett: P10675199DT02H48M05.4775807S. |
frissítés – időtúllépés | Mennyi ideig mindegyik frissítési tartományon van befejezését, mielőtt *FailureAction* hajtja végre. Először kerül értelmezésre egy karakterlánc, amely az ISO 8601 időtartama. Ha ez nem sikerül, majd kerül értelmezésre egy számot jelölő ezredmásodperc teljes száma. Az alapértelmezett érték: soha nem (végtelen) és az alkalmazás kell szabható meg megfelelően. Alapértelmezett: P10675199DT02H48M05.4775807S.|
warning-as-error | Engedélyezett értékek a következők **igaz** és **hamis**. Az alapértelmezett érték a **False** (Hamis). Az argumentumként átadhatók azt a jelzőt. Az alkalmazás a figyelmeztető állapot események tekinti a hibákat a frissítés során az alkalmazás állapotának kiértékelésekor. Alapértelmezés szerint a Service Fabric nem értékeli figyelmeztetés hálózatállapot-események kell hiba (hibák), így a frissítést folytatni lehessen, még akkor is, ha a figyelmeztetési esemény. |

## <a name="next-steps"></a>További lépések
[Az alkalmazás használatával a Visual Studio frissítése](service-fabric-application-upgrade-tutorial.md) végigvezeti egy alkalmazás frissítése a Visual Studio használatával.

[Az alkalmazás használatával Powershell frissítése](service-fabric-application-upgrade-tutorial-powershell.md) végigvezeti egy alkalmazás frissítése a PowerShell használatával.

[Service Fabric parancssori felület használata Linux rendszeren az alkalmazás frissítéséhez](service-fabric-application-lifecycle-sfctl.md#upgrade-application) végigvezeti egy alkalmazás frissítése a Service Fabric parancssori felület használatával.

[Az alkalmazás használatával a Service Fabric Eclipse beépülő modul frissítése](service-fabric-get-started-eclipse.md#upgrade-your-service-fabric-java-application)

Hogyan használja az alkalmazásfrissítések kompatibilissé [adatok szerializálása](service-fabric-application-upgrade-data-serialization.md).

Speciális funkciók használata közben lépésként tekintse át az alkalmazás frissítéséhez [haladó témakörök](service-fabric-application-upgrade-advanced.md).

Az alkalmazásfrissítések gyakori problémák megoldása szerint hajtsa végre a hivatkozó [Alkalmazásfrissítések hibaelhárítása](service-fabric-application-upgrade-troubleshooting.md).
