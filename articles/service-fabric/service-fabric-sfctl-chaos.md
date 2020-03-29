---
title: Az Azure Service Fabric CLI- sfctl káosz
description: Ismerje meg az sfctl, az Azure Service Fabric parancssori felület. A káosz kezelésére szolgáló parancsok listáját tartalmazza.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 6668446363361fbc6d24afc3d11a36a0b786667d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906163"
---
# <a name="sfctl-chaos"></a>sfctl-káosz
Indítsa el, állítsa le és jelentse a káosz teszt szolgáltatást.

## <a name="subgroups"></a>Alcsoportok
|Alcsoport|Leírás|
| --- | --- |
| [Ütemezése](service-fabric-sfctl-chaos-schedule.md) | Szerezd meg és állítsd be a káosz menetrendjét. |
## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| események | Lekérés a Következő szegmensa a Chaos események alapján a folytatási jogkivonatot vagy az időtartományt. |
| get | Kap a helyzet a Káosz. |
| start | Elindítja a Káosz t a fürtben. |
| állj | Állítsa le a káoszt, ha a fürtben fut, és állítsa le a Káosz ütemezését. |

## <a name="sfctl-chaos-events"></a>sfctl káosz események
Lekérés a Következő szegmensa a Chaos események alapján a folytatási jogkivonatot vagy az időtartományt.

