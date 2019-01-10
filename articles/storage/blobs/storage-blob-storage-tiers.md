---
title: Prémium szintű, a gyors Elérésűre, ritka elérésű és az archív blobok – Azure Storage-tároló
description: Prémium szintű, az Azure storage-fiókok gyakori, ritka elérésű és az archív tárolási.
services: storage
author: kuhussai
ms.service: storage
ms.topic: article
ms.date: 01/09/2018
ms.author: kuhussai
ms.component: blobs
ms.openlocfilehash: 21e442c7a0cdd0edcce77c862b11ae368d4a3abc
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54191666"
---
# <a name="azure-blob-storage-premium-preview-hot-cool-and-archive-storage-tiers"></a>Az Azure Blob storage: Prémium (előzetes verzió), a gyors Elérésűre, ritka elérésű és archív tárolási szintek

## <a name="overview"></a>Áttekintés

Az Azure storage a különböző tárolási rétegek, amelyek a leginkább költséghatékony módon Blob objektum adatok tárolását is lehetővé teszik kínál. Az elérhető csomagok a következők:

- **A Premium storage (előzetes verzió)** nagy teljesítményű hardver biztosít a gyakran elért adatokat.
 
- **Ritkáról gyakori elérésű tárolási**: gyakran használt adatok tárolására van optimalizálva. 

- **Ritka elérésű tárolási** ritkán elért és legalább 30 nappal a tárolt adatok tárolására van optimalizálva.
 
- **Archív tárolási** a ritkán elért és a legkevesebb 180 napi, rugalmas késési követelményekkel (órákban mért) tárolt adatok tárolására van optimalizálva.

A következő szempontokat kell kísérnie a különböző tárolási rétegek:

- Az archív tárolási szinten a blobok szintjén, és nem a tárolási fiók szintjén csak érhető el.
 
- A ritka elérésű tárolási szinten lévő adatok alacsonyabb elérhetőséget is, azonban továbbra is szükséges nagyfokú tartósság és a hasonló elérhető idő és az átviteli sebesség jellemzőkkel a gyakran használt adatokkal. A ritkán használt adatok, a valamelyest alacsonyabb rendelkezésre állási szolgáltatási szintek és magasabb hozzáférési költségek a gyakran használt adatokhoz képest az alacsonyabb tárolási költségek elfogadható kompromisszumot jelentenek ellenében.

- Az archív tárolás offline, és ez jár a legalacsonyabb tárolási, de ezzel párhuzamosan a legmagasabb hozzáférési költségekkel.
 
- Csak a gyakori és ritka elérésű tárolási szintet a fiók szintjén állítható. Az archív szint jelenleg a fiók szintjén nem állítható be.
 
- A gyakran és ritkán használt adatok, és archív tárolási szinteken lehet beállítani az objektumok szintjén.

A felhőben tárolt adatok mennyisége exponenciálisan nő növekszik. A növekvő tárolási szükségletek költségeinek kezelése érdekében hasznos lehet az adatokat olyan attribútumok alapján szervezni, mint a hozzáférés gyakorisága vagy a tervezett megőrzési időtartam, így optimalizálhatók a költségek. A felhőben tárolt adatok az előállítás, a feldolgozás és a hozzáférés tekintetében különbözőek lehetnek az élettartamuk során. Egyes adatokat aktívan használnak és módosítanak teljes élettartamuk során. Egyes adatokat élettartamuk korai szakaszában sokat használnak, az adatok életkorának növekedésével azonban a hozzáférések mennyisége drasztikusan csökken. Egyes adatok pedig inaktívan a felhőben maradnak, és a tárolást követően csak nagyon ritkán használják őket, ha használják őket egyáltalán.

Az egyes adathozzáférési forgatókönyvek esetében számos előnyt biztosít az olyan, szintekre osztott tárolási megoldás, amely egy adott hozzáférési mintára van optimalizálva. A gyors Elérésűre ritka elérésű és archív tárolási szintekkel az Azure Blob storage igényeket különböző tárolási igényeket célozza az különböző árképzési modellekkel.

