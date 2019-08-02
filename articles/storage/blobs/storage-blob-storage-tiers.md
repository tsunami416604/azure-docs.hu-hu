---
title: Gyakori, ritka elérésű és archív hozzáférési szintek Blobok számára – Azure Storage
description: Gyakori, ritka elérésű és archív hozzáférési szintek az Azure Storage-fiókokhoz.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/23/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: d370a8ac93de20596b2c15f52427d09f11c2b1bd
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68722063"
---
# <a name="azure-blob-storage-hot-cool-and-archive-access-tiers"></a>Azure Blob Storage: gyakori, ritka elérésű és archív hozzáférési szintek

Az Azure Storage különböző hozzáférési szinteket kínál, amelyek lehetővé teszik a blob-objektumok-adattárolást a legköltséghatékonyabb módon. Az elérhető hozzáférési szintek a következők:

- A gyakran használt adatok tárolására optimalizált.
- A ritkán használt és a legalább 30 napig tárolt adatok tárolására optimalizált.
- A ritkán használt és tárolt adatok tárolására optimalizált **archiválás** – legalább 180 napig, rugalmas késési követelményekkel (az órák sorrendje szerint).

A különböző hozzáférési szinteket a következő szempontok vonatkoznak:

- Az archív hozzáférési szint csak a blob szintjén érhető el, nem pedig a Storage-fiók szintjén.
- A ritka elérésű hozzáférési szintben lévő adatok némileg alacsonyabb rendelkezésre állást is igénybe vehetnek, de továbbra is magas tartósság és a gyakran használt adatokhoz hasonló elérési idő és teljesítmény szükséges. A ritkán használt adatok esetében a rendelkezésre állási szolgáltatói szerződés (SLA) és a gyakran használt adatokhoz képest magasabb hozzáférési költségek elfogadható kompromisszumot jelentenek az alacsonyabb tárolási költségek mellett.
- Az archív tárolás offline, és ez jár a legalacsonyabb tárolási, de ezzel párhuzamosan a legmagasabb hozzáférési költségekkel.
- Csak a gyors és a lassú elérésű hozzáférési szint állítható be a fiók szintjén.
- A gyakori és ritka elérésű, valamint az archiválási szintek az objektum szintjén állíthatók be.

A felhőben tárolt adatmennyiség exponenciális ütemben növekszik. A növekvő tárolási szükségletek költségeinek kezelése érdekében hasznos lehet az adatokat olyan attribútumok alapján szervezni, mint a hozzáférés gyakorisága vagy a tervezett megőrzési időtartam, így optimalizálhatók a költségek. A felhőben tárolt adat különbözhet a létrehozás, a feldolgozás és az élettartama során elért értéktől. Egyes adatokat aktívan használnak és módosítanak teljes élettartamuk során. Egyes adatokat élettartamuk korai szakaszában sokat használnak, az adatok életkorának növekedésével azonban a hozzáférések mennyisége drasztikusan csökken. Bizonyos adatforgalom üresjáratban van a felhőben, és ritkán fordul elő, hogy a tárolás után is hozzáférjen.

Az adathozzáférési forgatókönyvek mindegyike egy másik hozzáférési szinten érhető el, amely egy adott hozzáférési mintára van optimalizálva. A gyakori, ritka elérésű és archív hozzáférési szintekkel az Azure Blob Storage ezt az igényt külön díjszabási modellel rendelkező, differenciált hozzáférési szintekhez szükséges.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-accounts-that-support-tiering"></a>Rétegezést támogató Storage-fiókok

