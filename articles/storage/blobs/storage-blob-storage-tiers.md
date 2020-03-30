---
title: Gyakori, ritka elérésű és archiválási hozzáférési szintek blobokhoz – Azure Storage
description: Gyakori, ritka elérésű és archiválási hozzáférési szintek az Azure storage-fiókokhoz.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/23/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: f2f6be1022a7100a23f49534f2c18fc951d56284
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255508"
---
# <a name="azure-blob-storage-hot-cool-and-archive-access-tiers"></a>Azure Blob Storage: A gyakori és ritka elérésű, valamint az archív hozzáférési szintek

Az Azure Storage különböző hozzáférési szinteket kínál, amelyek lehetővé teszik a blobobjektum-adatok legköltséghatékonyabb módon történő tárolását. A rendelkezésre álló hozzáférési szintek a következők:

- **Gyakori** – A gyakran elérhető adatok tárolására optimalizált.
- **Cool** - A ritkán használt és legalább 30 napig tárolt adatok tárolására optimalizált.
- **Archiválás** – Olyan adatok tárolására optimalizálva, amelyeket ritkán, és legalább 180 napig tárolnak rugalmas késési követelményekkel (órák sorrendjében).

A következő szempontok vonatkoznak a különböző hozzáférési szintekre:

- Csak a gyakori elérésű és ritka elérésű hozzáférési szintek állíthatók be a fiók szintjén. Az archív hozzáférési szint nem érhető el a fiók szintjén.
- A gyakori elérésű, ritka elérésű és archív szintek a blob szintjén állíthatók be a feltöltés során vagy a feltöltés után.
- A ritka elérésű hozzáférési rétegben lévő adatok valamivel alacsonyabb rendelkezésre állást is elviselnek, de továbbra is nagy tartósságot, visszakeresési késést és a gyakori elérésű adatokhoz hasonló átviteli jellemzőket igényelnek. A hűvös adatok esetében a rendelkezésre állási szolgáltatásszintű szerződés (SLA) és a rendszeralacsonyabb tárolási költségek hez képest a magasabb hozzáférési költségek elfogadható kompromisszumok.
- Az archív tárolás offline tárolja az adatokat, és a legalacsonyabb tárolási költségeket kínálja, de a legmagasabb adathidrahidrátást és hozzáférési költségeket is.

A felhőben tárolt adatok exponenciális ütemben nőnek. A növekvő tárolási szükségletek költségeinek kezelése érdekében hasznos lehet az adatokat olyan attribútumok alapján szervezni, mint a hozzáférés gyakorisága vagy a tervezett megőrzési időtartam, így optimalizálhatók a költségek. A felhőben tárolt adatok a létrehozásuk, feldolgozásuk és elérésének teljes élettartama alatt való elérése alapján eltérőek lehetnek. Egyes adatokat aktívan használnak és módosítanak teljes élettartamuk során. Egyes adatokat élettartamuk korai szakaszában sokat használnak, az adatok életkorának növekedésével azonban a hozzáférések mennyisége drasztikusan csökken. Egyes adatok továbbra is tétlen a felhőben, és ritkán, ha valaha is elérhető, miután a tárolt.

Ezek az adatelérési forgatókönyvek mindegyike egy adott hozzáférési mintára optimalizált más hozzáférési szint előnyeit élvezi. A gyakori, ritka elérésű és archív hozzáférési szintekkel az Azure Blob storage a differenciált hozzáférési szintek, valamint a külön díjszabási modellek iránti igényt elégíti ki.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-accounts-that-support-tiering"></a>Rétegezést támogató Storage-fiókok

A gyakori elérésű, ritka elérésű és archiválási objektumok tárolási adatrétegezését csak a Blob storage és az Általános célú v2 (GPv2) fiókok támogatják. Általános célú v1 (GPv1) fiókok nem támogatják a rétegződést. Az ügyfelek egyszerűen konvertálhatják meglévő GPv1- vagy Blob-tárfiókjaikat GPv2-fiókokká az Azure Portalon keresztül. A GPv2 új árképzést és funkciókat biztosít a blobok, fájlok és várólisták számára. Egyes funkciók és árcsökkentések csak GPv2-fiókokban találhatók. GPv2-fiókok használatával kiértékelheti az árképzés átfogó áttekintése után. Egyes számítási feladatok drágábbak lehetnek a GPv2-en, mint a GPv1. További információkat az [Azure Storage-fiókok áttekintésében](../common/storage-account-overview.md) találhat.

