---
title: Speciális alkalmazás frissítési témakörök |} A Microsoft Docs
description: Ez a cikk ismerteti a Service Fabric-alkalmazás frissítése kapcsolatos néhány speciális témakört.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: e29585ff-e96f-46f4-a07f-6682bbe63281
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 3cdddac74552b56dfe3567adf30f1a05b6eb8e24
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58663791"
---
# <a name="service-fabric-application-upgrade-advanced-topics"></a>Service Fabric-alkalmazás frissítése: Speciális témakörök
## <a name="adding-or-removing-service-types-during-an-application-upgrade"></a>Hozzáadásával vagy eltávolításával szolgáltatástípusok alkalmazásfrissítések során
Ha egy frissítés részeként közzétett alkalmazást egy új szolgáltatástípus ad hozzá, majd az új szolgáltatástípus hozzáadódik az üzembe helyezett alkalmazást. Ilyen frissítés nem érinti a már az alkalmazás részét képező szolgáltatás példányra, de a szolgáltatás típusa, amely hozzá lett adva egy példányát, létre kell hozni az új szolgáltatás típus aktívnak kell lennie (lásd: [New-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps)).

Ehhez hasonlóan a szolgáltatástípusok távolíthatja el egy alkalmazás egy frissítés részeként. Azonban a frissítés folytatása előtt el kell távolítani a to-be eltávolított szolgáltatás típusa az összes szolgáltatás példányát (lásd: [Remove-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricservice?view=azureservicefabricps)).

## <a name="manual-upgrade-mode"></a>Manuális frissítési mód
> [!NOTE]
> A *figyelt* frissítési mód használata ajánlott az összes Service Fabric-frissítések.
> A *UnmonitoredManual* frissítési mód csak akkor sikertelen és felfüggesztett frissítéseket. 
>
>

A *figyelt* mód, a Service Fabric vonatkozik, hogy az alkalmazás kifogástalan állapotú a frissítés előrehaladtával állapotházirendeket. Ha állapotházirendeket nem sikeres, akkor a frissítés felfüggesztett vagy a megadott függően automatikusan visszaállításra *FailureAction*.