A blob Storage és a általános célú v2 (GPv2) fiókok esetében csak az objektum tárolási adatait lehet a gyors, a ritka elérésű és az archív elérési szintre felvenni. General Purpose v1 (GPv1) fiókok nem támogatják a rétegezést. A meglévő GPv1-vagy blob Storage-fiókok azonban egyszerűen átalakíthatók úgy, hogy a Azure Portal egy kattintással elvégezhető folyamaton keresztül GPv2 a fiókokat. A GPv2 új díjszabási struktúrát biztosít a blobokhoz, fájlokhoz és várólistákhoz, és számos más új tárolási szolgáltatáshoz fér hozzá. A jövőben bizonyos új funkciók és díjak csak GPv2-fiókokban lesznek elérhetők. Ezért a GPv2-fiókok használatával kell kiértékelnie, de csak az összes szolgáltatás díjszabásának áttekintése után használhatja azokat. Bizonyos számítási feladatok drágábbak lehetnek a GPv2, mint a GPv1. További információkat az [Azure Storage-fiókok áttekintésében](../common/storage-account-overview.md) találhat.

A blob Storage és a GPv2-fiókok a **hozzáférési szint** attribútumot teszik elérhetővé a fiók szintjén. Ez az attribútum lehetővé teszi az alapértelmezett hozzáférési szint megadását a Storage-fiókban lévő olyan Blobok esetében, amelyek nem rendelkeznek explicit szinttel az objektum szintjén. Azon objektumok esetében, amelyek szintje az objektumok szintjén van megadva, a fiókszint nem lesz érvényes. Az archiválási szint csak az objektum szintjén alkalmazható. A hozzáférési szintek közötti váltást bármikor megteheti.

## <a name="premium-performance-block-blob-storage"></a>Prémium szintű teljesítmény-blokkoló blob Storage

A prémium szintű teljesítmény-blokkoló blob Storage a nagy teljesítményű hardveren keresztül elérhetővé teszi a gyakran használt adatelérést. Az ebben a teljesítményszintban található adatok tárolása SSD-meghajtókon történik, amelyek alacsony és konzisztens késésre vannak optimalizálva. Az SSD-k magasabb tranzakciós sebességet és adatátviteli sebességet biztosítanak a hagyományos merevlemezekhez képest.

A prémium szintű teljesítmény-blokkoló blob Storage ideális olyan munkaterhelésekhez, amelyek gyors és konzisztens válaszidőt igényelnek. A legjobb olyan munkaterhelések esetében, amelyek sok kisebb tranzakciót hajtanak végre, például a telemetria adatok rögzítése, az üzenetkezelés és az adatok átalakítása. A végfelhasználók, például az interaktív videoszerkesztő, a statikus webes tartalmak és az online tranzakciók is jó jelöltek.

A prémium szintű teljesítmény-blokkoló blob Storage csak a blob Storage-fiók típusán keresztül érhető el, és jelenleg nem támogatja a leválasztást a gyakori, ritka vagy archív hozzáférési szintekre.

## <a name="hot-access-tier"></a>Gyakran használt adatok hozzáférési szintje

A gyors elérési szint magasabb tárolási költséggel rendelkezik, mint a ritka elérésű és az archiválási szint, de a legalacsonyabb hozzáférési költségek. Példa használati forgatókönyvek a gyors elérési szinthez:

- Az aktív használatban lévő vagy várhatóan elérni kívánt adatok gyakran előfordulnak (olvasás és írás).
- Az adatok feldolgozásra és végső áttelepítésre való előkészítése a lassú elérési szintre.

## <a name="cool-access-tier"></a>Ritkán használt adatok hozzáférési szintje

A lassú elérési szint alacsonyabb tárolási költségekkel és magasabb hozzáférési költségekkel jár, mint a gyors tárolás. Ezen a szinten olyan adatokat érdemes tárolni, amelyek legalább 30 napig maradnak a ritka elérésű szinten. Példa használati forgatókönyvek a ritka elérésű szinthez:

- Rövid távú biztonsági mentési és vészhelyreállítási adatkészletek.
- Régebbi, már csak ritkán megtekintett médiatartalmak, amelyek elérésére igény esetén azonban azonnal szükség van.
- Nagyobb adatkészletek, amelyeket költséghatékonyan kell tárolni, amíg a későbbi feldolgozáshoz szükséges többi adat gyűjtése még folyamatban van. (*Például* tudományos adatok vagy gyártási létesítményből származó nyers telemetriaadatok hosszú távú tárolása)

