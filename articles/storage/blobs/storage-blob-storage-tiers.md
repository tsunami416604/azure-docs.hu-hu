---
title: Az Azure blobtárolási rétege gyakran és ritkán használt, valamint archivált adatokhoz | Microsoft Docs
description: A gyakran és a ritkán használt, valamint az archivált adatok tárolása Azure Storage-fiókok esetében.
services: storage
author: kuhussai
ms.service: storage
ms.topic: article
ms.date: 09/11/2018
ms.author: kuhussai
ms.component: blobs
ms.openlocfilehash: 66c47a97eee6759eb963db43d5c573fb6612bde6
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2018
ms.locfileid: "45735918"
---
# <a name="azure-blob-storage-hot-cool-and-archive-storage-tiers"></a>Azure Blob Storage: A gyakori és ritka elérésű, valamint az archív tárolási szintek

## <a name="overview"></a>Áttekintés

Az Azure Storage három tárolási szintet kínál a Blob-objektumok tárolásához, hogy adatait a legköltséghatékonyabb módon tárolhassa a használat függvényében. Az Azure **gyakori elérésű tárolási szintje** a gyakran használt adatok tárolására van optimalizálva. Az Azure **ritka elérésű tárolási szintje** a ritkábban használt adatok legkevesebb 30 napi tárolására van optimalizálva. Az Azure **archív tárolási szintje** az alig használt adatok legkevesebb 180 napi, rugalmas (órákban mért) késési követelményekkel rendelkező tárolására van optimalizálva. Az archív tárolási szint csak a blobok szintjén érhető el, a tárfiók szintjén nem. A ritka elérésű tárolási szinten lévő adatok esetében ugyan alacsonyabb rendelkezésre állás is elegendő lehet, azonban továbbra is magas tartósság és a gyakran használt adatokkal egyező hozzáférési idő és teljesítmény szükséges. A ritkán használt adatok esetében a valamelyest alacsonyabb rendelkezésre állási szolgáltatási szintek és a gyakran használt adatokhoz képest magasabb hozzáférési költségek elfogadható kompromisszumot jelentenek az alacsonyabb tárolási költségek ellenében. Az archív tárolás offline, és ez jár a legalacsonyabb tárolási, de ezzel párhuzamosan a legmagasabb hozzáférési költségekkel. A fiók szintjén csak a gyakori és a ritka elérésű tárolási szintet (nem archív) lehet beállítani. Az objektumok szintjén mindhárom szintet be lehet állítani.

Napjainkban a felhőben tárolt adatok mennyisége exponenciálisan nő. A növekvő tárolási szükségletek költségeinek kezelése érdekében hasznos lehet az adatokat olyan attribútumok alapján szervezni, mint a hozzáférés gyakorisága vagy a tervezett megőrzési időtartam, így optimalizálhatók a költségek. A felhőben tárolt adatok az előállítás, a feldolgozás és a hozzáférés tekintetében különbözőek lehetnek az élettartamuk során. Egyes adatokat aktívan használnak és módosítanak teljes élettartamuk során. Egyes adatokat élettartamuk korai szakaszában sokat használnak, az adatok életkorának növekedésével azonban a hozzáférések mennyisége drasztikusan csökken. Egyes adatok pedig inaktívan a felhőben maradnak, és a tárolást követően csak nagyon ritkán használják őket, ha használják őket egyáltalán.

Az egyes adathozzáférési forgatókönyvek esetében számos előnyt biztosít az olyan, szintekre osztott tárolási megoldás, amely egy adott hozzáférési mintára van optimalizálva. A gyakori és a ritka elérésű, valamint az archív tárolási szintekkel az Azure Blob Storage a különböző tárolási igényeket célozza meg, különböző árképzési modellekkel.

## <a name="storage-accounts-that-support-tiering"></a>Rétegezést támogató Storage-fiókok

