---
title: Gyakori és ritka elérésű és archív elérési szint a blobok – Azure Storage
description: Gyakori és ritka elérésű és archív elérési szint, az Azure storage-fiókok.
services: storage
author: mhopkins-msft
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: mhopkins
ms.subservice: blobs
ms.openlocfilehash: 78158f49748a8e9e08f3695860d95a2a6eff9fd4
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58402439"
---
# <a name="azure-blob-storage-hot-cool-and-archive-access-tiers"></a>Az Azure Blob storage: gyakori és ritka elérésű, és az archív elérési szint

Az Azure storage kínál különböző elérési szint, amely a leginkább költséghatékony módon blob objektum adatok tárolását is lehetővé teszik. A rendelkezésre álló hozzáférési szintek a következők:

- **Gyakori elérésű** – optimalizált a gyakran használt adatok tárolására.
- **Ritka elérésű** – optimalizált, a ritkán elért és legalább 30 napig tárolt adatok tárolására.
- **Archív** – optimalizált, a ritkán elért és a legkevesebb 180 napi, rugalmas késési követelményekkel (órákban mért) tárolt adatok tárolására.

Az alábbi szempontokat alkalmazni a különböző elérési szint:

- Az archivált adatok hozzáférési szintje csak a blobok szintjén, és nem a tárolási fiók szinten érhető el.
- A ritkán használt adatok tároláselérési rétegében adatok alacsonyabb elérhetőséget is, de is szükség van a nagyfokú tartósság és a gyakran használt adatokkal hasonló elérhető idő és az átviteli sebesség jellemzőit. A ritkán használt adatok, a valamelyest alacsonyabb rendelkezésre állási szolgáltatásiszint-szerződés (SLA) és magasabb hozzáférési költségek a gyakran használt adatokhoz képest az alacsonyabb tárolási költségek elfogadható kompromisszumot jelentenek ellenében.
- Az archív tárolás offline, és ez jár a legalacsonyabb tárolási, de ezzel párhuzamosan a legmagasabb hozzáférési költségekkel.
- A fiók szintjén a gyakori és ritka elérésű hozzáférési szint csak akkor állítható.
- Gyakori és ritka elérésű és archív tárolási szinteken beállítható az objektumok szintjén.

A felhőben tárolt adatok mennyisége exponenciálisan nő növekszik. A növekvő tárolási szükségletek költségeinek kezelése érdekében hasznos lehet az adatokat olyan attribútumok alapján szervezni, mint a hozzáférés gyakorisága vagy a tervezett megőrzési időtartam, így optimalizálhatók a költségek. Lehet, hogy a felhőben tárolt adatok tekintetében hogyan, rendelkezik jön létre, a feldolgozás és az élettartamuk érhető el. Egyes adatokat aktívan használnak és módosítanak teljes élettartamuk során. Egyes adatokat élettartamuk korai szakaszában sokat használnak, az adatok életkorának növekedésével azonban a hozzáférések mennyisége drasztikusan csökken. Bizonyos adatokat a felhőben inaktív marad, és ritkán van, ha után lesz tárolva.

Az egyes data access forgatókönyvek esetében számos előnyt biztosít az egy másik hozzáférési szint, amely egy adott hozzáférési mintára van optimalizálva. A gyakori és ritka elérésű és archív elérési szint, az Azure Blob storage címek egyedi elérési szint a igényeket meg különböző árképzési modellekkel.

## <a name="storage-accounts-that-support-tiering"></a>Rétegezést támogató Storage-fiókok

