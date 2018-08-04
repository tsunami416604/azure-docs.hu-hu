---
title: Az Azure Service Fabric parancssori felület - sfctl-káosz |} A Microsoft Docs
description: Ismerteti a Service Fabric parancssori felület sfctl-káosz parancsokat.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 07/31/2018
ms.author: bikang
ms.openlocfilehash: 68799fe2755d8c0e811ae217f2cf6ceb84c7d869
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39494226"
---
# <a name="sfctl-chaos"></a>sfctl-káosz
Indítása, leállítása és a chaos jelentés tesztelheti a szolgáltatást.

## <a name="subgroups"></a>Alcsoportok
|Alcsoport|Leírás|
| --- | --- |
| [schedule](service-fabric-sfctl-chaos-schedule.md) | GET, és a chaos ütemezés beállításához. |
## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| események | A Chaos események a folytatási token vagy időtartomány alapján a következő szegmens beolvasása. |
| Get | A Chaos állapotának lekéréséhez. |
| start | A Chaos elindítja a fürtben. |
| állj | Leállítja a káosz, ha a fürt fut, és helyezze a Chaos ütemezés leállított állapotban. |

## <a name="sfctl-chaos-events"></a>sfctl-káosz események
A Chaos események a folytatási token vagy időtartomány alapján a következő szegmens beolvasása.