Az objektum tárolási adataihoz csak a Blob Storage- vagy a General Purpose v2 (GPv2) (általános célú) fiókokban használhatja a gyakori, ritka elérésű, vagy archív tárolási szinteket. General Purpose v1 (GPv1) fiókok nem támogatják a rétegezést. Az ügyfelek azonban könnyedén, egyetlen kattintással átalakíthatják meglévő GPv1- vagy Blob Storage-fiókjukat GPv2-fiókká az Azure Portalon. A GPv2 új díjszabási struktúrát nyújt a blobokhoz, fájlokhoz és üzenetsorokhoz, továbbá hozzáférést biztosít különféle egyéb, új tárolási szolgáltatásokhoz is. Továbbá, a későbbiek során egyes új szolgáltatások és árengedmények csak a GPv2-fiókok esetében lesznek elérhetők. Az ügyfeleknek tehát érdemes megfontolni a GPv2 fiókok használatát. A használatot azonban csak akkor kezdjék meg, ha áttekintették az összes szolgáltatás díjszabását. Egyes számítási feladatok ugyanis drágábbak lehetnek GPv2-fiókokon, mint a GPv1-fiókok esetében. További információkért lásd: [az Azure storage-fiók áttekintése](../common/storage-account-overview.md).

A Blob Storage- és GPv2-fiókokban fiókszinten elérhető a **Hozzáférési szint** attribútum, amellyel megadható, hogy a tárfiók alapértelmezett szintje gyakori vagy ritka elérésű legyen minden olyan, a tárfiókban található blob esetében, amelyhez nincs az objektumok szintjén beállított szint. Azon objektumok esetében, amelyek szintje az objektumok szintjén van megadva, a fiókszint nem lesz érvényes. Az archív szint csak az objektumok szintjén alkalmazható. Bármikor válthat a tárolási szintek között.

## <a name="hot-access-tier"></a>Gyakran használt adatok hozzáférési szintje

Egy gyakran használt tároló tárolási költsége magasabb, mint a ritkán használt vagy archív tárolóké, de ennek a legalacsonyabb a hozzáférési költsége. Példa használati forgatókönyvek a gyakori elérésű tárolási szinthez:

* Adatok, amelyeket aktívan használnak, vagy amelyekhez várhatóan gyakran szeretnének hozzáférni (olvasás vagy írás formájában).
* A feldolgozásra és a későbbiekben a ritka elérésű tárolási szintre való áttelepítésre előkészített adatok.

## <a name="cool-access-tier"></a>Ritkán használt adatok hozzáférési szintje

A ritka elérésű tárolási szint tárolási költsége alacsonyabb, a hozzáférési költsége viszont magasabb, mint a gyakori elérésű szinté. Ezen a szinten olyan adatokat érdemes tárolni, amelyek legalább 30 napig maradnak a ritka elérésű szinten. Példa használati forgatókönyvek a ritka elérésű tárolási szinthez:

* Rövid távú biztonsági mentési és vészhelyreállítási adatkészletek.
* Régebbi, már csak ritkán megtekintett médiatartalmak, amelyek elérésére igény esetén azonban azonnal szükség van.
* Nagyobb adatkészletek, amelyeket költséghatékonyan kell tárolni, amíg a későbbi feldolgozáshoz szükséges többi adat gyűjtése még folyamatban van. (*Például* tudományos adatok vagy gyártási létesítményből származó nyers telemetriaadatok hosszú távú tárolása)

## <a name="archive-access-tier"></a>Archivált adatok hozzáférési szintje

Az archív tároló a gyakran és a ritkán használt adatok tárolásához képest a legalacsonyabb tárolási költségekkel, viszont magasabb adatlekérési költségekkel rendelkezik. Ezen a szinten olyan adatokat érdemes tárolni, amelyek legalább 180 napig maradnak az archív szinten, és amelyeknél nem okoz gondot a lekérés több órás késése.

Az archív tárolóban lévő blobok offline állapotban vannak, nem olvashatók (az online állapotban lévő és hozzáférhető metaadatok kivételével), nem másolhatók, valamint nem írhatók felül és nem módosíthatók. Az archív tárolóban lévő blobokról pillanatképek sem készíthetők. A törlés, listázás, a blobtulajdonságok/metaadatok lekérése vagy a blob tárolási szintjének módosítása azonban elvégezhető.

Példa használati forgatókönyvek az archív tárolási szinthez:

