---
title: Speciális alkalmazásfrissítési témakörök
description: Ez a cikk a Service Fabric-alkalmazások frissítésével kapcsolatos néhány speciális témakört ismerteti.
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 182ab6dc1663e160561b8941ebf3a36b5af3d950
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422811"
---
# <a name="service-fabric-application-upgrade-advanced-topics"></a>A Service Fabric alkalmazásfrissítése: Speciális témakörök

## <a name="add-or-remove-service-types-during-an-application-upgrade"></a>Szolgáltatástípusok hozzáadása vagy eltávolítása alkalmazásfrissítés közben

Ha egy frissítés részeként egy közzétett alkalmazáshoz új szolgáltatástípust ad hozzá, akkor az új szolgáltatástípus hozzáadódik az üzembe helyezett alkalmazáshoz. Az ilyen frissítés nem érinti azokat a szolgáltatáspéldányokat, amelyek már az alkalmazás részét képezik, de létre kell hozni az új szolgáltatástípus aktívvá válásához hozzáadott szolgáltatástípus egy példányát (lásd: [New-ServiceFabricService).](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps)

Hasonlóképpen a szolgáltatástípusok is eltávolíthatók az alkalmazásból a frissítés részeként. A frissítés folytatása előtt azonban el kell távolítani a eltávolítandó szolgáltatástípus összes szolgáltatáspéldányát [(lásd: Remove-ServiceFabricService).](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricservice?view=azureservicefabricps)

## <a name="avoid-connection-drops-during-stateless-service-planned-downtime-preview"></a>A kapcsolatleejtés elkerülése állapot nélküli szolgáltatás tervezett állásidejének (előzetes verzió)

A tervezett állapotmentes példány állásidők, például az alkalmazás/fürt frissítése vagy a csomópont-deaktiválás esetén a kapcsolatok megszakadhatnak a kitett végpont eltávolítása miatt a példány leállítása után, ami kényszerített kapcsolatlezárásokat eredményez.

Ennek elkerülése érdekében konfigurálja a *RequestDrain* (előzetes verzió) szolgáltatást úgy, hogy egy *példány közeli késleltetési időtartamot* ad hozzá a szolgáltatáskonfigurációban, hogy lehetővé tegye a kiürítést, miközben a fürt más szolgáltatásaitól érkező kéréseket kap, és fordított proxyt használ, vagy az API feloldása értesítési modellt használ a végpontok frissítéséhez. Ez biztosítja, hogy az állapotnélküli példány által hirdetett végpontot a rendszer eltávolítja a késés megkezdése *előtt* a példány bezárása előtt. Ez a késleltetés lehetővé teszi, hogy a meglévő kérelmek kiürítése kecsesen, mielőtt a példány ténylegesen leáll. Az ügyfelek a késleltetés indításakor egy visszahívási függvény értesítik a végpont változásáról, hogy újra feloldhassák a végpontot, és elkerülhessék, hogy új kérelmeket küldjenek a leálló példánynak.

### <a name="service-configuration"></a>Szolgáltatás konfigurációja

A késés konfigurálása többféleképpen is konfigurálható a szolgáltatási oldalon.

 * **Új szolgáltatás létrehozásakor**adja `-InstanceCloseDelayDuration`meg a következőt:

    ```powershell
    New-ServiceFabricService -Stateless [-ServiceName] <Uri> -InstanceCloseDelayDuration <TimeSpan>`
    ```

 * **A szolgáltatás nak az alkalmazásjegyzék alapértelmezett szakaszában történő definiálása kor**rendelje hozzá a `InstanceCloseDelayDurationSeconds` tulajdonságot:

    ```xml
          <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]" InstanceCloseDelayDurationSeconds="15">
              <SingletonPartition />
          </StatelessService>
    ```

 * **Meglévő szolgáltatás frissítésekor**adjon `-InstanceCloseDelayDuration`meg egy:

    ```powershell
    Update-ServiceFabricService [-Stateless] [-ServiceName] <Uri> [-InstanceCloseDelayDuration <TimeSpan>]`
    ```

### <a name="client-configuration"></a>Ügyfél-konfiguráció

Ha értesítést szeretne kapni egy végpont változásáról, az ügyfeleknek regisztrálniuk kell egy visszahívást a [ServiceNotificationFilterDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.servicenotificationfilterdescription)témakörben.
A változásértesítés azt jelzi, hogy a végpontok megváltoztak, az ügyfélnek újra fel kell oldania a végpontokat, és nem kell használnia azokat a végpontokat, amelyek már nem hirdetettek, mivel hamarosan lefognak menni.

### <a name="optional-upgrade-overrides"></a>Választható frissítési felülbírálások

A szolgáltatásonkénti alapértelmezett késleltetési időtartamok beállításán kívül az alkalmazás/fürt`InstanceCloseDelayDurationSec`frissítése során is felülbírálhatja a késleltetést ugyanazzal a ( ) beállítással:

```powershell
Start-ServiceFabricApplicationUpgrade [-ApplicationName] <Uri> [-ApplicationTypeVersion] <String> [-InstanceCloseDelayDurationSec <UInt32>]

Start-ServiceFabricClusterUpgrade [-CodePackageVersion] <String> [-ClusterManifestVersion] <String> [-InstanceCloseDelayDurationSec <UInt32>]
```