A Blob storage és a GPv2-fiókok fiókszinten teszik elérhetővé az **Access Tier** attribútumot. Ez az attribútum lehetővé teszi, hogy adja meg az alapértelmezett hozzáférési szint minden blob, amely nem rendelkezik explicit készlet az objektum szintjén. Az objektumok szintjén beállított szinttel rendelkező objektumok esetében a fiókszint nem érvényes. Az archív szint csak az objektum szintjén alkalmazható. Ezek a hozzáférési szintek között bármikor válthat.

## <a name="hot-access-tier"></a>Gyakran használt adatok hozzáférési szintje

A gyakori elérésű hozzáférési szint magasabb tárolási költségekkel rendelkezik, mint a ritka elérésű és az archív szintek, de a legalacsonyabb hozzáférési költségek. A gyakori elérésű hozzáférési szint használati forgatókönyvei például a következők:

- Az aktív használatban lévő vagy várhatóan elérhető (beolvasott és erre írásra írt) adatok.
- Feldolgozásra és a ritka elérésű hozzáférési szintre való esetleges áttelepítésre előkészített adatok.

## <a name="cool-access-tier"></a>Ritkán használt adatok hozzáférési szintje

A ritka elérésű hozzáférési szint alacsonyabb tárolási költségekkel és magasabb hozzáférési költségekkel rendelkezik, mint a gyakori elérésű tárolás. Ezen a szinten olyan adatokat érdemes tárolni, amelyek legalább 30 napig maradnak a ritka elérésű szinten. Példa használati forgatókönyvek a ritka elérésű hozzáférési szint közé:

- Rövid távú biztonsági mentési és vészhelyreállítási adatkészletek.
- Régebbi, már csak ritkán megtekintett médiatartalmak, amelyek elérésére igény esetén azonban azonnal szükség van.
- Nagyobb adatkészletek, amelyeket költséghatékonyan kell tárolni, amíg a későbbi feldolgozáshoz szükséges többi adat gyűjtése még folyamatban van. (*Például* tudományos adatok vagy gyártási létesítményből származó nyers telemetriaadatok hosszú távú tárolása)

## <a name="archive-access-tier"></a>Archivált adatok hozzáférési szintje

Az archív hozzáférési szint rendelkezik a legalacsonyabb tárolási költséggel. De ez birtokol magasabb adat visszakeresés költségek képest a forró és hűvös szintek. Az archív rétegben lévő adatok beolvasása több órát is igénybe vehet. Az adatoknak legalább 180 napig az archív szinten kell maradniuk, vagy korai törlési díjat kell fizetniük.

Amíg egy blob az archív tárolóban van, a blob adatok offline állapotban vannak, és nem olvashatók, felülírhatók vagy módosíthatók. Egy blob archívumban való olvasásához vagy letöltéséhez először újra kell hidratálnia egy online rétegre. Nem készíthet pillanatképeket egy blobról az archív tárolóban. Azonban a blob metaadatok online marad, és elérhető, amely lehetővé teszi a blob és tulajdonságainak listázása. Az archívumban lévő blobok esetében az egyetlen érvényes művelet a GetBlobProperties, a GetBlobMetadata, a ListBlobs, a SetBlobTier, a CopyBlob és a DeleteBlob. További információért tekintse meg [a blobadatok újrahidratálása az archív rétegből című témakört.](storage-blob-rehydration.md)

Az archív hozzáférési szint használati forgatókönyvei például a következők:

- Hosszú távú biztonsági mentések, másodlagos biztonsági mentések és archiválási adatkészletek
- Eredeti (nyers) adatok, amelyeket a végső használható formába való feldolgozásukat követően is meg kell őrizni.
- Megfelelőségi és archiválási adatok, amelyeket hosszú ideig kell tárolni, azonban nagyon ritkán kell hozzáférni.