## <a name="archive-access-tier"></a>Archivált adatok hozzáférési szintje

Az archív hozzáférési szint a legalacsonyabb tárolási költségekkel és a nagy teljesítményű és a ritkán használt adatok lekérésével kapcsolatos költségekkel rendelkezik. Ezen a szinten olyan adatokat érdemes tárolni, amelyek legalább 180 napig maradnak az archív szinten, és amelyeknél nem okoz gondot a lekérés több órás késése.

Míg a Blobok archiválási tárolóban találhatók, a blob-adatok offline állapotban vannak, és nem olvashatók, nem másolhatók, nem írhatók felül vagy nem módosíthatók. Az archív tárolóban nem lehet pillanatképeket készíteni egy blobról. A blob metaadatai azonban online és elérhető állapotban maradnak, és lehetővé teszik a blob és a hozzá tartozó tulajdonságok listázását. Az archív Blobok esetében az egyetlen érvényes művelet a GetBlobProperties, a GetBlobMetadata, a ListBlobs, a SetBlobTier és a DeleteBlob.

Az archív hozzáférési szint használati forgatókönyvei például a következők:

- Hosszú távú biztonsági mentések, másodlagos biztonsági mentések és archiválási adatkészletek
- Eredeti (nyers) adatok, amelyeket a végső használható formába való feldolgozásukat követően is meg kell őrizni. (*Például* nyers médiafájlok a más formátumba való átkódolásukat követően)
- Megfelelőségi és archiválási adatok, amelyeket hosszú ideig kell tárolni, azonban nagyon ritkán kell hozzáférni. (*Például*a biztonsági kamera adatfelvételei, a régi X-sugarak/MRI az egészségügyi szervezeteknek, a hangfelvételek és az ügyfelek által a pénzügyi szolgáltatások iránti hívások átiratai)

### <a name="blob-rehydration"></a>Blob rehidratálása

Az archív tárolóban lévő adatok olvasásához előbb módosítania kell a blob szintjét a gyakran vagy ritkán használt adatok tárolási szintjére. Ezt a folyamatot rehidratálásnak nevezik, és akár 15 órát is igénybe vehet. A nagy méretű Blobok mérete ajánlott az optimális teljesítmény érdekében. Több kis méretű blob egyidejűleg történő rehidratálása további időt vehet igénybe.

A rehidratálás során a blob **Archív állapot** tulajdonságának ellenőrzésével bizonyosodhat meg arról, hogy a szint módosítása befejeződött-e. Az állapot a célszinttől függően „rehydrate-pending-to-hot” (rehidratálás-folyamatban-a-gyakoriba) vagy „rehydrate-pending-to-cool” (rehidratálás-folyamatban-a-ritkába) lehet. A folyamat befejeztével a „archív állapot” tulajdonság törlődik, és a „hozzáférési szint” tulajdonság mutatja, hogy az új szint a gyakran vagy a ritkán használt szint-e.  

## <a name="blob-level-tiering"></a>Blobszintű rétegezés

A blobszintű rétegezés segítségével az adatok szintje egyetlen művelet, a [Blobszint beállítása](/rest/api/storageservices/set-blob-tier) használatával módosítható az egyes objektumok szintjén. A blobok hozzáférési szintje a használati forgatókönyvek változásával könnyen állítható a gyakran és ritkán használt, valamint az archív szintek között anélkül, hogy az adatokat át kellene helyezni egyik fiókból a másikba. Az összes szintet érintő változás azonnal megtörténik. A Blobok archívumból való rehidratálása azonban több órát is igénybe vehet.

Az utolsó blobszint-módosítás időpontja a **Hozzáférési szint utolsó módosítása** blobtulajdonságon keresztül érhető el. Ha egy blob az archiválási szinten található, akkor nem lehet felülírni, ezért a blob feltöltése nem engedélyezett ebben a forgatókönyvben. A Blobok a gyakori vagy ritka elérésű szinteken írhatók felül, ebben az esetben az új blob örökli a blob azon rétegét, amely felül lett írva.

