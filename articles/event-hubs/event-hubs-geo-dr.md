---
title: Az Azure Event Hubs földrajzi-vész-helyreállítási |} Microsoft Docs
description: Feladatátvételi földrajzi régió használatáról, és hajtsa végre az Azure Event Hubs katasztrófa utáni helyreállítás
services: event-hubs
documentationcenter: ''
author: sethmanheim
manager: timlt
editor: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: sethm
ms.openlocfilehash: 237b0639be75e12cff56f40ac76426aba7a8a701
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/16/2017
ms.locfileid: "26745894"
---
# <a name="azure-event-hubs-geo-disaster-recovery"></a>Az Azure Event Hubs földrajzi-vész-helyreállítási

Ha a teljes Azure-régiók vagy adatközpontok (Ha nincs [rendelkezésre állási zónák](../availability-zones/az-overview.md) használt) leállás következik be, nagyon fontos az adatok feldolgozásához továbbra is működik egy másik régióban vagy datacenter. Ilyen *földrajzi-vész-helyreállítási* és *georeplikáció* bármely vállalati fontos funkciókat. Az Azure Event Hubs földrajzi-vész-helyreállítási és a georeplikáció, a névterek szintjén is támogatja. 

A földrajzi-vész-helyreállítási funkció világszerte elérhető, az Event Hubs Standard termékváltozat.

## <a name="outages-and-disasters"></a>Kimaradások és vészhelyzetek

Fontos megjegyzés: a megkülönböztetése "kimaradások" és "katasztrófák." Egy *szolgáltatáskimaradás* Azure Event hubs elérhetetlenség, és hatással lehet a szolgáltatás, például egy üzenetküldési tárolóban, vagy akár a teljes adatközpont néhány összetevője. Azonban a probléma megszüntetése után az Event Hubs ismét elérhetővé válik. Nem tervezett kimaradás általában nem okoznak üzenetek vagy egyéb adatok elvesztését. Egy példa ilyen kimaradás lehet áramkimaradás az adatközpontban. Néhány kimaradások veszteségként csak rövid kapcsolat átmeneti vagy hálózati problémák miatt. 

A *katasztrófa* állandó, vagy hosszabb távú elvész az Event Hubs egy fürt, az Azure-régió vagy datacenter típusúként van definiálva. A régió vagy datacenter előfordulhat, hogy előfordulhat, hogy nem ismét elérhetővé válik, vagy lehet a óráig vagy napig. Ilyen katasztrófák példák tűz, elárasztás vagy földrengés. Egy olyan vészhelyzet esetén váló állandó egyes üzenetek, eseményeket és egyéb adatok elvesztését okozhatja. Azonban a legtöbb esetben kell adatvesztés nélküli, és üzenetek helyreállíthatók legyenek, amikor az Adatközpont készítsen biztonsági másolatot.

A földrajzi-vész-helyreállítási funkció Azure Event hubs egy vész-helyreállítási megoldást. A fogalmakat és a jelen cikkben ismertetett munkafolyamat alkalmazza a vészhelyreállítási forgatókönyveket, és nem átmeneti, illetve ideiglenes kimaradások esetén. A Microsoft Azure-ban vész-helyreállítási részletes tárgyalását lásd: [Ez a cikk](/azure/architecture/resiliency/disaster-recovery-azure-applications).

## <a name="basic-concepts-and-terms"></a>Alapvető fogalmakat és szakkifejezéseket tartalmazza

