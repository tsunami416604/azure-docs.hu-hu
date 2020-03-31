---
title: Magas rendelkezésre állású alkalmazások tervezése georedundáns tárolással
titleSuffix: Azure Storage
description: Ismerje meg, hogyan használhatja az olvasási hozzáférésű georedundáns tárolást egy magas rendelkezésre állású alkalmazás megtervezéséhez, amely elég rugalmas a kimaradások kezeléséhez.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 592be1710893791e80dfe4b20e1323e789b33e69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77157092"
---
# <a name="designing-highly-available-applications-using-read-access-geo-redundant-storage"></a>Magas rendelkezésre állású alkalmazások tervezése olvasási hozzáférésű georedundáns tárolással

A felhőalapú infrastruktúrák, például az Azure Storage közös jellemzője, hogy magas rendelkezésre állású platformot biztosítanak az alkalmazások üzemeltetéséhez. A felhőalapú alkalmazások fejlesztőinek alaposan meg kell fontolniuk, hogyan használhatják ki ezt a platformot, hogy magas rendelkezésre állású alkalmazásokat juttassanak el a felhasználókhoz. Ez a cikk arra összpontosít, hogy a fejlesztők hogyan használhatják az Azure georedundáns replikációs beállításait annak biztosítására, hogy az Azure Storage-alkalmazásaik magas rendelkezésre állásúak legyenek.

A georedundáns replikációra konfigurált tárfiókok szinkron replikálásra kerülnek az elsődleges régióban, majd aszinkron módon replikálódnak egy több száz mérföldre lévő másodlagos régióba. Az Azure Storage kétféle georedundáns replikációt kínál:

* [A földrajzi zónaredundáns tárolás (GZRS) (előzetes verzió)](storage-redundancy.md) replikációt biztosít a magas rendelkezésre állást és maximális tartósságot igénylő forgatókönyvekhez. Az adatok szinkron módon replikálódik három Azure rendelkezésre állási zónában az elsődleges régióban zónaredundáns tárolás (ZRS) használatával, majd aszinkron módon replikálódik a másodlagos régióban. A másodlagos régióban lévő adatokolvasási hozzáférés engedélyezéséhez engedélyezze az olvasási hozzáférésű geozónaredundáns tárolást (RA-GZRS).
* [A georedundáns tárolás (GRS)](storage-redundancy.md) régióközi replikációt biztosít a regionális kimaradások elleni védelem érdekében. Az adatok szinkron módon háromszor replikálódnak az elsődleges régióban a helyileg redundáns tárolás (LRS) használatával, majd aszinkron módon replikálják a másodlagos régióba. A másodlagos régióban lévő adatokolvasási hozzáférés engedélyezéséhez engedélyezze az olvasási hozzáférésű georedundáns tárolást (RA-GRS).

Ez a cikk bemutatja, hogyan tervezheti meg az alkalmazást az elsődleges régióban egy kimaradás kezelésére. Ha az elsődleges régió elérhetetlenné válik, az alkalmazás alkalmazkodhat olvasási műveletek et a másodlagos régió helyett. A kezdés előtt győződjön meg arról, hogy tárfiókja ra-GRS vagy RA-GZRS-hez van konfigurálva.

Arról, hogy mely elsődleges régiók vannak párosítva a másodlagos régiókkal, olvassa el [az Üzletmenet folytonossága és a vész-helyreállítási (BCDR): Azure Paired Regions című témakört.](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)

A cikk kódrészleteket tartalmaz, és a végén egy teljes mintára mutató hivatkozást, amelyet letölthet és futtathat.

## <a name="application-design-considerations-when-reading-from-the-secondary"></a>Alkalmazástervezési szempontok a másodlagos

A cikk célja, hogy megmutassa, hogyan tervezheti meg az olyan alkalmazást, amely továbbra is működni fog (bár korlátozott kapacitással), még az elsődleges adatközpontban bekövetkező súlyos katasztrófa esetén is. Az alkalmazást úgy tervezheti meg, hogy az átmeneti vagy hosszú ideig futó problémákat úgy kezelje, hogy a másodlagos régióból olvassa, ha olyan probléma merül fel, amely zavarja az elsődleges régióból történő olvasást. Ha az elsődleges régió ismét elérhető, az alkalmazás visszatérhet az olvasáshoz az elsődleges régióból.