Ön csak a objektum tárolási adataihoz való gyors és lassú elérésű, és archiválja a Blob storage és az általános célú v2 (GPv2-) fiókok. General Purpose v1 (GPv1) fiókok nem támogatják a rétegezést. Azonban könnyedén átalakíthatja meglévő GPv1- vagy Blob storage-fiókok GPv2-fiókba az Azure Portalon egyetlen kattintással folyamaton keresztül. A GPv2 új díjszabási struktúrát biztosít a blobok, fájlok, és a várólisták és számos egyéb új tárolási szolgáltatásokhoz való hozzáférés. Továbbítja, egyes új funkciók és díjak darabok csak a GPv2-fiókok biztosítjuk. Ezért kell kiértékelni a GPv2-fiókok használatát, de csak áttekintették az összes szolgáltatás díjszabását. Lehet, hogy bizonyos munkaterhelések drágább, gpv2-fiókokon, mint a GPv1. További információkat az [Azure Storage-fiókok áttekintésében](../common/storage-account-overview.md) találhat.

BLOB storage és GPv2-fiókokban fiókszinten elérhető a **hozzáférési szint** attribútum a fiók szintjén. Ez az attribútum adja meg az alapértelmezett elérési szint gyors vagy lassú elérésű minden BLOB, a storage-fiókban nincs külön szint beállítva az objektumok szintjén teszi lehetővé. Azon objektumok esetében, amelyek szintje az objektumok szintjén van megadva, a fiókszint nem lesz érvényes. Az archív szint csak az objektumok szintjén alkalmazható. Bármikor hozzáférési szintek között válthat.

## <a name="premium-performance-block-blob-storage"></a>Prémium szintű teljesítményt blokkblob-tárolás

Prémium szintű teljesítményt blokkblob típusú tárolás nagy teljesítményű hardveren keresztül elérhetővé ritkábban használt adatokat. Ez a teljesítményszint az adatokat tárolja a tartós állapotú meghajtókhoz (SSD-kkel), amely egységes és az alacsony késés vannak optimalizálva. SSD-k biztosítanak a nagyobb tranzakciós sebesség és az átviteli sebesség a hagyományos merevlemezek képest.

Prémium szintű teljesítményt blokkblob típusú tárolás gyors és következetes válaszidők megkövetelő számítási feladatokhoz ideális. Célszerű a sok kis tranzakció, például a telemetriai adatok rögzítését, üzenetküldési és átalakítását végző számítási feladatokhoz. Adatok, amely magában foglalja a végfelhasználók számára, például az interaktív videoszerkesztés, a statikus webtartalmakat és az online tranzakció esetén is használható jól az.

Prémium szintű teljesítményt blokkblob típusú tárolás csak a block blob storage-fiók típusú keresztül érhető el, és jelenleg nem támogatja a gyakori és ritka elérésű, valamint az archív elérési szint a rétegezést.

## <a name="hot-access-tier"></a>Gyakran használt adatok hozzáférési szintje

A gyakori elérésű hozzáférési szint van, mint a ritka elérésű és az archív szint magasabb tárolási költségek, de a legalacsonyabb a hozzáférési költsége. Példa használati forgatókönyvek a gyakori elérésű hozzáférési szint a következők:

- Adatok, amelyeket aktívan használnak, vagy amelyekhez várhatóan érhető el (olvasás vagy írás formájában) gyakran.
- A ritka elérésű hozzáférési szintre feldolgozásra és a későbbiekben áttelepítésre előkészített adatok.

## <a name="cool-access-tier"></a>Ritkán használt adatok hozzáférési szintje

A ritka elérésű hozzáférési szint alacsonyabb tárolási költségek és a gyakori elérésű tárolási képest magasabb hozzáférési költségek rendelkezik. Ezen a szinten olyan adatokat érdemes tárolni, amelyek legalább 30 napig maradnak a ritka elérésű szinten. Példa használati forgatókönyvek a ritka elérésű hozzáférési szint a következők:

- Rövid távú biztonsági mentési és vészhelyreállítási adatkészletek.
- Régebbi, már csak ritkán megtekintett médiatartalmak, amelyek elérésére igény esetén azonban azonnal szükség van.
- Nagyobb adatkészletek, amelyeket költséghatékonyan kell tárolni, amíg a későbbi feldolgozáshoz szükséges többi adat gyűjtése még folyamatban van. (*Például* tudományos adatok vagy gyártási létesítményből származó nyers telemetriaadatok hosszú távú tárolása)

