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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80519546"
---
Az Azure a következő beépített RBAC-szerepköröket biztosítja a blob-és üzenetsor-információhoz való hozzáférés engedélyezéséhez az Azure AD és a OAuth használatával:

- [Storage blob-adattulajdonos](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner): a használatával a Azure Data Lake Storage Gen2 számára biztosíthatja a tulajdonosi és a POSIX hozzáférés-vezérlést. További információ: [hozzáférés-vezérlés Azure Data Lake Storage Gen2ban](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Storage blob-adatközreműködői](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor): a használatával olvasási/írási/törlési engedélyeket adhat a blob Storage-erőforrásokhoz.
- [Storage blob-Adatolvasó](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader): a használatával írásvédett engedélyeket adhat a blob Storage-erőforrásokhoz.
- [Storage-üzenetsor adatközreműködői](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): a használatával olvasási/írási/törlési engedélyeket adhat az Azure Queues szolgáltatásnak.
- [Storage-üzenetsor Adatolvasója](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): a használatával csak olvasási jogosultságot adhat az Azure Queues szolgáltatásnak.
- [Storage-üzenetsor adatüzenet-processzora](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): a használatával megtekintheti az Azure Storage-várólistákban lévő üzenetekhez való betekintés, lekérés és törlés engedélyeit.
- [Tárolási várólista Adatüzenetének küldője](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): a használatával adhat hozzá engedélyeket az üzenetekhez az Azure Storage-várólistákban.

Az Azure Storage-hoz készült beépített RBAC-szerepkörökről és az adatszolgáltatásokról, valamint a felügyeleti szolgáltatásról az Azure [RBAC beépített Azure-beli szerepkörök](../articles/role-based-access-control/built-in-roles.md#storage) **című részében** talál további információt. Emellett az Azure-ban engedélyeket biztosító szerepkörök különböző típusaival kapcsolatos információkért lásd: [klasszikus előfizetés-rendszergazdai szerepkörök, Azure RBAC-szerepkörök és Azure ad-szerepkörök](../articles/role-based-access-control/rbac-and-directory-admin-roles.md).

> [!NOTE]
> A RBAC szerepkör-hozzárendelések akár öt percet is igénybe vehetnek.
>
> Csak az adathozzáféréshez explicit módon definiált szerepköröknek kell megadniuk a rendszerbiztonsági tag számára a blob-vagy üzenetsor-adat elérését. A **tulajdonos**, **közreműködő**és a **Storage-fiók közreműködő** szerepkörei lehetővé teszik a rendszerbiztonsági tag számára a Storage-fiókok kezelését, de nem biztosítanak hozzáférést az adott fiókon belüli blob-vagy üzenetsor-információhoz.
>
> A Azure Portal blob-vagy üzenetsor-adataihoz való hozzáférés az Azure AD-fiók vagy a Storage-fiók hozzáférési kulcsa alapján engedélyezhető. További információ: [a Azure Portal használata a blob-vagy üzenetsor-adatok eléréséhez](../articles/storage/common/storage-access-blobs-queues-portal.md).
