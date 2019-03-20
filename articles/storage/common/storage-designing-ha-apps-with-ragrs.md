---
title: Írásvédett georedundáns tárolás (RA-GRS) használatával magas rendelkezésre állású Aaplications kialakítása |} A Microsoft Docs
description: Hogyan tervezhet rugalmas, így szolgáltatáskimaradások kezeléséhez magas rendelkezésre állású alkalmazások Azure-RA-GRS tároló használatával.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 01/17/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 256d709ac976736715f441ecde5eee22a6d86fa6
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58009083"
---
# <a name="designing-highly-available-applications-using-ra-grs"></a>RA-GRS használatával magas rendelkezésre állású alkalmazások tervezése

Felhőalapú infrastruktúrák, mint például az Azure Storage közös funkciója, a magas rendelkezésre állású platform biztosítanak az alkalmazások üzemeltetéséhez. A felhőalapú alkalmazások kell alaposan fontolja meg, hogyan használhatja a platform révén magas rendelkezésre állású alkalmazások a felhasználók számára. Ez a cikk a fejlesztők miképpen használhatják írásvédett Georedundáns tárolás (RA-GRS) annak érdekében, az Azure Storage-alkalmazásaikat magas rendelkezésre állású összpontosít.

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

Ez a cikk a GRS és RA-GRS összpontosít. A grs Tárolással az adatokat három példányban őrzi meg az elsődleges régióban a storage-fiók beállítása során kiválasztott. Három további másolatot készít egy Azure által megadott másodlagos régióban aszinkron módon karbantartása. RA-GRS, olvasási hozzáférés a másodlagos példány georedundáns tárolást kínál.

