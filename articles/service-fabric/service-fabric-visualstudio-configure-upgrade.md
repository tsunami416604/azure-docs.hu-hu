---
title: Service Fabric-alkalmazás frissítésének konfigurálása
description: Megtudhatja, hogy miként konfigurálhatja a Service Fabric-alkalmazások Microsoft Visual Studio használatával történő frissítésének beállításait.
author: mikkelhegn
ms.topic: conceptual
ms.date: 06/29/2017
ms.author: mikhegn
ms.openlocfilehash: ef88a528dbb6d326e5cc742d14c27218eb7502f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464083"
---
# <a name="configure-the-upgrade-of-a-service-fabric-application-in-visual-studio"></a>Service Fabric-alkalmazás frissítésének konfigurálása a Visual Studióban
Az Azure Service Fabric Visual Studio-eszközei frissítési támogatást nyújtanak a helyi vagy távoli fürtökön való közzétételhez. Három esetben szeretné frissíteni az alkalmazást egy újabb verzióra ahelyett, hogy lecserélné az alkalmazást a tesztelés és a hibakeresés során:

* Az alkalmazásadatok nem vesznek el a frissítés során.
* A rendelkezésre állás továbbra is magas marad, így a frissítés során nem lesz szolgáltatásmegszakítás, ha elegendő szolgáltatáspéldány van a frissítési tartományok között elosztva.
* A tesztek egy alkalmazáson futtathatók frissítés közben.

## <a name="parameters-needed-to-upgrade"></a>A frissítéshez szükséges paraméterek
Kétféle központi telepítés közül választhat: rendszeres vagy frissítés. A rendszeres központi telepítés törli a fürtön lévő korábbi telepítési információkat és adatokat, miközben a frissítési központi telepítés megőrzi azt. Amikor egy Service Fabric-alkalmazást frissít a Visual Studióban, meg kell adnia az alkalmazásfrissítési paramétereket és az állapot-ellenőrzési házirendeket. Az alkalmazásfrissítési paraméterek segítenek a frissítés vezérléséhez, míg az állapot-ellenőrzési házirendek megállapítják, hogy a frissítés sikeres volt-e. További részletekért lásd: [Service Fabric alkalmazás frissítése: frissítési paraméterek.](service-fabric-application-upgrade-parameters.md)

Három frissítési mód létezik: *Figyelt*, *UnmonitoredAuto*és *UnmonitoredManual*.

* A figyelt frissítés automatizálja a frissítést és az alkalmazás állapotának ellenőrzését.
* A Nem figyelt automatikus frissítés automatizálja a frissítést, de kihagyja az alkalmazás állapot-ellenőrzését.
* Ha Nem figyeltmanuális frissítést végez, manuálisan kell frissítenie az egyes frissítési tartományokat.

Minden frissítési mód különböző paramétereket igényel. A rendelkezésre álló frissítési lehetőségekről az [Alkalmazásfrissítési paraméterek](service-fabric-application-upgrade-parameters.md) című témakörben olvashat bővebben.

## <a name="upgrade-a-service-fabric-application-in-visual-studio"></a>Service Fabric-alkalmazás frissítése a Visual Studióban
Ha a Visual Studio Service Fabric eszközeit használja egy Service Fabric-alkalmazás frissítéséhez, megadhatja, hogy a közzétételi folyamat frissítés legyen, ne pedig rendszeres központi telepítés, ha bejelöli **az alkalmazás frissítése** jelölőnégyzetet.

### <a name="to-configure-the-upgrade-parameters"></a>A frissítési paraméterek konfigurálása
1. Kattintson a jelölőnégyzet melletti **Beállítások** gombra. Megjelenik a **Frissítési paraméterek szerkesztése** párbeszédpanel. A **Frissítési paraméterek szerkesztése** párbeszédpanel támogatja a Figyelt, a Nem figyelt, és a Nem figyeltmanuális frissítési módokat.
2. Válassza ki a használni kívánt frissítési módot, majd töltse ki a paraméterrácsot.

    Minden paraméternek vannak alapértelmezett értékei. A *DefaultServiceTypeHealthPolicy* nem kötelező paraméter kivonattábla-bemenetet vesz igénybe. Íme egy példa a *DefaultServiceTypeHealthPolicy*kivonattábla-beviteli formátumára:

    ```
    @{ ConsiderWarningAsError = "false"; MaxPercentUnhealthyDeployedApplications = 0; MaxPercentUnhealthyServices = 0; MaxPercentUnhealthyPartitionsPerService = 0; MaxPercentUnhealthyReplicasPerPartition = 0 }
    ```

    *A ServiceTypeHealthPolicyMap* egy másik választható paraméter, amely a következő formátumban vesz fel egy kivonatoló tábla bemenetet:

    ```    
    @ {"ServiceTypeName" : "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"}
    ```

    Íme egy valós példa:

    ```
    @{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" }
    ```
3. Ha a UnmonitoredManual frissítési módot választja, manuálisan kell elindítania egy PowerShell-konzolt a frissítési folyamat folytatásához és befejezéséhez. Tekintse meg a [Service Fabric alkalmazásfrissítése: speciális témakörök,](service-fabric-application-upgrade-advanced.md) hogy megtudja, hogyan működik a manuális frissítés.

## <a name="upgrade-an-application-by-using-powershell"></a>Alkalmazás frissítése a PowerShell használatával
A PowerShell-parancsmagokkal frissítheti a Service Fabric-alkalmazást. Részletes információkért lásd: [Service Fabric alkalmazásfrissítési oktatóanyag](service-fabric-application-upgrade-tutorial.md) és [A Start-ServiceFabricApplicationUpgrade.](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade)

## <a name="specify-a-health-check-policy-in-the-application-manifest-file"></a>Állapot-ellenőrzési házirend megadása az alkalmazásjegyzékfájlban
A Service Fabric-alkalmazások minden szolgáltatása rendelkezhet saját állapotházirend-paraméterek, amelyek felülbírálják az alapértelmezett értékeket. Ezeket a paraméterértékeket az alkalmazásjegyzékfájlban is megadhatja.

A következő példa bemutatja, hogyan alkalmazhat egyedi állapot-ellenőrzési házirendet az alkalmazásjegyzékben lévő egyes szolgáltatásokhoz.

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
Az alkalmazások frissítéséről az [Alkalmazás frissítése a Visual Studio használatával](service-fabric-application-upgrade-tutorial.md)című témakörben talál további információt.