* Hosszú távú biztonsági mentések, másodlagos biztonsági mentések és archiválási adatkészletek
* Eredeti (nyers) adatok, amelyeket a végső használható formába való feldolgozásukat követően is meg kell őrizni. (*Például* nyers médiafájlok a más formátumba való átkódolásukat követően)
* Megfelelőségi és archiválási adatok, amelyeket hosszú ideig kell tárolni, azonban nagyon ritkán kell hozzáférni. (*Például* biztonsági kamerák felvételei, egészségügyi intézmények régi röntgen-/MRI-felvételei, pénzügyi szolgáltatók ügyfélszolgálati hívásainak hangfelvételei és átiratai)

### <a name="blob-rehydration"></a>Blob rehidratálása
Az archív tárolóban lévő adatok olvasásához előbb módosítania kell a blob szintjét a gyakran vagy ritkán használt adatok tárolási szintjére. Ezt a folyamatot rehidratálásnak nevezik, és akár 15 órát is igénybe vehet. Nagy méretű blobok használata akkor javasolt, az optimális teljesítmény érdekében. Több kis méretű blob egyidejűleg történő rehidratálása további időt vehet igénybe.

A rehidratálás során a blob **Archív állapot** tulajdonságának ellenőrzésével bizonyosodhat meg arról, hogy a szint módosítása befejeződött-e. Az állapot a célszinttől függően „rehydrate-pending-to-hot” (rehidratálás-folyamatban-a-gyakoriba) vagy „rehydrate-pending-to-cool” (rehidratálás-folyamatban-a-ritkába) lehet. A folyamat befejeztével a „archív állapot” tulajdonság törlődik, és a „hozzáférési szint” tulajdonság mutatja, hogy az új szint a gyakran vagy a ritkán használt szint-e.  

## <a name="blob-level-tiering"></a>Blobszintű rétegezés

A blobszintű rétegezés segítségével az adatok szintje egyetlen művelet, a [Blobszint beállítása](/rest/api/storageservices/set-blob-tier) használatával módosítható az egyes objektumok szintjén. A blobok hozzáférési szintje a használati forgatókönyvek változásával könnyen állítható a gyakran és ritkán használt, valamint az archív szintek között anélkül, hogy az adatokat át kellene helyezni egyik fiókból a másikba. Minden szint módosításának azonnali fordulhat elő. Azonban a blob archív szintről rehidratálása több óráig is tarthat. 

Az utolsó blobszint-módosítás időpontja a **Hozzáférési szint utolsó módosítása** blobtulajdonságon keresztül érhető el. Ha egy blob az archív szinten van, akkor nem lehet felülírni, ezért ugyanennek a blobnak a feltöltése nem engedélyezett ebben a forgatókönyvben. A blobok, amelyben eset az új blob örökli a felül lett írva a blob szintjét gyors vagy lassú elérésű szint is írja felül.

Egy adott fiók tartalmazhat blobokat egyszerre akár mindhárom szinten is. Azok a blobok, amelyekhez nincs kifejezetten hozzárendelve szint, a fiók hozzáférési szintjének beállítását veszik át. Ha a hozzáférési szint a fióktól vette a beállításait a fiók, akkor tekintse meg a **hozzáférési szint következtetett** blobtulajdonság beállítása "true", és a blob **hozzáférési szint** blobtulajdonság megfelel a fiókszintjének. Az Azure Portalon a hozzáférési szint hozott tulajdonsága a blob hozzáférési szintjével együtt jelenik meg (például Gyakori (következtetett) vagy Ritka (következtetett)).

> [!NOTE]
> Az archív tárolás és a blobszintű rétegezés csak a blokkblobokat támogatja. A pillanatképeket tartalmazó blokkblob szintje sem módosítható.