Információ arról, hogy mely elsődleges régió párban mely másodlagos régióban lásd: [üzleti folytonossági és vészhelyreállítási helyreállítási (BCDR): Azure – párosított régiók](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

Nincsenek kódrészletek szerepelni fog ebben a cikkben és a egy teljes példa, amely letölthető és futtatható végén mutató hivatkozást.

> [!NOTE]
> Az Azure Storage mostantól támogatja a zónaredundáns tárolás (ZRS), magas rendelkezésre állású alkalmazások létrehozásához. A ZRS redundancia igényeinek megfelelően számos alkalmazás egyszerű megoldást kínál. A ZRS védelmet nyújt az hardveres hibák esetén, vagy egyetlen adatközponton érintő súlyos vészhelyzetek esetére. További információkért lásd: [zónaredundáns tárolás (ZRS): Azure Storage magas rendelkezésre állású alkalmazások](storage-redundancy-zrs.md).

## <a name="key-features-of-ra-grs"></a>RA-GRS főbb funkciói

Ne feledje alábbi alapvető szempontokat tervezésekor az alkalmazás az RA-GRS:

* Az Azure Storage fenntart egy csak olvasható másodlagos régióban, az elsődleges régióban tárolja az adatokat. A fentieknek megfelelően a storage szolgáltatás helyét, a másodlagos régió határozza meg.

* A csak olvasható Másolás [végül konzisztens](https://en.wikipedia.org/wiki/Eventual_consistency) az elsődleges régióban lévő adatokkal.

* A blobok, táblák és üzenetsorok, lekérdezheti, ha a másodlagos régióban egy *utolsó szinkronizálás időpontja* érték, amely közli, hogy mikor történt, az utolsó replikáció az elsődleges kiszolgálóról a másodlagos régióba. (Ez nem támogatott az Azure-fájlokat, amelyek jelenleg nem rendelkezik az RA-GRS redundancia.)

* A Storage ügyféloldali kódtára segítségével használni tudják az adatokat az elsődleges vagy másodlagos régióban. Is irányíthatja olvasási kérelmek automatikusan a másodlagos régióban, ha az elsődleges régió felé az olvasási kérelem időkorlátja lejár.

* Ha az elsődleges régió elérhetetlenné válik, egy fiók feladatátvételt kezdeményezhet. Amikor feladatátvételt a másodlagos régióba, a DNS-bejegyzések az elsődleges régió felé mutat módosult, mutasson a másodlagos régióba. A feladatátvétel befejezése után írási hozzáférés helyreállítva GRS és RA-GRS fiókok esetében. További információkért lásd: [katasztrófa utáni helyreállítás és a tárolási fiók feladatátvételi (előzetes verzió) az Azure Storage-ban](storage-disaster-recovery-guidance.md).

## <a name="application-design-considerations-when-using-ra-grs"></a>Alkalmazások tervezési szempontjait, ha az RA-GRS használatával

Ez a cikk célja megmutatjuk, hogyan tervezhető olyan alkalmazás, amely továbbra is (bár a korlátozott funkciókkal), az elsődleges adatközpont jelentős katasztrófa is működni fognak. Az alkalmazás átmeneti vagy hosszan futó problémák kezeléséhez olvasni a másodlagos régió szerint, az elsődleges régióba való olvasás ütköző probléma esetén is tervezhet. Ha az elsődleges régió újra elérhetővé válik, az alkalmazás az elsődleges régióból történő olvasó térhet vissza.

### <a name="using-eventually-consistent-data"></a>Végül konzisztens adatokat használatával

A javasolt megoldás feltételezi, hogy ez elfogadható, ha a hívó alkalmazás esetlegesen elavult adatokat. Mivel a másodlagos régióban lévő adatok konzisztens másolataként, akkor lehet az elsődleges régió elérhetetlenné válhat, előtt a másodlagos régióba frissítés befejeződött, replikálásához.

Tegyük fel például, hogy az ügyfél sikeres frissítést küld, de az elsődleges régió nem sikerül, mielőtt végbement volna a frissítés van a másodlagos régióba. Ha az ügyfél kéri vissza az adatokat, az elavult adatok mailekben helyett a következő frissített adatokat a másodlagos régióból. Az alkalmazás tervezésekor el kell döntenie ez elfogadható-e, és ha igen, hogyan fogja jelenik meg az ügyfél. 

Ez a cikk későbbi részében bemutatjuk, hogyan ellenőrizheti a legutóbbi szinkronizálás időpontja a másodlagos adatok ellenőrzéséhez, hogy naprakész állapotban-e a másodlagos.

### <a name="handling-services-separately-or-all-together"></a>Szolgáltatások kezelése minden együtt vagy külön-külön

Amíg nem valószínű, lehetőség egy szolgáltatáshoz, amely már nem érhető el, amíg a többi szolgáltatás továbbra is teljes körűen használható. Kezelheti az újrapróbálkozásokat és az egyes csak olvasható üzemmódú szolgáltatás külön (blobok, üzenetsorok, táblák), illetve a storage-szolgáltatások általános újrapróbálkozások együtt kezelheti.

Például ha üzenetsorokat és blobokat használ az alkalmazásban, dönthet a Újrapróbálkozást lehetővé tevő hibáinak kezelése minden egyes külön kódot helyezi. Ezután ha a blobszolgáltatás egy újrapróbálkozási kap, de a queue szolgáltatás továbbra is működik, csak az alkalmazás, amely kezeli a blobok a részeként érint. Ha úgy dönt, hogy az összes tárolási szolgáltatás az újrapróbálkozások kezelésére általános védelemmel lát, és a blob szolgáltatás hívása egy újrapróbálható hiba adja vissza, majd a blob szolgáltatás és a queue szolgáltatás kérelmeket érinti.

Végső soron Ez függ az alkalmazás összetettségét. Előfordulhat, hogy nem kívánja kezelni a hibákat a szolgáltatás által, de ehelyett átirányítása lemezolvasási kérések a másodlagos régióba tárolási szolgáltatásokhoz és az alkalmazás futtatásához csak olvasható módban, ha az elsődleges régióban észlelni a használatával bármilyen probléma.

### <a name="other-considerations"></a>Egyéb szempontok

Ezek azok az egyéb szempontok ismertetjük, hogy ez a cikk további részében található.

*   Kezeli az újrapróbálkozásokat olvasási kérelmek használatával az áramkör-megszakító minta

*   Végül konzisztens adatokat és a legutóbbi szinkronizálás időpontja

*   Tesztelés

## <a name="running-your-application-in-read-only-mode"></a>Az alkalmazás csak olvasható módban fut.

RA-GRS tároló használatához mindkét sikertelen olvasási kérelmek kezelésére is képesnek kell lennie, és nem sikerült a frissítési kérelmek (az update tehát ebben az esetben a Beszúrás, frissítés és törlés). Ha az elsődleges adatközpont meghibásodik, olvassa el a kérelmek átirányíthatók a másodlagos adatközpontba. Azonban a frissítési kérelmek nem irányítható a másodlagos, mert a másodlagos csak olvasható. Ebből kifolyólag kell terveznie az alkalmazás csak olvasható módban történő futtatására.

Megadhatja például, hogy azt a jelzőt, amely be van jelölve, az Azure Storage-frissítés kérések elküldése előtt. Ha, egy a frissítési kérelmet, kihagyhatja, és az ügyfélnek a megfelelő választ ad vissza. Előfordulhat, hogy még akkor is le kívánja tiltani bizonyos szolgáltatások funkciót azonban teljesen, amíg a probléma megoldódott, és értesítse a felhasználókat, hogy ezek a funkciók átmenetileg nem érhetők el.

Ha úgy dönt, az egyes szolgáltatások hibáinak kezelése a külön-külön is szüksége lesz az alkalmazás futtatására csak olvasható módban szolgáltatás kezeléséhez. Például előfordulhat, hogy rendelkezik olvasási jelzők minden egyes szolgáltatás, amely engedélyezve van, és le van tiltva. Ezután a kód a megfelelő helyeken jelző képes kezelni.

Egy másik oldalán benefit képes arra, hogy az alkalmazás futtatásához csak olvasható módban van –, lehetővé teszi annak biztosítása érdekében korlátozott működéssel egy nagyobb alkalmazásfrissítés során. Csak olvasható módban fusson, és a másodlagos adatközpont, mutasson az alkalmazás senki sem fér hozzá az adatokat az elsődleges régióban közben végez frissítéseket biztosító aktiválhat.

## <a name="handling-updates-when-running-in-read-only-mode"></a>Frissítések kezelése, csak olvasható módban való futtatáskor

Számos módon frissítési kérelmek kezelésére, csak olvasható módban való futtatáskor. Nem bemutatjuk, ez átfogó, de általában három olyan mintákat, amelyek több.

1.  A felhasználónak válaszolni, és mondja el neki, amelyek jelenleg nem fogad frissítéseket. Például egy ügyfél rendszer tudta engedélyezni az ügyfelek számára, hogy a kapcsolattartási adatok eléréséhez, de nem végzi a frissítéseket.

2.  Sorba helyezni a frissítéseket egy másik régióban is. Ebben az esetben, lenne a függőben lévő kérelmek írni a várólista egy másik régióban, és ezután tudja feldolgozni ezeket a kérelmeket, miután újra online állapotba az elsődleges adatközpont kerül. Ha ebben a forgatókönyvben hagyja, a vásárlót, hogy a kért frissítés a rendszer sorba állítja későbbi feldolgozás céljából.

3.  A frissítések írhat egy másik régióban lévő tárfiókhoz. Ha az elsődleges adatközpont ismét online elérhető, akkor is egyesíti ezeket a frissítéseket az elsődleges adatokat, az adatok struktúráját függően úgy rendelkezik. Például ha az a név egy dátum-idő bélyeg külön fájlt hoz létre, másolhatja azokat a fájlokat az elsődleges régióba. Ez a módszer bizonyos számítási feladatokhoz, például a naplózás és az iOT-adatokat.

## <a name="handling-retries"></a>Kezeli az újrapróbálkozásokat

Hogyan tudjuk, hogy mely hibákat – Újrapróbálkozást lehetővé tevő? Ez határozza meg a storage ügyféloldali kódtára. Például egy 404-es hiba (az erőforrás nem található) nem áll – Újrapróbálkozást lehetővé tevő mivel újrapróbálása azt valószínűleg nem sikeres eredményez. Másrészről 500-as hiba azért Újrapróbálkozást lehetővé tevő kiszolgálói hiba, és egyszerűen lehet egy átmeneti probléma. További részletekért tekintse meg a [nyissa meg a forráskódot ExponentialRetry osztály](https://github.com/Azure/azure-storage-net/blob/87b84b3d5ee884c7adc10e494e2c7060956515d0/Lib/Common/RetryPolicies/ExponentialRetry.cs) storage .NET ügyféloldali kódtár. (A ShouldRetry használata esetén tekintse meg.)

### <a name="read-requests"></a>Olvasási kérelmek

Ha az elsődleges tárolási probléma van olvasási kérelmek átirányíthatók a másodlagos tárterületre. Mint azt a fentiekben leírtuk a [végül konzisztens adatokat használó](#using-eventually-consistent-data), elfogadható, hogy az alkalmazás esetlegesen elavult adatokat olvasni. kell lennie. A storage ügyféloldali kódtára segítségével RA-GRS-adatok elérése, ha egy olvasási kérést újrapróbálkozási viselkedését értékének beállításával megadhatja a **LocationMode** tulajdonságot a következők egyikét:

*   **PrimaryOnly** (alapértelmezett)

*   **PrimaryThenSecondary**

*   **SecondaryOnly**

*   **SecondaryThenPrimary**

Ha a **LocationMode** való **PrimaryThenSecondary**, ha a kezdeti olvasási kérést az elsődleges végpont sikertelen lesz, és a egy újrapróbálható hiba, az ügyfél automatikusan lehetővé teszi egy másik olvasási kérést, hogy a másodlagos végpont. Ha a hiba a kiszolgáló időkorlátja, az ügyfél kell várnia az időkorlát lejár, mielőtt egy újrapróbálható hiba kap a szolgáltatástól.

Alapvetően két forgatókönyv közül választhat, fontolja meg, hogyan reagáljon a egy újrapróbálható hiba meghatározásakor:

*   Ez egy elkülönített probléma és az elsődleges végpontra későbbi kérelmeket nem ad vissza egy újrapróbálható hiba. Egy példa, ahol ez akkor fordulhat elő, amikor egy átmeneti hálózati hiba.

    Ebben a forgatókönyvben nincs nincs jelentős teljesítménybeli napján belül pótdíj kellene **LocationMode** beállítása **PrimaryThenSecondary** , ez csak akkor fordul elő ritkán.

*   Ez a probléma legalább egy, a storage-szolgáltatás, az elsődleges régióban, és minden későbbi kérelmeket az adott szolgáltatáshoz, az elsődleges régióban valószínűleg Újrapróbálkozást lehetővé tevő hibák visszaadása egy ideig. Példa erre, ha az elsődleges régióban nem érhető el teljesen.

    Ebben a forgatókönyvben nincs rendszer teljesítményét, mert az olvasási kéréseket fog próbálja meg először az elsődleges végpont, várjon, amíg az időkorlát lejár, majd váltson át a másodlagos végpontra.

Ebben az esetben meg kell határoznia, amely nincs az elsődleges végpont folyamatos problémáját, és küldése közvetlenül a másodlagos végpontnak küldött kérelmek beállításával olvassa a **LocationMode** tulajdonságot **SecondaryOnly** . Jelenleg is módosítania kell az alkalmazás csak olvasható módban történő futtatására. Ezt a módszert nevezik a [áramkör-megszakító minta](https://msdn.microsoft.com/library/dn589784.aspx).

### <a name="update-requests"></a>Szabályzatfrissítési kérések

Az áramkör-megszakító minta frissítési kérelmek is alkalmazható. Szabályzatfrissítési kérések azonban nem irányítható a másodlagos tárterületre, amely csak olvasható. Ezeket a kérelmeket, akkor hagyja a **LocationMode** tulajdonság **PrimaryOnly** (alapértelmezett). Ezek a hibák kezelésére, metrika vonatkoznak ezek a kérelmek – például a sor – 10 hibák, és a küszöbértéket, amikor váltson az alkalmazás írásvédett módba. Frissítési mód, mint amelyek a következő szakaszban arról az áramkör-megszakító minta az alább ismertetett használhatja a visszaadó ugyanazokat a módszereket.

## <a name="circuit-breaker-pattern"></a>Áramkör-megszakító minta

Az alkalmazásban használt áramkör-megszakítási minta megakadályozhatja, amely nagy eséllyel lesz sikertelen ismételten művelet újrapróbálása. Ez lehetővé teszi, hogy az alkalmazás futtatásának folytatásához ahelyett idő közben a művelet megkezdésének exponenciálisan rendszer. Azt is észleli, ha a tartalék megoldottuk, ekkor az alkalmazás megpróbálhatja megismételni a műveletet.

### <a name="how-to-implement-the-circuit-breaker-pattern"></a>Az áramkör-megszakító minta megvalósítása

Azonosíthatja, hogy nincs-e egy meglévő problémája elsődleges végpontból, követheti nyomon milyen gyakran az ügyfél – Újrapróbálkozást lehetővé tevő hibát észlel. Mivel minden esetben egy másik, hogy határoz meg a döntését átvált a másodlagos végpontot, és csak olvasható módban az alkalmazás futtatásához használni kívánt küszöbértéket. Például ha 10 hibák teendőt olyan sorok esetén a nem sikeres, hajtsa végre a kapcsoló sikerült dönt. Egy másik példa, hogy ha 90 %-a kérelmek egy 2 perces időszakban sikertelen.

Az első forgatókönyvben is egyszerűen tartsa a hibák számát, és ha sikeres érhesse el a maximális száma állítsa vissza a nulla. A második forgatókönyvben egyik implementálásáról módja a MemoryCache objektum használata (a .NET-ben). Az egyes kérések hozzáadása egy CacheItem a gyorsítótárat, állítsa az értékét (1) sikeres vagy sikertelen (0), és állítsa a lejárati idő 2 percet most (vagy bármilyen az időkorlát van). Egy bejegyzés lejárati idő elérésekor a rendszer automatikusan eltávolítja a bejegyzést. Ekkor kap egy 2 perces működés közbeni ablakot. Minden alkalommal, amikor egy kérést a storage szolgáltatás először segítségével egy Linq-lekérdezésekre különböző MemoryCache Objekt számítja ki a sikerességi arány százalékban megengedő az értékeket, és a számát elosztjuk. Ha a sikerességi arány százalékban (például 10 %) néhány küszöbérték alá csökken, a **LocationMode** tulajdonság olvasási kérelmek **SecondaryOnly** , és váltson az alkalmazás írásvédett módba a folytatás előtt.

A küszöbértéket, hogy mikor, hogy a kapcsoló hibák eltérőek lehetnek szolgáltatás a szolgáltatás az alkalmazásban, így érdemes lehet minősítené konfigurálható paraméterek. Ez akkor is döntheti el, az egyes szolgáltatások – Újrapróbálkozást lehetővé tevő hibáinak kezelése a külön-külön vagy egy, a korábban említett.

Egy másik szempont, hogyan legyen kezelve az alkalmazás több példánya, és mi a teendő, ha az egyes példányok Újrapróbálkozást lehetővé tevő hibák észlelése. Például előfordulhat, hogy rendelkezik a betöltött ugyanazt az alkalmazást futtató 20 virtuális gépet. Tegye mindegyik példány külön-külön kezelni? Ha egy példány indításakor problémák merülnek fel, szeretné korlátozni a választ, hogy az egyik példányhoz, vagy szeretné próbál meg kell minden példány ugyanúgy válaszol, amikor egy példány van probléma? A példányok külön-külön kezelése sokkal egyszerűbb, mint a próbál koordinálja a válasz azok között, de ennek módja függ az alkalmazás architektúra.

### <a name="options-for-monitoring-the-error-frequency"></a>Figyelheti a hiba gyakorisága

Három fő lehetősége a gyakoriságát, az elsődleges régióban újrapróbálkozások figyelése annak érdekében, hogy mikor váltson át a másodlagos régióba, és módosítsa az alkalmazás futtatásához csak olvasható módban van.

*   Adjon hozzá egy kezelő-a [ **újrapróbálkozás** ](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.operationcontext.retrying.aspx) -es azonosítójú esemény a [ **OperationContext** ](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.operationcontext.aspx) objektumot adja át a Storage-kérelmek – Ez a mód Ez a cikk jelenik meg, és a hozzájuk tartozó mintában használt. Ezek az események aktiválódik, amikor az ügyfél újrapróbálkozik a kéréseket, így nyomon követheti, hogy milyen gyakran az ügyfél egy elsődleges végponton Újrapróbálkozást lehetővé tevő hibát észlel-e.

    ```csharp 
    operationContext.Retrying += (sender, arguments) =>
    {
        // Retrying in the primary region
        if (arguments.Request.Host == primaryhostname)
            ...
    };
    ```

*   Az a [ **Evaluate** ](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.retrypolicies.iextendedretrypolicy.evaluate.aspx) metódus az egyéni újrapróbálkozási szabályzatot, futtathatja egyéni kódot, amikor egy újrapróbálkozási kerül sor. Amikor egy újrapróbálkozási rögzítése mellett történik, ez is lehetővé teszi, hogy az újrapróbálkozási viselkedés módosításához.

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

*   A harmadik módszer az, hogy egy egyéni monitorozási összetevő megvalósítása az alkalmazásban, amely folyamatosan Pingeli az elsődleges tárolóvégpont a lemezolvasási kérések (például egy kis méretű blob olvasása) dummy meghatározni az állapotát. Ez egyes erőforrásokat, de nem jelentős mentése igénybe. Ha a probléma felderítése, amely eléri a küszöbértéket, majd kell elvégeznie a kapcsolót, hogy **SecondaryOnly** és írásvédett módban.

Bizonyos helyzetekben célszerű váltson vissza az elsődleges végpont használatával, és lehetővé teszi a frissítések. Ha a fenti első két módszer egyikével, sikerült egyszerűen váltson vissza az elsődleges végpont és a frissítési mód engedélyezése tetszőlegesen kiválasztott mennyi idő vagy a műveletek végrehajtását követően. Majd engedélyezheti azt az újrapróbálkozási logika meg újra. Ha a problémát megoldottuk, továbbra is az elsődleges végpontot használja, és engedélyezi a frissítéseket. Ha a probléma továbbra is van, azt fogja még egyszer váltson vissza a másodlagos végpontra és a csak olvasható üzemmódú után sikertelen a beállított feltételeknek.

A harmadik forgatókönyvhöz, amikor az elsődleges tárolóvégpont pingelése sikeres ismét válik, is aktiválhatja a kapcsoló vissza **PrimaryOnly** , és folytassa a frissítések engedélyezése.

## <a name="handling-eventually-consistent-data"></a>Végül konzisztens adatokat kezelése

Az RA-GRS úgy működik, hogy az elsődleges régióból a másodlagosba replikálja a tranzakciókat. A replikációs folyamat garantálja, hogy van-e a másodlagos régióban lévő adatok *végül konzisztens*. Ez azt jelenti, hogy az elsődleges régióban tranzakciók végül jelenik meg a másodlagos régióhoz, azonban, hogy a késés csak akkor jelennek meg is lehet, és, hogy nincs-e a másodlagos régió, amelyben, amely ugyanabban a sorrendben érkeznek a tranzakciók garancia arra, hogy eredetileg voltak a alkalmazni az elsődleges régióba. Ha a tranzakciók a másodlagos régió sorrendje, nem érkezik meg *előfordulhat, hogy* fontolja meg az adatok mindaddig, amíg a szolgáltatás behozza inkonzisztens állapotban lennie a másodlagos régióban.

Az alábbi táblázat egy példát, hogy mi történne, ha tagként számára egy egy alkalmazott adatainak frissítése a *rendszergazdák* szerepkör. Ebben a példában az ehhez szükséges frissítenie a **alkalmazott** entitás- és update- **rendszergazdai szerepkör** entitás-számot tartalmazza a rendszergazdák teljes száma. Figyelje meg, hogy a frissítések alkalmazása sorrendben a másodlagos régióban.

| **idő** | **Tranzakció**                                            | **Replikáció**                       | **Utolsó szinkronizálás időpontja** | **Eredmény** |
|----------|------------------------------------------------------------|---------------------------------------|--------------------|------------| 
| T0       | Tranzakció válasz: <br> Alkalmazott beszúrása <br> az elsődleges entitás |                                   |                    | Elsődleges – beszúrni egy tranzakció<br> még nem replikált. |
| T1       |                                                            | A tranzakció <br> replikálja a<br> másodlagos | T1 | Tranzakciós A másodlagos replikálja. <br>Utolsó szinkronizálás időpontja frissítve.    |
| T2       | "B" tranzakció<br>Frissítés<br> Alkalmazott entitás<br> az elsődleges  |                                | T1                 | Tranzakció írt elsődleges, a B<br> még nem replikált.  |
| T3       | Transaction C:<br> Frissítés <br>rendszergazda<br>a szerepkör-entitás<br>elsődleges |                    | T1                 | Tranzakció írt elsődleges, C<br> még nem replikált.  |
| *T4*     |                                                       | Tranzakció C <br>replikálja a<br> másodlagos | T1         | Tranzakció replikálja a másodlagos C.<br>Nincs frissítve, mert LastSyncTime <br>tranzakció B még nem replikálódott.|
| *T5*     | Entitások olvasása <br>a másodlagos                           |                                  | T1                 | A régi értéket alkalmazott <br> entitás, mert a tranzakció B nem <br> még replikált. Az új értéket kap<br> rendszergazdai szerepkör entitás mert C<br> replikálja. Utolsó szinkronizálás időpontja még nem.<br> lett frissítve, mert a tranzakció B<br> még nem replikálódnak. Azt is megadhatja, hogy a<br>rendszergazdai szerepkör entitás nem konzisztens <br>mivel az entitás dátum/idő után <br>a legutóbbi szinkronizálás időpontja. |
| *T6*     |                                                      | B tranzakció<br> replikálja a<br> másodlagos | T6                 | *T6* – C keresztül minden tranzakciónak kell <br>lettek replikálva, a legutóbbi szinkronizálás időpontja<br> frissül. |

Ebben a példában feltételeztük az ügyfél vált, amennyiben az olvasó T5, a másodlagos régióból. Sikeresen tudja olvasni a **rendszergazdai szerepkör** entitás jelenleg, de az entitás tartalmazza a replikája nem konzisztens a száma a rendszergazdák számának értékét **alkalmazott** entitások számára jelenleg megjelölve a rendszergazdák a másodlagos régióban. Az ügyfél egyszerűen jelenjenek meg ezt az értéket, és annak a kockázata, hogy-e nem biztosítottak elegendő információt. Másik megoldásként az ügyfél kísérletet határozza meg, amely a **rendszergazdai szerepkör** egy potenciálisan inkonzisztens állapotban van, mert a frissítések sorrendje nem azért történt, és majd tájékoztatja a felhasználót az a tény.

Ismeri fel, hogy esetleg ellentmondásos adatok rendelkezik, az ügyfél által használt értékét a *utolsó szinkronizálás időpontja* , hogy megkaphassa bármikor a storage szolgáltatás lekérdezése. Azonban igen, akkor az idő, mikor történt legutóbbi a másodlagos régióban lévő adatok konzisztens, és ha a szolgáltatás alkalmazott előtt adott pont a tranzakciók időben. Után a szolgáltatás beszúrja a fent bemutatott példában a **alkalmazott** beállítása a másodlagos régióban, a legutóbbi szinkronizálás időpontja entitás *T1*. Ez továbbra is *T1* amíg a szolgáltatásfrissítések a **alkalmazott** entitást, ha ezt a beállítást a másodlagos régióban *T6*. Ha az ügyfél lekéri a legutóbbi szinkronizálás időpontja, amikor az entitást, olvas be *T5*, azt is összehasonlíthatja az entitásra az időbélyegzővel. Ha a az entitás időbélyegzőjének későbbi, mint a legutóbbi szinkronizálás időpontja, majd az entitás egy potenciálisan inkonzisztens állapotban van, és elvégezhető függetlenül-e a megfelelő műveletet az alkalmazás. Ez a mező használatához arról, hogy mikor fejeződött be az utolsó frissítés az elsődleges.

## <a name="testing"></a>Tesztelés

Fontos, hogy az alkalmazás működését – Újrapróbálkozást lehetővé tevő hiba várt teszteléséhez. Például szeretné tesztelni, hogy az alkalmazás-kapcsolók és a csak olvasható módban, ha problémát észlel, és átvált a másodlagos biztonsági, amikor az elsődleges régió újra lesz elérhető. Ehhez meg kell egy újrapróbálkozást lehetővé tevő hiba és milyen gyakran előforduló vezérlő szimulálásához.

Használhat [Fiddler](https://www.telerik.com/fiddler) elfogására és a egy parancsfájlban HTTP-válaszok módosítása. Ez a szkript is azonosítsa az elsődleges végpontra érkező válaszokat, és módosítsa a HTTP-állapotkód, hogy a Storage ügyféloldali kódtára – Újrapróbálkozást lehetővé tevő hiba felismeri. Ez a kódrészlet bemutatja egy egyszerű példa, amely elfogja a választ olvassa el a kérelmeket a Fiddler parancsfájlra a **employeedata** tábla visszatérési 502 állapotot:

```java
static function OnBeforeResponse(oSession: Session) {
    ...
    if ((oSession.hostname == "\[yourstorageaccount\].table.core.windows.net")
      && (oSession.PathAndQuery.StartsWith("/employeedata?$filter"))) {
        oSession.responseCode = 502;
    }
}
```

Ebben a példában intercept kérelmek szélesebb köre, és csak módosítása lehetett bővíteni a **responseCode** egyes jobban szimulálja a valós életből vett helyzet. Fiddler-parancsfájlok testreszabásával kapcsolatos további információkért lásd: [kérés módosítása](https://docs.telerik.com/fiddler/KnowledgeBase/FiddlerScript/ModifyRequestOrResponse) a Fiddler dokumentációjában.

Ha végzett az alkalmazás konfigurálható csak olvasható módra váltáshoz a küszöbértékeket, tesztelje a viselkedés nem éles tranzakció kötetek könnyebb lesz.

## <a name="next-steps"></a>További lépések

* További információt kapcsolatos írásvédett Georedundáns tárolás, többek között egy másik példa a LastSyncTime hogyan van beállítva, tekintse meg [írásvédett Georedundáns tárolás és a Windows Azure Storage Redundanciabeállításainál](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

* Teljes példa, amely bemutatja, hogy a kapcsoló oda-vissza az elsődleges és másodlagos végpontok közötti, lásd: [Azure-minták – az áramkör-megszakító minta használata a RA-GRS tároló](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs).