## <a name="storage-accounts-that-support-tiering"></a>Rétegezést támogató Storage-fiókok

Gyakori és ritka elérésű, vagy archiválhatja a Blob storage vagy a General Purpose v2 (GPv2-) fiókok a objektum tárolási adataihoz csak, előfordulhat, hogy szinten. General Purpose v1 (GPv1) fiókok nem támogatják a rétegezést. Az ügyfelek azonban könnyedén, egyetlen kattintással átalakíthatják meglévő GPv1- vagy Blob Storage-fiókjukat GPv2-fiókká az Azure Portalon. A GPv2 új díjszabási struktúrát nyújt a blobokhoz, fájlokhoz és üzenetsorokhoz, továbbá hozzáférést biztosít különféle egyéb, új tárolási szolgáltatásokhoz is. Továbbá, a későbbiek során egyes új szolgáltatások és árengedmények csak a GPv2-fiókok esetében lesznek elérhetők. Az ügyfeleknek tehát érdemes megfontolni a GPv2 fiókok használatát. A használatot azonban csak akkor kezdjék meg, ha áttekintették az összes szolgáltatás díjszabását. Egyes számítási feladatok ugyanis drágábbak lehetnek GPv2-fiókokon, mint a GPv1-fiókok esetében. További információkat az [Azure Storage-fiókok áttekintésében](../common/storage-account-overview.md) találhat.

BLOB storage és GPv2-fiókokban fiókszinten elérhető a **hozzáférési szint** attribútum a fiók szintjén, amely lehetővé teszi, hogy a tárfiók alapértelmezett szintje gyakori vagy ritka elérésű bármely, nincs külön szint beállítva a storage-fiókban található blob esetében adja meg a objektum szintjén. Azon objektumok esetében, amelyek szintje az objektumok szintjén van megadva, a fiókszint nem lesz érvényes. Az archív szint csak az objektumok szintjén alkalmazható. Bármikor válthat a tárolási szintek között.

## <a name="premium-access-tier"></a>Prémium szintű hozzáférési szint

Előzetes verzióban elérhető van egy prémium szintű hozzáférési szint, mely teszi a gyakran használt adatok nagy teljesítményű hardveren keresztül érhető el. Ezen a szinten tárolt adatok tárolják, SSD-meghajtókat, amely a hagyományos merevlemezek képest egy nagyobb tranzakciós díjszabás alacsonyabb késést vannak optimalizálva. A prémium szintű hozzáférési szint a Blokkblob típusú tárfiók típusa csak keresztül érhető el.

Ez a szint a gyors és következetes válaszidők megkövetelő számítási feladatokhoz ideális. Olyan adatok, amely magában foglalja a végfelhasználók számára, például az interaktív videó szerkesztésére statikus webes tartalom online tranzakció- és hasonló a prémium szintű hozzáférési szint a deduplikációra. Ez a szint személyre szabott sok kis tranzakció, például a telemetriai adatok rögzítését, üzenetküldési és átalakítását végző számítási feladatokhoz.