## <a name="archive-access-tier"></a>Archivált adatok hozzáférési szintje

Az archivált adatok hozzáférési szintje a legalacsonyabb tárolási költségekkel és a gyakori és ritka elérésű szint képest magasabb adatok adatlekérési költségekkel rendelkezik. Ezen a szinten olyan adatokat érdemes tárolni, amelyek legalább 180 napig maradnak az archív szinten, és amelyeknél nem okoz gondot a lekérés több órás késése.

Bár egy blobot az archív tárolóban lévő, a Blobadatok offline állapotban, és nem olvasható, másolt, felülírt vagy módosított. Az archív tárolóban lévő blobokról pillanatképek nem lehet végrehajtani. A blob metaadatai azonban továbbra is, online és elérhető, így a blob és a Tulajdonságok listázása. Az archív blobok az egyetlen érvényes műveletekre GetBlobProperties, GetBlobMetadata, ListBlobs, SetBlobTier és DeleteBlob.

Példa használati forgatókönyvek az archivált adatok hozzáférési szintje a következők:

- Hosszú távú biztonsági mentések, másodlagos biztonsági mentések és archiválási adatkészletek
- Eredeti (nyers) adatok, amelyeket a végső használható formába való feldolgozásukat követően is meg kell őrizni. (*Például* nyers médiafájlok a más formátumba való átkódolásukat követően)
- Megfelelőségi és archiválási adatok, amelyeket hosszú ideig kell tárolni, azonban nagyon ritkán kell hozzáférni. (*Például*, biztonsági kamerák felvételei, régi-röntgen/MRI-egészségügyi szervezetek, és az ügyfél meghívja a pénzügyi szolgáltatások)

### <a name="blob-rehydration"></a>Blob rehidratálása

Az archív tárolóban lévő adatok olvasásához előbb módosítania kell a blob szintjét a gyakran vagy ritkán használt adatok tárolási szintjére. Ezt a folyamatot rehidratálásnak nevezik, és akár 15 órát is igénybe vehet. Nagy méretű blobok használata akkor javasolt, az optimális teljesítmény érdekében. Több kis méretű blob egyidejűleg történő rehidratálása további időt vehet igénybe.

A rehidratálás során a blob **Archív állapot** tulajdonságának ellenőrzésével bizonyosodhat meg arról, hogy a szint módosítása befejeződött-e. Az állapot a célszinttől függően „rehydrate-pending-to-hot” (rehidratálás-folyamatban-a-gyakoriba) vagy „rehydrate-pending-to-cool” (rehidratálás-folyamatban-a-ritkába) lehet. A folyamat befejeztével a „archív állapot” tulajdonság törlődik, és a „hozzáférési szint” tulajdonság mutatja, hogy az új szint a gyakran vagy a ritkán használt szint-e.  

## <a name="blob-level-tiering"></a>Blobszintű rétegezés

A blobszintű rétegezés segítségével az adatok szintje egyetlen művelet, a [Blobszint beállítása](/rest/api/storageservices/set-blob-tier) használatával módosítható az egyes objektumok szintjén. A blobok hozzáférési szintje a használati forgatókönyvek változásával könnyen állítható a gyakran és ritkán használt, valamint az archív szintek között anélkül, hogy az adatokat át kellene helyezni egyik fiókból a másikba. Minden szint módosításának azonnali fordulhat elő. Azonban a blob archív szintről rehidratálása több óráig is tarthat.

Az utolsó blobszint-módosítás időpontja a **Hozzáférési szint utolsó módosítása** blobtulajdonságon keresztül érhető el. Ha egy blob az archív szinten van, akkor nem lehet felülírni, ezért ugyanennek a blobnak a feltöltése nem engedélyezett ebben a forgatókönyvben. A blobok, amelyben eset az új blob örökli a felül lett írva a blob szintjét gyors vagy lassú elérésű szint is írja felül.

