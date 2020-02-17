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
ms.openlocfilehash: f2f6be1022a7100a23f49534f2c18fc951d56284
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368718"
---
# <a name="azure-blob-storage-hot-cool-and-archive-access-tiers"></a>Azure Blob Storage: gyakori, ritka elérésű és archív hozzáférési szintek

Az Azure Storage különböző hozzáférési szinteket kínál, amelyek lehetővé teszik a blob-objektumok-adattárolást a legköltséghatékonyabb módon. Az elérhető hozzáférési szintek a következők:

- A gyakran használt adatok tárolására optimalizált.
- A ritkán használt és a legalább 30 napig tárolt adatok tárolására optimalizált.
- A ritkán használt és tárolt adatok tárolására optimalizált **archiválás** – legalább 180 napig, rugalmas késési követelményekkel (az órák sorrendje szerint).

A különböző hozzáférési szinteket a következő szempontok vonatkoznak:

- Csak a gyors és a lassú elérésű hozzáférési szint állítható be a fiók szintjén. Az archív hozzáférési szint nem érhető el a fiók szintjén.
- A gyors és a lassú elérésű, valamint az archiválási szintek a feltöltéskor vagy a feltöltés után is megadhatók a blob szintjén.
- A ritka elérésű hozzáférési szintben lévő adatok némileg alacsonyabb rendelkezésre állást biztosíthatnak, de továbbra is magas tartósságot, lekérési késést és átviteli sebességet igényelnek, hasonlóan a gyors adatokhoz. A ritkán használt adatok esetében a rendelkezésre állási szolgáltatói szerződés (SLA) és a gyakran használt adatokhoz képest magasabb hozzáférési költségek elfogadható kompromisszumot jelentenek az alacsonyabb tárolási költségek mellett.
- Az archiválási tároló tárolja az adatok kapcsolat nélküli üzemmódját, és a legalacsonyabb tárolási költségeket, valamint a legmagasabb adattisztítási és-hozzáférési költségeket is biztosítja.

A felhőben tárolt adatmennyiség exponenciális ütemben növekszik. A növekvő tárolási szükségletek költségeinek kezelése érdekében hasznos lehet az adatokat olyan attribútumok alapján szervezni, mint a hozzáférés gyakorisága vagy a tervezett megőrzési időtartam, így optimalizálhatók a költségek. A felhőben tárolt adat a létrehozás, a feldolgozás és a hozzáférés időtartama alapján különbözhet. Egyes adatokat aktívan használnak és módosítanak teljes élettartamuk során. Egyes adatokat élettartamuk korai szakaszában sokat használnak, az adatok életkorának növekedésével azonban a hozzáférések mennyisége drasztikusan csökken. Bizonyos adatforgalom üresjáratban van a felhőben, és ritkán fordul elő, hogy a tárolás után is hozzáférjen.

Az adathozzáférési forgatókönyvek mindegyike egy másik hozzáférési szinten érhető el, amely egy adott hozzáférési mintára van optimalizálva. A gyakori, ritka elérésű és archív hozzáférési szintekkel az Azure Blob Storage ezt az igényt külön díjszabási modellel rendelkező, differenciált hozzáférési szintekhez szükséges.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-accounts-that-support-tiering"></a>Rétegezést támogató Storage-fiókok

A gyakori, a lassú és az archív objektumok tárolási adatrétege csak a blob Storage és a általános célú v2 (GPv2) fiókok esetében támogatott. A általános célú v1-(GPv1-) fiókok nem támogatják a rétegek használatát. Az ügyfelek könnyedén átalakíthatók meglévő GPv1-vagy blob Storage-fiókjaikat, hogy GPv2 a fiókokat a Azure Portalon keresztül. A GPv2 új díjszabást és funkciókat biztosít a blobokhoz, fájlokhoz és várólistákhoz. Bizonyos funkciók és árak csökkentése csak GPv2-fiókokban lehetséges. Értékelje ki a GPv2-fiókokat a díjszabás átfogó áttekintése után. Bizonyos számítási feladatok drágábbak lehetnek a GPv2, mint a GPv1. További információkat az [Azure Storage-fiókok áttekintésében](../common/storage-account-overview.md) találhat.

