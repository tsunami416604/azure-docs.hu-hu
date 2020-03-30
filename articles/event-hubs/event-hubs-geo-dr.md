---
title: Geo-vészhelyreállítás – Azure Event Hubs| Microsoft dokumentumok
description: Földrajzi régiók használata feladatátvételhez és vészhelyreállítás végrehajtásához az Azure Event Hubs-ban
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 40db6e9f429569bc19641aa5f0f371f287db7b18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281469"
---
# <a name="azure-event-hubs---geo-disaster-recovery"></a>Azure Event Hubs – Geo-katasztrófa utáni helyreállítás 

Ha teljes Azure-régiók vagy adatközpontok (ha nincs [rendelkezésre állási zónák)](../availability-zones/az-overview.md) állásidőt tapasztal, fontos, hogy az adatfeldolgozás továbbra is egy másik régióban vagy adatközpontban működjön. Mint ilyen, *a geo-katasztrófa-helyreállítási* és *georeplikáció* fontos funkciók minden vállalat számára. Az Azure Event Hubs támogatja a földrajzi vész-helyreállítási és a georeplikáció, a névtér szintjén. 

> [!NOTE]
> A geo-vészhelyreállítási funkció csak a [szabványos és dedikált sk-k](https://azure.microsoft.com/pricing/details/event-hubs/)hoz érhető el.  

## <a name="outages-and-disasters"></a>Kimaradások és katasztrófák

Fontos megjegyezni, hogy különbséget kell tenni a "kimaradások" és a "katasztrófák" között. A *szolgáltatáskimaradás* az Azure Event Hubs ideiglenes elérhetetlensége, és hatással lehet a szolgáltatás egyes összetevőire, például egy üzenettárolóra, vagy akár a teljes adatközpontra. A probléma megoldása után azonban az Event Hubs ismét elérhetővé válik. A kimaradás általában nem okoz üzenetek vagy más adatok elvesztését. Egy ilyen kimaradás lehet áramkimaradás az adatközpontban. Egyes kimaradások csak rövid kapcsolat veszteségek átmeneti vagy hálózati problémák miatt. 

A *katasztrófa* az Event Hubs-fürt, az Azure-régió vagy az adatközpont állandó vagy hosszabb távú elvesztése. A régió vagy az adatközpont lehet, hogy nem lesz elérhető újra, vagy lehet, hogy le órákig vagy napokig. Ilyen katasztrófák például a tűz, az árvíz vagy a földrengés. Állandóvá válik katasztrófa egyes üzenetek, események vagy egyéb adatok elvesztését okozhatja. A legtöbb esetben azonban nem lehet adatvesztés, és az üzenetek az adatközpont biztonsági csinálása után helyreállnak.

Az Azure Event Hubs geo-vész-helyreállítási szolgáltatása egy vész-helyreállítási megoldás. A jelen cikkben ismertetett fogalmak és munkafolyamatok a katasztrófa-forgatókönyvekre vonatkoznak, nem pedig átmeneti vagy ideiglenes kimaradásokra. A Vészhelyreállítás microsoft Azure-beli részletes ismertet [ebben a cikkben](/azure/architecture/resiliency/disaster-recovery-azure-applications)olvashat.

## <a name="basic-concepts-and-terms"></a>Alapfogalmak és kifejezések

A vész-helyreállítási szolgáltatás metaadat-vész-helyreállítási, és támaszkodik az elsődleges és másodlagos vész-helyreállítási névterek. 

A geo-vész-helyreállítási funkció csak a [szabványos és dedikált sk-k](https://azure.microsoft.com/pricing/details/event-hubs/) érhető el. Nem kell módosítania a kapcsolati karakterláncot, mivel a kapcsolat aliason keresztül történik.

A cikk a következő kifejezéseket használja:

-  *Alias*: A létrehozott vész-helyreállítási konfiguráció neve. Az alias egyetlen stabil, teljesen minősített tartománynév (FQDN) kapcsolati karakterláncot biztosít. Az alkalmazások ezt az alias kapcsolati karakterláncot használják a névtérhez való csatlakozáshoz. 

-  *Elsődleges/másodlagos névtér*: Az aliasnak megfelelő névterek. Az elsődleges névtér "aktív", és üzeneteket fogad (ez lehet meglévő vagy új névtér). A másodlagos névtér "passzív", és nem fogad üzeneteket. A két metaadat szinkronban van, így mindkettő zökkenőmentesen fogadhatja az üzeneteket alkalmazáskód vagy kapcsolati karakterlánc módosítása nélkül. Annak érdekében, hogy csak az aktív névtér fogadjon üzeneteket, az aliast kell használnia. 

-  *Metaadatok*: Entitások, például eseményközpontok és fogyasztói csoportok; és a szolgáltatás névtérhez társított tulajdonságai. Ne feledje, hogy csak az entitások és azok beállításai replikálódnak automatikusan. Az üzenetek és események replikálása nem történt meg. 

-  *Feladatátvétel*: A másodlagos névtér aktiválásának folyamata.

## <a name="supported-namespace-pairs"></a>Támogatott névtérpárok
Az elsődleges és másodlagos névterek következő kombinációi támogatottak:  

| Elsődleges névtér | Másodlagos névtér | Támogatott | 
| ----------------- | -------------------- | ---------- |
| Standard | Standard | Igen | 
| Standard | Dedikált | Igen | 
| Dedikált | Dedikált | Igen | 
| Dedikált | Standard | Nem | 

> [!NOTE]
> Nem párosíthatja az azonos dedikált fürtben lévő névtereket. A külön fürtökben lévő névterek párosíthatók. 

## <a name="setup-and-failover-flow"></a>Beállítási és feladatátvételi folyamat

A következő szakasz áttekintést nyújt a feladatátvételi folyamatról, és bemutatja, hogyan állíthatja be a kezdeti feladatátvételt. 

![1][]

### <a name="setup"></a>Telepítés

Először hozzon létre vagy használjon egy meglévő elsődleges névteret, és egy új másodlagos névteret, majd párosítsa a kettőt. Ez a párosítás olyan aliast ad, amelyet a csatlakozáshoz használhat. Mivel aliast használ, nem kell módosítania a kapcsolati karakterláncokat. Csak új névterek adhatók hozzá a feladatátvételi párosításhoz. Végül hozzá kell adnia néhány figyelést, hogy észlelje, ha feladatátvételre van szükség. A legtöbb esetben a szolgáltatás egy nagy ökoszisztéma része, így az automatikus feladatátvétel ritkán lehetséges, mivel nagyon gyakran a feladatátvételt a fennmaradó alrendszerrel vagy infrastruktúrával szinkronban kell végrehajtani.

### <a name="example"></a>Példa

Ebben a forgatókönyvben az értékesítési pont (POS) megoldás, amely üzeneteket vagy eseményeket bocsát ki. Az Event Hubs továbbítja ezeket az eseményeket valamilyen leképezési vagy újraformázási megoldásnak, amely ezután továbbítja a leképezett adatokat egy másik rendszernek további feldolgozáscéljából. Ezen a ponton előfordulhat, hogy az összes ilyen rendszerek üzemelteti az azonos Azure-régióban. A döntés, hogy mikor és milyen része a feladatátvétel függ az adatok áramlását az infrastruktúrában. 

A feladatátvételt monitorozási rendszerekkel vagy egyéni tervezési megoldásokkal automatizálhatja. Az ilyen automatizálás azonban extra tervezést és munkát igényel, amely kívül esik a cikk hatókörén.

### <a name="failover-flow"></a>Feladatátvételi folyamat

Ha elindítja a feladatátvételt, két lépésre van szükség:

1. Ha egy másik kimaradás történik, azt szeretné, hogy képes legyen a feladatátvétel újra. Ezért állítson be egy másik passzív névteret, és frissítse a párosítást. 

2. Üzenetek lekérése a korábbi elsődleges névtérből, amint az ismét elérhetővé válik. Ezt követően használja ezt a névteret a geo-helyreállítási beállításon kívüli rendszeres üzenetküldéshez, vagy törölje a régi elsődleges névteret.

> [!NOTE]
> Csak a sikertelen továbbító szemantika támogatott. Ebben az esetben adja át a feladatátvételt, majd párosítsa újra egy új névtérrel. A visszanemében nem támogatott a hiba. például egy SQL-fürtben. 

![2][]

## <a name="management"></a>Kezelés

Ha hibázott; például a kezdeti beállítás során nem a megfelelő területeket párosította, bármikor megszakíthatja a két névtér párosítását. Ha a párosított névtereket normál névterekként szeretné használni, törölje az aliast.

## <a name="samples"></a>Példák

A [githubon a minta](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient) bemutatja, hogyan lehet beállítani és kezdeményezni a feladatátvételt. Ez a minta a következő fogalmakat mutatja be:

- Az Azure Active Directoryban az Azure Resource Manager és az Event Hubs használatához szükséges beállítások. 
- A mintakód végrehajtásához szükséges lépések. 
- Küldés és fogadás az aktuális elsődleges névtérből. 

## <a name="considerations"></a>Megfontolandó szempontok

Ezt a kiadást vegye figyelembe:

1. Az Event Hubs geo-katasztrófa-helyreállítás nem replikálja az adatokat, ezért nem használhatja fel újra az elsődleges eseményközpont régi eltolási értékét a másodlagos eseményközpontban. Javasoljuk, hogy az alábbi eszközök egyikével indítsa újra az eseményfogadót:

- *EventPosition.FromStart()* – Ha a másodlagos eseményközpont összes adatát szeretné beolvasni.
- *EventPosition.FromEnd()* – Ha az összes új adatot a másodlagos eseményközponttal való kapcsolat idejéből szeretné olvasni.
- *EventPosition.FromEnqueuedTime(dateTime)* – Ha egy adott dátumtól és időponttól kezdve szeretné elolvasni a másodlagos eseményközpontban fogadott összes adatot.

2. A feladatátvétel i. Ha például 15–20 percnél hosszabb ideig veszíti el a kapcsolatot, dönthet úgy, hogy kezdeményezi a feladatátvételt. 
 
3. Az a tény, hogy nincs adat replikálása azt jelenti, hogy a jelenleg aktív munkamenetek nem replikálódik. Emellett előfordulhat, hogy a duplikáltelemek és az ütemezett üzenetek nem működnek. Az új munkamenetek, az ütemezett üzenetek és az új ismétlődések működni fognak. 

4. Egy összetett elosztott infrastruktúra nem megfelelő feladatát, legalább egyszer el kell [próbálni.](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) 

5. Az entitások szinkronizálása eltarthat egy ideig, körülbelül 50–100 entitás percenként.

## <a name="availability-zones"></a>Rendelkezésre állási zónák 

Az Event Hubs standard termékváltozat támogatja [a rendelkezésre állási zónák,](../availability-zones/az-overview.md)amely hiba-elszigetelt helyek egy Azure-régióban. 

> [!NOTE]
> Az Azure Event Hubs Standard rendelkezésre állási zónák támogatása csak olyan [Azure-régiókban](../availability-zones/az-overview.md#services-support-by-region) érhető el, ahol rendelkezésre állási zónák vannak jelen.

Csak az Azure Portal használatával engedélyezheti a rendelkezésre állási zónákat az új névterekben. Az Event Hubs nem támogatja a meglévő névterek áttelepítését. A zónaredundancia nem tiltható le, miután engedélyezte azt a névtérben.

![3][]

## <a name="next-steps"></a>További lépések

* A [GitHub-minta](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient) egy egyszerű munkafolyamaton vezet be, amely létrehoz egy földrajzi párosítást, és feladatátvételt kezdeményez egy vész-helyreállítási forgatókönyvhöz.
* A [REST API-hivatkozás](/rest/api/eventhub/disasterrecoveryconfigs) ismerteti a geo-vész-helyreállítási konfiguráció végrehajtásához szükséges API-kat.

Ha további információkat szeretne az Event Hubsról, tekintse meg az alábbi hivatkozásokat:

- Bevezetés az Event Hubs használatába
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [Javascript](get-started-java-send-v2.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)
* [Az Event Hubsot használó mintaalkalmazások](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-geo-dr/geo1.png
[2]: ./media/event-hubs-geo-dr/geo2.png
[3]: ./media/event-hubs-geo-dr/eh-az.png
