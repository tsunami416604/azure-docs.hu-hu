---
title: Service Fabric a fürterőforrás-kezelő – alkalmazáscsoportok |} A Microsoft Docs
description: Az alkalmazáscsoport funkciókat az a Service Fabric fürterőforrás-kezelő áttekintése
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: 4cae2370-77b3-49ce-bf40-030400c4260d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 7e90dc00a8e042e48d8016e25dda04c15ce9f619
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58670634"
---
# <a name="introduction-to-application-groups"></a>Alkalmazáscsoportok bemutatása
A Service Fabric a fürterőforrás-kezelő általában fürterőforrások kezeli a terhelés elosztható (keresztül jelölt [metrikák](service-fabric-cluster-resource-manager-metrics.md)) egyenletesen oszlanak meg a fürtön. A Service Fabric kezeli a fürt és a fürt csomópontjainak keresztül teljes kapacitásának [kapacitás](service-fabric-cluster-resource-manager-cluster-description.md). Metrikák és a kapacitás működik kiválóan alkalmas a számos számítási feladatokhoz, de néha további követelmények is betöltheti a Service Fabric alkalmazás különböző példányait használó mintákat. Például előfordulhat, hogy szeretné:

- Tartalékkapacitás néhány a csomóponton a fürtben az egyes névvel ellátott alkalmazáspéldány szolgáltatásai
- Korlátozza a csomópontok egy elnevezett alkalmazáspéldány szolgáltatásai rendszeren futó (nem terjedhetnek őket az egész fürt keresztül) teljes száma
- Kapacitások meghatározni az elnevezett alkalmazáspéldány magát a szolgáltatások vagy a szolgáltatások, teljes erőforrás-felhasználásának számának korlátozása

Mindezen követelmények teljesítése érdekében a Service Fabric-fürt Resource Manager támogatja a szolgáltatás alkalmazásbiztonsági csoportokból.

## <a name="limiting-the-maximum-number-of-nodes"></a>Korlátozza a csomópontok maximális száma
A legegyszerűbb megmaradjon az alkalmazás kapacitásának akkor, ha a alkalmazáspéldány kell lennie a csomópontok egy bizonyos maximális száma korlátozott. Ez összesíti a minden szolgáltatás beállítása a gépek száma az alakzatot a alkalmazáspéldány belül. Összevonás akkor hasznos, ha szeretne előrejelzése vagy elnevezett alkalmazás példányhoz tartozó szolgáltatások által költségplafont, fizikai erőforrások felhasználása. 

Az alábbi képen egy alkalmazáspéldány rendelkező és anélküli egy meghatározott csomópontok maximális száma:

<center>

![Alkalmazás-példány meghatározása a csomópontok maximális száma][Image1]
</center>

A bal oldali példában az alkalmazás nem rendelkezik egy meghatározott csomópontok maximális számát, és rendelkezik mindhárom szolgáltatást. A fürterőforrás-kezelő van elosztva ki az összes replika hat elérhető csomópontok a fürtben (alapértelmezés) a legjobb egyenleg elérése érdekében. A jobb oldali példában ugyanannak az alkalmazásnak legfeljebb három csomópont látható.

A paraméter, ezt a viselkedést vezérlő MaximumNodes nevezzük. Ez a paraméter alkalmazás létrehozása során, vagy frissíteni egy alkalmazás-példányhoz, amelyen már futott.

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

Belüli csomópontok készletét a fürterőforrás-kezelő nem garantálja, mely szolgáltatás objektumok együtt elhelyezett, vagy melyik csomópontokon használt beolvasása.

## <a name="application-metrics-load-and-capacity"></a>Alkalmazásmetrikák, betöltési és kapacitás
Alkalmazáscsoportok is lehetővé teszi, hogy egy adott nevű alkalmazás-példányt és az adott alkalmazáspéldány kapacitás követhesse a társított-metrikáinak definiálása. Alkalmazásmetrikák nyomon követésére, foglaljon le, és korlátozza az erőforrás-használat az adott alkalmazáspéldány belüli szolgáltatások teszi lehetővé.

Minden alkalmazás metrika van két érték beállítható:

- **Teljes kapacitás alkalmazás** – ezt a beállítást jelöli az alkalmazást egy adott mérőszám a teljes kapacitását. A fürterőforrás-kezelő lehetetlenné teszi, hogy ez az érték meghaladja a teljes terhelés miatt az alkalmazáspéldány belül minden olyan új szolgáltatás létrehozása. Például tegyük fel, az alkalmazáspéldány kellett 10 kapacitását, és már volt az öt betöltése. Szolgáltatás létrehozása egy teljes alapértelmezett terheléssel 10 lenne kell nem engedélyezett.
- **Maximális csomópont-kapacitás** – ezzel a beállítással megadhatja az alkalmazás maximális összesített terhelés egyetlen csomóponton. Ha a terhelést a kapacitások keresztül haladnak, a fürterőforrás-kezelő áthelyezi replikák más csomópontokon, hogy a terhelés csökkenése.


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

## <a name="reserving-capacity"></a>Kapacitás foglalása
Egy másik gyakori alkalmazási alkalmazáscsoportok számára, hogy győződjön meg arról, hogy a fürtön belüli erőforrások egy adott alkalmazáspéldány számára vannak fenntartva. A hely mindig van fenntartva, ha az alkalmazáspéldány jön létre.

A fürtben lévő terület lefoglalása esetében azonnal történik, az alkalmazás akkor is, ha:
- Az alkalmazáspéldány hoz létre, de még nem rendelkezik minden benne lévő szolgáltatások
- az alkalmazás példány módosítások minden egyes szolgáltatásai száma 
- a szolgáltatások létezik, de nem használ az erőforrások 

Egy alkalmazáspéldány erőforrások lefoglalását meg kell adni a két további paraméterek: *MinimumNodes* és *NodeReservationCapacity*

- **MinimumNodes** – határozza meg, hogy az alkalmazáspéldány futhat a csomópontok minimális száma.  
- **NodeReservationCapacity** – Ez a beállítás nem az alkalmazás metrikánként. Az érték azt mutatja, hogy az alkalmazás bármely csomópontján fenntartott mérőszám ahol, amelyek az adott alkalmazásban a szolgáltatások futnak.

Kombinálva **MinimumNodes** és **NodeReservationCapacity** garantálja az alkalmazást a fürtön belüli minimális terhelés foglalás. Ha nincs kevesebb a fürtben, mint a teljes foglalás fennmaradó kapacitás szükséges, az alkalmazás létrehozása meghiúsul. 

Lássunk erre egy példát kapacitás Foglalás:

<center>

![Az alkalmazáspéldányok lefoglalt kapacitás meghatározása][Image2]
</center>

A bal oldali példában alkalmazások nem kell minden olyan alkalmazás kapacitásának meghatározott. A fürterőforrás-kezelő elosztja minden normál szabályok szerint.

A példában a jobb oldali tegyük fel, hogy Alkalmaz1 létrejött-e a következő beállításokkal:

- Két MinimumNodes beállítása
- Egy alkalmazás meghatározott metrika
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

A Service Fabric foglal le a két csomópont kapacitását a Alkalmaz1, és nem engedélyezi a szolgáltatások felhasználásához a kapacitást, akkor sem, ha nincs terhelés vett Alkalmaz1 belül a szolgáltatások által egyidejűleg Alkalmaz2 nevű almappát a. A fenntartott alkalmazás kapacitásának számít a felhasznált és beleszámít a fennmaradó kapacitás ezen a csomóponton, és a fürtön belül.  A Foglalás vonni a fennmaradó fürtkapacitás azonnal, azonban a fenntartott használatalapú vonjuk le az egy adott csomópont kapacitását, csak akkor, ha legalább egy szolgáltatási objektumot el van helyezve azt. A későbbi foglalás rugalmasságot és erőforrás-kihasználást lehetővé teszi, mivel az erőforrások csak szükség esetén csomópontokon vannak lefoglalva.

## <a name="obtaining-the-application-load-information"></a>Az alkalmazás betöltése adatainak beszerzése
Minden alkalmazáshoz, amely rendelkezik az alkalmazás kapacitásának meghatározott egy vagy több metrikát a replikákat a szolgáltatások által jelentett összesített terhelési vonatkozó információkat is kaphat.

PowerShell:

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

A ApplicationLoad lekérdezés visszaadja az alapvető információkat szeretne az alkalmazáshoz megadott alkalmazás kapacitásának. Ezen információk közé tartozik a minimális és maximális csomópontok adatait, és a számát, amelyet az alkalmazás jelenleg kötnek van. Minden egyes alkalmazásmetrika terhelés vonatkozó információkat is tartalmaz többek között:

* Metrika neve: A metrika neve.
* Foglalás kapacitás: A fürt számára az alkalmazás fenntartott fürt kapacitása.
* Alkalmazásterhelés: Az alkalmazás gyermek replikák teljes terhelés.
* Alkalmazás kapacitásának: A maximális engedélyezett érték az alkalmazások terhelésének.

## <a name="removing-application-capacity"></a>Alkalmazás kapacitásának eltávolítása
Miután beállította az alkalmazás kapacitásának paramétereket az alkalmazáshoz, eltávolíthatók frissítés alkalmazás API-k vagy a PowerShell-parancsmagok használatával. Példa:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 –RemoveApplicationCapacity

```

Ezzel a paranccsal eltávolítható az összes alkalmazás kapacitás felügyeleti paraméterei az alkalmazáspéldány. Ez magában foglalja MinimumNodes MaximumNodes és az alkalmazásmetrikák, ha van ilyen. A parancs a hatása az azonnali. Ez a parancs befejeződése után a fürterőforrás-kezelő alkalmazások kezelése az alapértelmezett viselkedés használ. Alkalmazás kapacitásának paraméter adható meg újra keresztül `Update-ServiceFabricApplication` / `System.Fabric.FabricClient.ApplicationManagementClient.UpdateApplicationAsync()`.

### <a name="restrictions-on-application-capacity"></a>Alkalmazás kapacitásának korlátozásai
Alkalmazás kapacitásának paraméterekkel, hogy figyelembe kell venni néhány korlátozások vonatkoznak. Ha érvényesítési hibák léptek fel a módosítások nem kerül sor.

- Az összes egész szám paramétert nem negatív számnak kell lennie.
- MinimumNodes soha nem MaximumNodes nagyobbnak kell lennie.
- Ha a terhelés metrika kapacitások vannak megadva, majd kell követniük ezeket a szabályokat:
  - Csomópont foglalás kapacitása nem lehet nagyobb, mint a maximális csomópont kapacitását. Például nem korlátozza a kapacitás, a metrika "Processzor" a csomóponton két egységre, és megpróbálja lefoglalni minden csomóponton három egységet.
  - Ha MaximumNodes van megadva, majd a termék MaximumNodes és maximális csomópont-kapacitás nem lehet nagyobb, mint a teljes alkalmazás kapacitásának. Például tegyük fel, load metrika "Processzor" értéke 8 csomópont maximális kapacitását. Is tegyük fel, a csomópontok maximális száma 10-es értékre. Alkalmazás teljes kapacitás ebben az esetben a terhelés mérőszám 80-nál nagyobbnak kell lennie.

A korlátozások érvényben vannak, mind az alkalmazás létrehozása és a frissítések során.

## <a name="how-not-to-use-application-capacity"></a>Alkalmazás kapacitásának nem használatával
- Ne próbálja meg az alkalmazáscsoport funkciók használatával korlátozhatja az alkalmazás egy _adott_ csomópontok részhalmazát. Más szóval megadhatja, hogy az alkalmazás legfeljebb öt csomóponton fut, de nem mely adott öt csomópont van a fürtben. Egy alkalmazás bizonyos csomópontjaihoz korlátozásra szolgáltatások elhelyezési korlátozások használatával lehet elérni.
- Ne próbálkozzon az alkalmazás kapacitásának használatával győződjön meg arról, hogy ugyanazt az alkalmazást a két szolgáltatás kerüljenek-e az azonos csomópontokon. Helyette használjon [affinitás](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md) vagy [elhelyezési korlátozások](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).

## <a name="next-steps"></a>További lépések
- További információt a szolgáltatások konfigurálása [megismerheti a szolgáltatások konfigurálása](service-fabric-cluster-resource-manager-configure-services.md)
- Ismerje meg hogyan a fürterőforrás-kezelő felügyeli, és elosztja a terhelést a fürtben, tekintse meg a cikk a [terheléselosztás](service-fabric-cluster-resource-manager-balancing.md)
- Elölről kezdődik, és [, a Service Fabric fürterőforrás-kezelő bemutatása](service-fabric-cluster-resource-manager-introduction.md)
- Metrikák működése általában a további információkért olvassa a [Service Fabric terhelési mérőszámok](service-fabric-cluster-resource-manager-metrics.md)
- A fürterőforrás-kezelő leíró a fürt számos lehetőség áll. A velük kapcsolatos további információért tekintse meg a cikk a [leíró, Service Fabric-fürt](service-fabric-cluster-resource-manager-cluster-description.md)

[Image1]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-max-nodes.png
[Image2]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-reserved-capacity.png
