---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/25/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 4f9a828e5233c88db2106bc648c07578927e0d29
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68514866"
---
Az Azure a következő beépített RBAC-szerepköröket biztosítja a blob-és üzenetsor-információhoz való hozzáférés engedélyezéséhez az Azure AD és a OAuth használatával:

- [Storage blob](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner)-adattulajdonos: A használatával megadhatja a tulajdonosi és a POSIX hozzáférés-vezérlést a Azure Data Lake Storage Gen2 számára. További információ: [hozzáférés-vezérlés Azure Data Lake Storage Gen2ban](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Storage-blob adatközreműködői](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor): A használatával olvasási/írási/törlési engedélyeket adhat a blob Storage-erőforrásokhoz.
- [Storage blob](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader)-Adatolvasó: A használatával írásvédett engedélyeket adhat a blob Storage-erőforrásokhoz.
- [Tárolási várólista adatközreműködői](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Az használatával olvasási/írási/törlési engedélyeket adhat az Azure-várólistáknak.
- [Tárolási várólista](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader)adatolvasója: A használatával írásvédett engedélyeket adhat az Azure-várólistáknak.
- [Tárolási üzenetsor adatüzenet](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor)-processzora: A használatával megadhatja a betekintési, lekérési és törlési engedélyeket az Azure Storage-várólisták üzeneteihez.
- [Tárolási várólista](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender)adatüzenetének küldője: A használatával adhat hozzá engedélyeket az üzenetekhez az Azure Storage-várólistákban.

> [!NOTE]
> Ne feledje, hogy a RBAC szerepkör-hozzárendelések akár öt percet is igénybe vehetnek.
