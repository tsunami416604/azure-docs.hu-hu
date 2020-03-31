---
title: Szolgáltatáshálófürt erőforrás-kezelője – alkalmazáscsoportok
description: A Service Fabric fürterőforrás-kezelő alkalmazáscsoport-funkcióinak áttekintése
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 988c7ce52125800c16aa785d5b1458604a927ecd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75452149"
---
# <a name="introduction-to-application-groups"></a>Alkalmazáscsoportok – bevezetés
A Service Fabric fürterőforrás-kezelője általában úgy kezeli a fürterőforrásokat, hogy a terhelést [(a Metrikákon](service-fabric-cluster-resource-manager-metrics.md)keresztül ábrázolt) egyenletesen osztja el a fürtön. A Service Fabric kapacitással kezeli a fürt csomópontjainak és a fürt egészének [kapacitását.](service-fabric-cluster-resource-manager-cluster-description.md) Metrikák és a kapacitás számos számítási feladatok, de a minták, amelyek nagy haszna a különböző Service Fabric-alkalmazáspéldányok néha további követelményeket. Például a következőket teheti:

- Foglaljon le némi kapacitást a fürt csomópontjain a szolgáltatások számára néhány elnevezett alkalmazáspéldányon belül
- Az elnevezett alkalmazáspéldányon belüli szolgáltatások által futtatott csomópontok teljes számának korlátozása (ahelyett, hogy szétosztaná őket a teljes fürtön)
- A névvel ellátott alkalmazáspéldány kapacitásainak meghatározása a szolgáltatások számának vagy a benne lévő szolgáltatások teljes erőforrás-felhasználásának korlátozásához

Ezeknek a követelményeknek való megfeleltetésérdekében a Service Fabric fürterőforrás-kezelő támogatja az alkalmazáscsoportok nevű szolgáltatást.

## <a name="limiting-the-maximum-number-of-nodes"></a>A csomópontok maximális számának korlátozása
Az alkalmazáskapacitás legegyszerűbb használati esete az, amikor egy alkalmazáspéldányt egy bizonyos maximális számú csomópontra kell korlátozni. Ez összesíti az adott alkalmazáspéldányon belüli összes szolgáltatást egy meghatározott számú gépre. Az összevonás akkor hasznos, ha az adott elnevezett alkalmazáspéldányon belüli szolgáltatások fizikai erőforrás-használatát próbálja előre jelezni vagy korlátozni. 

Az alábbi képen egy definiált csomópontok maximális számával rendelkező és nem rendelkező alkalmazáspéldány látható:

<center>

![Alkalmazáspéldány a csomópontok maximális számát meghatározó][Image1]
</center>

A bal oldali példában az alkalmazás nem rendelkezik a definiált csomópontok maximális száma, és három szolgáltatással rendelkezik. A fürterőforrás-kezelő az összes replikát hat rendelkezésre álló csomópontra osztotta szét a fürt legjobb egyensúlyának elérése érdekében (ez az alapértelmezett viselkedés). A megfelelő példában azt látjuk, ugyanazt az alkalmazást három csomópontra korlátozva.

A viselkedést vezérlő paraméter neve MaximumNodes. Ez a paraméter beállítható az alkalmazás létrehozása során, vagy frissíthető egy már futó alkalmazáspéldányhoz.

PowerShell