A következő szegmenst a Chaos események beolvasása, megadhatja a continuationtoken argumentumot használja. Egy számára új szegmensben káosz események kezdetét lekéréséhez megadhatja az időtartományt StartTimeUtc és EndTimeUtc. Az adott hívásban nem adható meg mind a continuationtoken argumentumot használja, és az időtartományt. Ha több mint 100 káosz események, a Chaos események több szegmensben, ha egy szegmens tartalmazza: 100-nál több káosz események, valamint hogy a következő szegmensben adja vissza, győződjön meg arról, az API-hoz a folytatási kód hívása.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| ---folytatási kód | A folytatási token paraméter eredmények következő készletét beszerzésére használatos. Az eredményeket a rendszer nem férnek el egyetlen válasz egy folytatási tokent egy nem üres értékkel szerepel az API-válasz. Ha ez az érték átadott, a következő API-hívás az API-t az eredmények tovább készletet ad vissza. Ha nincsenek további eredmények, a folytatási token neobsahuje értéket. Ez a paraméter értéke nem lehet URL-kódolású. |
| --end-time-utc | A Windows fájl jelölő az időtartomány, amelynek a Chaos jelentést generáló van befejezésének időpontja. Tekintse meg [DateTime.ToFileTimeUtc metódus](https\://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx) részleteiről. |
| --max-results | A lapozható lekérdezés részeként visszaadandó eredmények maximális száma. Ez a paraméter határozza meg, visszaadott eredmények számának felső határnál. Az eredmények vissza is lehet kisebb, mint a megadott maximális eredményeket, ha azok nem férnek el megfelelően az üzenetek maximális mérete korlátozások az üzenetben a konfigurációban meghatározott. Ha ez a paraméter értéke nulla, vagy nincs megadva, a lapozható lekérdezés annyi eredmények, amelyek illeszkednek az visszaadott üzenet a lehető tartalmazza. |
| --start-time-utc | A Windows fájl az időtartományt, amelyhez a Chaos jelentést, hogy jöjjön létre, kezdési idejét jelölő időpontja. Tekintse meg [DateTime.ToFileTimeUtc metódus](https\://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx) részleteiről. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-chaos-get"></a>sfctl-káosz get
A Chaos állapotának lekéréséhez.

A Chaos jelzi-e a Chaos fut, a Chaos paraméterek, a Chaos ütemezés állapota- és Káosztesztek futtatásához használt állapotának lekéréséhez.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-chaos-start"></a>sfctl-káosz indítása
A Chaos elindítja a fürtben.

A Chaos már nem fut a fürtben, ha először a Chaos az átadott a Chaos paraméterek. A Chaos már fut, ha a kérés érkezett, ha a hívás sikertelen lesz, a következő hibakóddal: FABRIC_E_CHAOS_ALREADY_RUNNING.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – alkalmazás-típus-állapotfigyelő-házirend-térkép | JSON kódolású a sérült alkalmazások maximális százalékos aránya a konkrét alkalmazástípusokat list. Mindegyik bejegyzés kulcsként az alkalmazás típusát, és a egy egész számot jelölő értékeli ki a megadott alkalmazástípus az alkalmazásokat, hogy MaxPercentUnhealthyApplications százalékos értékként adja meg. <br><br> Meghatározza egy leképezés sérült alkalmazások maximális százalékos aránya az adott alkalmazás esetében. Minden bejegyzés adja meg a kulcs az alkalmazás típusát és a egy egész számot jelölő értékeli ki a megadott alkalmazástípus az alkalmazásokat, hogy MaxPercentUnhealthyApplications százalékos értékként. Speciális alkalmazástípusokat ismerteti típusát állapotfigyelő szabályzat alkalmazástérkép használható fürt állapotának kiértékelése közben. Az alkalmazás-típust, a térkép tartalmaz a térképen, és nem a globális MaxPercentUnhealthyApplications a fürt állapotának házirendben meghatározott a megadott százalék alapján értékeli ki. Az alkalmazások a leképezésben megadott alkalmazástípusok nem számítanak a globális készlet alkalmazások ellen. Például kritikus fontosságú bizonyos típusú alkalmazások esetén a fürt rendszergazdája is adjon hozzá egy bejegyzést a térképen, hogy az alkalmazás típusához, és rendelje hozzá egy értéket 0 %-os (vagyis nem lekérés hibákat). Minden más alkalmazást a 20 %-os értékre van állítva, az alkalmazáspéldányok a több ezer kívül bizonyos hibák ugyan MaxPercentUnhealthyApplications kiértékelése. Típusát állapotfigyelő szabályzat alkalmazástérkép csak akkor, ha a fürtjegyzék lehetővé teszi, hogy az alkalmazás típusának állapotának kiértékelését a konfigurációs bejegyzés használatával HealthManager/EnableApplicationTypeHealthEvaluation szolgál. |
| --chaos-target-filter | JSON kódolású szótár két karakterlánc típusú kulcsokkal. A két kulcs a NodeTypeInclusionList is és az ApplicationInclusionList is. Mindkét ezeket a kulcsokat értékek listáját. chaos_target_filter határozza meg az összes szűrőt a megcélzott káosz hibák, például csak az egyes csomóponttípusok hibás, vagy csak bizonyos alkalmazások hibás. <br><br> Chaos_target_filter nem használatos, ha a Chaos hibákat az összes fürt entitásokat. Chaos_target_filter használata esetén a Chaos hibákat csak azokat az entitásokat, amelyek megfelelnek a chaos_target_filter specifikációnak. A NodeTypeInclusionList is és az ApplicationInclusionList lehetővé teszik a union szemantikáját. Nem alkalmas a NodeTypeInclusionList is és az ApplicationInclusionList metszetét adja meg. Ha például nincs lehetőség adja meg a "hiba az alkalmazás csak akkor, ha be van kapcsolva a csomópont típusa." Miután egy entitás tartalmazza a NodeTypeInclusionList vagy ApplicationInclusionList, entitás nem zárható ki – egyidejűleg használatával. Akkor is, ha az ApplicationInclusionList applicationX nem jelenik meg, néhány káosz ismétléskor applicationX is hibás történik a kell egy olyan csomópontján, amely megtalálható a NodeTypeInclusionList nodeTypeY, mert. Ha üres a NodeTypeInclusionList is és az ApplicationInclusionList is, egy ArgumentException fordul elő. Minden típusú hibák (csomópont újraindítása, indítsa újra a kódcsomag, távolítsa el a replika, indítsa újra a replikát, helyezze át az elsődleges és másodlagos áthelyezése) a következő csomópont típusú csomópontok engedélyezve vannak. Ha a csomópont típusa (például NodeTypeX) nem jelenik meg a NodeTypeInclusionList majd soha nem lesznek engedélyezve NodeTypeX csomópontjait csomópont szolgáltatói hibák (például NodeRestart), de a kód csomag és a replika hibákat továbbra is engedélyezhető a NodeTypeX, ha az alkalmazás a ApplicationInclusionList történik a NodeTypeX csomópontján találhatók. Legfeljebb 100 csomópont típusnevek felvehetők a listában, ezt a számot növelheti, a konfiguráció frissítése a MaxNumberOfNodeTypesInChaosEntityFilter konfigurációhoz szükség. Ezekhez az alkalmazásokhoz, szolgáltatásokhoz tartozó összes replika replika hibák (újraindítás replika, távolítsa el a replika, áthelyezés elsődleges és áthelyezése másodlagos) káosz által kezelhető. A Chaos előfordulhat, hogy indítsa újra a egy kódcsomaghoz csak akkor, ha a kódcsomag replikák csak ezeket az alkalmazásokat futtatja. Ha egy alkalmazás nem jelenik meg ebben a listában, azt is továbbra is hibás néhány káosz ismétléskor, ha az alkalmazás egy olyan csomópontján, a csomópont típusa, amely megtalálható a NodeTypeInclusionList is említi. Azonban ha applicationX kötődik elhelyezési korlátozások és applicationX nodeTypeY hiányzik az ApplicationInclusionList és nodeTypeY hiányzik a NodeTypeInclusionList, majd applicationX soha nem hibás lesz. Legfeljebb 1000 alkalmazásnevek felvehetők a listában, ezt a számot növelheti, a konfiguráció frissítése a MaxNumberOfApplicationsInChaosEntityFilter konfigurációhoz szükség. |
| – környezet | (String, string) JSON-kódolású térkép írja be a kulcs-érték párokat. A térkép segítségével rögzíti a Chaos Futtatás kapcsolatos információt. Nem lehet több mint 100 ilyen párok, és mindegyik sztring (kulcs vagy érték), legfeljebb 4095 karakter hosszú lehet. Ezen a térképen az alapszintű, a káosz, futtassa a környezetben a meghatározott Futtatás tárolását állítja be. |
| --letiltása – move-replika-hibák | Letiltja az áthelyezési elsődleges és másodlagos hibák áthelyezni. |
| --max-fürt-stabilizálása | A maximális mennyisége, és megvárja, hogy a fürt összes entitást stabillá és kifogástalan állapotú.  Alapértelmezett\: 60. <br><br> Iterációban végrehajtja a káosz, és minden egyes ismétléskor elején fürt entitások állapotát ellenőrzi. Érvényesítése során Ha egy fürt entitás nem stabil és kifogástalan állapotú MaxClusterStabilizationTimeoutInSeconds, belül káosz állít elő egy érvényesítése sikertelen esemény. |
| --max-concurrent-faults | Egyidejű hibák maximális száma száma az iteráció által kiváltott. Iterációban végrehajtja a káosz, és két egymást követő ismétlések egy érvényesítési fázist vannak elválasztva. Minél nagyobb az egyidejűséget, az agresszívabb injektálás hibák – hogy összetettebb sorozatát állapotot, és hibák elemzése révén. A javaslat, hogy kezdje a egy értéke 2 vagy 3 valamint körültekintéssel áthelyezése közben.  Alapértelmezett\: 1. |
| --max-percent-unhealthy-apps | Során káosz fürt állapotának kiértékelésekor a maximálisan engedélyezett sérült alkalmazások százaléka előtt egy hibát jelez. <br><br> A maximálisan engedélyezett sérült alkalmazások százaléka előtt egy hibát jelez. Ahhoz, hogy 10 %-a nem megfelelő állapotú, hogy az alkalmazások, például ez az érték lenne 10. A százalékos eltűrt maximális százalékos aránya, amely előtt a fürt hibás lehet nem megfelelő alkalmazások jelöli. Ha a százalékos tiszteletben tartják, de legalább egy sérült alkalmazás, az egészségügyi figyelmeztetés minősül. Ez kiszámítása a nem megfelelő állapotú alkalmazások száma teljes száma a fürtben, kivéve az alkalmazásokat, amelyek szerepelnek a ApplicationTypeHealthPolicyMap alkalmazástípusok alkalmazáspéldányok keresztül. A számítási kerekít egy sikertelen kérelmek kis számú működését. Alapértelmezett százalékos értéke nulla. |
| --max-percent-unhealthy-nodes | A Chaos során fürt állapotának kiértékelésekor a maximálisan engedélyezett sérült csomópontok aránya reporting a hiba előtt. <br><br> A maximálisan engedélyezett százalékos arányát sérült csomópontok előtt egy hibát jelez. Ahhoz, hogy sérült csomópontok 10 %-át, például ez az érték lenne 10. A százalékos eltűrt maximális százalékos aránya, amely előtt a fürt hibás lehet nem megfelelő állapotú csomópontokat jelöli. Ha a százalékos tiszteletben tartják, de legalább egy nem megfelelő állapotú csomóponti, az egészségügyi figyelmeztetés minősül. Százalékos kiszámítása a nem megfelelő állapotú csomópontok számát a fürtben található csomópontok száma keresztül. Felfelé kerekít a számítási csomópontok kis számú egy hiba elviselni. Alapértelmezett százalékos értéke nulla. Nagy fürtjein található egyes csomópontok minden esetben le- illetve felskálázni a javításához, ezért ezt a százalékos arányt, amely ugyan úgy kell konfigurálni. |
| --futásra idő | Teljes időtartam (másodpercben), amelynek a Chaos automatikusan leállítása előtt fog futni. A maximális megengedett értéke 4 294 967 295 (System.UInt32.MaxValue).  Alapértelmezett\: 4294967295. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |
| --wait-time-között-hibák | Várakozási idő (másodpercben) belül egyetlen iteráció egymást követő hibák között.  Alapértelmezett\: 20. <br><br> Minél nagyobb az értéke, annál kisebb az átfedő hibák és az egyszerűbb közötti állapot sorozatát értékre vált, amely a fürt halad át. A javaslat, hogy kezdje mozgatása 1 és 5 és a gyakorlatban körültekintő közötti értéket. |
| --wait-idő-között – az ismétlések | Idő-elkülönülését (másodpercben) a Chaos két egymást követő ismétlésének. Minél nagyobb az érték, annál alacsonyabbra tartalék injektálási sebessége.  Alapértelmezett\: 30. |
| --warning-as-error | A figyelmeztetési hibaként állapotházirend állítja be. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-chaos-stop"></a>sfctl-káosz stop
Leállítja a káosz, ha a fürt fut, és helyezze a Chaos ütemezés leállított állapotban.

A Chaos leállítja a végrehajtás alatt álló új hibát. Hajtsa végre, amíg azokat el teljes átvitel közben hibák továbbra is. Az aktuális káosz ütemezés egy leállított állapotba kerül. Ütemezés leállítását követően azt fog leállított állapotban marad, és új futtatásának káosz ütemezés káosz nem használhatók. Ütemezés folytatása érdekében állítson be egy új Chaos kimutatást.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="next-steps"></a>További lépések
- [A telepítő](service-fabric-cli.md) a Service Fabric parancssori felület.
- Ismerje meg, hogyan használható a Service Fabric parancssori felület használatával a [-szkript minták](/azure/service-fabric/scripts/sfctl-upgrade-application).