Minden hozzáférési három szinten lévő blobok egy adott fiókban egyszerre is használható. Azok a blobok, amelyekhez nincs kifejezetten hozzárendelve szint, a fiók hozzáférési szintjének beállítását veszik át. Ha a hozzáférési szint a fióktól vette a beállításait a fiók, akkor tekintse meg a **hozzáférési szint következtetett** blobtulajdonság beállítása "true", és a blob **hozzáférési szint** blobtulajdonság megfelel a fiókszintjének. Az Azure Portalon a hozzáférési szint hozott tulajdonsága a blob hozzáférési szintjével együtt jelenik meg (például **gyakori (következtetett)** vagy **ritka (következtetett)**).

> [!NOTE]
> Az archív tárolás és a blobszintű rétegezés csak a blokkblobokat támogatja. A pillanatképeket tartalmazó blokkblob szintje sem módosítható.

> [!NOTE]
> A block blob storage-fiókban tárolt adatok jelenleg nem lesz rétegzett, gyakori és ritka elérésű, vagy archív tárolási szint használatával [Blobszint beállítása](/rest/api/storageservices/set-blob-tier) vagy Azure Blob Storage életciklus-felügyelet használatával.
> Az adatok áthelyezéséhez szinkron módon másolja az blobok az block blob storage-fiók egy másik fiók használata a gyakran használt adatok hozzáférési szintre a [blokk helyezze a API URL-címet a](/rest/api/storageservices/put-block-from-url) vagy az AzCopy az API-t támogató verzióját.
> A *URL blokk Put* API szinkron módon másolja az adatokat a kiszolgálón, ami azt jelenti, a hívás befejezése csak egyszer minden az adatokat a rendeltetési helyre áthelyezik az eredeti kiszolgálón.

### <a name="blob-lifecycle-management"></a>BLOB életciklus-felügyelete

A BLOB Storage életciklus-felügyelet (előzetes verzió) kínál egy gazdag, szabályalapú házirend, amelyet használhat való áttérés a adatait, hogy a legjobb hozzáférési szint és az adatok életciklusa végén lejár. Lásd: [kezelése az Azure Blob storage életciklus](storage-lifecycle-management-concepts.md) további.  

### <a name="blob-level-tiering-billing"></a>Blobszintű rétegezési számlázás

Amikor egy blob egy írásként kerül (gyakori elérésű -> ritka elérésű, gyakori elérésű -> archív vagy ritka elérésű -> archív), a művelet lesz számlázva, a cél szintre, ahol a célszintű írási műveleteire (10.000 műveletenként) és adatírásaira (gigabájtonként) vonatkozó díjak a cél szintjének alkalmazása írási művelet.

Amikor egy blob egy olvasásként kerül (archív -> ritka elérésű, archív -> gyakori elérésű vagy ritka elérésű -> gyakori elérésű), a művelet lesz számlázva, egy olvasási forrásszint, ahol az olvasási műveletek (10.000 műveletenként) és adatlekéréseire (gigabájtonként) vonatkozó díjak a forrás szintjének vonatkoznak. A következő táblázat összefoglalja, milyen szintű módosítások számlázzuk.

| | **Írás (művelet + hozzáférés) díjak** | **Olvasás (művelet + hozzáférés) díjak**
| ---- | ----- | ----- |
| **SetBlobTier Direction** | gyakori elérésű -> ritka elérésű, gyakori elérésű -> archív, ritka elérésű -> archív | archív -> ritka elérésű, archív -> gyakori elérésű, ritka elérésű -> gyakori elérésű

Ha átváltja a fiókszintet gyakori elérésűről ritka elérésűre, csak a GPv2-fiókok esetében lesznek díjkötelesek a beállított szinttel nem rendelkező blobok esetében végrehajtott írási műveletek (10.000 műveletenként). Nem jár költséggel a módosítás a Blob storage-fiókok. Ha Blob Storage- vagy GPv2-fiókját ritka elérésűről gyakori elérésűre módosítja, az olvasási műveletek (10 000 műveletenként) és adatlekérések (GB-onként) után díjat számolunk fel. A ritka elérésű vagy archív szintről áthelyezett blobok esetében korai törlésre vonatkozó díjak lehetnek érvényesek.