## <a name="account-level-tiering"></a>Fiókszintű rétegezés

Blobok mindhárom hozzáférési szint en belül létezhet ugyanazon a fiókon belül. Minden blob, amely nem rendelkezik explicit módon hozzárendelt réteg következtet a réteget a fiók hozzáférési réteg beállítása. Ha a hozzáférési szint a fiókból származik, megjelenik az **Access Tier Inferred** blob tulajdonság beállítása "true", és az **Access Tier** blob tulajdonság a fiókrétegnek felel meg. Az Azure Portalon a _hozzáférési szint inferred_ tulajdonság jelenik meg a blob hozzáférési szint **gyakori elérésű (kibúlt)** vagy **ritka elérésű (inferred)**.

A fiókhozzáférési szint módosítása a fiókban tárolt összes _olyan hozzáférési szintre_ vonatkozik, amely nem rendelkezik explicit szintkészlettel. Ha a fiókréteget a gyakori elérésűről a ritka elérésűre kapcsolja be, az írási műveletekért (10 000-re) kell fizetnie az összes blobesetében, ha csak a GPv2-fiókokban csak meghatározott szint nélkül van beállítva. A blobstorage-fiókokban a módosításért nem számítunk fel díjat. Ha a Blob storage-ban vagy a GPv2-fiókokban a laza a rendszerről a gyors működésre váltáskor az olvasási műveletekért (10 000-re) és az adatok lekéréséért is díjat számítunk fel.

## <a name="blob-level-tiering"></a>Blobszintű rétegezés

