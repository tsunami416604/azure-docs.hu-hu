---
title: "Service Fabric fürt erőforrás-kezelő - affinitás |} Microsoft Docs"
description: "A Service Fabric szolgáltatások kapcsolat konfigurálása – áttekintés"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 678073e1-d08d-46c4-a811-826e70aba6c4
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 8122f1644da25a9a2ab05291dafc33c77a91147f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="configuring-and-using-service-affinity-in-service-fabric"></a>Konfigurálásával és a Service Fabric szolgáltatás affinitás használatával
Kapcsolat az biztosított főként a vezérlőelem megkönnyítik az átmenet nagyobb egységes alkalmazások be a felhő- és mikroszolgáltatások világ segítségével. Azt is szolgál az optimalizálás szolgáltatások teljesítményének növelése, így bár mellékhatásokkal is.

Tegyük fel, most gépidőt, egy nagyobb alkalmazást, vagy olyat, amely nem csupán kialakítása során vegye figyelembe, hogy a Service Fabric mikroszolgáltatások (vagy bármely elosztott környezetben). Ez a átviteli típus általános. Indítsa el a teljes alkalmazás feloldása a környezetbe, csomagolás, valamint annak biztosítása, hogy zökkenőmentesen fut. Majd megkezdése ossza fel különböző kisebb szolgáltatásokat, hogy az összes kommunikálni egymással.

Végül azt tapasztalhatja, hogy az alkalmazás egyes hibákat észlelt. A problémák általában egyikébe ezen kategóriák:

1. Néhány összetevőt X az egységes alkalmazásban volt egy nem dokumentált függőség összetevő Y, és az összetevőket külön szolgáltatás csak kapcsolva. Mivel ezek a szolgáltatások a fürt különböző csomópontokon futnak, fontosságúak megszakadt.
2. Ezek az összetevők kommunikációra keresztül (nevesített csövek helyi |} megosztott memória |} lemezen tárolt fájlok) és azok valóban képesnek kell lenniük a teljesítményre vonatkozó megfontolásból megosztott helyi erőforrás most írni. Rögzített függőséget is törlődik, talán.
3. Minden megfelelően működik, de azt elemről kiderül, hogy, hogy a két összetevő-e a ténylegesen chatty/teljesítmény-és nagybetűket. Ha azok áthelyezi őket szolgáltatások külön teljes tanked az alkalmazások teljesítményének és késést növelni. Emiatt az alkalmazás általános nem teljesíti elvárásainak.

Ebben az esetben azt szeretne tartani az újrabontási munka, és nem szeretné, ha vissza kíván térni a monolit. Az utolsó feltétel lehet egy egyszerű optimalizálás kívánatos. Azonban amíg azt lehet átalakítani az összetevők működéséhez természetes szolgáltatásként (vagy igazolnia megoldható a teljesítményre vonatkozó elvárásokat bármilyen más módon) lesz, bizonyos értelemben helység kell.

Mi a teendő ilyenkor? Jól próbálkozzon affinitás bekapcsolásával.

## <a name="how-to-configure-affinity"></a>Kapcsolat konfigurálása
Kapcsolat beállításához adja meg egy két különböző szolgáltatások közötti kapcsolat. Az eltolásokat tekintheti affinitás "mutató" egy szolgáltatás más, valamint arról, hogy "csak fut, ahol, hogy fut-e a szolgáltatás." Egyes esetekben affinitás lesz az (ahol, mutasson a gyermek a szülő) szülő-gyermek kapcsolat. Affinitás biztosítja, hogy a replikák és a szolgáltatás egy példánya kerülnek, mint egy másik szolgáltatás ugyanazon csomópontok.