A blob Storage és a GPv2-fiókok a **hozzáférési szint** attribútumot teszik elérhetővé a fiók szintjén. Ez az attribútum lehetővé teszi az alapértelmezett hozzáférési szint megadását minden olyan blob esetében, amely nem rendelkezik explicit beállítással az objektum szintjén. Az objektum szintjén beállított szinttel rendelkező objektumok esetén a fiók szintje nem lesz érvényes. Az archiválási szint csak az objektum szintjén alkalmazható. A hozzáférési szintek közötti váltást bármikor megteheti.

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

Az archív hozzáférési szint a legalacsonyabb tárolási díjat számítja ki. A gyakori és a ritka elérésű szintekhez képest azonban nagyobb Adatlekérdezési költségekkel jár. Az archiválási szinten tárolt adatok több órát is igénybe vehetnek. Az adatoknak az archiválási szinten kell maradniuk legalább 180 napig, vagy egy korai törlési díjat kell fizetni.

Míg a Blobok archiválási tárolóban találhatók, a blob-adatok offline állapotban vannak, és nem olvashatók, írhatók felül vagy módosíthatók. Az archívumban található Blobok olvasásához vagy letöltéséhez először egy online szintre kell kiszáradni. Az archív tárolóban nem lehet pillanatképeket készíteni egy blobról. A blob metaadatai azonban online és elérhető állapotban maradnak, és lehetővé teszik a blob és a hozzá tartozó tulajdonságok listázását. Az archív Blobok esetében az egyetlen érvényes művelet a GetBlobProperties, a GetBlobMetadata, a ListBlobs, a SetBlobTier, a CopyBlob és a DeleteBlob. További információért lásd [a blob-adatok rehidratálása az archív szintről](storage-blob-rehydration.md) című témakört.

Az archív hozzáférési szint használati forgatókönyvei például a következők:

- Hosszú távú biztonsági mentések, másodlagos biztonsági mentések és archiválási adatkészletek
- Eredeti (nyers) adatok, amelyeket a végső használható formába való feldolgozásukat követően is meg kell őrizni.
- Megfelelőségi és archiválási adatok, amelyeket hosszú ideig kell tárolni, azonban nagyon ritkán kell hozzáférni.

## <a name="account-level-tiering"></a>Fiók szintű rétegek

A Blobok mind a három hozzáférési szinten egyszerre létezhetnek ugyanabban a fiókban. Minden olyan blob, amely nem rendelkezik explicit módon hozzárendelt szintjével, a fiók hozzáférési szintje beállításból kikövetkezteti a szintet. Ha a hozzáférési szintet a fiókból származik, a **hozzáférési rétegek kikövetkeztetett** blob tulajdonsága "true" értékre van állítva, a **hozzáférési szintű** blob tulajdonság pedig megegyezik a fiók szintjével. A Azure Portal a _hozzáférési réteg kikövetkeztetett_ tulajdonsága a blob-hozzáférési szinttel jelenik meg, mint a **gyors (késleltetett)** vagy a ritka **(késleltetett)** .

A fiók hozzáférési rétegének módosítása a fiókban tárolt összes olyan _hozzáférési rétegre_ vonatkozik, amely nem rendelkezik explicit szintű készlettel. Ha bekapcsolja a fiók rétegét a melegről a lassúra, akkor az írási műveletek (10 000) után az összes blobra vonatkozóan csak a GPv2-fiókokat tartalmazó set szint használata után kell fizetnie. A blob Storage-fiókokban ez a változás díjmentes. Az olvasási műveletekért (10 000) és az adatok lekéréséhez (GB-onként) kell fizetnie, ha a lassúról a gyors elérésű blob Storage-ba vagy GPv2-fiókba vált.

