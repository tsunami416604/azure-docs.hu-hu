---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 02/19/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: d97a2350765ac321cf77f8a9f84825c88d0c9185
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56824253"
---
Az Azure storage-adatokhoz való hozzáférést biztosít a következő beépített RBAC-szerepkörök:

- [Tárolási Blob adatok tulajdonosa (előzetes verzió)](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner-preview): Állítsa be a tulajdonosi és a POSIX hozzáférés-vezérlés kezelése az Azure Data Lake Storage Gen2 (előzetes verzió). További információkért lásd: [hozzáférés-vezérlés az Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Storage-Blobadatok Közreműködője (előzetes verzió)](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor-preview): A Blob storage-erőforrások olvasási, írási és törlési engedélyek használatával.
- [Storage-Blobadatok olvasója (előzetes verzió)](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader-preview): Használja a Blob storage-erőforrások csak olvasható engedélyek.
- [Storage-Üzenetsorbeli adatok Közreműködője (előzetes verzió)](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor-preview): Olvasási, írási és törlési engedélyt az Azure-üzenetsorok használatával.
- [Storage-Üzenetsorbeli adatok olvasója (előzetes verzió)](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader-preview): Csak olvasási engedélyeket az Azure-üzenetsorok használatával.

További információ a beépített szerepkörök határozza meg az Azure Storage, lásd: [megismerheti a szerepkör-definíciók](../articles/role-based-access-control/role-definitions.md#management-and-data-operations-preview).

Az Azure Storage támogatja az egyéni RBAC-szerepkörök is. További információkért lásd: [egyéni szerepkörök létrehozása az Azure szerepköralapú hozzáférés-vezérlés](../articles/role-based-access-control/custom-roles.md). 
