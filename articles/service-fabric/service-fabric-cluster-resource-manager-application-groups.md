---
title: Service Fabric fürt erőforrás-kezelő - alkalmazáscsoportok |} Microsoft Docs
description: A Service Fabric fürt erőforrás-kezelőben alkalmazáscsoport funkcióinak áttekintése
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 4cae2370-77b3-49ce-bf40-030400c4260d
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 215efc1f0597f5199dd37baf4b109d7e76040aae
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="introduction-to-application-groups"></a>Alkalmazáscsoportok bemutatása
A Service Fabric-fürt erőforrás-kezelő általában kezeli a fürterőforrások úgy, hogy a terhelés terjednek (keresztül képviselt [metrikák](service-fabric-cluster-resource-manager-metrics.md)) egyenletesen oszlanak meg a fürtön. A fürt és a fürt csomópontjai keresztül egy teljes kapacitásának kezeli a Service Fabric [kapacitás](service-fabric-cluster-resource-manager-cluster-description.md). Metrikák és a kapacitás működik nagy számos különféle munkaterheléshez azonban néha további követelmények is betöltheti különböző háló alkalmazáspéldányok használó mintákat. Például előfordulhat, hogy szeretné:

- Tartalékkapacitás néhány a fürt csomópontjain a szolgáltatások néhány elnevezett alkalmazáspéldány belül
- A csomópontokra, amelyeket a szolgáltatások egy elnevezett alkalmazáspéldány belül (helyett ezzel azokat a teljes fürt keresztül) futtassa a teljes számának korlátozása
- Adja meg az elnevezett alkalmazáspéldány magát a szolgáltatások vagy a teljes erőforrás-felhasználás, azon belül a szolgáltatások száma korlátozható az kapacitások

Ezek a követelmények teljesítéséhez, a Service Fabric fürt erőforrás-kezelő alkalmazáscsoportok nevezett szolgáltatással támogatja.

## <a name="limiting-the-maximum-number-of-nodes"></a>Csomópontok maximális számának korlátozása
Az alkalmazás kapacitásának legegyszerűbb használata esetében akkor, ha egy alkalmazáspéldányt kell lennie, csak a csomópontok egy bizonyos maximális számát. Ez összesíti az adott alkalmazáspéldány alakzatot gépek meghatározott számú belül minden szolgáltatás. Összevonás akkor hasznos, ha a megszüntetni kívánt előrejelzése vagy fizikai erőforrások felhasználását cap által a szolgáltatások adott elnevezett alkalmazáspéldány belül. 

Az alábbi ábrán egy alkalmazáspéldány rendelkező és anélküli egy meghatározott csomópontok maximális száma:

<center>
![Az alkalmazáspéldány meghatározása csomópontok maximális száma][Image1]
</center>

A bal oldali példában az alkalmazás nem rendelkezik egy definiált csomópontok maximális száma, és nem rendelkezik, mindhárom szolgáltatást. A fürt erőforrás-kezelő van elosztva el az összes replika hat elérhető csomópontok a legjobb egyensúlyt a fürtben (alapértelmezés) eléréséhez. A megfelelő példa látható legfeljebb három csomópont ugyanazt az alkalmazást.

A paraméter, amely meghatározza, ez a viselkedés MaximumNodes nevezik. Ez a paraméter alkalmazás létrehozása során be, vagy egy alkalmazáspéldányt, amelyen már futott frissítve.

PowerShell

``` posh
New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -MaximumNodes 3
Update-ServiceFabricApplication –Name fabric:/AppName –MaximumNodes 5
```

C#

``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";
ad.MaximumNodes = 3;
await fc.ApplicationManager.CreateApplicationAsync(ad);

ApplicationUpdateDescription adUpdate = new ApplicationUpdateDescription(new Uri("fabric:/AppName"));
adUpdate.MaximumNodes = 5;
await fc.ApplicationManager.UpdateApplicationAsync(adUpdate);

