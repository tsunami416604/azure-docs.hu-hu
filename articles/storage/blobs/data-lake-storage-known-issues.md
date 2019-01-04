---
title: Ismert problémák az Azure Data Lake Storage Gen2 |} A Microsoft Docs
description: További információ a korlátozások és az Azure Data Lake Storage Gen2 ismert problémái
services: storage
author: normesta
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: normesta
ms.openlocfilehash: bc6f59922286663435782623a72345c19adb494c
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2019
ms.locfileid: "53995033"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Az Azure Data Lake Storage Gen2 ismert problémái

Ez a cikk az ismert problémák és korlátozások vonatkoznak az Azure Data Lake Storage Gen2 tartalmazza.

## <a name="api-interoperability"></a>API-együttműködés

A BLOB Storage API-k és az Azure Data Lake Gen2 API-k nem együttműködésre egymással.

Ha szeretne dolgozni a fiókjába feltöltött tartalom mindegyikét a ugyanazon eszközzel, majd nem engedélyezi az hierarchikus névtér a Blob storage-fiókjában mindaddig, amíg ezen API-k válnak együttműködésre egymással. A hierarchikus névtér nélkül-tárfiókok használata azt jelenti, hogy ezután nem rendelkezik hozzáféréssel a Data Lake Storage Gen2 konkrét funkcióra, például a címtár- és a fájlrendszer hozzáférés-vezérlési listák.

## <a name="blob-storage-apis"></a>A BLOB storage API-k

A BLOB storage API-k még nem érhetők el az Azure Data Lake Storage Gen2-fiókokhoz.

Ezen API-k sikerült merülnek fel, mert a Blob Storage API-k még nem működik együtt az Azure Data Lake Gen2 API-k nem szándékos adatok hozzáférési problémák elkerülése érdekében le vannak tiltva.

Ha ezekkel az API-adatok betöltése előtt letiltottuk azokat, és a, termelési adatokhoz való hozzáférést, majd lépjen kapcsolatba Microsoft Support a következő információkat:

* Előfizetés-azonosító (GUID azonosítója, nem a név)

* Storage-fiók neve

* Éles környezetben aktívan érinti-e, és ha igen, melyik storage-fiókok?

* Akkor is, ha az aktívan nem érintett éles környezetben, akkor mondja el, hogy ezeket az adatokat a valamilyen okból egy másik tárfiókba másolni kell, és ha igen, hogy miért?

Ilyen körülmények azt is visszaállíthatók hozzáférés a Blob API-t egy korlátozott ideig, hogy a storage-fiókra, amely nem rendelkezik a hierarchikus névterek engedélyezettek az adatokat másolja.

Virtuális gép (VM) a nem felügyelt lemezek a letiltott Blob Storage API-k attól függenek, ezért ha engedélyezi a hierarchikus névterek, a storage-fiók, érdemes elhelyezését a nem felügyelt Virtuálisgép-lemezek egy storage-fiókot, amely nem rendelkezik a hierarchikus névterek engedélyezettek.

## <a name="azure-storage-explorer"></a>Azure Storage Explorer

Megtekintéséhez vagy a Data Lake Storage Gen2-fiókok kezelése az Azure Storage Explorer használatával kell rendelkeznie legalább verzió `1.6.0` az eszköz, amely egy [ingyenesen letölthető](https://azure.microsoft.com/features/storage-explorer/).

Vegye figyelembe, hogy a Storage Explorer, az Azure Portalra beágyazott verziója amelyen jelenleg nem támogatja a megtekintése, vagy a Data Lake Storage Gen2-fiókok kezelése a hierarchikus névterek engedélyezettek.

## <a name="blob-viewing-tool"></a>BLOB megtekintése eszköz

Eszköz megtekintése az Azure Portallal BLOB csak korlátozott mértékben támogatja az Azure Data Lake Storage Gen2.

## <a name="third-party-applications"></a>Harmadik féltől származó alkalmazások

Harmadik féltől származó alkalmazások az Azure Data Lake Storage Gen2 nem támogatja.

Minden külső alkalmazás szolgáltatója belátása szerint a rendszer támogatja. Jelenleg a Blob storage API-k és az Azure Data Lake Storage Gen2 API-k nem használható ugyanaz a tartalom kezeléséhez. Mert dolgozunk, hogy együttműködést, akkor lehet, hogy számos harmadik felektől származó eszközök automatikusan támogatják az Azure Data Lake Storage Gen2.

## <a name="azcopy-support"></a>Az AzCopy-támogatás

Az AzCopy 8-as verzió nem támogatja az Azure Data Lake Storage Gen2.

Ehelyett használja az AzCopy legújabb előzetes verzióját ( [AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json) ), az Azure Data Lake Storage Gen2 végpontokat támogatja.

## <a name="azure-event-grid"></a>Azure Event Grid

[Az Azure Event Grid](https://azure.microsoft.com/services/event-grid/) nem események fogadása az Azure Data Lake Gen2 fiókok mert azok a fiókok nem még létre őket.  

## <a name="soft-delete-and-snapshots"></a>Helyreállítható törlés és pillanatképek

Helyreállítható törlés és a pillanatképek nem az Azure Data Lake Storage Gen2 fiókokhoz érhető el.

Összes versioning funkciót, beleértve a [pillanatképek](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob) és [helyreállítható törlési](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) még nem érhetők el, a Storage-fiókok, amelyek hierarchikus névterek engedélyezettek.

## <a name="object-level-storage-tiers"></a>Objektum szintű tárolási rétegek

Objektum szintű tárolási rétegek (gyors Elérésűre, ritka elérésű és archív) még nem érhetők el az Azure Data Lake Storage általános 2 fiókok esetében, de érhetők el a Storage-fiókok, amelyek nem rendelkeznek a hierarchikus tárolóhelyek engedélyezve van.

## <a name="azure-blob-storage-lifecycle-management-preview-policies"></a>Az Azure Blob Storage életciklus-kezelési (előzetes verzió) házirendek

Az Azure Blob Storage életciklus-kezelési (előzetes verzió) házirendek még nem érhetők el az Azure Data Lake Storage Gen2-fiókok esetében.

Ezek a szabályzatok a Storage-fiókok, amelyek nem rendelkeznek a hierarchikus szóközt engedélyezni érhetők el.

## <a name="diagnostic-logs"></a>Diagnosztikai naplók

Diagnosztikai naplók nem érhetők el az Azure Data Lake Storage Gen2-fiókok esetében.

Diagnosztikai naplók kérést, forduljon az Azure ügyfélszolgálatához. A fiók neve és az az időtartam, amelynek naplók van szüksége, adja meg azokat.