A Blobok mind a három hozzáférési szinten egyszerre létezhetnek ugyanabban a fiókban. Azok a blobok, amelyekhez nincs kifejezetten hozzárendelve szint, a fiók hozzáférési szintjének beállítását veszik át. Ha a hozzáférési szintet a rendszer a fiókból következteti ki, a **hozzáférési rétegek** kikövetkeztetett blob tulajdonsága "true" (igaz) értékre van állítva, és a blob **hozzáférési szintje** blob tulajdonsága megegyezik a fiók szintjével. A Azure Portal a hozzáférési réteg késleltetett tulajdonsága megjelenik a blob-hozzáférési réteggel (például **gyors (késleltetett)** vagy ritka **(késleltetett**)).

> [!NOTE]
> Az archív tárolás és a blobszintű rétegezés csak a blokkblobokat támogatja. A pillanatképeket tartalmazó blokkblob szintje sem módosítható.

> [!NOTE]
> A blokk blob Storage-fiókban tárolt adatok jelenleg nem állíthatók be gyors, ritka elérésű vagy archív szintre a [set blob szint](/rest/api/storageservices/set-blob-tier) vagy az Azure Blob Storage életciklus-kezelés használatával.
> Az adatok áthelyezéséhez a blob Storage-fiókból egy másik fiókban lévő, a [put blokk URL API](/rest/api/storageservices/put-block-from-url) -val vagy az API-t támogató AzCopy-verzióval szinkronban kell átmásolni a blobokat a blokkból.
> A *put blokk from URL* API szinkron módon másolja az adatait a-kiszolgálón, ami azt jelenti, hogy a hívás csak akkor fejeződik be, ha az összes adatok átkerülnek az eredeti kiszolgáló helyéről a célhelyre.

### <a name="blob-lifecycle-management"></a>BLOB-életciklus kezelése

Az Blob Storage életciklus-kezelés sokoldalú, szabályon alapuló szabályzatot kínál, amellyel átválthatja az adatait a legjobb hozzáférési rétegre, és lemondhatja az adatait az életciklusa végén. További információért lásd: [Az Azure Blob Storage életciklusának kezelése](storage-lifecycle-management-concepts.md) .  

### <a name="blob-level-tiering-billing"></a>Blobszintű rétegezési számlázás

Ha egy blobot egy hűvösebb rétegbe helyez át (a gyors > a lassú elérésű, a gyors > az archívumot vagy a lassú elérésű > archívumot), a művelet a célként megadott szintre írási műveletként történik, ahol az írási művelet (10 000) és a célként megadott adatírás (GB-onként) érvényes.

Ha egy blobot egy melegebb rétegbe helyez át (archivált > lassú elérésű, archív > gyors vagy lassú elérésű >), a művelet számlázása a forrás rétegből történik, ahol az olvasási művelet (10 000) és a forrás szint (GB-onként) Adatlekérdezési díja érvényes. A következő táblázat összefoglalja, hogyan történik a szintek változásainak számlázása.

| | **Írási díjak (művelet + hozzáférés)** | **Olvasási díjak (művelet + hozzáférés)**
| ---- | ----- | ----- |
| **SetBlobTier iránya** | gyors elérésű > – gyors elérésű > Archívum, > Archívum | Archívum – > ritka elérésű, archív – > gyakori, ritka > – gyors

Ha átváltja a fiókszintet gyakori elérésűről ritka elérésűre, csak a GPv2-fiókok esetében lesznek díjkötelesek a beállított szinttel nem rendelkező blobok esetében végrehajtott írási műveletek (10.000 műveletenként). A blob Storage-fiókokban ez a változás díjmentes. Ha Blob Storage- vagy GPv2-fiókját ritka elérésűről gyakori elérésűre módosítja, az olvasási műveletek (10 000 műveletenként) és adatlekérések (GB-onként) után díjat számolunk fel. A ritka elérésű vagy archív szintről áthelyezett blobok esetében korai törlésre vonatkozó díjak lehetnek érvényesek.

