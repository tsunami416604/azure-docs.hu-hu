---
title: 'Az alkalmazásfrissítés: frissítési paraméterek |} Microsoft Docs'
description: A Service Fabric-alkalmazás, beleértve a Állapotellenőrzések végrehajtásához és a házirendek automatikusan visszavonja a frissítés frissítéssel kapcsolatos paramétereket ismerteti.
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
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: eb319b0f4e910163572ee62d8bdee735f27be592
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="application-upgrade-parameters"></a>Alkalmazásfrissítési paraméterek
Ez a cikk ismerteti a különböző paraméterek, amelyek érvényesek az Azure Service Fabric-alkalmazás frissítése során. A paraméternek számít a nevét és az alkalmazás verziója. Az időtúllépések és állapot-ellenőrzést, a frissítés során alkalmazott szabályozó forgatógombját, és azok adja meg a házirendekben, amelyek kell alkalmazni, ha egy frissítés sikertelen lesz.

<br>

| Paraméter | Leírás |
| --- | --- |
| ApplicationName |Neve az alkalmazást, amely a frissítés alatt áll. Példák: fabric: / VisualObjects, fabric: / ClusterMonitor |
| TargetApplicationTypeVersion |Az alkalmazás verziójától írja be, amely a frissítési célokat. |
| FailureAction |Ha a frissítés nem sikerül, a Service Fabric által végrehajtott műveletet. Az alkalmazás esetleg állítható vissza a frissítés előtti verziót (visszaállítás), vagy a frissítés az aktuális frissítési tartomány, lehet, hogy leállt. Az utóbbi esetben a frissítési mód is manuálisra változott. Megengedett értékek: visszaállítási és manuális. |
| HealthCheckWaitDurationSec |A rendszer mennyi ideig vár (másodpercben), miután a frissítés befejeződött a frissítési tartomány előtt a Service Fabric kiértékeli az alkalmazás állapotáról. Ennek az időtartamnak a kérelmet rendszerűnek kell lennie, mielőtt azt is figyelembe kell venni megfelelő idő szerint is meg kell fontolni. Ha az állapot-ellenőrzése sikeres, a frissítési folyamat eltérő lehet a következő frissítési tartományra.  Ha az állapot-ellenőrzése sikertelen, a Service Fabric vár időközt (UpgradeHealthCheckInterval) mielőtt újra próbálkozna az állapot-ellenőrzéssel, a HealthCheckRetryTimeout eléréséig. Az alapértelmezett és ajánlott érték: 0 másodperc. |
| HealthCheckRetryTimeoutSec |Az időtartam (másodpercben), hogy a Service Fabric továbbra is állapotának kiértékelését, mielőtt jelezné a frissítés, mert nem sikerült végrehajtani. Az alapértelmezett érték 600 másodperc. Ennek az időtartamnak a HealthCheckWaitDuration lejárta után elindul. A HealthCheckRetryTimeout belül a Service Fabric előfordulhat, hogy több egészségügyi ellenőrzések elvégzéséhez az alkalmazás állapotát. Az alapértelmezett érték 10 perc és kell testre szabható megfelelően az alkalmazást. |
| HealthCheckStableDurationSec |Az időtartam (másodpercben) ellenőrizze, hogy az alkalmazás stabil áthelyezése a következő frissítési tartományra vagy a frissítés befejezése előtt. Megakadályozza a megfelelő rendszerállapot példával módosítását, az állapot-ellenőrzés végrehajtása után a várakozási időtartamot használja. Az alapértelmezett érték 120 másodperc, és kell testre szabható megfelelően az alkalmazást. |
| UpgradeDomainTimeoutSec |Maximális idő (másodperc) egyetlen frissítési tartomány frissítéséhez. Az időtúllépési elérésekor, a frissítés leáll, és UpgradeFailureAction beállítást függően eltérő lehet. Az alapértelmezett érték: Soha ne (végtelen) és kell testre szabható megfelelően az alkalmazást. |
| UpgradeTimeout |Egy időtúllépés (másodpercben), amely a teljes frissítés vonatkozik. Ez az időkorlát elérésekor, a frissítési leáll, és UpgradeFailureAction elindul. Az alapértelmezett érték: Soha ne (végtelen) és kell testre szabható megfelelően az alkalmazást. |
| UpgradeHealthCheckInterval |A gyakoriság, hogy az állapot ellenőrzése jelölőnégyzet be van jelölve. Ez a paraméter van megadva a ClusterManager szakaszában a *fürt* *manifest*, és nincs megadva a frissítési parancsmag részeként. Az alapértelmezett értéke 60 másodperc. |

<br>

## <a name="service-health-evaluation-during-application-upgrade"></a>Szolgáltatás állapotának kiértékelését az alkalmazásfrissítés során
<br>
Az állapotfigyelő értékelési feltételek opcionálisak. Ha a rendszerállapot-értékelési feltételek frissítés indításakor nincsenek megadva, a Service Fabric használja az alkalmazás az alkalmazáspéldány ApplicationManifest.xml megadott házirendek.

<br>

