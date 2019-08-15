---
title: BLOB-adatok rehidratálása az archív szintről
description: Távolítsa el a blobokat az archív tárolóból, hogy hozzáférjen az adatokhoz.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/07/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: 2e7d56a1461dfd89a7309288aadb0ba245d0f885
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "68958212"
---
# <a name="rehydrate-blob-data-from-the-archive-tier"></a>BLOB-adatok rehidratálása az archív szintről

Míg a blob az archív hozzáférési szinten található, offline állapotba kerül, és nem olvasható és nem módosítható. A blob metaadatai online és elérhető állapotban maradnak, és lehetővé teszik a blob és a hozzá tartozó tulajdonságok listázását. A Blobok beolvasása és módosítása csak online szinteken érhető el, például a gyakori vagy a ritka elérésű. Az archív hozzáférési szinten tárolt adatok lekérdezésére és elérésére két lehetőség áll rendelkezésre.

1. [Archivált Blobok rehidratálása online rétegre](#rehydrate-an-archived-blob-to-an-online-tier) – az archivált blobokat gyors vagy lassú állapotba állíthatja, ha megváltoztatja a rétegét a [blob-réteg beállítása](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) művelettel.
2. [Archivált blob másolása online szintre](#copy-an-archived-blob-to-an-online-tier) – az archivált Blobok új másolatának létrehozása a [blob másolása](https://docs.microsoft.com/rest/api/storageservices/copy-blob) művelettel. Adjon meg egy másik blob-nevet és egy gyors vagy ritka elérésű célként megadott szintet.

 A rétegekkel kapcsolatos további információkért lásd [: Azure Blob Storage: gyakori, ritka elérésű és archív hozzáférési szintek](storage-blob-storage-tiers.md).

## <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>Archivált Blobok rehidratálása online szintre

[!INCLUDE [storage-blob-rehydration](../../../includes/storage-blob-rehydrate-include.md)]

## <a name="copy-an-archived-blob-to-an-online-tier"></a>Archivált blob másolása online szintre

Ha nem szeretne kiszáradni egy blobot, kiválaszthatja a [blob másolása](https://docs.microsoft.com/rest/api/storageservices/copy-blob) műveletet. Az eredeti blob változatlan marad az archívumban, miközben az új blobon dolgozik a gyakori vagy a ritka elérésű szinten. A másolási folyamat használatakor beállíthatja a választható *x-MS-rehidratált-priority* tulajdonságot standard vagy magas (előzetes verzió) értékre.

Az archiválási Blobok csak online célként megadott szintre másolhatók. Az archív Blobok másik archív blobba való másolása nem támogatott.

A Blobok archívumból való másolása időt vesz igénybe. A háttérben a másolási **blob** művelet átmenetileg felhidratálja az archív forrás blobját egy új online blob létrehozásához a célként megadott szinten. Ez az új blob addig nem érhető el, amíg az archívumból való ideiglenes rehidratálás be nem fejeződik, és az adatok az új blobba íródnak.

## <a name="pricing-and-billing"></a>Árak és számlázás

Az olvasási műveletek és az adatok lekérése során a rendszer felszámítja, hogy az archivált Blobok a gyakori vagy a ritka elérésű szintekre kerülnek. A magas prioritású (előzetes verzió) használata esetén a standard prioritáshoz képest magasabb a művelet és az adatok beolvasása. A magas prioritású rehidratálás a számlán külön tételként jelenik meg. Ha a magas prioritású kérelem egy pár gigabájt archiválási blobjának visszaadására vonatkozik, 5 órát vesz igénybe, nem számítunk fel díjat a magas prioritású lekérési arányért. A normál lekérési díjszabás azonban továbbra is érvényes.

A Blobok archívumból gyors vagy ritka elérésű szintekre való másolása olvasási műveletként és adatlekérdezésként történik. Az új másolat létrehozásához írási műveletért kell fizetni. A korai törlési díjak nem érvényesek az online blobra másoláskor, mert a forrás blobja változatlan marad az archiválási szinten. A magas prioritású költségek érvényesek.

Az archiválási szinten lévő blobokat legalább 180 napig kell tárolni. Az archivált Blobok törlésére vagy újraszárítására az 180 nap előtt a korai törlési díjat kell fizetni.

> [!NOTE]
> A blokk-blobok és az adattisztítások díjszabásával kapcsolatos további információkért lásd: az [Azure Storage díjszabása](https://azure.microsoft.com/pricing/details/storage/blobs/). A kimenő adatátviteli díjakkal kapcsolatos további információkért tekintse meg az adatátviteli [díjszabás részleteit](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="next-steps"></a>További lépések

* [Tudnivalók a Blob Storage szintjeiről](storage-blob-storage-tiers.md)
* [A gyakori és ritka elérésű, valamint az archív tárolási szint díjszabásának régiók szerinti ellenőrzése Blob Storage- és GPv2-fiókok esetében](https://azure.microsoft.com/pricing/details/storage/)
* [Az Azure Blob Storage életciklusának kezelése](storage-lifecycle-management-concepts.md)
* [Az adatátviteli díjszabás megtekintése](https://azure.microsoft.com/pricing/details/data-transfers/)
