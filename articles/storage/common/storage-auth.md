---
title: Adatműveletek engedélyezése
titleSuffix: Azure Storage
description: Ismerje meg az Azure Storage-hoz való hozzáférés engedélyezésének különböző módjait, beleértve a Azure Active Directory, a megosztott kulcs engedélyezését vagy a közös hozzáférésű aláírásokat (SAS).
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 783e8666e2602f9251d81e976a27fbce099defa2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75460534"
---
# <a name="authorizing-access-to-data-in-azure-storage"></a>Az Azure Storage-beli adathozzáférés engedélyezése

Minden alkalommal, amikor hozzáfér a Storage-fiókban lévő összes adathoz, az ügyfél HTTP/HTTPS-alapú kérést tesz elérhetővé az Azure Storage-ban. Minden, biztonságos erőforráshoz érkező kérést engedélyezni kell, a szolgáltatás ugyanis így győződik meg arról, hogy az ügyfél rendelkezik az adatok hozzáféréséhez szükséges engedélyekkel.

Az alábbi táblázat azokat a lehetőségeket ismerteti, amelyeket az Azure Storage biztosít az erőforrásokhoz való hozzáférés engedélyezéséhez:

|  |Megosztott kulcs (Storage-fiók kulcsa)  |Közös hozzáférésű jogosultságkód (SAS)  |Azure Active Directory (Azure AD)  |Névtelen nyilvános olvasási hozzáférés  |
|---------|---------|---------|---------|---------|
|Azure Blobs     |[Támogatott](/rest/api/storageservices/authorize-with-shared-key/)         |[Támogatott](storage-sas-overview.md)         |[Támogatott](storage-auth-aad.md)         |[Támogatott](../blobs/storage-manage-access-to-resources.md)         |
|Azure Files (SMB)     |[Támogatott](/rest/api/storageservices/authorize-with-shared-key/)         |Nem támogatott         |[Támogatott, csak a HRE tartományi szolgáltatásokkal](../files/storage-files-active-directory-overview.md)         |Nem támogatott         |
|Azure Files (REST)     |[Támogatott](/rest/api/storageservices/authorize-with-shared-key/)         |[Támogatott](storage-sas-overview.md)         |Nem támogatott         |Nem támogatott         |
|Azure Queues     |[Támogatott](/rest/api/storageservices/authorize-with-shared-key/)         |[Támogatott](storage-sas-overview.md)         |[Támogatott](storage-auth-aad.md)         |Nem támogatott         |
|Azure-táblák     |[Támogatott](/rest/api/storageservices/authorize-with-shared-key/)         |[Támogatott](storage-sas-overview.md)         |Nem támogatott         |Nem támogatott         |

Az egyes engedélyezési beállítások rövid ismertetését az alábbiakban találja:

- **Azure Active Directory (Azure ad) integráció** a blobokhoz és a várólistákhoz. Az Azure AD szerepköralapú hozzáférés-vezérlést (RBAC) biztosít, amely részletesen szabályozza az ügyfelek hozzáférését egy Storage-fiók erőforrásaihoz. A blobok és várólisták Azure AD-integrálásával kapcsolatos további információkért lásd: [Az Azure-blobok és-várólisták hozzáférésének engedélyezése Azure Active Directory használatával](storage-auth-aad.md).

- A fájlok **Azure ad Domain Services (DS) integrációja (előzetes verzió)** . Azure Files támogatja az identitás-alapú hitelesítést az Azure-AD DS keresztül az SMB protokollon keresztül. A RBAC segítségével részletesen szabályozhatja az ügyfelek hozzáférését egy Storage-fiók Azure Files erőforrásaihoz. További információ az Azure AD-integrációról a tartományi szolgáltatásokkal való fájlokhoz: [Azure Files Azure Active Directory tartományi szolgáltatás (HRE DS) hitelesítési támogatásának áttekintése az SMB-hozzáféréshez (előzetes verzió)](../files/storage-files-active-directory-overview.md).

- A Blobok, a fájlok, a várólisták és a táblák **megosztott kulcsos hitelesítése** . A megosztott kulcsot használó ügyfelek minden, a Storage-fiók elérési kulcsával aláírt kérelemmel továbbítanak egy fejlécet. További információ: [Engedélyezés megosztott kulccsal](/rest/api/storageservices/authorize-with-shared-key/).
- A blobokhoz, fájlokhoz, várólistákhoz és táblákhoz tartozó **közös hozzáférésű aláírások** . A közös hozzáférésű aláírások (SAS) korlátozott delegált hozzáférést biztosítanak egy Storage-fiók erőforrásaihoz. Korlátozásokat adhat hozzá az aláírás érvényességi időintervallumához vagy az általa biztosított engedélyekhez, és rugalmasságot biztosít a hozzáférés kezelésében. További információ: [Shared Access Signatures (SAS) használata](storage-sas-overview.md).
- **Névtelen nyilvános olvasási hozzáférés** tárolók és Blobok számára. Az engedélyezés nem szükséges. További információkért lás a [tárolók és blobok névtelen olvasási hozzáférésének kezelésével](../blobs/storage-manage-access-to-resources.md) foglalkozó témakört.  

Alapértelmezés szerint az Azure Storage összes erőforrása védett, és csak a fiók tulajdonosa számára érhető el. Habár a fentiekben ismertetett engedélyezési stratégiák bármelyikét felhasználhatja az ügyfelek számára a Storage-fiók erőforrásaihoz való hozzáférés biztosítására, a Microsoft az Azure AD használatát javasolja, ha lehetséges a maximális biztonság és a könnyű használat.

## <a name="next-steps"></a>Következő lépések

- [Azure-blobok és-várólisták hozzáférésének engedélyezése Azure Active Directory használatával](storage-auth-aad.md)
- [Engedélyezés megosztott kulccsal](/rest/api/storageservices/authorize-with-shared-key/)
- [Korlátozott hozzáférés biztosítása az Azure Storage-erőforrásokhoz közös hozzáférésű aláírások (SAS) használatával](storage-sas-overview.md)
