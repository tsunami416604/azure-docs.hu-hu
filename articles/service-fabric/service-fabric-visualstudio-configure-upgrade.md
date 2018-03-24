---
title: Konfigurálja a Service Fabric-alkalmazás frissítése |} Microsoft Docs
description: 'Útmutató: a beállítások konfigurálása a Service Fabric-alkalmazás frissítése a Microsoft Visual Studio használatával.'
services: service-fabric
documentationcenter: na
author: mikkelhegn
manager: mfussell
editor: tglee
ms.assetid: 1757ba85-0b7b-4f16-8a23-2ddaa61c86c6
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/29/2017
ms.author: mikkelhegn
ms.openlocfilehash: 6bd9f23be2133ca8a207ceca19fcccc116eaf24d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="configure-the-upgrade-of-a-service-fabric-application-in-visual-studio"></a>A Service Fabric-alkalmazás frissítését a Visual Studio konfigurálása
A Visual Studio eszközök Azure Service Fabric frissítési támogatást nyújt a helyi vagy távoli fürtök közzétételt. Háromféle esetben kívánja frissíteni az alkalmazást tesztelés és hibakeresés során az alkalmazás felülírása helyett egy újabb verzióra van:

* Alkalmazásadatok nem fognak veszni a frissítés során.
* Rendelkezésre állási továbbra is magas, nem lesz a frissítés során a szolgáltatás megszakadásának Ha elegendő szolgáltatáspéldány frissítési tartományok között ossza szét.
* Tesztek futtatható az alkalmazáshoz, amíg a frissítés alatt áll.

## <a name="parameters-needed-to-upgrade"></a>A paraméterek frissítéséhez szükséges
Kétféle típusú központi telepítés közül választhat: rendszeres vagy frissítés. A szokásos telepítési töröl minden korábbi központi telepítési információkat és adatokat a fürtön során egy frissítés telepítése megőrzi azt. Amikor frissít egy Service Fabric-alkalmazás, a Visual Studio alkalmazásban, meg kell adnia az alkalmazás frissítési paramétereit és állapotának ellenőrzése házirendek. Alkalmazás frissítési paramétereit a frissítés szabályozni a állapotházirendeket jelölőnégyzet határozza meg, hogy sikeres volt-e a frissítés során. Lásd: [Service Fabric az alkalmazásfrissítés: frissítési paraméterek](service-fabric-application-upgrade-parameters.md) további részleteket.

Három frissítési módot: *figyelt*, *UnmonitoredAuto*, és *UnmonitoredManual*.

* A figyelt frissítés automatizálja a frissítés és az alkalmazás az állapot-ellenőrzéssel.
* UnmonitoredAuto frissítés automatizálja a frissítést, de kihagyja az állapot-ellenőrzése.
* Ha így tesz, UnmonitoredManual frissítés, frissítenie kell manuálisan mindegyik frissítési tartományon.

Minden egyes frissítési módhoz paraméterek más-más részhalmazához. Lásd: [alkalmazás frissítési paraméterei](service-fabric-application-upgrade-parameters.md) tudhat meg többet az elérhető frissítési beállítások.

## <a name="upgrade-a-service-fabric-application-in-visual-studio"></a>A Service Fabric-alkalmazás, a Visual Studio frissítése
A Visual Studio Service Fabric-eszközök frissítése a Service Fabric-alkalmazás használata, megadhatja a közzétételi folyamat úgy, hogy a normál központi telepítés helyett frissítés ellenőrzése a **az alkalmazás frissítése** jelölőnégyzetet.

### <a name="to-configure-the-upgrade-parameters"></a>A frissítési paramétereinek a konfigurálása
1. Kattintson a **beállítások** gomb melletti jelölőnégyzetet. A **frissítése paraméterek szerkesztése** párbeszédpanel jelenik meg. A **frissítése paraméterek szerkesztése** párbeszédpanel a figyelt UnmonitoredAuto és UnmonitoredManual frissítési módot támogat.
2. Válassza ki a frissítési mód, amelyet szeretne használni, és ezután adja meg a paraméter rács.

    Minden paraméter alapértelmezett értéke van. A nem kötelező paraméter *DefaultServiceTypeHealthPolicy* egy kivonatoló tábla bemenetből fogad adatokat. A kivonat tábla bemeneti formátum a példa *DefaultServiceTypeHealthPolicy*:

    ```
    @{ ConsiderWarningAsError = "false"; MaxPercentUnhealthyDeployedApplications = 0; MaxPercentUnhealthyServices = 0; MaxPercentUnhealthyPartitionsPerService = 0; MaxPercentUnhealthyReplicasPerPartition = 0 }
    ```

    *Servicetypehealthpolicymap paraméterek hiányzó értékei* egy másik olyan nem kötelező paraméter, amely egy kivonatoló tábla bemenetből fogad adatokat a következő formátumban:

    ```    
    @ {"ServiceTypeName" : "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"}
    ```

    Íme egy valós példa:

    ```
    @{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" }
    ```
3. Ha UnmonitoredManual frissítési módot választja, a PowerShell-konzolban, a folytatáshoz, majd a frissítési folyamat befejezéséhez manuálisan kell elindítani. Tekintse meg [Service Fabric az alkalmazásfrissítés: témakörök speciális](service-fabric-application-upgrade-advanced.md) megtudhatja, hogyan kézi frissítés működik.

## <a name="upgrade-an-application-by-using-powershell"></a>Alkalmazások frissítése a PowerShell használatával
PowerShell-parancsmagok segítségével frissítse a Service Fabric-alkalmazás. Lásd: [Service Fabric-alkalmazás frissítési oktatóanyag](service-fabric-application-upgrade-tutorial.md) és [Start-ServiceFabricApplicationUpgrade](https://msdn.microsoft.com/library/mt125975.aspx) részletes információkat.

## <a name="specify-a-health-check-policy-in-the-application-manifest-file"></a>Adjon meg egy állapotházirend-ellenőrzés az Alkalmazásjegyzék-fájl
Minden szolgáltatás a Service Fabric-alkalmazás rendelkezhet saját állapotfigyelő házirend paramétereket, amelyek felülírják az alapértelmezett értékeket. Megadhatja, hogy ezek az Alkalmazásjegyzék-fájl a paraméterértékek.

A következő példa bemutatja, hogyan egyedi ellenőrzés állapotházirend az alkalmazásjegyzékben szereplő minden egyes szolgáltatás alkalmazni.

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
Egy alkalmazás frissítésével kapcsolatos további információkért lásd: [frissít egy alkalmazást a Visual Studio használatával](service-fabric-application-upgrade-tutorial.md).
