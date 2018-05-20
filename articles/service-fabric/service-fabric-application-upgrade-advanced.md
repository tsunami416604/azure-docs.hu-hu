---
title: Speciális alkalmazás frissítési témakörök |} Microsoft Docs
description: Ez a cikk ismertet néhány speciális témakörök vonatkozó frissítése a Service Fabric-alkalmazás.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: e29585ff-e96f-46f4-a07f-6682bbe63281
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 168d944f72c1409b5b69c9ab7c07f7fcfa04c7c8
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="service-fabric-application-upgrade-advanced-topics"></a>A Service Fabric az alkalmazásfrissítés: speciális kapcsolatos témakörök
## <a name="adding-or-removing-service-types-during-an-application-upgrade"></a>Hozzáadásával vagy eltávolításával szolgáltatástípusok alkalmazás frissítéskor
Ha egy frissítés részeként közzétett alkalmazást egy új szolgáltatás típusának ad hozzá, majd az új típus hozzáadódik a telepített alkalmazás. Ilyen frissítés nincs hatással a szolgáltatás-példányra, amelyek már az alkalmazás részei voltak, de a szolgáltatás típusú vettek fel kell létrehozni az új service Type aktívnak kell lennie (lásd: [New-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps)).

Ehhez hasonlóan szolgáltatástípusok távolíthatók el az alkalmazás frissítés részeként. Azonban a frissítés folytatása előtt el kell távolítani a service to-be eltávolított típusú minden szolgáltatáspéldány (lásd: [Remove-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricservice?view=azureservicefabricps)).

## <a name="manual-upgrade-mode"></a>Manuális frissítési módra
> [!NOTE]
> A *figyelt* frissítési mód használata ajánlott az összes Service Fabric-frissítések.
> A *UnmonitoredManual* frissítési mód csak a frissítések sikertelen vagy felfüggesztett tekinteni. 
>
>

A *figyelt* módban, a Service Fabric annak biztosításához, hogy az alkalmazás a frissítési előrehaladtával kifogástalan állapotát házirendet alkalmaz. Ha állapotházirendeket nem sikeres, akkor a frissítés felfüggeszti vagy attól függően, hogy a megadott automatikus visszaállításra *FailureAction*.

