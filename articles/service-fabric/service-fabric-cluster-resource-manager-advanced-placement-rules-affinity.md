---
title: Service Fabric fürt Resource Manager - kapcsolat |} A Microsoft Docs
description: Kapcsolat a Service Fabric-szolgáltatások konfigurálása – áttekintés
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: 678073e1-d08d-46c4-a811-826e70aba6c4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 29377492b90f366227ca7bedf85890b7734ea25f
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58663417"
---
# <a name="configuring-and-using-service-affinity-in-service-fabric"></a>Konfigurálása és a szolgáltatási affinitás használata a Service Fabricben
Kapcsolat az elsősorban biztosított a vezérlőelem megkönnyítése érdekében a Váltás nagyobb monolitikus alkalmazások, a felhő- és mikroszolgáltatás-alapú világ segítségével. Szolgáltatások teljesítményének növelése, bár ezt egy optimalizálási mellékhatása is van, mivel is használható.

Tegyük fel, hogy egy nagyobb alkalmazás, vagy olyat, amely csak az ne feledje, hogy a Service Fabric mikroszolgáltatásokat (vagy bármely elosztott környezetben) nem tervezett válnak elérhetővé. Ez a Váltás típus közös. Indítsa el a környezetbe, a teljes alkalmazás emelő csomagolás, és így meg arról, hogy már simán fut. Ezután elindíthatja ossza fel, hogy az összes kommunikáljon egymással különböző kisebb szolgáltatásra.

Végül tapasztalhatja, hogy az alkalmazás néhány problémát tapasztalja. A problémák általában az alábbi kategóriák valamelyikébe tartoznak:

1. A monolitikus alkalmazások néhány összetevőt X volt egy nem dokumentált függőségi Y összetevő, és csak kapcsolva az összetevőket külön szolgáltatásokra. Ezeket a szolgáltatásokat a fürt másik csomópontjain a most futtatja, mivel azok már megszakadt.
2. Ezek az összetevők közötti keresztüli kommunikációt (helyi nevesített csövek |} megosztott memória |} lemezen tárolt fájlok) és azok valóban szükséges fognak tudni írni a teljesítmény javítása érdekében a helyi megosztott erőforrás most. Rögzített függőséget is törlődik később, esetleg.
3. Minden rendben, de azt tapasztaltuk, hogy ez a két összetevő-e a ténylegesen forgalmas/teljesítmény-és nagybetűket. Amikor azokat áthelyezni őket külön szolgáltatásokra teljes tanked alkalmazásteljesítmény vagy késés nagyobb. Ennek eredményeképpen a teljes alkalmazás nem teljesíti a elvárásainak.

Ezekben az esetekben hogy nem szeretné elveszteni a újrabontási munka, és nem szeretné, lépjen vissza a monolit. A legutóbbi feltételt még akkor is, egy egyszerű optimalizálási kívánatosak lehetnek. Azonban amíg azt átalakíthatók az összetevők működik természetesen szolgáltatásként (vagy azt tudja oldani a teljesítmény elvárások más módon) fogunk kell néhány megismerje a helye.

Mi a teendő ilyenkor? Nos próbálkozzon ne tudják bekapcsolni a kapcsolat.

## <a name="how-to-configure-affinity"></a>Kapcsolat konfigurálása
Kapcsolat beállításához, megadhat egy kapcsolat két különböző szolgáltatások között. Is felfoghatók affinitás "mutat" egy szolgáltatást egy másik, valamint közli, hogy a "csak fut, ahol, hogy fut-e szolgáltatás." Néha nevezzük affinitás, egy szülő-gyermek kapcsolatot (Ha Ön mutassanak a gyermek a szülő). Affinitás biztosítja, hogy egy másik szolgáltatás, mint a azonos csomópontokon a replikák és a egy szolgáltatás példányai vannak elhelyezve.