A kés és a késleltetés időtartama csak a meghívott frissítési példányra vonatkozik, és egyébként nem módosítja az egyes szolgáltatáskésleltetési konfigurációkat. Ezzel például megadhatja `0` a késést, hogy kihagyja az előre beállított frissítési késéseket.

> [!NOTE]
> A kérelmek kiürítése nem kerül a kérelmek az Azure Load balancer nem kerül betöltésére. A beállítás nem teljesül, ha a hívó szolgáltatás panaszalapú megoldást használ.
>
>

> [!NOTE]
> Ez a szolgáltatás konfigurálható a meglévő szolgáltatások segítségével Update-ServiceFabricService parancsmag a fent említettek szerint, ha a fürtkód verziója 7.1.XXX vagy annál magasabb.
>
>

## <a name="manual-upgrade-mode"></a>Kézi frissítési mód

> [!NOTE]
> A *figyelt* frissítési mód ajánlott minden Service Fabric-frissítések.
> A *Nem figyelt manuális* frissítési módot csak sikertelen vagy felfüggesztett frissítések esetén kell figyelembe venni. 
>
>

*Figyelt* módban a Service Fabric állapotházirendeket alkalmaz annak érdekében, hogy az alkalmazás kifogástalan állapotban legyen a frissítés előrehaladtával. Ha az állapotházirendek sérülnek, akkor a frissítés vagy fel van függesztve, vagy automatikusan visszaállítható a megadott *FailureAction függvényben.*