### <a name="cool-and-archive-early-deletion"></a>Korai törlés a ritka elérésű és az archív szinten

A GB-alapú és havi díjak mellett minden, a ritka elérésű szintre áthelyezett blobra (csak GPv2-fiókok esetében) 30 napos ritka elérésű korai törlési időszak, az archív szintre áthelyezett blobokra pedig 180 napos archív korai törlési időszak vonatkozik. A díj számlázása időarányosan történik. Ha például áthelyez egy blobot az archív szintre, majd 45 nap után törli vagy áthelyezi azt a gyakori elérésű szintre, 135 (180 mínusz 45) napnyi archív tárolásnak megfelelő korai törlési díjat számolunk fel.

A korai törlést kiszámíthatja a blob tulajdonsággal, a **létrehozási idővel**, ha a hozzáférési réteg nem módosult. Ellenkező esetben akkor használhatja, ha a hozzáférési réteg utolsó módosításának ideje a következő: **hozzáférés-réteg-módosítási idő**. További információ a blob tulajdonságairól: [blob tulajdonságainak](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties)beolvasása.

## <a name="comparing-block-blob-storage-options"></a>A blob Storage-beli blokkolási beállításainak összehasonlítása

Az alábbi táblázat a prémium szintű, a blob Storage és a gyakori, ritka elérésű és archív hozzáférési szintek összehasonlítását mutatja be.

|                                           | **Prémium szintű teljesítmény** | **Gyors elérési szint** | **Hűvös szint** | **Archiválási szint**
| ----------------------------------------- | ---------------- | ------------ | ----- | ----- |
| **Rendelkezésre állás**                          | 99,9%            | 99,9%        | 99% | N/A |
| **Rendelkezésre állás** <br> **(RA-GRS olvasások)**  | –              | 99.99%       | 99.9% | N/A |
| **Használati díjak**                         | Magasabb tárolási költségek, alacsonyabb hozzáférési és tranzakciós költségek | Magasabb tárolási költségek, alacsonyabb hozzáférés és tranzakciós költségek | Alacsonyabb tárolási költségek, magasabb hozzáférési és tranzakciós költségek | Legalacsonyabb tárolási költségek, legmagasabb hozzáférési és tranzakciós költségek |
| **Minimális objektumméret**                   | N/A | N/A | N/A | N/A |
| **Minimális tárolási időtartam**              | N/A | – | 30 nap (csak GPv2 esetében) | 180 nap
| **Késés** <br> **(Az első bájtig eltelt idő)** | Egy számjegyű ezredmásodperc | ezredmásodperc | ezredmásodperc | < 15 óra

> [!NOTE]
> A méretezhetőség és a teljesítmény céljaival kapcsolatban lásd: [Az Azure Storage skálázhatósági és teljesítménybeli céljai a Storage-fiókok esetében](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) .

## <a name="quickstart-scenarios"></a>Rövid útmutatóul szolgáló forgatókönyvek

Ebben a szakaszban a következő forgatókönyveket mutatjuk be az Azure Portal használatával:

- GPv2- vagy Blob Storage-fiók alapértelmezett hozzáférési szintjének módosítása.
- GPv2- vagy Blob Storage-fiókban található blob szintjének módosítása.

### <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>GPv2- vagy Blob Storage-fiók alapértelmezett hozzáférési szintjének módosítása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A tárfiókjának eléréséhez válassza az Összes erőforrás lehetőséget, majd válassza ki a tárfiókját.

1. A Beállítások panelen kattintson a **Konfiguráció** elemre a fiók konfigurációjának megtekintéséhez és/vagy megváltoztatásához.

1. Válassza ki az igényeinek megfelelő hozzáférési szintet: Állítsa be a **hozzáférési szintet** a **lassú vagy a** **gyors**értékre.

1. Kattintson a panel tetején lévő **Mentés** elemre.