```

A csomóponton belül a fürt erőforrás-kezelő nem garantálható, hogy mely szolgáltatás objektumok együtt elhelyezett vagy csomópontok használt beolvasása.

## <a name="application-metrics-load-and-capacity"></a>Alkalmazás metrikákat, a betöltés és a kapacitás
Alkalmazáscsoportok is engedélyezi, hogy egy adott nevű alkalmazáspéldány és az adott alkalmazáspéldány kapacitás e metrikáihoz társított metrikáinak definiálása. Alkalmazás metrikák engedélyezése nyomon követhető, lefoglalni, és korlátozza az erőforrás-felhasználás a szolgáltatások adott alkalmazáspéldány belül.

Minden egyes alkalmazásmetrika vonatkoznak-e két érték beállítható:

- **Összes alkalmazás kapacitás** – ezt a beállítást egy adott mérőszám az alkalmazás teljes kapacitását mutatja. A fürt erőforrás-kezelő nem engedélyezi a új szolgáltatások, amelyek teljes terhelés meghaladja ezt az értéket az alkalmazáspéldány belül létrehozását. Például tegyük fel, az alkalmazáspéldány 10 kapacitású kellett, és már a terheléselosztási öt. A szolgáltatás létrehozásának 10 teljes alapértelmezett terhelésű volna le van tiltva.
- **Maximális csomópont-kapacitás** – ezzel a beállítással az alkalmazás maximális teljes terhelés egyetlen csomópontján. Ha terhelés kerül át ezeket a feladatokat, a fürt erőforrás-kezelő áthelyezése replikák más csomópontok, hogy a terhelés csökkenése.


PowerShell:

``` posh
New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -Metrics @("MetricName:Metric1,MaximumNodeCapacity:100,MaximumApplicationCapacity:1000")
```

C#:

``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";

var appMetric = new ApplicationMetricDescription();
appMetric.Name = "Metric1";
appMetric.TotalApplicationCapacity = 1000;
appMetric.MaximumNodeCapacity = 100;
ad.Metrics.Add(appMetric);
await fc.ApplicationManager.CreateApplicationAsync(ad);
```

## <a name="reserving-capacity"></a>Lefoglal kapacitást
Alkalmazáscsoportok egy másik gyakori felhasználási annak érdekében, hogy egy adott alkalmazáspéldány fenntartott erőforrások a fürtön belül. A terület mindig van fenntartva, amikor az alkalmazáspéldány jön létre.

A fürt terület foglalása az azonnal történik, az alkalmazás akkor is, ha:
- Az alkalmazáspéldány létrejött, de még nem rendelkezik hozzá tartozó szolgáltatások
- Az alkalmazáspéldány belül szolgáltatások száma minden egyes változik 
- a szolgáltatások léteznek, de az erőforrást nem kötött. 

Erőforrások lefoglalása egy alkalmazáspéldányt megadása szükséges, két további paraméterek megadása: *MinimumNodes* és *NodeReservationCapacity*

- **MinimumNodes** -csomóponton, amelyeket az alkalmazáspéldány futtasson-e a minimális számát határozza meg.  
- **NodeReservationCapacity** -metrikáját, az alkalmazás működik ez a beállítás. Az érték azt mutatja, hogy az alkalmazás minden csomópont számára mérőszám ahol, amelyek az adott alkalmazásban a szolgáltatások futnak.

Kombinálásával **MinimumNodes** és **NodeReservationCapacity** biztosítja, hogy az alkalmazás a fürtön belüli minimális terhelés foglalást. Ha nincs kevesebb a fürtben, mint a teljes foglalás fennmaradó kapacitás szükséges, az alkalmazás létrehozása sikertelen lesz. 

Nézzük kapacitásfoglalás példát:

<center>
![Az alkalmazáspéldányok lefoglalt kapacitás meghatározása][Image2]
</center>