A *UnmonitoredManual* módot, az alkalmazás-rendszergazda teljes vezérléssel rendelkezik felett a frissítés állapotát. Ebben a módban akkor hasznos, ha egyéni értékelési házirendek alkalmazása, vagy nem hagyományos frissítésével állapotfigyelési teljesen elkerülésére (pl. az alkalmazás már szerepel az adatveszteség). Ebben a módban fut a frissítés fog felfüggeszteni saját magát minden UD befejezése után, és kell explicit módon folytatható használatával [Resume-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps). Ha egy frissítés fel van függesztve, és készen áll a felhasználó által folytatható, a frissítési állapotát megjeleníti *RollforwardPending* (lásd: [UpgradeState](https://docs.microsoft.com/dotnet/api/system.fabric.applicationupgradestate?view=azure-dotnet)).

Végezetül a *UnmonitoredAuto* mód akkor hasznos, ha a szolgáltatás fejlesztési vagy tesztelési során végrehajtaná gyors frissítési ismétlési, mert nincs felhasználói bevitel szükséges, ezért nem alkalmazás házirendek kiértékelése.

## <a name="upgrade-with-a-diff-package"></a>A diff csomag frissítése
Helyett használt dinamikus kiosztásnál a teljes alkalmazáscsomag frissítések is különbözeti csomagok, melyek csak a frissített kód/config/adatok csomagokat és a teljes alkalmazás jegyzékfájlja, és végezze el a szolgáltatás jegyzékfájlokban úgy hajtható végre. Csak azok a teljes alkalmazás csomagok egy alkalmazást a fürt kezdeti telepítése szükséges. További frissítések alkalmazáscsomagok teljes vagy különbözeti csomagok lehet.  

A jelenleg telepített verziója automatikusan felülírja az alkalmazásjegyzék vagy a szolgáltatás jegyzékfájlokat egy különbségi csomag, amely nem található az alkalmazáscsomag minden hivatkozás.

A különbözeti csomag használatára vonatkozó forgatókönyvek a következők:

* Ha rendelkezik a nagy alkalmazáscsomagok, amelyek több service manifest fájl és/vagy több kód csomagok, konfigurációs csomagokat vagy adatok csomagok hivatkozik.
* Ha egy központi telepítési rendszer közvetlenül az alkalmazásból a build elrendezés generáló build folyamat. Ebben az esetben annak ellenére, hogy a kód nem változott, újonnan beépített szerelvények egy másik ellenőrzőösszeg beolvasása. A teljes alkalmazás csomag használata, hogy frissítse az összes kódot csomag igényelnének. Diff csomagot használ, csak megadja a módosított fájlokra és a jegyzékfájlt, amelyekben módosult a verzióra.

Ha egy alkalmazás frissítve van, a Visual Studio használatával, különbözeti csomag automatikusan van közzétéve. Hozzon létre manuálisan egy különbségi csomag, az alkalmazás jegyzékében és a szolgáltatás jegyzékfájlokat frissíteni kell, de csak a módosított csomagok szerepelnie kell a végső alkalmazáscsomagot.

Például kezdjük azokkal a következő alkalmazás (verziószámok ismertetése könnyű előírt):

```text
app1           1.0.0
  service1     1.0.0
    code       1.0.0
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

Tételezzük fel, hogy csak a kód csomag service1 a különbözeti csomagot szeretne. A frissített alkalmazás a következő verzió módosításokat tartalmaz:

```text
app1           2.0.0      <-- new version
  service1     2.0.0      <-- new version
    code       2.0.0      <-- new version
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

Ebben az esetben frissíti az alkalmazás jegyzékében 2.0.0 és a szolgáltatás jegyzékfájl service1, hogy tükrözze a csomag frissítése. A mappa az alkalmazáscsomag az alábbi szerkezettel rendelkezik:

```text
app1/
  service1/
    code/
```

Ez azt jelenti általában a teljes alkalmazás-csomag létrehozása, majd távolítsa el a kód/config/adatcsomag mappákat, amelynek a verziója nem változott.

## <a name="rolling-back-application-upgrades"></a>Alkalmazásfrissítések visszaállítása

Amíg frissítéseket is előregörgetve három módban (*figyelt*, *UnmonitoredAuto*, vagy *UnmonitoredManual*), akkor is csak állítható vissza a vagy *UnmonitoredAuto* vagy *UnmonitoredManual* mód. Vissza a működés közbeni *UnmonitoredAuto* mód működik ugyanúgy, mint a működés közbeni előre azzal a kivétellel, hogy az alapértelmezett érték *UpgradeReplicaSetCheckTimeout* eltérő - lásd: [alkalmazás Frissítse a paraméterek](service-fabric-application-upgrade-parameters.md). Vissza a működés közbeni *UnmonitoredManual* mód működik ugyanúgy, mint a működés közbeni előre - visszaállításának fog felfüggeszteni saját magát minden UD befejezése után, és kell explicit módon folytatható használatával [ RESUME-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps) visszaállításának folytatása előtt.

Visszagörgetése is elindítható a automatikusan Ha a házirendek, a frissítés *figyelt* módban egy *FailureAction* a *visszaállítási* nem sikeres ( Lásd[Alkalmazás frissítése paraméterei](service-fabric-application-upgrade-parameters.md)) vagy explicit módon [Start-ServiceFabricApplicationRollback](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationrollback?view=azureservicefabricps).

Visszaállítás értékének során *UpgradeReplicaSetCheckTimeout* és a mód továbbra is módosíthatja bármely használatával [frissítés-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricapplicationupgrade?view=azureservicefabricps).

## <a name="next-steps"></a>További lépések
[Az alkalmazás használata a Visual Studio frissítése](service-fabric-application-upgrade-tutorial.md) végigvezeti Önt az alkalmazásfrissítés Visual Studio használatával.

[Az alkalmazás használatával Powershell frissítése](service-fabric-application-upgrade-tutorial-powershell.md) végigvezeti Önt az alkalmazásfrissítés PowerShell használatával.

Szabályozhatja, hogy az alkalmazás használatával frissíti [frissítése paraméterek](service-fabric-application-upgrade-parameters.md).

Az alkalmazásfrissítéseket által használatának megtanulása kompatibilissé [Adatszerializálás](service-fabric-application-upgrade-data-serialization.md).

Alkalmazásfrissítések gyakori problémáinak megoldásához hajtsa végre a hivatkozással [Alkalmazásfrissítések hibaelhárítási](service-fabric-application-upgrade-troubleshooting.md).