### <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>BLOB szintjeinek módosítása GPv2-vagy blob Storage-fiókban

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Ha meg szeretné keresni a blobot a Storage-fiókjában, válassza a **minden erőforrás**lehetőséget, válassza ki a Storage-fiókját, válassza ki a tárolót, majd válassza ki a blobot.

1. A **blob tulajdonságai** panelen válassza a **hozzáférési szint** legördülő menüt a **gyakori, ritka**vagy **archív** hozzáférési szint kiválasztásához.

1. Kattintson a panel tetején lévő **Mentés** elemre.

## <a name="pricing-and-billing"></a>Árak és számlázás

Az összes Storage-fiók a blob Storage díjszabási modelljét használja az egyes Blobok szintjétől függően. Tartsa szem előtt az alábbi számlázási szempontokat:

- **Tárolási költségek**: A tárolt adatok mennyisége mellett az adattárolási díjak a hozzáférési rétegtől függően változnak. A gigabájtonkénti költség csökken, ha a szint ritkábban használt adatokat tárol.
- **Adatelérési költségek**: Az adathozzáférési díjak növekednek, mivel a csomag hűvösebb lesz. A lassú elérésű és az archív hozzáférési szinten tárolt adatokért a GB-os adatelérési díjat számítjuk fel a beolvasáshoz.
- **Tranzakciós költségek**: A szinthez tartozó tranzakciós díj minden olyan rétegre kiterjed, amely növekszik, mivel a réteg hidegebb lesz.
- **Földrajzi replikálási adatátviteli költségek**: Ez a díj csak a Geo-replikációval konfigurált fiókokra vonatkozik, beleértve a GRS és az RA-GRS. A georeplikációs adatátvitel gigabájtonkénti díj ellenében érhető el.
- **Kimenő**adatforgalom költségei: A kimenő adatforgalom (az Azure-régióból átvitt adatok) esetében a sávszélesség-használatért az általános célú Storage-fiókokkal összhangban kell fizetni.
- **A hozzáférési réteg módosítása**: A fiók hozzáférési rétegének a lassúról a gyorsra való módosítása a Storage-fiókban meglévő összes információ olvasásával egyenlő. Azonban a fiókhoz való hozzáférési szint gyors és lassú elérésű értékre való módosítása az összes adatoknak a ritka rétegbe való írásával egyenlő (csak GPv2-fiókok esetében).

