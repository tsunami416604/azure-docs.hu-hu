---
title: Apache Kafka-ügyfelek ajánlott konfigurációi – Azure Event Hubs
description: Ez a cikk az Azure Event Hubs for Apache Kafka szolgáltatással kommunikáló ügyfelek számára ajánlott Apache Kafka konfigurációkat tartalmazza.
ms.topic: reference
ms.date: 07/20/2020
ms.openlocfilehash: f9a03d1d3433461a575b32cd69893408a8b0ef97
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87096646"
---
# <a name="recommended-configurations-for-apache-kafka-clients"></a>Apache Kafka ügyfelek ajánlott konfigurációi
Az alábbi, az Azure Event Hubs Apache Kafka ügyfélalkalmazások általi használatát ajánlott konfigurációk. 

## <a name="java-client-configuration-properties"></a>Java-ügyfél konfigurációjának tulajdonságai

### <a name="producer-and-consumer-configurations"></a>Gyártói és fogyasztói konfigurációk

Tulajdonság | Ajánlott értékek | Engedélyezett tartomány | Jegyzetek
---|---:|-----:|---
`metadata.max.age.ms` | 180000 (hozzávetőleges) | < 240000 | A metaadatok módosításának hamarabb is lecsökkenthető.
`connections.max.idle.ms`   | 180000 | < 240000 | Az Azure bezárja a bejövő TCP üresjárati > 240 000 MS-ot, ami a küldési időkorlát miatt elhalt kapcsolatok küldését eredményezheti (lejárt kötegek megjelenítve).

