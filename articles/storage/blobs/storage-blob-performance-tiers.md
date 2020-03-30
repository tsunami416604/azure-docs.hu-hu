---
title: Blob-tárolási teljesítményszintek blokkolása – Azure Storage
description: A cikk ismerteti a prémium és standard szintű teljesítményszintek közötti különbséget az Azure blokkblob-tárolóhoz.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/12/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: ff82986b27d038c536872b07e1308b0d48fadaef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74270226"
---
# <a name="performance-tiers-for-block-blob-storage"></a>Teljesítményszintek a blokkblob-tárolóhoz

Mivel a vállalatok teljesítményérzékeny, natív felhőalapú alkalmazásokat telepítenek, fontos, hogy a különböző teljesítményszinteken költséghatékony adattárolási lehetőségekkel rendelkezzen.

Az Azure blokkblob-tároló két különböző teljesítményszintet kínál:

- **Prémium**: a magas tranzakciós arányokra és az egyszámjegyű konzisztens tárolási késleltetésre optimalizálva
- **Standard**: nagy kapacitásra és nagy átadóképességre optimalizálva

A következő szempontok vonatkoznak a különböző teljesítményszintekre:

| Terület |Normál teljesítmény  |Prémium teljesítmény  |
|---------|---------|---------|
|Régiónkénti elérhetőség     |   Minden régió      | [Egyes régiókban](https://azure.microsoft.com/global-infrastructure/services/?products=storage)       |
|Támogatott [tárfiók-típusok](../common/storage-account-overview.md#types-of-storage-accounts)     |     Általános célú v2, BlobStorage, Általános célú v1    |    BlockBlobStorage tároló     |
|Támogatja a [nagy átviteli sebességű blokkblobokat](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/)     |    Igen, 4 MiB PutBlock vagy PutBlob méretnél nagyobb méretben     |    Igen, 256 KiB PutBlock vagy PutBlob méretnél nagyobb méretben    |
|Redundancia     |     Lásd: [A tárfiókok típusai](../common/storage-account-overview.md#types-of-storage-accounts)   |  Jelenleg csak helyileg redundáns tárolást (LRS) és zónaredudant storage (ZRS)<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>1</sup></div>     |

<div id="zone-redundant-storage"><sup>1 1</sup> Zónaredundáns tárolás (ZRS) érhető el a prémium szintű teljesítményblokk blob storage-fiókok egyes régiókban érhető el.</div>

Ami a költségeket illeti, a prémium szintű teljesítmény optimalizált díjszabást biztosít a magas tranzakciós sebességű alkalmazások számára, hogy csökkentse a [teljes tárolási költséget](https://azure.microsoft.com/blog/reducing-overall-storage-costs-with-azure-premium-blob-storage/) ezeknél a számítási feladatoknál.

## <a name="premium-performance"></a>Prémium teljesítmény

Prémium teljesítmény blokk blob storage teszi az adatokat a nagy teljesítményű hardveren keresztül. Az adatok at ssd-meghajtókon (SSD) tárolják, amelyek alacsony késleltetésre vannak optimalizálva. Az SSD-k nagyobb átviteli hatóát biztosítják, mint a hagyományos merevlemezek.

A prémium szintű teljesítményalapú tárolás ideális a gyors és konzisztens válaszidőt igénylő számítási feladatokhoz. A legjobb a sok kis tranzakciót végző számítási feladatokhoz. Példa számítási feladatok közé:

- **Interaktív munkaterhelések**. Ezek a munkaterhelések azonnali frissítéseket és felhasználói visszajelzéseket igényelnek, például az e-kereskedelmi és térképészeti alkalmazásokat. Egy e-kereskedelmi alkalmazásban például a ritkábban megtekintett elemek valószínűleg nem kerülnek a gyorsítótárba. Ezeket azonban azonnal meg kell jelenlennie az ügyfél számára igény szerint.

- **Analytics**. Egy IoT-forgatókönyvben sok kisebb írási műveletek et lehet leküldéses a felhőbe másodpercenként. Nagy mennyiségű adatot lehet venni, elemzési célokra összesíteni, majd szinte azonnal törölni. A prémium szintű blokkblob-tároló magas betöltési képességei hatékonysá teszik az ilyen típusú számítási feladatokhoz.

- **Mesterséges intelligencia/gépi tanulás (AI/ML)**. Az AI/ML a különböző adattípusok, például a vizualizációk, a beszéd és a szöveg felhasználásával és feldolgozásával foglalkozik. Ez a nagy teljesítményű számítási számítási feladat nagy mennyiségű adattal foglalkozik, amelyek gyors választ és hatékony betöltési időt igényelnek az adatelemzéshez.

- **Adatátalakítás**. Az adatok állandó szerkesztését, módosítását és átalakítását igénylő folyamatok azonnali frissítést igényelnek. A pontos adatmegjelenítés érdekében ezeknek az adatoknak a fogyasztóknak azonnal látniuk kell ezeket a változásokat.

## <a name="standard-performance"></a>Normál teljesítmény

A standard teljesítmény támogatja a különböző [hozzáférési szinteket](storage-blob-storage-tiers.md) az adatok legköltséghatékonyabb módon történő tárolásához. Nagy kapacitásra és nagy átviteli kapacitásra van optimalizálva nagy adatkészleteken.

- **Biztonsági mentési és vész-helyreállítási adatkészletek**. A standard szintű tárolási kapacitás költséghatékony rétegeket kínál, így tökéletes használati eset mind a rövid távú, mind a hosszú távú vész-helyreállítási adatkészletek, a másodlagos biztonsági mentések és a megfelelőségi adatok archiválása számára.

- **Médiatartalom**. A képekhez és videókhoz gyakran gyakran hozzáférnek, amikor először hozják létre és tárolják őket, de ezt a tartalomtípust ritkábban használják, amint öregszik. A szabványos teljesítményalapú tárolás megfelelő szinteket kínál a médiatartalmak igényeinek megfelelően. 

- **Tömeges adatfeldolgozás**. Az ilyen típusú számítási feladatok szabványos tárolásra alkalmasak, mivel konzisztens alacsony késés helyett költséghatékony, nagy átviteli sebességű tárolást igényelnek. A nagy, nyers adatkészletek feldolgozásra vannak előkészítve, és végül átkerülnek a hűvösebb szintekre.

## <a name="migrate-from-standard-to-premium"></a>Áttérés standardról prémiumra

Nem konvertálhat egy meglévő szabványos teljesítményszintű tárfiókot egy prémium szintű teljesítménnyel rendelkező blokkblob-tárfiókká. A prémium szintű teljesítménytár-fiókba való áttelepítéshez létre kell hoznia egy BlockBlobStorage-fiókot, és át kell telepítenie az adatokat az új fiókba. További információ: [BlockBlobStorage-fiók létrehozása.](storage-blob-create-account-block-blob.md)

Blobok tárfiókok közötti másolásához használhatja az [AzCopy](../common/storage-use-azcopy-blobs.md) parancssori eszköz legújabb verzióját. Más eszközök, például az Azure Data Factory is rendelkezésre állnak az adatok mozgatása és átalakítása.

## <a name="blob-lifecycle-management"></a>Blob életciklus-kezelése

A Blob-tároló életciklus-kezelése gazdag, szabályalapú szabályzatot kínál:

- **Prémium**: Az adatok lejárata az életciklus végén.
- **Standard**: Átmenet adatok a legjobb hozzáférési szintre, és lejár az adatok az életciklus végén.

További információ: [Az Azure Blob-tárolási életciklus kezelése.](storage-lifecycle-management-concepts.md)

A prémium szintű blokkblob-tárfiókban tárolt adatok nem helyezhető át a gyakori elérésű, ritka elérésű és az archív szintek között. Azonban a blobok másolása egy blokk blob storage-fiók a gyakori elérésű hozzáférési szint egy *másik* fiókban. Ha adatokat szeretne másolni egy másik fiókba, használja a [Blokk elhelyezése URL-ből API-t](/rest/api/storageservices/put-block-from-url) vagy az [AzCopy v10 parancsot.](../common/storage-use-azcopy-v10.md) A **Put Block From URL** API szinkron módon másolja az adatokat a kiszolgálón. A hívás csak akkor fejeződik be, ha az összes adat az eredeti kiszolgáló helyéről a célhelyre kerül.

## <a name="next-steps"></a>További lépések

Értékelje a forró, hűvös és archiválási gpv2- és blobtár-fiókokban.

- [Ismerje meg a blobadatok újrahidratálását az archív rétegből](storage-blob-rehydration.md)
- [Aktuális tárfiókjai használatának értékelése az Azure Storage mérőszámainak engedélyezésével](../common/storage-enable-and-view-metrics.md)
- [A blobstorage- és GPv2-fiókok gyors, hűvös és archív díjszabásának ellenőrzése régiónként](https://azure.microsoft.com/pricing/details/storage/)
- [Az adatátviteli díjszabás megtekintése](https://azure.microsoft.com/pricing/details/data-transfers/)