A vész-helyreállítási szolgáltatás valósítja meg a metaadatok vész-helyreállítási, és az elsődleges és másodlagos vész-helyreállítási névterek támaszkodik. Vegye figyelembe, hogy a földrajzi-vész-helyreállítási funkció érhető el a [Standard Termékváltozat](https://azure.microsoft.com/pricing/details/event-hubs/) csak. Nem kell a beállítások kapcsolati karakterlánc módosításait, a kapcsolat alias keresztül.

Ez a cikk a következő kifejezéseket használjuk:

-  *Alias*: az Ön által beállított vészhelyreállítási konfiguráció nevét. Az alias nyújt egy teljesen minősített tartománynév (FQDN) egyetlen stabil kapcsolati karakterláncot. Alkalmazások ez alias a kapcsolati karakterlánc használatával csatlakozni a névtérhez. 

-  *Elsődleges és másodlagos névtér*: az alias vannak rendelve az áttelepítendő névterek. Az elsődleges névtér "aktív", és fogadja az üzeneteket (Ez lehet egy meglévő vagy új névtér). A másodlagos névtér "passzív", és nem fogadhat üzeneteket. A metaadatok között is a Szinkronizáló, így is képes zökkenőmentesen fogadja az üzeneteket alkalmazás kódja vagy kapcsolati karakterlánc módosítás nélkül. Győződjön meg arról, hogy csak az aktív névtér fogadja az üzeneteket, az aliast kell használnia. 

-  *Metaadatok*: például az event hubs és a fogyasztói csoportok; és tulajdonságaikat a szolgáltatás a névtér társított entitásokat. Vegye figyelembe, hogy csak az entitásokat és a beállítások automatikusan replikálása. Üzenetek és eseményeket a rendszer nem replikálja. 

-  *Feladatátvételi*: a másodlagos névtér aktiválása folyamatán.

## <a name="setup-and-failover-flow"></a>A telepítő és a feladatátvételi folyamat

Az alábbi szakasz a feladatátvételi folyamat áttekintése, és elmagyarázza, hogyan állíthat be a kezdeti feladatátvevő. 

![1][]

### <a name="setup"></a>Beállítás

Meg elsőként hozzon létre vagy egy meglévő elsődleges névtér, és egy új másodlagos névtér, majd a két párosítsa. A párosítást lehetővé teszi az aliast való csatlakozásra használja. Használja az alias, mert nincs kapcsolati karakterláncok módosítása. Csak új névtér a feladatátvételi párosítás lehet hozzáadni. Végül adja hozzá néhány figyelő észleli, ha a feladatátvétel során szükséges. A legtöbb esetben a szolgáltatás része egy nagy ökoszisztéma, így automatikus feladatátvétel ritkán lehetséges, mivel nagyon gyakran feladatátvételek szinkronban vannak a fennmaradó alrendszer vagy infrastruktúra kell végrehajtani.

### <a name="example"></a>Példa

Ebben a forgatókönyvben egy példa fontolja meg az üzenetek vagy események bocsát ki pénztári pont (POS) megoldást. Az Event Hubs továbbítja ezeket az eseményeket néhány hozzárendelését vagy újraformázást megoldást, amely ezután továbbítja a leképezett adatokat egy másik rendszerre további feldolgozásra. Ezen a ponton az összes ilyen rendszerek lehet, hogy futhat azonos Azure-régióban. A döntést, mikor és milyen része, hogy áthelyezze a feladatokat a infrastruktúrában adatáramlásra függ. 

Feladatátvételi rendszerek figyelése, vagy egyedi figyelési megoldások automatizálható. Azonban az ilyen automation vesz igénybe, további tervezési és a munkájához, ez a cikk hatókörén kívül esik.

### <a name="failover-flow"></a>Feladatátvétel folyamatábrája

Kezdeményezze a feladatátvételt, ha két lépésre szükség:

1. Egy másik tervezett kimaradás esetén érdemes átvehet újra. Ezért egy másik passzív névtér beállítását, és frissítse a jelenlegi párosítását. 

2. A korábbi elsődleges névtérből származó üzenetek lekérésére, amennyiben az rendelkezésre áll újra. Ezt követően használja a névtér rendszeres üzenetküldési kívül a földrajzi-helyreállítási beállításai, vagy törölje a régi elsődleges névteret.

> [!NOTE]
> Csak a sikertelen előre szemantikáját támogatottak. Ebben a forgatókönyvben a feladatátvétel, és ezután újra párosítani egy új névtér. Sikertelen vissza nem támogatott. például az SQL-fürt. 

![2][]

## <a name="management"></a>Kezelés

Ha hibát; például a nem megfelelő régiók párosítása, a kezdeti beállítás során, a két névtér párosítás bármikor meghibásodásához vezethet. Ha szeretné használni a párosított névterek rendszeres névterek, törölje az aliast.

## <a name="samples"></a>Példák

A [mintát a Githubon](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/GeoDRClient) bemutatja, hogyan állíthat be, és kezdeményezze a feladatátvételt. A következő fogalmakat mutatja be:

- Az Azure Active Directoryban az Event Hubs Azure Resource Manager használatához szükséges beállításokat. 
- A mintakód végrehajtásához szükséges lépéseket. 
- Küldése és fogadása a jelenlegi elsődleges névtérből. 

## <a name="considerations"></a>Megfontolandó szempontok

Ezzel a kiadással szem előtt tartani a következő érdemes figyelembe venni:

1. A feladatátvételi tervezés során is figyelembe kell venni a idő tényező. Például ha több mint 15-20 percig kapcsolat megszakad, előfordulhat, hogy mellett dönt kezdeményezze a feladatátvételt. 
 
2. Azt a tényt, hogy nincs adat replikálódik azt jelenti, hogy a jelenleg aktív munkamenetek nem replikálva vannak. Továbbá, kettős észlelés és ütemezett üzenetek esetleg nem működnek. Új munkamenetek, ütemezett üzenetek és új ismétlődések fog működni. 

3. Egy összetett elosztott infrastruktúra feladatátvételét kell [kipróbálni](/azure/architecture/resiliency/disaster-recovery-azure-applications#disaster-simulation) legalább egyszer. 

4. Entitások szinkronizálása eltarthat egy ideig, körülbelül 50-100 entitások / perc.

## <a name="next-steps"></a>Következő lépések

* A [mintát a Githubon](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/GeoDRClient) végigvezeti egy egyszerű munkafolyamatot, amely létrehoz egy földrajzi párosítás és indít el egy feladatátvételt egy vész-helyreállítási forgatókönyv.
* A [REST API-referenciában](/rest/api/eventhub/disasterrecoveryconfigs) API-k ismerteti a földrajzi-vészhelyreállítási konfigurációban végrehajtásához.

Ha további információkat szeretne az Event Hubsról, tekintse meg az alábbi hivatkozásokat:

* Bevezetés az [Event Hubs használatába oktatóanyag](event-hubs-dotnet-standard-getstarted-send.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)
* [Az Event Hubsot használó mintaalkalmazások](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-geo-dr/geo1.png
[2]: ./media/event-hubs-geo-dr/geo2.png