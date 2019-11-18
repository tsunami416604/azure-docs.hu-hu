---
title: BLOB-adatok rehidratálása az archív szintről
description: Távolítsa el a blobokat az archív tárolóból, hogy hozzáférjen az adatokhoz.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/14/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: d6370509b49ae464b53525e7320676b04912bd12
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113710"
---
# <a name="rehydrate-blob-data-from-the-archive-tier"></a>BLOB-adatok rehidratálása az archív szintről

Míg a blob az archív hozzáférési szinten található, offline állapotba kerül, és nem olvasható és nem módosítható. A blob metaadatai online és elérhető állapotban maradnak, és lehetővé teszik a blob és a hozzá tartozó tulajdonságok listázását. A Blobok beolvasása és módosítása csak online szinteken érhető el, például a gyakori vagy a ritka elérésű. Az archív hozzáférési szinten tárolt adatok lekérdezésére és elérésére két lehetőség áll rendelkezésre.

1. [Archivált Blobok rehidratálása online szintre](#rehydrate-an-archived-blob-to-an-online-tier) – az archív blobot gyors vagy ritka állapotba állíthatja, ha megváltoztatja a rétegét a [blob-réteg beállítása](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) művelettel.
2. [Archivált blob másolása online szintre](#copy-an-archived-blob-to-an-online-tier) – hozzon létre egy archív blob új másolatát a [blob másolása](https://docs.microsoft.com/rest/api/storageservices/copy-blob) művelet használatával. Adjon meg egy másik blob-nevet és egy gyors vagy ritka elérésű célként megadott szintet.

 A rétegekkel kapcsolatos további információkért lásd [: Azure Blob Storage: gyakori, ritka elérésű és archív hozzáférési szintek](storage-blob-storage-tiers.md).

## <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>Archivált Blobok rehidratálása online szintre

[!INCLUDE [storage-blob-rehydration](../../../includes/storage-blob-rehydrate-include.md)]

## <a name="copy-an-archived-blob-to-an-online-tier"></a>Archivált blob másolása online szintre

Ha nem szeretné kiszáradni az archív blobot, dönthet úgy, hogy [másolási blob](https://docs.microsoft.com/rest/api/storageservices/copy-blob) műveletet hajt végre. Az eredeti blob változatlan marad az archívumban, miközben új blob jön létre az online gyakori vagy ritka elérésű szinten, hogy működjön. A blob másolása műveletben az opcionális *x-MS-rehidratált-priority* tulajdonságot a standard vagy a High (előzetes verzió) értékre is beállíthatja annak a prioritásnak a megadásához, amelyen a blob-példányt létre kívánja hozni.

Az archív Blobok csak ugyanazon a Storage-fiókon belüli online célhelyekre másolhatók. Az archív Blobok másik archív blobba való másolása nem támogatott.

A Blobok archívumból való másolása a kiválasztott rehidratálás prioritástól függően órákig elvégezhető. A háttérben a **blob másolása** művelet beolvassa az archív forrás blobját, hogy létrehozzon egy új online blobot a kiválasztott célhelyen. Előfordulhat, hogy az új blob látható a Blobok listázásakor, de az adatok nem érhetők el, amíg a forrás archív blobból való olvasás be nem fejeződik, és az adatok bekerülnek az új online cél blobba. Az új blob független másolat, és minden módosítás vagy törlés nem befolyásolja a forrás archív blobot.

## <a name="pricing-and-billing"></a>Árak és számlázás

Az olvasási műveletek és az adatok lekérése során a rendszer felszámítja, hogy az archivált Blobok a gyakori vagy a ritka elérésű szintekre kerülnek. A magas prioritású (előzetes verzió) használata esetén a standard prioritáshoz képest magasabb a művelet és az adatok beolvasása. A magas prioritású rehidratálás a számlán külön tételként jelenik meg. Ha a magas prioritású kérelem egy pár gigabájt archiválási blobjának visszaadására irányul, 5 órát vesz igénybe, nem számítunk fel díjat a magas prioritású beolvasási arányért. A normál lekérési díjak azonban továbbra is érvényben maradnak, mivel a rehidratálás elsőbbséget élvez más kérelmekkel szemben.

A Blobok archívumból gyors vagy ritka elérésű szintekre való másolása olvasási műveletként és adatlekérdezésként történik. Az új blob másolatának létrehozásakor a rendszer írási műveletet számít fel. A korai törlési díjak nem érvényesek az online blobra másoláskor, mert a forrás blobja változatlan marad az archiválási szinten. Ha be van jelölve, a magas prioritású beolvasási díjak érvényesek.

Az archiválási szinten lévő blobokat legalább 180 napig kell tárolni. Az archivált Blobok törlésére vagy újraszárítására az 180 nap előtt a korai törlési díjat kell fizetni.

> [!NOTE]
> A blokk-blobok és az adattisztítások díjszabásával kapcsolatos további információkért lásd: az [Azure Storage díjszabása](https://azure.microsoft.com/pricing/details/storage/blobs/). A kimenő adatátviteli díjakkal kapcsolatos további információkért tekintse meg az [adatátviteli díjszabás részleteit](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="next-steps"></a>További lépések

* [Tudnivalók a Blob Storage szintjeiről](storage-blob-storage-tiers.md)
* [A gyakori és ritka elérésű, valamint az archív tárolási szint díjszabásának régiók szerinti ellenőrzése Blob Storage- és GPv2-fiókok esetében](https://azure.microsoft.com/pricing/details/storage/)
* [Azure Blob Storage-életciklus felügyelete](storage-lifecycle-management-concepts.md)
* [Az adatátviteli díjszabás megtekintése](https://azure.microsoft.com/pricing/details/data-transfers/)