``` posh
New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -MaximumNodes 3
Update-ServiceFabricApplication –ApplicationName fabric:/AppName –MaximumNodes 5
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

A csomópontok készletén belül a fürterőforrás-kezelő nem garantálja, hogy mely szolgáltatásobjektumok kerülnek együtt, vagy mely csomópontok at használja.

## <a name="application-metrics-load-and-capacity"></a>Alkalmazásmetrikák, terhelés és kapacitás
Az alkalmazáscsoportok azt is lehetővé teszik, hogy egy adott elnevezett alkalmazáspéldányhoz társított metrikákat határozzon meg, és az adott alkalmazáspéldány kapacitását ezekhez a metrikákhoz. Az alkalmazásmetrikák lehetővé teszik az adott alkalmazáspéldányon belüli szolgáltatások erőforrás-felhasználásának nyomon követését, lefoglalását és korlátozását.

Minden alkalmazásmetrika esetében két érték állítható be:

- **Teljes alkalmazáskapacitás** – Ez a beállítás egy adott metrika alkalmazás teljes kapacitását jelöli. A fürterőforrás-kezelő nem engedélyezi az alkalmazáspéldányon belül olyan új szolgáltatások létrehozását, amelyek a teljes terhelés túllépését eredményeznék. Tegyük fel például, hogy az alkalmazáspéldány kapacitása 10 volt, és már öt volt. A szolgáltatás létrehozása a teljes alapértelmezett terhelés 10 lenne tilos.
- **Maximális csomópontkapacitás** – Ez a beállítás adja meg az alkalmazás maximális teljes terhelését egyetlen csomóponton. Ha a terhelés túllépi ezt a kapacitást, a fürterőforrás-kezelő áthelyezi a replikákat más csomópontokra, hogy a terhelés csökkenjen.


Powershell:

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

## <a name="reserving-capacity"></a>Kapacitás lefoglalása
Az alkalmazáscsoportok egy másik gyakori használata annak biztosítása, hogy a fürtön belüli erőforrások egy adott alkalmazáspéldány számára legyenek fenntartva. A terület mindig le van foglalva az alkalmazáspéldány létrehozásakor.

Hely foglalása a fürtben az alkalmazás azonnal történik, még akkor is, ha:
- az alkalmazáspéldány létrejött, de még nem rendelkezik szolgáltatásokkal
- az alkalmazáspéldányon belüli szolgáltatások száma minden alkalommal megváltozik 
- a szolgáltatások léteznek, de nem fogyasztják az erőforrásokat 

Erőforrások lefoglalása egy alkalmazáspéldányhoz két további paraméter megadását igényli: *MinimumNodes* és *NodeReservationCapacity*

- **MinimumNodes** – az alkalmazáspéldány futtatásához szükséges csomópontok minimális számát határozza meg.  
- **NodeReservationCapacity** – Ez a beállítás az alkalmazás metrikája szerint. Az érték az alkalmazás számára fenntartott metrika mennyisége bármely csomóponton, ahol az adott alkalmazás szolgáltatásai futnak.

**A MinimumNodes** és a **NodeReservationCapacity kombinálása** garantálja az alkalmazás minimális terhelési foglalását a fürtön belül. Ha a fürtben kevesebb a fennmaradó kapacitás, mint a teljes foglalás szükséges, az alkalmazás létrehozása sikertelen lesz. 

Nézzünk meg egy példát a kapacitásfoglalásra:

<center>

![Alkalmazáspéldányok Fenntartott kapacitást definiáló][Image2]
</center>

A bal oldali példában az alkalmazások nem rendelkeznek definiált alkalmazáskapacitással. A fürterőforrás-kezelő mindent a normál szabályok szerint egyensúlyoz.

A jobb oldali példában tegyük fel, hogy az Application1 a következő beállításokkal jött létre:

- MinimumNodes beállítása kettő
- Alkalmazásmetrika definiálva:
  - NodeReservation20 kapacitása

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

A Service Fabric két csomóponton foglal le kapacitást az Application1 számára, és nem engedélyezi az Application2 szolgáltatásainak ezt a kapacitást, még akkor sem, ha nincs terhelés az Application1-en belüli szolgáltatások által. Ez a fenntartott alkalmazáskapacitás felhasználtnak minősül, és beleszámít az adott csomóponton és a fürtön fennmaradó kapacitásba.  A foglalás azonnal levonásra kerül a fennmaradó fürtkapacitásból, azonban a fenntartott felhasználás csak akkor kerül levonásra egy adott csomópont kapacitásából, ha legalább egy szolgáltatásobjektum van rajta. Ez a későbbi foglalás rugalmasságot és jobb erőforrás-kihasználtságot tesz lehetővé, mivel az erőforrások csak szükség esetén vannak lefoglalva a csomópontokon.

## <a name="obtaining-the-application-load-information"></a>Az alkalmazás terhelési adatainak beszerzése
Minden olyan alkalmazás, amely egy vagy több metrikához definiált alkalmazáskapacitással rendelkezik, beszerezheti a szolgáltatások replikái által jelentett összesített terhelésre vonatkozó információkat.

Powershell:

``` posh
Get-ServiceFabricApplicationLoadInformation –ApplicationName fabric:/MyApplication1
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

Az ApplicationLoad lekérdezés az alkalmazáshoz megadott alkalmazáskapacitással kapcsolatos alapvető információkat adja vissza. Ez az információ tartalmazza a minimális csomópontok és a maximális csomópontok adatait, valamint azt a számot, amelyet az alkalmazás jelenleg foglal. Az egyes alkalmazásterhelési metrikákra vonatkozó információkat is tartalmaz, többek között a következőket:

* Metrika neve: A metrika neve.
* Foglalási kapacitás: Fürtkapacitás, amely az alkalmazás számára fenntartott fürtben van fenntartva.
* Alkalmazás terhelése: Az alkalmazás gyermekreplikáinak teljes terhelése.
* Alkalmazási kapacitás: Az alkalmazásterhelés maximálisan megengedett értéke.

## <a name="removing-application-capacity"></a>Alkalmazáskapacitás eltávolítása
Miután az alkalmazáskapacitás-paraméterek be vannak állítva egy alkalmazáshoz, eltávolíthatók az alkalmazás API-k vagy a PowerShell-parancsmagok használatával. Példa:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 –RemoveApplicationCapacity

```

Ez a parancs eltávolítja az összes alkalmazáskapacitás-kezelési paramétert az alkalmazáspéldányból. Ez magában foglalja a MinimumNodes, MaximumNodes, és az alkalmazás metrikák, ha van ilyen. A parancs hatása azonnali. A parancs befejezése után a fürterőforrás-kezelő az alkalmazások kezeléséhez az alapértelmezett viselkedést használja. Az alkalmazási kapacitás paraméterei `Update-ServiceFabricApplication` / `System.Fabric.FabricClient.ApplicationManagementClient.UpdateApplicationAsync()`ismét megadhatók a segítségével.

### <a name="restrictions-on-application-capacity"></a>Az alkalmazáskapacitáskorlátozásai
Az alkalmazáskapacitás paramétereire számos korlátozás vonatkozik, amelyeket be kell tartani. Érvényesítési hibák esetén nem történik változás.

- Minden egész paraméternek nem negatív számnak kell lennie.
- A minimumNodes soha nem lehet nagyobb, mint a MaximumNodes.
- Ha egy terhelési metrika kapacitása meg van határozva, akkor az alábbi szabályokat kell követniük:
  - A csomópont foglalási kapacitása nem lehet nagyobb, mint a maximális csomópontkapacitás. Például nem korlátozhatja a "CPU" metrika kapacitását a csomóponton két egységre, és megpróbálhat három egységet lefoglalni minden csomóponton.
  - Ha a MaximumNodes meg van adva, akkor a MaximumNodes és a Maximum Node Capacity szorzata nem lehet nagyobb, mint a teljes alkalmazáskapacitás. Tegyük fel például, hogy a "CPU" terhelési metrika maximális csomópontkapacitása nyolc. Tegyük fel azt is, hogy a maximális csomópontokat 10-re állítja. Ebben az esetben a teljes alkalmazáskapacitásnak 80-nál nagyobbnak kell lennie ehhez a terhelési metrikahoz.

A korlátozások az alkalmazás létrehozása és a frissítések során is érvénybe lépnek.

## <a name="how-not-to-use-application-capacity"></a>Az alkalmazáskapacitás használatának lenem lesirántai lesibe
- Ne próbálja meg az Alkalmazáscsoport szolgáltatásaival az alkalmazást a csomópontok egy _adott_ részhalmazára korlátozni. Más szóval megadhatja, hogy az alkalmazás legfeljebb öt csomóponton fut, de nem azt, hogy a fürt ben melyik adott öt csomópont. Az alkalmazás adott csomópontokra való korlátozása a szolgáltatások elhelyezési korlátozásának használatával érhető el.
- Ne próbálja meg használni az alkalmazáskapacitást annak biztosítására, hogy ugyanazon alkalmazásból két szolgáltatás ugyanazon a csomóponton legyen. Ehelyett [használjon affinitási](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md) vagy [elhelyezési megkötéseket.](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)

## <a name="next-steps"></a>További lépések
- A szolgáltatások konfigurálásával kapcsolatos további [tudnivalókért](service-fabric-cluster-resource-manager-configure-services.md)
- Ha meg szeretné tudni, hogy a fürterőforrás-kezelő hogyan kezeli és egyensúlyozza ki a terhelést a fürtben, olvassa el a [terhelés elosztásáról](service-fabric-cluster-resource-manager-balancing.md) szóló cikket.
- Kezdje az elejétől, és [kapjon bevezetést a Szolgáltatásháló-fürt erőforrás-kezelőjébe](service-fabric-cluster-resource-manager-introduction.md)
- A metrikák általános működésével kapcsolatos további információkért olvassa el a [Service Fabric terhelési metrikáit](service-fabric-cluster-resource-manager-metrics.md)
- A fürterőforrás-kezelő számos lehetőséget kínál a fürt leírására. Ha többet szeretne megtudni róluk, olvassa el ezt a cikket a [Service Fabric-fürt leírásáról](service-fabric-cluster-resource-manager-cluster-description.md)

[Image1]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-max-nodes.png
[Image2]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-reserved-capacity.png