Ne feledje, hogy az RA-GRS vagy RA-GZRS alkalmazás tervezésekor ezek a kulcsfontosságú pontok:

* Az Azure Storage egy másodlagos régióban az elsődleges régióban tárolt adatok csak olvasható másolatát tárolja. Afent említettek szerint a tárolási szolgáltatás határozza meg a másodlagos régió helyét.

* Az írásvédett másolat [végül konzisztens](https://en.wikipedia.org/wiki/Eventual_consistency) az elsődleges régió ban lévő adatokkal.

* Blobok, táblák és várólisták esetén lekérdezheti a másodlagos régióban az *utolsó szinkronizálási idő* értékét, amely jelzi, hogy mikor történt az utolsó replikáció az elsődleges és a másodlagos régió között. (Ez nem támogatott az Azure Files, amely jelenleg nem rendelkezik RA-GRS redundanciával.)

* A storage-ügyfélkódtár segítségével adatokat olvashat és írhat az elsődleges vagy másodlagos régióban. Az olvasási kérelmeket automatikusan átis irányíthatja a másodlagos régióba, ha az elsődleges régióba irányuló olvasási kérelem idővel kimenő.

* Ha az elsődleges régió elérhetetlenné válik, kezdeményezhet egy fiók feladatátvételt. Amikor a másodlagos régióba ad át feladatátvételt, az elsődleges régióra mutató DNS-bejegyzések a másodlagos régióra változnak. A feladatátvétel befejezése után az írási hozzáférés helyreáll a GRS- és RA-GRS-fiókokhoz. További információ: [Vész-helyreállítási és tárfiók feladatátvétel (előzetes verzió) az Azure Storage-ban.](storage-disaster-recovery-guidance.md)

> [!NOTE]
> Az ügyfél által kezelt fiók feladatátvétele (előzetes verzió) még nem érhető el a GZRS/RA-GZRS-t támogató régiókban, így az ügyfelek jelenleg nem kezelhetik a gzrs- és RA-GZRS-fiókokkal rendelkező fiókfeladat-átvételi eseményeket. Az előzetes verzió során a Microsoft kezeli a GZRS/RA-GZRS-fiókokat érintő feladatátvételi eseményeket.

### <a name="using-eventually-consistent-data"></a>Végül konzisztens adatok használata

A javasolt megoldás feltételezi, hogy elfogadható a potenciálisan elavult adatok visszaküldése a hívó alkalmazásnak. Mivel a másodlagos régióban lévő adatok végül konzisztensek, lehetséges, hogy az elsődleges régió elérhetetlenné válik, mielőtt a másodlagos régió frissítése befejeződött.

Tegyük fel például, hogy az ügyfél sikeresen küld egy frissítést, de az elsődleges régió sikertelen lesz, mielőtt a frissítés a másodlagos régióba kerül. Amikor az ügyfél kéri, hogy olvassa vissza az adatokat, a frissített adatok helyett a másodlagos régióból kapnak az elavult adatokat. Az alkalmazás tervezésekor el kell döntenie, hogy ez elfogadható-e, és ha igen, hogyan fog üzenetet küldeni az ügyfélnek. 

A cikk későbbi részében bemutatjuk, hogyan ellenőrizheti a másodlagos adatok utolsó szinkronizálási idejét, hogy ellenőrizze, hogy a másodlagos naprakész-e.

### <a name="handling-services-separately-or-all-together"></a>Szolgáltatások külön-külön vagy együttes kezelése

Bár valószínűtlen, lehetséges, hogy egy szolgáltatás elérhetetlenné válik, míg a többi szolgáltatás továbbra is teljesen működőképes. Az újrapróbálkozások és az írásvédett mód az egyes szolgáltatások külön-külön (blobok, várólisták, táblák), vagy általánosan kezelni újrapróbálkozások az összes tárolási szolgáltatások együtt.

Ha például várólistákat és blobokat használ az alkalmazásban, dönthet úgy, hogy külön kódot helyez el az újrapróbálható hibák kezelésére ezek mindegyikéhez. Ezután ha újrapróbálkozik a blob szolgáltatás, de a várólista-szolgáltatás továbbra is működik, csak az alkalmazás, amely kezeli a blobok hatással lesz. Ha úgy dönt, hogy általánosan kezeli az összes tárolási szolgáltatás újrapróbálkozását, és a blobszolgáltatás hívása újrapróbálható hibát ad vissza, akkor a blobszolgáltatás és a várólista-szolgáltatás kéréseit is érinti.

Végső soron ez az alkalmazás összetettségétől függ. Dönthet úgy, hogy nem kezeli a hibákat a szolgáltatás, hanem átirányítja az olvasási kérelmeket az összes tárolási szolgáltatások a másodlagos régióba, és futtassa az alkalmazást csak olvasható módban, ha hibát észlel az elsődleges régióbármely tárolási szolgáltatás.

### <a name="other-considerations"></a>Egyéb szempontok

Ezek a többi szempont fogjuk megvitatni a többi ezt a cikket.

* Olvasási kérelmek újrapróbálkozásainak kezelése az Áramkör-megszakító mintával

* Végül konzisztens adatok és az utolsó szinkronizálás időpontja

* Tesztelés

## <a name="running-your-application-in-read-only-mode"></a>Az alkalmazás futtatása írásvédett módban

Az elsődleges régióban a kimaradás hatékony előkészítéséhez képesnek kell lennie a sikertelen olvasási kérelmek és a sikertelen frissítési kérelmek kezelésére (ebben az esetben a frissítés, azaz beszúrások, frissítések és törlések). Ha az elsődleges régió meghibásodik, az olvasási kérelmek átirányíthatók a másodlagos régióba. A frissítési kérelmek azonban nem irányíthatók át a másodlagosra, mert a másodlagos írásvédett. Ezért meg kell terveznie az alkalmazást, hogy csak olvasható módban fusson.

Például beállíthatja a jelzőt, amely ellenőrzi, mielőtt bármilyen frissítési kérelmek et az Azure Storage-ba. Amikor az egyik frissítési kérelem megérkezik, kihagyhatja azt, és visszaküldheti a megfelelő választ az ügyfélnek. Előfordulhat, hogy bizonyos szolgáltatásokat teljesen le szeretne tiltani, amíg a probléma meg nem oldódik, és értesíti a felhasználókat, hogy ezek a szolgáltatások átmenetileg nem érhetők el.

Ha úgy dönt, hogy az egyes szolgáltatások hibáit külön-külön kezeli, akkor az alkalmazás szolgáltatásonkénti csak olvasható módban való futtatásának lehetőségét is kezelnie kell. Előfordulhat például, hogy minden engedélyezhető és letiltható szolgáltatáshoz írásvédett jelzők vannak. Ezután a kód megfelelő helyein kezelheti a zászlót.

Az alkalmazás írásvédett módban való futtatásának másik oldala is előnyös – lehetővé teszi, hogy egy nagyobb alkalmazásfrissítés során korlátozott funkcionalitást biztosítson. Az alkalmazás csak olvasható módban futtatható, és a másodlagos adatközpontra mutathat, így a frissítések készítése közben senki sem férhet hozzá az adatokhoz az elsődleges régióban.

## <a name="handling-updates-when-running-in-read-only-mode"></a>Frissítések kezelése írásvédett módban történő futtatáskor

Írásvédett módban számos módon kezelhetők a frissítési kérelmek. Nem fogjuk átfogóan lefedni, de általában van néhány minta, amit figyelembe veszel.

1. Válaszolhat a felhasználónak, és megmondhatja nekik, hogy jelenleg nem fogadja el a frissítéseket. Egy kapcsolatkezelő rendszer például lehetővé teheti az ügyfelek számára a kapcsolattartási adatok elérését, de nem csökkentheti a frissítéseket.

2. A frissítések et egy másik régióban is várólistára viheti. Ebben az esetben a függőben lévő frissítési kérelmeket egy másik régióban lévő várólistába kell írnia, majd az elsődleges adatközpont újra online állapotba kerülése után feldolgozhatja ezeket a kérelmeket. Ebben az esetben tudatnia kell az ügyféllel, hogy a kért frissítés várólistára kerül későbbi feldolgozásra.

3. A frissítéseket egy másik régióban lévő tárfiókba írhatja. Ezután, amikor az elsődleges adatközpont újra online állapotba kerül, az adatok szerkezetétől függően egyesítheti ezeket a frissítéseket az elsődleges adatokba. Ha például külön fájlokat hoz létre dátum-/időbélyegzővel a névben, ezeket a fájlokat visszamásolhatja az elsődleges régióba. Ez bizonyos számítási feladatok, például a naplózás és az iOT-adatok esetében működik.

## <a name="handling-retries"></a>Újrapróbálkozások kezelése

Az Azure Storage-ügyfélkódtár segítségével meghatározhatja, hogy mely hibák kísérelhetők meg újra. Például egy 404-es hiba (az erőforrás nem található) nem történik meg újra, mert az újbóli próbálkozás valószínűleg nem eredményez sikeres eredményt. Másrészt az 500-as hiba újrapróbálkozhat, mert kiszolgálóhiba, és a probléma egyszerűen átmeneti probléma lehet. További részletekért tekintse meg az [ExponentialRetry osztály nyílt forráskódját](https://github.com/Azure/azure-storage-net/blob/87b84b3d5ee884c7adc10e494e2c7060956515d0/Lib/Common/RetryPolicies/ExponentialRetry.cs) a .NET storage ügyféltárban. (Keresse meg a ShouldRetry metódust.)

### <a name="read-requests"></a>Olvasási kérelmek

Az olvasási kérelmek átirányíthatók a másodlagos tárolóba, ha probléma van az elsődleges tárhellyel. Amint azt a fentiekben [a használata végül konzisztens adatok](#using-eventually-consistent-data)használata, elfogadhatónak kell lennie az alkalmazás potenciálisan elavult adatok olvasása. Ha a tárolóügyfél-kódtárat használja a másodlagos adatok eléréséhez, megadhatja az olvasási kérelem újrapróbálkozási viselkedését, ha a **LocationMode** tulajdonság értékét az alábbiak egyikére állítja:

* **PrimaryOnly** (az alapértelmezett)

* **PrimaryThenMásodlagos**

* **MásodlagosCsak**

* **Másodlagos ThenPrimary**

Ha a **LocationMode-ot** **PrimaryThenSecondary**-ra állítja, ha az elsődleges végpontra irányuló kezdeti olvasási kérelem sikertelen, újrapróbálkozható hibával, az ügyfél automatikusan újabb olvasási kérelmet küld a másodlagos végpontnak. Ha a hiba kiszolgálói időtúljárat, akkor az ügyfélnek meg kell várnia az időtúltöltés lejáratát, mielőtt újrapróbálkozási hibát kapna a szolgáltatástól.

Alapvetően két forgatókönyvet kell figyelembe venni, amikor eldönti, hogyan reagáljon egy újrapróbálható hibára:

* Ez egy elszigetelt probléma, és az elsődleges végpontra irányuló további kérelmek nem adnak vissza újrapróbálható hibát. Egy példa arra, hogy ez hol fordulhat elő, ha átmeneti hálózati hiba lép fel.

    Ebben a forgatókönyvben nincs jelentős teljesítménybüntetés a **LocationMode** **PrimaryThenSecondary** beállítással, mivel ez csak ritkán fordul elő.

* Ez a probléma az elsődleges régióban lévő legalább egy tárolási szolgáltatással van, és az elsődleges régióban a szolgáltatásminden későbbi kérésvalószínűleg egy ideig újrapróbálható hibákat ad vissza. Erre példa, ha az elsődleges régió teljesen elérhetetlen.

    Ebben a forgatókönyvben van egy teljesítmény büntetés, mert az összes olvasási kérelmek megpróbálja az elsődleges végpont ot, várja meg, amíg az időtúltöltés lejár, majd váltson a másodlagos végpontra.

Ezekben az esetekben azonosítania kell, hogy folyamatban van-e az elsődleges végpontdal kapcsolatos probléma, és az összes olvasási kérelmet közvetlenül a másodlagos végpontnak küldje el a **LocationMode** tulajdonság **SecondaryOnly**beállításával. Ekkor is módosítania kell az alkalmazást, hogy csak olvasható módban fusson. Ezt a megközelítést [megszakítómintának nevezik.](/azure/architecture/patterns/circuit-breaker)

### <a name="update-requests"></a>Kérelmek frissítése

Az áramkör-megszakító minta a frissítési kérelmekre is alkalmazható. A frissítési kérelmek azonban nem irányíthatók át másodlagos tárolóba, amely írásvédett. Ezeknél a kérelmeknél a **LocationMode** tulajdonságot **PrimaryOnly** értékre (alapértelmezett) kell hagyni. A hibák kezeléséhez metrikát alkalmazhat ezekre a kérelmekre – például 10 hibát egy sorban –, és ha a küszöbérték teljesül, kapcsolja be az alkalmazást írásvédett módba. Ugyanazokat a módszereket használhatja a frissítési módba való visszatéréshez, mint amelyeket az áramkör-megszakító mintáról szóló következő szakaszban ismertetünk.

## <a name="circuit-breaker-pattern"></a>Áramkör-megszakító minta

Az áramkör-megszakító minta használata az alkalmazásban megakadályozhatja, hogy újra megkíséreljen egy olyan műveletet, amely valószínűleg ismételten sikertelen lesz. Lehetővé teszi, hogy az alkalmazás továbbra is fusson, és ne vegye fel az időt, amíg a művelet et exponenciálisan próbálja meg. Azt is észleli, ha a hiba ki lett javítva, amikor az alkalmazás újra próbálkozhat a művelettel.

### <a name="how-to-implement-the-circuit-breaker-pattern"></a>Az áramkör-megszakító minta megvalósítása

Annak megállapításához, hogy egy elsődleges végpontdal kapcsolatban folyamatos probléma van-e, figyelheti, hogy az ügyfél milyen gyakran találkozik újrapróbálkozási hibákkal. Mivel minden eset más, el kell döntenie, hogy a kívánt küszöbértéket a döntést, hogy a másodlagos végpontra váltson, és futtassa az alkalmazást csak olvasható módban. Dönthet például úgy, hogy végrehajtja a kapcsolót, ha 10 hiba van egymás után sikertelenül. Egy másik példa a váltás, ha a kérelmek 90%-a 2 perces időszakban sikertelen.

Az első forgatókönyv, egyszerűen tartsa meg a hibák száma, és ha van egy sikeres elérése előtt a maximális, állítsa be a count vissza nullára. A második esetben a megvalósítás egyik módja a MemoryCache objektum használata (a .NET-ben). Minden kérelemhez adjon hozzá egy CacheItem elemet a gyorsítótárhoz, állítsa az értéket sikeresre (1) vagy sikertelen (0), és állítsa a lejárati időt 2 percre (vagy bármi legyen is az időmegkötés). A bejegyzés lejárati idejének elérésekor a program automatikusan eltávolítja a bejegyzést. Ez ad egy gördülő 2 perces ablak. Minden alkalommal, amikor kérelmet küld a tárolási szolgáltatásnak, először egy Linq lekérdezést használ a MemoryCache objektumon keresztül az értékek összegzésével és a számmal való elosztjuk a sikeres százalékos értéket. Ha a százalékos siker bizonyos küszöbérték (például 10%) alá csökken, állítsa a **LocationMode** tulajdonságot az olvasási kérelmekhez **Másodlagoscsak** értékre, és a folytatás előtt váltsa át az alkalmazást írásvédett módba.

A kapcsoló beállításának meghatározásához használt hibaküszöb szolgáltatásról szolgáltatásra változhat az alkalmazásban, ezért érdemes lehet konfigurálható paraméterekké tenni őket. Ez az a pont, ahol úgy dönt, hogy az egyes szolgáltatások újrapróbálható hibáit külön-külön vagy egyként kezeli, ahogy azt korábban tárgyaltuk.

Egy másik szempont, hogyan kell kezelni egy alkalmazás több példányát, és mi a teendő, ha újrapróbálkozási hibákat észlel az egyes példányokban. Előfordulhat például, hogy 20 virtuális gép fut ugyanazzal az alkalmazással. Minden esetet külön kezel? Ha egy példány problémákat okoz, csak erre az egy példányra szeretné korlátozni a választ, vagy meg szeretné próbálni, hogy az összes példány ugyanúgy reagáljon, ha egy példánynak problémája van? A példányok külön-külön történő kezelése sokkal egyszerűbb, mint a válasz koordinálása közöttük, de ennek módja az alkalmazás architektúrájának függ.

### <a name="options-for-monitoring-the-error-frequency"></a>A hibagyakoriság figyelésének lehetőségei

Három fő lehetőség van az újrapróbálkozások gyakoriságának figyelésére az elsődleges régióban annak meghatározásához, hogy mikor váltson át a másodlagos régióra, és módosítsa az alkalmazást, hogy csak olvasható módban fusson.

* Adjon hozzá egy kezelőt az [**Újrapróbálkozás**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.operationcontext.retrying) iajzathoz a tárolási kérelmeknek átadott [**OperationContext**](https://docs.microsoft.com/java/api/com.microsoft.applicationinsights.extensibility.context.operationcontext) objektumhoz – ez a módszer jelenik meg ebben a cikkben, és a kísérő mintában használatos. Ezek az események akkor jönnek végbe, amikor az ügyfél újrapróbálkozik egy kéréssel, így nyomon követheti, hogy az ügyfél milyen gyakran találkozik újrapróbálható hibákkal az elsődleges végponton.

    ```csharp
    operationContext.Retrying += (sender, arguments) =>
    {
        // Retrying in the primary region
        if (arguments.Request.Host == primaryhostname)
            ...
    };
    ```

* Az egyéni újrapróbálkozási házirend [**Kiértékelése**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.iextendedretrypolicy.evaluate) metódusában egyéni kódot futtathat, amikor újrapróbálkozás történik. Az újrapróbálkozási folyamat felvételén kívül ez lehetőséget ad az újrapróbálkozási viselkedés módosítására is.

    ```csharp 
    public RetryInfo Evaluate(RetryContext retryContext,
    OperationContext operationContext)
    {
        var statusCode = retryContext.LastRequestResult.HttpStatusCode;
        if (retryContext.CurrentRetryCount >= this.maximumAttempts
            || ((statusCode >= 300 && statusCode < 500 && statusCode != 408)
            || statusCode == 501 // Not Implemented
            || statusCode == 505 // Version Not Supported
            ))
        {
            // Do not retry
            return null;
        }

        // Monitor retries in the primary location
        ...

        // Determine RetryInterval and TargetLocation
        RetryInfo info =
            CreateRetryInfo(retryContext.CurrentRetryCount);

        return info;
    }
    ```

* A harmadik megközelítés egy egyéni figyelési összetevő az alkalmazásban, amely folyamatosan pingeli az elsődleges tárolási végpont dumi olvasási kérelmek (például egy kis blob olvasása) állapotának meghatározásához. Ez némi forrást igényelne, de nem jelentős összeget. Ha olyan problémát fedez fel, amely eléri a küszöbértéket, akkor a kapcsolót **másodlagoscsak** és csak olvasható módba kell végrehajtania.

Egy bizonyos ponton érdemes visszaváltani az elsődleges végpont használatára, és lehetővé kell tenni a frissítéseket. Ha a fent felsorolt első két módszer egyikét használja, egyszerűen visszaválthat az elsődleges végpontra, és engedélyezheti a frissítési módot, miután tetszőlegesen kiválasztott idő vagy műveletek száma történt. Ezután hagyja, hogy menjen át az újrapróbálkozási logika újra. Ha a probléma megoldódott, továbbra is az elsődleges végpontot fogja használni, és engedélyezi a frissítéseket. Ha továbbra is fennáll a probléma, akkor még egyszer visszavált a másodlagos végpontra és a csak olvasható módra, miután nem felelt meg a megadott feltételeknek.

A harmadik forgatókönyv esetén, ha az elsődleges tárolási végpont pingelése ismét sikeres lesz, aktiválhatja a kapcsolót a **PrimaryOnly-ra,** és folytathatja a frissítések engedélyezését.

## <a name="handling-eventually-consistent-data"></a>Kezelés végül konzisztens adatok

Georedundáns tárolási munkák tranzakciók replikálása az elsődleges a másodlagos régióban. Ez a replikációs folyamat garantálja, hogy a másodlagos régióban lévő adatok *végül konzisztensek lesznek.* Ez azt jelenti, hogy az elsődleges régió összes tranzakciója végül megjelenik a másodlagos régióban, de előfordulhat, hogy késés van a megjelenésük előtt, és nincs garancia arra, hogy a tranzakciók ugyanabban a sorrendben érkeznek meg, mint amilyensorrendben vannak. eredetileg az elsődleges régióban alkalmazták. Ha a tranzakciók nem sorrendben érkeznek a másodlagos régióba, *előfordulhat,* hogy a másodlagos régióban lévő adatok inkonzisztens állapotban vannak, amíg a szolgáltatás utoléri.

Az alábbi táblázat egy példát mutat be arra, hogy mi történhet, ha frissíti egy alkalmazott adatait, hogy azok a rendszergazdák szerepkör tagjává *válhassanak.* A példa kedvéért ehhez frissítenie kell az **alkalmazottentitást,** és frissítenie kell egy **rendszergazdai szerepkör** entitást a rendszergazdák teljes számának számával. Figyelje meg, hogyan alkalmazzák a frissítéseket sorrendben a másodlagos régióban.

| **Time** | **Tranzakció**                                            | **Replikációs**                       | **Utolsó szinkronizálás időpontja** | **Eredmény** |
|----------|------------------------------------------------------------|---------------------------------------|--------------------|------------| 
| T0       | "A" tranzakció: <br> Alkalmazott beszúrása <br> az elsődleges |                                   |                    | Az elsődleges,<br> még nem replikálódott. |
| T1       |                                                            | A tranzakció <br> replikált<br> Másodlagos | T1 | Az A tranzakció replikált másodlagos. <br>Utolsó szinkronizálás idáig frissítve.    |
| T2       | "B" tranzakció:<br>Frissítés<br> alkalmazotti entitás<br> az elsődleges  |                                | T1                 | "B" tranzakció írott elsődleges,<br> még nem replikálódott.  |
| T3       | C tranzakció:<br> Frissítés <br>adminisztrátor<br>szerepkör entitás a<br>Elsődleges |                    | T1                 | C tranzakció az elsődleges,<br> még nem replikálódott.  |
| *T4*     |                                                       | C tranzakció <br>replikált<br> Másodlagos | T1         | A C tranzakció replikált másodlagos.<br>A LastSyncTime nem frissült, mert <br>a B tranzakció még nem lett replikálva.|
| *T5*     | Entitások olvasása <br>másodlagos                           |                                  | T1                 | Az alkalmazott elavult értékét kapja <br> mert a B tranzakció nem <br> replikálódik még. Ön kap az új értéket<br> rendszergazdai szerepkör entitás, mert C<br> Replikált. Az utolsó szinkronizálás időpontja még mindig nem<br> frissítve lett, mert a B tranzakció<br> nem replikálódott. Megmondhatja a<br>a rendszergazdai szerepkör entitása inkonzisztens <br>mert az entitás dátuma/időpontja <br>az utolsó szinkronizálás ióményidő. |
| *T6 (1998)*     |                                                      | "B" tranzakció<br> replikált<br> Másodlagos | T6 (1998)                 | *T6* – A C-n keresztül végrehajtott összes tranzakció <br>replikált, Utolsó szinkronizálás időpontja<br> frissítésre kerül. |

Ebben a példában tegyük fel, hogy az ügyfél a T5 másodlagos régiójából való olvasásra vált. Jelenleg sikeresen tudja olvasni a **rendszergazdai szerepkör** entitását, de az entitás olyan értéket tartalmaz a rendszergazdák számára, amely jelenleg nem egyeztethető össze a másodlagos régióban rendszergazdaként megjelölt **alkalmazottentitások** számával. Az ügyfél egyszerűen megjelenítheti ezt az értéket, azzal a kockázattal, hogy inkonzisztens információ. Másik lehetőségként az ügyfél megpróbálhatja megállapítani, hogy a **rendszergazdai szerepkör** esetleg inkonzisztens állapotban van-e, mert a frissítések nem megfelelően történtek, majd erről tájékoztatja a felhasználót.

Annak felismeréséhez, hogy potenciálisan inkonzisztens adatokkal rendelkezik, az ügyfél használhatja az *utolsó szinkronizálási idő* értékét, amelyet bármikor megkaphat egy tárolási szolgáltatás lekérdezésével. Ez azt az időpontot jelzi, amikor a másodlagos régióban lévő adatok utoljára konzisztensek voltak, és amikor a szolgáltatás az adott időpont előtti összes tranzakciót alkalmazta. A fenti példában, miután a szolgáltatás beszúrta az **alkalmazott entitást** a másodlagos régióban, az utolsó szinkronizálási idő *T1*értékre van állítva. A *T1* szinten marad, amíg a szolgáltatás nem frissíti az **alkalmazottentitást** a másodlagos régióban, amikor *T6*értékre van állítva . Ha az ügyfél lekéri az utolsó szinkronizálási időpont, amikor beolvassa az entitást a *T5,* összehasonlíthatja azt az entitás időbélyegével. Ha az entitás időbélyege későbbi, mint az utolsó szinkronizálási idő, akkor az entitás potenciálisan inkonzisztens állapotban van, és az alkalmazáshoz tartozó műveletet végrehajthatja. Ennek a mezőnek a használatával tudnia kell, hogy mikor fejeződött be az elsődleges frissítés utolsó frissítése.

Az utolsó szinkronizálási időpont ellenőrzéséről a [Tárfiók utolsó szinkronizálási ideje tulajdonságának ellenőrzése című témakörben olvashat.](last-sync-time-get.md)

## <a name="testing"></a>Tesztelés

Fontos annak tesztelése, hogy az alkalmazás a várt módon viselkedik-e, amikor újrapróbálkozási hibákba ütközik. Például meg kell vizsgálnia, hogy az alkalmazás átvált-e a másodlagos és csak olvasható módba, ha problémát észlel, és visszavált, amikor az elsődleges régió ismét elérhetővé válik. Ehhez meg kell szimulálni a újrapróbálható hibákat, és szabályoznia kell, hogy milyen gyakran fordulnak elő.

A [Fiddler](https://www.telerik.com/fiddler) segítségével elhallgathatja és módosíthatja a HTTP-válaszokat egy parancsfájlban. Ez a parancsfájl képes azonosítani az elsődleges végpontból érkező válaszokat, és a HTTP-állapotkódot olyanra módosítja, amelyet a storage-ügyféltár újrapróbálható hibaként ismer fel. Ez a kódrészlet egy egyszerű példát mutat be egy Fiddler-parancsfájlra, amely elfogja az 502-es állapot visszaadásához az **alkalmazotti adatok** táblájával szembeni olvasási kérelmekre adott válaszokat:

```java
static function OnBeforeResponse(oSession: Session) {
    ...
    if ((oSession.hostname == "\[yourstorageaccount\].table.core.windows.net")
      && (oSession.PathAndQuery.StartsWith("/employeedata?$filter"))) {
        oSession.responseCode = 502;
    }
}
```

Ezt a példát kiterjesztheti a kérelmek szélesebb körének elfogására, és csak néhányon módosíthatja a **responseCode-ot,** hogy jobban szimuláljon egy valós forgatókönyvet. A Fiddler-parancsfájlok testreszabásáról a Fiddler [dokumentációban](https://docs.telerik.com/fiddler/KnowledgeBase/FiddlerScript/ModifyRequestOrResponse) található Kérelem vagy válasz módosítása című témakörben olvashat bővebben.

Ha az alkalmazás írásvédett módra való váltásának küszöbértékeit konfigurálhatóvá tette, könnyebb lesz a viselkedés nem éles tranzakciós kötetekkel való tesztelése.

## <a name="next-steps"></a>Következő lépések

* A másodlagos régióból való olvasásról további információt, többek között az Utolsó szinkronizálási idő tulajdonság beállításának további példáját az [Azure Storage Redundancia beállításai és az Olvasási georedundáns tárolás című témakörben talál.](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/)

* A teljes minta, amely bemutatja, hogyan lehet a váltás oda-vissza az elsődleges és másodlagos végpontok között, [lásd: Azure minták – Az áramkör-megszakító minta használata AZ RA-GRS tároló.](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs)
