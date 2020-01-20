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
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279366"
---
Az Azure a következő beépített RBAC-szerepköröket biztosítja a blob-és üzenetsor-információhoz való hozzáférés engedélyezéséhez az Azure AD és a OAuth használatával:

- [Storage blob-adattulajdonos](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner): a használatával a Azure Data Lake Storage Gen2 számára biztosíthatja a tulajdonosi és a POSIX hozzáférés-vezérlést. További információ: [hozzáférés-vezérlés Azure Data Lake Storage Gen2ban](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Storage blob-adatközreműködői](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor): a használatával olvasási/írási/törlési engedélyeket adhat a blob Storage-erőforrásokhoz.
- [Storage blob-Adatolvasó](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader): a használatával írásvédett engedélyeket adhat a blob Storage-erőforrásokhoz.
- [Storage-üzenetsor adatközreműködői](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): a használatával olvasási/írási/törlési engedélyeket adhat az Azure Queues szolgáltatásnak.
- [Storage-üzenetsor Adatolvasója](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): a használatával csak olvasási jogosultságot adhat az Azure Queues szolgáltatásnak.
- [Storage-üzenetsor adatüzenet-processzora](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): a használatával megtekintheti az Azure Storage-várólistákban lévő üzenetekhez való betekintés, lekérés és törlés engedélyeit.
- [Tárolási várólista Adatüzenetének küldője](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): a használatával adhat hozzá engedélyeket az üzenetekhez az Azure Storage-várólistákban.

> [!NOTE]
> A RBAC szerepkör-hozzárendelések akár öt percet is igénybe vehetnek.
>
> Csak az adathozzáféréshez explicit módon definiált szerepköröknek kell megadniuk a rendszerbiztonsági tag számára a blob-vagy üzenetsor-adat elérését. A **tulajdonos**, **közreműködő**és a **Storage-fiók közreműködő** szerepkörei lehetővé teszik a rendszerbiztonsági tag számára a Storage-fiókok kezelését, de nem biztosítanak hozzáférést az adott fiókon belüli blob-vagy üzenetsor-információhoz.
>
> A Azure Portal blob-vagy üzenetsor-adataihoz való hozzáférés az Azure AD-fiók vagy a Storage-fiók hozzáférési kulcsa alapján engedélyezhető. További információ: [a Azure Portal használata a blob-vagy üzenetsor-adatok eléréséhez](../articles/storage/common/storage-access-blobs-queues-portal.md).
