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
ms.openlocfilehash: 9b8418dba12748915666c6a91ee65b37c0f59ace
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58807680"
---
Az Azure storage-adatokhoz való hozzáférést biztosít a következő beépített RBAC-szerepkörök:

- [Tárolási Blob adatok tulajdonosa](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner): Állítsa be a tulajdonosi és a POSIX hozzáférés-vezérlés kezelése az Azure Data Lake Storage Gen2 (előzetes verzió). További információkért lásd: [hozzáférés-vezérlés az Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Storage-Blobadatok Közreműködője](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor): A Blob storage-erőforrások olvasási, írási és törlési engedélyek használatával.
- [Storage-Blobadatok olvasója](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader): Használja a Blob storage-erőforrások csak olvasható engedélyek.
- [Storage-Üzenetsorbeli adatok Közreműködője](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Olvasási, írási és törlési engedélyt az Azure-üzenetsorok használatával.
- [Storage-Üzenetsorbeli adatok olvasója](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): Csak olvasási engedélyeket az Azure-üzenetsorok használatával.
- [Tárolási üzenetsor adatok üzenetet feldolgozó](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): Használja a grant peek, lekérése és törlési jogosultsággal az Azure Storage-üzenetsorok üzenetek.
- [Tárolási üzenetsor adatok üzenetet küldő](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): Engedélyeket adhat hozzá engedélyeket az Azure Storage-üzenetsorok üzenetek.

> [!IMPORTANT]
> RBAC szerepkör-hozzárendelések propagálása akár öt percet is igénybe vehet.

További információ a beépített szerepkörök határozza meg az Azure Storage, lásd: [megismerheti a szerepkör-definíciók](../articles/role-based-access-control/role-definitions.md#management-and-data-operations-preview). Egyéni RBAC-szerepkörök létrehozásával kapcsolatos információkért lásd: [egyéni szerepkörök létrehozása az Azure szerepköralapú hozzáférés-vezérlés](../articles/role-based-access-control/custom-roles.md). 
