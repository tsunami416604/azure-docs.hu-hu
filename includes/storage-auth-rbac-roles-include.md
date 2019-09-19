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
ms.openlocfilehash: c2b409f0eefe5efa389432cbb007cc08e0c6ae1e
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71078253"
---
Az Azure a következő beépített RBAC-szerepköröket biztosítja a blob-és üzenetsor-információhoz való hozzáférés engedélyezéséhez az Azure AD és a OAuth használatával:

- [Storage blob-adattulajdonos](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner): A használatával megadhatja a tulajdonosi és a POSIX hozzáférés-vezérlést a Azure Data Lake Storage Gen2 számára. További információ: [hozzáférés-vezérlés Azure Data Lake Storage Gen2ban](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Storage-blob adatközreműködői](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor): A használatával olvasási/írási/törlési engedélyeket adhat a blob Storage-erőforrásokhoz.
- [Storage blob-Adatolvasó](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader): A használatával írásvédett engedélyeket adhat a blob Storage-erőforrásokhoz.
- [Tárolási várólista adatközreműködői](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Az használatával olvasási/írási/törlési engedélyeket adhat az Azure-várólistáknak.
- [Tárolási várólista Adatolvasója](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): A használatával írásvédett engedélyeket adhat az Azure-várólistáknak.
- [Tárolási üzenetsor adatüzenet-processzora](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): A használatával megadhatja a betekintési, lekérési és törlési engedélyeket az Azure Storage-várólisták üzeneteihez.
- [Tárolási várólista Adatüzenetének küldője](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): A használatával adhat hozzá engedélyeket az üzenetekhez az Azure Storage-várólistákban.

> [!NOTE]
> A RBAC szerepkör-hozzárendelések akár öt percet is igénybe vehetnek.
>
> Csak az adathozzáféréshez explicit módon definiált szerepköröknek kell megadniuk a rendszerbiztonsági tag számára a blob-vagy üzenetsor-adat elérését. A **tulajdonos**, **közreműködő**és a **Storage-fiók közreműködő** szerepkörei lehetővé teszik a rendszerbiztonsági tag számára a Storage-fiókok kezelését, de nem biztosítanak hozzáférést az adott fiókon belüli blob-vagy üzenetsor-információhoz.
