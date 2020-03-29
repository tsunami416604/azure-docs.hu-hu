---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/17/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 4b10955a1d3b85acbcae109836bebc03ec04c72c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76279366"
---
Az Azure a következő beépített RBAC-szerepköröket biztosítja a blob- és várólista-adatokhoz való hozzáférés engedélyezéséhez az Azure AD és az OAuth használatával:

- [Storage Blob Data Owner](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner): Az Azure Data Lake Storage Gen2 felhasználói tulajdonjogának beállításával és a POSIX hozzáférés-vezérlésének kezelésével használható. További információ: [Access control in Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Storage Blob Data Contributor:](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor)Olvasási/írási/törlési engedélyek et adhat a Blob storage-erőforrásoknak.
- [Storage Blob Data Reader:](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader)A Blob storage-erőforrások írásvédett engedélyek et adhat.
- [Storage Queue Data Contributor:](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor)Olvasási/írási/törlési engedélyek et adhat az Azure-várólistáknak.
- [Tárolási várólista-adatolvasó:](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader)írásvédett engedélyek et adhat az Azure-várólistáknak.
- [Tárolási várólista-adatfeldolgozó:](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor)Betekintési, beolvasási és törlési engedélyek et adhat az Azure Storage-várólistákban lévő üzenetekhez.
- [Tárolási várólista-adatok küldése:](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender)Az Azure Storage-várólistákban lévő üzenetekhez adjon hozzá engedélyeket.

> [!NOTE]
> Az RBAC szerepkör-hozzárendelések propagálása akár öt percet is igénybe vehet.
>
> Csak az adathozzáféréshez explicit módon definiált szerepkörök teszik lehetővé, hogy egy rendszerbiztonsági tag hozzáférjen a blob- vagy várólista-adatokhoz. Szerepkörök, például **tulajdonos,** **közreműködő**és **a tárfiók közreműködője** lehetővé teszi a rendszerbiztonsági tag egy tárfiók kezeléséhez, de nem biztosít hozzáférést a blob vagy várólista adatait a fiókon belül.
>
> Az Azure Portalon a blob- vagy várólista-adatokhoz való hozzáférés az Azure AD-fiók vagy a tárfiók hozzáférési kulcsa használatával is engedélyezhető. További információ: [Használja az Azure Portalon a blob- vagy várólista-adatok eléréséhez.](../articles/storage/common/storage-access-blobs-queues-portal.md)
