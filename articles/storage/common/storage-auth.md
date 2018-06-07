---
title: Azure Storage a hozzáférés engedélyezése |} Microsoft Docs
description: További tudnivalók az Azure Storage, beleértve az Azure Active Directory, a megosztott kulcsos hitelesítést vagy a közös hozzáférésű jogosultságkód való hozzáférés engedélyezésére a különböző módszereket.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 05/18/2018
ms.author: tamram
ms.openlocfilehash: 46bb332e28d7503e543ca3c99fa1099ef17f34c3
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660866"
---
# <a name="authorizing-access-to-azure-storage"></a>Azure Storage a hozzáférés engedélyezése

Minden alkalommal, amikor a tárfiókban lévő adatelérés az ügyfél kérelmet küld a HTTP/HTTPS-KAPCSOLATON keresztül Azure Storage. Minden kérelemnél biztonságos erőforráshoz engedéllyel kell rendelkeznie, így a szolgáltatás biztosítja, hogy az ügyfél rendelkezik-e az adatok eléréséhez szükséges engedélyekkel. Az Azure Storage ezek a beállítások, amelyek engedélyezik a hozzáférést erőforrások biztonságossá tételére kínál:

- **Az Azure Active Directory (Azure AD) integrálása (előzetes verzió)** a blobokat és üzenetsorokat. Az Azure AD egy ügyfélelérési a tárfiók erőforrásaihoz részletes szabályozhatják a szerepköralapú hozzáférés-vezérlést (RBAC) biztosít. További információkért lásd: [intézett hitelesítési kérésekhez az Azure Active Directoryval (előzetes verzió) Azure Storage](storage-auth-aad.md).
- **A megosztott kulcs engedélyezési** a blobokat, fájlok, üzenetsorokat és táblákat. Egy ügyfél megosztott kulcs használatával továbbítja a fejlécet minden kérelemnél, amely a fiók tárelérési kulcs használatával van aláírva. További információkért lásd: [megosztott kulccsal engedélyezés](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key/).
- **Közös hozzáférésű jogosultságkód** a blobokat, fájlok, üzenetsorokat és táblákat. Közös hozzáférésű jogosultságkód (SAS) a tárfiókban lévő erőforrások korlátozott delegált hozzáférést biztosítanak. Korlátozások a időtartam alatt, amelyhez az aláírás érvénytelen vagy a hozzáférés-kezelés rugalmasságot biztosít ad engedélyek hozzáadása. További információkért lásd: [használata közös hozzáférésű jogosultságkód (SAS)](storage-dotnet-shared-access-signature-part-1.md).
- **Névtelen nyilvános olvasási hozzáférés** a tárolók és blobok. Engedély megadása nem kötelező. További információkért lásd: [tárolók és blobok névtelen olvasási hozzáférés kezelése](../blobs/storage-manage-access-to-resources.md).  

Alapértelmezés szerint az összes erőforrást az Azure Storage védett, és csak a fiók tulajdonosa számára elérhető. Bár a hitelesítési stratégiák a tárfiókban lévő erőforrások elérésének engedélyezése az ügyfelek a fent vázolt bármelyikét használhatja, a Microsoft azt javasolja, hogy az Azure AD a maximális biztonság és könnyen használható lehetőség. 



