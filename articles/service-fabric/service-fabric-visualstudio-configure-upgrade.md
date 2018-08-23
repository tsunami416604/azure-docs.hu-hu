---
title: Service Fabric-alkalmazás verziófrissítésének konfigurálása |} A Microsoft Docs
description: Ismerje meg, hogy a beállítások konfigurálása a Service Fabric-alkalmazás frissítése a Microsoft Visual Studio használatával.
services: service-fabric
documentationcenter: na
author: mikkelhegn
manager: mfussell
editor: tglee
ms.assetid: 1757ba85-0b7b-4f16-8a23-2ddaa61c86c6
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/29/2017
ms.author: mikkelhegn
ms.openlocfilehash: 79120371ca2a62e5ef9f2bf38476635db12e9fcc
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2018
ms.locfileid: "42055091"
---
# <a name="configure-the-upgrade-of-a-service-fabric-application-in-visual-studio"></a>Service Fabric-alkalmazás verziófrissítésének konfigurálása Visual studióban
Visual Studio-eszközök az Azure Service Fabric frissítési támogatást nyújt a helyi vagy távoli fürtökhöz való közzététel beállításait. Nincsenek három forgatókönyv, amelyben meg szeretné frissíteni az alkalmazást egy újabb verzióra, és cserélje le az alkalmazás tesztelése és hibakeresése során helyett:

* Alkalmazásadatok a frissítés során legyen adatvesztés.
* Rendelkezésre állási marad, magas így nem lesz a frissítés során szolgáltatás megszakítás van-e elegendő szolgáltatáspéldányok elosztva frissítési tartományok között.
* Tesztek is futtathatók alkalmazások ellen, miközben frissítés alatt áll.

## <a name="parameters-needed-to-upgrade"></a>Frissítés szükséges paraméterek
Két típusú központi közül választhat: rendszeres vagy a verziófrissítésre. Egy normál központi telepítés egy frissítés telepítése megőrzi, amíg a korábbi központi telepítési információk és az adatokat a fürtön törli. Amikor frissít egy Service Fabric-alkalmazás a Visual Studióban, meg kell adnia a alkalmazásfrissítési paraméterek és egészségügyi ellenőrizheti a szabályzatokat. Alkalmazásfrissítési paraméterek segítségével szabályozhatja a frissítés állapotának ellenőrzése szabályzatok határozzák meg, hogy sikeres volt-e a frissítés közben. Lásd: [Service Fabric-alkalmazás frissítése: frissítési paraméterek](service-fabric-application-upgrade-parameters.md) további részletekért.

Három frissítési módot: *figyelt*, *UnmonitoredAuto*, és *UnmonitoredManual*.

* A figyelt frissítés automatizálja a frissítés és az alkalmazás állapotának ellenőrzése.
* UnmonitoredAuto frissítés automatizálja a frissítést, de kihagyja az állapot-ellenőrzés.
* Amikor ezt teszi, hogy a frissítés UnmonitoredManual, mindegyik frissítési tartományon manuálisan frissíteni szeretne.

Minden frissítési üzemmódban szükséges paraméterek más-más részhalmazához. Lásd: [alkalmazásfrissítési paraméterek](service-fabric-application-upgrade-parameters.md) további információ az elérhető frissítési beállítások.

## <a name="upgrade-a-service-fabric-application-in-visual-studio"></a>Frissítés a Service Fabric-alkalmazás a Visual Studióban
A Visual Studio Service Fabric-eszközök frissítése a Service Fabric-alkalmazás használata, megadhatja a közzététel a folyamatok ellenőrzésével által normál központi telepítés helyett a frissítés a **Upgradovat aplikaci** jelölőnégyzetet.

### <a name="to-configure-the-upgrade-parameters"></a>A frissítési paraméterek konfigurálása
1. Kattintson a **beállítások** gomb mellett a jelölőnégyzet jelölését. A **frissítési paraméterek szerkesztése** párbeszédpanel jelenik meg. A **frissítési paraméterek szerkesztése** párbeszédpanel a figyelt UnmonitoredAuto és UnmonitoredManual frissítési módot támogat.
2. Válassza ki a frissítési módot, amelyet szeretne használni, és töltse ki a paraméter rács.

    Minden paraméter alapértelmezett értéke van. A nem kötelező paraméter *DefaultServiceTypeHealthPolicy* egy kivonatot tábla bemenete vesz igénybe. Íme egy példa a kivonat tábla bemeneti formátum a *DefaultServiceTypeHealthPolicy*:

    ```
    @{ ConsiderWarningAsError = "false"; MaxPercentUnhealthyDeployedApplications = 0; MaxPercentUnhealthyServices = 0; MaxPercentUnhealthyPartitionsPerService = 0; MaxPercentUnhealthyReplicasPerPartition = 0 }
    ```

    *ServiceTypeHealthPolicyMap* van egy másik olyan nem kötelező paraméter, amely egy kivonatot tábla bemenete a következő formátumban:

    ```    
    @ {"ServiceTypeName" : "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"}
    ```

    Íme egy valós beszédhelyzetek példa:

    ```
    @{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" }
    ```
3. Ha UnmonitoredManual frissítési módot választja, a PowerShell-konzolt, a folytatáshoz, és a frissítési folyamat befejezéséhez manuálisan kell elindítani. Tekintse meg [Service Fabric-alkalmazás frissítése: Speciális témakörök](service-fabric-application-upgrade-advanced.md) megtudhatja, hogyan kézi frissítés működik.

## <a name="upgrade-an-application-by-using-powershell"></a>Alkalmazások frissítése a PowerShell használatával
PowerShell-parancsmagok segítségével Service Fabric-alkalmazás frissítése. Lásd: [Service Fabric-alkalmazás frissítési oktatóanyag](service-fabric-application-upgrade-tutorial.md) és [Start-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade) részletes információkat.

## <a name="specify-a-health-check-policy-in-the-application-manifest-file"></a>Adja meg egy állapotházirend-ellenőrzés az Alkalmazásjegyzék-fájl
Minden szolgáltatás a Service Fabric-alkalmazás rendelkezhet saját egészségügyi házirend paramétereket, amelyek felülírják az alapértelmezett értékeket. Megadhatja, hogy ezek a paraméterértékek az Alkalmazásjegyzék-fájl található.

Az alábbi példa bemutatja, hogyan az egyes szolgáltatások az alkalmazásjegyzékben egyedi állapotának ellenőrzése szabályzat alkalmazásához.

```xml
<Policies>
    <HealthPolicy ConsiderWarningAsError="false" MaxPercentUnhealthyDeployedApplications="20">
        <DefaultServiceTypeHealthPolicy MaxPercentUnhealthyServices="20"               
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />
        <ServiceTypeHealthPolicy ServiceTypeName="ServiceTypeName1"
                MaxPercentUnhealthyServices="20"
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />      
    </HealthPolicy>
</Policies>
```
## <a name="next-steps"></a>További lépések
Egy alkalmazás frissítésével kapcsolatos további információkért lásd: [Visual studióval egy alkalmazás frissítése](service-fabric-application-upgrade-tutorial.md).
