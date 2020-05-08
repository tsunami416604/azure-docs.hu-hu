---
title: Földrajzi katasztrófa-helyreállítás – Azure Event Hubs | Microsoft Docs
description: Földrajzi régiók használata a feladatátvételhez és az Azure-beli vész-helyreállítás elvégzéséhez Event Hubs
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
ms.openlocfilehash: 2c42637dda9d1a413c0521ea2d7565a63ca58e81
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858284"
---
# <a name="azure-event-hubs---geo-disaster-recovery"></a>Azure Event Hubs – geo-vész-helyreállítás 

Ha a teljes Azure-régiók vagy-adatközpontok (ha nincsenek használatban [rendelkezésre állási zónák](../availability-zones/az-overview.md) ) a tapasztalatok leállását tapasztalják, kritikus fontosságú, hogy az adatfeldolgozás továbbra is egy másik régióban vagy adatközpontban működjön. Így a *geo-* vész-helyreállítás és a *geo-replikáció* minden vállalat számára fontos funkció. Az Azure Event Hubs a Geo-vész-helyreállítást és a Geo-replikációt is támogatja a névtér szintjén. 

> [!NOTE]
> A földrajzi katasztrófa utáni helyreállítási funkció csak a [standard és a dedikált SKU](https://azure.microsoft.com/pricing/details/event-hubs/)esetében érhető el.  

## <a name="outages-and-disasters"></a>Kimaradások és katasztrófák

Fontos megjegyezni az "kimaradások" és a "katasztrófák" közötti különbséget. A *leállás* az Azure Event Hubs ideiglenes nem érhető el, és hatással lehet a szolgáltatás egyes összetevőire, például az üzenetküldési tárolóra vagy akár a teljes adatközpontra is. A probléma javítása után azonban Event Hubs újra elérhetővé válik. A leállás általában nem okoz üzenetet vagy más adatvesztést. Ilyen kimaradás például áramkimaradás lehet az adatközpontban. Bizonyos kimaradások átmeneti vagy hálózati problémák miatt csak rövid kapcsolódási veszteségek. 

A *katasztrófa* a Event Hubs-fürt, az Azure-régió vagy az adatközpont állandó vagy hosszú távú elvesztéseként van meghatározva. Előfordulhat, hogy a régió vagy az adatközpont újra elérhetővé válik, vagy akár órákig, akár napokig nem. Ilyen katasztrófák például a tűz, az árvíz vagy a földrengés. Az állandó vészhelyzet miatt előfordulhat, hogy bizonyos üzenetek, események vagy egyéb adatvesztést okoznak. Azonban a legtöbb esetben nem lehet adatvesztés, és az adatközpont biztonsági mentése után az üzenetek nem állíthatók helyre.

Az Azure Event Hubs földrajzi katasztrófa utáni helyreállítási funkciója vész-helyreállítási megoldás. Az ebben a cikkben ismertetett fogalmak és munkafolyamatok a katasztrófa-forgatókönyvekre, és nem átmeneti vagy átmeneti kimaradásokra vonatkoznak. A Microsoft Azure vész-helyreállítási részletes megvitatását [ebben a cikkben](/azure/architecture/resiliency/disaster-recovery-azure-applications)találja.

## <a name="basic-concepts-and-terms"></a>Alapvető fogalmak és kifejezések

A vész-helyreállítási funkció a metaadatok vész-helyreállítását valósítja meg, és az elsődleges és másodlagos vész-helyreállítási névterekre támaszkodik. 

A földrajzi katasztrófa utáni helyreállítási funkció csak a [standard és a dedikált SKU](https://azure.microsoft.com/pricing/details/event-hubs/) esetében érhető el. Nem kell módosítania a kapcsolódási karakterláncot, mert a kapcsolatok aliason keresztül történnek.

A cikk a következő kifejezéseket használja:

-  *Alias*: az Ön által beállított vész-helyreállítási konfiguráció neve. Az alias egyetlen stabil teljes tartománynevet (FQDN) tartalmazó adatkarakterláncot biztosít. Az alkalmazások ezt az alias kapcsolati karakterláncot használják a névtérhez való kapcsolódáshoz. 

-  *Elsődleges/másodlagos névtér*: az aliasnak megfelelő névterek. Az elsődleges névtér "aktív", és fogadja az üzeneteket (ez lehet egy meglévő vagy egy új névtér). A másodlagos névtér "passzív", és nem fogad üzeneteket. A kettő közötti metaadatok szinkronban vannak, így mindkét alkalmazás kód vagy kapcsolati karakterlánc módosítása nélkül is zökkenőmentesen fogadhat üzeneteket. Annak biztosítása érdekében, hogy csak az aktív névtér kapjon üzeneteket, az aliast kell használnia. 

-  *Metaadatok*: olyan entitások, mint az Event hubok és a fogyasztói csoportok; a névtérhez társított szolgáltatás tulajdonságai. Vegye figyelembe, hogy csak az entitások és azok beállításai lesznek automatikusan replikálva. Az üzenetek és az események nem replikálódnak. 

-  *Feladatátvétel*: a másodlagos névtér aktiválása folyamatban van.

## <a name="supported-namespace-pairs"></a>Támogatott névtér-párok
Az elsődleges és a másodlagos névterek következő kombinációi támogatottak:  

| Elsődleges névtér | Másodlagos névtér | Támogatott | 
| ----------------- | -------------------- | ---------- |
| Standard | Standard | Igen | 
| Standard | Dedikált | Igen | 
| Dedikált | Dedikált | Igen | 
| Dedikált | Standard | No | 

> [!NOTE]
> Ugyanahhoz a dedikált fürthöz tartozó névtereket nem lehet párosítani. A különálló fürtökben található névtereket is párosíthatja. 

## <a name="setup-and-failover-flow"></a>Telepítési és feladatátvételi folyamat

A következő szakasz áttekintést nyújt a feladatátvételi folyamatról, és bemutatja, hogyan állíthatja be a kezdeti feladatátvételt. 

![1][]

### <a name="setup"></a>Telepítés

Először hozzon létre vagy használjon egy meglévő elsődleges névteret, és egy új másodlagos névteret, és párosítsa a kettőt. Ez a párosítás egy aliast ad meg, amely a kapcsolódáshoz használható. Mivel aliast használ, nem kell módosítania a kapcsolódási karakterláncokat. Csak új névterek adhatók hozzá a feladatátvételi párosításhoz. Végezetül vegyen fel némi figyelést, hogy ellenőrizze, szükség van-e feladatátvételre. A legtöbb esetben a szolgáltatás a nagyméretű ökoszisztémák egyik része, így az automatikus feladatátvétel ritkán lehetséges, mivel a feladatátvételt a többi alrendszerrel vagy infrastruktúrával szinkronizálva kell végrehajtani.

### <a name="example"></a>Példa

Ebben a forgatókönyvben egy példa arra, hogy egy olyan pénztári (POS) megoldást vegyünk fel, amely üzeneteket vagy eseményeket bocsát ki. A Event Hubs átadja ezeket az eseményeket valamilyen leképezési vagy újraformázási megoldásnak, amely ezután a leképezett adatfeldolgozást egy másik rendszerbe továbbítja. Ezen rendszerek esetében előfordulhat, hogy az összes ilyen rendszer ugyanabban az Azure-régióban fut. Az, hogy mikor és milyen részben kell átadni a feladatátvételt, az infrastruktúra adatforgalmának függvénye. 

A feladatátvételt a figyelési rendszerekkel vagy a testreszabott figyelési megoldásokkal automatizálhatja. Az ilyen automatizálás azonban további tervezést és munkát is igénybe vesz, amely nem tartozik a jelen cikk hatálya alá.

### <a name="failover-flow"></a>Feladatátvételi folyamat

Ha elindítja a feladatátvételt, két lépés szükséges:

1. Ha egy másik leállás következik be, újra kell tudnia tenni a feladatátvételt. Ezért állítson be egy másik passzív névteret, és frissítse a párosítást. 

2. A korábbi elsődleges névtérből származó üzenetek lekérése, ha ismét elérhetővé válik. Ezt követően használja ezt a névteret a normál üzenetküldéshez a Geo-helyreállítási beállításon kívül, vagy törölje a régi elsődleges névteret.

> [!NOTE]
> Csak a meghiúsult továbbítási szemantika támogatott. Ebben a forgatókönyvben feladatátvételt hajt végre, majd egy új névtérrel újra párosítja. A sikertelen visszaállítás nem támogatott; például egy SQL-fürtben. 

![2][]

## <a name="management"></a>Kezelés

Ha hibát vétett; Előfordulhat például, hogy a kezdeti beállítás során nem a megfelelő régiókat párosítja, hanem bármikor megszakíthatja a két névtér párosítását. Ha a párosított névtereket normál névtérként szeretné használni, törölje az aliast.

## <a name="samples"></a>Példák

A [githubon található minta](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient) bemutatja, hogyan kell beállítani és kezdeményezni a feladatátvételt. Ez a példa a következő fogalmakat mutatja be:

- A Azure Active Directory a Azure Resource Manager Event Hubs használatával való használatához szükséges beállítások. 
- A mintakód végrehajtásához szükséges lépések. 
- Küldés és fogadás az aktuális elsődleges névtérből. 

## <a name="considerations"></a>Megfontolandó szempontok

Vegye figyelembe a következő szempontokat, hogy ne feledje a jelen kiadást:

1. A tervezés szerint Event Hubs geo-vész-helyreállítás nem replikálja az adatait, ezért nem használhatja fel az elsődleges Event hub régi eltolási értékét a másodlagos esemény központján. Javasoljuk, hogy az Event receivert a következők egyikével indítsa újra:

- *EventPosition. FromStart ()* – ha szeretné beolvasni az összes adatközpontot a másodlagos esemény központján.
- *EventPosition. FromEnd ()* – ha szeretné beolvasni az összes új adatforrást a másodlagos esemény központhoz való kapcsolódáskor.
- *EventPosition. FromEnqueuedTime (datetime)* – ha szeretné beolvasni a másodlagos Event hub-ban fogadott összes adat egy adott dátumtól és időponttól kezdve.

2. A feladatátvétel tervezése során az időtényezőt is figyelembe kell venni. Ha például megszakad a kapcsolat a 15 – 20 percnél hosszabb ideig, dönthet úgy, hogy kezdeményezi a feladatátvételt. 
 
3. Az a tény, hogy nem replikálódnak az adathalmazok, a jelenleg aktív munkamenetek nem replikálódnak. Emellett előfordulhat, hogy a duplikált észlelés és az ütemezett üzenetek nem működnek. Az új munkamenetek, az ütemezett üzenetek és az új ismétlődések is működni fognak. 

4. Egy összetett elosztott infrastruktúra feladatátvétele legalább egyszer [kipróbálható](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) . 

5. Az entitások szinkronizálása hosszabb időt is igénybe vehet, körülbelül 50-100 entitást percenként.

## <a name="availability-zones"></a>Rendelkezésre állási zónák 

A Event Hubs standard SKU támogatja a [Availability Zones](../availability-zones/az-overview.md), amely az Azure-régiókban a hibáktól elkülönített helyet biztosít. 

> [!NOTE]
> Az Azure Event Hubs standard Availability Zones támogatása csak olyan [Azure-régiókban](../availability-zones/az-region.md) érhető el, ahol elérhetők a rendelkezésre állási zónák.

A Availability Zones csak az új névtereken engedélyezheti, a Azure Portal használatával. A Event Hubs nem támogatja a meglévő névterek áttelepítését. A zóna redundancia nem tiltható le, miután engedélyezte azt a névtérben.

![3][]

## <a name="next-steps"></a>További lépések

* A [githubon található minta](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient) egy egyszerű munkafolyamaton keresztül megy át, amely egy geo-párosítást hoz létre, és feladatátvételt kezdeményez a vész-helyreállítási forgatókönyvek esetében.
* A [REST API hivatkozás](/rest/api/eventhub/) a Geo-vész-helyreállítási konfigurációt végző API-kat ismerteti.

Ha további információkat szeretne az Event Hubsról, tekintse meg az alábbi hivatkozásokat:

- Bevezetés az Event Hubs használatába
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)
* [Az Event Hubsot használó mintaalkalmazások](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-geo-dr/geo1.png
[2]: ./media/event-hubs-geo-dr/geo2.png
[3]: ./media/event-hubs-geo-dr/eh-az.png
