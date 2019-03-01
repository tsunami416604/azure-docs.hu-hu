---
title: Ismert problémák az Azure Data Lake Storage Gen2 |} A Microsoft Docs
description: További információ a korlátozások és az Azure Data Lake Storage Gen2 ismert problémái
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: normesta
ms.openlocfilehash: 89cfdbdaa034bae5ca736ccb9164255b833ed75d
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/01/2019
ms.locfileid: "57194708"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Az Azure Data Lake Storage Gen2 ismert problémái

Ez a cikk az ismert problémák és korlátozások vonatkoznak a Data Lake Storage Gen2 tartalmazza.

## <a name="sdk-support-for-data-lake-storage-gen2-accounts"></a>SDK-t a Data Lake Storage Gen2-fiókok támogatása

Nincsenek elérhető SDK-k, amelyek a Data Lake Storage Gen2 fiókokkal fog működni.

## <a name="blob-storage-apis"></a>A BLOB storage API-k

A BLOB storage API-k még nem érhetők el a Data Lake Storage Gen2-fiókokat.

Ezen API-k sikerült merülnek fel, mert a Blob Storage API-k még nem működik együtt az Azure Data Lake Gen2 API-k nem szándékos adatok hozzáférési problémák elkerülése érdekében le vannak tiltva.

Ha ezekkel az API-adatok betöltése előtt letiltottuk azokat, és a, termelési adatokhoz való hozzáférést, majd lépjen kapcsolatba Microsoft Support a következő információkat:

* Előfizetés-azonosító (GUID azonosítója, nem a név)

* Storage-fiók neve

* Éles környezetben aktívan érinti-e, és ha igen, melyik storage-fiókok?

* Akkor is, ha az aktívan nem érintett éles környezetben, akkor mondja el, hogy ezeket az adatokat a valamilyen okból egy másik tárfiókba másolni kell, és ha igen, hogy miért?

Ilyen körülmények azt is visszaállíthatók hozzáférés a Blob API-t egy korlátozott ideig, hogy a storage-fiókra, amely nem rendelkezik a hierarchikus névtér szolgáltatás engedélyezve van az adatokat másolja.

Virtuális gép (VM) a nem felügyelt lemezek a letiltott Blob Storage API-k attól függenek, ezért ha szeretné engedélyezni a storage-fiók, hierarchikus névtér, érdemes egy storage-fiókot, amely nem rendelkezik a hierarchikus névtér funkció elhelyezését a nem felügyelt Virtuálisgép-lemezek engedélyezve van.

## <a name="api-interoperability"></a>API-együttműködés

A BLOB Storage API-k és az Azure Data Lake Gen2 API-k nem együttműködésre egymással.

Ha az eszközök, alkalmazások, szolgáltatások vagy parancsfájlok, amelyek a Blob API-kkal rendelkezik, és ezek segítségével a fiókjába feltöltött tartalom mindegyikét dolgozni szeretne, majd nem engedélyezi az a Blob storage-fiókjában hierarchikus névtér mindaddig, amíg a Blob API-k válnak együttműködik az Azure Data Lake Gen2 API-k. Hierarchikus névtér nélkül-tárfiókok használata azt jelenti, hogy ezután nem rendelkezik hozzáféréssel a Data Lake Storage Gen2 konkrét funkcióra, például a címtár- és a fájlrendszer hozzáférés-vezérlési listák.

## <a name="azure-storage-explorer"></a>Azure Storage Explorer

Megtekintéséhez vagy a Data Lake Storage Gen2-fiókok kezelése az Azure Storage Explorer használatával kell rendelkeznie legalább verzió `1.6.0` az eszköz, amely egy [ingyenesen letölthető](https://azure.microsoft.com/features/storage-explorer/).

Vegye figyelembe, hogy a Storage Explorer, az Azure Portalra beágyazott verziója amelyen jelenleg nem támogatja a megtekintése, vagy a hierarchikus névtér szolgáltatás engedélyezve van a Data Lake Storage Gen2-fiókok kezelése.

## <a name="blob-viewing-tool"></a>BLOB megtekintése eszköz

Eszköz megtekintése az Azure Portallal BLOB csak korlátozott mértékben támogatja a Data Lake Storage Gen2.

## <a name="third-party-applications"></a>Harmadik féltől származó alkalmazások

Harmadik féltől származó alkalmazások előfordulhat, hogy támogatja a Data Lake Storage Gen2.

Minden külső alkalmazás szolgáltatója belátása szerint a rendszer támogatja. Jelenleg a Blob storage API-k és Data Lake Storage Gen2 API-k nem használható ugyanaz a tartalom kezeléséhez. Mert dolgozunk, hogy együttműködést, lehetőség számos harmadik felektől származó eszközök automatikusan támogatja a Data Lake Storage Gen2.

## <a name="azcopy-support"></a>Az AzCopy-támogatás

Az AzCopy 8-as verzió nem támogatja a Data Lake Storage Gen2.

Ehelyett használja az AzCopy legújabb előzetes verzióját ( [AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json) ), a Data Lake Storage Gen2 végpontokat támogatja.

## <a name="azure-event-grid"></a>Azure Event Grid

[Az Azure Event Grid](https://azure.microsoft.com/services/event-grid/) nem események fogadása az Azure Data Lake Gen2 fiókok mert azok a fiókok nem még létre őket.  

## <a name="soft-delete-and-snapshots"></a>Helyreállítható törlés és pillanatképek

Helyreállítható törlés és a pillanatképek nem a Data Lake Storage Gen2 fiókokhoz érhető el.

Összes versioning funkciót, beleértve a [pillanatképek](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob) és [helyreállítható törlési](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) még nem érhetők el, a Storage-fiókok, amelyek rendelkeznek a hierarchikus névtér szolgáltatás engedélyezve van.

## <a name="object-level-storage-tiers"></a>Objektum szintű tárolási rétegek

Objektum szintű tárolási rétegek (gyors Elérésűre, ritka elérésű és archív) még nem érhetők el az Azure Data Lake Storage általános 2 fiókok esetében, de érhetők el a Storage-fiókok, amelyek nem rendelkeznek a hierarchikus névtér szolgáltatás engedélyezve van.

## <a name="azure-blob-storage-lifecycle-management-policies"></a>Az Azure Blob Storage életciklus-kezelési házirendek

Az Azure Blob Storage életciklus-kezelési házirendek még nem érhetők el a Data Lake Storage Gen2-fiókokat.

Tárfiókok, amelyek nem rendelkeznek a hierarchikus névtér szolgáltatás engedélyezve van, ezek a szabályzatok érhetők el.

## <a name="diagnostic-logs"></a>Diagnosztikai naplók

Diagnosztikai naplók nem érhetők el a Data Lake Storage Gen2-fiókok esetében.
