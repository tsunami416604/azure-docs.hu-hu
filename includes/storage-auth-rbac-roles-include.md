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
ms.openlocfilehash: 9402c4b24c9d64b4b69d750fbd19de40cda396f3
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/01/2019
ms.locfileid: "57218090"
---
Az Azure storage-adatokhoz való hozzáférést biztosít a következő beépített RBAC-szerepkörök:

- [Tárolási Blob adatok tulajdonosa (előzetes verzió)](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner-preview): Állítsa be a tulajdonosi és a POSIX hozzáférés-vezérlés kezelése az Azure Data Lake Storage Gen2 (előzetes verzió). További információkért lásd: [hozzáférés-vezérlés az Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Storage-Blobadatok Közreműködője (előzetes verzió)](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor-preview): A Blob storage-erőforrások olvasási, írási és törlési engedélyek használatával.
- [Storage-Blobadatok olvasója (előzetes verzió)](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader-preview): Használja a Blob storage-erőforrások csak olvasható engedélyek.
- [Storage-Üzenetsorbeli adatok Közreműködője (előzetes verzió)](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor-preview): Olvasási, írási és törlési engedélyt az Azure-üzenetsorok használatával.
- [Storage-Üzenetsorbeli adatok olvasója (előzetes verzió)](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader-preview): Csak olvasási engedélyeket az Azure-üzenetsorok használatával.

További információ a beépített szerepkörök határozza meg az Azure Storage, lásd: [megismerheti a szerepkör-definíciók](../articles/role-based-access-control/role-definitions.md#management-and-data-operations-preview). Egyéni RBAC-szerepkörök létrehozásával kapcsolatos információkért lásd: [egyéni szerepkörök létrehozása az Azure szerepköralapú hozzáférés-vezérlés](../articles/role-based-access-control/custom-roles.md). 