### <a name="blob-lifecycle-management"></a>BLOB életciklus-felügyelete
A BLOB Storage életciklus-felügyelet (előzetes verzió) kínál egy gazdag, szabályalapú házirend, amelyet használhat való áttérés a adatait, hogy a legjobb hozzáférési szint és az adatok életciklusa végén lejár. Lásd: [kezelése az Azure Blob storage életciklus](https://docs.microsoft.com/en-us/azure/storage/common/storage-lifecycle-managment-concepts) további.  

### <a name="blob-level-tiering-billing"></a>Blobszintű rétegezési számlázás

Amikor egy blob egy írásként kerül (gyakori elérésű -> ritka elérésű, gyakori elérésű -> archív vagy ritka elérésű -> archív), a művelet lesz számlázva, a cél szintre, ahol a célszintű írási műveleteire (10.000 műveletenként) és adatírásaira (gigabájtonként) vonatkozó díjak a cél szintjének alkalmazása írási művelet. Ha egy blob egy gyakrabban használt szintre kerül (Archív->Ritka elérésű, Archív->Gyakori elérésű vagy Ritka elérésű->Gyakori elérésű), a műveletet a rendszer a forrásszintről olvasásként számlázza, amely esetében a forrásszint olvasási műveleteire (10 000 műveletenként) és adatlekéréseire (GB-onként) vonatkozó díjak érvényesek.

Ha átváltja a fiókszintet gyakori elérésűről ritka elérésűre, csak a GPv2-fiókok esetében lesznek díjkötelesek a beállított szinttel nem rendelkező blobok esetében végrehajtott írási műveletek (10.000 műveletenként). Nem jár költséggel a módosítás a Blob storage-fiókok. Ha Blob Storage- vagy GPv2-fiókját ritka elérésűről gyakori elérésűre módosítja, az olvasási műveletek (10 000 műveletenként) és adatlekérések (GB-onként) után díjat számolunk fel. A ritka elérésű vagy archív szintről áthelyezett blobok esetében korai törlésre vonatkozó díjak lehetnek érvényesek.

### <a name="cool-and-archive-early-deletion"></a>Korai törlés a ritka elérésű és az archív szinten

A GB-alapú és havi díjak mellett minden, a ritka elérésű szintre áthelyezett blobra (csak GPv2-fiókok esetében) 30 napos ritka elérésű korai törlési időszak, az archív szintre áthelyezett blobokra pedig 180 napos archív korai törlési időszak vonatkozik. A díj számlázása időarányosan történik. Ha például áthelyez egy blobot az archív szintre, majd 45 nap után törli vagy áthelyezi azt a gyakori elérésű szintre, 135 (180 mínusz 45) napnyi archív tárolásnak megfelelő korai törlési díjat számolunk fel.

## <a name="comparison-of-the-storage-tiers"></a>A tárolási szintek összehasonlítása

Az alábbi táblázat a gyakori és ritka elérésű, valamint archív tárolási szintet hasonlítja össze.

| | **Gyakori elérésű tárolási szint** | **Ritka elérésű tárolási szint** | **Archív tárolási szint**
| ---- | ----- | ----- | ----- |
| **Rendelkezésre állás** | 99.9% | 99% | N/A |
| **Rendelkezésre állás** <br> **(RA-GRS olvasások)**| 99.99% | 99.9% | N/A |
| **Használati díjak** | Magasabb tárolási, alacsonyabb hozzáférési és tranzakciós költségek | Alacsonyabb tárolási, magasabb hozzáférési és tranzakciós költségek | Legalacsonyabb tárolási, legmagasabb hozzáférési és tranzakciós költségek |
| **Minimális objektumméret** | N/A | N/A | N/A |
| **Minimális tárolási időtartam** | – | 30 nap (csak GPv2 esetében) | 180 nap
| **Késés** <br> **(Az első bájtig eltelt idő)** | ezredmásodperc | ezredmásodperc | < 15 óra
| **Méretezhetőségi és teljesítménycélok** | Ugyanazok, mint az általános célú tárfiókok esetében | Ugyanazok, mint az általános célú tárfiókok esetében | Ugyanazok, mint az általános célú tárfiókok esetében |

> [!NOTE]
> A Blob Storage-fiókok ugyanazokat a méretezhetőségi és teljesítménycélokat támogatják, mint az általános célú tárfiókok. További információkért lásd: [Azure Storage Scalability and Performance Targets](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) (Az Azure Storage méretezhetőségi és teljesítménycéljai).

## <a name="quickstart-scenarios"></a>Rövid útmutatóul szolgáló forgatókönyvek

Ebben a szakaszban a következő forgatókönyveket mutatjuk be az Azure Portal használatával:

* GPv2- vagy Blob Storage-fiók alapértelmezett hozzáférési szintjének módosítása.
* GPv2- vagy Blob Storage-fiókban található blob szintjének módosítása.

### <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>GPv2- vagy Blob Storage-fiók alapértelmezett hozzáférési szintjének módosítása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. A tárfiókjának eléréséhez válassza az Összes erőforrás lehetőséget, majd válassza ki a tárfiókját.

3. A Beállítások panelen kattintson a **Konfiguráció** elemre a fiók konfigurációjának megtekintéséhez és/vagy megváltoztatásához.

4. Válassza ki az igényeinek megfelelő tárolási szintet: a **Hozzáférési szint** beállításnál válassza a **Ritka** vagy a **Gyakori** lehetőséget.

5. Kattintson a panel tetején lévő Mentés elemre.

### <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>GPv2- vagy Blob Storage-fiókban található blob szintjének módosítása.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. A tárfiókban található blobja eléréséhez válassza az Összes erőforrás lehetőséget, majd válassza ki a tárfiókot, a tárolót és végül a blobot.

3. A Blob tulajdonságai panelen kattintson a **Hozzáférési szint** legördülő menüre, és válassza a **Gyakori**, **Ritka** vagy **Archív** tárolási szintet.

5. Kattintson a panel tetején lévő Mentés elemre.

## <a name="pricing-and-billing"></a>Árak és számlázás

Az összes storage-fiókok árképzési modellt alkalmaznak a Blob minden egyes blob szintjének alapján. Tartsa szem előtt az alábbi számlázási szempontok:

* **Tárolási költségek**: Az adattárolás díja a tárolt adatok mennyisége mellett a tárolási szinttől függ. A gigabájtonkénti költség csökken, ha a szint ritkábban használt adatokat tárol.
* **Adathozzáférési költségek**: az adathozzáférési költségek emelkednek, ha a szint ritkábban használt adatokat tárol. A ritka elérésű és az archív tárolási szint esetében gigabájtonkénti adathozzáférési díjat kell fizetni az adatolvasásokért.
* **Tranzakciós költségek**: Minden szint esetében tranzakciónkénti díjat kell fizetni, ez emelkedik, ha a szint ritkábban használt adatokat tárol.
* **Georeplikációs adatátviteli költségek**: Ez csak a georeplikációval konfigurált fiókok esetében érvényes, beleértve a GRS-t és az RA-GRS-t. A georeplikációs adatátvitel gigabájtonkénti díj ellenében érhető el.
* **Kimenő adatátviteli költségek**: A kimenő adatátvitel (azaz az adott Azure-régióból kivitt adatok) esetében gigabájtalapú sávszélesség-használati díjak lépnek fel, csakúgy, mint az általános célú tárfiókok esetében.
* **A tárolási szint módosítása**: a fiók tárolási szintjének a ritka elérésű szintről gyakori elérésűre váltása esetében felmerülő díj megegyezik a tárfiókban lévő összes adat beolvasásának költségével. A fiók tárolási szintjének gyakori elérésűről ritka elérésűre való váltása esetében felmerülő díj viszont az összes adat ritka elérésű szintre írásának költségével egyezik meg (csak a GPv2-fiókok esetében).

> [!NOTE]
> A Blob storage-fiókok díjszabása kapcsolatos további információkért lásd: [Azure Storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage/) lapot. A kimenő adatátviteli díjakkal kapcsolatos további információért lásd az [adatátviteli díjszabást](https://azure.microsoft.com/pricing/details/data-transfers/) ismertető lapot.

## <a name="faq"></a>GYIK

**Blob Storage vagy GPv2-fiókokat kell használnom, ha rétegezni szeretném az adataimat?**

Javasoljuk, hogy Blob Storage-fiókok helyett használjon GPv2-fiókokat a rétegzéshez kialakításához. A GPv2 a Blob Storage-fiókok által támogatott szolgáltatások mellett sok mást is támogat. A Blob Storage és GPv2-fiókok díjszabása majdnem teljesen megegyezik, azonban egyes új szolgáltatások és árengedmények csak GPv2-fiókokhoz lesznek elérhetők. A GPv1-fiókok nem támogatják a rétegezést.

A GPv1- és GPv2-fiókok díjszabási struktúrája eltér egymástól, ezért az ügyfeleknek érdemes mindkettőt alaposan áttekinteni, mielőtt a GPv2-fiókok használata mellett döntenek. Meglévő Blob Storage- vagy GPv1-fiókját könnyedén, egyetlen kattintással átalakíthatja GPv2-fiókká. További információkért lásd: [az Azure storage-fiók áttekintése](../common/storage-account-overview.md).

**Tárolhatok objektumokat mindhárom (gyakori és ritka elérésű, valamint archív) tárolási szinten egyazon fiók esetében?**

Igen. A **Hozzáférési szint** attribútum a fiók szintjén beállított alapértelmezett szint, amely a fiókban lévő összes olyan objektumra vonatkozik, amelyhez nem állítottak be szintet. A Blobszintű rétegezés szolgáltatással azonban külön megadhatja a hozzáférési szintet az egyes objektumokhoz, függetlenül attól, hogy a fiókon milyen hozzáférési szint van beállítva. Egy adott fiók tartalmazhat blobokat egyszerre akár mindhárom (gyakori, ritka és archív) szinten is.

**Módosíthatom a Blob Storage- vagy GPv2-tárfiókom alapértelmezett tárolási szintjét?**

Igen, az alapértelmezett tárolási szintet úgy változtathatja meg, hogy a tárfiókban megadja a **Hozzáférési szint** attribútum kívánt értékét. A tárolási szint módosítása az adott fiókban lévő összes olyan objektumra vonatkozik, amelyhez nincs külön szint beállítva. A tárolási szint gyakoriról ritka elérésű szintre váltása költségekkel jár az írási műveletek (10 000 műveletenként) után (csak a GPv2-fiókokban található, beállított szinttel nem rendelkező blobok esetén). A tárolási szint ritkáról gyakori elérésű szintre váltása költségekkel jár az olvasási műveletek (10 000 műveletenként) és adatlekérések (GB-onként) után is a Blob Storage és GPv2 fiókok összes blobja esetén.

**Beállíthatom a fiók alapértelmezett hozzáférési szintjét archív szintre?**

Nem. Csak a gyakori és ritka elérésű tárolási szint állítható be a fiók alapértelmezett hozzáférési szintjének. Az archív szint csak az objektumok szintjén állítható be.

**Melyik régiókban érhető el a gyakori és ritka elérésű, valamint az archív tárolási szint?**

A gyakori és ritka elérésű tárolási szint, illetve a blobszintű rétegezés minden régióban elérhető. Az archív tárolási szint eleinte csak bizonyos régiókban lesz elérhető. A teljes listát a [Régiónként elérhető Azure-termékek](https://azure.microsoft.com/regions/services/) című részben tekintheti meg.

**A gyakori elérésű tárolási szinten és a ritka elérésű tárolási szinten eltérően viselkednek a blobok?**

A gyakori elérésű tárolási szinten a blobok ugyanolyan késéssel rendelkeznek, mint a GPv1-, GPv2- és Blob Storage-fiókok esetében. A ritka elérésű tárolási szinten a blobok hasonló késéssel rendelkeznek (ezredmásodpercben), mint a GPv1-, GPv2- és Blob Storage-fiókokban lévő blobok. Az archív tárolási szinten lévő blobok esetében több óra késés tapasztalható GPv1-, GPv2- és Blob Storage-fiókok esetében.

A ritka elérésű tárolási szinten a blobok rendelkezésre állási szolgáltatási szintje (SLA) kissé alacsonyabb, mint a gyakori elérésű tárolási szinten. További információt a [tárolók rendelkezésreállási szolgáltatási szintjeit](https://azure.microsoft.com/support/legal/sla/storage/v1_2/) ismertető szakaszban talál.

**A műveletek megegyeznek a gyakori és a ritka elérésű, valamint az archív tárolási szint esetében?**

Igen. A gyakori és a ritka elérésű szint esetében minden művelet teljesen megegyezik. Az érvényes archív tárolási szintű műveletek, mint például a törlés, listázás, blobtulajdonságok/metaadatok lekérése, valamint a blob szintjének megadása teljesen megegyezik a gyakori és ritka elérésű szint műveleteivel. Az archív tárolási szinten lévő blobok nem olvashatók vagy módosíthatók.

**Amikor az archív tárolási szintről rehidratálok egy blobot a gyakori vagy ritka elérésű szintre, honnan tudom, hogy befejeződött-e a rehidratálás?**

A rehidratálás során a blobtulajdonságok lekérése művelettel kérdezheti le az **archív állapot** attribútumot, amelynek segítségével ellenőrizheti, hogy befejeződött-e a szintváltás. Az állapot a célszinttől függően „rehydrate-pending-to-hot” (rehidratálás-folyamatban-a-gyakoriba) vagy „rehydrate-pending-to-cool” (rehidratálás-folyamatban-a-ritkába) lehet. A folyamat befejeztével a „archív állapot” tulajdonság törlődik, és a „hozzáférési szint” tulajdonság mutatja, hogy az új szint a gyakran vagy a ritkán használt szint-e.  

**Egy blob szintjének beállítása után mikor kezdődik a megfelelő díjak alapján történő számlázás?**

Minden egyes blob van a blob blobtulajdonságban megjelölt szint alapján történik **hozzáférési szint** tulajdonság. Amikor egy BLOB egy új szint a **hozzáférési szint** tulajdonság azonnal minden váltás esetében az új szintet tükrözi. Azonban a blob az archív tárolási szintről a gyakori elérésű vagy ritka elérésű szintre való rehidratálása több óráig is tarthat. Ebben az esetben akkor is archív díjak amíg rehidratálás be nem fejeződik, ekkor a **hozzáférési szint** tulajdonságot az új szintet tükrözi. Ezen a ponton számlázzuk ki, hogy a blob, a gyors vagy lassú elérésű díj.

**Hogyan állapíthatom meg, hogy egy blob ritka elérésű vagy archív tárolási szintről való törlése vagy áthelyezése során van-e korai törlési díj?**

Minden blob, amelyet a ritka elérésű (csak GPv2-fiókok esetében) vagy archív tárolási szintről töröl vagy helyez át a vonatkozó 30, illetve 180 nap letelte előtt, a korai törléseknek megfelelő, időarányos költségeket von maga után. Megadhatja, hogy mennyi ideig blob lett a ritka elérésű vagy archív tárolási szinten ellenőrzésével a **hozzáférési szint módosításának ideje** blob-tulajdonság, amely biztosítja az utolsó szintmódosítás időbélyegét. További részletekért lásd: [Korai törlés ritka elérésű vagy archív szint esetében](#cool-and-archive-early-deletion).

**Melyik Azure-eszközök és SDK-k támogatják a blobszintű rétegezést és az archív tárolási szintet?**

Az Azure Portal, a PowerShell, valamint a parancssori felület eszközei, illetve a .NET-, Java-, Python- és Node.js-ügyfélkönyvtárak mind támogatják a blobszintű rétegezést és az archív tárolási szintet.  

**Mennyi adatot tárolhatok a gyakori és ritka elérésű, valamint az archív tárolási szinten?**

Az adattárolási és egyéb korlátok a fiókok szintjén vannak meghatározva, nem a tárolók szintjén. Ezért kiválaszthatja használata összes korlátot egy szinten vagy összes szinten. További információkért lásd: [Azure Storage méretezhetőségi és Teljesítménycéljai](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="next-steps"></a>További lépések

### <a name="evaluate-hot-cool-and-archive-in-gpv2-blob-storage-accounts"></a>A gyakori és ritka elérésű, valamint az archív tárolási szint értékelése a GPv2 Blob Storage-fiókok esetében

[A gyakori és ritka elérésű, valamint az archív tárolási szint rendelkezésre állásának ellenőrzése régiónként](https://azure.microsoft.com/regions/#services)

[Az Azure Blob storage életciklus kezelése](https://docs.microsoft.com/azure/storage/common/storage-lifecycle-managment-concepts)

[Aktuális tárfiókjai használatának értékelése az Azure Storage mérőszámainak engedélyezésével](../common/storage-enable-and-view-metrics.md)

[A gyakori és ritka elérésű, valamint az archív tárolási szint díjszabásának régiók szerinti ellenőrzése Blob Storage- és GPv2-fiókok esetében](https://azure.microsoft.com/pricing/details/storage/)

[Az adatátviteli díjszabás megtekintése](https://azure.microsoft.com/pricing/details/data-transfers/)