*Nem figyelt kézi* módban az alkalmazás rendszergazdája teljes mértékben szabályozhatja a frissítés előrehaladását. Ez a mód akkor hasznos, ha egyéni állapotértékelési szabályzatokat alkalmaz, vagy nem hagyományos frissítéseket hajt végre az állapotfigyelés teljes megkerülése érdekében (pl. az alkalmazás már adatvesztésben van). Az ebben a módban futó frissítés az egyes UD-ok befejezése után felfüggeszti magát, és explicit módon újra kell indítani a [Resume-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps)használatával. Ha egy frissítés felfüggesztésre kerül, és készen áll a felhasználó általi folytatásra, a frissítési állapota *rollforwardPending* (lásd [UpgradeState)](https://docs.microsoft.com/dotnet/api/system.fabric.applicationupgradestate?view=azure-dotnet)jelenik meg.

Végül a *Nem figyelt automatikus* mód a szolgáltatásfejlesztés vagy tesztelés során a gyors frissítési ismétlések végrehajtásához hasznos, mivel nincs szükség felhasználói beavatkozásra, és nincs kiértékelt alkalmazásállapot-házirend.

## <a name="upgrade-with-a-diff-package"></a>Frissítés diff csomaggal

A teljes alkalmazáscsomag kiépítése helyett a frissítések et is el lehet végezni olyan diff-csomagok kiépítésével, amelyek csak a frissített kód/konfigurációs/adatcsomagokat, valamint a teljes alkalmazásjegyzéket és a teljes szolgáltatásjegyzékeket tartalmazzák. A teljes alkalmazáscsomagok csak az alkalmazás fürtre történő kezdeti telepítéséhez szükségesek. A későbbi frissítések lehetnek teljes alkalmazáscsomagokból vagy diff-csomagokból.  

Az alkalmazásjegyzékben vagy a szolgáltatásjegyzékben lévő olyan diff-csomag hivatkozásait, amelyek nem találhatók meg az alkalmazáscsomagban, automatikusan lecseréli a jelenleg kiépített verzióra.

A különbözeti csomagok használatának forgatókönyvei a következők:

* Ha olyan nagy alkalmazáscsomaggal rendelkezik, amely több szolgáltatásjegyzékfájlra és/vagy több kódcsomagra, konfigurációs csomagokra vagy adatcsomagokra hivatkozik.
* Ha olyan központi telepítési rendszerrel rendelkezik, amely közvetlenül az alkalmazásösszeállítási folyamatból hozza létre a buildelrendezést. Ebben az esetben, annak ellenére, hogy a kód nem változott, az újonnan létrehozott szerelvények egy másik ellenőrzőösszeget kapnak. A teljes alkalmazáscsomag használata szükséges lenne a verzió frissítése az összes kódcsomagon. Egy diff csomag használatával csak a megváltozott fájlokat és a jegyzékfájlokat, ahol a verzió megváltozott.

Amikor egy alkalmazást a Visual Studio használatával frissít, a rendszer automatikusan közzéteszi a különbözeti csomagot. A diff-csomag manuális létrehozásához az alkalmazásjegyzéket és a szolgáltatásjegyzékeket frissíteni kell, de csak a módosított csomagokat kell tartalmaznia a végleges alkalmazáscsomagban.

Kezdjük például a következő alkalmazással (a könnyebb megértés érdekében biztosított verziószámok):

```text
app1           1.0.0
  service1     1.0.0
    code       1.0.0
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

Tegyük fel, hogy csak a szolgáltatás kódcsomagját1 szeretné frissíteni egy diff csomag használatával. A frissített alkalmazás a következő verzióváltozásokat használja:

```text
app1           2.0.0      <-- new version
  service1     2.0.0      <-- new version
    code       2.0.0      <-- new version
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

Ebben az esetben frissíti az alkalmazás jegyzékfájl2.0.0 és a service1 szolgáltatás jegyzékfájlja, hogy tükrözze a kódcsomag frissítése. Az alkalmazáscsomag mappája a következő struktúrával rendelkezik:

```text
app1/
  service1/
    code/
```

Más szóval hozzon létre egy teljes alkalmazáscsomagot a szokásos módon, majd távolítsa el azokat a kód/konfigurációs/adatcsomag mappákat, amelyek verziószáma nem változott.

## <a name="upgrade-application-parameters-independently-of-version"></a>Alkalmazásparaméterek frissítése a verziótól függetlenül

Néha kívánatos, hogy módosítsa a paramétereket a Service Fabric-alkalmazás a jegyzékfájl-verzió módosítása nélkül. Ez kényelmesen elvégezhető a **-ApplicationParameter** jelző és a **Start-ServiceFabricApplicationUpgrade** Azure Service Fabric PowerShell-parancsmag használatával. Tegyük fel, hogy egy Service Fabric-alkalmazás a következő tulajdonságokkal rendelkezik:

```PowerShell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/Application1

ApplicationName        : fabric:/Application1
ApplicationTypeName    : Application1Type
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : { "ImportantParameter" = "1"; "NewParameter" = "testBefore" }
```

Most frissítse az alkalmazást a **Start-ServiceFabricApplicationUpgrade** parancsmag használatával. Ebben a példában egy figyelt frissítés látható, de egy nem figyelt frissítés is használható. A parancsmag által elfogadott jelzők teljes leírását az [Azure Service Fabric PowerShell modulhivatkozásában](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps#parameters) olvassa el.

```PowerShell
PS C:\> $appParams = @{ "ImportantParameter" = "2"; "NewParameter" = "testAfter"}

PS C:\> Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/Application1 -ApplicationTypeVers
ion 1.0.0 -ApplicationParameter $appParams -Monitored

```

A frissítés után ellenőrizze, hogy az alkalmazás rendelkezik-e a frissített paraméterekkel és ugyanazzal a verzióval:

```PowerShell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/Application1

ApplicationName        : fabric:/Application1
ApplicationTypeName    : Application1Type
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : { "ImportantParameter" = "2"; "NewParameter" = "testAfter" }
```

## <a name="roll-back-application-upgrades"></a>Alkalmazásfrissítések visszaállítása

Míg a frissítések a három mód *(Figyelt*, *UnmonitoredAuto*vagy *UnmonitoredManual)* egyikében továbbíthatók, csak *UnmonitoredAuto* vagy *UnmonitoredManual* módban állíthatók vissza. A *Nem figyelt automatikus* módban történő visszaállítás ugyanúgy működik, mint a görgetés, azzal a kivétellel, hogy az *UpgradeReplicaSetCheckTimeout* alapértelmezett értéke eltérő - [lásd: Alkalmazásfrissítési paraméterek](service-fabric-application-upgrade-parameters.md). A *Nem figyeltkézi* módban történő visszaállítás ugyanúgy működik, mint a görgetés – a visszaállítás az egyes UD-k befejezése után felfüggeszti magát, és explicit módon újra kell indítani a [Resume-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps) használatával a visszaállítás folytatásához.

A visszaállításautomatikusan elindítható, ha egy figyelt módban a *visszaállítás* *műveletével* *rendelkező frissítés* állapotházirendjesérül (lásd: [Alkalmazásfrissítési paraméterek](service-fabric-application-upgrade-parameters.md)) vagy explicit módon a [Start-ServiceFabricApplicationRollback](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationrollback?view=azureservicefabricps)parancsot használja.

A visszaállítás során az *UpdateReplicaSetCheckTimeout* és a mód értéke bármikor módosítható az [Update-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricapplicationupgrade?view=azureservicefabricps)segítségével.

## <a name="next-steps"></a>További lépések
[Az alkalmazás Visual Studio használatával történő frissítése](service-fabric-application-upgrade-tutorial.md) végigvezeti az alkalmazás frissítésén a Visual Studió használatával.

[Az alkalmazás PowerShell használatával történő frissítése](service-fabric-application-upgrade-tutorial-powershell.md) végigvezeti a PowerShell használatával történő alkalmazásfrissítésen.

A frissítési paraméterek használatával szabályozhatja, hogy az alkalmazás hogyan [frissítsen.](service-fabric-application-upgrade-parameters.md)

Tegye kompatibilissé az alkalmazásfrissítéseket az [adatszerializálás](service-fabric-application-upgrade-data-serialization.md)használatának elsajátításával.

Az alkalmazásfrissítések gyakori problémáinak megoldása az [Alkalmazásfrissítések hibaelhárítása](service-fabric-application-upgrade-troubleshooting.md)című témakör lépéseire hivatkozva.
