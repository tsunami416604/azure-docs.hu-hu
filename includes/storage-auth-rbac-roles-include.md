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
ms.openlocfilehash: d73bab76860abf3e9fa442dad44e1ddb66d147a3
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519546"
---
Az Azure a következő beépített RBAC-szerepköröket biztosítja a blob- és várólista-adatokhoz való hozzáférés engedélyezéséhez az Azure AD és az OAuth használatával:

- [Storage Blob Data Owner](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner): Az Azure Data Lake Storage Gen2 felhasználói tulajdonjogának beállításával és a POSIX hozzáférés-vezérlésének kezelésével használható. További információ: [Access control in Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Storage Blob Data Contributor:](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor)Olvasási/írási/törlési engedélyek et adhat a Blob storage-erőforrásoknak.
- [Storage Blob Data Reader:](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader)A Blob storage-erőforrások írásvédett engedélyek et adhat.
- [Storage Queue Data Contributor:](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor)Olvasási/írási/törlési engedélyek et adhat az Azure-várólistáknak.
- [Tárolási várólista-adatolvasó:](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader)írásvédett engedélyek et adhat az Azure-várólistáknak.
- [Tárolási várólista-adatfeldolgozó:](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor)Betekintési, beolvasási és törlési engedélyek et adhat az Azure Storage-várólistákban lévő üzenetekhez.
- [Tárolási várólista-adatok küldése:](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender)Az Azure Storage-várólistákban lévő üzenetekhez adjon hozzá engedélyeket.

Az Azure Storage beépített RBAC szerepköreiről az adatszolgáltatások és a felügyeleti szolgáltatás számára részletes en olvassa el az [Azure RBAC beépített szerepkörei](../articles/role-based-access-control/built-in-roles.md#storage) **tárolási szakaszát.** Az Azure-ban engedélyeket biztosító különböző típusú szerepkörökről további információt a [Klasszikus előfizetéses rendszergazdai szerepkörök, az Azure RBAC-szerepkörök és az Azure AD-szerepkörök](../articles/role-based-access-control/rbac-and-directory-admin-roles.md)című témakörben talál.

> [!NOTE]
> Az RBAC szerepkör-hozzárendelések propagálása akár öt percet is igénybe vehet.
>
> Csak az adathozzáféréshez explicit módon definiált szerepkörök teszik lehetővé, hogy egy rendszerbiztonsági tag hozzáférjen a blob- vagy várólista-adatokhoz. Szerepkörök, például **tulajdonos,** **közreműködő**és **a tárfiók közreműködője** lehetővé teszi a rendszerbiztonsági tag egy tárfiók kezeléséhez, de nem biztosít hozzáférést a blob vagy várólista adatait a fiókon belül.
>
> Az Azure Portalon a blob- vagy várólista-adatokhoz való hozzáférés az Azure AD-fiók vagy a tárfiók hozzáférési kulcsa használatával is engedélyezhető. További információ: [Használja az Azure Portalon a blob- vagy várólista-adatok eléréséhez.](../articles/storage/common/storage-access-blobs-queues-portal.md)
