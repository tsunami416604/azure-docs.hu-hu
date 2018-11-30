---
title: Ismert problémák az Azure Data Lake Storage Gen2 |} A Microsoft Docs
description: További információ a korlátozások és az Azure Data Lake Storage Gen2 ismert problémái
services: storage
author: normesta
manager: twooley
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: normesta
ms.openlocfilehash: fa37c23f936173c19f32b76dffab8908176ebd75
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2018
ms.locfileid: "52621805"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Az Azure Data Lake Storage Gen2 ismert problémái

Ez a cikk az ismert problémák és korlátozások vonatkoznak az Azure Data Lake Storage Gen2 tartalmazza.

## <a name="api-interoperability"></a>API-együttműködés

A BLOB Storage API-k és az Azure Data Lake Gen2 API-k nem együttműködésre egymással.

Ha szeretne dolgozni a fiókjába feltöltött tartalom mindegyikét a ugyanazon eszközzel, majd nem engedélyezi az hierarchikus névtér a Blob storage-fiókjában mindaddig, amíg ezen API-k válnak együttműködésre egymással. A hierarchikus névtér nélkül-tárfiókok használata azt jelenti, hogy ezután nem rendelkezik hozzáféréssel a Data Lake Storage Gen2 konkrét funkcióra, például a címtár- és a fájlrendszer hozzáférés-vezérlési listák.

## <a name="blob-storage-apis"></a>A BLOB storage API-k

A BLOB storage API-k még nem érhetők el az Azure Data Lake Storage általános 2 fiókokhoz.

Ezen API-k sikerült merülnek fel, mert a Blob Storage API-k még nem működik együtt az Azure Data Lake Gen2 API-k nem szándékos adatok hozzáférési problémák elkerülése érdekében le vannak tiltva.

Virtuális gép (VM) a nem felügyelt lemezek ezen API-k attól függenek, ezért ha engedélyezi a hierarchikus névterek, a storage-fiók, érdemes a nem felügyelt Virtuálisgép-lemezek elhelyezését a storage-fiók, amely nem rendelkezik a hierarchikus névterek engedélyezettek.

## <a name="azure-storage-explorer"></a>Azure Storage Explorer

Egyes funkciók a Storage Explorerben még nem működnek az Azure Data Lake Storage Gen2 fájlrendszerek. Ezek a korlátozások vonatkoznak, mindkettő a [önálló verziója](https://azure.microsoft.com/features/storage-explorer/) Azure Storage Explorer, valamint a verzió, amely akkor jelenik meg az Azure Portalon.

## <a name="blob-viewing-tool"></a>BLOB megtekintése eszköz

Eszköz megtekintése az Azure Portallal BLOB csak korlátozott mértékben támogatja az Azure Data Lake Storage Gen2.

## <a name="third-party-applications"></a>Harmadik féltől származó alkalmazások

Harmadik féltől származó alkalmazások az Azure Data Lake Storage Gen2 nem támogatja.

Minden külső alkalmazás szolgáltatója belátása szerint a rendszer támogatja. Jelenleg a Blob storage API-k és az Azure Data Lake Storage Gen2 API-k nem használható ugyanaz a tartalom kezeléséhez. Mert dolgozunk, hogy együttműködést, akkor lehet, hogy számos harmadik felektől származó eszközök automatikusan támogatják az Azure Data Lake Storage Gen2.

## <a name="azcopy-support"></a>Az AzCopy-támogatás

Az AzCopy 8-as verzió nem támogatja az Azure Data Lake Storage Gen2.

Ehelyett használja az AzCopy legújabb előzetes verzióját ( [AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json) ), az Azure Data Lake Storage Gen2 végpontokat támogatja.

## <a name="oauth-authentication"></a>OAuth-hitelesítés

Például az Azure Databricks, a HDInsight és az Azure Data Factory szolgáltatás még nem integrálható az Azure Active Directory (Azure AD) OAuth tulajdonosi jogkivonat-hitelesítés.

## <a name="access-control-lists-acl"></a>Hozzáférés-vezérlési listák (ACL)

Címtár- és fájlszintű hozzáférés-vezérlési listák (ACL) nagyon nehéz kezelni. Nincs olyan Felhasználóifelület-alapú eszköz, amellyel lekérése, és állítsa be ezeket hozzáférés-vezérlési listák.

## <a name="azure-event-grid"></a>Azure Event Grid

[Az Azure Event Grid](https://azure.microsoft.com/services/event-grid/) nem események fogadása az Azure Data Lake Gen2 fiókok mert azok a fiókok nem még létre őket.  

## <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

Szerepköralapú hozzáférés-vezérlés fájlrendszerobjektumok egy Azure Data Lake Storage Gen2-fiók nem alkalmazhat.

## <a name="sql-data-warehouse-polybase"></a>Az SQL Data Warehouse PolyBase

Ha a Storage-tűzfalak engedélyezve vannak az Azure Storage-fiókot, az SQL Data Warehouse [Polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide?view=sql-server-2017) nem érhető el ezeket a fiókokat.

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