## <a name="blob-level-tiering"></a>Blobszintű rétegezés

A blob szintű rétegek lehetővé teszik az adatok feltöltését az Ön által választott hozzáférési szintre a Blobok [elhelyezése](/rest/api/storageservices/put-blob) vagy a [letiltási lista](/rest/api/storageservices/put-block-list) műveleteivel, és az adatok rétegét az objektum szintjén módosíthatja a [blob szint beállítása](/rest/api/storageservices/set-blob-tier) művelet vagy az [életciklus-kezelési](#blob-lifecycle-management) funkció használatával. A szükséges hozzáférési szinthez feltöltheti az adatokat, majd a használati minták változása révén egyszerűen módosíthatja a blob-hozzáférési szintet a gyakori, ritka vagy archív rétegek között, anélkül, hogy az adatokat át kellene helyeznie a fiókok között. Az összes rétegbeli módosítási kérelem azonnal megtörténik, és a gyors és a lassú elérési szint közötti változások azonnaliek. A Blobok archívumból való rehidratálása azonban több órát is igénybe vehet.

Az utolsó blobszint-módosítás időpontja a **Hozzáférési szint utolsó módosítása** blobtulajdonságon keresztül érhető el. Amikor egy blobot felülír a gyakori vagy a ritka elérésű rétegben, az újonnan létrehozott blob örökli a blob azon rétegét, amelyet a rendszer felülírt, kivéve, ha az új blob-hozzáférési szintet explicit módon beállította a létrehozáshoz. Ha egy blob az archiválási szinten van, akkor nem lehet felülírni, ezért a blob feltöltése nem engedélyezett ebben a forgatókönyvben. 

> [!NOTE]
> Az archív tárolás és a blobszintű rétegezés csak a blokkblobokat támogatja. A pillanatképekkel rendelkező blokk-Blobok szintje jelenleg nem módosítható.

### <a name="blob-lifecycle-management"></a>BLOB-életciklus kezelése

Az Blob Storage életciklus-kezelés sokoldalú, szabályon alapuló szabályzatot kínál, amellyel átválthatja az adatait a legjobb hozzáférési rétegre, és lemondhatja az adatait az életciklusa végén. További információért lásd: [Az Azure Blob Storage életciklusának kezelése](storage-lifecycle-management-concepts.md) .  

> [!NOTE]
> A blokk blob Storage-fiókban (prémium szintű teljesítmény) tárolt adatok jelenleg nem állíthatók be gyors, ritka elérésű vagy archív verzióra a [blob-réteg beállítása](/rest/api/storageservices/set-blob-tier) vagy az Azure Blob Storage életciklus-kezelés használatával.
> Az adatok áthelyezéséhez a blob Storage-fiókból egy másik fiókban lévő, a [put blokk URL API](/rest/api/storageservices/put-block-from-url) -val vagy az API-t támogató AzCopy-verzióval szinkronban kell átmásolni a blobokat a blokkból.
> A *put blokk from URL* API szinkron módon másolja az adatait a-kiszolgálón, ami azt jelenti, hogy a hívás csak akkor fejeződik be, ha az összes adatok átkerülnek az eredeti kiszolgáló helyéről a célhelyre.

### <a name="blob-level-tiering-billing"></a>Blobszintű rétegezési számlázás

Ha egy blobot feltöltenek vagy áthelyeznek a gyakori, ritka vagy archív szintre, a rendszer a megfelelő arányban azonnal felszámítja a szintet.

Ha egy blobot egy hűvösebb rétegbe helyez át (a gyors > a lassú elérésű, a gyors > az archívumot vagy a lassú elérésű > archívumot), a művelet a célként megadott szintre írási műveletként történik, ahol az írási művelet (10 000) és a célként megadott adatírás (GB-onként) érvényes.

Ha egy blobot egy melegebb rétegbe helyez át (archivált > lassú elérésű, archív > gyors vagy lassú elérésű >), a művelet számlázása a forrás rétegből történik, ahol az olvasási művelet (10 000) és a forrás szint (GB-onként) Adatlekérdezési díja érvényes. A ritka elérésű vagy archív szintről áthelyezett blobok esetében korai törlésre vonatkozó díjak lehetnek érvényesek. Az [archivált adatok újraszárítása](storage-blob-rehydration.md) időt vesz igénybe, és az adatok az archiválási árakon lesznek felszámítva, amíg az adatok online állapotba nem állnak vissza, és a Blobok szintje gyakori vagy ritka A következő táblázat összefoglalja, hogyan történik a szintek változásainak számlázása:

| | **Írási díjak (művelet + hozzáférés)** | **Olvasási díjak (művelet + hozzáférés)**
| ---- | ----- | ----- |
| **SetBlobTier iránya** | gyors ><br> gyors > Archívum,<br> Cool-> Archívum | Archívum – > ritka elérésű,<br> archiválás – > gyors,<br> Cool > – gyors

### <a name="cool-and-archive-early-deletion"></a>Korai törlés a ritka elérésű és az archív szinten

A lassú rétegbe áthelyezett Blobok (csak a GPv2-fiókok esetében) egy 30 napos, korai törlési időszakra vonatkoznak. Az archiválási szintre áthelyezett Blobok egy 180 napos korai törlési időszakra vonatkoznak. A díj számlázása időarányosan történik. Ha például egy blobot az archívumba helyez át, majd 45 nap után törli vagy áthelyezi a gyors elérésű szintre, akkor a blobnak az archívumban való tárolásával megegyező korai törlési díjat kell fizetnie a 135 (180 mínusz 45) napon.

A korai törlést kiszámíthatja a blob tulajdonsággal, amelyet a **legutóbbi módosítással**lehet elvégezni, ha nem módosult a hozzáférési réteg. Ellenkező esetben akkor használhatja, ha a hozzáférési réteg utolsó módosításának ideje a következő: **hozzáférés-réteg-módosítási idő**. További információ a blob tulajdonságairól: [blob tulajdonságainak beolvasása](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties).

## <a name="comparing-block-blob-storage-options"></a>A blob Storage-beli blokkolási beállításainak összehasonlítása

Az alábbi táblázat a prémium szintű, a blob Storage és a gyakori, ritka elérésű és archív hozzáférési szintek összehasonlítását mutatja be.

|                                           | **Prémium szintű teljesítmény**   | **Gyors elérési szint** | **Hűvös szint**       | **Archiválási szint**  |
| ----------------------------------------- | ------------------------- | ------------ | ------------------- | ----------------- |
| **Rendelkezésre állás**                          | 99.9%                     | 99.9%        | 99%                 | Offline           |
| **Rendelkezésre állás** <br> **(RA-GRS olvasások)**  | N/A                       | 99.99%       | 99.9%               | Offline           |
| **Használati díjak**                         | Magasabb tárolási költségek, alacsonyabb hozzáférési és tranzakciós költségek | Magasabb tárolási költségek, alacsonyabb hozzáférés és tranzakciós költségek | Alacsonyabb tárolási költségek, magasabb hozzáférési és tranzakciós költségek | Legalacsonyabb tárolási költségek, legmagasabb hozzáférési és tranzakciós költségek |
| **Minimális objektumméret**                   | N/A                       | N/A          | N/A                 | N/A               |
| **Minimális tárolási időtartam**              | N/A                       | N/A          | 30 nap<sup>1</sup> | 180 nap
| **Késés** <br> **(Az első bájtig eltelt idő)** | Egy számjegyű ezredmásodperc | ezredmásodperc | ezredmásodperc        | óra<sup>2</sup> |

<sup>1</sup> a GPv2-fiókok ritka elérési szintjében lévő objektumok minimális megőrzési időtartama 30 nap. A blob Storage-fiókok nem rendelkeznek minimális megőrzési időtartammal a ritka elérési szinthez.

<sup>2</sup> Archive Storage jelenleg 2 rehidratált prioritást (magas és standard) támogat, amely különböző lekérési késéseket biztosít. További információkért lásd: [Blobok adatainak rehidratálása az archív szintről](storage-blob-rehydration.md).

> [!NOTE]
> A blob Storage-fiókok ugyanazt a teljesítmény-és méretezhetőségi célokat támogatják, mint az általános célú v2 Storage-fiókok. További információkért lásd [a blob Storage skálázhatósági és teljesítménybeli céljait](scalability-targets.md)ismertető témakört.

## <a name="quickstart-scenarios"></a>Rövid útmutatóul szolgáló forgatókönyvek

Ebben a szakaszban a következő forgatókönyveket mutatjuk be a Azure Portal és a PowerShell használatával:

- GPv2- vagy Blob Storage-fiók alapértelmezett hozzáférési szintjének módosítása.
- GPv2- vagy Blob Storage-fiókban található blob szintjének módosítása.

### <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>GPv2- vagy Blob Storage-fiók alapértelmezett hozzáférési szintjének módosítása

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).