### <a name="cool-and-archive-early-deletion"></a>Korai törlés a ritka elérésű és az archív szinten

A GB-alapú és havi díjak mellett minden, a ritka elérésű szintre áthelyezett blobra (csak GPv2-fiókok esetében) 30 napos ritka elérésű korai törlési időszak, az archív szintre áthelyezett blobokra pedig 180 napos archív korai törlési időszak vonatkozik. A díj számlázása időarányosan történik. Ha például áthelyez egy blobot az archív szintre, majd 45 nap után törli vagy áthelyezi azt a gyakori elérésű szintre, 135 (180 mínusz 45) napnyi archív tárolásnak megfelelő korai törlési díjat számolunk fel.

A blob tulajdonság használatával lehet, hogy számítják ki a korai Törlés **létrehozáskor**, ha nincs hozzáférési szint módosításokat. Ellenkező esetben használhatja a hozzáférési szint történt utolsó módosításának a ritka elérésű vagy archív tárolási blob tulajdonság megtekintésével: **access-szint-módosítás-time**. További információ a blob tulajdonságai: [Blobtulajdonságok lekérése](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties).

## <a name="comparing-block-blob-storage-options"></a>Block blob tárolási lehetőségek összehasonlítása

Az alábbi táblázat hasonlítja össze a prémium szintű teljesítményt blokkblob tárolás és a gyakori elérésű, ritka elérésű és az archív elérési szint.

|                                           | **Prémium szintű teljesítmény** | **Gyakori elérésű szint** | **Lassú elérési szint** | **Archív szinten**
| ----------------------------------------- | ---------------- | ------------ | ----- | ----- |
| **Rendelkezésre állás**                          | 99.9%            | 99.9%        | 99% | N/A |
| **Rendelkezésre állás** <br> **(RA-GRS olvasások)**  | –              | 99.99%       | 99.9% | N/A |
| **Használati díjak**                         | Magasabb tárolási, alacsonyabb hozzáférési és tranzakciós költségek | Magasabb tárolási költségek, alacsonyabb hozzáférési és tranzakciós költségek | Alacsonyabb tárolási költségek, magasabb hozzáférési és tranzakciós költségek | Legalacsonyabb tárolási költségek, legmagasabb hozzáférési és tranzakciós költségek |
| **Minimális objektumméret**                   | N/A | N/A | N/A | N/A |
| **Minimális tárolási időtartam**              | N/A | – | 30 nap (csak GPv2 esetében) | 180 nap
| **Késés** <br> **(Az első bájtig eltelt idő)** | Egy számjegyű ideje ezredmásodpercben | ezredmásodperc | ezredmásodperc | < 15 óra