> [!NOTE]
> A blob Storage-fiókok díjszabásával kapcsolatos további információkért lásd az [Azure Storage díjszabását](https://azure.microsoft.com/pricing/details/storage/) ismertető oldalt. A kimenő adatátviteli díjakkal kapcsolatos további információért lásd az [adatátviteli díjszabást](https://azure.microsoft.com/pricing/details/data-transfers/) ismertető lapot.

## <a name="faq"></a>GYIK

**Blob Storage vagy GPv2-fiókokat kell használnom, ha rétegezni szeretném az adataimat?**

Javasoljuk, hogy Blob Storage-fiókok helyett használjon GPv2-fiókokat a rétegzéshez kialakításához. A GPv2 a Blob Storage-fiókok által támogatott szolgáltatások mellett sok mást is támogat. A Blob Storage és GPv2-fiókok díjszabása majdnem teljesen megegyezik, azonban egyes új szolgáltatások és árengedmények csak GPv2-fiókokhoz lesznek elérhetők. A GPv1-fiókok nem támogatják a rétegezést.

A GPv1- és GPv2-fiókok díjszabási struktúrája eltér egymástól, ezért az ügyfeleknek érdemes mindkettőt alaposan áttekinteni, mielőtt a GPv2-fiókok használata mellett döntenek. Meglévő Blob Storage- vagy GPv1-fiókját könnyedén, egyetlen kattintással átalakíthatja GPv2-fiókká. További információkat az [Azure Storage-fiókok áttekintésében](../common/storage-account-overview.md) találhat.

**Tárolhatok objektumokat mind a három (gyakori, ritka és archív) hozzáférési rétegben ugyanabban a fiókban?**

Igen. A **Hozzáférési szint** attribútum a fiók szintjén beállított alapértelmezett szint, amely a fiókban lévő összes olyan objektumra vonatkozik, amelyhez nem állítottak be szintet. A Blobszintű rétegezés szolgáltatással azonban külön megadhatja a hozzáférési szintet az egyes objektumokhoz, függetlenül attól, hogy a fiókon milyen hozzáférési szint van beállítva. Ugyanazon a fiókon belül a három hozzáférési réteg (gyakori, ritka elérésű vagy archív) egyikének blobja is létezhet.

**Módosíthatom a blob vagy GPv2 alapértelmezett hozzáférési szintjét?**

Igen, a Storage-fiók **hozzáférési rétegek** attribútumának beállításával módosíthatja az alapértelmezett hozzáférési szintet. A hozzáférési réteg módosítása a fiókban tárolt összes olyan objektumra vonatkozik, amely nem rendelkezik explicit szintű készlettel. Átállítja a hozzáférési szintet a gyakori és ritka elérésű írási műveletekhez (10 000) az összes blobhoz, amely csak a GPv2-fiókokban van, és az olvasási műveletek (10 000) és az beolvasás (GB-onként) terhelése a blob Storage-ban lévő összes blob esetében és GPv2 fiókokat.

**Beállíthatom a fiók alapértelmezett hozzáférési szintjét archív szintre?**

Nem. Csak a gyakori és ritka elérésű hozzáférési szintek állíthatók be alapértelmezett fiók-hozzáférési szintként. Az archív szint csak az objektumok szintjén állítható be.

**Mely régiókban érhetők el a gyakori, ritka elérésű és archív hozzáférési szintek?**

A gyors és a lassú elérési szint a blob szintű rétegek mellett minden régióban elérhető. Az archív tárolási szint eleinte csak bizonyos régiókban lesz elérhető. A teljes listát a [Régiónként elérhető Azure-termékek](https://azure.microsoft.com/regions/services/) című részben tekintheti meg.

**A ritka elérésű hozzáférési szinten lévő Blobok eltérően működnek, mint a gyakori elérésű hozzáférési szint?**

A gyors elérésű rétegben lévő Blobok ugyanolyan késéssel rendelkeznek, mint a GPv1, a GPv2 és a blob Storage-fiókokban lévő Blobok. A ritka elérésű hozzáférési szinten lévő Blobok hasonló késéssel rendelkeznek (ezredmásodpercben), mint a GPv1, a GPv2 és a blob Storage-fiókokban. Az archív hozzáférési szinten lévő Blobok több órányi késéssel rendelkeznek a GPv1, a GPv2 és a blob Storage-fiókokban.

A ritka elérésű hozzáférési szinten lévő Blobok valamivel alacsonyabb rendelkezésre állási szolgáltatási szinttel (SLA) rendelkeznek, mint a gyors elérésű hozzáférési szintben tárolt Blobok. További információt a [tárolók rendelkezésreállási szolgáltatási szintjeit](https://azure.microsoft.com/support/legal/sla/storage/v1_2/) ismertető szakaszban talál.

**A műveletek megegyeznek a gyakori és a ritka elérésű, valamint az archív tárolási szint esetében?**

Igen. A gyakori és a ritka elérésű szint esetében minden művelet teljesen megegyezik. Az érvényes archív tárolási szintű műveletek, mint például a törlés, listázás, blobtulajdonságok/metaadatok lekérése, valamint a blob szintjének megadása teljesen megegyezik a gyakori és ritka elérésű szint műveleteivel. Az archív tárolási szinten lévő blobok nem olvashatók vagy módosíthatók.

**Amikor az archív tárolási szintről rehidratálok egy blobot a gyakori vagy ritka elérésű szintre, honnan tudom, hogy befejeződött-e a rehidratálás?**

A rehidratálás során a blobtulajdonságok lekérése művelettel kérdezheti le az **archív állapot** attribútumot, amelynek segítségével ellenőrizheti, hogy befejeződött-e a szintváltás. Az állapot a célszinttől függően „rehydrate-pending-to-hot” (rehidratálás-folyamatban-a-gyakoriba) vagy „rehydrate-pending-to-cool” (rehidratálás-folyamatban-a-ritkába) lehet. A folyamat befejeztével a „archív állapot” tulajdonság törlődik, és a „hozzáférési szint” tulajdonság mutatja, hogy az új szint a gyakran vagy a ritkán használt szint-e.  

**Egy blob szintjének beállítása után mikor kezdődik a megfelelő díjak alapján történő számlázás?**

Az egyes Blobok számlázása a blob **hozzáférési szintje** tulajdonsága által jelzett szintek szerint történik. Amikor új szintet állít be egy blobhoz, a **hozzáférési szintet** megadó tulajdonság azonnal az új szintet tükrözi az összes átmenet esetében. Ez alól kivétel, ha az archív tárolási szintről rehidratál egy blobot a gyakori vagy ritka elérésű szintre, mivel az több órát is igénybe vehet. Ebben az esetben az archiválási díjak számlázása addig történik, amíg a rehidratálás be nem fejeződik, ekkor a **hozzáférési szint** tulajdonság az új szintet tükrözi. Ezen a ponton a blobot a gyakori vagy a ritka elérésű díjszabás alapján számlázjuk.

**Hogyan állapíthatom meg, hogy egy blob ritka elérésű vagy archív tárolási szintről való törlése vagy áthelyezése során van-e korai törlési díj?**

Minden blob, amelyet a ritka elérésű (csak GPv2-fiókok esetében) vagy archív tárolási szintről töröl vagy helyez át a vonatkozó 30, illetve 180 nap letelte előtt, a korai törléseknek megfelelő, időarányos költségeket von maga után. Megadhatja, hogy mennyi ideig legyenek a Blobok a lassú vagy az archív szinten a **hozzáférési szint módosítási idő** blob tulajdonságának megnyomásával, amely az utolsó réteg változásának bélyegzőjét adja meg. További információ: [korai törlés](#cool-and-archive-early-deletion)a ritka elérésű és az archív archívumban.

**Melyik Azure-eszközök és SDK-k támogatják a blobszintű rétegezést és az archív tárolási szintet?**

Az Azure Portal, a PowerShell, valamint a parancssori felület eszközei, illetve a .NET-, Java-, Python- és Node.js-ügyfélkönyvtárak mind támogatják a blobszintű rétegezést és az archív tárolási szintet.  

**Mennyi adatot tárolhatok a gyakori és ritka elérésű, valamint az archív tárolási szinten?**

Az adattárolás és más korlátok a fiók szintjén vannak megadva, nem pedig hozzáférési szinten. Ezért dönthet úgy is, hogy az összes korlátot egy vagy mindhárom szinten használja. További információ: az [Azure Storage skálázhatósági és teljesítménybeli céljai](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="next-steps"></a>További lépések

### <a name="evaluate-hot-cool-and-archive-in-gpv2-blob-storage-accounts"></a>A gyakori és ritka elérésű, valamint az archív tárolási szint értékelése a GPv2 Blob Storage-fiókok esetében

[A gyakori és ritka elérésű, valamint az archív tárolási szint rendelkezésre állásának ellenőrzése régiónként](https://azure.microsoft.com/regions/#services)

[Az Azure Blob Storage életciklusának kezelése](storage-lifecycle-management-concepts.md)

[Aktuális tárfiókjai használatának értékelése az Azure Storage mérőszámainak engedélyezésével](../common/storage-enable-and-view-metrics.md)

[A gyakori és ritka elérésű, valamint az archív tárolási szint díjszabásának régiók szerinti ellenőrzése Blob Storage- és GPv2-fiókok esetében](https://azure.microsoft.com/pricing/details/storage/)

[Az adatátviteli díjszabás megtekintése](https://azure.microsoft.com/pricing/details/data-transfers/)