```csharp
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

> [!NOTE]
> Egy alárendelt szolgáltatás csak egyetlen kapcsolat vehet részt. Ha a gyermeke lehet hozzárendelt két szülő szolgáltatások egyszerre néhány lehetőségek állnak rendelkezésére:
> - A kapcsolatok fordított (kell parentService1 és az aktuális gyermek szolgáltatás mutassanak parentService2), vagy
> - Megjelölni a szülők egyik hub konvenció szerint, és mutasson, hogy a szolgáltatás minden szolgáltatás van. 
>
> A fürt elhelyezési viselkedésről azonosnak kell lennie.
>

## <a name="different-affinity-options"></a>Másik kapcsolat beállításai
Affinitás jelölt keresztül több korrelációs rendszerek egyikét, és két különböző módot támogat. A leggyakoribb affinitási módja úgynevezett NonAlignedAffinity. NonAlignedAffinity a replikákat vagy a példányok a különböző szolgáltatások ugyanazon a csomóponton elhelyezett. A másik módra AlignedAffinity. Igazított kapcsolat akkor hasznos, csak az állapotalapú szolgáltatásokkal. A kapcsolat van igazítva két állapotalapú szolgáltatások konfigurálása biztosítja, hogy ezeket a szolgáltatásokat eredményezi, egymással azonos csomópontokon vannak elhelyezve. Ezeket a szolgáltatásokat a azonos csomópontokon elhelyezni kívánt másodlagos párjaihoz is okoz. Lehetőség arra is (bár kevésbé gyakori) NonAlignedAffinity konfigurálása az állapotalapú szolgáltatások esetében. A NonAlignedAffinity a két állapotalapú szolgáltatások különböző replikába futtatná a ugyanazon a csomóponton, de az elsődleges sikerült végül a különböző csomópontokon.

<center>

![Affinitás módok és hatásuk][Image1]
</center>

### <a name="best-effort-desired-state"></a>A legjobb kívánt tevékenységi állapot
Egy kapcsolat a lehető legjobb. Elhelyezés és megbízhatóságáról, amelyekre azonos futó végrehajtható a folyamat nem ugyanazon garanciákat nem biztosítják. Egy kapcsolat funkciók alapvetően különböző entitások, visszaadhatja a feladatokat, és egymástól függetlenül helyezhetők. Egy kapcsolat is érvénytelenítheti, bár ezek a sortörések ideiglenes. Például kapacitás korlátozások azt jelenti, hogy csak néhányat a kapcsolat a szolgáltatásobjektumokat kiférjen egy adott csomópont. Ezekben az esetekben annak ellenére, hogy egy kapcsolat van érvényben, akkor nem kényszeríti ki az egyéb megszorítások miatt. Ha lehetséges, akkor a megsértése automatikusan kijavítja később.

### <a name="chains-vs-stars"></a>Láncok és csillagok
Ma a fürterőforrás-kezelő nem tud modell láncok affinitás kapcsolatok. Ez azt jelenti, hogy egy szolgáltatás, amely egy kapcsolat a gyermek nem lehet szülője egy másik kapcsolat. Ha kíván ilyen kapcsolatban modellezheti, hatékonyan modell, a lánc helyett egy csillag rendelkezik. Szeretne áthelyezni egy láncból egy csillag, a legalsó gyermek lenne kell szülőjének első gyermek-szülő helyette. A szolgáltatások elhelyezkedését, attól függően előfordulhat, ezt többször tennie. Ha természetes szülő szolgáltatás nincs, akkor előfordulhat, hogy hozzon létre egyet, amely egy helyőrző. A követelményeitől függően akkor is érdemes megvizsgáljuk [alkalmazáscsoportok](service-fabric-cluster-resource-manager-application-groups.md).

<center>

![Láncok vs. Kapcsolat kapcsolatok kontextusában csillagok][Image2]
</center>

Egy másik kapcsolat kapcsolatok ma ne előnye, hogy azok alapértelmezés szerint irányt. Ez azt jelenti, hogy a kapcsolat a szabály csak kikényszeríti, hogy a gyermek elhelyezni, hogy a szülő. Ezért nem biztosítja, hogy a gyermek együtt-e a szülő. Ezért ha affinitás megsértést, és a megsértése kijavításához valamilyen okból nem található áthelyezése a gyermek a szülő csomópontra, majd – akkor is, ha a szülő áthelyezése a gyermek csomópont lenne javítása a megsértése – a szülő nem kerül át th e gyermek csomópont. A konfigurációs beállítás [MoveParentToFixAffinityViolation](service-fabric-cluster-fabric-settings.md) , igaz el kell távolítani a írásmód. Is fontos megjegyezni, hogy a kapcsolat nem perfect vagy azonnal kényszeríthető, mivel a különböző szolgáltatásokhoz különböző életciklusának rendelkező és is sikertelen, és egymástól függetlenül át. Például tegyük fel, a szülő hirtelen átadja a feladatokat egy másik csomópontra, mert a lefagyott. A fürterőforrás-kezelő és a Feladatátvevőfürt-kezelő a feladatátvételi funkciót először óta, a szolgáltatások egységes, gondoskodik, és elérhető a prioritás. A feladatátvétel befejezése után a kapcsolat megszakad, de a fürterőforrás-kezelő fenyegetésként észlel, minden rendben mindaddig, amíg azt észleli, hogy az alárendelt helye nem a szülő. Ezek rendezi az ellenőrzések rendszeres időközönként történik. További információkat a hogyan értékeli ki a fürterőforrás-kezelő a megkötések [Ez a cikk](service-fabric-cluster-resource-manager-management-integration.md#constraint-types), és [ehhez](service-fabric-cluster-resource-manager-balancing.md) ismerteti a kiadása, amelyre ezek a korlátozások vannak ütemben történik konfigurálásával kapcsolatos további értékeli ki.   


### <a name="partitioning-support"></a>Particionálási támogatása
A végső szembetűnő kapcsolatra vonatkozó dolog, hogy a kapcsolatok nem támogatottak, ahol a szülő particionálva van kapcsolat. Lehet, hogy végül támogatja a particionált szülő szolgáltatások, de jelenleg nem engedélyezett.

## <a name="next-steps"></a>További lépések
- További információt a szolgáltatások konfigurálása [megismerheti a szolgáltatások konfigurálása](service-fabric-cluster-resource-manager-configure-services.md)
- Gépek egy kis készletét a szolgáltatásokat, vagy használjon összesíti a szolgáltatások terhelését [alkalmazáscsoport](service-fabric-cluster-resource-manager-application-groups.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png