Ez a csomag használatára, új Block Blob storage-fiók kiépítése, és kezdjen el tárolók és blobok használatával a [Blob Service REST API](/rest/api/storageservices/blob-service-rest-api), [AzCopy](/azure/storage/common/storage-use-azcopy), vagy [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

Az előzetes verzióban a prémium szintű hozzáférési szint:

- Helyileg redundáns tárolás (LRS) érhető el
- Csak érhető el ezekben a régiókban: USA 2. keleti régiója, USA középső régiója és USA nyugati régiója
- Objektum szintű rétegezést vagy automatizált adatrétegzést kínáló, az adatok életciklus-felügyelet nem támogatja

Megtudhatja, hogyan regisztrálhat a prémium szint előzetes verzió eléréséhez, lásd: [Azure prémium szintű Blob Storage bemutatása](https://aka.ms/premiumblob).

## <a name="hot-access-tier"></a>Gyakran használt adatok hozzáférési szintje

Gyakori elérésű tárolási rendelkezik, mint a ritka elérésű és archív tárolási, de a legalacsonyabb a hozzáférési költsége magasabb tárolási költségek. Példa használati forgatókönyvek a gyakori elérésű tárolási szinten a következők:

* Adatok, amelyeket aktívan használnak, vagy amelyekhez várhatóan gyakran szeretnének hozzáférni (olvasás vagy írás formájában).
* A ritka elérésű tárolási szinten való feldolgozásra és a későbbiekben áttelepítésre előkészített adatok.

## <a name="cool-access-tier"></a>Ritkán használt adatok hozzáférési szintje

Ritka elérésű tárolási szint alacsonyabb tárolási költségek és a gyakori elérésű tárolási képest magasabb hozzáférési költségek rendelkezik. Ez a szint, amely legalább 30 napig maradnak a ritka elérésű szinten adatok szól. Példa használati forgatókönyvek a ritka elérésű tárolási szinten a következők:

* Rövid távú biztonsági mentési és vészhelyreállítási adatkészletek.
* Régebbi, már csak ritkán megtekintett médiatartalmak, amelyek elérésére igény esetén azonban azonnal szükség van.
* Nagyobb adatkészletek, amelyeket költséghatékonyan kell tárolni, amíg a későbbi feldolgozáshoz szükséges többi adat gyűjtése még folyamatban van. (*Például* tudományos adatok vagy gyártási létesítményből származó nyers telemetriaadatok hosszú távú tárolása)

## <a name="archive-access-tier"></a>Archivált adatok hozzáférési szintje

Az Archive storage a legalacsonyabb tárolási költségekkel, és a gyakori és ritka elérésű tárolási képest magasabb adatok adatlekérési költségekkel rendelkezik. Ez a szint a lekérés több órás késése és legalább 180 napig maradnak az archív szinten lévő adatok szól.

Bár egy blobot az archív tárolóban lévő, a Blobadatok offline állapotban, és nem olvasható, másolt, felülírt vagy módosított. Sem készíthetők blobokról pillanatképek az Archive storage-ban. A blob metaadatai azonban továbbra is, online és elérhető, így a blob és a Tulajdonságok listázása. Az archív szinten lévő blobok esetében az érvényes műveletek a következők: GetBlobProperties, GetBlobMetadata, ListBlobs, SetBlobTier és DeleteBlob. 


Az archív tárolási szinten példa használati forgatókönyvek a következők:

* Hosszú távú biztonsági mentések, másodlagos biztonsági mentések és archiválási adatkészletek
* Eredeti (nyers) adatok, amelyeket a végső használható formába való feldolgozásukat követően is meg kell őrizni. (*Például* nyers médiafájlok a más formátumba való átkódolásukat követően)
* Megfelelőségi és archiválási adatok, amelyeket hosszú ideig kell tárolni, azonban nagyon ritkán kell hozzáférni. (*Például* biztonsági kamerák felvételei, egészségügyi intézmények régi röntgen-/MRI-felvételei, pénzügyi szolgáltatók ügyfélszolgálati hívásainak hangfelvételei és átiratai)

### <a name="blob-rehydration"></a>Blob rehidratálása
Az archív tárolóban lévő adatok olvasásához előbb módosítania kell a gyakori vagy ritka elérésű blob szintjének. Ezt a folyamatot rehidratálásnak nevezik, és akár 15 órát is igénybe vehet. Nagy méretű blobok használata akkor javasolt, az optimális teljesítmény érdekében. Több kis méretű blob egyidejűleg történő rehidratálása további időt vehet igénybe.

A rehidratálás során a blob **Archív állapot** tulajdonságának ellenőrzésével bizonyosodhat meg arról, hogy a szint módosítása befejeződött-e. Az állapot a célszinttől függően „rehydrate-pending-to-hot” (rehidratálás-folyamatban-a-gyakoriba) vagy „rehydrate-pending-to-cool” (rehidratálás-folyamatban-a-ritkába) lehet. Befejezéskor az archív állapot tulajdonság törlődik, és a **hozzáférési szint** tulajdonság mutatja, az új gyors vagy lassú elérésű szint.  

## <a name="blob-level-tiering"></a>Blobszintű rétegezés

A blobszintű rétegezés segítségével az adatok szintje egyetlen művelet, a [Blobszint beállítása](/rest/api/storageservices/set-blob-tier) használatával módosítható az egyes objektumok szintjén. Fiókok közötti adatok áthelyezése nélkül használati forgatókönyvek változásával könnyen módosíthatja az elérési szint egy blobot a gyakori, ritka elérésű vagy archív szintek között. Minden szint módosításának azonnali fordulhat elő. Azonban a blob archív szintről rehidratálása több óráig is tarthat. 

Az utolsó blobszint-módosítás időpontja a **Hozzáférési szint utolsó módosítása** blobtulajdonságon keresztül érhető el. Ha egy blob az archív szinten van, akkor nem lehet felülírni, ezért ugyanennek a blobnak a feltöltése nem engedélyezett ebben a forgatókönyvben. Egy blob egy gyors vagy lassú elérésű szint, amelyben eset az új blob örökli felül lett írva a blob szintjének felülírhatja.

Egy adott fiók tartalmazhat blobokat egyszerre akár mindhárom szinten is. Azok a blobok, amelyekhez nincs kifejezetten hozzárendelve szint, a fiók hozzáférési szintjének beállítását veszik át. Ha a hozzáférési szint a fióktól vette a beállításait a fiók, akkor tekintse meg a **hozzáférési szint következtetett** blobtulajdonság beállítása "true", és a blob **hozzáférési szint** blobtulajdonság megfelel a fiókszintjének. Az Azure Portalon a hozzáférési szint hozott tulajdonsága a blob hozzáférési szintjével együtt jelenik meg (például Gyakori (következtetett) vagy Ritka (következtetett)).

> [!NOTE]
> Az archív tárolás és a blobszintű rétegezés csak a blokkblobokat támogatja. A pillanatképeket tartalmazó blokkblob szintje sem módosítható.

> [!NOTE]
> Hozzáférés prémium szinten tárolt adatok jelenleg nem lesz rétegzett, gyakori és ritka elérésű, vagy archív tárolási szint használatával [Blobszint beállítása](/rest/api/storageservices/set-blob-tier) vagy Azure Blob Storage életciklus-felügyelet használatával. Az adatok áthelyezéséhez szinkron módon másolja az blobok az prémium szintű hozzáférést a gyakran használt adatok használata a [blokk helyezze a API URL-címet a](/rest/api/storageservices/put-block-from-url) vagy az AzCopy az API-t támogató verzióját. A *URL blokk Put* API szinkron módon másolja az adatokat a kiszolgálón, ami azt jelenti, a hívás befejezése csak egyszer minden az adatokat a rendeltetési helyre áthelyezik az eredeti kiszolgálón.

### <a name="blob-lifecycle-management"></a>BLOB életciklus-felügyelete
A BLOB Storage életciklus-felügyelet (előzetes verzió) kínál egy gazdag, szabályalapú házirend, amelyet használhat való áttérés a adatait, hogy a legjobb hozzáférési szint és az adatok életciklusa végén lejár. Lásd: [kezelése az Azure Blob storage életciklus](storage-lifecycle-management-concepts.md) további.  

### <a name="blob-level-tiering-billing"></a>Blobszintű rétegezési számlázás

Amikor egy blob egy írásként kerül (gyakori elérésű -> ritka elérésű, gyakori elérésű -> archív vagy ritka elérésű -> archív), a művelet lesz számlázva, a cél szintre, ahol a célszintű írási műveleteire (10.000 műveletenként) és adatírásaira (gigabájtonként) vonatkozó díjak a cél szintjének alkalmazása írási művelet. Amikor egy blob egy olvasásként kerül (archív -> ritka elérésű, archív -> gyakori elérésű vagy ritka elérésű -> gyakori elérésű), a művelet lesz számlázva, egy olvasási forrásszint, ahol az olvasási műveletek (10.000 műveletenként) és adatlekéréseire (gigabájtonként) vonatkozó díjak a forrás szintjének vonatkoznak.

| | **Díj írása** | **Olvassa el a díj** 
| ---- | ----- | ----- |
| **SetBlobTier iránya** | Gyakori elérésű -> ritka elérésű, gyakori elérésű, archív -> ritka elérésű -> archív | Archív -> ritka elérésű, archív -> gyakori elérésű, ritka elérésű -> gyakori elérésű

Ha Ön átváltja a gyors elérésűről lassú elérésűre módosítja, akkor az írási műveletek (10.000 műveletenként), beállított szinttel a csak a GPv2-fiókok összes blobja számítunk. Nem jár költséggel a módosítás a Blob storage-fiókok. Meg kell fizetni az olvasási műveletek (10.000 műveletenként) és az adatlekérés (GB-onként) ha átváltja a Blob storage vagy GPv2-fiókra lassú elérésűről gyors elérésű szintre. A ritka elérésű vagy archív tárolási szintről áthelyezett blobok esetében korai törlésre vonatkozó díjak lehetnek érvényesek.

### <a name="cool-and-archive-early-deletion"></a>Korai törlés a lassú elérésű és az archív szinten

Mellett a gigabájtonkénti Havidíjakat, havi díjak mellett minden blob, amelyet a ritka elérésű szintre (csak GPv2 fiókok esetében) kerül a ritka elérésű korai törlési időszak 30 napig érvényesek, és minden blob, amelyet az archív tárolási szintről áthelyezett egy archív korai törlési időszak 180 napig érvényesek. A díj számlázása időarányosan történik. Például, ha a blob archív át, és majd törlése vagy a gyakori elérésű szintre áthelyezett 45 nap után számítunk fel egy korai törlési díjat, 135 (180 mínusz 45) napnyi archív tárolásnak megfelelő.

A blob tulajdonság használatával lehet, hogy számítják ki a korai Törlés **létrehozáskor**, ha nincs hozzáférési szint módosításokat. Egyéb használhatja, ha a hozzáférési szint utolsó módosításának ritka elérésű vagy archív tárolási szint, a blob tulajdonság megtekintésével: **access-szint-módosítás-time**. További információ a blob tulajdonságai: [Blobtulajdonságok lekérése](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties).

## <a name="comparison-of-the-storage-tiers"></a>A tárolási szintek összehasonlítása

Az alábbi táblázat hasonlítja össze a gyors Elérésűre, ritka elérésű és archív tárolási szintek.

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
> A Blob Storage-fiókok ugyanazokat a méretezhetőségi és teljesítménycélokat támogatják, mint az általános célú tárfiókok. További információkért lásd: [Azure Storage méretezhetőségi és Teljesítménycéljai](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). 

## <a name="quickstart-scenarios"></a>Rövid útmutatóul szolgáló forgatókönyvek

Ebben a szakaszban a következő forgatókönyveket mutatjuk be az Azure Portal használatával:

* GPv2- vagy Blob Storage-fiók alapértelmezett hozzáférési szintjének módosítása.
* GPv2- vagy Blob Storage-fiókban található blob szintjének módosítása.

### <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>GPv2- vagy Blob Storage-fiók alapértelmezett hozzáférési szintjének módosítása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. A tárfiókjának eléréséhez válassza az Összes erőforrás lehetőséget, majd válassza ki a tárfiókját.

3. A Beállítások panelen kattintson a **Konfiguráció** elemre a fiók konfigurációjának megtekintéséhez és/vagy megváltoztatásához.

4. Válassza ki az igényeinek megfelelő tárolási szintjére: Állítsa be a **hozzáférési szint** egyaránt **ritkán használt adatok** vagy **gyors**.

5. Kattintson a panel tetején lévő Mentés elemre.

### <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>GPv2- vagy Blob Storage-fiókban található blob szintjének módosítása.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. A tárfiókban található blobja eléréséhez válassza az Összes erőforrás lehetőséget, majd válassza ki a tárfiókot, a tárolót és végül a blobot.

3. A Blob tulajdonságai panelen kattintson a **Hozzáférési szint** legördülő menüre, és válassza a **Gyakori**, **Ritka** vagy **Archív** tárolási szintet.

5. Kattintson a panel tetején lévő Mentés elemre.

## <a name="pricing-and-billing"></a>Árak és számlázás

Az összes storage-fiókok árképzési modellt alkalmaznak a Blob minden egyes blob szintjének alapján. Tartsa szem előtt az alábbi számlázási szempontok:

* **Tárolási költségek**: Tárolt adatok mennyisége mellett az adattárolás díja a tárolási réteg függvényében. A gigabájtonkénti költség csökken, ha a szint ritkábban használt adatokat tárol.
* **Adathozzáférési költségek**: Az adathozzáférési költségek emelkednek, ha a szint ritkábban. A ritka elérésű és archív tárolási szint díja a gigabájtonkénti adathozzáférési díjat az olvasásokhoz.
* **Tranzakciós költségek**: A tranzakciónkénti díjat kell fizetni minden szint esetében, amely növeli, ha a szint ritkábban van.
* **Georeplikációs adatátviteli költségek**: Ezt a díjat csak a georeplikációval konfigurált, beleértve a GRS és RA-GRS fiókok vonatkozik. A georeplikációs adatátvitel gigabájtonkénti díj ellenében érhető el.
* **Kimenő adatátviteli költségek**: Kimenő adatforgalom (azaz az Azure-régióba adatok) sávszélesség-használat gigabájtonkénti történik, az általános célú tárfiókok esetében a számlázás számítunk fel.
* **A tárolási réteg módosítása**: A fiók tárolási szintjének a ritkán használt adatok gyors elérésűre díj költségével egyezik megegyezik a tárfiókban lévő összes adat. Azonban a fiók tárolási szintjének módosítása gyors elérésűről lassú elérésűre kötelezettséggel írásának összes adat ritka elérésű szintre (csak GPv2 fiókok esetében).

> [!NOTE]
> A Blob storage-fiókok díjszabása kapcsolatos további információkért lásd: [Azure Storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage/) lapot. A kimenő adatátviteli díjakkal kapcsolatos további információért lásd az [adatátviteli díjszabást](https://azure.microsoft.com/pricing/details/data-transfers/) ismertető lapot.

## <a name="faq"></a>GYIK

**Blob Storage vagy GPv2-fiókokat kell használnom, ha rétegezni szeretném az adataimat?**

Javasoljuk, hogy Blob Storage-fiókok helyett használjon GPv2-fiókokat a rétegzéshez kialakításához. A GPv2 a Blob Storage-fiókok által támogatott szolgáltatások mellett sok mást is támogat. A Blob Storage és GPv2-fiókok díjszabása majdnem teljesen megegyezik, azonban egyes új szolgáltatások és árengedmények csak GPv2-fiókokhoz lesznek elérhetők. A GPv1-fiókok nem támogatják a rétegezést.

A GPv1- és GPv2-fiókok díjszabási struktúrája eltér egymástól, ezért az ügyfeleknek érdemes mindkettőt alaposan áttekinteni, mielőtt a GPv2-fiókok használata mellett döntenek. Meglévő Blob Storage- vagy GPv1-fiókját könnyedén, egyetlen kattintással átalakíthatja GPv2-fiókká. További információkat az [Azure Storage-fiókok áttekintésében](../common/storage-account-overview.md) találhat.

**Tárolhatok objektumokat mindhárom (gyakori, ritka elérésű és archív) szinten egyazon fiók?**

Igen. A **Hozzáférési szint** attribútum a fiók szintjén beállított alapértelmezett szint, amely a fiókban lévő összes olyan objektumra vonatkozik, amelyhez nem állítottak be szintet. A Blobszintű rétegezés szolgáltatással azonban külön megadhatja a hozzáférési szintet az egyes objektumokhoz, függetlenül attól, hogy a fiókon milyen hozzáférési szint van beállítva. Blobokat egyszerre a három tárolási szintet (gyors Elérésűre, ritka elérésű vagy archív), előfordulhat, hogy létezik egy adott fiókban.

**Módosíthatom a Blob Storage- vagy GPv2-tárfiókom alapértelmezett tárolási szintjét?**

Igen, az alapértelmezett tárolási szintet úgy változtathatja meg, hogy a tárfiókban megadja a **Hozzáférési szint** attribútum kívánt értékét. A tárolási szint módosítása az adott fiókban lévő összes olyan objektumra vonatkozik, amelyhez nincs külön szint beállítva. A tárolási rétegnek a gyakran használt adatok a ritka elérésű szintre váltása költségekkel jár írási műveletek (10.000 műveletenként) beállított szinttel a csak a GPv2-fiókok összes blobja és lassúról gyors elérésűre szintre váltása költségekkel jár mindkét olvasási műveletek (10.000 műveletenként) és minden blobok, Blob Storage-díjak adatlekérés (GB-onként) és a GPv2-fiókok esetében.

**Állítható a fiók alapértelmezett hozzáférési szintjét archív?**

Nem. A fiók alapértelmezett hozzáférési szintjének csak gyors és lassú elérésű tárolási szint állítható be. Az archív szint csak az objektumok szintjén állítható be.

**Ahol régiókban érhetők el a gyors Elérésűre, ritka elérésű és az archív tárolási szintet?**

A gyakori és ritka elérésű tárolási szint, illetve a blobszintű rétegezés minden régióban érhetők el. Az archív tárolási szint eleinte csak bizonyos régiókban lesz elérhető. A teljes listát a [Régiónként elérhető Azure-termékek](https://azure.microsoft.com/regions/services/) című részben tekintheti meg.

**A gyakran használt adatok tárolási rétegében eltérően tegye viselkednek a ritka elérésű tárolási szinten a blobok?**

A gyakori elérésű tárolási szinten a blobok, a blobok ugyanolyan késéssel rendelkeznek, a GPv1-, GPv2 és Blob storage-fiókok. A ritka elérésű tárolási szinten a blobok a GPv1-, GPv2 és Blob storage-fiókok (ezredmásodpercben), a blobok hasonló késéssel rendelkeznek. Az archív tárolási szinten lévő blobok több óra késés tapasztalható GPv1-, GPv2 és Blob storage-fiókok.

A ritka elérésű tárolási szinten a blobok a valamelyest alacsonyabb rendelkezésre állási szolgáltatási szintje (SLA), mint a gyakran használt adatok tárolási rétegében a blobok rendelkezik. További információt a [tárolók rendelkezésreállási szolgáltatási szintjeit](https://azure.microsoft.com/support/legal/sla/storage/v1_2/) ismertető szakaszban talál.

**Ugyanazok a műveletek a gyors Elérésűre, ritka elérésű és az archív szintek között?**

Igen. Gyakori és ritka elérésű közötti összes műveletekre 100 %-os konzisztens. Minden érvényes archív műveletek mint például a törlés, lista, get blobtulajdonságok/metaadatok és blobszint beállítása a 100 %-os gyakori és ritka elérésű adatok konzisztensek. A blob nem olvassa el és nem módosítható az archív szinten lévő.

**Ha egy blobot az archív tárolási szintről a gyakori vagy ritka elérésű szint rehidratálása, hogyan tudom rehidratálás?**

A rehidratálás során a blobtulajdonságok lekérése művelettel kérdezheti le az **archív állapot** attribútumot, amelynek segítségével ellenőrizheti, hogy befejeződött-e a szintváltás. Az állapot a célszinttől függően „rehydrate-pending-to-hot” (rehidratálás-folyamatban-a-gyakoriba) vagy „rehydrate-pending-to-cool” (rehidratálás-folyamatban-a-ritkába) lehet. Befejezéskor az archív állapot tulajdonság törlődik, és a **hozzáférési szint** tulajdonság mutatja, az új gyors vagy lassú elérésű szint.  

**Egy blob szintjének beállítása után mikor kezdődik a megfelelő díjak alapján történő számlázás?**

Minden egyes blob van a blob blobtulajdonságban megjelölt szint alapján történik **hozzáférési szint** tulajdonság. Amikor egy BLOB egy új szint a **hozzáférési szint** tulajdonság azonnal minden váltás esetében az új szintet tükrözi. Azonban rehidratálok egy blobot az archív szintből gyors vagy lassú elérésű szint több óráig is tarthat. Ebben az esetben akkor is archív díjak amíg rehidratálás be nem fejeződik, ekkor a **hozzáférési szint** tulajdonságot az új szintet tükrözi. Ezen a ponton számlázzuk ki, hogy a blob, a gyakori vagy ritka elérésű mellett.

**Hogyan állapítható meg, hogy ha törlése vagy áthelyezése egy blob ritka a ritka elérésű vagy archív tárolási szintről során fog fel korai törlési díj?**

Minden blob, amelyet töröl vagy helyez át a ritka elérésű (csak GPv2 fiókok esetében) vagy archív tárolási szintről előtt 30 nappal, illetve 180 nap rendre időarányos korai törlési díjat számítunk fel. Megadhatja, hogy mennyi ideig blob lett a ritka elérésű vagy archív tárolási szinten ellenőrzésével a **hozzáférési szint módosításának ideje** blob-tulajdonság, amely biztosítja az utolsó szintmódosítás időbélyegét. Lásd: [ritka elérésű és archív korai Törlés](#cool-and-archive-early-deletion) további részleteket a következő szakaszban.

**Mely Azure-eszközök és SDK-k támogatják a blobszintű rétegezést és az archív tárolási?**

Az Azure portal, PowerShell és CLI eszközök és klienskódtárak .NET, Java, Python és Node.js összes támogatják a blobszintű rétegezést és az archív tárolási.  

**Mennyi adatot tárolhatok a gyakori, ritka elérésű és az archív szinten?**

Az adattárolási és egyéb korlátok a fiókok szintjén vannak meghatározva, nem a tárolók szintjén. Ezért kiválaszthatja használata összes korlátot egy szinten vagy összes szinten. További információkért lásd: [Azure Storage méretezhetőségi és Teljesítménycéljai](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="next-steps"></a>További lépések

### <a name="evaluate-hot-cool-and-archive-in-gpv2-blob-storage-accounts"></a>Gyors Elérésűre, ritka elérésű és az archív értékelése a GPv2 Blob storage-fiókok

[Gyors Elérésűre, ritka elérésű és az archív régiónként elérhetőségének ellenőrzése](https://azure.microsoft.com/regions/#services)

[Az Azure Blob storage életciklus kezelése](storage-lifecycle-management-concepts.md)

[Aktuális tárfiókjai használatának értékelése az Azure Storage mérőszámainak engedélyezésével](../common/storage-enable-and-view-metrics.md)

[Ellenőrizze a gyors, lassú és az Archive Blob storage és GPv2 fiókok régiónként díjszabás](https://azure.microsoft.com/pricing/details/storage/)

[Az adatátviteli díjszabás megtekintése](https://azure.microsoft.com/pricing/details/data-transfers/)