A bal oldali Példa alkalmazások nem rendelkeznek egyetlen meghatározott alkalmazás kapacitás. A fürt erőforrás-kezelő normál szabályoknak megfelelően minden egyensúlyba kerüljön.

A jobb oldali példában tegyük fel, hogy a Alkalmaz1 létrejött-e a következő beállításokkal:

- Két MinimumNodes értéke
- Egy alkalmazás definiálva mértéket
  - 20 NodeReservationCapacity

PowerShell

 ``` posh
 New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -MinimumNodes 2 -Metrics @("MetricName:Metric1,NodeReservationCapacity:20")
 ```

C#

 ``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";
ad.MinimumNodes = 2;

var appMetric = new ApplicationMetricDescription();
appMetric.Name = "Metric1";
appMetric.NodeReservationCapacity = 20;

ad.Metrics.Add(appMetric);

await fc.ApplicationManager.CreateApplicationAsync(ad);
```

A Service Fabric két csomópont kapacitást foglal a Alkalmaz1, és nem engedélyezi a szolgáltatások felhasználásához, hogy a kapacitás, akkor sem, ha nem a belső Alkalmaz1 szolgáltatások használnak, időpontjában Alkalmaz2 nevű almappát a. Ez a fenntartott alkalmazás kapacitás tekinthető fel, és csökkenti a kapacitása a csomóponton, és a fürtön belül.  A Foglalás kell vonni a fennmaradó fürt kapacitás azonnal, azonban a fenntartott fogyasztás kell vonni a kapacitás egy adott csomópont csak akkor, ha legalább egy objektum el van helyezve az. A későbbi foglalás lehetővé teszi, hogy a rugalmasság és erőforrás-kihasználást óta erőforrások csak szükség esetén csomópontján vannak lefoglalva.

## <a name="obtaining-the-application-load-information"></a>Az alkalmazás betöltése adatainak lekérése
Minden egyes alkalmazás esetében, amely rendelkezik egy alkalmazás ezt úgy szerezheti be a összesített terhelést, a hozzá tartozó szolgáltatások replikái által jelentett információ egy vagy több metrikák meghatározott kapacitás.

PowerShell:

``` posh
Get-ServiceFabricApplicationLoad –ApplicationName fabric:/MyApplication1
```

C#

``` csharp
var v = await fc.QueryManager.GetApplicationLoadInformationAsync("fabric:/MyApplication1");
var metrics = v.ApplicationLoadMetricInformation;
foreach (ApplicationLoadMetricInformation metric in metrics)
{
    Console.WriteLine(metric.ApplicationCapacity);  //total capacity for this metric in this application instance
    Console.WriteLine(metric.ReservationCapacity);  //reserved capacity for this metric in this application instance
    Console.WriteLine(metric.ApplicationLoad);  //current load for this metric in this application instance
}
```

A ApplicationLoad lekérdezés alkalmazás kapacitás az alkalmazáshoz megadott alapvető információt ad vissza. Ezen információk közé tartozik, a minimális csomópontok és a maximális csomópontszám adatait, és a szám, amelyet az alkalmazás jelenleg elfoglaló van. Minden alkalmazás terhelési metrika kapcsolatos adatokat is tartalmaz többek között:

* Metrika neve: A mérték neve.
* Foglalási kapacitás: Fürt foglalt kapacitást, a fürt ehhez az alkalmazáshoz.
* Alkalmazásterhelés: Az alkalmazás gyermek replikák teljes terhelése.
* Alkalmazás kapacitás: Maximálisan engedélyezett alkalmazás terhelés érték.

## <a name="removing-application-capacity"></a>Alkalmazás kapacitás eltávolítása
Az alkalmazás kapacitásának paraméterei vannak beállítva, a kérelmet, ha azok távolíthatja el frissítés alkalmazás API-k vagy PowerShell-parancsmagok használatával. Példa:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 –RemoveApplicationCapacity

```