> [!NOTE]
> Méretezhetőségi és teljesítménycéljai, lásd: [tárfiókok az Azure Storage méretezhetőségi és teljesítménycéljai](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="quickstart-scenarios"></a>Rövid útmutatóul szolgáló forgatókönyvek

Ebben a szakaszban a következő forgatókönyveket mutatjuk be az Azure Portal használatával:

- GPv2- vagy Blob Storage-fiók alapértelmezett hozzáférési szintjének módosítása.
- GPv2- vagy Blob Storage-fiókban található blob szintjének módosítása.

### <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>GPv2- vagy Blob Storage-fiók alapértelmezett hozzáférési szintjének módosítása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A tárfiókjának eléréséhez válassza az Összes erőforrás lehetőséget, majd válassza ki a tárfiókját.

1. A Beállítások panelen kattintson a **Konfiguráció** elemre a fiók konfigurációjának megtekintéséhez és/vagy megváltoztatásához.

1. Válassza ki a megfelelő hozzáférési szintet az igényeinek: Állítsa be a **hozzáférési szint** egyaránt **ritkán használt adatok** vagy **gyors**.

1. Kattintson a panel tetején lévő **Mentés** elemre.

### <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>A GPv2- vagy Blob storage-fiókban található blob szintjének módosítása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A tárfiókban található blobja eléréséhez válassza **összes erőforrás**, válassza ki a tárfiókját, és válassza ki a tárolót, majd válassza ki a blob.

1. Az a **Blob tulajdonságai** panelen válassza ki a **hozzáférési szint** legördülő menüre kattintva kiválaszthatja a **interaktív**, **ritkán használt adatok**, vagy **archívum**  hozzáférési szintet.

1. Kattintson a panel tetején lévő **Mentés** elemre.

## <a name="pricing-and-billing"></a>Árak és számlázás

Az összes storage-fiókok árképzési modellt alkalmaznak a Blob minden egyes blob szintjének alapján. Tartsa szem előtt az alábbi számlázási szempontok:

- **Tárolási költségek**: Tárolt adatok mennyisége mellett a az adattárolás díja a hozzáférési szintjétől függően változik. A gigabájtonkénti költség csökken, ha a szint ritkábban használt adatokat tárol.
- **Adathozzáférési költségek**: Az adathozzáférési költségek emelkednek, ha a szint ritkábban. A lassú elérésű és archív hozzáférési szint adatok egy gigabájtonkénti adathozzáférési díjat az olvasásokhoz díjkötelesek.
- **Tranzakciós költségek**: A tranzakciónkénti díjat kell fizetni minden szint esetében, amely növeli, ha a szint ritkábban van.
- **Georeplikációs adatátviteli költségek**: Ezt a díjat csak a georeplikációval konfigurált, beleértve a GRS és RA-GRS fiókok vonatkozik. A georeplikációs adatátvitel gigabájtonkénti díj ellenében érhető el.
- **Kimenő adatátviteli költségek**: Kimenő adatforgalom (azaz az Azure-régióba adatok) sávszélesség-használat gigabájtonkénti történik, az általános célú tárfiókok esetében a számlázás számítunk fel.
- **A hozzáférési szint módosítása**: A fiók hozzáférési szintjének a ritka elérésű szintre váltása költséggel jár díj megegyezik a tárfiókban lévő összes adat. Azonban a fiók hozzáférési szintjének a ritka elérésűre való költségével díj írása az összes adat ritka elérésű szintre (csak GPv2 fiókok esetében).

> [!NOTE]
> A Blob storage-fiókok díjszabása kapcsolatos további információkért lásd: [Azure Storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage/) lapot. A kimenő adatátviteli díjakkal kapcsolatos további információért lásd az [adatátviteli díjszabást](https://azure.microsoft.com/pricing/details/data-transfers/) ismertető lapot.

## <a name="faq"></a>GYIK

**Blob Storage vagy GPv2-fiókokat kell használnom, ha rétegezni szeretném az adataimat?**

Javasoljuk, hogy Blob Storage-fiókok helyett használjon GPv2-fiókokat a rétegzéshez kialakításához. A GPv2 a Blob Storage-fiókok által támogatott szolgáltatások mellett sok mást is támogat. A Blob Storage és GPv2-fiókok díjszabása majdnem teljesen megegyezik, azonban egyes új szolgáltatások és árengedmények csak GPv2-fiókokhoz lesznek elérhetők. A GPv1-fiókok nem támogatják a rétegezést.

A GPv1- és GPv2-fiókok díjszabási struktúrája eltér egymástól, ezért az ügyfeleknek érdemes mindkettőt alaposan áttekinteni, mielőtt a GPv2-fiókok használata mellett döntenek. Meglévő Blob Storage- vagy GPv1-fiókját könnyedén, egyetlen kattintással átalakíthatja GPv2-fiókká. További információkat az [Azure Storage-fiókok áttekintésében](../common/storage-account-overview.md) találhat.

**Tárolhatok objektumokat mindhárom (gyakori és ritka elérésű, valamint archív) szinten egyazon fiók eléréséhez?**

Igen. A **Hozzáférési szint** attribútum a fiók szintjén beállított alapértelmezett szint, amely a fiókban lévő összes olyan objektumra vonatkozik, amelyhez nem állítottak be szintet. A Blobszintű rétegezés szolgáltatással azonban külön megadhatja a hozzáférési szintet az egyes objektumokhoz, függetlenül attól, hogy a fiókon milyen hozzáférési szint van beállítva. Blobokat egyszerre a három elérési szint (gyakori, ritka és archív) lehet, hogy létezik egy adott fiókban.

**Módosíthatom a Blob- vagy GPv2-tárfiókom alapértelmezett hozzáférési szintjének?**

Igen, módosíthatja az alapértelmezett hozzáférési szint beállítása a **hozzáférési szint** attribútum a tárfiókban. A hozzáférési szint módosítása az összes objektumra vonatkozik a fiókban tárolt, amelyhez nincs külön szint beállítva. A hozzáférési réteg a gyakori elérésű szintre váltása költségekkel jár írási műveletek (10.000 műveletenként) beállított szinttel a csak a GPv2-fiókok összes blobja és a ritka elérésű szintre váltása költségekkel jár mindkét olvasási műveletek (10.000 műveletenként) és minden blobok, Blob Storage-díjak adatlekérés (GB-onként) és a GPv2-fiókok esetében.

**Beállíthatom a fiók alapértelmezett hozzáférési szintjét archív szintre?**

Nem. Gyakori és ritka elérésű hozzáférési szint csak a fiók alapértelmezett hozzáférési szintjének állítható be. Az archív szint csak az objektumok szintjén állítható be.

**Ahol régiók a gyakori és ritka elérésű, és archív hozzáférési szintek elérhető?**

A gyakori és ritka elérésű hozzáférési szint, illetve a blobszintű rétegezés minden régióban érhetők el. Az archív tárolási szint eleinte csak bizonyos régiókban lesz elérhető. A teljes listát a [Régiónként elérhető Azure-termékek](https://azure.microsoft.com/regions/services/) című részben tekintheti meg.

**A gyakran használt adatok hozzáférési rétegében eltérően tegye viselkednek a ritkán használt adatok tároláselérési rétegében a blobok?**

A gyakran használt adatok tároláselérési rétegében a blobok, a blobok ugyanolyan késéssel rendelkeznek, a GPv1-, GPv2 és Blob storage-fiókok. A ritkán használt adatok tároláselérési rétegében a blobok egy hasonló késéssel rendelkeznek (ezredmásodpercben) mint a GPv1-, GPv2 és Blob storage-fiókok. Az archivált adatok hozzáférési szintje lévő blobok több óra késés tapasztalható GPv1-, GPv2 és Blob storage-fiókok.

A ritkán használt adatok tároláselérési rétegében a blobok a valamelyest alacsonyabb rendelkezésre állási szolgáltatási szintje (SLA), mint a gyakran használt adatok tároláselérési rétegében a blobok rendelkezik. További információt a [tárolók rendelkezésreállási szolgáltatási szintjeit](https://azure.microsoft.com/support/legal/sla/storage/v1_2/) ismertető szakaszban talál.

**A műveletek megegyeznek a gyakori és a ritka elérésű, valamint az archív tárolási szint esetében?**

Igen. A gyakori és a ritka elérésű szint esetében minden művelet teljesen megegyezik. Az érvényes archív tárolási szintű műveletek, mint például a törlés, listázás, blobtulajdonságok/metaadatok lekérése, valamint a blob szintjének megadása teljesen megegyezik a gyakori és ritka elérésű szint műveleteivel. Az archív tárolási szinten lévő blobok nem olvashatók vagy módosíthatók.

**Amikor az archív tárolási szintről rehidratálok egy blobot a gyakori vagy ritka elérésű szintre, honnan tudom, hogy befejeződött-e a rehidratálás?**

A rehidratálás során a blobtulajdonságok lekérése művelettel kérdezheti le az **archív állapot** attribútumot, amelynek segítségével ellenőrizheti, hogy befejeződött-e a szintváltás. Az állapot a célszinttől függően „rehydrate-pending-to-hot” (rehidratálás-folyamatban-a-gyakoriba) vagy „rehydrate-pending-to-cool” (rehidratálás-folyamatban-a-ritkába) lehet. A folyamat befejeztével a „archív állapot” tulajdonság törlődik, és a „hozzáférési szint” tulajdonság mutatja, hogy az új szint a gyakran vagy a ritkán használt szint-e.  

**Egy blob szintjének beállítása után mikor kezdődik a megfelelő díjak alapján történő számlázás?**

Minden egyes blob van a blob blobtulajdonságban megjelölt szint alapján történik **hozzáférési szint** tulajdonság. Amikor egy BLOB egy új szint a **hozzáférési szint** tulajdonság azonnal minden váltás esetében az új szintet tükrözi. Ez alól kivétel, ha az archív tárolási szintről rehidratál egy blobot a gyakori vagy ritka elérésű szintre, mivel az több órát is igénybe vehet. Ebben az esetben akkor is archív díjak amíg rehidratálás be nem fejeződik, ekkor a **hozzáférési szint** tulajdonságot az új szintet tükrözi. Ezen a ponton számlázzuk ki, hogy a blob, a gyors vagy lassú elérésű díj.

**Hogyan állapíthatom meg, hogy egy blob ritka elérésű vagy archív tárolási szintről való törlése vagy áthelyezése során van-e korai törlési díj?**

Minden blob, amelyet a ritka elérésű (csak GPv2-fiókok esetében) vagy archív tárolási szintről töröl vagy helyez át a vonatkozó 30, illetve 180 nap letelte előtt, a korai törléseknek megfelelő, időarányos költségeket von maga után. Megadhatja, hogy mennyi ideig blob lett a ritka elérésű vagy archív tárolási szinten ellenőrzésével a **hozzáférési szint módosításának ideje** blob-tulajdonság, amely biztosítja az utolsó szintmódosítás időbélyegét. További információkért lásd: [korai törlés ritka elérésű és archív](#cool-and-archive-early-deletion).

**Melyik Azure-eszközök és SDK-k támogatják a blobszintű rétegezést és az archív tárolási szintet?**

Az Azure Portal, a PowerShell, valamint a parancssori felület eszközei, illetve a .NET-, Java-, Python- és Node.js-ügyfélkönyvtárak mind támogatják a blobszintű rétegezést és az archív tárolási szintet.  

**Mennyi adatot tárolhatok a gyakori és ritka elérésű, valamint az archív tárolási szinten?**

Az adattárolási és egyéb korlátozások vannak beállítva, a fiók szintjén, és nem kiszolgálónként hozzáférési szintet. Ezért kiválaszthatja használata összes korlátot egy szinten vagy összes szinten. További információkért lásd: [Azure Storage méretezhetőségi és teljesítménycéljai](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="next-steps"></a>További lépések

### <a name="evaluate-hot-cool-and-archive-in-gpv2-blob-storage-accounts"></a>A gyakori és ritka elérésű, valamint az archív tárolási szint értékelése a GPv2 Blob Storage-fiókok esetében

[A gyakori és ritka elérésű, valamint az archív tárolási szint rendelkezésre állásának ellenőrzése régiónként](https://azure.microsoft.com/regions/#services)

[Az Azure Blob storage életciklus kezelése](storage-lifecycle-management-concepts.md)

[Aktuális tárfiókjai használatának értékelése az Azure Storage mérőszámainak engedélyezésével](../common/storage-enable-and-view-metrics.md)

[A gyakori és ritka elérésű, valamint az archív tárolási szint díjszabásának régiók szerinti ellenőrzése Blob Storage- és GPv2-fiókok esetében](https://azure.microsoft.com/pricing/details/storage/)

[Az adatátviteli díjszabás megtekintése](https://azure.microsoft.com/pricing/details/data-transfers/)