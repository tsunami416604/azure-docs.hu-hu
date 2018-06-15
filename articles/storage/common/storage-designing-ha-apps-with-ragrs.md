---
title: Azure-írásvédett Georedundáns tárolás (RA-GRS) használatával magas rendelkezésre álló alkalmazások megtervezése |} Microsoft Docs
description: Hogyan lehet Azure-RA-GRS storage segítségével tervezővel vajon elég rugalmas az kimaradások kezelni a magas rendelkezésre állású alkalmazások.
services: storage
documentationcenter: .net
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 8f040b0f-8926-4831-ac07-79f646f31926
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/21/2018
ms.author: tamram
ms.openlocfilehash: f7f3f2d99e5582a1bcb672cc176258dfff9c3217
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
ms.locfileid: "30322930"
---
# <a name="designing-highly-available-applications-using-ra-grs"></a>RA-GRS használatával magas rendelkezésre álló alkalmazások megtervezése

Egy felhőalapú infrastruktúrák hasonlóan az Azure Storage közös szolgáltatása a magas rendelkezésre állású platform biztosítják az alkalmazások tárolására szolgáló. A felhőalapú alkalmazások fejlesztők kell alaposan fontolja meg, hogyan használhatók ki ezen a platformon, magas rendelkezésre állású alkalmazások a felhasználóknak való biztosítása érdekében. Ez a cikk foglalkozik a fejlesztők használatát írásvédett Georedundáns tárolás (RA-GRS) biztosításához az Azure Storage alkalmazásaikat képező magas rendelkezésre állású.

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

