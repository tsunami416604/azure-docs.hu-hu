---
title: Gyakorlati tanácsok az automatikus skálázáshoz
description: Automatikus skálázási minták az Azure-ban webalkalmazásokhoz, virtuálisgép-méretezési készletekhez és felhőszolgáltatásokhoz
ms.topic: conceptual
ms.date: 07/07/2017
ms.subservice: autoscale
ms.openlocfilehash: a05cf87e660cc6c388ea2055bb174c47b99da4a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248917"
---
# <a name="best-practices-for-autoscale"></a>Ajánlott eljárások az automatikus méretezéshez
Az Azure Monitor automatikus skálázása csak [a virtuális gép méretezési készleteire,](https://azure.microsoft.com/services/virtual-machine-scale-sets/) [a felhőszolgáltatásokra,](https://azure.microsoft.com/services/cloud-services/) [az appszolgáltatásra – webalkalmazásokra](https://azure.microsoft.com/services/app-service/web/)és [az API-kezelési szolgáltatásokra](https://docs.microsoft.com/azure/api-management/api-management-key-concepts)vonatkozik.

## <a name="autoscale-concepts"></a>Automatikus skálázási fogalmak

* Egy erőforrásnak csak *egy* automatikus skálázási beállítása lehet
* Egy automatikus skálázási beállítás egy vagy több profillal rendelkezhet, és minden profil rendelkezhet egy vagy több automatikus skálázási szabállyal.
* Az automatikus skálázási beállítás horizontálisan méretezi a példányokat, ami a példányok *növelésével* *és* a példányok számának csökkentésével történik.
  Az automatikus skálázási beállítások maximális, minimális és alapértelmezett példányértékkel rendelkeznek.
* Az automatikus skálázási feladat mindig beolvassa a társított metrikát a méretezéshez, ellenőrzi, hogy átlépte-e a horizontális felskálázás vagy a horizontális felskálázás konfigurált küszöbértékét. Megtekintheti az automatikus skálázás által skálázható metrikák listáját az [Azure Monitor automatikus skálázás közös metrikáiban.](autoscale-common-metrics.md)
* Az összes küszöbértéket a rendszer példányszinten számítja ki. Például a "horizontális felskálázás egy példával, amikor az átlagos CPU > 80%, ha a példányok száma 2", azt jelenti, horizontális felskálázás, ha az átlagos CPU minden példányban nagyobb, mint 80%.
* Az összes automatikus skálázási hiba a tevékenységnaplóba kerül. Ezután konfigurálhat egy [tevékenységnapló-riasztást,](./../../azure-monitor/platform/activity-log-alerts.md) hogy értesítést kapjon e-mailben, SMS-ben vagy webhookokon keresztül, ha automatikus skálázási hiba történik.
* Hasonlóképpen az összes sikeres méretezési művelet et felkönyveli a tevékenységnaplóba. Ezután konfigurálhat egy tevékenységnapló-riasztást, hogy e-mailben, SMS-ben vagy webhookon keresztül értesítést kapjon, ha sikeres automatikus skálázási művelet történik. Beállíthatja, hogy az e-mail vagy a webhook-értesítések értesítést kapjanak a sikeres méretezési műveletekről az automatikus skálázási beállítás értesítései lapján.

## <a name="autoscale-best-practices"></a>Gyakorlati tanácsok az automatikus skálázáshoz

Az automatikus skálázás használata során az alábbi ajánlott eljárásokat használhatja.

### <a name="ensure-the-maximum-and-minimum-values-are-different-and-have-an-adequate-margin-between-them"></a>Győződjön meg arról, hogy a maximális és minimális értékek eltérőek, és a közöttük lévő különbség elegendő

Ha olyan beállítással rendelkezik, amely minimum=2, maximum=2, és az aktuális példányszám 2, nem fordulhat elő méretezési művelet. Tartson megfelelő mozgástéra a maximális és a minimális példányszám között, amely tartalmazza a példányokat. Az automatikus skálázás mindig a korlátok között skálázódik.

### <a name="manual-scaling-is-reset-by-autoscale-min-and-max"></a>A kézi skálázás visszaállítása az automatikus skálázás minimuma és maximuma alapján történik

Ha manuálisan frissíti a példányok számát a maximum felett vagy alatt lévő értékre, az automatikus skálázási motor automatikusan visszakerül a minimumra (ha alatt) vagy a maximumra (ha fent). Például 3 és 6 között állítja be a tartományt. Ha egy futó példány, az automatikus skálázási motor skálázáshárom példányban a következő futtatáskor. Hasonlóképpen, ha manuálisan állítja be a skálát nyolc példányra, a következő automatikus skálázás a következő futtatáskor hat példányra méretezi vissza.  A manuális skálázás ideiglenes, kivéve, ha alaphelyzetbe állítja az automatikus skálázási szabályokat is.

### <a name="always-use-a-scale-out-and-scale-in-rule-combination-that-performs-an-increase-and-decrease"></a>Mindig használjon horizontális felskálázási és horizontális felskálázási szabálykombinációt, amely növeli és csökkenti a
Ha a kombinációnak csak egy részét használja, az automatikus skálázás csak egy irányban (horizontális felskálázás vagy be) műveleteket fog végrehajtani, amíg el nem éri a profilban meghatározott maximális vagy minimális példányszámot. Ez nem optimális, ideális esetben azt szeretné, hogy az erőforrás nagy használat esetén a rendelkezésre állás biztosítása érdekében. Hasonlóképpen, az alacsony használat idején azt szeretné, hogy az erőforrás leskálázható legyen, így költségmegtakarítást érhet el.

### <a name="choose-the-appropriate-statistic-for-your-diagnostics-metric"></a>Válassza a megfelelő statisztikát a diagnosztikametrikához
A diagnosztikai metrikák, választhat *az Átlag,* *Minimum*, *Maximum* és *Összesen,* mint a metrika skálázási. A leggyakoribb statisztika az *Átlag*.

### <a name="choose-the-thresholds-carefully-for-all-metric-types"></a>A küszöbértékeket körültekintően válassza ki az összes metrikatípushoz
Javasoljuk, hogy gondosan válasszon különböző küszöbértékeket a horizontális felskálázáshoz és a horizontális felskálázáshoz a gyakorlati helyzetek alapján.

*Nem javasoljuk* az automatikus skálázási beállításokat, mint például az alábbi példákat az azonos vagy hasonló küszöbértékekkel a kimenő és a feltételek:

* Példányok számának növelése 1-vel, ha a szálak száma >= 600
* Példányok számának csökkentése 1-vel, ha a szálak száma <= 600

Nézzünk egy példát arra, hogy mi vezethet egy zavarónak tűnő viselkedéshez. Vegye figyelembe a következő sorrendet.

1. Tegyük fel, hogy két példánykezdődik, majd az átlagos szálak száma példányonként nő 625.
2. Az automatikus skálázás egy harmadik példány hozzáadásával egészül ki.
3. Ezután tegyük fel, hogy az átlagos szálszám példány on-át 575.Next, assume that the average thread count across instance falls to 575.
4. A leskálázás előtt az automatikus skálázás megpróbálja megbecsülni, hogy mi lesz a végső állapot, ha az méretezés. Például 575 x 3 (aktuális példányszám) = 1725 / 2 (a példányok végleges száma lefelé skálázva) = 862,5 szál. Ez azt jelenti, hogy az automatikus skálázásnak azonnal újra ki kell bővítenie, még akkor is, ha az átlagos szálszám ugyanaz marad, vagy akár csak kis mértékben esik. Azonban, ha újra felskálázódik, az egész folyamat megismétlődik, ami végtelen ciklushoz vezet.
5. Ennek a helyzetnek az elkerülése érdekében (az úgynevezett "csapkodás"), az automatikus skálázás egyáltalán nem csökken. Ehelyett kihagyja, és újraértékeli a feltételt a következő alkalommal, amikor a szolgáltatás végrehajtása. A csapkodó állapot sok embert összezavarhat, mert az automatikus skálázás nem működik, amikor az átlagos szálszám 575 volt.

A horizontális felskálázás során végzett becslés célja, hogy elkerülje a "csapkodási" helyzeteket, ahol a horizontális felskálázási és horizontális felskálázási műveletek folyamatosan oda-vissza haladnak. Tartsa szem előtt ezt a viselkedést, ha ugyanazokat a küszöbértékeket választja a horizontális felskálázáshoz és a be

Javasoljuk, hogy válasszon megfelelő árrést a horizontális felskálázás és a küszöbértékek között. Vegyük például a következő jobb szabálykombinációt.

* A példányok számának növelése 1-vel, ha a CPU%>= 80
* A példányok számának csökkentése 1-vel, ha a CPU%-a <= 60

Ebben az esetben  

1. Tegyük fel, hogy két példánynal kell kezdeni.
2. Ha az átlagos CPU-% példányok között megy 80, automatikus skálázás idvanáta egy harmadik példány hozzáadása.
3. Most tegyük fel, hogy idővel a CPU% esik 60.
4. Az automatikus skálázási horizontális felskálázási szabály megbecsüli a végső állapotot, ha az a skálázás. Például 60 x 3 (aktuális példányszám) = 180 / 2 (a példányok végleges száma lefelé skálázva) = 90. Így az automatikus skálázás nem skálázható, mert azonnal újra kell horizontális felskáláznia. Ehelyett kihagyja a leskálázást.
5. A következő alkalommal, amikor az automatikus skálázás ellenőrzi, a CPU továbbra is esik 50. Becslése újra - 50 x 3 példány = 150 / 2 példány = 75, amely nem éri el a 80 horizontális felskálázási küszöbértéket, így sikeresen 2 példányra skálázódik.

### <a name="considerations-for-scaling-threshold-values-for-special-metrics"></a>A speciális metrikák küszöbértékeinek skálázásakor megfontolandó szempontok
 Speciális metrikák, például a storage vagy a Service Bus-várólista hossza metrika esetében a küszöbérték a példányok aktuális száma szerint elérhető üzenetek átlagos száma. Gondosan válassza ki a metrika küszöbértékét.

Mutassuk be egy példával, hogy jobban megértsd a viselkedést.

* Példányok számának növelése 1-vel, ha a storage-üzenetüzenetek száma >= 50
* Példányok csökkentése 1-vel, ha a storage-üzenetüzenetek száma <= 10

Vegye figyelembe a következő sorrendet:

1. Két tárolóvárólista-példány létezik.
2. Üzenetek folyamatosan jönnek, és amikor áttekinti a tárolási várólistát, a teljes szám 50 olvasása. Feltételezheti, hogy az automatikus skálázás nak horizontális felskálázási műveletet kell indítania. Azonban vegye figyelembe, hogy még mindig 50/2 = 25 üzenet példányonként. Tehát a horizontális felskálázás nem fordul elő. Az első horizontális felskálázás, hogy megtörténjen, a teljes üzenetszám a tárolási várólistában kell 100.
3. Ezután tegyük fel, hogy a teljes üzenetszám eléri a 100-at.
4. Egy harmadik tárolási várólista-példány egy horizontális felskálázási művelet miatt kerül hozzáadásra.  A következő horizontális felskálázási művelet nem történik meg, amíg a várólistában lévő üzenetek teljes száma el nem éri a 150-et, mert 150/3 = 50.
5. Most a várólistában lévő üzenetek száma csökken. Három példány esetén az első horizontális felskálázási művelet akkor történik, amikor az összes várólistában lévő összes üzenet legfeljebb 30-at ad ki, mert példányonként 30/3 = 10 üzenet, amely a horizontális felskálázási küszöbérték.

### <a name="considerations-for-scaling-when-multiple-profiles-are-configured-in-an-autoscale-setting"></a>Megfontolandó szempontok, ha az automatikus skálázáshoz több profil van konfigurálva
Automatikus skálázási beállításokesetén választhat egy alapértelmezett profilt, amely mindig az ütemezéstől vagy az időponttól való függőség nélkül lesz alkalmazva, vagy választhat egy ismétlődő profilt vagy egy dátum- és időtartományt adó rögzített időszakra vonatkozó profilt.

Amikor az automatikus skálázási szolgáltatás feldolgozza őket, mindig a következő sorrendben ellenőrzi:

1. Rögzített dátum profil
2. Ismétlődő profil
3. Alapértelmezett ("Mindig") profil

Ha egy profilfeltétel teljesül, az automatikus skálázás nem ellenőrzi az alatta lévő következő profilfeltételt. Az automatikus skálázás egyszerre csak egy profilt dolgoz fel. Ez azt jelenti, hogy ha alacsonyabb szintű profilból szeretne feldolgozási feltételt is felvenni, akkor ezeket a szabályokat is bele kell foglalnia az aktuális profilba.

Tekintsük át egy példával:

Az alábbi képen egy automatikus skálázási beállítás látható, amelynek alapértelmezett profilja minimális példányok = 2 és maximális példányok = 10. Ebben a példában a szabályok úgy vannak beállítva, hogy horizontális felskálázás, ha az üzenetek száma a várólistában nagyobb, mint 10 és a horizontális felskálázás, ha az üzenetek száma a várólistában kevesebb, mint három. Így most az erőforrás skálázása kettő és tíz példány között.

Ezen kívül van egy ismétlődő profil készlet hétfőre. Úgy van beállítva, minimális példányok = 3 és maximális példányok = 10. Ez azt jelenti, hogy hétfőn az első automatikus skálázás ellenőrzi ezt a feltételt, ha a példányszáma kettő, akkor az új minimum három. Mindaddig, amíg az automatikus skálázás továbbra is megtalálja ezt a profilfeltételt egyeztetett (hétfő), csak a profilhoz konfigurált CPU-alapú horizontális felskálázási szabályokat dolgozza fel. Jelenleg nem ellenőrzi a várólista hosszát. Ha azonban azt is szeretné, hogy a várólista hosszának feltétele legyen ellenőrizve, akkor ezeket a szabályokat az alapértelmezett profilból is bele kell foglalnia a hétfői profilba.

Hasonlóképpen, amikor az automatikus skálázás visszavált az alapértelmezett profilra, először ellenőrzi, hogy a minimális és maximális feltételek teljesülnek-e. Ha a példányok száma abban az időben 12, akkor a skálázás 10, a maximálisan engedélyezett alapértelmezett profil.

![automatikus méretezési beállítások](./media/autoscale-best-practices/insights-autoscale-best-practices-2.png)

### <a name="considerations-for-scaling-when-multiple-rules-are-configured-in-a-profile"></a>Megfontolandó szempontok, ha egy profilon belül több szabály van konfigurálva

Vannak esetek, amikor előfordulhat, hogy több szabályt kell beállítania egy profilban. A következő automatikus skálázási szabályokat az automatikus skálázási motor több szabály beállításakor használja.

*Horizontális felskálázáskor*az automatikus skálázás akkor fut, ha bármely szabály teljesül.
*A horizontális felskálázáskor az*automatikus skálázás megköveteli, hogy minden szabályt be kell tartani.

Szemléltetésképpen tegyük fel, hogy a következő négy automatikus skálázási szabékkal rendelkezik:

* Ha a CPU < 30%, a méretezés 1-re
* Ha a memória < 50%, 1-es
* Ha a CPU > 75%, 1-es
* Ha a memória > 75%, a horizontális felskálázás 1

Ezután következik be a következő:

* Ha a CPU 76%, a memória pedig 50%, akkor horizontálisfelskálázjuk.
* Ha a CPU 50%, a memória pedig 76%, akkor horizontálisfelskálázjuk.

Másrészt, ha a CPU 25%, és a memória 51%, automatikus skálázás **nem** scale-in. A méretezéshez a CPU-nak 29%-nak, a memória49%-nak kell lennie.

### <a name="always-select-a-safe-default-instance-count"></a>Mindig válasszon biztonságos alapértelmezett példányszámot
Az alapértelmezett példányszám azért fontos, mert az automatikus skálázás a szolgáltatás az adott számra méretezi, ha a metrikák nem érhetők el. Ezért válassza ki az alapértelmezett példányszám, amely biztonságos a számítási feladatokhoz.

### <a name="configure-autoscale-notifications"></a>Automatikus skálázással kapcsolatos értesítések konfigurálása
Az automatikus skálázás az alábbi feltételek bármelyike esetén kerül a tevékenységnaplóba:

* Az automatikus skálázás méretezési műveletet ad ki.
* Az automatikus skálázási szolgáltatás sikeresen végrehajt egy méretezési műveletet.
* Az automatikus skálázási szolgáltatás nem képes léptékműveletet végrehajtani.
* Metrikák nem érhetők el az automatikus skálázási szolgáltatás méretezési döntést hoz.
* Metrikák érhetők el (helyreállítási) ismét, hogy egy skálázási döntést.

A tevékenységnapló-riasztást is használhatja az automatikus skálázási motor állapotának figyelésére. Íme néhány példa [egy tevékenységnapló-riasztás létrehozására az előfizetés összes automatikus skálázási motorjának működésének figyelésére,](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert) vagy [egy tevékenységnapló-riasztás létrehozására az előfizetés összes sikertelen automatikus skálázási skálázási skálázási/horizontális felskálázási műveletének figyelésére.](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

A tevékenységnapló-riasztások használata mellett e-mail- vagy webhook-értesítéseket is konfigurálhat, hogy értesítést kapjon a sikeres méretezési műveletekről az automatikus skálázási beállítás értesítések lapján keresztül.

## <a name="next-steps"></a>Következő lépések
- [Hozzon létre egy tevékenységnapló-riasztást az előfizetés összes automatikus skálázási motor-műveletének figyeléséhez.](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Tevékenységnapló-riasztás létrehozása az előfizetés összes sikertelen automatikus skálázási skálázási/horizontális felskálázási műveletének figyeléséhez](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