Ez a parancs eltávolítja az összes alkalmazás kapacitás paraméterek az alkalmazáspéldány. Ez magában foglalja MinimumNodes, maximumnodes értéke és az alkalmazás metrikákat, ha van ilyen. A parancs a hatása az azonnali. Ez a parancs befejezése után a a fürt erőforrás-kezelő az alkalmazások kezelésére az alapértelmezett viselkedést alkalmazza. Alkalmazás kapacitás paraméter adható újra keresztül `Update-ServiceFabricApplication` / `System.Fabric.FabricClient.ApplicationManagementClient.UpdateApplicationAsync()`.

### <a name="restrictions-on-application-capacity"></a>Az alkalmazás kapacitás korlátozások
Nincsenek több korlátozások figyelembe kell venni a alkalmazás kapacitás paramétereket. Érvényesítési hiba esetén nem kerül sor.

- Minden egész paraméterek nem negatív számnak kell lennie.
- MinimumNodes soha nem lehet nagyobb, mint a maximumnodes értéke.
- Ha a terhelési metrika kapacitások vannak meghatározva, majd kell követniük ezeket a szabályokat:
  - Csomópont foglalási kapacitás nem lehet nagyobb, mint a maximális csomópont-kapacitás. Például nem korlátozza a metrika "CPU" két egységhez a csomóponton a kapacitás, és próbáljon meg minden egyes csomóponton három egység.
  - Ha maximumnodes értéke meg van adva, majd a termék MaximumNodes és a maximális csomópont-kapacitás nem lehet nagyobb, mint a teljes alkalmazás kapacitás. Ha tegyük fel például, "CPU" értéke 8 terhelési metrika csomópont maximális kapacitását. Tételezzük is fel, a csomópontok maximális száma 10-re. Teljes alkalmazás kapacitás ebben az esetben a terhelési metrika 80-as nagyobbnak kell lennie.

A korlátozások érvényesek lesznek, mind az alkalmazás létrehozása és a frissítések során.

## <a name="how-not-to-use-application-capacity"></a>Hogyan nem használható az alkalmazás kapacitása
- Ne próbálja meg az alkalmazáscsoport szolgáltatásainak használatával korlátozhatja az alkalmazás egy _adott_ csomópontok részhalmaza. Más szóval megadhatja, hogy az alkalmazás fut, legfeljebb öt csomópont, de a fürt nem adott öt csomópontok. Egy adott csomópont alkalmazást korlátozási elérhető placement Constraints korlátozásokat szolgáltatások használatával.
- Ne próbálja meg az alkalmazás kapacitásának segítségével győződjön meg arról, hogy ugyanazt az alkalmazást a két szolgáltatás ugyanazon a csomóponton kerülnek. Ehelyett használja [affinitás](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md) vagy [placement Constraints korlátozásokat](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).

## <a name="next-steps"></a>További lépések
- A szolgáltatások konfigurálásáról [további információ a szolgáltatások konfigurálása](service-fabric-cluster-resource-manager-configure-services.md)
- Hogyan kezeli a fürt erőforrás-kezelő, és elosztja a terhelést a fürt kapcsolatos további tudnivalókért tekintse meg a cikk a [terheléselosztás](service-fabric-cluster-resource-manager-balancing.md)
- Indítsa el az elejétől és [Bevezetés a Service Fabric fürt Resource Manager](service-fabric-cluster-resource-manager-introduction.md)
- További tájékoztatást a metrikák működése általában olvassa a [Service Fabric terheléselosztási metrikák](service-fabric-cluster-resource-manager-metrics.md)
- A fürt erőforrás-kezelő rendelkezik a fürt leíró számos lehetőséget. További információkért róluk, tekintse meg a cikk a [leíró a Service Fabric-fürt](service-fabric-cluster-resource-manager-cluster-description.md)

[Image1]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-max-nodes.png
[Image2]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-reserved-capacity.png