A *UnmonitoredManual* módot, az alkalmazás-rendszergazda keresztül metódushívásainak sorrendjét a frissítés teljes vezérléssel rendelkezik. Ebben a módban a következő esetekben hasznos kiértékelése egyéni házirendek alkalmazása, vagy nem hagyományos frissítésével állapotfigyelési teljes egészében kikerülheti (pl. az alkalmazás már szerepel az adatvesztést). Ebben a módban a frissítés fog felfüggessze egyes UD befejezése után, és kell explicit módon folytatható használatával [Resume-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps). Amikor frissítést fel van függesztve, és készen áll a felhasználónak meg kell újítani, megjelenik a frissítési állapot *RollforwardPending* (lásd: [UpgradeState](https://docs.microsoft.com/dotnet/api/system.fabric.applicationupgradestate?view=azure-dotnet)).

Végül a *UnmonitoredAuto* mód hasznos gyors frissítési ismétlések végrehajtása során a szolgáltatás fejlesztési vagy tesztelési, mivel nincs felhasználói bevitel nem szükséges, és nincs alkalmazás állapotának házirendek kiértékelése.

## <a name="upgrade-with-a-diff-package"></a>A diff csomagjának frissítése
Helyett egy teljes alkalmazás csomag kiépítést, frissítéseket is, amely csak a frissített kód/config/data csomag együtt a teljes alkalmazás-jegyzékfájlt tartalmaznak, és hajtsa végre a szolgáltatásjegyzékek diff csomagok kiépítésével hajtható végre. Csak azok a teljes alkalmazás csomagok egy alkalmazást a fürtön kezdeti telepítése szükséges. További frissítések az alkalmazáscsomagok teljes vagy különbözeti csomagok lehet.  

Az alkalmazásjegyzék vagy a különbözeti csomag, amely nem található az alkalmazáscsomagban szolgáltatásjegyzékek hivatkozás automatikusan helyére a jelenleg biztosított verzió.

A diff csomag használatára vonatkozó forgatókönyvek a következők:

* Ha rendelkezik egy nagy alkalmazáscsomagot, amely több service manifest fájlt és/vagy több kódcsomagok, konfigurációs csomagokat vagy adatok csomagok hivatkozik.
* Ha egy központi telepítés rendszer, amely létrehozza a build elrendezés közvetlenül az alkalmazásból folyamatot hozhat létre. Ebben az esetben annak ellenére, hogy a kód nem változott, újonnan létrehozott szerelvények különböző ellenőrzőösszeg beolvasása. A teljes alkalmazás-csomag használatával megköveteli, hogy frissítse az összes kódot csomag verzióját. Diff csomagot használ, csak a módosított fájlokat és adnia a jegyzékfájlok, ha a verzió megváltozott.

Ha egy alkalmazás frissítve van, a Visual Studio használatával, a különbözeti csomag automatikusan van közzétéve. Diff csomagot manuálisan hozhat létre, az alkalmazás jegyzékfájlja és a szolgáltatásjegyzékek frissíteni kell, de csak a módosított csomag szerepelnie kell a végső alkalmazáscsomagot.

Ha például kezdjük a következő alkalmazás (verziószámok ismertetése a könnyű megadva):

```text
app1           1.0.0
  service1     1.0.0
    code       1.0.0
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

Tegyük fel, csak a kódcsomag service1, különbözeti csomagokkal frissíteni szeretne. A frissített alkalmazást az alábbi megváltozik a verzió rendelkezik:

```text
app1           2.0.0      <-- new version
  service1     2.0.0      <-- new version
    code       2.0.0      <-- new version
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

Az alkalmazásjegyzék ebben az esetben 2.0.0-s és a szolgáltatásjegyzék számára, hogy a kód Csomagfrissítés service1 frissítése. Az alkalmazáscsomag a mappa volna az alábbi struktúrával rendelkeznek:

```text
app1/
  service1/
    code/
```

Más szóval általában egy teljes alkalmazás-csomag létrehozása, majd távolítsa el minden olyan kódot/config/adatcsomag mappát, amelynek a verziója nem változott.

## <a name="rolling-back-application-upgrades"></a>Alkalmazásfrissítések visszaállítása

Miközben frissítéseket is előregörgetve a három mód közül az (*figyelt*, *UnmonitoredAuto*, vagy *UnmonitoredManual*), akkor is csak vissza lesz állítva a vagy *UnmonitoredAuto* vagy *UnmonitoredManual* mód. Vissza a működés közbeni *UnmonitoredAuto* módban működik ugyanúgy, mint a működés közbeni előre azzal a kivétellel, hogy az alapértelmezett érték *UpgradeReplicaSetCheckTimeout* eltér - lásd: [alkalmazás Frissítési paraméterek](service-fabric-application-upgrade-parameters.md). Vissza a működés közbeni *UnmonitoredManual* módban működik ugyanúgy, mint a működés közbeni előre, – a visszaállítás felfüggeszti magát az egyes UD befejezése után, és kell explicit módon folytatható használatával [ RESUME-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps) folytatásához a visszaállítás.

Visszagörgetése aktiválható automatikusan Ha a házirendek egy frissítésének *figyelt* módban egy *FailureAction* , *visszaállítási* nem sikeres ( Lásd[Frissítése Alkalmazásparamétereket](service-fabric-application-upgrade-parameters.md)) vagy explicit módon [Start-ServiceFabricApplicationRollback](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationrollback?view=azureservicefabricps).

Az érték a visszaállítás során *UpgradeReplicaSetCheckTimeout* és a mód továbbra is bármikor módosítható [Update-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricapplicationupgrade?view=azureservicefabricps).

## <a name="next-steps"></a>További lépések
[Az alkalmazás használatával a Visual Studio frissítése](service-fabric-application-upgrade-tutorial.md) végigvezeti egy alkalmazás frissítése a Visual Studio használatával.

[Az alkalmazás használatával Powershell frissítése](service-fabric-application-upgrade-tutorial-powershell.md) végigvezeti egy alkalmazás frissítése a PowerShell használatával.

Vezérelheti, hogyan az alkalmazásfrissítések használatával [frissítési paraméterek](service-fabric-application-upgrade-parameters.md).

Hogyan használja az alkalmazásfrissítések kompatibilissé [adatok szerializálása](service-fabric-application-upgrade-data-serialization.md).

Az alkalmazásfrissítések gyakori problémák megoldása szerint hajtsa végre a hivatkozó [Alkalmazásfrissítések hibaelhárítása](service-fabric-application-upgrade-troubleshooting.md).
