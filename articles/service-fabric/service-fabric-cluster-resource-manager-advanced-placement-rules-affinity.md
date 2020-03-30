---
title: Szolgáltatásháló-fürt erőforrás-kezelője – affinitás
description: Az Azure Service Fabric-szolgáltatások iránti szolgáltatásaffinitás áttekintése és a szolgáltatásaffinitás konfigurációjának útmutatása.
services: service-fabric
documentationcenter: .net
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 7bfd261802fbf891b8f45079255783cb1e8ac7d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551743"
---
# <a name="configuring-and-using-service-affinity-in-service-fabric"></a>Szolgáltatásaffinitás konfigurálása és használata a Service Fabricben
Affinitás egy olyan vezérlő, amely elsősorban a nagyobb monolitikus alkalmazások a felhőbe és a mikroszolgáltatások világába való átmenet megkönnyítése érdekében érhető el. Azt is használják, mint egy optimalizálás teljesítményének javítása érdekében a szolgáltatások, bár ezzel lehetnek mellékhatásai.

Tegyük fel, hogy egy nagyobb alkalmazást hoz, vagy egy olyan alkalmazást, amelyet nem a mikroszolgáltatások szem előtt tartásával terveztek, a Service Fabric (vagy bármely elosztott környezet). Ez a fajta átmenet gyakori. Először emelje fel az egész alkalmazást a környezetbe, csomagolja be, és győződjön meg arról, hogy zökkenőmentesen működik. Aztán elkezded lebontani különböző kisebb szolgáltatásokra, amik mind beszélnek egymással.

Előfordulhat, hogy az alkalmazás bizonyos problémákat tapasztal. A problémák általában az alábbi kategóriák egyikébe tartoznak:

1. A monolitikus alkalmazás X összetevője egy része nem dokumentált függőséget tartalmazott az Y összetevőtől, és ezeket az összetevőket külön szolgáltatásokká alakította. Mivel ezek a szolgáltatások most a fürt különböző csomópontjain futnak, megszakadnak.
2. Ezek az összetevők kommunikálnak keresztül (helyi nevű csövek | megosztott memória | fájlok at disk), és tényleg képesnek kell lennie arra, hogy írjon egy megosztott helyi erőforrás teljesítmény miatt most. Ez a kemény függőség lesz eltávolítása később, talán.
3. Minden rendben van, de kiderül, hogy ez a két összetevő valóban beszédes / teljesítmény érzékeny. Amikor áthelyezték őket külön szolgáltatások általános alkalmazás teljesítmény tankolt, vagy a késés nőtt. Ennek eredményeképpen az általános alkalmazás nem felel meg az elvárásoknak.

Ezekben az esetekben nem akarjuk elveszíteni a refactoring munkánkat, és nem akarunk visszatérni a monolithoz. Az utolsó feltétel is kívánatos, mint egy sima optimalizálás. Azonban, amíg nem tudjuk újratervezni az összetevőket, hogy természetesen működjön, mint a szolgáltatások (vagy amíg nem tudjuk megoldani a teljesítmény elvárások at más módon) mi lesz szükség némi értelemben a helység.

Mi a teendő? Nos, megpróbálhatnád bekapcsolni az affinitást.

## <a name="how-to-configure-affinity"></a>Az affinitás konfigurálása
Affinitás beállításához két különböző szolgáltatás közötti affinitási kapcsolatot kell meghatároznia. Az affinitást úgy is felhasználhatja, mint ha az egyik szolgáltatásra mutat, és azt mondja: "Ez a szolgáltatás csak ott futtatható, ahol a szolgáltatás fut." Néha utalunk affinitás, mint a szülő / gyermek kapcsolat (ahol pont a gyermek a szülő). Affinitás biztosítja, hogy a replikák vagy példányai egy szolgáltatás ugyanazon a csomóponton, mint egy másik szolgáltatás.