```csharp
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

> [!NOTE]
> Egy alárendelt szolgáltatás csak egyetlen kapcsolat vehet részt. Ha a gyermek rendelhetők affinitás alapján két szülő szolgáltatások egyszerre néhány lehetőség közül választhat:
> - A kapcsolatok fordított (parentService1 és az aktuális gyermek szolgáltatási pont parentService2 rendelkeznek), vagy
> - Konvenció hubot kijelölni a szülő egyikét, és mutasson, hogy a szolgáltatás minden szolgáltatás van. 
>
> A fürt elhelyezési viselkedésről azonosnak kell lennie.
>

## <a name="different-affinity-options"></a>Másik kapcsolat beállításai
Kapcsolat több korrelációs rendszer keresztül képviseli, és két különböző módja van. A kapcsolat a leggyakoribb módja a úgynevezett NonAlignedAffinity. NonAlignedAffinity a replikák és a különböző szolgáltatáspéldánynak ugyanazon a csomóponton elhelyezni. A más módja AlignedAffinity. Igazított affinitás akkor hasznos, csak a állapotalapú szolgáltatás. Van igazítva a kapcsolat a két állapotalapú szolgáltatások konfigurálása biztosítja, hogy a szolgáltatások eredményezi kerülnek-e egymással a azonos csomópontokon. Több másodlagos adatbázist, az ezekbe a szolgáltatásokba, az ugyanazon a csomóponton kell elhelyezni minden párt is okoz. Lehetőség arra is (bár a kevésbé közös) NonAlignedAffinity állapotalapú szolgáltatások konfigurálásához. NonAlignedAffinity a két állapotalapú szolgáltatások különböző replikáinak futna ugyanazon a csomóponton, de az elsődleges különböző csomópontokon sikerült befejezéséhez.

<center>
![Kapcsolat módok és azok által okozott hatások][Image1]
</center>

### <a name="best-effort-desired-state"></a>Szükséges elérhető legjobb állapota
Egy kapcsolat a lehető legkedvezőbb módon. Nem biztosít közös elhelyezés vagy megbízhatóságát, hogy a futó megegyezik a folyamat végrehajtható nem azonos garanciát. Egy kapcsolat a szolgáltatások sikertelen lehet, és egymástól függetlenül helyezhető alapvetően különböző entitások. Egy kapcsolat is érvénytelenítheti, bár ezeket ideiglenes. Például kapacitás korlátozások jelentheti, hogy a szolgáltatás objektumokat a kapcsolat némelyike csak egy adott csomópont kiférjen. Ezekben az esetekben annak ellenére, hogy egy kapcsolat van érvényben, akkor nem kényszeríthető a más korlátok miatt. Ha lehetséges, a megsértése automatikusan kijavításáig később.

### <a name="chains-vs-stars"></a>Csillag és láncok
Ma a fürt erőforrás-kezelő nem tudta modell láncok affinitás kapcsolatok. Ez azt jelenti, hogy egy szolgáltatás, amely egy kapcsolat a gyermek nem lehet szülője egy másik kapcsolat. Ha azt szeretné, a következő modellre: a kapcsolat típusát, hatékonyan csillag, nem pedig a lánc a modell rendelkezik. Áthelyezése egy csillag, a legalsó gyermek volna szülőjének megadni az első gyermek szülő helyette. Attól függően, hogy a szolgáltatások elrendezésének előfordulhat, hogy ezt többször elvégzéséhez. Ha nincs természetes szülő szolgáltatás, előfordulhat, szeretne létrehozni egyet, amely helyőrzőként szolgál. A követelményeitől függően előfordulhat, hogy szeretné megismerhetők [alkalmazáscsoportok](service-fabric-cluster-resource-manager-application-groups.md).

<center>
![Láncok vs. A környezeti kapcsolat kapcsolatok csillagokra][Image2]
</center>

Ma megjegyezni affinitás kapcsolatok egy másik művelet, hogy azok irányt. Ez azt jelenti, hogy a kapcsolatot a szabály csak kényszeríti, hogy a gyermek elhelyezni, hogy a szülő. Ezért nem biztosítja, hogy a szülő elhelyezve a gyermek. Is fontos megjegyezni, hogy a kapcsolat nem perfect vagy azonnal kényszeríthető óta különböző szolgáltatásokhoz különböző életciklusának rendelkező és is sikertelen mozognak. Például tegyük fel, a szülő hirtelen átadja a feladatokat egy másik csomópontra, mert lefagyott. A fürt erőforrás-kezelő és a Feladatátvevőfürt-kezelő a feladatátvétel először óta, a szolgáltatások konzisztens, tartása és kezelésére használható a prioritás van. Miután befejeződött a feladatátvétel, a kapcsolat megszakad, de a erőforrás-kezelőt úgy értelmezi, minden rendben mindaddig, amíg azt észleli, hogy az alárendelt helye nem a szülő. Ezek rendezi az ellenőrzések rendszeres időközönként kerül sor. További információkat a hogyan értékeli ki a fürt erőforrás-kezelő a megkötések [Ez a cikk](service-fabric-cluster-resource-manager-management-integration.md#constraint-types), és [a](service-fabric-cluster-resource-manager-balancing.md) -kiszolgálóhoz a ütemben történik, amelyen ezek a megkötések teljesíthetők a konfigurálásával kapcsolatos további értékeli ki.   


### <a name="partitioning-support"></a>Particionálás támogatása
A végső lépés kapcsolatra vonatkozó figyelje meg, hogy kapcsolatok nem támogatottak, ha a szülő particionálva van kapcsolat. Lehet, hogy végül támogatja a particionált szülő szolgáltatások, de jelenleg nem engedélyezett.

## <a name="next-steps"></a>Következő lépések
- A szolgáltatások konfigurálásáról [további információ a szolgáltatások konfigurálása](service-fabric-cluster-resource-manager-configure-services.md)
- Korlátozza a gépek egy kis készletét a szolgáltatások vagy szolgáltatások, a terhelés összesítése [alkalmazáscsoportok](service-fabric-cluster-resource-manager-application-groups.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png