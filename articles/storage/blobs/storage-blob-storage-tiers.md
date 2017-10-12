---
title: "Az Azure blobtárolási rétege gyakran és ritkán használt, valamint archivált adatokhoz | Microsoft Docs"
description: "A gyakran és a ritkán használt, valamint az archivált adatok tárolása Azure Blob Storage-fiókok esetén."
services: storage
documentationcenter: 
author: michaelhauss
manager: vamshik
editor: tysonn
ms.assetid: eb33ed4f-1b17-4fd6-82e2-8d5372800eef
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/05/2017
ms.author: mihauss
ms.openlocfilehash: 544b11d74a926fe62b8ceca51570ce9d2ee7e6e7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-blob-storage-hot-cool-and-archive-preview-storage-tiers"></a>Azure Blob Storage: A gyakran és a ritkán használt, valamint az archivált adatokhoz (előzetes verzió) használt tárolási rétegek

## <a name="overview"></a>Áttekintés

Az Azure Storage három tárolási réteget kínál a Blob-objektumok tároláshoz, hogy adatait a legköltséghatékonyabb módon tárolhassa a használat függvényében. Az Azure **tárolási rétege gyakran használt adatokhoz** a gyakran használt adatok tárolására van optimalizálva. Az Azure **tárolási rétege ritkán használt adatokhoz** a ritkábban használt adatok egy havi és annál hosszabb idejű tárolására van optimalizálva. Az Azure [tárolási rétege archivált adatokhoz (előzetes verzió)](https://azure.microsoft.com/blog/announcing-the-public-preview-of-azure-archive-blob-storage-and-blob-level-tiering) az alig használt adatok hat havi és annál hosszabb idejű, rugalmas (órákban mért) késési követelményekkel rendelkező tárolására van optimalizálva. Az *archív* tárolási réteg csak a blobok szintjén érhető el, a teljes tárfiókén nem. A ritkán használt adatok tárolási rétegében lévő adatok esetében ugyan alacsonyabb rendelkezésre állás is elegendő lehet, azonban továbbra is magas tartósság és a gyakran használt adatokkal egyező hozzáférési idő és teljesítmény szükséges. A ritkán használt és az archív adatok esetében a valamelyest alacsonyabb rendelkezésre állási szolgáltatási szintek és magasabb hozzáférési költségek elfogadható kompromisszumot jelentenek az alacsonyabb tárolási költségek ellenében.

Napjainkban a felhőben tárolt adatok mennyisége exponenciálisan nő. A növekvő tárolási szükségletek költségeinek kezelése érdekében hasznos lehet az adatokat olyan attribútumok alapján szervezni, mint a hozzáférés gyakorisága vagy a tervezett megőrzési időtartam. A felhőben tárolt adatok az előállítás, a feldolgozás és a hozzáférés tekintetében különbözőek lehetnek az élettartamuk során. Egyes adatokat aktívan használnak és módosítanak teljes élettartamuk során. Egyes adatokat élettartamuk korai szakaszában sokat használnak, az adatok életkorának növekedésével azonban a hozzáférések mennyisége drasztikusan csökken. Egyes adatok pedig inaktívan a felhőben maradnak, és a tárolást követően csak nagyon ritkán használják őket, ha használják őket egyáltalán.

Az egyes adathozzáférési forgatókönyvek esetében számos előnyt biztosít az olyan rétegelt tárolási megoldás, amely egy adott hozzáférési mintára van optimalizálva. A gyakran és a ritkán használt, valamint az archivált adatok tárolási rétegeivel az Azure Blob Storage a különböző tárolási igényeket célozza meg különböző árképzési modellekkel.

## <a name="blob-storage-accounts"></a>Blob Storage-fiókok

A **Blob Storage-fiókok** speciális tárfiókok a strukturálatlan adatok blobként (objektumokként) való tárolására az Azure Storage-ban. A Blob Storage-fiókok esetében a fiók szintjén a gyakran és a ritkán használt, az egyes blobok szintjén pedig a gyakran és a ritkán használt, valamint az archivált adatok tárolási rétege közül választhat az adathozzáférési forgatókönyveknek megfelelően. Az alig használt adatokat a legalacsonyabb tárolási költséggel, a ritkábban használt adatokat a gyakran használtaknál alacsonyabb tárolási költséggel, a gyakrabban használt adatokat pedig a legalacsonyabb hozzáférési költséggel tárolhatja. A Blob Storage-fiókok hasonlóak a meglévő általános célú tárfiókjaihoz, és a jelenlegi rendszereivel megegyező szintű tartósságot, rendelkezésre állást, méretezhetőséget és teljesítményt nyújtanak, beleértve a 100 százalékos API-konzisztenciát a blokkblobokhoz és a hozzáfűző blobokhoz.

> [!NOTE]
> A Blob Storage-fiókok csak a blokkblobokat és a hozzáfűző blobokat támogatják, a lapblobokat nem.

A Blob Storage-fiókokban elérhető a **Hozzáférési réteg** attribútum, amely segítségével megadhatja, hogy a tárolási réteg **gyakran használt adatok** vagy **ritkán használt adatok** tárolására szolgáljon a fiókban tárolt adatoktól függően. Ha változik az adatok használati módja, bármikor válthat a tárolási rétegek között. Az archív réteg (előzetes verzió) csak a blobok szintjén alkalmazható.

> [!NOTE]
> A tárolási rétegek módosítása további díjakat vonhat maga után. További részleteket a következő szakaszban talál: [Árak és számlázás](#pricing-and-billing).

### <a name="hot-access-tier"></a>Gyakran használt adatok hozzáférési szintje

Példa használati forgatókönyvek a gyakran használt adatok tárolási rétegéhez:

* Adatok, amelyeket aktívan használnak, vagy amelyekhez várhatóan gyakran szeretnének hozzáférni (olvasás vagy írás formájában).
* A feldolgozásra és a későbbiekben a ritkán használt adatok tárolási rétegébe való áttelepítésre előkészített adatok.

### <a name="cool-access-tier"></a>Ritkán használt adatok hozzáférési szintje

Példa használati forgatókönyvek a ritkán használt adatok tárolási rétegéhez:

* Rövid távú biztonsági mentési és vészhelyreállítási adatkészletek.
* Régebbi, már csak ritkán megtekintett médiatartalmak, amelyek elérésére igény esetén azonban azonnal szükség van.
* Nagyobb adatkészletek, amelyeket költséghatékonyan kell tárolni, amíg a későbbi feldolgozáshoz szükséges többi adat gyűjtése még folyamatban van. (*Például* tudományos adatok vagy gyártási létesítményből származó nyers telemetriaadatok hosszú távú tárolása)

### <a name="archive-access-tier-preview"></a>Archivált adatok hozzáférési szintje (előzetes verzió)

Az [archív tároló](https://azure.microsoft.com/blog/announcing-the-public-preview-of-azure-archive-blob-storage-and-blob-level-tiering) a gyakran és a ritkán használt adatok tárolásához képest a legalacsonyabb tárolási költségekkel, viszont magasabb adatlekérési költségekkel rendelkezik.

Az archív tárolóban lévő blobok nem olvashatók, másolhatók, írhatók felül vagy módosíthatók. Az archív tárolóban lévő blobokról pillanatképek sem készíthetők. A törlés, listázás, a blobtulajdonságok/metaadatok lekérése vagy a blob tárolási rétegének módosítása azonban elvégezhető. Az archív tárolóban lévő adatok olvasásához előbb módosítania kell a blob rétegét a gyakran vagy ritkán használt adatok tárolási rétegére. Ezt a folyamatot rehidratálásnak nevezik, és 50 GB alatti méretű blobok esetében akár 15 órát is igénybe vehet. A nagyobb méretű blobok esetében még több időt vesz igénybe a blob átviteli sebességkorlátjának függvényében.

A rehidratálás során a blob „archív állapot” tulajdonságának ellenőrzésével bizonyosodhat meg arról, hogy a réteg módosítása befejeződött-e. Az állapot a célrétegtől függően „rehydrate-pending-to-hot” (rehidratálás-folyamatban-a-gyakran-használt-rétegbe) vagy „rehydrate-pending-to-cool” (rehidratálás-folyamatban-a-ritkán-használt-rétegbe) lehet. A folyamat befejeztével a blob „archív állapot” tulajdonsága törlődik, és a „hozzáférési szint” tulajdonság mutatja, hogy a gyakran vagy a ritkán használt rétegről van-e szó.  

Példa használati forgatókönyvek az archív adatok tárolási rétegéhez:

* Hosszú távú biztonsági mentési, archiválási és vészhelyreállítási adatkészletek.
* Eredeti (nyers) adatok, amelyeket a végső használható formába való feldolgozásukat követően is meg kell őrizni. (*Például* nyers médiafájlok a más formátumba való átkódolásukat követően)
* Megfelelőségi és archiválási adatok, amelyeket hosszú ideig kell tárolni, azonban nagyon ritkán kell hozzáférni. (*Például* biztonsági kamerák felvételei, egészségügyi intézmények régi röntgen-/MRI-felvételei, pénzügyi szolgáltatók ügyfélszolgálati hívásainak hangfelvételei és átiratai)

### <a name="recommendations"></a>Javaslatok

A tárfiókokkal kapcsolatos további információk: [Tudnivalók az Azure Storage-fiókokról](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

A csak blokk- és hozzáfűző blobok tárolását igénylő alkalmazásokhoz javasoljuk a Blob Storage-fiókok használatát, így kiaknázhatóak a rétegelt tárolás differenciált árképzési modelljének előnyei. Tisztában vagyunk azonban azzal, hogy ez bizonyos körülmények között esetleg nem lehetséges, ahol inkább az általános célú tárfiókok használata célszerű, például:

* Ha táblákat, üzenetsorokat vagy fájlokat kell használnia, és szeretné, ha a blobok ugyanabban a tárfiókban lennének tárolva. Vegye figyelembe, hogy ezek azonos tárfiókban való tárolásának egyetlen műszaki előnye, hogy ugyanazok a megosztott kulcsok szükségesek hozzájuk.

* Ugyanúgy a hagyományos telepítési modellt kell alkalmaznia. A Blob Storage-fiókok kizárólag az Azure Resource Manager-alapú üzemi modellben érhetőek el.

* Lapblobokat kell használnia. A Blob Storage-fiókok nem támogatják a lapblobokat. Általában a blokkblobok használatát javasoljuk, kivéve, ha valamiért kifejezetten lapblobokra van szüksége.

* A [Storage szolgáltatások REST API felülete](https://msdn.microsoft.com/library/azure/dd894041.aspx) 2014. 02. 14-nél korábbi verzióját vagy egy 4.x-nél korábbi verziójú ügyfélkódtárat használ, és nem tudja frissíteni az alkalmazást.

> [!NOTE]
> A Blob Storage-fiókok jelenleg az Azure-régiók mindegyikében támogatottak.
 

## <a name="blob-level-tiering-feature-preview"></a>Blobszintű rétegzési szolgáltatás (előzetes verzió)

A blobszintű rétegzés segítségével az adatok rétege mostantól egyetlen művelet, a [Blobréteg beállítása](/rest/api/storageservices/set-blob-tier) használatával módosítható az objektum szintjén. A blobok hozzáférési szintje a használati forgatókönyvek változásával könnyen állítható a gyakran és ritkán használt, valamint az archív rétegek közt anélkül, hogy az adatokat át kellene helyezni egyik fiókból a másikba. Az archív blobok rehidratálásától eltekintve a rétegváltások azonnal megtörténnek. Egy adott fiók tartalmazhat blobokat egyszerre akár mindhárom rétegben is. Azok a blobok, amelyekhez nincs kifejezetten hozzárendelve szint, a fiók hozzáférési szintjének beállítását öröklik.

Ha szeretné ezeket az előzetes verzióban elérhető szolgáltatásokat használni, kövesse az [Azure Archive and Blob-Level Tiering (Archív és blobszintű rétegzés az Azure-ban) című blogbejelentésben](https://azure.microsoft.com/blog/announcing-the-public-preview-of-azure-archive-blob-storage-and-blob-level-tiering) közzétett utasításokat.

Az alábbiakban felsorolunk néhány, az előzetes verzióban a blobszintű rétegzésre vonatkozó korlátozást:

* Az előzetes verzióra való sikeres regisztrációt követően csak az USA 2. keleti régiójában létrehozott új Blob Storage-fiókok támogatják az archív tárolást.

* Az előzetes verzióra való sikeres regisztrációt követően csak a nyilvános régiókban létrehozott új Blob Storage-fiókok támogatják a blobszintű rétegzést.

* A blobszintű rétegzés és az archív tárolás csak az [LRS] (../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#locally-redundant-storage) tárolóban támogatottak. A [GRS](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#geo-redundant-storage) és az [RA-GRS](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#read-access-geo-redundant-storage) támogatása a jövőben valósul meg.

* A pillanatképekkel rendelkező blobok rétegének módosítása nem lehetséges.

* Az archív tárolóban lévő blobok nem másolhatók, és pillanatképek sem készíthetők róluk.

## <a name="comparison-of-the-storage-tiers"></a>A tárolási rétegek összehasonlítása

Az alábbi táblázat a gyakran és ritkán használt adatok tárolási rétegeit hasonlítja össze. Az archív blobszintű tárolás még csak előzetes verzióban érhető el, ezért nincsenek vonatkozó SLA-k.

| | **Gyakran használt adatok tárolási rétege** | **Ritkán használt adatok tárolási rétege** |
| ---- | ----- | ----- |
| **Rendelkezésre állás** | 99.9% | 99% |
| **Rendelkezésre állás** <br> **(RA-GRS olvasások)**| 99.99% | 99.9% |
| **Használati díjak** | Magasabb tárolási, alacsonyabb hozzáférési és tranzakciós költségek | Alacsonyabb tárolási, magasabb hozzáférési és tranzakciós költségek |
| **Minimális objektumméret** | N/A | N/A |
| **Minimális tárolási időtartam** | N/A | N/A |
| **Késés** <br> **(Az első bájtig eltelt idő)** | ezredmásodperc | ezredmásodperc |
| **Méretezhetőségi és teljesítménycélok** | Ugyanazok, mint az általános célú tárfiókok esetében | Ugyanazok, mint az általános célú tárfiókok esetében |

> [!NOTE]
> A Blob Storage-fiókok ugyanazokat a méretezhetőségi és teljesítménycélokat támogatják, mint az általános célú tárfiókok. További információkért lásd: [Azure Storage Scalability and Performance Targets](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) (Az Azure Storage méretezhetőségi és teljesítménycéljai).


## <a name="pricing-and-billing"></a>Árak és számlázás
A Blob Storage-fiókok a tárolási rétegen alapuló árképzési modellt alkalmaznak a blobtároláshoz. A Blob Storage-fiókok használatakor az alábbi számlázási szempontok érvényesek:

* **Tárolási költségek**: Az adattárolás díja a tárolt adatok mennyisége mellett a tárolási rétegtől függ. A ritkán használt adatok tárolási rétegének gigabájtonkénti költsége alacsonyabb, mint a gyakran használt adatok tárolási rétegéé.

* **Adat-hozzáférési költségek**: A ritkán használt adatok tárolási rétege esetében gigabájtonkénti adat-hozzáférési díjat kell fizetni az adatolvasásokért és -írásokért.

* **Tranzakciós költségek**: Mindkét réteg esetében tranzakciónkénti díjat kell fizetni. A tranzakciónkénti költség azonban magasabb a ritkán használt adatok, mint a gyakran használt adatok tárolási rétegének esetében.

* **Georeplikációs adatátviteli költségek**: Ez csak a georeplikációval konfigurált fiókok esetében érvényes, beleértve a GRS-t és az RA-GRS-t. A georeplikációs adatátvitel gigabájtonkénti díj ellenében érhető el.

* **Kimenő adatátviteli költségek**: A kimenő adatátvitel (azaz az adott Azure-régióból kivitt adatok) esetében gigabájtalapú sávszélesség-használati díjak lépnek fel, csakúgy, mint az általános célú tárfiókok esetében.

* **A tárolási réteg módosítása**: a tárolási rétegnek a ritkán használt adatok rétegéről a gyakran használt adatok rétegére való váltása esetében felmerülő díj minden váltás esetében megegyezik a tárfiókban lévő összes adat beolvasásának költségével. A gyakran használt adatok tárolási rétegéről a ritkán használt adatok tárolási rétegére való váltás azonban díjmentes.

> [!NOTE]
> A Blob Storage-fiókok árképzési modelljével kapcsolatos további részletekért lásd [Az Azure Storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage/) lapot. A kimenő adatátviteli díjakkal kapcsolatos további részletekért lásd az [Adatforgalmi díjszabás](https://azure.microsoft.com/pricing/details/data-transfers/) lapot.

## <a name="quickstart"></a>Első lépések

Ebben a szakaszban bemutatjuk a következő forgatókönyveket az Azure Portal használatával kapcsolatban:

* Blob Storage-fiók létrehozása.
* Blob Storage-fiók kezelése.

A következő példákban nem állíthatja a hozzáférés szintet archívra, mivel ez a beállítás a teljes tárfiókra vonatkozik. Az archív réteg csak egy adott blobra vonatkozóan állítható be.

### <a name="create-a-blob-storage-account-using-the-azure-portal"></a>Blob Storage-fiók létrehozása az Azure Portal használatával

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).

2. A Központ menüben válassza az **Új** > **Adatok + tárolás** > **Tárfiók** elemet.

3. Adja meg a tárfiók nevét.
   
    A névnek globálisan egyedinek kell lennie. A név része lesz a tárfiókban található objektumok elérése használt URL-címnek.  

4. Válassza a **Resource Manager** lehetőséget üzemi modellként.
   
    A rétegezett tárolás csak a Resource Manager tárfiókjaival használható. Ez az új erőforrások ajánlott üzembe helyezési modellje. További információk: [Az Azure Resource Manager áttekintése](../../azure-resource-manager/resource-group-overview.md).  

5. A Fiók típusa legördülő listában válassza a **Blobtároló** elemet.
   
    Itt választhatja ki a tárfiók típusát. A rétegezett tárolás nem érhető el az általános célú tárolóknál, csak a Blobtároló típusú fiókban.     
   
    Vegye figyelembe, hogy ha ezt választja, a teljesítményréteg beállítása Standard lesz. A rétegezett tárolás nem érhető el a Prémium teljesítményrétegnél.

6. Válassza ki a tárfiók replikálási beállítását: **LRS**, **GRS** vagy **RA-GRS**. Az alapértelmezett beállítás az **RA-GRS**.
   
    LRS = helyileg redundáns tárolás, GRS = georedundáns tárolás (2 régió), RA-GRS = olvasási hozzáférésű georedundáns tárolás (2 régió, olvasási hozzáférés a másodikhoz).
   
    Az Azure Storage replikálási beállításaival kapcsolatos további részleteket lásd: [Azure Storage replication](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) (Az Azure Storage replikációja).

7. Válassza ki az igényeinek megfelelő tárolási réteget: a **Hozzáférési szint** beállításnál válassza a **Ritka** vagy a **Gyakori** lehetőséget. Az alapértelmezett réteg a **gyakran használt adatok**. 

8. Válassza ki az előfizetést, amelyikben az új tárfiókot létre szeretné hozni.

9. Adjon meg egy új erőforráscsoportot, vagy válasszon ki egy meglévőt. További információ az erőforráscsoportokkal kapcsolatban: [Az Azure Resource Manager áttekintése](../../azure-resource-manager/resource-group-overview.md).

10. Válassza ki a tárfiók régióját.

11. Kattintson a **Létrehozás** parancsra a tárfiók létrehozásához.

### <a name="change-the-storage-tier-of-a-blob-storage-account-using-the-azure-portal"></a>Blob Storage-fiók tárolási rétegének módosítása az Azure Portal használatával

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).

2. A tárfiókjának eléréséhez válassza az Összes erőforrás lehetőséget, majd válassza ki a tárfiókját.

3. A Beállítások panelen kattintson a **Konfiguráció** elemre a fiók konfigurációjának megtekintéséhez és/vagy megváltoztatásához.

4. Válassza ki az igényeinek megfelelő tárolási réteget: a **Hozzáférési szint** beállításnál válassza a **Ritka** vagy a **Gyakori** lehetőséget.

5. Kattintson a panel tetején lévő Mentés elemre.

> [!NOTE]
> A tárolási rétegek módosítása további díjakat vonhat maga után. További részleteket a következő szakaszban talál: [Árak és számlázás](#pricing-and-billing).


## <a name="evaluating-and-migrating-to-blob-storage-accounts"></a>A lehetőség mérlegelése és áttérés a Blob Storage-fiókokra
Ennek a résznek a célja, hogy segítséget nyújtson a felhasználóknak a Blob Storage-fiókok használatára való zökkenőmentes váltásban. Két felhasználói forgatókönyv közül választhat:

* Rendelkezik egy meglévő általános célú tárfiókkal, és szeretné kiértékelni a Blob Storage-fiók megfelelő tárolási rétegére való áttérést.
* Úgy döntött, hogy Blob Storage-fiókot fog használni, vagy már van egy ilyen fiókja, és szeretné meghatározni, hogy a gyakran használt adatok tárolási rétegét vagy a ritkán használt adatok tárolási rétegét célszerű-e használnia.

Mindkét esetben az első teendő a Blob Storage-fiókban tárolt adataihoz kapcsolódó tárolási és hozzáférési költségek kiszámítása, és az eredmények összehasonlítása az aktuális költségekkel.

## <a name="evaluating-blob-storage-account-tiers"></a>A Blob Storage-fiókok rétegeinek kiértékelése

A Blob Storage-fiókban lévő adatok tárolási és hozzáférési költségeire vonatkozó becslés elkészítéséhez ki kell értékelnie a jelenlegi használati módot vagy a várható használati módot. Általában a következőket érdemes figyelembe venni:

* Tárhelyhasználat – Mennyi adatot tárol, és ez milyen mértékben változik havi szinten?

* Tároló-hozzáférési minta – Mennyi adatot olvas és ír a fiókba (beleértve az új adatokat is)? Hány tranzakciót használ az adatok eléréséhez, és ezek milyen típusú tranzakciók?

## <a name="monitoring-existing-storage-accounts"></a>A meglévő tárfiókok figyelése

A meglévő tárfiókok figyeléséhez és az ezzel kapcsolatos adatgyűjtéshez nyújt segítséget az Azure Storage Analytics, amellyel naplózhatja a tárfiókokat, és a fiókokra vonatkozó mérőszámokat kaphat. A Storage Analytics olyan mérőszámokat tárol, amelyek a Blob Storage szolgáltatáshoz érkező kérések összesített tranzakcióstatisztikáját és kapacitási adatait tartalmazzák mind az általános célú tárfiókok, mind a Blob Storage-fiókok esetében. Ezeket az adatokat a jól ismert táblák tárolják az adott tárfiókban.

A további részleteket lásd: [About Storage Analytics Metrics](https://msdn.microsoft.com/library/azure/hh343258.aspx) (A Storage Analytics mérőszámainak áttekintése) és [Storage Analytics Metrics Table Schema](https://msdn.microsoft.com/library/azure/hh343264.aspx) (A Storage Analytics mérőszámainak táblasémája).

> [!NOTE]
> A Blob Storage-tárfiókok a tábla szolgáltatásvégpontját csak az adott fiók mérőszámadatainak tárolásához és eléréséhez jelenítik meg.

A Blob Storage szolgáltatás tárolófelhasználásának figyeléséhez engedélyeznie kell a kapacitási mérőszámot.
Ha ez a mérőszám engedélyezve van, a rendszer naponta rögzíti a tárfiók Blob szolgáltatásának kapacitásadatait, és létrehoz egy táblabejegyzést az adott tárfiók *$MetricsCapacityBlob* táblájában.

A Blob Storage szolgáltatás adat-hozzáférési mintájának figyeléséhez engedélyeznie kell az óránkénti tranzakció-mérőszámot az API szintjén. Amikor engedélyezve van ez a mérőszám, a rendszer óránként összesíti az API-tranzakciókat, és táblabejegyzést hoz létre az adott tárfiók *$MetricsHourPrimaryTransactionsBlob* táblájában. A *$MetricsHourSecondaryTransactionsBlob* tábla a másodlagos végpontra rögzíti a tranzakciókat RA-GRS-tárfiókok használata esetében.

> [!NOTE]
> Abban az esetben, ha rendelkezik egy általános célú tárfiókkal, amelyben lapblobokat és virtuálisgép-lemezek tárol a blokkblobok és a hozzáfűző blobok adatai mellett, akkor ez a becslési folyamat nem alkalmazható. Ennek az az oka, hogy nem lehet megkülönböztetni a kapacitás és a tranzakciók mérőszámait a blob típusa alapján az olyan blokkblobok és hozzáfűző blobok esetében, amelyek áttelepíthetők a Blob Storage-tárfiókba.

Azt javasoljuk, hogy az adatfelhasználás és -hozzáférés megfelelő mintájának előállításához olyan megőrzési időszakot válasszon a mérőszámhoz, amely a megfelel az Ön használati szokásainak, és extrapolálja az adatokat. Az egyik lehetőség az, hogy 7 napig őrzi meg a mérőszámadatokat, és minden héten összegyűjti az adatokat a hónap végén elvégzendő elemzéshez. A másik lehetőség az, hogy az utolsó 30 nap mérőszámadatait őrzi meg, és a 30 napos időszak végén hajtja végre az adatok összegyűjtését és elemzését.

További tudnivalók a mérőszámadatok engedélyezésével, gyűjtésével és megtekintésével kapcsolatban: [Enabling Azure Storage metrics and viewing metrics data](../common/storage-enable-and-view-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) (Az Azure Storage mérőszámainak engedélyezése és a mérőszámadatok megtekintése).

> [!NOTE]
> Az elemzési adatok tárolása, elérése és letöltése ugyanúgy díjhoz kötött, mint a normál felhasználói adatok használata.

### <a name="utilizing-usage-metrics-to-estimate-costs"></a>Költségbecslés a használati mérőszámok alapján

### <a name="storage-costs"></a>Tárolási költségek

A kapacitási mérőszám *$MetricsCapacityBlob* táblájának *„data”* sorkulcsú utolsó bejegyzése mutatja a felhasználói adatok által igénybe vett tárolókapacitást. A kapacitási mérőszám *$MetricsCapacityBlob* táblájának *„analytics”* sorkulcsú utolsó bejegyzése mutatja az elemzési naplók által igénybe vett tárolókapacitást.

A felhasználói adatok és az elemzési naplók (ha engedélyezve vannak) által igénybe vett teljes kapacitás alapján lehet megbecsülni a tárfiókban lévő adatok tárolási költségét. Ugyanezzel a módszerrel becsülhetők meg a blokkblobok és a hozzáfűző blobok tárolási költségei az általános célú tárfiókokban.

### <a name="transaction-costs"></a>Tranzakciós költségek

A tranzakciók mérőszámának táblájában az adott API-hoz tartozó bejegyzések *„TotalBillableRequests”* összege mutatja az adott API tranzakcióinak teljes számát. *Például* az adott időszakba eső *„GetBlob”* tranzakciók száma a *„user;GetBlob”* sorkulcsú bejegyzések összes számlázható kérelmének összege alapján számítható ki.

A Blob Storage-tárfiókok tranzakciós költségeinek kiszámításához a tranzakciókat három csoportra kell felosztania, mivel ezekhez különböző árak tartoznak.

* Írási tranzakciók, például *„PutBlob”*, *„PutBlock”*, *„PutBlockList”*, *„AppendBlock”*, *„ListBlobs”*, *„ListContainers”*, *„CreateContainer”*, *„SnapshotBlob”* és *„CopyBlob”*.
* Törlési tranzakciók, például *„DeleteBlob”* és *„DeleteContainer”*.
* Minden egyéb tranzakció.

Az általános célú tárfiókok tranzakciós költségeinek becsléséhez összesítenie kell az összes tranzakciót, függetlenül a művelettől és az API-tól.

### <a name="data-access-and-geo-replication-data-transfer-costs"></a>Az adatok hozzáférésének és a georeplikációs adatok átvitelének költségei

Bár a tároló elemzése nem jelzi a tárfiókból olvasott és a tárfiókba írt adatok mennyiségét, a tranzakciók mérőszámának táblája alapján lehetőség van megközelítőleges becslésre. A tranzakciók mérőszámának táblájában az adott API-hoz tartozó bejegyzések *„TotalIngress”* összege mutatja az adott API bejövő adatainak teljes mennyiségét bájtban. Hasonlóképpen a *„TotalEgress”* összege a kimenő adatok teljes mennyiségét mutatja bájtban.

A Blob Storage-tárfiókok adat-hozzáférési költségeinek kiszámításához a tranzakciókat két csoportra kell felosztania. 

* A tárfiókból lekért adatok mennyisége a *„TotalEgress”* összegéből becsülhető meg, elsődlegesen a *„GetBlob”* és a *„CopyBlob”* művelet alapján.

* A tárfiókba írt lekért adatok mennyisége a *„TotalIngress”* összegéből becsülhető meg, elsődlegesen a *„PutBlob”*, a *„PutBlock”*, a *„CopyBlob”* és az *„AppendBlock”* művelet alapján.

A Blob Storage-tárfiókok georeplikációs adatátviteli költségei szintén az írt adatok mennyiségének becslése alapján számítható ki GRS- vagy RA-GRS-tárfiókok használata esetében.

> [!NOTE]
> Ha részletesebb példát szeretne látni a gyakran használt és a ritkán használt adatok tárolási rétegének használatához kapcsolódó költségek kiszámítására, tekintse át a *„What are Hot and Cool access tiers and how should I determine which one to use?”* (Mi a gyakran használt adatok rétege és a ritkán használt adatok rétege, és hogyan határozhatom meg, hogy melyiket kell használnom?) című gyakori kérdéseket az [Azure Storage díjszabását tartalmazó oldalon](https://azure.microsoft.com/pricing/details/storage/).
 
## <a name="migrating-existing-data"></a>Meglévő adatok áttelepítése

A Blob Storage-fiókok kifejezetten blokkblobok és hozzáfűző blobok tárolására készültek. A meglévő általános célú tárfiókok, amelyek a blobok mellett táblák, üzenetsorok, fájlok és lemezek tárolását is lehetővé teszik, nem konvertálhatóak Blob Storage-fiókká. A tárolási rétegek használatához létre kell hoznia egy új Blob Storage-fiókot, és áttelepíteni meglévő adatait az újonnan létrehozott fiókra.

A meglévő adatok helyszíni tárolóeszközökről, külső felhőtárolási szolgáltatókból vagy meglévő általános célú Azure-tárfiókokból Blob Storage-fiókokba való áttelepítéséhez az alábbi módszereket használhatja:

### <a name="azcopy"></a>AzCopy

Az AzCopy egy Windows parancssori segédprogram, amely az adatok az Azure Storage szolgáltatásba vagy onnan máshová való nagyteljesítményű másolására lett kifejlesztve. Az AzCopy használatával adatait átmásolhatja Blob Storage-fiókjába meglévő általános célú tárfiókjából, vagy feltölthet adatokat helyi tárolóeszközeiről a Blob Storage-fiókba.

További részletekért lásd: [Transfer data with the AzCopy Command-Line Utility](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) (Adatátvitel az AzCopy parancssori segédprogrammal).

### <a name="data-movement-library"></a>Adatátviteli könyvtár

Az Azure Storage a .NET-keretrendszerhez készült adatátviteli könyvtára az AzCopyt működtető alapvető adatátviteli keretrendszeren alapul. A könyvtár az AzCopyhoz hasonló nagy teljesítményű, megbízható és könnyű adatátviteli műveletekhez készült. Így az AzCopy által kínált szolgáltatások előnyeit teljes mértékben kiaknázhatja alkalmazásában natív módon, anélkül, hogy ehhez az AzCopy külső példányait kellene futtatnia vagy felügyelnie.

További részletek: [Azure Storage adatátviteli könyvtár a .Net-keretrendszerhez](https://github.com/Azure/azure-storage-net-data-movement)

### <a name="rest-api-or-client-library"></a>REST API vagy ügyfélkódtár

Az adatok a Blob Storage-fiókra való áttelepítéséhez létrehozhat egy egyéni alkalmazást az Azure ügyfélkódtárai vagy az Azure Storage szolgáltatások REST API felülete segítségével. Az Azure Storage gazdag ügyfélkódtárakat biztosít több nyelvhez és platformhoz is, beleértve a következőket: .NET, Java, C++, Node.JS, PHP, Ruby és Python. Az ügyfélkódtárak olyan fejlett képességeket biztosítanak, mint az újrapróbálkozási logika, a naplózás vagy a párhuzamos feltöltések. Fejleszthet közvetlenül a REST API-n is, amely minden, HTTP-/HTTPS-kérelmek létrehozására alkalmas nyelven meghívható.

További részletekért lásd: [Ismerkedés az Azure Blob Storage szolgáltatással](storage-dotnet-how-to-use-blobs.md).

> [!NOTE]
> Az ügyféloldali titkosítással titkosított blobok a titkosítással kapcsolatos metaadatokat a blobbal együtt tárolják. Ezért elengedhetetlen, hogy a másolási műveletek során a blob metaadatai, különösen a titkosítással kapcsolatos metaadatok megmaradjanak. Ha a blobokat a metaadatok nélkül másolja, a blob tartalma nem lesz újból lekérhető. A titkosítással kapcsolatos metaadatok további részletei: [Az Azure Storage ügyféloldali titkosítása](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
 
## <a name="faq"></a>GYIK

1. **A meglévő tárfiókok továbbra is elérhetőek?**
   
    Igen, a meglévő tárfiókok továbbra is elérhetőek változatlan árképzéssel és funkcionalitással.  Nem érhető el hozzájuk a tárolási rétegek kiválasztásának lehetősége, és a jövőben sem rendelkeznek majd ezzel a képességgel.

2. **Miért és mikor érdemes elkezdenem Blob Storage-fiókot használni?**
   
    A Blob Storage-fiókok kifejezetten blobok tárolására készültek, így új blobközpontú szolgáltatások érhetők el hozzájuk. A továbbiakban a Blob Storage-fiók az ajánlott módszer a blobok tárolására, mivel a későbbi képességek, például a hierarchikus tárolás és a rétegválasztás ezen fióktípus alapján lesz bevezetve. Azonban Ön döntheti el üzleti igényei alapján, mikor kíván áttérni.

3. **Konvertálhatom meglévő tárfiókomat Blob Storage-fiókká?**
   
    Nem. A Blob Storage-fiók egy eltérő jellegű tárfiók, és ezért új fiókként kell létrehoznia, és az adatokat a korábbiakban leírtak szerint áttelepítenie.

4. **Tárolhatok objektumokat mindkét tárolási rétegben egyazon fiók esetében?**
   
    A *„Hozzáférési szint”* attribútum a fiók szintjén beállított tárolási réteg értékét jelzi, és a fiókban lévő összes objektumra vonatkozik. A blobszintű rétegzés (előzetes verzió) szolgáltatással azonban külön megadhatja a hozzáférési szintet az adott blobokon, amely felülírja majd a fiókon beállított hozzáférési szintet. 

5. **Módosíthatom a Blob Storage-fiókom tárolási rétegét?**
   
    Igen. A tárolási réteget úgy változtathatja meg, hogy a tárfiókban megadja a *„Hozzáférési szint”* attribútum kívánt értékét. A tárolási réteg módosítása az adott fiókban lévő összes objektumra vonatkozik. A tárolási rétegnek a gyakran használt adatok rétegéről a ritkán használt adatok rétegére való váltása nem jár többletköltséggel, a ritkán használt adatok rétegéről a gyakran használt adatok rétegére való váltás azonban gigabájtonkénti beolvasási költséggel jár a fiókban lévő összes adatra vonatkozóan.

6. **Milyen gyakran módosíthatom a Blob Storage-fiókom tárolási rétegét?**
   
    Bár nem határoztunk meg korlátozást a tárolási réteg módosításának gyakoriságára vonatkozóan, vegye figyelembe, hogy a tárolási rétegnek a ritkán használt adatok rétegéről a gyakran használt adatok rétegére való váltása jelentős költségekkel jár. Nem javasoljuk, hogy gyakran váltson a tárolási rétegek között.

7. **A gyakran használt adatok tárolási rétegében és a ritkán használt adatok tárolási rétegében eltérően viselkednek a blobok?**
   
    A gyakran használt adatok tárolási rétegében a blobok ugyanolyan késéssel rendelkeznek, mint az általános célú tárfiókok esetében. A ritkán használt adatok tárolási rétegében a blobok hasonló késéssel rendelkeznek (ezredmásodpercben), mint az általános célú tárfiókok esetében.
   
    A ritkán használt adatok tárolási rétegében a blobok rendelkezésre állási szolgáltatási szintje (SLA) kissé alacsonyabb, mint a gyakran használt adatok tárolási rétegében. További információkért lásd: [SLA a következőhöz: Storage](https://azure.microsoft.com/support/legal/sla/storage).

8. **Tárolhatok lapblobokat és virtuális gépek lemezeit a Blob Storage-fiókban?**
   
    A Blob Storage-fiókok csak a blokkblobokat és a hozzáfűző blobokat támogatják, a lapblobokat nem. Az Azure virtuális gépek lemezei lapblobokon alapulnak, ezért a Blob Storage-fiókok nem használhatóak virtuális gépek lemezeinek tárolására. Azonban lehetséges a virtuális gépek lemezeinek biztonsági másolatait blokkblobként tárolni a Blob Storage-fiókokban.

9. **Módosítanom kell a meglévő alkalmazásaimat a Blob Storage-fiókok használatához?**
   
    A Blob Storage-fiókok 100%-ban API-konzisztensek az általános célú tárfiókokkal a blokkblobok és a hozzáfűző blobok esetében. Ha az alkalmazás blokkblobokat vagy hozzáfűző blobokat használ, és a [Storage szolgáltatások REST API felülete](https://msdn.microsoft.com/library/azure/dd894041.aspx) 2014. 02. 14-i vagy újabb verzióját használja, az alkalmazásnak működnie kellene. Ha a protokoll egy régebbi verzióját használja, frissítenie kell az alkalmazást, hogy az az új verziót használja, és mindkét típusú tárfiókkal zökkenőmentesen működjön. Általánosságban véve mindig a legújabb verzió használatát javasoljuk, függetlenül a használt tárfiók típusától.

10. **Változik a felhasználói élmény?**
    
    A Blob Storage-fiókok nagyon hasonlóak az általános célú tárfiókokhoz a blokk- és hozzáfűző blobok tárolása tekintetében, és támogatják az Azure Storage minden lényeges szolgáltatását, beleértve a nagy tartósságot, valamint a magas szintű rendelkezésre állást, méretezhetőséget, teljesítményt és biztonságot. A Blob Storage-fiókokra jellemző konkrét szolgáltatásoktól és korlátozásoktól, valamint a fent kiemelt tárolási rétegektől eltekintve minden más változatlan marad.

## <a name="next-steps"></a>Következő lépések

### <a name="evaluate-blob-storage-accounts"></a>A Blob Storage-fiókok értékelése

[A Blob Storage-fiókok elérhetőségének ellenőrzése régió alapján](https://azure.microsoft.com/regions/#services)

[Aktuális tárfiókjai használatának értékelése az Azure Storage mérőszámainak engedélyezésével](../common/storage-enable-and-view-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[A Blob Storage régió szerinti árképzésének megtekintése](https://azure.microsoft.com/pricing/details/storage/)

[Az adatátviteli díjszabás megtekintése](https://azure.microsoft.com/pricing/details/data-transfers/)

### <a name="start-using-blob-storage-accounts"></a>A Blob Storage-fiókok használatának megkezdése

[Ismerkedés az Azure Blob Storage szolgáltatással](storage-dotnet-how-to-use-blobs.md)

[Adatok áthelyezése az Azure Storage szolgáltatásba vagy onnan máshová](../common/storage-moving-data.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[Adatátvitel az AzCopy parancssori segédprogrammal](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[A tárfiókok tallózása és felfedezése](http://storageexplorer.com/)