A Blob-szintű rétegezés lehetővé teszi, hogy adatokat töltsön fel a kiválasztott hozzáférési szintre a [Put Blob](/rest/api/storageservices/put-blob) vagy [put block list](/rest/api/storageservices/put-block-list) műveletek használatával, és módosítsa az adatok szintjét az objektum szintjén a [Blobszint beállítása](/rest/api/storageservices/set-blob-tier) művelet vagy [életciklus-kezelési](#blob-lifecycle-management) funkció használatával. Adatokat tölthet fel a szükséges hozzáférési szintre, majd egyszerűen módosíthatja a blob hozzáférési szintjét a gyakori elérésű, ritka elérésű vagy archív szintek között a használati minták változásával, anélkül, hogy adatokat kellene áthelyeznie a fiókok között. Minden szintváltozási kérelmek azonnal megtörténik, és a szint közötti változások gyakori és ritka elérésű azonnali. Azonban egy blob újrahidratálása az archívumból több órát is igénybe vehet.

Az utolsó blobszint-módosítás időpontja a **Hozzáférési szint utolsó módosítása** blobtulajdonságon keresztül érhető el. Amikor felülír egy blobot a gyakori vagy ritka elérésű rétegben, az újonnan létrehozott blob örökli a blob, amely felülírt, kivéve, ha az új blob hozzáférési szint explicit módon van beállítva a létrehozáshoz. Ha egy blob az archív rétegben van, nem lehet felülírni, így ugyanazt a blobot feltöltése ebben a forgatókönyvben nem engedélyezett. 

> [!NOTE]
> Az archív tárolás és a blobszintű rétegezés csak a blokkblobokat támogatja. Jelenleg nem módosíthatja a pillanatképeket tartalmazó blokkblob szintjét.

### <a name="blob-lifecycle-management"></a>Blob életciklus-kezelése

A Blob Storage életciklus-kezelése gazdag, szabályalapú szabályzatot kínál, amely segítségével átválthatja az adatokat a legjobb hozzáférési szintre, és lejárthatja az adatokat az életciklusa végén. További [információ: Az Azure Blob tárolási életciklusának kezelése.](storage-lifecycle-management-concepts.md)  

> [!NOTE]
> A blokkblob-tárfiókban (prémium szintű teljesítmény) tárolt adatok jelenleg nem rétegezhetők a gyakori elérésű, ritka elérésű vagy archiválási állapotba [a Blob-szint beállítása](/rest/api/storageservices/set-blob-tier) vagy az Azure Blob Storage életciklus-kezelés használatával.
> Adatok áthelyezéséhez szinkron módon kell másolnia a blobokat a blokkblob-tárfiókból a gyakori elérésű hozzáférési szintre egy másik fiókban a [Put Block From URL API vagy](/rest/api/storageservices/put-block-from-url) az AzCopy azon verzióját, amely támogatja ezt az API-t.
> A *Put Block From URL* API szinkron módon másolja az adatokat a kiszolgálón, ami azt jelenti, hogy a hívás csak akkor fejeződik be, ha az összes adat az eredeti kiszolgáló helyéről a célhelyre kerül.

### <a name="blob-level-tiering-billing"></a>Blobszintű rétegezési számlázás

Amikor egy blob feltöltése vagy áthelyezése a gyakori elérésű, ritka elérésű vagy archív szintre, a szint módosítása után azonnal a megfelelő díja lesz.

Ha egy blob ot egy hűvösebb szintre (hot->,hot->archiválási vagy ritka elérésű >archívumba helyeződik át), a művelet írási műveletként kerül számlázásra a célréteghez, ahol az írási művelet (10 000 főre vetítve) és a célszint (GB-onkénti) adatírási díjai érvényesek.

Ha egy blob ot helyez át egy melegebb szintre (archív >, az archívum->gyakori a gyakori a >, a művelet a forrásrétegből olvasásként kerül számlázásra, ahol az olvasási művelet (10 000 főre vetítve) és a forrásszint (GB-onkénti) adatlekérései. A ritka elérésű vagy archív szintről áthelyezett blobok esetében korai törlésre vonatkozó díjak lehetnek érvényesek. [Az archívumból származó adatok újrahidratálása](storage-blob-rehydration.md) időt vesz igénybe, és az adatok at az archiválási árakra terheljük, amíg az adatok online helyre nem állnak, és a blobszint gyors vagy ritka elérésű rehidratálja. Az alábbi táblázat összefoglalja a rétegmódosítások számlázásának módját:

| | **Írási díjak (Operation + Access)** | **Díjak olvasása (Operation + Access)**
| ---- | ----- | ----- |
| **SetBlobTier iránya** | forró->hűvös,<br> hot->archívum,<br> cool->archívum | archív->hűvös,<br> archívum->forró,<br> hűvös->forró

### <a name="cool-and-archive-early-deletion"></a>Korai törlés a ritka elérésű és az archív szinten

Minden blob, amely átkerül a ritka elérésű réteg (Csak GPv2-fiókok) 30 napos hűvös korai törlési időszak. Az archív szintre áthelyezett blobok 180 napos archiválási korai törlési időszak alá esnek. A díj számlázása időarányosan történik. Ha például egy blobot áthelyeznek az archívumba, majd 45 nap után törlődnek vagy a rendszera rendszerbe kerülnek, a blob archiválásának korai törlési díja 135 (180 mínusz 45) nap.

A korai törlést a blob tulajdonság , **Utolsó módosítás**, ha nem történt hozzáférési szint módosítása. Ellenkező esetben használhatja, ha a hozzáférési szint utolsó módosítása a lefagyás vagy archiválás megtekintésével a blob tulajdonság: **access-tier-change-time.** A blob tulajdonságairól a [Blob tulajdonságainak beolvassa a get blob tulajdonságok című témakört.](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties)

## <a name="comparing-block-blob-storage-options"></a>Blokkblob-tárolási beállítások összehasonlítása

Az alábbi táblázat a prémium szintű teljesítményblokk blobstorage, valamint a gyakori elérésű, ritka elérésű és archív hozzáférési szintek összehasonlítását mutatja be.

|                                           | **Prémium teljesítmény**   | **Forró szint** | **Hűvös szint**       | **Archiválási szint**  |
| ----------------------------------------- | ------------------------- | ------------ | ------------------- | ----------------- |
| **Rendelkezésre állás**                          | 99.9%                     | 99.9%        | 99%                 | Offline           |
| **Rendelkezésre állás** <br> **(RA-GRS olvasások)**  | N/A                       | 99.99%       | 99.9%               | Offline           |
| **Használati díjak**                         | Magasabb tárolási költségek, alacsonyabb hozzáférési és tranzakciós költségek | Magasabb tárolási költségek, alacsonyabb hozzáférési és tranzakciós költségek | Alacsonyabb tárolási költségek, magasabb hozzáférési és tranzakciós költségek | A legalacsonyabb tárolási költségek, a legmagasabb hozzáférési és tranzakciós költségek |
| **Minimális objektumméret**                   | N/A                       | N/A          | N/A                 | N/A               |
| **Minimális tárolási időtartam**              | N/A                       | N/A          | 30 nap<sup>1</sup> | 180 nap
| **Késés** <br> **(az első bájtig eltelt idő)** | Egyszámjegyű ezredmásodpercek | ezredmásodperc | ezredmásodperc        | óra<sup>2</sup> |

<sup>1</sup> A GPv2-fiókok letöltő rétegében lévő objektumok minimális megőrzési időtartama 30 nap. Blob storage-fiókok nem rendelkeznek a ritka elérésű réteg minimális megőrzési időtartama.

<sup>2</sup> Az Archív tároló jelenleg 2 rehidratálási prioritást támogat, a Magas és a Standard, amely különböző lekérési késéseket kínál. További információ: [Blob adatok újrahidratálása az archív rétegből.](storage-blob-rehydration.md)

> [!NOTE]
> A Blob storage-fiókok ugyanazokat a teljesítmény- és méretezhetőségi célokat támogatják, mint az általános célú v2-es tárfiókok. További információ: [Méretezhetőségi és teljesítménycélok a Blob storage.](scalability-targets.md)

## <a name="quickstart-scenarios"></a>Rövid útmutatóul szolgáló forgatókönyvek

Ebben a szakaszban a következő forgatókönyvek az Azure Portal és a PowerShell használatával vannak bemutatva:

- GPv2- vagy Blob Storage-fiók alapértelmezett hozzáférési szintjének módosítása.
- GPv2- vagy Blob Storage-fiókban található blob szintjének módosítása.

### <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>GPv2- vagy Blob Storage-fiók alapértelmezett hozzáférési szintjének módosítása

# <a name="portal"></a>[Portál](#tab/azure-portal)
1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

1. Az Azure Portalon keresse meg és válassza az **Összes erőforrás lehetőséget.**

1. Válassza ki a tárfiókot.

1. A **Beállítások csoportban**válassza a **Konfiguráció** lehetőséget a fiókkonfiguráció megtekintéséhez és módosításához.

1. Válassza ki az igényeinek megfelelő hozzáférési szintet: Állítsa az **Access-szintet** **ritka elérésűre** vagy **gyakori elérésűre.**

1. Kattintson a **mentés gombra** a tetején.

![Tárfiók-szint módosítása](media/storage-tiers/account-tier.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
A következő PowerShell-parancsfájl segítségével módosíthatja a fiókréteget. A `$rgName` változót inicializálni kell az erőforráscsoport nevével. A `$accountName` változót inicializálni kell a tárfiók nevével. 
```powershell
#Initialize the following with your resource group and storage account names
$rgName = ""
$accountName = ""

#Change the storage account tier to hot
Set-AzStorageAccount -ResourceGroupName $rgName -Name $accountName -AccessTier Hot
```
---

### <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>Blob rétegének módosítása GPv2- vagy Blob-tárfiókban
# <a name="portal"></a>[Portál](#tab/azure-portal)
1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

1. Az Azure Portalon keresse meg és válassza az **Összes erőforrás lehetőséget.**

1. Válassza ki a tárfiókot.

1. Válassza ki a tárolót, majd válassza ki a blobot.

1. A **Blob tulajdonságai párbeszédpanelen**válassza a **Szint módosítása**lehetőséget.

1. Válassza a **Gyakori**, **Ritka elérés**és **archív** hozzáférési szint lehetőséget. Ha a blob jelenleg archívumban van, és egy online rétegre szeretne hidratálni, választhat egy **Normál** vagy **Magas**Rehidratálási prioritást is.

1. Válassza a **Mentés** gombot alul.

![Tárfiók-szint módosítása](media/storage-tiers/blob-access-tier.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
A következő PowerShell-parancsfájl a blobszint módosításához használható. A `$rgName` változót inicializálni kell az erőforráscsoport nevével. A `$accountName` változót inicializálni kell a tárfiók nevével. A `$containerName` változót inicializálni kell a tároló nevével. A `$blobName` változót inicializálni kell a blob nevével. 
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

Minden tárfiók egy díjszabási modell blokk blob storage alapján az egyes blobok szintje. Ne feledje az alábbi számlázási szempontokat:

- **Tárolási költségek**: Az adattárolás díja a tárolt adatok mennyisége mellett a hozzáférési rétegtől függ. A gigabájtonkénti költség csökken, ha a szint ritkábban használt adatokat tárol.
- **Adathozzáférési költségek**: az adathozzáférési költségek emelkednek, ha a szint ritkábban használt adatokat tárol. A ritka elérésű és archív hozzáférési szinten lévő adatokért gigabájtonkénti adathozzáférési díjat kell fizetnie az olvasásért.
- **Tranzakciós költségek:** Tranzakciónkénti díj jár minden rétegre, amely a szint hűvösebbé válik.
- **Georeplikációs adatátviteli költségek**: Ez csak a georeplikációval konfigurált fiókok esetében érvényes, beleértve a GRS-t és az RA-GRS-t. A georeplikációs adatátvitel gigabájtonkénti díj ellenében érhető el.
- **Kimenő adatátviteli költségek**: A kimenő adatátvitel (azaz az adott Azure-régióból kivitt adatok) esetében gigabájtalapú sávszélesség-használati díjak lépnek fel, csakúgy, mint az általános célú tárfiókok esetében.
- **A hozzáférési szint módosítása:** A fiók hozzáférési szintjének módosítása a _hozzáférési szint ből származó kikövetkeztetett_ blobok olyan hozzáférési szintre vonatkozó díját eredményezi, amelyek nem rendelkeznek explicit szintkészlettel. Ha egy blob hozzáférési szintjét szeretné módosítani, tekintse meg [a Blob-szintű rétegezési számlázást.](#blob-level-tiering-billing)

> [!NOTE]
> A blokkblobok díjszabásáról az [Azure Storage díjszabási](https://azure.microsoft.com/pricing/details/storage/blobs/) oldalán talál további információt. A kimenő adatátviteli díjakkal kapcsolatos további információért lásd az [adatátviteli díjszabást](https://azure.microsoft.com/pricing/details/data-transfers/) ismertető lapot.

## <a name="faq"></a>GYIK

**Blob Storage vagy GPv2-fiókokat kell használnom, ha rétegezni szeretném az adataimat?**

Javasoljuk, hogy Blob Storage-fiókok helyett használjon GPv2-fiókokat a rétegzéshez kialakításához. A GPv2 a Blob Storage-fiókok által támogatott szolgáltatások mellett sok mást is támogat. A Blob Storage és GPv2-fiókok díjszabása majdnem teljesen megegyezik, azonban egyes új szolgáltatások és árengedmények csak GPv2-fiókokhoz lesznek elérhetők. A GPv1-fiókok nem támogatják a rétegezést.

A GPv1- és GPv2-fiókok díjszabási struktúrája eltér egymástól, ezért az ügyfeleknek érdemes mindkettőt alaposan áttekinteni, mielőtt a GPv2-fiókok használata mellett döntenek. Meglévő Blob Storage- vagy GPv1-fiókját könnyedén, egyetlen kattintással átalakíthatja GPv2-fiókká. További információkat az [Azure Storage-fiókok áttekintésében](../common/storage-account-overview.md) találhat.

**Tárolhatok objektumokat mindhárom (gyakori, ritka elérésű és archiválási) hozzáférési szinten ugyanabban a fiókban?**

Igen. A fiók szintjén beállított **Access Tier** attribútum az alapértelmezett fiókréteg, amely a fiók összes objektumára vonatkozik explicit beállított szint nélkül. A Blob-szintű rétegezés lehetővé teszi, hogy a hozzáférési szintet az objektum szintjén állítsa be, függetlenül attól, hogy mi a hozzáférési szint beállítása a fiókban. Blobok a három hozzáférési szint (gyakori elérésű, ritka elérésű vagy archív) blobok előfordulhatnak ugyanazon a fiókon belül.

**Módosíthatom a Blob- vagy GPv2-tárfiókom alapértelmezett hozzáférési szintjét?**

Igen, módosíthatja az alapértelmezett fiókréteget az **Access tier** attribútum beállításával a tárfiókban. A fiókszint módosítása a fiókban tárolt összes olyan objektumra vonatkozik, amely nem rendelkezik explicit szintkészlettel (például **Gyakori elérés (kikövetkeztetett)** vagy **Ritka elérés (kikövetkeztetett)**). A fiókszint gyakori elérésűről a ritka elérésűre való váltása írási műveleteket (10 000-re) von maga után az összes olyan blob esetében, amelyben csak a GPv2-fiókokban nincs beállított szint, és a ritka elérésűről a gyakori elérésűre való váltás olvasási műveleteket (10 000-re) és a Blob-tárolóban lévő összes blob (GB-onként) adatlekérési (GB-onkénti) díját is kivonják. és GPv2-fiókok.

**Beállíthatom a fiók alapértelmezett hozzáférési szintjét archív szintre?**

Nem. Csak a gyakori elérésű és ritka elérésű hozzáférési szintek állíthatók be alapértelmezett fiókhozzáférési szintként. Az archív szint csak az objektumok szintjén állítható be. Blob feltöltésekor megadhatja a hozzáférési réteget, amelyet az alapértelmezett fiókszinttől függetlenül szeretne gyakori, ritka elérésű vagy archiválási rendszerként megadni. Ez a funkció lehetővé teszi, hogy közvetlenül az archív rétegbe írjon adatokat, hogy költségmegtakarítást valósítson meg ablob-tárolóban lévő adatok létrehozásától.

**Mely régiókban érhetők el a gyakori elérésű, ritka elérésű és archív hozzáférési szintek?**

A gyakori elérésű és ritka elérésű hozzáférési szintek a blobszintű rétegezés mellett minden régióban elérhetők. Az archív tárolási szint eleinte csak bizonyos régiókban lesz elérhető. A teljes listát a [Régiónként elérhető Azure-termékek](https://azure.microsoft.com/regions/services/) című részben tekintheti meg.

**A ritka elérésű hozzáférési rétegben lévő blobok eltérően viselkednek, mint a gyakori elérésű hozzáférési szinten lévők?**

A gyakori elérésű hozzáférési rétegben lévő blobok késése megegyezik a GPv1, GPv2 és Blob storage-fiókok ban lévő blobokkal. A ritka elérésű hozzáférési rétegben lévő blobok hasonló késést (ezredmásodpercben) rendelkeznek, mint a GPv1, gpv2 és Blob storage-fiókok blobjai. Az archív hozzáférési rétegben lévő blobok több órás késést biztosítanak a GPv1, a GPv2 és a Blob storage-fiókokban.

A ritka elérésű hozzáférési rétegben lévő blobok egy kicsit alacsonyabb rendelkezésre állási szolgáltatásszinttel (SLA) rendelkeznek, mint a gyakori elérésű hozzáférési szinten tárolt blobok. További információt a [tárolók rendelkezésreállási szolgáltatási szintjeit](https://azure.microsoft.com/support/legal/sla/storage/v1_2/) ismertető szakaszban talál.

**A műveletek megegyeznek a gyakori és a ritka elérésű, valamint az archív tárolási szint esetében?**

A gyakori és a ritka elérésű szint esetében minden művelet teljesen megegyezik. Minden érvényes archiválási művelet, beleértve a GetBlobProperties, GetBlobMetadata, ListBlobs, SetBlobTier és DeleteBlob 100%, konzisztens a gyakori és ritka elérésű. Blob-adatok nem olvashatók vagy módosíthatók az archív rétegben, amíg rehidratáltak; csak blob metaadat-olvasási műveletek támogatottak, míg az archívumban.

**Amikor az archív tárolási szintről rehidratálok egy blobot a gyakori vagy ritka elérésű szintre, honnan tudom, hogy befejeződött-e a rehidratálás?**

A rehidratálás során használhatja a blob tulajdonságok levétele művelet lekérdezéséhez az **archív állapot** attribútumot, és erősítse meg, ha a réteg módosítása befejeződött. Az állapot a célszinttől függően „rehydrate-pending-to-hot” (rehidratálás-folyamatban-a-gyakoriba) vagy „rehydrate-pending-to-cool” (rehidratálás-folyamatban-a-ritkába) lehet. Befejezését követően az archív állapot tulajdonság törlődik, és az **Access Tier** blob tulajdonság tükrözi az új gyakori vagy ritka elérésű szint. További információért tekintse meg [a blobadatok újrahidratálása az archív rétegből című témakört.](storage-blob-rehydration.md)

**Egy blob szintjének beállítása után mikor kezdődik a megfelelő díjak alapján történő számlázás?**

Minden blob mindig a blob **access tier** tulajdonsága által jelzett szint szerint kerül számlázásra. Amikor új online réteget állít be egy blobhoz, az **Access Tier** tulajdonság azonnal tükrözi az új réteget az összes átmenethez. Azonban egy blob rehidratálása az offline archív szintről egy gyakori vagy ritka elérésű szintre több órát is igénybe vehet. Ebben az esetben a rehidratálás befejezéséig archiválási díjakat számlázunk, ekkor az **Access Tier** tulajdonság az új szintet tükrözi. Miután rehidratált az online réteg, akkor a díj az adott blob a gyakori vagy ritka elérésű arány.

**Hogyan állapíthatom meg, hogy korai törlési díjat kapok-e, amikor egy blobot törlök vagy áthelyezek a ritka elérésű vagy archív szintről?**

Minden blob, amelyet a ritka elérésű (csak GPv2-fiókok esetében) vagy archív tárolási szintről töröl vagy helyez át a vonatkozó 30, illetve 180 nap letelte előtt, a korai törléseknek megfelelő, időarányos költségeket von maga után. Meghatározhatja, hogy mennyi ideig egy blob volt a ritka elérésű vagy archív rétegben az **Access Tier Change Time** blob tulajdonság ának ellenőrzésével, amely az utolsó réteg módosítási bélyegzőjét biztosítja. Ha a blob szintje soha nem változott, ellenőrizheti a **legutóbbi módosítás blob** tulajdonság. További információ: [A korai törlés hűvös és archiválása](#cool-and-archive-early-deletion)című témakörben talál.

**Melyik Azure-eszközök és SDK-k támogatják a blobszintű rétegezést és az archív tárolási szintet?**

Az Azure Portal, a PowerShell, valamint a parancssori felület eszközei, illetve a .NET-, Java-, Python- és Node.js-ügyfélkönyvtárak mind támogatják a blobszintű rétegezést és az archív tárolási szintet.  

**Mennyi adatot tárolhatok a gyakori és ritka elérésű, valamint az archív tárolási szinten?**

Az adattárolás és az egyéb korlátok a fiók szintjén vannak beállítva, nem hozzáférési szintenként. Választhat, hogy az összes korlátot egy rétegben vagy mindhárom rétegben használja. További információ: [Méretezhetőségi és teljesítménycélok a szabványos tárfiókokhoz.](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="next-steps"></a>További lépések

Kiértékelheti a forró, hűvösebb és archiválási rendszereket a GPv2- és blobtárfiókokban

- [A gyakori és ritka elérésű, valamint az archív tárolási szint rendelkezésre állásának ellenőrzése régiónként](https://azure.microsoft.com/regions/#services)
- [Azure Blob Storage-életciklus felügyelete](storage-lifecycle-management-concepts.md)
- [Ismerje meg a blobadatok újrahidratálását az archív rétegből](storage-blob-rehydration.md)
- [Annak meghatározása, hogy a prémium teljesítmény előnyös lenne-e az alkalmazás számára](storage-blob-performance-tiers.md)
- [Aktuális tárfiókjai használatának értékelése az Azure Storage mérőszámainak engedélyezésével](../common/storage-enable-and-view-metrics.md)
- [A blobstorage- és GPv2-fiókok gyors, hűvös és archív díjszabásának ellenőrzése régiónként](https://azure.microsoft.com/pricing/details/storage/)
- [Az adatátviteli díjszabás megtekintése](https://azure.microsoft.com/pricing/details/data-transfers/)
