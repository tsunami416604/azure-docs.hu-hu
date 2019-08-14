---
title: Hozzáférés engedélyezése az Azure Storage-hoz | Microsoft Docs
description: Ismerje meg az Azure Storage-hoz való hozzáférés engedélyezésének különböző módjait, beleértve a Azure Active Directory, a megosztott kulcsos hitelesítést vagy a közös hozzáférési aláírásokat.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 3cdbb6587871b970c0188c0b8f510cc9f1f4fe07
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68985177"
---
# <a name="authorizing-access-to-azure-storage"></a>Hozzáférés engedélyezése az Azure Storage-hoz

Minden alkalommal, amikor hozzáfér a Storage-fiókban lévő összes adathoz, az ügyfél HTTP/HTTPS-alapú kérést tesz elérhetővé az Azure Storage-ban. A biztonságos erőforrásokra vonatkozó minden kérést engedélyezni kell, hogy a szolgáltatás biztosítsa, hogy az ügyfél rendelkezik az adathozzáféréshez szükséges engedélyekkel.

Az alábbi táblázat azokat a lehetőségeket ismerteti, amelyeket az Azure Storage biztosít az erőforrásokhoz való hozzáférés engedélyezéséhez:

|  |Megosztott kulcs (Storage-fiók kulcsa)  |Közös hozzáférésű jogosultságkód (SAS)  |Azure Active Directory (Azure AD)  |Névtelen nyilvános olvasási hozzáférés  |
|---------|---------|---------|---------|---------|
|Azure Blobs     |[Támogatott](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |[Támogatott](storage-sas-overview.md)         |[Támogatott](storage-auth-aad.md)         |[Támogatott](../blobs/storage-manage-access-to-resources.md)         |
|Azure Files (SMB)     |[Támogatott](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |Nem támogatott         |[Támogatott, csak a HRE tartományi szolgáltatásokkal](../files/storage-files-active-directory-overview.md)         |Nem támogatott         |
|Azure Files (REST)     |[Támogatott](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |[Támogatott](storage-sas-overview.md)         |Nem támogatott         |Nem támogatott         |
|Azure-üzenetsorok     |[Támogatott](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |[Támogatott](storage-sas-overview.md)         |[Támogatott](storage-auth-aad.md)         |Nem támogatott         |
|Azure-táblák     |[Támogatott](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |[Támogatott](storage-sas-overview.md)         |Nem támogatott         |Nem támogatott         |

Az egyes engedélyezési beállítások rövid ismertetését az alábbiakban találja:

- **Azure Active Directory (Azure ad) integráció** a blobokhoz és a várólistákhoz. Az Azure AD szerepköralapú hozzáférés-vezérlést (RBAC) biztosít, amely részletesen szabályozza az ügyfelek hozzáférését egy Storage-fiók erőforrásaihoz. A blobok és várólisták Azure AD-integrálásával kapcsolatos további információkért lásd: [kérelmek hitelesítése az Azure Storage](storage-auth-aad.md)-ba a Azure Active Directory használatával.

- A fájlok **Azure ad Domain Services (DS) integrációja (előzetes verzió)** . Azure Files támogatja az identitás-alapú hitelesítést az Azure AD DS keresztül az SMB protokollon keresztül. Ezzel a RBAC részletesen szabályozhatja az ügyfelek hozzáférését egy Storage-fiók erőforrásaihoz. További információ az Azure AD-integrációról a tartományi szolgáltatásokkal való fájlokhoz: [Azure Files Azure Active Directory tartományi szolgáltatás (HRE DS) hitelesítési támogatásának áttekintése az SMB-hozzáféréshez (előzetes verzió)](../files/storage-files-active-directory-overview.md).

- A Blobok, a fájlok, a várólisták és a táblák **megosztott kulcsos hitelesítése** . A megosztott kulcsot használó ügyfelek minden, a Storage-fiók elérési kulcsával aláírt kérelemmel továbbítanak egy fejlécet. További információ: [Engedélyezés megosztott kulccsal](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/).
- A blobokhoz, fájlokhoz, várólistákhoz és táblákhoz tartozó **közös hozzáférésű aláírások** . A közös hozzáférésű aláírások (SAS) korlátozott delegált hozzáférést biztosítanak egy Storage-fiók erőforrásaihoz. Korlátozásokat adhat hozzá az aláírás érvényességi időintervallumához vagy az általa biztosított engedélyekhez, és rugalmasságot biztosít a hozzáférés kezelésében. További információ: Shared [Access Signatures (SAS) használata](storage-sas-overview.md).
- **Névtelen nyilvános olvasási hozzáférés** tárolók és Blobok számára. Az engedélyezés nem szükséges. További információkért lás a [tárolók és blobok névtelen olvasási hozzáférésének kezelésével](../blobs/storage-manage-access-to-resources.md) foglalkozó témakört.  

Alapértelmezés szerint az Azure Storage összes erőforrása védett, és csak a fiók tulajdonosa számára érhető el. Habár a fentiekben ismertetett engedélyezési stratégiák bármelyikét felhasználhatja az ügyfelek számára a Storage-fiók erőforrásaihoz való hozzáférés biztosítására, a Microsoft az Azure AD használatát javasolja, ha lehetséges a maximális biztonság és a könnyű használat. 