1. A Azure Portal keresse meg és válassza ki az **összes erőforrás**elemet.

1. Válassza ki a tárfiókot.

1. A **Beállítások**területen válassza a **konfiguráció** lehetőséget a fiók konfigurációjának megtekintéséhez és módosításához.

1. Válassza ki az igényeinek megfelelő hozzáférési szintet: állítsa be a **hozzáférési szintet** a lassú **vagy** a **gyors**eléréshez.

1. Kattintson a felső **Mentés** gombra.

![Storage-fiók szintjeinek módosítása](media/storage-tiers/account-tier.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Az alábbi PowerShell-parancsfájl használatával módosíthatja a fiók szintjét. Az `$rgName` változót inicializálni kell az erőforráscsoport nevével. A `$accountName` változót inicializálni kell a Storage-fiók nevével. 
```powershell
#Initialize the following with your resource group and storage account names
$rgName = ""
$accountName = ""

#Change the storage account tier to hot
Set-AzStorageAccount -ResourceGroupName $rgName -Name $accountName -AccessTier Hot
```
---

### <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>BLOB szintjeinek módosítása GPv2-vagy blob Storage-fiókban
# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).

1. A Azure Portal keresse meg és válassza ki az **összes erőforrás**elemet.

1. Válassza ki a tárfiókot.

1. Válassza ki a tárolót, majd válassza ki a blobot.

1. A **blob tulajdonságainál**válassza a lehetőség **módosítása**lehetőséget.

1. Válassza a **gyors**, **a lassú vagy az** **archív** hozzáférési szintet. Ha a blob jelenleg archiválás alatt áll, és egy online szintre szeretné kiszáradni, akkor a **standard** vagy a **magas**rehidratálás prioritást is kiválaszthatja.

1. Kattintson a **Save (Mentés** ) gombra a lap alján.

![Storage-fiók szintjeinek módosítása](media/storage-tiers/blob-access-tier.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
A blob-rétegek módosításához a következő PowerShell-parancsfájl használható. Az `$rgName` változót inicializálni kell az erőforráscsoport nevével. A `$accountName` változót inicializálni kell a Storage-fiók nevével. A `$containerName` változót inicializálni kell a tároló nevével. A `$blobName` változót inicializálni kell a blob nevével. 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$containerName = ""
$blobName == ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Select the blob from a container
$blobs = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $context

#Change the blob’s access tier to archive
$blob.ICloudBlob.SetStandardBlobTier("Archive")
```
---

## <a name="pricing-and-billing"></a>Árak és számlázás

Az összes Storage-fiók díjszabási modellt használ az egyes Blobok szintjétől függően a blob Storage blokkolásához. Tartsa szem előtt az alábbi számlázási szempontokat:

- **Tárolási költségek**: Az adattárolás díja a tárolt adatok mennyisége mellett a hozzáférési rétegtől függ. A gigabájtonkénti költség csökken, ha a szint ritkábban használt adatokat tárol.
- **Adathozzáférési költségek**: az adathozzáférési költségek emelkednek, ha a szint ritkábban használt adatokat tárol. A lassú elérésű és az archív hozzáférési szinten tárolt adatokért a GB-os adatelérési díjat számítjuk fel a beolvasáshoz.
- **Tranzakciós költségek**: az összes olyan réteg esetében, amely növeli a szintet, megnövekszik a tranzakciós díj.
- **Georeplikációs adatátviteli költségek**: Ez csak a georeplikációval konfigurált fiókok esetében érvényes, beleértve a GRS-t és az RA-GRS-t. A georeplikációs adatátvitel gigabájtonkénti díj ellenében érhető el.
- **Kimenő adatátviteli költségek**: A kimenő adatátvitel (azaz az adott Azure-régióból kivitt adatok) esetében gigabájtalapú sávszélesség-használati díjak lépnek fel, csakúgy, mint az általános célú tárfiókok esetében.
- **A hozzáférési réteg módosítása**: a fiók hozzáférési rétegének módosítása azt eredményezi, hogy a hozzáférési rétegben lévő _késleltetett_ Blobok a fiókban tárolt, explicit szintű készlettel nem rendelkező Blobok esetében változnak. Az egyetlen blob hozzáférési szintjének módosításával kapcsolatos információkért tekintse meg a [blob szintű](#blob-level-tiering-billing)szinteken alapuló számlázást ismertető témakört.

> [!NOTE]
> A blokkos Blobok díjszabásával kapcsolatos további információkért lásd az [Azure Storage díjszabását](https://azure.microsoft.com/pricing/details/storage/blobs/) ismertető oldalt. A kimenő adatátviteli díjakkal kapcsolatos további információért lásd az [adatátviteli díjszabást](https://azure.microsoft.com/pricing/details/data-transfers/) ismertető lapot.

## <a name="faq"></a>GYIK

**Blob Storage vagy GPv2-fiókokat kell használnom, ha rétegezni szeretném az adataimat?**

Javasoljuk, hogy Blob Storage-fiókok helyett használjon GPv2-fiókokat a rétegzéshez kialakításához. A GPv2 a Blob Storage-fiókok által támogatott szolgáltatások mellett sok mást is támogat. A Blob Storage és GPv2-fiókok díjszabása majdnem teljesen megegyezik, azonban egyes új szolgáltatások és árengedmények csak GPv2-fiókokhoz lesznek elérhetők. A GPv1-fiókok nem támogatják a rétegek bevezetését.

A GPv1- és GPv2-fiókok díjszabási struktúrája eltér egymástól, ezért az ügyfeleknek érdemes mindkettőt alaposan áttekinteni, mielőtt a GPv2-fiókok használata mellett döntenek. Meglévő Blob Storage- vagy GPv1-fiókját könnyedén, egyetlen kattintással átalakíthatja GPv2-fiókká. További információkat az [Azure Storage-fiókok áttekintésében](../common/storage-account-overview.md) találhat.

**Tárolhatok objektumokat mind a három (gyakori, ritka és archív) hozzáférési rétegben ugyanabban a fiókban?**

Igen. A fiók szintjén beállított **hozzáférési szint** attribútum az alapértelmezett fióktípus, amely az adott fiókban lévő összes objektumra érvényes, explicit set szint nélkül. A blob szintű rétegek lehetővé teszik a hozzáférési szint beállítását az objektum szintjén, függetlenül attól, hogy a fiókhoz milyen hozzáférési szint van beállítva. Ugyanazon a fiókon belül a három hozzáférési réteg (gyakori, ritka elérésű vagy archív) egyikének blobja is létezhet.

**Módosíthatom a blob vagy GPv2 alapértelmezett hozzáférési szintjét?**

Igen, a Storage-fiók **hozzáférési rétegek** attribútumának beállításával módosíthatja az alapértelmezett fiók szintjét. A fiók rétegének módosítása a fiókban tárolt összes olyan objektumra vonatkozik, amely nem rendelkezik explicit szinttel (például **gyors (késleltetett)** vagy ritka **(késleltetett**)). A fiók rétegét a gyors és a lassú elérésű írási műveletre (10 000) állítja be a GPv2-fiókokban anélkül, hogy csak a készlet rétege legyen, és az olvasási műveletek (10 000) és az adatok beolvasása (GB-onként) a blob-tárolóban lévő összes blob esetében és GPv2 fiókokat.

**Beállíthatom a fiók alapértelmezett hozzáférési szintjét archív szintre?**

Nem. Csak a gyakori és ritka elérésű hozzáférési szintek állíthatók be alapértelmezett fiók-hozzáférési szintként. Az archív szint csak az objektumok szintjén állítható be. A blob feltöltésekor megadhatja, hogy az alapértelmezett fiók szintjétől függetlenül milyen hozzáférési szintet kell megadnia a gyors, a lassú és az archív elérési szintnek. Ez a funkció lehetővé teszi, hogy közvetlenül az Archívum szintjére írja az adatait, így az adatok a blob Storage-ban való létrehozása pillanatában költségmegtakarítást eredményezhet.

**Mely régiókban érhetők el a gyakori, ritka elérésű és archív hozzáférési szintek?**

A gyors és a lassú elérési szint a blob szintű rétegek mellett minden régióban elérhető. Az archív tárolási szint eleinte csak bizonyos régiókban lesz elérhető. A teljes listát a [Régiónként elérhető Azure-termékek](https://azure.microsoft.com/regions/services/) című részben tekintheti meg.

**A ritka elérésű hozzáférési szinten lévő Blobok eltérően működnek, mint a gyakori elérésű hozzáférési szint?**

A gyors elérésű rétegben lévő Blobok ugyanolyan késéssel rendelkeznek, mint a GPv1, a GPv2 és a blob Storage-fiókokban lévő Blobok. A ritka elérésű hozzáférési szinten lévő Blobok hasonló késéssel rendelkeznek (ezredmásodpercben), mint a GPv1, a GPv2 és a blob Storage-fiókokban. Az archív hozzáférési szinten lévő Blobok több órányi késéssel rendelkeznek a GPv1, a GPv2 és a blob Storage-fiókokban.

A ritka elérésű hozzáférési szinten lévő Blobok valamivel alacsonyabb rendelkezésre állási szolgáltatási szinttel (SLA) rendelkeznek, mint a gyors elérésű hozzáférési szintben tárolt Blobok. További információt a [tárolók rendelkezésreállási szolgáltatási szintjeit](https://azure.microsoft.com/support/legal/sla/storage/v1_2/) ismertető szakaszban talál.

**A műveletek megegyeznek a gyakori és a ritka elérésű, valamint az archív tárolási szint esetében?**

A gyakori és a ritka elérésű szint esetében minden művelet teljesen megegyezik. Az összes érvényes archiválási művelet, beleértve a GetBlobProperties, a GetBlobMetadata, a ListBlobs, a SetBlobTier és a DeleteBlob, a gyors és a ritka elérésű 100%. A blob-adatok nem olvashatók és nem módosíthatók az archiválási szinten, amíg újra nem hidratálja őket. archiválás közben csak a blob-metaadatok olvasási műveletei támogatottak.

**Amikor az archív tárolási szintről rehidratálok egy blobot a gyakori vagy ritka elérésű szintre, honnan tudom, hogy befejeződött-e a rehidratálás?**

A rehidratálás során a blob beolvasása művelettel kérdezheti le az **Archive status** attribútumot, és megerősítheti, hogy a szint módosítása befejeződött-e. Az állapot a célszinttől függően „rehydrate-pending-to-hot” (rehidratálás-folyamatban-a-gyakoriba) vagy „rehydrate-pending-to-cool” (rehidratálás-folyamatban-a-ritkába) lehet. A folyamat befejeztével a „archív állapot” tulajdonság törlődik, és a „hozzáférési szint” tulajdonság mutatja, hogy az új szint a gyakran vagy a ritkán használt szint-e. További információért lásd [a blob-adatok rehidratálása az archív szintről](storage-blob-rehydration.md) című témakört.

**Egy blob szintjének beállítása után mikor kezdődik a megfelelő díjak alapján történő számlázás?**

Az egyes Blobok számlázása a blob **hozzáférési szintje** tulajdonsága által jelzett szintek szerint történik. Amikor új online szintet állít be egy blobhoz, a **hozzáférési szintet** megadó tulajdonság azonnal az új szintet tükrözi az összes átmenet esetében. A Blobok offline archiválási szintről egy gyors vagy ritka elérésű szintre való rehidratálása azonban több órát is igénybe vehet. Ebben az esetben az archiválási díjakat kell fizetnie, amíg a rehidratálás be nem fejeződik, ekkor a **hozzáférési szint** tulajdonság az új szintet tükrözi. Az online szintre való rehidratálás után a blobot a gyakori vagy a ritka elérési árfolyamon számoljuk el.

**Hogyan eldönteni, hogy van-e korai törlési díj, amikor törli vagy áthelyezi a blobot a ritka vagy archív szintről?**

Minden blob, amelyet a ritka elérésű (csak GPv2-fiókok esetében) vagy archív tárolási szintről töröl vagy helyez át a vonatkozó 30, illetve 180 nap letelte előtt, a korai törléseknek megfelelő, időarányos költségeket von maga után. Megadhatja, hogy mennyi ideig legyenek a Blobok a lassú vagy az archív szinten a **hozzáférési szint módosítási idő** blob tulajdonságának megnyomásával, amely az utolsó réteg változásának bélyegzőjét adja meg. Ha a blob szintje soha nem módosult, akkor ellenőrizze az **utolsó módosított** blob-tulajdonságot. További információ: korai törlés a ritka elérésű [és az archív archívumban](#cool-and-archive-early-deletion).

**Melyik Azure-eszközök és SDK-k támogatják a blobszintű rétegezést és az archív tárolási szintet?**

Az Azure Portal, a PowerShell, valamint a parancssori felület eszközei, illetve a .NET-, Java-, Python- és Node.js-ügyfélkönyvtárak mind támogatják a blobszintű rétegezést és az archív tárolási szintet.  

**Mennyi adatot tárolhatok a gyakori és ritka elérésű, valamint az archív tárolási szinten?**

Az adattárolás és más korlátok a fiók szintjén vannak megadva, nem pedig hozzáférési szinten. Választhat, hogy az összes korlátot egy vagy mindhárom szinten használja-e. További információ: [a méretezhetőség és a teljesítményre vonatkozó célok a standard szintű Storage-fiókok esetében](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="next-steps"></a>Következő lépések

A GPv2-és blob Storage-fiókok gyors, ritka és archív verzióinak kiértékelése

- [A gyakori és ritka elérésű, valamint az archív tárolási szint rendelkezésre állásának ellenőrzése régiónként](https://azure.microsoft.com/regions/#services)
- [Azure Blob Storage-életciklus felügyelete](storage-lifecycle-management-concepts.md)
- [Tudnivalók a blob-adatok archiválási szintről való kiszárításáról](storage-blob-rehydration.md)
- [Annak megállapítása, hogy előnyös-e a prémium szintű teljesítmény az alkalmazásban](storage-blob-performance-tiers.md)
- [Aktuális tárfiókjai használatának értékelése az Azure Storage mérőszámainak engedélyezésével](../common/storage-enable-and-view-metrics.md)
- [A gyakori és ritka elérésű, valamint az archív tárolási szint díjszabásának régiók szerinti ellenőrzése Blob Storage- és GPv2-fiókok esetében](https://azure.microsoft.com/pricing/details/storage/)
- [Az adatátviteli díjszabás megtekintése](https://azure.microsoft.com/pricing/details/data-transfers/)
