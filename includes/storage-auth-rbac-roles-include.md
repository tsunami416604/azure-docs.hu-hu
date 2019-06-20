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
ms.openlocfilehash: 026717dff2b6883eb643497dec91226e4afe8133
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178793"
---
Az Azure beépített RBAC-szerepkörök biztosít az Azure AD-vel blob és üzenetsor-adatokhoz való hozzáférés engedélyezése és az OAuth révén:

- [Tárolási Blob adatok tulajdonosa](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner): Állítsa be a tulajdonosi és a POSIX hozzáférés-vezérlés kezelése az Azure Data Lake Storage Gen2 (előzetes verzió). További információkért lásd: [hozzáférés-vezérlés az Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Storage-Blobadatok Közreműködője](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor): A Blob storage-erőforrások olvasási, írási és törlési engedélyek használatával.
- [Storage-Blobadatok olvasója](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader): Használja a Blob storage-erőforrások csak olvasható engedélyek.
- [Storage-Üzenetsorbeli adatok Közreműködője](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Olvasási, írási és törlési engedélyt az Azure-üzenetsorok használatával.
- [Storage-Üzenetsorbeli adatok olvasója](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): Csak olvasási engedélyeket az Azure-üzenetsorok használatával.
- [Tárolási üzenetsor adatok üzenetet feldolgozó](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): Használja a grant peek, lekérése és törlési jogosultsággal az Azure Storage-üzenetsorok üzenetek.
- [Tárolási üzenetsor adatok üzenetet küldő](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): Engedélyeket adhat hozzá engedélyeket az Azure Storage-üzenetsorok üzenetek.

> [!NOTE]
> Ne feledje, hogy az RBAC szerepkör-hozzárendelések propagálása akár öt percet is igénybe vehet.