```csharp
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

> [!NOTE]
> A gyermekszolgálat csak egyetlen affinitási kapcsolatban vehet részt. Ha azt szeretné, hogy a gyermek egyszerre két szülőszolgáltatáshoz kötődjön, van néhány lehetősége:
> - A kapcsolatok sztornírozása (az aktuális gyermekszolgáltatásban az parentService1 és a parentService2 pont), vagy
> - Jelöld ki az egyik szülőt központként megegyezés szerint, és minden szolgáltatás az adott szolgáltatásra mutat. 
>
> Az eredményül kapott elhelyezési viselkedésnek a fürtben azonosnak kell lennie.
>

## <a name="different-affinity-options"></a>Különböző affinitási beállítások
Az affinitás tegy e több korrelációs séma egyikén keresztül jelenik meg, és két különböző móddal rendelkezik. Az affinitás leggyakoribb módja az, amit nonalignedaffinity-nek hívunk. A NonAlignedAffinity, a replikák vagy példányai a különböző szolgáltatások kerülnek ugyanazon a csomóponton. A másik mód alignedAffinity. Az összehangolt affinitás csak állapotalapú szolgáltatások esetén hasznos. Két állapotalapú szolgáltatás konfigurálása az affinitás összehangolása biztosítja, hogy a szolgáltatások elsődleges helye ugyanazon a csomóponton legyen, mint egymás. Azt is okozza, hogy az egyes másodlagos párok ezeket a szolgáltatásokat kell helyezni az azonos csomópontokon. Az is lehetséges (bár kevésbé gyakori) konfigurálni NonAlignedAffinity állapotalapú szolgáltatásokhoz. NonAlignedAffinity esetében a két állapotalapú szolgáltatás különböző replikái ugyanazon a csomóponton futnának, de az elsődleges elemek különböző csomópontokon végződhetnek.

<center>

![Affinitás módok és hatásaik][Image1]
</center>

### <a name="best-effort-desired-state"></a>A kívánt legjobb erőfeszítés
Az affinitás kapcsolat a legjobb erőfeszítés. Nem biztosítja ugyanazokat a garanciákat a helymegosztásra vagy a megbízhatóságra, mint az ugyanazon végrehajtható folyamatban való futtatás. Az affinitási kapcsolatban lévő szolgáltatások alapvetően különböző entitások, amelyek sikertelenek lehetnek, és egymástól függetlenül áthelyezhetők. Az affinitási kapcsolat is megszakadhat, bár ezek a szünetek ideiglenesek. Például a kapacitáskorlátozások azt jelenthetik, hogy az affinitási kapcsolatban csak néhány szolgáltatásobjektum fér el egy adott csomóponton. Ezekben az esetekben annak ellenére, hogy van egy affinitás kapcsolat a helyén, nem lehet érvényesíteni a többi megkötések miatt. Ha ez lehetséges, a jogsértést később automatikusan kijavítják.

### <a name="chains-vs-stars"></a>Láncok vs csillagok
Ma a fürterőforrás-kezelő nem tudja modellezni az affinitási kapcsolatok láncait. Ez azt jelenti, hogy egy olyan szolgáltatás, amely egy affinitáskapcsolatban gyermek, nem lehet szülő egy másik affinitási kapcsolatban. Ha azt szeretnénk, hogy modell ez a fajta kapcsolat, akkor hatékonyan kell modellezni, mint egy csillag, nem pedig egy lánc. Ha láncról csillagra szeretne lépni, a legalsó gyermek az első gyermek szülőjéhez kerül. A szolgáltatások elrendezése függően előfordulhat, hogy ezt többször is meg kell tennie. Ha nincs természetes szülőszolgáltatás, előfordulhat, hogy létre kell hoznia egy helyőrzőként szolgáló szolgáltatást. A követelményektől függően érdemes lehet az [alkalmazáscsoportokat](service-fabric-cluster-resource-manager-application-groups.md)is megvizsgálni.

<center>

![Láncok vs Csillagok keretében affinitás kapcsolatok][Image2]
</center>

A másik dolog, hogy vegye figyelembe affinitás kapcsolatok ma az, hogy ezek irányított alapértelmezés szerint. Ez azt jelenti, hogy az affinitási szabály csak azt kényszeríti, hogy a gyermek helyezett a szülő. Nem biztosítja, hogy a szülő a gyermekkel együtt helyezkedik el. Ezért, ha affinitás-megsértés történt, és valamilyen okból kijavíthatja a szabálysértést, nem lehetséges a gyermeket a szülő csomópontjára helyezni, akkor - még akkor is, ha a szülő áthelyezése a gyermek csomópontjára korrigálná a jogsértést - a szülő nem kerül át a gyermek csomópontja. Ha a [MoveParentToFixAffinityViolation](service-fabric-cluster-fabric-settings.md) konfigurációt true értékre állítja, az eltávolítja az irányzatot. Azt is fontos megjegyezni, hogy az affinitás kapcsolat nem lehet tökéletes, vagy azonnal érvényesíteni, mivel a különböző szolgáltatások különböző életciklusok, és nem, és önállóan mozog. Tegyük fel például, hogy a szülő hirtelen átadja a felettejár egy másik csomópontnak, mert összeomlott. A fürterőforrás-kezelő és a feladatátvétel-kezelő először a feladatátvételt kezeli, mivel a szolgáltatások fenntartása, konzisztens és elérhető a prioritás. Miután a feladatátvétel befejeződött, az affinitás kapcsolat megszakad, de a fürt erőforrás-kezelő úgy gondolja, minden rendben van, amíg észre veszi, hogy a gyermek nem található a szülő. Az ilyen típusú ellenőrzéseket rendszeresen elvégzik. Ebben a [cikkben](service-fabric-cluster-resource-manager-management-integration.md#constraint-types)további információ arról, hogy a fürterőforrás-kezelő hogyan értékeli ki a megkötéseket, és [ez](service-fabric-cluster-resource-manager-balancing.md) a cikk arról szól, hogyan konfigurálhatja azt a lépésszám, amelyen ezeket a megkötéseket kiértékelik.   


### <a name="partitioning-support"></a>Particionálás támogatása
Az utolsó dolog, hogy észre affinitás, hogy affinitás kapcsolatok nem támogatott, ha a szülő particionált. Partiparti szülő szolgáltatások lehet támogatni végül, de ma ez nem engedélyezett.

## <a name="next-steps"></a>További lépések
- A szolgáltatások konfigurálásával kapcsolatos további [tudnivalókért](service-fabric-cluster-resource-manager-configure-services.md)
- Ha a szolgáltatásokat kis gépkészletre szeretné korlátozni, vagy a szolgáltatások terhelését összesíteni szeretné, használja az [alkalmazáscsoportokat](service-fabric-cluster-resource-manager-application-groups.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png