| Paraméter | Leírás |
| --- | --- |
| ConsiderWarningAsError |Alapértelmezett értéke False. Hibaként a figyelmeztetések állapotát az alkalmazáshoz frissítés során az alkalmazás állapotának kiértékelésekor. Alapértelmezés szerint a Service Fabric nem értékelik ki figyelmeztetés állapotát az eseményeket hiba (hibák), így a frissítést folytatni lehessen, még akkor is, ha nincsenek a figyelmeztetési események. |
| MaxPercentUnhealthyDeployedApplications |Alapértelmezett és ajánlott érték: 0. Adja meg a központilag telepített alkalmazások maximális számát (lásd a [állapotfigyelő szakasz](service-fabric-health-introduction.md)), amely lehet a nem megfelelő, mielőtt az alkalmazás nem megfelelő állapotúnak számít, és a frissítés sikertelen lesz. Ez a paraméter határozza meg az alkalmazás állapotának a csomóponton és segítséget nyújt a problémák a frissítés során. Általában a replikákat az alkalmazás első terhelésű másik fürtcsomópontra, amely lehetővé teszi az alkalmazás megjelenik a kifogástalan állapotú, így a frissítés gombra. A szigorú MaxPercentUnhealthyDeployedApplications állapotfigyelő megadásával a Service Fabric képesek észlelni az alkalmazás a csomaggal kapcsolatos problémára gyorsan és előállíthatja egy gyors frissítése sikertelen. |
| MaxPercentUnhealthyServices |Alapértelmezett és ajánlott érték: 0. Adja meg a szolgáltatások maximális számát az alkalmazáspéldány, amely a nem megfelelő lehet, mielőtt az alkalmazás nem megfelelő állapotúnak számít, és a frissítés sikertelen lesz. |
| MaxPercentUnhealthyPartitionsPerService |Alapértelmezett és ajánlott érték: 0. Adja meg a maximális számú partíciót egy szolgáltatás, amely a nem megfelelő lehet, mielőtt a szolgáltatás nem megfelelő állapotúnak számít. |
| MaxPercentUnhealthyReplicasPerPartition |Alapértelmezett és ajánlott érték: 0. A partíció, amely a nem megfelelő lehet, mielőtt a partíció nem megfelelő állapotúnak számít. Adja meg a másodpéldányok maximális száma. |
| UpgradeReplicaSetCheckTimeout |<p>**Állapotmentes szolgáltatások**--frissítési tartományon belüli, a Service Fabric megpróbálja győződjön meg arról, hogy rendelkezésre állnak-e a szolgáltatás további példányai. Ha a cél-példányok száma több, a Service Fabric megvárja-e a egynél több példány van, akár a maximális időkorlátot. Ez az időkorlát a UpgradeReplicaSetCheckTimeout tulajdonság használatával van megadva. Ha az időkorlát lejár, a Service Fabric folytatja, a frissítést, függetlenül a szolgáltatás példányainak száma. Ha a cél-példányok száma, a Service Fabric nem történik meg, és azonnal folytatja a frissítést.</p><p>**Az állapotalapú szolgáltatás**--frissítési tartományon belüli, a Service Fabric megpróbálja győződjön meg arról, hogy rendelkezik-e a kvórum a replikakészlethez. A Service Fabric egy kvórum van, akár (UpgradeReplicaSetCheckTimeout tulajdonság által megadott) maximális időtúllépési értéket vár. Ha az időkorlát lejár, a Service Fabric folytatja, a frissítést, függetlenül a kvórum. Ez a beállítás be, soha nem (végtelen), ha a működés közbeni előre, 1200-as másodperc visszaállításakor.</p> |
| ForceRestart |Egy konfigurációs vagy az adatcsomag frissíti a szolgáltatást kód frissítése nélkül, ha a szolgáltatás újraindításakor csak akkor, ha a ForceRestart tulajdonság értéke igaz. Ha a frissítés befejeződött, a Service Fabric értesíti a szolgáltatás, hogy egy új konfigurációs csomagot vagy adatcsomag érhető el. A szolgáltatás felelős a módosítások végrehajtásakor. Ha szükséges, a szolgáltatás újraindíthatja magát. |

<br>
<br>
A MaxPercentUnhealthyServices MaxPercentUnhealthyPartitionsPerService és MaxPercentUnhealthyReplicasPerPartition feltételek egy alkalmazás példány szolgáltatás típusonkénti adható meg. Ezen paraméterek /-szolgáltatás beállítása lehetővé teszi, hogy az alkalmazás különböző értékelési házirendekkel különböző szolgáltatásokat tartalmaznak. Például egy állapot nélküli átjáró szolgáltatás típusa lehet egy MaxPercentUnhealthyPartitionsPerService, egy állapot-nyilvántartó motor szolgáltatástípus egy adott alkalmazás példány eltér.

## <a name="next-steps"></a>További lépések
[Az alkalmazás használata a Visual Studio frissítése](service-fabric-application-upgrade-tutorial.md) végigvezeti Önt az alkalmazásfrissítés Visual Studio használatával.

[Az alkalmazás használatával Powershell frissítése](service-fabric-application-upgrade-tutorial-powershell.md) végigvezeti Önt az alkalmazásfrissítés PowerShell használatával.

[A Service Fabric parancssori felület használatával Linux alkalmazás frissítése](service-fabric-application-lifecycle-sfctl.md#upgrade-application) végigvezeti Önt az alkalmazásfrissítés Service Fabric parancssori felület használatával.

[Service Fabric Eclipse beépülő modul segítségével az alkalmazás frissítése](service-fabric-get-started-eclipse.md#upgrade-your-service-fabric-java-application)

Az alkalmazásfrissítéseket által használatának megtanulása kompatibilissé [Adatszerializálás](service-fabric-application-upgrade-data-serialization.md).

Összetettebb funkciók használata az alkalmazás frissítésekor szakaszra [Speciális témakörök](service-fabric-application-upgrade-advanced.md).

Alkalmazásfrissítések gyakori problémáinak megoldásához hajtsa végre a hivatkozással [Alkalmazásfrissítések hibaelhárítási](service-fabric-application-upgrade-troubleshooting.md).