### <a name="producer-configurations-only"></a>Csak a termelői konfigurációk
A termelői konfigurációk [itt](https://kafka.apache.org/documentation/#producerconfigs)találhatók.

Tulajdonság | Ajánlott értékek | Engedélyezett tartomány | Jegyzetek
---|---:|---:|---
`max.request.size` | 1000000 | < 1046528 | Ha 1 046 528 bájtnál nagyobb kérelmeket küld, a szolgáltatás lezárul a kapcsolat.  *Ezt az **értéket módosítani kell,** és problémákat okozhat a nagy átviteli sebességű előállítási helyzetekben.*
`retries` | > 0 | | Növekvő delivery.timeout.ms értéket igényelhet, lásd a dokumentációt.
`request.timeout.ms` | 30000.. 60000 | > 20000| EH belsőleg alapértelmezés szerint legalább 20 000 MS-ot tesz ki.  *Az alacsonyabb időtúllépési értékkel rendelkező kérelmek elfogadásakor az ügyfél viselkedése nem garantált.*
`metadata.max.idle.ms` | 180000 | > 5000 | Azt szabályozza, hogy a gyártó mennyi ideig gyorsítótárazza a metaadatokat egy tétlen témakörhöz. Ha a témakör utolsó előállítása óta eltelt idő meghaladja a metaadatok üresjárati időtartamát, akkor a témakör metaadatait elfelejti a rendszer, és a következőhöz való hozzáférés egy Metaadat-beolvasási kérést fog kényszeríteni.
`linger.ms` | > 0 | | A nagy átviteli sebességű forgatókönyvek esetében az linger értékének meg kell egyeznie a legnagyobb elfogadható értékkel, hogy kihasználhassa a kötegelt feldolgozás előnyeit.
`delivery.timeout.ms` | | | A képletnek ( `request.timeout.ms`  +  `linger.ms` ) * megfelelően van beállítva `retries` .
`enable.idempotence` | hamis | | A idempotencia jelenleg nem támogatott.
`compression.type` | `none` | | A tömörítés jelenleg nem támogatott.

### <a name="consumer-configurations-only"></a>Csak a fogyasztói konfigurációk
A fogyasztói konfigurációk [itt](https://kafka.apache.org/documentation/#consumerconfigs)találhatók.

Tulajdonság | Ajánlott értékek | Engedélyezett tartomány | Jegyzetek
---|---:|-----:|---
`heartbeat.interval.ms` | 3000 | | 3000 az alapértelmezett érték, ezért nem módosítható.
`session.timeout.ms` | 30000 |6000.. 300000| Az 30000-as számú kezdettel növelheti, ha a kihagyott szívverés miatt a gyakori újrakiegyensúlyozást látja.


## <a name="librdkafka-configuration-properties"></a>librdkafka-konfigurációs tulajdonságok
A fő `librdkafka` konfigurációs fájl ([hivatkozás](https://github.com/edenhill/librdkafka/blob/master/CONFIGURATION.md)) az alábbi tulajdonságok kiterjesztett leírásait tartalmazza.

### <a name="producer-and-consumer-configurations"></a>Gyártói és fogyasztói konfigurációk

Tulajdonság | Ajánlott értékek | Engedélyezett tartomány | Jegyzetek
---|---:|-----:|---
`socket.keepalive.enable` | true | | Akkor szükséges, ha a kapcsolódás várhatóan üresjáratban van.  Az Azure a bejövő TCP inaktív > 240 000 MS-ot fogja lezárva.
`metadata.max.age.ms` | ~ 180000| < 240000 | A metaadatok módosításának hamarabb is lecsökkenthető.

### <a name="producer-configurations-only"></a>Csak a termelői konfigurációk

Tulajdonság | Ajánlott értékek | Engedélyezett tartomány | Jegyzetek
---|---:|-----:|---
`retries` | > 0 | | Az alapértelmezett érték a 2. Javasoljuk, hogy tartsa meg ezt az értéket. 
`request.timeout.ms` | 30000.. 60000 | > 20000| EH belsőleg alapértelmezés szerint legalább 20 000 MS-ot tesz ki.  `librdkafka` az alapértelmezett érték 5000, ami problematikus lehet. *Az alacsonyabb időtúllépési értékkel rendelkező kérelmek elfogadásakor az ügyfél viselkedése nem garantált.*
`partitioner` | `consistent_random` | Lásd: librdkafka dokumentáció | `consistent_random` az alapértelmezett és a legjobb.  Az üres és a null kulcsok kezelése a legtöbb esetben ideális megoldás.
`enable.idempotence` | hamis | | A idempotencia jelenleg nem támogatott.
`compression.codec` | `none` || A tömörítés jelenleg nem támogatott.

### <a name="consumer-configurations-only"></a>Csak a fogyasztói konfigurációk

Tulajdonság | Ajánlott értékek | Engedélyezett tartomány | Jegyzetek
---|---:|-----:|---
`heartbeat.interval.ms` | 3000 || 3000 az alapértelmezett érték, ezért nem módosítható.
`session.timeout.ms` | 30000 |6000.. 300000| Az 30000-as számú kezdettel növelheti, ha a kihagyott szívverés miatt a gyakori újrakiegyensúlyozást látja.


## <a name="further-notes"></a>További megjegyzések

Tekintse meg az alábbi táblázatot a konfigurációval kapcsolatos gyakori hibákról.

Hibajelenségek | Probléma | Megoldás
----|---|-----
Terheléselosztási hibák eltolása | A fogyasztó túl sokáig vár a lekérdezés () és a szolgáltatás között, és a felhasználó kirúgja a csoportot. | Erre számos lehetősége van: <ul><li>munkamenet időkorlátjának emelése</li><li>az üzenet batch-méretének csökkentése a feldolgozás felgyorsításához</li><li>a párhuzamos feldolgozásának javítása a fogyasztók blokkolása érdekében. lekérdezés ()</li></ul> A három kombináció alkalmazása valószínűleg legbölcsebb.
Hálózati kivételek nagy teljesítményű átviteli sebességgel | A Java Client + default max. Request. size értéket használja?  Előfordulhat, hogy a kérések túl nagyok. | Lásd a fenti Java-konfigurációkat.

## <a name="next-steps"></a>További lépések
Tekintse meg az [Azure-előfizetések és-szolgáltatások korlátozásait, kvótáit és korlátozásait](..//azure-resource-manager/management/azure-subscription-service-limits.md) az összes Azure-szolgáltatás kvótái és korlátai tekintetében. 
