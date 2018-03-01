---
title: Az Azure Service Fabric CLI - sfctl choas |} Microsoft Docs
description: A Service Fabric CLI sfctl chaos parancsok ismerteti.
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 02/22/2018
ms.author: ryanwi
ms.openlocfilehash: 34e4d47b1de509c2053996d9d1078733d7055447
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2018
---
# <a name="sfctl-chaos"></a>sfctl-káosz
Indítás, Leállítás, és a chaos teszt szolgáltatás jelentést.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
|    jelentés| A következő szegmensnél az átadott-a folytatási kód vagy átadott-időtartománya alapján Chaos jelentés lekérése.|
|    start | A fürt Chaos elindul.|
|    állj  | Chaos leáll a fürtben már fut, ha más módon azt nincs semmi hatása.|


## <a name="sfctl-chaos-report"></a>sfctl chaos jelentés
A következő szegmensnél az átadott-a folytatási kód vagy átadott-időtartománya alapján Chaos jelentés lekérése.

Megadhatja a következő szegmens Chaos jelentés beolvasandó ContinuationToken StartTimeUtc és EndTimeUtc időtartományt adhat meg, de nem adható meg mind a ContinuationToken, és az időtartományt az adott hívásban. Ha több mint 100 Chaos események, a Chaos jelentés eredmény abban az esetben szegmensek Ha a szegmens ne legyen nagyobb 100 Chaos események tartalmaz. 

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| ---folytatási| A folytatási támogatójogkivonat-paramétere a következő set eredmények beszerzésére szolgál. Az eredményeket a rendszer egyetlen válasz nem férnek el a folytatási kód nem üres érték szerepel az API-t adott válaszokat. Ha ez az érték átadása a következő API-hívás, hogy az API-t az eredmények következő készletet ad vissza. Ha további eredménytelen majd az a folytatási kód nem tartalmaz értéket. Ez a paraméter értéke nem lehet URL-kódolású.|
| --end-time-utc   | A Windows, amelynek Chaos jelentés generálása az időtartomány jelképező idő fájlt. További részleteket [DateTime.ToFileTimeUtc metódus](https://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx) részleteiről.|
| --start-time-utc | A Windows, amelynek Chaos jelentés generálása az időtartományt a kezdési idejét jelző idő fájlt. További részleteket [DateTime.ToFileTimeUtc metódus](https://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx) részleteiről.|
| – időtúllépés -t     | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug          | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h        | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o      | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
| --lekérdezés          | JMESPath lekérdezési karakterlánc. További információt és példákat http://jmespath.org/ talál.|
| – részletes        | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-chaos-start"></a>sfctl chaos indítása
A fürt Chaos elindul.

Ha Chaos már nem fut a fürtön, kezdődik Chaos az átadott Chaos paraméterek. Ha Chaos már fut, amikor a rendszer telefonhívást indít, a következő hibakóddal FABRIC_E_CHAOS_ALREADY_RUNNING a hívás sikertelen lesz.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – alkalmazás-típus-rendszerállapot-házirend-leképezés  | JSON kódolású listában megadott alkalmazás esetében maximális százalékos aránya a nem megfelelő alkalmazásokkal. A leírásokban lesz a kulcs az alkalmazás neve, és értékeként egész számot, amely a megadott alkalmazás típusú alkalmazások értékeléséhez használt MaxPercentUnhealthyApplications százalékban adja meg. Meghatározza a térkép maximális százalékos aránya a nem megfelelő alkalmazásokkal adott alkalmazás esetében. Mindegyik bejegyzés kulcsa az alkalmazás neve, és értékeként egész számot, amely a megadott alkalmazás típusú alkalmazások értékeléséhez használt MaxPercentUnhealthyApplications százalékban adja meg. Az alkalmazás típusa állapotfigyelő házirend-hozzárendelés segítségével fürt állapot kiértékelésekor különleges alkalmazástípusokat ismerteti. Az alkalmazástípusok, a leképezésben szerepel rendszer összehasonlítja a térképen, nem pedig a globális MaxPercentUnhealthyApplications meghatározott a fürt megadott százalék. Az alkalmazások a leképezésben megadott alkalmazástípus nem számítanak a globális készlet alkalmazások ellen. Például, ha bizonyos alkalmazások típusú kritikus, a fürt rendszergazda adjon hozzá egy bejegyzést a térkép az adott alkalmazás és rendelje hozzá egy értéket 0 % (Ez azt jelenti, hogy nem elviselni hiba). Minden más alkalmazást a MaxPercentUnhealthyApplications állítható 20 % tűrését alkalmazáspéldányok ezer kívül néhány hiba kiértékelése. Az alkalmazás típusa állapotfigyelő házirend-hozzárendelés csak akkor, ha a fürtjegyzékben lehetővé teszi, hogy az alkalmazás típusa állapotának kiértékelését használja a konfigurációs bejegyzés HealthManager/EnableApplicationTypeHealthEvaluation szolgál.|
|--chaos-target-filter         | JSON szótári két karakterlánc típusú kulcs kódolva. A két kulcsai NodeTypeInclusionList és ApplicationInclusionList. Ezek a kulcsok mindkét értékei karakterláncok listáját. chaos_target_filter összes szűrőt a célzott Chaos hibák, például csak egyes csomóponttípusok hibás, vagy csak bizonyos alkalmazások hibás határozza meg. Ha nem használja a chaos_target_filter, Chaos hibákat entitásokhoz fürt. Chaos_target_filter használata esetén a Chaos hibákat csak az entitásokat, amelyek megfelelnek a chaos_target_filter megadását. NodeTypeInclusionList és ApplicationInclusionList engedélyezése csak a "union" szemantikáját. Nincs lehetőség a NodeTypeInclusionList és ApplicationInclusionList metszetét adja meg. Például nincs lehetőség a adja meg a "hiba az alkalmazás csak akkor, ha be van kapcsolva, hogy csomóponttípus." Entitás NodeTypeInclusionList vagy ApplicationInclusionList szerepel, ha az entitás nem zárható ChaosTargetFilter használatával. Akkor is, ha applicationX ApplicationInclusionList nem jelenik meg, néhány Chaos munkamenetben applicationX is hibát jelez, mert akkor fordul elő, egy olyan csomópontján, amely megtalálható a NodeTypeInclusionList nodeTypeY lennie. Ha NodeTypeInclusionList és ApplicationInclusionList is üres, egy ArgumentException vált ki. Hibák összes típusú (csomópont újraindítása, indítsa újra a kódcsomag, távolítsa el a replika, indítsa újra a replikát, áthelyezése elsődleges és másodlagos áthelyezése) a következő csomópont típusú csomópontok engedélyezve vannak. Ha a csomóponttípus (azaz NodeTypeX) nem szerepel a NodeTypeInclusionList, akkor soha nem lesz engedélyezve NodeTypeX csomópontjait csomópont szintű hibák (például NodeRestart), de a kód csomag és a replika hibák továbbra is engedélyezhető az NodeTypeX Ha az alkalmazás a ApplicationInclusionList NodeTypeX csomópontja elhelyezése történik. Legfeljebb 100 csomópont típusnevek tartalmazhat erre a listára, ezt a számot növelheti, a konfiguráció frissítése a MaxNumberOfNodeTypesInChaosEntityFilter konfigurációhoz szükség. Az alkalmazások szolgáltatásokhoz tartozó összes replika replika hibák (újraindítás replika, távolítsa el a replika, áthelyezés elsődleges és áthelyezése másodlagos) Chaos által kezelhető. Chaos előfordulhat, hogy indítsa újra a kódcsomag, csak akkor, ha a kódcsomag üzemeltető replikái csak ezeket az alkalmazásokat. Ha egy alkalmazás nem jelenik meg ebben a listában, azt is továbbra is hibát jelez néhány Chaos munkamenetben, ha az alkalmazás fejeződik be, amely megtalálható a NodeTypeInclusionList csomópont típusú csomóponton. Azonban ha applicationX van kötve egy elhelyezési korlátozás és applicationX nodeTypeY hiányzik a ApplicationInclusionList és nodeTypeY hiányzik a NodeTypeInclusionList, majd applicationX soha nem hibát jelez majd. Legfeljebb 1000 alkalmazásnevek tartalmazhat erre a listára, ezt a számot növelheti, a konfiguráció frissítése a MaxNumberOfApplicationsInChaosEntityFilter konfigurációhoz szükség.|
|– a környezetben                     | (Karakterlánc, karakterlánc) JSON-kódolású térkép írja be a kulcs-érték párokat. A térkép Chaos futtatás adatainak rögzítésére is használható. Nem lehet 100-nál több ilyen párok, és minden karakterlánc (kulcs vagy érték), legfeljebb 4095 karakter hosszú lehet. Ez a térkép a Chaos tárolását a környezetben a meghatározott Futtatás futtassa az alapszintű állítja be.|
| --disable-move-replica-faults | Letiltja az áthelyezés elsődleges és másodlagos hibák áthelyezése.|
| --max-cluster-stabilization| Az összes fürt, miközben stabillá és kifogástalan állapotú entitások várakozási idő maximális mennyisége.  Alapértelmezett: 60. Chaos ismétlési hajtja végre, és minden egyes ismétlés elején fürt entitások állapotát ellenőrzi. Érvényesítése során egy fürt entitás nincs stabil és kifogástalan belül MaxClusterStabilizationTimeoutInSeconds, ha Chaos hoz létre egy érvényesítése sikertelen esemény.|
| --max-concurrent-faults    | Maximális száma párhuzamos hibák száma az iteráció előidézett.           Alapértelmezett: 1. Ismétlési Chaos hajtja végre, és két egymást követő ismétlési elválasztott egy érvényesítési fázist. Minél nagyobb a feldolgozási, a szigorúbb hibák – hogy hibák felfedésére állapotok összetettebb sorozatát fájlbeszúrás. A javaslat értéke 2 vagy 3 érték kezdődik, és körültekintően járjon el áthelyezése közben.|
| --max-percent-unhealthy-apps  | Fürt állapotfigyelő Chaos során kiértékelésekor az engedélyezett maximális százalékos aránya a nem megfelelő alkalmazások mielőtt hibajelzést. Az engedélyezett maximális százalékos aránya a nem megfelelő alkalmazások mielőtt hibajelzést. Ahhoz, hogy 10 %-a nem kifogástalan, hogy az alkalmazások, például ez az érték lenne 10. A százalékos alkalmazásokat, amelyek nem megfelelő állapotú lehet, mielőtt a fürt tekinthető hiba a maximális megengedett százalékát jelenti. Ha százalékos tiszteletben tartják, de legalább egy nem megfelelő alkalmazás, az egészségügyi értékeli – figyelmeztetés. Ez kiszámítása a nem megfelelő alkalmazások száma a teljes száma a fürtben, alkalmazások, amelyek szerepelnek az applicationtypehealthpolicymap paraméter hiányzó értékei alkalmazástípus kizárásának alkalmazáspéldányok keresztül. A számítási kerekít alkalmazások kis számú hiba működését. Alapértelmezett százalékos értéke nulla.|
| --max-percent-unhealthy-nodes | Fürt állapotfigyelő Chaos során kiértékelésekor az engedélyezett maximális százalékos aránya nem kifogástalan csomópontokat mielőtt hibajelzést. Az engedélyezett maximális százalékos aránya nem kifogástalan csomópontokat mielőtt hibajelzést. Ahhoz, hogy 10 % a csomópontok sérült állapotot jelez, például ez az érték lenne 10. A százalékos arányát a csomópontokra, amelyeket nem megfelelő állapotú lehet, mielőtt a fürt tekinthető hiba a maximális megengedett százalékát jelenti. A százalékos tiszteletben tartják, de legalább egy nem megfelelő állapotú csomóponti, ha a rendszerállapot – figyelmeztetés értékeli. Az érték kiszámításához igény szerinti felosztásával sérült csomópontok száma a fürtben található csomópontok száma keresztül. A számítási csomópontok kis számú hiba tűrését kerekít. Alapértelmezett százalékos értéke nulla. Nagy fürtök egyes csomópontok mindig lesz le, vagy ki a javításához, így ezen százalékos arány kell konfigurálni, amely elviselni.|
| --idő futtatásra              | Teljes időtartam (másodpercben), amelynek Chaos automatikusan leállítása előtt fog futni. A maximális megengedett értéke 4 294 967 295 (System.UInt32.MaxValue).  Alapértelmezett: 4294967295.|
| – időtúllépés -t               | Időtúllépését másodpercben.  Alapértelmezett: 60.|
| --wait-time-between-faults | Várakozási idő (másodpercben) belül egy egyetlen munkamenetben egymást követő hibák között.  Alapértelmezett: 20. Minél nagyobb értéke, annál kisebb, amely a fürt végighalad az átfedő hibák és a egyszerűbb közötti állapot sorozatát átmenetek. Az ajánljuk, hogy kezdje az 1 és 5 és a gyakorlatban körültekintően áthelyezése közben közötti értéket.|
| --várakozási-idő-közötti-ismétlés| Idő-elkülönítése (másodpercben) Chaos két egymást követő ismétlésének. Minél nagyobb értéket, a injektálási minél kisebb gyakorisága. Alapértelmezett: 30.|
| --warning-as-error         | Fürt állapotfigyelő Chaos során kiértékelésekor figyelmeztetések hibaként kiegészített az azonos.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug                    | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h                  | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o                | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.           Alapértelmezett: JSON-ná.|
| --lekérdezés                    | JMESPath lekérdezési karakterlánc. További információt és példákat http://jmespath.org/ talál.|
| – részletes                  | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-chaos-stop"></a>sfctl chaos leállítása
Chaos leáll a fürtben már fut, ha más módon azt nincs semmi hatása.

Leállítja Chaos ütemezési további hibák; de a üzenetsoroktól hibák, nem érintettek.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – időtúllépés -t| Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug  | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h| Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
| --lekérdezés  | JMESPath lekérdezési karakterlánc. További információt és példákat http://jmespath.org/ talál.|
| – részletes| Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="next-steps"></a>További lépések
- [A telepítő](service-fabric-cli.md) a Service Fabric CLI-t.
- A Service Fabric parancssori felület használatával használata a [minta parancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application).