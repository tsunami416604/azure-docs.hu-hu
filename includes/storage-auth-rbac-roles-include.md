---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/21/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: c7fa81f6d23962eedb3dfeafdd397b62a83d130e
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58372678"
---
Az Azure storage-adatokhoz való hozzáférést biztosít a következő beépített RBAC-szerepkörök:

- [Tárolási Blob adatok tulajdonosa](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner-preview): Állítsa be a tulajdonosi és a POSIX hozzáférés-vezérlés kezelése az Azure Data Lake Storage Gen2 (előzetes verzió). További információkért lásd: [hozzáférés-vezérlés az Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Storage-Blobadatok Közreműködője](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor-preview): A Blob storage-erőforrások olvasási, írási és törlési engedélyek használatával.
- [Storage-Blobadatok olvasója](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader-preview): Használja a Blob storage-erőforrások csak olvasható engedélyek.
- [Storage-Üzenetsorbeli adatok Közreműködője](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor-preview): Olvasási, írási és törlési engedélyt az Azure-üzenetsorok használatával.
- [Storage-Üzenetsorbeli adatok olvasója](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader-preview): Csak olvasási engedélyeket az Azure-üzenetsorok használatával.
- [Tárolási üzenetsor adatok üzenetet feldolgozó](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor-preview): Használja a grant peek, lekérése és törlési jogosultsággal az Azure Storage-üzenetsorok üzenetek.
- [Tárolási üzenetsor adatok üzenetet küldő](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender-preview): Engedélyeket adhat hozzá engedélyeket az Azure Storage-üzenetsorok üzenetek.

További információ a beépített szerepkörök határozza meg az Azure Storage, lásd: [megismerheti a szerepkör-definíciók](../articles/role-based-access-control/role-definitions.md#management-and-data-operations-preview). Egyéni RBAC-szerepkörök létrehozásával kapcsolatos információkért lásd: [egyéni szerepkörök létrehozása az Azure szerepköralapú hozzáférés-vezérlés](../articles/role-based-access-control/custom-roles.md). 