A Chaos események következő szegmensének lekéréséhez megadhatja a ContinuationToken.To get the next segment of the Chaos events, you can specify the ContinuationToken. A Chaos események új szegmensének elindításához megadhatja az időtartományt a StartTimeUtc és az EndTimeUtc között. A continuationtoken és az időtartomány nem adható meg ugyanabban a hívásban. Ha több mint 100 Chaos-esemény van, a Chaos események több szegmensben jelennek meg, ahol egy szegmens legfeljebb 100 Chaos-eseményt tartalmaz, és a következő szegmenst, amelyet az API-ra a folytatási jogkivonattal hív meg.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --folytatás-token | A folytatási token paraméter az eredmények következő készletének elérésére szolgál. A folytatási jogkivonat egy nem üres érték szerepel az API válasza, ha a rendszer eredményei nem férnek el egyetlen válasz. Ha ezt az értéket a következő API-hívás, az API adja vissza a következő eredményhalmaz. Ha nincsenek további eredmények, akkor a folytatási jogkivonat nem tartalmaz értéket. Ennek a paraméternek az értékét nem szabad URL-kódolásra kódolni. |
| --vég-idő-utc | A Windows-fájl idő, amely annak az időtartománynak a befejezési idejét jelöli, amelyhez Chaos-jelentést kell létrehozni. A részletekért tekintse meg a [DateTime.ToFileTimeUtc metódust.](https\://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx) |
| --max-eredmények | A lapozható lekérdezések részeként visszaadandó eredmények maximális száma. Ez a paraméter határozza meg a visszaadott eredmények számának felső határát. A visszaadott eredmények kisebbek lehetnek, mint a megadott maximális eredmények, ha nem férnek el az üzenetben a konfigurációban megadott maximális üzenetméret-korlátozások nak megfelelően. Ha ez a paraméter nulla vagy nincs megadva, a lapozható lekérdezés a lehető legtöbb eredményt tartalmazza, amely elfér a visszatérési üzenetben. |
| --kezdés-idő-utc | A Windows-fájl ideje, amely annak az időtartománynak a kezdési idejét jelöli, amelyhez Chaos-jelentést kell létrehozni. A részletekért tekintse meg a [DateTime.ToFileTimeUtc metódust.](https\://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx) |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-chaos-get"></a>sfctl káosz kap
Kap a helyzet a Káosz.

Chaos állapotának beírása arról, hogy fut-e a Chaos, a Chaos futtatásához használt Chaos paraméterek és a Chaos Schedule állapota.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-chaos-start"></a>sfctl káosz kezdete
Elindítja a Káosz t a fürtben.

Ha a Chaos még nem fut a fürtben, elindítja a Chaos-t a Chaos paraméterekkel. Ha a Chaos már fut a hívás meghívásakor, a hívás sikertelen lesz a FABRIC_E_CHAOS_ALREADY_RUNNING hibakóddal. További részletekért tekintse meg a cikkben a szabályozott káosz a [service Fabric-fürtökben](https\://docs.microsoft.com/azure/service-fabric/service-fabric-controlled-chaos) című cikket.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --app-típus-egészségügyi-politika-térkép | JSON kódolású lista, amelynek maximális százalékos százalékos nem megfelelő állapotú alkalmazásai adott alkalmazástípusokhoz. Minden bejegyzés kulcsként adja meg az alkalmazás típusnevét, és értékként egy egész szám, amely a megadott alkalmazástípus alkalmazásainak kiértékeléséhez használt MaxPercentUnhealthyApplications százalékot jelöli. <br><br> Meghatározott alkalmazástípusokhoz tartozó, a sérült alkalmazások maximális százalékos arányával rendelkező leképezést határoz meg. Minden bejegyzés kulcsként adja meg az alkalmazás típusnevét, és értékként egy egész szám, amely a megadott alkalmazástípus alkalmazásainak kiértékeléséhez használt MaxPercentUnhealthyApplications százalékot jelöli. Az alkalmazástípus állapotházirend-leképezése a fürt állapotának kiértékelése során speciális alkalmazástípusok leírására használható. A térképben szereplő alkalmazástípusokat a rendszer a térképben megadott százalékkal szemben értékeli ki, és nem a fürt állapotházirendjében definiált globális MaxPercentUnhealthyApplications alkalmazásokkal. A térképen megadott alkalmazástípusok alkalmazásai nem számítanak bele az alkalmazások globális készletéhez. Ha például egy adott típusú alkalmazás kritikus fontosságú, a fürt rendszergazdája hozzáadhat egy bejegyzést az adott alkalmazástípus leképezéséhez, és 0%- os értéket rendelhet hozzá (azaz nem tolerálja a hibákat). Az összes többi alkalmazás kiértékelhető, ha a MaxPercentUnhealthyApplications 20%-ra van állítva, hogy az alkalmazáspéldányok ezrei közül bizonyos hibákat toleráljon. Az alkalmazástípus állapotházirend-leképezése csak akkor használatos, ha a fürtjegyzék lehetővé teszi az alkalmazástípus állapotának kiértékelését a HealthManager/EnableApplicationTypeHealthEvaluation konfigurációs bejegyzéshasználatával. |
| --chaos-target-szűrő | JSON kódolt szótár két karakterlánc típusú kulcsokkal. A két kulcs a NodeTypeInclusionList és az ApplicationInclusionList. Mindkét kulcs értékei karakterláncok listája. chaos_target_filter határozza meg az összes szűrőt a célzott Chaos hibák, például a hibás csak bizonyos csomóponttípusok vagy csak bizonyos alkalmazások hibás. <br><br> Ha chaos_target_filter nem használja, a Chaos az összes fürtentitást hibát okoz. Ha chaos_target_filter van használva, a Chaos csak azokat az entitásokat hibáztatja, amelyek megfelelnek a chaos_target_filter specifikációnak. A NodeTypeInclusionList és az ApplicationInclusionList csak egyesítő szemantikát engedélyez. A NodeTypeInclusionList és az ApplicationInclusionList csomópontja nem adható meg. Például nem lehet megadni "hiba ez az alkalmazás csak akkor, ha az adott csomópont típus." Ha egy entitás szerepel a NodeTypeInclusionList vagy az ApplicationInclusionList listán, az entitás nem zárható ki a ChaosTargetFilter használatával. Még akkor is, ha az applicationX nem jelenik meg ApplicationInclusionList, néhány Chaos iterációs applicationX lehet hibás, mert történetesen egy csomópont nodeTypeY, amely szerepel NodeTypeInclusionList. Ha a NodeTypeInclusionList és az ApplicationInclusionList is üres, argumentexception jelenik meg. Minden típusú hiba (újraindítás csomópont, újraindítás kódcsomag, távolítsa replika, újraindítás replika, áthelyezés elsődleges, és másodlagos áthelyezése) engedélyezve vannak a csomópontok ezen csomóponttípusok. Ha egy csomóponttípus (például NodeTypeX) nem jelenik meg a NodeTypeInclusionList listában, akkor a NodeTypeX csomópontjaihoz soha nem lesz engedélyezve csomópontszintű hibák (például NodeRestart), de a kódcsomag és a replikahibák továbbra is engedélyezhetők a NodeTypeX-hez, ha a NodeTypeX alkalmazása továbbra is engedélyezhető. Az ApplicationInclusionList történetesen a NodeTypeX csomópontján található. Legfeljebb 100 csomóponttípus szerepelhet ebben a listában, a szám növeléséhez a MaxNumberOfNodeTypesInChaosEntityFilter konfigurációhoz konfigurációs frissítés szükséges. Ezeknek az alkalmazásoknak a szolgáltatásaihoz tartozó összes replikahiba (replika újraindítása, replika eltávolítása, elsődleges áthelyezése és másodlagos áthelyezése) a Chaos által. A Chaos csak akkor indíthat újra egy kódcsomagot, ha a kódcsomag csak ezen alkalmazások replikáit üzemelteti. Ha egy alkalmazás nem jelenik meg ebben a listában, akkor is hibás lehet néhány Chaos iterációban, ha az alkalmazás a NodeTypeInclusionList listában szereplő csomóponttípus csomópontjára kerül. Ha azonban az applicationX az elhelyezési megkötéseken keresztül a nodeTypeY-hoz van kötve, és az applicationX hiányzik az ApplicationInclusionList list-ből, és a nodeTypeY hiányzik a NodeTypeInclusionList list-ből, akkor az applicationX soha nem lesz hibás. Legfeljebb 1000 alkalmazásnév szerepelhet ebben a listában, a szám növeléséhez a MaxNumberOfApplicationsInChaosEntityFilter konfigurációhoz konfigurációs frissítés szükséges. |
| --kontextus | JSON kódolású (karakterlánc, karakterlánc) típusú kulcs-érték párok. A térkép segítségével rögzítheti a Chaos futással kapcsolatos információkat. Nem lehet több, mint 100 ilyen pár, és minden karakterlánc (kulcs vagy érték) legfeljebb 4095 karakter hosszú lehet. Ezt a térképet a Chaos run kezdője állítja be, hogy az adott futtatás környezetét tetszés szerint tárolja. |
| --disable-move-replika-hibák | Letiltja az áthelyezési elsődleges állapotot, és áthelyezi a másodlagos hibákat. |
| --max-cluster-stabilizáció | A maximális ideig kell várni, amíg az összes fürtentitás stabil és kifogástalan lesz.  Alapértelmezett\: 60. <br><br> A Chaos iterációkban hajt végre, és minden egyes iteráció kezdetén ellenőrzi a fürtentitások állapotát. Az ellenőrzés során, ha egy fürtentitás nem stabil és kifogástalan a MaxClusterStabilizationTimeoutInSeconds-n belül, a Chaos sikertelen érvényesítési eseményt hoz létre. |
| --max-egyidejű hibák | Az iteráció által kiváltott egyidejű hibák maximális száma. A káosz iterációkban hajt végre, és két egymást követő ismétlést egy érvényesítési fázis választ el egymástól. Minél magasabb az egyidejűség, annál agresszívebb a hibák befecskendezése- összetettebb államsorozatokat idézve elő a hibák feltárására. Az ajánlás az, hogy 2 vagy 3 értékkel kezdje, és legyen óvatos, miközben felfelé halad.  Alapértelmezett\: 1. |
| --max-százalék-egészségtelen-apps | A fürt állapotának chaos során történő kiértékelésekor a nem megfelelő állapotú alkalmazások maximálisan engedélyezett százaléka hiba jelentéselőtt. <br><br> A nem megfelelő állapotú alkalmazások maximálisan engedélyezett százaléka a hiba jelentésének bejelentése előtt. Ha például az alkalmazások 10%-a nem megfelelő állapotú, ez az érték 10. A százalékos érték az alkalmazások maximális tolerálható százalékát jelöli, amelyek nem megfelelő állapotúak lehetnek, mielőtt a fürt hibásnak minősülne. Ha a százalékos érték tiszteletben van, de legalább egy nem megfelelő állapotú alkalmazás van, az állapot figyelmeztetésként lesz kiértékelve. Ezt úgy számítja ki, hogy elosztja a nem megfelelő állapotú alkalmazások számát a fürtben lévő alkalmazáspéldányok teljes számával, kivéve az ApplicationTypeHealthPolicyMap-ben szereplő alkalmazástípusok alkalmazásait. A számítás kerekíti, hogy tolerálja egy hiba kis számú alkalmazások. Az alapértelmezett százalék nulla. |
| --max-százalék-egészségtelen-csomópontok | A fürt állapotának chaos során történő kiértékelésekor a nem megfelelő állapotú csomópontok maximálisan engedélyezett százaléka hiba jelentéselőtt. <br><br> A nem megfelelő állapotú csomópontok maximálisan engedélyezett százaléka a hiba jelentése előtt. Ha például a csomópontok 10%-a nem megfelelő állapotú, ez az érték 10. A százalékos érték a csomópontok maximális tolerálható százalékát jelöli, amely nem megfelelő állapotú lehet, mielőtt a fürt hibásnak minősülne. Ha a százalékos érték tiszteletben van, de legalább egy nem megfelelő állapotú csomópont van, az állapot figyelmeztetésként lesz kiértékelve. A százalékos értéket úgy számítja ki, hogy elosztja a nem megfelelő állapotú csomópontok számát a fürt ben lévő csomópontok teljes számával. A számítás kerekít, hogy tolerálja a kis számú csomópontok egy hiba. Az alapértelmezett százalék nulla. Nagy fürtök, egyes csomópontok mindig le vagy ki a javításokat, ezért ezt a százalékot úgy kell beállítani, hogy tolerálja ezt. |
| --idő-hoz-fuss | Az a teljes idő (másodpercben), amelynél a Chaos automatikusan leállna. A megengedett maximális érték 4 294 967 295 (System.UInt32.MaxValue).  Alapértelmezett\: érték 4294967295. |
| --idő-out -t | Alapértelmezett\: 60. |
| --várakozási idő-hibák között | Várakozási idő (másodpercben) az egymást követő hibák között egyetlen iteráción belül.  Alapértelmezett\: 20. <br><br> Minél nagyobb az érték, annál kisebb a hibák közötti átfedés, és annál egyszerűbb az állapotátmenetek sorrendje, amelyen a fürt keresztülmegy. Az ajánlás az, hogy 1 és 5 közötti értékkel kezdje, és legyen óvatos, miközben felfelé halad. |
| --várakozási idő-iterációk között | Időelválasztás (másodpercben) a Chaos két egymást követő ismétlése között. Minél nagyobb az érték, annál alacsonyabb a hibabefecskendezési sebesség.  Alapértelmezett\: 30. |
| --figyelmeztetés hibaként | Azt jelzi, hogy a figyelmeztetések kezelése ugyanolyan súlyos, mint a hibák. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-chaos-stop"></a>sfctl káosz stop
Állítsa le a káoszt, ha a fürtben fut, és állítsa le a Káosz ütemezését.

Megakadályozza, hogy a Chaos új hibákat hajtson végre. A repülés közbeni hibák a teljes benemesedésig folytatódnak. A jelenlegi Káosz ütemezés e-g. Ha egy ütemterv levan állítva, akkor marad a leállított állapotban, és nem használható Chaos ütemezése új fut a Chaos. Az ütemezés folytatásához új Káosz ütemezést kell beállítani.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |


## <a name="next-steps"></a>További lépések
- [Állítsa be](service-fabric-cli.md) a Service Fabric CLI-t.
- Ismerje meg, hogyan használhatja a Service Fabric CLI-t a [mintaparancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application)használatával.