Ez a cikk a GRS és az RA-GRS összpontosít. A GRS az adatok három példányban tárolják a storage-fiók beállítása során kiválasztott elsődleges régióban. Három további másolatokat aszinkron módon karbantartása az Azure által megadott másodlagos régióba. RA-GRS ugyanaz, mint a Georedundáns, azzal a különbséggel, hogy rendelkezik olvasási hozzáféréssel a másodlagos példány. Azure Storage redundancia különböző beállításokkal kapcsolatos további információkért lásd: [Azure Storage replikációs](https://docs.microsoft.com/azure/storage/storage-redundancy). A replikációs cikk azt az elsődleges és másodlagos régiók párosítása is ismerteti.

Nincsenek kódtöredékek szerepelni fog ebben a cikkben, és egy teljes mintát, töltse le, és futtassa a végén mutató hivatkozást.

> [!NOTE]
> Az Azure Storage zónaredundáns tárolás (ZRS) mostantól támogatja a magas rendelkezésre állású alkalmazások létrehozásához. A ZRS számos alkalmazás redundanciájának igényeinek egy egyszerű megoldást kínál. A ZRS a hardver meghibásodása vagy egy egyetlen datacenter érintő katasztrofális katasztrófák védelmet biztosít. További információkért lásd: [zónaredundáns tárolás (ZRS): Azure Storage magas rendelkezésre állású alkalmazások](storage-redundancy-zrs.md).

## <a name="key-features-of-ra-grs"></a>RA-GRS a kulcsfontosságú szolgáltatásokat

Vegye figyelembe a legfontosabb az RA-GRS alkalmazását tervezése során:

* Az Azure Storage fenntart egy csak olvasható az elsődleges régióban egy másodlagos régióban tárolja az adatokat. A fentiek szerint a tároló szolgáltatást határozza meg a másodlagos régióba helyét.

* A csak olvasható másolat [idővel konzisztenssé](https://en.wikipedia.org/wiki/Eventual_consistency) az adatokat az elsődleges régióban.

* A blobot, táblát és üzenetsort, a másodlagos régióba lekérheti a *utolsó szinkronizálás* érték, amely jelzi, hogy mikor történt, a legutóbbi replikáció, az elsődleges a másodlagos régióba. (Ez nem támogatott az Azure-fájlokat, amelyek jelenleg nem rendelkezik az RA-GRS redundancia.)

* A Storage ügyféloldali kódtár segítségével az adatok az elsődleges vagy másodlagos régióban kezeléséhez. Átirányítani, akkor olvassa el automatikusan a másodlagos régióba kérelmeket, ha az elsődleges régióban olvasási kérelem időkorlátja lejár.

* Ha egy elsődleges régióban adatok elérhetőségét érintő jelentős probléma, az Azure-csapat indíthatnak földrajzi-feladatátvétel, amikor a DNS-bejegyzések az elsődleges régióban mutató változnak, hogy a másodlagos régióba mutasson.

* Egy földrajzi feladatátvétel esetén Azure lesz jelöljön ki egy új másodlagos helyet erre a helyre replikálja az adatokat, majd a másodlagos DNS-bejegyzések mutasson. A másodlagos végponti elérhetetlenné válik, amíg a tárfiók nem fejeződött be, replikálása. További információkért lásd: [Mi a teendő, ha egy Azure Storage esetleges leálláskor](https://docs.microsoft.com/azure/storage/storage-disaster-recovery-guidance).

## <a name="application-design-considerations-when-using-ra-grs"></a>RA-GRS használatakor alkalmazás kialakítási szempontok

Ez a cikk célja mutatjuk be, hogyan tervezhető olyan alkalmazás, amely továbbra is működnek (bár a korlátozott kapacitás), még akkor is, az elsődleges adatközpont jelentős katasztrófa esetén. Az alkalmazás számára az elsődleges régióban a olvasásakor ütköző probléma van a másodlagos régióba történő olvasás által kezelni a átmeneti vagy hosszú futású problémák kialakítani. Az elsődleges régióban újból elérhető lesz, ha az alkalmazás elsődleges régióban olvasási térhet vissza.

### <a name="using-eventually-consistent-data"></a>Idővel konzisztenssé adatok használata

A javasolt megoldás feltételezi, hogy a rendszer elfogadható, ha a hívó alkalmazás potenciálisan elavult adatokat. Mivel az adatokat a másodlagos régióban idővel konzisztenssé, akkor lehet az elsődleges régióban előtt egy frissítést adunk ki a másodlagos régióba befejezte a replikáló lehetnek nem elérhetők.

Tegyük fel például, hogy az ügyfél sikeresen elküldi a frissítést, de az elsődleges régióban nem sikerül, a másodlagos régióba propagálja a frissítés előtt. Amikor az ügyfél beolvasni az adatokat vissza megkérdezi, hogy a elavult adatokat fogad helyett a frissített adatokat a másodlagos régióba. Az alkalmazás tervezésekor döntse el, hogy ez az elfogadható, és ha igen, hogyan fogja jelenik meg az ügyfél. 

A cikk későbbi részében megmutatjuk, hogyan ellenőrizhető a legutóbbi szinkronizálás ideje a másodlagos adatok annak ellenőrzéséhez, hogy a másodlagos naprakész.

### <a name="handling-services-separately-or-all-together"></a>Kezelési szolgáltatások, külön-külön vagy együtt

Nem valószínű, miközben lehetőség egy szolgáltatás elérhetetlenné válik, amíg az egyéb szolgáltatások továbbra is teljes körűen működik. Kezelheti az újrapróbálkozások és az egyes írásvédett módban szolgáltatás külön-külön (BLOB, üzenetsorok, táblák), vagy a tárolási szolgáltatások általános újrapróbálásainak együtt kezelheti.

Például ha az alkalmazás üzenetsorokat és blobokat használ, dönthet úgy, amelyre az újrapróbálkozást lehetővé tevő hibák kezelésének minden egyes külön kódban. Majd ha ismételt próbálkozással beszerezni a blob szolgáltatás, de továbbra is működik-e a várólista-szolgáltatás, csak az alkalmazáshoz, amely kezeli a blobok részét csökkenhet. Ha úgy dönt, hogy az összes tárolási szolgáltatás újrapróbálkozás általános kezeli, és vissza a blob szolgáltatás hívása Újrapróbálkozást lehetővé tevő hiba, majd a blob szolgáltatás és a queue szolgáltatás kérelmek csökkenhet.

Végül Ez függ az alkalmazás összetettsége. Előfordulhat, hogy nem kívánja kezelni a hibák szolgáltatás, de ehelyett átirányítása kérések a másodlagos régióba tárolási szolgáltatások és az alkalmazás futtatásához csak olvasható módban, ha az elsődleges régióban bármely társzolgáltatás probléma névváltozást észlel.

### <a name="other-considerations"></a>Egyéb szempontok

Ezek a más ismertetett szempontok alapján ez a cikk a többi ismertetik.

*   Az olvasási kérések az áramköri megszakító minta használatával újrapróbálkozások kezelése

*   Idővel konzisztenssé adatok és a legutóbbi szinkronizálás ideje

*   Tesztelés

## <a name="running-your-application-in-read-only-mode"></a>Az alkalmazás csak olvasható módban fut

RA-GRS storage használatához mindkét sikertelen olvasási kérések kezelésére is képesnek kell lennie, és nem sikerült a frissítési kérelmek (ebben az esetben a Beszúrás tehát frissítés, frissítés és törlés). Ha az elsődleges adatközpont leáll, olvassa el a kérelmek átirányíthatók a másodlagos adatközpontba. Azonban frissítési kérelmek nem irányítható át a másodlagos, mert a másodlagos csak olvasható. Emiatt az alkalmazás futtatásához csak olvasható módban kell.

Megadhatja például, hogy a jelzőt, amely be van jelölve, a frissítési kérelmek Azure Storage elküldése előtt. Ha a frissítési kérelmek egyik, hagyja ki, és térjen vissza az ügyfél egy megfelelő választ. Akkor is érdemes lehet bizonyos funkciókat tilthat le elemet, amíg a probléma megoldódott, és értesítse a felhasználókat, hogy ezek nem átmenetileg nem érhető el.

Ha úgy dönt, hogy az egyes szolgáltatásokhoz hibák kezelésének külön-külön, is szüksége lesz az alkalmazás futtatásához csak olvasható módban szolgáltatás képes kezelni. Például előfordulhat, hogy minden egyes szolgáltatás engedélyezett és letiltott írásvédett jelzők. Ezután a megfelelő helyen jelző képes kezelni a kódban.

Egy másik oldalán juttatás tudnak majd futtassa az alkalmazást csak olvasható módban van –, lehetővé teszi a korlátozott működésének biztosításához a súlyos alkalmazás frissítése során. Az alkalmazás csak olvasható módban fusson, és a másodlagos adatközpontba ponton senki sem fér hozzá az adatokat az elsődleges régióban frissítések létrehozása idejére biztosítása elindítható.

## <a name="handling-updates-when-running-in-read-only-mode"></a>Frissítések kezelése, csak olvasható módban történő futtatásakor

Számos módon frissítés-kérelmeket kezelnek, csak olvasható módban történő futtatásakor. Átfogó azt ne fedje ez, de általában többféle annak a figyelembe venni.

1.  A felhasználó válaszolnak, és közölje vele, jelenleg nem elfogadja frissítések. Például a kapcsolattartási rendszer kihasználva ügyfelek számára a kapcsolattartási adatok eléréséhez, de nem módosításokat.

2.  A frissítések, egy másik régióban is sorba helyezni. Ebben az esetben, akkor a függőben lévő frissítési kérelmek írni a várólista egy másik régióban, és majd tudja feldolgozni ezeket a kérelmeket, miután újra online állapotba az elsődleges adatközpont kerül. Ha ebben a forgatókönyvben hagyja, az ügyfél tudja, hogy a kért frissítés várakozik későbbi feldolgozás céljából.

3.  A frissítések a tárfiók más régióban írhat. Majd ha az elsődleges adatközpont ismét online elérhető, akkor ezek a frissítések egyesítése az elsődleges adatok, attól függően, hogy az adatok szerkezete módot. Például ha a név egy dátum-/ időbélyeg külön fájlt hoz létre, másolhatja azokat a fájlokat vissza az elsődleges régióban a. Ez a módszer egyes munkaterhelések, például a naplózás és az iOT.

## <a name="handling-retries"></a>Az újrapróbálkozások kezelése

Hogyan tudja megállapítani, mely hibák Újrapróbálkozást lehetővé tevő? Ez határozza meg a storage ügyféloldali kódtára. Például a 404-es hiba (az erőforrás nem található) nincs Újrapróbálkozást lehetővé tevő mert az újrapróbálkozás nem valószínűleg sikeres. Másrészről 500 hiba nem Újrapróbálkozást lehetővé tevő, mert a kiszolgáló hibája, és egyszerűen lehet, hogy átmeneti jellegű probléma. További részletekért tekintse meg a [nyissa meg a ExponentialRetry osztály forráskódja](https://github.com/Azure/azure-storage-net/blob/87b84b3d5ee884c7adc10e494e2c7060956515d0/Lib/Common/RetryPolicies/ExponentialRetry.cs) a .NET a storage ügyféloldali kódtára a. (Keresse meg a ShouldRetry metódust.)

### <a name="read-requests"></a>Olvasási kérelmek

Ha az elsődleges storage probléma van a olvasási kérelmek átirányíthatók a másodlagos tárterületre. Szerint azt a fentiekben leírtuk a [idővel azonos adatokat használó](#using-eventually-consistent-data), esetleg az elavult adatokat olvasni az alkalmazás elfogadható kell lennie. A storage ügyféloldali kódtár használatakor RA-GRS adatok eléréséhez egy olvasási kérést újrapróbálkozási viselkedését értékének beállításával megadhatja a **LocationMode** tulajdonságot a következők egyikét:

*   **PrimaryOnly** (alapértelmezés)

*   **PrimaryThenSecondary**

*   **SecondaryOnly**

*   **SecondaryThenPrimary**

Ha a **LocationMode** való **PrimaryThenSecondary**, ha az elsődleges végpont nem tud Újrapróbálkozást lehetővé tevő hiba, az ügyfél számára a kezdeti olvasási kérelem automatikusan küld egy másik olvasási kérelem, hogy a másodlagos végpont. Ha a hiba: a kiszolgáló időkorlátja, az ügyfél lesz Várjon, amíg az időkorlát lejár, mielőtt egy újrapróbálkozást lehetővé tevő hiba kap a szolgáltatástól.

Alapvetően két esetben kell figyelembe venni, hogyan reagáljon a Újrapróbálkozást lehetővé tevő hiba meghatározásakor:

*   Ez egy elkülönített probléma, és az elsődleges végpont későbbi kérelmek nem ad vissza egy újrapróbálkozást lehetővé tevő hiba. Egy példa, ahol ez akkor fordulhat elő, amikor egy átmeneti hálózati hiba.

    Ilyen esetben van nincs jelentős teljesítményét rendelkező **LocationMode** beállítása **PrimaryThenSecondary** , ez csak akkor fordul elő ritkán.

*   Ez a tárolási szolgáltatások az elsődleges régióban közül legalább egy probléma, és az adott szolgáltatáshoz, az elsődleges régióban az összes további kérelmet valószínűleg Újrapróbálkozást lehetővé tevő hibák visszaadása egy ideig. Egy példa erre, ha az elsődleges régióban nem teljesen érhető el.

    Ebben a forgatókönyvben nincs rendszer teljesítményét, mert az olvasási kéréseket fog először próbálja az elsődleges végpont, várjon, amíg az időkorlát lejár, és a másodlagos végponti váltani.

Ezek a forgatókönyvek esetén meg kell határoznia, hogy nincs folyamatban lévő probléma van az elsődleges végpont és a küldési kérelmek közvetlenül a másodlagos végponti úgy, hogy olvassa a **LocationMode** tulajdonságot **SecondaryOnly** . Ilyenkor is módosítania kell az alkalmazás futtatásához csak olvasható módban. Ezt a módszert nevezik a [áramköri megszakító mintát](https://msdn.microsoft.com/library/dn589784.aspx).

### <a name="update-requests"></a>Frissítési kérelmek

Az áramköri megszakító mintát frissítési kérelmek is alkalmazható. Azonban frissítési kérelmek nem irányítható át másodlagos tároló, amely csak olvasható. Az ilyen kérelmeket, akkor hagyja a **LocationMode** tulajdonsága **PrimaryOnly** (alapértelmezett). Kezelje ezeket a hibákat, metrika vonatkoznak ezek a kérelmek – például olyan sorok esetén – 10 hibák, és amikor teljesül a küszöbérték, váltson az alkalmazás csak olvasható módba. Ugyanazokat a módszereket visszaküldésére használatos segítségével frissítési mód, mint az áramköri megszakító mintát kapcsolatban a következő szakaszban az alábbiakban.

## <a name="circuit-breaker-pattern"></a>Áramkör-megszakító minta

Az áramköri megszakító minta az alkalmazásban használt megakadályozhatja egy művelet, amely várhatóan többszöri sikertelen. Lehetővé teszi az alkalmazás tovább futni, hanem idő, amíg a művelet fel a rendszer ismét megkísérli exponenciálisan növekszik. Azt is észleli, ha a hiba kijavítása, amikor az alkalmazás képes próbálja megismételni a műveletet.

### <a name="how-to-implement-the-circuit-breaker-pattern"></a>Az áramköri megszakító mintát implementálása

Azonosítására, hogy van-e az elsődleges végpont folyamatban lévő hiba, megfigyelheti, hogy milyen gyakran az ügyfél Újrapróbálkozást lehetővé tevő hibát észlel. Mivel minden egyes eset különböző, akkor adja meg a másodlagos végponti váltani, és futtassa az alkalmazást csak olvasható módban a döntést a használni kívánt küszöbértéket. Például sikertelen eldöntheti, hajtanak végre a kapcsolót, ha nincs sikeres tartalmazó sor 10 hibák szerepelnek. Egy másik példa egy vált, ha a kérelem egy 2 perces időszak 90 %-a nem.

Az első forgatókönyvben is egyszerűen tároljuk a hibák számát, és ha sikeres a maximális elérése előtt állítsa be a count vissza. A második forgatókönyvben egyik megvalósítása módja használni a MemoryCache objektum (.NET). Az egyes kérelmek egy CacheItem hozzá a gyorsítótárhoz, az érték beállítása sikeres állapotra (1) vagy nem sikerül (0), és a lejárati idő beállítása – 2 percet most (vagy bármilyen az időkorlát értéke). Egy bejegyzést lejárati idő elérésekor a rendszer automatikusan eltávolítja a bejegyzést. Ez biztosítja a működés közbeni 2 perces ablak. Minden alkalommal, amikor Ön indítson egy lekérdezést a társzolgáltatás először segítségével a Linq lekérdezés között a MemoryCache objektum számítja ki a százalékos sikerességi megengedő az értékeket, és a számát elosztjuk. Amikor a százalékos sikerességi bizonyos küszöb (például 10 %) alá csökken, állítsa be a **LocationMode** tulajdonság olvasási kérelem **SecondaryOnly** és írásvédett módban a folytatás előtt az alkalmazást.

Határozza meg, hogy mikor legyen a kapcsoló hibák száma a küszöbérték service szolgáltatás eltérhetnek az alkalmazás, így érdemes lehet minősítené konfigurálható paraméterek. Ez egyben döntheti el, az egyes szolgáltatások Újrapróbálkozást lehetővé tevő hibák kezelésének külön-külön vagy egy, a korábban bemutatott módon.

Meg kell vizsgálni, hogyan legyen kezelve az alkalmazások több példányt, és mi a teendő, ha észleli a Újrapróbálkozást lehetővé tevő hibákat minden egyes példányában. Például előfordulhat, hogy 20 virtuális gép fut betöltött ugyanazt az alkalmazást. Tegye mindegyik példány külön-külön kezeli? Ha egy példány indításakor problémák, szeretné korlátozni a választ csak egy példányhoz, vagy szeretné rendelkezik megpróbálja példányainak ugyanúgy válaszol, amikor egy példány problémát észlelt? A példányok külön kezelése sokkal egyszerűbb, mint a próbál koordinálja a választ ezek között, de ennek módja az alkalmazás architektúra függ.

### <a name="options-for-monitoring-the-error-frequency"></a>Figyelheti a gyakoriságát

Három fő lehetőség az elsődleges régióban újrapróbálkozások gyakorisága figyelés ahhoz, hogy mikor átválthat a másodlagos régióba, és módosítsa az alkalmazás futtatásához csak olvasható módban van.

*   Adja hozzá a kezelőt a [ **újrapróbálkozás** ](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.operationcontext.retrying.aspx) az esemény a [ **OperationContext** ](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.operationcontext.aspx) objektumot adja meg a Storage-kérelmek – Ez a mód Ebben a cikkben jelenik meg, és a hozzá tartozó mintában. Ezeket az eseményeket az érvényesítést, amikor az ügyfél újrapróbálkozik a kérelmet, engedélyezése, hogy nyomon kövesse, milyen gyakran az az ügyfél az elsődleges végpont Újrapróbálkozást lehetővé tevő hibát észlel.

    ```csharp 
    operationContext.Retrying += (sender, arguments) =>
    {
        // Retrying in the primary region
        if (arguments.Request.Host == primaryhostname)
            ...
    };
    ```

*   Az a [ **Evaluate** ](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.retrypolicies.iextendedretrypolicy.evaluate.aspx) egyéni újrapróbálkozási házirendje metódust, bármikor futtatható, egyéni kód ha ismételt próbálkozással kerül sor. Ha ismételt próbálkozással rögzítése mellett történik, ez is lehetővé teszi a újrapróbálkozásra lehetőséget.

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

*   A harmadik módszer, hogy olyan egyéni figyelő-összetevőt alkalmazza az alkalmazás, amely folyamatosan Pingeli az olvasási kérések (például Olvasás kis blob) helyőrző az elsődleges tárolási végpontjának állapotának meghatározásához. A volna tarthat néhány forrás, de nem jelentős időt. Ha a probléma, amely eléri a küszöbérték felderített, majd hajtaná végre a kapcsolót, hogy **SecondaryOnly** és írásvédett módban.

Egy bizonyos ponton érdemes váltson vissza az elsődleges végpont használatával, és lehetővé teszi a frissítések. Ha a fenti első két módszer egyikével sikerült egyszerűen visszavált az elsődleges végpont, engedélyezze a frissítési mód tetszőlegesen kiválasztott mennyi idő vagy a műveletek végrehajtását követően. Majd engedélyezheti azt az újrapróbálkozási logika keresztül nyissa meg újra. Ha a probléma kijavítása, továbbra is fogja használni az elsődleges végpontot, és a frissítések engedélyezése. Ha a probléma továbbra is van, azt még egyszer vált vissza a másodlagos végponti és írásvédett módban után a feltételek beállítása sikertelen.

A harmadik eset, ha az elsődleges tárolási végpont pingelés sikeres ismét válik, indíthat el a kapcsoló vissza **PrimaryOnly** , és folytassa a frissítések engedélyezése.

## <a name="handling-eventually-consistent-data"></a>Idővel konzisztenssé adatok kezelése

Az RA-GRS úgy működik, hogy az elsődleges régióból a másodlagosba replikálja a tranzakciókat. A replikálási folyamat biztosítja, hogy az adatok a másodlagos régióban *idővel konzisztenssé*. Ez azt jelenti, hogy az elsődleges régióban lévő összes tranzakció végül megjelenik a másodlagos régióban, de lehet egy lag csak akkor jelennek meg, és, hogy nincs-e a tranzakciók érkeznek, ugyanabban a sorrendben, mint amelyben a másodlagos régióban garancia azok eredetileg volt alkalmazva az elsődleges régióban. Ha a tranzakciók sorrendje, nem a másodlagos régióban érkeznek meg *előfordulhat, hogy* fontolja meg az adatok inkonzisztens állapotban lesz, amíg a szolgáltatás ki, hogy a másodlagos régióban.

Az alábbi táblázat egy példát, hogy mi történne, ügyeljen rá, hogy tagja alkalmazotti részletek frissítésekor a *rendszergazdák* szerepkör. Ebben a példában az ehhez szükséges frissítenie a **alkalmazott** entitás és a frissítés egy **rendszergazdai szerepkör** entitás a számával, a rendszergazdák teljes száma. Figyelje meg, hogy a frissítések alkalmazása nem megfelelő sorrendben másodlagos régióban.

| **Idő** | **Transaction**                                            | **Replikáció**                       | **Utolsó szinkronizálás** | **eredménye** |
|----------|------------------------------------------------------------|---------------------------------------|--------------------|------------| 
| T0       | Tranzakció A: <br> Alkalmazott beszúrása <br> az elsődleges entitás |                                   |                    | Az elsődleges, szúrja be A tranzakció<br> még nem replikált. |
| T1       |                                                            | A tranzakció <br> replikált<br> másodlagos | T1 | Tranzakció A másodlagos replikálja. <br>Utolsó szinkronizálás ideje frissíteni.    |
| T2       | B tranzakció<br>Frissítés<br> alkalmazott entitás<br> az elsődleges  |                                | T1                 | Tranzakció B elsődleges, írása<br> még nem replikált.  |
| T3       | Tranzakció C:<br> Frissítés <br>Rendszergazda<br>a szerepkör entitás<br>elsődleges |                    | T1                 | Tranzakció elsődleges, írása C<br> még nem replikált.  |
| *T4*     |                                                       | Tranzakció C <br>replikált<br> másodlagos | T1         | C másodlagos replikált tranzakció.<br>Nincs frissítve, mert LastSyncTime <br>B tranzakció még nincs replikálva.|
| *T5*     | Olvassa el az entitások <br>másodlagos                           |                                  | T1                 | Alkalmazott elavult értékének beolvasása <br> entitás, mert a tranzakció B nem <br> még replikált. Az új értéket kap<br> rendszergazdai szerepkör entitás mert C<br> a rendszer replikálja. Utolsó szinkronizálás ideje még nem<br> lett frissítése, mert a tranzakció B<br> a rendszer nem replikálja. Beállíthatja a<br>rendszergazdai szerepkör entitás inkonzisztens. <br>mivel az entitás dátum/idő után <br>a legutóbbi szinkronizálás ideje. |
| *T6*     |                                                      | B tranzakció<br> replikált<br> másodlagos | T6                 | *T6* – C – az összes tranzakció van <br>van replikálva, a legutóbbi szinkronizálás ideje<br> frissül. |

Ebben a példában feltételezzük, T5 másodlagos régióban olvasási vált az ügyfél. Sikeresen tudja olvasni a **rendszergazdai szerepkör** jelenleg entitás, de az entitást a rendszergazdák számát, amely nem megfelelő számú értéket tartalmaz **alkalmazott** , amelyek entitások a másodlagos régióban rendszergazdák jelölésű most. Az ügyfél az értéket, és annak kockázata, hogy a rendszer inkonzisztens adatokat egyszerűen jeleníti meg. Alternatív megoldásként az ügyfél kísérletet határozza meg, amely a **rendszergazdai szerepkör** a potenciálisan inkonzisztens állapotban van, mert a frissítések sorrendje nem történt, és ezután megadja az erről a felhasználó.

Ismeri fel, hogy vélhetően ellentmondó adatokat tartalmaz, az ügyfél által használt értékének a *utolsó szinkronizálás* , hogy kaphat a bármikor a társzolgáltatás lekérdezésével. Igen, akkor a időpontja az adatokat a másodlagos régióban utolsó konzisztens, és ha a szolgáltatás kellett alkalmazni előtt, az adott pont a tranzakciók időben. A fenti példában, miután a szolgáltatás által a a **alkalmazott** másodlagos régióban, a legutóbbi szinkronizálás ideje entitás értéke *T1*. Az értéken marad *T1* csak a szolgáltatásfrissítések a **alkalmazott** a másodlagos régióban, ha entitás *T6*. Ha az ügyfél lekéri a legutóbbi szinkronizálás ideje, ha olvassa be a következő entitás *T5*, azt is összehasonlíthatja az entitás időbélyegzőjét. Az entitás időbélyegzőjét későbbre esik, mint a legutóbbi szinkronizálás ideje, ha az entitás potenciálisan inkonzisztens állapotban van, majd, és is igénybe vehet, függetlenül a az alkalmazás megfelelő művelet. Ez a mező használatához arról, hogy ha az elsődleges az utolsó frissítés befejeződött.

## <a name="testing"></a>Tesztelés

Fontos, hogy az alkalmazás viselkedik-e a várt Újrapróbálkozást lehetővé tevő hiba teszteléséhez. Például tesztelni kell, hogy az alkalmazás a másodlagos, és csak olvasható módban, ha problémát észlel, és vált, váltás amikor ismét elérhetővé az elsődleges régióban válik. Ehhez meg kell egy újrapróbálkozást lehetővé tevő hibák, és milyen gyakran előforduló vezérlő szimulálásához.

Használhat [Fiddler](http://www.telerik.com/fiddler) elfogására és módosítsa a parancsfájl a HTTP-válaszokat. A parancsfájl azonosítsa az elsődleges végpont érkező válaszokat, és a HTTP-állapotkód módosítása, amely a Storage ügyféloldali kódtár – Újrapróbálkozást lehetővé tevő hiba felismeri. A kódrészletet mutat be egy egyszerű a Fiddler parancsfájlt, amely elfogja a válaszok az olvasási kérésekre szemben a **employeedata** tábla visszatérési 502 állapotot:

```java
static function OnBeforeResponse(oSession: Session) {
    ...
    if ((oSession.hostname == "\[yourstorageaccount\].table.core.windows.net")
      && (oSession.PathAndQuery.StartsWith("/employeedata?$filter"))) {
        oSession.responseCode = 502;
    }
}
```

Ebben a példában a kérelmek szélesebb körének elfogására és annak csak módosítása lehetett bővíteni a **responseCode** néhány olyan valós forgatókönyv jobban szimulálása. Fiddler parancsfájlok testreszabásával kapcsolatos további információkért lásd: [módosítását olyan kérésre vagy válaszra](http://docs.telerik.com/fiddler/KnowledgeBase/FiddlerScript/ModifyRequestOrResponse) a Fiddler dokumentációjában.

Amennyiben végrehajtotta az alkalmazás konfigurálható csak olvasható módra vált küszöbértékeit, akkor könnyebb lesz a viselkedés nem éles tranzakció kötetekkel tesztelése.

## <a name="next-steps"></a>További lépések

* Olvasási hozzáférés-Georedundancia kapcsolatos további információkért egy másik példa lehet megadni a LastSyncTime, beleértve talál [Windows Azure tárolási redundancia lehetőségek és az írásvédett Georedundáns tárolás](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

* Egy teljes mintát, hogyan végezheti el a kapcsoló oda-vissza az elsődleges és másodlagos végpontok közötti megjelenítő, lásd: [Azure-minták – az áramköri megszakító minta használatával az RA-GRS tárolással rendelkező](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs).
